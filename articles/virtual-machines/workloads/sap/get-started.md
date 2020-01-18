---
title: Introdução ao SAP em VMs do Azure | Microsoft Docs
description: Saiba mais sobre as soluções SAP executadas em VMs (máquinas virtuais) no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3712e67e95c8957d2537660add14d6cdb09d609f
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264819"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Usar o Azure para hospedar e executar cenários de carga de trabalho do SAP

Ao usar o Microsoft Azure, você pode executar de forma confiável suas cargas de trabalho e cenários de missão crítica do SAP em uma plataforma escalonável, em conformidade e comprovada pela empresa. Você Obtém a escalabilidade, a flexibilidade e a economia de custos do Azure. Com a parceria expandida entre a Microsoft e a SAP, você pode executar aplicativos SAP em cenários de desenvolvimento e teste e produção no Azure e ter total suporte. Do SAP NetWeaver ao SAP S/4HANA, SAP BI no Linux ao Windows e SAP HANA ao SQL, temos a você a você.

Além de hospedar cenários do SAP NetWeaver com o DBMS diferente no Azure, você pode hospedar outros cenários de carga de trabalho do SAP, como o SAP BI no Azure. 

A exclusividade do Azure para SAP HANA é uma oferta que define o Azure separadamente. Para habilitar a hospedagem de mais cenários SAP que exigem recursos de memória e CPU que envolvem SAP HANA, o Azure oferece o uso de hardware bare-metal dedicado ao cliente. Use esta solução para executar implantações SAP HANA que exigem até 24 TB (escala horizontal de 120 TB) de memória para S/4HANA ou outra carga de trabalho de SAP HANA. 

A hospedagem de cenários de carga de trabalho do SAP no Azure também pode criar requisitos de integração de identidade e logon único. Essa situação pode ocorrer quando você usa o Azure Active Directory (Azure AD) para conectar diferentes componentes SAP e ofertas de software como serviço (SaaS) ou plataforma como serviço (PaaS) do SAP. Uma lista desses cenários de integração e logon único com o Azure AD e as entidades do SAP é descrita e documentada na seção "integração de identidade e logon único do AAD SAP".

## <a name="changes-to-the-sap-workload-section"></a>Alterações na seção de carga de trabalho do SAP
Alterações em documentos na seção de carga de trabalho do SAP no Azure estão listadas no final deste artigo.


## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA no Azure (Instâncias Grandes)

Uma série de documentos o conduz por SAP HANA no Azure (instâncias grandes) ou para instâncias grandes e HANA. Para obter informações sobre as seguintes áreas do HANA em instâncias grandes, consulte:

