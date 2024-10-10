<template>
  <div class="container">
    <ejs-querybuilder ref="querybuilder" id="querybuilder" 
      :rule="importRules"
      :headerTemplate="'headerTemplate'">
      <e-columns>
        <e-column field="EmployeeID" label="Employee ID" type="number" />
        <e-column field="FirstName" label="First Name" type="string" />
        <e-column field="LastName" label="Last Name" type="string" />
        <e-column field='TitleOfCourtesy' label='Title Of Courtesy' type='string'
          :template="'columnTemplate'" />
        <e-column field="HireDate" label="Hire Date" type="date" format="dd/MM/yyyy" />
        <e-column field="Country" label="Country" type="string"
          :ruleTemplate="'ruleTemplate'"/>
      </e-columns>
      <template #headerTemplate="{ data }">
        <div class="query-template-control">
          <div class="e-groupheader">
            <ejs-dropdownlist cssClass="e-custom-group-btn"
              :id="`${data.ruleID}_cndtn`" v-model="data.condition"
              :dataSource="grpHdrOperatorItems"
              :fields="grpHdrOperatorfields"
              :change="conditionChange">
            </ejs-dropdownlist>
            <div class="e-header">
              <div class="e-qb-hdr-content">
                <ejs-button :id="`${data.ruleID}_addgrp`" 
                  cssClass="e-primary e-grp-btn"
                  @click="grpbtnClick">Add Group</ejs-button>
              </div>
              <div class="e-qb-hdr-content">
                <ejs-button :id="`${data.ruleID}_addrule`" cssClass="e-primary e-cond-btn"
                 @click="rulebtnClick">Add Condition</ejs-button>
              </div>
              <div class="e-qb-hdr-content">
                <ejs-button :id="`${data.ruleID}_dltbtn`" cssClass="e-danger"
                  v-if="data.ruleID !== 'querybuilder_group0'"
                  @click="onDeleteGrpBtnClick"
                  >Delete Group</ejs-button>
              </div>
            </div>
          </div>
        </div>
      </template>
      <template #columnTemplate="{ data }">
        <ejs-dropdownlist :dataSource="['Mr.', 'Mrs.', 'Dr.', 'Ms.']"
          v-model="data.rule.value" :change="onTitleChange">
        </ejs-dropdownlist>
      </template>
      <template #ruleTemplate="{ data }">
        <div>
        <div class="e-rule-filter">
          <ejs-dropdownlist :dataSource="data.columns" :fields="data.fields"
            :value="data.rule.field" :change="fieldChange">
            </ejs-dropdownlist>
        </div>
        <div class="e-rule-operator e-operator">
            <ejs-radiobutton label="Is Equal" value="equal" name="operator" 
              checked=true :change="operatorChange"></ejs-radiobutton>
            <ejs-radiobutton label="Is Not Equal" value="notequal" name="operator"
            :change="operatorChange"></ejs-radiobutton>
          </div>
          <div class="e-rule-value e-value e-custom-value">
            <ejs-dropdownlist :value="data.rule.value" 
              :dataSource="countryItems" :fields="countryFields"
              :change="valueChange">
            </ejs-dropdownlist>
          </div>
          <div class="e-rule-value-delete">
            <button 
              class="e-removerule e-custom-delete e-rule-delete e-css e-btn e-small e-round"
              title="Delete Rule">
              <span class="e-btn-icon e-icons e-delete-icon"></span>
            </button>
          </div>
        </div>
      </template>
    </ejs-querybuilder>
  </div>
</template>
<script>
import {
  QueryBuilderComponent,
  ColumnDirective,
  ColumnsDirective
} from "@syncfusion/ej2-vue-querybuilder";
import { DropDownListComponent } from "@syncfusion/ej2-vue-dropdowns";
import { ButtonComponent, RadioButtonComponent } from "@syncfusion/ej2-vue-buttons";
import { closest } from "@syncfusion/ej2-base";

