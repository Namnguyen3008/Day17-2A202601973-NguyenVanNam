# Lab 17 Submission

- **Học viên:** Nguyễn Văn Nam
- **Mã học viên:** 2A202601973
- **Bài:** Multi-Memory Agent với Zep Cloud V3

## Tóm tắt triển khai

Student implementation tách đúng bốn lớp memory: short-term local, long-term user-scoped, episodic user-scoped và semantic shared graph. Long-term dùng Context Block sau khi prime evaluation thread; episodic dùng `user_id` với `scope="episodes"`; semantic dùng `graph_id` với fallback từ `episodes` sang `nodes`; context cuối được ghép theo budget 10/4/3/3 và thứ tự short-term → long-term → episodic → semantic.

## Phân tích bắt buộc

1. Long-term là lớp quan trọng nhất trong practice set vì bao phủ E02, E03, E08 và E09, đồng thời kiểm tra preference, open loop, recency và user isolation.
2. Zep Context Block giảm công sức xây ingestion, graph search, relevance và cross-session context. Redis + Qdrant cho quyền kiểm soát schema, TTL và vector pipeline tốt hơn nhưng phải tự xây extraction, ranking, provenance, isolation và deletion.
3. Guardrail chống memory poisoning: chỉ ghi memory khi consent còn hiệu lực; redact PII; lưu scope, source, timestamp, confidence và validity; không trộn user graph với shared semantic graph; review preference update quan trọng; heartbeat không được tự cấp quyền mới; verify deletion trên mọi user-scoped store.
4. E07 phải kết hợp long-term với semantic để giữ đồng thời preference `Python` và evidence `Idempotency-Key`. E08 ưu tiên constraint mới `BLUEBIRD-42 → TypeScript/NestJS` mà không xóa preference Python ở scope khác. E10 phải giữ `REVIEW-DEADLINE-1600`, `Friday` và `16:00` sau compaction.

## Kiểm chứng

Chạy `pytest`, seed Zep, benchmark student/no-memory và `compare_reports` trước khi nộp. Các số liệu hit rate, latency, token reduction và case có nhiều token nhất phải lấy trực tiếp từ `reports/benchmark.md`, không tự suy đoán hoặc ghi số liệu giả.
