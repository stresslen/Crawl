# Sophie - AI Shopping Assistant

Hệ thống chatbot AI hỗ trợ tìm kiếm và so sánh giá sản phẩm từ nhiều sàn thương mại điện tử.

## Mô tả hệ thống

### Chatbot (Sophie)
- **Trợ lý AI thông minh** sử dụng LangChain + OpenAI
- **Phân loại ý định**: Tự động nhận diện chat thông thường hoặc yêu cầu so sánh giá
- **Tìm kiếm Vector Database**: Sử dụng ChromaDB với OpenAI Embeddings
- **Tự động crawl**: Nếu không tìm thấy trong database, tự động thu thập dữ liệu mới
- **So sánh giá thông minh**: Phân tích và đề xuất sản phẩm tốt nhất

### Hệ thống Crawl
Thu thập dữ liệu sản phẩm từ 4 nền tảng:
- **Tiki** (API-based, nhanh nhất)
- **Lazada** (Selenium-based)
- **Cellphones** (Playwright-based)
- **DienThoaiVui** (Playwright-based)

**Đặc điểm:**
- Crawl song song nhiều platforms
- Tự động lưu vào SQL database và Vector database
- Hỗ trợ giới hạn số lượng sản phẩm
- Xuất kết quả JSON

## Cài đặt

### 1. Cài đặt dependencies
```bash
pip install -r requirements.txt
```

### 2. Cài đặt Playwright (cho crawlers)
```bash
playwright install
```

### 3. Cấu hình môi trường
Tạo file `.env` với nội dung:
```env
OPENAI_API_KEY=your_openai_api_key_here
```

### 4. Khởi tạo Vector Database
```bash
python create_vector_database.py
```

## 💻 Cách chạy

### Chạy Chatbot CLI
```bash
python chatbot.py
```

### Chạy API Server
```bash
python main.py
```
Server khởi động tại: `http://localhost:8010`

### Chạy với Docker
```bash
docker-compose up -d
```

### Chạy Crawler độc lập
```bash
# Crawl tất cả platforms
python Crawl_Data/run_all_crawlers.py --query "iPhone 15" --limit 10

# Crawl platforms cụ thể
python Crawl_Data/run_all_crawlers.py --query "laptop" --platforms tiki cellphones --limit 5

# Lưu kết quả
python Crawl_Data/run_all_crawlers.py --query "Samsung" --output "results.json"
```

## 📋 Luồng hoạt động

1. **User nhập query** → Chatbot nhận câu hỏi
2. **Phân tích ý định** → AI phân loại: chat hoặc tìm sản phẩm
3. **Tìm kiếm Vector DB** → Tìm trong database hiện có
4. **Nếu không có** → Tự động crawl từ 4 platforms
5. **Lưu dữ liệu mới** → Cập nhật SQL DB + Vector DB
6. **So sánh giá** → AI phân tích và đưa ra đề xuất
7. **Trả kết quả** → Hiển thị cho người dùng

## Công nghệ sử dụng

- **LangChain** - Framework xây dựng chatbot AI
- **OpenAI GPT** - Mô hình ngôn ngữ
- **ChromaDB** - Vector database
- **FastAPI** - API framework
- **Selenium & Playwright** - Web crawling
- **SQLite** - Lưu trữ dữ liệu

## Cấu trúc chính

```
├── chatbot.py              # Chatbot CLI
├── main.py                 # FastAPI server
├── tool.py                 # LangChain chains & tools
├── create_vector_database.py   # Khởi tạo vector DB
├── Crawl_Data/
│   ├── run_all_crawlers.py     # Crawler tổng hợp
│   ├── crawl_tiki_product.py   # Tiki crawler
│   ├── lazada_crawler_complete.py  # Lazada crawler
│   ├── scrape_cellphones_playwright.py  # Cellphones crawler
│   └── scrape_dienthoaivui_playwright_search.py  # DienThoaiVui crawler
└── chroma_data/            # Vector database storage
```

## Cấu hình nâng cao

### Tùy chỉnh số lượng crawl
Sửa trong `chatbot.py`:
```python
all_products = crawl_all_platforms(product_name, limit=20)  # Mặc định: None
```

### Thay đổi model AI
Sửa trong `tool.py`:
```python
chat_model = ChatOpenAI(model="gpt-4", temperature=0)
```

## Lưu ý

- Cần API key OpenAI hợp lệ
- Crawlers có thể bị chặn nếu crawl quá nhiều
- Playwright cần cài đặt browsers: `playwright install`
- Nên giới hạn `--limit` để tránh crawl quá lâu
