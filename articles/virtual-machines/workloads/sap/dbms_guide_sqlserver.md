---
title: Implementação de DBMS de máquinas virtuais do SQL Server do Azure para a carga de trabalho do SAP | Documentos da Microsoft
description: Implementação de DBMS de máquinas virtuais do SQL Server do Azure para a carga de trabalho do SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aac7ca7aa67143f89d9247da879a6fad2cfbb7b5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992482"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Implementação de DBMS de máquinas virtuais do SQL Server do Azure para SAP NetWeaver

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
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
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




Este documento aborda várias áreas diferentes a serem considerados ao implementar o SQL Server para a carga de trabalho SAP no Azure IaaS. Como pré-condição para este documento, deve ter ler o documento [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md) , bem como outros guias do [carga de trabalho SAP na documentação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 



> [!IMPORTANT]
> O âmbito deste documento é a versão do Windows no SQL Server. SAP não está a suportar a versão do Linux do SQL Server com qualquer uma do software SAP. O documento não ensina base de dados do SQL do Azure de Microsoft, que é uma plataforma como uma oferta de serviço da plataforma Microsoft Azure. A discussão neste artigo é sobre a execução do produto SQL Server que é conhecido por implementações no local nas máquinas virtuais do Azure, tirando partido da infraestrutura como um recurso de serviço do Azure. Capacidades de base de dados e de funcionalidades entre estas duas ofertas são diferentes e não devem misturar entre si. Consulte também: <https://azure.microsoft.com/services/sql-database/>
> 
>

Em geral, deve considerar a usando o SQL Server mais recentes versões para executar a carga de trabalho do SAP no Azure IaaS. As versões mais recentes do SQL Server oferecem uma melhor integração em alguns dos serviços do Azure e funcionalidade. Ou tem alterações que otimizar as operações numa infraestrutura de IaaS do Azure.

É recomendado que reveja [isso] [ virtual-machines-sql-server-infrastructure-services] documentação antes de continuar.

Nas seções a seguir, as partes das partes da documentação, sob a ligação acima são agregados e mencionados. Especificações em torno SAP mencionadas também e alguns conceitos são descritos mais detalhadamente. No entanto, é altamente recomendado para trabalhar com a documentação acima primeiro antes de ler a documentação específica do servidor SQL.

Há alguns do SQL Server em IaaS informações específicas, que deve saber antes de continuar:

* **Suporte para a versão SQL**: Para os clientes SAP, SQL Server 2008 R2 e superior é suportada na máquina Virtual do Microsoft Azure. Edições anteriores não são suportadas. Rever esta gerais [declaração de suporte](https://support.microsoft.com/kb/956893) para obter mais detalhes. Em geral, o SQL Server 2008 também é suportado pela Microsoft. No entanto, devido à funcionalidade significativa para SAP, que foi introduzida com o SQL Server 2008 R2, SQL Server 2008 R2 é a versão mínima para o SAP. Em geral, deve considerar a usando o SQL Server mais recentes versões para executar a carga de trabalho do SAP no Azure IaaS. As versões mais recentes do SQL Server oferecem uma melhor integração em alguns dos serviços do Azure e funcionalidade. Ou tem alterações que otimizar as operações numa infraestrutura de IaaS do Azure. Portanto, o documento é restrito a SQL Server 2016 e o SQL Server 2017.
* **Desempenho de SQL**: Microsoft Azure máquinas virtuais alojadas execute bem em comparação com outras ofertas de Virtualização de nuvem pública, mas os resultados individuais podem variar. Consulte o artigo [melhores práticas de desempenho para o SQL Server em máquinas de virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Utilização de imagens do Azure Marketplace**: A forma mais rápida de implementar uma nova VM do Azure da Microsoft é usar uma imagem do Azure Marketplace. Existem imagens no Azure Marketplace, que contêm as mais recentes versões do SQL Server. As imagens em que o SQL Server já está instalado não podem ser utilizadas imediatamente para aplicações SAP NetWeaver. O motivo é que o agrupamento do SQL Server padrão for instalado dentro dessas imagens e não o agrupamento exigido pela SAP NetWeaver sistemas. Para usar essas imagens, verifique os passos documentados no capítulo [utilizando uma imagem do SQL Server fora do Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Recomendações sobre a estrutura VM/VHD para implementações de servidor de SQL relacionadas com o SAP
Em conformidade com a descrição geral, os executáveis do SQL Server devem ser localizado ou instalado numa unidade de sistema de disco do SO da VM (unidade c:\).  Normalmente, a maioria das bases de dados de sistema do SQL Server não é utilizada num alto nível por carga de trabalho do SAP NetWeaver. Como resultado, as bases de dados do sistema do SQL Server (mestre, msdb e model) podem permanecer na unidade C:\. Uma exceção deve ser tempdb, que, no caso de cargas de trabalho do SAP, poderão necessitar de mais alto volume de dados ou volume de operações de e/s. Trabalho de e/s, que não deve ser aplicado ao VHD do SO. Para esses sistemas, devem ser efetuados os seguintes passos:


* Com o SAP todos os tipos de VM de certificação (consulte a nota SAP [1928533]), exceto as VMs de série, dados de tempdb e ficheiros de registo podem ser colocados na unidade D:\ não persistente. 
* No entanto, é recomendável utilizar vários arquivos de dados tempdb. Tenha em atenção volumes de unidade D:\ são diferentes consoante o tipo VM. Para obter tamanhos exatos da unidade D:\ das VMs diferentes, consulte o artigo [máquinas de virtuais de tamanhos para Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

Essas configurações permitem tempdb consumir mais espaço do que a unidade do sistema é capaz de fornecer. A unidade D:\ não persistentes também oferece a melhor latência de e/s e o débito (à exceção de VMs de série). Para determinar o tamanho de tempdb apropriada, pode verificar os tamanhos de tempdb em sistemas já existentes. 

>[!NOTE]
> no caso de colocar ficheiros de dados tempdb e o ficheiro de registo para uma pasta na unidade D:\ que criou, terá de certificar-se de que a pasta existir após um reinício VM. Uma vez que a unidade D:\ é recentemente inicializada depois de reiniciar todas as estruturas de diretório e arquivo são eliminadas. Uma possibilidade de recriar as estruturas de diretório eventual na unidade D:\ antes do início do serviço do SQL Server está documentado no [este artigo](https://www.sqlserver.co.uk/index.php/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Uma configuração de VM, que executa o SQL Server com uma base de dados do SAP e onde os dados de tempdb e o ficheiro de registo de tempdb são colocados na unidade D:\ teria o seguinte aspeto:

![Diagrama da simple configuração de disco VM para o SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

O diagrama acima mostra um caso simples. Como eluded no artigo [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md), number, e o tamanho dos discos de armazenamento Premium é dependente de fatores diferentes. Mas em geral, recomendamos:

- Utilizar espaços de armazenamento para formar um ou um pequeno número de volumes, que contêm os ficheiros de dados do SQL Server. O motivo por trás esta configuração é que na vida real há diversos bancos de dados do SAP com ficheiros de base de dados de tamanho diferente com a carga de trabalho de e/s diferente.
- Utilizar espaços de armazenamento para fornecer suficiente IOPS e para o ficheiro de registo de transações do SQL Server. Carga de trabalho IOPS potencial é, muitas vezes, a linha de orientação para o dimensionamento de volume de registo de transação e não o volume potencial do volume de transações do SQL Server
- Utilize o D:\drive para tempdb, desde que o desempenho é bom o suficiente. Se a carga de trabalho geral é limitada em desempenho por tmepdb a ser localizado na unidade D:\ poderá ter a serem consideradas para mover o tempdb em discos de armazenamento Premium de diferentes, conforme recomendado nas [este artigo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Especial para VMs de série M
Para VMS de série M do Azure, a latência de escrita no log de transação pode ser reduzida por fatores, em comparação comparadas o desempenho de armazenamento Premium do Azure, ao utilizar o acelerador de escrita do Azure. Por este motivo, deve implementar o acelerador de escrita do Azure para o VHD que formam o volume de registo de transações do SQL Server. Detalhes podem ser lidos no documento [acelerador de escrita](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>Os discos de formatação
Para o SQL Server, o tamanho do bloco NTFS para discos que contém ficheiros de dados e de registo do SQL Server deve ser a 64KB. Não é necessário para formatar a unidade D:\. Esta unidade vem previamente formatada.

Para certificar-se de que a criação de bases de dados ou restauro está não a inicializar os ficheiros de dados por zeroing o conteúdo dos ficheiros, certifique-se de que o contexto de utilizador, que o serviço SQL Server está em execução no tem uma determinada permissão. Normalmente, os utilizadores no grupo de administrador do Windows tem estas permissões. Se o serviço SQL Server for executado no contexto de utilizador do utilizador de administrador não - Windows, tem de atribuir o direito de usuário de que o usuário **executar tarefas de manutenção de volume**.  Consulte os detalhes neste artigo de Conhecimento Microsoft: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Impacto da compressão de base de dados
Em configurações em que a largura de banda de e/s pode ser um fator restritivo, a cada medida, que reduz o IOPS poderá ajudar a Stretch Database a carga de trabalho é possível executar um cenário de IaaS, como o Azure. Por conseguinte, se ainda não o fez, aplicar a compactação PAGE do SQL Server é recomendado pela SAP e Microsoft antes de carregar uma base de dados existente do SAP para o Azure.

A recomendação para fazem a compressão de base de dados antes de carregar para o Azure é fornecida sem dois motivos:

* A quantidade de dados a ser carregado é mais baixa.
* A duração da execução de compactação é mais curta, partindo do princípio que é possível usar o hardware mais forte com mais CPUs ou largura de banda de e/s superior ou menos e/s latência no local.
* Tamanhos de base de dados menores podem originar menos os custos de alocação do disco

Compressão de base de dados também funciona num máquinas de virtuais do Azure, como no local. Para obter mais detalhes sobre como compactar as bases de dados existentes do SQL Server do SAP NetWeaver, consulte o artigo [ferramenta de compressão melhorada SAP MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 e mais recentes - armazenar ficheiros base de dados diretamente no armazenamento de Blobs do Azure
SQL Server 2014 e posterior de versões de abrir a possibilidade para armazenar ficheiros de base de dados diretamente no Store de Blobs do Azure sem o "wrapper" de um VHD em torno delas. Especialmente com a utilização do armazenamento do Azure Standard ou tipos de VM mais pequenos, este tipo de implementação permite cenários em que pode ultrapassar os limites de IOPS que poderia ser impostas por um número limitado de discos que podem ser montados para alguns tipos VM mais pequenos. Esse modo de implantação funciona para bases de dados do utilizador no entanto, nem para bases de dados do sistema do SQL Server. Também funciona para os ficheiros de registo e de dados do SQL Server. Se gostaria de implementar uma base de dados do SQL Server do SAP desta forma, em vez de "encapsulamento" em VHDs, tenha em mente:

* A conta de armazenamento utilizado necessidades para ser na mesma região do Azure, como aquele que é utilizada para implementar o SQL Server da VM está a executar.
* São aplicáveis considerações listadas anteriormente sobre a distribuição de VHDs através de diferentes contas de armazenamento do Azure para que este método de implementações também. Significa que a contagem de operações de e/s contra os limites da conta de armazenamento do Azure.
* Em vez de contabilidade em relação a quota de e/s de armazenamento da VM, o tráfego de blobs de armazenamento que representa os arquivos de dados e de registo de SQL Server, irá ser considerados na largura de banda de rede da VM do tipo VM específico. Largura de banda de rede e armazenamento de um determinado tipo VM, consulte o artigo [máquinas de virtuais de tamanhos para Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
* Como resultado de envio de e/s de ficheiros através da quota de rede, são principalmente a stranding a quota de armazenamento e com que utilize apenas parcialmente a largura de banda geral da VM.
* Os IOPS e e/s débito metas de desempenho que tenha de armazenamento Premium do Azure para os tamanhos de disco diferente não deixa de poder aplicar. Mesmo que os blobs que criou estão localizados no armazenamento Premium do Azure. Os destinos estão documentados o artigo [High-performance Premium Storage e os discos geridos para VMs](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage). Como resultado de colocar ficheiros de dados do SQL Server e os ficheiros de registo diretamente nos blobs armazenados no armazenamento Premium do Azure, as características de desempenho podem ser diferentes em comparação com VHDs no armazenamento Premium do Azure.
* Baseada no anfitrião de colocação em cache como estando disponíveis para discos de armazenamento Premium do Azure não está disponível quando colocar ficheiros de dados do SQL Server diretamente nos blobs do Azure.
* Em VMs de série M, acelerador de escrita do Azure não pode ser utilizado para suportar escritas submilissegundos contra o ficheiro de registo de transações do SQL Server. 

Podem encontrar detalhes sobre esta funcionalidade no artigo [ficheiros de dados do SQL Server no Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

Recomendação para sistemas de produção é para evitar esta configuração e escolha os posicionamentos de dados do SQL Server em vez disso e ficheiros de registo em VHDs de armazenamento Premium do Azure em vez de diretamente nos blobs do Azure.


## <a name="sql-server-2014-buffer-pool-extension"></a>Extensão do conjunto de memória intermédia do SQL Server 2014
SQL Server 2014 introduziu um novo recurso, o que é chamado [extensão do conjunto de memória intermédia](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017). Esta funcionalidade estende o pool de buffers do SQL Server, que é mantida na memória com uma cache de segundo nível, que é sustentada pelo SSD local de um servidor ou VM. A extensão do conjunto de memória intermédia permite manter um conjunto de trabalho maior de dados "na memória'. Aceder ao armazenamento Standard do Azure em comparação com o acesso para a extensão do pool de buffers, o que é armazenado no SSDs locais de uma VM do Azure é muitos fatores mais rapidamente. Não comparar a extensão do conjunto de memória intermédia para a Cache de leitura de armazenamento Premium do Azure, tal como recomendado para ficheiros de dados do SQL Server, espera nenhuma vantagem significativa para extensões de conjunto de memórias intermédias. Motivo é que ambos os caches (extensão do conjunto de memória intermédia do SQL Server e Cache de leitura do armazenamento Premium) estão a utilizar os discos locais do nó de computação do Azure.

Experiências de obteve nesse meio tempo com a extensão do conjunto de memória intermédia do SQL Server com cargas de trabalho do SAP é misto e ainda não permite limpar recomendações sobre se deve utilizá-lo em todos os casos. O caso de ideal é que o conjunto de trabalho que requer que o aplicativo SAP ajusta a memória principal. Com o Azure ao mesmo tempo, oferecendo as VMs que acompanham até 4 TB de memória, ele deve ser alcançável para manter o conjunto de trabalho na memória. Por conseguinte, a utilização da extensão do conjunto de memória intermédia está limitada a alguns casos raros e não deve ser um caso de base.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Considerações sobre a cópia de segurança/recuperação para o SQL Server
Quando implementar o SQL Server para o Azure, tem de ser revista sua metodologia de cópia de segurança. Mesmo que o sistema não é um sistema de produção, a base de dados do SAP hospedado pelo SQL Server deve ser cópias periodicamente. Uma vez que o armazenamento do Azure mantém três imagens, uma cópia de segurança agora é menos importante em relativamente a uma falha de armazenamento de compensação. O motivo de prioridade para a manutenção de um plano de cópia de segurança e recuperação apropriadas, é mais do que compensar erros de lógica/manual, fornecendo o ponto em recursos em tempo de recuperação. Assim, o objetivo é qualquer uma das cópias de segurança de uso para restaurar a base de dados para um determinado ponto no tempo ou para utilizar as cópias de segurança no Azure para efetuar o seeding outro sistema ao copiar a base de dados existente. 

Para examinar diferentes do SQL Server possibilidades de cópia de segurança no Azure leia o artigo [cópia de segurança e restaurar para o SQL Server em máquinas de virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). O artigo aborda várias possibilidades diferentes.

### <a name="manual-backups"></a>Cópias de segurança manuais
Tem várias possibilidades para fazer cópias de segurança 'manuais' por:

1. Efetuar cópias de segurança do SQL Server convencionais em diretos discos Azure ligados. Este método tem a vantagem de que as cópias de segurança disponíveis de forma célere para atualizações de sistema e criar cópia de segurança de novos sistemas, como cópias dos sistemas existentes do SAP
2.  CU4 do SQL Server 2012 e superior pode fazer backup dos bancos de dados para um URL de armazenamento do Azure.
3.  Cópias de segurança de instantâneos de ficheiros para ficheiros de base de dados no armazenamento de Blobs do Azure. Este método só funciona quando os ficheiros de dados e de registo do SQL Server estão localizados no armazenamento de Blobs do Azure

O primeiro método é bem conhecida e aplicada em muitos casos, no mundo no local. No entanto, ele permite que tenha a tarefa para resolver a localização de cópia de segurança do termo mais tempo. Uma vez que não pretende manter as cópias de segurança para 30 ou mais dias no armazenamento do Azure ligadas localmente, tem a necessidade de utilizar serviços de cópia de segurança do Azure ou outra ferramenta de recuperação e backup de terceiros que inclui a gestão de acesso e de retenção para as cópias de segurança. Ou crie um servidor de ficheiros grandes no Azure através dos espaços de armazenamento do Windows.

O segundo método é mais perto descrito no artigo [cópia de segurança do SQL Server para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). Versões diferentes do SQL Server tem algumas variações desta funcionalidade. Por conseguinte, consulte a documentação para a sua verificação de versão específica do SQL Server. É importante ter em atenção que este artigo apresenta uma lista de muitas restrições. Tem a possibilidade de realizar a cópia de segurança em relação a optar por:

- Uma única página do Azure blob, que, em seguida, limita o tamanho da cópia de segurança para 1000 GB. Isso também limita a taxa de transferência que pode conseguir.
- Vários blobs de blocos do Azure (até 64), que permitem a um tamanho de cópia de segurança teórico de 12 TB. No entanto, os testes com bancos de dados do cliente revelou que o tamanho máximo de cópia de segurança pode ser menor do que o limite teórico. Neste caso, é responsável por gerir a retenção de cópias de segurança e o acesso também as cópias de segurança.


### <a name="automated-backup-for-sql-server"></a>Cópia de Segurança Automatizada para SQL Server
Cópia de segurança automatizada fornece um serviço de cópia de segurança automática para as edições do SQL Server Standard e Enterprise em execução numa VM do Windows no Azure. Este serviço é fornecido pelos [extensão de agente IaaS do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension), que é instalado automaticamente em imagens de máquinas virtuais do SQL Server Windows no portal do Azure. Se implementar suas próprias imagens de sistema operacional com o SQL Server instalado, terá de instalar as extensões VM em separado. As etapas necessárias estão documentadas nesta [artigo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Podem encontrar mais detalhes sobre as capacidades deste método nestes artigos:

- SQL Server 2014: [Cópia de segurança automatizada para máquinas de virtuais do SQL Server 2014 (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [V2 de cópia de segurança automatizada para máquinas virtuais do Azure (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Examinar a documentação, pode ver que a funcionalidade com as versões mais recentes do SQL Server melhorada. Mais detalhes sobre o SQL Server, cópias de segurança automáticas são lançadas no artigo [SQL Server cópia de segurança gerida para o Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). O limite de tamanho de cópia de segurança teórico é 12 TB.  As cópias de segurança automatizadas podem ser um método boa para tamanhos de cópia de segurança de atingir 12 TB. Uma vez que vários blobs são escritos em paralelo, pode esperar um débito de maior do que 100 MB/seg. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Cópia de segurança do Azure para VMs do SQL Server
Este novo método de cópias de segurança do SQL Server é oferecido a partir de Junho de 2018, como pré-visualização pública, pelos serviços de cópia de segurança do Azure. O método de cópia de segurança do SQL Server é o mesmo como outras ferramentas de terceiros estiver a utilizar, ou seja, a interface do SQL Server VSS/VDI para cópias de segurança do stream para uma localização de destino. Nesse caso, a localização de destino é o Cofre de serviço de recuperação do Azure.

Uma descrição mais detalhada desse método de cópia de segurança, que adiciona diversas vantagens das configurações de cópia de segurança centrais, monitorização, e administração está disponível [aqui](https://docs.microsoft.com/azure/backup/backup-azure-sql-database). 


### <a name="third-party-backup-solutions"></a>Soluções de cópia de segurança de terceiros
Para um grande número de clientes SAP, não havia nenhuma possibilidade de recomeçar e introduzir completas novas soluções de cópia de segurança para a parte do seu ambiente SAP que estava em execução no Azure. Como resultado, as soluções de cópia de segurança existentes precisava ser utilizado e expandidos para o Azure. Expandir soluções de cópia de segurança existentes para o Azure, normalmente, funcionado bem com a maioria dos principais fornecedores neste espaço. 


## <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Utilizar uma imagem do SQL Server fora do Microsoft Azure Marketplace
A Microsoft oferece as VMs no Azure Marketplace, que já contêm as versões do SQL Server. Para clientes SAP que necessitam de licenças para SQL Server e Windows, usar essas imagens pode ser uma oportunidade para cobrir a necessidade de licenças ao configurar as VMs com o SQL Server já instalado. Para usar essas imagens para SAP, as seguintes considerações precisam ser feitas:

* Versões de não avaliação do SQL Server adquirir custos superiores que uma VM de 'Windows-only' implementadas a partir do Azure Marketplace. Veja estes artigos para comparar preços: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> e <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Apenas pode utilizar versões do SQL Server, que são suportadas pelo SAP.
* O agrupamento de instância do SQL Server, o que é instalado nas VMs oferecidas no mercado do Azure não é o agrupamento que SAP NetWeaver requer a instância do SQL Server para ser executado. Pode alterar o agrupamento embora com as instruções na secção seguinte.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Alterar o agrupamento do SQL Server de uma VM do servidor do Microsoft Windows/SQL
Uma vez que as imagens do SQL Server no Azure Marketplace não são configuradas para utilizar o agrupamento, o que é exigido pela aplicações SAP NetWeaver, ele precisa ser alterada imediatamente após a implementação. Para o SQL Server, esta alteração do agrupamento pode ser feita com os passos seguintes assim que a VM tiver sido implementada e um administrador pode iniciar sessão na VM implementada:

* Abra uma janela de comando do Windows, como administrador.
* Altere o diretório para C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Execute o comando: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> é a conta, que foi definida como a conta de administrador ao implementar a VM pela primeira vez a partir da galeria.

O processo deverá demorar apenas alguns minutos. Para verificar se se o passo ficou com o resultado correto, execute os seguintes passos:

* Abra o SQL Server Management Studio.
* Abra uma janela de consulta.
* Execute o comando sp_helpsort na base de dados mestra do SQL Server.

O resultado desejado deve ser semelhante:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Se o resultado for diferente, pare a implementação de SAP e investigar por que o comando de configuração não funcionar conforme esperado. Implementação de aplicações SAP NetWeaver em instância do SQL Server com diferentes páginas de código do SQL Server que mencionado acima é **não** suportado.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server elevada disponibilidade para SAP no Azure
Com o SQL Server em implementações de IaaS do Azure para SAP, tem várias possibilidades diferentes para adicionar ao implementar a camada do DBMS elevada disponibilidade. Conforme discutido [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md) já, o Azure oferecem diferentes de tempo SLAs para uma única VM e um par de VMs implementadas num conjunto de disponibilidade do Azure. Pressuposição é de unidade para o SLA de tempo para as implementações de produção que requer a implementação em conjuntos de disponibilidade do Azure. Nesse caso, terá de implementar um mínimo de duas VMs na tal um conjunto de disponibilidade. Uma VM irá executar a instância do SQL Server Active Directory. A outra VM irá executar a instância passiva

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>Agrupamento do SQL Server com o servidor de ficheiros de escalamento horizontal do Windows
Com o Windows Server 2016, a Microsoft introduziu [espaços de armazenamento direto](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview). Com base na implementação direta de espaços de armazenamento, o clustering do SQL Server FCI é suportado. Pode encontrar detalhes no artigo [configurar a ativação pós-falha de Cluster de instância do SQL Server em máquinas de virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster). A solução requer também um balanceador de carga do Azure para lidar com o endereço IP virtual os recursos de cluster. Os ficheiros de base de dados do SQL Server são armazenados nos espaços de armazenamento. Por conseguinte, é um dado que seriam necessários para criar os espaços de armazenamento do Windows com base no armazenamento Premium do Azure. Uma vez que esta solução tem sido suportada para demasiado longo ainda não, não existem não os de clientes conhecidos do SAP que utilizam esta solução em cenários de produção do SAP.  

### <a name="sql-server-log-shipping"></a>Envio de Log do SQL Server
Um dos métodos de elevada disponibilidade (HA) é o envio de registo do SQL Server. Se as VMs que participem na configuração de HA tem que trabalhar com resolução de nomes, não há problema e a configuração no Azure não diferem dos qualquer configuração que é feita no local. Com respeito a configuração de envio de registo e os princípios em torno de envio de registo. Detalhes de envio de registo do SQL Server podem ser encontradas no artigo [sobre (SQL Server) de envio de registo](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017).

Funcionalidade de envio de registos do SQL Server foi mal utilizado no Azure para assegurar elevada disponibilidade numa região do Azure. No entanto nos seguintes cenários os clientes SAP estavam usando o envio de log com êxito em conjunto com o Azure:

- Cenários de recuperação após desastre de uma região do Azure para outra região do Azure
- Configuração de recuperação após desastre do local para uma região do Azure
- Cenários de cortar a ativação pós-falha do local para o Azure. Nesses casos, o envio de log é usado para sincronizar a nova implementação de DBMS no Azure com o produção em curso sistema no local. No momento da cutting sobre, produção é desligada e ela se torna-se de que os backups de log de transação de última e mais recente foi transferidos para a implementação de DBMS de Azure. Em seguida, a implementação de DBMS do Azure é aberta para produção.  



### <a name="database-mirroring"></a>Espelhamento de banco de dados
Espelhamento de banco de dados, como suportadas pelo SAP (consulte a nota SAP [965908]) baseia-se sobre como definir um parceiro de ativação pós-falha na cadeia de ligação de SAP. Nos casos em vários locais, partimos do princípio de que as duas VMs estão no mesmo domínio e que as instâncias de SQL Server do usuário contexto os dois estão a ser executado também um utilizador de domínio e tem privilégios suficientes em duas instâncias do SQL Server envolvidas. Por conseguinte, a configuração do espelhamento de banco de dados no Azure não diferem entre uma configuração típica no local.

A partir de implementações apenas na Cloud, o método mais fácil é ter outra configuração do domínio no Azure para que essas DBMS VMs (e VMs de SAP Idealmente dedicadas) dentro de um domínio.

Se um domínio não for possível, é possível usar certificados para a base de dados espelhamento pontos de extremidade, conforme descrito aqui: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Um tutorial para configurar o espelhamento de banco de dados no Azure pode ser encontrado aqui: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server Always On
Como Always On é suportada para o SAP no local (consulte a nota SAP [1772688]), é suportado em combinação com o SAP no Azure. Existem algumas considerações especiais sobre a implantação de escuta do grupo disponibilidade do SQL Server (não deve ser confundido com o conjunto de disponibilidade do Azure), uma vez que o Azure no momento não permite a criação de um objeto do AD/DNS, pois é possível no local. Portanto, alguns passos de instalação diferentes são necessários para superar o comportamento específico do Azure.

Algumas considerações usando um serviço de escuta do grupo de disponibilidade são:

* Utilizar um serviço de escuta do grupo de disponibilidade só é possível com o Windows Server 2012 ou superior como sistema operacional convidado da VM. Para o Windows Server 2012 terá de certificar-se de que esta atualização é aplicada: <https://support.microsoft.com/kb/2854082> 
* Para o Windows Server 2008 R2, esta atualização não existe e Always On, seria necessário ser utilizado da mesma forma como espelhamento de banco de dados, especificando um parceiro de ativação pós-falha na cadeia de ligações (feito por meio do SAP default.pfl parâmetro bds/mss/server - consulte a nota SAP [965908]).
* Quando utilizar um serviço de escuta do grupo de disponibilidade, as VMs de base de dados tem de ser ligada a um balanceador de carga dedicado. Para evitar que o Azure é atribuir novos endereços IP em casos em que ambas as VMs por acaso forem fechadas, uma deve atribuir endereços IP estáticos para as interfaces de rede essas VMs na configuração Always On (definir um endereço IP estático é descrito em [isso] [ virtual-networks-reserved-private-ip] artigo)
* Existem passos especiais é necessários quando criar a configuração de cluster WSFC onde o cluster precisa de um endereço IP especial atribuído, porque o Azure com sua funcionalidade atual atribuiria o nome do cluster o mesmo endereço IP à medida que o nó do cluster é criado em. Isso significa que um passo manual deve ser executado para atribuir um endereço IP diferente para o cluster.
* O serviço de escuta do grupo de disponibilidade vai ser criada no Azure com pontos finais de TCP/IP, que estão atribuídos às VMs em execução as réplicas primárias e secundárias do grupo de disponibilidade.
* Pode haver uma necessidade de proteger estes pontos finais com ACLs.

Documentação detalhada sobre como implementar o Always On com o SQL Server em VMs do Azure apresenta uma lista, como:

- [Introdução ao SQL Server Always On grupos de disponibilidade em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
- [Configurar um grupo de disponibilidade Always On em máquinas virtuais do Azure em regiões diferentes](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- [Configurar um balanceador de carga para um grupo de disponibilidade Always On no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> Se estiver a configurar o Balanceador de carga do Azure para o endereço IP virtual do serviço de escuta do grupo de disponibilidade, certifique-se de que o DirectServerReturn está configurado. configurar esta opção reduzirá a rede arredondar latência entre a camada de aplicação SAP e a camada do DBMS. 

SQL Server Always On é a mais comuns utilizada elevada disponibilidade e a funcionalidade de recuperação após desastre utilizada no Azure para implementações de carga de trabalho SAP. A maioria dos clientes utilize Always On para elevada disponibilidade numa única região do Azure. Se a implementação está limitada a apenas dois nós, tem duas opções de conectividade:

- Utilizar o serviço de escuta do grupo de disponibilidade. Com o serviço de escuta do grupo de disponibilidade, tem de implementar um balanceador de carga do Azure. Isto é, normalmente, o método predefinido da implementação. Aplicações SAP são configuradas para ligar-se contra o serviço de escuta do grupo de disponibilidade e não em relação a um único nó
- Utilizando os parâmetros de conectividade do espelhamento de banco de dados do SQL Server. Neste caso, terá de configurar a conectividade das aplicações SAP de uma forma em que ambos os nomes de nó são nomeados. Obter detalhes exatos sobre tal uma configuração do lado do SAP está documentado na nota SAP [#965908](https://launchpad.support.sap.com/#/notes/965908). Ao utilizar esta opção, teria sem a necessidade de configurar um serviço de escuta do grupo de disponibilidade. E com que não Azure Balanceador de carga para a elevada disponibilidade de SQL Server. Como resultado, a latência de rede entre a camada de aplicação SAP e a camada DBMS é mais baixa, uma vez que o tráfego de entrada para a instância do SQL Server não é encaminhado através do Balanceador de carga do Azure. Mas lembre-se de que esta opção só funciona se restringir o seu grupo de disponibilidade para abranger duas instâncias. 

Alguns clientes estão utilizando a funcionalidade do SQL Server Always On para a funcionalidade de recuperação após desastre adicionais entre regiões do Azure. Vários clientes também utilizam a capacidade para efetuar cópias de segurança a partir de uma réplica secundária. 

## <a name="sql-server-transparent-data-encryption"></a>Criptografia de dados transparente do SQL Server
Existe um número de clientes que estão a utilizar o SQL Server [encriptação de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) quando implementar o seu SQL Server do SAP bases de dados no Azure. A funcionalidade do SQL Server TDE é totalmente suportada pela SAP (consulte a nota SAP [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Aplicar a TDE do SQL Server
Em casos onde efetuar uma migração heterogênea do outro DBMS, em execução no local, para Windows e do SQL Server em execução no Azure, deve criar sua base de dados vazio de destino no SQL Server antes do tempo. Como passo seguinte, aplicaria funcionalidade do SQL Server TDE. Enquanto ainda estão em execução em sua produção sistema no local. Motivo que deseja realizar nesta sequência é que o processo de criptografar o banco de dados vazio pode demorar um pouco. Os processos da importação SAP seriam, em seguida, importar os dados no banco de dados encriptado durante a fase de tempo de inatividade. A sobrecarga da importação para uma base de dados encriptado tem um impacto de tempo nisso mais baixo que a encriptação da base de dados após a fase de exportação na para baixo a fase de tempo. Negativo experiências onde feitas ao tentar aplicar a TDE com carga de trabalho SAP em execução na parte superior da base de dados. Por conseguinte, a recomendação está tratando a implementação de TDE como uma atividade que precisa ser feito sem carga de trabalho SAP na base de dados específico.

Em casos onde mover bases de dados SAP SQL Server do local para o Azure, recomendamos que teste na infraestrutura de que pode obter a criptografia aplicada mais rápida. Para isso ter esses fatos em mente:

- Não é possível definir o número de threads são utilizados para aplicar a encriptação de dados na base de dados. O número de threads é majorly depende do número de volumes de disco, que os arquivos de dados e de registo do SQL Server são distribuídos por. Significa que os volumes distintos (letras de unidade), mais threads serão envolvidos em paralelo para efetuar a encriptação. Essa configuração menos forte contradiz um pouco com a sugestão de configuração do disco anterior sobre a criação de um ou um número menor de espaços de armazenamento para os ficheiros de base de dados do SQL Server em VMs do Azure. Uma configuração com um pequeno número de volumes poderia levar a um pequeno número de threads executando a encriptação. Encriptar um único thread está lendo as extensões de 64KB, encripta-lo e, em seguida, escreve um registo no ficheiro de registo de transação, informando que a extensão foi encriptada. Como resultado, a carga no log de transação é moderada.
- Em versões mais antigas do SQL Server, compactação de backup não obtiveram eficiência mais quando criptografou a sua base de dados do SQL Server. Esse comportamento poderia desenvolver num problema quando o seu plano foi encriptar o seu SQL Server da base de dados no local e, em seguida, copie uma cópia de segurança para o Azure para restaurar a base de dados no Azure. Compressão de cópias de segurança do SQL Server, normalmente, atinge uma taxa de compressão de fator de 4.
- Com o SQL Server 2016, o SQL Server introduziu novas funcionalidades que permite a compactação de bases de dados encriptadas também de uma forma eficiente. Ver [este blogs](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) para alguns detalhes.
 
Tratar o aplicativo da encriptação TDE sem nenhum pouco SAP carga de trabalho apenas, deve testar em sua configuração específica de se é melhor para aplicar a TDE para seu SAP da base de dados no local ou para fazê-lo no Azure. No Azure, certamente terá mais flexibilidade em termos de infraestrutura de excesso de aprovisionamento e diminuir a infraestrutura após TDE foi aplicada.

### <a name="using-azure-key-vault"></a>Com o Azure Key Vault
O Azure oferece o serviço de um [Key Vault](https://azure.microsoft.com/services/key-vault/) para armazenar chaves de encriptação. SQL Server no outro lado oferecem um conector para tirar partido do Cofre de chaves do Azure como armazenamento para certificados TDE.

Obter mais detalhes a utilizar o Azure Key Vault para SQL Server TDE lista, como:

- [Gestão de chaves extensível com o Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [SQL Server TDE gestão de chaves extensível com o Cofre de chaves do Azure - passos de configuração](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [SQL Server Connector manutenção e solução de problemas](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Mais perguntas dos clientes sobre o SQL Server encriptação de dados transparente – TDE + do Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Usando o SQL Server TDE, especialmente com o Azure key Vault, recomenda-se para utilizar os patches mais recentes do SQL Server 2014, o SQL Server 2016 e o SQL Server 2017. Razão é que com base nos comentários dos clientes, otimizações e correções de obteve aplicadas ao código. Por exemplo, verifique [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Geral SQL Server para o SAP no resumo do Azure
Existem muitas recomendações neste guia e recomendamos que lê-lo mais de uma vez antes de planear a implementação do Azure. Em geral, porém, não se esqueça de seguir o DBMS geral superior nas recomendações do Azure específicas:

1. Utilize a versão mais recente do DBMS, como o SQL Server 2017, o que tem as maioria das vantagens no Azure. 
2. Planear cuidadosamente o seu ambiente de sistema SAP no Azure para balancear as restrições do Azure e o esquema do arquivo de dados:
   * Não tem demasiados discos, mas terá uma parte suficiente para garantir que pode entrar seu IOPS necessários.
   * Se não utilizar Managed Disks, lembre-se de que o IOPS também são limitados por conta de armazenamento do Azure e que as contas de armazenamento são limitadas em cada subscrição do Azure ([mais detalhes][azure-subscription-service-limits]). 
   * Apenas faixa entre discos se de que precisa para alcançar um débito mais elevado.
3. Nunca instalar software ou colocar todos os ficheiros que requerem persistência na unidade D:\, à medida que é não permanente e qualquer coisa nesta unidade é perdida numa reinicialização do Windows.
4. Não utilize a colocação em cache do disco Standard do armazenamento do Azure.
5. Não utilize o Azure georreplicado padrão as contas de armazenamento.  Utilize localmente redundante para cargas de trabalho do DBMS.
6. Utilize a solução de HA/DR do seu fornecedor DBMS para replicar dados de base de dados.
7. Sempre utilizar a resolução de nomes, não se baseiam em endereços IP.
8. Utilizar o SQL Server TDE, aplica os patches mais recentes do SQL Server.
9. Utilize a compressão de base de dados mais elevada possível. Qual é a compactação page para o SQL Server.
10. Tenha cuidado ao utilizar imagens do SQL Server no Azure Marketplace. Se utilizar um SQL Server, tem de alterar o agrupamento de instância antes de instalar qualquer sistema SAP NetWeaver no mesmo.
11. Instalar e configurar a monitorização de anfitrião de SAP para o Azure, conforme descrito em [guia de implementação][deployment-guide].
