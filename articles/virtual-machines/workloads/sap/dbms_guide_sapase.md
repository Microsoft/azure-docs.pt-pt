---
title: Implementação de SAP ASE DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP | Documentos da Microsoft
description: Implementação de SAP ASE DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP
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
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9c37c5339dedb3db68ed9bd23cd28e16a195bbde
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005777"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Implementação de SAP ASE DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP

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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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



Neste documento, abrange várias áreas diferentes a serem considerados ao implementar o SAP ASE no IaaS do Azure. Como pré-condição para este documento, deve ter ler o documento [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md) e outros guias do [carga de trabalho SAP na documentação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-to-sap-ase-on-windows"></a>Informações específicas para o SAP ASE no Windows
A partir do Microsoft Azure, pode migrar as suas aplicações SAP ASE existentes para máquinas de virtuais do Azure. SAP ASE na máquina Virtual do Azure permite-lhe reduzir o custo total de propriedade de implantação, gerenciamento e manutenção de aplicativos de amplitude empresariais ao migrar facilmente esses aplicativos para o Microsoft Azure. Com o SAP ASE na máquina Virtual do Azure, os administradores e desenvolvedores podem continuar a utilizar as ferramentas de administração que estão disponíveis no local e o desenvolvimento mesmo.

SLAs para máquinas de virtuais do Azure, pode ser encontrada aqui: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

O Microsoft Azure oferece vários tipos diferentes de máquinas virtuais que permitem-lhe executar menor sistemas SAP e panoramas até sistemas de grande dimensão SAP e panoramas com milhares de usuários. Certificação de SAP dimensionamento números SAPS do SAP diferente SKUs de VM é fornecido na nota SAP [1928533].

Instruções e recomendações relativamente à utilização do armazenamento do Azure, a implementação de VMs de SAP ou a monitorização de SAP feitas no [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md) aplicam-se demasiado para implementações do SAP ASE.

### <a name="sap-ase-version-support"></a>Suporte para a versão de ASE SAP
SAP atualmente suporta SAP ASE versão 16.0 para uso com produtos SAP Business Suite. Todas as atualizações para o servidor SAP ASE ou os controladores ODBC e JDBC para ser utilizado com produtos SAP Business Suite, são fornecidas exclusivamente através do SAP Service Marketplace em: <https://support.sap.com/swdc>.

Não transferir atualizações para o servidor SAP ASE ou para os controladores ODBC e JDBC diretamente a partir de Web sites do Sybase. Para obter informações detalhadas sobre patches, que são suportadas para utilização com o SAP produtos no local e em máquinas de virtuais do Azure consulte as notas de SAP seguintes:

* [1590719]
* [1973241]

Informações gerais sobre como executar o SAP Business Suite no SAP ASE podem ser encontradas no [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Diretrizes de configuração do SAP ASE para SAP relacionados com o SAP ASE instalações em VMs do Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Estrutura de implementação SAP ASE
Executáveis de SAP ASE devem ser localizado ou instalado numa unidade de sistema de disco do SO da VM (unidade c:\). Normalmente, a maioria das bases de dados do sistema e as ferramentas SAP ASE não está a experienciar elevada carga de trabalho. Por conseguinte, as bases de dados e ferramentas de sistema (master, model, saptools, sybmgmtdb, sybsystemdb) podem permanecer na unidade C:\. 

Uma exceção pode ser a base de dados temporário, que, no caso de alguns SAP ERP e todas as cargas de trabalho BW, poderão necessitar de mais alto volume de dados ou volume de operações de e/s. Volumes ou de IOPS que não podem ser fornecidas por disco de SO da VM (unidade c:\).

Depende da versão do SAPInst/SWPM utilizada para instalar, a configuração de instância do SAP ASE pode ter o seguinte aspeto:

* Um único tempdb SAP ASE, o que é criado ao instalar o SAP ASE
* Um tempdb SAP ASE criado ao instalar o SAP ASE e um saptempdb adicional criados pela rotina de instalação SAP
* Um tempdb SAP ASE criado ao instalar o SAP ASE e um tempdb adicionais que tenha sido criado manualmente (por exemplo a seguir a nota SAP [1752266]) para satisfazer os requisitos de tempdb específicos de ERP/BW

Motivos de desempenho para ERP específico ou todas as cargas de trabalho BW, pode fazer sentido para armazenar os dispositivos de tempdb da tempdb além disso, criado numa unidade que não seja C:\. Não se existir nenhuma tempdb adicionais, é recomendado para criar um (a nota SAP [1752266]).

Para esses sistemas, os seguintes passos devem ser executados para o tempdb adicionalmente criado:

* Mover o primeiro dispositivo de tempdb para o primeiro dispositivo de base de dados do SAP
* Adicionar dispositivos de tempdb para cada um dos VHDs que contém um dispositivo da base de dados SAP

Tempdb de permite esta configuração consomem mais espaço do que a unidade do sistema pode fornecer. Como uma referência um pode verificar os tamanhos de dispositivo de tempdb em sistemas já existentes, que são executadas no local. Ou esta configuração permite que os números IOPS contra tempdb, que não pode ser fornecido com a unidade do sistema. Sistemas que estejam a executar no local podem ser utilizados para monitorizar a carga de trabalho de e/s em tempdb.

Nunca coloca todos os dispositivos SAP ASE na unidade D:\ da VM. Para o SAP ASE, esse aviso também se aplica a tempdb, mesmo que os objetos mantidos no tempdb são apenas temporários.

Para dados e implementações de ficheiros de registo de transação, as instruções e sugestões feitas no [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md). No caso de Implantações baseadas em Windows, a utilização de espaços de armazenamento do Windows é recomendada usar para criar conjuntos de stripe com IOPS, débito e volume suficiente.  

#### <a name="impact-of-database-compression"></a>Impacto da compressão de base de dados
Em configurações em que a largura de banda de e/s pode ser um fator restritivo, a cada medida, que reduz o IOPS poderá ajudar a Stretch Database a carga de trabalho é possível executar um cenário de IaaS, como o Azure. Por isso, recomenda-se para se certificar de que é utilizada compressão de SAP ASE antes de carregar uma base de dados existente do SAP para o Azure.

A recomendação para aplicar a compactação antes de carregar para o Azure é fornecida fora de vários motivos:

* A quantidade de dados a ser carregado para o Azure é mais baixa
* Durante a execução de compactação é mais curta, partindo do princípio que é possível usar o hardware mais forte com mais CPUs ou largura de banda de e/s superior ou menos e/s latência no local
* Tamanhos de base de dados menores podem originar menos os custos de alocação do disco

Compressão de dados e de LOB trabalhar numa VM alojada em máquinas de virtuais do Azure, como o faz no local. Para obter mais detalhes sobre como verificar se compressão já se encontra em utilização na base de dados existente do SAP ASE, veja a nota SAP [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilizar DBACockpit para monitorizar instâncias de base de dados
Para os sistemas SAP, o que estiver a utilizar o SAP ASE como plataforma de base de dados, o DBACockpit é acessível como janelas do browser incorporado na transação DBACockpit ou como Webdynpro. No entanto todas as funcionalidades de monitorização e administrar a base de dados estão disponível na implementação de Webdynpro de apenas o DBACockpit.

Como com sistemas no local vários passos são necessários para ativar todas as funcionalidades do SAP NetWeaver utilizada pela implementação Webdynpro do DBACockpit. Siga a nota SAP [1245200] para ativar a utilização de webdynpros e gerar aqueles necessários. Ao seguir as instruções nas notas para o acima, também configurar o Gestor de comunicação de Internet (icm), juntamente com as portas a utilizar para ligações http e https. A predefinição para http é semelhante a:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

e os links gerados na transação que dbacockpit é semelhante a:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Dependendo da forma como a Máquina Virtual do Azure que aloja o sistema SAP está ligada ao seu AD e DNS, terá de certificar-se de que o ICM está a utilizar um nome de anfitrião totalmente qualificado que pode ser resolvido na máquina onde abrir o DBACockpit do. Consulte a nota SAP [773830] para compreender como ICM determina o nome de anfitrião totalmente qualificado com base nos parâmetros de perfil e defina o parâmetro icm/host_name_full explicitamente se necessário.

Se tiver de implementar a VM num cenário de apenas na Cloud sem conectividade em vários locais entre no local e o Azure, terá de definir um endereço IP público e um domainlabel. O formato do nome DNS público da VM é semelhante a:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Podem encontrar mais detalhes relacionados com o nome DNS [aqui][virtual-machines-azurerm-versus-azuresm].

Definir o parâmetro de perfil icm/host_name_full para o SAP para o nome DNS da VM do Azure a ligação poderá ter um aspeto semelhante:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Neste caso, precisa certificar-se de que para:

* Adicionar regras de entrada para o grupo de segurança de rede no portal do Azure para as portas TCP/IP utilizada para comunicar com ICM
* Adicionar regras de entrada para a configuração da Firewall do Windows para as portas TCP/IP utilizada para comunicar com o ICM

Para um automatizada importados de todas as correções disponíveis, é recomendado para periodicamente aplicam-se a coleção de correção a nota SAP aplicáveis à sua versão SAP:

* [1558958]
* [1619967]
* [1882376]

Podem encontrar mais informações sobre o DBA Cockpit para SAP ASE nas notas para o SAP seguintes:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerações sobre a cópia de segurança/recuperação para o SAP ASE
Ao implementar o SAP ASE para o Azure, tem de ser revista sua metodologia de cópia de segurança. Mesmo para sistemas de não produção, as bases de dados do SAP devem ser cópias periodicamente. Uma vez que o armazenamento do Azure mantém três imagens, uma cópia de segurança poderá ser menos importante em relativamente a uma falha de armazenamento de compensação. A principal razão para a manutenção de um plano de cópia de segurança e restauro adequado é mais do que compensar erros de lógica/manual, fornecendo o ponto em recursos em tempo de recuperação. Assim, o objetivo é qualquer uma das cópias de segurança de uso para restaurar a base de dados para um determinado ponto no tempo ou para utilizar as cópias de segurança no Azure para efetuar o seeding outro sistema ao copiar a base de dados existente. 

Fazer backup e restaurar uma base de dados do Azure funcionam da mesma forma como no local. Consulte as notas de SAP:

* [1588316]
* [1585981]

Para obter detalhes sobre a criação de despejo de configurações e agendamento de cópias de segurança. Dependendo de suas necessidades pode configurar e a estratégia de base de dados e registo de informações do Estado para o disco para um dos discos existentes ou adicionar um disco adicional para a cópia de segurança. Para reduzir o risco de perda de dados em caso de erro, é recomendado para utilizar um disco em que não existem ficheiros de base de dados estão localizados.

Além de dados - e compressão de LOB, o SAP ASE também oferece compactação de backup. Para consumir menos espaço com informações de estado da base de dados e de registo é recomendado para utilizar a compressão de cópias de segurança. Para obter mais informações, consulte a nota SAP [1588316]. Comprimir a cópia de segurança também é crucial para reduzir a quantidade de dados a serem transferidos, se planear transferir cópias de segurança ou VHDs que contêm informações de cópia de segurança da máquina de Virtual do Azure para o local.

Não utilize a unidade D:\ como destino de despejo de base de dados ou log.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para Backups/restaurações
Como nas implementações bare-metal, cópia de segurança/restaurar desempenho é dependente de quantos volumes podem ser lidos em paralelo e o que pode ser o débito desses volumes. Tenha em atenção que a compactação de backup consome recursos da CPU. Esse consumo da CPU de compactação de backup pode desempenham um papel significativo em VMs com um número pequeno de threads de CPU. Portanto, pode assumir:

* Menos o número de discos utilizados para armazenar os dispositivos de base de dados, menor será a produtividade geral em ler
* Menor será que o número de CPU threads na VM, o mais grave o impacto da compactação de backup
* Os destinos menos (diretórios do Stripe, discos) para gravar a cópia de segurança, o menor a taxa de transferência

Para aumentar o número de destinos para escrever existem duas opções, que podem ser utilizado/combinadas consoante as suas necessidades:

* Repartição de volume de destino de cópia de segurança ao longo de vários discos montados para melhorar o débito IOPS nesse volume repartidos
* Criação de uma configuração de informação no nível de SAP ASE, que utiliza a mais do que um diretório de destino para escrever o despejo para

Repartição de um volume de disco ao longo de vários discos montados já foi discutida na [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md). Para obter mais informações sobre como utilizar vários diretórios na configuração de despejo do SAP ASE, consulte a documentação no sp_config_dump de procedimento armazenado, o que é utilizada para criar a configuração de informação sobre o [centros de informações do Sybase](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperação após desastre com VMs do Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicação de dados com o servidor de replicação do SAP Sybase
Com o servidor de replicação do Sybase de SAP (SRS), o SAP ASE fornece uma solução de reserva quente para transferir transações da base de dados para um local distante de forma assíncrona. 

A instalação e a operação de SRS também funciona funcionalmente numa VM alojada nos serviços de Máquina Virtual do Azure, como o faz no local.

SAP ASE HADR não necessita de um balanceador de carga interno do Azure e não tem dependências no nível clustering do SO. Ele funciona no Windows Azure e VMs do Linux. Para obter detalhes sobre o SAP ASE HADR leia os [Guia do usuário do SAP ASE HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Informações específicas para o SAP ASE no Linux
A partir do Microsoft Azure, pode migrar facilmente as aplicações SAP ASE existentes para máquinas de virtuais do Azure. SAP ASE numa máquina Virtual permite-lhe reduzir o custo total de propriedade de implantação, gerenciamento e manutenção de aplicativos de amplitude empresariais ao migrar facilmente esses aplicativos para o Microsoft Azure. Com o SAP ASE na máquina Virtual do Azure, os administradores e desenvolvedores podem continuar a utilizar as ferramentas de administração que estão disponíveis no local e o desenvolvimento mesmo.

Para implementar VMs do Azure é importante saber os SLAs oficiais, que podem ser encontrados aqui: <https://azure.microsoft.com/support/legal/sla>

Informações de tamanho do SAP e uma lista de SKUs de VM com certificação do SAP é fornecido na nota SAP [1928533]. SAP adicional documentos de dimensionamento de máquinas virtuais do Azure podem ser encontradas aqui <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> e aqui <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Instruções e recomendações sobre a utilização de armazenamento do Azure, VMs de implementação de SAP ou SAP de monitorização aplicável a implementações do SAP ASE em conjunto com as aplicações SAP, conforme indicado em todo os quatro primeiros capítulos deste documento.

As notas de SAP duas seguintes incluem informações gerais sobre o ASE em Linux e o ASE na Cloud:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Suporte para a versão de ASE SAP
SAP atualmente suporta SAP ASE versão 16.0 para uso com produtos SAP Business Suite. Todas as atualizações para o servidor SAP ASE ou os controladores ODBC e JDBC para ser utilizado com produtos SAP Business Suite, são fornecidas exclusivamente através do SAP Service Marketplace em: <https://support.sap.com/swdc>.

Como para as instalações no local, não transferir as atualizações para o servidor SAP ASE ou para os controladores ODBC e JDBC diretamente a partir de Web sites do Sybase. Para obter informações detalhadas sobre patches, que são suportadas para utilização com o SAP Business Suite produtos no local e em máquinas de virtuais do Azure consulte as notas de SAP seguintes:

* [1590719]
* [1973241]

Informações gerais sobre como executar o SAP Business Suite no SAP ASE podem ser encontradas no [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Diretrizes de configuração do SAP ASE para SAP relacionados com o SAP ASE instalações em VMs do Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Estrutura de implementação SAP ASE
Executáveis de SAP ASE devem ser localizado ou instalados no sistema de arquivo de raiz da VM (/sybase). Normalmente, a maioria das bases de dados do sistema e as ferramentas SAP ASE não está a experienciar elevada carga de trabalho. Por conseguinte, as bases de dados e ferramentas de sistema (master, model, saptools, sybmgmtdb, sybsystemdb) podem ser armazenados no sistema de ficheiros de raiz. 

Uma exceção pode ser a base de dados temporário, que, no caso de alguns SAP ERP e todas as cargas de trabalho BW, poderão necessitar de mais alto volume de dados ou volume de operações de e/s. Volumes ou de IOPS que não podem ser fornecidas por disco de SO da VM 

Dependendo da versão do SAPInst/SWPM utilizado para instalar o sistema, pode conter a base de dados:

* Um único tempdb de SAP ASE criado ao instalar o SAP ASE
* Um tempdb SAP ASE criado ao instalar o SAP ASE e um saptempdb adicional criados pela rotina de instalação SAP
* Um tempdb SAP ASE criado ao instalar o SAP ASE e um tempdb adicionais que tenha sido criado manualmente (por exemplo a seguir a nota SAP [1752266]) para satisfazer os requisitos de tempdb específicos de ERP/BW

Fora de motivos de desempenho para ERP específico ou todas as cargas de trabalho BW, pode fazer sentido armazenar os dispositivos de tempdb da tempdb adicionalmente criado (por SWPM ou manualmente) num sistema de arquivo separado, que pode ser representado por um disco de dados do Azure individual ou um RAID de Linux que abrange m discos de dados do Azure de múltiplas. Não se existir nenhuma tempdb adicionais, é recomendado para criar um (a nota SAP [1752266]).

Para esses sistemas, os seguintes passos devem ser efetuados para o tempdb adicionalmente criado:

* Mova o diretório de tempdb primeiro para o primeiro sistema de ficheiros da base de dados SAP
* Adicionar diretórios de tempdb para cada um dos discos que contém um sistema de ficheiros da base de dados SAP

Tempdb de permite esta configuração consomem mais espaço do que a unidade do sistema pode fornecer. Como uma referência um pode verificar os tamanhos de dispositivo de tempdb em sistemas já existentes, que são executadas no local. Ou esta configuração permite que os números IOPS contra tempdb, que não pode ser fornecido com a unidade do sistema. Sistemas que estejam a executar no local podem ser utilizados para monitorizar a carga de trabalho de e/s em tempdb.

Nunca coloca todos os diretórios SAP ASE no /mnt ou /mnt/resource da VM. Para o SAP ASE, esse aviso também se aplica a tempdb, mesmo que os objetos mantidos no tempdb são apenas temporários. Porque /mnt ou /mnt/resource é um espaço de temporário da VM do Azure predefinido, que não é persistente. Podem encontrar mais detalhes sobre o espaço temporário da VM do Azure no [neste artigo][virtual-machines-linux-how-to-attach-disk]

Para dados e implementações de ficheiros de registo de transação, as instruções e sugestões feitas no [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md). Em caso de Implantações baseadas em Linux a utilização de LVM ou MDADM é recomendada usar para criar conjuntos de stripe com IOPS, débito e volume suficiente. 

#### <a name="impact-of-database-compression"></a>Impacto da compressão de base de dados
Em configurações em que a largura de banda de e/s pode ser um fator restritivo, a cada medida, que reduz o IOPS poderá ajudar a Stretch Database a carga de trabalho é possível executar um cenário de IaaS, como o Azure. Por isso, recomenda-se para se certificar de que é utilizada compressão de SAP ASE antes de carregar uma base de dados existente do SAP para o Azure.

A recomendação para aplicar a compactação antes de carregar para o Azure é fornecida fora de vários motivos:

* A quantidade de dados a ser carregado para o Azure é mais baixa
* Durante a execução de compactação é mais curta, partindo do princípio que é possível usar o hardware mais forte com mais CPUs ou largura de banda de e/s superior ou menos e/s latência no local
* Tamanhos de base de dados menores podem originar menos os custos de alocação do disco

Compressão de dados e de LOB trabalhar numa VM alojada em máquinas de virtuais do Azure, como o faz no local. Para obter mais detalhes sobre como verificar se compressão já se encontra em utilização na base de dados existente do SAP ASE, veja a nota SAP [1750510]. Para obter mais informações sobre a compressão de base de dados, consulte a nota SAP [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilizar DBACockpit para monitorizar instâncias de base de dados
Para os sistemas SAP, o que estiver a utilizar o SAP ASE como plataforma de base de dados, o DBACockpit é acessível como janelas do browser incorporado na transação DBACockpit ou como Webdynpro. No entanto todas as funcionalidades de monitorização e administrar a base de dados estão disponível na implementação de Webdynpro de apenas o DBACockpit.

Como com sistemas no local vários passos são necessários para ativar todas as funcionalidades do SAP NetWeaver utilizada pela implementação Webdynpro do DBACockpit. Siga a nota SAP [1245200] para ativar a utilização de webdynpros e gerar aqueles necessários. Ao seguir as instruções nas notas para o acima, também configurar o Gestor de comunicação de Internet (icm), juntamente com as portas a utilizar para ligações http e https. A predefinição para http tem esta aparência:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

e os links gerados na transação DBACockpit terá um aspeto semelhante a este:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Dependendo da forma como a Máquina Virtual do Azure que aloja o sistema SAP está ligada ao seu AD e DNS, terá de certificar-se de que o ICM está a utilizar um nome de anfitrião totalmente qualificado que pode ser resolvido na máquina onde abrir o DBACockpit do. Consulte a nota SAP [773830] para compreender como ICM determina o nome de anfitrião totalmente qualificado, consoante os parâmetros de perfil e o parâmetro de conjunto icm/host_name_full explicitamente se necessário.

Se tiver de implementar a VM num cenário de apenas na Cloud sem conectividade em vários locais entre no local e o Azure, terá de definir um endereço IP público e um domainlabel. O formato do nome DNS público da VM é semelhante a:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Podem encontrar mais detalhes relacionados com o nome DNS [aqui][virtual-machines-azurerm-versus-azuresm].

Definir o parâmetro de perfil icm/host_name_full para o SAP para o nome DNS da VM do Azure a ligação poderá ter um aspeto semelhante:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Neste caso, precisa certificar-se de que para:

* Adicionar regras de entrada para o grupo de segurança de rede no portal do Azure para as portas TCP/IP utilizada para comunicar com ICM
* Adicionar regras de entrada para a configuração da Firewall do Windows para as portas TCP/IP utilizada para comunicar com o ICM

Para um automatizada importados de todas as correções disponíveis, é recomendado para periodicamente aplicam-se a coleção de correção a nota SAP aplicáveis à sua versão SAP:

* [1558958]
* [1619967]
* [1882376]

Podem encontrar mais informações sobre o DBA Cockpit para SAP ASE nas notas para o SAP seguintes:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerações sobre a cópia de segurança/recuperação para o SAP ASE
Ao implementar o SAP ASE para o Azure, tem de ser revista sua metodologia de cópia de segurança. Mesmo para sistemas de não produção, as bases de dados do SAP devem ser cópias periodicamente. Uma vez que o armazenamento do Azure mantém três imagens, uma cópia de segurança poderá ser menos importante em relativamente a uma falha de armazenamento de compensação. A principal razão para a manutenção de um plano de cópia de segurança e restauro adequado é mais do que compensar erros de lógica/manual, fornecendo o ponto em recursos em tempo de recuperação. Assim, o objetivo é qualquer uma das cópias de segurança de uso para restaurar a base de dados para um determinado ponto no tempo ou para utilizar as cópias de segurança no Azure para efetuar o seeding outro sistema ao copiar a base de dados existente. 

Fazer backup e restaurar uma base de dados do Azure funcionam da mesma forma como no local. Consulte as notas de SAP:

* [1588316]
* [1585981]

Para obter detalhes sobre a criação de despejo de configurações e agendamento de cópias de segurança. Dependendo das suas necessidades e estratégia, pode configurar despejos de registo e base de dados para disco para um dos discos existentes ou adicionar um disco adicional para a cópia de segurança. Para reduzir o risco de perda de dados em caso de erro, é recomendado para utilizar um disco onde não está localizado nenhum diretório/ficheiro de base de dados.

Além de dados - e compressão de LOB, o SAP ASE também oferece compactação de backup. Para consumir menos espaço com informações de estado da base de dados e de registo é recomendado para utilizar a compressão de cópias de segurança. Para obter mais informações, consulte a nota SAP [1588316]. Comprimir a cópia de segurança também é crucial para reduzir a quantidade de dados a serem transferidos, se planear transferir cópias de segurança ou VHDs que contêm informações de cópia de segurança da máquina de Virtual do Azure para o local.

Não utilize o /mnt de espaço temporário da VM do Azure ou /mnt/resource como destino de despejo de base de dados ou log.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para Backups/restaurações
Como nas implementações bare-metal, cópia de segurança/restaurar desempenho é dependente de quantos volumes podem ser lidos em paralelo e o que pode ser o débito desses volumes. Tenha em atenção que a compactação de backup consome recursos da CPU. Esse consumo da CPU de compactação de backup pode desempenham um papel significativo em VMs com um número pequeno de threads de CPU.  Portanto, pode assumir:

* Menos o número de discos utilizados para armazenar os dispositivos de base de dados, menor será a produtividade geral em ler
* Menor será que o número de CPU threads na VM, o mais grave o impacto da compactação de backup
* Os destinos menos (Linux RAID de software, discos) para gravar a cópia de segurança, o menor a taxa de transferência

Para aumentar o número de destinos para escrever existem duas opções, que podem ser utilizado/combinadas consoante as suas necessidades:

* Repartição de volume de destino de cópia de segurança ao longo de vários discos montados para melhorar o débito IOPS nesse volume repartidos
* Criação de uma configuração de informação no nível de SAP ASE, que utiliza a mais do que um diretório de destino para escrever o despejo para

Repartição de um volume de disco ao longo de vários discos montados já foi discutida na [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md). Para obter mais informações sobre como utilizar vários diretórios na configuração de despejo do SAP ASE, consulte a documentação no sp_config_dump de procedimento armazenado, o que é utilizada para criar a configuração de informação sobre o [centros de informações do Sybase](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperação após desastre com VMs do Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicação de dados com o servidor de replicação do SAP Sybase
Com o servidor de replicação do Sybase de SAP (SRS), o SAP ASE fornece uma solução de reserva quente para transferir transações da base de dados para um local distante de forma assíncrona. 

A instalação e a operação de SRS também funciona funcionalmente numa VM alojada nos serviços de Máquina Virtual do Azure, como o faz no local.

É suportado o ASE HADR através do servidor de replicação de SAP. É altamente recomendado para utilizar o SAP ASE 16.03 para tentar essa configuração. Instruções mais detalhadas para instalar essas configurações podem ser encontradas em detalhe neste [blogue](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/).