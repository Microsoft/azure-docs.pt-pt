---
title: Gerenciar ambientes de serviço de integração nos aplicativos lógicos do Azure
description: Verificar a integridade da rede e gerenciar aplicativos lógicos, conexões, conectores personalizados e contas de integração no ISE (ambiente do serviço de integração) para aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 8f10e3d3fd7c67d1e803e8f85c9918c91bb81d59
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517465"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Gerenciar seu ISE (ambiente do serviço de integração) nos aplicativos lógicos do Azure

Para verificar a integridade da rede para o seu [ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) e gerenciar os aplicativos lógicos, conexões, contas de integração e conectores personalizados que existem no ISE, siga as etapas neste tópico.

## <a name="view-your-ise"></a>Exibir seu ISE

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa do portal, insira "ambientes do serviço de integração" e, em seguida, selecione **ambientes do serviço de integração**.

   ![Localizar ambientes de serviço de integração](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Na lista de resultados, selecione o ambiente do serviço de integração.

   ![Selecione o ambiente do serviço de integração](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Continue nas próximas seções para localizar aplicativos lógicos, conexões, conectores ou contas de integração no ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Verificar a integridade da rede

No menu do ISE, em **configurações**, selecione **integridade da rede**. Esse painel mostra o status de integridade para suas sub-redes e dependências de saída em outros serviços.

![Verificar a integridade da rede](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Gerenciar seus aplicativos lógicos

1. No menu do ISE, em **configurações**, selecione **aplicativos lógicos**.

   ![Localizar aplicativos lógicos](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Para remover aplicativos lógicos do ISE quando não forem mais necessários, selecione esses aplicativos lógicos e, em seguida, selecione **excluir**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Gerenciar conexões de API

1. Para exibir as conexões de API que foram criadas por aplicativos lógicos em execução no ISE, no menu do ISE, em **configurações**, selecione **conexões de API**.

   ![Localizar conexões de API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Para remover conexões do ISE quando elas não forem mais necessárias, selecione essas conexões e, em seguida, selecione **excluir**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Gerenciar conectores personalizados

1. Para exibir conectores personalizados que foram criados no ISE, no menu do ISE, em **configurações**, selecione **conectores personalizados**.

   ![Localizar conectores personalizados](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Para remover conectores personalizados do ISE quando não forem mais necessários, selecione esses conectores e, em seguida, selecione **excluir**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Gerir contas de integração

1. No menu do ISE, em **configurações**, selecione **contas de integração**.

   ![Localizar contas de integração](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Para remover contas de integração do ISE quando não forem mais necessárias, selecione as contas de integração e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [se conectar a redes virtuais do Azure de aplicativos lógicos isolados](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
