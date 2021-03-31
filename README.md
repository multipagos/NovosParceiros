# NovosParceiros

Estamos muito felizes por você está aqui!
Através da documentação abaixo você será capaz de utilizar os serviços Multipagos que foram construídos para facilitar a consulta e a transição de informações entre suas arrecações e o grupo Claro.

Qualquer dúvida que possa ter, fique à vontade de enviar um e-mail para valniria.bandeira@multicomnet.com.br que iremos te ajudar.


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

Retorno em caso de falha:
```html
{
    "sucesso": false,
    "message": "Pdv ou Terminal inválidos."
}
```


# Envio de Arrecadação
Após a realização de uma venda em sua plataforma, envie as seguintes informações para que possamos processar e encaminhar ao Grupo Claro:

- POST /api/Multipagos/Arrecadacao
```html
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
 
  {                                  
   "DataPagamento": "2020-08-11",
   "CodigoBarras": "84600000000036200710102274806370098002119689",
   "ValorRecebido": 3.62,
   "CodigoAgenciaArrecadora": "01",
   "FormaArrecadacaoCaptura": "3",
   "NumeroAutenticacao": "36961752",
   "FormaPagamento": "1"
 }

*Não esqueça de enviar o token obtido na autenticação como 'Bearer Token'
```
**Orientação dos campos:**
- CodigoConvenio: Definido pelo banco - Alfanumérico(20)
- CodigoBanco: Código do Banco na câmara de compensação - Inteiro(3)
- DomicilioBancario: Identificação da empresa/órgão no banco/agência/conta/dígito creditada - Alfanumérico(20)
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

# Mensagens de retorno possíveis:
De acordo com as informações recebidas, durante as nossas validações, podemos retornar as seguintes mensagens:

**Arrecadação recebida com sucesso**
```html
{
    "sucesso": true,
    "mensagem": "Pagamento (Número do Código de Barras de 44 caracteres) recebido e processado com sucesso."
}
*HTTP 200 – OK
```

**Usuário não Autorizado**
```html
{
    "sucesso": false,
    "mensagem": "Usuário não autenticado",
    "detalheErro": "O token da requisição deve ser válido"
}
*HTTP 401 – Unauthorized
```

**Código Agência Arrecadora Inválido**
```html
{
    "sucesso": false,
    "mensagem": "Código Agência Arrecadora inválido.",
    "detalheErro": "Código Agência Arrecadora não poder ser zero / branco."
}
*HTTP 200 – OK
```

**Código da Agência arrecadadora igual a zero ou vazio**
```html
{
    "sucesso": false,
    "mensagem": "Código Agência Arrecadora inválido.",
    "detalheErro": "Código Agência Arrecadora não poder ser zero / branco."
}
```

**Forma de arrecadação/ captura diferente das informações permitidas**
```html
{
    "sucesso": false,
    "mensagem": "Forma Arrecadação Captura inválido.",
    "detalheErro": "Forma Arrecadação Captura não poder ser zero / branco."
}
```

**Forma de Pagamento Inválida**
```html
{
    "sucesso": false,
    "mensagem": "Forma de Pagamento Inválida",
    "detalheErro": "Forma de Pagamento (código inserido) Inválida."
}
```

**Forma de Pagamento Zero ou Branco**
```html
{
    "sucesso": false,
    "mensagem": "Forma Pagamento inválido.",
    "detalheErro": "Forma Pagamento não poder ser zero / branco."
}
```

**Código de barras vazio**
```html
{
    "sucesso": false,
    "mensagem": "Código de Barras inválido.",
    "detalheErro": "Código de Barras não poder ser zero / branco."
}
```

**Código de Barras com tamanho incorreto**
```html
{
    "sucesso": false,
    "mensagem": "Código de barras inválido.",
    "detalheErro": "Código de barras com tamanho incorreto."
}
```

**Código de Barras zerado**
```html
{
    "sucesso": false,
    "mensagem": "Código de barras inválido.",
    "detalheErro": "Código de barras não é uma arrecadação(0)."
}
```

**Código de Barras com produto diferente de 8 (arrecadação)**
```html
{
    "sucesso": false,
    "mensagem": "Código de barras inválido.",
    "detalheErro": "Código de barras não é uma arrecadação(número inserido)."
}
```

**Código de Barras com segmento diferente de 4 (telecomunicações)**
```html
{
    "sucesso": false,
    "mensagem": "Código de barras inválido.",
    "detalheErro": "Segmento do Código de Barras é Inválido(número inserido)."
}
```

**Código de Barras com DV inválido**
```html
{
    "sucesso": false,
    "mensagem": "Código de barras inválido.",
    "detalheErro": "Código de barras com erro no cálculo do Modulo10."
}
```

