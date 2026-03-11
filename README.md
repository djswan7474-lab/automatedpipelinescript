import pandas as pd
import sqlite3
import logging

# Set up logging to track pipeline execution
logging.basicConfig(level=logging.INFO, format='%(levelname)s: %(message)s')

def run_etl_pipeline(input_file, db_name):
    try:
        # 1. EXTRACT: Load raw operational data
        logging.info("Extracting data...")
        df = pd.read_csv(input_file)
        
        # 2. TRANSFORM: Clean and format data
        logging.info("Transforming data...")
        # Example: Remove duplicates and fill missing values
        df = df.drop_duplicates()
        df['Sales'] = df['Sales'].fillna(0)
        # Convert date column to datetime objects
        df['Order_Date'] = pd.to_datetime(df['Order_Date'])
        
        # 3. LOAD: Push to SQLite database
        logging.info("Loading data into database...")
        conn = sqlite3.connect(db_name)
        df.to_sql('cleaned_sales_data', conn, if_exists='replace', index=False)
        conn.close()
        
        logging.info("Pipeline executed successfully.")
        
    except Exception as e:
        logging.error(f"Pipeline failed: {e}")

if __name__ == "__main__":
    run_etl_pipeline('raw_sales_data.csv', 'business_metrics.db')
