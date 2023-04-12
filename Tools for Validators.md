# Tools for Validators - Mantle Tech Docs

import (

"github.com/ethereum/go-ethereum/crypto"

libcrypto "github.com/libp2p/go-libp2p/core/crypto"

"github.com/libp2p/go-libp2p/core/peer"

)

​

​

func main() {

priStr := "XXXX"

priKey, \_ := crypto.HexToECDSA(priStr)

pubKey := crypto.CompressPubkey(&priKey.PublicKey)

ppk, \_ := libcrypto.UnmarshalSecp256k1PublicKey(pubKey)

peerId, \_ := peer.IDFromPublicKey(ppk)

fmt.Println(peerId)

}
