---
title: Azure VMs HA arquitetura e cenários para SAP NetWeaver Microsoft Docs
description: Arquitetura de alta disponibilidade e cenários para SAP NetWeaver em Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b409254abbde1c1f6156052c49a07e6cc09a4dfd
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958783"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Arquitetura e cenários de alta disponibilidade para SAP NetWeaver

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
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:../../windows/manage-availability.md
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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de alta disponibilidade DE SAP multi-SID)


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

[virtual-machines-manage-availability]:../../manage-availability.md


## <a name="terminology-definitions"></a>Definições de terminologia

**Alta disponibilidade**: Refere-se a um conjunto de tecnologias que minimizam as perturbações de TI, fornecendo continuidade de negócios de serviços de TI através de componentes redundantes, tolerantes a falhas ou protegidos por falhas dentro do *mesmo* centro de dados. No nosso caso, o centro de dados reside numa região de Azure.

**Recuperação de desastres**: Refere-se também à minimização da perturbação dos serviços de TI e à sua recuperação, mas através de *vários* centros de dados que podem estar a centenas de quilómetros uns dos outros. No nosso caso, os centros de dados podem residir em várias regiões do Azure dentro da mesma região geopolítica ou em locais como estabelecido por si como cliente.


## <a name="overview-of-high-availability"></a>Visão geral da alta disponibilidade
A alta disponibilidade do SAP em Azure pode ser separada em três tipos:

* **Alta disponibilidade da infraestrutura azul:** 

    Por exemplo, a alta disponibilidade pode incluir cálculo (VMs), rede ou armazenamento e seus benefícios para aumentar a disponibilidade de aplicações SAP.

* **Utilizando a infraestrutura Azure, o VM reinicia para obter *uma maior disponibilidade* de aplicações SAP**: 

    Se decidir não utilizar funcionalidades como o Clustering de Falha do Servidor do Windows (WSFC) ou o Pacemaker no Linux, o reinício do VM do Azure é utilizado. Protege os sistemas SAP contra o tempo de inatividade planeado e não planeado da infraestrutura de servidores físicos Azure e da plataforma Azure subjacente.

* **Aplicação SAP alta disponibilidade**: 

    Para obter uma disponibilidade completa do sistema SAP, deve proteger todos os componentes críticos do sistema SAP. Por exemplo:
    * Servidores de aplicações SAP redundantes.
    * Componentes únicos. Um exemplo pode ser um único componente de falha (SPOF), como uma instância SAP ASCS/SCS ou um sistema de gestão de bases de dados (DBMS).

A alta disponibilidade do SAP em Azure difere da alta disponibilidade do SAP num ambiente físico ou virtual no local. O seguinte papel [SAP NetWeaver alta disponibilidade e continuidade de negócios em ambientes virtuais com VMware e Hyper-V no Microsoft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] descreve configurações padrão de alta disponibilidade SAP em ambientes virtualizados no Windows.

Não existe nenhuma configuração de alta disponibilidade SAP integrada para o Linux, tal como existe para o Windows. Para obter informações sobre a alta disponibilidade do SAP no local para o Linux, consulte [informações sobre parceiros][sap-ha-partner-information]de alta disponibilidade.

## <a name="azure-infrastructure-high-availability"></a>Alta disponibilidade da infraestrutura Azure

### <a name="sla-for-single-instance-virtual-machines"></a>SLA para máquinas virtuais de instância única

Existe atualmente um SLA único de 99,9% com armazenamento premium. Para ter uma ideia sobre qual pode ser a disponibilidade de um único VM, pode construir o produto dos vários Acordos de [Nível de Serviço Azure][azure-sla]disponíveis.

A base para o cálculo é de 30 dias por mês, ou 43.200 minutos. Por exemplo, uma inatividade de 0,05% corresponde a 21,6 minutos. Como habitualmente, a disponibilidade dos vários serviços é calculada da seguinte forma:

