<template>
  <main>
    
    <v-row>
        <!-- input template -->
        <v-col   >
            <div class="input_template">
                <span class="inputCol"> روابط الحسابات </span>
                <v-select
                    @change="handleChange"
                    :items="texts"
                    label=""
                    v-model="selectLink"
                    
                ></v-select>
            </div>
        </v-col>

        <!-- input template -->
        <v-col  >
            <div class="input_template">
                <span class="inputCol">  الحساب </span>
                <v-select
                    :items="accountsTexts"
                    label=""
                    @change="getSelectLink"
                ></v-select>
            </div>
        </v-col>

        <!-- input template -->
        <v-col  >
            <div class="input_template">
                <span class="inputCol"> من تاريخ </span>
                <v-menu
                    ref="menu1"
                    v-model="menu1"
                    :close-on-content-click="false"
                    :return-value.sync="date1"
                    transition="scale-transition"
                    offset-y
                    min-width="auto"
                >
                    <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                        v-model="date1"
                        prepend-icon="mdi-calendar"
                        readonly
                        v-bind="attrs"
                        v-on="on"
                    ></v-text-field>
                    </template>
                    <v-date-picker
                        v-model="date1"
                        no-title
                        scrollable
                    >
                    <v-spacer></v-spacer>
                    <v-btn
                        text
                        color="primary"
                        @click="menu1 = false"
                    >
                        Cancel
                    </v-btn>
                    <v-btn
                        text
                        color="primary"
                        @click="$refs.menu.save(date1)"
                    >
                        OK
                    </v-btn>
                    </v-date-picker>
                </v-menu>
            </div>
        </v-col>

        <!-- input template -->
        <v-col  >
            <div class="input_template">
                <span class="inputCol"> حتى تاريخ </span>
                <v-menu
                    ref="menu"
                    v-model="menu"
                    :close-on-content-click="false"
                    :return-value.sync="date"
                    transition="scale-transition"
                    offset-y
                    min-width="auto"
                >
                    <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                        v-model="date"
                        prepend-icon="mdi-calendar"
                        readonly
                        v-bind="attrs"
                        v-on="on"
                    ></v-text-field>
                    </template>
                    <v-date-picker
                    v-model="date"
                    no-title
                    scrollable
                    >
                    <v-spacer></v-spacer>
                    <v-btn
                        text
                        color="primary"
                        @click="menu = false"
                    >
                        Cancel
                    </v-btn>
                    <v-btn
                        text
                        color="primary"
                        @click="$refs.menu.save(date)"
                    >
                        OK
                    </v-btn>
                    </v-date-picker>
                </v-menu>
            </div>
        </v-col>

    </v-row>

    <!-- start table Actions -->
    <section class="tableActions d-flex justify-center align-center mt-5">
        <v-btn
            depressed
            color="primary rounded-pill"
            @click="getTableData"
            >
                <v-icon dark>
                    mdi-filter
                </v-icon>
                تصفية
        </v-btn>

        <v-btn
            depressed
            color="green"
            style="color:#fff"
            >
                <v-icon dark>
                    mdi-filter
                </v-icon>
                تقرير مخصص
        </v-btn>

        <v-btn
            class="mx-2"
            fab
            dark
            small
            color="white"
            >
            <v-icon
                color="black"
            >
                mdi-refresh 
            </v-icon>
        </v-btn>
    </section>



    <!-- table content -->
    <section v-if="equalAccount">

        <!-- table header -->
        <div class="header">
            <button class="print">
                <span>طباعة</span>
                <span><i class="fas fa-print"></i></span>
            </button>
            <div class="account-invoice">
                <div> كشف حساب :{{targetAcc}} - {{targetAccText}} </div>
                <div> عن الفترة من : الى </div>
            </div>
        </div>


        <!-- table body -->
        <div class="table_cont">
            <div class="table col-12 mt-5">
                <div class="pos-sticky col-12">
                    <div class="table-header  col-12">
                        <div class="item col-2">الرصيد</div>
                        <div class="item col-2">حركة الحساب</div>
                        <div class="item col-8">رصيد سابق : 0</div>
                    </div>
                    <div class="table-header col-12">
                        <div class="item col-1">مدين</div>
                        <div class="item col-1">الدائن</div>
                        <div class="item col-1">مدين</div>
                        <div class="item col-1">دائن</div>
                        <div class="item col-3">البيان</div>
                        <div class="item col-3">التاريخ</div>
                        <div class="item col-2">رقم المستند</div>
                    </div>
                </div>
            
                <div class="table-body col-12 mb-4" v-for="data in tableData" :key="data.id">
                    <div class="item col-1">{{ data.debit }}</div>
                    <div class="item col-1">  {{ data.credit }} </div>
                    <div class="item col-1">{{ data.balance_debit }}</div>
                    <div class="item col-1"> {{ data.balance_credit }} </div>
                    <div class="item col-3"> {{ data.description }} </div>
                    <div class="item col-3"> {{ data.created_at }} </div>
                    <div class="item col-2">{{ data.reference_id }}</div>
                    <div class="col-12">

                        <div class="col-4"></div>
                        <div class="table-header col-8">
                            <div class="col-5">
                                <div class="item col-4">الباركود</div>
                                <div class="item col-4">الصنف</div>
                                <div class="item col-4">الكمية</div>
                            </div>
                            <div class="item col-5">السعر</div>
                            <div class="item col-1">ض.ق.م</div>
                            <div class="item col-1">القيمة</div>
                        </div>
                    </div>
                    <div class="col-12">
                        <div class="col-4"></div>
                        <div class="table-body col-8" v-for="sale in data.reference.items" :key="sale">
                            <div class="col-5" >
                                <div class="item col-4">{{ sale.serial }}</div>
                                <div class="item col-4">{{sale.title_ar}}</div>
                                <div class="item col-4">{{ sale.quantity }}</div>
                            </div>
                            <div class="item col-5">{{ sale.subtotal }} ريال سعودي</div>
                            <div class="item col-1">{{ sale.tax_percentage }}%</div>
                            <div class="item col-1">{{ sale.total }} </div>

                            
                            <div class="col-10">
                                <div class="item col-6"> اجمالى القيمة</div>
                                <div class="item col-6">{{ data.reference.subtotal }}</div>
                            </div>
                            <div class="col-10">
                                <div class="item col-6">يضاف ضربية القسمة المضافة</div>
                                <div class="item col-6">{{ data.reference.total }}</div>
                            </div>
                            <div class="col-10">
                                <div class="item col-6">اجمالى الفاتورة</div>
                                <div class="item col-6">{{ data.reference.paid_amount }}</div>
                            </div>
                            <div class="col-10">
                                <div class="item col-6">المدفوع</div>
                                <div class="item col-6">{{ data.reference.total_tax }}</div>
                            </div>
                            <div class="col-10">
                                <div class="item col-6">المتبقى</div>
                                <div class="item col-6">{{ data.reference.remaining_amount }}</div>
                            </div>
                            <div class="col-10">
                                <div class="item col-6">التاريخ</div>
                                <div class="item col-6">{{ data.date }}</div>
                            </div>
                        </div>
                    </div>
                </div>
        </div>
        </div>
    </section>
  </main>
