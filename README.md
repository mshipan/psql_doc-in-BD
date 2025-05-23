# PostgreSQL ডকুমেন্টেশন (বাংলা)

<details>
<summary><strong>1. psql এবং এর ডিফল্ট আচরণ, ডাটাবেস তৈরি করা</strong></summary>

### 🔶 psql কী?

psql হলো PostgreSQL-এর একটি ইন্টারেক্টিভ টার্মিনাল ক্লায়েন্ট, যেটি ব্যবহার করে আপনি ডাটাবেজে প্রশ্ন করতে (query) এবং কমান্ড চালাতে পারেন। এটি PostgreSQL সার্ভার থেকে সরাসরি সংযুক্ত হয়ে কাজ করে।

### 🔶 psql এর ডিফল্ট আচরণ:

যখন আপনি psql চালান, তখন এটি নিচের কাজগুলো করে:

1. ডিফল্ট ইউজার ও ডাটাবেজে কানেক্ট হয়:

- সাধারণত আপনার অপারেটিং সিস্টেমের ইউজারনেইম অনুযায়ী একই নামের ডাটাবেজে কানেক্ট হয়।
- উদাহরণ:

```bash
  $ psql
```

যদি আপনার ইউজারনেইম shipan হয়, তাহলে এটি shipan নামে ডাটাবেজে কানেক্ট হওয়ার চেষ্টা করবে।

2. ইন্টারেক্টিভ মোডে কাজ করে:

- আপনি সরাসরি SQL কমান্ড দিতে পারেন।
- প্রতিটি কমান্ড ; দিয়ে শেষ করতে হয়।

3. কিছু কমান্ড শুরু হয় \ দিয়ে:

- এগুলো psql-specific internal command, যেমন:
  - \l : সমস্ত ডাটাবেজের তালিকা
  - \c dbname : নির্দিষ্ট ডাটাবেজে কানেক্ট
  - \dt : টেবিল তালিকা
  - \q : psql থেকে বের হওয়া

### 🔷 PostgreSQL-এ ডাটাবেজ তৈরি

ডাটাবেজ তৈরি করা যায় দুইভাবে:

1. SQL কমান্ড ব্যবহার করে:

```sql
  CREATE DATABASE database_name;
```

2. psql ইন্টারফেসে createdb কমান্ড ব্যবহার করে:

```bash
  $ createdb company_db
```

দুই কমান্ড একই কাজ করে — পার্থক্য হলো, প্রথমটি SQL কমান্ড, দ্বিতীয়টি psql-এর CLI utility

### 🔷 একটি নির্দিষ্ট ডাটাবেজে কানেক্ট হওয়া:

```bash
  $ psql -d database_name
```

অথবা psql চালিয়ে, এর পরে \c কমান্ড:

```sql
  \c company_db
```

### 🔷 সংক্ষিপ্ত উদাহরণ:

```bash
  $ createdb testdb     # ডাটাবেজ তৈরি
  $ psql                # psql চালু
```

```sql
  \c testdb             -- ডাটাবেজে কানেক্ট
  CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100)
  );                    -- টেবিল তৈরি
```

### 🔎 টিপস:

| কমান্ড      | কাজ                  |
| ----------- | -------------------- |
| `\l`        | সমস্ত ডাটাবেজ দেখায়  |
| `\c dbname` | ডাটাবেজে কানেক্ট     |
| `\dt`       | টেবিল তালিকা দেখায়   |
| `\q`        | `psql` থেকে বের হওয়া |

</details>

<details>
<summary><strong>2. ইউজার, রোল ও প্রিভিলেজ ম্যানেজমেন্ট</strong></summary>

### 🔶 রোল (Role) কী?

PostgreSQL-এ Role বলতে ইউজার এবং গ্রুপ দুটোই বোঝায়। রোল ব্যবহার করে আপনি:

- ইউজার তৈরি করতে পারেন
- বিভিন্ন অধিকার দিতে পারেন
- গ্রুপ রোলের মতো ব্যবহার করে একাধিক ইউজারকে একই অধিকার দিতে পারেন

✅ Role = User = Group (PostgreSQL-এ এরা একই জিনিস)

