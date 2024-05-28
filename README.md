pip install requests

import requests

# The URL of the subgraph you want to query
SUBGRAPH_URL = "https://api.thegraph.com/subgraphs/name/uniswap/uniswap-v2"

# Define the GraphQL query to fetch the top liquidity providers
query = """
{
  users(first: 5, orderBy: liquidityPosition, orderDirection: desc) {
    id
    liquidityPositions {
      liquidityTokenBalance
      pair {
        token0 {
          symbol
        }
        token1 {
          symbol
        }
      }
    }
  }
}
"""

# Function to send the GraphQL request
def query_the_graph(url, query):
    response = requests.post(url, json={'query': query})
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Query failed with status code {response.status_code}, response: {response.text}")

# Execute the query and process the data
try:
    data = query_the_graph(SUBGRAPH_URL, query)
    print("Top Liquidity Providers:")

    # Print User Data
    for user in data['data']['users']:
        print(f"\nUser ID: {user['id']}")
        for position in user['liquidityPositions']:
            print(f"  Liquidity Token Balance: {position['liquidityTokenBalance']}")
            print(f"  Pair: {position['pair']['token0']['symbol']}/{position['pair']['token1']['symbol']}")
            
except Exception as e:
    print(e)
