# Stock Market Webapp Microservices 📈 📉

## Introduction

Stock market applications are essential tools for investors and traders of all levels, from seasoned professionals to everyday users seeking to benefit from the market. This project demonstrates how to build a full-stack stock market application following a microservices architecture, focusing on scalability, reliability, and real-time features. The frontend connects to multiple backend microservices, offering a modern example suitable for a MERN (MongoDB, Express, React, Node.js) developer portfolio.

---

## Features

- **Stock Trading**: Users can place buy and sell orders for stocks.
- **Live Stock Price Reflection**: Real-time updates of stock prices.
- **Live Order Matching**: Orders are matched and executed in real-time.
- **Stock Line and Candlestick Graphs**: Visualization of stock price trends.
- **Stock Fundamentals and Information**: View comprehensive details for each stock.
- **Wishlist of Favorite Stocks**: Users can bookmark stocks for quick access.

---

## Backend Architecture Overview

![Backend Architecture Diagram](./assets/architecture-backend.png)

## Backend Services


### Design Goals

- **Scalable and Reliable**: The architecture is designed to handle growing complexity and user load.
- **Efficient Communication**: Uses message brokers for fast, decoupled service interaction.
- **Optimized Data Storage**: Employs both SQL and NoSQL databases for different types of data.
- **Fast Data Streaming**: Real-time updates to users via sockets.

---

## Tech Stack & Services

| Component      | Technology    | Purpose                                                                                   |
| -------------- | ------------- | ----------------------------------------------------------------------------------------- |
| **Frontend**   | React         | User interface (in this repo)                                                             |
| **Backend**    | Nest.js       | Main backend framework & microservices                                                    |
| **Node.js**    | Node.js       | Runtime platform for backend services                                                     |
| **Microservices** | Matching Engine | Dedicated service to match buy/sell orders (FIFO algorithm)                             |
| **Message Broker** | Redis      | Pub/Sub and message broker between main server and microservices                         |
| **Primary DB** | PostgreSQL    | Stores users, stocks, and most transactional data                                        |
| **Time Series DB** | TimescaleDB | Extension of PostgreSQL for storing executed trades with high throughput                 |
| **NoSQL DB**   | Cassandra     | High-availability storage for matching engine order data                                  |
| **Sockets**    | WebSockets    | Real-time data streaming to clients                                                       |
| **Cloud**      | AWS (EC2, RDS)| Cloud hosting for backend and managed databases                                           |
| **Containerization** | Docker  | Containerizes all backend services for consistent deployment                              |
| **Mono-repo**  | Nx / Lerna    | (Optional) For managing all backend services in a single codebase                        |

---

## Backend Diagram Explanation

```
+-----------+          +-----------------+         +-----------------+
|  Frontend | <------> |   Main Server   | <-----> | Matching Engine |
+-----------+          +-----------------+         +-----------------+
       |                       |                            |
       |      WebSockets       |                            |
       |<--------------------->|                            |
       |                       |                            |
       |        REST API       |                            |
       |<--------------------->|                            |
       |                       |                            |
       |         Redis Pub/Sub (Message Broker)             |
       |<--------------------->|<-------------------------->|
       |                       |                            |
       v                       v                            v
+-----------+       +-------------------+        +-------------------+
| PostgreSQL| <-->  |   TimescaleDB     |        |   Cassandra       |
+-----------+       +-------------------+        +-------------------+
       |                   |                           |
     (AWS RDS)           (AWS RDS)                 (Managed/EC2)
```

- **Frontend** connects to the main server for REST APIs and real-time socket updates.
- **Main Server** handles user authentication, order placement, and communicates with the matching engine.
- **Matching Engine** runs as a microservice, matches orders, and manages order books.
- **Redis** acts as a message broker for pub/sub communication between main server and matching engine.
- **PostgreSQL** stores user data, stocks, and transactions.
- **TimescaleDB** saves time-series data of executed trades for analytics and charting.
- **Cassandra** stores active and historical orders for fault tolerance.
- **AWS** provides cloud infrastructure; Docker ensures portability.

---

## Reasoning Behind Tech Choices

- **SQL as Primary DB**: Relational structure fits user, trade, and order relationships; enables complex queries.
- **TimescaleDB**: Seamless with PostgreSQL for time-series data (executed trades).
- **Redis**: Lightweight, fast, and supports both message brokering and pub/sub.
- **Cassandra**: Handles large volumes of active/historical order data with high availability.
- **Docker**: Standardizes deployment across environments.
- **AWS**: Managed services for scalability, reliability, and ease of use.

---

## Order Processing & Data Flow

1. **Order Placement**: User places an order via the frontend.
2. **Order Queuing**: Main server places the order in a Redis queue for the specific stock.
3. **Order Matching**: The matching engine consumes orders from Redis, persists to Cassandra, and matches using FIFO (First-In-First-Out).
4. **Order Execution**: Once matched, the engine publishes results via Redis pub/sub.
5. **Persistence**: The main server writes executed trade to TimescaleDB with timestamp.
6. **Real-time Updates**: Price and trade data are streamed to clients via sockets for graphs and live feeds.

---

## Matching Engine Logic (FIFO Example)

- Orders are processed in the order received.
- Example: A buy order for 500 shares at ₹50 is followed by a buy order for 200 shares at the same price. The 500-share order is matched first with available sell orders, then the 200-share order is processed.

---

## Frontend 

Frontend repository [Link]() contains the **React** frontend that communicates with the backend microservices described above. It manages user authentication, displays real-time stock data, charts, and provides interfaces for trading and managing watchlists.

---

## How to Use

1. **Clone this repo** for the frontend.
2. **Clone/setup backend services** as per the architecture above (see backend repo or relevant documentation).
3. Run all services using **Docker Compose** or deploy to cloud (AWS EC2/RDS recommended).
4. Start the frontend with `npm start` or `yarn start`.
5. Visit the app in your browser and sign up to start trading.

---

## Credits & References

- [Redis Pub/Sub](https://redis.io/docs/interact/pubsub/)
- [TimescaleDB Docs](https://docs.timescale.com/)
- [Cassandra Docs](https://cassandra.apache.org/doc/latest/)
- [Docker Documentation](https://docs.docker.com/)
- [AWS RDS](https://aws.amazon.com/rds/)

---

## Notes

- This project is a learning example; do not use for real trading or financial transactions.
- For detailed backend code, refer to the backend repository and code snippets.
