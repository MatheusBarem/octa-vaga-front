# Teste vaga octadesk
Teste  realizado por Matheus Barem da Octadesk form builder utilizando Vue.js


## Sobre
Sem muito tempo para executar a aplicação achei interessante utilizar o package [Vue Grid Layout](https://jbaysolutions.github.io/vue-grid-layout/) onde eu crio na Design Area em um array de objetos e armazeno as posições dos grids preenchidos que devem ser mostrados na tela, dentro dessas propriedades eu consigo renderizar outros componentes que estão na Toolbox.

## Demo
[Live Demo](https://peaceful-varahamihira-b2aeba.netlify.app/)


## Observações

Devido a alta demanda do meu trabalho atual não tive tempo para **refatorar** o código e aplicar as modificações necessárias para ter um código mais maduro, as modificações que eu aplicaria:

 1. Componentização de forma mais profunda, onde criaria um componente para Toolbox e outro para a Design area para "desafogar" o App.vue (como está no projeto na versão atual).
 2. Aplicaria o Vuex após a componentização para organizar melhor os estados da aplicação e comunicação entre os componentes além de deixar o código mais limpo dentro de cada um deles.
 3. Escreveria testes automatizados em Jest.js. 
 4. Trabalharia em volta de cada componente da Toolbox para deixa-los mais dinâmicos e customizáveis ao usuário. 

