---
title: Visão geral do armazenamento do disco Azure
description: Visão geral dos discos geridos Azure, que manuseiam as contas de armazenamento para si quando utilizar VMs.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: contperf-fy21q1
ms.openlocfilehash: eea5c800d7aa9c8d1e6c0c507136b86ab8bf21f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604037"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduction to Azure managed disks (Introdução aos discos geridos do Azure)

Os discos geridos azure são volumes de armazenamento de nível de bloco que são geridos pela Azure e usados com Máquinas Virtuais Azure. Os discos geridos são como um disco físico num servidor no local, mas virtualizados. Com os discos geridos, tudo o que tem de fazer é especificar o tamanho do disco, o tipo de disco e o fornecimento do disco. Uma vez que forja o disco, Azure trata do resto.

Os tipos disponíveis de discos são discos ultra, unidades de estado sólido premium (SSD), SSDs padrão e unidades de disco rígido padrão (HDD). Para obter informações sobre cada tipo de disco individual, consulte [selecionar um tipo de disco para IaaS VMs](disks-types.md).

## <a name="benefits-of-managed-disks"></a>Benefícios dos discos geridos

Vamos ver alguns dos benefícios que ganha usando discos geridos.

### <a name="highly-durable-and-available"></a>Altamente durável e disponível

Os discos geridos são projetados para uma disponibilidade de 99.999%. Os discos geridos tornam isso possível ao fornecer-lhe três réplicas dos seus dados que lhe proporcionam uma durabilidade elevada. Se ocorrerem problemas numa ou duas réplicas, as restantes ajudarão a garantir a persistência dos seus dados, bem como uma tolerância elevada contra falhas. Esta arquitetura tem ajudado a Azure a oferecer consistentemente durabilidade de qualidade empresarial para infraestruturas como discos de serviço (IaaS), com uma taxa de insucesso anualizada líder no setor.

### <a name="simple-and-scalable-vm-deployment"></a>Implantação de VM simples e escalável

Utilizando discos geridos, pode criar até 50.000 **discos** VM de um tipo numa subscrição por região, permitindo-lhe criar milhares de **VMs** numa única subscrição. Esta funcionalidade também aumenta a escalabilidade dos [conjuntos de escala de máquinas virtuais,](../virtual-machine-scale-sets/overview.md) permitindo-lhe criar até 1.000 VMs numa escala de máquina virtual definida com uma imagem de Marketplace.

### <a name="integration-with-availability-sets"></a>Integração com conjuntos de disponibilidade

Os discos geridos são integrados com conjuntos de disponibilidade para garantir que os discos de [VMs num conjunto](./availability-set-overview.md) de disponibilidade estão suficientemente isolados uns dos outros para evitar um único ponto de falha. Os discos são automaticamente colocados em diferentes unidades de escala de armazenamento (selos). Se um carimbo falhar devido a falha de hardware ou software, apenas as instâncias VM com discos nesses selos falham. Por exemplo, digamos que tem uma aplicação em execução em cinco VMs, e os VMs estão em um Conjunto de Disponibilidade. Os discos para esses VMs não serão todos armazenados no mesmo carimbo, por isso, se um carimbo cair, as outras instâncias da aplicação continuam a ser executadas.

### <a name="integration-with-availability-zones"></a>Integração com Zonas de Disponibilidade

Os discos geridos suportam [Zonas de Disponibilidade,](../availability-zones/az-overview.md)que é uma oferta de alta disponibilidade que protege as suas aplicações contra falhas no datacenter. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas. Com Zonas de Disponibilidade, o Azure oferece à indústria o melhor SLA de 99,99% de VM uptime.

### <a name="azure-backup-support"></a>Suporte de backup Azure

Para proteger contra desastres regionais, [o Azure Backup](../backup/backup-overview.md) pode ser usado para criar um trabalho de reserva com backups baseados no tempo e políticas de retenção de backup. Isto permite-lhe realizar restauros de VM ou discos geridos à vontade. Atualmente, o Azure Backup suporta tamanhos de disco até 32 discos tebibyte (TiB). [Saiba mais](../backup/backup-support-matrix-iaas.md) sobre o suporte de backup da Azure VM.

#### <a name="azure-disk-backup"></a>Cópia de Segurança de Discos do Azure