**Cliente/Parceiro não habilitado para transferir arrecadações**
```html
{
    "sucesso": false,
    "mensagem": "Recebimento não autorizado.",
    "detalheErro": "Ponto não autorizado a receber arrecadações."
}
```

**Arrecadação enviada sem valor e configurado para não aceitar documento sem valor**
```html
{
    "sucesso": false,
    "mensagem": "Valor recebido Inválido.",
    "detalheErro": "Não é permitido receber arrecadação com valor zerado ou nulo."
}
```

**Arrecadação em duplicidade e configurado para não aceitar documento de mesmo código de barras**
```html
{
    "sucesso": false,
    "mensagem": "Arrecadação Duplicada.",
    "detalheErro": "Foi encontrada uma arrecadação com esse código de barras (número do código de barras) com o pagamento registrado com sucesso."
}
```

**Valor no Código de Barras diferente do valor informado e configurado para não aceitar documento com valor divergente entre o informado e o do Código de Barras**
```html
{
    "sucesso": false,
    "mensagem": "Valor da arrecadação (valor no código de barras) é diferente do valor declarado (valor informado na requisição)",
    "detalheErro": "O valor do código de barras da arrecadação é diferente do valor declarado como recebido"
}
```


# Consulta de Fatura
Para verificar se existem contas na Claro ou na Net de seus clientes, utilize:

- POST /api/Multipagos/ConsultaFatura
```html
{
  "PosId": 6000,
  "TerminalId": "48B20B9C-69B9-443E-9090-A5CE730DFA76",
  "NumSequencial": 10,
  "EmpresaId": 4,
  "IdentificacaoCliente": "12345678912",
  "Contrato": "123"
}
```
**Orientação dos campos:**
- PosId - Campo PDV enviado
- TerminalId - Campo Terminal enviado
- NumSequencial - Seu contador de utilização
- EmpresaId - Referência da empresa podendo ser:
  - 2 - NET
  - 4 - Claro
  - 8 - NET e Claro
- IdentificacaoCliente - Forma de identificar o cliente podendo ser:
  - CPF ou CNPJ para faturas NET sem pontuação. Exemplo: 12345678912
  - Numero de celular com DDD. Exemplo: 21987654321
- Contrato - Informação complementar de contrato do cliente com a Claro.


Retorno em caso de sucesso:
```html
{
    "PosId": 6000,
    "NumSequencial": 10,
    "BloquearPDV": false,
    "AtualizacaoDisponivel": false,
    "DadosConsulta": {
        "Empresa": 8,
        "IdentificacaoCliente": "telephoneNumber::21987654321",
        "SucessoConsulta": true,
        "DescricaoAuxiliar": "OK",
        "Pendencias": [
            {
                "Vencimento": "2021-01-01T00:00:00-03:00",
                "Fatura": "846000000000362007101022748063700980021196891234",
                "Valor": 31.69,
                "Empresa": 8,
                "PendenciaOriginal": "<?xml version=\"1.0\"?>\r\n<NetClaroFatura xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\">\r\n  <paymentDueDate>2021-01-01T00:00:00-03:00</paymentDueDate>\r\n  <billCode>846000000000362007101022748063700980021196891234</billCode>\r\n  <name>NOME DO USUÁRIO AQUI</name>\r\n  <partyIdentificationNumber>12345678912</partyIdentificationNumber>\r\n</NetClaroFatura>"
            }
        ],
        "Contratos": null
    }
}
```


Retorno em caso de não encontrar fatura em aberto:
```html
{
    "PosId": 6000,
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

Retorno em caso de IdentificacaoCliente não encontrada:
```html
{
    "PosId": 6000,
    "NumSequencial": 10,
    "BloquearPDV": false,
    "AtualizacaoDisponivel": false,
    "DadosConsulta": {
        "Empresa": 2,
        "IdentificacaoCliente": "02130063160",
        "SucessoConsulta": true,
        "DescricaoAuxiliar": "Cliente não foi encontrado",
        "Pendencias": null,
        "Contratos": null
    }
}
```


Retorno em caso de erro na Claro:
```html
{
    "PosId": 6000,
    "NumSequencial": 10,
    "BloquearPDV": false,
    "AtualizacaoDisponivel": false,
    "DadosConsulta": {
        "Empresa": 8,
        "IdentificacaoCliente": "02130063160",
        "SucessoConsulta": false,
        "DescricaoAuxiliar": "Não foi possivel realizar a chamada ao servidor Net/Claro Invoices\nDetalhes: The remote server returned an error: (400) Bad Request.",
        "Pendencias": null,
        "Contratos": null
    }
}
```
