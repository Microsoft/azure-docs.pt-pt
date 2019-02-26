---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: ramankumarlive
ms.service: storage
ms.topic: include
ms.date: 09/24/2018
ms.author: ramankum
ms.custom: include file
ms.openlocfilehash: cd29fb968bd73e09c2e3b60e62a478b4e577146d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805491"
---
# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Armazenamento Premium de elevado desempenho e os discos geridos para VMs

Armazenamento Premium do Azure fornece suporte de discos de elevado desempenho e de baixa latência para máquinas virtuais (VMs) com a entrada/saída (e/s)-cargas de trabalho intensivas. Discos de VM que utilizam o armazenamento Premium armazenam dados em unidades de estado sólido (SSDs). Para tirar partido da velocidade e o desempenho de discos de armazenamento premium, pode migrar os discos de VM existentes para o armazenamento Premium.

No Azure, pode anexar vários discos de armazenamento premium a uma VM. Utilizar vários discos proporciona a seus aplicativos até 256 TB de armazenamento por VM, se usar os tamanhos de pré-visualização seu aplicativo pode ter até PiB cerca de 2 de armazenamento por VM. Com o armazenamento Premium, seus aplicativos podem obter 80 000 operações de e/s por segundo (IOPS) por VM e um débito de disco de até 2000 megabytes por segundo (MB/s) por VM. Operações de leitura dão-lhe muito baixas latências.

Com o armazenamento Premium, o Azure oferece a capacidade de realmente migração lift-and-shift aplicações empresariais necessárias, como o Dynamics AX, o Dynamics CRM, o Exchange Server, o SAP Business Suite e o SharePoint farms de servidores para a cloud. Pode executar cargas de trabalho de base de dados com uso intensivo de desempenho em aplicativos como o SQL Server, Oracle, MongoDB, MySQL e Redis, que requerem desempenho consistente de alta e baixa latência.

> [!NOTE]
> Para obter o melhor desempenho para a sua aplicação, recomendamos que migre qualquer disco VM que requer o IOPS elevado para o armazenamento Premium. Se o disco não necessita de IOPS elevado, pode ajudar os custos de limite mantê-lo no armazenamento do Azure standard. No armazenamento standard, os dados de disco VM são armazenados em unidades de disco rígido (HDDs) em vez de no SSDs.

O Azure oferece duas maneiras de criar discos de armazenamento premium para as VMs:

* **Discos não geridos**

    O método original é utilizar discos não geridos. Num disco não gerido, gerir as contas de armazenamento que utilizar para armazenar os ficheiros de disco rígido virtual (VHD) que correspondem aos seus discos VM. Ficheiros VHD são armazenados como blobs de páginas em contas de armazenamento do Azure. 

* **Discos geridos**

    Quando escolhe [Managed Disks do Azure](../articles/virtual-machines/windows/managed-disks-overview.md), o Azure gere as contas de armazenamento que utilizar para os discos da VM. Especifique o tipo de disco (Premium ou Standard) e o tamanho do disco que precisa. O Azure cria e gere o disco por si. Não precisa se preocupar sobre os discos são colocados em várias contas de armazenamento para se certificar de que permaneça dentro dos limites de escalabilidade para as suas contas de armazenamento. O Azure trata que por si.

Recomendamos que escolha os discos geridos, para tirar partido dos seus muitos recursos.

