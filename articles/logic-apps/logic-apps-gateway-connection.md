---
title: Fontes de dados de acesso às instalações
description: Conecte-se a fontes de dados no local a partir de Azure Logic Apps criando um recurso de gateway de dados em Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 356e63bb0a749ad0f41d886e75971e9b05c7f9dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99218999"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Ligar a origens de dados no local a partir do Azure Logic Apps

Depois de [instalar o portal de *dados no local* num computador local](../logic-apps/logic-apps-gateway-install.md) e antes de poder aceder a fontes de dados nas instalações a partir das suas aplicações lógicas, tem de criar um recurso de gateway em Azure para a sua instalação gateway. Em seguida, pode selecionar este recurso gateway nos gatilhos e ações que pretende utilizar para os [conectores no local](../connectors/apis-list.md#on-premises-connectors) disponíveis nas Apps Azure Logic. A Azure Logic Apps suporta operações de leitura e escrita através do portal de dados. No entanto, estas operações têm [limites no seu tamanho de carga](/data-integration/gateway/service-gateway-onprem#considerations)útil.

Este artigo mostra como criar o seu recurso de gateway Azure para um gateway previamente [instalado no seu computador local.](../logic-apps/logic-apps-gateway-install.md) Para mais informações sobre o portal, consulte [como funciona o portal.](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)

> [!TIP]
> Para aceder diretamente aos recursos nas instalações das redes virtuais Azure sem ter de usar o gateway, considere criar um ambiente de serviço de [*integração.*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 

Para obter informações sobre como utilizar o gateway com outros serviços, consulte estes artigos:

* [Microsoft Power Automatizar porta de dados no local](/power-automate/gateway-reference)
* [Porta de dados do Microsoft Power BI no local](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps no local gateway de dados](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services on-in-local data gateway](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Supported data sources (Origens de dados suportadas)

Nas Aplicações Lógicas Azure, o gateway de dados no local suporta os [conectores no local](../connectors/apis-list.md#on-premises-connectors) para estas fontes de dados:

* BizTalk Server 2016
* Sistema de Ficheiros
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Base de dados Oracle
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Também pode criar [conectores personalizados](../logic-apps/custom-connector-overview.md) que se ligam a fontes de dados através de HTTP ou HTTPS utilizando REST ou SOAP. Embora o gateway em si não incorra em custos adicionais, o [modelo de preços de Aplicações Lógicas](../logic-apps/logic-apps-pricing.md) aplica-se a estes conectores e outras operações em Azure Logic Apps.

## <a name="prerequisites"></a>Pré-requisitos

* Já [instalou o portal de dados no local num computador local.](../logic-apps/logic-apps-gateway-install.md) Esta instalação gateway deve existir antes de poder criar um recurso de gateway que se ligue a esta instalação.

* Tem a [mesma conta Eszure e subscrição](../logic-apps/logic-apps-gateway-install.md#requirements) que usou para a instalação do gateway. Esta conta Azure deve pertencer apenas a um único [inquilino ou diretório Azure Ative Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology) Tem de utilizar a mesma conta Estil e subscrição Azure para criar o seu recurso gateway em Azure porque só o administrador de gateway pode criar o recurso gateway em Azure. Os diretores de serviços não são apoiados.

  * Quando criar um recurso de gateway em Azure, selecione uma instalação gateway para ligar com o seu recurso gateway e apenas com esse recurso gateway. Cada recurso de gateway pode ligar-se a apenas uma instalação de gateway. Não é possível selecionar uma instalação de gateway que já esteja associada a outro recurso de gateway.

  * A sua aplicação lógica e recurso gateway não têm de existir na mesma subscrição do Azure. Nos gatilhos e ações onde pode utilizar o recurso gateway, pode selecionar uma subscrição Azure diferente que tenha um recurso de gateway, mas apenas se essa subscrição existir no mesmo inquilino ou diretório AD AZure que a sua aplicação lógica. Também tem de ter permissões de administrador no gateway, que outro administrador pode configurar para si. Para obter mais informações, consulte [Data Gateway: Automation using PowerShell - Part 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) e [PowerShell: Data Gateway - Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).

    > [!NOTE]
    > Atualmente, não é possível partilhar um recurso de gateway ou instalação em várias subscrições. Para submeter feedback do produto, consulte [o Microsoft Azure Feedback Forum](https://feedback.azure.com/forums/34192--general-feedback).

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Criar recurso de gateway Azure

Depois de instalar o gateway num computador local, crie o recurso Azure para o seu gateway.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com a mesma conta Azure que foi usada para instalar o gateway.

1. Na caixa de pesquisa do portal Azure, introduza "gateway de dados no local", e selecione **Gateways de dados no local**.

   ![Encontre "Gateway de dados no local"](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. No **local, data gateways,** selecione **Add**.

   ![Adicione novo recurso Azure para gateway de dados](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. No **portal de ligação Create,** forneça estas informações para o seu recurso gateway. Quando concluir, selecione **Criar**.

   | Propriedade | Descrição |
   |----------|-------------|
   | **Nome do Recurso** | Forneça um nome para o seu recurso gateway que contenha apenas letras, números, hífenes `-` (), sublinhados `_` (, parênteses ( `(` , ) ou `)` períodos `.` (). |
   | **Subscrição** | Selecione a subscrição Azure para a conta Azure que foi utilizada para a instalação gateway. A subscrição por defeito baseia-se na conta Azure que usou para iniciar scontabilidade. |
   | **Grupo de recursos** | O [grupo de recursos Azure](../azure-resource-manager/management/overview.md) que pretende utilizar |
   | **Localização** | A mesma região ou local que foi selecionado para o serviço de nuvem gateway durante a [instalação gateway](../logic-apps/logic-apps-gateway-install.md). Caso contrário, a instalação do gateway não aparecerá na lista **de Nomes de Instalação.** A localização da aplicação lógica pode diferir da localização do seu recurso gateway. |
   | **Nome de instalação** | Selecione uma instalação gateway, que só aparece na lista quando estas condições estiverem satisfeitas: <p><p>- A instalação gateway utiliza a mesma região que o recurso gateway que pretende criar. <br>- A instalação do gateway não está ligada a outro recurso de gateway Azure. <br>- A instalação gateway está ligada à mesma conta Azure que está a usar para criar o recurso gateway. <br>- A sua conta Azure pertence a um único [inquilino ou diretório Azure Ative (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) e é a mesma conta que usou para a instalação do gateway. <p><p>Para mais informações, consulte a secção [de perguntas frequentes.](#faq) |
   |||

   Aqui está um exemplo que mostra uma instalação de gateway que está na mesma região que o seu recurso gateway e está ligada à mesma conta Azure:

   ![Fornecer detalhes para criar recurso de gateway de dados](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ligar aos dados no local

Depois de criar o seu recurso gateway e associar a sua subscrição Azure a este recurso, pode agora criar uma ligação entre a sua aplicação lógica e a sua fonte de dados no local, utilizando o gateway.

1. No portal Azure, crie ou abra a sua aplicação lógica no Logic App Designer.

1. Adicione um conector que suporta ligações no local, por exemplo, **SQL Server**.

1. Selecione **Connect via gateway de dados no local.**

1. Em **Gateway**, a partir da lista de **subscrição,** selecione a sua subscrição Azure que tem o recurso gateway que deseja.

   A sua aplicação lógica e recurso gateway não têm de existir na mesma subscrição do Azure. Pode selecionar a partir de outras subscrições Azure que cada uma tem um recurso de gateway, mas apenas se estas subscrições existirem no mesmo inquilino ou diretório AZure AD como a sua aplicação lógica, e tem permissões de administrador no gateway, que outro administrador pode configurar para si. Para obter mais informações, consulte [Data Gateway: Automation using PowerShell - Part 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) e [PowerShell: Data Gateway - Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).
  
1. A partir da lista **Connection Gateway,** que mostra os recursos de gateway disponíveis na sua subscrição selecionada, selecione o recurso gateway que deseja. Cada recurso de gateway está ligado a uma instalação de um único gateway.

   > [!NOTE]
   > A lista de gateways inclui recursos de gateway noutras regiões porque a localização da sua aplicação lógica pode diferir da localização do seu recurso gateway. 

1. Forneça um nome de ligação único e outras informações necessárias, que dependem da ligação que pretende criar.

   Um nome de ligação único ajuda-o a encontrar facilmente essa ligação mais tarde, especialmente se criar várias ligações. Se aplicável, inclua também o domínio qualificado para o seu nome de utilizador.

   Segue-se um exemplo:

   ![Criar ligação entre app lógica e gateway de dados](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Quando concluir, selecione **Criar**.

A sua ligação gateway está agora pronta para a sua aplicação lógica para usar.

## <a name="edit-connection"></a>Editar ligação

Para atualizar as definições de uma ligação gateway, pode editar a sua ligação.

1. Para encontrar todas as ligações API apenas para a sua aplicação lógica, no menu da sua aplicação lógica, em **Ferramentas** de Desenvolvimento, selecione **ligações API**.

   ![No menu de aplicativos logic, selecione "Conexões API"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Selecione a ligação gateway desejada e, em seguida, **selecione Editar a ligação API**.

   > [!TIP]
   > Se as atualizações não pesarem, tente [parar e reiniciar a conta de serviço gateway Windows](../logic-apps/logic-apps-gateway-install.md#restart-gateway) para a sua instalação gateway.

Para encontrar todas as ligações API associadas à sua subscrição Azure:

* A partir do menu do portal Azure, selecione **Todas as ligações**  >  **web**  >  **API .**
* Ou, a partir do menu do portal Azure, selecione **Todos os recursos**. Coloque o filtro **Tipo** na **Ligação API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Eliminar recurso de gateway

Para criar um recurso de gateway diferente, ligue a instalação do gateway a um recurso de gateway diferente ou remova o recurso gateway, pode eliminar o recurso gateway sem afetar a instalação do gateway.

1. A partir do menu do portal Azure, selecione **Todos os recursos,** ou procure e selecione **Todos os recursos** de qualquer página. Encontre e selecione o seu recurso gateway.

1. Se ainda não estiver selecionado, no menu de recursos gateway, selecione Data Gateway no **local.** Na barra de ferramentas de recursos gateway, **selecione Delete**.

   Por exemplo:

   ![Eliminar recurso de gateway em Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P:** Porque é que a minha instalação gateway não aparece quando crio o meu recurso de gateway em Azure? <br/>
**R**: Esta questão pode acontecer por estas razões:

* A sua conta Azure não é a mesma que usou para a instalação do gateway no seu computador local. Verifique se inscreveu no portal Azure com a mesma identidade que usou para a instalação do gateway. Apenas o administrador de gateway pode criar o recurso gateway em Azure. Os diretores de serviços não são apoiados.

* Sua conta Azure não pertence apenas a um único inquilino ou diretório da [AD Azure.](../active-directory/fundamentals/active-directory-whatis.md#terminology) Verifique se está a usar o mesmo inquilino ou diretório AZure que usou durante a instalação do gateway.

* O seu recurso de gateway e instalação de gateway não existem na mesma região. No entanto, a localização da sua aplicação lógica pode diferir da localização do seu recurso gateway.

* A instalação do gateway já está associada a outro recurso de gateway. Cada recurso de gateway pode ligar-se a apenas uma instalação de gateway, que pode ligar-se a apenas uma conta Eszure e subscrição. Então, não pode selecionar uma instalação de gateway que já está associada a outro recurso de gateway. Estas instalações não aparecerão na lista **de Nomes de Instalação.**

  Para rever os seus registos de gateway no portal Azure, encontre todos os seus recursos Azure que tenham o tipo de recurso **Data Gateways no local** em todas *as* suas subscrições Azure. Para desvincular a instalação do gateway a partir do outro recurso de gateway, consulte [o recurso Delete gateway](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Passos seguintes

* [Proteger as suas aplicações lógicas](./logic-apps-securing-a-logic-app.md)
* [Exemplos e cenários comuns para apps lógicas](./logic-apps-examples-and-scenarios.md)
