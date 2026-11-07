# BÁO CÁO KẾ HOẠCH XÂY DỰNG MỚI — MOVIE REVIEW STUDIO

**Mã tài liệu:** MRS-GREENFIELD-PLAN-001  
**Phiên bản tài liệu:** 1.3 — Kế hoạch hợp nhất từ đánh giá vòng 2; cập nhật Spike 0, nguồn lực, hợp đồng dữ liệu, kiểm chứng và phát hành  
**Ngày lập:** 07/11/2026  
**Vị trí lưu:** D:\review-phim\KE_HOACH_XAY_DUNG_MOI_MOVIE_REVIEW_STUDIO_2026-11-07.md  
**Tình trạng:** KẾ HOẠCH CHỜ PHÊ DUYỆT; P0 hiện BLOCKED do chưa chứng minh đủ owner, nguồn có quyền, reviewer và golden set. Các ước lượng/điểm/ngưỡng là giả thuyết, chưa có sản phẩm hoặc benchmark P3/P4.

> **Tuyên bố dự án mới:** Tài liệu này mô tả một sản phẩm được thiết kế từ số 0. Không dùng mã nguồn, cơ sở dữ liệu, cấu trúc repository, checkpoint, kết quả kiểm thử hoặc quyết định kiến trúc của bất kỳ dự án trước nào làm điều kiện tiên quyết. Các công cụ mã nguồn mở chỉ là ứng viên để đánh giá và tích hợp mới, không mặc nhiên được chấp thuận để cài đặt/phân phối.

---

## 1. TÓM TẮT ĐIỀU HÀNH

**Movie Review Studio** là ứng dụng desktop chạy trên Windows, giúp một người dùng tạo **video kể lại và bình luận nội dung phim bằng tiếng Việt** từ video có quyền sử dụng. Phim đầu vào có thể chứa lời thoại tiếng Anh, Trung, Nhật, Hàn hoặc Việt; các ngôn ngữ hỗ trợ thực tế phải được kiểm chứng từng loại, không tuyên bố hỗ trợ hoàn chỉnh chỉ dựa trên model card.

Định hướng sản phẩm: **giao diện dễ thao tác + lõi điều phối nhỏ do dự án phát triển + các engine mã nguồn mở chạy cục bộ**. Không tự viết lại mô hình ASR, OCR, TTS, VLM, codec hoặc engine dựng video khi đã có dự án phù hợp. Tuy vậy, vẫn phải tự phát triển dữ liệu liên thông, quy trình xét duyệt và cách biến dữ liệu nguồn thành review có căn cứ.

**Kết quả cuối cùng mong muốn:** một MP4 có cảnh/hình minh họa được phép dùng, lời dẫn review tiếng Việt nghe được, phụ đề Việt tùy chọn, cùng tệp kịch bản và báo cáo kiểm tra.

**Nguyên tắc triển khai đề xuất:** chạy AI local/offline sau giai đoạn chuẩn bị; không truyền phim, lời thoại hoặc dữ liệu dự án đến dịch vụ cloud; không tải model khi đang xử lý job. Kết nối mạng chỉ dành cho bước nghiên cứu/cài đặt *có cho phép* trước khi đóng gói offline.

**Thành công đầu tiên:** tạo được video review từ một video truyện ngắn có quyền dài 3–10 phút, lời thoại tiếng Anh, xuất MP4 có voice Việt thực sự, chạy từ GUI và có người nghe/xem xác nhận. Chưa đặt mục tiêu xử lý phim điện ảnh 120 phút ngay ở bản đầu.

## 2. PHẠM VI VÀ RANH GIỚI

### 2.1. Chức năng nằm trong sản phẩm
1. Tạo, lưu, mở lại dự án trên Windows.
2. Chọn video nguồn, quét video/audio/subtitle tracks, kiểm tra codec và quyền.
3. Tách tiếng, nhận dạng lời thoại gốc có mốc thời gian; ưu tiên phụ đề có sẵn nếu đáng tin.
4. Dịch nghĩa lời thoại/phụ đề sang tiếng Việt, giữ nguyên bản nguồn để so sánh.
5. Phát hiện các cảnh cắt; trích khung hình có thời gian và tạo mô tả bằng AI thị giác khi cần.
6. Gộp lời thoại + hình ảnh thành bản đồ sự kiện, nhân vật và trình tự câu chuyện.
7. **AI tự tạo kịch bản review tiếng Việt** gồm dàn ý, lời kể nhiều phần, bình luận có căn cứ, liên kết nguồn và kiểm duyệt; không phải dịch/lồng tiếng nguyên phim. Chi tiết bắt buộc tại **Mục 2.5**, tiêu chí đo tại **Mục 15.4–15.6**.
8. Cho phép người dùng duyệt/sửa kịch bản, chọn giọng đọc và nghe thử.
9. Sinh voice Việt, ghép video minh họa theo timeline, trộn audio, xuất H.264/AAC MP4.
10. Kiểm tra file đầu ra, theo dõi tiến trình, xử lý lỗi, lưu lịch sử và xuất báo cáo.

### 2.2. Ngoài phạm vi bản đầu
- Video editor chuyên nghiệp đầy đủ hiệu ứng như Premiere/DaVinci.
- Tự động đăng lên mạng xã hội, tải hoặc thu thập phim trái phép.
- Vượt DRM, tránh Content ID, hoặc tự bảo đảm một clip là hợp pháp.
- Tuyên bố AI hiểu đúng toàn bộ phim dài/phim phi tuyến mà không cần người kiểm tra.
- Tối ưu chạy nhiều video và nhiều model nặng đồng thời.
- Gói ứng dụng thương mại có model/giọng đi kèm trước khi hoàn tất rà soát giấy phép.

### 2.3. Giả định phải xác nhận trong giai đoạn thiết kế
- Người dùng ưu tiên Windows và tiếng Việt.
- Ứng dụng tập trung vào **review phim**, không phải sản phẩm dịch phụ đề/dubbing.
- Một máy một người vận hành tại thời điểm đầu.
- **P0 phải có một video narrative EN dài 3–10 phút**, quyền phân tích và quyền tái sử dụng clip/âm thanh có văn bản và SHA; chưa có thì P0 BLOCKED.
- **P0 phải chỉ định reviewer và lịch chấm, chuẩn bị ba review Việt viết tay, transcript/bản nghĩa và 10–15 câu hỏi diễn biến có đáp án/timecode** để tạo golden set P1.
- **Trước P0 phải chốt hình thức phân phối** (nội bộ/mã mở/thương mại) để làm cơ sở đánh giá GPL/LGPL/voice/model.
- Chính sách AI local-only là mặc định của kế hoạch mới, có thể được chủ dự án xác nhận độc lập.

### 2.4. Ba điều kiện chất lượng bắt buộc ngay từ đầu

1. **Định nghĩa "review hay":** rubric 5 tiêu chí × 0–2 điểm, thử ngưỡng tổng ≥8/10 và không tiêu chí = 0; đây **chỉ là giả thuyết đo P1**, không phải ngưỡng đạt đã được chứng minh. Critical claim sai hoặc thiếu quyền chặn phát hành bất kể tổng điểm. Chi tiết tại Mục 15.4.
2. **Không để LLM tự chứng nhận claim:** bộ kiểm chứng không-LLM kiểm tra nguồn/timecode, exact/contains, truy hồi semantic similarity và rule contradictions **trước**; không có bằng chứng thì tự đặt INSUFFICIENT_EVIDENCE, không dùng similarity để nâng SUPPORTED. Chỉ reviewer hoặc deterministic rule được chứng minh cho loại claim hẹp mới xác nhận. Xem Mục 2.5.4, 2.5.6.
3. **Bộ chuẩn P0/P1:** một video EN có quyền, transcript nguồn và nghĩa Việt chuẩn, tối thiểu ba review Việt viết tay có phong cách, 10–15 câu hỏi diễn biến có đáp án/timecode, reviewer/lịch chấm và rubric phải có trước khi đo model. Không đạt thì P0/P1 BLOCKED; không hứa chất lượng P3/P4.

### 2.5. PHÂN HỆ CỐT LÕI — AI TẠO KỊCH BẢN REVIEW PHIM

> **MUST HAVE — KHÔNG PHẢI TÍNH NĂNG PHỤ.** Ứng dụng phải **tự động viết một kịch bản review phim mới bằng tiếng Việt từ nội dung phim** và chuyển kịch bản đó thành lời thuyết minh/video. Không được lấy việc dịch lời thoại, xuất phụ đề hay cho người dùng tự gõ kịch bản thay thế chức năng này. AI Script Studio là chức năng sản phẩm cốt lõi.

#### 2.5.1. Mục đích và khác biệt với dịch phim

- **Dịch phim:** chuyển nghĩa từng câu thoại từ ngôn ngữ nguồn sang tiếng Việt.
- **Tóm tắt sự kiện:** xác định ai làm gì, lúc nào, ở đâu và vì sao theo dữ liệu nguồn.
- **Tạo kịch bản review:** chủ động chọn và sắp xếp sự kiện để kể lại phim dễ hiểu, bổ sung cách dẫn chuyện, điểm nhấn, nhận xét/bình luận và đoạn kết. Không dịch nguyên thoại hoặc bịa thêm tình tiết.
- Kịch bản được viết **sau khi** có story/evidence, **trước khi** tạo voice và dựng timeline.

#### 2.5.2. Input và output bắt buộc

