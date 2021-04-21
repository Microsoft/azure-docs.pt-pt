---
title: Azure Analysis Services escala-out| Microsoft Docs
description: Replicar servidores de Serviços de Análise Azure com escala. As consultas ao cliente podem então ser distribuídas entre réplicas de consulta múltiplas numa piscina de consultas de escala.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2385cb811e322bd44daefa03d821b2ae47e0652
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769270"
---
# <a name="azure-analysis-services-scale-out"></a>Escalamento horizontal do Azure Analysis Services

Com a escala, as consultas ao cliente podem ser distribuídas entre réplicas de consulta *múltiplas* numa piscina de *consultas,* reduzindo os tempos de resposta durante as elevadas cargas de trabalho de consulta. Também pode separar o processamento do pool de consultas, garantindo que as consultas ao cliente não são afetadas negativamente pelas operações de processamento. A escala pode ser configurada no portal Azure ou utilizando os Serviços de Análise REST API.

A escala está disponível para servidores no nível de preços Standard. Cada réplica de consulta é faturada à mesma taxa que o seu servidor. Todas as réplicas de consulta são criadas na mesma região que o seu servidor. O número de réplicas de consulta que pode configurar são limitados pela região em que o seu servidor se encontra. Para saber mais, consulte [Disponibilidade por região.](analysis-services-overview.md#availability-by-region) A escala não aumenta a quantidade de memória disponível para o seu servidor. Para aumentar a memória, precisa de atualizar o seu plano. 

## <a name="why-scale-out"></a>Por que escalar?

Numa implementação típica do servidor, um servidor serve tanto como servidor de processamento como servidor de consulta. Se o número de consultas de clientes contra modelos no seu servidor exceder as Unidades de Processamento de Consulta (QPU) para o plano do seu servidor, ou o processamento de modelos ocorrer ao mesmo tempo que cargas de trabalho de consulta elevadas, o desempenho pode diminuir. 

Com a escala, pode criar um pool de consultas com até sete recursos de réplica de consulta adicionais (oito no total, incluindo o seu servidor *primário).* Pode escalar o número de réplicas na piscina de consultas para satisfazer as exigências da QPU em momentos críticos, e pode separar um servidor de processamento do pool de consultas a qualquer momento. 

Independentemente do número de réplicas de consulta que tenha numa piscina de consultas, as cargas de trabalho de processamento não são distribuídas entre réplicas de consulta. O servidor primário serve como servidor de processamento. As réplicas de consulta servem apenas consultas contra as bases de dados de modelo sincronizadas entre o servidor primário e cada réplica no pool de consultas. 

Ao escalonar, pode levar até cinco minutos para que novas réplicas de consulta sejam adicionadas gradualmente à piscina de consulta. Quando todas as réplicas de consultas novas estão em funcionamento, as novas ligações ao cliente são equilibradas entre os recursos na piscina de consultas. As ligações de clientes existentes não são alteradas a partir do recurso a que estão atualmente ligadas. Ao escalonar, quaisquer ligações de cliente existentes a um recurso de conjunto de consulta que está sendo removido da piscina de consulta são encerradas. Os clientes podem voltar a ligar-se a um recurso de piscina de consulta restante.

## <a name="how-it-works"></a>Como funciona

Ao configurar a escala da primeira vez, as bases de dados dos modelos no seu servidor primário são *automaticamente* sincronizadas com novas réplicas num novo pool de consultas. A sincronização automática ocorre apenas uma vez. Durante a sincronização automática, os ficheiros de dados do servidor primário (encriptados em repouso no armazenamento de bolhas) são copiados para uma segunda localização, também encriptados em repouso no armazenamento de bolhas. As réplicas no pool de consultas são então *hidratadas* com dados do segundo conjunto de ficheiros. 

Enquanto uma sincronização automática é realizada apenas quando escala um servidor pela primeira vez, também pode efetuar uma sincronização manual. A sincronização assegura dados sobre réplicas na piscina de consulta que correspondem às do servidor primário. Quando processar (atualizar) os modelos no servidor primário, deve ser efetuada uma sincronização *após* a conclusão das operações de processamento. Esta sincronização copia dados atualizados dos ficheiros do servidor primário no armazenamento de bolhas para o segundo conjunto de ficheiros. As réplicas no pool de consultas são então hidratadas com dados atualizados a partir do segundo conjunto de ficheiros no armazenamento de bolhas. 

Ao executar uma operação de escala subsequente, por exemplo, aumentando o número de réplicas na piscina de consulta de dois para cinco, as novas réplicas são hidratadas com dados do segundo conjunto de ficheiros no armazenamento de bolhas. Não há sincronização. Se então fizer uma sincronização depois de escalonar, as novas réplicas na piscina de consulta seriam hidratadas duas vezes - uma hidratação redundante. Ao realizar uma operação de escala subsequente, é importante ter em mente:

* Efetuar uma sincronização *antes da operação de escala para* evitar a hidratação redundante das réplicas adicionadas. Não são permitidas operações de sincronização e de escala simultâneas em execução ao mesmo tempo.

* Ao automatizar as operações de processamento *e* escala, é importante primeiro processar os dados no servidor primário, depois realizar uma sincronização e, em seguida, executar a operação de escala. Esta sequência garante o mínimo impacto na QPU e nos recursos de memória.

* Durante as operações de escala, todos os servidores do pool de consultas, incluindo o servidor primário, estão temporariamente offline.

* A sincronização é permitida mesmo quando não há réplicas na piscina de consultas. Se estiver a escalonar de zero para uma ou mais réplicas com novos dados de uma operação de processamento no servidor primário, execute primeiro a sincronização sem réplicas no pool de consultas e, em seguida, dimensione. Sincronizar antes de escalonar evita a hidratação redundante das réplicas recém-adicionadas.

* Ao eliminar uma base de dados de modelos do servidor primário, não é automaticamente eliminado de réplicas no pool de consultas. Tem de efetuar uma operação de sincronização utilizando o comando [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell que remove o ficheiro/s dessa base de dados a partir do local de armazenamento de bolhas partilhadas da réplica e, em seguida, elimina a base de dados do modelo nas réplicas no pool de consultas. Para determinar se existe uma base de dados de modelos em réplicas no pool de consultas, mas não no servidor primário, certifique-se de que **o servidor de processamento separa da definição do pool de consulta** é sim .  Em seguida, utilize SSMS para ligar ao servidor primário utilizando o `:rw` qualificador para ver se a base de dados existe. Em seguida, ligue-se a réplicas na piscina de consulta conectando-se sem o `:rw` qualificador para ver se a mesma base de dados também existe. Se a base de dados existir em réplicas no pool de consultas, mas não no servidor primário, execute uma operação de sincronização.   

* Ao renomear uma base de dados no servidor primário, há um passo adicional necessário para garantir que a base de dados está corretamente sincronizada com quaisquer réplicas. Após renomear, efetue uma sincronização utilizando o comando [Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) especificando o `-Database` parâmetro com o nome da base de dados antiga. Esta sincronização remove a base de dados e ficheiros com o nome antigo de quaisquer réplicas. Em seguida, efetue outra sincronização especificando o `-Database` parâmetro com o novo nome da base de dados. A segunda sincronização copia a base de dados recém-nomeada para o segundo conjunto de ficheiros e hidrata quaisquer réplicas. Estas sincronizações não podem ser realizadas utilizando o comando do modelo Synchronize no portal.

### <a name="synchronization-mode"></a>Modo de sincronização

Por padrão, as réplicas de consulta são rehidratadas na totalidade, não incrementalmente. A reidratação acontece por etapas. São separadas e anexadas duas de cada vez (assumindo que existem pelo menos três réplicas) para garantir que pelo menos uma réplica seja mantida on-line para consultas a qualquer momento. Em alguns casos, os clientes podem precisar de voltar a ligar-se a uma das réplicas online enquanto este processo está a decorrer. Ao utilizar a definição **ReplicaSyncMode,** pode agora especificar que a sincronização de réplicas de consulta ocorre em paralelo. A sincronização paralela proporciona os seguintes benefícios: 

- Redução significativa do tempo de sincronização. 
- Os dados através de réplicas são mais propensos a serem consistentes durante o processo de sincronização. 
- Como as bases de dados são mantidas on-line em todas as réplicas durante todo o processo de sincronização, os clientes não precisam de se reconectar. 
- A cache na memória é atualizada incrementalmente apenas com os dados alterados, que podem ser mais rápidos do que hidratar totalmente o modelo. 

#### <a name="setting-replicasyncmode"></a>Definição replicaSyncMode

Utilize SSMS para definir ReplicaSyncMode em Propriedades Avançadas. Os valores possíveis são: 

- `1` (predefinição): Reidratação total da base de dados de réplicas em fases (incrementais). 
- `2`: Sincronização otimizada em paralelo. 

![Definição de RelicaSyncMode](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

Ao definir **ReplicaSyncMode=2**, dependendo da quantidade de cache que precisa de ser atualizada, a memória adicional pode ser consumida pelas réplicas de consulta. Para manter a base de dados on-line e disponível para consultas, dependendo da quantidade de dados alterados, a operação pode exigir até *duplicar a memória* na réplica porque tanto os segmentos antigos como os novos são mantidos na memória simultaneamente. Os nós de réplica têm a mesma alocação de memória que o nó primário, e normalmente há memória extra no nó primário para operações de atualização, por isso pode ser improvável que as réplicas fiquem sem memória. Além disso, o cenário comum é que a base de dados é progressivamente atualizada no nó primário, pelo que a exigência de duplicar a memória deve ser incomum. Se a operação Sync encontrar um erro de memória fora do tempo, tentará novamente utilizando a técnica predefinida (anexar/desprender dois de cada vez). 

### <a name="separate-processing-from-query-pool"></a>Processamento separado da piscina de consulta

Para um desempenho máximo tanto para operações de processamento como de consulta, pode optar por separar o seu servidor de processamento do pool de consultas. Quando separados, novas ligações de clientes são atribuídas a réplicas de consulta apenas no pool de consultas. Se as operações de processamento demorarem apenas um curto período de tempo, pode optar por separar o seu servidor de processamento do pool de consultas apenas pelo tempo necessário para executar operações de processamento e sincronização e, em seguida, incluí-lo de volta no pool de consultas. Ao separar o servidor de processamento da piscina de consulta, ou adicioná-lo de volta à piscina de consulta pode demorar até cinco minutos para a operação estar concluída.

## <a name="monitor-qpu-usage"></a>Monitorização da utilização do QPU

Para determinar se a escala para o seu servidor é necessária, [monitorize o seu servidor](analysis-services-monitor.md) no portal Azure utilizando métricas. Se o seu QPU regularmente atingir o limite, significa que o número de consultas contra os seus modelos está a exceder o limite de QPU para o seu plano. A métrica do comprimento da fila do trabalho da piscina de consulta também aumenta quando o número de consultas na fila da piscina de rosca de consulta excede o QPU disponível. 

Outra boa métrica para assistir é qPU média por ServerResourceType. Esta métrica compara o QPU médio para o servidor primário com o pool de consultas. 

![Métricas de escala de consulta](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**Para configurar qPU por ServerResourceType**

1. Num gráfico de linha métrica, clique **em Adicionar métrica**. 
2. Em **RECURSO**, selecione o seu servidor, em seguida, em **METRIC NAMESPACE**, selecione **métricas métricas de Serviços de Análise**, em seguida, em **MÉTRICA**, selecione **QPU**, e, em seguida, em **AGGREGATION**, selecione **Avg**. 
3. Clique **em Aplicar Divisão**. 
4. Em **VALORES**, selecione **ServerResourceType**.  

### <a name="detailed-diagnostic-logging"></a>Registo de diagnóstico detalhado

Utilize registos de monitores Azure para diagnósticos mais detalhados dos recursos do servidor dimensionado. Com registos, pode utilizar consultas de Log Analytics para desvendar qPU e memória por servidor e réplica. Para saber mais, consulte consultas de exemplo em [Serviços de Análise de diagnósticos de registo.](analysis-services-logging.md#example-queries)


## <a name="configure-scale-out"></a>Configurar o escalamento horizontal

### <a name="in-azure-portal"></a>No portal Azure

1. No portal, clique em **Scale-out**. Utilize o slider para selecionar o número de servidores de réplicas de consulta. O número de réplicas que escolhe é além do servidor existente.  

2. Em **Separar o servidor de processamento do pool de consulta,** selecione sim para excluir o seu servidor de processamento de servidores de consulta. As [ligações](#connections) do cliente utilizando a cadeia de ligação padrão `:rw` (sem) são redirecionadas para réplicas na piscina de consulta. 

   ![Deslizador de escala](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Clique em **Guardar** para obter os seus novos servidores de réplica de consulta. 

Ao configurar a escala para um servidor pela primeira vez, os modelos no seu servidor primário são automaticamente sincronizados com réplicas na piscina de consulta. A sincronização automática só ocorre uma vez, quando configura a escala para uma ou mais réplicas. Alterações subsequentes ao número de réplicas no mesmo servidor *não desencadearão outra sincronização automática*. A sincronização automática não voltará a ocorrer mesmo que tenha definido o servidor em réplicas zero e, em seguida, novamente em escala para qualquer número de réplicas. 

## <a name="synchronize"></a>Sincronizar 

As operações de sincronização devem ser efetuadas manualmente ou utilizando a API REST.

### <a name="in-azure-portal"></a>No portal Azure

No **modelo** > > **Synchronize.**

![Ícone de sincronização](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST

Utilize a operação **de sincronização.**

#### <a name="synchronize-a-model"></a>Sincronizar um modelo   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Obtenha o estado de sincronização  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Códigos de estado de devolução:


|Código  |Description  |
|---------|---------|
|-1     |  Inválido       |
|0     | Replicação        |
|1     |  Reidratação       |
|2     |   Concluído       |
|3     |   Com falhas      |
|4     |    Finalização     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de utilizar o PowerShell, [instale ou atualize o mais recente módulo Azure PowerShell](/powershell/azure/install-az-ps). 

Para executar sincronização, utilize [o Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Para definir o número de réplicas de consulta, utilize [o Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o `-ReadonlyReplicaCount` parâmetro opcional.

Para separar o servidor de processamento do pool de consultas, utilize [o Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o parâmetro opcional `-DefaultConnectionMode` a utilizar `Readonly` .

Para saber mais, consulte [utilizar um principal de serviço com o módulo Az.AnalysisServices.](analysis-services-service-principal.md#azmodule)

## <a name="connections"></a>Ligações

Na página geral do seu servidor, existem dois nomes de servidor. Se ainda não configurar a escala para um servidor, ambos os nomes do servidor funcionam da mesma forma. Uma vez configurar a escala para um servidor, tem de especificar o nome do servidor apropriado dependendo do tipo de ligação. 

Para ligações de clientes finais como Power BI Desktop, Excel e aplicações personalizadas, utilize **o nome do Servidor**. 

Para SSMS, Visual Studio e cadeias de conexão em PowerShell, aplicações Azure Function e AMO, utilize **o nome do servidor Management**. O nome do servidor de gestão inclui uma qualificação especial `:rw` (ler-escrever). Todas as operações de processamento ocorrem no servidor de gestão (primário).

![Nomes do servidor](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Escala, Escala para baixo vs. Escala-out

Pode alterar o nível de preços num servidor com várias réplicas. O mesmo nível de preços aplica-se a todas as réplicas. Uma operação de escala irá primeiro derrubar todas as réplicas de uma só vez e depois trazer todas as réplicas para o novo nível de preços.

## <a name="troubleshoot"></a>Resolução de problemas

**Emissão:** Os utilizadores obtêm erro **Não podem encontrar a instância do servidor no modo de \<Name of the server> ligação 'ReadOnly'.**

**Solução:** Ao selecionar **o servidor de processamento separar o servidor de processamento da opção de piscina de consulta, as** ligações do cliente utilizando a cadeia de ligação padrão (sem ) são `:rw` redirecionadas para réplicas de piscina de consulta. Se as réplicas no pool de consultas ainda não estiverem on-line porque a sincronização ainda não foi concluída, as ligações redirecionadas do cliente podem falhar. Para evitar ligações falhadas, deve haver pelo menos dois servidores na piscina de consultas quando efetuar uma sincronização. Cada servidor é sincronizado individualmente enquanto outros permanecem online. Se optar por não ter o servidor de processamento na piscina de consultas durante o processamento, pode optar por removê-lo da piscina para processamento e, em seguida, adicioná-lo de volta à piscina após o processamento estar concluído, mas antes da sincronização. Utilize as métricas de Memória e QPU para monitorizar o estado de sincronização.



## <a name="related-information"></a>Informações relacionadas

[Monitorizar métricas do servidor](analysis-services-monitor.md)   
[Gerir serviços de análise Azure](analysis-services-manage.md)
