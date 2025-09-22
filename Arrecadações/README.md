# Novos Parceiros

Seja bem vindo ao guia de orientação à integrações de Arrecadações com Multipagos!

Através da documentação abaixo você será capaz de utilizar os serviços Multipagos que foram construídos para facilitar a consulta e a transição de informações entre suas arrecações e o grupo Claro.

Qualquer dúvida que possa ter, fique à vontade de enviar um e-mail para team-mpagos@multipagos.com.br que iremos te ajudar.

#Pagamento de Arrecadações Direto
Disponibilizamos duas APIs para a realização do processo de pagamento. Uma para consulta do status da arrecadação (de uso não obrigatório) e outra para registrar o pagamento de uma forma mais ágil e direta.

# SandBox
Disponibilizamos um ambiente SandBox para que você possa realizar os testes. Fique à vontade para usar!
- Url SandBox: https://sandbox.multipagos.com.br


# Autenticação
Processo de obtenção de token que irá garantir a autorização na utilização dos demais serviços.
- POST /api/Multipagos/Token
```html
{
  "pdv": 8000,
  "terminal": "6f579b30-46fb-456b-a81b-21e4304a8926"
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

# Consulta Detalhes da Fatura
Antes de realizar a venda em sua plataforma, caso deseje realizar uma consulta de informações da fatura, basta realizar a seguinte request:

- POST /api/v2/Pagamento/VerificarAutorizacao
```html
{
    "CodigoInformado": "84800000000018601622022081514940937601021123",
    "Origem": 30,
    "Valor": 1.86
}

*Não esqueça de enviar o token obtido na autenticação como 'Bearer Token'
```
**Orientação dos campos:**
- CodigoInformado: Código de Barras da fatura - Alfanumérico(44) - Obrigatório
- Origem: Identificação da origem do recebimento dessa consulta. Favor informar fixo 30. - Obrigatório
- Valor: Valor efetivamente recebido - Decimal(10) + V(99) - Não Obrigatório


# Mensagens de retorno possíveis:
De acordo com as informações recebidas, durante as nossas validações, podemos retornar as seguintes mensagens:

**Arrecadação recebida com sucesso**
```html
{
    "sucesso": true,
    "mensagem": "Autorização efetuada com sucesso.",
	"dados": {
		"statusArrecadacao" : "Autorizada",
		"codigoDeBarras": "84800000000018601622022081514940937601021123",
		"valor": 1.86,
		"empresaOrgaoId": "0162",
		"dataVencimento": "2022-09-30T00:00:00",
		"nomeEmpresa": "CLARO SP DDD 11",
		"cnpjEmpresa": "40432544044608"
	},
	"totalDeRegistros": 1,
	"mensagemErro": "",
	"validadeToken": "Token expira em: 45 minutos e 26 segundos."
}
StatusCode: 200
```
**Orientação dos campos:**
- StatusArrecadacao: Código de status da arrecadação. Podendo ser:
  - Autorizada
  - Não Autorizada - Empresa não Autorizada
  - Já Recebido
  - Inválido
- CodigoDeBarras: Código de Barras recebido.
- Valor: Valor do código de barras recebido - Decimal(10) + V(99).
- EmpresaOrgaoId: Codigo febraban do código de barras recebido.
- NomeEmpresa: Nome da empresa referente ao código de barras recebido.


**Token inválido**
```html
StatusCode: 401 Unauthorized
```

**Empresa não autorizada para o Pdv**
```html
{
  "sucesso": false,
  "mensagem": "O pdv informado não possui a empresa do código de barras autorizada. Favor procurar o suporte.",
  "totalDeRegistros": 0,
  "validadeToken": "Token expira em : 57 minutos e 59 segundos."
}
StatusCode: 400
```

**Código de Barras informado é inválido**
```html
{
  "dados": null,
  "sucesso": false,
  "mensagem": "O código de barras informado é inválido. Favor verificar.",
  "totalDeRegistros": 0,
  "validadeToken": "Token expira em : 57 minutos e 30 segundos."
}
StatusCode: 400
```

**Código de Barras já recebido**
```html
{
  "dados": null,
  "sucesso": false,
  "mensagem": "Já foi realizado o pagamento dessa conta.",
  "totalDeRegistros": 0,
  "validadeToken": "Token expira em : 57 minutos e 59 segundos."
}
StatusCode: 400
```

# Envio de Arrecadação
Após a realização de uma venda em sua plataforma, envie as seguintes informações para que possamos processar e encaminhar ao Grupo Claro:

- POST /api/Multipagos/Arrecadacao
```html
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
- DataPagamento: Data da efetivação do pagamento em sua plataforma - formato yyyy-MM-dd
- CodigoBarras: Código de Barras da fatura - Alfanumérico(44)
- ValorRecebido: Valor efetivamente recebido - Decimal(10) + V(99)
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



# Mensagens de retorno possíveis:
De acordo com as informações recebidas, durante as nossas validações, podemos retornar as seguintes mensagens:

**Arrecadação recebida com sucesso**
```html
{
    "sucesso": true,
	"dataPagamento": "2022-10-04T16:35:23",
    "mensagem": "Pagamento (Número do Código de Barras de 44 caracteres) recebido e processado com sucesso."
}
```

**Usuário não Autorizado**
```html
{
    "sucesso": false,
    "mensagem": "401 Unauthorized - Token não autorizado, A aplicação encaminhou uma credencial invalida ou inexistente."
}
```

**Código da Agência arrecadadora igual a zero ou vazio**
```html
{
    "sucesso": false,
    "mensagem": "Código Agência Arrecadadora inválido.",
    "detalheErro": "Código Agência Arrecadadora não pode ser zero / branco."
}

```
**Forma de arrecadação/captura igual a zero ou vazio**
```html

{
    "sucesso": false,
    "mensagem": "Forma Arrecadação Captura inválido.",
    "detalheErro": "Forma Arrecadação Captura não pode ser zero / branco."
}
```
**Forma de arrecadação/captura diferente das informações permitidas**
```html
{
    "sucesso": false,
    "mensagem": "Forma Arrecadacao Captura inválida.",
    "detalheErro": "Forma de arrecadação / captura (Código inserido) Inválida."
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
    "detalheErro": "Forma Pagamento não pode ser zero / branco."
}
```

**Código de barras vazio**
```html
{
    "sucesso": false,
    "mensagem": "Código de Barras inválido.",
    "detalheErro": "Código de Barras não pode ser zero / branco."
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

**Código de Barras com identificador de tipo do valor inválido**
```html
{
    "sucesso": false,
    "mensagem": "Código de barras inválido.",
    "detalheErro": "Código de barras com identificador de tipo do valor inválido(número inserido)."
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

**Arrecadação vencida e configurado para não aceitar arrecadação vencida**
```html
{
    "sucesso": false,
    "mensagem": "Data de Vencimento Inválida.",
    "detalheErro": "A data de vencimento deve ser maior ou igual que a data corrente no formato aaaaMMdd."
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
    "detalheErro": "Foi encontrada uma arrecadação com esse código de barras (número do código de barras) e número autenticação (número autenticação) com o pagamento registrado com sucesso."
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



# Tipos HTTP de Erros
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

