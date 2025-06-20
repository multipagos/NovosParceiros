# Novos Parceiros

Seja bem vindo ao guia de orientação à integrações de Arrecadações com Multipagos!

Através da documentação abaixo você será capaz de utilizar os serviços Multipagos que foram construídos para facilitar as integrações.

Qualquer dúvida que possa ter, fique à vontade de enviar um e-mail para team-mpagos@multipagos.com.br que iremos te ajudar.


# SandBox
Disponibilizamos um ambiente SandBox para que você possa realizar os testes. Fique à vontade para usar!
- Url SandBox: https://sandbox.multipagos.com.br


# **Autenticação**
Processo de obtenção de token que irá garantir a autorização na utilização dos demais serviços.
- POST /api/Multipagos/Token
```html
{
  "pdv": "6000",
  "terminal": "48B20B9C-69B9-443E-9090-A5CE730DFA76"
}
```
**Pdv e Terminal serão fornecidos.**

Retorno em caso de sucesso:
```html
{
    "sucesso": true,
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6IjEiLCJuYmYiOjE2MTU5MDg0MjQsImV4cCI6MTYxNTkwODQ4NCwiaWF0IjoxNjE1OTA4NDI0fQ.Iy7m-U1KPomjQTh2tN3X5gGXn6LvE3W4H3dBRnc5-7s"
}

StatusCode: 200
```
**Token válido por 1 hora.**

Retorno em caso de falha:
```html
{
    "sucesso": false,
    "message": "Pdv ou Terminal inválidos."
}

StatusCode: 400
```

# **Início do Fluxo: Obter link do frame + Identificador da Transação**
Seguem detalhes sobre os campos que precisam ser enviados.

- POST /api/v2/Pagamento/FramePagamentoEspecial 

```html
{
  "FormaPagamento": 4,
  "TipoTransacao": 12,
  "Origem": 60,
  "Canal": "EnergisaON",
  "Antecipado": true,
  "Arrecadacoes": [
    {
       "CodigoInformado": "84670000002203602962021102518700000987654321",
       "Valor": 220.36,
	     "Adimplente": true 
    }
  ],
  "DetalhesCliente": {
      "NomeCliente": "José Tadeu Bijus",
      "CodigoCliente": "123456789",
      "CpfCnpj": "01234567890",
      "Telefone": "21987654321",
      "Email": "emaildocliente@teste.com",
      "DataNascimento": "2000-01-01",
      "Endereco": {
         "Logradouro": "R. Alexandre Mackenzie",
         "Numero": "75",
         "Complemento": "7º andar",
         "Cidade": "Rio de Janeiro",
         "Uf": "RJ",
         "Cep": "20221410",
         "Pais": "BR"
      }
   },
  "Parcelas": 
  [
    {
      "numParcela": 5,
      "desParcela":"5x",
      "taxa": 1.11,
      "valor": 220.36,
      "valorJuros": 24.24,
      "taxaFixa": 2.00,
      "valorParcela": 49.32,
      "valorTotal": 246.60
    }
  ],  
  "MerchantDefinedData":
  [
    {
      "id": "1",
      "value": "1"
    }
  ]
}

*Todos os campos são obrigatórios com exceção do campo "taxaFixa" dentro de "parcelas".
**Números e informações do exemplo são fictícias.
***Não esqueça de enviar o token obtido na autenticação como 'Bearer Token'
```

**Orientação dos campos:**
- FormaPagamento - Identificador da Forma de Pagamento. Usar:
	- 4 (Pagamento em Cartão de Crédito)
 	- 1 (Pagamento Cartão de Débito)
- TipoTransacao - Identificador do Tipo de Transacação. Usar:
	- 12 (Cartão de Crédito Parcelado pela Administradora).
 	- 20 (Cartão de Débito)
