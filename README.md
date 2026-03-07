# Maze Duel - BO3 Match

## 1. Giới thiệu dự án

`Maze Duel` là một đồ án game mê cung 2D viết bằng một file HTML/CSS/JavaScript duy nhất. Trò chơi cho phép người dùng chơi theo hai chế độ:

- `Offline`: chơi với CPU ở 3 mức độ khó.
- `Online`: nhiều người chơi trong cùng một phòng qua MQTT.

Mỗi trận đấu diễn ra theo thể thức `BO3` (Best of 3). Trong mỗi ván, người chơi phải tìm kho báu trong mê cung, nhặt kho báu và đưa nó về đúng vị trí xuất phát của mình để giành chiến thắng.

Điểm nổi bật của dự án:

- Mê cung được sinh ngẫu nhiên theo seed.
- Độ khó tăng dần theo từng round.
- Có quiz tile, trap, quái vật, cơ chế cướp kho báu.
- Có ứng dụng các kỹ thuật AI như CSP, Graph Coloring và Pathfinding.
- Có debug panel và báo cáo chất lượng bản đồ phục vụ mục tiêu học tập, phân tích thuật toán.

README này mô tả chi tiết phiên bản hiện tại của dự án trong `claudegame.html`.

## 2. Mục tiêu của đồ án

Đồ án hướng tới các mục tiêu sau:

- Xây dựng một trò chơi mê cung có tính tương tác cao.
- Kết hợp giữa lập trình giao diện, gameplay và thuật toán AI.
- Minh họa trực quan cho các chủ đề như:
  - Sinh mê cung.
  - Tìm đường.
  - Bài toán thỏa mãn ràng buộc.
  - Tô màu đồ thị.
- Tạo ra một sản phẩm có thể trình bày như một đồ án môn học hoặc bài tập lớn.

## 3. Tổng quan gameplay

Trong mỗi ván chơi:

1. Người chơi xuất hiện tại base của mình.
2. Kho báu được đặt ở một vị trí hợp lệ trong mê cung.
3. Người chơi di chuyển để tìm kho báu.
4. Khi nhặt được kho báu, mục tiêu chuyển thành mang kho báu về base.
5. Người chơi khác hoặc CPU có thể cướp kho báu nếu chạm vào người đang giữ kho báu.
6. Quái vật và trap tạo thêm áp lực trong quá trình di chuyển.
7. Người đầu tiên đưa kho báu về base sẽ thắng ván.

Toàn bộ trận đấu kéo dài tối đa 3 ván. Ai thắng nhiều ván hơn sẽ thắng trận.

## 4. Tính năng chính

### 4.1 Chế độ chơi

- Chơi offline với CPU.
- Chơi online theo phòng.
- Hỗ trợ tối đa nhiều người chơi trong một phòng theo cấu hình `MAX_PLAYERS`.

### 4.2 Hệ thống mê cung

- Mê cung được sinh bằng thuật toán DFS carve trên lưới ô lẻ.
- Có thêm bước mở loop để giảm ngõ cụt.
- Có bước hậu xử lý đảm bảo bản đồ luôn có đường đi hợp lệ.
- Độ khó mê cung tăng theo round:
  - Round cao hơn thì bản đồ chặt hơn.
  - Số vòng lặp ít hơn.
  - Xác suất đắp lại tường cao hơn.

### 4.3 Hệ thống kho báu

- Kho báu được đặt bằng CSP ở vị trí hợp lệ.
- Người chơi có thể nhặt kho báu khi đi vào đúng ô chứa kho báu.
- Người cầm kho báu có thể bị cướp nếu đối thủ chạm vào.
- Khi có người nhặt kho báu, bản đồ có thể được tái sinh để làm trận đấu khó hơn và biến động hơn.

### 4.4 Hệ thống quiz

- Một số ô trên bản đồ là `quiz tile`.
- Khi đi vào quiz tile, người chơi phải trả lời câu hỏi.
- Nếu trả lời sai thì vẫn phải tiếp tục trả lời lại.
- Chỉ khi trả lời đúng thì quiz tile mới bị xóa.
- CPU không hiện modal quiz mà sẽ dừng trong một khoảng thời gian để mô phỏng suy nghĩ.

