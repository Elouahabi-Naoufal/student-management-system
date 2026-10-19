<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->
<a id="readme-top"></a>

<!-- PROJECT SHIELDS -->
<!--
*** I'm using markdown "reference style" links for readability.
*** Reference links are enclosed in brackets [ ] instead of parentheses ( ).
*** See the bottom of this document for the declaration of the reference variables.
*** https://www.markdownguide.org/basic-syntax/#reference-style-links
-->
[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]

<!-- PROJECT TITLE BLOCK -->
<br />
<div align="center">
  <a href="https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants">
    <h3 align="center">Gestion-des-Etudiants</h3>
  </a>

  <p align="center">
    A console application in C++ for managing students, teachers, exams and grades
    against a MariaDB/MySQL database.
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants/blob/main/mariadb_installation.sh"><strong>Database setup script »</strong></a>
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants/issues">Report Bug</a>
    &middot;
    <a href="https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants/issues">Request Feature</a>
  </p>
</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#repository-layout">Repository layout</a></li>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
        <li><a href="#database-schema">Database schema</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>

<!-- ABOUT THE PROJECT -->
## About The Project

`Gestion-des-Etudiants` is a command-line student management system written as a single
C++ translation unit, `Gestion_des_etudiants.cpp`. It connects to a MariaDB/MySQL
server through the MySQL C client API and offers CRUD operations over four entities —
students, teachers, exams and grades — plus CSV export, CSV import and CSV backups.

The code is organised around a small set of classes:

| Class | Responsibility |
|---|---|
| `Database` | Wraps the `MYSQL*` handle. Owns `query`, `fetch` (prints a result set as a padded table), `delete_record`, `update_record` and `backup`. |
| `Personne` | Abstract base class holding `nom` / `prenom`, with a pure-virtual `saisir()`. |
| `Enseignant` | Teacher: `nom`, `prenom`, `matiere`. Full CRUD plus name search. |
| `Etudiant` | Student: `id`, `nom`, `prenom`, `id_groupe`. Full CRUD plus name search. |
| `Examen` | Exam: `id`, `matiere`, `date`. Full CRUD. |
| `Note` | Grade: composite key `(id_etudiant, id_examen)` plus a `float` mark. Insert, update, delete, and an average computed by the database with `AVG(note)`. |

Input is read straight from `std::cin`. Two helpers, `validateIntInput` and
`validateFloatInput`, loop until they get a value of the right type, and the rest of
the code depends on them to survive bad input.

### Repository layout

```
Gestion-des-Etudiants/
├── Gestion_des_etudiants.cpp   # the whole application (single translation unit)
├── mariadb_installation.sh     # installs MariaDB, creates the user, database and tables
└── Tables                      # captured `information_schema.COLUMNS` output for `school`
```

Two things worth calling out before you read further:

- **`Tables` is a file, not a directory.** It is a saved copy of the output of the
  `information_schema` query at the end of `mariadb_installation.sh`, i.e. a snapshot
  of the schema after creation. Editing it has no effect on the database.
- **Connection settings are hardcoded.** `Gestion_des_etudiants.cpp:17` connects as
  `poo` / `poo` to the `school` database on `localhost:3306`. The shell script creates
  that user with `GRANT ALL PRIVILEGES ON *.*` (`mariadb_installation.sh:25`). These
  are throwaway local-development credentials; change both the script and the
  `mysql_real_connect` call before pointing this at anything shared.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Built With

* [![C++][C++.url]][C++]
* [![MySQL][MySQL.url]][MySQL]
* [![MariaDB][MariaDB.url]][MariaDB]
* [![Bash][Bash.url]][Bash]

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- GETTING STARTED -->
## Getting Started

### Prerequisites

* A C++ compiler supporting C++11 or later (the code uses `override` and
  brace-free initialiser lists, nothing newer).
* The MySQL/MariaDB client development headers, so that `#include <mysql/mysql.h>`
  resolves — `libmysqlclient-dev` on Debian/Ubuntu, `mariadb-devel` on Fedora/RHEL.
