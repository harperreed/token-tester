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
              <span class="text-gray-700">
                Enter your token contract and ID
              </span>
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
                for="tokenId"
              >
                Token ID
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
                placeholder="Enter Token ID"
                v-model="tokenId"
              />
            </div>
            <div class="mb-4">
              <label
                class="block text-gray-700 text-sm font-bold mb-2"
                for="tokenId"
              >
                <button
                  class="
                    bg-blue-500
                    hover:bg-blue-700
                    text-white
                    font-bold
                    py-2
                    px-4
                    rounded
                    focus:outline-none
                    focus:shadow-outline
                  "
                  @click="getTokenInfo()"
                  v-if="!loading"
                >
                  Get Token Info
                </button>
                <div
                  v-else
                  class="
                    bg-blue-500
                    hover:bg-blue-700
                    text-white
                    font-bold
                    py-2
                    px-4
                    rounded
                    animate-pulse
                  "
                >
                  Making request...
                </div>
              </label>
            </div>
          </div>
        </div>
        <div class="py-3 w-full md:w-1/2 ">
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
      <div class="flex sm:flex-row flex-col" >
        <div class="py-3 sm:max-w-lg max-w-full sm:mr-4 bg-white border rounded-xl p-4" v-if="tokenLoaded">

            <div class="mb-4">
              <h3 class="text-xl font-bold mb-4">{{ tokenMetadata.name }}</h3>
              <img :src="tokenMetadata.image" alt="token image" />
              <span class="text-gray-700 text-2xl border-b mt-4 font-semibold mb-2" v-if="tokenMetadata.attributes">Attributes </span>
              <div class="flex flex-wrap" v-if="tokenMetadata.attributes">
                <div
                  v-for="attribute in tokenMetadata.attributes"
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
                  <p class="text-gray-500 text-xs" v-if="attribute.trait_type">
                    {{ attribute.trait_type }}
                  </p>
                  <p v-else class="text-gray-500 text-xs">Property</p>
                  <p class="text-gray-900 text-lg">
                    {{ attribute.value }}
                  </p>
                </div>
              </div>
              <span class="text-gray-700 text-2xl border-b mt-4 font-semibold" v-if="tokenMetadata.description ">Description </span>
              <div class="" v-if="tokenMetadata.description ">
                {{ tokenMetadata.description }}
              </div>
              <span class="text-gray-700 text-2xl border-b mt-4 font-semibold">Misc </span>
              <div class="overflow-scroll">
                <ul>
                <li><a :href="tokenMetadata.uri" target="_blank">Metadata URL</a></li>
                <li>Contract Address: {{ this.contractAddress }}</li>
                <li>Token Id: {{ this.tokenId }}</li>
                </ul>
              </div>
            </div>

            <div class="mb-4">
              <span class="text-gray-700 text-2xl border-b mt-4 font-semibold">Raw Response </span>
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
        <div class=" max-w-full bg-white border rounded-xl p-4" v-if="openSeaLoaded">
          <div class="" >
            <h3 class="text-xl font-bold mb-4">
              <span class="text-gray-700"> OpenSea validation </span>
            </h3>
            <div class="mb-4">
              <div
                class="bg-green-500 rounded w-auto px-4 py-4 border font-medium"
                v-if="validateResponse.valid"
              >
                Passed
              </div>
              <div
                class="bg-red-500 rounded w-auto px-4 py-4 border font-medium"
                v-else
              >
                Failed
              </div>
            </div>
            <div class="mb-4 overflow-scroll max-w-sm" v-if="!validateResponse.valid">
              <span class="text-gray-700 text-2xl border-b mt-4 font-semibold"> Errors </span>

                <div v-for="error in validateResponse.errors" v-bind:key="error" class="
                      p-2
                      text-xs
                      border-b
                      text-gray-700
                      bg-gray-100
                      mb-2
                    ">
                    {{ error }}
                  </div>

            </div>
            <div class="mb-4" v-if="validateResponseRaw">
              <span class="text-gray-700 text-2xl border-b mt-4 font-semibold">Raw Response </span>
              <textarea
                class="
                  text-gray-700 text-xs
                  overflow-scroll
                  bg-gray-100
                  w-full
                  h-36
                  font-mono
                "
                v-model="validateResponseRaw"
              />
            </div>
          </div>
        </div>
      </div>
    </div>

       <div
      class="
        container
        mx-auto




        text-center
        text-sm

      "
    >
    <a href="https://art.pizza/#/harper.eth" class="hover:text-green-300 ">harper.eth</a> / <a class="hover:text-green-300" href="https://twitter.com/harper">@harper</a><br /> <a class="hover:text-green-300" href="mailto:harper@modest.com">harper@modest.com</a></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      contractAddress: "0x495f947276749ce646f68ac8c248420045cb7b5e",
      tokenId: "12830345331175526280463936575724208108263643015557233882150701312422960955393",
      network: "mainnet",
      validateResponse: {},
      openSeaLoaded: false,
      tokenLoaded: false,
      validateResponseRaw: "",
      loading: false,
      tokenMetadata: {},
      tokenMetadataRaw: {},
    };
  },
  methods: {
    async getTokenInfo() {
      this.loading = true;
      this.resetData();
      let host = "testnets-api.opensea.io";

      if (this.network === "mainnet") {
        host = "api.opensea.io";
      }

      const validateUrl = `https://${host}/asset/${this.contractAddress}/${this.tokenId}/validate/`;
      console.log(validateUrl);
      try {
        const validateResponse = await this.$axios.$get(validateUrl);
        this.validateResponse = validateResponse;
        this.validateResponseRaw = JSON.stringify(validateResponse, null, 2);

        if (validateResponse.token_uri) {
          await this.getTokenPayload(validateResponse.token_uri);
        }
      } catch (e) {
        console.log(e);
        this.validateResponse = {
          valid: false,
          errors: [e.message],
        };
      }
      this.openSeaLoaded = true;

      this.loading = false;
      // console.log(response.data);
    },

    async getTokenPayload(url) {
      console.log(url);
      const tokenMetadata = await this.$axios.$get(url);
      tokenMetadata['uri'] = url;

      if (tokenMetadata.image.includes('ipfs://')) {
        const ipfsUrl = tokenMetadata.image.replace('ipfs://', 'https://ipfs.io/');
        tokenMetadata.image = ipfsUrl;
      }

      this.tokenMetadata = tokenMetadata;
      this.tokenMetadataRaw = JSON.stringify(tokenMetadata, null, 2);
      this.tokenLoaded = true;
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
