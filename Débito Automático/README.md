# NovosParceiros

Seja bem vindo ao guia de integrações Multipagos para Débito Automático Claro.

A documentação abaixo se refere um recorte da documentação oficial do **FEBRABAN versão 4** referente aos tipos de registros que são utilizados para transição de informações com o Grupo Claro.

Qualquer dúvida que possa ter, fique à vontade de enviar um e-mail para valniria.bandeira@multicomnet.com.br que iremos te ajudar.


# Registros aceitos em arquivo Retorno


| Registro | Função | Tratamento |
|--|--|--|
| A | Header | Obrigatório |
| B | Cadastramento de Débito Automático | Obrigatório  |
| F | Retorno do Débito Automático | Obrigatório  |
| J | Confirmação de Processamento de Arquivos  | Opcional |
| Z | Trailler | Obrigatório   |

#CARACTERÍSTICAS DO ARQUIVO
O arquivo com as informações da sistemática de débito automático, deverá obedecer ao **Padrão FEBRABAN de Débito Automático**, conforme Circular Oficial. Este manual utiliza a **Versão 04**, deste padrão.
O arquivo deverá obedecer às seguintes características :
- Meio: Internet,
- Organização : Sequencial,
- Tamanho do registro : 150 bytes,
- Blocagem : 20 registros por bloco - 3000 bytes,
- Label : no Label (sem Tape Mark no início e obrigatório no final do arquivo),
- Codificação : será utilizado o conjunto de caracteres “EBCDIC”

# NORMALIZAÇÃO DOS CAMPOS
- Numéricos 
Alinhados à Direita, com zeros a esquerda.
Os campos não utilizados deverão conter zeros.
- Alfanuméricos 
Alinhados à Esquerda, com “brancos” a direita.
Se a informação utilizada for menor que o tamanho físico do campo, este deverá ser preenchido com “brancos” a partir da primeira posição não utilizada.
Os campos não utilizados deverão conter “brancos”.

# ESTRUTURA DO ARQUIVO
O Arquivo deverá sempre obedecer a seguinte estrutura :
- Header : primeiro registro físico do arquivo - Registro “A”,
- Detalhe : registros subseqüentes, podendo ser (dependendo da origem) - Registros “B”, “F”, “J”, e
- Trailler : último registro físico do arquivo - Registro “Z”.

# PRAZO DE RETENÇÃO
Reenviar arquivo em até 24 horas.

# LAYOUT DOS REGISTROS

- Registro “A” - Header


|CAMPO| POSIÇÃO DE - ATÉ | FORMATO | CONTEÚDO |
|--|--|--|--|
| A01-Código do Registro | 001 - 001 |X(001) | A |
| A02-Código de Remessa | 002 - 002 | 9(001)  | 1 = Remessa - Enviado pela Empresa para o Banco 2 = Retorno - Enviado pelo Banco para a Empresa ​|
| A03-Código do Convênio | 003 - 022 | X(020)  |  Código atribuído pelo Banco, para seu controle interno. Este código será informado à Empresa, pelo Banco, antes da implantação do serviço de débito automático.|
| A04-Nome da Empresa | 023 - 042 | X(020)  |  Nome da Empresa.|
| A05-Código do Banco |043 - 045  | 9(003) |  Código do Banco na Câmara de compensação|
| A06-Nome do Banco  | 046 - 065 | X(020) |  Nome do Banco.|
| A07-Data de Geração | 066 - 073 | 9(008)  |  Data de geração do arquivo (AAAAMMDD).|
| A08-Número SequenciadoArquivo (NSA) |074 - 079|9(006) |Este número deverá evoluir de 1 em 1, para cada arquivo gerado, e terá uma ssequênciapara o Banco e outra para a Empresa. OBS.: O NSA deverá ser rigorosamente observado, pois arquivos que não estiverem na sequência serão rejeitados implicando no não processamento dos mesmos.
|A09-Versão do Layout  | 080 - 081  | 9(002)  |04|
| A10-Identificação do Serviço | 082 - 098 | X(017) |  DÉBITOAUTOMÁTICO|
| A11-Reservado para o futuro | 099 - 150 | X(052)  | Brancos|



- Registro “B” - Cadastramento de Débito Automático

