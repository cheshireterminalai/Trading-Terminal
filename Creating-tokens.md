# Creating Tokens

The Cheshire Trading Terminal provides a streamlined process for creating tokens on the Solana blockchain using SHYFT's token creation API.

## Overview

Token creation in the Cheshire Trading Terminal is powered by SHYFT's create_detach API, which provides a simple yet powerful way to create tokens with custom parameters and metadata.

## Prerequisites

- Connected Solana wallet
- SHYFT API key (Tchyvp-dBSr1nHLG)
- Token metadata (name, symbol, etc.)
- Token image (optional)

## Token Creation Process

### 1. API Configuration

The token creation process uses the following SHYFT API endpoints:

```typescript
const SHYFT_CONFIG = {
  API_KEY: 'Tchyvp-dBSr1nHLG',
  BASE_URL: 'https://api.shyft.to/sol/v1',
  RPC_URL: 'https://rpc.shyft.to',
  WEBSOCKET: 'wss://rpc.shyft.to'
};
```

### 2. Token Parameters

Required and optional parameters for token creation:

```typescript
interface TokenCreationParams {
  // Required Parameters
  wallet: string;       // Wallet address that will have update authority
  name: string;        // Token name
  symbol: string;      // Token symbol (typically 3-5 characters)
  
  // Optional Parameters
  decimals?: number;   // Number of decimal places (default: 9)
  description?: string; // Token description
  image?: File;        // Token image file
}
```

### 3. API Request Format

The token creation request uses multipart/form-data format:

```typescript
async function createToken(params: TokenCreationParams) {
  const formData = new FormData();
  formData.append('network', 'mainnet-beta');
  formData.append('wallet', params.wallet);
  formData.append('name', params.name);
  formData.append('symbol', params.symbol);
  
  if (params.decimals !== undefined) {
    formData.append('decimals', params.decimals.toString());
  }
  
  if (params.description) {
    formData.append('description', params.description);
  }
  
  if (params.image) {
    formData.append('file', params.image);
  }

  return await postToShyft('/token/create_detach', formData);
}
```

## User Interface

The token creation form provides a user-friendly interface for creating tokens:

```typescript
const CreateTokenForm: FC = () => {
  // Form state
  const [name, setName] = useState('');
  const [symbol, setSymbol] = useState('');
  const [decimals, setDecimals] = useState(9);
  const [description, setDescription] = useState('');
  const [image, setImage] = useState<File | null>(null);

  // Form submission
  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    
    try {
      const result = await createToken({
        wallet: wallet.publicKey.toString(),
        name,
        symbol,
        decimals,
        description,
        image: image || undefined,
      });
      
      // Handle success
    } catch (error) {
      // Handle error
    }
  };

  // Form JSX...
};
```

## Example Usage

### Basic Token Creation

```typescript
// Create a basic token
const result = await shyftService.createDetachedToken({
  wallet: "FZxUbyKFYeGXXDPZxeyzyXRuQwCfPLhPYQgGLDzNk2tC",
  name: "Example Token",
  symbol: "EXT",
  decimals: 9
});
```

### Token with Metadata

```typescript
// Create a token with metadata
const result = await shyftService.createDetachedToken({
  wallet: "FZxUbyKFYeGXXDPZxeyzyXRuQwCfPLhPYQgGLDzNk2tC",
  name: "Example Token",
  symbol: "EXT",
  decimals: 9,
  description: "An example token with metadata",
  image: imageFile // File object from input
});
```

## Response Format

The API returns a JSON response with the token details:

```typescript
interface TokenCreationResponse {
  success: boolean;
  result: {
    address: string;      // Token mint address
    name: string;         // Token name
    symbol: string;       // Token symbol
    decimals: number;     // Decimal places
    metadata?: {
      uri: string;        // Metadata URI
      name: string;       // Metadata name
      symbol: string;     // Metadata symbol
      description?: string; // Description
      image?: string;     // Image URI
    };
  };
}
```

## Error Handling

Common errors and their solutions:

```typescript
try {
  const result = await createToken(params);
} catch (error) {
  switch (error.code) {
    case 'INVALID_WALLET':
      // Handle invalid wallet
      break;
    case 'INVALID_PARAMS':
      // Handle invalid parameters
      break;
    case 'NETWORK_ERROR':
      // Handle network issues
      break;
    default:
      // Handle other errors
  }
}
```

## Best Practices

1. **Input Validation**
   - Validate token name and symbol format
   - Check image size and format
   - Verify wallet connection

2. **Error Handling**
   - Provide clear error messages
   - Implement retry logic
   - Handle network issues

3. **User Experience**
   - Show loading states
   - Provide success feedback
   - Enable form reset

4. **Security**
   - Validate wallet ownership
   - Check transaction signatures
   - Monitor for suspicious activity

## Token Management

After creation, tokens can be managed through various operations:

### 1. Supply Management
```typescript
// Mint additional tokens
await shyftService.mintToken({
  wallet: ownerAddress,
  token: tokenAddress,
  amount: amount,
  receiver: receiverAddress
});

// Burn tokens
await shyftService.burnToken({
  wallet: ownerAddress,
  token: tokenAddress,
  amount: amount
});
```

### 2. Metadata Updates
```typescript
// Update token metadata
await shyftService.updateTokenMetadata({
  wallet: ownerAddress,
  token: tokenAddress,
  name: newName,
  symbol: newSymbol,
  image: newImageFile
});
```

### 3. Transfer Authority
```typescript
// Transfer token authority
await shyftService.transferAuthority({
  wallet: currentOwner,
  token: tokenAddress,
  newAuthority: newOwnerAddress
});
```

## Monitoring and Analytics

Track token performance and usage:

1. **Supply Metrics**
   - Total supply
   - Circulating supply
   - Holder count

2. **Trading Metrics**
   - Volume
   - Liquidity
   - Price action

3. **Usage Analytics**
   - Transaction count
   - Active holders
   - Trading pairs

## Future Improvements

1. **Enhanced Metadata**
   - Rich media support
   - Social links
   - Team information

2. **Advanced Features**
   - Token vesting
   - Governance integration
   - Automated burns

3. **Integration Options**
   - DEX listings
   - Marketing tools
   - Community features

## Support

For token creation support:
- Technical documentation: [SHYFT Docs](https://docs.shyft.to)
- Community support: [Discord](https://discord.gg/cheshire)
- Email support: support@cheshire.trading
