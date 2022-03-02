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
```
**Token válido por 1 hora.**

Retorno em caso de falha:
```html
{
    "sucesso": false,
    "message": "Pdv ou Terminal inválidos."
}
```

# **Envio de Informações de Débito Automático**
Seguem detalhes sobre os campos que precisam ser enviados.

- POST /api/v1/DebitoAutomatico/Retorno

```html
{
	"TotalDeRegistros": 3,
	"ValorTotalRegistros": 125.99,
	"CodigoConvenio": "CLAROMOVEL",
	"Nsa": 1,
	"Detalhes":
	[
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001008",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001123",
			"DataOperacao": "2022-02-04T12:01:02",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "F",
			"IdentificacaoClienteEmpresa": "00000000012",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001444",
			"UsoEmpresa": "",
			"DataDebito": "2022-02-04",
			"ValorDebito": 125.99,
			"StatusRetorno": "00",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "H",
			"IdentificacaoClienteEmpresa": "00000000010",
			"AgenciaDebito": "122",
			"IdentificacaoClienteBanco": "00000001555",
			"IdentificacaoClienteNovo": "00000001478",
			"Ocorrencia": "Descrição da rejeição",
			"CodigoMovimento": "3"
		}
	]
}
```

**Orientação dos campos:**
- TotalDeRegistros - Campo de validação. Vamos validar se o total de registros que enviou confere com o total de elementos em Detalhes.
- ValorTotalRegistros - Campo de validação. Vamos validar se o valor total de valores informado confere com o total das informações financeiras que recebemos.
- CodigoConvenio - Identificação do convênio ao qual os registros se referem. Vamos encaminhar a lista de convênios aceitos.
- Nsa - "Número sequencial de API" é um campo de validação. Serve para garantirmos que nenhuma request se perdeu ao longo do caminho.
- Detalhes - Lista de registros.
	- CodigoRegistro - Identificação do tipo de informação que será trafegada. Se for Retorno então pode ser B, F ou H.
	- IdentificacaoClienteEmpresa - Esta identificação deverá : Conter um processo de validação (DV), Ser única para cada cliente da empresa, Ser a mesma em todos os débitos consecutivos de um mesmo cliente. Esta informação será validada pelo Banco, conforme regra definida pela empresa, no momento do cadastramento.
	- AgenciaDebito - Identificação da Agência no Banco onde será efetuado o Débito Automático
	- IdentificacaoClienteBanco - Identificação utilizada pelo Banco para efetuar o débito 
	
Os demais campos podem variar de acor dom o CodigoRegistro. Podendo ser:
- CodigoRegistro: B - Cadastramento de Débito Automático
	- DataOperacao - Data e hora da operação/ocorrência
	- CodigoMovimento - Podendo ser:
		- 1 = Exclusão de optante pelo débito automático
		- 2 = Inclusão de optante pelo débito automático
- CodigoRegistro: F - Retorno do Débito Automático
	- UsoEmpresa - O conteúdo deverá ser idêntico ao anteriormente enviado pela Empresa, no registro tipo “E”
	- DataDebito - Conterá : Data do Vencimento, se o Código de Retorno, for diferente de “00” (não debitado); Data real do Débito, se o Código de retorno for igual a “00” (debitado). Formato AAAAMMDD.
	- ValorDebito - Conterá : Valor Original enviado, se o Código de Retorno, for diferente de “00” (não debitado); Valor efetivamente Debitado, se o Código de Retorno for igual a “00” (debitado)
	- CodigoMovimento - O conteúdo será ser idêntico ao anteriormente enviado pela Empresa, no registro tipo “E”
	- StatusRetorno:
````
“00” = Débito efetuado
“01” = Débito não efetuado - Insuficiência de fundos
“02” = Débito não efetuado - Conta corrente não cadastrada
“04” = Débito não efetuado - Outras restrições
“05” = Débito não efetuado – valor do débito excede valor limite aprovado.
“10” = Débito não efetuado - Agência em regime de encerramento
“12” = Débito não efetuado - Valor inválido
“13” = Débito não efetuado - Data de lançamento inválida
“14” = Débito não efetuado - Agência inválida
“15” = Débito não efetuado - conta corrente inválida
“18” = Débito não efetuado - Data do débito anterior à do processamento
“30” = Débito não efetuado - Sem contrato de débito automático
“31” = Débito efetuado em data diferente da data informada – feriado na praça de débito
“96” = Manutenção do Cadastro
“97” = Cancelamento - Não encontrado
“98” = Cancelamento - Não efetuado, fora do tempo hábil
“99” = Cancelamento - cancelado conforme solicitação
````

- CodigoRegistro: H
	- IdentificacaoClienteNovo - O conteúdo deverá ser idêntico ao anteriormente enviado pela Empresa, no registro tipo “D”
	- Ocorrencia - Mensagem explicativa do não processamento
	- CodigoMovimento - O conteúdo deverá ser idêntico ao anteriormente enviado pela Empresa, no registro tipo “D”

**Resultado com sucesso:**
```
{
    "sucesso": true,
    "mensagem": "Dados recebidos com sucesso. Vamos enviar à Claro e assim que houver retorno nós enviaremos.",
    "validadeToken": "Token expira em : 50 minutos e 53 segundos."
}
```
**Número Total de Registros é diferente da soma total dos itens.**
```
{
    "sucesso": false,
    "mensagem": "Número Total de Registros é diferente da soma total dos itens.",
    "validadeToken": "Token expira em : 50 minutos e 53 segundos."
}
```
**Convênio não encontrado**
```
{
    "sucesso": false,
    "mensagem": "Convênio não encontrado.",
    "validadeToken": "Token expira em : 50 minutos e 53 segundos."
}
```
**Código de Registro inválido**
```
{
    "sucesso": false,
    "mensagem": "Campo CodigoRegistro (X) inválido.",
    "validadeToken": "Token expira em : 50 minutos e 53 segundos."
}
```
**NSA fora de sequência**
```
{
    "sucesso": false,
    "mensagem": "Código de NSA fora de sequência. O último recebido foi 2.",
    "validadeToken": "Token expira em : 50 minutos e 53 segundos."
}
```



# **Exemplo de Fluxo de Débito Automático**
A seguir, temos um exemplo de uma request de cadastramento de correntistas que fizeram a adesão do Débito Automático.
Primeiramente, é necessário assinar o WebHook para receber notificações de Débito Automático
Após autenticado com sucesso, realize a request abaixo.
- POST /api/v1/WebHook/AssinarWebHook
```
{
  "nome": "Banco X",
  "funcionalidadeId": 4,
  "url": "SuaUrlAqui/resource", 
  "idExterno": "bancox1234" 
}
```

**Orientação dos campos:**
- nome - Identificação do assinante. Nome da instituição parceira
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
```

