<template>

    <div class="container">
        <div v-if="!Array.isArray(orders) || !orders.length" class="row h-100 d-flex justify-content-center">
            <div class="col-md-8 text-center align-self-center">
                <h1>شما تاکنون هیچ سفارشی ثبت نکرده اید</h1>
                <button v-on:click="$router.push({ path: '/dashboard/new-order' })" class="btn btn-info btn-lg mt-5">
                    <span>ثبت سفارش جدید</span>&nbsp;&nbsp;<i class="fas fa-plus"></i></button>
            </div>

        </div>

        <div v-if="pagesLength > 1" class="row d-flex justify-content-center px-2 pt-3 pb-1">
            <div class="col-12 col-md-6 bg-light rounded shadow-lg p-0">
                <pagination class="my-2 mx-1" dir="rtl"
                        :pagesCount="pagesLength"
                        :currentPage="currentPage"
                        v-on:select="currentPage = $event"/>
            </div>
        </div>

        <div v-if="Array.isArray(orders) && orders.length" class="row h-auto p-2 px-lg-0" dir="rtl">

            <div class="col-lg-6 p-2" v-for="(order, index) in pages[currentPage-1]" v-bind:key="index">
                <div class="card shadow shadow-sm card-hover-blue"
                     @click='HandleModal(order)'>
                    <div class="card-body">
                        <div class="d-flex justify-content-between">
                            <div class="h5">سفارش {{ order['number_order'] }}</div>
                            <div>{{ order['order_status'] }}</div>
                        </div>
                        <div class="container mt-2">
                            <div class="row">
                                <div class="col-lg-3 text-center text-lg-right font-weight-bold">دریافت :</div>
                                <div class="col-lg-9 text-center">{{ order['date_pickup_user'] + " ساعت " +
                                    order['time_pickup_user'].split(':')[0] + "تا" +
                                    order['time_pickup_user'].split(':')[1] }}
                                </div>
                            </div>

                            <div class="row">
                                <div class="col-lg-3 text-center text-lg-right font-weight-bold mt-3 mt-lg-0">تحویل :
                                </div>
                                <div class="col-lg-9 text-center">{{ order['date_delivery_user'] + " ساعت " +
                                    order['time_delivery_user'].split(':')[0] + "تا" +
                                    order['time_delivery_user'].split(':')[1] }}
                                </div>
                            </div>
                        </div>
                        <div class="col-12"></div>
                    </div>
                </div>
            </div>

        </div>

        <!-- Modal -->
        <div class="modal fade" id="order_info_modal" tabindex="-1" role="dialog"
             aria-labelledby="order_info_modal_Title" aria-hidden="true">
            <div class="modal-dialog modal-lg" role="document">
                <div class="modal-content">
                    <div class="p-3 border-bottom">
                        <div class="row">
                            <div class="col-12  d-flex justify-content-between">
                                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                    <span aria-hidden="true">&times;</span>
                                </button>
                                <h5 class="modal-title text-right" id="order_info_modal_Title">{{ modal_title }}</h5>
                            </div>
                        </div>
                    </div>
                    <div class="modal-body">


                        <div class="card border border-dark">
                            <div class="card-body" dir="rtl">
                                <div class="row">
                                    <div class="col-lg-3 text-center text-lg-right font-weight-bold mb-2 mb-lg-0">تاریخ
                                        و ساعت دریافت :
                                    </div>
                                    <div id="order_info_modal_pickup" class="col-lg-9 text-center text-lg-left">
                                        {{ pickup_info }}
                                    </div>
                                </div>

                                <div class="dropdown-divider"></div>

                                <div class="row">
                                    <div class="col-lg-3 text-center text-lg-right font-weight-bold mb-2 mb-lg-0">تاریخ
                                        و ساعت تحویل :
                                    </div>
                                    <div id="order_info_modal_delivery" class="col-lg-9 text-center text-lg-left">
                                        {{ delivery_info }}
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="card border border-dark mt-3">
                            <div class="card-body" dir="rtl">
                                <div class="row">
                                    <div class="col-lg-5 text-center text-lg-right font-weight-bold mb-2 mb-lg-0">وضعیت
                                        فاکتور :
                                    </div>
                                    <div class="col-lg-7 text-center text-lg-left">{{ factor_payment_status }}</div>
                                </div>

                                <div v-if="order_missionary" class="dropdown-divider"></div>

                                <div v-if="order_missionary" class="row">
                                    <div class="col-lg-5 text-center text-lg-right font-weight-bold mb-2 mb-lg-0">هزینه
                                        سفارش :
                                    </div>
                                    <div class="col-lg-7  text-center text-lg-left">
                                        {{ toMoneyFormat(order_missionary) }}
                                    </div>
                                </div>

                                <div v-if="!factor_payment_status.includes('پرداخت')" class="dropdown-divider"></div>

                                <div v-if="!factor_payment_status.includes('پرداخت')" class="row">
                                    <div class="col-lg-5 text-center text-lg-right font-weight-bold mb-2 mb-lg-0">موجودی
                                        حساب :
                                    </div>
                                    <div class="col-lg-7  text-center text-lg-left">
                                        {{ toMoneyFormat($userData.credit) }}
                                    </div>
                                </div>

                                <div v-if="!factor_payment_status.includes('پرداخت') && extra_needed_money !== 0"
                                     class="dropdown-divider"></div>

                                <div v-if="!factor_payment_status.includes('پرداخت') && extra_needed_money !== 0"
                                     class="row">
                                    <div class="col-lg-5 text-center text-lg-right font-weight-bold mb-2 mb-lg-0">مبلغ
                                        اضافی مورد نیاز :
                                    </div>
                                    <div class="col-lg-7  text-center text-lg-left">
                                        {{ toMoneyFormat(extra_needed_money) }}
                                    </div>
                                </div>

                                <div v-if="!factor_payment_status.includes('پرداخت') && factor_payment_status !== 'فاکتور صادر نشده'"
                                     class="dropdown-divider"></div>

                                <div v-if="!factor_payment_status.includes('پرداخت') && factor_payment_status !== 'فاکتور صادر نشده'"
                                     class="row d-flex justify-content-center mt-3">
                                    <button class="btn btn-success" @click="payOrder(order_number)">
                                        {{ payButtonText }}
                                    </button>
                                </div>

                                <transition name="fade" mode="out-in">
                                    <div class="row">
                                        <div class="col-lg-12">
                                            <div v-if="isProgressActive" class="progress mt-3">
                                                <div class="progress-bar progress-bar-striped progress-bar-animated bg-info"
                                                     role="progressbar" aria-valuenow="75" aria-valuemin="0"
                                                     aria-valuemax="100"
                                                     style="width: 100%"></div>
                                            </div>
                                        </div>
                                    </div>
                                </transition>

                                <transition name="fade" mode="out-in">
                                    <div class="alert alert-danger small text-right like-pre mt-3"
                                         v-if="!isHiddenError" dir="rtl">{{ Error }}
                                    </div>
                                </transition>
                            </div>
                        </div>


                        <div v-if="factors.length" class="container-fluid">
                        <div class="row mt-4">
                            <h4 class="col-12 text-center mb-2">فاکتورها</h4>

                            <table class="table table-hover table-borderless col-12"
                                   :class="{'table-striped': !$parent.isMobileDevice}"
                                   dir="rtl">
                                <thead>
                                <tr class="bg-dark text-white">
                                    <th>#</th>
                                    <th>عنوان</th>
                                    <th>دسته بندی</th>
                                    <th>هزینه واحد (ریال)</th>
                                    <th>تعداد</th>
                                    <th>قیمت کل (ریال)</th>
                                </tr>
                                </thead>

                                <tbody>
                                <tr v-for="(factor, index) in factors" v-bind:key="index">
                                    <td data-label="#">{{ index+1 }}</td>
                                    <td data-label="عنوان">{{ factor['title'] }}</td>
                                    <td data-label="دسته بندی">{{ factor['service'] }}</td>
                                    <td data-label="هزینه واحد (ریال)">{{ toMoneyFormat(factor['price']) }}</td>
                                    <td data-label="تعداد">{{ toMoneyFormat(factor['number']) }}</td>
                                    <td data-label="قیمت کل (ریال)">{{ toMoneyFormat(factor['total']) }}</td>
                                </tr>
                                </tbody>
                            </table>
                        </div>
                        </div>
                    </div>
                    <div class="modal-footer" dir="rtl">
                        <button type="button" class="btn btn-secondary btn-danger" data-dismiss="modal">بستن</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- the modal -->
        <div class="modal" id="confirm_modal" tabindex="-1" role="dialog">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="p-3 border-bottom">
                        <div class="row">
                            <div class="col-12  d-flex justify-content-between">
                                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                    <span aria-hidden="true">&times;</span>
                                </button>
                                <h5 class="modal-title text-right">پیام تایید</h5>
                            </div>
                        </div>
                    </div>
                    <div class="modal-body">
                        <div class="d-block text-center">
                            <h5>پرداخت با موفقیت انجام شد</h5>
                        </div>
                    </div>
                    <div class="modal-footer d-flex justify-content-center">
                        <button type="button" class="btn btn-success" data-dismiss="modal">متوجه شدم</button>
                    </div>
                </div>
            </div>
        </div>

    </div>

