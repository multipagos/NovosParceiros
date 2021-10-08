# Novos Parceiros

Seja bem vindo ao guia de orientação à integrações de Arrecadações com Multipagos!

Através da documentação abaixo você será capaz de utilizar os serviços Multipagos que foram construídos para facilitar a consulta e a transição de informações entre suas arrecações e o grupo Claro.

Qualquer dúvida que possa ter, fique à vontade de enviar um e-mail para team-mpagos@multipagos.com.br que iremos te ajudar.


#Pagamento de Arrecadações por Etapas
Disponibilizamos 3 APIs para a realização do processo de pagamento com sucesso.
Cada API tem sua função que envolve validação de informações e por fim, a efetivação do pagamento da fatura.
Ao optar integrar por esse processo, saiba que a arrecadação só será recebida e aprovada após a execução do processo completo e na ordem correta.
- 1 - API SolicitarAutorizacao
- 2 - API IniciarPagamento
- 3 - API EfetivarPagamento


# SandBox
Disponibilizamos um ambiente SandBox para que você possa realizar os testes. Fique à vontade para usar!
- Url SandBox: https://sandbox.multipagos.com.br


# Solicita Autorização
Processo de solicitar a autorização de um documento tem como objetivo a validação do código de barras para garantir que é um código aceito pela plataformae que não haja duplicidade no pagamento. É o primeiro passo da efetivação.
- POST /api/Pagamento/SolicitarAutorizacao
```html
{
	"CodigoInformado": "84600000000036200710102274806370098002119689",
	"Origem": 0,
	"Valor": 0,
	"PosId": "6000",
	"TerminalId": "48B20B9C-69B9-443E-9090-A5CE730DFA76",
	"NumSequencial": 0
}
```
**Pdv e Terminal serão fornecidos.**
**Orientação dos campos:**
- CodigoInformado: Código de Barras da fatura - Alfanumérico(44)
- Origem:  
- Valor: Valor efetivamente recebido - Decimal(10) + V(99)
- PosId - Campo PDV enviado
- TerminalId - Campo Terminal enviado
- NumSequencial - Seu contador de utilização


# Iniciar Pagamento
Após a realização com sucesso do SolicitaAutorizacao, o próximo passo é indicar a forma de pagamento. 

- POST /api/Pagamento/IniciarPagamento
```html
{
	"FormaPagamento": 0,
	"DocumentoId": "00000000-0000-0000-0000-000000000000",
	"PosId": "6000",
	"TerminalId": "48B20B9C-69B9-443E-9090-A5CE730DFA76",
	"NumSequencial": 0
 }
```
**Orientação dos campos:**
- FormaPagamento: Forma de pagamento - Inteiro(1) podendo ser:
  - 1 – Dinheiro
  - 2 – Cheque
  - 3 – Não identificado/outras formas
  - 4 – Cartão de crédito
  - 5 – Cartão/Multibanco
  - 6 – Débito em conta
- DocumentoId: Informação recebida na etapa SolicitaAutorizacao 
- PosId - Campo PDV enviado
- TerminalId - Campo Terminal enviado
- NumSequencial - Seu contador de utilização


# Efetivar Pagamento
EfetivarPagamento é a ultima etapa do processo de pagamento e tem como objetivo informar os detalhes do pagamento. Ao ser executado com sucesso, sua fatura estará com o registro devidamente salvo e pronto para ser enviado para o grupo Claro. 

- POST /api/Pagamento/EfetivarPagamento
```html
{
	"DadosPagamento": {
		"valor": 0,
		"moeda": 0,
		"rede": "string",
		"tipoTransacao": 0,
		"numeroTransacaoTEF": "string",
		"codigoAutorizacao": "string",
		"timeStampHost": "string",
		"timeStampClient": "string",
		"parcelamentoAdministradora": true,
		"parcelas": 0,
		"dataHoraTransacao": "2021-10-08T19:45:06.453Z",
		"finalizacao": "string",
		"comprovanteCompleto": "string",
		"comprovanteLoja": "string",
		"comprovanteCliente": "string",
		"comprovanteReduzido": "string",
		"administradora": "string",
		"resposta": "string",
		"nsuAdquirente": "string"
	},
	"DocumentoId": "00000000-0000-0000-0000-000000000000",
	"PosId": "6000",
	"TerminalId": "48B20B9C-69B9-443E-9090-A5CE730DFA76",
	"NumSequencial": 0
 }
```
**Orientação dos campos:**
- DadosPagamento
  - valor - Valor efetivamente recebido - Decimal(10) + V(99)
  - moeda - Moeda utilizada no pagamento - Inteiro(1) podendo ser:
    - 0 - Não Definido
	- 1 - Real
	- 2 - Dolar
	- 3 - Euro
  - rede - Adquirente utilizada - String(20)
  - tipoTransacao - Tipo de Transação realizada - Inteiro(2) podendo ser:
	- 0 - Não realizado
	- 10 - Cartão de Crédito a Vista
	- 11 - Parcelamento realizado pelo estabelecimento
	- 12 - Parcelamento realizado pela administradora do cartão
	- 20 - Cartão de Débito a vista
	- 30 - Outros cartões
	- 35 - Cartão Digitado - Débito a vista
	- 36 - Cartão Digitado - Crédito a vista
	- 99 - Outras formas
  - numeroTransacaoTEF - numero da transação do TEF - String
  - codigoAutorizacao - código de autorização do cartão - String
  - timeStampHost - TimeStamp do host - String
  - timeStampClient - TimeStamp do client - String
  - parcelamentoAdministradora - parcelamento da administradora do cartão - Boolean
  - parcelas - quantidade de parcelas do pagameto - Inteiro(2)
  - dataHoraTransacao - Data e Hora da transação - DateTime formato yyyy-MM-dd HH-mm-ss
  - finalizacao - finalização do pagamento - String
  - comprovanteCompleto - Dados do comprovante de pagamento - String
  - comprovanteLoja - Dados do comprovante de pagamento - String
  - comprovanteCliente - Dados do comprovante de pagamento - String
  - comprovanteReduzido - Dados do comprovante de pagamento - String
  - administradora - Administradora do cartão - String
  - resposta - Resposa do Pagamento - String
  - nsuAdquirente - NSU Adquirente - String
- DocumentoId: Informação recebida na etapa SolicitaAutorizacao 
- PosId - Campo PDV enviado
- TerminalId - Campo Terminal enviado
- NumSequencial - Seu contador de utilização

# Cancelar Pagamento
Em caso de desistência do pagamento, ou até algum problema na finalização do seu processo, você pode cancelar o registro e isso irá garantir que sua fatura não será concluída.
**Atenção: Isso não pode ser realizado em fatura paga no dia anterior.**

- POST /api/Pagamento/CancelarPagamento

```html
{
	"Motivo": "string",
	"DocumentoId": "00000000-0000-0000-0000-000000000000",
	"PosId": "6000",
	"TerminalId": "48B20B9C-69B9-443E-9090-A5CE730DFA76",
	"NumSequencial": 0
 }
```
**Orientação dos campos:**
- Motivo - Detalhamento do motivo de cancelamento - String
- DocumentoId: Informação recebida na etapa SolicitaAutorizacao 
- PosId - Campo PDV enviado
- TerminalId - Campo Terminal enviado
- NumSequencial - Seu contador de utilização



