---
title: Implementação de DBMS de máquinas virtuais do Azure do Oracle para a carga de trabalho do SAP | Documentos da Microsoft
description: Implementação em Oracle do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP
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
ms.date: 12/14/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6abfd26e63cc8001f501371fffce0a4c10f4ff85
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483524"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP

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


Este documento aborda várias áreas diferentes a serem considerados quando estiver a implementar base de dados Oracle para a carga de trabalho SAP no Azure IaaS. Antes de ler este documento, recomendamos que leia [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md). Também recomendamos que leia outros guias do [carga de trabalho SAP na documentação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

Pode encontrar informações sobre versões de Oracle e versões de SO correspondentes, que são suportadas para executar o SAP no Oracle no Azure na nota SAP [2039619].

Informações gerais sobre como executar o SAP Business Suite no Oracle, podem ser encontradas em [SAP no Oracle](https://www.sap.com/community/topic/oracle.html).
Oracle software é suportado pelo Oracle para ser executado no Microsoft Azure. Para obter mais informações sobre o suporte geral para Windows. o Hyper-V e o Azure, consulte a [Oracle e perguntas frequentes do Microsoft Azure](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Notas SAP relevantes para a Oracle, SAP e o Azure 

As seguintes notas de SAP estão relacionados com o SAP no Azure.

| Número de nota | Título |
| --- | --- |
| [1928533] |Aplicações de SAP no Azure: Produtos suportados e tipos de VM do Azure |
| [2015553] |SAP no Microsoft Azure: Pré-requisitos de suporte |
| [1999351] |Resolução de problemas avançado do Azure de monitorização para SAP |
| [2178632] |Chave de métricas de monitorização para o SAP no Microsoft Azure |
| [2191498] |SAP no Linux com o Azure: Melhorada a monitorização |
| [2039619] |Aplicações de SAP no Microsoft Azure utilizando a base de dados do Oracle: Produtos suportados e versões |
| [2243692] |Linux no Microsoft Azure (IaaS) VM: Problemas de licença do SAP |
| [2069760] |Instalação de SAP do Oracle Linux 7.x e atualização |
| [1597355] |Recomendação de espaço de comutação para Linux |
| [2171857] |Base de dados Oracle 12C - suporte de sistema de ficheiros no Linux |
| [1114181] |Oracle Database 11g - suporte de sistema de ficheiros no Linux |

As configurações exatas e funcionalidades para o que são suportados pelo Oracle e SAP no Azure estão documentados na nota SAP [#2039619](https://launchpad.support.sap.com/#/notes/2039619).

Windows e Linux da Oracle são os sistemas de operativos apenas que são suportados pelo Oracle e SAP no Azure. As distribuições de SLES e RHEL Linux amplamente utilizadas não são suportadas para a implementação de componentes de Oracle no Azure. Componentes de Oracle incluem o cliente de base de dados Oracle, o que é utilizado por aplicações SAP para ligar-se contra o DBMS de Oracle. 

Exceções, de acordo com a nota SAP [#2039619](https://launchpad.support.sap.com/#/notes/2039619), são componentes SAP que não utilizam o cliente de base de dados Oracle. Esses componentes SAP são enfileirar autónomo, servidor de mensagens, serviços de replicação de colocar em fila, WebDispatcher e SAP Gateway do SAP.  

Mesmo se estiver a executar o seu DBMS de Oracle e instâncias de aplicações SAP no Oracle Linux, pode executar o SAP Central Services no SLES ou RHEL e protegê-lo com um cluster baseado em Pacemaker. Pacemaker como uma estrutura de elevada disponibilidade não é suportada no Oracle Linux.

## <a name="specifics-for-oracle-database-on-windows"></a>Especificações da base de dados do Oracle no Windows

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Diretrizes de configuração do Oracle para instalações de SAP em VMs do Azure no Windows

Em conformidade com o manual de instalação SAP, os ficheiros relacionados com a Oracle não devem ser instalados ou localizados no driver de sistema para o disco de SO de uma VM (unidade c). Máquinas de virtuais de tamanhos diferentes pode suportar um número variável de discos ligados. Tipos de máquinas virtuais mais pequenos podem suportar um número menor de discos ligados. 

Se tiver VMs menores, é recomendável instalar/localização Oracle home, fase, "saptrace", "saparch", "sapbackup", "sapcheck" ou "sapreorg" para o disco do SO. Essas partes dos componentes de Oracle DBMS não são intensa em e/s e e/s débito. Isso significa que o disco do SO pode lidar com os requisitos de e/s. O tamanho predefinido do disco do SO é de 127 GB. 

Se existir o espaço livre suficiente não estiver disponível, o disco pode ser [redimensionado](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk) a 2048 GB. Base de dados Oracle e Refazer registo necessidade de ficheiros para serem armazenados em discos de dados separado. Existe uma exceção para o espaço de tabelas temporária do Oracle. Tempfiles podem ser criadas em d: / (não persistentes unidade). A unidade D:\ não persistentes também oferece a melhor latência de e/s e o débito (à exceção de VMs de série). 

Para determinar a quantidade certa de espaço para o tempfiles, pode verificar os tamanhos de tempfiles em sistemas já existentes.

### <a name="storage-configuration"></a>Configuração do armazenamento
Só o Oracle de instância única usando o NTFS formatado discos é suportado. Todos os ficheiros de base de dados devem ser armazenados no sistema de ficheiros NTFS em Managed Disks (recomendado) ou em VHDs. Estes discos estão montados à VM do Azure e são baseiam [armazenamento de BLOBs de página do Azure](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) ou [Managed Disks do Azure](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview). 

Recomendamos vivamente a utilização [Managed Disks do Azure](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview). Também recomendamos usando [premium SSDs](../../windows/disks-types.md) para as implementações de base de dados Oracle.

Unidades de rede ou partilhas remotas, como os serviços de ficheiros do Azure não são suportadas para os ficheiros de base de dados Oracle. Para obter mais informações, consulte:

- [Introducing Microsoft Azure File Service (Introdução ao Serviço de Ficheiros do Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Persisting connections to Microsoft Azure Files (Ligações persistentes aos Ficheiros do Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)


Se estiver a utilizar discos que são baseados no armazenamento de BLOBs de página do Azure ou as instruções do Managed Disks [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md) aplicam-se a implementações com a base de dados Oracle também.

Existem quotas no débito IOPS de discos do Azure. Esse conceito é explicado na [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md). As quotas exatas dependem o tipo de VM que utiliza. Uma lista dos tipos VM com as respetivas quotas pode ser encontrada em [máquinas de virtuais de tamanhos para Windows no Azure][virtual-machines-sizes-windows].

Para identificar os tipos de VM do Azure suportados, consulte a nota SAP [1928533].

A configuração mínima é o seguinte: 

| Componente | Disco | Colocação em cache | Agrupamento de armazenamento |
| --- | ---| --- | --- |
| \oracle\<SID > \origlogaA & mirrlogB | Premium | Nenhuma | Não é preciso |
| \oracle\<SID > \origlogaB & mirrlogA | Premium | Nenhuma | Não é preciso |
| \oracle\<SID > \sapdata1...n | Premium | Só de leitura | Pode ser utilizado |
| \oracle\<SID > \oraarch | Standard | Nenhuma | Não é preciso |
| Página inicial do Oracle, saptrace,... | Disco do SO | | Não é preciso |


Seleção de discos para o alojamento de registos de Refazer online deve ser conduzida por requisitos de IOPs. É possível armazenar todos os sapdata1... n (tablespaces) num único disco montado, desde que o tamanho, IOPS e débito satisfazem os requisitos. 

A configuração de desempenho é o seguinte:

| Componente | Disco | Colocação em cache | Agrupamento de armazenamento |
| --- | ---| --- | --- |
| \oracle\<SID > \origlogaA | Premium | Nenhuma | Pode ser utilizado  |
| \oracle\<SID > \origlogaB | Premium | Nenhuma | Pode ser utilizado |
| \oracle\<SID > \mirrlogAB | Premium | Nenhuma | Pode ser utilizado |
| \oracle\<SID > \mirrlogBA | Premium | Nenhuma | Pode ser utilizado |
| \oracle\<SID > \sapdata1...n | Premium | Só de leitura | Recomendado  |
| \oracle\SID\sapdata(n+1)* | Premium | Nenhuma | Pode ser utilizado |
| \oracle\<SID > \oraarch* | Premium | Nenhuma | Não é preciso |
| Página inicial do Oracle, saptrace,... | Disco do SO | Não é preciso |

* (n + 1): tablespaces de sistema, TEMP e DESFAZER de alojamento. O padrão de e/s de sistema e desfazer tablespaces são diferentes dos outros tablespaces que aloja os dados de aplicação. Sem colocação em cache é a melhor opção para o desempenho das tablespaces sistema e desfazer.

* oraarch: agrupamento de armazenamento não é necessário a partir de um ponto de vista do desempenho. Ele pode ser usado para obter mais espaço.

Se mais IOPS forem necessárias, recomendamos que utilize agrupamentos de armazenamento do Windows (apenas disponível no Windows Server 2012 e posterior) para criar um dispositivo lógico grande ao longo de vários discos montados. Esta abordagem simplifica a administração sobrecarga para gerir o espaço em disco e ajuda a evitar o esforço de distribuição de arquivos manualmente em vários discos montados.


#### <a name="write-accelerator"></a>Acelerador de Escritas
Para VMs de série M do Azure, a latência de escrita nos registos de Refazer online pode ser reduzida por fatores em comparação com o armazenamento Premium do Azure. Ative o acelerador de escrita do Azure para os discos (VHDs) com base no armazenamento Premium do Azure que são utilizados para ficheiros de registo de Refazer online. Para obter mais informações, consulte [acelerador de escrita](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).


### <a name="backuprestore"></a>Cópia de segurança/restauro
Para a funcionalidade de cópia de segurança/restauro, a SAP BR * ferramentas para Oracle são suportadas da mesma forma como estão em sistemas de operativos do Windows Server standard. Gestor de recuperação de Oracle (RMAN) também é suportado para cópias de segurança em disco e restaura a partir de disco.

Pode também utilizar cópias de segurança do Azure para executar uma cópia de segurança VM consistentes. O artigo [planear a sua infraestrutura de cópia de segurança de VM no Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) explica como cópia de segurança do Azure utiliza a funcionalidade do VSS do Windows para a execução de cópias de segurança consistentes com aplicações. As versões de Oracle DBMS que são suportadas pelo SAP no Azure podem tirar partido da funcionalidade do VSS para cópias de segurança. Para obter mais informações, consulte a documentação do Oracle [conceitos básicos de cópia de segurança da base de dados e recuperação com o VSS](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701).


### <a name="high-availability"></a>Elevada disponibilidade
Oracle Data Guard é suportada para elevada disponibilidade e fins de recuperação após desastre. Para alcançar a ativação pós-falha automática na proteção de dados, a necessidade de usar a ativação pós-falha de início rápido (FSFA). O observador (FSFA) aciona a ativação pós-falha. Se não usar FSFA, só pode utilizar uma configuração de ativação pós-falha manual.

Para obter mais informações sobre recuperação de desastres para bancos de dados do Oracle no Azure, consulte [recuperação após desastre para uma base de dados do Oracle Database 12C no ambiente do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery).

### <a name="accelerated-networking"></a>Redes aceleradas
Para implementações do Oracle no Windows, é altamente recomendável em rede acelerado conforme descrito em [Azure accelerated networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Considere também as recomendações efetuadas numa [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md). 
### <a name="other"></a>Outros
[Considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md) descreve outros conceitos importantes relacionados com implementações de VMs com a base de dados Oracle, incluindo conjuntos de disponibilidade do Azure e a monitorização de SAP.

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Especificações da base de dados do Oracle no Oracle Linux
Oracle software é suportado pelo Oracle para ser executado no Microsoft Azure com o Oracle Linux como sistema operacional convidado. Para obter mais informações sobre o suporte geral para Windows. o Hyper-V e o Azure, consulte a [do Azure e o Oracle FAQ](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

Também é suportado o cenário específico de aplicações SAP tirar partido das bases de dados Oracle. Detalhes são discutidos na próxima parte do documento.

### <a name="oracle-version-support"></a>Suporte da versão Oracle
Para obter informações sobre qual Oracle versões e versões de SO correspondentes são suportadas para executar o SAP no Oracle em máquinas de virtuais do Azure, consulte a nota SAP [2039619].

Pode encontrar informações gerais sobre como executar o SAP Business Suite no Oracle no [SAP na página de Comunidade do Oracle](https://www.sap.com/community/topic/oracle.html).

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Diretrizes de configuração do Oracle para instalações de SAP em VMs do Azure no Linux

Em conformidade com manuais de instalação SAP, os ficheiros relacionados com a Oracle não devem ser instalados ou localizados em controladores de sistema para o disco de inicialização de uma VM. Diversos tamanhos de máquinas virtuais suportam um número variável de discos ligados. Tipos de máquinas virtuais mais pequenos podem suportar um número menor de discos ligados. 

Neste caso, é recomendável instalar/localização de home, fase, saptrace Oracle, saparch, sapbackup, sapcheck ou sapreorg para o disco de arranque. Essas partes dos componentes de Oracle DBMS não são intensa em e/s e e/s débito. Isso significa que o disco do SO pode lidar com os requisitos de e/s. O tamanho predefinido do disco do SO é 30 GB. Pode expandir o disco de arranque, utilizando o portal do Azure, o PowerShell ou a CLI. Depois do disco de arranque foi expandido, pode adicionar uma partição adicional para os binários de Oracle.


### <a name="storage-configuration"></a>Configuração do armazenamento

Os sistemas de ficheiros de ext4, xfs ou Oracle ASM são suportados para os ficheiros de base de dados Oracle no Azure. Todos os ficheiros de base de dados devem ser armazenados nestes sistemas de ficheiros com base em VHDs ou Managed Disks. Estes discos estão montados à VM do Azure e são baseiam [armazenamento de BLOBs de página do Azure](<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou [Managed Disks do Azure](../../windows/managed-disks-overview.md).

Para kernels do Oracle Linux UEK, um mínimo de UEK versão 4 é necessário para suportar [premium do Azure SSDs](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-caching).

É altamente recomendável usar [discos geridos do Azure](../../windows/managed-disks-overview.md). Também é altamente recomendável usar [premium do Azure SSDs](../../windows/disks-types.md) para as implementações de base de dados Oracle.

Unidades de rede ou partilhas remotas, como os serviços de ficheiros do Azure não são suportadas para os ficheiros de base de dados Oracle. Para obter mais informações, consulte o seguinte: 

- [Introducing Microsoft Azure File Service (Introdução ao Serviço de Ficheiros do Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Persisting connections to Microsoft Azure Files (Ligações persistentes aos Ficheiros do Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

Se estiver a utilizar discos com base no armazenamento de BLOBs de página do Azure ou Managed Disks, as declarações feitas no [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md) aplicam-se a implementações com a base de dados Oracle também.

 Existem quotas no débito IOPS de discos do Azure. Esse conceito é explicado na [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md). As quotas exatas dependem o tipo VM que é utilizado. Para obter uma lista dos tipos VM com as respetivas quotas, consulte [tamanhos de máquinas de virtuais do Linux no Azure][virtual-machines-sizes-linux].

Para identificar os tipos de VM do Azure suportados, consulte a nota SAP [1928533].

Configuração mínima:

| Componente | Disco | Colocação em cache | Remoção * |
| --- | ---| --- | --- |
| /Oracle/\<SID > / origlogaA & mirrlogB | Premium | Nenhuma | Não é preciso |
| /Oracle/\<SID > / origlogaB & mirrlogA | Premium | Nenhuma | Não é preciso |
| /Oracle/\<SID > / sapdata1... n | Premium | Só de leitura | Pode ser utilizado |
| /Oracle/\<SID > / oraarch | Standard | Nenhuma | Não é preciso |
| Página inicial do Oracle, saptrace,... | Disco do SO | | Não é preciso |

* Remoção: O stripe LVM ou MDADM usando RAID0

A seleção de disco para o alojamento de registos de Refazer online da Oracle deve ser conduzida por requisitos de IOPS. É possível armazenar todos os sapdata1... n (tablespaces) num único disco montado, desde que o volume, IOPS e débito satisfazem os requisitos. 

Configuração de desempenho:

| Componente | Disco | Colocação em cache | Remoção * |
| --- | ---| --- | --- |
| /Oracle/\<SID > / origlogaA | Premium | Nenhuma | Pode ser utilizado  |
| /Oracle/\<SID > / origlogaB | Premium | Nenhuma | Pode ser utilizado |
| /Oracle/\<SID > / mirrlogAB | Premium | Nenhuma | Pode ser utilizado |
| /Oracle/\<SID > / mirrlogBA | Premium | Nenhuma | Pode ser utilizado |
| /Oracle/\<SID > / sapdata1... n | Premium | Só de leitura | Recomendado  |
| /oracle/\<SID>/sapdata(n+1)* | Premium | Nenhuma | Pode ser utilizado |
| /oracle/\<SID>/oraarch* | Premium | Nenhuma | Não é preciso |
| Página inicial do Oracle, saptrace,... | Disco do SO | Não é preciso |

* Remoção: O stripe LVM ou MDADM usando RAID0

* (n + 1): tablespaces de sistema, TEMP e DESFAZER de alojamento: O padrão de e/s de sistema e desfazer tablespaces são diferentes dos outros tablespaces que aloja os dados de aplicação. Sem colocação em cache é a melhor opção para o desempenho das tablespaces sistema e desfazer.

* oraarch: agrupamento de armazenamento não é necessário a partir de um ponto de vista do desempenho.


Se mais IOPS forem necessárias, recomendamos que utilize LVM (Gestor de volumes lógicos) ou MDADM para criar um grande volume de lógico ao longo de vários discos montados. Para obter mais informações, consulte [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md) sobre as diretrizes e ponteiros sobre como tirar partido LVM ou MDADM. Esta abordagem simplifica a sobrecarga de administração de gerir o espaço em disco e ajuda a evitar o esforço de distribuição de arquivos manualmente em vários discos montados.


#### <a name="write-accelerator"></a>Acelerador de Escritas
Para VMs de série M do Azure, quando utiliza o acelerador de escrita de Azure, a latência de escrita nos registos de Refazer online pode ser reduzida por fatores em comparação com o desempenho de armazenamento Premium do Azure. Ative o acelerador de escrita do Azure para os discos (VHDs) com base no armazenamento Premium do Azure que são utilizados para ficheiros de registo de Refazer online. Para obter mais informações, consulte [acelerador de escrita](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).


### <a name="backuprestore"></a>Cópia de segurança/restauro
Para a funcionalidade de cópia de segurança/restauro, a SAP BR * ferramentas para Oracle são suportadas da mesma forma como estão em bare-metal e Hyper-V. Gestor de recuperação de Oracle (RMAN) também é suportado para cópias de segurança em disco e restaura a partir de disco.

Para obter mais informações sobre como pode utilizar os serviços de recuperação e cópia de segurança do Azure para criar cópias de segurança e recuperar bases de dados Oracle, consulte [cópia de segurança e recuperar uma base de dados do Oracle Database 12C numa máquina virtual Linux do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-backup-recovery).

### <a name="high-availability"></a>Elevada disponibilidade
Oracle Data Guard é suportada para elevada disponibilidade e fins de recuperação após desastre. Para alcançar a ativação pós-falha automática na proteção de dados, terá de utilizar a ativação pós-falha de início rápido (FSFA). A funcionalidade de observador (FSFA) aciona a ativação pós-falha. Se não usar FSFA, só pode utilizar uma configuração de ativação pós-falha manual. Para obter mais informações, consulte [implementar proteção de dados Oracle numa máquina virtual Linux do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard).


Aspectos de recuperação de desastres para bancos de dados do Oracle no Azure são apresentados no artigo [recuperação após desastre para uma base de dados do Oracle Database 12C no ambiente do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery).

### <a name="accelerated-networking"></a>Redes aceleradas
Suporte do Azure Accelerated Networking no Oracle Linux é fornecido com o Oracle Linux 7 Update 5 (Oracle Linux 7.5). Se não é possível atualizar para a versão mais recente do Oracle Linux 7.5, poderá haver uma solução com o RedHat compatível Kernel (RHCK) em vez do kernel de Oracle UEK. 

Utilizar o kernel do RHEL no Oracle Linux é suportado, de acordo com a nota SAP [#1565179](https://launchpad.support.sap.com/#/notes/1565179). Para redes aceleradas do Azure, a versão de kernel RHCKL mínimo tem de ser 3.10.0-862.13.1.el7. Se estiver a utilizar o kernel UEK no Oracle Linux juntamente com [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/), tem de utilizar a versão de kernel 5 do Oracle UEK.

Se estiver implantando VMs a partir de uma imagem que não se baseia no Azure Marketplace, em seguida, terá de copiar ficheiros de configuração adicionais para a VM ao executar o seguinte código: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


### <a name="other"></a>Outros
[Considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md) descreve outros conceitos importantes relacionados com implementações de VMs com a base de dados Oracle, incluindo conjuntos de disponibilidade do Azure e a monitorização de SAP.
