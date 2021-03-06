<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <v-row>
          <v-col cols="12">
            <h1 class="page-header">Import Data</h1>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12">
            You can manually import data from the services below by clicking on
            the respective logo
          </v-col>
          <v-col cols="12">
            <strong> ⚠️Important notice ⚠️:</strong> Those platforms could
            change their format in a backwards incompatible way quite often. As
            such this import may stop working. If that happens
            <external-link
              url="https://github.com/rotki/rotki/issues/new/choose"
            >
              open an issue with rotki
            </external-link>
            and we will see what we can do.
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12">
            <v-img
              max-width="200"
              class="import-data__cointracking-info elevation-1"
              :src="require('../assets/images/import/cointracking_info.png')"
              @click="importData('cointracking.info')"
            />
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12">
            Important notes for importing data from
            <strong>cointracking</strong>'s CSV exports.
            <ul>
              <li>
                Trades/deposits/withdrawals from Cointracking do not include
                fees.
              </li>
              <li>
                All trades imported from Cointracking will always be considered
                as buys due to the way the data are represented.
              </li>
              <li>
                ETH/BTC Transactions are treated as deposits/withdrawals so they
                are not imported in Rotkehlchen. To import ETH transactions
                simply input your accounts in
                <router-link to="/accounts-balance">
                  Blockchain Accounts/Balances
                </router-link>
                and they will be imported automatically for you.
              </li>
            </ul>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12">
            For the above reasons it's preferred to directly connect your
            exchanges in order to import data from there. If you do not do that
            a lot of accuracy is lost.
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12">
            <div
              class="import-data__crypto-com elevation-1"
              @click="importData('crypto.com')"
            >
              <v-img
                max-width="200"
                :src="require('../assets/images/import/crypto_com.png')"
              />
            </div>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12">
            Important notes for importing data from
            <strong>crypto.com mobile app</strong>'s CSV exports.
            <ul>
              <li>
                [Early stage] This import script may miss some transactions
                types, like withdrawals. Since we can't have an exhaustive list
                of every type, we develop it using what we know at the time. If
                you think that import script can be improved and you can help
                providing more data types,
                <external-link
                  url="https://github.com/rotki/rotki/issues/new/choose"
                >
                  please let us know
                </external-link>
                .
              </li>
              <li>
                <strong>
                  It only concerns the Crypto.com mobile application.
                </strong>
                If you want to connect your Crypto.com Exchange account, please
                wait until we support it and then connect to it as an exchange.
              </li>
              <li>
                Trades/deposits/withdrawals from crypto.com do not include fees
                details. They are waived at the moment (August 2020) or they are
                handled in the traded amount.
              </li>
              <li>
                Only transactions are imported here (for tax report), if you
                want the assets be displayed in your Rotki balances, you have to
                add them manually as manual balances:
                <router-link to="/accounts-balance/manual-balances">
                  Manual Accounts/Balances
                </router-link>
              </li>
            </ul>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import ExternalLink from '@/components/helper/ExternalLink.vue';
import { Message } from '@/store/types';

@Component({
  components: { ExternalLink }
})
export default class ImportData extends Vue {
  async importData(importType: string) {
    try {
      const file = await this.$interop.openFile('Select a file');
      if (!file) {
        return;
      }
      await this.$api.importDataFrom(importType, file);
      this.$store.commit('setMessage', {
        success: true,
        title: 'Import successful',
        description: `Data imported from ${importType} export file successfully`
      } as Message);
    } catch (e) {
      this.$store.commit('setMessage', {
        title: 'Import failed',
        description: e.message
      } as Message);
    }
  }
}
</script>

<style scoped lang="scss">
.import-data {
  &__crypto-com {
    max-width: 200px;
    padding: 10px;

    &:hover {
      cursor: pointer;
    }
  }

  &__cointracking-info {
    &:hover {
      cursor: pointer;
    }
  }
}
</style>
