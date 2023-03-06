# EC-IT143_W9.2_weekly-gasoline_um

/*****************************************************************************************************************
        NAME:    EC-IT143_W9.2_weekly-gasoline_um
        PURPOSE: To create question frpm dataset and answer it---

        MODIFICATION LOG:
        Ver      Date        Author        Description
        -----   ----------   -----------   -------------------------------------------------------------------------------
        1.0     03/03/2023   Uduak Mbaba       1. Built this script for EC IT143


        RUNTIME: 
        Xm Xs

        NOTES: 
        This is where I talk about what this script is, why I built it, and other stuff...

  ******************************************************************************************************************/

-- Q1: What is the average weekly difference in gasoline production from the same week last year, and how has this trended over the past 5 years?
-- A1: Let's query the dataset for answer...

--- Create an Ad Hoc SQL query

        SELECT YEAR(`Fiscal_Week_Start_Date`) AS `Year`, AVG(`Difference_From_Same Week_Last_Year`) AS `Avg Weekly Difference`
        FROM weekly_gasoline
        WHERE YEAR(`Fiscal_Week_Start_Date`) >= YEAR(NOW()) - 5
        GROUP BY `Year`
        ORDER BY `Year` ASC;


 --- Turn the Ad Hoc SQL query into a View

            CREATE VIEW weekly_gasoline_avg_diff AS
        SELECT YEAR(`Fiscal_Week_Start_Date`) AS `Year`, AVG(`Difference_From_Same Week_Last_Year`) AS `Avg Weekly Difference`
        FROM weekly_gasoline
        WHERE YEAR(`Fiscal_Week_Start_Date`) >= YEAR(NOW()) - 5
        GROUP BY `Year`
        ORDER BY `Year` ASC;



--- Turn the View into a Table

            CREATE TABLE gasoline_yearly_avg (
              `Year` INT PRIMARY KEY,
              `Avg_Weekly_Difference` FLOAT NOT NULL
            );


--- Load the Table from the View

            INSERT INTO gasoline_yearly_avg (`Year`, `Avg_Weekly_Difference`)
            SELECT * FROM v_gasoline_yearly_avg;


--- Turn the Ad Hoc SQL script in a Stored Procedure

          CREATE PROCEDURE `sp_weekly_gasoline_avg_diff` ()
BEGIN
         SELECT *
         FROM weekly_gasoline_avg_diff_table;
END

--- Call the Stored Procedure

        CALL weekly_gasoline_yearly_avg();



/*****************************************************************************************************************
        NAME:    EC-IT143_W9.2_weekly-gasoline_um
        PURPOSE: To create question frpm dataset and answer it---

        MODIFICATION LOG:
        Ver      Date        Author        Description
        -----   ----------   -----------   -------------------------------------------------------------------------------
        1.0     03/03/2023   Uduak Mbaba       1. Built this script for EC IT143


        RUNTIME: 
        Xm Xs

        NOTES: 
        This is where I talk about what this script is, why I built it, and other stuff...

  ******************************************************************************************************************/

-- Q2: Which fiscal weeks have seen the largest deviations from the average weekly gasoline production, and what factors might account for these deviations?
-- A2: Let's query the dataset for answer...

--- Create an Ad Hoc SQL query

        SELECT Fiscal_Year, Fiscal_Week, `Current_Year_Cumulative_Production`, `Previous_Year_Cumulative_Production`,
               (`Current_Year_Cumulative_Production` - `Previous_Year_Cumulative_Production`) AS `Cumulative Increase`
        FROM weekly_gasoline
        ORDER BY `Cumulative Increase` DESC
        LIMIT 10;

--- Turn the Ad Hoc SQL query into a View

        CREATE VIEW top_10_weekly_gasoline AS
        SELECT Fiscal_Year, Fiscal_Week, `Current_Year_Cumulative_Production`, `Previous_Year_Cumulative_Production`,
               (`Current_Year_Cumulative_Production` - `Previous_Year_Cumulative_Production`) AS `Cumulative Increase`
        FROM weekly_gasoline
        ORDER BY `Cumulative Increase` DESC
        LIMIT 10;

--- Turn the View into a Table

        CREATE TABLE top_10_weekly_gasoline (
          Fiscal_Year INT NOT NULL,
          Fiscal_Week INT NOT NULL,
          Current_Year_Cumulative_Production DECIMAL(10,2) NOT NULL,
          Previous_Year_Cumulative_Production DECIMAL(10,2) NOT NULL,
          Cumulative_Increase DECIMAL(10,2) NOT NULL,
          CONSTRAINT pk_top_10_weekly_gasoline PRIMARY KEY (Fiscal_Year, Fiscal_Week)
        );

