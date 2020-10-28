---
title: 'SAP on Azure: Guia de Planeamento e Implementação'
description: Azure Virtual Machines planejamento e implementação para SAP NetWeaver
author: MSSedusch
manager: juergent
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: ea53eda3863ea5164142fa0d37fff7be365a4d5c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92894105"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure Virtual Machines planejamento e implementação para SAP NetWeaver

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
[storage-premium-storage-preview-portal]:../../disks-types.md
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
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines/windows/index.yml
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
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#format-and-mount-the-disk
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



O Microsoft Azure permite que as empresas adquiram recursos de computação e armazenamento em tempo mínimo sem ciclos de aquisição prolongados. O serviço Azure Virtual Machine permite que as empresas implementem aplicações clássicas, como aplicações baseadas em SAP NetWeaver no Azure e aumentem a sua fiabilidade e disponibilidade sem dispor de recursos adicionais disponíveis no local. A Azure Virtual Machine Services também suporta a conectividade transversal, que permite às empresas integrarem ativamente as Máquinas Virtuais Azure nos seus domínios no local, nas suas Nuvens Privadas e na sua Paisagem do Sistema SAP.
Este livro branco descreve os fundamentos da Microsoft Azure Virtual Machine e fornece uma análise de planeamento e implementação para instalações SAP NetWeaver em Azure e, como tal, deve ser o documento a ler antes de iniciar as implementações reais do SAP NetWeaver em Azure.
O trabalho complementa a Documentação de Instalação SAP e notas SAP, que representam os recursos primários para instalações e implantações de software SAP em determinadas plataformas.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Resumo
A Cloud Computing é um termo amplamente utilizado, que está a ganhar cada vez mais importância na indústria informática, desde pequenas empresas até grandes e multinacionais.

O Microsoft Azure é a Plataforma de Serviços cloud da Microsoft, que oferece um vasto leque de novas possibilidades. Agora, os clientes são capazes de provisões e desavisagem rápida como um serviço na nuvem, pelo que não se limitam a restrições técnicas ou orçamentais. Em vez de investir tempo e orçamento em infraestruturas de hardware, as empresas podem focar-se na aplicação, processos de negócio e seus benefícios para clientes e utilizadores.

Com os Serviços da Máquina Virtual do Microsoft Azure, a Microsoft oferece uma plataforma completa de Infraestrutura como um Serviço (IaaS). As aplicações baseadas em SAP NetWeaver são suportadas em Máquinas Virtuais do Azure (IaaS). Este whitepaper descreve como planear e implementar aplicações baseadas em SAP NetWeaver dentro do Microsoft Azure como a plataforma de eleição.

O próprio papel centra-se em dois aspetos principais:

* A primeira parte descreve dois padrões de implementação suportados para aplicações baseadas em SAP NetWeaver em Azure. Também descreve o manuseamento geral do Azure com as implementações sap em mente.
* A segunda parte detalha a implementação dos diferentes cenários descritos na primeira parte.

Para obter recursos adicionais, consulte o capítulo [Recursos][planning-guide-1.2] neste documento.

### <a name="definitions-upfront"></a>Definições antecipadas
Ao longo do documento, utilizamos os seguintes termos:

* IaaS: Infraestrutura como Serviço
* PaaS: Plataforma como Serviço
* SaaS: Software como serviço
* Componente SAP: uma aplicação SAP individual como ECC, BW, Solution Manager ou S/4HANA.  Os componentes SAP podem basear-se em tecnologias tradicionais ABAP ou Java ou numa aplicação não baseada em NetWeaver, como Objetos Empresariais.
* SAP Ambiente: um ou mais componentes SAP agrupados logicamente para desempenhar uma função de negócio como Desenvolvimento, QAS, Formação, DR ou Produção.
* SAP Landscape: Este termo refere-se a todos os ativos SAP na paisagem de TI de um cliente. A paisagem SAP inclui todos os ambientes de produção e não produção.
* Sistema SAP: A combinação de camada de DBMS e camada de aplicação de, por exemplo, um sistema de desenvolvimento ERP SAP, sistema de teste SAP BW, sistema de produção DE CRM SAP, etc. Nas implementações de Azure, não é suportado para dividir estas duas camadas entre as instalações e Azure. Significa que um sistema SAP é implantado no local ou é implantado em Azure. No entanto, pode implantar os diferentes sistemas de uma paisagem SAP em Azure ou no local. Por exemplo, pode implantar os sistemas de desenvolvimento e teste de CRM SAP em Azure, mas o sistema de produção de CRM SAP no local.
* Premissas cruzadas ou híbridas: Descreve um cenário em que os VMs são implantados numa subscrição do Azure que tem conectividade site-to-site, multi-site ou ExpressRoute entre o(s) datacenter(s) no local e a Azure. Em documentação comum do Azure, este tipo de implantações também são descritos como premissas cruzadas ou cenários híbridos. A razão para a ligação é alargar os domínios no local, o Ative Directory/OpenLDAP e o DNS no local para a Azure. A paisagem no local é estendida aos ativos da Azure da subscrição. Com esta extensão, os VMs podem fazer parte do domínio no local. Os utilizadores de domínio do domínio no local podem aceder aos servidores e podem executar serviços nesses VMs (como os serviços DBMS). É possível a resolução de comunicação e nome entre os VM implantados no local e os VM implantados da Azure. Este é o caso mais comum e quase exclusivo que coloca os ativos da SAP no Azure. Para mais informações, consulte [este][vpn-gateway-cross-premises-options] artigo e [este.][vpn-gateway-site-to-site-create]
* Extensão de monitorização azul, monitorização melhorada e extensão de Azure para SAP: Descreva um e o mesmo item. Descreve uma extensão VM que precisa de ser implementada por si para fornecer alguns dados básicos sobre a infraestrutura Azure ao Agente Anfitrião SAP. O SAP nas notas SAP pode referir-se a ela como extensão de monitorização ou monitorização melhorada. Em Azure, estamos a referir-nos a ele como **Extensão Azure para SAP** .

> [!NOTE]
> As instalações cruzadas ou as implantações híbridas de sistemas SAP onde as máquinas virtuais Azure que executam sistemas SAP são membros de um domínio no local são suportados para sistemas SAP de produção. As instalações cruzadas ou híbridas são suportadas para a implantação de peças ou para completar paisagens SAP em Azure. Mesmo executando a paisagem COMPLETA SAP em Azure requer que esses VMs sejam parte do domínio no local e ADS/OpenLDAP.
>
>



### <a name="resources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Recursos
O ponto de entrada para a carga de trabalho sap na documentação do Azure [encontra-se aqui.](./get-started.md) Começando por este ponto de entrada encontra-se muitos artigos que cobrem os tópicos de:

- SAP NetWeaver e Business One em Azure
- Guias SAP DBMS para vários sistemas DBMS em Azure
- Alta disponibilidade e recuperação de desastres para a carga de trabalho da SAP em Azure
- Orientação específica para executar SAP HANA em Azure
- Orientação específica para Azure HANA Grandes Instâncias para o SAP HANA DBMS


