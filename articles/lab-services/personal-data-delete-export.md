---
title: Como excluir e exportar dados pessoais do Azure DevTest Labs
description: Saiba como excluir e exportar dados pessoais do serviço do Azure DevLast Labs para dar suporte às suas obrigações no Regulamento Geral sobre a Proteção de Dados (GDPR).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c87e2fb534480bbf9bbe625d67782e5a11eda18c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169686"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportar ou excluir dados pessoais do Azure DevTest Labs
Este artigo fornece etapas para excluir e exportar dados pessoais do serviço de Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Quais dados pessoais o DevTest Labs coleta?
O DevTest Labs coleta duas partes principais de dados pessoais do usuário. Eles são: endereço de email do usuário e ID de objeto de usuário. Essas informações são essenciais para que o serviço forneça recursos no serviço para administradores de laboratório e usuários de laboratório.

### <a name="user-email-address"></a>Endereço de email do usuário
O DevTest Labs usa o endereço de email do usuário para enviar notificações por email de desligamento automático para os usuários do laboratório. O email notifica os usuários sobre seu computador que está sendo desligado. Os usuários podem adiar ou ignorar o desligamento se desejarem fazer isso. Você configura o endereço de email no nível de laboratório ou no nível da VM.

**Configurando email no laboratório:**

![Configurando email no nível do laboratório](./media/personal-data-delete-export/lab-user-email.png)

**Configurando email na VM:**

