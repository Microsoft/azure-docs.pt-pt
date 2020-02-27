---
title: 'Quickstart: Instalação manual de SAP HANA de instância única em Máquinas Virtuais Azure  Microsoft Docs'
description: Guia de arranque rápido para instalação manual de SAP HANA de instância única em Máquinas Virtuais Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 0090ffe977dee3e493d726c9eb4d151bcbeb503f
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617237"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Quickstart: Instalação manual de SAP HANA de instância única em máquinas virtuais azure
## <a name="introduction"></a>Introdução
Este guia ajuda-o a configurar manualmente um SAP HANA de uma só instância nas Máquinas Virtuais Azure quando instalar manualmente o SAP NetWeaver 7.5 e o SAP HANA 1.0 SP12. O foco deste guia é como implantar o SAP HANA no Azure. Não substitui a documentação da SAP. 

> [!NOTE]
> Este guia descreve as implantações da SAP HANA em VMs Azure. Para obter informações sobre como implantar o SAP HANA em grandes instâncias HANA, consulte [Use SAP em Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Pré-requisitos
Este guia assume que está familiarizado com tais infraestruturas como um serviço (IaaS) básicos como:
 * Como implantar máquinas virtuais (VMs) ou redes virtuais através do portal Azure ou PowerShell.
 * A interface de linha de comando de plataforma cruzada Azure (CLI), que inclui a opção de utilizar os modelos de Notação de Objetos JavaScript (JSON).

Este guia também assume que está familiarizado com:
* SAP HANA e SAP NetWeaver e como instalá-los no local.
* Como instalar e operar instâncias de aplicação SAP HANA e SAP no Azure.
* Os seguintes conceitos e procedimentos:
   * Planejamento para implantação de SAP no Azure, que inclui planeamento de rede virtual Azure e utilização de Armazenamento Azure. Consulte [o SAP NetWeaver em Máquinas Virtuais Azure - Guia de Planeamento e Implementação.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * Princípios e formas de implantação de VMs em Azure. Consulte a implantação de [Máquinas Virtuais Azure para SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Alta disponibilidade para SAP NetWeaver ABAP SAP Central Services (ASCS), SAP Central Services (SCS) e Enqueue Replication Server (ERS) no Azure. Consulte [a elevada disponibilidade para SAP NetWeaver em VMs Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Detalhes sobre como melhorar a eficiência numa instalação multi-SID de ASCS/SCS no Azure. Ver [Criar uma configuração multi-SID SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Princípios de funcionamento SAP NetWeaver baseados em VMs orientados por Linux em Azure. Ver [Run SAP NetWeaver no Microsoft Azure SUSE Linux VMs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Este guia fornece configurações específicas para linux em VMs Azure. Também fornece informações sobre como ligar corretamente os discos de armazenamento Azure aos VMs Linux.

Os tipos de VM Azure que podem ser utilizados para cenários de produção estão listados na [documentação SAP para iAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, uma maior variedade de tipos de VM Azure nativos está disponível.
Para obter mais informações sobre a configuração e operações vm, consulte [as configurações e operações de infraestrutura SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Para a alta disponibilidade do SAP HANA, consulte [a alta disponibilidade do SAP HANA para máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Se quiser que seja implementado rapidamente um sistema SAP HANA ou S/4HANA ou BW/4HANA, considere utilizar a Biblioteca de [Eletrodomésticos SAP Cloud](https://cal.sap.com). Pode encontrar documentação sobre como implantar um sistema S/4HANA através da Biblioteca de Eletrodomésticos SAP Cloud em Azure, por exemplo, [neste guia](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Tudo o que precisa é de uma subscrição Azure e de um utilizador SAP que possa ser registado na Biblioteca de Eletrodomésticos SAP Cloud.

## <a name="additional-resources"></a>Recursos adicionais
### <a name="sap-hana-backup"></a>Backup SAP HANA
Para obter informações sobre como fazer o backup sap HANA bases de dados em VMs Azure, consulte:
* [Guia de reserva para SAP HANA em Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [SAP HANA Azure Backup no nível de ficheiro](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [Backup SAP HANA com base em instantâneos](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)de armazenamento .

### <a name="sap-cloud-appliance-library"></a>Biblioteca de eletrodomésticos SAP Cloud
Para obter informações sobre como utilizar a Biblioteca de Eletrodomésticos SAP Cloud para implantar S/4HANA ou BW/4HANA, consulte [A Implantação SAP S/4HANA ou BW/4HANA no Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Sistemas operativos suportados pela SAP HANA
Para obter informações sobre os sistemas operativos suportados pelo SAP HANA, consulte [a Nota SAP 2235581 - SAP HANA: Sistemas operativos suportados](https://launchpad.support.sap.com/#/notes/2235581/E). Os VMs azure suportam apenas um subconjunto destes sistemas operativos. Os seguintes sistemas operativos são suportados para implantar o SAP HANA no Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Para obter documentação adicional sobre o SAP HANA e diferentes sistemas operativos Linux, consulte:

* [Nota SAP 171356: Software SAP em Linux: Informações gerais](https://launchpad.support.sap.com/#/notes/1984787).
* [Nota SAP 1944799: Diretrizes SAP HANA para instalação do sistema operativo SLES](http://service.sap.com/sap/support/notes/1944799).
* [Nota SAP 2205917: SAP HANA DB recomenda as definições de OS para aplicações SLES 12 para aplicações SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
* [Nota SAP 1391070: Soluções Linux UUID](https://launchpad.support.sap.com/#/notes/1391070).
* [Nota SAP 2009879: Diretrizes SAP HANA para o sistema operativo Red Hat Enterprise Linux (RHEL).](https://launchpad.support.sap.com/#/notes/2009879)
* [Nota SAP 2292690: SAP HANA DB: Definições recomendadas de OS para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>Monitorização SAP em Azure
Para obter informações sobre a monitorização do SAP em Azure:

* [SAP Nota 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) discute a monitorização reforçada com Os VMs Linux em Azure. 
* [SAP Nota 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) discute informações sobre SAPOSCOL no Linux. 
* [O SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) discute as principais métricas de monitorização para o SAP no Microsoft Azure.

### <a name="azure-vm-types"></a>Tipos de VM Azure
Os tipos de VM Azure e cenários de carga de trabalho suportados por SAP utilizados com o SAP HANA estão documentados nas [plataformas IaaS certificadas pela SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Os tipos de VM Azure certificados pela SAP para SAP NetWeaver ou a camada de aplicação S/4HANA estão documentados na [Nota SAP 1928533: Aplicações SAP em Azure: Produtos suportados e tipos de VM Azure.](https://launchpad.support.sap.com/#/notes/1928533/E)

> [!NOTE]
> A integração SAP-Linux-Azure é suportada apenas no Azure Resource Manager e não no modelo clássico de implantação. 

## <a name="manual-installation-of-sap-hana"></a>Instalação manual do SAP HANA

> [!IMPORTANT]
> Certifique-se de que o SISTEMA selecionado é certificado sAP para SAP HANA nos tipos de VM específicos que utiliza. A lista de tipos vM certificados SAP HANA e lançamentos de OS para esses tipos vM pode ser procurada nas [plataformas IaaS certificadas sAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Certifique-se de clicar nos detalhes do tipo VM listado para obter a lista completa de lançamentos de SAP HANA suportados por SAP HANA para o tipo de VM específico. Por exemplo neste documento, usamos um suse Linux Enterprise Server (SLES) que não é suportado pela SAP para SAP HANA em VMs da série M.
>

Este guia descreve como instalar manualmente o SAP HANA em VMs Azure de duas maneiras diferentes:

* Utilize o Gestor de Provisionamento de Software SAP (SWPM) como parte de uma instalação NetWeaver distribuída na etapa "instalar a instância da base de dados".
* Utilize a ferramenta SAP HANA database lifecycle manager (HDBLCM) e, em seguida, instale o NetWeaver.

Também pode utilizar o SWPM para instalar todos os componentes, tais como o SAP HANA, o servidor de aplicações SAP e a instância ASCS, num único VM. Os passos são descritos neste anúncio de [blog SAP HANA.](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/) Esta opção não é descrita neste guia de arranque rápido, mas as questões que deve considerar são as mesmas.

Antes de iniciar uma instalação, recomendamos que leia a secção "Prepare Os VMs Azure para instalação manual de SAP HANA" mais tarde neste guia. Fazê-lo pode ajudar a prevenir vários erros básicos que podem ocorrer quando se utiliza apenas uma configuração de VM Azure padrão.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Passos-chave para a instalação SAP HANA quando utilizar SAP SWPM
Esta secção lista os passos-chave para uma instalação Manual, de uma só instância SAP HANA, quando utilizar o SAP SWPM para realizar uma instalação SAP NetWeaver 7.5 distribuída. Os passos individuais são explicados mais detalhadamente em imagens mais tarde neste guia.

1. Crie uma rede virtual Azure que inclua dois VMs de teste.
2. Implementar os dois VMs Azure com sistemas operativos de acordo com o modelo Do Gestor de Recursos Azure. Este exemplo utiliza o SUSE Linux Enterprise Server e o SLES para aplicações SAP 12 SP1. 
3. Fixe dois discos de armazenamento standard ou premium Azure, por exemplo, discos de 75 GB ou 500 GB, ao VM do servidor de aplicações.
4. Fixe discos de armazenamento premium ao VM do servidor HANA DB. Para mais informações, consulte a secção "Configuração do disco" mais tarde neste guia.
5. Dependendo dos requisitos de tamanho ou de entrada, prenda vários discos. Em seguida, crie volumes listrados. Utilize a gestão lógica do volume (LVM) ou uma ferramenta de administração de múltiplos dispositivos (mdadm) ao nível de Os dentro do VM.
6. Crie sistemas de ficheiros XFS nos discos ligados ou volumes lógicos.
7. Monte os novos sistemas de ficheiros XFS ao nível do SISTEMA. Utilize um sistema de ficheiros para todo o software SAP. Utilize o outro sistema de ficheiros para o diretório /sapmnt e cópias de segurança, por exemplo. No servidor SAP HANA DB, monte os sistemas de ficheiros XFS nos discos de armazenamento premium como /hana e /usr/seiva. Este processo é necessário para evitar que o sistema de ficheiros radiculares se encha. O sistema de ficheiros radicular não é grande em VMs Linux Azure. 
8. Introduza os endereços IP locais dos VMs de teste no ficheiro /etc/anfitriões.
9. Introduza o parâmetro **nofail** no ficheiro /etc/fstab.
10. Defina os parâmetros de kernel linux de acordo com a libertação do Sistema De si o sistema operativo Linux OS que utiliza. Para mais informações, consulte as Notas SAP que discutem a HANA e a secção "Parâmetros Kernel" neste guia.
11. Adicione espaço de comutação.
12. Opcionalmente, instale um ambiente de trabalho gráfico nos VMs de teste. Caso contrário, utilize uma instalação sAPinst remota.
13. Descarregue o software SAP do SAP Service Marketplace.
14. Instale a instância SAP ASCS no VM do servidor de aplicações.
15. Partilhe o diretório /sapmnt entre os VMs de teste utilizando NFS. O VM do servidor de aplicações é o servidor NFS.
16. Instale a instância de base de dados, que inclui HANA, utilizando o SWPM no VM do servidor DB.
17. Instale o servidor de aplicação principal (PAS) no VM do servidor de aplicações.
18. Iniciar a consola de gestão SAP (SAP MC). Conecte-se com o SAP GUI ou o HANA Studio, por exemplo.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Passos-chave para a instalação SAP HANA quando utilizar HDBLCM
Esta secção lista os passos-chave para uma instalação Manual, de uma só instância SAP HANA, quando utilizar o SAP HDBLCM para realizar uma instalação SAP NetWeaver 7.5 distribuída. Os passos individuais são explicados mais detalhadamente em imagens ao longo deste guia.

1. Crie uma rede virtual Azure que inclua dois VMs de teste.
2. Implementar dois VMs Azure com sistemas operativos de acordo com o modelo Do Gestor de Recursos Azure. Este exemplo utiliza SLES e SLES para Aplicações SAP 12 SP1.
3. Fixe dois discos de armazenamento standard ou premium Azure, por exemplo, discos de 75 GB ou 500 GB, ao VM do servidor de aplicações.
4. Fixe discos de armazenamento premium ao VM do servidor HANA DB. Para mais informações, consulte a secção "Configuração do disco" mais tarde neste guia.
5. Dependendo dos requisitos de tamanho ou de entrada, prenda vários discos. Crie volumes listrados utilizando a gestão lógica do volume ou uma ferramenta mdadm ao nível de S dentro do VM.
6. Crie sistemas de ficheiros XFS nos discos ligados ou volumes lógicos.
7. Monte os novos sistemas de ficheiros XFS ao nível do SISTEMA. Utilize um sistema de ficheiros para todo o software SAP. Utilize o outro sistema de ficheiros para o diretório /sapmnt e cópias de segurança, por exemplo. No servidor SAP HANA DB, monte os sistemas de ficheiros XFS nos discos de armazenamento premium como /hana e /usr/seiva. Este processo é necessário para ajudar a evitar que o sistema de ficheiros radiculares se encha. O sistema de ficheiros radicular não é grande em VMs Linux Azure.
8. Introduza os endereços IP locais dos VMs de teste no ficheiro /etc/anfitriões.
9. Introduza o parâmetro **nofail** no ficheiro /etc/fstab.
10. Defina os parâmetros do núcleo de acordo com a libertação do Sistema De so-lo. Para mais informações, consulte as Notas SAP que discutem a HANA e a secção "Parâmetros Kernel" neste guia.
11. Adicione espaço de comutação.
12. Opcionalmente, instale um ambiente de trabalho gráfico nos VMs de teste. Caso contrário, utilize uma instalação sAPinst remota.
13. Descarregue o software SAP do SAP Service Marketplace.
14. Crie um grupo, sapsys, com id de grupo 1001, no VM do servidor HANA DB.
15. Instale o SAP HANA no VM do servidor DB utilizando o gestor de ciclo de vida da base de dados HANA.
16. Instale a instância SAP ASCS no VM do servidor de aplicações.
17. Partilhe o diretório /sapmnt entre os VMs de teste utilizando NFS. O VM do servidor de aplicações é o servidor NFS.
18. Instale a instância de base de dados, que inclui HANA, utilizando o SWPM no VM do servidor HANA DB.
19. Instale o servidor de aplicação principal no VM do servidor de aplicações.
20. Comece sap MC. Ligue-se através do SAP GUI ou do HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Prepare VMs Azure para uma instalação manual de SAP HANA
Esta secção aborda os seguintes tópicos:

* Atualizações do SO
* Configuração do disco
* Parâmetros kernel
* Sistemas de ficheiros
* O ficheiro /etc/anfitriões
* O ficheiro /etc/fstab

### <a name="os-updates"></a>Atualizações do SO
Verifique as atualizações e correções do Sistema Linux OS antes de instalar software adicional. Ao instalar um patch, poderá evitar uma chamada para o balcão de suporte.

Certifique-se de que utiliza:
* Servidor empresarial SUSE Linux para aplicações SAP.
* Red Hat Enterprise Linux para Aplicações SAP ou Red Hat Enterprise Linux para SAP HANA. 

Se ainda não o fez, registe a implementação do SO com a subscrição do Linux do fornecedor Linux. A SUSE tem imagens de OS para aplicações SAP que já incluem serviços, e que são registadas automaticamente.

Aqui está um exemplo de como verificar os patches disponíveis para SUSE Linux usando o comando **zypper:**

 `sudo zypper list-patches`

Dependendo do tipo de questão, os patches são classificados por categoria e gravidade. Os valores geralmente utilizados para categoria são: 
- Segurança
- Recomendado
- Opcional
- Funcionalidade
- Documento
- Yast

Os valores comumente utilizados para a gravidade são:

- Crítica
- Importante
- Moderado
- Baixa
- Não especificado

O comando **zypper** procura apenas as atualizações de que os seus pacotes instalados precisam. Por exemplo, pode utilizar este comando:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Pode adicionar o parâmetro `--dry-run` para testar a atualização sem atualizar o sistema.


### <a name="disk-setup"></a>Configuração do disco
O sistema de ficheiros raiz num VM Linux no Azure tem uma limitação de tamanho. Por isso, é necessário anexar espaço adicional de disco a um VM Azure para executar o SAP. Para o servidor de aplicações SAP, os VMs Azure, a utilização de discos de armazenamento padrão Azure pode ser suficiente. Para os VMs SAP HANA DBMS Azure, é obrigatória a utilização de discos de armazenamento premium Azure para implementações de produção e não produção.

Com base nos requisitos de [armazenamento SAP HANA TDI,](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)sugere-se a seguinte configuração de armazenamento premium Azure: 

| SKU da VM | RAM |  /hana/data e /hana/log <br /> listrado com LVM ou mdadm | /hana/compartilhado | /volume de raiz | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Na configuração do disco sugerido, o volume de dados hana e o volume de registo são colocados no mesmo conjunto de discos de armazenamento premium Azure que são listrados com LVM ou mdadm. Não é necessário definir qualquer nível de redundância RAID porque o armazenamento premium Azure guarda três imagens dos discos para redundância. 

Para se certificar de que configura o armazenamento suficiente, consulte os requisitos de [armazenamento SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) e o guia de [instalação e atualização do servidor SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Considere também os diferentes volumes de entrada de discos rígidos virtuais (VHD) dos diferentes discos de armazenamento premium Azure, conforme documentado no [armazenamento premium de alto desempenho e discos geridos para VMs](../../windows/disks-types.md). 

Pode adicionar mais discos de armazenamento premium aos VMs HANA DBMS para armazenar bases de dados ou cópias de segurança de registo de transações.

Para obter mais informações sobre as duas principais ferramentas usadas para configurar as striping, consulte:

* [Configure o software RAID no Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Configure LVM num Linux VM em Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter mais informações sobre como anexar discos a VMs Azure que executam o Linux como um OS convidado, consulte [Adicionar um disco a um VM Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Com SSDs premium Azure, pode definir modos de cache de disco. Para o conjunto listrado que contém /hana/data e /hana/log, desative o cache do disco. Para os outros volumes, isto é, discos, desloque o modo de cache para **ReadOnly**.

Para encontrar modelos JSON de amostra para usar para criar VMs, consulte os [modelos de arranque rápido azure](https://github.com/Azure/azure-quickstart-templates).
O modelo vm-simple-sles é um modelo básico. Inclui uma secção de armazenamento, com um disco de dados adicional de 100 GB. Use este modelo como base. Pode adaptar o modelo à sua configuração específica.

> [!NOTE]
> É importante anexar o disco de armazenamento Azure utilizando um UUID como documentado no [Run SAP NetWeaver no Microsoft Azure SUSE Linux VMs](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

No ambiente de teste, dois discos de armazenamento padrão Azure são anexados ao servidor de aplicações SAP VM, como mostra a seguinte imagem. Um disco armazena todo o software SAP, como NetWeaver 7.5, SAP GUI e SAP HANA, para instalação. O segundo disco garante que há espaço livre suficiente para requisitos adicionais. Por exemplo, os dados de backup e teste e o diretório/sapmnt, isto é, perfis SAP, precisam de ser partilhados entre todos os VMs que pertencem à mesma paisagem SAP.

![Janela de servidor de servidor de aplicativos SAP HANA mostrando dois discos de dados e seus tamanhos](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parâmetros kernel
O SAP HANA requer definições específicas de kernel linux. Estas definições de núcleo não fazem parte das imagens padrão da galeria Azure e devem ser definidas manualmente. Dependendo se usa SUSE ou Chapéu Vermelho, os parâmetros podem ser diferentes. As notas SAP listadas anteriormente dão informações sobre esses parâmetros. Nas imagens mostradas, foi utilizado o SUSE Linux 12 SP1. 

SLES para Aplicações SAP 12 disponibilidade geral e SLES para Aplicações SAP 12 SP1 têm uma nova ferramenta, **afinada- adm,** que substitui a antiga ferramenta **sapconf.** Um perfil especial SAP HANA está disponível para **sintonizado- adm**. Para afinar o sistema para SAP HANA, introduza o seguinte perfil como um utilizador raiz:

   `tuned-adm profile sap-hana`

Para mais informações sobre o **satonizado-adm,** consulte a [documentação suse sobre o suse -adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Na seguinte imagem, pode ver como o **administrador afinado** alterou os valores de `transparent_hugepage` e `numa_balancing`, de acordo com as definições necessárias do SAP HANA:

![A ferramenta afinada altera os valores de acordo com as definições necessárias do SAP HANA](./media/hana-get-started/image005.jpg)

Para tornar as definições de kernel SAP HANA permanentes, utilize **o grub2** nas SLES 12. Para obter mais informações sobre **a grub2,** consulte a secção de estrutura de ficheiros de [Configuração](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) da documentação SUSE.

A imagem seguinte mostra como as definições do núcleo foram alteradas no ficheiro de configuração e, em seguida, compiladas utilizando **grub2-mkconfig:**

![As definições de kernel alteradas no ficheiro de configuração e compiladas utilizando grub2-mkconfig](./media/hana-get-started/image006.jpg)

Outra opção é alterar as definições utilizando o YaST e o Carregador de **Botas** > definições de **Parâmetros Kernel:**

![O separador de definições de parâmetros Kernel no Carregador de Botas YaST](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Sistemas de ficheiros
A imagem seguinte mostra dois sistemas de ficheiros que foram criados no vM do servidor de aplicações SAP em cima dos dois discos de armazenamento padrão Azure anexados. Ambos os sistemas de ficheiros são do tipo XFS e são montados para /sapdata e /sapsoftware.

Não é obrigatório estruturar os seus sistemas de ficheiros desta forma. Tem outras opções para estruturar o espaço do disco. A consideração mais importante é evitar que o sistema de ficheiros radiculares se esgote no espaço livre.

![Dois sistemas de ficheiros criados no servidor de aplicações SAP VM](./media/hana-get-started/image008.jpg)

Para o VM SAP HANA DB, durante uma instalação de base de dados, quando utiliza sAPinst com SWPM e a **opção de** instalação típica, tudo é instalado sob /hana e /usr/seiva. A localização padrão para a cópia de segurança de log SAP HANA está em /usr/seiva. Mais uma vez, é importante evitar que o sistema de ficheiros radiculares se esgote do espaço de armazenamento. Certifique-se de que há espaço livre suficiente sob /hana e /usr/seiva antes de instalar o SAP HANA utilizando o SWPM.

Para obter uma descrição do layout padrão do sistema de ficheiros do SAP HANA, consulte a instalação e o guia de atualização do [servidor SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Sistemas de ficheiros adicionais criados no vM do servidor de aplicações SAP](./media/hana-get-started/image009.jpg)

Quando instala o SAP NetWeaver numa imagem padrão da Galeria SLES/SLES para aplicações SAP 12 Azure, uma mensagem mostra que não há espaço de troca, como mostra a seguinte imagem. Para descartar esta mensagem, pode adicionar manualmente um ficheiro de troca utilizando **dd,** **mkswap**e **swapon**. Para saber como, procure "Adicionar manualmente um ficheiro de troca" na secção [de partição YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) da documentação SUSE.

Outra opção é configurar o espaço de troca utilizando o agente Linux VM. Para obter mais informações, veja o [Guia de utilizador do Agente Linux do Azure](../../extensions/agent-linux.md).

![Mensagem pop-up avisando que não há espaço de troca insuficiente](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>O ficheiro /etc/anfitriões
Antes de começar a instalar o SAP, certifique-se de que inclui os nomes de anfitriões e endereços IP dos VMs SAP no ficheiro /etc/anfitriões. Implemente todos os VMs SAP dentro de uma rede virtual Azure. Em seguida, utilize os endereços IP internos, como mostrado aqui:

![Nomes de anfitriões e endereços IP dos VMs SAP listados no ficheiro /etc/anfitriões](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>O ficheiro /etc/fstab

É útil adicionar o parâmetro **nofail** ao ficheiro fstab. Desta forma, se algo correr mal com os discos, o VM não para de responder no processo de arranque. Mas lembre-se que o espaço adicional do disco pode não estar disponível, e os processos podem encher o sistema de ficheiros radicular. Se a Hana faltar, a SAP HANA não começa.

![Adicione o parâmetro nofail ao ficheiro fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Ambiente de trabalho gnome gráfico nas SLES 12/SLES para Aplicações SAP 12
Esta secção explica como:

* Instale o ambiente de trabalho GNOME e xrdp nas SLES 12/SLES para aplicações SAP 12.
* Executar O SAP MC baseado em Java utilizando o Firefox nas SLES 12/SLES para aplicações SAP 12.

Também pode utilizar alternativas como Xterminal ou VNC, que não são descritas neste guia.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Instale o ambiente de trabalho GNOME e xrdp nas SLES 12/SLES para aplicações SAP 12
Se tiver um fundo Windows, pode utilizar facilmente um ambiente de trabalho gráfico diretamente dentro dos VMs SAP Linux para executar Firefox, SAPinst, SAP GUI, SAP MC ou HANA Studio. Em seguida, pode ligar-se ao VM através do Protocolo de Ambiente de Trabalho Remoto (RDP) a partir de um computador Windows. Dependendo das políticas da sua empresa sobre a adição de GUIs aos sistemas baseados em Linux de produção e não produção, poderá querer instalar o GNOME no seu servidor. Siga estes passos para instalar o ambiente de trabalho GNOME num Azure SLES 12/SLES para aplicações SAP 12 VM.

1. Instale o ambiente de trabalho GNOME inserindo o seguinte comando, por exemplo, numa janela PuTTY:

   `zypper in -t pattern gnome-basic`

2. Instale o XRDP para permitir uma ligação ao VM através de RDP:

   `zypper in xrdp`

3. Editar /etc/sysconfig/windowmanager, e definir o gestor de janela seleções padrão para GNOME:

   `DEFAULT_WM="gnome"`

4. Executar **chkconfig** para se certificar de que o xrdp começa automaticamente após um reboot:

   `chkconfig -level 3 xrdp on`

5. Se tiver algum problema com a ligação RDP, tente reiniciar, por exemplo, a partir de uma janela PuTTY:

   `/etc/xrdp/xrdp.sh restart`

6. Se um reinício do XRDP mencionado no passo anterior não funcionar, verifique se há um ficheiro .pid:

   `check /var/run` 

   Procure `xrdp.pid`. Se o encontrar, retire-o e tente recomeçar.

### <a name="start-sap-mc"></a>Iniciar SAP MC
Depois de instalar o ambiente de trabalho GNOME, inicie o SAP MC baseado em Java gráfico a partir do Firefox. Se funcionar num Azure SLES 12/SLES para aplicações SAP 12 VM, pode apresentar um erro. O erro ocorre devido ao plug-in do navegador Java em falta.

O URL para iniciar o SAP MC é `<server>:5<instance_number>13`.

Para mais informações, consulte [Iniciar a consola de gestão SAP baseada na web](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

A seguinte imagem mostra a mensagem de erro que mostra quando o plug-in do navegador Java está em falta:

![Mensagem de erro indicando falta de plug-in do navegador Java](./media/hana-get-started/image013.jpg)

Uma forma de resolver o problema é instalar o plug-in em falta utilizando o YaST, como mostra a seguinte imagem:

![Utilização do YaST para instalar plug-in em falta](./media/hana-get-started/image014.jpg)

Quando reentrar no URL da Consola de Gestão SAP, é-lhe pedido que ative o plug-in:

![Caixa de diálogo solicitando ativação de plug-in](./media/hana-get-started/image015.jpg)

Também pode receber uma mensagem de erro sobre um ficheiro em falta, javafx.properties. Refere-se à exigência do Oracle Java 1.8 para sap GUI 7.4. Para mais informações, consulte [SAP Nota 2059429](https://launchpad.support.sap.com/#/notes/2059424).
A versão IBM Java e o pacote openjdk que é entregue com SLES/SLES para Aplicações SAP 12 não incluem o ficheiro javafx.properties necessário. A solução é descarregar e instalar o Java SE 8 da Oracle.

Para obter informações sobre um problema semelhante com openjdk em openSUSE, consulte o fio de discussão [SAPGui 7.4 Java para abrir SUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Instalação manual de SAP HANA: SWPM
A série de imagens nesta secção mostra os passos-chave para como instalar o SAP NetWeaver 7.5 e o SAP HANA SP12 quando utiliza o SWPM com sAPinst. Como parte de uma instalação NetWeaver 7.5, o SWPM também pode instalar a base de dados HANA como uma única instância.

Num ambiente de teste de amostra, instalámos um servidor de aplicação de aplicações de aplicação de negócios avançado (ABAP). Como mostra a seguinte imagem, usamos a opção **Sistema Distribuído** para instalar as instâncias de servidor de aplicação ASCS e principal num VM Azure. Usamos o SAP HANA como sistema de base de dados noutro Azure VM.

![ASCS e instâncias de servidor de aplicação primária instaladas utilizando a opção Sistema Distribuído](./media/hana-get-started/image012.jpg)

Depois da instância ASCS ser instalada no VM do servidor de aplicações, é identificado por um ícone verde na Consola de Gestão SAP. O diretório /sapmnt, que inclui o diretório de perfil SAP, deve ser partilhado com o VM do servidor DB SAP HANA. O passo de instalação db precisa de acesso a esta informação. A melhor maneira de fornecer acesso é usar o NFS, que pode ser configurado usando o YaST.

![Consola de gestão SAP mostrando a instância ASCS instalada no vM do servidor de aplicações usando um ícone verde](./media/hana-get-started/image016.jpg)

No vM do servidor de aplicações, o diretório /sapmnt é partilhado via NFS utilizando as opções **de rw** e **no_root_squash.** As predefinições são **ro** e **root_squash**, o que pode levar a problemas quando instalar a instância de base de dados.

![Partilhando o diretório /sapmnt via NFS utilizando as opções de rw e no_root_squash](./media/hana-get-started/image017b.jpg)

Como mostra a próxima imagem, a parte /sapmnt do servidor de aplicações VM deve ser configurada no VM do servidor DB SAP HANA utilizando o **Cliente NFS** e o YaST:

![A parte /sapmnt configurada através da utilização do cliente NFS](./media/hana-get-started/image018b.jpg)

Para realizar uma instalação NetWeaver 7.5 distribuída, isto é, uma Instância de Base de **Dados,** inicie sessão no VM do servidor Db SAP HANA e inicie o SWPM:

![Instalação de uma instância de base de dados, iniciando o vM do servidor DB SAP HANA e iniciando o SWPM](./media/hana-get-started/image019.jpg)

Depois de selecionar a instalação **típica** e o caminho para os meios de instalação, introduza um DB SID, o nome do anfitrião, o número de instância e a senha do administrador do sistema DB:

![Página de inscrição do administrador de base de dados SAP HANA](./media/hana-get-started/image035b.jpg)

Introduza a palavra-passe para o esquema DBACOCKPIT:

![A caixa de entrada de senha para o esquema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Introduza uma pergunta para a senha de esquema SAPABAP1:

![Insira uma pergunta para a senha de esquema SAPABAP1](./media/hana-get-started/image037b.jpg)

Após a conclusão de cada tarefa, é apresentada uma marca de verificação verde junto a cada fase do processo de instalação DB. A mensagem "Execução de ... Base de Dados A Instância de Dados concluiu".

![Janela completada por tarefas com mensagem de confirmação](./media/hana-get-started/image023.jpg)

Após uma instalação bem sucedida, a Consola de Gestão SAP também mostra a instância DB com um ícone verde. Apresenta a lista completa dos processos SAP HANA, tais como hdbindexserver e hdbcompileserver.

![Janela da consola de gestão SAP com lista de processos SAP HANA](./media/hana-get-started/image024.jpg)

A imagem seguinte mostra as partes da estrutura de ficheiros sob o diretório /hana/partilhado que a SWPM criou durante a instalação hana. Como não há opção para especificar um caminho diferente, é importante montar espaço adicional de disco sob o diretório /hana antes da instalação SAP HANA usando sWPM. Este passo impede que o sistema de ficheiros radiculares se esgote em espaço livre.

![A estrutura de ficheiros de diretório /hana/partilhado criada durante a instalação hana](./media/hana-get-started/image025.jpg)

Esta imagem mostra a estrutura de ficheiros do diretório /usr/seiva:

![A estrutura de ficheiros de diretório /usr/seiva](./media/hana-get-started/image026.jpg)

O último passo da instalação ABAP distribuída é instalar a instância principal do servidor de aplicações:

![Instalação ABAP mostrando a instância do servidor de aplicação primária como o passo final](./media/hana-get-started/image027b.jpg)

Após a localização do servidor de aplicação primária e o SAP GUI, utilize a transação **DBA Cockpit** para confirmar que a instalação SAP HANA terminou corretamente:

![Janela do Cockpit dBA confirmando instalação bem sucedida](./media/hana-get-started/image028b.jpg)

Como último passo, é possível instalar primeiro o HANA Studio no servidor de aplicações SAP VM. Em seguida, ligue-se à instância SAP HANA que está em execução no VM do servidor DB.

![Instalação do Estúdio SAP HANA no servidor de aplicações SAP VM](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Instalação manual de SAP HANA: HDBLCM
Além de instalar o SAP HANA como parte de uma instalação distribuída utilizando o SWPM, pode instalar primeiro o HANA autónomo, utilizando o HDBLCM. Em seguida, pode instalar o SAP NetWeaver 7.5, por exemplo. As imagens desta secção mostram como este processo funciona.

Para mais informações sobre a ferramenta HANA HDBLCM, consulte:

* [Escolha o SAP HANA HDBLCM correto para a sua tarefa](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [Ferramentas de gestão do ciclo de vida SAP HANA.](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)
* [Guia de instalação e atualização do servidor SAP HANA](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

Deseja evitar problemas com uma definição de ID de grupo padrão para o `\<HANA SID\>adm user`, que é criado pela ferramenta HDBLCM. Antes de instalar o SAP HANA via HDBLCM, defina um novo grupo chamado `sapsys` utilizando `1001`de identificação do grupo:

![Novo grupo "sapsys" definido pela utilização do grupo ID 1001](./media/hana-get-started/image030.jpg)

Quando iniciar o HDBLCM pela primeira vez, um menu de início simples exibe. Selecione o artigo 1, **instale um novo sistema:**

![Opção "Instalar novo sistema" na janela de arranque hDBLCM](./media/hana-get-started/image031.jpg)

A imagem a seguir mostra todas as opções-chave que selecionou anteriormente.

> [!IMPORTANT]
> Os diretórios nomeados para os volumes de registo e dados da HANA, e a trajetória de instalação, que é /hana/partilhada nesta amostra, e /usr/seiva não devem fazer parte do sistema de ficheiros radicular. Estes diretórios pertencem aos discos de dados Azure que estavam ligados ao VM. Para mais informações, consulte a secção "Configuração do disco". 

Esta abordagem ajuda a evitar que o sistema de ficheiros radicular esgote o espaço. Note que o administrador do sistema HANA tem `1005` de identificação do utilizador e faz parte do grupo `sapsys`, com id `1001`, que foi definido antes da instalação.

![Lista de todos os componentes principais do SAP HANA selecionados anteriormente](./media/hana-get-started/image032.jpg)

Verifique os detalhes `\<HANA SID\>adm user` no diretório /etc/passwd. Procure `azdadm`, como mostra a seguinte imagem:

![HANA \<HANA SID\>adm dados do utilizador listados no diretório /etc/passwd](./media/hana-get-started/image033.jpg)

Depois de instalar o SAP HANA utilizando o HDBLCM, pode ver a estrutura de ficheiros no Estúdio SAP HANA, como mostra a seguinte imagem. O esquema SAPABAP1, que inclui todas as tabelas SAP NetWeaver, ainda não está disponível.

![Estrutura de ficheiros SAP HANA no Estúdio SAP HANA](./media/hana-get-started/image034.jpg)

Depois de instalar o SAP HANA, pode instalar o SAP NetWeaver em cima. Como mostra a seguinte imagem, a instalação foi realizada como uma instalação distribuída utilizando o SWPM. Este processo é descrito na secção anterior. Quando instala a instância da base de dados utilizando o SWPM, introduza os mesmos dados utilizando hDBLCM. Por exemplo, introduza o nome de anfitrião, HANA SID, e número de exemplo. A SWPM utiliza então a instalação HANA existente e adiciona mais esquemas.

![Uma instalação distribuída realizada através da utilização de SWPM](./media/hana-get-started/image035b.jpg)

A imagem seguinte mostra o passo de instalação do SWPM onde introduz dados sobre o esquema DBACOCKPIT:

![A etapa de instalação do SWPM onde os dados do esquema DBACOCKPIT são introduzidos](./media/hana-get-started/image036b.jpg)

Insira dados sobre o esquema SAPABAP1:

![Introduzir dados sobre o esquema SAPABAP1](./media/hana-get-started/image037b.jpg)

Após a instalação da instância de base de dados SWPM estar terminada, pode ver o esquema SAPABAP1 no Estúdio SAP HANA:

![O esquema SAPABAP1 no Estúdio SAP HANA](./media/hana-get-started/image038b.jpg)

Finalmente, após o servidor de aplicações SAP e as instalações do SAP GUI estarem terminadas, verifique a instância HANA DB utilizando a transação **DBA Cockpit:**

![A instância HANA DB verificada com a transação dba cockpit](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Downloads de software SAP
Pode descarregar software a partir do SAP Service Marketplace, como mostram as seguintes imagens.

Baixar NetWeaver 7.5 para Linux/HANA:

 ![Instalação de serviço SAP e janela de upgrade para descarregar NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Baixe a edição da plataforma HANA SP12:

 ![Instalação de serviço SAP e janela de upgrade para download HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)

