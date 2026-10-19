<a id="readme-top"></a>

[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]

<br />
<div align="center">
  <h3 align="center">Student Management System</h3>

  <p align="center">
    A console application in C++ for managing students, teachers, exams and grades against a
    MariaDB/MySQL database, built as a 3rd-year PFM project.
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/student-management-system"><strong>Explore the source »</strong></a>
    <br />
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/student-management-system/issues/new?labels=bug">Report Bug</a>
    &middot;
    <a href="https://github.com/Elouahabi-Naoufal/student-management-system/issues/new?labels=enhancement">Request Feature</a>
  </p>
</div>

<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
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

## About The Project

This is a menu-driven student management system written as a single C++ translation unit,
`Gestion_des_etudiants.cpp`. It connects to a MariaDB/MySQL server through the MySQL C client API
and provides CRUD operations over four entities: `etudiants`, `enseignants`, `examens` and
`notes`.

The code is organised around a small set of classes:

| Class | Responsibility |
| --- | --- |
| `Database` | Wraps the `MYSQL*` handle and provides query, fetch, update, delete and CSV backup helpers |
| `Personne` | Abstract base class holding `nom` and `prenom`, with a pure-virtual `saisir()` |
| `Enseignant` | Teacher CRUD plus search by name |
| `Etudiant` | Student CRUD plus search by name and average-grade lookup |
| `Examen` | Exam CRUD |
| `Note` | Grade insert, update, delete and a database-computed average |

Additional behaviour includes:

* Typed integer and float input validation for the console
* CSV export and import for students
* CSV backup of teachers, exams and grades
* An OOP class hierarchy with `Personne` as the abstract base for `Enseignant` and `Etudiant`

The database schema is created by `mariadb_installation.sh`, and `Tables` contains a captured
`information_schema` snapshot of that schema. A compiled `Projet` binary is also committed, but
you can build from source with the command below.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Built With

* [![C++][C++.url]][C++]
* [![MySQL][MySQL.url]][MySQL]
* [![MariaDB][MariaDB.url]][MariaDB]
* [![Bash][Bash.url]][Bash]

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Getting Started

### Prerequisites

* A C++ compiler supporting C++11 or later
* MySQL/MariaDB client development headers so `#include <mysql/mysql.h>` resolves
  (`libmysqlclient-dev` on Debian/Ubuntu)
* A MariaDB or MySQL server

```sh
sudo apt-get install -y g++ libmysqlclient-dev
```

### Installation

1. Clone the repository
   ```sh
   git clone https://github.com/Elouahabi-Naoufal/student-management-system.git
   cd student-management-system
   ```

2. Create the database, user and tables
   ```sh
   sudo bash mariadb_installation.sh
   ```
   The script installs MariaDB, creates the user `poo`/`poo` on `localhost`, and creates the
   `school` database with the four tables.

3. Build the application
   ```sh
   g++ -std=c++17 Gestion_des_etudiants.cpp -o gestion_etudiants -lmysqlclient
   ```

4. Run it
   ```sh
   ./gestion_etudiants
   ```

The connection is opened in the `Database` constructor with hard-coded credentials
(`poo`/`poo`, database `school`, `localhost:3306`). If the connection fails the program prints
`Database connection failed!` and exits.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Usage

The main menu has six entries plus quit:

```
1. Gérer les Étudiants      4. Gérer les Notes
2. Gérer les Enseignants    5. Exporter/Importer
3. Gérer les Examens        6. Backup
                            0. Quitter
```

Each submenu is a nested loop; enter `0` at any prompt to return to the main menu.

| Section | Operations |
| --- | --- |
| Étudiants | add, list, search by name, show by ID, delete, update, average grade |
| Enseignants | add, list, search by name, delete, update |
| Examens | add, list, delete, update |
| Notes | add, list, delete, update |
| Export/Import | export students to `etudiants.csv`, import them back |
| Backup | write `enseignants.csv`, `examens.csv` or `notes.csv` |

Files are written to the current working directory. The import replays each CSV row as an
`INSERT`, so importing the same file twice will hit duplicate primary keys.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Roadmap

- [x] CRUD for students, teachers, exams and grades
- [x] Abstract `Personne` base class and inheritance for teachers/students
- [x] Search by name, lookup by ID and average grade
- [x] CSV export/import and per-table CSV backups
- [x] Typed console input validation
- [ ] Replace concatenated SQL with prepared statements
- [ ] Move connection settings out of `Gestion_des_etudiants.cpp`
- [ ] Add foreign keys for `notes` and a `groupes` table for `etudiants.id_groupe`
- [ ] Make student import idempotent
- [ ] Add a `Makefile` or `CMakeLists.txt`

See the [open issues](https://github.com/Elouahabi-Naoufal/student-management-system/issues) for a
full list of proposed features and known issues.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire,
and create. Any contributions you make are **greatly appreciated**.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Top contributors:

<a href="https://github.com/Elouahabi-Naoufal/student-management-system/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=Elouahabi-Naoufal/student-management-system" alt="contrib.rocks image" />
</a>

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## License

This project is not licensed — all rights reserved.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Contact

Naoufal Elouahabi - [LinkedIn](https://www.linkedin.com/in/elouahabi-naoufal/) - elouahabi-naoufal1@gmail.com

Project Link: [https://github.com/Elouahabi-Naoufal/student-management-system](https://github.com/Elouahabi-Naoufal/student-management-system)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Acknowledgments

* [MySQL C API documentation](https://dev.mysql.com/doc/c-api/8.0/en/)
* [MariaDB Knowledge Base](https://mariadb.com/kb/en/)
* [Best-README-Template](https://github.com/othneildrew/Best-README-Template)
* [Img Shields](https://shields.io)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

[contributors-shield]: https://img.shields.io/github/contributors/Elouahabi-Naoufal/student-management-system.svg?style=for-the-badge
[contributors-url]: https://github.com/Elouahabi-Naoufal/student-management-system/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/Elouahabi-Naoufal/student-management-system.svg?style=for-the-badge
[forks-url]: https://github.com/Elouahabi-Naoufal/student-management-system/network/members
[stars-shield]: https://img.shields.io/github/stars/Elouahabi-Naoufal/student-management-system.svg?style=for-the-badge
[stars-url]: https://github.com/Elouahabi-Naoufal/student-management-system/stargazers
[issues-shield]: https://img.shields.io/github/issues/Elouahabi-Naoufal/student-management-system.svg?style=for-the-badge
[issues-url]: https://github.com/Elouahabi-Naoufal/student-management-system/issues
[license-shield]: https://img.shields.io/badge/license-none-lightgrey?style=for-the-badge
[license-url]: #license
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/elouahabi-naoufal/
[C++.url]: https://img.shields.io/badge/C%2B%2B-00599C?style=for-the-badge&logo=c%2B%2B&logoColor=white
[C++]: https://isocpp.org/
[MySQL.url]: https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white
[MySQL]: https://www.mysql.com/
[MariaDB.url]: https://img.shields.io/badge/MariaDB-003545?style=for-the-badge&logo=mariadb&logoColor=white
[MariaDB]: https://mariadb.org/
[Bash.url]: https://img.shields.io/badge/Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white
[Bash]: https://www.gnu.org/software/bash/
