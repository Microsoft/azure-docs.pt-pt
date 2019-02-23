---
title: Aumentar horizontalmente o Azure Analysis Services | Documentos da Microsoft
description: Replicar servidores do Azure Analysis Services com o Escalamento horizontal
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8f253d150a5073d2d19daf51c12180c9f7b3660b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734528"
---
# <a name="azure-analysis-services-scale-out"></a>Aumentar horizontalmente o Azure Analysis Services

Com o aumento horizontal, consultas de cliente podem ser distribuídas entre várias *as réplicas de consultas* num conjunto de consultas, reduzindo os tempos de resposta durante cargas de trabalho de consulta elevado. Também pode separar o processamento do conjunto de consultas, garantindo a consultas de cliente não são afetadas negativamente por operações de processamento. Escalamento horizontal pode ser configurado no portal do Azure ou com a API de REST do Analysis Services.

## <a name="how-it-works"></a>Como funciona

Numa implementação de servidor típico, um servidor serve como o servidor de processamento e o servidor de consulta. Se o número de consultas de cliente em relação a modelos no seu servidor excede o processamento de consulta unidades (QPU) para o plano do seu servidor, ou se ocorrer o processamento do modelo ao mesmo tempo que cargas de trabalho de consulta elevado, pode reduzir o desempenho. 

Com o aumento horizontal, pode criar um conjunto de consultas com até sete recursos de réplica de consulta adicionais (oito no total, incluindo o seu servidor). Pode dimensionar o número de réplicas de consulta para atender às demandas QPU em momentos críticos e pode separar um servidor de processamento do conjunto de consultas em qualquer altura. Todas as réplicas de consulta são criadas na mesma região que o seu servidor.

Independentemente do número de réplicas de consulta que ter num conjunto de consultas, o processamento de cargas de trabalho não são distribuídas entre réplicas de consulta. Um único servidor serve como o servidor de processamento. Réplicas de consulta servem apenas de consultas em modelos sincronizados entre cada réplica de consulta no agrupamento de consulta. 

Ao aumentar horizontalmente, as réplicas de consulta novos são adicionadas ao agrupamento de consulta incrementalmente. Pode demorar até cinco minutos para novos recursos de réplica de consulta a serem incluídos no agrupamento de consulta. Quando todas as réplicas de consulta nova estão operacionais e em execução, novas ligações de cliente são balanceada de carga em todos os recursos de agrupamento de consulta. Ligações de cliente existentes não são alteradas do recurso que estão atualmente ligados.  Quando o dimensionamento no, quaisquer ligações de cliente existentes para um recurso de conjunto de consulta que está a ser removido do conjunto de consultas estão terminadas. Eles são reconectados a um recurso de conjunto de consulta restantes quando a operação de dimensionamento for concluída, o que pode demorar até cinco minutos.

Durante o processamento de modelos, depois das operações de processamento estiverem concluídas, tem de ser efetuada uma sincronização entre o servidor de processamento e as réplicas de consulta. Ao automatizar as operações de processamento, é importante configurar uma operação de sincronização após a conclusão bem-sucedida de operações de processamento. Sincronização pode ser executada manualmente no portal ou utilizando o PowerShell ou a REST API. 

### <a name="separate-processing-from-query-pool"></a>Separar o processamento a partir do conjunto de consultas

Para um desempenho máximo para processamento e operações de consulta, pode optar por separar o servidor de processamento do conjunto de consultas. Quando separados, existentes e novas ligações de cliente são atribuídas para réplicas de consulta no agrupamento consulta apenas. Se as operações de processamento demorar apenas um curto período de tempo, pode optar por separar o servidor de processamento do conjunto de consultas para apenas a quantidade de tempo que demora a executar operações de processamento e a sincronização e, em seguida, inclua-o para o pool de consulta. 

> [!NOTE]
> Escalamento horizontal está disponível para os servidores no escalão de preço padrão. Cada réplica de consulta é cobrada à mesma taxa que o seu servidor.

> [!NOTE]
> Escalamento horizontal não aumenta a quantidade de memória disponível para o seu servidor. Para aumentar a memória, terá de atualizar o seu plano.

## <a name="region-limits"></a>Limites de região

