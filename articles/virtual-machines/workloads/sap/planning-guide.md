---
title: Planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver | Microsoft Docs
description: Planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1b791ac58ada84ac0c2087f266d29bff4bd9c6fe
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224716"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]: https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]: https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure permite que as empresas adquiram recursos de computação e armazenamento em tempo mínimo sem ciclos de aquisição longos. O serviço de máquina virtual do Azure permite que as empresas implantem aplicativos clássicos, como aplicativos baseados em SAP NetWeaver no Azure, e ampliem sua confiabilidade e disponibilidade sem ter mais recursos disponíveis no local. Os serviços de máquina virtual do Azure também dão suporte à conectividade entre locais, o que permite às empresas integrar ativamente máquinas virtuais do Azure em seus domínios locais, em suas nuvens privadas e em seu cenário de sistema SAP.
Este white paper descreve os conceitos básicos de Microsoft Azure máquina virtual e fornece um passo a passo de considerações de planejamento e implementação para instalações do SAP NetWeaver no Azure e, como tal, deve ser o documento a ser lido antes de iniciar o real implantações do SAP NetWeaver no Azure.
O artigo complementa a documentação de instalação do SAP e as notas SAP, que representam os principais recursos para instalações e implantações de software SAP em determinadas plataformas.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Resumo
A computação em nuvem é um termo amplamente usado, que está ganhando mais e mais importância no setor de ti, de pequenas empresas até grandes e multinacionais.

Microsoft Azure é a plataforma de serviços de nuvem da Microsoft, que oferece uma ampla gama de novas possibilidades. Agora, os clientes são capazes de provisionar e desprovisionar rapidamente aplicativos como um serviço na nuvem, para que eles não fiquem limitados a restrições técnicas ou de orçamento. Em vez de investir tempo e orçamento na infraestrutura de hardware, as empresas podem se concentrar no aplicativo, nos processos de negócios e em seus benefícios para clientes e usuários.

Com os Serviços da Máquina Virtual do Microsoft Azure, a Microsoft oferece uma plataforma completa de Infraestrutura como um Serviço (IaaS). As aplicações baseadas em SAP NetWeaver são suportadas em Máquinas Virtuais do Azure (IaaS). Este white paper descreve como planejar e implementar aplicativos baseados no SAP NetWeaver dentro de Microsoft Azure como a plataforma escolhida.

O papel se concentra em dois aspectos principais:

* A primeira parte descreve dois padrões de implantação com suporte para aplicativos baseados no SAP NetWeaver no Azure. Ele também descreve a manipulação geral do Azure com as implantações do SAP em mente.
* A segunda parte detalha a implementação dos dois cenários diferentes descritos na primeira parte.

Para obter recursos adicionais, consulte os [recursos][planning-guide-1.2] do capítulo neste documento.

### <a name="definitions-upfront"></a>Definições antecipadas
Em todo o documento, usamos os seguintes termos:

* IaaS: Infraestrutura como serviço
* PaaS: plataforma como serviço
* SaaS: software como serviço
* Componente SAP: um aplicativo SAP individual, como ECC, BW, Solution Manager ou S/4HANA.  Os componentes SAP podem ser baseados em tecnologias tradicionais de ABAP ou Java ou em um aplicativo não baseado em NetWeaver, como objetos comerciais.
* Ambiente SAP: um ou mais componentes SAP agrupados logicamente para executar uma função de negócios, como desenvolvimento, QAS, treinamento, DR ou produção.
* Cenário SAP: este termo refere-se a todos os ativos do SAP no cenário de ti de um cliente. A estrutura SAP inclui todos os ambientes de produção e de não produção.
* Sistema SAP: a combinação de camada de DBMS e camada de aplicativo de, por exemplo, um sistema de desenvolvimento SAP ERP, SAP BW sistema de teste, sistema de produção SAP CRM, etc. Em implantações do Azure, não há suporte para dividir essas duas camadas entre o local e o Azure. Significa que um sistema SAP é implantado localmente ou implantado no Azure. No entanto, você pode implantar os diferentes sistemas de uma estrutura SAP no Azure ou no local. Por exemplo, você pode implantar os sistemas de desenvolvimento e teste do SAP CRM no Azure, mas o sistema de produção SAP CRM local.
* Entre locais ou híbridos: descreve um cenário em que as VMs são implantadas em uma assinatura do Azure que tem conectividade site a site, multissite ou ExpressRoute entre os datacenters locais e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritos como cenários entre locais ou híbridos. O motivo para a conexão é estender os domínios locais, Active Directory/OpenLDAP locais e o DNS local para o Azure. O cenário local é estendido para os ativos do Azure da assinatura. Tendo essa extensão, as VMs podem fazer parte do domínio local. Os usuários de domínio do domínio local podem acessar os servidores e podem executar serviços nessas VMs (como serviços DBMS). A comunicação e a resolução de nomes entre as VMs implantadas localmente e as VMs implantadas no Azure são possíveis. Esse é o caso mais comum e quase exclusivo da implantação de ativos do SAP no Azure. Para obter mais informações, consulte [este][vpn-gateway-cross-premises-options] artigo e [isso][vpn-gateway-site-to-site-create].
* Extensão de monitoramento do Azure, monitoramento aprimorado e extensão do Azure para SAP: Descreva um e o mesmo item. Ele descreve uma extensão de VM que precisa ser implantada por você para fornecer alguns dados básicos sobre a infraestrutura do Azure para o agente de host do SAP. SAP no SAP Notes pode referir-se a ele como extensão de monitoramento ou monitoramento avançado. No Azure, estamos nos referindo a ele como a **extensão do Azure para SAP**.

> [!NOTE]
> As implantações híbridas ou entre instalações de sistemas SAP em que as máquinas virtuais do Azure que executam sistemas SAP são membros de um domínio local têm suporte para sistemas SAP de produção. Há suporte para configurações entre locais ou híbridas para implantar partes ou cenários SAP completos no Azure. Até mesmo a execução da estrutura SAP completa no Azure exige que essas VMs façam parte do domínio local e anúncios/OpenLDAP. 
>
>



### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Os
O ponto de entrada para a carga de trabalho do SAP na documentação do Azure é encontrado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). A partir desse ponto de entrada, você encontrará muitos artigos que abrangem os tópicos de:

- SAP NetWeaver e Business One no Azure
- Guias do SAP DBMS para vários sistemas DBMS no Azure
- Alta disponibilidade e recuperação de desastre para carga de trabalho do SAP no Azure
- Diretrizes específicas para executar o SAP HANA no Azure
- Diretrizes específicas para o Azure HANA em instâncias grandes para o SAP HANA DBMS 


> [!IMPORTANT]
> Sempre que possível, um link para os guias de instalação do SAP de referência ou outra documentação do SAP é usado (referência InstGuide-01, consulte <http://service.sap.com/instguides>). Quando se trata dos pré-requisitos, do processo de instalação ou dos detalhes de funcionalidades específicas do SAP, a documentação e os guias do SAP devem sempre ser lidos com cuidado, já que os documentos da Microsoft abordam apenas tarefas específicas para o software SAP instalado e operado em um Microsoft Azure máquina virtual.
>
>

As seguintes notas SAP estão relacionadas ao tópico do SAP no Azure:

| Número da nota | Título |
| --- | --- |
| [1928533] |Aplicativos SAP no Azure: produtos e dimensionamento com suporte |
| [2015553] |SAP em Microsoft Azure: pré-requisitos de suporte |
| [1999351] |Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2178632] |Principais métricas de monitoramento para SAP em Microsoft Azure |
| [1409604] |Virtualização no Windows: monitoramento avançado |
| [2191498] |SAP no Linux com o Azure: monitoramento avançado |
| [2243692] |VM do Linux em Microsoft Azure (IaaS): problemas de licença do SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7. x: instalação e atualização |
| [2069760] |Instalação e atualização do SAP Oracle Linux 7. x |
| [1597355] |Recomendação de espaço de permuta para Linux |

