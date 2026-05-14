# BÁO CÁO LAB - Day26 MCP và A2A Infrastructure

## 1. Thông Tin Chung

- Repo: `Day26-Track02-MCP_and_A2A_Infrastructure`
- Chủ đề: Xây dựng hệ thống multi-agent với MCP/A2A
- Mô hình sử dụng: `deepseek/deepseek-v4-flash:free`
- Công nghệ chính: LangGraph, LangChain, A2A SDK, FastAPI

## 2. Mục Tiêu

- Hiểu luồng phát triển từ LLM đơn lẻ đến distributed multi-agent.
- Hoàn thành các bài tập trong `exercises/`.
- Chạy được hệ thống A2A end-to-end với registry và các agent chuyên môn hóa.

## 3. Nội Dung Thực Hiện

### Stage 1: Direct LLM

- Đã đọc và hiểu cách `get_llm()` khởi tạo `ChatOpenAI`.
- Message gửi vào LLM gồm `SystemMessage` và `HumanMessage`.
- `SystemMessage` đặt vai trò và ràng buộc cho mô hình, `HumanMessage` chứa câu hỏi người dùng.
- Đã cập nhật câu hỏi demo sang tiếng Việt để phù hợp ngữ cảnh lab.

### Stage 2: LLM + RAG & Tools

- Đã bổ sung entry `labor_law` vào `LEGAL_KNOWLEDGE`.
- Đã tạo tool `check_statute_of_limitations(case_type)` để trả về thời hiệu khởi kiện.
- Đã thêm tool này vào danh sách `tools` và xử lý trong vòng gọi tool.

### Stage 3: Single Agent với ReAct

- Đã thêm tool `search_case_law(keywords)` để tra cứu án lệ mẫu.
- Đã đưa tool này vào `TOOLS`.
- Đã chỉnh câu hỏi demo để kích hoạt trường hợp breach of contract.
- `create_react_agent()` được dùng để agent tự quyết định gọi tool theo chu trình Think -> Act -> Observe.

### Stage 4: Multi-Agent In-Process

- Đã implement `privacy_agent`.
- Đã thêm logic routing theo từ khóa `data`, `privacy`, `gdpr`, `dữ liệu`.
- Đã thêm `privacy_analysis` vào phần tổng hợp kết quả cuối cùng.
- Đã thêm node và edge cần thiết trong `StateGraph`.

### Stage 5: Distributed A2A System

- Đã giữ nguyên kiến trúc registry + customer/law/tax/compliance agents.
- Đã cập nhật prompt của `tax_agent` để câu trả lời ngắn gọn hơn.
- Đã xác minh luồng request đi qua registry, customer agent, law agent, tax agent và quay về customer agent để tổng hợp.

## 4. Kết Quả Xác Minh

- `python -m py_compile common/llm.py stages/stage_1_direct_llm/main.py stages/stage_2_rag_tools/main.py stages/stage_3_single_agent/main.py stages/stage_4_milti_agent/main.py exercises/exercise_2_tools.py exercises/exercise_4_multiagent.py tax_agent/graph.py customer_agent/graph.py law_agent/graph.py compliance_agent/graph.py registry/__main__.py test_client.py`
  - Kết quả: thành công.
- `python -m pip install -e .`
  - Kết quả: thành công, cài được `a2a-sdk` và các dependency cần thiết.
- `bash start_all.sh`
  - Kết quả: registry và các agent đã khởi động, register thành công.
- `python test_client.py`
  - Kết quả: thành công, nhận được phản hồi cuối cùng từ hệ thống A2A end-to-end.

## 5. Ghi Nhận Quan Trọng

- Trong môi trường ban đầu, `uv` không có trong `PATH`, nên việc xác minh phải dùng trực tiếp `python`.
- Trước khi cài lại dependency, hệ thống A2A bị thiếu `a2a-sdk`; sau khi cài lại, toàn bộ service đã chạy được.
- Một số warning deprecation vẫn xuất hiện trong log, nhưng không làm hỏng luồng chạy của lab.

## 6. Kết Luận

- Lab đã được hoàn thành theo đúng luồng yêu cầu của repo.
- Các bài tập về tools, ReAct agent, privacy routing và distributed A2A đều đã được triển khai.
- Hệ thống đã được kiểm tra bằng cả compile step và chạy thực tế end-to-end.