**Đầu vào của tầng phân tích/audit:** source hash/timecode, transcript gốc, bản dịch Việt, scene, StoryEvent, evidence và lexicon thực thể. EvidenceStore giữ raw transcript và đoạn nguồn gốc.

**Đầu vào duy nhất của ReviewScriptGenerator:** StoryEvent.description_vi có event_id, fact slots, entity lexicon đã duyệt, mức spoiler, cấu hình văn phong, và quote block được đánh dấu có nguồn. **Không đưa transcript.text_original hay translation.text_vi thô vào prompt viết review**; chỉ ClaimAuditService có quyền truy vấn chúng ở bước kiểm chứng. Mỗi câu FACT phải trỏ tối thiểu một event_ref và evidence_ref; câu nhận xét phải gắn OPINION, quote phải có quote_id. Không có chứng cứ thì INSUFFICIENT_EVIDENCE, không tự chấp nhận.

**Đầu ra:**
1. review_outline.json — dàn ý phân phần có liên kết source event/evidence.
2. review_script.json — section, paragraph, nội dung lời dẫn tiếng Việt, claims và dấu thời gian tham khảo.
3. review_script.md — bản kịch bản có thể đọc, sửa, sao chép và xuất tệp.
4. script_validation.json — mệnh đề có chứng cứ, chưa đủ chứng cứ, mâu thuẫn, lặp, sai nhân vật hoặc lệch spoiler.
5. script_revision_manifest.json — model ID/revision/hash, source/config hash, version và reviewer decision.
6. voice_script.json — lời dẫn đã duyệt, tách câu để gửi TTS, không chứa ghi chú dựng phim.

#### 2.5.3. Tùy chọn người dùng

| Thuộc tính | Khả năng phải cung cấp |
|---|---|
| Kiểu review | Kể toàn bộ cốt truyện; recap có bình luận; phân tích nhân vật/chi tiết; giới thiệu không spoil |
| Giọng văn | Kể chuyện hấp dẫn; khách quan; cảm xúc; phân tích sâu; hài hước có kiểm soát |
| Cấu trúc | Hook → giới thiệu/bối cảnh → xung đột → biến cố → cao trào → kết (nếu được spoil) → bình luận |
| Thời lượng | Tự động theo diễn biến hoặc do người dùng đặt mục tiêu/khoảng phút |
| Mức spoiler | Không spoil; spoil có giới hạn; full recap |
| Bình luận | Mức độ phân tích/nhận xét, tách ý kiến chủ quan khỏi tình tiết đã xác minh |
| Sự kiện | Cho người dùng giữ/bỏ/chuyển thứ tự những sự kiện được chọn |
| Ngôn ngữ | Review script luôn là tiếng Việt; giữ bản nguồn và bản dịch riêng |

**Giới hạn sáng tạo:** AI được diễn đạt hấp dẫn và phân tích nhưng không được tự coi phỏng đoán là tình tiết phim. Tên riêng và quan hệ nhân vật phải nhất quán.

#### 2.5.4. Quy trình Review Script Generator

1. **Story Grounding:** đọc story events + evidence và ghép nhân vật/sự kiện, xử lý hồi tưởng và những đoạn chưa chắc chắn.
2. **Narrative Planning:** chọn các diễn biến cần giữ, nhóm chương, sắp xếp thứ tự kể, thiết kế hook/cao trào/kết và tạo outline JSON trước.
3. **Section Generation:** model local viết từng chương theo evidence/chính sách spoiler; không nhét cả phim dài vào một prompt rồi giả định model nhớ hết.
4. **Consistency Check:** kiểm tra trùng lặp, chuyển đoạn, nhân vật, thứ tự nguyên nhân–kết quả, thuật ngữ và mâu thuẫn giữa các chương.
5. **Claim Audit — không-LLM trước:** mỗi claim trỏ source SHA, timecode và đoạn chứng cứ/frame hash; schema/source bounds và deterministic rules kiểm tra phủ định, nhân vật, chronology. **Name grounding bắt buộc:** mọi danh từ riêng trong narration cần map vào entity lexicon từ nguồn (canonical, alias, phiên âm tiếng Việt, evidence refs). Tên lạ, alias chưa duyệt, hoặc NER mơ hồ → INSUFFICIENT_EVIDENCE + reviewer cảnh báo; không được tự đổi tên. Exact/contains chỉ xác nhận candidate quote; semantic similarity chỉ truy hồi nguồn; không có support hoặc chỉ similarity → INSUFFICIENT_EVIDENCE; mâu thuẫn → FLAG_CONTRADICTION. Chỉ người duyệt hoặc rule deterministic được chứng minh cho loại claim hẹp mới cấp SUPPORTED. Test phản ví dụ: tên bịa, alias hợp lệ, đảo phủ định, sai nhân vật, SHA đổi.
6. **Polish for Voice:** viết tự nhiên để đọc thành tiếng; điều chỉnh chỗ ngắt nghỉ, số và cách phát âm tên riêng; không thay đổi sự kiện.
7. **Human Review:** người dùng duyệt outline/kịch bản, xem cảnh gốc, sửa tay hoặc yêu cầu viết lại một phần.
8. **Duration Validation:** ước lượng bằng độ dài chữ, sau đó đo thời lượng WAV do TTS tạo thật; nếu lệch, rút/gia tăng nội dung có kiểm soát và duyệt lại.
9. **Version & Handoff:** chỉ kịch bản đã APPROVED đúng revision/hash được đưa sang VieNeu-TTS và timeline; mọi thay đổi nội dung/nguồn quan trọng làm mất hiệu lực duyệt cũ.

#### 2.5.5. AI Script Studio — điều khiển phải có thật trên GUI

- **Tạo dàn ý bằng AI:** sinh outline, hiển thị các sự kiện nguồn theo từng phần.
- **Tạo kịch bản review bằng AI:** sinh lời dẫn tiếng Việt hoàn chỉnh nhiều đoạn/phần, hiển thị tiến trình thật.
- **Viết lại đoạn:** dài/ngắn hơn, thay đổi giọng kể, tăng bình luận nhưng chỉ dựa vào dữ kiện đã có.
- **Kiểm tra nội dung:** hiển thị claims được hỗ trợ, nguồn còn thiếu, chỗ có mâu thuẫn.
- **Xem cảnh gốc:** chọn câu kịch bản để phát phim tại timestamp tham chiếu.
- **Chỉnh sửa và lưu phiên bản:** Undo/Redo, so sánh revision, autosave, export Markdown/TXT.
- **Duyệt kịch bản:** ghi reviewer, hash, cấu hình, thời điểm và evidence set.
- **Chuyển sang tạo giọng đọc:** chỉ bật sau khi đạt review gate; TTS chỉ đọc lời dẫn, không đọc notes/JSON.

**Bố cục đề xuất:** trái = outline + event list; giữa = trình soạn kịch bản Việt; phải = nguồn chứng cứ/cảnh gốc và cảnh báo fact; trên = style/spoiler/duration; dưới = phiên bản/approval + Voice Studio.

#### 2.5.6. Kiến trúc phần mềm riêng cho phân hệ

| Module | Chức năng | Sử dụng gì có sẵn |
|---|---|---|
| StoryPackBuilder | Chọn events và evidence, rút gọn theo ngữ cảnh | Tự viết |
| ReviewNarrativePlanner | Dàn ý và chiến lược kể chuyện | Tự viết, gọi LLM local |
| ReviewScriptGenerator | Viết từng phần, ghép review dài | Tự viết adapter trên llama.cpp |
| **ClaimAuditService** | **Non-LLM first:** kiểm tra evidence refs, nguồn/timecode, exact/contains cho quote, semantic retrieval, rules phủ định/tên/chronology; NO_SUPPORT → INSUFFICIENT_EVIDENCE; conflict → human review | Tự viết deterministic validators + embeddings để tìm nguồn + human reviewer; **LLM không được tự cấp SUPPORTED** |
| ScriptRevisionStore | Lưu version, diff, hash, duyệt | SQLite/JSON |
| ScriptStudioPage | Editor và nút sinh/viết lại/duyệt | PySide6 |
| NarrationHandoff | Chia lời dẫn, giữ pronunciation map và script hash | VieNeu-TTS ở bước sau |

**Model text — rủi ro then chốt:** llama.cpp là runtime, Qwen text 4B/8B chỉ là ứng viên cần benchmark **khả năng viết review Việt dài, không lặp, đúng diễn biến**. P1 dùng 03 bài review mẫu + rubric (Mục 15.4) và 10–15 câu hỏi diễn biến, không giả định 8B đủ tốt. VRAM của model GGUF còn tính KV-cache và runtime; một heavy GPU model tại một thời điểm. Nếu P1 thất bại, giảm phạm vi review ngắn hoặc tăng biên tập thủ công, không ép P3 PASS.

**Chú ý:** Không có thư viện nào trong danh sách GitHub thay thế trọn vẹn lõi hiểu phim → chọn sự kiện → viết review mới → audit tính đúng. Đây là phần dự án phải tự thiết kế và kiểm thử.

#### 2.5.7. Hợp đồng dữ liệu cho đủ sáu đầu ra của AI Script Studio

**Quy ước:** JSON Schema/Pydantic phải validate, mọi artifact có schema_version, project ID, source SHA, producer/model revision, timestamps; ID ổn định và SHA của file nguồn hoặc revision. Các ví dụ dưới đây **chỉ là contract thiết kế**, không phải kết quả chạy mô hình. JSON là nguồn dữ liệu có thẩm quyền, Markdown và voice handoff được dẫn xuất từ revision đã xác thực.

