---
title: Criar tempo de integração do Azure na Azure Data Factory
description: Saiba como criar o tempo de integração do Azure na Azure Data Factory, que é usada para copiar dados e enviar atividades de transformação.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/09/2020
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: c7880fd7fb687483409ce591059e0f5b2d2e2991
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659706"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Como criar e configurar o tempo de execução da integração do Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O Tempo de Integração (IR) é a infraestrutura de computação utilizada pela Azure Data Factory para fornecer capacidades de integração de dados em diferentes ambientes de rede. Para obter mais informações sobre o IR, consulte [o tempo de execução da Integração.](concepts-integration-runtime.md)

O Azure IR fornece um cálculo totalmente gerido para realizar atividades de movimento de dados e despachar atividades de transformação de dados para serviços de computação como o HDInsight. Está hospedado em ambiente Azure e apoia a ligação a recursos em ambiente de rede pública com pontos finais acessíveis ao público.

Este documento introduz como pode criar e configurar o Tempo de Execução da Integração Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Azure IR padrão
Por padrão, cada fábrica de dados tem um Azure IR no backend que suporta operações em lojas de dados em nuvem e serviços de computação na rede pública. A localização do Azure IR é a autoresolve. Se a propriedade **connectVia** não for especificada na definição de serviço ligada, o Azure IR predefinido é utilizado. Só precisa de criar explicitamente um Azure IR quando pretender definir explicitamente a localização do IR, ou se pretender agrupar virtualmente as execuções de atividade em diferentes IRs para fins de gestão. 

## <a name="create-azure-ir"></a>Criar Azure IR

Para criar e configurar um Azure IR, pode utilizar os seguintes procedimentos.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Criar um Azure IR via Azure PowerShell
O tempo de execução de integração pode ser criado utilizando o cmdlet PowerShell do **Set-AzDataFactoryV2IntegrationRuntime.** Para criar um Azure IR, especifique o nome, localização e digite para o comando. Aqui está um comando de amostra para criar um Azure IR com localização definida para "Europa Ocidental":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Para O Azure IR, o tipo deve ser definido para **Managed**. Não precisa de especificar detalhes do cálculo porque é totalmente gerido elásticamente em nuvem. Especifique detalhes do cálculo como o tamanho do nó e a contagem de nós quando pretende criar O Azure-SSIS IR. Para obter mais informações, consulte [Create and Configure Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Pode configurar um Azure IR existente para alterar a sua localização utilizando o cmdlet PowerShell set-AzDataFactoryV2IntegrationRuntime. Para obter mais informações sobre a localização de um Azure IR, consulte [Introdução ao tempo de execução da integração.](concepts-integration-runtime.md)

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Criar um Azure IR via Azure Data Factory UI
Utilize os seguintes passos para criar um Azure IR utilizando uI Azure Data Factory.

1. Na página **Let's get start** of Azure Data Factory UI, selecione o [separador Gerir](https://docs.microsoft.com/azure/data-factory/author-management-hub) a partir do painel mais à esquerda.

   ![O botão de gerir a página inicial](media/doc-common-process/get-started-page-manage-button.png)

1. Selecione **os tempos de integração** no painel esquerdo e, em seguida, selecione **+New**.

   ![Criar um integration runtime](media/doc-common-process/manage-new-integration-runtime.png)

1. Na página de configuração do tempo de execução da **Integração,** selecione **Azure, Self-Hosted**e, em seguida, selecione **Continue**. 

1. Na página seguinte, selecione **Azure** para criar um Azure IR e, em seguida, **selecione Continue**.
   ![Criar um integration runtime](media/create-azure-integration-runtime/new-azure-integration-runtime.png)

1. Insira um nome para o seu Azure IR e selecione **Criar**.
   ![Criar um Azure IR](media/create-azure-integration-runtime/create-azure-integration-runtime.png)

1. Verá uma notificação pop-up quando a criação terminar. Na página de **tempos de execução da Integração,** certifique-se de que vê o recém-criado IR na lista.

## <a name="use-azure-ir"></a>Use Azure IR

Uma vez criado um Azure IR, pode fazê-lo referenciar na definição de Serviço Linked. Abaixo está uma amostra de como você pode referenciar o Tempo de Execução de Integração Azure criado acima a partir de um Serviço Ligado ao Armazenamento Azure:

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

## <a name="next-steps"></a>Próximos passos
Consulte os seguintes artigos sobre como criar outros tipos de períodos de integração:

- [Create self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Criar o runtime de integração autoalojado)
- [Criar o runtime de integração de SSIS do Azure](create-azure-ssis-integration-runtime.md)
 
