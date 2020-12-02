---
title: Problemas na resolução de problemas da Fábrica de Dados do Azure
description: Saiba como resolver problemas com a utilização da Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
manager: anandsub
robots: noindex
ms.openlocfilehash: bd18a26a1c199e1ecc32cfc371d2931b1dee0c3f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494975"
---
# <a name="troubleshoot-data-factory-issues"></a>Resolver Problemas do Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. 

Este artigo fornece dicas de resolução de problemas para problemas ao utilizar a Azure Data Factory. Este artigo não enumera todos os problemas possíveis ao utilizar o serviço, mas abrange algumas questões e dicas gerais de resolução de problemas.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Aparece o erro: a subscrição não está registada para utilizar o espaço de nomes 'Microsoft.DataFactory'
Se aparecer este erro, o fornecedor de recursos do Azure Data Factory não foi registado no seu computador. Faça o seguinte:

1. Inicie o Azure PowerShell.
2. Faça login na sua conta Azure utilizando o seguinte comando.

    ```powershell
    Connect-AzAccount
    ```
3. Executar o seguinte comando para registar o fornecedor Azure Data Factory.

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problema: Erro não autorizado ao executar um cmdlet da Data Factory
Provavelmente não está a utilizar a conta ou a subscrição do Azure adequada com o Azure PowerShell. Utilize os seguintes cmdlets para selecionar a conta e a subscrição do Azure adequadas que devem ser utilizadas com o Azure PowerShell.

1. Connect-AzAccount - Use o ID do utilizador certo e a palavra-passe
2. Get-AzSubscription - Ver todas as subscrições da conta.
3. Select-AzSubscription nome &lt; de subscrição &gt; - Selecione a subscrição certa. Use o mesmo que usa para criar uma fábrica de dados no portal Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problema: Falha no lançamento da Data Management Gateway Express Setup a partir do portal Azure
A configuração rápida para o Data Management Gateway necessita do Internet Explorer ou de um browser compatível com o Microsoft ClickOnce. Se não for possível iniciar a Configuração Rápida, faça o seguinte:

* Utilize o Internet Explorer ou um navegador web compatível com o Microsoft ClickOnce.

    Se estiver a utilizar o Chrome, vá à loja web do [Chrome,](https://chrome.google.com/webstore/)procure com a palavra-chave "ClickOnce", escolha uma das extensões ClickOnce e instale-a.

    Faça o mesmo para o Firefox (instale o add-in). Clique no botão Abrir Menu na barra de ferramentas (três linhas horizontais no canto superior direito), clique em Suplementos, pesquise com a palavra-chave "ClickOnce", escolha uma das extensões de ClickOnce e instale-a.
* Utilize o link **de configuração manual** indicado na mesma lâmina do portal. Utilize esta abordagem para descarregar o ficheiro de instalação e executá-lo manualmente. Depois de a instalação ter sido bem sucedida, vê a caixa de diálogo de configuração do Gateway de Gestão de Dados. Copie a **chave** no ecrã do portal e utilize-a no gestor de configuração para registar manualmente o gateway no serviço.  

### <a name="problem-fail-to-connect-to-sql-server"></a>Problema: Falha na ligação ao SQL Server
Lance o **Gestor de Configuração gateway de gestão de dados** na máquina de gateway e utilize o **separador de resolução de problemas** para testar a ligação ao SQL Server a partir da máquina de gateway. Consulte [os problemas de gateway de resolução de problemas](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre questões relacionadas com a ligação de resolução de problemas/gateways.   

### <a name="problem-input-slices-are-in-waiting-state-forever"></a>Problema: As fatias de entrada estão em estado de espera para sempre
As fatias podem estar em estado **de espera** por várias razões. Uma das razões comuns é que a propriedade **externa** não é definida como **verdadeira.** Qualquer conjunto de dados produzido fora do âmbito da Azure Data Factory deve ser marcado com propriedade **externa.** Esta propriedade indica que os dados são externos e não são apoiados por quaisquer oleodutos dentro da fábrica de dados. Os setores de dados são marcados como **Pronto** assim que os dados estiverem disponíveis no respetivo arquivo.

Veja o seguinte exemplo para saber como utilizar a propriedade **external**. Pode especificar opcionalmente **externalData** _ quando configurar externo para verdadeiro.

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

Para resolver o erro, adicione a propriedade _ *externa** e a secção **opcional externalData** à definição JSON da tabela de entrada e recrie a tabela.

### <a name="problem-hybrid-copy-operation-fails"></a>Problema: Falha na operação de cópia híbrida
Consulte [problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) de resolução de problemas para as medidas para resolver problemas com a cópia de/para uma loja de dados no local utilizando o Gateway de Gestão de Dados.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problema: O provisionamento de HDInsight a pedido falha
Quando utiliza um serviço ligado do tipo HDInsightOnDemand, tem de especificar um linkedServiceName que aponte para um armazenamento de blobs do Azure. O serviço do Data Factory utiliza este armazenamento para armazenar registos e ficheiros de suporte para o cluster do HDInsight a pedido.  Por vezes, o aprovisionamento de um cluster de HDInsight a pedido pode falhar com o erro seguinte:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Normalmente, este erro indica que a localização da conta de armazenamento especificada no linkedServiceName não está na mesma localização do datacenter, onde o aprovisionamento do HDInsight está a acontecer. Exemplo: se a sua fábrica de dados estiver no Oeste dos EUA e o armazenamento do Azure estiver no Leste dos EUA, o fornecimento a pedido falha no Oeste dos EUA.

Além disso, existe um segundo additionalLinkedServiceNames de propriedade JSON onde é possível especificar contas de armazenamento adicionais no HDInsight a pedido. Essas contas de armazenamento ligadas adicionais devem estar no mesmo local que o cluster HDInsight, ou falha com o mesmo erro.

### <a name="problem-custom-net-activity-fails"></a>Problema: A atividade personalizada .NET falha
Consulte [depurar um oleoduto com atividade personalizada](data-factory-use-custom-activities.md#troubleshoot-failures) para passos detalhados.

## <a name="use-azure-portal-to-troubleshoot"></a>Use o portal Azure para resolver problemas
### <a name="using-portal-blades"></a>Usando lâminas de portal
Consulte [o gasoduto Monitor](data-factory-monitor-manage-pipelines.md) para obter etapas.

### <a name="using-monitor-and-manage-app"></a>Com a Aplicação Monitorizar e Gerir
Consulte [o Monitor e gere os oleodutos da fábrica de dados utilizando a App Monitor e Manage para](data-factory-monitor-manage-app.md) obter detalhes.

## <a name="use-azure-powershell-to-troubleshoot"></a>Use a Azure PowerShell para resolver problemas
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Use a Azure PowerShell para resolver um erro
Consulte [os oleodutos Monitor Data Factory utilizando a Azure PowerShell](data-factory-monitor-manage-pipelines.md) para obter mais detalhes.

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: /previous-versions/azure/dn835050(v=azure.100)

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