* MariaDB server. `mariadb_installation.sh` is written for Debian/Ubuntu: it calls
  `apt-get` and `service mariadb start`.

```sh
# Debian / Ubuntu
sudo apt-get install -y g++ libmysqlclient-dev
```

### Installation

1. Clone the repo

   ```sh
   git clone https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants.git
   cd Gestion-des-Etudiants
   ```

2. Create the database, the user and the tables

   ```sh
   sudo bash mariadb_installation.sh
   ```

   The script installs `mariadb-server`, runs `mysql_secure_installation`, creates the
   user `poo`@`localhost` with password `poo`, then creates the `school` database and
   its four tables. It ends by printing the schema.

   If MariaDB is already installed and running, skip the first lines and run only the
   `mysql -u poo -ppoo <<EOF ... EOF` block.

3. Build

   There is no Makefile or CMakeLists in the repository, so compile the single source
   file directly:

   ```sh
   g++ -std=c++17 Gestion_des_etudiants.cpp -o gestion_etudiants -lmysqlclient
   ```

4. Run

   ```sh
   ./gestion_etudiants
   ```

   The connection is attempted in the `Database` constructor, before the menu is drawn.
   If it fails the program prints `Database connection failed!` and exits with status 1.

### Database schema

As created by `mariadb_installation.sh` and confirmed by the `Tables` snapshot:

| Table | Columns |
|---|---|
| `enseignants` | `id INT AUTO_INCREMENT PRIMARY KEY`, `nom VARCHAR(255) NOT NULL`, `prenom VARCHAR(255) NOT NULL`, `matiere VARCHAR(255) NOT NULL` |
| `etudiants` | `id INT PRIMARY KEY`, `nom VARCHAR(255) NOT NULL`, `prenom VARCHAR(255) NOT NULL`, `id_groupe INT NOT NULL` |
| `examens` | `id INT PRIMARY KEY`, `matiere VARCHAR(255) NOT NULL`, `date DATE NOT NULL` |
| `notes` | `id_etudiant INT NOT NULL`, `id_examen INT NOT NULL`, `note FLOAT NOT NULL`, `PRIMARY KEY (id_etudiant, id_examen)` |

Two observations that are visible in the code as well as the schema:

- `etudiants.id_groupe` is an integer with no `groupes` table behind it, so group
  membership is stored but never validated or reported on.
- There are no foreign keys. `notes.id_etudiant`, `notes.id_examen` and
  `etudiants.id_groupe` are plain integers, and referential integrity is left entirely
  to the application.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- USAGE EXAMPLES -->
## Usage

The program is menu driven. The main menu (`displayMainMenu`) has six entries plus quit:

```
1. Gérer les Étudiants      4. Gérer les Notes
2. Gérer les Enseignants    5. Exporter/Importer
3. Gérer les Examens        6. Backup
                            0. Quitter
```

Each submenu is a nested loop; enter `0` at any prompt to return to the main menu.

| Section | Choices |
|---|---|
| Étudiants (`1`) | `1` add · `2` list · `3` search by name · `4` show by ID · `12` delete · `16` update · `20` average grade |
| Enseignants (`2`) | `5` add · `6` list · `7` search by name · `13` delete · `17` update |
| Examens (`3`) | `8` add · `9` list · `14` delete · `18` update |
| Notes (`4`) | `10` add · `11` list · `15` delete · `19` update |
| Export/Import (`5`) | `21` export students to `etudiants.csv` · `22` import students from `etudiants.csv` |
| Backup (`6`) | `23` backup `enseignants` · `24` backup `examens` · `25` backup `notes` |

A couple of behaviours to expect:

- **Sorting.** The "list" option prompts `Trier les ... par (id, nom)` and appends the
  answer straight into the query as `ORDER BY <answer>`. Typing a column name sorts;
  typing anything else produces a server-side error.
- **Search** is `WHERE nom LIKE '%<input>%'`, so a substring is enough.
- **Average grade** runs `SELECT AVG(note) FROM notes WHERE id_etudiant = <id>` and
  prints `Aucune note trouvée pour cet étudiant.` when the student has no rows.