Leia também o [wiki do SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contém todas as notas SAP para Linux.

As limitações gerais padrão e as limitações máximas das assinaturas do Azure podem ser encontradas neste [artigo][azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Cenários possíveis
O SAP é geralmente visto como um dos aplicativos mais críticos nas empresas. A arquitetura e as operações desses aplicativos são principalmente complexas e garantir que você atenda aos requisitos de disponibilidade e desempenho é importante.

Portanto, as empresas precisam pensar cuidadosamente em qual provedor de nuvem escolher para executar processos comerciais críticos de negócios. O Azure é a plataforma de nuvem pública ideal para aplicativos SAP e processos de negócios críticos para os negócios. Considerando a ampla variedade de infraestrutura do Azure, quase todos os sistemas SAP NetWeaver e S/4HANA existentes podem ser hospedados no Azure hoje. O Azure fornece VMs com muitos terabytes de memória e mais de 200 CPUs. Além disso, o Azure oferece o [Hana em instâncias grandes](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), que permitem implantações do Hana em escala horizontal de até 24TB e expansão de implantações Ana de até 120 TB. 


Para implantar sistemas SAP com êxito no Azure IaaS ou IaaS em geral, é importante entender as diferenças significativas entre as ofertas de terceirizados tradicionais ou hosters e ofertas de IaaS. Enquanto o hoster ou o terceirizado tradicional adapta a infraestrutura (tipo de servidor, rede e armazenamento) à carga de trabalho que um cliente deseja hospedar, é responsabilidade do cliente ou do parceiro caracterizar a carga de trabalho e escolher o Azure correto componentes de VMs, armazenamento e rede para implantações de IaaS.

Como uma primeira etapa, os clientes precisam verificar os seguintes itens:

* Os tipos de VM com suporte SAP do Azure
* Os produtos/versões com suporte do SAP no Azure
* As versões de so e DBMS com suporte para as versões SAP específicas no Azure
* Produtividade de SAPS fornecida por diferentes SKUs do Azure

As respostas para essas perguntas podem ser lidas no SAP Note [1928533].

Como uma segunda etapa, as limitações de recursos e largura de banda do Azure precisam ser comparadas ao consumo de recursos real de sistemas locais. Portanto, os clientes precisam estar familiarizados com os diferentes recursos dos tipos do Azure com suporte no SAP na área de:

* Recursos de CPU e memória de diferentes tipos de VM e
* Largura de banda de IOPS de diferentes tipos de VM e
* Recursos de rede de diferentes tipos de VM.

A maioria desses dados pode ser encontrada [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

Tenha em mente que os limites listados no link acima são os limites superiores. Isso não significa que os limites para qualquer um dos recursos, por exemplo, IOPS possam ser fornecidos em todas as circunstâncias. No entanto, as exceções são os recursos de CPU e memória de um tipo de VM escolhido. Para os tipos de VM com suporte pelo SAP, os recursos de CPU e memória são reservados e, como tal, disponíveis em qualquer ponto no tempo para consumo dentro da VM.

A plataforma de Microsoft Azure é uma plataforma multilocatário. Como resultado, o armazenamento, a rede e outros recursos são compartilhados entre locatários. A limitação inteligente e a lógica de cota são usadas para impedir que um locatário afete o desempenho de outro locatário (vizinho barulhento) de uma maneira drástica. Especialmente para a certificação da plataforma Azure para SAP HANA, a Microsoft precisa provar o isolamento de recursos para casos em que várias VMs podem ser executadas no mesmo host regularmente para o SAP. Embora a lógica no Azure tente manter as variações de largura de banda experimentadas pequenas, plataformas altamente compartilhadas tendem a introduzir variações maiores na disponibilidade de recursos/largura de banda do que os clientes podem experimentar em suas implantações locais. A probabilidade de que um sistema SAP no Azure possa ter variações maiores do que em um sistema local precisa ser levada em conta.

A última etapa é avaliar os requisitos de disponibilidade. Pode acontecer que a infraestrutura subjacente do Azure precise ser atualizada e exija que os hosts que executam VMs sejam reinicializados. A Microsoft documenta os diferentes casos em [manutenção para máquinas virtuais no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates). Para atenuar os casos raros em que as VMs são forçadas a reinicializar, mas ainda mais importante para os casos em que você precisa para corrigir os componentes do sistema operacional convidado ou do DBMS, você precisa desenvolver conceitos de alta disponibilidade válidos para seus sistemas SAP de produção. Esse requisito não é diferente dos requisitos que você enfrenta no local. A Microsoft está progredindo constantemente com a plataforma Azure para reduzir o tempo de inatividade causado por alterações na plataforma. 

Para implantar com êxito um sistema SAP no Azure, os aplicativos SAP, de banco de dados e de sistema operacional locais devem aparecer na matriz de suporte do SAP Azure, ajustar-se nos recursos que a infraestrutura do Azure pode fornecer e que podem funcionar com os SLAs de disponibilidade Microsoft Azure ofertas. Como esses sistemas são identificados, você precisa decidir em um dos dois cenários de implantação a seguir.





### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Entre instalações-implantação de uma ou várias VMs SAP no Azure com o requisito de integração completa à rede local
![VPN com conectividade site a site (entre instalações)][planning-guide-figure-300]

Esse cenário é um cenário entre instalações com muitos padrões de implantação possíveis. Ele pode ser descrito como executar algumas partes da estrutura SAP local e outras partes da estrutura SAP no Azure. Todos os aspectos do fato de que parte dos componentes SAP estão em execução no Azure devem ser transparentes para os usuários finais. Portanto, o STMS (sistema de correção de transporte) SAP, a comunicação RFC, a impressão, a segurança (como SSO) etc. funcionam de forma direta para os sistemas SAP em execução no Azure. Mas o cenário entre instalações também descreve um cenário em que a estrutura SAP completa é executada no Azure com o domínio do cliente e o DNS estendidos para o Azure.

> [!NOTE]
> Esse é o cenário de implantação, que tem suporte para a execução de sistemas SAP produtivos.
>
>

Leia [Este artigo][vpn-gateway-create-site-to-site-rm-powershell] para obter mais informações sobre como conectar sua rede local a Microsoft Azure

> [!IMPORTANT]
> Quando estamos falando sobre cenários entre instalações entre implantações do Azure e do cliente local, estamos analisando a granularidade dos sistemas SAP inteiros. Cenários que *não têm suporte* para cenários entre instalações são:
>
> * Executando diferentes camadas de aplicativos SAP em diferentes métodos de implantação. Por exemplo, executando a camada de DBMS local, mas a camada de aplicativo SAP em VMs implantadas como VMs do Azure ou vice-versa.
> * Alguns componentes de uma camada SAP no Azure e outros locais. Por exemplo, dividir instâncias da camada de aplicativo SAP entre VMs locais e do Azure.
> * Não há suporte para a distribuição de VMs que executam instâncias SAP de um sistema em várias regiões do Azure.
>
> O motivo dessas restrições é o requisito para uma rede de alto desempenho de baixa latência em um sistema SAP, especialmente entre as instâncias do aplicativo e a camada DBMS de um sistema SAP.
>
> O planejamento especial dos sistemas e regiões deve ocorrer ao usar vários sistemas SAP que são altamente integrados. Certifique-se de implantar esses sistemas o mais próximo possível entre si para minimizar a latência de rede. Exemplos de sistemas SAP altamente integrados são:
> * SAP BW ler dados de sistemas de OLTP SAP como ERP ou CRM ou SRM; or
> * O SAP SLT está sendo usado para replicar dados entre vários sistemas de SPA ou mesmo entre sistemas SAP e não SAP; or
> * SAP S/4 conectado a um sistema SAP ERP; diante.


### <a name="supported-os-and-database-releases"></a>Versões de sistema operacional e banco de dados com suporte
* O software de servidor da Microsoft com suporte para serviços de máquina virtual do Azure está listado neste artigo: <https://support.microsoft.com/kb/2721672>.
* Versões do sistema operacional com suporte, as versões do sistema de banco de dados com suporte nos serviços de máquina virtual do Azure em conjunto com o software SAP estão documentadas na observação SAP [1928533].
* Os aplicativos SAP e as versões com suporte nos serviços de máquina virtual do Azure estão documentados em nota SAP [1928533].
* Somente imagens de 64 bits têm suporte para execução como VMs convidadas no Azure para cenários SAP. Como resultado, há suporte apenas para aplicativos e bancos de dados SAP de 64 bits.

## <a name="microsoft-azure-virtual-machine-services"></a>Serviços de máquina virtual Microsoft Azure
A plataforma Microsoft Azure é uma plataforma de serviços de nuvem em escala da Internet hospedada e operada nos data centers da Microsoft. A plataforma inclui os serviços de máquina virtual Microsoft Azure (infraestrutura como serviço ou IaaS) e um conjunto de recursos avançados de PaaS (plataforma como serviço).

A plataforma Azure reduz a necessidade de compras de tecnologia e de infraestrutura iniciais. Ele simplifica a manutenção e a operação de aplicativos, fornecendo computação sob demanda e armazenamento para hospedar, dimensionar e gerenciar aplicativos Web e conectados. O gerenciamento de infraestrutura é automatizado com uma plataforma projetada para alta disponibilidade e dimensionamento dinâmico para atender às necessidades de uso com a opção de um modelo de preços pré-pago.

![Posicionamento de serviços de máquina virtual Microsoft Azure][planning-guide-figure-400]

Com os serviços de máquina virtual do Azure, a Microsoft está permitindo que você implante imagens de servidor personalizadas no Azure como instâncias de IaaS (consulte a Figura 4). As máquinas virtuais no Azure são baseadas em discos rígidos virtuais (VHD) do Hyper-V e são capazes de executar sistemas operacionais diferentes como sistema operacional convidado.

Do ponto de vista operacional, o serviço de máquina virtual do Azure oferece experiências semelhantes às máquinas virtuais implantadas localmente. No entanto, ele tem a vantagem significativa de que você não precisa adquirir, administrar e gerenciar a infraestrutura. Os desenvolvedores e administradores têm controle total da imagem do sistema operacional nessas máquinas virtuais. Os administradores podem entrar remotamente nessas máquinas virtuais para executar tarefas de manutenção e solução de problemas, bem como tarefas de implantação de software. Em relação à implantação, as únicas restrições são os tamanhos e as funcionalidades das VMs do Azure. Esses tamanhos podem não ser tão detalhados na configuração quanto poderiam ser feitos localmente. Há uma opção de tipos de VM que representam uma combinação de:

* Número de vCPUs
* Memória
* Número de VHDs que podem ser anexados
* Largura de banda de rede e armazenamento

O tamanho e as limitações de vários tamanhos diferentes de máquinas virtuais oferecidos podem ser vistos em uma tabela neste [artigo (Linux)][virtual-machines-sizes-linux] e [neste artigo (Windows)][virtual-machines-sizes-windows].

Nem todas as séries de VM diferentes podem ser oferecidas em cada uma das regiões do Azure (para regiões do Azure, consulte o próximo capítulo). Lembre-se também de que nem todas as VMs ou séries de VM são certificadas para SAP.

> [!IMPORTANT]
> Para o uso de aplicativos baseados no SAP NetWeaver, somente o subconjunto de tipos de VM e configurações listadas na nota SAP [1928533] tem suporte.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiões do Azure
As máquinas virtuais são implantadas para chamadas de *regiões do Azure*. Uma região do Azure pode ser um ou vários data centers que estão localizados em proximidade. Para a maioria das regiões geopolíticas do mundo, a Microsoft tem pelo menos duas regiões do Azure. Por exemplo, na Europa, há uma região do Azure de *Europa setentrional* e uma das *Europa Ocidental*. Essas duas regiões do Azure em uma região geopolítica são separadas por uma distância suficientemente significativa para que desastres naturais ou técnicos não afetem ambas as regiões do Azure na mesma região geopolítica. Como a Microsoft está desenvolvendo constantemente novas regiões do Azure em diferentes regiões geopolíticas globalmente, o número dessas regiões está crescendo constantemente e a partir de Dec 2015, o número de 20 regiões do Azure com regiões adicionais já foi anunciado. Você, como cliente, pode implantar sistemas SAP em todas essas regiões, incluindo as duas regiões do Azure na China. Para obter informações atualizadas atualizadas sobre regiões do Azure, consulte este site: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>O conceito de Microsoft Azure máquina virtual
O Microsoft Azure oferece uma solução de IaaS (infraestrutura como serviço) para hospedar máquinas virtuais com funcionalidades semelhantes a uma solução de virtualização local. Você pode criar máquinas virtuais de dentro do portal do Azure, PowerShell ou CLI, que também oferecem recursos de implantação e gerenciamento.

O Gestor de Recursos do Azure permite utilizar modelos declarativos para aprovisionar as suas aplicações. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Você usa o mesmo modelo para implantar repetidamente seu aplicativo durante cada estágio do ciclo de vida do aplicativo.

Mais informações sobre como usar modelos do Resource Manager podem ser encontradas aqui:

* [Implantar e gerenciar máquinas virtuais usando modelos de Azure Resource Manager e o CLI do Azure](../../linux/create-ssh-secured-vm-from-template.md)
* [Gerenciar máquinas virtuais usando o Azure Resource Manager e o PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Outro recurso interessante é a capacidade de criar imagens de máquinas virtuais, o que permite que você prepare determinados repositórios dos quais é possível implantar rapidamente instâncias de máquinas virtuais, que atendem às suas necessidades.

Mais informações sobre como criar imagens de máquinas virtuais podem ser encontradas neste [artigo (Linux)][virtual-machines-linux-capture-image-resource-manager] e [neste artigo (Windows)][virtual-machines-windows-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domínios de falha
Os domínios de falha representam uma unidade física de falha, bem relacionada à infraestrutura física contida nos data centers e, embora uma folha física ou um rack possa ser considerado um domínio de falha, não há nenhum mapeamento direto de um para um entre os dois.

Ao implantar várias máquinas virtuais como parte de um sistema SAP em Microsoft Azure serviços de máquina virtual, você pode influenciar o controlador de malha do Azure para implantar seu aplicativo em diferentes domínios de falha, atendendo assim aos requisitos do Microsoft Azure SLA. No entanto, a distribuição de domínios de falha em uma unidade de escala do Azure (coleção de centenas de nós de computação ou nós de armazenamento e rede) ou a atribuição de VMs a um domínio de falha específico é algo sobre o qual você não tem controle direto. Para direcionar o controlador de malha do Azure para implantar um conjunto de VMs em diferentes domínios de falha, você precisa atribuir um conjunto de disponibilidade do Azure às VMs no momento da implantação. Para obter mais informações sobre os conjuntos de disponibilidade do Azure, consulte o capítulo [conjuntos de disponibilidade do Azure][planning-guide-3.2.3] neste documento.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Domínios de atualização
Os domínios de atualização representam uma unidade lógica que ajuda a determinar como uma VM em um sistema SAP, que consiste em instâncias SAP em execução em várias VMs, é atualizada. Quando ocorre uma atualização, Microsoft Azure passa pelo processo de atualização desses domínios de atualização um a um. Ao distribuir as VMs no momento da implantação em diferentes domínios de atualização, você pode proteger seu sistema SAP parcialmente de um possível tempo de inatividade. Para forçar o Azure a implantar as VMs de um sistema SAP distribuídos em diferentes domínios de atualização, você precisa definir um atributo específico no momento da implantação de cada VM. Semelhante aos domínios de falha, uma unidade de escala do Azure é dividida em vários domínios de atualização. Para direcionar o controlador de malha do Azure para implantar um conjunto de VMs em diferentes domínios de atualização, você precisa atribuir um conjunto de disponibilidade do Azure às VMs no momento da implantação. Para obter mais informações sobre os conjuntos de disponibilidade do Azure, consulte o capítulo [conjuntos de disponibilidade do Azure][planning-guide-3.2.3] abaixo.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Conjuntos de disponibilidade do Azure
As máquinas virtuais do Azure em um conjunto de disponibilidade do Azure são distribuídas pelo controlador de malha do Azure em diferentes domínios de falha e atualização. A finalidade da distribuição em diferentes domínios de falha e de atualização é impedir que todas as VMs de um sistema SAP sejam desligadas no caso de uma manutenção de infraestrutura ou uma falha em um domínio de falha. Por padrão, as VMs não fazem parte de um conjunto de disponibilidade. A participação de uma VM em um conjunto de disponibilidade é definida no momento da implantação ou posteriormente por uma reconfiguração e reimplantação de uma VM.

Para entender o conceito de conjuntos de disponibilidade do Azure e a maneira como os conjuntos de disponibilidade se relacionam com os domínios de falha e atualização, leia [Este artigo][virtual-machines-manage-availability]

Para definir conjuntos de disponibilidade para Azure Resource Manager por meio de um modelo JSON, consulte [as especificações da API REST](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) e pesquise por "disponibilidade".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Armazenamento: Armazenamento do Microsoft Azure e discos de dados
Máquinas Virtuais do Microsoft Azure utilizar diferentes tipos de armazenamento. Ao implementar o SAP nos serviços de máquina virtual do Azure, é importante entender as diferenças entre esses dois tipos principais de armazenamento:

* Armazenamento volátil não persistente.
* Armazenamento persistente.

As VMs do Azure oferecem discos não persistentes após a implantação de uma VM. No caso de uma reinicialização da VM, todo o conteúdo dessas unidades será apagado. Portanto, é um dado que os arquivos de dados e os arquivos de log/restauração de bancos de dado não devem estar localizados nessas unidades não persistentes. Pode haver exceções para alguns dos bancos de dados, onde essas unidades não persistentes podem ser adequadas para os espaços de tabela tempdb e Temp. No entanto, evite usar essas unidades para VMs de série A, pois essas unidades não persistentes são limitadas na taxa de transferência com essa família de VMs. Para obter mais detalhes, leia o artigo [noções básicas sobre a unidade temporária em VMs do Windows no Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

---
> ![Windows][Logo_Windows] Windows
> 
> D:\ da unidade em uma VM do Azure é uma unidade não persistente, que é apoiada por alguns discos locais no nó de computação do Azure. Como não é persistente, isso significa que todas as alterações feitas no conteúdo no D:\ a unidade é perdida quando a VM é reinicializada. Por "qualquer alteração", como arquivos armazenados, diretórios criados, aplicativos instalados, etc.
> 
> ![Linux][Logo_Linux] Linux
> 
> As VMs do Azure do Linux montam automaticamente uma unidade em/mnt/Resource que é uma unidade não persistente apoiada por discos locais no nó de computação do Azure. Como não é persistente, isso significa que todas as alterações feitas no conteúdo em/mnt/Resource são perdidas quando a VM é reinicializada. Por qualquer alteração, como arquivos armazenados, diretórios criados, aplicativos instalados, etc.
> 
> 

---

O Armazenamento do Microsoft Azure fornece armazenamento persistente e os níveis típicos de proteção e redundância vistos no armazenamento SAN. Discos baseados no armazenamento do Azure são VHDs (disco rígido virtual) localizado nos serviços de armazenamento do Azure. O disco do sistema operacional local (Windows C:\, Linux/dev/sda1) é armazenado no armazenamento do Azure, e volumes/discos adicionais montados na VM também são armazenados lá.

É possível carregar um VHD existente do local ou criar vazios de dentro do Azure e anexá-los às VMs implantadas.

Depois de criar ou carregar um VHD no armazenamento do Azure, é possível montar e anexá-los a uma máquina virtual existente e copiar o VHD existente (desmontado).

Como esses VHDs são persistidos, os dados e as alterações dentro deles são seguros ao reinicializar e recriar uma instância de máquina virtual. Mesmo que uma instância seja excluída, esses VHDs permanecem seguros e podem ser reimplantados ou, no caso de discos que não sejam do sistema operacional, podem ser montados em outras VMs.

Mais informações sobre o armazenamento do Azure podem ser encontradas aqui:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Armazenamento standard do Azure
O armazenamento standard do Azure era o tipo de armazenamento disponível quando o Azure IaaS foi lançado. Havia cotas de IOPS impostas por um único disco. A latência realizada não estava na mesma classe que os dispositivos SAN/NAS normalmente implantados para sistemas SAP de alto nível hospedados localmente. No entanto, o armazenamento standard do Azure provou suficiente para vários sistemas SAP de centenas, enquanto implantado no Azure.

Os discos armazenados nas contas de armazenamento standard do Azure são cobrados com base nos dados reais armazenados, no volume de transações de armazenamento, transferências de dados de saída e opção de redundância escolhida. Muitos discos podem ser criados com o tamanho máximo de 1 TB, mas desde que eles permaneçam vazios, não há nenhum encargo. Se, em seguida, você preencher um VHD com 100 GB, será cobrado pelo armazenamento de 100 GB e não pelo tamanho nominal com o qual o VHD foi criado.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Armazenamento Premium do Azure
O armazenamento Premium do Azure foi introduzido com o objetivo de fornecer:

* Melhor latência de e/s.
* Melhor taxa de transferência.
* Menos variabilidade na latência de e/s.

Para essa finalidade, muitas alterações foram introduzidas das duas mais significativas:

* Uso de discos SSD nos nós de armazenamento do Azure
* Um novo cache de leitura que é apoiado pelo SSD local de um nó de computação do Azure

Ao contrário do armazenamento padrão em que as funcionalidades não mudaram de acordo com o tamanho do disco (ou VHD), o armazenamento Premium atualmente tem três categorias de disco diferentes, que são mostradas neste artigo: <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Você verá que o disco de IOPS/taxa de transferência de disco e disco depende da categoria de tamanho dos discos

A base de custo no caso do armazenamento Premium não é o volume de dados real armazenado nesses discos, mas a categoria de tamanho de um disco, independentemente da quantidade de dados armazenados no disco.

Você também pode criar discos no armazenamento Premium que não estão mapeando diretamente para as categorias de tamanho mostradas. Esse pode ser o caso, especialmente ao copiar discos do armazenamento Standard para o armazenamento Premium. Nesses casos, um mapeamento para a próxima opção maior de disco de armazenamento Premium é executado.

A maioria das famílias de VMs do Azure certificadas com SAP é capaz de trabalhar com o armazenamento Premium e uma mistura entre o armazenamento Standard e Premium do Azure.

Se você estiver verificando a parte das VMs da série DS neste [artigo (Linux)][virtual-machines-sizes-linux] e [neste artigo (Windows)][virtual-machines-sizes-windows], perceberá que há limitações de volume de dados para discos de armazenamento Premium na GRANULARIDADE do nível da VM. VMs diferentes da série DS ou da série GS também têm limitações diferentes em relação ao número de discos de dados que podem ser montados. Esses limites também estão documentados no artigo mencionado acima. Mas, em essência, isso significa que, se você, por exemplo, montar 32 x p30 discos para uma única VM DS14, não poderá obter 32 x a taxa de transferência máxima de um disco p30. Em vez disso, a taxa de transferência máxima no nível de VM, conforme documentado no artigo, limita a produtividade dos dados.

Mais informações sobre o armazenamento Premium podem ser encontradas aqui: <https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Contas de Armazenamento do Azure

Ao implantar serviços ou VMs no Azure, a implantação de VHDs e imagens de VM pode ser organizada em unidades chamadas contas de armazenamento do Azure. Ao planejar uma implantação do Azure, você precisa considerar cuidadosamente as restrições do Azure. Por um lado, há um número limitado de contas de armazenamento por assinatura do Azure. Embora cada conta de armazenamento do Azure possa conter um grande número de arquivos VHD, há um limite fixo no total de IOPS por conta de armazenamento. Ao implantar centenas de VMs SAP com sistemas DBMS que criam chamadas de e/s significativas, é recomendável distribuir VMs de alto IOPS do DBMS entre várias contas de armazenamento do Azure. É preciso tomar cuidado para não exceder o limite atual de contas de armazenamento do Azure por assinatura. Como o armazenamento é uma parte vital da implantação de banco de dados para um sistema SAP, esse conceito é discutido em mais detalhes no [Guia de implantação do DBMS][dbms-guide]já referenciado.

Mais informações sobre as contas de armazenamento do Azure podem ser encontradas neste [artigo][storage-scalability-targets]. Ao ler este artigo, você percebe que há diferenças nas limitações entre contas de armazenamento standard do Azure e contas de armazenamento Premium. As principais diferenças são o volume de dados que podem ser armazenados nessa conta de armazenamento. No armazenamento Standard, o volume é uma magnitude maior do que com o armazenamento Premium. Por outro lado, a conta de armazenamento Standard é severamente limitada em IOPS (consulte a **taxa de solicitação total**de coluna), enquanto a conta de armazenamento Premium do Azure não tem essa limitação. Abordaremos os detalhes e os resultados dessas diferenças ao discutir as implantações de sistemas SAP, especialmente os servidores DBMS.

Em uma conta de armazenamento, você tem a possibilidade de criar contêineres diferentes com a finalidade de organizar e categorizar VHDs diferentes. Esses contêineres são usados para, por exemplo, VHDs separados de VMs diferentes. Não há nenhuma implicações de desempenho no uso de apenas um contêiner ou de vários contêineres abaixo de uma única conta de armazenamento do Azure.

No Azure, um nome VHD segue a seguinte conexão de nomenclatura que precisa fornecer um nome exclusivo para o VHD no Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

A cadeia de caracteres acima precisa identificar exclusivamente o VHD que está armazenado no armazenamento do Azure.

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Managed Disks

Managed Disks são um novo tipo de recurso no Azure Resource Manager que pode ser usado em vez de VHDs que são armazenados em contas de armazenamento do Azure. Managed Disks alinhar automaticamente com o conjunto de disponibilidade da máquina virtual à qual eles estão conectados e, portanto, aumentar a disponibilidade de sua máquina virtual e os serviços em execução na máquina virtual. Para obter mais informações, leia o [artigo Visão geral](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

É recomendável usar o disco gerenciado, pois eles simplificam a implantação e o gerenciamento de suas máquinas virtuais.
Atualmente, o SAP dá suporte apenas a Managed Disks Premium. Para obter mais informações, leia a observação do SAP [1928533].

#### <a name="microsoft-azure-storage-resiliency"></a>Resiliência Armazenamento do Microsoft Azure

O Armazenamento do Microsoft Azure armazena o VHD de base (com o sistema operacional) e OS discos anexados ou BLOBs em pelo menos três nós de armazenamento separados. Esse fato é chamado de LRS (armazenamento redundante local). LRS é o padrão para todos os tipos de armazenamento no Azure. 

Há vários outros métodos de redundância, que são descritos no artigo [replicação de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>A partir do armazenamento Premium do Azure, que é o tipo recomendado de armazenamento para VMs DBMS e discos que armazenam arquivos de banco de dados e log/redo, o único método disponível é LRS. Como resultado, você precisa configurar métodos de banco de dados, como SQL Server Always On, a replicação de sistema do Oracle Data Guard ou HANA para habilitar a replicação de dados em outra região do Azure ou outra zona de disponibilidade do Azure.


> [!NOTE]
> Para implantações de DBMS, o uso de armazenamento com redundância geográfica como disponível com o armazenamento standard do Azure não é recomendado, pois ele tem um impacto grave no desempenho e não respeita a ordem de gravação em diferentes VHDs anexados a uma VM. O fato de não respeitar a ordem de gravação em diferentes VHDs tem um alto potencial de terminar em bancos de dados inconsistentes no lado do destino de replicação se os arquivos de banco de dados e de log/restauração forem distribuídos entre vários VHDs (como o caso) no lado da VM de origem.


### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure rede

O Microsoft Azure fornece uma infraestrutura de rede, que permite o mapeamento de todos os cenários, que queremos perceber com o software SAP. Os recursos são:

* Acesso de fora, diretamente às VMs por meio dos serviços de terminal do Windows ou SSH/VNC
* Acesso a serviços e portas específicas usadas por aplicativos nas VMs
* Comunicação interna e resolução de nomes entre um grupo de VMs implantadas como VMs do Azure
* Conectividade entre instalações entre a rede local de um cliente e a rede do Azure
* Conectividade entre a região do Azure ou data center entre os sites do Azure

Pode encontrar mais informações aqui: <https://azure.microsoft.com/documentation/services/virtual-network/>

Há muitas possibilidades diferentes para configurar o nome e a resolução de IP no Azure. Também há um serviço DNS do Azure, que pode ser usado em vez de configurar seu próprio servidor DNS. Mais informações podem ser encontradas neste [artigo][virtual-networks-manage-dns-in-vnet] e nesta [página](https://azure.microsoft.com/services/dns/).

Para cenários entre locais ou híbridos, estamos contando com o fato de que o AD/OpenLDAP/DNS local foi estendido via VPN ou conexão privada ao Azure. Para determinados cenários, conforme documentado aqui, pode ser necessário ter uma réplica do AD/OpenLDAP instalada no Azure.

Como a resolução de nomes e de rede é uma parte vital da implantação de banco de dados para um sistema SAP, esse conceito é discutido em mais detalhes no [Guia de implantação do DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Redes Virtuais do Azure

Ao criar uma rede virtual do Azure, você pode definir o intervalo de endereços dos endereços IP privados alocados pela funcionalidade DHCP do Azure. Em cenários entre instalações, o intervalo de endereços IP definido ainda é alocado usando DHCP pelo Azure. No entanto, a resolução de nomes de domínio é feita localmente (pressupondo que as VMs façam parte de um domínio local) e, portanto, podem resolver endereços além dos diferentes serviços de nuvem do Azure.

Cada máquina virtual no Azure precisa estar conectada a uma rede virtual.

Mais detalhes podem ser encontrados neste [artigo][resource-groups-networking] e nesta [página](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Por padrão, depois que uma VM é implantada, você não pode alterar a configuração de rede virtual. As configurações de TCP/IP devem ser deixadas para o servidor DHCP do Azure. O comportamento padrão é atribuição de IP dinâmico.
>
>

O endereço MAC da placa de rede virtual pode mudar, por exemplo, após o redimensionamento, e o SO convidado Windows ou Linux escolhe a nova placa de rede e usa automaticamente o DHCP para atribuir os endereços IP e DNS nesse caso.

##### <a name="static-ip-assignment"></a>Atribuição de IP estático
É possível atribuir endereços IP fixos ou reservados a VMs em uma rede virtual do Azure. A execução das VMs em uma rede virtual do Azure abre uma grande possibilidade de aproveitar essa funcionalidade, se necessário, ou necessária para alguns cenários. A atribuição de IP permanece válida durante toda a existência da VM, independentemente de a VM estar em execução ou desligada. Como resultado, você precisa levar em conta o número total de VMs (VMs em execução e interrompidas) ao definir o intervalo de endereços IP para a rede virtual. O endereço IP permanece atribuído até que a VM e sua interface de rede sejam excluídas ou até que o endereço IP seja desatribuído novamente. Para obter mais informações, leia [Este artigo][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Você deve atribuir endereços IP estáticos por meio do Azure significa para vNICs individuais. Você não deve atribuir endereços IP estáticos dentro do sistema operacional convidado a um vNIC. Alguns serviços do Azure, como o serviço de backup do Azure, dependem do fato de que pelo menos o vNIC primário está definido como DHCP e não para endereços IP estáticos. Consulte também o documento [solucionar problemas de backup de máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Várias NICs por VM

Você pode definir várias vNIC (placas de interface de rede virtual) para uma máquina virtual do Azure. Com a capacidade de ter várias vNICs, você pode começar a configurar a separação de tráfego de rede, em que, por exemplo, o tráfego do cliente é roteado por meio de um vNIC e o tráfego de back-end é roteado por um segundo vNIC. Dependendo do tipo de VM, há diferentes limitações em relação ao número de vNICs. Detalhes, funcionalidades e restrições exatos podem ser encontrados nestes artigos:

* [Criar uma VM do Windows com várias NICs][virtual-networks-multiple-nics-windows]
* [Criar uma VM do Linux com várias NICs][virtual-networks-multiple-nics-linux]
* [Implantar VMs com várias NICs usando um modelo][virtual-network-deploy-multinic-arm-template]
* [Implantar VMs com várias NICs usando o PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Implantar VMs com várias NICs usando o CLI do Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Conectividade site a site

Entre instalações são VMs do Azure e locais vinculadas a uma conexão VPN transparente e permanente. Espera-se que se torne o padrão de implantação do SAP mais comum no Azure. A suposição é que os procedimentos operacionais e processos com instâncias SAP no Azure funcionem de forma transparente. Isso significa que você deve ser capaz de imprimir fora desses sistemas, bem como usar o sistema de gerenciamento de transporte SAP (TMS) para transportar alterações de um sistema de desenvolvimento no Azure para um sistema de teste, que é implantado localmente. Mais documentação sobre o site a site pode ser encontrada neste [artigo][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Dispositivo de túnel VPN

Para criar uma conexão site a site (data center local para o Azure data center), você precisa obter e configurar um dispositivo VPN, ou usar o RRAS (serviço de roteamento e acesso remoto) que foi introduzido como um componente de software com o Windows Server 2012.

* [Criar uma rede virtual com uma conexão VPN site a site usando o PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Acerca dos dispositivos de VPN para ligações de Gateway de Rede de VPNs][vpn-gateway-about-vpn-devices]
* [FAQ do Gateway de VPN][vpn-gateway-vpn-faq]

![Conexão site a site entre o local e o Azure][planning-guide-figure-600]

A figura acima mostra que duas assinaturas do Azure têm subintervalos de endereços IP reservados para uso em redes virtuais no Azure. A conectividade da rede local para o Azure é estabelecida por meio de VPN.

#### <a name="point-to-site-vpn"></a>VPN ponto a Site

A VPN ponto a site requer que cada computador cliente se conecte com sua própria VPN no Azure. Para cenários SAP, estamos vendo, a conectividade ponto a site não é prática. Portanto, nenhuma referência adicional é dada à conectividade VPN ponto a site.

Mais informações podem ser encontradas aqui
* [Configurar uma ligação de Ponto a Site a uma VNet com o portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Configurar uma ligação de Ponto a Site a uma VNet com o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>VPN de vários sites

O Azure também tem a possibilidade de criar conectividade VPN de vários sites para uma assinatura do Azure. Anteriormente, uma única assinatura era limitada a uma conexão VPN site a site. Essa limitação ficou ausente com conexões VPN multissite para uma única assinatura. Isso possibilita o aproveitamento de mais de uma região do Azure para uma assinatura específica por meio de configurações entre locais.

Para obter mais documentação, consulte [Este artigo][vpn-gateway-create-site-to-site-rm-powershell]

#### <a name="vnet-to-vnet-connection"></a>Conexão VNet com VNet

Usando a VPN de vários sites, você precisa configurar um Entrada na Rede virtual do Azure separado em cada uma das regiões. No entanto, geralmente você tem o requisito de que os componentes de software em diferentes regiões devem se comunicar entre si. Idealmente, essa comunicação não deve ser roteada de uma região do Azure para o local e de lá para a outra região do Azure. Para o atalho, o Azure oferece a possibilidade de configurar uma conexão de um Entrada na Rede virtual do Azure uma região para outra rede virtual do Azure hospedada em outra região. Essa funcionalidade é chamada de conexão VNet a VNet. Mais detalhes sobre essa funcionalidade podem ser encontrados aqui: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Conexão privada com o Azure ExpressRoute

Microsoft Azure ExpressRoute permite a criação de conexões privadas entre data centers do Azure e a infraestrutura local do cliente ou em um ambiente de colocalização. O ExpressRoute é oferecido por vários provedores de VPN de MPLS (pacotes comutados) ou outros provedores de serviços de rede. As ligações do ExpressRoute não passam para a Internet pública. As conexões do ExpressRoute oferecem maior segurança, mais confiabilidade por meio de vários circuitos paralelos, velocidades mais rápidas e latências menores do que conexões típicas pela Internet.

Encontre mais detalhes sobre o Azure ExpressRoute e ofertas aqui:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

O Express Route permite várias assinaturas do Azure por meio de um circuito do ExpressRoute, conforme documentado aqui

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Túnel forçado no caso de entre instalações
Para VMs que ingressam em domínios locais por meio de site a site, ponto a site ou ExpressRoute, você precisa certificar-se de que as configurações de proxy da Internet estejam sendo implantadas para todos os usuários nessas VMs também. Por padrão, o software em execução nessas VMs ou usuários que usam um navegador para acessar a Internet não passaria pelo proxy da empresa, mas se conectaria diretamente ao Azure à Internet. Mas até mesmo a configuração de proxy não é uma solução de 100% para direcionar o tráfego por meio do proxy da empresa, pois é responsabilidade de software e serviços verificar o proxy. Se o software em execução na VM não estiver fazendo isso ou um administrador manipular as configurações, o tráfego para a Internet poderá ser desviado novamente diretamente por meio do Azure para a Internet.

Para evitar essa conectividade direta com a Internet, você pode configurar o túnel forçado com conectividade site a site entre o local e o Azure. A descrição detalhada do recurso de túnel forçado é publicada aqui <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

O túnel forçado com o ExpressRoute é habilitado por clientes que anunciam uma rota padrão por meio das sessões de emparelhamento BGP do ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Resumo da rede do Azure

Este capítulo continha muitos pontos importantes sobre a rede do Azure. Aqui está um resumo dos principais pontos:

* As redes virtuais do Azure permitem colocar uma estrutura de rede em sua implantação do Azure. O VNets pode ser isolado entre si ou com a ajuda do tráfego de grupos de segurança de rede entre VNets pode ser controlado.
* As redes virtuais do Azure podem ser aproveitadas para atribuir intervalos de endereços IP a VMs ou atribuir endereços IP fixos a VMs
* Para configurar uma conexão site a site ou ponto a site, você precisa criar uma rede virtual do Azure primeiro
* Depois que uma máquina virtual tiver sido implantada, não será mais possível alterar a rede virtual atribuída à VM

### <a name="quotas-in-azure-virtual-machine-services"></a>Cotas nos serviços de máquina virtual do Azure
Precisamos estar claros sobre o fato de que a infraestrutura de armazenamento e rede é compartilhada entre VMs que executam uma variedade de serviços na infraestrutura do Azure. E assim como nos data centers do próprio cliente, o excesso de provisionamento de alguns recursos de infraestrutura ocorre em um certo grau. A plataforma de Microsoft Azure usa cotas de disco, CPU, rede e outras para limitar o consumo de recursos e preservar o desempenho consistente e determinístico.  Os diferentes tipos de VM (A5, A6, etc.) têm cotas diferentes para o número de discos, CPU, RAM e rede.

> [!NOTE]
> Os recursos de CPU e memória dos tipos de VM com suporte pelo SAP são alocados previamente nos nós de host. Isso significa que, depois que a VM for implantada, os recursos no host estarão disponíveis conforme definido pelo tipo de VM.
>
>

Ao planejar e dimensionar o SAP em soluções do Azure, as cotas para cada tamanho de máquina virtual devem ser consideradas. As cotas de VM são descritas [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

As cotas descritas representam os valores máximos teóricos.  O limite de IOPS por disco pode ser alcançado com pequeno IOs (8 KB), mas possivelmente pode não ser alcançado com IOs grande (1Mb).  O limite de IOPS é imposto na granularidade de um único disco.

Como uma árvore de decisão aproximada para decidir se um sistema SAP se encaixa nos serviços de máquina virtual do Azure e em seus recursos ou se um sistema existente precisa ser configurado de forma diferente para implantar o sistema no Azure, a árvore de decisão abaixo pode ser usada:

![Árvore de decisão para decidir a capacidade de implantar o SAP no Azure][planning-guide-figure-700]

**Etapa 1**: as informações mais importantes para começar são o requisito de SAPs para um determinado sistema SAP. Os requisitos de SAPS precisam ser separados na parte do DBMS e na parte do aplicativo SAP, mesmo que o sistema SAP já esteja implantado localmente em uma configuração de duas camadas. Para sistemas existentes, os SAPS relacionados ao hardware em uso muitas vezes podem ser determinados ou estimados com base em benchmarks SAP existentes. Os resultados podem ser encontrados aqui: <https://sap.com/about/benchmark.html>.
Para sistemas SAP implantados recentemente, você deve ter passado por um exercício de dimensionamento, que deve determinar os requisitos de SAPS do sistema.
Consulte também este blog e documento anexado para dimensionamento do SAP no Azure: <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Etapa 2**: para sistemas existentes, o volume de e/s e as operações de e/s por segundo no servidor DBMS devem ser medidos. Para sistemas planejados recentemente, o exercício de dimensionamento para o novo sistema também deve fornecer ideias aproximadas dos requisitos de e/s no lado do DBMS. Se não tiver certeza, você precisará, eventualmente, realizar uma prova de conceito.

**Etapa 3**: comparar o requisito de SAPs para o servidor DBMS com os SAPs que os diferentes tipos de VM do Azure podem fornecer. As informações sobre SAPS dos diferentes tipos de VM do Azure estão documentadas em nota SAP [1928533]. O foco deve estar na VM do DBMS primeiro, pois a camada do banco de dados é a camada em um sistema SAP NetWeaver que não se expande horizontalmente na maioria das implantações. Por outro lado, a camada de aplicativo SAP pode ser expandida. Se nenhum dos tipos de VM do Azure com suporte do SAP puder fornecer os SAPS necessários, a carga de trabalho do sistema SAP planejado não poderá ser executada no Azure. Você precisa implantar o sistema no local ou precisa alterar o volume de carga de trabalho do sistema.

**Etapa 4**: conforme documentado [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows], o Azure impõe uma cota de IOPS por disco independente, independentemente de você usar o armazenamento Standard ou o armazenamento Premium. Dependendo do tipo de VM, o número de discos de dados, que pode ser montado, varia. Como resultado, você pode calcular um número de IOPS máximo que pode ser obtido com cada um dos diferentes tipos de VM. Dependendo do layout do arquivo de banco de dados, você pode distribuir discos para se tornar um volume no sistema operacional convidado. No entanto, se o volume de IOPS atual de um sistema SAP implantado exceder os limites calculados do maior tipo de VM do Azure e se não houver nenhuma chance de compensar mais memória, a carga de trabalho do sistema SAP poderá ser seriamente afetada. Nesses casos, você pode atingir um ponto em que não deve implantar o sistema no Azure.

**Etapa 5**: especialmente em sistemas SAP, que são implantados localmente em configurações de duas camadas, as chances são de que o sistema talvez precise ser configurado no Azure em uma configuração de três camadas. Nesta etapa, você precisa verificar se há um componente na camada de aplicativo SAP, que não pode ser escalado horizontalmente e que não se ajustaria aos recursos de CPU e memória que os diferentes tipos de VM do Azure oferecem. Se realmente houver um componente desse tipo, o sistema SAP e sua carga de trabalho não poderão ser implantados no Azure. Mas se você puder escalar horizontalmente os componentes do aplicativo SAP em várias VMs do Azure, o sistema poderá ser implantado no Azure.

**Etapa 6**: se os componentes de camada de aplicativo do DBMS e do SAP puderem ser executados em VMs do Azure, a configuração precisará ser definida em relação a:

* Número de VMs do Azure
* Tipos de VM para os componentes individuais
* Número de VHDs na VM do DBMS para fornecer IOPS suficiente

## <a name="managing-azure-assets"></a>Gerenciando ativos do Azure

### <a name="azure-portal"></a>Portal do Azure

O portal do Azure é uma das três interfaces para gerenciar implantações de VM do Azure. As tarefas básicas de gerenciamento, como implantar VMs de imagens, podem ser feitas por meio do portal do Azure. Além disso, a criação de contas de armazenamento, redes virtuais e outros componentes do Azure também são tarefas que o portal do Azure pode lidar bem. No entanto, a funcionalidade como carregar VHDs do local para o Azure ou copiar um VHD no Azure são tarefas, que exigem ferramentas de terceiros ou administração por meio do PowerShell ou da CLI.

![Portal do Microsoft Azure-visão geral da máquina virtual][planning-guide-figure-800]


As tarefas de administração e configuração para a instância de máquina virtual são possíveis no portal do Azure.

Além de reiniciar e desligar uma máquina virtual, você também pode anexar, desanexar e criar discos de dados para a instância de máquina virtual, capturar a instância para preparação de imagem e configurar o tamanho da instância de máquina virtual.

O portal do Azure fornece a funcionalidade básica para implantar e configurar VMs e muitos outros serviços do Azure. No entanto, nem toda a funcionalidade disponível é coberta pelo portal do Azure. No portal do Azure, não é possível executar tarefas como:

* Carregando VHDs para o Azure
* Copiando VMs


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Gerenciamento por meio de cmdlets Microsoft Azure PowerShell

O Windows PowerShell é uma estrutura avançada e extensível que foi amplamente adotada por clientes que implantam um número maior de sistemas no Azure. Após a instalação de cmdlets do PowerShell em um desktop, laptop ou estação de gerenciamento dedicada, os cmdlets do PowerShell podem ser executados remotamente.

O processo para habilitar um desktop/laptop local para o uso de Azure PowerShell cmdlets e como configurá-los para o uso com as assinaturas do Azure é descrito neste [artigo][powershell-install-configure].

Etapas mais detalhadas sobre como instalar, atualizar e configurar os cmdlets de Azure PowerShell também podem ser encontradas neste [capítulo do guia de implantação][deployment-guide-4.1].

A experiência do cliente até o momento é que o PowerShell (PS) é certamente a ferramenta mais poderosa para implantar VMs e criar etapas personalizadas na implantação de VMs. Todos os clientes que executam instâncias SAP no Azure estão usando cmdlets do PS para complementar as tarefas de gerenciamento que eles fazem no portal do Azure ou até mesmo usando cmdlets do PS exclusivamente para gerenciar suas implantações no Azure. Como os cmdlets específicos do Azure compartilham a mesma convenção de nomenclatura que os mais de 2000 cmdlets relacionados ao Windows, é uma tarefa fácil para os administradores do Windows utilizarem esses cmdlets.

Consulte o exemplo aqui: <https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


A implantação da extensão do Azure para SAP (consulte o capítulo [extensão do Azure para SAP][planning-guide-9.1] neste documento) só é possível por meio do PowerShell ou da CLI. Portanto, é obrigatório configurar e configurar o PowerShell ou a CLI ao implantar ou administrar um sistema SAP NetWeaver no Azure.  

Como o Azure fornece mais funcionalidade, novos cmdlets do PS serão adicionados que exigem uma atualização dos cmdlets. Portanto, faz sentido verificar o site de download do Azure pelo menos uma vez ao mês <https://azure.microsoft.com/downloads/> para obter uma nova versão dos cmdlets. A nova versão é instalada em cima da versão mais antiga.

Para obter uma lista geral de comandos do PowerShell relacionados ao Azure, verifique aqui: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Gerenciamento por meio de comandos da CLI Microsoft Azure

Para clientes que usam o Linux e que desejam gerenciar recursos do Azure, o PowerShell pode não ser uma opção. A Microsoft oferece CLI do Azure como uma alternativa.
O CLI do Azure fornece um conjunto de comandos de plataforma cruzada de software livre para trabalhar com a plataforma Azure. O CLI do Azure fornece grande parte da mesma funcionalidade encontrada no portal do Azure.

Para obter informações sobre instalação, configuração e como usar comandos da CLI para realizar tarefas do Azure, consulte

* [Instalar a CLI clássica do Azure][xplat-cli]
* [Implementar e gerir máquinas virtuais utilizando modelos Azure Resource Manager e a CLI do Azure] [../../linux/create-ssh-secured-vm-from-template.md]
* [Use a CLI clássica do Azure para Mac, Linux e Windows com Azure Resource Manager][xplat-cli-azure-resource-manager]

Leia também o capítulo [CLI do Azure para VMs do Linux][deployment-guide-4.5.2] no [Guia de implantação][planning-guide] sobre como usar CLI do Azure para implantar a extensão do Azure para SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Diferentes maneiras de implantar VMs para SAP no Azure

Neste capítulo, você aprende as diferentes maneiras de implantar uma VM no Azure. Os procedimentos de preparação adicionais, bem como a manipulação de VHDs e VMs no Azure, são abordados neste capítulo.

### <a name="deployment-of-vms-for-sap"></a>Implantação de VMs para SAP

O Microsoft Azure oferece várias maneiras de implantar VMs e discos associados. Portanto, é importante entender as diferenças, já que as preparações das VMs podem diferir dependendo do método de implantação. Em geral, vamos examinar os seguintes cenários:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Movendo uma VM do local para o Azure com um disco não generalizado

Você planeja mover um sistema SAP específico do local para o Azure. Isso pode ser feito carregando o VHD, que contém o sistema operacional, os binários do SAP e os binários do DBMS, além dos VHDs com os arquivos de dados e de log do DBMS para o Azure. Em contraste com o [cenário #2 abaixo][planning-guide-5.1.2], você mantém o nome do host, o SID do SAP e as contas de usuário SAP na VM do Azure, pois eles foram configurados no ambiente local. Portanto, generalizar a imagem não é necessária. Confira os capítulos [preparação para mover uma VM do local para o Azure com um disco não generalizado][planning-guide-5.2.1] deste documento para obter as etapas de preparação local e o carregamento de VMs ou VHDs não generalizados para o Azure. Leia [o capítulo cenário 3: movendo uma VM do local usando um VHD do Azure não generalizado com o SAP][deployment-guide-3.4] no [Guia de implantação][deployment-guide] para obter etapas detalhadas de implantação de uma imagem desse tipo no Azure.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Implantando uma VM com uma imagem específica do cliente

Devido aos requisitos de patch específicos de sua versão do so ou do DBMS, as imagens fornecidas no Azure Marketplace podem não atender às suas necessidades. Portanto, talvez seja necessário criar uma VM usando sua própria imagem privada de VM do so/DBMS, que pode ser implantada várias vezes depois. Para preparar essa imagem privada para duplicação, os seguintes itens devem ser considerados:

---
> ![Windows][Logo_Windows] Windows
>
> Veja mais detalhes aqui: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> as configurações do Windows (como o nome de host e SID do Windows) devem ser abstratas/generalizadas na VM local por meio do comando Sysprep.
>
>
> ![Linux][Logo_Linux] Linux
>
> Siga as etapas descritas nestes artigos para [SuSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd]ou [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle], para preparar um VHD a ser carregado no Azure.
>
>

---
Se você já tiver instalado o conteúdo do SAP em sua VM local (especialmente para sistemas de duas camadas), poderá adaptar as configurações do sistema SAP após a implantação da VM do Azure por meio do procedimento de renomeação de instância com suporte do Gerenciador de provisionamento de software SAP (SAP Note [1619720]). Confira os capítulos [preparação para implantar uma VM com uma imagem específica do cliente para SAP][planning-guide-5.2.2] e [carregando um VHD do local para o Azure][planning-guide-5.3.2] deste documento para obter as etapas de preparação local e o carregamento de uma VM generalizada para o Azure. Leia [o capítulo cenário 2: Implantando uma VM com uma imagem personalizada para SAP][deployment-guide-3.3] no [Guia de implantação][deployment-guide] para obter etapas detalhadas de implantação desse tipo de imagem no Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Implantando uma VM fora do Azure Marketplace

Você gostaria de usar uma imagem de VM fornecida pela Microsoft ou por terceiros do Azure Marketplace para implantar sua VM. Depois de implantar sua VM no Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP e/ou DBMS dentro de sua VM, como você faria em um ambiente local. Para obter uma descrição de implantação mais detalhada, consulte [o capítulo cenário 1: Implantando uma VM do Azure Marketplace para SAP][deployment-guide-3.2] no [Guia de implantação][deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Preparando VMs com SAP para o Azure

Antes de carregar as VMs no Azure, você precisa verificar se as VMs e os VHDs atendem a determinados requisitos. Há pequenas diferenças, dependendo do método de implantação usado.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Preparação para mover uma VM do local para o Azure com um disco não generalizado

Um método de implantação comum é mover uma VM existente, que executa um sistema SAP do local para o Azure. Essa VM e o sistema SAP na VM devem apenas ser executados no Azure usando o mesmo nome de host e provavelmente o mesmo SID do SAP. Nesse caso, o sistema operacional convidado da VM não deve ser generalizado para várias implantações. Se a rede local foi estendida para o Azure (Confira o capítulo [entre instalações-implantação de uma ou várias VMs SAP no Azure com o requisito de integração completa à rede local][planning-guide-2.2] neste documento), até mesmo as mesmas contas de domínio podem ser usadas na VM, já que elas foram usadas antes do local.

Os requisitos ao preparar seu próprio disco de VM do Azure são:

* Originalmente, o VHD que contém o sistema operacional poderia ter apenas um tamanho máximo de 127 GB. Essa limitação foi eliminada no final de março de 2015. Agora, o VHD que contém o sistema operacional pode ter até 1 TB de tamanho como qualquer outro VHD hospedado do armazenamento do Azure também.
* Ele precisa estar no formato de VHD fixo. VHDs dinâmicos ou VHDs no formato VHDx ainda não têm suporte no Azure. VHDs dinâmicos serão convertidos em VHDs estáticos quando você carregar o VHD com o PowerShell commandlets ou a CLI
* Os VHDs, que são montados na VM e devem ser montados novamente no Azure para a VM precisam estar em um formato VHD fixo também. Leia [Este artigo (Linux)](../../linux/managed-disks-overview.md) e [Este artigo (Windows)](../../windows/managed-disks-overview.md)) para obter os limites de tamanho dos discos de dados. VHDs dinâmicos serão convertidos em VHDs estáticos quando você carregar o VHD com o PowerShell commandlets ou a CLI
* Adicione outra conta local com privilégios de administrador, que pode ser usada pelo suporte da Microsoft ou que pode ser atribuída como contexto para que serviços e aplicativos sejam executados até que a VM seja implantada e mais usuários apropriados possam ser usados.
* Adicione outras contas locais, pois elas podem ser necessárias para o cenário de implantação específico.

---
> ![Windows][Logo_Windows] Windows
>
> Nesse cenário, nenhuma generalização (Sysprep) da VM é necessária para carregar e implantar a VM no Azure.
> Verifique se a unidade D:\ Não é usado.
> Defina a montagem automática de disco para discos anexados, conforme descrito no capítulo [Configurando a montagem automática para discos anexados][planning-guide-5.5.3] neste documento.
>
> ![Linux][Logo_Linux] Linux
>
> Nesse cenário, nenhuma generalização (waagent-deprovision) da VM é necessária para carregar e implantar a VM no Azure.
> Certifique-se de que/mnt/Resource não seja usado e que todos os discos sejam montados via UUID. Para o disco do sistema operacional, certifique-se de que a entrada do carregador de pacarregado também reflita a montagem baseada em UUID.
>
>

---
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Preparação para implantar uma VM com uma imagem específica do cliente para SAP

Os arquivos VHD que contêm um sistema operacional generalizado são armazenados em contêineres nas contas de armazenamento do Azure ou como imagens de disco gerenciado. Você pode implantar uma nova VM a partir de uma imagem desse tipo referenciando o VHD ou a imagem do disco gerenciado como uma fonte em seus arquivos de modelo de implantação, conforme descrito no capítulo [cenário 2: Implantando uma VM com uma imagem personalizada para SAP][deployment-guide-3.3] do [Guia de implantação][deployment-guide].

Os requisitos ao preparar sua própria imagem de VM do Azure são:

* Originalmente, o VHD que contém o sistema operacional poderia ter apenas um tamanho máximo de 127 GB. Essa limitação foi eliminada no final de março de 2015. Agora, o VHD que contém o sistema operacional pode ter até 1 TB de tamanho como qualquer outro VHD hospedado do armazenamento do Azure também.
* Ele precisa estar no formato de VHD fixo. VHDs dinâmicos ou VHDs no formato VHDx ainda não têm suporte no Azure. VHDs dinâmicos serão convertidos em VHDs estáticos quando você carregar o VHD com o PowerShell commandlets ou a CLI
* Os VHDs, que são montados na VM e devem ser montados novamente no Azure para a VM precisam estar em um formato VHD fixo também. Leia [Este artigo (Linux)](../../windows/managed-disks-overview.md) e [Este artigo (Windows)](../../linux/managed-disks-overview.md) para obter limites de tamanho de discos de dados. VHDs dinâmicos serão convertidos em VHDs estáticos quando você carregar o VHD com o PowerShell commandlets ou a CLI
* Adicione outras contas locais, pois elas podem ser necessárias para o cenário de implantação específico.
* Se a imagem contiver uma instalação do SAP NetWeaver e for provável renomear o nome do host do nome original no ponto da implantação do Azure, é recomendável copiar as versões mais recentes do DVD do Gerenciador de provisionamento de software SAP para o modelo. Isso permitirá que você use facilmente a funcionalidade de renomeação fornecida pelo SAP para adaptar o nome de host alterado e/ou alterar o SID do sistema SAP na imagem de VM implantada assim que uma nova cópia for iniciada.

---
> ![Windows][Logo_Windows] Windows
>
> Verifique se a unidade D:\ Não é usado definir montagem automática de disco para discos anexados, conforme descrito no capítulo [Configurando a montagem automática para discos anexados][planning-guide-5.5.3] neste documento.
>
> ![Linux][Logo_Linux] Linux
>
> Certifique-se de que/mnt/Resource não seja usado e que todos os discos sejam montados via UUID. Para o disco do sistema operacional, verifique se a entrada do carregador de pacarregado também reflete a montagem baseada em UUID.
>
>

---
* A SAP GUI (para fins administrativos e de instalação) pode ser pré-instalada em um modelo como esse.
* Outro software necessário para executar as VMs com êxito em cenários entre instalações pode ser instalado contanto que esse software possa funcionar com a renomeação da VM.

Se a VM estiver preparada o suficiente para ser genérica e eventualmente independente de contas/usuários não disponíveis no cenário de implantação de destino do Azure, a última etapa de preparação de generalizar tal imagem será realizada.

##### <a name="generalizing-a-vm"></a>Generalizando uma VM
---
> ![Windows][Logo_Windows] Windows
>
> A última etapa é entrar em uma VM com uma conta de administrador. Abra uma janela de comando do Windows como *administrador*. Vá para%WINDIR%\Windows\System32\Sysprep e execute Sysprep. exe.
> Uma janela pequena será exibida. É importante verificar a opção **generalizar** (o padrão está desmarcado) e alterar a opção de desligamento do padrão de ' reinicializar ' para ' desligar '. Esse procedimento pressupõe que o processo Sysprep seja executado localmente no sistema operacional convidado de uma VM.
> Se você quiser executar o procedimento com uma VM já em execução no Azure, siga as etapas descritas neste [artigo](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [Como capturar uma máquina virtual do Linux para ser utilizada como modelo do Resource Manager][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Transferindo VMs e VHDs entre o local para o Azure
Como o carregamento de imagens e discos de VM para o Azure não é possível por meio do portal do Azure, você precisa usar cmdlets Azure PowerShell ou a CLI. Outra possibilidade é o uso da ferramenta ' AzCopy '. A ferramenta pode copiar VHDs entre o local e o Azure (em ambas as direções). Ele também pode copiar VHDs entre regiões do Azure. Consulte [esta documentação][storage-use-azcopy] para obter o download e o uso do AzCopy.

Uma terceira alternativa seria usar várias ferramentas orientadas por GUI de terceiros. No entanto, certifique-se de que essas ferramentas estão dando suporte a blobs de páginas do Azure. Para nossos objetivos, precisamos usar o repositório de blob de páginas do Azure (as diferenças são descritas aqui: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Além disso, as ferramentas fornecidas pelo Azure são eficientes na compactação das VMs e dos VHDs, que precisam ser carregadas. Isso é importante porque essa eficiência na compactação reduz o tempo de carregamento (que varia de qualquer forma, dependendo do link de upload para a Internet a partir da instalação local e da região de implantação do Azure de destino). É uma suposição justa que carregar uma VM ou um VHD do local europeu para os data centers do Azure baseados nos EUA levará mais tempo do que carregar as mesmas VMs/VHDs para os data centers do Azure Europeu.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Carregando um VHD do local para o Azure
Para carregar uma VM ou um VHD existente da rede local, essa VM ou um VHD precisa atender aos requisitos, conforme listado no capítulo [preparação para mover uma VM do local para o Azure com um disco não generalizado][planning-guide-5.2.1] deste documento.

Essa VM não precisa ser generalizada e pode ser carregada no estado e na forma que ela tem após o desligamento no lado local. O mesmo é verdadeiro para VHDs adicionais, que não contêm nenhum sistema operacional.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Carregando um VHD e tornando-o um disco do Azure
Nesse caso, desejamos carregar um VHD, com ou sem um sistema operacional nele, e montá-lo em uma VM como um disco de dados ou usá-lo como um disco do sistema operacional. Este é um processo de várias etapas

**PowerShell**

* Entre em sua assinatura com *Connect-AzAccount*
* Defina a assinatura do contexto com *set-AzContext* e o parâmetro SubscriptionId ou subscriptionname-consulte <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Carregue o VHD com *Add-AzVhd* em uma conta de armazenamento do Azure-consulte <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Adicional Crie um disco gerenciado a partir do VHD com *New-AzDisk* -consulte <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Defina o disco do sistema operacional de uma nova configuração de VM para o VHD ou disco gerenciado com *set-AzVMOSDisk* -consulte <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Crie uma nova VM da configuração da VM com *New-AzVM* -consulte <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Adicionar um disco de dados a uma nova VM com *Add-AzVMDataDisk* -consulte <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**CLI do Azure**

* Entre em sua assinatura com *AZ login*
* Selecione sua assinatura com *AZ Account Set--subscription `<subscription name or id`>*
* Carregar o VHD com *AZ Storage blob upload* -consulte [usando o CLI do Azure com o armazenamento do Azure][storage-azure-cli]
* Adicional Crie um disco gerenciado do VHD com *AZ Disk Create* -consulte https://docs.microsoft.com/cli/azure/disk
* Crie uma nova VM especificando o VHD carregado ou o disco gerenciado como disco do sistema operacional com *AZ VM Create* e o parâmetro *--Attach-os-Disk*
* Adicionar um disco de dados a uma nova VM com *AZ VM Disk Attach* e o parâmetro *--New*

**Modelo**

* Carregar o VHD com o PowerShell ou CLI do Azure
* Adicional Criar um disco gerenciado do VHD com o PowerShell, CLI do Azure ou o portal do Azure
* Implante a VM com um modelo JSON referenciando o VHD, conforme mostrado neste [modelo JSON de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) ou usando Managed disks, conforme mostrado neste [modelo JSON de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Implantação de uma imagem de VM
Para carregar uma VM ou um VHD existente da rede local, para usá-la como uma imagem de VM do Azure, tal VM ou VHD precisa atender aos requisitos listados na [preparação do capítulo para implantar uma VM com uma imagem específica do cliente para SAP][planning-guide-5.2.2] deste documento.

* Use o *Sysprep* no Windows ou *waagent-deprovision* no Linux para generalizar sua VM-consulte [referência técnica do Sysprep](https://technet.microsoft.com/library/cc766049.aspx) para Windows ou [como capturar uma máquina virtual Linux para usar como um modelo do Resource Manager][capture-image-linux-step-2-create-vm-image] para Linux
* Entre em sua assinatura com *Connect-AzAccount*
* Defina a assinatura do contexto com *set-AzContext* e o parâmetro SubscriptionId ou subscriptionname-consulte <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Carregue o VHD com *Add-AzVhd* em uma conta de armazenamento do Azure-consulte <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Adicional Crie uma imagem de disco gerenciado do VHD com *New-AzImage* -consulte <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Definir o disco do sistema operacional de uma nova configuração de VM para o
  * VHD com *set-AzVMOSDisk-SourceImageUri-Createoption fromImage* -consulte <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Conjunto de imagens do disco gerenciado *-AzVMSourceImage* -consulte <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Crie uma nova VM da configuração da VM com *New-AzVM* -consulte <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**CLI do Azure**

* Use o *Sysprep* no Windows ou *waagent-deprovision* no Linux para generalizar sua VM-consulte [referência técnica do Sysprep](https://technet.microsoft.com/library/cc766049.aspx) para Windows ou [como capturar uma máquina virtual Linux para usar como um modelo do Resource Manager][capture-image-linux-step-2-create-vm-image] para Linux
* Entre em sua assinatura com *AZ login*
* Selecione sua assinatura com *AZ Account Set--subscription `<subscription name or id`>*
* Carregar o VHD com *AZ Storage blob upload* -consulte [usando o CLI do Azure com o armazenamento do Azure][storage-azure-cli]
* Adicional Crie uma imagem de disco gerenciado do VHD com *AZ Image Create* -consulte https://docs.microsoft.com/cli/azure/image
* Criar uma nova VM especificando o VHD carregado ou a imagem de disco gerenciado como disco do sistema operacional com *AZ VM Create* e Parameter *--Image*

**Modelo**

* Use o *Sysprep* no Windows ou *waagent-deprovision* no Linux para generalizar sua VM-consulte [referência técnica do Sysprep](https://technet.microsoft.com/library/cc766049.aspx) para Windows ou [como capturar uma máquina virtual Linux para usar como um modelo do Resource Manager][capture-image-linux-step-2-create-vm-image] para Linux
* Carregar o VHD com o PowerShell ou CLI do Azure
* Adicional Criar uma imagem de disco gerenciado do VHD com o PowerShell, CLI do Azure ou o portal do Azure
* Implante a VM com um modelo JSON referenciando o VHD da imagem, conforme mostrado neste [modelo JSON de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) ou usando a imagem de disco gerenciado, conforme mostrado neste [modelo JSON de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Baixando VHDs ou Managed Disks para o local
A infraestrutura do Azure como um serviço não é uma rua unidirecional de ser capaz de carregar VHDs e sistemas SAP. Você também pode mover os sistemas SAP do Azure de volta para o mundo local.

Durante o tempo do download, os VHDs ou o Managed Disks não podem estar ativos. Mesmo ao baixar discos, que são montados em VMs, a VM precisa ser desligada e desalocada. Se você quiser apenas baixar o conteúdo do banco de dados, então deve ser usado para configurar um novo sistema local e se for aceitável que, durante o horário do download, e a instalação do novo sistema que o sistema no Azure ainda possa estar operacional , você pode evitar um tempo de inatividade longo executando um backup de banco de dados compactado em um disco e apenas baixar esse disco em vez de também baixar a VM de base do sistema operacional.

#### <a name="powershell"></a>PowerShell

* Baixando um disco gerenciado  
  Primeiro, você precisa obter acesso ao blob subjacente do disco gerenciado. Em seguida, você pode copiar o blob subjacente para uma nova conta de armazenamento e baixar o blob dessa conta de armazenamento.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* Baixando um VHD  
  Depois que o sistema SAP for interrompido e a VM for desligada, você poderá usar o cmdlet do PowerShell Save-AzVhd no destino local para baixar os discos VHD de volta para o mundo local. Para fazer isso, você precisa da URL do VHD, que pode ser encontrado na ' seção de armazenamento ' do portal do Azure (é necessário navegar até a conta de armazenamento e o contêiner de armazenamento no qual o VHD foi criado) e você precisa saber para onde o VHD deve ser copiado.

  Em seguida, você pode aproveitar o comando definindo o parâmetro SourceUri como a URL do VHD a ser baixado e o LocalFilePath como o local físico do VHD (incluindo seu nome). O comando poderia ser semelhante a:

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Para obter mais detalhes sobre o cmdlet Save-AzVhd, marque aqui <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>.

#### <a name="azure-cli"></a>CLI do Azure
* Baixando um disco gerenciado  
  Primeiro, você precisa obter acesso ao blob subjacente do disco gerenciado. Em seguida, você pode copiar o blob subjacente para uma nova conta de armazenamento e baixar o blob dessa conta de armazenamento.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Baixando um VHD   
  Depois que o sistema SAP for interrompido e a VM for desligada, você poderá usar o comando CLI do Azure _download de blob de armazenamento do Azure_ no destino local para baixar os discos VHD de volta para o mundo local. Para fazer isso, você precisa do nome e do contêiner do VHD, que pode ser encontrado na ' seção de armazenamento ' do portal do Azure (é necessário navegar até a conta de armazenamento e o contêiner de armazenamento no qual o VHD foi criado) e você precisa saber onde o VHD deve ser COP negado.

  Em seguida, você pode aproveitar o comando definindo o blob de parâmetros e o contêiner do VHD para baixar e o destino como o local de destino físico do VHD (incluindo seu nome). O comando poderia ser semelhante a:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Transferindo VMs e discos no Azure

#### <a name="copying-sap-systems-within-azure"></a>Copiando sistemas SAP no Azure

Um sistema SAP ou até mesmo um servidor DBMS dedicado com suporte a uma camada de aplicativo SAP provavelmente consistirá em vários discos, que contêm o sistema operacional com os binários ou os arquivos de dados e de log do banco de dado SAP. Nem a funcionalidade do Azure de copiar discos nem a funcionalidade do Azure de salvar discos em um disco local tem um mecanismo de sincronização, que captura vários discos de maneira consistente. Portanto, o estado dos discos copiados ou salvos, mesmo se eles forem montados na mesma VM, seriam diferentes. Isso significa que, no caso concreto de ter diferentes dados e arquivos de log contidos nos diferentes discos, o banco de dado no final seria inconsistente.

**Conclusão: para copiar ou salvar discos, que fazem parte de uma configuração do sistema SAP, você precisa interromper o sistema SAP e também precisar desligar a VM implantada. Somente em seguida, você pode copiar ou baixar o conjunto de discos para criar uma cópia do sistema SAP no Azure ou no local.**

Os discos de dados podem ser armazenados como arquivos VHD em uma conta de armazenamento do Azure e podem ser anexados diretamente a uma máquina virtual ou ser usados como uma imagem. Nesse caso, o VHD é copiado para outro local antes de ser anexado à máquina virtual. O nome completo do arquivo VHD no Azure deve ser exclusivo no Azure. Como já mencionado anteriormente, o nome é um tipo de nome de três partes semelhante a:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Os discos de dados também podem ser Managed Disks. Nesse caso, o disco gerenciado é usado para criar um novo disco gerenciado antes de ser anexado à máquina virtual. O nome do disco gerenciado deve ser exclusivo dentro de um grupo de recursos.

##### <a name="powershell"></a>PowerShell

Você pode usar Azure PowerShell cmdlets para copiar um VHD, conforme mostrado neste [artigo][storage-powershell-guide-full-copy-vhd]. Para criar um novo disco gerenciado, use New-AzDiskConfig e New-AzDisk, conforme mostrado no exemplo a seguir.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>CLI do Azure

Você pode usar CLI do Azure para copiar um VHD, conforme mostrado neste [artigo][storage-azure-cli-copy-blobs]. Para criar um novo disco gerenciado, use *AZ Disk Create* , conforme mostrado no exemplo a seguir.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Ferramentas de armazenamento do Azure

* <https://storageexplorer.com/>

As edições profissionais dos gerenciadores de armazenamento do Azure podem ser encontradas aqui:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

A cópia de um VHD em uma conta de armazenamento é um processo, que leva apenas alguns segundos (semelhante ao hardware de SAN criando instantâneos com cópia lenta e cópia na gravação). Depois de ter uma cópia do arquivo VHD, você pode anexá-lo a uma máquina virtual ou usá-lo como uma imagem para anexar cópias do VHD a máquinas virtuais.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```
##### <a name="azure-cli"></a>CLI do Azure

```

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Copiando discos entre contas de armazenamento do Azure
Esta tarefa não pode ser executada no portal do Azure. Você pode usar Azure PowerShell cmdlets, CLI do Azure ou um navegador de armazenamento de terceiros. Os cmdlets do PowerShell ou os comandos da CLI podem criar e gerenciar BLOBs, que incluem a capacidade de copiar blobs de forma assíncrona entre contas de armazenamento e entre regiões dentro da assinatura do Azure.

##### <a name="powershell"></a>PowerShell
Você também pode copiar VHDs entre assinaturas. Para obter mais informações, leia [Este artigo][storage-powershell-guide-full-copy-vhd].

O fluxo básico da lógica do cmdlet do PS é semelhante ao seguinte:

* Crie um contexto de conta de armazenamento para a conta de armazenamento de **origem** com *New-AzStorageContext* -consulte <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Crie um contexto de conta de armazenamento para a conta de armazenamento de **destino** com *New-AzStorageContext* -consulte <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Inicie a cópia com

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Verifique o status da cópia em um loop com

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Anexe o novo VHD a uma máquina virtual, conforme descrito acima.

Para obter exemplos, consulte [Este artigo][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>CLI do Azure
* Inicie a cópia com

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Verifique o status se a cópia ainda estiver em um loop com

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Anexe o novo VHD a uma máquina virtual, conforme descrito acima.

Para obter exemplos, consulte [Este artigo][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Manuseio de disco

#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Estrutura de VM/disco para implantações do SAP

Idealmente, a manipulação da estrutura de uma VM e dos discos associados deve ser simples. Em instalações locais, os clientes desenvolveram várias maneiras de estruturar uma instalação de servidor.

* Um disco base, que contém o sistema operacional e todos os binários do DBMS e/ou SAP. Desde março de 2015, esse disco pode ter até 1 TB de tamanho em vez de restrições anteriores que o limitou a 127 GB.
* Um ou vários discos, que contém o arquivo de log do DBMS do banco de dados SAP e o arquivo de log da área de armazenamento temporário do DBMS (se o DBMS der suporte a isso). Se os requisitos de IOPS do log de banco de dados forem altos, você precisará distribuir vários discos para alcançar o volume de IOPS necessário.
* Um número de discos que contêm um ou dois arquivos de banco de dados do SAP e também os arquivos temporários do DBMS (se o DBMS der suporte a isso).

![Configuração de referência da VM IaaS do Azure para SAP][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> Com muitos clientes, vimos configurações em que, por exemplo, os binários do SAP e do DBMS não foram instalados em c:\ unidade em que o sistema operacional foi instalado. Havia vários motivos para isso, mas quando voltamos para a raiz, normalmente era que as unidades eram pequenas e as atualizações de so precisavam de espaço adicional de 10-15 anos atrás. Ambas as condições ainda não se aplicam a esses dias com muita frequência. Hoje, o c:\ a unidade pode ser mapeada em grandes discos de volume ou VMs. Para manter as implantações simples em sua estrutura, é recomendável seguir o seguinte padrão de implantação para sistemas SAP NetWeaver no Azure
>
> O arquivo de paginação do sistema operacional Windows deve estar na unidade D: (disco não persistente)
>
> ![Linux][Logo_Linux] Linux
>
> Coloque o arquivo de permuta do Linux em/mnt/mnt/Resource no Linux, conforme descrito neste [artigo][virtual-machines-linux-agent-user-guide]. O arquivo de permuta pode ser configurado no arquivo de configuração do agente do Linux/etc/waagent.conf. Adicione ou altere as seguintes configurações:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Para ativar as alterações, você precisa reiniciar o agente do Linux com

```
sudo service waagent restart
```

Leia a observação do SAP [1597355] para obter mais detalhes sobre o tamanho recomendado do arquivo de permuta

---
O número de discos usados para os arquivos de dados do DBMS e o tipo de armazenamento do Azure em que esses discos estão hospedados deve ser determinado pelos requisitos de IOPS e pela latência necessária. As cotas exatas são descritas neste [artigo (Linux)][virtual-machines-sizes-linux] e [neste artigo (Windows)][virtual-machines-sizes-windows].

A experiência das implantações do SAP nos últimos dois anos nos ensina algumas lições, que podem ser resumidas como:

* O tráfego de IOPS para arquivos de dados diferentes nem sempre é o mesmo, já que os sistemas de clientes existentes podem ter arquivos de dados de tamanho diferente que representam seus bancos de dado SAP. Como resultado, é melhor usar uma configuração RAID em vários discos para posicionar os arquivos de dados que os LUNs reproduziram. Houve situações, especialmente com o armazenamento standard do Azure, em que uma taxa de IOPS atingiu a cota de um único disco em relação ao log de transações do DBMS. Nesses cenários, o uso do armazenamento Premium é recomendado ou, opcionalmente, agregar vários discos de armazenamento Standard com uma distribuição de software.

---
> ![Windows][Logo_Windows] Windows
>
> * [Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Configurar o RAID de software no Linux][virtual-machines-linux-configure-raid]
> * [Configurar o LVM em uma VM do Linux no Azure][virtual-machines-linux-configure-lvm]
> * [Segredos do armazenamento do Azure e otimizações de e/s do Linux](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

---
* O armazenamento Premium mostra um desempenho melhor e significativo, especialmente para gravações de log de transações críticas. Para cenários do SAP que devem fornecer produção como desempenho, é altamente recomendável usar a série de VMs que podem aproveitar o armazenamento Premium do Azure.

Tenha em mente que o disco, que contém o sistema operacional e, como recomendamos, os binários do SAP e do banco de dados (VM de base) também não é mais limitado a 127 GB. Agora ele pode ter até 1 TB de tamanho. Isso deve ser espaço suficiente para manter todo o arquivo necessário, incluindo, por exemplo, logs de trabalho do lote SAP.

Para obter mais sugestões e mais detalhes, especificamente para VMs do DBMS, consulte o [Guia de implantação do DBMS][dbms-guide]

#### <a name="disk-handling"></a>Manuseio de disco

Na maioria dos cenários, você precisa criar discos adicionais para implantar o banco de dados SAP na VM. Falamos sobre as considerações sobre o número de discos na [estrutura de VM/disco do capítulo para implantações do SAP][planning-guide-5.5.1] deste documento. O portal do Azure permite anexar e desanexar discos depois que uma VM base é implantada. Os discos podem ser anexados/desanexados quando a VM estiver ativa e em execução, bem como quando ela for interrompida. Ao anexar um disco, o portal do Azure oferece para anexar um disco vazio ou um disco existente, que nesse momento não está conectado a outra VM.

**Observação**: os discos só podem ser anexados a uma VM em um determinado momento.

![Anexar/desanexar discos com o armazenamento standard do Azure][planning-guide-figure-1400]

Durante a implantação de uma nova máquina virtual, você pode decidir se deseja usar Managed Disks ou posicionar seus discos em contas de armazenamento do Azure. Se você quiser usar o armazenamento Premium, recomendamos o uso de Managed Disks.

Em seguida, você precisa decidir se deseja criar um disco novo e vazio ou se deseja selecionar um disco existente que foi carregado anteriormente e deve ser anexado à VM agora.

**Importante**: você **não** deseja usar o cache de host com o armazenamento standard do Azure. Você deve deixar a preferência de cache do host no padrão de nenhum. Com o armazenamento Premium do Azure, você deve habilitar o cache de leitura se a característica de e/s for mais lida como o tráfego de e/s típico em relação aos arquivos de dados. No caso do arquivo de log de transações do banco de dados, nenhum cache é recomendado.

---
> ![Windows][Logo_Windows] Windows
>
> [Como anexar um disco de dados no portal do Azure][virtual-machines-linux-attach-disk-portal]
>
> Se discos estiverem anexados, você precisará entrar na VM para abrir o Gerenciador de disco do Windows. Se a montagem automática não estiver habilitada como recomendado no capítulo [Configurando a montagem automática para discos anexados][planning-guide-5.5.3], o volume recentemente anexado precisará ser colocado online e inicializado.
>
> ![Linux][Logo_Linux] Linux
>
> Se discos estiverem anexados, você precisará entrar na VM e inicializar os discos, conforme descrito neste [artigo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

---
Se o novo disco for um disco vazio, você também precisará formatar o disco. Para formatação, especialmente para arquivos de log e de dados do DBMS, as mesmas recomendações para implantações bare-metal do DBMS se aplicam.

Como já mencionado no capítulo [o conceito de Microsoft Azure máquina virtual][planning-guide-3.2], uma conta de armazenamento do Azure não fornece recursos infinitas em termos de volume de e/s, IOPS e volume de dados. Geralmente, as VMs do DBMS são mais afetadas por isso. Pode ser melhor usar uma conta de armazenamento separada para cada VM se você tiver poucas VMs de volume de e/s a serem implantadas para permanecer dentro do limite do volume da conta de armazenamento do Azure. Caso contrário, você precisará ver como você pode balancear essas VMs entre diferentes contas de armazenamento sem atingir o limite de cada conta de armazenamento única. Mais detalhes são discutidos no [Guia de implantação do DBMS][dbms-guide]. Você também deve manter essas limitações em mente para VMs puras do servidor de aplicativos SAP ou outras VMs, que eventualmente podem exigir VHDs adicionais. Essas restrições não se aplicam se você usar o disco gerenciado. Se você planeja usar o armazenamento Premium, recomendamos o uso do disco gerenciado.

Outro tópico, que é relevante para contas de armazenamento, é se os VHDs em uma conta de armazenamento estão sendo replicados geograficamente. A replicação geográfica é habilitada ou desabilitada no nível da conta de armazenamento e não no nível da VM. Se a replicação geográfica estiver habilitada, os VHDs na conta de armazenamento serão replicados em outra data center do Azure na mesma região. Antes de decidir sobre isso, você deve pensar na seguinte restrição:

A replicação geográfica do Azure funciona localmente em cada VHD em uma VM e não replica o IOs em ordem cronológica em vários VHDs em uma VM. Portanto, o VHD que representa a VM base, bem como quaisquer VHDs adicionais anexados à VM, são replicados independentemente um do outro. Isso significa que não há nenhuma sincronização entre as alterações nos diferentes VHDs. O fato de que o IOs é replicado de forma independente da ordem em que eles são gravados significa que a replicação geográfica não é de valor para servidores de banco de dados que têm seus bancos distribuídos em vários VHDs. Além do DBMS, também pode haver outros aplicativos em que os processos gravam ou manipulam dados em diferentes VHDs e onde é importante manter a ordem das alterações. Se isso for um requisito, a replicação geográfica no Azure não deve ser habilitada. Dependendo se você precisa ou deseja replicação geográfica para um conjunto de VMs, mas não para outro conjunto, você pode já categorizar as VMs e seus VHDs relacionados em diferentes contas de armazenamento que têm a replicação geográfica habilitada ou desabilitada.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Configurando a montagem automática para discos anexados
---
> ![Windows][Logo_Windows] Windows
>
> Para VMs, que são criadas a partir de imagens ou discos próprios, é necessário verificar e possivelmente definir o parâmetro de montagem automática. Definir esse parâmetro permitirá a VM após uma reinicialização ou reimplantação no Azure para montar as unidades conectadas/montadas automaticamente.
> O parâmetro é definido para as imagens fornecidas pela Microsoft no Azure Marketplace.
>
> Para definir a montagem automática, verifique a documentação do executável de linha de comando diskpart. exe aqui:
>
> * [Opções de linha de comando do DiskPart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Montagem automática](https://technet.microsoft.com/library/cc753703.aspx)
>
> A janela de linha de comando do Windows deve ser aberta como administrador.
>
> Se discos estiverem anexados, você precisará entrar na VM para abrir o Gerenciador de disco do Windows. Se a montagem automática não estiver habilitada como recomendado no capítulo [Configurando a montagem automática para discos anexados][planning-guide-5.5.3], o volume recentemente anexado > precisará ser colocado online e inicializado.
>
> ![Linux][Logo_Linux] Linux
>
> Você precisa inicializar um disco vazio anexado recentemente, conforme descrito neste [artigo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Você também precisa adicionar novos discos ao/etc/fstab.
>
>

---
### <a name="final-deployment"></a>Implantação final

Para a implantação final e as etapas exatas, especialmente com relação à implantação da extensão do Azure para SAP, consulte o [Guia de implantação][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Acessando sistemas SAP em execução nas VMs do Azure

Para cenários em que você deseja se conectar a esses sistemas SAP pela Internet pública usando a SAP GUI, os procedimentos a seguir precisam ser aplicados.

Posteriormente no documento, discutiremos o outro cenário principal, conectando-se a sistemas SAP em implantações entre locais, que têm uma conexão site a site (túnel VPN) ou conexão do Azure ExpressRoute entre os sistemas locais e os sistemas do Azure.

### <a name="remote-access-to-sap-systems"></a>Acesso remoto a sistemas SAP

Com Azure Resource Manager, não há mais nenhum ponto de extremidade padrão como no antigo modelo clássico. Todas as portas de uma VM Azure Resource Manager são abertas desde que:

1. Nenhum grupo de segurança de rede está definido para a sub-rede ou para a interface de rede. O tráfego de rede para VMs do Azure pode ser protegido por meio do chamado "grupos de segurança de rede". Para obter mais informações, consulte o artigo [O que é um Grupo de Segurança de Rede (NSG)?][virtual-networks-nsg]
2. Nenhuma Azure Load Balancer está definida para a interface de rede   

Consulte a diferença de arquitetura entre o modelo clássico e o ARM, conforme descrito neste [artigo][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Configuração do sistema SAP e conectividade de GUI SAP na Internet

Consulte este artigo, que descreve os detalhes deste tópico: <https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Alterando as configurações de firewall na VM

Pode ser necessário configurar o firewall em suas máquinas virtuais para permitir o tráfego de entrada para seu sistema SAP.

---
> ![Windows][Logo_Windows] Windows
>
> Por padrão, o Firewall do Windows em uma VM implantada no Azure é ativado. Agora você precisará permitir que a porta SAP seja aberta, caso contrário, a GUI SAP não será capaz de se conectar.
> Para efetuar este procedimento:
>
> * Abra Control Controle\sistema e segurança \firewall firewall to **Advanced Settings**.
> * Agora, clique com o botão direito do mouse em regras de entrada e escolha **nova regra**.
> * No assistente a seguir, escolha criar uma nova regra de **porta** .
> * Na próxima etapa do assistente, deixe a configuração em TCP e digite o número da porta que você deseja abrir. Como nossa ID de instância SAP é 00, pegamos 3200. Se sua instância tiver um número de instância diferente, a porta que você definiu anteriormente com base no número da instância deverá ser aberta.
> * Na próxima parte do assistente, você precisa deixar o item **permitir a conexão** marcada.
> * Na próxima etapa do assistente, você precisa definir se a regra se aplica ao domínio, à rede privada e pública. Ajuste-o, se necessário, às suas necessidades. No entanto, conectar-se com a GUI do SAP de fora por meio da rede pública, você precisa ter a regra aplicada à rede pública.
> * Na última etapa do assistente, nomeie a regra e salve pressionando **concluir**.
>
> A regra entra em vigor imediatamente.
>
> ![Definição de regra de porta][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> As imagens do Linux no Azure Marketplace não habilitam o Firewall do iptables por padrão e a conexão com o sistema SAP deve funcionar. Se você tiver habilitado o iptables ou outro firewall, consulte a documentação do iptables ou o firewall usado para permitir o tráfego TCP de entrada para a porta 32xx (em que XX é o número do sistema do sistema SAP).
>
>

---
#### <a name="security-recommendations"></a>Recomendações de segurança

A GUI do SAP não se conecta imediatamente a nenhuma das instâncias SAP (porta 32xx) que estão em execução, mas se conecta pela primeira vez por meio da porta aberta para o processo do servidor de mensagens SAP (porta 36xx). No passado, a mesma porta foi usada pelo servidor de mensagens para a comunicação interna com as instâncias do aplicativo. Para evitar que os servidores de aplicativos locais se comuniquem inadvertidamente com um servidor de mensagens no Azure, as portas de comunicação internas podem ser alteradas. É altamente recomendável alterar a comunicação interna entre o servidor de mensagens SAP e suas instâncias de aplicativo para um número de porta diferente em sistemas que foram clonados de sistemas locais, como um clone de desenvolvimento para testes de projeto, etc. Isso pode ser feito com o parâmetro de perfil padrão:

> rdisp/msserv_internal
>
>

conforme documentado em [configurações de segurança para o servidor de mensagens SAP](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Cenário de demonstração/treinamento de VM única com SAP NetWeaver

![Executando sistemas de demonstração SAP de VM única com os mesmos nomes de VM, isolados nos serviços de nuvem do Azure][planning-guide-figure-1700]

Neste cenário, estamos implementando um cenário típico de sistema de treinamento/demonstração em que o cenário completo de treinamento/demonstração está contido em uma única VM. Supomos que a implantação seja feita por meio de modelos de imagem de VM. Também presumimos que várias dessas VMs de demonstração/treinamento precisam ser implantadas com as VMs com o mesmo nome. Os sistemas de treinamento completos não têm conectividade com seus ativos locais e são um oposto a uma implantação híbrida.

A suposição é que você criou uma imagem de VM, conforme descrito em algumas seções do capítulo [preparando VMs com o SAP para Azure][planning-guide-5.2] neste documento.

A sequência de eventos para implementar o cenário tem a seguinte aparência:

##### <a name="powershell"></a>PowerShell

* Criar um novo grupo de recursos para cada cenário de treinamento/demonstração

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```
* Crie uma nova conta de armazenamento se você não quiser usar Managed Disks

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Crie uma nova rede virtual para cada cenário de treinamento/demonstração para habilitar o uso do mesmo nome de host e endereços IP. A rede virtual é protegida por um grupo de segurança de rede que permite apenas o tráfego para a porta 3389 para habilitar o acesso Área de Trabalho Remota e a porta 22 para SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Criar um novo endereço IP público que possa ser usado para acessar a máquina virtual da Internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Cria uma máquina virtual. Para esse cenário, cada VM terá o mesmo nome. O SID do SAP das instâncias do SAP NetWeaver nessas VMs também será o mesmo. Dentro do grupo de recursos do Azure, o nome da VM precisa ser exclusivo, mas em diferentes grupos de recursos do Azure, você pode executar VMs com o mesmo nome. A conta padrão ' Administrador ' do Windows ou ' raiz ' para Linux não é válida. Portanto, um novo nome de usuário de administrador precisa ser definido junto com uma senha. O tamanho da VM também precisa ser definido.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Opcionalmente, adicione mais discos e restaure o conteúdo necessário. Todos os nomes de BLOB (URLs para os BLOBs) devem ser exclusivos no Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>CLI

O código de exemplo a seguir pode ser usado no Linux. Para o Windows, use o PowerShell conforme descrito acima ou adapte o exemplo para usar% rgName% em vez de $rgName e defina a variável de ambiente usando o *conjunto*de comandos do Windows.

* Criar um novo grupo de recursos para cada cenário de treinamento/demonstração

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Criar uma nova conta de armazenamento

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Crie uma nova rede virtual para cada cenário de treinamento/demonstração para habilitar o uso do mesmo nome de host e endereços IP. A rede virtual é protegida por um grupo de segurança de rede que permite apenas o tráfego para a porta 3389 para habilitar o acesso Área de Trabalho Remota e a porta 22 para SSH.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Criar um novo endereço IP público que possa ser usado para acessar a máquina virtual da Internet

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Cria uma máquina virtual. Para esse cenário, cada VM terá o mesmo nome. O SID do SAP das instâncias do SAP NetWeaver nessas VMs também será o mesmo. Dentro do grupo de recursos do Azure, o nome da VM precisa ser exclusivo, mas em diferentes grupos de recursos do Azure, você pode executar VMs com o mesmo nome. A conta padrão ' Administrador ' do Windows ou ' raiz ' para Linux não é válida. Portanto, um novo nome de usuário de administrador precisa ser definido junto com uma senha. O tamanho da VM também precisa ser definido.

```
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Opcionalmente, adicione mais discos e restaure o conteúdo necessário. Todos os nomes de BLOB (URLs para os BLOBs) devem ser exclusivos no Azure.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Modelo

Você pode usar os modelos de exemplo no repositório Azure-QuickStart-templates no GitHub.

* [VM Linux simples](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [VM simples do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VM da imagem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementar um conjunto de VMs que se comunicam no Azure

Esse cenário não híbrido é um cenário típico para fins de treinamento e demonstração em que o software que representa o cenário de demonstração/treinamento é distribuído por várias VMs. Os diferentes componentes instalados nas diferentes VMs precisam se comunicar entre si. Novamente, nesse cenário, não é necessária nenhuma comunicação de rede local ou cenário entre instalações.

Esse cenário é uma extensão da instalação descrita no capítulo [única VM com o cenário de demonstração/treinamento do SAP NetWeaver][planning-guide-7.1] deste documento. Nesse caso, mais máquinas virtuais serão adicionadas a um grupo de recursos existente. No exemplo a seguir, o panorama de treinamento consiste em uma VM SAP ASCS/SCS, uma VM que executa um DBMS e uma VM de instância do servidor de aplicativos SAP.

Antes de criar esse cenário, você precisa pensar sobre as configurações básicas como já exercidas no cenário antes.

#### <a name="resource-group-and-virtual-machine-naming"></a>Nomenclatura de grupo de recursos e máquina virtual

Todos os nomes de grupos de recursos devem ser exclusivos. Desenvolva seu próprio esquema de nomenclatura de seus recursos, como `<rg-name`> sufixo.

O nome da máquina virtual deve ser exclusivo dentro do grupo de recursos.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Configurar a rede para comunicação entre as diferentes VMs

![Conjunto de VMs em uma rede virtual do Azure][planning-guide-figure-1900]

Para evitar colisões de nomenclatura com clones dos mesmos cenários de treinamento/demonstração, você precisa criar uma rede virtual do Azure para cada cenário. A resolução de nomes DNS será fornecida pelo Azure ou você poderá configurar seu próprio servidor DNS fora do Azure (não será mais discutido aqui). Nesse cenário, não configuramos nosso próprio DNS. Para todas as máquinas virtuais dentro de uma rede virtual do Azure, a comunicação por meio de nomes de host será habilitada.

Os motivos para separar os cenários de treinamento ou de demonstração por redes virtuais e não apenas os grupos de recursos podem ser:

* A estrutura da SAP como configurada precisa de seu próprio AD/OpenLDAP e um servidor de domínio precisa fazer parte de cada um dos cenários.  
* A estrutura do SAP como configurada tem componentes que precisam trabalhar com endereços IP fixos.

Mais detalhes sobre as redes virtuais do Azure e como defini-las podem ser encontradas neste [artigo][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Implantando VMs SAP com conectividade de rede corporativa (entre instalações)

Você executa uma estrutura SAP e deseja dividir a implantação entre bare-metal para servidores DBMS de alto nível, ambientes virtualizados locais para camadas de aplicativos e sistemas SAP menores configurados de 2 camadas e Azure IaaS. A suposição base é que os sistemas SAP em uma estrutura SAP precisam se comunicar entre si e com muitos outros componentes de software implantados na empresa, independentemente do seu formulário de implantação. Também não deve haver nenhuma diferença introduzida pelo formulário de implantação para o usuário final se conectando com a GUI SAP ou outras interfaces. Essas condições só podem ser atendidas quando temos os serviços de Active Directory/OpenLDAP e DNS locais estendidos para os sistemas do Azure por meio de conectividade site a site/multissite ou conexões privadas, como o Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Cenário de uma estrutura SAP

O cenário entre locais ou híbridos pode ser descrito aproximadamente como nos gráficos abaixo:

![Conectividade site a site entre os ativos locais e do Azure][planning-guide-figure-2100]

O cenário mostrado acima descreve um cenário no qual o local

O requisito mínimo é o uso de protocolos de comunicação seguros, como SSL/TLS para acesso ao navegador ou conexões baseadas em VPN para acesso do sistema aos serviços do Azure. A suposição é que as empresas manipulem a conexão VPN entre sua rede corporativa e o Azure de forma diferente. Algumas empresas podem abrir todas as portas em branco. Algumas outras empresas podem querer ser precisas em quais portas elas precisam abrir etc.

Na tabela abaixo, estão listadas portas de comunicação SAP típicas. Basicamente, é suficiente abrir a porta do gateway SAP.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Serviço | Nome de porta | Exemplo `<nn`> = 01 | Intervalo padrão (mín.-máx.) | Comentário |
| --- | --- | --- | --- | --- |
| Despacha |sapdp`<nn>` consulte * |3201 |3200 - 3299 |Dispatcher SAP, usado pela SAP GUI para Windows e Java |
| Servidor de mensagens |sapms`<sid`> consulte * * |3600 |sapms >`<anySID`grátis |Sid = SAP-System-ID |
| Gateway |sapgw`<nn`> consulte * |3301 |informações |SAP gateway, usado para comunicação comunicação CPIC e RFC |
| Roteador SAP |sapdp99 |3299 |informações |Somente nomes de serviço CI (instância central) podem ser reatribuídos em/etc/Services para um valor arbitrário após a instalação. |

*) nn = número da instância do SAP

\* *) Sid = SAP-System-ID

Informações mais detalhadas sobre as portas necessárias para diferentes produtos ou serviços SAP por produtos SAP podem ser encontradas aqui <https://scn.sap.com/docs/DOC-17124>.
Com este documento, você deve ser capaz de abrir portas dedicadas no dispositivo VPN necessárias para produtos e cenários específicos do SAP.

Outras medidas de segurança ao implantar VMs em um cenário como esse poderiam ser criar um [grupo de segurança de rede][virtual-networks-nsg] para definir as regras de acesso.

### <a name="dealing-with-different-virtual-machine-series"></a>Lidando com séries de máquina virtual diferentes

A Microsoft adicionou muitos outros tipos de VM que diferem em número de vCPUs, memória ou mais importantes no hardware em que ele está sendo executado. Nem todas essas VMs têm suporte com o SAP (consulte tipos de VM com suporte na observação SAP [1928533]). Algumas dessas VMs são executadas em diferentes gerações de hardware de host. Essas gerações de hardware de host estão sendo implantadas na granularidade de uma unidade de escala do Azure. Podem surgir casos em que os diferentes tipos de VM que você escolheu não podem ser executados na mesma unidade de escala. Um conjunto de disponibilidade é limitado na capacidade de abranger unidades de escala baseadas em hardwares diferentes.  Por exemplo, se você estiver executando a camada do DBMS do SAP em uma VM E64s_v3 que está em um conjunto de disponibilidade junto com a VM que executa a instância de DBMS secundária em uma configuração de alta disponibilidade, não poderá simplesmente parar e reiniciar a VM secundária como VM da série M porque talvez você queira atualiza r a VM. O motivo é que as VMs da série M e as VMs da série Ev3 estão em execução em diferentes hardwares e com isso em diferentes unidades de escala. Você precisaria criar um novo conjunto de disponibilidade, excluir a VM da série Ev3 secundária, sem excluir o armazenamento e reimplantar a VM como VM da série M no novo conjunto de disponibilidade.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Imprimindo em uma impressora de rede local da instância SAP no Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Imprimindo sobre TCP/IP em cenário entre instalações

A configuração de suas impressoras de rede baseadas em TCP/IP locais em uma VM do Azure é o mesmo geral da rede corporativa, supondo que você tenha um túnel VPN site a site ou conexão de ExpressRoute estabelecida.

---
> ![Windows][Logo_Windows] Windows
>
> Para efetuar este procedimento:
>
> * Algumas impressoras de rede vêm com um assistente de configuração que facilita a configuração de sua impressora em uma VM do Azure. Se nenhum software de assistente tiver sido distribuído com a impressora, a maneira manual de configurar a impressora é criar uma nova porta de impressora TCP/IP.
> * Abra o painel de controle-> dispositivos e impressoras-> Adicionar uma impressora
> * Escolha Adicionar uma impressora usando um endereço TCP/IP ou nome do host
> * Digite o endereço IP da impressora
> * Porta de impressora standard 9100
> * Se necessário, instale manualmente o driver de impressora apropriado.
>
> ![Linux][Logo_Linux] Linux
>
> * como o para Windows, basta seguir o procedimento padrão para instalar uma impressora de rede
> * Basta seguir os guias públicos do Linux para [SuSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) ou [Red Hat e Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) sobre como adicionar uma impressora.
>
>

---
![Impressão de rede][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Impressora baseada em host sobre SMB (impressora compartilhada) no cenário entre instalações

As impressoras baseadas em host não são compatíveis com a rede por design. Mas uma impressora baseada em host pode ser compartilhada entre computadores em uma rede, contanto que a impressora esteja conectada a um computador ligado. Conecte sua rede corporativa site a site ou ExpressRoute e compartilhe sua impressora local. O protocolo SMB usa NetBIOS em vez de DNS como serviço de nome. O nome do host NetBIOS pode ser diferente do nome do host DNS. O caso padrão é que o nome do host NetBIOS e o nome do host DNS são idênticos. O domínio DNS não faz sentido no espaço do nome NetBIOS. Da mesma forma, o nome de host DNS totalmente qualificado que consiste no nome de host DNS e no domínio DNS não deve ser usado no espaço de nome NetBIOS.

O compartilhamento de impressora é identificado por um nome exclusivo na rede:

* Nome do host do host SMB (sempre necessário).
* Nome do compartilhamento (sempre necessário).
* Nome do domínio se o compartilhamento de impressora não estiver no mesmo domínio que o sistema SAP.
* Além disso, um nome de usuário e uma senha podem ser necessários para acessar o compartilhamento de impressora.

Como:

---
> ![Windows][Logo_Windows] Windows
>
> Compartilhe sua impressora local.
> Na VM do Azure, abra o Windows Explorer e digite o nome do compartilhamento da impressora.
> Um assistente de instalação de impressora orientará você durante o processo de instalação.
>
> ![Linux][Logo_Linux] Linux
>
> Aqui estão alguns exemplos de documentação sobre como configurar impressoras de rede no Linux ou incluir um capítulo em relação à impressão no Linux. Ele funcionará da mesma maneira em uma VM Linux do Azure, desde que a VM faça parte de uma VPN:
>
> * <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share> SLES
> * RHEL ou Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>Impressora USB (encaminhamento de impressora)

No Azure, a capacidade do Serviços de Área de Trabalho Remota de fornecer aos usuários o acesso aos dispositivos de impressora local em uma sessão remota não está disponível.

---
> ![Windows][Logo_Windows] Windows
>
> Mais detalhes sobre a impressão com o Windows podem ser encontrados aqui: <https://technet.microsoft.com/library/jj590748.aspx>.
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integração de sistemas do SAP Azure ao TMS (sistema de correção e transporte) em vários locais

O TMS (sistema de alteração e transporte) SAP precisa ser configurado para exportar e importar a solicitação de transporte entre sistemas no cenário. Supomos que as instâncias de desenvolvimento de um sistema SAP (desenvolvimento) estão localizadas no Azure, enquanto os sistemas de controle de qualidade (QA) e produtiva (PRD) são locais. Além disso, supomos que haja um diretório de transporte central.

##### <a name="configuring-the-transport-domain"></a>Configurando o domínio de transporte

Configure seu domínio de transporte no sistema designado como o controlador de domínio de transporte, conforme descrito em [Configurando o controlador de domínio de transporte](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Um TMSADM de usuário do sistema será criado e o destino RFC necessário será gerado. Você pode verificar essas conexões RFC usando a transação SM59. A resolução do nome do host deve ser habilitada em seu domínio de transporte.

Como:

* Em nosso cenário, decidimos que o sistema QAS local será o controlador de domínio CTS. Chame a transação STMS. A caixa de diálogo TMS é exibida. Uma caixa de diálogo Configurar domínio de transporte é exibida. (Essa caixa de diálogo só aparecerá se você ainda não tiver configurado um domínio de transporte.)
* Verifique se o usuário criado automaticamente TMSADM é autorizado (SM59-> conexão de ABAP-> TMSADM@E61.DOMAIN_E61-> detalhes-> Utilitários (M)-> teste de autorização). A tela inicial de STMS de transação deve mostrar que este sistema SAP agora está funcionando como controlador do domínio de transporte, conforme mostrado aqui:

![Tela inicial de STMS de transação no controlador de domínio][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Incluindo sistemas SAP no domínio de transporte

A sequência de inclusão de um sistema SAP em um domínio de transporte tem a seguinte aparência:

* No sistema de desenvolvimento no Azure, vá para o sistema de transporte (cliente 000) e chame a transação STMS. Escolha outra configuração na caixa de diálogo e continue com o sistema de inclusão no domínio. Especifique o controlador de domínio como host de destino ([incluindo sistemas SAP no domínio de transporte](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). O sistema agora está aguardando para ser incluído no domínio de transporte.
* Por motivos de segurança, você precisa voltar ao controlador de domínio para confirmar sua solicitação. Escolha visão geral do sistema e aprove o sistema em espera. Em seguida, confirme o prompt e a configuração será distribuída.

Este sistema SAP agora contém as informações necessárias sobre todos os outros sistemas SAP no domínio de transporte. Ao mesmo tempo, os dados de endereço do novo sistema SAP são enviados a todos os outros sistemas SAP, e o sistema SAP é inserido no perfil de transporte do programa de controle de transporte. Verifique se os RFCs e o acesso ao diretório de transporte do domínio funcionam.

Continue com a configuração do seu sistema de transporte como de costume, conforme descrito no [sistema de alteração e transporte](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)de documentação.

Como:

* Verifique se seu STMS local está configurado corretamente.
* Verifique se o nome do host do controlador de domínio de transporte pode ser resolvido por sua máquina virtual no Azure e vice-versa.
* Chamar STMS de transação-> outra configuração-> incluir o sistema no domínio.
* Confirme a conexão no sistema TMS local.
* Configure rotas de transporte, grupos e camadas como de costume.

Em cenários entre locais conectados site a site, a latência entre o local e o Azure ainda pode ser substancial. Se seguirmos a sequência de transporte de objetos por meio de sistemas de desenvolvimento e teste para produção ou pensarmos em aplicar transportes ou pacotes de suporte a diferentes sistemas, você percebe que, dependendo do local do diretório de transporte central, alguns dos sistemas encontrarão alta latência de leitura ou gravação de dados no diretório de transporte central. A situação é semelhante às configurações de cenário do SAP em que os diferentes sistemas estão espalhados por diferentes data centers com distância substancial entre os data centers.

Para contornar essa latência e fazer com que os sistemas funcionem rapidamente na leitura ou gravação no diretório de transporte, você pode configurar dois domínios de transporte STMS (um para o local e outro com os sistemas no Azure e vincular os domínios de transporte. Consulte esta documentação, que explica os princípios por trás deste conceito no SAP TMS: <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Como:

* Configure um domínio de transporte em cada local (no local e no Azure) usando a transação STMS <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Vincule os domínios com um link de domínio e confirme o vínculo entre os dois domínios.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribua a configuração para o sistema vinculado.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Tráfego RFC entre instâncias SAP localizado no Azure e no local (entre instalações)

O tráfego RFC entre os sistemas, que são locais e no Azure, precisa funcionar. Para configurar uma conexão, chame a transação SM59 em um sistema de origem em que você precisa definir uma conexão RFC para o sistema de destino. A configuração é semelhante à configuração padrão de uma conexão RFC.

Supomos que, no cenário entre instalações, as VMs, que executam sistemas SAP que precisam se comunicar entre si, estão no mesmo domínio. Portanto, a configuração de uma conexão RFC entre sistemas SAP não difere das etapas de instalação e das entradas em cenários locais.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Acessando compartilhamentos de filelocal de instâncias SAP localizadas no Azure ou vice-versa

As instâncias SAP localizadas no Azure precisam acessar compartilhamentos de arquivos, que estão dentro do local corporativo. Além disso, as instâncias SAP locais precisam acessar compartilhamentos de arquivos, que estão localizados no Azure. Para habilitar os compartilhamentos de arquivos, você deve configurar as permissões e as opções de compartilhamento no sistema local. Certifique-se de abrir as portas na conexão VPN ou ExpressRoute entre o Azure e seu datacenter.

## <a name="supportability"></a>Suportabilidade

### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Extensão do Azure para SAP

Para alimentar alguma parte das informações de infraestrutura do Azure de sistemas SAP críticos para as instâncias do agente de host do SAP, instaladas em VMs, uma extensão do Azure (VM) para SAP precisa ser instalada para as VMs implantadas. Como as demandas pela SAP eram específicas para aplicativos SAP, a Microsoft decidiu não implementar genericamente a funcionalidade necessária no Azure, mas deixar que os clientes implantem a extensão de VM necessária e as configurações em suas máquinas virtuais em execução no Azure. No entanto, a implantação e o gerenciamento do ciclo de vida da extensão de VM do Azure para SAP serão geralmente automatizados pelo Azure.

#### <a name="solution-design"></a>Conceção da solução

A solução desenvolvida para habilitar o agente de host do SAP obter as informações necessárias é baseada na arquitetura do agente de VM do Azure e da estrutura de extensão. A ideia do agente de VM do Azure e da estrutura de extensão é permitir a instalação de aplicativos de software disponíveis na Galeria de extensões de VM do Azure em uma VM. A ideia principal por trás desse conceito é permitir (em casos como a extensão do Azure para SAP), a implantação de funcionalidade especial em uma VM e a configuração desses softwares no momento da implantação.

O "agente de VM do Azure" que permite o tratamento de extensões de VM do Azure específicas dentro da VM é injetado em VMs do Windows por padrão na criação de VM no portal do Azure. No caso do SUSE, Red Hat ou Oracle Linux, o agente de VM já faz parte da imagem do Azure Marketplace. Caso um carregue uma VM Linux do local para o Azure, o agente de VM precisa ser instalado manualmente.

Os blocos de construção básicos da solução para fornecer informações de infraestrutura do Azure ao agente de host do SAP no Azure têm a seguinte aparência:

![Componentes de extensão de Microsoft Azure][planning-guide-figure-2400]

Conforme mostrado no diagrama de bloco acima, uma parte da solução é hospedada na imagem de VM do Azure e na Galeria de extensões do Azure, que é um repositório replicado globalmente que é gerenciado pelas operações do Azure. É responsabilidade da equipe conjunta do SAP/MS trabalhando na implementação do SAP do Azure para trabalhar com operações do Azure para publicar novas versões da extensão do Azure para SAP.

Quando você implanta uma nova VM do Windows, o agente de VM do Azure é adicionado automaticamente à VM. A função desse agente é coordenar o carregamento e a configuração das extensões do Azure das VMs. Para VMs do Linux, o agente de VM do Azure já faz parte da imagem do sistema operacional do Azure Marketplace.

No entanto, há uma etapa que ainda precisa ser executada pelo cliente. Esta é a habilitação e configuração da coleta de desempenho. O processo relacionado à configuração é automatizado por um script do PowerShell ou comando da CLI. O script do PowerShell pode ser baixado no Microsoft Azure Script Center, conforme descrito no [Guia de implantação][deployment-guide].

A arquitetura geral da extensão do Azure para SAP é semelhante a:

![Extensão do Azure para SAP ][planning-guide-figure-2500]

**Para obter instruções exatas e etapas detalhadas sobre como usar esses cmdlets do PowerShell ou o comando da CLI durante as implantações, siga as orientações fornecidas no [Guia de implantação][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integração da instância SAP localizada do Azure ao SAProuter

As instâncias SAP em execução no Azure precisam estar acessíveis do SAProuter também.

![Conexão de rede do roteador SAP][planning-guide-figure-2600]

Um SAProuter permite a comunicação TCP/IP entre os sistemas participantes se não houver nenhuma conexão IP direta. Isso fornece a vantagem de que nenhuma conexão de ponta a ponta entre os parceiros de comunicação é necessária no nível de rede. O SAProuter está escutando na porta 3299 por padrão.
Para conectar instâncias SAP por meio de um SAProuter, você precisa fornecer a cadeia de caracteres SAProuter e o nome do host com qualquer tentativa de conexão.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java

Até agora, o foco do documento foi o SAP NetWeaver em geral ou a pilha ABAP do SAP NetWeaver. Nesta pequena seção, são listadas considerações específicas para a pilha Java do SAP. Um dos aplicativos mais importantes baseados em Java do SAP NetWeaver é o Enterprise Portal do SAP. Outros aplicativos baseados no SAP NetWeaver, como o SAP PI e o SAP Solution Manager, usam as pilhas de Java e ABAP do SAP NetWeaver. Portanto, certamente há a necessidade de considerar aspectos específicos relacionados à pilha Java do SAP NetWeaver também.

### <a name="sap-enterprise-portal"></a>Enterprise Portal SAP

A configuração de um portal SAP em uma máquina virtual do Azure não difere de uma instalação local se você estiver implantando em cenários entre instalações. Como o DNS é feito pelo local, as configurações de porta das instâncias individuais podem ser feitas como configuradas localmente. As recomendações e restrições descritas neste documento até agora se aplicam a um aplicativo como o SAP Enterprise Portal ou a pilha Java do SAP NetWeaver em geral.

![Portal SAP exposto][planning-guide-figure-2700]

Um cenário de implantação especial por alguns clientes é a exposição direta do SAP Enterprise Portal à Internet enquanto o host da máquina virtual está conectado à rede da empresa por meio do túnel VPN site a site ou do ExpressRoute. Para esse cenário, você precisa certificar-se de que as portas específicas estão abertas e não bloqueadas pelo firewall ou pelo grupo de segurança de rede. 

O URI inicial do portal é http (s):`<Portalserver`>: 5XX00/irj, em que a porta é formada como documentada pela SAP no <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Configuração do ponto de extremidade][planning-guide-figure-2800]

Se você quiser personalizar a URL e/ou portas do seu Enterprise Portal do SAP, consulte esta documentação:

* [Alterar URL do portal](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Alterar números de porta padrão, números de porta do portal](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>HA (alta disponibilidade) e recuperação de desastre (DR) para SAP NetWeaver em execução em máquinas virtuais do Azure

### <a name="definition-of-terminologies"></a>Definição de terminologias

O termo **alta disponibilidade (ha)** geralmente está relacionado a um conjunto de tecnologias que minimiza as interrupções de ti, fornecendo continuidade de negócios dos serviços de ti por meio de componentes redundantes, tolerantes a falhas ou protegidos por failover dentro do **mesmo** Data Center. Em nosso caso, em uma região do Azure.

A **recuperação de desastres (Dr)** também visa minimizar a interrupção dos serviços de ti e sua recuperação, mas em data centers **diferentes** , que geralmente estão localizados centenas de quilômetros de distância. Em nosso caso, geralmente entre diferentes regiões do Azure na mesma região geopolítica ou conforme estabelecido por você como um cliente.

### <a name="overview-of-high-availability"></a>Visão geral da alta disponibilidade

Podemos separar a discussão sobre a alta disponibilidade do SAP no Azure em duas partes:

* **Alta disponibilidade de infraestrutura do Azure**, por exemplo, ha de computação (VMS), rede, armazenamento etc. e seus benefícios para aumentar a disponibilidade do aplicativo SAP.
* **Alta disponibilidade de aplicativos SAP**, por exemplo, ha de componentes de software SAP:
  * Servidores de aplicativos SAP
  * Instância do SAP ASCS/SCS
  * Servidor de BD

e como ele pode ser combinado com a HA de infraestrutura do Azure.

A alta disponibilidade do SAP no Azure tem algumas diferenças em comparação com a alta disponibilidade do SAP em um ambiente físico ou virtual local. O documento a seguir do SAP descreve as configurações padrão de alta disponibilidade do SAP em ambientes virtualizados no Windows: <https://scn.sap.com/docs/DOC-44415>. Não há nenhuma configuração SAP-HA sapinst integrada para Linux como existe para o Windows. Sobre o SAP HA local para Linux encontre mais informações aqui: <https://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Alta disponibilidade de infraestrutura do Azure

Atualmente, há um SLA de VM única de 99,9%. Para ter uma ideia de como a disponibilidade de uma única VM pode ser parecida, você pode criar o produto dos diferentes SLAs do Azure disponíveis: <https://azure.microsoft.com/support/legal/sla/>.

A base para o cálculo é de 30 dias por mês ou de 43200 minutos. Portanto, o tempo de inatividade de 0, 5% corresponde a 21,6 minutos. Como de costume, a disponibilidade dos diferentes serviços será multiplicada da seguinte maneira:

(Serviço de disponibilidade #1/100) * (serviço de disponibilidade #2/100) * (serviço de disponibilidade #3/100) 

Similar

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 ou uma disponibilidade geral de 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Alta disponibilidade de VM (máquina virtual)

Há dois tipos de eventos da plataforma do Azure que podem afetar a disponibilidade de suas máquinas virtuais: manutenção planejada e manutenção não planejada.

* Os eventos de manutenção planejada são atualizações periódicas feitas pela Microsoft na plataforma subjacente do Azure para melhorar a confiabilidade, o desempenho e a segurança geral da infraestrutura da plataforma em que as máquinas virtuais são executadas.
* Os eventos de manutenção não planejados ocorrem quando o hardware ou a infraestrutura física subjacente à sua máquina virtual apresenta uma falha de alguma maneira. Isto pode incluir falhas de rede local, falhas de disco local ou outras falhas estruturais. Quando essa falha for detectada, a plataforma do Azure migrará automaticamente sua máquina virtual do servidor físico não íntegro que hospeda sua máquina virtual para um servidor físico íntegro. Estes eventos são raros, mas também podem provocar o reinício da máquina virtual.

Mais detalhes podem ser encontrados nesta documentação: <https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Redundância de armazenamento do Azure

Os dados em sua conta de Armazenamento do Microsoft Azure são sempre replicados para garantir durabilidade e alta disponibilidade, atendendo ao SLA do armazenamento do Azure mesmo diante de falhas transitórias de hardware.

Como o armazenamento do Azure está mantendo três imagens dos dados por padrão, RAID5 ou RAID1 em vários discos do Azure não são necessários.

Mais detalhes podem ser encontrados neste artigo: <https://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Utilizando a reinicialização de VM de infraestrutura do Azure para obter maior disponibilidade de aplicativos SAP

Se você decidir não usar funcionalidades como WSFC (Windows Server failover clustering) ou pacemaker no Linux (atualmente com suporte apenas para SLES 12 e superior), a reinicialização de VM do Azure será utilizada para proteger um sistema SAP contra tempo de inatividade planejado e não planejado do Azure infraestrutura de servidor físico e plataforma subjacente do Azure.

> [!NOTE]
> É importante mencionar que a reinicialização de VM do Azure protege principalmente as VMs e não os aplicativos. A reinicialização da VM não oferece alta disponibilidade para aplicativos SAP, mas oferece um certo nível de disponibilidade da infraestrutura e, portanto, uma disponibilidade indiretamente maior dos sistemas SAP. Também não há SLA para o tempo que levará para reiniciar uma VM após uma interrupção planejada ou não planejada do host. Portanto, esse método de alta disponibilidade não é adequado para componentes críticos de um sistema SAP como (A) SCS ou DBMS.
>
>

Outro importante elemento de infraestrutura para alta disponibilidade é o armazenamento. Por exemplo, o SLA de armazenamento do Azure é de 99,9% de disponibilidade. Se um implantar todas as VMs com seus discos em uma única conta de armazenamento do Azure, a indisponibilidade potencial do armazenamento do Azure causará indisponibilidade de todas as VMs que são colocadas na conta de armazenamento do Azure e também de todos os componentes SAP em execução nessas VMs.  

Em vez de colocar todas as VMs em uma única conta de armazenamento do Azure, você também pode usar contas de armazenamento dedicadas para cada VM e, dessa forma, aumentar a disponibilidade geral da VM e do aplicativo SAP usando várias contas de armazenamento do Azure independentes.

Os Managed Disks do Azure são colocados automaticamente no domínio de falha da máquina virtual à qual estão conectados. Se você colocar duas máquinas virtuais em um conjunto de disponibilidade e usar Managed Disks, a plataforma cuidará da distribuição dos Managed Disks em diferentes domínios de falha também. Se você planeja usar o armazenamento Premium, é altamente recomendável usar gerenciar discos também.

Uma arquitetura de exemplo de um sistema SAP NetWeaver que usa as contas de armazenamento e HA de infraestrutura do Azure pode ser parecida com esta:

![Utilizando a HA de infraestrutura do Azure para atingir a maior disponibilidade do aplicativo SAP][planning-guide-figure-2900]

Uma arquitetura de exemplo de um sistema SAP NetWeaver que usa HA e Managed Disks de infraestrutura do Azure poderia ser assim:

![Utilizando a HA de infraestrutura do Azure para atingir a maior disponibilidade do aplicativo SAP][planning-guide-figure-2901]

Para componentes críticos do SAP, atingimos o seguinte até agora:

* Alta disponibilidade de servidores de aplicativos SAP (AS)

  As instâncias do servidor de aplicativos SAP são componentes redundantes. Cada instância do SAP AS é implantada em sua própria VM, que está sendo executada em um domínio de atualização e de falha do Azure diferente (consulte os capítulos [domínios de falha][planning-guide-3.2.1] e [domínios de atualização][planning-guide-3.2.2]). Isso é garantido com o uso de conjuntos de disponibilidade do Azure (Confira o capítulo [conjuntos de disponibilidade do Azure][planning-guide-3.2.3]). A indisponibilidade potencial planejada ou não planejada de um domínio de falha ou de atualização do Azure causará indisponibilidade de um número restrito de VMs com seu SAP como instâncias.

  Cada instância do SAP AS é colocada em sua própria conta de armazenamento do Azure-a indisponibilidade potencial de uma conta de armazenamento do Azure causará indisponibilidade de apenas uma VM com sua instância SAP AS. No entanto, lembre-se de que há um limite de contas de armazenamento do Azure em uma assinatura do Azure. Para garantir o início automático da instância do (A) SCS após a reinicialização da VM, certifique-se de definir o parâmetro inicialização automática no perfil de início da instância do (A) SCS descrito no capítulo [usando a inicialização automática para instâncias SAP][planning-guide-11.5].
  Leia também o capítulo [alta disponibilidade para servidores de aplicativos SAP][planning-guide-11.4.1] para obter mais detalhes.

  Mesmo que você use Managed Disks, esses discos também são armazenados em uma conta de armazenamento do Azure e podem não estar disponíveis em caso de uma interrupção de armazenamento.

* *Mais alto* Disponibilidade da instância do SAP (A) SCS

  Aqui, utilizamos a reinicialização de VM do Azure para proteger a VM com a instância do SAP (A) SCS instalada. No caso de tempo de inatividade planejado ou não planejado de servidores do Azure, as VMs serão reiniciadas em outro servidor disponível. Como mencionado anteriormente, a reinicialização de VM do Azure protege principalmente as VMs e não os aplicativos, nesse caso, a instância (A) SCS. Por meio da reinicialização da VM, atingiremos uma disponibilidade indiretamente maior da instância do SAP (A) SCS. Para garantir o início automático da instância do (A) SCS após a reinicialização da VM, certifique-se de definir o parâmetro de inicialização automática no perfil de início da instância do (A) SCS descrito no capítulo [usando a inicialização automática para instâncias SAP][planning-guide-11.5]. Isso significa que a instância (A) SCS como um ponto único de falha (SPOF) em execução em uma única VM será o fator fator determinante para a disponibilidade de toda a estrutura SAP.

* *Mais alto* Disponibilidade do servidor DBMS

  Aqui, semelhante ao caso de uso da instância do SAP (A) SCS, utilizamos a reinicialização de VM do Azure para proteger a VM com o software DBMS instalado e atingimos uma maior disponibilidade do software DBMS por meio da reinicialização da VM.
  DBMS em execução em uma única VM também é um SPOF, e é o fator fator determinante para a disponibilidade de toda a estrutura do SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Alta disponibilidade do aplicativo SAP no Azure IaaS

Para obter alta disponibilidade completa do sistema SAP, precisamos proteger todos os componentes críticos do sistema SAP, por exemplo, servidores de aplicativos SAP redundantes e componentes exclusivos (por exemplo, ponto único de falha), como instância do SAP (A) SCS e DBMS.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Alta disponibilidade para servidores de aplicativos SAP

Para as instâncias de caixa de diálogo/servidores de aplicativos SAP, não é necessário pensar em uma solução específica de alta disponibilidade. A alta disponibilidade é obtida pela redundância e, portanto, com o suficiente em máquinas virtuais diferentes. Todos eles devem ser colocados no mesmo conjunto de disponibilidade do Azure para evitar que as VMs possam ser atualizadas ao mesmo tempo durante o período de inatividade da manutenção planejada. A funcionalidade básica, que se baseia em diferentes domínios de falha e atualização dentro de uma unidade de escala do Azure, já foi introduzida no capítulo [domínios de atualização][planning-guide-3.2.2]. Os conjuntos de disponibilidade do Azure foram apresentados no capítulo [conjuntos de disponibilidade do Azure][planning-guide-3.2.3] deste documento.

Não há número infinito de domínios de falha e de atualização que podem ser usados por um conjunto de disponibilidade do Azure em uma unidade de escala do Azure. Isso significa que colocar um número de VMs em um conjunto de disponibilidade, mais cedo ou mais tarde, mais de uma VM termina no mesmo domínio de falha ou de atualização.

Implantando algumas instâncias do servidor de aplicativos SAP em suas VMs dedicadas e supondo que tenhamos cinco domínios de atualização, a imagem a seguir surge no final. O número máximo real de domínios de falha e atualização dentro de um conjunto de disponibilidade pode mudar no futuro:

![HA de servidores de aplicativos SAP no Azure][planning-guide-figure-3000]

Mais detalhes podem ser encontrados nesta documentação: <https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Alta disponibilidade para serviços centrais do SAP no Azure

Para arquitetura de alta disponibilidade dos serviços centrais do SAP no Azure, verifique o artigo [arquitetura de alta disponibilidade e cenários do SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) como informações de entrada. O artigo aponta para descrições mais detalhadas para os sistemas operacionais específicos.

#### <a name="high-availability-for-the-sap-database-instance"></a>Alta disponibilidade para a instância do banco de dados SAP

A configuração típica de HA do SAP DBMS é baseada em duas VMs do DBMS em que a funcionalidade de alta disponibilidade do DBMS é usada para replicar dados da instância do DBMS ativa para a segunda VM em uma instância de DBMS passiva.

A funcionalidade de alta disponibilidade e recuperação de desastres para DBMS em geral, bem como DBMS específico, são descritas no [Guia de implantação do DBMS][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Alta disponibilidade de ponta a ponta para o sistema SAP completo

Aqui estão dois exemplos de uma arquitetura de alta disponibilidade do SAP NetWeaver completa no Azure – uma para Windows e outra para o Linux.

Somente discos não gerenciados: os conceitos, conforme explicado abaixo, podem precisar ser comprometidos um pouco quando você implanta muitos sistemas SAP e o número de VMs implantadas está excedendo o limite máximo de contas de armazenamento por assinatura. Nesses casos, os VHDs de VMs precisam ser combinados em uma conta de armazenamento. Normalmente, você faria isso combinando VHDs de VMs de camada de aplicativo SAP de diferentes sistemas SAP.  Também combinamos VHDs diferentes de diferentes VMs de DBMS de diferentes sistemas SAP em uma conta de armazenamento do Azure. Mantendo assim os limites de IOPS das contas de armazenamento do Azure em mente (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogo_windows-ha-on-windows"></a>![Windows][Logo_Windows] HA no Windows

![Arquitetura de alta disponibilidade de aplicativos SAP NetWeaver com SQL Server no Azure IaaS][planning-guide-figure-3200]

As seguintes construções do Azure são usadas para o sistema SAP NetWeaver, para minimizar o impacto por problemas de infraestrutura e aplicação de patch de host:

* O sistema completo é implantado no Azure (obrigatório-camada DBMS, instância (A) SCS e a camada de aplicativo completa precisa ser executada no mesmo local).
* O sistema completo é executado em uma assinatura do Azure (obrigatório).
* O sistema completo é executado em uma rede virtual do Azure (obrigatório).
* A separação das VMs de um sistema SAP em três conjuntos de disponibilidade é possível mesmo com todas as VMs que pertencem à mesma rede virtual.
* Cada camada (por exemplo, DBMS, ASCS, servidores de aplicativos) deve usar um conjunto de disponibilidade dedicado.
* Todas as VMs que executam instâncias de DBMS de um sistema SAP estão em um conjunto de disponibilidade. Supomos que há mais de uma VM executando instâncias de DBMS por sistema, já que recursos nativos de alta disponibilidade do DBMS são usados, como SQL Server AlwaysOn ou Oracle Data Guard.
* Todas as VMs que executam instâncias DBMS usam sua própria conta de armazenamento. Os arquivos de log e de dados do DBMS são replicados de uma conta de armazenamento para outra conta de armazenamento usando funções de alta disponibilidade do DBMS que sincronizam os dados. A indisponibilidade de uma conta de armazenamento causará indisponibilidade de um nó de cluster do Windows do SQL, mas não todo o serviço de SQL Server.
* Todas as VMs que executam A instância do (A) SCS de um sistema SAP estão em um conjunto de disponibilidade. Um WSFC (cluster de failover do Windows Server) é configurado dentro dessas VMs para proteger a instância do (A) SCS.
* Todas as VMs que executam as instâncias do (A) SCS usam sua própria conta de armazenamento. Um Os arquivos de instância do SCS e a pasta global SAP são replicados de uma conta de armazenamento para outra conta de armazenamento usando a replicação SIOS datakeeper. A indisponibilidade de uma conta de armazenamento causará indisponibilidade de um nó de cluster do Windows (A) SCS, mas não o serviço (A) SCS inteiro.
* TODAS as VMs que representam a camada do servidor de aplicativos SAP estão em um terceiro conjunto de disponibilidade.
* TODAS as VMs que executam servidores de aplicativos SAP usam sua própria conta de armazenamento. A indisponibilidade de uma conta de armazenamento causará indisponibilidade de um servidor de aplicativos SAP, no qual outros servidores de aplicativos SAP continuam a ser executados.

A figura a seguir ilustra o mesmo cenário usando Managed Disks.

![Arquitetura de alta disponibilidade de aplicativos SAP NetWeaver com SQL Server no Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogo_linux-ha-on-linux"></a>![Linux][Logo_Linux] HA no Linux

A arquitetura do SAP HA no Linux no Azure é basicamente a mesma do Windows, conforme descrito acima. Consulte a observação do SAP [1928533] para obter uma lista de soluções de alta disponibilidade com suporte.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Usando a inicialização automática para instâncias SAP

A SAP ofereceu a funcionalidade de iniciar instâncias SAP imediatamente após o início do sistema operacional dentro da VM. As etapas exatas foram documentadas no artigo [1909114]da base de dados de conhecimento SAP. No entanto, o SAP não recomenda usar mais a configuração porque não há nenhum controle na ordem de reinicializações de instância, supondo que mais de uma VM tenha sido afetada ou várias instâncias executadas por VM. Supondo um cenário típico do Azure de uma instância do servidor de aplicativos SAP em uma VM e o caso de uma única VM eventualmente sendo reiniciada, o AutoStart não é crítico e pode ser habilitado adicionando esse parâmetro:

    Autostart = 1

No perfil inicial da instância do SAP ABAP e/ou Java.

> [!NOTE]
> O parâmetro de inicialização automática também pode ter alguns desvantagens. Mais detalhadamente, o parâmetro dispara o início de uma instância do SAP ABAP ou Java quando o serviço Windows/Linux relacionado da instância é iniciado. Esse é certamente o caso quando o sistema operacional é inicializado. No entanto, reinicializações de serviços SAP também são uma coisa comum para a funcionalidade de gerenciamento do ciclo de vida de software SAP, como SUM ou outras atualizações ou atualizações. Essas funcionalidades não estão esperando que uma instância seja reiniciada automaticamente. Portanto, o parâmetro de inicialização automática deve ser desabilitado antes de executar essas tarefas. O parâmetro AutoStart também não deve ser usado para instâncias SAP que são clusterizadas, como ASCS/SCS/CI.
>
>

Consulte informações adicionais sobre a inicialização automática para instâncias SAP aqui:

* [Iniciar/parar SAP junto com o servidor Unix iniciar/parar](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Iniciando e parando os agentes de gerenciamento do SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Como habilitar o início automático do banco de dados HANA](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Sistemas SAP de 3 camadas maiores
Os aspectos de alta disponibilidade das configurações SAP de 3 camadas já foram discutidos nas seções anteriores. Mas e quanto aos sistemas em que os requisitos do servidor DBMS são muito grandes para tê-lo localizado no Azure, mas a camada de aplicativo SAP pode ser implantada no Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Local das configurações SAP de três camadas
Não há suporte para dividir a camada do aplicativo em si ou a camada do aplicativo e do DBMS entre o local e o Azure. Um sistema SAP é completamente implantado localmente ou no Azure. Também não há suporte para que alguns dos servidores de aplicativos sejam executados localmente e outros no Azure. Esse é o ponto de partida da discussão. Também não estamos dando suporte para que os componentes do DBMS de um sistema SAP e a camada do servidor de aplicativos SAP sejam implantados em duas regiões diferentes do Azure. Por exemplo, DBMS no oeste dos EUA e na camada de aplicativo SAP em EUA Central. O motivo para não dar suporte a essas configurações é a sensibilidade de latência da arquitetura do SAP NetWeaver.

No entanto, no decorrer do ano passado data center parceiros desenvolveram colocalidades para regiões do Azure. Essas colocalizações costumam estar perto dos data centers físicos do Azure em uma região do Azure. A curta distância e a conexão dos ativos na colocalização por meio do ExpressRoute no Azure podem resultar em uma latência menor que 2 ms. Nesses casos, para localizar a camada de DBMS (incluindo SAN/NAS de armazenamento) em tal local e a camada de aplicativo SAP no Azure é possível. [Instâncias grandes do Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>Backup offline de sistemas SAP
Dependendo da configuração do SAP escolhida (2 ou 3 camadas), pode ser necessário fazer backup. O conteúdo da VM em si mais para ter um backup do banco de dados. Os backups relacionados ao DBMS devem ser feitos com métodos de banco de dados. Uma descrição detalhada para os diferentes bancos de dados pode ser encontrada no [Guia do DBMS][dbms-guide]. Por outro lado, os dados do SAP podem ser submetidos a backup de maneira offline (incluindo o conteúdo do banco de dado também), conforme descrito nesta seção ou online, conforme descrito na próxima seção.

O backup offline requer basicamente um desligamento da VM por meio do portal do Azure e uma cópia do disco da VM de base, além de todos os discos anexados para a VM. Isso preservaria uma imagem pontual da VM e de seu disco associado. É recomendável copiar os backups em uma conta de armazenamento do Azure diferente. Portanto, o procedimento descrito no capítulo [copiando discos entre as contas de armazenamento do Azure][planning-guide-5.4.2] deste documento será aplicável.
Além do desligamento usando o portal do Azure um também pode fazer isso por meio do PowerShell ou da CLI, conforme descrito aqui: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Uma restauração desse Estado consistiria em excluir a VM base, bem como os discos originais da VM de base e discos montados, copiar os discos salvos para a conta de armazenamento original ou o grupo de recursos para discos gerenciados e, em seguida, reimplantar o sistema.
Este artigo mostra um exemplo de como gerar um script desse processo no PowerShell: <http://www.westerndevs.com/azure-snapshots/>

Certifique-se de instalar uma nova licença do SAP, pois a restauração de um backup de VM, conforme descrito acima, cria uma nova chave de hardware.

### <a name="online-backup-of-an-sap-system"></a>Backup online de um sistema SAP

O backup do DBMS é executado com métodos específicos do DBMS, conforme descrito no [Guia do DBMS][dbms-guide].

É possível fazer backup de outras VMs no sistema SAP usando a funcionalidade de backup de máquina virtual do Azure. O backup de máquina virtual do Azure é um método padrão para fazer backup de uma VM completa no Azure. O backup do Azure armazena os backups no Azure e permite uma restauração de uma VM novamente.

> [!NOTE]
> A partir de Dec 2015 usando o backup da VM não mantém a ID da VM exclusiva que é usada para o licenciamento do SAP. Isso significa que uma restauração de um backup de VM requer a instalação de uma nova chave de licença SAP, pois a VM restaurada é considerada uma nova VM e não uma substituição da anterior que foi salva.
>
> ![Windows][Logo_Windows] Windows
>
> Teoricamente, as VMs que executam bancos de dados podem ser submetidas a backup de maneira consistente também se o sistema DBMS der suporte ao VSS do Windows (Serviço de Cópias de Sombra de Volume <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) como, por exemplo, SQL Server.
> No entanto, lembre-se de que, com base em backups de VM do Azure, não são possíveis restaurações pontuais de bancos de dados. Portanto, a recomendação é executar backups de bancos de dados com a funcionalidade do DBMS em vez de depender do backup de VM do Azure.
>
> Para se familiarizar com o backup da máquina virtual do Azure, comece aqui: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Outras possibilidades são usar uma combinação do Microsoft Data Protection Manager instalado em uma VM do Azure e o backup do Azure para bancos de dados de backup/restauração. Mais informações podem ser encontradas aqui: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Não há nenhum equivalente ao VSS do Windows no Linux. Portanto, somente backups consistentes com o arquivo são possíveis, mas não backups consistentes com o aplicativo. O backup do DBMS do SAP deve ser feito usando a funcionalidade do DBMS. O sistema de arquivos que inclui os dados relacionados ao SAP pode ser salvo, por exemplo, usando o tar, conforme descrito aqui: <https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure como site de recuperação de desastre para cenários SAP de produção

Desde meados de 2014, as extensões para vários componentes em relação ao Hyper-V, ao System Center e ao Azure permitem o uso do Azure como site de DR para VMs em execução no local com base no Hyper-V.

Um blog detalhando como implantar essa solução está documentado aqui: <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Resumo

Os principais pontos de alta disponibilidade para sistemas SAP no Azure são:

* Nesse momento, o ponto único de falha do SAP não pode ser protegido exatamente da mesma forma que pode ser feito em implantações locais. O motivo é que os clusters de discos compartilhados ainda não podem ser criados no Azure sem o uso de software de terceiros.
* Para a camada DBMS, você precisa usar a funcionalidade do DBMS que não depende da tecnologia de cluster de disco compartilhado. Os detalhes estão documentados no [Guia do DBMS][dbms-guide].
* Para minimizar o impacto dos problemas nos domínios de falha na infraestrutura do Azure ou na manutenção do host, você deve usar os conjuntos de disponibilidade do Azure:
  * É recomendável ter um conjunto de disponibilidade para a camada de aplicativo SAP.
  * É recomendável ter um conjunto de disponibilidade separado para a camada do DBMS do SAP.
  * Não é recomendável aplicar o mesmo conjunto de disponibilidade para VMs de diferentes sistemas SAP.
  * É recomendável usar Managed Disks Premium.
* Para fins de backup da camada do DBMS do SAP, verifique o [Guia do DBMS][dbms-guide].
* O backup de instâncias de caixa de diálogo do SAP faz pouco sentido, pois geralmente é mais rápido reimplantar instâncias de diálogo simples.
* Fazer backup da VM que contém o diretório global do sistema SAP e com ele todos os perfis das diferentes instâncias faz sentido e deve ser executado com o backup do Windows ou, por exemplo, tar no Linux. Como há diferenças entre o Windows Server 2008 (R2) e o Windows Server 2012 (R2), que facilitam o backup usando as versões mais recentes do Windows Server, recomendamos a execução do Windows Server 2012 (R2) como sistema operacional convidado do Windows.

## <a name="next-steps"></a>Passos seguintes
Leia os artigos:

- [Implantação de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP HANA configurações e operações de infraestrutura no Azure] (https://docs.microsoft.com/
- Azure/Virtual-Machines/cargas de trabalho/SAP/Hana-VM-Operations)
