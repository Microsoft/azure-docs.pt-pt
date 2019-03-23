---
title: Migração de VMs para o armazenamento Premium do Azure | Documentos da Microsoft
description: Migre as suas VMs existentes para o armazenamento Premium do Azure. O armazenamento Premium oferece suporte de disco de elevado desempenho e de baixa latência para cargas de trabalho de e/S intensivas executadas em máquinas de virtuais do Azure.
services: storage
author: yuemlu
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.subservice: common
ms.openlocfilehash: fdca10c54c798bd47a34eb0f8af091908bcc2711
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372323"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrar para o armazenamento Premium do Azure (discos não geridos)

> [!NOTE]
> Este artigo descreve como migrar uma VM que utiliza discos standard não geridos para uma VM que utiliza discos não geridos premium. Recomendamos que utilize o Managed Disks do Azure para novas VMs, e que converta os discos não geridos anteriores para discos geridos. Gerido o identificador de discos as contas de armazenamento subjacente para que não precisa. Para obter mais informações, consulte nosso [descrição geral do Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).
>

Armazenamento Premium do Azure fornece suporte de discos de elevado desempenho e de baixa latência para máquinas virtuais que executam cargas de trabalho de e/S intensivas. Pode tirar partido da velocidade e o desempenho destes discos migrando discos de VM da sua aplicação para o armazenamento Premium do Azure.

O objetivo deste guia é ajudar os novos utilizadores do armazenamento do Azure Premium melhor, prepare-se para uma transição mais tranqüila do seu sistema atual para o armazenamento Premium. O guia aborda três dos principais componentes nesse processo:

