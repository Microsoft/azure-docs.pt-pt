---
title: Criar runtime de integração do Azure no Azure Data Factory | Documentos da Microsoft
description: Saiba como criar o runtime de integração do Azure no Azure Data Factory, que é utilizada para copiar dados e atividades de transformação de expedição.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: douglasl
ms.openlocfilehash: 93777a3d9ecbb6c2749594985494975f61cc1948
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455705"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Como criar e configurar o Runtime de integração do Azure
O Runtime de integração (IR) é a infraestrutura de computação utilizada pelo Azure Data Factory para fornecer capacidades de integração de dados em diferentes ambientes de rede. Para obter mais informações sobre o runtime de integração, consulte [runtime de integração](concepts-integration-runtime.md).

Runtime de integração do Azure fornece uma computação totalmente gerida para executar nativamente dados movimento e enviar dados atividades de transformação para computar os serviços, como o HDInsight. Ele está hospedado no ambiente do Azure e suporta a ligação a recursos no ambiente de rede pública com pontos finais públicos acessíveis.

Este documento apresenta como pode criar e configurar o Runtime de integração do Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Padrão do Runtime de integração do Azure
Por predefinição, cada fábrica de dados tem um runtime de integração no back-end que oferece suporte a operações na cloud, arquivos de dados e serviços na rede pública de computação. A localização desse Runtime de integração do Azure é resolver automaticamente. Se **connectVia** propriedade não for especificada na definição do serviço ligado, a predefinição é utilizado o runtime de integração do Azure. Apenas terá de criar explicitamente um runtime de integração do Azure quando quiser definir explicitamente a localização do IR de, ou se quiser praticamente as execuções de atividade no IRs diferentes para fins de gestão de grupo. 

## <a name="create-azure-ir"></a>Criar Runtime de integração do Azure
Runtime de integração pode ser criado com o **Set-AzDataFactoryV2IntegrationRuntime** cmdlet do PowerShell. Para criar um runtime de integração do Azure, especifique o nome, a localização e o tipo para o comando. Eis um comando de exemplo para criar um runtime de integração do Azure com a localização definida como "Na Europa Ocidental":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Para o runtime de integração do Azure, o tipo tem de ser definido **gerida**. Não é necessário especificar detalhes de computação, porque está totalmente gerido elástica na cloud. Especifique a computação detalha como o tamanho do nó e o nó de contagem de quando gostaria de criar o ir Azure-SSIS. Para obter mais informações, consulte [criar e configurar o runtime de integração Azure-SSIS](create-azure-ssis-integration-runtime.md).

Pode configurar um IR do Azure existente para alterar a localização com o cmdlet Set-AzDataFactoryV2IntegrationRuntime PowerShell. Para obter mais informações sobre o local de um IR do Azure, consulte [introdução ao runtime de integração](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Utilize o runtime de integração do Azure

Depois de criar um runtime de integração do Azure, pode referenciá-lo na sua definição de serviço ligado. Abaixo é um exemplo de como pode referenciar o Runtime de integração do Azure criado acima de um serviço ligado de armazenamento do Azure:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos sobre como criar outros tipos de runtimes de integração:

- [Criar um integration runtime autoalojado](create-self-hosted-integration-runtime.md)
- [Criar runtime de integração Azure-SSIS](create-azure-ssis-integration-runtime.md)
 
