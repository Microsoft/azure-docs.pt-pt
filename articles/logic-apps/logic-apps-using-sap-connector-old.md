---
title: Conectar-se a sistemas SAP-aplicativos lógicos do Azure | Microsoft Docs
description: Como acessar e gerenciar recursos do SAP automatizando fluxos de trabalho com aplicativos lógicos do Azure
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 31f6a3fc281b8dc309ddcd237246c870c85ae20b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971634"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Conectar-se a sistemas SAP de aplicativos lógicos do Azure

> [!NOTE]
> Este conector SAP está agendado para substituição. Use ou migre para o [conector SAP mais recente e avançado](./logic-apps-using-sap-connector.md). 

Este artigo mostra como você pode acessar seus recursos SAP de dentro de um aplicativo lógico usando o servidor de aplicativos SAP e os conectores do servidor de mensagens SAP. Dessa forma, você pode automatizar tarefas, processos e fluxos de trabalho que gerenciam seus dados e recursos do SAP criando aplicativos lógicos.

Este exemplo usa um aplicativo lógico que você pode disparar com uma solicitação HTTP. O aplicativo lógico envia um documento intermediário (IDoc) para um servidor SAP e retorna uma resposta para o solicitante que chamou o aplicativo lógico.
Os conectores atuais do SAP têm ações, mas não gatilhos, portanto, este exemplo usa o [gatilho de solicitação HTTP](../connectors/connectors-native-reqres.md) como a primeira etapa no fluxo de trabalho do aplicativo lógico. Para obter informações técnicas específicas do conector SAP, consulte estes artigos de referência: 

* <a href="https://docs.microsoft.com/connectors/sap" target="blank">Conector do servidor de aplicativos SAP</a>
* <a href="https://docs.microsoft.com/connectors/sap/#send-message-to-sap" target="blank">Conector do servidor de mensagens SAP</a>

Se você ainda não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">Inscreva-se para obter uma conta gratuita do Azure</a>.

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar este artigo, você precisará destes itens:

* O aplicativo lógico do qual você deseja acessar seu sistema SAP e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. Atualmente, os conectores SAP fornecem apenas ações. Se você for novo em aplicativos lógicos, examine [o que é o](../logic-apps/logic-apps-overview.md) início [rápido e aplicativos lógicos do Azure: Crie seu primeiro aplicativo](../logic-apps/quickstart-create-first-logic-app-workflow.md)lógico.

* <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">Servidor de aplicativos SAP</a> ou <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">servidor de mensagens SAP</a>

* Baixe e instale o [Gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127) mais recente em qualquer computador local. Certifique-se de configurar seu gateway na portal do Azure antes de continuar. O gateway ajuda você a acessar dados e recursos com segurança no local. Para obter mais informações, consulte [instalar o gateway de dados local para aplicativos lógicos do Azure](../logic-apps/logic-apps-gateway-install.md).

* Baixe e instale a biblioteca de cliente SAP mais recente, que é atualmente o <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">NCo (conector SAP) 3.0.20.0 para Microsoft .NET Framework 4,0 e Windows de 64 bits (x64)</a>, no mesmo computador que o gateway de dados local. Instale esta versão ou posterior por estes motivos:

  * Versões anteriores do SAP NCo podem ser bloqueadas quando mais de uma mensagem de IDoc são enviadas ao mesmo tempo. 
  Essa condição bloqueia todas as mensagens posteriores enviadas ao destino SAP, fazendo com que as mensagens expirem o tempo limite.

  * O gateway de dados local é executado somente em sistemas de 64 bits. 
  Caso contrário, você obterá um erro de "imagem inadequada" porque o serviço de host do gateway de dados não dá suporte a assemblies de 32 bits.

  * O serviço de host do gateway de dados e o adaptador SAP da Microsoft usam .NET Framework 4,5. O SAP NCo para .NET Framework 4,0 funciona com processos que usam o .NET Runtime 4,0 a 4.7.1. 
  O SAP NCo para .NET Framework 2,0 funciona com processos que usam o tempo de execução do .NET 2,0 para 3,5 e não funciona mais com o gateway de dados local mais recente.

* O conteúdo da mensagem que você pode enviar para o servidor SAP, como um arquivo IDoc de exemplo. Esse conteúdo deve estar no formato XML e incluir o namespace para a ação SAP que você deseja usar.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Adicionar gatilho de solicitação HTTP

Em aplicativos lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico e começa a executar o fluxo de trabalho do aplicativo.

Neste exemplo, você cria um aplicativo lógico com um ponto de extremidade no Azure para que você possa enviar *solicitações HTTP post* para seu aplicativo lógico. Quando seu aplicativo lógico recebe essas solicitações HTTP, o gatilho é acionado e executa a próxima etapa no fluxo de trabalho.

1. No portal do Azure, crie um aplicativo lógico em branco, que abre o designer do aplicativo lógico. 

