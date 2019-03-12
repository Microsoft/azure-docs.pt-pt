---
title: Resolver problemas do Azure Data Factory
description: Saiba como resolver problemas com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: 5b9e0a30658e1cb8fe0f83d55c04f120637babaf
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549209"
---
# <a name="troubleshoot-data-factory-issues"></a>Resolver Problemas do Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. 

Este artigo fornece sugestões de resolução de problemas para problemas ao utilizar o Azure Data Factory. Este artigo não lista os possíveis problemas ao utilizar o serviço, mas abrange alguns problemas e sugestões de resolução de problemas gerais.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Sugestões para a resolução de problemas
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Erro: A subscrição não está registada para utilizar o espaço de nomes 'Microsoft. DataFactory'
Se aparecer este erro, o fornecedor de recursos do Azure Data Factory não foi registado no seu computador. Faça o seguinte:

1. Inicie o Azure PowerShell.
2. Inicie sessão na sua conta do Azure com o seguinte comando.

    ```powershell
    Connect-AzAccount
    ```
3. Execute o seguinte comando para registar o fornecedor do Azure Data Factory.

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problema: Erro não autorizado ao executar um cmdlet do Data Factory
Provavelmente não está a utilizar a conta ou a subscrição do Azure adequada com o Azure PowerShell. Utilize os seguintes cmdlets para selecionar a conta e a subscrição do Azure adequadas que devem ser utilizadas com o Azure PowerShell.

1. Ligar-AzAccount - utilize o ID de utilizador certo e a palavra-passe
2. Get-AzSubscription - ver todas as subscrições para a conta.
3. Selecione AzSubscription &lt;nome da subscrição&gt; -selecione a subscrição correta. Utilize a mesma que utiliza para criar uma fábrica de dados no portal do Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problema: Falha ao iniciar a configuração de Express do Data Management Gateway no portal do Azure
A configuração rápida para o Data Management Gateway necessita do Internet Explorer ou de um browser compatível com o Microsoft ClickOnce. Se não for possível iniciar a Configuração Rápida, faça o seguinte:

* Utilize o Internet Explorer ou um navegador da web compatível com ClickOnce da Microsoft.

    Se estiver a utilizar o Chrome, aceda à [Web Store do Chrome](https://chrome.google.com/webstore/), pesquise com a palavra-chave "ClickOnce", escolha uma das extensões de ClickOnce e instale-a.

    Fazer o mesmo no Firefox (instalação suplemento). Clique no botão Abrir Menu na barra de ferramentas (três linhas horizontais no canto superior direito), clique em Suplementos, pesquise com a palavra-chave "ClickOnce", escolha uma das extensões de ClickOnce e instale-a.
* Utilize o **configuração Manual** link mostrado no painel do mesmo no portal. Utilize esta abordagem para transferir o ficheiro de instalação e executá-lo manualmente. Depois da instalação foi bem-sucedida, verá a caixa de diálogo de configuração do Gateway de gestão de dados. Copie a **chave** no ecrã do portal e utilize-a no gestor de configuração para registar manualmente o gateway no serviço.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problema: Falha ao ligar ao SQL Server no local
Inicie **Gestor de configuração do Data Management Gateway** na máquina de gateway e utilize o **resolução de problemas** separador para testar a ligação ao SQL Server a partir da máquina de gateway. Ver [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter sugestões sobre resolução de problemas do gateway de ligação/problemas relacionados com.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problema: Os setores de entrada estão num estado de espera permanente
Os setores podem estar no **aguardando** Estado devido a vários motivos. Uma das razões comuns é que o **externo** propriedade não está definida como **verdadeiro**. Qualquer conjunto de dados produzido fora do escopo do Azure Data Factory deve ser marcado com **externo** propriedade. Esta propriedade indica que os dados são externos e não estão protegidos por quaisquer pipelines na fábrica de dados. Os setores de dados são marcados como **Pronto** assim que os dados estiverem disponíveis no respetivo arquivo.

Veja o seguinte exemplo para saber como utilizar a propriedade **external**. Opcionalmente, pode especificar **externalData*** quando definir external como true.

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

### <a name="problem-hybrid-copy-operation-fails"></a>Problema: Falha da operação de cópia híbrida
Ver [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter os passos resolver problemas com copiar de um dados no local/para armazenar a utilizar o Gateway de gestão de dados.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problema: HDInsight a pedido falhar de aprovisionamento
Quando utilizar um serviço ligado do tipo HDInsightOnDemand, tem de especificar um linkedServiceName que aponte para um armazenamento de Blobs do Azure. O serviço do Data Factory utiliza este armazenamento para armazenar registos e ficheiros de suporte para o cluster do HDInsight a pedido.  Por vezes, o aprovisionamento de um cluster de HDInsight a pedido pode falhar com o erro seguinte:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Normalmente, este erro indica que a localização da conta de armazenamento especificada no linkedServiceName não está na mesma localização do datacenter, onde o aprovisionamento do HDInsight está a acontecer. Exemplo: se a fábrica de dados está na região E.U.A. oeste e o armazenamento do Azure está na região E.U.A. leste, o provisionamento por demanda falha na região E.U.A. oeste.

Além disso, existe um segundo additionalLinkedServiceNames de propriedade JSON onde é possível especificar contas de armazenamento adicionais no HDInsight a pedido. Essas contas de armazenamento ligadas adicionais devem estar na mesma localização que o cluster do HDInsight ou falhar com o mesmo erro.

### <a name="problem-custom-net-activity-fails"></a>Problema: Falhas de atividades .NET personalizadas
Ver [depurar um pipeline com atividade personalizada](data-factory-use-custom-activities.md#troubleshoot-failures) para obter passos detalhados.

## <a name="use-azure-portal-to-troubleshoot"></a>Utilizar o portal do Azure para resolver problemas
### <a name="using-portal-blades"></a>Com os painéis do portais
Ver [monitorizar o pipeline](data-factory-build-your-first-pipeline-using-editor.md#monitor-a-pipeline) para obter os passos.

### <a name="using-monitor-and-manage-app"></a>Utilizar a Aplicação Monitorizar e Gerir
Ver [monitorizar e gerir pipelines de fábrica de dados com a monitorizar e gerir aplicações](data-factory-monitor-manage-app.md) para obter detalhes.

## <a name="use-azure-powershell-to-troubleshoot"></a>Utilizar o Azure PowerShell para resolver problemas
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Utilizar o Azure PowerShell para resolver problemas relacionados com um erro
Ver [pipelines de Monitor de Data Factory com o Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) para obter detalhes.

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