### 4.5 Hệ thống trap

- Người chơi có thể đặt bẫy bằng phím `Space`.
- Mỗi người có giới hạn số lượng bẫy đang tồn tại cùng lúc.
- Bẫy có thời gian tồn tại hữu hạn.
- Người đặt bẫy không bị trúng bẫy của chính mình.
- Nếu người đang mang kho báu dẫm trúng bẫy, kho báu sẽ rơi tại chỗ.

### 4.6 Hệ thống quái vật

- Quái vật xuất hiện từ đầu ván với số lượng tăng theo round.
- Quái vật tuần tra và truy đuổi người chơi khi phát hiện trong bán kính cho phép.
- Quái vật không đi xuyên tường.
- Nếu chạm vào quái vật, người chơi sẽ bị đưa về base.
- Vị trí spawn ban đầu của quái được kiểm soát để tránh gây vòng lặp chết-liên-tục gần base.

### 4.7 Debug và đánh giá

- Có `debug panel` trong game.
- Có thể xem thống kê về CSP, Graph Coloring, số quái, số trap, round hiện tại.
- Có thể gọi `mapQualityReport()` từ console để in báo cáo chất lượng bản đồ.

## 5. Công nghệ sử dụng

Dự án hiện tại sử dụng:

- `HTML5`
- `CSS3`
- `JavaScript` thuần (vanilla JavaScript)
- `HTML5 Canvas` để render game 2D
- `MQTT.js` để hỗ trợ chế độ online
- MQTT broker mặc định:
  - `wss://broker.emqx.io:8084/mqtt`

## 6. Kiến trúc hệ thống hiện tại

Phiên bản hiện tại không có backend riêng trong repository. Toàn bộ logic chính đang nằm trong `claudegame.html`.

### 6.1 Chế độ offline

- Chạy hoàn toàn cục bộ trong trình duyệt.
- Game loop, AI, render, logic va chạm đều xử lý trên client.

### 6.2 Chế độ online

- Dùng MQTT để gửi/nhận thông điệp giữa các client.
- Host đóng vai trò `authoritative client`:
  - Nhận input từ client khác.
  - Xử lý logic game.
  - Đồng bộ trạng thái cho các client.

Điều này có nghĩa là online hiện tại phù hợp cho demo, học tập, trình diễn đồ án, nhưng chưa phải mô hình server chuyên dụng dành cho production.

## 7. Cấu trúc thư mục

```text
Game_Gemini/
|- claudegame.html   # Toàn bộ giao diện, logic game, AI, network
|- README.md         # Tài liệu mô tả đồ án
```

Vì toàn bộ dự án được gói trong một file HTML lớn, việc triển khai rất đơn giản nhưng cũng khiến mã nguồn tập trung nhiều logic trong một nơi.

## 8. Hướng dẫn chạy dự án

Bạn có thể mở trực tiếp file HTML hoặc chạy qua local server.

### 8.1 Cách 1: Mở trực tiếp file

Mở file `claudegame.html` bằng trình duyệt.

### 8.2 Cách 2: Chạy local server bằng Python

Khuyến nghị dùng local server để ổn định hơn khi làm việc với asset hoặc môi trường trình duyệt.

```powershell
cd E:\Learn\Ky4_25_26\TTNT\Game_Gemini
python -m http.server 5500
```

Sau đó truy cập:

```text
http://127.0.0.1:5500/claudegame.html
```

## 9. Hướng dẫn sử dụng

### 9.1 Chơi offline

1. Nhập tên người chơi.
2. Nhấn `Bắt đầu` trong phần chơi với máy.
3. Chọn độ khó CPU:
   - `Dễ`
   - `Thường`
   - `Khó`
4. Xác nhận để vào game.

### 9.2 Chơi online

1. Đợi trạng thái MQTT kết nối thành công.
2. Một người tạo phòng để lấy mã phòng.
3. Người chơi khác nhập mã phòng để tham gia.
4. Host nhấn `Bắt đầu trận` khi đủ người.

## 10. Điều khiển trong game

### 10.1 Bàn phím

- Di chuyển:
  - `W A S D`
  - hoặc `Arrow Keys`
- Đặt bẫy:
  - `Space`
- Bật/tắt debug panel:
  - `Tab`
