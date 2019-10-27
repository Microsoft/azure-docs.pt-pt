---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 979fa8bed99a8244e9ebae9de4b615bfac4bd773
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966469"
---
## <a name="benefits-of-managed-disks"></a>Benefícios dos Managed disks

Vamos examinar alguns dos benefícios obtidos com o uso de discos gerenciados.

### <a name="highly-durable-and-available"></a>Elevada disponibilidade e durabilidade

Os Managed disks são projetados para a disponibilidade de 99,999%. Os discos gerenciados obtêm isso fornecendo três réplicas de seus dados, permitindo alta durabilidade. Se uma ou até mesmo duas réplicas apresentarem problemas, as restantes ajudam a garantir a persistência dos dados e alta tolerância contra falhas. Essa arquitetura ajudou o Azure a fornecer consistentemente a durabilidade de nível empresarial para discos de IaaS (infraestrutura como serviço), com uma taxa de falha anual de ZERO% líder do setor.

### <a name="simple-and-scalable-vm-deployment"></a>Implantação de VM simples e escalonável

Usando discos gerenciados, você pode criar até 50.000 **discos** de VM de um tipo em uma assinatura por região, permitindo que você crie milhares de **VMs** em uma única assinatura. Esse recurso também aumenta ainda mais a escalabilidade dos [conjuntos de dimensionamento de máquinas virtuais](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) , permitindo que você crie até 1.000 VMs em um conjunto de dimensionamento de máquinas virtuais usando uma imagem do Marketplace.

### <a name="integration-with-availability-sets"></a>Integração com conjuntos de disponibilidade

Os Managed disks são integrados aos conjuntos de disponibilidade para garantir que os discos de [VMs em um conjunto de disponibilidade](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) estejam suficientemente isolados uns dos outros para evitar um único ponto de falha. Os discos são colocados automaticamente em diferentes unidades de escala de armazenamento (carimbos). Se um carimbo falhar devido a uma falha de hardware ou de software, somente as instâncias de VM com discos nesses carimbos falharão. Por exemplo, digamos que você tenha um aplicativo em execução em cinco VMs e as VMs estejam em um conjunto de disponibilidade. Os discos dessas VMs não serão armazenados no mesmo carimbo, portanto, se um carimbo falhar, as outras instâncias do aplicativo continuarão a ser executadas.

### <a name="integration-with-availability-zones"></a>Integração com o Zonas de Disponibilidade

O Managed disks oferece suporte a [zonas de disponibilidade](../articles/availability-zones/az-overview.md), que é uma oferta de alta disponibilidade que protege seus aplicativos contra falhas do datacenter. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. Com o Zonas de Disponibilidade, o Azure oferece um SLA de tempo de atividade de VM melhor do setor 99,99%.

### <a name="azure-backup-support"></a>Suporte do backup do Azure

Para proteger contra desastres regionais, o [backup do Azure](../articles/backup/backup-overview.md) pode ser usado para criar um trabalho de backup com backups baseados em tempo e políticas de retenção de backup. Isso permite que você execute restaurações de VM fáceis no. Atualmente, o backup do Azure dá suporte a tamanhos de disco de até quatro discos tebibyte (TiB).  O backup do Azure dá suporte a backup e restauração de discos gerenciados. [Saiba mais](../articles/backup/backup-support-matrix-iaas.md) sobre o suporte de backup de VM do Azure.

### <a name="granular-access-control"></a>Controle de acesso granular

Você pode usar o [RBAC (controle de acesso baseado em função) do Azure](../articles/role-based-access-control/overview.md) para atribuir permissões específicas para um disco gerenciado a um ou mais usuários. Os discos gerenciados expõem uma variedade de operações, incluindo leitura, gravação (criação/atualização), exclusão e recuperação de um [URI de SAS (assinatura de acesso compartilhado)](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) para o disco. Você pode conceder acesso apenas às operações que uma pessoa precisa para executar seu trabalho. Por exemplo, se você não quiser que uma pessoa Copie um disco gerenciado para uma conta de armazenamento, você pode optar por não conceder acesso à ação de exportação para esse disco gerenciado. Da mesma forma, se você não quiser que uma pessoa use um URI de SAS para copiar um disco gerenciado, você pode optar por não conceder essa permissão ao disco gerenciado.

