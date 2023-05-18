<template>
    <div class="grid">
        <div class="col-12">

            <div class="card p-fluid">
                <h5>GENERAR RFC</h5>

                <div class="field">
                    <label for="nom">NOMBRE(S)</label>
                    <InputText id="nom" v-model="nom" type="text" />
                </div>

                <div class="field">
                    <label for="app">APELLIDO PATERNO</label>
                    <InputText id="app" v-model="app" type="text" />
                </div>

                <div class="field">
                    <label for="apm">APELLIDO MATERNO</label>
                    <InputText id="apm" v-model="apm" type="text" />
                </div>

                <div class="field">
                    <label for="nac">FECHA DE NACIMIENTO</label>
                    <Calendar id="nac" v-model="nac" dateFormat="dd/mm/yy" showIcon />
                </div>

                <div class="field">
                    <Button type="button" label="RFC" severity="secondary" :disabled="disabledBtn" :loading="load" @click="fRfc"/>
                </div>

                <!-- <div class="field" v-if="rfcG"> -->
                    <!-- <div class="p-inputgroup justify-content-center">
                        <Message class="p-inputgroup-addon" severity="info" size="small" :loading="load" :closable="false">RFC: {{rfcG}}</Message> -->
                        <!-- <InlineMessage class="p-inputgroup-addon" severity="success"  icon="pi pi-user">RFC: {{rfcG}}</InlineMessage> -->
                        <!-- <Button icon="pi pi-copy" severity="success" @click="copyRfc" />
                    </div> -->
                <!-- </div> -->

                <!-- <div class="field" v-if="rfcG"> -->

                    <!-- <div class="field justify-content-center">
                        <InlineMessage severity="info">Username is required</InlineMessage>
                        <Button icon="pi pi-copy" severity="success" @click="showInfo" />
                    </div>

                    <div class="field justify-content-center">
                        <Message :closable="false">RFC: {{rfcG}}</Message>
                        <Button icon="pi pi-copy" severity="success" @click="showInfo" />
                    </div> -->

                    <!-- <div class="p-inputgroup"> -->

                        <!-- <div class="p-inputgroup-addon"> -->
                            <!-- <InlineMessage class="p-inputgroup-addon" severity="success">RFC: {{rfcG}}</InlineMessage> -->
                        <!-- </div> -->

                        <!-- <Button icon="pi pi-search" severity="warning" /> -->
                        <!-- <InputText placeholder="Keyword" /> -->

                        <!-- <Button icon="pi pi-search" severity="warning" /> -->
                    <!-- </div> -->

                <div class="field" v-if="rfcG">

                <!-- <div class="field"> -->
                    <Toast />
                    <div class="p-inputgroup justify-content-center">
                        <!-- <Message class="p-inputgroup-addon" severity="success" icon="NULL" :closable="false">RFC: {{rfcG}}</Message> -->

                        <InlineMessage class="p-inputgroup-addon" severity="success"  icon="pi pi-send">RFC: {{rfcG}}</InlineMessage>
                        <Button icon="pi pi-copy" severity="success" @click="copyRfc" outlined/>
                    </div>
                </div>

                <div class="field" v-if="err">
                    <InlineMessage severity="error">DEBE LLENAR CORECTAMENTE TODOS LOS CAMPOS</InlineMessage>
                </div>
            </div>

        </div>
    </div>
</template>

<script>

export default({
    data() {
        return {
            nom:'',
            app:'',
            apm:'',
            nac:'',
            rfcG: false,
            load: false,
            err: false,
            //isDisabled: true,
        };
    },
    computed: {
        disabledBtn() {

            if (this.nom.length && this.app.length && this.apm.length && this.nac.toString().length) {
                return false
                //isDisabled = false
            }
            return true
        }
    },
    methods:{

        fRfc() {
            this.rfcG = false;
            this.load = true;
            this.err = false;
            var rfc = "";
            rfc = this.app.substring(0,2);
            rfc += this.apm.substring(0,1);
            rfc += this.nom.substring(0,1);
            rfc += this.nac.getFullYear().toString().substring(2,4);
            rfc += this.nac.toISOString().substring(5,7);
            rfc += this.nac.toISOString().substring(8,10);
            rfc += "R7A";
            //this.rfcG = rfc.length//.toUpperCase()
            //this.rfcG=this.nac.getDate().toString()
            //this.rfcG=this.nac.toISOString().toString()

            if (rfc.length < 13) {
                setTimeout(() => {
                    this.rfcG = false;
                    this.err = true;
                    this.load = false;
                }, 200);

            } else {
                setTimeout(() => {
                    this.err = false;
                    this.load = false;
                    this.rfcG = rfc.toUpperCase();
                    //alert('Copied');
                    // document.execCommand("copy");
                }, 250);
                //this.rfcG = rfc.toUpperCase()
            }

        },

        copyRfc() {
            try {
                navigator.clipboard.writeText(this.rfcG)
                this.$toast.add({ severity: 'info', summary: 'RFC COPIADO', detail: this.rfcG, life: 3000 });
            } catch(e) {
                throw e;
            }
        },

        show() {
            this.$toast.add({ severity: 'success', summary: 'RFC COPIADO', detail: this.rfcG, life: 3000 });
        }

    }
})
</script>
