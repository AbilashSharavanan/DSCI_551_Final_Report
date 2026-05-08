# TiDB Global E-commerce Workload

This repository contains the DSCI 551 project implementation and final report for a TiDB-based global e-commerce workload. The project studies how application operations map to TiDB internals such as regions, Raft replication, MVCC, and two-phase commit.

## Repository Contents

- `DSCI_551_Project.ipynb`: Main notebook containing schema creation, synthetic data generation, core workload operations, UI code, and load-generation logic.
- `final_report.tex`: Final report in LaTeX format with the same style as the midterm report.
- `final_report.pdf`: Compiled final report.
- `README.md`: Setup and usage instructions.

## Project Overview

The application models a compact global e-commerce system with five tables:

- `users`
- `products`
- `inventory`
- `orders`
- `order_items`

The project is designed to explore how TiDB's distributed storage and execution model behaves under a mix of transactional and analytical workloads. The final report discusses how schema design, key ordering, and access patterns affect region locality, replication behavior, and query execution.

## Environment Setup

### Requirements

- Windows 10/11 with WSL enabled, or a Linux/macOS environment.
- Python 3.10 or later.
- A running TiDB playground or TiDB cluster accessible from your machine.

## TiDB Playground Setup on Windows

If you are setting up the TiDB playground on Windows, open PowerShell and run the following commands:

```powershell
wsl --install
wsl
curl --proto '=https' --tlsv1.2 -sSf https://tiup-mirrors.pingcap.com/install.sh | sh
source ~/.bashrc
tiup playground
```

### Notes for Windows

- `wsl --install` installs WSL and Ubuntu on supported Windows versions.
- You may need to restart your machine after installing WSL.
- The TiUP installer is run inside the WSL shell.
- After installation, reload the shell environment with `source ~/.bashrc`.
- Start the local TiDB playground with `tiup playground`.

## TiDB Playground Setup on Mac

If you are using macOS, you do not need WSL. Open Terminal and run:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install curl
curl --proto '=https' --tlsv1.2 -sSf https://tiup-mirrors.pingcap.com/install.sh | sh
source ~/.bashrc
tiup playground
```

### Notes for Mac

- If Homebrew is already installed, you can skip the installation command.
- Some macOS shells use `~/.zshrc` instead of `~/.bashrc`.
- If `source ~/.bashrc` does not work, run:

```bash
source ~/.zshrc
```

- Start the local TiDB playground with `tiup playground`.

## Dependencies

Install the Python packages used by the notebook:

```bash
pip install pandas sqlalchemy pymysql notebook jupyter
```

If you plan to use the Tkinter UI, make sure Tkinter is available in your Python installation.

## Database Connection

The notebook connects to TiDB using SQLAlchemy. The expected connection string in the notebook is similar to:

```python
mysql+pymysql://root@127.0.0.1:4000/tidbecommerce
```

The workflow first connects without a database name, creates the database if needed, and then connects to the `tidbecommerce` schema.

## Setup and Initialization

1. Start TiDB using `tiup playground`.
2. Open `DSCI_551_Project.ipynb`.
3. Run the schema creation cells.
4. Verify that the tables were created successfully.
5. Run the data-generation cells to populate the database.
6. Execute the workload and UI cells as needed.

## Schema Summary

| Table | Purpose |
|---|---|
| `users` | Stores customer identity and region information. |
| `products` | Stores product catalog data and prices. |
| `inventory` | Stores product stock by location and region. |
| `orders` | Stores order headers and transactional metadata. |
| `order_items` | Stores order line items. |

The `orders` table is designed to support locality analysis through a composite key strategy and a secondary index for user-history lookups.

## Data Generation

The notebook generates synthetic data rather than depending on an external dataset. The current workload includes:

- 1,000 users
- 500 products
- 2,001 inventory records
- 3,000 orders
- 9,057 order items

This synthetic pipeline makes the project easy to reproduce and avoids the need to distribute a separate large dataset.

## Core Operations

The application implements four main operations:

1. **Place Order**: Inserts an order header, order items, and inventory updates in a single transaction.
2. **User Order History**: Retrieves recent orders for a user and computes totals.
3. **Regional Inventory Check**: Lists low-stock products for a selected region.
4. **Sales Summary**: Aggregates daily revenue by region.

These operations are used in the final report to explain how SQL statements map to TiDB internals such as region scans, coprocessor pushdown, Raft replication, and two-phase commit.

## Running the Notebook

Run the notebook top to bottom for the cleanest setup:

1. Create the database and tables.
2. Generate and insert users, products, inventory, orders, and order items.
3. Run the validation and sample query cells.
4. Use the workload functions to test order placement and lookup behavior.
5. Optionally launch the Tkinter app from the notebook to interact with the database through a simple UI.

## Running the Tkinter Application

The notebook includes a Tkinter-based warehouse dashboard with tabs for order placement, inventory updates, inventory checks, sales summaries, and user order history. To use it, make sure the TiDB cluster is running and the database has already been populated.

## Reproducibility Notes

For best results:

- Start with a fresh TiDB playground instance.
- Run schema creation before loading any data.
- Populate users and products before generating orders and order items.
- Keep the same region codes and table definitions when repeating experiments.
- Save any experimental results separately if you compare alternative key layouts.

## Secrets and Credentials

Do not commit secrets or credentials to GitHub. If your setup requires environment variables or API keys, store them outside the repository and document the required values in this file.

If you change the TiDB username, password, host, or port, update the SQLAlchemy connection string in the notebook accordingly.

## Final Report

The final report is written in LaTeX and matches the style of the midterm report:

- `\documentclass[11pt]{article}`
- `geometry` with 1 inch margins
- `times`
- `hyperref`
- `booktabs`

The report covers the database system overview, internal architecture, application design, implementation progress, comparison with MySQL and MongoDB, limitations, and next steps.

## Suggested Workflow

1. Launch the TiDB playground.
2. Run the notebook to create and populate the database.
3. Validate the query outputs.
4. Export or compile the final report.
5. Submit the notebook, README, and final report together.

## Troubleshooting

- If `wsl --install` fails on Windows, ensure your version supports WSL and virtualization is enabled.
- If TiUP installation fails, rerun the installer after confirming that `curl` is available.
- If the notebook cannot connect to TiDB, verify that the playground is running on `127.0.0.1:4000`.
- If table creation fails, drop the schema and rerun the notebook from the beginning.
- On Mac, if `source ~/.bashrc` fails, use `source ~/.zshrc` instead.

## Submission Notes

The project submission should include the source notebook, the README, and the final report PDF. The final report should be readable as a standalone document and should clearly explain how the application uses TiDB.
