# Formula 1 Project
Projekt ma na celu przeprowadzenie analizy różnych aspektów dotyczących historii Formuły 1. W tym celu wykorzystałem bazę danych dotyczącą wydarzeń w Formule 1 na przestrzeni lat 1950 – 2022, 
która posłużyła jako baza do pisania zapytań w MySQL. Otrzymane wyniki zostały zilustrowane w postaci raportu utworzonego za pomocą programu Power BI. 

Przedstawione poniżej zapytania umożliwiają zgłębienie historii Formuły 1, dając wgląd w dynamikę tego sportu. Główne obszary jakie zostały wzięte pod uwagę to:

•	Statystki dotyczące sezonów, a więc na przykład to ile wyścigów wchodziło w kalendarz danego sezonu czy to na jakich torach i w jakich krajach odbywały się wyścigi.

•	Analiza kierowców pod względem osiągnięć jakie zdobyli w trakcie swoich karier, dzięki czemu możliwe jest zidentyfikowanie dominujących postaci w całej historii Formuły 1 jak i na przestrzeni wybranych sezonów 

•	Analiza konstruktorów – podobnie jak w przypadku kierowców zapytania mają na celu stworzyć rankingi zespołów biorąc pod uwagę takie aspekty jak na przykład 
liczba wygranych wyścigów czy średnia liczba punktów zdobywanych na wyścig. 


# Liczba wyścigów w sezonie
   
      select Year as Season, count(Round) as Num_races_season
      from races
      group by Year
      order by Year;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/ff971648-f53d-4b8a-8b25-5422289540a6)
![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/10ff5053-826a-467c-8c66-04ecc2ca1200)


Powyższe zapytanie grupuje wyścigi po latach i oblicza liczbę wyścigów w każdym sezonie. Wynik jest sortowany rosnąco według roku. Na wykresie widać oczywisty trend wzrostowy liczby wyścigów w kolejnych latach. 
Początkowo w latach 50 wartość ta nie przekraczała 10 wyścigów, a w ostatnim sezonie odbyło się aż 22 wyścigi. 


# Liczba wyścigów w podziale na kraje
   
      select Country, count(r.race_id) as Num_races_country
      from circuits c left join races r on c.Circuit_ID = r.Circuit_Id
      group by Country
      order by 2 desc;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/05113f89-ff58-46b5-b9c5-bbb15af584a3)
![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/b5c6bce5-7782-45c8-a845-9b29d9b54140)

To zapytanie zestawia wyścigi według krajów, oblicza liczbę wyścigów w każdym kraju i sortuje wynik malejąco według liczby wyścigów. Prym w tym zestawieniu prowadzą kraje europejskie i USA, które od samego początku istnienia F1 praktycznie nie przerwanie organizują wyścigi Grand Prix.


# Liczba wyścigów w podziale na kraje i poszczególne tory
   
      select c.Country, c.Name_of_circuit, count(r.Race_Id) as num_races_ciruit
      from circuits c left join races r on c.Circuit_ID = r.Circuit_ID
      group by c.Country, c.Name_of_circuit
      order by 3 desc;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/1ee29fe1-e62d-48fc-b2f7-acc23957be40)
![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/5f4c7db2-27f2-482f-9d37-3489bf86391f)

Powyższe zapytanie prezentuje liczbę wyścigów na poszczególnych torach. Wyświetla kraj oraz nazwę toru, a wynik jest sortowany malejąco według liczby wyścigów. Wyniki tego zapytania zostały zwizualizowane za pomocą tak zwanej mapy drzewa. Do głównej kategorii należą tutaj kraje, które organizowały wyścig F1, a wyszczególnieniem są poszczególne tory na, których w tych krajach były organizowane zawody. Dzięki czemu można zobaczyć na ile torów i jak rozkładała się ogólna liczba wyścigów w danym kraju. 


# Podstawowe informacje dotyczące sezonów i torów
   
      select count(distinct Race_ID) as total_num_races
      from results;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/58d6dc42-cea7-44ed-8d39-fca43708e64a)

      select count(distinct country) as total_num_country
      from circuits;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/d37e3e14-9458-4dc9-9ade-68fd56878611)

      select count(Year) as total_num_seasons
      from seasons;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/2d40af84-f91a-411c-8327-6e696babe9bd)

      select count(distinct Name_of_circuit) as total_num_circuits
      from circuits;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/b762bb4e-addb-4fe9-9133-2270d7668f48)

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/6943c631-48f3-4949-a10b-136bbe7014f2)


