# Reflection

Anti-pattern mà team em dễ gặp nhất là **small-file proliferation do ingest theo micro-batch nhưng thiếu maintenance định kỳ**. Với workload AI/agent, request log, trace và event có thể được ghi liên tục theo các batch rất nhỏ. Nếu mỗi batch tạo một Parquet file riêng, số file sẽ tăng nhanh hơn nhiều so với dung lượng dữ liệu; chi phí lập kế hoạch, metadata và object GET có thể trở thành bottleneck trước cả compute.

Rủi ro lớn hơn là “maintenance debt”: nếu compaction, clustering, snapshot expiry và orphan removal không có lịch chạy rõ ràng, hệ thống vẫn đúng về mặt dữ liệu nhưng ngày càng chậm, đắt và khó vận hành. Team em nên theo dõi file count, average file size, metadata size và pruning ratio; đặt ngưỡng để trigger compaction; dùng retention có chủ đích thay vì vacuum tùy tiện; và luôn có age guard khi quét orphan để tránh xóa file của writer đang chạy.

Điểm em rút ra từ lab là maintenance phải được coi như một phần của pipeline, không phải việc dọn dẹp sau cùng.