- Bật/tắt graph coloring overlay:
  - `G`

### 10.2 Cảm ứng

Trên thiết bị cảm ứng, giao diện hiện các nút điều hướng ở góc màn hình để hỗ trợ di chuyển.

## 11. Luật chơi chi tiết

### 11.1 Base

- Mỗi người chơi có một base riêng.
- Đưa kho báu về đúng base của mình để thắng ván.

### 11.2 Kho báu

- Kho báu chỉ có một trên bản đồ.
- Nếu người chơi chưa có kho báu và đi vào đúng vị trí, người đó sẽ nhặt được kho báu.
- Nếu đối thủ chạm vào người đang giữ kho báu, kho báu có thể đổi chủ.

### 11.3 Quiz tile

- Quiz tile là các ô đặc biệt trên bản đồ.
- Khi đi vào ô này, người chơi phải trả lời câu hỏi.
- Trả lời sai thì không được bỏ qua mà phải tiếp tục trả lời lại.
- Trả lời đúng mới được xem là vượt qua ô đó.

### 11.4 Trap

- Người chơi có thể đặt bẫy ở vị trí hiện tại.
- Không thể đặt bẫy ngay trên base.
- Bẫy sẽ biến mất sau một khoảng thời gian.
- Nếu người chơi đang giữ kho báu dẫm phải bẫy, kho báu sẽ rơi xuống vị trí đó.

### 11.5 Quái vật

- Quái vật di chuyển theo chu kỳ thời gian cấu hình.
- Có thể tuần tra hoặc truy đuổi người chơi.
- Khi chạm người chơi, quái vật sẽ đưa người chơi về base.
- Quái vật được bố trí để hạn chế camp ngay sát base từ đầu ván.

### 11.6 Điều kiện thắng

- Thắng ván: mang kho báu về base.
- Thắng trận: thắng đa số ván trong BO3.

## 12. Các thuật toán và kỹ thuật AI được áp dụng

## 12.1 Sinh mê cung

Phần sinh mê cung có các bước chính:

- DFS carve trên các ô có tọa độ lẻ.
- Mở thêm loop để giảm số lượng ngõ cụt.
- Giảm dead-end có kiểm soát.
- Hậu xử lý để nối các vùng rời rạc thành một thành phần liên thông.
- Điều chỉnh độ khó theo round bằng cách thay đổi mật độ tường và số lượng loop.

## 12.2 Constraint Satisfaction Problem (CSP)

Hệ CSP dùng để đặt:

- Vị trí kho báu.
- Vị trí quái vật.
- Vị trí quiz tile.

Các ràng buộc chính:

- Không trùng vị trí.
- Khoảng cách tối thiểu đến base.
- Khoảng cách tối thiểu đến kho báu hoặc giữa các quái với nhau.
- Kho báu phải reachable.
- Quái vật phải có khoảng cách an toàn theo đường đi thật tới base, tránh tạo vòng lặp chết liên tục.

## 12.3 Graph Coloring

- Áp dụng tô màu đồ thị trên các ô hợp lệ trong mê cung.
- Dùng để phân tán quiz theo vùng.
- Đồng thời hỗ trợ phân tích danger/opportunity trong debug hoặc AI.

## 12.4 Pathfinding

Các thuật toán tìm đường đang được sử dụng gồm:

- `BFS`
- `A*`
- `A* an toàn` có weighting theo nguy hiểm
- `Dijkstra safe` cho CPU hard

CPU sẽ chọn chiến lược khác nhau tùy theo độ khó.

## 13. Hệ thống AI của CPU

CPU có 3 mức độ:

### 13.1 Dễ

- Di chuyển tương đối đơn giản.
- Có yếu tố ngẫu nhiên cao.
- Phản ứng chậm hơn.

### 13.2 Thường

- Cân bằng giữa đuổi mục tiêu và né nguy hiểm.
- Dùng pathfinding hợp lý hơn.

### 13.3 Khó

- Dùng chiến lược tìm đường an toàn tốt hơn.
- Tận dụng influence map và danger map.
- Thời gian phản ứng nhanh hơn.

## 14. Giao thức mạng MQTT

Các message type chính đang được sử dụng:

