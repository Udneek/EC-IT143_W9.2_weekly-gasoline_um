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

        SELECT YEAR(`Fiscal_Week_Start_Date`) AS `Year`, AVG(`Difference_From_Same Week_Last_Year`) AS `Avg Weekly Difference`
        FROM weekly_gasoline
        WHERE YEAR(`Fiscal_Week_Start_Date`) >= YEAR(NOW()) - 5
        GROUP BY `Year`
        ORDER BY `Year` ASC;

--- Create an Ad Hoc SQL query

CREATE TEMPORARY TABLE tmp_gasoline_yearly_avg (
  `Year` INT,
  `Avg_Weekly_Difference` FLOAT
);

            INSERT INTO tmp_gasoline_yearly_avg (`Year`, `Avg_Weekly_Difference`)
            SELECT YEAR(`Fiscal_Week_Start_Date`) AS `Year`, AVG(`Difference_From_Same Week_Last_Year`) AS `Avg Weekly Difference`
            FROM weekly_gasoline
            WHERE YEAR(`Fiscal_Week_Start_Date`) >= YEAR(NOW()) - 5
            GROUP BY `Year`
            ORDER BY `Year` ASC;


 --- Turn the Ad Hoc SQL query into a View

            CREATE VIEW v_gasoline_yearly_avg AS
            SELECT * FROM tmp_gasoline_yearly_avg;


--- Turn the View into a Table

            CREATE TABLE gasoline_yearly_avg (
              `Year` INT PRIMARY KEY,
              `Avg_Weekly_Difference` FLOAT NOT NULL
            );


--- Load the Table from the View using an Ad Hoc SQL script

            INSERT INTO gasoline_yearly_avg (`Year`, `Avg_Weekly_Difference`)
            SELECT * FROM v_gasoline_yearly_avg;


--- Turn the Ad Hoc SQL script in a Stored Procedure

          CREATE PROCEDURE pr_gasoline_yearly_avg()
          BEGIN
            CREATE TEMPORARY TABLE tmp_gasoline_yearly_avg (
              `Year` INT,
              `Avg_Weekly_Difference` FLOAT
            );

            INSERT INTO tmp_gasoline_yearly_avg (`Year`, `Avg_Weekly_Difference`)
            SELECT YEAR(`Fiscal_Week_Start_Date`) AS `Year`, AVG(`Difference_From_Same Week_Last_Year`) AS `Avg Weekly Difference`
            FROM weekly_gasoline
            WHERE YEAR(`Fiscal_Week_Start_Date`) >= YEAR(NOW()) - 5
            GROUP BY `Year`
            ORDER BY `Year` ASC;

            INSERT INTO gasoline_yearly_avg (`Year`, `Avg_Weekly_Difference`)
            SELECT * FROM tmp_gasoline_yearly_avg;

            DROP TEMPORARY TABLE IF EXISTS tmp_gasoline_yearly_avg;
          END;


--- Call the Stored Procedure

        CALL pr_gasoline_yearly_avg();



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

        SELECT Fiscal_Year, Fiscal_Week, `Current_Year_Production`, AVG(`Current_Year_Production`) OVER () AS `Avg Weekly Production`,
               (`Current_Year_Production` - AVG(`Current_Year_Production`) OVER ()) AS `Weekly Deviation`
        FROM weekly_gasoline
        ORDER BY `Weekly Deviation` DESC
        LIMIT 10;

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

--- Load the Table from the View using an Ad Hoc SQL script

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

        SELECT Fiscal_Year, Fiscal_Week, `Current_Year_Cumulative_Production`, `Previous_Year_Cumulative_Production`,
               (`Current_Year_Cumulative_Production` - `Previous_Year_Cumulative_Production`) AS `Cumulative Increase`
        FROM weekly_gasoline
        ORDER BY `Cumulative Increase` DESC
        LIMIT 10;

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

--- Load the Table from the View using an Ad Hoc SQL script

        INSERT INTO gasoline_production_stats_table
        SELECT * FROM gasoline_production_stats;

        Step 7 - Turn the Ad Hoc SQL script in a Stored Procedure

        CREATE PROCEDURE get_gasoline_production_stats()
        BEGIN
          SELECT * FROM gasoline_production_stats_table;
        END;

--- Call the Stored Procedure

         CALL get_gasoline_production_stats();
