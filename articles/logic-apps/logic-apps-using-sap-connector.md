---
title: Conectar-se a sistemas SAP-aplicativos lógicos do Azure
description: Acesse e gerencie recursos do SAP automatizando fluxos de trabalho com aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 90348ad05879aff75dadab85af4e905d92228a2d
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287114"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Conectar-se a sistemas SAP de aplicativos lógicos do Azure

> [!IMPORTANT]
> Os conectores anteriores do servidor de aplicativos SAP e do SAP Message Server estão agendados para substituição em 29 de fevereiro de 2020. O conector SAP atual consolida esses conectores do SAP anteriores para que você não precise alterar o tipo de conexão, é totalmente compatível com conectores anteriores, fornece muitos recursos adicionais e continua a usar a biblioteca de conectores do SAP .net ( SAP NCo).
>
> Para aplicativos lógicos que usam os conectores mais antigos, [migre para o conector mais recente](#migrate) antes da data de reprovação. Caso contrário, esses aplicativos lógicos sofrerão falhas de execução e não poderão enviar mensagens para o sistema SAP.

Este artigo mostra como você pode acessar seus recursos SAP locais de dentro de um aplicativo lógico usando o conector SAP. O conector funciona com as versões clássicas do SAP, como sistemas de R/3 e ECC locais. O conector também habilita a integração com os sistemas SAP mais recentes baseados em HANA da SAP, como S/4 HANA, se eles estão hospedados localmente ou na nuvem. O conector SAP dá suporte à integração de mensagens ou de dados de e para sistemas baseados no SAP NetWeaver por meio de IDoc (documento intermediário), BAPI (interface de programação de aplicativo de negócios) ou RFC (chamada de função remota).

O conector SAP usa a [biblioteca do NCo (conector do SAP .net)](https://support.sap.com/en/product/connectors/msnet.html) e fornece estas ações:

* **Enviar mensagem para SAP**: enviar IDOC sobre tRFC, chamar funções BAPI sobre a RFC ou chamar RFC/tRFC em sistemas SAP.
* **Quando uma mensagem é recebida do SAP**: receber IDOC sobre tRFC, chamar funções BAPI em tRFC ou chamar RFC/tRFC em sistemas SAP.
* **Gerar esquemas**: gere esquemas para os artefatos do SAP para IDOC, BAPI ou RFC.

Para essas operações, o conector SAP dá suporte à autenticação básica por meio de nomes de acessações e senhas. O conector também dá suporte a [SNC (comunicações de rede segura)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). O SNC pode ser usado para SSO (logon único) do SAP NetWeaver ou para recursos de segurança adicionais fornecidos por um produto de segurança externo.

O conector SAP se integra com sistemas SAP locais por meio do [Gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Em cenários de envio, por exemplo, quando uma mensagem é enviada de um aplicativo lógico para um sistema SAP, o gateway de dados atua como um cliente RFC e encaminha as solicitações recebidas do aplicativo lógico para o SAP. Da mesma forma, em cenários de recebimento, o gateway de dados atua como um servidor RFC que recebe solicitações do SAP e as encaminha para o aplicativo lógico.

Este artigo mostra como criar aplicativos lógicos de exemplo que se integram ao SAP e abrangem os cenários de integração descritos anteriormente. Para aplicativos lógicos que usam os conectores do SAP mais antigos, este artigo mostra como migrar seus aplicativos lógicos para o conector SAP mais recente.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar este artigo, você precisará destes itens:

* Uma subscrição do Azure. Se você ainda não tiver uma assinatura do Azure, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico do qual você deseja acessar seu sistema SAP e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. Se você for novo em aplicativos lógicos, consulte [o que é o aplicativo lógico do Azure? e o](../logic-apps/logic-apps-overview.md) [início rápido: criar seu primeiro aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Servidor de aplicativos SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou [servidor de mensagens SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Baixe e instale o [Gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127) mais recente em qualquer computador local. Certifique-se de configurar seu gateway na portal do Azure antes de continuar. O gateway ajuda você a acessar com segurança os dados e recursos locais. Para obter mais informações, consulte [instalar um gateway de dados local para aplicativos lógicos do Azure](../logic-apps/logic-apps-gateway-install.md).

* Se você usar o SNC com SSO, verifique se o gateway está sendo executado como um usuário mapeado no usuário SAP. Para alterar a conta padrão, selecione **alterar conta**e insira as credenciais do usuário.

  ![Alterar conta de gateway](./media/logic-apps-using-sap-connector/gateway-account.png)

* Se você habilitar o SNC com um produto de segurança externo, copie a biblioteca de SNC ou os arquivos no mesmo computador em que o gateway está instalado. Alguns exemplos de produtos do SNC incluem [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.

* Baixe e instale a biblioteca de cliente SAP mais recente, que é atualmente o [conector SAP (NCo 3,0) para Microsoft .net 3.0.22.0 compilado com .NET Framework 4,0-Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), no mesmo computador que o gateway de dados local. Instale esta versão ou posterior por estes motivos:

  * Versões anteriores do SAP NCo podem ser bloqueadas quando mais de uma mensagem IDoc é enviada ao mesmo tempo. Essa condição bloqueia todas as mensagens posteriores enviadas ao destino SAP, o que faz com que as mensagens expirem o tempo limite.
  
  * O gateway de dados local é executado somente em sistemas de 64 bits. Caso contrário, você obterá um erro de "imagem inadequada" porque o serviço de host do gateway de dados não dá suporte a assemblies de 32 bits.
  
  * O serviço de host do gateway de dados e o adaptador SAP da Microsoft usam .NET Framework 4,5. O SAP NCo para .NET Framework 4,0 funciona com processos que usam o .NET Runtime 4,0 a 4.7.1. O SAP NCo para .NET Framework 2,0 funciona com processos que usam o tempo de execução do .NET 2,0 para 3,5, mas não funciona mais com o gateway de dados local mais recente.

* O conteúdo da mensagem que você pode enviar para o servidor SAP, como um arquivo IDoc de exemplo, deve estar no formato XML e incluir o namespace para a ação SAP que você deseja usar.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrar para o conector atual

1. Se você ainda não fez isso, atualize seu [Gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127) para que você tenha a versão mais recente. Para obter mais informações, consulte [instalar um gateway de dados local para aplicativos lógicos do Azure](../logic-apps/logic-apps-gateway-install.md).

1. No aplicativo lógico que usa o conector do SAP mais antigo, exclua a ação **Enviar para SAP** .

1. A partir do conector SAP mais recente, adicione a ação **Enviar mensagem para SAP** . Para poder usar essa ação, recrie a conexão com o sistema SAP.

1. Quando tiver terminado, salve seu aplicativo lógico.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Enviar mensagem para o SAP

Este exemplo usa um aplicativo lógico que você pode disparar com uma solicitação HTTP. O aplicativo lógico envia um IDoc para um servidor SAP e retorna uma resposta para o solicitante que chamou o aplicativo lógico.

### <a name="add-an-http-request-trigger"></a>Adicionar um gatilho de solicitação HTTP

Em aplicativos lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico e começa a executar o fluxo de trabalho do aplicativo.

Neste exemplo, você cria um aplicativo lógico com um ponto de extremidade no Azure para que você possa enviar *solicitações HTTP post* para seu aplicativo lógico. Quando seu aplicativo lógico recebe essas solicitações HTTP, o gatilho é acionado e executa a próxima etapa no fluxo de trabalho.

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco, que abre o designer do aplicativo lógico.

1. Na caixa de pesquisa, insira `http request` como seu filtro. Na lista de **gatilhos** , selecione **quando uma solicitação HTTP é recebida**.

   ![Adicionar gatilho de solicitação HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Agora, salve seu aplicativo lógico para que você possa gerar uma URL de ponto de extremidade para seu aplicativo lógico. Na barra de ferramentas do designer, selecione **salvar**.

   A URL do ponto de extremidade agora aparece em seu gatilho, por exemplo:

   ![Gerar URL para ponto de extremidade](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Adicionar uma ação SAP

Em aplicativos lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa no fluxo de trabalho que segue um gatilho ou outra ação. Se você ainda não adicionou um gatilho ao seu aplicativo lógico e deseja seguir este exemplo, [adicione o gatilho descrito nesta seção](#add-trigger).

1. No designer do aplicativo lógico, no gatilho, selecione **nova etapa**.

   ![Adicionar nova etapa ao aplicativo lógico](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Na caixa de pesquisa, insira `sap` como seu filtro. Na lista **ações** , selecione **Enviar mensagem para SAP**.
  
   ![Selecione a ação "Enviar mensagem para o SAP"](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Ou, você pode selecionar a guia **Enterprise** e selecionar a ação SAP.

   ![Selecione a ação "Enviar mensagem para o SAP" na guia empresa](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Se sua conexão já existir, continue com a próxima etapa para que você possa configurar sua ação SAP. No entanto, se os detalhes de conexão forem solicitados, forneça as informações para que você possa criar uma conexão com o servidor SAP local agora.

   1. Forneça um nome para a conexão.

   1. Na seção **Gateway de dados** , em **assinatura**, primeiro selecione a assinatura do Azure para o recurso de gateway que você criou no portal do Azure para a instalação do gateway. 
   
   1. Em **Gateway de conexão**, selecione o recurso de gateway.

   1. Continue fornecendo informações sobre a conexão. Para a propriedade **tipo de logon** , siga a etapa com base em se a propriedade está definida como servidor ou **grupo**de **aplicativos** :
   
      * Para o **servidor de aplicativos**, essas propriedades, que geralmente aparecem opcionais, são necessárias:

        ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para o **grupo**, essas propriedades, que geralmente aparecem opcionais, são necessárias:

        ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Por padrão, a tipagem forte é usada para verificar valores inválidos executando a validação de XML no esquema. Esse comportamento pode ajudá-lo a detectar problemas anteriormente. A opção de **tipagem segura** está disponível para compatibilidade com versões anteriores e verifica apenas o comprimento da cadeia de caracteres. Saiba mais sobre a [opção de digitação segura](#safe-typing).

   1. Quando tiver terminado, selecione **criar**.

      Os aplicativos lógicos configuram e testam sua conexão para garantir que a conexão funcione corretamente.

1. Agora, encontre e selecione uma ação no servidor SAP.

   1. Na caixa **ação SAP** , selecione o ícone de pasta. Na lista arquivo, localize e selecione a mensagem SAP que você deseja usar. Para navegar na lista, use as setas.

      Este exemplo seleciona um IDoc com o tipo **Orders** .

      ![Localizar e selecionar a ação IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Se você não conseguir localizar a ação desejada, poderá inserir manualmente um caminho, por exemplo:

      ![Forneça manualmente o caminho para a ação IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Forneça o valor para a **ação do SAP** por meio do editor de expressão. Dessa forma, você pode usar a mesma ação para tipos de mensagens diferentes.

      Para obter mais informações sobre operações de IDoc, consulte [esquemas de mensagem para operações de IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Clique dentro da caixa de **mensagem de entrada** para que a lista de conteúdo dinâmico seja exibida. Nessa lista, em **quando uma solicitação HTTP é recebida**, selecione o campo **corpo** .

      Esta etapa inclui o conteúdo do corpo do gatilho de solicitação HTTP e envia essa saída para o servidor SAP.

      ![Selecione a propriedade "corpo" do gatilho](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Quando terminar, sua ação SAP será parecida com este exemplo:

      ![Concluir a ação SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Adicionar uma ação de resposta HTTP

Agora, adicione uma ação de resposta ao fluxo de trabalho do aplicativo lógico e inclua a saída da ação SAP. Dessa forma, seu aplicativo lógico retorna os resultados do seu servidor SAP para o solicitante original.

1. No designer de aplicativo lógico, na ação SAP, selecione **nova etapa**.

1. Na caixa de pesquisa, insira `response` como seu filtro. Na lista **ações** , selecione **resposta**.

1. Clique dentro da caixa **corpo** para que a lista de conteúdo dinâmico seja exibida. Nessa lista, em **Enviar mensagem para SAP**, selecione o campo **corpo** .

   ![Ação completa do SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Guarde a aplicação lógica.

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

1. Se seu aplicativo lógico ainda não estiver habilitado, no menu do aplicativo lógico, selecione **visão geral**. Na barra de ferramentas, selecione **habilitar**.

1. Na barra de ferramentas do designer, selecione **executar**. Essa etapa inicia manualmente o aplicativo lógico.

1. Dispare seu aplicativo lógico enviando uma solicitação HTTP POST para a URL em seu gatilho de solicitação HTTP.
Inclua o conteúdo da mensagem com sua solicitação. Para enviar a solicitação, você pode usar uma ferramenta como o [postmaster](https://www.getpostman.com/apps).

   Para este artigo, a solicitação envia um arquivo IDoc, que deve estar no formato XML e incluir o namespace para a ação SAP que você está usando, por exemplo:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Depois de enviar sua solicitação HTTP, aguarde a resposta do seu aplicativo lógico.

   > [!NOTE]
   > Seu aplicativo lógico pode atingir o tempo limite se todas as etapas necessárias para a resposta não forem concluídas dentro do tempo limite da [solicitação](./logic-apps-limits-and-config.md). Se essa condição ocorrer, as solicitações poderão ser bloqueadas. Para ajudá-lo a diagnosticar problemas, saiba como você pode [verificar e monitorar seus aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Agora você criou um aplicativo lógico que pode se comunicar com o servidor SAP. Agora que você configurou uma conexão SAP para seu aplicativo lógico, pode explorar outras ações SAP disponíveis, como BAPI e RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Receber mensagem do SAP

Este exemplo usa um aplicativo lógico que é disparado quando o aplicativo recebe uma mensagem de um sistema SAP.

### <a name="add-an-sap-trigger"></a>Adicionar um gatilho SAP

1. No portal do Azure, crie um aplicativo lógico em branco, que abre o designer do aplicativo lógico.

1. Na caixa de pesquisa, insira `sap` como seu filtro. Na lista de **gatilhos** , selecione **quando uma mensagem é recebida do SAP**.

   ![Adicionar gatilho SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Ou, você pode selecionar a guia **Enterprise** e, em seguida, selecionar o gatilho:

   ![Adicionar gatilho SAP da guia Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Se sua conexão já existir, continue com a próxima etapa para que você possa configurar sua ação SAP. No entanto, se os detalhes de conexão forem solicitados, forneça as informações para que você possa criar uma conexão com o servidor SAP local agora.

   1. Forneça um nome para a conexão.

   1. Na seção **Gateway de dados** , em **assinatura**, primeiro selecione a assinatura do Azure para o recurso de gateway que você criou no portal do Azure para a instalação do gateway. 

   1. Em **Gateway de conexão**, selecione o recurso de gateway.

   1. Continue fornecendo informações sobre a conexão. Para a propriedade **tipo de logon** , siga a etapa com base em se a propriedade está definida como servidor ou **grupo**de **aplicativos** :

      * Para o **servidor de aplicativos**, essas propriedades, que geralmente aparecem opcionais, são necessárias:

        ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para o **grupo**, essas propriedades, que geralmente aparecem opcionais, são necessárias:

        ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Por padrão, a tipagem forte é usada para verificar valores inválidos executando a validação de XML no esquema. Esse comportamento pode ajudá-lo a detectar problemas anteriormente. A opção de **tipagem segura** está disponível para compatibilidade com versões anteriores e verifica apenas o comprimento da cadeia de caracteres. Saiba mais sobre a [opção de digitação segura](#safe-typing).

   1. Quando tiver terminado, selecione **criar**.

      Os aplicativos lógicos configuram e testam sua conexão para garantir que a conexão funcione corretamente.

1. Forneça os parâmetros necessários com base na configuração do sistema SAP.

   Opcionalmente, você pode fornecer uma ou mais ações do SAP. Essa lista de ações especifica as mensagens que o gatilho recebe do servidor SAP por meio do gateway de dados. Uma lista vazia especifica que o gatilho recebe todas as mensagens. Se a lista tiver mais de uma mensagem, o gatilho receberá apenas as mensagens especificadas na lista. Todas as outras mensagens enviadas do servidor SAP são rejeitadas pelo Gateway.

   Você pode selecionar uma ação SAP no seletor de arquivos:

   ![Adicionar ação SAP ao aplicativo lógico](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Ou você pode especificar uma ação manualmente:

   ![Inserir manualmente a ação SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Aqui está um exemplo que mostra como a ação é exibida quando você configura o gatilho para receber mais de uma mensagem.

   ![Exemplo de gatilho que recebe várias mensagens](media/logic-apps-using-sap-connector/example-trigger.png)

   Para obter mais informações sobre a ação SAP, consulte [esquemas de mensagem para operações de IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Agora, salve seu aplicativo lógico para que você possa começar a receber mensagens do seu sistema SAP. Na barra de ferramentas do designer, selecione **salvar**.

Seu aplicativo lógico agora está pronto para receber mensagens do seu sistema SAP.

> [!NOTE]
> O gatilho SAP não é um gatilho de sondagem, mas é um gatilho baseado em webhook. O gatilho é chamado a partir do gateway somente quando uma mensagem existe, portanto, nenhuma sondagem é necessária.

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

1. Para disparar seu aplicativo lógico, envie uma mensagem do seu sistema SAP.

1. No menu do aplicativo lógico, selecione **visão geral**. Examine o **histórico de execuções** para qualquer nova execução para seu aplicativo lógico.

1. Abra a execução mais recente, que mostra a mensagem enviada do seu sistema SAP na seção saídas do gatilho.

## <a name="receive-idoc-packets-from-sap"></a>Receber pacotes IDOC do SAP

Você pode configurar o SAP para [Enviar IDocs em pacotes](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), que são lotes ou grupos de IDocs. Para receber pacotes IDOC, o conector SAP e, especificamente, o gatilho, não precisa de configuração adicional. No entanto, para processar cada item em um pacote IDOC depois que o gatilho recebe o pacote, algumas etapas adicionais são necessárias para dividir o pacote em IDOCs individuais.

Aqui está um exemplo que mostra como extrair IDOCs individuais de um pacote usando a [função`xpath()`](./workflow-definition-language-functions-reference.md#xpath):

1. Antes de começar, você precisa de um aplicativo lógico com um gatilho SAP. Se você ainda não tiver esse aplicativo lógico, siga as etapas anteriores neste tópico para [configurar um aplicativo lógico com um gatilho do SAP](#receive-from-sap).

   Por exemplo:

   ![Adicionar gatilho SAP ao aplicativo lógico](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Obtenha o namespace raiz do IDOC XML que seu aplicativo lógico recebe do SAP. Para extrair esse namespace do documento XML, adicione uma etapa que cria uma variável de cadeia de caracteres local e armazena esse namespace usando uma `xpath()` expressão:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Obter namespace de raiz de IDOC](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Para extrair um IDOC individual, adicione uma etapa que cria uma variável de matriz e armazena a coleção IDOC usando outra expressão de `xpath()`:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Obter matriz de itens](./media/logic-apps-using-sap-connector/get-array.png)

   A variável de matriz torna cada IDOC disponível para o aplicativo lógico processar individualmente, enumerando sobre a coleção. Neste exemplo, o aplicativo lógico transfere cada IDOC para um servidor SFTP usando um loop:

   ![Enviar IDOC para o servidor SFTP](./media/logic-apps-using-sap-connector/loop-batch.png)

   Cada IDOC deve incluir o namespace raiz, que é o motivo pelo qual o conteúdo do arquivo é encapsulado dentro de um elemento `<Receive></Receive` juntamente com o namespace raiz antes de enviar o IDOC para o aplicativo downstream, ou para o servidor SFTP nesse caso.

Você pode usar o modelo de início rápido para esse padrão selecionando este modelo no designer de aplicativo lógico ao criar um novo aplicativo lógico.

![Selecionar modelo de aplicativo lógico do lote](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Gerar esquemas para artefatos no SAP

Este exemplo usa um aplicativo lógico que você pode disparar com uma solicitação HTTP. A ação do SAP envia uma solicitação para um sistema SAP para gerar os esquemas para IDoc e BAPI especificados. Os esquemas que retornam na resposta são carregados em uma conta de integração usando o conector de Azure Resource Manager.

### <a name="add-an-http-request-trigger"></a>Adicionar um gatilho de solicitação HTTP

1. No portal do Azure, crie um aplicativo lógico em branco, que abre o designer do aplicativo lógico.

1. Na caixa de pesquisa, insira `http request` como seu filtro. Na lista de **gatilhos** , selecione **quando uma solicitação HTTP é recebida**.

   ![Adicionar gatilho de solicitação HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Agora, salve seu aplicativo lógico para que você possa gerar uma URL de ponto de extremidade para seu aplicativo lógico.
Na barra de ferramentas do designer, selecione **salvar**.

   A URL do ponto de extremidade agora aparece em seu gatilho, por exemplo:

   ![Gerar URL para ponto de extremidade](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Adicionar uma ação SAP para gerar esquemas

1. No designer do aplicativo lógico, no gatilho, selecione **nova etapa**.

   ![Adicionar nova etapa ao aplicativo lógico](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Na caixa de pesquisa, insira `sap` como seu filtro. Na lista **ações** , selecione **gerar esquemas**.
  
   ![Adicionar a ação "gerar esquemas" ao aplicativo lógico](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Ou, você pode selecionar a guia **Enterprise** e selecionar a ação SAP.

   ![Selecione a ação enviar SAP da guia empresa](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Se sua conexão já existir, continue com a próxima etapa para que você possa configurar sua ação SAP. No entanto, se os detalhes de conexão forem solicitados, forneça as informações para que você possa criar uma conexão com o servidor SAP local agora.

   1. Forneça um nome para a conexão.

   1. Na seção **Gateway de dados** , em **assinatura**, primeiro selecione a assinatura do Azure para o recurso de gateway que você criou no portal do Azure para a instalação do gateway. 
   
   1. Em **Gateway de conexão**, selecione o recurso de gateway.

   1. Continue fornecendo informações sobre a conexão. Para a propriedade **tipo de logon** , siga a etapa com base em se a propriedade está definida como servidor ou **grupo**de **aplicativos** :
   
      * Para o **servidor de aplicativos**, essas propriedades, que geralmente aparecem opcionais, são necessárias:

        ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para o **grupo**, essas propriedades, que geralmente aparecem opcionais, são necessárias:

        ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Por padrão, a tipagem forte é usada para verificar valores inválidos executando a validação de XML no esquema. Esse comportamento pode ajudá-lo a detectar problemas anteriormente. A opção de **tipagem segura** está disponível para compatibilidade com versões anteriores e verifica apenas o comprimento da cadeia de caracteres. Saiba mais sobre a [opção de digitação segura](#safe-typing).

   1. Quando tiver terminado, selecione **criar**.

      Os aplicativos lógicos configuram e testam sua conexão para garantir que a conexão funcione corretamente.

1. Forneça o caminho para o artefato para o qual você deseja gerar o esquema.

   Você pode selecionar a ação SAP no seletor de arquivos:

   ![Selecionar ação SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Ou, você pode inserir a ação manualmente:

   ![Inserir manualmente a ação SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Para gerar esquemas para mais de um artefato, forneça os detalhes da ação SAP para cada artefato, por exemplo:

   ![Selecione Adicionar novo item](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Mostrar dois itens](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Para obter mais informações sobre a ação SAP, consulte [esquemas de mensagem para operações de IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

1. Na barra de ferramentas do designer, selecione **executar** para disparar uma execução para seu aplicativo lógico.

1. Abra a execução e verifique as saídas para a ação **gerar esquemas** .

   As saídas mostram os esquemas gerados para a lista de mensagens especificada.

### <a name="upload-schemas-to-an-integration-account"></a>Carregar esquemas para uma conta de integração

Opcionalmente, você pode baixar ou armazenar os esquemas gerados em repositórios, como um blob, um armazenamento ou uma conta de integração. As contas de integração fornecem uma experiência de primeira classe com outras ações XML, portanto, este exemplo mostra como carregar esquemas para uma conta de integração para o mesmo aplicativo lógico usando o conector de Azure Resource Manager.

1. No designer do aplicativo lógico, no gatilho, selecione **nova etapa**.

1. Na caixa de pesquisa, insira `Resource Manager` como seu filtro. Selecione **criar ou atualizar um recurso**.

   ![Selecionar ação de Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Insira os detalhes da ação, incluindo sua assinatura do Azure, grupo de recursos do Azure e conta de integração. Para adicionar os tokens do SAP aos campos, clique dentro das caixas para esses campos e selecione na lista de conteúdo dinâmico que aparece.

   1. Abra a lista **Adicionar novo parâmetro** e selecione os campos **local** e **Propriedades** .

   1. Forneça detalhes para esses novos campos, conforme mostrado neste exemplo.

      ![Inserir detalhes para Azure Resource Manager ação](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   A ação **gerar esquemas** do SAP gera esquemas como uma coleção, de modo que o designer adiciona automaticamente um loop **for each** à ação. Aqui está um exemplo que mostra como essa ação é exibida:

   ![Ação Azure Resource Manager com o loop "for each"](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Os esquemas usam o formato codificado em base64. Para carregar os esquemas para uma conta de integração, eles devem ser decodificados usando a função `base64ToString()`. Aqui está um exemplo que mostra o código para o elemento `"properties"`:
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

1. Na barra de ferramentas do designer, selecione **executar** para disparar manualmente seu aplicativo lógico.

1. Após uma execução bem-sucedida, vá para a conta de integração e verifique se os esquemas gerados existem.

## <a name="enable-secure-network-communications"></a>Habilitar comunicações de rede seguras

Antes de começar, certifique-se de que você atende aos [pré-requisitos](#pre-reqs)listados anteriormente:

* O gateway de dados local é instalado em um computador que está na mesma rede que o seu sistema SAP.
* Para o SSO, o gateway é executado como um usuário que é mapeado para um usuário SAP.
* A biblioteca SNC que fornece as funções de segurança adicionais é instalada no mesmo computador que o gateway de dados. Alguns exemplos incluem [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.

   Para habilitar o SNC para suas solicitações de ou para o sistema SAP, marque a caixa de seleção **usar SNC** na conexão SAP e forneça estas propriedades:

   ![Configurar o SAP SNC na conexão](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Propriedade | Descrição |
   |----------| ------------|
   | **Caminho da biblioteca SNC** | O nome ou caminho da biblioteca SNC relativo ao local de instalação do NCo ou ao caminho absoluto. Os exemplos são `sapsnc.dll` ou `.\security\sapsnc.dll` ou `c:\security\sapsnc.dll`. |
   | **SSO DO SNC** | Quando você se conecta por meio do SNC, a identidade da SNC normalmente é usada para autenticar o chamador. Outra opção é substituir para que as informações de usuário e senha possam ser usadas para autenticar o chamador, mas a linha ainda está criptografada. |
   | **Meu nome do SNC** | Na maioria dos casos, essa propriedade pode ser omitida. A solução SNC instalada geralmente sabe seu próprio nome de SNC. Somente para soluções que dão suporte a várias identidades, talvez seja necessário especificar a identidade a ser usada para esse destino ou servidor específico. |
   | **Nome do parceiro SNC** | O nome da SNC de back-end. |
   | **Qualidade de proteção da SNC** | A qualidade do serviço a ser usada para comunicação de SNC desse servidor ou destino específico. O valor padrão é definido pelo sistema back-end. O valor máximo é definido pelo produto de segurança usado para a SNC. |
   |||

   > [!NOTE]
   > Não defina as variáveis de ambiente SNC_LIB e SNC_LIB_64 no computador em que você tem o gateway de dados e a biblioteca do SNC. Se definido, eles terão precedência sobre o valor da biblioteca SNC passado pelo conector.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Digitação segura

Por padrão, quando você cria sua conexão SAP, a tipagem forte é usada para verificar valores inválidos executando a validação de XML no esquema. Esse comportamento pode ajudá-lo a detectar problemas anteriormente. A opção de **tipagem segura** está disponível para compatibilidade com versões anteriores e verifica apenas o comprimento da cadeia de caracteres. Se você escolher a **digitação segura**, o tipo de DATs e o tipo TIMS no SAP serão tratados como cadeias de caracteres em vez de como seus equivalentes XML, `xs:date` e `xs:time`, em que `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. A digitação segura afeta o comportamento de toda a geração de esquema, a mensagem de envio para a carga "enviada" e a resposta "foi recebida" e o gatilho. 

Quando a digitação forte é usada (a**digitação segura** não está habilitada), o esquema MAPEIA os DATs e os tipos TIMS para tipos XML mais diretos:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Quando você envia mensagens usando tipagem forte, os DATS e a resposta TIMS estão em conformidade com o formato de tipo XML correspondente:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Quando a **digitação segura** está habilitada, o esquema MAPEIA os DATs e os tipos TIMS para campos de cadeia de caracteres XML com restrições de comprimento apenas, por exemplo:

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

Quando as mensagens são enviadas com a **digitação segura** habilitada, a resposta de DATs e TIMS é semelhante a este exemplo:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Cenários avançados

### <a name="confirm-transaction-explicitly"></a>Confirmar transação explicitamente

Quando você envia transações para o SAP de aplicativos lógicos, essa troca ocorre em duas etapas, conforme descrito no documento SAP, [programas de servidor RFC transacionais](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Por padrão, a ação **Enviar para SAP lida com** as etapas para a transferência de função e para a confirmação de transação em uma única chamada. O conector SAP oferece a opção de desacoplar essas etapas. Você pode enviar um IDOC e, em vez de confirmar automaticamente a transação, pode usar a ação de **confirmar ID da transação** explícita.

Esse recurso para desacoplar a confirmação de ID de transação é útil quando você não deseja duplicar transações no SAP, por exemplo, em cenários em que as falhas podem ocorrer devido a causas, como problemas de rede. Confirmando a ID da transação separadamente, a transação é concluída apenas uma vez no sistema SAP.

Aqui está um exemplo que mostra esse padrão:

1. Crie um aplicativo lógico em branco e adicione um gatilho HTTP.

1. No conector SAP, adicione a ação **Enviar IDOC** . Forneça os detalhes para o IDOC que você envia para o sistema SAP.

1. Para confirmar explicitamente a ID da transação em uma etapa separada, no campo **confirmar tid** , selecione **não**. Para o campo **GUID da ID de transação** opcional, você pode especificar manualmente o valor ou fazer com que o conector gere e retorne automaticamente esse GUID na resposta da ação enviar IDOC.

   ![Enviar Propriedades da ação IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Para confirmar explicitamente a ID da transação, adicione a ação **confirmar ID da transação** . Clique dentro da caixa **ID da transação** para que a lista de conteúdo dinâmico seja exibida. Nessa lista, selecione o valor da **ID da transação** que é retornado da ação **Enviar IDOC** .

   ![Confirmar ação da ID da transação](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Depois que essa etapa é executada, a transação atual é marcada como concluída em ambas as extremidades, no lado do conector SAP e no lado do sistema SAP.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

Aqui estão os problemas e limitações atualmente conhecidos do conector SAP:

* O gatilho SAP não dá suporte a clusters de gateway de dados. Em alguns casos de failover, o nó do gateway de dados que se comunica com o sistema SAP pode ser diferente do nó ativo, o que resulta em um comportamento inesperado. Para cenários de envio, há suporte para clusters de gateway de dados.

* Atualmente, o conector SAP não dá suporte a cadeias de caracteres do roteador SAP. O gateway de dados local deve existir na mesma LAN que o sistema SAP que você deseja conectar.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência do conector](/connectors/sap/).

## <a name="next-steps"></a>Passos seguintes

* [Conecte-se a sistemas locais](../logic-apps/logic-apps-gateway-connection.md) de aplicativos lógicos do Azure.
* Saiba como validar, transformar e usar outras operações de mensagem com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md).
