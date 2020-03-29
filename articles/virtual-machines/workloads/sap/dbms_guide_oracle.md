---
title: Implantação dbms das máquinas virtuais Oracle Azure para carga de trabalho SAP Microsoft Docs
description: Implementação em Oracle do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP
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
ms.date: 12/14/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a23fb981e24f6152d99b76bd72115f8159f5d60f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645849"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Implantação de DBMS de máquinas virtuais azure para carga de trabalho SAP

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


Este documento abrange várias áreas diferentes a considerar quando está a implementar a Oracle Database para carga de trabalho SAP no Azure IaaS. Antes de ler este documento, recomendamos que leia [considerações para a implantação de DBMS de Máquinas Virtuais Azure para carga de trabalho SAP](dbms_guide_general.md). Recomendamos também que leia outros guias na carga de trabalho do [SAP sobre documentação Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 

Pode encontrar informações sobre as versões Oracle e as versões S correspondentes que são suportadas para executar SAP no Oracle on Azure em SAP Nota [2039619].

Informações gerais sobre a execução da SAP Business Suite na Oracle podem ser encontradas na [SAP na Oracle](https://www.sap.com/community/topic/oracle.html).
O software Oracle é suportado pela Oracle para executar no Microsoft Azure. Para obter mais informações sobre o suporte geral para Windows Hyper-V e Azure, consulte o Oracle e o [Microsoft Azure FAQ](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Notas SAP relevantes para a Oracle, SAP e Azure 

As seguintes Notas SAP estão relacionadas com o SAP no Azure.

| Número de nota | Título |
| --- | --- |
| [1928533] |Aplicações SAP em Azure: Produtos suportados e tipos de VM Azure |
| [2015553] |SAP no Microsoft Azure: Apoiar pré-requisitos |
| [1999351] |Resolução de problemas reforçou a monitorização do Azure para o SAP |
| [2178632] |Principais métricas de monitorização para SAP no Microsoft Azure |
| [2191498] |SAP em Linux com Azure: Monitorização melhorada |
| [2039619] |Aplicações SAP no Microsoft Azure utilizando a base de dados Oracle: Produtos e versões suportados |
| [2243692] |Linux no Microsoft Azure (IaaS) VM: Problemas de licença SAP |
| [2069760] |Instalação e atualização e upgrade oracle Linux 7.x SAP |
| [1597355] |Recomendação de espaço de troca para Linux |
| [2171857] |Oracle Database 12c - suporte do sistema de ficheiros em Linux |
| [1114181] |Oracle Database 11g - suporte do sistema de ficheiros em Linux |

As configurações e funcionalidades exatas que são suportadas pela Oracle e pela SAP no Azure estão documentadas no SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619).

Windows e Oracle Linux são os únicos sistemas operativos que são suportados pela Oracle e SAP no Azure. As distribuições sles e Linux RHEL amplamente utilizadas não são suportadas para a implementação de componentes Oracle em Azure. Os componentes da Oracle incluem o cliente Oracle Database, que é usado por aplicações SAP para se conectar com o DbMS oracle. 

As exceções, de acordo com a Nota [sap #2039619,](https://launchpad.support.sap.com/#/notes/2039619)são componentes SAP que não usam o cliente da Oracle Database. Estes componentes SAP são a fila autónoma do SAP, servidor de mensagens, serviços de replicação Enqueue, WebDispatcher e SAP Gateway.  

Mesmo que esteja a executar as suas instâncias de aplicação Oracle DBMS e SAP no Oracle Linux, pode executar os seus Serviços Centrais SAP em SLES ou RHEL e protegê-lo com um cluster baseado em Pacemaker. Pacemaker como um quadro de alta disponibilidade não é suportado no Oracle Linux.

## <a name="specifics-for-oracle-database-on-windows"></a>Especificidades para Oráculo Base de Dados no Windows

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Diretrizes de configuração do Oracle para instalações SAP em VMs Azure no Windows

De acordo com o manual de instalação do SAP, os ficheiros relacionados com o Oracle não devem ser instalados ou localizados no controlador do sistema para o disco OS de um VM (unidade c:). Máquinas virtuais de tamanhos variados podem suportar um número variado de discos anexados. Os tipos de máquinas virtuais mais pequenos podem suportar um número menor de discos anexados. 

Se tiver VMs mais pequenos, recomendamos instalar/localizar a Oracle em casa, palco, "saptrace", "saparch", "sapbackup", "sapcheck" ou "sapreorg" no disco OS. Estas partes dos componentes DBMS da Oracle não são intensas na entrada de I/O e I/O. Isto significa que o disco DE SM pode lidar com os requisitos de I/S. O tamanho padrão do disco OS é de 127 GB. 

Se não houver espaço livre suficiente disponível, o disco pode ser [redimensionado](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk) para 2048 GB. A Oracle Database e os ficheiros de registo de redoção têm de ser armazenados em discos de dados separados. Há uma exceção para o espaço temporário do Oráculo. Os tempfiles podem ser criados em D:/ (unidade não persistente). O D não persistente:\ O drive também oferece melhor latência e entrada em I/S (com exceção dos VMs da Série A). 

Para determinar a quantidade certa de espaço para os ficheiros temporários, pode verificar os tamanhos dos ficheiros temporários nos sistemas existentes.

### <a name="storage-configuration"></a>Configuração do armazenamento
É suportado apenas o Oráculo de uma instância única utilizando discos formados NTFS. Todos os ficheiros de base de dados devem ser armazenados no sistema de ficheiros NTFS em Discos Geridos (recomendado) ou em VHDs. Estes discos são montados no Azure VM e são baseados no [armazenamento de blob](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) de página Azure ou em Discos [Geridos Azure](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview). 

Recomendamos vivamente a utilização de [discos geridos azure](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview). Também recomendamos vivamente a utilização de [SSDs premium](../../windows/disks-types.md) para as suas implementações na Base de Dados Oracle.

As unidades de rede ou ações remotas, como os serviços de ficheiros Azure, não são suportadas para ficheiros oracle Database. Para obter mais informações, consulte:

- [Introdução ao Serviço do Ficheiro do Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Persisting connections to Microsoft Azure Files (Ligações persistentes aos Ficheiros do Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)


Se estiver a utilizar discos baseados no armazenamento de blob de página Azure ou em Discos Geridos, as declarações em Considerações para a [implantação de DBMS de Máquinas Virtuais Azure para carga de trabalho SAP](dbms_guide_general.md) aplicam-se também a implementações com a Oracle Database.

Existem quotas na produção de IOPS para discos Azure. Este conceito é explicado em Considerações para a [implantação de DBMS de Máquinas Virtuais Azure para carga de trabalho SAP](dbms_guide_general.md). As quotas exatas dependem do tipo VM que usas. Uma lista de tipos vM com as suas quotas pode ser encontrada em [Sizes para máquinas virtuais Windows em Azure][virtual-machines-sizes-windows].

Para identificar os tipos de VM Azure suportados, consulte a Nota SAP [1928533].

A configuração mínima é a seguinte: 

| Componente | Disco | Colocação em cache | Piscina de armazenamento |
| --- | ---| --- | --- |
| \oráculo\<SID>\origlogaA & mirrlogB | Premium | Nenhuma | Não necessário |
| \oráculo\<SID>\origlogaB & mirrlogA | Premium | Nenhuma | Não necessário |
| \oracle\<SID>\sapdata1... n | Premium | Só de leitura | Pode ser usado |
| \oráculo\<SID>\oraarch | Standard | Nenhuma | Não necessário |
| Oracle Home, saptrace, ... | Disco do SO | | Não necessário |


A seleção de discos para hospedar registos de redo on-line deve ser impulsionada pelos requisitos dos IOPs. É possível armazenar todos os sapdata1... n (espaços de mesa) num único disco montado, desde que o tamanho, iOPS e a entrada satisfaçam os requisitos. 

A configuração de desempenho é a seguinte:

| Componente | Disco | Colocação em cache | Piscina de armazenamento |
| --- | ---| --- | --- |
| \oráculo\<SID>\origlogaA | Premium | Nenhuma | Pode ser usado  |
| \oráculo\<SID>\origlogaB | Premium | Nenhuma | Pode ser usado |
| \oráculo\<SID>\mirrlogAB | Premium | Nenhuma | Pode ser usado |
| \oráculo\<SID>\mirrlogBA | Premium | Nenhuma | Pode ser usado |
| \oracle\<SID>\sapdata1... n | Premium | Só de leitura | Recomendado  |
| \oracle\SID\sapdata(n+1)* | Premium | Nenhuma | Pode ser usado |
| \oráculo\<SID>\oraarca* | Premium | Nenhuma | Não necessário |
| Oracle Home, saptrace, ... | Disco do SO | Não necessário |

*(n+1): espaços de mesa SYSTEM, TEMP e UNDO. O padrão De/S de Espaços de Mesa System e Undo são diferentes dos outros espaços de mesa que hospedam dados de aplicações. Sem cache é a melhor opção para o desempenho do Sistema e espaços de mesa Desfazer.

*oraarca: o armazenamento não é necessário do ponto de vista do desempenho. Pode ser usado para conseguir mais espaço.

Se forem necessários mais IOPS, recomendamos a utilização de Pools de Armazenamento do Windows (apenas disponíveis no Windows Server 2012 e mais tarde) para criar um grande dispositivo lógico sobre vários discos montados. Esta abordagem simplifica a administração para gerir o espaço do disco, e ajuda-o a evitar o esforço de distribuição manual de ficheiros através de vários discos montados.


#### <a name="write-accelerator"></a>Acelerador de Escritas
Para os VMs da Série M Azure, a latência escrita nos registos de redo on-line pode ser reduzida por fatores quando comparado com o Armazenamento Premium Azure. Ative o acelerador de escrita Azure para os discos (VHDs) com base no Armazenamento Premium Azure que são utilizados para ficheiros de registo de redo on-line. Para mais informações, consulte [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).


### <a name="backuprestore"></a>Cópia/restauro
Para a funcionalidade de backup/restauro, as Ferramentas SAP BR*para oracle são suportadas da mesma forma que estão nos sistemas operativos padrão do Windows Server. O Oracle Recovery Manager (RMAN) também é suportado para backups no disco e restaura a partir do disco.

Também pode utilizar o Azure Backup para executar uma cópia de segurança VM consistente com aplicações. O artigo [Plan a sua infraestrutura de backup VM em Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) explica como o Azure Backup utiliza a funcionalidade VSS do Windows para executar backups consistentes com aplicações. Os lançamentos oracle DBMS suportados em Azure por SAP podem alavancar a funcionalidade VSS para backups. Para mais informações, consulte a documentação Oracle [Conceitos básicos de backup e recuperação de bases de dados com VSS](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701).


### <a name="high-availability"></a>Elevada disponibilidade
A Oracle Data Guard é apoiada para fins de alta disponibilidade e recuperação de desastres. Para obter falhas automáticas no Data Guard, a sua necessidade de utilizar a Falha de Arranque Rápido (FSFA). O Observador (FSFA) aciona a falha. Se não utilizar o FSFA, só pode utilizar uma configuração de failover manual.

Para obter mais informações sobre a recuperação de desastres para as bases de dados da Oracle em Azure, consulte a recuperação de desastres para uma base de [dados 12c da Oracle Database num ambiente Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery)

### <a name="accelerated-networking"></a>Redes aceleradas
Para as implementações da Oracle no Windows, recomendamos vivamente a ligação acelerada em rede, conforme descrito em [Rede acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Considere também as recomendações que são feitas em considerações para a implantação de [DBMS de Máquinas Virtuais Azure para carga de trabalho SAP](dbms_guide_general.md). 
### <a name="other"></a>Outros
Considerações para a implantação de [Máquinas Virtuais Azure DBMS para carga de trabalho SAP](dbms_guide_general.md) descreve outros conceitos importantes relacionados com implementações de VMs com base de dados Oracle, incluindo conjuntos de disponibilidade Azure e monitorização SAP.

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Especificidades para A base de dados oracle no Oracle Linux
O software Oracle é suportado pela Oracle para executar no Microsoft Azure com a Oracle Linux como o oss o convidado. Para obter mais informações sobre o suporte geral para Windows Hyper-V e Azure, consulte o [Azure e o Oracle FAQ](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

O cenário específico das aplicações SAP que alavancam a Oracle Databases também é suportado. Os detalhes são discutidos na próxima parte do documento.

### <a name="oracle-version-support"></a>Suporte de versão Oracle
Para obter informações sobre quais as versões Oracle e as versões S correspondentes são suportadas para executar SAP no Oracle em Máquinas Virtuais Azure, consulte SAP Note [2039619].

Informações gerais sobre a execução da SAP Business Suite na Oracle podem ser encontradas no [SAP na página comunitária da Oracle.](https://www.sap.com/community/topic/oracle.html)

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Diretrizes de configuração da Oracle para instalações SAP em VMs Azure em Linux

De acordo com os manuais de instalação SAP, os ficheiros relacionados com a Oracle não devem ser instalados ou localizados em controladores de sistema para o disco de arranque de um VM. Tamanhos variados de máquinas virtuais suportam um número variado de discos anexados. Os tipos de máquinas virtuais mais pequenos podem suportar um número menor de discos anexados. 

Neste caso, recomendamos instalar/localizar a Oracle em casa, palco, saptrace, saparch, sapbackup, sapcheck ou sapreorg para arrancar o disco. Estas partes dos componentes DBMS da Oracle não são intensas na entrada de I/O e I/O. Isto significa que o disco DE SM pode lidar com os requisitos de I/S. O tamanho padrão do disco OS é de 30 GB. Pode expandir o disco de arranque utilizando o portal Azure, PowerShell ou CLI. Depois de o disco de arranque ter sido expandido, pode adicionar uma divisória adicional para binários oráculo.


### <a name="storage-configuration"></a>Configuração do armazenamento

Os sistemas de ficheiros de ext4, xfs ou Oracle ASM são suportados para ficheiros oracle Database no Azure. Todos os ficheiros de base de dados devem ser armazenados nestes sistemas de ficheiros com base em VHDs ou Discos Geridos. Estes discos são montados no Azure VM e são baseados no [armazenamento de blob](<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) de página Azure ou em Discos [Geridos Azure](../../windows/managed-disks-overview.md).

Para os kernels UEK Oracle Linux, é necessário um mínimo de versão UEK 4 para suportar [SSDs premium Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-caching).

É altamente recomendado utilizar [discos geridos pelo Azure.](../../windows/managed-disks-overview.md) Também é altamente recomendado usando [SSDs premium Azure](../../windows/disks-types.md) para as suas implementações da Base de Dados Oracle.

As unidades de rede ou ações remotas, como os serviços de ficheiros Azure, não são suportadas para ficheiros oracle Database. Para obter mais informações, consulte o seguinte: 

- [Introdução ao Serviço do Ficheiro do Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Persisting connections to Microsoft Azure Files (Ligações persistentes aos Ficheiros do Microsoft Azure)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

Se estiver a utilizar discos baseados no armazenamento de blob de página Azure ou em Discos Geridos, as declarações feitas em Considerações para a [implantação de DBMS de Máquinas Virtuais Azure para carga de trabalho SAP](dbms_guide_general.md) aplicam-se também a implementações com a Oracle Database.

 Existem quotas na produção de IOPS para discos Azure. Este conceito é explicado em Considerações para a [implantação de DBMS de Máquinas Virtuais Azure para carga de trabalho SAP](dbms_guide_general.md). As quotas exatas dependem do tipo VM que é usado. Para obter uma lista de tipos vm com as suas quotas, consulte [Sizes for Linux virtual machines in Azure][virtual-machines-sizes-linux].

Para identificar os tipos de VM Azure suportados, consulte a Nota SAP [1928533].

Configuração mínima:

| Componente | Disco | Colocação em cache | Despir* |
| --- | ---| --- | --- |
| /oráculo/\<SID>/origlogaA & mirrlogB | Premium | Nenhuma | Não necessário |
| /oráculo/\<SID>/origlogaB & mirrlogA | Premium | Nenhuma | Não necessário |
| /oráculo/\<SID>/sapdata1... n | Premium | Só de leitura | Pode ser usado |
| /oráculo/\<SID>/oraarco | Standard | Nenhuma | Não necessário |
| Oracle Home, saptrace, ... | Disco do SO | | Não necessário |

*Despir: Listra LVM ou MDADM usando RAID0

A seleção do disco para hospedar os registos de redo on-line da Oracle deve ser impulsionada pelos requisitos do IOPS. É possível armazenar todos os sapdata1... n (espaços de mesa) num único disco montado, desde que o volume, o IOPS e a entrada satisfaçam os requisitos. 

Configuração de desempenho:

| Componente | Disco | Colocação em cache | Despir* |
| --- | ---| --- | --- |
| /oráculo/\<SID>/origlogaA | Premium | Nenhuma | Pode ser usado  |
| /oráculo/\<SID>/origlogaB | Premium | Nenhuma | Pode ser usado |
| /oráculo/\<SID>/mirrlogAB | Premium | Nenhuma | Pode ser usado |
| /oráculo/\<SID>/mirrlogBA | Premium | Nenhuma | Pode ser usado |
| /oráculo/\<SID>/sapdata1... n | Premium | Só de leitura | Recomendado  |
| /oráculo/\<SID>/sapdata(n+1)* | Premium | Nenhuma | Pode ser usado |
| /oráculo/\<SID>/oraarca* | Premium | Nenhuma | Não necessário |
| Oracle Home, saptrace, ... | Disco do SO | Não necessário |

*Despir: Listra LVM ou MDADM usando RAID0

*(n+1):SISTEMA de hospedagem, ESPAÇOS DE MESA TEMP e UNDO: O padrão De/O de Espaços de Mesa System e Undo são diferentes dos outros espaços de mesa que hospedam dados de aplicações. Sem cache é a melhor opção para o desempenho do Sistema e espaços de mesa Desfazer.

*oraarca: o armazenamento não é necessário do ponto de vista do desempenho.


Se forem necessários mais IOPS, recomendamos a utilização de LVM (Logical Volume Manager) ou MDADM para criar um grande volume lógico sobre vários discos montados. Para mais informações, consulte considerações para a implantação de [DBMS de Máquinas Virtuais Azure para carga de trabalho SAP](dbms_guide_general.md) sobre orientações e indicações sobre como alavancar o LVM ou o MDADM. Esta abordagem simplifica a administração da gestão do espaço do disco e ajuda-o a evitar o esforço de distribuição manual de ficheiros através de vários discos montados.


#### <a name="write-accelerator"></a>Acelerador de Escritas
Para os VMs da Série M Azure, quando utilizar o Acelerador de Escrita Azure, a latência escrita nos registos de redo on-line pode ser reduzida por fatores quando comparado com o desempenho do Armazenamento Premium Azure. Ative o acelerador de escrita Azure para os discos (VHDs) com base no Armazenamento Premium Azure que são utilizados para ficheiros de registo de redo on-line. Para mais informações, consulte [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).


### <a name="backuprestore"></a>Cópia/restauro
Para a funcionalidade de backup/restauro, as Ferramentas SAP BR*para Oráculo são suportadas da mesma forma que estão em metal nu e Hyper-V. O Oracle Recovery Manager (RMAN) também é suportado para backups no disco e restaura a partir do disco.

Para obter mais informações sobre como pode utilizar os serviços de backup e recuperação do Azure para fazer backup e recuperar bases de dados da Oracle, consulte o Back up e recupere uma base de [dados Oracle Database 12c numa máquina virtual Azure Linux.](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-backup-recovery)

### <a name="high-availability"></a>Elevada disponibilidade
A Oracle Data Guard é apoiada para fins de alta disponibilidade e recuperação de desastres. Para obter falhas automáticas no Data Guard, é necessário utilizar a Falha de Arranque Rápido (FSFA). A funcionalidade Observador (FSFA) aciona a falha. Se não utilizar o FSFA, só pode utilizar uma configuração de failover manual. Para mais informações, consulte [a Implement Oráculo Data Guard numa máquina virtual Azure Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard).


Os aspetos da recuperação de desastres para as bases de dados da Oracle em Azure são apresentados no artigo Recuperação de desastres para uma base de [dados 12c da Oracle Database num ambiente Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery)

### <a name="accelerated-networking"></a>Redes aceleradas
O suporte para a rede acelerada Azure no Oracle Linux é fornecido com oracle Linux 7 Update 5 (Oracle Linux 7.5). Se não conseguir fazer upgrade para o mais recente lançamento do Oracle Linux 7.5, pode haver uma saliência utilizando o Kernel Compatível com OCa (RHCK) em vez do kernel Oráculo UEK. 

A utilização do núcleo RHEL dentro do Oracle Linux é suportada de acordo com o SAP Note [#1565179](https://launchpad.support.sap.com/#/notes/1565179). Para a rede acelerada azure, a libertação mínima de kernel RHCKL tem de ser 3.10.0-862.13.1.el7. Se estiver a utilizar o núcleo UEK em Oracle Linux em conjunto com o [Azure Accelerated Networking,](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)precisa de utilizar a versão 5 do kernel Oracle UEK.

Se estiver a implementar VMs a partir de uma imagem que não é baseada no Azure Marketplace, então precisa de copiar ficheiros de configuração adicionais para o VM executando o seguinte código: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


### <a name="other"></a>Outros
Considerações para a implantação de [Máquinas Virtuais Azure DBMS para carga de trabalho SAP](dbms_guide_general.md) descreve outros conceitos importantes relacionados com implementações de VMs com base de dados Oracle, incluindo conjuntos de disponibilidade Azure e monitorização SAP.
