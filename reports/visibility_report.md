# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 27 skeleton, trung bình 15.85 khớp có v > 0 mỗi người
- Tổng: v=2 355 | v=1 73 | v=0 31

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 24 | 3 | 0 | 11% |
| 1 | left_eye | 23 | 4 | 0 | 15% |
| 2 | right_eye | 21 | 6 | 0 | 22% |
| 3 | left_ear | 22 | 5 | 0 | 19% |
| 4 | right_ear | 17 | 10 | 0 | 37% |
| 5 | left_shoulder | 27 | 0 | 0 | 0% |
| 6 | right_shoulder | 26 | 1 | 0 | 4% |
| 7 | left_elbow | 25 | 2 | 0 | 7% |
| 8 | right_elbow | 21 | 6 | 0 | 22% |
| 9 | left_wrist | 20 | 6 | 1 | 22% |
| 10 | right_wrist | 19 | 8 | 0 | 30% |
| 11 | left_hip | 26 | 1 | 0 | 4% |
| 12 | right_hip | 21 | 6 | 0 | 22% |
| 13 | left_knee | 18 | 2 | 7 | 7% |
| 14 | right_knee | 12 | 8 | 7 | 30% |
| 15 | left_ankle | 18 | 1 | 8 | 4% |
| 16 | right_ankle | 15 | 4 | 8 | 15% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
