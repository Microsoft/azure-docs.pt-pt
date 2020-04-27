---
title: Considerações para a implantação de DBMS de Máquinas Virtuais Azure para carga de trabalho SAP [ Microsoft Docs
description: Considerações para a implantação de DBMS de Máquinas Virtuais Azure para carga de trabalho SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a852ddc68a6f51e677e5ff2e641ada25f4bf0105
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "70101370"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Considerações para a implantação de DBMS de Máquinas Virtuais Azure para carga de trabalho SAP
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


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Este guia faz parte da documentação sobre como implementar e implementar software SAP no Microsoft Azure. Antes de ler este guia, leia o guia de [Planeamento e Implementação.][planning-guide] Este documento abrange os aspetos genéricos de implantação de sistemas DBMS relacionados com o SAP em máquinas virtuais do Microsoft Azure (VMs), utilizando a infraestrutura Azure como um serviço (IaaS) capacidades.

O documento complementa a documentação de instalação SAP e as Notas SAP, que representam os principais recursos para instalações e implementações de software SAP em determinadas plataformas.

Neste documento são introduzidas considerações sobre a execução de sistemas DBMS relacionados com o SAP em VMs Azure. Existem poucas referências a sistemas DBMS específicos neste capítulo. Em vez disso, os sistemas DBMS específicos são tratados dentro deste documento, após este documento.

## <a name="definitions"></a>Definições
Ao longo do documento, estes termos são utilizados:

* **IaaS**: Infraestrutura como serviço.
* **PaaS**: Plataforma como serviço.
* **SaaS**: Software como serviço.
* **Componente SAP**: Uma aplicação SAP individual, como erp central component (ECC), Business Warehouse (BW), Solution Manager ou Enterprise Portal (EP). Os componentes SAP podem basear-se nas tecnologias tradicionais ABAP ou Java ou numa aplicação não baseada em NetWeaver, como Objetos Empresariais.
* **Ambiente SAP**: Um ou mais componentes SAP agrupados logicamente para desempenhar uma função de negócio como desenvolvimento, garantia de qualidade, formação, recuperação de desastres ou produção.
* **Paisagem SAP**: Este termo refere-se a todos os ativos SAP na paisagem de TI de um cliente. A paisagem SAP inclui todos os ambientes de produção e não produção.
* **Sistema SAP**: A combinação de uma camada DBMS e uma camada de aplicação de, por exemplo, um sistema de desenvolvimento ERP SAP, um sistema de teste SAP Business Warehouse ou um sistema de produção sAP CRM. Em destacamentos azure, dividir estas duas camadas entre as instalações e Azure não é suportado. Como resultado, um sistema SAP é implantado no local ou é implantado em Azure. Pode implantar os diferentes sistemas de uma paisagem SAP em Azure ou no local. Por exemplo, poderia implantar os sistemas de desenvolvimento e teste de CRM SAP em Azure, mas implementar o sistema de produção de CRM SAP no local.
* **Cross-premises**: Descreve um cenário em que os VMs são implantados para uma subscrição Azure que tem conectividade local-a-local, multisite ou Azure ExpressRoute entre os centros de dados no local e o Azure. Na documentação comum do Azure, este tipo de implantações também são descritos como cenários transversais. 

    A razão da ligação é o alargamento dos domínios no local, do Diretório Ativo no local e do DNS no local para o Azure. A paisagem no local é alargada aos ativos do Azure da subscrição. Com esta extensão, os VMs podem fazer parte do domínio no local. Os utilizadores de domínio do domínio no local podem aceder aos servidores e executar serviços nesses VMs, como serviços DBMS. A resolução de comunicação e nome entre VMs implantados no local e VMs implantados em Azure é possível. Este cenário é o cenário mais comum em uso para implantar ativos SAP no Azure. Para mais informações, consulte [Planeamento e Design para gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> As implantações transfronteiriças de sistemas SAP são onde as máquinas virtuais Azure que executam sistemas SAP são membros de um domínio no local e são suportadas para sistemas SAP de produção. As configurações transversais são suportadas para implantar peças ou paisagens Completas de SAP em Azure. Mesmo executando a paisagem completa do SAP em Azure exige que esses VMs façam parte de um domínio no local e Diretório Ativo/LDAP. 
>
> Em versões anteriores da documentação, foram mencionados cenários híbridos-IT. O termo *híbrido* está enraizado no facto de haver uma conectividade transversal entre as instalações e o Azure. Neste caso, a híbrida também significa que os VMs em Azure fazem parte do Diretório Ativo no local.
>
>

Alguma documentação da Microsoft descreve cenários de premissas cruzadas de forma um pouco diferente, especialmente para configurações de alta disponibilidade de DBMS. No caso dos documentos relacionados com o SAP, o cenário transversal resume-se à conectividade local-a-local ou privada [ExpressRoute](https://azure.microsoft.com/services/expressroute/) e a uma paisagem SAP que é distribuída entre as instalações e o Azure.

## <a name="resources"></a>Recursos
Existem outros artigos disponíveis sobre carga de trabalho SAP no Azure. Comece com [a carga de trabalho sap em Azure: Comece](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) e escolha a sua área de interesse.

As seguintes notas SAP estão relacionadas com o SAP em Azure no que diz respeito à área abrangida pelo presente documento.

| Número de nota | Título |
| --- | --- |
| [1928533] |Aplicações SAP em Azure: Produtos suportados e tipos de VM Azure |
| [2015553] |SAP no Microsoft Azure: Apoiar pré-requisitos |
| [1999351] |Resolução de problemas reforçou a monitorização do Azure para o SAP |
| [2178632] |Principais métricas de monitorização para SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: Monitorização melhorada |
| [2191498] |SAP em Linux com Azure: Monitorização melhorada |
| [2039619] |Aplicações SAP no Microsoft Azure utilizando a base de dados Oracle: Produtos e versões suportados |
| [2233094] |DB6: Aplicações SAP no Azure utilizando O IBM DB2 para Linux, UNIX e Windows: Informações adicionais |
| [2243692] |Linux no Microsoft Azure (IaaS) VM: Problemas de licença SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalação e upgrade |
| [2069760] |Instalação e atualização e upgrade oracle Linux 7.x SAP |
| [1597355] |Recomendação de espaço de troca para Linux |
| [2171857] |Oracle Database 12c: Suporte do sistema de ficheiros em Linux |
| [1114181] |Oracle Database 11g: Suporte do sistema de ficheiros em Linux |


Para obter informações sobre todas as notas SAP para Linux, consulte a [comunidade SAP wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Você precisa de um conhecimento de trabalho da arquitetura Microsoft Azure e de como as máquinas virtuais do Microsoft Azure são implantadas e operadas. Para mais informações, consulte a [documentação do Azure.](https://docs.microsoft.com/azure/)

Em geral, a instalação e configuração do Windows, Linux e DBMS são essencialmente as mesmas que qualquer máquina virtual ou máquina de metal nu que instale no local. Existem algumas decisões de implementação de arquitetura e gestão de sistemas que são diferentes quando se utiliza o Azure IaaS. Este documento explica as diferenças específicas de gestão arquitetónica e do sistema para estar preparado quando utilizar o Azure IaaS.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estrutura de armazenamento de um VM para implantações RDBMS
Para acompanhar este capítulo, leia e compreenda as informações apresentadas [neste capítulo][deployment-guide-3] do Guia de [Implantação.][deployment-guide] Você precisa entender e conhecer sobre as diferentes Séries VM e as diferenças entre armazenamento padrão e premium antes de ler este capítulo. 

Para saber sobre o Armazenamento Azure para VMs Azure, consulte:

- [Introdução aos discos geridos para VMs do Windows Azure](../../windows/managed-disks-overview.md).
- [Introdução aos discos geridos para VMs Azure Linux](../../linux/managed-disks-overview.md).

Numa configuração básica, recomendamos geralmente uma estrutura de implementação onde o sistema operativo, dbmS e eventuais binários SAP estão separados dos ficheiros de base de dados. Recomendamos que os sistemas SAP que funcionam em máquinas virtuais Azure tenham o VHD base, ou disco, instalado com o sistema operativo, executáveis do sistema de gestão de bases de dados e executáveis sAP. 

Os dados dbms e os ficheiros de registo são armazenados em armazenamento padrão ou armazenamento premium. São armazenados em discos separados e anexados como discos lógicos à imagem original do sistema operativo Azure VM. Para as implementações do Linux, estão documentadas diferentes recomendações, especialmente para o SAP HANA.

Quando planeia o layout do disco, encontre o melhor equilíbrio entre estes itens:

* O número de ficheiros de dados.
* O número de discos que contêm os ficheiros.
* As quotas iops de um único disco.
* A entrada de dados por disco.
* O número de discos de dados adicionais possíveis por tamanho VM.
* O armazenamento global que um VM pode fornecer.
* A latência que diferentes tipos de Armazenamento Azure podem fornecer.
* VM SLAs.

O Azure impõe uma quota IOPS por disco de dados. Estas quotas são diferentes para discos alojados em armazenamento padrão e armazenamento premium. A latência de I/O também é diferente entre os dois tipos de armazenamento. O armazenamento premium proporciona uma melhor latência de E/S. 

Cada um dos diferentes tipos de VM tem um número limitado de discos de dados que pode anexar. Outra restrição é que apenas certos tipos de VM podem usar armazenamento premium. Normalmente, decide utilizar um certo tipo de VM com base em CPU e requisitos de memória. Também pode considerar os requisitos de iops, latência e entrada de disco que normalmente são dimensionados com o número de discos ou o tipo de discos de armazenamento premium. O número de IOPS e a entrada a obter por cada disco podem ditar o tamanho do disco, especialmente com armazenamento premium.

> [!NOTE]
> Para implementações de DBMS, recomendamos a utilização de armazenamento premium para quaisquer ficheiros de dados, transações ou redo. Não importa se quer implantar sistemas de produção ou não produção.

> [!NOTE]
> Para beneficiar do único [VM SLA único](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)da Azure, todos os discos que estão ligados devem ser do tipo de armazenamento premium, que inclui o VHD base.

> [!NOTE]
> Não é suportado o alojamento de ficheiros principais da base de dados, como dados e ficheiros de registo, de bases de dados SAP sobre hardware de armazenamento localizados em centros de dados de terceiros co-localizados adjacentes aos centros de dados do Azure. Para cargas de trabalho SAP, apenas o armazenamento que é representado como serviço azure nativo é suportado para os ficheiros de registo de dados e transações das bases de dados SAP.

A colocação dos ficheiros de base de dados e dos ficheiros de registo e redo e o tipo de Armazenamento Azure que utiliza é definido pelos requisitos de IOPS, latência e de entrada. Para ter IOPS suficientes, pode ser obrigado a usar vários discos ou a utilizar um disco de armazenamento premium maior. Se utilizar vários discos, construa uma faixa de software nos discos que contenham os ficheiros de dados ou o registo e refazer ficheiros. Nesses casos, os IOPS e o produto de disco SLAs dos discos de armazenamento premium subjacentes ou os IOPS máximos de discos de armazenamento padrão são acumulados para o conjunto de listras resultante.

Como já foi referido, se o seu requisito iOPS exceder o que um único VHD pode fornecer, equilibre o número de IOPS que são necessários para os ficheiros de base de dados em vários VHDs. A forma mais fácil de distribuir a carga IOPS através dos discos é construir uma faixa de software sobre os diferentes discos. Em seguida, coloque uma série de ficheiros de dados do SAP DBMS nas LUNs esculpidas na faixa de software. O número de discos na risca é impulsionado pelas exigências dos IOPs, pelas exigências de entrada em disco e pelas exigências de volume.


---
> ![Windows][Logo_Windows] Windows
>
> Recomendamos que utilize espaços de armazenamento do Windows para criar conjuntos de riscas em vários VHDs Azure. Utilize pelo menos o Windows Server 2012 R2 ou windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Apenas o MDADM e o Logical Volume Manager (LVM) são suportados para construir um software RAID no Linux. Para obter mais informações, consulte:
>
> - [Configure o software RAID no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) usando o MDADM
> - [Configure LVM em um Linux VM em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) usando LVM
>
>

---

> [!NOTE]
> Como o Azure Storage guarda três imagens dos VHDs, não faz sentido configurar uma redundância quando se risca. Basta configurar as tiras para que o I/Os seja distribuído pelos diferentes VHDs.
>

### <a name="managed-or-nonmanaged-disks"></a>Discos geridos ou não geridos
Uma conta de armazenamento Azure é uma construção administrativa e também um tema de limitações. As limitações diferem entre as contas de armazenamento padrão e as contas de armazenamento premium. Para obter informações sobre capacidades e limitações, consulte [a escalabilidade e os objetivos](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)de desempenho do Azure Storage .

Para armazenamento padrão, lembre-se que há um limite no IOPS por conta de armazenamento. Consulte a linha que contém **a Taxa Total de Pedidos** no artigo Metas de escalabilidade e desempenho do Armazenamento [Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Há também um limite inicial para o número de contas de armazenamento por subscrição do Azure. Balance VHDs para a maior paisagem SAP através de diferentes contas de armazenamento para evitar atingir os limites destas contas de armazenamento. Isto é um trabalho tedioso quando se fala de algumas centenas de máquinas virtuais com mais de mil VHDs.

Porque a utilização de armazenamento padrão para implementações de DBMS em conjunto com uma carga de trabalho SAP não é recomendada, referências e recomendações para armazenamento padrão são limitadas a este [pequeno artigo](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Para evitar o trabalho administrativo de planeamento e implementação de VHDs em diferentes contas de armazenamento do Azure, a Microsoft introduziu [o Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) em 2017. Os discos geridos estão disponíveis para armazenamento padrão e armazenamento premium. As principais vantagens dos discos geridos em comparação com os discos não geridos são:

- Para discos geridos, o Azure distribui automaticamente os diferentes VHDs através de diferentes contas de armazenamento no momento da implementação. Desta forma, os limites da conta de armazenamento para o volume de dados, a entrada em I/S e o IOPS não são atingidos.
- Utilizando discos geridos, o Azure Storage homenageia os conceitos de conjuntos de disponibilidade do Azure. Se o VM fizer parte de um conjunto de disponibilidade seletiva, o VHD base e o disco anexado de um VM são implantados em diferentes domínios de avaria e atualização.


> [!IMPORTANT]
> Dadas as vantagens dos Discos Geridos azure, recomendamos que utilize discos geridos azure para as suas implementações DBMS e implementações SAP em geral.
>

Para converter de discos não geridos para geridos, consulte:

- [Converter uma máquina virtual do Windows a partir de discos não geridos para discos geridos](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Converter uma máquina virtual Linux de discos não geridos para discos geridos](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Caching para VMs e discos de dados
Quando monta discos em VMs, pode escolher se o tráfego de I/S entre o VM e os discos localizados no armazenamento Azure está em cache. O armazenamento standard e premium utiliza duas tecnologias diferentes para este tipo de cache.

As seguintes recomendações assumem estas características de E/S para DBMS padrão:

- É principalmente uma carga de trabalho lida contra ficheiros de dados de uma base de dados. Estas leituras são críticas para o sistema DBMS.
- A escrita contra os ficheiros de dados ocorre em explosões com base em pontos de verificação ou num fluxo constante. Média ao longo de um dia, há menos escritos do que as leituras. Em oposição às leituras dos ficheiros de dados, estas escritas são assíncronas e não mantêm nenhuma transação de utilizador.
- Quase não há leituras do registo de transações ou dos ficheiros de refazer. As exceções são grandes I/Os quando executa cópias de segurança de registo de transações.
- A carga principal contra transações ou ficheiros de registo de refazer é escrita. Dependendo da natureza da carga de trabalho, pode ter I/Os tão pequeno como 4 KB ou, em outros casos, tamanhos de I/O de 1 MB ou mais.
- Todas as escritas devem ser persistidas no disco de uma forma fiável.

Para armazenamento padrão, os tipos de cache possíveis são:

* Nenhuma
* Leitura
* Leitura/Escrita

Para obter um desempenho consistente e determinista, delineie o cache no armazenamento padrão para todos os discos que contenham ficheiros de dados relacionados com DBMS, ficheiros de registo e redo e espaço de mesa para **NENHUM**. O cache da base VHD pode permanecer com o padrão.

Para armazenamento premium, existem as seguintes opções de cache:

* Nenhuma
* Leitura
* Leitura/escrita
* Nenhum + Write Accelerator, que é apenas para VMs da Série M Azure
* Leia + Write Accelerator, que é apenas para VMs da Série M Azure

Para armazenamento premium, recomendamos que utilize **O artigo Ler para ficheiros** de dados da base de dados SAP e escolher Não cache para os discos de **ficheiros de registo(s)**.

Para implementações da Série M, recomendamos que utilize o Acelerador De Escrita Azure para a sua implementação dbmS. Para mais detalhes, restrições e implantação do Acelerador de Escrita Azure, consulte [Enable Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Discos azure não persistentes
Os VMs azure oferecem discos não persistentes após a implantação de um VM. No caso de um reboot vm, todo o conteúdo desses discos é eliminado. É dado que os ficheiros de dados e os ficheiros de registo e redoção de bases de dados não devem, em circunstância alguma, ser localizados nessas unidades não persistiu. Pode haver exceções para algumas bases de dados, onde estas unidades não persistiu podem ser adequadas para espaços de mesa temporários e temporários. Evite utilizar esses discos para VMs série A porque esses discos não persistidos são limitados na entrada com essa família VM. 

Para mais informações, consulte [Compreender a unidade temporária de VMs do Windows em Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> Drive D em um VM Azure é uma unidade não persistiu, que é apoiada por alguns discos locais no nó de computação Azure. Por não ser persistiu, quaisquer alterações feitas ao conteúdo na unidade D perdem-se quando o VM é reiniciado. As alterações incluem ficheiros que foram armazenados, diretórios que foram criados e aplicações que foram instaladas.
>
> ![Linux][Logo_Linux] Linux
>
> Os VMs Linux Azure montam automaticamente uma unidade a /mnt/recurso que é uma unidade não persistiu apoiada por discos locais no nó de computação Azure. Por não persistir, quaisquer alterações feitas ao conteúdo em /mnt/recurso perdem-se quando o VM é reiniciado. As alterações incluem ficheiros que foram armazenados, diretórios que foram criados e aplicações que foram instaladas.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resiliência do Armazenamento Microsoft Azure
O Microsoft Azure Storage armazena a base VHD, com OS e discos ou bolhas anexados, em pelo menos três nós de armazenamento separados. Este tipo de armazenamento é chamado armazenamento localmente redundante (LRS). O LRS é o padrão para todos os tipos de armazenamento em Azure.

Há outros métodos de despedimento. Para mais informações, consulte a [replicação do Armazenamento Azure.](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

> [!NOTE]
>O armazenamento premium é o tipo recomendado de armazenamento para VMs DBMS e discos que armazenam base de dados e registam e refazem ficheiros. O único método de despedimento disponível para armazenamento premium é o LRS. Como resultado, é necessário configurar métodos de base de dados para permitir a replicação de dados de base de dados noutra região do Azure ou zona de disponibilidade. Os métodos de base de dados incluem o Servidor SQL Always On, o Oracle Data Guard e a Replicação do Sistema HANA.


> [!NOTE]
> Para as implementações de DBMS, o uso de armazenamento geo-redundante (GRS) não é recomendado para armazenamento padrão. GrS afeta severamente o desempenho e não honra a ordem de escrita em diferentes VHDs que estão ligados a um VM. Não honrar a ordem de escrita em diferentes VHDs potencialmente leva a bases de dados inconsistentes no lado alvo da replicação. Esta situação ocorre se os ficheiros de base de dados e registos e redo são distribuídos por vários VHDs, como é geralmente o caso, no lado vM de origem.



## <a name="vm-node-resiliency"></a>Resiliência do nó VM
O Azure oferece vários SLAs diferentes para VMs. Para mais informações, consulte o mais recente lançamento de [SLA para Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Como a camada DBMS é geralmente fundamental para a disponibilidade num sistema SAP, você precisa entender conjuntos de disponibilidade, zonas de disponibilidade e eventos de manutenção. Para obter mais informações sobre estes conceitos, consulte [Gerir a disponibilidade de máquinas virtuais Windows em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e Gerir a disponibilidade de [máquinas virtuais Linux em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

A recomendação mínima para cenários de DBMS de produção com uma carga de trabalho SAP é:

- Coloque dois VMs numa disponibilidade separada na mesma região de Azure.
- Execute estes dois VMs na mesma rede virtual Azure e tenha NICs ligados das mesmas subredes.
- Utilize métodos de base de dados para manter um suporte quente com o segundo VM. Os métodos podem ser O Servidor SQL Sempre Ligado, Guarda de Dados Oracle ou Replicação do Sistema HANA.

Você também pode implementar um terceiro VM em outra região de Azure e usar os mesmos métodos de base de dados para fornecer uma réplica assíncrona em outra região de Azure.

Para obter informações sobre como configurar conjuntos de disponibilidade do Azure, consulte [este tutorial](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considerações de rede Azure
Em implementações SAP em larga escala, utilize a planta do Centro de [Dados Virtuais Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Use-o para a configuração da rede virtual e permissões e atribuições de papéis para diferentes partes da sua organização.

Estas boas práticas são o resultado de centenas de implementações de clientes:

- As redes virtuais em que a aplicação SAP é implementada não têm acesso à internet.
- Os VMs da base de dados funcionam na mesma rede virtual que a camada de aplicação.
- Os VMs dentro da rede virtual têm uma alocação estática do endereço IP privado. Para mais informações, consulte os tipos de [endereços IP e os métodos](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)de atribuição no Azure .
- As restrições de encaminhamento de e para os VMs DBMS *não* são definidas com firewalls instalados nos VMs DBMS locais. Em vez disso, o encaminhamento de tráfego é definido com grupos de segurança de [rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Para separar e isolar o tráfego para o VM DBMS, atribua diferentes NICs ao VM. Cada NIC obtém um endereço IP diferente, e cada NIC é atribuído a uma subnet de rede virtual diferente. Cada subnet tem regras diferentes de NSG. O isolamento ou a separação do tráfego de rede é uma medida para o encaminhamento. Não é usado para definir quotas para a produção da rede.

> [!NOTE]
> Atribuir endereços IP estáticos através do Azure significa atribuí-los a NICs virtuais individuais. Não designe endereços IP estáticos dentro do so do hóspede a um NIC virtual. Alguns serviços azure como o Azure Backup dependem do facto de que pelo menos o NIC virtual primário está definido para dHCP e não para endereços IP estáticos. Para mais informações, consulte o backup da [máquina virtual Troubleshoot Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Para atribuir vários endereços IP estáticos a um VM, atribua vários NICs virtuais a um VM.
>


> [!IMPORTANT]
> Configurar [aparelhos virtuais](https://azure.microsoft.com/solutions/network-appliances/) de rede na via de comunicação entre a aplicação SAP e a camada DBMS de um sistema SAP NetWeaver-, Hybris-ou S/4HANA baseado em S/4HANA não é suportado. Esta restrição é por razões de funcionalidade e desempenho. O caminho de comunicação entre a camada de aplicação SAP e a camada DBMS deve ser direto. A restrição não inclui as regras do grupo de segurança de [aplicações (ASG) e NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) se essas regras ASG e NSG permitirem uma via de comunicação direta. 
>
> Outros cenários em que os aparelhos virtuais da rede não são suportados são em:
>
> * Percursos de comunicação entre VMs Azure que representam os nós do cluster Linux Pacemaker e dispositivos SBD, conforme descrito em [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Os caminhos de comunicação entre os VMs Azure e o Windows Server Scale-Out File Server (SOFS) configuram como descrito no [Cluster uma instância SAP ASCS/SCS num cluster de falhas](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)do Windows utilizando uma parte de ficheiro no Azure . 
>
> Os aparelhos virtuais de rede em caminhos de comunicação podem facilmente duplicar a latência da rede entre dois parceiros de comunicação. Também podem restringir a entrada em caminhos críticos entre a camada de aplicação SAP e a camada DBMS. Em alguns cenários de cliente, os aparelhos virtuais de rede podem fazer com que os clusters Pacemaker Linux falhem. Estes são casos em que as comunicações entre os nós do cluster Do Pacemaker Linux comunicam com o seu dispositivo SBD através de um aparelho virtual de rede.
>

> [!IMPORTANT]
> Outro design que *não* é suportado é a segregação da camada de aplicação SAP e a camada DBMS em diferentes redes virtuais Azure que não são [espreitadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) umas com as outras. Recomendamos que segregaa a camada de aplicação SAP e a camada DBMS utilizando subredes dentro de uma rede virtual Azure em vez de utilizar diferentes redes virtuais Azure. 
>
> Se decidir não seguir a recomendação e, em vez disso, segregar as duas camadas em diferentes redes virtuais, as duas redes virtuais devem ser [espreitadas.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 
>
> Esteja ciente de que o tráfego de rede entre duas redes virtuais [azure peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) está sujeito a custos de transferência. Um enorme volume de dados que consiste em muitos terabytes é trocado entre a camada de aplicação SAP e a camada DBMS. Pode acumular custos substanciais se a camada de aplicação SAP e a camada DBMS estiverem segregadas entre duas redes virtuais Azure.

Utilize dois VMs para a sua implantação DE DBMS de produção dentro de um conjunto de disponibilidade Azure. Utilize também encaminhamento separado para a camada de aplicação SAP e o tráfego de gestão e operações para os dois VMs DBMS. Veja a imagem seguinte:

![Diagrama de dois VMs em duas subredes](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Utilize o Equilíbrio de Carga Azure para redirecionar o tráfego
A utilização de endereços IP virtuais privados utilizados em funcionalidades como a Replicação do Sistema SQL Server Always On ou HANA requer a configuração de um equilibrador de carga Azure. O equilibrador de carga utiliza portas de sonda para determinar o nó DBMS ativo e encaminhar o tráfego exclusivamente para aquele nó de base de dados ativo. 

Se houver uma falha no nó de base de dados, não há necessidade de a aplicação SAP reconfigurar. Em vez disso, as arquiteturas de aplicação SAP mais comuns reconectam-se contra o endereço IP virtual privado. Entretanto, o equilibrador de carga reage à falha do nó redirecionando o tráfego contra o endereço IP virtual privado para o segundo nó.

O Azure oferece dois [SKUs](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)de equilíbrio de carga diferentes: um SKU básico e um SKU padrão. A menos que pretenda implantar em zonas de disponibilidade azure, o equilibrador de carga básico SKU faz bem.

O tráfego entre os VMs DBMS e a camada de aplicação SAP está sempre encaminhado através do equilibrista de carga a toda a hora? A resposta depende da configuração do equilibrador de carga. 

Neste momento, o tráfego de entrada para o VM DBMS é sempre encaminhado através do equilibrista de carga. A rota de tráfego de saída do VM DBMS para a camada de aplicação VM depende da configuração do equilibrador de carga. 

O equilibrador de carga oferece uma opção de DirectServerReturn. Se essa opção estiver configurada, o tráfego direcionado do VM DBMS para a camada de aplicação SAP *não* é encaminhado através do equilibrista de carga. Em vez disso, vai diretamente para a camada de aplicação. Se o DirectServerReturn não estiver configurado, o tráfego de devolução à camada de aplicação SAP é encaminhado através do equilibrista de carga.

Recomendamos que configure o DirectServerReturn em combinação com os equilibradores de carga que estão posicionados entre a camada de aplicação SAP e a camada DBMS. Esta configuração reduz a latência da rede entre as duas camadas.

Para um exemplo de como configurar esta configuração com o SQL Server Always On, consulte [Configure um ouvinte ILB para grupos de disponibilidade Always On em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Se utilizar os modelos GitHub JSON publicados como referência para as suas implementações de infraestruturas SAP em Azure, estude este [modelo para um sistema SAP 3-Tier](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Neste modelo, também pode ver as definições corretas para o equilibrador de carga.

### <a name="azure-accelerated-networking"></a>Rede acelerada Azure
Para reduzir ainda mais a latência da rede entre Os VMs Azure, recomendamos que escolha a [Rede Acelerada Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Use-o quando implementar VMs Azure para uma carga de trabalho SAP, especialmente para a camada de aplicação SAP e a camada DBMS SAP.

> [!NOTE]
> Nem todos os tipos vM suportam o Networking Acelerado. O artigo anterior lista os tipos vM que suportam o Networking Acelerado.
>

---
> ![Windows][Logo_Windows] Windows
>
> Para aprender a implementar VMs com Rede Acelerada para Windows, consulte [Criar uma máquina virtual Windows com Networking Acelerado](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Para obter mais informações sobre a distribuição do Linux, consulte [Criar uma máquina virtual Linux com Networking Acelerado](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> No caso de SUSE, Red Hat e Oracle Linux, o Accelerated Networking é suportado com lançamentos recentes. Lançamentos mais antigos como SLES 12 SP2 ou RHEL 7.2 não suportam o Azure Accelerated Networking.
>


## <a name="deployment-of-host-monitoring"></a>Implantação da monitorização do hospedeiro
Para a utilização da produção de aplicações SAP em máquinas virtuais Azure, o SAP requer a capacidade de obter dados de monitorização do hospedeiro dos anfitriões físicos que executam as máquinas virtuais Azure. É necessário um nível específico de patch do Agente anfitrião SAP que permite esta capacidade no SAPOSCOL e no Agente anfitrião da SAP. O nível exato de patch está documentado na Nota [SAP 1409604].

Para obter mais informações sobre a implantação de componentes que entregam dados de hospedas ao SAPOSCOL e ao Agente anfitrião da SAP e à gestão do ciclo de vida desses componentes, consulte o guia de [implantação.][deployment-guide]


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre um DBMS em particular, consulte:

- [Implementação em SQL Server do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_sqlserver.md)
- [Implementação em Oracle do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_oracle.md)
- [Implementação em IBM DB2 do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_ibm.md)
- [Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_sapase.md)
- [SAP maxDB, Live Cache e Implementação do Servidor de Conteúdo no Azure](dbms_guide_maxdb.md)
- [Manual de operações do SAP HANA no Azure](hana-vm-operations.md)
- [SAP HANA alta disponibilidade para máquinas virtuais Azure](sap-hana-availability-overview.md)
- [Guia de backup para SAP HANA em máquinas virtuais Azure](sap-hana-backup-guide.md)

