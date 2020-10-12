---
title: Considerações para a implementação de DBMS de máquinas virtuais Azure para carga de trabalho SAP Microsoft Docs
description: Considerações para a implantação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, DBMS, armazenamento, Disco ultra, armazenamento premium
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4ac3a43776ee71716e618d7a1698aa1915d3d1b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331357"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Considerações para a implantação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png



Este guia faz parte da documentação sobre como implementar e implementar software SAP no Microsoft Azure. Antes de ler este guia, leia o [guia de planeamento e implementação][planning-guide] e os artigos que o guia de planeamento o apontam. Este documento cobre os aspetos genéricos de implementação dos sistemas DBMS relacionados com o SAP nas máquinas virtuais (VMs) do Microsoft Azure, utilizando a infraestrutura Azure como capacidade de serviço (IaaS).

O papel complementa a documentação de instalação DO SAP e as Notas SAP, que representam os recursos primários para instalações e implantações de software SAP em determinadas plataformas.

Neste documento, são introduzidas considerações sobre a execução dos sistemas DBMS relacionados com a SAP em VMs Azure. Há poucas referências a sistemas DBMS específicos neste capítulo. Em vez disso, os sistemas DBMS específicos são tratados dentro deste documento, após este documento.

## <a name="definitions"></a>Definições
Ao longo do documento, estes termos são utilizados:

* **IaaS**: Infraestrutura como serviço.
* **PaaS**: Plataforma como serviço.
* **SaaS**: Software como serviço.
* **Componente SAP**: Uma aplicação SAP individual, como componente central do ERP (ECC), Business Warehouse (BW), Solution Manager ou Enterprise Portal (EP). Os componentes SAP podem basear-se em tecnologias tradicionais ABAP ou Java ou numa aplicação não baseada em NetWeaver, como Objetos Empresariais.
* **Ambiente SAP**: Um ou mais componentes SAP agrupados logicamente para desempenhar uma função de negócio como desenvolvimento, garantia de qualidade, formação, recuperação de desastres ou produção.
* **Paisagem SAP**: Este termo refere-se a todos os ativos SAP na paisagem de TI de um cliente. A paisagem SAP inclui todos os ambientes de produção e não produção.
* **Sistema SAP**: A combinação de uma camada DBMS e uma camada de aplicação de, por exemplo, um sistema de desenvolvimento ERP SAP, um sistema de teste SAP Business Warehouse ou um sistema de produção de CRM SAP. Em implantações de Azure, dividir estas duas camadas entre as instalações e Azure não é suportado. Como resultado, um sistema SAP é implantado no local ou é implantado em Azure. Pode implantar os diferentes sistemas de uma paisagem SAP em Azure ou no local. Por exemplo, pode implantar os sistemas de desenvolvimento e teste de CRM SAP em Azure, mas implementar o sistema de produção de CRM SAP no local.
* **Premissas cruzadas**: Descreve um cenário em que os VMs são implantados numa subscrição do Azure que tem conectividade site-to-site, multisite ou Azure ExpressRoute entre os centros de dados no local e o Azure. Em documentação comum do Azure, este tipo de implantações também são descritos como cenários de premissas cruzadas. 

    A razão para a ligação é alargar os domínios no local, o Ative Directory e o DNS no local para a Azure. A paisagem no local é estendida aos ativos da Azure da subscrição. Com esta extensão, os VMs podem fazer parte do domínio no local. Os utilizadores de domínio do domínio no local podem aceder aos servidores e executar serviços nesses VMs, como os serviços DBMS. É possível a resolução de comunicação e nome entre os VM implantados no local e os VM implantados em Azure. Este cenário é o cenário mais comum em uso para implantar ativos SAP no Azure. Para mais informações, consulte [Planeamento e design para gateway VPN.](../../../vpn-gateway/vpn-gateway-about-vpngateways.md)

> [!NOTE]
> As instalações cruzadas dos sistemas SAP são onde as máquinas virtuais Azure que executam sistemas SAP são membros de um domínio no local e são suportadas para sistemas SAP de produção. As configurações de instalações cruzadas são suportadas para a implantação de peças ou para completar paisagens SAP em Azure. Mesmo executando a paisagem completa do SAP em Azure requer que esses VMs sejam parte de um domínio no local e diretório ativo/LDAP. 
>
> Em versões anteriores da documentação, foram mencionados cenários híbridos-TI. O termo *híbrido* está enraizado no facto de haver uma conectividade transversal entre as instalações e o Azure. Neste caso, híbrido também significa que os VMs em Azure fazem parte do Diretório Ativo no local.
>
>