- Origem - Identificador da origem da transação. Usar 60 E-Commerce
- Canal - Identificador do canal utilizado
- Antecipado – Identifica se é uma transação a ter repasse antecipado ou não. Campo boleano (true ou false).
- Arrecadacoes - Lista de documentos de arrecadação.
	- CodigoInformado - Código de barras. Pode ser tanto 44 ou 48 caracteres.
	- Valor - Valor da fatura.
  - Adimplente - Indicação se o cliente é adimplente ou não. Podendo ser true ou false.
- DetalhesCliente - Informações sobre o cliente
	- NomeCliente - Nome completo do cliente
	- CodigoCliente - Unidade Consumidora ou Identificação do Cliente
	- CpfCnpj - Cadastro de Pessoa Física ou Cadastro Nacional de Pessoa Jurídica
	- Telefone - Número de telefone com DDD. Ex: 99999999999
	- Email - Endereço eletrônico do cliente
	- DataNascimento - Data de Nascimento do Cliente
	- Endereco - Endereço do cliente titular da(s) fatura(s).
		- Logradouro - Logradouro do endereço do cliente
		- Numero - Número do endereço do cliente
		- Complemento - Complemento do endereço do endereço do cliente
		- Cidade - Cidade do endereço do cliente
		- Uf - Unidade Federativa do endereço do cliente
		- Cep - Código de Endereçamento Postal do endereço do cliente
		- Pais - País do endereço do cliente
- Parcelas – Lista de parcelas do pagamento
  - numParcela – Número da Parcela - Inteiro
  - desParcela – Descrição da Parcela - String
  - Taxa – Multiplicador referente ao Juros da Parcela - Decimal
  - Valor – Somatório dos valores das Faturas (Original sem Juros) - Decimal
  - ValorJuros – Valor Calculado de Juros - Decimal
  - TaxaFixa – Valor Taxa Fixa (Não Obrigatório) - Decimal em Reais
  - ValorParcela - Valor referente à parcela apresentada ao cliente
  - ValorTotal – Valor Total da Transação (Somatório do 'Valor' + 'ValorJuros' + 'TaxaFixa') - Decimal
- MerchantDefinedData - Lista de regras de Anti Fraude
  - id - Código de identificação do MDD
  - value - Informação correspondente ao MDD
	

**Resultado com sucesso:**
```
{
    "sucesso": true,
    "mensagem": "Processo realizado com sucesso.",
    "dados": {
      "frameLink": "https://app-framespa-hml.azurewebsites.net/CardFormCustom?token=20220215BB4B51B73011467E9F41CCD76DB52D72",
      "frameToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1lIjoiTXVsdGlwYWdvc0lmcmFtZSIsIm5iZiI6MTY0NzgzMTU4NSwiZXhwIjoxOTYzMTkxNTg1LCJpc3MiOiJIdWJQYWdhbWVudG8iLCJhdWQiOiJFdmVyeW9uZSJ9.yHciL7W5UMtoUpuaz0pyyawAxABrjMgodzmkrxbC-is",
      "orderId": "LTYz0DA5MzQ3Mwaa"
    },
    "totalRegistros": 1,
    "validadeToken": "Token expira em : 59 minutos e 1 segundos.",
    "dataValidadeToken": "2024-06-07T12:17:14-03:00"
}
StatusCode: 200
```
**Campo Inválido**
```
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalRegistros": 0,
    "mensagemErro": "O campo (X) é inválido.",
    "validadeToken": "Token expira em : 59 minutos e 1 segundos."
}
StatusCode: 400
```

**Campo obrigatório não preenchido**
```
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalRegistros": 0,
    "mensagemErro": "O campo (X) é obrigatório e não foi preenchido.",
    "validadeToken": "Token expira em : 59 minutos e 1 segundos."
}
StatusCode: 400
```

**Pagamento Já recebido**
```
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalRegistros": 0,
    "mensagemErro": "Ocorreu um erro no Cod Barras: 84670000002203602962021102518700000987654321. Motivo: Não Autorizada - Já Recebido",
    "validadeToken": "Token expira em : 59 minutos e 1 segundos."
}
StatusCode: 400
```

