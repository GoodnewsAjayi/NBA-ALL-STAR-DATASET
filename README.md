---NBA ALL STAR DATASET

SELECT 
    Name,
    [Years Active],
    TRY_CAST(LEFT([Years Active], 4) AS INT) AS Start_Year,
    TRY_CAST(
        CASE 
            WHEN [Years Active] LIKE '%Present' THEN '2025'
            WHEN CHARINDEX(',', [Years Active]) > 0 THEN 
                RIGHT(LTRIM(RIGHT([Years Active], CHARINDEX(',', REVERSE([Years Active]) + ',') - 1)), 4)
            ELSE 
                RIGHT([Years Active], 4)
        END AS INT
    ) AS End_Year
FROM [dbo].[NBA ALL TIME STAR];




SELECT 
    a.Name,
    a.Nationality,
    a.Clubs,
    a.Age,
    a.[Total Points],
    a.Achievements,
    TRY_CAST(LEFT(a.[Years Active], 4) AS INT) AS Start_Year,
    TRY_CAST(
        CASE 
            WHEN a.[Years Active] LIKE '%Present' THEN '2025'
            WHEN CHARINDEX(',', a.[Years Active]) > 0 THEN 
                RIGHT(LTRIM(RIGHT(a.[Years Active], CHARINDEX(',', REVERSE(a.[Years Active]) + ',') - 1)), 4)
            ELSE 
                RIGHT(a.[Years Active], 4)
        END AS INT
    ) AS End_Year
FROM [dbo].[NBA ALL TIME STAR] a;



IF OBJECT_ID('dbo.NBA_PLAYER_MVP_YEAR', 'U') IS NOT NULL
    DROP TABLE dbo.NBA_PLAYER_MVP_YEAR;

-- Use brackets to reference the table with spaces
SELECT 
    [Name],
    TRY_CAST(TRIM(value) AS INT) AS MVP_Year
INTO dbo.NBA_PLAYER_MVP_YEAR
FROM [dbo].[NBA ALL TIME STAR]
CROSS APPLY STRING_SPLIT([MVP Seasons], ',')
WHERE [MVP toSeasons] IS NOT NULL AND [MVP Seasons] <> '';