**1) review_outline.json — OutlineContract v1**

~~~json
{"schema_version":"outline_v1","project_id":"PRJ_DEMO","source_sha256":"<sha>","sections":[{"section_id":"SEC_001","purpose":"hook","event_refs":["EV_001"],"target_words":100,"spoiler_level":"full"}],"state":"DRAFT"}
~~~

**2) review_script.json — ScriptContract v1**

~~~json
{"schema_version":"review_script_v1","project_id":"PRJ_DEMO","source_sha256":"<sha>","entity_lexicon_sha256":"<sha>","status":"NEEDS_REVIEW","sections":[{"section_id":"SEC_001","narration_vi":"Đây chỉ là văn bản ví dụ.","sentences":[{"sentence_id":"S_001","kind":"FACT","text_vi":"Đây chỉ là văn bản ví dụ.","event_refs":["EV_001"],"evidence_refs":["EVID_001"],"claim_refs":["CL_001"],"audit_state":"INSUFFICIENT_EVIDENCE"}]}],"revision_sha256":null}
~~~

Loại câu: FACT, OPINION, QUOTE, TRANSITION. **FACT cần event_ref; OPINION được đánh dấu rõ, không được là suy đoán trá hình; QUOTE cần quote_id và evidence_ref**. Việc có ref chưa đủ để tự xác nhận đúng.

**3) review_script.md — RenderedMarkdownContract v1** (được tái tạo từ JSON, không phải nguồn sự thật riêng)

~~~markdown
<!-- schema: review_script_md_v1; source: review_script.json; revision: <sha> -->
# Kịch bản review tiếng Việt
## SEC_001 — Mở đầu
Đây chỉ là văn bản ví dụ.
~~~

Chỉ xuất **lời đọc** vào Markdown voice handoff; ghi chú đạo diễn/claim/provenance lưu riêng trong JSON.

**4) script_validation.json — AuditContract v1**

~~~json
{"schema_version":"script_validation_v1","script_revision_sha256":"<sha>","claims":[{"claim_id":"CL_001","sentence_id":"S_001","evidence_refs":["EVID_001"],"signals":{"exact_quote_match":false,"semantic_retrieval_score":0.67,"entity_grounding":"UNKNOWN"},"state":"INSUFFICIENT_EVIDENCE","reason":"Chưa đủ căn cứ"}],"critical_open_count":1,"gate":"NEEDS_REVIEW"}
~~~

**Semantic similarity chỉ tìm nguồn ứng viên**, không bao giờ tự đẩy claim lên SUPPORTED.

**5) script_revision_manifest.json — RevisionApprovalContract v1**

~~~json
{"schema_version":"script_revision_manifest_v1","project_id":"PRJ_DEMO","source_sha256":"<sha>","story_pack_sha256":"<sha>","entity_lexicon_sha256":"<sha>","script_revision_sha256":"<sha>","model_id":"<local-model>","model_revision_sha256":"<sha>","generation_config_sha256":"<sha>","audit_sha256":"<sha>","reviewer_ids":[],"reviewer_decisions":[],"approved":false,"approved_at_utc":null}
~~~

**6) voice_script.json — NarrationHandoffContract v1**

~~~json
{"schema_version":"voice_script_v1","approved_script_revision_sha256":"<sha>","language":"vi","voice_id":"<licensed-voice>","segments":[{"cue_id":"CUE_001","section_id":"SEC_001","sentence_ids":["S_001"],"text_vi":"Đây chỉ là văn bản ví dụ.","pronunciation_map_ref":"<ref>","output_start_ms":null,"duration_ms":null}],"ready_for_tts":false}
~~~

ready_for_tts chỉ true khi script revision chính xác đã được duyệt, không còn critical claim chưa giải quyết, license voice hợp lệ. Thời gian cue cập nhật sau khi đo WAV TTS thực.

**Contract tests P2:** mọi file validate schema + source/revision/hash; tên/ID dẫn chiếu tồn tại, source timestamps hợp lệ, quote được gắn nhãn; thay script/source/rights/lexicon làm invalid approval và voice; dữ liệu không đạt schema không được chuyển TTS, timeline hay phát hành.

#### 2.5.8. Kiểm thử và nghiệm thu bắt buộc

- Với phim truyện ngắn tiếng Anh có quyền dài 3–10 phút trong golden set P0/P1: tự tạo được **outline và kịch bản review Việt nhiều đoạn đủ mở–thân–kết** từ transcript + events + cảnh/video có chứng cứ, không phải bản dịch lời thoại. **Không bắt buộc VLM tự hiểu phim ở P3** nếu reviewer đã xác nhận scene/events.
- Các biến cố/nhân vật chính không bị thay đổi, thứ tự nhân quả hợp lý, tình tiết không chắc chắn hiện cảnh báo và có source timestamps.
- Nút viết lại **một đoạn** không làm mất các đoạn khác hoặc giả mạo evidence.
- Đổi style/độ dài/spoiler cho kết quả đáp ứng cấu hình; nếu giới hạn không khả thi, phải báo chứ không bỏ bừa sự kiện trọng yếu.
- Editor xem nguồn và sửa duyệt, phiên bản phê duyệt có hash; sửa sau duyệt buộc duyệt lại phần bị ảnh hưởng.
- Voice TTS nhận đúng lời dẫn Việt và MP4 cuối có tiếng review nghe được.
- Reviewer có tên và lịch chấm đọc kịch bản, đối chiếu nguồn và chấm theo **rubric 5 tiêu chí ×0–2** Mục 15.4; ngưỡng thử **≥8/10, không điểm 0** và target overlap **≤5% 4-gram** chỉ là đề xuất trước baseline. **4-gram so script Việt với bản dịch transcript Việt, không so với transcript EN trực tiếp**; cả ba review người viết phải đo để hiệu chỉnh false positives.
- Bộ 10–15 câu hỏi diễn biến có đáp án/timecode được dùng để phát hiện thiếu hoặc sai tình tiết. Critical claim mâu thuẫn, thiếu reviewer hoặc thiếu quyền thì BLOCKED, bất kể điểm rubric.
- Nếu model không đạt chất lượng, báo BLOCKED/NEEDS_REVIEW; chỉnh StoryPack/section, giới hạn review ngắn 3–5 phút hoặc xin quyền thử model khác; không thay model hoặc hạ ngưỡng âm thầm.

#### 2.5.9. Điều chỉnh mức ưu tiên triển khai

- **P0 (điều kiện bắt buộc):** video EN có quyền + reviewer/lịch, 03 kịch bản VI viết tay, transcript/timecodes, bản dịch VI gold và 10–15 QA câu hỏi, rubric; chốt hình thức phân phối và owners.
- **P1:** spike sinh outline → script review VI từ gold events, benchmark LLM/RAM/VRAM, rubric và overlap 4-gram đã hiệu chuẩn; ClaimAudit không-LLM phải có phản chứng trước P3.
- **P2:** viết StoryPackBuilder, ReviewNarrativePlanner, ReviewScriptGenerator, ClaimAuditService **non-LLM first** và schema/revision tests.
- **P3:** chỉ đạt vertical-slice khi **script review AI tạo thật vượt ngưỡng rubric đã chốt tại P1**, có reviewer approval, voice và MP4 có hình/tiếng cùng rights/claims hợp lệ.
- **P4:** giao diện AI Script Studio phải điều khiển được các engine thật từ tạo dàn ý đến xuất kịch bản/duyệt.
- **P5/P6:** benchmark review đa ngôn ngữ/phim dài, kiểm soát nhân vật, độ phủ và đóng gói Windows.

**Definition of Done bổ sung:** Ứng dụng KHÔNG đạt MVP nếu chỉ có nút nhập kịch bản thủ công, dịch phụ đề, một câu demo, hoặc MP4 có TTS nhưng chưa có kịch bản review mới được tạo từ nội dung phim. **ENGINE_OK ≠ PRODUCT_E2E_OK ≠ RELEASE_READY**; rubric không đạt, thiếu reviewer/golden set, thiếu quyền hoặc unsupported critical claim đều NO-GO. Đây là kế hoạch đề xuất, **không cam kết chất lượng P3/P4 trước P1**.

---

**Ghi nhận cập nhật:** Mục 2.5 là phân hệ cốt lõi có UI, dữ liệu, workflow, audit không-LLM, rubric, reviewer và kiểm thử. Bản hiệu chỉnh này chỉ cập nhật kế hoạch; chưa lập trình hoặc chạy AI. Các ngưỡng chất lượng, license, runtime và model vẫn phải được P0/P1 kiểm chứng trước khi cam kết P3/P4.

## 3. LỰA CHỌN CÁCH XÂY DỰNG

| Phương án | Lợi thế | Bất lợi | Quyết định |
|---|---|---|---|
| A. Fork nguyên ứng dụng dịch/lồng tiếng trên GitHub | Nhanh có GUI và pipeline ban đầu | Khó thay luồng dubbing thành review; mã đan xen và ràng buộc GPL/online | Không chọn làm nền tảng |
| B. Tự viết toàn bộ AI và media | Kiểm soát tuyệt đối | Chi phí, thời gian và rủi ro rất cao | Không chọn |
| **C. Greenfield + các engine tách biệt qua adapter** | Dễ thay model, dễ test, GUI độc lập, giữ chủ quyền sản phẩm | Phải tự xây pipeline, bằng chứng, timeline và QA | **ĐỀ XUẤT** |

