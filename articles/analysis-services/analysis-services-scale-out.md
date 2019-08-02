---
title: Escala horizontal Azure Analysis Servicesda | Microsoft Docs
description: Replicar servidores Azure Analysis Services com expansão
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8297a2b1e78da6685b3129071612dc4457990bc1
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696392"
---
# <a name="azure-analysis-services-scale-out"></a>Escalamento horizontal do Azure Analysis Services

Com a expansão, as consultas de cliente podem ser distribuídas entre várias réplicas de *consulta* em um *pool de consultas*, reduzindo os tempos de resposta durante cargas de trabalho de consulta alta. Você também pode separar o processamento do pool de consultas, garantindo que as consultas do cliente não sejam afetadas negativamente pelas operações de processamento. A expansão pode ser configurada no portal do Azure ou usando a API REST do Analysis Services.

A expansão está disponível para servidores no tipo de preço Standard. Cada réplica de consulta é cobrada com a mesma taxa que o servidor. Todas as réplicas de consulta são criadas na mesma região que o servidor. O número de réplicas de consulta que você pode configurar é limitado pela região em que o servidor está. Para saber mais, confira [disponibilidade por região](analysis-services-overview.md#availability-by-region). A expansão não aumenta a quantidade de memória disponível para o servidor. Para aumentar a memória, você precisa atualizar seu plano. 

## <a name="why-scale-out"></a>Por que escalar horizontalmente?

Em uma implantação de servidor típica, um servidor serve como servidor de processamento e de consulta. Se o número de consultas de cliente em modelos em seu servidor exceder as QPU (unidades de processamento de consulta) para o plano do seu servidor ou o processamento de modelo ocorrer ao mesmo tempo que as cargas de trabalho de consulta altas, o desempenho poderá diminuir. 

Com a expansão, você pode criar um pool de consultas com até sete recursos adicionais de réplica de consulta (oito no total, incluindo o servidor *primário* ). Você pode dimensionar o número de réplicas no pool de consultas para atender às demandas de QPU em momentos críticos e pode separar um servidor de processamento do pool de consultas a qualquer momento. 

Independentemente do número de réplicas de consulta que você tem em um pool de consultas, as cargas de trabalho de processamento não são distribuídas entre réplicas de consulta. O servidor primário serve como servidor de processamento. As réplicas de consulta servem somente para os bancos de dados de modelo sincronizados entre o servidor primário e cada réplica no pool de consultas. 

Ao escalar horizontalmente, pode levar até cinco minutos para que novas réplicas de consulta sejam adicionadas incrementalmente ao pool de consulta. Quando todas as novas réplicas de consulta estiverem em funcionamento, novas conexões de cliente serão balanceadas com carga entre os recursos no pool de consultas. As conexões de cliente existentes não são alteradas do recurso ao qual estão conectadas no momento. Ao dimensionar no, todas as conexões de cliente existentes com um recurso de pool de consulta que está sendo removido do pool de consulta são encerradas. Os clientes podem se reconectar a um recurso do pool de consultas restante.

## <a name="how-it-works"></a>Como funciona

Ao configurar a expansão na primeira vez, os bancos de dados de modelo em seu servidor primário são sincronizados *automaticamente* com novas réplicas em um novo pool de consulta. A sincronização automática ocorre apenas uma vez. Durante a sincronização automática, os arquivos de dados do servidor primário (criptografados em repouso no armazenamento de BLOB) são copiados para um segundo local, também criptografados em repouso no armazenamento de BLOBs. As réplicas no pool de consultas são então alimentadas com dados do segundo conjunto de arquivos. 

Embora uma sincronização automática seja executada somente quando você escala horizontalmente um servidor pela primeira vez, você também pode executar uma sincronização manual. A sincronização garante que os dados em réplicas no pool de consulta correspondam ao servidor primário. Ao processar (atualizar) modelos no servidor primário, uma sincronização deve ser executada *após* a conclusão das operações de processamento. Esta sincronização copia os dados atualizados dos arquivos do servidor primário no armazenamento de BLOBs para o segundo conjunto de arquivos. As réplicas no pool de consultas são então alimentadas com dados atualizados do segundo conjunto de arquivos no armazenamento de BLOBs. 

Ao executar uma operação de expansão subsequente, por exemplo, aumentar o número de réplicas no pool de consultas de duas a cinco, as novas réplicas são alimentadas com dados do segundo conjunto de arquivos no armazenamento de BLOBs. Não há sincronização. Se, em seguida, você executar uma sincronização depois de escalar horizontalmente, as novas réplicas no pool de consultas seriam alimentadas duas vezes-uma hidratação redundante. Ao executar uma operação de expansão subsequente, é importante ter em mente:

* Execute uma sincronização *antes da operação de expansão* para evitar hidratação redundantes das réplicas adicionadas. A sincronização simultânea e as operações de expansão em execução ao mesmo tempo não são permitidas.

* Ao automatizar operações de processamento *e* expansão, é importante processar primeiro os dados no servidor primário, executar uma sincronização e, em seguida, executar a operação de expansão. Essa sequência garante um impacto mínimo sobre os recursos de memória e QPU.

* A sincronização é permitida mesmo quando não há réplicas no pool de consultas. Se você estiver expandindo de zero para uma ou mais réplicas com novos dados de uma operação de processamento no servidor primário, execute a sincronização primeiro sem réplicas no pool de consultas e, em seguida, expanda horizontalmente. A sincronização antes de escalar horizontalmente evita hidratação redundantes das réplicas adicionadas recentemente.

* Ao excluir um banco de dados modelo do servidor primário, ele não é excluído automaticamente das réplicas no pool de consultas. Você deve executar uma operação de sincronização usando o comando do PowerShell [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) que remove os arquivos do banco de dados do local de armazenamento de blob compartilhado da réplica e, em seguida, exclui o banco de dados modelo nas réplicas no pool de consultas. Para determinar se um banco de dados modelo existe em réplicas no pool de consultas, mas não no servidor primário, certifique-se de que a configuração **separar o servidor de processamento da consulta do pool** seja **Sim**. Em seguida, use o SSMS para se conectar ao servidor `:rw` primário usando o qualificador para ver se o banco de dados existe. Em seguida, conecte-se às réplicas no pool de consultas `:rw` conectando-se sem o qualificador para ver se o mesmo banco de dados também existe. Se o banco de dados existir em réplicas no pool de consultas, mas não no servidor primário, execute uma operação de sincronização.   

* Ao renomear um banco de dados no servidor primário, há uma etapa adicional necessária para garantir que o banco de dados seja sincronizado corretamente com as réplicas. Após a renomeação, execute uma sincronização usando o comando [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) especificando `-Database` o parâmetro com o nome antigo do banco de dados. Essa sincronização remove o banco de dados e os arquivos com o nome antigo de qualquer réplica. Em seguida, execute outra sincronização `-Database` especificando o parâmetro com o novo nome do banco de dados. A segunda sincronização copia o banco de dados nomeado recentemente para o segundo conjunto de arquivos e hidratam quaisquer réplicas. Essas sincronizações não podem ser executadas usando o comando sincronizar modelo no Portal.

### <a name="separate-processing-from-query-pool"></a>Processamento separado do pool de consultas

Para obter o desempenho máximo para operações de processamento e consulta, você pode optar por separar o servidor de processamento do pool de consulta. Quando separadas, novas conexões de cliente são atribuídas a réplicas de consulta somente no pool de consultas. Se as operações de processamento ocupam apenas um curto período de tempo, você pode optar por separar o servidor de processamento do pool de consulta apenas pelo tempo necessário para realizar operações de processamento e sincronização e, em seguida, incluí-lo novamente no pool de consultas. Ao separar o servidor de processamento do pool de consulta ou adicioná-lo de volta ao pool de consultas pode levar até cinco minutos para que a operação seja concluída.

## <a name="monitor-qpu-usage"></a>Monitorar o uso de QPU

Para determinar se a escala horizontal para o servidor é necessária, monitore o servidor em portal do Azure usando métricas. Se seu QPU regularmente maximizar, isso significa que o número de consultas em seus modelos está excedendo o limite de QPU para seu plano. A métrica de comprimento da fila de trabalhos do pool de consultas também aumenta quando o número de consultas na fila do pool de threads de consulta excede o QPU disponível. 

Outra boa métrica a ser observada é a média de QPU por ServerResourceType. Essa métrica compara o QPU médio para o servidor primário com o do pool de consulta. 

![Métricas de expansão de consulta](media/analysis-services-scale-out/aas-scale-out-monitor.png)

### <a name="to-configure-qpu-by-serverresourcetype"></a>Para configurar o QPU by ServerResourceType
1. Em um gráfico de linhas de métricas, clique em **Adicionar métrica**. 
2. Em **recurso**, selecione o servidor e, em seguida, em **namespace de métrica**, selecione **Analysis Services métricas padrão**e, em **métrica**, selecione **QPU**e, em **agregação**, selecione **Méd**. 
3. Clique em **aplicar divisão**. 
4. Em **valores**, selecione **ServerResourceType**.  

Para saber mais, consulte as [métricas do servidor de Monitorização](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurar o escalamento horizontal

### <a name="in-azure-portal"></a>Em portal do Azure

1. No portal, clique em **expansão**. Use o controle deslizante para selecionar o número de servidores de réplica de consulta. O número de réplicas que você escolher é além do servidor existente.  

2. Em **separar o servidor de processamento do pool de consulta**, selecione Sim para excluir o servidor de processamento dos servidores de consulta. [As conexões](#connections) de cliente que usam a cadeia de `:rw`conexão padrão (sem) são redirecionadas para réplicas no pool de consultas. 

   ![Controle deslizante de escala horizontal](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Clique em **salvar** para provisionar seus novos servidores de réplica de consulta. 

Ao configurar a expansão para um servidor na primeira vez, os modelos em seu servidor primário são sincronizados automaticamente com réplicas no pool de consultas. A sincronização automática ocorre apenas uma vez, quando você configura primeiro a expansão para uma ou mais réplicas. Alterações subsequentes no número de réplicas no mesmo servidor *não dispararão outra sincronização automática*. A sincronização automática não ocorrerá novamente mesmo que você defina o servidor como zero réplicas e, em seguida, redimensione horizontalmente para qualquer número de réplicas. 

## <a name="synchronize"></a>Sincronizar 

As operações de sincronização devem ser executadas manualmente ou usando a API REST.

### <a name="in-azure-portal"></a>Em portal do Azure

Em **visão geral** > modelo > **sincronizar modelo**.

![Controle deslizante de escala horizontal](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST

Use a operação de **sincronização** .

#### <a name="synchronize-a-model"></a>Sincronizar um modelo   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Obter status de sincronização  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Códigos de status de retorno:


|Código  |Descrição  |
|---------|---------|
|-1     |  inválido       |
|0     | A replicar        |
|1     |  Reidratar       |
|2     |   Concluído       |
|3     |   Com Falhas      |
|4     |    A finalizar     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de usar o PowerShell, [Instale ou atualize o módulo de Azure PowerShell mais recente](/powershell/azure/install-az-ps). 

Para executar a sincronização, use [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Para definir o número de réplicas de consulta, use [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o parâmetro `-ReadonlyReplicaCount` opcional.

Para separar o servidor de processamento do pool de consulta, use [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Especifique o parâmetro `-DefaultConnectionMode` opcional a ser `Readonly`usado.

Para saber mais, consulte [usando uma entidade de serviço com o módulo AZ. AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Ligações

Na página Visão geral do servidor, há dois nomes de servidor. Se você ainda não tiver configurado a expansão para um servidor, ambos os nomes de servidor funcionarão da mesma. Depois de configurar a expansão para um servidor, você precisa especificar o nome do servidor apropriado dependendo do tipo de conexão. 

Para conexões de cliente de usuário final como Power BI Desktop, Excel e aplicativos personalizados, use o **nome do servidor**. 

Para SSMS, SSDT e cadeias de conexão no PowerShell, aplicativos de funções do Azure e AMO, use o **nome do servidor de gerenciamento**. O nome do servidor de gerenciamento inclui `:rw` um qualificador (leitura-gravação) especial. Todas as operações de processamento ocorrem no servidor de gerenciamento (primário).

![Nomes de servidor](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up--down-vs-scale-out"></a>Escalar verticalmente vs. Aumentar horizontalmente

Você pode alterar o tipo de preço do servidor no servidor com várias réplicas. O mesmo tipo de preço se aplica a todas as réplicas. A operação escalar verticalmente e reduzir horizontalmente primeiro desativará todas as réplicas de uma vez e, em seguida, abrirá todas as réplicas no novo tipo de preço.

## <a name="troubleshoot"></a>Resolução de problemas

**Problema:** Os usuários obtêm erro **não é\<possível encontrar o servidor ' nome da instância do servidor > ' no modo de conexão ' ReadOnly '.**

**Soluções** Ao selecionar o **servidor de processamento separado da opção pool de consulta** , as conexões de cliente que usam a cadeia de conexão `:rw`padrão (sem) são redirecionadas para as réplicas do pool de consulta. Se as réplicas no pool de consultas ainda não estiverem online porque a sincronização ainda não foi concluída, as conexões de cliente redirecionadas poderão falhar. Para evitar conexões com falha, deve haver pelo menos dois servidores no pool de consultas ao executar uma sincronização. Cada servidor é sincronizado individualmente enquanto outros permanecem online. Se você optar por não ter o servidor de processamento no pool de consultas durante o processamento, poderá optar por removê-lo do pool para processamento e, em seguida, adicioná-lo novamente ao pool após a conclusão do processamento, mas antes da sincronização. Use as métricas de memória e QPU para monitorar o status de sincronização.



## <a name="related-information"></a>Informações relacionadas

[Monitorar métricas do servidor](analysis-services-monitor.md)   
[Gerenciar Azure Analysis Services](analysis-services-manage.md) 