- `JOIN`
- `ROOM_UPDATE`
- `REJECT`
- `INIT`
- `MOVE`
- `UPDATE`
- `MONS`
- `ROUND_END`
- `NEXT_ROUND`
- `MATCH_END`
- `RETURN_LOBBY`
- `LEAVE`
- `TRAP`
- `REGEN_MAP`

Topic chính cho phòng:

```text
mazeduel/room/<ROOM_CODE>
```

## 15. Các cấu hình quan trọng trong mã nguồn

Trong object `CFG` của `claudegame.html`, có thể điều chỉnh nhanh các thông số như:

- `W`, `H`, `TILE`: kích thước mê cung và tile.
- `MAX_PLAYERS`: số người chơi tối đa.
- `MONSTER_MOVE_MS`: chu kỳ di chuyển của quái.
- `TRAP_LIMIT`: số bẫy tối đa mỗi người.
- `TRAP_TTL`: thời gian tồn tại của bẫy.
- `BO3`: số ván tối đa trong trận.
- `MQTT_URL`: địa chỉ broker MQTT.
- `CPU_EASY_MS`, `CPU_NORMAL_MS`, `CPU_HARD_MS`: tốc độ suy nghĩ của CPU.
- `LOOP_ADD_RATIO`, `DEAD_END_PASSES`: cấu hình độ phức tạp mê cung.

## 16. Debug và đánh giá chất lượng bản đồ

### 16.1 Debug panel trong game

Nhấn `Tab` để mở debug panel. Tại đây có thể xem:

- FPS
- Ping MQTT
- Mode hiện tại
- Số lượng người chơi
- Số trap
- Round hiện tại
- Thông tin Graph Coloring
- Thông tin CSP

### 16.2 Báo cáo map quality

Mở trình duyệt, nhấn `F12`, vào `Console`, sau đó gọi:

```js
mapQualityReport()
```

Hàm này sẽ in ra:

- Thống kê chất lượng mê cung.
- Thông tin placement của treasure, quiz, quái vật.
- Số liệu CSP.
- Thông tin fairness giữa các base.

## 17. Ưu điểm của đồ án

- Chạy gọn trong một file, dễ demo và nộp bài.
- Có đầy đủ gameplay cơ bản và nhiều cơ chế mở rộng.
- Có ứng dụng nhiều thuật toán AI rõ ràng.
- Dễ trực quan hóa để thuyết trình.
- Có cả chế độ offline lẫn online.

## 18. Hạn chế hiện tại

- Logic hiện tập trung trong một file HTML lớn, khó bảo trì khi tiếp tục mở rộng.
- Chế độ online phụ thuộc vào broker MQTT công cộng.
- Host-authority mới là mức client-side, chưa phải dedicated server thực thụ.
- Việc phân tách module, test tự động, và logging chuyên sâu chưa được tách riêng thành cấu trúc project hoàn chỉnh.

## 19. Hướng phát triển trong tương lai

- Tách game logic thành nhiều module JavaScript riêng.
- Xây dựng backend authoritative server thật sự.
- Bổ sung replay, thống kê sau trận và lịch sử trận đấu.
- Mở rộng thêm vật phẩm, kỹ năng, nhiều loại quái vật.
- Thêm chế độ xếp hạng hoặc nhiều bản đồ khác nhau.
- Tối ưu AI theo hành vi người chơi.

## 20. Kết luận

`Maze Duel` là một đồ án game mê cung có tính tương tác cao, phù hợp để trình bày trong các môn học liên quan đến lập trình game, trí tuệ nhân tạo, hoặc hệ thống tương tác thời gian thực. Dự án thể hiện rõ sự kết hợp giữa:

- Thiết kế gameplay.
- Lập trình giao diện canvas.
- Đồng bộ trạng thái online.
- Ứng dụng các thuật toán AI trong thực tế.

Đây là một nền tảng tốt để tiếp tục mở rộng thành một sản phẩm hoàn chỉnh hơn trong tương lai.

## 21. Ghi chú bản quyền

Dự án hiện phù hợp cho mục đích học tập, báo cáo, bài tập lớn và trình diễn đồ án. Nếu muốn công bố rộng rãi, nên bổ sung thêm file license riêng như `MIT` hoặc `Apache-2.0`.
