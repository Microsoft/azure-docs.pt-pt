---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0eaac5aac94c536fda58d7d004a54df51219f7cd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82147784"
---
## <a name="benefits-of-managed-disks"></a>Benefícios dos discos geridos

Vamos ver alguns dos benefícios que ganhas com o uso de discos geridos.

### <a name="highly-durable-and-available"></a>Altamente durável e disponível

Os discos geridos destinam-se a uma disponibilidade de 99,999%. Os discos geridos conseguem-no fornecendo-lhe três réplicas dos seus dados, permitindo uma elevada durabilidade. Se uma ou mesmo duas réplicas experimentarem problemas, as réplicas restantes ajudam a garantir a persistência dos seus dados e a alta tolerância contra falhas. Esta arquitetura tem ajudado o Azure a oferecer consistentemente durabilidade de nível empresarial para a infraestrutura como um serviço (IaaS) discos, com uma taxa de falha anualizada líder do setor de ZERO%.

### <a name="simple-and-scalable-vm-deployment"></a>Implantação vm simples e escalável

Utilizando discos geridos, pode criar até 50.000 **discos** VM de um tipo numa subscrição por região, permitindo-lhe criar milhares de **VMs** numa única subscrição. Esta funcionalidade também aumenta ainda mais a escalabilidade dos conjuntos de escala de [máquinas virtuais,](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) permitindo-lhe criar até 1.000 VMs num conjunto de escala de máquina virtual utilizando uma imagem do Marketplace.

### <a name="integration-with-availability-sets"></a>Integração com conjuntos de disponibilidade

Os discos geridos são integrados com conjuntos de disponibilidade para garantir que os discos de [VMs num conjunto](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) de disponibilidade estão suficientemente isolados uns dos outros para evitar um único ponto de falha. Os discos são automaticamente colocados em diferentes unidades de escala de armazenamento (selos). Se um carimbo falhar devido a falha de hardware ou software, apenas os casos vm com discos nesses selos falham. Por exemplo, digamos que tem uma aplicação em execução em cinco VMs, e os VMs estão em um Conjunto de Disponibilidade. Os discos para esses VMs não serão todos armazenados no mesmo carimbo, por isso, se um carimbo cair, as outras instâncias da aplicação continuam a ser executadas.

### <a name="integration-with-availability-zones"></a>Integração com Zonas de Disponibilidade

Os discos geridos suportam as Zonas de [Disponibilidade,](../articles/availability-zones/az-overview.md)que é uma oferta de alta disponibilidade que protege as suas aplicações de falhas no datacenter. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas. Com Zonas de Disponibilidade, o Azure oferece à indústria o melhor tempo de uptime De SLA de 99,99% vM.

### <a name="azure-backup-support"></a>Suporte de backup Azure

Para proteger contra desastres regionais, [o Azure Backup](../articles/backup/backup-overview.md) pode ser usado para criar um trabalho de backup com backups baseados no tempo e políticas de retenção de backup. Isto permite-lhe realizar VM ou restauros de discos geridos à vontade. Atualmente, o Azure Backup suporta tamanhos de disco até 32 discos de tebibyte (TiB). [Saiba mais](../articles/backup/backup-support-matrix-iaas.md) sobre o suporte de apoio à VM Azure.

### <a name="granular-access-control"></a>Controlo de acesso granular

