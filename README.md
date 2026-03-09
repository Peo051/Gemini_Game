# Maze Duel - BO3 Match (Game_Gemini)

Do an mon **Tri Tue Nhan Tao**: game me cung 2D viet bang **1 file HTML/CSS/JavaScript**, tap trung vao thuat toan AI, gameplay doi khang, va che do Online qua MQTT.

## 1. Tong quan du an

`Maze Duel` la game me cung 2D theo the thuc **Best of 3 (BO3)**.
Nguoi choi tim kho bau, tranh chap voi doi thu, va mang kho ve base de ghi diem.

Du an hien co 2 phien ban:
- `claudegame_v2.html`: phien ban moi, day du tinh nang PvP/AI, khuyen nghi dung de demo va nop do an.
- `claudegame.html`: phien ban cu (co the dung de tham khao qua trinh phat trien).

## 2. Muc tieu do an

- Xay dung game co tinh tuong tac cao, de trinh bay truc quan cac ky thuat TTNT.
- Tich hop nhieu nhom thuat toan AI trong cung mot san pham:
  - Sinh me cung: DFS procedural generation.
  - Tim duong: BFS, A*, Dijkstra.
  - Rang buoc va toi uu bo tri: CSP.
  - Phan vung do thi: Graph Coloring.
  - Phan tich diem ngheo/diem nghen: Tarjan (articulation point, bridge).
  - Danh gia chien thuat: Influence Map.
  - Quyết dinh doi khang CPU: Utility scoring + Minimax/Tactical search.
- Tao ban game co the chay ngay tren trinh duyet, khong can build phuc tap.

## 3. Gameplay chinh

1. Moi nguoi choi spawn o base (4 goc map toi da 4 nguoi).
2. Kho bau duoc dat vao vi tri hop le (co kiem tra fairness).
3. Nguoi choi di chuyen de tim, nhat, va mang kho bau ve base.
4. Neu doi thu o gan holder co the mo **contest** de cuop kho.
5. Khi dung o base va giu kho du thoi gian **channel**, se ghi diem.
6. Thang 2/3 van la thang tran.

## 4. Tinh nang noi bat cua ban `v2`

### 4.1 Che do choi
- Offline: choi voi CPU (Easy / Normal / Hard).
- Online: nhieu nguoi choi trong phong qua MQTT (Host authoritative snapshot sync).

### 4.2 Co che doi khang PvP
- Contest (tranh chap kho bau) co progress bar.
- Goal channel tai base de ghi diem.
- Treasure drop grace de tranh bi cuop lap tuc qua vo ly.
- Trap va bo ky nang chu dong tao nhieu pha outplay.

### 4.3 He thong Skill + Energy
Co 5 skill chu dong:
- Sprint (`Shift`): tang toc trong thoi gian ngan.
- Radar (`Q`): chi huong kho bau/holder.
- Shield (`E`): mien nhiem trap, contest, magnet, quai trong thoi gian ngan.
- Clean Step (`R`): xoa trap gan, hoac kich hoat buoc an toan.
- Magnet (`T`): hut kho bau dang roi trong pham vi gan.

Moi skill co:
- Nang luong tieu hao (`Energy` 0..100).
- Cooldown rieng.
- Dieu kien kich hoat.

### 4.4 Zone Control
- Vung trung tam map cho phep hoi nang luong nhanh hon.
- Co co che capture, contest, va owner theo thoi gian.
- Tac dong truc tiep den nhịp ra skill trong PvP.

### 4.5 He thong Quiz / Trap / Monster
- Quiz cell co cau hoi ngan, tang do da dang gameplay.
- Trap co gioi han so luong va thoi gian ton tai.
- Monster di chuyen tren map va tao suc ep chien thuat.

### 4.6 Debug va quan sat AI
- Debug panel (`Tab`) hien trang thai AI, map quality, CSP, influence.
- Overlay debug:
  - `G`: Graph Coloring overlay.
  - `I`: Influence overlay.
  - `C`: Choke-point overlay.
- Console reports:
  - `aiDecisionReport()`
  - `tacticalSearchReport()`
  - `monsterAssignmentReport()`
  - `mapBalanceReport()`
  - `mapQualityReport()`

