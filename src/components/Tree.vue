<template>
  <div class="container">
    <tree
      :data="tree"
      node-text="name"
      layoutType="euclidean"
      type="tree"
      :zoomable="true"
      :duration="0"
      :radius="7"
    ></tree>
  </div>
</template>

<script>
import { tree } from 'vued3tree';
import { mapState } from 'vuex';
export default {
  name: 'Tree',
  components: {
    tree
  },
  computed: {
    ...mapState(['componentMap']),
    componentMap: {
      get() {
        return this.$store.state.componentMap;
      }
    }
  },
  data() {
    return {
      tree: null
    };
  },
  methods: {
    formatComponentMap(compMap) {
      console.log('\n Map : ', compMap, '\n');
      let result = [];
      Object.values(compMap).forEach(compData => {
        result.push({
          name: compData.componentName,
          children: compData.children
        });
      });
      return result;
    },
    transformToTree(data) {
      let result = {};
      const nodes = {};
      const formattedData = this.formatComponentMap(data);
      
      // console.log('\n >>>> Formatted data <<<<');
      // console.log('FormattedData: ', formattedData, '\n');
      
      // console.log('\n >>>> TRANSFORM TO TREE <<<< \n');

      formattedData.forEach(component => {
        if (!nodes[component.name]) {
          nodes[component.name] = { name: component.name, children: [] };
          result = nodes;
        }
        // console.log('CURRENT COMPONENT: ', component.name);
        component.children.forEach(child => {
          // if(typeof child === 'object') child = child.componentName;
          nodes[child] = { name: child, children: [] };
          nodes[component.name].children.push(nodes[child]);
          // console.log('Adding child: ', typeof child, child);
          // console.log('\n');
        });
      });

      console.log('\n >>>> RESULTS <<<< ');
      console.log(result);
      console.log('\n >>>> ______ <<<<');
      return result;
    },

    buildTree() {
      let build = this.transformToTree(this.componentMap);
      this.tree = build['App'];
    }
  },
  created() {
    this.buildTree();
  }
};
</script>
<style>
.container {
  height: 600px;
  width: 100%;
}

.treeclass .nodetree text {
  font: 19px Roboto !important;
  text-transform: uppercase !important;
  cursor: pointer;
  text-shadow: none !important;
  background-color: red !important;
  font-weight: bold;
  color: white !important;
}

/* .treeclass .node--internal text {
  color: white;
} */
</style>
