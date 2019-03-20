---
title: Ligar a sistemas SAP - Azure Logic Apps | Documentos da Microsoft
description: Como aceder e gerir recursos SAP através da automatização de fluxos de trabalho com o Azure Logic Apps
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: d677c0eae9c92f90783ed4ebd95a528b34c872ec
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170841"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ligar a sistemas SAP a partir do Azure Logic Apps

> [!NOTE]
> Este conector SAP está agendada para preterição. Utilize ou migrar para o [conector do SAP mais recente e mais avançado](./logic-apps-using-sap-connector.md). 

Este artigo mostra como pode acessar seus recursos SAP a partir de dentro de uma aplicação lógica ao utilizar os conectores do servidor de aplicações SAP e SAP Message Server. Dessa forma, pode automatizar tarefas, processos e fluxos de trabalho que gerir os seus dados do SAP e a recursos através da criação de aplicações lógicas.

Este exemplo utiliza uma aplicação lógica que pode acionar com um pedido HTTP. A aplicação lógica envia um documento de intermediários (IDoc) para um servidor SAP e retorna uma resposta para o requerente que chamou a aplicação lógica.
Os conectores SAP atuais tem ações, mas não acionadores, para que este exemplo utiliza a [acionador de pedido HTTP](../connectors/connectors-native-reqres.md) como o primeiro passo no fluxo de trabalho da aplicação lógica. Para informações técnicas específicas do conector SAP, veja estes artigos de referência: 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">Conector do servidor de aplicações SAP</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">Conector de servidor de mensagens do SAP</a>

Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">Inscreva-se uma conta gratuita do Azure</a>.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este artigo, precisa destes itens:

* A aplicação de lógica de onde deseja acessar seu sistema SAP e um acionador que inicia o fluxo de trabalho da sua aplicação lógica. Atualmente, os conectores SAP fornecem apenas as ações. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Sua <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">servidor de aplicações SAP</a> ou <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">servidor de mensagens do SAP</a>

