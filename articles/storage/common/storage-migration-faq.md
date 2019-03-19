---
title: FAQ sobre migração de armazenamento do Azure | Documentos da Microsoft
description: Respostas a perguntas comuns sobre migração de armazenamento do Azure
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 2272414c185388df0ccbc9e21b7d166432fc7a52
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58110414"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Perguntas mais frequentes sobre a migração de armazenamento do Azure

Este artigo responde a perguntas comuns sobre a migração de armazenamento do Azure. 

## <a name="faq"></a>FAQ

**Como posso criar um script para copiar ficheiros de um contentor para outro?**

Para copiar ficheiros entre contentores, pode utilizar o AzCopy. Veja o seguinte exemplo:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy utiliza a [API do Blob de cópia](https://docs.microsoft.com/rest/api/storageservices/copy-blob) para copiar cada ficheiro no contentor.  
  
Pode usar qualquer máquina virtual ou máquina local com acesso à internet para executar o AzCopy. Também pode utilizar uma agenda do Azure Batch para fazê-lo automaticamente, mas é mais complicado.  
  
O script de automação destina-se a implementação do Azure Resource Manager, em vez de manipulação de conteúdo de armazenamento. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Existe algum custo de cópia de dados entre duas partilhas de ficheiros na mesma conta de armazenamento na mesma região?**

Não. Não existe nenhum custo associado para que este processo.

**Como posso fazer cópia de segurança minha conta de armazenamento completo para outra conta de armazenamento?**

Não existe nenhuma opção para fazer backup de uma conta de armazenamento completo diretamente. Mas pode manualmente mover o contentor nessa conta de armazenamento para outra conta com o AzCopy ou o Explorador de armazenamento. Os passos seguintes mostram como utilizar o AzCopy para mover o contentor:  
 

1.  Instalar o [AzCopy](storage-use-azcopy.md) ferramenta da linha de comandos. Essa ferramenta ajuda a mudar o ficheiro VHD entre contas de armazenamento.

2.  Depois de instalar o AzCopy no Windows através do instalador, abra uma janela de Prompt de comando e, em seguida, navegue até à pasta de instalação do AzCopy no seu computador. Por predefinição, o AzCopy é instalado para **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** ou **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Execute o seguinte comando para mover o contentor. Tem de substituir o texto com os valores reais.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Forneça o URI para a conta de armazenamento de origem (até o contentor).  
    - `/Dest`: Forneça o URI para a conta de armazenamento de destino (até o contentor).  
    - `/SourceKey`: Forneça a chave primária para a conta de armazenamento de origem. Pode copiar esta chave a partir do portal do Azure ao selecionar a conta de armazenamento.  
    - `/DestKey`: Forneça a chave primária para a conta de armazenamento de destino. Pode copiar esta chave a partir do portal ao selecionar a conta de armazenamento.

Depois de executar este comando, os ficheiros de contentor são movidos para a conta de armazenamento de destino.

> [!NOTE]
> A CLI do AzCopy não funciona em conjunto com o **padrão** mudar quando copia a partir do Azure de um blob para outro.
>
> Pode diretamente copiar e editar o comando do AzCopy e certificar-se de que uma verificação cruzada **padrão** corresponde a origem. Também certificar-se de que **/S** carateres universais estão em vigor. Para obter mais informações, consulte [AzCopy parâmetros](storage-use-azcopy.md).

**Como posso mover dados de um contentor de armazenamento para outra?**

Siga estes passos.

1.  Crie o contentor (pasta) no blob de destino.

2.  Uso [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) para copiar o conteúdo do contêiner de blob original para um contentor de blob diferente.

**Como posso criar um script do PowerShell para mover dados de uma partilha de ficheiros Azure para outra no armazenamento do Azure?**

Utilize o AzCopy para mover os dados de uma partilha de ficheiros do Azure para outra no armazenamento do Azure. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso carregar ficheiros. csv grande ao armazenamento do Azure?**

Utilize o AzCopy para carregar ficheiros. csv grandes para o armazenamento do Azure. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com AzCopy no Linux](storage-use-azcopy-linux.md).

**Tenho de mover os registos de unidade D, para minha conta de armazenamento do Azure todos os dias. Como posso automatizar Isto?**

Pode utilizar o AzCopy e crie uma tarefa no agendador de tarefas. Carregar ficheiros para uma conta de armazenamento do Azure, utilizando um script de comandos do AzCopy. Para obter mais informações, consulte [como configurar e executar tarefas de arranque para um serviço cloud](../../cloud-services/cloud-services-startup-tasks.md).

**Como posso mover minha conta de armazenamento entre subscrições?**

Utilize o AzCopy para mover a sua conta de armazenamento entre subscrições. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso mover 10 TB de dados para o armazenamento em outra região?**

Utilize o AzCopy para mover os dados. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com AzCopy no Linux](storage-use-azcopy-linux.md).