2. Na caixa de pesquisa, digite "solicitação HTTP" como filtro. Na lista de gatilhos, selecione este gatilho: **Solicitação-quando uma solicitação HTTP é recebida**

   ![Adicionar gatilho de solicitação HTTP](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Agora, salve seu aplicativo lógico para que você possa gerar uma URL de ponto de extremidade para seu aplicativo lógico.
Na barra de ferramentas do estruturador, escolha **Guardar**. 

   A URL do ponto de extremidade agora aparece em seu gatilho, por exemplo:

   ![Gerar URL para ponto de extremidade](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Adicionar ação SAP

Em aplicativos lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa no fluxo de trabalho que segue um gatilho ou outra ação. Se você ainda não adicionou um gatilho ao seu aplicativo lógico e deseja seguir este exemplo, [adicione o gatilho descrito nesta seção](#add-trigger).

1. No designer do aplicativo lógico, no gatilho, escolha **nova etapa** > **Adicionar uma ação**.

   ![Adicionar uma ação](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. Na caixa de pesquisa, digite "servidor SAP" como filtro. Na lista ações, selecione a ação para o servidor SAP: 

   * **Servidor de aplicativos SAP – enviar para SAP**
   * **Servidor de mensagens SAP – enviar para SAP**

   Este exemplo usa esta ação: **Servidor de aplicativos SAP – enviar para SAP**

   ![Selecione "servidor de aplicativos SAP" ou "servidor de mensagens SAP"](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Se você for solicitado a fornecer detalhes de conexão, crie sua conexão SAP agora. Caso contrário, se a conexão já existir, continue com a próxima etapa para que você possa configurar sua ação SAP. 

   **Criar conexão SAP local**

   1. Para **gateways**, selecione **conectar por meio do gateway de dados local** para que as propriedades de conexão locais sejam exibidas.

   2. Forneça as informações de conexão para o servidor SAP. 
   Para a propriedade **Gateway** , selecione o gateway de dados que você criou no portal do Azure para a instalação do gateway, por exemplo:

      **Servidor de aplicativos SAP**

      ![Criar conexão do servidor de aplicativos SAP](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **Servidor de mensagens SAP**

      ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Quando tiver terminado, escolha **Create** (Criar).

      Os aplicativos lógicos configuram e testam sua conexão, certificando-se de que a conexão funcione corretamente.

4. Agora, encontre e selecione uma ação no servidor SAP. 

   1. Na caixa **ação SAP** , escolha o ícone de pasta. 
   Na lista de pastas, localize e selecione a ação que deseja usar. 

      Este exemplo seleciona a categoria **IDOC** para a ação IDOC. 

      ![Localizar e selecionar a ação IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Se você não conseguir localizar a ação desejada, poderá inserir manualmente um caminho, por exemplo:

      ![Forneça manualmente o caminho para a ação IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Para obter mais informações sobre operações de IDoc, consulte [esquemas de mensagem para operações de IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Clique dentro da caixa de **mensagem de entrada** para que a lista de conteúdo dinâmico seja exibida. 
   Nessa lista, em **quando uma solicitação HTTP é recebida**, selecione o campo **corpo** . 

      Esta etapa inclui o conteúdo do corpo do gatilho de solicitação HTTP e envia essa saída para o servidor SAP.

      ![Selecione o campo "corpo"](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Quando terminar, sua ação SAP será parecida com este exemplo:

      ![Ação completa do SAP](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Adicionar ação de resposta HTTP

Agora, adicione uma ação de resposta ao fluxo de trabalho do aplicativo lógico e inclua a saída da ação SAP. Dessa forma, seu aplicativo lógico retorna os resultados do seu servidor SAP para o solicitante original. 

1. No designer de aplicativo lógico, na ação SAP, escolha **nova etapa** > **Adicionar uma ação**.

2. Na caixa de pesquisa, digite "resposta" como filtro. Na lista ações, selecione esta ação: **Solicitação-resposta**

3. Clique dentro da caixa **corpo** para que a lista de conteúdo dinâmico seja exibida. Nessa lista, em **Enviar para SAP**, selecione o campo **corpo** . 

   ![Ação completa do SAP](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Guarde a aplicação lógica. 

## <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

1. Se seu aplicativo lógico ainda não estiver habilitado, no menu do aplicativo lógico, escolha **visão geral**. Na barra de ferramentas, escolha **habilitar**. 

2. Na barra de ferramentas do designer do aplicativo lógico, escolha **executar**. Essa etapa inicia manualmente o aplicativo lógico.

3. Dispare seu aplicativo lógico enviando uma solicitação HTTP POST para a URL em seu gatilho de solicitação HTTP e inclua o conteúdo da mensagem com sua solicitação. Para enviar a solicitação, você pode usar uma ferramenta como o [postmaster](https://www.getpostman.com/apps). 

   Para este artigo, a solicitação envia um arquivo IDoc, que deve estar no formato XML e incluir o namespace para a ação SAP que você está usando, por exemplo: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Depois de enviar sua solicitação HTTP, aguarde a resposta do seu aplicativo lógico.

> [!NOTE]
> Seu aplicativo lógico pode atingir o tempo limite se todas as etapas necessárias para a resposta não forem concluídas dentro do tempo limite da [solicitação](./logic-apps-limits-and-config.md). Se essa condição ocorrer, as solicitações poderão ser bloqueadas. Para ajudá-lo a diagnosticar problemas, saiba como você pode [verificar e monitorar seus aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Parabéns, agora você criou um aplicativo lógico que pode se comunicar com o servidor SAP. Agora que você configurou uma conexão SAP para seu aplicativo lógico, pode explorar outras ações SAP disponíveis, como BAPI e RFC.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre o conector, conforme descrito pelos arquivos do Swagger dos conectores, consulte estes artigos de referência: 

* [Servidor de aplicativos SAP](/connectors/sap)
* [Servidor de mensagens SAP](/connectors/sap/#send-message-to-sap)

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* [Conectar-se a sistemas locais](../logic-apps/logic-apps-gateway-connection.md) de aplicativos lógicos
* Saiba como validar, transformar e outras operações de mensagens com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre outros conectores de [aplicativos lógicos](../connectors/apis-list.md)
