---
title: Como eliminar e exportar dados pessoais da Azure DevTest Labs
description: Saiba como eliminar e exportar dados pessoais do serviço Azure DevLast Labs para apoiar as suas obrigações ao abrigo do Regulamento Geral de Proteção de Dados (RGPD).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169686"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportar ou eliminar dados pessoais da Azure DevTest Labs
Este artigo fornece medidas para a apagar e exportar dados pessoais do serviço Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Que dados pessoais a DevTest Labs recolhe?
A DevTest Labs recolhe duas peças principais de dados pessoais do utilizador. São: endereço de e-mail do utilizador e ID do objeto do utilizador. Esta informação é fundamental para que o serviço forneça funcionalidades em serviço a administrações de laboratório e utilizadores de laboratório.

### <a name="user-email-address"></a>Endereço de e-mail do utilizador
A DevTest Labs utiliza o endereço de e-mail do utilizador para enviar notificações de e-mail de encerramento automático para utilizadores de laboratório. O e-mail identifica os utilizadores da sua máquina a ser desligada. Os utilizadores podem adiar ou saltar a paralisação se assim o desejarem. Configura o endereço de e-mail ao nível do laboratório ou ao nível de VM.

**Definição de e-mail no laboratório:**

![Definição de e-mail ao nível do laboratório](./media/personal-data-delete-export/lab-user-email.png)

**Definição de e-mail no VM:**

