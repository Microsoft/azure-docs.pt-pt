---
title: Mover uma conta de armazenamento do Azure para outra região | Microsoft Docs
description: Mostra como mover uma conta de armazenamento do Azure para outra região.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 76648428e6adcaed579b0e4f1896fdf83e11a8b6
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348857"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Mover uma conta de armazenamento do Azure para outra região

Para mover uma conta de armazenamento, crie uma cópia da sua conta de armazenamento em outra região. Em seguida, mova os dados para essa conta usando o AzCopy ou outra ferramenta de sua escolha.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> 
> * Exportar um modelo.
> * Modifique o modelo adicionando a região de destino e o nome da conta de armazenamento.
> * Implante o modelo para criar a nova conta de armazenamento.
> * Configure a nova conta de armazenamento.
> * Mover dados para a nova conta de armazenamento.
> * Exclua os recursos na região de origem.

## <a name="prerequisites"></a>Pré-requisitos

- Verifique se os serviços e recursos que sua conta usa têm suporte na região de destino.

- Para recursos de visualização, verifique se sua assinatura está na lista de permissões para a região de destino.

<a id="prepare" />

## <a name="prepare"></a>Preparar

Para começar, exportar e modificar um modelo do Resource Manager. 

### <a name="export-a-template"></a>Exportar um modelo

Este modelo contém configurações que descrevem sua conta de armazenamento. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Para exportar um modelo usando portal do Azure:

1. Inicie sessão no [portal do Azure](http://portal.azure.com).

2. Selecione **todos os recursos** e, em seguida, selecione sua conta de armazenamento.

3. Selecione **configurações** > de >**modelo de exportação**.

4. Escolha **baixar** na folha **Exportar modelo** .

5. Localize o arquivo. zip que você baixou do portal e descompacte esse arquivo em uma pasta de sua escolha.

   Esse arquivo zip contém os arquivos. JSON que compõem o modelo e os scripts para implantar o modelo.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para exportar um modelo usando o PowerShell:

1. Entre em sua assinatura do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções na tela:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento que você deseja mover.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exporte o modelo de sua conta de armazenamento de origem. Esses comandos salvam um modelo JSON em seu diretório atual.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>Modificar o modelo 

Modifique o modelo alterando o nome da conta de armazenamento e a região.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Para implantar o modelo usando portal do Azure:

1. No portal do Azure, selecione **Criar um recurso**.

2. Em **Pesquisar no Marketplace**, escreva **implementação de modelo** e prima **ENTER**.

3. Selecione **Implementação de modelo**.

    ![Biblioteca de modelos do Azure Resource Manager](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Selecione **Criar**.

5. Selecione **Crie o seu próprio modelo no editor**.

6. Selecione **carregar arquivo**e siga as instruções para carregar o arquivo **Template. JSON** que você baixou na última seção.

7. No arquivo **Template. JSON** , nomeie a conta de armazenamento de destino definindo o valor padrão do nome da conta de armazenamento. Este exemplo define o valor padrão do nome da conta de armazenamento como `mytargetaccount`.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código de uma região é o nome da região sem espaços, **EUA Central** = **centralus**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para implantar o modelo usando o PowerShell:

1. No arquivo **Template. JSON** , nomeie a conta de armazenamento de destino definindo o valor padrão do nome da conta de armazenamento. Este exemplo define o valor padrão do nome da conta de armazenamento como `mytargetaccount`.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Edite a propriedade **Location** no arquivo **Template. JSON** para a região de destino. Este exemplo define a região de destino como `eastus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Você pode obter códigos de região executando o comando [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) .

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Mover

Implante o modelo para criar uma nova conta de armazenamento na região de destino. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Salve o arquivo **Template. JSON** .

2. Insira ou selecione os valores de propriedade:

- **Subscrição**: Selecione uma subscrição do Azure.

- **Grupo de recursos**: Selecione **criar novo** e dê um nome ao grupo de recursos.

- **Local**: Selecione uma localização do Azure.

3. Clique na caixa de seleção **eu concordo com os termos e condições declarados acima** e clique no botão **selecionar compra** .

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Obtenha a ID da assinatura na qual você deseja implantar o IP público de destino com [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Use estes comandos para implantar seu modelo:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Configurar a nova conta de armazenamento

Alguns recursos não serão exportados para um modelo, portanto, você precisará adicioná-los à nova conta de armazenamento. 

A tabela a seguir lista esses recursos, juntamente com as diretrizes para adicioná-los à sua nova conta de armazenamento.

| Funcionalidade    | Orientação    |
|--------|-----------|
| **Políticas de gerenciamento do ciclo de vida** | [Gerir o ciclo de vida do Armazenamento de blobs do Azure](../blobs/storage-lifecycle-management-concepts.md) |
| **Sites estáticos** | [Hospedar um site estático no armazenamento do Azure](../blobs/storage-blob-static-website-how-to.md) |
| **Assinaturas de evento** | [Reagir aos eventos de armazenamento de Blobs](../blobs/storage-blob-event-overview.md) |
| **Alertas** | [Criar, exibir e gerenciar alertas do log de atividades usando Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **CDN (rede de distribuição de conteúdo)** | [Usar a CDN do Azure para acessar BLOBs com domínios personalizados por HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Se você configurar uma CDN para a conta de armazenamento de origem, basta alterar a origem da CDN existente para o ponto de extremidade do serviço blob primário (ou o ponto de extremidade primário do site estático) da sua nova conta. 

### <a name="move-data-to-the-new-storage-account"></a>Mover dados para a nova conta de armazenamento

Aqui estão algumas maneiras de mover seus dados.

:heavy_check_mark: **Explorador do Armazenamento do Azure**

  Ele é fácil de usar e adequado para conjuntos de dados pequenos. Você pode copiar contêineres e compartilhamentos de arquivos e, em seguida, colá-los na conta de destino.

  Consulte [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/);

:heavy_check_mark: **AZCopy**

  Essa é a abordagem preferida. Ele é otimizado para desempenho.  Uma maneira mais rápida, é que os dados são copiados diretamente entre os servidores de armazenamento, portanto, o AzCopy não usa a largura de banda de rede do seu computador. Use AzCopy na linha de comando ou como parte de um script personalizado.

  Consulte Introdução [ao AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark: **Azure Data Factory** 

  Use essa ferramenta somente se precisar de funcionalidade que não tenha suporte na versão atual do AzCopy. Por exemplo, na versão atual do AzCopy, não é possível copiar BLOBs entre contas que têm um namespace hierárquico. Também AzCopy não preserva as listas de controle de acesso do arquivo ou os carimbos de data/hora do arquivo (por exemplo: criar e modificar carimbos de data/hora) 

  Consulte estes links:
  - [Copiar dados de ou para o armazenamento de BLOBs do Azure usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Copiar dados de ou para Azure Data Lake Storage Gen2 usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Copiar dados de ou para o armazenamento de arquivos do Azure usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Copiar dados de e para o armazenamento de tabelas do Azure usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Descartar ou limpar

Após a implantação, se você quiser começar novamente, poderá excluir a conta de armazenamento de destino e repetir as etapas descritas nas seções [preparar](#prepare) e [mover](#move) deste artigo.

Para confirmar as alterações e concluir a movimentação de uma conta de armazenamento, exclua a conta de armazenamento de origem.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Para remover uma conta de armazenamento usando o portal do Azure:

1. No portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e escolha contas de **armazenamento** para exibir a lista de suas contas de armazenamento.

2. Localize a conta de armazenamento de destino a ser excluída e clique com o botão direito do mouse no botão **mais** ( **...** ) no lado direito da listagem.

3. Selecione **excluir**e confirmar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [Remove-AzStorageAccount](/powershell/module/az.resources/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você moveu uma conta de armazenamento do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