- [Visão geral do SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Arquitetura de SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infraestrutura e conectividade para SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Instalar SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Alta disponibilidade e recuperação de desastre de SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Solucionar problemas e monitorar SAP HANA no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Passos seguintes:

- Leia [visão geral e arquitetura de SAP Hana no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA em máquinas virtuais do Azure
Esta seção da documentação aborda diferentes aspectos de SAP HANA. Como pré-requisito, você deve estar familiarizado com os principais serviços do Azure que fornecem serviços elementares do Azure IaaS. Portanto, você precisa de conhecimento da computação, do armazenamento e da rede do Azure. Muitos desses assuntos são tratados no [Guia de planejamento do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)relacionado ao SAP NetWeaver. 

Para obter informações sobre o HANA no Azure, consulte os seguintes artigos e seus subartigos:

- [Início rápido: instalação manual de SAP HANA de instância única em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Implantar o SAP S/4HANA ou BW/4HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Configurações e operações de infraestrutura do SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA alta disponibilidade para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP HANA disponibilidade em uma região do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Disponibilidade de SAP HANA nas regiões do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Alta disponibilidade de SAP HANA em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guia de backup para SAP HANA em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA o backup do Azure no nível do arquivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA backup com base em instantâneos de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver implantado em máquinas virtuais do Azure
Esta seção lista a documentação de planejamento e implantação para o SAP NetWeaver e Business One no Azure. A documentação se concentra nas noções básicas e no uso de bancos de dados não HANA com uma carga de trabalho do SAP no Azure. Os documentos e artigos para alta disponibilidade também são a base para a alta disponibilidade do HANA no Azure, como:

- [SAP Business One em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Implantar SAP IDES EHP7 SP3 para SAP ERP 6,0 no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Executar o SAP NetWeaver em VMs Microsoft Azure SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Implantação de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Proteger uma implantação de aplicativo do SAP NetWeaver de várias camadas usando Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Conector de SAP LaMa para o Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Para obter informações sobre bancos de dados não HANA em uma carga de trabalho do SAP no Azure, consulte:

- [Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server implantação de DBMS de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Implementação em Oracle do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Implementação em IBM DB2 do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, cache dinâmico e implantação de servidor de conteúdo em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Para obter informações sobre bancos de dados SAP HANA no Azure, consulte a seção "SAP HANA em máquinas virtuais do Azure".

Para obter informações sobre a alta disponibilidade de uma carga de trabalho do SAP no Azure, consulte:

- [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Este documento aponta para vários outros documentos de arquitetura e cenário. Em documentos de cenário posterior, os links para documentos técnicos detalhados que explicam a implantação e a configuração dos diferentes métodos de alta disponibilidade são fornecidos. Os diferentes documentos que mostram como estabelecer e configurar a alta disponibilidade para uma carga de trabalho do SAP NetWeaver abrangem sistemas operacionais Linux e Windows.


Para obter informações sobre a integração entre Azure Active Directory (Azure AD) e serviços SAP e logon único, consulte:

- [Tutorial: integração do Azure Active Directory com o SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: integração do Azure Active Directory com a autenticação de identidade da SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: integração do Azure Active Directory com a plataforma de nuvem SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: integração do Azure Active Directory com o SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: integração do Azure Active Directory com o SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: integração do Azure Active Directory com o SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Seu ambiente S/4HANA: logon único SAML do Fiori Launchpad com o Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Para obter informações sobre a integração dos serviços do Azure em componentes SAP, consulte:

- [Utilizar o SAP HANA no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Utilizar o conector SAP BW no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [O Azure Data Factory oferece integração de dados do SAP HANA e Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Registo de Alterações
- 01/16/2020: alterar em [como instalar e configurar SAP Hana (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) para adaptar as versões do sistema operacional ao diretório de hardware de IaaS do Hana
- 01/16/2020: alterações em [alta disponibilidade para SAP NetWeaver em VMs do Azure no guia de vários SID do SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) para adicionar instruções para sistemas SAP, usando a arquitetura do enqueue Server 2 (ENSA2)
- 01/10/2020: alterações no [SAP Hana escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp files no SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) e no [SAP Hana escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp files no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) para adicionar instruções sobre como tornar as alterações de `nfs4_disable_idmapping` permanentes.
- 01/10/2020: alterações na [alta disponibilidade para SAP NetWeaver em VMs do Azure no SLES com Azure NetApp Files para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) e em [máquinas virtuais do Azure alta disponibilidade para SAP NetWeaver no RHEL com Azure NetApp Files para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) para adicionar instruções sobre como montar volumes Azure NetApp files NFSv4.
- 12/23/2019: lançamento de [alta disponibilidade para SAP NetWeaver em VMs do Azure em guia de vários SID do SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 12/18/2019: versão do [SAP Hana escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp files no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 11/21/2019: alterações no [SAP Hana escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp files no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) para simplificar a configuração do mapeamento de ID do NFS e alterar a interface de rede principal recomendada para simplificar o roteamento.
- 11/15/2019: pequenas alterações na [alta disponibilidade para SAP NetWeaver em SuSE Linux Enterprise Server com Azure NetApp Files para aplicativos SAP](high-availability-guide-suse-netapp-files.md) e [alta disponibilidade para sap NetWeaver no Red Hat Enterprise Linux com Azure NetApp Files para aplicativos SAP](high-availability-guide-rhel-netapp-files.md) para esclarecer as restrições de tamanho do pool de capacidade e remover a instrução que apenas a versão NFSv3 tem suporte.
- 11/12/2019: lançamento de [alta disponibilidade para SAP NetWeaver no Windows com Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: alterações na [alta disponibilidade de SAP Hana em VMs do Azure no SUSE Linux Enterprise Server](sap-hana-high-availability.md), [SAP Hana configurar a replicação do sistema em VMS (máquinas virtuais)](sap-hana-high-availability-rhel.md)do Azure, [a alta disponibilidade de máquinas virtuais do azure para SAP NetWeaver em SuSE Linux Enterprise Server para aplicativos SAP](high-availability-guide-suse.md), alta disponibilidade de máquinas virtuais do Azure [para SAP NetWeaver no SUSE Linux Enterprise Server com Azure NetApp files](high-availability-guide-suse-netapp-files.md), [alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux ](high-availability-guide-rhel.md), [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver em Red Hat Enterprise Linux com Azure NetApp files](high-availability-guide-rhel-netapp-files.md), [alta disponibilidade para NFS em vms do Azure no SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS em VMs do Azure no Red Hat Enterprise Linux para o SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) para recomendar o Azure Standard Load Balancer  
- 11/08/2019: alterações na [lista de verificação de planejamento e implantação da carga de trabalho do SAP](sap-deployment-checklist.md) para esclarecer a recomendação de criptografia  
- 11/04/2019: alterações na [configuração de pacemaker no SUSE Linux Enterprise Server no Azure](high-availability-guide-suse-pacemaker.md) para criar o cluster diretamente com a configuração de unicast  
- 10/29/2019: liberação de [conectividade de ponto de extremidade pública para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: alterações em [SAP Hana configurações de armazenamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) e [SAP Hana expansão com o nó em espera em VMs do Azure com Azure NetApp files no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) para esclarecer o protocolo NFS para o volume/Hana/Shared
- 10/22/2019: alteração na [alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server com Azure NetApp Files para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [alta disponibilidade para NFS em VMs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)do Azure no SUSE Linux Enterprise Server, [Configurando pacemaker em SuSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [alta disponibilidade do IBM DB2 LUW em VMs do Azure no SUSE Linux Enterprise Server com pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), e [alta disponibilidade de SAP Hana em VMs do Azure em SuSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) para proteção da detecção do balanceador de carga do Azure
- Altera a seção de seja e seção de cabeçalho em [SAP Hana configurações de armazenamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: versão do [SAP Hana escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp files no SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: corrigir links desfeitos em [backup e restauração](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: alterar o sistema operacional mínimo recomendado do SLES 12 SP3 para o SLES 12 SP4 em [alta disponibilidade do IBM DB2 LUW em VMs do Azure em SuSE Linux Enterprise Server com pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: alterações nas configurações de armazenamento do ultra Disk e introdução do seja em [configurações de armazenamento de máquina virtual SAP Hana Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: alteração nos elementos gráficos dos [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) para obter mais clareza
- 10/01/2019: alteração nas [configurações de infraestrutura SAP Hana e nas operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) para corrigir instruções sobre o compartilhamento NFS altamente disponível para/Hana/Shared. 
- 09/28/2019: alteração na [configuração de pacemaker em Red Hat Enterprise Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) para esclarecer SBD como um mecanismo de isolamento não tem suporte em CLUSTERs RHEL  
- 09/17/2019: alterar o guia de planejamento e implantação do NetWeaver para unificar os termos relacionados à extensão de VM para SAP  
- 08/22/2019: alterações na [configuração de pacemaker em SuSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) para atualizar as URLs para a criação de função personalizada  
- 08/16/2019: alterações na [configuração de pacemaker em Red Hat Enterprise Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) para lembrar os clientes de atualizar as ações na função personalizada, se atualizar para a nova versão do agente de limite do Azure  
- 08/15/2019: alterações em [SAP Hana configurações de armazenamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para refletir a disponibilidade geral do ultra Disk (anteriormente SSD ultra)
- 08/01/2019: alterações na [configuração de pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) para integrar alterações especificamente para SLES 15 
- 07/23/2019: alterações no [cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um compartilhamento de arquivos no Azure](sap-high-availability-guide-wsfc-file-share.md) para refletir o suporte ao espaço de armazenamento direto por serviços de Azure site Recovery
- 07/14/2019: lançamento de [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md)
- 07/11/2019: alterações em vários documentos que abrangem instâncias grandes do HANA para cobrir a revisão 4 de instâncias grandes do HANA
- 07/09/2019: lançamento do novo guia para [IBM DB2 HADR no Red Hat Enterprise Server](high-availability-guide-rhel-ibm-db2-luw.md)
- 06/13/2019: lançamento de [alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux com Azure NetApp Files para aplicativos SAP](high-availability-guide-rhel-netapp-files.md)


