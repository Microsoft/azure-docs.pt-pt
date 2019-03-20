---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 287ae3b8122dd2a1e43c31055ac0ea5b04dddb07
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190697"
---
## <a name="benefits-of-managed-disks"></a>Benefícios dos discos geridos

Vamos abordar alguns dos benefícios que obtém ao utilizar discos geridos.

### <a name="highly-durable-and-available"></a>Elevada disponibilidade e durabilidade

Discos geridos foram projetados para disponibilidade de 99,999%. Discos geridos conseguir isto ao fornecer-lhe com três réplicas dos seus dados, permitindo uma elevada durabilidade. Se uma ou até mesmo duas réplicas apresentarem problemas, as restantes ajudam a garantir a persistência dos dados e alta tolerância contra falhas. Esta arquitetura ajudou a entregar consistentemente a nível empresarial do Azure durabilidade para infraestrutura como serviço (IaaS) discos, com um líder do setor ZERO taxa anual de falha de %.

### <a name="simple-and-scalable-vm-deployment"></a>Implantação de VM simples e dimensionável

Utilizar discos geridos, pode criar até 50 000 VM **discos** de um tipo de uma subscrição por região, permitindo-lhe criar milhares de **VMs** numa única subscrição. Esta funcionalidade também aumenta a escalabilidade do [conjuntos de dimensionamento de máquinas virtuais](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) , permitindo-lhe criar até 1000 VMs num conjunto de dimensionamento com uma imagem do Marketplace.

### <a name="integration-with-availability-sets"></a>Integração com conjuntos de disponibilidade

Discos geridos estão integrados com conjuntos de disponibilidade para garantir que os discos da [VMs num conjunto de disponibilidade](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) são suficientemente isolados uns dos outros para evitar um ponto único de falha. Discos são colocados automaticamente em unidades de escala de armazenamento diferente (carimbos de data /). Se um carimbo de data / falhar devido uma falha de hardware ou software, apenas as instâncias VM com discos nesses carimbos falharem. Por exemplo, digamos que tenha um aplicativo em execução em VMs de cinco, e as VMs estão num conjunto de disponibilidade. Os discos para essas VMs não ser armazenadas no carimbo a mesmo, portanto, se um carimbo ficar inativo, as outras instâncias do aplicativo continuam a executar.

### <a name="azure-backup-support"></a>Suporte de cópia de segurança do Azure

