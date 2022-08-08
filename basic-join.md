### African Cities

Given the **CITY** and **COUNTRY** tables, query the names of all cities where the `CONTINENT` is 'Africa'.

**Note**: CITY.CountryCode and COUNTRY.Code are matching key columns.
<br>

    SELECT
        city.name
    FROM city
    INNER JOIN country ON country.code = city.countrycode
    WHERE country.continent = 'Africa'


### Average Population of Each Continent

