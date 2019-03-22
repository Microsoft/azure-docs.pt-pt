---
title: Ligar a sistemas SAP - Azure Logic Apps | Documentos da Microsoft
description: Como aceder e gerir recursos SAP através da automatização de fluxos de trabalho com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 09/14/2018
tags: connectors
ms.openlocfilehash: 468e73c64037a76da612cba8d6c2e9507dd3ac87
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887090"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ligar a sistemas SAP a partir do Azure Logic Apps

Este artigo mostra como pode aceder os recursos SAP no local de dentro de uma aplicação lógica utilizando o conector de SAP ERP Central componente (ECC). O conector funciona com os sistemas ECC e S/4 HANA no local. O conector de SAP ECC suporta a integração de dados ou de mensagem de e para sistemas baseados em SAP Netweaver através do documento de intermediários (IDoc) ou Business Application Programming Interface (BAPI) ou a chamada de função remota (RFC).

O conector de SAP ECC utiliza a <a href="https://support.sap.com/en/product/connectors/msnet.html">biblioteca de conectores de .NET de SAP (NCo)</a> e fornece essas operações ou ações:

- **Enviar para o SAP**: Enviar IDoc ou chamar funções BAPI sobre tRFC em sistemas SAP.
- **Receber do SAP**: Receba chamadas de função de IDoc ou BAPI pela tRFC de sistemas SAP.
- **Gerar esquemas**: Gere esquemas para os artefactos SAP para IDoc ou BAPI ou RFC.

