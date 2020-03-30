---
title: SQL Server Azure Virtual Machines DBMS implementação para carga de trabalho SAP [ Microsoft Docs
description: Implementação em SQL Server do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP
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
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a0fbed1f4dd62b2d75d39f475d2fe124c55a2b97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645808"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SQL Server Azure Virtual Machines DBMS implementação para SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
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
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
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

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

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

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




Este documento abrange várias áreas diferentes a considerar ao implementar o SQL Server para carga de trabalho SAP no Azure IaaS. Como condição prévia para este documento, deve ter lido o documento Considerações para a implantação de [DBMS de Máquinas Virtuais Azure para carga de trabalho SAP,](dbms_guide_general.md) bem como outros guias na carga de trabalho [SAP sobre documentação Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 



> [!IMPORTANT]
> O âmbito deste documento é a versão do Windows no SQL Server. A SAP não está a suportar a versão Linux do SQL Server com nenhum dos softwares SAP. O documento não está a discutir a Microsoft Azure SQL Database, que é uma Plataforma como oferta de serviço da Plataforma Microsoft Azure. A discussão neste artigo é sobre a execução do produto SQL Server, uma vez que é conhecido pelas implementações no local em Máquinas Virtuais Azure, alavancando a Infraestrutura como uma capacidade de serviço do Azure. As capacidades e funcionalidades da base de dados entre estas duas ofertas são diferentes e não devem ser misturadas umas com as outras. Consulte também:<https://azure.microsoft.com/services/sql-database/>
> 
>

Em geral, deve considerar a utilização dos mais recentes lançamentos do SQL Server para executar a carga de trabalho SAP no Azure IaaS. Os mais recentes lançamentos do SQL Server oferecem uma melhor integração em alguns dos serviços e funcionalidades do Azure. Ou ter mudanças que otimizam as operações numa infraestrutura Azure IaaS.

Recomenda-se rever [esta][virtual-machines-sql-server-infrastructure-services] documentação antes de continuar.

Nas seguintes secções, as partes da documentação sob o link acima são agregadas e mencionadas. As especificidades em torno do SAP também são mencionadas e alguns conceitos são descritos mais detalhadamente. No entanto, é altamente recomendado trabalhar através da documentação acima primeiro antes de ler a documentação específica do Servidor SQL.

Existe algum Servidor SQL em informações específicas do IaaS que deve saber antes de continuar:

* **Suporte para versão SQL**: Para clientes SAP, O SQL Server 2008 R2 e superior é suportado na Máquina Virtual Microsoft Azure. Edições anteriores não são suportadas. Reveja esta Declaração geral [de Apoio](https://support.microsoft.com/kb/956893) para mais detalhes. Em geral, o SQL Server 2008 também é suportado pela Microsoft. No entanto, devido a uma funcionalidade significativa para o SAP, que foi introduzido com o SQL Server 2008 R2, o SQL Server 2008 R2 é o lançamento mínimo para o SAP. Em geral, deve considerar a utilização dos mais recentes lançamentos do SQL Server para executar a carga de trabalho SAP no Azure IaaS. Os mais recentes lançamentos do SQL Server oferecem uma melhor integração em alguns dos serviços e funcionalidades do Azure. Ou ter mudanças que otimizam as operações numa infraestrutura Azure IaaS. Portanto, o papel é restrito ao SQL Server 2016 e ao SQL Server 2017.
* **SQL Performance**: Microsoft Azure hospedado Virtual Machines tem um bom desempenho em comparação com outras ofertas públicas de virtualização em nuvem, mas os resultados individuais podem variar. Confira o artigo [As melhores práticas de desempenho para o Servidor SQL em Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Utilizar imagens do Azure Marketplace**: A forma mais rápida de implementar um novo Microsoft Azure VM é utilizar uma imagem do Azure Marketplace. Existem imagens no Azure Marketplace, que contêm as mais recentes versões do SQL Server. As imagens onde o SQL Server já está instalado não podem ser imediatamente utilizadas para aplicações SAP NetWeaver. A razão é que a colagem padrão do Servidor SQL está instalada dentro dessas imagens e não na colagem exigida pelos sistemas SAP NetWeaver. Para utilizar estas imagens, verifique os passos documentados no capítulo [Utilizando uma imagem do Servidor SQL a partir do Mercado Microsoft Azure][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Recomendações sobre a estrutura VM/VHD para implementações de servidores SQL relacionados com SAP
De acordo com a descrição geral, os executíveis do Servidor SQL devem ser localizados ou instalados na unidade do sistema do disco OS do VM (unidade C:\).  Tipicamente, a maioria das bases de dados do sistema SQL Server não são utilizadas a um nível elevado pela carga de trabalho SAP NetWeaver. Como resultado, as bases de dados do sistema do SQL Server (mestre, msdb e modelo) podem permanecer no C:\ dirigir também. Deve ser temporária uma exceção, que no caso das cargas de trabalho do SAP, pode exigir um volume de dados mais elevado ou um volume de operações de I/S. Carga de trabalho de I/S, que não deve ser aplicada ao VHD osso. Para estes sistemas, devem ser efetuados os seguintes passos:


* Com todos os tipos de VM certificados SAP (ver Nota SAP [1928533),]exceto VMs série A, dados tempdb e ficheiros de registo podem ser colocados no D não persistido:\ Conduzir. 
* No entanto, recomenda-se a utilização de vários ficheiros de dados temporários. Esteja atento d:\ os volumes de unidade são diferentes com base no tipo VM. Para tamanhos exatos do D:\ unidade dos diferentes VMs, verifique o artigo [Tamanhos para máquinas virtuais Windows em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

Estas configurações permitem que a tempdb consuma mais espaço do que a unidade do sistema é capaz de fornecer. O D não persistente:\ O drive também oferece melhor latência e entrada em I/S (com exceção dos VMs da Série A). Para determinar o tamanho temporário adequado, pode verificar os tamanhos temporários nos sistemas existentes. 

>[!NOTE]
> caso coloque ficheiros de dados tempdb e faça log file numa pasta em D:\ unidade que criou, precisa de se certificar de que a pasta existe após um reboot VM. Desde o D:\ a unidade é recentemente inicializada após um VM reiniciar todos os ficheiros e estruturas de diretório saem eliminadas. Possibilidade de recriar eventuais estruturas de diretório em D:\ conduzir antes do início do serviço SQL Server é documentado [neste artigo](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Uma configuração VM, que executa o Servidor SQL com uma base de dados SAP e onde os dados tempdb e o logfile tempdb são colocados no D:\ unidade seria como:

![Diagrama de configuração simples do disco VM para servidor SQL](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

O diagrama acima apresenta um caso simples. Tal como iludido no artigo [Considerações para a implantação de DBMS de Máquinas Virtuais Azure para carga](dbms_guide_general.md)de trabalho SAP , número e tamanho dos discos de armazenamento Premium depende de diferentes fatores. Mas, em geral, recomendamos:

- Utilizando espaços de armazenamento para formar um ou um pequeno número de volumes, que contêm os ficheiros de dados do SQL Server. A razão por detrás desta configuração é que na vida real existem numerosas bases de dados SAP com diferentes ficheiros de base de dados de tamanho sinuoso com diferentes cargas de trabalho de I/S.
- Utilização de espaços de armazenamento para fornecer IOPS suficientes e para o ficheiro de registo de transações do Servidor SQL. A carga de trabalho potencial do IOPS é frequentemente a linha orientadora para o dimensionamento do volume de registo de transações e não o volume potencial do volume de transações do SQL Server
- Use o D:\drive para tempdb, desde que o desempenho seja bom o suficiente. Se a carga de trabalho global for limitada no desempenho por tmepdb localizado no D:\ unidade que você pode ter que considerar mover tempdb para separar discos de armazenamento Premium como recomendado [neste artigo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Especial para VMs série M
Para o Azure M-Series VM, a latência escrita no registo de transações pode ser reduzida por fatores, em comparação com o desempenho do Armazenamento Azure Premium, quando se utiliza o Acelerador De Escrita Azure. Por isso, deve utilizar o Acelerador de Escrita Azure para os VHD(s) que formam o volume do registo de transações do Servidor SQL. Os detalhes podem ser lidos no documento [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>Formatação dos discos
Para o Servidor SQL, o tamanho do bloco NTFS para discos que contenham dados do Servidor SQL e ficheiros de registo deve ser de 64KB. Não há necessidade de formatar o D:\ Conduzir. Esta unidade vem pré-formatada.

Para se certificar de que a restauração ou criação de bases de dados não está a inicializar os ficheiros de dados, azeroing o conteúdo dos ficheiros, deve certificar-se de que o contexto do utilizador em que o serviço SQL Server está a ser gerido tem uma certa permissão. Normalmente, os utilizadores do grupo De Administrador do Windows têm estas permissões. Se o serviço SQL Server for executado no contexto do utilizador do utilizador não-Windows Administrator, tem de atribuir a esse utilizador as **tarefas**de manutenção de volume do Utilizador Right Perform .  Consulte os detalhes deste Artigo da Base de Conhecimento da Microsoft:<https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Impacto da compressão da base de dados
Em configurações onde a largura de banda de I/S pode tornar-se um fator limitativo, cada medida, que reduz o IOPS pode ajudar a esticar a carga de trabalho que se pode executar num cenário IaaS como o Azure. Portanto, se ainda não for feito, a aplicação da compressão sQL Server PAGE é recomendada tanto pela SAP como pela Microsoft antes de enviar uma base de dados SAP existente para o Azure.

A recomendação para realizar a Compressão da Base de Dados antes de enviar para o Azure é dada por duas razões:

* A quantidade de dados a carregar é menor.
* A duração da execução da compressão é mais curta, assumindo que se pode utilizar hardware mais forte com mais CPUs ou largura de banda de I/S mais elevada ou menos latência de E/S no local.
* Tamanhos de base de dados menores podem levar a menos custos para a atribuição de discos

A compressão da base de dados funciona também numa Máquina Sé azul como no local. Para obter mais detalhes sobre como comprimir as bases de dados existentes do SAP NetWeaver SQL Server, consulte o artigo Ferramenta de [compressão SAP Melhorada MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 e mais recente - Armazenar ficheiros de base de dados diretamente no Armazenamento De Blob Azure
O SQL Server 2014 e posteriormente lança a possibilidade de armazenar ficheiros de base de dados diretamente na Loja Blob Azure sem o 'invólucro' de um VHD à sua volta. Especialmente com a utilização de tipos de Armazenamento Standard Azure ou VM mais pequenos este tipo de implantação permite cenários onde pode ultrapassar os limites do IOPS que seriam aplicados por um número limitado de discos que podem ser montados em alguns tipos de VM menores. Esta forma de implementação funciona para bases de dados de utilizadores, no entanto, não para bases de dados do sistema do Servidor SQL. Também funciona para dados e ficheiros de registo do Servidor SQL. Se quiser implementar uma base de dados do SAP SQL Server desta forma em vez de o 'embrulhar' em VHDs, lembre-se:

* A Conta de Armazenamento utilizada tem de estar na mesma região azure que a que é usada para implantar o VM SQL Server está a decorrer.
* Considerações enumeradas anteriormente relativamente à distribuição de VHDs em diferentes Contas de Armazenamento Azure aplicam-se também a este método de implantações. Significa que as operações de I/S contam contra os limites da Conta de Armazenamento Azure.
* Em vez de contabilizar a quota de I/O de armazenamento do VM, o tráfego contra as bolhas de armazenamento que representam os dados do SQL Server e ficheiros de registo, será contabilizado na largura de banda da rede vM do tipo VM específico. Para a largura de banda de rede e armazenamento de um tipo de VM específico, consulte o artigo [Tamanhos para máquinas virtuais Windows em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
* Como resultado da utilização do ficheiro I/O através da quota de rede, está a encalhar a quota de armazenamento maioritariamente e com essa utilização apenas parcialmente a largura de banda total do VM.
* Os objetivos de desempenho do iOPS e do i/O de desempenho que o Azure Premium Storage tem para os diferentes tamanhos do disco já não se aplicam. Mesmo que as bolhas que criou estejam localizadas no Armazenamento Premium Azure. Os alvos são documentados o artigo [Armazenamento Premium de alto desempenho e discos geridos para VMs](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage). Como resultado da colocação de ficheiros de dados do SQL Server e ficheiros de registo diretamente em blobs armazenados no Armazenamento Premium Azure, as características de desempenho podem ser diferentes em comparação com VHDs no Armazenamento Premium Azure.
* O cache baseado no hospedeiro, tal como disponível para os discos de armazenamento Premium Azure, não está disponível ao colocar ficheiros de dados do SQL Server diretamente em blobs Azure.
* Nos VMs da Série M, o Acelerador de Escrita Azure não pode ser usado para suportar escritos submilissegundos contra o ficheiro de registo de transações do SQL Server. 

Detalhes desta funcionalidade podem ser encontrados no artigo Ficheiros de dados do [SQL Server no Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

A recomendação para sistemas de produção é evitar esta configuração e, em vez disso, escolher as colocações de dados do SQL Server e ficheiros de registo em VHDs de Armazenamento Premium Azure em vez de diretamente em blobs Azure.


## <a name="sql-server-2014-buffer-pool-extension"></a>Extensão do pool do sql server 2014
O SQL Server 2014 introduziu uma nova funcionalidade, que se chama [Extensão](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017)do Pool Buffer . Esta funcionalidade estende o conjunto tampão do SQL Server, que é mantido na memória com uma cache de segundo nível que é apoiada por SSDs locais de um servidor ou VM. A extensão do conjunto tampão permite manter um conjunto de dados de trabalho maior 'na memória'. Em comparação com o acesso ao Azure Standard Storage, o acesso à extensão do pool tampão, que é armazenado em SSDs locais de um Azure VM, é muito mais rápido. Comparando a extensão do pool do tampão com o Cache de Leitura de Armazenamento Premium Azure, conforme recomendado para ficheiros de dados do Servidor SQL, não são esperadas vantagens significativas para as extensões do Pool Buffer. A razão é que ambos os caches (Extensão do Tampão do Servidor SQL e Cache de Leitura de Armazenamento Premium) estão a usar os discos locais do nó de computação Azure.

As experiências adquiridas entretanto com a Extensão do Pool buffer do Servidor SQL com a carga de trabalho SAP são misturadas e ainda não permitem recomendações claras sobre se devem ou não usá-lo em todos os casos. O caso ideal é que o conjunto de trabalho que a aplicação SAP requer se enquadra na memória principal. Com o Azure, entretanto, a oferecer VMs que vêm com até 4 TB de memória, deve ser possível manter o conjunto de trabalho na memória. Por conseguinte, o uso da extensão do pool de tampão limita-se a alguns casos raros e não deve ser um caso mainstream.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Considerações de backup/recuperação para o Servidor SQL
Ao implantar o SQL Server no Azure, a sua metodologia de backup deve ser revista. Mesmo que o sistema não seja um sistema de produção, a base de dados SAP hospedada pelo SQL Server deve ser apoiada periodicamente. Uma vez que o Azure Storage mantém três imagens, uma cópia de segurança é agora menos importante no que diz respeito a compensar um acidente de armazenamento. A razão prioritária para manter um plano de backup e recuperação adequado é mais que você pode compensar erros lógicos/manuais fornecendo capacidades de recuperação de pontos no tempo. Assim, o objetivo é utilizar cópias de backup para restaurar a base de dados a um certo ponto do tempo ou utilizar as cópias de segurança em Azure para semear outro sistema copiando a base de dados existente. 

Para ver diferentes possibilidades de backup do SQL Server em Azure, leia o artigo [Backup e Restore for SQL Server em Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). O artigo abrange várias possibilidades diferentes.

### <a name="manual-backups"></a>Backups manuais
Tem várias possibilidades de realizar cópias de segurança 'manuais' por:

1. Executando cópias de segurança convencionais do SQL Server em discos Azure ligados diretamente. Este método tem a vantagem de ter as cópias de segurança disponíveis rapidamente para atualizações do sistema e construção de novos sistemas como cópias dos sistemas SAP existentes
2.  SQL Server 2012 CU4 e superior pode fazer o backup de bases de dados para um URL de armazenamento Azure.
3.  Backups de ficheiros snapshot para ficheiros de base de dados no armazenamento de Blob Azure. Este método só funciona quando os dados do SQL Server e os ficheiros de registo estão localizados no armazenamento de blob Azure

O primeiro método é bem conhecido e aplicado em muitos casos no mundo das instalações também. No entanto, deixa-lhe a tarefa de resolver a localização de backup a longo prazo. Uma vez que não pretende manter as suas cópias de segurança durante 30 ou mais dias no Azure Storage localmente ligado, tem a necessidade de utilizar os Serviços de Backup Azure ou outra ferramenta de backup/recuperação de terceiros que inclua acesso e gestão de retenção para as suas cópias de segurança. Ou constrói um grande servidor de ficheiros em Azure utilizando espaços de armazenamento do Windows.

O segundo método é descrito mais perto no artigo [SQL Server Backup to URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). Diferentes lançamentos do SQL Server têm algumas variações nesta funcionalidade. Portanto, deve consultar a documentação para a sua verificação de lançamento do SQL Server em particular. É importante notar que este artigo enumera muitas restrições. Ou tem a possibilidade de realizar o backup contra:

- Uma única página Azure blob, que depois limita o tamanho de reserva a 1000 GB. Isto também limita a entrada que pode alcançar.
- Múltiplas bolhas de bloco sinuoso (até 64) que permitem uma cópia de segurança teórica de 12 TB. No entanto, os testes com bases de dados dos clientes revelaram que o tamanho máximo de backup pode ser menor do que o seu limite teórico. Neste caso, é responsável pela gestão da retenção de backups e acesso às cópias de segurança.


### <a name="automated-backup-for-sql-server"></a>Cópia de Segurança Automatizada para SQL Server
A Backup Automatizada fornece um serviço de backup automático para edições SQL Server Standard e Enterprise em execução num Windows VM em Azure. Este serviço é fornecido pela extensão do [agente IaaS do Servidor SQL,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)que é automaticamente instalada no portal SQL Server Windows no portal Azure. Se implementar as suas próprias imagens de OS com o SQL Server instalado, tem de instalar as extensões VM separadamente. Os passos necessários estão documentados neste [artigo.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)

Mais detalhes sobre as capacidades deste método podem ser encontrados nestes artigos:

- SQL Server 2014: [Backup automatizado para Máquinas Virtuais SQL Server 2014 (Gestor de Recursos)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Backup automático v2 para Máquinas Virtuais Azure (Gestor de Recursos)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Olhando para a documentação, pode ver que a funcionalidade com as mais recentes versões do SQL Server melhorou. Alguns mais detalhes sobre cópias de segurança automatizadas do SQL Server são lançados no artigo [SQL Server Managed Backup para o Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). O limite teórico de tamanho de reserva é de 12 TB.  As cópias de segurança automatizadas podem ser um bom método para tamanhos de reserva de até 12 TB. Uma vez que várias bolhas são escritas em paralelo, pode esperar uma entrada superior a 100 MB/seg. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Backup Azure para VMs do Servidor SQL
Este novo método de backups Do SQL Server é oferecido a partir de junho de 2018 como pré-visualização pública pelos serviços de Backup Azure. O método para fazer backup o SQL Server é o mesmo que outras ferramentas de terceiros estão a usar, nomeadamente a interface SQL Server VSS/VDI para transmitir cópias de segurança para um local-alvo. Neste caso, a localização do alvo é o cofre do Serviço de Recuperação Azure.

Uma descrição mais do que detalhada deste método de backup, que adiciona inúmeras vantagens das configurações centrais de backup, monitorização e administração está disponível [aqui](https://docs.microsoft.com/azure/backup/backup-azure-sql-database). 


### <a name="third-party-backup-solutions"></a>Soluções de backup de terceiros
Para um grande número de clientes SAP, não havia possibilidade de recomeçar e introduzir novas soluções completas de backup para a parte da sua paisagem SAP que estava a funcionar no Azure. Como resultado, as soluções de backup existentes precisavam de ser utilizadas e estendidas para Azure. O alargamento das soluções de backup existentes para o Azure normalmente funcionou bem com a maioria dos principais fornecedores deste espaço. 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Usando uma imagem do Servidor SQL a partir do Mercado Microsoft Azure
A Microsoft oferece VMs no Azure Marketplace, que já contém versões do SQL Server. Para os clientes SAP que necessitem de licenças para O Servidor SQL e Windows, a utilização destas imagens pode ser uma oportunidade para cobrir a necessidade de licenças, girando VMs com O Servidor SQL já instalado. Para utilizar estas imagens para o SAP, devem ser tomadas as seguintes considerações:

* As versões de não avaliação do SQL Server adquirem custos mais elevados do que um VM 'apenas para windows' implantado no Azure Marketplace. Consulte estes artigos para <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>comparar preços: e . 
* Só pode utilizar as versões SQL Server, que são suportadas pelo SAP.
* A colagem da instância SQL Server, que está instalada nos VMs oferecidos no Mercado Azure não é a colagem SAP NetWeaver requer que a instância do Servidor SQL seja executada. Pode alterar a colagem com as instruções na secção seguinte.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Alterar a Colagem do Servidor SQL de um Microsoft Windows/SQL Server VM
Uma vez que as imagens do SQL Server no Mercado Azure não estão configuradas para utilizar a colagem, que é exigida pelas aplicações SAP NetWeaver, esta deve ser alterada imediatamente após a implementação. Para o SQL Server, esta alteração de colagem pode ser feita com os seguintes passos logo que o VM foi implementado e um administrador é capaz de iniciar sessão no VM implantado:

* Abra uma janela de comando do Windows, como administrador.
* Altere o diretório para C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Executar o comando: Configuração.exe /QUIET /ACTION=REBUILDDATABASE /INSTÂNCIANAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> é a conta, que foi definida como a conta de administrador ao implantar o VM pela primeira vez através da galeria.

O processo deve demorar apenas alguns minutos. A fim de se certificar de que o passo acabou com o resultado correto, execute os seguintes passos:

* Abra o SQL Server Management Studio.
* Abra uma janela de consulta.
* Execute o comando sp_helpsort na base de dados principal do SQL Server.

O resultado desejado deve parecer:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Se o resultado for diferente, PARE de implantar SAP e investigue por que razão o comando de configuração não funcionou como esperado. A implementação de aplicações SAP NetWeaver na instância do Servidor SQL com diferentes páginas de código do Servidor SQL do que a mencionada acima **não** é suportada.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server Alta Disponibilidade para SAP em Azure
Utilizando o SQL Server em implementações Azure IaaS para SAP, tem várias possibilidades diferentes para adicionar para implementar a camada DBMS altamente disponível. Tal como discutido em considerações para a implantação de [DBMS de Máquinas Virtuais Azure para carga](dbms_guide_general.md) de trabalho SAP já, o Azure fornece diferentes SLAs de tempo em altura para um único VM e um par de VMs implantados num Conjunto de Disponibilidade Azure. Supõe-se que conduz até ao SLA de tempo em alta para as suas implementações de produção que requer a implantação em Conjuntos de Disponibilidade Azure. Neste caso, é necessário implementar um mínimo de dois VMs num conjunto de disponibilidade. Um VM executará a instância de servidor SQL ativa. O outro VM vai executar a instância passiva

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>Clustering de servidor SQL usando o servidor de ficheiros windows scale-out
Com o Windows Server 2016, a Microsoft introduziu espaços de [armazenamento direto](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview). Com base na implementação direta de espaços de armazenamento, o clustering SQL Server FCI é suportado. Os detalhes podem ser encontrados no artigo [Configure SQL Server Failover Cluster Instance on Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster). A solução requer um equilibrador de carga Azure também para lidar com o endereço IP virtual dos recursos do cluster. Os ficheiros de base de dados do SQL Server são armazenados em Espaços de Armazenamento. Assim, é um dado adquirido que você seria obrigado a construir os Espaços de Armazenamento windows com base no Armazenamento Premium Azure. Uma vez que esta solução foi suportada por não muito tempo ainda, não existem clientes Conhecidos da SAP que utilizem esta solução em cenários de produção sap.  

### <a name="sql-server-log-shipping"></a>Envio de registo de servidor SQL
Um dos métodos de alta disponibilidade (HA) é o Envio de Registo sQL Server. Se os VMs que participam na configuração HA tiverem resolução de nomes de trabalho, não há qualquer problema e a configuração em Azure não difere de qualquer configuração que seja feita no local. No que diz respeito à criação de envio de log e aos princípios em torno do envio de registos. Os detalhes do envio de registo do servidor SQL podem ser encontrados no artigo Sobre envio de [registo (Servidor SQL)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017).

A funcionalidade de envio de registo SQL Server dificilmente foi utilizada em Azure para alcançar uma elevada disponibilidade dentro de uma região do Azure. No entanto, nos seguintes cenários, os clientes da SAP estavam a utilizar o envio de registos bem sucedido em conjunto com o Azure:

- Cenários de recuperação de desastres de uma região de Azure para outra região de Azure
- Configuração de recuperação de desastres a partir de instalações para uma região de Azure
- Cenários de corte de instalações para Azure. Nesses casos, o envio de diários de bordo é utilizado para sincronizar a nova implantação do DBMS em Azure com o sistema de produção em curso no local. No momento do corte, a produção é encerrada e é assegurado que as últimas e mais recentes cópias de segurança de registo de transações foram transferidas para a implantação do AZURE DBMS. Em seguida, a implantação azure DBMS é aberta para produção.  



### <a name="database-mirroring"></a>Espelho de base de dados
A base de dados Espelhada como suportado pelo SAP (ver SAP Note [965908)]depende da definição de um parceiro de failover na cadeia de ligação SAP. Para os casos Cross-Premises, assumimos que os dois VMs estão no mesmo domínio e que o contexto do utilizador as duas instâncias do SQL Server estão a decorrer sob um utilizador de domínio e têm privilégios suficientes nas duas instâncias do SQL Server envolvidas. Por conseguinte, a configuração do Database Mirroring em Azure não difere entre uma configuração/configuração típica no local.

A partir de implementações cloud-only, o método mais fácil é ter outra configuração de domínio em Azure para ter esses VMs DBMS (e vMs SAP idealmente dedicados) dentro de um domínio.

Se um domínio não for possível, também se pode utilizar certificados para os pontos finais espelhados da base de dados, tal como aqui descrito:<https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Um tutorial para criar o Database Mirroring em Azure pode ser encontrado aqui:<https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server AlwaysOn
Como Sempre On é suportado para SAP on-pre-presidiário (ver SAP Nota [1772688),]é suportado em combinação com SAP em Azure. Existem algumas considerações especiais em torno da implementação do SQL Server Availability Group Listener (não confundir com o Conjunto de Disponibilidade Azure) uma vez que o Azure neste momento não permite criar um objeto AD/DNS como é possível no local. Portanto, alguns passos de instalação diferentes são necessários para superar o comportamento específico de Azure.

Algumas considerações usando um Ouvinte de Grupo de Disponibilidade são:

* A utilização de um Ouvinte de Grupo de Disponibilidade só é possível com o Windows Server 2012 ou superior como os si convidado si do VM. Para o Windows Server 2012, é necessário certificar-se de que este patch é aplicado:<https://support.microsoft.com/kb/2854082> 
* Para o Windows Server 2008 R2, este patch não existe e a Always On teria de ser utilizada da mesma forma que a Database Mirroring, especificando um parceiro de falha na cadeia de ligações (feito através do parâmetro dbs/mss/server do SAP default.pfl - ver Nota [SAP 965908]).
* Ao utilizar um ouvinte de grupo de disponibilidade, os VMs da Base de Dados precisam de ser ligados a um equilibrador de carga dedicado. A fim de evitar que o Azure esteja a atribuir novos endereços IP nos casos em que ambos os VMs são desligados, deve-se atribuir endereços IP estáticos às interfaces de rede desses VMs na configuração Always On (definindo um endereço IP estático é descrito [neste][virtual-networks-reserved-private-ip] artigo)
* Existem passos especiais necessários para a construção da configuração do cluster WSFC onde o cluster precisa de um endereço IP especial atribuído, porque o Azure com a sua funcionalidade atual atribuiria ao nome do cluster o mesmo endereço IP que o nó em que o cluster é criado. Isto significa que deve ser realizado um passo manual para atribuir um endereço IP diferente ao cluster.
* O Grupo de Disponibilidade Vai ser criado em Azure com pontos finais TCP/IP, que são atribuídos aos VMs que executam as réplicas primárias e secundárias do grupo Disponibilidade.
* Pode haver necessidade de assegurar estes pontos finais com ACLs.

Documentação detalhada sobre a implementação always on with SQL Server em listas de VMs Azure como:

- [Apresentando o Servidor SQL Sempre em grupos de disponibilidade em máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
- [Configure um grupo sempre em disponibilidade em máquinas virtuais Azure em diferentes regiões.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)
- [Configure um balanceor de carga para um grupo sempre em funcionação em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> Se estiver a configurar o balanceador de carga Azure para o endereço IP virtual do ouvinte do Grupo de Disponibilidade, certifique-se de que o DirectServerReturn está configurado. Configurar esta opção reduzirá a latência de ida e volta da rede entre a camada de aplicação SAP e a camada DBMS. 

SQL Server Always On é a funcionalidade de alta disponibilidade usada mais comum e de recuperação de desastres utilizada em Azure para implementações de carga de trabalho SAP. A maioria dos clientes utiliza o Always On para uma elevada disponibilidade dentro de uma única região azure. Se a implantação for restrita apenas a dois nós, você tem duas opções para conectividade:

- Utilizando o Ouvinte do Grupo de Disponibilidade. Com o Listener do Grupo de Disponibilidade, é necessário implementar um equilibrador de carga Azure. Este é geralmente o método padrão de implantação. As aplicações SAP seriam configuradas para se conectarem com o ouvinte do Grupo de Disponibilidade e não contra um único nó
- Utilizando os parâmetros de conectividade do Espelho de Base de Dados do Servidor SQL. Neste caso, é necessário configurar a conectividade das aplicações SAP de uma forma em que ambos os nomes de nós são nomeados. Os detalhes exatos de tal configuração lateral Do SAP estão documentados no SAP Note [#965908](https://launchpad.support.sap.com/#/notes/965908). Ao utilizar esta opção, não precisaria de configurar um ouvinte do Grupo de Disponibilidade. E com isso nenhum equilíbrio de carga Azure para o SQL Server alta disponibilidade. Como resultado, a latência da rede entre a camada de aplicação SAP e a camada DBMS é menor, uma vez que o tráfego de entrada para a instância SQL Server não é encaminhado através do equilíbrio de carga Azure. Mas lembre-se, esta opção só funciona se restringir o seu Grupo de Disponibilidade para abranger duas instâncias. 

Alguns clientes estão a aproveitar a funcionalidade SQL Server Always On para uma funcionalidade adicional de recuperação de desastres entre as regiões do Azure. Vários clientes também usam a capacidade de realizar backups a partir de uma réplica secundária. 

## <a name="sql-server-transparent-data-encryption"></a>Encriptação transparente de dados transparente do servidor SQL
Há uma série de clientes que estão a usar encriptação transparente de [dados (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) do SQL Server ao implementar as suas bases de dados do SAP SQL Server em Azure. A funcionalidade SQL Server TDE é totalmente suportada pelo SAP (ver Nota SAP [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Aplicação do SQL Server TDE
Nos casos em que realiza uma migração heterogénea de outro DBMS, que está a decorrer no local, para o Windows/SQL Server em funcionamento em Azure, deverá criar a sua base de dados de destino vazia no SQL Server antes do tempo. Como próximo passo, aplicaria a funcionalidade SQL Server TDE. Enquanto ainda estás a gerir o teu sistema de produção no local. A razão pela qual pretende realizar nesta sequência é que o processo de encriptação da base de dados vazia pode demorar bastante tempo. Os processos de importação de SAP importariam os dados para a base de dados encriptada durante a fase de inatividade. A sobrecarga de importação para uma base de dados encriptada tem um impacto temporal muito mais baixo do que encriptar a base de dados após a fase de exportação na fase de tempo de paragem. Experiências negativas em que se tenta aplicar TDE com carga de trabalho SAP em cima da base de dados. Por conseguinte, a recomendação é tratar a implantação do TDE como uma atividade que tem de ser feita sem a carga de trabalho do SAP na base de dados específica.

Nos casos em que transfere as bases de dados do SAP SQL Server das instalações para o Azure, recomendamos testar em que infraestrutura pode obter a encriptação aplicada mais rapidamente. Para isto, lembre-se destes factos:

- Não é possível definir quantos fios são usados para aplicar encriptação de dados na base de dados. O número de fios depende principalmente do número de volumes de disco que os dados do SQL Server e os ficheiros de registo são distribuídos. Significa que quanto mais distintos os volumes (letras de acionamento), mais fios serão ligados paralelamente para executar a encriptação. Tal configuração contradiz um pouco com a sugestão de configuração do disco anterior na construção de um ou um número menor de espaços de armazenamento para os ficheiros de base de dados do SQL Server em VMs Azure. Uma configuração com um pequeno número de volumes levaria a um pequeno número de fios executando a encriptação. Uma única encriptação de fios está a ler 64KB extensões, encripta-o e depois escreve um registo no ficheiro de registo de transações, dizendo que a extensão foi encriptada. Como resultado, a carga no registo de transações é moderada.
- Nos lançamentos mais antigos do SQL Server, a compressão de backup já não teve eficiência quando encriptou a base de dados do SQL Server. Este comportamento pode desenvolver-se num problema quando o seu plano era encriptar a sua base de dados do SQL Server no local e, em seguida, copiar uma cópia de cópia para o Azure para restaurar a base de dados em Azure. A compressão de backup do SQL Server geralmente consegue uma relação de compressão do fator 4.
- Com o SQL Server 2016, o SQL Server introduziu uma nova funcionalidade que permite comprimir bases de dados encriptadas, bem como de forma eficiente. Consulte [estes blogs](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) para obter alguns detalhes.
 
Tratando a aplicação da encriptação TDE apenas com nenhuma carga de trabalho SAP, deve testar na sua configuração específica se é melhor aplicar O TDE na sua base de dados SAP no local ou fazê-lo em Azure. Em Azure, certamente tem mais flexibilidade em termos de infraestruturas de excesso de provisionamento e diminui a infraestrutura após a aplicação da TDE.

### <a name="using-azure-key-vault"></a>Usando o cofre da chave azure
O Azure oferece o serviço de um [Cofre chave](https://azure.microsoft.com/services/key-vault/) para armazenar chaves de encriptação. O SQL Server, do outro lado, oferece um conector para alavancar o Cofre de Chaves Azure como loja para os certificados TDE.

Mais detalhes para utilizar o Cofre de Chaves Azure para listas DeTDE do Servidor SQL como:

- [Gestão de chaves extensíveis utilizando o cofre da chave Azure (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [Gestão extensível da chave do servidor SQL Utilizando o cofre da chave azure - Passos de configuração](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- Manutenção do [conector do servidor SQL & resolução de problemas](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Mais perguntas de clientes sobre encriptação transparente de dados transparente sQL Server – TDE + Cofre de Chaves Azure](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Utilizando o SQL Server TDE, especialmente com o Cofre da Chave Azure, recomenda-se a utilização das mais recentes manchas do SQL Server 2014, SQL Server 2016 e SQL Server 2017. A razão é que, com base no feedback do cliente, otimizações e correções foram aplicadas ao código. Como exemplo, verifique [a KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Servidor Geral SQL para SAP em Resumo Azure
Existem muitas recomendações neste guia e recomendamos que o leia mais do que uma vez antes de planear a sua implantação do Azure. Em geral, porém, não se esqueça de seguir o DBMS geral de topo em recomendações específicas do Azure:

1. Utilize o mais recente lançamento do DBMS, como o SQL Server 2017, que tem mais vantagens em Azure. 
2. Planeie cuidadosamente a sua paisagem do sistema SAP em Azure para equilibrar o layout de ficheiros de dados e as restrições do Azure:
   * Não tenha muitos discos, mas tenha o suficiente para garantir que pode alcançar o iOPS necessário.
   * Se não utilizar discos geridos, lembre-se de que os IOPS também são limitados por Conta de Armazenamento Azure e que as Contas de Armazenamento são limitadas dentro de cada subscrição do Azure[(mais detalhes).][azure-resource-manager/management/azure-subscription-service-limits] 
   * Só riscas através dos discos se precisar de obter uma entrada mais alta.
3. Nunca instale software ou coloque ficheiros que exijam persistência no D:\ unidade como não é permanente e qualquer coisa nesta unidade se perde num reboot do Windows.
4. Não utilize o cache de disco para o Armazenamento Padrão Azure.
5. Não utilize contas de armazenamento padrão Azure geo-replicadas.  Utilize redundante localmente para cargas de trabalho DBMS.
6. Utilize a solução HA/DR do seu fornecedor DBMS para replicar dados de base de dados.
7. Utilize sempre a Resolução de Nomes, não confie em endereços IP.
8. Utilizando o SQL Server TDE, aplique os mais recentes patches do Servidor SQL.
9. Utilize a compressão de base de dados mais alta possível. Que é a compressão de página para o Servidor SQL.
10. Tenha cuidado ao utilizar imagens do SQL Server do Azure Marketplace. Se utilizar o SQL Server 1, tem de alterar a colagem de instâncias antes de instalar qualquer sistema SAP NetWeaver no mesmo.
11. Instale e configure a monitorização do hospedeiro SAP para o Azure, conforme descrito no Guia de [Implantação][deployment-guide].
