# 🎬 Flix API

![Django](https://img.shields.io/badge/Django-6.1-092E20?style=for-the-badge&logo=django&logoColor=white)
![DRF](https://img.shields.io/badge/DRF-3.18-A30000?style=for-the-badge&logo=django&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-SimpleJWT-000000?style=for-the-badge&logo=jsonwebtokens&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=for-the-badge&logo=python&logoColor=white)

API REST para catálogo de filmes: **CRUD completo** de atores, gêneros, filmes e
avaliações, com **autenticação JWT**, **permissões derivadas dos models** e um
endpoint de **estatísticas agregadas**.

---

## ✨ Destaques de implementação

**Permissão que acompanha o modelo de dados.**
Uma permissão customizada (`GlobalDefaultPermission`) traduz o método HTTP na
permissão de model do Django — `GET` → `view_<model>`, `POST` → `add_<model>`,
`PUT/PATCH` → `change_<model>`, `DELETE` → `delete_<model>`. O controle de acesso
é aplicado por padrão em todas as views e **um recurso novo não exige regra de
acesso nova**: basta criar as permissões do model no admin.

**Serializers por contexto.**
Nas leituras, o filme vem com **gênero e elenco aninhados** e a **média de
estrelas** calculada no banco (anotação com `Avg`); na escrita, é usado o
serializer do model — com validações próprias: data de lançamento não anterior a
1900 e resumo limitado a 500 caracteres.

**Endpoint de estatísticas.**
`GET /api/v1/movies/stats/` devolve total de filmes, distribuição por gênero,
total de avaliações e média de estrelas — tudo agregado pelo ORM em uma única
consulta de leitura.

**Importação em massa.**
Comando de management `import_actors <arquivo.csv>` para popular o elenco a
partir de um CSV.

---

## 🔐 Autenticação

JWT via `djangorestframework-simplejwt` (access token com validade de 1 dia):

| Método | Rota | Descrição |
|---|---|---|
| `POST` | `/api/v1/authentication/token/` | Obtém o par de tokens (access + refresh) |
| `POST` | `/api/v1/authentication/token/refresh` | Renova o access token |
| `POST` | `/api/v1/authentication/token/verify` | Valida um token |

Envie o token nas requisições autenticadas:

```http
Authorization: Bearer <access_token>
```

---

## 🔗 Endpoints

| Recurso | Rotas |
|---|---|
| **Atores** | `GET/POST /api/v1/actors/` · `GET/PUT/PATCH/DELETE /api/v1/actors/<id>/` |
| **Gêneros** | `GET/POST /api/v1/genres/` · `GET/PUT/PATCH/DELETE /api/v1/genres/<id>/` |
| **Filmes** | `GET/POST /api/v1/movies/` · `GET/PUT/PATCH/DELETE /api/v1/movies/<id>/` |
| **Avaliações** | `GET/POST /api/v1/reviews/` · `GET/PUT/PATCH/DELETE /api/v1/reviews/<id>/` |
| **Estatísticas** | `GET /api/v1/movies/stats/` |

---

## 🗂️ Modelo de dados

```text
Genre 1 ─── N Movie N ─── M Actor
                  │
                  1
                  │
                  N
               Review  (stars 0–5, comment)
```

- `Movie` — título, gênero, data de lançamento, elenco (M2M) e resumo
- `Actor` — nome, data de nascimento e nacionalidade
- `Review` — avaliação com estrelas e comentário, ligada a um filme

---

## 📁 Estrutura

```text
flix_api/
├── app/                 # configuração (settings, urls, permissions)
├── authentication/      # rotas de token JWT
├── actors/              # CRUD + comando de importação por CSV
├── genres/              # CRUD de gêneros
├── movies/              # CRUD + endpoint de estatísticas
├── reviews/             # CRUD de avaliações
├── manage.py
└── requirements.txt
```

---

## 🚀 Como rodar

```bash
git clone https://github.com/marcusvgcruz/flix_api.git
cd flix_api

python3 -m venv .venv
source .venv/bin/activate        # Windows: .\.venv\Scripts\Activate.ps1

pip install -r requirements.txt

python manage.py migrate
python manage.py createsuperuser   # cria o usuário para obter o token
python manage.py runserver
```

A API sobe em `http://127.0.0.1:8000/api/v1/`. O admin do Django fica em `/admin/`.

### Importar atores de um CSV

```bash
python manage.py import_actors caminho/para/atores.csv
```

### Qualidade de código

```bash
pip install -r requirements_dev.txt   # flake8
flake8
```

---

## 📄 Autor

**Marcus Cruz** — [GitHub](https://github.com/marcusvgcruz) · [LinkedIn](https://www.linkedin.com/in/marcusvgcruz)
