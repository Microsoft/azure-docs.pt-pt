---
title: VMs migratórios para o Armazenamento Premium Azure [ Microsoft Docs
description: Emigre os seus VMs existentes para o Armazenamento Premium Azure. O Premium Storage oferece suporte de disco de alto desempenho e baixa latência para cargas de trabalho intensivas em I/O em execução em Máquinas Virtuais Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 7cb5a335af7093bc217578d57340b03b8b9c08b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748342"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migração para o Armazenamento Premium Azure (Discos Não Geridos)

> [!NOTE]
> Este artigo discute como migrar um VM que utiliza discos padrão não geridos para um VM que usa discos premium não geridos. Recomendamos que utilize discos geridos azure para novos VMs e que converta os seus discos não geridos anteriores para discos geridos. Os Discos geridos tratam das contas de armazenamento subjacentes para que não seja necessário. Para mais informações, consulte a nossa visão geral dos [Discos Geridos](../../virtual-machines/windows/managed-disks-overview.md).
>

O Armazenamento Azure Premium oferece suporte de disco de alto desempenho e baixa latência para máquinas virtuais que executam cargas de trabalho intensivas em I/O. Pode aproveitar a velocidade e desempenho destes discos migrando os discos VM da sua aplicação para o Armazenamento Premium Azure.

O objetivo deste guia é ajudar os novos utilizadores do Azure Premium Storage a prepararem-se para fazerem uma transição suave do seu sistema atual para o Armazenamento Premium. O guia aborda três dos componentes-chave neste processo:

* [Plano para a Migração para Armazenamento Premium](#plan-the-migration-to-premium-storage)
* [Prepare e copie discos rígidos virtuais (VHDs) para armazenamento premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Criar máquina virtual Azure usando armazenamento premium](#create-azure-virtual-machine-using-premium-storage)

Pode migrar VMs de outras plataformas para o Azure Premium Storage ou migrar os VMs Azure existentes do Armazenamento Standard para o Armazenamento Premium. Este guia cobre passos para ambos os cenários. Siga os passos especificados na secção relevante, dependendo do seu cenário.

> [!NOTE]
> Pode encontrar uma visão geral da funcionalidade e preços de SSDs premium em: [Selecione um tipo de disco para VMs IaaS](../../virtual-machines/windows/disks-types.md#premium-ssd). Recomendamos a migração de qualquer disco de máquina virtual que exija iOPS elevados ao Armazenamento Premium Azure para o melhor desempenho da sua aplicação. Se o seu disco não necessitar de IOPS elevados, pode limitar os custos mantendo-o no Armazenamento Padrão, que armazena dados de discos de máquinas virtuais em Discos Rígidos (HDDs) em vez de SSDs.
>

Completar o processo de migração na sua totalidade pode exigir ações adicionais antes e depois dos passos fornecidos neste guia. Exemplos incluem configurar redes virtuais ou pontos finais ou fazer alterações de código dentro da própria aplicação, o que pode exigir algum tempo de inatividade na sua aplicação. Estas ações são únicas para cada aplicação, e você deve completá-las juntamente com os passos fornecidos neste guia para tornar a transição completa para o Armazenamento Premium o mais perfeita possível.

## <a name="plan-for-the-migration-to-premium-storage"></a><a name="plan-the-migration-to-premium-storage"></a>Plano para a migração para armazenamento premium
Esta secção garante que está pronto para seguir os passos de migração neste artigo, e ajuda-o a tomar a melhor decisão sobre os tipos vM e Disk.

### <a name="prerequisites"></a>Pré-requisitos
* Precisa de uma subscrição do Azure. Se não tiver uma, pode criar uma subscrição [de teste gratuita](https://azure.microsoft.com/pricing/free-trial/) de um mês ou visitar o Preço [Azure](https://azure.microsoft.com/pricing/) para mais opções.
* Para executar os cmdlets PowerShell, necessitará do módulo Microsoft Azure PowerShell. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter as instruções de instalação e do ponto de instalação.
* Quando planeia utilizar VMs Azure em execução em Armazenamento Premium, precisa de utilizar os VMs premium capazes de armazenamento. Pode utilizar discos de armazenamento Standard e Premium com VMs de armazenamento premium. Os discos de armazenamento premium estarão disponíveis com mais tipos de VM no futuro. Para obter mais informações sobre todos os tipos e tamanhos de discos Azure VM disponíveis, consulte [Tamanhos para máquinas virtuais](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [tamanhos para serviços](../../cloud-services/cloud-services-sizes-specs.md)em nuvem .

### <a name="considerations"></a>Considerações
Um VM Azure suporta a fixação de vários discos de Armazenamento Premium para que as suas aplicações possam ter até 256 TB de armazenamento por VM. Com o Armazenamento Premium, as suas aplicações podem atingir 80.000 IOPS (operações de entrada/saída por segundo) por VM e 2000 MB por segundo de produção de disco por VM com tardios extremamente baixos para operações de leitura. Você tem opções em uma variedade de VMs e Discos. Esta secção é para ajudá-lo a encontrar uma opção que melhor se adapte à sua carga de trabalho.

#### <a name="vm-sizes"></a>Tamanhos de VM
As especificações do tamanho Do VM Azure estão listadas em [tamanhos para máquinas virtuais.](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Reveja as características de desempenho das máquinas virtuais que funcionam com o Armazenamento Premium e escolha o tamanho VM mais adequado que melhor se adapte à sua carga de trabalho. Certifique-se de que existe largura de banda suficiente disponível no seu VM para conduzir o tráfego do disco.

#### <a name="disk-sizes"></a>Tamanhos do disco
Existem cinco tipos de discos que podem ser usados com o seu VM e cada um tem iOPs específicos e limites de entrada. Tenha em conta estes limites ao escolher o tipo de disco para o seu VM com base nas necessidades da sua aplicação em termos de capacidade, desempenho, escalabilidade e pico de cargas.

| Tipo de discos premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Tamanho do disco           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPs por disco       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Débito por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo |

Dependendo da sua carga de trabalho, determine se são necessários discos de dados adicionais para o seu VM. Pode anexar vários discos de dados persistentes ao seu VM. Se necessário, pode riscar os discos para aumentar a capacidade e desempenho do volume. (Veja o que é Striping de Disco [aqui](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Se riscar os discos de dados premium de armazenamento utilizando espaços de [armazenamento,][4]deve configurá-lo com uma coluna para cada disco que seja utilizado. Caso contrário, o desempenho global do volume listrado pode ser inferior ao esperado devido à distribuição desigual do tráfego através dos discos. Para Os VMs Linux pode usar a utilidade *do mdadm* para conseguir o mesmo. Consulte o artigo [Configure Software RAID no Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais detalhes.

#### <a name="storage-account-scalability-targets"></a>Metas de escalabilidade da conta de armazenamento

As contas de armazenamento premium têm os seguintes objetivos de escalabilidade. Se os seus requisitos de aplicação excederem os alvos de escalabilidade de uma única conta de armazenamento, construa a sua aplicação para utilizar várias contas de armazenamento e partilhe os seus dados através dessas contas de armazenamento.

| Capacidade total da conta | Largura total de banda para uma conta de armazenamento localmente redundante |
|:--- |:--- |
| Capacidade do disco: 35TB<br />Capacidade instantânea: 10 TB |Até 50 gigabits por segundo para Entrada + Saída |

Para obter mais informações sobre as especificações de Armazenamento Premium, consulte [os alvos de escalabilidade para contas](../blobs/scalability-targets-premium-page-blobs.md)de armazenamento de blob de página premium .

#### <a name="disk-caching-policy"></a>Política de cache de disco
Por predefinição, a política de cache de disco é *Read-Only* para todos os discos de dados Premium e *Read-Write* para o disco do sistema operativo Premium ligado ao VM. Esta configuração de configuração é recomendada para obter o desempenho ideal para os IOs da sua aplicação. Para discos de dados de escrita pesadas ou escritas (como ficheiros de registo do SQL Server), desative o cache do disco para que possa obter um melhor desempenho de aplicação. As definições de cache para os discos de dados existentes podem ser atualizadas utilizando o [portal Azure](https://portal.azure.com) ou o parâmetro *-HostCaching* do cmdlet *Set-AzureDataDisk.*

#### <a name="location"></a>Localização
Escolha um local onde o Azure Premium Storage esteja disponível. Consulte os [Serviços Azure por Região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas sobre os locais disponíveis. VMs localizados na mesma região que a conta de Armazenamento que armazena os discos para o VM darão muito melhor desempenho do que se estiverem em regiões separadas.

#### <a name="other-azure-vm-configuration-settings"></a>Outras configurações de configuração de VM Azure
Ao criar um VM Azure, será-lhe pedido que configure certas definições vM. Lembre-se, poucas configurações são fixadas para o tempo de vida útil do VM, enquanto pode modificar ou adicionar outras mais tarde. Reveja estas definições de configuração Do VM Azure e certifique-se de que estas estão configuradas adequadamente para corresponder aos seus requisitos de carga de trabalho.

### <a name="optimization"></a>Otimização
[Armazenamento Azure Premium: Design para alto desempenho](../../virtual-machines/windows/premium-storage-performance.md) fornece orientações para a construção de aplicações de alto desempenho utilizando o Armazenamento Premium Azure. Pode seguir as diretrizes combinadas com as melhores práticas de desempenho aplicáveis às tecnologias utilizadas pela sua aplicação.

## <a name="prepare-and-copy-virtual-hard-disks-vhds-to-premium-storage"></a><a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Preparar e copiar discos rígidos virtuais (VHDs) para Armazenamento Premium
A seguinte secção fornece orientações para a preparação de VHDs do seu VM e cópia de VHDs para armazenamento azure.

* [Cenário 1: "Estou a migrar os VMs Azure existentes para o Armazenamento Premium Azure."](#scenario1)
* [Cenário 2: "Estou a migrar VMs de outras plataformas para o Armazenamento Premium Azure."](#scenario2)

### <a name="prerequisites"></a>Pré-requisitos
Para preparar os VHDs para migração, você precisará:

* Uma subscrição Azure, uma conta de armazenamento e um recipiente nessa conta de armazenamento para o qual pode copiar o seu VHD. Note que a conta de armazenamento de destino pode ser uma conta standard ou premium de armazenamento, dependendo do seu requisito.
* Uma ferramenta para generalizar o VHD se planeia criar várias instâncias vM a partir dele. Por exemplo, sysprep para Windows ou virt-sysprep para Ubuntu.
* Uma ferramenta para fazer o upload do ficheiro VHD para a conta de Armazenamento. Consulte os dados de transferência com o Utilitário de [Linha de Comando AzCopy](storage-use-azcopy.md) ou utilize um explorador de [armazenamento Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Este guia descreve a cópia do seu VHD utilizando a ferramenta AzCopy.

> [!NOTE]
> Se escolher a opção de cópia sincronizada com a AzCopy, para um desempenho ótimo, copie o seu VHD executando uma destas ferramentas a partir de um VM Azure que se encontra na mesma região que a conta de armazenamento de destino. Se estiver a copiar um VHD de um VM Azure numa região diferente, o seu desempenho pode ser mais lento.
>
> Para copiar uma grande quantidade de dados sobre largura de banda limitada, considere [utilizar o serviço Azure Import/Export para transferir dados para o Armazenamento Blob;](../storage-import-export-service.md) isto permite transferir os seus dados enviando discos rígidos para um datacenter Azure. Pode utilizar o serviço Azure Import/Export para copiar dados apenas para uma conta de armazenamento padrão. Uma vez que os dados estão na sua conta de armazenamento padrão, pode utilizar a [Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou a AzCopy para transferir os dados para a sua conta de armazenamento premium.
>
> Note que o Microsoft Azure apenas suporta ficheiros VHD de tamanho fixo. Os ficheiros VHDX ou VHDs dinâmicos não são suportados. Se tiver um VHD dinâmico, pode convertê-lo em tamanho fixo utilizando o cmdlet [Convert-VHD.](https://technet.microsoft.com/library/hh848454.aspx)
>
>

### <a name="scenario-1-i-am-migrating-existing-azure-vms-to-azure-premium-storage"></a><a name="scenario1"></a>Cenário 1: "Estou a migrar os VMs Azure existentes para o Armazenamento Premium Azure."
Se estiver a migrar os VMs Azure existentes, pare o VM, prepare VHDs por tipo de VHD que deseja e, em seguida, copie o VHD com AzCopy ou PowerShell.

O VM precisa de estar completamente em baixo para migrar um estado limpo. Haverá um tempo de inatividade até que a migração termine.

#### <a name="step-1-prepare-vhds-for-migration"></a>Passo 1. Prepare VHDs para migração
Se estiver a migrar os VMs Azure existentes para o Armazenamento Premium, o seu VHD pode ser:

* Uma imagem generalizada do sistema operativo
* Um disco único do sistema operativo
* Um disco de dados

Abaixo percorremos estes 3 cenários para preparar o seu VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Utilize um VHD do sistema operativo generalizado para criar várias instâncias vm
Se estiver a carregar um VHD que será usado para criar múltiplas instâncias genéricas de VM Azure, primeiro deve generalizar o VHD utilizando um utilitário de sysprep. Isto aplica-se a um VHD que está no local ou na nuvem. A Sysprep remove qualquer informação específica da máquina do VHD.

> [!IMPORTANT]
> Tire uma foto ou faça backup o seu VM antes de generalizá-lo. A sysprep de funcionamento irá parar e desalocar a instância VM. Siga os passos abaixo para sipreparar um VHD do Windows OS. Note que o funcionamento do comando Sysprep exigirá que desligue a máquina virtual. Para mais informações sobre sysprep, consulte [a Visão Geral da Sysprep](https://technet.microsoft.com/library/hh825209.aspx) ou a Referência Técnica [Sysprep](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Abra uma janela de comando como administrador.
2. Insira o seguinte comando para abrir o Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Na ferramenta de preparação do sistema, selecione Enter System Out-of-Box Experience (OOBE), selecione a caixa de verificação Generalize, selecione **Shutdown**, e, em seguida, clique **EM OK**, como mostrado na imagem abaixo. A Sysprep generalizará o sistema operativo e desligará o sistema.

    ![][1]

Para um Ubuntu VM, use virt-sysprep para obter o mesmo. Consulte [virt-sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para mais detalhes. Consulte também alguns dos softwares de provisionamento de servidores [Linux](https://www.cyberciti.biz/tips/server-provisioning-software.html) de origem aberta para outros sistemas operativos Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Utilize um VHD de sistema operativo único para criar uma única instância VM
Se tiver uma aplicação em execução no VM que requer dados específicos da máquina, não generalize o VHD. Um VHD não generalizado pode ser usado para criar uma instância VM Azure única. Por exemplo, se tiver controlador de domínio no seu VHD, executar sysprep tornará-o ineficaz como controlador de domínio. Reveja as aplicações em execução no seu VM e o impacto da execução de sysprep neles antes de generalizar o VHD.

##### <a name="register-data-disk-vhd"></a>Registar o disco de dados VHD
Se tiver discos de dados em Azure para migrar, deve certificar-se de que os VMs que utilizam estes discos de dados são desligados.

Siga os passos abaixo descritos para copiar VHD para O Armazenamento Premium Azure e registá-lo como um disco de dados provisionado.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passo 2. Crie o destino para o seu VHD
Crie uma conta de armazenamento para manter os seus VHDs. Considere os seguintes pontos ao planear onde armazenar os seus VHDs:

* A conta de armazenamento premium alvo.
* A localização da conta de armazenamento deve ser a mesma que os VMs Azure capazes de armazenamento premium que irá criar na fase final. Pode copiar para uma nova conta de armazenamento, ou planear usar a mesma conta de armazenamento com base nas suas necessidades.
* Copie e guarde a chave da conta de armazenamento da conta de armazenamento de destino para a próxima fase.

Para discos de dados, pode optar por manter alguns discos de dados numa conta de armazenamento padrão (por exemplo, discos que têm armazenamento mais frio), mas recomendamos vivamente que movimente todos os dados para a carga de trabalho de produção usar armazenamento premium.

#### <a name="step-3-copy-vhd-with-azcopy-or-powershell"></a><a name="copy-vhd-with-azcopy-or-powershell"></a>Passo 3: Copiar VHD com AzCopy ou PowerShell
Você precisará encontrar o seu caminho de contentor e a chave da conta de armazenamento para processar qualquer uma destas duas opções. A chave do caminho do contentor e da conta de armazenamento pode ser encontrada no > **Armazenamento**do **Portal Azure.** O URL do recipiente será\/como "https: /myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opção 1: Copiar um VHD com AzCopy (cópia assíncrona)

Utilizando o AzCopy, pode facilmente fazer o upload do VHD pela Internet. Dependendo do tamanho dos VHDs, isto pode levar tempo. Lembre-se de verificar os limites de entrada/saída da conta de armazenamento ao utilizar esta opção. Consulte [a escalabilidade e os alvos de desempenho para obter](scalability-targets-standard-account.md) detalhes sobre as contas de armazenamento padrão.

1. Descarregue e instale a AzCopy a partir daqui: [Versão mais recente do AzCopy](https://aka.ms/downloadazcopy)
2. Abra o Azure PowerShell e vá para a pasta onde o AzCopy está instalado.
3. Utilize o seguinte comando para copiar o ficheiro VHD de "Fonte" para "Destino".

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Exemplo:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```
 
   Aqui estão as descrições dos parâmetros utilizados no comando AzCopy:

   * **/Fonte:** _ &lt;&gt;fonte :_ Localização do URL da pasta ou do recipiente de armazenamento que contém o VHD.
   * **/FonteChave:** _ &lt;chave fonte-conta&gt;:_ Chave da conta de armazenamento da conta de armazenamento de origem.
   * **/Dest:** _ &lt;&gt;destino :_ URL do recipiente de armazenamento para copiar o VHD para.
   * **/DestKey:** _ &lt;chave&gt;de conta dedes:_ Chave da conta de armazenamento da conta de armazenamento de destino.
   * **/Padrão:** _ &lt;nome&gt;de ficheiro :_ Especifique o nome do ficheiro do VHD para copiar.

Para mais detalhes sobre a utilização da ferramenta AzCopy, consulte [os dados de transferência com o utilitário da linha de comando AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opção 2: Copiar um VHD com PowerShell (cópia sincronizada)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Também pode copiar o ficheiro VHD utilizando o PowerShell cmdlet Start-AzStorageBlobCopy. Utilize o seguinte comando no Azure PowerShell para copiar VHD. Substitua os valores em <> pelos valores correspondentes da sua conta de armazenamento de origem e destino. Para utilizar este comando, deve ter um contentor chamado vhds na sua conta de armazenamento de destino. Se o recipiente não existir, crie um antes de executar o comando.

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

### <a name="scenario-2-i-am-migrating-vms-from-other-platforms-to-azure-premium-storage"></a><a name="scenario2"></a>Cenário 2: "Estou a migrar VMs de outras plataformas para o Armazenamento Premium Azure."
Se estiver a migrar VHD do armazenamento em nuvem não Azure para Azure, tem primeiro de exportar o VHD para um diretório local. Tenha o percurso de origem completo do diretório local onde o VHD é armazenado à mão e, em seguida, use a AzCopy para carregá-lo para o Armazenamento Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Passo 1. Exportação VHD para um diretório local
##### <a name="copy-a-vhd-from-aws"></a>Copiar um VHD da AWS
1. Se estiver a usar AWS, exporte a instância EC2 para um VHD num balde Amazon S3. Siga os passos descritos na documentação da Amazon para exportar as instâncias Da Amazon EC2 para instalar a ferramenta de interface de linha de comando (CLI) da Amazon EC2 e executar o comando de tarefas de exportação de instâncias para exportar a instância EC2 para um ficheiro VHD. Certifique-se de que utiliza **VHD** para o disco&#95;imagem&#95;variável FORMAT ao executar o comando de **tarefas de criação de instâncias de exportação.** O ficheiro VHD exportado é guardado no balde Amazon S3 que designa durante esse processo.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Descarregue o ficheiro VHD do balde S3. Selecione o ficheiro VHD e, em seguida, o**Download** **de Ações** > .

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copiar um VHD de outra nuvem não-Azure
Se estiver a migrar VHD do armazenamento em nuvem não Azure para Azure, tem primeiro de exportar o VHD para um diretório local. Copie o percurso de origem completo do diretório local onde o VHD está armazenado.

##### <a name="copy-a-vhd-from-on-premises"></a>Copiar um VHD a partir de instalações
Se estiver a migrar VHD de um ambiente no local, necessitará do caminho de origem completo onde o VHD está armazenado. O caminho de origem pode ser uma localização do servidor ou uma partilha de ficheiros.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passo 2. Crie o destino para o seu VHD
Crie uma conta de armazenamento para manter os seus VHDs. Considere os seguintes pontos ao planear onde armazenar os seus VHDs:

* A conta de armazenamento alvo pode ser armazenamento padrão ou premium dependendo do seu requisito de candidatura.
* A região da conta de armazenamento deve ser a mesma que os VMs Azure capazes de armazenamento premium que irá criar na fase final. Pode copiar para uma nova conta de armazenamento, ou planear usar a mesma conta de armazenamento com base nas suas necessidades.
* Copie e guarde a chave da conta de armazenamento da conta de armazenamento de destino para a próxima fase.

Recomendamos vivamente que movimente todos os dados para a carga de trabalho de produção para utilizar armazenamento premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Passo 3. Faça upload do VHD para o Armazenamento Azure
Agora que tem o seu VHD no diretório local, pode utilizar o AzCopy ou o AzurePowerShell para fazer o upload do ficheiro .vhd para o Armazenamento Azure. Ambas as opções são fornecidas aqui:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opção 1: Utilização do Add-AzureVhd da PowerShell AzureVhd para carregar o ficheiro .vhd

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Um \<exemplo que Uri> pode ser **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd._**" . Um \<exemplo De FileInfo> pode ser **_"C:\path\to\upload.vhd"_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opção 2: Utilizar o AzCopy para carregar o ficheiro .vhd

Utilizando o AzCopy, pode facilmente fazer o upload do VHD pela Internet. Dependendo do tamanho dos VHDs, isto pode levar tempo. Lembre-se de verificar os limites de entrada/saída da conta de armazenamento ao utilizar esta opção. Consulte [a escalabilidade e os alvos de desempenho para obter](scalability-targets-standard-account.md) detalhes sobre as contas de armazenamento padrão.

1. Descarregue e instale a AzCopy a partir daqui: [Versão mais recente do AzCopy](https://aka.ms/downloadazcopy)
2. Abra o Azure PowerShell e vá para a pasta onde o AzCopy está instalado.
3. Utilize o seguinte comando para copiar o ficheiro VHD de "Fonte" para "Destino".

   ```azcopy
      AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

   Exemplo:

   ```azcopy
      AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
   ```

   Aqui estão as descrições dos parâmetros utilizados no comando AzCopy:

   * **/Fonte:** _ &lt;&gt;fonte :_ Localização do URL da pasta ou do recipiente de armazenamento que contém o VHD.
   * **/FonteChave:** _ &lt;chave fonte-conta&gt;:_ Chave da conta de armazenamento da conta de armazenamento de origem.
   * **/Dest:** _ &lt;&gt;destino :_ URL do recipiente de armazenamento para copiar o VHD para.
   * **/DestKey:** _ &lt;chave&gt;de conta dedes:_ Chave da conta de armazenamento da conta de armazenamento de destino.
   * **/BlobType: página:** Especifica que o destino é uma mancha de página.
   * **/Padrão:** _ &lt;nome&gt;de ficheiro :_ Especifique o nome do ficheiro do VHD para copiar.

Para mais detalhes sobre a utilização da ferramenta AzCopy, consulte [os dados de transferência com o utilitário da linha de comando AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD
Também pode fazer o upload de um VHD para a sua conta de armazenamento utilizando um dos seguintes meios:

* [Azure Storage Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Blobs do Explorador de Armazenamento Azure](https://azurestorageexplorer.codeplex.com/)
* [Referência api api do serviço de importação/exportação de armazenagem REST](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Recomendamos a utilização do Serviço de Importação/Exportação se o tempo estimado de upload for superior a 7 dias. Pode utilizar [dataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para estimar o tempo a partir do tamanho dos dados e unidade de transferência.
>
> A importação/exportação pode ser utilizada para copiar para uma conta de armazenamento padrão. Terá de copiar do armazenamento padrão para a conta de armazenamento premium utilizando uma ferramenta como a AzCopy.
>
>

## <a name="create-azure-vms-using-premium-storage"></a><a name="create-azure-virtual-machine-using-premium-storage"></a>Criar VMs Azure usando armazenamento premium
Depois de o VHD ser carregado ou copiado para a conta de armazenamento desejada, siga as instruções nesta secção para registar o VHD como uma imagem de OS, ou disco OS dependendo do seu cenário e, em seguida, crie uma instância VM a partir dele. O vHD do disco de dados pode ser ligado ao VM uma vez criado.
Um roteiro de migração de amostras é fornecido no final desta secção. Este simples guião não corresponde a todos os cenários. Poderá ter de atualizar o script para corresponder ao seu cenário específico. Para ver se este script se aplica ao seu cenário, veja abaixo um Script de [Migração de Amostras](#a-sample-migration-script).

### <a name="checklist"></a>Lista de Verificação
1. Aguarde até que todos os discos VHD esteja completo.
2. Certifique-se de que o Armazenamento Premium está disponível na região para onde está a migrar.
3. Decida a nova série VM que vai utilizar. Deve ser um Armazenamento Premium capaz, e o tamanho deve ser dependendo da disponibilidade na região e com base nas suas necessidades.
4. Decida o tamanho exato do VM que irá utilizar. O tamanho do VM tem de ser grande o suficiente para suportar o número de discos de dados que tem. Por exemplo, se tiver 4 discos de dados, o VM deve ter 2 ou mais núcleos. Além disso, considere o poder de processamento, a memória e as necessidades de largura de banda da rede.
5. Crie uma conta de Armazenamento Premium na região alvo. Esta é a conta que vai utilizar para o novo VM.
6. Tenha os detalhes vm atuais à mão, incluindo a lista de discos e as correspondentes bolhas VHD.

Prepare o seu pedido para o tempo de descanso. Para fazer uma migração limpa, tem que parar todo o processamento no sistema atual. Só assim pode fazê-lo para um estado consistente, que pode migrar para a nova plataforma. A duração do tempo de inatividade dependerá da quantidade de dados nos discos para migrar.

> [!NOTE]
> Se estiver a criar um VM do Gestor de Recursos Azure a partir de um Disco VHD especializado, consulte [este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) para implementar o Gestor de Recursos VM utilizando o disco existente.
>
>

### <a name="register-your-vhd"></a>Registe o seu VHD
Para criar um VM a partir de VHD osS ou para anexar um disco de dados a um novo VM, tem primeiro de os registar. Siga os passos abaixo, dependendo do cenário do seu VHD.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>VHD do Sistema Operativo Generalizado para criar múltiplas instâncias de VM Azure
Depois de a imagem de Os generalizada VHD ser enviada para a conta de armazenamento, registe-a como uma **Imagem Azure VM** para que possa criar uma ou mais instâncias vM a partir dela. Utilize os seguintes cmdlets PowerShell para registar o seu VHD como uma imagem De Os O Azure VM. Forneça o URL completo do recipiente para onde o VHD foi copiado.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Copie e guarde o nome desta nova Imagem Azure VM. No exemplo acima, é *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>VHD do sistema operativo único para criar uma única instância Azure VM
Depois de o VHD único ser enviado para a conta de armazenamento, registe-o como um **Disco Os Azure para** que possa criar uma instância VM a partir dela. Utilize estes cmdlets PowerShell para registar o seu VHD como um Disco Os Azure. Forneça o URL completo do recipiente para onde o VHD foi copiado.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Copie e guarde o nome deste novo Disco Os Azure. No exemplo acima, é *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>VHD do disco de dados a ser anexado às novas instâncias De VM Azure
Depois de o disco de dados VHD ser enviado para a conta de armazenamento, registe-o como um Disco de Dados Azure para que possa ser anexado à sua nova série DS, série DSv2 ou gS Series Azure VM.

Utilize estes cmdlets PowerShell para registar o seu VHD como um Disco de Dados Azure. Forneça o URL completo do recipiente para onde o VHD foi copiado.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Copie e guarde o nome deste novo Disco de Dados Azure. No exemplo acima, é *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Criar um VM de armazenamento premium
Uma vez registado o disco de imagem OS ou OS, crie uma nova série DS, série DSv2 ou VM da série GS. Estará a utilizar a imagem do sistema operativo ou o nome do disco do sistema operativo que registou. Selecione o tipo VM do nível de Armazenamento Premium. Por exemplo abaixo, estamos a usar o *tamanho vm Standard_DS2.*

> [!NOTE]
> Atualize o tamanho do disco para se certificar de que corresponde aos requisitos de capacidade e desempenho e aos tamanhos de disco azure disponíveis.
>
>

Siga o passo passo passo powerShell cmdlets abaixo para criar o novo VM. Primeiro, definir os parâmetros comuns:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Primeiro, crie um serviço na nuvem no qual irá acolher os seus novos VMs.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Em seguida, dependendo do seu cenário, crie a instância Azure VM a partir da Imagem OS ou do Disco OS que registou.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>VHD do Sistema Operativo Generalizado para criar múltiplas instâncias de VM Azure
Crie as novas instâncias DS Series Azure VM utilizando a **Imagem Os Azure** que registou. Especifique este nome de imagem OS na configuração VM ao criar um novo VM, como mostrado abaixo.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>VHD do sistema operativo único para criar uma única instância Azure VM
Crie uma nova instância Azure VM da série DS utilizando o **Disco Os Azure** que registou. Especifique este nome de Disco OS na configuração VM ao criar o novo VM, como mostrado abaixo.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Especifique outras informações de VM Azure, tais como um serviço na nuvem, região, conta de armazenamento, conjunto de disponibilidade e política de cache. Note que a instância VM deve ser co-localizada com sistema operativo associado ou discos de dados, pelo que o serviço de nuvem selecionado, a região e a conta de armazenamento devem estar todos no mesmo local que os VHDs subjacentes desses discos.

### <a name="attach-data-disk"></a>Anexar disco de dados
Por último, se tiver VHDs de disco de dados registados, prenda-os ao novo Premium Storage capaz de Obter VM.

Utilize o seguinte cmdlet PowerShell para anexar o disco de dados ao novo VM e especificar a política de cache. Por exemplo, abaixo da política de cache é definido para *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Pode haver passos adicionais necessários para apoiar a sua aplicação que não esteja abrangida por este guia.
>
>

### <a name="checking-and-plan-backup"></a>Verificação e plano de backup
Uma vez que o novo VM esteja em funcionamento, aceda-o utilizando o mesmo id de login e a palavra-passe é como o VM original, e verifique se está tudo a funcionar como esperado. Todas as definições, incluindo os volumes listrados, estariam presentes no novo VM.

O último passo é planear o calendário de backup e manutenção para o novo VM com base nas necessidades da aplicação.

### <a name="a-sample-migration-script"></a><a name="a-sample-migration-script"></a>Um roteiro de migração de amostras
Se tiver vários VMs para migrar, a automatização através dos scripts PowerShell será útil. Segue-se um guião de amostra que automatiza a migração de um VM. Note que abaixo do script é apenas um exemplo, e há poucos pressupostos feitos sobre os discos VM atuais. Poderá ter de atualizar o script para corresponder ao seu cenário específico.

As suposições são:

* Estás a criar VMs azure clássicos.
* Os discos operativos operativos e os discos de dados de origem estão na mesma conta de armazenamento e no mesmo recipiente. Se os seus Discos OS e Discos de Dados não estiverem no mesmo local, pode utilizar o AzCopy ou o Azure PowerShell para copiar VHDs sobre contas de armazenamento e contentores. Consulte o passo anterior: [Copiar VHD com AzCopy ou PowerShell](#copy-vhd-with-azcopy-or-powershell). Editar este script para atender ao seu cenário é outra escolha, mas recomendamos usar a AzCopy ou powerShell, uma vez que é mais fácil e rápido.

O script de automação é fornecido abaixo. Substitua o texto com as suas informações e atualize o script para corresponder ao seu cenário específico.

> [!NOTE]
> A utilização do script existente não preserva a configuração de rede do vM de origem. Terá de reconefificar as definições de rede nos seus VMs migrados.
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

#### <a name="optimization"></a><a name="optimization"></a>Otimização
A configuração vm atual pode ser personalizada especificamente para funcionar bem com discos Standard. Por exemplo, para aumentar o desempenho utilizando muitos discos num volume listrado. Por exemplo, em vez de utilizar 4 discos separadamente no Armazenamento Premium, poderá ser capaz de otimizar o custo com um único disco. Otimizações como esta precisam de ser tratadas caso a caso e requerem passos personalizados após a migração. Além disso, note que este processo pode não funcionar muito bem para bases de dados e aplicações que dependem do layout do disco definido na configuração.

##### <a name="preparation"></a>Preparação
1. Complete a Migração Simples como descrito na secção anterior. As otimizações serão realizadas no novo VM após a migração.
2. Defina os novos tamanhos de disco necessários para a configuração otimizada.
3. Determine o mapeamento dos discos/volumes atuais às novas especificações do disco.

##### <a name="execution-steps"></a>Etapas de execução
1. Crie os novos discos com os tamanhos certos no VM de Armazenamento Premium.
2. Inicie sessão no VM e copie os dados do volume atual para o novo disco que mapeia para esse volume. Faça isto por todos os volumes atuais que precisam mapear para um novo disco.
3. Em seguida, altere as definições de aplicação para mudar para os novos discos e desabote os volumes antigos.

Para afinar a aplicação para um melhor desempenho do disco, consulte a secção de desempenho da aplicação otimizada do nosso desenho para artigo de [alto desempenho.](../../virtual-machines/windows/premium-storage-performance.md)

### <a name="application-migrations"></a>Migrações de candidaturas
As bases de dados e outras aplicações complexas podem exigir passos especiais definidos pelo fornecedor de aplicações para a migração. Consulte a documentação da respetiva aplicação. Por exemplo, normalmente, as bases de dados podem ser migradas através de cópias de segurança e restauro.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes recursos para cenários específicos para máquinas virtuais migratórias:

* [Máquinas virtuais migrate Azure entre contas de armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Crie e carregue um VHD do Windows Server para o Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Criação e upload de um Linux VHD para Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Máquinas virtuais migratórias da Amazon AWS para o Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Consulte também os seguintes recursos para saber mais sobre o Armazenamento Azure e as Máquinas Virtuais Azure:

* [Storage do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas Virtuais Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Selecionar um tipo de disco para VMs IaaS](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
