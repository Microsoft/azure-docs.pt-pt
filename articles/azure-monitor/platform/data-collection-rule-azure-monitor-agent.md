---
title: Configure a recolha de dados para o agente Azure Monitor (pré-visualização)
description: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: 3cd2ed692f3a34223675da69efd92e78c2ba9504
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083193"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Configure a recolha de dados para o agente Azure Monitor (pré-visualização)
As Regras de Recolha de Dados (DCR) definem os dados que entram no Azure Monitor e especificam para onde devem ser enviados. Este artigo descreve como criar uma regra de recolha de dados para recolher dados de máquinas virtuais usando o agente Azure Monitor.

Para obter uma descrição completa das regras de recolha de dados, consulte [as regras de recolha de dados no Azure Monitor (pré-visualização)](data-collection-rule-overview.md).

> [!NOTE]
> Este artigo descreve como configurar dados para máquinas virtuais com o agente Azure Monitor que está atualmente em pré-visualização. Consulte [a visão geral dos agentes do Azure Monitor](agents-overview.md) para obter uma descrição dos agentes que estão geralmente disponíveis e como usá-los para recolher dados.


## <a name="dcr-associations"></a>Associações DCR
Para aplicar um DCR a uma máquina virtual, cria-se uma associação para a máquina virtual. Uma máquina virtual pode ter uma associação a vários DCRs, e um DCR pode ter várias máquinas virtuais associadas a ela. Isto permite-lhe definir um conjunto de DCRs, cada um correspondendo a um requisito específico, e aplicá-los apenas nas máquinas virtuais onde se aplicam. 

Por exemplo, considere um ambiente com um conjunto de máquinas virtuais executando uma linha de aplicação de negócio e outras executando SQL Server. Pode ter uma regra de recolha de dados padrão que se aplica a todas as máquinas virtuais e regras separadas de recolha de dados que recolhem dados especificamente para a linha de aplicação de negócios e para o SQL Server. As associações das máquinas virtuais às regras de recolha de dados seriam semelhantes ao diagrama seguinte.

![Associações](media/data-collection-rule-azure-monitor-agent/associations.png)

## <a name="create-using-the-azure-portal"></a>Criar com o portal do Azure
Pode utilizar o portal Azure para criar uma regra de recolha de dados e associar máquinas virtuais na sua subscrição a essa regra. O agente Azure Monitor será instalado automaticamente e uma identidade gerida criada para quaisquer máquinas virtuais que ainda não o tenham instalado.

No menu **Azure Monitor** no portal Azure, selecione Regras de Recolha de **Dados** a partir da secção **Definições.** Clique **em Adicionar** para adicionar uma nova Regra de Recolha de Dados e atribuição.

[![Regras de recolha de dados](media/azure-monitor-agent/data-collection-rules.png)](media/azure-monitor-agent/data-collection-rules.png#lightbox)

Clique **em Adicionar** para criar uma nova regra e conjunto de associações. Forneça um **nome de regra** e especifique um Grupo de **Subscrição** e **Recursos.** Isto especifica onde o DCR será criado. As máquinas virtuais e as suas associações podem estar em qualquer grupo de subscrição ou recursos no arrendatário.

[![Básicos básicos da regra de recolha de dados](media/azure-monitor-agent/data-collection-rule-basics.png)](media/azure-monitor-agent/data-collection-rule-basics.png#lightbox)

No separador **máquinas Virtuais,** adicione máquinas virtuais que devem ter a Regra de Recolha de Dados aplicada. O Agente Monitor Azure será instalado em máquinas virtuais que ainda não a tenham instalada.

[![Máquinas virtuais da Regra de Recolha de Dados](media/azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

No **separador Recolher e entregar,** clique em **Adicionar fonte de dados** para adicionar uma fonte de dados e um conjunto de destino. Selecione um **tipo de fonte de dados**, e os detalhes correspondentes a selecionar serão exibidos. Para contadores de desempenho, pode selecionar a partir de um conjunto de objetos predefinidos e da sua taxa de amostragem. Para eventos, pode selecionar a partir de um conjunto de registos ou instalações e o nível de gravidade. 

[![Fonte de dados básica](media/azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Para especificar outros registos e contadores de desempenho, selecione **Custom**. Em seguida, pode especificar um [XPath](https://www.w3schools.com/xml/xpath_syntax.asp) para quaisquer valores específicos a recolher. Consulte [o SAMPLE DCR](data-collection-rule-overview.md#sample-data-collection-rule) por exemplos.

[![Costume de fonte de dados](media/azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

No **separador Destino,** adicione um ou mais destinos para a fonte de dados. O evento Windows e as fontes de dados Syslog só podem enviar para registos do Monitor Azure. Os contadores de desempenho podem enviar tanto para as Métricas do Monitor Azure como para os Registos do Monitor Azure.

[![Destino](media/azure-monitor-agent/data-collection-rule-destination.png)](media/azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Clique **em Adicionar Fonte de Dados** e, em seguida, **Reveja + crie** para rever os detalhes da regra de recolha de dados e associação com o conjunto de VMs. Clique em **Criar** para criá-lo.

> [!NOTE]
> Uma vez criada a regra de recolha de dados e as associações, pode demorar até 5 minutos para que os dados sejam enviados para os destinos.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Agente monitor Azure.](azure-monitor-agent-overview.md)
- Saiba mais sobre [as regras de recolha de dados.](data-collection-rule-overview.md)
