---
title: Criar tempo de execução de integração do Azure no Azure Data Factory
description: Saiba como criar o tempo de execução de integração do Azure no Azure Data Factory, que é usado para copiar dados e distribuir atividades de transformação.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 87633abaaae1f6034709c6e552be6647533115ec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440300"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Como criar e configurar Azure Integration Runtime
A Integration Runtime (IR) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer recursos de integração de dados em diferentes ambientes de rede. Para obter mais informações sobre o IR, consulte [Integration Runtime](concepts-integration-runtime.md).

O Azure IR fornece uma computação totalmente gerenciada para executar nativamente a movimentação de dados e a distribuição de atividades de transformação de dados para serviços de computação como o HDInsight. Ele é hospedado no ambiente do Azure e dá suporte à conexão a recursos no ambiente de rede pública com pontos de extremidade acessíveis públicos.

Este documento apresenta como você pode criar e configurar Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Azure IR padrão
Por padrão, cada data factory tem um Azure IR no back-end que dá suporte a operações em armazenamentos de dados de nuvem e serviços de computação na rede pública. O local do Azure IR é resolver automaticamente. Se a propriedade **connectVia** não for especificada na definição de serviço vinculado, o Azure ir padrão será usado. Você só precisa criar explicitamente um Azure IR quando desejar definir explicitamente o local do IR ou se desejar agrupar virtualmente as execuções de atividade em diferentes IRs para fins de gerenciamento. 

## <a name="create-azure-ir"></a>Criar Azure IR
Integration Runtime pode ser criado usando o cmdlet do PowerShell **set-AzDataFactoryV2IntegrationRuntime** . Para criar um Azure IR, especifique o nome, o local e o tipo para o comando. Aqui está um comando de exemplo para criar um Azure IR com o local definido como "Europa Ocidental":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Para Azure IR, o tipo deve ser definido como **gerenciado**. Você não precisa especificar detalhes de computação porque ele é totalmente gerenciado de forma elástica na nuvem. Especifique detalhes de computação, como o tamanho do nó e a contagem de nós, quando você deseja criar Azure-SSIS IR. Para obter mais informações, consulte [criar e configurar Azure-SSIS ir](create-azure-ssis-integration-runtime.md).

Você pode configurar um Azure IR existente para alterar seu local usando o cmdlet do PowerShell Set-AzDataFactoryV2IntegrationRuntime. Para obter mais informações sobre o local de um Azure IR, consulte [introdução ao Integration Runtime](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Usar Azure IR

Depois que um Azure IR for criado, você poderá referenciá-lo em sua definição de serviço vinculado. Veja abaixo um exemplo de como você pode referenciar o Azure Integration Runtime criado acima de um serviço vinculado do armazenamento do Azure:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos sobre como criar outros tipos de tempos de execução de integração:

- [Create self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Criar o runtime de integração autoalojado)
- [Criar tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md)
 