**Kết luận:** Chọn C. Có thể tham khảo cách tổ chức chức năng của các dự án khác, nhưng chỉ sao chép/ghép code khi giấy phép và nhu cầu kỹ thuật cho phép. Ưu tiên sử dụng các thư viện chuẩn và CLI/API chính thức thay vì copy từng module từ nhiều repository lớn.

## 4. KIỂM TRA MÔI TRƯỜNG MÁY MỤC TIÊU

Quan sát trực tiếp qua Remote Desktop Commander ngày 07/11/2026:
- Máy Windows kết nối: **DESKTOP-2GP3E41**.
- Đã truy cập thực tế: **D:\review-phim**, thư mục **trống** tại thời điểm kiểm tra.
- CPU đọc từ Windows CIM: **Intel Core i7-14700F**.
- GPU đọc từ Windows CIM: **NVIDIA GeForce RTX 4060**.
- RAM vật lý do Windows trả về: **34,138,730,496 byte** (xấp xỉ 31.8 GiB).

Chưa kiểm tra trong đợt này: VRAM trống/driver thực tế, phiên bản Python, ffmpeg, CUDA/cuDNN, tốc độ ổ đĩa, quyền phần mềm, nhiệt độ và chất lượng AI. Những dữ liệu này là hạng mục **hardware/runtime audit** đầu dự án, không được suy diễn là đã đạt.

**Tương thích GPU Windows cần đo ở P1:** Faster-Whisper dùng CTranslate2 phiên bản mới cần thư viện **CUDA 12 cuBLAS + cuDNN 9** tương thích với wheel đã pin; driver hiện thị mức CUDA cao hơn không có nghĩa DLL runtime CUDA 12 đã có. Khi chạy trên Windows, đường dẫn DLL phải thực sự được worker resolve qua PATH hoặc cơ chế runtime đã kiểm thử; không tự ý sửa PATH hệ thống. Phải test import và GPU inference thật, không chỉ xem `nvidia-smi`.

**Ngân sách tài nguyên đề xuất để thử:** một tiến trình AI GPU nặng một lúc; trích frame có giới hạn; lưu proxy/cache có quota; TTS trên CPU nếu đủ nhanh. Các model 8B quantized có thể chiếm phần lớn VRAM 8 GB khi tính KV-cache/vision projector, **không chạy đồng thời với ASR**. Vision CPU fallback chỉ là đường thoát có cảnh báo hiệu năng và có thể không khả thi. Đo free/peak VRAM, memory/OOM và RTF trước khi khóa model; không coi số GB ước tính là benchmark máy.

## 5. DANH MỤC DỰ ÁN GITHUB ỨNG VIÊN

Danh mục kiểm tra nguồn công khai ngày 07/11/2026. **Giấy phép code** khác **giấy phép trọng số model, voice, asset, codec và mục đích thương mại**.

| Khối chức năng | Repository / nguồn | Giấy phép code được hiển thị | Vai trò đề xuất | Trạng thái |
|---|---|---|---|---|
| ASR tiếng nói | https://github.com/SYSTRAN/faster-whisper | MIT | ASR có timestamp, CPU/GPU | ỨNG VIÊN |
| Tách scene/shot | https://github.com/Breakthrough/PySceneDetect | BSD-3-Clause | Phát hiện điểm cắt + keyframe | ỨNG VIÊN |
| OCR đa ngôn ngữ | https://github.com/PaddlePaddle/PaddleOCR | Apache-2.0 | Nhận chữ/hard subtitle | ỨNG VIÊN |
| Dịch offline | https://github.com/argosopentech/argos-translate | MIT/CC0 theo upstream | Baseline dịch để so với LLM | ỨNG VIÊN |
| Local LLM runtime | https://github.com/ggml-org/llama.cpp | MIT | Chạy model text/VLM quantized | ỨNG VIÊN |
| Vision-language | https://github.com/QwenLM/Qwen3-VL | Apache-2.0 cho repo | Hiểu khung hình/clip ngắn | CẦN SPIKE + MODEL LICENSE |
| Tiếng Việt TTS | https://github.com/pnnbao97/VieNeu-TTS | Apache-2.0 cho repo | Tạo giọng đọc Việt local | CẦN SPIKE + VOICE LICENSE |
| Biểu diễn timeline | https://github.com/AcademySoftwareFoundation/OpenTimelineIO | Apache-2.0 | Chuẩn hóa clip/timing | ỨNG VIÊN |
| Mã hóa & dựng video | https://ffmpeg.org/ | LGPL/GPL tùy build | Probe, trim, filter, mix, encode | CẦN LICENSE AUDIT |
| GUI desktop | https://www.qt.io/development/qt-framework/python-bindings | LGPLv3/GPL/commercial tùy cách dùng | Giao diện PySide6 | CẦN LICENSE AUDIT |
| Dự án tham khảo | https://github.com/jianchang512/pyvideotrans | GPL-3.0 | Học workflow dịch/lồng tiếng, **không lấy làm nền** | THAM KHẢO |

**Lưu ý quan trọng:**
- **Faster-Whisper/CTranslate2 GPU:** cấu hình ứng viên cho các bản CTranslate2 mới là CUDA 12 cuBLAS + cuDNN 9; trên Windows phải bảo đảm worker resolve đúng DLL qua PATH/runtime được test. Không suy ra runtime từ số CUDA báo trên driver; chốt wheel/DLL version bằng spike.
- **PySceneDetect:** phát hiện điểm cắt được qua OpenCV/PyAV, **không bắt buộc FFmpeg để detect**; FFmpeg có thể dùng cho split-video/ingest/render.
- OpenTimelineIO lưu mô hình timeline và trao đổi dữ liệu; **không tự render MP4**. FFmpeg vẫn là công cụ dựng/encode.
- PaddleOCR có hỗ trợ đa ngôn ngữ, nhưng phải kiểm tra chính xác model nhận chữ cần dùng trên ảnh video thực.
- Argos Translate cần gói dịch offline cho từng cặp; nên chấm chất lượng riêng thay vì mặc định đủ tốt.
- **Qwen3-VL qua llama.cpp:** bản GGUF cần kiểm đủ **model GGUF + mmproj projector** cùng revision/template/tokenizer; cân nhắc ứng viên VLM nhỏ (2B/4B) thay vì giả định 8B vừa VRAM. Chưa phê duyệt model trước benchmark/license.
- **VieNeu-TTS:** ưu tiên khảo sát **v3 Turbo open-source** cho local; không dựa vào v4 proprietary/API khi yêu cầu offline. Giấy phép repo GitHub **không thay thế** giấy phép weights, codec và voice/reference audio.
- **FFmpeg:** kiểm tra build flags, LGPL/GPL/nonfree và quyền phân phối binary. Chạy GPL-enabled FFmpeg qua CLI/process riêng có thể giảm vấn đề liên kết nhưng **không tự động bảo đảm tuân thủ pháp lý**; rà soát riêng cho kịch bản phân phối.
- **PySide6:** tùy LGPLv3/GPL/commercial, phải có kế hoạch notice, nguồn/offer và khả năng thay thế thư viện khi áp dụng LGPL; kiểm tra cách đóng gói PyInstaller/Nuitka trước phát hành.


## 6. KIẾN TRÚC SẢN PHẨM MỚI

**Một ứng dụng desktop + một local engine host + các adapter độc lập.** Không cần microservices, container hoặc Kubernetes cho bản đầu. Các tác vụ AI dài chạy subprocess, GUI không chờ đồng bộ.

~~~mermaid
flowchart TD
    UI["PySide6 Desktop: Project / Analysis / AI Script Studio / Voice / Timeline"]
    APP["Local Application Controller + Job Scheduler + Approvals"]
    DB[("SQLite: projects / jobs / approvals")]
    STORE[("Versioned evidence / script / WAV / timeline / MP4")]
    ASR["Faster-Whisper + Media Ingest"]
    SCENE["PySceneDetect + OCR / VLM (optional)"]
    TRANSLATE["Offline Translator"]
    STORY["StoryPack Builder + evidence"]
    SCRIPT["Local LLM + Review Script Generator"]
    AUDIT{"Non-LLM Claim Audit + Human Review"}
    VOICE["VieNeu-TTS v3 Turbo"]
    TIMELINE["Rights-first EDL / OTIO timeline"]
    RENDER["FFmpeg render + technical QA"]
    UI --> APP
    APP --> DB
    APP --> ASR
    APP --> SCENE
    APP --> TRANSLATE
    ASR --> STORY
    SCENE --> STORY
    TRANSLATE --> STORY
    STORY --> SCRIPT
    SCRIPT --> AUDIT
    AUDIT -->|Needs review| UI
    AUDIT -->|Approved revision| VOICE
    VOICE --> TIMELINE
    STORY --> TIMELINE
    TIMELINE --> RENDER
    RENDER --> STORE
    STORE --> UI
~~~

