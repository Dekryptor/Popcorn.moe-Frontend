<template>
  <v-container grid-list-md>
    <v-layout class="text-xs-center">
      <v-flex offset-xs2 xs8>
        {{ selectedUsers }}
        <v-select
          label="Search Friends"
          autocomplete
          dark
          multiple
          chips
          prepend-icon="search"
          :items="searchResults"
          item-text="login"
          item-value="id"
          :rules="[() => search.length > 0 || 'You must choose at least one']"
          :search-input.sync="search"
          v-model="selectedUsers"
        ></v-select>
      </v-flex>
      <v-flex xs1>
        <v-btn fab dark small color="primary">
          <v-icon>add</v-icon>
        </v-btn>
      </v-flex>
    </v-layout>
    <v-tabs class="friends-tabs">
      <v-layout>
        <v-flex xs3>
          <v-list class="friends-tabs-list">
            <v-list-tile @click.stop="currTab = 'friends'" :class="{'active': currTab === 'friends'}">
              <v-list-tile-content>
                <v-list-tile-title>Friends List</v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile @click.stop="currTab = 'invites'" :class="{'active': currTab === 'invites'}">
              <v-list-tile-content>
                <v-list-tile-title>Friends Invites</v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile @click.stop="currTab = 'pending'" :class="{'active': currTab === 'pending'}">
              <v-list-tile-content>
                <v-list-tile-title>Pending invites</v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
          </v-list>
        </v-flex>
        <v-flex xs9>
          <v-layout>
            <v-flex xs12>
              <v-tabs class="friends-tabs" :value="currTab">
                <div class="tabs__items">
                  <v-tabs-content lazy id="friends">
                    <v-data-table
                      :headers="friendsHeader"
                      :items="friends()"
                      hide-actions
                    >
                      <template slot="items" slot-scope="props">
                        <td>
                          {{ props.item.to.login }}
                        </td>
                        <td>
                          {{ "NOT YET" }}
                        </td>
                        <td align="right">
                          <v-btn icon @click.stop="">
                            <v-icon>close</v-icon>
                          </v-btn>
                        </td>
                      </template>
                    </v-data-table>
                  </v-tabs-content>
                  <v-tabs-content lazy id="invites">
                    <v-data-table
                      :headers="invitesHeader"
                      :items="invites()"
                      hide-actions
                    >
                      <template slot="items" slot-scope="props">
                        <td>
                          {{ props.item.from.name }}
                        </td>
                        <td>
                          {{ props.item.date }}
                        </td>
                        <td align="right">
                          <v-btn icon @click.stop="">
                            <v-icon>close</v-icon>
                          </v-btn>
                        </td>
                      </template>
                    </v-data-table>
                  </v-tabs-content>
                  <v-tabs-content lazy id="pending">
                    <v-data-table
                      :headers="pendingHeader"
                      :items="pendingRequests()"
                      hide-actions
                    >
                      <template slot="items" slot-scope="props">
                        <td>
                          {{ props.item.name }}
                        </td>
                        <td>
                          {{ props.item.date }}
                        </td>
                        <td align="right">
                          <v-btn icon @click.stop="">
                            <v-icon>close</v-icon>
                          </v-btn>
                        </td>
                      </template>
                    </v-data-table>
                  </v-tabs-content>
                </div>
              </v-tabs>
            </v-flex>
          </v-layout>
        </v-flex>
      </v-layout>
    </v-tabs>
  </v-container>
</template>

<script>
import VTabs from 'vuetify/es5/components/VTabs'
import VTabsContent from 'vuetify/es5/components/VTabs/VTabsContent'
import VTabsItem from 'vuetify/es5/components/VTabs/VTabsItem'
import { VIcon, VBtn, VTextField, VDataTable, VSelect, VChip } from 'vuetify/es5/components'
import { VList, VListGroup, VListTile, VListTileAction, VListTileContent, VListTileTitle } from 'vuetify/es5/components/VList'
import { VContainer, VFlex, VLayout } from 'vuetify/es5/components/VGrid'
import gql from 'graphql-tag'

export default {
  name: "user_friends",
  data() {
    return {
      me: { relations: [] },
      currTab: "friends",
      search: "",
      selectedUsers: [],
      searchResults: [],
      friendsHeader: [
        {
          text: 'Name',
          align: 'left',
          value: 'name'
        }, {
          text: 'Status',
          align: 'left',
          value: 'status'
        }, {
          text: 'Action',
          align: 'left'
        }
      ],
      invitesHeader: [
        {
          text: 'From',
          align: 'left',
          value: 'from'
        }, {
          text: 'Date',
          align: 'left',
          value: 'date'
        }, {
          text: 'Action',
          align: 'left'
        }
      ],
      pendingHeader: [
        {
          text: 'To',
          align: 'left',
          value: 'name'
        }, {
          text: 'Date',
          align: 'left',
          value: 'date'
        }, {
          text: 'Action',
          align: 'left'
        }
      ],
    }
  },
  methods: {
    friends() {
      return this.me.relations.filter(r => r.status === "FRIEND");
    },
    pendingRequests() {
      return this.me.relations.filter(r => r.status === "SEND_INVITE");
    },
    invites() {
      return this.me.relations.filter(r => r.status === "RECEIVE_INVITE");
    }
  },
  components: {
    VContainer,
    VFlex,
    VLayout,
    VList,
    VListGroup,
    VListTile,
    VListTileAction,
    VListTileContent,
    VListTileTitle,
    VIcon,
    VBtn,
    VTextField,
    VTabs,
    VTabsContent,
    VTabsItem,
    VDataTable,
    VSelect,
    VChip
  },
  apollo: {
    me: {
      query: gql`{ me { relations { status to { login } } } }`,
      update: ({ me }) => me
    },
    searchResults: {
      query: gql`query ($name: String!) {
        searchUser(name: $name) {
          login id
        }
      }`,
      variables() {
        return {
          name: this.search ? this.search : ""
        }
      },
      update: ({ searchUser }) => searchUser
    }
  }
}
</script>

<style lang="stylus">
  @import "../../stylus/main.styl";

  .friends-tabs-list {
    padding-top: 0 !important;
    padding-bottom: 0 !important;
    background-color: #dcdcdc !important;

    .active {
      background-color: #d0d0d0 !important;
      .list__tile__title {
        color: $main-color !important;
      }
    }
  }

  .friends-tabs {
    .table {
      background-color: #dcdcdc !important;
    }
  }

  .application--dark {
    .friends-tabs-list {
      background-color: #454545 !important;

      .active {
        background-color: #4c4c4c !important;
        .list__tile__title {
          color: $main-color !important;
        }
      }
    }

    .friends-tabs {
      .table {
        background-color: #454545 !important;
      }
    }
  }
</style>
