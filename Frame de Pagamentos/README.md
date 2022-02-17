# Novos Parceiros

Seja bem vindo ao guia de orientação à integrações de Arrecadações com Multipagos!

Através da documentação abaixo você será capaz de utilizar os serviços Multipagos que foram construídos para facilitar a consulta e a transição de informações entre suas arrecações e o grupo Claro.

Qualquer dúvida que possa ter, fique à vontade de enviar um e-mail para team-mpagos@multipagos.com.br que iremos te ajudar.


# SandBox - Disponível a partir de 28/02/2022
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

- POST /api/Pagamento/PagParceladoFrame

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
        "frameLink": "https://app-framespa-hml.azurewebsites.net/CardFormCustom?token=20220215BB4B51B73011467E9F41CCD76DB52D72"
    },
    "totalDeRegistros": 1,
    "mensagemErro": ""
}
```
**Campo Inválido**
```
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalDeRegistros": 0,
    "mensagemErro": "A campo (X) é inválido."
}
```
**Campo obrigatório não preenchido**
```
{
    "sucesso": false,
    "mensagem": "Não foi possível efetuar esta ação. Tente mais tarde.",
    "totalDeRegistros": 0,
    "mensagemErro": "A campo (X) é obrigatório e não foi preenchido."
}