![Configurando email no nível da VM](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID de objeto de usuário
O DevTest Labs usa a ID de objeto de usuário para mostrar as tendências de custo mês a mês e o custo pelas informações de recursos para os administradores de laboratório. Ele permite controlar os custos e gerenciar os limites de seu laboratório. 

**Tendência de custo estimado para o mês do calendário atual:** 
![tendência de custo estimado para o mês do calendário atual](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Custo estimado acumulado no mês por recurso:** 
![custo estimado de mês até a data por recurso](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Por que precisamos desses dados pessoais?
O serviço DevTest Labs usa os dados pessoais para fins operacionais. Esses dados são essenciais para que o serviço forneça os principais recursos. Se você definir uma política de retenção no endereço de email do usuário, os usuários do laboratório não receberão notificações de email de desligamento automático em tempo hábil depois que seu endereço de email for excluído do nosso sistema. Da mesma forma, o administrador do laboratório não poderá exibir as tendências de custo do mês a mês e o custo por recurso para os computadores em seus laboratórios se as IDs de objeto do usuário forem excluídas com base em uma política de retenção. Portanto, esses dados precisam ser retidos, desde que o recurso do usuário esteja ativo no laboratório.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Como posso ter o sistema para esquecer meus dados pessoais?
Como um usuário do laboratório, se você quiser que esses dados pessoais sejam excluídos, poderá fazer isso excluindo o recurso correspondente no laboratório. O serviço DevTest Labs torna anônimas os dados pessoais excluídos 30 dias após sua exclusão pelo usuário.

Por exemplo, se você excluir sua VM ou removido seu endereço de email, o serviço DevTest Labs torna anônimas esses dados 30 dias após a exclusão do recurso. A política de retenção de 30 dias após a exclusão é certificar-se de que fornecemos uma projeção de custo de mês a mês precisa para o administrador do laboratório.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Como posso solicitar uma exportação em meus dados pessoais?
Você pode exportar dados de uso pessoais e de laboratório usando o portal do Azure ou o PowerShell. Os dados são exportados como dois arquivos CSV diferentes:

- **disks. csv** -contém informações sobre os discos que estão sendo usados pelas diferentes VMs
- **VirtualMachines. csv** -contém informações sobre as VMs no laboratório.

### <a name="azure-portal"></a>Portal do Azure
Como um usuário do laboratório, você pode solicitar uma exportação dos dados pessoais que o serviço do DevTest Labs armazena. Para solicitar uma exportação, navegue até a opção **dados pessoais** na página **visão geral** do seu laboratório. Selecionar o botão de **exportação de solicitação** ativa a criação de um arquivo do Excel para download na conta de armazenamento do administrador do laboratório. Em seguida, você pode entrar em contato com o administrador do laboratório para exibir esses dados.

1. Selecione **dados pessoais** no menu à esquerda. 

    ![Página de dados pessoais](./media/personal-data-delete-export/personal-data-page.png)
2. Selecione o **grupo de recursos** que contém o laboratório.

    ![Selecionar grupo de recursos](./media/personal-data-delete-export/select-resource-group.png)
3. Selecione a **conta de armazenamento** no grupo de recursos.
4. Na página **conta de armazenamento** , selecione **BLOBs**.

    ![Selecionar bloco de BLOBs](./media/personal-data-delete-export/select-blobs-tile.png)
5. Selecione o contêiner chamado **labresourceusage** na lista de contêineres.

    ![Selecionar contêiner de BLOB](./media/personal-data-delete-export/select-blob-container.png)
6. Selecione a **pasta** nomeada após o laboratório. Você encontra arquivos **CSV** para **discos** e **máquinas virtuais** em seu laboratório nesta pasta. Você pode baixar esses arquivos CSV, filtrar o conteúdo para o usuário do laboratório que solicita um acesso e compartilhá-lo com eles.

    ![Baixar arquivo CSV](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

Os principais componentes no exemplo acima são:

- O comando Invoke-AzureRmResourceAction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Dois parâmetros de ação
    - **blobStorageAbsoluteSasUri** -o URI da conta de armazenamento com o token SAS (assinatura de acesso compartilhado). No script do PowerShell, esse valor pode ser passado em vez da chave de armazenamento.
    - **usageStartDate** -a data de início para efetuar pull de dados, com a data de término sendo a data atual em que a ação é executada. A granularidade está no nível do dia, portanto, mesmo que você adicione informações de tempo, elas serão ignoradas.

### <a name="exported-data---a-closer-look"></a>Dados exportados-uma análise mais detalhada
Agora, vamos examinar mais de perto os dados exportados. Conforme mencionado anteriormente, depois que os dados são exportados com êxito, haverá dois arquivos CSV. 

O **VirtualMachines. csv** contém as seguintes colunas de dados:

| Nome da coluna | Descrição |
| ----------- | ----------- | 
| SubscriptionId | O identificador de assinatura no qual o laboratório existe. |
| LabUId | Identificador de GUID exclusivo para o laboratório. |
| LabName | Nome do laboratório. |
| LabResourceId | ID de recurso de laboratório totalmente qualificado. |
| ResourceGroupName | Nome do grupo de recursos que contém a VM | 
| ResourceId | ID de recurso totalmente qualificado para a VM. |
| ResourceUId | GUID da VM |
| Nome | Nome da máquina virtual. |
| CreatedTime | A data e hora em que a VM foi criada. |
| DeletedDate | A data e hora em que a VM foi excluída. Se estiver vazio, a exclusão ainda não ocorreu. |
| ResourceOwner | Proprietário da VM. Se o valor estiver vazio, será uma VM declarável ou criada por uma entidade de serviço. |
| pricingTier | Tipo de preço da VM |
| ResourceStatus | Estado de disponibilidade da VM. Ativo, se ainda existir ou inativo, se a VM tiver sido excluída. |
| ComputeResourceId | Identificador de recurso de computação de máquina virtual totalmente qualificado. |
| Declarável | Definir como true se a VM for uma VM declarável | 
| Ambienteid | O identificador de recurso de ambiente no qual a máquina virtual foi criada. Ele está vazio quando a VM não foi criada como parte de um recurso de ambiente. |
| ExpirationDate | Data de validade da VM. Ele será definido como vazio se uma data de expiração não tiver sido definida.
| GalleryImageReferenceVersion |  Versão da imagem base da VM. |
| GalleryImageReferenceOffer | Oferta da imagem base da VM. |
| GalleryImageReferencePublisher | Editor da imagem base da VM. |
| GalleryImageReferenceSku | SKU da imagem base da VM |
| GalleryImageReferenceOsType | Tipo de so da imagem base da VM |
| CustomImageId | ID totalmente qualificada da imagem personalizada base da VM. |

As colunas de dados contidas em **disks. csv** estão listadas abaixo:

| Nome da coluna | Descrição | 
| ----------- | ----------- | 
| SubscriptionId | ID da assinatura que contém o laboratório |
| LabUId | GUID do laboratório |
| LabName | nome do laboratório | 
| LabResourceId | ID de recurso totalmente qualificado para o laboratório | 
| ResourceGroupName | Nome do grupo de recursos que contém o laboratório | 
| ResourceId | ID de recurso totalmente qualificado para a VM. |
| ResourceUId | GUID da VM |
 |Nome | O nome do disco anexado |
| CreatedTime |A data e a hora em que o disco de dados foi criado. |
| DeletedDate | A data e a hora em que o disco de dados foi excluído. |
| ResourceStatus | Status do recurso. Ativo, se o recurso existir. Inativo, quando excluído. |
| DiskBlobName | Nome do blob para o disco de dados. |
| DiskSizeGB | O tamanho do disco de dados. |
| DiskType | Tipo de disco de dados. 0 para Standard, 1 para Premium. |
| LeasedByVmId | ID de recurso da VM à qual o disco de dados foi anexado. |


> [!NOTE]
> Se você estiver lidando com vários laboratórios e quiser obter informações gerais, as duas colunas de chave serão o **LabUID** e o **ResourceUId**, que são as IDs exclusivas entre as assinaturas.

Os dados exportados podem ser manipulados e visualizados usando ferramentas, como SQL Server, Power BI, etc. Esse recurso é especialmente útil quando você deseja relatar o uso de seu laboratório para sua equipe de gerenciamento que talvez não esteja usando a mesma assinatura do Azure que você faz.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas mais frequentes](devtest-lab-faq.md)
