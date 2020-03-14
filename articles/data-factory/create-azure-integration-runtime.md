---
title: Criar o tempo de execução da integração Azure na Fábrica de Dados Azure
description: Saiba como criar o tempo de funcionação da integração Azure na Azure Data Factory, que é usada para copiar dados e despachar atividades de transformação.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260764"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Como criar e configurar o Tempo de Funcionação de Integração Azure
O Tempo de Integração (IR) é a infraestrutura computacional utilizada pela Azure Data Factory para fornecer capacidades de integração de dados em diferentes ambientes de rede. Para mais informações sobre o IR, consulte o tempo de [execução da Integração.](concepts-integration-runtime.md)

O Azure IR fornece uma computação totalmente gerida para realizar de forma nativa o movimento de dados e enviar atividades de transformação de dados para calcular serviços como o HDInsight. Está alojado em ambiente Azure e apoia a ligação aos recursos em ambiente de rede pública com pontos finais acessíveis ao público.

Este documento introduz como pode criar e configurar o Tempo de Funcionação de Integração Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Default Azure IR
Por padrão, cada fábrica de dados tem um IR Azure no backend que suporta operações em lojas de dados em nuvem e serviços de computação em rede pública. A localização do Azure IR é auto-resolvida. Se a **propriedade connectVia** não estiver especificada na definição de serviço ligada, o Inete padrão é utilizado. Basta criar explicitamente um IR Azure quando quiser definir explicitamente a localização do IR, ou se quiser praticamente agrupar as execuções de atividade em diferentes IRs para fins de gestão. 

## <a name="create-azure-ir"></a>Criar o Azure IR
Integração O tempo de execução pode ser criado utilizando o **set-AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet. Para criar um IR Azure, especifique o nome, localização e tipo ao comando. Aqui está um comando de amostra para criar um IR Azure com localização definida para "Europa Ocidental":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Para o Azure IR, o tipo deve ser definido para **gerido**. Não é necessário especificar detalhes da computação porque é totalmente gerido elástico na nuvem. Especifique detalhes computacionais como o tamanho do nó e a contagem do nó quando quiser criar o Ir Azure-SSIS. Para mais informações, consulte [Create and Configure Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Pode configurar um Ir Azure existente para alterar a sua localização utilizando o Set-AzDataFactoryV2IntegrationTimePowerShell cmdlet. Para obter mais informações sobre a localização de um IR Azure, consulte Introdução ao tempo de execução da [integração.](concepts-integration-runtime.md)

## <a name="use-azure-ir"></a>Utilizar o Azure IR

Uma vez criado um IR Azure, pode referenciar na definição do Serviço Linked. Abaixo está uma amostra de como pode fazer referência ao Tempo de Funcionação de Integração Azure criado acima a partir de um Serviço Ligado ao Armazenamento Azure:  

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
Consulte os seguintes artigos sobre como criar outros tipos de tempos de integração:

- [Criar um integration runtime autoalojado](create-self-hosted-integration-runtime.md)
- [Criar o tempo de execução da integração Azure-SSIS](create-azure-ssis-integration-runtime.md)
 
