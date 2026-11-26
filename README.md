# BTL3 – Data-Driven Automation Testing & Non-Functional Testing (Moodle LMS)

Khung kiểm thử tự động dùng chung cho nhóm **A01.G03** (Python 3 + Selenium WebDriver + `unittest` + CSV + Simple POM).

## 1. Cấu trúc thư mục

```text
btl3/
├── common/                        # Core dùng chung (BaseTest, BasePage, CSV Helper, Logger, Config)
│   ├── config.py                  # URL, tài khoản, trình duyệt, timeout
│   ├── csv_helper.py              # Đọc CSV test data & CSV/JSON locator configs
│   ├── logger.py                  # Ghi log console + file .log + xuất báo cáo results_*.csv
│   ├── base_page.py               # Simple POM base class (Explicit Wait, login/logout, screenshot)
│   └── base_test.py               # BaseTest(unittest.TestCase) + execute_ddt_case()
│
├── setup_cleanup/                 # Tạo & dọn dữ liệu mẫu Moodle dùng chung
│   ├── group_setup.py             # Tạo 7 users mẫu, Cohort Test 01, khóa TC_COURSE_01, Group Test 01
│   └── group_cleanup.py           # Xóa sạch dữ liệu mẫu sau khi test
│
├── level1/                        # LEVEL 1: Data-Driven Testing (Input + Expected Result trong CSV)
│   ├── data/                      # File CSV của từng thành viên (data_*.csv)
│   ├── pages/                     # Page Object của từng feature (*_page.py)
│   ├── template_ddt_example.py    # Mẫu chuẩn để copy tạo test_*.py
│   └── test_<feature>.py          # File test của từng thành viên
│
├── level2/                        # LEVEL 2: Data + URL & UI Element Locator Driven
│   ├── configs/                   # Tách URL & Element Locators ra file CSV/JSON
│   │   ├── common_urls.csv
│   │   └── common_locators.csv
│   ├── data/                      # Dữ liệu test (có thể dùng chung hoặc copy từ Level 1)
│   ├── pages/                     # Page Object đọc locator qua LocatorRepository
│   └── locator_repository.py      # Bộ nạp URL và Locator động cho Level 2
│
├── non_functional/                # Kiểm thử phi chức năng (mỗi thành viên ít nhất 2 yêu cầu)
│   ├── compatibility/            
│   ├── performance/              
│   └── reliability/               
│
├── reports/                       # Tự động sinh: logs/, screenshots/, results_*.csv
├── requirements.txt
└── run_all_tests.py               # Script chạy toàn bộ hoặc từng bộ test
```

## 2. Cài đặt môi trường (1 lần duy nhất)

1. Đảm bảo đã cài **Python 3.10+** và **Docker Moodle** đang chạy tại `http://localhost:8080` (`admin` / `Admin@12345`).
2. Cài đặt thư viện:
   ```bash
   pip install -r requirements.txt
   ```
   *(Selenium 4 tự động tải ChromeDriver / EdgeDriver / GeckoDriver phù hợp, không cần tải thủ công).*

## 3. Chuẩn bị & Dọn dẹp dữ liệu mẫu

```bash
# Bước 1: Tạo dữ liệu mẫu trước khi chạy test
python -m setup_cleanup.group_setup

# Bước 2: Chạy test (xem mục 4)

# Bước 3: Dọn sạch dữ liệu mẫu sau khi xong (tùy chọn)
python -m setup_cleanup.group_cleanup
```

## 4. Quy ước đặt tên cho 4 thành viên (để làm song song không xung đột)

| Thành viên | Feature | File CSV Level 1 | File Page Level 1 | File Test Level 1 | File Config Level 2 | File Test Level 2 |
|---|---|---|---|---|---|---|
| **Bảo Trân** | 001 – Add User | `level1/data/data_add_user.csv` | `level1/pages/add_user_page.py` | `level1/test_add_user.py` | `level2/configs/add_user_locators.csv` | `level2/test_add_user_lv2.py` |
| **Như Quỳnh** | 002 – Create Course | `level1/data/data_create_course.csv` | `level1/pages/create_course_page.py` | `level1/test_create_course.py` | `level2/configs/create_course_locators.csv` | `level2/test_create_course_lv2.py` |
| **Ngọc Huyền** | 003 – Enrol User | `level1/data/data_enrol_user.csv` | `level1/pages/enrol_user_page.py` | `level1/test_enrol_user.py` | `level2/configs/enrol_user_locators.csv` | `level2/test_enrol_user_lv2.py` |
| **Bảo Ngọc** | 004 – Create Group | `level1/data/data_create_group.csv` | `level1/pages/create_group_page.py` | `level1/test_create_group.py` | `level2/configs/create_group_locators.csv` | `level2/test_create_group_lv2.py` |

## 5. Cách chạy kiểm thử

```bash
# Chạy toàn bộ Level 1
python run_all_tests.py --level 1

# Chạy riêng 1 feature
python run_all_tests.py --level 1 --pattern "test_enrol_user.py"

# Chạy Level 2
python run_all_tests.py --level 2

# Chạy trên trình duyệt khác (tiện cho Compatibility Testing)
python run_all_tests.py --level 1 --browser edge
python run_all_tests.py --level 1 --browser firefox --headless

# Đổi cổng Moodle nếu máy thành viên chạy cổng khác (vd: 9501)
python run_all_tests.py --level 1 --url http://127.0.0.1:9501
```

## 6. Kết quả đầu ra
Sau mỗi lần chạy, `ResultLogger` tự động sinh ra:
- `reports/results_<level>_<suite>.csv`: Bảng kết quả từng TC (`tc_id`, `tc_name`, `status`, `duration_sec`, `expected`, `actual`, `screenshot`, `note`).
- `reports/logs/<level>_<suite>_<timestamp>.log`: Nhật ký chạy chi tiết.
- `reports/screenshots/`: Ảnh chụp màn hình tự động khi có TC bị `FAIL`.
"# btl3-testing" 
