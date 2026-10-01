---
title: "Event 1"
date: 2026-09-29
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---


# Bài thu hoạch sự kiện AWS – 29/09/2026

### Mục Đích Của Sự Kiện

- Giới thiệu các dịch vụ AWS đang được vận hành thực tế tại doanh nghiệp lớn ở Việt Nam
- Chia sẻ định hướng tương lai của AI tại Việt Nam và vai trò của AWS trong chuyển đổi số
- Phân tích khoảng cách giữa cách Business Users và IT/Engineering đang dùng AI
- Giới thiệu nền tảng Amazon Quick (Unified AI Agent Platform) và IDE thế hệ mới Kiro
- Thảo luận cách adopt Agentic AI mà vẫn giữ được kiểm soát (Monitor – Control – Standardize)

### Danh Sách Diễn Giả / Thành Phần Tham Dự

- Lãnh đạo cấp cao AWS khu vực Đông Nam Á
- General Manager AWS tại Malaysia — trình bày về tương lai ngành AI tại Việt Nam nói chung và AWS tại Việt Nam nói riêng
- Đại diện doanh nghiệp lớn tại Việt Nam, trong đó có Techcombank
- Đội ngũ chuyên gia AWS / G-ASIA PACIFIC hỗ trợ nội dung sản phẩm (Amazon Quick, Kiro)

### Nội Dung Nổi Bật

#### Bối cảnh AI trong doanh nghiệp lớn tại Việt Nam

Sự kiện nhấn mạnh rằng nhiều hệ thống quan trọng của doanh nghiệp Việt Nam đã và đang vận hành trên các dịch vụ AWS. Bên cạnh phần giới thiệu use case thực tế, General Manager AWS Malaysia tập trung vào cơ hội phát triển:

- Chuyển đổi số không chỉ là “lên cloud”, mà là thay đổi cách vận hành hệ thống và ra quyết định
- AI sẽ là lớp tăng tốc tiếp theo cho doanh nghiệp Việt Nam nếu được triển khai có kiểm soát
- AWS đóng vai trò hạ tầng + công cụ để doanh nghiệp vừa thử nghiệm nhanh, vừa bảo vệ dữ liệu nội bộ

#### The State of AI Adoption in Enterprises

Slide then chốt một thực tế: **nhân viên đã dùng AI mỗi ngày, nhưng lãnh đạo không nắm được họ dùng gì, dùng thế nào, và dữ liệu đi đâu**.

**Phía Business Users:**

- Khó khai thác dữ liệu nội bộ (CRM, hồ sơ khách hàng…)
- Tự động hóa kém, vẫn phụ thuộc copy-paste thủ công
- Rủi ro data governance — dữ liệu nhạy cảm có thể bị đưa vào công cụ không được phép
- Không có audit trail — không biết nhân viên đang hỏi AI điều gì

**Phía IT / Engineering:**

- Không kiểm soát được chi phí token / spend
- Không kiểm soát được việc dùng công cụ bên ngoài
- Khó đo ROI, khó giải trình ngân sách
- Quy trình chưa chuẩn hóa, thiếu chất lượng và oversight

Kết luận của phần này: doanh nghiệp đã có năng suất từ AI, nhưng **chưa được GOVERNED**. Hướng xử lý được nhấn mạnh là **Monitor – Control – Standardize**.

#### Adopt Agentic AI without losing control

Các diễn giả không khuyến khích “cấm AI”, mà khuyến khích đưa AI vào vận hành có khung kiểm soát:

- **Monitor:** theo dõi ai đang dùng công cụ nào, dữ liệu nào được đưa vào
- **Control:** phân quyền, guardrail, giới hạn nguồn dữ liệu và chi phí
- **Standardize:** thống nhất nền tảng, quy trình và chất lượng đầu ra giữa các phòng ban

Đây cũng là cầu nối sang phần sản phẩm: thay vì để nhân viên tự dùng nhiều công cụ rời rạc, doanh nghiệp cần một nền tảng thống nhất.

#### Amazon Quick — Unified AI Agent Platform

Amazon Quick được giới thiệu là nền tảng AI Agent thống nhất, thay thế nhiều công cụ rời rạc, tập trung vào 3 trụ cột:

- **Enterprise Intelligence:** kết nối dữ liệu On-Premises, Cloud và SaaS
- **Automation:** lập kế hoạch và thực thi workflow phức tạp
- **Security & Governance:** access control, guardrails, audit

Điểm then chốt: Quick giúp doanh nghiệp **dùng được dữ liệu nội bộ** mà vẫn **đo được, kiểm soát được và bảo mật được**.

#### Extending Quick’s Capabilities

Quick được định vị là nền tảng scale từ **no-code đến high-code**:

- **Custom Chat Agent:** trợ lý AI gắn kiến thức doanh nghiệp, dễ chia sẻ giữa các phòng ban
- **Quick Sight:** phân tích dữ liệu, dashboard tương tác, self-service analytics
- **Research:** phân tích chuyên sâu, báo cáo tổng hợp, xuất tài liệu chuyên nghiệp
- **Amazon Bedrock AgentCore:** xây dựng custom AI agent gắn sâu vào hệ thống enterprise, kiến trúc multi-agent kèm security & governance

