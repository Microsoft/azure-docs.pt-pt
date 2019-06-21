---
title: Considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP | Documentos da Microsoft
description: Considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 975289f338e638ed0209d4f6cf2a163ced996e42
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202948"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP
[1114181]: https://launchpad.support.sap.com/#/notes/1114181
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]: https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2171857]: https://launchpad.support.sap.com/#/notes/2171857
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Este guia faz parte da documentação sobre como implementar e implantar software SAP no Microsoft Azure. Antes de ler este guia, leia os [guia de planeamento e implementação][planning-guide]. Este documento aborda os aspectos de genérico de implementação de sistemas relacionados com o SAP DBMS em máquinas de virtuais (VMs) do Microsoft Azure ao utilizar a infraestrutura do Azure como um capacidades do serviço (IaaS).

O documento complementa a documentação de instalação de SAP e SAP Notes, que representam os recursos principais para instalações e implementações de SAP software em determinadas plataformas.

Neste documento, são introduzidas considerações da execução de sistemas relacionados com o SAP DBMS em VMs do Azure. Existem algumas referências para sistemas específicos do DBMS neste capítulo. Em vez disso, os sistemas DBMS específicos são tratados dentro deste documento, depois deste documento.

## <a name="definitions"></a>Definições
Em todo o documento, esses termos são utilizados:

* **IaaS**: Infraestrutura como serviço.
* **PaaS**: Plataforma como serviço.
* **SaaS**: Software como serviço.
* **Componente SAP**: Um aplicativo SAP individual como o componente Central de ERP (ECC), o Business Warehouse (BW), o Gestor de solução ou o Enterprise Portal (EP). Componentes SAP podem basear-se em tecnologias tradicionais de ABAP ou Java ou num aplicativo com base no NetWeaver, como objetos comerciais.
* **Ambiente de SAP**: Um ou mais componentes SAP logicamente agrupados para executar uma função de negócio, como o desenvolvimento, controle de qualidade, treinamento, recuperação após desastre ou de produção.
* **Ambiente SAP**: Esse termo refere-se para os ativos SAP inteiros num cliente paisagem IT. A paisagem SAP inclui todos os ambientes de produção e de não produção.
* **Sistema SAP**: A combinação de uma camada do DBMS e uma camada de aplicativo de, por exemplo, um sistema de desenvolvimento SAP ERP, um SAP Business Warehouse testar o sistema ou um sistema de produção do SAP CRM. Nas implementações do Azure, dividir estas duas camadas entre no local e o Azure não é suportada. Como resultado, um sistema SAP é implementado no local ou é implementado no Azure. Pode implantar os sistemas diferentes de um ambiente SAP no Azure ou no local. Por exemplo, pode implementar o desenvolvimento de CRM do SAP e testar sistemas no Azure mas implementar o SAP CRM produção sistema no local.
* **Em vários locais**: Descreve um cenário em que as VMs são implementadas para uma subscrição do Azure com o site a site, múltiplos sites ou centros de conectividade do ExpressRoute do Azure entre os dados no local e o Azure. Documentação do Azure em comum, esses tipos de implementações também são descritos como cenários de vários locais. 

    O motivo para a ligação é para expandir os domínios no local, Active Directory no local e o DNS no local para o Azure. O Panorama de no local é expandido para recursos do Azure da subscrição. Com essa extensão, as VMs podem ser parte do domínio no local. Os utilizadores de domínio do domínio no local podem aceder aos servidores e executam serviços nessas VMS, como serviços do DBMS. Resolução de nomes e de comunicação entre VMs implementadas no local e as VMs implementadas no Azure, é possível. Este cenário é o cenário mais comum em utilização para implementar recursos SAP no Azure. Para obter mais informações, consulte [planear e conceber para o gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Implementações em vários locais de sistemas SAP são em que máquinas virtuais do Azure com sistemas SAP são membros de um domínio no local e são suportadas para sistemas SAP de produção. Configurações em vários locais são suportadas para a implementação de partes ou concluir cenários SAP no Azure. Até mesmo executar o ambiente completo do SAP no Azure requer essas VMs para ser parte de um domínio no local e o LDAP/do Active Directory. 
>
> Nas versões anteriores da documentação, cenários de TI híbrida foram mencionados. O termo *híbrida* está enraizada no fato de que existe uma conectividade em vários locais entre no local e o Azure. Neste caso, híbridas também significa que as VMs no Azure fazem parte do Active Directory no local.
>
>

Alguma documentação de Microsoft descreve os cenários de em vários locais de forma um pouco diferente, especialmente para configurações de elevada disponibilidade do DBMS. No caso dos documentos relacionados com o SAP, o cenário em vários locais se resume no local para local ou privado [ExpressRoute](https://azure.microsoft.com/services/expressroute/) conectividade e um ambiente SAP, que é distribuído entre no local e o Azure.

## <a name="resources"></a>Recursos
Existem outros artigos disponíveis na carga de trabalho SAP no Azure. Começar com [carga de trabalho SAP no Azure: Introdução ao](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) e, em seguida, escolha a sua área de interesse.

As seguintes notas de SAP estão relacionados com o SAP no Azure em relação à área de abrangidas neste documento.

| Número de nota | Cargo |
| --- | --- |
| [1928533] |Aplicações de SAP no Azure: Produtos suportados e tipos de VM do Azure |
| [2015553] |SAP no Microsoft Azure: Pré-requisitos de suporte |
| [1999351] |Resolução de problemas avançado do Azure de monitorização para SAP |
| [2178632] |Chave de métricas de monitorização para o SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: Melhorada a monitorização |
| [2191498] |SAP no Linux com o Azure: Melhorada a monitorização |
| [2039619] |Aplicações de SAP no Microsoft Azure utilizando a base de dados do Oracle: Produtos suportados e versões |
| [2233094] |DB6: Aplicações de SAP no Azure com o IBM DB2 para Windows, Linux e UNIX: Informações adicionais |
| [2243692] |Linux no Microsoft Azure (IaaS) VM: Problemas de licença do SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Observações de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalação e atualização |
| [2069760] |Instalação de SAP do Oracle Linux 7.x e atualização |
| [1597355] |Recomendação de espaço de comutação para Linux |
| [2171857] |Oracle Database 12C: Suporte de sistema de ficheiros no Linux |
| [1114181] |Oracle Database 11g: Suporte de sistema de ficheiros no Linux |


Para informações sobre todas as notas de SAP para o Linux, consulte a [SAP Comunidade wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

É necessário um conhecimento funcional de arquitetura do Microsoft Azure e como as máquinas virtuais do Microsoft Azure são implementadas e em funcionamento. Para obter mais informações, consulte [documentação do Azure](https://docs.microsoft.com/azure/).

Em geral, a instalação do Windows, Linux e DBMS e configuração são, essencialmente, igual a qualquer máquina virtual ou máquina bare-metal instala no local. Existem alguns arquitetura e o sistema de gestão suas decisões de implementação que são diferentes quando utiliza o IaaS do Azure. Este documento explica as diferenças de gestão do sistema para estar preparado para quando usa o IaaS do Azure e específicos de arquitetura.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estrutura de armazenamento de uma VM para implementações de RDBMS
Para seguir este capítulo, leia e compreenda as informações apresentadas [este capítulo] [ deployment-guide-3] do [guia de implementação][deployment-guide]. Tem de compreender e saber sobre os diferentes série de VM e as diferenças entre armazenamento standard e premium antes de ler este capítulo. 

Para saber mais sobre o armazenamento do Azure para VMs do Azure, consulte:

- [Introdução aos discos geridos para VMs do Windows Azure](../../windows/managed-disks-overview.md).
- [Introdução aos discos geridos para VMs Linux do Azure](../../linux/managed-disks-overview.md).

Numa configuração básica, normalmente, recomendamos uma estrutura de implementação em que o sistema operativo, o DBMS e a eventual binários SAP estão separados dos arquivos de banco de dados. Recomendamos que os sistemas SAP que são executados em máquinas virtuais do Azure têm o VHD base, ou disco, instalados com o sistema operativo, executáveis de sistema de gestão de base de dados e executáveis SAP. 

Os ficheiros de dados e de registo do DBMS são armazenados no armazenamento standard ou o armazenamento premium. Estão armazenados em discos separados e anexados como discos lógicos para a imagem de sistema de operativo Azure VM original. Para implementações de Linux, recomendações diferentes são documentadas, especialmente para o SAP HANA.

Ao planear o layout de disco, encontre o melhor equilíbrio entre estes itens:

* O número de ficheiros de dados.
* O número de discos que contêm os ficheiros.
* As quotas IOPS de um único disco.
* A taxa de transferência de dados por disco.
* O número de discos de dados adicionais possíveis por tamanho da VM.
* O débito global do armazenamento uma VM pode fornecer.
* A latência podem fornecer diferentes tipos de armazenamento do Azure.
* SLAs VM.

Azure impõe uma quota IOPS por disco de dados. Estas quotas são diferentes para discos alojados no armazenamento standard e o armazenamento premium. Latência de e/s também é diferente entre os dois tipos de armazenamento. O armazenamento Premium fornece melhor latência de e/s. 

Cada um dos diferentes tipos VM tem um número limitado de discos de dados que podem ser anexadas. Outra restrição é que apenas determinados tipos VM podem utilizar o armazenamento premium. Normalmente, optar por utilizar um determinado tipo VM com base nos requisitos de CPU e memória. Também poderá considerar o IOPS, latência e requisitos de débito de disco que normalmente são dimensionados com o número de discos ou o tipo de discos de armazenamento premium. O número de IOPS e o débito para ser possível através de cada disco pode ditar o tamanho do disco, especialmente com o armazenamento premium.

> [!NOTE]
> Para implementações do DBMS, recomendamos a utilização do armazenamento premium para quaisquer dados, o log de transações, ou ficheiros de Refazer. Não importa se deseja implantar sistemas de produção ou de não produção.

> [!NOTE]
> Para se beneficiar do Azure do exclusivo [único SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), todos os discos que estão anexados tem de ser o tipo de armazenamento premium, que inclui o VHD base.

> [!NOTE]
> Arquivos de banco de dados principal de alojamento, como arquivos de dados e de registo, de bases de dados do SAP no hardware de armazenamento que está localizado em centros de dados localizadas conjuntamente de terceiros adjacentes a centros de dados do Azure não é suportada. Para cargas de trabalho do SAP, o armazenamento único que é representado como serviço do Azure nativo é suportado para os ficheiros de registo de dados e de transação de bases de dados do SAP.

A colocação de ficheiros de base de dados e os ficheiros de registo e refazer e o tipo de armazenamento do Azure que utiliza é definida pelos requisitos de IOPS, débito e latência. Para que o suficiente IOPS, será forçado a utilizar vários discos ou utilizar um disco de armazenamento premium maior. Se utilizar vários discos, crie uma faixa de software em todos os discos que contêm os ficheiros de dados ou o registo e ficheiros de Refazer. Em tais casos, o IOPS e o débito de disco SLAs do armazenamento premium subjacente os discos ou o IOPS máximo alcançável de discos de armazenamento standard são cumulativos para o conjunto resultante do stripe.

Como já declarado, se seu requisito de IOPS exceder o que pode fornecer um único VHD, equilibrar o número de IOPS que são necessários para os ficheiros de base de dados num número de VHDs. A maneira mais fácil de distribuir a carga IOPS entre discos é criar uma faixa de software sobre os discos diferentes. Em seguida, coloca um número de ficheiros de dados do SAP DBMS em LUNs criados fora do stripe de software. o número de discos no stripe é orientado por demanda, as necessidades de débito de disco e volume IOPs demandas.


---
> ![Windows][Logo_Windows] Windows
>
> Recomendamos que utilize espaços de armazenamento do Windows para criar conjuntos de faixa entre vários VHDs do Azure. Utilize, pelo menos, Windows Server 2012 R2 ou Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> São suportados apenas o MDADM e o Gestor de volumes lógicos (LVM) para criar um RAID de software no Linux. Para obter mais informações, consulte:
>
> - [Configurar o RAID de software no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) usando MDADM
> - [Configurar LVM numa VM do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) usando LVM
>
>

---

> [!NOTE]
> Porque o armazenamento do Azure mantém três imagens dos VHDs, não faz sentido para configurar uma redundância, quando do stripe. Apenas terá de configurar a repartição, para que o e/s são distribuídos pelos VHDs de diferentes.
>

### <a name="managed-or-nonmanaged-disks"></a>Discos geridos ou em
Uma conta de armazenamento do Azure é uma construção administrativa e também um sujeito de limitações. Limitações são diferentes entre contas de armazenamento standard e contas de armazenamento premium. Para obter informações sobre as capacidades e limitações, consulte [metas de escalabilidade e desempenho do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Para o armazenamento standard, lembre-se de que existe um limite de IOPS por conta de armazenamento. Consulte a linha que contém **taxa Total de pedidos** no artigo [metas de escalabilidade e desempenho do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Também existe um limite inicial no número de contas de armazenamento por subscrição do Azure. VHDs de saldo para o ambiente SAP maior em contas de armazenamento diferentes para evitar atingir os limites de uma dessas contas de armazenamento. Isso é entediante trabalho quando está falando sobre algumas das máquinas virtuais centenas com mais de mil VHDs.

Uma vez que não é recomendado utilizar o armazenamento standard para implementações do DBMS em conjunto com uma carga de trabalho do SAP, referências e recomendações para o armazenamento standard estão limitadas a neste breve [artigo](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Para evitar o trabalho administrativo de planeamento e implementação de VHDs em contas de armazenamento do Azure diferente, a Microsoft introduziu [Managed Disks do Azure](https://azure.microsoft.com/services/managed-disks/) em 2017. Discos geridos estão disponíveis para o armazenamento standard e o armazenamento premium. São as principais vantagens de discos geridos em comparação comparadas discos em:

- Para discos geridos, o Azure distribui os VHDs de diferentes em contas de armazenamento diferentes automaticamente no momento da implementação. Dessa forma, limita a conta de armazenamento para o volume de dados, débito de e/s, e não são atingir o IOPS.
- Utilizar discos geridos, o armazenamento do Azure honra os conceitos de conjuntos de disponibilidade do Azure. Se a VM faz parte de um conjunto de disponibilidade do Azure, o base VHD e o disco anexado de uma VM são implementadas em diferentes domínios de falha e de atualização.


> [!IMPORTANT]
> Tendo em conta as vantagens do Managed Disks do Azure, recomendamos que use Managed Disks do Azure para as suas implementações do DBMS e implementações de SAP em geral.
>

Para converter de discos não para os discos geridos, veja:

- [Converter uma máquina virtual do Windows de discos não geridos para managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Converter uma máquina virtual Linux de discos não geridos para managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Colocação em cache para VMs e os discos de dados
Ao montar discos para VMs, pode escolher se o tráfego de e/s entre a VM e esses discos localizados no armazenamento do Azure é colocado em cache. Armazenamento Standard e premium usar duas tecnologias diferentes para este tipo de cache.

As recomendações seguintes partem do princípio dessas características de e/s para DBMS padrão:

- É principalmente uma leitura carga de trabalho em relação a arquivos de dados de uma base de dados. Essas leituras são crítico para o sistema DBMS de desempenho.
- Escrever contra os ficheiros de dados ocorre em picos com base em pontos de verificação ou um fluxo constante. Média por dia, existem escritas menos do que de leituras. Diferentes dos padrões convencionais leituras dos ficheiros de dados, essas escritas são assíncronas e não mantêm quaisquer transações de utilizador.
- Existem mínimo de leituras da transação arquivos de log ou refazer. As exceções são grandes e/SS, quando executar backups de log de transação.
- A carga principal em arquivos de log de transação ou refazer é escrita. Depende da natureza da carga de trabalho, pode ter e/ss tão pequena como 4 KB ou, em outros casos, os tamanhos de e/s de 1 MB ou mais.
- Todas as gravações devem persistência no disco de forma fiável.

Para o armazenamento standard, os tipos de cache de possíveis são:

* Nenhuma
* Leitura
* Leitura/Escrita

Para obter um desempenho consistente e determinista, definir a colocação em cache no armazenamento standard para todos os discos que contêm os ficheiros de dados relacionados com o DBMS, inicie sessão e Refazer a ficheiros e espaço de tabela para **NONE**. A colocação em cache da base de VHD pode permanecer com o padrão.

Para armazenamento premium, existem as seguintes opções de colocação em cache:

* Nenhuma
* Leitura
* Leitura/escrita
* Nenhum + acelerador de escrita, o que é apenas para VMs de série M do Azure
* Leitura + acelerador de escrita, o que é apenas para VMs de série M do Azure

Para armazenamento premium, recomendamos que utilize **colocação em cache para ficheiros de dados de leitura** do SAP de base de dados e escolha **sem colocação em cache para os discos dos ficheiros de registo**.

Para implementações de série M, recomendamos que utilize o acelerador de escrita do Azure para a sua implementação do DBMS. Para obter detalhes, restrições e implantação do acelerador de escrita do Azure, consulte [ativar o acelerador de escrita](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Discos nonpersistent do Azure
VMs do Azure oferecem discos nonpersistent depois de uma VM é implementada. No caso de um reinício VM, todo o conteúdo nessas unidades serem eliminado. É uma vez que os ficheiros de dados e ficheiros de registo e retrabalho de bases de dados devem sob nenhuma circunstância estar localizados nessas unidades continuadas. Pode haver exceções para algumas bases de dados, onde estas unidades continuadas poderiam ser adequadas para tempdb e temp tablespaces. Evite utilizar essas unidades para VMs de série, uma vez que essas unidades continuadas são limitadas em débito com essa família VM. 

Para obter mais informações, consulte [entender a unidade temporária em VMs do Windows no Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> A unidade D numa VM do Azure é uma unidade continuada, que é suportada por alguns discos locais no nó de computação do Azure. Porque é continuada, todas as alterações feitas para o conteúdo na unidade D são perdidas quando a VM é reiniciada. As alterações incluem ficheiros que foram armazenados, os diretórios que foram criados e aplicações que foram instaladas.
>
> ![Linux][Logo_Linux] Linux
>
> As VMs do Linux do Azure automaticamente montar uma unidade durante /mnt/resource que é uma unidade continuada, apoiada por discos locais no nó de computação do Azure. Porque é continuada, todas as alterações efetuadas ao conteúdo no/mnt/recurso são perdidas quando a VM é reiniciada. As alterações incluem ficheiros que foram armazenados, os diretórios que foram criados e aplicações que foram instaladas.
>
>

---



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resiliência de armazenamento do Microsoft Azure
Armazenamento do Microsoft Azure armazena o VHD base, com o SO e discos ligados ou blobs, em, pelo menos, três nós de armazenamento separada. Este tipo de armazenamento é denominado o armazenamento localmente redundante (LRS). LRS é a predefinição para todos os tipos de armazenamento no Azure.

Existem outros métodos de redundância. Para obter mais informações, consulte [replicação de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>O armazenamento Premium é o tipo recomendado de armazenamento para VMs do DBMS e os discos que armazenam o registo e base de dados e Refazer ficheiros. O método de redundância só estão disponíveis para o armazenamento premium é LRS. Como resultado, tem de configurar métodos de base de dados para ativar a replicação de dados de base de dados para outra zona de disponibilidade ou região do Azure. Métodos de base de dados incluem o SQL Server Always On, Oracle Data Guard e HANA System Replication.


> [!NOTE]
> Para implementações do DBMS, a utilização do armazenamento georredundante (GRS) não é recomendada para o armazenamento standard. GRS gravemente afeta o desempenho e não respeitar a ordem de escrita em VHDs de diferentes que estão ligados a uma VM. Não respeitar a ordem de escrita em VHDs de diferentes potencialmente leva a bases de dados inconsistentes no lado do destino de replicação. Esta situação ocorre se os ficheiros de base de dados e de registo e de retrabalho são distribuídos por vários VHDs, como é geralmente o caso, na origem de lado VM.



## <a name="vm-node-resiliency"></a>Resiliência de nó VM
O Azure oferece vários SLAs diferentes para as VMs. Para obter mais informações, consulte a versão mais recente do [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Como a camada do DBMS é, normalmente, é essencial para disponibilidade num sistema SAP, tem de compreender os conjuntos de disponibilidade, zonas de disponibilidade e eventos de manutenção. Para obter mais informações sobre estes conceitos, veja [gerir a disponibilidade das máquinas de virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [gerir a disponibilidade das máquinas virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

A recomendação mínima para cenários do DBMS de produção com uma carga de trabalho do SAP é:

- Implemente duas VMs num conjunto na mesma região do Azure de disponibilidade separada.
- Execute estas duas VMs na mesma rede virtual do Azure e tem NICs ligadas fora das sub-redes da mesmas.
- Utilize os métodos de base de dados para manter um modo de espera ativo com a segunda VM. Métodos podem ser SQL Server Always On, proteção de dados Oracle ou HANA System Replication.

Também pode implementar uma terceira VM noutra região do Azure e utilize os mesmos métodos de base de dados para fornecer uma réplica assíncrona noutra região do Azure.

Para obter informações sobre como configurar conjuntos de disponibilidade do Azure, consulte [deste tutorial](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considerações de rede do Azure
Nas implementações de SAP em grande escala, utilize o plano gráfico de [Datacenter Virtual do Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Utilize-o para as atribuições de configuração e as permissões e funções de rede virtual para diferentes partes da sua organização.

Essas práticas recomendadas são o resultado de centenas de implementações de cliente:

- As redes virtuais, que a aplicação SAP é implementada num não tem acesso à internet.
- Executar a base de dados VMs na mesma rede virtual como a camada de aplicativo.
- As VMs dentro da rede virtual tem uma alocação de estática do endereço IP privado. Para obter mais informações, consulte [tipos e métodos de alocação no Azure de endereços IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Existem restrições de encaminhamento de e para as VMs do DBMS *não* definido com firewalls instalados nas VMs locais a DBMS. Em vez disso, o encaminhamento de tráfego é definido com [(NSGs) de grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Para separar e isolar o tráfego para a VM do DBMS, atribua diferentes NICs à VM. Cada NIC obtém um endereço IP diferente, e cada NIC está atribuído a uma sub-rede de rede virtual diferente. Cada sub-rede tem diferentes regras NSG. O isolamento ou a separação do tráfego de rede é uma medida para o encaminhamento. Não é utilizado para definir as quotas para o débito de rede.

> [!NOTE]
> Atribuição de endereços IP estáticos através do Azure significa que para atribuí-las a NICs virtuais individuais. Não atribuir endereços IP estáticos no SO convidado a uma NIC virtual. Alguns serviços do Azure como o Azure Backup confiam no fato de que, no mínimo a NIC virtual principal está definida para DHCP e não para endereços IP estáticos. Para obter mais informações, consulte [cópia de segurança de máquina virtual de resolução de problemas do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Para atribuir vários endereços IP estáticos a uma VM, atribua vários NICs virtuais a uma VM.
>


> [!IMPORTANT]
> Configurando [aplicações virtuais de rede](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de NetWeaver um SAP-, Hybris- ou sistema baseado em S/4HANA SAP não é suportada. Esta restrição é por motivos de desempenho e funcionalidade. O caminho de comunicação entre a camada de aplicação SAP e a camada DBMS tem de ser um direto. A restrição não inclui [grupo de segurança de aplicação (ASG) e as regras do NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) se essas regras ASG e NSG permitem um caminho de comunicação direta. 
>
> Outros cenários em que não são suportadas aplicações virtuais de rede estão em:
>
> * Caminhos de comunicação entre as VMs do Azure que representam Pacemaker de Linux do cluster nós e dispositivos SBD conforme descrito em [elevada disponibilidade para SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Caminhos de comunicação entre as VMs do Azure e o servidor de ficheiros (SOFS) do Windows Server horizontal definido até conforme descrito em [uma instância do SAP ASCS/SCS de Cluster num cluster de ativação pós-falha do Windows ao utilizar uma partilha de ficheiros no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Aplicações virtuais de rede em caminhos de comunicação podem facilmente double a latência de rede entre parceiros de comunicação de dois. Também pode restringir a taxa de transferência em caminhos críticas entre a camada de aplicação SAP e a camada do DBMS. Em alguns cenários de cliente, dispositivos de rede virtual podem fazer com que os clusters do Linux de Pacemaker falhe. Esses são casos em que as comunicações entre os nós de cluster do Linux Pacemaker comunicam para o respetivo dispositivo SBD através de uma aplicação virtual de rede.
>

> [!IMPORTANT]
> Outro que de design da *não* suportada é a diferenciação de camada de aplicação SAP e a camada do DBMS em diferentes redes virtuais do Azure que não são [em modo de peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre si. É recomendável separar a camada de aplicação SAP e a camada do DBMS utilizando sub-redes dentro de uma rede virtual do Azure em vez de através da utilização de diferentes redes virtuais do Azure. 
>
> Se decidir não seguir a recomendação e segregar em vez disso, as duas camadas em redes virtuais diferentes, as duas redes virtuais têm de ser [em modo de peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Tenha em atenção que o tráfego entre as duas de rede [em modo de peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) redes virtuais do Azure está sujeita a custos de transferência. Volume de dados grande que consiste em muitos terabytes é trocado entre a camada de aplicação SAP e a camada do DBMS. Pode acumular os custos substanciais se a camada de aplicação SAP e a camada do DBMS são separados entre duas redes virtuais do Azure em modo de peering.

Definir a utilização de duas VMs para a sua implementação de DBMS dentro de disponibilidade do Azure de produção. Também pode Utilize encaminhamento separado para a camada de aplicação SAP e o tráfego de gerenciamento e operações para as duas VMs do DBMS. Veja a imagem seguinte:

![Diagrama de duas VMs em duas sub-redes](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Utilizar o Balanceador de carga do Azure para redirecionar o tráfego
A utilização de endereços IP virtual privados utilizado em funcionalidades como o SQL Server Always On ou o HANA System Replication requer a configuração de um balanceador de carga do Azure. O Balanceador de carga utiliza as portas da sonda para determinar o nó ativo do DBMS e encaminhar o tráfego exclusivamente para esse nó de base de dados ativa. 

Se houver uma ativação pós-falha do nó da base de dados, não é necessário para a aplicação SAP reconfigurar. Em vez disso, as arquiteturas de aplicativos mais comuns do SAP restabelecer a ligação com o endereço IP virtual privado. Enquanto isso, o Balanceador de carga reage para a ativação pós-falha de nó ao redirecionar o tráfego em comparação ao endereço IP virtual privado para o segundo nó.

O Azure oferece dois diferentes [SKUs de Balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview): um SKU standard e de um SKU básico. A menos que queira implemente nas zonas de disponibilidade do Azure, o SKU do Balanceador de carga básico faz bem.

É o tráfego entre as VMs do DBMS e a camada de aplicação SAP sempre encaminhados através do Balanceador de carga o tempo todo? A resposta depende de como configurar o Balanceador de carga. 

Neste momento, o tráfego de entrada para a VM do DBMS sempre é encaminhado através do Balanceador de carga. A saída tráfego rota da DBMS VM para a camada de aplicativo que VM depende da configuração de Balanceador de carga. 

O Balanceador de carga oferece uma opção de DirectServerReturn. Se essa opção estiver configurada, o tráfego de direcionado da DBMS VM para a camada de aplicação SAP for *não* encaminhado através do Balanceador de carga. Em vez disso, ele vai diretamente para a camada de aplicativo. Se não estiver configurado DirectServerReturn, o tráfego de retorno para a camada de aplicação SAP é encaminhado através do Balanceador de carga.

Recomendamos que configure DirectServerReturn em combinação com balanceadores de carga que são posicionados entre a camada de aplicação SAP e a camada do DBMS. Esta configuração reduz a latência de rede entre as duas camadas.

Para obter um exemplo de como configurar esta configuração com o SQL Server Always On, veja [configurar um serviço de escuta ILB para grupos de disponibilidade Always On no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Se usar modelos publicados do GitHub JSON como uma referência para as implementações de infraestrutura SAP no Azure, estude este [modelo para um sistema de 3 camadas SAP](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Neste modelo, também pode ver as definições corretas para o Balanceador de carga.

### <a name="azure-accelerated-networking"></a>Azure Accelerated Networking
Para reduzir ainda mais a latência de rede entre as VMs do Azure, recomendamos que escolha [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Utilize-o ao implementar as VMs do Azure para uma carga de trabalho do SAP, especialmente para a camada de aplicação SAP e a camada do SAP DBMS.

> [!NOTE]
> Nem todos os tipos de VM suportam redes aceleradas. O artigo anterior lista os tipos VM que suportam redes aceleradas.
>

---
> ![Windows][Logo_Windows] Windows
>
> Para saber como implementar VMs com o Accelerated Networking para Windows, veja [criar uma máquina virtual do Windows com redes aceleradas](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Para obter mais informações sobre a distribuição do Linux, consulte [criar uma máquina virtual Linux com redes aceleradas](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> No caso do Red Hat, SUSE e Oracle Linux, a redes aceleradas é suportada com as versões recentes. Versões mais antigas, como o RHEL 7.2 ou o SLES 12 SP2 não suportam o Azure Accelerated Networking.
>


## <a name="deployment-of-host-monitoring"></a>Implementação do anfitrião de monitorização
Para efeitos de produção de aplicações SAP em máquinas virtuais do Azure, o SAP requer a capacidade de obter anfitrião de monitorização dos dados dos anfitriões físicos que executam as máquinas virtuais do Azure. Um nível de patch do agente de anfitrião do SAP específico é necessário que permite que esta capacidade em SAPOSCOL e o agente de anfitrião do SAP. O nível de patch exata está documentado na nota SAP [1409604].

Para obter mais informações sobre a implementação de componentes que fornecem dados de anfitrião para SAPOSCOL e o agente de anfitrião do SAP e o gerenciamento de ciclo de vida desses componentes, consulte a [guia de implementação][deployment-guide].


## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre um DBMS específica, consulte:

- [Implementação em SQL Server do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_sqlserver.md)
- [Implementação em Oracle do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_oracle.md)
- [Implementação em IBM DB2 do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_ibm.md)
- [Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_sapase.md)
- [SAP maxDB, a Cache em direto e a implementação do servidor de conteúdo no Azure](dbms_guide_maxdb.md)
- [Manual de operações do SAP HANA no Azure](hana-vm-operations.md)
- [Elevada disponibilidade de SAP HANA para máquinas virtuais do Azure](sap-hana-availability-overview.md)
- [Guia de cópia de segurança para SAP HANA em máquinas virtuais do Azure](sap-hana-backup-guide.md)