### 6.1. Các lớp do dự án tự viết
- **Desktop/UI:** widgets, player, timeline, biểu mẫu và trang duyệt; không chứa logic AI.
- **Application:** start/pause/resume/cancel, cấu hình job, chính sách duyệt và trạng thái.
- **Domain:** project, media track, timestamp, transcript, translation, evidence, event, claim, narration, edit decision, rights.
- **Adapters:** một giao diện thống nhất cho thư viện và CLI bên ngoài; runtime thực có thể thay mà không ảnh hưởng GUI.
- **Workers:** cách ly model crash/FFmpeg, kiểm soát resource, timeout, retry, progress, checkpoint.
- **Storage:** SQLite chứa chỉ mục và state, tệp JSON/WAV/MP4 chứa artifact; checksum xác định phiên bản.
- **Quality:** xác thực schema, độ phủ evidence, QC media, quyền sử dụng, bước duyệt của con người.

### 6.2. Không đưa những gì vào lõi
- Không copy toàn bộ code một dự án lớn rồi thêm GUI khác.
- Không để prompt của LLM trực tiếp gọi file system, shell hoặc quyết định quyền phát hành.
- Không gắn cứng file path/model ID ở GUI hoặc domain.
- Không trộn đầu vào transcript, bản dịch, kịch bản review thành một trường duy nhất.
- Không để từng adapter tự tạo kết quả MP4 tùy ý mà bỏ qua timeline, approval và quality.

## 7. HỢP ĐỒNG CHUẨN GIỮA CÁC MODULE

Mỗi adapter nhận input đã validate, trả output có version và thông tin xuất xứ. Các trường tối thiểu đề xuất:

| Dữ liệu | Trường cần có |
|---|---|
| SourceMedia | source_id, path, sha256, duration_ms, streams, rights_ref |
| TrackSelection | video_stream, audio_stream, subtitle_stream?, language_override?, selection_reason |
| TranscriptSegment | id, source_id, audio_stream, start_ms, end_ms, text_original, language, model_ref |
| TranslationSegment | id, source_segment_ids, text_vi, status, model_ref, reviewer_note |
| VisualObservation | id, shot_id, frame_pts_ms, frame_sha, description, source_type, confidence, review_status |
| StoryEvent | event_id, description_vi (sanitized), character_entity_ids, causal_links, source_spans, evidence_refs, uncertainty, provenance_sha |
| EvidenceRef | evidence_id, source_sha, source_span_ms, excerpt_original, translation_ref?, frame_sha?, access_role=audit_only |
| EntityLexicon | entity_id, canonical, name_type, aliases_vi, transliterations, source_evidence_refs, reviewer_status |
| ReviewClaim | id, script_span, claim_kind, evidence_refs, verified_status |
| Narration | text_vi, script_revision, voice_ref, wav_path, wav_sha, duration_ms |
| TimelineClip | source_id, source_in_ms, source_out_ms, output_in_ms, output_out_ms, rights_ref, loop_policy (default=forbidden), watermark_or_logo_rights |
| RenderMix | narration_wav_sha, original_audio_policy (mute/duck), loudness_target_lufs, true_peak_ceiling_dbtp, subtitle_clock=output_narration, ducking_profile |
| RenderResult | mp4_path, video_stream, audio_stream, duration_ms, sha256, qc_state |

**Bất biến kỹ thuật:** source timecode không mất khi chia chunk; mỗi claim quan trọng liên kết được tới nguồn; chỉ chọn clip trong phạm vi có quyền; final MP4 phải có audio lời dẫn kiểm tra được; thay script/voice/rights làm vô hiệu các artifact phụ thuộc.

**Chuẩn tiến trình:** QUEUED → RUNNING → NEEDS_REVIEW → READY_TO_RENDER → RENDERING → TECHNICAL_OK → EDITORIAL_OK → DELIVERED; trạng thái FAILED, CANCELLED và BLOCKED phải có nguyên nhân rõ. Không dùng phần trăm 100% để thay quyết định nghiệm thu.

## 8. PIPELINE SẢN XUẤT TỪ ĐẦU ĐẾN CUỐI

1. **Import:** chọn video nguồn; quét bằng ffprobe; kiểm tra metadata, stream và quyền.
2. **Index:** lấy audio, nhúng phụ đề, nhận diện scene cut và tạo proxy/keyframe với PTS.
3. **Speech:** ASR theo nguồn, người dùng có thể sửa; không tự ép toàn bộ lời thoại sang tiếng Việt.
4. **Translate:** dịch Việt trên transcript, lưu hai lớp song song; cảnh báo chỗ bất định/tên riêng.
5. **Visual:** PySceneDetect phát hiện scene bằng OpenCV/PyAV; FFmpeg tách proxy/frame khi cần. OCR/VLM là tùy chọn theo benchmark và quyền model, không bắt buộc VLM hiểu phim cho MVP. Với phim dài, chunk **theo scene**, giữ source PTS và overlap kiểm soát.
6. **Story (map–reduce):** map từng chunk thành event + evidence/timestamp, deduplicate các event do chunk overlap, reduce thành story graph chung với nhân vật/alias, chronology/flashback và uncertainty; người dùng kiểm tra các mâu thuẫn. Không đưa toàn bộ phim dài vào một prompt.
7. **Generate Review Script (PHÂN HỆ BẮT BUỘC):** từ StoryEvent + source evidence, AI local xây dàn ý, viết từng phần kịch bản review tiếng Việt có mở bài, kể diễn biến, bình luận, kết và liên kết mốc phim; người dùng chọn phong cách, độ dài, spoiler, sửa/viết lại từng đoạn. Kiểm chứng claim quan trọng trước duyệt. Xem đặc tả chi tiết tại **Mục 2.5**.
8. **Approve:** người dùng chỉnh sửa và duyệt script; khóa revision để tạo audio nhất quán.
9. **Narrate:** chạy VieNeu-TTS, kiểm tra WAV và thời lượng, cho nghe thử/sửa phát âm.
10. **Edit:** tìm clip theo event; hard filter quyền footage; tạo timeline từ EDL/OTIO; cho phép chỉnh sửa thủ công.
11. **Render theo luật:** nếu narration dài hơn tổng clip hợp quyền, ưu tiên clip hợp lệ khác → graphic/title card có quyền → xin duyệt; **không tự lặp B-roll** hoặc cắt bỏ sự kiện critical. Đặt loop_policy chỉ khi clip thực sự được phép/không gây sai nghĩa. FFmpeg cắt source in/out, mix lời dẫn; mute audio gốc mặc định hoặc duck khi đủ quyền. Preset thử nghiệm **-14 LUFS integrated, true peak ≤ -1 dBTP** chỉ là đề xuất, cần đo lại theo kênh; subtitle lấy timecode **narration/output**, không phải source sau dựng.
12. **Quality:** ffprobe check stream; kiểm tra tiếng Việt nghe được, sync, loudness/clipping, thời lượng, hình đen, quyền watermark/logo/font; người dùng xem/nghe, duyệt rồi xuất bản kết quả.

**Tình huống thiếu bằng chứng hoặc clip hợp lệ:** đưa sự kiện vào hàng chờ xem xét hoặc dùng đồ họa/title card có quyền; không tự bịa cảnh, không để similarity score thay quyền footage hoặc human review.

**Giấy phép/đóng gói:** Phân phối theo GPL/LGPL, Qt/PySide6, FFmpeg build flags, model weights và giọng là quyết định phụ thuộc hình thức phát hành. Chạy FFmpeg theo CLI/process riêng không tự loại bỏ mọi nghĩa vụ pháp lý. Kiểm tra hồ sơ giấy phép riêng trước khi phân phối.

## 9. THIẾT KẾ GIAO DIỆN ĐỀ XUẤT

| Màn hình | Mục đích | Điều kiện để coi là hoạt động |
|---|---|---|
| Home/Projects | Tạo, tìm, mở lại project | Lưu SQLite và reopen đúng |
| Import Media | Chọn video, xem audio/subtitle, quyền | Probe đúng dữ liệu thật |
| Analysis | Player nguồn + transcript gốc/dịch Việt + scene list | Click câu/scene seek đúng mốc |
| Story Board | Sự kiện, nhân vật, mốc phim, bằng chứng | Chỉnh/sửa evidence được |
| **AI Script Studio — Tạo kịch bản review** | **Tạo kịch bản tự động** từ sự kiện đã phân tích; cấu hình phong cách/độ dài/spoiler; dàn ý, mở bài, diễn biến, bình luận, kết; viết lại từng phần, xem nguồn và duyệt | **Sinh được review Việt nhiều đoạn thật**, lưu revision + evidence links, kiểm tra critical claims và invalidate job phụ thuộc; chi tiết Mục 2.5 |
| Voice Studio | Chọn giọng, phát thử, nghe WAV | TTS real local và audio playback |
| Timeline Editor | Clip bins, preview, cắt và bố trí cảnh | Timeline đi vào FFmpeg render |
| Export & QA | Preset, tiến độ, playback MP4, báo lỗi | H.264/AAC có tiếng, được người duyệt |
| Settings/Models | Chọn model local, device, disk quota | Không tự download; kiểm file/checksum |
| Diagnostics | Nhật ký, tool versions, GPU/RAM, log lỗi | Chẩn đoán lỗi không lộ dữ liệu riêng tư |

**Nguyên tắc UI:** tối ưu cho độ phân giải 1920×1080, đường dẫn tiếng Việt, trạng thái task và khả năng mở lại. Cửa sổ không treo khi AI chạy. Nút Pause/Cancel phải có chức năng thật. Người dùng luôn có thể so sánh kịch bản với đoạn nguồn.

