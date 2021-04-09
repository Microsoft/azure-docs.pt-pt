---
title: Configure a recolha de dados para o agente Azure Monitor (pré-visualização)
description: Descreve como criar uma regra de recolha de dados para recolher dados de máquinas virtuais utilizando o agente Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 8943986bf8e8c082889d3a0b18618ac54c75e6d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022981"
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

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Limite a recolha de dados com consultas personalizadas de XPath
Uma vez que é cobrado por quaisquer dados recolhidos num espaço de trabalho do Log Analytics, deve recolher apenas os dados que necessita. Utilizando a configuração básica no portal Azure, apenas tem capacidade limitada de filtrar eventos para recolher. Para registos de aplicações e sistemas, este é todo o registo com uma gravidade particular. Para registos de segurança, isto é todo o sucesso da auditoria ou todos os registos de falha de auditoria.

Para especificar filtros adicionais, tem de utilizar a configuração personalizada e especificar um XPath que filtra os eventos que não tem. As entradas XPath estão escritas no formulário `LogName!XPathQuery` . Por exemplo, pode querer devolver apenas eventos do registo do evento Application com um ID de evento de 1035. O XPathQuery para estes eventos `*[System[EventID=1035]]` seria. Uma vez que você quer recuperar os eventos do registo de eventos da Aplicação, o XPath seria `Application!*[System[EventID=1035]]`

Consulte [as limitações do XPath 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations) para uma lista de limitações no XPath suportado pelo registo de eventos do Windows.

> [!TIP]
> Utilize o cmdlet PowerShell `Get-WinEvent` com o parâmetro para testar a `FilterXPath` validade de um XPathQuery. O seguinte guião mostra um exemplo.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Se os eventos forem devolvidos, a consulta é válida.
> - Se receber a mensagem *Não foram encontrados eventos que correspondam aos critérios de seleção especificados.*
> - Se receber a mensagem *A consulta especificada é inválida,* a sintaxe de consulta é inválida. 

A tabela que se segue mostra exemplos para eventos de filtragem utilizando um XPath personalizado.

| Description |  XPath |
|:---|:---|
| Colete apenas eventos do Sistema com ID de evento = 4648 |  `System!*[System[EventID=4648]]`
| Colete apenas eventos do Sistema com ID de evento = 4648 e um nome de processo de consent.exe | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| Recolha todos os eventos críticos, erros, avisos e informações do registo de eventos do Sistema, exceto para o ID do evento = 6 (Carregado de condutor) |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Colete todo o sucesso e insucesso Eventos de segurança, exceto para o Evento ID 4624 (logon de sucesso) |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>Criar regra e associação usando REST API

Siga os passos abaixo para criar uma regra de recolha de dados e associações utilizando a API REST.

1. Crie manualmente o ficheiro DCR utilizando o formato JSON mostrado no [Sample DCR](data-collection-rule-overview.md#sample-data-collection-rule).

2. Criar a regra utilizando a [API REST](/rest/api/monitor/datacollectionrules/create#examples).

3. Criar uma associação para cada máquina virtual à regra de recolha de dados utilizando a [API REST](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Criar associação usando o modelo de Gestor de Recursos

Não é possível criar uma regra de recolha de dados utilizando um modelo de Gestor de Recursos, mas pode criar uma associação entre uma máquina virtual Azure ou um servidor ativado pelo Azure Arc utilizando um modelo de Gestor de Recursos. Consulte [as amostras de modelo do Gestor de Recursos para as regras de recolha de dados no Azure Monitor](./resource-manager-data-collection-rules.md) para modelos de amostras.



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Agente monitor Azure.](azure-monitor-agent-overview.md)
- Saiba mais sobre [as regras de recolha de dados.](data-collection-rule-overview.md)
