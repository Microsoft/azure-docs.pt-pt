---
title: Mensagens EDIFACT para integração B2B
description: Trocar mensagens EDIFACT em formato EDI para integração empresarial B2B em Aplicações Lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 8aed4f44b597235557a495a263c6a4ddf8e93ce7
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106326"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens EDIFACT para integração empresarial B2B em Aplicações Lógicas Azure com Pacote de Integração Empresarial

Antes de poder trocar mensagens EDIFACT por Apps Lógicas Azure, tem de criar um acordo EDIFACT e armazenar esse acordo na sua conta de integração. Aqui estão os passos para como criar um acordo EDIFACT.

> [!NOTE]
> Esta página cobre as funcionalidades EDIFACT para Aplicações Lógicas Azure. Para mais informações, consulte [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens que precisa:

* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua subscrição Azure  
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos na sua conta de integração

> [!NOTE]
> Quando cria um acordo, o conteúdo nas mensagens que recebe ou envia de e para o parceiro deve corresponder ao tipo de acordo.

Depois de criar uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [adicionar parceiros,](logic-apps-enterprise-integration-partners.md)pode criar um acordo EDIFACT seguindo estes passos.

## <a name="create-an-edifact-agreement"></a>Criar um acordo EDIFACT 

1. Inicie sessão no [portal do Azure](https://portal.azure.com "Portal do Azure"). 

2. No menu Azure principal, selecione **Todos os serviços.** Na caixa de pesquisa, insira "integração", e, em seguida, selecione **contas integrais.**

   ![Encontre a sua conta de integração](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Se **todos os serviços** não aparecerem, poderá ter de expandir o menu primeiro. No topo do menu colapsado, selecione **etiquetas**de texto .

3. Em **Contas integrativas,** selecione a conta de integração onde pretende criar o acordo.

   ![Selecione conta de integração onde criar o acordo](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Escolha **Acordos**. Se não tiver um azulejo de acordos, adicione primeiro o azulejo.   

   ![Escolha azulejos "Acordos"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na página de Acordos, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Em **Adicionar,** insira um **nome** para o seu acordo. Para **o tipo de Acordo,** selecione **EDIFACT**. Selecione o **Parceiro anfitrião,** identidade de **anfitrião,** **parceiro convidado**e identidade de **hóspedes** para o seu acordo.

   ![Fornecer detalhes do acordo](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Propriedade | Descrição |
   | --- | --- |
   | Nome |Nome do acordo |
   | Tipo de acordo | Deve ser EDIFACT |
   | Parceiro anfitrião |Um acordo precisa tanto de um anfitrião como de um parceiro convidado. O parceiro anfitrião representa a organização que configura o acordo. |
   | Identidade de acolhimento |Um identificador para o parceiro anfitrião |
   | Parceiro Convidado |Um acordo precisa tanto de um anfitrião como de um parceiro convidado. O parceiro convidado representa a organização que está a fazer negócios com o parceiro anfitrião. |
   | Identidade de Hóspede |Um identificador para o parceiro convidado |
   | Receber Definições |Estas propriedades aplicam-se a todas as mensagens recebidas por um acordo. |
   | Enviar Definições |Estas propriedades aplicam-se a todas as mensagens enviadas por um acordo. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configure como o seu acordo lida com mensagens recebidas

Agora que definiu as propriedades do contrato, pode configurar como este acordo identifica e lida com as mensagens recebidas do seu parceiro através deste acordo.

> [!IMPORTANT]
> O conector EDIFACT suporta apenas caracteres UTF-8.
> Se a sua saída contiver caracteres inesperados, verifique se as suas mensagens EDIFACT utilizam o conjunto de caracteres UTF-8.

1. Em **'Adicionar',** **selecione 'Receber Definições**' .
Configure estas propriedades com base no seu acordo com o parceiro que troca mensagens consigo. Para descrições de propriedades, consulte as tabelas nesta secção.

   **As Definições de Receção** estão organizadas nestas secções: Identificadores, Reconhecimento, Schemas, Números de Controlo, Validação e Definições Internas.

   ![Configurar "Receber Definições"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Depois de terminar, certifique-se de guardar as suas definições escolhendo **OK**.

Agora o seu acordo está pronto para lidar com mensagens de entrada que estejam em conformidade com as definições selecionadas.

### <a name="identifiers"></a>Identificadores

| Propriedade | Descrição |
| --- | --- |
| UNB6.1 (Palavra-passe de referência do destinatário) |Introduza um valor alfanumérico que varia entre 1 e 14 caracteres. |
| UNB6.2 (Qualificação de Referência do Destinatário) |Introduza um valor alfanumérico com um mínimo de um caracteres e um máximo de dois caracteres. |

### <a name="acknowledgments"></a>Agradecimentos

| Propriedade | Descrição |
| --- | --- |
| Receção da Mensagem (CONTRL) |Selecione esta caixa de verificação para devolver um reconhecimento técnico (CONTRL) ao remetente de intercâmbio. O reconhecimento é enviado para o remetente de intercâmbio com base nas Definições de Envio para o acordo. |
| Reconhecimento (CONTRL) |Selecione esta caixa de verificação para devolver um reconhecimento funcional (CONTRL) ao remetente de intercâmbio O reconhecimento é enviado ao remetente de intercâmbio com base nas Definições de Envio do acordo. |

### <a name="schemas"></a>Esquemas

| Propriedade | Descrição |
| --- | --- |
| UNH2.1 (TIPO) |Selecione um tipo de conjunto de transações. |
| UNH2.2 (VERSÃO) |Introduza o número da versão da mensagem. (Mínimo, um personagem; máximo, três caracteres). |
| UNH2.3 (LIBERTAÇÃO) |Introduza o número de lançamento da mensagem. (Mínimo, um personagem; máximo, três caracteres). |
| UNH2.5 (CÓDIGO DESIGNADO ASSOCIADO) |Introduza o código atribuído. (Máximo, seis caracteres. Deve ser alfanumérico). |
| UNG2.1 (ID DO REMETENTE DA APP) |Introduza um valor alfanumérico com um mínimo de um caracteres e um máximo de 35 caracteres. |
| UNG2.2 (APP SENDER CODE QUALIFIER) |Introduza um valor alfanumérico, com um máximo de quatro caracteres. |
| SCHEMA |Selecione o esquema previamente carregado que pretende utilizar na sua conta de integração associada. |

### <a name="control-numbers"></a>Números de controlo

| Propriedade | Descrição |
| --- | --- |
| Duplicado do Número de Controlo de Intercâmbio deSallow |Para bloquear trocas duplicadas, selecione esta propriedade. Se selecionado, o EDIFACT Decode Action verifica que o número de controlo de intercâmbio (UNB5) para o intercâmbio recebido não corresponde a um número de controlo de intercâmbio previamente processado. Se for detetada uma correspondência, o intercâmbio não é processado. |
| Verifique se o UNB5 duplicado todos os dias (dias) |Se optar por proibir os números de controlo de troca duplicados, pode especificar o número de dias em que efetuar a verificação, dando o valor adequado para esta definição. |
| Duplicados de número de controlo de grupo desabotoados |Para bloquear trocas com números de controlo de grupo duplicados (UNG5), selecione esta propriedade. |
| Duplicados de número de controlo de conjunto de transações desadmissidos |Para bloquear trocas com números de controlo de conjuntos de transações duplicadas (UNH1), selecione esta propriedade. |
| Número de controlo de reconhecimento edifact |Para designar os números de referência definidos para a transação num reconhecimento, insira um valor para o prefixo, uma gama de números de referência e um sufixo. |

### <a name="validation"></a>Validação

Quando completa cada linha de validação, outra é adicionada automaticamente. Se não especificar quaisquer regras, a validação utiliza a linha "Padrão".

| Propriedade | Descrição |
| --- | --- |
| Tipo de mensagem |Selecione o tipo de mensagem EDI. |
| Validação EDI |Execute a validação EDI em tipos de dados definidos pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores de rasto. |
| Validação Alargada |Se o tipo de dados não for EDI, a validação está no requisito do elemento de dados e permitiu a repetição, enumerações e validação do comprimento do elemento de dados (min/max). |
| Permitir zeros de ponta/rasto |Mantenha quaisquer caracteres adicionais de liderança ou de fuga zero e espaciais. Não remova estes caracteres. |
| Guarnição de pontos de liderança/trailing |Remova os caracteres de liderança ou de rasto zero e espaciais. |
| Política de Separador de Trailing |Gerar separadores de rastos. <p>Selecione **Não É Permitido** proibir delimitar e separadores no intercâmbio recebido. Se o intercâmbio tiver delimitadores e separadores, o intercâmbio é declarado inválido. <p>Selecione **Opcional** para aceitar intercâmbios com ou sem delimitadores e separadores. <p>**Selecione Obrigatório** quando o intercâmbio recebido deve ter delimitadores e separadores de rasto. |

### <a name="internal-settings"></a>Configurações internas

| Propriedade | Descrição |
| --- | --- |
| Crie etiquetas XML vazias se forem permitidos separadores de rasto |Selecione esta caixa de verificação para que o remetente de intercâmbio inclua etiquetas XML vazias para separadores de rasto. |
| Split Interchange como conjuntos de transações - suspender conjuntos de transações por erro|Analisa cada transação definida num intercâmbio num documento XML separado aplicando o envelope apropriado ao conjunto de transações. Suspender apenas os conjuntos de transações que falham na validação. |
| Intercâmbio dividido como conjuntos de transações - suspender o intercâmbio por erro|Analisa cada transação definida num intercâmbio num documento XML separado aplicando o envelope apropriado. Suspender todo o intercâmbio quando um ou mais conjuntos de transações na validação de falhas de troca. | 
| Preserve Interchange - suspender conjuntos de transações por erro |Deixa o intercâmbio intacto, cria um documento XML para todo o intercâmbio em lotado. Suspender apenas os conjuntos de transações que falham na validação, enquanto continua a processar todos os outros conjuntos de transações. |
| Preserve Interchange - suspender o intercâmbio por erro |Deixa o intercâmbio intacto, cria um documento XML para todo o intercâmbio em lotado. Suspender todo o intercâmbio quando um ou mais conjuntos de transações na validação de falhas de troca. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configure como o seu acordo envia mensagens

Pode configurar como este acordo identifica e lida com mensagens de saída que envia aos seus parceiros através deste acordo.

1.  Em **adicionar,** selecione **'Enviar Definições**' .
Configure estas propriedades com base no seu acordo com o seu parceiro que troca mensagens consigo. Para descrições de propriedades, consulte as tabelas nesta secção.

    **As Definições de Envio** estão organizadas nestas secções: Identificadores, Reconhecimento, Schemas, Envelopes, Conjuntos de Caracteres e Separadores, Números de Controlo e Validações.

    ![Configurar "Enviar Definições"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Depois de terminar, certifique-se de guardar as suas definições escolhendo **OK**.

Agora o seu acordo está pronto para lidar com mensagens de saída que estejam em conformidade com as definições selecionadas.

### <a name="identifiers"></a>Identificadores

| Propriedade | Descrição |
| --- | --- |
| UNB1.2 (versão Sintax) |Selecione um valor entre **1** e **4**. |
| UNB2.3 (Endereço de encaminhamento inverso do remetente) |Introduza um valor alfanumérico com um mínimo de um caracteres e um máximo de 14 caracteres. |
| UNB3.3 (Destinatário Endereço de encaminhamento inverso) |Introduza um valor alfanumérico com um mínimo de um caracteres e um máximo de 14 caracteres. |
| UNB6.1 (Palavra-passe de referência do destinatário) |Introduza um valor alfanumérico com um mínimo de um e um máximo de 14 caracteres. |
| UNB6.2 (Qualificação de Referência do Destinatário) |Introduza um valor alfanumérico com um mínimo de um caracteres e um máximo de dois caracteres. |
| UNB7 (ID de referência de aplicação) |Introduza um valor alfanumérico com um mínimo de um personagem e um máximo de 14 caracteres |

### <a name="acknowledgment"></a>Reconhecimento

| Propriedade | Descrição |
| --- | --- |
| Receção da Mensagem (CONTRL) |Selecione esta caixa de verificação se o parceiro hospedado espera receber um reconhecimento técnico (CONTRL). Esta definição especifica que o parceiro hospedado, que está a enviar a mensagem, solicita um reconhecimento do parceiro convidado. |
| Reconhecimento (CONTRL) |Selecione esta caixa de verificação se o parceiro hospedado espera receber um reconhecimento funcional (CONTRL). Esta definição especifica que o parceiro hospedado, que está a enviar a mensagem, solicita um reconhecimento do parceiro convidado. |
| Gerar loop SG1/SG4 para conjuntos de transações aceites |Se optou por solicitar um reconhecimento funcional, selecione esta caixa de verificação para forçar a geração de loops SG1/SG4 em reconhecimentos contrl funcionais para conjuntos de transações aceites. |

### <a name="schemas"></a>Esquemas

| Propriedade | Descrição |
| --- | --- |
| UNH2.1 (TIPO) |Selecione um tipo de conjunto de transações. |
| UNH2.2 (VERSÃO) |Introduza o número da versão da mensagem. |
| UNH2.3 (LIBERTAÇÃO) |Introduza o número de lançamento da mensagem. |
| SCHEMA |Selecione o esquema para usar. Os schemas estão localizados na sua conta de integração. Para aceder aos seus esquemas, ligue primeiro a sua conta de integração à sua aplicação Logic. |

### <a name="envelopes"></a>Envelopes

| Propriedade | Descrição |
| --- | --- |
| UNB8 (Código Prioritário de Processamento) |Introduza um valor alfabético que não tem mais de um personagem de comprimento. |
| UNB10 (Acordo de Comunicação) |Introduza um valor alfanumérico com um mínimo de um caracteres e um máximo de 40 caracteres. |
| UNB11 (Indicador de ensaio) |Selecione esta caixa de verificação para indicar que o intercâmbio gerado são dados de teste |
| Aplicar o Segmento UNA (Conselhos de Cordas de Serviço) |Selecione esta caixa de verificação para gerar um segmento UNA para o intercâmbio a enviar. |
| Aplicar segmentos de UNG (Cabeçalho do Grupo de Funções) |Selecione esta caixa de verificação para criar segmentos de agrupamento no cabeçalho do grupo funcional nas mensagens enviadas ao parceiro convidado. Os seguintes valores são utilizados para criar os segmentos DEUNG: <p>Para **UNG1,** introduza um valor alfanumérico com um mínimo de um caracteres e um máximo de seis caracteres. <p>Para **UNG2.1,** introduza um valor alfanumérico com um mínimo de um caracteres e um máximo de 35 caracteres. <p>Para **UNG2.2,** introduza um valor alfanumérico, com um máximo de quatro caracteres. <p>Para **UNG3.1,** introduza um valor alfanumérico com um mínimo de um caracteres e um máximo de 35 caracteres. <p>Para **UNG3.2,** introduza um valor alfanumérico, com um máximo de quatro caracteres. <p>Para **UNG6,** introduza um valor alfanumérico com um mínimo de um e um máximo de três caracteres. <p>Para **UNG7.1,** introduza um valor alfanumérico com um mínimo de um caracteres e um máximo de três caracteres. <p>Para **UNG7.2,** introduza um valor alfanumérico com um mínimo de um caracteres e um máximo de três caracteres. <p>Para **UNG7.3,** introduza um valor alfanumérico com um mínimo de 1 caracteres e um máximo de 6 caracteres. <p>Para **UNG8,** introduza um valor alfanumérico com um mínimo de um caracteres e um máximo de 14 caracteres. |

### <a name="character-sets-and-separators"></a>Conjuntos de caracteres e separadores

Além do conjunto de caracteres, pode introduzir um conjunto diferente de delimitadores a serem utilizados para cada tipo de mensagem. Se um conjunto de caracteres não for especificado para um determinado esquema de mensagem, então o conjunto de caracteres padrão é usado.

| Propriedade | Descrição |
| --- | --- |
| UNB1.1 (Identificador de Sistema) |Selecione o conjunto de caracteres EDIFACT a aplicar no intercâmbio de saída. |
| Esquema |Selecione um esquema da lista de lançamentos. Depois de completar cada linha, uma nova linha é adicionada automaticamente. Para o esquema selecionado, selecione o conjunto de separadores que pretende utilizar, com base nas descrições do separador abaixo. |
| Tipo de Entrada |Selecione um tipo de entrada na lista de drop-down. |
| Separador de componentes |Para separar elementos de dados compósitos, introduza um único carácter. |
| Separador de elementos de dados |Para separar elementos de dados simples dentro de elementos de dados compósitos, introduza um único caracteres. |
| Exterminador de Segmento |Para indicar o fim de um segmento EDI, insira um único personagem. |
| Sufixo |Selecione o caracteres utilizados com o identificador de segmento. Se designar um sufixo, então o elemento de dados do exterminador do segmento pode estar vazio. Se o exterminador do segmento ficar vazio, então deve designar um sufixo. |

### <a name="control-numbers"></a>Números de controlo

| Propriedade | Descrição |
| --- | --- |
| UNB5 (Número de Controlo de Intercâmbio) |Introduza um prefixo, uma gama de valores para o número de controlo de permuta e um sufixo. Estes valores são usados para gerar um intercâmbio de saída. O prefixo e o sufixo são opcionais, enquanto o número de controlo é necessário. O número de controlo é incrementado para cada nova mensagem; o prefixo e o sufixo permanecem os mesmos. |
| UNG5 (Número de Controlo de Grupo) |Introduza um prefixo, uma gama de valores para o número de controlo de permuta e um sufixo. Estes valores são usados para gerar o número de controlo do grupo. O prefixo e o sufixo são opcionais, enquanto o número de controlo é necessário. O número de controlo é incrementado para cada nova mensagem até que o valor máximo seja atingido; o prefixo e o sufixo permanecem os mesmos. |
| UNH1 (Número de referência do cabeçalho da mensagem) |Introduza um prefixo, uma gama de valores para o número de controlo de permuta e um sufixo. Estes valores são utilizados para gerar o número de referência do cabeçalho da mensagem. O prefixo e o sufixo são opcionais, enquanto o número de referência é necessário. O número de referência é incrementado para cada nova mensagem; o prefixo e o sufixo permanecem os mesmos. |

### <a name="validation"></a>Validação

Quando completa cada linha de validação, outra é adicionada automaticamente. Se não especificar quaisquer regras, a validação utiliza a linha "Padrão".

| Propriedade | Descrição |
| --- | --- |
| Tipo de mensagem |Selecione o tipo de mensagem EDI. |
| Validação EDI |Executar validação EDI em tipos de dados definidos pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores de rasto. |
| Validação Alargada |Se o tipo de dados não for EDI, a validação está no requisito do elemento de dados e permitiu a repetição, enumerações e validação do comprimento do elemento de dados (min/max). |
| Permitir zeros de ponta/rasto |Mantenha quaisquer caracteres adicionais de liderança ou de fuga zero e espaciais. Não remova estes caracteres. |
| Guarnição de pontos de liderança/trailing |Remova os caracteres de liderança ou de rasto zero. |
| Política de Separador de Trailing |Gerar separadores de rastos. <p>Selecione **Não É Permitido** proibir delimitar e separadores no intercâmbio enviado. Se o intercâmbio tiver delimitadores e separadores, o intercâmbio é declarado inválido. <p>Selecione **Opcional** para enviar intercâmbios com ou sem delimitadores e separadores. <p>**Selecione Obrigatório** se o intercâmbio enviado deve ter delimitadores e separadores de rasto. |

## <a name="find-your-created-agreement"></a>Encontre o seu acordo criado

1.  Depois de terminar de definir todas as propriedades do seu contrato, na página **Adicionar,** escolha **OK** para terminar de criar o seu acordo e volte à sua conta de integração.

    O seu novo acordo aparece agora na sua lista de **Acordos.**

2.  Pode também ver os seus acordos na sua conta de integração. No menu da sua conta de integração, escolha **visão geral**e, em seguida, selecione o azulejo **dos Acordos.** 

    ![Escolha azulejos "Acordos"](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/edifact/).

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão do conector com o rótulo ISE utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)