O Azure Backup oferece backup de disco Azure (pré-visualização) como uma solução de backup nativa baseada na nuvem que protege os seus dados em discos geridos. É uma solução simples, segura e económica que lhe permite configurar a proteção para discos geridos em alguns passos. O Azure Disk Backup oferece uma solução chave na mão que fornece uma gestão instantânea do ciclo de vida para discos geridos, automatizando a criação periódica de instantâneos e mantendo-o durante a duração configurada usando a política de backup. Para obter mais informações sobre a cópia de segurança do disco Azure, consulte [a visão geral da cópia de segurança do disco Azure (na pré-visualização)](../backup/disk-backup-overview.md).

### <a name="granular-access-control"></a>Controlo de acesso granular

Pode utilizar [o controlo de acesso baseado em funções (Azure RBAC)](../role-based-access-control/overview.md) para atribuir permissões específicas para um disco gerido a um ou mais utilizadores. Os discos geridos expõem uma variedade de operações, incluindo ler, escrever (criar/atualizar), eliminar e recuperar uma [assinatura de acesso partilhado (SAS) URI](../storage/common/storage-sas-overview.md) para o disco. Pode conceder acesso apenas às operações que uma pessoa precisa para desempenhar o seu trabalho. Por exemplo, se não quiser que uma pessoa copie um disco gerido para uma conta de armazenamento, pode optar por não conceder acesso à ação de exportação para esse disco gerido. Da mesma forma, se não quiser que uma pessoa utilize um SAS URI para copiar um disco gerido, pode optar por não conceder essa permissão ao disco gerido.

### <a name="upload-your-vhd"></a>Faça upload do seu vhd

O upload direto facilita a transferência do vhd para um disco gerido aZure. Anteriormente, tinha de seguir um processo mais envolvido que incluísse a paragem dos seus dados numa conta de armazenamento. Agora, há menos passos. É mais fácil carregar nas instalações VMs para Azure, carregar para grandes discos geridos, e o processo de backup e restauro é simplificado. Também reduz os custos, permitindo-lhe carregar dados para discos geridos diretamente sem os anexar aos VMs. Pode utilizar o upload direto para carregar vhds até 32 TiB em tamanho.

Para aprender a transferir o seu vhd para Azure, consulte os artigos [CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) ou [PowerShell.](windows/disks-upload-vhd-to-managed-disk-powershell.md)

## <a name="security"></a>Segurança

### <a name="private-links"></a>Links Privados

O suporte private Link para discos geridos pode ser usado para importar ou exportar um disco gerido interno para a sua rede. As Ligações Privadas permitem-lhe gerar um URI de assinatura de acesso compartilhado (SAS) com tempo ligado para discos geridos não ligados e instantâneos que pode utilizar para exportar os dados para outras regiões para expansão regional, recuperação de desastres e análise forense. Também pode utilizar o SAS URI para carregar diretamente um VHD para um disco vazio a partir do local. Agora pode aproveitar [as Ligações Privadas](../private-link/private-link-overview.md) para restringir a exportação e importação de discos geridos para que só possa ocorrer dentro da sua rede virtual Azure. O Private Links permite-lhe garantir que os seus dados apenas viajam dentro da rede segura da espinha dorsal da Microsoft.

Para saber como permitir links privados para importar ou exportar um disco gerido, consulte os artigos [do CLI](linux/disks-export-import-private-links-cli.md) ou [do Portal.](disks-enable-private-links-for-import-export-portal.md)

### <a name="encryption"></a>Encriptação

Os discos geridos oferecem dois tipos diferentes de encriptação. A primeira é a Encriptação Do Lado do Servidor (SSE), que é realizada pelo serviço de armazenamento. A segunda é Azure Disk Encryption (ADE), que pode ativar no SISTEMA e discos de dados para os seus VMs.

#### <a name="server-side-encryption"></a>Encriptação do lado do servidor

