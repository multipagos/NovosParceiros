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

- Registro “F” - Retorno do Débito Automático 

- Registro “J” - Confirmação de Processamento de Arquivos 

- Registro “Z” - Trailler