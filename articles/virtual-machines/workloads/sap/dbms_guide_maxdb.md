---
title: IMPLANTAÇÃO SAP MaxDB, liveCache e Servidor de Conteúdo em VMs Azure Microsoft Docs
description: IMPLEMENTAÇÃO DE SAP MaxDB, liveCache e Servidor de Conteúdo no Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4257fcca1172502560c182651d649745e494cc1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958970"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>IMPLANTAÇÃO DE SAP MaxDB, liveCache e Servidor de Conteúdo em VMs Azure

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
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

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




Este documento abrange várias áreas diferentes a ter em conta ao implementar MaxDB, liveCache e Content Server em Azure IaaS. Como condição prévia para este documento, deveria ter lido o documento [Considerações para a implementação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP,](dbms_guide_general.md) bem como outros guias na [carga de trabalho SAP sobre documentação Azure](./get-started.md). 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Especificidades para as implementações DOE MaxDB no Windows
### <a name="sap-maxdb-version-support-on-azure"></a>Suporte de versão SAP MaxDB em Azure
A SAP suporta atualmente a versão 7.9 ou superior do SAP MaxDB para utilização com produtos à base de SAP NetWeaver em Azure. Todas as atualizações para o servidor SAP MaxDB, ou os controladores JDBC e ODBC a serem utilizados com produtos à base de SAP NetWeaver são fornecidos exclusivamente através do SAP Service Marketplace em <https://support.sap.com/swdc> .
Informações gerais sobre a execução do SAP NetWeaver no SAP MaxDB podem ser encontradas em <https://www.sap.com/community/topic/maxdb.html> .

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Suportadas versões microsoft Windows e tipos de VM Azure para DBMS SAP MaxDB
Para encontrar a versão suportada do Microsoft Windows para DBMS SAP MaxDB no Azure, consulte:

* [Matriz de disponibilidade de produto SAP (PAM)][sap-pam]
* Nota SAP [1928533]