export default {
  components: {
    "ejs-querybuilder": QueryBuilderComponent,
    "e-columns": ColumnsDirective,
    "e-column": ColumnDirective,
    "ejs-dropdownlist": DropDownListComponent,
    "ejs-button": ButtonComponent,
    "ejs-radiobutton": RadioButtonComponent
  },
  data() {
    return {
      grpHdrOperatorItems: [{ key: "AND", value: "and"},
        { key: "OR", value: "or"}
      ],
      grpHdrOperatorfields: { text: "key", value: "value" },
      countryItems: [{ field: 'USA', label: 'USA' }, { field: 'England', label: 'England' }, 
        { field: 'India', label: 'India' }, { field: 'Spain', label: 'Spain' }],
      countryFields: { text: 'field', value: 'label' },
      importRules: {
        condition: "and",
        rules: [
          { label: "First Name", field: "FirstName", type: "string", 
            operator: "equal", value: "Nancy" },
          { label: "Title Of Courtesy", field: "TitleOfCourtesy", type: "string", 
            operator: "equal", value: "Ms." },
          { label: "Country", field: "Country", type: "string", 
            operator: "equal", value: "USA" }
        ]
      }
    };
  },
  methods:{
    conditionChange(event){
      this.$refs.querybuilder.ej2Instances.notifyChange(
        event.value, event.element, "condition"
      );
    },
    grpbtnClick({target}){
      const targetParentGroupID = target.id.split("_")[1];
      this.$refs.querybuilder.ej2Instances.addGroups(
        [{ condition: "or", rules: [{}]}], targetParentGroupID
      );
    },
    rulebtnClick({target}){
      const targetParentGroupID = target.id.split("_")[1];
      this.$refs.querybuilder.ej2Instances.addRules(
        [{}], targetParentGroupID
      );
    },
    onDeleteGrpBtnClick({target}){
      this.$refs.querybuilder.ej2Instances.deleteGroup(
        closest(target, ".e-group-container")
      );
    },
    onTitleChange(event){
      this.$refs.querybuilder.ej2Instances.notifyChange(
        event.itemData.value, event.element
      );
    },
    fieldChange(event){
      this.$refs.querybuilder.ej2Instances.notifyChange(
        event.value, event.element, "field"
      );
    },
    operatorChange(operatorArgs){
      this.$refs.querybuilder.ej2Instances.getRule(
        operatorArgs.event.target).operator = operatorArgs.value;
    },
    valueChange(event){
      this.$refs.querybuilder.ej2Instances.notifyChange(
        event.value, event.element, "value"
      );
    }
  }
};
</script>
<style>
@import "../node_modules/@syncfusion/ej2-base/styles/material.css";
@import "../node_modules/@syncfusion/ej2-buttons/styles/material.css";
@import "../node_modules/@syncfusion/ej2-splitbuttons/styles/material.css";
@import "../node_modules/@syncfusion/ej2-dropdowns/styles/material.css";
@import "../node_modules/@syncfusion/ej2-inputs/styles/material.css";
@import "../node_modules/@syncfusion/ej2-lists/styles/material.css";
@import "../node_modules/@syncfusion/ej2-popups/styles/material.css";
@import "../node_modules/@syncfusion/ej2-calendars/styles/material.css";
@import "../node_modules/@syncfusion/ej2-navigations/styles/material.css";
@import "../node_modules/@syncfusion/ej2-vue-querybuilder/styles/material.css";

.container {
  display: flex;
  flex-direction: column;
  width: 60%;
  margin: 0 auto;
  position: absolute;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
}
.e-query-builder .query-template-control span.e-custom-group-btn {
  max-width: 70px;
  border-radius: 5px !important;
  border-width: 1px !important;
}
.e-query-builder .query-template-control .e-custom-group-btn {
  padding-left: 10px;
  height: 32px;
}
.e-query-builder .query-template-control .e-header {
  float: right;
}

.e-query-builder .query-template-control .e-qb-hdr-content {
  display: inline-block;
  padding: 5px;
}

.e-query-builder .query-template-control .e-grp-btn,
.e-query-builder .query-template-control .e-cond-btn {
  background-color: #6c757d !important;
}

.e-query-builder .query-template-control .e-grp-btn:hover,
.e-query-builder .query-template-control .e-cond-btn:hover {
  background-color: #545c63 !important;
}
</style>