---
title: Solucionar problemas Azure Data Factory
description: Saiba como solucionar problemas com o uso de Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
robots: noindex
ms.openlocfilehash: 81ae5c3c702108d854e4dfde93001d5c99875666
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931590"
---
# <a name="troubleshoot-data-factory-issues"></a>Resolver Problemas do Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. 

Este artigo fornece dicas de solução de problemas ao usar o Azure Data Factory. Este artigo não lista todos os possíveis problemas ao usar o serviço, mas aborda alguns problemas e dicas de solução de problemas gerais.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Sugestões para a resolução de problemas
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Aparece o erro: a subscrição não está registada para utilizar o espaço de nomes 'Microsoft.DataFactory'
Se aparecer este erro, o fornecedor de recursos do Azure Data Factory não foi registado no seu computador. Faça o seguinte:

1. Inicie o Azure PowerShell.
2. Faça logon em sua conta do Azure usando o comando a seguir.

    ```powershell
    Connect-AzAccount
    ```
3. Execute o comando a seguir para registrar o provedor de Azure Data Factory.

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problema: erro não autorizado ao executar um cmdlet Data Factory
Provavelmente não está a utilizar a conta ou a subscrição do Azure adequada com o Azure PowerShell. Utilize os seguintes cmdlets para selecionar a conta e a subscrição do Azure adequadas que devem ser utilizadas com o Azure PowerShell.

1. Connect-AzAccount-use a ID de usuário e a senha corretas
2. Get-AzSubscription-exibe todas as assinaturas da conta.
3. Select-AzSubscription &lt;nome da assinatura&gt;-selecione a assinatura correta. Use o mesmo que você usa para criar um data factory no portal do Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problema: falha ao iniciar a instalação do Gerenciamento de Dados gateway Express de portal do Azure
A configuração rápida para o Data Management Gateway necessita do Internet Explorer ou de um browser compatível com o Microsoft ClickOnce. Se não for possível iniciar a Configuração Rápida, faça o seguinte:

* Use o Internet Explorer ou um navegador da Web compatível com o Microsoft ClickOnce.

    Se estiver a utilizar o Chrome, aceda à [Web Store do Chrome](https://chrome.google.com/webstore/), pesquise com a palavra-chave "ClickOnce", escolha uma das extensões de ClickOnce e instale-a.

    Faça o mesmo para o Firefox (instalar suplemento). Clique no botão Abrir Menu na barra de ferramentas (três linhas horizontais no canto superior direito), clique em Suplementos, pesquise com a palavra-chave "ClickOnce", escolha uma das extensões de ClickOnce e instale-a.
* Use o link **instalação manual** mostrado na mesma folha no Portal. Use essa abordagem para baixar o arquivo de instalação e executá-lo manualmente. Depois que a instalação for bem-sucedida, você verá a caixa de diálogo configuração do Gerenciamento de Dados gateway. Copie a **chave** no ecrã do portal e utilize-a no gestor de configuração para registar manualmente o gateway no serviço.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problema: falha ao conectar-se ao SQL Server local
Inicie o **Gerenciamento de dados Gateway Configuration Manager** no computador do gateway e use a guia **solução de problemas** para testar a conexão com SQL Server do computador do gateway. Consulte [solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre como solucionar problemas relacionados à conexão/gateway.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problema: as fatias de entrada estão no estado de espera para sempre
As fatias podem estar em estado de **espera** devido a vários motivos. Um dos motivos comuns é que a propriedade **external** não está definida como **true**. Qualquer conjunto de os que for produzido fora do escopo de Azure Data Factory deve ser marcado com a propriedade **external** . Essa propriedade indica que os dados são externos e não são apoiados por nenhum pipeline dentro do data factory. Os setores de dados são marcados como **Pronto** assim que os dados estiverem disponíveis no respetivo arquivo.

Veja o seguinte exemplo para saber como utilizar a propriedade **external**. Opcionalmente, você pode especificar **externalData*** ao definir external como true.

Veja o artigo [Conjuntos de dados](data-factory-create-datasets.md) para obter mais detalhes sobre esta propriedade.

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

Para resolver o erro, adicione a propriedade **external** e a secção opcional **externalData** à definição JSON da tabela de entrada e recrie a tabela.

### <a name="problem-hybrid-copy-operation-fails"></a>Problema: falha na operação de cópia híbrida
Consulte [solucionar](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) problemas de gateway para ver as etapas para solucionar problemas com a cópia de/para um armazenamento de dados local usando o gateway de gerenciamento de dados.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problema: falha de provisionamento sob demanda do HDInsight
Ao usar um serviço vinculado do tipo HDInsightOnDemand, você precisa especificar um linkedServiceName que aponte para um armazenamento de BLOBs do Azure. O serviço do Data Factory utiliza este armazenamento para armazenar registos e ficheiros de suporte para o cluster do HDInsight a pedido.  Por vezes, o aprovisionamento de um cluster de HDInsight a pedido pode falhar com o erro seguinte:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Normalmente, este erro indica que a localização da conta de armazenamento especificada no linkedServiceName não está na mesma localização do datacenter, onde o aprovisionamento do HDInsight está a acontecer. Exemplo: se seu data factory estiver no oeste dos EUA e o armazenamento do Azure estiver no leste dos EUA, o provisionamento sob demanda falhará no oeste dos EUA.

Além disso, existe um segundo additionalLinkedServiceNames de propriedade JSON onde é possível especificar contas de armazenamento adicionais no HDInsight a pedido. Essas contas de armazenamento vinculadas adicionais devem estar no mesmo local que o cluster HDInsight ou falha com o mesmo erro.

### <a name="problem-custom-net-activity-fails"></a>Problema: falha na atividade personalizada do .NET
Consulte [depurar um pipeline com atividade personalizada](data-factory-use-custom-activities.md#troubleshoot-failures) para obter etapas detalhadas.

## <a name="use-azure-portal-to-troubleshoot"></a>Usar portal do Azure para solucionar problemas
### <a name="using-portal-blades"></a>Usando as folhas do portal
Consulte [monitorar pipeline](data-factory-monitor-manage-pipelines.md) para obter as etapas.

### <a name="using-monitor-and-manage-app"></a>Utilizar a Aplicação Monitorizar e Gerir
Consulte [monitorar e gerenciar pipelines data Factory usando o aplicativo monitorar e gerenciar](data-factory-monitor-manage-app.md) para obter detalhes.

## <a name="use-azure-powershell-to-troubleshoot"></a>Usar Azure PowerShell para solucionar problemas
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Usar Azure PowerShell para solucionar um erro
Consulte [monitorar data Factory pipelines usando Azure PowerShell](data-factory-monitor-manage-pipelines.md) para obter detalhes.

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: https://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
