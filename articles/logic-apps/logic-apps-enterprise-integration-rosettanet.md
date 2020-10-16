---
title: Mensagens RosettaNet para integração B2B
description: Troque mensagens RosettaNet em Azure Logic Apps com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: f02cbdc7ca8822c5fcc91b106856d7f8f547536b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565110"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Trocar mensagens RosettaNet para integração empresarial B2B em Azure Logic Apps

[A RosettaNet](https://resources.gs1us.org) é um consórcio sem fins lucrativos que estabeleceu processos padrão para a partilha de informações de negócios. Estas normas são comumente utilizadas para processos de cadeia de abastecimento e são generalizadas nas indústrias de semicondutores, electrónica e logística. O consórcio RosettaNet cria e mantém processos de interface de parceiros (PIPs), que fornecem definições comuns de processo de negócio para todas as trocas de mensagens RosettaNet. A RosettaNet baseia-se no XML e define as diretrizes de mensagens, interfaces para processos de negócio e quadros de implementação para comunicação entre empresas.

Nas [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)o conector RosettaNet ajuda-o a criar soluções de integração que suportem os padrões RosettaNet. O conector baseia-se na versão 2.0.01 do RosettaNet Implementation Framework (RNIF). O RNIF é um quadro de aplicação de rede aberto que permite aos parceiros de negócio gerir em colaboração os PIPs da RosettaNet. Este quadro define a estrutura da mensagem, a necessidade de reconhecimentos, a codificação de extensões multiusos de correio de Internet (MIME) e a assinatura digital.

Especificamente, o conector fornece estas capacidades:

* Codificar ou receber mensagens RosettaNet.
* Descodificar ou enviar mensagens RosettaNet.
* Aguarde a resposta e geração de Notificação de Falha.

Para estas capacidades, o conector suporta todos os PIPs definidos pelo RNIF 2.0.01. A comunicação com o parceiro pode ser sincronizada ou assíncronia.

## <a name="rosettanet-concepts"></a>Conceitos rosettaNet

Aqui estão alguns conceitos e termos que são exclusivos da especificação RosettaNet e são importantes na construção de integrações baseadas em RosettaNet:

* **PIP**

  A organização RosettaNet cria e mantém processos de interface de parceiros (PIPs), que fornecem definições comuns de processo de negócio para todas as trocas de mensagens RosettaNet. Cada especificação pip fornece um ficheiro de definição de tipo de documento (DTD) e um documento de orientação de mensagem. O ficheiro DTD define a estrutura da mensagem de conteúdo de serviço. O documento de orientação de mensagens, que é um ficheiro HTML legível pelo homem, especifica restrições ao nível dos elementos. Juntos, estes ficheiros fornecem uma definição completa do processo de negócio.

   Os PIPs são categorizados por uma função de negócio de alto nível, ou cluster, e uma subfunção, ou segmento. Por exemplo, "3A4" é o PIP para Ordem de Compra, enquanto "3" é a função de Gestão da Ordem, e "3A" é a subfunção de entrada de ordem & cotação. Para mais informações, consulte o [site da RosettaNet.](https://resources.gs1us.org)

* **Ação**

  Parte de um PIP, as mensagens de ação são mensagens de negócio que são trocadas entre parceiros.

* **Sinal**

   Parte de um PIP, as mensagens de sinal são agradecimentos que são enviados em resposta a mensagens de ação.

* **Ação única e ação dupla**

  Para um PIP de ação única, a única resposta é uma mensagem de sinal de reconhecimento. Para um PIP de ação dupla, o iniciador recebe uma mensagem de resposta e responde com um reconhecimento para além do fluxo de mensagens de ação única.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, [inscreva-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/)

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar o seu acordo e outros artefactos B2B. Esta conta de integração deve estar associada à sua subscrição Azure.

* Pelo menos dois [parceiros definidos](../logic-apps/logic-apps-enterprise-integration-partners.md) na sua conta de integração e configurados com o qualificador "DUNS" em **identidades empresariais**

* Uma configuração de processo PIP, que é necessária para enviar ou receber mensagens RosettaNet, na sua conta de integração. A configuração do processo armazena todas as características de configuração PIP. Em seguida, pode consultar esta configuração quando criar um acordo com o parceiro. Para criar uma configuração de processo PIP na sua conta de integração, consulte [a configuração do processo PIP](#add-pip).

* [Certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md) opcionais para encriptar, desencriptar ou assinar as mensagens que envia para a conta de integração. Os certificados só são necessários se utilizar a assinatura ou encriptação.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Adicionar configuração do processo PIP

Para adicionar uma configuração de processo PIP à sua conta de integração, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua conta de integração.

1. No **painel de visão** geral, selecione o azulejo **PIP RosettaNet.**

   ![Escolha o azulejo rosetaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. Em **RosettaNet PIP,** escolha **Add**. Forneça os seus detalhes do PIP.

   ![Adicione detalhes do PIP rosettaNet](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O seu nome PIP |
   | **Código PIP** | Sim | O código PIP de três dígitos. Para obter mais informações, consulte [os PIPs da RosettaNet](/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Versão PIP** | Sim | O número da versão PIP, que está disponível com base no seu código PIP selecionado |
   ||||

   Para mais informações sobre estas propriedades PIP, visite o site da [RosettaNet.](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)

1. Quando terminar, escolha **OK,** o que cria a configuração PIP.

1. Para ver ou editar a configuração do processo, selecione o PIP e escolha **Editar como JSON**.

   Todas as definições de configuração do processo provêm das especificações do PIP. As Aplicações Lógicas povoam a maioria das configurações com os valores padrão que são os valores mais utilizados para estas propriedades.

   ![Editar configuração PIP RosettaNet](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Confirme que as definições correspondem aos valores na especificação PIP apropriada e atende às necessidades do seu negócio. Se necessário, atualize os valores em JSON e guarde essas alterações.

## <a name="create-rosettanet-agreement"></a>Criar acordo RosettaNet

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua conta de integração, se não já estiver aberta.

1. No **painel de visão** geral, selecione o azulejo **dos Acordos.**

   ![Escolha os Acordos em azulejo](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. Nos **Termos de Acordos**, escolha **Adicionar**. Forneça os detalhes do seu acordo.

   ![Adicionar detalhes do acordo](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome do acordo |
   | **Tipo de acordo** | Sim | Selecione **RosettaNet**. |
   | **Parceiro anfitrião** | Sim | Um acordo requer tanto um anfitrião como um parceiro convidado. O parceiro anfitrião representa a organização que configura o acordo. |
   | **Identidade do anfitrião** | Sim | Um identificador para o parceiro anfitrião |
   | **Parceiro Convidado** | Sim | Um acordo requer tanto um anfitrião como um parceiro convidado. O parceiro convidado representa a organização que está a fazer negócios com o parceiro anfitrião. |
   | **Identidade do Hóspede** | Sim | Um identificador para o parceiro convidado |
   | **Receber Definições** | Varia | Estas propriedades aplicam-se a todas as mensagens recebidas pelo parceiro anfitrião |
   | **Enviar Definições** | Varia | Estas propriedades aplicam-se a todas as mensagens enviadas pelo parceiro anfitrião |  
   | **Referências PIP rosettaNet** | Sim | As referências do PIP para o acordo. Todas as mensagens RosettaNet requerem configurações PIP. |
   ||||

1. Para configurar o seu acordo para receber mensagens recebidas do parceiro convidado, selecione **'Receber Definições'.**

   ![Receber definições](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Para ativar a sessão ou encriptação para mensagens **recebidas,** em Mensagens , selecione **Mensagem deve ser assinada** ou a **Mensagem deve ser encriptada** respectivamente.

      | Propriedade | Necessário | Descrição |
      |----------|----------|-------------|
      | **Mensagem deve ser assinada** | Não | Assine mensagens recebidas com o certificado selecionado. |
      | **Certificado** | Sim, se a assinatura estiver ativada | O certificado a utilizar para a assinatura |
      | **Ativar encriptação de mensagens** | Não | Criptografe as mensagens recebidas com o certificado selecionado. |
      | **Certificado** | Sim, se a encriptação estiver ativada | O certificado a usar para encriptação |
      ||||

   1. Em cada seleção, selecione o respetivo [certificado](./logic-apps-enterprise-integration-certificates.md), que anteriormente adicionou à sua conta de integração, para utilizar para a assinatura ou encriptação.

1. Para configurar o seu acordo de envio de mensagens para o parceiro convidado, selecione **Enviar Definições**.

   ![Enviar configurações](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Para ativar a assinatura ou encriptação de mensagens de saída, em **Mensagens**, selecione **Ativar a assinatura de mensagens** ou **Ative a encriptação da mensagem,** respectivamente. Em cada seleção, selecione o respetivo algoritmo e [certificado](./logic-apps-enterprise-integration-certificates.md), que anteriormente adicionou à sua conta de integração, para utilizar para a assinatura ou encriptação.

      | Propriedade | Necessário | Descrição |
      |----------|----------|-------------|
      | **Ativar a assinatura de mensagens** | Não | Assine mensagens de saída com o algoritmo de assinatura selecionado e certificado. |
      | **Algoritmo de assinatura** | Sim, se a assinatura estiver ativada | O algoritmo de assinatura a utilizar, com base no certificado selecionado |
      | **Certificado** | Sim, se a assinatura estiver ativada | O certificado a utilizar para a assinatura |
      | **Ativar encriptação de mensagens** | Não | Criptografe a saída com o algoritmo de encriptação selecionado e certificado. |
      | **Algoritmo de encriptação** | Sim, se a encriptação estiver ativada | O algoritmo de encriptação a utilizar, com base no certificado selecionado |
      | **Certificado** | Sim, se a encriptação estiver ativada | O certificado a usar para encriptação |
      ||||

   1. Nos **pontos finais,** especifique os URLs necessários para o envio de mensagens de ação e agradecimentos.

      | Propriedade | Necessário | Descrição |
      |----------|----------|-------------|
      | **URL de ação** |  Sim | O URL a utilizar para o envio de mensagens de ação. O URL é um campo necessário para mensagens sincronizadas e assíncronos. |
      | **URL de reconhecimento** | Sim | O URL a utilizar para o envio de mensagens de reconhecimento. O URL é um campo necessário para mensagens assíncronos. |
      ||||

1. Para estabelecer o seu acordo com as referências PIP rosettaNet para parceiros, selecione **referências PIP RosettaNet**. Em **Nome PIP,** selecione o nome do pip anteriormente criado.

   ![Referências PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   A sua seleção povoa as propriedades restantes, que são baseadas no PIP que criou na sua conta de integração. Se necessário, pode alterar a **Função PIP**.

   ![PIP selecionado](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Depois de completar estes passos, está pronto para enviar ou receber mensagens RosettaNet.

## <a name="rosettanet-templates"></a>Modelos de RosettaNet

Para acelerar o desenvolvimento e recomendar padrões de integração, pode utilizar modelos de aplicações lógicas para descodição e codificação de mensagens RosettaNet. Quando criar uma aplicação lógica, pode selecionar a partir da galeria de modelos no Logic App Designer. Também pode encontrar estes modelos no [repositório GitHub para apps Azure Logic.](https://github.com/Azure/logicapps)

![Modelos de RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Receber ou descodificar mensagens RosettaNet

1. [Crie uma aplicação lógica em branco.](quickstart-create-first-logic-app-workflow.md)

1. [Ligue a sua conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account) à sua aplicação lógica.

1. Antes de poder adicionar uma ação para descodificar a mensagem RosettaNet, tem de adicionar um gatilho para iniciar a sua aplicação lógica, como um gatilho Request.

1. Depois de adicionar o gatilho, escolha **Novo passo**.

   ![Adicionar gatilho de pedido](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Na caixa de pesquisa, insira "rosetanet", e selecione esta ação: **RosettaNet Decode**

   ![Localizar e selecionar ação "RosettaNet Decode"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Forneça as informações sobre as propriedades da ação:

   ![Screenshot que mostra onde você fornece as informações para as propriedades da ação.](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Mensagem** | Sim | A mensagem RosettaNet para descodificar  |
   | **Cabeçalhos** | Sim | Os cabeçalhos HTTP que fornecem os valores para a versão, que é a versão RNIF, e o tipo de resposta, que indica o tipo de comunicação entre os parceiros e pode ser sincronizado ou assíncronos |
   | **Role** | Sim | O papel do parceiro anfitrião no PIP |
   ||||

   A partir da ação RosettaNet Decode, a saída, juntamente com outras propriedades, inclui **o sinal de saída,** que pode optar por codificar e voltar ao parceiro, ou tomar qualquer outra ação nessa saída.

## <a name="send-or-encode-rosettanet-messages"></a>Enviar ou codificar mensagens RosettaNet

1. [Crie uma aplicação lógica em branco.](quickstart-create-first-logic-app-workflow.md)

1. [Ligue a sua conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account) à sua aplicação lógica.

1. Antes de poder adicionar uma ação para codificar a mensagem RosettaNet, tem de adicionar um gatilho para iniciar a sua aplicação lógica, como um gatilho Request.

1. Depois de adicionar o gatilho, escolha **Novo passo**.

   ![Adicionar gatilho de pedido](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Na caixa de pesquisa, insira "rosetanet", e selecione esta ação: **RosettaNet Encode**

   ![Localizar e selecionar ação "RosettaNet Encode"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Forneça as informações sobre as propriedades da ação:

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
   | **Role** | Sim | O papel do parceiro anfitrião |
   ||||

   A mensagem codificada está agora pronta para enviar ao parceiro.

1. Para enviar a mensagem codificada, este exemplo utiliza a ação **HTTP,** que é renomeada "HTTP - Enviar mensagem codificada para parceiro".

   ![Ação HTTP para envio de mensagem RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   De acordo com as normas RosettaNet, as transações comerciais só são consideradas completas quando todos os passos definidos pelo PIP estiverem completos.

1. Depois de o anfitrião enviar a mensagem codificada para o parceiro, o anfitrião aguarda o sinal e o reconhecimento. Para realizar esta tarefa, adicione a **RosettaNet esperar pela** ação de resposta.

   ![Adicione a ação "RosettaNet wait for response"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   A duração a utilizar para espera e o número de retréis baseiam-se na configuração PIP na sua conta de integração. Se a resposta não for recebida, esta ação gera uma Notificação de Falha. Para lidar com as retrações, coloque sempre o **Código** e **aguarde** as ações de resposta num loop **Until.**

   ![Até loop com as ações rosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como validar, transformar e outras operações de mensagens com o [Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
