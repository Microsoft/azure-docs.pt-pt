---
title: Mover uma conta de Armazenamento Azure para outra região Microsoft Docs
description: Mostra-lhe como mover uma conta de Armazenamento Azure para outra região.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: c8578c518ac45bea147790028c2904c7ce36fffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459037"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Mova uma conta de Armazenamento Azure para outra região

Para mover uma conta de armazenamento, crie uma cópia da sua conta de armazenamento noutra região. Em seguida, mova os seus dados para essa conta utilizando o AzCopy, ou outra ferramenta à sua escolha.

Neste artigo, aprenderá a:

> [!div class="checklist"]
> 
> * Exportar um modelo.
> * Modifique o modelo adicionando a região alvo e o nome da conta de armazenamento.
> * Implemente o modelo para criar a nova conta de armazenamento.
> * Configure a nova conta de armazenamento.
> * Mova os dados para a nova conta de armazenamento.
> * Apagar os recursos na região de origem.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que os serviços e funcionalidades que a sua conta utiliza são suportados na região alvo.

- Para funcionalidades de pré-visualização, certifique-se de que a sua subscrição está listada para a região alvo.

<a id="prepare" />

## <a name="prepare"></a>Preparação

Para começar, exportar e, em seguida, modificar um modelo de Gestor de Recursos. 

### <a name="export-a-template"></a>Exportar um modelo

Este modelo contém configurações que descrevem a sua conta de armazenamento. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para exportar um modelo utilizando o portal Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. **Selecione Todos os recursos** e, em seguida, selecione a sua conta de armazenamento.

3. Selecione > Modelo**de exportação**de **definições** > .

4. Escolha **o download** na lâmina do modelo de **exportação.**

5. Localize o ficheiro .zip que descarregou do portal e desaperte esse ficheiro para uma pasta à sua escolha.

   Este ficheiro zip contém os ficheiros .json que compõem o modelo e os scripts para implementar o modelo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para exportar um modelo utilizando o PowerShell:

1. Inscreva-se na subscrição do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções no ecrã:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Se a sua identidade estiver associada a mais de uma subscrição, então delineie a sua subscrição ativa para a subscrição da conta de armazenamento que pretende mover.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exporte o modelo da sua conta de armazenamento de origem. Estes comandos guardam um modelo json para o seu diretório atual.

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

Modificar o modelo alterando o nome da conta de armazenamento e a região.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para implementar o modelo utilizando o portal Azure:

1. No portal do Azure, selecione **Criar um recurso**.

2. Em **Pesquisar no Marketplace**, escreva **implementação de modelo** e prima **ENTER**.