Pode utilizar [o controlo de acesso baseado em funções do Azure (RBAC)](../articles/role-based-access-control/overview.md) para atribuir permissões específicas para um disco gerido a um ou mais utilizadores. Os discos geridos expõem uma variedade de operações, incluindo ler, escrever (criar/atualizar), eliminar e recuperar uma assinatura de [acesso partilhado (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) para o disco. Só pode conceder acesso às operações que uma pessoa precisa para realizar o seu trabalho. Por exemplo, se não quiser que uma pessoa copie um disco gerido para uma conta de armazenamento, pode optar por não conceder acesso à ação de exportação para esse disco gerido. Da mesma forma, se não quiser que uma pessoa utilize um SAS URI para copiar um disco gerido, pode optar por não conceder essa permissão ao disco gerido.

### <a name="upload-your-vhd"></a>Faça upload do seu vhd

 O upload direto facilita a transferência do seu vhd para um disco gerido pelo Azure. Anteriormente, tinha de seguir um processo mais envolvido que incluía a encenação dos seus dados numa conta de armazenamento. Agora, há menos passos. É mais fácil fazer upload nas instalações vMs para Azure, carregar para grandes discos geridos, e o processo de backup e restauro é simplificado. Também reduz o custo, permitindo-lhe fazer o upload de dados para os discos geridos diretamente sem os ligar aos VMs. Pode utilizar o upload direto para carregar vhds até 32 TiB em tamanho.

 Para aprender a transferir o seu vhd para O Azure, consulte os artigos [CLI](../articles/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli.md) ou [PowerShell.](../articles/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)

## <a name="encryption"></a>Encriptação

Os discos geridos oferecem dois tipos diferentes de encriptação. A primeira é a Encriptação Lateral do Servidor (SSE), que é executada pelo serviço de armazenamento. A segunda é a Encriptação do Disco Azure (ADE), que pode ativar no SISTEMA e nos discos de dados para os seus VMs.

### <a name="server-side-encryption"></a>Encriptação do lado do servidor

A [encriptação do lado do Servidor Azure](../articles/virtual-machines/windows/disk-encryption.md) fornece encriptação em repouso e salvaguarda os seus dados para atender aos seus compromissos de segurança organizacional e conformidade. A encriptação do lado do servidor é ativada por padrão para todos os discos, instantâneos e imagens geridos em todas as regiões onde os discos geridos estão disponíveis. Pode permitir que o Azure gere as suas chaves para si, estas são chaves geridas pela plataforma, ou pode gerir as chaves por si mesmo, estas são chaves geridas pelo cliente. Visite a página FAQ de [Discos Geridos](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) para mais detalhes.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

A encriptação do disco Azure permite-lhe encriptar os discos OS e Data utilizados por uma Máquina Virtual IaaS. Esta encriptação inclui discos geridos. Para o Windows, as unidades são encriptadas utilizando a tecnologia de encriptação BitLocker padrão da indústria. Para o Linux, os discos são encriptados utilizando a tecnologia DM-Crypt. O processo de encriptação é integrado no Azure Key Vault para lhe permitir controlar e gerir as chaves de encriptação dos discos. Para mais informações, consulte a [Encriptação do Disco Azure para VMs IaaS](../articles/security/azure-security-disk-encryption-overview.md).

## <a name="disk-roles"></a>Funções de discos

Existem três funções principais de disco em Azure: o disco de dados, o disco OS e o disco temporário. Estas funções mapeiam para discos que estão ligados à sua máquina virtual.

![Funções de disco em ação](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Disco de dados

Um disco de dados é um disco gerido que está ligado a uma máquina virtual para armazenar dados de aplicações, ou outros dados que precisa de guardar. Os discos de dados estão registados como unidades SCSI e estão rotulados com uma letra que escolher. Cada disco de dados tem uma capacidade máxima de 32.767 gibibytes (GiB). O tamanho da máquina virtual determina quantos discos de dados pode sacar-lhe e o tipo de armazenamento que pode utilizar para alojar os discos.

### <a name="os-disk"></a>Disco do SO

Todas as máquinas virtuais têm um disco do sistema operativo ligado. O disco osso tem um SISTEMA pré-instalado, que foi selecionado quando o VM foi criado. Este disco contém o volume de arranque.

Este disco tem uma capacidade máxima de 2.048 GiB.

### <a name="temporary-disk"></a>Disco temporário

Cada VM contém um disco temporário, que não é um disco gerido. O disco temporário fornece armazenamento de curto prazo para aplicações e processos e destina-se apenas a armazenar dados como ficheiros de página ou de troca. Os dados do disco temporário podem perder-se durante um evento de [manutenção](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) ou quando [reimplanta um VM](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). Nos VMs Azure Linux, o disco temporário é /dev/sdb por padrão e nos VMs do Windows o disco temporário é D: por padrão. Durante um reboot padrão bem sucedido do VM, os dados do disco temporário persistirão.

## <a name="managed-disk-snapshots"></a>Instantâneos de disco geridos

Um instantâneo de disco gerido é uma cópia completa consistente com uma leitura de um disco gerido que é armazenado como um disco gerido padrão por padrão. Com imagens, pode fazer o back up dos seus discos geridos a qualquer momento. Estes instantâneos existem independentemente do disco de origem e podem ser usados para criar novos discos geridos. 

As imagens são faturadas com base no tamanho usado. Por exemplo, se criar uma imagem instantânea de um disco gerido com capacidade aprovisionada de 64 GiB e tamanho real de dados utilizados de 10 GiB, esse instantâneo é faturado apenas para o tamanho de dados utilizados de 10 GiB. Pode ver o tamanho usado das suas fotos olhando para o relatório de utilização do [Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). Por exemplo, se o tamanho dos dados utilizados de um instantâneo for de 10 GiB, o relatório de utilização **diária** mostrará 10 GiB/(31 dias) = 0,3226 como quantidade consumida.

Para saber mais sobre como criar instantâneos para discos geridos, consulte os seguintes recursos:

* [Criar uma imagem de um disco gerido no Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Crie uma imagem de um disco gerido em Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Imagens

Os discos geridos também suportam a criação de uma imagem personalizada gerida. Pode criar uma imagem a partir do seu VHD personalizado numa conta de armazenamento ou diretamente a partir de um VM generalizado (sysprepped). Este processo captura uma única imagem. Esta imagem contém todos os discos geridos associados a um VM, incluindo tanto o SISTEMA e os discos de dados. Esta imagem personalizada gerida permite criar centenas de VMs usando a sua imagem personalizada sem a necessidade de copiar ou gerir quaisquer contas de armazenamento.

Para obter informações sobre a criação de imagens, consulte os seguintes artigos:

* [Como capturar uma imagem gerida de um VM generalizado em Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [How to generalize and capture a Linux virtual machine using the Azure CLI](../articles/virtual-machines/linux/capture-image.md) (Como generalizar e capturar uma máquina virtual do Linux com a CLI do Azure)

#### <a name="images-versus-snapshots"></a>Imagens versus instantâneos

É importante entender a diferença entre imagens e imagens. Com discos geridos, pode tirar uma imagem de um VM generalizado que foi deallocalizado. Esta imagem inclui todos os discos ligados ao VM. Pode usar esta imagem para criar um VM, e inclui todos os discos.

Uma foto é uma cópia de um disco no momento em que a foto é tirada. Aplica-se apenas a um disco. Se tiver um VM que tenha um disco (o disco OS), pode tirar uma fotografia ou uma imagem do mesmo e criar um VM a partir do instantâneo ou da imagem.

Uma foto não tem consciência de nenhum disco exceto o que contém. Isto torna problemático usar em cenários que requerem a coordenação de vários discos, como a striping. Os instantâneos teriam de ser capazes de coordenar-se uns com os outros, o que não é atualmente suportado.

## <a name="disk-allocation-and-performance"></a>Atribuição e desempenho do disco

O diagrama seguinte retrata a atribuição em tempo real da largura de banda e do IOPS para discos, utilizando um sistema de provisionamento de três níveis:

![Sistema de provisionamento de três níveis que mostra largura de banda e atribuição de IOPS](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

O fornecimento de primeiro nível define o IOPS por disco e a atribuição de largura de banda.  No segundo nível, o anfitrião do servidor computacional implementa o fornecimento de SSD, aplicando-o apenas aos dados armazenados no SSD do servidor, que inclui discos com cache (ReadWrite e ReadOnly) bem como discos locais e temporários. Finalmente, o fornecimento de rede VM ocorre no terceiro nível para qualquer I/O que o anfitrião da computação envia para o backend do Azure Storage. Com este esquema, o desempenho de um VM depende de uma variedade de fatores, desde a forma como o VM utiliza o SSD local, até ao número de discos ligados, bem como o tipo de desempenho e cache dos discos que tem anexado.

Como exemplo destas limitações, uma VM Standard_DS1v1 é impedida de atingir o potencial de 5.000 IOPS de um disco P30, seja em cache ou não, devido a limites ao SSD e aos níveis de rede:

![Standard_DS1v1 atribuição de exemplo](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

O Azure utiliza um canal de rede prioritário para o tráfego de discos, o que obtém a precedência sobre outras baixas prioridades do tráfego de rede. Isto ajuda os discos a manter o seu desempenho esperado em caso de contenção de rede. Da mesma forma, o Armazenamento Azure lida com as contençãos de recursos e outros problemas em segundo plano com o equilíbrio automático de carga. O Azure Storage aloca recursos necessários quando cria um disco e aplica um equilíbrio proactivo e reativo de recursos para lidar com o nível de tráfego. Isto garante ainda que os discos podem sustentar os seus objetivos de IOPS e de entrada esperados. Pode utilizar as métricas de nível VM e de nível de disco para acompanhar os alertas de desempenho e configuração, conforme necessário.

Consulte o nosso design para artigo de [alto desempenho,](../articles/virtual-machines/windows/premium-storage-performance.md) para aprender as melhores práticas para otimizar as configurações vM + Disk para que possa alcançar o seu desempenho desejado

## <a name="next-steps"></a>Passos seguintes

Se quiser que um vídeo entre em mais detalhes nos discos geridos, confira: [Better Azure VM Resiliency with Managed Disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).

Saiba mais sobre os tipos individuais de discos que o Azure oferece, que tipo é um bom ajuste para as suas necessidades, e saiba sobre os seus objetivos de desempenho no nosso artigo sobre tipos de disco.
