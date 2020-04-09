---
title: Criar o tempo de execução da integração Azure na Fábrica de Dados Azure
description: Saiba como criar o tempo de funcionação da integração Azure na Azure Data Factory, que é usada para copiar dados e despachar atividades de transformação.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/13/2020
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: cf3bb7e6733ef55a85d0b4ae26a4ce05059a8fb9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887171"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Como criar e configurar o Tempo de Funcionação de Integração Azure
O Tempo de Integração (IR) é a infraestrutura computacional utilizada pela Azure Data Factory para fornecer capacidades de integração de dados em diferentes ambientes de rede. Para mais informações sobre o IR, consulte o tempo de [execução da Integração.](concepts-integration-runtime.md)

O Azure IR fornece uma computação totalmente gerida para realizar de forma nativa o movimento de dados e enviar atividades de transformação de dados para calcular serviços como o HDInsight. Está alojado em ambiente Azure e apoia a ligação aos recursos em ambiente de rede pública com pontos finais acessíveis ao público.

Este documento introduz como pode criar e configurar o Tempo de Funcionação de Integração Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Default Azure IR
Por padrão, cada fábrica de dados tem um IR Azure no backend que suporta operações em lojas de dados em nuvem e serviços de computação em rede pública. A localização do Azure IR é auto-resolvida. Se a **propriedade connectVia** não estiver especificada na definição de serviço ligada, o Inete padrão é utilizado. Basta criar explicitamente um IR Azure quando quiser definir explicitamente a localização do IR, ou se quiser praticamente agrupar as execuções de atividade em diferentes IRs para fins de gestão. 

## <a name="create-azure-ir"></a>Criar o Azure IR

Para criar e configurar um IR Azure, pode utilizar os seguintes procedimentos.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Criar um IR Azure via Azure PowerShell
Integração O tempo de execução pode ser criado utilizando o **set-AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet. Para criar um IR Azure, especifique o nome, localização e tipo ao comando. Aqui está um comando de amostra para criar um IR Azure com localização definida para "Europa Ocidental":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Para o Azure IR, o tipo deve ser definido para **gerido**. Não é necessário especificar detalhes da computação porque é totalmente gerido elástico na nuvem. Especifique detalhes computacionais como o tamanho do nó e a contagem do nó quando quiser criar o Ir Azure-SSIS. Para mais informações, consulte [Create and Configure Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Pode configurar um Ir Azure existente para alterar a sua localização utilizando o Set-AzDataFactoryV2IntegrationTimePowerShell cmdlet. Para obter mais informações sobre a localização de um IR Azure, consulte Introdução ao tempo de execução da [integração.](concepts-integration-runtime.md)

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Criar um IR Azure via Azure Data Factory UI
Utilize os seguintes passos para criar um Ir Azure utilizando a Azure Data Factory UI.

1. Na página **Let's start** da Azure Data Factory UI, selecione o separador **Autor** no painel esquerdo.

   ![O botão de autor da página inicial](media/doc-common-process/get-started-page-author-button.png)

1. Selecione Ligações na parte inferior do painel esquerdo e selecione **tempos** de **integração** na janela **Ligações.** **Selecione +Novo**.

   ![Criar um integration runtime](media/create-azure-integration-runtime/new-integration-runtime.png)

1. Na página de configuração do tempo de execução da **Integração,** selecione **Azure, Self-Hosted,** e, em seguida, selecione **Continue**. 

1. Na página seguinte, selecione **Azure** para criar um Ir Azure e, em seguida, selecione **Continuar**.
   ![Criar um integration runtime](media/create-azure-integration-runtime/new-azure-ir.png)

1. Introduza um nome para o seu Azure IR e selecione **Criar**.
   ![Criar um IR Azure](media/create-azure-integration-runtime/create-azure-ir.png)

1. Verá uma notificação pop-up quando a criação terminar. Na página de Tempos de **Integração,** certifique-se de ver o IR recém-criado na lista.

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

- [Create self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Criar o runtime de integração autoalojado)
- [Criar o runtime de integração de SSIS do Azure](create-azure-ssis-integration-runtime.md)
 
