---
title: Conector de gestão de serviços de TI em Log Analytics
description: Este artigo fornece uma visão geral do Connector de Gestão de Serviços de TI (ITSMC) e informações sobre a sua utilização para monitorizar e gerir artigos de trabalho ITSM em Log Analytics e resolver problemas rapidamente.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 30be0343b7c3282e3ce4da165225ab8209d07f4a
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452412"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-solution"></a>Ligue o Azure às ferramentas ITSM utilizando a Solução de Gestão de Serviços de TI

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Este artigo fornece informações sobre como configurar o Conector de Gestão de Serviços de TI (ITSMC) no Log Analytics para gerir centralmente os seus itens de trabalho de Gestão de Serviços de TI (ITSM).

## <a name="add-it-service-management-connector"></a>Adicionar conector de gestão de serviços de TI

Antes de poder criar uma ligação, tem de instalar o ITSMC.

1. No portal Azure, selecione **Criar um recurso**:

   ![Screenshot que mostra o item do menu para criar um recurso.](media/itsmc-overview/azure-add-new-resource.png)

2. Procure por **It Service Management Connector** no Azure Marketplace. Em seguida, **selecione Criar**:

   ![Screenshot que mostra o botão Criar no Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. Na secção **ESPAÇO DE TRABALHO DE LA,** selecione o espaço de trabalho Log Analytics onde pretende instalar o ITSMC.
   > [!NOTE]
   > Você pode instalar ITSMC em log analytics espaços de trabalho apenas nas seguintes regiões: Leste DOS EUA, West US 2, South Central US, West Central US, US Gov Arizona, US Gov Virginia, Canadá Central, West Europe, South UK, Southeast Asia, Japan East, Central India, e Australia Southeast.

4. Na secção **espaço de trabalho Log Analytics,** selecione o grupo de recursos onde pretende criar o recurso ITSMC:

   ![Screenshot que mostra a secção do espaço de trabalho Log Analytics.](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para o Azure Monitor, os espaços de trabalho da OMS são agora *chamados de espaços de trabalho Log Analytics*.

5. Selecione **OK**.

Quando o recurso ITSMC é implantado, aparece uma notificação no canto superior direito da janela.

## <a name="create-an-itsm-connection"></a>Criar uma ligação ITSM

Depois de ter instalado o ITSMC, tem de preparar a sua ferramenta ITSM para permitir a ligação a partir do ITSMC. Com base no produto ITSM a que está a ligar, selecione um dos seguintes links para instruções:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Depois de ter preparado a sua ferramenta ITSM, complete estes passos para criar uma ligação:

1. Em **Todos os recursos,** procure **o ServiceDesk(o *seu nome de espaço de trabalho)***:

   ![Screenshot que mostra recursos recentes no portal Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

1. Em **Fontes de Dados** do Espaço de Trabalho no painel esquerdo, selecione **Ligações ITSM**:

   ![Screenshot que mostra o item do menu ITSM Connections.](media/itsmc-overview/add-new-itsm-connection.png)

1. Selecione **Adicionar Ligação**.

1. Especifique as definições de ligação de acordo com o produto ITSM que está a utilizar:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > Por predefinição, o ITSMC atualiza os dados de configuração da ligação uma vez a cada 24 horas. Para atualizar os dados da sua ligação instantaneamente para refletir quaisquer edições ou atualizações de modelos que escote, selecione o botão **Sync** no painel da sua ligação:
   >
   > ![Screenshot que mostra o botão Sync no painel da ligação.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>Crie itens de trabalho ITSM a partir de alertas Azure

Depois de criar a sua ligação ITSM, pode utilizar o ITMC para criar itens de trabalho na sua ferramenta ITSM com base em alertas Azure. Para criar os itens de trabalho, você usará a ação ITSM em grupos de ação.

Os grupos de ação fornecem uma forma modular e reutilizável de desencadear ações para os seus alertas Azure. Pode utilizar grupos de ação com alertas métricos, alertas de registo de atividades e alertas de Log Analytics no portal Azure.

> [!NOTE]
> Depois de criar a ligação ITSM, tem de esperar 30 minutos para que o processo de sincronização termine.

## <a name="define-a-template"></a>Defina um modelo

Certos tipos de artigos de trabalho podem utilizar modelos que define na ferramenta ITSM. Ao utilizar modelos, pode definir campos que serão automaticamente povoados de acordo com valores fixos para um grupo de ação. Pode definir qual o modelo que pretende utilizar como parte da definição de um grupo de ação.

Para criar um grupo de ação:

1. No portal Azure, selecione  **Alertas**.
2. No menu na parte superior do ecrã, selecione **Gerir as ações**:

    ![Screenshot que mostra o item do menu de ações Manage.](media/itsmc-overview/action-groups-selection-big.png)

   Aparece a janela **do grupo de ação Create.**

3. Selecione o grupo **de Subscrição** e **Recursos** onde pretende criar o seu grupo de ação. Forneça valores no **nome do grupo action** e no nome **display** para o seu grupo de ação. Em seguida, selecione **Seguinte: Notificações**.

    ![Screenshot que mostra a janela do grupo de ação Create.](media/itsmc-overview/action-groups-details.png)

4. No separador **Notificações,** selecione **Seguinte: Ações**.
5. No **separador Ações,** selecione **ITSM** na lista **de Tipo de Ação.** Para **nome,** forneça um nome para a ação. Em seguida, selecione o botão de caneta que representa **editar detalhes**.

    ![Screenshot que mostra seleções para criar um grupo de ação.](media/itsmc-definition/action-group-pen.png)

6. Na lista **de Subscrição,** selecione a subscrição que contém o seu espaço de trabalho Log Analytics. Na lista **'Ligação',** selecione o nome do conector ITSM. Será seguido pelo seu nome de espaço de trabalho. Um exemplo é *MyITSMConnector (MyWorkspace)*.

7. Selecione um tipo **de item de trabalho.**

8. Se pretender preencher campos fora da caixa com valores fixos, selecione **Use Custom Template**. Caso contrário, escolha um [modelo](#define-a-template) existente na lista **de modelos** e introduza os valores fixos nos campos de modelo.

9. Na última secção da interface para a criação de um grupo de ação ITSM, pode definir quantos itens de trabalho serão criados para cada alerta.

   > [!NOTE]
   > Esta secção é relevante apenas para alertas de pesquisa de registo. Para todos os outros tipos de alerta, irá criar um item de trabalho por alerta.

   * Se selecionou **Incidente** ou **Alerta** na lista de drop-down do Produto de **Trabalho,** tem a opção de criar itens de trabalho individuais para cada item de configuração.
    
     ![Screenshot que mostra a área do bilhete I T S M com incidente selecionado como um item de trabalho.](media/itsmc-overview/itsm-action-configuration.png)
    
     * Se selecionar os itens de trabalho individuais para cada caixa de **verificação de pontos de configuração,** cada item de configuração em cada alerta criará um novo item de trabalho. Como ocorrerão vários alertas para os mesmos itens de configuração afetados, haverá mais de um item de trabalho para cada item de configuração.

       Por exemplo, um alerta que tenha três itens de configuração criará três itens de trabalho. Um alerta que tenha um item de configuração criará um item de trabalho.
        
     * Se limpar os itens de trabalho individuais para cada caixa de **verificação de pontos de configuração,** o ITSMC criará um único item de trabalho para cada regra de alerta e anexará a todos os itens de configuração afetados. Será criado um novo item de trabalho se o anterior for fechado.

       >[!NOTE]
       > Neste caso, alguns dos alertas disparados não gerarão novos itens de trabalho na ferramenta ITSM.

       Por exemplo, um alerta que tenha três itens de configuração criará um item de trabalho. Se um alerta para a mesma regra de alerta que o exemplo anterior tiver um item de configuração, esse item de configuração será anexado à lista de itens de configuração afetados no item de trabalho criado. Um alerta para uma regra de alerta diferente que tenha um item de configuração criará um item de trabalho.

   * Se selecionou o **Evento** na lista de drop-down do Produto de **Trabalho,** pode optar por criar itens de trabalho individuais para cada entrada de registo ou para cada item de configuração.
    
     ![Screenshot que mostra a área do bilhete I T S M com Evento selecionado como um item de trabalho.](media/itsmc-overview/itsm-action-configuration-event.png)

     * Se selecionar **Criar itens de trabalho individuais para cada Entrada de Registo (o campo de item de configuração não está preenchido. Pode resultar num grande número de itens de trabalho.)**, será criado um item de trabalho para cada linha nos resultados de pesquisa da consulta de alerta de pesquisa de registo. A propriedade descrição na carga útil do item de trabalho conterá a linha a partir dos resultados da pesquisa.
      
     * Se selecionar **Criar itens de trabalho individuais para cada Item de Configuração,** cada item de configuração em cada alerta criará um novo item de trabalho. Cada item de configuração pode ter mais do que um item de trabalho no sistema ITSM. Esta opção é a mesma que a seleção da caixa de verificação que aparece depois de selecionar **Incidente** como o tipo de produto de trabalho.

10. Selecione **OK**.

Quando criar ou editar uma regra de alerta Azure, utilize um grupo de ação, que tenha uma ação ITSM. Quando o alerta é acionada, o item de trabalho é criado ou atualizado na ferramenta ITSM.

> [!NOTE]
> Para obter informações sobre o preço da ação ITSM, consulte a [página de preços dos](https://azure.microsoft.com/pricing/details/monitor/) grupos de ação.
>
> O campo de descrição curta na definição de regra de alerta é limitado a 40 caracteres quando o envia utilizando a ação ITSM.

## <a name="next-steps"></a>Passos seguintes

* [Problemas de resolução de problemas no ITSMC](./itsmc-resync-servicenow.md)