### 🔷 রোল তৈরি করা (Creating Roles):

```sql
  CREATE ROLE role_name;
```

উদাহরণ:

```sql
  CREATE ROLE shipan;
```

এই রোল দিয়ে ডাটাবেজে লগইন করা যাবে না, কারণ এখনো LOGIN পারমিশন দেওয়া হয়নি।

### 🔷 লগইন রোল তৈরি করা (Login Privilege সহ):

```sql
  CREATE ROLE username WITH LOGIN PASSWORD 'your_password';
```

উদাহরণ:

```sql
  CREATE ROLE shipan WITH LOGIN PASSWORD '1234';
```

এখন shipan নামের এই রোল দিয়ে ডাটাবেজে লগইন করা যাবে।

### 🔷 SUPERUSER রোল তৈরি:

```sql
  CREATE ROLE admin WITH LOGIN SUPERUSER PASSWORD 'admin123';
```

এই রোলটি PostgreSQL-এর সর্বোচ্চ অনুমতিসম্পন্ন অ্যাডমিন রোল।

### 🔷 অধিকার (Privileges) কী কী থাকতে পারে?

| Privilege | কাজ                                  |
| --------- | ------------------------------------ |
| `SELECT`  | ডেটা দেখতে পারে                      |
| `INSERT`  | ডেটা যোগ করতে পারে                   |
| `UPDATE`  | ডেটা আপডেট করতে পারে                 |
| `DELETE`  | ডেটা মুছতে পারে                      |
| `CREATE`  | টেবিল, স্কিমা ইত্যাদি তৈরি করতে পারে |
| `CONNECT` | ডাটাবেজে সংযুক্ত হতে পারে            |

### 🔷 রোলকে একটি ডাটাবেজে অ্যাক্সেস দেওয়ার জন্য:

```sql
  GRANT CONNECT ON DATABASE your_db TO role_name;
```

উদাহরণ:

```sql
  GRANT CONNECT ON DATABASE company_db TO shipan;
```

### 🔷 রোলকে নির্দিষ্ট টেবিলে SELECT বা অন্যান্য অধিকার দেওয়া:

```sql
  GRANT SELECT, INSERT ON TABLE table_name TO role_name;
```

উদাহরণ:

```sql
  GRANT SELECT, INSERT ON TABLE employees TO shipan;
```

### 🔷 রোলের প্রপার্টি পরিবর্তন (e.g., PASSWORD, LOGIN):

```sql
  ALTER ROLE role_name WITH PASSWORD 'new_password';
  ALTER ROLE role_name WITH LOGIN;
```

### 🔷 রোল মুছে ফেলা:

```sql
  DROP ROLE role_name;
```

উদাহরণ:

```sql
  DROP ROLE shipan;
```

⚠️ যদি রোলটি কোনো ডাটাবেজ বা টেবিলের মালিক হয়, আগে সেই মালিকানা পরিবর্তন করতে হবে।

### 🔷 একজন রোলকে আরেকটি রোলের মেম্বার বানানো (Role Inheritance):

```sql
  GRANT parent_role TO child_role;
```

উদাহরণ:

```sql
  GRANT developers TO shipan;
```

এখন shipan রোলটি developers রোলের সব অধিকার পাবে।

### 🧠 সংক্ষেপে মনে রাখার মতো:

| কাজ           | কমান্ড                                         |
| ------------- | ---------------------------------------------- |
| রোল তৈরি      | `CREATE ROLE name;`                            |
| লগইন রোল      | `CREATE ROLE name WITH LOGIN PASSWORD 'pass';` |
| অধিকার দেওয়া  | `GRANT SELECT ON table TO role;`               |
| অধিকার বাতিল  | `REVOKE SELECT ON table FROM role;`            |
| রোল মুছে ফেলা | `DROP ROLE name;`                              |

</details>

<details>
<summary><strong>3. প্রিভিলেজ দেওয়া এবং তুলে নেওয়া</strong></summary>

