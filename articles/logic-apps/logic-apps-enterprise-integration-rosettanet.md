---
title: Mensagens RosettaNet para integração B2B
description: Trocar mensagens RosettaNet em aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792413"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Trocar mensagens RosettaNet para integração de empresas B2B em aplicativos lógicos do Azure

[RosettaNet](https://resources.gs1us.org) é um consórcio sem fins lucrativos que estabeleceu processos padrão para o compartilhamento de informações de negócios. Esses padrões são comumente usados para processos de cadeia de suprimentos e estão generalizados nos setores de semicondutor, eletrônicos e logística. O RosettaNet Consortium cria e mantém os PIPs (processos de interface de parceiro), que fornecem definições de processos empresariais comuns para todas as trocas de mensagens de RosettaNet. O RosettaNet é baseado em XML e define diretrizes de mensagens, interfaces para processos de negócios e estruturas de implementação para comunicação entre empresas.

Nos [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), o conector do RosettaNet ajuda a criar soluções de integração que dão suporte aos padrões do RosettaNet. O conector é baseado na versão 2.0.01 do RNIF (RosettaNet Implementation Framework). O RNIF é uma estrutura de aplicativo de rede aberta que permite aos parceiros de negócios executar RosettaNet PIPs de forma colaborativa. Essa estrutura define a estrutura da mensagem, a necessidade de confirmações, a codificação MIME (Multipurpose Internet Mail Extensions) e a assinatura digital.

Especificamente, o conector fornece estes recursos:

* Codifique ou receba mensagens RosettaNet.
* Decodifique ou envie mensagens RosettaNet.
* Aguarde a resposta e a geração de notificação de falha.

Para esses recursos, o conector dá suporte a todos os PIPs definidos pelo RNIF 2.0.01. A comunicação com o parceiro pode ser síncrona ou assíncrona.

## <a name="rosettanet-concepts"></a>Conceitos de RosettaNet

Aqui estão alguns conceitos e termos que são exclusivos para a especificação RosettaNet e são importantes ao criar integrações baseadas em RosettaNet:

* **PONTOS**

  A organização RosettaNet cria e mantém os PIPs (processos de interface de parceiro), que fornecem definições de processos empresariais comuns para todas as trocas de mensagens RosettaNet. Cada especificação de PIP fornece um arquivo de definição de tipo de documento (DTD) e um documento de orientação de mensagem. O arquivo DTD define a estrutura de mensagens de conteúdo do serviço. O documento de diretrizes de mensagem, que é um arquivo HTML legível, especifica restrições de nível de elemento. Juntos, esses arquivos fornecem uma definição completa do processo de negócios.

   PIPs são categorizados por uma função comercial de alto nível, ou cluster, e uma subfunção, ou segmento. Por exemplo, "3A4" é o PIP para a ordem de compra, enquanto "3" é a função de gerenciamento de pedidos, e "3A" é a subfunção de entrada de pedido & de aspas. Para obter mais informações, consulte o [site RosettaNet](https://resources.gs1us.org).

* **Ação**

  Parte de um PIP, as mensagens de ação são mensagens comerciais trocadas entre parceiros.

* **Aviso**

   Parte de um PIP, mensagens de sinal são confirmações enviadas em resposta a mensagens de ação.

* **Ação única e ação dupla**

  Para um PIP de ação única, a única resposta é uma mensagem de sinal de confirmação. Para um PIP de ação dupla, o iniciador recebe uma mensagem de resposta e responde com uma confirmação além do fluxo de mensagens de ação única.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você ainda não tiver uma assinatura do Azure, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar seu contrato e outros artefatos B2B. Esta conta de integração deve ser associada à sua assinatura do Azure.

* Pelo menos dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) que são definidos em sua conta de integração e configurados com o qualificador "duns" em **identidades de negócios**

* Uma configuração de processo PIP, que é necessária para enviar ou receber mensagens RosettaNet em sua conta de integração. A configuração do processo armazena todas as características de configuração do PIP. Em seguida, você pode fazer referência a essa configuração ao criar um contrato com o parceiro. Para criar uma configuração de processo PIP em sua conta de integração, consulte [Adicionar configuração de processo Pip](#add-pip).

* [Certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md) opcionais para criptografar, descriptografar ou assinar as mensagens que você carrega na conta de integração. Os certificados serão necessários somente se você estiver usando assinatura ou criptografia.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Adicionar configuração de processo PIP

Para adicionar uma configuração de processo PIP à sua conta de integração, siga estas etapas:

1. Na [portal do Azure](https://portal.azure.com), localize e abra sua conta de integração.

1. No painel **visão geral** , selecione o bloco **Pip RosettaNet** .

   ![Escolher bloco do RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. Em **ROSETTANET Pip**, escolha **Adicionar**. Forneça seus detalhes de PIP.

   ![Adicionar detalhes do PIP RosettaNet](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | Seu nome de PIP |
   | **Código PIP** | Sim | O código de três dígitos do PIP. Para obter mais informações, consulte [RosettaNet pips](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Versão do PIP** | Sim | O número de versão do PIP, que está disponível com base no seu código PIP selecionado |
   ||||

   Para obter mais informações sobre essas propriedades de PIP, visite o [site do RosettaNet](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Quando terminar, escolha **OK**, que cria a configuração de Pip.

1. Para exibir ou editar a configuração do processo, selecione o PIP e escolha **Editar como JSON**.

   Todas as definições de configuração do processo são provenientes das especificações do PIP. Os aplicativos lógicos populam a maior parte das configurações com os valores padrão que são os valores mais usados para essas propriedades.

   ![Editar configuração de PIP RosettaNet](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Confirme se as configurações correspondem aos valores na especificação de PIP apropriada e atenda às suas necessidades de negócios. Se necessário, atualize os valores em JSON e salve essas alterações.

## <a name="create-rosettanet-agreement"></a>Criar contrato de RosettaNet

1. No [portal do Azure](https://portal.azure.com), localize e abra sua conta de integração, se ainda não estiver aberta.

1. No painel **visão geral** , selecione o bloco **contratos** .

   ![Escolher bloco de contratos](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. Em **contratos**, escolha **Adicionar**. Forneça os detalhes do seu contrato.

   ![Adicionar detalhes do contrato](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome do contrato |
   | **Tipo de contrato** | Sim | Selecione **RosettaNet**. |
   | **Parceiro de host** | Sim | Um contrato requer um parceiro de host e convidado. O parceiro host representa a organização que configura o contrato. |
   | **Identidade do host** | Sim | Um identificador para o parceiro de host |
   | **Parceiro convidado** | Sim | Um contrato requer um parceiro de host e convidado. O parceiro convidado representa a organização que está fazendo negócios com o parceiro de host. |
   | **Identidade de convidado** | Sim | Um identificador para o parceiro convidado |
   | **Configurações de recebimento** | Varia | Essas propriedades se aplicam a todas as mensagens recebidas pelo parceiro de host |
   | **Configurações de envio** | Varia | Essas propriedades se aplicam a todas as mensagens enviadas pelo parceiro de host |  
   | **Referências de PIP RosettaNet** | Sim | As referências de PIP para o contrato. Todas as mensagens RosettaNet exigem configurações de PIP. |
   ||||

1. Para configurar seu contrato para receber mensagens de entrada do parceiro convidado, selecione **configurações de recebimento**.

   ![Configurações de recebimento](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Para habilitar a assinatura ou criptografia para mensagens de entrada, em **mensagens**, selecione a **mensagem deve ser assinada** ou a **mensagem deve ser criptografada** , respectivamente.

      | Propriedade | Obrigatório | Descrição |
      |----------|----------|-------------|
      | **A mensagem deve ser assinada** | Não | Assinar mensagens de entrada com o certificado selecionado. |
      | **Certificado** | Sim, se a assinatura estiver habilitada | O certificado a ser usado para assinatura |
      | **Habilitar criptografia de mensagem** | Não | Criptografar mensagens de entrada com o certificado selecionado. |
      | **Certificado** | Sim, se a criptografia estiver habilitada | O certificado a ser usado para criptografia |
      ||||

   1. Em cada seleção, selecione o respectivo [certificado](./logic-apps-enterprise-integration-certificates.md), que você adicionou anteriormente à sua conta de integração, para usar para assinatura ou criptografia.

1. Para configurar seu contrato para enviar mensagens para o parceiro convidado, selecione **configurações de envio**.

   ![Configurações de envio](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Para habilitar a assinatura ou criptografia para mensagens de saída, em **mensagens**, selecione **Habilitar assinatura de mensagem** ou **habilitar criptografia de mensagem** , respectivamente. Em cada seleção, selecione o respectivo algoritmo e [certificado](./logic-apps-enterprise-integration-certificates.md), que você adicionou anteriormente à sua conta de integração, para usar para assinatura ou criptografia.

      | Propriedade | Obrigatório | Descrição |
      |----------|----------|-------------|
      | **Habilitar assinatura de mensagem** | Não | Assinar mensagens de saída com o algoritmo de assinatura e o certificado selecionados. |
      | **Algoritmo de assinatura** | Sim, se a assinatura estiver habilitada | O algoritmo de assinatura a ser usado com base no certificado selecionado |
      | **Certificado** | Sim, se a assinatura estiver habilitada | O certificado a ser usado para assinatura |
      | **Habilitar criptografia de mensagem** | Não | Criptografar saída com o algoritmo de criptografia e o certificado selecionados. |
      | **Algoritmo de criptografia** | Sim, se a criptografia estiver habilitada | O algoritmo de criptografia a ser usado com base no certificado selecionado |
      | **Certificado** | Sim, se a criptografia estiver habilitada | O certificado a ser usado para criptografia |
      ||||

   1. Em **pontos de extremidade**, especifique as URLs necessárias a serem usadas para enviar mensagens de ação e confirmações.

      | Propriedade | Obrigatório | Descrição |
      |----------|----------|-------------|
      | **URL da ação** |  Sim | A URL a ser usada para enviar mensagens de ação. A URL é um campo obrigatório para mensagens síncronas e assíncronas. |
      | **URL de confirmação** | Sim | A URL a ser usada para enviar mensagens de confirmação. A URL é um campo obrigatório para mensagens assíncronas. |
      ||||

1. Para configurar seu contrato com as referências de PIP RosettaNet para parceiros, selecione **referências de Pip RosettaNet**. Em **nome do Pip**, selecione o nome para o Pip criado anteriormente.

   ![Referências de PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Sua seleção popula as propriedades restantes, que se baseiam no PIP que você configurou em sua conta de integração. Se necessário, você pode alterar a **função PIP**.

   ![PIP selecionado](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Depois de concluir essas etapas, você estará pronto para enviar ou receber mensagens RosettaNet.

## <a name="rosettanet-templates"></a>Modelos de RosettaNet

Para acelerar o desenvolvimento e recomendar os padrões de integração, você pode usar modelos de aplicativo lógico para decodificar e codificar mensagens RosettaNet. Ao criar um aplicativo lógico, você pode selecionar na Galeria de modelos no designer de aplicativos lógicos. Você também pode encontrar esses modelos no [repositório GitHub para aplicativos lógicos do Azure](https://github.com/Azure/logicapps).

![Modelos de RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Receber ou decodificar mensagens RosettaNet

1. [Crie um aplicativo lógico em branco](quickstart-create-first-logic-app-workflow.md).

1. [Vincule sua conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account) ao seu aplicativo lógico.

1. Antes de adicionar uma ação para decodificar a mensagem RosettaNet, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de solicitação.

1. Depois de adicionar o gatilho, escolha **nova etapa**.

   ![Adicionar gatilho de solicitação](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Na caixa de pesquisa, digite "RosettaNet" e selecione esta ação: **RosettaNet decodificar**

   ![Localizar e selecionar a ação "decodificação RosettaNet"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Forneça as informações para as propriedades da ação:

   ![Fornecer detalhes da ação](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Mensagem** | Sim | A mensagem RosettaNet a ser decodificada  |
   | **Headers** (Cabeçalhos) | Sim | Os cabeçalhos HTTP que fornecem os valores para a versão, que é a versão RNIF, e o tipo de resposta, que indica o tipo de comunicação entre os parceiros e pode ser síncrono ou assíncrono |
   | **Função** | Sim | A função do parceiro de host no PIP |
   ||||

   Da ação de decodificação RosettaNet, a saída, junto com outras propriedades, inclui o **sinal de saída**, que você pode optar por codificar e retornar ao parceiro ou executar qualquer outra ação nessa saída.

## <a name="send-or-encode-rosettanet-messages"></a>Enviar ou codificar mensagens RosettaNet

1. [Crie um aplicativo lógico em branco](quickstart-create-first-logic-app-workflow.md).

1. [Vincule sua conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account) ao seu aplicativo lógico.

1. Antes de adicionar uma ação para codificar a mensagem RosettaNet, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de solicitação.

1. Depois de adicionar o gatilho, escolha **nova etapa**.

   ![Adicionar gatilho de solicitação](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Na caixa de pesquisa, digite "RosettaNet" e selecione esta ação: **RosettaNet codificar**

   ![Localizar e selecionar a ação "codificação RosettaNet"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Forneça as informações para as propriedades da ação:

   ![Fornecer detalhes da ação](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Mensagem** | Sim | A mensagem RosettaNet a ser codificada  |
   | **Parceiro de host** | Sim | O nome do parceiro de host |
   | **Parceiro convidado** | Sim | O nome do parceiro convidado |
   | **Código PIP** | Sim | O código de PIP |
   | **Versão do PIP** | Sim | A versão do PIP |  
   | **Identidade da instância de PIP** | Sim | O identificador exclusivo para esta mensagem PIP |  
   | **Tipo de mensagem** | Sim | O tipo da mensagem a ser codificada |  
   | **Função** | Sim | A função do parceiro de host |
   ||||

   A mensagem codificada agora está pronta para ser enviada ao parceiro.

1. Para enviar a mensagem codificada, este exemplo usa a ação **http** , que é renomeada como "http-enviar mensagem codificada para o parceiro".

   ![Ação HTTP para enviar mensagem RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Por padrões de RosettaNet, as transações de negócios são consideradas completas somente quando todas as etapas definidas pelo PIP são concluídas.

1. Depois que o host envia a mensagem codificada para o parceiro, o host aguarda o sinal e a confirmação. Para realizar essa tarefa, adicione a ação **RosettaNet aguardar resposta** .

   ![Adicionar a ação "espera de RosettaNet de resposta"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   A duração a ser usada para aguardar e o número de novas tentativas baseiam-se na configuração de PIP em sua conta de integração. Se a resposta não for recebida, essa ação gerará uma notificação de falha. Para lidar com novas tentativas, sempre coloque a **codificação** e aguarde as ações de **resposta** em um loop **until** .

   ![Loop Until com ações RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como validar, transformar e outras operações de mensagens com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