> [!IMPORTANT]
> Sempre que possível é utilizado um link para os guias de instalação SAP ou outra documentação SAP (Referência InstGuide-01, ver <http://service.sap.com/instguides> ). No que diz respeito aos pré-requisitos, processo de instalação ou detalhes de uma funcionalidade específica do SAP, a documentação e os guias SAP devem ser sempre lidos atentamente, uma vez que os documentos da Microsoft apenas cobrem tarefas específicas para o software SAP instalado e operado numa Máquina Virtual Microsoft Azure.
>
>

As seguintes Notas SAP estão relacionadas com o tema da SAP sobre Azure:

| Número de nota | Título |
| --- | --- |
| [1928533] |Aplicações SAP em Azure: Produtos suportados e Dimensionamento |
| [2015553] |SAP no Microsoft Azure: Pré-requisitos de suporte |
| [1999351] |Resolução de problemas Monitorização do Azure Melhorado para SAP |
| [2178632] |Principais métricas de monitorização do SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: Monitorização melhorada |
| [2191498] |SAP on Linux com Azure: Monitorização Melhorada |
| [2243692] |Linux no Microsoft Azure (IaaS) VM: Problemas de licença SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalação e atualização |
| [2069760] |Instalação e upgrade do Oracle Linux 7.x SAP |
| [1597355] |Recomendação de espaço de troca para Linux |

Leia também o [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contém todas as notas SAP para Linux.

Neste [artigo][azure-resource-manager/management/azure-subscription-service-limits-subscription]podem encontrar-se limitações gerais por defeito e limitações máximas das assinaturas Azure.

## <a name="possible-scenarios"></a>Possíveis Cenários
A SAP é muitas vezes vista como uma das aplicações mais críticas da missão dentro das empresas. A arquitetura e as operações destas aplicações são na sua maioria complexas e garantir que você cumpre os requisitos de disponibilidade e desempenho é importante.

Assim, as empresas têm de pensar cuidadosamente sobre qual o fornecedor de nuvem a escolher para executar tais processos de negócio críticos de negócio. O Azure é a plataforma de nuvem pública ideal para aplicações SAP críticas empresariais e processos de negócio. Dada a grande variedade de infraestruturas Azure, quase todos os sistemas SAP NetWeaver e S/4HANA existentes podem ser hospedados em Azure hoje. Azure fornece VMs com muitos Terabytes de memória e mais de 200 CPUs. Além disso, a Azure oferece [HANA Large Instances](./hana-overview-architecture.md), que permitem implantações de HANA de até 24 TB e SAP HANA de até 120 TB. Pode-se afirmar hoje que quase todos os cenários sapos no local também podem ser executados em Azure.

Para uma descrição aproximada dos cenários e alguns cenários não suportados, consulte o documento [SAP workload em cenários suportados por máquinas virtuais Azure](./sap-planning-supported-configurations.md).

Verifique estes cenários e algumas das condições que foram nomeadas como não suportadas na documentação referenciada ao longo do planeamento e desenvolvimento da sua arquitetura que pretende implementar no Azure.

No geral, o padrão de implementação mais comum é um cenário transversal como exibido

![VPN com conectividade site-to-site (instalações cruzadas)][planning-guide-figure-300]

A razão para muitos clientes aplicarem um padrão de implementação transversal é o facto de ser mais transparente para todas as aplicações estender em Azure usando a Azure ExpressRoute e tratar o Azure como um centro de dados virtual. À medida que cada vez mais ativos são transferidos para a Azure, as infraestruturas e infraestruturas de rede implantadas do Azure irão crescer e os ativos no local reduzirão em conformidade. Tudo transparente para utilizadores e aplicações.

Para implementar com sucesso os sistemas SAP em Azure IaaS ou IaaS em geral, é importante compreender as diferenças significativas entre as ofertas de outsourcings tradicionais ou hospedeiros e ofertas iaaS. Enquanto o anfitrião ou outsourcing tradicional adapta a infraestrutura (tipo de rede, armazenamento e servidor) à carga de trabalho que um cliente quer acolher, em vez disso é da responsabilidade do cliente ou parceiro caracterizar a carga de trabalho e escolher os componentes Azure corretos de VMs, armazenamento e rede para implementações iaaS.

Para recolher dados para o planeamento da sua implantação em Azure, é importante:

- Avaliar quais os produtos SAP suportados em VMs Azure
- Avaliar que versões específicas do Sistema Operativo são suportadas com VMs Azure específicos para esses produtos SAP
- Avalie quais as versões DBMS suportadas para os seus produtos SAP com VMs Azure específicos
- Avalie se algumas das versões de OS/DBMS necessárias requerem que você execute o lançamento DE SAP, atualização do Pacote de Suporte e atualizações de kernel para chegar a uma configuração suportada
- Avalie se precisa de se deslocar para diferentes sistemas operativos para poder implantar no Azure.

Detalhes sobre componentes SAP suportados no Azure, unidades de infraestrutura suportadas Azure e versões relacionadas do sistema operativo e lançamentos DBMS são explicados no artigo [O software SAP é suportado para implementações Azure](./sap-supported-product-on-azure.md). Os resultados obtidos com a avaliação de lançamentos válidos de SAP, sistema operativo e lançamentos DBMS têm um grande impacto nos esforços de deslocação dos sistemas SAP para Azure. Os resultados desta avaliação vão definir se poderá haver esforços de preparação significativos nos casos em que a SAP liberta atualizações ou alterações dos sistemas operativos.


## <a name="azure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiões de Azure
Os serviços Azure da Microsoft são recolhidos nas regiões de Azure. Uma região de Azure é uma recolha de datacenters que contêm o hardware e infraestrutura que executa e acolhe os diferentes serviços Azure. Esta infraestrutura inclui um grande número de nós que funcionam como nós de computação ou nós de armazenamento, ou executar a funcionalidade de rede.

Para obter uma lista das diferentes regiões do Azure, consulte o artigo [Geografias Azure.](https://azure.microsoft.com/global-infrastructure/geographies/) Nem todas as regiões de Azure oferecem os mesmos serviços. Dependendo do produto SAP que pretende executar, e do sistema operativo e do DBMS relacionado com ele, pode acabar numa situação que uma determinada região não oferece os tipos de VM que necessita. Isto é especialmente verdade para executar SAP HANA, onde você geralmente precisa de VMs da série M/Mv2 VM. Estas famílias VM são implantadas apenas num subconjunto das regiões. Pode descobrir quais os VM exatos, tipos, tipos de armazenamento Azure ou outros Serviços Azure disponíveis em que das regiões com a ajuda do site [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/) À medida que inicia o seu planeamento e tem em mente certas regiões como região primária e eventualmente secundária, é necessário investigar primeiro se os serviços necessários estão disponíveis nessas regiões.

### <a name="availability-zones"></a>Zonas de Disponibilidade
Várias regiões do Azure implementaram um conceito chamado Zonas de Disponibilidade. As Zonas de Disponibilidade são locais fisicamente separados dentro de uma região de Azure. Cada Zona de Disponibilidade é constituída por um ou mais datacenters equipados com energia, refrigeração e redes independentes. Por exemplo, a implantação de dois VMs em duas Zonas de Disponibilidade de Azure, e implementar um quadro de alta disponibilidade para o seu sistema SAP DBMS ou os Serviços Centrais SAP dá-lhe o melhor SLA em Azure. Para esta máquina virtual em particular SLA em Azure, consulte a versão mais recente do [Virtual Machine SLAs](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Uma vez que as regiões do Azure se desenvolveram e se expandiram rapidamente ao longo dos últimos anos, a topologia das regiões do Azure, o número de datacenters físicos, a distância entre esses centros de dados e a distância entre as Zonas de Disponibilidade de Azure podem ser diferentes. E com isso a latência da rede.

O princípio das Zonas de Disponibilidade não se aplica ao serviço específico HANA de [HANA Large Instances](./hana-overview-architecture.md). Os acordos de nível de serviço para HANA Grandes Instâncias podem ser encontrados no artigo [SLA for SAP HANA em Azure Large Instances](https://azure.microsoft.com/support/legal/sla/sap-hana-large/)


### <a name="fault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domínios de avaria
Os Domínios de Avaria representam uma unidade física de falha, intimamente relacionada com a infraestrutura física contida nos centros de dados, e enquanto uma lâmina ou cremalheira física pode ser considerada um domínio de falha, não existe um mapeamento direto entre os dois.

Quando implementa várias Máquinas Virtuais como parte de um sistema SAP nos Serviços de Máquinas Virtuais do Microsoft Azure, pode influenciar o Controlador de TecidoS Azure para implementar a sua aplicação em diferentes Domínios de Falhas, cumprindo assim requisitos mais elevados de disponibilidade de SLAs. No entanto, a distribuição de Domínios de Avaria sobre uma Unidade de Escala Azure (recolha de centenas de nós compute ou nós de armazenamento e rede) ou a atribuição de VMs a um domínio de falha específico é algo sobre o qual não tem controlo direto. Para direcionar o controlador de tecido Azure para implantar um conjunto de VMs em diferentes domínios de avaria, é necessário atribuir um conjunto de disponibilidade de Azure definido para os VMs no momento da implementação. Para obter mais informações sobre os conjuntos de disponibilidade do [Azure,][planning-guide-3.2.3] consulte os conjuntos de disponibilidade do capítulo Azure neste documento.


### <a name="upgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Atualizar domínios
Os Domínios de Atualização representam uma unidade lógica que ajuda a determinar como um VM dentro de um sistema SAP, que consiste em casos DE SAP em execução em vários VMs, é atualizado. Quando ocorre uma atualização, o Microsoft Azure passa pelo processo de atualização destes Domínios de Atualização, um a um. Ao espalhar VMs no tempo de implementação sobre diferentes Domínios de atualização, pode proteger parcialmente o seu sistema SAP de um potencial tempo de inatividade. Para forçar o Azure a implantar os VMs de um sistema SAP espalhados por diferentes Domínios de Atualização, é necessário definir um atributo específico no momento de implantação de cada VM. Semelhante aos Domínios de Avaria, uma Unidade de Escala Azure é dividida em vários domínios de atualização. Para direcionar o controlador de tecido Azure para implantar um conjunto de VMs em diferentes domínios de atualização, é necessário atribuir um Conjunto de Disponibilidade Azure aos VMs no momento da implementação. Para obter mais informações sobre os conjuntos de disponibilidade do [Azure,][planning-guide-3.2.3] consulte os conjuntos de disponibilidade do capítulo Azure abaixo.


### <a name="azure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Conjuntos de disponibilidade de Azure
As máquinas virtuais Azure dentro de um conjunto de disponibilidades Azure são distribuídas pelo Controlador de Tecido Azure em diferentes domínios de falha e upgrade. O objetivo da distribuição sobre diferentes domínios de falha e atualização é evitar que todos os VM de um sistema SAP sejam desligados em caso de manutenção da infraestrutura ou de uma falha dentro de um domínio de avaria. Por predefinição, os VMs não fazem parte de um conjunto de disponibilidade. A participação de um VM num conjunto de disponibilidade é definida no tempo de implantação ou posteriormente através de uma reconfiguração e redistribuição de um VM.

Para compreender o conceito de conjuntos de disponibilidade Azure e a forma como os conjuntos de disponibilidade se relacionam com os domínios de falha e de upgrade, leia [este artigo][virtual-machines-manage-availability].

Ao definir conjuntos de disponibilidade e tentar misturar vários VM de diferentes famílias VM dentro de um conjunto de disponibilidade, poderá encontrar problemas que o impeçam de incluir um determinado tipo VM num conjunto de disponibilidade. A razão é que o conjunto de disponibilidade está ligado à unidade de escala que contém um certo tipo de hospedeiros computacional. E um certo tipo de hospedeiro de computação só pode executar certos tipos de famílias VM. Por exemplo, se criar um conjunto de disponibilidade e colocar o primeiro VM no conjunto de disponibilidade e escolher um tipo VM da família Esv3 e depois tentar implantar como segundo VM um VM da família M, será rejeitado na segunda atribuição. A razão é que os VM da família Esv3 não estão a funcionar no mesmo hardware de anfitrião que as máquinas virtuais da família M. O mesmo problema pode ocorrer, quando se tenta redimensionar os VMs e tentar tirar um VM da família Esv3 para um tipo VM da família M. No caso de redimensionamento para uma família VM que não pode ser hospedada no mesmo hardware do anfitrião, você precisa desligar todos os VMs no seu conjunto de disponibilidade e redimensioná-los para ser capaz de executar no outro tipo de máquina hospedeira. Para SLAs de VMs que são implantados dentro do conjunto de disponibilidade, verifique o artigo [Máquina Virtual SLAs](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

O princípio do conjunto de disponibilidade e do domínio de atualização e avarias relacionados não se aplica ao serviço específico HANA de [HANA Large Instances](./hana-overview-architecture.md). Os acordos de nível de serviço para HANA Grandes Instâncias podem ser encontrados no artigo [SLA for SAP HANA em Azure Large Instances](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

> [!IMPORTANT]
> Os conceitos de Zonas de Disponibilidade Azure e conjuntos de disponibilidade azure são mutuamente exclusivos. Isto significa que pode colocar um par ou vários VMs numa zona de disponibilidade específica ou um conjunto de disponibilidades Azure. Mas não os dois.

### <a name="azure-paired-regions"></a>Regiões emparelhadas azure
O Azure está a oferecer pares da Região Azure onde a replicação de certos dados é ativada entre estes pares de regiões fixas. O emparelhamento da região está documentado no artigo Continuidade do [negócio e recuperação de desastres (BCDR): Regiões Emparelhadas Azure](../../../best-practices-availability-paired-regions.md). Como o artigo descreve, a replicação de dados está ligada a tipos de armazenamento Azure que podem ser configurados por si para replicar na região emparelhada. Ver também o artigo [Redundância de armazenamento numa região secundária.](../../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) Os tipos de armazenamento que permitem tal replicação são tipos de armazenamento, que não são adequados para a carga de trabalho DBMS. Como tal, a usabilidade da replicação do armazenamento Azure seria limitada ao armazenamento de bolhas Azure (como para fins de backup) ou outros cenários de armazenamento de alta latência. Ao verificar as regiões emparelhadas e os serviços que pretende utilizar como região primária ou secundária, poderá encontrar situações em que os serviços Azure e/ou os tipos de VM que pretende utilizar na sua região primária não estejam disponíveis na região emparelhada. Ou poderá encontrar uma situação em que a região emparelhada Azure não seja aceitável por razões de conformidade com os dados. Para esses casos, é necessário utilizar uma região não emparelhada como região de recuperação secundária/de desastres. Neste caso, é necessário ter cuidado com a replicação de parte dos dados que o Azure teria replicado a si mesmo. Um exemplo sobre como replicar o seu Diretório Ativo e DNS para a sua região de recuperação de desastres é descrito no artigo [Configurar a recuperação de desastres para o Ative Directory e DNS](../../../site-recovery/site-recovery-active-directory.md)


## <a name="azure-virtual-machine-services"></a>Serviços de máquinas virtuais Azure
O Azure oferece uma grande variedade de máquinas virtuais que pode selecionar para implementar. Não há necessidade de tecnologia de primeira linha e de compras de infraestruturas. O serviço Azure VM oferece aplicações simplificativas de manutenção e funcionamento, fornecendo computação e armazenamento a pedido para hospedar, escalar e gerir aplicações web e aplicações conectadas. A gestão de infraestruturas é automatizada com uma plataforma projetada para alta disponibilidade e escala dinâmica para corresponder às necessidades de utilização com a opção de vários modelos de preços diferentes.

![Posicionamento dos serviços de máquinas virtuais do Microsoft Azure][planning-guide-figure-400]

Com máquinas virtuais Azure, a Microsoft está a permitir-lhe implementar imagens personalizadas do servidor para o Azure como instâncias iaaS. Ou pode escolher entre uma rica seleção de imagens do sistema operativo consumíveis fora da galeria de imagens Azure.

Do ponto de vista operacional, o Azure Virtual Machine Service oferece experiências semelhantes às máquinas virtuais implantadas no local. É responsável pela administração, operações e também pela remendação do sistema operativo específico, em execução num VM Azure e suas aplicações nesse VM. A Microsoft não está a prestar mais serviços para além de hospedar esse VM na sua infraestrutura Azure (Infraestrutura como Serviço - IaaS). Para a carga de trabalho SAP que você como cliente implementa, a Microsoft não tem ofertas além das ofertas da IaaS.

A plataforma Microsoft Azure é uma plataforma multi-inquilino. Como resultado, o armazenamento, a rede e os recursos de cálculo que acolhem os VMs Azure são, com algumas exceções, partilhados entre inquilinos. A lógica inteligente de estrangulamento e quota é usada para impedir que um inquilino impacte o desempenho de outro inquilino (vizinho barulhento) de forma drástica. Especialmente para certificar a plataforma Azure para SAP HANA, a Microsoft precisa provar o isolamento de recursos para casos em que vários VMs podem funcionar regularmente no mesmo anfitrião para SAP. Embora a lógica em Azure tente manter variações na largura de banda experimentadas pequenas plataformas altamente partilhadas tendem a introduzir variações maiores na disponibilidade de recursos/largura de banda do que os clientes podem experimentar nas suas implementações no local. É necessário ter em conta a probabilidade de um sistema SAP em Azure poder sofrer variações maiores do que num sistema no local.

### <a name="azure-virtual-machines-for-sap-workload"></a>Máquinas virtuais Azure para carga de trabalho SAP

Para a carga de trabalho sap, reduzimos a seleção a diferentes famílias VM que são adequadas para a carga de trabalho SAP e a carga de trabalho SAP HANA mais especificamente. A forma como encontra o tipo VM correto e a sua capacidade de trabalhar através da carga de trabalho SAP é descrita no documento [O software SAP é suportado para implementações Azure](./sap-supported-product-on-azure.md).

> [!NOTE]
> Os tipos de VM certificados para a carga de trabalho SAP, não existem excesso de fornecimento de CPU e recursos de memória.

Para além da seleção de tipos de VM puramente suportados, também é necessário verificar se esses tipos de VM estão disponíveis numa região específica com base no site [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/) Mas, mais importante, é preciso avaliar se:

- CPU e recursos de memória de diferentes tipos de VM
- Largura de banda IOPS de diferentes tipos de VM
- Capacidades de rede de diferentes tipos de VM
- Número de discos que podem ser ligados
- Capacidade de alavancar certos tipos de armazenamento Azure

adequar-se à sua necessidade. A maioria desses dados pode ser encontrada [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows] para um determinado tipo VM.

Como modelo de preços, tem várias opções de preços diferentes que listam como:

- Pay as you go
- Um ano reservado
- Três anos reservados
- Preços pontuais

O preço de cada uma das diferentes ofertas com diferentes ofertas de serviços em torno de sistemas operativos e diferentes regiões está disponível no site [Linux Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para mais detalhes e flexibilidade de um ano e três anos de instâncias reservadas, consulte estes artigos:

- [O que são as reservas do Azure?](../../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Flexibilidade de tamanho da máquina virtual com o Reserved VM Instances](../../reserved-vm-instance-size-flexibility.md)
- [Como o desconto de reserva do Azure é aplicado a máquinas virtuais](../../../cost-management-billing/manage/understand-vm-reservation-charges.md)

Para obter mais informações sobre os preços no local, leia o artigo [Máquinas Virtuais Azure Spot](https://azure.microsoft.com/pricing/spot/). Os preços do mesmo tipo VM também podem ser diferentes entre diferentes regiões do Azure. Para alguns clientes, valeu a pena implantar-se numa região de Azure menos dispendiosa.

Além disso, a Azure oferece os conceitos de um anfitrião dedicado. O conceito de anfitrião dedicado dá-lhe mais controlo sobre ciclos de remendos que são feitos pela Azure. Pode cronometrar o remendado de acordo com os seus próprios horários. Esta oferta destina-se especificamente aos clientes com carga de trabalho que pode não seguir o ciclo normal de carga de trabalho. Para ler os conceitos de ofertas dedicadas a Azure, leia o artigo [Azure Dedicado Anfitrião.](../../dedicated-hosts.md) A utilização desta oferta é suportada pela carga de trabalho SAP e é utilizada por vários clientes SAP que querem ter mais controlo sobre patching de infraestruturas e eventuais planos de manutenção da Microsoft. Para obter mais informações sobre como a Microsoft mantém e corrige a infraestrutura Azure que acolhe máquinas virtuais, leia o artigo [Manutenção para máquinas virtuais em Azure](../../maintenance-and-updates.md).

#### <a name="generation-1-and-generation-2-virtual-machines"></a>Máquinas virtuais geração 1 e Geração 2
O hipervisor da Microsoft é capaz de lidar com duas gerações diferentes de máquinas virtuais. Estes formatos são chamados **Geração 1** e **Geração 2.** **A Geração 2** foi introduzida no ano de 2012 com o hipervisor windows Server 2012. O Azure começou a usar máquinas virtuais da Geração 1. À medida que implementa máquinas virtuais Azure, o padrão ainda é utilizar o formato Geração 1. Entretanto, também podes implementar formatos VM da Geração 2. O artigo [Suporte para a geração 2 VMs em Azure](../../generation-2.md) lista as famílias Azure VM que podem ser implantadas como Geração 2 VM. Este artigo também enumera as importantes diferenças funcionais das máquinas virtuais generation 2, uma vez que podem funcionar em nuvem privada Hiper-V e Azure. Mais importante ainda este artigo também lista diferenças funcionais entre máquinas virtuais geração 1 e VMs geração 2, como as que são executadas em Azure.

> [!NOTE]
> Existem diferenças funcionais de Geração 1 e VMs de Geração 2 em execução em Azure. Leia o artigo  [Suporte para a geração 2 VMs em Azure](../../generation-2.md) para ver uma lista dessas diferenças.

Deslocar um VM existente de uma geração para outra não é possível. Para alterar a geração de máquinas virtuais, é necessário implementar um novo VM da geração que deseja e reinstalar o software que está a executar na máquina virtual da geração. Esta alteração apenas afeta a imagem VHD base do VM e não tem qualquer impacto nos discos de dados ou nas ações NFS ou SMB anexadas. Discos de dados, NFS ou Ações SMB que foram inicialmente atribuídas, por exemplo, a uma Geração 1 VM.

> [!NOTE]
> A implementação de VMs da família Mv1 VM como VMs da Geração 2 é possível a partir do início de maio de 2020. Com isso é possível um aparente menos ascendente e desajuste entre Mv1 e Mv2 família VMs é possível.


### <a name="storage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Armazenamento: Microsoft Azure Storage and Data Disks
As máquinas virtuais do Microsoft Azure utilizam diferentes tipos de armazenamento. Ao implementar o SAP nos Serviços de Máquinas Virtuais Azure, é importante compreender as diferenças entre estes dois principais tipos de armazenamento:

* Armazenamento não persistente e volátil.
* Armazenamento persistente.

Os VMs Azure oferecem discos não persistentes após a implantação de um VM. Em caso de reinicialização de VM, todo o conteúdo dessas unidades será eliminado. Por conseguinte, é dado que os ficheiros de dados e os ficheiros de registo/redo de bases de dados não devem, em circunstância alguma, ser localizados nessas unidades não persistência. Pode haver exceções para algumas das bases de dados, onde estas unidades não persistência poderiam ser adequadas para espaços de mesa temporários e temporários. No entanto, evite utilizar esses discos para VMs da série A, uma vez que essas unidades não persistência são limitadas em produção com aquela família VM. Para mais detalhes, leia o artigo [Compreender a unidade temporária em VMs do Windows em Azure](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Unidade D:\ num Azure VM é uma unidade não persistiu, que é apoiada por alguns discos locais no nó computacional Azure. Por não persistir, isto significa que quaisquer alterações feitas ao conteúdo no D:\ a unidade perde-se quando o VM é reiniciado. Por "quaisquer alterações", como ficheiros armazenados, diretórios criados, aplicações instaladas, etc.
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> Os VMs Linux Azure montam automaticamente uma unidade em /mnt/recurso que é uma unidade não persistido apoiada por discos locais no nó computacional Azure. Por não persistir, isto significa que quaisquer alterações efetuadas ao conteúdo em /mnt/recurso são perdidas quando o VM é reiniciado. Por quaisquer alterações, como ficheiros armazenados, diretórios criados, aplicações instaladas, etc.
>
>

#### <a name="azure-storage-accounts"></a>Contas de Armazenamento do Azure

Ao implementar serviços ou VMs em Azure, a implantação de VHDs e VM Images são organizadas em unidades chamadas Contas de Armazenamento Azure. [As contas de armazenamento Azure](../../../storage/common/storage-account-overview.md) têm limitações, quer no IOPS, quer no rendimento, quer nos tamanhos que podem conter. No passado, estas limitações, que são documentadas em:

- [Metas de escalabilidade para contas de armazenamento padrão](../../../storage/common/scalability-targets-standard-account.md)
- [Metas de escalabilidade para contas de armazenamento de blob de página premium](../../../storage/blobs/scalability-targets-premium-page-blobs.md)

desempenhou um papel importante no planeamento de uma implantação da SAP em Azure. Era sua para gerir o número de discos persistidos dentro de uma conta de armazenamento. Precisava de gerir as contas de armazenamento e eventualmente criar novas contas de armazenamento para criar discos mais persistidos.

Nos últimos anos, a introdução de discos geridos pela [Azure](../../managed-disks-overview.md) aliviou-o dessas tarefas. A recomendação para implementações SAP é alavancar os discos geridos pela Azure em vez de gerir as contas de armazenamento da Azure. Os discos geridos azure distribuirão discos em diferentes contas de armazenamento, de modo que os limites das contas de armazenamento individuais não sejam ultrapassados.

Dentro de uma conta de armazenamento, você tem um tipo de conceito de pasta chamado "recipientes" que pode ser usado para agrupar certos discos em recipientes específicos.

Dentro de Azure, um nome disco/VHD segue a seguinte ligação de nomeação que precisa fornecer um nome único para o VHD dentro de Azure:

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

A cadeia acima precisa de identificar exclusivamente o disco/VHD que é armazenado no Azure Storage.


#### <a name="azure-persisted-storage-types"></a>Tipos de armazenamento Azure persistidos
O Azure oferece uma variedade de opções de armazenamento persistidos que podem ser usadas para carga de trabalho SAP e componentes específicos da pilha SAP. Para mais detalhes, leia o documento  [Azure armazenamento para cargas de trabalho SAP](./planning-guide-storage.md).


### <a name="microsoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure Networking

O Microsoft Azure fornece uma infraestrutura de rede, que permite o mapeamento de todos os cenários, que queremos realizar com o software SAP. As capacidades são:

* Acesso a partir do exterior, diretamente aos VMs através dos Serviços de Terminal Windows ou ssh/VNC
* Acesso a serviços e portas específicas utilizadas por aplicações nos VMs
* Resolução de Comunicação Interna e Nome entre um grupo de VMs implantados como VMs Azure
* Inter-instalações Conectividade entre a rede de um cliente no local e a rede Azure
* Cross Azure Region ou data center conectividade entre sites Azure

Pode encontrar mais informações aqui: <https://azure.microsoft.com/documentation/services/virtual-network/>

Existem muitas possibilidades diferentes de configurar nome e resolução IP em Azure. Existe também um serviço Azure DNS, que pode ser utilizado em vez de configurar o seu próprio servidor DNS. Mais informações podem ser encontradas [neste artigo][virtual-networks-manage-dns-in-vnet] e [nesta página.](https://azure.microsoft.com/services/dns/)

Para cenários transversais ou híbridos, contamos com o facto de que o AD/OpenLDAP/DNS foi alargado através da VPN ou da ligação privada ao Azure. Para certos cenários documentados aqui, pode ser necessário ter uma réplica AD/OpenLDAP instalada no Azure.

Uma vez que a resolução de redes e nomes é uma parte vital da implementação da base de dados para um sistema SAP, este conceito é discutido mais detalhadamente no Guia de Implantação do [DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Redes Virtuais do Azure

Ao criar uma Rede Virtual Azure, pode definir o intervalo de endereços dos endereços IP privados atribuídos pela funcionalidade Azure DHCP. Em cenários de premissas cruzadas, o intervalo de endereços IP definido ainda é atribuído usando DHCP por Azure. No entanto, a resolução do Nome de Domínio é feita no local (assumindo que os VMs fazem parte de um domínio no local) e, portanto, pode resolver endereços para além de diferentes Serviços de Nuvem Azure.

Todas as máquinas virtuais em Azure precisam de ser ligadas a uma Rede Virtual.

Mais detalhes podem ser encontrados [neste artigo][resource-groups-networking] e [nesta página.](https://azure.microsoft.com/documentation/services/virtual-network/)


> [!NOTE]
> Por predefinição, uma vez implementado um VM não é possível alterar a configuração da Rede Virtual. As definições TCP/IP devem ser deixadas ao servidor Azure DHCP. O comportamento predefinido é a atribuição de IP dinâmico.
>
>

O endereço MAC do cartão de rede virtual pode ser alterado, por exemplo, após o redimensionado e o OS convidado do Windows ou Linux pega no novo cartão de rede e utiliza automaticamente o DHCP para atribuir os endereços IP e DNS neste caso.

##### <a name="static-ip-assignment"></a>Atribuição ip estática
É possível atribuir endereços IP fixos ou reservados a VMs dentro de uma Rede Virtual Azure. Executar os VMs numa Rede Virtual Azure abre uma grande possibilidade de aproveitar esta funcionalidade se necessário ou necessário para alguns cenários. A atribuição de IP permanece válida durante toda a existência do VM, independentemente de o VM estar em execução ou paragem. Como resultado, é necessário ter em conta o número total de VMs (vMs em execução e parados) ao definir a gama de endereços IP para a Rede Virtual. O endereço IP permanece atribuído até que o VM e a sua Interface de Rede seja eliminado ou até que o endereço IP seja novamente destribuido. Para mais informações, leia [este artigo.][virtual-networks-static-private-ip-arm-pportal]

> [!NOTE]
> Deve atribuir endereços IP estáticos através de meios Azure a vNICs individuais. Não deve atribuir endereços IP estáticos dentro do so do hóspede a um vNIC. Alguns serviços da Azure, como o Azure Backup Service, baseiam-se no facto de que pelo menos o vNIC primário está definido para DHCP e não para endereços IP estáticos. Consulte também o documento [Troubleshoot Azure virtual machine backup](../../../backup/backup-azure-vms-troubleshoot.md#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Múltiplos NICs por VM

Pode definir vários cartões de interface de rede virtual (vNIC) para uma Máquina Virtual Azure. Com a capacidade de ter vários vNICs pode começar a configurar a separação do tráfego de rede onde, por exemplo, o tráfego de clientes é encaminhado através de um vNIC e o tráfego de backend é encaminhado através de um segundo vNIC. Dependendo do tipo de VM existem diferentes limitações para o número de vNICs que um VM pode ter atribuído. Detalhes exatos, funcionalidade e restrições podem ser encontrados nestes artigos:

* [Criar um VM Windows com vários NICs][virtual-networks-multiple-nics-windows]
* [Criar um Linux VM com vários NICs][virtual-networks-multiple-nics-linux]
* [Implementar VMs multi NIC usando um modelo][virtual-network-deploy-multinic-arm-template]
* [Implementar VMs multi NIC usando PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Implementar VMs multi NIC utilizando o CLI Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Conectividade local-a-local

As instalações cruzadas são VMs Azure e Instalações ligadas a uma ligação VPN transparente e permanente. Espera-se que se torne o padrão de implantação mais comum da SAP em Azure. O pressuposto é que os procedimentos operacionais e os processos com casos SAP em Azure devem funcionar de forma transparente. Isto significa que deve ser capaz de imprimir fora destes sistemas, bem como usar o Sistema de Gestão de Transportes SAP (TMS) para transportar alterações de um sistema de desenvolvimento em Azure para um sistema de teste, que é implantado no local. Mais documentação em torno do site-a-site pode ser encontrada [neste artigo][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Dispositivo de túnel VPN

Para criar uma ligação site-to-site (centro de dados no local para o centro de dados Azure), é necessário obter e configurar um dispositivo VPN, ou utilizar o Serviço de Encaminhamento e Acesso Remoto (RRAS) que foi introduzido como um componente de software com o Windows Server 2012.

* [Criar uma rede virtual com uma ligação VPN site-to-site usando PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Acerca dos dispositivos de VPN para ligações de Gateway de Rede de VPNs][vpn-gateway-about-vpn-devices]
* [FAQ do VPN Gateway][vpn-gateway-vpn-faq]

![Ligação local-local entre as instalações e a Azure][planning-guide-figure-600]

A figura acima mostra que duas subscrições Azure têm subgrupos de endereço IP reservados para utilização em Redes Virtuais em Azure. A conectividade da rede no local para a Azure é estabelecida através da VPN.

#### <a name="point-to-site-vpn"></a>VPN ponto-a-local

A VPN ponto-a-local requer que todas as máquinas de clientes se conectem com a sua própria VPN em Azure. Para os cenários SAP, estamos a olhar para, a conectividade ponto-a-local não é prática. Por conseguinte, não são dadas mais referências à conectividade VPN ponto-a-local.

Pode encontrar mais informações aqui
* [Configure uma ligação ponto-a-local a um VNet utilizando o portal Azure](../../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Configurar uma ligação de Ponto a Site a uma VNet com o PowerShell](../../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

#### <a name="multi-site-vpn"></a>Multi-Site VPN

A Azure também oferece hoje a possibilidade de criar conectividade VPN multi-site para uma subscrição do Azure. Anteriormente, uma única subscrição estava limitada a uma ligação VPN site-to-site. Esta limitação foi-se com ligações VPN multi-site para uma única subscrição. Isto permite alavancar mais do que uma Região Azure para uma subscrição específica através de configurações de premissas cruzadas.

Para mais documentação, consulte [este artigo][vpn-gateway-create-site-to-site-rm-powershell]

#### <a name="vnet-to-vnet-connection"></a>Ligação VNet à VNet

Utilizando a VPN Multi-Site, é necessário configurar uma Rede Virtual Azure separada em cada uma das regiões. No entanto, muitas vezes tem a exigência de que os componentes do software nas diferentes regiões devem comunicar uns com os outros. Idealmente, esta comunicação não deve ser encaminhada de uma região de Azure para o local e daí para a outra Região de Azure. Para atalho, o Azure oferece a possibilidade de configurar uma ligação de uma Rede Virtual Azure numa região para outra Rede Virtual Azure hospedada noutra região. Esta funcionalidade chama-se ligação VNet-vNet. Mais detalhes sobre esta funcionalidade podem ser encontrados aqui: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/> .

#### <a name="private-connection-to-azure-expressroute"></a>Ligação Privada à Azure ExpressRoute

O Microsoft Azure ExpressRoute permite a criação de ligações privadas entre centros de dados Azure e a infraestrutura de acesso ao cliente no local ou num ambiente de co-localização. O ExpressRoute é oferecido por vários fornecedores de VPN (comutados por pacotes) ou outros Fornecedores de Serviços de Rede. As ligações do ExpressRoute não passam para a Internet pública. As ligações ExpressRoute oferecem maior segurança, mais fiabilidade através de múltiplos circuitos paralelos, velocidades mais rápidas e latências mais baixas do que as ligações típicas através da Internet.

Saiba mais detalhes sobre a Azure ExpressRoute e as ofertas aqui:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

A Rota Express permite várias subscrições do Azure através de um circuito ExpressRoute, conforme documentado aqui

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Túneis forçados em caso de instalações cruzadas
Para os VMs que se juntam aos domínios no local através do site-para-site, ponto a site ou ExpressRoute, é necessário certificar-se de que as definições de procuração da Internet estão a ser implementadas para todos os utilizadores nesses VMs também. Por padrão, o software que executa nesses VMs ou utilizadores que usam um browser para aceder à internet não passaria pelo representante da empresa, mas ligaria diretamente através do Azure à internet. Mas mesmo a configuração proxy não é uma solução 100% para direcionar o tráfego através do representante da empresa, uma vez que é da responsabilidade do software e serviços verificar se o representante. Se o software que está a funcionar no VM não estiver a fazer isso ou se um administrador manipular as definições, o tráfego para a Internet pode ser desviado novamente diretamente através do Azure para a Internet.

Para evitar tal conectividade direta na Internet, pode configurar o Túnel Forçado com conectividade local-local entre as instalações e o Azure. A descrição detalhada da funcionalidade de Túnel Forçado é publicada aqui <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

O Túnel Forçado com o ExpressRoute é ativado por clientes que publicam uma rota padrão através das sessões de observação do ExpressRoute BGP.

#### <a name="summary-of-azure-networking"></a>Resumo da rede Azure

Este capítulo continha muitos pontos importantes sobre o Azure Networking. Aqui está um resumo dos principais pontos:

* As Redes Virtuais Azure permitem-lhe colocar uma estrutura de rede na sua implantação Azure. Os VNets podem ser isolados uns contra os outros ou com a ajuda do tráfego de Grupos de Segurança de Rede entre VNets pode ser controlado.
* As Redes Virtuais Azure podem ser alavancadas para atribuir intervalos de endereços IP a VMs ou atribuir endereços IP fixos a VMs
* Para configurar uma ligação Site-To-Site ou Ponto-A-Local, é necessário criar primeiro uma Rede Virtual Azure
* Uma vez implementada uma máquina virtual, já não é possível alterar a Rede Virtual atribuída ao VM

### <a name="quotas-in-azure-virtual-machine-services"></a>Quotas nos serviços de máquinas virtuais Azure
Temos de ser claros quanto ao facto de a infraestrutura de armazenamento e de rede ser partilhada entre os VM que gerem uma variedade de serviços na infraestrutura de Azure. Tal como nos próprios centros de dados do cliente, o excesso de fornecimento de alguns dos recursos de infraestrutura ocorre até certo ponto. A Plataforma Microsoft Azure utiliza discos, CPU, rede e outras quotas para limitar o consumo de recursos e preservar um desempenho consistente e determinístico.  Os diferentes tipos de VM (A5, A6, etc.) têm quotas diferentes para o número de discos, CPU, RAM e Rede.

> [!NOTE]
> Os recursos de CPU e memória dos tipos de VM suportados pelo SAP são pré-atribuídos nos nós hospedeiros. Isto significa que uma vez que o VM é implementado, os recursos no hospedeiro estão disponíveis como definido pelo tipo VM.
>
>

Ao planear e dimensionar o SAP em soluções Azure, devem ser consideradas as quotas para cada tamanho de máquina virtual. As quotas VM são descritas [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

As quotas descritas representam os valores máximos teóricos.  O limite de IOPS por disco pode ser alcançado com pequenos I/Os (8 KB), mas possivelmente não pode ser alcançado com grandes I/Os (1 MB).  O limite de IOPS é aplicado na granularidade do disco único.

Como uma árvore de decisão áspera para decidir se um sistema SAP se encaixa nos Serviços de Máquina Virtual Azure e suas capacidades ou se um sistema existente precisa de ser configurado de forma diferente para implantar o sistema em Azure, a árvore de decisão abaixo pode ser utilizada:

![Decisão da árvore para decidir capacidade de implantar SAP em Azure][planning-guide-figure-700]

1. A informação mais importante para começar é o requisito SAPS para um determinado sistema SAP. Os requisitos SAPS devem ser separados na parte DBMS e na parte da aplicação SAP, mesmo que o sistema SAP já esteja implantado no local numa configuração de 2 níveis. Para os sistemas existentes, o SAPS relacionado com o hardware em uso muitas vezes pode ser determinado ou estimado com base em parâmetros DE SAP existentes. Os resultados podem ser [consultados aqui.](https://sap.com/about/benchmark.html) Para os sistemas SAP recém-implantados, deveria ter passado por um exercício de dimensionamento, que deve determinar os requisitos saps do sistema.
1. Para os sistemas existentes, devem ser medidos os volumes de E/S e as operações de E/S por segundo no servidor DBMS. Para os sistemas recentemente planeados, o exercício de dimensionamento do novo sistema também deve dar ideias bruscas sobre os requisitos de E/S do lado do DBMS. Se não tiver certeza, eventualmente precisa de conduzir uma Prova de Conceito.
1. Compare o requisito SAPS para o servidor DBMS com o SAPS os diferentes tipos de VM de Azure podem fornecer. A informação sobre o SAPS dos diferentes tipos Azure VM está documentada na Nota SAP [1928533]. O foco deve estar primeiro no DBMS VM uma vez que a camada de base de dados é a camada de um sistema SAP NetWeaver que não escala na maioria das implementações. Em contraste, a camada de aplicação SAP pode ser dimensionada. Se nenhum dos tipos de VM suportados pela SAP pode fornecer o SAPS necessário, a carga de trabalho do sistema SAP planeado não pode ser executada em Azure. Ou precisa de implantar o sistema no local ou tem de alterar o volume de carga de trabalho do sistema.
1. Como documentado [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows),][virtual-machines-sizes-windows]o Azure impõe uma quota IOPS por disco independente quer utilize o Armazenamento Standard ou o Armazenamento Premium. Dependente do tipo VM, o número de discos de dados, que podem ser montados, varia. Como resultado, pode calcular um número máximo de IOPS que pode ser alcançado com cada um dos diferentes tipos de VM. Dependendo do layout do ficheiro de base de dados, pode riscar discos para se tornar um volume no sistema operativo convidado. No entanto, se o atual volume de IOPS de um sistema SAP implantado exceder os limites calculados do maior tipo de VM de Azure e se não houver possibilidade de compensar com mais memória, a carga de trabalho do sistema SAP pode ser severamente afetada. Nesses casos, pode atingir um ponto em que não deve implantar o sistema no Azure.
1. Especialmente nos sistemas SAP, que são implantados no local em configurações de 2 Níveis, as probabilidades são de que o sistema possa ter de ser configurado no Azure numa configuração de 3 Níveis. Neste passo, é necessário verificar se existe um componente na camada de aplicação SAP, que não pode ser dimensionado e que não se enquadraria no CPU e nos recursos de memória que os diferentes tipos de VM do Azure oferecem. Se existe realmente tal componente, o sistema SAP e a sua carga de trabalho não podem ser implantados no Azure. Mas se conseguir escalar os componentes da aplicação SAP em vários VMs Azure, o sistema pode ser implantado no Azure.

Se os componentes da camada de aplicação DBMS e SAP puderem ser executados em VMs Azure, a configuração deve ser definida no que diz respeito a:

* Número de VMs Azure
* Tipos de VM para os componentes individuais
* Número de VHDs em DBMS VM para fornecer IOPS suficientes

## <a name="managing-azure-assets"></a>Gestão de ativos da Azure

### <a name="azure-portal"></a>Portal do Azure

O portal Azure é uma das três interfaces para gerir implementações de VM do Azure. As tarefas básicas de gestão, como a implantação de VMs a partir de imagens, podem ser feitas através do portal Azure. Além disso, a criação de Contas de Armazenamento, Redes Virtuais e outros componentes Azure são também tarefas que o portal Azure consegue lidar bem. No entanto, funcionalidades como o upload de VHDs de instalações para Azure ou copiar um VHD dentro do Azure são tarefas, que requerem ferramentas ou administração de terceiros através do PowerShell ou do CLI.

![Microsoft Azure portal - Visão geral da Máquina Virtual][planning-guide-figure-800]


As tarefas de administração e configuração para a instância máquina virtual são possíveis dentro do portal Azure.

Além de reiniciar e desligar uma Máquina Virtual, também pode anexar, separar e criar discos de dados para a instância da Máquina Virtual, para capturar a instância para a preparação da imagem e configurar o tamanho da instância da Máquina Virtual.

O portal Azure fornece funcionalidade básica para implementar e configurar VMs e muitos outros serviços Azure. No entanto, nem todas as funcionalidades disponíveis estão abrangidas pelo portal Azure. No portal Azure, não é possível executar tarefas como:

* Upload VHDs para Azure
* Cópia de VMs


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Gestão através dos cmdlets Microsoft Azure PowerShell

O Windows PowerShell é um quadro poderoso e extensível que tem sido amplamente adotado pelos clientes que implementam um maior número de sistemas em Azure. Após a instalação de cmdlets PowerShell num ambiente de trabalho, portátil ou estação de gestão dedicada, os cmdlets PowerShell podem ser executados remotamente.

O processo para permitir um ambiente de trabalho/portátil local para a utilização de cmdlets Azure PowerShell e como configurar os que são para a utilização com a(s) subscrição Azure(s) é descrito [neste artigo][powershell-install-configure].

Neste [capítulo do Guia de Implantação][deployment-guide-4.1]também podem ser encontrados passos mais pormenorizados sobre como instalar, atualizar e configurar os cmdlets Azure PowerShell .

Até agora, a experiência do cliente tem sido que o PowerShell (PS) é certamente a ferramenta mais poderosa para implantar VMs e criar passos personalizados na implementação de VMs. Todos os clientes que gerem os casos SAP no Azure estão a usar cmdlets PS para complementar as tarefas de gestão que fazem no portal Azure ou até estão a utilizar cmdlets ps exclusivamente para gerir as suas implementações no Azure. Uma vez que os cmdlets específicos do Azure partilham a mesma convenção de nomeação que os mais de 2000 cmdlets relacionados com o Windows, é uma tarefa fácil para os administradores do Windows alavancar esses cmdlets.

Veja o exemplo aqui: <https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


A implantação da extensão Azure para SAP (ver [extensão Azure][planning-guide-9.1] capítulo para SAP neste documento) só é possível através do PowerShell ou do CLI. Por isso, é obrigatório configurar e configurar o PowerShell ou o CLI ao implementar ou administrar um sistema SAP NetWeaver em Azure.

Como o Azure fornece mais funcionalidade, novos cmdlets PS vão ser adicionados que requer uma atualização dos cmdlets. Por isso, faz sentido verificar o site Azure Download pelo menos uma vez por mês <https://azure.microsoft.com/downloads/> para uma nova versão dos cmdlets. A nova versão encontra-se instalada em cima da versão mais antiga.

Para obter uma lista geral de comandos PowerShell relacionados com a Azure consulte aqui: <https://docs.microsoft.com/powershell/azure/> .

### <a name="management-via-microsoft-azure-cli-commands"></a>Gestão através dos comandos Microsoft Azure CLI

Para clientes que usam o Linux e querem gerir os recursos Azure PowerShell pode não ser uma opção. A Microsoft oferece o Azure CLI como alternativa.
O Azure CLI fornece um conjunto de comandos de código aberto e inter-plataforma para trabalhar com a Plataforma Azure. O Azure CLI fornece grande parte da mesma funcionalidade encontrada no portal Azure.

Para obter informações sobre instalação, configuração e como utilizar comandos CLI para realizar tarefas do Azure ver

* [Instalar a CLI clássica do Azure][xplat-cli]
* [Implementar e gerir máquinas virtuais utilizando modelos de Gestor de Recursos Azure e o CLI Azure] [.. /.. /linux/create-ssh-secured-vm-from-template.md]
* [Use o CLI clássico do Azure para Mac, Linux e Windows com Azure Resource Manager][xplat-cli-azure-resource-manager]

Leia também o capítulo [Azure CLI para Os VMs Linux][deployment-guide-4.5.2] no [Guia de Implementação][planning-guide] sobre como utilizar o CLI Azure para implantar a Extensão Azure para SAP.


## <a name="first-steps-planning-a-deployment"></a>Primeiros passos planejando uma implantação
O primeiro passo no planeamento da implantação é não verificar se há VMs disponíveis para executar o SAP. O primeiro passo pode ser aquele que é demorado, mas o mais importante, é trabalhar com as equipas de conformidade e segurança na sua empresa sobre quais são as condições limite para implantar que tipo de carga de trabalho sap ou processo de negócios em nuvem pública. Se a sua empresa implementou outro software antes para o Azure, o processo pode ser fácil. Se a sua empresa estiver mais no início da viagem, poderá haver discussões mais alargadas para descobrir as condições de fronteira, condições de segurança, que permitem que certos dados sap e processos de negócios SAP sejam hospedados em nuvem pública.

Como ajuda útil, pode apontar para [as ofertas](/microsoft-365/compliance/offering-home) de conformidade da Microsoft para uma lista de ofertas de conformidade que a Microsoft pode fornecer.

Outras áreas de preocupação, como encriptação de dados em repouso ou outra encriptação no serviço Azure, estão documentadas na [visão geral da encriptação Azure](../../../security/fundamentals/encryption-overview.md).

Não subestimes esta fase do projeto no teu planeamento. Só quando se tem acordo e regras em torno deste tema, é preciso dar o próximo passo, que é o planeamento da arquitetura de rede que implementa no Azure.


## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Diferentes formas de implantar VMs para SAP em Azure

Neste capítulo, aprende-se as diferentes formas de implantar um VM em Azure. Neste capítulo estão abrangidos procedimentos adicionais de preparação, bem como o manuseamento de VHDs e VMs em Azure.

### <a name="deployment-of-vms-for-sap"></a>Implantação de VMs para SAP

O Microsoft Azure oferece várias formas de implementar VMs e discos associados. Assim, é importante compreender as diferenças, uma vez que as preparações dos VM podem diferir em função do método de implantação. Em geral, olhamos para os seguintes cenários:

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Mover um VM das instalações para Azure com um disco não generalizado

Planeia mover um sistema SAP específico do local para Ozure. Isto pode ser feito através do upload do VHD, que contém o SO, os binários SAP e DBMS, além dos VHDs com os dados e ficheiros de registo do DBMS para Azure. Em contraste com o [cenário #2 abaixo][planning-guide-5.1.2], mantenha as contas de utilizadores hostname, SAP SID e SAP no Azure VM, uma vez que foram configuradas no ambiente no local. Portanto, generalizar a imagem não é necessário. Ver capítulos [Preparação para a deslocação de um VM das instalações para Azure com um disco não generalizado][planning-guide-5.2.1] deste documento para etapas de preparação no local e carregamento de VMs ou VHDs não generalizados para Azure. Leia o capítulo [3: Mover um VM das instalações utilizando um Azure VHD não generalizado com o SAP][deployment-guide-3.4] no Guia de [Implantação][deployment-guide] para passos detalhados de implantação de tal imagem em Azure.

Outra opção que não discutiremos em detalhe neste guia é a utilização da Recuperação do Site Azure para replicar os Servidores de Aplicações SAP NetWeaver e os Serviços Centrais SAP NetWeaver para o Azure. Não recomendamos a utilização da Recuperação do Local de Azure para a camada de base de dados e, em vez disso, utilizar mecanismos de replicação específicos da base de dados, como a replicação do sistema HANA. Para obter mais informações, consulte o capítulo [Protect SAP](../../../site-recovery/site-recovery-workload.md#protect-sap) of the About disaster recovery [for in-in-in apps](../../../site-recovery/site-recovery-workload.md) guide.

#### <a name="deploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Implantação de um VM com uma imagem específica do cliente

Devido aos requisitos específicos de correção da sua versão OS ou DBMS, as imagens fornecidas no Azure Marketplace podem não corresponder às suas necessidades. Portanto, poderá ser necessário criar um VM utilizando a sua própria imagem privada de OS/DBMS VM, que pode ser implantada várias vezes depois. Para preparar tal imagem privada para duplicação, devem ser considerados os seguintes itens:

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Consulte mais detalhes aqui: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> As definições do Windows (como o Windows SID e o nome de anfitrião) devem ser abstraídas/generalizadas nas VM no local através do comando sysprep.
>
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> Siga os passos descritos nestes artigos para [SUSE,][virtual-machines-linux-create-upload-vhd-suse] [Red Hat,][virtual-machines-linux-redhat-create-upload-vhd]ou [Oracle Linux,][virtual-machines-linux-create-upload-vhd-oracle]para preparar um VHD para ser enviado para Azure.
>
>

---
Se já instalou conteúdo SAP nas suas VM (especialmente para sistemas de 2 Níveis), pode adaptar as definições do sistema SAP após a implantação do Azure VM através do procedimento de renomeação de exemplo suportado pelo Gestor de Provisionamento de Software SAP (NOTA SAP [1619720).] Ver capítulos [Preparação para a implantação de um VM com uma imagem específica do cliente para SAP][planning-guide-5.2.2] e upload de um [VHD de instalações para Azure][planning-guide-5.3.2] deste documento para etapas de preparação no local e upload de um VM generalizado para Azure. Leia o capítulo [Cenário 2: Implantação de um VM com uma imagem personalizada para SAP][deployment-guide-3.3] no Guia de [Implantação][deployment-guide] para etapas detalhadas de implantação de tal imagem em Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Implantação de um VM fora do Mercado Azure

Gostaria de utilizar uma imagem VM fornecida pela Microsoft ou por terceiros do Azure Marketplace para implementar o seu VM. Depois de ter implantado o seu VM em Azure, segue as mesmas diretrizes e ferramentas para instalar o software SAP e/ou DBMS dentro do seu VM como faria num ambiente no local. Para obter uma descrição mais detalhada da implementação, consulte o capítulo [Cenário 1: Implantação de um VM fora do Mercado azul para SAP][deployment-guide-3.2] no Guia de [Implantação][deployment-guide].

### <a name="preparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Preparação de VMs com SAP para Azure

Antes de enviar VMs para Azure, você precisa se certificar de que os VMs e VHDs cumprem determinados requisitos. Existem pequenas diferenças dependendo do método de implantação que é usado.

#### <a name="preparation-for-moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Preparação para a deslocação de um VM das instalações para Ozure com um disco não generalizado

Um método comum de implantação é mover um VM existente, que executa um sistema SAP de instalações para Azure. Esse VM e o sistema SAP no VM só devem funcionar em Azure usando o mesmo nome de hospedeiro e provavelmente o mesmo SAP SID. Neste caso, o SO convidado da VM não deve ser generalizado para múltiplas implementações. Se a rede no local foi estendida para Azure, então até as mesmas contas de domínio podem ser utilizadas dentro do VM, como as utilizadas antes do local.

Os requisitos para preparar o seu próprio Disco VM Azure são:

* Originalmente, o VHD que contém o sistema operativo poderia ter um tamanho máximo de apenas 127 GB. Esta limitação foi eliminada no final de março de 2015. Agora, o VHD que contém o sistema operativo pode ter até 1 TB de tamanho como qualquer outro Azure Storage hospedado VHD também.
* Tem de estar no formato VHD fixo. VHDs dinâmicos ou VHDs em formato VHDx ainda não são suportados no Azure. Os VHDs dinâmicos serão convertidos em VHDs estáticos quando carregar o VHD com comandos PowerShell ou CLI
* Os VHDs, que são montados no VM e devem ser montados novamente em Azure para o VM também precisam estar num formato VHD fixo. Leia [este artigo](../../managed-disks-overview.md) para limites de tamanho dos discos de dados. Os VHDs dinâmicos serão convertidos em VHDs estáticos quando carregar o VHD com comandos PowerShell ou CLI
* Adicione outra conta local com privilégios de administrador, que pode ser usada pelo suporte da Microsoft ou que pode ser atribuída como contexto para serviços e aplicações a funcionar até que o VM seja implementado e utilizadores mais apropriados possam ser usados.
* Adicione outras contas locais, uma vez que estas podem ser necessárias para o cenário de implantação específico.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Neste cenário, não é necessária qualquer generalização (sysprep) do VM para carregar e implantar o VM no Azure.
> Certifique-se de que a unidade D:\ não é usado.
> Defina a desmontagem automática do disco para discos anexados, conforme descrito no capítulo [Definição de peças automáticas para discos anexados][planning-guide-5.5.3] neste documento.
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> Neste cenário, não é necessária qualquer generalização (waagent -deprovision) do VM para carregar e implantar o VM em Azure.
> Certifique-se de que /mnt/recurso não é utilizado e que todos os discos são montados via uuid. Para o disco SO, certifique-se de que a entrada do bootloader também reflete o suporte baseado em uuid.
>
>

---
#### <a name="preparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Preparação para implantação de um VM com uma imagem específica do cliente para SAP

Os ficheiros VHD que contêm um SO generalizado são armazenados em contentores em Contas de Armazenamento Azure ou como imagens de Disco Gerido. Pode implementar um novo VM a partir de tal imagem, referindo a imagem VHD ou Disco Gerido como uma fonte nos seus ficheiros de modelo de implantação, conforme descrito no capítulo [Cenário 2: Implantar um VM com uma imagem personalizada para SAP][deployment-guide-3.3] do Guia de [Implantação][deployment-guide].

Os requisitos para preparar a sua própria Imagem Azure VM são:

* Originalmente, o VHD que contém o sistema operativo poderia ter um tamanho máximo de apenas 127 GB. Esta limitação foi eliminada no final de março de 2015. Agora, o VHD que contém o sistema operativo pode ter até 1 TB de tamanho como qualquer outro Azure Storage hospedado VHD também.
* Tem de estar no formato VHD fixo. VHDs dinâmicos ou VHDs em formato VHDx ainda não são suportados no Azure. Os VHDs dinâmicos serão convertidos em VHDs estáticos quando carregar o VHD com comandos PowerShell ou CLI
* Os VHDs, que são montados no VM e devem ser montados novamente em Azure para o VM também precisam estar num formato VHD fixo. Leia [este artigo](../../managed-disks-overview.md) para limites de tamanho dos discos de dados. Os VHDs dinâmicos serão convertidos em VHDs estáticos quando carregar o VHD com comandos PowerShell ou CLI
* Adicione outras contas locais, uma vez que estas podem ser necessárias para o cenário de implantação específico.
* Se a imagem contiver uma instalação do SAP NetWeaver e se renomear o nome do anfitrião a partir do nome original no ponto da implantação do Azure, é aconselhável copiar as versões mais recentes do DVD do GESTOR de Provisionamento de Software SAP no modelo. Isto permitir-lhe-á utilizar facilmente a funcionalidade SAP fornecida para adaptar o nome de hospedeiro alterado e/ou alterar o SID do sistema SAP dentro da imagem VM implantada assim que uma nova cópia é iniciada.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Certifique-se de que a unidade D:\ não é utilizado Conjunto de conjunto automático para discos anexados, conforme descrito no capítulo [Definição de conjunto automático para discos anexados][planning-guide-5.5.3] neste documento.
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> Certifique-se de que /mnt/recurso não é utilizado e que todos os discos são montados via uuid. Para o disco SO, certifique-se de que a entrada do bootloader também reflete o suporte baseado em uuid.
>
>

---
* O SAP GUI (para fins administrativos e de configuração) pode ser pré-instalado em tal modelo.
* Outros softwares necessários para executar os VMs com sucesso em cenários de premissas cruzadas podem ser instalados desde que este software possa funcionar com o renome do VM.

Se o VM estiver preparado o suficiente para ser genérico e eventualmente independente das contas/utilizadores não disponíveis no cenário de implantação do Azure visado, é conduzido o último passo de preparação para generalizar tal imagem.

##### <a name="generalizing-a-vm"></a>Generalização de um VM
---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> O último passo é inscrever-se num VM com uma conta de Administrador. Abra uma janela de comando do Windows como *administrador* . Vá a %windir%\windows\system32\sysprep e execute sysprep.exe.
> Aparecerá uma pequena janela. É importante verificar a opção **Generalização** (o padrão não é verificado) e alterar a Opção de Encerramento do seu padrão de 'Reboot' para 'shutdown'. Este procedimento pressupõe que o processo sysprep é executado no local no So convidado de um VM.
> Se pretender realizar o procedimento com um VM já em execução em Azure, siga os passos descritos [neste artigo](../../windows/capture-image-resource.md).
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> [Como capturar uma máquina virtual do Linux para ser utilizada como modelo do Resource Manager (How to capture a Linux virtual machine to use as a Resource Manager template)][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Transferência de VMs e VHDs entre as instalações para Azure
Uma vez que o upload de imagens e discos VM para Azure não é possível através do portal Azure, é necessário utilizar cmdlets Azure PowerShell ou CLI. Outra possibilidade é a utilização da ferramenta 'AzCopy'. A ferramenta pode copiar VHDs entre as instalações e o Azure (em ambas as direções). Também pode copiar VHDs entre regiões de Azure. Consulte [esta documentação][storage-use-azcopy] para download e utilização da AzCopy.

Uma terceira alternativa seria utilizar várias ferramentas orientadas para o GUI de terceiros. No entanto, certifique-se de que estas ferramentas estão a suportar as Bolhas de Página Azure. Para os nossos propósitos, precisamos de utilizar a loja Azure Page Blob (as diferenças são descritas aqui: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs> ). Além disso, as ferramentas fornecidas pela Azure são eficientes na compressão dos VMs e VHDs, que precisam de ser carregados. Isto é importante porque esta eficiência na compressão reduz o tempo de carregamento (que varia de qualquer forma dependendo da ligação de upload para a internet a partir da instalação de instalações no local e da região de implantação de Azure direcionada). É um pressuposto justo que o envio de um VM ou VHD de localização europeia para os centros de dados Azure sediados nos EUA levará mais tempo do que carregar os mesmos VMs/VHDs para os centros de dados europeus Azure.

#### <a name="uploading-a-vhd-from-on-premises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>O upload de um VHD de instalações para Azure
Para carregar um VM ou VHD existente da rede no local, tal VM ou VHD precisa de satisfazer os requisitos listados no capítulo [Preparação para a deslocação de um VM de instalações para Azure com um disco não generalizado][planning-guide-5.2.1] deste documento.

Tal VM NÃO precisa de ser generalizada e pode ser carregado no estado e na forma que tem após o encerramento do lado do local. O mesmo acontece com os VHDs adicionais, que não contêm qualquer sistema operativo.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Carregar um VHD e torná-lo um Disco Azure
Neste caso, queremos carregar um VHD, com ou sem um SISTEMA, e montá-lo num VM como disco de dados ou usá-lo como disco de OS. Este é um processo em várias etapas

**PowerShell**

* Inscreva-se na sua subscrição com *o Connect-AzAccount*
* Desaprote a subscrição do seu contexto com *Set-AzContext* e parâmetro SubscriptionId ou SubscriptionName - ver <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Faça o upload do VHD com *Add-AzVhd* para uma conta de armazenamento Azure - ver <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Opcional) Criar um Disco Gerido a partir do VHD com *New-AzDisk*  - ver <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Desloque o disco DE de um novo config VM para o VHD ou Disco Gerido com *Set-AzVMOSDisk* - ver <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Criar um novo VM a partir do VM config com *New-AzVM* - ver <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Adicione um disco de dados a um novo VM com *Add-AzVMDataDisk* - ver <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**CLI do Azure**

* Inicie sessão na sua subscrição com *login az*
* Selecione a sua subscrição com *conjunto de conta Az `<subscription name or id` > --subscrição*
* Faça o upload do VHD com *uma bolha de armazenamento az* - ver [Usando o Azure CLI com armazenamento Azure][storage-azure-cli]
* (Opcional) Criar um Disco Gerido a partir do VHD com *o disco az criar* - ver https://docs.microsoft.com/cli/azure/disk
* Criar um novo VM especificando o VHD ou Disco Gerido como disco OS com *az vm criar* e parâmetro *--attach-os-disk*
* Adicione um disco de dados a um novo VM com *a az vm anexação* e parâmetro *-- novo*

**Modelo**

* Faça o upload do VHD com PowerShell ou Azure CLI
* (Opcional) Criar um Disco Gerido a partir do VHD com PowerShell, Azure CLI ou o portal Azure
* Implemente o VM com um modelo JSON referindo o VHD como mostrado [neste modelo JSON exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) ou usando Discos Geridos como mostrado neste modelo [JSON exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Implantação de uma imagem VM
Para carregar um VM ou VHD existente da rede de instalações, a fim de usá-lo como uma imagem VM Azure tal um VM ou VHD precisa satisfazer os requisitos listados no capítulo [Preparação para a implementação de um VM com uma imagem específica][planning-guide-5.2.2] do cliente para SAP deste documento.

* Use *sysprep* no Windows ou *waagent -deprovisionamento* no Linux para generalizar o seu VM - consulte [referência técnica do Sysprep](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) para Windows ou Como capturar uma máquina virtual [Linux para usar como modelo de Gestor de Recursos][capture-image-linux-step-2-create-vm-image] para Linux
* Inscreva-se na sua subscrição com *o Connect-AzAccount*
* Desaprote a subscrição do seu contexto com *Set-AzContext* e parâmetro SubscriptionId ou SubscriptionName - ver <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Faça o upload do VHD com *Add-AzVhd* para uma conta de armazenamento Azure - ver <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Opcional) Criar uma imagem de disco gerido a partir do VHD com *New-AzImage*  - ver <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Desfie o disco DE de um novo config VM para o
  * VHD com *Set-AzVMOSDisk -SourceImageUri -CreateOption fromImage* - ver <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Conjunto de imagem de disco *gerido-AzVMSourceImage* - ver <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Criar um novo VM a partir do VM config com *New-AzVM* - ver <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**CLI do Azure**

* Use *sysprep* no Windows ou *waagent -deprovisionamento* no Linux para generalizar o seu VM - consulte [referência técnica do Sysprep](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) para Windows ou Como capturar uma máquina virtual [Linux para usar como modelo de Gestor de Recursos][capture-image-linux-step-2-create-vm-image] para Linux
* Inicie sessão na sua subscrição com *login az*
* Selecione a sua subscrição com *conjunto de conta Az `<subscription name or id` > --subscrição*
* Faça o upload do VHD com *uma bolha de armazenamento az* - ver [Usando o Azure CLI com armazenamento Azure][storage-azure-cli]
* (Opcional) Criar uma imagem de disco gerido a partir do VHD com *a imagem az criar* - ver https://docs.microsoft.com/cli/azure/image
* Criar um novo VM especificando a imagem de VHD ou Disco Gerido como disco OS com criação de *vm az* e parâmetro *--imagem*

**Modelo**

* Use *sysprep* no Windows ou *waagent -deprovisionamento* no Linux para generalizar o seu VM - consulte [referência técnica do Sysprep](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) para Windows ou Como capturar uma máquina virtual [Linux para usar como modelo de Gestor de Recursos][capture-image-linux-step-2-create-vm-image] para Linux
* Faça o upload do VHD com PowerShell ou Azure CLI
* (Opcional) Criar uma imagem de disco gerido a partir do VHD com PowerShell, Azure CLI ou o portal Azure
* Implemente o VM com um modelo JSON referindo a imagem VHD como mostrado [neste modelo JSON exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) ou usando a Imagem do Disco Gerido como mostrado neste modelo [JSON exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Descarregar VHDs ou Discos Geridos para as instalações
A Azure Infrastructure as a Service não é uma rua de mão única de apenas ser capaz de carregar VHDs e sistemas SAP. Você pode mover os sistemas SAP de Azure de volta para o mundo do local também.

Durante o download, os VHDs ou Discos Geridos não podem estar ativos. Mesmo ao descarregar discos, que são montados em VMs, o VM precisa de ser desligado e transatado. Se quiser apenas descarregar o conteúdo da base de dados, que, então, deve ser usado para configurar um novo sistema no local e se for aceitável que durante o tempo do descarregamento e a configuração do novo sistema em Azure ainda possa estar operacional, poderá evitar um longo tempo de inatividade através da realização de uma base de dados comprimida num disco e apenas descarregar esse disco em vez de também descarregar o VM base oss.

#### <a name="powershell"></a>PowerShell

* Descarregar um Disco Gerido primeiro precisa de ter acesso à bolha subjacente do Disco Gerido. Em seguida, pode copiar a bolha subjacente para uma nova conta de armazenamento e fazer o download da bolha desta conta de armazenamento.

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

* Descarregar um VHD Uma vez que o sistema SAP é parado e o VM é desligado, você pode usar o cmdlet PowerShell `Save-AzVhd` no alvo no local para baixar os discos VHD de volta para o mundo do local. Para isso, precisa do URL do VHD, que pode encontrar na 'secção de armazenamento' do portal Azure (precisa navegar para a Conta de Armazenamento e para o recipiente de armazenamento onde o VHD foi criado) e precisa de saber para onde o VHD deve ser copiado.

  Em seguida, pode aproveitar o comando definindo o parâmetro SourceUri como o URL do VHD para descarregar e o LocalFilePath como a localização física do VHD (incluindo o seu nome). O comando pode parecer:

  ```powershell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Para mais detalhes sobre o Save-AzVhd cmdlet, consulte <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd> aqui.

#### <a name="azure-cli"></a>CLI do Azure
* Descarregar um Disco Gerido primeiro precisa de ter acesso à bolha subjacente do Disco Gerido. Em seguida, pode copiar a bolha subjacente para uma nova conta de armazenamento e fazer o download da bolha desta conta de armazenamento.

  ```azurecli
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Descarregar um VHD Uma vez que o sistema SAP é parado e o VM é desligado, você pode usar o comando Azure CLI `_azure storage blob download_` no alvo no local para baixar os discos VHD de volta para o mundo do local. Para isso, precisa do nome e do recipiente do VHD, que pode encontrar na 'Secção de Armazenamento' do portal Azure (precisa navegar para a Conta de Armazenamento e para o recipiente de armazenamento onde o VHD foi criado) e precisa de saber para onde o VHD deve ser copiado.

  Em seguida, pode aproveitar o comando definindo os parâmetros blob e o recipiente do VHD para descarregar e o destino como a localização do alvo físico do VHD (incluindo o seu nome). O comando pode parecer:

  ```azurecli
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Transferência de VMs e discos dentro de Azure

#### <a name="copying-sap-systems-within-azure"></a>Cópia dos sistemas SAP dentro do Azure

Um sistema SAP ou mesmo um servidor DBMS dedicado que suporte uma camada de aplicação SAP provavelmente será composto por vários discos, que contêm o SO com os binários ou os dados e ficheiros de registo da base de dados SAP. Nem a funcionalidade Azure de copiar discos nem a funcionalidade Azure de guardar discos para um disco local tem um mecanismo de sincronização, que desloque vários discos de forma consistente. Portanto, o estado dos discos copiados ou guardados, mesmo que estes sejam montados contra o mesmo VM seria diferente. Isto significa que, no caso concreto de ter dados diferentes e ficheiros de registo contidos nos diferentes discos, a base de dados no final seria inconsistente.

**Conclusão: Para copiar ou guardar discos, que fazem parte de uma configuração do sistema SAP, é necessário parar o sistema SAP e também para desligar o VM implantado. Só assim é possível copiar ou descarregar o conjunto de discos para criar uma cópia do sistema SAP em Azure ou no local.**

Os discos de dados podem ser armazenados como ficheiros VHD numa Conta de Armazenamento Azure e podem ser diretamente ligados a uma máquina virtual ou ser usados como imagem. Neste caso, o VHD é copiado para outro local antes de ser ligado à máquina virtual. O nome completo do ficheiro VHD em Azure deve ser único dentro do Azure. Como mencionado anteriormente, o nome é uma espécie de nome em três partes que se parece com:

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

Os discos de dados também podem ser Geridos Discos. Neste caso, o Disco Gerido é utilizado para criar um novo Disco Gerido antes de ser ligado à máquina virtual. O nome do Disco Gerido deve ser único dentro de um grupo de recursos.

##### <a name="powershell"></a>PowerShell

Pode utilizar cmdlets Azure PowerShell para copiar um VHD como mostrado [neste artigo][storage-powershell-guide-full-copy-vhd]. Para criar um novo Disco Gerido, utilize New-AzDiskConfig e New-AzDisk como mostrado no exemplo seguinte.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>CLI do Azure

Pode utilizar o Azure CLI para copiar um VHD. Para criar um novo Disco Gerido, utilize *o disco az criar* como mostrado no exemplo seguinte.

```azurecli
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Ferramentas de armazenamento Azure

* <https://storageexplorer.com/>

Edições profissionais do Azure Storage Explorers podem ser consultadas aqui:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

A cópia de um VHD em si dentro de uma conta de armazenamento é um processo, que leva apenas alguns segundos (semelhante ao hardware SAN criando instantâneos com cópia preguiçosa e cópia na escrita). Depois de ter uma cópia do ficheiro VHD, pode anexá-lo a uma máquina virtual ou usá-lo como imagem para anexar cópias do VHD a máquinas virtuais.

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

#### <a name="copying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Cópia de discos entre contas de armazenamento Azure
Esta tarefa não pode ser executada no portal Azure. Você pode usar cmdlets Azure PowerShell, Azure CLI ou um navegador de armazenamento de terceiros. Os comandos PowerShell cmdlets ou CLI podem criar e gerir bolhas, que incluem a capacidade de copiar assíncronamente bolhas através das Contas de Armazenamento e em todas as regiões dentro da subscrição Azure.

##### <a name="powershell"></a>PowerShell
Também pode copiar VHDs entre subscrições. Para mais informações, leia [este artigo.][storage-powershell-guide-full-copy-vhd]

O fluxo básico da lógica do CMDLET ps é assim:

* Criar um contexto de conta de armazenamento para a conta de armazenamento **de origem** com *New-AzStorageContext* - ver <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Criar um contexto de conta de armazenamento para a conta de armazenamento **alvo** com *New-AzStorageContext* - ver <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Inicie a cópia com

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Verifique o estado da cópia em loop com

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Fixe o novo VHD a uma máquina virtual, tal como descrito acima.

Por exemplo, veja [este artigo.][storage-powershell-guide-full-copy-vhd]

##### <a name="azure-cli"></a>CLI do Azure
* Inicie a cópia com

```azurecli
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Verifique o estado se a cópia ainda está em ciclo com

```azurecli
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Fixe o novo VHD a uma máquina virtual, tal como descrito acima.

### <a name="disk-handling"></a>Manipulação de discos

#### <a name="vmdisk-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Estrutura VM/disco para implementações DE SAP

Idealmente, o manuseamento da estrutura de um VM e dos discos associados deve ser simples. Nas instalações no local, os clientes desenvolveram muitas formas de estruturar uma instalação de servidor.

* Um disco base, que contém o SISTEMA e todos os binários do DBMS e/ou SAP. Desde março de 2015, este disco pode ter até 1 TB em tamanho em vez de restrições anteriores que o limitaram a 127 GB.
* Um ou vários discos, que contêm o ficheiro de registo DBMS da base de dados SAP e o ficheiro de registo da área de armazenamento de temperaturas DBMS (se o DBMS o suportar). Se os requisitos de registo de base de dados IOPS forem elevados, é necessário riscar vários discos para alcançar o volume IOPS necessário.
* Vários discos que contêm um ou dois ficheiros de base de dados da base de dados SAP e dos ficheiros de dados temporários DBMS também (se o DBMS o suportar).

![Configuração de referência do Azure IaaS VM para SAP][planning-guide-figure-1300]


---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Com muitos clientes vimos configurações onde, por exemplo, binários SAP e DBMS não foram instalados no c:\ unidade onde o SO foi instalado. Havia várias razões para isso, mas quando voltámos à raiz, normalmente era que as unidades eram pequenas e os upgrades de SO precisavam de espaço adicional há 10-15 anos. Ambas as condições já não se aplicam com demasiada frequência. Hoje o c:\ a unidade pode ser mapeada em discos de grande volume ou VMs. Para manter as implementações simples na sua estrutura, recomenda-se seguir o seguinte padrão de implementação para os sistemas SAP NetWeaver em Azure
>
> O ficheiro de página do sistema operativo Windows deve estar no D: unidade (disco não persistente)
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> Coloque o ficheiro de swap linux em /mnt /mnt/recurso no Linux, conforme descrito neste [artigo][virtual-machines-linux-agent-user-guide]. O ficheiro de troca pode ser configurado no ficheiro de configuração do Agente Linux /etc/waagent.conf. Adicione ou altere as seguintes definições:
>
>

```console
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Para ativar as alterações, é necessário reiniciar o Agente Linux com

```console
sudo service waagent restart
```

Leia a Nota [SAP 1597355] para mais detalhes sobre o tamanho recomendado do ficheiro swap

---
O número de discos utilizados para os ficheiros de dados DBMS e o tipo de Armazenamento Azure em que estes discos estão alojados devem ser determinados pelos requisitos do IOPS e pela latência necessária. As quotas exatas são descritas [neste artigo (Linux)][virtual-machines-sizes-linux] e [neste artigo (Windows)][virtual-machines-sizes-windows].

A experiência das implementações da SAP nos últimos dois anos ensinou-nos algumas lições, que podem ser resumidas como:

* O tráfego do IOPS para diferentes ficheiros de dados nem sempre é o mesmo, uma vez que os sistemas de clientes existentes podem ter ficheiros de dados de tamanho diferente que representam a sua base de dados SAP. Como resultado, acabou por ser melhor usando uma configuração RAID sobre vários discos para colocar os ficheiros de dados LUNs esculpidos desses. Houve situações, especialmente com o Azure Standard Storage, em que uma taxa IOPS atingiu a quota de um único disco contra o registo de transações DBMS. Nesses cenários, recomenda-se a utilização de Armazenamento Premium ou, em alternativa, agregar vários discos standard de armazenamento com uma risca de software.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> * [Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure][virtual-machines-sql-server-performance-best-practices]
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> * [Configure software RAID no Linux][virtual-machines-linux-configure-raid]
> * [Configure a LVM num Linux VM em Azure][virtual-machines-linux-configure-lvm]
>
>

---
* O Premium Storage está a apresentar um melhor desempenho significativo, especialmente para as gravações críticas de registos de transações. Para cenários SAP que se espera que entreguem produção como desempenho, é altamente recomendado usar VM-Series que possam alavancar o Azure Premium Storage.

Tenha em mente que o disco, que contém o SO, e como recomendamos, os binários do SAP e da base de dados (VM base) também não se limitam a 127 GB. Agora pode ter até 1 TB de tamanho. Isto deve ser espaço suficiente para manter todos os ficheiros necessários, incluindo, por exemplo, registos de trabalho em lote SAP.

Para mais sugestões e mais detalhes, especificamente para VMS DBMS, consulte o [Guia de Implantação do DBMS][dbms-guide]

#### <a name="disk-handling"></a>Manipulação de discos

Na maioria dos cenários, é necessário criar discos adicionais para implantar a base de dados SAP no VM. Falámos sobre as considerações sobre o número de discos no capítulo [VM/estrutura de disco para as implementações][planning-guide-5.5.1] do SAP deste documento. O portal Azure permite ligar e separar os discos assim que um VM base for implantado. Os discos podem ser ligados/separados quando o VM está a funcionar, bem como quando é parado. Ao anexar um disco, o portal Azure oferece-se para anexar um disco vazio ou um disco existente, que neste momento não está ligado a outro VM.

**Nota:** Os discos só podem ser ligados a um VM a qualquer momento.

![Anexar / desprender discos com armazenamento padrão Azure][planning-guide-figure-1400]

Durante a implementação de uma nova máquina virtual, pode decidir se pretende utilizar Discos Geridos ou colocar os discos nas Contas de Armazenamento Azure. Se quiser utilizar o Armazenamento Premium, recomendamos a utilização de Discos Geridos.

Em seguida, tem de decidir se pretende criar um disco novo e vazio ou se pretende selecionar um disco existente que foi carregado mais cedo e deve ser anexado ao VM agora.

**IMPORTANTE** : **NÃO** pretende utilizar o Caching hospedeiro com armazenamento padrão Azure. Deve deixar a preferência de Cache anfitrião por defeito de NENHUM. Com o Azure Premium Storage, deverá ativar o Read Caching se a característica de E/S for lida maioritariamente como tráfego de E/S típico contra ficheiros de dados de base de dados. No caso de registo de transações de base de dados, não é recomendado qualquer caching.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> [Como anexar um disco de dados no portal Azure][virtual-machines-linux-attach-disk-portal]
>
> Se os discos estiverem ligados, tem de iniciar sação no VM para abrir o Gestor do Disco do Windows. Se a desmontagem automática não estiver ativada como recomendado no capítulo [Definição de automontagens para discos anexados,][planning-guide-5.5.3]o volume recém-anexado deve ser tomado on-line e inicializado.
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> Se os discos estiverem ligados, tem de iniciar sação no VM e inicializar os discos conforme descrito [neste artigo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

---
Se o novo disco for um disco vazio, também tens de formatar o disco. Para formatação, especialmente para dados DBMS e ficheiros de registo, aplicam-se as mesmas recomendações que para as implementações de metais nus do DBMS.

Uma conta de Armazenamento Azure não fornece recursos infinitos em termos de volume de E/O, IOPS e volume de dados. Normalmente, os VMS DBMS são os mais afetados por isto. Talvez seja melhor utilizar uma conta de armazenamento separada para cada VM se tiver poucos VMs de volume de E/S elevados para implantar, a fim de se manter dentro do limite do volume da Conta de Armazenamento Azure. Caso contrário, precisa de ver como pode equilibrar estes VMs entre diferentes contas de Armazenamento sem atingir o limite de cada conta de armazenamento única. Mais detalhes são discutidos no Guia de Implantação do [DBMS.][dbms-guide] Também deve ter em mente estas limitações para vMs de servidor de aplicação SAP puro ou outros VMs, que eventualmente podem necessitar de VHDs adicionais. Estas restrições não se aplicam se utilizar o Disco Gerido. Se pretender utilizar o Armazenamento Premium, recomendamos a utilização do Disco Gerido.

Outro tópico, que é relevante para as Contas de Armazenamento é se os VHDs numa Conta de Armazenamento estão a ser geo-replicados. A geo-replicação está ativada ou desativada ao nível da Conta de Armazenamento e não ao nível VM. Se a geo-replicação estiver ativada, os VHDs dentro da Conta de Armazenamento seriam replicados em outro centro de dados Azure dentro da mesma região. Antes de decidir sobre isto, deve pensar sobre a seguinte restrição:

A azure Geo-replicação funciona localmente em cada VHD em um VM e não replica o I/Os em ordem cronológica através de vários VHDs em VM. Portanto, o VHD que representa o VM base, bem como quaisquer VHDs adicionais ligados ao VM são replicados independentemente uns dos outros. Isto significa que não há sincronização entre as alterações nos diferentes VHDs. O facto de o I/Os ser replicado independentemente da ordem em que estão escritos significa que a geo-replicação não é de valor para servidores de base de dados que têm as suas bases de dados distribuídas por vários VHDs. Além do DBMS, também pode haver outras aplicações em que os processos escrevem ou manipulam dados em diferentes VHDs e onde é importante manter a ordem das alterações. Se isso for um requisito, a geo-replicação em Azure não deve ser ativada. Dependendo se você precisa ou quer geo-replicação para um conjunto de VMs, mas não para outro conjunto, você já pode categorizar VMs e seus VHDs relacionados em diferentes Contas de Armazenamento que têm geo-replicação ativada ou desativada.

#### <a name="setting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Definição automática para discos anexos
---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Para os VMs, que são criados a partir de imagens ou discos próprios, é necessário verificar e possivelmente definir o parâmetro de automonto. A definição deste parâmetro permitirá ao VM, após um reinício ou recolocação em Azure, montar automaticamente as unidades anexadas/montadas.
> O parâmetro está definido para as imagens fornecidas pela Microsoft no Azure Marketplace.
>
> Para definir a automonto, consulte a documentação da linha de comando executável diskpart.exe aqui:
>
> * [Opções de Command-Line de parte de disco](/previous-versions/windows/it-pro/windows-xp/bb490893(v=technet.10))
> * [Automount](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc753703(v=ws.11))
>
> A janela da linha de comando do Windows deve ser aberta como administrador.
>
> Se os discos estiverem ligados, tem de iniciar sação no VM para abrir o Gestor do Disco do Windows. Se a desmontagem automática não estiver ativada como recomendado no capítulo [Definição de peças automáticas para discos anexados,][planning-guide-5.5.3]o volume recentemente anexado >deve ser tomado on-line e inicializado.
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> É necessário rubricar um disco vazio recentemente anexado, conforme descrito [neste artigo.][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
> Também é necessário adicionar novos discos ao /etc/fstab.
>
>

---
### <a name="final-deployment"></a>Implantação final

Para a colocação final e os passos exatos, especialmente no que diz respeito à implantação da Extensão Azure para SAP, consulte o [Guia de Implantação][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Aceder aos sistemas SAP em execução dentro dos VMs Azure

Para cenários em que pretende ligar-se a esses sistemas SAP através da internet pública utilizando o SAP GUI, é necessário aplicar os seguintes procedimentos.

Mais tarde no documento discutiremos o outro cenário importante, ligando-se aos sistemas SAP em instalações de instalações cruzadas, que têm uma ligação local-local (túnel VPN) ou Azure ExpressRoute entre os sistemas de instalações e sistemas Azure.

### <a name="remote-access-to-sap-systems"></a>Acesso remoto aos sistemas SAP

Com o Azure Resource Manager, já não existem pontos finais padrão como no antigo modelo clássico. Todas as portas de um VM Azure Resource Manager estão abertas desde que:

1. Nenhum Grupo de Segurança de Rede é definido para a sub-rede ou a interface de rede. O tráfego de rede para VMs Azure pode ser assegurado através dos chamados "Grupos de Segurança da Rede". Para obter mais informações, consulte o artigo [O que é um Grupo de Segurança de Rede (NSG)?][virtual-networks-nsg]
2. Nenhum balanceador de carga Azure está definido para a interface de rede

Veja a diferença de arquitetura entre modelo clássico e ARM como descrito [neste artigo.][virtual-machines-azure-resource-manager-architecture]

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Configuração do Sistema SAP e conectividade SAP GUI através da internet

Consulte este artigo, que descreve detalhes sobre este tópico:

<https://docs.microsoft.com/archive/blogs/saponsqlserver/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure>

#### <a name="changing-firewall-settings-within-vm"></a>Alterar definições de firewall dentro de VM

Pode ser necessário configurar a firewall das suas máquinas virtuais para permitir o tráfego de entrada no seu sistema SAP.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Por predefinição, o Windows Firewall dentro de um VM implantado Azure é ligado. Agora é necessário permitir a abertura da Porta SAP, caso contrário o SAP GUI não poderá ligar-se.
> Para efetuar este procedimento:
>
> * Painel de controlo aberto\Sistema e Segurança\Firewall do Windows para **Definições Avançadas** .
> * Agora clique à direita nas Regras de Entrada e escolha **Nova Regra** .
> * No seguinte Feiticeiro optou por criar uma nova regra **do Porto.**
> * No próximo passo do assistente, deixe a definição em TCP e escreva o número de porta que pretende abrir. Como a nossa identificação de caso SAP é 00, tomámos 3200. Se o seu caso tiver um número de instância diferente, a porta que definiu anteriormente com base no número de instância deve ser aberta.
> * Na parte seguinte do assistente, tem de deixar o item Permitir a **ligação** verificada.
> * No próximo passo do assistente é necessário definir se a regra se aplica à rede Domain, Private and Public. Ajuste-o, se necessário, às suas necessidades. No entanto, ligando-se ao SAP GUI de fora através da rede pública, é necessário que a regra seja aplicada à rede pública.
> * No último passo do assistente, nomeie a regra e guarde premindo **Finish** .
>
> A regra torna-se eficaz imediatamente.
>
> ![Definição da regra do porto][planning-guide-figure-1600]
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> As imagens Linux no Azure Marketplace não permitem por defeito a firewall dos iptables e a ligação ao seu sistema SAP deve funcionar. Se tiver ativado iptables ou outra firewall, consulte a documentação dos iptables ou a firewall usada para permitir o tráfego de entrada da TCP para a porta 32xx (onde xx é o número do sistema do seu sistema SAP).
>
>

---
#### <a name="security-recommendations"></a>Recomendações de segurança

O SAP GUI não se liga imediatamente a nenhuma das instâncias SAP (porta 32xx) que estão em funcionamento, mas liga-se primeiro através da porta aberta ao processo do servidor de mensagens SAP (porta 36xx). No passado, a mesma porta foi utilizada pelo servidor de mensagens para a comunicação interna às instâncias de aplicação. Para evitar que os servidores de aplicações no local se comuniquem inadvertidamente com um servidor de mensagens em Azure, as portas de comunicação internas podem ser alteradas. É altamente recomendado alterar a comunicação interna entre o servidor de mensagens SAP e as suas instâncias de aplicação para um número de porta diferente em sistemas que foram clonados de sistemas no local, como um clone de desenvolvimento para testes de projetos, etc. Isto pode ser feito com o parâmetro de perfil padrão:

> rdisp/msserv_internal
>
>

como documentado em [Definições de Segurança para o Servidor de Mensagens SAP](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="single-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>VM único com cenário de demonstração/treino SAP NetWeaver

![Executando sistemas de demonstração VM SAP com os mesmos nomes VM, isolados nos Serviços de Nuvem Azure][planning-guide-figure-1700]

Neste cenário estamos a implementar um cenário típico de sistema de treino/demonstração onde o cenário completo de treino/demonstração está contido num único VM. Assumimos que a implementação é feita através de modelos de imagem VM. Assumimos também que vários destes VMs de demonstração/treinos precisam de ser implantados com os VMs com o mesmo nome. Todos os sistemas de treino não têm conectividade com os seus ativos no local e são opostos a uma implantação híbrida.

O pressuposto é que criou uma Imagem VM como descrito em algumas secções do capítulo [Preparando VMs com SAP para Azure][planning-guide-5.2] neste documento.

A sequência de eventos para implementar o cenário é assim:

##### <a name="powershell"></a>PowerShell

* Criar um novo grupo de recursos para cada formação/demo paisagista

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```

* Crie uma nova conta de armazenamento se não quiser utilizar Discos Geridos

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Crie uma nova rede virtual para cada formação/demo paisagens para permitir a utilização do mesmo nome de hospedeiro e endereços IP. A rede virtual está protegida por um Grupo de Segurança de Rede que apenas permite o tráfego para a porta 3389 para permitir o acesso remoto do Ambiente de Trabalho e a porta 22 para SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Criar um novo endereço IP público que pode ser usado para aceder à máquina virtual a partir da internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Cria uma máquina virtual. Para este cenário, cada VM terá o mesmo nome. O SAP SID dos casos SAP NetWeaver nesses VMs também será o mesmo. Dentro do Grupo de Recursos Azure, o nome do VM tem de ser único, mas em diferentes Grupos de Recursos Azure pode executar VMs com o mesmo nome. A conta 'Administrador' padrão do Windows ou 'raiz' do Linux não é válida. Portanto, um novo nome de utilizador do administrador precisa de ser definido juntamente com uma palavra-passe. O tamanho do VM também precisa de ser definido.

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

* Opcionalmente adicione discos adicionais e restaure o conteúdo necessário. Todos os nomes blob (URLs para as bolhas) devem ser únicos dentro de Azure.

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

O seguinte código de exemplo pode ser utilizado no Linux. Para o Windows, utilize o PowerShell como descrito acima ou adapte o exemplo para utilizar %rgName% em vez de $rgName e definir a variável ambiental utilizando o *conjunto* de comandos Windows .

* Criar um novo grupo de recursos para cada formação/demo paisagista

```azurecli
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Criar uma nova conta de armazenamento

```azurecli
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Crie uma nova rede virtual para cada formação/demo paisagens para permitir a utilização do mesmo nome de hospedeiro e endereços IP. A rede virtual está protegida por um Grupo de Segurança de Rede que apenas permite o tráfego para a porta 3389 para permitir o acesso remoto do Ambiente de Trabalho e a porta 22 para SSH.

```azurecli
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Criar um novo endereço IP público que pode ser usado para aceder à máquina virtual a partir da internet

```azurecli
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```azurecli
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Cria uma máquina virtual. Para este cenário, cada VM terá o mesmo nome. O SAP SID dos casos SAP NetWeaver nesses VMs também será o mesmo. Dentro do Grupo de Recursos Azure, o nome do VM tem de ser único, mas em diferentes Grupos de Recursos Azure pode executar VMs com o mesmo nome. A conta 'Administrador' padrão do Windows ou 'raiz' do Linux não é válida. Portanto, um novo nome de utilizador do administrador precisa de ser definido juntamente com uma palavra-passe. O tamanho do VM também precisa de ser definido.

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

* Opcionalmente adicione discos adicionais e restaure o conteúdo necessário. Todos os nomes blob (URLs para as bolhas) devem ser únicos dentro de Azure.

```azurecli
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Modelo

Pode utilizar os modelos de amostra no repositório de modelos Azure-quickstart no GitHub.

* [Linux VM simples](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Janelas simples VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VM da imagem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementar um conjunto de VMs que comunicam dentro do Azure

Este cenário não-híbrido é um cenário típico para fins de treino e demonstração onde o software que representa o cenário de demonstração/treino está espalhado por vários VMs. Os diferentes componentes instalados nos diferentes VMs precisam de comunicar uns com os outros. Mais uma vez, neste cenário, não é necessário qualquer cenário de comunicação de rede no local ou de instalações cruzadas.

Este cenário é uma extensão da instalação descrita no capítulo [VM único com o cenário de demonstração/formação SAP NetWeaver][planning-guide-7.1] deste documento. Neste caso, mais máquinas virtuais serão adicionadas a um grupo de recursos existente. No exemplo seguinte, o panorama de formação consiste num SAP ASCS/SCS VM, um VM que executa um DBMS e um VM de aplicação SAP.

Antes de construir este cenário, precisa pensar em configurações básicas como já exercidas no cenário anterior.

#### <a name="resource-group-and-virtual-machine-naming"></a>Nomeação de grupo de recursos e máquina virtual

Todos os nomes de grupos de recursos devem ser únicos. Desenvolva o seu próprio esquema de nomeação dos seus recursos, como `<rg-name`>-sufixo.

O nome da máquina virtual tem de ser único dentro do grupo de recursos.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Criar rede para comunicação entre os diferentes VMs

![Conjunto de VMs dentro de uma rede virtual Azure][planning-guide-figure-1900]

Para evitar o nome de colisões com clones das mesmas paisagens de treino/demonstração, é necessário criar uma Rede Virtual Azure para cada paisagem. A resolução de nomes DNS será fornecida pela Azure ou pode configurar o seu próprio servidor DNS fora de Azure (para não ser discutido mais adiante aqui). Neste cenário, não configuramos o nosso próprio DNS. Para todas as máquinas virtuais dentro de uma Rede Virtual Azure, a comunicação através de hostnames será ativada.

As razões para separar as paisagens de formação ou de demonstração por redes virtuais e não apenas grupos de recursos podem ser:

* A paisagem SAP tal como configurada precisa do seu próprio AD/OpenLDAP e um Servidor de Domínio precisa de fazer parte de cada uma das paisagens.
* A paisagem SAP tal como configurada tem componentes que precisam de trabalhar com endereços IP fixos.

Mais detalhes sobre as Redes Virtuais Azure e como defini-las podem ser encontrados [neste artigo.][virtual-networks-create-vnet-arm-pportal]

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Implantação de VMs SAP com conectividade de rede corporativa (Cross-Premis)

Você tem uma paisagem SAP e quer dividir a implementação entre metal nu para servidores DBMS de alta qualidade, ambientes virtualizados no local para camadas de aplicação, e sistemas SAP configurados de 2 camadas mais pequenos e Azure IaaS. A suposição base é que os sistemas SAP dentro de uma paisagem SAP precisam de comunicar uns com os outros e com muitos outros componentes de software implantados na empresa, independentemente da sua forma de implantação. Também não deverá haver diferenças introduzidas pelo formulário de implantação para o utilizador final que se conecta com o SAP GUI ou outras interfaces. Estas condições só podem ser satisfeitas quando tivermos os serviços ative directy/OpenLDAP e DNS estendidos aos sistemas Azure através da conectividade site-local/multi-site ou ligações privadas como a Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Cenário de uma paisagem SAP

As premissas cruzadas ou o cenário híbrido podem ser descritos aproximadamente como nos gráficos abaixo:

![Conectividade local-local entre os ativos no local e a Azure][planning-guide-figure-2100]

O requisito mínimo é a utilização de protocolos de comunicação seguros, tais como SSL/TLS para acesso ao navegador ou ligações baseadas em VPN para acesso ao sistema aos serviços Azure. O pressuposto é que as empresas lidam com a ligação VPN entre a sua rede corporativa e a Azure de forma diferente. Algumas empresas podem abrir em branco todos os portos. Algumas outras empresas podem querer ser precisas em que portos precisam de abrir, etc.

Na tabela abaixo estão listadas as portas de comunicação típicas DO SAP. Basicamente, é suficiente abrir a porta de entrada SAP.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Serviço | Nome da Porta | Exemplo `<nn`> = 01 | Intervalo predefinido (min-max) | Comentário |
| --- | --- | --- | --- | --- |
| Despachante |sapdp `<nn>` ver * |3201 |3200 - 3299 |Despacho SAP, usado pelo SAP GUI para Windows e Java |
| Servidor de mensagens |seivas `<sid`> ver ** |3600 |seivas grátis`<anySID`> |sid = SAP-System-ID |
| Gateway |sapgw `<nn`> ver * |3301 |livre |Gateway SAP, utilizado para comunicação CPIC e RFC |
| Router SAP |sapdp99 |3299 |livre |Apenas os nomes de serviço ci (instância central) podem ser reatribuídos em /etc/serviços a um valor arbitrário após a instalação. |

*) nn = Número de instância SAP

**) sid = SAP-System-ID

Informações mais detalhadas sobre os portos necessários para diferentes produtos ou serviços SAP por produtos SAP podem ser consultados <https://scn.sap.com/docs/DOC-17124> aqui.
Com este documento, deverá ser capaz de abrir portas dedicadas no dispositivo VPN necessário para produtos e cenários SAP específicos.

Outras medidas de segurança ao implementar VMs neste cenário poderiam ser a criação de um [Grupo de Segurança da Rede][virtual-networks-nsg] para definir as regras de acesso.



#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Impressão em uma impressora de rede local a partir de caso SAP em Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Impressão sobre TCP/IP em cenário de Cross-Premis

A configuração das suas impressoras de rede baseadas em TCP/IP num VM Azure é globalmente a mesma que na sua rede corporativa, assumindo que tem um túnel VPN Site-To-Site ou ligação ExpressRoute estabelecida.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Para efetuar este procedimento:
>
> * Algumas impressoras de rede vêm com um assistente de configuração que facilita a configuração da sua impressora num VM Azure. Se nenhum software de assistente tiver sido distribuído com a impressora, a forma manual de configurar a impressora é criar uma nova porta de impressora TCP/IP.
> * Painel de controlo aberto -dispositivos > e impressoras -> Adicionar uma impressora
> * Escolha Adicionar uma impressora utilizando um endereço TCP/IP ou nome de anfitrião
> * Digite o endereço IP da impressora
> * Impressora Porta 9100
> * Se necessário, instale manualmente o controlador da impressora apropriado.
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> * como para o Windows basta seguir o procedimento padrão para instalar uma impressora de rede
> * basta seguir os guias públicos do Linux para [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) ou [Red Hat e Oracle Linux](https://access.redhat.com/documentation/red_hat_enterprise_linux/6/html/deployment_guide/sec-printer_configuration) sobre como adicionar uma impressora.
>
>

---
![Impressão de rede][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Impressora baseada em anfitrião sobre SMB (impressora partilhada) em cenário de Cross-Premis

As impressoras baseadas no anfitrião não são compatíveis com a rede por design. Mas uma impressora baseada em anfitriões pode ser partilhada entre computadores numa rede, desde que a impressora esteja ligada a um computador alimentado. Ligue a sua rede corporativa no Site-To-Site ou no ExpressRoute e partilhe a sua impressora local. O protocolo SMB utiliza o NetBIOS em vez de DNS como serviço de nome. O nome de anfitrião netbios pode ser diferente do nome de anfitrião DNS. O caso padrão é que o nome de anfitrião NetBIOS e o nome de anfitrião DNS são idênticos. O domínio DNS não faz sentido no espaço do nome NetBIOS. Assim, o nome de anfitrião DNS totalmente qualificado, constituído pelo nome de anfitrião DNS e pelo domínio DNS, não deve ser utilizado no espaço de nome NetBIOS.

A partilha da impressora é identificada por um nome único na rede:

* Nome de anfitrião do anfitrião SMB (sempre necessário).
* Nome da parte (sempre necessária).
* Nome do domínio se a partilha da impressora não estiver no mesmo domínio que o sistema SAP.
* Além disso, um nome de utilizador e uma palavra-passe podem ser necessários para aceder à partilha da impressora.

Como:

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Partilhe a sua impressora local.
> No Azure VM, abra o Explorador do Windows e escreva o nome de partilha da impressora.
> Um assistente de instalação da impressora irá guiá-lo através do processo de instalação.
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> Aqui estão alguns exemplos de documentação sobre configurar impressoras de rede em Linux ou incluir um capítulo sobre impressão em Linux. Funcionará da mesma forma num VM Azure Linux enquanto o VM fazer parte de uma VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL ou Oracle Linux <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/system_administrators_guide/index#sec-Starting_Print_Settings_Config>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>Impressora USB (encaminhamento da impressora)

No Azure, não está disponível a capacidade dos Serviços de Desktop Remoto de fornecer aos utilizadores o acesso aos seus dispositivos de impressora local numa sessão remota.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Mais detalhes sobre a impressão com o Windows podem ser consultados aqui: <https://technet.microsoft.com/library/jj590748.aspx> .
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integração dos Sistemas SAP Azure no Sistema de Correção e Transportes (TMS) em instalações cruzadas

O Sistema de Alteração e Transporte sap (TMS) tem de ser configurado para exportar e importar pedidos de transporte através de sistemas na paisagem. Assumimos que os casos de desenvolvimento de um sistema SAP (DEV) estão localizados em Azure, enquanto que a garantia de qualidade (QA) e os sistemas produtivos (PRD) estão no local. Além disso, assumimos que existe um diretório central de transportes.

##### <a name="configuring-the-transport-domain"></a>Configurar o Domínio dos Transportes

Configure o seu Domínio de Transporte no sistema designado como Controlador de Domínio de Transporte, conforme descrito na [Configuração do Controlador de Domínio de Transporte](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Será criado um utilizador do sistema TMSADM e será gerado o destino RFC necessário. Pode verificar estas ligações RFC utilizando a transação SM59. A resolução do nome hospedeiro deve ser ativada em todo o seu domínio de transporte.

Como:

* No nosso cenário, decidimos que o sistema QAS no local será o controlador de domínio CTS. Transação de chamada STMS. Aparece a caixa de diálogo TMS. É apresentada uma caixa de diálogo de domínio de transporte configurado. (Esta caixa de diálogo só aparece se ainda não tiver configurado um domínio de transporte.)
* Certifique-se de que o utilizador TMSADM, criado automaticamente, é autorizado (SM59 -> ABAP Connection -> TMSADM@E61.DOMAIN_E61 -> Detalhes -> Utilities(M) -> De acesso. O ecrã inicial de transação STMS deve mostrar que este Sistema SAP está agora a funcionar como o controlador do domínio de transporte, tal como mostrado aqui:

![Tela inicial de TRANSAÇÕES STMS no controlador de domínio][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Incluindo sistemas SAP no Domínio dos Transportes

A sequência de inclusão de um sistema SAP num domínio de transporte é a seguinte:

* No sistema DEV em Azure, vá ao sistema de transporte (Cliente 000) e ligue para a transação STMS. Escolha Outra Configuração na caixa de diálogo e continue com incluir o Sistema em Domínio. Especificar o Controlador de Domínio como anfitrião-alvo[(Incluindo sistemas SAP no Domínio de Transporte).](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm) O sistema está agora à espera de ser incluído no domínio dos transportes.
* Por razões de segurança, terá de voltar ao controlador de domínio para confirmar o seu pedido. Escolha a visão geral do sistema e aprove o sistema de espera. Em seguida, confirme a solicitação e a configuração será distribuída.

Este sistema SAP contém agora as informações necessárias sobre todos os outros sistemas SAP no domínio dos transportes. Ao mesmo tempo, os dados de endereço do novo sistema SAP são enviados para todos os outros sistemas SAP, e o sistema SAP é inserido no perfil de transporte do programa de controlo de transportes. Verifique se os RFCs e o acesso ao diretório de transportes do trabalho de domínio.

Continue com a configuração do seu sistema de transporte como de costume, como descrito na documentação [Alterar e Transporte Sistema.](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)

Como:

* Certifique-se de que o seu STMS no local está configurado corretamente.
* Certifique-se de que o nome de anfitrião do Controlador de Domínio de Transporte pode ser resolvido pela sua máquina virtual em Azure e visto de vice.
* Transação de chamada STMS -> Outra configuração -> Incluir sistema em domínio.
* Confirme a ligação no sistema TMS nas instalações.
* Configure rotas de transporte, grupos e camadas como de costume.

Em cenários de instalações interligados site-to-site, a latência entre as instalações e Azure ainda pode ser substancial. Se seguirmos a sequência de transporte de objetos através de sistemas de desenvolvimento e teste para a produção ou pensarmos na aplicação de transportes ou pacotes de apoio para os diferentes sistemas, percebemos que, dependendo da localização do diretório de transportes centrais, alguns dos sistemas encontrarão dados de leitura ou de escrita de alta latência no diretório de transportes centrais. A situação é semelhante às configurações paisagísticas do SAP onde os diferentes sistemas são espalhados por diferentes centros de dados com uma distância substancial entre os centros de dados.

Para trabalhar em torno de tal latência e fazer com que os sistemas funcionem rapidamente na leitura ou escrita de ou para o diretório de transportes, você pode configurar dois domínios de transporte STMS (um para as instalações e outro com os sistemas em Azure e ligar os domínios de transporte. Consulte esta documentação, que explica os princípios subjacentes a este conceito no SAP TMS: <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm> .

Como:

* Criar um domínio de transporte em cada local (no local e Azure) utilizando a transação STMS <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Ligue os domínios a uma ligação de domínio e confirme a ligação entre os dois domínios.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribua a configuração para o sistema ligado.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Tráfego RFC entre instâncias SAP localizadas em Azure e no local (Cross-Premis)

O tráfego de RFC entre sistemas, que estão no local e em Azure, tem de funcionar. Para configurar uma transação de chamada de ligação SM59 num sistema de origem onde é necessário definir uma ligação RFC para o sistema alvo. A configuração é semelhante à configuração padrão de uma Ligação RFC.

Assumimos que no cenário transversal, os VMs, que executam sistemas SAP que precisam de comunicar uns com os outros estão no mesmo domínio. Por conseguinte, a configuração de uma ligação RFC entre sistemas SAP não difere das etapas de configuração e entradas em cenários no local.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Acesso a partilhas de ficheiros locais a partir de instâncias SAP localizadas em Azure ou vice-versa

Os casos da SAP localizados em Azure precisam de aceder a ações de ficheiros, que se encontram dentro das instalações da empresa. Além disso, as instâncias SAP no local precisam de aceder a ações de ficheiros, que estão localizadas em Azure. Para ativar as ações dos ficheiros, tem de configurar as permissões e as opções de partilha no sistema local. Certifique-se de que abre as portas da ligação VPN ou ExpressRoute entre a Azure e o seu datacenter.

## <a name="supportability"></a>Suportabilidade

### <a name="azure-extension-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Extensão Azure para SAP

A fim de alimentar uma parte da informação da infraestrutura Azure dos sistemas DE SAP críticos da missão às instâncias do Agente anfitrião SAP, instaladas em VMs, uma extensão Azure (VM) para SAP precisa de ser instalada para os VMs implantados. Uma vez que as exigências da SAP eram específicas das aplicações SAP, a Microsoft decidiu não implementar genericamente a funcionalidade necessária no Azure, mas deixou-a para os clientes implementarem a necessária extensão e configurações de VM para as suas Máquinas Virtuais em execução em Azure. No entanto, a implantação e gestão do ciclo de vida da Extensão Azure VM para SAP será maioritariamente automatizada pela Azure.

#### <a name="solution-design"></a>Design de solução

A solução desenvolvida para permitir que o Agente Anfitrião SAP ocupe as informações necessárias baseia-se na arquitetura do quadro de agente e extensão Azure VM. A ideia do quadro de agente e extensão Azure VM é permitir a instalação de aplicações de software disponíveis na galeria de extensão Azure VM dentro de um VM. A ideia principal por trás deste conceito é permitir (em casos como a Extensão Azure para SAP), a implementação de funcionalidades especiais num VM e a configuração de tal software no momento da implementação.

O 'Agente VM Azure' que permite o manuseamento de extensões VM específicas do Azure no âmbito do VM é injetado em VMs do Windows por padrão na criação de VM no portal Azure. No caso de SUSE, Red Hat ou Oracle Linux, o agente VM já faz parte da imagem do Azure Marketplace. No caso de um carregamento de um Linux VM de instalações para Azure, o agente VM tem de ser instalado manualmente.

Os blocos básicos de construção da solução para fornecer informações sobre infraestruturas Azure ao agente hospedeiro SAP em Azure são assim:

![Componentes de extensão microsoft Azure][planning-guide-figure-2400]

Como mostrado no diagrama de bloco acima, uma parte da solução está hospedada na Azure VM Image e Azure Extension Gallery, que é um repositório globalmente replicado que é gerido pela Azure Operations. É da responsabilidade da equipa conjunta SAP/MS trabalhar na implementação do Azure da SAP para trabalhar com a Azure Operations para publicar novas versões da Extensão Azure para SAP.

Quando implementa um novo Windows VM, o Agente VM Azure é automaticamente adicionado ao VM. A função deste agente é coordenar o carregamento e configuração das Extensões Azure dos VMs. Para os VMs Linux, o Agente VM Azure já faz parte da imagem do Azure Marketplace OS.

No entanto, há um passo que ainda precisa de ser executado pelo cliente. Esta é a ativação e configuração da coleção de desempenho. O processo relacionado com a configuração é automatizado por um script PowerShell ou comando CLI. O script PowerShell pode ser descarregado no Microsoft Azure Script Center, conforme descrito no [Guia de Implementação][deployment-guide].

A arquitetura geral da extensão Azure para SAP parece:

![Extensão Azure para SAP ][planning-guide-figure-2500]

**Para saber exatamente como fazer e para passos detalhados da utilização destes cmdlets PowerShell ou comando CLI durante as implementações, siga as instruções dadas no [Guia de Implantação][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integração do Azure localizada em caso SAP em SAProuter

Os casos sap em execução em Azure também precisam de ser acessíveis a partir do SAProuter.

![Ligação de rede SAP-Router][planning-guide-figure-2600]

Um SAProuter permite a comunicação TCP/IP entre os sistemas participantes se não houver ligação IP direta. Isto proporciona a vantagem de que não é necessária qualquer ligação de ponta a ponta entre os parceiros de comunicação a nível da rede. O SAProuter está a ouvir a porta 3299 por defeito.
Para ligar as instâncias SAP através de um SAProuter, é necessário dar a cadeia SAProuter e o nome do anfitrião a qualquer tentativa de ligação.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java

Até agora, o foco do documento tem sido o SAP NetWeaver em geral ou a pilha SAP NetWeaver ABAP. Nesta pequena secção, estão listadas considerações específicas para a pilha SAP Java. Uma das aplicações mais importantes da SAP NetWeaver Java exclusivamente baseadas é o Portal da Empresa SAP. Outras aplicações baseadas em SAP NetWeaver, como SAP PI e SAP Solution Manager, utilizam as pilhas SAP NetWeaver ABAP e Java. Portanto, é certamente necessário considerar aspetos específicos relacionados com a pilha SAP NetWeaver Java também.

### <a name="sap-enterprise-portal"></a>Portal da Empresa SAP

A configuração de um Portal SAP numa Máquina Virtual Azure não difere de uma instalação nas instalações se estiver a ser implantada em cenários de instalações cruzadas. Uma vez que o DNS é feito por instalações, as definições de porta das instâncias individuais podem ser feitas como configuradas no local. As recomendações e restrições descritas neste documento até agora aplicam-se a uma aplicação como o SAP Enterprise Portal ou a pilha SAP NetWeaver Java em geral.

![Portal SAP exposto][planning-guide-figure-2700]

Um cenário especial de implantação por parte de alguns clientes é a exposição direta do Portal da Empresa SAP à Internet enquanto o anfitrião de máquinas virtuais está ligado à rede da empresa através do túnel VPN local ou ExpressRoute. Para tal cenário, tem de se certificar de que portas específicas estão abertas e não bloqueadas por firewall ou grupo de segurança de rede.

O portal inicial URI é http(s): `<Portalserver`>:5XX00/irj onde a porta é formada como documentado pela SAP em <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm> .

![Configuração do ponto final][planning-guide-figure-2800]

Se pretender personalizar o URL e/ou portas do seu Portal da Empresa SAP, consulte esta documentação:

* [Alterar URL do Portal](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Alterar números de porta predefinidos, números de porta do Portal](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Alta Disponibilidade (HA) e Recuperação de Desastres (DR) para SAP NetWeaver em execução em máquinas virtuais Azure

### <a name="definition-of-terminologies"></a>Definição de terminologias

O termo **alta disponibilidade (HA)** está geralmente relacionado com um conjunto de tecnologias que minimizam as perturbações de TI, fornecendo a continuidade do negócio dos serviços de TI através de componentes redundantes, tolerantes a falhas ou falhas no interior do **mesmo** centro de dados. No nosso caso, dentro de uma região de Azure.

**A recuperação de desastres (DR)** também tem como alvo a minimização da perturbação dos serviços de TI, e a sua recuperação, mas em **diferentes** centros de dados, que normalmente estão localizados a centenas de quilómetros de distância. No nosso caso, geralmente entre diferentes Regiões do Azure dentro da mesma região geopolítica ou como estabelecido por si como cliente.

### <a name="overview-of-high-availability"></a>Visão geral da Alta Disponibilidade

Podemos separar a discussão sobre a alta disponibilidade do SAP em Azure em duas partes:

* **Azure infraestrutura alta disponibilidade** , por exemplo HA de computação (VMs), rede, armazenamento etc. e seus benefícios para aumentar a disponibilidade de aplicações SAP.
* **Aplicação SAP alta disponibilidade** , por exemplo HA de componentes de software SAP:
  * Servidores de aplicações SAP
  * Caso SAP ASCS/SCS
  * Servidor DB

e como pode ser combinado com a infraestrutura Azure HA.

A ALTA Disponibilidade sap em Azure tem algumas diferenças em comparação com a ALTA Disponibilidade SAP em um ambiente físico ou virtual no local. O seguinte artigo da SAP descreve configurações padrão de alta disponibilidade SAP em ambientes virtualizados no Windows: <https://scn.sap.com/docs/DOC-44415> . Não existe nenhuma configuração SAP-HA integrada para o Linux como existe para o Windows. No que diz respeito ao SAP HA no local para o Linux, encontre mais informações aqui: <https://scn.sap.com/docs/DOC-8541> .

### <a name="azure-infrastructure-high-availability"></a>Alta Disponibilidade da Infraestrutura Azure

Existe atualmente um SLA único de 99,9%. Para ter uma ideia de como a disponibilidade de um único VM pode parecer, você pode construir o produto dos diferentes Azure SLAs disponíveis: <https://azure.microsoft.com/support/legal/sla/> .

A base para o cálculo é de 30 dias por mês, ou seja, 43200 minutos. Assim, o tempo de inatividade de 0,05% corresponde a 21,6 minutos. Como de costume, a disponibilidade dos diferentes serviços multiplicar-se-á da seguinte forma:

(Serviço de Disponibilidade #1/100) * (Serviço de Disponibilidade #2/100) * (Serviço de Disponibilidade #3/100)

Como:

(99.95/100) * (99.9/100) * (99.9/100) = 0,9975 ou uma disponibilidade global de 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Máquina virtual (VM) Alta Disponibilidade

Existem dois tipos de eventos da plataforma Azure que podem afetar a disponibilidade das suas máquinas virtuais: manutenção planeada e manutenção não planeada.

* Os eventos de manutenção planeada são atualizações periódicas feitas pela Microsoft na plataforma Azure subjacente para melhorar a fiabilidade, o desempenho e a segurança gerais da infraestrutura da plataforma na qual as suas máquinas virtuais são executadas.
* Os eventos de manutenção não planeada ocorrem quando o hardware ou a infraestrutura física subjacente à máquina virtual falharam de alguma forma. Isto pode incluir falhas de rede local, falhas de disco local ou outras falhas estruturais. Quando tal falha for detetada, a plataforma Azure migrará automaticamente a sua máquina virtual do servidor físico pouco saudável que hospeda a sua máquina virtual para um servidor físico saudável. Estes eventos são raros, mas também podem provocar o reinício da máquina virtual.

Para mais detalhes, consulte [disponibilidade de máquinas virtuais do Windows em Azure](../../manage-availability.md) e [Disponibilidade de máquinas virtuais Linux em Azure.](../../manage-availability.md)

#### <a name="azure-storage-redundancy"></a>Redundância de armazenamento Azure

Os dados na sua Conta de Armazenamento Microsoft Azure são sempre replicados para garantir a durabilidade e alta disponibilidade, atendendo ao SLA de armazenamento Azure mesmo face a falhas de hardware transitórios.

Uma vez que o Azure Storage está a manter três imagens dos dados por padrão, RAID5 ou RAID1 em vários discos Azure não são necessários.

Para mais detalhes, consulte [a redundância do Azure Storage](../../../storage/common/storage-redundancy.md).

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Utilizando o reinício da infraestrutura Azure VM para alcançar uma maior disponibilidade de aplicações SAP

Se decidir não utilizar funcionalidades como o Windows Server Failover Clustering (WSFC) ou o Pacemaker no Linux (atualmente apenas suportado para SLES 12 ou superior), o Azure VM Restart é utilizado para proteger um Sistema SAP contra o tempo de paragem planeado e não planeado da infraestrutura do servidor físico Azure e da plataforma Azure em geral subjacente.

> [!NOTE]
> É importante mencionar que o Azure VM Restart protege principalmente as aplicações VMs e NOT. O VM Restart não oferece uma elevada disponibilidade para aplicações SAP, mas oferece um certo nível de disponibilidade de infraestrutura e, portanto, indiretamente maior disponibilidade de sistemas SAP. Também não há SLA durante o tempo que levará a reiniciar um VM após uma paragem planeada ou não planeada do hospedeiro. Portanto, este método de elevada disponibilidade não é adequado para componentes críticos de um sistema SAP como (A)SCS ou DBMS.
>
>

Outro elemento importante da infraestrutura para a alta disponibilidade é o armazenamento. Por exemplo, o Azure Storage SLA tem uma disponibilidade de 99,9 %. Se se implantar todos os VMs com os seus discos numa única Conta de Armazenamento Azure, a indisponibilidade potencial do Armazenamento Azure causará indisponibilidade de todos os VMs colocados nessa Conta de Armazenamento Azul, bem como de todos os componentes SAP que estão dentro desses VMs.

Em vez de colocar todos os VMs numa única Conta de Armazenamento Azure, também pode utilizar contas de armazenamento dedicadas para cada VM, e desta forma aumentar a disponibilidade global de aplicações VM e SAP utilizando várias contas independentes de armazenamento de Azure.

Os discos geridos Azure são automaticamente colocados no domínio de falha da máquina virtual a que estão ligados. Se colocar duas máquinas virtuais num conjunto de disponibilidade e utilizar Discos Geridos, a plataforma também terá o cuidado de distribuir os Discos Geridos em diferentes Domínios de Falha. Se pretender utilizar o Armazenamento Premium, recomendamos vivamente a utilização de Discos De gestão também.

Uma arquitetura de amostra de um sistema SAP NetWeaver que usa a infraestrutura Azure HA e contas de armazenamento poderia ser assim:

![Diagrama que mostra um sistema SAP NetWeaver que utiliza a infraestrutura Azure HA e contas de armazenamento.][planning-guide-figure-2900]

Uma arquitetura de amostra de um sistema SAP NetWeaver que usa a infraestrutura Azure HA e Discos Geridos poderia ser assim:

![Utilizando a infraestrutura Azure HA para alcançar a aplicação SAP de maior disponibilidade][planning-guide-figure-2901]

Para componentes SAP críticos, conseguimos o seguinte até agora:

* Alta disponibilidade de servidores de aplicações SAP (AS)

  As instâncias do servidor de aplicações SAP são componentes redundantes. Cada instância SAP AS é implantada no seu próprio VM, que está a ser executada num domínio diferente de Falha e Atualização de Azure (ver [capítulos Domínios de falha][planning-guide-3.2.1] e [domínios de atualização).][planning-guide-3.2.2] Isto é assegurado utilizando conjuntos de disponibilidade de Azure (ver conjuntos de disponibilidade do capítulo [Azure).][planning-guide-3.2.3] A potencial indisponibilidade planeada ou não planeada de um domínio de falha ou de upgrade de Azure causará indisponibilidade de um número restrito de VMs com as suas instâncias SAP AS.

  Cada instância SAP AS é colocada na sua própria conta de Armazenamento Azure - a indisponibilidade potencial de uma Conta de Armazenamento Azure causará indisponibilidade de apenas um VM com a sua instância SAP AS. No entanto, esteja ciente de que existe um limite de Contas de Armazenamento Azure dentro de uma subscrição da Azure. Para garantir o início automático de (A)SCS após o reboot de VM, certifique-se de definir o parâmetro de arranque automático em (A)SCS exemplo perfil de início descrito no capítulo [Usando o Autostart para instâncias SAP][planning-guide-11.5].
  Leia também o capítulo [Alta Disponibilidade para Servidores de Aplicações SAP][planning-guide-11.4.1] para mais detalhes.

  Mesmo que utilize Discos Geridos, esses discos também são armazenados numa Conta de Armazenamento Azure e podem estar indisponíveis em caso de interrupção de armazenamento.

* *Mais alto* Disponibilidade de caso SAP (A)SCS

  Aqui utilizamos o Azure VM Restart para proteger o VM com a instância SAP (A)SCS instalada. No caso de tempo de inatividade planeado ou não planeado de severes Azure, os VMs serão reiniciados noutro servidor disponível. Como mencionado anteriormente, o Azure VM Restart protege principalmente as aplicações VMs e NOT, neste caso, a (A)SCS instance. Através do VM Restart, alcançaremos indiretamente uma maior disponibilidade de caso SAP (A)SCS. Para garantir o início automático de (A)SCS após o reboot de VM, certifique-se de definir o parâmetro de arranque automático em (A)SCS instance start profile descrito no capítulo [Usando o Arranque Automático para instâncias SAP][planning-guide-11.5]. Isto significa que o (A)SCS instância como um único ponto de falha (SPOF) que funciona num único VM será o fator determinante para a disponibilidade de toda a paisagem SAP.

* *Mais alto* Disponibilidade do Servidor DBMS

  Aqui, à semelhança do caso de utilização de instâncias SAP (A)SCS, utilizamos o Azure VM Restart para proteger o VM com o software DBMS instalado, e conseguimos uma maior disponibilidade de software DBMS através do VM Restart.
  O DBMS que funciona num único VM é também um SPOF, e é o fator determinante para a disponibilidade de toda a paisagem SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Alta disponibilidade da aplicação SAP em Azure IaaS

Para alcançar a alta disponibilidade do sistema SAP, precisamos proteger todos os componentes críticos do sistema SAP, por exemplo servidores de aplicações SAP redundantes, e componentes únicos (por exemplo, Single Point of Failure) como o CASO SAP (A)SCS e OBMS.

#### <a name="high-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Alta disponibilidade para servidores de aplicações SAP

Para os servidores/instâncias de diálogo de aplicações SAP, não é necessário pensar numa solução específica de alta disponibilidade. A elevada disponibilidade é conseguida por redundância e, assim, tendo o suficiente em diferentes máquinas virtuais. Todos eles devem ser colocados no mesmo conjunto de disponibilidade de Azure para evitar que os VM possam ser atualizados ao mesmo tempo durante o tempo de paragem de manutenção planeado. A funcionalidade básica, que se baseia em diferentes domínios de upgrade e falha dentro de uma Unidade de Escala Azure já foi introduzida no capítulo ["Upgrade Domains".][planning-guide-3.2.2] Os conjuntos de disponibilidade de Azure foram apresentados no capítulo [Azure Availability Sets][planning-guide-3.2.3] deste documento.

Não existe um número infinito de domínios de falha e de atualização que possam ser utilizados por uma disponibilidade Azure definida dentro de uma Unidade de Escala Azure. Isto significa que colocar uma série de VMs em um conjunto de disponibilidade, mais cedo ou mais tarde mais do que um VM acaba no mesmo Domínio de Falha ou Upgrade.

Implementando algumas instâncias de servidor de aplicações SAP nos seus VMs dedicados e assumindo que temos cinco Domínios de Upgrade, a imagem seguinte surge no final. O número máximo real de domínios de falha e atualização dentro de um conjunto de disponibilidade pode mudar no futuro:

![HA de Servidores de Aplicações SAP em Azure][planning-guide-figure-3000]


#### <a name="high-availability-for-sap-central-services-on-azure"></a>Alta Disponibilidade para Serviços Centrais SAP em Azure

Para obter uma arquitetura de alta disponibilidade dos Serviços Centrais SAP em Azure, consulte o artigo [Arquitetura de alta disponibilidade e cenários para SAP NetWeaver](./sap-high-availability-architecture-scenarios.md) como informação de entrada. O artigo aponta para descrições mais pormenorizadas para os sistemas operativos específicos.

#### <a name="high-availability-for-the-sap-database-instance"></a>Alta Disponibilidade para a caixa de dados sap

A configuração típica DO DBMS HA baseia-se em dois VMS DBMS onde a funcionalidade de alta disponibilidade DBMS é usada para replicar dados da instância DBMS ativa para a segunda VM em uma instância passiva de DBMS.

A funcionalidade de alta disponibilidade e recuperação de desastres para DBMS em geral, bem como dBMS específicos são descritas no Guia de [Implantação do DBMS][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Alta disponibilidade de ponta a ponta para o sistema SAP completo

Aqui estão dois exemplos de uma arquitetura COMPLETA SAP NetWeaver HA em Azure - um para Windows e um para Linux.

Apenas discos não geridos: Os conceitos explicados abaixo podem ter de ser comprometidos um pouco quando implementamos muitos sistemas SAP e o número de VMs implantados estão a exceder o limite máximo de Contas de Armazenamento por subscrição. Nestes casos, os VHDs de VMs devem ser combinados numa única conta de armazenamento. Normalmente, fá-lo-ia combinando VHDs de VMs de camada de aplicação SAP de diferentes sistemas SAP.  Também combinamos VHDs diferentes de diferentes DBMS VMs de diferentes sistemas SAP numa conta de armazenamento Azure. Mantendo assim os limites do IOPS das Contas de Armazenamento Azure em mente ( <https://azure.microsoft.com/documentation/articles/storage-scalability-targets> )


##### <a name="windows-logologo_windows-ha-on-windows"></a>![Logotipo do Windows.][Logo_Windows] HA no Windows

![Diagrama que mostra a aplicação DE NETWeaver HA Architecture com SQL Server em Azure IaaS.][planning-guide-figure-3200]

As seguintes construções Azure são utilizadas para o sistema SAP NetWeaver, para minimizar o impacto por problemas de infraestrutura e remendos de hospedeiros:

* O sistema completo é implantado no Azure (necessário - camada DBMS, (instância A)SCS, e a camada de aplicação completa precisa de ser executada no mesmo local).
* O sistema completo funciona dentro de uma subscrição do Azure (necessária).
* O sistema completo funciona dentro de uma Rede Virtual Azure (necessária).
* A separação dos VMs de um sistema SAP em três conjuntos de disponibilidade é possível mesmo com todos os VM pertencentes à mesma Rede Virtual.
* Cada camada (por exemplo, DBMS, ASCS, Servidores de Aplicações) deve utilizar um conjunto de disponibilidade dedicado.
* Todos os VMs que executam casos DBMS de um sistema SAP estão num conjunto de disponibilidade. Assumimos que há mais de um VM executando instâncias DBMS por sistema, uma vez que são usadas funcionalidades de alta disponibilidade de DBMS nativas, como SQL Server AlwaysOn ou Oracle Data Guard.
* Todos os VM que executam instâncias DBMS usam a sua própria conta de armazenamento. Os dados e ficheiros de registo DBMS são replicados de uma conta de armazenamento para outra conta de armazenamento utilizando funções de alta disponibilidade DBMS que sincronizam os dados. A indisponibilidade de uma conta de armazenamento causará indisponibilidade de um nó de cluster SQL Windows, mas não todo o serviço SQL Server.
* Todos os VMs em execução (A)SCS instância de um sistema SAP estão em um conjunto de disponibilidade. Um Cluster de Falha do Servidor do Windows (WSFC) está configurado dentro desses VMs para proteger a instância (A)SCS.
* Todos os VMs em execução (A)SCS casos usam a sua própria conta de armazenamento. a (A) Os ficheiros de instância SCS e a pasta global SAP são replicados de uma conta de armazenamento para outra conta de armazenamento utilizando a replicação sios DataKeeper. A indisponibilidade de uma conta de armazenamento causará indisponibilidade de um nó de cluster (A)SCS Windows, mas não todo o serviço (A)SCS.
* Todos os VMs que representam a camada do servidor de aplicações SAP estão num terceiro conjunto de disponibilidade.
* Todos os VMs que executam servidores de aplicações SAP usam a sua própria conta de armazenamento. A indisponibilidade de uma conta de armazenamento causará indisponibilidade de um servidor de aplicação SAP, onde outros servidores de aplicações SAP continuam a funcionar.

A figura a seguir ilustrava a mesma paisagem usando Discos Geridos.

![Aplicação SAP NetWeaver HA Arquitetura com Servidor SQL em Azure IaaS][planning-guide-figure-3201]

##### <a name="linux-logologo_linux-ha-on-linux"></a>![Logotipo linux.][Logo_Linux] HA em Linux

A arquitetura para SAP HA em Linux on Azure é basicamente a mesma que para o Windows como descrito acima. Consulte a NOTA SAP [1928533] para obter uma lista de soluções de alta disponibilidade apoiadas.

### <a name="using-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Utilização de Arranque Automático para casos SAP

A SAP ofereceu a funcionalidade para iniciar casos SAP imediatamente após o início do SO dentro do VM. Os passos exatos foram documentados no artigo [1909114]da Base de Conhecimento sapatado. No entanto, a SAP já não recomenda a utilização da definição porque não existe controlo na ordem de caso, assumindo que mais de um VM foi afetado ou vários casos foram per VM. Assumindo um cenário típico de Azure de uma instância de servidor de aplicações SAP num VM e o caso de um único VM eventualmente reiniciado, o Autostart não é crítico e pode ser ativado adicionando este parâmetro:

`Autostart = 1`

No perfil inicial do caso SAP ABAP e/ou Java.

> [!NOTE]
> O parâmetro De arranque Automático também pode ter algumas quedas. Mais detalhadamente, o parâmetro despoleta o início de um caso SAP ABAP ou Java quando o serviço relacionado Windows/Linux da instância é iniciado. É certamente o caso quando o sistema operativo arranca. No entanto, o reinício dos serviços SAP também é uma coisa comum para a funcionalidade DE Gestão de Ciclo de Vida do SOFTWARE SAP, como SUM ou outras atualizações ou atualizações. Estas funcionalidades não estão à espera que um caso seja reiniciado automaticamente. Por isso, o parâmetro De arranque Automático deve ser desativado antes de executar tais tarefas. O parâmetro De arranque Automático também não deve ser utilizado para casos SAP que estejam agrupados, como ASCS/SCS/CI.
>
>

Consulte aqui informações adicionais sobre o arranque automático para casos SAP:

* [Iniciar/Parar o SAP juntamente com o seu Início/Paragem do Servidor Unix](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Início e paragem dos agentes de gestão netWeaver do SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Como ativar o arranque automático da Base de Dados HANA](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>Sistemas SAP de 3 níveis maiores
High-Availability aspetos das configurações DE SAP de 3 níveis já foram discutidos em secções anteriores. Mas e os sistemas onde os requisitos do servidor DBMS são demasiado grandes para o ter localizado em Azure, mas a camada de aplicação SAP poderia ser implantada em Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Localização das configurações SAP de 3 níveis
Não é suportado para dividir o próprio nível de aplicação ou o nível de aplicação e DBMS entre as instalações e o Azure. Um sistema SAP ou está completamente implantado no local ou em Azure. Também não é suportado para ter alguns dos servidores de aplicações executados no local e alguns outros em Azure. Este é o ponto de partida da discussão. Também não estamos a apoiar que os componentes DBMS de um sistema SAP e a camada de servidor de aplicação SAP sejam implantados em duas regiões diferentes do Azure. Por exemplo, a camada de aplicação DBMS nos EUA e SAP no Centro dos EUA. Razão para não suportar tais configurações é a sensibilidade de latência da arquitetura SAP NetWeaver.

No entanto, ao longo do ano passado, os parceiros de data center desenvolveram co-localizações para as Regiões de Azure. Estes co-locais estão muitas vezes próximos dos centros de dados físicos do Azure dentro de uma Região de Azure. A curta distância e ligação dos ativos na co-localização através do ExpressRoute para a Azure pode resultar numa latência inferior a 2 milissegundos. Nesses casos, é possível localizar a camada DBMS (incluindo o armazenamento SAN/NAS) em tal co-localização e a camada de aplicação SAP em Azure. [HANA Grandes Instâncias](./hana-overview-architecture.md).

### <a name="offline-backup-of-sap-systems"></a>Backup offline dos sistemas SAP
Dependendo da configuração SAP escolhida (2-Tier ou 3-Tier) pode haver necessidade de fazer o back up. O conteúdo do próprio VM mais para ter uma cópia de segurança da base de dados. Espera-se que as cópias de segurança relacionadas com o DBMS sejam feitas com métodos de base de dados. Uma descrição detalhada das diferentes bases de dados pode ser encontrada no [Guia DBMS][dbms-guide]. Por outro lado, os dados SAP podem ser apoiados de forma offline (incluindo também o conteúdo da base de dados) tal como descrito nesta secção ou online, conforme descrito na secção seguinte.

A cópia de segurança offline exigiria basicamente o encerramento do VM através do portal Azure e uma cópia do disco VM base mais todos os discos ligados ao VM. Isto preservaria um ponto na imagem do VM e do seu disco associado. Recomenda-se copiar as cópias de segurança numa conta de armazenamento Azure diferente. Daí que o procedimento descrito no capítulo Cópia dos discos entre as Contas de [Armazenamento Azure][planning-guide-5.4.2] deste documento se aplique.


Uma restauração desse estado consistiria em eliminar o VM base, bem como os discos originais do VM base e discos montados, copiando os discos guardados para a Conta de Armazenamento original ou grupo de recursos para discos geridos e, em seguida, redistribuindo o sistema.
Este artigo mostra um exemplo de como escrever este processo em PowerShell: <http://www.westerndevs.com/azure-snapshots/>

Certifique-se de instalar uma nova licença SAP uma vez que restaurar uma cópia de segurança VM, tal como descrito acima, cria uma nova chave de hardware.

### <a name="online-backup-of-an-sap-system"></a>Backup on-line de um sistema SAP

A cópia de segurança do DBMS é realizada com métodos específicos do DBMS, conforme descrito no [Guia DBMS][dbms-guide].

Outros VMs dentro do sistema SAP podem ser apoiados utilizando a funcionalidade de Backup da Máquina Virtual Azure. Azure Virtual Machine Backup é um método padrão para apoiar um VM completo em Azure. A Azure Backup armazena as cópias de segurança em Azure e permite a restauração de um VM novamente.

> [!NOTE]
> A partir de dezembro de 2015 usando VM Backup NÃO mantém o ID VM único que é usado para licenciamento SAP. Isto significa que uma restauração de uma cópia de segurança VM requer a instalação de uma nova chave de licença SAP, uma vez que o VM restaurado é considerado um novo VM e não uma substituição da anterior que foi salva.
>
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Teoricamente, os VMs que executam bases de dados também podem ser suportados de forma consistente se o sistema DBMS suportar o Windows VSS (Volume Shadow Copy <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx> Service), como, por exemplo, o SQL Server o faz.
> No entanto, esteja ciente de que, com base em cópias de segurança Azure VM ponto a tempo, as restaurações das bases de dados não são possíveis. Portanto, a recomendação é realizar backups de bases de dados com funcionalidade DBMS em vez de confiar na Cópia de Segurança VM do Azure.
>
> Para se familiarizar com a Azure Virtual Machine Backup começa aqui: <https://docs.microsoft.com/azure/backup/backup-azure-vms> .
>
> Outras possibilidades são utilizar uma combinação de Microsoft Data Protection Manager instalada num Azure VM e Azure Backup para backup/restaurar bases de dados. Mais informações podem ser encontradas aqui: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction> .
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> Não há equivalente ao Windows VSS em Linux. Portanto, apenas cópias de segurança consistentes com ficheiros são possíveis, mas não cópias de segurança consistentes com aplicações. A cópia de segurança SAP DBMS deve ser feita utilizando a funcionalidade DBMS. O sistema de ficheiros que inclui os dados relacionados com o SAP pode ser guardado, por exemplo, utilizando o alcatrão como descrito aqui: <https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure como local dr para produção paisagens SAP

Desde meados de 2014, as extensões a vários componentes em torno do Hyper-V, System Center e Azure permitem o uso do Azure como dr site para VMs que executam no local com base no Hyper-V.

Um blog que detalha como implementar esta solução está documentado aqui: <https://docs.microsoft.com/archive/blogs/saponsqlserver/protecting-sap-solutions-with-azure-site-recovery> .

## <a name="summary-for-high-availability-for-sap-systems"></a>Resumo para Alta Disponibilidade para sistemas SAP

Os pontos-chave da Alta Disponibilidade para sistemas SAP em Azure são:

* Neste momento, o único ponto de falha da SAP não pode ser protegido exatamente da mesma forma que pode ser feito em implantações no local. A razão é que os clusters de Disco Partilhado ainda não podem ser construídos em Azure sem o uso de software de terceiros.
* Para a camada DBMS, é necessário utilizar a funcionalidade DBMS que não se baseia na tecnologia de cluster de discos partilhados. Os detalhes estão documentados no [Guia DBMS][dbms-guide].
* Para minimizar o impacto de problemas nos domínios de avaria na infraestrutura Azure ou manutenção do hospedeiro, deve utilizar conjuntos de disponibilidade do Azure:
  * Recomenda-se ter um conjunto de disponibilidade para a camada de aplicação SAP.
  * Recomenda-se ter um conjunto de disponibilidade separado para a camada SAP DBMS.
  * Não é aconselhável aplicar o mesmo conjunto de disponibilidade para VMs de diferentes sistemas SAP.
  * Recomenda-se a utilização de Discos Geridos Premium.
* Para efeitos de cópia de segurança da camada DBMS SAP, consulte o [Guia DBMS][dbms-guide].
* O backup das instâncias de diálogo SAP faz pouco sentido, uma vez que é geralmente mais rápido para recolocar instâncias de diálogo simples.
* O backup do VM, que contém o diretório global do sistema SAP e com ele todos os perfis das diferentes instâncias, faz sentido e deve ser realizado com o Windows Backup ou, por exemplo, alcatrão no Linux. Uma vez que existem diferenças entre o Windows Server 2008 (R2) e o Windows Server 2012 (R2), que facilitam o back up utilizando as versões mais recentes do Windows Server, recomendamos executar o Windows Server 2012 (R2) como sistema operativo para hóspedes do Windows.

## <a name="next-steps"></a>Passos seguintes
Leia os artigos:

- [Implementação de máquinas virtuais Azure para SAP NetWeaver](./deployment-guide.md)
- [Considerações para a implantação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](./dbms_guide_general.md)
- [Configurações e operações de infraestrutura do SAP HANA no Azure](/- azure/virtual-machines/workloads/sap/hana-vm-operations)