- **Files are written to the current working directory.** `etudiants.csv` for the
  export/import pair, and `<table>.csv` for each backup.
- **The import is not idempotent.** `importerEtudiants` replays every line of the CSV
  as an `INSERT`, so running it twice against the same file duplicates the rows and
  fails on the duplicate primary key.

All SQL is assembled by string concatenation and sent through `mysql_query`. There are
no prepared statements, so every string that comes from the keyboard reaches the SQL
parser verbatim. That is fine for a local teaching project; it is not fine for anything
exposed to untrusted input.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ROADMAP -->
## Roadmap

Implemented today:

- [x] Create, read, update and delete for `enseignants`, `etudiants`, `examens` and `notes`
- [x] Abstract `Personne` base class with a virtual `saisir()` and `Enseignant` / `Etudiant` overrides
- [x] Search by name and lookup by primary key
- [x] `ORDER BY` sorting on list views
- [x] Average grade per student
- [x] CSV export/import for students, CSV backup for teachers, exams and grades
- [x] Typed input validation for the console

Known gaps, derived from the current state of the code:

- [ ] Replace concatenated SQL with `mysql_stmt` prepared statements
- [ ] Move host, user, password and database out of `Gestion_des_etudiants.cpp` into a config file or environment variable
- [ ] Add a `groupes` table with a foreign key from `etudiants.id_groupe`, or drop the column
- [ ] Add foreign keys for `notes.id_etudiant` and `notes.id_examen`
- [ ] Make `importerEtudiants` idempotent (upsert or `LOAD DATA`)
- [ ] Add a build system — there is no `Makefile` or `CMakeLists.txt`
- [ ] Add a `LICENSE` file

See the [open issues](https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants/issues) for a full list of proposed features (and known issues).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Top contributors:

<a href="https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=Elouahabi-Naoufal/Gestion-des-Etudiants" alt="contrib.rocks image" />
</a>

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- LICENSE -->
## License

No `LICENSE` file is currently bundled with this repository, so no license has been
granted for it. Until one is added, treat the code as all rights reserved and ask
before reusing it. Opening an issue to propose a license is welcome.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTACT -->
## Contact

Naoufal Elouahabi - [LinkedIn](https://www.linkedin.com/in/elouahabi-naoufal/) - elouahabi-naoufal1@gmail.com

Project Link: [https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants](https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

* [Best README Template](https://github.com/othneildrew/Best-README-Template)
* [MySQL C API documentation](https://dev.mysql.com/doc/dev/c-api/8.0/en/)
* [MariaDB Knowledge Base](https://mariadb.com/kb/en/)
* [Img Shields](https://shields.io)
* [contrib.rocks](https://contrib.rocks/)
* [Choose an Open Source License](https://choosealicense.com)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/Elouahabi-Naoufal/Gestion-des-Etudiants.svg?style=for-the-badge
[contributors-url]: https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/Elouahabi-Naoufal/Gestion-des-Etudiants.svg?style=for-the-badge
[forks-url]: https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants/network/members
[stars-shield]: https://img.shields.io/github/stars/Elouahabi-Naoufal/Gestion-des-Etudiants.svg?style=for-the-badge
[stars-url]: https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants/stargazers
[issues-shield]: https://img.shields.io/github/issues/Elouahabi-Naoufal/Gestion-des-Etudiants.svg?style=for-the-badge
[issues-url]: https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants/issues
[license-shield]: https://img.shields.io/github/license/Elouahabi-Naoufal/Gestion-des-Etudiants.svg?style=for-the-badge
[license-url]: https://github.com/Elouahabi-Naoufal/Gestion-des-Etudiants
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/elouahabi-naoufal/
[C++]: https://img.shields.io/badge/c%2B%2B-00599C?style=for-the-badge&logo=c%2B%2B&logoColor=white
[MySQL]: https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white
[MariaDB]: https://img.shields.io/badge/MariaDB-003545?style=for-the-badge&logo=mariadb&logoColor=white
[Bash]: https://img.shields.io/badge/Bash-4EAA25?style=for-the-badge&logo=gnu-bash&logoColor=white