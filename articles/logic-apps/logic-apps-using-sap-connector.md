---
title: Ligar aos sistemas SAP
description: Acesso e gestão de recursos SAP automatizando fluxos de trabalho com aplicações lógicas azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 3990a9d53a8e53ddd29683440917031bc1bb6448
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904648"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ligar aos sistemas SAP a partir de Aplicações Lógicas Azure

> [!IMPORTANT]
> Os anteriores connectores sap application Server e SAP Message Server estão agendados para a deprecação em 29 de fevereiro de 2020. O atual conector SAP consolida estes conectores SAP anteriores para que não tenha de alterar o tipo de ligação, é totalmente compatível com conectores anteriores, fornece muitas capacidades adicionais e continua a utilizar a biblioteca de conectores SAP .Net ( SAP NCo).
>
> Para aplicações lógicas que utilizam os conectores mais antigos, [por favor, emigre para o mais recente conector](#migrate) antes da data de depreciação. Caso contrário, estas aplicações lógicas sofrerão falhas de execução e não poderão enviar mensagens para o seu sistema SAP.

Este artigo mostra como pode aceder aos seus recursos SAP no local a partir de dentro de uma aplicação lógica utilizando o conector SAP. O conector funciona com os lançamentos clássicos da SAP, tais como sistemas R/3 e ECC no local. O conector também permite a integração com os mais recentes sistemas SAP baseados em HANA, como S/4 HANA, quer estejam hospedados no local ou na nuvem. O conector SAP suporta a integração de mensagens ou dados de e para sistemas baseados em SAP NetWeaver através de Documento Intermédio (IDoc), Interface de Programação de Aplicações empresariais (BAPI) ou Chamada de Função Remota (RFC).

O conector SAP utiliza a [biblioteca SAP.NET Connector (NCo)](https://support.sap.com/en/product/connectors/msnet.html) e fornece estas ações:

* **Envie mensagem para sAP**: Envie IDoc através do tRFC, ligue para as funções BAPI através do RFC ou ligue para rFC/tRFC nos sistemas SAP.
* **Quando uma mensagem for recebida do SAP**: Receba iDoc através do tRFC, ligue para as funções BAPI através do tRFC ou ligue para o RFC/tRFC nos sistemas SAP.
* **Gerar schemas**: Gere schemas para os artefactos SAP para IDoc, BAPI ou RFC.

Para estas operações, o conector SAP suporta a autenticação básica através de nomes de utilizador e palavras-passe. O conector também suporta comunicações de [rede seguras (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). O SNC pode ser utilizado para o SAP NetWeaver individual sign-on (SSO) ou para capacidades de segurança adicionais fornecidas por um produto de segurança externo.

O conector SAP integra-se com sistemas SAP no local através da [porta de dados no local](../logic-apps/logic-apps-gateway-connection.md). Nos cenários de envio, por exemplo, quando uma mensagem é enviada de uma aplicação lógica para um sistema SAP, o portal de dados funciona como cliente RFC e reencaminha os pedidos recebidos da aplicação lógica para o SAP. Da mesma forma, nos cenários de receção, o portal de dados funciona como um servidor RFC que recebe pedidos do SAP e os reencaminha para a aplicação lógica.

Este artigo mostra como criar aplicações lógicas de exemplo que se integram com o SAP enquanto cobrem os cenários de integração previamente descritos. Para aplicações lógicas que utilizam os conectores SAP mais antigos, este artigo mostra como migrar as suas aplicações lógicas para o mais recente conector SAP.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar este artigo, precisa destes itens:

* Uma subscrição do Azure. Se você ainda não tiver uma assinatura do Azure, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* A aplicação lógica de onde pretende aceder ao seu sistema SAP e um gatilho que inicia o fluxo de trabalho da sua aplicação lógica. Se é novo em aplicações lógicas, veja [O que são as Aplicações Lógicas Do Azure e](../logic-apps/logic-apps-overview.md) o [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* O servidor de [aplicação SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou o servidor de [mensagens SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Faça o download e instale a mais recente [porta de dados no local](https://www.microsoft.com/download/details.aspx?id=53127) em qualquer computador no local. Certifique-se de configurar a sua porta de entrada no portal Azure antes de continuar. O portal ajuda-o a aceder de forma segura aos dados e recursos no local. Para mais informações, consulte [Instale uma porta de dados no local para aplicações lógicas azure](../logic-apps/logic-apps-gateway-install.md).

* Se utilizar o SNC com SSO, certifique-se de que o portal está a funcionar como um utilizador que está mapeado contra o utilizador SAP. Para alterar a conta predefinida, selecione **a conta De alterar**, e introduza as credenciais do utilizador.

  ![Alterar a conta gateway](./media/logic-apps-using-sap-connector/gateway-account.png)

* Se ativar o SNC com um produto de segurança externo, copie a biblioteca SNC ou os ficheiros na mesma máquina onde o portal está instalado. Alguns exemplos de produtos SNC incluem [sapseculib,](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)Kerberos e NTLM.

* Descarregue e instale a mais recente biblioteca de clientes SAP, que é atualmente [O Conector SAP (NCo 3.0) para a Microsoft .NET 3.0.22.0 compilada com .NET Framework 4.0 - Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), no mesmo computador que o gateway de dados no local. Instale esta versão ou posteriormente por estas razões:

  * As versões Anteriores da SAP NCo podem ficar num impasse quando mais de uma mensagem IDoc é enviada ao mesmo tempo. Esta condição bloqueia todas as mensagens posteriores que são enviadas para o destino SAP, o que faz com que as mensagens se estem.
  
  * A porta de dados no local funciona apenas em sistemas de 64 bits. Caso contrário, obtém um erro de "má imagem" porque o serviço de hospedagem de gateway de dados não suporta conjuntos de 32 bits.
  
  * Tanto o serviço de hospedagem de gateway de dados como o Adaptador Microsoft SAP utilizam .NET Framework 4.5. O SAP NCo para .NET Framework 4.0 trabalha com processos que utilizam o tempo de execução .NET 4.0 a 4.7.1. O SAP NCo para .NET Framework 2.0 trabalha com processos que utilizam o tempo de funcionamento .NET 2.0 a 3.5, mas já não trabalha com a mais recente porta de dados no local.

* O conteúdo da mensagem que pode enviar para o seu servidor SAP, como um ficheiro IDoc de amostra, deve estar no formato XML e incluir o espaço de nome para a ação SAP que pretende utilizar.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrar para o conector atual

1. Se ainda não o fez, atualize o portal [de dados no local](https://www.microsoft.com/download/details.aspx?id=53127) para que tenha a versão mais recente. Para mais informações, consulte [Instale uma porta de dados no local para aplicações lógicas azure](../logic-apps/logic-apps-gateway-install.md).

1. Na aplicação lógica que utiliza o conector SAP mais antigo, elimine a ação **Enviar para SAP.**

1. A partir do mais recente conector SAP, adicione a mensagem Enviar à ação **SAP.** Antes de poder utilizar esta ação, recrie a ligação ao seu sistema SAP.

1. Quando tiver terminado, salve seu aplicativo lógico.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Enviar mensagem para sap

Este exemplo utiliza uma aplicação lógica que pode desencadear com um pedido HTTP. A aplicação lógica envia um IDoc para um servidor SAP e devolve uma resposta ao solicitador que chamou a aplicação lógica.

### <a name="add-an-http-request-trigger"></a>Adicione um gatilho de pedido HTTP

Em aplicativos lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico e começa a executar o fluxo de trabalho do aplicativo.

Neste exemplo, cria uma aplicação lógica com um ponto final no Azure para que possa enviar *pedidos HTTP POST* para a sua aplicação lógica. Quando a sua aplicação lógica recebe estes pedidos HTTP, o gatilho dispara e corre o próximo passo no seu fluxo de trabalho.

1. No [portal Azure, crie](https://portal.azure.com)uma aplicação lógica em branco, que abre o Logic App Designer.

1. Na caixa de pesquisa, introduza `http request` como filtro. A partir da lista **de Gatilhos,** selecione **Quando um pedido HTTP for recebido**.

   ![Adicionar gatilho de pedido HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Agora guarde a sua aplicação lógica para que possa gerar um URL de ponto final para a sua aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

   O URL do ponto final aparece agora no gatilho, por exemplo:

   ![Gerar URL para ponto final](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Adicione uma ação SAP

Em aplicativos lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa no fluxo de trabalho que segue um gatilho ou outra ação. Se ainda não adicionou um gatilho à sua aplicação lógica e quer seguir este exemplo, [adicione o gatilho descrito nesta secção](#add-trigger).

1. No Logic App Designer, sob o gatilho, selecione **Novo passo**.

   ![Adicione um novo passo para a aplicação lógica](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Na caixa de pesquisa, introduza `sap` como filtro. A partir da lista **de Ações,** selecione **Enviar mensagem para SAP**.
  
   ![Selecione ação "Enviar mensagem para SAP"](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Ou, pode selecionar o separador **Enterprise** e selecionar a ação SAP.

   ![Selecione ação "Enviar mensagem para SAP" a partir do separador Enterprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Se a sua ligação já existir, continue com o próximo passo para que possa configurar a sua ação SAP. No entanto, se for solicitado para obter detalhes de ligação, forneça as informações para que possa criar uma ligação ao seu servidor SAP no local agora.

   1. Forneça um nome para a ligação.

   1. Na secção **Data Gateway,** em **Subscrição,** primeiro selecione a subscrição do Azure para o recurso gateway que criou no portal Azure para a sua instalação de gateway. 
   
   1. Em Gateway **de Ligação,** selecione o seu recurso gateway.

   1. Continue a fornecer informações sobre a ligação. Para a propriedade **Logon Type,** siga o passo com base no facto de a propriedade estar definida para o Servidor de **Aplicação** ou **Grupo:**
   
      * Para o Servidor de **Aplicações,** estas propriedades, que geralmente parecem opcionais, são necessárias:

        ![Criar a ligação do servidor de aplicações SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para **o Grupo,** estas propriedades, que geralmente parecem opcionais, são necessárias:

        ![Criar a ligação do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Por predefinição, a dactilografia forte é usada para verificar valores inválidos executando a validação XML contra o esquema. Este comportamento pode ajudá-lo a detetar problemas mais cedo. A opção **"Digito seguro"** está disponível para a retrocompatibilidade e verifica apenas o comprimento da corda. Saiba mais sobre a [opção Dactilografia Segura](#safe-typing).

   1. Quando tiver terminado, selecione **criar**.

      As Aplicações Lógicas configuram e testam a sua ligação para se certificar de que a ligação funciona corretamente.

1. Agora encontre e selecione uma ação do seu servidor SAP.

   1. Na caixa **de ação SAP,** selecione o ícone da pasta. A partir da lista de ficheiros, encontre e selecione a mensagem SAP que pretende utilizar. Para navegar na lista, use as setas.

      Este exemplo seleciona um IDoc com o tipo **Encomendas.**

      ![Localizar e selecionar ação IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Se não encontrar a ação que deseja, pode entrar manualmente num caminho, por exemplo:

      ![Manualmente, fornecer caminho para a ação do IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Forneça o valor para a **Ação SAP** através do editor de expressão. Assim, pode usar a mesma ação para diferentes tipos de mensagens.

      Para obter mais informações sobre as operações do IDoc, consulte [as mensagens para operações do IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Clique no interior da caixa **de mensagem** de entrada para que a lista de conteúdos dinâmicos apareça. A partir dessa lista, em quando **um pedido HTTP é recebido,** selecione o campo **Corpo.**

      Este passo inclui o conteúdo do corpo do gatilho http request e envia essa saída para o seu servidor SAP.

      ![Selecione a propriedade "Body" a partir do gatilho](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Quando terminar, a sua ação SAP parece este exemplo:

      ![Complete a ação SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Adicione uma ação de resposta HTTP

Adicione agora uma ação de resposta ao fluxo de trabalho da sua aplicação lógica e inclua a saída da ação SAP. Desta forma, a sua aplicação lógica devolve os resultados do seu servidor SAP ao solicitador original.

1. No Logic App Designer, sob a ação SAP, selecione **Novo passo**.

1. Na caixa de pesquisa, introduza `response` como filtro. Na lista **de Ações,** selecione **Resposta**.

1. Clique na caixa **Body** para que a lista de conteúdos dinâmicos apareça. A partir dessa lista, em **Enviar mensagem para O SAP,** selecione o campo **Body.**

   ![Ação Completa do SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Guarde a aplicação lógica.

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

1. Se a sua aplicação lógica ainda não estiver ativada, no menu de aplicações lógicas, selecione **Visão Geral**. Na barra de ferramentas, selecione **habilitar**.

1. Na barra de ferramentas do designer, selecione **executar**. Essa etapa inicia manualmente o aplicativo lógico.

1. Desencadeie a sua aplicação lógica enviando um pedido HTTP POST para o URL no seu gatilho http Request.
Inclua o conteúdo da sua mensagem com o seu pedido. Para enviar o pedido, pode utilizar uma ferramenta como [o Carteiro](https://www.getpostman.com/apps).

   Para este artigo, o pedido envia um ficheiro IDoc, que deve estar em formato XML e incluir o espaço de nome para a ação SAP que está a usar, por exemplo:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Depois de enviar o seu pedido HTTP, aguarde a resposta da sua aplicação lógica.

   > [!NOTE]
   > A sua aplicação lógica poderá esgotar-se se todos os passos necessários para a resposta não terminarem dentro do prazo de [pedido](./logic-apps-limits-and-config.md). Se esta condição acontecer, os pedidos podem ser bloqueados. Para ajudá-lo a diagnosticar problemas, saiba como pode [verificar e monitorizar as suas aplicações lógicas.](../logic-apps/monitor-logic-apps.md)

Criou agora uma aplicação lógica que pode comunicar com o seu servidor SAP. Agora que criou uma ligação SAP para a sua aplicação lógica, pode explorar outras ações disponíveis do SAP, como BAPI e RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Receber mensagem do SAP

Este exemplo utiliza uma aplicação lógica que dispara quando a aplicação recebe uma mensagem de um sistema SAP.

### <a name="add-an-sap-trigger"></a>Adicione um gatilho SAP

1. No portal Azure, crie uma aplicação lógica em branco, que abre o Logic App Designer.

1. Na caixa de pesquisa, introduza `sap` como filtro. A partir da lista **de Gatilhos,** selecione **Quando uma mensagem é recebida do SAP**.

   ![Adicionar gatilho SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Ou, pode selecionar o separador **Enterprise** e, em seguida, selecionar o gatilho:

   ![Adicione o gatilho SAP do separador Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Se a sua ligação já existir, continue com o próximo passo para que possa configurar a sua ação SAP. No entanto, se for solicitado para obter detalhes de ligação, forneça as informações para que possa criar uma ligação ao seu servidor SAP no local agora.

   1. Forneça um nome para a ligação.

   1. Na secção **Data Gateway,** em **Subscrição,** primeiro selecione a subscrição do Azure para o recurso gateway que criou no portal Azure para a sua instalação de gateway. 

   1. Em Gateway **de Ligação,** selecione o seu recurso gateway.

   1. Continue a fornecer informações sobre a ligação. Para a propriedade **Logon Type,** siga o passo com base no facto de a propriedade estar definida para o Servidor de **Aplicação** ou **Grupo:**

      * Para o Servidor de **Aplicações,** estas propriedades, que geralmente parecem opcionais, são necessárias:

        ![Criar a ligação do servidor de aplicações SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para **o Grupo,** estas propriedades, que geralmente parecem opcionais, são necessárias:

        ![Criar a ligação do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Por predefinição, a dactilografia forte é usada para verificar valores inválidos executando a validação XML contra o esquema. Este comportamento pode ajudá-lo a detetar problemas mais cedo. A opção **"Digito seguro"** está disponível para a retrocompatibilidade e verifica apenas o comprimento da corda. Saiba mais sobre a [opção Dactilografia Segura](#safe-typing).

   1. Quando tiver terminado, selecione **criar**.

      As Aplicações Lógicas configuram e testam a sua ligação para se certificar de que a ligação funciona corretamente.

1. Forneça os parâmetros necessários com base na configuração do sistema SAP.

   Você pode opcionalmente fornecer uma ou mais ações SAP. Esta lista de ações especifica as mensagens que o gatilho recebe do seu servidor SAP através do portal de dados. Uma lista vazia especifica que o gatilho recebe todas as mensagens. Se a lista tiver mais de uma mensagem, o gatilho recebe apenas as mensagens especificadas na lista. Quaisquer outras mensagens enviadas do seu servidor SAP são rejeitadas pelo portal.

   Pode selecionar uma ação SAP a partir do apanhador de ficheiros:

   ![Adicione ação SAP à aplicação lógica](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Ou pode especificar manualmente uma ação:

   ![Entrar manualmente na ação SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Aqui está um exemplo que mostra como a ação aparece quando configura o gatilho para receber mais do que uma mensagem.

   ![Exemplo de gatilho que recebe várias mensagens](media/logic-apps-using-sap-connector/example-trigger.png)

   Para mais informações sobre a ação do SAP, consulte [Message schemas para operações do IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Agora guarde a sua aplicação lógica para que possa começar a receber mensagens do seu sistema SAP. Na barra de ferramentas do designer, selecione **salvar**.

A sua aplicação lógica está agora pronta para receber mensagens do seu sistema SAP.

> [!NOTE]
> O gatilho SAP não é um gatilho de sondagens, mas é um gatilho baseado em webhook. O gatilho só é chamado do portal quando existe uma mensagem, pelo que não é necessária qualquer sondagem.

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

1. Para desencadear a sua aplicação lógica, envie uma mensagem do seu sistema SAP.

1. No menu de aplicativos lógicos, selecione **Visão Geral**. Reveja o **histórico de Runs** para quaisquer novas correções para a sua aplicação lógica.

1. Abra a execução mais recente, que mostra a mensagem enviada do seu sistema SAP na secção de saídas do gatilho.

## <a name="receive-idoc-packets-from-sap"></a>Receba pacotes IDOC da SAP

Pode configurar o SAP para [enviar IDOCs em pacotes,](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html)que são lotes ou grupos de IDOCs. Para receber pacotes IDOC, o conector SAP, e especificamente o gatilho, não precisa de configuração extra. No entanto, para processar cada item num pacote IDOC após o gatilho receber o pacote, são necessários alguns passos adicionais para dividir o pacote em IDOCs individuais.

Aqui está um exemplo que mostra como extrair IDOCs individuais de um pacote usando a [função`xpath()`:](./workflow-definition-language-functions-reference.md#xpath)

1. Antes de começar, precisa de uma aplicação lógica com um gatilho SAP. Se ainda não tem esta aplicação lógica, siga os passos anteriores neste tópico para [configurar uma aplicação lógica com um gatilho SAP](#receive-from-sap).

   Por exemplo:

   ![Adicione o gatilho SAP à aplicação lógica](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Obtenha o espaço de nome raiz do XML IDOC que a sua aplicação lógica recebe do SAP. Para extrair este espaço de nome do documento XML, adicione um passo que cria uma variável de cadeia local e armazena esse espaço de nome usando uma expressão `xpath()`:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Obtenha espaço de nome raiz do IDOC](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Para extrair um IDOC individual, adicione um passo que cria uma variável de matriz e armazena a coleção IDOC utilizando outra expressão `xpath()`:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Obtenha uma variedade de itens](./media/logic-apps-using-sap-connector/get-array.png)

   A variável matriz disponibiliza cada IDOC para que a sua aplicação lógica seja processada individualmente enumerando sobre a coleção. Neste exemplo, a aplicação lógica transfere cada IDOC para um servidor SFTP utilizando um loop:

   ![Enviar IDOC para servidor SFTP](./media/logic-apps-using-sap-connector/loop-batch.png)

   Cada IDOC deve incluir o espaço de nome raiz, razão pela qual o conteúdo do ficheiro é embrulhado dentro de um elemento `<Receive></Receive` juntamente com o espaço de nome raiz antes de enviar o IDOC para a aplicação a jusante, ou servidor SFTP neste caso.

Você pode usar o modelo quickstart para este padrão selecionando este modelo no Logic App Designer quando você criar uma nova aplicação lógica.

![Selecione modelo de aplicativo lógico de lote](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Gerar esquemia para artefactos em SAP

Este exemplo utiliza uma aplicação lógica que pode desencadear com um pedido HTTP. A ação SAP envia um pedido a um sistema SAP para gerar os esquemas para iDoc e BAPI especificados. Os schemas que regressam na resposta são enviados para uma conta de integração utilizando o conector Do Gestor de Recursos Azure.

### <a name="add-an-http-request-trigger"></a>Adicione um gatilho de pedido HTTP

1. No portal Azure, crie uma aplicação lógica em branco, que abre o Logic App Designer.

1. Na caixa de pesquisa, introduza `http request` como filtro. A partir da lista **de Gatilhos,** selecione **Quando um pedido HTTP for recebido**.

   ![Adicionar gatilho de pedido HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Agora guarde a sua aplicação lógica para que possa gerar um URL de ponto final para a sua aplicação lógica.
Na barra de ferramentas do designer, selecione **salvar**.

   O URL do ponto final aparece agora no gatilho, por exemplo:

   ![Gerar URL para ponto final](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Adicione uma ação SAP para gerar schemas

1. No Logic App Designer, sob o gatilho, selecione **Novo passo**.

   ![Adicione um novo passo para a aplicação lógica](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Na caixa de pesquisa, introduza `sap` como filtro. Na lista **de Ações,** selecione **Generate schemas**.
  
   ![Adicione ação "Gerar schemas" à aplicação lógica](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Ou, pode selecionar o separador **Enterprise** e selecionar a ação SAP.

   ![Selecione SAP enviar ação do separador Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Se a sua ligação já existir, continue com o próximo passo para que possa configurar a sua ação SAP. No entanto, se for solicitado para obter detalhes de ligação, forneça as informações para que possa criar uma ligação ao seu servidor SAP no local agora.

   1. Forneça um nome para a ligação.

   1. Na secção **Data Gateway,** em **Subscrição,** primeiro selecione a subscrição do Azure para o recurso gateway que criou no portal Azure para a sua instalação de gateway. 
   
   1. Em Gateway **de Ligação,** selecione o seu recurso gateway.

   1. Continue a fornecer informações sobre a ligação. Para a propriedade **Logon Type,** siga o passo com base no facto de a propriedade estar definida para o Servidor de **Aplicação** ou **Grupo:**
   
      * Para o Servidor de **Aplicações,** estas propriedades, que geralmente parecem opcionais, são necessárias:

        ![Criar a ligação do servidor de aplicações SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para **o Grupo,** estas propriedades, que geralmente parecem opcionais, são necessárias:

        ![Criar a ligação do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Por predefinição, a dactilografia forte é usada para verificar valores inválidos executando a validação XML contra o esquema. Este comportamento pode ajudá-lo a detetar problemas mais cedo. A opção **"Digito seguro"** está disponível para a retrocompatibilidade e verifica apenas o comprimento da corda. Saiba mais sobre a [opção Dactilografia Segura](#safe-typing).

   1. Quando tiver terminado, selecione **criar**.

      As Aplicações Lógicas configuram e testam a sua ligação para se certificar de que a ligação funciona corretamente.

1. Forneça o caminho para o artefacto para o qual pretende gerar o esquema.

   Pode selecionar a ação SAP a partir do apanhador de ficheiros:

   ![Selecione ação SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Ou pode entrar manualmente na ação:

   ![Entrar manualmente na ação SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Para gerar esquemia para mais de um artefacto, forneça os detalhes de ação SAP para cada artefacto, por exemplo:

   ![Selecione Adicionar novo item](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Mostrar dois itens](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Para obter mais informações sobre a ação SAP, consulte [Message schemas para operações iDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

1. Na barra de ferramentas de design, selecione **Run** para desencadear uma corrida para a sua aplicação lógica.

1. Abra a corrida e verifique as saídas para a ação **Generate schemas.**

   As saídas mostram os esquemas gerados para a lista especificada de mensagens.

### <a name="upload-schemas-to-an-integration-account"></a>Faça upload de schemas para uma conta de integração

Opcionalmente, pode descarregar ou armazenar os esquemas gerados em repositórios, tais como uma conta blob, armazenamento ou integração. As contas de integração proporcionam uma experiência de primeira classe com outras ações xml, por isso este exemplo mostra como enviar schemas para uma conta de integração para a mesma aplicação lógica usando o conector Do Gestor de Recursos Azure.

1. No Logic App Designer, sob o gatilho, selecione **Novo passo**.

1. Na caixa de pesquisa, introduza `Resource Manager` como filtro. Selecione **Criar ou atualizar um recurso**.

   ![Selecione ação do Gestor de Recursos Azure](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Insira os detalhes para a ação, incluindo a sua subscrição Azure, grupo de recursos Azure e conta de integração. Para adicionar tokens SAP aos campos, clique no interior das caixas para esses campos e selecione a partir da lista de conteúdos dinâmicos que aparecem.

   1. Abra a **lista de novos parâmetros** e selecione os campos **Localização** e **Propriedades.**

   1. Forneça detalhes para estes novos campos, como mostra este exemplo.

      ![Introduza detalhes para a ação do Gestor de Recursos do Azure](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   A ação SAP **Generate schemas** gera schemas como uma coleção, por isso o designer adiciona automaticamente um **Para cada** loop à ação. Aqui está um exemplo que mostra como esta ação aparece:

   ![Ação do Gestor de Recursos Azure com loop "para cada"](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Os schemas usam o formato codificado base64. Para fazer o upload dos esquemas para uma conta de integração, devem ser descodificados utilizando a função `base64ToString()`. Aqui está um exemplo que mostra o código para o elemento `"properties"`:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

1. Na barra de ferramentas de design, selecione **Executar** para acionar manualmente a sua aplicação lógica.

1. Depois de uma corrida bem sucedida, vá à conta de integração, e verifique se os schemas gerados existem.

## <a name="enable-secure-network-communications"></a>Ativar comunicações de rede seguras

Antes de começar, certifique-se de que cumpriu os [pré-requisitos](#pre-reqs)previamente listados:

* O portal de dados no local está instalado numa máquina que está na mesma rede que o seu sistema SAP.
* Para o SSO, o portal está a funcionar como um utilizador que está mapeado para um utilizador SAP.
* A biblioteca SNC que fornece as funções de segurança adicionais está instalada na mesma máquina que a porta de dados. Alguns exemplos incluem [sapseculib,](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)Kerberos e NTLM.

   Para ativar o SNC para os seus pedidos de ou para o sistema SAP, selecione a caixa de verificação **Use SNC** na ligação SAP e forneça estas propriedades:

   ![Configure scSNC Em conexão](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Propriedade | Descrição |
   |----------| ------------|
   | **Caminho da Biblioteca SNC** | O nome ou caminho da biblioteca SNC em relação à localização de instalação nCo ou caminho absoluto. Exemplos são `sapsnc.dll` ou `.\security\sapsnc.dll` ou `c:\security\sapsnc.dll`. |
   | **SNC SSO** | Quando se liga através do SNC, a identidade SNC é normalmente utilizada para autenticar o chamador. Outra opção é anular para que as informações do utilizador e da palavra-passe possam ser utilizadas para autenticar o chamador, mas a linha ainda está encriptada. |
   | **SNC Meu Nome** | Na maioria dos casos, esta propriedade pode ser omitida. A solução SNC instalada geralmente conhece o seu próprio nome SNC. Apenas para soluções que suportem múltiplas identidades, poderá ser necessário especificar a identidade a utilizar para este destino ou servidor específico. |
   | **Nome do parceiro SNC** | O nome do SNC de back-end. |
   | **Qualidade de Proteção snc** | A qualidade do serviço a utilizar para a comunicação SNC deste destino ou servidor em particular. O valor predefinido é definido pelo sistema back-end. O valor máximo é definido pelo produto de segurança utilizado para o SNC. |
   |||

   > [!NOTE]
   > Não coloque as variáveis ambientais SNC_LIB e SNC_LIB_64 na máquina onde tem a porta de dados e a biblioteca SNC. Se definidos, têm precedência sobre o valor da biblioteca SNC passado pelo conector.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Dactilografia segura

Por predefinição, quando cria a sua ligação SAP, a dactilografia forte é usada para verificar valores inválidos executando validação XML contra o esquema. Este comportamento pode ajudá-lo a detetar problemas mais cedo. A opção **"Digito seguro"** está disponível para a retrocompatibilidade e verifica apenas o comprimento da corda. Se escolher a **Digitação Segura,** o tipo DATS e o tipo TIMS em SAP são tratados como cordas e não como os seus equivalentes XML, `xs:date` e `xs:time`, onde `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. A dactilografia segura afeta o comportamento de toda a geração schema, a mensagem de envio tanto para a carga útil "enviada" como para a resposta "recebida" e para o gatilho. 

Quando se trata de uma dactilografia forte **(A dactilografia segura** não está ativada), o esquema mapeia os tipos DATS e TIMS para tipos xml mais simples:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Quando envia mensagens utilizando uma dactilografia forte, a resposta DATS e TIMS cumpre o formato do tipo XML correspondente:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Quando a **dactilografia segura** está ativada, o esquema mapeia os tipos DATS e TIMS para campos de cordas XML apenas com restrições de comprimento, por exemplo:

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

Quando as mensagens são enviadas com **Dactilografia Segura** ativada, a resposta DATS e TIMS parece-se com este exemplo:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Cenários avançados

### <a name="confirm-transaction-explicitly"></a>Confirmar a transação explicitamente

Quando envia transações para SAP a partir de Aplicações Lógicas, esta troca acontece em duas etapas, conforme descrito no documento SAP, [Transactional RFC Server Programs](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Por predefinição, a ação **Send to SAP** manuseia tanto os passos para a transferência de funções como para a confirmação da transação numa única chamada. O conector SAP dá-lhe a opção de dissociar estes passos. Pode enviar um IDOC e, em vez de confirmar automaticamente a transação, pode utilizar a ação explícita de ID de **transação Confirm.**

Esta capacidade de dissociar a confirmação de ID de transação é útil quando não quer duplicar transações no SAP, por exemplo, em cenários em que falhas podem ocorrer devido a causas como problemas de rede. Ao confirmar o ID de transação separadamente, a transação só é concluída uma vez no seu sistema SAP.

Aqui está um exemplo que mostra este padrão:

1. Crie uma aplicação lógica em branco e adicione um gatilho HTTP.

1. A partir do conector SAP, adicione a ação **Send IDOC.** Forneça os detalhes para o IDOC que envia para o seu sistema SAP.

1. Para confirmar explicitamente o ID de transação num passo separado, no campo **Confirmar TID,** selecione **No**. Para o campo GUIA DE ID de **Transação** opcional, pode especificar manualmente o valor ou ter o conector a gerar e devolver este GUID automaticamente na resposta da ação Send IDOC.

   ![Enviar propriedades de ação IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Para confirmar explicitamente o ID da transação, adicione a ação de ID de **transação Confirmar.** Clique no interior da caixa de ID de **transação** para que a lista de conteúdos dinâmicos apareça. A partir dessa lista, selecione o valor de ID de **transação** que é devolvido da ação **Enviar IDOC.**

   ![Confirmar ação de ID de transação](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Após este passo, a transação atual é marcada completa em ambas as extremidades, no lado do conector SAP e no lado do sistema SAP.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

Aqui estão as questões e limitações atualmente conhecidas para o conector SAP:

* O gatilho SAP não suporta clusters de gateway de dados. Em alguns casos de failover, o nó de gateway de dados que comunica com o sistema SAP pode diferir do nó ativo, o que resulta em comportamento inesperado. Para cenários de envio, os clusters de gateway de dados são suportados.

* O conector SAP não suporta atualmente cordas de router SAP. O portal de dados no local deve existir no mesmo LAN que o sistema SAP que pretende ligar.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência do conector](/connectors/sap/).

## <a name="next-steps"></a>Passos seguintes

* [Ligue-se aos sistemas no local](../logic-apps/logic-apps-gateway-connection.md) a partir de Aplicações Lógicas Azure.
* Aprenda a validar, transformar e utilizar outras operações de mensagens com o Pacote de [Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Saiba mais sobre outros [conectores de Aplicações Lógicas.](../connectors/apis-list.md)
