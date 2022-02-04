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

- POST /api/DebitoAutomatico/Retorno

```html
{
	"TotalDeRegistros": 3,
	"ValorTotalRegistros": 125.99,
	"CodigoConvenio": "EMPRESA_A",
	"Nsa": 1,
	"Detalhes":
	[
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001008",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001123",
			"DataOperacao": "2022-02-04 12:01:02",
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
    "mensagem": "Convênio não encontrado",
    "validadeToken": "Token expira em : 50 minutos e 53 segundos."
}
```
**Código de Registro inválido**
```
{
    "sucesso": false,
    "mensagem": "Campo CodigoRegistro (X) inválido",
    "validadeToken": "Token expira em : 50 minutos e 53 segundos."
}
```
**NSA fora de sequência**
```
{
    "sucesso": false,
    "mensagem": "Código de NSA fora de sequência. Último registro: 2",
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
  "chave": "bancox",
  "funcionalidadeId": 4,
  "url": "SuaUrlAqui/resource",
  "idExterno": "6000"
}
```
**Resultado com sucesso:**
```
{
  "sucesso": true,
  "mensagem": "Assinatura cadastrada.",
  "validadeToken": "Token expira em : 55 minutos e 36 segundos."
}
```

- POST /api/DebitoAutomatico/Retorno
```html
{
	"TotalDeRegistros": 8,
	"ValorTotalRegistros": 0,
	"CodigoConvenio": "EMPRESA_A",
	"Nsa": 1,
	"Detalhes":
	[
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001008",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001123",
			"DataOperacao": "2022-02-04 12:01:02",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001001",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001456",
			"DataOperacao": "2022-02-04 12:01:04",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001002",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001789",
			"DataOperacao": "2022-02-04 12:01:06",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001003",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001135",
			"DataOperacao": "2022-02-04 12:01:08",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001004",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001246",
			"DataOperacao": "2022-02-04 12:02:00",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001005",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001357",
			"DataOperacao": "2022-02-04 12:02:02",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001006",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001468",
			"DataOperacao": "2022-02-04 12:02:04",
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001007",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001579",
			"DataOperacao": "2022-02-04 12:02:06",
			"CodigoMovimento": "1"
		}
	]
}
```

**Sucesso:**
```
{
    "sucesso": true,
    "mensagem": "Dados recebidos com sucesso. Vamos enviar à Claro e assim que houver retorno nós enviaremos.",
    "validadeToken": "Token expira em : 50 minutos e 53 segundos."
}
```

**Request do WebHook para sua API:**
```
{
	"TotalDeRegistros": 7,
	"ValorTotalRegistros": 0,
	"CodigoConvenio": "EMPRESSA_A",
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
