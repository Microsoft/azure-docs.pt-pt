---
title: Serviços de Análise Azure escala-out Microsoft Docs
description: Replicar servidores de Serviços de Análise Azure com escala-out. As consultas de clientes podem então ser distribuídas por múltiplas réplicas de consultas numa piscina de consulta seletiva.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3ea304d038618fc428f20e7ad72b398f593d09a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78247999"
---
# <a name="azure-analysis-services-scale-out"></a>Escalamento horizontal do Azure Analysis Services

Com escala para fora, as consultas de cliente podem ser distribuídas por *múltiplas réplicas* de consultas em um pool de *consulta,* reduzindo os tempos de resposta durante as altas cargas de trabalho de consulta. Também pode separar o processamento do pool de consulta, garantindo que as consultas dos clientes não são afetadas negativamente pelas operações de processamento. A escala-out pode ser configurada no portal Azure ou utilizando a API REST Dos Serviços de Análise.

A escala está disponível para servidores no nível de preços Standard. Cada réplica de consulta é faturada ao mesmo ritmo que o seu servidor. Todas as réplicas de consulta são criadas na mesma região que o seu servidor. O número de réplicas de consulta que pode configurar é limitado pela região em que o seu servidor se encontra. Para saber mais, consulte [Disponibilidade por região.](analysis-services-overview.md#availability-by-region) A escala não aumenta a quantidade de memória disponível para o seu servidor. Para aumentar a memória, precisa de atualizar o seu plano. 

## <a name="why-scale-out"></a>Por que a escala para fora?

Numa implementação típica do servidor, um servidor serve tanto como servidor de processamento como servidor de consulta. Se o número de consultas de clientes contra modelos no seu servidor exceder as Unidades de Processamento de Consulta (QPU) para o plano do seu servidor, ou o processamento de modelos ocorrer ao mesmo tempo que as altas cargas de trabalho de consulta, o desempenho pode diminuir. 

Com escala para fora, você pode criar um conjunto de consulta com até sete recursos adicionais de réplica de consulta (oito no total, incluindo o seu servidor *principal).* Você pode escalar o número de réplicas no pool de consulta para satisfazer as exigências da QPU em momentos críticos, e você pode separar um servidor de processamento do pool de consulta a qualquer momento. 

Independentemente do número de réplicas de consulta que você tem em uma piscina de consulta, as cargas de trabalho de processamento não são distribuídas entre réplicas de consulta. O servidor principal serve como servidor de processamento. As réplicas de consulta servem apenas consultas contra as bases de dados do modelo sincronizadas entre o servidor primário e cada réplica no pool de consulta. 

Ao escalonar, pode levar até cinco minutos para que novas réplicas de consulta sejam adicionadas incrementalmente à piscina de consulta. Quando todas as novas réplicas de consultas estão em funcionamento, novas ligações de clientes são equilibradas em todos os recursos na piscina de consulta. As ligações existentes ao cliente não são alteradas a partir do recurso a que estão atualmente ligados. Ao escalar, quaisquer ligações de clientes existentes a um recurso de piscina de consulta que está sendo removido da piscina de consulta são terminadas. Os clientes podem reconectar-se com um recurso restante da consulta.

## <a name="how-it-works"></a>Como funciona

Ao configurar a escala pela primeira vez, as bases de dados do modelo no seu servidor principal são *automaticamente* sincronizadas com novas réplicas num novo conjunto de consultas. A sincronização automática ocorre apenas uma vez. Durante a sincronização automática, os ficheiros de dados do servidor primário (encriptados em repouso no armazenamento de blob) são copiados para uma segunda localização, também encriptadas em repouso no armazenamento de blob. As réplicas na piscina de consulta são então *hidratadas* com dados do segundo conjunto de ficheiros. 

Enquanto uma sincronização automática é realizada apenas quando você escala um servidor pela primeira vez, você também pode realizar uma sincronização manual. Sincronizar assegura dados sobre réplicas na consulta do pool correspondem aos do servidor primário. Ao processar (atualizar) os modelos no servidor primário, deve ser efetuada uma sincronização *após* a conclusão das operações de processamento. Esta sincronização copia dados atualizados dos ficheiros do servidor primário no armazenamento de blob para o segundo conjunto de ficheiros. As réplicas na piscina de consulta são então hidratadas com dados atualizados do segundo conjunto de ficheiros no armazenamento de blob. 

Ao realizar uma operação de escala subsequente, por exemplo, aumentando o número de réplicas no pool de consulta de dois para cinco, as novas réplicas são hidratadas com dados do segundo conjunto de ficheiros no armazenamento de blob. Não há sincronização. Se então realizar uma sincronização após a escala, as novas réplicas na piscina de consulta seriam hidratadas duas vezes - uma hidratação redundante. Ao realizar uma operação de escala subsequente, é importante ter em mente:

* Efetue uma sincronização *antes da operação de escala* para evitar uma hidratação redundante das réplicas adicionadas. Não são permitidas operações de sincronização simultânea e de escala em funcionamento ao mesmo tempo.

* Ao automatizar operações de processamento *e* de escala, é importante primeiro processar dados no servidor primário, em seguida, realizar uma sincronização e, em seguida, executar a operação scale-out. Esta sequência assegura um impacto mínimo na QPU e nos recursos de memória.

* A sincronização é permitida mesmo quando não há réplicas na piscina de consulta. Se estiver a escalonar de zero a uma ou mais réplicas com novos dados de uma operação de processamento no servidor primário, execute a sincronização primeiro sem réplicas no pool de consulta e, em seguida, scale-out. Sincronizar antes de esescalar evita a hidratação redundante das réplicas recém-adicionadas.

* Ao eliminar uma base de dados de modelos do servidor primário, não é automaticamente eliminado das réplicas no pool de consulta. Deve executar uma operação de sincronização utilizando o comando [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell que remove o ficheiro/s para essa base de dados do local de armazenamento de blob partilhado da réplica e, em seguida, elimina a base de dados do modelo nas réplicas do pool de consulta. Para determinar se existe uma base de dados de modelos em réplicas no pool de consultas, mas não no servidor principal, certifique-se de que o servidor de processamento Separado da definição **de piscina** de consulta é para **Sim**. Em seguida, utilize sSMS para se `:rw` ligar ao servidor primário utilizando o qualificador para ver se a base de dados existe. Em seguida, ligue-se a réplicas no `:rw` pool de consulta, conectando-se sem o qualificador para ver se a mesma base de dados também existe. Se a base de dados existir em réplicas no pool de consultas, mas não no servidor primário, execute uma operação de sincronização.   

* Ao renomear uma base de dados no servidor primário, há um passo adicional necessário para garantir que a base de dados está devidamente sincronizada com quaisquer réplicas. Depois de renomear, execute uma sincronização utilizando o comando [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) especificando o `-Database` parâmetro com o nome de base de dados antigo. Esta sincronização remove a base de dados e os ficheiros com o nome antigo de quaisquer réplicas. Em seguida, execute outra sincronização especificando o `-Database` parâmetro com o novo nome da base de dados. A segunda sincronização copia a base de dados recém-nomeada para o segundo conjunto de ficheiros e hidrata quaisquer réplicas. Estas sincronizações não podem ser realizadas utilizando o comando do modelo Synchronize no portal.

### <a name="synchronization-mode"></a>Modo de sincronização

Por padrão, as réplicas de consulta são rehidratadas na totalidade, não incrementalmente. A reidratação acontece por fases. São separadas e anexadas duas de cada vez (assumindo que existem pelo menos três réplicas) para garantir que pelo menos uma réplica seja mantida on-line para consultas a qualquer momento. Em alguns casos, os clientes podem precisar de se reconectar a uma das réplicas online enquanto este processo está a decorrer. Ao utilizar a definição (em Pré-visualização) **ReplicaSyncMode,** pode agora especificar a sincronização da réplica da consulta ocorre em paralelo. A sincronização paralela proporciona os seguintes benefícios: 

- Redução significativa do tempo de sincronização. 
- Os dados através das réplicas são mais propensos a serem consistentes durante o processo de sincronização. 
- Como as bases de dados são mantidas on-line em todas as réplicas durante todo o processo de sincronização, os clientes não precisam de se reconectar. 
- A cache na memória é atualizada incrementalmente com apenas os dados alterados, que podem ser mais rápidos do que reidratar totalmente o modelo. 

#### <a name="setting-replicasyncmode"></a>Definição de ReplicaSyncmode

Utilize SSMS para definir ReplicaSyncMode em propriedades avançadas. Os valores possíveis são: 

- `1`(predefinido): Reidratação total da base de dados de réplicas em fases (incremental). 
- `2`: Sincronização otimizada em paralelo. 

![Definição RelicaSyncMode](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

Ao definir **O ReplicaSyncMode=2**, dependendo da quantidade de cache que precisa de ser atualizada, a memória adicional pode ser consumida pelas réplicas de consulta. Para manter a base de dados on-line e disponível para consultas, dependendo da quantidade de dados alterada, a operação pode exigir até *duplicar a memória* da réplica, uma vez que tanto os segmentos antigos como os novos são mantidos na memória simultaneamente. Os nós de réplica têm a mesma alocação de memória que o nó primário, e normalmente há memória extra no nó primário para operações de atualização, por isso pode ser improvável que as réplicas fiquem sem memória. Além disso, o cenário comum é que a base de dados é atualizada incrementalmente no nó primário, pelo que a exigência de duplicar a memória deve ser incomum. Se a operação Sync encontrar um erro de memória, irá voltar a tentar utilizando a técnica predefinida (prenda/despeça dois de cada vez). 

### <a name="separate-processing-from-query-pool"></a>Processamento separado da piscina de consulta

Para um desempenho máximo tanto para operações de processamento como para consultas, pode optar por separar o seu servidor de processamento do pool de consulta. Quando separados, novas ligações de clientes são atribuídas a réplicas de consulta apenas na piscina de consulta. Se as operações de processamento demorarem apenas um curto período de tempo, pode optar por separar o seu servidor de processamento do pool de consulta apenas pelo tempo que leva para realizar operações de processamento e sincronização e, em seguida, incluí-lo de volta para o conjunto de consultas. Ao separar o servidor de processamento da piscina de consulta, ou adicioná-lo de volta na piscina de consulta pode demorar até cinco minutos para a operação ser concluída.

## <a name="monitor-qpu-usage"></a>Monitorizar a utilização do QPU

Para determinar se é necessário sair em escala para o seu servidor, [monitorize o seu servidor](analysis-services-monitor.md) no portal Azure utilizando métricas. Se o seu QPU atingir regularmente o limite de perguntas contra os seus modelos está a exceder o limite de QPU para o seu plano. A métrica de comprimento da fila de trabalho da piscina de consulta também aumenta quando o número de consultas na fila de consulta thread pool excede qPU disponível. 

Outra boa métrica para assistir é a média de QPU por ServerResourceType. Esta métrica compara a Média de QPU para o servidor primário com o pool de consulta. 

![Consulta escala métricas](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**Para configurar o QPU por ServerResourceType**

1. Num gráfico de linha De Métricas, clique em **Adicionar métrica**. 
2. No **RECURSO**, selecione o seu servidor, em seguida, em **METRIC NAMESPACE,** selecione **As métricas padrão dos Serviços**de Análise, em seguida, em **MÉTRICA,** selecione **QPU,** e depois em **AGGREGATION**, selecione **Avg**. 
3. Clique em **Aplicar Spliting**. 
4. Em **VALORES,** selecione **ServerResourceType**.  

### <a name="detailed-diagnostic-logging"></a>Registo de diagnóstico detalhado

Utilize registos de monitores Azure para diagnósticos mais detalhados dos recursos do servidor. Com registos, pode utilizar consultas de Log Analytics para libertar o QPU e a memória por servidor e réplica. Para saber mais, consulte consultas de exemplo em diagnósticos de [diagnóstico de Serviços](analysis-services-logging.md#example-queries)de Análise.


## <a name="configure-scale-out"></a>Configurar o escalamento horizontal

### <a name="in-azure-portal"></a>No portal Azure

1. No portal, clique em **Scale-out**. Utilize o slider para selecionar o número de servidores de réplicas de consulta. O número de réplicas que escolher é para além do servidor existente.  

2. Em **Separar o servidor de processamento do pool de consulta,** selecione sim para excluir o seu servidor de processamento de servidores de consulta. As [ligações](#connections) do cliente utilizando `:rw`a cadeia de ligação padrão (sem) são redirecionadas para réplicas na piscina de consulta. 

   ![Deslizador scale-out](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Clique em **Guardar** para fornecer os seus novos servidores de réplica de consulta. 

Ao configurar a escala para um servidor pela primeira vez, os modelos do seu servidor principal são automaticamente sincronizados com réplicas no pool de consulta. A sincronização automática só ocorre uma vez, quando configura a escala para uma ou mais réplicas. Alterações subsequentes no número de réplicas no mesmo servidor *não desencadearão outra sincronização automática*. A sincronização automática não voltará a ocorrer mesmo que coloque o servidor em réplicas zero e, em seguida, volte a escalar para qualquer número de réplicas. 

## <a name="synchronize"></a>Sincronizar 

As operações de sincronização devem ser efetuadas manualmente ou utilizando a API REST.

### <a name="in-azure-portal"></a>No portal Azure

Em **visão geral** > modelo > modelo **Synchronize**.

![Deslizador scale-out](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST

Use a operação **de sincronização.**

#### <a name="synchronize-a-model"></a>Sincronizar um modelo   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Obtenha o estado da sincronização  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Códigos de estado de devolução:


|Código  |Descrição  |
|---------|---------|
|-1     |  Inválido       |
|0     | Replicação        |
|1     |  Reidratação       |
|2     |   Concluído       |
|3     |   Falhou      |
|4     |    Finalizando     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de utilizar o PowerShell, [instale ou atualize o mais recente módulo PowerShell Do IA.](/powershell/azure/install-az-ps) 

Para executar sincronização, utilize [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Para definir o número de réplicas de consulta, utilize [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o parâmetro opcional. `-ReadonlyReplicaCount`

Para separar o servidor de processamento do pool de consultas, utilize o [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o parâmetro opcional `-DefaultConnectionMode` a utilizar. `Readonly`

Para saber mais, consulte Utilizar um diretor de [serviço com o módulo Az.AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Ligações

Na página de visão geral do seu servidor, existem dois nomes de servidores. Se ainda não configuraram a escala para um servidor, ambos os nomes do servidor funcionam da mesma forma. Uma vez configurado a escala para um servidor, precisa especificar o nome do servidor apropriado dependendo do tipo de ligação. 

Para ligações de clientes de utilizador final como Power BI Desktop, Excel e aplicações personalizadas, use **o nome server**. 

Para SSMS, Visual Studio e cordas de ligação em PowerShell, aplicações Azure Function e AMO, use o nome do **servidor Management**. O nome do servidor `:rw` de gestão inclui uma qualificação especial (read-write). Todas as operações de processamento ocorrem no servidor de gestão (primário).

![Nomes do servidor](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Escala-up, Scale-down vs. Scale-out

Pode alterar o nível de preços num servidor com várias réplicas. O mesmo nível de preços aplica-se a todas as réplicas. Uma operação à escala irá primeiro derrubar todas as réplicas de uma só vez e depois trazer todas as réplicas no novo nível de preços.

## <a name="troubleshoot"></a>Resolução de problemas

**Emissão:** Os utilizadores obtêm erro **Não podem encontrar o nome do servidor '\<Nome do servidor>' no modo de ligação 'ReadOnly'.**

**Solução:** Ao selecionar o **servidor de processamento Separe o servidor de processamento da** opção de consulta de piscina, as ligações do cliente utilizando a cadeia de ligação padrão (sem) `:rw`são redirecionadas para a consulta de réplicas do pool. Se as réplicas na piscina de consulta ainda não estiverem online porque a sincronização ainda não foi concluída, as ligações redirecionadas do cliente podem falhar. Para evitar ligações falhadas, deve haver pelo menos dois servidores na piscina de consulta ao realizar uma sincronização. Cada servidor é sincronizado individualmente enquanto outros permanecem on-line. Se optar por não ter o servidor de processamento no pool de consulta durante o processamento, pode optar por removê-lo da piscina para processamento e, em seguida, adicioná-lo de volta à piscina após o processamento estar completo, mas antes da sincronização. Utilize as métricas de Memória e QPU para monitorizar o estado de sincronização.



## <a name="related-information"></a>Informações relacionadas

[Monitorizar as métricas do servidor](analysis-services-monitor.md)   
[Gerir serviços de análise azure](analysis-services-manage.md) 
