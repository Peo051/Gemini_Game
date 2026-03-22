# Maze Duel BO3 - Đồ Án Trí Tuệ Nhân Tạo

Maze Duel là đồ án môn Trí Tuệ Nhân Tạo, xây dựng trò chơi mê cung 2D theo thể thức Best of 3 (BO3) bằng HTML/CSS/JavaScript thuần, tập trung vào tích hợp và trực quan hóa nhiều thuật toán AI trong cùng một sản phẩm chạy trực tiếp trên trình duyệt.

## 1. Giới thiệu đề tài

### 1.1 Bối cảnh
Trong nhiều đồ án AI, phần thuật toán thường tách rời trải nghiệm người dùng nên khó minh họa tính ứng dụng. Đề tài này lựa chọn hướng tiếp cận game đối kháng thời gian thực để:

- Biến các thuật toán AI thành hành vi có thể quan sát trực tiếp.
- Kết hợp giữa mô phỏng, ra quyết định và đồng bộ mạng trong một hệ thống hoàn chỉnh.
- Tạo sản phẩm có thể demo ngay, phục vụ báo cáo và phản biện.

### 1.2 Mục tiêu tổng quát
- Xây dựng game mê cung có tính tương tác cao, có AI và multiplayer online.
- Áp dụng nhiều nhóm thuật toán (sinh map, tìm đường, ràng buộc, đánh giá chiến thuật).
- Đảm bảo hệ thống có thể chạy ổn định trên trình duyệt phổ biến mà không cần quy trình build phức tạp.

### 1.3 Phạm vi triển khai
- Nền tảng: Web (Desktop + Mobile).
- Chế độ chơi: Offline với CPU và Online qua MQTT.
- Trọng tâm học thuật: Chất lượng map, quyết định CPU, đồng bộ trạng thái host-authoritative.

## 2. Tóm tắt gameplay

1. Người chơi xuất phát tại base tương ứng.
2. Kho báu xuất hiện ở vị trí hợp lệ trên bản đồ.
3. Người chơi di chuyển, tranh chấp với đối thủ và sử dụng kỹ năng để kiểm soát nhịp trận.
4. Cầm kho báu về base, giữ trong thời gian channel để ghi điểm.
5. Trận đấu theo BO3, đội/người đạt 2 ván thắng trước sẽ thắng chung cuộc.

## 3. Tính năng chính

### 3.1 Chế độ chơi
- Offline: chơi với CPU theo 3 mức độ (Easy, Normal, Hard).
- Online: nhiều người chơi cùng phòng thông qua MQTT over WebSocket.

### 3.2 Cơ chế đối kháng
- Contest cướp kho báu khi áp sát người giữ kho báu.
- Goal channel tại base để xác nhận ghi điểm.
- Trap đặt chiến thuật, có giới hạn số lượng và thời gian tồn tại.
- Quiz cell tạo yếu tố biến thiên trong nhịp trận.

### 3.3 Hệ kỹ năng và năng lượng
Các kỹ năng chủ động gồm:

- Sprint: tăng tốc di chuyển trong thời gian ngắn.
- Radar: hỗ trợ định hướng mục tiêu.
- Shield: miễn nhiễm một số hiệu ứng bất lợi trong thời gian ngắn.
- Clean Step: dọn bẫy gần hoặc tạo trạng thái đi an toàn.
- Magnet: hút kho báu trong phạm vi cho phép.

Mỗi kỹ năng có bộ tham số riêng:
- Chi phí năng lượng.
- Thời gian hồi (cooldown).
- Điều kiện kích hoạt.

### 3.4 Zone control
- Khu vực trung tâm cho phép hồi năng lượng nhanh hơn.
- Có trạng thái chiếm giữ, tranh chấp và đổi chủ.
- Tác động trực tiếp tới khả năng sử dụng kỹ năng của người chơi.

## 4. Kiến trúc và công nghệ

### 4.1 Công nghệ sử dụng
- HTML5, CSS3, JavaScript thuần.
- Canvas 2D API để render khung hình.
- MQTT.js (CDN) để truyền thông online.
- Public broker fallback:
  - wss://broker.hivemq.com:8884/mqtt
  - wss://test.mosquitto.org:8081/mqtt
  - wss://broker.emqx.io:8084/mqtt