| CAMPO | POSIÇÃO DE - ATÉ | FORMATO | CONTEÚDO |
|--|--|--|--|
| B01-Código do Registro | 001 - 001 | X(001) | B |
| B02-Identificação do Cliente na Empresa | 002 - 026 | X(025) |  Esta identificação deverá : Conter um processo de validação (DV), Ser única para cada cliente da empresa, Ser a mesma em todos os débitos consecutivos de um mesmo cliente. Esta informação será validada pelo Banco, conforme regra definida pela empresa, no momento do cadastramento.|
| B03-Agência para Débito  | 027 - 030 | X(004)  | Identificação da Agência no Banco onde será efetuado o Débito Automático |
| B04-Identificação do Cliente no Banco | 031 - 044  | X(014)  | Identificação utilizada pelo Banco para efetuar o débito |
| B05-Data da Opção/Exclusão | 045 - 052  | 9(008)  | Conterá : Data de Exclusão, se Código de Movimento igual a 1, Data de Inclusão, se Código de Movimento igual a 2. Formato AAAAMMDD|
| B06-Reservado para o futuro | 053 - 149 | X(097) | Brancos |
| B07-Código do Movimento  | 150 - 150  | 9(001) | 1 = Exclusão de optante pelo débito automático, 2 = Inclusão de optante pelo débito automático |


- Registro “F” - Retorno do Débito Automático 

| CAMPO | POSIÇÃO DE - ATÉ | FORMATO | CONTEÚDO |
|--|--|--|--|
| F01-Código do Registro | 001 - 001 | X(001) | F |
| F02-Identificação do Cliente na Empresa | 002 - 026  | X(025) | O conteúdo deverá ser idêntico ao anteriormente enviado pelo Banco, no registro tipo “B” |
| F03-Agência para Débito  | 027 - 030 | X(004)  | O conteúdo deverá ser idêntico ao anteriormente enviado pelo Banco, no registro tipo “B” |
| F04-Identificação do Cliente no Banco | 031 - 044 | X(014) | O conteúdo deverá ser idêntico ao anteriormente enviado pelo Banco, no registro tipo “B” |
| F05-Data do Vencimento / Débito| 045 - 052  | 9(008) | Conterá : Data do Vencimento, se o Código de Retorno, for diferente de “00” (não debitado); Data real do Débito, se o Código de retorno for igual a “00” (debitado). Formato AAAAMMDD. |
| F06-Valor Original /Debitado | 053 - 067 | 9(015) | Conterá : Valor Original enviado, se o Código de Retorno, for diferente de “00” (não debitado); Valor efetivamente Debitado, se o Código de Retorno for igual a “00” (debitado) |
| F07-Código de Retorno | 068 - 069  | X(002) | Orientações Abaixo |
| F08-Uso da Empresa | 070 - 139 | X(070) | O conteúdo deverá ser idêntico ao anteriormente enviado pela Empresa, no registro tipo “E” |
| F09-Reservado para o futuro  | 140 - 149 | X(010) | Brancos |
| F10-Código do Movimento  | 150 - 150 | 9(001) | O conteúdo será ser idêntico ao anteriormente enviado pela Empresa, no registro tipo “E” |


**F07-Código de Retorno**
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

- Registro “J” - Confirmação de Processamento de Arquivos 

| CAMPO | POSIÇÃO DE - ATÉ | FORMATO | CONTEÚDO |
|--|--|--|--|
| J01-Código do Registro | 001 - 001 | X(001)  | J |
| J02-Número Seqüêncial do Arquivo (NSA) processado | 002 - 007  | 9(006) | O conteúdo deverá ser idêntico ao campo A08 (registro “A”), do arquivo processado |
| J03-Data da Geração do Arquivo |  008 - 015| 9(008)  | O conteúdo deverá ser idêntico ao campo A07 (registro “A”), do arquivo processado. Formato AAAAMMDD |
| J04-Total de registros do Arquivo processado |  016 - 021 | 9(006) | O conteúdo deverá ser idêntico ao campo Z02 (registro “Z”), do arquivo processado. |
| J05-Valor Total do Arquivo processado |  022 - 038| 9(017)  | O conteúdo deverá ser idêntico ao campo Z03 (registro “Z”), do arquivo processado. |
|  J06-Data de Processamento do arquivo| 039 - 046 | 9(008)  | Data de processamento do arquivo pelo Banco ou pela Empresa. Formato AAAAMMDD. |
| J07-Reservado para o futuro  | 047 - 150  | X(104) | Brancos |

- Registro “Z” - Trailler

| CAMPO | POSIÇÃO DE - ATÉ | FORMATO | CONTEÚDO |
|--|--|--|--|
| Z01-Código do Registro | 001 - 001  | X(001)  | Z |
| Z02-Total de registros do arquivo |  002 - 007 | 9(006)  | No somatório dos registros, deverão ser também incluídos, os registros Header e Trailler. |
| Z03-Valor total dos registros do arquivo | 008 - 024  | 9(017)  | Este campo deverá ser o somatório do campo E06 (quando for o arquivo remessa - Empresa), ou do campo F06 (quando for arquivo retorno - Banco), independente do tratamento de casas decimais (ou código de moeda). |
| Z04-Reservado para o futuro | 025 - 150 | X(126)  | Brancos |