</template>

<script>
export default {
    data(){
        return{
            date: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
            menu: false,
            modal: false,
            menu2: false,
            date1 :(new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10) ,
            menu1:false,
 
            // for all account links
            account_links : [],
            // for account linkes text key
            texts : [] ,

            selectLink : [],

            linkName : '',

            // for selected accounts
            selectedAccounts : [],
            // for selected accounts texts
            accountsTexts : [],

            acountIds : [],

            equalAccount : false ,

            // table data
            tableData : [],
            tableAccIds : [],

            targetAcc : null ,
            targetAccText : '',

            // data table info
            dtInfo : {}
        }
    },
    mounted(){
        this.$axios
            .$get("accounts_links")
            .then((res) => {
                this.account_links = res.body.account_link;     
                
                for( let i = 0 ; i < this.account_links.length ; i++){
                    this.texts.push(res.body.account_link[i].text)
                }
                
        });
    } ,

    methods:{
        // first select
        handleChange(e){
            this.linkName = e;
            this.getSelectLink()
        },

        // second select
        getSelectLink(e){
            
            this.$axios
                .$get("single_account")
                .then((res) => {
                    // check if account name is the same in the previous select
                    if( this.linkName == res.body.account_link.text ){
                        this.selectedAccounts = res.body.accounts;

                        for( let x = 0 ; x < res.body.accounts.length ; x++){

                            this.accountsTexts.push(res.body.accounts[x].text)
                            this.acountIds.push(res.body.accounts[x].account)

                            if( e == res.body.accounts[x].text ){
                                // the specific target account
                                this.targetAcc = res.body.accounts[x].account
                                this.targetAccText = res.body.accounts[x].text
                            }
                            // this.
                        }

                    }else{
                        this.accountsTexts = []
                    }
            });


        },

        // call table after button click
        getTableData(){
            this.$axios
            .get('single_account_table')
            .then( (res)=>{

                    this.tableData = res.data.body.report.transaction.data

                    this.dtInfo = res.data.body.report.transaction.data.reference

                    console.log(res.data.body.report.transaction.data.reference)

                for( let x = 0 ; x < res.data.body.report.transaction.data.length ; x++ ){
                    this.tableAccIds.push(res.data.body.report.transaction.data[x].account)
                }


                // check if the target account is included in the table data
                if(   this.tableAccIds.includes(this.targetAcc) ){
                    
                    this.equalAccount = true





                    this.$swal.fire({
                        title: 'تم عرض البيانات بنجاح',
                        timer: 1000,
                        icon: "success",
                        allowOutsideClick: false,
                        showConfirmButton: false,
                        showCancelButton: false,
                    });

                }else{
                    this.equalAccount = false

                    this.$swal.fire({
                        title: 'برجاء اختيار حساب اخر !',
                        timer: 1000,
                        icon: "error",
                        allowOutsideClick: false,
                        showConfirmButton: false,
                        showCancelButton: false,
                    });
                }

            } )
        }

    }

         
}

</script>