## 10. CHIẾN LƯỢC TÍCH HỢP OPEN SOURCE

### 10.1. Quy trình tiếp nhận từng repository
1. Ghi repo URL, release/commit, license code, license weight/voice/asset, nguồn download, hash.
2. Kiểm tra tình trạng bảo trì, dependency native DLL và hỗ trợ Windows.
3. Chạy một **spike độc lập** bằng dữ liệu giả/tự tạo, có input/output và tiêu chí pass.
4. Benchmark RAM/VRAM/latency trên máy mục tiêu, thử offline sau khi tải thủ công.
5. Viết adapter nhỏ có schema, timeout và logging.
6. Chạy contract tests, integration tests và kiểm tra binary/dependency licenses.
7. Chỉ sau khi đạt mới chuyển trạng thái ứng viên thành APPROVED_FOR_INTEGRATION.

### 10.2. Ưu tiên chọn công cụ
- **Bắt buộc thử đầu:** FFmpeg, PySceneDetect, Faster-Whisper, llama.cpp, VieNeu.
- **Thử song song nhưng có thể bỏ:** Argos Translate hoặc LLM dịch, PaddleOCR, Qwen3-VL.
- **Thêm khi timeline phức tạp:** OpenTimelineIO.
- **Không cần thêm ngay:** diarization phức tạp, vector database, nhiều model VLM, GPU deep learning framework lớn.

**Quyết định mở:** nếu Vision quá yếu hoặc nặng trên 8 GB, MVP đầu cho phép **đề xuất scene bằng thoại + keyframe và bắt người dùng duyệt**, không tự quảng cáo là hiểu phim hoàn toàn.

## 11. MÔ HÌNH LƯU TRỮ VÀ THƯ MỤC DỰ KIẾN

Chỉ là cấu trúc **dự kiến tạo khi khởi động phát triển**, không phải các thư mục đã tồn tại:

~~~text
D:\review-phim\
  README.md
  pyproject.toml
  src\movie_review_studio\
    ui\
    application\
    domain\
    adapters\
    workers\
    infrastructure\
    quality\
  config\
  schemas\
  tests\
    unit\
    contract\
    integration\
    e2e\
  assets\
  docs\
  models\             # manifests/đường dẫn, không tự thêm weights
  runtime_cache\      # có quota, có thể chuyển sang ổ SSD
  projects\           # tạo theo job/project
~~~

Một project chứa **source manifest, tracks, transcripts, translations, observations, story, script revisions, narration, timeline, MP4, approvals, checksums và QA**. File video gốc không bị ghi đè hoặc âm thầm thay thế.

## 12. LỰA CHỌN CÔNG NGHỆ PHẦN MỀM BAN ĐẦU

| Tầng | Khuyến nghị | Lý do |
|---|---|---|
| Ngôn ngữ | Python 3.12 venv riêng (chưa cài trong kế hoạch) | Tương thích tốt với các AI/media Python phổ biến; cần kiểm chứng bản pin |
| GUI | PySide6 Widgets | Desktop native Windows; đầy đủ controls/player thông qua backend phù hợp |
| API nội bộ | Python classes, typed contracts; subprocess IPC JSONL ban đầu | Không cần server HTTP |
| DB | SQLite (WAL khi phù hợp) | Một máy/một người dùng, dễ backup |
| Artifact | JSON/JSONL/WAV/SRT/OTIO/MP4 | Dễ debug, tách metadata khỏi file lớn |
| Schema | Pydantic hoặc JSON Schema | Phát hiện sai format sớm |
| AI runtime | llama.cpp; Faster-Whisper/CTranslate2; ONNX CPU nếu phù hợp | Ưu tiên local/nhẹ |
| Video | FFmpeg/ffprobe | Chuẩn hóa media, cắt và mix |
| Build/Test | pytest, pytest-qt, Ruff, PyInstaller hoặc Nuitka sau spike | Kiểm chứng & đóng gói Windows |

Không chốt phiên bản và không cài trước khi kiểm tra ma trận dependency/giấy phép. Có thể thay lựa chọn nếu spike chứng minh không phù hợp.


## 13. LỘ TRÌNH XÂY DỰNG THEO CÁC BẢN GIAO ĐƯỢC

Thời gian dưới đây là **trình tự** chứ không phải cam kết số ngày. Kết thúc một pha phải có sản phẩm/biên bản test quan sát được.

| Pha | Phạm vi | Bàn giao bắt buộc | Điều kiện chuyển pha |
|---|---|---|---|
| **P0 — Khóa quyền/chuẩn chất lượng** | Trước khi cài model, chốt nội bộ/open-source/thương mại; chỉ định owner + lịch review | **01 video EN 3–10 phút có quyền**, SHA & rights manifest; reviewer/lịch; **03 review Việt viết tay**; transcript EN/timecode & nghĩa VI; 10–15 QA câu hỏi; rubric, candidate scorecard, license inventory | **BLOCKED nếu thiếu bất cứ mục bắt buộc về quyền, reviewer, review mẫu** |
| **P1 — Engine spikes + baseline review** | FFmpeg/ASR/translation/scene/TTS/local LLM; **sinh review thực** từ story gold, test ClaimAudit non-LLM | Các phép đo WER/dịch, **rubric review 5×0–2**, tỷ lệ 4-gram với bản nghĩa Việt, kết quả QA 10–15 câu, model/VRAM/latency/rights/license | Reviewer chấm, owner **duyệt ngưỡng P3**; không đạt thì NO-GO cho cam kết P3/P4 |
| P2 — Core skeleton | Domain schema, SQLite, worker, orchestrator, adapter contracts | Project tạo/mở lại; job chạy/nhật ký/checkpoint | GUI không bị treo; mock/test pass |
| P3 — Real vertical slice | Video tiếng Anh 3–10 phút → script Việt **AI tự tạo theo rubric** → voice Việt → MP4 | MP4 thực và scorecard reviewer + claim/source/timecode + rights/hash/audio QC | **NO-GO nếu review Việt không đạt chuẩn đã hiệu chuẩn ở P1**, dù render PASS |
| P4 — Guided desktop UI | Import → analyze → edit script → voice → preview → export | Toàn workflow chạy trong GUI | Không dùng script thủ công để đi hết quy trình |
| P5 — Story + multilingual | Cảnh/hình, OCR, nhân vật, dịch EN/ZH/JA/KO/VI, sự kiện | Golden set nhiều ngôn ngữ, metrics, reviewer decisions | Chất lượng từng ngôn ngữ được chấm |
| P6 — Reliability & release | Phim dài theo chunk, resume, Windows installer, offline audit | Báo cáo performance/security/license/UAT | Đạt Go/No-Go trước khi phân phối |

**Nguyên tắc ưu tiên:** P0/P1 **phải chứng minh chất lượng kịch bản review Việt** trước P3/P4. P3 có trọng số cao hơn giao diện đẹp; GUI hoàn chỉnh nhưng không có MP4 review thật và scorecard reviewer không được coi là MVP. P4 GUI chỉ có thể đạt PRODUCT_E2E_OK khi P3 đã đủ bằng chứng.

## 14. KẾ HOẠCH THỬ NGHIỆM NHANH (SPIKE) THEO THỨ TỰ

### Spike A — Video ingest, detection và render
- Dữ liệu: một video ngắn có hình, thoại và phụ đề thuộc quyền dự án.
- Thực hiện: ffprobe, lấy scene cuts qua PySceneDetect, trim 2–3 đoạn bằng FFmpeg, ghép một WAV mẫu hợp lệ.
- Pass: MP4 mở được trên Windows; có một video stream và một audio stream; duration và in/out khớp dự kiến.

### Spike B — Nhận tiếng Anh và dịch Việt
- ASR Faster-Whisper giữ transcript tiếng Anh, timecode, language code.
- So sánh Argos Translate với LLM local trên một tập câu có bản tham chiếu Việt.
- Pass: không đảo nghĩa phủ định/tên người trong mẫu khó; kết quả gắn đúng span nguồn; người đánh giá có thể sửa.

### Spike C — Viết review thật + rubric + Claim Audit độc lập
- Input: **golden set P0** (video EN 3–10 phút có quyền, transcript/translation/events chuẩn, 03 bài review Việt viết tay, 10–15 câu hỏi diễn biến có source/timecode); reviewer đã được chỉ định.
- Local LLM viết outline rồi review tiếng Việt theo section, có bình luận mới. Đo lặp/nhầm tên/sai logic và chấm 5 tiêu chí × 0–2; ngưỡng **≥8/10, không điểm 0** chỉ là đề xuất phải hiệu chuẩn.
- Kiểm tra trùng **4-gram theo từ** với **bản dịch Việt transcript** (nếu nguồn là EN), chuẩn hóa Unicode/tokenizer và loại quote được chú thích; mốc **≤5% chỉ là giả thuyết thử**, phải đo cả 03 review mẫu người viết trước khi quyết định ngưỡng.
- Bước Claim Audit **không-LLM trước**: exact/contains cho câu trích, semantic similarity để tìm nguồn chứ không tự SUPPORTED, no support → INSUFFICIENT_EVIDENCE, contradiction → human review. Test phản chứng tên nhân vật/đảo phủ định.
- Pass chỉ khi reviewer có tên chấm rubric và mô tả lỗi; critical claims trái nguồn không được phép qua; lưu script revision/hash và review sheet. Fail → cải thiện StoryPack/sectioning hoặc giới hạn review 3–5 phút, không tô đẹp kết quả.

