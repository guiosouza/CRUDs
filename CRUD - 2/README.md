# CRUD com LocalStorage - Passo a passo de como foi desenvolvido


# 1) CREATE

* Para o `localStorage`, temos que passar uma chave e o valor dela. Vamos criar então uma chave que é o nome do banco `db_client`. 
* O valor dessa chave será os dados que queremos armazenar. Só que queremos que esses dados seja um objeto. 
* Como o `localStorage` não aceita objeto, temos que transformar ele em String. 
* Além disso, o `db_client` tem que ser um array para armazenar os objetos em certas posições.
* Por questões didáticas, os dados que vamos inserir no banco, vão ser via `hard code`. Depois trataremos do front.

Veja o exemplo:

```js

// Cliente de teste para o banco
const tempClient = {
  nome: "Ana",
  email: "ana@gmail.com",
  celular: "111"
}


//  Função para criar cliente:
const createClient(client) {
  
  // Lê um cliente em um banco que já existe. É necessário converter para JSON para funcionar o .push
  // Basicamente: pega o que tem no banco, converte para JSON e armazena na varíavel client:
  const dbClient = JSON.parse(localStorage.getItem('db_client'));
  
  // Insere o cliente recebido no array db_client que foi pego acima
  dbClient.push(client);
  
  // Transforma o db_client em String, assim o localStorage recebe o array na chave
  localStorage.setItem('db_client', JSON.stringify(dbClient));
}

// Um exemplo do aplicação acima:
createClient(tempClient)

```

Vamos melhorar o código acima, pois pode acontecer do banco de dados estar vazio e não ser um array para receber push:


```js
const createClient(client) {
  
  // O localStorage está vazio? Caso sim, recebe um array vazio []
  const dbClient = JSON.parse(localStorage.getItem('dbClient')) ?? [];
  
  dbClient.push(client)
  
  localStorage.setItem('dbClient', JSON.stringify(dbClient));
}
```

Agora pegamos o conteúdo de algumas variáveis e transformamos em funções. Bora deixar o código mais limpo:

```js

const getLocalStorage = () => JSON.parse(localStorage.getItem('db_client')) ?? [];
const setLocalStorage = (dbClient) => localStorage.setItem("db_client", JSON.stringify(dbClient));

const createClient(client) {
    const dbClient = getLocalStorage();
    dbClient.push(client);
    
    // Atualizar o banco
    setLocalStorage(dbClient);
}
```

Pronto. Bora para o read.

# 2) READ

A função será:

```js

const readClient = () => getLocalStorage();

```

# 3) UPDATE
Para atualizar, primeiro vamos ler a partir de um index.

```js
const updateClient = (index, client) => {
  
  // Pegar dados do banco
  const dbClient = readClient();
  
  // ir até o índice que foi passado do banco e modificar ele
  dbClient[index] = client;
  setLocalStorage(dbClient);
}
```

# 4) DELETE

Para deletar, vamos ler um um cliente a partir de um index.

```js
// Recebe um index
const deleteClient = (index) => {

  // Recupera o banco em forma de JSON
  const dbClient = readClient();
  
  // Remove do array usando slice (a partir do index, incluir apenas 1)
  dbClient.slice(index, 1);
  
  // Atualiza o banco de dados
  setLocalStorage(dbClient);
}
```
# 5) FRONT
Agora vamos tratar do seguinte:
- Criar funções para pegar os elementos do localStorage e gerar dados em HTML para serem exibidos no front;
- Limpar certos campos ao ter atualizações para melhorar a experiência do usuário;
- Associar os botões e campos do front com variáveis do localStorage

```js


const isValidFields = () => {

  // Pega o elemento que tem id form, e veja se os elementos HTML estão preenchidos
  return document.getElementById('form').reportValidity();
}


// Limpar campos
const clearFields = () => {

  // Criar um array fields, pegando todos elementos de classe .modal-fields
  fields = document.querySelectorAll('.modal-field');
  
  // Para cada item do fields, pegar o valor dele e deixar nulo
  fields.forEach(field => field.value = '');
}

// Interação com o layout
const saveClient = () => {
  
  // Verificar se os campos estão preenchidos
  if(isValidFields()) {
    
    // Cliente é um JSON para enviar ao banco
    // Abaixo vamos pegar os dados vindos do front pelos ids e inseri-los
    const client = {
      nome: document.getElementById('nome').value;
      email: document.getElementById('email').value;
      celular: document.getElementById('celular').value;
      cidade: document.getElementById('cidade').value;
    }
    
    // Agora adicionar o cliente:
    createClient(client);
    updateTable();
    closeModal();
  }
}

const createRow = (client) => {
  const newRow = document.createElement('tr');
  newRow.innerHTML = `
        <td>${client.nome}</td>
        <td>${client.email}</td>
        <td>${client.celular}</td>
        <td>${client.cidade}</td>
        <td>
            <button type="button" class="button green" id="edit-${index}">Editar</button>
            <button type="button" class="button red" id="delete-${index}" >Excluir</button>
        </td>
  `
  document.querySelector('#tableClient>tbody').appenChild(newRow);
}

const clearTable = () => {
  // Pego tableClient dentro a tbody e dentro a tr (isso é um array de elementos)
  const rows = document.querySelectorAll('#tableClient>tbody tr');
  
  rows.forEach(row => row.parentNode.removeChild(row));
}

const updateTable = () => {
  // Ler os dados
  const dbClient = readClient();
  clearTable();
  // Criar uma linha para cada um dos clientes
  dbClient.forEach(createRow);
}

updateTable();

// Eventos

const openModal = () => document.getElementById('modal')
    .classList.add('active')

const closeModal = () => {
    // Limpar os campos, sempre que a janela for fechada
    clearFields();
    document.getElementById('modal').classList.remove('active')
}

document.getElementById('cadastrarCliente')
  .addEventListener('click' openModal);
  
document.getElementById('modalClose')
  .addEventListener('click' closeModal);
  
document.getElementById('modalClose')
  .addEventListener('click' saveClient);

// Pegar o tbody que é filho do tableClient:
document.querySelector(#tableClient>tbody)
  .addEventListener('click', editDelete);

const editDelete = (event) => {
  
  if(event.target.type == 'button') {
  
  }
}

```
