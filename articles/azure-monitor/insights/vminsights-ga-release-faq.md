---
title: Perguntas frequentes sobre Azure Monitor para VMs (GA) | Microsoft Docs
description: Azure Monitor para VMs é uma solução no Azure que combina o monitoramento de integridade e desempenho do sistema operacional da VM do Azure, bem como a descoberta automática de componentes e dependências de aplicativos com outros recursos e mapeia a comunicação entre -los. Este artigo responde a perguntas comuns sobre a versão GA.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: ea7c695ddb92d441018503839b974c1f4bb33473
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047849"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Perguntas frequentes de Azure Monitor para VMs disponíveis (GA)

Estas perguntas frequentes sobre disponibilidade geral abordam as alterações que estão ocorrendo na Azure Monitor para VMs conforme preparamos para nossa versão de GA. 

## <a name="updates-for-azure-monitor-for-vms"></a>Atualizações para Azure Monitor para VMs

Lançámos uma nova versão do Monitor Azure para VMs. Os clientes que permitem o Azure Monitors para VMs passarão a receber a nova versão, mas os clientes existentes que já utilizam o Monitor Azure para VMs serão solicitados a fazer upgrade. Estas perguntas frequentes e nossa documentação oferecem orientação para executar uma atualização em escala se você tiver grandes implantações em vários espaços de trabalho.

Com esta atualização, os dados de desempenho do Azure Monitor para VMs são armazenados na mesma tabela *InsightsMetrics* que o [Azure Monitor para contentores,](container-insights-overview.md)o que facilita a consulta dos dois conjuntos de dados. Além disso, você pode armazenar conjuntos de dados mais diversificados que não pudemos armazenar na tabela usada anteriormente. 

Nas próximas semanas ou duas, as nossas opiniões de desempenho também serão atualizadas para usar esta nova tabela.

Percebemos que fazer com que os clientes existentes atualizem as causas de interrupção para o fluxo de trabalho, e é por isso que optamos por fazê-lo agora em visualização pública, em vez de depois depois do GA.


## <a name="what-is-changing"></a>O que está mudando?

Lançámos uma nova solução, chamada VMInsights, que inclui capacidades adicionais para a recolha de dados, juntamente com uma nova localização para armazenar estes dados no seu espaço de trabalho Log Analytics. 

No passado, permitimos a solução ServiceMap no seu espaço de trabalho e configuramos contadores de desempenho no seu espaço de trabalho Log Analytics para enviar os dados para a tabela *Perf.* Esta nova solução envia os dados para uma tabela chamada *InsightsMetrics* que também é usada pelo Azure Monitor para contentores. Este esquema de tabela permite-nos armazenar métricas adicionais e conjuntos de dados de serviço que não são compatíveis com o formato de tabela *Perf.*


## <a name="how-do-i-upgrade"></a>Como faço upgrade?
Quando um espaço de trabalho de Log Analytics é atualizado para a versão mais recente do Monitor Azure para VMs, irá atualizar o agente de dependência em cada um dos VMs ligados a esse espaço de trabalho. Cada VM que necessite de upgrade será identificado no separador **Get Started** no Monitor Azure para VMs no portal Azure. Quando optar por atualizar um VM, irá atualizar o espaço de trabalho para esse VM juntamente com quaisquer outros VMs ligados a esse espaço de trabalho. Pode selecionar um único VM ou vários VMs, grupos de recursos ou subscrições. 

Utilize o seguinte comando para atualizar um espaço de trabalho utilizando o PowerShell:

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>O que devo fazer sobre os contadores de desempenho em meu espaço de trabalho se eu instalar a solução VMInsights?

O método atual de habilitar Azure Monitor para VMs usa contadores de desempenho em seu espaço de trabalho. O novo método armazena estes dados numa nova tabela, chamada `InsightsMetrics`.

