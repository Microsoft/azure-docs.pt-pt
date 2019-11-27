---
title: SQL Server implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP | Microsoft Docs
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
ms.openlocfilehash: 803b1e397efd4a6f9ddaa3bae1d101c8f204e728
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328299"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SQL Server implantação de DBMS de máquinas virtuais do Azure para SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]: https://launchpad.support.sap.com/#/notes/965908
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
[1772688]: https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
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




Este documento aborda várias áreas diferentes a serem consideradas ao implantar SQL Server para carga de trabalho do SAP no Azure IaaS. Como uma pré-condição deste documento, você deve ter lido as [Considerações sobre o documento para a implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md) , bem como outros guias na documentação da carga de trabalho do [SAP no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 



> [!IMPORTANT]
> O escopo deste documento é a versão do Windows no SQL Server. O SAP não oferece suporte à versão Linux do SQL Server com qualquer um dos softwares SAP. O documento não está discutindo Banco de Dados SQL do Microsoft Azure, que é uma oferta de plataforma como serviço da plataforma Microsoft Azure. A discussão neste documento é sobre a execução do produto SQL Server como é conhecido para implantações locais em máquinas virtuais do Azure, aproveitando a infraestrutura como um recurso de serviço do Azure. Os recursos de banco de dados e as funcionalidades entre essas duas ofertas são diferentes e não devem ser misturados entre si. Consulte também: <https://azure.microsoft.com/services/sql-database/>
> 
>

Em geral, você deve considerar o uso das versões de SQL Server mais recentes para executar a carga de trabalho do SAP no IaaS do Azure. As versões mais recentes do SQL Server oferecem uma melhor integração em alguns dos serviços e funcionalidades do Azure. Ou têm alterações que otimizam as operações em uma infraestrutura de IaaS do Azure.

É recomendável examinar [esta][virtual-machines-sql-server-infrastructure-services] documentação antes de continuar.

Nas seções a seguir, partes de partes da documentação sob o link acima são agregadas e mencionadas. As especificidades relacionadas ao SAP também são mencionadas e alguns conceitos são descritos mais detalhadamente. No entanto, é altamente recomendável trabalhar na documentação acima primeiro antes de ler a documentação específica do SQL Server.

Há algumas SQL Server em informações específicas de IaaS que você deve saber antes de continuar:

* **Suporte à versão do SQL**: para clientes SAP, SQL Server 2008 R2 e superior têm suporte em Microsoft Azure máquina virtual. Não há suporte para edições anteriores. Examine esta [declaração de suporte](https://support.microsoft.com/kb/956893) geral para obter mais detalhes. Em geral, SQL Server 2008 também tem suporte da Microsoft. No entanto, devido à funcionalidade significativa do SAP, que foi introduzida com o SQL Server 2008 R2, SQL Server 2008 R2 é a versão mínima do SAP. Em geral, você deve considerar o uso das versões de SQL Server mais recentes para executar a carga de trabalho do SAP no IaaS do Azure. As versões mais recentes do SQL Server oferecem uma melhor integração em alguns dos serviços e funcionalidades do Azure. Ou têm alterações que otimizam as operações em uma infraestrutura de IaaS do Azure. Portanto, o papel é restrito a SQL Server 2016 e SQL Server 2017.
* **Desempenho do SQL**: Microsoft Azure máquinas virtuais hospedadas executam bem em comparação com outras ofertas de virtualização de nuvem pública, mas os resultados individuais podem variar. Confira o artigo [práticas recomendadas de desempenho para SQL Server em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Usando imagens do Azure Marketplace**: a maneira mais rápida de implantar uma nova VM Microsoft Azure é usar uma imagem do Azure Marketplace. Há imagens no Azure Marketplace, que contêm as versões de SQL Server mais recentes. As imagens em que o SQL Server já está instalado não podem ser usadas imediatamente para aplicativos do SAP NetWeaver. O motivo é o padrão SQL Server Agrupamento é instalado nessas imagens e não o agrupamento exigido pelos sistemas SAP NetWeaver. Para usar essas imagens, verifique as etapas documentadas no capítulo [usando uma imagem SQL Server do Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Recomendações sobre a estrutura de VM/VHD para implantações SQL Server relacionadas ao SAP
De acordo com a descrição geral, SQL Server executáveis devem ser localizados ou instalados na unidade do sistema do disco do sistema operacional da VM (unidade C:\).  Normalmente, a maioria dos bancos de dados do sistema SQL Server não são utilizados em um alto nível pela carga de trabalho do SAP NetWeaver. Como resultado, os bancos de dados do sistema de SQL Server (Mestre, msdb e modelo) podem permanecer em C:\ também para a unidade. Uma exceção deve ser tempdb, que, no caso de cargas de trabalho do SAP, pode exigir um volume de dados mais alto ou de operações de e/s. Carga de trabalho de e/s, que não deve ser aplicada ao VHD do sistema operacional. Para esses sistemas, as etapas a seguir devem ser executadas:


* Com todos os tipos de VM certificados pelo SAP (consulte A observação SAP [1928533]), exceto as VMs da série a, os dados do tempdb e os arquivos de log podem ser colocados no D:\ não persistente Dirigir. 
* No entanto, é recomendável usar vários arquivos de dados tempdb. Lembre-se de D:\ os volumes da unidade são diferentes com base no tipo de VM. Para obter os tamanhos exatos do D:\ na unidade das diferentes VMs, verifique os [tamanhos de artigo das máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

Essas configurações permitem que o tempdb consuma mais espaço do que a unidade do sistema é capaz de fornecer. O D:\ não persistente a unidade também oferece melhor latência de e/s e taxa de transferência (com exceção das VMs da série A). Para determinar o tamanho apropriado do tempdb, você pode verificar os tamanhos de tempdb em sistemas existentes. 

>[!NOTE]
> caso você coloque arquivos de dados do tempdb e o arquivo de log em uma pasta no D:\ que você criou, precisará certificar-se de que a pasta existe após uma reinicialização da VM. Desde o D:\ a unidade é inicializada com uma nova inicialização após a reinicialização de todas as estruturas de arquivos e diretórios serem apagadas. Uma possibilidade de recriar estruturas de diretório eventual em D:\ antes do início do serviço de SQL Server está documentado neste [artigo](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Uma configuração de VM, que executa SQL Server com um banco de dados SAP e onde os arquivos tempdb e o arquivo de log tempdb são colocados no D:\ a unidade teria a seguinte aparência:

![Diagrama de configuração de disco de VM simples para SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

O diagrama acima exibe um caso simples. Como eluded no artigo [Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md), número e tamanho dos discos de armazenamento Premium dependem de fatores diferentes. Mas, em geral, recomendamos:

- Usar espaços de armazenamento para formar um ou um pequeno número de volumes, que contêm os arquivos de dados de SQL Server. O motivo por trás dessa configuração é que, na vida real, há vários bancos de dados do SAP com arquivos de banco de dados de tamanho diferente com carga de trabalho de e/s diferente.
- Usar espaços de armazenamento para fornecer IOPS suficientes e para o arquivo de log de transações SQL Server. A carga de trabalho de IOPS potencial geralmente é a linha de GUID para o dimensionamento do volume do log de transações e não o volume potencial do volume de transação SQL Server
- Use o D:\drive para tempdb, desde que o desempenho seja bom o suficiente. Se a carga de trabalho geral for limitada no desempenho por tmepdb estar localizada no D:\ Talvez seja necessário considerar a possibilidade de mover o tempdb para discos de armazenamento Premium separados, conforme recomendado neste [artigo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Especial para VMs da série M
Para a VM da série M do Azure, a latência de gravação no log de transações pode ser reduzida por fatores, em comparação com o desempenho do armazenamento Premium do Azure, ao usar o Azure Acelerador de Gravação. Portanto, você deve implantar os Acelerador de Gravação do Azure para os VHD que formam o volume para o log de transações SQL Server. Os detalhes podem ser lidos no [acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)do documento.
  

### <a name="formatting-the-disks"></a>Formatando os discos
Por SQL Server, o tamanho do bloco NTFS para discos que contêm SQL Server dados e arquivos de log deve ser 64 KB. Não é necessário formatar o D:\ Dirigir. Essa unidade vem previamente formatada.

Para certificar-se de que a restauração ou a criação de bancos de dados não está inicializando os arquivos de dado ao zerar o conteúdo dos arquivos, certifique-se de que o contexto do usuário no qual o serviço SQL Server está sendo executado tenha uma determinada permissão. Normalmente, os usuários no grupo administrador do Windows têm essas permissões. Se o serviço de SQL Server for executado no contexto de usuário de um usuário administrador não Windows, você precisará atribuir a esse usuário o direito de usuário **executar tarefas de manutenção de volume**.  Consulte os detalhes neste artigo da base de dados de conhecimento Microsoft: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Impacto da compactação do banco de dados
Em configurações nas quais a largura de banda de e/s pode se tornar um fator limitante, cada medida, que reduz o IOPS, pode ajudar a ampliar a carga de trabalho que uma pode executar em um cenário de IaaS como o Azure. Portanto, se ainda não tiver feito isso, aplicar SQL Server compactação de página é recomendado pela SAP e pela Microsoft antes de carregar um banco de dados SAP existente no Azure.

A recomendação para executar a compactação de banco de dados antes de carregar para o Azure é dada por dois motivos:

* A quantidade de dados a serem carregados é menor.
* A duração da execução da compactação é mais curta supondo que seja possível usar um hardware mais forte com mais CPUs ou maior largura de banda de e/s ou menos latência de e/s no local.
* Tamanhos de banco de dados menores podem levar a menos custos para alocação de disco

A compactação de banco de dados funciona bem em máquinas virtuais do Azure, como no local. Para obter mais detalhes sobre como compactar os bancos de dados do SAP NetWeaver SQL Server existentes, consulte o artigo [ferramenta de compactação SAP aprimorada MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 e mais recentes – armazenando arquivos de banco de dados diretamente no armazenamento de BLOBs do Azure
SQL Server 2014 e versões posteriores abrem a possibilidade de armazenar arquivos de banco de dados diretamente no repositório de blob do Azure sem o ' wrapper ' de um VHD em relação a eles. Especialmente com o uso do armazenamento do Azure Standard ou de tipos de VM menores, esse tipo de implantação permite cenários em que você pode superar os limites de IOPS que seriam aplicados por um número limitado de discos que podem ser montados em alguns tipos de VM menores. Essa forma de implantação funciona para bancos de dados de usuário, no entanto, não para bancos de dados do sistema de SQL Server. Ele também funciona para arquivos de dados e de log de SQL Server. Se você quiser implantar um banco de dados SAP SQL Server dessa forma, em vez de "encapsulá-lo em VHDs, tenha em mente:

* A conta de armazenamento usada precisa estar na mesma região do Azure que a usada para implantar a VM SQL Server está em execução no.
* As considerações listadas anteriormente sobre a distribuição de VHDs em diferentes contas de armazenamento do Azure também se aplicam a esse método de implantações. Significa que a contagem de operações de e/s em relação aos limites da conta de armazenamento do Azure.
* Em vez de fazer a contabilidade na cota de e/s de armazenamento da VM, o tráfego em relação aos blobs de armazenamento que representam os dados de SQL Server e os arquivos de log será contabilizado na largura de banda da rede da VM do tipo de VM específico. Para largura de banda de rede e armazenamento de um determinado tipo de VM, consulte os [tamanhos de artigo para máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
* Como resultado do envio de e/s de arquivo por Push por meio da cota de rede, você está subutilizando a cota de armazenamento com maior parte e com isso usar a largura de banda geral da VM apenas parcialmente.
* Os destinos de desempenho de taxa de transferência de e/s de IOPS que o armazenamento Premium do Azure tem para os diferentes tamanhos de disco não se aplicam mais. Mesmo que os blobs que você criou estejam localizados no armazenamento Premium do Azure. Os destinos são documentados no artigo [armazenamento Premium de alto desempenho e Managed disks para VMs](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage). Como resultado da colocação de SQL Server arquivos de dados e arquivos de log diretamente em BLOBs armazenados no armazenamento Premium do Azure, as características de desempenho podem ser diferentes em relação aos VHDs no armazenamento Premium do Azure.
* O Caching baseado em host como disponível para discos de armazenamento Premium do Azure não está disponível ao colocar SQL Server arquivos de dados diretamente em BLOBs do Azure.
* Nas VMs da série M, o Azure Acelerador de Gravação não pode ser usado para dar suporte a gravações de submilissegundos no arquivo de log de transações SQL Server. 

Os detalhes dessa funcionalidade podem ser encontrados no artigo [SQL Server arquivos de dados no Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

A recomendação para sistemas de produção é evitar essa configuração e, em vez disso, escolher os posicionamentos de SQL Server dados e arquivos de log nos VHDs de armazenamento Premium do Azure, em vez de diretamente nos BLOBs do Azure.


## <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server extensão do pool de buffers 2014
SQL Server 2014 introduziu um novo recurso, que é chamado de [extensão do pool de buffers](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017). Essa funcionalidade estende o pool de buffers de SQL Server, que é mantido na memória com um cache de segundo nível que é apoiado pelo SSDs local de um servidor ou VM. A extensão do pool de buffers permite manter um conjunto de trabalho maior de dados ' na memória '. Comparado ao acesso ao armazenamento standard do Azure, o acesso à extensão do pool de buffers, que é armazenado no SSDs local de uma VM do Azure, é muitos fatores mais rapidamente. Comparando a extensão do pool de buffers ao cache de leitura do armazenamento Premium do Azure, conforme recomendado para SQL Server arquivos de dados, não é esperada nenhuma vantagem significativa para extensões de pool de buffer O motivo é que ambos os caches (SQL Server a extensão do pool de buffers e o cache de leitura do armazenamento Premium) estão usando os discos locais do nó de computação do Azure.

As experiências obtidas enquanto isso ocorre com SQL Server extensão do pool de buffers com carga de trabalho do SAP é mista e ainda não permite recomendações claras sobre a possibilidade de usá-las em todos os casos. O caso ideal é que o conjunto de trabalho que o aplicativo SAP requer se ajuste à memória principal. Com o Azure enquanto oferece VMs que vêm com até 4 TB de memória, deve ser possível manter o conjunto de trabalho na memória. Portanto, o uso da extensão do pool de buffers é limitado a alguns casos raros e não deve ser um caso básico.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Considerações de backup/recuperação para SQL Server
Ao implantar SQL Server no Azure, sua metodologia de backup deve ser revisada. Mesmo que o sistema não seja um sistema de produção, o banco de dados SAP hospedado pelo SQL Server deve ser submetido a backup periodicamente. Como o armazenamento do Azure mantém três imagens, um backup agora é menos importante em relação à compensação de uma falha de armazenamento. O motivo da prioridade para manter um plano de backup e recuperação adequado é mais que você pode compensar erros lógicos/manuais fornecendo recursos de recuperação pontual. Portanto, o objetivo é usar backups para restaurar o banco de dados de volta a um determinado ponto no tempo ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. 

Para examinar diferentes SQL Server possibilidades de backup no Azure, leia o artigo [backup e restauração de SQL Server em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). O artigo aborda várias possibilidades diferentes.

### <a name="manual-backups"></a>Backups manuais
Você tem várias possibilidades para executar backups "manuais" por:

1. Executar backups de SQL Server convencionais em discos do Azure anexados diretamente. Esse método tem a vantagem de que você tem os backups disponíveis de forma rápida para atualizações do sistema e a criação de novos sistemas como cópias de sistemas SAP existentes
2.  SQL Server 2012 CU4 e superior podem fazer backup de bancos de dados em uma URL de armazenamento do Azure.
3.  Backups de instantâneo de arquivo para arquivos de banco de dados no armazenamento de BLOBs do Azure. Esse método só funciona quando seus SQL Server de dados e arquivos de log estão localizados no armazenamento de BLOBs do Azure

O primeiro método é bem conhecido e aplicado em muitos casos no mundo local também. No entanto, ele deixa a tarefa de resolver o local de backup de longo prazo. Como você não deseja manter seus backups por 30 ou mais dias no armazenamento do Azure anexado localmente, você tem a necessidade de usar os serviços de backup do Azure ou outra ferramenta de backup/recuperação de terceiros que inclua o gerenciamento de acesso e retenção para seus backups. Ou você cria um servidor de arquivos grande no Azure usando espaços de armazenamento do Windows.

O segundo método é descrito mais perto do artigo [SQL Server Backup para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). Diferentes versões do SQL Server têm algumas variações nessa funcionalidade. Portanto, você deve conferir a documentação para sua verificação de versão específica do SQL Server. É importante observar que este artigo lista muitas restrições. Você tem a possibilidade de executar o backup em relação a:

- Um único blob de páginas do Azure, que limita o tamanho do backup a 1000 GB. Isso também limita a taxa de transferência que você pode obter.
- Vários (até 64) blobs de blocos do Azure, que permitem um tamanho de backup teórico de 12 TB. No entanto, os testes com bancos de dados de clientes revelaram que o tamanho máximo do backup pode ser menor do que seu limite teórico. Nesse caso, você é responsável por gerenciar a retenção de backups e acessar também os backups.


### <a name="automated-backup-for-sql-server"></a>Cópia de Segurança Automatizada para SQL Server
O backup automatizado fornece um serviço de backup automático para SQL Server Standard e Enterprise Editions em execução em uma VM do Windows no Azure. Esse serviço é fornecido pelo [SQL Server extensão do agente IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension), que é instalado automaticamente em SQL Server imagens de máquina virtual do Windows no portal do Azure. Se você implantar suas próprias imagens de sistema operacional com SQL Server instalado, será necessário instalar as extensões de VM separadamente. As etapas necessárias estão documentadas neste [artigo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Mais detalhes sobre os recursos desse método podem ser encontrados nestes artigos:

- SQL Server 2014: [backup automatizado para máquinas virtuais SQL Server 2014 (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [backup automatizado v2 para máquinas virtuais do Azure (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Olhando para a documentação, você pode ver que a funcionalidade com as versões mais recentes do SQL Server melhorou. Mais detalhes sobre SQL Server backups automatizados são lançados no artigo [SQL Server Backup gerenciado para Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). O limite de tamanho de backup teórico é 12 TB.  Os backups automatizados podem ser um bom método para tamanhos de backup de até 12 TB. Como vários BLOBs são gravados em paralelo, você pode esperar uma taxa de transferência maior que 100 MB/s. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Backup do Azure para VMs SQL Server
Esse novo método de backups SQL Server é oferecido a partir de junho de 2018 como visualização pública pelos serviços de backup do Azure. O método de backup SQL Server é o mesmo que outras ferramentas de terceiros estão usando, ou seja, a interface SQL Server VSS/VDI para transmitir backups para um local de destino. Nesse caso, o local de destino é o cofre do serviço de recuperação do Azure.

Uma descrição mais detalhada desse método de backup, que adiciona várias vantagens de configurações de backup central, monitoramento e administração está disponível [aqui](https://docs.microsoft.com/azure/backup/backup-azure-sql-database). 


### <a name="third-party-backup-solutions"></a>Soluções de backup de terceiros
Para vários clientes SAP, não havia a possibilidade de começar e introduzir novas soluções completas de backup para a parte da estrutura SAP que estava em execução no Azure. Como resultado, as soluções de backup existentes precisavam ser usadas e estendidas para o Azure. Estender as soluções de backup existentes para o Azure geralmente funcionou bem com a maioria dos principais fornecedores neste espaço. 


## <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Usando uma imagem SQL Server fora do Microsoft Azure Marketplace
A Microsoft oferece VMs no Azure Marketplace, que já contêm versões do SQL Server. Para clientes SAP que precisam de licenças para SQL Server e Windows, usar essas imagens pode ser uma oportunidade para cobrir a necessidade de licenças girando VMs com SQL Server já instalado. Para usar essas imagens para SAP, as seguintes considerações precisam ser feitas:

* O SQL Server versões de não avaliação adquirem custos mais altos do que uma VM ' somente Windows ' implantada do Azure Marketplace. Consulte estes artigos para comparar preços: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> e <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Você só pode usar SQL Server versões, que são suportadas pelo SAP.
* O agrupamento da instância de SQL Server, que é instalado nas VMs oferecidas no Azure Marketplace, não é o agrupamento que o SAP NetWeaver requer que a instância de SQL Server seja executada. No entanto, você pode alterar o agrupamento com as instruções na seção a seguir.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Alterando o agrupamento de SQL Server de uma VM do Microsoft Windows/SQL Server
Como as imagens de SQL Server no Azure Marketplace não estão configuradas para usar o agrupamento, que é exigido pelos aplicativos SAP NetWeaver, ele precisa ser alterado imediatamente após a implantação. Por SQL Server, essa alteração de agrupamento pode ser feita com as etapas a seguir assim que a VM tiver sido implantada e um administrador puder fazer logon na VM implantada:

* Abra uma janela de comando do Windows, como administrador.
* Altere o diretório para C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Execute o comando: Setup. exe/QUIET/ACTION = REBUILDDATABASE/INSTANCENAME = MSSQLSERVER/SQLSYSADMINACCOUNTS =`<local_admin_account_name`>/SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> é a conta, que foi definida como a conta de administrador ao implantar a VM pela primeira vez por meio da galeria.

O processo deve levar apenas alguns minutos. Para certificar-se de que a etapa terminou com o resultado correto, execute as seguintes etapas:

* Abra o SQL Server Management Studio.
* Abra uma janela de consulta.
* Execute o comando sp_helpsort no banco de dados do SQL Server mestre.

O resultado desejado deve ser semelhante a:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Se o resultado for diferente, interrompa a implantação do SAP e investigue por que o comando de instalação não funcionou conforme o esperado. **Não** há suporte para a implantação de aplicativos SAP NetWeaver em SQL Server instância com páginas de código de SQL Server diferentes daquelas mencionadas acima.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server alta disponibilidade para SAP no Azure
Usando SQL Server nas implantações de IaaS do Azure para SAP, você tem várias possibilidades diferentes de adicionar para implantar a camada de DBMS altamente disponível. Como já foi discutido em [Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md) , o Azure fornece diferentes SLAs de tempo de vida para uma única VM e um par de VMs implantadas em um conjunto de disponibilidade do Azure. Suposição que você conduza para o SLA de tempo de indisponibilidade para suas implantações de produção que exigem a implantação em conjuntos de disponibilidade do Azure. Nesse caso, você precisa implantar no mínimo duas VMs nesse conjunto de disponibilidade. Uma VM executará a instância do SQL Server ativa. A outra VM executará a instância passiva

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>Clustering de SQL Server usando o servidor de arquivos de escalabilidade horizontal do Windows
Com o Windows Server 2016, a Microsoft introduziu o [espaços de armazenamento diretos](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview). Com base na implantação Espaços de Armazenamento Diretos, há suporte para o clustering FCI SQL Server. Os detalhes podem ser encontrados no artigo [configurar SQL Server instância de cluster de failover em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster). A solução requer um balanceador de carga do Azure também para lidar com o endereço IP virtual dos recursos de cluster. Os arquivos de banco de dados SQL Server são armazenados em espaços de armazenamento. Portanto, é um dado que você seria solicitado a criar os espaços de armazenamento do Windows com base no armazenamento Premium do Azure. Como essa solução tem suporte para não muito tempo, não há clientes SAP conhecidos que usam essa solução em cenários de produção do SAP.  

### <a name="sql-server-log-shipping"></a>Envio de logs do SQL Server
Um dos métodos de HA (alta disponibilidade) é SQL Server envio de logs. Se as VMs que participam da configuração de HA tiverem a resolução de nomes funcionando, não haverá problema e a configuração no Azure não será diferente de nenhuma configuração feita localmente. Com relação à configuração do envio de logs e aos princípios relacionados ao envio de logs. Detalhes de envio de logs de SQL Server podem ser encontrados no artigo [sobre envio de logs (SQL Server)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017).

A funcionalidade de envio de logs do SQL Server não foi usada no Azure para alcançar alta disponibilidade em uma região do Azure. No entanto, nos cenários a seguir, os clientes SAP estavam usando o envio de logs com êxito em conjunto com o Azure:

- Cenários de recuperação de desastre de uma região do Azure em outra região do Azure
- Configuração de recuperação de desastre do local em uma região do Azure
- Cenários de recorte do local para o Azure. Nesses casos, o envio de logs é usado para sincronizar a nova implantação de DBMS no Azure com o sistema de produção em andamento no local. No momento da reversão, a produção é desligada e garante que os últimos e mais recentes backups de log de transações foram transferidos para a implantação do DBMS do Azure. Em seguida, a implantação do DBMS do Azure é aberta para produção.  



### <a name="database-mirroring"></a>Espelhamento de banco de dados
O espelhamento de banco de dados com suporte do SAP (consulte a observação SAP [965908]) depende da definição de um parceiro de failover na cadeia de conexão SAP. Para os casos entre instalações, presumimos que as duas VMs estejam no mesmo domínio e que o contexto do usuário das duas instâncias de SQL Server estejam em execução sob um usuário de domínio e tenha privilégios suficientes nas duas instâncias de SQL Server envolvidas. Portanto, a configuração do espelhamento de banco de dados no Azure não é diferente entre uma instalação/configuração local típica.

A partir de implantações somente em nuvem, o método mais fácil é ter outra configuração de domínio no Azure para ter as VMs do DBMS (e as VMs do SAP, idealmente dedicadas) em um domínio.

Se um domínio não for possível, também será possível usar certificados para os pontos de extremidade de espelhamento de banco de dados, conforme descrito aqui: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Um tutorial para configurar o espelhamento de banco de dados no Azure pode ser encontrado aqui: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server Always On
Como Always On tem suporte para SAP local (consulte a observação SAP [1772688]), há suporte em combinação com o SAP no Azure. Há algumas considerações especiais sobre a implantação do ouvinte do grupo de disponibilidade SQL Server (não deve ser confundido com o conjunto de disponibilidade do Azure), já que o Azure neste momento não permite a criação de um objeto AD/DNS, pois ele é possível localmente. Portanto, algumas etapas de instalação diferentes são necessárias para superar o comportamento específico do Azure.

Algumas considerações sobre o uso de um ouvinte de grupo de disponibilidade são:

* O uso de um ouvinte de grupo de disponibilidade só é possível com o Windows Server 2012 ou superior como o SO convidado da VM. Para o Windows Server 2012, você precisa certificar-se de que esse patch seja aplicado: <https://support.microsoft.com/kb/2854082> 
* Para o Windows Server 2008 R2, esse patch não existe e Always On precisaria ser usado da mesma maneira que o espelhamento de banco de dados, especificando um parceiro de failover na cadeia de conexão (feito por meio do padrão SAP. PFL Parameter Database/MSS/Server-consulte SAP Note [965908]).
* Ao usar um ouvinte de grupo de disponibilidade, as VMs de banco de dados precisam estar conectadas a um Load Balancer dedicado. Para evitar que o Azure esteja atribuindo novos endereços IP em casos em que ambas as VMs sejam desligadas incidentalmente, uma delas deve atribuir endereços IP estáticos às interfaces de rede dessas VMs na configuração de Always On (definir um endereço IP estático é descrito [neste artigo)][virtual-networks-reserved-private-ip]
* Há etapas especiais necessárias ao criar a configuração do Cluster WSFC em que o cluster precisa de um endereço IP especial atribuído, pois o Azure com sua funcionalidade atual atribuiria o nome do cluster ao mesmo endereço IP que o nó no qual o cluster foi criado. Isso significa que uma etapa manual deve ser executada para atribuir um endereço IP diferente ao cluster.
* O ouvinte do grupo de disponibilidade será criado no Azure com pontos de extremidade TCP/IP, que são atribuídos às VMs que executam as réplicas primária e secundária do grupo de disponibilidade.
* Pode haver a necessidade de proteger esses pontos de extremidade com ACLs.

Documentação detalhada sobre a implantação de Always On com SQL Server em listas de VMs do Azure, como:

- [Apresentando SQL Server grupos de disponibilidade Always on em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
- [Configure um grupo de disponibilidade Always on em máquinas virtuais do Azure em regiões diferentes](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- [Configure um balanceador de carga para um grupo de disponibilidade Always on no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> Se você estiver configurando o balanceador de carga do Azure para o endereço IP virtual do ouvinte do grupo de disponibilidade, verifique se o DirectServerReturn está configurado. a configuração dessa opção reduzirá a latência da viagem de ida e volta da rede entre a camada do aplicativo SAP e a camada do DBMS. 

SQL Server Always On é a funcionalidade de alta disponibilidade e recuperação de desastres mais comum usada no Azure para implantações de carga de trabalho do SAP. A maioria dos clientes usa Always On para alta disponibilidade em uma única região do Azure. Se a implantação for restrita a dois nós apenas, você terá duas opções de conectividade:

- Usando o ouvinte do grupo de disponibilidade. Com o ouvinte do grupo de disponibilidade, é necessário implantar um balanceador de carga do Azure. Normalmente, esse é o método padrão de implantação. Os aplicativos SAP seriam configurados para se conectar ao ouvinte do grupo de disponibilidade e não a um único nó
- Usando os parâmetros de conectividade do espelhamento de banco de dados SQL Server. Nesse caso, você precisa configurar a conectividade dos aplicativos SAP de uma maneira onde ambos os nomes de nó são nomeados. Os detalhes exatos de tal configuração do lado do SAP estão documentados no SAP Note [#965908](https://launchpad.support.sap.com/#/notes/965908). Ao usar essa opção, você não precisará configurar um ouvinte de grupo de disponibilidade. E com isso nenhum balanceador de carga do Azure para a SQL Server alta disponibilidade. Como resultado, a latência de rede entre a camada de aplicativo SAP e a camada DBMS é menor, pois o tráfego de entrada para a instância de SQL Server não é roteado pelo balanceador de carga do Azure. Mas, lembre-se de que essa opção só funcionará se você restringir o grupo de disponibilidade para abranger duas instâncias. 

Alguns clientes estão aproveitando a funcionalidade de Always On SQL Server para a funcionalidade adicional de recuperação de desastre entre regiões do Azure. Vários clientes também usam a capacidade de executar backups de uma réplica secundária. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server Transparent Data Encryption
Há vários clientes que estão usando o SQL Server [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) ao implantar seus bancos de dados do SAP SQL Server no Azure. A funcionalidade de SQL Server TDE é totalmente suportada pela SAP (consulte SAP Note [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Aplicando SQL Server TDE
Nos casos em que você executa uma migração heterogênea de outro DBMS, em execução local, para Windows/SQL Server em execução no Azure, você deve criar seu banco de dados de destino vazio em SQL Server antes do tempo. Como a próxima etapa, você aplicará SQL Server funcionalidade TDE. Enquanto você ainda estiver executando seu sistema de produção local. O motivo que você deseja executar nessa sequência é que o processo de criptografar o banco de dados vazio pode demorar um pouco. Em seguida, os processos de importação do SAP importariam os dados para o banco de dado criptografado durante a fase de inatividade. A sobrecarga de importação em um banco de dados criptografado tem um impacto de tempo menor do que criptografar o banco de dados após a fase de exportação na fase de tempo de inatividade. Experiências negativas quando feitas ao tentar aplicar TDE com carga de trabalho do SAP em execução na parte superior do banco de dados. Portanto, a recomendação é tratar a implantação do TDE como uma atividade que precisa ser feita sem a carga de trabalho do SAP no banco de dados específico.

Nos casos em que você move os bancos de dados do SAP SQL Server do local para o Azure, é recomendável testar em qual infraestrutura você pode obter a criptografia aplicada mais rápido. Para isso, mantenha esses fatos em mente:

- Você não pode definir quantos threads são usados para aplicar a criptografia de dados ao banco de dado. O número de threads depende muito do número de volumes de disco nos quais os dados de SQL Server e os arquivos de log são distribuídos. Significa que os volumes mais distintos (letras da unidade), mais threads serão contratados em paralelo para executar a criptografia. Essa configuração contradiz um pouco com a sugestão de configuração de disco anterior sobre a criação de um ou um número menor de espaços de armazenamento para os arquivos de banco de dados SQL Server nas VMs do Azure. Uma configuração com um pequeno número de volumes levaria a um pequeno número de threads executando a criptografia. Uma única criptografia de thread está lendo extensões de 64 KB, criptografa-a e, em seguida, grava um registro no arquivo de log de transações, informando que a extensão foi criptografada. Como resultado, a carga no log de transações é moderada.
- Em versões de SQL Server mais antigas, a compactação de backup não obteve mais eficiência quando você criptografou seu banco de dados de SQL Server. Esse comportamento pode ser desenvolvido em um problema quando seu plano fosse criptografar o banco de dados do SQL Server local e, em seguida, copiar um backup no Azure para restaurar o banco de dados no Azure. SQL Server compactação de backup geralmente atinge uma taxa de compactação do fator 4.
- Com o SQL Server 2016, SQL Server introduziu uma nova funcionalidade que permite compactar bancos de dados criptografados também de maneira eficiente. Consulte [estes Blogs](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) para obter alguns detalhes.
 
Tratando o aplicativo de criptografia TDE sem apenas uma carga de trabalho SAP, você deve testar a configuração específica se é melhor aplicar TDE ao seu banco de dados SAP local ou fazer isso no Azure. No Azure, você certamente tem mais flexibilidade em termos de provisionamento excessivo da infraestrutura e reduz a infraestrutura depois que o TDE foi aplicado.

### <a name="using-azure-key-vault"></a>Usando Azure Key Vault
O Azure oferece o serviço de uma [Key Vault](https://azure.microsoft.com/services/key-vault/) para armazenar chaves de criptografia. SQL Server no outro lado oferecem um conector para aproveitar Azure Key Vault como armazenamento para os certificados TDE.

Mais detalhes para usar Azure Key Vault para SQL Server listas de TDE como:

- [Gerenciamento extensível de chaves usando Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [SQL Server o gerenciamento extensível de chaves TDE usando as etapas de configuração Azure Key Vault](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [Conector do SQL Server manutenção & solução de problemas](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Mais perguntas de clientes sobre o SQL Server Transparent Data Encryption – TDE + Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Usando SQL Server TDE, especialmente com o Azure Key Vault, é recomendável usar os patches mais recentes do SQL Server 2014, SQL Server 2016 e SQL Server 2017. O motivo é que, com base nos comentários do cliente, as otimizações e as correções foram aplicadas ao código. Por exemplo, verifique [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Resumo geral de SQL Server para SAP no Azure
Há muitas recomendações neste guia e recomendamos que você leia mais de uma vez antes de planejar a implantação do Azure. Em geral, no entanto, certifique-se de seguir o DBMS geral superior em recomendações específicas do Azure:

1. Use a versão mais recente do DBMS, como SQL Server 2017, que tem mais vantagens no Azure. 
2. Planeje cuidadosamente seu cenário do sistema SAP no Azure para balancear o layout do arquivo de dados e as restrições do Azure:
   * Não tenha muitos discos, mas tenha o suficiente para garantir que você possa acessar o IOPS necessário.
   * Se você não usar Managed Disks, lembre-se de que o IOPS também é limitado por conta de armazenamento do Azure e que as contas de armazenamento são limitadas em cada assinatura do Azure ([mais detalhes][azure-subscription-service-limits]). 
   * Somente distribua entre discos se você precisar obter uma taxa de transferência mais alta.
3. Nunca instale software ou coloque arquivos que exijam persistência no D:\ como não permanente, qualquer coisa nessa unidade é perdida na reinicialização do Windows.
4. Não use o cache de disco para o armazenamento standard do Azure.
5. Não use contas de armazenamento padrão do Azure replicadas geograficamente do Azure.  Use localmente redundante para cargas de trabalho do DBMS.
6. Use a solução de HA/DR do seu fornecedor de DBMS para replicar dados do banco de dados.
7. Sempre use a resolução de nomes, não confie em endereços IP.
8. Usando SQL Server TDE, aplique os patches de SQL Server mais recentes.
9. Use a maior compactação de banco de dados possível. Que é a compactação de página para SQL Server.
10. Tenha cuidado ao usar as imagens SQL Server do Azure Marketplace. Se você usar o SQL Server um, deverá alterar o agrupamento da instância antes de instalar qualquer sistema do SAP NetWeaver nele.
11. Instale e configure o monitoramento de host do SAP para o Azure, conforme descrito no [Guia de implantação][deployment-guide].