3. Selecione **Implementação de modelo**.

    ![Biblioteca de modelos do Azure Resource Manager](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Selecione **Criar**.

5. Selecione **Crie o seu próprio modelo no editor**.

6. Selecione **ficheiro Load**, e siga as instruções para carregar o ficheiro **template.json** que descarregou na última secção.

7. No ficheiro **template.json,** nomeie a conta de armazenamento alvo definindo o valor predefinido do nome da conta de armazenamento. Este exemplo define o valor predefinido `mytargetaccount`do nome da conta de armazenamento para .
    
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
    Para obter códigos de localização da região, consulte [localizações azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código para uma região é o nome da região sem espaços,**central** **dos EUA** = central.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para implementar o modelo utilizando o PowerShell:

1. No ficheiro **template.json,** nomeie a conta de armazenamento alvo definindo o valor predefinido do nome da conta de armazenamento. Este exemplo define o valor predefinido `mytargetaccount`do nome da conta de armazenamento para .
    
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

2. Editar a propriedade de **localização** no ficheiro **template.json** para a região alvo. Este exemplo define a `eastus`região-alvo para .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Pode obter códigos de região executando o comando [Get-AzLocation.](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Mover

Implemente o modelo para criar uma nova conta de armazenamento na região alvo. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Guarde o ficheiro **template.json.**

2. Insira ou selecione os valores de propriedade:

- **Subscrição**: Selecione uma subscrição Azure.

- **Grupo de recursos**: selecione **Criar novo** e dê um nome ao grupo de recursos.

- **Localização**: Selecione uma localização Azure.

3. Clique no **I concordando com os termos e condições indicados acima da** caixa de verificação e, em seguida, clique no botão **'Selecionar Comprar'.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Obtenha o ID de subscrição onde pretende implementar o IP público alvo com [a Get-AzSubscription:](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Utilize estes comandos para implementar o seu modelo:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Configure a nova conta de armazenamento

Algumas funcionalidades não serão exportadas para um modelo, por isso terá que adicioná-las à nova conta de armazenamento. 

A tabela seguinte lista estas funcionalidades juntamente com orientações para adicioná-las à sua nova conta de armazenamento.

| Funcionalidade    | Orientação    |
|--------|-----------|
| **Políticas de gestão do ciclo de vida** | [Gerir o ciclo de vida do Armazenamento de Blobs do Azure](../blobs/storage-lifecycle-management-concepts.md) |
| **Web sites estáticos** | [Hospedar um site estático no Armazenamento Azure](../blobs/storage-blob-static-website-how-to.md) |
| **Subscrições de eventos** | [Reagir aos eventos de armazenamento de Blobs](../blobs/storage-blob-event-overview.md) |
| **Alertas** | [Criar, visualizar e gerir alertas de registo de atividade utilizando o Monitor Azure](../../azure-monitor/platform/alerts-activity-log.md) |
| **Rede de Entrega de Conteúdos (CDN)** | [Utilize o Azure CDN para aceder a blobs com domínios personalizados em HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Se configurar um CDN para a conta de armazenamento de fonte, basta alterar a origem do seu CDN existente para o ponto final do serviço de blob primário (ou o ponto final do site estático primário) da sua nova conta. 

### <a name="move-data-to-the-new-storage-account"></a>Mova os dados para a nova conta de armazenamento

Aqui estão algumas maneiras de mover os seus dados.

:heavy_check_mark: **Explorador de Armazenamento Azure**

  É fácil de usar e é adequado para pequenos conjuntos de dados. Pode copiar contentores e arquivar ações e, em seguida, colar na conta-alvo.

  Ver Explorador de [Armazenamento Azure;](https://azure.microsoft.com/features/storage-explorer/)

:heavy_check_mark: **AzCopy**

  Esta é a abordagem preferida. Está otimizado para o desempenho.  Uma das formas de ser mais rápido, é que os dados são copiados diretamente entre servidores de armazenamento, para que o AzCopy não utilize a largura de banda da rede do seu computador. Utilize o AzCopy na linha de comando ou como parte de um script personalizado.

  Ver [Começar com AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark: Fábrica de **Dados Azure** 

  Utilize esta ferramenta apenas se precisar de funcionalidades que não são suportadas no lançamento atual do AzCopy. Por exemplo, no lançamento atual do AzCopy, não é possível copiar bolhas entre contas que têm um espaço de nome hierárquico. Também o AzCopy não preserva as listas de controlo de acesso de ficheiros ou os selos de tempo de ficheiro (por exemplo: criar e modificar selos de tempo). 

  Consulte estes links:
  - [Copiar dados de ou para o armazenamento da Blob Azure utilizando a Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Copiar dados de ou para o Azure Data Lake Storage Gen2 utilizando a Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Copiar dados de ou para o armazenamento de ficheiros Azure utilizando a Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Copiar dados de e para o armazenamento da tabela Azure utilizando a Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Descartar ou limpar

Após a implementação, se quiser recomeçar, pode eliminar a conta de armazenamento do alvo e repetir os passos descritos nas secções [preparee](#prepare) [move](#move) deste artigo.

Para epenhar as alterações e concluir o movimento de uma conta de armazenamento, elimine a conta de armazenamento de origem.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para remover uma conta de armazenamento utilizando o portal Azure:

1. No portal Azure, expanda o menu do lado esquerdo para abrir o menu de **serviços** e escolha contas de Armazenamento para exibir a lista das suas contas de armazenamento.

2. Localize a conta de armazenamento do alvo para eliminar e clique à direita no botão **Mais** (**...**) no lado direito da listagem.

3. Selecione **Excluir**e confirmar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para remover o grupo de recursos e os seus recursos associados, incluindo a nova conta de armazenamento, utilize o comando [Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou uma conta de armazenamento Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e a recuperação de desastres em Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