Assim que atualizarmos a nossa interface de utilizador para utilizar os dados na tabela `InsightsMetrics`, atualizaremos a nossa documentação e comunicaremos este anúncio através de vários canais, incluindo a exibição de um banner no portal Azure. Nessa altura, poderá optar por desativar estes contadores de [desempenho](vminsights-enable-overview.md#performance-counters-enabled) no seu espaço de trabalho se já não precisar de os utilizar. 

>[!NOTE]
>Se tiver Regras de Alerta que refiram estes contadores na tabela `Perf`, precisa atualizá-los para referência a novos dados armazenados na tabela `InsightsMetrics`. Consulte nossa documentação para obter exemplos de consultas de log que você pode usar para fazer referência a esta tabela.
>

Se decidir manter os contadores de desempenho ativados, será faturado pelos dados ingeridos e armazenados na tabela `Perf` com base no [Preço do Log Analytics[https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Como essa alteração afetará minhas regras de alerta?

Se criou [alertas](../platform/alerts-unified-log.md) de Registo que consultam os contadores de desempenho `Perf` que estavam ativados no espaço de trabalho, deve atualizar estas regras para se referir à tabela `InsightsMetrics`. Esta orientação aplica-se também a quaisquer regras de pesquisa de registos utilizando `ServiceMapComputer_CL` e `ServiceMapProcess_CL`, porque esses conjuntos de dados estão a mover-se para tabelas `VMComputer` e `VMProcess`.

Atualizaremos essas perguntas frequentes e nossa documentação para incluir regras de alerta de pesquisa de log de exemplo para os conjuntos de dados que coletamos.

## <a name="how-will-this-affect-my-bill"></a>Como isso afetará minha fatura?

A cobrança ainda se baseia nos dados ingeridos e retidos em seu espaço de trabalho Log Analytics.

Os dados de desempenho ao nível da máquina que recolhemos são os mesmos, têm um tamanho semelhante aos dados que armazenamos na tabela `Perf`, e custarão aproximadamente a mesma quantidade.

## <a name="what-if-i-only-want-to-use-service-map"></a>E se eu quiser usar apenas Mapa do Serviço?

Tudo bem. Você verá prompts na portal do Azure ao exibir Azure Monitor para VMs sobre a próxima atualização. Depois de liberado, você receberá uma solicitação solicitando que você atualize para a nova versão. Se preferir utilizar apenas a funcionalidade [Maps,](vminsights-maps.md) pode optar por não atualizar e continuar a utilizar a funcionalidade Maps no Monitor Azure para VMs e a solução Service Map acessada a partir do seu espaço de trabalho ou azulejo do painel de instrumentos.

Se você optar por habilitar manualmente os contadores de desempenho em seu espaço de trabalho, poderá ver os dados em alguns dos nossos gráficos de desempenho exibidos em Azure Monitor. Assim que a nova solução for lançada, atualizaremos os nossos gráficos de desempenho para consultar os dados armazenados na tabela `InsightsMetrics`. Se você quiser ver os dados dessa tabela nesses gráficos, será necessário atualizar para a nova versão do Azure Monitor para VMs.

As alterações para mover dados de `ServiceMapComputer_CL` e `ServiceMapProcess_CL` afetarão tanto o Service Map como o Azure Monitor para VMs, pelo que ainda precisa de planear esta atualização.

Se optou por não fazer upgrade para a solução **VMInsights,** continuaremos a fornecer versões antigas dos nossos livros de desempenho que se referem aos dados na tabela `Perf`.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Os conjuntos de dados de Mapa do Serviço também serão armazenados em InsightsMetrics?

Os conjuntos de dados não serão duplicados se você usar ambas as soluções. Ambas as ofertas partilham os conjuntos de dados que serão armazenados em `VMComputer` (anteriormente ServiceMapComputer_CL), `VMProcess` (anteriormente ServiceMapProcess_CL), `VMConnection`e `VMBoundPort` tabelas para armazenar os conjuntos de dados do mapa que recolhemos.  

A tabela `InsightsMetrics` armazenará VM, conjuntos de dados de processo e serviço que recolhemos e só serão preenchidos se estiver a utilizar o Monitor Azure para VMs e a solução VM Insights. A solução Service Map não recolherá ou armazenará dados na tabela `InsightsMetrics`.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Serei uma cobrança dupla se eu tiver as soluções Mapa do Serviço e VMInsights em meu espaço de trabalho?

Não, as duas soluções partilham os conjuntos de dados do mapa que armazenamos em `VMComputer` (anteriormente ServiceMapComputer_CL), `VMProcess` (anteriormente ServiceMapProcess_CL), `VMConnection`e `VMBoundPort`. Você não será cobrado duas vezes por esses dados se tiver as duas soluções em seu espaço de trabalho.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Se eu remover a solução Mapa do Serviço ou VMInsights, ela removerá meus dados?

Não, as duas soluções partilham os conjuntos de dados do mapa que armazenamos em `VMComputer` (anteriormente ServiceMapComputer_CL), `VMProcess` (anteriormente ServiceMapProcess_CL), `VMConnection`e `VMBoundPort`. Se você remover uma das soluções, esses conjuntos de dados perceberão que ainda há uma solução em vigor que usa os dados e ela permanece no espaço de trabalho Log Analytics. Você precisa remover ambas as soluções do seu espaço de trabalho para que os dados sejam removidos dela.

## <a name="when-will-this-update-be-released"></a>Quando esta atualização será lançada?

Esperamos lançar a atualização para Azure Monitor para VMs no início de janeiro de 2020. À medida que chegarmos mais perto da data de lançamento em Janeiro, publicaremos as atualizações aqui e apresentaremos notificações no portal do Azure quando você abrir Azure Monitor.

## <a name="health-feature-is-in-limited-public-preview"></a>O recurso de integridade está em visualização pública limitada

Recebemos muitos comentários incríveis de clientes sobre nosso conjunto de recursos de integridade da VM. Há muito interesse em torno desta funcionalidade e excitação sobre o seu potencial de monitorização de fluxos de trabalho. Estamos a planear fazer uma série de alterações para adicionar funcionalidade e abordar o feedback que recebemos. 

Para minimizar o impacto destas alterações para novos clientes, mudámos esta funcionalidade para uma **pré-visualização pública limitada.** Essa atualização ocorreu em outubro de 2019.

Planejamos reiniciar esse recurso de integridade no 2020, depois que Azure Monitor para VMs estiver em GA.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Como os clientes existentes acessam o recurso de integridade?

Os clientes existentes que estão usando o recurso de integridade continuarão a ter acesso a ele, mas não serão oferecidos a novos clientes.  

Para aceder à funcionalidade, pode adicionar a seguinte `feature.vmhealth=true` de bandeira de características ao portal Azure URL [https://portal.azure.com](https://portal.azure.com). Exemplo `https://portal.azure.com/?feature.vmhealth=true`.

Também pode utilizar este url curto, que define automaticamente a bandeira de recurso: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Como um cliente existente, você pode continuar a usar o recurso de integridade em VMs que estão conectadas a uma configuração de espaço de trabalho existente com a funcionalidade de integridade.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Eu uso a integridade da VM agora com um ambiente e gostaria de implantá-la em uma nova

Se é um cliente existente que está a utilizar a funcionalidade Saúde e a pretenda utilizá-la para um novo lançamento, contacte-nos em vminsights@microsoft.com para solicitar instruções.

## <a name="next-steps"></a>Passos seguintes

Para compreender os requisitos e métodos que o ajudam a monitorizar as suas máquinas virtuais, reveja o [Monitor De implantação de Dispositivos Azure para VMs](vminsights-enable-overview.md).
