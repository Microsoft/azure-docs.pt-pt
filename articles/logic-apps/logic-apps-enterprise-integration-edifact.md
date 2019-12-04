---
title: Mensagens EDIFACT para integração B2B
description: Trocar mensagens EDIFACT no formato EDI para integração corporativa B2B em aplicativos lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/26/2016
ms.openlocfilehash: b6a6ee21774ba931d9982d82b99008f312d19736
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793016"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens EDIFACT para integração de empresas B2B em aplicativos lógicos do Azure com o Enterprise Integration Pack

Antes de poder trocar mensagens EDIFACT para aplicativos lógicos do Azure, você deve criar um contrato EDIFACT e armazenar esse contrato em sua conta de integração. Aqui estão as etapas para criar um contrato EDIFACT.

> [!NOTE]
> Esta página aborda os recursos do EDIFACT para aplicativos lógicos do Azure. Para obter mais informações, consulte [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens de que você precisa:

* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua assinatura do Azure  
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração

> [!NOTE]
> Quando você cria um contrato, o conteúdo das mensagens que você recebe ou envia de e para o parceiro deve corresponder ao tipo de contrato.

Depois de [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [Adicionar parceiros](logic-apps-enterprise-integration-partners.md), você pode criar um contrato de EDIFACT seguindo estas etapas.

## <a name="create-an-edifact-agreement"></a>Criar um contrato do EDIFACT 

1. Inicie sessão no [portal do Azure](https://portal.azure.com "Portal do Azure"). 

2. No menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, digite "integração" e, em seguida, selecione **contas de integração**.

   ![Localizar sua conta de integração](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Se **todos os serviços** não aparecerem, talvez você tenha que expandir o menu primeiro. Na parte superior do menu recolhido, selecione **Mostrar rótulos de texto**.

3. Em **contas de integração**, selecione a conta de integração na qual você deseja criar o contrato.

   ![Selecione a conta de integração onde criar o contrato](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Escolha **contratos**. Se você não tiver um bloco de contratos, adicione o bloco primeiro.   

   ![Escolha o bloco "contratos"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na página contratos, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Em **Adicionar**, insira um **nome** para seu contrato. Para **tipo de contrato**, selecione **EDIFACT**. Selecione o **parceiro host**, a **identidade do host**, o **parceiro convidado**e a **identidade de convidado** para seu contrato.

   ![Fornecer detalhes do contrato](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Propriedade | Descrição |
   | --- | --- |
   | Nome |Nome do contrato |
   | Tipo de contrato | Deve ser EDIFACT |
   | Parceiro de host |Um contrato precisa de um parceiro de host e convidado. O parceiro host representa a organização que configura o contrato. |
   | Identidade do host |Um identificador para o parceiro de host |
   | Parceiro convidado |Um contrato precisa de um parceiro de host e convidado. O parceiro convidado representa a organização que está fazendo negócios com o parceiro de host. |
   | Identidade de convidado |Um identificador para o parceiro convidado |
   | Configurações de recebimento |Essas propriedades se aplicam a todas as mensagens recebidas por um contrato. |
   | Configurações de envio |Essas propriedades se aplicam a todas as mensagens enviadas por um contrato. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurar como seu contrato lida com mensagens recebidas

Agora que você definiu as propriedades do contrato, é possível configurar como este contrato identifica e manipula as mensagens recebidas do seu parceiro por meio deste contrato.

1. Em **Adicionar**, selecione **configurações de recebimento**.
Configure essas propriedades com base em seu contrato com o parceiro que troca mensagens com você. Para obter descrições de propriedades, consulte as tabelas nesta seção.

   **As configurações de recebimento** são organizadas nessas seções: identificadores, confirmação, esquemas, números de controle, validação e configurações internas.

   ![Configurar "configurações de recebimento"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Depois de terminar, lembre-se de salvar suas configurações escolhendo **OK**.

Agora seu contrato está pronto para lidar com mensagens de entrada que estão em conformidade com as configurações selecionadas.

### <a name="identifiers"></a>Identificadores

| Propriedade | Descrição |
| --- | --- |
| UNB 6.1 (senha de referência do destinatário) |Insira um valor alfanumérico entre 1 e 14 caracteres. |
| UNB 6.2 (qualificador de referência do destinatário) |Insira um valor alfanumérico com um mínimo de um caractere e um máximo de dois caracteres. |

### <a name="acknowledgments"></a>Confirmações

| Propriedade | Descrição |
| --- | --- |
| Recebimento de mensagem (CONTRL) |Marque esta caixa de seleção para retornar uma confirmação técnica (CONTRL) ao remetente do intercâmbio. A confirmação é enviada ao remetente do intercâmbio com base nas configurações de envio do contrato. |
| Confirmação (CONTRL) |Marque esta caixa de seleção para retornar uma confirmação funcional (CONTRL) ao remetente do intercâmbio. a confirmação é enviada ao remetente do intercâmbio com base nas configurações de envio do contrato. |

### <a name="schemas"></a>Esquemas

| Propriedade | Descrição |
| --- | --- |
| UNH 2.1 (TIPO) |Selecione um tipo de conjunto de transações. |
| UNH 2.2 (VERSÃO) |Insira o número de versão da mensagem. (Mínimo, um caractere; máximo, três caracteres). |
| UNH 2.3 (VERSÃO) |Insira o número de liberação da mensagem. (Mínimo, um caractere; máximo, três caracteres). |
| UNH 2.5 (CÓDIGO ATRIBUÍDO ASSOCIADO) |Insira o código atribuído. (Máximo, seis caracteres. Deve ser alfanumérico). |
| UNG 2.1 (ID DO REMETENTE DO APLICATIVO) |Insira um valor alfanumérico com um mínimo de um caractere e um máximo de 35 caracteres. |
| UNG 2.2 (QUALIFICADOR DE CÓDIGO DO REMETENTE DO APLICATIVO) |Insira um valor alfanumérico, com um máximo de quatro caracteres. |
| ESQUEMA |Selecione o esquema carregado anteriormente que você deseja usar em sua conta de integração associada. |

### <a name="control-numbers"></a>Números de controle
| Propriedade | Descrição |
| --- | --- |
| Não permitir duplicatas de números de controle de intercâmbio |Para bloquear intercâmbios duplicados, selecione essa propriedade. Se selecionada, a ação de decodificação EDIFACT verifica se o UNB5 (número de controle de intercâmbio) do intercâmbio recebido não corresponde a um número de controle de intercâmbio processado anteriormente. Se uma correspondência for detectada, o intercâmbio não será processado. |
| Verificar UNB5 duplicados a cada (dias) |Se você optar por não permitir números de controle de intercâmbio duplicados, poderá especificar o número de dias durante o qual executar a verificação, fornecendo o valor apropriado para essa configuração. |
| Não permitir duplicatas de número de controle de grupo |Para bloquear intercâmbios com números de controle de grupo duplicados (UNG5), selecione essa propriedade. |
| Não permitir duplicatas de números de controle de conjunto de transações |Para bloquear intercâmbios com números de controle de conjunto de transação duplicados (UNH1), selecione essa propriedade. |
| Número de controle de confirmação EDIFACT |Para designar os números de referência do conjunto de transações para uso em uma confirmação, insira um valor para o prefixo, um intervalo de números de referência e um sufixo. |

### <a name="validations"></a>Validações

Quando você conclui cada linha de validação, outra é adicionada automaticamente. Se você não especificar nenhuma regra, a validação usará a linha "padrão".

| Propriedade | Descrição |
| --- | --- |
| Tipo de mensagem |Selecione o tipo de mensagem EDI. |
| Validação de EDI |Execute a validação de EDI em tipos de dados, conforme definido pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita. |
| Validação estendida |Se o tipo de dados não for EDI, a validação estará no requisito de elemento de dados e na validação de comprimento de elemento de dados permitida, enumerações e repetição (mín/máx). |
| Permitir zeros à esquerda/à direita |Manter qualquer zero à esquerda ou à direita e caracteres de espaço adicionais. Não remova esses caracteres. |
| Cortar zeros à esquerda/à direita |Remova zeros à esquerda ou à direita e caracteres de espaço. |
| Política de separador à direita |Gerar separadores à direita. <p>Selecione **não permitido** para proibir delimitadores e separadores à direita no intercâmbio recebido. Se o intercâmbio tiver delimitadores e separadores à direita, o intercâmbio será declarado como inválido. <p>Selecione **opcional** para aceitar intercâmbios com ou sem delimitadores e separadores à direita. <p>Selecione **obrigatório** quando o intercâmbio recebido precisar ter delimitadores e separadores à direita. |

### <a name="internal-settings"></a>Configurações internas

| Propriedade | Descrição |
| --- | --- |
| Criar marcas XML vazias se os separadores à direita forem permitidos |Marque essa caixa de seleção para que o emissor de intercâmbio inclua marcas XML vazias para separadores à direita. |
| Dividir intercâmbio como conjuntos de transações – suspender conjuntos de transação com erro|Analisa cada conjunto de transações em um intercâmbio em um documento XML separado aplicando o envelope apropriado ao conjunto de transações. Suspenda apenas os conjuntos de transações que falham na validação. |
| Dividir intercâmbio como conjuntos de transações – suspender intercâmbio com erro|Analisa cada conjunto de transações em um intercâmbio em um documento XML separado aplicando o envelope apropriado. Suspenda todo o intercâmbio quando um ou mais conjuntos de transações no intercâmbio falharem na validação. | 
| Preservar intercâmbio-suspender conjuntos de transação com erro |Deixa o intercâmbio intacto, cria um documento XML para todo o intercâmbio em lote. Suspenda apenas os conjuntos de transações que falham na validação, enquanto continuam a processar todos os outros conjuntos de transações. |
| Preservar intercâmbio-suspender intercâmbio em caso de erro |Deixa o intercâmbio intacto, cria um documento XML para todo o intercâmbio em lote. Suspenda todo o intercâmbio quando um ou mais conjuntos de transações no intercâmbio falharem na validação. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurar como seu contrato envia mensagens

Você pode configurar como este contrato identifica e trata as mensagens de saída que você envia para seus parceiros por meio deste contrato.

1.  Em **Adicionar**, selecione **configurações de envio**.
Configure essas propriedades com base em seu contrato com seu parceiro que troca mensagens com você. Para obter descrições de propriedades, consulte as tabelas nesta seção.

    **As configurações de envio** são organizadas nessas seções: identificadores, confirmação, esquemas, envelopes, conjuntos de caracteres e separadores, números de controle e validações.

    ![Definir "configurações de envio"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Depois de terminar, lembre-se de salvar suas configurações escolhendo **OK**.

Agora seu contrato está pronto para lidar com mensagens de saída que estão em conformidade com as configurações selecionadas.

### <a name="identifiers"></a>Identificadores

| Propriedade | Descrição |
| --- | --- |
| UNB 1.2 (versão de sintaxe) |Selecione um valor entre **1** e **4**. |
| UNB 2.3 (endereço de roteamento reverso do remetente) |Insira um valor alfanumérico com um mínimo de um caractere e um máximo de 14 caracteres. |
| UNB 3.3 (endereço de roteamento inverso do destinatário) |Insira um valor alfanumérico com um mínimo de um caractere e um máximo de 14 caracteres. |
| UNB 6.1 (senha de referência do destinatário) |Insira um valor alfanumérico com no mínimo um e no máximo 14 caracteres. |
| UNB 6.2 (qualificador de referência do destinatário) |Insira um valor alfanumérico com um mínimo de um caractere e um máximo de dois caracteres. |
| UNB7 (ID de referência do aplicativo) |Insira um valor alfanumérico com um mínimo de um caractere e um máximo de 14 caracteres |

### <a name="acknowledgment"></a>Confirmação
| Propriedade | Descrição |
| --- | --- |
| Recebimento de mensagem (CONTRL) |Marque esta caixa de seleção se o parceiro hospedado espera receber uma confirmação técnica (CONTRL). Essa configuração especifica que o parceiro hospedado, que está enviando a mensagem, solicita uma confirmação do parceiro convidado. |
| Confirmação (CONTRL) |Marque esta caixa de seleção se o parceiro hospedado espera receber uma confirmação funcional (CONTRL). Essa configuração especifica que o parceiro hospedado, que está enviando a mensagem, solicita uma confirmação do parceiro convidado. |
| Gerar loop SG1/SG4 para conjuntos de transação aceitos |Se você optar por solicitar uma confirmação funcional, marque essa caixa de seleção para forçar a geração de loops SG1/SG4 em confirmações de CONTRL funcionais para conjuntos de transação aceitos. |

### <a name="schemas"></a>Esquemas
| Propriedade | Descrição |
| --- | --- |
| UNH 2.1 (TIPO) |Selecione um tipo de conjunto de transações. |
| UNH 2.2 (VERSÃO) |Insira o número de versão da mensagem. |
| UNH 2.3 (VERSÃO) |Insira o número de liberação da mensagem. |
| ESQUEMA |Selecione o esquema a ser usado. Os esquemas estão localizados em sua conta de integração. Para acessar seus esquemas, primeiro vincule sua conta de integração ao seu aplicativo lógico. |

### <a name="envelopes"></a>Envelopes
| Propriedade | Descrição |
| --- | --- |
| UNB8 (código de prioridade de processamento) |Insira um valor alfabético que não tenha mais de um caractere de comprimento. |
| UNB10 (contrato de comunicação) |Insira um valor alfanumérico com um mínimo de um caractere e um máximo de 40 caracteres. |
| UNB11 (indicador de teste) |Marque esta caixa de seleção para indicar que o intercâmbio gerado é de dados de teste |
| Aplicar segmento UNA (aviso de cadeia de caracteres de serviço) |Marque essa caixa de seleção para gerar um segmento UNA para o intercâmbio a ser enviado. |
| Aplicar segmentos UNG (cabeçalho de grupo de funções) |Marque essa caixa de seleção para criar segmentos de agrupamento no cabeçalho do grupo funcional nas mensagens enviadas para o parceiro convidado. Os seguintes valores são usados para criar os segmentos de UNG: <p>Para **UNG1**, insira um valor alfanumérico com um mínimo de um caractere e um máximo de seis caracteres. <p>Para **ung 2.1**, insira um valor alfanumérico com um mínimo de um caractere e um máximo de 35 caracteres. <p>Para **ung 2.2**, insira um valor alfanumérico, com um máximo de quatro caracteres. <p>Para **ung 3.1**, insira um valor alfanumérico com um mínimo de um caractere e um máximo de 35 caracteres. <p>Para **ung 3.2**, insira um valor alfanumérico, com um máximo de quatro caracteres. <p>Para **UNG6**, insira um valor alfanumérico com um mínimo de um e um máximo de três caracteres. <p>Para **ung 7.1**, insira um valor alfanumérico com um mínimo de um caractere e um máximo de três caracteres. <p>Para **ung 7.2**, insira um valor alfanumérico com um mínimo de um caractere e um máximo de três caracteres. <p>Para **ung 7.3**, insira um valor alfanumérico com um mínimo de 1 caractere e um máximo de 6 caracteres. <p>Para **UNG8**, insira um valor alfanumérico com um mínimo de um caractere e um máximo de 14 caracteres. |

### <a name="character-sets-and-separators"></a>Conjuntos de caracteres e separadores

Além do conjunto de caracteres, você pode inserir um conjunto diferente de delimitadores a serem usados para cada tipo de mensagem. Se um conjunto de caracteres não for especificado para um determinado esquema de mensagem, o conjunto de caracteres padrão será usado.

| Propriedade | Descrição |
| --- | --- |
| UNB 1.1 (identificador do sistema) |Selecione o conjunto de caracteres EDIFACT a ser aplicado no intercâmbio de saída. |
| Esquema |Selecione um esquema na lista suspensa. Depois de concluir cada linha, uma nova linha é adicionada automaticamente. Para o esquema selecionado, selecione o conjunto de separadores que você deseja usar, com base nas descrições de separador abaixo. |
| Tipo de entrada |Selecione um tipo de entrada na lista suspensa. |
| Separador de componentes |Para separar os elementos de dados compostos, insira um único caractere. |
| Separador de elemento de dados |Para separar elementos de dados simples em elementos de dados compostos, insira um único caractere. |
| Terminador de segmento |Para indicar o fim de um segmento EDI, insira um único caractere. |
| Sufixo |Selecione o caractere que é usado com o identificador de segmento. Se você designar um sufixo, o elemento de dados de terminador de segmento poderá ficar vazio. Se o terminador de segmento for deixado vazio, você deverá designar um sufixo. |

### <a name="control-numbers"></a>Números de controle
| Propriedade | Descrição |
| --- | --- |
| UNB5 (número de controle de intercâmbio) |Insira um prefixo, um intervalo de valores para o número de controle de intercâmbio e um sufixo. Esses valores são usados para gerar um intercâmbio de saída. O prefixo e o sufixo são opcionais, enquanto o número de controle é necessário. O número de controle é incrementado para cada nova mensagem; o prefixo e o sufixo permanecem os mesmos. |
| UNG5 (número de controle de grupo) |Insira um prefixo, um intervalo de valores para o número de controle de intercâmbio e um sufixo. Esses valores são usados para gerar o número de controle de grupo. O prefixo e o sufixo são opcionais, enquanto o número de controle é necessário. O número de controle é incrementado para cada nova mensagem até que o valor máximo seja atingido; o prefixo e o sufixo permanecem os mesmos. |
| UNH1 (número de referência do cabeçalho da mensagem) |Insira um prefixo, um intervalo de valores para o número de controle de intercâmbio e um sufixo. Esses valores são usados para gerar o número de referência do cabeçalho da mensagem. O prefixo e o sufixo são opcionais, enquanto o número de referência é necessário. O número de referência é incrementado para cada nova mensagem; o prefixo e o sufixo permanecem os mesmos. |

### <a name="validations"></a>Validações

Quando você conclui cada linha de validação, outra é adicionada automaticamente. Se você não especificar nenhuma regra, a validação usará a linha "padrão".

| Propriedade | Descrição |
| --- | --- |
| Tipo de mensagem |Selecione o tipo de mensagem EDI. |
| Validação de EDI |Execute a validação de EDI em tipos de dados, conforme definido pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita. |
| Validação estendida |Se o tipo de dados não for EDI, a validação estará no requisito de elemento de dados e na validação de comprimento de elemento de dados permitida, enumerações e repetição (mín/máx). |
| Permitir zeros à esquerda/à direita |Manter qualquer zero à esquerda ou à direita e caracteres de espaço adicionais. Não remova esses caracteres. |
| Cortar zeros à esquerda/à direita |Remova os caracteres zero à esquerda ou à direita. |
| Política de separador à direita |Gerar separadores à direita. <p>Selecione **não permitido** para proibir delimitadores e separadores à direita no intercâmbio enviado. Se o intercâmbio tiver delimitadores e separadores à direita, o intercâmbio será declarado como inválido. <p>Selecione **opcional** para enviar intercâmbios com ou sem delimitadores e separadores à direita. <p>Selecione **obrigatório** se o intercâmbio enviado precisar ter delimitadores e separadores à direita. |

## <a name="find-your-created-agreement"></a>Encontre seu contrato criado

1.  Depois de concluir a configuração de todas as propriedades do contrato, na página **Adicionar** , escolha **OK** para concluir a criação do seu contrato e retornar à sua conta de integração.

    Seu contrato recém-adicionado agora aparece na sua lista de **contratos** .

2.  Você também pode exibir seus contratos em sua visão geral da conta de integração. No menu conta de integração, escolha **visão geral**e, em seguida, selecione o bloco **contratos** . 

    ![Escolha o bloco "contratos"](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="view-swagger-file"></a>Exibir arquivo do Swagger
Para exibir os detalhes do Swagger para o conector do EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="learn-more"></a>Saber mais
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  

