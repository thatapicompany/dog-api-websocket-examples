# Live API Event Map (WebSocket Demo)

This project demonstrates how to connect to the **The Dog API / The Cat API** WebSocket Realtime Gateway to visualize API events (votes, favourites, uploads) as they happen across the world.

## 📂 Project Structure

- **`index.html`**  
  A simplified, full-screen map experience. It automatically connects to the WebSocket gateway on load and displays events on a global map.

- **`map-events.html`**  
  An interactive version with a settings panel. You can manually configure the WebSocket URL, API Key, and toggle image attachments. It also includes a scrolling event log alongside the map.

- **`events.html`**  
  A "WebSocket Tester" dashboard focused on logging raw events. Useful for debugging connection issues or inspecting the raw JSON payload of events.

## 🚀 How to Implement (WebSocket API)

The API provides a real-time stream of user interactions. You can use this to build live dashboards, activity feeds, or analytical tools.

### Connection Details

- **WebSocket URL**: `https://staging-api.thedogapi.com` or `https://api.thedogapi.com` (Production)
- **Transport**: `websocket` (Mandatory: Long-polling is disabled on serverless infrastructure)
- **Auth**: Pass your API Key via the `x-api-key` header (handshake or extraHeaders).

### Implementation Example (Socket.IO v4)

```html
<script src="https://cdn.socket.io/4.7.2/socket.io.min.js"></script>
<script>
    const socket = io('https://staging-api.thedogapi.com', {
        transports: ['websocket'], // ⚠️ IMPORTANT: Force websocket transport
        auth: {
            'x-api-key': 'YOUR_API_KEY', // Get one for free at thedogapi.com
            'attach_image': 'true'       // Optional: Ask server to lookup image details
        },
        extraHeaders: {
            // Some clients/proxies require headers here too
            'x-api-key': 'YOUR_API_KEY',
            'attach_image': 'true'
        }
    });

    socket.on('connect', () => {
        console.log('Connected to Realtime Gateway!');
    });

    socket.on('disconnect', () => {
        console.log('Disconnected');
    });

    // Listen for all events
    socket.onAny((eventName, data) => {
        console.log(`Received ${eventName}:`, data);
        
        // Example Data Structure:
        // {
        //   "id": 12345,
        //   "image_id": "b1",
        //   "value": 1,
        //   "_metadata": {
        //     "event_type": "vote",
        //     "country_code": "US"
        //   },
        //   "image": { "url": "..." } // Only if attach_image=true
        // }
    });
</script>
```

### Key Parameters

| Header / Auth | Value | Description |
| :--- | :--- | :--- |
| `x-api-key` | `string` | **Required**. Your API Key. |
| `attach_image` | `'true' \| 'false'` | **Optional**. If `true`, the server performs a DB lookup to include image URL/metadata in the event payload. Default is `false` (faster). |

### Reference

For full API documentation, endpoints, and schema details, please refer to the OpenAPI Specification:
[http://staging-api.thedogapi.com/openapi-json](http://staging-api.thedogapi.com/openapi-json)