#### Kiro — Next-Generation IDE for Engineering

Kiro được giới thiệu như IDE thế hệ mới cho engineering, định hướng **mạnh – linh hoạt – tối ưu chi phí**, với trải nghiệm gần với quy trình phát triển phần mềm quen thuộc (tương tự Visual Studio về vai trò IDE), đồng thời bổ sung lớp AI:

- Hỗ trợ nhiều bề mặt làm việc: **IDE, CLI, ACP**
- Luồng phát triển: **Docs → Build → Package → Deploy**
- **Diverse Models – Cost Optimized:** tự chọn model phù hợp độ phức tạp của task
- Các model được nêu trên slide gồm Claude Opus, GPT, DeepSeek, MiniMax, GLM và các biến thể tối ưu cho reasoning / code / chi phí

#### Vibe coding và Spec-driven

Phần giải thích hai cách tiếp cận khi dùng AI để viết phần mềm:

- **Vibe coding:** mô tả ý tưởng bằng ngôn ngữ tự nhiên, để AI sinh code nhanh — phù hợp prototype, khám phá, tăng tốc bước đầu
- **Spec-driven:** bắt đầu từ đặc tả rõ ràng (yêu cầu, ràng buộc, kiến trúc), rồi mới để AI implement — phù hợp hệ thống production, audit, bảo trì lâu dài

Thông điệp chính: vibe coding giúp đi nhanh, nhưng enterprise cần nghiêng về **spec-driven** nếu muốn giữ chất lượng và kiểm soát.

### Những Gì Học Được

#### Tư duy quản trị AI trong doanh nghiệp

- AI đã “len lỏi” vào công việc hàng ngày trước khi IT kịp chuẩn hóa
- Khoảng cách lớn nhất không phải “có AI hay không”, mà là **Business Users dùng AI một kiểu, IT lo một kiểu**
- Muốn scale AI thì phải govern: monitor, control, standardize — không phải cấm

#### Nền tảng và công cụ

- Amazon Quick giải bài toán “một nền tảng thống nhất” cho user nghiệp vụ + governance
- Bedrock AgentCore là hướng mở rộng khi cần gắn agent vào hệ thống sẵn có
- Kiro hướng đến đội engineering: vừa là IDE, vừa là lớp chọn model tối ưu chi phí

#### Cách làm phần mềm với AI

- Phân biệt được vibe coding (nhanh, exploratory) và spec-driven (chặt, kiểm soát được)
- Adopt agentic AI không có nghĩa giao hết quyền cho agent — vẫn cần guardrail, audit và ownership của con người

### Ứng Dụng Vào Công Việc / Học Tập

- Khi dùng AI cho bài lab / đồ án: ưu tiên viết spec ngắn trước (input, output, ràng buộc) rồi mới generate code
- Không đưa dữ liệu nhạy cảm vào công cụ AI bên ngoài khi chưa rõ chính sách
- Thử tiếp cận “một nguồn sự thật”: chọn một công cụ chính, ghi lại những gì đã hỏi AI (tạo thói quen audit trail)
- Theo dõi Kiro / Amazon Quick / Amazon Q trong các lab AWS tiếp theo để so sánh workflow IDE truyền thống với IDE có agent
- Áp dụng tư duy Monitor – Control – Standardize khi làm việc nhóm: thống nhất tool, thống nhất cách review code do AI sinh ra

### Trải nghiệm trong event

Tham dự sự kiện ngày **29/09/2026** giúp tôi thấy AI trong doanh nghiệp không còn là chủ đề “tương lai”, mà đang là vấn đề vận hành thật: nhân viên đã dùng, lãnh đạo chưa kiểm soát hết, IT thì lo chi phí – bảo mật – ROI.

#### Học từ lãnh đạo và doanh nghiệp thực tế
- Được nghe lãnh đạo AWS Đông Nam Á và General Manager AWS Malaysia nói về hướng phát triển AI tại Việt Nam
- Phần đóng góp từ doanh nghiệp lớn như Techcombank cho thấy AWS không chỉ là slide sản phẩm, mà đã nằm trong hệ thống đang chạy

#### Nội dung kỹ thuật dễ gắn với thực tế
- Slide “State of AI Adoption” nói đúng tình trạng shadow AI: tiện cho user, rủi ro cho data và chi phí
- Amazon Quick được trình bày như cách gom các nhu cầu chat, analytics, research và agent vào một nền tảng có governance
- Kiro cho thấy hướng IDE mới: nhiều model, tự chọn model theo task, gắn với vòng đời Docs – Build – Package – Deploy

#### Bài học rút ra
- Agentic AI chỉ bền nếu không mất kiểm soát
- Doanh nghiệp cần nền tảng thống nhất hơn là để mỗi người một tool
- Với người học / mới đi làm: nên tập spec-driven sớm, đừng chỉ vibe coding

#### Một số hình ảnh khi tham gia sự kiện

![Tham dự sự kiện AWS ngày 29/09/2026](event-29-09-photo.jpg)
