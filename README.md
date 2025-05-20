# Elasticsearch MCP Server

This repository contains experimental features intended for research and evaluation and are not production-ready.

Connect to your Elasticsearch data directly from any MCP Client (like Claude Desktop) using the Model Context Protocol (MCP).

This server connects agents to your Elasticsearch data using the Model Context Protocol. It allows you to interact with your Elasticsearch indices through natural language conversations.

<a href="https://glama.ai/mcp/servers/@elastic/mcp-server-elasticsearch">
  <img width="380" height="200" src="https://glama.ai/mcp/servers/@elastic/mcp-server-elasticsearch/badge" alt="Elasticsearch Server MCP server" />
</a>

## Available Tools

* `list_indices`: List all available Elasticsearch indices
* `get_mappings`: Get field mappings for a specific Elasticsearch index
* `search`: Perform an Elasticsearch search with the provided query DSL
* `get_shards`: Get shard information for all or specific indices
* `document_manage`: Perform CRUD operations (Create, Read, Update, Delete) on Elasticsearch documents

## Prerequisites

* An Elasticsearch instance
* Elasticsearch authentication credentials (API key or username/password)
* MCP Client (e.g. Claude Desktop)

### Testing with Mock Data

For testing purposes, this repository includes a script to create mock data with three indices and sample records.

1. Start Elasticsearch locally (you can use `curl -fsSL https://elastic.co/start-local | sh` for a quick setup)
2. Run the mock data script with your Elasticsearch credentials:
   ```bash
   chmod +x create_mock_data.sh
   ES_PASSWORD=your_password ./create_mock_data.sh
   ```
   You can also customize the URL and username if needed:
   ```bash
   ES_URL=http://localhost:9200 ES_USERNAME=elastic ES_PASSWORD=your_password ./create_mock_data.sh
   ```
3. This will create three indices (`customers`, `products`, and `orders`) with 5 records each, allowing you to test all features including the document management tool

## Demo

https://github.com/user-attachments/assets/5dd292e1-a728-4ca7-8f01-1380d1bebe0c

## Installation & Setup

### Using the Published NPM Package

> [!TIP]
> The easiest way to use Elasticsearch MCP Server is through the published npm package.

1. **Configure MCP Client**
   - Open your MCP Client. See the [list of MCP Clients](https://modelcontextprotocol.io/clients), here we are configuring Claude Desktop.
   - Go to **Settings > Developer > MCP Servers**
   - Click `Edit Config` and add a new MCP Server with the following configuration:

   ```json
   {
     "mcpServers": {
       "elasticsearch-mcp-server": {
         "command": "npx",
         "args": [
           "-y",
           "@elastic/mcp-server-elasticsearch"
         ],
         "env": {
           "ES_URL": "your-elasticsearch-url",
           "ES_API_KEY": "your-api-key"
         }
       }
     }
   }
   ```

2. **Start a Conversation**
   - Open a new conversation in your MCP Client
   - The MCP server should connect automatically
   - You can now ask questions about your Elasticsearch data

### Configuration Options

The Elasticsearch MCP Server supports configuration options to connect to your Elasticsearch:

> [!NOTE]
> You must provide either an API key or both username and password for authentication.


| Environment Variable | Description | Required |
|---------------------|-------------|----------|
| `ES_URL` | Your Elasticsearch instance URL | Yes |
| `ES_API_KEY` | Elasticsearch API key for authentication | No |
| `ES_USERNAME` | Elasticsearch username for basic authentication | No |
| `ES_PASSWORD` | Elasticsearch password for basic authentication | No |
| `ES_CA_CERT` | Path to custom CA certificate for Elasticsearch SSL/TLS | No |


### Developing Locally

> [!NOTE]
> If you want to modify or extend the MCP Server, follow these local development steps.

1. **Use the correct Node.js version**
   ```bash
   nvm use
   ```

2. **Install Dependencies**
   ```bash
   npm install
   ```

3. **Build the Project**
   ```bash
   npm run build
   ```

4. **Run locally in Claude Desktop App**
   - Open **Claude Desktop App**
   - Go to **Settings > Developer > MCP Servers**
   - Click `Edit Config` and add a new MCP Server with the following configuration:

   ```json
   {
     "mcpServers": {
       "elasticsearch-mcp-server-local": {
         "command": "node",
         "args": [
           "/path/to/your/project/dist/index.js"
         ],
         "env": {
           "ES_URL": "your-elasticsearch-url",
           "ES_API_KEY": "your-api-key"
         }
       }
     }
   }
   ```

5. **Debugging with MCP Inspector**
   ```bash
   ES_URL=your-elasticsearch-url ES_API_KEY=your-api-key npm run inspector
   ```

   This will start the MCP Inspector, allowing you to debug and analyze requests. You should see:

   ```bash
   Starting MCP inspector...
   Proxy server listening on port 3000

   🔍 MCP Inspector is up and running at http://localhost:5173 🚀
   ```

## Contributing

We welcome contributions from the community! For details on how to contribute, please see [Contributing Guidelines](/docs/CONTRIBUTING.md).

## Example Questions

> [!TIP]
> Here are some natural language queries you can try with your MCP Client.

* "What indices do I have in my Elasticsearch cluster?"
* "Show me the field mappings for the 'products' index."
* "Find all orders over $500 from last month."
* "Which products received the most 5-star reviews?"
* "Create a new document in the 'customers' index with this data."
* "Get the document with ID '12345' from the 'orders' index."
* "Update the customer profile for ID '1000' with this new information."
* "Delete the product with ID 'discontinued-item' from the catalog."

## How It Works

1. The MCP Client analyzes your request and determines which Elasticsearch operations are needed.
2. The MCP server carries out these operations (listing indices, fetching mappings, performing searches).
3. The MCP Client processes the results and presents them in a user-friendly format.

## Security Best Practices

> [!WARNING]
> Avoid using cluster-admin privileges. Create dedicated API keys with limited scope and apply fine-grained access control at the index level to prevent unauthorized data access.

You can create a dedicated Elasticsearch API key with minimal permissions to control access to your data:

```
POST /_security/api_key
{
  "name": "es-mcp-server-access",
  "role_descriptors": {
    "mcp_server_role": {
      "cluster": [
        "monitor"
      ],
      "indices": [
        {
          "names": [
            "index-1",
            "index-2",
            "index-pattern-*"
          ],
          "privileges": [
            "read",
            "view_index_metadata"
          ]
        }
      ]
    }
  }
}
```

## License

This project is licensed under the Apache License 2.0.

## Troubleshooting

* Ensure your MCP configuration is correct.
* Verify that your Elasticsearch URL is accessible from your machine.
* Check that your authentication credentials (API key or username/password) have the necessary permissions.
* If using SSL/TLS with a custom CA, verify that the certificate path is correct and the file is readable.
* Look at the terminal output for error messages.

If you encounter issues, feel free to open an issue on the GitHub repository.
