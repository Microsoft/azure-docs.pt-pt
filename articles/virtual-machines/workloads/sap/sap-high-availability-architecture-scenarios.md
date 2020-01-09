---
title: Arquitetura e cenários de alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver | Microsoft Docs
description: Arquitetura e cenários de alta disponibilidade para o SAP NetWeaver em máquinas virtuais do Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1a52977a46c1222a1626fa5a4dcb4de7dd84f8dd
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638209"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Arquitetura e cenários de alta disponibilidade para o SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de alta disponibilidade do SAP multi-SID)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="terminology-definitions"></a>Definições de terminologia

**Alta disponibilidade**: refere-se a um conjunto de tecnologias que minimizam as interrupções de ti, fornecendo continuidade de negócios dos serviços de ti por meio de componentes redundantes, tolerantes a falhas ou protegidos por failover dentro do *mesmo* Data Center. Em nosso caso, a data center reside em uma região do Azure.

**Recuperação de desastres**: também se refere à minimização da interrupção dos serviços de ti e à sua recuperação, mas em *vários* data centers que podem estar a centenas de quilômetros uns dos outros. Em nosso caso, os data centers podem residir em várias regiões do Azure na mesma região do geopolítica ou em locais, conforme estabelecido por você como um cliente.


## <a name="overview-of-high-availability"></a>Visão geral da alta disponibilidade
A alta disponibilidade do SAP no Azure pode ser separada em três tipos:

* **Alta disponibilidade de infraestrutura do Azure**: 

    Por exemplo, a alta disponibilidade pode incluir VMs (computação), rede ou armazenamento e seus benefícios para aumentar a disponibilidade de aplicativos SAP.

* **Utilizando a reinicialização de VM de infraestrutura do Azure para obter *maior disponibilidade* de aplicativos SAP**: 

    Se você decidir não usar funcionalidades como o WSFC (Windows Server failover clustering) ou o pacemaker no Linux, a reinicialização de VM do Azure será utilizada. Ele protege os sistemas SAP contra tempo de inatividade planejado e não planejado da infraestrutura de servidor físico do Azure e da plataforma subjacente do Azure.

* **Alta disponibilidade do aplicativo SAP**: 

    Para obter a alta disponibilidade completa do sistema SAP, você deve proteger todos os componentes críticos do sistema SAP. Por exemplo:
    * Servidores de aplicativos SAP redundantes.
    * Componentes exclusivos. Um exemplo pode ser um componente SPOF (ponto único de falha), como uma instância do SAP ASCS/SCS ou um DBMS (sistema de gerenciamento de banco de dados).

A alta disponibilidade do SAP no Azure difere da alta disponibilidade do SAP em um ambiente físico ou virtual local. O documento a seguir [, alta disponibilidade e continuidade dos negócios do SAP NetWeaver em ambientes virtuais com VMware e Hyper-V no Microsoft Windows,][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] descreve as configurações padrão de alta disponibilidade do SAP em ambientes virtualizados no Windows.