PostgreSQL-এ ডাটাবেজ নিরাপত্তার একটি গুরুত্বপূর্ণ দিক হচ্ছে privilege (অধিকার) নিয়ন্ত্রণ। কে কোন টেবিল, ভিউ, বা ডাটাবেজ অ্যাক্সেস করতে পারবে, সেটি নির্ধারণ করা হয় GRANT ও REVOKE কমান্ডের মাধ্যমে।

### 🔶 Privilege (অধিকার) কী?

Privilege হলো একটি অনুমতি, যা PostgreSQL-এর নির্দিষ্ট resource-এর (যেমন টেবিল, ডাটাবেজ, স্কিমা) উপর কাউকে কিছু কাজ করার অনুমতি দেয়।

### 🔷 PostgreSQL-এ কিছু সাধারণ Privileges:

| Privilege    | অর্থ                                              |
| ------------ | ------------------------------------------------- |
| `SELECT`     | টেবিল বা ভিউ থেকে তথ্য পড়তে পারবে                 |
| `INSERT`     | টেবিলে নতুন তথ্য যোগ করতে পারবে                   |
| `UPDATE`     | টেবিলের তথ্য পরিবর্তন করতে পারবে                  |
| `DELETE`     | টেবিল থেকে তথ্য মুছতে পারবে                       |
| `TRUNCATE`   | টেবিল খালি করতে পারবে                             |
| `REFERENCES` | অন্য টেবিলে foreign key হিসেবে ব্যবহার করতে পারবে |
| `USAGE`      | স্কিমা বা সিকোয়েন্স ব্যবহার করতে পারবে            |
| `CONNECT`    | ডাটাবেজে সংযুক্ত হতে পারবে                        |
| `CREATE`     | নতুন টেবিল, স্কিমা, ফাংশন ইত্যাদি তৈরি করতে পারবে |

1. GRANT – Privilege দেওয়া

```sql
  GRANT privilege_type ON object TO role_name;
```

উদাহরণ ১: টেবিলের ওপর SELECT ও INSERT অনুমতি দেওয়া

```sql
  GRANT SELECT, INSERT ON TABLE employees TO shipan;
```

উদাহরণ ২: ডাটাবেজে সংযোগের অনুমতি দেওয়া

```sql
  GRANT CONNECT ON DATABASE company_db TO shipan;
```

উদাহরণ ৩: স্কিমায় টেবিল তৈরি করার অনুমতি

```sql
  GRANT USAGE, CREATE ON SCHEMA public TO shipan;
```

2. REVOKE – Privilege প্রত্যাহার বা বাতিল করা

```sql
  REVOKE privilege_type ON object FROM role_name;
```

উদাহরণ ১: টেবিলের SELECT ও INSERT অনুমতি বাতিল করা

```sql
  REVOKE SELECT, INSERT ON TABLE employees FROM shipan;
```

উদাহরণ ২: ডাটাবেজে সংযোগের অনুমতি বাতিল করা

```sql
  REVOKE CONNECT ON DATABASE company_db FROM shipan;
```

উদাহরণ ৩: স্কিমায় CREATE করার অনুমতি বাতিল করা

```sql
  REVOKE CREATE ON SCHEMA public FROM shipan;
```

### 🔷 GRANT OPTION সহ – অন্যকে অনুমতি দেওয়ার ক্ষমতা

```sql
  GRANT SELECT ON employees TO shipan WITH GRANT OPTION;
```

এখন shipan নিজেও অন্য কাউকে SELECT অনুমতি দিতে পারবে।

### 🧠 টিপস:

- PostgreSQL-এ ডিফল্টভাবে কেবল টেবিলের মালিকই সকল প্রিভিলেজ পায়।
- অন্যান্য ইউজারকে টেবিল বা ডাটাবেজে কাজ করতে দিতে চাইলে স্পষ্টভাবে GRANT করতে হয়।

### 📌 কোন Object-এর ওপর GRANT/REVOKE দেওয়া যায়?

| Object   | Privileges                                           |
| -------- | ---------------------------------------------------- |
| DATABASE | CONNECT, TEMP                                        |
| TABLE    | SELECT, INSERT, UPDATE, DELETE, TRUNCATE, REFERENCES |
| SCHEMA   | USAGE, CREATE                                        |
| SEQUENCE | USAGE, SELECT, UPDATE                                |
| FUNCTION | EXECUTE                                              |
| COLUMN   | SELECT, INSERT, UPDATE                               |

