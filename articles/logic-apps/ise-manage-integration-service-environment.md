---
title: Gerir ambientes de serviços de integração em Aplicações Lógicas Azure
description: Verifique a saúde da rede e gere aplicações lógicas, conexões, conectores personalizados e contas de integração no seu ambiente de serviço de integração (ISE) para Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1d91813e0f39207bcf7768de89600a6bdee0fc53
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358882"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Gerencie o seu ambiente de serviço de integração (ISE) em Aplicações Lógicas Azure

Para verificar a saúde da rede para o seu ambiente de serviço de [integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) e gerir as aplicações lógicas, conexões, contas de integração e conectores que existem no seu ISE, siga os passos neste tópico. Para adicionar estes artefactos ao seu ISE, consulte Adicionar artefactos ao seu ambiente de serviço de [integração.](../logic-apps/add-artifacts-integration-service-environment-ise.md)

## <a name="view-your-ise"></a>Ver o seu ISE

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa do portal, insira "ambientes de serviço de integração", e depois selecione Ambientes de Serviço de **Integração.**

   ![Encontre ambientes de serviço de integração](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Na lista de resultados, selecione o seu ambiente de serviço de integração.

   ![Selecione ambiente de serviço de integração](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Continue para as secções seguintes para encontrar aplicações lógicas, conexões, conectores ou contas de integração no seu ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Verifique a saúde da rede

No menu ISE, em **Definições,** selecione **Saúde da Rede**. Este painel mostra o estado de saúde das suas subredes e dependências de saída de outros serviços.

![Verifique a saúde da rede](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Gerencie as suas aplicações lógicas

Pode ver e gerir as aplicações lógicas que estão no seu ISE.

1. No seu menu ISE, em **Definições,** selecione **aplicações Lógica.**

   ![Ver aplicativos lógicos](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Para remover aplicações lógicas que já não precisa no ISE, selecione essas aplicações lógicas e, em seguida, selecione **Delete**. Para confirmar que pretende eliminar, selecione **Sim**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Gerir ligações API

Pode ver e gerir as ligações que foram criadas pelas aplicações lógicas que estão a funcionar no seu ISE.

1. No menu ISE, em **Definições,** selecione **ligações API**.

   ![Ver ligações API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Para remover as ligações que já não precisa no ISE, selecione essas ligações e, em seguida, **selecione Delete**. Para confirmar que pretende eliminar, selecione **Sim**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Gerir conectores ISE

Pode visualizar e gerir os conectores API que estão implantados no seu ISE.

1. No menu ISE, em **Definições,** selecione **conectores geridos**.

   ![Ver conectores geridos](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Para remover os conectores que não deseja disponíveis no ISE, selecione esses conectores e, em seguida, selecione **Delete**. Para confirmar que pretende eliminar, selecione **Sim**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Gerir conectores personalizados

Pode ver e gerir os conectores personalizados que implementou no ise.

1. No menu ISE, em **Definições,** selecione **conectores personalizados**.

   ![Localizar conectores personalizados](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Para remover conectores personalizados que já não precisa no ISE, selecione esses conectores e, em seguida, **selecione Delete**. Para confirmar que pretende eliminar, selecione **Sim**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Gerir contas de integração

1. No menu ISE, em **Definições,** selecione **contas de Integração.**

   ![Encontrar contas de integração](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Para remover as contas de integração do ISE quando já não for necessário, selecione essas contas de integração e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [ligar-se a redes virtuais Azure a partir de aplicações lógicas isoladas](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
