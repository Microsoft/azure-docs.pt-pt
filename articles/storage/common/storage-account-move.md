---
title: Mover uma conta de Armazenamento Azure para outra região | Microsoft Docs
description: Mostra-lhe como mover uma conta de Armazenamento Azure para outra região.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: d894ce71e0ffa5a0894a1f6b0035efe66271ded8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591471"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Mover uma conta de Armazenamento Azure para outra região

Para mover uma conta de armazenamento, crie uma cópia da conta de armazenamento noutra região. Em seguida, mova os seus dados para essa conta usando o AzCopy, ou outra ferramenta à sua escolha.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> 
> * Exporte um modelo.
> * Modifique o modelo adicionando a região alvo e o nome da conta de armazenamento.
> * Implemente o modelo para criar a nova conta de armazenamento.
> * Configure a nova conta de armazenamento.
> * Mover dados para a nova conta de armazenamento.
> * Apagar os recursos na região origem.

## <a name="prerequisites"></a>Pré-requisitos

- Garanta que os serviços e as funcionalidades que a conta utiliza são suportados na região de destino.

- Para funcionalidades de pré-visualização, certifique-se de que a sua subscrição está na lista de visualização para a região alvo.

<a id="prepare"></a>

## <a name="prepare"></a>Preparação

Para começar, exportar e, em seguida, modificar um modelo de Gestor de Recursos. 

### <a name="export-a-template"></a>Exportar um modelo

Este modelo contém definições que descrevem a conta de armazenamento. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para exportar um modelo com o portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **todos os recursos** e, em seguida, selecione a sua conta de armazenamento.

3. Selecione > **Definições**  >  **Modelo de exportação**.

4. Escolha **Baixar** na lâmina do **modelo de exportação.**

5. Localize o ficheiro .zip que descarregou do portal e desaperte esse ficheiro para uma pasta à sua escolha.

   Este ficheiro zip contém os ficheiros .json que compõem o modelo e scripts para implementar o modelo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para exportar um modelo utilizando o PowerShell:

1. Inscreva-se na sua subscrição Azure com o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções no ecrã:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Se a sua identidade estiver associada a mais de uma subscrição, então desa estalem a sua subscrição ativa para a subscrição da conta de armazenamento que pretende mover.

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

Modifique o modelo ao alterar o nome e a região da conta de armazenamento.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para implementar o modelo utilizando o portal Azure:

1. No portal Azure, selecione **Criar um recurso**.

2. Em **Search the Marketplace**, **digitar a implementação do modelo** e, em seguida, premir **ENTER**.

3. Selecione **a implementação do modelo**.

    ![Biblioteca de modelos do Azure Resource Manager](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Selecione **Criar**.

5. Selecione **Construa o seu próprio modelo no editor.**

6. Selecione **carregar o ficheiro** e, em seguida, siga as instruções para carregar otemplate.js **no** ficheiro que descarregou na última secção.

7. No **template.jsno** ficheiro, nomeie a conta de armazenamento alvo, definindo o valor padrão do nome da conta de armazenamento. Este exemplo define o valor predefinido do nome da conta de armazenamento para `mytargetaccount` .
    
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
    Para obter códigos de localização da região, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/)  O código para uma região é o nome da região sem espaços, **central dos EUA central.**  =  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para implementar o modelo utilizando o PowerShell:

1. No **template.jsno** ficheiro, nomeie a conta de armazenamento alvo, definindo o valor padrão do nome da conta de armazenamento. Este exemplo define o valor predefinido do nome da conta de armazenamento para `mytargetaccount` .
    
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

