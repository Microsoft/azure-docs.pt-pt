---
title: 'SAP em Azure: Guia de Planeamento e Implementação'
description: Planeamento e implementação de Máquinas Virtuais Azure para SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c2e3219cebcc5e989059c02fec86ba242e1c31cc
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853870"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Planeamento e implementação de Máquinas Virtuais Azure para SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
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
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

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
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
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
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
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



O Microsoft Azure permite que as empresas adquiram recursos de computação e armazenamento em tempo mínimo sem longos ciclos de aquisição. O serviço Azure Virtual Machine permite que as empresas implementem aplicações clássicas, como aplicações baseadas em SAP NetWeaver no Azure e aumentem a sua fiabilidade e disponibilidade sem terem mais recursos disponíveis no local. A Azure Virtual Machine Services também suporta a conectividade transversal, que permite às empresas integrarem ativamente as Máquinas Virtuais Azure nos seus domínios no local, nas suas Nuvens Privadas e na sua Paisagem do Sistema SAP.
Este livro branco descreve os fundamentos da Máquina Virtual Microsoft Azure e fornece um walk-through de considerações de planeamento e implementação para instalações SAP NetWeaver em Azure e, como tal, deve ser o documento a ler antes de iniciar as implementações reais do SAP NetWeaver no Azure.
O documento complementa a Documentação de Instalação SAP e as Notas SAP, que representam os principais recursos para instalações e implementações de software SAP em determinadas plataformas.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Resumo
A Cloud Computing é um termo amplamente utilizado, que está a ganhar cada vez mais importância no âmbito da indústria informática, desde pequenas empresas até grandes e multinacionais.

O Microsoft Azure é a Plataforma de Serviços cloud da Microsoft, que oferece um vasto espectro de novas possibilidades. Agora, os clientes são capazes de fornecer e desprovisionar rapidamente aplicações como um serviço na nuvem, pelo que não se limitam a restrições técnicas ou orçamentais. Em vez de investir tempo e orçamento em infraestruturas de hardware, as empresas podem focar-se na aplicação, processos de negócio e seus benefícios para clientes e utilizadores.

Com os Serviços da Máquina Virtual do Microsoft Azure, a Microsoft oferece uma plataforma completa de Infraestrutura como um Serviço (IaaS). As aplicações baseadas em SAP NetWeaver são suportadas em Máquinas Virtuais do Azure (IaaS). Este livro branco descreve como planear e implementar aplicações baseadas em SAP NetWeaver dentro do Microsoft Azure como a plataforma de eleição.

O próprio trabalho centra-se em dois aspetos principais:

* A primeira parte descreve dois padrões de implementação suportados para aplicações baseadas em SAP NetWeaver no Azure. Também descreve o manuseamento geral do Azure com as implementações sap em mente.
* A segunda parte detalha a implementação dos diferentes cenários descritos na primeira parte.

Para obter recursos adicionais, consulte o capítulo [Recursos][planning-guide-1.2] neste documento.

### <a name="definitions-upfront"></a>Definições antecipadas
Ao longo do documento, utilizamos os seguintes termos:

* IaaS: Infraestruturas como serviço
* PaaS: Plataforma como serviço
* SaaS: Software como serviço
* Componente SAP: uma aplicação SAP individual como ECC, BW, Solution Manager ou S/4HANA.  Os componentes SAP podem basear-se nas tecnologias tradicionais ABAP ou Java ou numa aplicação não baseada em NetWeaver, como Objetos Empresariais.
* Ambiente SAP: um ou mais componentes SAP agrupados logicamente para desempenhar uma função de negócio como Desenvolvimento, QAS, Formação, DR ou Produção.
* Paisagem SAP: Este termo refere-se a todos os ativos SAP na paisagem de TI de um cliente. A paisagem SAP inclui todos os ambientes de produção e não produção.
* Sistema SAP: A combinação da camada DBMS e da camada de aplicação de, por exemplo, um sistema de desenvolvimento SAP ERP, sistema de teste SAP BW, sistema de produção de CRM SAP, etc. Nas implantações do Azure, não é suportado para dividir estas duas camadas entre as instalações e o Azure. Significa que um sistema SAP é implantado no local ou é implantado em Azure. No entanto, pode implantar os diferentes sistemas de uma paisagem SAP em Azure ou no local. Por exemplo, poderia implantar os sistemas de desenvolvimento e teste de CRM SAP em Azure, mas o sistema de produção de CRM SAP no local.
* Cross-premises ou híbrido: Descreve um cenário em que os VMs são implantados para uma subscrição Azure que tem conectividade site-a-site, multi-site ou ExpressRoute entre os datacenters no local e O Azure. Em documentação comum do Azure, este tipo de implantações também são descritos como cenários transversais ou híbridos. A razão da ligação é o alargamento dos domínios no local, no local Ative Directory/OpenLDAP e no local DNS para Azure. A paisagem no local é alargada aos ativos do Azure da subscrição. Tendo esta extensão, os VMs podem fazer parte do domínio no local. Os utilizadores de domínio do domínio no local podem aceder aos servidores e podem executar serviços nesses VMs (como serviços DBMS). A resolução de comunicação e nome entre VMs implantados no local e VMs implantados em Azure é possível. Este é o caso mais comum e quase exclusivo que implanta ativos DaAp no Azure. Para mais informações, consulte [este][vpn-gateway-cross-premises-options] artigo e [este][vpn-gateway-site-to-site-create].
* Extensão de monitorização Azure, Monitorização Melhorada e Extensão Azure para SAP: Descreva um e o mesmo item. Descreve uma extensão VM que precisa de ser implementada por si para fornecer alguns dados básicos sobre a infraestrutura Azure ao Agente Anfitrião SAP. As notas SAP em Notas SAP podem referir-se a ele como Extensão de Monitorização ou Monitorização Melhorada. Em Azure, referimo-nos a ela como **extensão Azure para SAP.**

> [!NOTE]
> As instalações cruzadas ou as implantações híbridas dos sistemas SAP onde as Máquinas Virtuais Azure que executam sistemas SAP são membros de um domínio no local são suportadas para sistemas SAP de produção. As configurações transversais ou híbridas são suportadas para implantar peças ou paisagens Completas de SAP em Azure. Mesmo executando a paisagem Completa sap em Azure requer que esses VMs façam parte do domínio no local e ADS/OpenLDAP. 
>
>



### <a name="resources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Recursos
O ponto de entrada para a carga de trabalho sap na documentação azure é encontrado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). A partir deste ponto de entrada encontra-se muitos artigos que cobrem os tópicos de:

- SAP NetWeaver e Business One em Azure
- Guias SAP DBMS para vários sistemas DBMS em Azure
- Elevada disponibilidade e recuperação de desastres para carga de trabalho SAP em Azure
- Orientação específica para executar SAP HANA em Azure
- Orientação específica para As Grandes Instâncias de Azure HANA para o DBMS SAP HANA 


> [!IMPORTANT]
> Sempre que possível, é utilizado um link para os guias de instalação SAP <http://service.sap.com/instguides>referm ou outra documentação SAP (Reference InstGuide-01, ver). No que diz respeito aos pré-requisitos, processo de instalação ou detalhes da funcionalidade SAP específica, a documentação e os guias SAP devem ser sempre lidos atentamente, uma vez que os documentos da Microsoft apenas cobrem tarefas específicas para software SAP instalado e operado numa Máquina Virtual Microsoft Azure.
>
>

As seguintes Notas SAP estão relacionadas com o tema da SAP no Azure:

| Número de nota | Título |
| --- | --- |
| [1928533] |Aplicações SAP em Azure: Produtos e Dimensionamento SAP |
| [2015553] |SAP no Microsoft Azure: Apoiar pré-requisitos |
| [1999351] |Resolução de problemas Monitorização Avançada do Azure para SAP |
| [2178632] |Principais métricas de monitorização para SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: Monitorização melhorada |
| [2191498] |SAP em Linux com Azure: Monitorização reforçada |
| [2243692] |Linux no Microsoft Azure (IaaS) VM: Problemas de licença SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalação e upgrade |
| [2069760] |Instalação e upgrade do Oracle Linux 7.x SAP |
| [1597355] |Recomendação de espaço de troca para Linux |

