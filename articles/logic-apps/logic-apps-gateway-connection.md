---
title: Acessar fontes de dados locais de aplicativos lógicos do Azure
description: Conectar-se a fontes de dados locais de aplicativos lógicos criando um gateway de dados local
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: ed2ba70b803940700044e900a1b2bb6607c0f051
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934051"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Conectar-se a fontes de dados locais de aplicativos lógicos do Azure

Para acessar fontes de dados locais de seus aplicativos lógicos, crie um recurso de gateway de dados local no portal do Azure. Seus aplicativos lógicos podem usar os [conectores locais](../connectors/apis-list.md#on-premises-connectors). Este artigo mostra como criar o recurso de gateway do Azure *depois* de [baixar e instalar o gateway em um computador local](../logic-apps/logic-apps-gateway-install.md). Para obter mais informações sobre o gateway, consulte [como funciona o gateway](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Para se conectar às redes virtuais do Azure, considere criar um [*ambiente de serviço de integração*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) em vez disso. 

Para obter informações sobre como usar o gateway com outros serviços, consulte estes artigos:

* [Gateway de dados local do Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow gateway de dados local](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps gateway de dados local](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services Gateway de dados local](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Origens de dados suportadas

Para aplicativos lógicos do Azure, o gateway de dados local dá suporte a [conectores locais](../connectors/apis-list.md#on-premises-connectors) para essas fontes de dados:

* BizTalk Server 2016
* Sistema de ficheiros
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Embora o próprio gateway não incorra em custos adicionais, o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md) se aplica a esses conectores e a outras operações nos aplicativos lógicos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Você já [instalou o gateway de dados local em um computador local](../logic-apps/logic-apps-gateway-install.md).

* Você tem a [mesma conta do Azure e a assinatura do Azure](../logic-apps/logic-apps-gateway-install.md#requirements) que usou quando instalou o gateway de dados local.

* Você não vinculou anteriormente a instalação do gateway a outro recurso de gateway no Azure.

  Ao criar um recurso de gateway, você seleciona uma instalação de gateway para associar ao recurso de gateway. Uma instalação de gateway já vinculada não está disponível para você selecionar ao criar recursos de gateway.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Criar recurso de gateway do Azure

Depois de instalar o gateway em um computador local, crie o recurso do Azure para seu gateway. 

1. Entre no [portal do Azure](https://portal.azure.com) com a mesma conta do Azure que foi usada para instalar o gateway.

1. Na caixa de pesquisa portal do Azure, insira "gateway de dados local" e selecione **gateways de dados locais**.

   ![Localizar "gateway de dados local"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. Em **gateways de dados locais**, selecione **Adicionar**.

   ![Adicionar gateway de dados](./media/logic-apps-gateway-connection/add-gateway.png)

1. Em **criar gateway de conexão**, forneça essas informações para o recurso de gateway. Quando terminar, selecione **Criar**.

   | Propriedade | Descrição |
   |----------|-------------|
   | **Nome do Recurso** | O nome do recurso de gateway, que pode conter apenas letras`-`, números, hifens (), sublinhados (`_`), parênteses `)`(`(`,) e pontos`.`(). |
   | **Subscrição** | Sua assinatura do Azure, que deve ser igual à instalação do gateway e ao aplicativo lógico. A assinatura padrão é baseada na conta do Azure que você usou para entrar. |
   | **Grupo de recursos** | O [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) que você deseja usar |
   | **Location** | A mesma região que o local selecionado para o serviço de nuvem do gateway durante a [instalação do gateway](../logic-apps/logic-apps-gateway-install.md). Caso contrário, a instalação do gateway não aparecerá na lista **nome da instalação** para que você selecione. O local do aplicativo lógico pode ser diferente do local do recurso do gateway. |
   | **Nome da instalação** | Se a instalação do gateway ainda não estiver selecionada, selecione o gateway que você instalou anteriormente. As instalações de gateway vinculadas anteriormente não aparecerão nessa lista para você selecionar. |
   |||

   Segue-se um exemplo:

   ![Forneça detalhes para criar seu gateway de dados local](./media/logic-apps-gateway-connection/gateway-details.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ligar aos dados no local

Depois de criar o recurso de gateway e associar sua assinatura do Azure a esse recurso, agora você pode criar uma conexão entre seu aplicativo lógico e sua fonte de dados local usando o gateway.

1. No portal do Azure, crie ou abra seu aplicativo lógico no designer de aplicativo lógico.

1. Adicione um conector que ofereça suporte a conexões locais, por exemplo, **SQL Server**.

1. Selecione **conectar por meio do gateway de dados local**. 

1. Para **gateways**, selecione o recurso de gateway que você criou.

   > [!NOTE]
   > A lista de gateways inclui recursos de gateway em outras regiões porque o local do seu aplicativo lógico pode ser diferente do local do seu recurso de gateway.

1. Forneça um nome de conexão exclusivo e outras informações necessárias, que dependem da conexão que você deseja criar.

   Um nome de conexão exclusivo ajuda a localizar facilmente essa conexão mais tarde, especialmente se você criar várias conexões. Se aplicável, inclua também o domínio qualificado para seu nome de usuário.
   
   Segue-se um exemplo:

   ![Criar conexão entre o aplicativo lógico e o gateway de dados](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Quando terminar, selecione **Criar**. 

Sua conexão de gateway agora está pronta para uso do seu aplicativo lógico.

## <a name="edit-connection"></a>Editar conexão

Para atualizar as configurações de uma conexão de gateway, você pode editar sua conexão.

1. Para localizar todas as conexões de API apenas para seu aplicativo lógico, no menu do aplicativo lógico, em **ferramentas de desenvolvimento**, selecione conexões de **API**.
   
   ![No menu do aplicativo lógico, selecione "conexões de API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. Selecione a conexão de gateway desejada e, em seguida, selecione **Editar conexão de API**.

   > [!TIP]
   > Se as atualizações não entrarem em vigor, tente [parar e reiniciar a conta de serviço do Windows de gateway](../logic-apps/logic-apps-gateway-install.md#restart-gateway) para a instalação do gateway.

Para localizar todas as conexões de API associadas à sua assinatura do Azure: 

* No menu principal do Azure, vá para **todos os serviços** > **conexões de API** **da Web** > .
* Ou, no menu principal do Azure, vá para **todos os recursos**. Defina o filtro de **tipo** como **conexão de API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Excluir recurso de gateway

Para criar um recurso de gateway diferente, vincule a instalação do gateway a um recurso de gateway diferente ou remova o recurso de gateway, você pode excluir o recurso de gateway sem afetar a instalação do gateway. 

1. No menu principal do Azure, selecione **todos os recursos**. Localize e selecione o recurso de gateway.

1. Se ainda não estiver selecionado, no menu de recursos do gateway, selecione **Gateway de dados local**. Na barra de ferramentas de recursos do gateway, selecione **excluir**.

   Por exemplo:

   ![Excluir gateway](./media/logic-apps-gateway-connection/gateway-delete.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P**: Por que não vejo minha instalação de gateway Quando crio o recurso de gateway no Azure? <br/>
**A**: Esse problema pode ocorrer por esses motivos:

* A instalação do gateway já está registrada e reivindicada por outro recurso de gateway no Azure. As instalações de gateway não aparecem na lista de instâncias depois que os recursos de gateway são criados para eles. Para verificar os registros de gateway no portal do Azure, examine todos os recursos do Azure com o tipo de **gateways de dados local** para *todas as* assinaturas do Azure.

* A identidade do Azure AD para a pessoa que instalou o gateway é diferente da pessoa que se conectou ao portal do Azure. Verifique se você está conectado com a mesma identidade que instalou o gateway.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Passos seguintes

* [Proteger as suas aplicações lógicas](./logic-apps-securing-a-logic-app.md)
* [Exemplos e cenários comuns para aplicativos lógicos](./logic-apps-examples-and-scenarios.md)
