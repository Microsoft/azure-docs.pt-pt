---
title: Tipos de armazenamento Azure para carga de trabalho SAP
description: Planeamento de tipos de armazenamento Azure para cargas de trabalho SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/23/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 819ac1f01cc182c79571de35ec0753f694dc7722
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653618"
---
# <a name="azure-storage-types-for-sap-workload"></a>Tipos de Armazenamento do Azure para a carga de trabalho SAP
O Azure tem inúmeros tipos de armazenamento que diferem muito em capacidades, produção, latência e preços. Alguns dos tipos de armazenamento não são, ou de usutilizável limitado para cenários SAP. Enquanto que vários tipos de armazenamento Azure são bem adequados ou otimizados para cenários específicos de carga de trabalho SAP. Especialmente para o SAP HANA, alguns tipos de armazenamento Azure foram certificados para o uso com SAP HANA. Neste documento, estamos a analisar os diferentes tipos de armazenamento e a descrever a sua capacidade e usabilidade com cargas de trabalho SAP e componentes SAP.

Comentário sobre as unidades utilizadas ao longo deste artigo. Os vendedores públicos de nuvem mudaram-se para usar GiB[(Gibibyte)](https://en.wikipedia.org/wiki/Gibibyte)ou TiB[(Tebibyte](https://en.wikipedia.org/wiki/Tebibyte) como unidades de tamanho, em vez de Gigabyte ou Terabyte. Portanto, toda a documentação e prizing da Azure estão usando essas unidades.  Ao longo do documento, estamos a fazer referência a estas unidades de tamanho de unidades MiB, GiB e TiB exclusivamente. Talvez precises de planear com MB, GB e Tuberculose. Portanto, esteja ciente de algumas pequenas diferenças nos cálculos se precisar de tamanho para uma produção de 400 MiB/seg, em vez de um 250 MiB/secoff.

## <a name="microsoft-azure-storage-resiliency"></a>Resiliência do Armazenamento Microsoft Azure

O armazenamento do Microsoft Azure de HDD Standard, Standard SSD, Azure premium storage e ultra disco mantém o VHD base (com OS) e VM discos de dados anexados ou VHDs em três cópias em três diferentes nós de armazenamento. Falhando em outra réplica e sementeira de uma nova réplica em caso de falha do nó de armazenamento é transparente. Como resultado desta redundância, **não** é necessário utilizar qualquer tipo de camada de redundância de armazenamento em vários discos Azure. Este facto chama-se Armazenamento Redundante Local (LRS). O LRS é padrão para este tipo de armazenamento em Azure. [O Azure NetApp Files](https://azure.microsoft.com/services/netapp/) fornece redundância suficiente para obter os mesmos SLAs que outros armazenamentos nativos da Azure.

Existem vários métodos de redundância mais, que são todos descritos no artigo [Azure Storage replicação](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) que se aplica a alguns dos diferentes tipos de armazenamento que a Azure tem para oferecer. 

### <a name="azure-managed-disks"></a>Discos geridos Azure

Os discos geridos são um tipo de recurso no Azure Resource Manager que pode ser usado em vez de VHDs que são armazenados em Contas de Armazenamento Azure. Os Discos Geridos alinham-se automaticamente com o [conjunto de disponibilidade][disponibilidade de gestão de máquinas virtuais] da máquina virtual a que estão ligados e, portanto, aumentam a disponibilidade da sua máquina virtual e os serviços que estão a funcionar na máquina virtual. Para mais informações, leia o [artigo Geral.](../../managed-disks-overview.md)

Relacionado com a resiliência, este exemplo demonstra a vantagem dos discos geridos:

- Está a implementar os seus dois VMS DBMS para o seu sistema SAP num conjunto de disponibilidades Azure 
- À medida que o Azure implanta os VMs, o disco com a imagem de SO será colocado num cluster de armazenamento diferente. Isto evita que ambos os VMs são impactados por uma questão de um único cluster de armazenamento Azure
- À medida que cria novos discos geridos que atribui a estes VMs para armazenar os dados e registar ficheiros da sua base de dados, estes novos discos para os dois VMs também são implantados em clusters de armazenamento separados, de modo que nenhum dos discos dos primeiros VM está a partilhar clusters de armazenamento com os discos do segundo VM

Implantando sem discos geridos em contas de armazenamento definidas pelo cliente, a atribuição de discos é arbitrária e não tem consciência de que os VMs são implantados dentro de um AvSet para fins de resiliência.

> [!NOTE]
> Desta razão e de várias outras melhorias que estão exclusivamente disponíveis através de discos geridos, exigimos que novas implementações de VMs que utilizem o armazenamento do bloco Azure para os seus discos (todo o armazenamento Azure, exceto Azure NetApp Files) precisem de utilizar discos geridos Azure para os discos VHD/OS base, discos de dados que contenham ficheiros de base de dados SAP. Independentemente de implementar os VMs através de disponibilidade definida, através de Zonas de Disponibilidade ou independentes dos conjuntos e zonas. Os discos que são utilizados para armazenar cópias de segurança não são necessariamente necessários para serem geridos discos.

> [!NOTE]
> Os discos geridos Azure fornecem apenas redundância local (LRS). 


## <a name="storage-scenarios-with-sap-workloads"></a>Cenários de armazenamento com cargas de trabalho SAP
O armazenamento persistido é necessário na carga de trabalho SAP em vários componentes da pilha que implementa no Azure. Estes cenários listam no mínimo como:

- Persistente o VHD base do seu VM que detém o sistema operativo e outro software que instala nesse disco. Este disco/VHD é a raiz do seu VM. Quaisquer alterações que lhe sejam feitas, têm de ser persistiu. Então, da próxima vez, paras e reinicias o VM, todas as alterações feitas antes ainda existem. Especialmente nos casos em que o VM está a ser implantado pelo Azure para outro hospedeiro do que estava a funcionar originalmente.
- Discos de dados persistidos. Estes discos são VHDs que anexa para armazenar dados da aplicação. Estes dados da aplicação podem ser ficheiros de dados e registo/redo de uma base de dados, ficheiros de backup ou instalações de software. Significa qualquer disco além da sua base VHD que detenha o sistema operativo
- Partilhas de ficheiros ou discos partilhados que contenham o seu diretório de transporte global para NetWeaver ou S/4HANA. O conteúdo dessas ações é consumido por software em funcionamento em vários VMs ou é usado para construir cenários de cluster de alta disponibilidade
- O diretório /sapmnt ou ações de ficheiros comuns para processos EDI ou similares. O conteúdo dessas ações é consumido por software em funcionamento em vários VMs ou é usado para construir cenários de cluster de alta disponibilidade

Nas próximas secções, os diferentes tipos de armazenamento Azure e a sua usabilidade para a carga de trabalho SAP são discutidos que se aplicam aos quatro cenários acima referidos. Uma categorização geral de como os diferentes tipos de armazenamento Azure devem ser utilizados está documentada no artigo [Que tipos de disco estão disponíveis no Azure?](../../disks-types.md) As recomendações para a utilização dos diferentes tipos de armazenamento Azure para a carga de trabalho SAP não serão muito diferentes.

Para restrições de suporte aos tipos de armazenamento Azure para a camada de armazenamento SAP NetWeaver/aplicação de S/4HANA, leia a nota de [suporte SAP 2015553](https://launchpad.support.sap.com/#/notes/2015553) Para os tipos de armazenamento Azure certificados e suportados pela SAP HANA leia o artigo [Configurações de armazenamento de máquinas virtuais SAP HANA Azure](./hana-vm-operations-storage.md).

As secções que descrevem os diferentes tipos de armazenamento Azure dar-lhe-ão mais informações sobre as restrições e possibilidades utilizando o armazenamento suportado pela SAP. 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>Recomendações de armazenamento para cenários de armazenamento SAP
Antes de entrarmos em detalhes, apresentamos o resumo e as recomendações já no início do documento. Considerando que os pormenores relativos aos tipos específicos de armazenagem Azure seguem esta secção do documento. Resumindo as recomendações de armazenamento para os cenários de armazenamento SAP numa tabela, parece:

| Cenário de utilização | HDD Standard | SSD Standard | Armazenamento Premium | Disco Ultra | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Disco do SO | não é adequado |  adequado restrito (não-prod) | recomendado | não é possível | não é possível |
| Diretório Global de Transportes | não apoiado | não apoiado | recomendado | recomendado | recomendado |
| /sapmnt | não é adequado | adequado restrito (não-prod) | recomendado | recomendado | recomendado |
| DBMS Volume de dados FAMÍLIAS SAP HANA M/Mv2 VM | não apoiado | não apoiado | recomendado | recomendado | recomendado<sup>2</sup> |
| Volume de log DBMS FAMÍLIAS SAP HANA M/Mv2 VM | não apoiado | não apoiado | recomendado<sup>1</sup> | recomendado | recomendado<sup>2</sup> | 
| DBMS Volume de dados FAMÍLIAS SAP HANA Esv3/Edsv4 VM | não apoiado | não apoiado | recomendado | recomendado | recomendado<sup>2</sup> |
| DBMS volume de log FAMÍLIAS SAP HANA Esv3/Edsv4 VM | não apoiado | não apoiado | não apoiado | recomendado | recomendado<sup>2</sup> | 
| DBMS Volume de dados não-HANA | não apoiado | adequado restrito (não-prod) | recomendado | recomendado | não apoiado |
| DBMS volume de registo não-HANA M/Mv2 VM famílias | não apoiado | adequado restrito (não-prod) | recomendado<sup>1</sup> | recomendado | não apoiado |
| DBMS volume de registo não-HANA não-M/Mv2 VM famílias | não apoiado | adequado restrito (não-prod) | adequado para a carga de trabalho até média | recomendado | não apoiado |


<sup>1</sup> Com a utilização do Acelerador de [Escrita Azure](../../how-to-enable-write-accelerator.md) para famílias M/Mv2 VM para volumes de log/redo log <sup>2</sup> A utilização da ANF requer /hana/dados, bem como /hana/log para estar na ANF 

Características que pode esperar da lista de diferentes tipos de armazenamento como:

| Cenário de utilização | HDD Standard | SSD Standard | Armazenamento Premium | Disco Ultra | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Produção/ IOPS SLA | não | não | sim | sim | sim |
| Leituras de Latência | alta | médio a alto | baixo | sub-milissegundo | sub-milissegundo |
| Escritos de Latência | alta | médio a alto  | baixo (sub-milissegundo<sup>1</sup>) | sub-milissegundo | sub-milissegundo |
| HANA apoiado | não | não | sim<sup>1</sup> | sim | sim |
| Instantâneos de disco possíveis | sim | sim | sim | não | sim |
| Atribuição de discos em diferentes clusters de armazenamento ao utilizar conjuntos de disponibilidade | através de discos geridos | através de discos geridos | através de discos geridos | tipo de disco não suportado com VMs implantados através de conjuntos de disponibilidade | não<sup>3</sup> |
| Alinhado com zonas de disponibilidade | sim | sim | sim | sim | precisa de envolvimento da Microsoft |
| Redundância zonal | não para discos geridos | não para discos geridos | não para discos geridos | não | não |
| Geo redundância | não para discos geridos | não para discos geridos | não | não | não |


<sup>1</sup> Com a utilização do Acelerador de [Escrita Azure](../../how-to-enable-write-accelerator.md) para famílias M/Mv2 VM para volumes de log/redo

<sup>2</sup> Os custos dependem do IOPS e da produção a provisionados

<sup>3</sup> A criação de diferentes agrupamentos de capacidade ANF não garante a implantação de piscinas de capacidade em diferentes unidades de armazenamento


> [!IMPORTANT]
> Para obter menos de 1 milissegundo de latência I/O utilizando ficheiros Azure NetApp (ANF), é necessário trabalhar com a Microsoft para organizar a colocação correta entre os seus VMs e as ações NFS com base na ANF. Até ao momento não existe nenhum mecanismo que proporcione uma proximidade automática entre um VM implantado e os volumes NFS alojados na ANF. Dada a configuração diferente das diferentes regiões do Azure, a latência adicionada da rede poderia empurrar a latência de E/O para além de 1 milissegundo se a quota de VM e NFS não for atribuída nas proximidades.


> [!IMPORTANT]
> Nenhum dos discos geridos de armazenamento baseados em blocos Azure atualmente oferecidos, ou Azure NetApp Files oferecem qualquer redundância zonal ou geográfica. Como resultado, você precisa ter certeza de que as suas arquiteturas de alta disponibilidade e recuperação de desastres não estão contando com qualquer tipo de replicação de armazenamento nativo Azure para estes discos geridos, NFS ou SMB shares.


## <a name="azure-premium-storage"></a>Armazenamento premium Azure
O armazenamento SSD premium Azure foi introduzido com o objetivo de fornecer:

* Baixa latência de E/O
* SLAs para IOPS e produção
* Menor variabilidade na latência de E/O

Este tipo de armazenamento destina-se a cargas de trabalho DBMS, tráfego de armazenamento que requer baixa latência de milissegundos de um dígito, e SLAs em IOPS e base de custo de produção no caso do armazenamento premium Azure não é o volume de dados real armazenado nesses discos, mas a categoria de tamanho de tal disco, independente da quantidade dos dados que são armazenados dentro do disco. Também pode criar discos em armazenamento premium que não estão a mapear diretamente as categorias de tamanho mostradas no artigo [Premium SSD](../../disks-types.md#premium-ssd). As conclusões deste artigo são:

- O armazenamento está organizado em gamas. Por exemplo, um disco na gama 513 GiB a 1024 Capacidade giB compartilham as mesmas capacidades e os mesmos custos mensais
- O IOPS per GiB não está a seguir lineares nas categorias de tamanho. Discos menores abaixo de 32 GiB têm taxas de IOPS mais altas por GiB. Para discos além de 32 GiB a 1024 GiB, a taxa de IOPS por GiB é entre 4-5 IOPS por GiB. Para discos maiores até 32.767 GiB, a taxa de IOPS por GiB está abaixo de 1
- A produção de I/O para este armazenamento não é linear com o tamanho da categoria disco. Para discos mais pequenos, como a categoria entre 65 GiB e 128 GiB, a potência ronda os 780KB/GiB. Enquanto que para os discos grandes extremos como um disco GiB de 32.767, a produção é de cerca de 28KB/GiB
- O IOPS e o SLAs de produção não podem ser alterados sem alterar a capacidade do disco

A Azure tem um único exemplo VM SLA de 99,9% que está ligado ao uso de armazenamento premium Azure ou armazenamento de disco Azure Ultra. O SLA está documentado em [SLA para máquinas virtuais.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Para cumprir com este único VM SLA, o disco VHD base, bem como **todo** o disco anexo, tem de ser armazenamento premium Azure ou armazenamento de disco Azure Ultra.

A matriz de capacidade para a carga de trabalho SAP parece:

| Funcionalidade| Comentário| Notas/Links | 
| --- | --- | --- | 
| OS base VHD | adequado | todos os sistemas |
| Disco de dados | adequado | todos os sistemas - [especialmente para SAP HANA](../../how-to-enable-write-accelerator.md) |
| Diretório global de transportes SAP | SIM | [Suportado](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAPMNT | adequado | todos os sistemas |
| Armazenamento de backup | adequado | para armazenamento a curto prazo de backups |
| Partilhas/disco partilhado | não disponível | Precisa de Ficheiros Azure Premium ou terceiros |
| Resiliência | LRS | Não há GRS ou ZRS disponíveis para discos |
| Latência | baixo a médio | - |
| IOPS SLA | SIM | - |
| IOPS linear à capacidade | semi linear em parênteses  | [Preços geridos do disco](https://azure.microsoft.com/pricing/details/managed-disks/) |
| IOPS máximo por disco | 20.000 [dependentes do tamanho do disco](https://azure.microsoft.com/pricing/details/managed-disks/) | Considere também [os limites de VM](../../sizes.md) |
| Produção SLA | SIM | - |
| Produção linear à capacidade | semi linear em parênteses | [Preços geridos do disco](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA certificada | SIM | [especialmente para SAP HANA](../../how-to-enable-write-accelerator.md) |
| Instantâneos de disco possíveis | SIM | - |
| Instantâneos VM de backup Azure possíveis | SIM | exceto para discos em cache [do acelerador de escrever](../../how-to-enable-write-accelerator.md)  |
| Custos | Média | - |

O armazenamento premium Azure não preenche os KPI'kPI's de latência de armazenamento SAP HANA com os tipos comuns de caching oferecidos com armazenamento premium Azure. Para cumprir os KPI's de latência de armazenamento para as gravações SAP HANA, é necessário utilizar o caching do acelerador de escrita Azure, conforme descrito no artigo [Enable Write Accelerator](../../how-to-enable-write-accelerator.md). O Azure Write Accelerator beneficia todos os outros sistemas DBMS para as suas gravações de transações e redações de registos de redo. Por isso, recomenda-se a sua utilização em todas as implementações do DBMS SAP. Para o SAP HANA, a utilização do Acelerador Azure Write em conjunto com o armazenamento premium Azure é obrigatória.



**Resumo:** O armazenamento azure premium é um dos tipos de armazenamento Azure recomendados para a carga de trabalho SAP. Esta recomendação aplica-se à não produção, bem como aos sistemas de produção. O armazenamento premium Azure é adequado para lidar com as cargas de trabalho da base de dados. O uso do Acelerador de Escrita Azure vai melhorar a latência da escrita contra os discos premium Azure substancialmente. No entanto, para sistemas DBMS com altas taxas de IOPS e taxas de produção, é necessário utilizar capacidade de armazenamento excessiva ou precisa de utilizar funcionalidades como Windows Storage Spaces ou gestores de volume lógicos no Linux para construir conjuntos de riscas que lhe dêem a capacidade desejada de um lado, mas também o IOPS necessário ou o rendimento na melhor eficiência de custos.


### <a name="azure-burst-functionality-for-premium-storage"></a>Funcionalidade de explosão Azure para armazenamento premium
Para discos de armazenamento premium Azure menores ou iguais a 512 GiB na capacidade, é oferecida a funcionalidade de explosão. A forma exata como funciona a explosão do disco é descrita no artigo [Disco rebentando](../../linux/disk-bursting.md). Ao ler o artigo, compreende o conceito de acumulação de IOPS e produção nos tempos em que a sua carga de trabalho de I/S está abaixo do IOPS nominal e da produção dos discos (para mais detalhes sobre a produção nominal ver [preços do Disco Gerido).](https://azure.microsoft.com/pricing/details/managed-disks/) Você vai acumular o delta do IOPS e a produção entre o seu uso atual e os valores nominais do disco. As rajadas estão limitadas a um máximo de 30 minutos.

Os casos ideais em que esta funcionalidade de explosão pode ser planeada serão provavelmente os volumes ou discos que contêm ficheiros de dados para os diferentes DBMS. Espera-se que a carga de trabalho de E/S contra esses volumes, especialmente com sistemas de pequena a média gama, se pareça com:

- Carga de trabalho de leitura baixa a moderada, uma vez que os dados idealmente estão em cache na memória, ou como no caso da HANA deve estar completamente na memória
- Explosões de escrita desencadeadas por pontos de verificação de bases de dados ou pontos de poupança que são emitidos regularmente
- Carga de trabalho de backup que lê num fluxo contínuo em casos em que cópias de segurança não são executadas através de instantâneos de armazenamento
- Para SAP HANA, carregue os dados na memória após um reinício de um caso

Especialmente em sistemas DBMS mais pequenos, onde a sua carga de trabalho está a lidar com algumas centenas de transações por segundo apenas, tal funcionalidade de explosão também pode fazer sentido para os discos ou volumes que armazenam a transação ou redo log. A carga de trabalho esperada contra tal disco ou volumes parece:

- Escreve regularmente para o disco que depende da carga de trabalho e da natureza da carga de trabalho, uma vez que cada compromisso emitido pela aplicação é suscetível de desencadear uma operação de E/S
- Maior carga de trabalho em produção para casos de tarefas operacionais, como a criação ou reconstrução de índices
- Leia as rajadas ao realizar registos de transações ou refazer backups de registos


## <a name="azure-ultra-disk"></a>Disco Azure Ultra
Os discos Ultra do Azure têm um débito elevado, IOPS elevado e armazenamento no disco com latência baixa consistente para VMs IaaS do Azure. Alguns benefícios adicionais dos discos ultra incluem a capacidade de alterar dinamicamente o IOPS e a produção do disco, juntamente com as suas cargas de trabalho, sem a necessidade de reiniciar as suas máquinas virtuais (VM). Os discos ultra são adequados para cargas de trabalho intensivas de dados, tais como carga de trabalho SAP DBMS. Os discos ultra só podem ser usados como discos de dados e não podem ser usados como disco VHD base que armazena o sistema operativo. Recomendamos a utilização do armazenamento premium Azure como disco VHD baseado.

Ao criar um disco ultra, tem três dimensões que pode definir:

- A capacidade do disco. Os intervalos são de 4 GiB a 65.536 GiB
- IOPS provisível para o disco. Valores máximos diferentes aplicam-se à capacidade do disco. Leia o artigo [Disco ultra para](../../disks-types.md#ultra-disk) mais detalhes
- Largura de banda de armazenamento a provisionada. A largura de banda máxima diferente aplica-se dependendo da capacidade do disco. Leia o artigo [Disco ultra para](../../disks-types.md#ultra-disk) mais detalhes

O custo de um único disco é determinado pelas três dimensões que pode definir para os discos específicos separadamente. 


A matriz de capacidade para a carga de trabalho SAP parece:

| Funcionalidade| Comentário| Notas/Links | 
| --- | --- | --- | 
| OS base VHD | não funciona | - |
| Disco de dados | adequado | todos os sistemas  |
| Diretório global de transportes SAP | SIM | [Suportado](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAPMNT | adequado | todos os sistemas |
| Armazenamento de backup | adequado | para armazenamento a curto prazo de backups |
| Partilhas/disco partilhado | não disponível | Precisa de terceiros |
| Resiliência | LRS | Não há GRS ou ZRS disponíveis para discos |
| Latência | muito baixo | - |
| IOPS SLA | SIM | - |
| IOPS linear à capacidade | semi linear em parênteses  | [Preços geridos do disco](https://azure.microsoft.com/pricing/details/managed-disks/) |
| IOPS máximo por disco | 1.200 a 160.000 | dependente da capacidade do disco |
| Produção SLA | SIM | - |
| Produção linear à capacidade | semi linear em parênteses | [Preços geridos do disco](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA certificada | SIM | - |
| Instantâneos de disco possíveis | NO | - |
| Instantâneos VM de backup Azure possíveis | NO | - |
| Custos | Maior que o armazenamento Premium | - |



**Resumo:** Os discos Azure ultra são um armazenamento adequado com baixa latência para todos os tipos de carga de trabalho SAP. Até agora, o disco ultra só pode ser utilizado em combinações com VMs que tenham sido implantados através de Zonas de Disponibilidade (implantação zonal). O disco ultra não está a suportar instantâneos de armazenamento neste momento. Em oposto a todos os outros armazenamentos, o disco Ultra não pode ser utilizado para o disco VHD base. O disco ultra é ideal para casos em que a carga de trabalho de I/S oscila muito e pretende adaptar a produção de armazenamento implantado ou iOPS aos padrões de carga de trabalho de armazenamento em vez de dimensionar para o máximo uso de largura de banda e IOPS.


## <a name="azure-netapp-files-anf"></a>Ficheiros Azure NetApp (ANF)
[O Azure NetApp Files](https://azure.microsoft.com/services/netapp/) é o resultado de uma cooperação entre a Microsoft e a NetApp com o objetivo de fornecer ações nativas de Azure NFS e SMB de alto desempenho. A ênfase é fornecer alta largura de banda e armazenamento de baixa latência que permite cenários de implementação DBMS, e com o tempo permitir a funcionalidade operacional típica do armazenamento netApp através do Azure também. As ações NFS/SMB são oferecidas em três níveis de serviço diferentes que diferenciam a produção de armazenamento e o preço. Os níveis de serviço estão documentados nos níveis de serviço do artigo [Para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Para os diferentes tipos de carga de trabalho SAP, os seguintes níveis de serviço são altamente recomendados:

- Carga de trabalho SAP DBMS: Desempenho, idealmente Ultra
- PARTILHA SAPMNT: Performance, idealmente Ultra
- Diretório de transportes globais: Performance, idealmente Ultra

> [!NOTE]
> O tamanho mínimo de provisionamento é uma unidade de 4 TiB que é chamada de pool de capacidade. Em seguida, cria-se volumes a partir desta capacidade de piscina. Enquanto que o menor volume que se pode construir é de 100 GiB. Você pode expandir uma piscina de capacidade em passos TiB. Para obter preços, consulte o artigo [Azure NetApp Files Pricing](https://azure.microsoft.com/pricing/details/netapp/)

O armazenamento da ANF é atualmente suportado para vários cenários de carga de trabalho SAP:

- Disponibilização de ações da SMB ou NFS para o diretório global de transportes da SAP
- A partilha em cenários de alta disponibilidade, conforme documentado em:
    - [Alta disponibilidade para SAP NetWeaver em VMs Azure no Windows com Ficheiros Azure NetApp (SMB) para aplicações SAP](./high-availability-guide-windows-netapp-files-smb.md)
    - [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server com Ficheiros Azure NetApp para aplicações SAP](./high-availability-guide-suse-netapp-files.md)
    - [Azure Virtual Machines alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux com Ficheiros Azure NetApp para aplicações SAP](./high-availability-guide-rhel-netapp-files.md)
- Implementações SAP HANA utilizando ações NFS v4.1 para /hana/data e /hana/log volumes e/ou NFS v4.1 ou volumes NFS v3 para volumes /hana/shared como documentado no artigo [CONFIGURAÇÕES de armazenamento de máquinas virtuais SAP HANA Azure](./hana-vm-operations-storage.md)

> [!NOTE]
> Nenhuma outra carga de trabalho DBMS é suportada para ações NFS ou SMB baseadas em Ficheiros Azure NetApp. As atualizações e alterações serão fornecidas se isto for alterado.

Como já acontece com o armazenamento premium Azure, um tamanho de produção fixo ou linear por GB pode ser um problema quando você é obrigado a aderir a alguns números mínimos de produção. Como se fosse o caso da SAP HANA. Com a ANF, este problema pode tornar-se mais pronunciado do que com o disco premium Azure. No caso do disco premium Azure, você pode pegar em vários discos menores com uma produção relativamente alta por GiB e listras através deles para ser eficiente em custos e ter maior produção em menor capacidade. Este tipo de striping não funciona para ações NFS ou SMB acolhidas na ANF. Esta restrição resultou na implantação de sobrecapacidade como:

- Para conseguir, por exemplo, uma produção de 250 MiB/seg num volume NFS alojado na ANF, é necessário implantar 1,95 TiB de capacidade do nível de serviço Ultra. 
- Para atingir 400 MiB/seg, precisaria de implantar a capacidade de 3.125 TiB. Mas pode precisar do excesso de capacidade para atingir o rendimento que necessita do volume. Este excesso de a provisionamento da capacidade afeta a fixação de preços de casos menores de HANA. 
- No espaço de utilização de NFS em cima da ANF para o diretório SAP/sapmnt, normalmente vai longe com a capacidade mínima de 100 GiB a 150 GiB que é aplicada pela Azure NetApp Files. No entanto, a experiência do cliente mostrou que a produção relacionada de 12,8 MiB/seg (utilizando o nível de serviço Ultra) pode não ser suficiente e pode ter um impacto negativo na estabilidade do sistema SAP. Nestes casos, os clientes poderiam evitar problemas aumentando o volume do volume /sapmnt, de modo que mais produção é fornecida a esse volume.

A matriz de capacidade para a carga de trabalho SAP parece:

| Funcionalidade| Comentário| Notas/Links | 
| --- | --- | --- | 
| OS base VHD | não funciona | - |
| Disco de dados | adequado | SAP HANA apenas  |
| Diretório global de transportes SAP | SIM | SMB, bem como NFS |
| Sapmnt SAPMNT | adequado | todos os sistemas SMB (apenas Windows) ou NFS (apenas Linux) |
| Armazenamento de backup | adequado | - |
| Partilhas/disco partilhado | SIM | SMB 3.0, NFS v3 e NFS v4.1 |
| Resiliência | LRS | Não há GRS ou ZRS disponíveis para discos |
| Latência | muito baixo | - |
| IOPS SLA | SIM | - |
| IOPS linear à capacidade | estritamente linear  | Dependente do [Nível de Serviço](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| Produção SLA | SIM | - |
| Produção linear à capacidade | semi linear em parênteses | Dependente do [Nível de Serviço](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| HANA certificada | SIM | - |
| Instantâneos de disco possíveis | SIM | - |
| Instantâneos VM de backup Azure possíveis | NO | - |
| Custos | Maior que o armazenamento Premium | - |


Funcionalidade adicional incorporada do armazenamento da ANF:

- Capacidade de realizar instantâneos de volume
- Clonagem de volumes ANF a partir de instantâneos
- Restaurar volumes a partir de instantâneos (snap-revert)

**Resumo**: Azure NetApp Files é um armazenamento de baixa latência certificado pela HANA que permite implantar volumes ou ações de NFS e SMB. O armazenamento vem com três diferentes níveis de serviço que fornecem diferentes níveis de produção e IOPS de forma linear por gib capacidade do volume. O armazenamento ANF está a permitir implantar cenários de escala SAP HANA com um nó de espera. O armazenamento é adequado para fornecer ações de ficheiros conforme necessário para o diretório de transportes global /sapmnt ou SAP. O armazenamento DAF vem com a disponibilidade de funcionalidade que está disponível como funcionalidade nativa do NetApp.  



## <a name="azure-standard-ssd-storage"></a>Armazenamento SSD padrão Azure
Em comparação com o armazenamento HDD padrão Azure, o armazenamento SSD padrão Azure proporciona uma melhor disponibilidade, consistência, fiabilidade e latência. É otimizado para cargas de trabalho que precisam de um desempenho consistente em níveis de IOPS mais baixos. Este armazenamento é o armazenamento mínimo utilizado para sistemas SAP não produtivos que têm baixas iops e exigências de produção. A matriz de capacidade para a carga de trabalho SAP parece:

| Funcionalidade| Comentário| Notas/Links | 
| --- | --- | --- | 
| OS base VHD | restrito adequado | sistemas não produtivos |
| Disco de dados | restrito adequado | alguns sistemas de não produção com baixa iops e exigências de latência |
| Diretório global de transportes SAP | NO | [Não suportado](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAPMNT | restrito adequado | sistemas não produtivos |
| Armazenamento de backup | adequado | - |
| Partilhas/disco partilhado | não disponível | Precisa de terceiros |
| Resiliência | LRS, GRS | Não há ZRS disponível para discos |
| Latência | alta | demasiado alto para o diretório de transportes globais da SAP, ou sistemas de produção |
| IOPS SLA | NO | - |
| IOPS máximo por disco | 500 | Independentemente do tamanho do disco |
| Produção SLA | NO | - |
| HANA certificada | NO | - |
| Instantâneos de disco possíveis | SIM | - |
| Instantâneos VM de backup Azure possíveis | SIM | - |
| Custos | LOW | - |



**Resumo:** O armazenamento SSD padrão Azure é a recomendação mínima para VMs não produtivos para VHD de base, eventuais implantações de DBMS com insensibilidade relativa de latência e/ou baixas taxas de IOPS e taxas de produção. Este tipo de armazenamento Azure já não é suportado para hospedar o Diretório De Transportes Globais SAP. 



## <a name="azure-standard-hdd-storage"></a>Armazenamento HDD padrão Azure
O armazenamento Azure Standard HDD foi o único tipo de armazenamento quando a infraestrutura Azure foi certificada para a carga de trabalho SAP NetWeaver no ano de 2014. No ano de 2014, as máquinas virtuais Azure eram pequenas e baixas de armazenamento. Portanto, este tipo de armazenamento foi capaz de apenas acompanhar as exigências. O armazenamento é ideal para cargas de trabalho insensíveis à latência, que dificilmente experimenta no espaço SAP. Com o aumento da produção de VMs Azure e o aumento da carga de trabalho que estes VMs estão produzindo, este tipo de armazenamento já não é considerado para o uso com cenários SAP. A matriz de capacidade para a carga de trabalho SAP parece:

| Funcionalidade| Comentário| Notas/Links | 
| --- | --- | --- | 
| OS base VHD | não é adequado | - |
| Disco de dados | não é adequado | - |
| Diretório global de transportes SAP | NO | [Não suportado](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAPMNT | NO | Não suportado |
| Armazenamento de backup | adequado | - |
| Partilhas/disco partilhado | não disponível | Precisa de Ficheiros Azure ou terceiros |
| Resiliência | LRS, GRS | Não há ZRS disponível para discos |
| Latência | alta | demasiado alto para o uso de DBMS, diretório de transportes globais SAP, ou sapmnt/saploc |
| IOPS SLA | NO | - |
| IOPS máximo por disco | 500 | Independentemente do tamanho do disco |
| Produção SLA | NO | - |
| HANA certificada | NO | - |
| Instantâneos de disco possíveis | SIM | - |
| Instantâneos VM de backup Azure possíveis | SIM | - |
| Custos | LOW | - |



**Resumo:** O HDD standard é um tipo de armazenamento Azure que só deve ser utilizado para armazenar cópias de segurança SAP. Só deve ser utilizado como VHD base para sistemas bastante inativos, como os sistemas reformados utilizados para procurar dados aqui e ali. Mas nenhum desenvolvimento ativo, QA ou VMs de produção devem basear-se nesse armazenamento. Nem os ficheiros de base de dados devem ser hospedados nesse armazenamento


## <a name="azure-vm-limits-in-storage-traffic"></a>Limites de VM Azure no tráfego de armazenamento
Ao contrário dos cenários no local, o tipo VM individual que está a selecionar, desempenha um papel vital na largura de banda de armazenamento que pode alcançar. Para os diferentes tipos de armazenamento, deve considerar:

| Tipo de armazenamento| Linux | Windows | Comentários |
| --- | --- | --- | --- |
| HDD Standard | [Tamanhos para LMs Linux em Azure](../../sizes.md) | [Tamanhos para VMs windows em Azure](../../sizes.md) | Provavelmente difícil de tocar nos limites de armazenamento de VMs médios ou grandes |
| SSD Standard | [Tamanhos para LMs Linux em Azure](../../sizes.md) | [Tamanhos para VMs windows em Azure](../../sizes.md) | Provavelmente difícil de tocar nos limites de armazenamento de VMs médios ou grandes |
| Armazenamento Premium | [Tamanhos para LMs Linux em Azure](../../sizes.md) | [Tamanhos para VMs windows em Azure](../../sizes.md) | Fácil de atingir limites de IOPS ou de produção de armazenamento VM com configuração de armazenamento |
| Armazenamento ultra disco | [Tamanhos para LMs Linux em Azure](../../sizes.md) | [Tamanhos para VMs windows em Azure](../../sizes.md) | Fácil de atingir limites de IOPS ou de produção de armazenamento VM com configuração de armazenamento |
| Azure NetApp Files | [Tamanhos para LMs Linux em Azure](../../sizes.md) | [Tamanhos para VMs windows em Azure](../../sizes.md) | O tráfego de armazenamento está a utilizar largura de banda de produção de rede e não largura de banda de armazenamento! |

Como limitações, pode notar que:

- Quanto menor for o VM, menos discos se pode anexar. Isto não se aplica à ANF. Uma vez que monta ações NFS ou SMB, não encontra um limite de número de volumes partilhados a serem anexados
- Os VMs têm limites de produção de E/S e IOPS que facilmente poderiam ser ultrapassados com discos de armazenamento premium e discos ultra
- Com a ANF, o tráfego para os volumes partilhados está a consumir a largura de banda da rede VM e não a largura de banda de armazenamento
- Com grandes volumes NFS no espaço de capacidade tib de dois dígitos, a produção que acede a tal volume a partir de um único VM vai para o planalto com base nos limites do Linux para uma única sessão interagindo com o volume partilhado. 

À medida que o seu VMs Azure de tamanho superior no ciclo de vida de um sistema SAP, deve avaliar os limites de produção de IOPS e armazenamento do novo e maior tipo VM. Em alguns casos, também poderia fazer sentido ajustar a configuração de armazenamento às novas capacidades do Azure VM. 


## <a name="striping-or-not-striping"></a>Striping ou não striping
A criação de uma risca definida de vários discos Azure num volume maior permite-lhe acumular o IOPS e a produção dos discos individuais num só volume. É utilizado apenas para armazenamento padrão Azure e armazenamento premium Azure. O disco Azure Ultra onde pode configurar a produção e o IOPS independentemente da capacidade de um disco, não requer a utilização de conjuntos de listras. Volumes partilhados baseados em NFS ou SMB não podem ser listrados. Devido à natureza não linear da produção premium Azure e do IOPS, você pode providenciar uma capacidade menor com o mesmo IOPS e produção do que grandes discos de armazenamento premium Azure. Este é o método para obter uma produção mais elevada ou IOPS a um custo mais baixo usando o armazenamento premium Azure. Por exemplo:

- Despir-se em dois discos de armazenamento premium P15 leva-o a uma produção de 
- 250 MiB/seg. Tal volume vai ter 512 giB de capacidade. Se quiser ter um único disco que lhe dê 250 MiB de produção por segundo, terá de escolher um disco P40 com 2 capacidade tiB. 
- Ou pode obter uma produção de 400 MiB/seg, despojando quatro discos de armazenamento premium P10 com uma capacidade global de 512 GiB por strip. Se quiser ter um disco único com um mínimo de 500 MiB por segundo, terá de escolher um disco de armazenamento premium P60 com 8 TiB. Uma vez que o custo ou armazenamento premium é quase linear com a capacidade, você pode sentir a poupança de custos usando striping.

Algumas regras têm de ser seguidas no striping:

- Não deve ser utilizado nenhum armazenamento configurado in-VM, uma vez que o armazenamento do Azure mantém os dados redundantes já
- Os discos a que o conjunto de listras é aplicado, precisam de ser do mesmo tamanho

Despir-se em vários discos menores é a melhor maneira de alcançar uma boa relação preço/desempenho usando o armazenamento premium Azure. Entende-se que o striping tem algumas despesas adicionais de implantação e gestão.

Para recomendações específicas sobre o tamanho das listras, leia a documentação para as diferentes configurações de [armazenamento de máquinas virtuais SAP HANA Azure](./hana-vm-operations-storage.md).




## <a name="next-steps"></a>Passos seguintes
Leia os artigos:

- [Considerações para a implantação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](./dbms_guide_general.md)
- [Configurações de armazenamento da máquina virtual do Azure do SAP HANA](./hana-vm-operations-storage.md)
 