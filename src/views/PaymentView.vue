<template>
    <div>
        <div v-if="!paidFor">
            <h1>Buy this product - ${{ product.price }} OBO</h1>
            <p>{{ product.description }}</p>
        </div>

        <div v-if="paidFor">
            <h1>Noice, you bought a beautiful product!</h1>

        </div>

        <div ref="paypal"></div>
    </div>
</template>
<style>
/* .paypal-button-label-container {
    background-color: blue;
} */
</style>
<script>
export default {
    name: "HelloWorld",

    data: function() {
        return {
            loaded: false,
            paidFor: false,
            product: {
                price: 777.77,
                description: "Leg lamp from that one movie",
                img: './assets/lamp.jpg'
            },
            paymentInfo: {},
           
        };
    },
    mounted: function () {
        const script = document.createElement("script");
        script.src = "https://www.paypal.com/sdk/js?client-id=AXTXoDdf-Ie5EC0_Cfrl1nWR9I6Q8uDxt2kQdN40G-E8XOjNrAjf68v5aUbc0ZsYifhYeTx8MWJ5gwkX";
        script.addEventListener('load', this.setLoaded);
        document.body.appendChild(script);
    },
    methods: {
        setLoaded: function() {
            this.loaded = true;
            window.paypal
            .Buttons({
                createOrder: (data, actions) => {
                    return actions.order.create({
                        purchase_units: [
                            {
                                description: this.product.description,
                                amount: {
                                    currency_code: 'USD',
                                    value: this.product.price
                                }

                            }
                        ]
                    });
                },
                onApprove: async (data, actions) => {
                    const order = await actions.order.capture();
                    this.paidFor = true;
                    console.log(order);   

                    // send http request to store payment details
                    // route: /client_payments/{id}                 
                    
                    this.paymentInfo = {
                        client_user_id: 5,
                        job_id: 9,
                        transaction_id: order['id'],
                        amount: order['purchase_units'][0]['payments']['captures'][0]['amount']['value'],
                        currency: order['purchase_units'][0]['payments']['captures'][0]['amount']['currency_code'],
                        payment_status: order['status'],
                        payer_paypal_email: order['payer']['email_address'],
                        payer_id: order['payer']['payer_id']
                    }

                    console.log('payment info: ', this.paymentInfo);                
                },
                onError: err => {
                    console.log(err);
                }
            })
            .render(this.$refs.paypal);
        }
    }
};


//returned object
</script>