### <a name="upload-your-vhd"></a>Carregar seu VHD

 O carregamento direto facilita a transferência do VHD para um disco gerenciado do Azure. Anteriormente, era necessário seguir um processo mais envolvido que incluía o preparo de seus dados em uma conta de armazenamento. Agora, há menos etapas. É mais fácil carregar VMs locais para o Azure, carregar em grandes discos gerenciados e o processo de backup e restauração é simplificado. Ele também reduz o custo, permitindo que você carregue dados diretamente em discos gerenciados sem anexá-los a VMs. Você pode usar o carregamento direto para carregar VHDs de até 32 TiB de tamanho.

 Para saber como transferir seu VHD para o Azure, consulte os artigos da [CLI](../articles/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli.md) ou do [PowerShell](../articles/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md) .

## <a name="encryption"></a>Encriptação

O Managed disks oferece dois tipos diferentes de criptografia. A primeira é a SSE (criptografia do servidor), que é executada pelo serviço de armazenamento. A segunda é Azure Disk Encryption (ADE), que pode ser habilitada no sistema operacional e nos discos de dados para suas VMs.

### <a name="server-side-encryption"></a>Encriptação do lado do servidor

A [criptografia do lado do servidor do Azure](../articles/storage/common/storage-service-encryption.md) fornece criptografia em repouso e protege seus dados para atender aos compromissos de conformidade e segurança organizacional. A criptografia do lado do servidor é habilitada por padrão para todos os discos gerenciados, instantâneos e imagens em todas as regiões em que os Managed disks estão disponíveis. Visite a [página de perguntas frequentes Managed disks](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) para obter mais detalhes.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption permite criptografar o sistema operacional e os discos de dados usados por uma máquina virtual IaaS. Essa criptografia inclui discos gerenciados. Para o Windows, as unidades são criptografadas usando a tecnologia de criptografia BitLocker padrão do setor. Para o Linux, os discos são criptografados usando a tecnologia DM-cript. O processo de encriptação é integrado no Azure Key Vault para lhe permitir controlar e gerir as chaves de encriptação dos discos. Para obter mais informações, consulte [Azure Disk Encryption para VMs de IaaS](../articles/security/azure-security-disk-encryption-overview.md).

## <a name="disk-roles"></a>Funções de disco

Há três funções de disco principais no Azure: o disco de dados, o disco do sistema operacional e o disco temporário. Essas funções são mapeadas para discos anexados à sua máquina virtual.

