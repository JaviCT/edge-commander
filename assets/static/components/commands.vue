<template>
  <div>
    <div class="m-content">
      <div class="m-portlet m-portlet--mobile" style="margin-bottom: 0">
        <div class="m-portlet__body" style="padding: 10px;">
          <!--begin: Search Form -->
          <div class="m-form m-form--label-align-right m--margin-bottom-10">
            <div class="row align-items-center">
              <div class="col-md-8 order-2 order-md-1">
                <div class="form-group m-form__group row align-items-center">
                  <div class="col-md-5">
                    <div class="m-input-icon m-input-icon--left">
                      <input type="text" class="form-control m-input m-input--solid" placeholder="Search..." id="m_form_search" v-model="m_form_search" v-on:keyup="search()">
                      <span class="m-input-icon__icon m-input-icon__icon--left">
                        <span>
                          <i class="la la-search"></i>
                        </span>
                      </span>
                    </div>
                  </div>
                </div>
              </div>
              <div class="col-md-4 order-1 order-md-2 m--align-right">
                 <a href="javascript:void(0)" id="addRULE" class="btn btn-primary m-btn m-btn--icon" v-on:click="onRuleButton">
                      <span>
                          <i class="fa fa-plus-square"></i>
                          <span>
                              {{form_labels.add_rule_button}}
                          </span>
                      </span>
                  </a>
                  <div href="javascript:void(0)" class="btn btn-default grey" v-on:click="onRuleHideShowButton">
                    <i class="fa fa-columns"></i>
                  </div>
              </div>
            </div>
          </div>
          <!--end: Search Form -->
          <div>
          </div>
            <table id="commands-datatable" class="table table-striped  table-hover table-bordered display nowrap" cellspacing="0" width="100%">
                <thead>
                    <tr>
                        <th v-for="(item, index) in headings">{{item.column}}</th>
                    </tr>
                </thead>
            </table>
        </div>
      </div>
    </div>
    <!-- begin::modal -->
    <div class="modal fade toggle-datatable-columns" ref="hideShow" style="padding: 0px;" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true" data-backdrop="static" data-keyboard="false">
      <div class="modal-dialog modal-sm" role="document">
          <div class="modal-content" style="padding: 0px;">
              <div class="modal-header">
                  <h5 class="modal-title" id="exampleModalLabel">
                      {{form_labels.hide_show_title}}
                  </h5>
                  <div class="cancel">
                    <a href="#" id="discardModal" data-dismiss="modal" v-on:click="clearForm">X</a>
                  </div>
              </div>
              <div class="modal-body" id="body-sim-dis">
                  <div class="form-group">
                    <div class="column-checkbox" v-for="(item, index) in headings">
                        <label class="m-checkbox m-checkbox--single m-checkbox--solid m-checkbox--brand" style="width: auto;"><input type="checkbox" class="rule-column" v-bind:data-id="item.id" v-on:change="showHideColumns(item.id)"><span></span> {{item.column}}</label>
                    </div>
                  </div>
              </div>
              <div class="modal-footer">
                <button type="button" class="btn btn-default" data-dismiss="modal">{{form_labels.hide_show_button}}</button>
              </div>
          </div>
      </div>
    </div>
    <div class="modal fade add_rule_to_db"  ref="addmodal" style="padding: 0px;" id="m_modal_1" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true" data-backdrop="static" data-keyboard="false">
        <div class="modal-dialog" role="document">
            <div class="modal-content" style="padding: 0px;">
                <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel">
                       {{form_labels.add_title}}
                    </h5>
                    <div class="cancel">
                        <a href="#" id="discardModal" data-dismiss="modal" v-on:click="clearForm">X</a>
                    </div>
                </div>
                <div class="modal-body" id="body-rule-dis">
                    <img src="/images/loading.gif" id="api-wait" v-if="show_loading">
                    <div id="ruleErrorDetails"  v-if="show_add_errors">
                        <div class="form-group m-form__group m--margin-top-10">
                            <div class="alert m-alert m-alert--default" role="alert">
                                <ul style="margin:0px">
                                  <li v-for="message in show_add_messages">{{message}}</li>
                                </ul>
                            </div>
                        </div>
                    </div>
                    <div class="m-form m-form--fit m-form--label-align-left">
                        <input type="hidden" id="user_id" v-model="user_id">
                        <div class="form-group m-form__group row">
                            <label class="col-3 col-form-label">
                                {{form_labels.name}}
                            </label>
                            <div class="col-9">
                                <input type="text" class="form-control m-input m-input--solid" id="rule_name" aria-describedby="emailHelp" placeholder="Test Usage." v-model="rule_name">
                            </div>
                        </div>
                        <div class="form-group m-form__group row">
                            <label class="col-3 col-form-label">
                                {{form_labels.category}}
                            </label>
                            <div class="col-9">
                                <select class="form-control m-input" id="rule_category" v-model="rule_category">
                                    <option value="usage_command">Internet usage > 90%</option>
                                    <option value="daily_sms_usage_command">Daily SMS > 6</option>
                                    <option value="monthly_sms_usage_command">Monthy SMS > 190</option>
                                </select>
                            </div>
                        </div>
                        <div class="form-group m-form__group row">
                            <label class="col-3 col-form-label">
                                {{form_labels.recipients}}
                            </label>
                            <div class="col-9">
                                <input type="text" class="form-control m-input m-input--solid" id="rule_recipients" aria-describedby="emailHelp" placeholder="test@user.com,who@am.io" v-model="rule_recipients">
                            </div>
                        </div>
                        <div class="form-group m-form__group row">
                            <label class="col-3 col-form-label">
                            </label>
                            <div class="col-9">
                                <label class="m-checkbox">
                                    <input type="checkbox" id="rule_is_active" v-model="rule_is_active">
                                        {{form_labels.status}}
                                    <span></span>
                                </label>
                            </div>
                        </div>
                    </div>
                    <!--end::Form-->
                </div>
                <div class="modal-footer" style="padding: 11px;">
                    <button id="" type="button" class="btn btn-default" v-on:click="saveModal">
                        {{form_labels.submit_button}}
                    </button>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="edit_rule_to_db" ref="editmodal" style="padding: 0px;" data-backdrop="static" data-keyboard="false">
        <div class="modal-dialog" role="document">
            <div class="modal-content" style="padding: 0px;">
                <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel">
                        {{form_labels.edit_title}}
                    </h5>
                    <div class="cancel">
                        <a href="#" id="discardEditModal" data-dismiss="modal" v-on:click="editClearFrom">X</a>
                    </div>
                </div>
                <div class="modal-body" id="body-rule-edit-dis">
                    <img src="/images/loading.gif" id="api-wait"  v-if="show_loading">
                    <div id="ruleEditErrorDetails"  v-if="show_edit_errors">
                        <div class="form-group m-form__group m--margin-top-10">
                            <div class="alert m-alert m-alert--default" role="alert">
                                <ul style="margin:0px">
                                  <li v-for="message in show_edit_messages">{{message}}</li>
                                </ul>
                            </div>
                        </div>
                    </div>
                    <div class="m-form m-form--fit m-form--label-align-left">
                        <input type="hidden" id="user_id" v-model="user_id">
                        <input type="hidden" id="edit_rule_id" >
                        <div class="form-group m-form__group row">
                            <label class="col-3 col-form-label">
                                {{form_labels.name}}
                            </label>
                            <div class="col-9">
                                <input type="text" class="form-control m-input m-input--solid" id="edit_rule_name" aria-describedby="emailHelp" placeholder="Test Usage.">
                            </div>
                        </div>
                        <div class="form-group m-form__group row">
                            <label class="col-3 col-form-label">
                                {{form_labels.category}}
                            </label>
                            <div class="col-9">
                                <select class="form-control m-input" id="edit_rule_category">
                                    <option value="usage_command">Internet usage > 90%</option>
                                    <option value="daily_sms_usage_command">Daily SMS > 6</option>
                                    <option value="monthly_sms_usage_command">Monthy SMS > 190</option>
                                </select>
                            </div>
                        </div>
                        <div class="form-group m-form__group row">
                            <label class="col-3 col-form-label">
                                {{form_labels.recipients}}
                            </label>
                            <div class="col-9">
                                <input type="text" class="form-control m-input m-input--solid" id="edit_rule_recipients"aria-describedby="emailHelp" placeholder="test@user.com,who@am.io">
                            </div>
                        </div>
                        <div class="form-group m-form__group row">
                            <label class="col-3 col-form-label">
                            </label>
                            <div class="col-9">
                                <label class="m-checkbox">
                                    <input type="checkbox" id="edit_rule_is_active">
                                    {{form_labels.status}}
                                    <span></span>
                                </label>
                            </div>
                        </div>
                    </div>
                    <!--end::Form-->
                </div>
                <div class="modal-footer" style="padding: 11px;">
                    <button id="" type="button" class="btn btn-default" v-on:click="updateRule">
                        {{form_labels.submit_button}}
                    </button>
                </div>
            </div>
        </div>
    </div>
    <!--end::Modal-->
  </div>
