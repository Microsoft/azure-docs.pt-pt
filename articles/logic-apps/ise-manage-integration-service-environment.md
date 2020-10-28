---
title: Gerir ambientes de serviços de integração em Azure Logic Apps
description: Verifique a saúde da rede e gere aplicações lógicas, conexões, conectores personalizados e contas de integração no seu ambiente de serviço de integração (ISE) para Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: 4df9543e5e747de640562b7e5be224e257e0cfd1
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676106"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Gerir o ambiente de serviço de integração (ISE) no Azure Logic Apps

Este artigo mostra como executar tarefas de gestão para o seu ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)por exemplo:

* Gerencie os recursos tais como aplicações lógicas, conexões, contas de integração e conectores no seu ISE.

* Verifique a saúde da rede do ISE.

* Adicione capacidade, reinicie o ise ou elimine o ISE, siga os passos neste tópico. Para adicionar estes artefactos ao seu ISE, consulte [Adicionar artefactos ao seu ambiente de serviço de integração.](../logic-apps/add-artifacts-integration-service-environment-ise.md)

## <a name="view-your-ise"></a>Ver o seu ISE

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa do portal, insira "ambientes de serviço de integração", e, em seguida, selecione **Ambientes de Serviço de Integração** .

   ![Encontre ambientes de serviço de integração](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Na lista de resultados, selecione o seu ambiente de serviço de integração.

   ![Selecione ambiente de serviço de integração](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Continue até as próximas secções para encontrar aplicações lógicas, ligações, conectores ou contas de integração no seu ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Verifique a saúde da rede

No seu menu ISE, em **Definições,** selecione **Health** Network . Este painel mostra o estado de saúde das suas sub-redes e dependências de saída de outros serviços.

![Verifique a saúde da rede](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

> [!CAUTION]
> Se a rede do ISE não for saudável, o Ambiente interno de Serviço de Aplicações (ASE) que é utilizado pelo ISE também pode tornar-se insalubre. Se o ASE não estiver saudável por mais de sete dias, o ASE está suspenso. Para resolver este estado, verifique a sua configuração de rede virtual. Resolva quaisquer problemas que encontre e, em seguida, reinicie o seu ISE. Caso contrário, após 90 dias, o ASE suspenso é eliminado e o seu ISE torna-se inutilizável. Por isso, certifique-se de manter o seu ISE saudável para permitir o tráfego necessário.
> 
> Para obter mais informações, veja estes tópicos:
>
> * [Visão geral do Serviço de Aplicações Azure](../app-service/overview-diagnostics.md)
> * [Início de mensagem para ambiente de serviço de aplicações Azure](../app-service/environment/using-an-ase.md#logging)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Gerencie as suas aplicações lógicas

Pode ver e gerir as aplicações lógicas que estão no seu ISE.

1. No seu menu ISE, em **Definições,** selecione **Aplicações Lógicas** .

   ![Ver aplicativos lógicos](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Para remover aplicações lógicas que já não necessita no ISE, selecione essas aplicações lógicas e, em seguida, selecione **Delete** . Para confirmar que pretende eliminar, selecione **Sim** .

> [!NOTE]
> Se eliminar e recriar uma aplicação de lógica infantil, deve voltar a levantar a aplicação lógica dos pais. A aplicação infantil recriada terá metadados diferentes.
> Se não voltar a sair da aplicação lógica dos pais depois de recriar o seu filho, as suas chamadas para a aplicação de lógica infantil falharão com um erro de "não autorizado". Este comportamento aplica-se a aplicações lógicas de pais e filhos, por exemplo, aquelas que usam artefactos em contas de integração ou chamam funções de Azure.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Gerir ligações API

Pode ver e gerir as ligações que foram criadas pelas aplicações lógicas em execução no seu ISE.

1. No seu menu ISE, em **Definições,** selecione **ligações API** .

   ![Ver ligações API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Para remover as ligações que já não necessita no ISE, selecione essas ligações e, em seguida, **selecione Delete** . Para confirmar que pretende eliminar, selecione **Sim** .

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Gerir conectores ISE

Pode visualizar e gerir os conectores API que são implantados no seu ISE.

1. No seu menu ISE, em **Definições,** selecione **Conectores Geridos** .

   ![Ver conectores geridos](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Para remover os conectores que não pretende disponibilizar no seu ISE, selecione esses conectores e, em seguida, selecione **Delete** . Para confirmar que pretende eliminar, selecione **Sim** .

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Gerir conectores personalizados

Pode visualizar e gerir os conectores personalizados que implementou no seu ISE.

1. No seu menu ISE, em **Definições,** selecione **Conectores personalizados** .

   ![Localizar conectores personalizados](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Para remover os conectores personalizados que já não necessita no ISE, selecione esses conectores e, em seguida, selecione **Delete** . Para confirmar que pretende eliminar, selecione **Sim** .

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Gerir contas de integração

1. No seu menu ISE, em **Definições,** selecione **Contas de Integração** .

   ![Encontre contas de integração](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Para remover as contas de integração do ISE quando já não for necessário, selecione essas contas de integração e, em seguida, selecione **Delete** .

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Adicionar a capacidade do ISE

A unidade base Premium ISE tem capacidade fixa, por isso, se precisar de mais produção, pode adicionar mais unidades de escala, quer durante a criação quer depois. O Developer SKU não inclui a capacidade de adicionar unidades de escala.

1. No [portal Azure,](https://portal.azure.com)vá ao seu ISE.

1. Para rever as métricas de utilização e desempenho do seu ISE, no menu ISE, selecione **Overview** .

   ![Ver utilização para ISE](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. Em **Definições** , selecione **Scale out** . No **painel de configuração,** selecione a partir destas opções:

   * [**Escala manual**](#manual-scale): Escala com base no número de unidades de processamento que pretende utilizar.
   * [**Autoescala personalizada**](#custom-autoscale): Escala com base em métricas de desempenho selecionando de vários critérios e especificando as condições de limiar para o cumprimento desses critérios.

   ![Screenshot que mostra a página "Scale out" com "escala manual" selecionada.](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Escala manual

1. Depois de selecionar **a escala manual** , para capacidade **adicional,** selecione o número de unidades de escala que pretende utilizar.

   ![Selecione o tipo de escala que deseja](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. Quando tiver terminado, selecione **Guardar** .

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Autoescala personalizada

1. Depois de selecionar **autoescalação personalizada** , para **o nome de definição de autoescala,** forneça um nome para a sua definição e opcionalmente, selecione o grupo de recursos Azure onde a definição pertence.

   ![Fornecer nome para definição de autoescalação e selecionar grupo de recursos](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. Para a condição **predefinitiva,** selecione **a Escala com base numa métrica** ou escala para uma contagem de **instâncias específica** .

   * Se escolher a matrícula, insira o número das unidades de processamento, que é um valor de 0 a 10.

   * Se escolher base métrica, siga estes passos:

     1. Na secção **Regras,** **selecione Adicionar uma regra** .

     1. No painel **de regras da Escala,** estabeleça os seus critérios e ações a tomar quando a regra disparar.

     1. Por **exemplo, limites,** especifique estes valores:

        * **Mínimo** : O número mínimo de unidades de processamento a utilizar
        * **Máximo** : O número máximo de unidades de processamento a utilizar
        * **Predefinição** : Se ocorrerem problemas durante a leitura das métricas de recursos e a capacidade atual estiver abaixo da capacidade predefinida, a autoscalagem escala para o número predefinido das unidades de processamento. No entanto, se a capacidade atual exceder a capacidade padrão, a autoscalagem não escala.

1. Para adicionar outra condição, **selecione Adicionar a condição de escala** .

1. Quando terminar as definições de autoescala, guarde as alterações.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>Reiniciar ISE

Se alterar as definições do servidor DNS ou do servidor DNS, terá de reiniciar o ISE para que o ISE possa recolher essas alterações. Reiniciar um IsE Premium SKU não resulta em tempo de inatividade devido a redundância e componentes que reiniciam um de cada vez durante a reciclagem. No entanto, um desenvolvedor SKU ISE experimenta tempo de inatividade porque não existe redundância. Para mais informações, consulte [o ISE SKUs.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

1. No [portal Azure,](https://portal.azure.com)vá ao seu ISE.

1. No menu ISE, selecione **Overview** . Na barra de ferramentas Overview, **Reinicie** .

   ![Reiniciar ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>Eliminar o ISE

Antes de eliminar um ISE de que já não necessita ou de um grupo de recursos Azure que contenha um ISE, verifique se não tem políticas ou bloqueios no grupo de recursos Azure que contenha estes recursos ou na sua rede virtual Azure porque estes itens podem bloquear a eliminação.

Depois de eliminar o ISE, poderá ter de esperar até 9 horas antes de tentar eliminar a sua rede virtual Azure ou sub-redes.

## <a name="next-steps"></a>Passos seguintes

* [Adicionar recursos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
