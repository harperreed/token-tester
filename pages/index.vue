<template>
  <div>
    <div
      class="
        container
        mx-auto
        px-4
        sm:border
        sm:rounded-xl
        sm:my-4
        p-5
        bg-gradient-to-r
        from-green-400
        to-blue-500
      "
    >
      <h1 class="text-3xl text-white font-bold">Harper.eth's Token Tester</h1>

      <div class="flex flex-wrap">
        <div class="w-full md:w-1/2 py-3">
          <div class="bg-white border rounded-xl p-4">
            <h3 class="text-xl font-bold mb-4">
              <span class="text-gray-700"> Metadata info </span>
            </h3>
            <div class="mb-4">
              <label
                class="block text-gray-700 text-sm font-bold mb-2"
                for="network"
              >
                Ethereum Network
              </label>
              <select
                class="
                  block
                  appearance-none
                  w-full
                  bg-white
                  border border-gray-400
                  hover:border-gray-500
                  px-4
                  py-2
                  pr-8
                  rounded
                  shadow
                  leading-tight
                  focus:outline-none
                  focus:shadow-outline
                "
                id="network"
                v-model="network"
              >
                <option value="mainnet" selected>Mainnet</option>
                <option value="ropsten">Ropsten</option>
                <option value="rinkeby">Rinkeby</option>
                <option value="kovan">Kovan</option>
              </select>
            </div>

            <div class="mb-4">
              <label
                class="block text-gray-700 text-sm font-bold mb-2"
                for="contractAddress"
              >
                Token Contract Address
              </label>
              <input
                class="
                  shadow
                  appearance-none
                  border
                  rounded
                  w-full
                  py-2
                  px-3
                  text-gray-700
                  leading-tight
                  focus:outline-none
                  focus:shadow-outline
                "
                id="contractAddress"
                type="text"
                placeholder="Enter Contract Address"
                v-model="contractAddress"
              />
            </div>
            <div class="mb-4">
              <label
                class="block text-gray-700 text-sm font-bold mb-2"
                for="IPFSMetadataHash"
              >
                IPFS Metadata HASH
              </label>
              <input
                class="
                  shadow
                  appearance-none
                  border
                  rounded
                  w-full
                  py-2
                  px-3
                  text-gray-700
                  leading-tight
                  focus:outline-none
                  focus:shadow-outline
                "
                id="text"
                type="text"
                placeholder="Enter metadata IPFS Hash"
                v-model="IPFSMetadataHash"
              />
            </div>
            
            <div class="mb-4">
              <label
                class="block text-gray-700 text-sm font-bold mb-2"
                for="TokenId"
              >
                <div>
                  <ul class="flex pl-0 list-none rounded my-2 w-full">
                    <li class="relative block py-2 px-3 leading-tight bg-white border border-gray-300 text-blue-700 border-r-0 ml-0 rounded-l hover:bg-gray-200"><button @click="prevTokenId" v-shortkey.once="['arrowleft']" @shortkey="prevTokenId">Previous</button></li>
                    <li class="relative block py-2 px-3 leading-tight bg-white border border-gray-300 text-blue-700 border-r-0 hover:bg-gray-200" v-if="tokenId>1"><button  @click="jumpToTokenId((tokenId-2))">{{ `${tokenId-2}` }}</button></li>
                    <li class="relative block py-2 px-3 leading-tight bg-white border border-gray-300 text-blue-700 border-r-0 hover:bg-gray-200" v-if="tokenId>0"><button @click="jumpToTokenId((tokenId-1))">{{ `${tokenId-1}` }}</button></li>
                    <li class="relative block py-2 px-3 leading-tight bg-white border border-gray-300 text-blue-700 border-r-0 hover:bg-gray-200"><input v-model="tokenId" class="text-center font-bold" /></li>
                    <li class="relative block py-2 px-3 leading-tight bg-white border border-gray-300 text-blue-700 border-r-0 hover:bg-gray-200" v-if="tokenId<24999"><button @click="jumpToTokenId((tokenId+1))">{{ `${tokenId+1}` }}</button></li>
                    <li class="relative block py-2 px-3 leading-tight bg-white border border-gray-300 text-blue-700 border-r-0 hover:bg-gray-200" v-if="tokenId<24998"><button @click="jumpToTokenId((tokenId+2))"> {{ `${tokenId+2}` }}</button></li>
                    <li class="relative block py-2 px-3 leading-tight bg-white border border-gray-300 text-blue-700 rounded-r hover:bg-gray-200"><button @click="nextTokenId" v-if="!(tokenId==24999)" v-shortkey.once="['arrowright']" @shortkey="nextTokenId">Next</button><div v-else>end</div></li>
                  </ul>
                </div>
              </label>
            </div>
          </div>
          
        </div>
        <div class="py-3 w-full md:w-1/2">
          <div class="bg-white border rounded-xl p-4 md:ml-4">
            <p>
              Test your tokens. Enter a token ID and click the button to see if
              it is valid.
            </p>
            <p>
              We will check Opensea, the IPFS Metadataand the ERC-721 token
              contract.
            </p>
          </div>

          <div class="bg-white border rounded-xl p-4 mt-2 md:ml-4">
            <p>A simple tool built by @harper / harper.eth</p>
          </div>
        </div>
      </div>
      <div class="flex sm:flex-row flex-col">

        <div v-if="!loading">
        <div
          class="py-3 max-w-full sm:mr-4 bg-white border rounded-xl p-4"
          v-if="token"
        >
          <div class="mb-4" v-if="token">
            <h3 class="text-xl font-bold mb-4">
              #{{ tokenId }} - {{ token.name }}
            </h3>
            <div class="flex sm:flex-row flex-col">
              <div class="mr-4 sm:max-w-lg ">
                <img
                  :src="token.image"
                  alt="token image "
                  class="sm:max-w-full min-w-full bg-gray-300"
                />
              </div>
              <div >
                <span
                  class="
                    text-gray-700 text-2xl
                    border-b
                    mt-4
                    font-semibold
                    mb-2
                  "
                  v-if="token.attributes"
                  >Attributes
                </span>
                <div class="flex flex-wrap" v-if="token.attributes">
                  <div
                    v-for="attribute in token.attributes"
                    v-bind:key="attribute.value"
                    class="
                      mr-2
                      mb-2
                      px-2
                      py-2
                      rounded
                      border
                      bg-blue-100
                      text-center
                      w-auto
                    "
                  >
                    <p
                      class="text-gray-500 text-xs"
                      v-if="attribute.trait_type"
                    >
                      {{ attribute.trait_type }}
                    </p>
                    <p v-else class="text-gray-500 text-xs">Property</p>
                    <p class="text-gray-900 text-lg">
                      {{ attribute.value }}
                    </p>
                  </div>
                </div>
                <span
                  class="text-gray-700 text-2xl border-b mt-4 font-semibold"
                  v-if="token.description"
                  >Description
                </span>
                <div class="" v-if="token.description">
                  {{ token.description }}
                </div>
                <span class="text-gray-700 text-2xl border-b mt-4 font-semibold"
                  >Misc
                </span>
                <div class="overflow-scroll">
                  <ul>
                    <li>
                      <a :href="token.uri" target="_blank">Metadata URL</a>
                    </li>
                    <li v-if="openseaValidateTokenUrl">
                      <a :href="openseaValidateTokenUrl" target="_blank">Validate OpenSea Metdata</a>
                    </li>
                    <li v-if="openseaTokenUrl">
                      <a :href="openseaTokenUrl" target="_blank">OpenSea URL</a>
                    </li>
                    
                    <li v-if="etherscanTokenUrl"><a :href="etherscanTokenUrl" target="_blank">Etherscan Address</a></li>
                    <li v-if="contractAddress">Contract Address: {{ this.contractAddress }}</li>
                    
                    <li>Token Id: {{ this.tokenId }}</li>
                  </ul>
                </div>
              </div>
            </div>
          </div>

          <div class="mb-4">
            <span class="text-gray-700 text-2xl border-b mt-4 font-semibold"
              >Raw Response
            </span>
            <textarea
              class="
                text-gray-700 text-xs
                font-mono
                overflow-scroll
                bg-gray-100
                w-full
                h-36
              "
              v-model="tokenMetadataRaw"
            />
          </div>
        </div>
        </div>
        <div v-else class="border px-4 py-2 bg-white rounded-xl animate-pulse">
          loading
        </div>
      </div>

    </div>

    <div class="container mx-auto text-center text-sm">
      <a href="https://art.pizza/#/harper.eth" class="hover:text-green-300"
        >harper.eth</a
      >
      /
      <a class="hover:text-green-300" href="https://twitter.com/harper"
        >@harper</a
      ><br />
      <a class="hover:text-green-300" href="mailto:harper@modest.com"
        >harper@modest.com</a
      >
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      contractAddress: "",
      IPFSMetadataHash: "",
      tokenId: 0,
      network: "rinkeby",
      tokenMetadataRaw: "",
      token: {},
      loading: false,
    };
  },
  computed: {
    tokenIPFSMetadataUrl() {

      if (this.IPFSMetadataHash && this.tokenId){
        return `https://nervous.mypinata.cloud/ipfs/${this.IPFSMetadataHash}/${this.tokenId}`;
      }else{
        return undefined;
      }
    },
    openseaTokenUrl() {
      let host = "testnets.opensea.io";

      if (this.network === "mainnet") {
        host = "opensea.io";
      }

      if (this.contractAddress) {
        return `https://${host}/assets/${this.contractAddress}/${this.tokenId}`;
      } else {
        return false;
      }
    },
    etherscanTokenUrl() {
      let host = "testnets-api.opensea.io";

      if (this.network === "mainnet") {
        host = "etherscan.io";
      }else if (this.network === "rinkeby") {
        host = "rinkeby.etherscan.io";
      }else if (this.network === "ropsten") {
        host = "ropsten.etherscan.io";
      } 


      if (this.contractAddress) {
        return `https://${host}/token/${this.contractAddress}?a=${this.tokenId}`;
      } else {
        return false;
      }
    },
    openseaValidateTokenUrl() {
      let host = "testnets-api.opensea.io";

      if (this.network === "mainnet") {
        host = "api.opensea.io";
      }

      if (this.contractAddress) {
        return `https://${host}/asset/${this.contractAddress}/${this.tokenId}/validate/`;
      } else {
        return undefined;
      }
    },
  },
  watch: {
    async tokenId(newValue) {
      if (newValue) {
        
        this.tokenMetadataRaw = "";
        this.token = {};
        console.log(this.tokenIPFSMetadataUrl)
        if (this.tokenIPFSMetadataUrl){
          const metadata = await this.getTokenPayload(this.tokenIPFSMetadataUrl);
          if (metadata) {
            this.tokenMetadataRaw = JSON.stringify(metadata, null, 2);
            this.token = metadata;
          }else{
            // this.tokenMetadataRaw = "";
            this.token = undefined;
          }
        }else{
           this.token = undefined;
           this.tokenMetadataRaw = undefined;
        }
       
      }
    },
  },
  mounted() {
    this.tokenId = 1;
  },
  methods: {
    nextTokenId() {
      this.tokenId = parseInt(this.tokenId + 1);
    },
    prevTokenId() {
      this.tokenId = parseInt(this.tokenId - 1);
    },
    jumpToTokenId(tokenId) {
      this.tokenId = parseInt(tokenId);
    },
    async startTokenReview() {
      this.tokenId = 0;
      // console.log(response.data);
    },

    async getTokenPayload(url) {
      this.loading = true;
      try{

        const tokenMetadata = await this.$axios.$get(url);
      

        tokenMetadata["uri"] = url;

        if (tokenMetadata.image.includes("ipfs://")) {
          const ipfsUrl = tokenMetadata.image.replace(
            "ipfs://",
            "https://ipfs.io/"
          );
          tokenMetadata.image = ipfsUrl;
        }
        this.loading = false;
        return tokenMetadata;
       }catch(e){
        console.log(e);
        this.tokenMetadataRaw = JSON.stringify(e.message, null, 2);
        this.loading = false;
        return undefined
      }
    },
    resetData() {
      this.tokenMetadata = {};
      this.tokenMetadataRaw = "";
      this.validateResponse = {};
      this.validateResponseRaw = "";
      this.openSeaLoaded = false;
      this.tokenLoaded = false;
    },
  },
};
</script>
