---
title: Fontes de dados de acesso às instalações
description: Conecte-se a fontes de dados no local a partir de Azure Logic Apps criando um recurso de gateway de dados Azure no local
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: b216fa668483ef6fc30c1054dd4f65361ad17934
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065955"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Ligar a origens de dados no local a partir do Azure Logic Apps

Antes de poder aceder a fontes de dados nas instalações a partir das suas aplicações lógicas, tem de criar um recurso Azure depois [de instalar o portal de *dados no local* num computador local.](../logic-apps/logic-apps-gateway-install.md) As suas aplicações lógicas utilizam então este recurso de gateway Azure nos gatilhos e ações fornecidas pelos [conectores no local](../connectors/apis-list.md#on-premises-connectors) que estão disponíveis para apps Azure Logic.

Este artigo mostra como criar o seu recurso de gateway Azure para um gateway previamente [instalado no seu computador local.](../logic-apps/logic-apps-gateway-install.md) Para mais informações sobre o portal, consulte [como funciona o portal.](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)

> [!TIP]
> Para se conectar às redes virtuais Azure, considere criar um [*ambiente de serviço de integração.*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 

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

A Azure Logic Apps suporta operações de leitura e escrita através do portal de dados. No entanto, estas operações têm [limites no seu tamanho de carga](/data-integration/gateway/service-gateway-onprem#considerations)útil. Embora o gateway em si não incorra em custos adicionais, o [modelo de preços de Aplicações Lógicas](../logic-apps/logic-apps-pricing.md) aplica-se a estes conectores e outras operações em Azure Logic Apps.

## <a name="prerequisites"></a>Pré-requisitos

* Já [instalou o portal de dados no local num computador local.](../logic-apps/logic-apps-gateway-install.md)

* Está a utilizar a [mesma conta Estruz e subscrição](../logic-apps/logic-apps-gateway-install.md#requirements) que foi usada ao instalar o gateway de dados. Esta conta Azure deve pertencer a um único [inquilino ou diretório Azure Ative Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology)

* A instalação do seu gateway ainda não está registada e reclamada por outro recurso de gateway Azure.

  Quando cria um recurso de gateway no portal Azure, seleciona uma instalação gateway, que se liga ao seu recurso gateway e apenas a esse recurso gateway. Nas Azure Logic Apps, os gatilhos e ações no local utilizam o recurso gateway para se conectarem a fontes de dados no local. Nestes gatilhos e ações, seleciona a subscrição do Azure e o recurso de gateway associado que pretende utilizar. Cada recurso de gateway liga-se a apenas uma instalação de gateway, que se liga a apenas uma conta Azure.

  > [!NOTE]
  > Apenas o administrador de gateway pode criar o recurso gateway no portal Azure. Atualmente, os diretores de serviço não são apoiados. 

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
   | **Nome de instalação** | Selecione uma instalação gateway, que só aparece na lista quando estas condições estiverem satisfeitas: <p><p>- A instalação gateway utiliza a mesma região que o recurso gateway que pretende criar. <br>- A instalação do gateway não está ligada a outro recurso de gateway Azure. <br>- A instalação gateway está ligada à mesma conta Azure que está a usar para criar o recurso gateway. <br>- A sua conta Azure pertence a um único [inquilino ou diretório Azure Ative (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) e é a mesma conta que foi utilizada para a instalação do gateway. <p><p>Para mais informações, consulte a secção [de perguntas frequentes.](#faq) |
   |||

   Aqui está um exemplo que mostra uma instalação de gateway que está na mesma região que o seu recurso gateway e está ligada à mesma conta Azure:

   ![Fornecer detalhes para criar recurso de gateway de dados](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ligar aos dados no local

Depois de criar o seu recurso gateway e associar a sua subscrição Azure a este recurso, pode agora criar uma ligação entre a sua aplicação lógica e a sua fonte de dados no local, utilizando o gateway.

1. No portal Azure, crie ou abra a sua aplicação lógica no Logic App Designer.

1. Adicione um conector que suporta ligações no local, por exemplo, **SQL Server**.

1. Selecione **Connect via gateway de dados no local.**

1. Em **Gateways**, a partir da lista **de Subscrições,** selecione a sua subscrição Azure que tem o recurso gateway que deseja.

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

1. Para encontrar todas as ligações API apenas para a sua aplicação lógica, no menu da sua aplicação lógica, em **Ferramentas**de Desenvolvimento, selecione **ligações API**.

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

* A sua conta Azure deve ser a mesma que está ligada à instalação do gateway no computador local. Verifique se está inscrito no portal Azure com a mesma identidade que está ligada à instalação do gateway. Além disso, certifique-se de que a sua conta Azure pertence a um único [inquilino ou diretório Azure AD](../active-directory/fundamentals/active-directory-whatis.md#terminology) e está definido para o mesmo inquilino ou diretório AZure AD que foi usado durante a instalação gateway.

* O seu recurso de gateway e instalação gateway têm de usar a mesma região. No entanto, a localização da sua aplicação lógica pode diferir da localização do seu recurso gateway.

* A instalação do gateway já está registada e reclamada por outro recurso de gateway. Estas instalações não aparecerão na lista **de Nomes de Instalação.** Para rever os seus registos de gateway no portal Azure, encontre todos os seus recursos Azure que tenham o tipo **de Gateways de dados no local** em todas *as* suas subscrições Azure. Para desvincular a instalação do gateway a partir do outro recurso de gateway, consulte [o recurso Delete gateway](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Passos seguintes

* [Proteger as suas aplicações lógicas](./logic-apps-securing-a-logic-app.md)
* [Exemplos e cenários comuns para apps lógicas](./logic-apps-examples-and-scenarios.md)