### 🔍 চেক করতে চান কে কোন Privilege পেয়েছে?

```sql
  \z table_name
```

বা

```sql
  SELECT grantee, privilege_type
  FROM information_schema.role_table_grants
  WHERE table_name = 'employees';
```

</details>

<details>
<summary><strong>4. Structured Query Language (SQL)</strong></summary>

Structured Query Language (SQL) হলো একটি ডোমেইন-স্পেসিফিক ভাষা যা ডেটাবেজ ম্যানেজমেন্ট সিস্টেমে ডেটা তৈরি, পড়া, আপডেট, এবং মুছে ফেলার (CRUD) জন্য ব্যবহৃত হয়। PostgreSQL-এ SQL ব্যবহার করে আপনি ডেটা ও ডেটাবেজ স্ট্রাকচার নিয়ন্ত্রণ করতে পারেন।

### 🔹 SQL কীভাবে কাজ করে?

SQL কমান্ডগুলো ৫টি প্রধান ভাগে বিভক্ত:

#### 1. Data Query Language (DQL)

- ডেটা query করতে ব্যবহৃত হয়।
  | কমান্ড | কাজ |
  | -------- | ----------------- |
  | `SELECT` | ডেটা পড়তে ব্যবহৃত |

```sql
  SELECT * FROM employees;
  SELECT name, email FROM users;
```

#### 2. Data Definition Language (DDL)

- ডেটাবেজ অবজেক্ট (টেবিল, স্কিমা ইত্যাদি) তৈরি, পরিবর্তন, মুছে ফেলা হয়।
  | কমান্ড | কাজ |
  | -------- | -------------------------- |
  | `CREATE` | নতুন টেবিল/ডেটাবেজ তৈরি |
  | `ALTER` | টেবিল বা কলামে পরিবর্তন |
  | `DROP` | টেবিল বা ডেটাবেজ মুছে ফেলা |

```sql
  CREATE TABLE students (
  id SERIAL PRIMARY KEY,
  name TEXT,
  age INT
  );

  ALTER TABLE students ADD COLUMN email TEXT;
  DROP TABLE students;
```

#### 3. Data Manipulation Language (DML)

- ডেটাবেজে থাকা ডেটা পরিবর্তন করতে ব্যবহৃত হয়।
  | কমান্ড | কাজ |
  | -------- | -------------- |
  | `INSERT` | ডেটা ঢোকানো |
  | `UPDATE` | ডেটা আপডেট |
  | `DELETE` | ডেটা মুছে ফেলা |

```sql
  INSERT INTO students (name, age) VALUES ('Shipan', 28);

  UPDATE students SET age = 29 WHERE name = 'Shipan';

  DELETE FROM students WHERE name = 'Shipan';
```

#### 4. Data Control Language (DCL)

- ডেটার অ্যাক্সেস কন্ট্রোল (গ্রান্ট/রিভোক) করতে ব্যবহৃত হয়।
  | কমান্ড | কাজ |
  | -------- | --------------------------- |
  | `GRANT` | কাউকে ডেটা অ্যাক্সেস দেওয়া |
  | `REVOKE` | ডেটা অ্যাক্সেস বাতিল করা |

```sql
  GRANT SELECT ON students TO shipan;
  REVOKE SELECT ON students FROM shipan;
```

#### 5. Transaction Control Language (TCL)

- একাধিক SQL অপারেশন একত্রে চালানোর নিয়ন্ত্রণ।
  | কমান্ড | কাজ |
  | ---------- | ---------------------------------- |
  | `BEGIN` | ট্রানজ্যাকশন শুরু |
  | `COMMIT` | সফলভাবে শেষ করা |
  | `ROLLBACK` | ব্যর্থ হলে আগের অবস্থায় ফিরে যাওয়া |

```sql
  BEGIN;
  UPDATE accounts SET balance = balance - 500 WHERE id = 1;
  UPDATE accounts SET balance = balance + 500 WHERE id = 2;
  COMMIT;
```

### PostgreSQL-এ SQL ব্যবহার করার উদাহরণ:

