---
title: Ligar a sistemas SAP - Azure Logic Apps
description: Aceder e gerir recursos SAP através da automatização de fluxos de trabalho com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 05/09/2019
tags: connectors
ms.openlocfilehash: 8232bf90b4dc160583959345a257846aaabad690
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67458938"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ligar a sistemas SAP a partir do Azure Logic Apps

Este artigo mostra como pode aceder aos recursos SAP no local de dentro de uma aplicação lógica utilizando o conector SAP. O conector funciona com as versões de clássicos da SAP como R/3 e ECC sistemas no local. O conector também permite a integração com mais recentes com base em HANA SAP sistemas do SAP, como S/4 HANA, se estão hospedados no local ou na cloud. O conector SAP suporta a integração de dados ou de mensagem de e para sistemas baseados em SAP NetWeaver, através do documento de intermediários (IDoc), Business Application Programming Interface (BAPI) ou a chamada de função remota (RFC).

O conector SAP utiliza a [biblioteca de conectores de .NET de SAP (NCo)](https://support.sap.com/en/product/connectors/msnet.html) e fornece essas operações ou ações:

* **Enviar para o SAP**: Enviar IDoc por tRFC, chamar funções BAPI sobre RFC ou chamar RFC/tRFC em sistemas SAP.
* **Receber do SAP**: Recebe IDoc tRFC, chamar funções BAPI sobre tRFC ou chamar RFC/tRFC em sistemas SAP.
* **Gerar esquemas**: Gere esquemas para os artefactos SAP para IDoc, BAPI ou RFC.

Para essas operações, o conector SAP suporta a autenticação básica através de nomes de utilizador e palavras-passe. O conector também suporta [comunicações de rede seguro (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC pode ser utilizado para o SAP NetWeaver início de sessão único (SSO) ou para os recursos de segurança adicional fornecidos por um produto de segurança externas.

O conector SAP integra-se com os sistemas SAP no local através da [gateway de dados no local](../logic-apps/logic-apps-gateway-connection.md). Em cenários de envio, por exemplo, quando uma mensagem é enviada a partir de uma aplicação lógica a um sistema SAP, o gateway de dados age como um cliente RFC e encaminha os pedidos recebidos a partir da aplicação lógica para SAP.
Da mesma forma, na receção de cenários, o gateway de dados age como um servidor RFC que recebe pedidos da SAP e reencaminha-os para a aplicação lógica.

Este artigo mostra como criar aplicações lógicas que se integram com o SAP ao abordar os cenários de integração descrito anteriormente de exemplo.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este artigo, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [Inscreva-se uma conta gratuita do Azure](https://azure.microsoft.com/free/).
* A aplicação de lógica de onde deseja acessar seu sistema SAP e um acionador que inicia o fluxo de trabalho da sua aplicação lógica. Se estiver familiarizado com aplicações lógicas, consulte [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).
* Sua [servidor de aplicações SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou [servidor de mensagens SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).
* Transfira e instale a versão mais recente [gateway de dados no local](https://www.microsoft.com/download/details.aspx?id=53127) em qualquer computador no local. Certifique-se de que configurar o gateway no portal do Azure antes de continuar. O gateway ajuda-o em segurança aceder a dados no local e recursos. Para obter mais informações, consulte [instale um gateway de dados no local para o Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).
* Se utilizar SNC com SSO, certifique-se de que o gateway está em execução como um utilizador que está mapeado contra o utilizador SAP. Para alterar a conta predefinida, selecione **alterar conta**e introduza as credenciais de utilizador.

  ![Alterar a conta de gateway](./media/logic-apps-using-sap-connector/gateway-account.png)

* Se ativar SNC com um produto de segurança externa, copie a biblioteca SNC ou os ficheiros na mesma máquina em que o gateway está instalado. Alguns exemplos de produtos SNC [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.
* Transfira e instale a biblioteca de cliente SAP mais recentes, o que está atualmente [conector do SAP (NCo) 3.0.21.0 para Microsoft .NET Framework 4.0 e Windows de 64 bits (x64)](https://softwaredownloads.sap.com/file/0020000001865512018), no mesmo computador que o gateway de dados no local. Instale esta versão ou posterior por esses motivos:

  * Versões anteriores do SAP NCo podem se tornar travadas quando mais do que uma mensagem de IDoc é enviada ao mesmo tempo. Esta condição bloqueia todas as mensagens posteriores que são enviadas para o destino de SAP, o que faz com que as mensagens de tempo limite.
  * O gateway de dados no local é executado apenas em sistemas de 64 bits. Caso contrário, receberá um erro de "imagem errado" porque o serviço de anfitrião de gateway de dados não suporta assemblagens de 32 bits.
  * O serviço de anfitrião de gateway de dados e o adaptador de SAP da Microsoft utilizam o .NET Framework 4.5. O NCo SAP para o .NET Framework 4.0 funciona com processos que utilizam o tempo de execução do .NET 4.0 para 4.7.1. O NCo SAP para o .NET Framework 2.0 funciona com os processos que utilizam o tempo de execução do .NET 2.0 para 3.5, mas já não funciona com o gateway de dados no local mais recente.

* Conteúdo da mensagem que pode enviar ao seu servidor SAP, como um ficheiro de IDoc de exemplo, tem de estar no formato XML e incluir o espaço de nomes para a ação de SAP que pretende utilizar.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Enviar para o SAP

Este exemplo utiliza uma aplicação lógica que pode acionar com um pedido HTTP. A aplicação lógica envia um IDoc para um servidor SAP e retorna uma resposta para o requerente que chamou a aplicação lógica. 

### <a name="add-an-http-request-trigger"></a>Adicionar um acionador de pedido de HTTP

No Azure Logic Apps, todas as aplicações lógicas têm de começar com uma [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico acontece ou quando for cumprida uma condição específica. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica e começa a ser executado o fluxo de trabalho da sua aplicação.

Neste exemplo, vai criar uma aplicação lógica com um ponto de extremidade no Azure para que possa enviar *pedidos de HTTP POST* à sua aplicação lógica. Quando a aplicação lógica recebe estes pedidos HTTP, o acionador é acionado e executa o passo seguinte no fluxo de trabalho.

1. Na [portal do Azure](https://portal.azure.com), criar uma aplicação lógica em branco, que abre o Estruturador da aplicação lógica.

1. Na caixa de pesquisa, introduza "pedido de http" como o filtro. Partir do **Acionadores** lista, selecione **pedido de HTTP de quando é recebido**.

   ![Adicionar acionador de pedido de HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Guarde agora a aplicação lógica para que possa gerar um URL de ponto final para a aplicação lógica. Na barra de ferramentas da estruturador, selecione **guardar**.

   O ponto final do URL agora aparece no seu acionador, por exemplo:

   ![Gerar URL para o ponto final](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Adicionar uma ação de SAP

No Azure Logic Apps, um [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no fluxo de trabalho que se segue um acionador ou outra ação. Se ainda não adicionou um acionador à sua aplicação lógica ainda e quiser seguir nesse exemplo, [adicione o acionador descrito nesta secção](#add-trigger).

1. No Estruturador da aplicação lógica, no acionador, selecione **novo passo**.

   ![Selecione "Novo passo"](./media/logic-apps-using-sap-connector/add-action.png)

1. Na caixa de pesquisa, introduza "sap" como o filtro. Partir do **ações** lista, selecione **enviar mensagem para SAP**.
  
   ![Selecione a ação de envio do SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Ou em vez de procurar, escolha o **Enterprise** separador e selecione a ação de SAP.

   ![Selecione a ação de envio SAP da guia empresarial](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Se lhe for pedido para obter detalhes de ligação, crie a ligação de SAP agora. Caso contrário, se a ligação já existir, avance para o passo seguinte para que pode configurar a ação de SAP.

   **Crie uma ligação de SAP no local**

    1. Forneça as informações de ligação para o seu servidor SAP. Para o **Gateway de dados** propriedade, selecione o gateway de dados que criou no portal do Azure para a sua instalação do gateway.

         - Se o **tipo de início de sessão** estiver definida como **Application Server**, essas propriedades, que são apresentados normalmente opcionais, são necessárias:

            ![Criar ligação de servidor de aplicação SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

         - Se o **tipo de início de sessão** estiver definida como **grupo**, essas propriedades, que são apresentados normalmente opcionais, são necessárias:

            ![Criar a ligação ao servidor SAP mensagem](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

           Por predefinição, a rigidez de tipos é utilizado para verificar a existência de valores inválidos ao efetuar a validação de XML no esquema. Esse comportamento pode ajudar a detectar problemas anteriormente. O **escrever seguro** opção está disponível para compatibilidade com versões anteriores e só verifica o comprimento da cadeia. Saiba mais sobre o [escrever seguro opção](#safe-typing).

    1. Quando tiver terminado, selecione **criar**.

       O Logic Apps configura e testa a ligação para se certificar de que a ligação funciona corretamente.

1. Agora, localize e selecione uma ação a partir do seu servidor SAP.

    1. Na **SAP ação** caixa, selecione o ícone de pasta. Na lista de ficheiro, localize e selecione a mensagem SAP que pretende utilizar. Para navegar na lista, utilize as setas.

       Neste exemplo seleciona um IDoc com o **encomendas** tipo.

       ![Localize e selecione a ação de IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

       Se não conseguir encontrar a ação que pretende, pode introduzir manualmente um caminho, por exemplo:

       ![Fornecer manualmente o caminho para a ação de IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

       > [!TIP]
       > Forneça o valor para **SAP ação** por meio do editor de expressão. Dessa forma, pode utilizar a mesma ação para diferentes tipos de mensagem.

       Para obter mais informações sobre as operações de IDoc, consulte [mensagem de esquemas para operações de IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

    1. Clique no interior da **mensagem de entrada** caixa para que a lista de conteúdo dinâmico apareça. Nessa lista, sob **pedido de HTTP de quando é recebido**, selecione a **corpo** campo.

       Este passo inclui o conteúdo do corpo do acionador de pedido de HTTP e envia essa saída a seu servidor SAP.

       ![Selecione o campo "Corpo"](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

       Quando tiver terminado, a ação de SAP terá um aspeto semelhante a este exemplo:

       ![Ação concluída do SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas da estruturador, selecione **guardar**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Adicionar uma ação de resposta HTTP

Agora, adicione uma ação de resposta para o fluxo de trabalho da sua aplicação lógica e inclua a saída da ação de SAP. Dessa forma, a aplicação lógica devolve os resultados do seu servidor SAP para o requerente original.

1. No Estruturador da aplicação lógica, sob a ação de SAP, selecione **novo passo**.

1. Na caixa de pesquisa, introduza "resposta" como o filtro. Partir do **ações** lista, selecione **resposta**.

1. Clique no interior da **corpo** caixa para que a lista de conteúdo dinâmico apareça. Nessa lista, sob **mensagem de envio para o SAP**, selecione a **corpo** campo.

   ![Ação concluída do SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Guarde a aplicação lógica.

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

1. Se a sua aplicação lógica não estiver já ativada, no menu da aplicação lógica, selecione **descrição geral**. Na barra de ferramentas, selecione **ativar**.

1. Na barra de ferramentas da estruturador, selecione **executar**. Este passo inicia manualmente a aplicação lógica.

1. Acione a sua aplicação lógica, enviando um pedido HTTP POST para o URL no seu acionador de pedido de HTTP.
Inclua o seu conteúda com o seu pedido de mensagem. Enviar o pedido, pode utilizar uma ferramenta como [Postman](https://www.getpostman.com/apps).

   Neste artigo, o pedido envia um arquivo de IDoc, que tem de estar no formato XML e incluir o espaço de nomes para a ação de SAP que estiver a utilizar, por exemplo:

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Depois de enviar o pedido HTTP, aguarde que a resposta da sua aplicação lógica.

   > [!NOTE]
   > A aplicação lógica pode tempo limite se todos os passos necessários para a resposta não finalizar dentro de [limite de tempo limite do pedido](./logic-apps-limits-and-config.md). Se esta condição ocorrer, pedidos possam ser bloqueados. Para ajudar a diagnosticar problemas, saiba como pode [Verifique e monitorizar as aplicações lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Acabou de criar uma aplicação lógica que possa comunicar com o seu servidor SAP. Agora que tiver configurado a uma ligação de SAP para a aplicação lógica, pode explorar outras ações de SAP disponíveis, como BAPI e RFC.

## <a name="receive-from-sap"></a>Receber do SAP

Este exemplo utiliza uma aplicação lógica que aciona quando a aplicação recebe uma mensagem a partir de um sistema SAP.

### <a name="add-an-sap-trigger"></a>Adicionar um acionador SAP

1. No portal do Azure, crie uma aplicação lógica em branco, que abre o Estruturador da aplicação lógica.

1. Na caixa de pesquisa, introduza "sap" como o filtro. Partir do **Acionadores** lista, selecione **quando uma mensagem é recebida do SAP**.

   ![Adicionar o acionador do SAP](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Em alternativa, pode ir para o **Enterprise** separador e selecione o acionador:

   ![Adicionar o acionador SAP a partir do separador de Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Se lhe for pedido para obter detalhes de ligação, crie a ligação de SAP agora. Se a ligação já existir, continue com a próxima etapa para que possa definir a ação de SAP.

   **Crie uma ligação de SAP no local**

   - Forneça as informações de ligação para o seu servidor SAP. Para o **Gateway de dados** propriedade, selecione o gateway de dados que criou no portal do Azure para a sua instalação do gateway.

      - Se o **tipo de início de sessão** estiver definida como **Application Server**, essas propriedades, que são apresentados normalmente opcionais, são necessárias:

         ![Criar ligação de servidor de aplicação SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      - Se o **tipo de início de sessão** estiver definida como **grupo**, essas propriedades, que são apresentados normalmente opcionais, são necessárias:

          ![Criar a ligação ao servidor SAP mensagem](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Por predefinição, a rigidez de tipos é utilizado para verificar a existência de valores inválidos ao efetuar a validação de XML no esquema. Esse comportamento pode ajudar a detectar problemas anteriormente. O **escrever seguro** opção está disponível para compatibilidade com versões anteriores e só verifica o comprimento da cadeia. Saiba mais sobre o [escrever seguro opção](#safe-typing).

1. Forneça os parâmetros necessários com base na configuração do sistema SAP.

   Opcionalmente, pode fornecer uma ou mais ações de SAP. Esta lista de ações Especifica as mensagens que o acionador recebe a partir do seu servidor SAP através do gateway de dados. Uma lista vazia, especifica que o acionador recebe todas as mensagens. Se a lista tiver mais do que uma mensagem, o acionador recebe apenas as mensagens especificadas na lista. Quaisquer outras mensagens enviadas a partir do seu servidor SAP são rejeitadas pelo gateway.

   Pode selecionar uma ação de SAP o Seletor de ficheiros:

   ![Selecione a ação de SAP](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Em alternativa, pode especificar manualmente uma ação:

   ![Introduza manualmente a ação de SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Eis um exemplo que mostra como a ação é apresentado quando configurou o acionador para receber mais de uma mensagem.

   ![Exemplo de Acionador](media/logic-apps-using-sap-connector/example-trigger.png)  

   Para obter mais informações sobre a ação de SAP, consulte [esquemas para operações de IDOC da mensagem](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Guarde agora a aplicação lógica para que possa começar a receber mensagens do seu sistema SAP.
Na barra de ferramentas da estruturador, selecione **guardar**.

A aplicação lógica está agora pronta para receber mensagens do seu sistema SAP.

> [!NOTE]
> O acionador SAP não é um acionador de consulta, mas é um acionador de webhook com base em vez disso. O acionador é chamado do gateway apenas quando existe uma mensagem, pelo que não a sondagem é necessária.

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

1. Para acionar a sua aplicação lógica, envie uma mensagem do sistema SAP.

1. No menu da aplicação lógica, selecione **descrição geral**. Reveja os **histórico de execuções** para quaisquer novas execuções para a aplicação lógica.

1. Abra a execução mais recente, que mostra a mensagem enviada do seu sistema SAP na secção de saídas de Acionador.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Gerar esquemas para artefactos no SAP

Este exemplo utiliza uma aplicação lógica que pode acionar com um pedido HTTP. A ação de SAP envia um pedido para um sistema SAP para gerar os esquemas para IDoc e BAPI especificados. Esquemas que retornam na resposta são carregadas para uma conta de integração com o conector do Azure Resource Manager.

### <a name="add-an-http-request-trigger"></a>Adicionar um acionador de pedido de HTTP

1. No portal do Azure, crie uma aplicação lógica em branco, que abre o Estruturador da aplicação lógica.

1. Na caixa de pesquisa, introduza "pedido de http" como o filtro. Partir do **Acionadores** lista, selecione **pedido de HTTP de quando é recebido**.

   ![Adicionar acionador de pedido de HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Guarde agora a aplicação lógica para que pode gerar um URL de ponto final para a aplicação lógica.
Na barra de ferramentas da estruturador, selecione **guardar**.

   O ponto final do URL agora aparece no seu acionador, por exemplo:

   ![Gerar URL para o ponto final](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Adicionar uma ação de SAP para gerar esquemas

1. No Estruturador da aplicação lógica, no acionador, selecione **novo passo**.

   ![Selecione "Novo passo"](./media/logic-apps-using-sap-connector/add-action.png)

1. Na caixa de pesquisa, introduza "sap" como o filtro. Partir do **ações** lista, selecione **gerar esquemas**.
  
   ![Selecione a ação de envio do SAP](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Em alternativa, também pode escolher o **Enterprise** separador e selecione a ação de SAP.

   ![Selecione a ação de envio SAP da guia empresarial](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Se lhe for pedido para obter detalhes de ligação, crie a ligação de SAP agora. Se a ligação já existir, continue com a próxima etapa para que possa definir a ação de SAP.

   **Crie uma ligação de SAP no local**

    1. Forneça as informações de ligação para o seu servidor SAP. Para o **Gateway de dados** propriedade, selecione o gateway de dados que criou no portal do Azure para a sua instalação do gateway.

       - Se o **tipo de início de sessão** estiver definida como **Application Server**, essas propriedades, que são apresentados normalmente opcionais, são necessárias:

         ![Criar ligação de servidor de aplicação SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

       - Se o **tipo de início de sessão** estiver definida como **grupo**, essas propriedades, que são apresentados normalmente opcionais, são necessárias:

         ![Criar a ligação ao servidor SAP mensagem](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

        Por predefinição, a rigidez de tipos é utilizado para verificar a existência de valores inválidos ao efetuar a validação de XML no esquema. Esse comportamento pode ajudar a detectar problemas anteriormente. O **escrever seguro** opção está disponível para compatibilidade com versões anteriores e só verifica o comprimento da cadeia. Saiba mais sobre o [escrever seguro opção](#safe-typing).

    1. Quando tiver terminado, selecione **criar**. 
   
       O Logic Apps configura e testa a ligação para se certificar de que a ligação funciona corretamente.

1. Forneça o caminho para o artefacto para o qual pretende gerar o esquema.

   Pode selecionar a ação de SAP o Seletor de ficheiros:

   ![Selecione a ação de SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Em alternativa, pode introduzir manualmente a ação:

   ![Introduza manualmente a ação de SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Para gerar esquemas para a mais do que um artefato, forneça os detalhes da ação de SAP para cada artefato, por exemplo:

   ![Selecione Adicionar novo item](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Mostrar dois itens](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Para obter mais informações sobre a ação de SAP, consulte [mensagem de esquemas para operações de IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Guarde a aplicação lógica. Na barra de ferramentas da estruturador, selecione **guardar**.

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

1. Na barra de ferramentas da estruturador, selecione **executar** para acionar uma execução para a aplicação lógica.

1. Abra a execução e verifica os resultados para o **gerar esquemas** ação.

   Os resultados mostram os esquemas gerados para a lista especificada de mensagens.

### <a name="upload-schemas-to-an-integration-account"></a>Carregue esquemas para uma conta de integração

Opcionalmente, pode transferir ou armazenar os esquemas gerados no repositório, como um blob, o armazenamento ou a conta de integração. Contas de integração de proporcionam uma experiência de primeira classe com outras ações de XML, para que este exemplo mostra como carregar esquemas para uma conta de integração para a mesma aplicação lógica com o conector do Azure Resource Manager.

1. No Estruturador da aplicação lógica, no acionador, selecione **novo passo**.

1. Na caixa de pesquisa, introduza "Resource Manager" como o filtro. Selecione **criar ou atualizar um recurso**.

   ![Selecione a ação do Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Introduza os detalhes para a ação, incluindo a sua subscrição do Azure, o grupo de recursos do Azure e a conta de integração. Para adicionar os tokens SAP aos campos, clique no interior das caixas para os campos e selecione na lista de conteúdo dinâmica que aparece.

    1. Abra o **adicionar novo parâmetro** lista e selecione o **localização** e **propriedades** campos.

    1. Forneça detalhes para estes campos novos, conforme mostrado neste exemplo.

       ![Introduza os detalhes para a ação do Azure Resource Manager](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   O SAP **gerar esquemas** ação gera esquemas como uma coleção, para que o estruturador adiciona automaticamente um **para cada** loop para a ação. Eis um exemplo que mostra como esta ação é apresentado:

   ![Ação de Gestor de recursos do Azure "para cada um" loop](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)  

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

1. Guarde a aplicação lógica. Na barra de ferramentas da estruturador, selecione **guardar**.

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

1. Na barra de ferramentas da estruturador, selecione **executar** para acionar manualmente a aplicação lógica.

1. Após o êxito executar, vá para a conta de integração e verifique se existem os esquemas gerados.

## <a name="enable-secure-network-communications"></a>Ativar as comunicações de rede segura

Antes de começar, certifique-se de que cumpridos listadas anteriormente [pré-requisitos](#pre-reqs):

* O gateway de dados no local é instalado num computador que está na mesma rede que seu sistema SAP.
* Para SSO, o gateway está em execução como um utilizador que está mapeado para um utilizador SAP.
* A biblioteca SNC que fornece as funções de segurança adicional está instalada no mesmo computador que o gateway de dados. Alguns exemplos incluem [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.

Para ativar SNC para seus pedidos de ou para o sistema SAP, selecione o **utilização SNC** caixa na conexão de SAP de verificação e fornecem estas propriedades:

   ![Configurar o SAP SNC numa ligação](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Propriedade | Descrição |
   |----------| ------------|
   | **Caminho de biblioteca SNC** | O nome da biblioteca SNC ou caminho relativo à localização da instalação NCo ou o caminho absoluto. Os exemplos são `sapsnc.dll` ou `.\security\sapsnc.dll` ou `c:\security\sapsnc.dll`. |
   | **SNC SSO** | Quando se liga através de SNC, a identidade SNC é normalmente utilizada para autenticar o chamador. Outra opção é substituir para que as informações de utilizador e palavra-passe podem ser utilizadas para autenticar o chamador, mas a linha ainda está encriptada. |
   | **Meu nome SNC** | Na maioria dos casos, esta propriedade pode ser omitida. A solução SNC instalada, normalmente, sabe o seu próprio nome SNC. Apenas para soluções que suportam várias identidades, poderá ter de especificar a identidade a ser utilizado para este destino específico ou um servidor. |
   | **Nome do parceiro SNC** | O nome para o SNC de back-end. |
   | **SNC qualidade de proteção** | A qualidade de serviço para ser utilizado para comunicação de SNC deste determinado destino ou o servidor. O valor predefinido é definido pelo sistema back-end. O valor máximo é definido pelo produto de segurança utilizado para SNC. |
   |||

   > [!NOTE]
   > Não defina as variáveis de ambiente SNC_LIB e SNC_LIB_64 na máquina em que tem o gateway de dados e a biblioteca SNC. Se definido, eles têm precedência sobre o valor de biblioteca SNC transmitido através do conector.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Escrever seguro

Por predefinição, quando cria a ligação de SAP, rigidez de tipos é utilizado para verificar a existência de valores inválidos ao efetuar a validação de XML no esquema. Esse comportamento pode ajudar a detectar problemas anteriormente. O **escrever seguro** opção está disponível para compatibilidade com versões anteriores e só verifica o comprimento da cadeia. Se escolher **escrever seguro**, o tipo DATS e o tipo de TIMS no SAP são tratados como cadeias de caracteres e não como seus equivalentes em XML, `xs:date` e `xs:time`, onde `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. Escrever seguro afeta o comportamento para todos os geração de esquema, a mensagem de envio para o payload "foi enviado" e a resposta "foi recebida" e o acionador. 

Quando a tipagem forte é utilizada (**escrever seguro** não está ativada), o esquema mapeia os tipos de DATS e TIMS para tipos XML mais simples:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Quando enviar mensagens usando rigidez de tipos, a resposta DATS e TIMS está em conformidade para o formato de tipo XML correspondente:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Quando **escrever seguro** é ativado, o esquema mapeia o DATS e tipos de TIMS em XML cadeias de caracteres campos com restrições de comprimento apenas, por exemplo:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Quando as mensagens são enviadas com **escrever seguro** ativada, a resposta DATS e TIMS aspeto deste exemplo:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```


## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

Seguem-se a problemas atualmente conhecidos e limitações para o conector SAP:

* Apenas uma única enviar para SAP chamada ou mensagem funciona com tRFC. O padrão de consolidação BAPI, como a realização de várias chamadas de tRFC na mesma sessão, não é suportado.
* O acionador SAP não suporta o recebimento de batch IDocs do SAP. Esta ação poderá resultar numa falha de ligação de RFC entre seu sistema SAP e o gateway de dados.
* O acionador SAP não suporta clusters de gateway de dados. Em alguns casos de ativação pós-falha, o nó de gateway de dados que se comunique com o sistema SAP pode diferir do nó ativo, o que resulta em comportamentos inesperados. Para cenários de envio, são suportados clusters de gateway de dados.
* O conector SAP atualmente não suporta cadeias de caracteres de router SAP. O gateway de dados no local tem de existir LAN do mesmo como o sistema SAP que pretende ligar.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja os [página de referência do conector](/connectors/sap/).

## <a name="next-steps"></a>Passos Seguintes

* [Ligar a sistemas no local](../logic-apps/logic-apps-gateway-connection.md) do Azure Logic Apps.
* Saiba como a validação, transformar e usar outras operações de mensagem com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md).
