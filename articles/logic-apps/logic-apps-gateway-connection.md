---
title: Fontes de dados de acesso nas instalações
description: Ligue-se a fontes de dados no local de Aplicações Lógicas Azure através da criação de um recurso de gateway de dados Azure on-local
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 096943ff796f9c12c7f8715cadce5c3085965d4d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657141"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Ligar a origens de dados no local a partir do Azure Logic Apps

Antes de poder aceder a fontes de dados nas instalações das suas aplicações lógicas, precisa de criar um recurso Azure depois de [instalar a porta de dados no *local* num computador local.](../logic-apps/logic-apps-gateway-install.md) As suas aplicações lógicas utilizam então este recurso de gateway Azure nos gatilhos e ações fornecidas pelos [conectores no local](../connectors/apis-list.md#on-premises-connectors) que estão disponíveis para aplicações da Lógica Azure.

Este artigo mostra como criar o seu recurso de gateway Azure para uma porta de entrada previamente [instalada no seu computador local](../logic-apps/logic-apps-gateway-install.md). Para mais informações sobre o portal, consulte [como funciona o portal.](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)

> [!TIP]
> Para ligar às redes virtuais do Azure, considere criar um ambiente de serviço de [*integração.*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 

Para obter informações sobre como utilizar o portal com outros serviços, consulte estes artigos:

* [Microsoft Power Automatizar no local gateway de dados](/power-automate/gateway-reference)
* [Microsoft Power BI no local gateway de dados](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps no local gateway de dados](/powerapps/maker/canvas-apps/gateway-reference)
* [Gateway de dados dos Serviços de Análise Azure no local](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Origens de dados suportadas

Nas Aplicações Lógicas Azure, o portal de dados no local suporta os [conectores no local](../connectors/apis-list.md#on-premises-connectors) para estas fontes de dados:

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

As Aplicações Lógicas Azure suportam operações de leitura e escrita através do portal de dados. No entanto, estas operações têm [limites para o seu tamanho](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)de carga útil . Embora o gateway em si não incorra em custos adicionais, o modelo de preços das [Aplicações Lógicas](../logic-apps/logic-apps-pricing.md) aplica-se a estes conectores e outras operações em Aplicações Lógicas Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Já [instalou a porta de dados no local num computador local.](../logic-apps/logic-apps-gateway-install.md)

* Está a usar a [mesma conta Azure e subscrição](../logic-apps/logic-apps-gateway-install.md#requirements) que foi usada na instalação do portal de dados. Esta conta Azure deve pertencer a um único [inquilino ou diretório Azure Ative Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology)

* A sua instalação de gateway ainda não está registada e reclamada por outro recurso azure gateway.

  Quando cria um recurso de gateway no portal Azure, seleciona uma instalação de gateway, que se liga ao seu recurso gateway e apenas a esse recurso de gateway. Nas Aplicações Lógicas Azure, os gatilhos e ações no local usam o recurso gateway para se conectarem a fontes de dados no local. Nestes gatilhos e ações, selecione a sua subscrição Azure e o recurso de gateway associado que pretende utilizar. Cada recurso de gateway liga-se a apenas uma instalação de gateway, que se liga a apenas a uma conta Azure.

  > [!NOTE]
  > Só o administrador de gateway pode criar o recurso gateway no portal Azure. Atualmente, os diretores de serviço não são apoiados. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Criar recurso de gateway Azure

Depois de instalar o portal num computador local, crie o recurso Azure para o seu portal.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com a mesma conta Azure que foi usada para instalar o portal.

1. Na caixa de pesquisa do portal Azure, introduza "gateway de dados no local", e selecione **Gateways de Dados no local.**

   ![Localizar "Gateway de dados no local"](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. Em **gateways de dados no local,** selecione **Adicionar**.

   ![Adicione novo recurso Azure para gateway de dados](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. No âmbito **do Gateway de ligação Create,** forneça esta informação para o seu recurso gateway. Quando terminar, selecione **Criar**.

   | Propriedade | Descrição |
   |----------|-------------|
   | **Nome do recurso** | Forneça um nome para o seu recurso gateway que`-`contenha apenas letras, números, hífenes (),`_`parênteses`(`(, `)`) ou períodos ().`.` |
   | **Subscrição** | Selecione a subscrição Azure para a conta Azure que foi utilizada para a instalação do gateway. A subscrição predefinida baseia-se na conta Azure que usou para iniciar sessão. |
   | **Grupo de recursos** | O [grupo de recursos Azure](../azure-resource-manager/management/overview.md) que pretende utilizar |
   | **Localização** | A mesma região ou local que foi selecionado para o serviço de nuvem gateway durante a [instalação](../logic-apps/logic-apps-gateway-install.md)do gateway . Caso contrário, a instalação do gateway não aparecerá na lista de Nomes de **Instalação.** A localização da sua aplicação lógica pode diferir da localização do recurso gateway. |
   | **Nome de instalação** | Selecione uma instalação de gateway, que só aparece na lista quando estas condições forem satisfeitas: <p><p>- A instalação gateway utiliza a mesma região que o recurso gateway que pretende criar. <br>- A instalação de gateway não está ligada a outro recurso azure gateway. <br>- A instalação de gateway está ligada à mesma conta Azure que está a usar para criar o recurso gateway. <br>- A sua conta Azure pertence a um único [inquilino ou diretório Azure Ative Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) e é a mesma conta que foi utilizada para a instalação do gateway. <p><p>Para mais informações, consulte a secção de [perguntas frequentes.](#faq) |
   |||

   Aqui está um exemplo que mostra uma instalação de gateway que está na mesma região que o seu recurso gateway e está ligado à mesma conta Azure:

   ![Fornecer detalhes para criar recurso de gateway de dados](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ligar aos dados no local

Depois de criar o seu recurso gateway e associar a sua subscrição Azure a este recurso, pode agora criar uma ligação entre a sua aplicação lógica e a sua fonte de dados no local utilizando o portal.

1. No portal Azure, crie ou abra a sua aplicação lógica no Logic App Designer.

1. Adicione um conector que suporta ligações no local, por exemplo, **SQL Server**.

1. Selecione **Connect via gateway de dados no local**.

1. Em **Gateways**, a partir da lista de **Subscrições,** selecione a sua subscrição Azure que tem o recurso gateway que deseja.

1. A partir da lista de Gateway de **Ligação,** que mostra os recursos de gateway disponíveis na sua subscrição selecionada, selecione o recurso gateway que deseja. Cada recurso de gateway está ligado a uma única instalação de gateway.

   > [!NOTE]
   > A lista de gateways inclui recursos gateway em outras regiões porque a localização da sua aplicação lógica pode diferir da localização do seu recurso gateway. 

1. Forneça um nome de ligação único e outras informações necessárias, que dependem da ligação que pretende criar.

   Um nome de ligação único ajuda-o facilmente a encontrar essa ligação mais tarde, especialmente se criar várias ligações. Se aplicável, inclua também o domínio qualificado para o seu nome de utilizador.

   Segue-se um exemplo:

   ![Criar ligação entre app lógica e gateway de dados](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Quando terminar, selecione **Criar**.

A sua ligação gateway está agora pronta para a sua aplicação lógica para usar.

## <a name="edit-connection"></a>Editar ligação

Para atualizar as definições para uma ligação de gateway, pode editar a sua ligação.

1. Para encontrar todas as ligações API apenas para a sua aplicação lógica, no menu da sua aplicação lógica, em **Ferramentas**de Desenvolvimento, selecione **ligações API**.

   ![No menu de aplicações lógicas, selecione "Conexões API"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Selecione a ligação de gateway que deseja e, em seguida, selecione a **ligação Edit API**.

   > [!TIP]
   > Se as suas atualizações não produzirem efeito, tente [parar e reiniciar a conta](../logic-apps/logic-apps-gateway-install.md#restart-gateway) de serviço gateway Windows para a instalação do gateway.

Para encontrar todas as ligações API associadas à sua subscrição Azure:

* No menu do portal Azure, selecione **todos os serviços** > **Web** > **API Connections**.
* Ou, a partir do menu do portal Azure, selecione **Todos os recursos.** Detete o filtro **Tipo** para **a Ligação API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Eliminar recurso gateway

Para criar um recurso de gateway diferente, ligue a instalação do gateway a um recurso de gateway diferente, ou remova o recurso gateway, pode eliminar o recurso gateway sem afetar a instalação do gateway.

1. A partir do menu do portal Azure, selecione **Todos os recursos,** ou procure e selecione **Todos os recursos** de qualquer página. Encontre e selecione o seu recurso de gateway.

1. Se ainda não estiver selecionado, no menu de recursos gateway, selecione **Gateway de Dados no local**. Na barra de ferramentas de recurso gateway, **selecione Eliminar**.

   Por exemplo:

   ![Eliminar recurso gateway em Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P:** Porque é que a minha instalação de gateway não aparece quando crio o meu recurso gateway em Azure? <br/>
**R**: Esta questão pode acontecer por estas razões:

* A sua conta Azure deve ser a mesma conta que está ligada à instalação de gateway no computador local. Verifique se está inscrito no portal Azure com a mesma identidade que está ligada à instalação do portal. Além disso, certifique-se de que a sua conta Azure pertence a um único [inquilino ou diretório Azure AD](../active-directory/fundamentals/active-directory-whatis.md#terminology) e está definido para o mesmo inquilino ou diretório Azure AD que foi usado durante a instalação do gateway.

* O seu recurso de gateway e a instalação gateway têm de utilizar a mesma região. No entanto, a localização da sua aplicação lógica pode diferir da localização do recurso gateway.

* A instalação do gateway já está registada e reclamada por outro recurso de gateway. Estas instalações não constam da lista de Nomes de **Instalação.** Para rever as suas inscrições de gateway no portal Azure, encontre todos os seus recursos Azure que possuam o tipo de **Gateways de Dados no local** em todas *as* suas subscrições Azure. Para desligar a instalação do gateway a partir do outro recurso de gateway, consulte Eliminar o [recurso gateway](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Passos seguintes

* [Proteger as suas aplicações lógicas](./logic-apps-securing-a-logic-app.md)
* [Exemplos e cenários comuns para aplicações lógicas](./logic-apps-examples-and-scenarios.md)