![Funções de disco em ação](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Disco de dados

Um disco de dados é um disco gerenciado que é anexado a uma máquina virtual para armazenar dados de aplicativos ou outros dados que você precisa manter. Os discos de dados são registrados como unidades SCSI e rotulados com uma letra que você escolhe. Cada disco de dados tem uma capacidade máxima de 32.767 Gibibytes (GiB). O tamanho da máquina virtual determina quantos discos de dados você pode anexar a ele e o tipo de armazenamento que você pode usar para hospedar os discos.

### <a name="os-disk"></a>Disco do SO

Cada máquina virtual tem um disco do sistema operacional anexado. Esse disco do sistema operacional tem um sistema operacional pré-instalado, que foi selecionado quando a VM foi criada. Esse disco contém o volume de inicialização.

Esse disco tem uma capacidade máxima de 2.048 GiB.

### <a name="temporary-disk"></a>Disco temporário

Cada VM contém um disco temporário, que não é um disco gerenciado. O disco temporário fornece armazenamento de curto prazo para aplicativos e processos e destina-se apenas a armazenar dados como arquivos de página ou de permuta. Os dados no disco temporário podem ser perdidos durante um evento de [manutenção](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) ou quando você [reimplanta uma VM](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). Em VMs Linux do Azure, o disco temporário é/dev/sdb por padrão e, em VMs do Windows, o disco temporário é D: por padrão. Durante uma reinicialização padrão bem-sucedida da VM, os dados no disco temporário serão mantidos.

## <a name="managed-disk-snapshots"></a>Instantâneos do disco gerenciado

Um instantâneo do disco gerenciado é uma cópia completa consistente com falha somente leitura de um disco gerenciado que é armazenado como um disco gerenciado padrão por padrão. Com os instantâneos, você pode fazer backup dos discos gerenciados em qualquer momento. Esses instantâneos existem independentemente do disco de origem e podem ser usados para criar novos discos gerenciados. 

Os instantâneos são cobrados com base no tamanho usado. Por exemplo, se você criar um instantâneo de um disco gerenciado com capacidade provisionada de 64 GiB e tamanho real de dados usados de 10 GiB, esse instantâneo será cobrado somente pelo tamanho dos dados usados de 10 GiB. Você pode ver o tamanho usado de seus instantâneos examinando o [relatório de uso do Azure](https://docs.microsoft.com/en-us/azure/billing/billing-understand-your-bill). Por exemplo, se o tamanho dos dados usados de um instantâneo for 10 GiB, o relatório de uso mostrará 10 GiB/(31 dias x 24 horas) = 0, 13441 GiB como a quantidade consumida.

Para saber mais sobre como criar instantâneos com o Managed disks, confira os seguintes recursos:

* [Criar cópia do VHD armazenado como um disco gerenciado usando instantâneos no Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Create copy of VHD stored as a managed disk using snapshots in Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md) (Criar uma cópia de um VHD armazenado como disco gerido com os instantâneos no Linux)

### <a name="images"></a>Imagens

Os discos gerenciados também oferecem suporte à criação de uma imagem personalizada gerenciada. Você pode criar uma imagem de seu VHD personalizado em uma conta de armazenamento ou diretamente de uma VM generalizada (Sysprep). Esse processo captura uma única imagem. Esta imagem contém todos os discos gerenciados associados a uma VM, incluindo o sistema operacional e os discos de dados. Essa imagem personalizada gerenciada permite a criação de centenas de VMs usando sua imagem personalizada sem a necessidade de copiar ou gerenciar contas de armazenamento.

Para obter informações sobre como criar imagens, consulte os seguintes artigos:

* [Como capturar uma imagem gerenciada de uma VM generalizada no Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [How to generalize and capture a Linux virtual machine using the Azure CLI](../articles/virtual-machines/linux/capture-image.md) (Como generalizar e capturar uma máquina virtual do Linux com a CLI do Azure)

#### <a name="images-versus-snapshots"></a>Imagens versus instantâneos

É importante entender a diferença entre imagens e instantâneos. Com o Managed disks, você pode tirar uma imagem de uma VM generalizada que foi desalocada. Essa imagem inclui todos os discos anexados à VM. Você pode usar essa imagem para criar uma VM e ela inclui todos os discos.

Um instantâneo é uma cópia de um disco no momento em que o instantâneo é tirado. Aplica-se somente a um disco. Se você tiver uma VM que tenha um disco (o disco do sistema operacional), poderá tirar um instantâneo ou uma imagem dele e criar uma VM por meio do instantâneo ou da imagem.

Um instantâneo não tem reconhecimento de nenhum disco, exceto aquele que ele contém. Isso torna o uso problemático em cenários que exigem a coordenação de vários discos, como a distribuição. Os instantâneos precisariam ser capazes de coordenar um ao outro e não há suporte para isso no momento.

## <a name="disk-allocation-and-performance"></a>Desempenho e alocação de disco

O diagrama a seguir ilustra a alocação em tempo real de largura de banda e IOPS para discos, usando um sistema de provisionamento de três níveis:

![Sistema de provisionamento de três níveis mostrando largura de banda e alocação de IOPS](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

O provisionamento de primeiro nível define o IOPS por disco e a atribuição de largura de banda.  No segundo nível, o host do servidor de computação implementa o provisionamento de SSD, aplicando-o apenas aos dados armazenados no SSD do servidor, que inclui discos com cache (ReadWrite e ReadOnly), bem como discos locais e temporários. Por fim, o provisionamento da rede VM ocorre no terceiro nível para qualquer e/s que o host de computação envia para o back-end do armazenamento do Azure. Com esse esquema, o desempenho de uma VM depende de uma variedade de fatores, desde como a VM usa o SSD local até o número de discos anexados, bem como o tipo de desempenho e cache dos discos que ele anexou.

Como um exemplo dessas limitações, uma VM Standard_DS1v1 é impedida de alcançar o potencial de 5.000 IOPS de um disco p30, seja em cache ou não, devido aos limites nos níveis SSD e de rede:

![Standard_DS1v1 exemplo de alocação](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

O Azure usa o canal de rede priorizado para o tráfego de disco, que obtém a precedência sobre outra prioridade baixa do tráfego de rede. Isso ajuda os discos a manter o desempenho esperado no caso de contenções de rede. Da mesma forma, o armazenamento do Azure lida com contenções de recursos e outros problemas em segundo plano com balanceamento de carga automático. O armazenamento do Azure aloca os recursos necessários quando você cria um disco e aplica o balanceamento proativo e reativo de recursos para lidar com o nível de tráfego. Isso garante que os discos possam sustentar seus destinos de taxa de transferência e IOPS esperados. Você pode usar as métricas em nível de VM e de disco para controlar o desempenho e os alertas de configuração conforme necessário.

Consulte nosso artigo [design para alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md) para aprender as práticas recomendadas para otimizar as configurações de VM + disco para que você possa atingir o desempenho desejado

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os tipos de disco individuais que o Azure oferece, que tipo é uma boa opção para suas necessidades e saiba mais sobre seus destinos de desempenho em nosso artigo sobre tipos de disco.