- POST /api/v1/DebitoAutomatico/Retorno
```html
{
	"TotalDeRegistros": 8,
	"ValorTotalRegistros": 0,
	"CodigoConvenio": "CLAROMOVEL",
	"Nsa": 1,
	"Detalhes":
	[
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001008",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001123",
			"DataOperacao": "2022-02-04T12:01:02",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001001",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001456",
			"DataOperacao": "2022-02-04T12:01:04",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001002",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001789",
			"DataOperacao": "2022-02-04T12:01:06",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001003",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001135",
			"DataOperacao": "2022-02-04T12:01:08",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001004",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001246",
			"DataOperacao": "2022-02-04T12:02:00",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001005",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001357",
			"DataOperacao": "2022-02-04T12:02:02",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001006",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001468",
			"DataOperacao": "2022-02-04T12:02:04",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001007",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001579",
			"DataOperacao": "2022-02-04T12:02:06",
			"CodigoMovimento": "1"
		}
	]
}
```

**Sucesso:**
```
{
    "sucesso": true,
    "mensagem": "Dados recebidos com sucesso. Vamos enviar à Claro e assim que tivermos respostas, nós enviaremos.",
    "validadeToken": "Token expira em : 50 minutos e 53 segundos."
}
```

**Request do WebHook para sua API:**
```
{
	"TotalDeRegistros": 7,
	"ValorTotalRegistros": 0,
	"CodigoConvenio": "CLAROMOVEL",
	"Nsa": 1,
	"Detalhes":
	[
		{
			"CodigoRegistro": "E",
			"IdentificacaoClienteEmpresa": "00000001004",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001246",   
			"DataVencimento": "2022-02-09",
			"ValorDebito": 30.50,
			"CodigoMoeda": "BRL",
			"UsoEmpresa": "",
			"CodigoMovimento": "0"
		},
		{
			"CodigoRegistro": "E",
			"IdentificacaoClienteEmpresa": "00000001007",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001579",   
			"DataVencimento": "2022-02-09",
			"ValorDebito": 99.99,
			"CodigoMoeda": "BRL",
			"UsoEmpresa": "",
			"CodigoMovimento": "0"
		},
		{
			"CodigoRegistro": "E",
			"IdentificacaoClienteEmpresa": "00000001006",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001468",   
			"DataVencimento": "2022-02-09",
			"ValorDebito": 49,54,
			"CodigoMoeda": "BRL",
			"UsoEmpresa": "",
			"CodigoMovimento": "0"
		},
		{
			"CodigoRegistro": "E",
			"IdentificacaoClienteEmpresa": "00000001001",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001456",   
			"DataVencimento": "2022-02-09",
			"ValorDebito": 234.89,
			"CodigoMoeda": "BRL",
			"UsoEmpresa": "",
			"CodigoMovimento": "0"
		},
		{
			"CodigoRegistro": "E",
			"IdentificacaoClienteEmpresa": "00000001004",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001246",   
			"DataVencimento": "2022-02-09",
			"ValorDebito": 370.11,
			"CodigoMoeda": "BRL",
			"UsoEmpresa": "",
			"CodigoMovimento": "0"
		},
		{
			"CodigoRegistro": "D",
			"IdentificacaoClienteEmpresa": "00000001003",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001135",   
			"IdentificacaoClienteEmpresaNovo": "00000002222",
			"Ocorrencia": "Motivo ocorrência",
			"CodigoMovimento": "0"
		},
		{
			"CodigoRegistro": "C",
			"IdentificacaoClienteEmpresa": "00000001008",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001123",   
			"Ocorrencia": "Motivo ocorrência",
			"Ocorrencia2": "Motivo ocorrência 2",
			"CodigoMovimento": "1"
		}
	]
}
```
**Agendamos uma rotina para realizar a RemessaFake para o WebHook de 1 em 1 hora.**