# Narodowości kierowców
   
      select Nationality, count(Nationality)
      from drivers
      group by Nationality
      order by 2 desc 
      Limit 7;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/48ac1c02-5bf5-48bf-8282-d7b04fd64616)
![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/f88c0158-64ad-458a-8187-fc95afd49ce9)

Zapytanie to identyfikuje siedem najczęstszych narodowości wśród kierowców którzy wystartowali chociaż w jednym wyścigu Formuły 1. 


# Wygrane wyścigi i pole position według kierowców
   
      SELECT d.Name, SUM(r.Wins) AS num_wins, Sum(r.Pole_Position)
      FROM results r LEFT JOIN drivers d ON r.Driver_Id = d.Driver_Id
      GROUP BY d.Name
      HAVING SUM(r.Wins) > (
         SELECT AVG(total_wins) FROM (
            SELECT d.Name, SUM(r.Wins) AS total_wins
            FROM results r LEFT JOIN drivers d ON r.Driver_ID = d.Driver_Id
            GROUP BY d.Name
         ) AS subquery
      )
      ORDER BY num_wins DESC
      Limit 6;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/50223a8f-27a5-4042-b63e-4e06fc7ece21)
![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/e461069d-1735-4906-92e7-1510060c5b85)

Zapytanie to zestawia kierowców któych suma wygranych wyścigów jest większa niż średnia liczba wygranych wyścigów przypadających na jednego kierowce. Ponadto dla każdego keirowcy generuje liczbę wygranych i zdobytych pole position, a więc wygranych kwalifikacji do wyścigu. Wynik jest sortowany malejąco według liczby wygranych wyścigów i ograniczony do 6 kierowców, którzy tych zwycięstw mają w historii najwięcej. Na wykresie żółty pasek ilustruję liczbę wygranych wyścigów, a pasek fioletowych liczbę zdobytych Pole Position.


# Średnie miejsce na mecie vs średnie miejsce na starcie – kierowcy
   
      select d.Name, round(Avg(r.Position),2) as avg_finish, round(avg(r.grid),2) as avg_start
      from results r left join drivers d on r.driver_id = d.driver_id
      group by d.Name
      having count(distinct r.Race_Id) > 20
      order by 2 asc
      Limit 10;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/a98b8dbd-4c58-4d05-ba87-5c40d40fd621)
![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/92861ba4-fae0-451a-9765-1b233f18e28e)

Zapytanie to identyfikuje dziesięciu kierowców, którzy uczestniczyli w co najmniej 20 wyścigach, prezentując ich średnią pozycję na mecie wyścigu oraz na starcie wyścigu, a więc średnia pozycję jaką zajmowali w kwalifikacjach poprzedzających wyścig. Wynik jest sortowany rosnąco według średniej pozycji na mecie. Wynik zapytania został przedstawiony na wykresie warstwowym, gdzie warstwa fioletowa oznacza średnią pozycje na starcie, a warstwa szara średnią pozycje kierowcy na mecie wyścigu.


# Procent wygranych i pole position kierowców
   
      select d.Name, Round((sum(r.wins)/count(distinct ra.race_id)),4) as '%_win', round((sum(r.pole_position)/count(distinct ra.race_id)),4) as '%_pole_position'
      from results r left join drivers d on r.driver_id = d.driver_id left join races ra on r.Race_Id = ra.Race_Id
      group by d.Name
      having count(distinct ra.race_id) > 10 and sum(r.Wins) >= 1
      order by 2 desc;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/0a39b340-4cc6-4d9f-b1d7-a294daf2659b)
![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/df5c8de9-6d8c-4970-ab14-2563a0d9b618)

Zapytanie to odpowiada na pytanie jaki procent wyścigów, w których brał udział dany kierowca ostatecznie zostało przez niego wygranych. Analogicznie sytuacja ma się w przypadku zdobytych Pole Position i udziału w kwalifikacjach. Pod uwagę brani są kierowcy, którzy startowali w co najmniej 10 wyścigach,  a także odnieśli przynajmniej jedno zwycięstwo w wyścigu. Otrzymany wynik jest sortowany według procenta wygranych wyścigów, który na wykresie przedstawiony jest kolorem fioletowym, a procent wygranych kwalifikacji kolorem zielonym. 


