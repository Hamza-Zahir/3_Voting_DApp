<template>
  <div>
    <div
      class="bg-dark text-light p-2 d-flex justify-content-around align-items-center"
    >
      <h2 class="">Voting Dapp</h2>
      <div
        class="btn btn-primary"
        :class="CurrentAccount && ChainId != 4 ? 'btn-warning' : ''"
        @click="connectMetamask"
      >
        {{
          CurrentAccount && ChainId == 4
            ? `${CurrentAccount.slice(0, 5)}...${CurrentAccount.slice(
                CurrentAccount.length - 4
              )}`
            : CurrentAccount && ChainId != 4
            ? "network erore"
            : " Conect Wallet"
        }}
      </div>
    </div>
    <div class="text-center my-5" v-if="ChainId != 4 && CurrentAccount">
      <h4>
        pleas change
        <span class="text-info cp" @click="connectMetamask">network </span>
      </h4>
    </div>
    <div class="text-center my-5" v-if="loding">
      <h2>Loding...</h2>
    </div>
    <div class="mt-4 col-10 col-lg-7 mx-auto" v-if="AllCandidates.length">
      <h1 class="text-center">Election Results</h1>
      <hr />
      <div class="text-center">
        <div class="d-flex m-0 p-0 bg-secondary text-light">
          <span class="col-2 border p-1">#</span>
          <span class="col border p-1">Name</span>
          <span class="col border p-1">Votes</span>
          <span class="col border p-1">percentage</span>
        </div>
        <div
          class=""
          v-for="candidate in AllCandidates"
          :key="candidate.CandidateId"
        >
          <div class="d-flex m-0 p-0 bg-info">
            <div class="col-2 border p-1">{{ candidate.CandidateId }}</div>
            <span class="col border p-1">{{ candidate.CandidateName }}</span>
            <span class="col border p-1">{{
              candidate.CandidateVoteCount
            }}</span>
            <span class="col border p-1"
              >{{
                Number(
                  ((100 / TotalElected) * candidate.CandidateVoteCount).toFixed(
                    2
                  )
                )
              }}
              %</span
            >
          </div>
        </div>
        <hr />
        <div class="mt-3 text-left p-2">
          <h5>Select Candidate</h5>

          <select
            name=""
            id=""
            class="col p-2 rounded bg-secondary text-light"
            @change="
              (e) => {
                CandidateSelectId = e.target.value;
              }
            "
          >
            <option value="">Select Candidate</option>
            <option
              class="p-4"
              v-for="candidate in AllCandidates"
              :key="`option-${candidate.CandidateId}`"
              :value="candidate.CandidateId"
            >
              {{ candidate.CandidateName }}
            </option>
          </select>
          <div
            class="btn btn-primary m-4 px-4"
            @click="Vote(CandidateSelectId)"
          >
            Vote
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { mapActions, mapGetters } from "vuex";
export default {
  data() {
    return {
      CandidateSelectId: "",
      loding: false,
    };
  },
  computed: {
    ...mapGetters(["CurrentAccount"]),
    ...mapGetters(["ChainId"]),
    ...mapGetters(["AllCandidates"]),
    ...mapGetters(["TotalElected"]),
  },
  mounted() {
    this.GetCandidates();
  },
  methods: {
    ...mapActions(["checkWalletIsConnected"]),
    ...mapActions(["connectMetamask"]),
    ...mapActions(["getCandidates"]),
    ...mapActions(["Vote"]),

    async GetCandidates() {
      await this.checkWalletIsConnected();
      this.loding = true;
      await this.getCandidates().then(() => {
        this.loding = false;
      });
    },
  },
};
</script>
<style scoped>
.cp {
  cursor: pointer;
}
</style>
