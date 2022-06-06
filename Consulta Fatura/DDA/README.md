# Novos Parceiros

Seja bem vindo ao guia de orientação à integrações com Multipagos!

Através da documentação abaixo você será capaz de utilizar os serviços Multipagos que foram construídos para facilitar a consulta e a transição de informações entre suas arrecações e o grupo Claro.

Qualquer dúvida que possa ter, fique à vontade de enviar um e-mail para team-mpagos@multipagos.com.br que iremos te ajudar.



# SandBox
Disponibilizamos um ambiente SandBox para que você possa realizar os testes. Fique à vontade para usar!
- Url SandBox: https://sandbox.multipagos.com.br


# Autenticação
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
```
**Token válido por 1 hora.**

Retorno em caso de falha:
```html
{
    "sucesso": false,
    "message": "Pdv ou Terminal inválidos."
}
```
# **Início do Fluxo: Realizar consulta de Fatura de Cliente**
O primeiro passo é realizar a consulta normal do cliente. Essa consulta irá verificar se o cliente possui pendências e ao final iremos guardar as informações recebidas para que antes do próximo vencimento, de forma proativa, enviaremos à vocês as pendências do cliente para oferecerem o pagamento.
Seguem detalhes sobre os campos que precisam ser enviados.
# Consulta de Fatura

- POST /api/v1/DDA/realizaConsulta 
```html
{
  "EmpresaId": 8,
  "IdentificacaoCliente": "12345678912"
}
*Atenção - IdentificadorCliente inválido propositalmente.
```

**Orientação dos campos:**
- EmpresaId - Referência da empresa podendo ser:
  - 8 - NET ou Claro
- IdentificacaoCliente - Forma de identificar o cliente podendo ser:
  - CPF ou CNPJ para faturas NET sem pontuação. Exemplo: 12345678912
  - Numero de celular com DDD. Exemplo: 21987654321


Retorno em caso de sucesso em obter fatura:
```html
{
    "sucesso": true,
    "mensagem": "listagem efetuada",
    "data": {
        "identificacaoCliente": "12345678912",
        "pendencias": [
            {
                "vencimento": "2021-01-01",
                "fatura": "846000000000362007101022748063700980021196891234",
                "valor": 105.2,	
                "pendenciaOriginal": "<?xml version=\"1.0\"?>\r\n<NetClaroFatura xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\">\r\n  <billAmount>105.20</billAmount>\r\n  <paymentDueDate>2021-01-01T00:00:00</paymentDueDate>\r\n  <status>Aberta</status>\r\n  <billCode>846000000000362007101022748063700980021196891234</billCode>\r\n  <name>NOME DO USUÁRIO AQUI</name>\r\n  <partyIdentificationNumber>12345678912</partyIdentificationNumber>\r\n</NetClaroFatura>",
                "empresa": "NET"
            }
        ]
    },
    "totalDeRegistros": 1,
    "mensagemErro": ""
}
```


Retorno em caso de não encontrar fatura em aberto para o cliente:
```html
{
    "sucesso": true,
    "mensagem": "Nenhuma fatura em aberto foi encontrada",
    "data": {
        "identificacaoCliente": "12345678912",
        "pendencias": []
    },
    "totalDeRegistros": 0,
    "mensagemErro": ""
}
```

Retorno em caso EmpresaId incorreta
```html
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalDeRegistros": 0,
    "mensagemErro": "A Empresa informada não existe no serviço de consulta de fatura."
}
```


Retorno em caso EmpresaId não enviado
```html
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalDeRegistros": 0,
    "mensagemErro": "O campo 'EmpresaId' é obrigatório."
}
```

Retorno em caso IdentificacaoCliente com caracteres inválidos
```html
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalDeRegistros": 0,
    "mensagemErro": "O campo 'IdentificacaoCliente' possui caracteres inválidos."
}
```

Retorno em caso IdentificacaoCliente não enviado
```html
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalDeRegistros": 0,
    "mensagemErro": "O campo 'IdentificacaoCliente' é obrigatório."
}
```

# **Término do Fluxo: Recebimento de Informações de Arrecadação via WebHook Multicom**
Primeiramente, é necessário assinar o WebHook para receber notificações de faturas em aberto dos clientes previamente consultados.
Para isso, após autenticado com sucesso pela API de geração de Token, realize a request abaixo.
- POST /api/v1/WebHook/AssinarWebHook
```
{
  "nome": "Banco X",
  "funcionalidadeId": 6,
  "url": "https://SuaUrlAqui/resource", 
  "tipoAutenticacao": 1,
  "objetoAutenticacao": "{'user': 'nossoUsuarioDeAutenticacao','password': 'nossaSenhaDeAutenticacao'}",
  "urlAutenticacao": "https://SuaUrlAqui.com.br/api/Login/Token",
  "campoToken": "token"  
}
```

**Orientação dos campos:**
- nome - Identificação do assinante. Nome da instituição parceira. Obrigatório.
- funcionalidadeId - Identificação da funcionalidade assinada. Favor informar o valor 6 que equivale ao produto DDA. Obrigatório.
- url - Url que receberá as mensagem enviadas pelo WebHook. Sugerimos o uso de https://webhook.site/ para homologação. Obrigatório.
- tipoAutenticacao - Indicação de necessidade de autenticação ou não do campo 'url'. Obrigatório. Podendo ser:
	- 0 - Nenhuma
	- 1 - Bearer Token
	- 2 - Basic Auth
	- 3 - Bearer Cognito AWS
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
Após a assinatura do serviço, você já estará apto a receber notificações de pendências de faturas dos clientes enviados anteriormente (caso houverem).

Diariamente iremos procurar por clientes que possuem faturas próximas à data de vencimento. Ao encontrar, iremos disparar uma request POST à url assinada no passo anterior com os detalhes da Fatura.

O campo de referência para identificação do cliente na mensagem do WebHook é o **"IdentificadorCliente"** que corresponde ao CPF/CNPJ do mesmo.
Segue abaixo detalhes do contrato da request:

_Header:_ 
Content-Type: application/json
```
{
  "IdentificadorCliente": "00000000000",
  "DataVencimento":"2023-01-01",
  "Fatura":"84670000002203602962021102518700000987654321",
  "Valor": 220.36,
  "Empresa": "NET",
}
```

**Orientação dos campos:**
- IdentificadorCliente - Identificação do cliente (CPF/CNPJ).
- DataVencimento - Data de vencimento da fatura mais recente.
- Fatura - Número de Código de Barras
- Valor - Valor da fatura
- Empresa - Empresa referente à fatura.

# **Inativar um Agendamento de Cliente**
Em caso de um cliente cancelar o uso de seu serviço, e vocês não desejam mais receber notificações de faturas desse cliente, basta você inativar ele em nossa base por essa API e então você não receberá mais notificações desse cliente.

- PUT /api/v1/DDA/InativarAgendamento 
```
{
  "identificacaoCliente": "12345678912"
}
*Não esquecer de estar autenticado na nossa API de Token.
```

**Resultado com sucesso:**
```
{
    "sucesso": true,
    "mensagem": "Cliente inativado com sucesso",
    "totalDeRegistros": 1,
    "mensagemErro": ""
}
StatusCode: 200
```
**Resultado com falha:**
```
{
    "sucesso": false,
    "mensagem": "Não foi possível realizar a operação.",
    "totalDeRegistros": 1,
    "mensagemErro": "Cliente não encontrado"
}
StatusCode: 400
```


Caso seu cliente deseja retornar e você desejar agendar ele novamente no serviço, basta fazer novo cadastro em POST /api/DDA/ConsultaFatura.