**Fatura Vencida**
```
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalRegistros": 0,
    "mensagemErro": "Ocorreu um erro no Cod Barras: 84670000002203602962021102518700000987654321. Motivo: Não Autorizada - Vencida",
    "validadeToken": "Token expira em : 59 minutos e 1 segundos."
}
StatusCode: 400
```

**Empresa não Autorizada**
```
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalRegistros": 0,
    "mensagemErro": "Ocorreu um erro no Cod Barras: 84670000002203602962021102518700000987654321. Motivo: Não Autorizada - Empresa não Autorizada",
    "validadeToken": "Token expira em : 59 minutos e 1 segundos."
}
StatusCode: 400
```

**Arrecadação sem Valor no Código de Barras**
```
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalRegistros": 0,
    "mensagemErro": "Ocorreu um erro no Cod Barras: 84670000002203602962021102518700000987654321. Motivo: Não Autorizada - Sem valor",
    "validadeToken": "Token expira em : 59 minutos e 1 segundos."
}
StatusCode: 400
```

**PDV sem forma de pagamento cadastrada**
```
{
    "sucesso": false,
    "mensagem": "Falha ao obter o frame de pagamentos.",
    "totalRegistros": 0,
    "mensagemErro": "Nenhuma forma de pagamento encontrada",
    "validadeToken": "Token expira em : 59 minutos e 1 segundos."
}
StatusCode: 400
```

# **Utilizando as informações para montar o frame**
Após a execução do passo acima, será possível a renderização de um iframe autenticado em tela.
Segue exemplo de como utilizar o frameLink e o frameToken:

**Html**
```
<div id="divFrame">
  <iframe allowpaymentrequest="true" id="output_iframe_id"></iframe>
</div>
```
**Javascript**
```
$.ajax({
	type: "GET", 
	crossDomain: true,
	url: $("#frameLink").val(),
	contentType: "application/json",
	beforeSend: function(xhr, settings){
	  xhr.setRequestHeader("Authorization", `Bearer ${#frameToken}`);
	},
	success: function(data){
	  $("#output_iframe_id").attr('src',data.url);
	},
	error: function(e) {
	  console.log(e);
	}
});
```

- **Para mais detalhes vide arquivo de exemplo adicionado nesse artigo: [ExemploNavegadorAuthHeader.html](https://github.com/multipagos/NovosParceiros/blob/main/Frame%20de%20Pagamentos/Frame%20Especial/ExemploNavegadorAuthHeader.html)**


# **Dados de Teste**
Para realizar testes de pagamento de faturas, usar os seguintes dados de cartão de crédito:
```
Número cartão : 4024 0071 5562 2151
CVV : 197
Data : 08/25
```

# **Término do Fluxo: Recebimento de Informações de pagamento via WebHook Multicom**
Primeiramente, é necessário assinar o WebHook para receber notificações de pagamentos realizados com sucesso no frame.
Para isso, após autenticado com sucesso pela API de geração de Token, realize a request abaixo.
- POST /api/v1/WebHook/AssinarWebHook
```
{
  "nome": "Banco X",
  "funcionalidadeId": 5,
  "url": "https://SuaUrlAqui/resource", 
  "tipoAutenticacao": 1,
  "objetoAutenticacao": "{'user': 'nossoUsuarioDeAutenticacao','password': 'nossaSenhaDeAutenticacao'}",
  "urlAutenticacao": "https://SuaUrlAqui.com.br/api/Login/Token",
  "campoToken": "token",
  "idExterno": "ChaveCombinada"
}
```

**Orientação dos campos:**
- nome - Identificação do assinante. Nome da instituição parceira.
- funcionalidadeId - Identificação da funcionalidade assinada. Favor setar aqui o valor 5 pois é referente à funcionalidade de Notificações de Pagamentos via Frame.
- url - Url que receberá as mensagem enviadas pelo WebHook. Sugerimos o uso de https://webhook.site/ para homologação.
- tipoAutenticacao - Indicação de necessidade de autenticação ou não do campo 'url'. Obrigatório. Podendo ser:
	- 0 - Nenhuma
	- 1 - Bearer Token
	- 2 - Basic Auth
	- 3 - Bearer Cognito AWS
  -	4 - Application Token (token não expira)
	- Caso você não utilize nenhuma dessas autenticações, favor sinalizar para viabilizarmos o desenvolvimento.
- objetoAutenticacao - Campo string indicando as informações necessárias para autenticarmos em sua 'url'. Obrigatório se 'tipoAutenticacao' for diferente de 0.

**Importante:** Por ser um campo String, precisa ter aspas duplas ao redor da resposta. Favor converter as aspas duplas de seu objeto em aspas simples. 

**Exemplos:**
```
- TipoAutenticacao = 1 (Bearer Token)
	Ex: "{'user': 'nossoUsuarioDeAutenticacao','password': 'nossaSenhaDeAutenticacao'}" 