A encriptação do lado do servidor fornece encriptação em repouso e salvaguarda os seus dados para atender aos seus compromissos de segurança organizacional e de conformidade. A encriptação do lado do servidor é ativada por padrão para todos os discos, instantâneos e imagens geridos, em todas as regiões onde os discos geridos estão disponíveis. (Os discos temporários, por outro lado, não são encriptados por encriptação do lado do servidor, a menos que ative a encriptação no anfitrião; ver [Funções de Disco: discos temporários](#temporary-disk)).

Pode permitir que o Azure gere as suas chaves, estas são chaves geridas pela plataforma, ou pode gerir as chaves por si mesmo, estas são chaves geridas pelo cliente. Visite a encriptação do lado do Servidor do artigo [de Armazenamento de Disco Azure](./disk-encryption.md) para obter mais detalhes.


#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

A encriptação do disco Azure permite encriptar os discos de SISTEMA e Dados utilizados por uma Máquina Virtual IaaS. Esta encriptação inclui discos geridos. Para o Windows, as unidades são encriptadas utilizando a tecnologia de encriptação BitLocker padrão da indústria. Para o Linux, os discos são encriptados utilizando a tecnologia DM-Crypt. O processo de encriptação é integrado no Azure Key Vault para lhe permitir controlar e gerir as chaves de encriptação dos discos. Para obter mais informações, consulte [a encriptação do disco Azure para Os VMs Linux](linux/disk-encryption-overview.md) ou [encriptação do disco Azure para VMs do Windows](windows/disk-encryption-overview.md).

## <a name="disk-roles"></a>Funções de discos

Existem três funções principais em disco no Azure: o disco de dados, o disco DE E e o disco temporário. Estas funções mapeiam para discos que estão ligados à sua máquina virtual.

![Funções de disco em ação](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Disco de dados

Um disco de dados é um disco gerido que está ligado a uma máquina virtual para armazenar dados de aplicações ou outros dados que precisa de guardar. Os discos de dados estão registados como unidades SCSI e são rotulados com uma letra que escolher. Cada disco de dados tem uma capacidade máxima de 32.767 gibibytes (GiB). O tamanho da máquina virtual determina quantos discos de dados pode anexar a ela e o tipo de armazenamento que pode utilizar para hospedar os discos.

### <a name="os-disk"></a>Disco do SO

Cada máquina virtual tem um disco de sistema operativo ligado. O disco de SO tem um SISTEMA pré-instalado, que foi selecionado quando o VM foi criado. Este disco contém o volume de arranque.

Este disco tem uma capacidade máxima de 4.095 GiB.

### <a name="temporary-disk"></a>Disco temporário

A maioria dos VMs contêm um disco temporário, que não é um disco gerido. O disco temporário fornece armazenamento de curto prazo para aplicações e processos, e destina-se apenas a armazenar dados como página ou ficheiros de troca. Os dados relativos ao disco temporário podem ser perdidos durante um [evento de manutenção](./understand-vm-reboots.md) ou quando se [recolocar um VM](/troubleshoot/azure/virtual-machines/redeploy-to-new-node-windows?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Durante um reboot padrão bem sucedido do VM, os dados sobre o disco temporário persistirão. Para obter mais informações sobre VMs sem discos temporários, consulte [os tamanhos Azure VM sem disco temporário local.](azure-vms-no-temp-disk.md)

Nos VMs Azure Linux, o disco temporário é tipicamente /dev/sdb e no Windows VMs o disco temporário é D: por padrão. O disco temporário não é encriptado pela encriptação do lado do servidor, a menos que ative a encriptação no anfitrião.

## <a name="managed-disk-snapshots"></a>Instantâneos de disco geridos

Um snapshot de disco gerido é uma cópia completa consistente apenas de leitura de um disco gerido que é armazenado como um disco gerido padrão por padrão. Com instantâneos, pode fazer o back up dos seus discos geridos a qualquer momento. Estes instantâneos existem independentemente do disco de origem e podem ser usados para criar novos discos geridos. 

As imagens são faturadas com base no tamanho utilizado. Por exemplo, se criar uma imagem instantânea de um disco gerido com capacidade a provisionada de 64 GiB e tamanho real de dados usados de 10 GiB, esse instantâneo é faturado apenas para o tamanho de dados usado de 10 GiB. Pode ver o tamanho usado das suas fotos olhando para o relatório de utilização do [Azure.](../cost-management-billing/understand/review-individual-bill.md) Por exemplo, se o tamanho dos dados usados de um instantâneo for de 10 GiB, o relatório de utilização **diário** mostrará 10 GiB/((31 dias) = 0,3226 como a quantidade consumida.

Para saber mais sobre como criar instantâneos para discos geridos, consulte os seguintes recursos:

- [Criar uma imagem instantânea de um disco gerido no Windows](windows/snapshot-copy-managed-disk.md)
- [Crie uma imagem de um disco gerido em Linux](linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Imagens

Os discos geridos também suportam a criação de uma imagem personalizada gerida. Pode criar uma imagem a partir do seu VHD personalizado numa conta de armazenamento ou diretamente de um VM generalizado (sisprepped). Este processo captura uma única imagem. Esta imagem contém todos os discos geridos associados a um VM, incluindo tanto o SO como os discos de dados. Esta imagem personalizada gerida permite criar centenas de VMs usando a sua imagem personalizada sem a necessidade de copiar ou gerir quaisquer contas de armazenamento.

Para obter informações sobre a criação de imagens, consulte os seguintes artigos:

- [Como capturar uma imagem gerida de um VM generalizado em Azure](windows/capture-image-resource.md)
- [How to generalize and capture a Linux virtual machine using the Azure CLI](linux/capture-image.md) (Como generalizar e capturar uma máquina virtual do Linux com a CLI do Azure)

#### <a name="images-versus-snapshots"></a>Imagens versus instantâneos

É importante entender a diferença entre imagens e instantâneos. Com discos geridos, pode tirar uma imagem de um VM generalizado que foi transacionado. Esta imagem inclui todos os discos ligados ao VM. Pode utilizar esta imagem para criar um VM, e inclui todos os discos.

Uma fotografia é uma cópia de um disco no momento em que a fotografia é tirada. Aplica-se apenas a um disco. Se tiver um VM que tenha um disco (o disco DE), pode tirar uma fotografia ou uma imagem do mesmo e criar um VM a partir do instantâneo ou da imagem.

Um instantâneo não tem consciência de nenhum disco, exceto o que contém. Isto torna problemático a utilização em cenários que requerem a coordenação de vários discos, como o striping. Os instantâneos teriam de ser capazes de coordenar-se uns com os outros e isso não é atualmente suportado.

## <a name="disk-allocation-and-performance"></a>Atribuição e desempenho do disco

O diagrama a seguir retrata a atribuição em tempo real de largura de banda e IOPS para discos, utilizando um sistema de provisionamento de três níveis:

![Sistema de provisionamento de três níveis que mostra largura de banda e alocação de IOPS](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

O provisionamento de primeiro nível define a atribuição de IOPS por disco e largura de banda.  No segundo nível, o anfitrião do servidor computacional implementa o fornecimento de SSD, aplicando-o apenas aos dados armazenados no SSD do servidor, que inclui discos com caching (ReadWrite e ReadOnly) bem como discos locais e temporários. Finalmente, o fornecimento de rede VM ocorre no terceiro nível para qualquer I/O que o hospedeiro computacional enviar para o backend de Azure Storage. Com este esquema, o desempenho de um VM depende de uma variedade de fatores, desde a forma como o VM utiliza o SSD local, ao número de discos ligados, bem como ao desempenho e tipo de cache dos discos que anexou.

Como exemplo destas limitações, um Standard_DS1v1 VM é impedido de atingir o potencial de 5.000 IOPS de um disco P30, seja em cache ou não, devido aos limites nos níveis de SSD e de rede:

![Standard_DS1v1 alocação de exemplo](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

O Azure utiliza um canal de rede prioritário para o tráfego de discos, o que obtém a precedência sobre outra baixa prioridade do tráfego de rede. Isto ajuda os discos a manter o desempenho esperado em caso de disputas de rede. Da mesma forma, o Azure Storage lida com as disputas de recursos e outros problemas em segundo plano com o equilíbrio automático de carga. O Azure Storage atribui os recursos necessários quando cria um disco, e aplica um equilíbrio proactivo e reativo de recursos para lidar com o nível de tráfego. Isto garante ainda que os discos podem sustentar os seus alvos de IOPS e de produção esperados. Pode utilizar as métricas de nível VM e de nível de disco para acompanhar os alertas de desempenho e configuração, conforme necessário.

Consulte o nosso design para artigo [de alto desempenho,](premium-storage-performance.md) para aprender as melhores práticas para otimizar as configurações de VM + Disco para que possa alcançar o desempenho desejado

## <a name="next-steps"></a>Passos seguintes

Se quiser que um vídeo entre em mais detalhes sobre discos geridos, confira: [Melhor Resiliência VM Azure com Discos Geridos](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).

Saiba mais sobre os tipos individuais de discos que o Azure oferece, que tipo é um bom ajuste para as suas necessidades, e aprenda sobre os seus alvos de desempenho no nosso artigo sobre tipos de discos.

> [!div class="nextstepaction"]
> [Selecionar um tipo de disco para VMs IaaS](disks-types.md)