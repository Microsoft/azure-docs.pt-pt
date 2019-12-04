---
title: Gerenciar ambientes de serviço de integração nos aplicativos lógicos do Azure
description: Verificar a integridade da rede e gerenciar aplicativos lógicos, conexões, conectores personalizados e contas de integração no ISE (ambiente do serviço de integração) para aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1d91813e0f39207bcf7768de89600a6bdee0fc53
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792616"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Gerenciar seu ISE (ambiente do serviço de integração) nos aplicativos lógicos do Azure

Para verificar a integridade da rede para o seu [ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) e gerenciar os aplicativos lógicos, conexões, contas de integração e conectores que existem no ISE, siga as etapas neste tópico. Para adicionar esses artefatos ao ISE, consulte [Adicionar artefatos ao seu ambiente do serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md).

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

Você pode exibir e gerenciar os aplicativos lógicos que estão no ISE.

1. No menu do ISE, em **configurações**, selecione **aplicativos lógicos**.

   ![Exibir aplicativos lógicos](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Para remover os aplicativos lógicos que você não precisa mais no ISE, selecione esses aplicativos lógicos e, em seguida, selecione **excluir**. Para confirmar que deseja excluir, selecione **Sim**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Gerenciar conexões de API

Você pode exibir e gerenciar as conexões que foram criadas pelos aplicativos lógicos em execução no ISE.

1. No menu do ISE, em **configurações**, selecione **conexões de API**.

   ![Exibir conexões de API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Para remover as conexões que você não precisa mais no ISE, selecione essas conexões e, em seguida, selecione **excluir**. Para confirmar que deseja excluir, selecione **Sim**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Gerenciar conectores do ISE

Você pode exibir e gerenciar os conectores de API que são implantados no ISE.

1. No menu do ISE, em **configurações**, selecione **conectores gerenciados**.

   ![Exibir conectores gerenciados](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Para remover os conectores que você não deseja disponibilizar no ISE, selecione esses conectores e, em seguida, selecione **excluir**. Para confirmar que deseja excluir, selecione **Sim**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Gerenciar conectores personalizados

Você pode exibir e gerenciar os conectores personalizados que você implantou no ISE.

1. No menu do ISE, em **configurações**, selecione **conectores personalizados**.

   ![Localizar conectores personalizados](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Para remover conectores personalizados que não são mais necessários no ISE, selecione esses conectores e, em seguida, selecione **excluir**. Para confirmar que deseja excluir, selecione **Sim**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Gerir contas de integração

1. No menu do ISE, em **configurações**, selecione **contas de integração**.

   ![Localizar contas de integração](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Para remover contas de integração do ISE quando não forem mais necessárias, selecione as contas de integração e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [se conectar a redes virtuais do Azure de aplicativos lógicos isolados](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
