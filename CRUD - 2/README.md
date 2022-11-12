# CRUD com LocalStorage - Passo a passo de como foi desenvolvido


* Para o `localStorage`, temos que passar uma chave e o valor dela. Vamos criar então uma chave que é o nome do banco `db_client`. 
* O valor dessa chave será os dados que queremos armazenar. Só que queremos que esses dados seja um objeto. 
* Como o `localStorage` não aceita objeto, temos que transformar ele em String. 
* Além disso, o `db_client` tem que ser um array para armazenar os objetos em certas posições. 

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
const setLocalStorage = (dbClient) => JSON.stringify(localStorage.setItem("db_client", JSON.stringify(dbClient));

const createClient(client) {
    const dbClient = getLocalStorage();
    dbClient.push(client);
    
    // Atualizar o banco
    setLocalStorage(dbClient);
}
```
