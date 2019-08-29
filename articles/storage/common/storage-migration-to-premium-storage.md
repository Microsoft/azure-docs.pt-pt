---
title: Migrando VMs para o armazenamento Premium do Azure | Microsoft Docs
description: Migre suas VMs existentes para o armazenamento Premium do Azure. O armazenamento Premium oferece suporte a disco de alto desempenho e baixa latência para cargas de trabalho com uso intensivo de e/s em execução em máquinas virtuais do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 90cd079ebc82e8231b052f65156f85d612592ad2
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114740"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrando para o armazenamento Premium do Azure (discos não gerenciados)

> [!NOTE]
> Este artigo discute como migrar uma VM que usa discos padrão não gerenciados para uma VM que usa discos Premium não gerenciados. Recomendamos que você use o Azure Managed Disks para novas VMs e que converta seus discos não gerenciados anteriores em discos gerenciados. Managed Disks manipular as contas de armazenamento subjacentes para que você não precise fazê-lo. Para obter mais informações, consulte nossa [visão geral de Managed disks](../../virtual-machines/windows/managed-disks-overview.md).
>

O armazenamento Premium do Azure fornece suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando cargas de trabalho com e/s intensivas. Você pode aproveitar a velocidade e o desempenho desses discos migrando os discos de VM do aplicativo para o armazenamento Premium do Azure.

A finalidade deste guia é ajudar novos usuários do armazenamento Premium do Azure a se preparar melhor para fazer uma transição tranqüila do sistema atual para o armazenamento Premium. O guia aborda três dos principais componentes neste processo:

* [Planejar a migração para o armazenamento Premium](#plan-the-migration-to-premium-storage)
* [Preparar e copiar VHDs (discos rígidos virtuais) para o armazenamento Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Criar uma máquina virtual do Azure usando o armazenamento Premium](#create-azure-virtual-machine-using-premium-storage)

É possível migrar VMs de outras plataformas para o armazenamento Premium do Azure ou migrar VMs do Azure existentes do armazenamento Standard para o armazenamento Premium. Este guia aborda as etapas para ambos os dois cenários. Siga as etapas especificadas na seção relevante, dependendo do seu cenário.

> [!NOTE]
> Você pode encontrar uma visão geral do recurso e os preços do SSDs Premium em: [Selecione um tipo de disco para VMs IaaS](../../virtual-machines/windows/disks-types.md#premium-ssd). É recomendável migrar qualquer disco de máquina virtual que exija IOPS alto para o armazenamento Premium do Azure para obter o melhor desempenho para seu aplicativo. Se o disco não exigir IOPS alta, você poderá limitar os custos mantendo-os no armazenamento Standard, que armazena dados de disco da máquina virtual em HDDs (unidades de disco rígido) em vez de SSDs.
>

Concluir o processo de migração em sua totalidade pode exigir ações adicionais antes e depois das etapas fornecidas neste guia. Os exemplos incluem a configuração de redes virtuais ou pontos de extremidade ou a realização de alterações de código no próprio aplicativo, o que pode exigir algum tempo de inatividade em seu aplicativo. Essas ações são exclusivas de cada aplicativo e você deve concluí-las junto com as etapas fornecidas neste guia para tornar a transição completa para o armazenamento Premium o mais direto possível.

## <a name="plan-the-migration-to-premium-storage"></a>Planejar a migração para o armazenamento Premium
Esta seção garante que você está pronto para seguir as etapas de migração neste artigo e ajuda a tomar a melhor decisão sobre tipos de disco e de VM.

### <a name="prerequisites"></a>Pré-requisitos
* Você precisará de uma assinatura do Azure. Se você não tiver uma, poderá criar uma assinatura de [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) de um mês ou visitar [preços do Azure](https://azure.microsoft.com/pricing/) para obter mais opções.
* Para executar os cmdlets do PowerShell, você precisará do módulo Microsoft Azure PowerShell. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter as instruções de instalação e do ponto de instalação.
* Ao planejar usar VMs do Azure em execução no armazenamento Premium, você precisa usar as VMs compatíveis com o armazenamento Premium. Você pode usar discos de armazenamento Standard e Premium com VMs compatíveis com o armazenamento Premium. Os discos de armazenamento Premium estarão disponíveis com mais tipos de VM no futuro. Para obter mais informações sobre todos os tipos e tamanhos de disco de VM do Azure disponíveis, consulte [tamanhos de máquinas virtuais](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [tamanhos para serviços de nuvem](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Considerações
Uma VM do Azure dá suporte à anexação de vários discos de armazenamento Premium para que seus aplicativos possam ter até 256 TB de armazenamento por VM. Com o armazenamento Premium, seus aplicativos podem alcançar 80.000 IOPS (operações de entrada/saída por segundo) por VM e 2000 MB por segundo de taxa de transferência de disco por VM com latências extremamente baixas para operações de leitura. Você tem opções em uma variedade de VMs e discos. Esta seção serve para ajudá-lo a encontrar uma opção que melhor atenda à sua carga de trabalho.

#### <a name="vm-sizes"></a>Tamanhos de VM
As especificações de tamanho de VM do Azure são listadas em [tamanhos de máquinas virtuais](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Examine as características de desempenho de máquinas virtuais que funcionam com o armazenamento Premium e escolha o tamanho de VM mais apropriado que melhor atenda à sua carga de trabalho. Verifique se há largura de banda suficiente disponível em sua VM para direcionar o tráfego de disco.

#### <a name="disk-sizes"></a>Tamanhos de disco
Há cinco tipos de discos que podem ser usados com sua VM e cada um tem IOPs e limites de taxa de transferência específicos. Leve em consideração esses limites ao escolher o tipo de disco para sua VM com base nas necessidades do seu aplicativo em termos de capacidade, desempenho, escalabilidade e picos de carga.

| Tipo de discos Premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Tamanho do disco           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPs por disco       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Débito por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo |

Dependendo de sua carga de trabalho, determine se discos de dados adicionais são necessários para sua VM. Você pode anexar vários discos de dados persistentes à sua VM. Se necessário, você pode distribuir entre os discos para aumentar a capacidade e o desempenho do volume. (Veja o que é a distribuição de disco [aqui](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Se você distribuir discos de dados de armazenamento Premium usando [espaços de armazenamento][4], deverá configurá-lo com uma coluna para cada disco usado. Caso contrário, o desempenho geral do volume distribuído pode ser menor do que o esperado devido a uma distribuição irregular do tráfego entre os discos. Para VMs do Linux, você pode usar o utilitário *mdadm* para obter o mesmo. Consulte o artigo [Configurar o RAID de software no Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter detalhes.

#### <a name="storage-account-scalability-targets"></a>Metas de escalabilidade da conta de armazenamento
As contas de armazenamento Premium têm as seguintes metas de escalabilidade [, além das metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md). Se os requisitos do aplicativo excederem as metas de escalabilidade de uma única conta de armazenamento, crie seu aplicativo para usar várias contas de armazenamento e particione seus dados entre essas contas de armazenamento.

| Capacidade total da conta | Largura de banda total para uma conta de armazenamento com redundância local |
|:--- |:--- |
| Capacidade do disco: 35TB<br />Capacidade do instantâneo: 10 TB |Até 50 gigabits por segundo para entrada + saída |

Para obter mais informações sobre as especificações de armazenamento Premium, confira [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md#premium-performance-storage-account-scale-limits).

#### <a name="disk-caching-policy"></a>Política de cache de disco
Por padrão, a política de cache de disco é *somente leitura* para todos os discos de dados Premium e *leitura/gravação* para o disco do sistema operacional Premium anexado à VM. Essa definição de configuração é recomendada para alcançar o desempenho ideal para o IOs do seu aplicativo. Para discos de dados de gravação intensa ou somente gravação (como SQL Server arquivos de log), desabilite o cache de disco para que você possa obter melhor desempenho do aplicativo. As configurações de cache para discos de dados existentes podem ser atualizadas usando o [portal do Azure](https://portal.azure.com) ou o parâmetro *-HostCaching* do cmdlet *set-AzureDataDisk* .

#### <a name="location"></a>Location
Escolha um local onde o armazenamento Premium do Azure está disponível. Consulte [Serviços do Azure por região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas sobre locais disponíveis. As VMs localizadas na mesma região da conta de armazenamento que armazena os discos para a VM fornecerão um desempenho muito melhor do que se estivessem em regiões separadas.

#### <a name="other-azure-vm-configuration-settings"></a>Outras definições de configuração da VM do Azure
Ao criar uma VM do Azure, você será solicitado a definir determinadas configurações de VM. Lembre-se, algumas configurações são fixas durante o tempo de vida da VM, enquanto você pode modificar ou adicionar outras posteriormente. Examine essas definições de configuração da VM do Azure e certifique-se de que elas estejam configuradas adequadamente para corresponder aos seus requisitos de carga de trabalho.

### <a name="optimization"></a>Otimização
[Armazenamento Premium do Azure: Design para alto desempenho](../../virtual-machines/windows/premium-storage-performance.md) fornece diretrizes para a criação de aplicativos de alto desempenho usando o armazenamento Premium do Azure. Você pode seguir as diretrizes combinadas com as práticas recomendadas de desempenho aplicáveis às tecnologias usadas pelo seu aplicativo.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Preparar e copiar VHDs (discos rígidos virtuais) para o armazenamento Premium
A seção a seguir fornece diretrizes para preparar VHDs de sua VM e copiar VHDs para o armazenamento do Azure.

* [Cenário 1: "Estou migrando VMs do Azure existentes para o armazenamento Premium do Azure".](#scenario1)
* [Cenário 2: "Estou migrando VMs de outras plataformas para o armazenamento Premium do Azure."](#scenario2)

### <a name="prerequisites"></a>Pré-requisitos
Para preparar os VHDs para migração, você precisará de:

* Uma assinatura do Azure, uma conta de armazenamento e um contêiner na conta de armazenamento para o qual você pode copiar o VHD. Observe que a conta de armazenamento de destino pode ser uma conta de armazenamento Standard ou Premium, dependendo do seu requisito.
* Uma ferramenta para generalizar o VHD se você planeja criar várias instâncias de VM a partir dela. Por exemplo, Sysprep para Windows ou virt-Sysprep para Ubuntu.
* Uma ferramenta para carregar o arquivo VHD na conta de armazenamento. Consulte [transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md) ou usar um [Gerenciador de armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Este guia descreve como copiar o VHD usando a ferramenta AzCopy.

> [!NOTE]
> Se você escolher a opção de cópia síncrona com AzCopy, para um desempenho ideal, copie o VHD executando uma dessas ferramentas de uma VM do Azure que esteja na mesma região que a conta de armazenamento de destino. Se você estiver copiando um VHD de uma VM do Azure em uma região diferente, seu desempenho poderá ser mais lento.
>
> Para copiar uma grande quantidade de dados acima da largura de banda limitada, considere [usar o serviço de importação/exportação do Azure para transferir dados para o armazenamento de BLOBs](../storage-import-export-service.md); Isso permite que você transfira seus dados enviando unidades de disco rígido para um datacenter do Azure. Você pode usar o serviço de importação/exportação do Azure para copiar dados somente para uma conta de armazenamento padrão. Depois que os dados estiverem em sua conta de armazenamento padrão, você poderá usar a [API de cópia de blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou AzCopy para transferir os dados para sua conta de armazenamento Premium.
>
> Observe que Microsoft Azure dá suporte apenas a arquivos VHD de tamanho fixo. Não há suporte para arquivos VHDX ou VHDs dinâmicos. Se você tiver um VHD dinâmico, poderá convertê-lo em tamanho fixo usando o cmdlet [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) .
>
>

### <a name="scenario1"></a>Cenário 1: "Estou migrando VMs do Azure existentes para o armazenamento Premium do Azure".
Se você estiver migrando VMs do Azure existentes, pare a VM, prepare os VHDs de acordo com o tipo de VHD desejado e, em seguida, copie o VHD com AzCopy ou PowerShell.

A VM precisa estar completamente desativada para migrar um estado limpo. Haverá um tempo de inatividade até que a migração seja concluída.

#### <a name="step-1-prepare-vhds-for-migration"></a>Passo 1. Preparar VHDs para migração
Se você estiver migrando VMs do Azure existentes para o armazenamento Premium, seu VHD poderá ser:

* Uma imagem do sistema operacional generalizada
* Um disco do sistema operacional exclusivo
* Um disco de dados

Abaixo, percorremos esses três cenários para preparar seu VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Usar um VHD do sistema operacional generalizado para criar várias instâncias de VM
Se você estiver carregando um VHD que será usado para criar várias instâncias genéricas de VM do Azure, primeiro deverá generalizar o VHD usando um utilitário Sysprep. Isso se aplica a um VHD que é local ou na nuvem. O Sysprep remove todas as informações específicas do computador do VHD.

> [!IMPORTANT]
> Faça um instantâneo ou faça backup de sua VM antes de generalizar. Executar o Sysprep irá parar e desalocar a instância de VM. Siga as etapas abaixo para executar o Sysprep em um VHD do sistema operacional Windows. Observe que a execução do comando Sysprep exigirá que você desligue a máquina virtual. Para obter mais informações sobre o Sysprep, consulte [visão geral do Sysprep](https://technet.microsoft.com/library/hh825209.aspx) ou [referência técnica do Sysprep](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Abra uma janela de prompt de comando como administrador.
2. Digite o seguinte comando para abrir o Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Na ferramenta de preparação do sistema, selecione entrar na experiência de inicialização do sistema (OOBE), marque a caixa de seleção generalizar, selecione **desligar**e clique em **OK**, conforme mostrado na imagem abaixo. O Sysprep generalizará o sistema operacional e desligará o sistema.

    ![][1]

Para uma VM Ubuntu, use virt-Sysprep para obter o mesmo. Consulte [virt-Sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para obter mais detalhes. Veja também alguns dos [softwares de provisionamento do servidor Linux](https://www.cyberciti.biz/tips/server-provisioning-software.html) de código aberto para outros sistemas operacionais Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Usar um VHD do sistema operacional exclusivo para criar uma única instância de VM
Se você tiver um aplicativo em execução na VM que requer os dados específicos do computador, não generalizar o VHD. Um VHD não generalizado pode ser usado para criar uma instância exclusiva de VM do Azure. Por exemplo, se você tiver um controlador de domínio em seu VHD, a execução do Sysprep o tornará ineficaz como um controlador de domínio. Examine os aplicativos em execução na VM e o impacto da execução do Sysprep neles antes de generalizar o VHD.

##### <a name="register-data-disk-vhd"></a>Registrar VHD de disco de dados
Se você tiver discos de dados no Azure a serem migrados, você deve verificar se as VMs que usam esses discos de dados estão desligadas.

Siga as etapas descritas abaixo para copiar o VHD para o armazenamento Premium do Azure e registrá-lo como um disco de dados provisionado.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passo 2. Criar o destino para o VHD
Crie uma conta de armazenamento para manter seus VHDs. Considere os seguintes pontos ao planejar onde armazenar seus VHDs:

* A conta de armazenamento Premium de destino.
* O local da conta de armazenamento deve ser igual às VMs do Azure compatíveis com o armazenamento Premium que você criará no estágio final. Você pode copiar para uma nova conta de armazenamento ou planejar usar a mesma conta de armazenamento com base em suas necessidades.
* Copie e salve a chave da conta de armazenamento da conta de armazenamento de destino para o próximo estágio.

Para discos de dados, você pode optar por manter alguns discos de dados em uma conta de armazenamento padrão (por exemplo, discos que têm o armazenamento mais frio), mas é altamente recomendável mover todos os dados para a carga de trabalho de produção para usar o armazenamento Premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Etapa 3. Copiar VHD com AzCopy ou PowerShell
Você precisará localizar o caminho do contêiner e a chave da conta de armazenamento para processar qualquer uma dessas duas opções. O caminho do contêiner e a chave da conta de armazenamento podem ser encontrados no > **armazenamento** **do portal do Azure**. A URL do contêiner será como "https:\//myaccount.blob.Core.Windows.net/MyContainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opção 1: Copiar um VHD com AzCopy (cópia assíncrona)
Usando o AzCopy, você pode facilmente carregar o VHD pela Internet. Dependendo do tamanho dos VHDs, isso pode levar tempo. Lembre-se de verificar os limites de entrada/saída da conta de armazenamento ao usar essa opção. Consulte [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md) para obter detalhes.

1. Baixe e instale o AzCopy aqui: [Versão mais recente do AzCopy](https://aka.ms/downloadazcopy)
2. Abra Azure PowerShell e vá para a pasta onde o AzCopy está instalado.
3. Use o comando a seguir para copiar o arquivo VHD de "origem" para "destino".

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Exemplo:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

   Aqui estão as descrições dos parâmetros usados no comando AzCopy:

   * **/Source:** _origem:&gt; &lt;_ Local da pasta ou da URL do contêiner de armazenamento que contém o VHD.
   * **/SourceKey:** _chavedecontadeorigem&gt;: &lt;_ Chave de conta de armazenamento da conta de armazenamento de origem.
   * **/Dest:** _destino:&gt; &lt;_ URL do contêiner de armazenamento para o qual copiar o VHD.
   * **/DestKey:** _dest-Account-Key&gt;: &lt;_ Chave de conta de armazenamento da conta de armazenamento de destino.
   * **/Pattern:** nome do arquivo:  _&lt;&gt;_ Especifique o nome do arquivo do VHD a ser copiado.

Para obter detalhes sobre como usar a ferramenta AzCopy, consulte [transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opção 2: Copiar um VHD com o PowerShell (cópia sincronizada)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Você também pode copiar o arquivo VHD usando o cmdlet do PowerShell Start-AzStorageBlobCopy. Use o comando a seguir em Azure PowerShell para copiar o VHD. Substitua os valores em < > com os valores correspondentes da sua conta de armazenamento de origem e de destino. Para usar esse comando, você deve ter um contêiner chamado VHDs na sua conta de armazenamento de destino. Se o contêiner não existir, crie um antes de executar o comando.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Exemplo:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Cenário 2: "Estou migrando VMs de outras plataformas para o armazenamento Premium do Azure."
Se você estiver migrando o VHD do armazenamento em nuvem não Azure para o Azure, primeiro deverá exportar o VHD para um diretório local. Tenha o caminho de origem completo do diretório local onde o VHD é armazenado e, em seguida, usando AzCopy para carregá-lo no armazenamento do Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Passo 1. Exportar o VHD para um diretório local
##### <a name="copy-a-vhd-from-aws"></a>Copiar um VHD de AWS
1. Se você estiver usando o AWS, exporte a instância EC2 para um VHD em um Bucket S3 da Amazon. Siga as etapas descritas na documentação do Amazon para exportar instâncias do Amazon EC2 para instalar a ferramenta da CLI (interface de linha de comando) do Amazon EC2 e executar o comando Create-Instance-Export-Task para exportar a instância do EC2 para um arquivo VHD. Certifique-se de usar o **VHD** para&#95;a&#95;variável de formato de imagem de disco ao executar o comando **criar-instância-exportar-tarefa** . O arquivo VHD exportado é salvo no Bucket S3 da Amazon que você designa durante esse processo.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Baixe o arquivo VHD do Bucket S3. Selecione o arquivo VHD e**faça o download**das **ações** > .

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copiar um VHD de outra nuvem não Azure
Se você estiver migrando o VHD do armazenamento em nuvem não Azure para o Azure, primeiro deverá exportar o VHD para um diretório local. Copie o caminho de origem completo do diretório local em que o VHD está armazenado.

##### <a name="copy-a-vhd-from-on-premises"></a>Copiar um VHD do local
Se você estiver migrando o VHD de um ambiente local, será necessário o caminho de origem completo em que o VHD é armazenado. O caminho de origem pode ser um local do servidor ou um compartilhamento de arquivos.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passo 2. Criar o destino para o VHD
Crie uma conta de armazenamento para manter seus VHDs. Considere os seguintes pontos ao planejar onde armazenar seus VHDs:

* A conta de armazenamento de destino pode ser o armazenamento Standard ou Premium, dependendo do requisito do aplicativo.
* A região da conta de armazenamento deve ser igual às VMs do Azure compatíveis com o armazenamento Premium que você criará no estágio final. Você pode copiar para uma nova conta de armazenamento ou planejar usar a mesma conta de armazenamento com base em suas necessidades.
* Copie e salve a chave da conta de armazenamento da conta de armazenamento de destino para o próximo estágio.

É altamente recomendável mover todos os dados para a carga de trabalho de produção para usar o armazenamento Premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Passo 3: Carregar o VHD no armazenamento do Azure
Agora que você tem o VHD no diretório local, você pode usar AzCopy ou AzurePowerShell para carregar o arquivo. vhd no armazenamento do Azure. Ambas as opções são fornecidas aqui:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opção 1: Usando Azure PowerShell Add-AzureVhd para carregar o arquivo. vhd

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Um URI \<de exemplo > pode **_ser https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd""_** . Um > \<de FileInfo de exemplo pode ser **_"C:\path\to\upload.vhd"_** .

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opção 2: Usando AzCopy para carregar o arquivo. vhd
Usando o AzCopy, você pode facilmente carregar o VHD pela Internet. Dependendo do tamanho dos VHDs, isso pode levar tempo. Lembre-se de verificar os limites de entrada/saída da conta de armazenamento ao usar essa opção. Consulte [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md) para obter detalhes.

1. Baixe e instale o AzCopy aqui: [Versão mais recente do AzCopy](https://aka.ms/downloadazcopy)
2. Abra Azure PowerShell e vá para a pasta onde o AzCopy está instalado.
3. Use o comando a seguir para copiar o arquivo VHD de "origem" para "destino".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exemplo:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

   Aqui estão as descrições dos parâmetros usados no comando AzCopy:

   * **/Source:** _origem:&gt; &lt;_ Local da pasta ou da URL do contêiner de armazenamento que contém o VHD.
   * **/SourceKey:** _chavedecontadeorigem&gt;: &lt;_ Chave de conta de armazenamento da conta de armazenamento de origem.
   * **/Dest:** _destino:&gt; &lt;_ URL do contêiner de armazenamento para o qual copiar o VHD.
   * **/DestKey:** _dest-Account-Key&gt;: &lt;_ Chave de conta de armazenamento da conta de armazenamento de destino.
   * **/BlobType: página:** Especifica que o destino é um blob de páginas.
   * **/Pattern:** nome do arquivo:  _&lt;&gt;_ Especifique o nome do arquivo do VHD a ser copiado.

Para obter detalhes sobre como usar a ferramenta AzCopy, consulte [transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD
Você também pode carregar um VHD para sua conta de armazenamento usando um dos seguintes meios:

* [API do blob de cópia do armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Gerenciador de Armazenamento do Azure carregar BLOBs](https://azurestorageexplorer.codeplex.com/)
* [Referência da API REST do serviço de importação/exportação do armazenamento](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> É recomendável usar o serviço de importação/exportação se o tempo de carregamento estimado for maior que 7 dias. Você pode usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para estimar o tempo do tamanho dos dados e da unidade de transferência.
>
> A importação/exportação pode ser usada para copiar para uma conta de armazenamento padrão. Você precisará copiar do armazenamento Standard para a conta de armazenamento Premium usando uma ferramenta como AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Criar VMs do Azure usando o armazenamento Premium
Depois que o VHD for carregado ou copiado para a conta de armazenamento desejada, siga as instruções nesta seção para registrar o VHD como uma imagem do sistema operacional, ou disco do sistema operacional, dependendo do seu cenário e, em seguida, crie uma instância de VM a partir dele. O VHD do disco de dados pode ser anexado à VM após sua criação.
Um exemplo de script de migração é fornecido no final desta seção. Este script simples não corresponde a todos os cenários. Talvez seja necessário atualizar o script para que ele corresponda ao seu cenário específico. Para ver se esse script se aplica ao seu cenário, veja abaixo [um exemplo de script de migração](#a-sample-migration-script).

### <a name="checklist"></a>Lista de verificação
1. Aguarde até que todas as cópias dos discos VHD sejam concluídas.
2. Verifique se o armazenamento Premium está disponível na região para a qual você está migrando.
3. Decida a nova série de VMs que você usará. Ele deve ser compatível com armazenamento Premium, e o tamanho deve estar dependendo da disponibilidade na região e com base em suas necessidades.
4. Decida o tamanho exato da VM que será usado. O tamanho da VM precisa ser grande o suficiente para dar suporte ao número de discos de dados que você tem. Por exemplo, Se você tiver quatro discos de dados, a VM deverá ter dois ou mais núcleos. Além disso, considere o processamento de energia, memória e necessidades de largura de banda de rede.
5. Crie uma conta de armazenamento Premium na região de destino. Essa é a conta que você usará para a nova VM.
6. Tenha os detalhes da VM atual à mão, incluindo a lista de discos e blobs VHD correspondentes.

Prepare seu aplicativo para tempo de inatividade. Para fazer uma migração limpa, você precisa interromper todo o processamento no sistema atual. Somente então você pode obtê-lo em um estado consistente, que pode ser migrado para a nova plataforma. A duração do tempo de inatividade dependerá da quantidade de dados nos discos a serem migrados.

> [!NOTE]
> Se você estiver criando uma VM Azure Resource Manager de um disco VHD especializado, consulte [este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) para implantar a VM do Resource Manager usando o disco existente.
>
>

### <a name="register-your-vhd"></a>Registrar seu VHD
Para criar uma VM do VHD do sistema operacional ou anexar um disco de dados a uma nova VM, você deve primeiro registrá-las. Siga as etapas abaixo, dependendo do cenário do VHD.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>VHD do sistema operacional generalizado para criar várias instâncias de VM do Azure
Após o VHD da imagem de so generalizado ser carregado na conta de armazenamento, registre-o como uma **imagem de VM do Azure** para que você possa criar uma ou mais instâncias de VM a partir dele. Use os seguintes cmdlets do PowerShell para registrar seu VHD como uma imagem do sistema operacional da VM do Azure. Forneça a URL de contêiner completa para a qual o VHD foi copiado.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Copie e salve o nome dessa nova imagem de VM do Azure. No exemplo acima, é *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>VHD do sistema operacional exclusivo para criar uma única instância de VM do Azure
Depois que o VHD do sistema operacional exclusivo for carregado na conta de armazenamento, registre-o como um **disco do sistema operacional do Azure** para que você possa criar uma instância de VM a partir dele. Use estes cmdlets do PowerShell para registrar seu VHD como um disco do sistema operacional do Azure. Forneça a URL de contêiner completa para a qual o VHD foi copiado.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Copie e salve o nome desse novo disco do sistema operacional do Azure. No exemplo acima, é *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>VHD de disco de dados a ser anexado a novas instâncias de VM do Azure
Depois que o VHD do disco de dados for carregado na conta de armazenamento, registre-o como um disco de dados do Azure para que ele possa ser anexado à nova instância de VM do Azure série DS, série DSv2 ou GS.

Use estes cmdlets do PowerShell para registrar seu VHD como um disco de dados do Azure. Forneça a URL de contêiner completa para a qual o VHD foi copiado.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Copie e salve o nome desse novo disco de dados do Azure. No exemplo acima, é DataDisk.

### <a name="create-a-premium-storage-capable-vm"></a>Criar uma VM com capacidade de armazenamento Premium
Depois que a imagem do sistema operacional ou o disco do sistema operacional são registrados, crie uma nova VM da série DS, DSv2 ou GS. Você usará a imagem do sistema operacional ou o nome do disco do sistema operacional que você registrou. Selecione o tipo de VM na camada de armazenamento Premium. No exemplo abaixo, estamos usando o tamanho da VM *Standard_DS2* .

> [!NOTE]
> Atualize o tamanho do disco para garantir que ele corresponda aos requisitos de capacidade e desempenho e aos tamanhos de disco do Azure disponíveis.
>
>

Siga os cmdlets do PowerShell passo a passo abaixo para criar a nova VM. Primeiro, defina os parâmetros comuns:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Primeiro, crie um serviço de nuvem no qual você estará hospedando suas novas VMs.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Em seguida, dependendo do seu cenário, crie a instância de VM do Azure da imagem do sistema operacional ou do disco do sistema operacional que você registrou.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>VHD do sistema operacional generalizado para criar várias instâncias de VM do Azure
Crie uma ou mais novas instâncias de VM do Azure da série DS usando a **imagem do sistema operacional do Azure** que você registrou. Especifique esse nome de imagem do so na configuração da VM ao criar uma nova VM, conforme mostrado abaixo.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>VHD do sistema operacional exclusivo para criar uma única instância de VM do Azure
Crie uma nova instância de VM do Azure da série DS usando o **disco do sistema operacional do Azure** que você registrou. Especifique esse nome de disco do so na configuração da VM ao criar a nova VM, conforme mostrado abaixo.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Especifique outras informações de VM do Azure, como um serviço de nuvem, região, conta de armazenamento, conjunto de disponibilidade e política de cache. Observe que a instância de VM deve estar colocalizada com o sistema operacional ou os discos de dados associados, de modo que o serviço de nuvem selecionado, a região e a conta de armazenamento devem estar no mesmo local que os VHDs subjacentes desses discos.

### <a name="attach-data-disk"></a>Anexar disco de dados
Por fim, se você registrou VHDs de disco de dados, anexe-os à nova VM do Azure com capacidade de armazenamento Premium.

Use o cmdlet do PowerShell a seguir para anexar o disco de dados à nova VM e especificar a política de cache. No exemplo abaixo, a política de cache é definida como *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Pode haver etapas adicionais necessárias para dar suporte ao seu aplicativo que não será abordado neste guia.
>
>

### <a name="checking-and-plan-backup"></a>Verificando e planejando o backup
Depois que a nova VM estiver em execução, acesse-a usando a mesma ID de logon e senha como a VM original e verifique se tudo está funcionando conforme o esperado. Todas as configurações, incluindo os volumes distribuídos, estarão presentes na nova VM.

A última etapa é planejar o agendamento de backup e manutenção para a nova VM com base nas necessidades do aplicativo.

### <a name="a-sample-migration-script"></a>Um exemplo de script de migração
Se você tiver várias VMs para migrar, a automação por meio de scripts do PowerShell será útil. Veja a seguir um script de exemplo que automatiza a migração de uma VM. Observe que o script abaixo é apenas um exemplo, e há poucas suposições sobre os discos de VM atuais. Talvez seja necessário atualizar o script para que ele corresponda ao seu cenário específico.

As suposições são:

* Você está criando VMs do Azure clássicas.
* Os discos do sistema operacional de origem e os discos de dados de origem estão na mesma conta de armazenamento e no mesmo contêiner. Se os discos do sistema operacional e os discos de dados não estiverem no mesmo local, você poderá usar AzCopy ou Azure PowerShell para copiar VHDs em contêineres e contas de armazenamento. Consulte a etapa anterior: [Copie o VHD com o AzCopy ou o PowerShell](#copy-vhd-with-azcopy-or-powershell). Editar esse script para atender ao seu cenário é outra opção, mas é recomendável usar o AzCopy ou o PowerShell, pois ele é mais fácil e rápido.

O script de automação é fornecido abaixo. Substitua o texto pelas informações e atualize o script para que ele corresponda ao seu cenário específico.

> [!NOTE]
> O uso do script existente não preserva a configuração de rede da VM de origem. Você precisará reconfigurar as configurações de rede em suas VMs migradas.
>
>

```powershell
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    https://azure.microsoft.com/documentation/articles/powershell-install-configure/
    https://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in seconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module version" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article https://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the source vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting until the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more customization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Otimização
Sua configuração atual de VM pode ser personalizada especificamente para funcionar bem com discos padrão. Por exemplo, para aumentar o desempenho usando muitos discos em um volume distribuído. Por exemplo, em vez de usar 4 discos separadamente no armazenamento Premium, você poderá otimizar o custo tendo um único disco. Otimizações como essa precisam ser tratadas caso a caso e exigem etapas personalizadas após a migração. Além disso, observe que esse processo pode não funcionar bem para bancos de dados e aplicativos que dependem do layout do disco definido na configuração.

##### <a name="preparation"></a>Preparação
1. Conclua a migração simples, conforme descrito na seção anterior. As otimizações serão executadas na nova VM após a migração.
2. Defina os novos tamanhos de disco necessários para a configuração otimizada.
3. Determine o mapeamento dos discos/volumes atuais para as novas especificações de disco.

##### <a name="execution-steps"></a>Etapas de execução
1. Crie os novos discos com os tamanhos corretos na VM de armazenamento Premium.
2. Faça logon na VM e copie os dados do volume atual para o novo disco que é mapeado para esse volume. Faça isso para todos os volumes atuais que precisam ser mapeados para um novo disco.
3. Em seguida, altere as configurações do aplicativo para mudar para os novos discos e desanexe os volumes antigos.

Para ajustar o aplicativo para melhorar o desempenho do disco, consulte a seção otimizando o desempenho do aplicativo de nosso artigo [criando para alto desempenho](../../virtual-machines/windows/premium-storage-performance.md) .

### <a name="application-migrations"></a>Migrações de aplicativos
Os bancos de dados e outros aplicativos complexos podem exigir etapas especiais, conforme definido pelo provedor de aplicativos para a migração. Consulte a documentação do respectivo aplicativo. Por exemplo, Normalmente, os bancos de dados podem ser migrados por meio de backup e restauração.

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes recursos para cenários específicos para migrar máquinas virtuais:

* [Migrar máquinas virtuais do Azure entre contas de armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Criar e carregar um VHD do Windows Server no Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Criando e carregando um VHD do Linux para o Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migrando máquinas virtuais do Amazon AWS para Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Além disso, consulte os seguintes recursos para saber mais sobre o armazenamento do Azure e as máquinas virtuais do Azure:

* [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Selecionar um tipo de disco para VMs IaaS](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
