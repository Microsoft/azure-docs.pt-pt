---
title: Implantação de DBMS de máquinas virtuais do Azure ASE SAP para carga de trabalho do SAP | Microsoft Docs
description: Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP
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
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51e97089b1de88ccf9f45b1a0f429abc0cfac9f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101326"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]: https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]: https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]: https://launchpad.support.sap.com/#/notes/1558958
[1585981]: https://launchpad.support.sap.com/#/notes/1585981
[1588316]: https://launchpad.support.sap.com/#/notes/1588316
[1590719]: https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]: https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]: https://launchpad.support.sap.com/#/notes/1619967
[1750510]: https://launchpad.support.sap.com/#/notes/1750510
[1752266]: https://launchpad.support.sap.com/#/notes/1752266
[1757924]: https://launchpad.support.sap.com/#/notes/1757924
[1757928]: https://launchpad.support.sap.com/#/notes/1757928
[1758182]: https://launchpad.support.sap.com/#/notes/1758182
[1758496]: https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]: https://launchpad.support.sap.com/#/notes/1814258
[1882376]: https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]: https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]: https://launchpad.support.sap.com/#/notes/1941500
[1956005]: https://launchpad.support.sap.com/#/notes/1956005
[1973241]: https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]: https://launchpad.support.sap.com/#/notes/2121797
[2134316]: https://launchpad.support.sap.com/#/notes/2134316
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



