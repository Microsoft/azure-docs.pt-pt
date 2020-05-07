---
title: Gerir ambientes de serviços de integração em Aplicações Lógicas Azure
description: Verifique a saúde da rede e gere aplicações lógicas, conexões, conectores personalizados e contas de integração no seu ambiente de serviço de integração (ISE) para Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 41e511bce3599dd341ccf8192612e3f08111245a
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598424"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Gerencie o seu ambiente de serviço de integração (ISE) em Aplicações Lógicas Azure

Este artigo mostra como executar tarefas de gestão para o seu ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)por exemplo:

* Gerencie os recursos, tais como aplicações lógicas, conexões, contas de integração e conectores no seu ISE.
* Verifique a saúde da rede do ISE.
* Adicione capacidade, reinicie o ISE ou elimine o ISE, siga os passos neste tópico. Para adicionar estes artefactos ao seu ISE, consulte Adicionar artefactos ao seu ambiente de serviço de [integração.](../logic-apps/add-artifacts-integration-service-environment-ise.md)

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

> [!NOTE]
> Se eliminar e recriar uma aplicação de lógica infantil, terá de resalvar a aplicação lógica dos pais. A aplicação infantil recriada terá diferentes metadados.
> Se não resalvar a aplicação lógica dos pais depois de recriar o seu filho, as suas chamadas para a aplicação lógica infantil falharão com um erro de "não autorizado". Este comportamento aplica-se a aplicações lógicas pais-filhos, por exemplo, aquelas que usam artefactos em contas de integração ou chamam funções de Azure.

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

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Adicionar a capacidade do ISE

A unidade base Premium ISE tem capacidade fixa, por isso, se precisar de mais energia, pode adicionar mais unidades de escala, seja durante a criação ou depois. O Developer SKU não inclui a capacidade de adicionar unidades de escala.

1. No [portal Azure,](https://portal.azure.com)vá ao seu ISE.

1. Para rever as métricas de utilização e desempenho do seu ISE, no menu ISE, selecione **Visão Geral**.

   ![Ver utilização para ISE](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. Em **Definições,** **selecione Scale out**. No painel **Configure,** selecione a partir destas opções:

   * [**Escala manual**](#manual-scale): Escala com base no número de unidades de processamento que pretende utilizar.
   * [**Escala automática personalizada**](#custom-autoscale): Escala com base nas métricas de desempenho selecionando de vários critérios e especificando as condições de limiar para o cumprimento desse critério.

   ![Selecione o tipo de escala que deseja](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Escala manual

1. Depois de selecionar **a escala Manual**, para capacidade **adicional,** selecione o número de unidades de escala que pretende utilizar.

   ![Selecione o tipo de escala que deseja](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. Quando tiver terminado, selecione **Guardar**.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Escala automática personalizada

1. Depois de selecionar **a escala automática Personalizada**, para o nome de definição de escala **automática,** forneça um nome para a sua definição e opcionalmente, selecione o grupo de recursos Azure onde a definição pertence.

   ![Forneça nome para definição de escala automática e selecione grupo de recursos](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. Para a condição **predefinida,** selecione **a escala com base numa métrica** ou escala para uma contagem de **instâncias específicas**.

   * Se escolher com base em instâncias, insira o número para as unidades de processamento, que é um valor de 0 a 10.

   * Se escolher com base em métricas, siga estes passos:

     1. Na secção **Regras,** selecione **Adicionar uma regra**.

     1. No painel de **regras scale,** estabeleça os seus critérios e medidas a tomar quando a regra disparar.

     1. Por **exemplo, limites,** especifique estes valores:

        * **Mínimo**: O número mínimo de unidades de transformação a utilizar
        * **Máximo**: O número máximo de unidades de processamento a utilizar
        * **Predefinido**: Se ocorrerem problemas durante a leitura das métricas de recursos, e a capacidade atual estiver abaixo da capacidade padrão, a autoscalcificação dimensiona-se para o número padrão de unidades de processamento. No entanto, se a capacidade atual exceder a capacidade padrão, a autoescalação não entra em escala.

1. Para adicionar outra condição, **selecione Adicionar condição de escala**.

1. Quando terminar as definições de escala automática, guarde as alterações.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>Reiniciar ise

Se alterar as definições do servidor DNS ou do servidor DNS, tem de reiniciar o ISE para que o ISE possa recolher essas alterações. Reiniciar um Premium SKU ISE não resulta em tempo de paragem devido a redundância e componentes que reiniciam um de cada vez durante a reciclagem. No entanto, um Developer SKU ISE experimenta tempo de inatividade porque não existe redundância. Para mais informações, consulte [ise SKUs](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

1. No [portal Azure,](https://portal.azure.com)vá ao seu ISE.

1. No menu ISE, selecione **Visão Geral**. Na barra de ferramentas Overview, **Restart**.

   ![Reiniciar o ambiente do serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>Eliminar o ISE

Antes de eliminar um ISE de que já não precisa ou um grupo de recursos Azure que contenha um ISE, verifique se não tem políticas ou fechaduras no grupo de recursos Azure que contenha esses recursos ou na sua rede virtual Azure porque estes itens podem bloquear a eliminação.

Depois de eliminar o ise, poderá ter de esperar até 9 horas antes de tentar eliminar a rede virtual do Azure ou subredes.

## <a name="next-steps"></a>Passos seguintes

* [Adicionar recursos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)