### Spike D — Voice Việt và timeline
- VieNeu sinh audio thật từ script đã duyệt.
- Timeline planner chọn clip có quyền và FFmpeg mix audio Việt với hình.
- Pass: người nghe nhận ra nội dung tiếng Việt trong MP4, tiếng đọc không bị mất, mốc phụ đề hợp lý.

### Spike E — Offline/resource
- Chặn Internet và DNS khi chạy pipeline đã chuẩn bị đầy đủ.
- Quan sát RAM/VRAM, bộ nhớ đệm, thử thiếu file model, GPU OOM giả lập/được kiểm soát và crash recovery.
- Pass: không outbound; thiếu model thì lỗi có hướng xử lý; output cũ không bị hỏng.

**Không làm:** tự động clone, tự tải trọng số, thay driver, sửa cài đặt hệ thống hoặc chạy trên phim chưa có quyền chỉ để hoàn tất spike.

## 15. KIỂM THỬ VÀ NGHIỆM THU

### 15.1. Ba lớp nghiệm thu tách biệt
1. **ENGINE_OK:** một engine độc lập nhận input thật và xuất kết quả có cấu trúc.
2. **PRODUCT_E2E_OK:** từ video nguồn đến MP4 review tiếng Việt qua GUI, có timeline và âm thanh thực.
3. **RELEASE_READY:** đã đạt thêm chất lượng biên tập, offline, license, an toàn, đóng gói và kiểm thử người dùng.

Không dùng số bài pytest PASS hay một file demo đơn lẻ để thay thế ba cột mốc này.

### 15.2. Tiêu chí bắt buộc cho MVP đầu
- Nguồn là video truyện ngắn tiếng Anh 3–10 phút, có quyền phân tích và tái sử dụng.
- ASR trả transcript **tiếng Anh** có timecode; dịch nghĩa Việt là artifact riêng.
- Có danh sách sự kiện phù hợp nguồn và kịch bản review **tiếng Việt mới** được người biên tập sửa/duyệt.
- VieNeu sinh file audio tiếng Việt nghe được.
- Output dùng các đoạn hình thực tế được chọn theo timeline; không thay toàn bộ bằng ảnh tĩnh/lavfi giả lập.
- FFmpeg xuất MP4 chứa H.264 + AAC; ffprobe kiểm tra đọc lại metadata.
- Người kiểm tra **xem và nghe MP4**, đối chiếu ít nhất các critical claims với video nguồn.
- Các file đầu ra có checksum và provenance; offline run không gọi Internet.
- Lỗi quyền, lỗi nguồn hoặc không có bằng chứng phải chặn hoặc yêu cầu xem xét, không tự bỏ qua.

### 15.3. Bộ test phải có
- Unit: source bounds, schema, translation IDs, timeline timing, approvals, invalidation.
- Contract: adapter output/version, file path Unicode, crash/timeout, model missing.
- Integration: worker + SQLite + artifact hashing + ffprobe/FFmpeg.
- E2E: quy trình GUI một video, export phát lại được, reopen/resume/cancel.
- Quality: ASR lỗi nhạc nền/im lặng; dịch phủ định và tên riêng; VLM nhầm nhân vật; voice khó nghe; lựa cảnh sai; phim phi tuyến.
- Security: tên file độc hại, subtitle prompt injection, model tự cố tải mạng, paths/junctions, rights.
- Resource: GPU 8 GB không tràn trong preset cơ bản; đo peak VRAM/RAM/disk và latency.
- License: thu thập danh sách dependency, model, voice, asset, build flags trước đóng gói.

Ngưỡng CER/WER, dịch, độ phù hợp cảnh, thời gian xử lý, VRAM và chất lượng viết review sẽ **hiệu chuẩn sau baseline P1**; ghi cả ngưỡng đề xuất, bằng chứng đo, người quyết định và version của rubric. Không tự công bố các con số chưa đo như tiêu chí đã đạt.

### 15.4. Rubric định lượng chất lượng kịch bản review Việt — PHẢI hiệu chuẩn ở P1

**Bản rubric ứng viên**, người đánh giá độc lập chấm 5 tiêu chí, mỗi tiêu chí 0–2. 0 = lỗi nghiêm trọng/không đạt, 1 = đạt một phần, 2 = đạt tốt và có ví dụ dẫn chứng. **Mốc tổng ≥8/10 và không tiêu chí nào bằng 0 chỉ là đề xuất để thử**, chưa đủ dữ liệu chứng minh dùng cho phim khác.

| Tiêu chí | Điểm 0 | Điểm 1 | Điểm 2 |
|---|---|---|---|
| Cốt truyện và nhân quả | Bịa/đảo critical event; sai kết cục | Đúng phần lớn nhưng còn lỗi quan trọng | Đúng sự kiện trọng yếu, nhân quả, thứ tự kể và uncertainty |
| Bình luận có giá trị | Chỉ đọc lại transcript | Có nhận xét nhưng rời rạc | Review có góc nhìn/phân tích, tách fact khỏi opinion |
| Nguyên bản (khác dubbing) | Chép/dịch nguyên thoại thành kịch bản | Có viết lại nhưng nhiều đoạn vay mượn | Tổ chức lời dẫn mới, quote giới hạn và có mục đích |
| Nhân vật và thuật ngữ | Nhầm tên/quan hệ/đại từ xuyên suốt | Lỗi nhỏ sửa được | Nhất quán nhân vật, thuật ngữ, chronology |
| Tự nhiên khi thuyết minh Việt | Khó đọc, máy móc, thiếu nhịp | Hiểu được, còn cứng | Đọc trôi chảy, chuyển đoạn và pacing phù hợp |

**Hard gate:** critical claim bị phản chứng, dùng footage thiếu quyền, không có script từ nguồn thật hoặc không có reviewer approval đều FAIL/BLOCKED bất kể tổng điểm rubric.

### 15.5. Originality 4-gram và giới hạn đo lường

- Tỷ lệ overlap 4-gram theo từ = (số vị trí 4-gram trong **review script Việt** đồng thời xuất hiện trong **bản nghĩa Việt của transcript** / tổng số vị trí 4-gram trong review script), với cách tách từ/tokenizer, dấu câu, Unicode NFC và phiên bản tiền xử lý được ghim; nếu nguồn phim tiếng Việt thì so với transcript gốc Việt.
- Trước khi dùng ngưỡng, phải thử cách tính **trên 03 review mẫu người viết** để đo false positives, đặc biệt tên nhân vật, ngữ cảnh thường gặp và quote ngắn. Ghi số đoạn trùng dài và quote hợp lệ thay vì chỉ dùng một tỷ lệ.
- **≤5% là ngưỡng khảo sát ban đầu do bản đánh giá đề xuất, KHÔNG phải chỉ tiêu cứng đã phê duyệt.** Trùng 4-gram thấp không chứng minh bài review có bình luận mới hoặc không vi phạm bản quyền; đánh giá R2/R3 và human review vẫn bắt buộc.

### 15.6. Golden set, reviewer và tài liệu kiểm chứng chất lượng

| Deliverable **trước P1** | Ai chịu trách nhiệm | Bằng chứng |
|---|---|---|
| 01 video EN có lời thoại dài 3–10 phút **được phép phân tích + tái sử dụng** | Rights owner — TBD | Source SHA, quyền footage/audio, định danh file |
| Transcript EN có timestamp + bản nghĩa VI người hiệu đính | Dataset owner — TBD | Span IDs/timebase/source SHA |
| StoryEvent/nhân vật có nguồn/chronology | Editorial owner — TBD | Evidence IDs, clip mốc nguồn |
| **03 kịch bản review Việt viết tay**, giọng văn được định nghĩa | Editorial owner — TBD | File mẫu, reviewer note, spoiler profile |
| **10–15 câu hỏi/đáp án về diễn biến phim** | QA owner — TBD | Answer + source timestamp + critical flag |
| **Reviewer được chỉ định, lịch chấm, rubric ký** | Product owner — TBD | Reviewer ID, rubric version, lịch review |
| License inventory + phương án nội bộ/open-source/thương mại | Legal owner — TBD | Quyết định phát hành, danh mục license |

Thiếu video có quyền/reviewer/ba review mẫu thì **không được tuyên bố P0 READY/P1 QUALITY PASS**; chỉ được chạy các thử nghiệm synthetic kỹ thuật tách biệt. Reviewer không được lấy điểm tự chấm của LLM làm kết quả chính thức.

### 15.7. Điều kiện Go/No-Go theo thực chất

- **P0 GO:** owner quyết định hình thức phát hành, chỉ định người duyệt và lịch; đủ quyền phim mẫu; golden set và rubric đầu vào được chuẩn bị.
- **P1 GO:** engine kỹ thuật chạy offline trên máy thật; Review Script Generator được chấm độc lập, thresholds được hiệu chuẩn từ golden set, review tiếng Việt đạt mức đã duyệt. Nếu không đạt, chỉ được thử lại trong budget hoặc đề xuất giảm phạm vi review ngắn 3–5 phút, guided/human correction; không cam kết P3/P4.
- **P3 GO:** từ video EN có quyền ra MP4 có lời kể Việt và kịch bản AI viết thật, **đạt ngưỡng rubric P1**, không critical claim unsupported/contradicted, đủ voice/rights/ffprobe/human listening.
- **P4 GO:** cùng pipeline thực chạy trọn qua GUI, không dùng nút giả/CLI thủ công; P3 phải đã đạt.
- **Release GO:** bản Windows cài sạch, license/voice/weights/FFmpeg/PySide6 đã kiểm, offline audit, security/QC/recovery được reviewer và owner chấp thuận.