(Serviço de Disponibilidade #1/100) * (Serviço de Disponibilidade #2/100) * (Serviço de Disponibilidade #3/100) \* ...

Por exemplo:

(99.95/100) * (99.9/100) * (99.9/100) = 0,9975 ou uma disponibilidade global de 99,75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Múltiplas instâncias de máquinas virtuais no mesmo conjunto de disponibilidade
Para todas as máquinas virtuais que tenham duas ou mais instâncias implantadas no mesmo *conjunto de disponibilidade,* garantimos que terá conectividade de máquina virtual a pelo menos 99,95% das vezes.

Quando dois ou mais VMs fazem parte do mesmo conjunto de disponibilidade, cada máquina virtual no conjunto de disponibilidade é atribuída a um *domínio de atualização* e a um domínio de *avaria* pela plataforma Azure subjacente.

* **Os domínios de atualização** garantem que vários VM não são reiniciados ao mesmo tempo durante a manutenção planeada de uma infraestrutura Azure. Apenas um VM é reiniciado de cada vez.

* **Os domínios de** avaria garantem que os VMs são implantados em componentes de hardware que não partilham uma fonte de energia comum e um interruptor de rede. Quando os servidores, um interruptor de rede ou uma fonte de energia sofrem um tempo de inatividade não planeado, apenas um VM é afetado.

Para obter mais informações, consulte [Gerir a disponibilidade de máquinas virtuais do Windows no Azure.][azure-virtual-machines-manage-availability]

Um conjunto de disponibilidade é usado para alcançar uma elevada disponibilidade de:

* Servidores de aplicações SAP redundantes.  
* Clusters com dois ou mais nós (VMs, por exemplo) que protegem SPOFs como uma instância SAP ASCS/SCS ou um DBMS.


### <a name="azure-availability-zones"></a>Zonas de Disponibilidade Azure
O Azure está em vias de lançar um conceito de Zonas de [Disponibilidade Azure](../../../availability-zones/az-overview.md) em [diferentes Regiões do Azure.](https://azure.microsoft.com/global-infrastructure/regions/) Nas regiões de Azure onde são oferecidas zonas de disponibilidade, as regiões do Azure dispõem de múltiplos centros de dados, independentes no fornecimento de fonte de energia, arrefecimento e rede. A razão para oferecer diferentes zonas dentro de uma única região de Azure é permitir-lhe implementar aplicações em duas ou três Zonas de Disponibilidade oferecidas. Assumindo que problemas em fontes de energia e/ou rede afetariam apenas uma infraestrutura da Zona de Disponibilidade, a sua implementação de aplicação dentro de uma região do Azure ainda está totalmente funcional. Eventualmente, com alguma capacidade reduzida, uma vez que alguns VMs numa zona podem ser perdidos. Mas os VMs nas outras duas zonas ainda estão a funcionar. As regiões de Azure que oferecem zonas estão listadas em [Zonas de Disponibilidade Azure.](../../../availability-zones/az-overview.md)

Usando Zonas de Disponibilidade, há algumas coisas a considerar. A lista de considerações como:

- Não é possível implantar conjuntos de disponibilidade de Azure dentro de uma Zona de Disponibilidade. Tem de escolher uma Zona de Disponibilidade ou um Conjunto de Disponibilidade como quadro de implantação para um VM.
- Não é possível utilizar o [Balanceador de Carga Básica](../../../load-balancer/load-balancer-overview.md) para criar soluções de cluster com base em Serviços de Cluster Windows Failover ou Nolux Pacemaker. Em vez disso, precisa de utilizar o [Azure Standard Load Balancer SKU](../../../load-balancer/load-balancer-standard-availability-zones.md)
- As Zonas de Disponibilidade azul não estão a dar garantias de certa distância entre as diferentes zonas dentro de uma região
- A latência da rede entre diferentes Zonas de Disponibilidade de Azure nas diferentes regiões do Azure pode ser diferente da região de Azure para a região. Haverá casos, em que você, como cliente, pode razoavelmente executar a camada de aplicação SAP implantada em diferentes zonas, uma vez que a latência da rede de uma zona para a DBMS VM ativa ainda é aceitável a partir de um impacto de processo de negócio. Considerando que haverá cenários de clientes em que a latência entre o DBMS VM ativo numa zona e uma instância de aplicação SAP numa VM noutra zona pode ser demasiado intrusiva e inaceitável para os processos comerciais SAP. Como resultado, as arquiteturas de implantação precisam de ser diferentes com uma arquitetura ativa/ativa para a aplicação ou arquitetura ativa/passiva se a latência for demasiado elevada.
- A utilização de [discos geridos Azure](https://azure.microsoft.com/services/managed-disks/) é obrigatória para implantação em Zonas de Disponibilidade Azure 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Manutenção planeada e não planeada de máquinas virtuais

Dois tipos de eventos da plataforma Azure podem afetar a disponibilidade das suas máquinas virtuais:

* Os eventos **de manutenção planeados** são atualizações periódicas feitas pela Microsoft para a plataforma Azure subjacente. As atualizações melhoram a fiabilidade global, o desempenho e a segurança da infraestrutura da plataforma em que as suas máquinas virtuais funcionam.

* **Eventos de manutenção não planeados** ocorrem quando o hardware ou infraestrutura física subjacente à sua máquina virtual falhou de alguma forma. Pode incluir falhas de rede locais, falhas de disco locais ou outras falhas no nível da cremalheira. Quando tal falha é detetada, a plataforma Azure migra automaticamente a sua máquina virtual do servidor físico pouco saudável que hospeda a sua máquina virtual para um servidor físico saudável. Tais eventos são raros, mas também podem fazer com que a sua máquina virtual reinicie.

Para obter mais informações, consulte [Gerir a disponibilidade de máquinas virtuais do Windows no Azure.][azure-virtual-machines-manage-availability]

### <a name="azure-storage-redundancy"></a>Redundância do Armazenamento do Azure
Os dados na sua conta de armazenamento são sempre replicados para garantir a durabilidade e alta disponibilidade, atendendo ao SLA de Armazenamento Azure mesmo face a falhas de hardware transitórios.

Como o Azure Storage mantém três imagens dos dados por padrão, a utilização do RAID 5 ou RAID 1 em vários discos Azure é desnecessária.

Para obter mais informações, consulte [a replicação do Azure Storage][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Managed Disks do Azure
Os Discos Geridos são um tipo de recurso no Azure Resource Manager que é recomendado para ser usado em vez de discos rígidos virtuais (VHDs) que são armazenados em contas de armazenamento Azure. Os discos geridos alinham-se automaticamente com um conjunto de disponibilidade Azure da máquina virtual a que estão ligados. Aumentam a disponibilidade da sua máquina virtual e os serviços que a estão a executar.

Para mais informações, consulte  [a visão geral dos Discos Geridos Azure][azure-storage-managed-disks-overview].

Recomendamos que utilize discos geridos porque simplificam a implementação e gestão das suas máquinas virtuais.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Utilizando a infraestrutura Azure alta disponibilidade para alcançar *uma maior disponibilidade* de aplicações SAP

Se decidir não utilizar funcionalidades como WSFC ou Pacemaker no Linux (atualmente suportado apenas para SUSE Linux Enterprise Server [SLES] 12 e mais tarde), o reinício do Azure VM é utilizado. Protege os sistemas SAP contra o tempo de inatividade planeado e não planeado da infraestrutura de servidores físicos Azure e da plataforma Azure subjacente.

Para obter mais informações sobre esta abordagem, consulte [o reinício da infraestrutura Utilizar Azure VM para obter uma maior disponibilidade do sistema SAP][sap-higher-availability].

## <a name="high-availability-of-sap-applications-on-azure-iaas"></a><a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Alta disponibilidade de aplicações SAP no Azure IaaS

Para obter uma disponibilidade completa do sistema SAP, deve proteger todos os componentes críticos do sistema SAP. Por exemplo:
  * Servidores de aplicações SAP redundantes.
  * Componentes únicos. Um exemplo pode ser um único componente de falha (SPOF), como uma instância SAP ASCS/SCS ou um sistema de gestão de bases de dados (DBMS).

As próximas secções discutem como alcançar uma elevada disponibilidade para os três componentes críticos do sistema SAP.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Arquitetura de alta disponibilidade para servidores de aplicações SAP

> Esta secção aplica-se a:
>
> ![Logotipo do Windows.][Logo_Windows] Janelas e ![Logotipo linux.][Logo_Linux] Linux
>

Normalmente não precisa de uma solução específica de alta disponibilidade para o servidor de aplicações SAP e instâncias de diálogo. Obtém-se uma elevada disponibilidade por redundância e configura várias instâncias de diálogo em vários casos de máquinas virtuais Azure. Deverá ter pelo menos duas instâncias de aplicação SAP instaladas em duas instâncias de máquinas virtuais Azure.

![Figura 1: Servidor de aplicações SAP de alta disponibilidade][sap-ha-guide-figure-2000]

_**Figura 1:** Servidor de aplicações SAP de alta disponibilidade_

Deve colocar todas as máquinas virtuais que hospedam as instâncias do servidor de aplicações SAP no mesmo conjunto de disponibilidades Azure. Um conjunto de disponibilidade Azure garante que:

* Todas as máquinas virtuais não fazem parte do mesmo domínio de atualização.  
    Um domínio de atualização garante que as máquinas virtuais não são atualizadas ao mesmo tempo durante o tempo de inatividade de manutenção planeado.

    A funcionalidade básica, que se baseia em diferentes domínios de atualização e avaria dentro de uma unidade de escala Azure, já foi introduzida na secção de domínios de [atualização.][planning-guide-3.2.2]

* Todas as máquinas virtuais não fazem parte do mesmo domínio de avaria.  
    Um domínio de avaria garante que as máquinas virtuais são implantadas de modo a que nenhum ponto de falha afete a disponibilidade de todas as máquinas virtuais.

O número de domínios de atualização e avaria que podem ser utilizados por um conjunto de disponibilidades Azure dentro de uma unidade de escala Azure é finito. Se continuar a adicionar VMs a um único conjunto de disponibilidade, dois ou mais VMs acabarão eventualmente no mesmo domínio de falha ou atualização.

Se implementar algumas instâncias do servidor de aplicações SAP nos seus VMs dedicados, assumindo que temos cinco domínios de atualização, surge a seguinte imagem. O número máximo real de domínios de atualização e avaria dentro de um conjunto de disponibilidade pode mudar no futuro:

![Figura 2: Alta disponibilidade de servidores de aplicações SAP num conjunto de disponibilidade Azure ][planning-guide-figure-3000]
 _**Figura 2:** Alta disponibilidade de servidores de aplicações SAP num conjunto de disponibilidade Azure_

Para obter mais informações, consulte [Gerir a disponibilidade de máquinas virtuais do Windows no Azure.][azure-virtual-machines-manage-availability]

Para obter mais informações, consulte a secção de conjuntos de disponibilidade do [Azure][planning-guide-3.2.3] do planeamento e implementação de máquinas virtuais Azure para o documento SAP NetWeaver.

**Apenas discos não geridos:** Como a conta de armazenamento Azure é um potencial ponto único de falha, é importante ter pelo menos duas contas de armazenamento Azure, nas quais pelo menos duas máquinas virtuais são distribuídas. Numa configuração ideal, os discos de cada máquina virtual que está a executar uma instância de diálogo SAP seriam implantados numa conta de armazenamento diferente.

> [!IMPORTANT]
> Recomendamos vivamente que utilize discos geridos Azure para as suas instalações de alta disponibilidade SAP. Como os discos geridos se alinham automaticamente com o conjunto de disponibilidade da máquina virtual a que estão ligados, aumentam a disponibilidade da sua máquina virtual e os serviços que estão a funcionar nela.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Arquitetura de alta disponibilidade para uma instância SAP ASCS/SCS no Windows

> ![Logotipo do Windows.][Logo_Windows] Windows
>

Pode utilizar uma solução WSFC para proteger a instância SAP ASCS/SCS. A solução tem duas variantes:

* **Cluster o caso SAP ASCS/SCS utilizando discos partilhados agrupados**: Para obter mais informações sobre esta arquitetura, consulte [Cluster a UMA INSTÂNCIA SAP ASCS/SCS num cluster de falha do Windows utilizando um disco partilhado de cluster][sap-high-availability-guide-wsfc-shared-disk].   

* **Cluster o caso SAP ASCS/SCS utilizando a partilha de ficheiros**: Para obter mais informações sobre esta arquitetura, consulte [cluster uma instância SAP ASCS/SCS num cluster de failover do Windows utilizando a partilha de ficheiros][sap-high-availability-guide-wsfc-file-share].

* **Cluster o caso SAP ASCS/SCS utilizando a partilha ANF SMB**: Para obter mais informações sobre esta arquitetura, consulte cluster [cluster uma instância SAP ASCS/SCS num cluster de failover do Windows utilizando a partilha de ficheiros ANF SMB](./high-availability-guide-windows-netapp-files-smb.md).

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Arquitetura de alta disponibilidade para uma instância SAP ASCS/SCS em Linux

> ![Logotipo linux.][Logo_Linux] Linux
> 
> Para obter mais informações sobre o agrupamento da instância SAP ASCS/SCS utilizando a estrutura do cluster SLES, consulte [alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicações SAP][sap-suse-ascs-ha]. Para arquitetura HA alternativa no SLES, que não requer NFS altamente disponível consulte [guia de alta disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server com ficheiros Azure NetApp para aplicações SAP][sap-suse-ascs-ha-anf].

Para obter mais informações sobre o agrupamento do caso SAP ASCS/SCS utilizando a estrutura do cluster do chapéu vermelho, consulte [a disponibilidade de alta disponibilidade de Máquinas Virtuais Azure para SAP NetWeaver no Red Hat Enterprise Linux](./high-availability-guide-rhel.md)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>Configuração multi-SID SAP NetWeaver para uma instância SAP ASCS/SCS agrupada

> ![Logotipo do Windows.][Logo_Windows] Windows
> 
> Multi-SID é suportado com WSFC, usando partilha de ficheiros e disco compartilhado.
> 
> Para obter mais informações sobre a arquitetura multi-SID de alta disponibilidade no Windows, consulte:

* [SAP ASCS/SCS exemplo multi-SID alta disponibilidade para Clustering e partilha de ficheiros do Windows Server Failover][sap-ascs-ha-multi-sid-wsfc-file-share]

* [SAP ASCS/SCS exemplo multi-SID alta disponibilidade para Windows Server Failover Clustering e disco compartilhado][sap-ascs-ha-multi-sid-wsfc-shared-disk]

> ![Logotipo linux.][Logo_Linux] Linux
> 
> O agrupamento multi-SID é suportado em clusters Linux Pacemaker para SAP ASCS/ERS, limitado a **cinco** SIDs SAP no mesmo cluster.
> Para obter mais informações sobre a arquitetura multi-SID de alta disponibilidade em Linux, consulte:

* [HA para SAP NW em VMs Azure em SLES para aplicações SAP multi-SID guia](./high-availability-guide-suse-multi-sid.md)
* [HA para SAP NW em VMs Azure em RHEL para aplicações SAP multi-SID guia](./high-availability-guide-rhel-multi-sid.md)

### <a name="high-availability-dbms-instance"></a>Caso DBMS de alta disponibilidade

O DBMS também é um único ponto de contacto num sistema SAP. Você precisa protegê-lo usando uma solução de alta disponibilidade. A seguinte figura mostra uma solução de alta disponibilidade do SQL Server AlwaysOn em Azure, com o Cluster de Falha de Falha do Servidor do Windows e o equilibrador de carga interno Azure. SQL Server AlwaysOn replica dados DBMS e ficheiros de registo utilizando a sua própria replicação DBMS. Neste caso, não precisa de um disco partilhado de cluster, o que simplifica toda a configuração.

![Figura 3: Exemplo de um DBMS SAP de alta disponibilidade, com SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Figura 3:** Exemplo de um DBMS SAP de alta disponibilidade, com SQL Server AlwaysOn_

Para obter mais informações sobre o agrupamento de DBMS do SQL Server em Azure utilizando o modelo de implementação do Gestor de Recursos Azure, consulte estes artigos:

* [Configure um grupo de disponibilidade AlwaysOn em máquinas virtuais Azure manualmente utilizando o Gestor de Recursos][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Configure um balançador interno Azure para um grupo de disponibilidade AlwaysOn em Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Para obter mais informações sobre o agrupamento SAP HANA DBMS em Azure utilizando o modelo de implementação do Gestor de Recursos Azure, consulte [Alta disponibilidade de SAP HANA em máquinas virtuais Azure (VMs)][sap-hana-ha].
