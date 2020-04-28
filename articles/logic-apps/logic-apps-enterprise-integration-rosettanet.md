---
title: Mensagens RosettaNet para integração B2B
description: Trocar mensagens RosetaNet em Aplicativos Lógica Saquecom Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74792413"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Troca de mensagens RosetaNet para integração empresarial B2B em Aplicações Lógicas Azure

[A RosettaNet](https://resources.gs1us.org) é um consórcio sem fins lucrativos que estabeleceu processos padrão para a partilha de informações de negócios. Estas normas são geralmente utilizadas para processos de cadeia de abastecimento e são generalizadas nas indústrias de semicondutores, electrónica e logística. O consórcio RosettaNet cria e mantém processos de interface de parceiro (PIPs), que fornecem definições comuns de processo de negócio para todas as trocas de mensagens RosettaNet. A RosettaNet baseia-se no XML e define diretrizes de mensagens, interfaces para processos de negócio e quadros de implementação para comunicação entre empresas.

Nas [Aplicações Lógicas Azure,](../logic-apps/logic-apps-overview.md)o conector RosettaNet ajuda-o a criar soluções de integração que suportam os padrões RosettaNet. O conector baseia-se na versão 2.0.01 do Quadro de Implementação da RosetaNet (RNIF). O RNIF é um quadro aberto de aplicação de rede que permite aos parceiros de negócio sondar colaborativamente piPs RosettaNet. Este quadro define a estrutura da mensagem, a necessidade de reconhecimentos, a codificação multiusos de correio de Internet (MIME) e a assinatura digital.

Especificamente, o conector fornece estas capacidades:

* Enfique ou receba mensagens RosettaNet.
* Descodificar ou enviar mensagens Da RosetaNet.
* Aguarde a resposta e geração de Notificação de Fracasso.

Para estas capacidades, o conector suporta todos os PIPs definidos por RNIF 2.0.01. A comunicação com o parceiro pode ser sincronizada ou assíncrona.

## <a name="rosettanet-concepts"></a>Conceitos RosettaNet

Aqui estão alguns conceitos e termos que são exclusivos da especificação RosettaNet e são importantes na construção de integrações baseadas em RosetaNet:

* **PIP**

  A organização RosettaNet cria e mantém processos de interface de parceiro (PIPs), que fornecem definições comuns de processo de negócio para todas as trocas de mensagens RosettaNet. Cada especificação PIP fornece um ficheiro de definição de tipo de documento (DTD) e um documento de orientação de mensagens. O ficheiro DTD define a estrutura da mensagem de conteúdo de serviço. O documento de orientação de mensagens, que é um ficheiro HTML legível pelo homem, especifica restrições ao nível do elemento. Juntos, estes ficheiros fornecem uma definição completa do processo de negócio.

   Os PIPs são categorizados por uma função de negócio de alto nível, ou cluster, e uma subfunção, ou segmento. Por exemplo, "3A4" é o PIP para Encomenda de Compra, enquanto "3" é a função de Gestão de Encomendas, e "3A" é a subfunção de Entrada de Orçamento & Ordem. Para mais informações, consulte o [site da RosetaNet](https://resources.gs1us.org).

* **Ação**

  Parte de um PIP, as mensagens de ação são mensagens de negócio que são trocadas entre parceiros.

* **Sinal**

   Parte de um PIP, as mensagens de sinal são reconhecimentos que são enviados em resposta a mensagens de ação.

* **Ação única e dupla ação**

  Para um PIP de ação única, a única resposta é uma mensagem de sinal de reconhecimento. Para um PIP de dupla ação, o iniciador recebe uma mensagem de resposta e responde com um reconhecimento para além do fluxo de mensagem de ação única.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma assinatura Azure, [inscreva-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar o seu acordo e outros artefactos B2B. Esta conta de integração deve ser associada à sua subscrição Azure.

* Pelo menos dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) que são definidos na sua conta de integração e configurados com o apuramento "DUNS" ao abrigo de **Identidades Empresariais**

* Uma configuração de processo PIP, que é necessária para enviar ou receber mensagens RosettaNet, na sua conta de integração. A configuração do processo armazena todas as características de configuração PIP. Em seguida, pode referenciar esta configuração quando criar um acordo com o parceiro. Para criar uma configuração de processo PIP na sua conta de integração, consulte a [configuração](#add-pip)do processo Add PIP .

* Certificados [opcionais](../logic-apps/logic-apps-enterprise-integration-certificates.md) para encriptar, desencriptar ou assinar as mensagens que envia para a conta de integração. Os certificados só são necessários se utilizar a assinatura ou a encriptação.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Adicionar configuração do processo PIP

Para adicionar uma configuração de processo PIP à sua conta de integração, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua conta de integração.

1. No painel de **visão geral,** selecione o azulejo **ROSETTANet PIP.**

   ![Escolha azulejo RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. Em **RosettaNet PIP,** escolha **Adicionar**. Forneça os seus detalhes do PIP.

   ![Adicione detalhes do PIP da RosettaNet](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O seu nome PIP |
   | **Código PIP** | Sim | O código pip de três dígitos. Para mais informações, consulte [RosettaNet PIPs](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Versão PIP** | Sim | O número da versão PIP, que está disponível com base no seu código PIP selecionado |
   ||||

   Para mais informações sobre estas propriedades PIP, visite o site da [RosettaNet.](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)

1. Quando terminar, escolha **OK**, o que cria a configuração PIP.

1. Para visualizar ou editar a configuração do processo, selecione o PIP e escolha **editar como JSON**.

   Todas as definições de configuração do processo provêm das especificações do PIP. As Aplicações Lógicas povoam a maioria das definições com os valores padrão que são os valores mais utilizados para estas propriedades.

   ![Editar configuração do PIP da RosettaNet](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Confirme que as definições correspondem aos valores na especificação PIP apropriada e satisfaçam as suas necessidades de negócio. Se necessário, atualize os valores em JSON e guarde essas alterações.

## <a name="create-rosettanet-agreement"></a>Criar acordo RosettaNet

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua conta de integração, se ainda não estiver aberta.

1. No painel de **visão geral,** selecione o azulejo dos **Acordos.**

   ![Escolha o azulejo dos Acordos](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. Nos **termos de Acordos,** escolha **Adicionar**. Forneça os detalhes do seu acordo.

   ![Adicionar detalhes do acordo](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome do acordo |
   | **Tipo de acordo** | Sim | Selecione **RosettaNet**. |
   | **Parceiro anfitrião** | Sim | Um acordo requer um anfitrião e um parceiro convidado. O parceiro anfitrião representa a organização que configura o acordo. |
   | **Identidade de acolhimento** | Sim | Um identificador para o parceiro anfitrião |
   | **Parceiro Convidado** | Sim | Um acordo requer um anfitrião e um parceiro convidado. O parceiro convidado representa a organização que está a fazer negócios com o parceiro anfitrião. |
   | **Identidade de Hóspede** | Sim | Um identificador para o parceiro convidado |
   | **Receber Definições** | Varia | Estas propriedades aplicam-se a todas as mensagens recebidas pelo parceiro anfitrião |
   | **Enviar Definições** | Varia | Estas propriedades aplicam-se a todas as mensagens enviadas pelo parceiro anfitrião |  
   | **Referências do PIP da RosettaNet** | Sim | As referências do PIP para o acordo. Todas as mensagens RosettaNet requerem configurações PIP. |
   ||||

1. Para configurar o seu acordo para receber mensagens de entrada do parceiro convidado, selecione **'Receber Definições'**.

   ![Receber configurações](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Para ativar a assinatura ou encriptação para mensagens de entrada, em **Mensagens,** a mensagem selecionada **deve ser assinada** ou a Mensagem deve ser **encriptada** respectivamente.

      | Propriedade | Necessário | Descrição |
      |----------|----------|-------------|
      | **A mensagem deve ser assinada** | Não | Assine as mensagens de entrada com o certificado selecionado. |
      | **Certificado** | Sim, se a assinatura estiver ativada | O certificado a utilizar para a assinatura |
      | **Ativar encriptação de mensagem** | Não | Criptografe as mensagens de entrada com o certificado selecionado. |
      | **Certificado** | Sim, se a encriptação estiver ativada | O certificado a utilizar para encriptação |
      ||||

   1. Em cada seleção, selecione o respetivo [certificado](./logic-apps-enterprise-integration-certificates.md), que anteriormente adicionou à sua conta de integração, para utilizar para a assinatura ou encriptação.

1. Para configurar o seu acordo para enviar mensagens ao parceiro convidado, selecione **'Definições de Envio**' .

   ![Enviar definições](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Para ativar a assinatura ou encriptação para mensagens de saída, em **Mensagens,** selecione ativar a assinatura de **mensagens** ou **ativar a encriptação** da mensagem respectivamente. Em cada seleção, selecione o respetivo algoritmo e [certificado](./logic-apps-enterprise-integration-certificates.md), que adicionou anteriormente à sua conta de integração, para usar para assinar ou encriptar.

      | Propriedade | Necessário | Descrição |
      |----------|----------|-------------|
      | **Ativar a assinatura de mensagens** | Não | Assine mensagens de saída com o algoritmo de assinatura selecionado e certificado. |
      | **Algoritmo de assinatura** | Sim, se a assinatura estiver ativada | O algoritmo de assinatura a utilizar, com base no certificado selecionado |
      | **Certificado** | Sim, se a assinatura estiver ativada | O certificado a utilizar para a assinatura |
      | **Ativar encriptação de mensagem** | Não | Criptografe a saída com o algoritmo de encriptação e certificado selecionados. |
      | **Algoritmo de encriptação** | Sim, se a encriptação estiver ativada | O algoritmo de encriptação a utilizar, com base no certificado selecionado |
      | **Certificado** | Sim, se a encriptação estiver ativada | O certificado a utilizar para encriptação |
      ||||

   1. Em **pontos finais,** especifique os URLs necessários para o envio de mensagens de ação e reconhecimentos.

      | Propriedade | Necessário | Descrição |
      |----------|----------|-------------|
      | **URL de ação** |  Sim | O URL a utilizar para enviar mensagens de ação. O URL é um campo necessário para mensagens sincronizadas e assíncronas. |
      | **URL de reconhecimento** | Sim | O URL a utilizar para enviar mensagens de reconhecimento. O URL é um campo necessário para mensagens assíncronas. |
      ||||

1. Para estabelecer o seu acordo com as referências rosettaNet PIP para parceiros, selecione **referências RosettaNet PIP**. Em **nome PIP,** selecione o nome para o seu PIP previamente criado.

   ![Referências PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   A sua seleção preenche as propriedades restantes, que são baseadas no PIP que configura na sua conta de integração. Se necessário, pode alterar a **função PIP**.

   ![PIP selecionado](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Depois de completar estes passos, está pronto para enviar ou receber mensagens RosettaNet.

## <a name="rosettanet-templates"></a>Modelos RosettaNet

Para acelerar o desenvolvimento e recomendar padrões de integração, pode usar modelos de aplicativos lógicos para descodificar e codificar mensagens RosettaNet. Quando cria uma aplicação lógica, pode selecionar a partir da galeria de modelos no Logic App Designer. Também pode encontrar estes modelos no [repositório GitHub para Aplicações Lógicas Azure](https://github.com/Azure/logicapps).

![Modelos RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Receber ou descodificar mensagens RosettaNet

1. [Criar uma aplicação lógica em branco.](quickstart-create-first-logic-app-workflow.md)

1. [Ligue a sua conta](logic-apps-enterprise-integration-create-integration-account.md#link-account) de integração à sua aplicação lógica.

1. Antes de poder adicionar uma ação para descodificar a mensagem RosettaNet, tem de adicionar um gatilho para iniciar a sua aplicação lógica, como um gatilho de Pedido.

1. Depois de adicionar o gatilho, escolha **novo passo**.

   ![Adicionar gatilho de pedido](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Na caixa de pesquisa, insira "rosettanet", e selecione esta ação: **RosettaNet Decode**

   ![Localizar e selecionar ação "RosettaNet Decode"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Forneça as informações sobre os imóveis da ação:

   ![Fornecer detalhes de ação](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Mensagem** | Sim | A mensagem RosettaNet para descodificar  |
   | **Cabeçalhos** | Sim | Os cabeçalhos HTTP que fornecem os valores para a versão, que é a versão RNIF, e o tipo de resposta, que indica o tipo de comunicação entre os parceiros e pode ser sincronizado ou assíncrono |
   | **Função** | Sim | O papel do parceiro anfitrião no PIP |
   ||||

   A partir da ação RosettaNet Decode, a saída, juntamente com outras propriedades, inclui **o sinal outbound**, que pode optar por codificar e devolver ao parceiro, ou tomar qualquer outra ação nessa saída.

## <a name="send-or-encode-rosettanet-messages"></a>Enviar ou codificar mensagens RosettaNet

1. [Criar uma aplicação lógica em branco.](quickstart-create-first-logic-app-workflow.md)

1. [Ligue a sua conta](logic-apps-enterprise-integration-create-integration-account.md#link-account) de integração à sua aplicação lógica.

1. Antes de poder adicionar uma ação para codificar a mensagem RosettaNet, tem de adicionar um gatilho para iniciar a sua aplicação lógica, como um gatilho de Pedido.

1. Depois de adicionar o gatilho, escolha **novo passo**.

   ![Adicionar gatilho de pedido](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Na caixa de pesquisa, insira "rosettanet", e selecione esta ação: **RosettaNet Encode**

   ![Localizar e selecionar ação "RosettaNet Encode"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Forneça as informações sobre os imóveis da ação:

   ![Fornecer detalhes de ação](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Mensagem** | Sim | A mensagem RosettaNet para codificar  |
   | **Parceiro anfitrião** | Sim | O nome do parceiro anfitrião |
   | **Parceiro convidado** | Sim | O nome do parceiro convidado |
   | **Código PIP** | Sim | O código PIP |
   | **Versão PIP** | Sim | A versão PIP |  
   | **Identidade de instância PIP** | Sim | O identificador único para esta mensagem PIP |  
   | **Tipo de mensagem** | Sim | O tipo de mensagem para codificar |  
   | **Função** | Sim | O papel do parceiro anfitrião |
   ||||

   A mensagem codificada está agora pronta para enviar ao parceiro.

1. Para enviar a mensagem codificada, este exemplo utiliza a ação **HTTP,** que é renomeada "HTTP - Enviar mensagem codificada ao parceiro".

   ![HTTP ação para enviar mensagem RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   De acordo com as normas RosettaNet, as transações comerciais só são consideradas completas quando todos os passos definidos pelo PIP estiverem completos.

1. Depois de o anfitrião enviar a mensagem codificada ao parceiro, o anfitrião aguarda o sinal e o reconhecimento. Para realizar esta tarefa, adicione a **RosettaNet esperar por** ação de resposta.

   ![Adicione a ação "RosettaNet wait for response"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   A duração a utilizar para a espera e o número de repetições baseiam-se na configuração do PIP na sua conta de integração. Se a resposta não for recebida, esta ação gera uma Notificação de Falha. Para lidar com as tentativas, coloque sempre o **Código de Código** e **aguarde as** ações de resposta num loop **Até.**

   ![Até dar a volta às ações da RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como validar, transformar e outras operações de mensagens com o [Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