</template>

<script>
import Vue from 'vue'
import App from './App.vue'
const app = new Vue(App)

module.exports = {
  name: 'commands',
  data: function(){
    return{
      dataTable: null,
      m_form_search: "",
      show_loading: false,
      show_add_errors: false,
      show_edit_errors: false,
      show_edit_messages: "",
      show_add_messages: "",
      headings: [
        {column: "Actions", id: "actions"},
        {column: "Rule Name", id: "rule_name"},
        {column: "Active", id: "active"},
        {column: "Category", id: "category"},
        {column: "Recipients", id: "recipients"},
        {column: "Created At", id: "created_at"}
      ],
      form_labels: {
        name: "Rule Name",
        category: "Category",
        recipients: "Recipients",
        status: "Active",
        submit_button: "Save changes",
        edit_title: "Edit Rule",
        add_title: "Add Rule",
        hide_show_title: "Show/Hide Columns",
        add_rule_button: "Add Rule",
        hide_show_button: "OK"
      },
      edit_rule_name: "",
      edit_rule_id: "",
      edit_rule_category: "",
      edit_rule_recipients: "",
      edit_rule_is_active: false,
      rule_name: "",
      rule_category: "",
      rule_recipients: "",
      rule_is_active: false,
      user_id: ""
    }
  },
  methods: {
    initializeTable: function(){
      let commandsDataTable = $('#commands-datatable').DataTable({
        fnInitComplete: function(){
          // Enable TFOOT scoll bars
          $('.dataTables_scrollFoot').css('overflow', 'auto');
          $('.dataTables_scrollHead').css('overflow', 'auto');
          // Sync TFOOT scrolling with TBODY
          $('.dataTables_scrollFoot').on('scroll', function () {
          $('.dataTables_scrollBody').scrollLeft($(this).scrollLeft());
        });
        $('.dataTables_scrollHead').on('scroll', function () {
          $('.dataTables_scrollBody').scrollLeft($(this).scrollLeft());
        });
      },
      ajax: {
      url: "/rules",
        dataSrc: function(data) {
          return data.rules;
        },
        error: function(xhr, error, thrown) {
          if (xhr.responseJSON) {
            console.log(xhr.responseJSON.message);
          } else {
            console.log("Something went wrong, Please try again.");
          }
        }
      },
      columns: [
      {
        class: "text-center actions",
        data: function(row, type, set, meta) {
          return '<div id="action_btn"><div class="editRULE cursor_to_pointer fa fa-edit" data-id="'+ row.id +'"></div> <div class="cursor_to_pointer fa fa-trash delRule" data-id="'+ row.id +'"></div></div>';
        }
      },
      {
        class: "rule_name",
        data: function(row, type, set, meta) {
          return row.rule_name;
        }
      },
      {
        class: "text-center active",
        data: function(row, type, set, meta) {
          return row.active;
        }
      },
      {
        class: "text-center category",
        data: function(row, type, set, meta) {
          return row.category;
        }
      },
      {
        class: "recipients",
        data: function(row, type, set, meta) {
          return row.recipients;
        }
      },
      {
        class: "text-center created_at",
        data: function(row, type, set, meta) {
          return moment(row.created_at).format('DD/MM/YYYY HH:mm:ss');
        },
      },
      ],
      autoWidth: true,
      info: false,
      bPaginate: false,
      lengthChange: false,
      scrollX: true,
      colReorder: true,
      stateSave:  true
    });
      return this.dataTable = commandsDataTable;
      this.dataTable.search("");
   },
   search: function(){
      this.dataTable.search(this.m_form_search).draw();
   },
   getUniqueIdentifier: function(commandsDataTable){
      $(document).on("click", ".editRULE", function(){
        var tr = $(this).closest('tr');
        var row = commandsDataTable.row(tr);
        var data = row.data();
        let rule_id = $(this).data("id");
        module.exports.methods.onRuleEditButton(rule_id, data);
      });
   },
   onRuleEditButton: function(rule_id, data){
      $("#edit_rule_name").val(data.rule_name)
      $("#edit_rule_id").val(rule_id)
      $("#edit_rule_category").val(data.category)
      $("#edit_rule_recipients").val(data.recipients)
      if (data.active == true) {
        $("#edit_rule_is_active").prop( "checked", true );
      }
      $('#edit_rule_to_db').modal('show');
   },
   showHideColumns: function(id){
    var column = this.dataTable.columns("." +id);
    if(column.visible()[0] == true){
      column.visible(false);
    }else{
      column.visible(true);
    }
   },
   onRuleButton: function(){
    $(this.$refs.addmodal).modal("show");
   },
   onRuleHideShowButton: function(){
    $(this.$refs.hideShow).modal("show");
   },
   clearForm: function(){
    this.rule_name = "";
    this.rule_category = "";
    this.rule_recipients = "";
    this.rule_is_active = false;
    this.show_add_errors = false;
   },
   saveModal: function(){
    this.show_loading = true;
    this.show_add_errors = true;

    let recipients = this.rule_recipients
    if (recipients != "") {
      recipients = recipients.split(",");
    }else{
      recipients = "";
    }

    this.$http.post('/rules/new', {
      rule_name: this.rule_name,
      user_id:  this.user_id,
      category: this.rule_category,
      recipients: recipients,
      is_active: this.rule_is_active
    }).then(function (response) {
      app.$notify({group: 'notify', title: 'Rule has been added'});
      this.show_loading = false;
      this.dataTable.ajax.reload();
      this.clearForm();
      $(this.$refs.addmodal).modal("hide");
    }).catch(function (error) {
      this.show_add_messages = error.body.errors;
      this.show_add_errors = true;
      this.show_loading = false;
    });
   },
   updateRule: function(){
    this.show_loading = true;
    this.show_edit_errors = true;

    var ruleID = $("#edit_rule_id").val();

    let recipients = $("#edit_rule_recipients").val();
    if (recipients != "") {
        recipients = recipients.split(",");
    }else{
      recipients = "";
    }

    this.$http.patch('/rules/update', {
      rule_name: $("#edit_rule_name").val(),
      category: $("#edit_rule_category").val(),
      recipients: recipients,
      active: $('#edit_rule_is_active').is(':checked'),
      id: ruleID
    }).then(function (response) {
      app.$notify({group: 'notify', title: 'Rule has been updated'});
      this.show_loading = false;
      this.dataTable.ajax.reload();
      this.editClearFrom();
      $(this.$refs.editmodal).modal("hide");
    }).catch(function (error) {
      this.show_loading = false;
      this.show_edit_messages = error.body.errors;
      this.show_edit_errors = true;
    });

   },
   editClearFrom: function() {
    this.show_edit_errors = false;
    this.edit_rule_name = ""
    this.edit_rule_recipients = ""
    this.edit_rule_is_active = false
   },
    initHideShow: function(){
      $(".rule-column").each(function(){
        var that = $(this).attr("data-id");
        let commandsDataTable = $('#commands-datatable').DataTable();
        var column = commandsDataTable.columns("." +that);
        if(column.visible()[0] == true){
          $(this).prop('checked', true);
        }else{
          $(this).prop('checked', false);
        }
      });
    },
    get_session: function(){
      this.$http.get('/get_porfile').then(response => {
        this.user_id = response.body.id;
      });
    },
    deleteRule: function(){
    $(document).off("click").on("click", ".delRule", function(){
      let ruleRow, result;
      ruleRow = $(this).closest('tr');
      let ruleID = $(this).data("id");

      result = confirm("Are you sure to delete this Rule?");
      if (result === false) {
        return;
      }

      app.$http.delete("/rules/" + ruleID, {ruleRow: ruleRow}).then(function (response) {
        ruleRow.remove();
        app.$notify({group: 'notify', title: 'Rule has been deleted'});
      }).catch(function (error) {
         return false
      });
    });
   },
   select_menu_link: function(){
     $("li").removeClass(" m-menu__item--active");
     $(".commands").addClass(" m-menu__item--active");
   }
  }, // end of methods
   mounted(){
    this.deleteRule();
    let table = this.initializeTable();
    this.getUniqueIdentifier(table);
    this.get_session();
    this.initHideShow();
    this.dataTable.search("");
    this.select_menu_link();
   }
}
</script>

<style lang="scss">
</style>