O número de réplicas de consulta, que pode configurar está limitado por região do que seu servidor está em. Para obter mais informações, consulte [disponibilidade por região](analysis-services-overview.md#availability-by-region).

## <a name="monitor-qpu-usage"></a>Monitorizar a utilização QPU

 Para determinar se o Escalamento horizontal para o servidor é necessário, monitorize o seu servidor no portal do Azure ao uso de métricas. Se sua QPU regularmente maxes horizontalmente, significa que o número de consultas em relação a seus modelos está a exceder o limite QPU para o seu plano. Métrica de comprimento de fila de tarefa de conjunto de consulta também aumenta quando o número de consultas na fila de pool de threads a consulta excede QPU disponível. Para saber mais, consulte as [métricas do servidor de Monitorização](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurar o Escalamento horizontal

### <a name="in-azure-portal"></a>No portal do Azure

1. No portal, clique em **horizontal**. Utilize o controlo de deslize para selecionar o número de servidores de réplica de consulta. É o número de réplicas que escolher, além do servidor existente.

2. Na **separar o servidor de processamento do conjunto consulta**, selecione Sim para excluir o seu servidor de processamento de servidores de consulta. Ligações de cliente com a cadeia de ligação padrão (sem: rw) são redirecionadas para as réplicas no agrupamento de consulta. 

   ![Controlo de deslize de escalamento horizontal](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Clique em **guardar** para aprovisionar os novos servidores de réplica de consulta. 

Modelos em tabela no seu servidor primário são sincronizados com os servidores de réplica. Quando a sincronização estiver concluída, o conjunto de consultas começa a distribuir entradas consultas entre os servidores de réplica. 

## <a name="synchronization"></a>Sincronização 

Quando aprovisionar novos réplicas de consulta, o Azure Analysis Services replica automaticamente os seus modelos em todas as réplicas. Também pode efetuar uma sincronização manual com o portal ou a REST API. Quando processar seus modelos, deve efetuar uma sincronização para que as atualizações são sincronizadas entre as réplicas de consulta.

### <a name="in-azure-portal"></a>No portal do Azure

Na **descrição geral** > modelo > **sincronizar modelo**.

![Controlo de deslize de escalamento horizontal](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST

Utilize o **sincronização** operação.

#### <a name="synchronize-a-model"></a>Sincronizar um modelo   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Obter o estado de sincronização  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de utilizar o PowerShell, [instalar ou atualizar o módulo Azure PowerShell mais recente](/powershell/azure/install-az-ps). 

Para definir o número de réplicas de consulta, utilize [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o opcional `-ReadonlyReplicaCount` parâmetro.

Para executar a sincronização, utilize [sincronização AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

## <a name="connections"></a>Ligações

Na página de descrição geral do seu servidor, existem dois nomes de servidor. Se ainda não tiver configurado Escalamento horizontal para um servidor, ambos os nomes de servidor funcionam da mesma. Depois de configurar o Escalamento horizontal para um servidor, tem de especificar o nome de servidor adequado, consoante o tipo de ligação. 

Para ligações de cliente do utilizador final, como o Power BI Desktop, Excel e aplicações personalizadas, de utilização **nome do servidor**. 

Para o SSMS, SSDT e cadeias de ligação no PowerShell, aplicações de função do Azure e o AMO, utilizam **nome do servidor de gestão**. O nome do servidor de gestão inclui um especial `:rw` qualificador (leitura / escrita). Todas as operações de processamento ocorrerem no servidor de gestão.

![Nomes de servidor](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Resolução de problemas

**Problema:** Os utilizadores obtêm o erro **não é possível localizar o servidor '\<nome do servidor > "instância no modo de ligação"Só de leitura".**

**Solução:** Ao selecionar o **separar o servidor de processamento do conjunto consulta** opção, as ligações de cliente utilizando a cadeia de ligação padrão (sem: rw) são redirecionadas para réplicas de conjunto de consulta. Se as réplicas no agrupamento de consulta são ainda online porque a sincronização não tem ainda não foi concluídas, as ligações de cliente redirecionada podem falhar. Para impedir ligações falhadas, deve haver pelo menos dois servidores no agrupamento de consulta quando efetuar uma sincronização. Cada servidor está sincronizado individualmente, enquanto outros permanecem online. Se optar por não ter o servidor de processamento do conjunto de consulta durante o processamento, é possível removê-lo a partir do agrupamento para processamento, e, em seguida, adicioná-lo após a conclusão do processamento, mas antes da sincronização para o pool. Utilize métricas de memória e QPU para monitorizar o estado de sincronização.

## <a name="related-information"></a>Informações relacionadas

[Monitorizar as métricas do servidor](analysis-services-monitor.md)   
[Gerir o Azure Analysis Services](analysis-services-manage.md) 

