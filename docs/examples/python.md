# Python Examples

```python
import requests

BASE_URL = "http://localhost:8888"

# --- AI Analysis (Recommended) ---

def analyze_query(query: str):
    """Natural language query — auto-resolves stock name."""
    return requests.post(
        f"{BASE_URL}/analyze/query",
        json={"query": query},
        timeout=120,
    ).json()

def analyze_stock(stock_codes: list[str], query: str | None = None):
    """Smart analysis with explicit stock codes."""
    return requests.post(
        f"{BASE_URL}/analyze",
        json={"stock_codes": stock_codes, "query": query},
        timeout=120,
    ).json()

def analyze_full(stock_codes: list[str]):
    """Full analysis — all major feature groups."""
    return requests.post(
        f"{BASE_URL}/analyze/full",
        json={"stock_codes": stock_codes},
        timeout=120,
    ).json()

# --- Raw Data (Advanced) ---

def get_k_patterns(stock_codes: list[str]):
    return requests.post(f"{BASE_URL}/features/k-patterns", json={
        "stock_codes": stock_codes, "frequency": "1d", "count": 300,
    }).json()

def get_combined_flow(stock_codes: list[str]):
    return requests.post(f"{BASE_URL}/flow/combined", json={
        "stock_codes": stock_codes,
    }).json()

def get_all_features(stock_codes: list[str]):
    return requests.post(f"{BASE_URL}/features/all", json={
        "stock_codes": stock_codes,
    }).json()


# Usage
if __name__ == "__main__":
    result = analyze_query("帮我看看建科智能怎么样")
    print(result.get("summary") or result.get("report"))
```

See also: [API Reference](../api-reference.md) · [AI Integration Guide](../ai-integration.md)
