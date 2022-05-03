<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <table>
        <thead id='tableHead'>
          <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Watchers</th>
            <th>Forks</th>
          </tr>
        </thead>
        <tbody id='tableBody'>
        </tbody>
        <tfoot>
          <tr>
            <td >
              <v-chip
                class="ma-2"
                label
                color="#2A2B2A"
              >
              {{currentPageFooter}}
              </v-chip>
            </td>
            <td>
              <v-btn 
                color="#2A2B2A"
                @click='previousPage()'
                :disabled="currentPage === 1"
              >
                Previous Page
              </v-btn>
            </td>
            <td>
              <v-btn 
                color="#2A2B2A"
                @click='nextPage()'
                :disabled="checkIfLastPage()"
              >
                Next Page
              </v-btn>
            </td>
            <td>
              <v-chip
                class="ma-2"
                label
                color="#2A2B2A"
              >
              {{'Table Show Count: ' + tableShowCount}}
              </v-chip>
            </td>
          </tr>
        </tfoot>
      </table> 
    </v-col>
  </v-row>
</template>
<style>
table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

table tr:nth-child(odd) td{
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
  background-color: #996888
}

td {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
  background-color: #5E4955;
  color: white;
}

th {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
  background-color: #5E4955
}

tr:nth-child(even) {
  background-color: #dddddd;
}
</style>

<script>
  export default {
    data () {
      return {
        currentPage: 1,
        currentPageFooter: 'Page 1',
        highestRatedJSRepos: [],
        tableShowCount: 20,
        url: 'https://api.github.com/search/repositories?q=language:javascript&sort=stars&order=desc&per_page=100',
      }
    },
    methods: {
      checkIfLastPage() {
        let startingIndex = (this.currentPage)*this.tableShowCount
        if(startingIndex >= this.highestRatedJSRepos.length)
          return true
        
        return false
      },
      getGithubsHighestRatedJavascriptRepos() {
        let thisPoiner = this
        this.$axios
        .$get(this.url)
        .then(function (response) {
          thisPoiner.highestRatedJSRepos = response.items
          thisPoiner.populateTable(thisPoiner.highestRatedJSRepos)
        })
        .catch(function(error) {
          console.error(error)
        })
      },

      nextPage() {
        if( !this.checkIfLastPage() )
          this.currentPage++

        this.setCurrentPage()
        this.getGithubsHighestRatedJavascriptRepos()
        
      },
      
      populateTable(githubResponseObject) {
        //let thisPoiner = this
        //document.getElementById("currentPageFooter").innerHTML = 'Page ' + this.currentPage;
        let table = document.getElementById("tableBody");
        table.innerHTML = "";
        let startIndex = ((this.currentPage - 1)*this.tableShowCount)
        let endIndex = startIndex + this.tableShowCount
        if(githubResponseObject.length < endIndex)
          endIndex = githubResponseObject.length

        for (let index = startIndex; index < endIndex; index++) {
          let row = table.insertRow();
          let name = row.insertCell(0);
          name.innerHTML = githubResponseObject[index].name
          let description = row.insertCell(1);
          description.innerHTML = githubResponseObject[index].description
          let watchers = row.insertCell(2);
          watchers.innerHTML = githubResponseObject[index].watchers
          let forks = row.insertCell(3);
          forks.innerHTML = githubResponseObject[index]['forks_count']
    
        }
      },
      previousPage() {
        if(this.currentPage > 1)
          this.currentPage--

        this.setCurrentPage()
        this.getGithubsHighestRatedJavascriptRepos()
      },

      setCurrentPage() {
        this.currentPageFooter = 'Page ' + this.currentPage
      },
    },
    mounted() {
      this.getGithubsHighestRatedJavascriptRepos()
      this.setCurrentPage()
    }
  }
</script>
