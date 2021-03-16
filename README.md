# NovosParceiros

# Introdução
Estamos muito felizes por você está aqui!
Através da documentação abaixo você será capaz de utilizar os serviços Multipagos que foram construídos para facilitar a consulta e a transição de informações entre suas arrecações e o grupo Claro.

Qualquer dúvida que possa ter, fique à vontade de enviar um e-mail para valniria.bandeira@multicomnet.com.br que iremos te ajudar.

# Erros
Entenda os principais códigos de retorno das nossas API's

 
**HTTP 401 – Unauthorized**
Ocorre quando sua aplicação encaminhou uma credencial invalida ou inexistente.

**HTTP 403 – Forbidden**
Ocorre quando sua conta ou aplicação não tem permissão para utilizar o serviço.

**HTTP 405 – Method Not Allowed**
Ocorre quando sua aplicação efetuou a chamada utilizando um método não esperado. Neste caso verifique se o método da chamada é GET , POST ou PUT.

**HTTP 415 – Cannot consume content type**
Ocorre quando não é encaminhado o Content-Type na chamada.

**HTTP 400 – Bad Request**
Ocorre quando um ou mais dados foram encaminhados de forma incorreta ou fora do padrão. Este retorno possui um JSON ou XML no corpo da mensagem que identifica quais os erros presentes na chamada, no retorno sempre terá um código e uma mensagem descrevendo o erro.

# SandBox
Disponibilizamos um ambiente SandBox para que você possa realizar os testes. Fique à vontade para usar!
- Url SandBox: https://multipagos-sandbox.com.br


# Autenticação
Processo de obtenção de token que irá garantir a autorização na utilização dos demais serviços.
- POST /api/Multipagos/Token
```html
{
  "pdv": "1",
  "password": "8FDC63EE-EC4E-416B-80F6-2F51EE5DC6CD"
}
```

Retorno em caso de sucesso:
```html
{
    "sucesso": true,
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6IjEiLCJuYmYiOjE2MTU5MDg0MjQsImV4cCI6MTYxNTkwODQ4NCwiaWF0IjoxNjE1OTA4NDI0fQ.Iy7m-U1KPomjQTh2tN3X5gGXn6LvE3W4H3dBRnc5-7s"
}
```


# Envio de Arrecadação
Após a realização de uma venda, ou até um conjunto de vendas, 

- POST /api/Multipagos/Arrecadacao
```html
[
   {
    "CodigoConvenio": "212073",                                   
    "CodigoBanco": "212",
    "DomicilioBancario": "00000000000028847008",
    "DataPagamento": "2020-08-11",
    "DataCredito": "2020-08-11",
    "CodigoBarras": "84600000000036200710102274806370098002119689",
    "ValorRecebido": 3.62,
    "VrTarifa": 0.35,
    "CodigoAgenciaArrecadora": "01",
    "FormaArrecadacaoCaptura": "3",
    "NumeroAutenticacao": "36961752",
    "FormaPagamento": "1"
   }
]
```
Retorno em caso de sucesso:
```html
{
    "isValid": true,
    "msg": "Pagamento recebido e está sendo processado."
}
```


# Consulta de Fatura
Para verificar se existem contas na Claro ou na Net de seus clientes, utilize:

- POST /api/Multipagos/ConsultaFatura
```html
{
  "PosId": 1,
  "TerminalId": "8FDC63EE-EC4E-416B-80F6-2F51EE5DC6CD",
  "NumSequencial": 10,
  "EmpresaId": 8,
  "IdentificacaoCliente": "12345678912",
  "Contrato": "123"
}
```
Retorno em caso de sucesso:
```html
{
    "PosId": 1,
    "NumSequencial": 10,
    "BloquearPDV": false,
    "AtualizacaoDisponivel": false,
    "DadosConsulta": {
        "Empresa": 8,
        "IdentificacaoCliente": "12345678912",
        "SucessoConsulta": false,
        "DescricaoAuxiliar": "Não foi possivel realizar a chamada ao servidor Net/Claro Invoices\nDetalhes: O servidor remoto retornou um erro: (400) Solicitação Incorreta.",
        "Pendencias": null,
        "Contratos": null
    }
}
```


