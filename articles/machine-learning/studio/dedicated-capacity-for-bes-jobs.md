---
title: Trabalhos do serviço de lote do Azure
titleSuffix: ML Studio (classic) - Azure
description: Visão geral dos serviços do lote do Azure para trabalhos Machine Learning Studio (clássico). O processamento do pool do lote permite que você crie pools nos quais você pode enviar trabalhos em lotes.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio (classic) | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: ad3ae99b04d5386172c9598f35cbe7d64fddcd36
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837696"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-classic-jobs"></a>Serviço do lote do Azure para trabalhos Azure Machine Learning Studio (clássico)

O processamento do pool do lote Machine Learning fornece escala gerenciada pelo cliente para o serviço de execução do Azure Machine Learning Batch. O processamento em lotes clássico para o aprendizado de máquina ocorre em um ambiente multilocatário, que limita o número de trabalhos simultâneos que você pode enviar e que os trabalhos são enfileirados em uma base primeiro a entrar, primeiro a sair. Essa incerteza significa que você não pode prever com precisão quando seu trabalho será executado.

O processamento do pool do lote permite que você crie pools nos quais você pode enviar trabalhos em lotes. Você controla o tamanho do pool e para qual pool o trabalho é enviado. Seu trabalho BES é executado em seu próprio espaço de processamento, fornecendo desempenho de processamento previsível e a capacidade de criar pools de recursos que correspondam à carga de processamento que você envia.

> [!NOTE]
> Você deve ter um novo Gerenciador de recursos baseado Machine Learning serviço Web para criar um pool. Depois de criado, você pode executar qualquer serviço Web BES, o novo Resource Manager baseado e o clássico, no pool.

## <a name="how-to-use-batch-pool-processing"></a>Como usar o processamento de pool do lote

A configuração do processamento do pool do lote não está disponível no momento por meio do portal do Azure. Para usar o processamento do pool do lote, você deve:

-   Chamar CSS para criar uma conta de pool do lote e obter uma URL de serviço do pool e uma chave de autorização
-   Criar um novo serviço Web baseado no Resource Manager e um plano de cobrança

Para criar sua conta, ligue para o serviço de suporte e atendimento ao cliente da Microsoft (CSS) e forneça sua ID de assinatura. O CSS funcionará com você para determinar a capacidade apropriada para seu cenário. Em seguida, o CSS configura sua conta com o número máximo de pools que você pode criar e o número máximo de VMs (máquinas virtuais) que você pode inserir em cada pool. Quando sua conta estiver configurada, você receberá a URL do serviço do pool e uma chave de autorização.

Depois que sua conta for criada, use a URL do serviço do pool e a chave de autorização para executar operações de gerenciamento de pool em seu pool do lote.

![Arquitetura do serviço do pool do lote.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Crie pools chamando a operação criar pool na URL do serviço de pool que o CSS forneceu a você. Quando você cria um pool, especifique o número de VMs e a URL do Swagger. JSON de um novo Gerenciador de recursos com base Machine Learning serviço Web. Este serviço Web é fornecido para estabelecer a associação de cobrança. O serviço de pool do lote usa o Swagger. JSON para associar o pool a um plano de cobrança. Você pode executar qualquer serviço Web BES, o novo Resource Manager baseado e o clássico, no pool.

Você pode usar qualquer serviço Web baseado no novo Resource Manager, mas lembre-se de que a cobrança dos trabalhos é cobrada em relação ao plano de cobrança associado a esse serviço. Talvez você queira criar um serviço Web e um novo plano de cobrança especificamente para executar trabalhos de pool do lote.

Para obter mais informações sobre como criar serviços Web, consulte [implantar um serviço web Azure Machine Learning](deploy-a-machine-learning-web-service.md).

Depois de criar um pool, você envia o trabalho BES usando a URL de solicitações em lote para o serviço Web. Você pode optar por enviá-lo para um pool ou para o processamento em lotes clássico. Para enviar um trabalho para o processamento do pool do lote, adicione o seguinte parâmetro ao corpo da solicitação de envio do trabalho:

"AzureBatchPoolId": "&lt;ID do pool&gt;"

Se você não adicionar o parâmetro, o trabalho será executado no ambiente de processo em lote clássico. Se o pool tiver recursos disponíveis, o trabalho começará a ser executado imediatamente. Se o pool não tiver recursos livres, seu trabalho será colocado na fila até que um recurso esteja disponível.

Se você achar que está acessando regularmente a capacidade de seus pools e precisa de maior capacidade, você pode chamar o CSS e trabalhar com um representante para aumentar suas cotas.

Exemplo de solicitação:

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

## <a name="considerations-when-using-batch-pool-processing"></a>Considerações ao usar o processamento do pool do lote

O processamento do pool do lote é um serviço Faturável sempre ativa e requer que você o associe a um plano de cobrança baseado no Resource Manager. Você será cobrado apenas pelo número de horas de computação em execução no pool; independentemente do número de trabalhos executados durante esse pool de tempo. Se você criar um pool, será cobrado pelas horas de computação de cada máquina virtual no pool até que o pool seja excluído, mesmo que nenhum trabalho em lotes esteja em execução no pool. A cobrança pelas máquinas virtuais é iniciada quando o provisionamento é concluído e para quando elas são excluídas. Você pode usar qualquer um dos planos encontrados na [página de preços do Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Exemplo de cobrança:

Se você criar um pool do lote com duas máquinas virtuais e excluí-la após 24 horas, o plano de cobrança será debitado 48 horas de computação; independentemente de quantos trabalhos foram executados durante esse período.

Se você criar um pool do lote com 4 máquinas virtuais e excluí-la após 12 horas, seu plano de cobrança também será debitado em 48 horas de computação.

Recomendamos que você monitore o status do trabalho para determinar quando os trabalhos são concluídos. Quando todos os trabalhos terminarem de ser executados, chame a operação redimensionar pool para definir o número de máquinas virtuais no pool como zero. Se você tiver recursos de pool curtos e precisar criar um novo pool, por exemplo, para cobrar em um plano de cobrança diferente, você poderá excluir o pool em vez de concluir a execução de todos os trabalhos.


| **Use o processamento do pool do lote quando**    | **Use o processamento em lote clássico quando**  |
|---|---|
|Você precisa executar um grande número de trabalhos<br>Ou<br/>Você precisa saber que seus trabalhos serão executados imediatamente<br/>Ou<br/>Você precisa de taxa de transferência garantida. Por exemplo, você precisa executar vários trabalhos em um determinado intervalo de tempo e deseja escalar horizontalmente seus recursos de computação para atender às suas necessidades.    | Você está executando apenas alguns trabalhos<br/>e<br/> Você não precisa que os trabalhos sejam executados imediatamente |