## 5. Cong nghe su dung

- Frontend/Game loop: HTML5 + CSS3 + JavaScript (vanilla).
- Render: Canvas 2D API.
- Multiplayer transport: MQTT over WebSocket (`mqtt.js` CDN).
- Broker fallback (v2):
  - `wss://broker.hivemq.com:8884/mqtt`
  - `wss://test.mosquitto.org:8081/mqtt`
  - `wss://broker.emqx.io:8084/mqtt`
- Build system: khong can (single-file deployment).

## 6. Cac thuat toan AI trong do an

### 6.1 DFS - Sinh me cung
- Tao map tu trang thai toan tuong.
- Carve theo o le, random huong di de tao me cung.
- Hau xu ly them loop, giam dead-end, va noi cac vung roi rac.
- Do kho map thay doi theo round.

### 6.2 BFS - Tim duong ngan nhat va distance map
- Dung cho fallback pathfinding va tinh khoang cach toi moi o.
- Ho tro danh gia fairness cua vi tri kho bau.

### 6.3 A* - Tim duong nhanh theo muc tieu
- Heuristic Manhattan.
- Dung cho CPU va logic di chuyen thong minh.
- Co bien the an toan ket hop danger map.

### 6.4 Dijkstra (safe cost)
- Tim duong chi phi nho nhat khi moi o co cost khac nhau.
- Cost co the phu thuoc danger/opportunity/trap/monster pressure.

### 6.5 CSP - Dat kho bau, quai, quiz theo rang buoc
- Backtracking + pruning + MRV/degree + ordering.
- Kiem tra fairness va chat luong map.
- Co co che reroll khi map khong dat nguong de dam bao trai nghiem.

### 6.6 Graph Coloring
- Phan vung o floor thanh cac zone mau.
- Dung de phan bo quiz/monster can bang hon tren toan ban do.
- Dung cho cac phan tich nguy hiem theo zone.

### 6.7 Tarjan (Articulation Point / Bridge)
- Xac dinh choke points (diem ngheo) trong me cung.
- Lam co so cho chien thuat chan duong, phuc kich.

### 6.8 Influence Map
- Xay dung truong anh huong danger/opportunity toan map.
- CPU dung de lua chon duong di an toan va vi tri co loi.

### 6.9 Minimax + Tactical Search (CPU combat)
- Danh gia hanh dong doi khang o tinh huong can chien.
- Ket hop utility score va bo loc ngu canh de tranh tinh toan qua nang.
- Muc tieu: CPU ra quyet dinh co tinh chien thuat hon trong PvP.

## 7. AI CPU theo muc do kho

- Easy:
  - Toc do xu ly cham hon.
  - Quyết dinh don gian, uu tien duong di co ban.
- Normal:
  - Can bang giua tan cong, phong thu va laya map objective.
- Hard:
  - Phan tich danger/opportunity/choke ky hon.
  - Co tactical search, combat skill usage, va path an toan nang cao.

## 8. Kien truc Online (Host Authoritative)

- Host giu logic chinh:
  - xu ly game state,
  - va cham,
  - AI,
  - spawn/regen,
  - snapshot phat cho clients.
- Client:
  - gui input (`MOVE`, request skill/trap/interact),
  - nhan snapshot,
  - render trang thai.
- Co guard de bo qua snapshot cu (dua tren tick/seq/time), tranh giat lag va rollback sai.

## 9. Cau truc thu muc

```text
Game_Gemini/
|- claudegame_v2.html   # Ban khuyen nghi: gameplay + AI + online + debug
|- claudegame.html      # Ban cu/de tham khao
`- README.md            # Tai lieu du an
```

## 10. Huong dan chay du an

### Cach nhanh nhat
- Mo truc tiep `claudegame_v2.html` bang Chrome/Edge.

### Cach khuyen nghi (local server)

```powershell
cd E:\Learn\Ky4_25_26\TTNT\Game_Gemini
python -m http.server 5500
```

Mo trinh duyet:
- `http://127.0.0.1:5500/claudegame_v2.html`

Ly do khuyen nghi dung local server:
- On dinh hon khi test multiplayer tab/tab.
- Tranh mot so gioi han CSP/network theo chinh sach trinh duyet.

## 11. Huong dan su dung nhanh

### Offline
1. Nhap ten nguoi choi.
2. Chon `Choi voi may`.
3. Chon do kho CPU.
4. Vao countdown va bat dau van 1.

### Online
1. Dam bao trang thai MQTT da ket noi.
2. Mot nguoi tao phong (lay ma 4 ky tu).
3. Nguoi khac nhap ma va vao phong.
4. Host bat dau tran khi du nguoi.

## 12. Dieu khien trong game

### 12.1 Di chuyen
- `W A S D` hoac `Arrow keys`.

### 12.2 Hanh dong / Skill
- `Space`: Dat trap.
- `F`: Interact (tranh chap/nhat kho).
- `Shift`: Sprint.
- `Q`: Radar.
- `E`: Shield.
- `R`: Clean Step.
- `T`: Magnet.

### 12.3 HUD / Debug
- `H` hoac `?`: Mo/ dong huong dan trong game.
- `Tab`: Mo/ dong debug panel.
- `G`: Graph overlay.
- `I`: Influence overlay.
- `C`: Choke overlay.

### 12.4 Mobile
- Co D-pad cam ung va thanh action rieng.
- Tu dong an key-hint desktop tren thiet bi touch.

## 13. Luat choi tom tat

- Nhat kho bau: cham vao o kho bau.
- Cuop kho: contest thanh cong vao holder.
- Ghi diem: cam kho + dung dung base du thoi gian channel.
- Trung trap/quai: bi bat loi ve toc do/trang thai, tao loi the cho doi thu.
- Thang 2/3 van: thang tran.

## 14. Cac tham so game quan trong (tham khao)

Mot so config tieu bieu trong `CFG`:
- Grid: `W=41`, `H=25`, `TILE=16`.
- `MAX_PLAYERS=4`.
- `TRAP_LIMIT=2`, `TRAP_TTL=10000ms`.
- `BO3=3`.
- `GOAL_CHANNEL_MS=1000`.
- `ZONE_CAPTURE_MS=2000`.
- Skill cooldown/energy duoc cau hinh rieng cho tung ky nang.

Ban co the dieu chinh truc tiep trong object `CFG` de can bang game.

## 15. Danh gia chat luong map va AI

Su dung debug panel + console reports de:
- Kiem tra fairness vi tri kho bau theo khoang cach den cac base.
- Theo doi thong ke CSP (attempt/backtrack/reroll/fallback).
- Quan sat graph coloring distribution va zone danger.
- Danh gia quyet dinh CPU theo state, utility, tactical depth.

Goi nhanh o F12 Console:
- `mapQualityReport()`
- `aiDecisionReport()`
- `tacticalSearchReport()`
- `monsterAssignmentReport()`
- `mapBalanceReport()`

## 16. Uu diem va han che

### Uu diem
- Single-file, de chay de demo.
- Nhieu thuat toan AI duoc tich hop that vao gameplay.
- Co online mode, debug tools, va metric de trinh bay hoc thuat.

### Han che
- Chua tach module (toan bo logic nam trong 1 HTML lon).
- Chua co test tu dong/unit test.
- Broker MQTT public co the dao dong theo thoi diem mang.

## 17. Huong phat trien

- Tach code thanh nhieu module (`core/ai/net/ui`) de de bao tri.
- Them replay system va luu tran dau.
- Them che do xep hang MMR online.
- Them bo question bank quiz theo chu de/muc do.
- Them bot profiles voi style chien dau rieng (aggressive/defensive/objective).

## 18. Goi y trinh bay bao cao do an

1. Demo 1 van Offline (Normal/Hard) de the hien CPU AI.
2. Bat debug panel + overlay de giai thich thong tin AI truc quan.
3. Chay `mapQualityReport()` va `tacticalSearchReport()` de dua so lieu vao slide.
4. Demo online 2 may/tab de mo ta host-authoritative + snapshot sync.
5. Ket luan bang uu/nhuoc diem va roadmap nang cap.

---

Neu can, co the bo sung them:
- So do luong state machine cua CPU.
- So do sequence message MQTT cho che do online.
- Bang benchmark nho (FPS/ping/map-generation-time) de README thanh phien ban nop cuoi ky hoan chinh hon.
