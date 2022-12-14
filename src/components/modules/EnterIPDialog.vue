<template>
    <v-dialog v-model="show" width="600">
        <v-card style="user-select: none;">
            <v-card-title></v-card-title>

            <v-card-text>
                <p>Note: Manually entering the IP is only required if Map Connect fails to automatically connect to the
                    target device.</p>
                <v-text-field v-model="ip" label="IP Address*" :rules="[rules.required, rules.ip]"></v-text-field>
            </v-card-text>

            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="primary" elevation="0" @click="confirm">
                    Confirm
                </v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>
</template>

<script>
import { ipcRenderer } from 'electron';

window.isConnectToIP = {
    confirm: false
}

export default {
    name: "EnterIPDialog",

    data: () => ({
        show: false,
        ip: '',
        rules: {
            required: value => !!value || 'Please enter an IP address.',
            ip: value => {
                const regexp = /^(([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\.){3}([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])$/gi;
                return regexp.test(value) || 'Invalid IP address.';
            }
        }
    }),
    methods: {
        open() {
            this.show = true;
        },
        confirm() {
            if (this.rules.ip(this.ip) !== true || this.rules.required(this.ip) !== true) return;
            window.isConnectToIP.confirm = true;
            this.show = false;
            ipcRenderer.send('connectToIP', this.ip);
        }
    }
}
</script>