<style lang="scss" scoped>
.input_template{
    height: 40px;
    border-radius: 25px;
    border: 1px solid #ccc;
    display: flex;
    align-items: center;
    padding: 20px 10px !important;
    .inputCol{
        position: relative;
        color: #333;
        width: 150px;
        &::before{
            content: '';
            position: absolute;
            left: -10px;
            top: 4px;
            height: 20px;
            width: 1px;
            background-color: #ccc;
        }
    }
}
.theme--light.v-text-field > .v-input__control > .v-input__slot:before{  display: none !important; }
.v-input{ padding-right: 25px !important; }
.tableActions button{
    margin: 0 15px !important;
}
.tableActions:first-child{
    border-radius: 30px ;
}






.col-1 {
  width: 8.3333333333%;
  flex: 0 0 auto;
  display: flex;
  flex-wrap: wrap;
}

.col-2 {
  width: 16.6666666667%;
  flex: 0 0 auto;
  display: flex;
  flex-wrap: wrap;
  padding: 10px 0 !important;
}

.col-3 {
  width: 25%;
  flex: 0 0 auto;
  display: flex;
  flex-wrap: wrap;
}

.col-4 {
  width: 33.3333333333%;
  flex: 0 0 auto;
  display: flex;
  flex-wrap: wrap;
    padding: 6px 0 !important;
}

.col-5 {
  width: 41.6666666667%;
  flex: 0 0 auto;
  display: flex;
  flex-wrap: wrap;
}

.col-6 {
  width: 50%;
  flex: 0 0 auto;
  display: flex;
  flex-wrap: wrap;
  padding: 6px 0 !important;
}

.col-7 {
  width: 58.3333333333%;
  flex: 0 0 auto;
  display: flex;
  flex-wrap: wrap;
}

.col-8 {
  width: 66.6666666667%;
  flex: 0 0 auto;
  display: flex;
  flex-wrap: wrap;
}

.col-9 {
  width: 75%;
  flex: 0 0 auto;
  display: flex;
  flex-wrap: wrap;
}

.col-10 {
  width: 83.3333333333%;
  flex: 0 0 auto;
  display: flex;
  flex-wrap: wrap;
}

.col-11 {
  width: 91.6666666667%;
  flex: 0 0 auto;
  display: flex;
  flex-wrap: wrap;
}

.col-12 {
  width: 100%;
  flex: 0 0 auto;
  display: flex;
  flex-wrap: wrap;
}

body {
  direction: rtl;
  text-align: right;
  font-family: "Cairo", sans-serif;
  font-size: 16px;
}
@media (max-width: 567px) {
  body {
    font-size: 12px;
  }
}

.pos-sticky {
  position: -webkit-sticky;
  position: sticky;
  top: 0;
}

.table-container {
  margin: auto;
  width: 100%;
  height: 50vh;
  overflow-y: scroll;
  overflow-x: hidden;
  position: relative;
}

.table {
  width: 95%;
  margin: 25px auto;
  height: 286px;
  overflow-y: auto;
}
@media( max-width:600px ){
    .table{
        min-width: 700px;
        overflow-x:auto ;
    }
    .table_cont{overflow: auto;}
}
.table.float-left {
  float: left;
}
.table .item {
  justify-content: center;
  align-items: center;
  border: 1px solid #ccc;
}

.table-body {
  display: flex;
  flex-wrap: wrap;
}
.table-body .item:nth-child(odd) {
  background-color: #edededd1;
}
.table-body .item:nth-child(even) {
  background-color: #FFF;
}

.table-header {
  color: #FFF;
  display: flex;
  flex-wrap: wrap;
}
.table-header .item {
  background-color: #2e2eb7 !important;
}

/* width */
::-webkit-scrollbar {
  width: 7px;
}

/* Track */
::-webkit-scrollbar-track {
  background: #f1f1f1;
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: #888;
}

::-webkit-scrollbar-thumb:hover {
  background: #555;
}

button.print {
  border-radius: 15px;
  -webkit-border-radius: 15px;
  -moz-border-radius: 15px;
  -ms-border-radius: 15px;
  -o-border-radius: 15px;
  background: linear-gradient(to bottom, #03b5bc, rgba(3, 181, 188, 0.3098039216));
  padding: 10px 15px;
  color: white;
  font-weight: bold;
  border: none;
}

.header {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin: 10px 0;
  gap: 10px;
}

button:hover {
  box-shadow: 0 0 0px 5px rgba(3, 181, 188, 0.3098039216);
}

.account-invoice {
  display: flex;
}
.account-invoice :first-child {
  background-color: #2e2eb7;
  color: white;
  padding: 3px 15px;
  border-radius: 15px;
  -webkit-border-radius: 15px;
  -moz-border-radius: 15px;
  -ms-border-radius: 15px;
  -o-border-radius: 15px;
  margin-left: 10px;
}
 .col-12, .col-11, .col-10, .col-9, .col-8, .col-7, .col-6, .col-5, .col-4, .col-3, .col-2, .col-1{padding: 0px ;}

.theme--light.v-text-field > .v-input__control > .v-input__slot:before{display: none !important;}
</style>

