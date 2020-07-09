---
title: Azure Storage migração FAQ / Microsoft Docs
description: Respostas a perguntas comuns sobre a migração do Armazenamento Azure
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: f0272d53c5fc4c565baf5d7105bd6e1b4a0ef535
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114606"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Perguntas mais frequentes sobre a migração do Armazenamento do Azure

Este artigo responde a perguntas comuns sobre a migração do Azure Storage.

## <a name="copy-upload-or-download"></a>Copiar, carregar ou descarregar

**Como crio um guião para copiar ficheiros de um contentor para outro?**

Para copiar ficheiros entre contentores, pode utilizar o AzCopy. Veja o seguinte exemplo:

```azurepowershell-interactive
AzCopy /Source:https://xxx.blob.core.windows.net/xxx
/Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
/S
```

A AzCopy utiliza a [API copy Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) para copiar cada ficheiro no recipiente.  

Pode utilizar qualquer máquina virtual ou máquina local que tenha acesso à Internet para executar a AzCopy. Também pode utilizar um horário do Azure Batch para o fazer automaticamente, mas é mais complicado.  

O script de automatização foi concebido para a implementação do Azure Resource Manager em vez da manipulação de conteúdos de armazenamento. Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

**Existe alguma taxa para copiar dados entre duas ações de ficheiros na mesma conta de armazenamento na mesma região?**

Não. Não há qualquer custo para este processo.

**Como posso descarregar 1-2 TB de dados do portal Azure?**

