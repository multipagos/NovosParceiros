# Novos Parceiros

Seja bem vindo ao guia de orientação à integrações de Arrecadações com Multipagos!

Através da documentação abaixo você será capaz de utilizar os serviços Multipagos que foram construídos para facilitar a consulta e a transição de informações entre suas arrecações e o grupo Claro.

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

- POST /api/v1/Pagamento/PagParceladoFrame

```html
{
	"FormaPagamento": 1,
	"TipoTransacao": 12,
	"Origem": 60,
	"NomeCliente": "Nome do Cliente",
	"FaturaCartao":{
		"Endereco": {
			"Logradouro": "R. Alexandre Mackenzie",
			"Numero": "75",
			"Complemento": "7º andar",
			"Cidade": "Rio de Janeiro",
			"Uf": "RJ",
			"Cep": "20221410",
			"Pais": "BR"
		},
		"Telefone": "21987654321",
		"Email": "emaildocliente@teste.com"
	},
	"Arrecadacoes": [
		{
		  "CodigoInformado": "84670000002203602962021102518700000987654321",
		  "Valor": 220.36
		}
	]
}
*Todos os campos são obrigatórios
**Números e informações do exemplo são fictícias.
***Não esqueça de enviar o token obtido na autenticação como 'Bearer Token'
```

**Orientação dos campos:**
- FormaPagamento - Identificador da Forma de Pagamento. Usar 1 (Terminal Digital/Eletrônico).
- TipoTransacao - Identificador do Tipo de Transacação. Usar 12 (Cartão de Crédito Parcelado pela Administradora).
- Origem - Identificador da origem da transação. Usar 60 (Claro / Hub)
- NomeCliente - Nome do cliente titular da(s) fatura(s).
- FaturaCartao - Detalhes da Fatura.
	- Endereco - Endereço do cliente titular da(s) fatura(s).
		- Logradouro
		- Numero
		- Complemento
		- Cidade
		- Uf
		- Cep
		- Pais
	- Telefone - Telefone de contato do cliente.
	- Email - E-mail de contato do cliente.
- Arrecadacoes - Lista de documentos de arrecadação.
	- CodigoInformado - Código de barras. Pode ser tanto 44 ou 48 caracteres.
	- Valor - Valor da fatura.
	

**Resultado com sucesso:**
```
{
    "sucesso": true,
    "mensagem": "Processo realizado com sucesso.",
    "data": {
        "frameLink": "https://app-framespa-hml.azurewebsites.net/CardFormCustom?token=20220215BB4B51B73011467E9F41CCD76DB52D72",
	"id": "LTYz0DA5MzQ3Mw=="
    },
    "totalDeRegistros": 1,
    "mensagemErro": "",
    "validadeToken": "Token expira em : 59 minutos e 1 segundos."
}

StatusCode: 200
```
**Campo Inválido**
```
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalDeRegistros": 0,
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
    "totalDeRegistros": 0,
    "mensagemErro": "O campo (X) é obrigatório e não foi preenchido.",
    "validadeToken": "Token expira em : 59 minutos e 1 segundos."
}

StatusCode: 400
```

# **Término do Fluxo: Recebimento de Informações de pagamento via WebHook Multicom**
Primeiramente, é necessário assinar o WebHook para receber notificações de pagamentos realizados com sucesso no frame.
Para isso, após autenticado com sucesso pela API de geração de Token, realize a request abaixo.
- POST /api/v1/WebHook/AssinarWebHook
```
{
  "nome": "Banco X",
  "funcionalidadeId": 5,
  "url": "SuaUrlAqui/resource", 
  "idExterno": "bancox1234" 
}
```

**Orientação dos campos:**
- nome - Identificação do assinante. Nome da instituição parceira.
- funcionalidadeId - Identificação da funcionalidade assinada. Iremos passar a lista de funcionalidades disponíveis.
- url - Url que receberá as mensagem enviadas pelo WebHook. Sugerimos o uso de https://webhook.site/ para homologação.
- idExterno - Identificação externa 


**Resultado com sucesso:**
```
{
  "sucesso": true,
  "mensagem": "Assinatura realizada com sucesso.",
  "validadeToken": "Token expira em : 55 minutos e 36 segundos."
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
**Payload em caso de sucesso no pagamento**
```
{
  "data": {
    "uid": "LTYz0DA5MzQ3Mw==",
    "sucess": true,
    "service": "ClaroEcommerce-S3",
    "statusCode": "3",
    "statusMessage": "AUTHORIZED",
    "transactionId": "200-027-2022055-22104-145334446",
    "flag": "visa",
    "card": "483150-2271",
    "value": 79,
    "numberInstallments": 1,
    "orderId": "300003022104",
    "orderDate": "2022-02-24T16:20:24.7917546-03:00",
    "acquirator": {
      "nsu": "16473705",
      "authorizationCode": "068998",
      "acquiratorCode": "27",
      "transactionId": "",
      "responseCode": "16473705",
      "responseDescription": "APPROVED"
    }
  }
}
```
**Payload em caso de falha no pagamento**
```
{
  "data": {
    "uid": "LTYz0DA5MzQ3Mw==",
    "sucess": false,
    "service": "ClaroEcommerce-S3",
    "statusCode": "3",
    "statusMessage": "AUTHORIZED",
    "transactionId": "200-027-2022055-22104-145334446",
    "flag": "visa",
    "card": "483150-2271",
    "value": 79,
    "numberInstallments": 1,
    "orderId": "300003022104",
    "orderDate": "2022-02-24T16:20:24.7917546-03:00",
    "acquirator": {
      "nsu": "16473705",
      "authorizationCode": "068998",
      "acquiratorCode": "27",
      "transactionId": "",
      "responseCode": "16473705",
      "responseDescription": "DECLINED"
    }
  }
}
```