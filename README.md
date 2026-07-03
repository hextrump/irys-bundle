# Irys Bundle Demo

A simple demonstration of using Irys Network's bundle for decentralized storage.

## Irys Bundle Integration

### 1. Include the Bundle
Add the Irys bundle to your HTML:
```html
<script src="https://uploader.irys.xyz/6JwtssVsxFyRj8U7pBfEe2VqToEvtm5gErzaA5jTMvE4"></script>
```

```html
<script src="https://uploader.irys.xyz/Cip4wmuMv1K3bmcL4vYoZuV2aQQnnzViqwHm6PCei3QX/bundle.js"></script>
```


### 2. Initialize with Wallet
```javascript
const getIrysUploader = async () => {
    if (!wallet) {
        throw new Error("Wallet not connected");
    }

    try {
        // Configure Solana message signing
        const originalSignMessage = window.solana.signMessage;
        window.solana.signMessage = async (msg) => {
            const signedMessage = await originalSignMessage.call(window.solana, msg);
            return signedMessage.signature || signedMessage;
        };

        // Initialize WebIrys with Solana provider
        const webSolana = await WebIrys.WebUploader(WebIrys.WebSolana)
            .withProvider(window.solana);
        return webSolana;
    } catch (error) {
        throw new Error("Error connecting to Irys");
    }
};
```

### 3. Upload Data with Tags
```javascript
async function uploadData(message) {
    const tags = [
        { name: "App-Name", value: "YourAppName" },
        { name: "Content-Type", value: "text/plain" },
        { name: "Unix-Time", value: Date.now().toString() },
        { name: "User-Address", value: wallet.publicKey.toString() }
    ];

    const receipt = await irysUploader.upload(message, { tags });
    return receipt;
}
```

### 4. Query Uploaded Data
```javascript
async function queryData() {
    const query = `
    query {
        transactions(
            tags: [
                { name: "App-Name", values: ["YourAppName"] }
            ],
            order: DESC,
            limit: 50
        ) {
            edges {
                node {
                    id
                    tags {
                        name
                        value
                    }
                }
            }
        }
    }`;

    const response = await fetch('https://uploader.irys.xyz/graphql', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ query })
    });

    return response.json();
}
```

### 5. Retrieve Data
```javascript
async function retrieveData(id) {
    const response = await fetch(`https://gateway.irys.xyz/${id}`);
    const content = await response.text();
    return content;
}
```

## Key Features

- **Permanent Storage**: All uploaded data is permanently stored on Irys Network
- **Metadata Tagging**: Flexible tagging system for data organization
- **GraphQL Queries**: Advanced querying capabilities for data retrieval
- **Web3 Integration**: Seamless integration with Solana wallets
- **Content Addressing**: Each upload receives a unique identifier

## Technical Requirements

- A Web3-compatible browser
- Solana wallet (Phantom or OKX)
- Sufficient SOL tokens for upload fees

## Best Practices

1. **Wallet Connection**
   - Always verify wallet connection before initializing Irys
   - Handle connection errors gracefully
   - Maintain user feedback for connection status

2. **Data Upload**
   - Include relevant metadata tags
   - Implement proper error handling
   - Validate data before uploading
   - Show upload progress to users

3. **Data Retrieval**
   - Implement pagination for large datasets
   - Cache frequently accessed data
   - Handle network errors gracefully
   - Rate limit API calls

4. **Security**
   - Sanitize user input
   - Validate transaction signatures
   - Never expose sensitive data
   - Implement proper access controls

## API Endpoints

- **Upload Bundle**: `https://uploader.irys.xyz/Cip4wmuMv1K3bmcL4vYoZuV2aQQnnzViqwHm6PCei3QX/bundle.js`
- **GraphQL API**: `https://uploader.irys.xyz/graphql`
- **Gateway**: `https://gateway.irys.xyz/`

## Error Handling

```javascript
try {
    const uploader = await getIrysUploader();
    // Handle successful connection
} catch (error) {
    console.error('Error initializing Irys:', error);
    // Handle error appropriately
}
```

## Additional Resources

- [Irys Network Documentation](https://docs.irys.xyz)
- [GraphQL API Reference](https://docs.irys.xyz/developer-docs/querying/query-data)
- [Solana Integration Guide](https://docs.irys.xyz/developer-docs/web-sdk)

## License

This demo is provided under the MIT License. 