* Transfira e instale a versão mais recente [gateway de dados no local](https://www.microsoft.com/download/details.aspx?id=53127) em qualquer computador no local. Certifique-se de que configurar o gateway no portal do Azure antes de continuar. O gateway ajuda-o em segurança aceder a dados e recursos estão no local. Para obter mais informações, consulte [instalação no local gateway de dados para o Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Transfira e instale a biblioteca de cliente SAP mais recentes, o que está atualmente <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">conector do SAP (NCo) 3.0.20.0 para Microsoft .NET Framework 4.0 e Windows de 64 bits (x64)</a>, no mesmo computador que o gateway de dados no local. Instale esta versão ou posterior por esses motivos:

  * Versões anteriores do SAP NCo podem se tornar travadas quando mais do que um IDoc mensagens são enviados ao mesmo tempo. 
  Esta condição bloqueia todas as mensagens posteriores que são enviadas para o destino de SAP, fazendo com que as mensagens de tempo limite.

  * O gateway de dados no local é executado apenas em sistemas de 64 bits. 
  Caso contrário, receberá um erro de "imagem errado" porque o serviço de anfitrião de gateway de dados não suporta assemblagens de 32 bits.

  * O serviço de anfitrião de gateway de dados e o adaptador de SAP da Microsoft utilizam o .NET Framework 4.5. O NCo SAP para o .NET Framework 4.0 funciona com processos que utilizam o tempo de execução do .NET 4.0 para 4.7.1. 
  O NCo SAP para o .NET Framework 2.0 funciona com processos que utilizam o tempo de execução do .NET 2.0 para 3.5 e já não funciona com o gateway de dados no local mais recente.

* Conteúdo da mensagem que pode enviar ao seu servidor SAP, tal como um ficheiro de IDoc de exemplo. Este conteúdo tem de estar no formato XML e incluir o espaço de nomes para a ação de SAP que pretende utilizar.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Adicionar o acionador de pedido HTTP

No Azure Logic Apps, todas as aplicações lógicas têm de começar com uma [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico acontece ou quando for cumprida uma condição específica. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica e começa a ser executado o fluxo de trabalho da sua aplicação.

Neste exemplo, vai criar uma aplicação lógica com um ponto de extremidade no Azure para que possa enviar *pedidos de HTTP POST* à sua aplicação lógica. Quando a aplicação lógica recebe estes pedidos HTTP, o acionador é acionado e executa o passo seguinte no fluxo de trabalho.

1. No portal do Azure, crie uma aplicação lógica em branco, que abre o Estruturador da aplicação lógica. 

2. Na caixa de pesquisa, introduza "pedido de http" como o filtro. Na lista de disparadores, selecione este acionador: **Pedido - quando é recebido um pedido HTTP**

   ![Adicionar acionador de pedido de HTTP](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Guarde agora a aplicação lógica para que pode gerar um URL de ponto final para a aplicação lógica.
Na barra de ferramentas do estruturador, escolha **Guardar**. 

   O ponto final do URL agora aparece no seu acionador, por exemplo:

   ![Gerar URL para o ponto final](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Adicionar ação de SAP

No Azure Logic Apps, um [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no fluxo de trabalho que se segue um acionador ou outra ação. Se ainda não adicionou um acionador à sua aplicação lógica ainda e quiser seguir nesse exemplo, [adicione o acionador descrito nesta secção](#add-trigger).

1. No Estruturador da aplicação lógica, no acionador, escolha **novo passo** > **adicionar uma ação**.

   ![Adicionar uma ação](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. Na caixa de pesquisa, introduza "servidor sap" como o filtro. Na lista de ações, selecione a ação para o seu servidor SAP: 

   * **Servidor de aplicações SAP - enviá-las para SAP**
   * **Servidor de mensagens SAP - enviá-las para SAP**

   Este exemplo utiliza esta ação: **Servidor de aplicações SAP - enviá-las para SAP**

   ![Selecione "Servidor de aplicações SAP" ou "Servidor de mensagens SAP"](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Se lhe for pedido para obter detalhes de ligação, crie a ligação de SAP agora. Caso contrário, se a ligação já existir, avance para o passo seguinte para que possa definir a ação de SAP. 

   **Criar ligação de SAP no local**

   1. Para **Gateways**, selecione **ligar através do gateway de dados no local** para que as propriedades de ligação no local são apresentados.

   2. Forneça as informações de ligação para o seu servidor SAP. 
   Para o **gateway** propriedade, selecione o gateway de dados que criou no portal do Azure para a sua instalação do gateway, por exemplo:

      **Servidor de aplicações SAP**

      ![Criar ligação de servidor de aplicação SAP](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **Servidor de mensagens do SAP**

      ![Criar a ligação ao servidor SAP mensagem](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Quando tiver terminado, escolha **Create** (Criar).

      O Logic Apps configura e testa a ligação, certificar-se de que a ligação funciona corretamente.

4. Agora, localize e selecione uma ação a partir do seu servidor SAP. 

   1. Na **ação de SAP** caixa, escolha o ícone de pasta. 
   Na lista de pasta, localize e selecione a ação que pretende utilizar. 

      Neste exemplo seleciona os **IDOC** categoria para a ação de IDoc. 

      ![Localize e selecione a ação de IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Se não conseguir encontrar a ação que pretende, pode introduzir manualmente um caminho, por exemplo:

      ![Fornecer manualmente o caminho para a ação de IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Para obter mais informações sobre as operações de IDoc, consulte [esquemas para operações de IDOC da mensagem](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Clique no interior da **mensagem de entrada** caixa para que a lista de conteúdo dinâmico apareça. 
   Nessa lista, em **pedido de HTTP de quando é recebido**, selecione a **corpo** campo. 

      Este passo inclui o conteúdo do corpo do acionador de pedido de HTTP e envia essa saída a seu servidor SAP.

      ![Selecione o campo "Corpo"](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Quando tiver terminado, a ação de SAP aspeto deste exemplo:

      ![Ação concluída do SAP](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Adicionar ação de resposta HTTP

Agora, adicione uma ação de resposta para o fluxo de trabalho da sua aplicação lógica e inclua a saída da ação de SAP. Dessa forma, a aplicação lógica devolve os resultados do seu servidor SAP para o requerente original. 

1. No Estruturador da aplicação lógica, sob a ação de SAP, escolha **novo passo** > **adicionar uma ação**.

2. Na caixa de pesquisa, introduza "resposta" como o filtro. Na lista de ações, selecione a ação: **Pedido - resposta**

3. Clique no interior da **corpo** caixa para que a lista de conteúdo dinâmico apareça. Nessa lista, sob **enviar para o SAP**, selecione a **corpo** campo. 

   ![Ação concluída do SAP](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Guarde a aplicação lógica. 

## <a name="test-your-logic-app"></a>Teste a aplicação lógica

1. Se a sua aplicação lógica não estiver já ativada, no menu da aplicação lógica, escolha **descrição geral**. Na barra de ferramentas, escolha **ativar**. 

2. Na barra de ferramentas da Estruturador da aplicação lógica, escolha **executar**. Este passo inicia manualmente a aplicação lógica.

3. Acionar a sua aplicação lógica, enviando um pedido HTTP POST para o URL no acionador de pedido de HTTP e incluir a sua mensagem de conteúdo com o seu pedido. Enviar o pedido, pode utilizar uma ferramenta como [Postman](https://www.getpostman.com/apps). 

   Neste artigo, o pedido envia um arquivo de IDoc, que tem de estar no formato XML e incluir o espaço de nomes para a ação de SAP que estiver a utilizar, por exemplo: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Depois de enviar o pedido HTTP, aguarde que a resposta da sua aplicação lógica.

> [!NOTE]
> A aplicação lógica pode tempo limite se todos os passos necessários para a resposta não finalizar dentro de [limite de tempo limite do pedido](./logic-apps-limits-and-config.md). Se esta condição ocorrer, pedidos possam ser bloqueados. Para ajudar a diagnosticar problemas, saiba como pode [Verifique e monitorizar as aplicações lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Parabéns, acabou de criar uma aplicação lógica que possa comunicar com o seu servidor SAP. Agora que tiver configurado a uma ligação de SAP para a aplicação lógica, pode explorar outras ações de SAP disponíveis, como BAPI e RFC.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre o conector, conforme descrito pelo ficheiros de Swagger dos conectores, veja estes artigos de referência: 

* [Servidor de aplicações SAP](/connectors/sapapplicationserver/)
* [Servidor de mensagens do SAP](/connectors/sapmessageserver/)

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* [Ligar a sistemas no local](../logic-apps/logic-apps-gateway-connection.md) de aplicações lógicas
* Saiba como validar, transformar e outras operações de mensagem com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
