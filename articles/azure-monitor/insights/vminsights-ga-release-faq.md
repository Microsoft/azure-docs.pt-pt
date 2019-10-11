---
title: Perguntas frequentes sobre Azure Monitor para VMs (GA) | Microsoft Docs
description: Azure Monitor para VMs é uma solução no Azure que combina o monitoramento de integridade e desempenho do sistema operacional da VM do Azure, bem como a descoberta automática de componentes e dependências de aplicativos com outros recursos e mapeia a comunicação entre -los. Este artigo responde a perguntas comuns sobre a versão GA.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/07/2019
ms.author: magoedte
ms.openlocfilehash: cb21d3bed1efc8f6ee7e16a0976ce46d03404983
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72275968"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Perguntas frequentes de Azure Monitor para VMs disponíveis (GA)

Anunciamos recentemente no blog [do Azure Update](https://azure.microsoft.com/blog/) algumas alterações planejadas que estarão ocorrendo em outubro e em novembro de 2019. Mais detalhes sobre essas alterações são abordados nestas perguntas frequentes sobre disponibilidade geral.

## <a name="updates-for-azure-monitor-for-vms"></a>Atualizações para Azure Monitor para VMs

Estamos lançando uma nova versão do Azure Monitor para VMs em novembro. Os clientes que habilitam os monitores do Azure para VMs após essa versão recebem automaticamente a nova versão, mas os clientes existentes que já estão usando Azure Monitor para VMs são solicitados a fazer a atualização.  Estas perguntas frequentes e nossa documentação oferecem orientação para executar uma atualização em massa se você tiver grandes implantações em vários espaços de trabalho.

Com essa atualização, os conjuntos de dados de desempenho Azure Monitor para VMs agora são armazenados na mesma tabela `InsightsMetrics` como [Azure monitor para contêineres](container-insights-overview.md)e facilitam a consulta dos dois conjuntos de dados. Além disso, você pode armazenar conjuntos de dados mais diversificados que não pudemos armazenar na tabela usada anteriormente.  Nossas exibições de desempenho também serão atualizadas para usar essa nova tabela.

Estamos migrando para novos tipos de dados para nossos conjuntos de dados de conexão. Os dados armazenados em `ServiceMapComputer_CL` e `ServiceMapProcess_CL`, que estão usando tabelas de log personalizadas, serão movidos para os tipos de dados dedicados chamados `VMComputer` e `VMProcess`.  Ao migrar para tipos de dados dedicados, podemos fornecer essas prioridades para a ingestão de dados e o esquema de tabela será padronizado entre todos os clientes.

Percebemos que fazer com que os clientes existentes atualizem as causas de interrupção para o fluxo de trabalho, e é por isso que optamos por fazê-lo agora em visualização pública, e não mais tarde, uma vez que chegamos em GA.

## <a name="what-will-change"></a>O que será alterado?

Atualmente, quando você conclui o processo de integração para Azure Monitor para VMs, você habilita a solução de Mapa do Serviço no espaço de trabalho selecionado para armazenar os dados de monitoramento e, em seguida, configura os contadores de desempenho para os dados que coletamos de suas VMs. Nas próximas semanas, lançaremos uma nova solução, chamada **VMInsights**, que incluirá recursos adicionais para coleta de dados junto com um novo local para armazenar esses dados em log Analytics.

Nosso processo atual de usar contadores de desempenho no espaço de trabalho envia os dados para a tabela de desempenho no Log Analytics.  Essa nova solução enviará os dados para uma tabela chamada `InsightsMetrics` que também é usada pelo Azure Monitor para contêineres. Esse esquema de tabela nos permite armazenar métricas adicionais e conjuntos de dados de serviço que não são compatíveis com o formato de tabela perf.

## <a name="what-should-i-do-about-the-performance-counters-on-my-workspace-if-i-install-the-vminsights-solution"></a>O que devo fazer sobre os contadores de desempenho no meu espaço de trabalho se eu instalar a solução VMInsights?

O método atual de habilitar Azure Monitor para VMs usa contadores de desempenho em seu espaço de trabalho. O novo método armazena esses dados em uma nova tabela, denominada `InsightsMetrics`.

Depois de atualizarmos nossa interface do usuário para usar os dados no InsightsMetrics, atualizaremos nossa documentação e comunicaremos esse anúncio por meio de vários canais, incluindo a exibição de uma faixa no portal do Azure. Nesse ponto, você pode optar por desabilitar esses [contadores de desempenho](vminsights-enable-overview.md#performance-counters-enabled) em seu espaço de trabalho se não quiser mais usá-los. 

[!NOTE]
>Se você tiver regras de alerta que referenciem esses contadores na tabela perf, precisará atualizá-los para se referir aos novos dados na tabela `InsightsMetrics`.  Consulte nossa documentação para obter exemplos de consultas de log que você pode usar para fazer referência a esta tabela.

Se decidir manter os contadores de desempenho habilitados, você será cobrado pelos dados ingeridos e mantidos na tabela de desempenho com base em [preços de Log Analytics [(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Como essa alteração afetará minhas regras de alerta?

Se você tiver criado [alertas de log](../platform/alerts-unified-log.md) que consultam a tabela `Perf` que direciona os contadores de desempenho que foram habilitados no espaço de trabalho, você deve atualizar essas regras para fazer referência à tabela `InsightsMetrics` em vez disso. Essa orientação também se aplica a qualquer regra de pesquisa de log usando `ServiceMapComputer_CL` e `ServiceMapProcess_CL`, pois esses conjuntos de dados estão mudando para tabelas `VMComputer` e `VMProcess`.

Atualizaremos essas perguntas frequentes e nossa documentação para incluir regras de alerta de pesquisa de log de exemplo para os conjuntos de dados que coletamos.

## <a name="will-there-be-any-changes-to-billing"></a>Haverá qualquer alteração na cobrança?

A cobrança ainda é baseada em dados ingeridos e mantidos em seu espaço de trabalho de Log Analytics.

Os dados de desempenho no nível da máquina que coletamos são os mesmos, é de um tamanho semelhante aos dados que armazenamos na tabela perf e custarão aproximadamente o mesmo valor.

## <a name="what-if-i-only-want-to-use-service-map"></a>E se eu quiser usar apenas Mapa do Serviço?

Tudo bem.  Você verá prompts na portal do Azure ao exibir Azure Monitor para VMs sobre a próxima atualização. Depois de liberado, você receberá uma solicitação solicitando que você atualize para a nova versão. Se preferir usar apenas o recurso [Maps](vminsights-maps.md) , você poderá optar por não atualizar e continuar a usar o recurso Maps Azure monitor para VMs e a solução mapa do serviço acessada do seu espaço de trabalho ou bloco do Dashboard.

Se você optar por habilitar manualmente os contadores de desempenho em seu espaço de trabalho, poderá ver os dados em alguns dos nossos gráficos de desempenho exibidos em Azure Monitor. Depois que a nova solução for lançada, atualizaremos nossos gráficos de desempenho para consultar os dados armazenados na tabela `InsightsMetrics`. Se você quiser ver os dados dessa tabela nesses gráficos, será necessário atualizar para a nova versão do Azure Monitor para VMs.

As alterações para mover dados de `ServiceMapComputer_CL` e `ServiceMapProcess_CL` afetarão Mapa do Serviço e Azure Monitor para VMs, para que você ainda precise planejar essa atualização.

Se você optar por não atualizar para a solução **VMInsights** , continuaremos a fornecer versões herdadas de nossas pastas de trabalho de desempenho que se referem aos dados na tabela `Perf`.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Os conjuntos de dados de Mapa do Serviço também serão armazenados em InsightsMetrics?

Os conjuntos de dados não serão duplicados se você usar ambas as soluções. As duas ofertas compartilham os conjuntos de dados que serão armazenados nas tabelas `VMComputer` (anteriormente ServiceMapComputer_CL), `VMProcess` (anteriormente ServiceMapProcess_CL), `VMConnection` e `VMBoundPort` para armazenar os conjuntos de dados do mapa que coletamos.  

A tabela `InsightsMetrics` será usada para armazenar conjuntos de dados de VM, processos e serviços que coletamos e só serão populadas se você estiver usando Azure Monitor para VMs.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-on-my-workspace"></a>Serei uma cobrança dupla se eu tiver as soluções Mapa do Serviço e VMInsights no meu espaço de trabalho?

Não, as duas soluções compartilham os conjuntos de dados do mapa que armazenamos em `VMComputer` (anteriormente ServiceMapComputer_CL), `VMProcess` (anteriormente ServiceMapProcess_CL), `VMConnection` e `VMBoundPort`.  Você não será cobrado duas vezes por esses dados se tiver as duas soluções em seu espaço de trabalho.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data-in-log-analytics"></a>Se eu remover a solução Mapa do Serviço ou VMInsights, ela removerá meus dados no Log Analytics?

Não, as duas soluções compartilham os conjuntos de dados do mapa que armazenamos em `VMComputer` (anteriormente ServiceMapComputer_CL), `VMProcess` (anteriormente ServiceMapProcess_CL), `VMConnection` e `VMBoundPort`.  Se você remover uma das soluções, esses conjuntos de dados perceberão que ainda há uma solução em vigor que usa os dados e ela permanece em Log Analytics.  Você precisa remover as duas soluções do espaço de trabalho para que os dados sejam removidos do seu espaço de trabalho do Log Analytics.

## <a name="when-will-this-update-be-released"></a>Quando esta atualização será lançada?

Esperamos lançar a atualização para Azure Monitor para VMs em meados de novembro. À medida que chegarmos mais perto da data de lançamento, publicaremos as atualizações aqui e apresentaremos notificações no portal do Azure quando você navegar até Azure Monitor.

## <a name="health-feature-to-enter-limited-public-preview"></a>Recurso de integridade para entrar na visualização pública limitada

Recebemos muitos comentários incríveis de clientes sobre nosso conjunto de recursos de integridade da VM.  Há muito interesse em relação a esse recurso e empolga o seu potencial para dar suporte a fluxos de trabalho de monitoramento. Estamos planejando fazer uma série de alterações para adicionar funcionalidade e abordar os comentários que recebemos. Para minimizar o impacto dessas alterações em novos clientes, estamos movendo esse recurso para uma visualização pública limitada.

Essa transição será iniciada no início de outubro e deverá ser concluída até o final do mês.

Algumas das áreas com as quais iremos nos concentrar:

- Mais controle sobre o modelo de integridade e seus limites
- Criação em modelos de integridade de escala que se aplicam a um escopo de VMs
- Uso nativo da plataforma de alertas para gerenciar regras de alerta baseadas em integridade
- Capacidade de ver a integridade em um escopo maior, como uma ou mais assinaturas
- Latência reduzida em transições de integridade e notificações de alerta

## <a name="how-do-existing-customers-access-the-health-feature"></a>Como os clientes existentes acessam o recurso de integridade?

Os clientes existentes que estão usando o recurso de integridade continuarão a ter acesso a ele, mas não serão oferecidos a novos clientes.  

Para acessar o recurso, você pode adicionar o sinalizador de recurso a seguir `feature.vmhealth=true` à URL do portal [https://portal.azure.com](https://portal.azure.com). Exemplo `https://portal.azure.com/?feature.vmhealth=true`.

Você também pode usar essa URL curta, que define o sinalizador de recurso automaticamente: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Como um cliente existente, você pode continuar a usar o recurso de integridade em VMs que estão conectadas a uma configuração de espaço de trabalho existente com a funcionalidade de integridade.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-for-a-new-environment"></a>Eu uso a integridade da VM agora com um ambiente e gostaria de implantá-la para um novo ambiente

Se você for um cliente existente que está usando o recurso de integridade e deseja usá-lo para uma nova distribuição, entre em contato conosco em vminsights@microsoft.com para solicitar instruções.

## <a name="next-steps"></a>Passos seguintes

Para entender os requisitos e métodos que ajudam a monitorar suas máquinas virtuais, examine [implantar Azure monitor para VMs](vminsights-enable-overview.md).
