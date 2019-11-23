---
title: Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP | Microsoft Docs
description: Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP
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
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101370"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP
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

Este guia faz parte da documentação sobre como implementar e implantar o software SAP em Microsoft Azure. Antes de ler este guia, leia o [Guia de planejamento e implementação][planning-guide]. Este documento aborda os aspectos de implantação genérica de sistemas DBMS relacionados ao SAP em Microsoft Azure VMs (máquinas virtuais) usando os recursos de IaaS (infraestrutura como serviço) do Azure.

O artigo complementa a documentação de instalação do SAP e as notas SAP, que representam os principais recursos para instalações e implantações de software SAP em determinadas plataformas.

Neste documento, são introduzidas considerações sobre a execução de sistemas DBMS relacionados ao SAP em VMs do Azure. Há algumas referências a sistemas DBMS específicos neste capítulo. Em vez disso, os sistemas DBMS específicos são tratados neste documento.

## <a name="definitions"></a>Definições
Em todo o documento, estes termos são usados:

* **IaaS**: Infraestrutura como serviço.
* **PaaS**: plataforma como serviço.
* **SaaS**: software como serviço.
* **Componente SAP**: um aplicativo SAP individual, como um componente central de ERP (ECC), Business Warehouse (BW), Solution Manager ou Enterprise Portal (EP). Os componentes do SAP podem ser baseados em tecnologias tradicionais de ABAP ou Java ou em um aplicativo não baseado em NetWeaver, como objetos comerciais.
* **Ambiente SAP**: um ou mais componentes SAP agrupados logicamente para executar uma função de negócios, como desenvolvimento, garantia de qualidade, treinamento, recuperação de desastres ou produção.
* **Cenário SAP**: este termo refere-se a todos os ativos do SAP no cenário de ti de um cliente. A estrutura da SAP inclui todos os ambientes de produção e não produto.
* **Sistema SAP**: a combinação de uma camada DBMS e uma camada de aplicativo de, por exemplo, um sistema de desenvolvimento SAP ERP, um sistema de teste SAP Business Warehouse ou um sistema de produção SAP CRM. Nas implantações do Azure, dividir essas duas camadas entre locais e o Azure não tem suporte. Como resultado, um sistema SAP é implantado localmente ou implantado no Azure. Você pode implantar os diferentes sistemas de uma estrutura SAP no Azure ou no local. Por exemplo, você pode implantar os sistemas de desenvolvimento e teste do SAP CRM no Azure, mas implantar o sistema de produção do SAP CRM local.
* **Entre instalações**: descreve um cenário em que as VMs são implantadas em uma assinatura do Azure que tem conectividade site a site, multissite ou Azure ExpressRoute entre os data centers locais e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritos como cenários entre instalações. 

    O motivo para a conexão é estender domínios locais, Active Directory locais e o DNS local para o Azure. O cenário local é estendido para os ativos do Azure da assinatura. Com essa extensão, as VMs podem fazer parte do domínio local. Os usuários de domínio do domínio local podem acessar os servidores e executar serviços nessas VMs, como os serviços DBMS. A comunicação e a resolução de nomes entre as VMs implantadas localmente e as VMs implantadas no Azure são possíveis. Esse cenário é o cenário mais comum em uso para implantar ativos do SAP no Azure. Para obter mais informações, consulte [planejamento e design para o gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> As implantações entre instalações de sistemas SAP são onde as máquinas virtuais do Azure que executam sistemas SAP são membros de um domínio local e têm suporte para sistemas SAP de produção. Configurações entre instalações têm suporte para implantar partes ou cenários SAP completos no Azure. Até mesmo a execução da estrutura SAP completa no Azure exige que essas VMs façam parte de um domínio local e Active Directory/LDAP. 
>
> Nas versões anteriores da documentação, os cenários de ti híbrida foram mencionados. O termo *híbrido* tem raiz no fato de que há uma conectividade entre locais entre o local e o Azure. Nesse caso, o híbrido também significa que as VMs no Azure fazem parte do Active Directory local.
>
>

Algumas documentações da Microsoft descrevem cenários entre instalações de maneira um pouco diferente, especialmente para configurações de alta disponibilidade do DBMS. No caso dos documentos relacionados ao SAP, o cenário entre instalações se resume à conectividade de [ExpressRoute](https://azure.microsoft.com/services/expressroute/) de site a site ou privada e uma estrutura SAP que é distribuída entre o local e o Azure.

## <a name="resources"></a>Recursos
Há outros artigos disponíveis sobre a carga de trabalho do SAP no Azure. Comece com a [carga de trabalho do SAP no Azure: comece](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) e escolha sua área de interesse.

As seguintes notas SAP estão relacionadas ao SAP no Azure em relação à área abordada neste documento.

| Número da nota | Título |
| --- | --- |
| [1928533] |Aplicativos SAP no Azure: produtos com suporte e tipos de VM do Azure |
| [2015553] |SAP em Microsoft Azure: pré-requisitos de suporte |
| [1999351] |Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2178632] |Principais métricas de monitoramento para SAP em Microsoft Azure |
| [1409604] |Virtualização no Windows: monitoramento avançado |
| [2191498] |SAP no Linux com o Azure: monitoramento avançado |
| [2039619] |Aplicativos SAP em Microsoft Azure usando o banco de dados Oracle: produtos e versões com suporte |
| [2233094] |DB6: aplicativos SAP no Azure usando IBM DB2 para Linux, UNIX e Windows: informações adicionais |
| [2243692] |VM do Linux em Microsoft Azure (IaaS): problemas de licença do SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7. x: instalação e atualização |
| [2069760] |Instalação e atualização do SAP Oracle Linux 7. x |
| [1597355] |Recomendação de espaço de permuta para Linux |
| [2171857] |Oracle Database 12c: suporte ao sistema de arquivos no Linux |
| [1114181] |Oracle Database 11g: suporte ao sistema de arquivos no Linux |


Para obter informações sobre todas as notas SAP para Linux, consulte o [wiki da Comunidade SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Você precisa de um conhecimento funcional da arquitetura de Microsoft Azure e de como Microsoft Azure máquinas virtuais são implantadas e operadas. Para obter mais informações, consulte a [documentação do Azure](https://docs.microsoft.com/azure/).

Em geral, a instalação e a configuração do Windows, Linux e DBMS são essencialmente as mesmas que qualquer máquina virtual ou máquina bare-metal instalada no local. Há algumas decisões de implementação de arquitetura e gerenciamento do sistema que são diferentes quando você usa o Azure IaaS. Este documento explica as diferenças específicas de arquitetura e gerenciamento do sistema a serem preparadas para quando você usar o IaaS do Azure.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estrutura de armazenamento de uma VM para implantações de RDBMS
Para seguir este capítulo, leia e entenda as informações apresentadas neste [capítulo][deployment-guide-3] do guia de [implantação][deployment-guide]. Você precisa entender e conhecer as diferentes séries de VM e as diferenças entre o armazenamento Standard e Premium antes de ler este capítulo. 

Para saber mais sobre o armazenamento do Azure para VMs do Azure, consulte:

- [Introdução aos Managed disks para VMs do Windows do Azure](../../windows/managed-disks-overview.md).
- [Introdução aos Managed disks para VMs Linux do Azure](../../linux/managed-disks-overview.md).

Em uma configuração básica, geralmente recomendamos uma estrutura de implantação em que o sistema operacional, o DBMS e os binários SAP eventuals sejam separados dos arquivos de banco de dados. Recomendamos que os sistemas SAP executados em máquinas virtuais do Azure tenham o VHD de base, ou disco, instalado com o sistema operacional, os executáveis do sistema de gerenciamento de banco de dados e os executáveis do SAP. 

Os dados do DBMS e os arquivos de log são armazenados no armazenamento Standard ou no armazenamento Premium. Eles são armazenados em discos separados e anexados como discos lógicos à VM de imagem do sistema operacional do Azure original. Para implantações do Linux, recomendações diferentes são documentadas, especialmente para SAP HANA.

Ao planejar o layout do disco, encontre o melhor equilíbrio entre estes itens:

* O número de arquivos de dados.
* O número de discos que contêm os arquivos.
* As cotas de IOPS de um único disco.
* A taxa de transferência de dados por disco.
* O número de discos de dados adicionais possíveis por tamanho de VM.
* A taxa de transferência de armazenamento geral que uma VM pode fornecer.
* A latência que os diferentes tipos de armazenamento do Azure podem fornecer.
* SLAs de VM.

O Azure impõe uma cota de IOPS por disco de dados. Essas cotas são diferentes para discos hospedados no armazenamento Standard e no armazenamento Premium. A latência de e/s também é diferente entre os dois tipos de armazenamento. O armazenamento Premium oferece uma latência de e/s melhor. 

Cada um dos diferentes tipos de VM tem um número limitado de discos de dados que você pode anexar. Outra restrição é que apenas determinados tipos de VM podem usar o armazenamento Premium. Normalmente, você decide usar um determinado tipo de VM com base nos requisitos de CPU e memória. Você também pode considerar os requisitos de IOPS, latência e taxa de transferência de disco que geralmente são dimensionados com o número de discos ou o tipo de discos de armazenamento Premium. O número de IOPS e a taxa de transferência a ser obtida por cada disco podem determinar o tamanho do disco, especialmente com o armazenamento Premium.

> [!NOTE]
> Para implantações de DBMS, recomendamos o uso do armazenamento Premium para quaisquer dados, log de transações ou arquivos Redo. Não importa se você deseja implantar sistemas de produção ou de não produto.

> [!NOTE]
> Para se beneficiar do SLA exclusivo de [VM única](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)do Azure, todos os discos anexados devem ser o tipo de armazenamento Premium, que inclui o VHD de base.

> [!NOTE]
> Não há suporte para a hospedagem de arquivos de banco de dados principal, como arquivos de log e de bancos de dados do SAP no hardware de armazenamento localizado em data centers de terceiros colocalizados adjacentes aos data centers do Azure. Para cargas de trabalho do SAP, somente o armazenamento representado como serviço nativo do Azure tem suporte para os arquivos de log de transações e de dados de bancos de dado SAP.

O posicionamento dos arquivos de banco de dados e os arquivos de log e restauração e o tipo de armazenamento do Azure que você usa é definido por IOPS, latência e requisitos de taxa de transferência. Para ter IOPS suficientes, você pode ser forçado a usar vários discos ou usar um disco de armazenamento Premium maior. Se você usar vários discos, crie uma distribuição de software entre os discos que contêm os arquivos de dados ou os arquivos de log e refazer. Nesses casos, o IOPS e os SLAs de taxa de transferência de disco dos discos de armazenamento Premium subjacentes ou o IOPS máximo atingível de discos de armazenamento padrão são acumulativos para o conjunto de distribuição resultante.

Como já declarado, se o requisito de IOPS exceder o que um único VHD pode fornecer, equilibre o número de IOPS necessários para os arquivos de banco de dados em vários VHDs. A maneira mais fácil de distribuir a carga de IOPS entre discos é criar uma faixa de software em diferentes discos. Em seguida, coloque vários arquivos de dados do DBMS do SAP nos LUNs que foram gravados fora da faixa de software. O número de discos na faixa é orientado por demandas de IOPs, demandas de taxa de transferência de disco e demandas de volume.


---
> ![Windows][Logo_Windows] Windows
>
> Recomendamos que você use espaços de armazenamento do Windows para criar conjuntos de distribuição entre vários VHDs do Azure. Use pelo menos o Windows Server 2012 R2 ou o Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Somente MDADM e Gerenciador de volumes lógicos (LVM) têm suporte para criar um RAID de software no Linux. Para obter mais informações, veja:
>
> - [Configurar o RAID de software no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) usando o MDADM
> - [Configurar o LVM em uma VM do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) usando o LVM
>
>

---

> [!NOTE]
> Como o armazenamento do Azure mantém três imagens dos VHDs, não faz sentido configurar uma redundância quando você distribui. Você só precisa configurar a distribuição para que as e/SS sejam distribuídas nos diferentes VHDs.
>

### <a name="managed-or-nonmanaged-disks"></a>Discos gerenciados ou não gerenciados
Uma conta de armazenamento do Azure é uma construção administrativa e também um assunto de limitações. As limitações são diferentes entre as contas de armazenamento Standard e as contas de armazenamento Premium. Para obter informações sobre recursos e limitações, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Para o armazenamento Standard, lembre-se de que há um limite para o IOPS por conta de armazenamento. Consulte a linha que contém a **taxa de solicitação total** no artigo [metas de desempenho e escalabilidade do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Também há um limite inicial para o número de contas de armazenamento por assinatura do Azure. Equilibre VHDs para o panorama do SAP maior em diferentes contas de armazenamento para evitar atingir os limites dessas contas de armazenamento. Isso é um trabalho tedioso quando você está falando sobre algumas centenas de máquinas virtuais com mais de mil VHDs.

Como o uso do armazenamento padrão para implantações de DBMS em conjunto com uma carga de trabalho do SAP não é recomendado, as referências e recomendações para o armazenamento standard são limitadas a este pequeno [artigo](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Para evitar o trabalho administrativo de planejamento e implantação de VHDs em diferentes contas de armazenamento do Azure, a Microsoft introduziu o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) em 2017. Os Managed disks estão disponíveis para armazenamento padrão e armazenamento Premium. As principais vantagens dos discos gerenciados em comparação com os discos não gerenciados são:

- Para discos gerenciados, o Azure distribui os diferentes VHDs entre diferentes contas de armazenamento automaticamente no momento da implantação. Dessa forma, os limites da conta de armazenamento para o volume de dados, a taxa de transferência de e/s e o IOPS não são atingidos.
- Usando discos gerenciados, o armazenamento do Azure honra os conceitos dos conjuntos de disponibilidade do Azure. Se a VM fizer parte de um conjunto de disponibilidade do Azure, o VHD de base e o disco anexado de uma VM serão implantados em diferentes domínios de falha e de atualização.


> [!IMPORTANT]
> Dadas as vantagens do Azure Managed Disks, recomendamos que você use o Azure Managed Disks para suas implantações do DBMS e implantações do SAP em geral.
>

Para converter de não gerenciado para discos gerenciados, consulte:

- [Converta uma máquina virtual do Windows de discos não gerenciados em discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Converta uma máquina virtual Linux de discos não gerenciados em discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Caching para VMs e discos de dados
Ao montar discos em VMs, você pode escolher se o tráfego de e/s entre a VM e os discos localizados no armazenamento do Azure é armazenado em cache. O armazenamento Standard e Premium usa duas tecnologias diferentes para esse tipo de cache.

As recomendações a seguir pressupõem essas características de e/s para DBMS padrão:

- Trata-se principalmente de uma carga de trabalho de leitura em relação aos arquivos de dados de um banco. Essas leituras são críticas de desempenho para o sistema DBMS.
- A gravação nos arquivos de dados ocorre em intermitências com base em pontos de verificação ou em um fluxo constante. Com média de um dia, há menos gravações do que leituras. Oposto a leituras de arquivos de dados, essas gravações são assíncronas e não mantêm nenhuma transação de usuário.
- Raramente há leituras do log de transações ou dos arquivos Redo. As exceções são grandes e/SS quando você executa backups de log de transações.
- A carga principal em relação aos arquivos de log de transação ou de restauração é gravações. Dependendo da natureza da carga de trabalho, você pode ter e/SS tão pequena quanto 4 KB ou, em outros casos, tamanhos de e/s de 1 MB ou mais.
- Todas as gravações devem ser persistidas no disco de maneira confiável.

Para o armazenamento Standard, os tipos de cache possíveis são:

* Nenhum
* Leitura
* Leitura/Escrita

Para obter um desempenho consistente e determinístico, defina o cache no armazenamento Standard para todos os discos que contêm arquivos de dados relacionados ao DBMS, arquivos de log e restauração e espaço de tabela como **nenhum**. O cache do VHD de base pode permanecer com o padrão.

Para o armazenamento Premium, existem as seguintes opções de cache:

* Nenhum
* Leitura
* Leitura/gravação
* Nenhum + Acelerador de Gravação, que é apenas para VMs da série M do Azure
* Leia + Acelerador de Gravação, que é apenas para VMs da série M do Azure

Para o armazenamento Premium, é recomendável que você use o **cache de leitura para arquivos de dados** do SAP e escolha **nenhum cache para os discos de arquivo (s) de log**.

Para implantações da série M, recomendamos que você use o Azure Acelerador de Gravação para sua implantação do DBMS. Para obter detalhes, restrições e implantação do Acelerador de Gravação do Azure, consulte [habilitar acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Discos não persistentes do Azure
As VMs do Azure oferecem discos não persistentes após a implantação de uma VM. No caso de uma reinicialização da VM, todo o conteúdo dessas unidades é apagado. É um dado que os arquivos de dados e os arquivos de log e restauração de bancos de dado não devem estar localizados nessas unidades não persistentes. Pode haver exceções para alguns bancos de dados, onde essas unidades não persistentes podem ser adequadas para espaços de tabela tempdb e Temp. Evite usar essas unidades para VMs de série A porque essas unidades não persistentes são limitadas em taxa de transferência com essa família de VM. 

Para obter mais informações, consulte [entender a unidade temporária em VMs do Windows no Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> A unidade D em uma VM do Azure é uma unidade não persistente, que é apoiada por alguns discos locais no nó de computação do Azure. Como não é persistente, todas as alterações feitas no conteúdo da unidade D são perdidas quando a VM é reinicializada. As alterações incluem arquivos que foram armazenados, diretórios que foram criados e aplicativos que foram instalados.
>
> ![Linux][Logo_Linux] Linux
>
> As VMs do Azure do Linux montam automaticamente uma unidade em/mnt/Resource que é uma unidade não persistente apoiada por discos locais no nó de computação do Azure. Como não é persistente, todas as alterações feitas no conteúdo em/mnt/Resource são perdidas quando a VM é reinicializada. As alterações incluem arquivos que foram armazenados, diretórios que foram criados e aplicativos que foram instalados.
>
>

---



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resiliência Armazenamento do Microsoft Azure
O Armazenamento do Microsoft Azure armazena o VHD de base, com o sistema operacional e discos anexados ou BLOBs, em pelo menos três nós de armazenamento separados. Esse tipo de armazenamento é chamado de LRS (armazenamento com redundância local). LRS é o padrão para todos os tipos de armazenamento no Azure.

Há outros métodos de redundância. Para obter mais informações, consulte [replicação do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>O armazenamento Premium é o tipo recomendado de armazenamento para VMs DBMS e discos que armazenam arquivos de banco de dados e log e refazer. O único método de redundância disponível para o armazenamento Premium é LRS. Como resultado, você precisa configurar métodos de banco de dados para habilitar a replicação de banco de dados em outra região do Azure ou na zona de disponibilidade. Os métodos de banco de dados incluem SQL Server Always On, Oracle Data Guard e replicação do sistema HANA.


> [!NOTE]
> Para implantações de DBMS, o uso de armazenamento com redundância geográfica (GRS) não é recomendado para armazenamento Standard. O GRS afeta seriamente o desempenho e não respeita a ordem de gravação em diferentes VHDs anexados a uma VM. Não respeitar a ordem de gravação em diferentes VHDs potencialmente leva a bancos de dados inconsistentes no lado do destino de replicação. Essa situação ocorre se os arquivos de banco de dados e log e redo estiverem espalhados por vários VHDs, como geralmente é o caso, no lado da VM de origem.



## <a name="vm-node-resiliency"></a>Resiliência do nó da VM
O Azure oferece vários SLAs diferentes para VMs. Para obter mais informações, consulte a versão mais recente do [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Como a camada DBMS é geralmente crítica à disponibilidade em um sistema SAP, você precisa entender os conjuntos de disponibilidade, as zonas de disponibilidade e os eventos de manutenção. Para obter mais informações sobre esses conceitos, consulte [gerenciar a disponibilidade de máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [gerenciar a disponibilidade de máquinas virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

A recomendação mínima para cenários de produção de DBMS com uma carga de trabalho do SAP é:

- Implante duas VMs em um conjunto de disponibilidade separado na mesma região do Azure.
- Execute essas duas VMs na mesma rede virtual do Azure e tenha NICs anexadas das mesmas sub-redes.
- Use métodos de banco de dados para manter um modo de espera ativo com a segunda VM. Os métodos podem ser SQL Server Always On, o Oracle Data Guard ou a replicação do sistema HANA.

Você também pode implantar uma terceira VM em outra região do Azure e usar os mesmos métodos de banco de dados para fornecer uma réplica assíncrona em outra região do Azure.

Para obter informações sobre como configurar conjuntos de disponibilidade do Azure, consulte [este tutorial](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considerações de rede do Azure
Em implantações SAP em larga escala, use o plano gráfico do [datacenter virtual do Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Use-o para sua configuração de rede virtual e permissões e atribuições de função para diferentes partes da sua organização.

Essas práticas recomendadas são o resultado de centenas de implantações de clientes:

- As redes virtuais nas quais o aplicativo SAP é implantado não têm acesso à Internet.
- As VMs de banco de dados são executadas na mesma rede virtual que a camada de aplicativo.
- As VMs na rede virtual têm uma alocação estática do endereço IP privado. Para obter mais informações, consulte [tipos de endereço IP e métodos de alocação no Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- As restrições de roteamento de e para as VMs do DBMS *não* são definidas com firewalls instalados nas VMs do DBMS local. Em vez disso, o roteamento de tráfego é definido com [NSGs (grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Para separar e isolar o tráfego para a VM do DBMS, atribua diferentes NICs à VM. Cada NIC Obtém um endereço IP diferente e cada NIC é atribuída a uma sub-rede de rede virtual diferente. Cada sub-rede tem regras NSG diferentes. O isolamento ou a separação do tráfego de rede é uma medida para o roteamento. Não é usado para definir cotas para taxa de transferência de rede.

> [!NOTE]
> Atribuir endereços IP estáticos por meio do Azure significa atribuí-los a NICs virtuais individuais. Não atribua endereços IP estáticos no sistema operacional convidado a uma NIC virtual. Alguns serviços do Azure, como o backup do Azure, dependem do fato de que pelo menos a NIC virtual primária está definida como DHCP e não para endereços IP estáticos. Para obter mais informações, consulte [solucionar problemas de backup de máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Para atribuir vários endereços IP estáticos a uma VM, atribua várias NICs virtuais a uma VM.
>


> [!IMPORTANT]
> Não há suporte para a configuração de soluções de [virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de um sistema SAP baseado em SAP NetWeaver, Hybris ou S/4HANA. Essa restrição destina-se a questões de desempenho e funcionalidade. O caminho de comunicação entre a camada do aplicativo SAP e a camada do DBMS deve ser direto. A restrição não inclui as [regras ASG (grupo de segurança de aplicativo) e NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) se essas regras ASG e NSG permitem um caminho de comunicação direta. 
>
> Outros cenários em que as soluções de virtualização de rede não têm suporte estão em:
>
> * Caminhos de comunicação entre VMs do Azure que representam nós de cluster pacemaker do Linux e dispositivos SBD, conforme descrito em [alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Caminhos de comunicação entre VMs do Azure e SOFS (Windows Server Servidor de Arquivos de Escalabilidade Horizontal) configurados conforme descrito em [cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um compartilhamento de arquivos no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Dispositivos de rede virtual em caminhos de comunicação podem facilmente dobrar a latência de rede entre dois parceiros de comunicação. Eles também podem restringir a taxa de transferência em caminhos críticos entre a camada do aplicativo SAP e a camada do DBMS. Em alguns cenários de clientes, as soluções de virtualização de rede podem fazer com que os clusters do pacemaker Linux falhem. Esses são casos em que as comunicações entre os nós do cluster pacemaker do Linux se comunicam com o dispositivo SBD por meio de uma solução de virtualização de rede.
>

> [!IMPORTANT]
> Outro design *sem* suporte é a diferenciação da camada do aplicativo SAP e da camada DBMS em diferentes redes virtuais do Azure que não são [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre si. É recomendável separar a camada do aplicativo SAP e a camada do DBMS usando sub-redes em uma rede virtual do Azure em vez de usar diferentes redes virtuais do Azure. 
>
> Se você decidir não seguir a recomendação e, em vez disso, separar as duas camadas em redes virtuais diferentes, as duas redes virtuais deverão ser [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Lembre-se de que o tráfego de rede entre duas redes virtuais [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) do Azure está sujeito a custos de transferência. Um grande volume de dados que consiste em muitos terabytes é trocado entre a camada do aplicativo SAP e a camada do DBMS. Você pode acumular custos substanciais se a camada do aplicativo SAP e a camada do DBMS estiverem separadas entre duas redes virtuais emparelhadas do Azure.

Use duas VMs para sua implantação de DBMS de produção em um conjunto de disponibilidade do Azure. Use também roteamento separado para a camada de aplicativo SAP e o tráfego de gerenciamento e operações para as duas VMs DBMS. Veja a imagem seguinte:

![Diagrama de duas VMs em duas sub-redes](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Usar Azure Load Balancer para redirecionar o tráfego
O uso de endereços IP virtuais privados usados em funcionalidades como SQL Server Always On ou a replicação do sistema HANA requer a configuração de um Azure Load Balancer. O balanceador de carga usa portas de investigação para determinar o nó do DBMS ativo e rotear o tráfego exclusivamente para esse nó de banco de dados ativo. 

Se houver um failover do nó do banco de dados, não há necessidade de o aplicativo SAP reconfigurar. Em vez disso, as arquiteturas de aplicativos SAP mais comuns se reconectam ao endereço IP virtual privado. Enquanto isso, o balanceador de carga reage ao failover de nó redirecionando o tráfego para o endereço IP virtual privado para o segundo nó.

O Azure oferece dois [SKUs de balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)diferentes: um SKU básico e um SKU Standard. A menos que você queira implantar nas zonas de disponibilidade do Azure, o SKU do Load Balancer básico funciona bem.

O tráfego entre as VMs do DBMS e a camada do aplicativo SAP sempre é roteado pelo balanceador de carga o tempo todo? A resposta depende de como você configura o balanceador de carga. 

Neste momento, o tráfego de entrada para a VM do DBMS é sempre roteado pelo balanceador de carga. A rota de tráfego de saída da VM do DBMS para a VM da camada de aplicativo depende da configuração do balanceador de carga. 

O balanceador de carga oferece uma opção de DirectServerReturn. Se essa opção estiver configurada, o tráfego direcionado da VM do DBMS para a camada de aplicativo SAP *não* será roteado pelo balanceador de carga. Em vez disso, ele vai diretamente para a camada de aplicativo. Se DirectServerReturn não estiver configurado, o tráfego de retorno para a camada de aplicativo SAP será roteado por meio do balanceador de carga.

Recomendamos que você configure o DirectServerReturn em combinação com balanceadores de carga posicionados entre a camada do aplicativo SAP e a camada do DBMS. Essa configuração reduz a latência de rede entre as duas camadas.

Para obter um exemplo de como configurar essa configuração com SQL Server Always On, consulte [configurar um ouvinte de ILB para grupos de disponibilidade Always on no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Se você usar modelos JSON do GitHub publicados como uma referência para suas implantações de infraestrutura do SAP no Azure, estude este [modelo para um sistema SAP de três camadas](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Neste modelo, você também pode ver as configurações corretas para o balanceador de carga.

### <a name="azure-accelerated-networking"></a>Rede acelerada do Azure
Para reduzir ainda mais a latência de rede entre as VMs do Azure, recomendamos que você escolha [rede acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Use-o ao implantar VMs do Azure para uma carga de trabalho do SAP, especialmente para a camada de aplicativo SAP e a camada do DBMS SAP.

> [!NOTE]
> Nem todos os tipos de VM dão suporte à rede acelerada. O artigo anterior lista os tipos de VM que dão suporte à rede acelerada.
>

---
> ![Windows][Logo_Windows] Windows
>
> Para saber como implantar VMs com rede acelerada para Windows, consulte [criar uma máquina virtual do Windows com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Para obter mais informações sobre a distribuição do Linux, consulte [criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> No caso do SUSE, do Red Hat e do Oracle Linux, a rede acelerada tem suporte com versões recentes. Versões mais antigas, como SLES 12 SP2 ou RHEL 7,2, não dão suporte à rede acelerada do Azure.
>


## <a name="deployment-of-host-monitoring"></a>Implantação do monitoramento de host
Para uso em produção de aplicativos SAP em máquinas virtuais do Azure, o SAP requer a capacidade de obter dados de monitoramento de host dos hosts físicos que executam as máquinas virtuais do Azure. Um nível de patch do agente de host SAP específico é necessário para habilitar esse recurso no SAPOSCOL e no agente de host do SAP. O nível exato de patch está documentado na observação SAP [1409604].

Para obter mais informações sobre a implantação de componentes que fornecem dados do host para o SAPOSCOL e o agente de host do SAP e o gerenciamento do ciclo de vida desses componentes, consulte o [Guia de implantação][deployment-guide].


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre um determinado DBMS, consulte:

- [Implementação em SQL Server do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_sqlserver.md)
- [Implementação em Oracle do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_oracle.md)
- [Implementação em IBM DB2 do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_ibm.md)
- [Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_sapase.md)
- [SAP maxDB, cache dinâmico e implantação de servidor de conteúdo no Azure](dbms_guide_maxdb.md)
- [Manual de operações do SAP HANA no Azure](hana-vm-operations.md)
- [SAP HANA alta disponibilidade para máquinas virtuais do Azure](sap-hana-availability-overview.md)
- [Guia de backup para SAP HANA em máquinas virtuais do Azure](sap-hana-backup-guide.md)