--- Load the Table from the View

          INSERT INTO top_10_weekly_gasoline
          SELECT *
          FROM top_10_weekly_gasoline_view;

--- Turn the Ad Hoc SQL script in a Stored Procedure

          CREATE PROCEDURE get_top_10_weekly_gasoline()
          BEGIN
            SELECT *
            FROM top_10_weekly_gasoline;
          END;


--- Call the Stored Procedure

          CALL get_top_10_weekly_gasoline();


        
   
   
/*****************************************************************************************************************
        NAME:    EC-IT143_W9.2_weekly-gasoline_um
        PURPOSE: To create question frpm dataset and answer it---

        MODIFICATION LOG:
        Ver      Date        Author        Description
        -----   ----------   -----------   -------------------------------------------------------------------------------
        1.0     03/03/2023   Uduak Mbaba       1. Built this script for EC IT143


        RUNTIME: 
        Xm Xs

        NOTES: 
        This is where I talk about what this script is, why I built it, and other stuff...

  ******************************************************************************************************************/

-- Q3: Which fiscal weeks have seen the largest increases in cumulative gasoline production compared to the previous year,
--and what factors might account for these increases?
-- A3: Let's query the dataset for answer..

---Create an Ad Hoc SQL query

        SELECT Fiscal_Year, Fiscal_Week, `Current_Year_Production`, AVG(`Current_Year_Production`) OVER () AS `Avg Weekly Production`,
               (`Current_Year_Production` - AVG(`Current_Year_Production`) OVER ()) AS `Weekly Deviation`
        FROM weekly_gasoline
        ORDER BY `Weekly Deviation` DESC
        LIMIT 10;

---Turn the Ad Hoc SQL query into a View

        CREATE VIEW gasoline_production_stats AS
        SELECT Fiscal_Year, Fiscal_Week, `Current_Year_Production`, AVG(`Current_Year_Production`) OVER () AS `Avg Weekly Production`,
                 (`Current_Year_Production` - AVG(`Current_Year_Production`) OVER ()) AS `Weekly Deviation`
        FROM weekly_gasoline;

--- Turn the View into a Table

        CREATE TABLE gasoline_production_stats_table (
          Fiscal_Year INT NOT NULL,
          Fiscal_Week INT NOT NULL,
          Current_Year_Production FLOAT NOT NULL,
          Avg_Weekly_Production FLOAT NOT NULL,
          Weekly_Deviation FLOAT NOT NULL,
          PRIMARY KEY (Fiscal_Year, Fiscal_Week)
        );

        INSERT INTO gasoline_production_stats_table
        SELECT Fiscal_Year, Fiscal_Week, `Current_Year_Production`, AVG(`Current_Year_Production`) OVER () AS `Avg Weekly Production`,
               (`Current_Year_Production` - AVG(`Current_Year_Production`) OVER ()) AS `Weekly Deviation`
        FROM weekly_gasoline;

        ALTER TABLE gasoline_production_stats_table
        ADD CONSTRAINT weekly_gasoline_pk PRIMARY KEY (Fiscal_Year, Fiscal_Week);

--- Load the Table from the View

        INSERT INTO gasoline_production_stats_table
        SELECT * FROM gasoline_production_stats;

        Step 7 - Turn the Ad Hoc SQL script in a Stored Procedure

        CREATE PROCEDURE get_gasoline_production_stats()
        BEGIN
          SELECT * FROM gasoline_production_stats_table;
        END;

--- Call the Stored Procedure

         CALL get_gasoline_production_stats();
         
         
         
         
  /*****************************************************************************************************************
        NAME:    EC-IT143_W9.2_weekly-gasoline_um
        PURPOSE: To create question frpm dataset and answer it---

        MODIFICATION LOG:
        Ver      Date        Author        Description
        -----   ----------   -----------   -------------------------------------------------------------------------------
        1.0     03/03/2023   Uduak Mbaba       1. Built this script for EC IT143


        RUNTIME: 
        Xm Xs

        NOTES: 
        This is where I talk about what this script is, why I built it, and other stuff...

  ******************************************************************************************************************/

-- Q4: Which fiscal week saw the largest difference in current year production compared to the previous year, and what factors may have contributed to this deviation?
-- A4: Let's query the dataset for answer.