Neste documento, cobre várias áreas diferentes a serem consideradas ao implantar o SAP ASE no Azure IaaS. Como uma pré-condição deste documento, você deve ter lido as [Considerações sobre o documento para a implantação do DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md) e outros guias na documentação da carga de trabalho do [SAP no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-to-sap-ase-on-windows"></a>Especificações do SAP ASE no Windows
A partir do Microsoft Azure, você pode migrar seus aplicativos SAP ASE existentes para máquinas virtuais do Azure. O SAP ASE em uma máquina virtual do Azure permite que você reduza o custo total de propriedade de implantação, gerenciamento e manutenção de aplicativos de amplitude empresarial migrando facilmente esses aplicativos para Microsoft Azure. Com o SAP ASE em uma máquina virtual do Azure, os administradores e desenvolvedores ainda podem usar as mesmas ferramentas de desenvolvimento e administração que estão disponíveis localmente.

Os SLAs para máquinas virtuais do Azure podem ser encontrados aqui:<https://azure.microsoft.com/support/legal/sla/virtual-machines>

O Microsoft Azure oferece vários tipos diferentes de máquina virtual que permitem executar os menores sistemas SAP e cenários até grandes sistemas SAP e cenários com milhares de usuários. Os números de SAPS de dimensionamento SAP de diferentes SKUs de VM certificadas pelo SAP são fornecidos na observação SAP [1928533].

Instruções e recomendações relacionadas ao uso do armazenamento do Azure, implantação de VMs SAP ou monitoramento SAP feitos em [considerações para a implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md) também se aplicam a implantações do SAP ASE.

### <a name="sap-ase-version-support"></a>Suporte de versão do SAP ASE
Atualmente, o SAP oferece suporte ao SAP ASE versão 16,0 para uso com produtos SAP Business Suite. Todas as atualizações para o servidor SAP ASE ou drivers JDBC e ODBC a serem usadas com produtos SAP Business Suite são fornecidas exclusivamente por meio do SAP Service Marketplace <https://support.sap.com/swdc>em:.

Não baixe atualizações para o servidor do SAP ASE ou para os drivers JDBC e ODBC diretamente dos sites do Sybase. Para obter informações detalhadas sobre patches, que têm suporte para uso com produtos SAP locais e em máquinas virtuais do Azure, consulte as seguintes notas SAP:

* [1590719]
* [1973241]

Informações gerais sobre como executar o SAP Business Suite no SAP ASE podem ser encontradas no [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Diretrizes de configuração do SAP ASE para instalações SAP ASE relacionadas ao SAP em VMs do Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Estrutura da implantação do SAP ASE
Os executáveis do SAP ASE devem ser localizados ou instalados na unidade do sistema do disco do sistema operacional da VM (\)unidade c:. Normalmente, a maioria dos bancos de dados de ferramentas e sistema do SAP ASE não está apresentando alta carga de trabalho. Portanto, os bancos de dados de ferramentas e sistema (Mestre, modelo, saptools, sybmgmtdb, sybsystemdb) podem permanecer em C:\ Dirigir. 

Uma exceção pode ser o banco de dados temporário, que, no caso de algumas cargas de trabalho do SAP ERP e de todas as de BW, talvez exijam um volume maior de operações de e/s. Volumes ou IOPS que não podem ser fornecidos pelo disco do sistema operacional da VM (unidade\)C:.

Dependendo da versão do SAPInst/SWPM usada para instalar, a configuração da instância do SAP ASE poderia ser semelhante a:

* Um único tempdb do SAP ASE, que é criado durante a instalação do SAP ASE
* Um tempdb do SAP ASE criado pela instalação do SAP ASE e um saptempdb adicional criado pela rotina de instalação do SAP
* Um tempdb do SAP ASE criado pela instalação do SAP ASE e um tempdb adicional que foi criado manualmente (por exemplo, após a observação SAP [1752266]) para atender aos requisitos de tempdb específicos de ERP/BW

Por motivos de desempenho para cargas de trabalho específicas de ERP ou todas as de BW, pode fazer sentido armazenar os dispositivos tempdb do tempdb criado adicionalmente em uma unidade diferente de C:\. Se nenhum tempdb adicional existir, será recomendável criar um (SAP Note [1752266]).

Para esses sistemas, as etapas a seguir devem ser executadas para o tempdb criado adicionalmente:

* Mover o primeiro dispositivo tempdb para o primeiro dispositivo do banco de dados SAP
* Adicionar dispositivos tempdb a cada um dos VHDs contendo um dispositivo do banco de dados SAP

Essa configuração permite que o tempdb consuma mais espaço do que a unidade do sistema pode fornecer. Como referência, é possível verificar os tamanhos de dispositivo tempdb em sistemas existentes, que são executados localmente. Ou essa configuração habilita números de IOPS em relação a tempdb, que não podem ser fornecidos com a unidade do sistema. Os sistemas que estão em execução no local podem ser usados para monitorar a carga de trabalho de e/s em relação ao tempdb.

Nunca coloque nenhum dispositivo do SAP ASE no D:\ unidade da VM. Para o SAP ASE, esse aviso também se aplica ao tempdb, mesmo que os objetos mantidos no tempdb sejam apenas temporários.

Para implantações de arquivos de log de transações e de dados, as instruções e sugestões feitas em [Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md). No caso das implantações baseadas no Windows, o uso de espaços de armazenamento do Windows é recomendado para criar conjuntos de distribuição com IOPS, taxa de transferência e volume suficientes.  

#### <a name="impact-of-database-compression"></a>Impacto da compactação do banco de dados
Em configurações nas quais a largura de banda de e/s pode se tornar um fator limitante, cada medida, que reduz o IOPS, pode ajudar a ampliar a carga de trabalho que uma pode executar em um cenário de IaaS como o Azure. Portanto, é recomendável garantir que a compactação do SAP ASE seja usada antes de carregar um banco de dados SAP existente no Azure.

A recomendação para aplicar a compactação antes de carregar no Azure é dada por vários motivos:

* A quantidade de dados a serem carregados no Azure é menor
* A duração da execução da compactação é mais curta supondo que seja possível usar um hardware mais forte com mais CPUs ou maior largura de banda de e/s ou menos latência de e/s no local
* Tamanhos de banco de dados menores podem levar a menos custos para alocação de disco

A compactação de dados e LOB funciona em uma VM hospedada em máquinas virtuais do Azure, como no local. Para obter mais detalhes sobre como verificar se a compactação já está em uso em um banco de dados do SAP ASE existente, consulte a observação do SAP [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Usando DBACockpit para monitorar instâncias de banco de dados
Para sistemas SAP, que estão usando o SAP ASE como plataforma de banco de dados, o DBACockpit é acessível como janelas de navegador incorporadas na transação DBACockpit ou como Webdynpro. No entanto, a funcionalidade completa para monitorar e administrar o banco de dados está disponível apenas na implementação Webdynpro do DBACockpit.

Assim como acontece com sistemas locais, várias etapas são necessárias para habilitar toda a funcionalidade do SAP NetWeaver usada pela implementação de WebDynpro do DBACockpit. Siga a observação do SAP [1245200] para habilitar o uso de webdynpros e gerar os necessários. Ao seguir as instruções nas observações acima, você também configura o ICM (Internet Communication Manager) junto com as portas a serem usadas para conexões http e HTTPS. A configuração padrão para http é semelhante a:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

e os links gerados na transação DBACockpit se assemelham a:

> https:\//fullyqualifiedhostname>:44300/SAP/BC/Webdynpro\</SAP/dba_cockpit
> 
> http:\//fullyqualifiedhostname>:8000/SAP/BC/Webdynpro\</SAP/dba_cockpit
> 
> 

Dependendo de como a máquina virtual do Azure que hospeda o sistema SAP estiver conectada ao seu AD e DNS, você precisará certificar-se de que o ICM está usando um nome de host totalmente qualificado que pode ser resolvido no computador no qual você está abrindo o DBACockpit. Consulte SAP Note [773830] para entender como o ICM determina o nome de host totalmente qualificado com base nos parâmetros de perfil e defina o parâmetro ICM/host_name_full explicitamente, se necessário.

Se você implantou a VM em um cenário somente de nuvem sem conectividade entre locais entre o local e o Azure, você precisa definir um endereço IP público e um domainlabel. O formato do nome DNS público da VM é semelhante a:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Mais detalhes relacionados ao nome DNS podem ser encontrados [aqui][virtual-machines-azurerm-versus-azuresm].

Definindo o parâmetro de perfil SAP ICM/host_name_full como o nome DNS da VM do Azure, o link pode ser semelhante a:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Nesse caso, você precisa se certificar de:

* Adicionar regras de entrada ao grupo de segurança de rede no portal do Azure para as portas TCP/IP usadas para se comunicar com o ICM
* Adicionar regras de entrada à configuração do firewall do Windows para as portas TCP/IP usadas para se comunicar com o ICM

Para uma importação automatizada de todas as correções disponíveis, é recomendável aplicar periodicamente a anotação SAP da coleção de correção aplicável à sua versão SAP:

* [1558958]
* [1619967]
* [1882376]

Mais informações sobre o DBA cockpit para SAP ASE podem ser encontradas nas seguintes notas SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerações de backup/recuperação para o SAP ASE
Ao implantar o SAP ASE no Azure, sua metodologia de backup deve ser revisada. Mesmo para sistemas de não produção, o backup dos bancos de dados SAP deve ser feito periodicamente. Como o armazenamento do Azure mantém três imagens, um backup pode ser menos importante em relação à compensação de uma falha de armazenamento. O principal motivo para manter um plano de backup e restauração adequado é mais que você pode compensar erros lógicos/manuais fornecendo recursos de recuperação pontual. Portanto, o objetivo é usar backups para restaurar o banco de dados de volta a um determinado ponto no tempo ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. 

O backup e a restauração de um banco de dados no Azure funcionam da mesma maneira que no local. Consulte as observações do SAP:

* [1588316]
* [1585981]

para obter detalhes sobre como criar configurações de despejo e agendar backups. Dependendo da estratégia e das necessidades, você pode configurar o banco de dados e os despejos de log para o disco em um dos discos existentes ou adicionar um disco adicional para o backup. Para reduzir o perigo de perda de dados em caso de erro, é recomendável usar um disco onde não há nenhum arquivo de banco de dados localizado.

Além da compactação de dados e LOB, o SAP ASE também oferece compactação de backup. Para consumir menos espaço com o banco de dados e os despejos de log, é recomendável usar a compactação de backup. Para obter mais informações, consulte SAP Note [1588316]. A compactação do backup também é crucial para reduzir a quantidade de dados a serem transferidos se você planeja baixar backups ou VHDs contendo despejos de backup da máquina virtual do Azure para o local.

Não usar a unidade D:\ como banco de dados ou destino de despejo de log.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para backups/restaurações
Como em implantações bare-metal, o desempenho de backup/restauração depende de quantos volumes podem ser lidos em paralelo e qual pode ser a taxa de transferência desses volumes. Tenha em mente que a compactação de backup consome recursos de CPU. Esse consumo de CPU de compactação de backup pode desempenhar uma função significativa em VMs com um pequeno número de threads de CPU. Portanto, você pode assumir:

* Quanto menor o número de discos usados para armazenar os dispositivos de banco de dados, menor é a taxa de transferência geral na leitura
* Quanto menor o número de threads de CPU na VM, mais grave o impacto da compactação de backup
* Quanto menos destinos (diretórios de distribuição, discos) nos quais gravar o backup, menor será a taxa de transferência

Para aumentar o número de destinos a serem gravados, há duas opções, que podem ser usadas/combinadas dependendo de suas necessidades:

* Distribuição do volume de destino de backup em vários discos montados para melhorar a taxa de transferência de IOPS nesse volume distribuído
* Criando uma configuração de despejo no nível do SAP ASE, que usa mais de um diretório de destino no qual gravar o despejo

A distribuição de um volume de disco em vários discos montados foi discutida em [Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md). Para obter mais informações sobre como usar vários diretórios na configuração de despejo do SAP ASE, consulte a documentação sobre o procedimento armazenado sp_config_dump, que é usado para criar a configuração de despejo no [InfoCenter do Sybase](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperação de desastre com VMs do Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicação de dados com o servidor de replicação do SAP Sybase
Com o servidor de replicação do SAP Sybase (SRS), o SAP ASE fornece uma solução de espera passiva para transferir transações de banco de dados para um local distante de forma assíncrona. 

A instalação e a operação do SRS funcionam também de forma funcional em uma VM hospedada nos serviços de máquina virtual do Azure, como no local.

O HADR do SAP ASE não requer um Load Balancer interno do Azure e não tem dependências no cluster de nível do sistema operacional. Funciona em VMs do Windows e Linux do Azure. Para obter detalhes sobre o SAP ASE HADR, leia o [Guia de usuários do SAP ase HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Especificações do SAP ASE no Linux
A partir do Microsoft Azure, você pode migrar facilmente seus aplicativos SAP ASE existentes para as máquinas virtuais do Azure. O SAP ASE em uma máquina virtual permite reduzir o custo total de propriedade da implantação, do gerenciamento e da manutenção de aplicativos de amplitude empresarial, migrando facilmente esses aplicativos para Microsoft Azure. Com o SAP ASE em uma máquina virtual do Azure, os administradores e desenvolvedores ainda podem usar as mesmas ferramentas de desenvolvimento e administração que estão disponíveis localmente.

Para implantar VMs do Azure, é importante conhecer os SLAs oficiais, que podem ser encontrados aqui:<https://azure.microsoft.com/support/legal/sla>

As informações de dimensionamento SAP e uma lista de SKUs de VM certificadas pelo SAP são fornecidas na observação SAP [1928533]. Documentos adicionais de dimensionamento do SAP para máquinas virtuais do Azure <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> podem ser encontrados aqui e aqui<https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Instruções e recomendações relacionadas ao uso do armazenamento do Azure, implantação de VMs SAP ou monitoramento do SAP se aplicam a implantações do SAP ASE em conjunto com aplicativos SAP, conforme indicado nos quatro primeiros capítulos deste documento.

As duas notas SAP a seguir incluem informações gerais sobre o ASE no Linux e ASE na nuvem:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Suporte de versão do SAP ASE
Atualmente, o SAP oferece suporte ao SAP ASE versão 16,0 para uso com produtos SAP Business Suite. Todas as atualizações para o servidor SAP ASE ou drivers JDBC e ODBC a serem usadas com produtos SAP Business Suite são fornecidas exclusivamente por meio do SAP Service Marketplace <https://support.sap.com/swdc>em:.

Para instalações locais, não baixe atualizações para o servidor do SAP ASE ou para os drivers JDBC e ODBC diretamente dos sites do Sybase. Para obter informações detalhadas sobre patches, que têm suporte para uso com produtos do SAP Business Suite locais e em máquinas virtuais do Azure, consulte as seguintes notas SAP:

* [1590719]
* [1973241]

Informações gerais sobre como executar o SAP Business Suite no SAP ASE podem ser encontradas no [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Diretrizes de configuração do SAP ASE para instalações SAP ASE relacionadas ao SAP em VMs do Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Estrutura da implantação do SAP ASE
Os executáveis do SAP ASE devem ser localizados ou instalados no sistema de arquivos raiz da VM (/Sybase). Normalmente, a maioria dos bancos de dados de ferramentas e sistema do SAP ASE não está apresentando alta carga de trabalho. Portanto, os bancos de dados de ferramentas e sistema (Mestre, modelo, saptools, sybmgmtdb, sybsystemdb) podem ser armazenados no sistema de arquivos raiz. 

Uma exceção pode ser o banco de dados temporário, que, no caso de algumas cargas de trabalho do SAP ERP e de todas as de BW, talvez exijam um volume maior de operações de e/s. Volumes ou IOPS que não podem ser fornecidos pelo disco do sistema operacional da VM 

Dependendo da versão do SAPInst/SWPM usada para instalar o sistema, o banco de dados pode conter:

* Um único tempdb do SAP ASE criado ao instalar o SAP ASE
* Um tempdb do SAP ASE criado pela instalação do SAP ASE e um saptempdb adicional criado pela rotina de instalação do SAP
* Um tempdb do SAP ASE criado pela instalação do SAP ASE e um tempdb adicional que foi criado manualmente (por exemplo, após a observação SAP [1752266]) para atender aos requisitos de tempdb específicos de ERP/BW

Por motivos de desempenho para cargas de trabalho específicas de ERP ou todas as de BW, pode fazer sentido armazenar os dispositivos tempdb do tempdb criado adicionalmente (por SWPM ou manualmente) em um sistema de arquivos separado, que pode ser representado por um único disco de dados do Azure ou um RAID do Linux abrangendo m ultiple discos de dados do Azure. Se nenhum tempdb adicional existir, será recomendável criar um (SAP Note [1752266]).

Para esses sistemas, as etapas a seguir devem ser executadas para o tempdb criado adicionalmente:

* Mover o primeiro diretório tempdb para o primeiro sistema de arquivos do banco de dados SAP
* Adicionar diretórios tempdb a cada um dos discos que contêm um sistema de arquivos do banco de dados SAP

Essa configuração permite que o tempdb consuma mais espaço do que a unidade do sistema pode fornecer. Como referência, é possível verificar os tamanhos de dispositivo tempdb em sistemas existentes, que são executados localmente. Ou essa configuração habilita números de IOPS em relação a tempdb, que não podem ser fornecidos com a unidade do sistema. Os sistemas que estão em execução no local podem ser usados para monitorar a carga de trabalho de e/s em relação ao tempdb.

Nunca coloque quaisquer diretórios do SAP ASE em/mnt ou/mnt/Resource da VM. Para o SAP ASE, esse aviso também se aplica ao tempdb, mesmo que os objetos mantidos no tempdb sejam apenas temporários. Como/mnt ou/mnt/Resource é um espaço temporário de VM do Azure padrão, que não é persistente. Mais detalhes sobre o espaço temporário da VM do Azure podem ser encontrados neste [artigo][virtual-machines-linux-how-to-attach-disk]

Para implantações de arquivos de log de transações e de dados, as instruções e sugestões feitas em [Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md). No caso de implantações baseadas em Linux, é recomendável usar o LVM ou o MDADM para criar conjuntos de distribuição com IOPS, taxa de transferência e volume suficientes. 

#### <a name="impact-of-database-compression"></a>Impacto da compactação do banco de dados
Em configurações nas quais a largura de banda de e/s pode se tornar um fator limitante, cada medida, que reduz o IOPS, pode ajudar a ampliar a carga de trabalho que uma pode executar em um cenário de IaaS como o Azure. Portanto, é recomendável garantir que a compactação do SAP ASE seja usada antes de carregar um banco de dados SAP existente no Azure.

A recomendação para aplicar a compactação antes de carregar no Azure é dada por vários motivos:

* A quantidade de dados a serem carregados no Azure é menor
* A duração da execução da compactação é mais curta supondo que seja possível usar um hardware mais forte com mais CPUs ou maior largura de banda de e/s ou menos latência de e/s no local
* Tamanhos de banco de dados menores podem levar a menos custos para alocação de disco

A compactação de dados e LOB funciona em uma VM hospedada em máquinas virtuais do Azure, como no local. Para obter mais detalhes sobre como verificar se a compactação já está em uso em um banco de dados do SAP ASE existente, consulte a observação do SAP [1750510]. Para obter mais informações sobre a compactação de banco de dados, consulte SAP Note [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Usando DBACockpit para monitorar instâncias de banco de dados
Para sistemas SAP, que estão usando o SAP ASE como plataforma de banco de dados, o DBACockpit é acessível como janelas de navegador incorporadas na transação DBACockpit ou como Webdynpro. No entanto, a funcionalidade completa para monitorar e administrar o banco de dados está disponível apenas na implementação Webdynpro do DBACockpit.

Assim como acontece com sistemas locais, várias etapas são necessárias para habilitar toda a funcionalidade do SAP NetWeaver usada pela implementação de WebDynpro do DBACockpit. Siga a observação do SAP [1245200] para habilitar o uso de webdynpros e gerar os necessários. Ao seguir as instruções nas observações acima, você também configura o ICM (Internet Communication Manager) junto com as portas a serem usadas para conexões http e HTTPS. A configuração padrão para http tem esta aparência:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

e os links gerados na transação DBACockpit serão semelhantes a este:

> https:\//fullyqualifiedhostname>:44300/SAP/BC/Webdynpro\</SAP/dba_cockpit
> 
> http:\//fullyqualifiedhostname>:8000/SAP/BC/Webdynpro\</SAP/dba_cockpit
> 
> 

Dependendo de como a máquina virtual do Azure que hospeda o sistema SAP estiver conectada ao seu AD e DNS, você precisará certificar-se de que o ICM está usando um nome de host totalmente qualificado que pode ser resolvido no computador no qual você está abrindo o DBACockpit. Consulte SAP Note [773830] para entender como o ICM determina o nome de host totalmente qualificado, dependendo dos parâmetros de perfil e defina o parâmetro ICM/host_name_full explicitamente, se necessário.

Se você implantou a VM em um cenário somente de nuvem sem conectividade entre locais entre o local e o Azure, você precisa definir um endereço IP público e um domainlabel. O formato do nome DNS público da VM é semelhante a:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

Mais detalhes relacionados ao nome DNS podem ser encontrados [aqui][virtual-machines-azurerm-versus-azuresm].

Definindo o parâmetro de perfil SAP ICM/host_name_full como o nome DNS da VM do Azure, o link pode ser semelhante a:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Nesse caso, você precisa se certificar de:

* Adicionar regras de entrada ao grupo de segurança de rede no portal do Azure para as portas TCP/IP usadas para se comunicar com o ICM
* Adicionar regras de entrada à configuração do firewall do Windows para as portas TCP/IP usadas para se comunicar com o ICM

Para uma importação automatizada de todas as correções disponíveis, é recomendável aplicar periodicamente a anotação SAP da coleção de correção aplicável à sua versão SAP:

* [1558958]
* [1619967]
* [1882376]

Mais informações sobre o DBA cockpit para SAP ASE podem ser encontradas nas seguintes notas SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerações de backup/recuperação para o SAP ASE
Ao implantar o SAP ASE no Azure, sua metodologia de backup deve ser revisada. Mesmo para sistemas de não produção, o backup dos bancos de dados SAP deve ser feito periodicamente. Como o armazenamento do Azure mantém três imagens, um backup pode ser menos importante em relação à compensação de uma falha de armazenamento. O principal motivo para manter um plano de backup e restauração adequado é mais que você pode compensar erros lógicos/manuais fornecendo recursos de recuperação pontual. Portanto, o objetivo é usar backups para restaurar o banco de dados de volta a um determinado ponto no tempo ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. 

O backup e a restauração de um banco de dados no Azure funcionam da mesma maneira que no local. Consulte as observações do SAP:

* [1588316]
* [1585981]

para obter detalhes sobre como criar configurações de despejo e agendar backups. Dependendo de sua estratégia e necessidades, você pode configurar despejos de log e banco de dados para o disco em um dos discos existentes ou adicionar um disco adicional para o backup. Para reduzir o perigo de perda de dados em caso de erro, é recomendável usar um disco onde nenhum arquivo/diretório de banco de dado está localizado.

Além da compactação de dados e LOB, o SAP ASE também oferece compactação de backup. Para consumir menos espaço com o banco de dados e os despejos de log, é recomendável usar a compactação de backup. Para obter mais informações, consulte SAP Note [1588316]. A compactação do backup também é crucial para reduzir a quantidade de dados a serem transferidos se você planeja baixar backups ou VHDs contendo despejos de backup da máquina virtual do Azure para o local.

Não use o espaço temporário de VM do Azure/mnt ou/mnt/Resource como banco de dados ou destino de despejo de log.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para backups/restaurações
Como em implantações bare-metal, o desempenho de backup/restauração depende de quantos volumes podem ser lidos em paralelo e qual pode ser a taxa de transferência desses volumes. Tenha em mente que a compactação de backup consome recursos de CPU. Esse consumo de CPU de compactação de backup pode desempenhar uma função significativa em VMs com um pequeno número de threads de CPU.  Portanto, você pode assumir:

* Quanto menor o número de discos usados para armazenar os dispositivos de banco de dados, menor é a taxa de transferência geral na leitura
* Quanto menor o número de threads de CPU na VM, mais grave o impacto da compactação de backup
* Quanto menos destinos (RAID de software do Linux, discos) nos quais gravar o backup, menor será a taxa de transferência

Para aumentar o número de destinos a serem gravados, há duas opções, que podem ser usadas/combinadas dependendo de suas necessidades:

* Distribuição do volume de destino de backup em vários discos montados para melhorar a taxa de transferência de IOPS nesse volume distribuído
* Criando uma configuração de despejo no nível do SAP ASE, que usa mais de um diretório de destino no qual gravar o despejo

A distribuição de um volume de disco em vários discos montados foi discutida em [Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](dbms_guide_general.md). Para obter mais informações sobre como usar vários diretórios na configuração de despejo do SAP ASE, consulte a documentação sobre o procedimento armazenado sp_config_dump, que é usado para criar a configuração de despejo no [InfoCenter do Sybase](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperação de desastre com VMs do Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicação de dados com o servidor de replicação do SAP Sybase
Com o servidor de replicação do SAP Sybase (SRS), o SAP ASE fornece uma solução de espera passiva para transferir transações de banco de dados para um local distante de forma assíncrona. 

A instalação e a operação do SRS funcionam também de forma funcional em uma VM hospedada nos serviços de máquina virtual do Azure, como no local.

Há suporte para o ASE HADR por meio do servidor de replicação do SAP. É altamente recomendável usar o SAP ASE 16, 3 para tentar essa configuração. Instruções mais detalhadas para instalar essas configurações podem ser encontradas em detalhes neste [blog](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/).
