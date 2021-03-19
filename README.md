# NovosParceiros

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
- Url SandBox: https://sandbox.multipagos.com.br


# Autenticação
Processo de obtenção de token que irá garantir a autorização na utilização dos demais serviços.
- POST /api/Multipagos/Token
```html
{
  "pdv": "123",
  "terminal": "8FDC63EE-EC4E-416B-80F6-2F51EE5DC777"
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

Retorno em caso de falha:
```html
{
    "sucesso": false,
    "message": "Pdv ou Terminal inválidos."
}
```


# Envio de Arrecadação
Após a realização de uma venda, ou até um conjunto de vendas, 

- POST /api/Multipagos/Arrecadacao
```html
[
   {
    "CodigoConvenio": "1",                                   
    "CodigoBanco": "1",
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
**Orientação dos campos:**
- CodigoConvenio: Definido pelo banco - Alfanumérico(20)
- CodigoBanco: Código do Banco na câmara de compensação - Inteiro(3)
- DomicilioBancario:
- DataPagamento: Data da efetivação do pagamento em sua plataforma - formato yyyy-MM-dd
- DataCredito: Data da entrada do crédito - formato yyyy-MM-dd
- CodigoBarras: Código de Barras da fatura - Alfanumérico(44)
- ValorRecebido: Valor efetivamente recebido - Decimal(10) + V(99)
- VrTarifa: Valor da tarifa acordada contratualmente - Decimal(10) + V(99)
- CodigoAgenciaArrecadadora: Código da agência arrecadadora - Alfanumérico(8) 
- FormaArrecadacaoCaptura: Forma de arrecadação - Alfanumérico(1) podendo ser:
  - 1 – Guichê de Caixa com fatura/guia de arrecadação
  - 2 – Arrecadação Eletrônica com fatura/guia de arrecadação (terminais de auto - atendimento, ATM, home banking)
  - 3 – Internet/mobile com fatura/guia de arrecadação
  - 4 – Outros meios com fatura/guia de arrecadação
  - 5 – Correspondentes bancários com fatura/guia de arrecadação
  - 6 – Telefone com fatura/guia de arrecadação
  - 7 – Casas lotéricas com fatura/guia de arrecadação
  - 8 - Cartão/Multibanco com fatura/guia de arrecadação
  - 9 – PIX com fatura/guia de arrecadação
  - a – Guichê de Caixa sem fatura/guia de arrecadação
  - b – Arrecadação Eletrônica sem fatura/guia de arrecadação (terminais de auto - atendimento, ATM, home banking)
  - c – Internet/mobile sem fatura/guia de arrecadação
  - d – Correspondentes bancários sem fatura/guia de arrecadação
  - e – Telefone sem fatura/guia de arrecadação
  - f – Outros meios sem fatura/guia de arrecadação
  - g – Casas lotéricas sem fatura/guia de arrecadação
  - h – Cartão/Multibanco sem fatura/guia de arrecadação
  - i – PIX sem fatura/guia de arrecadação 
- NumeroAutenticacao: Número de autenticação caixa ou código de transação - Alfanumérico(23)
- FormaPagamento: Forma de pagamento - Inteiro(1) podendo ser:
  - 1 – Dinheiro
  - 2 – Cheque
  - 3 – Não identificado/outras formas
  - 4 – Cartão de crédito
  - 5 – Cartão/Multibanco
  - 6 – Débito em conta

Retorno em caso de sucesso:
```html
{
    "sucesso": true,
    "mensagem": "Pagamento recebido e está sendo processado."
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
  "EmpresaId": 4,
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
        "IdentificacaoCliente": "telephoneNumber::12345678912",
        "SucessoConsulta": true,
        "DescricaoAuxiliar": "Nenhuma fatura em aberto foi encontrada",
        "Pendencias": null,
        "Contratos": null
    }
}
```


