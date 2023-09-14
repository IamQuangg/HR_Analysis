# 1. Bối Cảnh
Bạn vừa được tuyển dụng làm Chuyên viên Phân tích Dữ liệu cho công ty SMARTOSC, một công ty hoạt động trong lĩnh vực Thương mại điện tử.
Công việc chính của bạn sẽ làm về bên mảng nhân sự. Cấp trên của bạn muốn xem tình hình chung về nhân sự của công ty và nhận thấy gần đây 
tỉ lệ rời bỏ công việc ở trên thị trường lao động khá cao. Anh/cô ấy muốn bạn xem liệu công ty mình có đang trong tình trạng như vậy hay không. 
Hãy phân tích và báo cáo lại với cấp trên.
# 2. Phân tích
2.1. Phân tích tỷ lệ giới tính và độ tuổi.

      SELECT Gender, COUNT(*) AS Total_Count, 			
             Format((COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Employee)),2) AS Gender_Percentage			
      FROM Employee			
      GROUP BY Gender;
  ![image](https://github.com/IamQuangg/HR_Analysis/assets/128073066/1359c2f3-f1c9-42f8-addd-ff0060c2140a)

Ta có thể thấy tỷ lệ giới tính nam, nữ ở trong tổ chức khá là tương đồng nhau. Không có sự chênh lệch giới tính nào xảy ra ở đây. 			

2.2. Phân tích độ tuổi

      SELECT distinct Case 		
                 WHEN Age BETWEEN 18 AND 24 THEN '18-24'		
                 WHEN Age BETWEEN 25 AND 34 THEN '25-34'		
                 WHEN Age BETWEEN 35 AND 44 THEN '35-44'		
                 WHEN Age BETWEEN 45 AND 54 THEN '45-54'		
                 WHEN Age BETWEEN 55 AND 64 THEN '55-64'		
                 ELSE '65+' 		
             END AS Age_Group,		
      Count(*) Total_count,		
      Count(*) * 100 / (SELECT COUNT(*) FROM Employee) Age_perc		
      From employee		
      Group by		
      Age_group		
      Order by 		
      Total_count		
    
![image](https://github.com/IamQuangg/HR_Analysis/assets/128073066/08df1a85-b652-4fea-b96a-1d6293da9f28)

Tỷ lệ người lao động trẻ từ 18-34 chiếm 75% lao động của tổ chức. Với tỷ lệ lao động trẻ có thể mang lại nhiều lợi ích cho tổ chức như 
là xây dựng môi trường làm việc năng động và sáng tạo và tổ chức dễ dàng nắm bắt công nghệ mới nhanh chóng.

2.3. Tỷ lệ Attrition theo vị trí công việc

        SELECT 			
            JobRole,			
            COUNT(*) AS TotalEmployees,			
            SUM(CASE WHEN Attrition = 'Yes' THEN 1 ELSE 0 END) AS AttritionCount,			
            (SUM(CASE WHEN Attrition = 'Yes' THEN 1 ELSE 0 END) / COUNT(*)) * 100 AS AttritionRate			
        FROM Employee			
        GROUP BY JobRole;

  ![image](https://github.com/IamQuangg/HR_Analysis/assets/128073066/2d187358-42a2-4969-9240-0757a4cb8624)

Có thể thấy Sales Representative và Recruiter là 2 công việc có tỷ lệ rời bỏ cao nhất với 39.75% và 37.5%. Ta sẽ đi sâu hơn nữa để tìm ra 
lý do tại sao 2 công việc này có tỷ lệ rời bỏ cao như vậy.Ta sẽ xem việc phải làm OverTime có ảnh hưởng gì đến tỷ lệ rời bỏ công việc không.

        Select
         JobRole,
         count(*) TotalEmployee,
             SUM(CASE WHEN OverTime = 'Yes' THEN 1 ELSE 0 END) AS OvertimeCount,
            (SUM(CASE WHEN OverTime = 'Yes' THEN 1 ELSE 0 END) / COUNT(*)) * 100 AS OvertimeRate
        FROM Employee
        group by
         JobRole	

![image](https://github.com/IamQuangg/HR_Analysis/assets/128073066/4f2f7d29-8133-4cac-b199-0981e26825fe)

Từ dữ liệu trên ta thấy các công việc đều có tỉ lệ overtime ở mức trên 20%. Hai công việc Sales Representative và Recruiter không phải 2 công việc
có tỷ lệ làm overtime cao nhất. Do đó, việc làm overtime không phải là nguyên nhất chính dẫn đến tỷ lệ rời bỏ công việc. Hãy kiểm tra xem mức lương 
và tính toán điểm trung bình về mức độ hài lòng công việc, mức độ hài lòng môi trường,.. để thấy đâu là yếu tố dẫn đến tỷ lệ rời bỏ cao với 2 
công việc trên.

      SELECT 
          JobRole,
          AVG(JobSatisfaction) AS AvgJobSatisfaction,
          AVG(EnvironmentSatisfaction) AS AvgEnvironmentSatisfaction,
          AVG(RelationshipSatisfaction) AS AvgRelationshipSatisfaction,
          AVG(SelfRating) AS AvgSelfRating,
          AVG(ManagerRating) AS AvgManagerRating
      FROM employee
       Left join performancerating
        on employee.employeeID = performancerating.employeeID
      GROUP BY JobRole

![image](https://github.com/IamQuangg/HR_Analysis/assets/128073066/334a9f1e-d3d3-4876-9c4e-c664c598bd66)

Điểm trung bình  của Recruiter và Sales Presentatives đều ở trong khoảng 3.3-3.8. Tức là ở mức có thể gọi là hài lòng. Do đó môi trường và sự thỏa mãn trong 
công việc không phải là yếu tố chính dẫn đến tỷ lệ rời bỏ. Hãy cùng xem yếu tố lương có phải là nguyên nhân chính đẫn đến rời bỏ công việc.

        select
             JobRole,
             avg(salary) 
        from employee
        group by
             JobRole
        Order by
            avg(salary) 

![image](https://github.com/IamQuangg/HR_Analysis/assets/128073066/33949153-b086-4246-88bb-467ab51eb899)

Lương trung bình của Recruiter và Sales Representative thấp hơn so với các công việc khác. Đây có thể là lý do làm cho tỷ lệ rời bỏ công việc của 2 lĩnh vực này cao như vậy.


Xem biểu đồ tổng quan tại đây: [Phạm Vinh Quang.pdf](https://github.com/IamQuangg/HR_Analysis/files/12611141/Ph.m.Vinh.Quang.pdf)











	
				


