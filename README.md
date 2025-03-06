# India Elections 2024 SQL Analysis (Microsoft SQL Server)

This repository contains SQL queries designed to analyze the results of the India Elections 2024. The queries provide insights into party performance, alliance dynamics (NDA and I.N.D.I.A), and candidate-specific statistics. These queries are designed specifically for **Microsoft SQL Server**.

## Data Sources

The queries rely on the following tables (schema details are assumed based on the queries):

*   `constituencywise_results`: Contains overall results for each constituency, including the winning candidate, party ID, total votes, and margin of victory.
*   `partywise_results`: Contains information about each party, including the number of seats won.  This table is modified by the included scripts to add a `party_alliance` column.
*   `statewise_results`: Links parliamentary constituencies to states.
*   `states`: Contains state information, such as State ID and State Name.
*   `constituencywise_details`: Contains detailed information about candidates in each constituency, including EVM votes and postal votes.

## Queries Overview

The SQL queries are categorized as follows, with descriptions taken directly from the provided file:

### 1. Basic Counts and Aggregations

*   **What are the total number of seats available for election in each state?**

    ```
    SELECT 
        s.State AS State_Name,
        COUNT(cr.Constituency_ID) AS Total_Seats_Available
    FROM 
        constituencywise_results cr
    JOIN 
        statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
    JOIN 
        states s ON sr.State_ID = s.State_ID
    GROUP BY 
        s.State
    ORDER BY 
        s.State;
    ```

*   **Total seats won by NDA Alliance**

    ```
    SELECT 
        SUM(CASE 
                WHEN party IN (
                    'Bharatiya Janata Party - BJP', 
                    'Telugu Desam - TDP', 
                    'Janata Dal  (United) - JD(U)',
                    'Shiv Sena - SHS', 
                    'AJSU Party - AJSUP', 
                    'Apna Dal (Soneylal) - ADAL', 
                    'Asom Gana Parishad - AGP',
                    'Hindustani Awam Morcha (Secular) - HAMS',
                    'Janasena Party - JnP', 
                    'Janata Dal  (Secular) - JD(S)',
                    'Lok Janshakti Party(Ram Vilas) - LJPRV', 
                    'Nationalist Congress Party - NCP',
                    'Rashtriya Lok Dal - RLD', 
                    'Sikkim Krantikari Morcha - SKM'
                ) THEN [Won]
                ELSE 0 
            END) AS NDA_Total_Seats_Won
    FROM 
        partywise_results
    ```

*   **Seats Won by NDA Allianz Parties**

    ```
    SELECT 
        party as Party_Name,
        won as Seats_Won
    FROM 
        partywise_results
    WHERE 
        party IN (
            'Bharatiya Janata Party - BJP', 
            'Telugu Desam - TDP', 
            'Janata Dal  (United) - JD(U)',
            'Shiv Sena - SHS', 
            'AJSU Party - AJSUP', 
            'Apna Dal (Soneylal) - ADAL', 
            'Asom Gana Parishad - AGP',
            'Hindustani Awam Morcha (Secular) - HAMS', 
            'Janasena Party - JnP', 
            'Janata Dal  (Secular) - JD(S)',
            'Lok Janshakti Party(Ram Vilas) - LJPRV', 
            'Nationalist Congress Party - NCP',
            'Rashtriya Lok Dal - RLD', 
            'Sikkim Krantikari Morcha - SKM'
        )
    ORDER BY Seats_Won DESC
    ```

