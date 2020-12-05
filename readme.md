# Wagtail demo project

This is a demonstration project for the amazing [Wagtail CMS](https://github.com/wagtail/wagtail).

The demo site is designed to provide examples of common features and recipes to introduce you to Wagtail development. Beyond the code, it will also let you explore the admin / editorial interface of the CMS.

Note we do _not_ recommend using this project to start your own site - the demo is intended to be a springboard to get you started. Feel free to copy code from the demo into your own project.

### Wagtail Features Demonstrated in This Demo

This demo is aimed primarily at developers wanting to learn more about the internals of Wagtail, and assumes you'll be reading its source code. After browsing the features, pay special attention to code we've used for:

- Dividing a project up into multiple apps
- Custom content models and "contexts" in the "breads" and "locations" apps
- A typical weblog in the "blog" app
- Example of using a "base" app to contain misc additional functionality (e.g. Contact Form, About, etc.)
- "StandardPage" model using mixins borrowed from other apps
- Example of customizing the Wagtail Admin via _wagtail_hooks_
- Example of using the Wagtail "snippets" system to represent bread categories, countries and ingredients
- Example of a custom "Galleries" feature that pulls in images used in other content types in the system
- Example of creating ManyToMany relationships via the Ingredients feature on BreadPage
- Lots more

## LAB 1

Välkommen till Wagtail - ett CMS-system för Python. Till att börja med ska vi få igång Wagtail i en docker container.

Ledning för de vanligaste docker-kommandona finns här: [Docker cheat sheet](https://www.docker.com/sites/default/files/d8/2019-09/docker-cheat-sheet.pdf)

### Övningar

1. Öppna terminalen och gå till root i projektet (alt. använd en IDE-terminal)

2. Bygg en **image** av Wagtail, med lämplig tag (-t). Det finns en färdiggjord Dockerfile som specificer hur Wagtail ska byggas.

   - Hint: "." markerar det directory du står i.

3. Vilka images har du nu på din dator?
4. Vilken image utgick Wagtail från tror du?
5. Testa starta en **container** med:

   `docker run {yourimage}`

6. Vilden adress lyssnar Wagtail på och du den och kommer du åt den adressen? Varför inte?
7. Starta nu en ny container med:

   - _port forwarding_ för att göra containern tillgänglig.
   - environment-variablen (-e) \*DJANGO_SECRET_KEY='test'.
   - I _detached_ state (-d). Detta betyder att docker run inte "attachar" till terminalen.

8. Kolla nu upp ID för containern du precis startade
9. Kan du också hitta containern du stoppade?

   Reflektera någon sekund över att du byggt två containers från samma image.

10. Gå till hemsidan, den har förhoppningsvis startat (Yay!), med ser inte mycket ut för världen. Testa att logga in via _/admin_. Det verkar inte som vi skapat en superuser va?

11. För att skapa en användare måste du köra:

    `/venv/bin/python manage.py createsuperuser`

    **inuti** containern. STÄNG INTE SHELL-SESSIONEN NÄR DU ÄR FÄRDIG.

    - Hint: Du står i _/code_ när du kommer in i containern.

12. Testa nu att logga in. Kan du lista ut vad som blir fel?

    - Hint: Wagtail använder en sqlite databas.
    - Hint: Går det att få ut containerns log?

13. Fixa felet för att kunna logga in

    - Hint: Även directory permission för /code behövs ändras.
    - Hint: chmod 777 (det är inte en säkerhetslab...) is your friend.

14. Testa nu att logga in!

15. Har ni ännu mer tid testa att:

    - På valfritt sätt mounta containerns /code/db.sqllite (-v)
    - Hjälp dina kollegor

## LAB 2

[docker-compose cheat sheet](https://devhints.io/docker-compose)

### Övningar

1. Öppna docker-compose.yml. Innehåller motsvarar ungefär docker start ... från LAB 1. `Docker compose up` används för att sätta upp alla services i docker-compose. Börja med att implementera så att docker-compose bygger image:n (motsvarande docker build).

2. Wagtail fungerar också med en annan databas än sqllite. Nu vill vi använda den färdiga image:n postgres:9.6. Sätt upp postgres:9.6 som en service, kallad _"db"_. Du behöber environment-variablerna:

   - POSTGRES_DB: app_db
   - POSTGRES_USER: {insert}
   - POSTGRES_PASSWORD: {insert}

   samt:

   - lägg till _"restart: always"_ också!

3. Testa att det funkar med `docker-compose up db`

4. Nu ska vi koppla ihop databasen med servern, genom environment-variablen _"DATABASE_URL"_. Använd url:en: "postgres://{username}:{password}@db/app_db". STARTA INTE APP-SERVERN ÄN. Tänk på hur vi refererar till databas-url:en från den andra containern. Varför funkar det?

5. Se nu till att databasen startar före appen. [Läs depends on i docs](https://docs.docker.com/compose/compose-file/)

   - Hint: Använd docker-entrypoint-compose.sh i Dockerfile istället. Varför?

6. Kör nu

   1. `docker-compose run app /venv/bin/python manage.py load_initial_data` för att ladda startdata
   2. `docker-compose up` för att köra igång appen.

   - Du kan logga in i admin med _admin / changeme_

   Vad händer med data när vi stänger av appen?

7. Skapa en volym och mounta _"/var/lib/postgresql/data"_ för att spara postgres data. Testa att det funkar genom att köra om 1 och 2 i steg 5.

8. Skapa nu ett separat bride-nätverk mellan appen och databasen. [Ett exempel finns här](https://linuxhint.com/docker_compose_bridge_networking/)
