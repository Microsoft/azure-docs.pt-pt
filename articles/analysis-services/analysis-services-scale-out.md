---
title: Aumentar horizontalmente o Azure Analysis Services | Documentos da Microsoft
description: Replicar servidores do Azure Analysis Services com o Escalamento horizontal
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: eae1569cf6f7ada89f64b96fe81b154b84932a12
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182851"
---
# <a name="azure-analysis-services-scale-out"></a>Aumentar horizontalmente o Azure Analysis Services

Com o aumento horizontal, consultas de cliente podem ser distribuídas entre várias *as réplicas de consultas* num *consultar o conjunto*, reduzindo os tempos de resposta durante cargas de trabalho de consulta elevado. Também pode separar o processamento do conjunto de consultas, garantindo a consultas de cliente não são afetadas negativamente por operações de processamento. Escalamento horizontal pode ser configurado no portal do Azure ou com a API de REST do Analysis Services.

Escalamento horizontal está disponível para os servidores no escalão de preço padrão. Cada réplica de consulta é cobrada à mesma taxa que o seu servidor. Todas as réplicas de consulta são criadas na mesma região que o seu servidor. O número de réplicas de consulta, que pode configurar está limitado por região do que seu servidor está em. Para obter mais informações, consulte [disponibilidade por região](analysis-services-overview.md#availability-by-region). Escalamento horizontal não aumenta a quantidade de memória disponível para o seu servidor. Para aumentar a memória, terá de atualizar o seu plano. 

## <a name="why-scale-out"></a>Por que motivo horizontal?

Numa implementação de servidor típico, um servidor serve como o servidor de processamento e o servidor de consulta. Se o número de consultas de cliente em relação a modelos no seu servidor excede o processamento de consulta unidades (QPU) para o plano do seu servidor, ou se ocorrer o processamento do modelo ao mesmo tempo que cargas de trabalho de consulta elevado, pode reduzir o desempenho. 

Com o aumento horizontal, pode criar um conjunto de consultas com até sete recursos de réplica de consulta adicionais (oito total, incluindo seus *primário* server). Pode dimensionar o número de réplicas no agrupamento de consulta para atender às demandas QPU em momentos críticos, e pode separar um servidor de processamento do conjunto de consultas em qualquer altura. 

Independentemente do número de réplicas de consulta que ter num conjunto de consultas, o processamento de cargas de trabalho não são distribuídas entre réplicas de consulta. O servidor principal serve como o servidor de processamento. Réplicas de consulta servem apenas consultas as bancos de dados do modelo sincronizados entre o servidor principal e todas as réplicas no agrupamento de consulta. 

Ao aumentar horizontalmente, pode demorar até cinco minutos para réplicas de consulta nova incrementalmente sejam adicionados ao agrupamento de consulta. Quando todas as réplicas de consulta nova estão operacionais e em execução, novas ligações de cliente são balanceada de carga em recursos no conjunto de consultas. Ligações de cliente existentes não são alteradas do recurso que estão atualmente ligados. Quando o dimensionamento no, quaisquer ligações de cliente existentes para um recurso de conjunto de consulta que está a ser removido do conjunto de consultas estão terminadas. Os clientes podem voltar a ligar a um recurso de conjunto de consulta restantes.

## <a name="how-it-works"></a>Como funciona

Ao configurar o Escalamento horizontal na primeira vez, bases de dados de modelo no seu servidor primário são *automaticamente* sincronizado com novo réplicas num novo conjunto de consultas. Sincronização automática ocorre apenas uma vez. Durante a sincronização automática, os ficheiros de dados do servidor primário (encriptados em inatividade no armazenamento de BLOBs) são copiados para uma segunda localização, também são encriptada em inatividade no armazenamento de Blobs. As réplicas no agrupamento de consulta são então *alimentado* com dados de que o segundo conjunto de ficheiros. 

Enquanto uma sincronização automática é executada apenas quando horizontal um servidor pela primeira vez, também pode executar uma sincronização manual. Sincronizar assegura que os dados em réplicas no agrupamento de consulta corresponde do servidor primário. Durante o processamento de modelos (atualização) no servidor primário, tem de ser efetuada uma sincronização *depois de* as operações de processamento são concluídas. Esta sincronização copia dados atualizados de ficheiros do servidor primário no armazenamento de BLOBs para o segundo conjunto de ficheiros. As réplicas no agrupamento de consulta, em seguida, estão a ser alimentadas com dados atualizados do segundo conjunto de ficheiros no armazenamento de Blobs. 

Ao realizar uma operação de escalamento horizontal subsequente, por exemplo, aumento do número de réplicas no agrupamento de consulta de duas a cinco, as réplicas novos são alimentadas com dados de que o segundo conjunto de ficheiros no armazenamento de Blobs. Não há nenhuma sincronização. Se fosse, em seguida, executar uma sincronização depois de aumentar horizontalmente, as réplicas de novo no agrupamento de consulta seria alimentado duas vezes - uma com redundância de hidratação. Ao realizar uma operação de aumento horizontal subseqüente, é importante ter em mente:

* Efetuar uma sincronização *antes da operação de aumento horizontal* para evitar hidratação redundante das réplicas foi adicionadas.

* Ao automatizar os dois processamento *e* operações de escalamento horizontal, é importante primeiro processam dados no servidor primário e, em seguida, efetuar uma sincronização e, em seguida, efetuar a operação de escalamento horizontal. Esta sequência garante um impacto mínimo nos recursos QPU e memória.

* A sincronização for permitida, mesmo quando não há nenhum réplicas no agrupamento de consulta. Se aumentar horizontalmente de zero a uma ou mais réplicas com novos dados de uma operação de processamento no servidor primário, execute a sincronização pela primeira vez com nenhuma réplicas no agrupamento de consulta e, em seguida, aumentar horizontalmente. Sincronizar antes de aumentar horizontalmente evita hidratação redundante das réplicas recém-adicionada.

* Quando eliminar uma base de dados do modelo do servidor primário, ele não automaticamente é eliminado do réplicas no agrupamento de consulta. Tem de efetuar uma operação de sincronização que remove o ficheiro/s para essa base de dados de localização de armazenamento de BLOBs partilhado a réplica e, em seguida, elimina a base de dados do modelo nas réplicas no agrupamento de consulta.

### <a name="separate-processing-from-query-pool"></a>Separar o processamento a partir do conjunto de consultas

Para um desempenho máximo para processamento e operações de consulta, pode optar por separar o servidor de processamento do conjunto de consultas. Quando separados, existentes e novas ligações de cliente são atribuídas para réplicas de consulta no agrupamento consulta apenas. Se as operações de processamento demorar apenas um curto período de tempo, pode optar por separar o servidor de processamento do conjunto de consultas para apenas a quantidade de tempo que demora a executar operações de processamento e a sincronização e, em seguida, inclua-o para o pool de consulta. 

## <a name="monitor-qpu-usage"></a>Monitorizar a utilização QPU

Para determinar se o Escalamento horizontal para o servidor é necessário, monitorize o seu servidor no portal do Azure ao uso de métricas. Se sua QPU regularmente maxes horizontalmente, significa que o número de consultas em relação a seus modelos está a exceder o limite QPU para o seu plano. Métrica de comprimento de fila de tarefa de conjunto de consulta também aumenta quando o número de consultas na fila de pool de threads a consulta excede QPU disponível. 

Outra boa métrica para ver é uma QPU média por ServerResourceType. Esta métrica compara QPU médio para o servidor primário com que o conjunto de consultas. 

### <a name="to-configure-qpu-by-serverresourcetype"></a>Para configurar QPU por ServerResourceType
1. Num gráfico de linhas de métricas, clique em **adicionar métrica**. 
2. Na **RESOURCE**, selecione o seu servidor, em seguida, no **espaço de nomes de MÉTRICA**, selecione **métricas padrão do Analysis Services**, em seguida, em **MÉTRICA**, Selecione **QPU**e, em seguida, no **agregação**, selecione **média**. 
3. Clique em **aplicam-se de que a divisão**. 
4. Na **valores**, selecione **ServerResourceType**.  

Para saber mais, consulte as [métricas do servidor de Monitorização](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurar o Escalamento horizontal

### <a name="in-azure-portal"></a>No portal do Azure

1. No portal, clique em **horizontal**. Utilize o controlo de deslize para selecionar o número de servidores de réplica de consulta. É o número de réplicas que escolher, além do servidor existente.

2. Na **separar o servidor de processamento do conjunto consulta**, selecione Sim para excluir o seu servidor de processamento de servidores de consulta. Cliente [conexões](#connections) a cadeia de ligação predefinida a utilizar (sem `:rw`) são redirecionadas para as réplicas no agrupamento de consulta. 

   ![Controlo de deslize de escalamento horizontal](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Clique em **guardar** para aprovisionar os novos servidores de réplica de consulta. 

Ao configurar o Escalamento horizontal para um servidor na primeira vez, modelos no seu servidor primário são sincronizados automaticamente com as réplicas no agrupamento de consulta. Sincronização automática só ocorre uma vez, quando configura pela primeira vez o Escalamento horizontal a um ou mais réplicas. As alterações subsequentes para o número de réplicas no mesmo servidor *não irá acionar a sincronização automática em outro*. Sincronização automática não irá ocorrer novamente, mesmo se definir o servidor de réplicas de zero e, em seguida, novamente Escalamento horizontal para qualquer número de réplicas. 

## <a name="synchronize"></a>Sincronizar 

Operações de sincronização devem ser executadas manualmente ou com a API de REST.

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

Para executar a sincronização, utilize [sincronização AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Para definir o número de réplicas de consulta, utilize [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o opcional `-ReadonlyReplicaCount` parâmetro.

## <a name="connections"></a>Ligações

Na página de descrição geral do seu servidor, existem dois nomes de servidor. Se ainda não tiver configurado Escalamento horizontal para um servidor, ambos os nomes de servidor funcionam da mesma. Depois de configurar o Escalamento horizontal para um servidor, tem de especificar o nome de servidor adequado, consoante o tipo de ligação. 

Para ligações de cliente do utilizador final, como o Power BI Desktop, Excel e aplicações personalizadas, de utilização **nome do servidor**. 

Para o SSMS, SSDT e cadeias de ligação no PowerShell, aplicações de função do Azure e o AMO, utilizam **nome do servidor de gestão**. O nome do servidor de gestão inclui um especial `:rw` qualificador (leitura / escrita). Todas as operações de processamento ocorrerem no servidor de gestão (principal).

![Nomes de servidor](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Resolução de problemas

**Problema:** Os utilizadores obtêm o erro **não é possível localizar o servidor '\<nome do servidor > "instância no modo de ligação"Só de leitura".**

**Solução:** Ao selecionar o **separar o servidor de processamento do conjunto de consulta** opção, as ligações de cliente utilizando a cadeia de ligação padrão (sem `:rw`) são redirecionadas para réplicas de conjunto de consulta. Se as réplicas no agrupamento de consulta são ainda online porque a sincronização não tem ainda não foi concluídas, as ligações de cliente redirecionada podem falhar. Para impedir ligações falhadas, deve haver pelo menos dois servidores no agrupamento de consulta quando efetuar uma sincronização. Cada servidor está sincronizado individualmente, enquanto outros permanecem online. Se optar por não ter o servidor de processamento do conjunto de consulta durante o processamento, é possível removê-lo a partir do agrupamento para processamento, e, em seguida, adicioná-lo após a conclusão do processamento, mas antes da sincronização para o pool. Utilize métricas de memória e QPU para monitorizar o estado de sincronização.

## <a name="related-information"></a>Informações relacionadas

[Monitorizar as métricas do servidor](analysis-services-monitor.md)   
[Gerir o Azure Analysis Services](analysis-services-manage.md) 