**Como copiar dados no local para o armazenamento do Azure?**

Utilize o AzCopy para copiar os dados. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com AzCopy no Linux](storage-use-azcopy-linux.md).

**Como mover dados do local para ficheiros do Azure?**

Utilize o AzCopy para mover dados. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso mapear uma pasta de contentor numa máquina virtual?**

Utilize uma partilha de ficheiros do Azure.

**Como posso fazer cópia de segurança de armazenamento de ficheiros do Azure?**

Existe uma solução de cópia de segurança. No entanto, os ficheiros do Azure também suporta cópia assíncrona. Por isso, pode copiar arquivos:

- De um compartilhamento para outra partilha de dentro de uma conta de armazenamento ou para uma conta de armazenamento diferente.

- De uma partilha para um contentor de BLOBs numa conta de armazenamento ou para outra conta de armazenamento.

Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md).

**Como faço para mover os discos geridos para outra conta de armazenamento?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Siga estes passos.

1.  Pare a máquina virtual que o disco gerido está ligado a.

2.  Copie o VHD de disco gerido de uma área para outra ao executar o seguinte script do Azure PowerShell:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Crie um disco gerido com o ficheiro VHD noutra região onde copiou o VHD. Para tal, execute o seguinte script do Azure PowerShell:  

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

Para obter mais informações sobre como implementar uma máquina virtual a partir de um disco gerido, consulte [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Como transferir 1 a 2 TB de dados do portal do Azure?**

Utilize o AzCopy para transferir os dados. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso alterar a localização secundária para a região da Europa para uma conta de armazenamento?**

Quando criar uma conta de armazenamento, selecione a região primária para a conta. A seleção da região secundária baseia-se a região primária e não pode ser alterado. Para obter mais informações, consulte [armazenamento georredundante (GRS): A replicação do armazenamento do Azure entre regiões](storage-redundancy.md).

**Onde posso obter mais informações sobre a encriptação de serviço de armazenamento do Azure (SSE)?**  
  
Consulte os seguintes artigos:

-  [Guia de segurança do Armazenamento do Azure](storage-security-guide.md)

-  [Encriptação do serviço de armazenamento do Azure para dados Inativos](storage-service-encryption.md)

**Como mover ou transferir dados de uma conta de armazenamento?**

Utilize o AzCopy para transferir os dados. Para obter mais informações, consulte [transferir dados com AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com AzCopy no Linux](storage-use-azcopy-linux.md).


**Como posso encriptar dados de uma conta de armazenamento?**

Depois de ativar a encriptação numa conta de armazenamento, os dados existentes não são encriptados. Para encriptar os dados existentes, deve carregá-lo novamente para a conta de armazenamento.

Utilize o AzCopy para copiar os dados para outra conta de armazenamento e, em seguida, mover os dados de volta. Também pode utilizar [encriptação em repouso](storage-service-encryption.md).

**Como transferir um VHD para um computador local, senão ao utilizar a opção de download no portal?**

Pode usar [Explorador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) para transferir um VHD.

**Existem pré-requisitos para alterar a replicação de uma conta de armazenamento de armazenamento com redundância geográfica para armazenamento localmente redundante?**

Não. 

**Como posso acessar o armazenamento com redundância de ficheiros do Azure?**

Armazenamento georredundante com acesso de leitura é necessária para aceder ao armazenamento redundante. No entanto, os ficheiros do Azure suporta armazenamento apenas localmente redundante e o armazenamento georredundante standard, que não permite acesso só de leitura. 

**Como posso mover de uma conta de armazenamento premium para uma conta de armazenamento standard?**

Siga estes passos.

1.  Crie uma conta de armazenamento standard. (Ou utilizar uma conta de armazenamento standard existente na sua subscrição.)

2.  Baixe o AzCopy. Execute um dos seguintes comandos do AzCopy.
      
    Para copiar discos todos na conta de armazenamento:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Para copiar apenas um disco, forneça o nome do disco nos **padrão**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
A operação poderá demorar várias horas a concluir.

Para certificar-se de que a transferência foi concluída com êxito, examine o contentor de conta de armazenamento de destino no portal do Azure. Depois dos discos são copiados para a conta de armazenamento standard, pode ligá-los para a máquina virtual como um disco existente. Para obter mais informações, consulte [como anexar um disco de dados geridos para uma máquina virtual do Windows no portal do Azure](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Como posso converter para armazenamento Premium do Azure para uma partilha de ficheiros?**

O armazenamento Premium não é permitido numa partilha de ficheiros do Azure.

**Como posso atualizar de uma conta de armazenamento standard para uma conta de armazenamento premium? Como posso passar de uma conta de armazenamento premium para uma conta de armazenamento standard?**

Tem de criar a conta de armazenamento de destino, copiar dados da conta de origem para a conta de destino e, em seguida, eliminar a conta de origem. Pode usar uma ferramenta como o AzCopy para copiar os dados.

Se tiver máquinas virtuais, tem de efetuar passos adicionais antes de migrar os dados da conta de armazenamento. Para obter mais informações, consulte [migrar para o armazenamento Premium do Azure (discos não geridos)](storage-migration-to-premium-storage.md).

**Como posso mover de uma conta de armazenamento clássica a uma conta de armazenamento do Azure Resource Manager?**

Pode utilizar o **movimentação AzStorageAccount** cmdlet. Este cmdlet tem vários passos (validar, preparar, consolidar). Pode validar a movimentação antes de fazê-lo.

Se tiver máquinas virtuais, tem de efetuar passos adicionais antes de migrar os dados da conta de armazenamento. Para obter mais informações, consulte [migrar recursos de IaaS do clássico para o Azure Resource Manager com o Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Como transferir dados para um computador baseado em Linux a partir de uma conta de armazenamento do Azure, ou carregar dados a partir de uma máquina Linux?**

Pode utilizar a CLI do Azure.

- Transferir um blob único:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Carregar um blob único: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Como posso dar outras pessoas acesso a recursos de armazenamento?**

Para dar acesso a outras pessoas para os recursos de armazenamento:

-   Utilize um token de assinatura (SAS) de acesso partilhado para fornecer acesso a um recurso. 

-   Forneça um utilizador com a chave primária ou secundária para a conta de armazenamento. Para obter mais informações, consulte [gerir a sua conta de armazenamento](storage-account-manage.md#access-keys).

-   Altere a política de acesso para permitir o acesso anônimo. Para obter mais informações, consulte [conceder permissões de utilizadores anónimos a contentores e blobs](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Onde está instalado o AzCopy?**

-   Se acessar o AzCopy a partir da linha de comando de armazenamento do Microsoft Azure, escreva **AzCopy**. A linha de comandos é instalada em conjunto com o AzCopy.

-   Se instalou a versão de 32 bits, é localizado aqui: **% ProgramFiles(x86) %\\SDKs da Microsoft\\Azure\\AzCopy**.

-   Se instalou a versão de 64 bits, é localizado aqui: **% ProgramFiles %\\SDKs da Microsoft\\Azure\\AzCopy**.

**Para uma conta de armazenamento replicado (como o armazenamento com redundância de zona, armazenamento georredundante ou armazenamento georredundante com acesso de leitura), como posso acessar dados armazenados na região secundária?**

-   Se estiver a utilizar o armazenamento com redundância de zona ou armazenamento georredundante, não é possível aceder a dados da região secundária, a menos que inicie uma ativação pós-falha para essa região. Para obter mais informações sobre o processo de ativação pós-falha, consulte [desastre recuperação e o armazenamento de conta ativação pós-falha (pré-visualização) no armazenamento do Azure](storage-disaster-recovery-guidance.md).

-   Se estiver a utilizar o armazenamento georredundante com acesso de leitura, pode aceder aos dados da região secundária em qualquer altura. Utilize um dos seguintes métodos:  
      
    - **AzCopy**: Acrescentar **-secundário** ao nome da conta de armazenamento no URL para aceder ao ponto final secundário. Por exemplo:  
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS token**: Utilize um token SAS para aceder a dados a partir do ponto final. Para obter mais informações, consulte [assinaturas de acesso partilhado do Using](storage-dotnet-shared-access-signature-part-1.md).

**Como posso utilizar um domínio personalizado HTTPS com a minha conta de armazenamento? Por exemplo, como me certifico de "<https://mystorageaccountname.blob.core.windows.net/images/image.gif>"aparecem como"<https://www.contoso.com/images/image.gif>"?**

SSL não é atualmente suportado em contas de armazenamento com domínios personalizados.
Mas pode usar domínios personalizados de não-HTTPS. Para obter mais informações, consulte [configurar um nome de domínio personalizado para o ponto final de armazenamento de BLOBs](../blobs/storage-custom-domain-name.md).

**Como utilizar o FTP para aceder a dados que está numa conta de armazenamento?**

Não é possível aceder a uma conta de armazenamento diretamente através de FTP. No entanto, pode configurar uma máquina virtual do Azure e, em seguida, instalar um servidor FTP na máquina virtual. Pode ter o servidor FTP armazenar os ficheiros numa partilha de ficheiros do Azure ou num disco de dados que está disponível para a máquina virtual.

Se pretender que apenas transferir dados sem ter de utilizar o Explorador de armazenamento ou um aplicativo semelhante, poderá conseguir utilizar um token SAS. Para obter mais informações, consulte [assinaturas de acesso partilhado do Using](storage-dotnet-shared-access-signature-part-1.md).

**Como posso migrar Blobs de uma conta de armazenamento para outra?**

 Pode fazê-lo com o nosso [script de migração de BLOBs](../scripts/storage-common-transfer-between-storage-accounts.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.
