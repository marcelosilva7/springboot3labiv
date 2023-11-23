<template>
  <div class="prova">
        <div class="body-prova">
            <h4>CADASTRAR NOVO VOCÁBULO</h4>
            <div class="formulario">
                <div class="input-box">
                    <label for="titulo">titulo</label>
                    <input type="text" name="titulo" v-model="titulo">
                </div>
                <div class="input-box">
                    <label for="estudio">estudio</label>
                    <input type="text" name="estudio" v-model="estudio">
                </div>
                <div class="input-box">
                    <label for="dataHoraLancamento">dataHoraLancamento</label>
                    <input type="datetime-local" name="dataHoraLancamento" v-model="dataHoraLancamento">
                </div>
                <div class="input-box">
                    <label for="classificacao">classificacao</label>
                    <input type="number" name="classificacao" v-model="classificacao">
                </div>
                <div class="input-box">
                    <label for="duracaoMedia">duracaoMedia</label>
                    <input type="text" name="duracaoMedia" v-model="duracaoMedia">
                </div>
                <div class="formulario-action">
                    <button @click="cadastrar()">Cadastrar</button>
                </div>
            </div>
            <h4>BUSCAR Jogo</h4>
            <div class="formulario">
                <div class="input-box">
                    <label for="estudio">estudio</label>
                    <input type="text" name="estudio" v-model="estudio">
                </div>
                <div class="input-box">
                    <label for="titulo">titulo</label>
                    <input type="text" name="titulo" v-model="titulo">
                </div>
                <div class="formulario-action">
                    <button @click="buscarJogos()">Buscar</button>
                </div>
            </div>
            <h4>TODOS OS JOGOS</h4>
            <div v-if="!erro" class="todos">
                <table>
                    <thead>
                        <td>ID</td>
                        <td>TITULO</td>
                        <td>ESTUDIO</td>
                        <td>DURACAOMEDIA</td>
                    </thead>
                    <tbody>
                        <tr v-for="jogo in jogos">
                            <td>{{ jogo.id }}</td>
                            <td>{{ jogo.titulo }}</td>
                            <td>{{ jogo.estudio }}</td>
                            <td>{{ jogo.duracaoMedia > 20 ? 'longo' : 'rápido' }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <div v-else="erro" class="todos">
                <p>{{ erro }}</p>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">

import { onMounted, ref } from 'vue';
import axios from 'axios';
const erro = ref();

const jogos = ref();

const titulo = ref()
const estudio = ref()
const dataHoraLancamento = ref()
const classificacao = ref()
const duracaoMedia = ref()



async function atualizar() {
    try {                                  
        jogos.value = (await axios.get("http://localhost:8080/jogo")).data;
    }
    catch (ex) {
        alert('azedou!!')
        erro.value = (ex as Error).message;
    }
}

//função de captura de dados POR TERMO E VERSAO
async function buscarJogos() {
    try {                                  
        jogos.value = (await axios.get(`http://localhost:8080/jogo/${estudio.value}/${titulo.value}`)).data;
        
        //SE RETORNAR VAZIO
        if(jogos.value == ''){
            erro.value = 'Nenhum registro foi encontrado para os criterios fornecidos!'
        }
    }
    catch (ex) {
        alert('azedou!!')
        erro.value = (ex as Error).message;
    }
}

//CADASTRAR NOVO VOCABULO
async function cadastrar() {
    try {
        await axios.post("http://localhost:8080/jogo",
            {
                titulo: titulo.value,
		        estudio: estudio.value,
		        dataHoraLancamento: dataHoraLancamento.value,
		        classificacao: classificacao.value,
	        	duracaoMedia: duracaoMedia.value
            });
    
            alert('Cadastrado')

            // LIMPAR OS CAMPOS
            titulo.value = ''
            estudio.value = ''
            dataHoraLancamento.value = ''
            classificacao.value = ''
            duracaoMedia.value = ''
    }
    catch (ex) {
        erro.value = (ex as Error).message;
    }
    atualizar();
}

//PUXR DADOS AO SISTEMA ABRIR A PAG
onMounted(() => {
    atualizar();
});

</script>