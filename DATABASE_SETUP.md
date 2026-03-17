# Database Setup Guide

This guide will help you configure the MySQL database for the investment news web scraping application.

## Prerequisites

1. MySQL server installed and running
2. Python dependencies installed: `pip install -r requirements.txt`

## Database Configuration

### 1. Create a Database User (Optional but Recommended)

```sql
CREATE USER 'news_user'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON investment_news.* TO 'news_user'@'localhost';
FLUSH PRIVILEGES;
```

### 2. Database Settings

The application uses fixed database configuration values. If you need to modify these settings, edit the `DB_CONFIG` dictionary in `utils/database.py`:

```python
DB_CONFIG = {
    'host': 'localhost',
    'port': 3306,
    'user': 'root',
    'password': 'root',  # Change this to your MySQL root password
    'database': 'investment_news',
    'charset': 'utf8mb4',
    'collation': 'utf8mb4_unicode_ci',
    'autocommit': True
}
```

**Important**: Update the `password` field to match your MySQL root password, or create a dedicated user as shown in step 1.

### 3. Run the Application

The application will automatically:
- Connect to MySQL
- Create the `investment_news` database if it does not exist
- Create all required tables with the proper schema
- Initialize the database connection

```bash
python app.py BUY_&_HOLD
```

## Database Schema

The application creates three main tables:

### 1. `tickers` - Stock symbols and company information
- `id`: Primary key
- `simbolo`: Stock symbol (e.g., PETR4, VALE3, AAPL)
- `nome_empresa`: Company name
- `setor`: Company sector
- `mercado`: Market type (B3, NYSE, NASDAQ, OTHERS)
- `ativo`: Active status
- `data_criacao`: Creation timestamp

### 2. `noticias` - News articles
- `id`: Primary key
- `ticker_id`: Foreign key to the `tickers` table
- `url`: Unique news URL
- `data_publicacao`: Publication date
- `autor`: Author
- `tipo_fonte`: Source type (EXAME, INFO_MONEY)
- `categoria`: News category
- `sentimento`: Sentiment analysis (POSITIVE, NEUTRAL, NEGATIVE)
- `relevancia`: Relevance score (0.00 to 1.00)
- `data_criacao`: Creation timestamp
- `data_atualizacao`: Last update timestamp

### 3. `processamento_texto` - Text processing data
- `id`: Primary key
- `noticia_id`: Foreign key to the `noticias` table
- `tipo_conteudo`: Content type (TITLE, HEADLINE, BODY)
- `texto_bruto`: Raw text content (with FULLTEXT index)
- `tokens_normalizados`: Normalized tokens (with FULLTEXT index)
- `tokens_stemming`: Stemmed tokens (LONGTEXT with FULLTEXT index)
- `tokens_lemma`: Lemmatized tokens (LONGTEXT with FULLTEXT index)
- `outros_dados`: Additional metadata (JSON)
- `data_criacao`: Creation timestamp
- `data_atualizacao`: Last update timestamp

## Troubleshooting

### Connection Issues
- Verify that the MySQL server is running
- Verify the database credentials
- Ensure the user has proper permissions
- Check firewall settings if connecting remotely

### Table Creation Issues
- Ensure the database user has `CREATE` privileges
- Check whether conflicting table names already exist
- Verify MySQL version compatibility (8.0+ recommended)

### Data Save Issues
- Check for duplicate URLs (they must be unique)
- Check foreign key constraints
- Verify JSON data format for text processing fields

## Testing the Database Connection

### Using Python
You can test the database connection by running:

```python
from src.financial_analysis.persistance.database import initialize_database

if initialize_database():
    print("Database connection successful!")
else:
    print("Database connection failed!")
```

### Using Command Line

#### Windows (PowerShell/Command Prompt)
```cmd
mysql -u root -p -h localhost
# Enter password when prompted
```

#### Linux/macOS
```bash
mysql -u root -p -h localhost
# Enter password when prompted
```

### Verify Database Creation
Once connected to MySQL, you can verify the database was created:

```sql
SHOW DATABASES;
USE investment_news;
SHOW TABLES;
```

You should see the `investment_news` database with three tables: `tickers`, `noticias`, and `processamento_texto`.