- TipoAutenticacao = 2 (Basic Auth)
	Ex: "usuario:senha"

- TipoAutenticacao = 3 (Bearer Cognito AWS)
	Ex: "{'user': 'nossoUsuarioDeAutenticacao','password': 'nossaSenhaDeAutenticacao', 'scope': 'escopo'}" 
  
- TipoAutenticacao = 4 (Application Token)
	Ex: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6IjEiLCJuYmYiOjE2MTU5MDg0MjQsImV4cCI6MTYxNTkwODQ4NCwiaWF0IjoxNjE1OTA4NDI0fQ.Iy7m-U1KPomjQTh2tN3X5gGXn6LvE3W4H3dBRnc5-7s" 
```

- urlAutenticacao - Url completa do endpoint de autenticação.Obrigatório se 'tipoAutenticacao' for diferente de 0. Exemplo: "https://sandbox.multipagos.com.br/api/Multipagos/Token"
- campoToken - Propriedade responsável por indicar onde o token de autenticação estará no retorno da request. Exemplo:
_No exemplo abaixo, o campo "token" é o campo que precisaria ser indicado em "campoToken"._
```html
{
    "sucesso": true,
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6IjEiLCJuYmYiOjE2MTU5MDg0MjQsImV4cCI6MTYxNTkwODQ4NCwiaWF0IjoxNjE1OTA4NDI0fQ.Iy7m-U1KPomjQTh2tN3X5gGXn6LvE3W4H3dBRnc5-7s"
}
```
- idExterno - Identificação externa combinada entre as partes

**Resultado com sucesso:**
```
{
  "sucesso": true,
  "mensagem": "Assinatura realizada com sucesso.",
  "validadeToken": "Token expira em : 59 minutos e 18 segundos.",
  "id_Externo": "I1NiIsInR5cCI6IkpXVCJ9",
  "id": 123
}
StatusCode: 200