---Create an Ad Hoc SQL query

        SELECT Fiscal_Year, Fiscal_Week, Difference_From_Same_Week_Last_Year
        FROM weekly_gasoline
        WHERE Difference_From_Same_Week_Last_Year = (SELECT MAX(Difference_From_Same_Week_Last_Year) FROM weekly_gasoline);


---Turn the Ad Hoc SQL query into a View

        CREATE VIEW largest_production_diff_week_view AS
        SELECT Fiscal_Year, Fiscal_Week, Difference_From_Same_Week_Last_Year
        FROM weekly_gasoline
        WHERE Difference_From_Same_Week_Last_Year = (SELECT MAX(Difference_From_Same_Week_Last_Year) FROM weekly_gasoline);


--- Turn the View into a Table

        CREATE TABLE largest_production_diff_week_table (
        id INT AUTO_INCREMENT PRIMARY KEY,
         Fiscal_Year INT NOT NULL,
         Fiscal_Week INT NOT NULL,
         Difference_From_Same_Week_Last_Year FLOAT NOT NULL
);


--- Load the Table from the View

       CREATE PROCEDURE load_largest_production_diff_week()
BEGIN
        INSERT INTO largest_production_diff_week_table (Fiscal_Year, Fiscal_Week, Difference_From_Same_Week_Last_Year)
        SELECT Fiscal_Year, Fiscal_Week, Difference_From_Same_Week_Last_Year
         FROM largest_production_diff_week_view;
END;



--- Call the Stored Procedure

         CALL load_largest_production_diff_week_table();
         
         
         
         
/*****************************************************************************************************************
        NAME:    EC-IT143_W9.2_weekly-gasoline_um
        PURPOSE: To create question frpm dataset and answer it---

        MODIFICATION LOG:
        Ver      Date        Author        Description
        -----   ----------   -----------   -------------------------------------------------------------------------------
        1.0     03/03/2023          1. Built this script for EC IT143


        RUNTIME: 
        Xm Xs

        NOTES: 
        This is where I talk about what this script is, why I built it, and other stuff...

  ******************************************************************************************************************/

-- Q5: How does the current year's cumulative production of gasoline compare to the previous year's cumulative production, and which fiscal weeks contributed the most to this difference?
-- A5: Let's query the dataset for answer..

---Create an Ad Hoc SQL query

        SELECT Fiscal_Year, Fiscal_Week, Current_Year_Cumulative_Production, Previous_Year_Cumulative_Production, 
                (Current_Year_Cumulative_Production - Previous_Year_Cumulative_Production) AS Cumulative_Production_Difference
        FROM weekly_gasoline
        ORDER BY Cumulative_Production_Difference DESC
        LIMIT 10;


---Turn the Ad Hoc SQL query into a View

        CREATE VIEW production_difference AS
        SELECT Fiscal_Year, Fiscal_Week, Current_Year_Cumulative_Production, Previous_Year_Cumulative_Production, 
                (Current_Year_Cumulative_Production - Previous_Year_Cumulative_Production) AS Cumulative_Production_Difference
        FROM weekly_gasoline;


--- Turn the View into a Table

       CREATE TABLE production_difference_table (
                Fiscal_Year INT NOT NULL,
                Fiscal_Week INT NOT NULL,
                Current_Year_Cumulative_Production DECIMAL(10,2) NOT NULL,
                Previous_Year_Cumulative_Production DECIMAL(10,2) NOT NULL,
                Cumulative_Production_Difference DECIMAL(10,2) NOT NULL,
                PRIMARY KEY (Fiscal_Year, Fiscal_Week)
);


--- Load the Table from the View 

        INSERT INTO production_difference_table (Fiscal_Year, Fiscal_Week, Current_Year_Cumulative_Production, 
                Previous_Year_Cumulative_Production, Cumulative_Production_Difference)
        SELECT Fiscal_Year, Fiscal_Week, Current_Year_Cumulative_Production, Previous_Year_Cumulative_Production, 
                (Current_Year_Cumulative_Production - Previous_Year_Cumulative_Production) AS Cumulative_Production_Difference
        FROM production_difference;


 ---Turn the Ad Hoc SQL script in a Stored Procedure

//DELIMITER 
        CREATE PROCEDURE get_production_difference()
BEGIN
        SELECT Fiscal_Year, Fiscal_Week, Current_Year_Cumulative_Production, Previous_Year_Cumulative_Production, 
                Cumulative_Production_Difference
        FROM production_difference_table
        ORDER BY Cumulative_Production_Difference DESC
        LIMIT 10;
END
//DELIMITER ;


--- Call the Stored Procedure

         CALL get_production_difference_table();
         
         
         