É altamente recomendado utilizar a versão mais recente do sistema operativo Microsoft Windows, que é o Microsoft Windows 2016.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>Documentação MAXDB disponível para MaxDB
Pode encontrar a lista atualizada de documentação SAP MaxDB na seguinte Nota [SAP 767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração SAP MaxDB para instalações SAP em VMs Azure
#### <a name="storage-configuration"></a><a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Configuração do armazenamento
As melhores práticas de armazenamento da Azure para o SAP MaxDB seguem as recomendações gerais mencionadas na estrutura de armazenamento do capítulo [de um VM para implantações RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64).

> [!IMPORTANT]
> À semelhança de outras bases de dados, o SAP MaxDB também tem ficheiros de dados e registos. No entanto, na terminologia SAP MaxDB o termo correto é "volume" (não "ficheiro"). Por exemplo, existem volumes de dados SAP MaxDB e volumes de registo. Não confunda isto com volumes de disco sonoro. 
> 
> 

Em suma, tem que:

* Se utilizar as contas de armazenamento do Azure, detenha a conta de armazenamento Azure que detém os dados e os volumes de registo SAP MaxDB (ficheiros de dados e registos) para **armazenamento redundante local (LRS),** conforme especificado em [Considerações para a implantação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md).
* Separe o caminho IO para volumes de dados SAP MaxDB (ficheiros de dados) da trajetória IO para volumes de registo (ficheiros de registo). Significa que os volumes de dados SAP MaxDB (ficheiros de dados) têm de ser instalados numa unidade lógica e os volumes de registo SAP MaxDB (ficheiros de registo) têm de ser instalados noutra unidade lógica.
* Descreva o tipo de cache adequado para cada disco, dependendo se o utiliza para os dados SAP MaxDB ou volumes de registo (ficheiros de dados e registos), e se utiliza o Azure Standard ou o Azure Premium Storage, conforme descrito em [Considerações para a implementação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md).
* Desde que a quota atual do IOPS por disco satisfaça os requisitos, é possível armazenar todos os volumes de dados num único disco montado e também armazenar todos os volumes de registo de base de dados em outro disco montado único.
* Se forem necessários mais IOPS e/ou espaço, é aconselhável utilizar os Pools de Armazenamento de Janelas do Microsoft (apenas disponíveis no Microsoft Windows Server 2012 e superiores) para criar um grande dispositivo lógico sobre vários discos montados. Para obter mais detalhes, consulte também [considerações para a implementação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md). Esta abordagem simplifica a sobrecarga da administração para gerir o espaço do disco e evita o esforço de distribuir manualmente ficheiros por vários discos montados.
* é altamente recomendado utilizar o Azure Premium Storage para implementações MaxDB. 

![Configuração de referência do Azure IaaS VM para SAP MaxDB DBMS](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="backup-and-restore"></a><a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Backup e Restauro
Ao colocar o SAP MaxDB no Azure, tem de rever a sua metodologia de backup. Mesmo que o sistema não seja um sistema produtivo, a base de dados SAP hospedada pelo SAP MaxDB deve ser apoiada periodicamente. Uma vez que o Azure Storage mantém três imagens, uma cópia de segurança é agora menos importante em termos de proteção do seu sistema contra falhas de armazenamento e falhas operacionais ou administrativas mais importantes. A principal razão para manter um plano de backup e restauro adequado é para que possa compensar erros lógicos ou manuais fornecendo capacidades de recuperação pontuais. Assim, o objetivo é usar backups para restaurar a base de dados a um determinado ponto no tempo ou usar as cópias de segurança em Azure para semear outro sistema copiando a base de dados existente. 

O backup e restauro de uma base de dados em Azure funciona da mesma forma que funciona para os sistemas no local, para que possa utilizar ferramentas de backup/restauro SAP MaxDB padrão, que são descritas num dos documentos de documentação SAP MaxDB listados no SAP Note [767598]. 

#### <a name="performance-considerations-for-backup-and-restore"></a><a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Considerações de desempenho para backup e restauro
Tal como nas implementações de metal nu, o desempenho de backup e restauro depende de quantos volumes podem ser lidos em paralelo e da produção desses volumes. Portanto, pode-se supor:

* Quanto menos o número de discos usados para armazenar os dispositivos de base de dados, menor é a produção geral de leitura
* Quanto menos alvos (Listras, discos) para escrever a cópia de segurança, menor a produção

Para aumentar o número de alvos para escrever, existem duas opções que pode utilizar, possivelmente em combinação, dependendo das suas necessidades:

* Dedicando volumes separados para backup
* Despojando o volume de alvo de backup sobre vários discos montados de modo a melhorar a produção do IOPS no volume de disco listrado
* Ter dispositivos de disco lógicos separados para:
  * Volumes de backup SAP MaxDB (isto é, ficheiros)
  * Volumes de dados DO SAP MaxDB (isto é, ficheiros)
  * Volumes de registo SAP MaxDB (isto é, ficheiros)

A despojamento de um volume sobre vários discos montados foi discutida anteriormente em [Considerações para a implementação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md). 

#### <a name="other-considerations"></a><a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Outras considerações
Todas as outras áreas gerais, tais como conjuntos de disponibilidade de Azure ou monitorização SAP também se aplicam como descrito em [Considerações para a implantação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md).  para a implantação de VMs com a base de dados SAP MaxDB.
Outras definições específicas do SAP MaxDB são transparentes para os VMs Azure e são descritas em diferentes documentos listados na Nota [SAP 767598] e nestas Notas SAP:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Especificidades para implementações sap liveCache no Windows
### <a name="sap-livecache-version-support"></a>Suporte para versão SAP liveCache
Versão mínima do SAP liveCache suportado em Azure Virtual Machines é **SAP LC/LCAPPS 10.0 SP 25,** incluindo **liveCache 7.9.08.31** e **LCA-Build 25**, lançado para **EhP 2 para SAP SCM 7.0** e lançamentos posteriores.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Suportadas versões microsoft Windows e tipos de VM Azure para SAP liveCache DBMS
Para encontrar a versão suportada do Microsoft Windows para o SAP liveCache no Azure, consulte:

* [Matriz de disponibilidade de produto SAP (PAM)][sap-pam]
* Nota SAP [1928533]

É altamente recomendado utilizar a versão mais recente do sistema operativo Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração de sapares para instalações SAP em VMs Azure
#### <a name="recommended-azure-vm-types-for-livecache"></a>Tipos Azure VM recomendados para liveCache
Como o SAP liveCache é uma aplicação que realiza cálculos enormes, a quantidade e rapidez da RAM e CPU tem uma grande influência no desempenho do SAP liveCache. 

Para os tipos Azure VM suportados pelo SAP (SAP Note [1928533),]todos os recursos virtuais da CPU atribuídos ao VM são apoiados por recursos físicos dedicados do CPU do hipervisor. Não se realiza uma sobreprovisão (e, portanto, não existe concorrência para os recursos da CPU).

Da mesma forma, para todos os tipos de instânciaS Azure VM suportados pelo SAP, a memória VM é 100% mapeada para a memória física - o excesso de provisionamento (excesso de compromisso), por exemplo, não é usado.

Nesta perspetiva, é altamente recomendado usar os VMs mais recentes do Dv2, Dv3, Ev3 e M-series. A escolha dos diferentes tipos de VM depende da memória necessária para o liveCache e os recursos da CPU de que necessita. Tal como acontece com todas as outras implementações do DBMS, é aconselhável alavancar o Azure Premium Storage para volumes críticos de desempenho.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Configuração de armazenamento para liveCache em Azure
Como o SAP liveCache é baseado na tecnologia SAP MaxDB, todas as recomendações de boas práticas de armazenamento Azure mencionadas para SAP MaxDB descritas neste documento também são válidas para o SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>Dedicado Azure VM para o cenário liveCache
Como SAP liveCache usa intensivamente o poder computacional, para uso produtivo é altamente recomendado para implementar em uma máquina virtual Azure dedicada. 

![Dedicado Azure VM para liveCache para caso de uso produtivo](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Backup e Restauro para liveCache em Azure
cópia de segurança e restauro, incluindo considerações de desempenho, já estão descritos nos capítulos SAP MaxDB relevantes neste documento. 

#### <a name="other-considerations"></a>Outras considerações
Todas as outras áreas gerais já estão descritas no capítulo SAP MaxDB relevante. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Especificidades para a implementação do Servidor de Conteúdo SAP no Windows in Azure
O SAP Content Server é um componente separado baseado no servidor para armazenar conteúdos como documentos eletrónicos em diferentes formatos. O SAP Content Server é fornecido pelo desenvolvimento de tecnologia e deve ser utilizado através de aplicações cruzadas para quaisquer aplicações SAP. Está instalado num sistema separado. O conteúdo típico é material de formação e documentação do Knowledge Warehouse ou desenhos técnicos originários do mySAP PLM Document Management System. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Suporte à versão do servidor de conteúdo SAP para VMs Azure
A SAP suporta atualmente:

* **Servidor de conteúdo SAP** com a versão **6.50 (e superior)**
* **VERSÃO SAP MaxDB 7.9**
* **Microsoft IIS (Internet Information Server) versão 8.0 (e superior)**

É altamente recomendado utilizar a versão mais recente do SAP Content Server, e a versão mais recente do **Microsoft IIS**. 

Consulte as versões mais recentes suportadas do SAP Content Server e do Microsoft IIS na [Matriz de Disponibilidade de Produto SAP (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Tipos de VM suportados do Microsoft Windows e Azure para o Servidor de Conteúdo SAP
Para obter a versão suportada do Windows para o SERVIDOR de Conteúdo SAP no Azure, consulte:

* [Matriz de disponibilidade de produto SAP (PAM)][sap-pam]
* Nota SAP [1928533]

É altamente recomendado utilizar a versão mais recente do Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do servidor de conteúdo SAP para instalações SAP em VMs Azure
#### <a name="storage-configuration-for-content-server-in-azure"></a>Configuração de armazenamento para servidor de conteúdo em Azure
Se configurar o SAP Content Server para armazenar ficheiros na base de dados SAP MaxDB, todas as recomendações de boas práticas de armazenamento Azure mencionadas para o SAP MaxDB neste documento também são válidas para o cenário do Servidor de Conteúdo SAP. 

Se configurar o SAP Content Server para armazenar ficheiros no sistema de ficheiros, é aconselhável utilizar uma unidade lógica dedicada. A utilização de Espaços de Armazenamento do Windows permite-lhe também aumentar o tamanho do disco lógico e a produção de IOPS, conforme descrito em [Considerações para a implementação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md). 

#### <a name="sap-content-server-location"></a>Localização do servidor de conteúdo SAP
O SERVIDOR de Conteúdo SAP tem de ser implantado na mesma região de Azure e Azure VNET onde o sistema SAP está implantado. É livre de decidir se pretende implantar componentes do SERVIDOR de Conteúdo SAP num VM Azure dedicado ou no mesmo VM onde o sistema SAP está a funcionar. 

![Azure VM dedicado para o Servidor de Conteúdo SAP](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>Localização do servidor de cache SAP
O SERVIDOR CACHE SAP é um componente adicional baseado no servidor para fornecer acesso a documentos (em cache) localmente. O Servidor cache SAP cache cache os documentos de um Servidor de Conteúdo SAP. Isto é para otimizar o tráfego de rede se os documentos tiverem de ser recuperados mais de uma vez de diferentes locais. A regra geral é que o Servidor cache SAP tem de estar fisicamente próximo do cliente que acede ao Servidor cache SAP. 

Aqui tem duas opções:

1. **O cliente é um sistema SAP backend** Se um sistema SAP backend estiver configurado para aceder ao Sap Content Server, esse sistema SAP é um cliente. Uma vez que tanto o sistema SAP como o SAP Content Server são implantados na mesma região do Azure, no mesmo datacenter Azure, estão fisicamente próximos uns dos outros. Portanto, não há necessidade de ter um servidor sap cache dedicado. Os clientes SAP UI (SAP GUI ou web browser) acedem diretamente ao sistema SAP e o sistema SAP recupera documentos do Servidor de Conteúdo SAP.
2. **O cliente é um navegador web no local** O SERVIDOR de Conteúdo SAP pode ser configurado para ser acedido diretamente pelo navegador web. Neste caso, um navegador web em execução no local é um cliente do Servidor de Conteúdo SAP. O datacenter no local e o datacenter Azure são colocados em diferentes locais físicos (idealmente próximos uns dos outros). O seu datacenter no local está ligado ao Azure via Azure Site-to-Site VPN ou ExpressRoute. Embora ambas as opções ofereçam ligação segura da rede VPN ao Azure, a ligação de rede site-to-site não oferece uma largura de banda de rede e um SLA de latência entre o datacenter no local e o datacenter Azure. Para acelerar o acesso aos documentos, pode fazer um dos seguintes:
   1. Instale o SERVIDOR SAP Cache nas instalações, perto do navegador web no local (opção na figura abaixo)
   2. Configure Azure ExpressRoute, que oferece uma ligação de rede dedicada de alta velocidade e baixa latência entre o datacenter no local e o datacenter Azure.

![Opção de instalar o SERVIDOR CACHE SAP no local](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Backup / Restaurar
Se configurar o Servidor de Conteúdo SAP para armazenar ficheiros na base de dados SAP MaxDB, o procedimento de backup/restauro e as considerações de desempenho já estão descritos nos capítulos SAP MaxDB deste documento. 

Se configurar o Servidor de Conteúdo SAP para armazenar ficheiros no sistema de ficheiros, uma opção é executar a cópia de segurança/restauro manual de toda a estrutura de ficheiros onde os documentos estão localizados. Semelhante ao backup/restauro SAP MaxDB, recomenda-se ter um volume de disco dedicado para fins de backup. 

#### <a name="other"></a>Outros
Outras definições específicas do Servidor de Conteúdo SAP são transparentes para VMs Azure e são descritas em vários documentos e notas SAP:

* <https://service.sap.com/contentserver> 
* Nota SAP [1619726]  