</template>


<script>

    import Pagination from "../../components/Pagination";
    export default {
        name: "orders",
        data() {
            return {
                data: [],
                orders: [],
                factors: [],

                currentPage: 1,

                modal_title: '',

                pickup_info: '',
                delivery_info: '',

                order_number: '',
                modal_delivery: '',
                modal_pickup: '',
                factor_payment_status: '',
                order_missionary: '',
                extra_needed_money: '',
                payButtonText: '',

                isProgressActive: false,

                isHiddenError: true,
                Error: '',


            }
        },
        computed: {

            pages() {
                return this.chunk(this.orders, 8);
            },
            pagesLength() {
                return this.pages.length;
            },

        },

        components: {
            Pagination
        },

        methods: {
            chunk: function (array, size) {
                if (!array) return [];
                const firstChunk = array.slice(0, size);
                if (!firstChunk.length) {
                    return array;
                }
                return [firstChunk].concat(this.chunk(array.slice(size, array.length), size));
            },


            HandleModal: function (order) {

                this.factors = [];

                this.modal_title = '';

                this.pickup_info = '';
                this.delivery_info = '';

                this.order_number = '';
                this.modal_delivery = '';
                this.modal_pickup = '';
                this.factor_payment_status = '';
                this.order_missionary = '';
                this.extra_needed_money = '';
                this.payButtonText = '';

                this.modal_title = 'سفارش شماره ' + order['number_order'];

                this.pickup_info = order['date_pickup_user'] + " ساعت " +
                    order['time_pickup_user'].split(':')[0] + "تا" +
                    order['time_pickup_user'].split(':')[1];

                this.delivery_info = order['date_delivery_user'] + " ساعت " +
                    order['time_delivery_user'].split(':')[0] + "تا" +
                    order['time_delivery_user'].split(':')[1];

                this.order_number = order['number_order'];

                this.modal_pickup = order['date_pickup_user'] + " ساعت " +
                    order['time_pickup_user'].split(':')[0] + "تا" +
                    order['time_pickup_user'].split(':')[1];

                this.modal_delivery = order['date_delivery_user'] + " ساعت " +
                    order['time_delivery_user'].split(':')[0] + "تا" +
                    order['time_delivery_user'].split(':')[1];

                this.factor_payment_status = order['factor_payment_status'];

                this.order_missionary = order['factor_missionary'];

                this.extra_needed_money = order['remaining_amount'];
                this.payButtonText = this.toMoneyFormat(this.order_missionary)
                    + ' پرداخت فاکتور';

                this.factors = order['factors'];

                this.$nextTick(function () {
                    $('#order_info_modal').modal('show');
                });
            },


            payOrder: function (order_id) {

                this.isProgressActive = true;
                this.isHiddenError = true;

                this.$API.get('/api/v5/user/payForOrder?token=' + this.$TOKEN + '&order_id=' + order_id)
                    .then(
                        (response) => {
                            this.isProgressActive = false;

                            if (parseInt(response.data.code) === 1) {

                                const THIS = this;
                                this.$initData(function (response) {
                                    const modal = $("#confirm_modal");
                                    modal.modal('show');
                                    modal.on('hide.bs.modal', function () {

                                        THIS.orders = response.orders;
                                        THIS.orders.sort(function (a, b) {
                                            return b.number_order - a.number_order;
                                        });
                                        $("#order_info_modal").modal('hide');
                                    });
                                });


                            } else if (parseInt(response.data.code) === 0) {

                                if (Object.keys(response.data.msg)[0] === 'token') {

                                    localStorage.removeItem('data');
                                    this.$router.push({path: '/login'});

                                } else {
                                    // console.log(response.data.msg);
                                    this.Error = '';
                                    const THIS = this;
                                    Object.values(response.data.msg).forEach(function (error_message) {
                                        THIS.Error += "\n - " + error_message;
                                    });
                                    this.isHiddenError = false;

                                }
                            } else {
                                const myWindow = window.open("", "_self");
                                myWindow.document.write(response.data);

                            }
                        }, (error) => {
                            this.isProgressActive = false;
                            console.log(error)
                        }
                    );

            },


            toMoneyFormat: function (number) {
                // return new Intl.NumberFormat('fa', {maximumSignificantDigits: 3}).format(number)

                const formatter = new Intl.NumberFormat('fa-IR', {
                    style: 'currency',
                    currency: 'IRR',
                    minimumFractionDigits: 0
                });

                return formatter.format(number).replace("ریال", '');
            }
        },
        beforeMount: function () {

            this.orders = this.$userData['orders'];
            if (Array.isArray(this.orders) && this.orders.length) {
                this.orders.sort(function (a, b) {
                    return b.number_order - a.number_order;
                });
            }


        }
    }
</script>


<style scoped>

    *:focus {
        outline: 0;
    }

    .card-hover-blue {
        -o-transition: .3s;
        -ms-transition: .3s;
        -moz-transition: .3s;
        -webkit-transition: .3s;
        transition: .3s;
    }

    .card-hover-blue:hover {
        cursor: pointer;
        color: white;
        background-color: #92ccff;
    }

</style>