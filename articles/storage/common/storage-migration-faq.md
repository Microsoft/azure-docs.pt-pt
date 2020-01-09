---
title: Perguntas frequentes sobre migração de armazenamento do Azure | Microsoft Docs
description: Respostas para perguntas comuns sobre como migrar o armazenamento do Azure
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 8b805f01722c58d60e994a3a6b2440bb115b1bfa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351285"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Perguntas frequentes sobre a migração de armazenamento do Azure

Este artigo responde a perguntas comuns sobre a migração de armazenamento do Azure. 

## <a name="faq"></a>FAQ

**Como fazer criar um script para copiar arquivos de um contêiner para outro?**

Para copiar arquivos entre contêineres, você pode usar AzCopy. Veja o seguinte exemplo:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy usa a [API de cópia de blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) para copiar cada arquivo no contêiner.  
  
Você pode usar qualquer máquina virtual ou máquina local que tenha acesso à Internet para executar o AzCopy. Você também pode usar uma agenda do lote do Azure para fazer isso automaticamente, mas é mais complicado.  
  
O script de automação foi projetado para implantação de Azure Resource Manager em vez de manipulação de conteúdo de armazenamento. Para obter mais informações, consulte [implantar recursos com modelos e Azure PowerShell do Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

**Há algum encargo para copiar dados entre dois compartilhamentos de arquivos na mesma conta de armazenamento na mesma região?**

Não. Não há nenhum encargo para esse processo.

**Como fazer fazer backup de minha conta de armazenamento inteira em outra conta de armazenamento?**

Não há nenhuma opção para fazer backup de uma conta de armazenamento inteira diretamente. Mas você pode mover manualmente o contêiner na conta de armazenamento para outra conta usando AzCopy ou Gerenciador de Armazenamento. As etapas a seguir mostram como usar o AzCopy para mover o contêiner:  
 

1.  Instale a ferramenta de linha de comando [AzCopy](storage-use-azcopy.md) . Essa ferramenta ajuda a mover o arquivo VHD entre contas de armazenamento.

2.  Depois de instalar o AzCopy no Windows usando o instalador, abra uma janela do prompt de comando e, em seguida, navegue até a pasta de instalação do AzCopy em seu computador. Por predefinição, o AzCopy é instalado para **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** ou **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Execute o comando a seguir para mover o contêiner. Você deve substituir o texto pelos valores reais.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: forneça o URI para a conta de armazenamento de origem (até o contêiner).  
    - `/Dest`: forneça o URI para a conta de armazenamento de destino (até o contêiner).  
    - `/SourceKey`: forneça a chave primária para a conta de armazenamento de origem. Você pode copiar essa chave do portal do Azure selecionando a conta de armazenamento.  
    - `/DestKey`: forneça a chave primária para a conta de armazenamento de destino. Você pode copiar essa chave do portal selecionando a conta de armazenamento.

Depois de executar esse comando, os arquivos de contêiner são movidos para a conta de armazenamento de destino.

> [!NOTE]
> A CLI do AzCopy não funciona junto com a opção de **padrão** quando você copia de um blob do Azure para outro.
>
> Você pode copiar e editar diretamente o comando AzCopy e fazer uma verificação cruzada para garantir que o **padrão** corresponda à origem. Verifique também se os curingas **/s** estão em vigor. Para obter mais informações, consulte [parâmetros de AzCopy](storage-use-azcopy.md).

**Como fazer mover dados de um contêiner de armazenamento para outro?**

Siga estes passos.

1.  Crie o contêiner (pasta) no blob de destino.

2.  Use [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) para copiar o conteúdo do contêiner de blob original para um contêiner de BLOBs diferente.

**Como fazer criar um script do PowerShell para mover dados de um compartilhamento de arquivos do Azure para outro no armazenamento do Azure?**

Use AzCopy para mover os dados de um compartilhamento de arquivos do Azure para outro no armazenamento do Azure. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como fazer carregar arquivos. csv grandes para o armazenamento do Azure?**

Use AzCopy para carregar arquivos. csv grandes para o armazenamento do Azure. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Preciso mover os logs da unidade D para a minha conta de armazenamento do Azure todos os dias. Como fazer automatizar isso?**

Você pode usar AzCopy e criar uma tarefa em Agendador de Tarefas. Carregue arquivos em uma conta de armazenamento do Azure usando um script de lote AzCopy. Para obter mais informações, consulte [como configurar e executar tarefas de inicialização para um serviço de nuvem](../../cloud-services/cloud-services-startup-tasks.md).

**Como fazer mover minha conta de armazenamento entre assinaturas?**

Use AzCopy para mover sua conta de armazenamento entre assinaturas. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso mover cerca de 10 TB de dados para o armazenamento em outra região?**

Use AzCopy para mover os dados. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como copiar dados do local para o armazenamento do Azure?**

Use AzCopy para copiar os dados. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso mover dados do local para os arquivos do Azure?**

Use AzCopy para mover dados. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como fazer mapear uma pasta de contêiner em uma máquina virtual?**

Use um compartilhamento de arquivos do Azure.

**Como fazer fazer backup do armazenamento de arquivos do Azure?**

Não há solução de backup. No entanto, os arquivos do Azure também dão suporte à cópia assíncrona. Portanto, você pode copiar arquivos:

- De um compartilhamento para outro compartilhamento dentro de uma conta de armazenamento ou para uma conta de armazenamento diferente.

- De um compartilhamento para um contêiner de BLOBs dentro de uma conta de armazenamento ou para uma conta de armazenamento diferente.

Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md).

