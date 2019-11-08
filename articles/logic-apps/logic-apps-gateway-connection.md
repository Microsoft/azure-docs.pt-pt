---
title: Acessar fontes de dados locais-aplicativos lógicos do Azure
description: Conectar-se a fontes de dados locais de aplicativos lógicos do Azure criando um recurso de gateway de dados local do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: e0354f96036cce968e6b6909a18f97ff48347eda
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796259"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Conectar-se a fontes de dados locais de aplicativos lógicos do Azure

Antes de poder acessar as fontes de dados locais de seus aplicativos lógicos, você precisará criar um recurso do Azure depois de [instalar o *Gateway de dados* local em um computador local](../logic-apps/logic-apps-gateway-install.md). Seus aplicativos lógicos usam esse recurso de gateway do Azure nos gatilhos e ações fornecidas pelos [conectores locais](../connectors/apis-list.md#on-premises-connectors) que estão disponíveis para aplicativos lógicos do Azure.

Este artigo mostra como criar seu recurso de gateway do Azure para um [Gateway instalado anteriormente no computador local](../logic-apps/logic-apps-gateway-install.md). Para obter mais informações sobre o gateway, consulte [como funciona o gateway](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Para se conectar às redes virtuais do Azure, considere criar um [*ambiente de serviço de integração*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) em vez disso. 

Para obter informações sobre como usar o gateway com outros serviços, consulte estes artigos:

* [Gateway de dados local do Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Automatizar o gateway de dados local de energia](https://flow.microsoft.com/documentation/gateway-manage/)
* [Gateway de dados local do Microsoft Power apps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services Gateway de dados local](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Origens de dados suportadas

Nos aplicativos lógicos do Azure, o gateway de dados local dá suporte aos [conectores locais](../connectors/apis-list.md#on-premises-connectors) para essas fontes de dados:

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

O aplicativo lógico do Azure dá suporte a operações de leitura e gravação por meio do gateway de dados. No entanto, essas operações têm [limites de seu tamanho de carga](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). Embora o próprio gateway não incorra em custos adicionais, o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md) se aplica a esses conectores e a outras operações nos aplicativos lógicos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Você já [instalou o gateway de dados local em um computador local](../logic-apps/logic-apps-gateway-install.md).

* Você está usando a [mesma conta e assinatura do Azure](../logic-apps/logic-apps-gateway-install.md#requirements) que foi usada ao instalar o gateway de dados. Essa conta do Azure deve pertencer a um único [locatário ou diretório do Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology).

* A instalação do gateway ainda não está registrada e reivindicada por outro recurso de gateway do Azure.

  Ao criar um recurso de gateway no portal do Azure, você seleciona uma instalação de gateway, que vincula o recurso de gateway e apenas esse recurso de gateway. Em aplicativos lógicos do Azure, gatilhos e ações locais usam o recurso de gateway para se conectar a fontes de dados locais. Nesses gatilhos e ações, você seleciona sua assinatura do Azure e o recurso de gateway associado que deseja usar. Cada recurso de gateway vincula-se a apenas uma instalação de gateway, que é vinculada a apenas uma conta do Azure.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Criar recurso de gateway do Azure

Depois de instalar o gateway em um computador local, crie o recurso do Azure para seu gateway.

1. Entre no [portal do Azure](https://portal.azure.com) com a mesma conta do Azure que foi usada para instalar o gateway.

1. Na caixa de pesquisa portal do Azure, insira "gateway de dados local" e selecione **gateways de dados locais**.

   ![Localizar "gateway de dados local"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. Em **gateways de dados locais**, selecione **Adicionar**.

   ![Adicionar novo recurso do Azure para gateway de dados](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Em **criar gateway de conexão**, forneça essas informações para o recurso de gateway. Quando terminar, selecione **Criar**.

   | Propriedade | Descrição |
   |----------|-------------|
   | **Nome do Recurso** | Forneça um nome para o recurso de gateway que contém apenas letras, números, hifens (`-`), sublinhados (`_`), parênteses (`(`, `)`) ou pontos (`.`). |
   | **Subscrição** | Selecione a assinatura do Azure para a conta do Azure que foi usada para a instalação do gateway. A assinatura padrão é baseada na conta do Azure que você usou para entrar. |
   | **Grupo de recursos** | O [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) que você deseja usar |
   | **Localização** | A mesma região ou local que foi selecionado para o serviço de nuvem do gateway durante a [instalação do gateway](../logic-apps/logic-apps-gateway-install.md). Caso contrário, a instalação do gateway não aparecerá na lista **nome da instalação** . O local do aplicativo lógico pode ser diferente do local do recurso do gateway. |
   | **Nome da instalação** | Selecione uma instalação de gateway, que aparece na lista somente quando essas condições são atendidas: <p><p>-A instalação do gateway usa a mesma região que o recurso de gateway que você deseja criar. <br>-A instalação do gateway não está vinculada a outro recurso de gateway do Azure. <br>-A instalação do gateway está vinculada à mesma conta do Azure que você está usando para criar o recurso de gateway. <br>-Sua conta do Azure pertence a um único [locatário ou diretório do Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) e é a mesma conta que foi usada para a instalação do gateway. <p><p>Para obter mais informações, consulte a seção [perguntas](#faq) frequentes. |
   |||

   Aqui está um exemplo que mostra uma instalação de gateway que está na mesma região que o recurso de gateway e está vinculado à mesma conta do Azure:

   ![Fornecer detalhes para criar um recurso de gateway de dados](./media/logic-apps-gateway-connection/add-azure-data-gateway-information.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ligar aos dados no local

Depois de criar o recurso de gateway e associar sua assinatura do Azure a esse recurso, agora você pode criar uma conexão entre seu aplicativo lógico e sua fonte de dados local usando o gateway.

1. No portal do Azure, crie ou abra seu aplicativo lógico no designer de aplicativo lógico.

1. Adicione um conector que ofereça suporte a conexões locais, por exemplo, **SQL Server**.

1. Selecione **conectar por meio do gateway de dados local**.

1. Em **gateways**, na lista **assinaturas** , selecione sua assinatura do Azure que tenha o recurso de gateway desejado.

1. Na lista **Gateway de conexão** , que mostra os recursos de gateway disponíveis em sua assinatura selecionada, selecione o recurso de gateway desejado. Cada recurso de gateway é vinculado a uma única instalação de gateway.

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

1. Para localizar todas as conexões de API apenas para seu aplicativo lógico, no menu do aplicativo lógico, em **ferramentas de desenvolvimento**, selecione **conexões de API**.

   ![No menu do aplicativo lógico, selecione "conexões de API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. Selecione a conexão de gateway desejada e, em seguida, selecione **Editar conexão de API**.

   > [!TIP]
   > Se as atualizações não entrarem em vigor, tente [parar e reiniciar a conta de serviço do Windows de gateway](../logic-apps/logic-apps-gateway-install.md#restart-gateway) para a instalação do gateway.

Para localizar todas as conexões de API associadas à sua assinatura do Azure:

* No menu principal do Azure, vá para **todos os serviços** > **conexões da API**de > **da Web** .
* Ou, no menu principal do Azure, vá para **todos os recursos**. Defina o filtro de **tipo** como **conexão de API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Excluir recurso de gateway

Para criar um recurso de gateway diferente, vincule a instalação do gateway a um recurso de gateway diferente ou remova o recurso de gateway, você pode excluir o recurso de gateway sem afetar a instalação do gateway.

1. No menu principal do Azure, selecione **todos os recursos**. Localize e selecione o recurso de gateway.

1. Se ainda não estiver selecionado, no menu de recursos do gateway, selecione **Gateway de dados local**. Na barra de ferramentas de recursos do gateway, selecione **excluir**.

   Por exemplo:

   ![Excluir recurso de gateway no Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway-resource.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P**: por que minha instalação de gateway não aparece quando crio meu recurso de gateway no Azure? <br/>
**R**: esse problema pode ocorrer por esses motivos:

* Sua conta do Azure deve ser a mesma conta vinculada à instalação do gateway no computador local. Verifique se você está conectado ao portal do Azure com a mesma identidade vinculada à instalação do gateway. Além disso, verifique se sua conta do Azure pertence a um único [locatário ou diretório do Azure ad](../active-directory/fundamentals/active-directory-whatis.md#terminology) e está definida para o mesmo diretório ou locatário do Azure AD que foi usado durante a instalação do gateway.

* O recurso de gateway e a instalação do gateway precisam usar a mesma região. No entanto, o local do aplicativo lógico pode ser diferente do local do recurso do gateway.

* A instalação do gateway já está registrada e reivindicada por outro recurso de gateway. Essas instalações não aparecerão na lista **nome da instalação** . Para examinar os registros de gateway no portal do Azure, encontre todos os recursos do Azure que têm o tipo de **gateways de dados local** em *todas as* suas assinaturas do Azure. Para desvincular a instalação do gateway do outro recurso de gateway, consulte [excluir recurso de gateway](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Passos seguintes

* [Proteger as suas aplicações lógicas](./logic-apps-securing-a-logic-app.md)
* [Exemplos e cenários comuns para aplicativos lógicos](./logic-apps-examples-and-scenarios.md)
