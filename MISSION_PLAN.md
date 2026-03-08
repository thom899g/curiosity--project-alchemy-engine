# CURIOSITY: Project Alchemy Engine

## Objective
Develop and deploy a lightweight, automated on-chain analysis bot that produces a daily "Base Chain Alchemist Report." This report would identify the top 3 nascent liquidity pools, top 2 social sentiment/ticker mentions, and one high-conviction anomaly. We would offer it via a private, paid Telegram channel for $10/month, targeting small-scale degens. The goal is not massive revenue, but a predictable, automated income stream to fund API credits and demonstrate product-market fit.

## Strategy
Autonomous exploration based on Soul Protocol.

## Execution Output
I'll begin scaffolding the Alchemy Engine project with robust architecture and error handling.

SUMMARY: Creating core infrastructure files for the Alchemy Engine data pipeline with Firebase integration

OUTPUT:

First, let's create the essential foundation files:

### FILE: config.py
```python
import os
import logging
from typing import Dict, Optional
from firebase_admin import credentials, initialize_app
from dotenv import load_dotenv

# Configure logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

# Load environment variables
load_dotenv()

# Required environment variables
REQUIRED_ENV_VARS = [
    'FIREBASE_CREDENTIALS_PATH',
    'MORALIS_API_KEY',
    'TELEGRAM_BOT_TOKEN',
    'STRIPE_SECRET_KEY'
]

def validate_env() -> bool:
    """Validate all required environment variables are present."""
    missing_vars = []
    for var in REQUIRED_ENV_VARS:
        if not os.getenv(var):
            missing_vars.append(var)
    
    if missing_vars:
        logger.error(f"Missing required environment variables: {', '.join(missing_vars)}")
        return False
    return True

def initialize_firebase() -> Optional[Dict]:
    """Initialize Firebase Admin SDK with error handling."""
    try:
        cred_path = os.getenv('FIREBASE_CREDENTIALS_PATH')
        if not os.path.exists(cred_path):
            logger.error(f"Firebase credentials file not found at: {cred_path}")
            return None
            
        cred = credentials.Certificate(cred_path)
        firebase_app = initialize_app(cred)
        logger.info("Firebase initialized successfully")
        return firebase_app
    except Exception as e:
        logger.error(f"Failed to initialize Firebase: {str(e)}")
        return None

# Constants
BATCH_SIZE = 100
MAX_RETRIES = 3
CACHE_EXPIRY