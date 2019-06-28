---
title: Mensagens de RosettaNet para integração empresarial de B2B - Azure Logic Apps
description: Trocar mensagens RosettaNet no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 88e02f3fbbca8007fdf479bb973f50c42a878d6e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332813"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Trocar mensagens RosettaNet para integração empresarial de B2B no Azure Logic Apps 

[RosettaNet](https://resources.gs1us.org) é um consórcio sem fins lucrativos que estabeleceu processos padrão para a partilha de informações comerciais. Esses padrões são frequentemente utilizados para processos de cadeia de fornecimento e são amplamente os setores de semicondutores, eletrônica e logística. O consórcio RosettaNet cria e mantém processos de Interface de parceiro (PIPs), que fornecem definições de processos de negócio comuns para todas as trocas de mensagens de RosettaNet. RosettaNet é baseado em XML e define as diretrizes de mensagem, interfaces para processos de negócios e arquiteturas de implementação para a comunicação entre empresas.

Na [do Azure Logic Apps](../logic-apps/logic-apps-overview.md), o conector de RosettaNet ajuda-o a criar soluções de integração que suportam padrões RosettaNet. O conector é baseado no RosettaNet implementação Framework (RNIF) versão 2.0.01. RNIF é uma estrutura de aplicações de rede aberta que permite que os parceiros de negócios executar em colaboração RosettaNet PIPs. Essa estrutura define a estrutura de mensagem, a necessidade de confirmações, codificação Multipurpose Internet Mail Extensions (MIME) e a assinatura digital.

Especificamente, o conector oferece estas capacidades:

* Codificar ou receber mensagens de RosettaNet.
* Descodificar ou enviar mensagens de RosettaNet.
* Aguarde que a resposta e a geração da notificação de falha.

Para estas capacidades, o conector suporta PIPs todos os que são definidos por RNIF 2.0.01. A comunicação com o parceiro pode ser síncrono ou assíncrono.

## <a name="rosettanet-concepts"></a>Conceitos de RosettaNet

Aqui estão alguns conceitos e termos que são exclusivas para a especificação de RosettaNet e são importante para criar integrações com base em RosettaNet:

* **PIP**

  A organização RosettaNet cria e mantém processos de Interface de parceiro (PIPs), que fornecem definições de processos de negócio comuns para todas as trocas de mensagens de RosettaNet. Cada especificação de PIP fornece um arquivo de definição (DTD) do tipo de documento e um documento de orientação de mensagem. O ficheiro DTD define a estrutura de mensagem de conteúdo de serviço. O documento de orientação de mensagem, que é um arquivo HTML legível por humanos, especifica restrições ao nível do elemento. Juntos, esses arquivos fornecem uma definição completa do processo empresarial.

   PIPs são categorizados por uma função de negócio de alto nível, ou cluster e um subfunction ou segmento. Por exemplo, "3A4" é o PIP para a ordem de compra, enquanto "3" é a função de gestão de ordem e "3A" é o subfunction de cotação de & entrada de pedidos. Para obter mais informações, consulte a [RosettaNet site](https://resources.gs1us.org).

* **ação**

  Parte de um PIP, mensagens de ação são mensagens de negócio que são trocadas entre parceiros.

* **Sinal**

   Parte de um PIP, mensagens de sinal são confirmações que são enviadas em resposta a mensagens de ação.

* **Ação única e a ação dupla**

  De PIP um única ação, a única resposta é uma mensagem de sinal de confirmação. Para um ação de double PIP, o iniciador recebe uma mensagem de resposta e responde com uma confirmação para além do fluxo de mensagens única ação.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [Inscreva-se uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar seu contrato e outros artefactos de B2B. Esta conta de integração tem de estar associada a sua subscrição do Azure.

* Pelo menos dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) que são definidos na sua conta de integração e configurado com o qualificador "DUNS" em **identidades de negócio**

* Uma configuração de processo PIP, que é necessário para enviar ou receber mensagens da RosettaNet, na sua conta de integração. A configuração do processo armazena todas as características de configuração de PIP. Em seguida, pode referenciar esta configuração quando cria um contrato com o parceiro. Para criar uma configuração de processo do PIP na sua conta de integração, consulte [configuração do processo de adicionar o PIP](#add-pip).

* Opcional [certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md) para encriptação, desencriptação ou assinando as mensagens que é carregado para a conta de integração. Os certificados são necessários apenas se for utilizar assinatura ou encriptação.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Adicionar configuração de processo do PIP

Para adicionar uma configuração de processo do PIP para a sua conta de integração, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com), localize e abra a sua conta de integração.

1. Sobre o **descrição geral** painel, selecione a **RosettaNet PIP** mosaico.

   ![Escolha o mosaico RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. Sob **RosettaNet PIP**, escolha **Add**. Forneça os detalhes do PIP.

   ![Adicionar detalhes RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome PIP |
   | **Código PIP** | Sim | O código de três dígitos do PIP. Para obter mais informações, consulte [RosettaNet PIPs](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Versão do PIP** | Sim | O número de versão do PIP, o que está disponível com base no seu código PIP selecionado |
   ||||

   Para obter mais informações sobre estas propriedades PIP, visite o [RosettaNet site](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Quando tiver terminado, escolha **OK**, que cria a configuração do PIP.

1. Para ver ou editar a configuração do processo, selecione o PIP e escolha **editar como JSON**.

   Todos os processam a configuração de definições provenientes de especificações do PIP. O Logic Apps preenche a maioria das configurações com os valores predefinidos que são os valores mais frequentemente utilizados para estas propriedades.

   ![Editar configuração de RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Confirme que as definições correspondem aos valores na especificação de PIP apropriado e atender às necessidades da sua empresa. Se necessário, atualize os valores em JSON e guarde as alterações.

## <a name="create-rosettanet-agreement"></a>Criar RosettaNet contrato

1. Na [portal do Azure](https://portal.azure.com), localize e abra a sua conta de integração, se não estiver já abrir.

1. Sobre o **descrição geral** painel, selecione a **contratos** mosaico.

   ![Escolha o que mosaico de contratos](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. Sob **contratos**, escolha **Add**. Forneça os detalhes do seu contrato.

   ![Adicionar detalhes do contrato](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome do contrato |
   | **Tipo de contrato** | Sim | Selecione **RosettaNet**. |
   | **Parceiro do anfitrião** | Sim | Um contrato exige um anfitrião e convidado parceiro. O parceiro do anfitrião representa a organização que configura o contrato. |
   | **Host Identity** | Sim | Um identificador para o parceiro do anfitrião |
   | **Parceiro convidado** | Sim | Um contrato exige um anfitrião e convidado parceiro. O parceiro convidado representa a organização que está negociando com o parceiro do anfitrião. |
   | **Identidade do convidado** | Sim | Um identificador para o parceiro convidado |
   | **Receber definições** | Varia | Estas propriedades aplicam-se a todas as mensagens recebidas pelo parceiro de anfitrião |
   | **Definições de envio** | Varia | Estas propriedades aplicam-se a todas as mensagens enviadas pelo parceiro de anfitrião |  
   | **Referências de RosettaNet PIP** | Sim | As referências PIP para o contrato. Todas as mensagens de RosettaNet precisam de configurações de PIP. |
   ||||

1. Para configurar seu contrato para o recebimento de mensagens de entrada do parceiro convidado, selecione **receber definições**.

   ![Receber definições](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Para ativar a assinatura ou encriptação de mensagens de entrada, em **mensagens**, selecione **mensagem deve ser assinada** ou **mensagem deve ser encriptada** , respetivamente.

      | Propriedade | Necessário | Descrição |
      |----------|----------|-------------|
      | **Deve ser assinada a mensagem** | Não | Inicie a sessão de mensagens de entrada com o certificado selecionado. |
      | **Certificado** | Sim, se a assinatura está ativada | O certificado a utilizar para iniciar sessão |
      | **Ativar a encriptação de mensagens** | Não | Encripte mensagens de entrada com o certificado selecionado. |
      | **Certificado** | Sim, se a encriptação está ativada | O certificado a utilizar para a encriptação |
      ||||

   1. Em cada seleção, selecione o respetivo [certificado](./logic-apps-enterprise-integration-certificates.md), que foi adicionado anteriormente para a sua conta de integração, a utilizar para a assinatura ou encriptação.

1. Para configurar seu contrato para enviar mensagens para o parceiro convidado, selecione **enviar definições**.

   ![Definições de envio](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Para ativar a assinatura ou encriptação de mensagens de saída, em **mensagens**, selecione **ativar a assinatura de mensagem** ou **ativar a encriptação de mensagens** , respetivamente. Em cada seleção, selecione o algoritmo respetivo e [certificado](./logic-apps-enterprise-integration-certificates.md), que foi adicionado anteriormente para a sua conta de integração, a utilizar para a assinatura ou encriptação.

      | Propriedade | Necessário | Descrição |
      |----------|----------|-------------|
      | **Ativar a assinatura da mensagem** | Não | Inicie a sessão de mensagens de saída com o algoritmo de assinatura selecionado e o certificado. |
      | **Algoritmo de assinatura** | Sim, se a assinatura está ativada | O algoritmo de assinatura para utilizar, com base em certificado selecionado |
      | **Certificado** | Sim, se a assinatura está ativada | O certificado a utilizar para iniciar sessão |
      | **Ativar a encriptação de mensagens** | Não | Encripte com o algoritmo de encriptação selecionado e o certificado de saída. |
      | **Algoritmo de encriptação** | Sim, se a encriptação está ativada | O algoritmo de encriptação a utilizar, com base em certificado selecionado |
      | **Certificado** | Sim, se a encriptação está ativada | O certificado a utilizar para a encriptação |
      ||||

   1. Sob **pontos de extremidade**, especifique os URLs necessários para utilizar para enviar mensagens de ação e confirmações.

      | Propriedade | Necessário | Descrição |
      |----------|----------|-------------|
      | **URL de ação** |  Sim | O URL a utilizar para enviar mensagens de ação. O URL é um campo obrigatório para mensagens síncronas e assíncronas. |
      | **URL de confirmação** | Sim | O URL a utilizar para enviar mensagens de confirmação. O URL é um campo obrigatório para mensagens assíncronas. |
      ||||

1. Para configurar seu contrato com as referências de RosettaNet PIP para parceiros, selecione **RosettaNet PIP referencia**. Sob **PIP nome**, selecione o nome para o PIP criado anteriormente.

   ![Referências PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   A seleção preenche as propriedades restantes, o que se baseiam no PIP que configurou na sua conta de integração. Se necessário, pode alterar o **PIP função**.

   ![PIP selecionado](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Depois de concluir estes passos, está pronto para enviar ou receber mensagens de RosettaNet.

## <a name="rosettanet-templates"></a>RosettaNet modelos

Para acelerar o desenvolvimento e recomendar padrões de integração, pode utilizar modelos de aplicações lógicas para descodificar e codificar RosettaNet mensagens. Quando cria uma aplicação lógica, pode selecionar a partir da galeria do modelo no Estruturador da aplicação lógica. Também pode encontrar estes modelos na [repositório do GitHub para o Azure Logic Apps](https://github.com/Azure/logicapps).

![RosettaNet modelos](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Receber ou decodificar RosettaNet mensagens

1. [Criar uma aplicação lógica em branco](quickstart-create-first-logic-app-workflow.md).

1. [Ligar a sua conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account) à sua aplicação lógica.

1. Antes de poder adicionar uma ação para decodificar a mensagem de RosettaNet, tem de adicionar um acionador para iniciar a sua aplicação lógica, por exemplo, um acionador de pedido.

1. Depois de adicionar o acionador, escolha **novo passo**.

   ![Adicionar acionador de pedido](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Na caixa de pesquisa, introduza "rosettanet" e selecione a ação: **RosettaNet descodificar**

   ![Localize e selecione a ação "RosettaNet decodificar"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Forneça as informações de propriedades da ação:

   ![Fornecer detalhes da ação](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **mensagem** | Sim | A mensagem de RosettaNet a descodificar  |
   | **Headers** (Cabeçalhos) | Sim | Os cabeçalhos HTTP que fornecem os valores para a versão, que é a versão RNIF, e o tipo de resposta, que indica o tipo de comunicação entre os parceiros e pode ser síncrono ou assíncrono |
   | **Função** | Sim | A função de parceiro de anfitrião no PIP |
   ||||

   Da ação de descodificar RosettaNet, a saída, juntamente com outras propriedades, inclui **sinal saída**, que pode optar por codificar e retorne ao parceiro ou executar qualquer outra ação que tipo de saída.

## <a name="send-or-encode-rosettanet-messages"></a>Enviar ou codificar RosettaNet mensagens

1. [Criar uma aplicação lógica em branco](quickstart-create-first-logic-app-workflow.md).

1. [Ligar a sua conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account) à sua aplicação lógica.

1. Antes de poder adicionar uma ação para codificar a mensagem de RosettaNet, tem de adicionar um acionador para iniciar a sua aplicação lógica, por exemplo, um acionador de pedido.

1. Depois de adicionar o acionador, escolha **novo passo**.

   ![Adicionar acionador de pedido](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Na caixa de pesquisa, introduza "rosettanet" e selecione a ação: **RosettaNet codificar**

   ![Localize e selecione a ação "RosettaNet codificar"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Forneça as informações de propriedades da ação:

   ![Fornecer detalhes da ação](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **mensagem** | Sim | A mensagem de RosettaNet para codificar  |
   | **Parceiro do anfitrião** | Sim | O nome do parceiro anfitrião |
   | **Parceiro convidado** | Sim | O nome do parceiro convidado |
   | **Código PIP** | Sim | O código PIP |
   | **Versão do PIP** | Sim | A versão do PIP |  
   | **Identidade de instância de PIP** | Sim | O identificador exclusivo para esta mensagem PIP |  
   | **Tipo de mensagem** | Sim | O tipo da mensagem a codificar |  
   | **Função** | Sim | A função de parceiro de anfitrião |
   ||||

   A mensagem codificada está agora pronta para enviar para o parceiro.

1. Para enviar a mensagem codificada, este exemplo utiliza a **HTTP** ação, o que é mudar o nome "HTTP - mensagem de envio codificado para parceiros".

   ![Ação de HTTP para enviar mensagem de RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Pelos padrões RosettaNet, transações comerciais são consideradas concluídas somente quando todos os passos definidos pelo PIP estão concluídos.

1. Depois do anfitrião envia a mensagem codificada para parceiros, o anfitrião aguarda o sinal e a confirmação. Para realizar esta tarefa, adicione a **RosettaNet aguardar resposta** ação.

   ![Adicionar ação "Wait RosettaNet para resposta a"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   A duração para uso em espera e o número de repetições baseiam-se a configuração de PIP na sua conta de integração. Se a resposta não é recebida, esta ação gera uma notificação de falha. Para processar várias repetições, sempre coloque os **Encode** e **aguardar resposta** ações num **até** loop.

   ![Até que o loop com ações de RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba como validar, transformar e outras operações de mensagem com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
