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
			"DataOperacao": "2024-02-04",
			"CodigoMovimento": "2"
		},
		{
			"CodigoRegistro": "F",
			"IdentificacaoClienteEmpresa": "00000000012",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001444",
			"UsoEmpresa": "",
			"DataDebito": "2024-02-04",
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
			"CodigoMovimento": "1"
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
Primeiramente, é necessário assinar o WebHook para receber notificações de Débito Automático.
Após autenticado com sucesso em nossa API de Token, realize a request abaixo.
- POST /api/v1/WebHook/AssinarWebHook
```
{
  "nome": "Banco X",
  "funcionalidadeId": 4,
  "url": "https://SuaUrlAqui/resource", 
  "tipoAutenticacao": 1,
  "objetoAutenticacao": "{'user': 'nossoUsuarioDeAutenticacao','password': 'nossaSenhaDeAutenticacao'}",
  "urlAutenticacao": "https://SuaUrlAqui.com.br/api/Login/Token",
  "campoToken": "token"  
}
```

**Orientação dos campos:**
- nome - Identificação do assinante. Nome da instituição parceira. Obrigatório.
- funcionalidadeId - Identificação da funcionalidade assinada. Favor informar o valor 4 que equivale ao produto Débito Automático por API. Obrigatório.
- url - Url que receberá as mensagem enviadas pelo WebHook. Sugerimos o uso de https://webhook.site/ para homologação. Obrigatório.
- tipoAutenticacao - Indicação de necessidade de autenticação ou não do campo 'url'. Obrigatório. Podendo ser:
	- 0 - Nenhuma
	- 1 - Bearer Token
	- 2 - Basic Auth
	- 3 - Bearer Cognito AWS
  	- 4 - Application Token (token não expira)
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


- urlAutenticacao - Url completa do endpoint de autenticação. Obrigatório se 'tipoAutenticacao' for diferente de 0. Exemplo: "https://sandbox.multipagos.com.br/api/Multipagos/Token"
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
```

- POST /api/v1/DebitoAutomatico/Retorno
```html
{
	"TotalDeRegistros": 13,
	"ValorTotalRegistros": 70.17,
	"CodigoConvenio": "CLAROMOVEL",
	"Nsa": 1,
	"Detalhes":
	[
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001008",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001123",
			"DataOperacao": "2024-02-04T12:01:02",
			"CodigoMovimento": "2"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001001",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001456",
			"DataOperacao": "2024-02-04T12:01:04",
			"CodigoMovimento": "2"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001002",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001789",
			"DataOperacao": "2024-02-04T12:01:06",
			"CodigoMovimento": "2"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001003",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001135",
			"DataOperacao": "2024-02-04T12:01:08",
			"CodigoMovimento": "2"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001004",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001246",
			"DataOperacao": "2024-02-04T12:02:00",
			"CodigoMovimento": "2"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001005",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001357",
			"DataOperacao": "2024-02-04T12:02:02",
			"CodigoMovimento": "2"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001006",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001468",
			"DataOperacao": "2024-02-04T12:02:04",
			"CodigoMovimento": "2"
		},
		{
			"CodigoRegistro": "B",
			"IdentificacaoClienteEmpresa": "00000001007",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001579",
			"DataOperacao": "2024-02-04T12:02:06",
			"CodigoMovimento": "2"
		},
		{
		      "codigoRegistro": "B",
		      "identificacaoClienteEmpresa": "131747605",
		      "agenciaDebito": "0001",
		      "identificacaoClienteBanco": "3773578",
		      "dataOperacao": "2024-01-17",
		      "codigoMovimento": "1",      
		},
		{
			"codigoRegistro": "F",
			"identificacaoClienteEmpresa": "151442819",
			"agenciaDebito": "0001",
			"identificacaoClienteBanco": "2264814",
			"codigoMovimento": "0",
			"valorDebito": 70.17,
			"usoEmpresa": "476159493",      
			"dataDebito": "2023-02-09T00:00:00",
			"statusRetorno": "00"	            
		},
		{      
		      "codigoRegistro": "F",
		      "identificacaoClienteEmpresa": "124430600",
		      "agenciaDebito": "0001",
		      "identificacaoClienteBanco": "82860",
		      "codigoMovimento": "0",
		      "valorDebito": 0.0,
		      "usoEmpresa": "476090623",      
		      "dataDebito": "2024-01-25T00:00:00",
		      "statusRetorno": "01"		
		},
		{
			"CodigoRegistro": "H",
			"IdentificacaoClienteEmpresa": "00000000010",
			"AgenciaDebito": "122",
			"IdentificacaoClienteBanco": "00000001555",
			"IdentificacaoClienteNovo": "00000001478",
			"Ocorrencia": "Descrição da rejeição",
			"CodigoMovimento": "1"		  
		},
		{
			"CodigoRegistro": "H",
			"IdentificacaoClienteEmpresa": "00000000010",
			"AgenciaDebito": "122",
			"IdentificacaoClienteBanco": "00000001555",
			"IdentificacaoClienteNovo": "00000001480",
			"Ocorrencia": "Descrição",
			"CodigoMovimento": "0"
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
			"DataVencimento": "2024-02-09",
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
			"DataVencimento": "2024-02-09",
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
			"DataVencimento": "2024-02-09",
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
			"DataVencimento": "2024-02-09",
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
			"DataVencimento": "2024-02-09",
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
			"CodigoMovimento": "1"
		},
		{
			"CodigoRegistro": "C",
			"IdentificacaoClienteEmpresa": "00000001008",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "00000001123",   
			"Ocorrencia": "Motivo ocorrência",
			"Ocorrencia2": "Motivo ocorrência 2",
			"CodigoMovimento": "1"
		},
		{
			"codigoRegistro": "E",
			"identificacaoClienteEmpresa": "151442819",
			"agenciaDebito": "0001",
			"identificacaoClienteBanco": "2264814",
			"codigoMovimento": "0",      
			"dataVencimento": "2024-01-27T00:00:00",
			"valorDebito": 70.17,
			"usoEmpresa": "476159493",     
		},
		{
			"codigoRegistro": "E",
			"identificacaoClienteEmpresa": "124430600",
			"agenciaDebito": "0001",
			"identificacaoClienteBanco": "82860",      
			"codigoMovimento": "0",            
			"dataVencimento": "2024-01-19T00:00:00",
			"valorDebito": 53.96,
			"usoEmpresa": "476090623"		      
		 },   
		 {
			"CodigoRegistro": "E",
			"IdentificacaoClienteEmpresa": "131747605",
			"AgenciaDebito": "001",
			"IdentificacaoClienteBanco": "3773578",   
			"DataVencimento": "2024-01-19",
			"ValorDebito": 30.50,			
			"UsoEmpresa": "476090624",
			"CodigoMovimento": "1"
		},
		{
			"codigoRegistro": "D",
			"identificacaoClienteEmpresa": "124252050",
			"agenciaDebito": "0001",
			"identificacaoClienteBanco": "79599",      
			"codigoMovimento": "1",
			"Ocorrencia": "Exclusao - transferido para debito em outro banco",
			"identificacaoClienteNovo": "124252050"
		},   
		{
			"codigoRegistro": "D",
			"identificacaoClienteEmpresa": "149399937",
			"agenciaDebito": "0001",
			"identificacaoClienteBanco": "86386",      
			"codigoMovimento": "1",
			"Ocorrencia": "Exclusao - transferido para debito em outro banco",
			"identificacaoClienteNovo": "149399937"
		},
		{
			"codigoRegistro": "C",
			"identificacaoClienteEmpresa": "2030765888",
			"agenciaDebito": "0001",
			"identificacaoClienteBanco": "86023",      
			"codigoMovimento": "2",
			"Ocorrencia": "CONTA CLE NAO ENCONTRADA NA BASE" 
		},
		{
			"codigoRegistro": "C",
			"identificacaoClienteEmpresa": "2030765888",
			"agenciaDebito": "0001",
			"identificacaoClienteBanco": "86023",      
			"codigoMovimento": "2",
			"Ocorrencia": "CONTA CLE NAO ENCONTRADA NA BASE"
		}
	]
}
```
**Agendamos uma rotina para realizar a RemessaFake para o WebHook sob demanda.**