* [Planear a migração para o armazenamento Premium](#plan-the-migration-to-premium-storage)
* [Preparar e copiar os discos rígidos virtuais (VHDs) para o armazenamento Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Criar a Máquina Virtual Linux utilizando o armazenamento Premium](#create-azure-virtual-machine-using-premium-storage)

Pode migrar VMs de outras plataformas para o armazenamento Premium do Azure ou migrar VMs do Azure existentes de armazenamento Standard para o armazenamento Premium. Este guia aborda os passos para ambos os dois cenários. Siga os passos especificados na secção relevante, dependendo do seu cenário.

> [!NOTE]
> Pode encontrar uma descrição geral das funcionalidades e preços do premium SSDs em: [Selecione um tipo de disco para IaaS VMs](../../virtual-machines/windows/disks-types.md#premium-ssd). Recomendamos que migre qualquer disco da máquina virtual que necessitam de IOPS elevado para o armazenamento Premium do Azure para o melhor desempenho para a sua aplicação. Se o disco não necessita de IOPS elevado, pode limitar os custos ao manter o aplicativo no armazenamento Standard, que armazena dados de disco da máquina virtual em unidades de disco rígido (HDDs) em vez do SSDs.
>

Concluir o processo de migração em sua totalidade pode exigir ações adicionais, antes e depois as etapas fornecidas neste guia. Os exemplos incluem a configurar redes virtuais ou pontos finais ou fazer alterações de código dentro da própria aplicação que poderão necessitar de um período de indisponibilidade na sua aplicação. Estas ações são exclusivas para cada aplicativo e deve ser concluído, juntamente com as etapas fornecidas neste guia para fazer a transição completa para o armazenamento Premium, o mais direto possível.

## <a name="plan-the-migration-to-premium-storage"></a>Planear a migração para o armazenamento Premium
Esta secção garante que está pronto para seguir os passos de migração neste artigo e ajuda-o a tomar a melhor decisão em tipos VM e o disco.

### <a name="prerequisites"></a>Pré-requisitos
* Precisará de uma subscrição do Azure. Se não tiver uma, pode criar um mês [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) subscrição ou visite [preços do Azure](https://azure.microsoft.com/pricing/) para obter mais opções.
* Para executar cmdlets do PowerShell, terá do módulo do PowerShell do Microsoft Azure. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter as instruções de instalação e do ponto de instalação.
* Quando planeja usar as VMs do Azure em execução no armazenamento Premium, tem de utilizar as VMs com capacidade de armazenamento Premium. Pode utilizar os discos Standard e o armazenamento Premium com VMs com capacidade de armazenamento Premium. No futuro, discos de armazenamento Premium estará disponíveis com mais tipos VM. Para obter mais informações sobre todos os tamanhos e tipos de disco de VM do Azure disponíveis, consulte [tamanhos de máquinas virtuais](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [tamanhos de serviços Cloud](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Considerações
Uma VM do Azure suporta anexar vários discos de armazenamento Premium, para que seus aplicativos podem ter até 256 TB de armazenamento por VM. Com o armazenamento Premium, seus aplicativos podem obter 80.000 IOPS (operações de entrada/saída por segundo) por VM e 2000 MB por segundo de débito de disco por VM com latências extremamente baixas para operações de leitura. Tem opções de várias VMs e os discos. Esta secção é para ajudá-lo a encontrar uma opção que melhor se adequa aos sua carga de trabalho.

#### <a name="vm-sizes"></a>Tamanhos de VM
As especificações de tamanho de VM do Azure estão listadas na [tamanhos de máquinas virtuais](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Reveja as características de desempenho de máquinas virtuais que funcionam com o armazenamento Premium e escolha o tamanho VM mais adequado que melhor se adequa aos sua carga de trabalho. Certifique-se de que existe largura de banda suficiente disponível na sua VM para direcionar o tráfego de disco.

#### <a name="disk-sizes"></a>Tamanhos de disco
Existem cinco tipos de discos que podem ser utilizados com a VM e cada uma tem específicos IOPs e débito limites. Leve em consideração estes limites ao escolher o tipo de disco para a sua VM com base nas necessidades da sua aplicação em termos de capacidade, desempenho, escalabilidade e cargas de pico.

| Tipo de discos Premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Tamanho do disco           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPs por disco       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Débito por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo |

Consoante a carga de trabalho, determine se os discos de dados adicionais são necessários para a sua VM. Pode anexar vários discos de dados persistentes para a VM. Se for necessário, pode do stripe em todos os discos para aumentar a capacidade e desempenho do volume. (Veja o que é a repartição de disco [aqui](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Se utilizar discos de dados de armazenamento Premium do stripe [espaços de armazenamento][4], deve configurá-la com uma coluna para cada disco que é utilizado. Caso contrário, o desempenho geral do volume repartido pode ser menor do que o esperado devido a distribuição desigual de tráfego em todos os discos. Para VMs do Linux, pode utilizar o *mdadm* utilitário para o mesmo. Consulte o artigo [configurar o RAID de Software no Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter detalhes.

#### <a name="storage-account-scalability-targets"></a>Destinos de escalabilidade de conta de armazenamento
Os seguintes destinos de escalabilidade, além de ter contas de armazenamento Premium a [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md). Se os requisitos da aplicação excederem os destinos de escalabilidade de uma única conta de armazenamento, criar a sua aplicação para utilizar várias contas de armazenamento e dividir os dados entre essas contas de armazenamento.

| Capacidade de conta total | Largura de banda total de uma conta de armazenamento localmente redundante |
|:--- |:--- |
| Capacidade do disco: 35TB<br />Capacidade do instantâneo: 10 TB |Máximo de 50 gigabits por segundo para entrada + saída |

Para obter mais informações sobre especificações do Premium Storage, confira [metas de escalabilidade e desempenho do armazenamento do Azure](storage-scalability-targets.md#premium-performance-storage-account-scale-limits).

#### <a name="disk-caching-policy"></a>Política de colocação em cache do disco
Por predefinição, é a política de colocação em cache *só de leitura* em todos os discos de dados de Premium, e *leitura-escrita* para o disco de sistema operativo Premium ligados à VM. Esta definição de configuração é recomendada para alcançar o desempenho ideal para a IOs sua aplicação. Para discos de dados de escrita intensiva ou só de escrita (por exemplo, ficheiros de registo do SQL Server), desative o cache em disco para que pode obter um melhor desempenho do aplicativo. As definições de cache para discos de dados existentes podem ser atualizadas utilizando o [portal do Azure](https://portal.azure.com) ou o *- HostCaching* parâmetro do *AzureDataDisk conjunto* cmdlet.

#### <a name="location"></a>Localização
Escolha uma localização onde o armazenamento Premium do Azure está disponível. Ver [serviços do Azure por região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas sobre localizações disponíveis. VMs localizadas na mesma região que a conta de armazenamento que armazena os discos da VM dará um desempenho muito melhor do que se eles estão em regiões separadas.

#### <a name="other-azure-vm-configuration-settings"></a>Outras definições de configuração de VM do Azure
Ao criar uma VM do Azure, será solicitado para definir certas configurações de VM. Lembre-se de que algumas definições são fixas durante o ciclo de vida da VM, enquanto pode modificar ou adicionar outros mais tarde. Reveja estas definições de configuração de VM do Azure e certifique-se de que estão configurados adequadamente para corresponder aos requisitos de carga de trabalho.

### <a name="optimization"></a>Otimização
[Armazenamento Premium do Azure: Conceber o elevado desempenho](../../virtual-machines/windows/premium-storage-performance.md) fornece diretrizes para a criação de aplicativos de alto desempenho usando o armazenamento Premium do Azure. Pode seguir as diretrizes combinadas com as melhores práticas de desempenho aplicáveis para as tecnologias utilizadas pela sua aplicação.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Preparar e copiar os discos rígidos virtuais (VHDs) para o armazenamento Premium
A secção seguinte fornece diretrizes para preparar os VHDs da VM e copiar VHDs ao armazenamento do Azure.

* [Cenário 1: "Estou a migrar as VMs do Azure existentes para o armazenamento Premium do Azure."](#scenario1)
* [Cenário 2: "Estou a migrar VMs de outras plataformas para armazenamento Premium do Azure."](#scenario2)

### <a name="prerequisites"></a>Pré-requisitos
Para preparar os VHDs para migração, terá de:

* Uma subscrição do Azure, uma conta de armazenamento e um contentor nessa conta de armazenamento para o qual pode copiar o VHD. Tenha em atenção que a conta de armazenamento de destino pode ser uma conta de armazenamento Standard ou Premium, consoante os requisitos.
* Uma ferramenta para generalizar o VHD, se planeja criar várias instâncias VM a partir do mesmo. Por exemplo, sysprep para Windows ou virt-sysprep para Ubuntu.
* Uma ferramenta para carregar o ficheiro VHD para a conta de armazenamento. Ver [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md) ou utilize um [Explorador de armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Este guia descreve a copiar o VHD com a ferramenta de AzCopy.

> [!NOTE]
> Se escolher a opção de cópia síncrona com o AzCopy, para um desempenho ideal, copie o VHD, executando uma dessas ferramentas a partir de uma VM do Azure que está na mesma região que a conta de armazenamento de destino. Se estiver a copiar um VHD a partir de uma VM do Azure numa região diferente, o desempenho poderá ser mais lento.
>
> Para copiar uma grande quantidade de dados através de largura de banda limitada, considere [com o serviço importar/exportar do Azure para transferir dados para armazenamento de BLOBs](../storage-import-export-service.md); Isto permite-lhe transferir os dados pelo envio de unidades de disco rígido num Datacenter do Azure. Pode utilizar o serviço importar/exportar do Azure para copiar dados para uma conta de armazenamento standard apenas. Quando os dados estiverem na sua conta de armazenamento standard, pode utilizar o [API do Blob de cópia](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou AzCopy para transferir os dados à sua conta de armazenamento premium.
>
> Tenha em atenção que o Microsoft Azure só suporta ficheiros VHD de tamanho fixo. Ficheiros VHDX ou VHDs dinâmicos não são suportadas. Se tiver um VHD dinâmico, pode convertê-la para a utilização de tamanho fixo de [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) cmdlet.
>
>

### <a name="scenario1"></a>Cenário 1: "Estou a migrar as VMs do Azure existentes para o armazenamento Premium do Azure."
Se estiver a migrar VMs do Azure existente, pare a VM, preparar VHDs por tipo de VHD que pretende e, em seguida, copie o VHD com o AzCopy ou PowerShell.

A VM tem de ser completamente baixo para migrar um estado limpo. Haverá um período de indisponibilidade até que a migração seja concluída.

#### <a name="step-1-prepare-vhds-for-migration"></a>Passo 1. Preparar o VHD para a migração
Se estiver a migrar as VMs do Azure existentes para o armazenamento Premium, o VHD pode ser:

* Uma imagem do sistema de operativo generalizada
* Um disco de exclusiva do sistema operativo
* Um disco de dados

Veja a seguir vamos analisar estas 3 cenários de preparação do VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Utilizar um VHD generalizado do sistema operativo para criar várias instâncias VM
Se estiver a carregar um VHD que vai ser utilizado para criar várias instâncias de VM do Azure genéricas, tem primeiro de generalizar VHD com um utilitário de sysprep. Isto aplica-se a um VHD que está no local ou na cloud. Sysprep remove todas as informações específicas da máquina do VHD.

> [!IMPORTANT]
> Tirar um instantâneo ou a VM de cópia de segurança antes de a generalizar a ele. Sysprep em execução irá parar e desalocar a instância VM. Siga os passos abaixo para sysprep um VHD do SO Windows. Tenha em atenção que executar o comando Sysprep irá solicitar-lhe encerrar a máquina virtual. Para obter mais informações sobre o Sysprep, consulte [visão geral do Sysprep](https://technet.microsoft.com/library/hh825209.aspx) ou [referência técnica de Sysprep](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Abra uma janela de linha de comandos como administrador.
2. Introduza o seguinte comando para abrir o Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Na ferramenta de preparação do sistema, selecione Enter System Out-of-Box Experience (OOBE), selecione a caixa de verificação Generalize, selecione **encerramento**e, em seguida, clique em **OK**, conforme mostrado na imagem abaixo. O Sysprep será generalizar o sistema operativo e encerrar o sistema.

    ![][1]

Para uma VM do Ubuntu, utilize virt sysprep para alcançar o mesmo. Ver [virt sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para obter mais detalhes. Consulte também alguns do código-fonte aberto [Provisionamento do servidor Linux software](https://www.cyberciti.biz/tips/server-provisioning-software.html) para outros sistemas de operativos Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Utilizar um único VHD de sistema operativo para criar uma única instância VM
Se tiver uma aplicação em execução na VM que requer que os dados específicos do computador, não generalize o VHD. Um VHD generalizado não pode ser utilizado para criar uma instância de VM do Azure exclusiva. Por exemplo, se tiver o controlador de domínio no seu VHD, execução de sysprep tornará ineficaz como um controlador de domínio. Reveja as aplicações em execução na sua VM e o impacto da execução de sysprep nos mesmos antes de generalizar o VHD.

##### <a name="register-data-disk-vhd"></a>Registar o VHD de disco de dados
Se tiver discos de dados no Azure a ser migrados, tem de certificar-se de que as VMs que utilizem estes discos de dados são encerradas.

Siga os passos descritos abaixo para copiar o VHD para o armazenamento Premium do Azure e registe-o como um disco de dados aprovisionados.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passo 2. Criar o destino para o VHD
Crie uma conta de armazenamento para a manutenção dos VHDs. Quando planear onde pretende armazenar os VHDs, considere os seguintes pontos:

* A conta de armazenamento Premium de destino.
* A localização da conta de armazenamento tem de ser igual a VMs do Azure com capacidade de armazenamento Premium, irá criar na fase final. Pode copiar para uma nova conta de armazenamento, ou se pretende utilizar a mesma conta de armazenamento com base nas suas necessidades.
* Copie e guarde a chave de conta de armazenamento da conta de armazenamento de destino para a próxima fase.

Para discos de dados, pode optar por manter alguns discos de dados numa conta de armazenamento standard (por exemplo, os discos que têm o armazenamento mais esporádica), mas, recomendamos vivamente que mover todos os dados para a carga de trabalho de produção utilizar o armazenamento premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Passo 3. Copie o VHD com o AzCopy ou o PowerShell
Precisará localizar a chave de conta de contentor caminho e o armazenamento, para processar uma destas duas opções. Chave de conta de armazenamento e o caminho do contentor pode ser encontrada na **Portal do Azure** > **armazenamento**. O contentor de URL vai ser, como "https:\//myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opção 1: Copiar um VHD com o AzCopy (cópia assíncrona)
Utilizar o AzCopy, pode carregar facilmente o VHD através da Internet. Dependendo do tamanho dos VHDs, isto pode demorar tempo. Não se esqueça de verificar os limites de entrada/saída de conta de armazenamento ao utilizar esta opção. Ver [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md) para obter detalhes.

1. Transfira e instale o AzCopy aqui: [Versão mais recente do AzCopy](https://aka.ms/downloadazcopy)
2. Abra o PowerShell do Azure e vá para a pasta onde o AzCopy é instalado.
3. Utilize o seguinte comando para copiar o ficheiro VHD da "Origem" para "Destino".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exemplo:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Aqui estão as descrições dos parâmetros utilizados no comando do AzCopy:

   * **/ Origem:  *&lt;origem&gt;:*** Localização da pasta ou URL do contentor de armazenamento que contém o VHD.
   * **/SourceKey: *&lt;source-account-key&gt;:*** Chave de conta de armazenamento da conta de armazenamento de origem.
   * **/ Dest:  *&lt;destino&gt;:*** URL do contentor de armazenamento para copiar o VHD.
   * **/DestKey: *&lt;dest-account-key&gt;:*** Chave de conta de armazenamento da conta de armazenamento de destino.
   * **/ Padrão:  *&lt;nome de ficheiro&gt;:*** Especifique o nome de ficheiro do VHD para copiar.

Para obter detalhes sobre como utilizar o AzCopy ferramenta, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opção 2: Copiar um VHD com o PowerShell (Synchronized cópia)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Também pode copiar o ficheiro VHD com o cmdlet do PowerShell AzStorageBlobCopy de início. Utilize o seguinte comando no Azure PowerShell para copiar o VHD. Substitua os valores em português com valores correspondentes da sua conta de armazenamento de origem e de destino. Para utilizar este comando, tem de ter um recipiente chamado vhds na sua conta de armazenamento de destino. Se o contentor não existir, crie uma antes de executar o comando.

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

### <a name="scenario2"></a>Cenário 2: "Estou a migrar VMs de outras plataformas para armazenamento Premium do Azure."
Se estiver a migrar VHD de não - armazenamento do Azure na Cloud para o Azure, primeiro tem de exportar o VHD para um diretório local. Ter o caminho de origem completo do diretório local onde o VHD é armazenado útil e, em seguida, utilizar o AzCopy para carregá-lo para o armazenamento do Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Passo 1. Exportar o VHD para um diretório local
##### <a name="copy-a-vhd-from-aws"></a>Copiar um VHD do AWS
1. Se estiver a utilizar o AWS, exporte a instância do EC2 para um VHD num bucket do Amazon S3. Siga os passos descritos na documentação do Amazon para exportar o Amazon EC2 instâncias instalar a ferramenta de interface de linha de comandos (CLI) do Amazon EC2 e execute o comando export-tarefa de criar-instâncias para exportar a instância do EC2 para um ficheiro VHD. Certifique-se de que usar **VHD** para o disco&#95;imagem&#95;variável de formato, ao executar o **criar instância-export-tarefa** comando. O ficheiro exportado do VHD é guardado no bucket do Amazon S3 que designar durante esse processo.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Transferir o ficheiro VHD a partir do registo de S3. Selecione o ficheiro VHD, em seguida, **ações** > **transferir**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copiar um VHD a partir de outra cloud não pertencentes ao Azure
Se estiver a migrar VHD de não - armazenamento do Azure na Cloud para o Azure, primeiro tem de exportar o VHD para um diretório local. Copie o caminho de origem completo do diretório local onde o VHD é armazenado.

##### <a name="copy-a-vhd-from-on-premises"></a>Copiar um VHD no local
Se estiver a migrar o VHD de um ambiente no local, terá do caminho de origem completo em que as VHD é armazenado. O caminho de origem pode ser uma partilha de ficheiro ou localização do servidor.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passo 2. Criar o destino para o VHD
Crie uma conta de armazenamento para a manutenção dos VHDs. Quando planear onde pretende armazenar os VHDs, considere os seguintes pontos:

* A conta de armazenamento de destino pode ser armazenamento standard ou premium, consoante os requisitos de aplicação.
* A região da conta de armazenamento tem de ser igual a VMs do Azure com capacidade de armazenamento Premium, irá criar na fase final. Pode copiar para uma nova conta de armazenamento, ou se pretende utilizar a mesma conta de armazenamento com base nas suas necessidades.
* Copie e guarde a chave de conta de armazenamento da conta de armazenamento de destino para a próxima fase.

Recomendamos vivamente que mover todos os dados para a carga de trabalho de produção utilizar o armazenamento premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Passo 3. Carregar o VHD para o armazenamento do Azure
Agora que tem o VHD no diretório local, pode utilizar o AzCopy ou AzurePowerShell para carregar o ficheiro. vhd para o armazenamento do Azure. Ambas as opções são fornecidas aqui:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opção 1: Utilização do Azure PowerShell Add-AzureVhd para carregar o ficheiro. vhd

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Um exemplo <Uri> pode ser ***"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"***. Um exemplo <FileInfo> pode ser ***"C:\path\to\upload.vhd"***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opção 2: Com o AzCopy para carregar o ficheiro. vhd
Utilizar o AzCopy, pode carregar facilmente o VHD através da Internet. Dependendo do tamanho dos VHDs, isto pode demorar tempo. Não se esqueça de verificar os limites de entrada/saída de conta de armazenamento ao utilizar esta opção. Ver [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md) para obter detalhes.

1. Transfira e instale o AzCopy aqui: [Versão mais recente do AzCopy](https://aka.ms/downloadazcopy)
2. Abra o PowerShell do Azure e vá para a pasta onde o AzCopy é instalado.
3. Utilize o seguinte comando para copiar o ficheiro VHD da "Origem" para "Destino".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exemplo:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Aqui estão as descrições dos parâmetros utilizados no comando do AzCopy:

   * **/ Origem:  *&lt;origem&gt;:*** Localização da pasta ou URL do contentor de armazenamento que contém o VHD.
   * **/SourceKey: *&lt;source-account-key&gt;:*** Chave de conta de armazenamento da conta de armazenamento de origem.
   * **/ Dest:  *&lt;destino&gt;:*** URL do contentor de armazenamento para copiar o VHD.
   * **/DestKey: *&lt;dest-account-key&gt;:*** Chave de conta de armazenamento da conta de armazenamento de destino.
   * **/BlobType: page:** Especifica que o destino é um blob de página.
   * **/ Padrão:  *&lt;nome de ficheiro&gt;:*** Especifique o nome de ficheiro do VHD para copiar.

Para obter detalhes sobre como utilizar o AzCopy ferramenta, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD
Também pode carregar um VHD para a sua conta de armazenamento através de um dos seguintes meios:

* [Blob de cópia de armazenamento do Azure API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Blobs de carregar o Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)
* [Referência da API do REST do armazenamento de importação/exportação serviço](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Recomendamos que utilize o serviço importar/exportar se estimado tempo a carregar tem mais de 7 dias. Pode usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) estimar o tempo da unidade de tamanho e a transferência de dados.
>
> Importação/exportação pode ser utilizada para copiar para uma conta de armazenamento standard. Terá de copiar de armazenamento standard para a conta de armazenamento premium usando uma ferramenta como o AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Criar VMs do Azure com o armazenamento Premium
Depois do VHD é carregado ou copiado para a conta de armazenamento pretendida, siga as instruções nesta secção para registar o VHD como uma imagem do SO ou o disco do SO dependendo do seu cenário e, em seguida, criar uma instância de VM a partir do mesmo. O VHD do disco de dados pode ser anexado à VM depois de criado.
Um script de migração de exemplo é fornecido no final desta secção. Este script simple não corresponde a todos os cenários. Terá de atualizar o script para corresponder com o seu cenário específico. Para ver se esse script aplica-se ao seu cenário, veja a seguir [um exemplo de Script de migração](#a-sample-migration-script).

### <a name="checklist"></a>Lista de verificação
1. Aguardar até que todos os discos VHD copiar está concluída.
2. Certifique-se de que o armazenamento Premium está disponível na região que está a migrar.
3. Decida a nova série VM que irá utilizar. Deve ser um compatível com o armazenamento Premium e o tamanho deve ser consoante a disponibilidade na região e com base nas suas necessidades.
4. Decida o tamanho VM exato que irá utilizar. Tamanho da VM tem de ser suficientemente grande para suportar o número de discos de dados que tiver. Por exemplo, Se tiver 4 discos de dados, a VM tem de ter 2 ou mais núcleos. Além disso, considere o poder, memória de processamento e necessidades de largura de banda de rede.
5. Crie uma conta de armazenamento Premium na região de destino. Esta é a conta que irá utilizar para a nova VM.
6. Ter os detalhes da VM atuais úteis, incluindo a lista de discos e blobs de VHD correspondentes.

Prepare a sua aplicação para o período de indisponibilidade. Para fazer uma migração limpa, terá de parar a todo o processamento do sistema atual. Só que, em seguida, pode obtê-lo para um estado consistente que pode migrar para a nova plataforma. Duração de tempo de inatividade dependerá da quantidade de dados nos discos para migrar.

> [!NOTE]
> Se estiver a criar uma VM do Azure Resource Manager a partir de um disco de VHD especializado, consulte [este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) para implementar a VM do Resource Manager com o disco existente.
>
>

### <a name="register-your-vhd"></a>Registar o VHD
Para criar uma VM a partir de VHD do SO ou anexar um disco de dados para uma nova VM, tem primeiro de registá-los. Siga os passos abaixo, dependendo do cenário do VHD.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalizado VHD de sistema operativo para criar várias instâncias de VM do Azure
Depois de imagem do SO generalizada VHD é carregada para a conta de armazenamento, registe-o como um **imagem de VM do Azure** para que possa criar uma ou mais instâncias VM a partir do mesmo. Utilize os seguintes cmdlets do PowerShell para registar o VHD como uma imagem de SO de VM do Azure. Forneça o URL de contentores completa, onde o VHD foi copiado para.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Copie e guarde o nome desta nova imagem de VM do Azure. No exemplo acima, é *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Exclusivo de sistema operativo de VHD para criar uma única instância de VM do Azure
Depois do VHD do SO exclusivo é carregado para a conta de armazenamento, registe-o como um **disco de SO do Azure** para que possa criar uma instância de VM a partir do mesmo. Utilize estes cmdlets do PowerShell para registar o VHD como um disco de SO do Azure. Forneça o URL de contentores completa, onde o VHD foi copiado para.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Copie e guarde o nome deste novo disco de SO do Azure. No exemplo acima, é *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>VHD para ser anexados a instância de VM do Azure nova (s) de disco de dados
Depois do VHD do disco de dados é carregado para a conta de armazenamento, registe-o como um disco de dados do Azure para que este pode ser ligado à sua nova série DS, série DSv2 ou instância de VM de Azure de série GS.

Utilize estes cmdlets do PowerShell para registar o VHD como um disco de dados do Azure. Forneça o URL de contentores completa, onde o VHD foi copiado para.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Copie e guarde o nome deste novo disco de dados do Azure. No exemplo acima, é *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Criar uma VM com capacidade de armazenamento Premium
Uma vez a imagem do SO ou disco do SO são registrados, criar uma nova série DS, série DSv2 ou série GS VM. Irá utilizar a imagem do sistema operativo ou o nome do disco de sistema operativo que registrou. Selecione o tipo VM da camada de armazenamento Premium. O exemplo abaixo, estamos a utilizar o *Standard_DS2* tamanho da VM.

> [!NOTE]
> Atualize o tamanho do disco para se certificar de que ele corresponda à sua capacidade e requisitos de desempenho e os tamanhos de disco do Azure disponível.
>
>

Siga o passo a passo cmdlets do PowerShell abaixo para criar a nova VM. Em primeiro lugar, defina os parâmetros comuns:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Primeiro, crie um serviço em nuvem em que irá alojar as suas VMs novas.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Em seguida, dependendo do seu cenário, crie a instância de VM do Azure a partir da imagem do SO ou o disco do SO que registrou.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalizado VHD de sistema operativo para criar várias instâncias de VM do Azure
Criar um ou mais novas DS série Azure instâncias de VM com o **imagem do SO do Azure** que registrou. Especifica este nome de imagem do SO na configuração da VM quando criar a nova VM, conforme mostrado abaixo.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Exclusivo de sistema operativo de VHD para criar uma única instância de VM do Azure
Crie um novo DS série VM do Azure instância com o **disco de SO do Azure** que registrou. Especifica este nome de disco do SO na configuração da VM ao criar a nova VM, conforme mostrado abaixo.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Especifique outras informações de VM do Azure, como um serviço em nuvem, a região, a conta de armazenamento, o conjunto de disponibilidade e a política de colocação em cache. Tenha em atenção que a instância VM tem de estar localizada conjuntamente com associado do sistema operacional ou discos de dados, portanto, a conta de serviço, região e o armazenamento em nuvem selecionado deve ser na mesma localização que os VHDs subjacentes desses discos.

### <a name="attach-data-disk"></a>Anexar disco de dados
Por último, se se registrou o disco de dados VHDs, anexe-os a VM nova do Azure com capacidade de armazenamento Premium.

Utilize o seguinte cmdlet do PowerShell para anexar o disco de dados para a nova VM e especificar a política de colocação em cache. No exemplo a seguir a política de colocação em cache está definida como *só de leitura*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Pode haver passos adicionais necessários para suportar a sua aplicação que não é são cobertas neste guia.
>
>

### <a name="checking-and-plan-backup"></a>A verificar e planear a cópia de segurança
Assim que a nova VM está em execução, aceda ao mesmo com o mesmo id de início de sessão e palavra-passe é que a VM original e certifique-se de que tudo está a funcionar conforme esperado. Todas as definições, incluindo volumes repartidos, estarão presentes na nova VM.

A última etapa é planear a cópia de segurança e agenda de manutenção para a nova VM com base nas necessidades do aplicativo.

### <a name="a-sample-migration-script"></a>Um exemplo de script de migração
Se tiver várias VMs para migrar, automatização mediante scripts do PowerShell serão úteis. Segue-se um exemplo de script que automatiza a migração de uma VM. Tenha em atenção que o script abaixo é apenas um exemplo e há algumas suposições feitas sobre os discos da VM atuais. Terá de atualizar o script para corresponder com o seu cenário específico.

As suposições são:

* Está a criar VMs clássicas do Azure.
* Os discos de SO de origem e discos de dados de origem estão na mesma conta de armazenamento e o mesmo contentor. Se os discos de SO e discos de dados não estiverem no mesmo local, pode utilizar o AzCopy ou o Azure PowerShell para copiar VHDs através de contas de armazenamento e de contentores. Consulte o passo anterior: [Copie o VHD com o AzCopy ou PowerShell](#copy-vhd-with-azcopy-or-powershell). Este script para satisfazer o seu cenário de edição é outra opção, mas recomendamos que utilize o AzCopy ou o PowerShell, uma vez que é mais fácil e rápida.

O script de automação é fornecido abaixo. Substitua o texto com as suas informações e atualizar o script para corresponder com o seu cenário específico.

> [!NOTE]
> Usar o script existente não preserva a configuração de rede da sua VM de origem. Precisará re-config definições de rede nas suas VMs migradas.
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
A configuração atual do VM pode ser personalizada especificamente para funcionar bem com os discos Standard. Por exemplo, para aumentar o desempenho ao utilizar vários discos num volume repartido. Por exemplo, em vez de usar separadamente 4 discos no armazenamento Premium, pode ser capaz de otimizar o custo por ter um único disco. Otimizações, como esta necessidade de ser tratado numa caso a caso e requer passos personalizados após a migração. Além disso, tenha em atenção que este processo poderá não funcionar bem para bases de dados e aplicações que dependem do layout de disco definido na configuração.

##### <a name="preparation"></a>Preparação
1. Conclua a migração simples, conforme descrito na seção anterior. Otimizações serão executadas na nova VM após a migração.
2. Defina novos tamanhos de disco necessários para a configuração otimizada.
3. Determine o mapeamento dos volumes/discos atuais para as especificações de disco novo.

##### <a name="execution-steps"></a>Passos de execução
1. Crie novos discos com os tamanhos adequados na VM de armazenamento Premium.
2. Início de sessão para a VM e copie os dados do volume atual para o novo disco, que mapeia para esse volume. Fazer isso para todos os volumes atuais necessário mapear para um novo disco.
3. Em seguida, alterar as definições de aplicação para mudar para novos discos e desanexar volumes antigos.

Para ajustar o aplicativo para um melhor desempenho de disco, consulte a secção de desempenho do aplicativo Otimização do nosso [conceber o elevado desempenho](../../virtual-machines/windows/premium-storage-performance.md) artigo.

### <a name="application-migrations"></a>Migrações de aplicações
Bases de dados e outras aplicações complexas podem exigir etapas especiais conforme definido pelo fornecedor da aplicação para a migração. Consulte a documentação da respetiva aplicação. Por exemplo, Normalmente, as bases de dados podem ser migrados através de cópia de segurança e restaurar.

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes recursos para cenários específicos para migrar máquinas virtuais:

* [Migrar máquinas virtuais do Azure entre contas de armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Criar e carregar um VHD do Windows Server para o Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Criar e carregar um VHD do Linux para o Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migração de máquinas virtuais da Amazon AWS para o Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Além disso, consulte os seguintes recursos para saber mais sobre o armazenamento do Azure e máquinas virtuais do Azure:

* [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Selecione um tipo de disco para IaaS VMs](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
