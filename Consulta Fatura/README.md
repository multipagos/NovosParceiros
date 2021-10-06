# NovosParceiros

Seja bem vindo ao guia de orientação à integrações de Arrecadações com Multipagos!

Através da documentação abaixo você será capaz de utilizar os serviços Multipagos que foram construídos para facilitar a consulta e a transição de informações entre suas arrecações e o grupo Claro.

Qualquer dúvida que possa ter, fique à vontade de enviar um e-mail para valniria.bandeira@multicomnet.com.br que iremos te ajudar.



# SandBox
Disponibilizamos um ambiente SandBox para que você possa realizar os testes. Fique à vontade para usar!
- Url SandBox: https://sandbox.multipagos.com.br



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
*Atenção - IdentificadorCliente inválido propositalmente.
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
        "IdentificacaoCliente": "telephoneNumber::12345678912",
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
        "IdentificacaoCliente": "12345678912",
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
        "IdentificacaoCliente": "12345678912",
        "SucessoConsulta": false,
        "DescricaoAuxiliar": "Não foi possivel realizar a chamada ao servidor Net/Claro Invoices\nDetalhes: The remote server returned an error: (400) Bad Request.",
        "Pendencias": null,
        "Contratos": null
    }
}
```

Retorno em caso de não identificar o PosId e TerminalId inseridos:
```html
null
```