Para proteger contra desastres regionais, [cópia de segurança do Azure](../articles/backup/backup-introduction-to-azure-backup.md) pode ser utilizado para criar uma tarefa de cópia de segurança com as políticas de retenção de cópia de segurança e cópias de segurança baseados no tempo. Isto permite-lhe executar restaurações de VM fácil à vontade. Atualmente o Azure Backup suporta tamanhos de disco até quatro discos de tebibyte (TiB). Para obter mais informações, consulte [utilizando o Azure Backup para VMs com discos geridos](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

### <a name="granular-access-control"></a>Controlo de acesso granular

Pode usar [controlo de acesso baseado em função do Azure (RBAC)](../articles/role-based-access-control/overview.md) para atribuir permissões específicas para um disco gerido a um ou mais utilizadores. Expõem de discos geridos uma variedade de operações, incluindo de leitura, escrita (criar/atualizar), eliminar e obter um [assinatura de acesso partilhado (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) para o disco. Pode conceder acesso a apenas as operações de que uma pessoa precisa de executar seu trabalho. Por exemplo, se não pretender que uma pessoa para copiar um disco gerido para uma conta de armazenamento, pode optar por não conceder acesso para a ação de exportação para esse disco gerido. Da mesma forma, se não pretender que uma pessoa a utilizar um URI de SAS para copiar um disco gerido, pode optar por não conceda essa permissão para o disco gerido.

## <a name="disk-roles"></a>Funções de disco

### <a name="data-disks"></a>Discos de dados

Um disco de dados é um disco gerido, que está ligado a uma máquina virtual para armazenar dados de aplicação ou outros dados que precisa para manter. Discos de dados estão registados como unidades SCSI e são rotulados com uma letra que escolher. Cada disco de dados tem a capacidade máxima de 4095 gibibytes (GiB). O tamanho da máquina virtual determina quantos discos de dados, pode anexar a ele e o tipo de armazenamento pode utilizar para alojar os discos.

### <a name="os-disks"></a>Discos de SO

Cada máquina virtual possui um disco de sistema de operativo anexado. Se o disco do SO tem um sistema operacional previamente instalado, o que foi selecionado quando a VM foi criada.

Este disco tem a capacidade máxima de 2048 GiB.

### <a name="temporary-disk"></a>Disco temporário

Cada VM contém um disco temporário, que não é um disco gerido. O disco temporário fornece armazenamento de curto prazo para aplicações e processos e destina-se para armazenar apenas os dados, tais como ficheiros de paginação ou de troca. Dados do disco temporário podem ser perdidos durante uma [evento de manutenção](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) eventos ou quando [Reimplementar uma VM](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). Durante uma reinicialização bem sucedida padrão da VM, irão manter os dados na unidade temporária. 

## <a name="managed-disk-snapshots"></a>Instantâneos de disco gerido

Um instantâneo do disco gerido é uma cópia completa só de leitura de um disco gerido, que é armazenado como um disco gerido standard por predefinição. Com instantâneos, pode fazer backup de seus discos geridos em qualquer ponto no tempo. Estes instantâneos existem independentemente do disco de origem e podem ser utilizados para criar novos discos geridos. Eles são faturados com base no tamanho utilizado. Por exemplo, se criar um instantâneo de um disco gerido com capacidade aprovisionada de 64 GiB e o tamanho de dados utilizados real de 10 GiB, nesse instantâneo é cobrado apenas para o tamanho de dados utilizados de 10 GiB.  

Para saber mais sobre como criar instantâneos com discos geridos, consulte os seguintes recursos:

* [Criar cópia de VHD armazenado como um disco gerido com os instantâneos no Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Criar cópia de VHD armazenado como um disco gerido com os instantâneos no Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Imagens

Discos geridos também suportam a criação de uma imagem personalizada gerida. Pode criar uma imagem a partir do seu VHD personalizado numa conta de armazenamento ou diretamente a partir de um generalizado (Sysprep) VM. Este processo captura uma única imagem. Esta imagem contém todos os discos geridos associados a uma VM, incluindo o sistema operacional e os dados de discos. Esta imagem personalizada gerida permite criar centenas de VMs através da sua imagem personalizada sem a necessidade de copiar ou gerir as contas de armazenamento.

Para obter informações sobre a criação de imagens, consulte os artigos seguintes:

* [Como capturar uma imagem gerida de uma VM generalizada no Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Como generalizar e capturar uma máquina virtual do Linux com a CLI do Azure](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Imagens com instantâneos

É importante compreender a diferença entre os instantâneos e imagens. Com discos geridos, pode tirar uma imagem de uma VM generalizada que foi desalocada. Esta imagem inclui todos os discos ligados à VM. Pode utilizar esta imagem para criar uma VM e inclui todos os discos.

Um instantâneo é uma cópia de um disco no ponto no tempo que o instantâneo. Aplica-se apenas a um disco. Se tiver uma VM que possui um disco (o disco do SO), pode tirar um instantâneo ou uma imagem dele e criar uma VM a partir do instantâneo ou a imagem.

Um instantâneo não tem o reconhecimento de qualquer disco, exceto o contém. Isso torna problemas ao serem usados em cenários que requerem a coordenação de vários discos, por exemplo, a repartição. Instantâneos precisariam ser capaz de se coordenar com uns aos outros e isso não é atualmente suportado.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os tipos de disco individual, que o Azure oferece e que tipo é uma boa opção para as suas necessidades no nosso artigo sobre os tipos de disco.