Alguma documentação da Microsoft descreve cenários de premissas cruzadas de forma um pouco diferente, especialmente para configurações de alta disponibilidade do DBMS. No caso dos documentos relacionados com o SAP, o cenário de instalações cruzadas resume-se à conectividade site-to-site ou privada [ExpressRoute](https://azure.microsoft.com/services/expressroute/) e a uma paisagem SAP que é distribuída entre as instalações e o Azure.

## <a name="resources"></a>Recursos
Existem outros artigos disponíveis na carga de trabalho da SAP em Azure. Comece com [a carga de trabalho SAP em Azure: Comece](./get-started.md) e, em seguida, escolha a sua área de interesse.

As seguintes notas SAP estão relacionadas com a SAP on Azure no que diz respeito à área abrangida pelo presente documento.

| Número de nota | Título |
| --- | --- |
| [1928533] |Aplicações SAP no Azure: Produtos suportados e tipos de VM Azure |
| [2015553] |SAP no Microsoft Azure: Suporte pré-requisitos |
| [1999351] |Resolução de problemas melhorou a monitorização do Azure para o SAP |
| [2178632] |Principais métricas de monitorização do SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: Monitorização melhorada |
| [2191498] |SAP on Linux com Azure: Monitorização melhorada |
| [2039619] |Aplicações SAP no Microsoft Azure utilizando a base de dados Oracle: Produtos e versões suportados |
| [2233094] |DB6: Aplicações SAP no Azure utilizando o IBM DB2 para Linux, UNIX e Windows: Informações adicionais |
| [2243692] |Linux no Microsoft Azure (IaaS) VM: Problemas de licença SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalação e atualização |
| [2069760] |Instalação e atualização oracle Linux 7.x SAP |
| [1597355] |Recomendação de espaço de troca para Linux |
| [2171857] |Oracle Database 12c: Suporte ao sistema de ficheiros no Linux |
| [1114181] |Oracle Database 11g: Suporte ao sistema de ficheiros no Linux |


Para obter informações sobre todas as notas SAP para Linux, consulte a [comunidade SAP wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Precisa de um conhecimento de trabalho da arquitetura Microsoft Azure e de como as máquinas virtuais microsoft Azure são implantadas e operadas. Para mais informações, consulte [a documentação do Azure.](../../../index.yml)

Em geral, a instalação e configuração do Windows, Linux e DBMS são essencialmente as mesmas que qualquer máquina virtual ou máquina de metal nua que instale no local. Existem algumas decisões de implementação de arquitetura e gestão de sistemas que são diferentes quando se usa O Azure IaaS. Este documento explica as diferenças específicas de arquitetura e gestão do sistema para quando utilizar o Azure IaaS.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estrutura de armazenamento de um VM para implantações RDBMS
Para acompanhar este capítulo, leia e compreenda a informação apresentada em:

- [Azure Virtual Machines planejamento e implementação para SAP NetWeaver](./planning-guide.md)
- [Tipos de Armazenamento do Azure para a carga de trabalho SAP](./planning-guide-storage.md)
- [Qual o software SAP suportado para implementações do Azure](./sap-supported-product-on-azure.md)
- [Carga de trabalho SAP em cenários de máquinas virtuais do Azure suportados](./sap-planning-supported-configurations.md) 

Você precisa entender e saber sobre os diferentes VM-Series e as diferenças entre o armazenamento padrão e premium antes de ler este capítulo. 

Para o armazenamento do bloco Azure, a utilização de discos geridos Azure é altamente recomendada. Para mais detalhes sobre discos geridos Azure leia o artigo [Introdução a discos geridos para VMs Azure](../../managed-disks-overview.md).

Numa configuração básica, recomendamos geralmente uma estrutura de implantação onde o sistema operativo, dBMS e eventuais binários SAP estão separados dos ficheiros de base de dados. Alterando recomendações anteriores, recomendamos ter discos Azure separados para:

- O sistema operativo (VHD base ou OS VHD)
- Executáveis de sistemas de gestão de bases de dados
- Executáveis SAP como /usr/seiva

Uma configuração que separa estes componentes em três discos Azure diferentes pode resultar numa maior resiliência, uma vez que as gravações excessivas ou as escritas de despejo pelos executáveis DBMS ou SAP não estão a interferir com as quotas de disco do disco DE. 

Os ficheiros de dados e registos de transações/redo DBMS são armazenados em blocos suportados pelo Azure ou nos ficheiros Azure NetApp. São armazenados em discos separados e ligados como discos lógicos à imagem original do sistema operativo Azure VM. Para as implementações do Linux, são documentadas diferentes recomendações, especialmente para o SAP HANA. Leia o artigo [Azure Storage types for SAP workload](./planning-guide-storage.md) for the capabilities and the support of the different storage types for your scenario. 

Ao planear o layout do seu disco, encontre o melhor equilíbrio entre estes itens:

* O número de ficheiros de dados.
* O número de discos que contêm os ficheiros.
* As quotas IOPS de um único disco ou NFS partilham.
* A produção de dados por disco ou NFS partilham.
* O número de discos de dados adicionais possíveis por tamanho VM.
* O armazenamento geral ou a produção de rede que um VM pode fornecer.
* Os diferentes tipos de armazenamento Azure podem fornecer a latência.
* VM SLAs.

A Azure aplica uma quota de IOPS por disco de dados ou NFS. Estas quotas são diferentes para discos alojados nas diferentes soluções ou partilhas de blocos Azure. A latência I/O também é diferente entre estes diferentes tipos de armazenamento também. 

Cada um dos diferentes tipos de VM tem um número limitado de discos de dados que pode anexar. Outra restrição é que apenas certos tipos de VM podem usar, por exemplo, o armazenamento premium. Normalmente, decide utilizar um determinado tipo VM com base nos requisitos de CPU e memória. Também pode considerar os requisitos de IOPS, latência e produção de disco que normalmente são dimensionado com o número de discos ou o tipo de discos de armazenamento premium. O número de IOPS e a produção a atingir por cada disco pode ditar o tamanho do disco, especialmente com armazenamento premium.

> [!NOTE]
> Para implementações DBMS, recomendamos o armazenamento premium Azure, ultra disco ou ações NFS baseadas em Azure NetApp (exclusivamente para SAP HANA) para quaisquer ficheiros de dados, registos de transações ou redo. Não importa se quer implantar sistemas de produção ou não-produção.

> [!NOTE]
> Para beneficiar do [VM SLA único](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)da Azure, todos os discos que estão ligados devem ser o armazenamento premium Azure ou o tipo de disco Azure Ultra, que inclui o VHD base (armazenamento premium Azure).

> [!NOTE]
> O alojamento de ficheiros de base de dados principais, tais como ficheiros de dados e ficheiros de registo, de bases de dados SAP em hardware de armazenamento que está localizado em centros de dados de terceiros co-localizados adjacentes aos centros de dados Azure não é suportado. O armazenamento fornecido através de aparelhos de software alojados em VMs Azure também não é suportado para este caso de utilização. Para as cargas de trabalho do SAP DBMS, apenas o armazenamento representado como serviço nativo Azure é suportado para os ficheiros de registo de dados de dados de dados SAP em geral. Diferentes DBMS podem suportar diferentes tipos de armazenamento Azure. Para mais detalhes, consulte os [tipos de armazenamento Azure](./planning-guide-storage.md) para a carga de trabalho SAP

A colocação dos ficheiros de base de dados e dos ficheiros de registo e redo e o tipo de Armazenamento Azure que utiliza, é definida pelos requisitos de IOPS, latência e produção. Especificamente para o armazenamento premium Azure para obter IOPS suficiente, você pode ser forçado a usar vários discos ou usar um disco de armazenamento premium maior. Se utilizar vários discos, construa uma risca de software através dos discos que contenham os ficheiros de dados ou o registo e redo ficheiros. Nesses casos, o IOPS e o disco de produção SLAs dos discos de armazenamento premium subjacentes ou o IOPS máximo alcançável dos discos de armazenamento padrão são acumulados para o conjunto de listras resultantes.

Se o seu requisito de IOPS exceder o que um único VHD pode fornecer, equilibre o número de IOPS que são necessários para os ficheiros de base de dados em vários VHDs. A forma mais fácil de distribuir a carga do IOPS através dos discos é construir uma risca de software sobre os diferentes discos. Em seguida, coloque uma série de ficheiros de dados do DBMS SAP nas LUNs esculpidas na faixa de software. O número de discos na risca é impulsionado pelas exigências do IOPS, pelas exigências de produção de discos e pelas exigências de volume.


---
> ![Tiragem de armazenamento de janelas][Logo_Windows] Windows
>
> Recomendamos que utilize espaços de armazenamento do Windows para criar conjuntos de listras em vários VHDs Azure. Utilize pelo menos o Windows Server 2012 R2 ou Windows Server 2016.
>
> ![Striping de armazenamento Linux][Logo_Linux] Linux
>
> Apenas mdadm e Gestor de Volume Lógico (LVM) são suportados para construir um RAID de software em Linux. Para obter mais informações, consulte:
>
> - [Configure o software RAID no Linux](../../linux/configure-raid.md) utilizando o MDADM
> - [Configure a LVM num Linux VM em Azure](../../linux/configure-lvm.md) utilizando a LVM
>
>

---

Para o disco Azure Ultra, não é necessário despir-se, uma vez que pode definir iops e saída de disco independentemente do tamanho do disco.


> [!NOTE]
> Como o Azure Storage guarda três imagens dos VHDs, não faz sentido configurar uma redundância quando se risca. Só é necessário configurar a tiragem para que o I/Os seja distribuído pelos diferentes VHDs.
>

### <a name="managed-or-nonmanaged-disks"></a>Discos geridos ou não geridos
Uma conta de armazenamento Azure é uma construção administrativa e também um assunto de limitações. As limitações diferem entre as contas de armazenamento padrão e as contas de armazenamento premium. Para obter informações sobre capacidades e limitações, consulte [a escalabilidade do armazenamento Azure e os objetivos de desempenho](../../../storage/common/scalability-targets-standard-account.md).

Para armazenamento padrão, lembre-se que há um limite na conta de IOPS por armazenamento. Consulte a linha que contém **a Taxa total de pedido** no artigo [Azure Storage scalability e objetivos de desempenho](../../../storage/common/scalability-targets-standard-account.md). Há também um limite inicial no número de contas de armazenamento por subscrição do Azure. Balanço VHDs para a paisagem SAP maior em diferentes contas de armazenamento para evitar atingir os limites destas contas de armazenamento. Isto é um trabalho enfadonho quando se fala de algumas centenas de máquinas virtuais com mais de mil VHDs.

Não é recomendado o armazenamento padrão para implementações DBMS em conjunto com uma carga de trabalho SAP. Portanto, as referências e recomendações ao armazenamento padrão são limitadas a este [artigo](/archive/blogs/mast/configuring-azure-virtual-machines-for-optimal-storage-performance) curto

Para evitar o trabalho administrativo de planeamento e implementação de VHDs em diferentes contas de armazenamento Azure, a Microsoft introduziu [discos geridos Azure](https://azure.microsoft.com/services/managed-disks/) em 2017. Os discos geridos estão disponíveis para armazenamento padrão e armazenamento premium. As principais vantagens dos discos geridos em comparação com os discos não geridos são:

- Para discos geridos, o Azure distribui automaticamente os diferentes VHDs em diferentes contas de armazenamento no momento da implementação. Desta forma, os limites da conta de armazenamento para volume de dados, produção de I/O e IOPS não são atingidos.
- Utilizando discos geridos, o Azure Storage honra os conceitos de conjuntos de disponibilidade Azure. Se o VM fizer parte de um conjunto de disponibilidades Azure, o VHD base e o disco anexo de um VM são implantados em diferentes domínios de falha e atualização.


> [!IMPORTANT]
> Dadas as vantagens dos Discos Geridos Azure, recomendamos vivamente que utilize Discos Geridos Azure para as suas implementações DBMS e implementações SAP em geral.
>

Para converter de discos não geridos para discos geridos, consulte:

- [Converter uma máquina virtual Windows de discos não geridos para discos geridos](../../windows/convert-unmanaged-to-managed-disks.md).
- [Converter uma máquina virtual Linux de discos não geridos para discos geridos](../../linux/convert-unmanaged-to-managed-disks.md).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Caching para VMs e discos de dados
Quando monta discos em VMs, pode escolher se o tráfego de E/S entre o VM e os discos localizados no armazenamento Azure está em cache.

As seguintes recomendações pressupõem estas características de E/S para o DBMS normalizado:

- É principalmente uma carga de trabalho lida contra ficheiros de dados de uma base de dados. Estas leituras são fundamentais para o desempenho do sistema DBMS.
- Escrever contra os ficheiros de dados ocorre em rajadas baseadas em pontos de verificação ou num fluxo constante. Em média, ao longo de um dia, há menos escritos do que as leituras. Ao contrário das leituras dos ficheiros de dados, estas gravações são assíncronos e não realizam quaisquer transações de utilizadores.
- Quase não existem leituras do registo de transações ou ficheiros de redo. As exceções são grandes I/Os quando efetua cópias de segurança de registo de transações.
- A carga principal contra ficheiros de transação ou redo é escrita. Dependendo da natureza da carga de trabalho, pode ter I/Os tão pequeno como 4 KB ou, em outros casos, tamanhos de I/O de 1 MB ou mais.
- Todas as escritas devem ser persistidos no disco de uma forma fiável.

Para o armazenamento normal, os tipos de cache possíveis são:

* Nenhum
* Ler
* Leitura/Escrita

Para obter um desempenho consistente e determinístico, descreva o cacheing no armazenamento padrão para todos os discos que contenham ficheiros de dados relacionados com DBMS, ficheiros de registo e redo e espaço de mesa para **NENHUM**. O caching da base VHD pode permanecer com o padrão.

Para o armazenamento premium Azure, existem as seguintes opções de caching:

* Nenhum
* Ler
* Leitura/escrita
* Nenhum + Write Accelerator, que é apenas para VMs da série M de Azure
* Ler + Escrever Acelerador, que é apenas para VMs da série M de Azure

Para armazenamento premium, recomendamos que utilize **o Cache de Leitura para ficheiros** de dados da base de dados SAP e escolha Não cache para os discos de **ficheiros de log.s.**

Para as implementações da Série M, recomendamos que utilize o Acelerador de Escrita Azure para a sua implementação DBMS. Para mais detalhes, restrições e implementação do Acelerador de Escrita Azure, consulte [Ativar o Acelerador de Escrita](../../how-to-enable-write-accelerator.md).

Para ficheiros Ultra disk e Azure NetApp, não são oferecidas opções de cache.


### <a name="azure-nonpersistent-disks"></a>Discos não-activos Azure
Os VMs Azure oferecem discos não-activos após a implementação de um VM. No caso de um reboot VM, todos os conteúdos dessas unidades são eliminados. É um dado adquirido que os ficheiros de dados e o registo e redo ficheiros de bases de dados não devem, em circunstância alguma, ser localizados nessas unidades não apoiadas. Pode haver exceções para algumas bases de dados, onde estas unidades não-escutadas podem ser adequadas para espaços de mesa temporários e temporários. Evite utilizar esses discos para VMs da série A porque essas unidades não-aproveitadas são limitadas em produção com aquela família VM. 

Para obter mais informações, [consulte a unidade temporária em VMs do Windows em Azure](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines).

---
> ![Windows disco não-protegido][Logo_Windows] Windows
>
> Drive D em um Azure VM é uma unidade não-onstária, que é apoiada por alguns discos locais no nó computacional Azure. Por não ser realizado, quaisquer alterações feitas ao conteúdo na unidade D perdem-se quando o VM é reiniciado. As alterações incluem ficheiros que foram armazenados, diretórios que foram criados e aplicações que foram instaladas.
>
> ![Disco linuxnonpersisted][Logo_Linux] Linux
>
> Os VMs Linux Azure montam automaticamente uma unidade em /mnt/recurso que é uma unidade não persistida apoiada por discos locais no nó de computação Azure. Por não ser cindiado, quaisquer alterações feitas ao conteúdo em /mnt/recurso perdem-se quando o VM é reiniciado. As alterações incluem ficheiros que foram armazenados, diretórios que foram criados e aplicações que foram instaladas.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resiliência do Armazenamento Microsoft Azure
O Microsoft Azure Storage armazena o VHD base, com discos ou bolhas ligados, em pelo menos três nós de armazenamento separados. Este tipo de armazenamento é chamado armazenamento localmente redundante (LRS). O LRS é o padrão para todos os tipos de armazenamento em Azure.

Há outros métodos de redundância. Para obter mais informações, consulte [a replicação do Azure Storage](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
> O armazenamento premium Azure, o disco Ultra e o Azure NetApp Files (exclusivamente para SAP HANA) são o tipo de armazenamento recomendado para VMs DBMS e discos que armazenam bases de dados e ficheiros de registo e redo. O único método de redundância disponível para estes tipos de armazenamento é o LRS. Como resultado, é necessário configurar métodos de base de dados para permitir a replicação de dados de base de dados noutra região do Azure ou zona de disponibilidade. Os métodos de base de dados incluem SQL Server Always On, Oracle Data Guard e HANA System Replication.


> [!NOTE]
> Para as implementações de DBMS, a utilização de armazenamento geo-redundante (GRS) não é recomendada para armazenamento padrão. GRS afeta severamente o desempenho e não honra a ordem de escrita em diferentes VHDs que estão ligados a um VM. Não honrar a ordem de escrita em diferentes VHDs potencialmente leva a bases de dados inconsistentes no lado alvo da replicação. Esta situação ocorre se os ficheiros de base de dados e de registo e redo estiverem espalhados por vários VHDs, como geralmente acontece, no lado VM da fonte.



## <a name="vm-node-resiliency"></a>Resiliência do nó VM
A Azure oferece vários SLAs diferentes para VMs. Para mais informações, consulte a mais recente versão do [SLA para Máquinas Virtuais.](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) Como a camada DBMS é fundamental para a disponibilidade num sistema SAP, é necessário compreender os conjuntos de disponibilidade, as Zonas de Disponibilidade e os eventos de manutenção. Para obter mais informações sobre estes conceitos, consulte [Gerir a disponibilidade de máquinas virtuais do Windows em Azure](../../windows/manage-availability.md) e Gerir a disponibilidade de [máquinas virtuais Linux em Azure.](../../linux/manage-availability.md)

A recomendação mínima para os cenários de DBMS de produção com uma carga de trabalho SAP é:

- Coloque dois VMs numa disponibilidade separada definida na mesma região de Azure.
- Executar estes dois VMs na mesma rede virtual Azure e ter NICs ligados a partir das mesmas sub-redes.
- Utilize métodos de base de dados para manter um estado de espera quente com o segundo VM. Os métodos podem ser SQL Server Always On, Oracle Data Guard ou HANA System Replication.

Também pode implantar um terceiro VM em outra região de Azure e usar os mesmos métodos de base de dados para fornecer uma réplica assíncrona em outra região de Azure.

Para obter informações sobre como configurar conjuntos de disponibilidade do Azure, consulte [este tutorial](../../windows/tutorial-availability-sets.md).



## <a name="azure-network-considerations"></a>Considerações da rede Azure
Em implementações SAP em larga escala, utilize a planta do [Azure Virtual Datacenter](/azure/architecture/vdc/networking-virtual-datacenter). Use-o para a sua configuração de rede virtual e permissões e atribuições de funções para diferentes partes da sua organização.

Estas boas práticas são o resultado de centenas de implementações de clientes:

- As redes virtuais em que a aplicação SAP é implementada não têm acesso à internet.
- Os VMs de base de dados funcionam na mesma rede virtual que a camada de aplicação, separada numa sub-rede diferente da camada de aplicação SAP.
- Os VMs dentro da rede virtual têm uma alocação estática do endereço IP privado. Para obter mais informações, consulte [os tipos de endereços IP e os métodos de atribuição em Azure](../../../virtual-network/public-ip-addresses.md).
- As restrições de encaminhamento de e para os VMs DBMS *não* são definidas com firewalls instaladas nos VMs DBMS locais. Em vez disso, o encaminhamento de tráfego é definido com [grupos de segurança de rede (NSGs)](../../../virtual-network/security-overview.md).
- Para separar e isolar o tráfego para o DBMS VM, atribua diferentes NICs ao VM. Cada NIC obtém um endereço IP diferente, e cada NIC é atribuído a uma sub-rede de rede virtual diferente. Cada sub-rede tem regras NSG diferentes. O isolamento ou separação do tráfego de rede é uma medida para o encaminhamento. Não é usado para definir quotas para a produção de rede.

> [!NOTE]
> Atribuir endereços IP estáticos através do Azure significa atribuí-los a NICs virtuais individuais. Não atribua endereços IP estáticos dentro do so do hóspede a um NIC virtual. Alguns serviços da Azure, como o Azure Backup, baseiam-se no facto de que pelo menos o NIC virtual primário está definido para DHCP e não para endereços IP estáticos. Para obter mais informações, consulte [a cópia de segurança da máquina virtual Troubleshoot Azure](../../../backup/backup-azure-vms-troubleshoot.md#networking). Para atribuir vários endereços IP estáticos a um VM, atribua vários NICs virtuais a um VM.
>


> [!WARNING]
> Configurar [aparelhos virtuais](https://azure.microsoft.com/solutions/network-appliances/) de rede na via de comunicação entre a aplicação SAP e a camada DBMS de um sistema SAP NetWeaver-, Hybris ou S/4HANA baseado em SAP não é suportado. Esta restrição é por razões de funcionalidade e desempenho. O caminho de comunicação entre a camada de aplicação SAP e a camada DBMS deve ser direto. A restrição não inclui [as regras do Grupo de Segurança de Aplicações (ASG) e NSG](../../../virtual-network/security-overview.md) se essas regras ASG e NSG permitirem uma via de comunicação direta. 
>
> Outros cenários em que os aparelhos virtuais da rede não são suportados estão em:
>
> * Caminhos de comunicação entre VMs Azure que representam os nós de cluster Linux Pacemaker e dispositivos SBD descritos em [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP](./high-availability-guide-suse.md).
> * As vias de comunicação entre O VMs do Azure e o Servidor de Ficheiros Scale-Out do Windows Server (SOFS) configuradas como descrito no [Cluster uma instância SAP ASCS/SCS num cluster de falha do Windows utilizando uma partilha de ficheiros no Azure](./sap-high-availability-guide-wsfc-file-share.md). 
>
> Os aparelhos virtuais de rede em vias de comunicação podem facilmente duplicar a latência da rede entre dois parceiros de comunicação. Também podem restringir a produção em caminhos críticos entre a camada de aplicação SAP e a camada DBMS. Em alguns cenários de clientes, os aparelhos virtuais de rede podem fazer com que os clusters Pacemaker Linux falhem. Estes são casos em que as comunicações entre os nós do cluster Linux Pacemaker comunicam ao seu dispositivo SBD através de um aparelho virtual de rede.
>

> [!IMPORTANT]
> Outro design que *não* é suportado é a segregação da camada de aplicação SAP e a camada DBMS em diferentes redes virtuais Azure que não são [espreitadas umas](../../../virtual-network/virtual-network-peering-overview.md) com as outras. Recomendamos que se segrega a camada de aplicação SAP e a camada DBMS utilizando sub-redes dentro de uma rede virtual Azure em vez de utilizar diferentes redes virtuais Azure. 
>
> Se decidir não seguir a recomendação e, em vez disso, segregar as duas camadas em diferentes redes virtuais, as duas redes virtuais devem ser [espreitadas.](../../../virtual-network/virtual-network-peering-overview.md) 
>
> Esteja ciente de que o tráfego de rede entre duas redes virtuais Azure [está](../../../virtual-network/virtual-network-peering-overview.md) sujeito a custos de transferência. O enorme volume de dados que consiste em muitos terabytes é trocado entre a camada de aplicação SAP e a camada DBMS. Pode acumular custos substanciais se a camada de aplicação SAP e a camada DBMS estiverem segregadas entre duas redes virtuais Azure.

Utilize dois VMs para a sua implantação DBMS de produção dentro de um conjunto de disponibilidade azure ou entre duas Zonas de Disponibilidade Azure. Utilize também o encaminhamento separado para a camada de aplicação SAP e o tráfego de gestão e operações para os dois VMs DBMS. Veja a imagem seguinte:

![Diagrama de dois VMs em duas sub-redes](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Use o balançador de carga Azure para redirecionar o tráfego
A utilização de endereços IP virtuais privados utilizados em funcionalidades como SQL Server Always On ou HANA System Replication requer a configuração de um equilibrador de carga Azure. O equilibrador de carga utiliza portas de sonda para determinar o nó DBMS ativo e encaminhar o tráfego exclusivamente para aquele nó de base de dados ativo. 

Se houver uma falha no nó de base de dados, não há necessidade da aplicação SAP para reconfigurar. Em vez disso, as arquiteturas de aplicações SAP mais comuns reconectam-se contra o endereço IP virtual privado. Enquanto isso, o equilibrador de carga reage ao failover do nó redirecionando o tráfego contra o endereço IP virtual privado para o segundo nó.

O Azure oferece dois [SKUs de balançadores de carga diferentes:](../../../load-balancer/load-balancer-overview.md)um SKU básico e um SKU padrão. Com base nas vantagens de configuração e funcionalidade, deve utilizar o SKU Standard do equilibrador de carga Azure. Uma das grandes vantagens da versão Standard do equilibrador de carga é que o tráfego de dados não é encaminhado através do próprio equilibrador de carga.

Um exemplo de como pode configurar um equilibrador de carga interno pode ser encontrado no artigo [Tutorial: Configurar manualmente um grupo de disponibilidade de servidor SQL em Azure Virtual Machines](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/availability-group-manually-configure-tutorial#create-an-azure-load-balancer)

> [!NOTE]
> Existem diferenças de comportamento do SKU básico e padrão relacionado com o acesso de endereços IP públicos. A forma de contornar as restrições do SKU padrão para aceder a endereços IP públicos é descrita no documento [Conectividade de ponto final público para máquinas virtuais usando O Balançador de Carga Padrão Azure em cenários de alta disponibilidade SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)


### <a name="azure-accelerated-networking"></a>Rede Acelerada Azure
Para reduzir ainda mais a latência da rede entre os VMs Azure, recomendamos que escolha [a Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Utilize-o quando implementar VMs Azure para uma carga de trabalho SAP, especialmente para a camada de aplicação SAP e para a camada SAP DBMS.

> [!NOTE]
> Nem todos os tipos de VM suportam rede acelerada. O artigo anterior lista os tipos de VM que suportam a rede acelerada.
>

---
> ![Rede acelerada do Windows][Logo_Windows] Windows
>
> Para aprender a implementar VMs com rede acelerada para windows, consulte [criar uma máquina virtual Windows com Rede Acelerada](../../../virtual-network/create-vm-accelerated-networking-powershell.md).
>
> ![Rede acelerada de Linux][Logo_Linux] Linux
>
> Para obter mais informações sobre a distribuição do Linux, consulte [criar uma máquina virtual Linux com Rede Acelerada.](../../../virtual-network/create-vm-accelerated-networking-cli.md)
>
>

---

> [!NOTE]
> No caso da SUSE, Red Hat e Oracle Linux, a Rede Acelerada é suportada com lançamentos recentes. Versões mais antigas como SLES 12 SP2 ou RHEL 7.2 não suportam Azure Accelerated Networking.
>


## <a name="deployment-of-host-monitoring"></a>Implantação da monitorização do hospedeiro
Para a utilização de aplicações SAP em máquinas virtuais Azure, o SAP requer a capacidade de obter dados de monitorização do hospedeiro dos anfitriões físicos que executam as máquinas virtuais Azure. É necessário um nível específico de correção do Agente anfitrião SAP que permita esta capacidade no SAPOSCOL e no Agente anfitrião SAP. O nível exato do patch está documentado na Nota [SAP 1409604].

Para obter mais informações sobre a implantação de componentes que entregam dados de anfitrião ao Agente anfitrião SAPOSCOL e SAP e a gestão do ciclo de vida desses componentes, consulte o [guia de implantação][deployment-guide].


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre um DBMS em particular, consulte:

- [Implementação em SQL Server do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_sqlserver.md)
- [Implementação em Oracle do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_oracle.md)
- [Ibm DB2 Azure Virtual Machines DBMS implantação para carga de trabalho SAP](dbms_guide_ibm.md)
- [Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_sapase.md)
- [Implementação de SAP maxDB, Live Cache e Servidor de Conteúdo no Azure](dbms_guide_maxdb.md)
- [Manual de operações do SAP HANA no Azure](hana-vm-operations.md)
- [SAP HANA alta disponibilidade para máquinas virtuais Azure](sap-hana-availability-overview.md)
- [Guia de backup para SAP HANA em máquinas virtuais Azure](sap-hana-backup-guide.md)