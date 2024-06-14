<template>
  <q-page>
    <div class="q-pa-md">

      <div v-if="showCamera" style="height: 400px;">
        <qr-stream @decode="onDecode">
          <div style="color: red;" class="frame"></div>
        </qr-stream>
      </div>

      <div class="custon-btn-carregar-img">
        <label for="qr-capture" class="">
          <span class="material-icons q-mr-sm">camera_alt</span>
          <span>Carregar Qrcode</span>
        </label>
        <qr-capture id="qr-capture" @decode="onDecode" class="mb" style="display: none;"></qr-capture>
      </div>
      <BannerMessage v-if="showSuccess" message="Operação realizada com sucesso!" type="success" />
      <BannerMessage v-if="showError" message="Erro na operação." type="error" />
      <div v-if="nfData" class="q-mt-lg">
        <q-table :rows="nfData.produtos" :columns="columns" row-key="produto" flat bordered>
        </q-table>
        <div class="text-h6 text-center q-mt-lg">
          <span class="text-green-7 text-bold">Valor Total:</span> {{ nfData.valorTotal }}
        </div>
        <div class="text-h6 text-center q-mt-lg">
          <q-btn class="q-mx-auto" @click="salvarDadosBD(nfData.produtos, nfData.valorTotal)" color="primary"
            label="Salvar Produtos" />
        </div>
      </div>

      <div class="q-mt-lg custom-btn-div">
        <q-btn class="q-mx-auto custom-btn material-icons" @click="toggleQrStream" icon="camera_alt" color="primary" />
      </div>

    </div>
  </q-page>

</template>

<script>
import { defineComponent, ref } from 'vue'
import { QrCapture, QrStream } from 'vue3-qr-reader'
import BannerMessage from '../components/BannerMessage.vue'
import axios from 'axios'

export default defineComponent({
  name: 'IndexPage',
  components: {
    QrStream,
    QrCapture,
    BannerMessage
  },
  setup() {
    const qrcode = ref('')
    const showCamera = ref(false)
    const nfData = ref(null)
    const showSuccessBanner = ref(false)
    const showSuccess = ref(false);
    const showError = ref(false);

    const exibirBannerSucesso = () => {
      showSuccess.value = true;
      setTimeout(() => {
        showSuccess.value = false;
      }, 3000); // Oculta o banner de sucesso após 5 segundos
    };

    const exibirBannerErro = () => {
      showError.value = true;
      setTimeout(() => {
        showError.value = false;
      }, 3000); // Oculta o banner de erro após 5 segundos
    };

    const onDecode = async (data) => {
      qrcode.value = data
      try {
        const response = await axios.get('https://nf-api-server.vercel.app/proxy', {
          params: { url: data }
        })
        nfData.value = response.data
        exibirBannerSucesso()
      } catch (error) {
        console.error('Erro ao buscar os dados da NF:', error)
        exibirBannerErro()
      }
    }

    const salvarDadosBD = async (produtos, valorTotal) => {
      try {
        const response = await axios.post('https://nf-api-server.vercel.app/save', { produtos, valorTotal });
        exibirBannerSucesso()
        console.log(response.data.message); // Exibe a mensagem de sucesso
        // showSuccess.value = true // Exibe o banner de sucesso
      } catch (error) {
        exibirBannerErro()
        console.error('Erro ao salvar os dados:', error);
      }
    };

    const toggleQrStream = () => {
      showCamera.value = !showCamera.value;
    };

    return {
      onDecode,
      salvarDadosBD,
      showSuccess,
      showError,
      exibirBannerSucesso,
      exibirBannerErro,
      qrcode,
      showCamera,
      toggleQrStream,
      nfData,
      columns: [
        { name: 'produto', label: 'Produto', field: 'produto' },
        { name: 'unidade', label: 'Unidade', field: 'unidade' },
        // { name: 'valorUnitario', label: 'Valor Unitário', field: 'valorUnitario' },
        { name: 'valorUnitarioTotal', label: 'Valor', field: 'valorUnitarioTotal' },
      ],
    }
  }
})
</script>

<style>
.banner {
  display: flex;
  align-items: center;
  justify-content: center;
  top: 10%;
}

.banner-in {
  margin-top: 12px;
}

.custom-btn-div {
  position: fixed;
  bottom: 16px;
  right: 16px;
}

.custon-btn-carregar-img {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 3rem;
  width: 10rem;
  background-color: #2196F3;
  border-radius: 10px;
  color: #FFFFFF;
  cursor: pointer;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.custom-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 80px;
  width: 80px;
  background-color: #2196F3;
  border-radius: 50%;
  color: #FFFFFF;
  cursor: pointer;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.custom-btn:hover {
  background-color: #1976D2;
}

.material-icons {
  font-size: 20px;
}
</style>
