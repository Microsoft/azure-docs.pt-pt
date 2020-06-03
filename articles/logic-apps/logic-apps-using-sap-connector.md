---
title: Ligar aos sistemas SAP
description: Aceder e gerir recursos SAP automatizando fluxos de trabalho com apps Azure Logic
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 05/29/2020
tags: connectors
ms.openlocfilehash: 557e162d9d7f0238d5554c32cb3ae96885877dbe
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220493"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ligar-se a sistemas SAP a partir de Azure Logic Apps

> [!IMPORTANT]
> Os conectores anteriores do Servidor de Aplicações SAP e do Servidor de Mensagens SAP são depreciados a 29 de fevereiro de 2020. O conector SAP atual consolida estes conectores SAP anteriores para que não tenha de alterar o tipo de ligação, seja totalmente compatível com conectores anteriores, fornece muitas capacidades adicionais e continua a utilizar a biblioteca de conector SAP .Net (SAP NCo).
>
> Para aplicações lógicas que usam os conectores mais antigos, por favor [migrar para o conector mais recente](#migrate) antes da data de depreciação. Caso contrário, estas aplicações lógicas irão experimentar falhas de execução e não poderão enviar mensagens para o seu sistema SAP.

Este artigo mostra como pode aceder aos seus recursos SAP no local a partir de dentro de uma aplicação lógica utilizando o conector SAP. O conector funciona com os lançamentos clássicos da SAP, tais como sistemas R/3 e ECC no local. O conector também permite a integração com os mais recentes sistemas SAP baseados em HANA, como o S/4 HANA, quer estejam hospedados no local ou na nuvem. O conector SAP suporta a integração de mensagens ou dados de e para sistemas baseados em SAP NetWeaver através do Documento Intermédio (IDoc), interface de programação de aplicações empresariais (BAPI) ou Chamada de Função Remota (RFC).

O conector SAP utiliza a [biblioteca SAP .NET Connector (NCo)](https://support.sap.com/en/product/connectors/msnet.html) e fornece estas ações:

* **Enviar mensagem para SAP**: Enviar IDoc através do tRFC, ligar para as funções BAPI através do RFC ou ligar para o RFC/tRFC em sistemas SAP.

* **Quando uma mensagem for recebida do SAP**: Receber IDoc sobre tRFC, ligue para as funções BAPI através do tRFC ou ligue para o RFC/tRFC em sistemas SAP.

* **Gerar esquemas**: Gere esquemas para os artefactos SAP para IDoc, BAPI ou RFC.

Para estas operações, o conector SAP suporta a autenticação básica através de nomes de utilizador e palavras-passe. O conector também suporta [comunicações de rede seguras (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). O SNC pode ser utilizado para o sap netWeaver single sign-on (SSO) ou para capacidades de segurança adicionais fornecidas por um produto de segurança externa.

Este artigo mostra como criar exemplos de aplicações lógicas que se integram com o SAP enquanto cobrem os cenários de integração previamente descritos. Para aplicações lógicas que usam os conectores SAP mais antigos, este artigo mostra como migrar as suas aplicações lógicas para o mais recente conector SAP.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar este artigo, precisa destes itens:

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, [inscreva-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/)

* A aplicação lógica de onde pretende aceder ao seu sistema SAP e um gatilho que inicia o fluxo de trabalho da sua aplicação lógica. Se é novo em aplicações lógicas, veja [O que é Azure Logic Apps e](../logic-apps/logic-apps-overview.md) [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* O seu [servidor de aplicações SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou servidor de [mensagens SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* O conteúdo da mensagem que pode enviar para o seu servidor SAP, como um ficheiro IDoc de amostra, deve estar no formato XML e incluir o espaço de nome para a ação SAP que pretende utilizar.

* Para utilizar a **mensagem Quando uma mensagem é recebida do** gatilho SAP, também precisa de executar estes passos de configuração:

  * Configurar as permissões de segurança do gateway SAP com esta definição:

    `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

  * Configurar o seu registo de segurança de gateway SAP, que ajuda a encontrar erros da Lista de Controlo de Acesso (ACL) e não está ativado por padrão. Caso contrário, obtém-se o seguinte erro:

    `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    Para obter mais informações, consulte o tópico de ajuda SAP, [Configurando a marcação de porta de entrada.](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm)

<a name="multi-tenant"></a>

### <a name="multi-tenant-azure-prerequisites"></a>Pré-requisitos de Azure multi-inquilinos

Estes pré-requisitos aplicam-se quando as suas aplicações lógicas funcionam no Azure multi-inquilino, e você quer usar o conector SAP gerido, que não funciona de forma nativa em um ambiente de [serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Caso contrário, se estiver a utilizar um ISE de nível Premium e quiser utilizar o conector SAP que funciona de forma nativa no ISE, consulte os [pré-requisitos do ambiente de serviço de integração (ISE).](#sap-ise)

O conector SAP gerido (não ISE) integra-se com sistemas SAP no local através do [gateway de dados no local](../logic-apps/logic-apps-gateway-connection.md). Por exemplo, em cenários de mensagens de envio, quando uma mensagem é enviada de uma aplicação lógica para um sistema SAP, o gateway de dados funciona como um cliente RFC e encaminha os pedidos recebidos da aplicação lógica para o SAP. Da mesma forma, em cenários de mensagem de receção, o gateway de dados funciona como um servidor RFC que recebe pedidos do SAP e os encaminha para a aplicação lógica.

* [Faça o download e instale o portal de dados no seu](../logic-apps/logic-apps-gateway-install.md) computador local. Em seguida, [crie um recurso de gateway Azure](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) para aquele portal Azure. O gateway ajuda-o a aceder de forma segura aos dados e recursos no local.

  Como uma boa prática, certifique-se de usar uma versão suportada do portal de dados no local. A Microsoft lança uma nova versão todos os meses. Atualmente, a Microsoft suporta as últimas seis versões. Se tiver algum problema com o seu gateway, tente [atualizar para a versão mais recente](https://aka.ms/on-premises-data-gateway-installer)– que poderá incluir atualizações para resolver o seu problema.

* [Descarregue e instale a mais recente biblioteca](#sap-client-library-prerequisites) de clientes SAP no mesmo computador que o portal de dados no local.

<a name="sap-ise"></a>

### <a name="integration-service-environment-ise-prerequisites"></a>Pré-requisitos do ambiente de serviço de integração (ISE)

Estes pré-requisitos aplicam-se quando as suas aplicações lógicas funcionam num ambiente de serviço de integração de nível Premium (não nível de [Desenvolvimento) (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)e pretende utilizar o conector SAP que funciona de forma nativa num ISE. Um ISE fornece acesso a recursos protegidos por uma rede virtual Azure e oferece outros conectores nativos do ISE que permitem que as aplicações lógicas acedam diretamente aos recursos no local sem usar o gateway de dados no local.

> [!NOTE]
> Embora o conector SAP ISE seja visível dentro de um ISE de nível de desenvolvimento, as tentativas de instalar o conector não serão bem sucedidas.

1. Se ainda não tiver uma conta de Armazenamento Azure e um recipiente de bolhas, crie esse recipiente utilizando o [portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md) ou o [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. [Faça o download e instale a mais recente biblioteca de clientes SAP](#sap-client-library-prerequisites) no seu computador local. Deve ter os seguintes ficheiros de montagem:

   * libicudecnumber.dll
   * rscp4n.dll
   * sapnco.dll
   * sapnco_utils.dll

1. Crie um ficheiro .zip que inclua estes conjuntos e carrema este pacote para o seu recipiente blob no Azure Storage.

1. No portal Azure ou no Azure Storage Explorer, navegue pelo local do contentor onde carregou o ficheiro .zip.

1. Copie o URL para esse local, certificando-se de que inclui o token assinatura de acesso partilhado (SAS).

   Caso contrário, o token SAS não é autorizado, e a implementação para o conector SAP ISE falhará.

1. Antes de poder utilizar o conector SAP ISE, tem de instalar e instalar o conector no seu ISE.

   1. No [portal Azure,](https://portal.azure.com)encontre e abra o seu ISE.
   
   1. No menu ISE, selecione **Conectores Geridos**  >  **Adicionar**. Na lista de conectores, encontre e selecione **SAP**.
   
   1. No Adicionar um novo painel de **conector gerido,** na caixa **de embalagem SAP,** cole o URL para o ficheiro .zip que tem os conjuntos SAP. *Certifique-se de que inclui o token SAS.*

   1. Quando concluir, selecione **Criar**.

   Para obter mais informações, consulte [os conectores ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

1. Se o seu caso SAP e ISE estiverem em diferentes redes virtuais, também precisa de [espreitar essas redes](../virtual-network/tutorial-connect-virtual-networks-portal.md) para que a rede virtual do ise esteja ligada à rede virtual do seu caso SAP.

<a name="sap-client-library-prerequisites"></a>

### <a name="sap-client-library-prerequisites"></a>Pré-requisitos da biblioteca de clientes SAP

* Certifique-se de que instala a versão mais recente, [SAP Connector (NCo 3.0) para o Microsoft .NET 3.0.22.0 compilado com .NET Framework 4.0 - Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019). Versões anteriores podem resultar em problemas de compatibilidade. Para mais informações, consulte as [versões da biblioteca de clientes SAP.](#sap-library-versions)

* Por predefinição, o instalador SAP coloca os ficheiros de montagem na pasta de instalação predefinido. Tem de copiar estes ficheiros de montagem para outro local, com base no seu cenário da seguinte forma:

  Para aplicações lógicas que funcionam num ISE, siga os passos descritos no ambiente de serviço de [integração pré-requisitos](#sap-ise). Para aplicações lógicas que funcionam no Azure multi-inquilino e utilizam o gateway de dados no local, copie os ficheiros de montagem da pasta de instalação predefinida para a pasta de instalação do gateway de dados. Se tiver problemas com a porta de dados, reveja as seguintes questões:

  * Tem de instalar a versão de 64 bits para a biblioteca do cliente SAP, porque o gateway de dados funciona apenas em sistemas de 64 bits. Caso contrário, obtém-se um erro de "má imagem" porque o serviço de anfitrião de gateway de dados não suporta conjuntos de 32 bits.

  * Se a sua ligação SAP falhar com a mensagem de erro "Por favor, verifique as informações da sua conta e/ou permissões e tente novamente", os ficheiros de montagem podem estar no local errado. Certifique-se de que copiou os ficheiros de montagem para a pasta de instalação do gateway de dados.

    Para o ajudar a resolver problemas, [utilize o visualizador de registo de encadernação de conjunto .NET,](https://docs.microsoft.com/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer)que permite verificar se os ficheiros de montagem estão na localização correta. Opcionalmente, pode selecionar a opção **de registo Cache de Montagem Global** quando instalar a biblioteca do cliente SAP.

<a name="sap-library-versions"></a>

#### <a name="sap-client-library-versions"></a>Versões da biblioteca de clientes SAP

As versões anteriores do SAP NCo podem ficar num impasse quando mais de uma mensagem IDoc é enviada ao mesmo tempo. Esta condição bloqueia todas as mensagens posteriores que são enviadas para o destino SAP, o que faz com que as mensagens se esmorecrem.

Aqui estão as relações entre a biblioteca do cliente SAP, o Quadro .NET, o tempo de execução .NET e o gateway:

* Tanto o adaptador Microsoft SAP como o serviço de anfitriões gateway utilizam .NET Framework 4.5.

* O SAP NCo para .NET Framework 4.0 funciona com processos que utilizam .NET tempo de execução 4.0 a 4.7.1.

* O SAP NCo para .NET Framework 2.0 funciona com processos que utilizam o tempo de funcionamento .NET 2.0 a 3.5, mas que já não funciona com o mais recente gateway.

### <a name="secure-network-communications-prerequisites"></a>Pré-requisitos de comunicações de rede seguras

Se utilizar o portal de dados no local com as comunicações de rede secure (SNC) opcionais, que é suportada apenas no Azure multi-inquilino, também precisa de configurar estas definições:

* Se utilizar o SNC com single Sign On (SSO), certifique-se de que o gateway de dados está a funcionar como um utilizador que está mapeado contra o utilizador SAP. Para alterar a conta predefinitiva, selecione **Alterar a conta**e introduza as credenciais de utilizador.

  ![Alterar conta de gateway de dados](./media/logic-apps-using-sap-connector/gateway-account.png)

* Se ativar o SNC com um produto de segurança externo, copie a biblioteca SNC ou ficheiros no mesmo computador onde o portal de dados está instalado. Alguns exemplos de produtos SNC incluem [sapseculib,](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)Kerberos e NTLM.

Para obter mais informações sobre a ativação do SNC para a porta de dados, consulte [Enable Secure Network Communications](#secure-network-communications).

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrar para o conector atual

Para migrar de um conector SAP gerido anteriormente (não-ISE) para o conector SAP gerido atualmente, siga estes passos:

1. Se ainda não o fez, atualize o seu [portal de dados no local](https://www.microsoft.com/download/details.aspx?id=53127) para que tenha a versão mais recente. Para obter mais informações, consulte [instalar uma porta de dados no local para aplicações Azure Logic](../logic-apps/logic-apps-gateway-install.md).

1. Na aplicação lógica que utiliza o conector SAP mais antigo, elimine a **ação Enviar para SAP.**

1. A partir do mais recente conector SAP, adicione a **mensagem Enviar à ação SAP.** Antes de poder utilizar esta ação, recrie a ligação ao seu sistema SAP.

1. Quando terminar, guarde a sua aplicação lógica.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Enviar mensagem para SAP

Este exemplo utiliza uma aplicação lógica que pode desencadear com um pedido HTTP. A aplicação lógica envia um IDoc para um servidor SAP e devolve uma resposta ao solicitador que chamou a aplicação lógica.

### <a name="add-an-http-request-trigger"></a>Adicione um gatilho de pedido HTTP

Nas Azure Logic Apps, todas as aplicações lógicas devem começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que dispara quando um evento específico acontece ou quando uma condição específica é cumprida. Cada vez que o gatilho dispara, o motor Logic Apps cria uma instância lógica de aplicações e começa a executar o fluxo de trabalho da sua aplicação.

> [!NOTE]
> Quando uma aplicação lógica recebe pacotes IDoc da SAP, o gatilho de [pedido](https://docs.microsoft.com/azure/connectors/connectors-native-reqres) não suporta o esquema XML "simples" gerado pela documentação WE60 IDoc da SAP. No entanto, o esquema XML "simples" é suportado para cenários que enviam mensagens de aplicações lógicas *para* SAP. Pode utilizar o gatilho de pedido com o IDoc XML da SAP, mas não com o IDoc sobre o RFC. Ou pode transformar o XML no formato necessário. 

Neste exemplo, cria-se uma aplicação lógica com um ponto final no Azure para que possa enviar *pedidos HTTP POST* para a sua aplicação lógica. Quando a sua aplicação lógica recebe estes pedidos HTTP, o gatilho dispara e corre o próximo passo no seu fluxo de trabalho.

1. No [portal Azure,](https://portal.azure.com)crie uma app lógica em branco, que abre o Logic App Designer.

1. Na caixa de pesquisa, introduza `http request` como filtro. Na lista **'Gatilhos',** selecione **Quando receber um pedido HTTP**.

   ![Adicionar http Pedido de gatilho](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Agora guarde a sua aplicação lógica para que possa gerar um URL de ponto final para a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

   O URL de ponto final aparece agora no seu gatilho, por exemplo:

   ![Gerar URL para ponto final](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Adicione uma ação SAP

Nas Azure Logic Apps, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no seu fluxo de trabalho que segue um gatilho ou outra ação. Se ainda não adicionou um gatilho à sua aplicação lógica e quiser seguir este exemplo, [adicione o gatilho descrito nesta secção](#add-trigger).

1. No Logic App Designer, sob o gatilho, selecione **Novo passo**.

   ![Adicione novo passo na aplicação lógica](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Na caixa de pesquisa, introduza `sap` como filtro. Na lista **de Ações,** selecione **Enviar mensagem para SAP**.
  
   ![Selecione ação "Enviar mensagem para SAP"](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Ou, pode selecionar o separador **Enterprise** e selecionar a ação SAP.

   ![Selecione ação "Enviar mensagem para SAP" a partir do separador Enterprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Se a sua ligação já existir, continue com o próximo passo para que possa configurar a sua ação SAP. No entanto, se for solicitado para obter detalhes de ligação, forneça as informações para que possa criar uma ligação ao seu servidor SAP no local.

   1. Forneça um nome para a ligação.

   1. Se estiver a utilizar o portal de dados, siga estes passos:
   
      1. Na secção **Data Gateway,** em **Subscrição,** selecione primeiro a subscrição do Azure para o recurso gateway de dados que criou no portal Azure para a instalação do gateway de dados.
   
      1. Em **'Connection Gateway',** selecione o seu recurso de gateway de dados em Azure.

   1. Continue a fornecer informações sobre a ligação. Para a propriedade **'Tipo de Início de** Símia', siga o passo com base no facto de a propriedade estar definida para Servidor de **Aplicações** ou **Grupo**:
   
      * Para **o Servidor de Aplicações,** estas propriedades, que normalmente aparecem opcionais, são necessárias:

        ![Criar ligação ao servidor de aplicações SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para **o Grupo**, estas propriedades, que normalmente parecem opcionais, são necessárias:

        ![Criar ligação ao servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Por predefinição, a dactilografia forte é usada para verificar se existem valores inválidos, efetuando a validação de XML contra o esquema. Este comportamento pode ajudá-lo a detetar problemas mais cedo. A opção **Safe Dactilografia** está disponível para retrocompatibilidade e verifica apenas o comprimento da corda. Saiba mais sobre a [opção Safe Dactilografia.](#safe-typing)

   1. Quando terminar, **selecione Criar**.

      A Logic Apps configura e testa a sua ligação para se certificar de que a ligação funciona corretamente.

1. Agora encontre e selecione uma ação do seu servidor SAP.

   1. Na caixa **de ação SAP,** selecione o ícone da pasta. Na lista de ficheiros, encontre e selecione a mensagem SAP que pretende utilizar. Para navegar na lista, use as setas.

      Este exemplo seleciona um IDoc com o tipo **De Pedidos.**

      ![Localizar e selecionar ação IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Se não encontrar a ação que deseja, pode entrar manualmente num caminho, por exemplo:

      ![Fornecer manualmente caminho para a ação IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Fornecer o valor para **a AÇÃO SAP** através do editor de expressão. Desta forma, pode utilizar a mesma ação para diferentes tipos de mensagens.

      Para obter mais informações sobre as operações do IDoc, consulte [os esquemas de mensagens para operações IDoc](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Clique dentro da caixa **de mensagem de entrada** para que a lista de conteúdos dinâmicos apareça. A partir dessa lista, em **Quando receber um pedido HTTP,** selecione o campo **Corpo.**

      Este passo inclui o conteúdo do corpo do seu gatilho HTTP Request e envia essa saída para o seu servidor SAP.

      ![Selecione a propriedade "Corpo" a partir do gatilho](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Quando terminar, a sua ação SAP parece este exemplo:

      ![Complete a ação SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Adicione uma ação de resposta HTTP

Agora adicione uma ação de resposta ao fluxo de trabalho da sua aplicação lógica e inclua a saída da ação SAP. Desta forma, a sua aplicação lógica devolve os resultados do seu servidor SAP ao solicitador original.

1. No Logic App Designer, no âmbito da ação SAP, selecione **Novo passo**.

1. Na caixa de pesquisa, introduza `response` como filtro. Na lista **de Ações,** selecione **Resposta.**

1. Clique dentro da caixa **Body** para que apareça a lista de conteúdos dinâmicos. A partir dessa lista, em **Enviar mensagem para SAP,** selecione o campo **Corpo.**

   ![Ação COMPLETA DO SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Guarde a aplicação lógica.

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

1. Se a sua aplicação lógica ainda não estiver ativada, no menu de aplicações lógicas, selecione **Overview**. Na barra de ferramentas, selecione **Ativar**.

1. Na barra de ferramentas do designer, selecione **Run**. Este passo inicia manualmente a sua aplicação lógica.

1. Desacione a sua aplicação lógica enviando um pedido HTTP POST para o URL no seu detonador http request.
Inclua o conteúdo da sua mensagem com o seu pedido. Para enviar o pedido, pode utilizar uma ferramenta como [o Carteiro.](https://www.getpostman.com/apps)

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
   > A sua aplicação lógica pode esgotar-se se todos os passos necessários para a resposta não terminarem dentro do [prazo limite de tempo de pedido.](./logic-apps-limits-and-config.md) Se esta condição acontecer, os pedidos podem ser bloqueados. Para ajudá-lo a diagnosticar problemas, saiba como pode [verificar e monitorizar as suas aplicações lógicas.](../logic-apps/monitor-logic-apps.md)

Criou agora uma aplicação lógica que pode comunicar com o seu servidor SAP. Agora que criou uma ligação SAP para a sua aplicação lógica, pode explorar outras ações SAP disponíveis, como BAPI e RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Receber mensagem da SAP

Este exemplo utiliza uma aplicação lógica que desencadeia quando a aplicação recebe uma mensagem de um sistema SAP.

### <a name="add-an-sap-trigger"></a>Adicione um gatilho SAP

1. No portal Azure, crie uma app lógica em branco, que abre o Logic App Designer.

1. Na caixa de pesquisa, introduza `sap` como filtro. Na lista **'Gatilhos',** selecione **Quando receber uma mensagem do SAP**.

   ![Adicione o gatilho SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Ou, pode selecionar o separador **Enterprise** e, em seguida, selecionar o gatilho:

   ![Adicione o gatilho SAP do separador Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Se a sua ligação já existir, continue com o próximo passo para que possa configurar a sua ação SAP. No entanto, se for solicitado para obter detalhes de ligação, forneça as informações para que possa criar uma ligação ao seu servidor SAP no local agora.

   1. Forneça um nome para a ligação.

   1. Se estiver a utilizar o portal de dados, siga estes passos:

      1. Na secção **Data Gateway,** em **Subscrição,** selecione primeiro a subscrição do Azure para o recurso gateway de dados que criou no portal Azure para a instalação do gateway de dados.

      1. Em **'Connection Gateway',** selecione o seu recurso de gateway de dados em Azure.

   1. Continue a fornecer informações sobre a ligação. Para a propriedade **'Tipo de Início de** Símia', siga o passo com base no facto de a propriedade estar definida para Servidor de **Aplicações** ou **Grupo**:

      * Para **o Servidor de Aplicações,** estas propriedades, que normalmente aparecem opcionais, são necessárias:

        ![Criar ligação ao servidor de aplicações SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para **o Grupo**, estas propriedades, que normalmente parecem opcionais, são necessárias:

        ![Criar ligação ao servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Por predefinição, a dactilografia forte é usada para verificar se existem valores inválidos, efetuando a validação de XML contra o esquema. Este comportamento pode ajudá-lo a detetar problemas mais cedo. A opção **Safe Dactilografia** está disponível para retrocompatibilidade e verifica apenas o comprimento da corda. Saiba mais sobre a [opção Safe Dactilografia.](#safe-typing)

   1. Quando terminar, **selecione Criar**.

      A Logic Apps configura e testa a sua ligação para se certificar de que a ligação funciona corretamente.

1. Forneça os [parâmetros necessários](#parameters) com base na configuração do sistema SAP.

   Pode opcionalmente fornecer uma ou mais ações SAP. Esta lista de ações especifica as mensagens que o gatilho recebe do seu servidor SAP. Uma lista vazia especifica que o gatilho recebe todas as mensagens. Se a lista tiver mais de uma mensagem, o gatilho recebe apenas as mensagens especificadas na lista. Quaisquer outras mensagens enviadas do seu servidor SAP são rejeitadas.

   Pode selecionar uma ação SAP a partir do selecionador de ficheiros:

   ![Adicione ação SAP à aplicação lógica](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Ou, pode especificar manualmente uma ação:

   ![Introduza manualmente a ação SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Aqui está um exemplo que mostra como a ação aparece quando configura o gatilho para receber mais do que uma mensagem.

   ![Exemplo de gatilho que recebe várias mensagens](media/logic-apps-using-sap-connector/example-trigger.png)

   Para obter mais informações sobre a ação SAP, consulte [esquemas de mensagens para operações IDoc](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Agora guarde a sua aplicação lógica para que possa começar a receber mensagens do seu sistema SAP. Na barra de ferramentas do designer, **selecione Save**.

A sua aplicação lógica está agora pronta para receber mensagens do seu sistema SAP.

> [!NOTE]
> O gatilho sap não é um gatilho de sondagens, mas é um gatilho baseado em webhook. Se estiver a usar o portal de dados, o gatilho é chamado do portal de dados apenas quando existe uma mensagem, pelo que não é necessária nenhuma sondagem.

<a name="parameters"></a>

#### <a name="parameters"></a>Parâmetros

Juntamente com simples entradas de cordas e números, o conector SAP aceita os seguintes parâmetros de tabela `Type=ITAB` (entradas):

* Parâmetros de direção da tabela, tanto de entrada como de saída, para versões SAP mais antigas.
* Alterando os parâmetros, que substituem os parâmetros de direção da tabela para novas versões SAP.
* Parâmetros hierárquicos da tabela

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

1. Para ativar a sua aplicação lógica, envie uma mensagem do seu sistema SAP.

1. No menu de aplicativos lógico, selecione **Overview**. Reveja o **histórico de Runs** para quaisquer novas corridas para a sua aplicação lógica.

1. Abra a execução mais recente, que mostra a mensagem enviada do seu sistema SAP na secção saídas do gatilho.

## <a name="receive-idoc-packets-from-sap"></a>Receba pacotes IDoc da SAP

Pode configurar o SAP para [enviar IDocs em pacotes,](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html)que são lotes ou grupos de IDocs. Para receber pacotes IDoc, o conector SAP, e especificamente o gatilho, não precisa de configuração extra. No entanto, para processar cada item num pacote IDoc após o gatilho receber o pacote, são necessários alguns passos adicionais para dividir o pacote em IDocs individuais.

Aqui está um exemplo que mostra como extrair IDocs individuais de um pacote usando a [ `xpath()` função:](./workflow-definition-language-functions-reference.md#xpath)

1. Antes de começar, precisa de uma aplicação lógica com um gatilho SAP. Se ainda não tem esta aplicação lógica, siga os passos anteriores neste tópico para [configurar uma aplicação lógica com um gatilho SAP](#receive-from-sap).

   Por exemplo:

   ![Adicione o gatilho SAP à aplicação lógica](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Obtenha o espaço de nome raiz do XML IDoc que a sua aplicação lógica recebe do SAP. Para extrair este espaço de nome do documento XML, adicione um passo que cria uma variável de cordas local e armazena esse espaço de nome usando uma `xpath()` expressão:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Obtenha o espaço de nome raiz do IDoc](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Para extrair um IDoc individual, adicione um passo que cria uma variável de matriz e armazena a coleção IDoc usando outra `xpath()` expressão:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Obtenha uma variedade de itens](./media/logic-apps-using-sap-connector/get-array.png)

   A variável de matriz disponibiliza cada IDoc para que a sua aplicação lógica processe individualmente, enumerando a coleção. Neste exemplo, a aplicação lógica transfere cada IDoc para um servidor SFTP utilizando um loop:

   ![Enviar IDoc para o servidor SFTP](./media/logic-apps-using-sap-connector/loop-batch.png)

   Cada IDoc deve incluir o espaço de nome de raiz, que é a razão pela qual o conteúdo do ficheiro é embrulhado dentro de um `<Receive></Receive` elemento juntamente com o espaço de nome raiz antes de enviar o IDoc para a app a jusante, ou servidor SFTP neste caso.

Pode utilizar o modelo de arranque rápido para este padrão selecionando este modelo no Logic App Designer quando criar uma nova aplicação lógica.

![Selecione o modelo de aplicativo de lógica de lote](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Gerar esquemas para artefactos em SAP

Este exemplo utiliza uma aplicação lógica que pode desencadear com um pedido HTTP. A ação SAP envia um pedido a um sistema SAP para gerar os esquemas para iDoc especificado e BAPI. Os esquemas que regressam na resposta são enviados para uma conta de integração utilizando o conector Azure Resource Manager.

### <a name="add-an-http-request-trigger"></a>Adicione um gatilho de pedido HTTP

1. No portal Azure, crie uma app lógica em branco, que abre o Logic App Designer.

1. Na caixa de pesquisa, introduza `http request` como filtro. Na lista **'Gatilhos',** selecione **Quando receber um pedido HTTP**.

   ![Adicionar http Pedido de gatilho](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Agora guarde a sua aplicação lógica para que possa gerar um URL de ponto final para a sua aplicação lógica.
Na barra de ferramentas do designer, **selecione Save**.

   O URL de ponto final aparece agora no seu gatilho, por exemplo:

   ![Gerar URL para ponto final](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Adicione uma ação SAP para gerar esquemas

1. No Logic App Designer, sob o gatilho, selecione **Novo passo**.

   ![Adicione novo passo na aplicação lógica](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Na caixa de pesquisa, introduza `sap` como filtro. Na lista **de Ações,** selecione **Gerar esquemas.**
  
   ![Adicionar ação "Gerar esquemas" à aplicação lógica](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Ou, pode selecionar o separador **Enterprise** e selecionar a ação SAP.

   ![Selecione SAP enviar ação a partir do separador Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Se a sua ligação já existir, continue com o próximo passo para que possa configurar a sua ação SAP. No entanto, se for solicitado para obter detalhes de ligação, forneça as informações para que possa criar uma ligação ao seu servidor SAP no local agora.

   1. Forneça um nome para a ligação.

   1. Na secção **Data Gateway,** em **Subscrição,** selecione primeiro a subscrição do Azure para o recurso gateway de dados que criou no portal Azure para a instalação do gateway de dados. 
   
   1. Em **'Connection Gateway',** selecione o seu recurso de gateway de dados em Azure.

   1. Continue a fornecer informações sobre a ligação. Para a propriedade **'Tipo de Início de** Símia', siga o passo com base no facto de a propriedade estar definida para Servidor de **Aplicações** ou **Grupo**:
   
      * Para **o Servidor de Aplicações,** estas propriedades, que normalmente aparecem opcionais, são necessárias:

        ![Criar ligação ao servidor de aplicações SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para **o Grupo**, estas propriedades, que normalmente parecem opcionais, são necessárias:

        ![Criar ligação ao servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Por predefinição, a dactilografia forte é usada para verificar se existem valores inválidos, efetuando a validação de XML contra o esquema. Este comportamento pode ajudá-lo a detetar problemas mais cedo. A opção **Safe Dactilografia** está disponível para retrocompatibilidade e verifica apenas o comprimento da corda. Saiba mais sobre a [opção Safe Dactilografia.](#safe-typing)

   1. Quando terminar, **selecione Criar**.

      A Logic Apps configura e testa a sua ligação para se certificar de que a ligação funciona corretamente.

1. Forneça o caminho para o artefacto para o qual pretende gerar o esquema.

   Pode selecionar a ação SAP a partir do selecionador de ficheiros:

   ![Selecione ação SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Ou, pode entrar manualmente na ação:

   ![Introduza manualmente a ação SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Para gerar esquemas para mais de um artefacto, forneça os detalhes de ação SAP para cada artefacto, por exemplo:

   ![Selecione Adicionar novo item](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Mostrar dois itens](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Para obter mais informações sobre a ação SAP, consulte [os esquemas de mensagens para operações IDoc](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

1. Na barra de ferramentas do designer, selecione **Run** para desencadear uma execução para a sua aplicação lógica.

1. Abra a execução e verifique as saídas para a ação **de esquemas gerar.**

   As saídas mostram os esquemas gerados para a lista de mensagens especificadas.

### <a name="upload-schemas-to-an-integration-account"></a>Enviar esquemas para uma conta de integração

Opcionalmente, pode descarregar ou armazenar os esquemas gerados em repositórios, como uma conta de bolha, armazenamento ou integração. As contas de integração proporcionam uma experiência de primeira classe com outras ações de XML, pelo que este exemplo mostra como carregar esquemas para uma conta de integração para a mesma aplicação lógica utilizando o conector Azure Resource Manager.

1. No Logic App Designer, sob o gatilho, selecione **Novo passo**.

1. Na caixa de pesquisa, introduza `Resource Manager` como filtro. Selecione **Criar ou atualizar um recurso**.

   ![Selecione ação do Gestor de Recursos Azure](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Insira os detalhes da ação, incluindo a sua subscrição Azure, grupo de recursos Azure e conta de integração. Para adicionar fichas SAP aos campos, clique dentro das caixas para esses campos e selecione a partir da lista de conteúdos dinâmicos que aparecem.

   1. Abra a nova lista **de parâmetros** e selecione os campos **Localização** e **Propriedades.**

   1. Forneça detalhes para estes novos campos, como mostra este exemplo.

      ![Introduza detalhes para a ação do Gestor de Recursos Azure](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   A ação de **esquemas** SAP Generate gera esquemas como uma coleção, pelo que o designer adiciona automaticamente um **Para cada** loop à ação. Aqui está um exemplo que mostra como esta ação aparece:

   ![Ação do Gestor de Recursos Azure com loop "para cada"](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Os esquemas utilizam o formato codificado base64. Para fazer o upload dos esquemas para uma conta de integração, devem ser descodificados utilizando a `base64ToString()` função. Aqui está um exemplo que mostra o código para o `"properties"` elemento:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

1. Na barra de ferramentas do designer, selecione **Executar** para ativar manualmente a sua aplicação lógica.

1. Depois de uma execução bem sucedida, vá à conta de integração e verifique se existem os esquemas gerados.

<a name="secure-network-communications"></a>

## <a name="enable-secure-network-communications"></a>Ativar comunicações de rede seguras

Antes de começar, certifique-se de que cumpriu os [pré-requisitos](#pre-reqs)anteriormente listados, que se aplicam apenas quando utiliza o gateway de dados e as suas aplicações lógicas executadas em Azure multi-inquilino:

* Certifique-se de que o portal de dados no local está instalado num computador que está na mesma rede que o seu sistema SAP.

* Para um único sinal on (SSO), o gateway de dados está funcionando como um utilizador que está mapeado para um utilizador SAP.

* A biblioteca SNC que fornece as funções de segurança adicionais está instalada na mesma máquina que o gateway de dados. Alguns exemplos incluem [sapseculib,](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)Kerberos e NTLM.

   Para ativar o SNC para os seus pedidos de ou para o sistema SAP, selecione a caixa de verificação **Use SNC** na ligação SAP e forneça estas propriedades:

   ![Configure SAP SNC em conexão](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Propriedade | Descrição |
   |----------| ------------|
   | **Caminho da Biblioteca SNC** | O nome ou caminho da biblioteca SNC relativo à localização de instalação da NCo ou caminho absoluto. Exemplos são `sapsnc.dll` ou `.\security\sapsnc.dll` ou . `c:\security\sapsnc.dll` . |
   | **SNC SSO** | Quando se conecta através do SNC, a identidade SNC é normalmente utilizada para autenticar o chamador. Outra opção é sobrepor-se para que as informações do utilizador e da palavra-passe possam ser usadas para autenticar o chamador, mas a linha ainda está encriptada. |
   | **SNC Meu Nome** | Na maioria dos casos, esta propriedade pode ser omitida. A solução SNC instalada normalmente conhece o seu próprio nome SNC. Apenas para soluções que suportem múltiplas identidades, poderá ser necessário especificar a identidade a utilizar para este destino ou servidor em particular. |
   | **Nome do parceiro SNC** | O nome do SNC. |
   | **Qualidade de Proteção SNC** | A qualidade do serviço a ser utilizado para a comunicação SNC deste destino ou servidor em particular. O valor predefinido é definido pelo sistema back-end. O valor máximo é definido pelo produto de segurança utilizado para o SNC. |
   |||

   > [!NOTE]
   > Não desconte as variáveis ambientais SNC_LIB e SNC_LIB_64 na máquina onde tem o portal de dados e a biblioteca SNC. Se definidos, têm precedência sobre o valor da biblioteca SNC passado através do conector.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Dactilografia segura

Por predefinição, quando cria a sua ligação SAP, é utilizada uma dactilografia forte para verificar valores inválidos, efetuando a validação XML contra o esquema. Este comportamento pode ajudá-lo a detetar problemas mais cedo. A opção **Safe Dactilografia** está disponível para retrocompatibilidade e verifica apenas o comprimento da corda. Se escolher **a dactilografia Safe,** o tipo DATS e o tipo TIMS em SAP são tratados como cordas e não como equivalentes XML, `xs:date` e , onde `xs:time` `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . A dactilografia segura afeta o comportamento de toda a geração de esquemas, a mensagem de envio tanto para a carga útil "enviada" como para a resposta "recebida" e para o gatilho. 

Quando se utiliza uma dactilografia forte **(A dactilografia segura** não está ativada), o esquema mapeia os tipos DATS e TIMS para tipos de XML mais simples:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Quando envia mensagens utilizando uma dactilografia forte, a resposta DATS e TIMS está em conformidade com o formato do tipo XML correspondente:

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

Quando as mensagens são enviadas com **dactilografia segura** ativada, a resposta DATS e TIMS parece este exemplo:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Cenários avançados

### <a name="confirm-transaction-explicitly"></a>Confirmar transação explicitamente

Quando envia transações para o SAP a partir de Aplicações Lógicas, esta troca ocorre em dois passos, conforme descrito no documento SAP, [Programas de Servidor RFC Transacionais](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Por predefinição, a **ação Enviar para SAP** trata tanto dos passos para a transferência de funções como para a confirmação da transação numa única chamada. O conector SAP dá-lhe a opção de dissociar estes passos. Pode enviar um IDoc e, em vez de confirmar automaticamente a transação, pode utilizar a ação explícita **de ID de transação** confirme.

Esta capacidade de dissociar a confirmação do ID de transação é útil quando não pretende duplicar transações em SAP, por exemplo, em cenários onde falhas podem ocorrer devido a causas como problemas de rede. Ao confirmar separadamente o ID da transação, a transação é concluída apenas uma vez no seu sistema SAP.

Aqui está um exemplo que mostra este padrão:

1. Crie uma aplicação lógica em branco e adicione um gatilho HTTP.

1. A partir do conector SAP, adicione a ação **Send IDOC.** Forneça os detalhes do IDoc que envia para o seu sistema SAP.

1. Para confirmar explicitamente o ID da transação num passo separado, no campo **Confirmar TID,** selecione **Nº**. Para o campo **ID GUID de transação** opcional, pode especificar manualmente o valor ou ter o conector automaticamente gerado e devolver este GUID na resposta a partir da ação Enviar IDOC.

   ![Enviar propriedades de ação IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Para confirmar explicitamente o ID da transação, adicione a ação **de ID de transação Confirm.** Clique dentro da caixa **de ID de transação** para que a lista de conteúdos dinâmicos apareça. A partir dessa lista, selecione o valor de **ID** de transação que é devolvido da ação **Enviar IDOC.**

   ![Confirmar ação de ID de transação](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Após este passo, a transação atual é marcada completa em ambas as extremidades, no lado do conector SAP e no lado do sistema SAP.

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

Aqui estão as questões e limitações atualmente conhecidas para o conector SAP gerido (não-ISE):

* O gatilho SAP não suporta agrupamentos de gateways de dados. Em alguns casos de falha, o nó de gateway de dados que comunica com o sistema SAP pode diferir do nó ativo, o que resulta em comportamento inesperado. Para os cenários de envio, os agrupamentos de gateways de dados são suportados.

* O conector SAP não suporta atualmente as cordas do router SAP. O portal de dados no local deve existir na mesma LAN que o sistema SAP que pretende ligar.

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sap/).

> [!NOTE]
> Para aplicações lógicas num ambiente de [serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com rótulo ISE deste conector utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* [Ligue-se aos sistemas no local](../logic-apps/logic-apps-gateway-connection.md) a partir de Azure Logic Apps.
* Saiba como validar, transformar e utilizar outras operações de mensagens com o [Pacote de Integração Empresarial.](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre [outros conectores de Aplicações Lógicas](../connectors/apis-list.md).
