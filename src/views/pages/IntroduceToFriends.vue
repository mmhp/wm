<template>

    <div class="container">
        <div class="row h-auto d-flex justify-content-center p-2 px-lg-0 py-lg-5">
            <div class="card h-auto shadow shadow-sm col-12 col-lg-8 p-2 text-right">

                <div class="card-body pt-1 pt-md-2 px-1" dir="rtl">
                <div class="px-3">


                    <div class="row p-3 bg-info text-white rounded shadow-sm">
                        <i class="col-1 fas fa-2x fa-share-alt align-self-center"></i>
                        <div class="col-10 col-md-11 text-right align-self-center text-center text-justify" dir="rtl">
                            واش ماش را به دوستان خود معرفی کنید و اعتبار هدیه دریافت کنید. برای این کار کد زیر را به
                            دوستان خود بدهید تا هنگام ثبت نام آن را در محل کد معرف خود وارد کنند.
                        </div>
                    </div>

                    <div class="row mt-5 d-flex justify-content-center">

                        <div style="height: 10%;" v-ripple @click="handleCopy()"
                             class="col-10 col-md-4 card-code rounded border border-danger p-3 d-flex justify-content-between align-items-center"
                             data-toggle="popover" data-content="در بریده دان ذخیره شد">

                            <i class="fas fa-2x fa-copy"></i>
                            <div class="col-12">
                                <div class="text-center">{{ $userData.tell_friends }}</div>
                            </div>
                        </div>

                    </div>

                    <div class="row mt-5 mb-3 d-flex justify-content-center">

                        <div class="col-8 col-lg-6">
                            <div class="row d-flex justify-content-between">
                                <div class="d-flex justify-content-center align-items-center">
                                    <a :href="'https://telegram.me/share/url?url=&text='+$userData.tell_friends"
                                       target="_blank">
                                        <i class="fab fa-3x fa-telegram align-self-center"></i>
                                    </a>
                                </div>
                                <!--<div class="col-md-3 d-flex justify-content-center align-items-center">-->
                                <!--<a :href="'https://www.facebook.com/sharer/sharer.php?u='+$userData.tell_friends">-->
                                <!--<i class="fab fa-3x fa-instagram align-self-center"></i>-->
                                <!--</a>-->
                                <!--</div>-->
                                <div class="d-flex justify-content-center align-items-center">
                                    <a :href="'https://www.facebook.com/sharer/sharer.php?u='+$userData.tell_friends"
                                       target="_blank">
                                        <i class="fab fa-3x fa-facebook align-self-center"></i>
                                    </a>
                                </div>
                                <div class="d-flex justify-content-center align-items-center">
                                    <a :href="'https://twitter.com/home?status='+$userData.tell_friends"
                                       target="_blank">
                                        <i class="fab fa-3x fa-twitter align-self-center"></i>
                                    </a>
                                </div>
                            </div>

                        </div>
                    </div>
                </div>
                </div>

            </div>

        </div>
    </div>

</template>


<script>
    export default {
        name: "introduce_to_friends",
        data() {
            return {}
        },
        computed: {

        },

        components: {
        },

        methods: {

            handleCopy: function () {
                this.copyToClipboard(this.$userData.tell_friends);
                const THIS = this;
                setTimeout(function () {
                    THIS.$nextTick(function () {
                        $('.card-code').popover('hide');
                    });
                }, 2000);
            },

            copyToClipboard: function (text) {
                if (document.queryCommandSupported && document.queryCommandSupported("copy")) {
                    var textarea = document.createElement("textarea");
                    textarea.textContent = text;
                    textarea.style.position = "fixed";  // Prevent scrolling to bottom of page in MS Edge.
                    document.body.appendChild(textarea);
                    textarea.select();
                    try {
                        return document.execCommand("copy");  // Security exception may be thrown by some browsers.
                    } catch (ex) {
                        console.warn("Copy to clipboard failed.", ex);
                        return false;
                    } finally {
                        document.body.removeChild(textarea);
                    }
                }
            },
        },
        beforeMount: function () {
        },
        mounted: function () {
            this.$nextTick(function () {
                $(function () {
                    $('[data-toggle="popover"]').popover();
                });
            })
        },
    }
</script>


<style scoped>

    *:focus {
        outline: 0;
    }

    .card-code {
        color: #c82333;
        background-color: #ffffff;
        -o-transition: .5s;
        -ms-transition: .5s;
        -moz-transition: .5s;
        -webkit-transition: .5s;
        transition: .5s;
    }

    .card-code:hover {
        cursor: pointer;
        color: #ffffff;
        background-color: #c82333;
    }

    .fa-telegram,
    .fa-twitter,
    .fa-facebook,
    .fa-instagram {
        -o-transition: .5s;
        -ms-transition: .5s;
        -moz-transition: .5s;
        -webkit-transition: .5s;
        transition: .5s;
    }

    a {
        color: inherit;
        text-decoration: none;
    }

    .fa-telegram:hover {
        color: #0088cc;
        transform: scale(1.3);
    }

    .fa-facebook:hover {
        color: #3B5998;
        transform: scale(1.3);
    }

    .fa-instagram:hover {
        color: #fb3958;
        transform: scale(1.3);
    }

    .fa-twitter:hover {
        color: #00C6FF;
        transform: scale(1.3);
    }


</style>