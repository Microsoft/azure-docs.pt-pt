---
title: Mensagens EDIFACT para integração B2B
description: Trocar mensagens EDIFACT em formato EDI para integração empresarial B2B em Azure Logic Apps com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b0df55e59bd519a816c4022f2434edfcd4460780
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006528"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens EDIFACT para a integração empresarial B2B no Azure Logic Apps com o Enterprise Integration Pack

Antes de poder trocar mensagens EDIFACT por Azure Logic Apps, tem de criar um acordo EDIFACT e armazenar esse acordo na sua conta de integração. Aqui estão os passos para como criar um acordo EDIFACT.

> [!NOTE]
> Esta página cobre as funcionalidades EDIFACT para Azure Logic Apps. Para mais informações, consulte [X12.](logic-apps-enterprise-integration-x12.md)

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens que precisa:

* Uma conta de [integração](logic-apps-enterprise-integration-create-integration-account.md) já definida e associada à sua subscrição do Azure  
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos na sua conta de integração

> [!NOTE]
> Quando cria um acordo, o conteúdo nas mensagens que recebe ou envia de e para o parceiro deve corresponder ao tipo de acordo.

Depois de [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e adicionar [parceiros,](logic-apps-enterprise-integration-partners.md)pode criar um acordo EDIFACT seguindo estes passos.

## <a name="create-an-edifact-agreement"></a>Criar um acordo EDIFACT 

1. Inicie sessão no [portal do Azure](https://portal.azure.com "Portal do Azure"). 

2. No menu Azure principal, selecione **Todos os serviços**. Na caixa de pesquisa, introduza "integração", e depois selecione **contas de Integração**.

   ![Encontre a sua conta de integração](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Se **todos os serviços** não aparecerem, poderá ter de expandir o menu primeiro. No topo do menu colapsado, selecione **Mostrar etiquetas de texto**.

3. Em **Contas de Integração,** selecione a conta de integração onde pretende criar o acordo.

   ![Selecione conta de integração onde criar o acordo](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Escolha **Os Acordos**. Se não tiver um azulejo de Acordos, adicione primeiro o azulejo.   

   ![Escolha azulejos "Acordos"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na página 'Acordos', escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Em **Adicionar,** insira um **Nome** para o seu acordo. Para **o tipo de acordo**, selecione **EDIFACT**. Selecione o **Host Partner,** **Host Identity,** **Guest Partner** e **Guest Identity** para o seu contrato.

   ![Fornecer detalhes do acordo](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Propriedade | Descrição |
   | --- | --- |
   | Nome |Nome do acordo |
   | Tipo de Acordo | Deve ser EDIFACT |
   | Parceiro anfitrião |Um acordo precisa tanto de um anfitrião como de um parceiro convidado. O parceiro anfitrião representa a organização que configura o acordo. |
   | Identidade do anfitrião |Um identificador para o parceiro anfitrião |
   | Parceiro Convidado |Um acordo precisa tanto de um anfitrião como de um parceiro convidado. O parceiro convidado representa a organização que está a fazer negócios com o parceiro anfitrião. |
   | Identidade do Hóspede |Um identificador para o parceiro convidado |
   | Receber Definições |Estas propriedades aplicam-se a todas as mensagens recebidas por um acordo. |
   | Enviar Definições |Estas propriedades aplicam-se a todas as mensagens enviadas por acordo. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configure como o seu acordo lida com mensagens recebidas

Agora que definiu as propriedades do contrato, pode configurar como este acordo identifica e lida com mensagens recebidas do seu parceiro através deste acordo.

> [!IMPORTANT]
> O conector EDIFACT suporta apenas caracteres UTF-8.
> Se a sua saída contiver caracteres inesperados, verifique se as suas mensagens EDIFACT utilizam o conjunto de caracteres UTF-8.

1. Em **Adicionar**, selecione **'Receber' Definições**.
Configure estas propriedades com base no seu acordo com o parceiro que troca mensagens consigo. Para obter descrições de propriedade, consulte as tabelas desta secção.

   **As Definições de Receber** são organizadas nestas secções: Identificadores, Reconhecimento, Esquemas, Números de Controlo, Validação e Definições Internas.

   ![Configurar "Receber Definições"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Depois de terminar, certifique-se de que guarda as suas definições escolhendo **OK**.

Agora o seu acordo está pronto para lidar com mensagens recebidas que estejam em conformidade com as definições selecionadas.

### <a name="identifiers"></a>Identificadores

| Propriedade | Descrição |
| --- | --- |
| UNB6.1 (Senha de referência do destinatário) |Introduza um valor alfanumérico que varia entre 1 e 14 caracteres. |
| UNB6.2 (Qualificação de Referência para Destinatário) |Introduza um valor alfanumérico com um mínimo de um personagem e um máximo de dois caracteres. |

### <a name="acknowledgments"></a>Agradecimentos

| Propriedade | Descrição |
| --- | --- |
| Receção da Mensagem (CONTRL) |Selecione esta caixa de verificação para devolver um reconhecimento técnico (CONTRL) ao remetente de intercâmbio. O reconhecimento é enviado para o remetente de intercâmbio com base nas Definições de Envio para o acordo. |
| Reconhecimento (CONTRL) |Selecione esta caixa de verificação para devolver um reconhecimento funcional (CONTRL) ao remetente de intercâmbio O reconhecimento é enviado para o remetente de intercâmbio com base nas Definições de Envio para o acordo. |

### <a name="schemas"></a>Esquemas

| Propriedade | Descrição |
| --- | --- |
| UNH2.1 (TIPO) |Selecione um tipo de conjunto de transação. |
| UNH2.2 (VERSÃO) |Introduza o número da versão da mensagem. (Mínimo, um carácter; máximo, três caracteres). |
| UNH2.3 (LANÇAMENTO) |Introduza o número de desbloqueio da mensagem. (Mínimo, um carácter; máximo, três caracteres). |
| UNH2.5 (CÓDIGO DESIGNADO ASSOCIADO) |Introduza o código atribuído. (Máximo, seis caracteres. Deve ser alfanumérico). |
| UNG2.1 (APP SENDER ID) |Introduza um valor alfanumérico com um mínimo de um personagem e um máximo de 35 caracteres. |
| UNG2.2 (APP SENDER CODE QUALIFIER) |Insira um valor alfanumérico, com um máximo de quatro caracteres. |
| SCHEMA |Selecione o esquema previamente carregado que pretende utilizar na sua conta de integração associada. |

### <a name="control-numbers"></a>Números de controlo

| Propriedade | Descrição |
| --- | --- |
| Duplicar o número de controlo de intercâmbio |Para bloquear trocas duplicadas, selecione esta propriedade. Se selecionado, o EDIFACT Decode Action verifica se o número de controlo de intercâmbio (UNB5) para o intercâmbio recebido não corresponde a um número de controlo de intercâmbio previamente processado. Se for detetada uma correspondência, o intercâmbio não será processado. |
| Verifique se a duplicação do UNB5 a cada (dias) |Se optar por não permitir duplicar os números de controlo de intercâmbio, pode especificar o número de dias para efetuar a verificação, dando o valor adequado para esta definição. |
| Duplicar o número de controlo do grupo |Para bloquear trocas com números de controlo de grupo duplicados (UNG5), selecione esta propriedade. |
| Desativar duplicações do número de controlo do conjunto de transações |Para bloquear trocas com números de controlo de transação duplicados (UNH1), selecione esta propriedade. |
| Número de controlo de reconhecimento EDIFACT |Para designar os números de referência definidos pela transação para utilização num reconhecimento, introduza um valor para o prefixo, uma gama de números de referência e um sufixo. |

### <a name="validation"></a>Validação

Quando se completa cada linha de validação, outra é automaticamente adicionada. Se não especificar quaisquer regras, a validação utiliza a linha "Predefinido".

| Propriedade | Descrição |
| --- | --- |
| Tipo de mensagem |Selecione o tipo de mensagem EDI. |
| Validação do EDI |Realizar validação de EDI em tipos de dados definidos pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores de fuga. |
| Validação alargada |Se o tipo de dados não for EDI, a validação está no requisito do elemento de dados e permitida repetição, enumerações e validação do comprimento do elemento de dados (min/máx). |
| Permitir Zeros de Liderança/Trailing |Mantenha qualquer protagonista adicional ou trailing zero e caracteres espaciais. Não remova estes caracteres. |
| Trim Liderando/Trailing Zeroes |Remova caracteres de condução ou de remova zero e espaço. |
| Política de separador de rastos |Gerar separadores de rasto. <p>Selecione **Não É Permitido** proibir delimiters e separadores de rasto no intercâmbio recebido. Se o intercâmbio tiver delimiters e separadores, o intercâmbio é declarado não válido. <p>Selecione **Opcional** para aceitar intercâmbios com ou sem delimiters e separadores de fuga. <p>Selecione **Obrigatório** quando o intercâmbio recebido deve ter delimiters e separadores de fuga. |

### <a name="internal-settings"></a>Configurações internas

| Propriedade | Descrição |
| --- | --- |
| Crie tags XML vazias se forem permitidos separadores de fuga |Selecione esta caixa de verificação para que o remetente de intercâmbio inclua tags XML vazias para separadores de fuga. |
| Split Interchange como conjuntos de transações - suspender conjuntos de transações por erro|Parses cada transação definida num intercâmbio num documento XML separado, aplicando o envelope apropriado ao conjunto de transações. Suspender apenas os conjuntos de transações que falham na validação. |
| Split Interchange como conjuntos de transações - suspender o intercâmbio por erro|Parses cada transação definida num intercâmbio num documento XML separado aplicando o envelope apropriado. Suspender todo o intercâmbio quando uma ou mais transações se estabelecerem na validação de falhas de permuta. | 
| Preserve Interchange - suspender conjuntos de transações por erro |Deixa o intercâmbio intacto, cria um documento XML para todo o intercâmbio em lote. Suspender apenas os conjuntos de transações que falham na validação, enquanto continua a processar todos os outros conjuntos de transações. |
| Preserve Interchange - suspender o intercâmbio por erro |Deixa o intercâmbio intacto, cria um documento XML para todo o intercâmbio em lote. Suspender todo o intercâmbio quando uma ou mais transações se estabelecerem na validação de falhas de permuta. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configure como o seu acordo envia mensagens

Pode configurar como este acordo identifica e trata as mensagens de saída que envia aos seus parceiros através deste acordo.

1.  Em **Adicionar**, selecione **'Enviar Definições'.**
Configure estas propriedades com base no seu acordo com o seu parceiro que troca mensagens consigo. Para obter descrições de propriedade, consulte as tabelas desta secção.

    **As Definições de Envio** são organizadas nestas secções: Identificadores, Reconhecimento, Esquemas, Envelopes, Conjuntos de Caracteres e Separadores, Números de Controlo e Validações.

    ![Configurar "Enviar Definições"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Depois de terminar, certifique-se de que guarda as suas definições escolhendo **OK**.

Agora o seu acordo está pronto para lidar com mensagens de saída que estejam em conformidade com as definições selecionadas.

### <a name="identifiers"></a>Identificadores

| Propriedade | Descrição |
| --- | --- |
| UNB1.2 (Versão Sintaxe) |Selecione um valor entre **1** e **4**. |
| UNB2.3 (Endereço de encaminhamento inverso do remetente) |Introduza um valor alfanumérico com um mínimo de um personagem e um máximo de 14 caracteres. |
| UNB3.3 (Endereço de encaminhamento inverso do destinatário) |Introduza um valor alfanumérico com um mínimo de um personagem e um máximo de 14 caracteres. |
| UNB6.1 (Senha de referência do destinatário) |Introduza um valor alfanumérico com um mínimo de um e um máximo de 14 caracteres. |
| UNB6.2 (Qualificação de Referência para Destinatário) |Introduza um valor alfanumérico com um mínimo de um personagem e um máximo de dois caracteres. |
| UNB7 (ID de referência de aplicação) |Insira um valor alfanumérico com um mínimo de um personagem e um máximo de 14 caracteres |

### <a name="acknowledgment"></a>Reconhecimento

| Propriedade | Descrição |
| --- | --- |
| Receção da Mensagem (CONTRL) |Selecione esta caixa de verificação se o parceiro hospedado espera receber um reconhecimento técnico (CONTRL). Esta definição especifica que o parceiro hospedado, que está a enviar a mensagem, solicita um aviso do parceiro convidado. |
| Reconhecimento (CONTRL) |Selecione esta caixa de verificação se o parceiro hospedado espera receber um reconhecimento funcional (CONTRL). Esta definição especifica que o parceiro hospedado, que está a enviar a mensagem, solicita um aviso do parceiro convidado. |
| Gerar loop SG1/SG4 para conjuntos de transações aceites |Se optar por solicitar um reconhecimento funcional, selecione esta caixa de verificação para forçar a geração de loops SG1/SG4 em reconhecimentos CONTRL funcionais para conjuntos de transações aceites. |

### <a name="schemas"></a>Esquemas

| Propriedade | Descrição |
| --- | --- |
| UNH2.1 (TIPO) |Selecione um tipo de conjunto de transação. |
| UNH2.2 (VERSÃO) |Introduza o número da versão da mensagem. |
| UNH2.3 (LANÇAMENTO) |Introduza o número de desbloqueio da mensagem. |
| SCHEMA |Selecione o esquema para usar. Os esquemas estão localizados na sua conta de integração. Para aceder aos seus esquemas, ligue primeiro a sua conta de integração à sua aplicação Logic. |

### <a name="envelopes"></a>Envelopes

| Propriedade | Descrição |
| --- | --- |
| UNB8 (Código Prioritário de Processamento) |Introduza um valor alfabético que não tenha mais do que um personagem. |
| UNB10 (Acordo de Comunicação) |Introduza um valor alfanumérico com um mínimo de um personagem e um máximo de 40 caracteres. |
| UNB11 (Indicador de Teste) |Selecione esta caixa de verificação para indicar que o intercâmbio gerado são dados de teste |
| Aplicar segmento UNA (Conselho de Cadeia de Serviço) |Selecione esta caixa de verificação para gerar um segmento UNA para o intercâmbio a ser enviado. |
| Aplicar segmentos UNG (Cabeçalho do Grupo de Função) |Selecione esta caixa de verificação para criar segmentos de agrupamento no cabeçalho funcional do grupo nas mensagens enviadas ao parceiro convidado. Os seguintes valores são utilizados para criar os segmentos UNG: <p>Para **UNG1**, insira um valor alfanumérico com um mínimo de um personagem e um máximo de seis caracteres. <p>Para **UNG2.1**, insira um valor alfanumérico com um mínimo de um personagem e um máximo de 35 caracteres. <p>Para **UNG2.2**, insira um valor alfanumérico, com um máximo de quatro caracteres. <p>Para **UNG3.1**, insira um valor alfanumérico com um mínimo de um personagem e um máximo de 35 caracteres. <p>Para **UNG3.2**, insira um valor alfanumérico, com um máximo de quatro caracteres. <p>Para **UNG6**, insira um valor alfanumérico com um mínimo de um e um máximo de três caracteres. <p>Para **UNG7.1**, insira um valor alfanumérico com um mínimo de um personagem e um máximo de três caracteres. <p>Para **UNG7.2**, insira um valor alfanumérico com um mínimo de um personagem e um máximo de três caracteres. <p>Para **UNG7.3**, insira um valor alfanumérico com um mínimo de 1 caracteres e um máximo de 6 caracteres. <p>Para **UNG8**, insira um valor alfanumérico com um mínimo de um personagem e um máximo de 14 caracteres. |

### <a name="character-sets-and-separators"></a>Conjuntos de caracteres e separadores

Além do conjunto de caracteres, pode introduzir um conjunto diferente de delimiters a serem utilizados para cada tipo de mensagem. Se um conjunto de caracteres não for especificado para um esquema de mensagem dado, então o conjunto de caracteres predefinidos é utilizado.

| Propriedade | Descrição |
| --- | --- |
| UNB1.1 (Identificador de Sistema) |Selecione o conjunto de caracteres EDIFACT a aplicar no intercâmbio de saída. |
| Esquema |Selecione um esquema da lista de drop-down. Depois de completar cada linha, uma nova linha é automaticamente adicionada. Para o esquema selecionado, selecione os separadores definidos que pretende utilizar, com base nas descrições do separador abaixo. |
| Tipo de Entrada |Selecione um tipo de entrada da lista de drop-down. |
| Separador de componentes |Para separar elementos de dados compostos, introduza um único carácter. |
| Separador de elementos de dados |Para separar elementos de dados simples dentro de elementos de dados compostos, introduza um único carácter. |
| Exterminador de segmento |Para indicar o fim de um segmento EDI, introduza um único carácter. |
| Sufixo |Selecione o carácter que é utilizado com o identificador de segmento. Se designar um sufixo, então o elemento de dados do exterminador do segmento pode estar vazio. Se o exterminador do segmento ficar vazio, deve designar um sufixo. |

### <a name="control-numbers"></a>Números de controlo

| Propriedade | Descrição |
| --- | --- |
| UNB5 (Número de controlo de intercâmbio) |Introduza um prefixo, uma gama de valores para o número de controlo de intercâmbio e um sufixo. Estes valores são usados para gerar um intercâmbio de saída. O prefixo e o sufixo são opcionais, enquanto o número de controlo é necessário. O número de controlo é incrementado para cada nova mensagem; o prefixo e o sufixo permanecem os mesmos. |
| UNG5 (Número de Controlo de Grupo) |Introduza um prefixo, uma gama de valores para o número de controlo de intercâmbio e um sufixo. Estes valores são utilizados para gerar o número de controlo do grupo. O prefixo e o sufixo são opcionais, enquanto o número de controlo é necessário. O número de controlo é incrementado para cada nova mensagem até que o valor máximo seja atingido; o prefixo e o sufixo permanecem os mesmos. |
| UNH1 (Número de referência do cabeçalho de mensagem) |Introduza um prefixo, uma gama de valores para o número de controlo de intercâmbio e um sufixo. Estes valores são utilizados para gerar o número de referência do cabeçalho da mensagem. O prefixo e o sufixo são opcionais, enquanto o número de referência é necessário. O número de referência é incrementado para cada nova mensagem; o prefixo e o sufixo permanecem os mesmos. |

### <a name="validation"></a>Validação

Quando se completa cada linha de validação, outra é automaticamente adicionada. Se não especificar quaisquer regras, a validação utiliza a linha "Predefinido".

| Propriedade | Descrição |
| --- | --- |
| Tipo de mensagem |Selecione o tipo de mensagem EDI. |
| Validação do EDI |Realizar validação de EDI em tipos de dados definidos pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores de fuga. |
| Validação alargada |Se o tipo de dados não for EDI, a validação está no requisito do elemento de dados e permitida repetição, enumerações e validação do comprimento do elemento de dados (min/máx). |
| Permitir Zeros de Liderança/Trailing |Mantenha qualquer protagonista adicional ou trailing zero e caracteres espaciais. Não remova estes caracteres. |
| Trim Liderando/Trailing Zeroes |Remova caracteres principais ou sem caracteres. |
| Política de separador de rastos |Gerar separadores de rasto. <p>Selecione **Não É Permitido** proibir delimiters e separadores de rasto no intercâmbio enviado. Se o intercâmbio tiver delimiters e separadores, o intercâmbio é declarado não válido. <p>Selecione **Opcional** para enviar intercâmbios com ou sem delimiters e separadores de fuga. <p>Selecione **Obrigatório** se o intercâmbio enviado tiver de ter delimiters e separadores de fuga. |

## <a name="find-your-created-agreement"></a>Encontre o seu acordo criado

1.  Depois de terminar de definir todas as propriedades do seu contrato, na página **Add,** escolha **OK** para terminar de criar o seu contrato e voltar à sua conta de integração.

    O seu novo acordo agora adicionado aparece na sua lista **de Acordos.**

2.  Também pode ver os seus acordos na sua conta de integração. No menu da sua conta de integração, escolha **o Resumo** e, em seguida, selecione o azulejo **dos Contratos.** 

    ![Screenshot que mostra o azulejo dos Acordos.](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/edifact/).

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)esta versão com rótulo ISE deste conector utiliza os limites de [mensagem B2B para ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
