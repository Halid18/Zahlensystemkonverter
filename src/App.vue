<template>
    <div id="app">
        <div class="options">
            <h2>Zahlensystemkonverter</h2>
            <select
                v-model="conversionType"
                @change="$emit('conversion-mode', conversionType)"
            >
                <option :value="option.type" v-for="option in options" :key="option.type">
                    {{ option.name }}
                </option>
            </select>
        </div>
        <div class="inner-container">
            <div class="box">
                <div>
                    <h3>
                        {{ fromText }}
                    </h3>
                    <input type="text" v-model="fromVal" />
                    <button @click="convertNumber()">Umwandeln</button>
                </div>
            </div>
            <div class="box">
                <div>
                    <h3>
                        {{ toText }}
                    </h3>
                    <p class="decimal-val">
                        {{ toVal }}
                    </p>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'App',
    data() {
        return {
            binaryValue: 0,
            decimalValue: 0,
            conversionType: 'dtb',
            options: [
                {
                    type: 'btd',
                    name: 'Binär in Dezimal'
                },
                {
                    type: 'dtb',
                    name: 'Dezimal in Binär'
                },
                {
                    type: 'adn',
                    name: 'Binärzahlen addieren'
                }
            ],
            fromVal: 0,
            fromText: 'Binärwert',
            toVal: 0,
            toText: 'Dezimalwert'
        };
    },

    watch: {
        conversionType: function(newvalue) {
            this.fromVal = 0;
            this.toVal = 0;
            this.toText = newvalue;
            if (newvalue == 'btd') {
                this.fromText = 'Binärwert';
                this.toText = 'Dezimalwert';
            } else {
                this.fromText = 'Dezimalwert';
                this.toText = 'Binärwert';
            }
        }
    },
    methods: {
        convertNumber: function() {
            if (this.conversionType == 'dtb') {
                const getBinary = v => {
                    if (v === 0) return '';
                    let remainder = v % 2;
                    let quotient = (v - remainder) / 2;
                    if (remainder === 0) {
                        return getBinary(quotient) + '0';
                    } else {
                        return getBinary(quotient) + '1';
                    }
                };
                const betterGetBinary = v => {
                    if (v === 0) return '0';
                    if (v < 0) return '-' + getBinary(-v);
                    return getBinary(v);
                };
                this.toVal = betterGetBinary(this.fromVal);
                this.$emit('updatedecimal', this.toVal);
            } else {
                let valToStr = `${this.fromVal}`;
                let decimal = +0;
                let bits = +1;
                for (let i = 0; i < valToStr.length; i++) {
                    let currNum = +valToStr[valToStr.length - i - 1];
                    if (currNum === 1) {
                        decimal += bits;
                    }
                    bits *= 2;
                }
                this.toVal = decimal;
                this.$emit('updatebinary', this.toVal);
            }
        }
    }
};
</script>

<style>
* {
    box-sizing: border-box;
}
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #fff;
    margin-top: 60px;
}

body {
    background-color: #0382B5;
    color: #fff;
}
.container {
    height: 100vh;
    text-align: center;
    padding-top: 20vh;
}
.options select {
    height: 40px;
    width: 50vw;
}
.inner-container {
    color: #fff;
    width: 70vw;
    height: 60vh;
    margin: auto;
    display: flex;
    align-items: center;
    justify-content: space-around;
}
.box {
    display: flex;
    align-items: center;
    justify-content: space-around;
    width: 45%;
    height: 250px;
    background-color: #eee;
    text-align: center;
    color: #000;
}
input {
    height: 40px;
}
button {
    height: 40px;
    background-color: #0382B5;
    border: 0;
    color: #fff;
    padding: 8px;
}
.decimal-val {
    color: white;
    background-color: #0382B5;
    padding: 10px;
    min-width: 70%;
    margin: auto;
}
</style>