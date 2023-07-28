<template>
  <div>
    <h1>Issueリスト</h1>
    <el-button type="success" @click="getIssues()">issue取得</el-button>
    <el-row :gutter="12">
      <el-col :span="12"  v-for="( issue, index ) in issues" :key="issue.id">
        <el-card class="box-card" shadow="hover" style="margin: 5px 0">
          <div class="clearfix">
            <el-button style="float: right; margin-top: -5px" @click="closeIssue(index)" type="success" icon="el-icon-check" circle></el-button>
          </div>
          <div>{{ issue.title }}</div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from 'axios'

const client = axios.create({
  baseURL: `${process.env.VUE_APP_GITHUB_ENDPOINT}`,
  headers: {
    // 'Authorization': `token ${process.env.VUE_APP_GITHUB_TOKEN}`,
    'Accept': 'application/vnd.github.v3+json',
    'Content-Type':'application/json',
  },
})

export default {
  name: 'IssueList',
  data () {
    return {
      issues: []
    }
  },
  methods: {
    closeIssue(index){
      this.issues.splice(index, 1);
    },
    getIssues() {
      client.get('/issues')
        .then((res) => {
          this.issues = res.data;
      })
    }
  }
}
</script>
