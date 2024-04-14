--- Verificando o dataset

SELECT *
FROM Dados_Airbnb

-- Data Cleaning

---- Verificando duplicatas

WITH RowNumCTE AS(
SELECT *, 
		ROW_NUMBER() OVER (
		PARTITION BY id, 
		name, 
		host_id
		ORDER BY id) as Row_Num
FROM Dados_Airbnb)

SELECT *
FROM RowNumCTE
WHERE Row_Num > 1 
ORDER BY id

---- Deletando colunas que não serão úteis 

ALTER TABLE Dados_Airbnb
DROP COLUMN last_review, latitude, longitude, host_name, name


---- Ajustando data em last_review

ALTER TABLE Dados_Airbnb -- Adicionando uma nova coluna ajustada
ADD last_review_ajustado Date;

UPDATE Dados_Airbnb  -- Equiparando a coluna antiga com a ajustada
SET last_review_ajustado = CONVERT(Date, last_review)

SELECT last_review_ajustado -- Verificando
FROM Dados_Airbnb

-- Olhando para dados nulos

---- Basicamente, após verificar, notei que as váriaveis categóricas não apresentam valores nulos. Apenas temos nulos em colunas numéricas, as quais temos a possibilidade natural de ter valores nulos. 



-- Quantidade de móveis por distrito

SELECT 
neighbourhood_group as 'Distrito', COUNT(room_type) as 'Número de Imóveis'
FROM Dados_Airbnb 
GROUP BY neighbourhood_group
ORDER BY COUNT(room_type) DESC

-- Quantidade de imóveis por tipo

SELECT
		room_type as 'Tipo de Imóvel', 
		COUNT(room_type) as 'Contagem',
		FORMAT(AVG(price), 'C', 'en-US') as 'Média de Preço'
FROM Dados_Airbnb
GROUP BY room_type
ORDER BY AVG(price) DESC

-- Média de disponibilidade de imóveis 

SELECT
    room_type AS 'Tipo de Imóvel',
    COUNT(room_type) AS 'Contagem',
    ROUND(AVG(availability_365), 2) AS 'Média de disponibilidade'
FROM
    Dados_Airbnb
GROUP BY
    room_type
ORDER BY
    AVG(availability_365) DESC;


-- Quantidade de imóveis por tipo por distrito

SELECT
  Tipo_de_Imovel,
  Distrito,
  Contagem,
  COUNT(*) OVER (PARTITION BY Distrito) as Total_por_Distrito
FROM (
  SELECT
    room_type as Tipo_de_Imovel,
    neighbourhood_group as Distrito,
    COUNT(room_type) as Contagem
  FROM Dados_Airbnb
  GROUP BY room_type, neighbourhood_group
) subquery
ORDER BY Distrito ASC

-- Distritos mais caros x mais baratos

SELECT 
    neighbourhood_group as 'Distrito', 
    FORMAT(AVG(price), 'C', 'en-US') as 'Média de Preço'
FROM 
    Dados_Airbnb
GROUP BY 
    neighbourhood_group
ORDER BY 
    AVG(price) DESC;



-- Quais os bairros mais caros e mais baratos? Quais tem a maior oferta de móveis?

SELECT 
	TOP 5 neighbourhood AS 'Bairro', 
	COUNT(neighbourhood) as 'Contagem de imóveis',
    FORMAT(AVG(price), 'C', 'en-US') AS 'Média de Preço',
	neighbourhood_group as 'Distrito'
FROM 
    Dados_Airbnb
GROUP BY 
    neighbourhood, neighbourhood_group
HAVING 
	Count(neighbourhood) > 1
ORDER BY 
    AVG(price) ASC;

-- Contando os imóveis somente
SELECT 
	TOP 5 neighbourhood AS 'Bairro', 
	COUNT(neighbourhood) as 'Contagem de imóveis',
	neighbourhood_group as 'Distrito'
FROM 
    Dados_Airbnb
GROUP BY 
    neighbourhood, neighbourhood_group
ORDER BY 
    COUNT(neighbourhood) DESC;


-- Contagem de bairros com mais ou menos imóveis em um mesmo output.

WITH ContagemImoveis AS (
    SELECT 
        neighbourhood AS 'Bairro', 
        COUNT(neighbourhood) AS 'Contagem de imóveis',
        neighbourhood_group AS 'Distrito',
        ROW_NUMBER() OVER (ORDER BY COUNT(neighbourhood) DESC) AS 'Rank_Mais',
        ROW_NUMBER() OVER (ORDER BY COUNT(neighbourhood) ASC) AS 'Rank_Menos'
    FROM 
        Dados_Airbnb
    GROUP BY 
        neighbourhood, neighbourhood_group
)
SELECT 
    'Top 5' AS 'Tipo',
    Bairro,
    [Contagem de imóveis],
    Distrito
FROM 
    ContagemImoveis
WHERE 
    Rank_Mais <= 5

UNION ALL

SELECT 
    '5 menos imóveis' AS 'Tipo',
    Bairro,
    [Contagem de imóveis],
    Distrito
FROM 
    ContagemImoveis
WHERE 
    Rank_Menos <= 5 

--
WITH ContagemImoveis AS (
    SELECT 
        neighbourhood AS 'Bairro', 
        COUNT(neighbourhood) AS 'Contagem de imóveis',
        FORMAT(AVG(price), 'C', 'en-US') AS 'Média de Preço',
        neighbourhood_group AS 'Distrito',
        ROW_NUMBER() OVER (ORDER BY AVG(price) DESC) AS 'Rank_Mais',
        ROW_NUMBER() OVER (ORDER BY AVG(price) ASC) AS 'Rank_Menos'
    FROM 
        Dados_Airbnb
    GROUP BY 
        neighbourhood, neighbourhood_group
    HAVING 
        COUNT(neighbourhood) > 1
)
SELECT 
    'Top 5' AS 'Tipo',
    Bairro,
    [Contagem de imóveis],
    [Média de Preço],
    Distrito,
    Rank_Mais AS 'Rank'
FROM 
    ContagemImoveis
WHERE 
    Rank_Mais <= 5

UNION ALL

SELECT 
    'Bottom 5' AS 'Tipo',
    Bairro,
    [Contagem de imóveis],
    [Média de Preço],
    Distrito,
    Rank_Menos AS 'Rank'
FROM 
    ContagemImoveis
WHERE 
    Rank_Menos <= 5
ORDER BY 'Tipo' DESC, Rank;