Utilize o AzCopy para descarregar os dados. Para obter mais informações, consulte [os dados de transferência com a AzCopy sobre os](storage-use-azcopy.md) dados do Windows e da Transferência com a [AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso transferir um VHD para uma máquina local, além de utilizar a opção de descarregamento no portal?**

Você pode usar [o Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para descarregar um VHD.

**Como faço o download de dados para um computador baseado em Linux a partir de uma conta de armazenamento Azure ou faço upload de dados de uma máquina Linux?**

Pode usar o Azure CLI.

- Faça o download de uma única bolha:

    ```azurecli-interactive
    azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"
    ```

- Faça o upload de uma única bolha:

    ```azurecli-interactive
    azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"
    ```

**Como posso migrar blobs de uma conta de armazenamento para outra?**

 Pode fazê-lo usando o nosso [script de migração Blob.](../scripts/storage-common-transfer-between-storage-accounts.md)
 
## <a name="migration-or-backup"></a>Migração ou backup

**Como posso mover dados de um contentor de armazenamento para outro?**

Siga estes passos.

1.  Crie o recipiente (pasta) na bolha de destino.

2.  Utilize [o AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) para copiar o conteúdo do recipiente original para um recipiente de bolhas diferente.

**Como posso criar um script PowerShell para mover dados de uma partilha de ficheiros Azure para outra no Azure Storage?**

Utilize o AzCopy para mover os dados de uma partilha de ficheiros Azure para outra no Azure Storage. Para obter mais informações, consulte [os dados de transferência com a AzCopy sobre os](storage-use-azcopy.md) dados do Windows e da Transferência com a [AzCopy no Linux](storage-use-azcopy-linux.md).

**Como faço o upload de ficheiros .csv grandes para o Azure Storage?**

Utilize o AzCopy para enviar ficheiros .csv grandes para o Azure Storage. Para obter mais informações, consulte [os dados de transferência com a AzCopy sobre os](storage-use-azcopy.md) dados do Windows e da Transferência com a [AzCopy no Linux](storage-use-azcopy-linux.md).

**Tenho de mudar os registos da unidade D para a minha conta de armazenamento do Azure todos os dias. Como posso automatizar isto?**

Pode utilizar o AzCopy e criar uma tarefa no Agendador de Tarefas. Faça upload de ficheiros para uma conta de armazenamento Azure utilizando um script de lote AzCopy. Para obter mais informações, consulte [Como configurar e executar tarefas de arranque para um serviço de cloud](../../cloud-services/cloud-services-startup-tasks.md).

**Como mudo a minha conta de armazenamento entre assinaturas?**

Utilize o AzCopy para mover a sua conta de armazenamento entre subscrições. Para obter mais informações, consulte [os dados de transferência com a AzCopy sobre os](storage-use-azcopy.md) dados do Windows e da Transferência com a [AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso mover cerca de 10 TB de dados para armazenamento noutra região?**

Use a AzCopy para mover os dados. Para obter mais informações, consulte [os dados de transferência com a AzCopy sobre os](storage-use-azcopy.md) dados do Windows e da Transferência com a [AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso copiar dados das instalações para o Azure Storage?**

Utilize o AzCopy para copiar os dados. Para obter mais informações, consulte [os dados de transferência com a AzCopy sobre os](storage-use-azcopy.md) dados do Windows e da Transferência com a [AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso transferir dados do local para os Ficheiros Azure?**

Use a AzCopy para mover dados. Para obter mais informações, consulte [os dados de transferência com a AzCopy sobre os](storage-use-azcopy.md) dados do Windows e da Transferência com a [AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso mover discos geridos para outra conta de armazenamento?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Siga estes passos.

1.  Pare a máquina virtual a que o disco gerido está ligado.

2.  Copie o VHD do disco gerido de uma área para outra executando o seguinte script Azure PowerShell:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Crie um disco gerido utilizando o ficheiro VHD noutra região para a qual copiou o VHD. Para isso, execute o seguinte script Azure PowerShell:  

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

**Como mexo ou descarrego dados de uma conta de armazenamento?**

Utilize o AzCopy para descarregar os dados. Para obter mais informações, consulte [os dados de transferência com a AzCopy sobre os](storage-use-azcopy.md) dados do Windows e da Transferência com a [AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso passar de uma conta de armazenamento premium para uma conta de armazenamento padrão?**

Siga estes passos.

1.  Criar uma conta de armazenamento padrão. (Ou utilize uma conta de armazenamento padrão existente na sua subscrição.)

2.  Descarregue a AzCopy. Executar um dos seguintes comandos AzCopy.

    Para copiar discos inteiros na conta de armazenamento:

    ```azurepowershell-interactive
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
    /Dest:https://destaccount.blob.core.windows.net/mycontainer2
    /SourceKey:key1 /DestKey:key2 /S
    ```

    Para copiar apenas um disco, forneça o nome do disco em **Pattern**:

    ```azurepowershell-interactive
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
    /Dest:https://destaccount.blob.core.windows.net/mycontainer2
    /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd
    ```

A operação pode levar várias horas para terminar.

Para se certificar de que a transferência terminou com sucesso, examine o contentor da conta de armazenamento de destino no portal Azure. Depois de os discos serem copiados para a conta de armazenamento padrão, pode fixá-los à máquina virtual como um disco existente. Para obter mais informações, consulte [como anexar um disco de dados gerido a uma máquina virtual do Windows no portal Azure](../../virtual-machines/windows/attach-managed-disk-portal.md).  

**Como posso passar de uma conta de armazenamento clássica para uma conta de armazenamento Azure Resource Manager?**

Pode utilizar o **cmdlet Move-AzureStorageAccount.** Este cmdlet tem vários passos (validar, preparar, comprometer). Pode validar o movimento antes de o fazer.

Se tiver máquinas virtuais, deve tomar medidas adicionais antes de migrar os dados da conta de armazenamento. Para obter mais informações, consulte [os recursos da Migrate IaaS do clássico para o Azure Resource Manager utilizando a Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Como faço para fazer o back up da minha conta de armazenamento para outra conta de armazenamento?**

Não há opção para fazer o apoio de uma conta de armazenamento inteira diretamente. Mas pode mover manualmente o recipiente nessa conta de armazenamento para outra conta utilizando o AzCopy ou o Storage Explorer. Os seguintes passos mostram como utilizar o AzCopy para mover o recipiente:  

1.  Instale a ferramenta de linha de comando [AzCopy.](storage-use-azcopy.md) Esta ferramenta ajuda-o a mover o ficheiro VHD entre as contas de armazenamento.

2.  Depois de instalar o AzCopy no Windows utilizando o instalador, abra uma janela de pedido de comando e, em seguida, navegue na pasta de instalação AzCopy no seu computador. Por predefinição, o AzCopy é instalado em **%ProgramFiles(x86%\Microsoft SDKs\Azure\Azure\AzCopy** ou **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Executar o seguinte comando para mover o recipiente. Deve substituir o texto pelos valores reais.   

    ```azurepowershell-interactive
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
    /Dest:https://destaccount.blob.core.windows.net/mycontainer2
    /SourceKey:key1 /DestKey:key2 /S
    ```

    - `/Source`: Forneça o URI para a conta de armazenamento de origem (até ao recipiente).  
    - `/Dest`: Forneça o URI para a conta de armazenamento alvo (até ao recipiente).  
    - `/SourceKey`: Fornecer a chave principal para a conta de armazenamento de origem. Pode copiar esta chave a partir do portal Azure selecionando a conta de armazenamento.  
    - `/DestKey`: Fornecer a chave principal para a conta de armazenamento do alvo. Pode copiar esta chave do portal selecionando a conta de armazenamento.

Depois de executar este comando, os ficheiros do contentor são transferidos para a conta de armazenamento do alvo.

> [!NOTE]
> O CLI AzCopy não funciona em conjunto com o interruptor **Padrão** quando se copia de uma bolha Azure para outra.
>
> Pode copiar e editar diretamente o comando AzCopy e verificar para se certificar de que o **Padrão** corresponde à fonte. Certifique-se também de que os wildcards **/S** estão em vigor. Para obter mais informações, consulte os [parâmetros AzCopy](storage-use-azcopy.md).

**Como faço o reforço do armazenamento de ficheiros Azure?**

Não há solução de reserva. No entanto, o Azure Files também suporta cópia assíncrona. Assim, pode copiar ficheiros:

- De uma parte para outra parte dentro de uma conta de armazenamento ou para uma conta de armazenamento diferente.

- De uma parte para um recipiente de bolhas dentro de uma conta de armazenamento ou para uma conta de armazenamento diferente.

Para obter mais informações, consulte [os dados de transferência com a AzCopy no Windows](storage-use-azcopy.md).

## <a name="configuration"></a>Configuração

**Como posso mudar a localização secundária para a região europeia por uma conta de armazenamento?**

Quando cria uma conta de armazenamento, seleciona a região primária para a conta. A seleção da região secundária baseia-se na região primária e não pode ser alterada. Para obter mais informações, consulte [o armazenamento geo-redundante (GRS): Replicação inter-regional para o armazenamento Azure](storage-redundancy.md).

**Onde posso obter mais informações sobre encriptação do Azure Storage?**  

Consulte os seguintes artigos:

- [Azure Storage encryption for data at rest](storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
- [Utilize chaves geridas pelo cliente com cofre de chaves Azure para gerir a encriptação de armazenamento Azure](encryption-customer-managed-keys.md)
- [Fornecer uma chave de encriptação sobre um pedido para o armazenamento Blob (pré-visualização)](encryption-customer-provided-keys.md)

**Como criptografo dados numa conta de armazenamento?**

Depois de ativar a encriptação numa conta de armazenamento, os dados existentes não são encriptados. Para encriptar os dados existentes, deve enviá-lo novamente para a conta de armazenamento.

Utilize o AzCopy para copiar os dados para uma conta de armazenamento diferente e, em seguida, mover os dados de volta. Também pode utilizar [a encriptação em repouso.](storage-service-encryption.md)

**Existem requisitos para alterar a replicação de uma conta de armazenamento de armazenamento geo-redundante para armazenamento localmente redundante?**

Não.

**Como posso converter-me ao Azure Premium Storage por uma partilha de ficheiros?**

O Armazenamento Premium não é permitido numa partilha de ficheiros Azure.

**Como faço o upgrade de uma conta de armazenamento padrão para uma conta de armazenamento premium? Como faço a redução de uma conta de armazenamento premium para uma conta de armazenamento padrão?**

Tem de criar a conta de armazenamento de destino, copiar dados da conta de origem para a conta de destino e, em seguida, apagar a conta de origem. Pode utilizar uma ferramenta como a AzCopy para copiar os dados.

Se tiver máquinas virtuais, deve tomar medidas adicionais antes de migrar os dados da conta de armazenamento. Para obter mais informações, consulte [migração para o Armazenamento Azure Premium (discos não geridos)](storage-migration-to-premium-storage.md).

**Como posso dar a outras pessoas acesso aos meus recursos de armazenamento?**

Para dar a outras pessoas acesso aos recursos de armazenamento:

- Utilize um token de assinatura de acesso partilhado (SAS) para fornecer acesso a um recurso.

- Forneça a um utilizador a chave primária ou secundária para a conta de armazenamento. Para obter mais informações, consulte [gerir as teclas de acesso à conta de armazenamento.](storage-account-keys-manage.md)

- Altere a política de acesso para permitir o acesso anónimo. Para obter mais informações, consulte [permissões de utilizadores anónimos para contentores e bolhas.](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)

**Onde está instalado o AzCopy?**

- Se aceder ao AzCopy a partir da linha de comando do Microsoft Azure Storage, **digite AzCopy**. A linha de comando é instalada ao lado da AzCopy.

- Se instalou a versão de 32 bits, está localizada aqui: **%ProgramFiles (x86)% \\ Microsoft SDKs \\ Azure \\ AzCopy**.

- Se instalou a versão de 64 bits, está localizada aqui: **%ProgramFiles% \\ Microsoft SDKs \\ Azure \\ AzCopy**.

**Como uso um domínio personalizado HTTPS com a minha conta de armazenamento? Por exemplo, como faço com que "https: \/ /mystorageaccountname.blob.core.windows.net/images/image.gif" apareçam como "https: \/ /www.contoso.com/images/image.gif"?**

O TLS/SSL não é atualmente suportado em contas de armazenamento com domínios personalizados.
Mas pode utilizar domínios personalizados não-HTTPS. Para obter mais informações, consulte [configurar um nome de domínio personalizado para o seu ponto final de armazenamento Blob](../blobs/storage-custom-domain-name.md).

## <a name="access-to-storage"></a>Acesso ao armazenamento

**Como mapear uma pasta de contentores numa máquina virtual?**

Use uma partilha de ficheiros Azure.

**Como acesso ao armazenamento redundante da Azure Files?**

O armazenamento geo-redundante de acesso à leitura é necessário para aceder ao armazenamento redundante. No entanto, a Azure Files suporta apenas armazenamento localmente redundante e armazenamento geo-redundante padrão que não permite o acesso apenas à leitura.

**Para uma conta de armazenamento replicada (como armazenamento redundante de zona, armazenamento geo-redundante ou armazenamento geo-redundante de acesso à leitura), como aceso aos dados armazenados na região secundária?**

- Se estiver a usar armazenamento redundante de zona ou armazenamento geo-redundante, não pode aceder a dados da região secundária a menos que inicie uma falha nessa região. Para obter mais informações sobre o processo de failover, consulte [a recuperação de desastres e a falha da conta de armazenamento.](storage-disaster-recovery-guidance.md)

- Se estiver a usar o armazenamento geo-redundante de acesso à leitura, pode aceder a dados da região secundária a qualquer momento. Utilizar um dos seguintes métodos:  

  - **AzCopy**: Apêndice **-secundário** ao nome da conta de armazenamento no URL para aceder ao ponto final secundário. Por exemplo:  

      `https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd`

  - **Ficha SAS**: Utilize um token SAS para aceder aos dados a partir do ponto final. Para obter mais informações, consulte [utilização de assinaturas de acesso partilhado.](storage-sas-overview.md)

**Como uso a FTP para aceder a dados que estão numa conta de armazenamento?**

Não há como aceder diretamente a uma conta de armazenamento utilizando FTP. No entanto, pode configurar uma máquina virtual Azure e, em seguida, instalar um servidor FTP na máquina virtual. Pode ter os ficheiros da loja de servidores FTP numa partilha de Ficheiros Azure ou num disco de dados disponível para a máquina virtual.

Se quiser apenas descarregar dados sem ter de usar o Storage Explorer ou uma aplicação similar, poderá utilizar um token SAS. Para obter mais informações, consulte [utilização de assinaturas de acesso partilhado.](storage-sas-overview.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
