# RotateAttention — ECCV Video Narration Script

**Paper:** RotateAttention: RoPE-Aware Rotation and Range Rectification for INT4 Quantized Attention in Video Generation  
**Submission:** ECCV 2026 #1552  
**Target length:** ~4–5 minutes (~615 spoken words; at 140–150 words per minute this runs roughly 4:10–4:30, plus pauses ≈ 4:30–5:00)

---

## Script

**[0:00–0:30] Opening**

Hello everyone, and welcome to our presentation. I'm happy to introduce RotateAttention, an efficient mixed-precision INT4 FlashAttention framework designed for diffusion-transformer-based video generation models that use three-dimensional rotary position embeddings, or 3D RoPE.

**[0:30–1:10] Problem**

Video generation has advanced rapidly, but it is extremely expensive. In modern diffusion transformers, the attention module is a primary bottleneck because its cost grows quadratically with sequence length, and video sequences routinely exceed ten thousand tokens. A promising solution is to quantize FlashAttention to four bits. However, existing low-bit methods overlook two critical issues. First, the standard trick for handling outliers in queries and keys is online rotation, but this rotation is hard to reconcile with RoPE. Second, the attention probability matrix is always positive, so symmetric four-bit quantization wastes half of its numeric range.

**[1:10–1:50] Observation**

We start with a careful analysis of what 3D RoPE does to the query and key matrices. In these models, the feature dimension is partitioned into three segments: frame, height, and width. We measure channel-level incoherence, which captures outlier strength. We observe two patterns. Outliers are strongly segmented according to these three RoPE segments, and within each segment one half of the dimensions concentrates most of the outliers. Importantly, queries and keys share almost symmetric outlier profiles. These patterns motivate our rotation design.

**[1:50–2:20] Method Overview**

Based on this analysis, we propose RotateAttention. It is a mixed-precision framework: accuracy-sensitive attention blocks and denoising steps fall back to FP16, while the remaining FlashAttention operations run in an INT4 kernel. The framework has two core ideas. First, RoPE-aware rotation for queries and keys. Second, range-optimized quantization for the attention probability matrix. We also add a standard Hadamard transform for the value matrix, which is fused offline at zero inference cost.

**[2:20–3:20] RoPE-Aware Rotation**

For queries and keys, we use a block-diagonal orthogonal rotation made of two-by-two blocks. Orthogonality is essential: if the singular values differ from one, then transforming queries and keys asymmetrically amplifies outliers on one side and enlarges quantization error. We propose two RoPE-aware variants. Interleaved Rotation operates on adjacent channel pairs and can be fused directly into the RoPE rotation, giving zero overhead. Half Rotation pairs each dimension with its counterpart in the other half of the same segment, which matches the observed outlier structure and has negligible cost. We also study learned rotations and find that they only work under strict orthogonality constraints, confirming that unconstrained LLM-style rotations do not transfer to video diffusion transformers.

**[3:20–3:50] Range-Optimized P Quantization**

The second idea addresses the probability matrix P. Standard symmetric quantization maps P to the range zero to seven, using only eight of the sixteen INT4 levels. We instead use a fixed scale and zero-point to map P across the full range from minus eight to seven. This doubles the quantization resolution with essentially no extra computation.

**[3:50–4:35] Results**

We evaluate RotateAttention on Wan 2.2 text-to-video and image-to-video models and on HunyuanVideo, all at 480p. Compared with full precision, our method preserves video quality almost identically, and it clearly outperforms the SageAttention baseline in visual fidelity and structural consistency. On difference metrics such as cosine similarity, SSIM, and PSNR, our method consistently improves over naive INT4 attention. In practice, we recommend Half Rotation as the default because it is robust across models, while Interleaved Rotation is an attractive zero-cost choice for text-to-video. Our INT4 kernel achieves a two-point-two-times speedup at the kernel level and end-to-end speedups between one-point-five-one and one-point-six-eight times.

**[4:35–5:00] Conclusion**

In summary, RotateAttention makes INT4 attention practical for 3D-RoPE video diffusion transformers. By designing rotations that understand RoPE's structure and by using the full INT4 range for attention probabilities, we achieve near-full-precision generation quality with substantial speedups. Thank you, and I'm happy to take questions.

---

## Pronunciation & Delivery Notes

- **RoPE** → say "rope" (not "R-O-P-E").
- **3D RoPE** → "three-D rope".
- **INT4** → "int-four".
- **FP16** → "F-P sixteen".
- **Wan 2.2** → "Wan two point two".
- **HunyuanVideo** → "Hunyuan Video".
- **2.2×** → "two point two times"; **1.51–1.68×** → "one point five one to one point six eight times".
- Pause 1–2 seconds at each bracketed section break for a natural video cut or slide change.
