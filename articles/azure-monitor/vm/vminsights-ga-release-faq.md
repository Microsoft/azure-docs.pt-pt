---
title: Os conhecimentos de VM (GA) frequentemente fizeram perguntas | Microsoft Docs
description: A VM insights é uma solução no Azure que combina monitorização de saúde e desempenho do sistema operativo Azure VM, bem como descobrir automaticamente componentes de aplicação e dependências com outros recursos e mapear a comunicação entre eles. Este artigo responde a perguntas comuns sobre o lançamento da AG.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: fbef73bfe8058110277b200b8c4091fcde110c04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031883"
---
# <a name="vm-insights-generally-available-ga-frequently-asked-questions"></a>Informações VM Geralmente disponíveis (GA) Perguntas frequentes
Esta Disponibilidade Geral de FAQ abrange as alterações que foram feitas nos quartos de final de 2019 e 1º trimestre de 2020 como nos preparamos para a AG.

## <a name="updates-for-vm-insights"></a>Atualizações para insights VM
Lançámos uma nova versão de insights VM em janeiro de 2020 antes do nosso anúncio de GA. Os clientes que permitirem insights VM passarão a receber a versão GA, mas os clientes existentes que utilizam a versão de informações VM do 4º trimestre de 2019 e mais cedo serão solicitados a fazer o upgrade. Este FAQ oferece orientação para realizar um upgrade em escala se você tiver grandes implementações em vários espaços de trabalho.


Com esta atualização, os dados de desempenho dos insights VM são armazenados na mesma tabela *InsightsMetrics* que [os insights do Contentor](../containers/container-insights-overview.md), o que facilita a consulta dos dois conjuntos de dados. Além disso, é capaz de armazenar conjuntos de dados mais diversos que não conseguimos armazenar na tabela anteriormente utilizada. 

As nossas vistas de desempenho estão agora a utilizar os dados que armazenamos na tabela *InsightsMetrics.*  Se ainda não tiver atualizado para utilizar a mais recente solução VMInsights no seu espaço de trabalho, os seus gráficos deixarão de apresentar informações.  Pode atualizar a partir da nossa página **Get Started,** conforme descrito abaixo.


## <a name="what-is-changing"></a>O que está a mudar?
Lançámos uma nova solução, denominada VMInsights, que inclui mais capacidades para recolha de dados, juntamente com uma nova localização para armazenar estes dados no seu espaço de trabalho Log Analytics. 

No passado, viabilámos a solução ServiceMap no seu espaço de trabalho e balcões de desempenho de configuração no seu espaço de trabalho Log Analytics para enviar os dados para a tabela *Perf.* Esta nova solução envia os dados para uma tabela chamada *InsightsMetrics* que também é utilizada por insights de contentores. Este esquema de tabela permite-nos armazenar mais métricas e conjuntos de dados de serviço que não são compatíveis com o formato da tabela *Perf.*

Atualizámos os nossos gráficos de Desempenho para utilizar os dados que armazenamos na tabela *InsightsMetrics.* Pode atualizar para utilizar a tabela *InsightsMetrics* a partir da nossa página **Get Started,** conforme descrito abaixo.


## <a name="how-do-i-upgrade"></a>Como faço o upgrade?
Quando um espaço de trabalho do Log Analytics for atualizado para a versão mais recente do Azure Monitor para VMs, irá atualizar o agente de dependência de cada um dos VMs ligados a esse espaço de trabalho. Cada VM que necessitar de atualização será identificado no **separador Get Started** em insights VM no portal Azure. Quando optar por atualizar um VM, irá atualizar o espaço de trabalho para esse VM juntamente com quaisquer outros VM ligados a esse espaço de trabalho. Pode selecionar um único VM ou vários VMs, grupos de recursos ou subscrições. 

Utilize o seguinte comando para atualizar um espaço de trabalho utilizando o PowerShell:

