---
title: Implantação DBMS de máquinas virtuais Oracle Azure para | de carga de trabalho SAP Microsoft Docs
description: Implementação em Oracle do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, Oráculo, Data Guard
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/18/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 29b1bcec58d6350d0f63c3fe0ce11ef99a648019
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668983"
---
# <a name="azure-virtual-machines-oracle-dbms-deployment-for-sap-workload"></a>Azure Virtual Machines Oracle DBMS implantação para carga de trabalho SAP

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
[azure-ps]:/powershell/azure/
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
[storage-premium-storage-preview-portal]:../../disks-types.md
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
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
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


Este documento abrange várias áreas diferentes a ter em conta quando estiver a implementar a Oracle Database para a carga de trabalho SAP em Azure IaaS. Antes de ler este documento, recomendamos que leia [Considerações para a implementação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md). Recomendamos também que leia outros guias na carga de trabalho do [SAP na documentação do Azure.](./get-started.md) 

Pode encontrar informações sobre as versões Oracle e as versões de SO correspondentes que são suportadas para executar SAP on Oracle on Azure no SAP Note [2039619].

Informações gerais sobre a gestão da SAP Business Suite na Oracle podem ser encontradas na [SAP on Oracle.](https://www.sap.com/community/topic/oracle.html)
O software Oracle é suportado pela Oracle para funcionar no Microsoft Azure. Para obter mais informações sobre o suporte geral para o Windows Hyper-V e Azure, consulte o [Oracle e o Microsoft Azure FAQ](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Notas SAP relevantes para Oráculo, SAP e Azure 

As seguintes notas SAP estão relacionadas com a SAP on Azure.

| Número de nota | Título |
| --- | --- |
| [1928533] |Aplicações SAP em Azure: Produtos suportados e tipos de VM Azure |
| [2015553] |SAP no Microsoft Azure: Suporte pré-requisitos |
| [1999351] |Resolução de problemas melhorou a monitorização do Azure para o SAP |
| [2178632] |Principais métricas de monitorização do SAP no Microsoft Azure |
| [2191498] |SAP on Linux com Azure: Monitorização melhorada |
| [2039619] |Aplicações SAP no Microsoft Azure utilizando a base de dados Oracle: Produtos e versões suportados |
| [2243692] |Linux no Microsoft Azure (IaaS) VM: Problemas de licença SAP |
| [2069760] |Instalação e atualização oracle Linux 7.x SAP |
| [1597355] |Recomendação de espaço de troca para Linux |
| [2171857] |Oracle Database 12c - suporte ao sistema de ficheiros no Linux |
| [1114181] |Oracle Database 11g - suporte ao sistema de ficheiros no Linux |

As configurações e funcionalidades exatas que são suportadas pela Oracle e PELA SAP on Azure estão documentadas na [#2039619 SAP](https://launchpad.support.sap.com/#/notes/2039619)Note .

Windows e Oracle Linux são os únicos sistemas operativos suportados pela Oracle e SAP em Azure. As distribuições SLES e RHEL Linux amplamente utilizadas não são suportadas para a implantação de componentes da Oracle em Azure. Os componentes da Oracle incluem o cliente Oracle Database, que é usado por aplicações SAP para se conectar com o DBMS da Oracle. 

As exceções, de acordo com a SAP Note [#2039619,](https://launchpad.support.sap.com/#/notes/2039619)são componentes SAP que não utilizam o cliente Oracle Database. Tais componentes SAP são o enqueue autónomo da SAP, servidor de mensagens, serviços de replicação enqueue, WebDispatcher e SAP Gateway.  

Mesmo que esteja a executar as suas instâncias de aplicação Oracle DBMS e SAP no Oracle Linux, pode executar os seus Serviços Centrais SAP em SLES ou RHEL e protegê-lo com um cluster baseado em Pacemaker. Pacemaker como uma estrutura de alta disponibilidade não é suportado no Oracle Linux.

## <a name="specifics-for-oracle-database-on-windows"></a>Especificidades para a Base de Dados do Oráculo no Windows

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Diretrizes de configuração da Oracle para instalações SAP em VMs Azure no Windows

De acordo com o manual de instalação SAP, os ficheiros relacionados com a Oracle não devem ser instalados ou localizados no disco OS do VM (unidade c:). Máquinas virtuais de diferentes tamanhos podem suportar um número variado de discos anexos. Os tipos de máquinas virtuais mais pequenos podem suportar um número menor de discos anexos. 

Se tiver VMs mais pequenos e atingir o limite do número de discos que pode anexar ao VM, pode instalar/localizar o Oracle em casa, estágio, `saptrace` , , , , ou no disco `saparch` `sapbackup` `sapcheck` `sapreorg` oss. Estas partes dos componentes do Oracle DBMS não são muito intensas na produção de I/O e I/O. Isto significa que o disco DE pode lidar com os requisitos de E/S. O tamanho predefinido do disco de so deve ser de 127 GB. 

A Oracle Database e os ficheiros de registo de redo precisam de ser armazenados em discos de dados separados. Há uma exceção para o espaço temporário do Oráculo. `Tempfiles` pode ser criado em D:/ (unidade não persistente). O D não persistente:\ drive também oferece melhor latência e produção de I/O (com exceção de VMs série A). 

Para determinar a quantidade certa de espaço para o `tempfiles` , pode verificar os tamanhos dos `tempfiles` sistemas existentes.

### <a name="storage-configuration"></a>Configuração do armazenamento
Apenas a Oracle de instância única que utiliza discos formatados NTFS é suportada. Todos os ficheiros de base de dados devem ser armazenados no sistema de ficheiros NTFS em Discos Geridos (recomendados) ou em VHDs. Estes discos são montados no Azure VM e são baseados no [armazenamento de blob de página Azure](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) ou [em Discos Geridos Azure](../../managed-disks-overview.md). 

Consulte o artigo [Azure Storage types for SAP workload](./planning-guide-storage.md) para obter mais detalhes sobre os tipos específicos de armazenamento de blocos Azure adequados para a carga de trabalho DBMS.

Recomendamos vivamente a utilização de [Discos Geridos Azure](../../managed-disks-overview.md). Recomendamos também vivamente a utilização de [armazenamento premium Azure ou disco Azure Ultra](../../disks-types.md) para as suas implementações oracle Database.

As unidades de rede ou as ações remotas, como os serviços de ficheiros Azure, não são suportadas por ficheiros Oracle Database. Para obter mais informações, consulte:

- [Introdução ao Serviço do Ficheiro do Microsoft Azure](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [Persisting connections to Microsoft Azure Files (Ligações persistentes aos Ficheiros do Microsoft Azure)](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)


Se estiver a utilizar discos baseados no armazenamento de blob de página Azure ou discos geridos, as declarações em [Considerações para a implementação de DBMS de Máquinas Virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md) aplicam-se também a implementações com a Oracle Database.

Existem quotas na produção de IOPS para discos Azure. Este conceito é explicado em [Considerações para a implementação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md). As quotas exatas dependem do tipo VM que utiliza. Uma lista de tipos de VM com as suas quotas pode ser encontrada em [Tamanhos para máquinas virtuais Windows em Azure][virtual-machines-sizes-windows].

Para identificar os tipos de VM Azure suportados, consulte a Nota SAP [1928533].

A configuração mínima é a seguinte: 

| Componente | Disco | Colocação em cache | Piscina de armazenamento |
| --- | ---| --- | --- |
| \oráculo \<SID> \origlogaA & mirrlogB | Disco premium ou ultra | Nenhum | Não necessário |
| \oráculo \<SID> \origlogaB & mirrlogA | Disco premium ou ultra | Nenhum | Não necessário |
| \orá \<SID> \sapdata1... n | Disco premium ou ultra | Só de leitura | Pode ser usado para Premium |
| \oráculo \<SID> \oraarca | Standard | Nenhum | Não necessário |
| Oracle Home, `saptrace` ... | Disco DE SO (Premium) | | Não necessário |


A seleção de discos para hospedar registos de redo on-line deve ser impulsionada pelos requisitos do IOPS. É possível armazenar todos os sapdata1... n (espaços de mesa) num único disco montado, desde que o tamanho, o IOPS e a potência satisfaçam os requisitos. 

A configuração de desempenho é a seguinte:

| Componente | Disco | Colocação em cache | Piscina de armazenamento |
| --- | ---| --- | --- |
| \oráculo \<SID> \origlogaA | Disco premium ou ultra | Nenhum | Pode ser usado para Premium  |
| \oráculo \<SID> \origlogaB | Disco premium ou ultra | Nenhum | Pode ser usado para Premium |
| \oráculo \<SID> \mirrlogAB | Disco premium ou ultra | Nenhum | Pode ser usado para Premium |
| \oráculo \<SID> \mirrlogBA | Disco premium ou ultra | Nenhum | Pode ser usado para Premium |
| \orá \<SID> \sapdata1... n | Disco premium ou ultra | Só de leitura | Recomendado para prémio  |
| \oracle\SID\sapdata(n+1)* | Disco premium ou ultra | Nenhum | Pode ser usado para Premium |
| \oráculo \<SID> \oraarca* | Disco premium ou ultra | Nenhum | Não necessário |
| Oracle Home, `saptrace` ... | Disco DE SO (Premium) | Não necessário |

*(n+1): sistema de hospedagem, TEMP e espaços de mesa UNDO. O padrão de I/O dos espaços de tabelas System e Undo são diferentes dos outros espaços de mesa que hospedam dados de aplicações. Não fazer caching é a melhor opção para o desempenho dos espaços de mesa System e U dou.

*oraarca: a piscina de armazenamento não é necessária do ponto de vista do desempenho. Pode ser usado para ter mais espaço.

Se forem necessários mais IOPS em caso de armazenamento premium Azure, recomendamos a utilização de Pools de Armazenamento do Windows (apenas disponíveis no Windows Server 2012 e posteriormente) para criar um grande dispositivo lógico sobre vários discos montados. Esta abordagem simplifica a sobrecarga da administração para gerir o espaço do disco, e ajuda-o a evitar o esforço de distribuir manualmente ficheiros por vários discos montados.


#### <a name="write-accelerator"></a>Acelerador de Escritas
Para os VMs da Série M de Azure, a escrita de latência nos registos de redo online pode ser reduzida por fatores quando comparada com o armazenamento premium Azure. Ativar o Acelerador de Escrita Azure para os discos (VHDs) com base no Azure Premium Storage que são utilizados para ficheiros de registo de redo on-line. Para obter mais informações, consulte [o Write Accelerator](../../how-to-enable-write-accelerator.md). Ou utilize o disco Azure Ultra para o volume de registo de redo on-line.


### <a name="backuprestore"></a>Cópia de segurança/restauro
Para a funcionalidade de backup/restauro, as ferramentas SAP BR*para o Oráculo são suportadas da mesma forma que estão nos sistemas operativos standard do Windows Server. O Oracle Recovery Manager (RMAN) também é suportado para cópias de segurança para o disco e restauros a partir do disco.

Também pode utilizar o Azure Backup para executar uma cópia de segurança VM consistente com aplicações. O artigo [Planeie a sua infraestrutura de backup VM em Azure](../../../backup/backup-azure-vms-introduction.md) explica como o Azure Backup utiliza a funcionalidade VSS do Windows para executar backups consistentes com aplicações. As versões Oracle DBMS suportadas no Azure pela SAP podem alavancar a funcionalidade VSS para cópias de segurança. Para mais informações, consulte a documentação oracle [Conceitos básicos de backup e recuperação de bases de dados com VSS.](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701)


### <a name="high-availability"></a>Elevada disponibilidade
A Oracle Data Guard é suportada para fins de alta disponibilidade e recuperação de desastres. Para obter falhas automáticas na Data Guard, a sua necessidade de utilizar Fast-Start Failover (FSFA). O Observador (FSFA) desencadeou a falha. Se não utilizar o FSFA, só pode utilizar uma configuração manual de falha.

Para obter mais informações sobre a recuperação de desastres para bases de dados oracle em Azure, consulte [a recuperação de desastres para uma base de dados Oracle Database 12c em um ambiente Azure](../oracle/oracle-disaster-recovery.md).

### <a name="accelerated-networking"></a>Redes aceleradas
Para as implementações da Oracle no Windows, recomendamos vivamente uma rede acelerada, tal como descrito em [rede acelerada Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Considere também as recomendações que são feitas em [Considerações para a implantação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md). 
### <a name="other"></a>Outro
[Considerações para a implementação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md) descreve outros conceitos importantes relacionados com implementações de VMs com Oracle Database, incluindo conjuntos de disponibilidade de Azure e monitorização SAP.

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Especificidades para a Oracle Database em Oracle Linux
O software oracle é suportado pela Oracle para executar no Microsoft Azure com o Oracle Linux como o SO convidado. Para obter mais informações sobre o suporte geral para o Windows Hyper-V e Azure, consulte as [FAQ Azure e Oracle.](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 

O cenário específico das aplicações SAP que alavancam as Bases de Dados da Oracle também é suportado. Os detalhes são discutidos na parte seguinte do documento.

### <a name="oracle-version-support"></a>Suporte à versão Oráculo
Para obter informações sobre quais as versões Oracle e as versões oss correspondentes para executar SAP na Oracle em Azure Virtual Machines, consulte SAP Note [2039619].

Informações gerais sobre a gestão da SAP Business Suite na Oracle podem ser encontradas na [página da comunidade DAE.](https://www.sap.com/community/topic/oracle.html)

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Diretrizes de configuração da Oracle para instalações SAP em VMs Azure em Linux

De acordo com os manuais de instalação DA SAP, os ficheiros relacionados com a Oracle não devem ser instalados ou localizados em controladores de sistema para o disco de arranque de um VM. Diferentes tamanhos de máquinas virtuais suportam um número variado de discos anexos. Os tipos de máquinas virtuais mais pequenos podem suportar um número menor de discos anexos. 

Neste caso, recomendamos a instalação/localização da Oracle em casa, palco, `saptrace` , ou para iniciar o `saparch` `sapbackup` `sapcheck` `sapreorg` disco. Estas partes dos componentes do Oracle DBMS não são intensas na produção de I/O e I/O. Isto significa que o disco DE pode lidar com os requisitos de E/S. O tamanho padrão do disco de so é de 30 GB. Pode expandir o disco de arranque utilizando o portal Azure, PowerShell ou CLI. Depois de o disco de arranque ter sido expandido, pode adicionar uma divisória adicional para binários Oráculos.


### <a name="storage-configuration"></a>Configuração do armazenamento

Os sistemas de ficheiros de ext4, xfs, NFSv4.1 (apenas em Ficheiros Azure NetApp (ANF)) ou Oracle ASM (ver [#2039619](https://launchpad.support.sap.com/#/notes/2039619) DE NOTA SAP para requisitos de versão/versão) são suportados para ficheiros Oracle Database em Azure. Todos os ficheiros de base de dados devem ser armazenados nestes sistemas de ficheiros com base em VHDs, Discos Geridos ou ANF. Estes discos são montados no Azure VM e são baseados no [armazenamento de blob de página Azure,](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) [discos geridos Azure](../../managed-disks-overview.md)ou [Ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/).

Lista de requisitos mínimos como: 

- Para os núcleos Oracle Linux UEK, é necessário um mínimo de versão UEK 4 para suportar [SSDs premium Azure](../../premium-storage-performance.md#disk-caching).
- Para a Oracle com ANF o Oracle Linux suportado mínimo é de 8.2.
- Para a Oracle com ANF a versão mínima suportada oracle é de 19c (19.8.0.0)

Confira o artigo [Azure Storage types for SAP workload](./planning-guide-storage.md) for get more details of the specific Azure block storage types suitable for DBMS workload.

Utilizando o armazenamento do bloco Azure, é altamente recomendado utilizar [discos geridos Azure](../../managed-disks-overview.md) e [SSDs premium Azure](../../disks-types.md) para as suas implementações oracle Database.

Com exceção dos Ficheiros Azure NetApp, outros discos partilhados, unidades de rede ou ações remotas como os Azure File Services (AFS) não são suportados por ficheiros Oracle Database. Para obter mais informações, consulte o seguinte: 

- [Introdução ao Serviço do Ficheiro do Microsoft Azure](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [Persisting connections to Microsoft Azure Files (Ligações persistentes aos Ficheiros do Microsoft Azure)](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)

Se estiver a utilizar discos baseados no armazenamento de blob de página Azure ou discos geridos, as declarações feitas em [Considerações para a implementação de DBMS de Máquinas Virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md) aplicam-se também a implementações com a Oracle Database.

Existem quotas na produção de IOPS para discos Azure. Este conceito é explicado em [Considerações para a implementação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md). As quotas exatas dependem do tipo VM que é usado. Para obter uma lista de tipos de VM com as suas quotas, consulte [tamanhos para máquinas virtuais Linux em Azure][virtual-machines-sizes-linux].

Para identificar os tipos de VM Azure suportados, consulte a Nota SAP [1928533].

Configuração mínima:

| Componente | Disco | Colocação em cache | Despida* |
| --- | ---| --- | --- |
| /oráculo/ \<SID> /origlogaA & mirrlogB | Premium, Ultra disco ou ANF | Nenhum | Não necessário |
| /oráculo/ \<SID> /origlogaB & mirrlogA | Premium, Ultra disco ou ANF | Nenhum | Não necessário |
| /oráculo/ \<SID> /sapdata1... n | Premium, Ultra disco ou ANF | Só de leitura | Pode ser usado para Premium |
| /oráculo/ \<SID> /oraarca | Standard ou ANF | Nenhum | Não necessário |
| Oracle Home, `saptrace` ... | Disco DE SO (Premium) | | Não necessário |

*Descascar: listras LVM ou MDADM usando RAID0

A seleção de discos para hospedar os registos de redo online da Oracle deve ser impulsionada pelos requisitos do IOPS. É possível armazenar todos os sapdata1... n (espaços de mesa) num único disco montado, desde que o volume, o IOPS e a potência satisfaçam os requisitos. 

Configuração de desempenho:

| Componente | Disco | Colocação em cache | Despida* |
| --- | ---| --- | --- |
| /oráculo/ \<SID> /origlogaA | Premium, Ultra disco ou ANF | Nenhum | Pode ser usado para Premium  |
| /oráculo/ \<SID> /origlogaB | Premium, Ultra disco ou ANF | Nenhum | Pode ser usado para Premium |
| /oráculo/ \<SID> /mirrlogAB | Premium, Ultra disco ou ANF | Nenhum | Pode ser usado para Premium |
| /oráculo/ \<SID> /mirrlogBA | Premium, Ultra disco ou ANF | Nenhum | Pode ser usado para Premium |
| /oráculo/ \<SID> /sapdata1... n | Premium, Ultra disco ou ANF | Só de leitura | Recomendado para Premium  |
| /oráculo/ \<SID> /sapdata(n+1)* | Premium, Ultra disco ou ANF | Nenhum | Pode ser usado para Premium |
| /oráculo/ \<SID> /oraarca* | Premium, Ultra disco ou ANF | Nenhum | Não necessário |
| Oracle Home, `saptrace` ... | Disco DE SO (Premium) | Não necessário |

*Descascar: listras LVM ou MDADM usando RAID0

*(n+1):hosting SYSTEM, TEMP e UNDO tablespaces: O padrão de E/S dos espaços de mesa System e Undo são diferentes dos outros espaços de mesa que hospedam dados de aplicações. Não fazer caching é a melhor opção para o desempenho dos espaços de mesa System e U dou.

*oraarca: a piscina de armazenamento não é necessária do ponto de vista do desempenho.


Se forem necessários mais IOPS ao utilizar o armazenamento premium Azure, recomendamos a utilização de LVM (Logical Volume Manager) ou MDADM para criar um grande volume lógico sobre vários discos montados. Para obter mais informações, consulte [considerações para a implementação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md) no que diz respeito a diretrizes e ponteiros sobre como alavancar a LVM ou o MDADM. Esta abordagem simplifica a sobrecarga da administração da gestão do espaço do disco e ajuda-o a evitar o esforço de distribuir manualmente ficheiros por vários discos montados.

Se planeia utilizar ficheiros Azure NetApp certifique-se de que o cliente dNFS está configurado corretamente. A utilização do dNFS é obrigatória para ter um ambiente suportado. A configuração do dNFS está documentada no artigo [Criar uma Base de Dados Oráculo em NFS Direta.](https://docs.oracle.com/en/database/oracle/oracle-database/19/ntdbi/creating-an-oracle-database-on-direct-nfs.html#GUID-2A0CCBAB-9335-45A8-B8E3-7E8C4B889DEA)

Um exemplo que demonstra a utilização de NFS baseados em Ficheiros Azure NetApp para bases de dados Oracle é apresentado no blog [Deploy SAP AnyDB (Oracle 19c) com ficheiros Azure NetApp](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043).


#### <a name="write-accelerator"></a>Acelerador de Escritas
Para os VMs da série M azul, quando utilizar o Acelerador de Escrita Azure, a escrita de latência nos registos de redo online pode ser reduzida por fatores ao utilizar o armazenamento premium Azure. Ativar o Acelerador de Escrita Azure para os discos (VHDs) com base no Azure Premium Storage que são utilizados para ficheiros de registo de redo on-line. Para obter mais informações, consulte [o Write Accelerator](../../how-to-enable-write-accelerator.md). Ou utilize o disco Azure Ultra para o volume de registo de redo on-line.


### <a name="backuprestore"></a>Cópia de segurança/restauro
Para a funcionalidade de backup/restauro, as ferramentas SAP BR*para o oráculo são suportadas da mesma forma que estão em metal nu e Hiper-V. O Oracle Recovery Manager (RMAN) também é suportado para cópias de segurança para o disco e restauros a partir do disco.

Para obter mais informações sobre como pode utilizar os serviços de Backup e Recuperação do Azure para fazer backup e recuperar bases de dados da Oracle, consulte [Back up e recupere uma base de dados oracle Database 12c numa máquina virtual Azure Linux](../oracle/oracle-overview.md).

### <a name="high-availability"></a>Elevada disponibilidade
A Oracle Data Guard é suportada para fins de alta disponibilidade e recuperação de desastres. Para obter falhas automáticas na Data Guard, é necessário utilizar Fast-Start Failover (FSFA). A funcionalidade Do Observador (FSFA) despoleta a falha. Se não utilizar o FSFA, só pode utilizar uma configuração manual de falha. Para obter mais informações, consulte [implementar o Oracle Data Guard numa máquina virtual Azure Linux](../oracle/configure-oracle-dataguard.md).


Os aspetos de recuperação de desastres para as bases de dados da Oracle em Azure são apresentados no artigo [Recuperação de desastres para uma base de dados Oracle Database 12c em um ambiente Azure](../oracle/oracle-disaster-recovery.md).

### <a name="accelerated-networking"></a>Redes aceleradas
O suporte para a rede acelerada Azure em Oracle Linux é fornecido com Oracle Linux 7 Update 5 (Oracle Linux 7.5). Se não conseguir atualizar para a mais recente versão Oracle Linux 7.5, pode haver uma solução alternativa utilizando o Kernel Compatível com RedHat (RHCK) em vez do kernel Oracle UEK. 

A utilização do núcleo RHEL dentro do Oracle Linux é suportada de acordo com a [#1565179 SAP](https://launchpad.support.sap.com/#/notes/1565179)Note . Para a rede acelerada Azure, a libertação mínima de kernel RHCKL deve ser 3.10.0-862.13.1.1.el7. Se estiver a utilizar o núcleo UEK no Oracle Linux em conjunto com [o Azure Accelerated Networking,](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)tem de utilizar a versão 5 do kernel da Oracle UEK.

Se estiver a implementar VMs a partir de uma imagem que não se baseie no Azure Marketplace, então tem de copiar ficheiros de configuração adicionais para o VM executando o seguinte código: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


## <a name="next-steps"></a>Passos seguintes
Leia o artigo 

- [Considerações para a implantação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md)
