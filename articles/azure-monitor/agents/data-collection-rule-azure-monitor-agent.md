---
title: Configure a recolha de dados para o agente Azure Monitor (pré-visualização)
description: Descreve como criar uma regra de recolha de dados para recolher dados de máquinas virtuais utilizando o agente Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: 6e65b828e2cf86614707c8a859abc0c4f2395899
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616362"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Configure a recolha de dados para o agente Azure Monitor (pré-visualização)

As Regras de Recolha de Dados (DCR) definem os dados que entram no Azure Monitor e especificam para onde devem ser enviados. Este artigo descreve como criar uma regra de recolha de dados para recolher dados de máquinas virtuais usando o agente Azure Monitor.

Para obter uma descrição completa das regras de recolha de dados, consulte [as regras de recolha de dados no Azure Monitor (pré-visualização)](data-collection-rule-overview.md).

> [!NOTE]
> Este artigo descreve como configurar dados para máquinas virtuais com o agente Azure Monitor que está atualmente em pré-visualização. Consulte [a visão geral dos agentes do Azure Monitor](agents-overview.md) para obter uma descrição dos agentes que estão geralmente disponíveis e como usá-los para recolher dados.

## <a name="data-collection-rule-associations"></a>Associações de regras de recolha de dados

Para aplicar um DCR a uma máquina virtual, cria-se uma associação para a máquina virtual. Uma máquina virtual pode ter uma associação a vários DCRs, e um DCR pode ter várias máquinas virtuais associadas a ela. Isto permite-lhe definir um conjunto de DCRs, cada um correspondendo a um requisito específico, e aplicá-los apenas nas máquinas virtuais onde se aplicam. 

Por exemplo, considere um ambiente com um conjunto de máquinas virtuais executando uma linha de aplicação de negócio e outras executando SQL Server. Pode ter uma regra de recolha de dados padrão que se aplica a todas as máquinas virtuais e regras separadas de recolha de dados que recolhem dados especificamente para a linha de aplicação de negócios e para o SQL Server. As associações das máquinas virtuais às regras de recolha de dados seriam semelhantes ao diagrama seguinte.

![O diagrama mostra máquinas virtuais que hospedam linha de aplicação de negócios e SQL Server associados às regras de recolha de dados denominadas central-i t-default e lob-app para linha de aplicação de negócios e central-i t-default e s q l para SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Criar regra e associação no portal Azure

Pode utilizar o portal Azure para criar uma regra de recolha de dados e associar máquinas virtuais na sua subscrição a essa regra. O agente Azure Monitor será instalado automaticamente e uma identidade gerida criada para quaisquer máquinas virtuais que ainda não o tenham instalado.

> [!IMPORTANT]
> Existe atualmente um problema conhecido em que se a regra de recolha de dados criar uma identidade gerida numa máquina virtual que já tem uma identidade gerida atribuída pelo utilizador, a identidade atribuída ao utilizador é desativada.

No menu **Azure Monitor** no portal Azure, selecione Regras de Recolha de **Dados** a partir da secção **Definições.** Clique **em Adicionar** para adicionar uma nova Regra de Recolha de Dados e atribuição.

[![Regras de recolha de dados](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Clique **em Adicionar** para criar uma nova regra e conjunto de associações. Forneça um **nome de regra** e especifique um Grupo de **Subscrição** e **Recursos.** Isto especifica onde o DCR será criado. As máquinas virtuais e as suas associações podem estar em qualquer grupo de subscrição ou recursos no arrendatário.

[![Básicos básicos da regra de recolha de dados](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

No separador **máquinas Virtuais,** adicione máquinas virtuais que devem ter a Regra de Recolha de Dados aplicada. Tanto as máquinas virtuais Azure como os servidores ativados do Azure Arc no seu ambiente devem ser listados. O Agente Monitor Azure será instalado em máquinas virtuais que ainda não a tenham instalada.

[![Máquinas virtuais da Regra de Recolha de Dados](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

No **separador Recolher e entregar,** clique em **Adicionar fonte de dados** para adicionar uma fonte de dados e um conjunto de destino. Selecione um **tipo de fonte de dados**, e os detalhes correspondentes a selecionar serão exibidos. Para contadores de desempenho, pode selecionar a partir de um conjunto de objetos predefinidos e da sua taxa de amostragem. Para eventos, pode selecionar a partir de um conjunto de registos ou instalações e o nível de gravidade. 

[![Fonte de dados básica](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Para especificar outros registos e contadores de desempenho a partir das fontes de [dados atualmente suportadas](azure-monitor-agent-overview.md#data-sources-and-destinations) ou para filtrar eventos usando consultas XPath, selecione **Custom**. Em seguida, pode especificar um [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) para quaisquer valores específicos a recolher. Consulte [o SAMPLE DCR](data-collection-rule-overview.md#sample-data-collection-rule) por exemplos.

[![Costume de fonte de dados](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

No **separador Destino,** adicione um ou mais destinos para a fonte de dados. O evento Windows e as fontes de dados Syslog só podem enviar para registos do Monitor Azure. Os contadores de desempenho podem enviar tanto para as Métricas do Monitor Azure como para os Registos do Monitor Azure.

[![Destino](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Clique **em Adicionar Fonte de Dados** e, em seguida, **Reveja + crie** para rever os detalhes da regra de recolha de dados e associação com o conjunto de VMs. Clique em **Criar** para criá-lo.

> [!NOTE]
> Após a criação da regra de recolha de dados e das associações, pode demorar até 5 minutos para que os dados sejam enviados para os destinos.


## <a name="create-rule-and-association-using-rest-api"></a>Criar regra e associação usando REST API

Siga os passos abaixo para criar uma regra de recolha de dados e associações utilizando a API REST.

1. Crie manualmente o ficheiro DCR utilizando o formato JSON mostrado no [Sample DCR](data-collection-rule-overview.md#sample-data-collection-rule).

2. Criar a regra utilizando a [API REST](/rest/api/monitor/datacollectionrules/create#examples).

3. Criar uma associação para cada máquina virtual à regra de recolha de dados utilizando a [API REST](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Criar associação usando o modelo de Gestor de Recursos

Não é possível criar uma regra de recolha de dados utilizando um modelo de Gestor de Recursos, mas pode criar uma associação entre uma máquina virtual Azure ou um servidor ativado pelo Azure Arc utilizando um modelo de Gestor de Recursos. Consulte [as amostras de modelo do Gestor de Recursos para as regras de recolha de dados no Azure Monitor](../samples/resource-manager-data-collection-rules.md) para modelos de amostras.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Agente monitor Azure.](azure-monitor-agent-overview.md)
- Saiba mais sobre [as regras de recolha de dados.](data-collection-rule-overview.md)
