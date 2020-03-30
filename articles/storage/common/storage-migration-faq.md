---
title: Migração de Armazenamento Azure FAQ [ Microsoft Docs
description: Respostas a perguntas comuns sobre o armazenamento azure migratório
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 1445d74e3050ffd6da7c45037df552f4bee9acf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116664"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Perguntas mais frequentes sobre a migração do Armazenamento do Azure

Este artigo responde a perguntas comuns sobre a migração do Armazenamento Azure.

## <a name="copy-upload-or-download"></a>Copiar, carregar ou descarregar

**Como posso criar um guião para copiar ficheiros de um contentor para outro?**

Para copiar ficheiros entre contentores, pode utilizar o AzCopy. Veja o seguinte exemplo:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

A AzCopy utiliza a [API Copy Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) para copiar cada ficheiro no recipiente.  

Pode utilizar qualquer máquina virtual ou máquina local que tenha acesso à Internet para executar o AzCopy. Também pode utilizar um horário do Lote Azure para o fazer automaticamente, mas é mais complicado.  

O script de automação foi concebido para a implementação do Gestor de Recursos Azure em vez da manipulação de conteúdo de armazenamento. Para mais informações, consulte [A implantação de recursos com modelos de Gestor de Recursos e PowerShell Azure](../../azure-resource-manager/templates/deploy-powershell.md).

**Existe algum encargo para copiar dados entre duas ações de ficheiros na mesma conta de armazenamento na mesma região?**

Não. Não há nenhuma acusação para este processo.

**Como posso descarregar 1-2 TB de dados do portal Azure?**

Utilize o AzCopy para descarregar os dados. Para mais informações, consulte os [dados de transferência com a AzCopy no Windows](storage-use-azcopy.md) e [transfira dados com a AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso descarregar um VHD para uma máquina local, além de usar a opção de descarregamento no portal?**

Pode utilizar o [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para descarregar um VHD.

**Como faço o download de dados para um computador baseado em Linux a partir de uma conta de armazenamento Azure, ou faço o upload de dados de uma máquina Linux?**

Pode usar o Azure CLI.

- Faça o download de uma única bolha:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Faça upload de uma única bolha:

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Como migrar blobs de uma conta de armazenamento para outra?**

 Pode fazê-lo usando o nosso roteiro de [migração Blob.](../scripts/storage-common-transfer-between-storage-accounts.md)
 
## <a name="migration-or-backup"></a>Migração ou apoio

**Como posso mover dados de um contentor de armazenamento para outro?**

Siga estes passos.

1.  Crie o recipiente (pasta) na bolha de destino.

2.  Utilize o [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) para copiar o conteúdo do recipiente de bolhas original para um recipiente de bolhas diferente.

**Como posso criar um script PowerShell para mover dados de uma partilha de ficheiros Azure para outra no Armazenamento Azure?**

Utilize o AzCopy para mover os dados de uma partilha de ficheiros Azure para outra no Armazenamento Azure. Para mais informações, consulte os [dados de transferência com a AzCopy no Windows](storage-use-azcopy.md) e [transfira dados com a AzCopy no Linux](storage-use-azcopy-linux.md).

**Como faço o envio de ficheiros .csv grandes para o Armazenamento Azure?**

Utilize o AzCopy para carregar ficheiros .csv grandes para o Armazenamento Azure. Para mais informações, consulte os [dados de transferência com a AzCopy no Windows](storage-use-azcopy.md) e [transfira dados com a AzCopy no Linux](storage-use-azcopy-linux.md).

**Tenho de mover os registos da unidade D para a minha conta de armazenamento azure todos os dias. Como posso automatizar isto?**

Pode utilizar o AzCopy e criar uma tarefa no Agendador de Tarefas. Faça upload de ficheiros para uma conta de armazenamento Azure utilizando um script de lote AzCopy. Para mais informações, consulte [Como configurar e executar tarefas](../../cloud-services/cloud-services-startup-tasks.md)de arranque para um serviço na nuvem .

**Como posso mover a minha conta de armazenamento entre assinaturas?**

Utilize o AzCopy para mover a sua conta de armazenamento entre subscrições. Para mais informações, consulte os [dados de transferência com a AzCopy no Windows](storage-use-azcopy.md) e [transfira dados com a AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso mover cerca de 10 TB de dados para armazenamento em outra região?**

Utilize o AzCopy para mover os dados. Para mais informações, consulte os [dados de transferência com a AzCopy no Windows](storage-use-azcopy.md) e [transfira dados com a AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso copiar dados do local para o Armazém Azure?**

Utilize o AzCopy para copiar os dados. Para mais informações, consulte os [dados de transferência com a AzCopy no Windows](storage-use-azcopy.md) e [transfira dados com a AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso mover dados do local para ficheiros Azure?**

Use o AzCopy para mover dados. Para mais informações, consulte os [dados de transferência com a AzCopy no Windows](storage-use-azcopy.md) e [transfira dados com a AzCopy no Linux](storage-use-azcopy-linux.md).

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

3.  Crie um disco gerido utilizando o ficheiro VHD noutra região para o qual copiou o VHD. Para tal, execute o seguinte script Azure PowerShell:  

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

**Como posso mover ou transferir dados de uma conta de armazenamento?**

Utilize o AzCopy para descarregar os dados. Para mais informações, consulte os [dados de transferência com a AzCopy no Windows](storage-use-azcopy.md) e [transfira dados com a AzCopy no Linux](storage-use-azcopy-linux.md).

**Como passo de uma conta de armazenamento premium para uma conta de armazenamento padrão?**

Siga estes passos.

1.  Crie uma conta de armazenamento padrão. (Ou utilize uma conta de armazenamento padrão existente na sua subscrição.)

2.  Baixar AzCopy. Executar um dos seguintes comandos AzCopy.

    Para copiar discos inteiros na conta de armazenamento:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S

    Para copiar apenas um disco, forneça o nome do disco em **Padrão:**

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

A operação pode levar várias horas para terminar.

Para se certificar de que a transferência terminou com sucesso, examine o recipiente de conta de armazenamento de destino no portal Azure. Depois de copiados os discos para a conta de armazenamento padrão, pode fixá-los à máquina virtual como um disco existente. Para mais informações, consulte Como anexar um disco de [dados gerido a uma máquina virtual do Windows no portal Azure](../../virtual-machines/windows/attach-managed-disk-portal.md).  

**Como passo de uma conta de armazenamento clássica para uma conta de armazenamento do Gestor de Recursos Azure?**

Pode utilizar o cmdlet **Move-AzureStorageAccount.** Este cmdlet tem vários passos (validar, preparar, comprometer). Podevavavavar a mudança antes de o fazer.

Se tiver máquinas virtuais, deve tomar medidas adicionais antes de migrar os dados da conta de armazenamento. Para mais informações, consulte [os recursos migrate IaaS do clássico para o Gestor de Recursos Azure utilizando o Azure PowerShell.](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md)

**Como posso fazer a minha conta de armazenamento para outra conta de armazenamento?**

Não há opção de fazer o backup toda uma conta de armazenamento diretamente. Mas pode mover manualmente o recipiente nessa conta de armazenamento para outra conta utilizando o AzCopy ou o Storage Explorer. Os seguintes passos mostram como utilizar o AzCopy para mover o recipiente:  

1.  Instale a ferramenta de linha de comando [AzCopy.](storage-use-azcopy.md) Esta ferramenta ajuda-o a mover o ficheiro VHD entre contas de armazenamento.

2.  Depois de instalar o AzCopy no Windows utilizando o instalador, abra uma janela Command Prompt e, em seguida, navegue para a pasta de instalação AzCopy no seu computador. Por predefinição, a AzCopy é instalada em **%ProgramFiles (x86)%\Microsoft SDKs\Azure\AzCopy** ou **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Executar o seguinte comando para mover o recipiente. Deve substituir o texto por valores reais.   

            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Forneça o URI para a conta de armazenamento de origem (até ao recipiente).  
    - `/Dest`: Fornecer o URI para a conta de armazenamento-alvo (até ao recipiente).  
    - `/SourceKey`: Fornecer a chave principal para a conta de armazenamento de origem. Pode copiar esta chave do portal Azure selecionando a conta de armazenamento.  
    - `/DestKey`: Fornecer a chave principal para a conta de armazenamento alvo. Pode copiar esta chave a partir do portal selecionando a conta de armazenamento.

Depois de executar este comando, os ficheiros do contentor são transferidos para a conta de armazenamento do alvo.

> [!NOTE]
> O AzCopy CLI não funciona em conjunto com o interruptor **Padrão** quando copia de uma bolha Azure para outra.
>
> Pode copiar e editar diretamente o comando AzCopy e cruzar a verificação para se certificar de que o **Padrão** corresponde à fonte. Certifique-se também de que **os wildcards /S** estão em vigor. Para mais informações, consulte [os parâmetros AzCopy](storage-use-azcopy.md).

**Como posso fazer o armazenamento de ficheiros Azure?**

Não há solução de reserva. No entanto, o Azure Files também suporta cópias assíncronas. Então, pode copiar ficheiros:

- De uma parte para outra parte dentro de uma conta de armazenamento ou para uma conta de armazenamento diferente.

- De uma parte para um recipiente de bolha dentro de uma conta de armazenamento ou para uma conta de armazenamento diferente.

Para mais informações, consulte [os dados de transferência com a AzCopy no Windows](storage-use-azcopy.md).
## <a name="configuration"></a>Configuração

**Como posso mudar a localização secundária para a região europeia para uma conta de armazenamento?**

Quando cria uma conta de armazenamento, seleciona a região primária para a conta. A seleção da região secundária baseia-se na região primária, e não pode ser alterada. Para mais informações, consulte [o armazenamento geo-redundante (GRS): replicação inter-regional para armazenamento azure](storage-redundancy.md).

**Onde posso obter mais informações sobre encriptação do serviço de armazenamento Azure (SSE)?**  

Consulte os seguintes artigos:

-  [Guia de segurança de armazenamento azure](../blobs/security-recommendations.md)

-  [Encriptação do Serviço de Armazenamento do Azure para Dados Inativos](storage-service-encryption.md)

**Como encriptar dados numa conta de armazenamento?**

Depois de ativar a encriptação numa conta de armazenamento, os dados existentes não são encriptados. Para encriptar os dados existentes, deve carregá-los novamente para a conta de armazenamento.

Utilize o AzCopy para copiar os dados para uma conta de armazenamento diferente e, em seguida, mover os dados de volta. Também pode usar [encriptação em repouso.](storage-service-encryption.md)

**Existem pré-requisitos para alterar a replicação de uma conta de armazenamento de armazenamento georedundante para armazenamento localmente redundante?**

Não.

**Como me converto ao Armazenamento Azure Premium para uma partilha de ficheiros?**

O Armazenamento Premium não é permitido numa partilha de ficheiros Azure.

**Como faço upgrade de uma conta de armazenamento padrão para uma conta de armazenamento premium? Como posso desvalorizar de uma conta de armazenamento premium para uma conta de armazenamento padrão?**

Deve criar a conta de armazenamento de destino, copiar dados da conta fonte para a conta de destino e, em seguida, apagar a conta fonte. Pode utilizar uma ferramenta como a AzCopy para copiar os dados.

Se tiver máquinas virtuais, deve tomar medidas adicionais antes de migrar os dados da conta de armazenamento. Para mais informações, consulte [Migrating to Azure Premium Storage (discos não geridos)](storage-migration-to-premium-storage.md).

**Como posso dar a outras pessoas acesso aos meus recursos de armazenamento?**

Para dar a outras pessoas acesso aos recursos de armazenamento:

-   Utilize um símbolo de assinatura de acesso partilhado (SAS) para fornecer acesso a um recurso.

-   Forneça a um utilizador a chave primária ou secundária para a conta de armazenamento. Para mais informações, consulte Gerir as chaves de [acesso à conta](storage-account-keys-manage.md)de armazenamento .

-   Altere a política de acesso para permitir o acesso anónimo. Para mais informações, consulte [grant permissões de utilizadores anónimos para contentores e bolhas](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Onde está instalado o AzCopy?**

-   Se aceder ao AzCopy a partir da linha de comando sinuosa Microsoft Azure Storage, **escreva AzCopy**. A linha de comando está instalada ao lado da AzCopy.

-   Se instalou a versão de 32 bits, está localizada aqui: **%ProgramFiles (x86)%\\Microsoft SDKs\\Azure\\AzCopy**.

-   Se instalou a versão de 64 bits, está localizada aqui: **%ProgramFiles%\\Microsoft\\SDKs Azure\\AzCopy**.

**Como uso um domínio personalizado HTTPS com a minha conta de armazenamento? Por exemplo, como faço com\/que "https: /mystorageaccountname.blob.core.windows.net/images/image.gif" apareça como "https:\//www.contoso.com/images/image.gif"?**

O SSL não é atualmente suportado em contas de armazenamento com domínios personalizados.
Mas pode utilizar domínios personalizados não HTTPS. Para mais informações, consulte [Configure um nome](../blobs/storage-custom-domain-name.md)de domínio personalizado para o seu ponto final de armazenamento Blob .

## <a name="access-to-storage"></a>Acesso ao armazenamento

**Como mapear uma pasta de contentores numa máquina virtual?**

Use uma parte de ficheiro Azure.

**Como posso aceder ao armazenamento redundante dos Ficheiros Azure?**

O armazenamento geo-redundante de acesso de leitura é necessário para aceder a armazenamento redundante. No entanto, o Azure Files suporta apenas armazenamento redundante localmente e armazenamento geo-redundante padrão que não permite acesso apenas a leitura.

**Para uma conta de armazenamento replicada (como armazenamento redundante de zona, armazenamento geo-redundante ou armazenamento geo-redundante de acesso à leitura), como posso aceder aos dados armazenados na região secundária?**

-   Se estiver a utilizar armazenamento redundante ou armazenamento geo-redundante, não pode aceder a dados da região secundária a menos que inicie uma falha nessa região. Para obter mais informações sobre o processo de failover, consulte a falha da conta de [recuperação de desastres e armazenamento (pré-visualização) no Armazenamento Azure](storage-disaster-recovery-guidance.md).

-   Se estiver a utilizar armazenamento geo-redundante de acesso de leitura, pode aceder a dados da região secundária a qualquer momento. Utilize um dos seguintes métodos:  

    - **AzCopy**: Apêndice **-secundário** ao nome da conta de armazenamento no URL para aceder ao ponto final secundário. Por exemplo:  

      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **Ficha SAS:** Utilize um token SAS para aceder a dados a partir do ponto final. Para mais informações, consulte [A utilização de assinaturas de acesso partilhado](storage-sas-overview.md).

**Como uso o FTP para aceder a dados que estão numa conta de armazenamento?**

Não há forma de aceder diretamente a uma conta de armazenamento utilizando ftp. No entanto, pode configurar uma máquina virtual Azure e, em seguida, instalar um servidor FTP na máquina virtual. Pode ter os ficheiros de loja de servidores FTP numa partilha do Azure Files ou num disco de dados que esteja disponível para a máquina virtual.

Se pretender apenas descarregar dados sem ter de utilizar o Storage Explorer ou uma aplicação similar, poderá utilizar um token SAS. Para mais informações, consulte [A utilização de assinaturas de acesso partilhado](storage-sas-overview.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