*   **Total Seats Won by I.N.D.I.A. Allianz(Indian National Developmental Inclusive Alliance)**

    ```
    SELECT 
        SUM(CASE 
                WHEN party IN (
                    'Indian National Congress - INC',
                    'Aam Aadmi Party - AAAP',
                    'All India Trinamool Congress - AITC',
                    'Bharat Adivasi Party - BHRTADVSIP',
                    'Communist Party of India  (Marxist) - CPI(M)',
                    'Communist Party of India  (Marxist-Leninist)  (Liberation) - CPI(ML)(L)',
                    'Communist Party of India - CPI',
                    'Dravida Munnetra Kazhagam - DMK',
                    'Indian Union Muslim League - IUML',
                    'Nat`Jammu & Kashmir National Conference - JKN',
                    'Jharkhand Mukti Morcha - JMM',
                    'Jammu & Kashmir National Conference - JKN',
                    'Kerala Congress - KEC',
                    'Marumalarchi Dravida Munnetra Kazhagam - MDMK',
                    'Nationalist Congress Party Sharadchandra Pawar - NCPSP',
                    'Rashtriya Janata Dal - RJD',
                    'Rashtriya Loktantrik Party - RLTP',
                    'Revolutionary Socialist Party - RSP',
                    'Samajwadi Party - SP',
                    'Shiv Sena (Uddhav Balasaheb Thackrey) - SHSUBT',
                    'Viduthalai Chiruthaigal Katchi - VCK'
                ) THEN [Won]
                ELSE 0 
            END) AS INDIA_Total_Seats_Won
    FROM 
        partywise_results
    ```

*   **Seats Won by I.N.D.I.A. Allianz Parties**

    ```
    SELECT 
        party as Party_Name,
        won as Seats_Won
    FROM 
        partywise_results
    WHERE 
        party IN (
            'Indian National Congress - INC',
                    'Aam Aadmi Party - AAAP',
                    'All India Trinamool Congress - AITC',
                    'Bharat Adivasi Party - BHRTADVSIP',
                    'Communist Party of India  (Marxist) - CPI(M)',
                    'Communist Party of India  (Marxist-Leninist)  (Liberation) - CPI(ML)(L)',
                    'Communist Party of India - CPI',
                    'Dravida Munnetra Kazhagam - DMK',
                    'Indian Union Muslim League - IUML',
                    'Nat`Jammu & Kashmir National Conference - JKN',
                    'Jharkhand Mukti Morcha - JMM',
                    'Jammu & Kashmir National Conference - JKN',
                    'Kerala Congress - KEC',
                    'Marumalarchi Dravida Munnetra Kazhagam - MDMK',
                    'Nationalist Congress Party Sharadchandra Pawar - NCPSP',
                    'Rashtriya Janata Dal - RJD',
                    'Rashtriya Loktantrik Party - RLTP',
                    'Revolutionary Socialist Party - RSP',
                    'Samajwadi Party - SP',
                    'Shiv Sena (Uddhav Balasaheb Thackrey) - SHSUBT',
                    'Viduthalai Chiruthaigal Katchi - VCK'
        )
    ORDER BY Seats_Won DESC
    ```

### 2. Data Modification (Adding Party Alliance Information)

These queries modify the `partywise_results` table to include a `party_alliance` column.  **Important:**  Run these *before* running the alliance-based analysis queries.

*   **Add new column field in table partywise\_results to get the Party Allianz as NDA, I.N.D.I.A and OTHER**

    ```
    ALTER TABLE partywise_results
    ADD party_alliance VARCHAR(50);
    ```

*   **I.N.D.I.A Allianz**

    ```
    UPDATE partywise_results
    SET party_alliance = 'I.N.D.I.A'
    WHERE party IN (
        'Indian National Congress - INC',
        'Aam Aadmi Party - AAAP',
        'All India Trinamool Congress - AITC',
        'Bharat Adivasi Party - BHRTADVSIP',
        'Communist Party of India  (Marxist) - CPI(M)',
        'Communist Party of India  (Marxist-Leninist)  (Liberation) - CPI(ML)(L)',
        'Communist Party of India - CPI',
        'Dravida Munnetra Kazhagam - DMK',	
        'Indian Union Muslim League - IUML',
        'Jammu & Kashmir National Conference - JKN',
        'Jharkhand Mukti Morcha - JMM',
        'Kerala Congress - KEC',
        'Marumalarchi Dravida Munnetra Kazhagam - MDMK',
        'Nationalist Congress Party Sharadchandra Pawar - NCPSP',
        'Rashtriya Janata Dal - RJD',
        'Rashtriya Loktantrik Party - RLTP',
        'Revolutionary Socialist Party - RSP',
        'Samajwadi Party - SP',
        'Shiv Sena (Uddhav Balasaheb Thackrey) - SHSUBT',
        'Viduthalai Chiruthaigal Katchi - VCK');
    ```

*   **NDA Allianz**

    ```
    UPDATE partywise_results
    SET party_alliance = 'NDA'
    WHERE party IN (
        'Bharatiya Janata Party - BJP',
        'Telugu Desam - TDP',
        'Janata Dal  (United) - JD(U)',
        'Shiv Sena - SHS',
        'AJSU Party - AJSUP',
        'Apna Dal (Soneylal) - ADAL',
        'Asom Gana Parishad - AGP',
        'Hindustani Awam Morcha (Secular) - HAMS',
        'Janasena Party - JnP',
        'Janata Dal  (Secular) - JD(S)',
        'Lok Janshakti Party(Ram Vilas) - LJPRV',
        'Nationalist Congress Party - NCP',
        'Rashtriya Lok Dal - RLD',
        'Sikkim Krantikari Morcha - SKM'
    );
    ```

*   **OTHER**

    ```
    UPDATE partywise_results
    SET party_alliance = 'OTHER'
    WHERE party_alliance IS NULL;
    ```

### 3. Analysis Based on Party Alliance

These queries analyze election data based on the `party_alliance` column in the `partywise_results` table.

*   **Which party alliance (NDA, I.N.D.I.A, or OTHER) won the most seats across all states?**

    ```
    SELECT 
        p.party_alliance,
        COUNT(cr.Constituency_ID) AS Seats_Won
    FROM 
        constituencywise_results cr
    JOIN 
        partywise_results p ON cr.Party_ID = p.Party_ID
    WHERE 
        p.party_alliance IN ('NDA', 'I.N.D.I.A', 'OTHER')
    GROUP BY 
        p.party_alliance
    ORDER BY 
        Seats_Won DESC;
    ```

*   **Winning candidate's name, their party name, total votes, and the margin of victory for a specific state and constituency?**

    ```
    SELECT cr.Winning_Candidate, p.Party, p.party_alliance, cr.Total_Votes, cr.Margin, cr.Constituency_Name, s.State
    FROM constituencywise_results cr
    JOIN partywise_results p ON cr.Party_ID = p.Party_ID
    JOIN statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
    JOIN states s ON sr.State_ID = s.State_ID
    WHERE s.State = 'KARNATAKA' AND cr.Constituency_Name = 'TUMKUR';
    ```

*   **Which parties won the most seats in s State, and how many seats did each party win?**

    ```
    SELECT 
        p.Party,
        COUNT(cr.Constituency_ID) AS Seats_Won
    FROM 
        constituencywise_results cr
    JOIN 
        partywise_results p ON cr.Party_ID = p.Party_ID
    JOIN 
        statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
    JOIN states s ON sr.State_ID = s.State_ID
    WHERE 
        s.state = 'KARNATAKA'
    GROUP BY 
        p.Party
    ORDER BY 
        Seats_Won DESC;
    ```

*   **What is the total number of seats won by each party alliance (NDA, I.N.D.I.A, and OTHER) in each state for the India Elections 2024**

    ```
    SELECT 
        s.State AS State_Name,
        SUM(CASE WHEN p.party_alliance = 'NDA' THEN 1 ELSE 0 END) AS NDA_Seats_Won,
        SUM(CASE WHEN p.party_alliance = 'I.N.D.I.A' THEN 1 ELSE 0 END) AS INDIA_Seats_Won,
    	SUM(CASE WHEN p.party_alliance = 'OTHER' THEN 1 ELSE 0 END) AS OTHER_Seats_Won
    FROM 
        constituencywise_results cr
    JOIN 
        partywise_results p ON cr.Party_ID = p.Party_ID
    JOIN 
        statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
    JOIN 
        states s ON sr.State_ID = s.State_ID
    WHERE 
        p.party_alliance IN ('NDA', 'I.N.D.I.A',  'OTHER')  -- Filter for NDA and INDIA alliances
    GROUP BY 
        s.State
    ORDER BY 
        s.State;
    ```

*   **Which candidate received the highest number of EVM votes in each constituency (Top 10)?**

    ```
    SELECT TOP 10
        cr.Constituency_Name,
        cd.Constituency_ID,
        cd.Candidate,
        cd.EVM_Votes
    FROM 
        constituencywise_details cd
    JOIN 
        constituencywise_results cr ON cd.Constituency_ID = cr.Constituency_ID
    WHERE 
        cd.EVM_Votes = (
            SELECT MAX(cd1.EVM_Votes)
            FROM constituencywise_details cd1
            WHERE cd1.Constituency_ID = cd.Constituency_ID
        )
    ORDER BY 
        cd.EVM_Votes DESC;
    ```

*   **Which candidate won and which candidate was the runner-up in each constituency of State for the 2024 elections?**

    ```
    WITH RankedCandidates AS (
        SELECT 
            cd.Constituency_ID,
            cd.Candidate,
            cd.Party,
            cd.EVM_Votes,
            cd.Postal_Votes,
            cd.EVM_Votes + cd.Postal_Votes AS Total_Votes,
            ROW_NUMBER() OVER (PARTITION BY cd.Constituency_ID ORDER BY cd.EVM_Votes + cd.Postal_Votes DESC) AS VoteRank
        FROM 
            constituencywise_details cd
        JOIN 
            constituencywise_results cr ON cd.Constituency_ID = cr.Constituency_ID
        JOIN 
            statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
        JOIN 
            states s ON sr.State_ID = s.State_ID
        WHERE 
            s.State = 'Maharashtra'
    )

    SELECT 
        cr.Constituency_Name,
        MAX(CASE WHEN rc.VoteRank = 1 THEN rc.Candidate END) AS Winning_Candidate,
        MAX(CASE WHEN rc.VoteRank = 2 THEN rc.Candidate END) AS Runnerup_Candidate
    FROM 
        RankedCandidates rc
    JOIN 
        constituencywise_results cr ON rc.Constituency_ID = cr.Constituency_ID
    GROUP BY 
        cr.Constituency_Name
    ORDER BY 
        cr.Constituency_Name;
    ```

*   **For the state of KARNATAKA, what are the total number of seats, total number of candidates, total number of parties, total votes (including EVM and postal), and the breakdown of EVM and postal votes?**

    ```
    SELECT 
        COUNT(DISTINCT cr.Constituency_ID) AS Total_Seats,
        COUNT(DISTINCT cd.Candidate) AS Total_Candidates,
        COUNT(DISTINCT p.Party) AS Total_Parties,
        SUM(cd.EVM_Votes + cd.Postal_Votes) AS Total_Votes,
        SUM(cd.EVM_Votes) AS Total_EVM_Votes,
        SUM(cd.Postal_Votes) AS Total_Postal_Votes
    FROM 
        constituencywise_results cr
    JOIN 
        constituencywise_details cd ON cr.Constituency_ID = cd.Constituency_ID
    JOIN 
        statewise_results sr ON cr.Parliament_Constituency = sr.Parliament_Constituency
    JOIN 
        states s ON sr.State_ID = s.State_ID
    JOIN 
        partywise_results p ON cr.Party_ID = p.Party_ID
    WHERE 
        s.State = 'KARNATAKA';
    ```

## Usage

1.  **Database Setup:** Ensure you have **Microsoft SQL Server** set up and populated with the election data, matching the table schemas implied by the queries.
2.  **Data Loading:** Load the election data into the appropriate tables.
3.  **Run Queries:** Execute the SQL queries using SQL Server Management Studio (SSMS) or another SQL Server client.  Remember to run the `ALTER TABLE` and `UPDATE` queries *before* running the alliance-based analysis queries.
4.  **Adapt:** Modify the queries as needed to analyze different states, constituencies, or specific parties.

## Notes

*   These queries are specifically designed for **Microsoft SQL Server**.
*   The queries assume a specific schema for the election data. Adjust table and column names as necessary to match your data.
*   The alliance definitions (NDA, I.N.D.I.A, OTHER) are hardcoded in the queries.  Update these definitions as needed based on the actual alliances.
*   The data modification queries (`ALTER TABLE` and `UPDATE`) should only be run once to set up the `party_alliance` column.

## Contributing

Contributions to this repository are welcome!  Feel free to submit pull requests with improvements to the queries, bug fixes, or new analysis scripts.