#### 1. নতুন টেবিল তৈরি

```sql
  CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  price NUMERIC CHECK (price > 0)
  );
```

#### 2. ডেটা ঢোকানো

```sql
  INSERT INTO products (name, price) VALUES ('Keyboard', 700.00);
```

#### 3. ডেটা পড়া

```sql
  SELECT name, price FROM products WHERE price > 500;
```

#### 4. ডেটা আপডেট

```sql
  UPDATE products SET price = 650.00 WHERE name = 'Keyboard';
```

#### 5. ডেটা মুছে ফেলা

```sql
  DELETE FROM products WHERE name = 'Keyboard';
```

### 📍 SQL লেখার কিছু নিয়ম:

- Case-insensitive: SQL কমান্ড বড় হাতের বা ছোট হাতের হতে পারে (ভাল অভ্যাস বড় হাতের লেখা `SELECT`, `FROM` ইত্যাদি)।
- Statement শেষে `;` ব্যবহার করা বাধ্যতামূলক।
- Comments: `--` এক লাইনের কমেন্ট, `/* */` মাল্টিলাইন।

```sql
  -- এটি একটি কমেন্ট
  SELECT * FROM users;  -- সব ইউজার দেখাবে
```

### 🧠 স্মার্ট টিপস:

- ✅ SQL শিখতে হলে SELECT দিয়ে শুরু করুন।
- ✅ সবসময় নতুন টেবিল/কমান্ড আগে test ডেটাবেজে পরীক্ষা করে দেখুন।
- ✅ ভুল হলে ROLLBACK দিয়ে ফেরত যান, তবে সেটা BEGIN-এর মধ্যে হতে হবে।

</details>

<details>
<summary><strong>5. ডাটাবেস এবং ডেটা টাইপ তৈরি, আপডেট, ডিলিট</strong></summary>

PostgreSQL-এ আপনি খুব সহজেই নতুন ডেটাবেজ তৈরি করতে, বিদ্যমান ডেটাবেজে পরিবর্তন আনতে এবং তা মুছে ফেলতে পারেন। এছাড়াও PostgreSQL বিভিন্ন ধরনের ডেটা টাইপ সাপোর্ট করে, যেগুলোর সঠিক ব্যবহার আপনার টেবিল ডিজাইনকে অনেক উন্নত করে।

### 🔶 1. ডেটাবেজ তৈরি করা (Create Database)

```sql
  CREATE DATABASE database_name;
```

উদাহরণ:

```sql
  CREATE DATABASE company;
```

👉 এটি company নামে একটি নতুন ডেটাবেজ তৈরি করবে।

⚠️ মনে রাখবেন:

- একটি নতুন ডেটাবেজ তৈরির জন্য আপনাকে সুপারইউজার অথবা CREATEDB রোল থাকতে হবে।
- একই নামের দুটি ডেটাবেজ একসাথে থাকতে পারে না।

### 🔶 2. ডেটাবেজে প্রবেশ করা (Connect to Database)

```bash
  \c database_name
```

উদাহরণ:

```sql
  \c company
```

👉 এটি company ডেটাবেজে স্যুইচ করবে।

### 🔶 3. ডেটাবেজ মুছে ফেলা (Drop Database)

```sql
  DROP DATABASE database_name;
```

উদাহরণ:

```sql
  DROP DATABASE company;
```

👉 এটি company নামের ডেটাবেজটি ডিলিট করে দেবে।

⚠️ সতর্কতা: DROP DATABASE করলে সেই ডেটাবেজের সব টেবিল ও ডেটা চিরতরে হারিয়ে যাবে।

### 🔶 4. ডেটাবেজ আপডেট (রিনেমিং) করা

```sql
  ALTER DATABASE old_name RENAME TO new_name;
```

উদাহরণ:

```sql
  ALTER DATABASE company RENAME TO company_db;
```

👉 এটি company ডেটাবেজের নাম পরিবর্তন করে company_db করবে।

### PostgreSQL এর গুরুত্বপূর্ণ ডেটা টাইপগুলো

