# GenMarket : Market Simulation by Generation

**GenMarket** is a modular, multi-layer generative market simulation framework designed to evaluate trading strategies and market impact under synthetic yet realistic conditions.

It combines macro regime generation, high-frequency order flow simulation, LOB microstructure modeling, and plug-in execution strategies (e.g. VWAP, TWAP, Dynamic Stop Loss), supporting both controlled experimentation and generative scenario construction.

---

## Features

- **Multi-layer simulation**  
  Top-down generation from macro regimes → order flow → limit order book (LOB).

- **Scenario generation**  
  Uses VAE/Diffusion model stubs to create synthetic market conditions beyond historical replay.

- **Order flow modeling**  
  Autoregressive event-level generator simulates realistic buy/sell order streams.

- **LOB simulation engine**  
  Matches orders with queue logic and customizable liquidity depth.

- **Built-in execution strategies**  
  Plug-and-play VWAP, TWAP, and Dynamic Stop Loss strategies for benchmark testing.

- **Market impact evaluation**  
  Measures execution price, slippage, impact cost, and profit/loss (PnL).

- **Visualization**  
  Interactive plotting of order flow, execution trajectory, and order book depth using Plotly.

- **Natural language scenario configuration**  
  Generate simulation configurations from plain English descriptions using OpenAI's GPT API.

- **Parallelized simulation**  
  Accelerated order flow generation and LOB simulation using multi-threading.

---

## Installation

```bash
pip install --upgrade genmarket
```

---

## Example Config (`configs/market_crash_scenario.json`)

```json
{
  "regime": "volatile",
  "volatility": 0.35,
  "liquidity": "low",
  "lob": {
    "levels": 5,
    "latency_ms": 10
  },
  "strategy": {
    "type": "vwap",
    "params": {
      "target_volume": 100,
      "time_horizon": 10
    }
  }
}
```

---

## Run a Simulation

```bash
python examples/run_simulation.py
```

```python
from genmarket import GenMarket
from genmarket.plotting import plot_executions
import json

with open("configs/market_crash_scenario.json") as f:
    config = json.load(f)

gm = GenMarket(config)
metrics = gm.run()

print("Simulation Metrics:")
for k, v in metrics.items():
    print(f"{k}: {v}")

# Visualize execution result
plot_executions(metrics.get("executions", []))
```

---

## Strategy Plugins

- `VWAPStrategy` – Volume-weighted execution across time horizon  
- `TWAPStrategy` – Time-weighted slices across fixed intervals  
- `DynamicStopLossStrategy` – Trailing or fixed stop loss based on market conditions  

You can create your own strategies and add them under `genmarket/strategy_plugins/`.

---

## New Features

### **Natural Language Scenario Configuration**
Generate simulation configurations from plain English descriptions using OpenAI's GPT API. Example:

```python
from genmarket.nlp_config_generator import NLPConfigGenerator

api_key = "your_openai_api_key"
generator = NLPConfigGenerator(api_key=api_key)

description = "Simulate a high-volatility market with low liquidity. Use a VWAP strategy with a target volume of 1000 shares over a 1-hour period."
config = generator.generate_config(description)

print("Generated Configuration:")
print(config)

# Save the configuration to a file
with open("generated_config.json", "w") as f:
    f.write(config)
```

---

### **Interactive Visualization**
Visualize execution prices, order flow, and order book depth interactively using Plotly.

```python
from genmarket.interactive_plotting import InteractivePlotting

# Example data for execution prices
timestamps = ['2025-04-13 10:00', '2025-04-13 10:01', '2025-04-13 10:02']
prices = [100, 102, 101]

InteractivePlotting.plot_execution_prices(prices=prices, timestamps=timestamps)

# Example data for order book depth
price_levels = [99, 100, 101, 102, 103]
bid_depth = [50, 70, 60, 40, 30]
ask_depth = [30, 40, 50, 60, 70]

InteractivePlotting.plot_order_book_depth(bid_depth=bid_depth, ask_depth=ask_depth, levels=price_levels)
```

---

### **Parallelized Simulation**
Accelerate order flow generation and LOB simulation using multi-threading.

```python
from genmarket.order_flow_generator import OrderFlowGenerator
from genmarket.lob_simulator import LOBSimulator

# Example parameters for order flow generation
order_flow_params = [
    {"base_price": 100, "steps": 10},
    {"base_price": 200, "steps": 10},
    {"base_price": 300, "steps": 10},
]

# Example parameters for LOB simulation
lob_params = [
    {"base_bid": 100, "base_ask": 101},
    {"base_bid": 200, "base_ask": 201},
    {"base_bid": 300, "base_ask": 301},
]

# Initialize generators
order_flow_generator = OrderFlowGenerator(flow_params={})
lob_simulator = LOBSimulator(lob_params={})

# Generate order flows in parallel
order_flows = order_flow_generator.generate_order_flow_parallel(order_flow_params, num_threads=3)
print("Order Flows:", order_flows)

# Simulate LOBs in parallel
lobs = lob_simulator.simulate_lob_parallel(lob_params, num_threads=3)
print("LOBs:", lobs)
```

---

## Applications

- Strategy benchmarking under stress scenarios  
- Execution cost modeling and market impact analysis  
- Synthetic data generation for pre-trade analytics  
- Reinforcement learning environment (future support)

---

## Roadmap

- [x] Integrate pretrained diffusion/VAE market generators  
- [x] Add LLM-driven natural language scenario parser  
- [x] Extend to multi-asset & cross-venue simulation  
- [ ] Add dashboard interface for real-time simulation control  

---

## License

MIT License. See `LICENSE`.

---

## Citation

```
@misc{genmarket2025,
  title   = {GenMarket: A Multi-Layer Generative Market Simulation Framework},
  author  = {Jialue Chen},
  year    = {2025},
  note    = {https://github.com/jialuechen/genmarket}
}
```