2. Editar a propriedade de **localização** no **template.jsem** arquivo para a região alvo. Este exemplo define a região-alvo para `eastus` .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Pode obter códigos de região executando o comando [Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Mover

Implemente o modelo para criar uma nova conta de armazenamento na região de destino. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Guarde o **template.jsarquivado.**

2. Insira ou selecione os valores da propriedade:

- **Assinatura**: Selecione uma subscrição Azure.

- **Grupo de recursos**: selecione **Criar novo** e dê um nome ao grupo de recursos.

- **Localização**: Selecione uma localização Azure.

3. Clique no **I concorda com os termos e condições indicados acima** da caixa de verificação e, em seguida, clique no botão **'Escolha'.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Obtenha o ID de subscrição onde pretende implementar o IP público alvo com [a subscrição Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

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

### <a name="configure-the-new-storage-account"></a>Configurar a nova conta de armazenamento

Algumas funcionalidades não serão exportadas para um modelo, pelo que terá de as adicionar à nova conta de armazenamento. 

A tabela seguinte lista estas funcionalidades juntamente com as orientações para as adicionar à nova conta de armazenamento.

| Funcionalidade    | Orientação    |
|--------|-----------|
| **Políticas de gestão do ciclo de vida** | [Gerir o ciclo de vida do Armazenamento de Blobs do Azure](../blobs/storage-lifecycle-management-concepts.md) |
| **Web sites estáticos** | [Hospedar um site estático no Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Subscrições de eventos** | [Reagir aos eventos de armazenamento de Blobs](../blobs/storage-blob-event-overview.md) |
| **Alertas** | [Criar, visualizar e gerir alertas de registo de atividades utilizando o Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md) |
| **Rede de Entrega de Conteúdos (CDN)** | [Utilize o Azure CDN para aceder a blobs com domínios personalizados em HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Se configurar um CDN para a conta de armazenamento de origem, basta alterar a origem da sua CDN existente para o ponto final do serviço blob primário (ou o ponto final do website estático primário) da sua nova conta. 

### <a name="move-data-to-the-new-storage-account"></a>Mover dados para a nova conta de armazenamento

A AzCopy é a ferramenta preferida para mover os seus dados. Está otimizada para o desempenho.  Uma maneira mais rápida consiste em copiar os dados diretamente entre os servidores de armazenamento para que o AzCopy não utilize a largura de banda de rede do computador. Utilize o AzCopy na linha de comandos ou como parte de um script personalizado. Ver [Começar com a AzCopy.](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Também pode utilizar a Azure Data Factory para transferir os seus dados. Fornece uma interface de utilizador intuitiva. Para utilizar a Azure Data Factory, consulte qualquer uma destas ligações:. 

  - [Copiar dados de/para o Armazenamento de blobs do Azure com o Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
  - [Copiar os dados de/para o Azure Data Lake Storage Gen2 com o Azure Data Factory](/azure/data-factory/connector-azure-data-lake-storage)
  - [Copiar dados de/para o Armazenamento de Ficheiros do Azure com o Azure Data Factory](/azure/data-factory/connector-azure-file-storage)
  - [Copiar dados de/para o armazenamento de Tabelas do Azure com o Azure Data Factory](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Eliminar ou limpar

Após a implantação, se quiser recomeçar, pode eliminar a conta de armazenamento do alvo e repetir os passos descritos nas secções [Preparar](#prepare) e [Mover](#move) deste artigo.

Para consolidar as alterações e concluir a mudança de uma conta de armazenamento, elimine a conta de armazenamento de origem.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para remover uma conta de armazenamento com o portal do Azure:

1. No portal Azure, expanda o menu do lado esquerdo para abrir o menu de **serviços** e escolha as contas de Armazenamento para exibir a lista das suas contas de armazenamento.

2. Localize a conta de armazenamento do alvo para apagar e clique com o botão **Mais** **(...**) no lado direito da listagem.

3. **Selecione Eliminar** e confirmar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para remover o grupo de recursos e os seus recursos associados, incluindo a nova conta de armazenamento, utilize o comando [Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você mudou uma conta de armazenamento Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou subscrição](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover VMs do Azure para outra região](../../site-recovery/azure-to-azure-tutorial-migrate.md)