PostgreSQL অনেকগুলো বিল্ট-ইন ডেটা টাইপ সাপোর্ট করে। নিচে সবচেয়ে বেশি ব্যবহৃত ডেটা টাইপগুলো তুলে ধরা হলো:

| টাইপ ক্যাটাগরি | ডেটা টাইপ                             | ব্যাখ্যা                     |
| -------------- | ------------------------------------- | ---------------------------- |
| সংখ্যা         | `INTEGER`, `SMALLINT`, `BIGINT`       | পূর্ণসংখ্যা রাখে             |
| দশমিক সংখ্যা   | `NUMERIC`, `REAL`, `DOUBLE PRECISION` | ভগ্নাংশের মান সহ             |
| টেক্সট         | `CHAR`, `VARCHAR`, `TEXT`             | স্ট্রিং ডেটা রাখে            |
| বুলিয়ান        | `BOOLEAN`                             | `TRUE` বা `FALSE` মান        |
| তারিখ ও সময়    | `DATE`, `TIME`, `TIMESTAMP`           | তারিখ ও সময় সংক্রান্ত        |
| UUID           | `UUID`                                | ইউনিক আইডেন্টিফায়ার          |
| JSON           | `JSON`, `JSONB`                       | জাভাস্ক্রিপ্ট অবজেক্ট নোটেশন |
| অ্যারে         | `TEXT[]`, `INT[]`                     | একাধিক মানের তালিকা          |

### 🔸 উদাহরণ সহ ডেটা টাইপ ব্যবহার:

```sql
  CREATE TABLE employees (
   id SERIAL PRIMARY KEY,
   name VARCHAR(100),
   age INTEGER,
   salary NUMERIC(10, 2),
   is_active BOOLEAN DEFAULT TRUE,
   joined_at DATE
  );

```

বুঝিয়ে বললে:

- `SERIAL`: অটো ইনক্রিমেন্ট পূর্ণসংখ্যা
- `VARCHAR(100)`: সর্বোচ্চ ১০০ ক্যারেক্টার স্ট্রিং
- `NUMERIC(10, 2)`: সর্বোচ্চ ১০ ডিজিট, যার মধ্যে ২টি দশমিকের পর
- `BOOLEAN`: `TRUE` বা `FALSE`
- `DATE`: শুধু তারিখ (YYYY-MM-DD)

### ✅ কিছু কাজের SQL কমান্ড সারাংশ:

| কাজ           | কমান্ড                                        |
| ------------- | --------------------------------------------- |
| ডেটাবেজ তৈরি  | `CREATE DATABASE db_name;`                    |
| ডেটাবেজ রিনেম | `ALTER DATABASE old_name RENAME TO new_name;` |
| ডেটাবেজ মুছুন | `DROP DATABASE db_name;`                      |
| ডেটাবেজে ঢোকা | `\c db_name`                                  |
| ডেটা টাইপ চেক | `\d+ table_name`                              |

### 📌 ব্যবহারিক টিপস:

- ✅ ডেটাবেজ নাম ও টেবিল নাম সবসময় ছোট হাতের এবং আন্ডারস্কোর (\_) দিয়ে লেখা ভালো।
- ✅ SERIAL ব্যবহার করলে PostgreSQL নিজে থেকেই সিকোয়েন্স তৈরি করে।
- ✅ NUMERIC ব্যবহার করা হয় আর্থিক মানের ক্ষেত্রে যাতে সঠিক ভগ্নাংশ রাখা যায়।
- ✅ BOOLEAN ডিফল্ট ভাবে TRUE/FALSE ধরে।
</details>

<details>
<summary><strong>6. একাধিক কলাম ও বিভিন্ন ডেটা টাইপ সহ টেবিল তৈরি</strong></summary>

PostgreSQL-এ টেবিল তৈরি করার সময় আপনি একাধিক কলাম যুক্ত করতে পারেন এবং প্রতিটি কলামে আলাদা আলাদা ডেটা টাইপ ব্যবহার করতে পারেন। এর মাধ্যমে আপনি ডেটাকে আরও সংগঠিত ও অর্থবহভাবে সংরক্ষণ করতে পারবেন।

### 🔶 1. টেবিল তৈরির মৌলিক গঠন

