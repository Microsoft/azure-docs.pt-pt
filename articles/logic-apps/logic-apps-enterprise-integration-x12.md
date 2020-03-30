---
title: X12 mensagens para integração B2B
description: Trocar mensagens X12 em formato EDI para integração empresarial B2B em Aplicações Lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: cbf0a1f033ddafc68debab8de26dff29d73cc98e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651479"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens X12 para integração empresarial B2B em Aplicações Lógicas Azure com Pacote de Integração Empresarial

Antes de poder trocar mensagens X12 por Apps Azure Logic, tem de criar um acordo X12 e armazenar esse acordo na sua conta de integração. Aqui estão os passos para como criar um acordo X12.

> [!NOTE]
> Esta página cobre as funcionalidades X12 para Aplicações Lógicas Azure. Para mais informações, consulte [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens que precisa:

* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua subscrição Azure
* Pelo menos dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) que são definidos na sua conta de integração e configurados com o identificador X12 ao abrigo de **Identidades empresariais**    
* Um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessário que pode fazer o upload para a sua conta de integração

Depois de criar uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [adicionar parceiros,](logic-apps-enterprise-integration-partners.md)e ter um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) que pretende utilizar, pode criar um acordo X12 seguindo estes passos.

## <a name="create-an-x12-agreement"></a>Criar um acordo X12

