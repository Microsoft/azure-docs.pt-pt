---
title: Como eliminar e exportar dados pessoais da Azure DevTest Labs
description: Saiba como apagar e exportar dados pessoais do serviço Azure DevLast Labs para suportar as suas obrigações ao abrigo do Regulamento Geral de Proteção de Dados (RGPD).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2c44b2f3aa6f2dfad18ed53804842a5dad8bd94a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483521"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportar ou eliminar dados pessoais da Azure DevTest Labs
Este artigo fornece passos para a eliminação e exportação de dados pessoais do serviço Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Que dados pessoais a DevTest Labs recolhe?
A DevTest Labs recolhe duas peças principais de dados pessoais do utilizador. São: endereço de e-mail do utilizador e ID de objeto de utilizador. Esta informação é fundamental para que o serviço forneça funcionalidades em serviço aos administradores de laboratório e aos utilizadores de laboratório.

### <a name="user-email-address"></a>Endereço de e-mail do utilizador
A DevTest Labs utiliza o endereço de e-mail do utilizador para enviar notificações de email de encerramento automático aos utilizadores do laboratório. O e-mail notifica os utilizadores da sua máquina que estão a ser desligadas. Os utilizadores podem adiar ou ignorar o encerramento se assim o desejarem. Configura o endereço de e-mail ao nível do laboratório ou ao nível de VM.

**Definição de e-mail no laboratório:**

![Definição de e-mail ao nível do laboratório](./media/personal-data-delete-export/lab-user-email.png)

**Definição de e-mail no VM:**

