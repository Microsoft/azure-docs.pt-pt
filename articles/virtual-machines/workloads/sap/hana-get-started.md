---
title: 'Início rápido: Instalação manual de SAP HANA de instância única em máquinas virtuais do Azure | Microsoft Docs'
description: Guia de início rápido para instalação manual de SAP HANA de instância única em máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
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
ms.openlocfilehash: 8d4e7b7056f4d5e53785366818fad05e24cfc605
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100057"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Início rápido: Instalação manual de SAP HANA de instância única em máquinas virtuais do Azure
## <a name="introduction"></a>Introdução
Este guia ajuda você a configurar um SAP HANA de instância única em máquinas virtuais do Azure quando você instala o SAP NetWeaver 7,5 e o SAP HANA 1,0 SP12 manualmente. O foco deste guia é sobre como implantar SAP HANA no Azure. Ele não substitui a documentação do SAP. 

> [!NOTE]
> Este guia descreve as implantações de SAP HANA em VMs do Azure. Para obter informações sobre como implantar SAP HANA em instâncias grandes do HANA, consulte [usar o SAP em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Pré-requisitos
Este guia pressupõe que você esteja familiarizado com esses conceitos básicos de IaaS (infraestrutura como serviço) como:
 * Como implantar máquinas virtuais (VMs) ou redes virtuais por meio do portal do Azure ou do PowerShell.
 * A CLI (interface de linha de comando) entre plataformas do Azure, que inclui a opção de usar modelos de JavaScript Object Notation (JSON).

Este guia também pressupõe que você esteja familiarizado com:
* SAP HANA e SAP NetWeaver e como instalá-los no local.
* Como instalar e operar SAP HANA e instâncias de aplicativo SAP no Azure.
* Os seguintes conceitos e procedimentos:
   * Planejando a implantação do SAP no Azure, que inclui o planejamento de rede virtual do Azure e o uso do armazenamento do Azure. Consulte [SAP NetWeaver em máquinas virtuais do Azure – guia de planejamento e implementação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Princípios de implantação e maneiras de implantar VMs no Azure. Consulte [implantação de máquinas virtuais do Azure para SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Alta disponibilidade para SAP NetWeaver ABAP SAP central Services (ASCS), SAP central Services (SCS) e servidor de replicação de enfileiramento (ERS) no Azure. Consulte [alta disponibilidade para SAP NetWeaver em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Detalhes sobre como melhorar a eficiência em uma instalação de vários SID do ASCS/SCS no Azure. Consulte [criar uma configuração de vários SID do SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Princípios de execução do SAP NetWeaver com base em VMs baseadas em Linux no Azure. Consulte [executar o SAP NetWeaver em VMs Microsoft Azure SuSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Este guia fornece configurações específicas para Linux em VMs do Azure. Ele também fornece informações sobre como anexar corretamente discos de armazenamento do Azure a VMs do Linux.

Os tipos de VM do Azure que podem ser usados para cenários de produção são listados na [documentação do SAP para IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, uma variedade maior de tipos de VM nativos do Azure está disponível.
Para obter mais informações sobre a configuração e as operações da VM, consulte [SAP Hana configurações de infraestrutura e operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Para SAP HANA alta disponibilidade, consulte [SAP Hana alta disponibilidade para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Se você quiser obter uma instância SAP HANA ou um sistema S/4HANA ou BW/4HANA implantado rapidamente, considere usar a [SAP Cloud Appliance library](https://cal.sap.com). Você pode encontrar a documentação sobre como implantar um sistema S/4HANA por meio da SAP Cloud Appliance Library no Azure, por exemplo, neste [guia](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Tudo o que você precisa é de uma assinatura do Azure e de um usuário SAP que possa ser registrado com a SAP Cloud Appliance library.

## <a name="additional-resources"></a>Recursos adicionais
### <a name="sap-hana-backup"></a>SAP HANA backup
Para obter informações sobre como fazer backup de bancos de dados do SAP HANA em VMs do Azure, consulte:
* [Guia de backup para SAP Hana em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [SAP Hana o backup do Azure no nível do arquivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [SAP Hana backup com base em instantâneos de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>Biblioteca de dispositivo de nuvem do SAP
Para obter informações sobre como usar a SAP Cloud Appliance Library para implantar S/4HANA ou BW/4HANA, consulte [implantar SAP S/4HANA ou BW/4HANA em Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Sistemas operacionais com suporte SAP HANA
Para obter informações sobre sistemas operacionais com suporte SAP Hana, [consulte SAP Note 2235581-SAP Hana: Sistemas](https://launchpad.support.sap.com/#/notes/2235581/E)operacionais com suporte. As VMs do Azure dão suporte apenas a um subconjunto desses sistemas operacionais. Os seguintes sistemas operacionais têm suporte para implantar o SAP HANA no Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7,2

Para obter documentação adicional do SAP sobre SAP HANA e diferentes sistemas operacionais Linux, consulte:

* [Nota SAP 171356: Software SAP no Linux: Informações](https://launchpad.support.sap.com/#/notes/1984787)gerais.
* [Nota SAP 1944799: Diretrizes de SAP HANA para a instalação](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)do sistema operacional SLES.
* [Nota SAP 2205917: SAP HANA DB as configurações de so recomendadas para o SLES](https://launchpad.support.sap.com/#/notes/2205917/E)12 para aplicativos SAP.
* [Nota SAP 1391070: Soluções](https://launchpad.support.sap.com/#/notes/1391070)de UUID do Linux.
* [Nota SAP 2009879: Diretrizes de SAP HANA para o sistema](https://launchpad.support.sap.com/#/notes/2009879)operacional Red Hat Enterprise Linux (RHEL).
* [Nota SAP 2292690: SAP HANA DB: Configurações de so recomendadas para](https://launchpad.support.sap.com/#/notes/2292690/E)RHEL 7.

### <a name="sap-monitoring-in-azure"></a>Monitoramento do SAP no Azure
Para obter informações sobre o monitoramento do SAP no Azure:

* A [Nota sap 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) aborda o monitoramento aprimorado do SAP com VMs do Linux no Azure. 
* A [Nota SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) discute informações sobre o SAPOSCOL no Linux. 
* A [Observação da sap 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) aborda as principais métricas de monitoramento para SAP em Microsoft Azure.

### <a name="azure-vm-types"></a>Tipos de VM do Azure
Os tipos de VM do Azure e os cenários de carga de trabalho com suporte SAP usados com SAP HANA estão documentados em [plataformas de IaaS certificadas pelo SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Os tipos de VM do Azure que são certificados pelo SAP para SAP NetWeaver ou pela camada de aplicativo S/ [4HANA estão documentados em SAP observação 1928533: Aplicativos SAP no Azure: Produtos com suporte e tipos](https://launchpad.support.sap.com/#/notes/1928533/E)de VM do Azure.

> [!NOTE]
> A integração do SAP-Linux-Azure tem suporte apenas em Azure Resource Manager e não no modelo de implantação clássico. 

## <a name="manual-installation-of-sap-hana"></a>Instalação manual do SAP HANA

> [!IMPORTANT]
> Certifique-se de que o sistema operacional selecionado é certificado pelo SAP para SAP HANA nos tipos específicos de VM que você usa. A lista de tipos de VM certificados SAP HANA e as versões de sistema operacional para esses tipos de VM podem ser pesquisadas em [SAP Hana plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Certifique-se de clicar nos detalhes do tipo de VM listado para obter a lista completa de versões de sistema operacional com suporte SAP HANA para o tipo de VM específico. Para o exemplo deste documento, usamos uma versão do sistema operacional SUSE Linux Enterprise Server (SLES) que não tem suporte do SAP para SAP HANA em VMs da série M.
>

Este guia descreve como instalar manualmente SAP HANA em VMs do Azure de duas maneiras diferentes:

* Use o SWPM (Gerenciador de provisionamento de software) do SAP como parte de uma instalação distribuída do NetWeaver na etapa "instalar instância de banco de dados".
* Use a ferramenta SAP HANA Database Lifecycle Manager (HDBLCM) e, em seguida, instale o NetWeaver.

Você também pode usar o SWPM para instalar todos os componentes, como SAP HANA, o servidor de aplicativos SAP e a instância ASCS, em uma única VM. As etapas são descritas neste [SAP Hana comunicado de blog](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Essa opção não está descrita neste guia de início rápido, mas os problemas que você deve considerar são os mesmos.

Antes de iniciar uma instalação, recomendamos que você leia a seção "preparar VMs do Azure para instalação manual do SAP HANA" mais adiante neste guia. Isso pode ajudar a evitar vários erros básicos que podem ocorrer quando você usa apenas uma configuração de VM do Azure padrão.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Etapas principais para a instalação do SAP HANA ao usar o SAP SWPM
Esta seção lista as principais etapas para uma instalação manual de SAP HANA de instância única quando você usa o SAP SWPM para executar uma instalação distribuída do SAP NetWeaver 7,5. As etapas individuais são explicadas em mais detalhes em capturas de tela posteriormente neste guia.

1. Crie uma rede virtual do Azure que inclua duas VMs de teste.
2. Implante as duas VMs do Azure com sistemas operacionais de acordo com o modelo de Azure Resource Manager. Este exemplo usa SUSE Linux Enterprise Server e SLES para aplicativos SAP 12 SP1. 
3. Anexe dois discos de armazenamento Standard ou Premium do Azure, por exemplo, discos de 75 GB ou 500 GB, à VM do servidor de aplicativos.
4. Anexe discos de armazenamento Premium à VM do servidor de BD HANA. Para obter mais informações, consulte a seção "configuração de disco" mais adiante neste guia.
5. Dependendo dos requisitos de tamanho ou taxa de transferência, anexe vários discos. Em seguida, crie volumes distribuídos. Use o LVM (gerenciamento de volume lógico) ou uma ferramenta de administração de vários dispositivos (mdadm) no nível do sistema operacional dentro da VM.
6. Crie sistemas de arquivos XFS nos discos conectados ou volumes lógicos.
7. Monte os novos sistemas de arquivos XFS no nível do sistema operacional. Use um sistema de arquivos para todo o software SAP. Use o outro sistema de arquivos para o diretório/sapmnt e backups, por exemplo. No servidor do SAP HANA DB, monte os sistemas de arquivos XFS nos discos do armazenamento Premium como/Hana e/usr/SAP. Esse processo é necessário para impedir que o sistema de arquivos raiz seja preenchido. O sistema de arquivos raiz não é grande em VMs do Azure do Linux. 
8. Insira os endereços IP locais das VMs de teste no arquivo/etc/hosts.
9. Insira o parâmetro nofail no arquivo/etc/fstab.
10. Defina parâmetros de kernel do Linux de acordo com a versão do sistema operacional Linux que você usa. Para obter mais informações, consulte as notas SAP que discutem o HANA e a seção "parâmetros de kernel" neste guia.
11. Adicionar espaço de permuta.
12. Opcionalmente, instale uma área de trabalho gráfica nas VMs de teste. Caso contrário, use uma instalação de SAPinst remota.
13. Baixe o software SAP do SAP Service Marketplace.
14. Instale a instância do SAP ASCS na VM do servidor de aplicativos.
15. Compartilhe o diretório/sapmnt entre as VMs de teste usando o NFS. A VM do servidor de aplicativos é o servidor NFS.
16. Instale a instância do banco de dados, que inclui o HANA, usando SWPM na VM do servidor de BD.
17. Instale o servidor de aplicativos primário (PAS) na VM do servidor de aplicativos.
18. Inicie o console de gerenciamento SAP (SAP MC). Conecte-se com a SAP GUI ou HANA Studio, por exemplo.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Etapas principais para a instalação do SAP HANA quando você usa o HDBLCM
Esta seção lista as principais etapas para uma instalação manual de SAP HANA de instância única quando você usa o SAP HDBLCM para executar uma instalação distribuída do SAP NetWeaver 7,5. As etapas individuais são explicadas em mais detalhes em capturas de tela em todo este guia.

1. Crie uma rede virtual do Azure que inclua duas VMs de teste.
2. Implante duas VMs do Azure com sistemas operacionais de acordo com o modelo de Azure Resource Manager. Este exemplo usa SLES e SLES para aplicativos SAP 12 SP1.
3. Anexe dois discos de armazenamento Standard ou Premium do Azure, por exemplo, discos de 75 GB ou 500 GB, à VM do servidor de aplicativos.
4. Anexe discos de armazenamento Premium à VM do servidor de BD HANA. Para obter mais informações, consulte a seção "configuração de disco" mais adiante neste guia.
5. Dependendo dos requisitos de tamanho ou taxa de transferência, anexe vários discos. Crie volumes distribuídos usando o gerenciamento de volume lógico ou uma ferramenta mdadm no nível do sistema operacional dentro da VM.
6. Crie sistemas de arquivos XFS nos discos conectados ou volumes lógicos.
7. Monte os novos sistemas de arquivos XFS no nível do sistema operacional. Use um sistema de arquivos para todo o software SAP. Use o outro sistema de arquivos para o diretório/sapmnt e backups, por exemplo. No servidor do SAP HANA DB, monte os sistemas de arquivos XFS nos discos do armazenamento Premium como/Hana e/usr/SAP. Esse processo é necessário para ajudar a impedir que o sistema de arquivos raiz seja preenchido. O sistema de arquivos raiz não é grande em VMs do Azure do Linux.
8. Insira os endereços IP locais das VMs de teste no arquivo/etc/hosts.
9. Insira o parâmetro nofail no arquivo/etc/fstab.
10. Defina parâmetros de kernel de acordo com a versão do sistema operacional Linux que você usa. Para obter mais informações, consulte as notas SAP que discutem o HANA e a seção "parâmetros de kernel" neste guia.
11. Adicionar espaço de permuta.
12. Opcionalmente, instale uma área de trabalho gráfica nas VMs de teste. Caso contrário, use uma instalação de SAPinst remota.
13. Baixe o software SAP do SAP Service Marketplace.
14. Crie um grupo, SAPs, com a ID de grupo 1001, na VM do servidor de BD HANA.
15. Instale SAP HANA na VM do servidor de banco de dados usando o HANA Database Lifecycle Manager.
16. Instale a instância do SAP ASCS na VM do servidor de aplicativos.
17. Compartilhe o diretório/sapmnt entre as VMs de teste usando o NFS. A VM do servidor de aplicativos é o servidor NFS.
18. Instale a instância do banco de dados, que inclui o HANA, usando SWPM na VM do servidor de BD HANA.
19. Instale o servidor de aplicativos primário na VM do servidor de aplicativos.
20. Inicie o SAP MC. Conecte-se por meio do SAP GUI ou HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Preparar VMs do Azure para uma instalação manual do SAP HANA
Esta seção aborda os seguintes tópicos:

* Atualizações do so
* Configuração do disco
* Parâmetros do kernel
* Sistemas de arquivos
* O arquivo/etc/hosts
* O arquivo/etc/fstab

### <a name="os-updates"></a>Atualizações do so
Verifique se há atualizações e correções do SO Linux antes de instalar o software adicional. Ao instalar um patch, você pode evitar uma chamada para o suporte técnico.

Certifique-se de usar:
* SUSE Linux Enterprise Server para aplicativos SAP.
* Red Hat Enterprise Linux para aplicativos SAP ou Red Hat Enterprise Linux para SAP HANA. 

Se você ainda não tiver feito isso, registre a implantação do sistema operacional com sua assinatura do Linux do fornecedor do Linux. O SUSE tem imagens do sistema operacional para aplicativos SAP que já incluem serviços e que são registrados automaticamente.

Veja um exemplo de como verificar se há patches disponíveis para o SUSE Linux usando o comando **zypper** :

 `sudo zypper list-patches`

Dependendo do tipo de problema, os patches são classificados por categoria e severidade. Os valores comumente usados para a categoria são: 
- Segurança
- Recomendado
- Opcional
- Funcionalidade
- Documento
- YaST

Valores comumente usados para gravidade são:

- Crítico
- Importante
- Moderado
- Baixa
- Não Especificado

O comando **zypper** procura apenas as atualizações de que seus pacotes instalados precisam. Por exemplo, você pode usar este comando:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Você pode adicionar o parâmetro `--dry-run` para testar a atualização sem realmente atualizar o sistema.


### <a name="disk-setup"></a>Configuração do disco
O sistema de arquivos raiz em uma VM Linux no Azure tem uma limitação de tamanho. Portanto, você precisa anexar espaço em disco adicional a uma VM do Azure para executar o SAP. Para VMs do Azure do servidor de aplicativos SAP, o uso de discos de armazenamento standard do Azure pode ser suficiente. Para SAP HANA VMs do Azure DBMS, o uso de discos de armazenamento Premium do Azure para implementações de produção e não produtos é obrigatório.

Com base nos [requisitos de armazenamento de TDI SAP Hana](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), a seguinte configuração de armazenamento Premium do Azure é sugerida: 

| SKU DA VM | RAM |  /Hana/data e/Hana/log <br /> distribuído com LVM ou mdadm | /Hana/Shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Na configuração de disco sugerida, o volume de dados do HANA e o volume de log são colocados no mesmo conjunto de discos de armazenamento Premium do Azure que são distribuídos com LVM ou mdadm. Não é necessário definir nenhum nível de redundância RAID porque o armazenamento Premium do Azure mantém três imagens dos discos para redundância. 

Para configurar o armazenamento suficiente, consulte [requisitos de armazenamento de SAP Hana TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) e [Guia de instalação e atualização do SAP Hana Server](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Considere também os diferentes volumes de taxa de transferência de VHD (disco rígido virtual) dos diferentes discos de armazenamento Premium do Azure, conforme documentado em [armazenamento Premium de alto desempenho e em discos gerenciados para VMs](../../windows/disks-types.md). 

Você pode adicionar mais discos de armazenamento Premium às VMs do DBMS do HANA para armazenar backups de banco de dados ou de log de transações.

Para obter mais informações sobre as duas ferramentas principais usadas para configurar a distribuição, consulte:

* [Configure o RAID de software no Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Configurar o LVM em uma VM do Linux no Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter mais informações sobre como anexar discos a VMs do Azure que executam o Linux como um sistema operacional convidado, consulte [Adicionar um disco a uma VM do Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Com o SSDs Premium do Azure, você pode definir modos de cache de disco. Para o conjunto distribuído que contém/Hana/data e/Hana/log, desabilite o cache de disco. Para os outros volumes, ou seja, discos, defina o modo de cache como **ReadOnly**.

Para encontrar modelos JSON de exemplo a serem usados para criar VMs, consulte os [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
O modelo VM-simple-SLES é um modelo básico. Ele inclui uma seção de armazenamento com um disco de dados de 100 GB adicional. Use este modelo como base. Você pode adaptar o modelo à sua configuração específica.

> [!NOTE]
> É importante anexar o disco de armazenamento do Azure usando um UUID, conforme documentado em [executar o SAP NetWeaver em VMs Microsoft Azure SuSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

No ambiente de teste, dois discos de armazenamento standard do Azure são anexados à VM do servidor de aplicativos SAP, conforme mostrado na captura de tela a seguir. Um disco armazena todo o software SAP, como NetWeaver 7,5, SAP GUI e SAP HANA, para instalação. O segundo disco garante que espaço livre suficiente esteja disponível para requisitos adicionais. Por exemplo, os dados de backup e teste e o diretório/sapmnt, ou seja, perfis SAP, precisam ser compartilhados entre todas as VMs que pertencem à mesma estrutura SAP.

![SAP HANA janela de discos do servidor de aplicativos exibindo dois discos de dados e seus tamanhos](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parâmetros do kernel
SAP HANA requer configurações específicas do kernel do Linux. Essas configurações de kernel não fazem parte das imagens padrão da galeria do Azure e devem ser definidas manualmente. Dependendo se você usa SUSE ou Red Hat, os parâmetros podem ser diferentes. As notas SAP listadas anteriormente fornecem informações sobre esses parâmetros. Nas capturas de tela mostradas, o SUSE Linux 12 SP1 foi usado. 

SLES for SAP Applications 12-disponibilidade geral e SLES for SAP Applications 12 SP1 têm uma nova ferramenta, **ajustada-ADM**, que substitui a antiga ferramenta de **sapconf** . Um perfil de SAP HANA especial está disponível para o **ajustado-ADM**. Para ajustar o sistema para SAP HANA, insira o seguinte perfil como um usuário raiz:

   `tuned-adm profile sap-hana`

Para obter mais informações sobre o **ajustado-ADM**, consulte a [documentação do SUSE sobre o ajustado-ADM](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Na captura de tela a seguir, você pode ver como **ajustado-ADM** alterou os `transparent_hugepage` valores e `numa_balancing` , de acordo com as configurações de SAP Hana necessárias:

![A ferramenta ajustada do ADM altera os valores de acordo com as configurações de SAP HANA necessárias](./media/hana-get-started/image005.jpg)

Para tornar as configurações de kernel SAP HANA permanentes, use **Grub2** no SLES 12. Para obter mais informações sobre o **Grub2**, consulte a seção [estrutura do arquivo de configuração](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) da documentação do SUSE.

A captura de tela a seguir mostra como as configurações do kernel foram alteradas no arquivo de configuração e, em seguida, compiladas usando **Grub2-mkconfig**:

![Configurações do kernel alteradas no arquivo de configuração e compiladas usando Grub2-mkconfig](./media/hana-get-started/image006.jpg)

Outra opção é alterar as configurações usando o YaST e as configurações de**parâmetros do kernel** do **carregador** > de inicialização:

![A guia Configurações de parâmetros do kernel no carregador de inicialização do YaST](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Sistemas de arquivos
A captura de tela a seguir mostra dois sistemas de arquivos que foram criados na VM do servidor de aplicativos SAP na parte superior dos dois discos de armazenamento standard do Azure anexados. Ambos os sistemas de arquivos são do tipo XFS e são montados em/sapdata e/sapsoftware.

Não é obrigatório estruturar os sistemas de arquivos dessa maneira. Você tem outras opções para estruturar o espaço em disco. A consideração mais importante é impedir que o sistema de arquivos raiz fique sem espaço livre.

![Dois sistemas de arquivos criados na VM do servidor de aplicativos SAP](./media/hana-get-started/image008.jpg)

Para a VM do SAP HANA DB, durante uma instalação de banco de dados, quando você usa o SAPinst com SWPM e a opção de instalação **típica** , tudo é instalado em/Hana e/usr/SAP. O local padrão para o backup de log de SAP HANA está em/usr/SAP. Novamente, é importante impedir que o sistema de arquivos raiz fique sem espaço de armazenamento. Verifique se há espaço livre suficiente em/Hana e/usr/SAP antes de instalar SAP HANA usando SWPM.

Para obter uma descrição do layout padrão do sistema de arquivos de SAP HANA, consulte o [Guia de instalação e atualização do SAP Hana Server](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Sistemas de arquivos adicionais criados na VM do servidor de aplicativos SAP](./media/hana-get-started/image009.jpg)

Quando você instala o SAP NetWeaver em uma imagem SLES/SLES padrão para aplicativos SAP 12 da galeria do Azure, uma mensagem é exibida dizendo que não há espaço de permuta, conforme mostrado na captura de tela a seguir. Para ignorar essa mensagem, você pode adicionar manualmente um arquivo de permuta usando **DD**, **mkswap**e permutation. Para saber como fazer isso, pesquise "adicionando um arquivo de permuta manualmente" na seção [usando o particionador YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) da documentação do SUSE.

Outra opção é configurar o espaço de permuta usando o agente de VM do Linux. Para obter mais informações, veja o [Guia de utilizador do Agente Linux do Azure](../../extensions/agent-linux.md).

![Mensagem pop-up avisando que não há espaço de permuta suficiente](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>O arquivo/etc/hosts
Antes de começar a instalar o SAP, certifique-se de incluir os nomes de host e os endereços IP das VMs SAP no arquivo/etc/hosts. Implante todas as VMs SAP em uma rede virtual do Azure. Em seguida, use os endereços IP internos, conforme mostrado aqui:

![Nomes de host e endereços IP das VMs SAP listadas no arquivo/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>O arquivo/etc/fstab

É útil adicionar o parâmetro **nofail** ao arquivo fstab. Dessa forma, se algo der errado com os discos, a VM não deixará de responder no processo de inicialização. Mas lembre-se de que o espaço em disco adicional pode não estar disponível e os processos podem preencher o sistema de arquivos raiz. Se/Hana estiver ausente, SAP HANA não será iniciado.

![Adicionar o parâmetro nofail ao arquivo fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Área de trabalho GNOME gráfica no SLES 12/SLES para aplicativos SAP 12
Esta seção explica como:

* Instale o desktop GNOME e o xrdp no SLES 12/SLES para aplicativos SAP 12.
* Execute o SAP MC baseado em Java usando o Firefox no SLES 12/SLES para aplicativos SAP 12.

Você também pode usar alternativas como Xterminal ou VNC, que não são descritas neste guia.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Instalar a área de trabalho GNOME e o xrdp no SLES 12/SLES para aplicativos SAP 12
Se você tiver um plano de fundo do Windows, poderá usar facilmente uma área de trabalho gráfica diretamente nas VMs do SAP Linux para executar o Firefox, o SAPinst, o SAP GUI, o SAP MC ou o HANA Studio. Em seguida, você pode se conectar à VM por meio do protocolo RDP (RDP) de um computador com Windows. Dependendo de suas políticas de empresa sobre a adição de GUIs a sistemas de produção e não-produtos baseados em Linux, talvez você queira instalar o GNOME no servidor. Siga estas etapas para instalar a área de trabalho GNOME em uma VM SLES 12/SLES para aplicativos SAP 12 do Azure.

1. Instale o desktop GNOME digitando o seguinte comando, por exemplo, em uma janela de reinicialização:

   `zypper in -t pattern gnome-basic`

2. Instale o xrdp para permitir uma conexão com a VM por meio de RDP:

   `zypper in xrdp`

3. Edite/etc/sysconfig/windowmanager e defina o Gerenciador de janelas padrão como GNOME:

   `DEFAULT_WM="gnome"`

4. Execute **comando chkconfig** para garantir que o xrdp seja iniciado automaticamente após uma reinicialização:

   `chkconfig -level 3 xrdp on`

5. Se você tiver um problema com a conexão RDP, tente reiniciar, por exemplo, de uma janela de reinicialização:

   `/etc/xrdp/xrdp.sh restart`

6. Se uma reinicialização xrdp mencionada na etapa anterior não funcionar, verifique se há um arquivo. PID:

   `check /var/run` 

   `xrdp.pid`Procure. Se você encontrá-lo, remova-o e tente reiniciar novamente.

### <a name="start-sap-mc"></a>Iniciar o SAP MC
Depois de instalar a área de trabalho GNOME, inicie o SAP MC gráfico baseado em Java do Firefox. Se ele for executado em uma VM do Azure SLES 12/SLES para aplicativos SAP 12, ele poderá exibir um erro. O erro ocorre devido ao plug-in de navegador Java ausente.

A URL para iniciar o SAP MC é `<server>:5<instance_number>13`.

Para obter mais informações, consulte [iniciando o console de gerenciamento SAP baseado na Web](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

A captura de tela a seguir mostra a mensagem de erro exibida quando o plug-in do navegador Java está ausente:

![Mensagem de erro indicando o plug-in de navegador Java ausente](./media/hana-get-started/image013.jpg)

Uma maneira de resolver o problema é instalar o plug-in ausente usando o YaST, conforme mostrado na seguinte captura de tela:

![Usando o YaST para instalar o plug-in ausente](./media/hana-get-started/image014.jpg)

Ao inserir novamente a URL do console de gerenciamento do SAP, você será solicitado a ativar o plug-in:

![Caixa de diálogo solicitando ativação de plug-in](./media/hana-get-started/image015.jpg)

Você também pode receber uma mensagem de erro sobre um arquivo ausente, JavaFX. Properties. Ele se relaciona com o requisito do Oracle Java 1,8 para SAP GUI 7,4. Para obter mais informações, consulte [SAP Note 2059429](https://launchpad.support.sap.com/#/notes/2059424).
A versão do IBM Java e o pacote OpenJDK que é fornecido com o SLES/SLES para aplicativos SAP 12 não incluem o arquivo JavaFX. Properties necessário. A solução é baixar e instalar o Java SE 8 da Oracle.

Para obter informações sobre um problema semelhante com o OpenJDK no openSUSE, consulte o thread de discussão [SAPGui 7,4 Java para openSUSE 42,1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Instalação manual do SAP HANA: SWPM
A série de capturas de tela nesta seção mostra as principais etapas de como instalar o SAP NetWeaver 7,5 e SAP HANA SP12 ao usar o SWPM com SAPinst. Como parte de uma instalação do NetWeaver 7,5, o SWPM também pode instalar o banco de dados do HANA como uma única instância.

Em um ambiente de teste de exemplo, instalamos um servidor de aplicativo de ABAP (programação de aplicativos de negócios) avançado. Conforme mostrado na captura de tela a seguir, usamos a opção **Distributed System** para instalar as instâncias de servidor de aplicativo ASCS e Primary em uma VM do Azure. Nós usamos SAP HANA como o sistema de banco de dados em outra VM do Azure.

![ASCS e instâncias do servidor de aplicativos primários instaladas usando a opção Distributed System](./media/hana-get-started/image012.jpg)

Depois que a instância ASCS é instalada na VM do servidor de aplicativos, ela é identificada por um ícone verde no console de gerenciamento do SAP. O diretório/sapmnt, que inclui o diretório de perfil SAP, deve ser compartilhado com a VM do servidor do SAP HANA DB. A etapa de instalação do BD precisa acessar essas informações. A melhor maneira de fornecer acesso é usar NFS, que pode ser configurado usando o YaST.

![Console de gerenciamento SAP mostrando a instância de ASCS instalada na VM do servidor de aplicativos usando um ícone verde](./media/hana-get-started/image016.jpg)

Na VM do servidor de aplicativos, o diretório/sapmnt é compartilhado via NFS usando as opções **RW** e **no_root_squash** . Os padrões são **ro** e **root_squash**, o que pode levar a problemas quando você instala a instância do banco de dados.

![Compartilhando o diretório/sapmnt via NFS usando as opções RW e no_root_squash](./media/hana-get-started/image017b.jpg)

Como mostra a próxima captura de tela, o compartilhamento/sapmnt da VM do servidor de aplicativos deve ser configurado na VM do servidor do SAP HANA DB usando o **cliente NFS** e o YaST:

![O compartilhamento/sapmnt configurado usando o cliente NFS](./media/hana-get-started/image018b.jpg)

Para executar uma instalação distribuída do NetWeaver 7,5, ou seja, uma **instância de banco de dados**, entre na VM do servidor do SAP Hana DB e inicie o SWPM:

![Instalando uma instância de banco de dados entrando na VM do servidor do SAP HANA DB e iniciando o SWPM](./media/hana-get-started/image019.jpg)

Depois de selecionar a instalação **típica** e o caminho para a mídia de instalação, insira um SID de banco de usuários, o nome do host, o número da instância e a senha do administrador do sistema do BD:

![A página de entrada do administrador do sistema de banco de dados SAP HANA](./media/hana-get-started/image035b.jpg)

Insira a senha para o esquema DBACOCKPIT:

![A caixa de entrada de senha para o esquema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Insira uma pergunta para a senha do esquema SAPABAP1:

![Insira uma pergunta para a senha do esquema SAPABAP1](./media/hana-get-started/image037b.jpg)

Após a conclusão de cada tarefa, uma marca de seleção verde é exibida ao lado de cada fase do processo de instalação do BD. A mensagem "execução de... A instância do banco de dados foi concluída "é exibida.

![Janela de tarefa concluída com mensagem de confirmação](./media/hana-get-started/image023.jpg)

Após a instalação bem-sucedida, o console de gerenciamento do SAP também mostra a instância do banco de forma com um ícone verde. Ele exibe a lista completa de processos de SAP HANA, como hdbindexserver e hdbcompileserver.

![Janela do console de gerenciamento SAP com a lista de processos de SAP HANA](./media/hana-get-started/image024.jpg)

A captura de tela a seguir mostra as partes da estrutura do arquivo no diretório/Hana/Shared que o SWPM criou durante a instalação do HANA. Como não há nenhuma opção para especificar um caminho diferente, é importante montar espaço em disco adicional no diretório/Hana antes da instalação do SAP HANA usando o SWPM. Esta etapa impede que o sistema de arquivos raiz fique sem espaço livre.

![A estrutura de arquivos do diretório/Hana/Shared criada durante a instalação do HANA](./media/hana-get-started/image025.jpg)

Esta captura de tela mostra a estrutura de arquivos do diretório/usr/SAP:

![A estrutura de arquivos do diretório/usr/SAP](./media/hana-get-started/image026.jpg)

A última etapa da instalação do ABAP distribuído é instalar a instância do servidor de aplicativos primário:

![Instalação do ABAP mostrando a instância do servidor de aplicativos primário como a etapa final](./media/hana-get-started/image027b.jpg)

Depois que a instância do servidor de aplicativos primário e a GUI do SAP estiverem instaladas, use a transação do **DBA cockpit** para confirmar se a instalação do SAP Hana foi concluída corretamente:

![Janela cockpit do DBA confirmando a instalação bem-sucedida](./media/hana-get-started/image028b.jpg)

Como etapa final, talvez você queira primeiro instalar o HANA Studio na VM do servidor de aplicativos SAP. Em seguida, conecte-se à instância de SAP HANA que está em execução na VM do servidor de BD.

![Instalando o SAP HANA Studio na VM do servidor de aplicativos SAP](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Instalação manual do SAP HANA: HDBLCM
Além de instalar SAP HANA como parte de uma instalação distribuída usando o SWPM, você pode instalar o HANA autônomo primeiro, usando o HDBLCM. Em seguida, você pode instalar o SAP NetWeaver 7,5, por exemplo. As capturas de tela nesta seção mostram como esse processo funciona.

Para obter mais informações sobre a ferramenta HANA HDBLCM, consulte:

* [Escolha a SAP Hana HDBLCM correta para a sua tarefa](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [SAP Hana ferramentas de gerenciamento do ciclo de vida](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [Guia de instalação e atualização do SAP Hana Server](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

Você deseja evitar problemas com uma configuração de ID de grupo padrão para `\<HANA SID\>adm user`o, que é criado pela ferramenta HDBLCM. Antes de instalar SAP Hana via HDBLCM, defina um novo grupo chamado `sapsys` usando a ID `1001`do Grupo:

![Novo grupo "SAPs" definido usando a ID de grupo 1001](./media/hana-get-started/image030.jpg)

Quando você inicia o HDBLCM pela primeira vez, um menu iniciar simples é exibido. Selecione o item 1, **instalar novo sistema**:

![Opção "instalar novo sistema" na janela inicial do HDBLCM](./media/hana-get-started/image031.jpg)

A captura de tela a seguir exibe todas as opções de chave que você selecionou anteriormente.

> [!IMPORTANT]
> Os diretórios que são nomeados para volumes de dados e log do HANA e o caminho de instalação, que é/Hana/Shared neste exemplo, e/usr/SAP não devem fazer parte do sistema de arquivos raiz. Esses diretórios pertencem aos discos de dados do Azure que foram anexados à VM. Para obter mais informações, consulte a seção "configuração de disco". 

Essa abordagem ajuda a impedir que o sistema de arquivos raiz fique sem espaço. Observe que o administrador do sistema Hana tem a `1005` ID `sapsys` de usuário e faz parte do grupo, `1001`com ID, que foi definido antes da instalação.

![Lista de todos os componentes de SAP HANA de chave selecionados anteriormente](./media/hana-get-started/image032.jpg)

Verifique os `\<HANA SID\>adm user` detalhes no diretório/etc/passwd `azdadm`Procure, conforme mostrado na seguinte captura de tela:

![Detalhes \<do usuário\>ADM de SID do Hana Hana listados no diretório/etc/passwd](./media/hana-get-started/image033.jpg)

Depois de instalar o SAP HANA usando o HDBLCM, você poderá ver a estrutura do arquivo no SAP HANA Studio, conforme mostrado na captura de tela a seguir. O esquema SAPABAP1, que inclui todas as tabelas do SAP NetWeaver, ainda não está disponível.

![SAP HANA estrutura de arquivos no SAP HANA Studio](./media/hana-get-started/image034.jpg)

Depois de instalar o SAP HANA, você pode instalar o SAP NetWeaver sobre ele. Conforme mostrado na captura de tela a seguir, a instalação foi executada como uma instalação distribuída usando SWPM. Esse processo é descrito na seção anterior. Ao instalar a instância do banco de dados usando o SWPM, você insere os mesmos dados usando HDBLCM. Por exemplo, insira o nome do host, o SID do HANA e o número da instância. Em seguida, o SWPM usa a instalação existente do HANA e adiciona mais esquemas.

![Uma instalação distribuída executada usando SWPM](./media/hana-get-started/image035b.jpg)

A captura de tela a seguir mostra a etapa de instalação do SWPM em que você insere dados sobre o esquema DBACOCKPIT:

![A etapa de instalação do SWPM em que os dados do esquema DBACOCKPIT são inseridos](./media/hana-get-started/image036b.jpg)

Insira dados sobre o esquema SAPABAP1:

![Inserindo dados sobre o esquema SAPABAP1](./media/hana-get-started/image037b.jpg)

Depois que a instalação da instância do banco de dados SWPM for concluída, você poderá ver o esquema SAPABAP1 no SAP HANA Studio:

![O esquema SAPABAP1 no SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Por fim, após a conclusão das instalações do servidor de aplicativos SAP e da GUI SAP, verifique a instância do banco de BD do HANA usando a transação do **DBA cockpit** :

![A instância do HANA DB verificada com a transação cockpit do DBA](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Downloads de software SAP
Você pode baixar o software do SAP Service Marketplace, conforme mostrado nas capturas de tela a seguir.

Baixe o NetWeaver 7,5 para Linux/HANA:

 ![Janela de instalação e atualização do serviço SAP para baixar o NetWeaver 7,5](./media/hana-get-started/image001.jpg)

Baixe o HANA SP12 Platform Edition:

 ![Janela de instalação e atualização do serviço SAP para baixar o HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)