1. Inicie sessão no [Portal do Azure](https://portal.azure.com "Portal do Azure"). 

2. A partir do menu Principal Azure, selecione **Todos os serviços.** 
   Na caixa de pesquisa, insira "integração", e, em seguida, selecione **contas integrais.**  

   ![Encontre a sua conta de integração](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Se **todos os serviços** não aparecerem, poderá ter de expandir o menu primeiro. No topo do menu colapsado, selecione **menu Show**.

3. Em **Contas integrativas,** selecione a conta de integração onde pretende adicionar o acordo.

   ![Selecione conta de integração onde criar o acordo](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Selecione **visão geral**e, em seguida, selecione o azulejo **dos Acordos.** 
   Se não tiver um azulejo de acordos, adicione primeiro o azulejo. 

   ![Escolha azulejos "Acordos"](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. Nos **termos de Acordos,** escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. Em **Adicionar,** insira um **nome** para o seu acordo. 
   Para o tipo de acordo, selecione **X12**. 
   Selecione o **Parceiro anfitrião,** identidade de **anfitrião,** **parceiro convidado**e identidade de **hóspedes** para o seu acordo. 
   Para mais detalhes sobre a propriedade, consulte a mesa neste passo.

    ![Fornecer detalhes do acordo](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Propriedade | Descrição |
    | --- | --- |
    | Nome |Nome do acordo |
    | Tipo de acordo | Deve ser X12 |
    | Parceiro anfitrião |Um acordo precisa tanto de um anfitrião como de um parceiro convidado. O parceiro anfitrião representa a organização que configura o acordo. |
    | Identidade de acolhimento |Um identificador para o parceiro anfitrião |
    | Parceiro Convidado |Um acordo precisa tanto de um anfitrião como de um parceiro convidado. O parceiro convidado representa a organização que está a fazer negócios com o parceiro anfitrião. |
    | Identidade de Hóspede |Um identificador para o parceiro convidado |
    | Receber Definições |Estas propriedades aplicam-se a todas as mensagens recebidas por um acordo. |
    | Enviar Definições |Estas propriedades aplicam-se a todas as mensagens enviadas por um acordo. |  

   > [!NOTE]
   > A resolução do acordo X12 depende da correspondência entre o qualificador e o identificador do remetente, bem como o recetor e identificador definido no parceiro e na mensagem de entrada. Se estes valores mudarem para o seu parceiro, atualize o acordo também.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configure como o seu acordo lida com mensagens recebidas

Agora que definiu as propriedades do contrato, pode configurar como este acordo identifica e lida com as mensagens recebidas do seu parceiro através deste acordo.

1.  Em **'Adicionar',** **selecione 'Receber Definições**' .
Configure estas propriedades com base no seu acordo com o parceiro que troca mensagens consigo. Para descrições de propriedades, consulte as tabelas nesta secção.

    **As Definições de Receção** estão organizadas nestas secções: Identificadores, Reconhecimento, Schemas, Envelopes, Números de Controlo, Validações e Definições Internas.

2. Depois de terminar, certifique-se de guardar as suas definições escolhendo **OK**.

Agora o seu acordo está pronto para lidar com mensagens de entrada que estejam em conformidade com as definições selecionadas.

### <a name="identifiers"></a>Identificadores

![Definir propriedades de identificador](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Propriedade | Descrição |
| --- | --- |
| ISA1 (Qualificação de Autorização) |Selecione o valor de qualificação de autorização da lista de abandono. |
| ISA2 |Opcional. Insira o valor da informação da Autorização. Se o valor que inscreveu para o ISA1 for diferente de 00, introduza um mínimo de um carácter alfanumérico e um máximo de 10. |
| ISA3 (Qualificação de Segurança) |Selecione o valor de qualificação de segurança da lista de drop-down. |
| ISA4 |Opcional. Introduza o valor da informação de segurança. Se o valor que inscreveu para o ISA3 for diferente de 00, introduza um mínimo de um carácter alfanumérico e um máximo de 10. |

### <a name="acknowledgment"></a>Reconhecimento

![Definir propriedades de reconhecimento](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Propriedade | Descrição |
| --- | --- |
| TA1 esperado |Devolve um reconhecimento técnico ao remetente de intercâmbio |
| Fa esperado |Devolve um reconhecimento funcional ao remetente de intercâmbio. Em seguida, selecione se deseja os agradecimentos 997 ou 999, com base na versão esquema |
| Incluir laço AK2/IK2 |Permite a geração de loops AK2 em agradecimentos funcionais para conjuntos de transações aceites |

### <a name="schemas"></a>Esquemas

Selecione um esquema para cada tipo de transação (ST1) e Aplicação Sender (GS2). O gasoduto de receção desmonta a mensagem de entrada combinando os valores de ST1 e GS2 na mensagem de entrada com os valores que fixou aqui, e o esquema da mensagem de entrada com o esquema que fixou aqui.

![Selecione esquema](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Propriedade | Descrição |
| --- | --- |
| Versão |Selecione a versão X12 |
| Tipo de transação (ST01) |Selecione o tipo de transação |
| Aplicação sender (GS02) |Selecione a aplicação remetente |
| Esquema |Selecione o ficheiro de esquema que pretende utilizar. Os schemas são adicionados à sua conta de integração. |

> [!NOTE]
> Configure o [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessário que é enviado para a sua conta de [integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Envelopes

![Especifique o separador num conjunto de transações: escolha identificador padrão ou separador de repetição](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Propriedade | Descrição |
| --- | --- |
| Usáldo ISA11 |Especifica o separador a utilizar num conjunto de transações: <p>Selecione **identificador Standard** para utilizar um período (.) para notação decimal, em vez da notação decimal do documento de entrada no EDI receber o gasoduto. <p>Selecione **Separador de Repetição** para especificar o separador para ocorrências repetidas de um simples elemento de dados ou de uma estrutura de dados repetida. Por exemplo, normalmente o quilate (^) é usado como separador de repetição. Para os esquemas hipaa, só pode usar o quilate. |

### <a name="control-numbers"></a>Números de controlo

![Selecione como lidar com duplicados de números de controlo](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Propriedade | Descrição |
| --- | --- |
| Duplicado do Número de Controlo de Intercâmbio deSallow |Bloqueie trocas duplicadas. Verifica o número de controlo de permuta (ISA13) para o número de controlo de intercâmbio recebido. Se for detetada uma correspondência, o gasoduto de receção não processa o intercâmbio. Pode especificar o número de dias para a realização do cheque, dando um valor para *verificar o duplicado ISA13 todos os dias ( dias)*. |
| Duplicados de número de controlo de grupo desabotoados |Alternações de blocos com números de controlo de grupo duplicados. |
| Duplicados de número de controlo de conjunto de transações desadmissidos |Bloqueie trocas com números de controlo de transações duplicadas. |

### <a name="validation"></a>Validação

![Definir propriedades de validação para mensagens recebidas](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Quando completa cada linha de validação, outra é adicionada automaticamente. Se não especificar quaisquer regras, a validação utiliza a linha "Padrão".

| Propriedade | Descrição |
| --- | --- |
| Tipo de mensagem |Selecione o tipo de mensagem EDI. |
| Validação EDI |Execute a validação EDI em tipos de dados definidos pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores de rasto. |
| Validação Alargada |Se o tipo de dados não for EDI, a validação está no requisito do elemento de dados e permitiu a repetição, enumerações e validação do comprimento do elemento de dados (min/max). |
| Permitir zeros de ponta/rasto |Mantenha quaisquer caracteres adicionais de liderança ou de fuga zero e espaciais. Não remova estes caracteres. |
| Guarnição de pontos de liderança/trailing |Remova os caracteres de liderança ou de rasto zero e espaciais. |
| Política de Separador de Trailing |Gerar separadores de rastos. <p>Selecione **Não É Permitido** proibir delimitar e separadores no intercâmbio recebido. Se o intercâmbio tiver delimitadores e separadores, o intercâmbio é declarado inválido. <p>Selecione **Opcional** para aceitar intercâmbios com ou sem delimitadores e separadores. <p>**Selecione Obrigatório** quando o intercâmbio deve ter delimitadores e separadores de rasto. |

### <a name="internal-settings"></a>Configurações internas

![Selecione definições internas](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Propriedade | Descrição |
| --- | --- |
| Converter o formato decimal implícito "Nn" para um valor numérico base 10 |Converte um número EDI especificado com o formato "Nn" num valor numérico base-10 |
| Crie etiquetas XML vazias se forem permitidos separadores de rasto |Selecione esta caixa de verificação para que o remetente de intercâmbio inclua etiquetas XML vazias para separadores de rasto. |
| Split Interchange como conjuntos de transações - suspender conjuntos de transações por erro|Analisa cada transação definida num intercâmbio num documento XML separado aplicando o envelope apropriado ao conjunto de transações. Suspende apenas as transações quando a validação falha. |
| Intercâmbio dividido como conjuntos de transações - suspender o intercâmbio por erro|Analisa cada transação definida num intercâmbio num documento XML separado aplicando o envelope apropriado. Suspende o intercâmbio total quando uma ou mais transações se estabelecem na validação de falhas de troca. | 
| Preserve Interchange - suspender conjuntos de transações por erro |Deixa o intercâmbio intacto, cria um documento XML para todo o intercâmbio em lotado. Suspende apenas os conjuntos de transações que falham na validação, continuando a processar todos os outros conjuntos de transações. |
| Preserve Interchange - suspender o intercâmbio por erro |Deixa o intercâmbio intacto, cria um documento XML para todo o intercâmbio em lotado. Suspende todo o intercâmbio quando uma ou mais transações se estabelecem na validação de falhas de troca. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configure como o seu acordo envia mensagens

Pode configurar como este acordo identifica e lida com mensagens de saída que envia ao seu parceiro através deste acordo.

1.  Em **adicionar,** selecione **'Enviar Definições**' .
Configure estas propriedades com base no seu acordo com o seu parceiro que troca mensagens consigo. Para descrições de propriedades, consulte as tabelas nesta secção.

    **As Definições de Envio** estão organizadas nestas secções: Identificadores, Reconhecimento, Schemas, Envelopes, Conjuntos de Caracteres e Separadores, Números de Controlo e Validação.

2. Depois de terminar, certifique-se de guardar as suas definições escolhendo **OK**.

Agora o seu acordo está pronto para lidar com mensagens de saída que estejam em conformidade com as definições selecionadas.

### <a name="identifiers"></a>Identificadores

![Definir propriedades de identificador](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Propriedade | Descrição |
| --- | --- |
| Qualificação de autorização (ISA1) |Selecione o valor de qualificação de autorização da lista de abandono. |
| ISA2 |Insira o valor da informação da Autorização. Se este valor for diferente de 00, introduza um mínimo de um carácter alfanumérico e um máximo de 10. |
| Qualificação de segurança (ISA3) |Selecione o valor de qualificação de segurança da lista de drop-down. |
| ISA4 |Introduza o valor da informação de segurança. Se este valor for diferente de 00, para a caixa de texto Value (ISA4), introduza um mínimo de um valor alfanumérico e um máximo de 10. |

### <a name="acknowledgment"></a>Reconhecimento

![Definir propriedades de reconhecimento](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriedade | Descrição |
| --- | --- |
| TA1 esperado |Devolva um reconhecimento técnico (TA1) ao remetente de intercâmbio. Esta definição especifica que o parceiro anfitrião que está a enviar a mensagem solicita um reconhecimento do parceiro convidado no acordo. Estes agradecimentos são esperados pelo parceiro anfitrião com base nas Definições de Receção do acordo. |
| Fa esperado |Devolva um reconhecimento funcional (FA) ao remetente de intercâmbio. Selecione se deseja os agradecimentos 997 ou 999, com base nas versões de esquema com as quais está a trabalhar. Estes agradecimentos são esperados pelo parceiro anfitrião com base nas Definições de Receção do acordo. |
| Versão FA |Selecione a versão FA |

### <a name="schemas"></a>Esquemas

![Selecione esquema para usar](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriedade | Descrição |
| --- | --- |
| Versão |Selecione a versão X12 |
| Tipo de transação (ST01) |Selecione o tipo de transação |
| SCHEMA |Selecione o esquema para usar. Os schemas estão localizados na sua conta de integração. Se selecionar o schema primeiro, ele automaticamente confunde versão e tipo de transação  |

> [!NOTE]
> Configure o [Schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessário que é enviado para a sua conta de [integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Envelopes

![Especifique o separador num conjunto de transações: escolha identificador padrão ou separador de repetição](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Propriedade | Descrição |
| --- | --- |
| Usáldo ISA11 |Especifica o separador a utilizar num conjunto de transações: <p>Selecione **identificador Standard** para utilizar um período (.) para notação decimal, em vez da notação decimal do documento de entrada no EDI receber o gasoduto. <p>Selecione **Separador de Repetição** para especificar o separador para ocorrências repetidas de um simples elemento de dados ou de uma estrutura de dados repetida. Por exemplo, normalmente o quilate (^) é usado como separador de repetição. Para os esquemas hipaa, só pode usar o quilate. |

### <a name="control-numbers"></a>Números de controlo

![Especificar propriedades de números de controlo](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Propriedade | Descrição |
| --- | --- |
| Número da versão de controlo (ISA12) |Selecione a versão do padrão X12 |
| Indicador de utilização (ISA15) |Selecione o contexto de um intercâmbio.  Os valores são informação, dados de produção ou dados de teste |
| Esquema |Gera os segmentos GS e ST para um intercâmbio codificado x12 que envia para o Pipeline Send |
| GS1 |Opcional, selecione um valor para o código funcional da lista de lançamentos |
| GS2 |Opcional, remetente de aplicações |
| GS3 |Opcional, recetor de aplicações |
| GS4 |Opcional, selecione CCYYMMDD ou YYMMDD |
| GS5 |Opcional, selecione HHMM, HHMMSS ou HHMMSSdd |
| GS7 |Opcional, selecione um valor para a agência responsável da lista de abandono |
| GS8 |Opcional, versão do documento |
| Número de controlo de intercâmbio (ISA13) |Necessário, introduza uma gama de valores para o número de controlo de permuta. Insira um valor numérico com um mínimo de 1 e um máximo de 999999999 |
| Número de controlo de grupo (GS06) |Necessário, introduza uma gama de números para o número de controlo do grupo. Insira um valor numérico com um mínimo de 1 e um máximo de 999999999 |
| Número de controlo definido de transação (ST02) |Necessário, introduza uma gama de números para o número de controlo do conjunto de transações. Insira uma gama de valores numéricos com um mínimo de 1 e um máximo de 9999999999 |
| Prefixo |Opcional, designado para o intervalo de números de controlo definidos de transação utilizados em reconhecimento. Introduza um valor numérico para os dois campos médios, e um valor alfanumérico (se desejar) para os campos de prefixo e sufixo. Os campos intermédios são necessários e contêm os valores mínimos e máximos para o número de controlo |
| Sufixo |Opcional, designado para o intervalo de números de controlo definidos de transação utilizados num reconhecimento. Introduza um valor numérico para os dois campos médios e um valor alfanumérico (se desejar) para os campos de prefixo e sufixo. Os campos intermédios são necessários e contêm os valores mínimos e máximos para o número de controlo |

### <a name="character-sets-and-separators"></a>Conjuntos de caracteres e separadores

Além do conjunto de caracteres, pode introduzir um conjunto diferente de delimitadores para cada tipo de mensagem. Se um conjunto de caracteres não for especificado para um determinado esquema de mensagem, então o conjunto de caracteres padrão é usado.

![Especificar delimitadores para tipos de mensagens](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Propriedade | Descrição |
| --- | --- |
| Conjunto de caracteres a ser usado |Para validar as propriedades, selecione o conjunto de caracteres X12. As opções são Básicas, Estendidas e UTF8. |
| Esquema |Selecione um esquema da lista de lançamentos. Depois de completar cada linha, uma nova linha é adicionada automaticamente. Para o esquema selecionado, selecione o conjunto de separadores que pretende utilizar, com base nas descrições do separador abaixo. |
| Tipo de Entrada |Selecione um tipo de entrada na lista de drop-down. |
| Separador de componentes |Para separar elementos de dados compósitos, introduza um único carácter. |
| Separador de elementos de dados |Para separar elementos de dados simples dentro de elementos de dados compósitos, introduza um único caracteres. |
| Personagem de substituição |Introduza um caracteres de substituição utilizado para substituir todos os caracteres separadores nos dados de carga útil ao gerar a mensagem X12 de saída. |
| Exterminador de Segmento |Para indicar o fim de um segmento EDI, insira um único personagem. |
| Sufixo |Selecione o caracteres utilizados com o identificador de segmento. Se designar um sufixo, então o elemento de dados do exterminador do segmento pode estar vazio. Se o exterminador do segmento ficar vazio, então deve designar um sufixo. |

> [!TIP]
> Para fornecer valores especiais de carácter, edite o acordo como JSON e forneça o valor ASCII para o caráter especial.

### <a name="validation"></a>Validação

![Definir propriedades de validação para envio de mensagens](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Quando completa cada linha de validação, outra é adicionada automaticamente. Se não especificar quaisquer regras, a validação utiliza a linha "Padrão".

| Propriedade | Descrição |
| --- | --- |
| Tipo de mensagem |Selecione o tipo de mensagem EDI. |
| Validação EDI |Execute a validação EDI em tipos de dados definidos pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores de rasto. |
| Validação Alargada |Se o tipo de dados não for EDI, a validação está no requisito do elemento de dados e permitiu a repetição, enumerações e validação do comprimento do elemento de dados (min/max). |
| Permitir zeros de ponta/rasto |Mantenha quaisquer caracteres adicionais de liderança ou de fuga zero e espaciais. Não remova estes caracteres. |
| Guarnição de pontos de liderança/trailing |Remova os caracteres de liderança ou de rasto zero. |
| Política de Separador de Trailing |Gerar separadores de rastos. <p>Selecione **Não É Permitido** proibir delimitar e separadores no intercâmbio enviado. Se o intercâmbio tiver delimitadores e separadores, o intercâmbio é declarado inválido. <p>Selecione **Opcional** para enviar intercâmbios com ou sem delimitadores e separadores. <p>**Selecione Obrigatório** se o intercâmbio enviado deve ter delimitadores e separadores de rasto. |

## <a name="find-your-created-agreement"></a>Encontre o seu acordo criado

1.  Depois de terminar de definir todas as propriedades do seu contrato, na página **Adicionar,** escolha **OK** para terminar de criar o seu acordo e volte à sua conta de integração.

    O seu novo acordo aparece agora na sua lista de **Acordos.**

2.  Pode também ver os seus acordos na sua conta de integração. No menu da sua conta de integração, escolha **visão geral**e, em seguida, selecione o azulejo **dos Acordos.**

    ![Escolha azulejos "Acordos"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/x12/). 

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão do conector com o rótulo ISE utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)