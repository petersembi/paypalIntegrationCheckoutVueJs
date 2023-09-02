<template>
    <div>
        <div v-if="!paidFor">
            <h1>Buy this Lamp - ${{ product.price }} OBO</h1>

            <p>{{ product.description }}</p>

        </div>

        <div v-if="paidFor">
            <h1>Noice, you bought a beautiful lamp!</h1>
        </div>

        <div ref="paypal"></div>
    </div>
</template>

<script>
// import image from "../assets/lamp.png"
export default {
    name: "HelloWorld",

    data: function () {
        return {
            loaded: false,
            paidFor: false,
            product: {
                price: 777.77,
                description: "leg lamp from that one movie",
                img: "./assets/lamp.jpg"
            }
        };
    },
    mounted: function () {
        const script = document.createElement("script");
        script.src =
            "https://www.paypal.com/sdk/js?client-id=AXTXoDdf-Ie5EC0_Cfrl1nWR9I6Q8uDxt2kQdN40G-E8XOjNrAjf68v5aUbc0ZsYifhYeTx8MWJ5gwkX";
        script.addEventListener("load", this.setLoaded);
        document.body.appendChild(script);
    },
    methods: {
        setLoaded: function () {
            this.loaded = true;
            window.paypal
                .Buttons({
                    createOrder: (data, actions) => {
                        return actions.order.create({
                            purchase_units: [
                                {
                                    description: this.product.description,
                                    amount: {
                                        currency_code: "USD",
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
                    },
                    onError: err => {
                        console.log(err);
                    }
                })
                .render(this.$refs.paypal);
        }
    }
};
</script>