---NBA ALL STAR DATASET

-- Drop cleaned table if it already exists
IF OBJECT_ID('dbo.NBA_ALL_TIME_STAR_CLEANED', 'U') IS NOT NULL
    DROP TABLE dbo.NBA_ALL_TIME_STAR_CLEANED;

-- Create cleaned and enriched version of NBA ALL TIME STAR
SELECT
    [Name],
    [Nationality],
    [Age],
    [Seasons Played],
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
    ) AS End_Year,
    [Average PPG],
    [Total Points],
    [Achievements],
    [Fame Seasons],
    [MVP Seasons],
    [Clubs],

    -- Binary Columns
    CASE 
        WHEN [MVP Seasons] IS NOT NULL AND [MVP Seasons] <> '' THEN 1 
        ELSE 0 
    END AS Has_MVP,

    CASE 
        WHEN [Achievements] LIKE '%NBA Champion%' THEN 1 
        ELSE 0 
    END AS Has_Championship,

    CASE 
        WHEN [Achievements] LIKE '%Olympic Gold%' THEN 1 
        ELSE 0 
    END AS Has_Olympic_Gold,

    -- Count Columns
    CASE 
        WHEN [Achievements] IS NOT NULL AND [Achievements] <> '' THEN 
            LEN([Achievements]) - LEN(REPLACE([Achievements], '×', '')) + 1
        ELSE 0 
    END AS Achievement_Count,

    CASE 
        WHEN [MVP Seasons] IS NOT NULL AND [MVP Seasons] <> '' THEN 
            LEN([MVP Seasons]) - LEN(REPLACE([MVP Seasons], ',', '')) + 1
        ELSE 0 
    END AS MVP_Season_Count,

    CASE 
        WHEN [Fame Seasons] IS NOT NULL AND [Fame Seasons] <> '' THEN 
            LEN([Fame Seasons]) - LEN(REPLACE([Fame Seasons], ',', '')) + 1
        ELSE 0 
    END AS Fame_Season_Count

INTO dbo.NBA_ALL_TIME_STAR_CLEANED
FROM [dbo].[NBA ALL TIME STAR]
WHERE 
    [Age] >= 0 
    AND [Seasons Played] >= 0 
    AND [Average PPG] >= 0 
    AND [Total Points] >= 0;
