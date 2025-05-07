# Liquibase Migration

## Giới thiệu

Dự án này sử dụng [Liquibase](https://www.liquibase.org/) để quản lý và theo dõi các thay đổi của cơ sở dữ liệu.
Liquibase giúp kiểm soát schema và dữ liệu giữa các môi trường một cách nhất quán.

## Cấu trúc dự án

```
liquibase-migration-main/
├── build.gradle
├── settings.gradle
├── gradle/
├── modules/
│   ├── pet/
│   └── user/
├── .idea/
├── gradlew
├── gradlew.bat
└── readme.md
```

## Cấu trúc 1 service project

Mỗi module dịch vụ nằm trong `modules/` gồm các phần sau:

```
modules/{service_name}/
├── changelog/                         # Chứa các file thay đổi (changeSets)
│   ├── changelog-1-create-xxx-table.xml
│   └── db.changelog-master.xml        # File gốc import các changelog khác
└── config/
    ├── postgresql/liquibase.properties
    ├── oracle/liquibase.properties
    └── mssql/liquibase.properties
```

## Thêm một service project mới

Để thêm một module service mới, ví dụ `order`, làm theo các bước:

1. **Tạo thư mục module:**
   ```bash
   mkdir -p modules/order/changelog
   mkdir -p modules/order/config/{postgresql,oracle,mssql}
   ```

2. **Tạo file changelog master:**
   `modules/order/changelog/db.changelog-master.xml`
   ```xml
   <databaseChangeLog
       xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
       http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.8.xsd">

       <include file="changelog-1-create-order-table.xml"/>
   </databaseChangeLog>
   ```

3. **Tạo `liquibase.properties` cho từng loại DB:**

    - PostgreSQL (`modules/order/config/postgresql/liquibase.properties`)
      ```properties
      changeLogFile=modules/order/changelog/db.changelog-master.xml
      url=jdbc:postgresql://localhost:5432/yourdb
      username=youruser
      password=yourpassword
      driver=org.postgresql.Driver
      logLevel=info
      ```

    - Oracle (`modules/order/config/oracle/liquibase.properties`)
      ```properties
      changeLogFile=modules/order/changelog/db.changelog-master.xml
      url=jdbc:oracle:thin:@localhost:1521:xe
      username=youruser
      password=yourpassword
      driver=oracle.jdbc.OracleDriver
      logLevel=info
      ```

    - SQL Server (`modules/order/config/mssql/liquibase.properties`)
      ```properties
      changeLogFile=modules/order/changelog/db.changelog-master.xml
      url=jdbc:sqlserver://localhost:1433;databaseName=yourdb
      username=youruser
      password=yourpassword
      driver=com.microsoft.sqlserver.jdbc.SQLServerDriver
      logLevel=info
      ```

4. **Chạy Liquibase:**
   ```bash
   ./gradlew update -PconfigFile=modules/order/config/postgresql/liquibase.properties
   ```

## Chạy Liquibase cho module hiện tại

Ví dụ, để chạy Liquibase cho module `pet` với PostgreSQL:

```bash
./gradlew update -PconfigFile=modules/pet/config/postgresql/liquibase.properties
```

Tương tự áp dụng với `oracle` hoặc `mssql`.
# liquibase-migration