### 4.2 Kiến trúc online host-authoritative
- Host giữ quyền quyết định trạng thái chuẩn của trận đấu.
- Client gửi input (di chuyển, kỹ năng, tương tác) và render theo snapshot nhận được.
- Snapshot có tick/seq/time để loại bỏ gói cũ và giảm lỗi giật/rollback sai.

### 4.3 Cấu trúc tệp

```text
Game_Gemini/
|- claudegame_v2.html   # Phiên bản chính, đầy đủ tính năng
|- claudegame.html      # Phiên bản cũ để tham khảo
|- Đồ án Trí Tuệ Nhân Tạo.txt
`- README.md
```

## 5. Các thuật toán AI đã áp dụng

### 5.1 DFS - Sinh mê cung thủ tục
- Tạo cấu trúc mê cung từ bản đồ ban đầu.
- Carve theo quy tắc lân cận để đảm bảo tính liên thông.
- Hậu xử lý thêm loop, giảm ngõ cụt và cân bằng độ khó.

### 5.2 BFS - Distance map và đường ngắn nhất theo bước
- Tính khoảng cách theo ô lưới nhanh, ổn định.
- Hỗ trợ đánh giá fairness khi bố trí tài nguyên.

### 5.3 A* - Tìm đường theo mục tiêu động
- Dùng heuristic Manhattan cho lưới 4 hướng.
- Áp dụng trong quyết định di chuyển và truy đuổi mục tiêu.

### 5.4 Dijkstra - Đường đi chi phí thấp nhất
- Phù hợp khi mỗi ô có mức rủi ro/chi phí khác nhau.
- Kết hợp với danger/opportunity để chọn tuyến an toàn hơn.

### 5.5 CSP - Bố trí tài nguyên theo ràng buộc
- Bố trí kho báu, quiz, monster theo điều kiện hợp lệ.
- Sử dụng backtracking, pruning và heuristic chọn biến.
- Có cơ chế thử lại để đảm bảo chất lượng bản đồ.

### 5.6 Graph Coloring - Phân vùng bản đồ
- Chia vùng ô đi được theo nhóm màu không kề nhau.
- Hỗ trợ phân bổ tài nguyên cân bằng hơn giữa các vùng.
- Dùng để trực quan hóa cấu trúc bản đồ.

### 5.7 Tarjan - Điểm nghẽn chiến thuật
- Tìm articulation points/bridges trên đồ thị mê cung.
- Phục vụ phân tích choke points cho cả AI và debug chiến thuật.

### 5.8 Influence Map - Bản đồ ảnh hưởng
- Mô hình hóa vùng nguy hiểm/cơ hội trên toàn map.
- AI dùng để chọn hướng đi, định vị ưu tiên và tránh rủi ro.

### 5.9 Utility + Tactical Search/Minimax
- Chấm điểm hành động theo ngữ cảnh hiện tại.
- Tăng chất lượng ra quyết định trong các pha tranh chấp gần.

## 6. Thiết kế AI CPU theo mức độ khó

### Easy
- Tần suất cập nhật quyết định thấp hơn.
- Ưu tiên hành vi cơ bản, ít tính chiến thuật phức tạp.

### Normal
- Cân bằng giữa bám mục tiêu, phòng thủ và tranh chấp.
- Có xét thêm bối cảnh giao tranh ở mức vừa phải.

### Hard
- Tận dụng influence/choke/utility sâu hơn.
- Ra quyết định kỹ năng và hướng đi chủ động hơn.

## 7. Hướng dẫn chạy dự án

### 7.1 Chạy nhanh
- Mở trực tiếp tệp claudegame_v2.html bằng Chrome hoặc Edge.

### 7.2 Chạy qua local server (khuyến nghị)

```powershell
cd E:\Learn\Ky4_25_26\TTNT\Game_Gemini
python -m http.server 5500
```

Truy cập:
- http://127.0.0.1:5500/claudegame_v2.html

Lý do khuyến nghị:
- Ổn định hơn khi thử online nhiều tab/máy.
- Hạn chế lỗi phát sinh do chính sách bảo mật của trình duyệt.

## 8. Hướng dẫn sử dụng

### 8.1 Chế độ Offline
1. Nhập tên người chơi.
2. Chọn Chơi với máy.
3. Chọn độ khó CPU.
4. Bắt đầu trận và thi đấu BO3.

### 8.2 Chế độ Online
1. Đảm bảo trạng thái MQTT đã kết nối.
2. Một người tạo phòng để lấy mã phòng.
3. Người còn lại nhập mã và tham gia.
4. Host bấm bắt đầu khi đã đủ người chơi.

## 9. Bảng phím điều khiển

### 9.1 Di chuyển
- W/A/S/D hoặc phím mũi tên.

### 9.2 Tương tác và kỹ năng
- Space: đặt bẫy.
- F: tương tác/contest.
- Shift: Sprint.
- Q: Radar.
- E: Shield.
- R: Clean Step.
- T: Magnet.

### 9.3 Phím debug - trực quan thuật toán
- H hoặc ?: mở/đóng bảng hướng dẫn trong game.
- Tab: bật/tắt debug panel.
- G: bật/tắt Graph Coloring overlay.
- I: bật/tắt Influence Map overlay.
- C: bật/tắt Choke Points overlay.

## 10. Quan sát và đánh giá kết quả AI

### 10.1 Công cụ trong game
- Debug panel hiển thị trạng thái CPU, map quality, CSP log, năng lượng, contest/channel.
- Các overlay hỗ trợ đối chiếu quyết định AI với trạng thái bản đồ.

### 10.2 Lệnh console phục vụ báo cáo
- aiDecisionReport()
- tacticalSearchReport()
- monsterAssignmentReport()
- mapBalanceReport()
- mapQualityReport()

Các báo cáo này có thể dùng trực tiếp để đưa số liệu minh họa vào slide hoặc phụ lục báo cáo.

## 11. Tham số cấu hình quan trọng

Một số cấu hình chính trong CFG:
- Kích thước lưới và tile.
- BO3 và số người chơi tối đa.
- Giới hạn bẫy, thời gian tồn tại bẫy.
- Năng lượng tối đa, tốc độ hồi, cooldown kỹ năng.
- Tham số contest/channel/zone control.

Việc điều chỉnh các tham số này giúp cân bằng độ khó, nhịp độ trận và trải nghiệm người chơi.

## 12. Ưu điểm, hạn chế và hướng phát triển

### 12.1 Ưu điểm
- Chạy trực tiếp trên trình duyệt, dễ demo.
- Tích hợp nhiều thuật toán AI trong cùng một gameplay thực tế.
- Có chế độ online và bộ công cụ debug/quan sát phục vụ báo cáo học thuật.

### 12.2 Hạn chế
- Mã nguồn hiện tập trung nhiều trong một tệp HTML lớn.
- Chưa có hệ thống kiểm thử tự động chính thức.
- Broker công cộng có thể dao động tùy thời điểm mạng.

### 12.3 Hướng phát triển
- Tách module theo lớp core/ai/net/ui để dễ bảo trì.
- Bổ sung replay và lưu lịch sử trận.
- Nâng cấp matchmaking/xếp hạng.
- Mở rộng ngân hàng câu hỏi quiz theo chủ đề và mức độ.

## 13. Gợi ý trình bày khi bảo vệ đồ án

1. Demo nhanh một ván Offline để giới thiệu vòng lặp game.
2. Bật Tab, G, I, C để giải thích trực quan thuật toán.
3. Chạy các hàm report trên console để đưa số liệu vào slide.
4. Demo Online 2 máy/tab, nhấn mạnh kiến trúc host-authoritative.
5. Kết luận bằng ưu điểm, hạn chế và kế hoạch mở rộng.

---

Nếu cần, có thể mở rộng README thêm các mục phụ lục như sơ đồ kiến trúc message MQTT, state machine CPU và benchmark FPS/ping theo thiết bị để hoàn thiện phiên bản nộp cuối kỳ.
