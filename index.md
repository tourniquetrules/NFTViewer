<!DOCTYPE html>
<html>
<head>
    <title>Liberty NFT Viewer</title>
    <script src="https://cdn.ethers.io/lib/ethers-5.2.umd.min.js"></script>
</head>
<body>
    <h1>Liberty NFT</h1>
    <p id="nft-name"></p>
    <p id="nft-description"></p>
    <img id="nft-image" width="300px">
    <p>Owner: <span id="nft-owner"></span></p>
    
    <script>
        const provider = new ethers.providers.JsonRpcProvider("https://rpc.libertyproject.space/");
        const contractAddress = "0x32124F3f053aE4Ed7BE2132b08FcCd04356D19A4"; // Replace with actual deployed contract
        const contractABI = [
            {
                "inputs": [
                    { "internalType": "uint256", "name": "tokenId", "type": "uint256" }
                ],
                "name": "ownerOf",
                "outputs": [
                    { "internalType": "address", "name": "", "type": "address" }
                ],
                "stateMutability": "view",
                "type": "function"
            },
            {
                "inputs": [
                    { "internalType": "uint256", "name": "tokenId", "type": "uint256" }
                ],
                "name": "tokenURI",
                "outputs": [
                    { "internalType": "string", "name": "", "type": "string" }
                ],
                "stateMutability": "view",
                "type": "function"
            }
        ];

        async function loadNFT(tokenId) {
            const contract = new ethers.Contract(contractAddress, contractABI, provider);
            
            try {
                // Fetch NFT metadata URI
                const tokenURI = await contract.tokenURI(tokenId);
                const metadataUrl = tokenURI.replace("ipfs://", "https://gateway.pinata.cloud/ipfs/");
                const metadata = await fetch(metadataUrl).then(res => res.json());
                
                document.getElementById("nft-name").innerText = metadata.name;
                document.getElementById("nft-description").innerText = metadata.description;
                document.getElementById("nft-image").src = metadata.image.replace("ipfs://", "https://gateway.pinata.cloud/ipfs/");
                
                // Fetch Owner Address
                const owner = await contract.ownerOf(tokenId);
                document.getElementById("nft-owner").innerText = owner;
            } catch (error) {
                console.error("Error fetching NFT data:", error);
            }
        }

        // Replace with actual NFT Token ID
        loadNFT(2);
    </script>
</body>
</html>
