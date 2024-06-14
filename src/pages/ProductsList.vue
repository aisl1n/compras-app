<template>
    <q-page>
        <div class="q-pa-md">
            <q-toolbar class="q-mb-md">
                <q-toolbar-title>Lista de Produtos</q-toolbar-title>
                <span v-if="totalValor !== null" class="total-valor">
                    (Total: R$ {{ totalValor }})
                </span>
            </q-toolbar>
            <q-table :rows="produtos" :columns="columns" row-key="_id" flat bordered>
                <template v-slot:header-cell-produto="props">
                    <q-th :props="props" class="text-left">
                        {{ props.col.label }}
                    </q-th>
                </template>
                <template v-slot:body-cell-produto="props">
                    <q-td :props="props" class="text-left">
                        {{ props.row.produto }}
                    </q-td>
                </template>
            </q-table>
        </div>
    </q-page>
</template>

<script>
import axios from 'axios';

export default {
    name: 'ProdutosPage',
    data() {
        return {
            produtos: [],
            totalValor: null,
            columns: [
                { name: 'produto', label: 'Produto', field: 'produto' },
                { name: 'unidade', label: 'Unidade', field: 'unidade' },
                // { name: 'valorUnitario', label: 'Valor Unitário', field: 'valorUnitario' },
                { name: 'valorUnitarioTotal', label: 'Valor', field: 'valorUnitarioTotal' },
            ],
        };
    },
    async mounted() {
        try {
            const response = await axios.get('https://nf-api-server.vercel.app/produtos');
            this.produtos = response.data;
            this.calculateTotalValor();
        } catch (error) {
            console.error('Erro ao buscar os produtos:', error);
        }
    },
    methods: {
        calculateTotalValor() {
            this.totalValor = this.produtos.reduce((acc, produto) => {
                return acc + parseFloat(produto.valorUnitarioTotal.replace(',', '.'));
            }, 0).toFixed(2).replace('.', ',');
        },
    },
};
</script>

<style>
.text-left {
    text-align: left;
}

.total-valor {
    font-size: 18px;
    font-weight: normal;
    margin-left: 10px;
    color: #2196F3;
}
</style>