Leia também o [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contém todas as notas SAP para Linux.

No [presente artigo][azure-resource-manager/management/azure-subscription-service-limits-subscription]podem ser encontradas limitações gerais de incumprimento e limitações máximas das assinaturas Do Azure.

## <a name="possible-scenarios"></a>Cenários possíveis
O SAP é muitas vezes visto como uma das aplicações mais críticas da missão dentro das empresas. A arquitetura e operações destas aplicações é maioritariamente complexa e garantir que cumpre os requisitos em termos de disponibilidade e desempenho é importante.

Assim, as empresas têm de pensar cuidadosamente em que fornecedor de nuvem escolher para executar tais processos de negócio críticos do negócio. O Azure é a plataforma pública de nuvem pública ideal para aplicações sap críticas de negócios e processos de negócio. Dada a grande variedade de infraestruturas Azure, quase todos os sistemas SAP NetWeaver existentes, e os sistemas S/4HANA podem ser hospedados hoje em Azure. Azure fornece VMs com muitos Terabytes de memória e mais de 200 CPUs. Além disso, o Azure oferece [grandes instâncias HANA,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)que permitem implantações de HANA de até 24TB e implantações de escala SAP HANA até 120TB. Pode-se afirmar hoje que quase todos os cenários SAP no local também podem ser executados em Azure. 

Para uma descrição aproximada dos cenários e alguns cenários não suportados, consulte o documento [de carga de trabalho SAP em cenários suportados por máquinas virtuais Azure](./sap-planning-supported-configurations.md).

Confira estes cenários e algumas das condições que foram apontadas como não suportadas na documentação referenciada ao longo do planeamento e desenvolvimento da sua arquitetura que pretende implementar no Azure.

No geral, o padrão de implantação mais comum é um cenário transversal como exibido

![VPN com Conectividade Site-A-Local (cross-premises)][planning-guide-figure-300]

A razão para muitos clientes aplicarem um padrão de implantação transversal é o facto de ser mais transparente para todas as aplicações estender em instalações para Azure usando o Azure ExpressRoute e tratar o Azure como centro de dados virtual. À medida que cada vez mais ativos estão a ser transferidos para o Azure, as infraestruturas e infraestruturas de rede implantadas do Azure irão crescer e os ativos no local reduzir-se-ão em conformidade. Tudo transparente para utilizadores e aplicações.

A fim de implantar com sucesso sistemas SAP em iaaS ou IaaS em geral, é importante compreender as diferenças significativas entre as ofertas de outsourcing seletivas tradicionais ou de hospedas e ofertas IaaS. Enquanto o anfitrião tradicional ou outsourcing adapta a infraestrutura (rede, armazenamento e tipo de servidor) à carga de trabalho que um cliente quer hospedar, é responsabilidade do cliente ou parceiro caracterizar a carga de trabalho e escolher os componentes Azure corretos de VMs, armazenamento e rede para implementações IaaS.

Para recolher dados para o planeamento da sua implantação no Azure, é importante:

- Avaliar quais os produtos SAP suportados em execução em VMs Azure
- Avaliar quais as versões específicas do Sistema Operativo suportadas com VMs Azure específicos para esses produtos SAP
- Avalie quais os lançamentos do DBMS suportados para os seus produtos SAP com VMs Azure específicos
- Avalie se algumas das versões DE OS/DBMS necessárias exigem que execute a versão SAP, a atualização do Pacote de Suporte e as atualizações do kernel para chegar a uma configuração suportada
- Avalie se precisa de se deslocar para diferentes sistemas operativos para implantar no Azure.

Os detalhes sobre componentes SAP suportados no Azure, unidades de infraestrutura azure suportadas e lançamentos de sistemas operativos relacionados e lançamentos dbmS são explicados no artigo [O software SAP é suportado para implementações Azure.](./sap-supported-product-on-azure.md) Os resultados obtidos com a avaliação de lançamentos sap válidos, sistema operativo e lançamentos DBMS têm um grande impacto nos esforços de deslocação dos sistemas SAP para o Azure. Os resultados desta avaliação vão definir se poderão existir esforços significativos de preparação nos casos em que são necessárias atualizações ou alterações dos sistemas operativos.


## <a name="azure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiões do Azure
Os serviços Azure da Microsoft são recolhidos nas regiões de Azure. Uma região do Azure é uma ou uma recolha de datacenters que contêm o hardware e infraestrutura que executa e acolhe os diferentes serviços Azure. Esta infraestrutura inclui um grande número de nós que funcionam como nós de computação ou nós de armazenamento, ou executar a funcionalidade da rede. 

Para obter uma lista das diferentes regiões do Azure, consulte o artigo [Geografias Azure.](https://azure.microsoft.com/global-infrastructure/geographies/) Nem todas as regiões de Azure oferecem os mesmos serviços. Dependendo do produto SAP que pretende executar, e do sistema operativo e do DBMS relacionados com ele, pode acabar numa situação em que uma determinada região não oferece os tipos de VM que necessita. Isto é especialmente verdade para executar SAP HANA, onde você geralmente precisa de VMs da série VM M/Mv2. Estas famílias vm são implantadas apenas num subconjunto das regiões. Você pode descobrir que VM exato, tipos, tipos de armazenamento Azure ou, outros Serviços Azure estão disponíveis em qual das regiões com a ajuda do site [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/) À medida que inicia o seu planeamento e tem certas regiões em mente como região primária e eventualmente região secundária, precisa de investigar primeiro se os serviços necessários estão disponíveis nessas regiões. 

### <a name="availability-zones"></a>Zonas de Disponibilidade
Várias regiões de Azure implementaram um conceito chamado Zonas de Disponibilidade. As Zonas de Disponibilidade são locais fisicamente separados dentro de uma região de Azure. Cada Zona de Disponibilidade é constituída por um ou mais datacenters equipados com energia, refrigeração e redes independentes. Por exemplo, implementar dois VMs em duas Zonas de Disponibilidade de Azure, e implementar um quadro de alta disponibilidade para o seu sistema DBMS SAP ou o SAP Central Services dá-lhe o melhor SLA em Azure. Para esta máquina virtual em particular SLA em Azure, consulte a versão mais recente de [Virtual Machine SLAs](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Uma vez que as regiões de Azure se desenvolveram e se alargaram rapidamente ao longo dos últimos anos, a topologia das regiões de Azure, o número de datacenters físicos, a distância entre esses centros de dados, e a distância entre as Zonas de Disponibilidade do Azure podem ser diferentes. E com isso a latência da rede.

O princípio das Zonas de Disponibilidade não se aplica ao serviço específico HANA das [Grandes Instâncias HANA.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) Os acordos de nível de serviço para as grandes instâncias hana podem ser encontrados no artigo [SLA para SAP HANA em Grandes Instâncias Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/) 


### <a name="fault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domínios de Falhas
Os Domínios de Falha representam uma unidade física de falha, intimamente relacionada com a infraestrutura física contida nos centros de dados, e enquanto uma lâmina física ou cremalheira pode ser considerada um Domínio de Falha, não existe um mapeamento direto entre os dois.

Ao implementar várias Máquinas Virtuais como parte de um sistema SAP nos Serviços de Máquinas Virtuais Microsoft Azure, pode influenciar o Controlador de Tecido SI a implementar a sua aplicação em diferentes Domínios de Falha, cumprindo assim requisitos mais elevados de disponibilidade de SLAs. No entanto, a distribuição de Domínios de Falha sobre uma Unidade de Escala Azure (coleção de centenas de nós computacionais ou de armazenamento e networking) ou a atribuição de VMs a um domínio de falha específico é algo sobre o qual não tem controlo direto. Para direcionar o controlador de tecido Azure para implantar um conjunto de VMs sobre diferentes Domínios de Falha, é necessário atribuir um conjunto de disponibilidade azure aos VMs no momento da implantação. Para obter mais informações sobre os conjuntos de disponibilidade do Azure, consulte os conjuntos de disponibilidade do capítulo [Azure][planning-guide-3.2.3] neste documento.


### <a name="upgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Domínios de upgrade
Os Domínios de Upgrade representam uma unidade lógica que ajuda a determinar como é que um VM dentro de um sistema SAP, que consiste em instâncias SAP em execução em vários VMs, é atualizado. Quando ocorre uma atualização, o Microsoft Azure passa pelo processo de atualização destes Domínios de Upgrade, um a um. Ao espalhar VMs no tempo de implementação através de diferentes Domínios de Upgrade, pode proteger o seu sistema SAP em parte do tempo de paragem potencial. Para forçar o Azure a implantar os VMs de um sistema SAP distribuído por diferentes Domínios de Upgrade, é necessário definir um atributo específico no momento de implantação de cada VM. Semelhante aos Domínios de Falha, uma Unidade de Escala Azure está dividida em vários Domínios de Upgrade. Para direcionar o controlador de tecido Azure para implantar um conjunto de VMs em diferentes Domínios de Upgrade, é necessário atribuir um Conjunto de Disponibilidade Azure aos VMs no momento da implantação. Para obter mais informações sobre os conjuntos de disponibilidade do Azure, consulte os conjuntos de disponibilidade do capítulo [Azure][planning-guide-3.2.3] abaixo.


### <a name="azure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Conjuntos de disponibilidade azure
As máquinas virtuais Azure dentro de um conjunto de disponibilidade Azure são distribuídas pelo Controlador de Tecido Sino Azul sobre diferentes Domínios de Avaria e Upgrade. O objetivo da distribuição sobre diferentes Domínios de Falha e Upgrade é evitar que todos os VMs de um sistema SAP sejam desligados em caso de manutenção da infraestrutura ou uma falha dentro de um domínio de falha. Por padrão, os VMs não fazem parte de um conjunto de disponibilidade. A participação de um VM num conjunto de disponibilidade é definida no momento de implantação ou posteriormente por uma reconfiguração e redistribuição de um VM.

Para compreender o conceito de conjuntos de disponibilidade do Azure e a forma como os conjuntos de disponibilidade se relacionam com domínios de falha e upgrade, leia [este artigo][virtual-machines-manage-availability]. 

À medida que define conjuntos de disponibilidade e tenta misturar vários VMs de diferentes famílias vM dentro de um conjunto de disponibilidade, poderá encontrar problemas que o impeçam de incluir um certo tipo de VM em tal conjunto de disponibilidade. A razão é que o conjunto de disponibilidade é ligado à unidade de escala que contém um certo tipo de anfitriões de computação. E um certo tipo de hospedeiro de computação só pode gerir certos tipos de famílias VM. Por exemplo, se criar um conjunto de disponibilidade e implementar o primeiro VM no conjunto de disponibilidade e escolher um tipo VM da família Esv3 e, em seguida, tentar implementar como segundo VM um VM da família M, você será rejeitado na segunda alocação. A razão é que os VMs da família Esv3 não estão a funcionar no mesmo hardware de hospedeiro que as máquinas virtuais da família M fazem. O mesmo problema pode ocorrer, quando se tenta redimensionar os VMs e tentar transferir um VM da família Esv3 para um tipo VM da família M. No caso de redimensionar para uma família VM que não possa ser hospedada no mesmo hardware de hospedagem, você precisa desligar todos os VMs no seu conjunto de disponibilidade e redimensioná-los para ser capaz de executar no outro tipo de máquina anfitrião. Para SLAs de VMs que estão implantados dentro do conjunto de disponibilidade, consulte o artigo [Virtual Machine SLAs](https://azure.microsoft.com/support/legal/sla/virtual-machines/). 

O princípio do conjunto de disponibilidade e o domínio relacionado de atualização e falha não se aplicam ao serviço específico HANA de [Grandes Instâncias HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Os acordos de nível de serviço para as grandes instâncias hana podem ser encontrados no artigo [SLA para SAP HANA em Grandes Instâncias Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/). 

> [!IMPORTANT]
> Os conceitos de Zonas de Disponibilidade Azure e conjuntos de disponibilidade Azure são mutuamente exclusivos. Isto significa que pode implantar um par ou vários VMs numa Zona de Disponibilidade específica ou num conjunto de disponibilidade azure. Mas não os dois.


## <a name="azure-virtual-machine-services"></a>Serviços de máquinas virtuais Azure
O Azure oferece uma grande variedade de máquinas virtuais que pode selecionar para implementar. Não há necessidade de compras de tecnologia e infraestruturas antecipadas. O serviço Azure VM oferece simplifica aplicações de manutenção e operação, fornecendo computação e armazenamento a pedido para hospedar, escalar e gerir aplicações web e aplicações conectadas. A gestão de infraestruturas é automatizada com uma plataforma que é projetada para alta disponibilidade e escaladinâmica para combinar com as necessidades de utilização com a opção de vários modelos de preços diferentes.

![Posicionamento dos Serviços de Máquinas Virtuais microsoft Azure][planning-guide-figure-400]

Com máquinas virtuais Azure, a Microsoft está a permitir-lhe implementar imagens personalizadas do servidor para o Azure, conforme o IaaS. Ou pode escolher entre uma rica seleção de imagens consumíveis do sistema operativo a partir da galeria de imagens Azure.

Do ponto de vista operacional, o Serviço de Máquinas Virtuais Azure oferece experiências semelhantes às máquinas virtuais implantadas nas instalações. É responsável pela administração, operações e também pela correção do sistema operativo específico, funcionando num VM Azure e nas suas aplicações nesse VM. A Microsoft não está a prestar mais serviços para além de hospedar aquele VM na sua infraestrutura Azure (Infraestrutura como Serviço - IaaS). Para a carga de trabalho da SAP que você, como um cliente implementa, a Microsoft não tem ofertas além das ofertas IaaS. 

A plataforma Microsoft Azure é uma plataforma multi-inquilinos. Como resultado, o armazenamento, rede e recursos de computação que acolhem VMs Azure são, com algumas exceções, partilhados entre inquilinos. A lógica inteligente de estrangulamento e quota é usada para evitar que um inquilino impacte o desempenho de outro inquilino (vizinho barulhento) de forma drástica. Especialmente para certificar a plataforma Azure para o SAP HANA, a Microsoft precisa de provar o isolamento de recursos para casos em que vários VMs podem funcionar regularmente no mesmo anfitrião para o SAP. Embora a lógica em Azure tente manter variações na largura de banda experimentada pequenas plataformas altamente partilhadas tendem a introduzir maiores variações na disponibilidade de recursos/largura de banda do que os clientes podem experimentar nas suas implementações no local. A probabilidade de um sistema SAP em Azure poder sofrer variações maiores do que num sistema no local deve ser tida em conta.

### <a name="azure-virtual-machines-for-sap-workload"></a>Máquinas virtuais Azure para carga de trabalho SAP

Para a carga de trabalho sap, reduzimos a seleção para diferentes famílias VM que são adequadas para a carga de trabalho SAP e carga de trabalho SAP HANA mais especificamente. A forma como encontra o tipo VM correto e a sua capacidade de trabalhar através da carga de trabalho SAP é descrita no documento [O software SAP é suportado para implementações Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure). 

> [!NOTE]
> Os tipos vM certificados para carga de trabalho SAP, não há um excesso de fornecimento de CPU e recursos de memória.

Para além da seleção de VMs puramente suportados, você também precisa verificar se esses estão disponíveis em uma região específica com base no site [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Mas, mais importante, é preciso avaliar se:

- CPU e recursos de memória de diferentes tipos de VM 
- Largura de banda IOPS de diferentes tipos de VM
- Capacidades de rede de diferentes tipos de VM
- Número de discos que podem ser anexados
- Capacidade de alavancar certos tipos de armazenamento Azure

se adequa à sua necessidade. A maioria desses dados pode ser encontrada [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows] para um tipo de VM particular.

Como modelo de preços, você tem várias opções de preços diferentes que listam como:

- Pay as you go
- Um ano reservado
- Três anos reservados
- Preços à vista

O preço de cada uma das diferentes ofertas com diferentes ofertas de serviço em torno de sistemas operativos e diferentes regiões está disponível no site [Linux Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter detalhes e flexibilidade de um ano e três anos de instâncias reservadas, consulte estes artigos:

- [O que é o Azure Reservations?](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)
- [Flexibilidade de tamanho da máquina virtual com o Reserved VM Instances](https://docs.microsoft.com/azure/virtual-machines/windows/reserved-vm-instance-size-flexibility)
- [Como o desconto de reserva do Azure é aplicado a máquinas virtuais](https://docs.microsoft.com/azure/cost-management-billing/manage/understand-vm-reservation-charges) 

Para mais informações sobre os preços no local, leia o artigo [Máquinas Virtuais Do Spot.](https://azure.microsoft.com/pricing/spot/) Os preços do mesmo tipo VM também podem ser diferentes entre diferentes regiões azure. Para alguns clientes, valia a pena implantar-se numa região azure mais barata.

Além disso, o Azure oferece os conceitos de um anfitrião dedicado. O conceito dedicado do anfitrião dá-lhe mais controlo sobre os ciclos de remendos que são feitos pelo Azure. Pode cronometrar o remendo de acordo com os seus próprios horários. Esta oferta destina-se especificamente a clientes com carga de trabalho que podem não seguir o ciclo normal de carga de trabalho. Para ler os conceitos de ofertas dedicadas ao anfitrião azure, leia o artigo [Azure Dedicado Host.](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts) A utilização desta oferta é suportada para carga de trabalho SAP e é usada por vários clientes SAP que querem ter mais controlo sobre a remensão da infraestrutura e eventuais planos de manutenção da Microsoft. Para obter mais informações sobre como a Microsoft mantém e remende a infraestrutura Azure que acolhe máquinas virtuais, leia o artigo [Manutenção de máquinas virtuais em Azure](https://docs.microsoft.com/azure/virtual-machines/maintenance-and-updates).

#### <a name="generation-1-and-generation-2-virtual-machines"></a>Máquinas virtuais geração 1 e geração 2
O hipervisor da Microsoft é capaz de lidar com duas gerações diferentes de máquinas virtuais. Estes formatos são chamados **Geração 1** e **Geração 2**. **A Geração 2** foi introduzida no ano de 2012 com o hipervisor Windows Server 2012. O Azure começou a usar máquinas virtuais da Geração 1. À medida que implanta máquinas virtuais Azure, o padrão ainda é utilizar o formato Geração 1. Entretanto, também pode supor formatos VM da Geração 2. O artigo [Apoio à geração 2 VMs no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) lista as famílias Azure VM que podem ser implantadas como Geração 2 VM. Este artigo também enumera as diferenças funcionais muito importantes das máquinas virtuais da Geração 2, uma vez que podem funcionar na nuvem privada Hyper-V e azure. Mais importante ainda este artigo também enumera diferenças funcionais entre máquinas virtuais geração 1 e VMs geração 2, como as que funcionam em Azure. 

> [!NOTE]
> Existem diferenças funcionais de Geração 1 e Geração 2 VMs em execução em Azure. Leia o artigo Apoio para os [VMs da geração 2 no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) para ver uma lista dessas diferenças.  
 
Mover um VM existente de uma geração para outra geração não é possível. Para mudar a geração de máquinas virtuais, precisa de implementar um novo VM da geração que deseja e reinstalar o software que está a executar na máquina virtual da geração. Isto apenas afeta a imagem vHD base do VM e não tem qualquer impacto nos discos de dados ou nas ações NFS ou SMB anexadas. Discos de dados, nFS ou ações SMB que foram originalmente atribuídas, por exemplo, numa Geração 1 VM

> [!NOTE]
> A implantação de VMs da família Mv1 VM como VMs geração 2 é possível a partir de maio de 2020. Com isso é possível um aparente menor de sizing entre os VMs da família Mv1 e Mv2.


#### <a name="quotas-in-azure-virtual-machine-services"></a>Quotas em Serviços de Máquinas Virtuais Azure
A infraestrutura de armazenamento e rede Azure é partilhada entre VMs que executam uma variedade de serviços na infraestrutura Azure. E tal como nos seus próprios centros de dados, o excesso de fornecimento de alguns dos recursos de infraestrutura ocorre até certo ponto. A Plataforma Microsoft Azure utiliza discos, CPU, rede e outras quotas para limitar o consumo de recursos e preservar um desempenho consistente e determinístico. Os diferentes tipos e famílias de VM (E32s_v3, D64s_v3, etc.) têm quotas diferentes para o número de discos, CPU, RAM e Rede.

> [!NOTE]
> Os recursos de CPU e memória dos tipos vM suportados pela SAP são pré-atribuídos nos nódosos hospedeiros. Isto significa que, uma vez implantado o VM, os recursos do hospedeiro estão disponíveis conforme definido pelo tipo VM.


Ao planear e dimensionar o SAP em soluções Azure, as quotas para cada tamanho virtual da máquina devem ser consideradas. As quotas VM são descritas [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows]. 

Para além das quotas de CPU e de recursos de memória, outras quotas definidas para as SKUs vm dizem respeito a:

- Entrada do tráfego de rede para o VM
- IOPS para tráfego de armazenamento
- Entrada para tráfego de rede

Os limites de entrada para armazenamento de uma rede são definidos, de modo que os efeitos ruidosos do vizinho podem ser mantidos ao mínimo absoluto. A quota relacionada com o armazenamento de um VM sobreporasas as quotas dos discos acumulados que estão anexados (ver também mais tarde na parte de armazenamento). Ou por outras palavras, se montar discos de armazenamento que em acumulação excederiam a quota de produção e IOPS do VM, os limites de quota VM têm prioridade.

#### <a name="rough-sizing-of-vms-for-sap"></a>Tamanho bruto de VMs para SAP 

Como uma árvore de decisão áspera para decidir se um sistema SAP se enquadra nos Serviços de Máquinas Virtuais Azure e nas suas capacidades ou se um sistema existente precisa de ser configurado de forma diferente para implantar o sistema em Azure, a árvore de decisão abaixo pode ser utilizada:

![Árvore de decisão para decidir capacidade de implantar SAP no Azure][planning-guide-figure-700]

**Passo 1**: A informação mais importante para começar é o requisito SAPS para um determinado sistema SAP. Os requisitos SAPS devem ser separados na parte DBMS e na aplicação SAP, mesmo que o sistema SAP já esteja implantado no local numa configuração de 2 níveis. Para os sistemas existentes, o SAPS relacionado com o hardware em uso pode muitas vezes ser determinado ou estimado com base nos referenciais SAP existentes. Os resultados podem <https://sap.com/about/benchmark.html>ser consultados aqui: .
Para os sistemas SAP recentemente implantados, você deveria ter passado por um exercício de dimensionamento, que deve determinar os requisitos SAPS do sistema.
Consulte também este blog e documento anexo para sap sizing no Azure:<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Passo 2**: Para os sistemas existentes, as operações de volume de I/O e de I/S por segundo no servidor DBMS devem ser medidas. Para os sistemas recém-planeados, o exercício de dimensionamento para o novo sistema também deve dar ideias ásperas sobre os requisitos de I/S do lado do DBMS. Se não tiver a certeza, eventualmente terá de conduzir uma Prova de Conceito.

**Passo 3:** Compare o requisito SAPS para o servidor DBMS com o SAPS que os diferentes tipos de VM do Azure podem fornecer. A informação sobre sAPS dos diferentes tipos de VM Azure está documentada na Nota SAP [1928533]. O foco deve estar no DBMS VM primeiro, uma vez que a camada de base de dados é a camada de um sistema SAP NetWeaver que não escala na maioria das implementações. Em contraste, a camada de aplicação SAP pode ser dimensionada. Se nenhum dos tipos de VM Azure suportado sAP pode fornecer o SAPS necessário, a carga de trabalho do sistema SAP planeado não pode ser executada no Azure. Ou precisa de implantar o sistema no local ou precisa de alterar o volume de carga de trabalho do sistema.

**Passo 4**: Tal como documentado [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows),][virtual-machines-sizes-windows]o Azure aplica uma quota IOPS por disco independente, quer utilize o Armazenamento Standard ou o Armazenamento Premium. Dependendo do tipo VM, o número de discos de dados, que podem ser montados varia. Como resultado, pode calcular um número máximo de IOPS que pode ser alcançado com cada um dos diferentes tipos de VM. Dependendo do layout de ficheiros da base de dados, pode riscar discos para se tornar um volume no OS convidado. No entanto, se o volume atual de IOPS de um sistema SAP implantado exceder os limites calculados do maior tipo de VM de Azure e se não houver possibilidade de compensar com mais memória, a carga de trabalho do sistema SAP pode ser severamente afetada. Nesses casos, pode chegar a um ponto em que não deve implantar o sistema no Azure.

**Passo 5**: Especialmente nos sistemas SAP, que são implantados no local em configurações de 2 níveis, é provável que o sistema possa ter de ser configurado no Azure numa configuração de 3 níveis. Neste passo, é necessário verificar se existe um componente na camada de aplicação SAP, que não pode ser dimensionado e que não se enquadraria no CPU e nos recursos de memória que os diferentes tipos de VM Azure oferecem. Se existe mesmo um componente deste tipo, o sistema SAP e a sua carga de trabalho não podem ser implantados no Azure. Mas se conseguir escalar os componentes da aplicação SAP em vários VMs Azure, o sistema pode ser implantado no Azure.

**Passo 6**: Se os componentes da camada de aplicação DBMS e SAP puderem ser executados em VMs Azure, a configuração deve ser definida no que diz respeito a:

* Número de VMs Azure
* Tipos vM para os componentes individuais
* Número de VHDs em VM DBMS para fornecer IOPS suficientes 

### <a name="storage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Armazenamento: Armazenamento e discos de dados do Microsoft Azure
As Máquinas Virtuais Microsoft Azure utilizam diferentes tipos de armazenamento. Ao implementar o SAP nos Serviços de Máquinas Virtuais Azure, é importante compreender as diferenças entre estes dois principais tipos de armazenamento:

* Armazenamento volátil e não persistente.
* Armazenamento persistente.

Os VMs azure oferecem discos não persistentes após a implantação de um VM. Em caso de reinicialização vm, todo o conteúdo dessas unidades será eliminado. Por conseguinte, é dado adquirido que os ficheiros de dados e os ficheiros de registo/redo das bases de dados não devem, em caso algum, ser localizados nessas unidades não persistiu. Pode haver exceções para algumas das bases de dados, onde estas unidades não persistiu podem ser adequadas para espaços de mesa temporários e temporários. No entanto, evite utilizar esses discos para VMs série A, uma vez que essas unidades não persistiu são limitadas na entrada com essa família VM. Para mais detalhes, leia o artigo [Compreensão da condução temporária em VMs windows em Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

---
> ![Windows][Logo_Windows] Windows
> 
> Unidade D:\ em um VM Azure é uma unidade não persistiu, que é apoiada por alguns discos locais no nó de computação Azure. Uma vez que não é persistiu, isto significa que quaisquer alterações feitas ao conteúdo no D:\ a unidade perde-se quando o VM é reiniciado. Por "quaisquer alterações", como ficheiros armazenados, diretórios criados, aplicações instaladas, etc.
> 
> ![Linux][Logo_Linux] Linux
> 
> Os VMs Linux Azure montam automaticamente uma unidade a /mnt/recurso que é uma unidade não persistiu apoiada por discos locais no nó de computação Azure. Por não persistir, isto significa que quaisquer alterações feitas ao conteúdo em /mnt/recurso são perdidas quando o VM é reiniciado. Por quaisquer alterações, como ficheiros armazenados, diretórios criados, aplicações instaladas, etc.
> 
> 

---

O Microsoft Azure Storage fornece armazenamento persistente e os níveis típicos de proteção e redundância vistos no armazenamento SAN. Os discos baseados no Armazenamento Azure são discos rígidos virtuais (VHDs) localizados nos Serviços de Armazenamento Azure. O OS-Disk local (Windows C:\, Linux /dev/sda1) é armazenado no Armazenamento Azure, e volumes/discos adicionais montados no VM também são armazenados lá.

É possível fazer o upload de um VHD existente a partir do local ou criar os vazios de dentro do Azure e anexar esses VHDs a VMs implantados.

Depois de criar ou carregar um VHD no Armazenamento Azure, é possível montá-los e ligá-los a uma Máquina Virtual existente e copiar vHD existente (não montado).

À medida que estes VHDs são persistidos, os dados e as mudanças dentro desses VHDs são seguros ao reiniciar e recriar uma instância de Máquina Virtual. Mesmo que uma instância seja eliminada, estes VHDs permanecem seguros e podem ser reimplantados ou em caso de discos não-So podem ser montados em outros VMs.

Mais informações sobre o Armazenamento Azure podem ser encontradas aqui:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Armazenamento Standard do Azure
O armazenamento da Azure Standard foi o tipo de armazenamento disponível quando o Azure IaaS foi lançado. Havia quotas IOPS aplicadas por disco único. A latência experiente não estava na mesma classe que os dispositivos SAN/NAS normalmente implantados para sistemas SAP topo de gama hospedados no local. No entanto, o Armazenamento Padrão Azure revelou-se suficiente para muitas centenas de sistemas SAP entretanto implantados em Azure.

Os discos armazenados nas Contas de Armazenamento Padrão do Azure são cobrados com base nos dados reais armazenados, no volume de transações de armazenamento, nas transferências de dados de saída e na opção de despedimento escolhida. Muitos discos podem ser criados no máximo 1TB de tamanho, mas desde que estes permaneçam vazios, não há custo. Se, em seguida, encher um VHD com 100GB cada, é-lhe cobrado o armazenamento de 100GB e não pelo tamanho nominal com que o VHD foi criado.

#### <a name="azure-premium-storage"></a><a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Armazenamento Azure Premium
O Azure Premium Storage foi introduzido com o objetivo de fornecer:

* Melhor latência de I/O.
* Melhor entrada.
* Menos variabilidade na latência de I/S.

Para o efeito, foram introduzidas muitas alterações das quais as duas mais significativas são:

* Utilização de discos SSD nos nódeos de armazenamento azure
* Uma nova cache de leitura que é apoiada pelo SSD local de um nó de computação Azure

Em frente ao armazenamento Standard onde as capacidades não se alteraram dependendo do tamanho do disco (ou VHD), o Armazenamento Premium tem atualmente três categorias de discos diferentes, que são mostradas neste artigo:<https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Vê que iOPS/disco e entrada/disco são dependentes da categoria de tamanho dos discos

A base de custos no caso do Armazenamento Premium não é o volume real de dados armazenado nesses discos, mas sim a categoria de tamanho de tal disco, independentemente da quantidade de dados que são armazenados dentro do disco.

Também pode criar discos no Armazenamento Premium que não estão a mapear diretamente as categorias de tamanhos mostradas. Este pode ser o caso, especialmente quando copia discos do Armazenamento Padrão para Armazenamento Premium. Nestes casos, é realizado um mapeamento para a próxima maior opção de disco de armazenamento Premium.

A maioria das famílias Azure VM certificadas com SAP são capazes de trabalhar com armazenamento premium e ou uma mistura entre a norma Azure e o Armazenamento Premium.

Se estiver a verificar a parte dos VMs da série DS [neste artigo (Linux)][virtual-machines-sizes-linux] e [neste artigo (Windows)][virtual-machines-sizes-windows], percebe que existem limitações de volume de dados aos discos de Armazenamento Premium na granularidade do nível VM. Diferentes séries DS ou VMs da série GS também têm limitações diferentes no número de discos de dados que podem ser montados. Estes limites também estão documentados no artigo acima referido. Mas, na essência, significa que se você, por exemplo, montar discos 32 x P30 para um único DS14 VM não pode obter 32 x a potência máxima de um disco P30. Em vez disso, a potência máxima ao nível vm, tal como documentada no artigo, limita a entrada de dados.

Mais informações sobre o Armazenamento Premium podem ser encontradas aqui:<https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Contas de Armazenamento Azure

Ao implantar serviços ou VMs em Azure, a implantação de VHDs e VM Images pode ser organizada em unidades chamadas Contas de Armazenamento Azure. Ao planear uma implantação azure, você precisa considerar cuidadosamente as restrições de Azure. Por um lado, há um número limitado de Contas de Armazenamento por subscrição azure. Embora cada Conta de Armazenamento Azure possa conter um grande número de ficheiros VHD, existe um limite fixo no total do IOPS por Conta de Armazenamento. Ao implantar centenas de VMs SAP com sistemas DBMS criando chamadas IO significativas, recomenda-se a distribuição de VMS DBMS de IOPS elevados entre várias Contas de Armazenamento Azure. Há que ter cuidado para não exceder o limite atual das Contas de Armazenamento Azure por subscrição. Uma vez que o armazenamento é uma parte vital da implantação da base de dados para um sistema SAP, este conceito é discutido mais detalhadamente no já referenciado Guia de [Implantação DBMS][dbms-guide].

Mais informações sobre as Contas de Armazenamento Azure podem ser encontradas nos alvos de [escalabilidade para contas de armazenamento padrão](../../../storage/common/scalability-targets-standard-account.md) e metas de escalabilidade para contas de armazenamento de [blob](../../../storage/blobs/scalability-targets-premium-page-blobs.md)de página premium . Ao ler estes artigos, percebe que existem diferenças nas limitações entre as Contas De Armazenamento Padrão Azure e as Contas de Armazenamento Premium. Grandes diferenças são o volume de dados que podem ser armazenados dentro de tal Conta de Armazenamento. No Armazenamento Standard, o volume é uma magnitude maior do que com armazenamento premium. Por outro lado, a Conta De Armazenamento Padrão é severamente limitada no IOPS (ver coluna Taxa total de **pedido),** enquanto a Conta de Armazenamento Premium Azure não tem tal limitação. Discutiremos detalhes e resultados destas diferenças ao discutir as implementações dos sistemas SAP, especialmente os servidores DBMS.

Dentro de uma Conta de Armazenamento, tem a possibilidade de criar diferentes recipientes com o propósito de organizar e categorizar diferentes VHDs. Estes recipientes são utilizados, por exemplo, para vHDs separados de diferentes VMs. Não existem implicações de desempenho na utilização de apenas um recipiente ou vários contentores por baixo de uma única Conta de Armazenamento Azure.

Dentro do Azure, um nome VHD segue a seguinte ligação de nomeação que precisa fornecer um nome único para o VHD dentro do Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

A cadeia acima precisa identificar exclusivamente o VHD que é armazenado no Armazenamento Azure.

#### <a name="managed-disks"></a><a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Managed Disks

Os Discos Geridos são um novo tipo de recurso no Gestor de Recursos Azure que pode ser usado em vez de VHDs que são armazenados em Contas de Armazenamento Azure. Os Discos Geridos alinham-se automaticamente com o conjunto de disponibilidade da máquina virtual a que estão ligados e, portanto, aumentam a disponibilidade da sua máquina virtual e os serviços que estão a funcionar na máquina virtual. Para mais informações, leia o [artigo Visão Geral.](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)

Recomendamos que utilize o disco Gerido, pois simplificam a implementação e gestão das suas máquinas virtuais.
Atualmente, a SAP apenas suporta discos geridos Premium. Para mais informações, leia Nota SAP [1928533 .]

#### <a name="microsoft-azure-storage-resiliency"></a>Resiliência do Armazenamento Microsoft Azure

O Microsoft Azure Storage armazena a base VHD (com OS) e discos ou BLOBs anexados em pelo menos três nós de armazenamento separados. Este facto chama-se Armazenamento Redundante Local (LRS). O LRS é padrão para todos os tipos de armazenamento em Azure. 

Existem vários métodos de despedimento, todos descritos no artigo [Replicação de Armazenamento Azure.](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

> [!NOTE]
>A partir do Armazenamento Azure Premium, que é o tipo recomendado de armazenamento para VMs DBMS e discos que armazenam base de dados e ficheiros de log/redo, o único método disponível é o LRS. Como resultado, é necessário configurar métodos de base de dados, como o SQL Server Always On, o Oracle Data Guard ou a HANA System Replication para permitir a replicação de dados de base de dados noutra região azure ou noutra Zona de Disponibilidade Azure.


> [!NOTE]
> Para as implementações de DBMS, o uso de Armazenamento Geo Redundante, disponível no Azure Standard Storage, não é recomendado, uma vez que tem um impacto de desempenho severo e não honra a ordem de escrita em diferentes VHDs que estão ligados a um VM. O facto de não honrar a ordem de escrita em diferentes VHDs tem um elevado potencial para acabar em bases de dados inconsistentes no lado alvo da replicação se os ficheiros de base de dados e log/redo estiverem espalhados por vários VHDs (como na sua maioria o caso) no lado vM de origem.


### <a name="microsoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Rede Microsoft Azure

O Microsoft Azure fornece uma infraestrutura de rede, que permite o mapeamento de todos os cenários, que queremos realizar com o software SAP. As capacidades são:

* Acesso a partir do exterior, diretamente aos VMs através de Serviços de Terminal Windows ou ssh/VNC
* Acesso a serviços e portas específicas utilizados por aplicações dentro dos VMs
* Resolução de Comunicação Interna e Nome entre um grupo de VMs implantados como VMs Azure
* Conectividade transversal entre a rede de instalações de um cliente e a rede Azure
* Região Cross Azure ou conectividade do centro de dados entre os sítios do Azure

Pode encontrar mais informações aqui: <https://azure.microsoft.com/documentation/services/virtual-network/>

Existem muitas possibilidades diferentes para configurar nome e resolução IP em Azure. Existe também um serviço Azure DNS, que pode ser usado em vez de configurar o seu próprio servidor DNS. Mais informações podem ser encontradas [neste artigo][virtual-networks-manage-dns-in-vnet] e [nesta página.](https://azure.microsoft.com/services/dns/)

Para cenários transversais ou híbridos, contamos com o facto de o AD/OpenLDAP/DNS ter sido alargado através de VPN ou ligação privada ao Azure. Para certos cenários aqui documentados, pode ser necessário ter uma réplica AD/OpenLDAP instalada em Azure.

Uma vez que a resolução de networking e de nome é uma parte vital da implantação da base de dados para um sistema SAP, este conceito é discutido mais detalhadamente no Guia de Implementação do [DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Redes Virtuais do Azure

Ao criar uma Rede Virtual Azure, pode definir a gama de endereços dos endereços IP privados atribuídos pela funcionalidade Azure DHCP. Em cenários transversais, a gama de endereços IP definida ainda é atribuída utilizando dHCP pelo Azure. No entanto, a resolução de nome de domínio é feita no local (assumindo que os VMs fazem parte de um domínio no local) e, portanto, pode resolver endereços para além de diferentes Serviços azure cloud.

Todas as máquinas virtuais em Azure precisam de ser ligadas a uma Rede Virtual.

Mais detalhes podem ser encontrados [neste artigo][resource-groups-networking] e [nesta página.](https://azure.microsoft.com/documentation/services/virtual-network/)


> [!NOTE]
> Por predefinição, uma vez implantado um VM, não pode alterar a configuração da Rede Virtual. As definições TCP/IP devem ser deixadas no servidor Azure DHCP. O comportamento padrão é a atribuição de IP dinâmico.
>
>

O endereço MAC do cartão de rede virtual pode mudar, por exemplo, após o redimensionar e o Os do Hóspede Windows ou Linux recolhe o novo cartão de rede e utiliza automaticamente o DHCP para atribuir os endereços IP e DNS neste caso.

##### <a name="static-ip-assignment"></a>Atribuição de IP estática
É possível atribuir endereços IP fixos ou reservados a VMs dentro de uma Rede Virtual Azure. Executar os VMs numa Rede Virtual Azure abre uma grande possibilidade de alavancar esta funcionalidade se necessário ou necessário para alguns cenários. A atribuição ip permanece válida em toda a existência do VM, independentemente de o VM estar a funcionar ou a encerrar. Como resultado, é necessário ter em conta o número total de VMs (vMs em execução e parados) ao definir o leque de endereços IP para a Rede Virtual. O endereço IP permanece atribuído quer até que o VM e a sua Interface de Rede seja eliminado ou até que o endereço IP seja desatribuído novamente. Para mais informações, leia [este artigo.][virtual-networks-static-private-ip-arm-pportal]

> [!NOTE]
> Deve atribuir endereços IP estáticos através de meios Azure para vNICs individuais. Não deve atribuir endereços IP estáticos dentro do sodo de hóspedes a um vNIC. Alguns serviços azure, como o Azure Backup Service, baseiam-se no facto de que pelo menos o vNIC primário está definido para o DHCP e não para endereços IP estáticos. Consulte também o documento [Troubleshoot Azure virtual machine backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Múltiplos NICs por VM

Pode definir vários cartões de interface de rede virtual (vNIC) para uma Máquina Virtual Azure. Com a capacidade de ter vários vNICs pode começar a configurar a separação de tráfego da rede onde, por exemplo, o tráfego do cliente é encaminhado através de um vNIC e o tráfego de backend é encaminhado através de um segundo vNIC. Dependendo do tipo de VM existem diferentes limitações para o número de vNICs que um VM pode ter atribuído. Detalhes exatos, funcionalidade e restrições podem ser encontrados nestes artigos:

* [Criar um VM Windows com vários NICs][virtual-networks-multiple-nics-windows]
* [Crie um VM Linux com vários NICs][virtual-networks-multiple-nics-linux]
* [Implementar VMs multi NIC usando um modelo][virtual-network-deploy-multinic-arm-template]
* [Implementar vMs multi NIC usando powerShell][virtual-network-deploy-multinic-arm-ps]
* [Implementar vMs multi NIC utilizando o Azure CLI][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Conectividade site-a-site

Cross-premises é Azure VMs e On-Premises ligados a uma conexão VPN transparente e permanente. Espera-se que se torne o padrão de implantação SAP mais comum em Azure. O pressuposto é que os procedimentos operacionais e os processos com instâncias SAP em Azure devem funcionar de forma transparente. Isto significa que deve ser capaz de imprimir estes sistemas, bem como utilizar o Sistema de Gestão de Transportes SAP (TMS) para transportar alterações de um sistema de desenvolvimento em Azure para um sistema de teste, que é implantado no local. Mais documentação em torno do site-a-local pode ser encontrada [neste artigo][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Dispositivo do túnel VPN

Para criar uma ligação site-a-site (centro de dados no local para o centro de dados Azure), é necessário obter e configurar um dispositivo VPN, ou utilizar o Serviço de Encaminhamento e Acesso Remoto (RRAS) que foi introduzido como um componente de software com o Windows Server 2012.

* [Criar uma rede virtual com uma ligação VPN site-to-site usando powerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Acerca dos dispositivos de VPN para ligações de Gateway de Rede de VPNs][vpn-gateway-about-vpn-devices]
* [FAQ do VPN Gateway][vpn-gateway-vpn-faq]

![Ligação local-a-local entre as instalações e o Azure][planning-guide-figure-600]

A Figura acima mostra que duas subscrições do Azure têm subgamas de endereços IP reservadas para utilização em Redes Virtuais em Azure. A conectividade da rede no local para o Azure é estabelecida via VPN.

#### <a name="point-to-site-vpn"></a>VPN Ponto a Site

A VPN ponto-a-site requer que todas as máquinas de clientes se conectem com a sua própria VPN em Azure. Para os cenários SAP, estamos a olhar, a conectividade ponto-a-local não é prática. Por conseguinte, não são dadas mais referências à conectividade VPN ponto-a-local.

Pode encontrar mais informações aqui
* [Configure uma ligação Ponto-a-Local a um VNet utilizando o portal Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Configurar uma ligação de Ponto a Site a uma VNet com o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>Multi-Site VPN

O Azure também oferece hoje em dia a possibilidade de criar conectividade VPN multi-site para uma subscrição Azure. Anteriormente, uma única subscrição limitava-se a uma ligação VPN site-to-site. Esta limitação foi-se com ligações VPN multi-site para uma única subscrição. Isto permite alavancar mais do que uma região azure para uma subscrição específica através de configurações de instalações cruzadas.

Para mais documentação, consulte [este artigo][vpn-gateway-create-site-to-site-rm-powershell]

#### <a name="vnet-to-vnet-connection"></a>Ligação VNet para VNet

Utilizando VPN multi-site, você precisa configurar uma rede virtual Azure separada em cada uma das regiões. No entanto, muitas vezes tem a exigência de que os componentes do software nas diferentes regiões se comuniquem entre si. Idealmente, esta comunicação não deve ser direcionada de uma região azure para o local e daí para a outra região de Azure. A curto prazo, o Azure oferece a possibilidade de configurar uma ligação de uma Rede Virtual Azure numa região para outra Rede Virtual Azure alojada noutra região. Esta funcionalidade chama-se ligação VNet-to-VNet. Mais detalhes sobre esta funcionalidade podem <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>ser encontrados aqui: .

#### <a name="private-connection-to-azure-expressroute"></a>Ligação privada à Azure ExpressRoute

O Microsoft Azure ExpressRoute permite a criação de ligações privadas entre centros de dados Azure e a infraestrutura no local do cliente ou num ambiente de co-localização. O ExpressRoute é oferecido por vários fornecedores de VPN MPLS (comutados com pacotes) ou outros Fornecedores de Serviços de Rede. As ligações do ExpressRoute não passam para a Internet pública. As ligações ExpressRoute oferecem maior segurança, mais fiabilidade através de múltiplos circuitos paralelos, velocidades mais rápidas e latências mais baixas do que as ligações típicas através da Internet.

Saiba mais detalhes sobre o Azure ExpressRoute e ofertas aqui:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Rota Expressa permite várias subscrições do Azure através de um circuito ExpressRoute como documentado aqui

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Túneis forçados em caso de cruzamento de instalações
Para que os VMs se juntem aos domínios no local através do site-para-site, ponto-a-site ou ExpressRoute, você precisa ter certeza de que as definições de procuração de Internet estão sendo implementadas para todos os utilizadores desses VMs também. Por padrão, o software que funciona nesses VMs ou utilizadores que usam um navegador para aceder à internet não passaria pelo proxy da empresa, mas ligaria diretamente através do Azure à internet. Mas mesmo a definição de procuração não é uma solução 100% para direcionar o tráfego através da procuração da empresa, uma vez que é da responsabilidade do software e dos serviços verificar o proxy. Se o software que funciona no VM não estiver a fazer isso ou um administrador manipular as definições, o tráfego para a Internet pode ser desviado novamente através do Azure para a Internet.

Para evitar uma conectividade de internet tão direta, pode configurar a tunneling forçada com conectividade local-a-local entre as instalações e o Azure. A descrição detalhada da função de túnel forçado é publicada aqui<https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

A Tunneling Forçada com a ExpressRoute é ativada por clientes que publicitam uma rota padrão através das sessões de peering ExpressRoute BGP.

#### <a name="summary-of-azure-networking"></a>Resumo da Rede Azure

Este capítulo continha muitos pontos importantes sobre o Azure Networking. Aqui está um resumo dos principais pontos:

* As Redes Virtuais Azure permitem-lhe colocar uma estrutura de rede na sua implantação Azure. Os VNets podem ser isolados uns contra os outros ou com a ajuda dos Grupos de Segurança da Rede, o tráfego entre VNets pode ser controlado.
* As Redes Virtuais Azure podem ser alavancadas para atribuir gamas de endereços IP a VMs ou atribuir endereços IP fixos a VMs
* Para configurar uma ligação site-a-site ou ponto-a-local, você precisa criar uma rede virtual Azure primeiro
* Uma vez que uma máquina virtual foi implantada, já não é possível alterar a Rede Virtual atribuída ao VM


## <a name="managing-azure-assets"></a>Gestão de Ativos Azure

### <a name="azure-portal"></a>Portal do Azure

O portal Azure é uma das três interfaces para gerir as implementações de VM Azure. As tarefas básicas de gestão, como a implantação de VMs a partir de imagens, podem ser feitas através do portal Azure. Além disso, a criação de Contas de Armazenamento, Redes Virtuais e outros componentes Do Azure são também tarefas que o portal Azure pode lidar bem. No entanto, funcionalidades como carregar VHDs de instalações para Azure ou copiar um VHD dentro do Azure são tarefas, que requerem ferramentas ou administração de terceiros através do PowerShell ou clI.

![Portal Microsoft Azure - Visão geral da Máquina Virtual][planning-guide-figure-800]


As tarefas de administração e configuração da instância Máquina Virtual são possíveis a partir do portal Azure.

Além de reiniciar e desligar uma Máquina Virtual, também pode anexar, desprender e criar discos de dados para a instância Máquina Virtual, para capturar a instância de preparação da imagem e configurar o tamanho da instância Máquina Virtual.

O portal Azure fornece funcionalidade básica para implementar e configurar VMs e muitos outros serviços Azure. No entanto, nem todas as funcionalidades disponíveis estão abrangidas pelo portal Azure. No portal Azure, não é possível executar tarefas como:

* Upload vHDs para Azure
* Copiando VMs


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Gestão via Microsoft Azure PowerShell cmdlets

O Windows PowerShell é um quadro poderoso e extensível que tem sido amplamente adotado pelos clientes que implementam um maior número de sistemas no Azure. Após a instalação de cmdlets PowerShell numa estação de ambiente de trabalho, portátil ou estação de gestão dedicada, os cmdlets PowerShell podem ser executados remotamente.

O processo para permitir um ambiente de trabalho/portátil local para a utilização de cmdlets Azure PowerShell e como configurar os dispositivos para a utilização com a subscrição azure(s) é descrito [neste artigo][powershell-install-configure].

Passos mais detalhados sobre como instalar, atualizar e configurar os cmdlets Azure PowerShell também podem ser encontrados [neste capítulo do Guia de Implantação][deployment-guide-4.1].

A experiência do cliente até agora tem sido que a PowerShell (PS) é certamente a ferramenta mais poderosa para implementar VMs e criar passos personalizados na implementação de VMs. Todos os clientes que executam instâncias SAP em Azure estão a usar cmdlets PS para complementar as tarefas de gestão que fazem no portal Azure ou até estão a utilizar cmdlets PS exclusivamente para gerir as suas implementações no Azure. Uma vez que os cmdlets específicos do Azure partilham a mesma convenção de nomeação que os mais de 2000 cmdlets relacionados com o Windows, é uma tarefa fácil para os administradores do Windows alavancar esses cmdlets.

Veja o exemplo aqui:<https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


A implantação da extensão Azure para SAP (ver capítulo [Extensão Azure para SAP][planning-guide-9.1] neste documento) só é possível através da PowerShell ou CLI. Por isso, é obrigatório configurar e configurar powerShell ou CLI ao implementar ou administrar um sistema SAP NetWeaver em Azure.  

À medida que o Azure fornece mais funcionalidades, vão ser adicionados novos cmdlets PS que requerem uma atualização dos cmdlets. Por isso, faz sentido verificar o site de <https://azure.microsoft.com/downloads/> download do Azure pelo menos uma vez por mês para uma nova versão dos cmdlets. A nova versão está instalada em cima da versão mais antiga.

Para obter uma lista geral de comandos PowerShell relacionados com OT, consulte aqui: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Gestão através de comandos ClI Microsoft Azure

Para os clientes que usam o Linux e querem gerir os recursos azure A PowerShell pode não ser uma opção. A Microsoft oferece o Azure CLI como alternativa.
O Azure CLI fornece um conjunto de comandos de código aberto e cross-platform para trabalhar com a Plataforma Azure. O Azure CLI fornece grande parte da mesma funcionalidade encontrada no portal Azure.

Para obter informações sobre instalação, configuração e como usar comandos CLI para realizar tarefas Azure ver

* [Instalar a CLI clássica do Azure][xplat-cli]
* [Implementar e gerir máquinas virtuais utilizando modelos de Gestor de Recursos Azure e o Azure CLI] [.. /.. /linux/create-ssh-secured-vm-from-template.md]
* [Use o CLI clássico azure para Mac, Linux e Windows com Azure Resource Manager][xplat-cli-azure-resource-manager]

Leia também o capítulo [Azure CLI para VMs Linux][deployment-guide-4.5.2] no Guia de [Implantação][planning-guide] sobre como utilizar o Azure CLI para implantar a extensão Azure para SAP.


## <a name="first-steps-planning-a-deployment"></a>Primeiros passos planejando uma implantação
O primeiro passo no planeamento de implantação é não verificar se os VMs estão disponíveis para executar o SAP. O primeiro passo pode ser aquele que é demorado, mas o mais importante, é trabalhar com equipas de conformidade e segurança na sua empresa sobre quais são as condições de fronteira para implantar que tipo de carga de trabalho SAP ou processo de negócio para a nuvem pública. Se a sua empresa tiver implementado outro software antes no Azure, o processo pode ser fácil. Se a sua empresa estiver mais no início da viagem, poderá haver discussões maiores necessárias para descobrir as condições de fronteira, condições de segurança, que permitem que certos dados SAP e processos de negócio SAP sejam alojados na nuvem pública.

Como ajuda útil, pode apontar as ofertas de conformidade da Microsoft para uma lista de [ofertas](https://docs.microsoft.com/microsoft-365/compliance/offering-home) de conformidade que a Microsoft pode fornecer. 

Outras áreas de preocupação, como a encriptação de dados para dados em repouso ou outra encriptação no serviço Azure, estão documentadas na visão geral da [encriptação Azure.](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview)

Não subestimes esta fase do projeto no teu planeamento. Só quando se tem acordo e regras em torno deste tema, é preciso passar ao próximo passo, que é o planeamento da arquitetura de rede que implementa no Azure.


## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Diferentes formas de implantar VMs para SAP em Azure

Neste capítulo, aprende-se as diferentes formas de implantar um VM em Azure. Neste capítulo estão abrangidos os procedimentos de preparação adicionais, bem como o manuseamento de VHDs e VMs em Azure.

### <a name="deployment-of-vms-for-sap"></a>Implantação de VMs para SAP

O Microsoft Azure oferece várias formas de implementar VMs e discos associados. Por conseguinte, é importante compreender as diferenças, uma vez que os preparativos dos VM podem diferir consoante o método de implantação. Em geral, olhamos para os seguintes cenários:

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Mover um VM de instalações para Azure com um disco não generalizado

Planeia mover um sistema SAP específico do local para o Azure. Isto pode ser feito através do upload do VHD, que contém o OS, os binários SAP e DBMS mais os VHDs com os dados e ficheiros de registo do DBMS para o Azure. Em contraste com o [cenário #2 abaixo][planning-guide-5.1.2], você mantém o nome de anfitrião, sAP SID e contas de utilizador SAP no Azure VM como foram configurados no ambiente no local. Portanto, generalizar a imagem não é necessário. Ver capítulos [Preparação para mover um VM do local para Azure com um disco não generalizado][planning-guide-5.2.1] deste documento para etapas de preparação no local e upload de VMs ou VHDs não generalizados para Azure. Leia o capítulo [Cenário 3: Mover um VM das instalações utilizando um Azure VHD não generalizado com SAP][deployment-guide-3.4] no Guia de [Implantação][deployment-guide] para passos detalhados de implantação de tal imagem em Azure.

#### <a name="deploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Implementação de um VM com uma imagem específica do cliente

Devido aos requisitos específicos de patch da sua versão OS ou DBMS, as imagens fornecidas no Mercado Azure podem não corresponder às suas necessidades. Por isso, poderá ser necessário criar um VM utilizando a sua própria imagem privada de VM OS/DBMS, que pode ser implementada várias vezes depois. Para preparar uma imagem privada para duplicação, há que considerar os seguintes itens:

---
> ![Windows][Logo_Windows] Windows
>
> Consulte mais detalhes <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> aqui: As definições do Windows (como o Windows SID e o nome de anfitrião) devem ser abstraídas/generalizadas no VM no local através do comando de sysprep.
>
>
> ![Linux][Logo_Linux] Linux
>
> Siga os passos descritos nestes artigos para [SUSE,][virtual-machines-linux-create-upload-vhd-suse] [Red Hat][virtual-machines-linux-redhat-create-upload-vhd], ou [Oracle Linux,][virtual-machines-linux-create-upload-vhd-oracle]para preparar um VHD para ser enviado para Azure.
>
>

---
Se já instalou conteúdo SAP no seu VM no local (especialmente para sistemas de 2 camadas), pode adaptar as definições do sistema SAP após a implementação do VM Azure através do procedimento de renomeação de instância suportado pelo Gestor de Provisionamento de Software SAP (Nota [SAP 1619720]). Consulte capítulos [Preparação para a implementação][planning-guide-5.2.2] de um VM com uma imagem específica do cliente para SAP e [upload de um VHD de instalações para Azure][planning-guide-5.3.2] deste documento para etapas de preparação no local e upload de um VM generalizado para Azure. Leia o capítulo [Cenário 2: Implantação de um VM com uma imagem personalizada para O SAP][deployment-guide-3.3] no Guia de [Implantação][deployment-guide] para passos detalhados de implementação de tal imagem em Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Implantação de um VM para fora do Mercado Azure

Deseja utilizar uma imagem VM fornecida pela Microsoft ou por terceiros do Azure Marketplace para implementar o seu VM. Depois de ter implantado o seu VM em Azure, segue as mesmas diretrizes e ferramentas para instalar o software SAP e/ou DBMS dentro do seu VM, como faria num ambiente no local. Para uma descrição mais detalhada da implantação, consulte o capítulo [Cenário 1: Implantação de um VM para fora do Mercado Azure para SAP][deployment-guide-3.2] no Guia de [Implantação][deployment-guide].

### <a name="preparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Preparação de VMs com SAP para Azure

Antes de enviar VMs para Azure, você precisa certificar-se de que os VMs e VHDs cumprem determinados requisitos. Existem pequenas diferenças dependendo do método de implantação que é utilizado.

#### <a name="preparation-for-moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Preparação para mover um VM do local para Azure com um disco não generalizado

Um método comum de implantação é mover um VM existente, que executa um sistema SAP de instalações para Azure. Esse VM e o sistema SAP no VM devem funcionar em Azure usando o mesmo nome de anfitrião e provavelmente o mesmo Sid SAP. Neste caso, o osso convidado da VM não deve ser generalizado para múltiplas implementações. Se a rede no local foi estendida para Azure, então mesmo as mesmas contas de domínio podem ser utilizadas dentro do VM como as utilizadas anteriormente no local.

Os requisitos para preparar o seu próprio Disco Azure VM são:

* Originalmente, o VHD que contém o sistema operativo poderia ter um tamanho máximo de 127GB apenas. Esta limitação foi eliminada no final de março de 2015. Agora, o VHD que contém o sistema operativo pode ter até 1TB de tamanho, tal como qualquer outro Azure Storage hospedado em VHD também.
* Tem de estar no formato VHD fixo. VHDs dinâmicos ou VHDs em formato VHDx ainda não são suportados no Azure. VHDs dinâmicos serão convertidos em VHDs estáticos quando carregar o VHD com comandos PowerShell ou CLI
* Os VHDs, que são montados no VM e devem ser montados novamente em Azure para o VM, também precisam de estar num formato VHD fixo. Leia [este artigo (Linux)](../../linux/managed-disks-overview.md) e [este artigo (Windows)](../../windows/managed-disks-overview.md)para obter limites de tamanho dos discos de dados. VHDs dinâmicos serão convertidos em VHDs estáticos quando carregar o VHD com comandos PowerShell ou CLI
* Adicione outra conta local com privilégios de administrador, que podem ser usados pelo suporte da Microsoft ou que podem ser atribuídos como contexto para serviços e aplicações para executar até que o VM seja implementado e utilizadores mais adequados possam ser usados.
* Adicione outras contas locais, uma vez que estas podem ser necessárias para o cenário específico de implantação.

---
> ![Windows][Logo_Windows] Windows
>
> Neste cenário, não é necessária qualquer generalização (sysprep) do VM para carregar e implantar o VM em Azure.
> Certifique-se de que a unidade D:\ não é usado.
> Defina o disco automaticamente para os discos anexados, tal como descrito no capítulo [Definição de automatização para discos ligados][planning-guide-5.5.3] neste documento.
>
> ![Linux][Logo_Linux] Linux
>
> Neste cenário, não é necessária qualquer generalização (waagent -deprovisionamento) do VM para carregar e implantar o VM em Azure.
> Certifique-se de que não é utilizado /mnt/recurso e que todos os discos são montados através de uuid. Para o disco OS, certifique-se de que a entrada do bootloader também reflete a montagem à base de uide.
>
>

---
#### <a name="preparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Preparação para a implementação de um VM com uma imagem específica do cliente para o SAP

Os ficheiros VHD que contenham um OS generalizado são armazenados em contentores em Contas de Armazenamento Azure ou como imagens de Disque geridas. Pode implementar um novo VM a partir de tal imagem, referindo-se à imagem VHD ou Disque gerida como fonte nos ficheiros do modelo de implementação, tal como descrito no capítulo [Cenário 2: Implementar um VM com uma imagem personalizada para SAP][deployment-guide-3.3] do Guia de [Implementação][deployment-guide].

Os requisitos para preparar a sua própria Imagem Azure VM são:

* Originalmente, o VHD que contém o sistema operativo poderia ter um tamanho máximo de 127GB apenas. Esta limitação foi eliminada no final de março de 2015. Agora, o VHD que contém o sistema operativo pode ter até 1TB de tamanho, tal como qualquer outro Azure Storage hospedado em VHD também.
* Tem de estar no formato VHD fixo. VHDs dinâmicos ou VHDs em formato VHDx ainda não são suportados no Azure. VHDs dinâmicos serão convertidos em VHDs estáticos quando carregar o VHD com comandos PowerShell ou CLI
* Os VHDs, que são montados no VM e devem ser montados novamente em Azure para o VM, também precisam de estar num formato VHD fixo. Leia [este artigo (Linux)](../../windows/managed-disks-overview.md) e [este artigo (Windows)](../../linux/managed-disks-overview.md) para obter limites de tamanho dos discos de dados. VHDs dinâmicos serão convertidos em VHDs estáticos quando carregar o VHD com comandos PowerShell ou CLI
* Adicione outras contas locais, uma vez que estas podem ser necessárias para o cenário específico de implantação.
* Se a imagem contiver uma instalação do SAP NetWeaver e renomear o nome anfitrião a partir do nome original no ponto da implementação do Azure, é provável que copie as versões mais recentes do DVD do Gestor de Provisionamento de Software SAP no modelo. Isto permitir-lhe-á utilizar facilmente o SAP fornecido funcionalidade de renome para adaptar o nome de anfitrião alterado e/ou alterar o SID do sistema SAP dentro da imagem VM implementada assim que uma nova cópia for iniciada.

---
> ![Windows][Logo_Windows] Windows
>
> Certifique-se de que a unidade D:\ não é utilizado Configurar o disco automaticamente para discos ligados, tal como descrito no capítulo [Definição de automatização para discos ligados][planning-guide-5.5.3] neste documento.
>
> ![Linux][Logo_Linux] Linux
>
> Certifique-se de que não é utilizado /mnt/recurso e que todos os discos são montados através de uuid. Para o disco OS, certifique-se de que a entrada do bootloader também reflete a montagem à base de uide.
>
>

---
* O SAP GUI (para fins administrativos e de configuração) pode ser pré-instalado em tal modelo.
* Outros softwares necessários para executar os VMs com sucesso em cenários transversais podem ser instalados desde que este software possa funcionar com o nome de novo nome do VM.

Se o VM estiver preparado o suficiente para ser genérico e eventualmente independente das contas/utilizadores que não estão disponíveis no cenário de implantação do Azure direcionado, é realizado o último passo de preparação para generalizar tal imagem.

##### <a name="generalizing-a-vm"></a>Generalizar um VM
---
> ![Windows][Logo_Windows] Windows
>
> O último passo é iniciar sessão num VM com uma conta de Administrador. Abra uma janela de comando windows como *administrador*. Vá para %windir%\windows\system32\sysprep e execute sysprep.exe.
> Uma pequena janela aparecerá. É importante verificar a opção **Generalize** (o predefinido não está verificado) e alterar a Opção de Encerramento do seu padrão de 'Reboot' para 'shutdown'. Este procedimento pressupõe que o processo de sysprep é executado no local no Os convidado de um VM.
> Se quiser realizar o procedimento com um VM já em funcionamento em Azure, siga os passos descritos [neste artigo.](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
>
> ![Linux][Logo_Linux] Linux
>
> [Como capturar uma máquina virtual do Linux para ser utilizada como modelo do Resource Manager (How to capture a Linux virtual machine to use as a Resource Manager template)][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Transferência de VMs e VHDs entre as instalações para o Azure
Uma vez que o upload de imagens e discos VM para O Azure não é possível através do portal Azure, é necessário utilizar cmdlets Azure PowerShell ou CLI. Outra possibilidade é a utilização da ferramenta 'AzCopy'. A ferramenta pode copiar VHDs entre as instalações e o Azure (em ambas as direções). Também pode copiar VHDs entre regiões azure. Consulte [esta documentação][storage-use-azcopy] para download e utilização da AzCopy.

Uma terceira alternativa seria utilizar várias ferramentas orientadas para o GUI de terceiros. No entanto, certifique-se de que estas ferramentas estão a apoiar as Blobs da Página Azure. Para os nossos propósitos, precisamos de utilizar a loja Azure Page Blob (as diferenças são descritas aqui: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Também as ferramentas fornecidas pelo Azure são eficientes na compressão dos VMs e VHDs, que precisam de ser carregados. Isto é importante porque esta eficiência na compressão reduz o tempo de upload (que varia de qualquer forma dependendo do link de carregamento para a internet a partir da instalação no local e da região de implantação de Azure direcionada). É um pressuposto justo que o upload de um VM ou VHD da localização europeia para os centros de dados azure sediados nos EUA levará mais tempo do que o upload dos mesmos VMs/VHDs para os centros de dados europeus Azure.

#### <a name="uploading-a-vhd-from-on-premises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Upload de um VHD de instalações para Azure
Para fazer o upload de um VM ou VHD existentes a partir da rede no local, tal VM ou VHD precisa de satisfazer os requisitos enumerados no capítulo [Preparação para a deslocação de um VM do local para O-Local para Azure com um disco não generalizado][planning-guide-5.2.1] deste documento.

Tal VM NÃO precisa de ser generalizada e pode ser carregada no estado e forma que tem após o encerramento no local. O mesmo acontece com os VHDs adicionais, que não contêm qualquer sistema operativo.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Carregar um VHD e torná-lo um Disco Azure
Neste caso, queremos fazer o upload de um VHD, com ou sem um SISTEMA, e montá-lo num VM como um disco de dados ou usá-lo como disco de OS. Este é um processo em várias etapas

**PowerShell**

* Inscreva-se na sua subscrição com *o Connect-AzAccount*
* Defina a subscrição do seu contexto com *Set-AzContext* e parameter SubscriptionId ou SubscriptionName - ver<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Faça upload do VHD com *Add-AzVhd* para uma conta de armazenamento Azure - ver<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Opcional) Criar um Disco Gerido a partir do VHD com *New-AzDisk* - ver<https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Defina o disco OS de um novo config VM para o VHD ou disco gerido com *Set-AzVMOSDisk* - ver<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Criar um novo VM a partir do VM config com *New-AzVM* - ver<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Adicione um disco de dados a um novo VM com *Add-AzVMDataDisk* - ver<https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**CLI do Azure**

* Inicie sessão na sua subscrição com *login az*
* Selecione a sua subscrição com conjunto de *conta az `<subscription name or id` -- subscrição*
* Carregue o VHD com *upload de blob* de armazenamento az - ver Utilizar o [Azure CLI com armazenamento Azure][storage-azure-cli]
* (Opcional) Criar um Disco Gerido a partir do VHD com *az disk criar* - verhttps://docs.microsoft.com/cli/azure/disk
* Crie um novo VM especificando o VHD carregado ou o disco gerido como disco OS com *az vm criar* e parâmetro *-- anexar-os-disco*
* Adicione um disco de dados a um novo VM com aanexação e parâmetro do *disco az vm* *-- novo*

**Modelo**

* Faça upload do VHD com PowerShell ou Azure CLI
* (Opcional) Criar um Disco Gerido a partir do VHD com PowerShell, Azure CLI ou o portal Azure
* Desloque o VM com um modelo JSON referindo o VHD como mostrado [neste modelo JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) exemplo ou utilizando discos geridos como mostrado [neste modelo JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)exemplo .

#### <a name="deployment-of-a-vm-image"></a>Implantação de uma Imagem VM
Para fazer o upload de um VM ou VHD existentes a partir da rede no local, de modo a utilizá-lo como uma imagem Azure VM como uma VM ou VHD precisa satisfazer os requisitos listados no capítulo [Preparação para a implementação de um VM com uma imagem específica do cliente para o SAP][planning-guide-5.2.2] deste documento.

* Utilize *sysprep* no Windows ou *waagent -deprovisionamento* no Linux para generalizar o seu VM - consulte [Sysprep Referência Técnica](https://technet.microsoft.com/library/cc766049.aspx) para Windows ou Como capturar uma máquina virtual [Linux para usar como modelo][capture-image-linux-step-2-create-vm-image] de Gestor de Recursos para Linux
* Inscreva-se na sua subscrição com *o Connect-AzAccount*
* Defina a subscrição do seu contexto com *Set-AzContext* e parameter SubscriptionId ou SubscriptionName - ver<https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Faça upload do VHD com *Add-AzVhd* para uma conta de armazenamento Azure - ver<https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Opcional) Criar uma imagem de disco gerida a partir do VHD com *New-AzImage* - ver<https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Desloque o disco OS de um novo config VM para o
  * VHD com *Set-AzVMOSDisk -SourceImageUri -CreateOption fromImage* - ver ver<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Conjunto de imagem de disco *gerido-AzVMSourceImage* - ver<https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Criar um novo VM a partir do VM config com *New-AzVM* - ver<https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**CLI do Azure**

* Utilize *sysprep* no Windows ou *waagent -deprovisionamento* no Linux para generalizar o seu VM - consulte [Sysprep Referência Técnica](https://technet.microsoft.com/library/cc766049.aspx) para Windows ou Como capturar uma máquina virtual [Linux para usar como modelo][capture-image-linux-step-2-create-vm-image] de Gestor de Recursos para Linux
* Inicie sessão na sua subscrição com *login az*
* Selecione a sua subscrição com conjunto de *conta az `<subscription name or id` -- subscrição*
* Carregue o VHD com *upload de blob* de armazenamento az - ver Utilizar o [Azure CLI com armazenamento Azure][storage-azure-cli]
* (Opcional) Criar uma imagem de disco gerida a partir do VHD com *imagem az criar* - verhttps://docs.microsoft.com/cli/azure/image
* Crie um novo VM especificando o VHD carregado ou imagem de disco gerido como disco OS com *az vm criar* e parâmetro *--imagem*

**Modelo**

* Utilize *sysprep* no Windows ou *waagent -deprovisionamento* no Linux para generalizar o seu VM - consulte [Sysprep Referência Técnica](https://technet.microsoft.com/library/cc766049.aspx) para Windows ou Como capturar uma máquina virtual [Linux para usar como modelo][capture-image-linux-step-2-create-vm-image] de Gestor de Recursos para Linux
* Faça upload do VHD com PowerShell ou Azure CLI
* (Opcional) Criar uma imagem de disco gerida a partir do VHD com PowerShell, Azure CLI ou o portal Azure
* Desloque o VM com um modelo JSON referindo-se ao VHD de imagem como mostrado [neste modelo JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) exemplo ou utilizando a Imagem de Disco Gerida, como mostrado [neste modelo JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)exemplo .

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Descarregar VHDs ou Discos Geridos para o local
A Azure Infrastructure como Serviço não é uma rua de sentido único de ser apenas capaz de carregar vHDs e sistemas SAP. Também pode mover os sistemas SAP do Azure para o mundo das instalações.

Durante o período de download, os VHDs ou Discos Geridos não podem estar ativos. Mesmo quando descarrega discos, que são montados para VMs, o VM precisa de ser desligado e transferido. Se quiser apenas descarregar o conteúdo da base de dados, que, então, deve ser utilizado para instalar um novo sistema no local e se for aceitável que durante o tempo de download e a configuração do novo sistema que o sistema em Azure ainda possa estar operacional, poderá evitar um longo período de inatividade através da realização de uma cópia de dados comprimido num disco e apenas descarregar esse disco em vez de descarregar também o VM base do OS.

#### <a name="powershell"></a>PowerShell

* Descarregar um disco gerido  
  Primeiro é necessário ter acesso à bolha subjacente do Disco Gerido. Em seguida, pode copiar a bolha subjacente a uma nova conta de armazenamento e descarregar a bolha desta conta de armazenamento.

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

* Descarregar um VHD  
  Uma vez desligado o sistema SAP e o VM desligado, pode utilizar o powerShell cmdlet Save-AzVhd no alvo no local para descarregar os discos VHD de volta para o mundo no local. Para isso, é necessário o URL do VHD, que pode encontrar na 'secção de armazenamento' do portal Azure (precisa de navegar para a Conta de Armazenamento e para o recipiente de armazenamento onde o VHD foi criado) e precisa de saber para onde deve ser copiado o VHD.

  Em seguida, pode alavancar o comando definindo o parâmetro SourceUri como o URL do VHD para descarregar e o LocalFilePath como a localização física do VHD (incluindo o seu nome). O comando pode parecer:

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Para mais detalhes sobre o cmdlet Save-AzVhd, consulte aqui <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>.

#### <a name="azure-cli"></a>CLI do Azure
* Descarregar um disco gerido  
  Primeiro é necessário ter acesso à bolha subjacente do Disco Gerido. Em seguida, pode copiar a bolha subjacente a uma nova conta de armazenamento e descarregar a bolha desta conta de armazenamento.

  ```azurecli
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Descarregar um VHD   
  Uma vez que o sistema SAP é parado e o VM é desligado, você pode usar o download de blob de _armazenamento azure_ de comando Azure CLI no alvo no local para descarregar os discos VHD de volta para o mundo no local. Para isso, precisa do nome e do recipiente do VHD, que pode encontrar na 'Secção de Armazenamento' do portal Azure (precisa de navegar para a Conta de Armazenamento e o recipiente de armazenamento onde o VHD foi criado) e precisa de saber para onde deve ser copiado o VHD.

  Em seguida, pode aproveitar o comando definindo a bolha de parâmetros e o recipiente do VHD para descarregar e o destino como a localização do alvo físico do VHD (incluindo o seu nome). O comando pode parecer:

  ```azurecli
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Transferência de VMs e discos dentro do Azure

#### <a name="copying-sap-systems-within-azure"></a>Copiar sistemas SAP dentro do Azure

Um sistema SAP ou mesmo um servidor DBMS dedicado que suporte uma camada de aplicação SAP provavelmente consistirá em vários discos, que contêm o OS com os binários ou os dados e ficheiros de registo da base de dados SAP. Nem a funcionalidade Azure de copiar discos nem a funcionalidade Azure de guardar discos para um disco local tem um mecanismo de sincronização, que instantâneos múltiplos discos de forma consistente. Portanto, o estado dos discos copiados ou guardados mesmo que estes sejam montados contra o mesmo VM seria diferente. Isto significa que, no caso concreto de ter dados e ficheiros de registo diferentes contidos nos diferentes discos, a base de dados acabaria por ser inconsistente.

**Conclusão: Para copiar ou guardar discos, que fazem parte de uma configuração do sistema SAP, é necessário parar o sistema SAP e também ter de desligar o VM implantado. Só assim poderá copiar ou descarregar o conjunto de discos para criar uma cópia do sistema SAP em Azure ou no local.**

Os discos de dados podem ser armazenados como ficheiros VHD numa Conta de Armazenamento Azure e podem ser diretamente ligados a uma máquina virtual ou ser usados como imagem. Neste caso, o VHD é copiado para outro local antes de ser ligado à máquina virtual. O nome completo do ficheiro VHD em Azure deve ser único dentro do Azure. Como já foi mencionado anteriormente, o nome é uma espécie de nome em três partes que parece:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Os discos de dados também podem ser Discos Geridos. Neste caso, o Disco Gerido é utilizado para criar um novo Disco Gerido antes de ser ligado à máquina virtual. O nome do Disco Gerido deve ser único dentro de um grupo de recursos.

##### <a name="powershell"></a>PowerShell

Pode utilizar cmdlets Azure PowerShell para copiar um VHD, como mostrado [neste artigo][storage-powershell-guide-full-copy-vhd]. Para criar um novo Disco Gerido, utilize o New-AzDiskConfig e o New-AzDisk, como mostra o exemplo seguinte.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>CLI do Azure

Pode utilizar o Azure CLI para copiar um VHD. Para criar um novo Disco Gerido, utilize o *disco az criar* como mostrado no exemplo seguinte.

```azurecli
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Ferramentas de armazenamento Azure

* <https://storageexplorer.com/>

Edições profissionais dos Exploradores de Armazenamento Azure podem ser consultadas aqui:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

A cópia de um VHD em si dentro de uma conta de armazenamento é um processo, que leva apenas alguns segundos (semelhante ao hardware SAN criando instantâneos com cópia preguiçosa e cópia por escrito). Depois de ter uma cópia do ficheiro VHD, pode fixá-lo a uma máquina virtual ou usá-lo como imagem para anexar cópias do VHD a máquinas virtuais.

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

```azurecli

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

#### <a name="copying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Copiar discos entre contas de armazenamento azure
Esta tarefa não pode ser executada no portal Azure. Pode utilizar cmdlets Azure PowerShell, Azure CLI ou um navegador de armazenamento de terceiros. Os comandos PowerShell cmdlets ou CLI podem criar e gerir bolhas, que incluem a capacidade de copiar assincronicamente bolhas através de Contas de Armazenamento e em todas as regiões dentro da subscrição Azure.

##### <a name="powershell"></a>PowerShell
Também pode copiar VHDs entre subscrições. Para mais informações, leia [este artigo.][storage-powershell-guide-full-copy-vhd]

O fluxo básico da lógica cmdlet ps parece assim:

* Criar um contexto de conta de armazenamento para a conta de armazenamento **de fonte** com *New-AzStorageContext* - ver<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Criar um contexto de conta de armazenamento para a conta de armazenamento **alvo** com *New-AzStorageContext* - ver<https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Iniciar a cópia com

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Verifique o estado da cópia em loop com

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Fixe o novo VHD a uma máquina virtual, tal como acima descrito.

Por exemplo, veja [este artigo.][storage-powershell-guide-full-copy-vhd]

##### <a name="azure-cli"></a>CLI do Azure
* Iniciar a cópia com

```azurecli
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Verifique o estado se a cópia ainda estiver em ciclo com

```azurecli
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Fixe o novo VHD a uma máquina virtual, tal como acima descrito.

### <a name="disk-handling"></a>Manuseamento de discos

#### <a name="vmdisk-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Estrutura VM/disco para implementações SAP

Idealmente, o manuseamento da estrutura de um VM e dos discos associados deve ser simples. Nas instalações no local, os clientes desenvolveram muitas formas de estruturar uma instalação de servidor.

* Um disco base, que contém o S e todos os binários do DBMS e/ou SAP. Desde março de 2015, este disco pode ter até 1TB de tamanho em vez de restrições anteriores que o limitaram a 127GB.
* Um ou vários discos, que contêm o ficheiro de registo DBMS da base de dados SAP e o ficheiro de registo da área de armazenamento temporário DBMS (se o DBMS o suportar). Se os requisitos do IOPS de registo de base de dados forem elevados, é necessário riscar vários discos para atingir o volume iopS exigido.
* Vários discos que contêm um ou dois ficheiros de base de dados da base de dados SAP e os ficheiros de dados temporários DBMS (se o DBMS o suportar).

![Configuração de referência do VM Azure IaaS para SAP][planning-guide-figure-1300]


---
> ![Windows][Logo_Windows] Windows
>
> Com muitos clientes vimos configurações onde, por exemplo, os binários SAP e DBMS não foram instalados no c:\ unidade onde o Sistema operativo foi instalado. Havia várias razões para isso, mas quando voltámos à raiz, normalmente era que as unidades eram pequenas e as atualizações do SO precisavam de espaço adicional há 10-15 anos. Ambas as condições já não se aplicam com demasiada frequência. Hoje o c:\ a unidade pode ser mapeada em discos de grande volume ou VMs. Para manter as implementações simples na sua estrutura, recomenda-se seguir o seguinte padrão de implementação para sistemas SAP NetWeaver em Azure
>
> O ficheiro de página do sistema operativo Windows deve estar no D: unidade (disco não persistente)
>
> ![Linux][Logo_Linux] Linux
>
> Coloque o swap file Linux sob /mnt /mnt/recurso em Linux, conforme descrito [neste artigo][virtual-machines-linux-agent-user-guide]. O ficheiro swap pode ser configurado no ficheiro de configuração do Agente Linux /etc/waagent.conf. Adicione ou altere as seguintes definições:
>
>

```console
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Para ativar as alterações, precisa reiniciar o Agente Linux com

```console
sudo service waagent restart
```

Leia a Nota [SAP 1597355] para obter mais detalhes sobre o tamanho recomendado do ficheiro swap

---
O número de discos utilizados para os ficheiros de dados DBMS e o tipo de Armazenamento Azure em que estes discos estão hospedados deve ser determinado pelos requisitos do IOPS e pela latência necessária. As quotas exatas são descritas [neste artigo (Linux)][virtual-machines-sizes-linux] e [neste artigo (Windows)][virtual-machines-sizes-windows].

A experiência das implementações da SAP nos últimos dois anos ensinou-nos algumas lições, que podem ser resumidas como:

* O tráfego iOPS para diferentes ficheiros de dados nem sempre é o mesmo, uma vez que os sistemas de clientes existentes podem ter ficheiros de dados de tamanho diferente que representam a sua base de dados SAP. Como resultado, acabou por ser melhor usando uma configuração RAID sobre vários discos para colocar os ficheiros de dados LUNs esculpidos desses. Houve situações, especialmente com o Azure Standard Storage, em que uma taxa IOPS atingiu a quota de um único disco contra o registo de transações DBMS. Nestes cenários, recomenda-se a utilização do Armazenamento Premium ou, em alternativa, agregação de vários discos standard de armazenamento com uma risca de software.

---
> ![Windows][Logo_Windows] Windows
>
> * [Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Configure Software RAID em Linux][virtual-machines-linux-configure-raid]
> * [Configure LVM em um Linux VM em Azure][virtual-machines-linux-configure-lvm]
> * [Segredos de Armazenamento Azure e otimizações de I/O linux](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

---
* O Armazenamento Premium está a mostrar um desempenho significativo e melhor, especialmente para os escritos de registo de transações críticas. Para cenários SAP que se espera que proporcionem produção como desempenho, é altamente recomendado utilizar vM-Series que pode alavancar o Armazenamento Premium Azure.

Tenha em mente que o disco, que contém o S, e como recomendamos, os binários do SAP e a base de dados (Base VM) também não se limitam a 127GB. Agora pode ter até 1TB de tamanho. Este deve ser espaço suficiente para manter todos os ficheiros necessários, incluindo, por exemplo, registos de trabalho em lotes SAP.

Para mais sugestões e mais detalhes, especificamente para VMs DBMS, consulte o Guia de [Implementação dbms][dbms-guide]

#### <a name="disk-handling"></a>Manuseamento de discos

Na maioria dos cenários, é necessário criar discos adicionais para implantar a base de dados SAP no VM. Falámos sobre as considerações sobre o número de discos no capítulo [VM/estrutura de disco para as implementações sap][planning-guide-5.5.1] deste documento. O portal Azure permite anexar e separar discos uma vez que um VM base é implantado. Os discos podem ser fixados/separados quando o VM está a funcionar, bem como quando este é parado. Ao ligar um disco, o portal Azure oferece-se para fixar um disco vazio ou um disco existente, que neste momento não está ligado a outro VM.

**Nota:** Os discos só podem ser ligados a um VM a qualquer momento.

![Anexar / separar discos com armazenamento padrão Azure][planning-guide-figure-1400]

Durante a implementação de uma nova máquina virtual, pode decidir se pretende utilizar Discos Geridos ou colocar os seus discos nas Contas de Armazenamento Azure. Se pretender utilizar o Armazenamento Premium, recomendamos a utilização de Discos Geridos.

Em seguida, você precisa decidir se você quer criar um disco novo e vazio ou se você quer selecionar um disco existente que foi carregado mais cedo e deve ser anexado ao VM agora.

**IMPORTANTE**: **Não** pretende utilizar o Hospedeiro Caching com armazenamento padrão Azure. Deve deixar a preferência de Cache hospedeira no padrão de NONE. Com o Armazenamento Azure Premium, deve ativar o Read Caching se a característica de I/O for lida principalmente como tráfego típico de I/S contra ficheiros de dados de base de dados. No caso de ficheiro de registo de transações de base de dados, não é recomendado qualquer cache.

---
> ![Windows][Logo_Windows] Windows
>
> [Como anexar um disco de dados no portal Azure][virtual-machines-linux-attach-disk-portal]
>
> Se os discos estiverem ligados, tem de iniciar sessão no VM para abrir o Windows Disk Manager. Se a montagem automática não estiver ativada como recomendado no capítulo [Definição de montagem automática para discos ligados,][planning-guide-5.5.3]o volume recém-ligado deve ser tomado online e inicializado.
>
> ![Linux][Logo_Linux] Linux
>
> Se os discos estiverem ligados, tem de iniciar sessão no VM e rubricar os discos descritos [neste artigo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

---
Se o disco novo for um disco vazio, também é necessário formatar o disco. Para a formatação, especialmente para dados DBMS e ficheiros de registo, aplicam-se as mesmas recomendações que para as implementações de metal nu do DBMS.

Uma conta de Armazenamento Azure não fornece recursos infinitos em termos de volume de I/O, IOPS e volume de dados. Normalmente, os VMs DBMS são os mais afetados por isto. Talvez seja melhor utilizar uma Conta de Armazenamento separada para cada VM se tiver poucos VMs de volume de I/O elevados para implementar para se manter dentro do limite do volume da Conta de Armazenamento Azure. Caso contrário, precisa de ver como pode equilibrar estes VMs entre diferentes contas de Armazenamento sem atingir o limite de cada conta de armazenamento. Mais detalhes são discutidos no Guia de [Implantação dbms][dbms-guide]. Deve também ter em mente estas limitações para vMs de servidor de aplicação SAP puros ou outros VMs, que eventualmente podem exigir VHDs adicionais. Estas restrições não se aplicam se utilizar o Disco Gerido. Se planeia utilizar o Armazenamento Premium, recomendamos a utilização do Disco Gerido.

Outro tópico, que é relevante para contas de armazenamento é se os VHDs numa Conta de Armazenamento estão a ser geo-replicados. A geo-replicação está ativada ou desativada ao nível da Conta de Armazenamento e não ao nível vm. Se a geo-replicação estiver ativada, os VHDs dentro da Conta de Armazenamento seriam replicados em outro centro de dados Azure dentro da mesma região. Antes de decidir sobre isto, deve pensar na seguinte restrição:

A geo-replicação azure funciona localmente em cada VHD num VM e não replica o I/Os em ordem cronológica através de vários VHDs num VM. Por conseguinte, o VHD que representa o VM base, bem como quaisquer VHDs adicionais ligados ao VM são replicados independentemente uns dos outros. Isto significa que não há sincronização entre as mudanças nos diferentes VHDs. O facto de os I/Os serem replicados independentemente da ordem em que estão escritos significa que a geo-replicação não é de valor para servidores de base de dados que têm as suas bases de dados distribuídas por vários VHDs. Além do DBMS, também pode haver outras aplicações em que os processos escrevem ou manipulam dados em diferentes VHDs e onde é importante manter a ordem das alterações. Se isso for um requisito, a geo-replicação em Azure não deve ser ativada. Dependendo se precisa ou quer geo-replicação para um conjunto de VMs, mas não para outro conjunto, já pode categorizar VMs e seus VHDs relacionados em diferentes Contas de Armazenamento que tenham geo-replicação ativado ou desativado.

#### <a name="setting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Configurar a montagem automática para discos ligados
---
> ![Windows][Logo_Windows] Windows
>
> Para VMs, que são criados a partir de imagens próprias ou discos, é necessário verificar e possivelmente definir o parâmetro de montagem automática. A definição deste parâmetro permitirá ao VM depois de reiniciar ou recolocar em Azure para montar as unidades anexadas/montadas automaticamente.
> O parâmetro está definido para as imagens fornecidas pela Microsoft no Mercado Azure.
>
> Para configurar a montagem automática, consulte a documentação do disco executável da linha de comando.exe aqui:
>
> * [Opções de linha de comando diskpart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Montagem automática](https://technet.microsoft.com/library/cc753703.aspx)
>
> A janela da linha de comando windows deve ser aberta como administrador.
>
> Se os discos estiverem ligados, tem de iniciar sessão no VM para abrir o Windows Disk Manager. Se a montagem automática não estiver ativada como recomendado no capítulo [Definição de montagem automática para discos ligados,][planning-guide-5.5.3]o volume recém-ligado >precisa de ser tomado online e inicializado.
>
> ![Linux][Logo_Linux] Linux
>
> É necessário inicializar um disco vazio recém-ligado, tal como descrito [neste artigo.][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
> Também precisa de adicionar discos novos ao /etc/fstab.
>
>

---
### <a name="final-deployment"></a>Implantação Final

Para a implementação final e passos exatos, especialmente no que diz respeito à implantação da extensão Azure para SAP, consulte o Guia de [Implantação][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Aceder aos sistemas SAP em funcionamento dentro de VMs Azure

Para cenários em que pretende ligar-se a esses sistemas SAP através da internet pública utilizando o SAP GUI, é necessário aplicar os seguintes procedimentos.

Mais tarde no documento discutiremos o outro grande cenário, ligando-se aos sistemas SAP em implementações transversais, que têm uma ligação local-a-local (túnel VPN) ou ligação Azure ExpressRoute entre os sistemas no local e os sistemas Azure.

### <a name="remote-access-to-sap-systems"></a>Acesso remoto aos sistemas SAP

Com o Azure Resource Manager, já não existem pontos finais padrão como no antigo modelo clássico. Todos os portos de um VM do Gestor de Recursos Azure estão abertos desde que:

1. Nenhum Grupo de Segurança de Rede está definido para a sub-rede ou para a interface de rede. O tráfego de rede para VMs Azure pode ser assegurado através dos chamados "Grupos de Segurança da Rede". Para obter mais informações, consulte o artigo [O que é um Grupo de Segurança de Rede (NSG)?][virtual-networks-nsg]
2. Nenhum Balancer de carga Azure está definido para a interface de rede   

Veja a diferença de arquitetura entre o modelo clássico e o ARM, como descrito [neste artigo.][virtual-machines-azure-resource-manager-architecture]

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Configuração do Sistema SAP e conectividade SAP GUI através da internet

Por favor, consulte este artigo, que descreve detalhes sobre este tópico:<https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Alterar definições de firewall dentro do VM

Pode ser necessário configurar a firewall nas suas máquinas virtuais para permitir o tráfego de entrada no seu sistema SAP.

---
> ![Windows][Logo_Windows] Windows
>
> Por predefinição, o Windows Firewall dentro de um VM implantado em Azure é ligado. Agora tem de permitir a abertura da Porta SAP, caso contrário o SAP GUI não poderá ligar-se.
> Para efetuar este procedimento:
>
> * Open Control Panel\System and Security\Windows Firewall to **Advanced Settings**.
> * Agora clique à direita nas Regras de Entrada e escolha **New Rule**.
> * No seguinte Feiticeiro optou por criar uma nova regra **do Porto.**
> * No próximo passo do assistente, deixe a definição no TCP e digite o número de porta que pretende abrir. Como a nossa identificação da instância SAP é 00, tomamos 3200. Se a sua instância tiver um número de instância diferente, a porta que definiu anteriormente com base no número da instância deve ser aberta.
> * Na parte seguinte do assistente, tem de deixar verificado o item Permitir a **Ligação.**
> * No próximo passo do assistente é necessário definir se a regra se aplica à rede Dedomínio, Privado e Público. Por favor, ajuste-o se necessário às suas necessidades. No entanto, ligando-se ao SAP GUI de fora através da rede pública, é necessário que a regra seja aplicada à rede pública.
> * No último passo do assistente, diga a regra e guarde premindo **O Acabamento**.
>
> A regra torna-se eficaz imediatamente.
>
> ![Definição de regra do porto][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> As imagens Linux no Mercado Azure não permitem a firewall iptables por padrão e a ligação ao seu sistema SAP deve funcionar. Se ativar os iptables ou outra firewall, consulte a documentação dos iptables ou a firewall utilizada para permitir o tráfego de tCP de entrada para a porta 32xx (onde xx é o número de sistema do seu sistema SAP).
>
>

---
#### <a name="security-recommendations"></a>Recomendações de segurança

O SAP GUI não liga imediatamente a nenhuma das instâncias SAP (porta 32xx) que estão em execução, mas primeiro liga-se através da porta aberta ao processo de servidor de mensagens SAP (porta 36xx). No passado, a mesma porta foi utilizada pelo servidor de mensagens para a comunicação interna às instâncias da aplicação. Para evitar que os servidores de aplicações no local se introdissoçam inadvertidamente com um servidor de mensagens no Azure, as portas de comunicação internas podem ser alteradas. É altamente recomendável alterar a comunicação interna entre o servidor de mensagens SAP e as suas instâncias de aplicação para um número de porta diferente em sistemas que foram clonados a partir de sistemas no local, como um clone de desenvolvimento para testes de projeto, etc. Isto pode ser feito com o parâmetro de perfil padrão:

> rdisp/msserv_internal
>
>

conforme documentado nas Definições de [Segurança para o Servidor de Mensagens SAP](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="single-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>VM único com cenário de demonstração/treino SAP NetWeaver

![Executando sistemas de demonstração VM SAP com os mesmos nomes VM, isolados em Serviços Azure Cloud][planning-guide-figure-1700]

Neste cenário estamos a implementar um cenário típico de sistema de treino/demonstração onde o cenário completo de formação/demonstração está contido num único VM. Assumimos que a implementação é feita através de modelos de imagem VM. Assumimos também que vários destes VMs de demonstração/treino precisam de ser implantados com os VMs com o mesmo nome. Todos os sistemas de treino não têm conectividade com os seus ativos no local e são um oposto a uma implantação híbrida.

O pressuposto é que criou uma Imagem VM como descrito em algumas secções do capítulo [Preparing VMs com SAP para Azure][planning-guide-5.2] neste documento.

A sequência de eventos para implementar o cenário é assim:

##### <a name="powershell"></a>PowerShell

* Criar um novo grupo de recursos para cada paisagem de formação/demonstração

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```

* Crie uma nova conta de armazenamento se não quiser usar Discos Geridos

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Crie uma nova rede virtual para cada paisagem de formação/demonstração para permitir a utilização do mesmo nome de anfitrião e endereços IP. A rede virtual está protegida por um Grupo de Segurança de Rede que apenas permite o tráfego para a porta 3389 para permitir o acesso remoto ao ambiente de trabalho e a porta 22 para SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Crie um novo endereço IP público que possa ser usado para aceder à máquina virtual a partir da internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Cria uma máquina virtual. Para este cenário, cada VM terá o mesmo nome. O Sid SAP dos casos SAP NetWeaver nesses VMs também será o mesmo. Dentro do Grupo de Recursos Azure, o nome do VM tem de ser único, mas em diferentes Grupos de Recursos Azure pode executar VMs com o mesmo nome. A conta 'Administrador' predefinida do Windows ou 'raiz' do Linux não são válidas. Por isso, um novo nome de utilizador de administrador tem de ser definido juntamente com uma palavra-passe. O tamanho do VM também precisa de ser definido.

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

* Adicione opcionalmente discos adicionais e restaure o conteúdo necessário. Todos os nomes blob (URLs para as bolhas) devem ser únicos dentro do Azure.

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

O seguinte código de exemplo pode ser usado no Linux. Para windows, utilize o PowerShell como descrito acima ou adapte o exemplo para utilizar %rgName% em vez de $rgName e desloque a variável ambiental utilizando o *conjunto*de comandos Windows .

* Criar um novo grupo de recursos para cada paisagem de formação/demonstração

```azurecli
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Criar uma nova conta de armazenamento

```azurecli
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Crie uma nova rede virtual para cada paisagem de formação/demonstração para permitir a utilização do mesmo nome de anfitrião e endereços IP. A rede virtual está protegida por um Grupo de Segurança de Rede que apenas permite o tráfego para a porta 3389 para permitir o acesso remoto ao ambiente de trabalho e a porta 22 para SSH.

```azurecli
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Crie um novo endereço IP público que possa ser usado para aceder à máquina virtual a partir da internet

```azurecli
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```azurecli
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Cria uma máquina virtual. Para este cenário, cada VM terá o mesmo nome. O Sid SAP dos casos SAP NetWeaver nesses VMs também será o mesmo. Dentro do Grupo de Recursos Azure, o nome do VM tem de ser único, mas em diferentes Grupos de Recursos Azure pode executar VMs com o mesmo nome. A conta 'Administrador' predefinida do Windows ou 'raiz' do Linux não são válidas. Por isso, um novo nome de utilizador de administrador tem de ser definido juntamente com uma palavra-passe. O tamanho do VM também precisa de ser definido.

```azurecli
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

```azurecli
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

* Adicione opcionalmente discos adicionais e restaure o conteúdo necessário. Todos os nomes blob (URLs para as bolhas) devem ser únicos dentro do Azure.

```azurecli
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Modelo

Pode utilizar os modelos de amostra no repositório de modelos de arranque rápido no GitHub.

* [VM Linux Simples](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [VM windows simples](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VM da imagem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementar um conjunto de VMs que comunicam dentro do Azure

Este cenário não-híbrido é um cenário típico para fins de treino e demonstração onde o software que representa o cenário de demonstração/treino está espalhado por vários VMs. Os diferentes componentes instalados nos diferentes VMs precisam de comunicar entre si. Mais uma vez, neste cenário não é necessário nenhum cenário de comunicação de rede no local ou de instalações cruzadas.

Este cenário é uma extensão da instalação descrita no capítulo Single VM com cenário de [demonstração/formação SAP NetWeaver][planning-guide-7.1] deste documento. Neste caso, mais máquinas virtuais serão adicionadas a um grupo de recursos existente. No exemplo seguinte, a paisagem de formação consiste num SAP ASCS/SCS VM, um VM que executa um DBMS e um VM de servidor de aplicação SAP.

Antes de construir este cenário, precisa de pensar em configurações básicas como já exercido no cenário anterior.

#### <a name="resource-group-and-virtual-machine-naming"></a>Grupo de Recursos e Nomeação de Máquina Virtual

Todos os nomes de grupos de recursos devem ser únicos. Desenvolva o seu próprio esquema `<rg-name` de nomeação dos seus recursos, como>-sufixo.

O nome da máquina virtual tem de ser único dentro do grupo de recursos.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Criar rede de comunicação entre os diferentes VMs

![Conjunto de VMs dentro de uma Rede Virtual Azure][planning-guide-figure-1900]

Para evitar o nome de colisões com clones das mesmas paisagens de formação/demonstração, é necessário criar uma Rede Virtual Azure para cada paisagem. A resolução de nomes DNS será fornecida pelo Azure ou pode configurar o seu próprio servidor DNS fora do Azure (não deve ser discutido aqui). Neste cenário, não configuramos o nosso próprio DNS. Para todas as máquinas virtuais dentro de uma Rede Virtual Azure, a comunicação através de nomes de anfitriões será ativada.

As razões para separar as paisagens de formação ou dedemonstração por redes virtuais e não apenas grupos de recursos poderiam ser:

* A paisagem SAP tal como criada precisa do seu próprio AD/OpenLDAP e um Servidor de Domínio precisa de fazer parte de cada uma das paisagens.  
* A paisagem SAP tal como configurada tem componentes que precisam de trabalhar com endereços IP fixos.

Mais detalhes sobre redes virtuais Azure e como defini-las podem ser encontradas [neste artigo.][virtual-networks-create-vnet-arm-pportal]

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Implantação de VMs SAP com Conectividade de Rede Corporativa (Cross-Premises)

Você executa uma paisagem SAP e quer dividir a implementação entre o metal nu para servidores DBMS de alta qualidade, ambientes virtualizados no local para camadas de aplicação, e sistemas SAP configurados de 2 camadas menores e Azure IaaS. A base pressupõe que os sistemas SAP dentro de uma paisagem SAP precisam de comunicar entre si e com muitos outros componentes de software implantados na empresa, independentemente do seu formulário de implantação. Também não devem existir diferenças introduzidas pelo formulário de implementação para o utilizador final que se conecta com o SAP GUI ou outras interfaces. Estas condições só podem ser satisfeitas quando dispomos dos serviços Ative Directory/OpenLDAP e DNS estendidos aos sistemas Azure através da conectividade site-to-site/multi-site ou ligações privadas como o Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Cenário de uma paisagem SAP

As premissas cruzadas ou o cenário híbrido podem ser descritos aproximadamente como nos gráficos abaixo:

![Conectividade local-a-local entre ativos no local e ativos do Azure][planning-guide-figure-2100]

O requisito mínimo é a utilização de protocolos de comunicação seguros, tais como SSL/TLS para acesso ao navegador ou ligações baseadas em VPN para acesso ao sistema aos serviços Azure. O pressuposto é que as empresas lidam com a ligação VPN entre a sua rede corporativa e o Azure de forma diferente. Algumas empresas podem abrir em branco todos os portos. Algumas outras empresas podem querer ser precisas em que portos precisam de abrir, etc.

Na tabela abaixo estão listadas as portas de comunicação Típicas SAP. Basicamente, basta abrir a porta de entrada SAP.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Serviço | Nome da Porta | Exemplo `<nn`> = 01 | Gama Padrão (min-max) | Comentário |
| --- | --- | --- | --- | --- |
| Expedidor |sapdp`<nn>` ver * |3201 |3200 - 3299 |Despacho SAP, usado pela SAP GUI para Windows e Java |
| Servidor de mensagem |sapms`<sid`> ver ** |3600 |sapms grátis`<anySID`> |sid = SAP-System-ID |
| Gateway |sapgw`<nn`> ver * |3301 |livre |Gateway SAP, usado para comunicação CPIC e RFC |
| Router SAP |sapdp99 |3299 |livre |Apenas os nomes de serviço ci (instância central) podem ser reatribuídos em /etc/serviços a um valor arbitrário após a instalação. |

*) nn = Número de instância SAP

**) sid = SAP-System-ID

Informações mais detalhadas sobre as portas necessárias para diferentes produtos <https://scn.sap.com/docs/DOC-17124>ou serviços SAP por produtos SAP podem ser consultados aqui .
Com este documento, deverá ser capaz de abrir portas dedicadas no dispositivo VPN necessários para produtos e cenários SAP específicos.

Outras medidas de segurança ao implementar Em tal cenário, poderão ser a criação de um Grupo de Segurança de [Rede][virtual-networks-nsg] para definir as regras de acesso.



#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Impressão em uma impressora de rede local a partir da instância SAP em Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Impressão sobre TCP/IP em cenário cross-premises

A instalação das suas impressoras de rede baseadas em TCP/IP no local num Azure VM é, no geral, a mesma que na sua rede corporativa, assumindo que tem um túnel VPN Site-To-Site ou ligação ExpressRoute estabelecido.

---
> ![Windows][Logo_Windows] Windows
>
> Para efetuar este procedimento:
>
> * Algumas impressoras de rede vêm com um assistente de configuração que facilita a configuração da sua impressora num VM Azure. Se nenhum software de assistente foi distribuído com a impressora, a forma manual de configurar a impressora é criar uma nova porta de impressora TCP/IP.
> * Painel de controlo aberto -> dispositivos e impressoras -> Adicione uma impressora
> * Escolha Adicionar uma impressora usando um endereço TCP/IP ou nome de anfitrião
> * Digite o endereço IP da impressora
> * Norma 9100 da porta da impressora
> * Se necessário, instale manualmente o controlador de impressora apropriado.
>
> ![Linux][Logo_Linux] Linux
>
> * como para o Windows basta seguir o procedimento padrão para instalar uma impressora de rede
> * basta seguir os guias públicos de Linux para [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) ou [Red Hat e Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) sobre como adicionar uma impressora.
>
>

---
![Impressão de rede][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Impressora baseada em hospedeiro sobre SMB (impressora partilhada) no cenário Cross-Premises

As impressoras baseadas em hospedeiros não são compatíveis com a rede por design. Mas uma impressora baseada em hospedeiros pode ser partilhada entre computadores numa rede, desde que a impressora esteja ligada a um computador alimentado. Conecte a sua rede corporativa no Site-To-Site ou expressRoute e partilhe a sua impressora local. O protocolo SMB utiliza o NetBIOS em vez de DNS como serviço de nome. O nome de anfitrião NetBIOS pode ser diferente do nome de anfitrião DNS. O caso padrão é que o nome de anfitrião NetBIOS e o nome de anfitrião DNS são idênticos. O domínio DNS não faz sentido no espaço de nome NetBIOS. Por conseguinte, o nome de anfitrião dNS totalmente qualificado, constituído pelo nome de anfitrião DNS e pelo domínio DNS, não deve ser utilizado no espaço de nome NetBIOS.

A partilha da impressora é identificada por um nome único na rede:

* Nome anfitrião do hospedeiro SMB (sempre necessário).
* Nome da parte (sempre necessário).
* Nome do domínio se a partilha da impressora não estiver no mesmo domínio que o sistema SAP.
* Além disso, um nome de utilizador e uma palavra-passe podem ser necessários para aceder à partilha da impressora.

Como:

---
> ![Windows][Logo_Windows] Windows
>
> Partilhe a sua impressora local.
> No Azure VM, abra o Windows Explorer e escreva o nome de partilha da impressora.
> Um assistente de instalação de impressora irá guiá-lo através do processo de instalação.
>
> ![Linux][Logo_Linux] Linux
>
> Aqui estão alguns exemplos de documentação sobre configurar impressoras de rede em Linux ou incluindo um capítulo sobre impressão em Linux. Funcionará da mesma forma num VM Azure Linux, desde que o VM faça parte de uma VPN:
>
> * SLES<https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL ou Oracle Linux<https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>Impressora USB (encaminhamento da impressora)

No Azure não está disponível a capacidade dos Serviços de Desktop Remoto para fornecer aos utilizadores o acesso aos seus dispositivos de impressora local numa sessão remota.

---
> ![Windows][Logo_Windows] Windows
>
> Mais detalhes sobre a impressão com <https://technet.microsoft.com/library/jj590748.aspx>windows podem ser encontrados aqui: .
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integração dos Sistemas SAP Azure no Sistema de Correção e Transporte (TMS) em Cross-Premises

O Sistema de Mudança e Transporte SAP (TMS) tem de ser configurado para exportar e importar pedidos de transporte através de sistemas da paisagem. Assumimos que as instâncias de desenvolvimento de um sistema SAP (DEV) estão localizadas em Azure, enquanto que a garantia de qualidade (QA) e os sistemas produtivos (PRD) estão no local. Além disso, assumimos que existe um diretório central dos transportes.

##### <a name="configuring-the-transport-domain"></a>Configurar o Domínio de Transporte

Configure o seu domínio de transporte no sistema designado como Controlador de Domínio de Transporte, conforme descrito na [configuração do controlador de domínio de transporte](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Será criado um utilizador do sistema TMSADM e o destino RFC necessário será gerado. Pode verificar estas ligações RFC utilizando a transação SM59. A resolução do nome de anfitrião deve ser ativada em todo o seu domínio de transporte.

Como:

* No nosso cenário, decidimos que o sistema QAS no local será o controlador de domínio CTS. Chamada transação STMS. A caixa de diálogo TMS aparece. É apresentada uma caixa de diálogo de domínio de transporte configurada. (Esta caixa de diálogo só aparece se ainda não tiver configurado um domínio de transporte.)
* Certifique-se de que o utilizador tMSADM, criado automaticamente, está autorizado (SM59 -> ABAP Connection -> TMSADM@E61.DOMAIN_E61 -> Details -> Utilities(M) -> Authorization Test). O ecrã inicial da transação STMS deve mostrar que este Sistema SAP está agora a funcionar como controlador do domínio de transporte, como mostrado aqui:

![Ecrã inicial da transação STMS no controlador de domínio][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Incluindo sistemas SAP no domínio de transporte

A sequência de incluir um sistema SAP num domínio de transporte é a seguinte:

* No sistema DEV em Azure, vá ao sistema de transporte (Cliente 000) e ligue para a transação STMS. Escolha outra configuração da caixa de diálogo e continue com incluir o Sistema no Domínio. Especifique o Controlador de Domínio como hospedeiro-alvo[(incluindo sistemas SAP no domínio de transporte](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). O sistema está agora à espera de ser incluído no domínio dos transportes.
* Por razões de segurança, terá de voltar ao controlador de domínio para confirmar o seu pedido. Escolha a visão geral do sistema e aprove o sistema de espera. Em seguida, confirme o pedido e a configuração será distribuída.

Este sistema SAP contém agora as informações necessárias sobre todos os outros sistemas SAP no domínio de transporte. Ao mesmo tempo, os dados de endereço do novo sistema SAP são enviados para todos os outros sistemas SAP, e o sistema SAP é inserido no perfil de transporte do programa de controlo de transportes. Verifique se os RFCs e o acesso ao diretório de transporte do trabalho de domínio.

Continue com a configuração do seu sistema de transporte, como habitualmente descrito na documentação [Sistema de Alteração e Transporte.](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)

Como:

* Certifique-se de que o Seu STMS no local está configurado corretamente.
* Certifique-se de que o nome de anfitrião do Controlador de Domínio de Transporte pode ser resolvido pela sua máquina virtual em Azure e vice-visto.
* Chamada transação STMS -> Outra configuração -> incluir o sistema no domínio.
* Confirme a ligação no sistema TMS no local.
* Configure rotas de transporte, grupos e camadas, como de costume.

Nos cenários inter-locais ligados ao local, a latência entre as instalações e o Azure ainda pode ser substancial. Se seguirmos a sequência de transporte de objetos através de sistemas de desenvolvimento e teste para a produção ou pensarmos em aplicar transportes ou pacotes de suporte aos diferentes sistemas, percebe-se que, dependendo da localização do diretório central de transportes, alguns dos sistemas encontrarão dados de leitura ou escrita de alta latência no diretório central de transportes. A situação é semelhante às configurações da paisagem SAP onde os diferentes sistemas são espalhados por diferentes centros de dados com uma distância substancial entre os centros de dados.

Para contornar essa latência e fazer com que os sistemas funcionem rapidamente na leitura ou escrita de ou para o diretório de transportes, pode criar dois domínios de transporte STMS (um para instalações e outro com os sistemas em Azure e ligar os domínios de transporte. Verifique esta documentação, que explica os princípios por detrás <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>deste conceito no SAP TMS: .

Como:

* Criar um domínio de transporte em cada local (no local e no Azure) utilizando a transação STMS<https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Ligue os domínios a uma ligação de domínio e confirme a ligação entre os dois domínios.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribua a configuração para o sistema ligado.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Tráfego de RFC entre instâncias SAP localizadas em Azure e no local (Cross-Premises)

O tráfego de RFC entre sistemas, que estão no local e em Azure, tem de funcionar. Para configurar uma transação de chamada de ligação SM59 num sistema de origem onde é necessário definir uma ligação RFC para o sistema alvo. A configuração é semelhante à configuração padrão de uma Ligação RFC.

Assumimos que no cenário transversal, os VMs, que executam sistemas SAP que precisam de comunicar entre si, estão no mesmo domínio. Por conseguinte, a criação de uma ligação RFC entre sistemas SAP não difere das etapas de configuração e dos inputs em cenários no local.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Acesso a fileshares locais de instâncias SAP localizadas em Azure ou vice-versa

As instâncias SAP localizadas no Azure precisam de aceder a ações de ficheiros, que se encontram dentro das instalações corporativas. Além disso, as instâncias SAP no local precisam de aceder a ações de ficheiros, que estão localizadas em Azure. Para ativar as partilhas de ficheiros, tem de configurar as permissões e partilhar opções no sistema local. Certifique-se de que abre as portas na ligação VPN ou ExpressRoute entre o Azure e o seu datacenter.

## <a name="supportability"></a>Suportabilidade

### <a name="azure-extension-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Extensão Azure para SAP

Para alimentar alguma parte das informações de infraestrutura Azure dos sistemas SAP críticos da missão para as instâncias do Agente anfitrião SAP, instalados em VMs, uma extensão Azure (VM) para SAP precisa de ser instalada para os VMs implantados. Uma vez que as exigências da SAP eram específicas das aplicações SAP, a Microsoft decidiu não implementar genericamente a funcionalidade necessária para o Azure, mas deixou-a para os clientes implementarem a extensão e configurações vM necessárias para as suas Máquinas Virtuais em funcionamento no Azure. No entanto, a implementação e gestão do ciclo de vida da extensão Azure VM para SAP será maioritariamente automatizada pelo Azure.

#### <a name="solution-design"></a>Design de solução

A solução desenvolvida para permitir que o Agente anfitrião SAP obtemos as informações necessárias baseia-se na arquitetura do Agente Azure VM e da estrutura de extensão. A ideia do agente e ampliação do Azure VM é permitir a instalação de aplicações de software disponíveis na galeria de extensão Azure VM dentro de um VM. A ideia principal por trás deste conceito é permitir (em casos como a Extensão Azure para SAP), a implementação de uma funcionalidade especial num VM e a configuração de tal software no momento da implementação.

O 'Agente Azure VM' que permite o manuseamento de extensões VM específicas do Azure dentro do VM é injetado em VMs windows por padrão na criação de VM no portal Azure. No caso de SUSE, Red Hat ou Oracle Linux, o agente VM já faz parte da imagem do Azure Marketplace. No caso de enviar um VM Linux do local para o Azure, o agente VM tem de ser instalado manualmente.

Os blocos básicos de construção da solução para fornecer informações sobre infraestruturas Azure ao agente hospedeiro SAP em Azure são assim:

![Componentes de extensão Microsoft Azure][planning-guide-figure-2400]

Como mostra o diagrama de blocoacima, uma parte da solução está hospedada na Azure VM Image e Azure Extension Gallery, que é um repositório globalmente replicado que é gerido pela Azure Operations. É da responsabilidade da equipa conjunta da SAP/MS trabalhar na implementação do Azure da SAP para trabalhar com a Azure Operations para publicar novas versões da Extensão Azure para sAP.

Quando implementa um novo VM do Windows, o Agente VM Azure é automaticamente adicionado ao VM. A função deste agente é coordenar o carregamento e configuração das extensões Azure dos VMs. Para os VMs Linux, o Agente Azure VM já faz parte da imagem do Azure Marketplace OS.

No entanto, há um passo que ainda precisa de ser executado pelo cliente. Esta é a ativação e configuração da coleção de desempenho. O processo relacionado com a configuração é automatizado por um script PowerShell ou comando CLI. O script PowerShell pode ser descarregado no Microsoft Azure Script Center, conforme descrito no Guia de [Implementação][deployment-guide].

A arquitetura geral da extensão Azure para SAP parece:

![Extensão Azure para SAP ][planning-guide-figure-2500]

**Para as medidas exatas de como e para as etapas detalhadas da utilização destes cmdlets PowerShell ou comando CLI durante as implementações, siga as instruções dadas no Guia de [Implantação][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integração do Azure localizada na Instância SAP em SAProuter

As instâncias SAP que correm em Azure também precisam de ser acessíveis a partir de SAProuter.

![Conexão de rede SAP-Router][planning-guide-figure-2600]

Um SAProuter permite a comunicação TCP/IP entre os sistemas participantes se não houver uma ligação IP direta. Isto proporciona a vantagem de que não é necessária uma ligação de ponta a ponta entre os parceiros de comunicação a nível da rede. O SAProuter está a ouvir na porta 3299 por defeito.
Para ligar as instâncias SAP através de um SAProuter, tem de dar o fio SAProuter e o nome do anfitrião com qualquer tentativa de ligação.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java

Até agora, o foco do documento foi o SAP NetWeaver em geral ou a pilha SAP NetWeaver ABAP. Nesta pequena secção, estão listadas considerações específicas para a pilha SAP Java. Uma das aplicações exclusivamente baseadas no SAP NetWeaver Java é o Portal da Empresa SAP. Outras aplicações baseadas em SAP NetWeaver, como SAP PI e SAP Solution Manager, utilizam as pilhas SAP NetWeaver ABAP e Java. Portanto, é certamente necessário considerar aspetos específicos relacionados com a pilha De Java SAP NetWeaver também.

### <a name="sap-enterprise-portal"></a>Portal da Empresa SAP

A configuração de um Portal SAP numa Máquina Virtual Azure não difere de uma instalação no local se estiver a implantar em cenários transversais. Uma vez que o DNS é feito por instalações, as definições de porta das instâncias individuais podem ser feitas conforme configurado no local. As recomendações e restrições descritas neste documento até agora aplicam-se a uma aplicação como o Portal da Empresa SAP ou a pilha SAP NetWeaver Java em geral.

![Portal SAP exposto][planning-guide-figure-2700]

Um cenário especial de implantação por parte de alguns clientes é a exposição direta do Portal da Empresa SAP à Internet, enquanto o anfitrião da máquina virtual está ligado à rede da empresa através do túnel VPN site-to-site ou ExpressRoute. Para tal cenário, tem de se certificar de que as portas específicas estão abertas e não bloqueadas por firewall ou grupo de segurança de rede. 

O portal inicial URI é`<Portalserver` http(s):>:5XX00/irj onde o porto <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>é formado como documentado pela SAP em .

![Configuração de ponto final][planning-guide-figure-2800]

Se pretender personalizar o URL e/ou portas do seu Portal empresarial SAP, verifique esta documentação:

* [Alterar URL do Portal](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Alterar números de porta padrão, números porta-porta do Portal](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Alta Disponibilidade (HA) e Recuperação de Desastres (DR) para SAP NetWeaver em execução em Máquinas Virtuais Azure

### <a name="definition-of-terminologies"></a>Definição de terminologias

O termo **elevada disponibilidade (HA)** está geralmente relacionado com um conjunto de tecnologias que minimizam as perturbações de TI, fornecendo continuidade de negócio dos serviços de TI através de componentes redundantes, tolerantes a falhas ou failover protegidos dentro do **mesmo** centro de dados. No nosso caso, dentro de uma região de Azure.

**A recuperação de desastres (DR)** também tem como alvo minimizar a perturbação dos serviços de TI, e a sua recuperação, mas em **diferentes** centros de dados, que normalmente estão localizados a centenas de quilómetros de distância. No nosso caso, normalmente, entre diferentes regiões azure dentro da mesma região geopolítica ou estabelecidapor si como cliente.

### <a name="overview-of-high-availability"></a>Visão geral da Alta Disponibilidade

Podemos separar a discussão sobre a alta disponibilidade do SAP em Azure em duas partes:

* **Infraestrutura Azure elevada disponibilidade**, por exemplo HA de computação (VMs), rede, armazenamento etc. e seus benefícios para aumentar a disponibilidade de aplicações SAP.
* **Aplicação SAP alta disponibilidade**, por exemplo HA de componentes de software SAP:
  * Servidores de aplicações SAP
  * Instância SAP ASCS/SCS
  * Servidor DB

e como pode ser combinado com a infraestrutura Azure HA.

A Alta Disponibilidade do SAP em Azure tem algumas diferenças em comparação com a Alta Disponibilidade Do SAP num ambiente físico ou virtual no local. O seguinte artigo do SAP descreve configurações padrão de alta disponibilidade <https://scn.sap.com/docs/DOC-44415>do SAP em ambientes virtualizados no Windows: . Não existe uma configuração SAP-HA integrada para o Linux como existe para windows. No que diz respeito ao SAP HA no <https://scn.sap.com/docs/DOC-8541>local para o Linux, encontre mais informações aqui: .

### <a name="azure-infrastructure-high-availability"></a>Alta disponibilidade de infraestruturas azure

Existe atualmente um SLA de 99,9%. Para ter uma ideia de como pode parecer a disponibilidade de um único VM, pode <https://azure.microsoft.com/support/legal/sla/>construir o produto das diferentes SLAs Azure disponíveis: .

A base para o cálculo é de 30 dias por mês, ou 43200 minutos. Assim, 0,05% de tempo de inatividade corresponde a 21,6 minutos. Como de costume, a disponibilidade dos diferentes serviços multiplicar-se-á da seguinte forma:

(Serviço de Disponibilidade #1/100) * (Serviço de Disponibilidade #2/100) * (Serviço de Disponibilidade #3/100) 

Como:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 ou uma disponibilidade global de 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Alta disponibilidade da Máquina Virtual (VM)

Existem dois tipos de eventos da plataforma Azure que podem afetar a disponibilidade das suas máquinas virtuais: manutenção planeada e manutenção não planeada.

* Os eventos de manutenção planeada são atualizações periódicas feitas pela Microsoft na plataforma Azure subjacente para melhorar a fiabilidade, o desempenho e a segurança gerais da infraestrutura da plataforma na qual as suas máquinas virtuais são executadas.
* Os eventos de manutenção não planeada ocorrem quando o hardware ou a infraestrutura física subjacente à máquina virtual falharam de alguma forma. Isto pode incluir falhas de rede local, falhas de disco local ou outras falhas estruturais. Quando tal falha for detetada, a plataforma Azure migrará automaticamente a sua máquina virtual do servidor físico pouco saudável que hospeda a sua máquina virtual para um servidor físico saudável. Estes eventos são raros, mas também podem provocar o reinício da máquina virtual.

Mais detalhes podem ser encontrados nesta documentação:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Redundância de armazenamento azure

Os dados da sua Conta de Armazenamento Microsoft Azure são sempre replicados para garantir durabilidade e elevada disponibilidade, cumprindo o Azure Storage SLA mesmo face a falhas de hardware transitórias.

Uma vez que o Armazenamento Azure está a manter três imagens dos dados por padrão, raid5 ou RAID1 em vários discos Azure não são necessários.

Mais detalhes podem ser encontrados neste artigo:<https://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Utilização de Infraestruturas Azure VM Reinício para alcançar maior disponibilidade de aplicações SAP

Se decidir não utilizar funcionalidades como o Clustering failover do Windows Server (WSFC) ou o Pacemaker no Linux (atualmente apenas suportado para SLES 12 ou superior), o Azure VM Restart é utilizado para proteger um Sistema SAP contra o tempo de inatividade planeado e não planeado da infraestrutura de servidores físicos Azure e a plataforma azure subjacente global.

> [!NOTE]
> É importante referir que o Azure VM Restart protege principalmente as aplicações vMs e NÃO. A VM Restart não oferece uma elevada disponibilidade para aplicações SAP, mas oferece um certo nível de disponibilidade de infraestrutura e, portanto, indiretamente maior disponibilidade de sistemas SAP. Também não existe SLA para o tempo que levará para reiniciar um VM após uma paragem planeada ou não planeada do hospedeiro. Portanto, este método de alta disponibilidade não é adequado para componentes críticos de um sistema SAP como (A)SCS ou DBMS.
>
>

Outro elemento importante de infraestrutura para alta disponibilidade é o armazenamento. Por exemplo, o Azure Storage SLA tem 99,9 % de disponibilidade. Se se implantar todos os VMs com os seus discos numa única Conta de Armazenamento Azure, a indisponibilidade potencial do Armazenamento Azure causará indisponibilidade de todos os VMs que são colocados nessa Conta de Armazenamento Azure, bem como todos os componentes SAP que correm dentro desses VMs.  

Em vez de colocar todos os VMs numa única Conta de Armazenamento Azure, também pode utilizar contas de armazenamento dedicadas para cada VM, e desta forma aumentar a disponibilidade global de aplicações VM e SAP utilizando várias Contas independentes de Armazenamento Azure.

Os Discos Geridos Azure são automaticamente colocados no Domínio da Falha da máquina virtual a que estão ligados. Se colocar duas máquinas virtuais num conjunto de disponibilidade e utilizar Discos Geridos, a plataforma cuidará também da distribuição dos Discos Geridos em diferentes Domínios de Falha. Se planeia utilizar o Armazenamento Premium, recomendamos vivamente a utilização de 'Gerir discos'.

Uma arquitetura de amostra de um sistema SAP NetWeaver que utiliza a infraestrutura Azure HA e contas de armazenamento poderia ser assim:

![Utilização da infraestrutura azure HA para alcançar maior disponibilidade de aplicação SAP][planning-guide-figure-2900]

Uma arquitetura de amostra de um sistema SAP NetWeaver que utiliza a infraestrutura Azure HA e Discos Geridos poderia ser assim:

![Utilização da infraestrutura azure HA para alcançar maior disponibilidade de aplicação SAP][planning-guide-figure-2901]

Para componentes SAP críticos, conseguimos o seguinte até agora:

* Alta Disponibilidade de Servidores de Aplicações SAP (AS)

  As instâncias do servidor de aplicações SAP são componentes redundantes. Cada instância SAP AS é implantada no seu próprio VM, que está a funcionar num domínio de falha e atualização de falhas de Azure diferente (ver capítulos [Domínios de Falhas][planning-guide-3.2.1] e [Domínios de Atualização).][planning-guide-3.2.2] Isto é assegurado utilizando conjuntos de disponibilidade Azure (ver capítulo Conjuntos de [Disponibilidade Azure).][planning-guide-3.2.3] A indisponibilidade planeada ou não planeada de um Domínio de Falha ou Atualização do Azure causará a indisponibilidade de um número restrito de VMs com as suas instâncias SAP AS.

  Cada instância SAP AS é colocada na sua própria conta de Armazenamento Azure - a potencial indisponibilidade de uma conta de armazenamento Azure causará indisponibilidade de apenas um VM com a sua instância SAP AS. No entanto, esteja ciente de que existe um limite de Contas de Armazenamento Azure dentro de uma subscrição Azure. Para garantir o arranque automático da instância (A)SCS após o reboot do VM, certifique-se de que define o parâmetro de arranque automático no (A)SCS de perfil de início descrito no capítulo [Utilização automática para instâncias SAP][planning-guide-11.5].
  Leia também o capítulo [High Availability para Servidores de Aplicações SAP][planning-guide-11.4.1] para mais detalhes.

  Mesmo que utilize discos geridos, esses discos também são armazenados numa Conta de Armazenamento Azure e podem estar indisponíveis em caso de falha de armazenamento.

* *Mais alto* Disponibilidade de sap (a)Instância SCS

  Aqui utilizamos o Reinício Do VM Azure para proteger o VM com a instância SAP (A)SCS instalada. No caso de tempo de paragem planeado ou não planeado de falhas do Azure, os VMs serão reiniciados noutro servidor disponível. Como mencionado anteriormente, o Azure VM Restart protege principalmente vMs e não aplicações, neste caso a instância (A)SCS. Através do VM Restart, chegaremos indiretamente a uma maior disponibilidade de instância SAP (A)SCS. Para assegurar o arranque automático da instância (A)SCS após o reboot do VM, certifique-se de que define o parâmetro de arranque automático no (A)SCS de perfil de início descrito no capítulo [Utilizando o Autostart para instâncias SAP][planning-guide-11.5]. Isto significa que a instância (A)SCS como um ponto único de falha (SPOF) que funciona num único VM será o fator determinante para a disponibilidade de toda a paisagem SAP.

* *Mais alto* Disponibilidade do Servidor DBMS

  Aqui, à semelhança do caso de utilização de instânciaS SAP (A)SCS, utilizamos o Azure VM Restart para proteger o VM com o software DBMS instalado, e conseguimos uma maior disponibilidade de software DBMS através do VM Restart.
  DbMS funcionando em um único VM é também um SPOF, e é o fator determinante para a disponibilidade de toda a paisagem SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Aplicação SAP Alta Disponibilidade no Azure IaaS

Para alcançar uma elevada disponibilidade do sistema SAP, precisamos de proteger todos os componentes críticos do sistema SAP, por exemplo servidores de aplicações SAP redundantes, e componentes únicos (por exemplo, Ponto Único de Falha) como a instância SAP (A)SCS, e DBMS.

#### <a name="high-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Alta disponibilidade para servidores de aplicações SAP

Para os servidores/casos de diálogo de aplicações SAP, não é necessário pensar numa solução específica de alta disponibilidade. A elevada disponibilidade é conseguida por redundância e, assim, tendo o suficiente delas em diferentes máquinas virtuais. Todos devem ser colocados na mesma disponibilidade do Azure definida para evitar que os VMs possam ser atualizados ao mesmo tempo durante o tempo de paragem de manutenção previsto. A funcionalidade básica, que se baseia em diferentes Domínios de Upgrade e Falhas dentro de uma Unidade de Escala Azure já foi introduzida no capítulo [Domínios de Upgrade][planning-guide-3.2.2]. Os conjuntos de disponibilidade do Azure foram apresentados no capítulo Conjuntos de [Disponibilidade do Azure][planning-guide-3.2.3] deste documento.

Não existe um número infinito de Domínios de Avaria e Upgrade que podem ser usados por um conjunto de disponibilidade azure dentro de uma Unidade de Escala Azure. Isto significa que colocar um número de VMs em um conjunto de disponibilidade, mais cedo ou mais tarde mais do que um VM acaba no mesmo Domínio de Falha ou Upgrade.

Implementando algumas instâncias de servidor de aplicações SAP nos seus VMs dedicados e assumindo que temos cinco Domínios de Upgrade, a imagem seguinte surge no final. O número máximo real de domínios de avaria e atualização dentro de um conjunto de disponibilidade pode mudar no futuro:

![Servidores de aplicações HA de SAP em Azure][planning-guide-figure-3000]

Mais detalhes podem ser encontrados nesta documentação:<https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Alta disponibilidade para serviços centrais SAP em Azure

Para uma arquitetura de alta disponibilidade dos Serviços Centrais SAP em Azure, consulte o artigo [Arquitetura de alta disponibilidade e cenários para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) como informação de entrada. O artigo aponta para descrições mais pormenorizadas para os sistemas operativos específicos.

#### <a name="high-availability-for-the-sap-database-instance"></a>Alta Disponibilidade para a instância de base de dados SAP

A configuração típica do SAP DBMS HA baseia-se em dois VMs DBMS onde a funcionalidade de alta disponibilidade dbMS é usada para replicar dados desde a instância dbmS ativa até ao segundo VM numa instância dbms passiva.

A funcionalidade de alta disponibilidade e recuperação de desastres para o DBMS em geral, bem como dbmS específico, são descritas no Guia de [Implementação dbms][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Alta disponibilidade de ponta a ponta para o sistema Completo SAP

Aqui estão dois exemplos de uma arquitetura SAP NetWeaver HA completa em Azure - um para Windows e outro para Linux.

Discos não geridos apenas: Os conceitos explicados abaixo podem ter de ser comprometidos um pouco quando implementar muitos sistemas SAP e o número de VMs implantados estão excedendo o limite máximo de Contas de Armazenamento por subscrição. Nesses casos, os VHDs de VMs precisam de ser combinados numa única Conta de Armazenamento. Normalmente, fá-lo-ia combinando VHDs de vMs de camada de aplicação SAP de diferentes sistemas SAP.  Também combinamos diferentes VHDs de diferentes VMs DBMS de diferentes sistemas SAP em uma conta de armazenamento Azure. Assim, tendo em mente os limites iops<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>das contas de armazenamento azure ( )


##### <a name="windowslogo_windows-ha-on-windows"></a>![Windows][Logo_Windows] HA no Windows

![Aplicação SAP NetWeaver HA Arquitetura com Servidor SQL em Azure IaaS][planning-guide-figure-3200]

As seguintes construções Azure são utilizadas para o sistema SAP NetWeaver, para minimizar o impacto por problemas de infraestrutura e remendar o hospedeiro:

* O sistema completo é implantado no Azure (necessário - camada DBMS, (instância A)SCS e camada completa de aplicação precisa de ser executado no mesmo local).
* O sistema completo funciona dentro de uma subscrição azure (necessária).
* O sistema completo funciona dentro de uma Rede Virtual Azure (necessária).
* A separação dos VMs de um sistema SAP em três conjuntos de disponibilidade é possível mesmo com todos os VMs pertencentes à mesma Rede Virtual.
* Cada camada (por exemplo DBMS, ASCS, Servidores de Aplicações) deve utilizar um conjunto de disponibilidade dedicado.
* Todos os VMs que executam as instâncias DBMS de um sistema SAP estão num conjunto de disponibilidade. Assumimos que há mais de um VM executando instâncias DEDBMS por sistema, uma vez que são utilizadas funcionalidades nativas de alta disponibilidade dbmS, como O Servidor SQL AlwaysOn ou Oráculo Data Guard.
* Todos os VMs que executam instâncias DEDBMS usam a sua própria conta de armazenamento. Os dados dbms e os ficheiros de registo são replicados de uma conta de armazenamento para outra conta de armazenamento utilizando funções de alta disponibilidade DBMS que sincronizam os dados. A indisponibilidade de uma conta de armazenamento causará a indisponibilidade de um nó de cluster SQL Windows, mas não todo o serviço SQL Server.
* Todos os VMs em execução (A)SCS exemplo de um sistema SAP estão em um conjunto de disponibilidade. Um Cluster de Failover do Servidor windows (WSFC) está configurado dentro desses VMs para proteger a instância (A)SCS.
* Todos os VMs em execução (A)SCS exemplos usam a sua própria conta de armazenamento. A Os ficheiros de instância SCS e a pasta global SAP são replicados de uma conta de armazenamento para outra conta de armazenamento utilizando a replicação do SIOS DataKeeper. A indisponibilidade de uma conta de armazenamento causará indisponibilidade de um nó de cluster (A)SCS Windows, mas não todo o serviço (A)SCS.
* TODOS os VMs que representam a camada de servidor de aplicação SAP estão num terceiro conjunto de disponibilidade.
* Todos os VMs que executam servidores de aplicações SAP usam a sua própria conta de armazenamento. A indisponibilidade de uma conta de armazenamento causará a indisponibilidade de um servidor de aplicações SAP, onde outros servidores de aplicações SAP continuam a funcionar.

A figura seguinte ilustrou a mesma paisagem usando discos geridos.

![Aplicação SAP NetWeaver HA Arquitetura com Servidor SQL em Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogo_linux-ha-on-linux"></a>![Linux][Logo_Linux] HA em Linux

A arquitetura para SAP HA em Linux on Azure é basicamente a mesma que para Windows como descrito acima. Consulte a Nota SAP [1928533] para obter uma lista de soluções de alta disponibilidade suportadas.

### <a name="using-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Utilização automática para instâncias SAP

A SAP ofereceu a funcionalidade para iniciar as instâncias SAP imediatamente após o início do Sistema Operativo dentro do VM. As etapas exatas foram documentadas no Artigo [1909114]da Base de Conhecimento sap. No entanto, a SAP já não recomenda utilizar a definição porque não existe controlo na ordem do caso reinicia, assumindo que mais de um VM foi afetado ou várias instâncias percorridos por VM. Assumindo um cenário típico de Azure de uma instância de servidor de aplicação SAP num VM e o caso de um único VM eventualmente reiniciado, o Autostart não é crítico e pode ser ativado adicionando este parâmetro:

    Autostart = 1

No perfil inicial do caso SAP ABAP e/ou Java.

> [!NOTE]
> O parâmetro Autostart também pode ter algumas quedas. Com mais detalhes, o parâmetro desencadeia o início de um caso SAP ABAP ou Java quando o serviço Windows/Linux relacionado da instância é iniciado. É certamente o caso quando o sistema operativo arranca. No entanto, o reinício dos serviços SAP também é uma coisa comum para a funcionalidade SAP Software Lifecycle Management, como sum ou outras atualizações ou atualizações. Estas funcionalidades não esperam que uma instância seja reiniciada automaticamente. Por conseguinte, o parâmetro Autostart deve ser desativado antes de executar tais tarefas. O parâmetro Autostart também não deve ser utilizado para instâncias SAP que estejam agrupadas, como ASCS/SCS/CI.
>
>

Consulte informações adicionais sobre o arranque automático para os casos SAP aqui:

* [Iniciar/Parar SAP juntamente com o seu Servidor Unix Iniciar/Parar](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Iniciar e parar agentes de gestão SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Como ativar o arranque automático da Base de Dados HANA](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>Sistemas SAP de 3 camadas maiores
Aspetos de alta disponibilidade das configurações sap de 3 níveis já foram discutidos em secções anteriores. Mas e os sistemas em que os requisitos do servidor DBMS são demasiado grandes para o localizarem em Azure, mas a camada de aplicação SAP pode ser implantada no Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Localização das configurações SAP de 3 níveis
Não é suportado para dividir o próprio nível de aplicação ou a aplicação e o nível DBMS entre as instalações e o Azure. Um sistema SAP ou está completamente implantado no local OU em Azure. Também não é suportado para que alguns dos servidores de aplicações executem no local e outros em Azure. Este é o ponto de partida da discussão. Também não estamos a suportar ter os componentes DBMS de um sistema SAP e a camada de servidor de aplicação SAP implantada em duas regiões azure diferentes. Por exemplo, dbms em West US e camada de aplicação SAP no Centro dos EUA. A razão para não apoiar tais configurações é a sensibilidade da latência da arquitetura SAP NetWeaver.

No entanto, ao longo do ano passado, os parceiros do data center desenvolveram co-localizações para as Regiões Azure. Estes co-locais estão muitas vezes nas proximidades dos centros de dados físicos do Azure dentro de uma região de Azure. A curta distância e ligação de ativos na co-localização através da ExpressRoute para o Azure pode resultar numa latência inferior a 2ms. Nesses casos, localizar a camada DBMS (incluindo armazenamento SAN/NAS) em tal co-localização e a camada de aplicação SAP em Azure é possível. [HANA Grandes Instâncias.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 

### <a name="offline-backup-of-sap-systems"></a>Backup offline dos sistemas SAP
Dependendo da configuração SAP escolhida (2-Tier ou 3-Tier) pode haver necessidade de recuar. O conteúdo do próprio VM mais para ter uma cópia de segurança da base de dados. Espera-se que as cópias de segurança relacionadas com o DBMS sejam feitas com métodos de base de dados. Uma descrição detalhada para as diferentes bases de dados, pode ser encontrada no [Guia DBMS][dbms-guide]. Por outro lado, os dados do SAP podem ser apoiados de forma offline (incluindo o conteúdo da base de dados também) tal como descrito nesta secção ou online, conforme descrito na secção seguinte.

A cópia de segurança offline exigiria basicamente o encerramento do VM através do portal Azure e uma cópia do disco VM base mais todos os discos ligados ao VM. Isto preservaria um ponto na imagem do VM e do seu disco associado. Recomenda-se copiar as cópias de cópias de segurança numa conta de armazenamento Azure diferente. Daí que se aplique o procedimento descrito no capítulo [Copying entre as Contas][planning-guide-5.4.2] de Armazenamento Azure deste documento.
Além da paragem utilizando o portal Azure, também se pode fazê-lo através do PowerShell ou clI, conforme descrito aqui:<https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Um restauro desse estado consistiria em apagar o VM base, bem como os discos originais da Base VM e discos montados, copiando os discos guardados para a Conta de Armazenamento original ou grupo de recursos para discos geridos e, em seguida, reimplantando o sistema.
Este artigo mostra um exemplo de como escrever este processo no PowerShell:<http://www.westerndevs.com/azure-snapshots/>

Por favor, certifique-se de instalar uma nova licença SAP uma vez que restaurar uma cópia de segurança VM, conforme descrito acima, cria uma nova chave de hardware.

### <a name="online-backup-of-an-sap-system"></a>Backup on-line de um sistema SAP

A cópia de segurança do DBMS é realizada com métodos específicos do DBMS, conforme descrito no [Guia DBMS][dbms-guide].

Outros VMs dentro do sistema SAP podem ser apoiados utilizando a funcionalidade De Backup de Máquinas Virtuais Azure. O Azure Virtual Machine Backup é um método padrão para fazer backup de um VM completo em Azure. A Azure Backup armazena as cópias de segurança em Azure e permite novamente restaurar um VM.

> [!NOTE]
> A partir de dezembro de 2015, a utilização de VM Backup NÃO mantém o ID VM único que é utilizado para o licenciamento SAP. Isto significa que um restauro de uma cópia de segurança VM requer a instalação de uma nova chave de licença SAP, uma vez que o VM restaurado é considerado um novo VM e não uma substituição da anterior que foi guardada.
>
> ![Windows][Logo_Windows] Windows
>
> Teoricamente, os VMs que executam bases de dados podem ser apoiados de forma consistente, bem como <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>se o sistema DBMS suportar o Windows VSS (Serviço de Cópia de Sombra de Volume), como, por exemplo, o SQL Server.
> No entanto, esteja ciente de que, com base em cópias de segurança do Azure VM, não são possíveis restauros ponto-a-tempo das bases de dados. Portanto, a recomendação é executar backups de bases de dados com funcionalidade DBMS em vez de depender do Backup Azure VM.
>
> Para se familiarizar com o Azure Virtual Machine Backup comece aqui: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Outras possibilidades são utilizar uma combinação do Microsoft Data Protection Manager instalada num Azure VM e Azure Backup para backup/restauração bases de dados. Mais informações podem <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>ser encontradas aqui: .  
>
> ![Linux][Logo_Linux] Linux
>
> Não há equivalente ao Windows VSS em Linux. Portanto, apenas cópias de segurança consistentes com ficheiros são possíveis, mas não cópias de segurança consistentes com aplicações. A cópia de segurança SAP DBMS deve ser feita utilizando a funcionalidade DBMS. O sistema de ficheiros que inclui os dados relacionados com o SAP pode ser guardado, por exemplo, utilizando alcatrão como descrito aqui:<https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure como local DR para produção paisagens SAP

Desde meados de 2014, as extensões a vários componentes em torno de Hyper-V, System Center e Azure permitem o uso de Azure como site DR para VMs que executam no local com base no Hyper-V.

Um blog que detalha como implementar esta <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>solução está documentado aqui: .

## <a name="summary"></a>Resumo

Os pontos-chave da alta disponibilidade para sistemas SAP em Azure são:

* Neste momento, o ponto único de falha do SAP não pode ser assegurado exatamente da mesma forma que pode ser feito em destacamentos no local. A razão é que os clusters de Discos Partilhados ainda não podem ser construídos em Azure sem o uso de software de terceiros.
* Para a camada DBMS é necessário utilizar a funcionalidade DBMS que não depende da tecnologia de cluster de disco partilhado. Os detalhes estão documentados no [Guia DBMS][dbms-guide].
* Para minimizar o impacto dos problemas nos Domínios de Falha na infraestrutura Azure ou na manutenção do hospedeiro, deverá utilizar conjuntos de disponibilidade azure:
  * Recomenda-se que tenha um conjunto de disponibilidade para a camada de aplicação SAP.
  * Recomenda-se que tenha um conjunto de disponibilidade separado para a camada DBMS SAP.
  * Não é aconselhável aplicar o mesmo conjunto de disponibilidade para VMs de diferentes sistemas SAP.
  * Recomenda-se a utilização de Discos Geridos Premium.
* Para efeitos de backup da camada DBMS SAP, verifique o [guia DBMS][dbms-guide].
* Apoiar casos de diálogo SAP faz pouco sentido, uma vez que é geralmente mais rápido reimplantar instâncias de diálogo simples.
* O backup do VM, que contém o diretório global do sistema SAP e com ele todos os perfis das diferentes instâncias, faz sentido e deve ser realizado com o Windows Backup ou, por exemplo, alcatrão no Linux. Uma vez que existem diferenças entre o Windows Server 2008 (R2) e o Windows Server 2012 (R2), que facilitam o seu backback utilizando as versões mais recentes do Windows Server, recomendamos a execução do Windows Server 2012 (R2) como sistema operativo de hóspedes windows.

## <a name="next-steps"></a>Passos seguintes
Leia os artigos:

- [Implantação de Máquinas Virtuais Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Considerações para a implantação de DBMS de Máquinas Virtuais Azure para carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Configurações e operações de infraestruturas SAP HANA em Azure] (https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/hana-vm-operations)
