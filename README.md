# payflow_optimized.sql
-- HỆ THỐNG PAYFLOW (SCRIPT TỐI ƯU HÓA TRUY VẤN)
CREATE DATABASE IF NOT EXISTS payflow_db;
USE payflow_db;

-- 1. Tạo lại bảng Transactions (Mô phỏng 5 triệu dòng)
DROP TABLE IF EXISTS Transactions;
CREATE TABLE Transactions (
    transaction_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    amount DECIMAL(15,2),
    transaction_type VARCHAR(20), -- 'DEPOSIT', 'WITHDRAW', 'TRANSFER'
    created_at DATETIME
);

-- 2. Tạo Composite Index tối ưu cho bộ lọc (transaction_type, created_at)
CREATE INDEX idx_type_date ON Transactions(transaction_type, created_at);

-- ========================================================
-- BÁO CÁO ĐÃ TỐI ƯU HÓA (Thay thế hàm YEAR/MONTH bằng khoảng SARGable)
-- ========================================================

-- Sử dụng EXPLAIN để kiểm tra kế hoạch thực thi trước khi chạy thực tế
EXPLAIN 
SELECT SUM(amount) AS total_deposit
FROM Transactions
WHERE transaction_type = 'DEPOSIT' 
  AND created_at >= '2026-06-01 00:00:00' 
  AND created_at < '2026-07-01 00:00:00';