Não há nenhuma configuração de alta disponibilidade do SAP integrada ao sapinst para Linux como há para o Windows. Para obter informações sobre a alta disponibilidade do SAP local para Linux, consulte [informações de parceiros de alta disponibilidade][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Alta disponibilidade de infraestrutura do Azure

### <a name="sla-for-single-instance-virtual-machines"></a>SLA para máquinas virtuais de instância única

Atualmente, há um SLA de VM única de 99,9% com o armazenamento Premium. Para ter uma ideia de como a disponibilidade de uma única VM pode ser, você pode criar o produto dos vários contratos de [nível de serviço do Azure][azure-sla]disponíveis.

A base para o cálculo é de 30 dias por mês ou de 43.200 minutos. Por exemplo, um tempo de inatividade de 0, 5% corresponde a 21,6 minutos. Como de costume, a disponibilidade dos vários serviços é calculada da seguinte maneira:

(Serviço de disponibilidade #1/100) * (serviço de disponibilidade #2/100) * (serviço de disponibilidade #3/100) \*...

Por exemplo:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 ou uma disponibilidade geral de 99,75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Várias instâncias de máquinas virtuais no mesmo conjunto de disponibilidade
Para todas as máquinas virtuais que têm duas ou mais instâncias implantadas no mesmo *conjunto de disponibilidade*, garantimos que você terá conectividade de máquina virtual com pelo menos 99,95% do tempo.

Quando duas ou mais VMs fazem parte do mesmo conjunto de disponibilidade, cada máquina virtual no conjunto de disponibilidade recebe um *domínio de atualização* e um *domínio de falha* pela plataforma subjacente do Azure.

* Os **domínios de atualização** garantem que várias VMs não sejam reinicializadas ao mesmo tempo durante a manutenção planejada de uma infraestrutura do Azure. Apenas uma VM é reiniciada por vez.

* Os **domínios de falha** garantem que as VMs sejam implantadas em componentes de hardware que não compartilham uma fonte de energia e um comutador de rede comuns. Quando servidores, um comutador de rede ou uma fonte de energia passam por um tempo de inatividade não planejado, apenas uma VM é afetada.

Para obter mais informações, consulte [gerenciar a disponibilidade de máquinas virtuais do Windows no Azure][azure-virtual-machines-manage-availability].

Um conjunto de disponibilidade é usado para obter alta disponibilidade de:

* Servidores de aplicativos SAP redundantes.  
* Clusters com dois ou mais nós (VMs, por exemplo) que protegem SPOFs, como uma instância do SAP ASCS/SCS ou um DBMS.


### <a name="azure-availability-zones"></a>Zonas de Disponibilidade do Azure
O Azure está em processo de distribuir os conceitos de [zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) em diferentes [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). Nas regiões do Azure nas quais Zonas de Disponibilidade são oferecidas, as regiões do Azure têm vários data centers, que são independentes no fornecimento de fonte de energia, resfriamento e rede. O motivo para oferecer diferentes zonas em uma única região do Azure é permitir que você implante aplicativos em dois ou três Zonas de Disponibilidade oferecidos. Supondo que os problemas nas fontes de energia e/ou na rede afetem apenas uma infraestrutura de zona de disponibilidade, a implantação do aplicativo em uma região do Azure ainda estará totalmente funcional. Eventualmente, com alguma capacidade reduzida, uma vez que algumas VMs em uma zona podem ser perdidas. Mas as VMs nas outras duas zonas ainda estão em funcionamento. As regiões do Azure que oferecem zonas são listadas em [zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Usando Zonas de Disponibilidade, há algumas coisas a serem consideradas. A lista de considerações como:

- Não é possível implantar conjuntos de disponibilidade do Azure em uma zona de disponibilidade. Você precisa escolher uma zona de disponibilidade ou um conjunto de disponibilidade como quadro de implantação para uma VM.
- Você não pode usar o [Load Balancer básico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) para criar soluções de cluster de failover com base nos serviços de cluster de failover do Windows ou no pacemaker do Linux. Em vez disso, você precisa usar o [SKU Standard Load Balancer do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)
- Zonas de Disponibilidade do Azure não estão concedendo garantias de determinada distância entre as diferentes zonas dentro de uma região
- A latência de rede entre diferentes Zonas de Disponibilidade do Azure dentro das diferentes regiões do Azure pode ser diferente da região do Azure para a região. Haverá casos em que você, como cliente, pode executar razoavelmente a camada de aplicativo SAP implantada em diferentes zonas, uma vez que a latência de rede de uma zona para a VM do DBMS ativo ainda é aceitável de um impacto no processo de negócios. Enquanto que haverá cenários de clientes em que a latência entre a VM do DBMS ativo em uma zona e uma instância do aplicativo SAP em uma VM em outra zona pode ser muito invasiva e não aceitável para os processos de negócios do SAP. Como resultado, as arquiteturas de implantação precisam ser diferentes com uma arquitetura ativa/ativa para o aplicativo ou a arquitetura ativa/passiva se a latência for muito alta.
- O uso do [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) é obrigatório para a implantação no zonas de disponibilidade do Azure 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Manutenção planejada e não planejada de máquinas virtuais

Dois tipos de eventos da plataforma Azure podem afetar a disponibilidade de suas máquinas virtuais:

* Os eventos de **manutenção planejada** são atualizações periódicas feitas pela Microsoft na plataforma subjacente do Azure. As atualizações melhoram a confiabilidade, o desempenho e a segurança geral da infraestrutura da plataforma em que as máquinas virtuais são executadas.

* Os eventos de **manutenção não planejados** ocorrem quando o hardware ou a infraestrutura física subjacente à sua máquina virtual falhou de alguma maneira. Isso pode incluir falhas de rede local, falhas no disco local ou outras falhas no nível do rack. Quando essa falha é detectada, a plataforma do Azure migra automaticamente sua máquina virtual do servidor físico não íntegro que hospeda sua máquina virtual para um servidor físico íntegro. Esses eventos são raros, mas também podem fazer com que sua máquina virtual seja reinicializada.

Para obter mais informações, consulte [gerenciar a disponibilidade de máquinas virtuais do Windows no Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Redundância de armazenamento do Azure
Os dados em sua conta de armazenamento são sempre replicados para garantir durabilidade e alta disponibilidade, atendendo ao SLA do armazenamento do Azure mesmo diante de falhas transitórias de hardware.

Como o armazenamento do Azure mantém três imagens dos dados por padrão, o uso de RAID 5 ou RAID 1 em vários discos do Azure é desnecessário.

Para obter mais informações, consulte [replicação do armazenamento do Azure][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Managed Disks do Azure
Managed Disks é um tipo de recurso em Azure Resource Manager que é recomendado para ser usado em vez de VHDs (discos rígidos virtuais) armazenados em contas de armazenamento do Azure. Os discos gerenciados são alinhados automaticamente com um conjunto de disponibilidade do Azure da máquina virtual à qual estão conectados. Eles aumentam a disponibilidade de sua máquina virtual e os serviços que estão em execução nela.

Para obter mais informações, consulte [visão geral do Azure Managed disks][azure-storage-managed-disks-overview].

Recomendamos que você use discos gerenciados porque eles simplificam a implantação e o gerenciamento de suas máquinas virtuais.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Utilizando a alta disponibilidade da infraestrutura do Azure para obter *maior disponibilidade* de aplicativos SAP

Se você decidir não usar funcionalidades como WSFC ou pacemaker no Linux (atualmente com suporte apenas para o SUSE Linux Enterprise Server [SLES] 12 e posterior), a reinicialização de VM do Azure será utilizada. Ele protege os sistemas SAP contra tempo de inatividade planejado e não planejado da infraestrutura de servidor físico do Azure e da plataforma subjacente do Azure.

Para obter mais informações sobre essa abordagem, consulte [utilizar a reinicialização de VM de infraestrutura do Azure para obter maior disponibilidade do sistema SAP][sap-higher-availability].

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a>Alta disponibilidade de aplicativos SAP no Azure IaaS

Para obter a alta disponibilidade completa do sistema SAP, você deve proteger todos os componentes críticos do sistema SAP. Por exemplo:
  * Servidores de aplicativos SAP redundantes.
  * Componentes exclusivos. Um exemplo pode ser um componente SPOF (ponto único de falha), como uma instância do SAP ASCS/SCS ou um DBMS (sistema de gerenciamento de banco de dados).

As seções a seguir discutem como obter alta disponibilidade para todos os três componentes críticos do sistema SAP.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Arquitetura de alta disponibilidade para servidores de aplicativos SAP

> Esta seção aplica-se a:
>
> ![Windows][Logo_Windows] Windows e ![Linux][Logo_Linux] Linux
>

Normalmente, você não precisa de uma solução específica de alta disponibilidade para o servidor de aplicativos SAP e as instâncias de caixa de diálogo. Você obtém alta disponibilidade por redundância e configura várias instâncias de caixa de diálogo em várias instâncias de máquinas virtuais do Azure. Você deve ter pelo menos duas instâncias do aplicativo SAP instaladas em duas instâncias de máquinas virtuais do Azure.

![Figura 1: servidor de aplicativos SAP de alta disponibilidade][sap-ha-guide-figure-2000]

_**Figura 1:** Servidor de aplicativos SAP de alta disponibilidade_

Você deve posicionar todas as máquinas virtuais que hospedam instâncias do servidor de aplicativos SAP no mesmo conjunto de disponibilidade do Azure. Um conjunto de disponibilidade do Azure garante que:

* Todas as máquinas virtuais fazem parte do mesmo domínio de atualização.  
    Um domínio de atualização garante que as máquinas virtuais não sejam atualizadas simultaneamente durante o tempo de inatividade da manutenção planejada.

    A funcionalidade básica, que se baseia em diferentes domínios de falha e atualização dentro de uma unidade de escala do Azure, já foi introduzida na seção [domínios de atualização][planning-guide-3.2.2] .

* Todas as máquinas virtuais fazem parte do mesmo domínio de falha.  
    Um domínio de falha garante que as máquinas virtuais sejam implantadas para que nenhum ponto único de falha afete a disponibilidade de todas as máquinas virtuais.

O número de domínios de atualização e de falha que podem ser usados por um conjunto de disponibilidade do Azure em uma unidade de escala do Azure é finito. Se você continuar adicionando VMs a um único conjunto de disponibilidade, duas ou mais VMs acabarão, eventualmente, no mesmo domínio de falha ou de atualização.

Se você implantar algumas instâncias do servidor de aplicativos SAP em suas VMs dedicadas, supondo que tenhamos cinco domínios de atualização, a imagem a seguir surgirá. O número máximo real de domínios de atualização e falha dentro de um conjunto de disponibilidade pode mudar no futuro:

![Figura 2: alta disponibilidade de servidores de aplicativos SAP em um conjunto de disponibilidade do Azure][planning-guide-figure-3000]
_ **Figura 2:** alta disponibilidade de servidores de aplicativos SAP em um conjunto de disponibilidade do Azure_

Para obter mais informações, consulte [gerenciar a disponibilidade de máquinas virtuais do Windows no Azure][azure-virtual-machines-manage-availability].

Para obter mais informações, consulte a seção [conjuntos de disponibilidade do Azure][planning-guide-3.2.3] do documento planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver.

**Somente discos não gerenciados:** Como a conta de armazenamento do Azure é um possível ponto único de falha, é importante ter pelo menos duas contas de armazenamento do Azure, nas quais pelo menos duas máquinas virtuais são distribuídas. Em uma configuração ideal, os discos de cada máquina virtual que está executando uma instância da caixa de diálogo do SAP seriam implantados em uma conta de armazenamento diferente.

> [!IMPORTANT]
> É altamente recomendável que você use o Azure Managed disks para suas instalações de alta disponibilidade do SAP. Como os discos gerenciados se alinham automaticamente com o conjunto de disponibilidade da máquina virtual à qual eles estão conectados, eles aumentam a disponibilidade da sua máquina virtual e dos serviços que estão em execução nela.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Arquitetura de alta disponibilidade para uma instância do SAP ASCS/SCS no Windows

> ![Windows][Logo_Windows] Windows
>

Você pode usar uma solução WSFC para proteger a instância do SAP ASCS/SCS. A solução tem duas variantes:

* **Clusterizar a instância do SAP ASCS/SCS usando discos compartilhados clusterizados**: para obter mais informações sobre essa arquitetura, consulte [cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado do cluster][sap-high-availability-guide-wsfc-shared-disk].   

* **Clusterizar a instância do SAP ASCS/SCS usando o compartilhamento de arquivos**: para obter mais informações sobre essa arquitetura, consulte [cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando o compartilhamento de arquivos][sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Arquitetura de alta disponibilidade para uma instância do SAP ASCS/SCS no Linux

> ![Linux][Logo_Linux] Linux
> 
> Para obter mais informações sobre como clusterizar a instância do SAP ASCS/SCS usando a estrutura de cluster SLES, consulte [alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server para aplicativos SAP][sap-suse-ascs-ha]. Para uma arquitetura de HA alternativa no SLES, que não requer NFS altamente disponível, consulte o [Guia de alta disponibilidade para SAP NetWeaver em SuSE Linux Enterprise Server com Azure NetApp Files para aplicativos SAP][sap-suse-ascs-ha-anf].

Para obter mais informações sobre como clusterizar a instância do SAP ASCS/SCS usando a estrutura de cluster do Red Hat, consulte [alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>Configuração de vários SID do SAP NetWeaver para uma instância do SAP ASCS/SCS clusterizada

> ![Windows][Logo_Windows] Windows
> 
> Atualmente, há suporte para vários SID somente com o WSFC. Há suporte para vários SID usando o compartilhamento de arquivos e o disco compartilhado.
> 
> Para obter mais informações sobre a arquitetura de alta disponibilidade de vários SIDs, consulte:

* [Alta disponibilidade de multi-SID da instância do SAP ASCS/SCS para clustering de failover do Windows Server e compartilhamento de arquivos][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Alta disponibilidade de multi-SID da instância do SAP ASCS/SCS para clustering de failover do Windows Server e disco compartilhado][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Instância de DBMS de alta disponibilidade

O DBMS também é um ponto único de contato em um sistema SAP. Você precisa protegê-lo usando uma solução de alta disponibilidade. A figura a seguir mostra uma solução de alta disponibilidade SQL Server AlwaysOn no Azure, com o clustering de failover do Windows Server e o balanceador de carga interno do Azure. SQL Server o AlwaysOn replica os dados do DBMS e os arquivos de log usando sua própria replicação de DBMS. Nesse caso, você não precisa de um disco compartilhado clusterizado, o que simplifica toda a configuração.

![Figura 3: exemplo de um DBMS SAP de alta disponibilidade, com o SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Figura 3:** Exemplo de um DBMS SAP de alta disponibilidade, com o SQL Server AlwaysOn_

Para obter mais informações sobre clustering SQL Server DBMS no Azure usando o modelo de implantação Azure Resource Manager, consulte estes artigos:

* [Configurar um grupo de disponibilidade AlwaysOn em máquinas virtuais do Azure manualmente usando o Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Configurar um balanceador de carga interno do Azure para um grupo de disponibilidade AlwaysOn no Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Para obter mais informações sobre clustering SAP HANA DBMS no Azure usando o modelo de implantação Azure Resource Manager, consulte [alta disponibilidade de SAP Hana em VMS (máquinas virtuais) do Azure][sap-hana-ha].
