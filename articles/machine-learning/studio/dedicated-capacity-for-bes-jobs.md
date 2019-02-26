---
title: Serviço do Azure Batch para trabalhos de Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Descrição geral dos serviços do Azure Batch para trabalhos de Machine Learning. Processamento de conjunto do batch permite-lhe criar conjuntos de onde pode submeter tarefas de lote.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: 2b50eb449592337b70b09258357ffb763b11a1cd
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818447"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-jobs"></a>Serviço do Azure Batch para tarefas do Azure Machine Learning Studio

Processamento do Machine Learning Batch Pool fornece dimensionamento gerida pelo cliente para o serviço de execução de lote do Azure Machine Learning. O processamento de batch de clássico para aprendizagem ocorre num ambiente multi-inquilino, o que limita o número de tarefas simultâneas pode enviar e tarefas são colocados em fila numa base first-in-first-out. Essa incerteza significa que não é possível prever com precisão quando a tarefa será executada.

Processamento de conjunto do batch permite-lhe criar conjuntos de onde pode submeter tarefas de lote. Controla o tamanho do conjunto e, para que conjunto da tarefa é submetida. Seu trabalho BES é executado no seu próprio espaço de processamento de fornecer o desempenho do processamento previsível e a capacidade de criar agrupamentos de recursos que correspondem a carga de processamento que submeter.

> [!NOTE]
> Tem de ter um novo Gerenciador de recursos com base em Machine Learning web service para criar um conjunto. Depois de criado, pode executar serviço da web, os dois novos do Resource Manager clássico e com base no conjunto para qualquer BES.

## <a name="how-to-use-batch-pool-processing"></a>Como utilizar o processamento do conjunto do Batch

Configuração de processamento de conjunto do Batch não está atualmente disponível através do portal do Azure. Para utilizar o processamento do conjunto do Batch, tem de:

-   Chamar o CSS para criar uma conta do conjunto de Batch e obter um URL de serviço do conjunto e uma chave de autorização
-   Criar um serviço da web baseados no Resource Manager novo e o plano de faturação

Para criar a sua conta, chamar o serviço de cliente da Microsoft e suporte (CSS) e fornecer sua ID de subscrição. CSS irá trabalhar para determinar a capacidade adequada para o seu cenário. CSS, em seguida, configura a sua conta com o número máximo de conjuntos que pode criar e o número máximo de máquinas virtuais (VMs) que podem ser colocados em cada agrupamento. Assim que a sua conta está configurada, são fornecidos o URL do serviço de agrupamento e uma chave de autorização.

Depois de criar a sua conta, utilize a chave de URL do serviço de agrupamento e a autorização para efetuar operações de gestão de agrupamento no seu conjunto de Batch.

![Arquitetura de serviço do batch pool.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Criar conjuntos ao chamar a operação Criar conjunto acerca do URL de serviço do conjunto CSS fornecido a. Quando cria um conjunto, especifique o número de VMs e o URL do swagger de um Gerenciador de recursos novo com base em serviço web Machine Learning. Esse web service é fornecido para estabelecer a associação de faturação. O serviço do conjunto do Batch utiliza o swagger para associar o agrupamento de um plano de faturação. Pode executar qualquer BES serviço da web, os dois novos do Resource Manager clássico e com base no conjunto.

Pode usar qualquer serviço da web baseados no Resource Manager novas, mas lembre-se de que a faturação para as tarefas são cobradas conforme o plano de faturação associado a esse serviço. Pode querer criar um serviço web e um novo plano de faturação especificamente para executar tarefas de conjunto do Batch.

Para obter mais informações sobre a criação de serviços da web, consulte [implementar um serviço web do Azure Machine Learning](publish-a-machine-learning-web-service.md).

Depois de criar um conjunto, submeter a tarefa de BES utilizando o URL de pedidos de lote para o serviço web. Pode optar por enviá-lo a um conjunto de ou para processamento em lotes clássico. Para submeter uma tarefa de processamento de conjunto do Batch, adicione o seguinte parâmetro ao corpo de pedido de submissão da tarefa:

"AzureBatchPoolId": "&lt;ID do agrupamento&gt;"

Se não adicionar o parâmetro, a tarefa é executada no ambiente do processo de lote clássico. Se o conjunto tiver recursos disponíveis, a tarefa começa imediatamente a executar. Se o conjunto não tem recursos gratuitos, seu trabalho é colocada na fila até que um recurso esteja disponível.

Se achar que regularmente atingir a capacidade dos conjuntos e precisar de capacidade aumentada, pode chamar o CSS e trabalhar com um representante para aumentar as suas quotas.

Pedido de exemplo:

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Considerações ao utilizar o processamento do conjunto do Batch

Processamento de conjunto do batch é um serviço de cobrar sempre ativa e que requer a associá-lo a um plano de faturação baseados no Resource Manager. É-lhe cobrada apenas para o número de horas de computação que do conjunto estiver em execução; independentemente do número de tarefas são executadas durante esse conjunto de tempo. Se criar um conjunto, é-lhe cobrada as horas de computação de cada máquina virtual no conjunto até que o conjunto ser eliminado, mesmo se não existem tarefas de lote estão em execução no conjunto. A faturação para as máquinas virtuais é iniciado quando tiver terminado de aprovisionamento e termina quando eles tem sido eliminados. Pode usar qualquer um dos planos encontrados no [página de preços do Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Exemplo de faturação:

Se criar um conjunto do Batch com 2 máquinas virtuais e eliminá-la após 24 horas o seu plano de faturação é debitado 48 horas de computação; independentemente de quantas tarefas foram executados durante esse período.

Se cria um conjunto do Batch com 4 máquinas virtuais e eliminá-la após 12 horas, seu plano de faturação também é debitado 48 horas de computação.

Recomendamos que consultar o estado da tarefa para determinar quando conclusão das tarefas. Quando tem concluído a todas as suas tarefas em execução, chame a operação de redimensionamento de conjunto para definir o número de máquinas virtuais no conjunto a zero. Se for curta nos recursos de agrupamento e terá de criar um novo conjunto, por exemplo, para efetuar a cobrança em relação a um plano de faturação, pode eliminar o conjunto em vez disso, quando terminar de todas as suas tarefas em execução.


| **Utilizar conjunto do Batch quando a processar**    | **Utilizar o batch clássico processamento quando**  |
|---|---|
|Tem de executar um grande número de tarefas<br>Ou<br/>Precisa saber que as tarefas serão executadas imediatamente<br/>Ou<br/>Precisa de débito garantido. Por exemplo, terá de executar um número de tarefas num determinado intervalo de tempo e quer aumentar horizontalmente os recursos de computação para satisfazer as necessidades.    | Estiver a executar apenas algumas tarefas<br/>E<br/> Não precisa de trabalhos para execução imediatamente |
