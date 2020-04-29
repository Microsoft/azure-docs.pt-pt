---
title: O Monitor Azure para VMs (GA) frequentemente fez perguntas / Microsoft Docs
description: O Azure Monitor para VMs é uma solução no Azure que combina monitorização de saúde e desempenho do sistema operativo Azure VM, bem como descobrir automaticamente componentes e dependências de aplicações com outros recursos e mapeia a comunicação entre eles. Este artigo responde a perguntas comuns sobre a libertação da AG.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 3d250ef1aba979be04a44acaf31a3d685f162e37
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80283893"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Monitor Azure para VMs Geralmente disponíveis (GA) Perguntas frequentes
Esta FaQ de Disponibilidade Geral cobre as alterações que foram feitas no 4º trimestre de 2019 e no 1º trimestre de 2020, tal como nos preparámos para a GA.

## <a name="updates-for-azure-monitor-for-vms"></a>Atualizações para Monitor Azure para VMs
Lançámos uma nova versão do Azure Monitor para VMs em janeiro de 2020 antes do nosso anúncio da AG. Os clientes que permitem o Azure Monitor para VMs passarão a receber a versão GA, mas os clientes existentes que utilizem a versão do Monitor Azure para VMs a partir do 4º trimestre de 2019 e mais cedo serão solicitados a fazer upgrade. Esta FAQ oferece orientação para realizar uma atualização em escala se tiver grandes implementações em vários espaços de trabalho.


Com esta atualização, os dados de desempenho do Azure Monitor para VMs são armazenados na mesma tabela *InsightsMetrics* que o [Azure Monitor para contentores,](container-insights-overview.md)o que facilita a consulta dos dois conjuntos de dados. Além disso, é capaz de armazenar conjuntos de dados mais diversos que não poderíamos armazenar na tabela anteriormente utilizada. 

As nossas opiniões de desempenho estão agora a usar os dados que armazenamos na tabela *InsightsMetrics.*  Se ainda não tiver atualizado para utilizar a mais recente solução VMInsights no seu espaço de trabalho, os seus gráficos deixarão de exibir informações.  Pode fazer upgrade a partir da nossa página **Get Started,** conforme descrito abaixo.


## <a name="what-is-changing"></a>O que está a mudar?
Lançámos uma nova solução, chamada VMInsights, que inclui capacidades adicionais para a recolha de dados, juntamente com uma nova localização para armazenar estes dados no seu espaço de trabalho Log Analytics. 

No passado, permitimos a solução ServiceMap no seu espaço de trabalho e configuramos contadores de desempenho no seu espaço de trabalho Log Analytics para enviar os dados para a tabela *Perf.* Esta nova solução envia os dados para uma tabela chamada *InsightsMetrics* que também é usada pelo Azure Monitor para contentores. Este esquema de tabela permite-nos armazenar métricas adicionais e conjuntos de dados de serviço que não são compatíveis com o formato de tabela *Perf.*

Atualizámos os nossos gráficos de Desempenho para utilizar os dados que armazenamos na tabela *InsightsMetrics.* Pode fazer upgrade para utilizar a tabela *InsightsMetrics* a partir da nossa página **Get Started,** conforme descrito abaixo.


## <a name="how-do-i-upgrade"></a>Como faço upgrade?
Quando um espaço de trabalho de Log Analytics é atualizado para a versão mais recente do Monitor Azure para VMs, irá atualizar o agente de dependência em cada um dos VMs ligados a esse espaço de trabalho. Cada VM que necessite de upgrade será identificado no separador **Get Started** no Monitor Azure para VMs no portal Azure. Quando optar por atualizar um VM, irá atualizar o espaço de trabalho para esse VM juntamente com quaisquer outros VMs ligados a esse espaço de trabalho. Pode selecionar um único VM ou vários VMs, grupos de recursos ou subscrições. 

Utilize o seguinte comando para atualizar um espaço de trabalho utilizando o PowerShell:

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>O que devo fazer sobre os balcões de Desempenho no meu espaço de trabalho se eu instalar a solução VMInsights?

O método anterior de permitir o Monitor Azure para VMs utilizou contadores de desempenho no seu espaço de trabalho. A versão atual armazena `InsightsMetrics`estes dados numa tabela chamada . Pode optar por desativar estes contadores de desempenho no seu espaço de trabalho se já não precisar de os utilizar. 

>[!NOTE]
>Se tiver Regras de Alerta que `Perf` referenciam estes contadores na tabela, precisa atualizá-los para referência a novos dados armazenados na `InsightsMetrics` tabela. Consulte a nossa documentação, por exemplo, consultas de registo que pode utilizar que se referem a esta tabela.
>

Se decidir manter os contadores de desempenho ativados, será faturado pelos dados ingeridos e armazenados na `Perf` https://azure.microsoft.com/pricing/details/monitor/)tabela com base no preço [Log Analytics].

## <a name="how-will-this-change-affect-my-alert-rules"></a>Como é que esta mudança vai afetar as minhas regras de alerta?

Se criou [alertas](../platform/alerts-unified-log.md) de Registo `Perf` que consultam os contadores de desempenho da tabela que estavam ativados `InsightsMetrics` no espaço de trabalho, deve atualizar estas regras para se referir à tabela. Esta orientação também se aplica `ServiceMapComputer_CL` a `ServiceMapProcess_CL`quaisquer regras de pesquisa `VMComputer` `VMProcess` de registo suster e, porque esses conjuntos de dados estão se movendo para e tabelas.

Atualizaremos esta FAQ e a nossa documentação para incluir regras de alerta de pesquisa de registo de exemplo para os conjuntos de dados que recolhemos.

## <a name="how-will-this-affect-my-bill"></a>Como é que isto vai afetar a minha conta?