## 16. QUẢN LÝ NGUỒN, QUYỀN SỬ DỤNG VÀ BẢO MẬT

- Mỗi video nguồn có khai báo **quyền phân tích** và **quyền tái sử dụng clip khi xuất thành phẩm** tách biệt.
- Quyền đối với nhạc, tiếng gốc, hình ảnh, font, voice, model weights và metadata cũng được kiểm tra.
- Không giải mã DRM trái phép; không tự tuyên bố video review chắc chắn được miễn trừ bản quyền.
- Khi lỗi quyền, loại clip khỏi candidate list trước khâu dựng, không để điểm AI vượt qua quyền.
- Không upload video/transcript; không AI Cloud fallback; không tự cập nhật hoặc model download trong sản xuất.
- Mọi FFmpeg command được tạo từ tham số đã chuẩn hóa, tránh nội suy shell từ văn bản AI.
- Runtime model tách khỏi thư viện UI; mỗi model/codec có digest, license, version và nguồn cung cấp.
- Dữ liệu private và câu thoại đầy đủ không ghi vào log mặc định; khi xuất bug report phải có redaction.
- File được ghi staging rồi kiểm tra và đổi tên atomically; không xóa/ghi đè output đã được người duyệt.
- Có cơ chế backup project và phục hồi sau crash. Bản backup phải nằm ngoài ổ nguồn nếu cần chống mất ổ.

## 17. RỦI RO HÀNG ĐẦU

| Rủi ro | Mức | Biện pháp |
|---|---|---|
| Engine riêng lẻ chạy tốt nhưng ghép không tương thích | Cao | Adapter contracts + P1/P3 integration sớm |
| LLM bịa nội dung review / **8B viết dài bị lặp, đổi nhân vật, sai logic** | **Rất cao** | P1 golden review + 5 tiêu chí rubric, StoryPack theo section, claim audit, thử model nhỏ/trung bình theo budget; không đạt thì đề xuất review ngắn 3–5 phút + reviewer |
| Model VLM không hiểu chuỗi hành động/phim dài | Rất cao | **Không bắt buộc VLM ở P3**: scene detect + thoại + vài keyframe, người dùng duyệt events; VLM chỉ mở sau spike; phim dài map–reduce có source PTS |
| Dịch thiếu nghĩa, tên riêng sai | Cao | Corpus đối chiếu, correction UI, reviewer |
| GPU 8 GB không đủ VLM + ASR đồng thời | Cao | One-heavy-model scheduling; CPU fallback |
| **Không có video EN mẫu có quyền** | **Cao — chặn P0/P1 quality** | P0 phải giao 1 source 3–10 phút, SHA, quyền analysis và reuse; không dùng phim ngoài chưa được phép |
| **Thiếu reviewer hoặc 03 bài review gold** | **Cao — chặn P0/P1 quality** | Chốt reviewer, lịch chấm, 3 phong cách review và 10–15 câu hỏi từ P0; reviewer sign-off |
| **ClaimAudit dùng LLM tự xác nhận** | **Rất cao** | Exact/contains, semantic retrieval và rule checks không-LLM; không đủ evidence tự INSUFFICIENT_EVIDENCE; reviewer quyết định |
| File video có bản quyền không hợp lệ | Rất cao | Rights intake và clip-filter |
| Giấy phép mô hình/code cản đóng gói | Cao | License audit trước download/integration |
| GUI trông xong nhưng pipeline vẫn giả lập | Rất cao | Product E2E bắt buộc ở P3 |
| Windows path Unicode, codec, audio sync sai | Cao | Test thực bằng FFprobe/FFmpeg, Windows E2E |
| Mất điện/crash khi render | Cao | State checkpoint, temp outputs, hashes |
| API/model tự kết nối Internet | Cao | Firewall + process audit trong offline test |

## 18. CÔNG VIỆC TỰ PHÁT TRIỂN KHÔNG THỂ BỎ QUA

Dù sử dụng hầu hết engine từ GitHub, ứng dụng vẫn cần tự viết:
- Giao diện và luồng người dùng.
- Lõi lập lịch/điều phối, dự phòng resource, tracking tiến trình, retry.
- Các adapter chuyển đổi kiểu dữ liệu và phiên bản.
- Dữ liệu sự kiện, nhân vật, bằng chứng, claim và script revision.
- Logic tổng hợp câu chuyện, chọn/đổi clip, ràng buộc timeline + quyền.
- Kiểm thử chất lượng tiếng Việt, người duyệt và ghi nhận quyết định.
- Kiểm thử tích hợp, đóng gói, support diagnostics và xử lý lỗi.

Điều này là chi phí kỹ thuật thiết yếu của việc “ghép nhiều dự án” và chính là phần tạo khác biệt cho sản phẩm, không thể thay hoàn toàn bằng các nút trên GUI.

## 19. DANH SÁCH QUYẾT ĐỊNH CẦN CHỐT SAU BÁO CÁO

1. Xác nhận tên sản phẩm và giao diện PySide6 hay một framework GUI khác.
2. Xác nhận offline tuyệt đối ngay cả khi người dùng có Internet.
3. **BẮT BUỘC trước P0:** Xác định loại sản phẩm phát hành: nội bộ, mở mã hay bán thương mại. Quyết định này làm cơ sở license/packaging Qt/PySide6, FFmpeg, weights, voice.
4. **P0:** Chốt 01 video EN 3–10 phút có quyền (analysis + reuse), source SHA, owner quyền, reviewer và lịch chấm.
5. **P0:** Chốt 03 review Việt mẫu do người viết, transcript EN/VI, 10–15 câu hỏi đáp án, rubric người chấm; chọn giọng VI có quyền.
6. Quyết định mức can thiệp mặc định: guided người duyệt hay tự động từng bước có chặn.
7. Chốt thứ tự ngôn ngữ sau video tiếng Anh và mức ưu tiên phim dài.
8. Sau các spike, pin model cụ thể, bộ runtime và kích thước cache theo benchmark thực.

**Ma trận owner/deadline cần ký tại P0:** Product owner = TBD; Rights owner = TBD; Dataset/Editorial owner = TBD; QA reviewer = TBD; Tech/License owner = TBD. Deadline từng mục = TBD (bắt buộc ấn định **trước khi bắt đầu P1**). Chưa có chữ ký/role cụ thể thì trạng thái OPEN/BLOCKED, không suy diễn là đã được cung cấp.

**Tài liệu hiện tại chưa phải phê duyệt để tải/cài ứng viên, sử dụng phim ngoài hoặc phát hành phần mềm.**

## 20. HÀNH ĐỘNG KẾ TIẾP ĐƯỢC ĐỀ NGHỊ

1. Dùng **chính tài liệu duy nhất này** làm nguồn yêu cầu mới cho dự án Movie Review Studio; ưu tiên thực thi điều kiện chất lượng tại 2.4/15.4–15.7/22.
2. Rà soát quyết định Mục 19 (đặc biệt hình thức phát hành **trước P0**), chuẩn bị video mẫu có quyền + reviewer + 03 review mẫu + 10–15 golden QA; chốt phạm vi MVP bằng demo EN→VI MP4.
3. Tạo mới repository sau khi được phép; không import dự án có sẵn để giả định là “greenfield”.
4. Lập scoreboard cho từng OSS candidate: Windows, offline, API, resources, quality, maintenance, license, integration effort.
5. Chọn engine cơ sở cho P1; spike FFmpeg/PySceneDetect → Faster-Whisper (CUDA12/cuDNN9 runtime test) → translator → LLM **review Việt + rubric** → non-LLM ClaimAudit → VieNeu v3 Turbo. VLM optional, có thể hoãn sau P3.
6. Chỉ sau spike pass mới triển khai domain contracts, controller, GUI và pipeline end-to-end.
7. Giữ lại log/benchmark của **dự án mới** để ra quyết định có bằng chứng; không sử dụng số liệu của bất kỳ dự án khác như số đo của dự án này.

## 21. NGUỒN THAM CHIẾU CÔNG KHAI ĐỂ KIỂM TRA KHI TRIỂN KHAI

Ngày tra cứu: **07/11/2026**. Các đường dẫn là nguồn upstream/giới thiệu, không đồng nghĩa approval hoặc sự bảo đảm chất lượng.

- faster-whisper — https://github.com/SYSTRAN/faster-whisper
- PySceneDetect — https://github.com/Breakthrough/PySceneDetect
- PaddleOCR — https://github.com/PaddlePaddle/PaddleOCR
- Argos Translate — https://github.com/argosopentech/argos-translate
- llama.cpp — https://github.com/ggml-org/llama.cpp
- Qwen3-VL — https://github.com/QwenLM/Qwen3-VL
- VieNeu-TTS — https://github.com/pnnbao97/VieNeu-TTS
- OpenTimelineIO — https://github.com/AcademySoftwareFoundation/OpenTimelineIO
- Qt for Python / PySide6 — https://pypi.org/project/PySide6/
- FFmpeg legal — https://ffmpeg.org/legal.html
- pyVideoTrans (chỉ tham khảo, GPL-3.0) — https://github.com/jianchang512/pyvideotrans

---
