---
title: Ligar aos sistemas SAP
description: Aceder e gerir recursos SAP automatizando fluxos de trabalho com apps Azure Logic
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/21/2020
tags: connectors
ms.openlocfilehash: 4afd6f0cc3b4b5e135d80b420d8260c50d9ca46c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89488852"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ligar-se a sistemas SAP a partir de Azure Logic Apps

> [!IMPORTANT]
> Os conectores anteriores do Servidor de Aplicações SAP e do Servidor de Mensagens SAP são depreciados a 29 de fevereiro de 2020. O conector SAP atual consolida estes conectores SAP anteriores para que não tenha de alterar o tipo de ligação, seja totalmente compatível com conectores anteriores, fornece muitas capacidades adicionais e continua a utilizar a biblioteca de conector SAP .Net (SAP NCo).
>
> Para aplicações lógicas que usam os conectores mais antigos, por favor [migrar para o conector mais recente](#migrate) antes da data de depreciação. Caso contrário, estas aplicações lógicas irão experimentar falhas de execução e não poderão enviar mensagens para o seu sistema SAP.

Este artigo mostra como pode aceder aos seus recursos SAP no local a partir de dentro de uma aplicação lógica utilizando o conector SAP. O conector funciona com os lançamentos clássicos da SAP, tais como sistemas R/3 e ECC no local. O conector também permite a integração com os novos sistemas SAP mais recentes baseados em HANA, como S/4 HANA, quer estejam alojados no local ou na cloud. O conector SAP suporta a integração de mensagens ou de dados de/para sistemas baseados em SAP NetWeaver através dos protocolos Intermediate Document (IDoc), Business Application Programming Interface (BAPI) ou Chamada de Função Remota (RFC).

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

* O conteúdo da mensagem que envia para o seu servidor SAP, como um ficheiro IDoc de amostra, deve estar no formato XML e incluir o espaço de nome para a ação SAP que pretende utilizar.

* Para utilizar a **mensagem Quando uma mensagem é recebida do** gatilho SAP, também precisa de executar estes passos de configuração:
  
  > [!NOTE]
  > Este gatilho utiliza a mesma localização URI para renovar e cancelar a subscrição de um webhook. A operação de renovação utiliza o `PATCH` método HTTP, enquanto a operação de não subscrição utiliza o `DELETE` método HTTP. Este comportamento pode fazer com que uma operação de renovação apareça como uma operação de não subscrição no histórico do seu gatilho, mas a operação ainda é uma renovação porque o gatilho utiliza `PATCH` como método HTTP, não `DELETE` .

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

* Certifique-se de que instala a versão mais recente, [SAP Connector (NCo 3.0) para o Microsoft .NET 3.0.22.0 compilado com .NET Framework 4.0 - Windows 64-bit (x64)](https://support.sap.com/en/product/connectors/msnet.html). Versões anteriores podem resultar em problemas de compatibilidade. Para mais informações, consulte as [versões da biblioteca de clientes SAP.](#sap-library-versions)

* Por predefinição, o instalador SAP coloca os ficheiros de montagem na pasta de instalação predefinido. Tem de copiar estes ficheiros de montagem para outro local, com base no seu cenário da seguinte forma:

  * Para aplicações lógicas que funcionam num ISE, siga os passos descritos no ambiente de serviço de [integração pré-requisitos](#sap-ise). Para aplicações lógicas que funcionam no Azure multi-inquilino e utilizam o gateway de dados no local, copie os ficheiros de montagem da pasta de instalação predefinida para a pasta de instalação do gateway de dados. Se tiver problemas com a porta de dados, reveja as seguintes questões:

  * Tem de instalar a versão de 64 bits para a biblioteca do cliente SAP, porque o gateway de dados funciona apenas em sistemas de 64 bits. Caso contrário, obtém-se um erro de "má imagem" porque o serviço de anfitrião de gateway de dados não suporta conjuntos de 32 bits.

  * Se a sua ligação SAP falhar com a mensagem de erro "Por favor, verifique as informações da sua conta e/ou permissões e tente novamente", os ficheiros de montagem podem estar no local errado. Certifique-se de que copiou os ficheiros de montagem para a pasta de instalação do gateway de dados.

    Para o ajudar a resolver problemas, [utilize o visualizador de registo de encadernação de conjunto .NET,](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer)que permite verificar se os ficheiros de montagem estão na localização correta. Opcionalmente, pode selecionar a opção **de registo Cache de Montagem Global** quando instalar a biblioteca do cliente SAP.

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
> Quando uma aplicação lógica recebe pacotes IDoc da SAP, o gatilho de [pedido](../connectors/connectors-native-reqres.md) não suporta o esquema XML "simples" gerado pela documentação WE60 IDoc da SAP. No entanto, o esquema XML "simples" é suportado para cenários que enviam mensagens de aplicações lógicas *para* SAP. Pode utilizar o gatilho de pedido com o IDoc XML da SAP, mas não com o IDoc sobre o RFC. Ou pode transformar o XML no formato necessário. 

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

      Para obter mais informações sobre as operações do IDoc, consulte [os esquemas de mensagens para operações IDoc](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Clique dentro da caixa **de mensagem de entrada** para que a lista de conteúdos dinâmicos apareça. A partir dessa lista, em **Quando receber um pedido HTTP,** selecione o campo **Corpo.**

      Este passo inclui o conteúdo do corpo do seu gatilho HTTP Request e envia essa saída para o seu servidor SAP.

      ![Selecione a propriedade "Corpo" a partir do gatilho](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Quando terminar, a sua ação SAP parece este exemplo:

      ![Complete a ação SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Adicione uma ação de resposta HTTP

Agora adicione uma ação de resposta ao fluxo de trabalho da sua aplicação lógica e inclua a saída da ação SAP. Desta forma, a sua aplicação lógica devolve os resultados do seu servidor SAP ao solicitador original.

1. No Logic App Designer, no âmbito da ação SAP, selecione **Novo passo**.

1. Na caixa de pesquisa, introduza `response` como filtro. Na lista **de Ações,** selecione **Resposta.**

1. Clique dentro da caixa **Body** para que apareça a lista de conteúdos dinâmicos. A partir dessa lista, em **Enviar mensagem para SAP,** selecione o campo **Corpo.**

   ![Ação COMPLETA DO SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Guarde a sua aplicação lógica.

#### <a name="add-rfc-request-response"></a>Adicionar resposta de pedido rfc

> [!NOTE]
> O gatilho SAP recebe IDocs sobre tRFC, que não tem um parâmetro de resposta por design. 

Tem de criar um padrão de pedido e resposta se precisar de receber respostas utilizando uma chamada de função remota (RFC) para Aplicações Lógicas da SAP ABAP. Para receber IDocs na sua aplicação lógica, deverá fazer da sua primeira ação um [pedido HTTP](../connectors/connectors-native-reqres.md#add-a-response-action) com um código de estado e `200 OK` sem conteúdo. Este passo recomendado completa imediatamente a transferência assíncrona sap LUW sobre o tRFC, o que deixa a conversa SAP CPIC disponível novamente. Em seguida, pode adicionar mais ações na sua aplicação lógica para processar o IDoc recebido sem bloquear mais transferências.

Para implementar um padrão de pedido e resposta, deve primeiro descobrir o esquema RFC usando o [ `generate schema` comando](#generate-schemas-for-artifacts-in-sap). O esquema gerado tem dois possíveis nóns de raiz: 

1. O nó de pedido, que é a chamada que recebe da SAP.
1. O nó de resposta, que é a sua resposta de volta à SAP.

No exemplo seguinte, um padrão de pedido e resposta é gerado a partir do `STFC_CONNECTION` módulo RFC. O pedido XML é analisado para extrair um valor de nó no qual o SAP solicita `<ECHOTEXT>` . A resposta insere a atual estação de tempo como um valor dinâmico. Recebe uma resposta semelhante quando envia um `STFC_CONNECTION` RFC de uma aplicação lógica para o SAP.

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

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

   Pode [filtrar as mensagens que recebe do seu servidor SAP especificando uma lista de ações SAP](#filter-with-sap-actions).

   Pode selecionar uma ação SAP a partir do selecionador de ficheiros:

   ![Adicione ação SAP à aplicação lógica](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Ou, pode especificar manualmente uma ação:

   ![Introduza manualmente a ação SAP que pretende utilizar](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Aqui está um exemplo que mostra como a ação aparece quando configura o gatilho para receber mais do que uma mensagem.

   ![Exemplo de gatilho que recebe várias mensagens](media/logic-apps-using-sap-connector/example-trigger.png)

   Para obter mais informações sobre a ação SAP, consulte [esquemas de mensagens para operações IDoc](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

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

<a name="filter-with-sap-actions"></a>

#### <a name="filter-with-sap-actions"></a>Filtrar com ações SAP

Pode filtrar opcionalmente as mensagens que a sua aplicação lógica recebe do seu servidor SAP, fornecendo uma lista, ou matriz, com uma única ou múltiplas ações SAP. Por predefinição, esta matriz está vazia, o que significa que a sua aplicação lógica recebe todas as mensagens do seu servidor SAP sem filtragem. 

Quando configura o filtro de matriz, o gatilho só recebe mensagens dos tipos de ação SAP especificados e rejeita todas as outras mensagens do seu servidor SAP. No entanto, este filtro não afeta se a dactilografia da carga recebida é fraca ou forte.

Qualquer filtragem de ação SAP ocorre ao nível do adaptador SAP para o seu gateway de dados no local. Para obter mais informações, [consulte como enviar iDocs de teste para Aplicações Lógicas da SAP](#send-idocs-from-sap).

Se não conseguir enviar pacotes IDoc do SAP para o gatilho da sua aplicação lógica, consulte a mensagem de rejeição de chamadas Transactional RFC (tRFC) na caixa de diálogo SAP tRFC (código T SM58). Na interface SAP, poderá obter as seguintes mensagens de erro, que são cortadas devido aos limites de sublamento no campo **Texto de Estado.**

* `The RequestContext on the IReplyChannel was closed without a reply being`: Falhas inesperadas acontecem quando o manipulador catch-all para o canal termina o canal devido a um erro e reconstrói o canal para processar outras mensagens.

  * Para reconhecer que a sua aplicação lógica recebeu o IDoc, [adicione uma ação de Resposta](../connectors/connectors-native-reqres.md#add-a-response-action) que devolva um código de `200 OK` estado. O IDoc é transportado através do tRFC, o que não permite uma carga útil de resposta.

  * Se, em vez disso, tiver de rejeitar o IDoc, responda com qualquer código de estado HTTP que `200 OK` não seja o adaptador SAP que devolva uma exceção ao SAP em seu nome. 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`: Falhas previstas acontecem com outros erros, tais como a falha na geração de uma carga útil IDoc XML porque os seus segmentos não são lançados pela SAP, pelo que faltam os metadados do tipo de segmento necessários para a conversão. 

  * Para que estes segmentos tenham sido lançados pela SAP, contacte o engenheiro ABAP para o seu sistema SAP.

<a name="find-extended-error-logs"></a>

## <a name="find-extended-error-logs"></a>Encontre registos de erros estendidos

Para obter mensagens de erro completas, verifique os registos estendidos do adaptador SAP. 

Para lançamentos de gateway de dados no local a partir de junho de 2020 e posteriormente, pode [ativar registos de gateway nas definições da aplicação.](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app)

Para as versões de gateway de dados no local a partir de abril de 2020 e anteriores, os registos são desativados por padrão. Para recuperar troncos estendidos, siga estes passos:

1. Na pasta de instalação do portal de dados no local, abra o `Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` ficheiro. 

1. Para a definição **SapExtendedTracing,** altere o valor de **Falso** para **Verdadeiro**.

1. Opcionalmente, para menos eventos, altere o valor **SapTracingLevel** de **Informational** (padrão) para **Error** ou **Warning**. Ou, para mais eventos, **altere o Informational** para **Verbose.**

1. Guarde o ficheiro de configuração.

1. Reinicie o seu portal de dados. Abra a aplicação de instalador de gateway de dados no local e vá ao menu **Definições de Serviço.** No **Reinício do gateway**, selecione Reiniciar **agora**.

1. Reproduza o teu problema.

1. Exporte os seus registos de gateway. Na aplicação de instalação do portal de dados, aceda ao menu **De diagnóstico.** Nos **registos gateway**, selecione **registos de exportação**. Estes ficheiros incluem registos SAP organizados por data. Dependendo do tamanho do registo, podem existir vários ficheiros de registo para uma única data.

1. No ficheiro de configuração, reverta a **definição Detração SapExtended** para **Falso**.

1. Reinicie o serviço de gateway.

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

1. Para ativar a sua aplicação lógica, envie uma mensagem do seu sistema SAP.

1. No menu de aplicativos lógico, selecione **Overview**. Reveja o **histórico de Runs** para quaisquer novas corridas para a sua aplicação lógica.

1. Abra a execução mais recente, que mostra a mensagem enviada do seu sistema SAP na secção saídas do gatilho.

<a name="send-idocs-from-sap"></a>

### <a name="test-sending-idocs-from-sap"></a>Teste de envio de IDocs da SAP

Para enviar IDocs do SAP para a sua aplicação lógica, precisa da seguinte configuração mínima:

> [!IMPORTANT]
> Utilize estes passos apenas quando testar a sua configuração SAP com a sua aplicação lógica. Ambientes de produção requerem configuração adicional.

1. [Configurar um destino RFC em SAP](#create-rfc-destination)

1. [Crie uma ligação ABAP ao seu destino RFC](#create-abap-connection)

1. [Criar uma porta recetora](#create-receiver-port)

1. [Criar uma porta de remetente](#create-sender-port)

1. [Criar um parceiro de sistema lógico](#create-logical-system-partner)

1. [Criar um perfil de parceiro](#create-partner-profiles)

1. [Testar mensagens de envio](#test-sending-messages)

#### <a name="create-rfc-destination"></a>Criar destino RFC

1. Para abrir a **Configuração das definições de Conexões RFC,** na sua interface SAP, utilize o código de transação **sm59** (código T) com o prefixo **/n.**

1. Selecione **ligações TCP/IP**  >  **Criar**.

1. Criar um novo destino RFC com as seguintes definições:
    
    * Para o seu **Destino RFC,** insira um nome.
    
    * No separador **Definições Técnicas,** para **Tipo de Ativação,** selecione **Programa de Servidor Registado**. Para o seu **ID do programa,** insira um valor. No SAP, o gatilho da sua aplicação lógica será registado utilizando este identificador.
    
    * No **separador Unicode,** para **Tipo de Comunicação com Sistema Alvo,** selecione **Unicode**.

1. Guarde as alterações.

1. Registe o seu novo **ID do programa** com aplicações lógicas Azure.

1. Para testar a sua ligação, na interface SAP, no âmbito do seu novo **Destino RFC,** selecione **Connection Test**.

#### <a name="create-abap-connection"></a>Criar conexão ABAP

1. Para abrir a **Configuração das definições de Conexões RFC,** na sua interface SAP, utilize o código de transação **sm59*** (código T) com o prefixo **/n.**

1. Selecione **Ligações ABAP**  >  **Criar**.

1. Para **o RFC Destination,** insira o identificador para [o seu sistema SAP de teste](#create-rfc-destination).

1. Guarde as alterações.

1. Para testar a sua ligação, selecione **Connection Test**.

#### <a name="create-receiver-port"></a>Criar porta recetora

1. Para abrir as portas Nas definições **de processamento do IDOC,** na sua interface SAP, utilize o código de transação **we21** (código T) com o prefixo **/n.**

1. Selecione **Ports**  >  **Transactional RFC**  >  **Create**.

1. Na caixa de definições que abre, selecione **o nome da porta própria**. Para a sua porta de teste, insira um **Nome**. Guarde as alterações.

1. Nas definições para a sua nova porta recetora, para **o destino RFC,** insira o identificador para [o seu destino RFC de teste](#create-rfc-destination).

1. Guarde as alterações.

#### <a name="create-sender-port"></a>Criar porta de remetente

1.  Para abrir as portas Nas definições **de processamento do IDOC,** na sua interface SAP, utilize o código de transação **we21** (código T) com o prefixo **/n.**

1. Selecione **Ports**  >  **Transactional RFC**  >  **Create**.

1. Na caixa de definições que abre, selecione **o nome da porta própria**. Para a sua porta de teste, insira um **Nome** que comece com **SAP**. Todos os nomes porta-remetentes devem começar com as letras **SAP**, por exemplo, **SAPTEST**. Guarde as alterações.

1. Nas definições para a sua nova porta de remetente, para **o destino RFC,** insira o identificador para [a sua ligação ABAP](#create-abap-connection).

1. Guarde as alterações.

#### <a name="create-logical-system-partner"></a>Criar parceiro de sistema lógico

1. Para abrir o **Change View "Sistemas lógicos": Definições de visão geral,** na interface SAP, utilize o código de transação **bd54** (código T).

1. Aceite a mensagem de aviso que aparece: **Cuidado: A tabela é cliente transversal**

1. Acima da lista que mostra os seus sistemas lógicos existentes, **selecione Novas Entradas**.

1. Para o seu novo sistema lógico, introduza um identificador **temLog.Sys** e uma descrição curta **do Nome.** Guarde as alterações.

1. Quando aparecer a **Prompt for Workbench,** crie um novo pedido fornecendo uma descrição, ou se já criou um pedido, ignore este passo.

1. Depois de criar o pedido de bancada de trabalho, ligue esse pedido ao pedido de atualização da tabela. Para confirmar que a sua tabela foi atualizada, guarde as suas alterações.

#### <a name="create-partner-profiles"></a>Criar perfis de parceiros

Para ambientes de produção, deve criar dois perfis de parceiros. O primeiro perfil é para o remetente, que é a sua organização e sistema SAP. O segundo perfil é para o recetor, que é a sua aplicação lógica.

1. Para abrir as definições de **perfis do Parceiro,** na sua interface SAP, utilize o código de transação **we20** (código T) com o prefixo **/n.**

1. Sob **perfis de parceiros**, selecione **Partner Type LS**  >  **Create**.

1. Criar um novo perfil de parceiro com as seguintes definições:

    * Para **o parceiro nº,** insira [o identificador do seu parceiro de sistema lógico.](#create-logical-system-partner)

    * Para **Partn. Tipo,** insira **LS**.

    * Para **agente,** introduza o identificador para a conta de utilizador SAP a utilizar quando registar identificadores de programas para Apps Azure Logic ou outros sistemas não-SAP.

1. Guarde as alterações. Se não [criou o parceiro de sistema lógico,](#create-logical-system-partner)obtém o erro, **introduza um número de parceiro válido**.

1. Nas definições do seu perfil de parceiro, em **parmtrs outbound.** **Create outbound parameter**

1. Criar um novo parâmetro de saída com as seguintes definições:

    * Introduza o **seu Tipo de Mensagem,** por exemplo, **CREMAS**.

    * Introduza o [identificador da porta recetora](#create-receiver-port).

    * Introduza um tamanho IDoc para **Pack. Tamanho**. Ou, para [enviar IDocs um de cada vez a partir de SAP,](#receive-idoc-packets-from-sap)selecione **Passe IDoc Imediatamente**.

1. Guarde as alterações.

#### <a name="test-sending-messages"></a>Testar mensagens de envio

1. Para abrir a Ferramenta de Teste para Definições **de Processamento de IDoc,** na sua interface SAP, utilize o código de transação **we19** (código T) com o prefixo **/n.**

1. Em **Modelo para teste**, selecione Via **mensagem tipo**, e introduza o seu tipo de mensagem, por exemplo, **CREMAS**. Selecione **Criar**.

1. Confirmar a mensagem **do tipo IDoc** selecionando **Continue**.

1. Selecione o nó **EDIDC.** Introduza os valores adequados para as portas do seu recetor e remetente. Selecione **Continuar**.

1. Selecione **o processamento de saída padrão**.

1. Para iniciar o processamento iDoc de saída, **selecione Continue**. Quando o processamento termina, aparece o **IDoc enviado para o sistema SAP ou** mensagem de programa externa.

1.  Para verificar se há erros de processamento, utilize o código de transação **sm58** (código T) com o prefixo **/n.**

## <a name="receive-idoc-packets-from-sap"></a>Receba pacotes IDoc da SAP

Pode configurar o SAP para [enviar IDocs em pacotes,](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html)que são lotes ou grupos de IDocs. Para receber pacotes IDoc, o conector SAP, e especificamente o gatilho, não precisa de configuração extra. No entanto, para processar cada item num pacote IDoc após o gatilho receber o pacote, são necessários alguns passos adicionais para dividir o pacote em IDocs individuais.

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

Este exemplo utiliza uma aplicação lógica que pode desencadear com um pedido HTTP. Para gerar os esquemas para o IDoc especificado e BAPI, a ação SAP **Gerar esquema** envia um pedido para um sistema SAP.

Esta ação SAP devolve um [esquema XML,](#sample-xml-schemas)não o conteúdo ou dados do próprio documento XML. Os esquemas devolvidos na resposta são enviados para uma conta de integração utilizando o conector Azure Resource Manager. Os esquemas contêm as seguintes partes:

* A estrutura da mensagem de pedido. Utilize estas informações para formar a sua `get` lista BAPI.
* A estrutura da mensagem de resposta. Use esta informação para analisar a resposta. 

Para enviar a mensagem de pedido, utilize a ação genérica SAP **Enviar mensagem para SAP,** ou as ações de **Chamada BAPI** direcionadas.

### <a name="sample-xml-schemas"></a>Esquemas XML da amostra

Se estiver a aprender a gerar um esquema XML para utilização na criação de um documento de amostragem, consulte as seguintes amostras. Estes exemplos mostram como pode trabalhar com muitos tipos de cargas, incluindo:

* [Pedidos de RFC](#xml-samples-for-rfc-requests)
* [Pedidos da BAPI](#xml-samples-for-bapi-requests)
* [Pedidos iDoc](#xml-samples-for-idoc-requests)
* Tipos de dados de esquemas XML simples ou complexos
* Parâmetros de tabela
* Comportamentos opcionais de XML

Pode iniciar o seu esquema XML com um prologa XML opcional. O conector SAP funciona com ou sem o prologar XML.

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>Amostras de XML para pedidos de RFC

O exemplo a seguir é uma chamada de RFC básica. O nome RFC é `STFC_CONNECTION` . Este pedido utiliza o espaço de nome `xmlns=` predefinido, no entanto, pode atribuir e utilizar pseudónimos do espaço de nome, tais como `xmmlns:exampleAlias=` . O valor do espaço de nome é o espaço de nome para todos os RFCs em SAP para serviços microsoft. Há um parâmetro de entrada simples no pedido, `<REQUTEXT>` .

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

O exemplo a seguir é uma chamada RFC com um parâmetro de tabela. Esta chamada de exemplo e o seu grupo de RFCs de teste estão disponíveis como parte de todos os sistemas SAP. O nome do parâmetro da tabela é `TCPICDAT` . O tipo de linha de mesa é `ABAPTEXT` , e este elemento repete-se para cada linha da tabela. Este exemplo contém uma única linha, chamada `LINE` . Os pedidos com parâmetro de tabela podem conter qualquer número de campos, onde o número é um número inteiro positivo *(n*). 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

O exemplo a seguir é uma chamada RFC com um parâmetro de tabela que tem um campo anónimo. Um campo anónimo é quando o campo não tem nome atribuído. Os tipos complexos são declarados sob um espaço de nome separado, no qual a declaração define um novo padrão para o nó atual e todos os seus elementos infantis. O exemplo usa o código hexajoso `x002F` como um personagem de fuga para o símbolo */* , porque este símbolo é reservado no nome de campo SAP.

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

O exemplo a seguir inclui prefixos para os espaços de nome. Pode declarar todos os prefixos de uma só vez, ou pode declarar qualquer número de prefixos como atributos de um nó. O pseudónimo do espaço de nome RFC `ns0` é usado como raiz e parâmetros para o tipo básico.

> [!NOTE]
> os tipos complexos são declarados sob um espaço de nome diferente para tipos RFC com o pseudónimo `ns3` em vez do espaço de nome RFC regular com o pseudónimo `ns0` .

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>Amostras de XML para pedidos bapi

> [!TIP]
> Se estiver a utilizar o designer de Aplicações Lógicas para editar o seu pedido BAPI, pode utilizar as seguintes funções de pesquisa: 
> 
> * Selecione um objeto no designer para ver um menu suspenso dos métodos disponíveis.
> * Filtrar os tipos de objetos de negócio por palavra-chave utilizando a lista pes pes pes pes pes pes que é fornecida pela chamada bapi API.

> [!NOTE]
> O SAP disponibiliza objetos empresariais para sistemas externos, descrevendo-os em resposta ao RFC `RPY_BOR_TREE_INIT` , que a Logic Apps emite sem filtro de entrada. A Logic Apps inspeciona a tabela de `BOR_TREE` saídas. O `SHORT_TEXT` campo é usado para nomes de objetos de negócio. Os objetos empresariais não devolvidos pela SAP na tabela de saída não estão acessíveis a Aplicações Lógicas.
> Se utilizar objetos comerciais personalizados, deve certificar-se de que publica e liberta estes objetos comerciais no SAP. Caso contrário, a SAP não lista os seus objetos de negócio personalizados na tabela de `BOR_TREE` saídas. Não pode aceder aos seus objetos de negócio personalizados em Logic Apps até expor os objetos de negócio da SAP. 

O exemplo a seguir obtém uma lista de bancos utilizando o método `GETLIST` BAPI. Esta amostra contém o objeto de negócios de um banco, `BUS1011` . 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

O exemplo a seguir cria um objeto bancário utilizando o `CREATE` método. Este exemplo utiliza o mesmo objeto de negócio que o exemplo `BUS1011` anterior, . Quando utilizar o `CREATE` método para criar um banco, certifique-se de que comete as suas alterações porque este método não é cometido por defeito.

> [!TIP]
> Certifique-se de que o seu documento XML segue quaisquer regras de validação configuradas no seu sistema SAP. Por exemplo, neste documento de amostra, a chave bancária ( `<BANK_KEY>` ) precisa ser um número de encaminhamento bancário, também conhecido como um número ABA, nos EUA.

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

O exemplo seguinte obtém detalhes para um banco usando o número de encaminhamento bancário, o valor para `<BANK_KEY>` . 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>Amostras de XML para pedidos de IDoc

Para gerar um esquema simples SAP IDoc XML, utilize a aplicação **SAP Logon** e o código T `WE-60` . Aceda à documentação SAP através do GUI e gere esquemas XML em formato XSD para os seus tipos e extensões IDoc. Para obter uma explicação dos formatos e cargas sapais genéricos, bem como dos seus diálogos incorporados, consulte a [documentação SAP](https://help.sap.com/viewer/index).

Este exemplo declara o nó raiz e os espaços de nome. O URI no código de amostra, `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` declara a seguinte configuração:

* `/IDoc` é a nota de raiz para todos os IDocs
* `/3` é a versão dos tipos de registo para definições comuns de segmento
* `/ORDERS05` é o tipo IDoc
* `//` é um segmento vazio, porque não há extensão IDoc
* `/700` é a versão SAP
* `/Send` é a ação para enviar a informação para o SAP

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

Pode repetir o `idocData` nó para enviar um lote de IDocs numa única chamada. No exemplo abaixo, há um registo de `EDI_DC40` controlo, e vários registos de dados.

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

Segue-se um registo de controlo IDoc de amostra, que utiliza o `EDI_DC` prefixo. Tem de atualizar os valores para corresponder à instalação SAP e ao tipo IDoc. Por exemplo, o seu código de cliente IDoc pode não ser `800` . Contacte a sua equipa SAP para se certificar de que está a utilizar os valores corretos para a sua instalação SAP.

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

O exemplo a seguir é um registo de dados de amostra com segmentos simples. Este exemplo utiliza o formato de data SAP. Documentos de tipo forte podem usar formatos de data XML nativos, tais como `2020-12-31 23:59:59` .

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

O exemplo a seguir é um registo de dados com segmentos agrupados. O registo inclui um nó de pai em `E2EDKT1002GRP` grupo, e vários nós infantis, incluindo `E2EDKT1002` e `E2EDKT2001` . 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

O método recomendado é criar um identificador IDoc para utilização com tRFC. Pode configurar este identificador de `tid` transação, utilizando a [operação Send IDoc](/connectors/sap/#send-idoc) na API do conector SAP.

O exemplo a seguir é um método alternativo para definir o identificador de transações, ou `tid` . Neste exemplo, o último nó do segmento de registo de dados e o nó de dados do IDoc estão fechados. Em seguida, o `guid` GUID, é usado como o identificador tRFC para detetar duplicados. 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

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

   Para obter mais informações sobre a ação SAP, consulte [os esquemas de mensagens para operações IDoc](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

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

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

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

### <a name="change-language-headers"></a>Alterar cabeçalhos de linguagem

Quando se liga ao SAP a partir de Aplicações Lógicas, o idioma predefinido para a ligação é inglês. Pode definir o idioma para a sua ligação utilizando [o cabeçalho `Accept-Language` HTTP padrão](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) com os seus pedidos de entrada.

> [!TIP]
> A maioria dos navegadores da Web adiciona um `Accept-Language` cabeçalho com base nas definições do utilizador. O navegador web aplica este cabeçalho quando cria uma nova ligação SAP no designer de Aplicações Lógicas. Se não quiser criar ligações SAP no idioma preferido do seu navegador, atualize as definições do seu navegador web para usar o seu idioma preferido ou crie a sua ligação SAP utilizando o Azure Resource Manager em vez do designer de Aplicações Lógicas. 

Por exemplo, pode enviar um pedido com o `Accept-Language` cabeçalho para a sua aplicação lógica utilizando o gatilho **HTTP Request.** Todas as ações da sua aplicação lógica recebem o cabeçalho. Em seguida, o SAP utiliza as línguas especificadas nas suas mensagens de sistema, tais como mensagens de erro BAPI.

Os parâmetros de ligação SAP para uma aplicação lógica não têm uma propriedade linguística. Assim, se utilizar o `Accept-Language` cabeçalho, poderá obter o seguinte erro: **Verifique as informações da sua conta e/ou permissões e tente novamente.** Neste caso, verifique os registos de erro do componente SAP. O erro ocorre na componente SAP que utiliza o cabeçalho, para que possa obter uma destas mensagens de erro:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>Confirmar transação explicitamente

Quando envia transações para o SAP a partir de Aplicações Lógicas, esta troca ocorre em dois passos, conforme descrito no documento SAP, [Programas de Servidor RFC Transacionais](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Por predefinição, a **ação Enviar para SAP** trata tanto dos passos para a transferência de funções como para a confirmação da transação numa única chamada. O conector SAP dá-lhe a opção de dissociar estes passos. Pode enviar um IDoc e, em vez de confirmar automaticamente a transação, pode utilizar a ação explícita **de ID de transação** confirme.

Esta capacidade de dissociar a confirmação do ID de transação é útil quando não pretende duplicar transações em SAP, por exemplo, em cenários onde falhas podem ocorrer devido a causas como problemas de rede. Ao confirmar separadamente o ID da transação, a transação é concluída apenas uma vez no seu sistema SAP.

Aqui está um exemplo que mostra este padrão:

1. Crie uma aplicação lógica em branco e adicione um gatilho HTTP.

1. A partir do conector SAP, adicione a ação **Send IDOC.** Forneça os detalhes do IDoc que envia para o seu sistema SAP.

1. Para confirmar explicitamente o ID da transação num passo separado, no campo **Confirmar TID,** selecione **Nº**. Para o campo **ID GUID de transação** opcional, pode especificar manualmente o valor ou ter o conector automaticamente gerado e devolver este GUID na resposta a partir da ação Enviar IDOC.

   ![Enviar propriedades de ação IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Para confirmar explicitamente o ID da transação, adicione a ação **de ID de transação Confirm,** certificando-se de [que evita o envio de IDocs duplicados para SAP](#avoid-sending-duplicate-idocs). Clique dentro da caixa **de ID de transação** para que a lista de conteúdos dinâmicos apareça. A partir dessa lista, selecione o valor de **ID** de transação que é devolvido da ação **Enviar IDOC.**

   ![Confirmar ação de ID de transação](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Após este passo, a transação atual é marcada completa em ambas as extremidades, no lado do conector SAP e no lado do sistema SAP.

#### <a name="avoid-sending-duplicate-idocs"></a>Evite enviar IDocs duplicados

Se sentir um problema com iDocs duplicados sendo enviados para o SAP a partir da sua aplicação lógica, siga estes passos para criar uma variável de cadeia para servir como o seu identificador de transação IDoc. A criação deste identificador de transações ajuda a prevenir a duplicação de transmissões de rede quando existem problemas como interrupções temporárias, problemas de rede ou reconhecimentos perdidos.

> [!NOTE]
> Os sistemas SAP esquecem um identificador de transações após um tempo especificado, ou 24 horas por padrão. Como resultado, a SAP nunca deixa de confirmar um identificador de transações se o ID ou o GUID forem desconhecidos.
> Se a confirmação de um identificador de transações falhar, esta falha indica que a comunhão com o sistema SAP falhou antes da SAP ter sido capaz de reconhecer a confirmação.

1. No designer de Aplicações Lógicas, adicione a **variável de** ação Initialize à sua aplicação lógica. 
1. No editor para a ação **Inicialize variável,** configufique as seguintes definições. Então, guarde as suas mudanças.
    1. Para **nome,** insira um nome para a sua variável. Por exemplo, `IDOCtransferID`.
    2. Para **o tipo**, selecione **String** como o tipo variável.
    3. Para **Valor**, selecione a caixa de texto **Introduza** o valor inicial para abrir o menu de conteúdo dinâmico. Selecione o separador **Expressões.** Na lista de funções, insira a função `guid()` . Em seguida, selecione **OK** para guardar as suas alterações. O campo **Valor** está agora definido para a `guid()` função, que gera um GUID.
1. Após a ação **variável Inicialize,** adicione a ação **Enviar IDOC**.
1. No editor para a ação **Enviar IDOC,** configurar as seguintes definições. Então, guarde as suas mudanças.
    1. Para **o tipo IDOC** selecione o seu tipo de mensagem e para **inserir a mensagem IDOC**, especifique a sua mensagem.
    1. Para **a versão de lançamento SAP,** selecione os valores da sua configuração SAP.
    1. Para **a versão dos tipos De Registo,** selecione os valores da sua configuração SAP.
    1. Para **confirmar o TID**, selecione **Nº**.
    1. **Selecione Adicionar nova lista de**  >  **parâmetros Transaction ID GUID**. Selecione a caixa de texto para abrir o menu de conteúdo dinâmico. No separador **Variáveis,** selecione o nome da variável que criou. Por exemplo, `IDOCtransferID`.
1. Na barra de título da ação **Enviar IDOC,** selecione **...**  >  **Definições**. Para **a Política de Retíria**, selecione **Nenhum**  >  **Feito**.
1. Após a ação **Enviar IDOC,** adicione a ação **Confirm transaction ID**.
1. No editor para a ação Confirme o **ID de transação,** configufique as seguintes definições. Então, guarde as suas mudanças.
    1. Para **o ID de transação,** introduza novamente o nome da sua variável. Por exemplo, `IDOCtransferID`.

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

Aqui estão as questões e limitações atualmente conhecidas para o conector SAP gerido (não-ISE):

* O gatilho SAP não suporta agrupamentos de gateways de dados. Em alguns casos de falha, o nó de gateway de dados que comunica com o sistema SAP pode diferir do nó ativo, o que resulta em comportamento inesperado. Para os cenários de envio, os agrupamentos de gateways de dados são suportados.

* O conector SAP não suporta atualmente as cordas do router SAP. O portal de dados no local deve existir na mesma LAN que o sistema SAP que pretende ligar.

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/sap/).

> [!NOTE]
> Para aplicações lógicas num ambiente de [serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com rótulo ISE deste conector utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* [Ligue-se aos sistemas no local](../logic-apps/logic-apps-gateway-connection.md) a partir de Azure Logic Apps.
* Saiba como validar, transformar e utilizar outras operações de mensagens com o [Pacote de Integração Empresarial.](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre [outros conectores de Aplicações Lógicas](../connectors/apis-list.md).