![Definição de e-mail ao nível vM](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID do objeto do utilizador
A DevTest Labs usa o ID do objeto do utilizador para mostrar tendências de custos mensais e custos por informações de recursos para administrações de laboratório. Permite-lhes controlar os custos e gerir os limiares para o laboratório. 

**Tendência de custos estimada para o mês civil em curso:**
![Tendência estimada de custos para o mês civil em curso](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Custo estimado**
![mensal mente por recurso: Custo estimado mensal por recurso](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Por que precisamos destes dados pessoais?
O serviço DevTest Labs utiliza os dados pessoais para fins operacionais. Estes dados são fundamentais para que o serviço entregue as principais funcionalidades. Se definir uma política de retenção no endereço de e-mail do utilizador, os utilizadores do laboratório não recebem notificações de e-mail de encerramento automático atempada após o seu endereço de e-mail ser eliminado do nosso sistema. Da mesma forma, a administração do laboratório não pode ver as tendências de custos mensais e os custos por recurso para máquinas nos seus laboratórios se as identidades do objeto do utilizador forem eliminadas com base numa política de retenção. Portanto, estes dados precisam de ser conservados enquanto o recurso do utilizador estiver ativo no Laboratório.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Como posso ter o sistema para esquecer os meus dados pessoais?
Como utilizador de laboratório, se gosta de ter estes dados pessoais eliminados, pode fazê-lo eliminando o recurso correspondente no Laboratório. O serviço DevTest Labs anonimiza os dados pessoais apagados 30 dias após o seu lançamento.

Por exemplo, se eliminar o seu VM ou remover o seu endereço de e-mail, o serviço DevTest Labs anonimiza estes dados 30 dias após a eliminação do recurso. A política de retenção de 30 dias após a eliminação é para garantir que fornecemos uma projeção exata de custos mensais ao administrador do laboratório.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Como posso pedir uma exportação dos meus dados pessoais?
Pode exportar dados pessoais e de utilização de laboratório utilizando o portal Azure ou powerShell. Os dados são exportados como dois ficheiros CSV diferentes:

- **disks.csv** - contém informações sobre os discos que estão a ser utilizados pelos diferentes VMs
- **virtualmachines.csv** - contém informações sobre os VMs no laboratório.

### <a name="azure-portal"></a>Portal do Azure
Como utilizador de laboratório, pode solicitar uma exportação dos dados pessoais que o serviço DevTest Labs armazena. Para solicitar uma exportação, navegue para a opção **de dados pessoais** na página **de visão geral** do seu laboratório. Selecione o botão **de exportação do Pedido** inicia a criação de um ficheiro excel descarregado na conta de armazenamento do seu administrador lab. Em seguida, pode contactar o administrador do seu laboratório para ver estes dados.

1. Selecione **dados pessoais** no menu esquerdo. 

    ![Página de dados pessoais](./media/personal-data-delete-export/personal-data-page.png)
2. Selecione o **grupo de recursos** que contém o laboratório.

    ![Selecionar grupo de recursos](./media/personal-data-delete-export/select-resource-group.png)
3. Selecione a **conta de armazenamento** no grupo de recursos.
4. Na página da **conta de Armazenamento,** selecione **Blobs**.

    ![Selecione azulejoblos](./media/personal-data-delete-export/select-blobs-tile.png)
5. Selecione o recipiente denominado **utilização** de recursos laboratoriais na lista de recipientes.

    ![Selecione recipiente de bolha](./media/personal-data-delete-export/select-blob-container.png)
6. Selecione a **pasta** com o nome do seu laboratório. Encontra **ficheiros** **CSV** para discos e **máquinas virtuais** no seu laboratório nesta pasta. Pode descarregar estes ficheiros csv, filtrar o conteúdo para o utilizador do laboratório que solicita um acesso e partilhá-lo com eles.

    ![Baixar ficheiro CSV](./media/personal-data-delete-export/download-csv-file.png)

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

Os componentes-chave da amostra acima são:

- O comando Invoke-AzureRmResourceAction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Dois parâmetros de ação
    - **blobStorageAbsoluteSasUri** - A conta de armazenamento URI com o símbolo de Assinatura de Acesso Partilhado (SAS). No script PowerShell, este valor poderia ser passado em vez da chave de armazenamento.
    - **utilizaçãoStartDate** - A data de início para puxar os dados, sendo a data de fim a data atual em que a ação é executada. A granularidade está ao nível do dia, por isso mesmo que adicione informações de tempo, será ignorada.

### <a name="exported-data---a-closer-look"></a>Dados exportados - um olhar mais atento
Agora vamos ver mais de perto os dados exportados. Como mencionado anteriormente, uma vez exportados com sucesso os dados, haverá dois ficheiros CSV. 

As **virtualmachines.csv** contém as seguintes colunas de dados:

| Nome da coluna | Descrição |
| ----------- | ----------- | 
| SubscriptionId | O identificador de assinatura em que o laboratório existe. |
| Labuídio | Identificador GUIA único para o laboratório. |
| Nome de laboratório | Nome do laboratório. |
| LabResourceid | Identificação de recursos de laboratório totalmente qualificados. |
| ResourceGroupName | Nome do grupo de recursos que contém o VM | 
| ResourceId | Id de recursos totalmente qualificado sacar para o VM. |
| Resourceuid | GUID para o VM |
| Nome | Nome de máquina virtual. |
| CreatedTime | A data-data em que o VM foi criado. |
| Data eliminada | A data-data em que o VM foi apagado. Se está vazio, a eliminação ainda não ocorreu. |
| Proprietário de Recursos | Dono da VM. Se o valor estiver vazio, então ou é um VM repreensível ou criado por um diretor de serviço. |
| PreçoTier | Nível de preços do VM |
| Estatuto de Recursos | Estado de disponibilidade do VM. Ativo, se ainda existir ou Inativo, se o VM tiver sido eliminado. |
| ComputeResourceId | Identificador de recursos de computação virtual totalmente qualificado. |
| Reclamação | Definido para verdade se o VM é um VM reivindicavel | 
| AmbienteId | O identificador de recursos ambientais no qual a máquina Virtual foi criada. Está vazio quando o VM não foi criado como parte de um recurso ambiental. |
| Data de Validade | Data de validade para o VM. Está programado para esvaziar, se não tiver sido fixada uma data de validade.
| GalleryImageReferenceVersion |  Versão da imagem base VM. |
| GalleryImageReferenceOffer | Oferta da imagem base vm. |
| GalleryImageReferencePublisher | Editor da imagem base vm. |
| GalleryImageReferenceSku | Sku da imagem base VM |
| GaleriaImageReferenceosType | Tipo OS da imagem base VM |
| CustomImageid | Identificação totalmente qualificada da imagem personalizada da base VM. |

As colunas de dados contidas em **discos.csv** estão listadas abaixo:

| Nome da coluna | Descrição | 
| ----------- | ----------- | 
| SubscriptionId | ID da subscrição que contém o laboratório |
| Labuídio | GUID para o laboratório |
| Nome de laboratório | Nome do laboratório | 
| LabResourceid | Id de recursos totalmente qualificados para o laboratório | 
| ResourceGroupName | Nome do grupo de recursos que contém o laboratório | 
| ResourceId | Id de recursos totalmente qualificado sacar para o VM. |
| Resourceuid | GUID para o VM |
 |Nome | O nome do disco anexo |
| CreatedTime |A data e a hora em que o disco de dados foi criado. |
| Data eliminada | A data e a hora em que o disco de dados foi eliminado. |
| Estatuto de Recursos | Estado do recurso. Ativo, se o recurso existir. Inativo, quando apagado. |
| DiskBlobName | Nome blob para o disco de dados. |
| DiskSizeGB | O tamanho do disco de dados. |
| DiskType | Tipo do disco de dados. 0 para Standard, 1 para Premium. |
| LeasedByVmId | Identificação de recursos do VM ao qual o disco de dados foi anexado. |


> [!NOTE]
> Se você está lidando com vários laboratórios e quer obter informações globais, as duas colunas-chave são o **LabUID** e o **ResourceUId**, que são os ids únicos em subscrições.

Os dados exportados podem ser manipulados e visualizados usando ferramentas, como O Servidor SQL, Power BI, etc. Esta funcionalidade é especialmente útil quando pretende reportar o uso do seu laboratório à sua equipa de gestão que pode não estar a usar a mesma subscrição Azure que você.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas frequentes](devtest-lab-faq.md)