**Como fazer mover os discos gerenciados para outra conta de armazenamento?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Siga estes passos.

1.  Pare a máquina virtual à qual o disco gerenciado está anexado.

2.  Copie o VHD do disco gerenciado de uma área para outra executando o seguinte script de Azure PowerShell:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Crie um disco gerenciado usando o arquivo VHD em outra região para a qual você copiou o VHD. Para fazer isso, execute o seguinte script de Azure PowerShell:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ``` 

Para obter mais informações sobre como implantar uma máquina virtual de um disco gerenciado, consulte [CreateVmFromManagedOsDisk. ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Como posso baixar 1-2 TB de dados do portal do Azure?**

Use AzCopy para baixar os dados. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como fazer alterar o local secundário para a região da Europa para uma conta de armazenamento?**

Ao criar uma conta de armazenamento, você seleciona a região primária da conta. A seleção da região secundária é baseada na região primária e não pode ser alterada. Para obter mais informações, consulte [armazenamento com redundância geográfica (GRS): replicação entre regiões para o armazenamento do Azure](storage-redundancy.md).

**Onde posso obter mais informações sobre o Azure Criptografia do Serviço de Armazenamento (SSE)?**  
  
Consulte os seguintes artigos:

-  [Guia de segurança do Armazenamento do Azure](storage-security-guide.md)

-  [Criptografia do Serviço de Armazenamento do Azure para dados em repouso](storage-service-encryption.md)

**Como fazer mover ou baixar dados de uma conta de armazenamento?**

Use AzCopy para baixar os dados. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).


**Como fazer criptografar dados em uma conta de armazenamento?**

Depois de habilitar a criptografia em uma conta de armazenamento, os dados existentes não são criptografados. Para criptografar os dados existentes, você deve carregá-los novamente na conta de armazenamento.

Use AzCopy para copiar os dados para uma conta de armazenamento diferente e, em seguida, mova os dados de volta. Você também pode usar [a criptografia em repouso](storage-service-encryption.md).

**Como posso baixar um VHD em um computador local, além de usar a opção de download no portal?**

Você pode usar [Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) para baixar um VHD.

**Há algum pré-requisito para alterar a replicação de uma conta de armazenamento de um armazenamento com redundância geográfica para o armazenamento com redundância local?**

Não. 

**Como fazer acessar o armazenamento com redundância de arquivos do Azure?**

O armazenamento com redundância geográfica com acesso de leitura é necessário para acessar o armazenamento redundante. No entanto, os arquivos do Azure dão suporte apenas ao armazenamento com redundância local e ao armazenamento com redundância geográfica padrão que não permite acesso somente leitura. 

**Como fazer mudar de uma conta de armazenamento Premium para uma conta de armazenamento Standard?**

Siga estes passos.

1.  Crie uma conta de armazenamento padrão. (Ou use uma conta de armazenamento padrão existente em sua assinatura.)

2.  Baixe o AzCopy. Execute um dos comandos AzCopy a seguir.
      
    Para copiar discos inteiros na conta de armazenamento:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Para copiar apenas um disco, forneça o nome do disco em **padrão**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
A operação pode levar várias horas para ser concluída.

Para certificar-se de que a transferência foi concluída com êxito, examine o contêiner da conta de armazenamento de destino na portal do Azure. Depois que os discos forem copiados para a conta de armazenamento padrão, você poderá anexá-los à máquina virtual como um disco existente. Para obter mais informações, consulte [como anexar um disco de dados gerenciado a uma máquina virtual do Windows no portal do Azure](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Como fazer converter para o armazenamento Premium do Azure para um compartilhamento de arquivos?**

O armazenamento Premium não é permitido em um compartilhamento de arquivos do Azure.

**Como fazer atualizar de uma conta de armazenamento Standard para uma conta de armazenamento Premium? Como fazer fazer downgrade de uma conta de armazenamento Premium para uma conta de armazenamento Standard?**

Você deve criar a conta de armazenamento de destino, copiar dados da conta de origem para a conta de destino e, em seguida, excluir a conta de origem. Você pode usar uma ferramenta como AzCopy para copiar os dados.

Se você tiver máquinas virtuais, deverá executar etapas adicionais antes de migrar os dados da conta de armazenamento. Para obter mais informações, consulte [migrando para o armazenamento Premium do Azure (discos não gerenciados)](storage-migration-to-premium-storage.md).

**Como fazer mudar de uma conta de armazenamento clássica para uma conta de armazenamento de Azure Resource Manager?**

Você pode usar o cmdlet **move-AzureStorageAccount** . Esse cmdlet tem várias etapas (validar, preparar, confirmar). Você pode validar a movimentação antes de fazer isso.

Se você tiver máquinas virtuais, deverá executar etapas adicionais antes de migrar os dados da conta de armazenamento. Para obter mais informações, consulte [migrar recursos de IaaS do clássico para o Azure Resource Manager usando Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Como fazer baixar dados em um computador baseado em Linux de uma conta de armazenamento do Azure ou carregar dados de um computador Linux?**

Você pode usar o CLI do Azure.

- Baixar um único blob:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Carregar um único blob: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Como posso conceder a outras pessoas acesso aos meus recursos de armazenamento?**

Para conceder a outras pessoas acesso aos recursos de armazenamento:

-   Use um token SAS (assinatura de acesso compartilhado) para fornecer acesso a um recurso. 

-   Forneça um usuário com a chave primária ou secundária para a conta de armazenamento. Para obter mais informações, consulte [gerenciar chaves de acesso da conta de armazenamento](storage-account-keys-manage.md).

-   Altere a política de acesso para permitir acesso anônimo. Para obter mais informações, consulte [conceder permissões de usuários anônimos a contêineres e blobs](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Onde o AzCopy está instalado?**

-   Se você acessar o AzCopy na linha de comando Armazenamento do Microsoft Azure, digite **AzCopy**. A linha de comando é instalada junto com AzCopy.

-   Se você instalou a versão de 32 bits, ela está localizada aqui: **% ProgramFiles (x86)%\\Microsoft SDKs\\Azure\\AzCopy**.

-   Se você instalou a versão de 64 bits, ela está localizada aqui: **% ProgramFiles%\\Microsoft SDKs\\Azure\\AzCopy**.

**Para uma conta de armazenamento replicada (como armazenamento com redundância de zona, armazenamento com redundância geográfica ou armazenamento com redundância geográfica com acesso de leitura), como fazer para acessar dados armazenados na região secundária?**

-   Se você estiver usando armazenamento com redundância de zona ou armazenamento com redundância geográfica, não será possível acessar dados da região secundária, a menos que você inicie um failover para essa região. Para obter mais informações sobre o processo de failover, consulte [recuperação de desastre e failover de conta de armazenamento (versão prévia) no armazenamento do Azure](storage-disaster-recovery-guidance.md).

-   Se você estiver usando o armazenamento com redundância geográfica com acesso de leitura, poderá acessar dados da região secundária a qualquer momento. Utilize um dos métodos seguintes:  
      
    - **AzCopy**: acrescente **-Secondary** ao nome da conta de armazenamento na URL para acessar o ponto de extremidade secundário. Por exemplo:  
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **Token SAS**: Use um token SAS para acessar dados do ponto de extremidade. Para obter mais informações, consulte [usando assinaturas de acesso compartilhado](storage-sas-overview.md).

**Como fazer usar um domínio personalizado HTTPS com minha conta de armazenamento? Por exemplo, como fazer com que "https:\//mystorageaccountname.blob.core.windows.net/images/image.gif" apareça como "https:\//www.contoso.com/images/image.gif"?**

No momento, não há suporte para SSL em contas de armazenamento com domínios personalizados.
Mas você pode usar domínios personalizados não HTTPS. Para obter mais informações, consulte [configurar um nome de domínio personalizado para o ponto de extremidade do armazenamento de BLOBs](../blobs/storage-custom-domain-name.md).

**Como fazer usar o FTP para acessar dados que estão em uma conta de armazenamento?**

Não é possível acessar uma conta de armazenamento diretamente usando o FTP. No entanto, você pode configurar uma máquina virtual do Azure e, em seguida, instalar um servidor FTP na máquina virtual. Você pode fazer com que o servidor FTP armazene arquivos em um compartilhamento de arquivos do Azure ou em um disco de dados que esteja disponível para a máquina virtual.

Se você quiser apenas baixar dados sem precisar usar Gerenciador de Armazenamento ou um aplicativo semelhante, talvez seja possível usar um token SAS. Para obter mais informações, consulte [usando assinaturas de acesso compartilhado](storage-sas-overview.md).

**Como fazer migrar blobs de uma conta de armazenamento para outra?**

 Você pode fazer isso usando nosso [script de migração de blob](../scripts/storage-common-transfer-between-storage-accounts.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