```sql
  CREATE TABLE table_name (
    column1 data_type,
    column2 data_type,
  ...
  );
```

উদাহরণ:

```sql
  CREATE TABLE employees (
    id SERIAL,
    name VARCHAR(100),
    age INTEGER,
    salary NUMERIC(10, 2),
    is_active BOOLEAN,
    join_date DATE
  );
```

### 🔍 ব্যাখ্যা:

| কলাম        | ডেটা টাইপ        | ব্যাখ্যা                                                        |
| ----------- | ---------------- | --------------------------------------------------------------- |
| `id`        | `SERIAL`         | অটো ইনক্রিমেন্ট পূর্ণসংখ্যা (Primary key হিসেবে ব্যবহার করা হয়) |
| `name`      | `VARCHAR(100)`   | সর্বোচ্চ ১০০ ক্যারেক্টার স্ট্রিং                                |
| `age`       | `INTEGER`        | বয়সের মতো পূর্ণসংখ্যার মান                                     |
| `salary`    | `NUMERIC(10, 2)` | আর্থিক মান (মোট ১০ ডিজিট, দশমিকের পর ২টি)                       |
| `is_active` | `BOOLEAN`        | TRUE অথবা FALSE মান                                             |
| `join_date` | `DATE`           | শুধুমাত্র তারিখ                                                 |

### 🔶 2. অন্যান্য সাধারণ ডেটা টাইপ

PostgreSQL অনেক বিল্ট-ইন ডেটা টাইপ সাপোর্ট করে:

| ডেটা টাইপ       | ব্যাখ্যা                                  |
| --------------- | ----------------------------------------- |
| `TEXT`          | আনলিমিটেড দৈর্ঘ্যের স্ট্রিং               |
| `CHAR(n)`       | নির্দিষ্ট দৈর্ঘ্যের ফিক্সড স্ট্রিং        |
| `TIMESTAMP`     | তারিখ ও সময় একসাথে                        |
| `TIME`          | শুধুমাত্র সময়                             |
| `UUID`          | ইউনিক আইডেন্টিফায়ার                       |
| `JSON`, `JSONB` | জাভাস্ক্রিপ্ট অবজেক্ট নোটেশন              |
| `ARRAY`         | একাধিক ভ্যালু রাখার জন্য (যেমন: `TEXT[]`) |

### 🔶 3. মাল্টি-টাইপ কলামের আরেকটি উদাহরণ

```sql
  CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    price NUMERIC(8, 2) CHECK (price > 0),
    in_stock BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    tags TEXT[]
  );
```

⚙️ এখানে কী হচ্ছে?

- `product_id`: অটো ইনক্রিমেন্ট + প্রাইমারি কী
- `name`: অবশ্যই থাকতে হবে (`NOT NULL`)
- `price`: শূন্যের চেয়ে বড় হতে হবে (`CHECK`)
- `in_stock`: ডিফল্ট মান `TRUE`
- `created_at`: টেবিলে ইনসার্ট করার সময়কার টাইমস্ট্যাম্প
- `tags`: স্ট্রিং অ্যারে (যেমন: `['electronics', 'home']`)

### 🔶 4. টেবিল তৈরির পর চেক করতে চাইলে:

```bash
  \d+ table_name
```

উদাহরণ:

```bash
  \d+ employees
```

👉 এটি টেবিলের সব কলাম, ডেটা টাইপ, কনস্ট্রেইন্ট এবং স্টোরেজ তথ্য দেখাবে।

### 📌 টিপস:

- টেবিল তৈরি করার সময় ডেটা টাইপ সঠিকভাবে নির্ধারণ করলে পরবর্তী ডেটা ম্যানেজমেন্ট অনেক সহজ হয়।
- `NUMERIC` বা `DECIMAL` ব্যবহার করুন যদি আপনি এক্সাক্ট ভ্যালু চান (অর্থনৈতিক হিসাবের জন্য গুরুত্বপূর্ণ)।
- `TEXT` এবং `VARCHAR` এর পারফরম্যান্স প্রায় একই — তবে `VARCHAR(n)` দিলে দৈর্ঘ্যের সীমা নির্ধারিত হয়।
</details>
