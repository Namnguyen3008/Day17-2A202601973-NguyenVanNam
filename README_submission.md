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

Đã chạy `pytest`, seed Zep Cloud, benchmark student/no-memory và `compare_reports`.

- Student: **11/11**, evidence hit rate **100.0%**, latency trung bình **753.4 ms**, token reduction trung bình **14.2%**.
- No-memory: **2/11**, evidence hit rate **18.2%**.
- Memory giúp tăng **9 case pass** và **81.8 điểm phần trăm hit rate**. Token reduction thấp hơn vì hệ thống giữ lại evidence cần thiết; giảm token mà không có evidence không phải là tối ưu.
- Tất cả bốn lớp đều đạt **100%** trên các case của lớp tương ứng; không có lớp nào có hit rate thấp hơn. Case lấy nhiều token nhất là **E03 long-term: 1,445 tokens**.
- E07 dùng đồng thời long-term và semantic: giữ preference `Python` cùng rule `Idempotency-Key`. E10 giữ được `REVIEW-DEADLINE-1600`, `Friday` và `16:00` sau compaction.

Chi tiết số liệu và evidence nằm trong `reports/benchmark.md`, `reports/benchmark_no_memory.md` và `reports/comparison.md`.
