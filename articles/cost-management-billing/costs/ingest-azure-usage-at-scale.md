---
title: Recuperar conjuntos de dados de grandes custos recorrentemente com exportações
description: Este artigo ajuda-o a exportar regularmente grandes quantidades de dados com exportações da Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 465225341bdffc984ac6cbc82ba94eb656ad60df
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509701"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Recuperar conjuntos de dados de grandes custos recorrentemente com exportações

Este artigo ajuda-o a exportar regularmente grandes quantidades de dados com exportações da Azure Cost Management. A exportação é o método recomendado para a obtenção de dados de custo não agregados. Em especial quando os ficheiros de utilização são demasiado grandes para serem chamados e transferidos de forma fiável com a API de Detalhes de Utilização. Os dados exportados são colocados na conta de Armazenamento do Azure escolhida por si. A partir daí, pode carregá-los nos seus próprios sistemas e analisá-los conforme necessário. Para configurar as exportações no portal do Azure, veja [Exportar dados](tutorial-export-acm-data.md).

Se pretender automatizar as exportações em vários âmbitos, o exemplo de pedido de API apresentado na secção seguinte é um bom ponto de partida. Pode utilizar a API de Exportações para criar exportações automáticas como parte da configuração do ambiente geral. As exportações automáticas ajudam a garantir que tem os dados de que necessita. Pode utilizá-los nos sistemas da sua própria organização à medida que for expandindo a utilização do Azure.

## <a name="common-export-configurations"></a>Configurações de exportação comuns

Antes de criar a sua primeira exportação, reflita sobre o seu cenário e as opções de configuração de que necessita para o tornar possível. Considere as seguintes opções de exportação:

- **Periodicidade** – Determina a frequência com que a tarefa de exportação é executada quando um ficheiro é colocado na sua conta de Armazenamento do Azure. Escolha entre Diariamente, Semanalmente e Mensalmente. Tente configurar a periodicidade de modo a corresponder às tarefas de importação de dados utilizadas pelo sistema interno da sua organização.
- **Período de Periodicidade** – Determina o período de validade da Exportação. Os ficheiros só são exportados durante o período de periodicidade.
- **Intervalo de Tempo** – Determina o volume de dados gerado pela exportação numa determinada execução. As opções comuns são MonthToDate e WeekToDate.
- **StartDate** – Configura o início da agenda de exportações pretendido. É criada uma exportação na data de início (StartDate) e, mais tarde, com base na Periodicidade escolhida.
- **Tipo** – Existem três tipos de exportação:
  - ActualCost – Mostra o total da utilização e dos custos para o período especificado, à medida que os valores vão sendo acumulados, e apresenta-o na sua fatura.
  - AmortizedCost – Mostra o total da utilização e dos custos para o período especificado, com a amortização aplicada aos custos de compra de reserva aplicáveis.
  - Utilização – Todas as exportações criadas antes de 20 de julho de 2020 são do tipo Utilização. Atualize todas as exportações agendadas como ActualCost ou AmortizedCost.
- **Colunas** – Define os campos de dados que pretende ver incluídos no ficheiro de exportação. Coincidem com os campos disponíveis na API de Detalhes de Utilização. Para obter mais informações, consulte [API de Detalhes de Utilização](/rest/api/consumption/usagedetails/list).

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Criar uma exportação do mês acumulada até hoje diária para uma subscrição

URL do Pedido: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>Copiar grandes bolhas de armazenamento Azure

Pode utilizar a Cost Management para agendar as exportações dos seus dados de utilização Azure nas suas contas de Armazenamento Azure como bolhas. Os tamanhos de bolhas resultantes podem ter mais de gigabytes de tamanho. A equipa de Gestão de Custos da Azure trabalhou com a equipa de armazenamento Azure para testar a cópia de grandes bolhas de armazenamento Azure. Os resultados estão documentados nas seguintes secções. Pode esperar ter resultados semelhantes à medida que copia bolhas de armazenamento de uma região de Azure para outra.

Para testar o seu desempenho, a equipa transferiu bolhas de contas de armazenamento na região oeste dos EUA para as mesmas regiões e outras regiões. A equipa mediu velocidades que variavam entre 2 GB por segundo na mesma região e 150 MB por segundo para contas de armazenamento na região do Sudeste Asiático.

### <a name="test-configuration"></a>Configuração do teste

Para medir as velocidades de transferência de blob, a equipa criou uma aplicação simples de consola .NET que refere a versão mais recente (v2.0.1) da Azure Data Movement Library (DLM) via NuGet. A DLM é uma SDK fornecida pela equipa de Armazenamento Azure que facilita o acesso programático aos seus serviços de transferência. Em seguida, criaram contas de armazenamento Standard V2 em várias regiões e usam o Oeste dos EUA como a região de origem. Povoaram as contas de armazenamento lá com contentores, onde cada um tinha dez bolhas de bloco de 2 GB. Copiaram os contentores para outras contas de armazenamento utilizando o método _TransferManager.CopyDirectoryAsync da_ DLM com a opção _CopyMethod.ServiceSideSyncCopy._ Os testes foram realizados num computador que executa o Windows 10 com 12 núcleos e uma rede de 1-GbE.

Definições de aplicação utilizadas:

- _TransferManager.Configurations. ParallelOperations_  =  _Environment.ProcessorCount \* 32_. A equipa achou que o cenário tinha mais efeito na produção geral. Um valor de 32 vezes o número de núcleos forneceu a melhor produção para o cliente de teste.
- _ServicePointManager.DefaultConnectionLimit = int. MaxValue._ Defini-lo para um valor máximo efetivamente passa o controlo total do paralelismo de transferência para a _definição de Operações Paralelas_ acima.
- _TransferManager.Configurations. Blocos = 4.194.304_. Teve algum efeito nas taxas de transferência com 4 MB, provando ser o melhor para os testes.

Para obter mais informações e código de amostra, consulte links na secção [Etapas Seguintes.](#next-steps)

### <a name="test-results"></a>Resultados do teste

| **Número do teste** | **Para a região** | **Blobs** | **Tempo (segs)** | **MB/s** | **Comentários** |
| --- | --- | --- | --- | --- | --- |
| 1 | Westus | 2 GB x 10 | 10 | 2.000 |   |
| 2 | Westus2 | 2 GB x 10 | 33 | 600 |   |
| 3 | Rio Eastus | 2 GB x 10 | 67 | 300 |   |
| 4 | Rio Eastus | 2 GB x 10 x 4 | 99 | 200 | 4 transferências paralelas utilizando 8 contas de armazenamento: 4 Oeste a 4 Média Leste por transferência |
| 6 | Rio Eastus | 2 GB x 10 x 4 | 92 | 870 | 4 transferências paralelas de 1 conta de armazenamento para outra |
| 5 | Rio Eastus | 2G x 10 x 8 | 148 | 135 | 8 transferências paralelas utilizando 8 contas de armazenamento: 4 Oeste a 4x2 Média leste por transferência |
| 7 | SE Ásia | 2 GB x 10 | 133 | 150 |   |
| 8 | SE Ásia | 2 GB x 10 x 4 | 444 | 180 | 4 transferências paralelas de 1 conta de armazenamento para outra |

### <a name="sync-transfer-characteristics"></a>Características de transferência de sincronização

Estas são algumas das características da transferência de sincronização do lado do serviço utilizada com a DML que são relevantes para a sua utilização:

- A DML pode transferir uma única bolha ou um diretório. Para transferência de diretório, pode utilizar um padrão de pesquisa para combinar com o prefixo blob.
- As transferências de blob de bloco acontecem em paralelo. Tudo completo no final do processo de transferência. Os blocos de bolhas individuais são transferidos em paralelo.
- A transferência é executada assíncroticamente no cliente. O estado de transferência está disponível periodicamente através de uma chamada para um método que pode ser definido num objeto _TransferContext._
- A transferência cria pontos de verificação durante o seu progresso e expõe um objeto _TransferCheckpoint._ O objeto representa o último ponto de verificação através do objeto _TransferContext._ Se o _TransferCheckpoint_ for guardado antes de uma transferência ser cancelada/abortada, a transferência pode ser retomada a partir do ponto de verificação por um máximo de sete dias. A transferência pode ser retomada a partir de qualquer posto de controlo, não apenas o mais recente.
- Se o processo do cliente de transferência for morto e reiniciado sem implementar a função de ponto de verificação.
  - Antes de quaisquer transferências blob terem sido concluídas, a transferência reinicia.
  - Depois de concluídas algumas das bolhas, a transferência reinicia apenas para as bolhas incompletas.
- Fazer uma pausa na execução do cliente faz uma pausa nas transferências.
- A funcionalidade de transferência de bolhas abstrata o cliente de falhas transitórias. Por exemplo, o estrangulamento da conta de armazenamento não normalmente causará a falha de uma transferência, mas irá atrasar a transferência.
- As transferências do lado do serviço têm baixo uso de recursos do cliente para CPU e memória, alguma largura de banda de rede e conexões.

### <a name="async-transfer-characteristics"></a>Características de transferência de async

Pode invocar o método _TransferManager.CopyDirectoryAsync()_ com a opção _CopyMethod.ServiceSideAsyncCopy._ Opera de forma semelhante ao mecanismo de transferência de sincronização do ponto de vista do cliente, mas com as seguintes diferenças de funcionamento:

- As taxas de transferência são muito mais lentas do que a transferência de sincronização equivalente (normalmente 10 MB/s ou menos).
- A transferência continua mesmo que o processo do cliente termine.
- Embora os pontos de verificação sejam suportados, o reinício de uma transferência utilizando um _TransferCheckpoint_ não será retomado no momento do checkpoint, mas no estado atual da transferência.

### <a name="test-summary"></a>Resumo do teste

O armazenamento de blob Azure suporta altas taxas de transferência globais com a sua funcionalidade de transferência de sincronização do lado do serviço. A utilização da funcionalidade em aplicações .NET é simples utilizando a Biblioteca de Movimentos de Dados. É possível que as exportações de Cost Management copiem de forma fiável centenas de gigabytes de dados para uma conta de armazenamento em qualquer lugar em menos de uma hora.

## <a name="next-steps"></a>Passos seguintes

- Consulte a fonte da [Microsoft Azure Storage Data Movement Library.](https://github.com/Azure/azure-storage-net-data-movement)
- [Transferir dados com a biblioteca do Movimento de Dados.](../../storage/common/storage-use-data-movement-library.md)
- Consulte a amostra de origem da [amostra AzureDmlBackup.](https://github.com/markjbrown/AzureDmlBackup)
- Leia [High-Throughput com Armazenamento Azure Blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage).