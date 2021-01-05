---
title: Conector de gestão de serviços de TI em Log Analytics
description: Este artigo fornece uma visão geral do Connector de Gestão de Serviços de TI (ITSMC) e informações sobre a sua utilização para monitorizar e gerir artigos de trabalho ITSM em Log Analytics e resolver problemas rapidamente.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 1f7a493c071e86114afd7d4a9e08e204bbab509d
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809484"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Ligue o Azure às ferramentas ITSM utilizando o Conector de Gestão de Serviços de TI

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Este artigo fornece informações sobre como configurar o Conector de Gestão de Serviços de TI (ITSMC) no Log Analytics para gerir centralmente os seus itens de trabalho.

## <a name="add-it-service-management-connector"></a>Adicionar conector de gestão de serviços de TI

Antes de criar uma ligação, tem de adicionar ITSMC.

1. No portal Azure, selecione **Criar um recurso**:

   ![Screenshot que mostra o item do menu de recursos Criar.](media/itsmc-overview/azure-add-new-resource.png)

2. Procure por **It Service Management Connector** no Azure Marketplace. Selecione **Criar:**

   ![Screenshot que mostra o botão Criar no Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. Na secção **ESPAÇO DE TRABALHO DE LA,** selecione o espaço de trabalho Azure Log Analytics onde pretende instalar o ITSMC.
   >[!NOTE]
   >
   > * ITSMC só pode ser instalado em espaços de trabalho Log Analytics nas seguintes regiões: Leste dos EUA, West US 2, South Central US, West Central US, US Gov Arizona, US Gov Virginia, Canada Central, West Europe, South UK, Southeast Asia, Japan East, Central India, e Australia Southeast.

4. Na secção **espaço de trabalho Log Analytics,** selecione o grupo de recursos onde pretende criar o recurso ITSMC:

   ![Screenshot que mostra a secção do espaço de trabalho Log Analytics.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para o Azure Monitor, os espaços de trabalho da OMS são agora referidos como *espaços de trabalho Log Analytics*.

5. Selecione **OK**.

Quando o recurso ITSMC é implantado, aparece uma notificação no canto superior direito da janela.

## <a name="create-an-itsm-connection"></a>Criar uma ligação ITSM

Depois de instalar o ITSMC, pode criar uma ligação.

Para criar uma ligação, terá de preparar a sua ferramenta ITSM para permitir a ligação a partir do ITSMC.  

Com base no produto ITSM a que está a ligar, selecione um dos seguintes links para instruções:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [Gestor de serviços do Centro de Sistema](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Depois de ter preparado as suas ferramentas ITSM, complete estes passos para criar uma ligação:

1. Em **Todos os recursos,** procure **o ServiceDesk(o *seu nome de espaço de trabalho)***:

   ![Screenshot que mostra recursos recentes no portal Azure.](media/itsmc-overview/itsm-connections.png)

1. Em **Fontes de Dados** do Espaço de Trabalho no painel esquerdo, selecione **ItsM Connections**:

   ![Screenshot que mostra o item do menu ITSM Connections.](media/itsmc-overview/add-new-itsm-connection.png)
1. Selecione **Adicionar Ligação**.

1. Especificar as definições de ligação descritas de acordo com os produtos/serviços ITSM:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [Gestor de serviços do Centro de Sistema](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   >
   > Por predefinição, o ITSMC atualiza os dados de configuração da ligação uma vez a cada 24 horas. Para atualizar os dados da sua ligação instantaneamente para refletir quaisquer edições ou atualizações de modelos que escote, selecione o botão **Sync** na lâmina da sua ligação:
   >
   > ![Screenshot que mostra o botão Sync na lâmina de ligação.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>Utilizar o ITSMC

   Pode utilizar o ITSMC para criar alertas a partir de alertas do Monitor Azure para a ferramenta ITSM.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Crie itens de trabalho ITSM a partir de alertas Azure

Depois de criar a sua ligação ITSM, pode criar itens de trabalho na sua ferramenta ITSM com base em alertas Azure. Para criar os itens de trabalho, você usará a ação ITSM em grupos de ação.

Os grupos de ação fornecem uma forma modular e reutilizável de desencadear ações para os seus alertas Azure. Pode utilizar grupos de ação com alertas métricos, alertas de registo de atividades e alertas Azure Log Analytics no portal Azure.

> [!NOTE]
> Depois de criar a ligação ITSM, tem de esperar 30 minutos para que o processo de sincronização termine.

### <a name="template-definitions"></a>Definições de modelo

   Existem tipos de artigos de trabalho que podem usar modelos que são definidos pela ferramenta ITSM.
Ao utilizar modelos, pode definir campos que serão automaticamente povoados de acordo com valores fixos que são definidos como parte do grupo de ação. Define modelos na ferramenta ITSM.
Pode definir qual o modelo que gostaria de usar como parte da definição do grupo de ação.

Utilize o seguinte procedimento para criar grupos de ação:

1. No portal Azure, selecione  **Alertas**.
2. No menu no topo do ecrã, selecione **Gerir as ações**:

    ![Screenshot que mostra o item do menu de ações Manage.](media/itsmc-overview/action-groups-selection-big.png)

   Aparece a janela **do grupo de ação Create.**

3. Selecione o grupo **de Subscrição** e **Recursos** onde pretende criar o seu grupo de ação. Forneça um **nome de grupo de ação** e nome de **exibição** para o seu grupo de ação. Selecione **Seguinte: Notificações**.

    ![Screenshot que mostra a janela do grupo de ação Create.](media/itsmc-overview/action-groups-details.png)

4. Na lista de notificações, selecione **Seguinte: Ações**.
5. Na lista de ações, selecione **ITSM** na lista **de Tipo de Ação.** Forneça um **nome** para a ação. Selecione o botão de caneta que representa **Editar detalhes.**

    ![Screenshot que mostra a definição de grupo de ação.](media/itsmc-definition/action-group-pen.png)

6. Na lista **de Subscrição,** selecione a subscrição na qual o seu espaço de trabalho Log Analytics está localizado. Na lista **'Ligação',** selecione o nome do conector ITSM. Será seguido pelo seu nome de espaço de trabalho. Por exemplo, MyITSMConnector (MyWorkspace).

7. Selecione um tipo **de item de trabalho.**

8. Se pretender preencher campos fora da caixa com valores fixos, selecione **Use Custom Template**. Caso contrário, escolha um [modelo](#template-definitions) existente na lista **de modelos** e introduza os valores fixos nos campos de modelo.

9. Se selecionar **Criar itens de trabalho individuais para cada Item de Configuração,** cada item de configuração terá o seu próprio item de trabalho. O que significa que haverá um item de trabalho por item de configuração.

    * Num caso que selecione no item de trabalho "Incidente" ou "Alerta": Se limpar os itens de trabalho individuais para cada caixa de **verificação de ponto de configuração,** cada alerta criará um novo item de trabalho. Pode haver mais de um alerta por item de configuração.

       ![Screenshot que mostra a janela itsm Incident.](media/itsmc-overview/itsm-action-configuration.png)

    * Num caso que selecione no ponto de trabalho "Evento": Se selecionar **Criar itens de trabalho individuais para cada Entrada** de Registo na seleção de botões de rádio, cada alerta criará um novo item de trabalho. Se selecionar **Criar itens de trabalho individuais para cada Item de Configuração** na seleção dos botões de rádio, cada item de configuração terá o seu próprio item de trabalho.
   ![Screenshot que mostra a janela do Evento ITSM.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Selecione **OK**.

Quando criar ou editar uma regra de alerta Azure, utilize um grupo de ação, que tenha uma ação ITSM. Quando o alerta é acionada, o item de trabalho é criado ou atualizado na ferramenta ITSM.

> [!NOTE]
>
>- Para obter informações sobre o preço da ação ITSM, consulte a [página de preços dos](https://azure.microsoft.com/pricing/details/monitor/) grupos de ação.
>
>
>- O campo de descrição curta na definição de regra de alerta é limitado a 40 caracteres quando o envia utilizando a ação ITSM.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas do Conector do ITSM](./itsmc-resync-servicenow.md)
