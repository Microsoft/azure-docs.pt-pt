---
title: 'Início rápido: Instalação manual do SAP HANA de instância única em máquinas de virtuais do Azure | Documentos da Microsoft'
description: Guia de início rápido para a instalação manual do SAP HANA de instância única em máquinas de virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 635f519239ad00df8672472bcb6c54e6b8a59927
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001698"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Início rápido: Instalação manual de instância única SAP HANA em VMs do Azure
## <a name="introduction"></a>Introdução
Este guia ajuda-o a configurar uma instância única de SAP HANA em máquinas virtuais do Azure (VMs), quando instala 7.5 do SAP NetWeaver e SAP HANA 1.0 SP12 manualmente. O foco deste guia é sobre como implementar o SAP HANA no Azure. Ele não substitui a documentação do SAP. 

>[!Note]
>Este guia descreve as implementações do SAP HANA em VMs do Azure. Para obter informações sobre como implementar o SAP HANA nas instâncias grandes do HANA, consulte [utilizar o SAP nas máquinas virtuais do Azure (VMs)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Pré-requisitos
Este guia assume que está familiarizado com essa infraestrutura como um Noções básicas do serviço (IaaS) como:
 * Como implementar máquinas virtuais ou redes virtuais através do portal do Azure ou o PowerShell.
 * A interface do Azure para várias plataformas da linha de comandos (CLI), incluindo a opção para utilizar modelos de JavaScript Object Notation (JSON).

Este guia também assume que está familiarizado com:
* SAP HANA e SAP NetWeaver e como instalá-los no local.
* Instalar e utilizar o SAP HANA e instâncias de aplicações SAP no Azure.
* Os seguintes conceitos e procedimentos:
   * Planear a implementação de SAP no Azure, incluindo o planeamento de rede Virtual do Azure e a utilização de armazenamento do Azure. Ver [SAP NetWeaver em máquinas virtuais do Azure (VMs) - guia de planeamento e implementação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Princípios de implementação e formas de implementar VMs no Azure. Ver [implementação de máquinas virtuais do Azure para SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Elevada disponibilidade para SAP NetWeaver ASCS (ABAP SAP Central Services), SCS (SAP Central Services) e ERS (colocar em fila de replicação servidor) no Azure. Ver [elevada disponibilidade para SAP NetWeaver em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Obter detalhes sobre como melhorar a eficiência de tirar partido de uma instalação de múltiplos SID do ASCS/SCS no Azure. Ver [criar uma configuração de múltiplos SID SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Princípios da execução do SAP NetWeaver, com base em VMs baseadas em Linux no Azure. Ver [com o SAP NetWeaver em VMs do Linux do Microsoft Azure SUSE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Este guia fornece definições específicas para Linux em VMs do Azure e detalhes sobre como corretamente anexar discos de armazenamento do Azure a VMs do Linux.

Os tipos de VM do Azure que podem ser utilizados para cenários de produção estão listados na [documentação de SAP para o IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, uma maior variedade de tipos nativos de VM do Azure está disponível.
Para obter mais detalhes na VM de configuração e operações, consulte o documento [configurações de infraestrutura de SAP HANA e operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Para o SAP HANA de elevada disponibilidade, consulte [elevada disponibilidade de SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Se necessita de obter uma instância do SAP HANA ou S/4HANA ou sistema BW/4HANA implementado no tempo extremamente rápido, deve considerar a utilização de [SAP Cloud Appliance Library](https://cal.sap.com). Pode encontrar documentação sobre como implementar, por exemplo, um sistema S/4HANA através de SAP CAL no Azure no [este guia](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Tudo o que precisa ter é uma subscrição do Azure e um utilizador SAP que pode ser registado com o SAP Cloud Appliance Library.

## <a name="additional-resources"></a>Recursos adicionais
### <a name="sap-hana-backup"></a>Cópia de segurança do SAP HANA
Para obter informações sobre a criação de segurança das bases de dados do SAP HANA em VMs do Azure, consulte:
* [Guia de segurança para SAP HANA em máquinas de virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Cópia de segurança do SAP HANA do Azure no nível de ficheiro](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [Cópia de segurança do SAP HANA com base nos instantâneos de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>Biblioteca de aplicações SAP na Cloud
Para obter informações sobre como utilizar o SAP Cloud Appliance Library para implementar o S/4HANA ou BW/4HANA, consulte [SAP implementar S/4HANA ou BW/4HANA no Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Sistemas de operativos suportadas para HANA SAP
Para obter informações sobre sistemas operativos suportados para SAP HANA, consulte [2235581 de n. º de nota de suporte de SAP - SAP HANA: Sistemas operativos suportados](https://launchpad.support.sap.com/#/notes/2235581/E). VMs do Azure suportam apenas um subconjunto desses sistemas operacionais. Os seguintes sistemas operativos são suportados para implementar o SAP HANA no Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Para obter documentação de SAP adicional sobre o SAP HANA diferentes sistemas operativos e Linux, consulte:

* [SAP suporte Nota #171356 - Software SAP no Linux:  Informações gerais](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP suporte Nota #1944799 - SAP HANA diretrizes para instalação de sistema operativo do SLES](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [Suporte a nota SAP #2205917 - SAP HANA DB recomendado configurações de SO para o SLES 12 para aplicativos de SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
* [Nota SAP de suporte #1984787 - SUSE Linux Enterprise Server 12:  Observações de instalação](https://launchpad.support.sap.com/#/notes/1984787)
* [Suporte a nota SAP #1391070 - soluções do UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP suporte Nota #2009879 - SAP HANA diretrizes para o sistema operativo do Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 - SAP HANA DB: Definições de sistema operacional recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>SAP monitorização no Azure
Para obter informações sobre o SAP monitorização no Azure, consulte:

* [A nota SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E). Esta nota aborda SAP "aprimorada de monitorização" com VMs do Linux no Azure. 
* [A nota SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Esta nota aborda informações sobre SAPOSCOL no Linux. 
* [A nota SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Esta nota discute as principais métricas monitorização para SAP no Microsoft Azure.

### <a name="azure-vm-types"></a>Tipos de VM do Azure
Tipos de VM do Azure e cenários SAP suportados a carga de trabalho utilizados com o SAP HANA estão documentados em [plataformas IaaS com certificação SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Tipos VM do Azure que são certificados pelo SAP para o SAP NetWeaver ou a camada de aplicativo de S/4HANA estão documentados em [SAP Note 1928533 – aplicações SAP no Azure: Produtos suportados e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533/E).

>[!Note]
>Integração da SAP-Linux-Azure é suportada apenas no Azure Resource Manager e não o modelo de implementação clássica. 

## <a name="manual-installation-of-sap-hana"></a>Instalação manual do SAP HANA

> [!IMPORTANT]
> Certifique-se de que o sistema operacional seleciona é SAP certificado para SAP HANA em determinados tipos VM que está a utilizar. A lista de SAP HANA certificadas de tipos de VM e o sistema operacional libera para aqueles podem ser pesquisados [plataformas de IaaS com certificação do SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Certifique-se de clicar em detalhes sobre o tipo de VM listados para obter a lista completa de SAP HANA suportadas versões do SO para o tipo VM específico. Tenha em atenção que o exemplo neste documento usamos uma versão de SO de SLES que não é suportada pelo SAP para o SAP HANA nas VMs de série M.
>

Este guia descreve como instalar manualmente o SAP HANA em VMs do Azure de duas formas diferentes:

* Ao utilizar o Gestor de aprovisionamento de Software SAP (SWPM) como parte de uma instalação distribuída do NetWeaver no passo "instância de base de dados de instalação"
* Ao utilizar o SAP HANA de base de dados ferramenta do Gerenciador de ciclo de vida, HDBLCM e, em seguida, instalar NetWeaver

Também pode utilizar SWPM para instalar todos os componentes (SAP HANA, o servidor de aplicações SAP e a instância do ASCS) numa única VM, conforme descrito neste [anúncio do blogue de SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Esta opção não esteja descrita neste guia de início rápido, mas os problemas que deve levar em consideração são os mesmos.

Antes de iniciar uma instalação, recomendamos que leia o "Azure Preparando para VMs durante a instalação manual do SAP HANA" seção mais adiante neste guia. Se o fizer, pode ajudar a evitar que vários erros básicos que podem ocorrer ao utilizar apenas uma configuração de VM do Azure padrão.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Principais etapas para a instalação de SAP HANA ao utilizar o SAP SWPM
Esta secção lista as principais etapas para uma instalação manual, a única instância do SAP HANA ao utilizar o SAP SWPM para efetuar uma instalação distribuída do SAP NetWeaver 7.5. Os passos individuais são explicados em mais detalhes nas capturas de ecrã mais tarde neste guia.

1. Crie uma rede virtual do Azure, que inclui o teste de duas VMs.
2. Implemente duas VMs do Azure com sistemas operativos (no nosso exemplo, SUSE Linux Enterprise Server (SLES) e SLES para SAP aplicativos 12 SP1), de acordo com o modelo Azure Resource Manager.
3. Anexe dois padrão do Azure ou discos de armazenamento premium (por exemplo, discos de 75 GB ou 500 GB) para a VM do servidor de aplicação.
4. Anexe discos de armazenamento premium para VM do servidor de DB do HANA. Para obter detalhes, consulte a secção "Configuração de disco" mais adiante neste guia.
5. Dependendo dos requisitos de tamanho ou débito, anexar vários discos e, em seguida, criar volumes repartidos com gestão de volumes lógicos ou uma ferramenta de administração de vários dispositivos (MDADM) ao nível do SO dentro da VM.
6. Crie sistemas de ficheiros XFS na lógicas volumes ou discos ligados.
7. Monte os novos sistemas de ficheiros XFS ao nível do SO. Utilize um sistema de ficheiros de todo o software da SAP. Utilize o sistema de ficheiros para o diretório de /sapmnt e cópias de segurança, por exemplo. No servidor de BD SAP HANA, monte os sistemas de ficheiros XFS nos discos de armazenamento premium como /hana e /usr/sap. Este processo é necessário para impedir que o sistema de ficheiros de raiz, que não é grande em VMs do Linux do Azure, a ser preenchida.
8. Introduza os endereços IP locais do teste VMs no ficheiro /etc/hosts.
9. Introduza o **nofail** parâmetro v souboru/etc/fstab.
10. Defina parâmetros de kernel do Linux, de acordo com a versão de SO Linux que está a utilizar. Para obter mais informações, consulte as notas SAP apropriadas que discutem HANA e a secção "Parâmetros de Kernel" neste guia.
11. Adicione espaço de comutação.
12. Opcionalmente, instale uma área de trabalho gráfica no teste de VMs. Caso contrário, utilize uma instalação de SAPinst remota.
13. Baixe o software SAP do SAP Service Marketplace.
14. Instale a instância do SAP ASCS no servidor da aplicação VM.
15. Partilhe o diretório de /sapmnt entre o teste de VMs ao utilizar o NFS. A VM do servidor de aplicação é o servidor NFS.
16. Instale a instância de base de dados, incluindo o HANA, utilizando SWPM na VM do servidor de DB.
17. Instale o servidor de aplicação principal (PAS) no servidor de aplicativos VM.
18. Inicie o Console de gerenciamento de SAP (SAP MC). Estabelecer ligação com a GUI de SAP ou HANA Studio, por exemplo.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Principais etapas para a instalação de SAP HANA ao utilizar HDBLCM
Esta secção lista as principais etapas para uma instalação manual, a única instância do SAP HANA ao utilizar o SAP HDBLCM para efetuar uma instalação distribuída do SAP NetWeaver 7.5. Os passos individuais são explicados em mais detalhes nas capturas de ecrã ao longo deste guia.

1. Crie uma rede virtual do Azure, que inclui o teste de duas VMs.
2. Implemente duas VMs do Azure com sistemas operativos (no nosso exemplo, SLES e SLES para SAP aplicativos 12 SP1), de acordo com o modelo Azure Resource Manager.
3. Anexe dois padrão do Azure ou discos de armazenamento premium (por exemplo, discos de 75 GB ou 500 GB) para a VM do servidor de aplicação.
4. Anexe discos de armazenamento premium para VM do servidor de DB do HANA. Para obter detalhes, consulte a secção "Configuração de disco" mais adiante neste guia.
5. Dependendo dos requisitos de tamanho ou débito, anexar vários discos e criar volumes repartidos com gestão de volumes lógicos ou uma ferramenta de administração de vários dispositivos (MDADM) ao nível do SO dentro da VM.
6. Crie sistemas de ficheiros XFS na lógicas volumes ou discos ligados.
7. Monte os novos sistemas de ficheiros XFS ao nível do SO. Utilize um sistema de ficheiros de todo o software da SAP e utilizar a outra para o diretório de /sapmnt e cópias de segurança, por exemplo. No servidor de BD SAP HANA, monte os sistemas de ficheiros XFS nos discos de armazenamento premium como /hana e /usr/sap. Este processo é necessário para ajudar a impedir que o sistema de ficheiros de raiz, que não é grande em VMs do Linux do Azure, a ser preenchida.
8. Introduza os endereços IP locais do teste VMs no ficheiro /etc/hosts.
9. Introduza o **nofail** parâmetro v souboru/etc/fstab.
10. Definir parâmetros de kernel, de acordo com a versão de SO Linux que está a utilizar. Para obter mais informações, consulte as notas SAP apropriadas que discutem HANA e a secção "Parâmetros de Kernel" neste guia.
11. Adicione espaço de comutação.
12. Opcionalmente, instale uma área de trabalho gráfica no teste de VMs. Caso contrário, utilize uma instalação de SAPinst remota.
13. Baixe o software SAP do SAP Service Marketplace.
14. Crie um grupo, sapsys, com o grupo de ID de 1001, na VM do servidor de DB do HANA.
15. Instale o SAP HANA na VM do servidor de DB com o Gestor de ciclo de vida de base de dados do HANA (HDBLCM).
16. Instale a instância do SAP ASCS no servidor da aplicação VM.
17. Partilhe o diretório de /sapmnt entre o teste de VMs ao utilizar o NFS. A VM do servidor de aplicação é o servidor NFS.
18. Instale a instância de base de dados, incluindo o HANA, utilizando SWPM na VM do servidor de DB do HANA.
19. Instale o servidor de aplicação principal (PAS) no servidor de aplicativos VM.
20. Inicie o SAP MC. Ligue-se através de SAP GUI ou HANA Studio.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Preparar as VMs do Azure para uma instalação manual do SAP HANA
Esta secção abrange os seguintes tópicos:

* Atualizações do SO
* Configuração de disco
* Parâmetros de kernel
* Sistemas de ficheiros
* O ficheiro de anfitriões/etc /
* O ficheiro/etc/fstab

### <a name="os-updates"></a>Atualizações do SO
Verificação de atualizações de SO Linux e correções antes de instalar software adicional. Ao instalar um patch, poderá evitar uma chamada para o suporte técnico de suporte.

Certifique-se de que está a utilizar:
* SUSE Linux Enterprise Server para aplicações SAP.
* Red Hat Enterprise Linux para aplicações SAP ou Red Hat Enterprise Linux para o SAP HANA. 

Se ainda não o fez, registe-se a implementação do SO com a sua subscrição de Linux do fornecedor do Linux. Observe que o SUSE tem imagens do sistema operacional para aplicações SAP que já incluem os serviços e que é registrado automaticamente.

Eis um exemplo de como a verificação de patches disponíveis para o SUSE Linux ao utilizar o **zypper** comando:

 `sudo zypper list-patches`

Dependendo do tipo de problema, os patches são classificados por categoria e gravidade. Valores usadas para a categoria são: **security**, **recomendado**, **opcional**, **funcionalidade**, **documento**, ou **yast**.
São frequentemente utilizados valores para a gravidade: **críticas**, **importante**, **moderado**, **baixa**, ou **unspecified**.

O **zypper** comando procura apenas as atualizações que precisam dos pacotes instalados. Por exemplo, pode utilizar este comando:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Pode adicionar o parâmetro `--dry-run` para testar a atualização sem, na verdade, atualizar o sistema.


### <a name="disk-setup"></a>Configuração de disco
O sistema de ficheiros de raiz numa VM do Linux no Azure tem um limite de tamanho. Portanto, é necessário anexar o espaço em disco adicional para uma VM do Azure para executar o SAP. Para o servidor de aplicações SAP VMs do Azure, a utilização de discos de armazenamento standard do Azure pode ser suficiente. No entanto, para as VMs do Azure do SAP HANA DBMS, a utilização de discos de armazenamento Premium do Azure para implementações de produção e que não seja de produção é obrigatória.

Com base na [requisitos de armazenamento do SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), é aconselhável a seguinte configuração de armazenamento Premium do Azure: 

| SKU DE VM | RAM |  / hana/dados e/hana/do registo <br /> repartidos com LVM ou MDADM | /hana/shared | volume de /Root | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

A configuração do disco sugeridas, o volume de dados do HANA e o volume de registo são colocados no mesmo conjunto de discos de armazenamento premium do Azure que são distribuídos com LVM ou MDADM. Não é necessário definir qualquer nível de redundância RAID, porque o armazenamento Premium do Azure mantém três imagens dos discos para redundância. Para certificar-se de que configure o armazenamento suficiente, consulte a [requisitos de armazenamento TDI do SAP HANA](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) e [guia de atualização e de instalação do servidor do SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Considere também os volumes de débito de disco rígido virtual (VHD) diferente dos discos de armazenamento premium do Azure diferente conforme documentado [High-performance Premium Storage e os discos geridos para VMs](../../windows/disks-types.md). 

Pode adicionar mais discos de armazenamento premium para as VMs do HANA DBMS para armazenar cópias de segurança da base de dados ou a transação de registo.

Para obter mais informações sobre as duas ferramentas principais utilizado para configurar a repartição, consulte os artigos seguintes:

* [Configurar o RAID de software no Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configurar LVM numa VM do Linux no Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Para obter mais informações sobre a anexar discos para VMs do Azure com o Linux como um SO convidado, consulte [adicionar um disco a uma VM do Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Premium do Azure SSDs permite-lhe definir modos da colocação em cache. Para o conjunto repartido reter /hana/data e /hana/log, colocação em cache do disco deve ser desativada. Para os outros volumes (discos), o modo de colocação em cache deve ser definido como **só de leitura**.

Para encontrar modelos de JSON de exemplo para criar VMs, aceda a [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
O modelo de vm-simples-sles é um modelo básico. Ele inclui uma seção de armazenamento, com um disco de dados de 100 GB adicionais. Este modelo pode ser utilizado como base. Pode adaptar o modelo para a configuração específica.

>[!Note]
>É importante anexar o disco de armazenamento do Azure com um UUID conforme documentado [a executar o SAP NetWeaver em VMs de Linux do Microsoft Azure SUSE](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

No ambiente de teste, foram anexados os discos de dois armazenamento standard do Azure para o servidor de aplicações SAP VM, conforme mostrado na captura de ecrã seguinte. Um disco armazenadas todo o software SAP (incluindo NetWeaver 7.5 e SAP HANA SAP GUI) para a instalação. O segundo disco garantiu que seria disponível para requisitos adicionais (por exemplo, dados de cópia de segurança e de teste) e para o diretório de /sapmnt (ou seja, perfis SAP) a ser partilhado entre todas as VMs que pertencem a paisagem SAP mesmo espaço livre suficiente.

![Exibição de dois discos de dados e seus tamanhos dos janela de discos de servidor para aplicação SAP HANA](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parâmetros de kernel
SAP HANA requer definições de kernel Linux específicas, que não fazem parte das imagens da galeria do Azure standard e tem de ser definidas manualmente. Consoante utilize ou SUSE ou Red Hat, os parâmetros podem ser diferentes. As notas de SAP listadas anteriormente fornecer informações sobre esses parâmetros. Nas capturas de ecrã mostradas, SUSE Linux 12 SP1 foi utilizado. 

SLES para SAP aplicativos 12 GA e SLES para SAP aplicativos 12 SP1 têm uma nova ferramenta **adm ajustado**, que substitui o antigo **sapconf** ferramenta. Um perfil especial do SAP HANA está disponível para **adm ajustado**. Para otimizar o sistema para o SAP HANA, introduza o seguinte como um utilizador de raiz:

   `tuned-adm profile sap-hana`

Para obter mais informações sobre **adm ajustado**, consulte a [documentação SUSE sobre adm ajustado](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Captura de ecrã seguinte, pode ver como **adm ajustado** alterado a `transparent_hugepage` e `numa_balancing` valores, de acordo com as definições necessárias do SAP HANA.

![A ferramenta do adm ajustado altera os valores de acordo com as definições necessárias do SAP HANA](./media/hana-get-started/image005.jpg)

Para tornar as definições de kernel do SAP HANA permanentes, utilize **grub2** no SLES 12. Para obter mais informações sobre **grub2**, aceda ao [estrutura de ficheiros de configuração](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) seção da documentação do SUSE.

Captura de ecrã seguinte mostra como as definições de kernel foram alteradas no ficheiro de configuração e, em seguida, compiladas usando **grub2 mkconfig**:

![Definições de kernel foi alterado no ficheiro de configuração e compilado usando grub2 mkconfig](./media/hana-get-started/image006.jpg)

Outra opção consiste em alterar as definições utilizando YaST e o **carregador de inicialização** > **parâmetros de Kernel** definições:

![O separador de definições de parâmetros de Kernel no carregador de inicialização do YaST](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Sistemas de ficheiros
Captura de ecrã seguinte mostra os dois sistemas de ficheiros que foram criados no servidor de aplicação SAP VM sobre os dois discos de armazenamento standard do Azure ligado. Ambos os sistemas de ficheiros são do tipo XFS e são montados /sapdata e /sapsoftware.

Não é obrigatório para estruturar os seus sistemas de ficheiros dessa forma. Há outras opções para estruturar o espaço em disco. A consideração mais importante é impedir que o sistema de ficheiros de raiz de falta de espaço livre.

![Dois sistemas de ficheiros criados no servidor de aplicação SAP VM](./media/hana-get-started/image008.jpg)

Sobre a VM de DB do SAP HANA, durante a instalação de uma base de dados, ao utilizar SAPinst (SWPM) e o **típico** opção de instalação, tudo o que é instalado em /hana e /usr/sap. A localização predefinida para a cópia de segurança do registo do SAP HANA está sob /usr/sap. Novamente, uma vez que é importante impedir que o sistema de ficheiros de raiz ficar sem espaço de armazenamento, certifique-se de que existe espaço livre suficiente em /hana e /usr/sap antes de instalar o SAP HANA com o SWPM.

Para obter uma descrição do layout padrão do sistema de ficheiros do SAP HANA, consulte a [guia de atualização e de instalação do servidor do SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Sistemas de ficheiros adicionais criados no servidor de aplicação SAP VM](./media/hana-get-started/image009.jpg)

Quando instala o SAP NetWeaver num SLES/SLES padrão para a imagem da galeria do Azure de 12 de aplicações SAP, é apresentada uma mensagem que diz que não há nenhum espaço de comutação, conforme mostrado na captura de ecrã seguinte. Para dispensar esta mensagem, pode adicionar manualmente um ficheiro de comutação utilizando **dd**, **mkswap**, e **swapon**. Para saber como, pesquisar por "Adicionar um ficheiro de comutação manualmente" no [usando o Particionador YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) seção da documentação do SUSE.

Outra opção consiste em Configurar espaço de comutação com o agente de VM do Linux. Para obter mais informações, consulte a [guia de utilizador do agente Azure Linux](../../extensions/agent-linux.md).

![Mensagem de pop-up aconselhá que existe espaço de comutação insuficiente](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>O ficheiro de anfitriões/etc /
Antes de começar a instalar o SAP, certifique-se de que incluir os nomes de anfitrião e os endereços IP das VMs de SAP no arquivo /etc/hosts. Implementar todas as VMs de SAP dentro de uma rede virtual do Azure e, em seguida, utilize os endereços IP internos, conforme mostrado aqui:

![Os nomes de anfitrião e endereços IP das VMs de SAP listados no arquivo /etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>O ficheiro/etc/fstab

É útil adicionar o **nofail** parâmetro para o ficheiro fstab. Dessa forma, se algo der errado com os discos, a VM não aguente o processo de inicialização. Mas lembre-se de que o espaço em disco adicional pode não estar disponível e processos podem preencher o sistema de ficheiros de raiz. Se /hana está em falta, não inicie o SAP HANA.

![Adicione o parâmetro de nofail para o ficheiro fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Ambiente de trabalho GNOME gráfica no SLES 12/SLES para SAP aplicativos 12
Esta secção abrange os seguintes tópicos:

* Instalar o GNOME desktop e o xrdp no SLES 12/SLES para SAP aplicativos 12
* MC de SAP baseadas em Java em execução com o Firefox SLES 12/SLES para SAP aplicativos 12

Também pode utilizar alternativas como Xterminal ou VNC (não descrito neste guia).

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Instalar o GNOME desktop e o xrdp no SLES 12/SLES para SAP aplicativos 12
Se tiver um plano de fundo do Windows, pode facilmente utilizar uma área de trabalho gráfica diretamente nas VMs do Linux SAP para executar o Firefox, SAPinst, GUI do SAP, SAP MC ou HANA Studio e ligar à VM através do protocolo RDP (Remote Desktop), a partir de um computador Windows. Sistemas baseados em depende das políticas da sua empresa sobre como adicionar interfaces de usuário gráfica para produção e do Linux de não produção, pode querer instalar GNOME no seu servidor. Para instalar a área de trabalho GNOME num Azure SLES 12/SLES para SAP aplicativos 12 VM:

1. Instale a área de trabalho GNOME introduzindo o seguinte comando (por exemplo, numa janela PuTTY):

   `zypper in -t pattern gnome-basic`

2. Instale xrdp para permitir uma conexão para a VM através do RDP:

   `zypper in xrdp`

3. Editar /etc/sysconfig/windowmanager e defina o Gerenciador de janelas padrão para GNOME:

   `DEFAULT_WM="gnome"`

4. Execute **chkconfig** para se certificar de que esse xrdp inicia automaticamente após um reinício:

   `chkconfig -level 3 xrdp on`

5. Se tiver um problema com a ligação de RDP, tente reiniciar o (a partir de uma janela do PuTTY, por exemplo):

   `/etc/xrdp/xrdp.sh restart`

6. Se uma reinicialização xrdp mencionada no passo anterior não funcionar, verifique se existem um ficheiro de .pid:

   `check /var/run` 

   Procure `xrdp.pid`. Se encontrá-lo, removê-lo e tente novamente a reiniciar.

### <a name="starting-sap-mc"></a>A partir de SAP MC
Depois de instalar a área de trabalho GNOME, a SAP MC gráfica baseadas em Java a partir de Firefox durante a execução num valor de erro do Azure SLES 12/SLES para SAP aplicativos 12 VM poderá apresentar um erro devido o falta Java-browser Plug-in.

O URL para iniciar o MC SAP é `<server>:5<instance_number>13`.

Para obter mais informações, consulte [iniciar a consola de gestão baseado na Web do SAP](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Captura de ecrã seguinte mostra a mensagem de erro é apresentada quando o plug-in de Java-browser está em falta:

![Mensagem de erro que indica em falta Java-browser Plug-in](./media/hana-get-started/image013.jpg)

Uma forma de resolver o problema é para instalar o em falta Plug-in utilizando YaST, conforme mostrado na captura de ecrã seguinte:

![Usando YaST para instalar o plug-in tem em falta](./media/hana-get-started/image014.jpg)

Quando voltar a introduzir o URL de consola de gestão do SAP, é apresentada uma mensagem a pedir-lhe para ativar o plug-in:

![Caixa de diálogo solicitando a ativação do plug-in](./media/hana-get-started/image015.jpg)

Também poderá receber uma mensagem de erro sobre um ficheiro em falta, javafx.properties. Isso está relacionado com os requisitos do Oracle em Java 1.8 para SAP GUI 7.4. (Consulte [a nota SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424).) Nem a versão do IBM Java nem o pacote de openjdk pré-instaladas SLES/SLES para SAP aplicativos 12 inclui o ficheiro de javafx.properties necessários. A solução é transferir e instalar o Java SE 8 da Oracle.

Para obter informações sobre um problema similar com openjdk openSUSE, consulte o thread de discussão [SAPGui 7.4 Java para openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Instalação manual do SAP HANA: SWPM
A série de capturas de ecrã nesta secção mostra as principais etapas para a instalação 7.5 do SAP NetWeaver e SAP HANA SP12 quando utiliza SWPM (SAPinst). Como parte de uma instalação do NetWeaver 7.5, SWPM também pode instalar a base de dados do HANA como uma única instância.

Num ambiente de teste de exemplo, podemos instalado apenas um servidor de aplicações avançadas Business Application Programming (ABAP). Conforme mostrado na captura de ecrã seguinte, usamos o **sistema distribuído** opção para instalar as instâncias de servidor de aplicação principal e ASCS numa VM do Azure e o SAP HANA, como o sistema de base de dados em outra VM do Azure.

![ASCS e instâncias do servidor principal da aplicação instaladas através da opção de sistema distribuído](./media/hana-get-started/image012.jpg)

Depois da instância do ASCS é instalada na VM do servidor de aplicação e é definido como "verde" na consola de gestão do SAP (mostrado na seguinte captura de ecrã), o diretório de /sapmnt (incluindo o diretório de perfil do SAP) tem de ser partilhado com a VM do servidor de BD SAP HANA. O passo de instalação de DB precisa de aceder a estas informações. É a melhor maneira de fornecer acesso utilizar o NFS, que pode ser configurado utilizando YaST.

![Console de gerenciamento de SAP que mostra a instância do ASCS instalado na VM do servidor de aplicação e definido como "verde"](./media/hana-get-started/image016.jpg)

No servidor da aplicação VM, o diretório de /sapmnt deve ser partilhado por meio de NFS, utilizando o **rw** e **no_root_squash** opções. As predefinições são **ro** e **root_squash**, que podem originar problemas ao instalar a instância de base de dados.

![Partilhar o diretório de /sapmnt por meio de NFS, utilizando as opções de rw e no_root_squash](./media/hana-get-started/image017b.jpg)

Como mostra a captura de ecrã seguinte, a partilha de /sapmnt da VM do servidor de aplicação tem de ser configurada no servidor do SAP HANA DB VM utilizando **cliente NFS** (e YaST).

![A partilha de /sapmnt configurada utilizando o cliente NFS](./media/hana-get-started/image018b.jpg)

Para efetuar uma instalação distribuída do NetWeaver 7.5 (**instância de base de dados**), conforme mostrado na captura de ecrã seguinte, inicie sessão para a VM do servidor de BD SAP HANA e inicie SWPM.

![Instalar uma instância de base de dados ao iniciar sessão na VM do servidor de BD SAP HANA e ao iniciá SWPM](./media/hana-get-started/image019.jpg)

Depois de selecionar **típico** instalação e o caminho para a mídia de instalação, introduza um SID de DB, o nome de anfitrião, o número de instância e a palavra-passe de administrador do sistema de DB.

![A SAP HANA página base de dados system administrador início de sessão](./media/hana-get-started/image035b.jpg)

Introduza a palavra-passe para o esquema DBACOCKPIT:

![A caixa de entrada de palavra-passe para o esquema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Introduza uma pergunta para a palavra-passe do SAPABAP1 esquema:

![Introduza uma pergunta para a palavra-passe do SAPABAP1 esquema](./media/hana-get-started/image037b.jpg)

Depois de cada tarefa está concluída, é apresentada uma marca de verificação verde junto a cada fase do processo de instalação de DB. A mensagem "execução de... A base de dados foi concluída a instância"é apresentada.

![Tarefa concluída janela com a mensagem de confirmação](./media/hana-get-started/image023.jpg)

Após a instalação bem-sucedida, o Console de gerenciamento de SAP também deve mostrar a instância de BD como "verde" e apresentar a lista completa dos processos de SAP HANA (hdbindexserver hdbcompileserver e assim por diante).

![Janela de consola de gestão do SAP com a lista de processos de SAP HANA](./media/hana-get-started/image024.jpg)

Captura de ecrã seguinte mostra as partes da estrutura de arquivo sob o diretório de /hana/shared SWPM criado durante a instalação do HANA. Porque não existe nenhuma opção para especificar um caminho diferente, é importante montar o espaço em disco adicional sob o diretório de /hana antes da instalação de SAP HANA com o SWPM. Isto impede que o sistema de ficheiros de raiz ficar sem espaço livre.

![A estrutura de ficheiros do diretório de /hana/shared criada durante a instalação do HANA](./media/hana-get-started/image025.jpg)

Nesta captura de ecrã mostra a estrutura de ficheiros do diretório /usr/sap:

![A/usr/sap ficheiro estrutura de diretórios](./media/hana-get-started/image026.jpg)

A última etapa de instalação de ABAP distribuída é instalar a instância de servidor principal da aplicação:

![Instância de servidor do principal da aplicação do ABAP instalação que mostra como a etapa final](./media/hana-get-started/image027b.jpg)

Depois de instalar a instância de servidor de aplicação principal e a GUI do SAP, utilize o **DBA Cockpit** transação para confirmar que a instalação de SAP HANA foi concluída corretamente:

![Janela de DBA Cockpit confirmar o êxito da instalação](./media/hana-get-started/image028b.jpg)

Como passo final, poderá querer instalar primeiro HANA Studio no servidor de aplicações SAP VM e, em seguida, ligar à instância do SAP HANA que está a executar a VM do servidor de DB:

![Instalação de SAP HANA Studio numa VM com o servidor de aplicação SAP](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Instalação manual do SAP HANA: HDBLCM
Para além de instalar o SAP HANA como parte de uma instalação distribuída utilizando SWPM, pode instalar a autónoma do HANA em primeiro lugar, com HDBLCM. Em seguida, pode instalar SAP NetWeaver 7.5, por exemplo. As capturas de ecrã nesta secção mostram como este processo funciona.

Para obter mais informações sobre a ferramenta de HANA HDBLCM, consulte:

* [Escolher o HDBLCM de HANA SAP correto para a sua tarefa](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [Ferramentas de gerenciamento de ciclo de vida do SAP HANA](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)
* [Guia de atualização e de instalação do servidor do SAP HANA](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Para evitar problemas com uma configuração de ID de grupo padrão para o `\<HANA SID\>adm user` (criado pela ferramenta HDBLCM), definir um novo grupo denominado `sapsys` com o ID de grupo `1001` antes de instalar o SAP HANA via HDBLCM:

![Novo grupo "sapsys" definidas utilizando o ID de 1001 de grupo](./media/hana-get-started/image030.jpg)

Quando inicia HDBLCM pela primeira vez, é apresentado um menu de início simples. Selecione item 1, **instalar o novo sistema**, conforme mostrado na captura de ecrã seguinte:

![Opção "Instalar o novo sistema" na janela de início de HDBLCM](./media/hana-get-started/image031.jpg)

Captura de ecrã seguinte mostra todas as opções de chave que selecionou anteriormente.

> [!IMPORTANT]
> Diretórios que estão com o nome para o registo do HANA e volumes de dados, bem como o caminho de instalação (/ hana/partilhado neste exemplo) e /usr/sap, não devem ser parte do sistema de arquivos de raiz. Esses diretórios pertencerem aos discos de dados do Azure que foram ligados à VM (descrita na secção "Configuração de disco"). Esta abordagem ajuda a impedir que o sistema de ficheiros de raiz em execução sem espaço. Captura de ecrã seguinte, pode ver que o administrador de sistema HANA tem o ID de utilizador `1005` e faz parte dos `sapsys` grupo (ID `1001`) que foi definida antes da instalação.

![Lista de todos os componentes principais de SAP HANA selecionada anteriormente](./media/hana-get-started/image032.jpg)

Pode verificar o `\<HANA SID\>adm user` (`azdadm` na captura de ecrã seguinte) detalhes no diretório de passwd nomedeanfitrião:

![HANA \<HANA SID\>detalhes de utilizador do adm listados no diretório de passwd/etc /](./media/hana-get-started/image033.jpg)

Depois de instalar o SAP HANA com o HDBLCM, pode ver a estrutura de ficheiros no SAP HANA Studio, conforme mostrado na captura de ecrã seguinte. O esquema de SAPABAP1, que inclui todas as tabelas de SAP NetWeaver, ainda não está disponível.

![Estrutura de arquivo do SAP HANA no SAP HANA Studio](./media/hana-get-started/image034.jpg)

Depois de instalar o SAP HANA, pode instalar o SAP NetWeaver com base no mesmo. Conforme mostrado na captura de ecrã seguinte, a instalação foi executada como uma instalação distribuída com SWPM (conforme descrito na secção anterior). Ao instalar a instância de base de dados utilizando SWPM, inserir os mesmos dados ao utilizar HDBLCM (por exemplo, a nome de anfitrião, o SID do HANA e o número de instância). SWPM, em seguida, utiliza a instalação existente do HANA e adiciona mais esquemas.

![Uma instalação distribuída efetuada utilizando SWPM](./media/hana-get-started/image035b.jpg)

Captura de ecrã seguinte mostra o passo de instalação SWPM onde introduzir dados sobre o esquema DBACOCKPIT:

![O passo de instalação de SWPM em que os dados de esquema DBACOCKPIT são inseridos](./media/hana-get-started/image036b.jpg)

Introduza dados sobre o esquema de SAPABAP1:

![Introduzir dados sobre o esquema de SAPABAP1](./media/hana-get-started/image037b.jpg)

Depois de concluída a instalação de instância de base de dados SWPM, pode ver o esquema de SAPABAP1 no SAP HANA Studio:

![O esquema de SAPABAP1 no SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Por fim, depois do servidor de aplicações SAP e instalações de SAP GUI são concluídas, pode verificar a instância de BD do HANA através da **DBA Cockpit** transação:

![A instância de BD do HANA verificada com a transação de DBA Cockpit](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Downloads de software SAP
Pode transferir software a partir do SAP Service Marketplace, conforme mostrado nas capturas de ecrã seguintes.

Transferir NetWeaver 7.5 para o Linux/HANA:

 ![Janela de instalação de serviço do SAP e a atualização por baixar o NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Baixe a edição da plataforma SP12 HANA:

 ![Janela de instalação de serviço do SAP e a atualização para baixar a edição da plataforma SP12 HANA](./media/hana-get-started/image002.jpg)