O conector SAP integra-se com os sistemas SAP no local através da [gateway de dados no local](https://www.microsoft.com/download/details.aspx?id=53127). Em cenários de envio, por exemplo, ao enviar uma mensagem a partir das aplicações lógicas a um sistema SAP, o gateway de dados age como um cliente RFC e encaminha os pedidos recebidos de aplicações lógicas para SAP.
Da mesma forma, em cenários de receção, o gateway de dados age como um servidor RFC que recebe pedidos do SAP e a encaminha para a aplicação lógica. 

Este artigo mostra como criar aplicações lógicas que se integram com o SAP ao abordar os cenários de integração descrito anteriormente de exemplo.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este artigo, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">Inscreva-se uma conta gratuita do Azure</a>.

* A aplicação de lógica de onde deseja acessar seu sistema SAP e um acionador que inicia o fluxo de trabalho da sua aplicação lógica. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Sua <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">servidor de aplicações SAP</a> ou <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">servidor de mensagens do SAP</a>

* Transfira e instale a versão mais recente [gateway de dados no local](https://www.microsoft.com/download/details.aspx?id=53127) em qualquer computador no local. Certifique-se de que configurar o gateway no portal do Azure antes de continuar. O gateway ajuda-o em segurança aceder a dados e recursos estão no local. Para obter mais informações, consulte [instalação no local gateway de dados para o Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Transfira e instale a biblioteca de cliente SAP mais recentes, o que está atualmente <a href="https://softwaredownloads.sap.com/file/0020000001865512018" target="_blank">conector do SAP (NCo) 3.0.21.0 para Microsoft .NET Framework 4.0 e Windows de 64 bits (x64)</a>, no mesmo computador que o gateway de dados no local. Instale esta versão ou posterior por esses motivos:

  * Versões anteriores do SAP NCo podem se tornar travadas quando mais do que um IDoc mensagens são enviados ao mesmo tempo. 
  Esta condição bloqueia todas as mensagens posteriores que são enviadas para o destino de SAP, fazendo com que as mensagens de tempo limite.

  * O gateway de dados no local é executado apenas em sistemas de 64 bits. 
  Caso contrário, receberá um erro de "imagem errado" porque o serviço de anfitrião de gateway de dados não suporta assemblagens de 32 bits.

  * O serviço de anfitrião de gateway de dados e o adaptador de SAP da Microsoft utilizam o .NET Framework 4.5. O NCo SAP para o .NET Framework 4.0 funciona com processos que utilizam o tempo de execução do .NET 4.0 para 4.7.1. 
  O NCo SAP para o .NET Framework 2.0 funciona com processos que utilizam o tempo de execução do .NET 2.0 para 3.5 e já não funciona com o gateway de dados no local mais recente.

* Conteúdo da mensagem que pode enviar ao seu servidor SAP, tal como um ficheiro de IDoc de exemplo. Este conteúdo tem de estar no formato XML e incluir o espaço de nomes para a ação de SAP que pretende utilizar.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Enviar para o SAP

Este exemplo utiliza uma aplicação lógica que pode acionar com um pedido HTTP. A aplicação lógica envia um documento de intermediários (IDoc) para um servidor SAP e retorna uma resposta para o requerente que chamou a aplicação lógica. 

### <a name="add-http-request-trigger"></a>Adicionar o acionador de pedido HTTP

No Azure Logic Apps, todas as aplicações lógicas têm de começar com uma [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico acontece ou quando for cumprida uma condição específica. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica e começa a ser executado o fluxo de trabalho da sua aplicação.

Neste exemplo, vai criar uma aplicação lógica com um ponto de extremidade no Azure para que possa enviar *pedidos de HTTP POST* à sua aplicação lógica. Quando a aplicação lógica recebe estes pedidos HTTP, o acionador é acionado e executa o passo seguinte no fluxo de trabalho.

1. Na [portal do Azure](https://portal.azure.com), criar uma aplicação lógica em branco, que abre o Estruturador da aplicação lógica. 

2. Na caixa de pesquisa, introduza "pedido de http" como o filtro. Na lista de disparadores, selecione este acionador: **Pedido - quando é recebido um pedido HTTP**

   ![Adicionar acionador de pedido de HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Guarde agora a aplicação lógica para que pode gerar um URL de ponto final para a aplicação lógica.
Na barra de ferramentas do estruturador, escolha **Guardar**. 

   O ponto final do URL agora aparece no seu acionador, por exemplo:

   ![Gerar URL para o ponto final](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-sap-action"></a>Adicionar ação de SAP

No Azure Logic Apps, um [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no fluxo de trabalho que se segue um acionador ou outra ação. Se ainda não adicionou um acionador à sua aplicação lógica ainda e quiser seguir nesse exemplo, [adicione o acionador descrito nesta secção](#add-trigger).

1. No Estruturador da aplicação lógica, no acionador, escolha **novo passo** > **adicionar uma ação**.

   ![Adicionar uma ação](./media/logic-apps-using-sap-connector/add-action.png) 

2. Na caixa de pesquisa, introduza "sap" como o filtro. Na lista de ações, selecione a ação: **Enviar mensagem para SAP**
  
   ![Selecione a ação de envio do SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Em alternativa, em vez de procurar, escolha o **Enterprise** separador e selecione a ação de SAP.

   ![Selecione a ação de envio SAP da guia empresarial](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

3. Se lhe for pedido para obter detalhes de ligação, crie a ligação de SAP agora. Caso contrário, se a ligação já existir, avance para o passo seguinte para que possa definir a ação de SAP. 

   **Criar ligação de SAP no local**

   1. Forneça as informações de ligação para o seu servidor SAP. 
   Para o **Gateway de dados** propriedade, selecione o gateway de dados que criou no portal do Azure para a sua instalação do gateway.

      Se o **tipo de início de sessão** estiver definida como **Application Server**, essas propriedades, que são apresentados normalmente opcionais, são necessárias:

      ![Criar ligação de servidor de aplicação SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Se o **tipo de início de sessão** estiver definida como **grupo**, essas propriedades, que são apresentados normalmente opcionais, são necessárias: 

      ![Criar a ligação ao servidor SAP mensagem](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. Quando tiver terminado, escolha **Create** (Criar). 
   
      O Logic Apps configura e testa a ligação, certificar-se de que a ligação funciona corretamente.

4. Agora, localize e selecione uma ação a partir do seu servidor SAP. 

   1. Na **ação de SAP** caixa, escolha o ícone de pasta. 
   Na lista de ficheiro, localize e selecione a mensagem SAP que pretende utilizar. 
   Para navegar na lista, utilize as setas.

      Neste exemplo seleciona um IDoc com **ordem** tipo. 

      ![Localize e selecione a ação de IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Se não conseguir encontrar a ação que pretende, pode introduzir manualmente um caminho, por exemplo:

      ![Fornecer manualmente o caminho para a ação de IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Forneça o valor para a ação de SAP através do editor de expressões. Desta forma, pode utilizar a mesma ação para diferentes tipos de mensagem.

      Para obter mais informações sobre as operações de IDoc, consulte [mensagem de esquemas para operações de IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   2. Clique no interior da **mensagem de entrada** caixa para que a lista de conteúdo dinâmico apareça. 
   Nessa lista, sob **pedido de HTTP de quando é recebido**, selecione a **corpo** campo. 

      Este passo inclui o conteúdo do corpo do acionador de pedido de HTTP e envia essa saída a seu servidor SAP.

      ![Selecione o campo "Corpo"](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Quando tiver terminado, a ação de SAP aspeto deste exemplo:

      ![Ação concluída do SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

5. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

<a name="add-response"></a>

### <a name="add-http-response-action"></a>Adicionar ação de resposta HTTP

Agora, adicione uma ação de resposta para o fluxo de trabalho da sua aplicação lógica e inclua a saída da ação de SAP. Dessa forma, a aplicação lógica devolve os resultados do seu servidor SAP para o requerente original. 

1. No Estruturador da aplicação lógica, sob a ação de SAP, escolha **novo passo** > **adicionar uma ação**.

2. Na caixa de pesquisa, introduza "resposta" como o filtro. Na lista de ações, selecione a ação: **Pedido - resposta**

3. Clique no interior da **corpo** caixa para que a lista de conteúdo dinâmico apareça. Nessa lista, sob **enviar para o SAP**, selecione a **corpo** campo. 

   ![Ação concluída do SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. Guarde a aplicação lógica. 

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

1. Se a sua aplicação lógica não estiver já ativada, no menu da aplicação lógica, escolha **descrição geral**. Na barra de ferramentas, escolha **ativar**. 

2. Na barra de ferramentas da Estruturador da aplicação lógica, escolha **executar**. Este passo inicia manualmente a aplicação lógica.

3. Acionar a sua aplicação lógica, enviando um pedido HTTP POST para o URL no acionador de pedido de HTTP e incluir a sua mensagem de conteúdo com o seu pedido. Enviar o pedido, pode utilizar uma ferramenta como [Postman](https://www.getpostman.com/apps). 

   Neste artigo, o pedido envia um arquivo de IDoc, que tem de estar no formato XML e incluir o espaço de nomes para a ação de SAP que estiver a utilizar, por exemplo: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Depois de enviar o pedido HTTP, aguarde que a resposta da sua aplicação lógica.

   > [!NOTE]
   > A aplicação lógica pode tempo limite se todos os passos necessários para a resposta não finalizar dentro de [limite de tempo limite do pedido](./logic-apps-limits-and-config.md). Se esta condição ocorrer, pedidos possam ser bloqueados. Para ajudar a diagnosticar problemas, saiba como pode [Verifique e monitorizar as aplicações lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Parabéns, acabou de criar uma aplicação lógica que possa comunicar com o seu servidor SAP. Agora que tiver configurado a uma ligação de SAP para a aplicação lógica, pode explorar outras ações de SAP disponíveis, como BAPI e RFC.

## <a name="receive-from-sap"></a>Receber do SAP

Este exemplo utiliza uma aplicação lógica que aciona quando receber uma mensagem de um sistema SAP. 

### <a name="add-sap-trigger"></a>Adicionar o acionador do SAP

1. No portal do Azure, crie uma aplicação lógica em branco, que abre o Estruturador da aplicação lógica. 

2. Na caixa de pesquisa, introduza "sap" como o filtro. Na lista de disparadores, selecione este acionador: **Quando uma mensagem é recebida do SAP**

   ![Adicionar o acionador do SAP](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Em alternativa, pode ir até a guia empresarial e selecione o acionador

   ![Adicionar o acionador SAP a partir do separador ent](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

3. Se lhe for pedido para obter detalhes de ligação, crie a ligação de SAP agora. Caso contrário, se a ligação já existir, avance para o passo seguinte para que possa definir a ação de SAP. 

   **Criar ligação de SAP no local**

   1. Forneça as informações de ligação para o seu servidor SAP. 
   Para o **Gateway de dados** propriedade, selecione o gateway de dados que criou no portal do Azure para a sua instalação do gateway.

      Se o **tipo de início de sessão** estiver definida como **Application Server**, essas propriedades, que são apresentados normalmente opcionais, são necessárias:

      ![Criar ligação de servidor de aplicação SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Se o **tipo de início de sessão** estiver definida como **grupo**, essas propriedades, que são apresentados normalmente opcionais, são necessárias:

      ![Criar a ligação ao servidor SAP mensagem](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

4. Forneça os parâmetros necessários com base na configuração do sistema SAP. 

   Opcionalmente, pode fornecer uma ou mais ações de SAP. 
   Esta lista de ações Especifica as mensagens que o acionador recebe a partir do seu servidor SAP através do gateway de dados. 
   Uma lista vazia, especifica que o acionador recebe todas as mensagens. 
   Se a lista tiver mais do que uma mensagem, o acionador recebe apenas as mensagens especificadas na lista. Quaisquer outras mensagens enviadas a partir do seu servidor SAP são rejeitadas pelo gateway.

   Pode selecionar uma ação de SAP o Seletor de ficheiros:

   ![Selecione a ação de SAP](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Em alternativa, pode especificar manualmente uma ação:

   ![Introduza manualmente a ação de SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Eis um exemplo que mostra como a ação é apresentado quando configurou o acionador para receber mais de uma mensagem.

   ![Exemplo de Acionador](media/logic-apps-using-sap-connector/example-trigger.png)  

   Para obter mais informações sobre a ação de SAP, consulte [esquemas para operações de IDOC da mensagem](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

5. Guarde agora a aplicação lógica para que possa começar a receber mensagens do seu sistema SAP.
Na barra de ferramentas do estruturador, escolha **Guardar**. 

A aplicação lógica está agora pronta para receber mensagens do seu sistema SAP. 

> [!NOTE]
> O acionador SAP não é um acionador de consulta, mas um acionador de webhook com base em vez disso. O acionador é chamado do gateway apenas quando existe uma mensagem, pelo que não a sondagem é necessária. 

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

1. Para acionar a sua aplicação lógica, envie uma mensagem do sistema SAP.

2. No menu da aplicação lógica, escolha **descrição geral**e reveja a **histórico de execuções** para quaisquer novas execuções para a aplicação lógica. 

3. Abra a execução mais recente, que mostra a mensagem enviada do seu sistema SAP na secção de saídas de Acionador.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Gerar esquemas para artefactos no SAP

Este exemplo utiliza uma aplicação lógica que pode acionar com um pedido HTTP. A ação de SAP envia um pedido para um sistema SAP para gerar os esquemas para o documento intermediários especificado (IDoc) e BAPI. Esquemas que retornam na resposta são carregadas para uma conta de integração com o conector do Azure Resource Manager.

### <a name="add-http-request-trigger"></a>Adicionar o acionador de pedido HTTP

1. No portal do Azure, crie uma aplicação lógica em branco, que abre o Estruturador da aplicação lógica. 

2. Na caixa de pesquisa, introduza "pedido de http" como o filtro. Na lista de disparadores, selecione este acionador: **Pedido - quando é recebido um pedido HTTP**

   ![Adicionar acionador de pedido de HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Guarde agora a aplicação lógica para que pode gerar um URL de ponto final para a aplicação lógica.
Na barra de ferramentas do estruturador, escolha **Guardar**. 

   O ponto final do URL agora aparece no seu acionador, por exemplo:

   ![Gerar URL para o ponto final](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-sap-action-to-generate-schemas"></a>Adicionar ação de SAP para gerar esquemas

1. No Estruturador da aplicação lógica, no acionador, escolha **novo passo** > **adicionar uma ação**.

   ![Adicionar uma ação](./media/logic-apps-using-sap-connector/add-action.png) 

2. Na caixa de pesquisa, introduza "sap" como o filtro. Na lista de ações, selecione a ação: **Gerar esquemas**
  
   ![Selecione a ação de envio do SAP](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Em alternativa, também pode escolher o **Enterprise** separador e selecione a ação de SAP.

   ![Selecione a ação de envio SAP da guia empresarial](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

3. Se lhe for pedido para obter detalhes de ligação, crie a ligação de SAP agora. Caso contrário, se a ligação já existir, avance para o passo seguinte para que possa definir a ação de SAP. 

   **Criar ligação de SAP no local**

   1. Forneça as informações de ligação para o seu servidor SAP. 
   Para o **Gateway de dados** propriedade, selecione o gateway de dados que criou no portal do Azure para a sua instalação do gateway.

      Se o **tipo de início de sessão** estiver definida como **Application Server**, essas propriedades, que são apresentados normalmente opcionais, são necessárias:

      ![Criar ligação de servidor de aplicação SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Se o **tipo de início de sessão** estiver definida como **grupo**, essas propriedades, que são apresentados normalmente opcionais, são necessárias:
   
      ![Criar a ligação ao servidor SAP mensagem](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. Quando tiver terminado, escolha **Create** (Criar). O Logic Apps configura e testa a ligação, certificar-se de que a ligação funciona corretamente.

4. Forneça o caminho para o artefacto para o qual pretende gerar o esquema.

   Pode selecionar a ação de SAP o Seletor de ficheiros:

   ![Selecione a ação de SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Em alternativa, pode introduzir manualmente a ação:

   ![Introduza manualmente a ação de SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png) 

   Para gerar esquemas para a mais do que um artefato, forneça os detalhes da ação de SAP para cada artefato, por exemplo:

   ![Selecione Adicionar novo item](media/logic-apps-using-sap-connector/schema-generator-array-pick.png) 

   ![Mostrar dois itens](media/logic-apps-using-sap-connector/schema-generator-example.png) 

   Para obter mais informações sobre a ação de SAP, consulte [mensagem de esquemas para operações de IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

5. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

1. Na barra de ferramentas da estruturador, escolha **executar** para acionar uma execução para a aplicação lógica.

2. Abra a execução e verifica os resultados para o **gerar esquema** ação. 

   Os resultados mostram os esquemas gerados para a lista especificada de mensagens.

### <a name="upload-schemas-to-integration-account"></a>Carregue esquemas para a conta de integração

Opcionalmente, pode transferir ou armazenar os esquemas gerados no repositório, como um blob, o armazenamento ou a conta de integração. Contas de integração de proporcionam uma experiência de primeira classe com outras ações de XML, para que este exemplo mostra como carregar esquemas para uma conta de integração para a mesma aplicação lógica com o conector do Azure Resource Manager.

1. No Estruturador da aplicação lógica, no acionador, escolha **novo passo** > **adicionar uma ação**. Na caixa de pesquisa, introduza "resource manager" como o filtro. Selecione a ação: **Criar ou atualizar um recurso**

   ![Selecione a ação do Azure Resource Manager](media/logic-apps-using-sap-connector/select-arm-action.png) 

2. Introduza os detalhes, incluindo a sua subscrição do Azure, o grupo de recursos do Azure e a conta de integração. Para outros campos, siga o exemplo abaixo.

   ![Introduza os detalhes para a ação do Azure Resource Manager](media/logic-apps-using-sap-connector/arm-action.png)

   O SAP **gerar esquemas** ação gera esquemas como uma coleção, para que o estruturador adiciona automaticamente um **para cada** loop para a ação. 
   Eis um exemplo que mostra como esta ação é apresentado:

   ![Ação de Gestor de recursos do Azure "para cada um" loop](media/logic-apps-using-sap-connector/arm-action-foreach.png)  

   > [!NOTE]
   > Os esquemas de utilizam o formato com codificação base64. Para carregar os esquemas para uma conta de integração, devem ser descodificados utilizando o `base64ToString()` função. Eis um exemplo que mostra o código para o `"properties"` elemento:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

3. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

1. Na barra de ferramentas da estruturador, escolha **executar** para acionar manualmente a aplicação lógica.

2. Após o êxito executar, vá para a conta de integração e verifique que os esquemas gerados gerados existem.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

Seguem-se a problemas atualmente conhecidos e limitações para o conector SAP:

* O acionador SAP não suporta a receber de batch IDOCs da SAP. Esta ação poderá resultar numa falha de ligação de RFC entre seu sistema SAP e o gateway de dados.

* O acionador SAP não suporta clusters de gateway de dados. Em alguns casos de ativação pós-falha, o nó de gateway de dados que se comunique com o sistema SAP pode diferir a partir do nó ativo, resultando num comportamento inesperado. Para cenários de envio, são suportados clusters de gateway de dados.

* Em cenários de receção, retornar uma resposta não nulo não é suportada. Uma aplicação lógica com um acionador e uma ação de resposta resulta em comportamentos inesperados. 

* Apenas uma única enviar para SAP chamada ou mensagem funciona com tRFC. O padrão de consolidação de Business Application Programming Interface (BAPI), como a realização de várias chamadas de tRFC na mesma sessão, não é suportado.

* RFCs com anexos não são suportados para ambos os enviar para o SAP e gerar as ações de esquemas.

* O conector SAP atualmente não suporta cadeias de caracteres de router SAP. O gateway de dados no local tem de existir LAN do mesmo como o sistema SAP que pretende ligar.

* A conversão para a ausência de valores de vazios, mínimo e máximo de (null), para campos DATS e TIMS SAP está sujeitas a alterações em atualizações posteriores para o gateway de dados no local.

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* [Ligar a sistemas no local](../logic-apps/logic-apps-gateway-connection.md) de aplicações lógicas
* Saiba como validar, transformar e outras operações de mensagem com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