![Definição de e-mail ao nível de VM](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID do objeto do utilizador
A DevTest Labs usa o ID do objeto do utilizador para mostrar as tendências de custos mensais e o custo da informação de recursos para os administradores de laboratório. Permite-lhes controlar os custos e gerir os limiares para o seu Laboratório. 

**Tendência de custo estimada para o mês civil em curso:** 
 ![ Tendência de custos estimada para o mês civil em curso](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Custo estimado mensal por recurso:** 
 ![ Custo estimado mensal por recurso](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Por que precisamos destes dados pessoais?
O serviço DevTest Labs utiliza os dados pessoais para fins operacionais. Estes dados são fundamentais para que o serviço entregue funcionalidades-chave. Se definir uma política de retenção no endereço de e-mail do utilizador, os utilizadores de laboratório não recebem notificações de correio eletrónico de paragem automática oportuna após a sua utilização do endereço de correio eletrónico para análise automática. Da mesma forma, o administrador de laboratório não consegue visualizar as tendências de custos mensais e o custo por recurso para máquinas em seus laboratórios se os IDs do objeto do utilizador forem eliminados com base numa política de retenção. Por isso, estes dados devem ser conservados enquanto o recurso do utilizador estiver ativo no Laboratório.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Como posso ter o sistema para esquecer os meus dados pessoais?
Como utilizador de laboratório, se quiser que estes dados pessoais seja eliminado, pode fazê-lo eliminando o recurso correspondente no Laboratório. O serviço DevTest Labs anonimiza os dados pessoais apagados 30 dias após a sua execção pelo utilizador.

Por exemplo, se eliminar o seu VM ou remover o seu endereço de e-mail, o serviço DevTest Labs anonimiza estes dados 30 dias após a eliminação do recurso. A política de retenção de 30 dias após a eliminação é para garantir que fornecemos uma projeção de custos de um mês ao longo do mês para a administração do laboratório.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Como posso solicitar uma exportação dos meus dados pessoais?
Pode exportar dados pessoais e de utilização em laboratório utilizando o portal Azure ou o PowerShell. Os dados são exportados como dois ficheiros CSV diferentes:

- **disks.csv** - contém informações sobre os discos que estão a ser usados pelos diferentes VMs
- **virtualmachines.csv** contém informações sobre os VMs no laboratório.

### <a name="azure-portal"></a>Portal do Azure
Como utilizador de laboratório, pode solicitar uma exportação sobre os dados pessoais que os serviços da DevTest Labs armazenam. Para solicitar uma exportação, navegue para a opção **de dados pessoais** na página **geral** do seu laboratório. Selecione o botão **de exportação Request** inicia a criação de um ficheiro excel descarregado na conta de armazenamento do seu administrador lab. Em seguida, pode contactar o seu administrador de laboratório para ver estes dados.

1. Selecione **Dados pessoais** no menu esquerdo. 

    ![Página de dados pessoais](./media/personal-data-delete-export/personal-data-page.png)
2. Selecione o **grupo de recursos** que contém o laboratório.

    ![Selecionar o grupo de recursos](./media/personal-data-delete-export/select-resource-group.png)
3. Selecione a **conta de armazenamento** no grupo de recursos.
4. Na página da **conta de Armazenamento,** selecione **Blobs**.

    ![Selecione azulejos Blobs](./media/personal-data-delete-export/select-blobs-tile.png)
5. Selecione o recipiente denominado **labresourceusage** na lista de contentores.

    ![Selecione recipiente blob](./media/personal-data-delete-export/select-blob-container.png)
6. Selecione a **pasta** com o nome do seu laboratório. Encontra **ficheiros csv** para **discos** e **máquinas virtuais** no seu laboratório nesta pasta. Pode descarregar estes ficheiros csv, filtrar o conteúdo do utilizador do laboratório que solicita um acesso e partilhá-los com eles.

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

Os componentes-chave da amostra acima referida são:

- O comando Invoke-AzureRmResourceAction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Dois parâmetros de ação
    - **blobStorageAbsoluteSasUri** - A conta de armazenamento URI com o token assinatura de acesso partilhado (SAS). No script PowerShell, este valor pode ser passado em vez da chave de armazenamento.
    - **usageStartDate** - A data inicial para retirar dados, sendo a data de fim a data atual em que a ação é executada. A granularidade está ao nível do dia, por isso mesmo que adicione informações sobre o tempo, será ignorada.

### <a name="exported-data---a-closer-look"></a>Dados exportados - um olhar mais atento
Agora vamos olhar mais de perto os dados exportados. Como mencionado anteriormente, uma vez que os dados são exportados com sucesso, haverá dois ficheiros CSV. 

O **virtualmachines.csv** contém as seguintes colunas de dados:

| Nome da coluna | Description |
| ----------- | ----------- | 
| SubscriptionId | O identificador de assinatura em que o laboratório existe. |
| LabUId | Identificador GUID único para o laboratório. |
| Nome lab | O nome do laboratório. |
| LabResourceId | Identificação de recursos de laboratório totalmente qualificado. |
| ResourceGroupName | Nome do grupo de recursos que contém o VM | 
| ResourceId | ID de recursos totalmente qualificado para o VM. |
| ResourceUId | GUID para o VM |
| Name | Nome da máquina virtual. |
| CreatedTime | A data-data em que o VM foi criado. |
| DeletedDate | A data-data em que o VM foi apagado. Se está vazio, a eliminação ainda não ocorreu. |
| ResourceOwner | Dono do VM. Se o valor estiver vazio, então ou é um VM reclamado ou criado por um diretor de serviço. |
| Preços Mais Novos | Nível de preços do VM |
| Estatísticas de Recursos | Estado de disponibilidade do VM. Ativo, se ainda existe ou Inativo, se o VM tiver sido eliminado. |
| ComputeResourceId | Identificador de recursos de computação de máquina virtual totalmente qualificado. |
| Reclamação | Definido como verdadeiro se o VM é um VM reclamado | 
| EnvironmentId | O identificador de recursos ambientais dentro do qual a máquina Virtual foi criada. Está vazio quando o VM não foi criado como parte de um recurso ambiental. |
| ExpiraçãoDate | Data de validade para o VM. Está programado para esvaziar, se não tiver sido fixada uma data de validade.
| GaleriaImageReferênciaVersão |  Versão da imagem base VM. |
| GalleryImageReferenceOffer | Oferta da imagem base VM. |
| GaleriaImageReferencePublisher | Editor da imagem base VM. |
| GaleriaImageReferencesku | Sku da imagem base VM |
| GaleriaImageReferenceOsType | Tipo de SO da imagem base VM |
| CustomImageId | ID totalmente qualificado da imagem personalizada base VM. |

As colunas de dados contidas na **disks.csv** estão listadas abaixo:

| Nome da coluna | Description | 
| ----------- | ----------- | 
| SubscriptionId | ID da assinatura que contém o laboratório |
| LabUId | GUID para o laboratório |
| Nome lab | Nome do laboratório | 
| LabResourceId | ID de recursos totalmente qualificado para o laboratório | 
| ResourceGroupName | Nome do grupo de recursos que contém o laboratório | 
| ResourceId | ID de recursos totalmente qualificado para o VM. |
| ResourceUId | GUID para o VM |
 |Name | O nome do disco anexo |
| CreatedTime |A data e a hora em que o disco de dados foi criado. |
| DeletedDate | A data e a hora em que o disco de dados foi eliminado. |
| Estatísticas de Recursos | Estado do recurso. Ativo, se o recurso existir. Inativo, quando apagado. |
| Nome do DiscoBlob | Nome blob para o disco de dados. |
| DiskSizeGB | O tamanho do disco de dados. |
| DiskType | Tipo do disco de dados. 0 para Standard, 1 para Premium. |
| AlugadoByVmId | Identificação de recursos do VM ao qual o disco de dados foi anexado. |


> [!NOTE]
> Se estiver a lidar com vários laboratórios e quiser obter informações globais, as duas colunas-chave são o **LabUID** e o **ResourceUId**, que são os ids únicos em todas as subscrições.

Os dados exportados podem ser manipulados e visualizados utilizando ferramentas, como SQL Server, Power BI, etc. Esta funcionalidade é especialmente útil quando pretende reportar o uso do seu laboratório à sua equipa de gestão que pode não estar a utilizar a mesma subscrição do Azure que o faz.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 

- [Definir políticas para um laboratório](devtest-lab-set-lab-policy.md)
- [Perguntas mais frequentes](devtest-lab-faq.md)