```

**Resultado com falha:**
```
{
  "sucesso": false,
  "mensagem": "Erro ao cadastrar assinatura.",
  "validadeToken": "Token expira em : 55 minutos e 36 segundos."
}
StatusCode: 400
```

Após a assinatura do serviço, você já estará apto a receber notificações de pagamentos (quando ocorrerem).
Em caso de ocorrência de tentativa de pagamento, iremos disparar uma request POST à url assinada no passo anterior com os detalhes do pagamento.
O campo de referência da request PagParceladoFrame para a mensagem do WebHook é o **"orderId"**.
**Payload em caso de sucesso no pagamento**
_Header:_ 
Content-Type: application/json
```
{
  "data": {
    "sucess": true,
    "service": "ChaveCombinada",
    "statusCode": "8",
    "statusMessage": "CAPTURED",
    "transactionId": "100-022-2022062-00_15-162429362",
    "flag": "visa",
    "card": "481716-0881",
    "value": 69.89,
    "numberInstallments": 2,
    "orderId": "LTYz0DA5MzQ3Mwaa",
    "orderDate": "2022-03-03T16:24:29.3628480-03:00",
    "acquirator": {
      "nsu": "239280",
      "authorizationCode": "670763",
      "acquiratorCode": "1",
      "transactionId": "0303042431832",
      "responseCode": "6",
      "responseDescription": "CAPTURED"
    }
  }
}
```
**Payload em caso de falha na autorização do pagamento**
_Header:_ 
Content-Type: application/json
```
{
  "data": {
    "sucess": false,
    "service": "Ecommerce-S3",
    "statusCode": "4",
    "statusMessage": "UNAUTHORIZED",
    "transactionId": "100-022-2022062-65410-153728675",
    "flag": "visa",
    "card": "481716-0881",
    "value": 69.89,
    "numberInstallments": 2,
    "orderId": "LTYz0DA5MzQ3Mwaa",
    "orderDate": "2022-03-03T15:37:28.6770000",
    "acquirator": {
      "nsu": "",
      "authorizationCode": "",
      "acquiratorCode": "1",
      "responseCode": "102",
      "responseDescription": "NOT APPROVED"
    }
  }
}
```

**Orientação dos campos:**
- sucess - true | false - Se true foi autorizado o pagamento.
- service - Nome do Serviço.
- statusCode - Status da transação em conjunto com  "statusMessage". Podendo ser:
	- 0 - Criada / Em andamento
	- 1 - Autenticada
	- 2 - Não-Autenticada
	- 3 - Autorizada pela operadora
	- 4 - Não-Autorizada pela operadora
	- 5 - Em Cancelamento
	- 6 - Cancelado
	- 7 - Em Captura
	- 8 - Capturada / Finalizada
	- 9 - Não-Capturada
	- 10 - Pagamento Recorrente - Agendado
	- 16 - Não Cancelada
	- 19 - Autorizado de fraude
	- 20 - Cancelado por fraude
	- 21 - Não Autorizada por erro
	- 22 - Agendamento Finalizado
	- 23 - Agendamento Pausado
	- 409 - Erro de negócio
- statusMessage - Status da Transação
- transactionId - Numero da Transação no HUB
- flag - Bandeira
- card - Numero do cartão
- value - Valor total do pagamento
- numberInstallments - Numero de parcelas realizadas
- orderId - Código de referência da transação
- orderDate - Data Hora e segundo da transação de pagamento
	- acquirator - Informações da Adquirente
	- nsu - NSU do pagamento
	- authorizationCode - Código da autorização do pagamento
	- acquiratorCode - Adquirente. Podendo ser:
		- CIELO = 1
		- STONE = 20
		- GETNET = 22
		- GLOBALPAYMENTS = 24
	- transactionId - Numero da transação na Adquirente
	- responseCode - Código de Resposta da Adquirente (verificar o manual do adquirente)
	- responseDescription - Descrição da Resposta da Adquirente (verificar o manual do adquirente)


# **Descadastramento de WebHook**
Caso deseje descadastrar sua assinatura de Webhook em nosso sistema para não mais receber mensagens de webhooks, você poderá descadastrar seguinte os seguintes passos: 
- PUT /api/v1/WebHook/DescadastrarWebhook/{id}

**Orientação dos campos:**
- id - Identificação gerada para a sua assinatura recebido no cadastramento.

**Resultado com sucesso:**
```
{
  "sucesso": true,
  "mensagem": "Assinatura Descadastrada com sucesso.",
  "validadeToken": "Token expira em : 59 minutos e 37 segundos."
}
```

**Resultado com falha:**
```
{
  "sucesso": false,
  "mensagem": "Não foi possível realizar o descadastramento. Tente novamente mais tarde.",
  "validadeToken": "Token expira em : 59 minutos e 37 segundos."
}
```