Para começar a utilizar o armazenamento Premium, [crie uma conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Para obter informações sobre como migrar as suas VMs existentes para o armazenamento Premium, consulte [converter uma VM do Windows de discos não geridos para managed disks](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) ou [converter uma VM do Linux de discos não geridos para managed disks](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Armazenamento Premium está disponível na maioria das regiões. Para obter a lista de regiões disponíveis, consulte a linha para **armazenamento de disco** na [produtos do Azure disponíveis por região](https://azure.microsoft.com/regions/#services).

## <a name="features"></a>Funcionalidades

Aqui estão alguns dos recursos do armazenamento Premium:

* **Discos de armazenamento Premium**

    O armazenamento Premium suporta discos VM que podem ser anexados a VMs de série de tamanho específicas. O armazenamento Premium suporta uma grande variedade de VMs do Azure. Tem uma opção de oito tamanhos de disco de disponibilidade geral:  P4 (32 GiB), P6 (64 GiB), P10 (128 GiB), P15 (256 GiB), P20 (512 GiB), P30 (1,024 GiB), P40 (2,048 GiB), P50 (4,095 GiB). Bem como três tamanhos de disco de pré-visualização: P60 8.192 GiB (8 TiB), P70 16,348 GiB (16 TiB), P80 32.767 GiB (32 TiB). Tamanhos de disco P4, P6, P15, P60, P70 e P80 estão atualmente apenas suportado para discos geridos. Cada tamanho de disco tem seus próprio especificações de desempenho. Dependendo dos requisitos de aplicação, pode anexar um ou mais discos à sua VM. Descrevemos as especificações de forma mais detalhada [metas de escalabilidade e desempenho do armazenamento Premium](#scalability-and-performance-targets).

* **Blobs de páginas Premium**

    O armazenamento Premium suporta blobs de páginas. Utilize blobs de páginas para armazenar discos persistentes e não geridos para VMs no armazenamento Premium. Ao contrário do armazenamento do Azure standard, o armazenamento Premium não suportam blobs de blocos, blobs, ficheiros, tabelas ou filas de acréscimo. Blobs de páginas Premium suportam tamanhos de seis de P10 P50 e P60 (8191GiB). Blob de páginas P60 Premium não é suportado para ser anexados como discos VM. 

    Qualquer objeto colocado numa conta de armazenamento premium será um blob de página. O blob de página de suporte de dados ajusta-se a um dos tamanhos suportados aprovisionados. É por isso uma conta de armazenamento premium não se destina a ser utilizado para armazenar blobs muito pequenos.

* **Conta de armazenamento Premium**

    Para começar a utilizar o armazenamento Premium, crie uma conta de armazenamento premium para discos não geridos. Na [portal do Azure](https://portal.azure.com), para criar uma conta de armazenamento premium, escolha a **Premium** escalão de desempenho. Selecione o **armazenamento localmente redundante (LRS)** opção de replicação. Também pode criar uma conta de armazenamento premium ao definir o escalão de desempenho **Premium_LRS**. Para alterar o escalão de desempenho, utilize uma das abordagens seguintes:
     
    - [PowerShell para o armazenamento do Azure](../articles/storage/common/storage-powershell-guide-full.md#manage-the-storage-account)
    - [CLI do Azure para o armazenamento do Azure](../articles/storage/common/storage-azure-cli.md#manage-storage-accounts)
    - [Azure API de REST do fornecedor de recursos de armazenamento](https://docs.microsoft.com/rest/api/storagerp) (para implementações do Azure Resource Manager) ou uma das bibliotecas de cliente do fornecedor de recursos do armazenamento do Azure

    Para saber mais sobre os limites de conta de armazenamento premium, veja [metas de escalabilidade e desempenho](#scalability-and-performance-targets).

* **Armazenamento localmente redundante Premium**

    Uma conta de armazenamento premium suporta armazenamento apenas localmente redundante, como a opção de replicação. Armazenamento localmente redundante mantém três cópias dos dados numa única região. Para a recuperação de desastre regional, é necessário fazer backup os discos da VM numa região diferente, utilizando [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md). Também tem de utilizar uma conta de armazenamento georredundante (GRS) que o Cofre de cópia de segurança. 

    O Azure utiliza a conta de armazenamento como um contêiner para os discos não geridos. Quando cria uma VM do Azure que suporte o armazenamento Premium com discos não geridos e que selecione uma conta de armazenamento premium, o sistema operativo e os discos de dados são armazenados nessa conta de armazenamento.

## <a name="supported-vms"></a>VMs suportadas

Armazenamento Premium é suportado numa grande variedade de VMs do Azure. Pode utilizar discos de armazenamento standard e premium com esses tipos VM. Não é possível utilizar discos de armazenamento premium com a série VM que não sejam Premium compatível com o armazenamento.


Para obter informações sobre os tipos e os tamanhos de VMs no Azure para Windows, veja [Windows VM sizes](../articles/virtual-machines/windows/sizes.md) (Tamanhos de VMs do Windows). Para obter informações sobre os tipos e os tamanhos de VMs no Azure para Linux, veja [Linux VM sizes](../articles/virtual-machines/linux/sizes.md) (Tamanhos de VMs do Linux).

Estes são alguns dos recursos suportados no premium armazenamento ativado VMs:

* **Conjunto de disponibilidade**

    Usando o exemplo de uma VMs de série DS, pode adicionar uma VM de série DS para um serviço em nuvem que tem apenas as VMs de série DS. Não adicione VMs de série DS para um serviço cloud existente com qualquer tipo diferente de VMs de série DS. Pode migrar os VHDs existentes para um novo serviço cloud que é executado apenas as VMs de série DS. Se pretender utilizar o mesmo endereço IP virtual para o novo serviço em nuvem que aloja as suas VMs de série DS, utilize [endereços IP reservados](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

* **Disco do sistema operativo**

    Pode configurar a sua VM de armazenamento Premium para utilizar uma premium ou um disco de sistema operativo padrão. Para obter a melhor experiência possível, recomendamos que utilize um disco de sistema operacional baseado em armazenamento Premium.

* **Discos de dados**

    Pode utilizar os discos standard e premium na mesma VM de armazenamento Premium. Com o armazenamento Premium, pode aprovisionar uma VM e anexar vários discos de dados persistentes para a VM. Se for necessário, para aumentar a capacidade e desempenho do volume, pode do stripe em seus discos.

    > [!NOTE]
    > Se da faixa de discos de dados de armazenamento premium, utilizando [espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx), configurar espaços de armazenamento com 1 coluna para cada disco que utiliza. Caso contrário, o desempenho geral do volume repartido pode ser mais baixo de que o esperado devido a distribuição desigual de tráfego em todos os discos. Por predefinição, no Gestor de servidores, pode configurar colunas para até 8 discos. Se tiver mais de 8 discos, utilize o PowerShell para criar o volume. Especifique o número de colunas manualmente. Caso contrário, a IU do Gestor de servidor continua a utilizar 8 colunas, mesmo que tenha mais discos. Por exemplo, se tiver 32 discos num conjunto único stripe, especifica colunas de 32. Para especificar o número de colunas, além do disco virtual utiliza a [New-VirtualDisk](https://technet.microsoft.com/library/hh848643.aspx) cmdlet do PowerShell, utilize o *NumberOfColumns* parâmetro. Para obter mais informações, consulte [descrição geral dos espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx) e [FAQs de espaços de armazenamento](https://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).

* **Cache**

    Máquinas virtuais (VMs) que suportam o armazenamento Premium tem uma capacidade única de colocação em cache para níveis mais elevados de débito e latência reduzida. Sua capacidade de colocação em cache excede o desempenho do disco de armazenamento premium subjacente. No entanto, nem todas as VMs do suporte de colocação em cache pelo que deve rever as especificações de VM para os tamanhos VM que está interessado para obter mais informações.  As VMs que suportam a colocação em cache indicará isso em suas especificações com uma medida de "Débito do armazenamento temporário e em cache de Max".  Eles também são especificados diretamente sob o título VM.
    
    Com a colocação em cache, pode definir o disco política nos discos de armazenamento premium para a colocação em cache **só de leitura**, **ReadWrite**, ou **None**. O disco de predefinido a política de colocação em cache está **só de leitura** para todos os discos de dados de premium, e **ReadWrite** para discos de sistema operativo. Para um desempenho ideal para inicie seu aplicativo não se esqueça de utilizar a definição de cache correta. 
    
    Como exemplos, para discos de dados de leitura intensiva ou só de leitura, como arquivos de dados do SQL Server, defina o disco de política para a colocação em cache **só de leitura**. Para discos de dados de escrita intensiva ou só de escrita, como ficheiros de registo do SQL Server, defina o disco de política para a colocação em cache **None**. 
    
    Para saber mais sobre como otimizar seu design com o armazenamento Premium, veja [Design para desempenho com o armazenamento Premium](../articles/virtual-machines/windows/premium-storage-performance.md).

* **Análise**

    Para analisar o desempenho da VM com discos no armazenamento Premium, ativar os diagnósticos VM no [portal do Azure](https://portal.azure.com). Para obter mais informações, consulte [monitorização de VM do Azure com a extensão de diagnóstico do Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Para ver o desempenho do disco, use as ferramentas com base no sistema operativo como [Monitor de desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) para as VMs do Windows e o [iostat](http://linux.die.net/man/1/iostat) comando para VMs do Linux.

* **Limites de dimensionamento VM e o desempenho**

    Cada tamanho de VM de armazenamento Premium suportado tem limites de dimensionamento e especificações de desempenho de IOPS, largura de banda e o número de discos que podem ser anexados por VM. Ao utilizar discos de armazenamento premium com VMs, certifique-se de que existe suficiente IOPS e largura de banda na sua VM para o tráfego de disco de unidade.

    Por exemplo, uma VM de STANDARD_DS1 tem uma largura de banda dedicada de 32 MB/s para o tráfego de disco de armazenamento premium. Um disco de armazenamento premium P10 pode fornecer uma largura de banda de 100 MB/s. Se um disco de armazenamento premium P10 é anexado a esta VM, pode fazer até 32 MB/s. Ele não é possível usar o máximo 100 MB/s que pode fornecer o disco P10.

    Atualmente, a VM maior a série DS é o Standard_DS15_v2. O Standard_DS15_v2 pode fornecer até 960 MB/s em todos os discos. A VM maior na série GS é o Standard_GS5. O Standard_GS5 pode fornecer até 2.000 MB/s em todos os discos.

    Estes limites são para apenas o tráfego de disco. Estes limites não incluem acertos na cache e o tráfego de rede. Uma largura de banda separada está disponível para tráfego de rede VM. Largura de banda para tráfego de rede é diferente da largura de banda dedicada utilizada por discos de armazenamento premium.

    Para obter as informações mais atualizadas sobre o máximo IOPS e débito (largura de banda) para VMs de armazenamento Premium suportado, consulte [tamanhos de VM do Windows](../articles/virtual-machines/windows/sizes.md) ou [tamanhos de VM do Linux](../articles/virtual-machines/linux/sizes.md).

    Para obter mais informações sobre os discos de armazenamento premium e os respetivos limites IOPS e débito, consulte a tabela na secção seguinte.

## <a name="scalability-and-performance-targets"></a>Metas de escalabilidade e desempenho
Nesta seção, descrevemos os destinos de escalabilidade e desempenho a ter em consideração quando utilizar o armazenamento Premium.

Contas de armazenamento Premium tem os seguintes destinos de escalabilidade:

| Capacidade de conta total | Largura de banda total de uma conta de armazenamento localmente redundante |
| --- | --- | 
| Capacidade do disco: 35 TB <br>Capacidade do instantâneo: 10 TB | Cópia de segurança para 50 gigabits por segundo de entrada<sup>1</sup> + saída<sup>2</sup> |

<sup>1</sup> todos os dados (pedidos), que são enviados para uma conta de armazenamento

<sup>2</sup> todos os dados (respostas) que são recebidos a partir de uma conta de armazenamento

Para obter mais informações, consulte [metas de escalabilidade e desempenho do armazenamento do Azure](../articles/storage/common/storage-scalability-targets.md).

Se estiver a utilizar contas de armazenamento premium para discos não geridos e seu aplicativo excede os destinos de escalabilidade de uma única conta de armazenamento, pode querer migrar para discos geridos. Se não quiser migrar para discos geridos, crie a sua aplicação para utilizar várias contas de armazenamento. Em seguida, particione os dados entre essas contas de armazenamento. Por exemplo, se quiser anexar discos de 51-TB em várias VMs, espalhá-los entre duas contas de armazenamento. 35 TB é o limite para uma conta de armazenamento premium única. Certifique-se de que uma conta de armazenamento premium única nunca tem mais de 35 TB de discos aprovisionados.

### <a name="premium-storage-disk-limits"></a>Limites de disco de armazenamento Premium
Quando aprovisiona um disco de armazenamento premium, o tamanho do disco determina o máximo IOPS e débito (largura de banda). O Azure oferece oito GA tipos de discos de armazenamento premium: P4 (discos geridos apenas), P6 (discos geridos apenas), P10, P15 (discos geridos apenas), P20, P30, P40 e P50. Bem como três tamanhos de disco de pré-visualização: P60 P70 e P80. Cada tipo de disco de armazenamento premium tem limites específicos de IOPS e débito. Limites para os tipos de disco são descritas na tabela a seguir:

Tamanhos marcados com um asterisco estão atualmente em pré-visualização.

| Tipo de discos Premium  | P4    | P6    | P10    | P15    | P20    | P30              | P40             | P50             | P60 *            | P70 *               | P80 *               |
|---------------------|-------|-------|--------|--------|--------|------------------|-----------------|-----------------|-----------------|--------------------|--------------------|
| Tamanho do disco           | 32 GiB| 64 GiB| 128 GiB| 256 GiB| 512 GiB| 1024 giB (1 TiB) | 2048 giB (2 TiB)| 4095 giB (4 TiB)| 8192 GiB (8 TiB)| 16,384 giB (TiB de 16)| 32.767 giB (32 TiB)|
| IOPs por disco       | 120   | 240   | 500    | 1100   | 2300   | 5000             | 7500            | 7500            | 12.500          | 15 000             | 20,000             |
| Débito por disco | 25 MB por segundo | 50 MB por segundo | 100 MB por segundo | 125 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo | 480 MB por segundo | 750 MB por segundo | 750 MB por segundo |

> [!NOTE]
> Certifique-se de largura de banda suficiente está disponível na sua VM para o tráfego de disco de unidade, conforme descrito em [VMs suportadas de](#supported-vms). Caso contrário, o débito de disco e IOPS é restrito para reduzir os valores. Débito máximo e IOPS baseiam-se sobre os limites VM, não aos limites de disco descritos na tabela anterior.  
> Azure foi concebido a plataforma de armazenamento Premium para ser paralelo em grande escala. Criação do seu aplicativo para ser com múltiplos threads irá ajudar a alcançar o destino de elevado desempenho oferecido sobre os tamanhos de disco maior.

Seguem-se alguns aspetos importantes a saber sobre metas de escalabilidade e desempenho do armazenamento Premium:

* **Desempenho e capacidade aprovisionada**

    Quando aprovisiona um disco de armazenamento premium, ao contrário do armazenamento standard, haverá a garantia de capacidade, IOPS e débito desse disco. Por exemplo, se criar um disco de P50, Azure Aprovisiona a capacidade de armazenamento de 4095 GB, 7.500 IOPS e débito de 250-MB/s desse disco. Seu aplicativo pode usar toda ou parte da capacidade e desempenho. Os discos SSD Premium foram concebidos para fornecer o destino desempenho 99,9% do tempo.

* **Tamanho do disco**

    Azure mapeia o tamanho do disco (arredondado) para a mais próxima premium storage opção de disco, conforme especificado na tabela na secção anterior. Por exemplo, um tamanho de disco de 100 GB é classificado como uma opção de P10. Pode executar até 500 IOPS, com até 100-MB/s débito. Da mesma forma, um disco de tamanho que 400 GB é classificado como P20. Ele pode executar até 2,300 IOPS, com um débito 150-MB/s.

    > [!NOTE]
    > Pode facilmente aumentar o tamanho dos discos existentes. Por exemplo, pode querer aumentar o tamanho de um disco de 30 GB para 128 GB, ou até mesmo a 1 TB. Em alternativa, pode querer converter seu disco P20 para um disco de P30 porque precisa de mais capacidade ou mais IOPS e débito. 

* **Tamanho de e/s**

    O tamanho de e/s é 256 KB. Se os dados que está a ser transferidos são inferior a 256 KB, é considerado 1 unidade de e/s. Tamanhos de e/s maiores são contabilizados como várias e/SS de tamanho 256 KB. Por exemplo, e/s de 1,100 KB é contabilizado como 5 unidades de e/s.

* **Débito**

    O limite de débito inclui escritas no disco e inclui operações de leitura de disco que não são atendidas a partir da cache. Por exemplo, um disco P10 tem 100-MB/s débito por disco. Alguns exemplos de saída válida para um disco P10 são mostrados na tabela a seguir:

    | Débito máx por disco P10 | Leituras de não-cache do disco | Não-cache gravações em disco |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Acertos na cache**

    Acertos na cache não estão limitados pelo IOPS ou débito do disco alocado. Por exemplo, quando utiliza um disco de dados com um **só de leitura** definição da cache numa VM que é suportada pelo armazenamento Premium, leituras atendidos a partir da cache não está sujeitas ao IOPS e débito do disco em maiúsculas. Se a carga de trabalho de um disco for predominantemente leituras, poderá obter débito muito elevado. A cache está sujeito a IOPS separado e limites de débito na VM nível, com base no tamanho VM. As VMs de série DS têm aproximadamente 4.000 IOPS e débito de 33-MB/s por núcleo para a cache de e/s de SSD local. As VMs de série GS têm um limite de 5000 IOPS e débito de 50-MB/s por núcleo para a cache de e/s de SSD local.

## <a name="throttling"></a>Limitação

Limitação poderão ocorrer, se a sua aplicação IOPS ou o débito exceder os limites alocados para um disco de armazenamento premium. Também limitação poderá ocorrer se o seu tráfego de disco total em todos os discos na VM excede o limite de largura de banda disco disponível para a VM. Para evitar a limitação, recomendamos que limite o número de pedidos de e/s para o disco pendentes. Utilize um limite com base em metas de escalabilidade e desempenho para o disco aprovisionou e na largura de banda de disco disponível para a VM.  

A aplicação pode alcançar a menor latência quando ele foi projetado para evitar a limitação. No entanto, se o número de pedidos de e/s para o disco pendentes for demasiado pequeno, seu aplicativo não é possível aproveitar o IOPS máximo e níveis de débito que estão disponíveis para o disco.

Os exemplos seguintes demonstram como calcular os níveis de limitação. Todos os cálculos baseiam-se um tamanho de unidade de e/s de 256 KB.

### <a name="example-1"></a>Exemplo 1

Seu aplicativo processou 495 unidades de e/s de 16 KB de tamanho num segundo num disco P10. As unidades de e/s são contabilizadas como 495 IOPS. Se tentar uma e/s de 2 MB da mesma em segundo lugar, o total de unidades de e/s é igual ao IOPS 495 + 8. Isto acontece porque e de 2 MB/s = unidades de 256 KB/2.048 KB = 8 e/s, quando o tamanho da unidade de e/s é 256 KB. Uma vez que a soma do 495 + 8 excede o limite de IOPS 500 para o disco, limitação ocorre.

### <a name="example-2"></a>Exemplo 2

Seu aplicativo processou 400 unidades de e/s de tamanho de 256 KB num disco P10. A largura de banda total consumida é (400 &#215; 256) / 1024 KB = 100 MB/s. Um disco P10 tem um limite de taxa de transferência de 100 MB/s. Se seu aplicativo tentar efetuar outras operações de e/s nesse segundo, é limitado porque excede o limite alocado.

### <a name="example-3"></a>Exemplo 3

Tem uma VM DS4 com dois discos de P30 ligados. Cada disco P30 é capaz de débito de 200-MB/s. No entanto, uma VM DS4 tem uma capacidade de largura de banda total do disco de 256 MB/s. Não é possível direcionar ambos os discos ligados para o débito máximo nesta VM DS4 ao mesmo tempo. Para resolver este problema, pode suportar o tráfego de 200 MB/s num disco e 56 MB/s no disco outro. Se a soma de seu tráfego de disco exceder 256 MB/s, o tráfego de disco é limitado.

> [!NOTE]
> Se o seu tráfego de disco consiste principalmente em tamanhos de e/s pequenas, seu aplicativo provavelmente irá atingir o limite IOPS antes do limite de taxa de transferência. No entanto, se o tráfego de disco consiste principalmente em grandes tamanhos de e/s, seu aplicativo provavelmente atingirá o limite de taxa de transferência em primeiro lugar, em vez do limite de IOPS. Pode maximizar o IOPS de seu aplicativo e a capacidade de débito através da utilização de tamanhos de e/s ideais. Além disso, pode limitar o número de pedidos de e/s para um disco pendentes.

Para saber mais sobre a criação de elevado desempenho ao utilizar o armazenamento Premium, veja [Design para desempenho com o armazenamento Premium](../articles/virtual-machines/windows/premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Os instantâneos e copiar Blob

Para o serviço de armazenamento, o ficheiro VHD é um blob de página. Pode criar instantâneos de blobs de páginas e copiá-los para outra localização, por exemplo, para outra conta de armazenamento.

### <a name="unmanaged-disks"></a>Discos não geridos

Crie [instantâneos incrementais](../articles/virtual-machines/linux/incremental-snapshots.md) para não gerido premium discos da mesma forma utilizar instantâneos de armazenamento standard. O armazenamento Premium suporta armazenamento apenas localmente redundante, como a opção de replicação. Recomendamos que crie instantâneos e, em seguida, copie os instantâneos para uma conta de armazenamento standard com redundância geográfica. Para obter mais informações, consulte [opções de redundância de armazenamento do Azure](../articles/storage/common/storage-redundancy.md).

Se um disco está ligado a uma VM, algumas operações de API no disco não são permitidas. Por exemplo, não é possível efetuar uma [Blob de cópia](/rest/api/storageservices/Copy-Blob) operação desse blob se o disco está ligado a uma VM. Em vez disso, primeiro, crie um instantâneo desse blob usando o [Blob de instantâneo](/rest/api/storageservices/Snapshot-Blob) REST API. Em seguida, efetue o [Blob de cópia](/rest/api/storageservices/Copy-Blob) do instantâneo para copiar o disco anexado. Em alternativa, pode desligar o disco e, em seguida, executar quaisquer operações necessárias.

Os limites seguintes aplicam-se para instantâneos de blob de armazenamento premium:

| Limite de armazenamento Premium | Value |
| --- | --- |
| Número máximo de instantâneos por blob | 100 |
| Capacidade de conta de armazenamento de instantâneos<br>(Inclui dados em apenas com instantâneos. Não inclua dados no base blob.) | 10 TB |
| Tempo mínimo entre os instantâneos consecutivos | 10 minutos |

Para manter cópias georredundantes de sua instantâneos, pode copiar instantâneos de uma conta de armazenamento premium para uma conta de armazenamento standard com redundância geográfica com o AzCopy ou Blob de cópia. Para obter mais informações, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](../articles/storage/common/storage-use-azcopy.md) e [Blob de cópia](/rest/api/storageservices/Copy-Blob).

Para obter informações detalhadas sobre como efetuar operações de REST em relação a blobs de páginas numa conta de armazenamento premium, consulte [operações de serviço com o armazenamento do Azure Premium de BLOBs](https://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Managed disks

Um instantâneo para um disco gerido é uma cópia só de leitura do disco gerido. O instantâneo é armazenado como um disco gerido standard. Atualmente, [instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md) não são suportadas para discos geridos. Para saber como tirar um instantâneo para um disco gerido, veja [criar uma cópia de um VHD armazenado como do Azure gerido disco com instantâneos geridos no Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) ou [criar uma cópia de um VHD armazenado como do Azure gerido disco utilizando geridos instantâneos no Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md).

Se um disco gerido está ligado a uma VM, algumas operações de API no disco não são permitidas. Por exemplo, não é possível gerar uma assinatura de acesso partilhado (SAS) para efetuar uma operação de cópia, enquanto o disco estiver ligado a uma VM. Em vez disso, crie primeiro um instantâneo do disco e, em seguida, efetuar a cópia do instantâneo. Em alternativa, pode desligar o disco e, em seguida, gerar uma SAS para efetuar a operação de cópia.


## <a name="premium-storage-for-linux-vms"></a>Armazenamento Premium para VMs do Linux
Pode utilizar as seguintes informações para ajudar a configurar as suas VMs do Linux no armazenamento Premium:

Para atingir destinos de escalabilidade no armazenamento Premium, em todos os discos de armazenamento premium com a cache, defina como **só de leitura** ou **nenhum**, tem de desativar "barreiras" ao montar o sistema de ficheiros. Não precisa barreiras neste cenário, uma vez que as gravações para discos de armazenamento premium são duráveis para estas definições de cache. Quando a solicitação de gravação for concluída com êxito, os dados tenham sido gravados no arquivo persistente. Para desativar "barreiras", utilize um dos seguintes métodos. Escolha o para o sistema de ficheiros:
  
* Para **reiserFS**para desativar as barreiras, utilize o `barrier=none` montar opção. (Para ativar as barreiras, utilize `barrier=flush`.)
* Para **ext3/ext4**para desativar as barreiras, utilize o `barrier=0` montar opção. (Para ativar as barreiras, utilize `barrier=1`.)
* Para **XFS**para desativar as barreiras, utilize o `nobarrier` montar opção. (Para ativar as barreiras, utilize `barrier`.)
* Para o armazenamento premium discos com cache definido como **ReadWrite**, ative as barreiras para durabilidade de escrita.
* Para procurar etiquetas de volume persistir depois de reiniciar a VM, tem de atualizar /etc/fstab. com as referências de identificador exclusivo universalmente (UUID) para os discos. Para obter mais informações, consulte [adicionar um disco gerido a uma VM do Linux](../articles/virtual-machines/linux/add-disk.md).

As distribuições de Linux seguintes foram validadas para armazenamento Premium do Azure. Para obter um melhor desempenho e estabilidade com o armazenamento Premium, recomendamos que Atualize as suas VMs para um destas versões, no mínimo (ou para uma versão posterior). Algumas das versões requerem o mais recente Linux Integration Services (LIS), v4.0, para o Azure. Para transferir e instalar uma distribuição, siga o link listado na tabela seguinte. Podemos adicionar imagens à lista, à medida que concluir a validação. Tenha em atenção que o nosso validações mostram que o desempenho varia para cada imagem. Desempenho depende de características de carga de trabalho e as definições de imagem. Diferentes imagens otimizadas para diferentes tipos de cargas de trabalho.

| Distribuição | Versão | Kernel suportada | Detalhes |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 necessário](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Consulte a nota na secção seguinte* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 recomendado](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Consulte a nota na secção seguinte* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 ou RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 ou RHCK c /[LIS 4.1](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 ou RHCK c /[LIS 4.1](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Controladores LIS para OpenLogic CentOS

Se estiver a executar VMs do OpenLogic CentOS, execute o seguinte comando para instalar os controladores mais recentes:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Para ativar os novos controladores, reinicie a VM.

## <a name="pricing-and-billing"></a>Preços e faturação

Quando utilizar o armazenamento Premium, aplicam-se as seguintes considerações de faturas:

* **Tamanho de disco e BLOBs de armazenamento do Premium**

    A faturação de um blob ou um disco de armazenamento premium depende do tamanho aprovisionado do disco ou do blob. Azure mapeia o tamanho aprovisionado (arredondado) para a opção de disco de armazenamento premium mais próxima. Para obter detalhes, consulte a tabela [metas de escalabilidade e desempenho](#scalability-and-performance-targets). Cada disco mapeia para um tamanho de disco de aprovisionamento suportados e é cobrado em conformidade. Para qualquer disco aprovisionado a faturação é contabilizada à hora, utilizando o preço mensal para a oferta de armazenamento Premium. Por exemplo, se aprovisionar um disco P10 e eliminado depois de 20 horas, é-lhe cobrada para a oferta de P10 Rateado para 20 horas. Isto é, independentemente da quantidade de dados reais, escritos no disco ou o IOPS e débito utilizado.

* **Instantâneos de discos não geridos Premium**

    Instantâneos de discos premium não gerido são cobrados a capacidade adicional utilizada pelos instantâneos. Para obter mais informações sobre instantâneos, consulte [criar um instantâneo de um blob](/rest/api/storageservices/Snapshot-Blob).

* **Instantâneos de discos geridos Premium**

    Um instantâneo de um disco gerido é uma cópia só de leitura do disco. O disco é armazenado como um disco gerido standard. Um instantâneo de custos o mesmo como um padrão de disco gerido. Por exemplo, se tirar um instantâneo de um disco gerido premium de 128 GB, o custo do instantâneo é equivalente a um disco gerido standard da 128 GB.  

* **Transferências de dados de saída**

    [Transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) faturação para utilização de largura de banda de incorrer em (dados que saem de datacenters do Azure).

Para obter informações detalhadas sobre os preços para o armazenamento Premium, VMs de armazenamento Premium suportado e os discos geridos, veja estes artigos:

* [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Preços das máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Preços dos Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Suporte de cópia de segurança do Azure

Para a recuperação de desastre regional, é necessário fazer backup os discos da VM numa região diferente, utilizando [cópia de segurança do Azure](../articles/backup/backup-introduction-to-azure-backup.md) e uma conta de armazenamento GRS como um cofre de cópia de segurança.

Para criar uma tarefa de cópia de segurança com cópias de segurança baseados no tempo, fácil restauro de VM e políticas de retenção de cópia de segurança, utilizam cópias de segurança do Azure. Pode utilizar o Backup, ambas com discos geridos e não. Para obter mais informações, consulte [cópia de segurança do Azure para VMs com discos não geridos](../articles/backup/backup-azure-vms-first-look-arm.md) e [cópia de segurança do Azure para VMs com discos geridos](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o armazenamento Premium, veja os artigos seguintes.