# Średnia liczba punktów na wyścig kierowców

      select d.Name, (round(sum(r.points)/count(distinct r.race_ID),2)) as avg_points_per_race
      from results r left join drivers d on r.driver_id = d.Driver_Id
      group by d.Name
      having count(distinct r.race_id) > 20
      order by 2 desc;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/b0039db7-3f55-44c7-952b-889faa754d8e)
![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/490b291d-7177-4fab-aebb-e9cd058df46a)

Zapytanie to oblicza średnią liczbę punktów zdobywanych przez kierowców podczas jednego wyścigu. Pod uwagę brani są tylko ci kierowcy, którzy brali udział w co najmniej 20 wyścigach. Otrzymany wynik jest sortowany malejąco według średniej liczby punktów. 


# Podstawowe informacje dotyczące kierowców i liczby wyścigów
    
      select count(distinct Race_ID) as total_num_races
      from results;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/d4a9595c-e05d-460c-83b6-cb3a850923ac)

      select count(distinct country) as total_num_country
      from circuits;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/f52ea8ac-aa78-47ed-ab1c-9718b4f73eb7)

      select count(distinct Constructor_Id) as total_num_drivers
      from constructors;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/1b8f1664-c2a7-45cc-bb7f-551e528af68e)

      select count(distinct driver_id) as total_num_circuits
      from drivers;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/8fd73b9f-e5c2-49c1-95d0-b8bca54e0794)

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/ec255273-4c81-4e4d-a028-4d25a5734b83)


# Kraje w których siedziby miało najwięcej zespołów

      select Nationality, count(Nationality) as num_nationality_con
      from constructors
      group by Nationality
      order by 2 desc
      limit 5;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/bfd30ccb-cd35-42ab-bdc6-fd0ca14ec4df)
![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/204e207e-dc6e-419d-a61c-78d4675a4993)

Zapytanie to identyfikuje pięć krajów w których swoje siedziby miało bądź ma najwięcej zespołów F1 i przedstawia ich liczbę przypadającą każdej narodowości, sortując otrzymany wynik malejąco właśnie według tej liczby. 


# Najwięcej wygranych i pole position według zespołów
    
      select c.Name_of_Constructor, sum(r.wins) as num_win_con, sum(r.Pole_Position) as num_pole_position_con
      from constructors c left join results r on c.Constructor_Id = r.Constructor_Id
      group by c.Name_of_Constructor
      order by 2 desc
      Limit 6;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/b591350f-dcdd-4bf7-968b-6e3219c67971)
![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/7b1746fb-6725-4921-9e0a-aca6f1b04d2a)

Powyższe zapytanie zestawia konstruktorów według liczby wygranych wyścigów oraz zdobytych pole position. Wynik sortowany jest malejąco według tej pierwszej liczby. Na wykresie pasek żółtego koloru obrazuje liczbę wygranych wyścigów, a pasek fioletowy zdobytych pole position.


# Suma punktów konstruktorów
    
      select Name_of_Constructor, sum(r.Points) as sum_point_con
      from constructors c left join results r on c.Constructor_Id = r.Constructor_Id
      group by Name_of_Constructor
      order by 2 desc;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/d048d64b-14a2-4187-a0be-000e0bd5ba4c)
![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/6edf6e79-4558-4b44-8e66-388e92ecc2c0)

Powyższe zapytanie przedstawia konstruktorów według sumy zdobytych punktów. Otrzymany wynik sortowany jest malejąco po sumie zdobytych punktów.


# Średnia liczba punktów zespołu na wyścig
    
      select Name_of_Constructor, round(sum(r.Points)/(count(r.Race_Id)),2) as avg_points_per_race
      from constructors c left join results r on c.Constructor_Id = r.Constructor_Id
      group by Name_of_Constructor
      having count(distinct r.Race_Id) > 10
      order by 2 desc;

![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/8160ec5b-5b91-4cbc-892e-195e8ae2b4bd)
![image](https://github.com/Michal-Janulewski/Formula_1_Project/assets/139379729/e9d2527d-8ab5-4216-9978-0648609bc10f)

Zapytanie to oblicza średnią liczbę punktów zdobytych przez jeden samochód danego konstruktora podczas wyścigu. Uwzględnianie są tylko zespoły, które brały udział w co najmniej 10 wyścigach. Wynik sortowany jest malejąco według otrzymanej średniej liczby punktów na wyścig.
