A faturação ainda é baseada em dados ingeridos e retidos no seu espaço de trabalho Log Analytics.

Os dados de desempenho ao nível da máquina que recolhemos são os `Perf` mesmos, têm um tamanho semelhante aos dados que armazenamos na tabela, e custarão aproximadamente a mesma quantidade.

## <a name="what-if-i-only-want-to-use-service-map"></a>E se eu só quiser usar o Mapa de Serviço?

Não faz mal. Você verá as solicitações no portal Azure ao visualizar o Monitor Azure para VMs sobre a próxima atualização. Uma vez lançado, receberá um pedido de solicitação para atualizar para a nova versão. Se preferir utilizar apenas a funcionalidade [Maps,](vminsights-maps.md) pode optar por não atualizar e continuar a utilizar a funcionalidade Maps no Monitor Azure para VMs e a solução Service Map acessada a partir do seu espaço de trabalho ou azulejo do painel de instrumentos.

Se optou por ativar manualmente os contadores de desempenho no seu espaço de trabalho, então poderá ver dados em algumas das nossas tabelas de desempenho visualizadas pelo Monitor Azure. Assim que a nova solução for lançada, atualizaremos as `InsightsMetrics` nossas tabelas de desempenho para consultar os dados armazenados na tabela. Se quiser ver dados dessa tabela nestes gráficos, terá de fazer upgrade para a nova versão do Monitor Azure para VMs.

As alterações para `ServiceMapComputer_CL` `ServiceMapProcess_CL` mover dados e afetarão tanto o Service Map como o Azure Monitor para VMs, pelo que ainda precisa de planear esta atualização.

Se optou por não fazer upgrade para a solução **VMInsights,** continuaremos a fornecer versões antigas dos nossos livros de desempenho que se referem aos dados na `Perf` tabela.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Os conjuntos de dados do Mapa de Serviços também serão armazenados na InsightsMetrics?

Os conjuntos de dados não serão duplicados se utilizar ambas as soluções. Ambas as ofertas partilham os conjuntos `VMComputer` de dados que `VMProcess` serão armazenados (anteriormente ServiceMapComputer_CL), (anteriormente ServiceMapProcess_CL), `VMConnection`e `VMBoundPort` tabelas para armazenar os conjuntos de dados do mapa que recolhemos.  

A `InsightsMetrics` tabela irá armazenar VM, conjuntos de dados de processo e serviço que recolhemos e só serão povoados se estiver a utilizar o Monitor Azure para VMs e a solução VM Insights. A solução Service Map não recolherá `InsightsMetrics` ou armazenará dados na tabela.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Serei duplamente cobrado se tiver as soluções do Mapa de Serviço e VMInsights no meu espaço de trabalho?

Não, as duas soluções partilham os conjuntos `VMComputer` de dados `VMProcess` do mapa que `VMConnection`armazenamos (anteriormente ServiceMapComputer_CL), (anteriormente ServiceMapProcess_CL), e `VMBoundPort`. Não será cobrado duas vezes por estes dados se tiver ambas as soluções no seu espaço de trabalho.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Se eu remover o Mapa de Serviço ou a solução VMInsights removerá os meus dados?

Não, as duas soluções partilham os conjuntos `VMComputer` de dados `VMProcess` do mapa que `VMConnection`armazenamos (anteriormente ServiceMapComputer_CL), (anteriormente ServiceMapProcess_CL), e `VMBoundPort`. Se remover uma das soluções, estes conjuntos de dados notam que ainda existe uma solução que utiliza os dados e que permanece no espaço de trabalho do Log Analytics. É necessário remover ambas as soluções do seu espaço de trabalho para que os dados sejam removidos do mesmo.

## <a name="health-feature-is-in-limited-public-preview"></a>A funcionalidade de saúde está em pré-visualização pública limitada

Recebemos um grande feedback dos clientes sobre o nosso conjunto de funcionalidades VM Health. Há muito interesse em torno desta funcionalidade e excitação sobre o seu potencial de monitorização de fluxos de trabalho. Estamos a planear fazer uma série de alterações para adicionar funcionalidade e abordar o feedback que recebemos. 

Para minimizar o impacto destas alterações para novos clientes, mudámos esta funcionalidade para uma **pré-visualização pública limitada.** Esta atualização aconteceu em outubro de 2019.

Planeamos relançar esta funcionalidade de Saúde em 2020, depois do Azure Monitor for VMs estar na GA.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Como é que os clientes existentes acedem à funcionalidade Saúde?

Os clientes existentes que estão a utilizar a funcionalidade Saúde continuarão a ter acesso à funcionalidade, mas não serão oferecidos a novos clientes.  

Para aceder à funcionalidade, pode adicionar `feature.vmhealth=true` a seguinte bandeira [https://portal.azure.com](https://portal.azure.com)de recurso ao URL do portal Azure . Exemplo. `https://portal.azure.com/?feature.vmhealth=true`

Também pode utilizar este url curto, que define [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)automaticamente a bandeira de recurso: .

Como cliente existente, pode continuar a utilizar a funcionalidade Saúde em VMs que estão ligados a uma configuração de espaço de trabalho existente com a funcionalidade de saúde.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Eu uso a VM Health agora com um ambiente e gostaria de implantá-lo para um novo

Se é um cliente existente que está a utilizar a funcionalidade Saúde e a vminsights@microsoft.com pretenda utilizá-la para um novo lançamento, contacte-nos para solicitar instruções.

## <a name="next-steps"></a>Passos seguintes

Para compreender os requisitos e métodos que o ajudam a monitorizar as suas máquinas virtuais, reveja o [Monitor De implantação de Dispositivos Azure para VMs](vminsights-enable-overview.md).