```PowerShell
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>O que devo fazer sobre os balcões de desempenho no meu espaço de trabalho se instalar a solução VMInsights?

O método anterior de permitir insights VM usou contadores de desempenho no seu espaço de trabalho. A versão atual armazena estes dados numa tabela chamada `InsightsMetrics` . Pode optar por desativar estes contadores de desempenho no seu espaço de trabalho se já não precisar de os utilizar. 

>[!NOTE]
>Se tiver Regras de Alerta que refiram estes contadores na `Perf` tabela, tem de os atualizar para fazer referência aos novos dados armazenados na `InsightsMetrics` tabela. Consulte a nossa documentação, por exemplo, consultas de registo que pode utilizar que se referem a esta tabela.
>

Se decidir manter os contadores de desempenho ativados, será cobrado pelos dados ingeridos e armazenados na `Perf` tabela com base nos preços [Log Analytics]. https://azure.microsoft.com/pricing/details/monitor/)

## <a name="how-will-this-change-affect-my-alert-rules"></a>Como é que esta mudança vai afetar as minhas regras de alerta?

Se criou [alertas de Log](../alerts/alerts-unified-log.md) que consultam os `Perf` contadores de desempenho direcionados para a tabela que foram ativados no espaço de trabalho, deverá atualizar estas regras para se referir à `InsightsMetrics` tabela. Esta orientação também se aplica a quaisquer regras de pesquisa de registos que utilizem `ServiceMapComputer_CL` e , porque esses `ServiceMapProcess_CL` conjuntos de dados estão se movendo para `VMComputer` e `VMProcess` tabelas.

Atualizaremos este FAQ e a nossa documentação para incluir as regras de alerta de pesquisa de registo de exemplo para os conjuntos de dados que recolhemos.

## <a name="how-will-this-change-affect-my-bill"></a>Como é que esta mudança vai afetar a minha conta?

A faturação ainda é baseada em dados ingeridos e retidos no seu espaço de trabalho Log Analytics.

Os dados de desempenho de nível de máquina que recolhemos são os mesmos, são de tamanho semelhante aos dados armazenados na `Perf` tabela, e custarão aproximadamente a mesma quantidade.

## <a name="what-if-i-only-want-to-use-service-map"></a>E se eu só quiser usar o Mapa de Serviço?

Não faz mal. Verá as solicitações no portal Azure ao visualizar informações sobre a próxima atualização. Uma vez lançado, receberá um pedido de solicitação para atualizar para a nova versão. Se preferir utilizar apenas a funcionalidade [Maps,](vminsights-maps.md) pode optar por não fazer upgrade e continuar a utilizar a funcionalidade Maps em insights VM e a solução 'Mapa de Serviço' acedida a partir do seu espaço de trabalho ou azulejo do painel de instrumentos.

Se optar por ativar manualmente os contadores de desempenho no seu espaço de trabalho, poderá ver dados em alguns dos nossos gráficos de desempenho visualizados pelo Azure Monitor. Assim que a nova solução for lançada, atualizaremos os nossos gráficos de desempenho para consultar os dados armazenados na `InsightsMetrics` tabela. Se quiser ver dados dessa tabela nestes gráficos, terá de atualizar para a nova versão dos insights de VM.

As alterações para mover dados de e que afetarão tanto o Mapa de `ServiceMapComputer_CL` Serviço como os `ServiceMapProcess_CL` conhecimentos de VM, pelo que ainda precisa de planear esta atualização.

Se optar por não atualizar para a solução **VMInsights,** continuaremos a fornecer versões antigas dos nossos livros de desempenho que se referem aos dados na `Perf` tabela.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Os conjuntos de dados do Mapa de Serviço também serão armazenados na InsightsMetrics?

Os conjuntos de dados não serão duplicados se utilizar ambas as soluções. Ambas as ofertas partilham os conjuntos de dados que serão `VMComputer` armazenados (anteriormente ServiceMapComputer_CL), `VMProcess` (anteriormente ServiceMapProcess_CL), `VMConnection` e `VMBoundPort` tabelas para armazenar os conjuntos de dados do mapa que recolhemos.  

A `InsightsMetrics` tabela armazenará conjuntos de dados de VM, processo e serviço que recolhemos e só serão povoados se estiver a utilizar insights VM e a solução VM Insights. A solução Mapa de Serviço não recolherá nem armazenará dados na `InsightsMetrics` tabela.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Serei duplamente cobrado se tiver as soluções de Mapa de Serviço e VMInsights no meu espaço de trabalho?

Não, as duas soluções partilham os conjuntos de dados do mapa em que armazenamos `VMComputer` (anteriormente ServiceMapComputer_CL), `VMProcess` (anteriormente ServiceMapProcess_CL), `VMConnection` e `VMBoundPort` . Não será cobrado duas vezes por estes dados se tiver ambas as soluções no seu espaço de trabalho.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Se eu remover o Mapa de Serviço ou a solução VMInsights, irá remover os meus dados?

Não, as duas soluções partilham os conjuntos de dados do mapa em que armazenamos `VMComputer` (anteriormente ServiceMapComputer_CL), `VMProcess` (anteriormente ServiceMapProcess_CL), `VMConnection` e `VMBoundPort` . Se remover uma das soluções, estes conjuntos de dados notam que ainda existe uma solução que utiliza os dados e permanece no espaço de trabalho do Log Analytics. É necessário remover ambas as soluções do seu espaço de trabalho para que os dados sejam removidos do mesmo.

## <a name="health-feature-is-in-limited-public-preview"></a>A funcionalidade de saúde está em pré-visualização pública limitada

Recebemos um grande feedback dos clientes sobre o nosso conjunto de funcionalidades VM Health. Existe um interesse significativo em torno desta funcionalidade e excitação sobre o seu potencial de apoio aos fluxos de trabalho de monitorização. Estamos a planear fazer uma série de alterações para adicionar funcionalidade e abordar o feedback que recebemos. 

Para minimizar o impacto destas alterações em novos clientes, mudamos esta funcionalidade para uma **pré-visualização pública limitada.** Esta atualização aconteceu em outubro de 2019.

Planeamos relançar esta funcionalidade de Saúde em 2020, depois de os conhecimentos de VM estarem na AG.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Como é que os clientes existentes acedem à funcionalidade Saúde?

Os clientes existentes que estão a utilizar a funcionalidade Saúde continuarão a ter acesso à funcionalidade Saúde, mas não serão oferecidos a novos clientes.  

Para aceder à funcionalidade, pode adicionar a seguinte bandeira de recurso `feature.vmhealth=true` ao URL do portal Azure. [https://portal.azure.com](https://portal.azure.com) `https://portal.azure.com/?feature.vmhealth=true`Exemplo.

Também pode utilizar este url curto, que define automaticamente a bandeira da funcionalidade: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview) .

Como cliente existente, pode continuar a utilizar a funcionalidade Saúde em VMs que estão ligadas a uma configuração existente do espaço de trabalho com a funcionalidade de saúde.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Eu uso VM Health agora com um ambiente e gostaria de implantá-lo para um novo

Se é um cliente existente que está a utilizar a função Saúde e quer usá-la para um novo roll-out, contacte-nos vminsights@microsoft.com para solicitar instruções.

## <a name="next-steps"></a>Passos seguintes

Para compreender os requisitos e métodos que o ajudam a monitorizar as suas máquinas virtuais, [reveja os insights de Implementação de VM](./vminsights-enable-overview.md).
