---
title: Implantação de Máquinas Virtuais Azure para SAP NetWeaver / Microsoft Docs
description: Aprenda a implementar software SAP em máquinas virtuais Linux em Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: sedusch
ms.openlocfilehash: 7fb87380047d046a580d1ad62b1d7107a94bb297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239893"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Implantação de Máquinas Virtuais Azure para SAP NetWeaver

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
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Implantação de DBMS de Máquinas Virtuais Azure para SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching para VMs e VHDs)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Armazenamento Microsoft Azure)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Estrutura de uma implantação RDBMS)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Elevada disponibilidade e recuperação de desastres com VMs Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e mais tarde)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e lançamentos anteriores)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Usando uma imagem do Servidor SQL do Mercado Azure)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Servidor Geral SQL para SAP no resumo do Azure)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Especificidades para O Servidor SQL RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configuração de armazenamento)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup e restauro)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Considerações de desempenho para backup e restauro)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Outros)
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

[deployment-guide]:deployment-guide.md (Implantação de Máquinas Virtuais Azure para SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Recursos SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Implementação de um VM usando uma imagem personalizada)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Cenário 1: Implantação de um VM do Azure Marketplace para SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Cenário 2: Implementação de um VM com uma imagem personalizada para SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Cenário 3: Mover um VM das instalações utilizando um Azure VHD não generalizado com SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Cenários de implementação de VMs para SAP no Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Implantação de cmdlets Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Descarregue e importe cmdlets PowerShell relevantes para a SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Junte-se a um VM para um domínio no local - apenas windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Descarregue, instale e ative o Agente Azure VM)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configure a extensão Azure para SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Verificação de prontidão para extensão azure para SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Verificação de saúde da extensão Azure para configuração SAP)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Resolução de problemas extensão azure para SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configurar extensão VM)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure o proxy)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Verificações e resolução de problemas para recolha de dados de ponta a ponta para agente hospedeiro SAP)

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

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (Planeamento e implementação de Máquinas Virtuais Azure para SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Recursos)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Elevada disponibilidade e recuperação de desastres para SAP NetWeaver em execução em Máquinas Virtuais Azure)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Alta disponibilidade para servidores de aplicações SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Utilização automática para instâncias SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Apenas cloud - Implementações de Máquina virtual em Azure sem dependências da rede de clientes no local)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-premises - Implantação de VMs SAP únicos ou múltiplos em Azure totalmente integrados com a rede no local)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiões de Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domínios de falha)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domínios de upgrade)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Conjuntos de disponibilidade azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Conceito de máquinas virtuais do Microsoft Azure)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Armazenamento Azure Premium)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Mover um VM de instalações para Azure com um disco não generalizado)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Implementação de um VM com uma imagem específica do cliente)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparação para mover um VM do local para Azure com um disco não generalizado)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparação para a implementação de um VM com uma imagem específica do cliente para o SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparação de VMs com SAP para Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Diferença entre um disco Azure e uma imagem Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Upload de um VHD de instalações para Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copiar discos entre contas de armazenamento azure)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Estrutura VM/VHD para implementações SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Configurar a montagem automática para discos ligados)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (VM único com cenário de demonstração/treino SAP NetWeaver)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Conceitos de implantação apenas em nuvem de instâncias SAP)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solução de Monitorização Azure para SAP)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Armazenamento Azure Premium)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Discos geridos)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Rede Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Armazenamento: Armazenamento e discos de dados do Microsoft Azure)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Matriz de disponibilidade de produto SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
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
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Implementar e gerir máquinas virtuais utilizando modelos de Gestor de Recursos Azure e o Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Gerir máquinas virtuais utilizando o Gestor de Recursos Azure e a PowerShell)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
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
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

A Azure Virtual Machines é a solução para organizações que precisam de recursos de computação e armazenamento, em tempo mínimo e sem longos ciclos de aquisição. Pode utilizar máquinas virtuais Azure para implementar aplicações clássicas, como aplicações baseadas em SAP NetWeaver, em Azure. Alargar a fiabilidade e disponibilidade de uma aplicação sem recursos adicionais no local. As Máquinas Virtuais Azure suportam a conectividade transversal, para que possa integrar as Máquinas Virtuais Azure nos domínios no local da sua organização, nuvens privadas e paisagem do sistema SAP.

Neste artigo, cobrimos os passos para implementar aplicações SAP em máquinas virtuais (VMs) em Azure, incluindo opções alternativas de implementação e resolução de problemas. Este artigo baseia-se na informação em [Azure Virtual Machines planejando e implementação para SAP NetWeaver][planning-guide]. Complementa também a documentação de instalação SAP e as Notas SAP, que são os principais recursos para a instalação e implementação de software SAP.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

A criação de uma máquina virtual Azure para implementação de software SAP envolve múltiplas etapas e recursos. Antes de começar, certifique-se de que cumpre os pré-requisitos para instalar software SAP em máquinas virtuais em Azure.

### <a name="local-computer"></a>Computador local

Para gerir os VMs Windows ou Linux, pode utilizar um script PowerShell e o portal Azure. Para ambas as ferramentas, é necessário um computador local que execute o Windows 7 ou uma versão posterior do Windows. Se quiser gerir apenas os VMs Linux e pretender utilizar um computador Linux para esta tarefa, pode utilizar o Azure CLI.

### <a name="internet-connection"></a>Ligação à Internet

Para descarregar e executar as ferramentas e scripts necessários para a implementação do software SAP, deve estar ligado à Internet. O Azure VM que está a executar a extensão Azure para SAP também precisa de acesso à Internet. Se o Azure VM fizer parte de uma rede virtual Azure ou de um domínio no local, certifique-se de que as definições de procuração relevantes são definidas, conforme descrito na [Configuração do proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Subscrição do Microsoft Azure

Precisa de uma conta Azure ativa.

### <a name="topology-and-networking"></a>Topologia e networking

Você precisa definir a topologia e arquitetura da implantação SAP em Azure:

* Contas de armazenamento azure a utilizar
* Rede virtual onde pretende implementar o sistema SAP
* Grupo de recursos para o qual pretende implantar o sistema SAP
* Região de Azure onde pretende implantar o sistema SAP
* Configuração SAP (dois ou três níveis)
* Tamanhos vm e o número de discos de dados adicionais a montar nos VMs
* Configuração do Sistema de Correção e Transporte (CTS) do SAP

Crie e configure as contas de armazenamento Azure (se necessário) ou redes virtuais Azure antes de iniciar o processo de implementação do software SAP. Para obter informações sobre como criar e configurar estes recursos, consulte o planeamento e implementação de [Máquinas Virtuais Azure para o SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Tamanho SAP

Conheça as seguintes informações, para dimensionamento SAP:

* Carga de trabalho SAP projetada, por exemplo, utilizando a ferramenta SAP Quick Sizer, e o número Padrão de Desempenho da Aplicação SAP (SAPS)
* Recurso CPU exigido e consumo de memória do sistema SAP
* Operações de entrada/saída (I/O) necessárias por segundo
* Largura de banda de rede necessária de eventual comunicação entre VMs em Azure
* Largura de banda de rede exigida entre os ativos no local e o sistema SAP implantado pelo Azure

### <a name="resource-groups"></a>Grupos de recursos

No Azure Resource Manager, pode utilizar grupos de recursos para gerir todos os recursos de aplicação na sua subscrição Azure. Para obter mais informações, veja [Descrição geral do Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Recursos

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos SAP

Quando estiver a configurar a implementação do software SAP, precisa dos seguintes recursos SAP:

* Nota SAP [1928533,]que tem:
  * Lista de tamanhos De VM Azure que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade para tamanhos de VM Azure
  * Software SAP suportado e sistema operativo (OS) e combinações de bases de dados
  * Versão necessária do kernel SAP para Windows e Linux no Microsoft Azure

* O SAP Note [2015553] lista os pré-requisitos para implementações de software SAP suportadas pela SAP em Azure.
* O SAP Note [2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* O SAP Note [1409604] tem a versão necessária do Agente anfitrião SAP para windows in Azure.
* O SAP Note [2191498] tem a versão necessária do Agente anfitrião SAP para o Linux em Azure.
* SAP Nota [2243692] tem informações sobre licenciamento SAP em Linux em Azure.
* SAP Note [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* SAP Note [2002167] tem informações gerais sobre red hat enterprise Linux 7.x.
* SAP Nota [2069760] tem informações gerais sobre o Oracle Linux 7.x.
* A Nota [SAP 1999351] tem informações adicionais de resolução de problemas para a extensão de monitorização avançada do Azure para sAP.
* SAP Nota [1597355] tem informações gerais sobre o espaço de troca para linux.
* [A página da SC SC](https://wiki.scn.sap.com/wiki/x/Pia7Gg) do Azure tem novidades e uma coleção de recursos úteis.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) exigiu todas as notas SAP para linux.
* Cmdlets PowerShell específicos da SAP que fazem parte da [Azure PowerShell][azure-ps].
* Comandos Azure CLI específicos da SAP que fazem parte do [Azure CLI][azure-cli].

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos windows

Estes artigos da Microsoft cobrem implementações SAP em Azure:

* [Planeamento e implementação de Máquinas Virtuais Azure para SAP NetWeaver][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP NetWeaver (este artigo)][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Cenários de implementação de software SAP em VMs Azure

Tem várias opções para implementar VMs e discos associados em Azure. É importante compreender as diferenças entre opções de implementação, pois pode tomar diferentes passos para preparar os seus VMs para implementação com base no tipo de implementação que escolher.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Cenário 1: Implantação de um VM do Azure Marketplace para SAP

Pode utilizar uma imagem fornecida pela Microsoft ou por terceiros no Azure Marketplace para implementar o seu VM. O Marketplace oferece algumas imagens padrão de OS do Windows Server e diferentes distribuições linux. Também pode implementar uma imagem que inclui sistema de gestão de bases de dados (DBMS) SKUs, por exemplo, Microsoft SQL Server. Para obter mais informações sobre a utilização de imagens com DBMS SKUs, consulte a implementação de [DBMS de Máquinas Virtuais Azure para SAP NetWeaver][dbms-guide].

O seguinte fluxograma mostra a sequência de passos específicos do SAP para a implementação de um VM do Mercado Azure:

![Fluxograma de implantação VM para sistemas SAP utilizando uma imagem VM do Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Criar uma máquina virtual utilizando o portal Azure

A forma mais fácil de criar uma nova máquina virtual com uma imagem do Azure Marketplace é utilizando o portal Azure.

1.  Aceda a <https://portal.azure.com/#create/hub>.  Ou, no menu do portal Azure, selecione **+ Novo**.
1.  Selecione **Compute**e, em seguida, selecione o tipo de sistema operativo que pretende implementar. Por exemplo, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) ou Oracle Linux 7.2. A visão da lista padrão não mostra todos os sistemas operativos suportados. Selecione **ver tudo** para uma lista completa. Para obter mais informações sobre sistemas operativos suportados para implementação de software SAP, consulte SAP Note [1928533 .]
1.  Na página seguinte, reveja os termos e condições.
1.  Na caixa de **modelo Seletiva,** selecione **Gestor de Recursos**.
1.  Selecione **Criar**.

O assistente guia-o através da definição dos parâmetros necessários para criar a máquina virtual, além de todos os recursos necessários, como interfaces de rede e contas de armazenamento. Alguns destes parâmetros são:

1. **Bases:**
   * **Nome**: O nome do recurso (o nome da máquina virtual).
   * **Tipo de disco VM:** Selecione o tipo de disco do disco OS. Se pretender utilizar o Armazenamento Premium para os seus discos de dados, recomendamos também a utilização do Armazenamento Premium para o disco OS.
   * **Nome de utilizador e palavra-passe** ou **chave pública SSH**: Introduza o nome de utilizador e a palavra-passe do utilizador que é criado durante o fornecimento. Para uma máquina virtual Linux, pode introduzir a chave Secure Shell (SSH) pública que utiliza para iniciar sessão na máquina.
   * **Subscrição**: Selecione a subscrição que pretende utilizar para fornecer a nova máquina virtual.
   * **Grupo de recursos**: O nome do grupo de recursos para o VM. Pode introduzir o nome de um novo grupo de recursos ou o nome de um grupo de recursos que já existe.
   * **Localização**: Onde implantar a nova máquina virtual. Se pretender ligar a máquina virtual à sua rede no local, certifique-se de que seleciona a localização da rede virtual que liga o Azure à sua rede no local. Para mais informações, consulte a [rede Microsoft Azure][planning-guide-microsoft-azure-networking] em [Máquinas Virtuais Azure planejando e implementação para SAP NetWeaver][planning-guide].
1. **Tamanho**:

     Para obter uma lista de tipos de VM suportados, consulte SAP Note [1928533]. Certifique-se de que seleciona o tipo VM correto se pretender utilizar o Armazenamento Premium Azure. Nem todos os tipos de VM suportam o Armazenamento Premium. Para mais informações, consulte Armazenamento: Armazenamento e discos de [dados do Microsoft Azure][planning-guide-storage-microsoft-azure-storage-and-data-disks] e armazenamento [Azure Premium][planning-guide-azure-premium-storage] em [Máquinas Virtuais Azure planejando e implementação para SAP NetWeaver][planning-guide].

1. **Definições:**
   * **Armazenamento**
     * **Tipo de disco:** Selecione o tipo de disco do disco OS. Se pretender utilizar o Armazenamento Premium para os seus discos de dados, recomendamos também a utilização do Armazenamento Premium para o disco OS.
     * **Utilize discos geridos**: Se pretender utilizar discos geridos, selecione Sim. Para mais informações sobre discos geridos, consulte o capítulo ['Discos Geridos'][planning-guide-managed-disks] no guia de planeamento.
     * **Conta de armazenamento**: Selecione uma conta de armazenamento existente ou crie uma nova. Nem todos os tipos de armazenamento funcionam para executar aplicações SAP. Para obter mais informações sobre tipos de armazenamento, consulte a [estrutura de armazenamento de um VM para implantações RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Rede**
     * **Rede virtual** e **Subnet**: Para integrar a máquina virtual com a sua intranet, selecione a rede virtual que está ligada à sua rede no local.
     * **Endereço IP público**: Selecione o endereço IP público que pretende utilizar ou introduza parâmetros para criar um novo endereço IP público. Pode utilizar um endereço IP público para aceder à sua máquina virtual através da Internet. Certifique-se de que também cria um grupo de segurança de rede para ajudar a garantir o acesso à sua máquina virtual.
     * **Grupo de segurança da rede**: Para mais informações, consulte o fluxo de tráfego da rede de controlo com [grupos de segurança][virtual-networks-nsg]da rede .
   * **Extensões**: Pode instalar extensões de máquinas virtuais adicionando-as à implementação. Não precisa de adicionar extensões neste passo. As extensões necessárias para o suporte SAP são instaladas posteriormente. Consulte o capítulo [Configure a extensão Azure para SAP][deployment-guide-4.5] neste guia.
   * **Alta Disponibilidade**: Selecione um conjunto de disponibilidade ou introduza os parâmetros para criar um novo conjunto de disponibilidade. Para mais informações, consulte os conjuntos de [disponibilidade do Azure.][planning-guide-3.2.3]
   * **Monitorização**
     * **Diagnóstico sinuoso:** Pode selecionar **Desativar** para diagnósticos de arranque.
     * **Diagnóstico sinuoso do hóspede**: Pode selecionar **Desativar** para monitorizar diagnósticos.

1. **Resumo:**

   Reveja as suas seleções e, em seguida, selecione **OK**.

A sua máquina virtual está implantada no grupo de recursos selecionado.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Criar uma máquina virtual usando um modelo

Pode criar uma máquina virtual utilizando um dos modelos SAP publicados no [repositório GitHub de modelos de arranque rápido.][azure-quickstart-templates-github] Também pode criar manualmente uma máquina virtual utilizando o [portal Azure,][virtual-machines-windows-tutorial] [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]ou [Azure CLI][virtual-machines-linux-tutorial].

* [**Modelo de configuração de dois níveis (apenas uma máquina virtual)** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  Para criar um sistema de dois níveis utilizando apenas uma máquina virtual, utilize este modelo.
* [**Modelo de configuração de dois níveis (apenas uma máquina virtual) - Discos geridos** (seiva-2-nível-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Para criar um sistema de dois níveis utilizando apenas uma máquina virtual e discos geridos, utilize este modelo.
* [**Modelo de configuração de três camadas (múltiplas máquinas virtuais)** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  Para criar um sistema de três camadas utilizando várias máquinas virtuais, use este modelo.
* [**Modelo de configuração de três camadas (múltiplas máquinas virtuais) - Disquetes geridos** (seiva-3-nível-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Para criar um sistema de três camadas utilizando múltiplas máquinas virtuais e discos geridos, utilize este modelo.

No portal Azure, introduza os seguintes parâmetros para o modelo:

1. **Bases:**
   * **Subscrição**: A subscrição a utilizar para implementar o modelo.
   * **Grupo de recursos**: O grupo de recursos a utilizar para implementar o modelo. Pode criar um novo grupo de recursos, ou pode selecionar um grupo de recursos existente na subscrição.
   * **Localização**: Onde implantar o modelo. Se selecionou um grupo de recursos existente, a localização desse grupo de recursos é utilizada.

1. **Definições:**
   * **ID do sistema SAP:** O ID do sistema SAP (SID).
   * **Tipo OS**: O sistema operativo que pretende implementar, por exemplo, O Windows Server 2012 R2, o SUSE Linux Enterprise Server 12 (SLES 12), o Red Hat Enterprise Linux 7.2 (RHEL 7.2) ou o Oracle Linux 7.2.

     A visão da lista não mostra todos os sistemas operativos suportados. Para obter mais informações sobre sistemas operativos suportados para implementação de software SAP, consulte SAP Note [1928533 .]
   * Tamanho do **sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS que o novo sistema fornece. Se não tem a certeza de quantos SAPS o sistema necessita, pergunte ao seu Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
   * **Disponibilidade do sistema** (apenas modelo de três camadas): A disponibilidade do sistema.

     Selecione **HA** para uma configuração adequada para uma instalação de alta disponibilidade. São criados dois servidores de base de dados e dois servidores para os Serviços Centrais ABAP SAP (ASCS).
   * **Tipo** de armazenamento (apenas modelo de dois níveis): O tipo de armazenamento a utilizar.

     Para sistemas maiores, recomendamos vivamente a utilização de Armazenamento Premium Azure. Para obter mais informações sobre tipos de armazenamento, consulte estes recursos:
      * [Utilização de Armazenamento SSD Premium Azure para instância SAP DBMS][2367194]
      * [Estrutura de armazenamento de um VM para implantações RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho da Máquina Virtual Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Storage do Microsoft Azure][storage-introduction]
   * **Nome de utilizador e** **senha de administrador:** Um nome de utilizador e senha.
     É criado um novo utilizador para iniciar sessão na máquina virtual.
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e sub-rede são criadas ou se é utilizada uma sub-rede existente. Se já tem uma rede virtual ligada à sua rede no local, selecione **Existino**.
   * **ID sub-rede**: Se pretender implantar o VM numa VNet existente onde tenha uma sub-rede definida a VM deve ser atribuída, diga o nome da identificação dessa sub-rede específica. O ID geralmente se parece&lt;com este: /subscrições/ id de subscrição&lt;>/recursosGroups/&lt;nome de grupo de&lt;recursos>/fornecedores/Microsoft.Network/virtualNetworks/ nome de rede virtual>/subnets/ nome de subnet>

1. **Termos e condições:**  
    Reveja e aceite os termos legais.

1. Selecione **Comprar**.

O Agente Azure VM é implantado por padrão quando utiliza uma imagem do Mercado Azure.

#### <a name="configure-proxy-settings"></a>Configurar definições de proxy

Dependendo da configuração da sua rede no local, poderá ter de configurar o proxy no seu VM. Se o seu VM estiver ligado à sua rede no local via VPN ou ExpressRoute, o VM pode não conseguir aceder à Internet e não poderá descarregar as extensões VM necessárias ou recolher informações de infraestrutura Azure para o agente hospedeiro SAP através da extensão SAP para Azure. Para mais informações, consulte [Configure o proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Junte-se a um domínio (apenas windows)

Se a sua implantação Azure estiver ligada a um diretório ativo ou dNS no local através de uma ligação VPN site-site Azure ou ExpressRoute (isto é chamado *de cross-premises* em [Máquinas Virtuais Azure planejamento e implementação para SAP NetWeaver),][planning-guide]espera-se que o VM esteja se juntando a um domínio no local. Para obter mais informações sobre considerações para esta tarefa, consulte [Juntar um VM a um domínio no local (apenas windows)][deployment-guide-4.3].

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configurar extensão VM

Para ter a certeza que o SAP suporta o seu ambiente, configure a extensão Azure para SAP, conforme descrito na [Configuração da Extensão Azure para SAP][deployment-guide-4.5]. Verifique os pré-requisitos para o SAP e exigiu versões mínimas de SAP Kernel e SAP Host Agent, nos recursos listados nos [recursos SAP][deployment-guide-2.2].

#### <a name="vm-extension-for-sap-check"></a>Extensão VM para verificação SAP

Verifique se a extensão VM para SAP está a funcionar, conforme descrito em [Controlos e resolução de problemas para a recolha de dados de ponta a ponta para o Agente hospedeiro SAP][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Etapas de pós-implantação

Depois de criar o VM e o VM ser implantado, é necessário instalar os componentes de software necessários no VM. Devido à sequência de instalação de implementação/software neste tipo de implementação vm, o software a instalar já deve estar disponível, quer em Azure, em outro VM, quer como um disco que possa ser ligado. Ou, considere usar um cenário transversal, no qual a conectividade com os ativos no local (ações de instalação) é dada.

Depois de implementar o seu VM em Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP no seu VM, como faria num ambiente no local. Para instalar o software SAP num VM Azure, tanto a SAP como a Microsoft recomendam que carregue e guarde os meios de instalação SAP em Azure VHDs ou Discos Geridos, ou que crie um VM Azure que funcione como um servidor de ficheiros que tenha todos os meios de instalação SAP necessários.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Cenário 2: Implementação de um VM com uma imagem personalizada para SAP

Uma vez que diferentes versões de um sistema operativo ou DBMS têm diferentes requisitos de patch, as imagens que encontra no Mercado Azure podem não satisfazer as suas necessidades. Em vez disso, pode querer criar um VM utilizando a sua própria imagem VM OS/DBMS, que poderá voltar a implantar mais tarde.
Usa diferentes passos para criar uma imagem privada para o Linux do que para criar uma para windows.

---
> ![Windows][Logo_Windows] Windows
>
> Para preparar uma imagem do Windows que pode utilizar para implementar várias máquinas virtuais, as definições do Windows (como o Windows SID e o nome de anfitrião) devem ser resumidas ou generalizadas no VM no local. Podes usar [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) para fazer isto.
>
> ![Linux][Logo_Linux] Linux
>
> Para preparar uma imagem Linux que pode usar para implementar várias máquinas virtuais, algumas definições de Linux devem ser abstraídas ou generalizadas no VM no local. Podes `waagent -deprovision` usar para fazer isto. Para mais informações, consulte [Capture uma máquina virtual Linux em funcionamento no Azure][virtual-machines-linux-capture-image] e no guia de utilizador do agente [Azure Linux][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
Pode preparar e criar uma imagem personalizada e depois usá-la para criar vários novos VMs. Isto é descrito no planeamento e implementação de [Máquinas Virtuais Azure para SAP NetWeaver][planning-guide]. Configurar o conteúdo da sua base de dados utilizando o SAP Software Provisioning Manager para instalar um novo sistema SAP (restaura uma cópia de segurança da base de dados a partir de um disco que está ligado à máquina virtual) ou restaurando diretamente uma cópia de segurança da base de dados do armazenamento do Azure, se o seu DBMS apoia-o. Para mais informações, consulte a implementação de [DBMS das Máquinas Virtuais Azure para SAP NetWeaver][dbms-guide]. Se já instalou um sistema SAP no seu VM no local (especialmente para sistemas de dois níveis), pode adaptar as definições do sistema SAP após a implementação do VM Azure utilizando o procedimento de renome do sistema suportado pelo SAP Software Provisioning Manager (SAP Note [1619720]). Caso contrário, pode instalar o software SAP depois de implementar o Azure VM.

O fluxograma seguinte mostra a sequência de passos específicos do SAP para a implementação de um VM a partir de uma imagem personalizada:

![Fluxograma de implantação VM para sistemas SAP utilizando uma imagem VM no Marketplace privado][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Criar uma máquina virtual utilizando o portal Azure

A forma mais fácil de criar uma nova máquina virtual a partir de uma imagem de Disco Gerido é utilizando o portal Azure. Para obter mais informações sobre como criar uma Imagem de Disco de Gestão, leia [Capture uma imagem gerida de um VM generalizado em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Aceda a <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Ou, no menu do portal Azure, selecione **Imagens.**
1.  Selecione a imagem do Disco Gerido que pretende implementar e clique em **Criar VM**

O assistente guia-o através da definição dos parâmetros necessários para criar a máquina virtual, além de todos os recursos necessários, como interfaces de rede e contas de armazenamento. Alguns destes parâmetros são:

1. **Bases:**
   * **Nome**: O nome do recurso (o nome da máquina virtual).
   * **Tipo de disco VM:** Selecione o tipo de disco do disco OS. Se pretender utilizar o Armazenamento Premium para os seus discos de dados, recomendamos também a utilização do Armazenamento Premium para o disco OS.
   * **Nome de utilizador e palavra-passe** ou **chave pública SSH**: Introduza o nome de utilizador e a palavra-passe do utilizador que é criado durante o fornecimento. Para uma máquina virtual Linux, pode introduzir a chave Secure Shell (SSH) pública que utiliza para iniciar sessão na máquina.
   * **Subscrição**: Selecione a subscrição que pretende utilizar para fornecer a nova máquina virtual.
   * **Grupo de recursos**: O nome do grupo de recursos para o VM. Pode introduzir o nome de um novo grupo de recursos ou o nome de um grupo de recursos que já existe.
   * **Localização**: Onde implantar a nova máquina virtual. Se pretender ligar a máquina virtual à sua rede no local, certifique-se de que seleciona a localização da rede virtual que liga o Azure à sua rede no local. Para mais informações, consulte a [rede Microsoft Azure][planning-guide-microsoft-azure-networking] em [Máquinas Virtuais Azure planejando e implementação para SAP NetWeaver][planning-guide].
1. **Tamanho**:

     Para obter uma lista de tipos de VM suportados, consulte SAP Note [1928533]. Certifique-se de que seleciona o tipo VM correto se pretender utilizar o Armazenamento Premium Azure. Nem todos os tipos de VM suportam o Armazenamento Premium. Para mais informações, consulte Armazenamento: Armazenamento e discos de [dados do Microsoft Azure][planning-guide-storage-microsoft-azure-storage-and-data-disks] e armazenamento [Azure Premium][planning-guide-azure-premium-storage] em [Máquinas Virtuais Azure planejando e implementação para SAP NetWeaver][planning-guide].

1. **Definições:**
   * **Armazenamento**
     * **Tipo de disco:** Selecione o tipo de disco do disco OS. Se pretender utilizar o Armazenamento Premium para os seus discos de dados, recomendamos também a utilização do Armazenamento Premium para o disco OS.
     * **Utilize discos geridos**: Se pretender utilizar discos geridos, selecione Sim. Para mais informações sobre discos geridos, consulte o capítulo ['Discos Geridos'][planning-guide-managed-disks] no guia de planeamento.
   * **Rede**
     * **Rede virtual** e **Subnet**: Para integrar a máquina virtual com a sua intranet, selecione a rede virtual que está ligada à sua rede no local.
     * **Endereço IP público**: Selecione o endereço IP público que pretende utilizar ou introduza parâmetros para criar um novo endereço IP público. Pode utilizar um endereço IP público para aceder à sua máquina virtual através da Internet. Certifique-se de que também cria um grupo de segurança de rede para ajudar a garantir o acesso à sua máquina virtual.
     * **Grupo de segurança da rede**: Para mais informações, consulte o fluxo de tráfego da rede de controlo com [grupos de segurança][virtual-networks-nsg]da rede .
   * **Extensões**: Pode instalar extensões de máquinas virtuais adicionando-as à implementação. Não precisa de adicionar extensão neste passo. As extensões necessárias para o suporte SAP são instaladas posteriormente. Consulte o capítulo [Configure a extensão Azure para SAP][deployment-guide-4.5] neste guia.
   * **Alta Disponibilidade**: Selecione um conjunto de disponibilidade ou introduza os parâmetros para criar um novo conjunto de disponibilidade. Para mais informações, consulte os conjuntos de [disponibilidade do Azure.][planning-guide-3.2.3]
   * **Monitorização**
     * **Diagnóstico sinuoso:** Pode selecionar **Desativar** para diagnósticos de arranque.
     * **Diagnóstico sinuoso do hóspede**: Pode selecionar **Desativar** para monitorizar diagnósticos.

1. **Resumo:**

   Reveja as suas seleções e, em seguida, selecione **OK**.

A sua máquina virtual está implantada no grupo de recursos selecionado.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Criar uma máquina virtual usando um modelo

Para criar uma implementação utilizando uma imagem de SO privada do portal Azure, utilize um dos seguintes modelos SAP. Estes modelos são publicados no [repositório gitHub de modelos de arranque rápido.][azure-quickstart-templates-github] Também pode criar manualmente uma máquina virtual, utilizando [o PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Modelo de configuração de dois níveis (apenas uma máquina virtual)** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  Para criar um sistema de dois níveis utilizando apenas uma máquina virtual, utilize este modelo.
* [**Modelo de configuração de dois níveis (apenas uma máquina virtual) - Imagem de disco gerida** (seiva-2-nível-user-image-md)][sap-templates-2-tier-user-image-md]

  Para criar um sistema de dois níveis utilizando apenas uma máquina virtual e uma imagem de Disco Gerido, utilize este modelo.
* [**Modelo de configuração de três camadas (múltiplas máquinas virtuais)** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  Para criar um sistema de três camadas utilizando múltiplas máquinas virtuais ou a sua própria imagem de SO, use este modelo.
* [**Modelo de configuração de três camadas (múltiplas máquinas virtuais) - Imagem de disco gerida** (seiva-3-nível-user-image-md)][sap-templates-3-tier-user-image-md]

  Para criar um sistema de três camadas utilizando múltiplas máquinas virtuais ou a sua própria imagem de OS e uma imagem de Disco Gerido, use este modelo.

No portal Azure, introduza os seguintes parâmetros para o modelo:

1. **Bases:**
   * **Subscrição**: A subscrição a utilizar para implementar o modelo.
   * **Grupo de recursos**: O grupo de recursos a utilizar para implementar o modelo. Pode criar um novo grupo de recursos ou selecionar um grupo de recursos existente na subscrição.
   * **Localização**: Onde implantar o modelo. Se selecionou um grupo de recursos existente, a localização desse grupo de recursos é utilizada.
1. **Definições:**
   * **ID do sistema SAP:** O ID do sistema SAP.
   * **Tipo OS**: O tipo de sistema operativo que pretende implementar (Windows ou Linux).
   * Tamanho do **sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS que o novo sistema fornece. Se não tem a certeza de quantos SAPS o sistema necessita, pergunte ao seu Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
   * **Disponibilidade do sistema** (apenas modelo de três camadas): A disponibilidade do sistema.

     Selecione **HA** para uma configuração adequada para uma instalação de alta disponibilidade. São criados dois servidores de base de dados e dois servidores para ASCS.
   * **Tipo** de armazenamento (apenas modelo de dois níveis): O tipo de armazenamento a utilizar.

     Para sistemas maiores, recomendamos vivamente a utilização de Armazenamento Premium Azure. Para obter mais informações sobre os tipos de armazenamento, consulte os seguintes recursos:
      * [Utilização de Armazenamento SSD Premium Azure para instância SAP DBMS][2367194]
      * [Estrutura de armazenamento de um VM para implantações RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho de máquinas virtuais Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Storage do Microsoft Azure][storage-introduction]
   * Imagem de **utilizador VHD URI** (apenas modelo de imagem de disco não gerido):&lt;O URI da imagem privada do OS VHD, por exemplo, https:// nome de conta>.blob.core.windows.net/vhds/userimage.vhd.
   * **Conta** de armazenamento de imagem do utilizador (apenas modelo de imagem de disco não gerido): O nome da conta de armazenamento onde a imagem privada de OS é armazenada, por exemplo, &lt;> de conta em https:// nome de conta>.blob.core.windows.net/vhds/userimage.vhd.&lt;
   * **userImageId** (apenas modelo de imagem de disco gerido): Id da imagem do Disco Gerido que pretende utilizar
   * **Nome de utilizador e** **senha de administrador:** O nome de utilizador e a palavra-passe.

     É criado um novo utilizador para iniciar sessão na máquina virtual.
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e sub-rede é criada ou se é utilizada uma sub-rede existente. Se já tem uma rede virtual ligada à sua rede no local, selecione **Existino**.
   * **ID sub-rede**: Se pretender implantar o VM numa VNet existente onde tenha uma sub-rede definida a VM deve ser atribuída, diga o nome da identificação dessa sub-rede específica. O ID geralmente se parece&lt;com este: /subscrições/ id de subscrição&lt;>/recursosGroups/&lt;nome de grupo de&lt;recursos>/fornecedores/Microsoft.Network/virtualNetworks/ nome de rede virtual>/subnets/ nome de subnet>

1. **Termos e condições:**  
    Reveja e aceite os termos legais.

1. Selecione **Comprar**.

#### <a name="install-the-vm-agent-linux-only"></a>Instale o Agente VM (apenas Linux)

Para utilizar os modelos descritos na secção anterior, o Agente Linux já deve ser instalado na imagem do utilizador, ou a implementação falhará. Descarregue e instale o Agente VM na imagem do utilizador conforme descrito no [Download, instale e ative o Agente VM Azure][deployment-guide-4.4]. Se não utilizar os modelos, também pode instalar o Agente VM mais tarde.

#### <a name="join-a-domain-windows-only"></a>Junte-se a um domínio (apenas windows)

Se a sua implantação Azure estiver ligada a um diretório ativo ou dNS no local através de uma ligação VPN site-para-site Azure ou Azure ExpressRoute (isto é chamado *de cross-premises* em [Bluee Virtual Machines planejando e implementação para SAP NetWeaver),][planning-guide]espera-se que o VM esteja se juntando a um domínio no local. Para obter mais informações sobre considerações para este passo, consulte [Juntar um VM para um domínio no local (apenas windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configurar definições de proxy

Dependendo da configuração da sua rede no local, poderá ter de configurar o proxy no seu VM. Se o seu VM estiver ligado à sua rede no local via VPN ou ExpressRoute, o VM poderá não conseguir aceder à Internet e não poderá descarregar as extensões VM necessárias ou recolher informações de infraestrutura Azure para o agente hospedeiro SAP através da extensão SAP para o Azure, consulte [configurar o proxy][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Configure extensão VM Azure para SAP

Para ter a certeza que o SAP suporta o seu ambiente, configure a extensão Azure para SAP, conforme descrito na [Configuração da Extensão Azure para SAP][deployment-guide-4.5]. Verifique os pré-requisitos para o SAP e exigiu versões mínimas de SAP Kernel e SAP Host Agent, nos recursos listados nos [recursos SAP][deployment-guide-2.2].

#### <a name="sap-vm-extension-check"></a>Verificação de extensão SAP VM

Verifique se a extensão VM para SAP está a funcionar, conforme descrito em [Controlos e resolução de problemas para a recolha de dados de ponta a ponta para o Agente hospedeiro SAP][deployment-guide-troubleshooting-chapter].


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Cenário 3: Mover um VM no local utilizando um Azure VHD não generalizado com SAP

Neste cenário, planeia mover um sistema SAP específico de um ambiente no local para O Azure. Pode fazê-lo enviando o VHD que tem o OS, os binários SAP e, eventualmente, os binários DBMS, além dos VHDs com os dados e ficheiros de registo do DBMS, para o Azure. Ao contrário do cenário descrito no [Cenário 2: Implantação de um VM com uma imagem personalizada para SAP][deployment-guide-3.3], neste caso, você mantém o nome de anfitrião, sAP SID e contas de utilizador SAP no Azure VM, porque foram configurados no ambiente no local. Não precisa generalizar o SO. Este cenário aplica-se mais frequentemente a cenários transversais onde parte da paisagem sap corre no local e parte dela funciona em Azure.

Neste cenário, o Agente VM **não** é instalado automaticamente durante a implementação. Uma vez que o Agente VM e a extensão Azure para SAP são necessários para executar sAP NetWeaver no Azure, você precisa descarregar, instalar e ativar ambos os componentes manualmente depois de criar a máquina virtual.

Para mais informações sobre o Agente Azure VM, consulte os seguintes recursos.

---
> ![Windows][Logo_Windows] Windows
>
> [Visão geral do Agente De MáquinaS Virtuais Azure][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Guia de Utilizador de Agente Azure Linux][virtual-machines-linux-agent-user-guide]
>
>

---

O fluxograma seguinte mostra a sequência de passos para mover um VM no local utilizando um VHD Azure não generalizado:

![Fluxograma de implantação VM para sistemas SAP utilizando um disco VM][deployment-guide-figure-400]

Se o disco já estiver carregado e definido em Azure (ver Planeamento e implementação de [Máquinas Virtuais Azure para SAP NetWeaver),][planning-guide]faça as tarefas descritas nas próximas secções.

#### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para criar uma implementação utilizando um disco operativo privado através do portal Azure, utilize o modelo SAP publicado no [repositório GitHub de modelos de arranque rápido.][azure-quickstart-templates-github] Também pode criar manualmente uma máquina virtual, utilizando o PowerShell.

* [**Modelo de configuração de dois níveis (apenas uma máquina virtual)** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  Para criar um sistema de dois níveis utilizando apenas uma máquina virtual, utilize este modelo.
* [**Modelo de configuração de dois níveis (apenas uma máquina virtual) - Disco gerido** (seiva-2-nível-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Para criar um sistema de dois níveis utilizando apenas uma máquina virtual e um Disco Gerido, utilize este modelo.

No portal Azure, introduza os seguintes parâmetros para o modelo:

1. **Bases:**
   * **Subscrição**: A subscrição a utilizar para implementar o modelo.
   * **Grupo de recursos**: O grupo de recursos a utilizar para implementar o modelo. Pode criar um novo grupo de recursos ou selecionar um grupo de recursos existente na subscrição.
   * **Localização**: Onde implantar o modelo. Se selecionou um grupo de recursos existente, a localização desse grupo de recursos é utilizada.
1. **Definições:**
   * **ID do sistema SAP:** O ID do sistema SAP.
   * **Tipo OS**: O tipo de sistema operativo que pretende implementar (Windows ou Linux).
   * Tamanho do **sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS que o novo sistema fornece. Se não tem a certeza de quantos SAPS o sistema necessita, pergunte ao seu Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
   * **Tipo** de armazenamento (apenas modelo de dois níveis): O tipo de armazenamento a utilizar.

     Para sistemas maiores, recomendamos vivamente a utilização de Armazenamento Premium Azure. Para obter mais informações sobre os tipos de armazenamento, consulte os seguintes recursos:
      * [Utilização de Armazenamento SSD Premium Azure para instância SAP DBMS][2367194]
      * [Estrutura de armazenamento de um VM para implantações RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho da Máquina Virtual Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Storage do Microsoft Azure][storage-introduction]
   * **O disco OS VHD URI** (apenas modelo de disco não gerido):&lt;O URI do disco operativo privado, por exemplo, https:// nome de conta>.blob.core.windows.net/vhds/osdisk.vhd.
   * **ID** do disco gerido do disco (apenas modelo de disco gerido): Id do disco os do disco gerido, /subscrições/92d102f7-81a5-4df7-9877-54987ba97dd9/recursosGroups/group/providers/Microsoft.Compute/disks/WIN
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e sub-rede são criadas ou se é utilizada uma sub-rede existente. Se já tem uma rede virtual ligada à sua rede no local, selecione **Existino**.
   * **ID sub-rede**: Se pretender implantar o VM numa VNet existente onde tenha uma sub-rede definida a VM deve ser atribuída, diga o nome da identificação dessa sub-rede específica. O ID geralmente se parece&lt;com este: /subscrições/ id de subscrição&lt;>/recursosGroups/&lt;nome de grupo de&lt;recursos>/fornecedores/Microsoft.Network/virtualNetworks/ nome de rede virtual>/subnets/ nome de subnet>

1. **Termos e condições:**  
    Reveja e aceite os termos legais.

1. Selecione **Comprar**.

#### <a name="install-the-vm-agent"></a>Instalar o Agente VM

Para utilizar os modelos descritos na secção anterior, o Agente VM deve ser instalado no disco OS, ou a implantação falhará. Descarregue e instale o Agente VM no VM, conforme descrito no [Download, instale e ative o Agente Azure VM][deployment-guide-4.4].

Se não utilizar os modelos descritos na secção anterior, também pode instalar o Agente VM posteriormente.

#### <a name="join-a-domain-windows-only"></a>Junte-se a um domínio (apenas windows)

Se a sua implantação Azure estiver ligada a um diretório ativo ou dNS no local através de uma ligação VPN site-site Azure ou ExpressRoute (isto é chamado *de cross-premises* em [Máquinas Virtuais Azure planejamento e implementação para SAP NetWeaver),][planning-guide]espera-se que o VM esteja se juntando a um domínio no local. Para obter mais informações sobre considerações para esta tarefa, consulte [Juntar um VM a um domínio no local (apenas windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configurar definições de proxy

Dependendo da configuração da sua rede no local, poderá ter de configurar o proxy no seu VM. Se o seu VM estiver ligado à sua rede no local via VPN ou ExpressRoute, o VM poderá não conseguir aceder à Internet e não poderá descarregar as extensões VM necessárias ou recolher informações de infraestrutura Azure para o agente hospedeiro SAP através da extensão SAP para o Azure, consulte [configurar o proxy][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Configure extensão VM Azure para SAP

Para ter a certeza que o SAP suporta o seu ambiente, configure a extensão Azure para SAP, conforme descrito na [Configuração da Extensão Azure para SAP][deployment-guide-4.5]. Verifique os pré-requisitos para o SAP e exigiu versões mínimas de SAP Kernel e SAP Host Agent, nos recursos listados nos [recursos SAP][deployment-guide-2.2].

#### <a name="sap-vm-check"></a>Verificação VM SAP

Verifique se a extensão VM para SAP está a funcionar, conforme descrito em [Controlos e resolução de problemas para a recolha de dados de ponta a ponta para o Agente hospedeiro SAP][deployment-guide-troubleshooting-chapter].

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Atualize a configuração da extensão Azure para SAP

Atualize a configuração da extensão Azure para SAP em qualquer um dos seguintes cenários:
* A equipa conjunta Microsoft/SAP alarga as capacidades da extensão VM e solicita mais ou menos contadores.
* A Microsoft introduz uma nova versão da infraestrutura azure subjacente que fornece os dados, e a extensão Azure para SAP precisa de ser adaptada a essas alterações.
* Monta discos de dados adicionais para o seu VM Azure ou remove um disco de dados. Neste cenário, atualize a recolha de dados relacionados com o armazenamento. Alterar a sua configuração adicionando ou apagando pontos finais ou atribuindo endereços IP a um VM não afeta a configuração da extensão.
* Muda o tamanho do seu VM Azure, por exemplo, do tamanho A5 para qualquer outro tamanho VM.
* Adiciona novas interfaces de rede ao seu VM Azure.

Para atualizar as definições, atualize a configuração da extensão Azure para SAP seguindo os passos na [Configuração da Extensão Azure para SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Tarefas detalhadas para implementação de software SAP

Esta secção tem passos detalhados para fazer tarefas específicas no processo de configuração e implementação.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implementar cmdlets Azure PowerShell

1. Vá ao [Microsoft Azure Downloads](https://azure.microsoft.com/downloads/).
1. Sob **as ferramentas da linha Command**, em **PowerShell,** selecione **a instalação do Windows**.
1. Na caixa de diálogo Microsoft Download Manager, para o ficheiro descarregado (por exemplo, WindowsAzurePowershellGet.3f.3f.3f.exe), selecione **Executar**.
1. Para executar o Instalador da Plataforma Web da Microsoft (Microsoft Web PI), selecione **Sim**.
1. Uma página que se parece com esta aparece:

   ![Página de instalação para cmdlets Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

1. Selecione **Instalar**e, em seguida, aceitar os Termos da Licença de Software da Microsoft.
1. O PowerShell está instalado. Selecione **Terminar** para fechar o assistente de instalação.

Verifique frequentemente as atualizações dos cmdlets PowerShell, que normalmente são atualizados mensalmente. A forma mais fácil de verificar se há atualizações é fazer os passos de instalação anteriores, até à página de instalação mostrada no passo 5. A data de lançamento e o número de lançamento dos cmdlets estão incluídos na página mostrada no passo 5. Salvo indicação em contrário no SAP Note [1928533] ou SAP Note [2015553,]recomendamos que trabalhe com a versão mais recente dos cmdlets Azure PowerShell.

Para verificar a versão dos cmdlets Azure PowerShell que estão instalados no seu computador, execute este comando PowerShell:

```powershell
(Get-Module Az.Compute).Version
```

O resultado é o seguinte:

![Resultado da verificação da versão cmdlet Da PowerShell Azure][deployment-guide-figure-600]
<a name="figure-6"></a>

Se a versão cmdlet Azure instalada no seu computador for a versão atual, a primeira página do assistente de instalação indica-a adicionando **(Instalada)** ao título do produto (ver a seguinte imagem). Os seus cmdlets PowerShell Azure estão atualizados. Para fechar o assistente de instalação, selecione **Saída**.

![Página de instalação para cmdlets Azure PowerShell indicando que a versão mais recente dos cmdlets Azure PowerShell estão instaladas][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implementar o Azure CLI

1. Vá ao [Microsoft Azure Downloads](https://azure.microsoft.com/downloads/).
1. Sob **as ferramentas da linha Command**, sob a interface da linha de comando **Azure,** selecione o link **Instalar** para o seu sistema operativo.
1. Na caixa de diálogo Microsoft Download Manager, para o ficheiro descarregado (por exemplo, WindowsAzureXPlatCLI.3f.3f.3f.exe), selecione **Executar**.
1. Para executar o Instalador da Plataforma Web da Microsoft (Microsoft Web PI), selecione **Sim**.
1. Uma página que se parece com esta aparece:

   ![Página de instalação para cmdlets Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

1. Selecione **Instalar**e, em seguida, aceitar os Termos da Licença de Software da Microsoft.
1. O Azure CLI está instalado. Selecione **Terminar** para fechar o assistente de instalação.

Verifique frequentemente as atualizações do Azure CLI, que normalmente é atualizado mensalmente. A forma mais fácil de verificar se há atualizações é fazer os passos de instalação anteriores, até à página de instalação mostrada no passo 5.

Para verificar a versão do Azure CLI que está instalada no seu computador, execute este comando:

```console
azure --version
```

O resultado é o seguinte:

![Resultado da verificação da versão Azure CLI][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Junte-se a um VM para um domínio no local (apenas windows)

Se implantar VMs SAP num cenário transversal, onde no local o Ative Directory e o DNS são estendidos em Azure, espera-se que os VMs estejam a aderir a um domínio no local. As medidas detalhadas que toma para aderir a um VM a um domínio no local, e o software adicional necessário para ser membro de um domínio no local, varia consoa do cliente. Normalmente, para se juntar a um VM a um domínio no local, é necessário instalar software adicional, como software antimalware, e software de backup ou monitorização.

Neste cenário, também é necessário certificar-se de que se as definições de procuração de Internet forem forçadas quando um VM se junta a um domínio no seu ambiente, a Conta de Sistema Local windows (S-1-5-18) no VM do Hóspede tem as mesmas definições de procuração. A opção mais fácil é forçar o representante usando uma Política de Grupo de Domínio, que se aplica aos sistemas no domínio.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Descarregue, instale e ative o Agente Azure VM

Para máquinas virtuais que são implantadas a partir de uma imagem de OS que não é generalizada (por exemplo, uma imagem que não tenha origem na Preparação do Sistema Windows, ou sysprep, ferramenta), é necessário descarregar, instalar e ativar manualmente o Agente Azure VM.

Se implementar um VM do Azure Marketplace, este passo não é necessário. As imagens do Azure Marketplace já têm o Agente Azure VM.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Descarregue o Agente Azure VM:
   1.  Descarregue o pacote de instalação do [Agente Azure VM](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Guarde o pacote MSI do Agente VM localmente num computador ou servidor pessoal.
1. Instale o Agente VM Azure:
   1.  Ligue-se ao Azure VM implantado utilizando o Protocolo de Ambiente de Trabalho Remoto (RDP).
   1.  Abra uma janela do Windows Explorer no VM e selecione o directório-alvo para o ficheiro MSI do Agente VM.
   1.  Arraste o ficheiro MSI do instalador de agente Azure VM do seu computador/servidor local para o directório-alvo do Agente VM no VM.
   1.  Clique duas vezes no ficheiro MSI no VM.
1. Para vMs que estão unidos aos domínios no local, certifique-se de que eventuais configurações de procuração de Internet também se aplicam à conta do Sistema Local do Windows (S-1-5-18) no VM, conforme descrito no [Configure o proxy][deployment-guide-configure-proxy]. O Agente VM funciona neste contexto e precisa de ser capaz de se ligar ao Azure.

Não é necessária qualquer interação do utilizador para atualizar o Agente Azure VM. O Agente VM é automaticamente atualizado e não necessita de um reinício vm.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Utilize os seguintes comandos para instalar o Agente VM para o Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) ou Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Se o agente já estiver instalado, para atualizar o Agente Azure Linux, faça as etapas descritas no [Update the Azure Linux Agent num VM para a versão mais recente do GitHub][virtual-machines-linux-update-agent].

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configure o proxy

Os passos que toma para configurar o proxy no Windows são diferentes da forma como configura o proxy em Linux.

#### <a name="windows"></a>Windows

As definições de procuração devem ser configuradas corretamente para que a conta do Sistema Local aceda à Internet. Se as definições de procuração não forem definidas pela Política de Grupo, pode configurar as definições para a conta do Sistema Local.

1. Ir para **Iniciar,** **insira gpedit.msc,** e depois selecione **Enter**.
1. Selecione**modelos administrativos** > de **configuração** > de computador**Windows Components** > **Internet Explorer**. Certifique-se de que a definição Faça as definições de **procuração por máquina (em vez de por utilizador)** estar desativada ou não configurada.
1. No **Painel de Controlo,** vá a**Opções**de Internet **do Network and Sharing Center** > .
1. No separador **Ligações,** selecione o botão de **definições LAN.**
1. Desmarque a caixa de verificação **Detetar definições automaticamente**.
1. Selecione o **servidor proxy utilize para a sua** caixa de verificação LAN e, em seguida, introduza o endereço proxy e a porta.
1. Selecione o botão **Avançado.**
1. Na caixa **exceções,** introduza o endereço IP **168.63.129.16**. Selecione **OK**.

#### <a name="linux"></a>Linux

Configure o proxy correto no ficheiro de configuração do Agente \\\\Convidado do Microsoft Azure, que está localizado em etc waagent.conf.

Definir os seguintes parâmetros:

1. **Http proxy host**. Por exemplo, coloque-o em **proxy.corp.local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **Http proxy port**. Por exemplo, coloque-o em **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Reinicie o agente.

   ```console
   sudo service waagent restart
   ```

As definições \\\\de procuração em waagent.conf também se aplicam às extensões VM necessárias. Se quiser utilizar os repositórios Azure, certifique-se de que o tráfego para estes repositórios não está a passar pela sua intranet no local. Se criou rotas definidas pelo utilizador para permitir a escavação forçada, certifique-se de que adiciona uma rota que encaminha o tráfego para os repositórios diretamente para a Internet, e não através da sua ligação VPN site-to-site.

* **SLES**

  Também precisa de adicionar rotas para os \\\\endereços IP listados em etc regionserverclnt.cfg. A figura que se segue mostra um exemplo:

  ![Túnel forçado][deployment-guide-figure-50]


* **RHEL**

  Também precisa de adicionar rotas para os endereços \\\\IP dos anfitriões\\listados em etc yum.repos.d rhui-load-balancers. Por exemplo, veja a figura anterior.

* **Oracle Linux**

  Não há repositórios para o Oracle Linux em Azure. Você precisa configurar seus próprios repositórios para Oráculo Linux ou usar os repositórios públicos.

Para obter mais informações sobre as rotas definidas pelo utilizador, consulte as rotas definidas pelo Utilizador e o [reencaminhamento ip][virtual-networks-udr-overview].

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configure a extensão Azure para SAP

Quando tiver preparado o VM conforme descrito nos [cenários de Implantação de VMs para SAP no Azure,][deployment-guide-3]o Agente Azure VM está instalado na máquina virtual. O próximo passo é implementar a extensão Azure para SAP, que está disponível no Repositório de Extensão Azure nos centros de dados globais do Azure. Para mais informações, consulte o planeamento e implementação de [Máquinas Virtuais Azure para o SAP NetWeaver][planning-guide-9.1].

Pode utilizar o PowerShell ou o Azure CLI para instalar e configurar a extensão Azure para SAP. Para instalar a extensão num Windows ou Linux VM utilizando uma máquina Windows, consulte [o Azure PowerShell][deployment-guide-4.5.1]. Para instalar a extensão num VM Linux utilizando um ambiente de trabalho Linux, consulte [O ClI Azure][deployment-guide-4.5.2].

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell para VMs Linux e Windows

Para instalar a extensão Azure para SAP utilizando a PowerShell:

1. Certifique-se de que instalou a versão mais recente do cmdlet Azure PowerShell. Para mais informações, consulte a implantação de [cmdlets Azure PowerShell][deployment-guide-4.1].  
1. Execute o seguinte cmdlet do PowerShell.
    Para obter uma lista de `commandlet Get-AzEnvironment`ambientes disponíveis, corra. Se quiser utilizar o Global Azure, o seu ambiente é **O AzureCloud.** Para Azure na China, selecione **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Depois de introduzir os dados da sua conta e identificar a máquina virtual Azure, o script implementa as extensões necessárias e permite as funcionalidades necessárias. Esta ação poderá demorar alguns minutos.
Para mais `Set-AzVMAEMExtension`informações sobre , consulte [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Execução bem sucedida de Conjunto azure-azVMAEMExtension específica do SAP][deployment-guide-figure-900]

A `Set-AzVMAEMExtension` configuração faz todos os passos para configurar a recolha de dados do anfitrião para o SAP.

A saída do script inclui as seguintes informações:

* Confirmação de que a recolha de dados para o disco DE E E e todos os discos de dados adicionais foi configurada.
* As próximas duas mensagens confirmam a configuração das Métricas de Armazenamento para uma conta de armazenamento específica.
* Uma linha de saída dá o estado da atualização real da extensão VM para a configuração SAP.
* Outra linha de saída confirma que a configuração foi implementada ou atualizada.
* A última linha de saída é informacional. Mostra as suas opções para testar a extensão VM para a configuração SAP.
* Para verificar se todas as etapas da extensão Azure VM para a configuração SAP foram executadas com sucesso, e que a Infraestrutura Azure fornece os dados necessários, proceda com a verificação de prontidão para a extensão Azure para SAP, conforme descrito no controlo de [prontidão para extensão Azure para SAP][deployment-guide-5.1].
* Aguarde 15 a 30 minutos para que o Azure Diagnostics recolha os dados relevantes.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI para VMs Linux

Para instalar a extensão Azure para SAP utilizando o Azure CLI:

   1. Instale o CLI clássico azure, conforme descrito na [Instalação do CLI clássico azure][azure-cli].
   1. Inscreva-se na sua conta Azure:

      ```console
      azure login
      ```

   1. Mude para o modo Gestor de Recursos Azure:

      ```console
      azure config mode arm
      ```

   1. Ativar extensão Azure para SAP:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Instalar utilizando o Azure CLI 2.0

   1. Instale o Azure CLI 2.0, conforme descrito no [Azure CLI 2.0][azure-cli-2].
   1. Inscreva-se na sua conta Azure:

      ```azurecli
      az login
      ```

   1. Instalar extensão AEM Azure CLI
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Instalar a extensão com
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Verifique se a extensão Azure para SAP está ativa no VM Azure Linux. Verifique se o \\\\ficheiro\\var lib\\AzureEnhancedMonitor PerfCounters existe. Se existir, a pedido de comando, executar este comando para exibir as informações recolhidas pela extensão Azure para SAP:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   A saída é assim:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="checks-and-troubleshooting-for-end-to-end-data-collection-for-sap-host-agent"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Verificações e resolução de problemas para recolha de dados de ponta a ponta para agente hospedeiro SAP

Depois de ter implantado o seu Azure VM e configurado a extensão Azure relevante para sAP, verifique se todos os componentes da extensão estão funcionando como esperado.

Verifique a verificação de prontidão da extensão Azure para SAP, conforme descrito na verificação de [prontidão para a extensão Azure para SAP][deployment-guide-5.1]. Se todos os resultados da verificação de prontidão forem positivos e todos os contadores de desempenho relevantes parecerem OK, a extensão Azure para SAP foi criada com sucesso. Pode prosseguir com a instalação do Agente hospedeiro SAP, conforme descrito nas notas SAP em [recursos SAP][deployment-guide-2.2]. Se a verificação de prontidão indicar que faltam contadores, faça o exame de saúde da extensão Azure para SAP, conforme descrito no exame de [saúde para a extensão Azure para a configuração do SAP][deployment-guide-5.2]. Para mais opções de resolução de problemas, consulte [A extensão de Troubleshooting Azure para SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Verificação de prontidão para a extensão Azure para SAP

Esta verificação garante que todas as métricas de desempenho que aparecem dentro da sua aplicação SAP são fornecidas pela extensão Azure subjacente para SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Faça a verificação de prontidão num VM do Windows

1. Inscreva-se na máquina virtual Azure (não é necessária uma conta de administração).
1. Abra uma janela de Linha de Comandos.
1. No pedido de comando, altere o diretório para a pasta de\\instalação da\\extensão Azure para SAP: C: Packages\\&lt;Plugins \\\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler versão>drop

   A *versão* no caminho para a extensão pode variar. Se vir pastas para várias versões da extensão na pasta de instalação, verifique a configuração do serviço Windows de Monitorização Do AzureEnhanced e, em seguida, mude para a pasta indicada como *Caminho para executável*.

   ![Propriedades de serviço que executam a extensão Azure para SAP][deployment-guide-figure-1000]

1. No pedido de comando, corra **azperflib.exe** sem parâmetros.

   > [!NOTE]
   > Azperflib.exe corre em loop e atualiza os contadores recolhidos a cada 60 segundos. Para terminar o laço, feche a janela De Comando.
   >
   >

Se a extensão Azure para SAP não estiver instalada ou se o serviço De Monitorização AzureEnhanced não estiver em funcionamento, a extensão não foi configurada corretamente. Para obter informações detalhadas sobre como implementar a extensão, consulte [A resolução de problemas da extensão Azure para SAP][deployment-guide-5.3].

> [!NOTE]
> O Azperflib.exe é um componente que não pode ser usado para fins próprios. É um componente que fornece exclusivamente dados de infraestrutura Azure relacionados com o VM para o Agente anfitrião SAP.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Verifique a saída de azperflib.exe

A saída azperflib.exe mostra todos os contadores de desempenho do Azure populosos para a SAP. Na parte inferior da lista de balcões recolhidos, um indicador de resumo e saúde mostra o estado da extensão Azure para SAP.

![Saída do exame de saúde executando azperflib.exe, o que indica que não existem problemas][deployment-guide-figure-1100]
<a name="figure-11"></a>

Verifique o resultado devolvido para a produção total dos **Contadores,** que é reportada como vazia, e para o estado de **Saúde,** mostrado no valor anterior.

Interpretar os valores resultantes da seguinte forma:

| Valores de resultados azperflib.exe | Extensão Azure para o estado de saúde do SAP |
| --- | --- |
| **Chamadas API - não disponíveis** | Os contadores que não estão disponíveis podem não ser aplicáveis à configuração da máquina virtual, ou são erros. Consulte o **estado da saúde.** |
| **Total de balcões - vazio** |Os dois balcões de armazenamento Azure seguintes podem estar vazios: <ul><li>Armazenamento Ler Op Latency Server msec</li><li>Armazenamento Ler Latência Op E2E msec</li></ul>Todos os outros balcões devem ter valores. |
| **Estado da saúde** |Só OK se o estado de devolução mostrar **OK**. |
| **Diagnóstico** |Informações detalhadas sobre o estado de saúde. |

Se o valor do estado de **saúde** não estiver **ok,** siga as instruções em [health check para extensão Azure para configuração SAP][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Verifique a prontidão num VM Linux

1. Ligue-se à Máquina Virtual Azure utilizando o SSH.

1. Verifique a saída da extensão Azure para SAP.

   a.  Execute `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Resultado esperado**: Lista de devoluções de contadores de desempenho. O ficheiro não deve estar vazio.

   b. Execute `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Resultado esperado**: Devolve uma linha em que o erro não é **nenhum,** por exemplo, **3;config; Erro;;0;0; nenhum;0;1456416792;tst-servercs;**

   c. Execute `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Resultado esperado**: Devoluções como vazias ou não existem.

Se o controlo anterior não tiver sido bem sucedido, efecsua estes controlos adicionais:

1. Certifique-se de que o waagent está instalado e ativado.

   a.  Execute `sudo ls -al /var/lib/waagent/`

     **Resultado esperado**: Lista o conteúdo do diretório waagent.

   b.  Execute `ps -ax | grep waagent`

   **Resultado esperado**: Apresenta uma entrada semelhante a:`python /usr/sbin/waagent -daemon`

1. Certifique-se de que a extensão Azure para SAP está instalada e em funcionamento.

   a.  Execute `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Resultado esperado**: Lista o conteúdo da extensão Azure para o diretório SAP.

   b. Execute `ps -ax | grep AzureEnhanced`

   **Resultado esperado**: Apresenta uma entrada semelhante a:`python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Instale o Agente hospedeiro SAP conforme descrito no SAP Note [1031096], e verifique a saída de `saposcol`.

   a.  Execute `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Execute `dump ccm`

   c.  Verifique se a métrica **de acesso de monitorização Virtualization_Configuration\Melhorada** é **verdadeira**.

Se já tiver um servidor de aplicação SAP NetWeaver ABAP instalado, abra o ST06 de transações e verifique se a monitorização melhorada está ativada.

Se algum destes controlos falhar e para obter informações detalhadas sobre como reimplantar a extensão, consulte a [resolução de problemas da extensão Azure para SAP][deployment-guide-5.3].

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Verificação de saúde da extensão Azure para configuração SAP

Se alguns dos dados da infraestrutura não forem entregues corretamente, tal como indicado `Test-AzVMAEMExtension` no teste descrito no controlo de [prontidão para extensão Azure para SAP,][deployment-guide-5.1]faça o cmdlet para verificar se a infraestrutura Azure e a extensão Azure para SAP estão corretamente configuradas.

1. Certifique-se de que instalou a versão mais recente do cmdlet Azure PowerShell, conforme descrito na implantação de [cmdlets Azure PowerShell][deployment-guide-4.1].
1. Execute o seguinte cmdlet do PowerShell. Para obter uma lista de ambientes `Get-AzEnvironment`disponíveis, corra o cmdlet . Para utilizar o Azure global, selecione o ambiente **AzureCloud.** Para Azure na China, selecione **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Insira os dados da sua conta e identifique a máquina virtual Azure.

   ![Página de entrada do SAP-specific Azure cmdlet Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

1. O script testa a configuração da máquina virtual selecionada.

   ![Saída de teste bem sucedido da extensão Azure para SAP][deployment-guide-figure-1300]

Certifique-se de que todos os resultados dos exames de saúde estão **bem**. Se algumas verificações não apresentarem **OK,** execute o cmdlet de atualização conforme descrito no [Configure a extensão Azure para SAP][deployment-guide-4.5]. Aguarde 15 minutos e repita os controlos descritos na [verificação de prontidão para extensão Azure para SAP][deployment-guide-5.1] e verificação de [saúde para extensão Azure para configuração SAP][deployment-guide-5.2]. Se as verificações ainda indicarem um problema com alguns ou todos os balcões, consulte [a resolução de problemas da extensão Azure para SAP][deployment-guide-5.3].

> [!Note]
> Pode experimentar alguns avisos nos casos em que utiliza discos Standard Azure Geridos. Serão apresentados avisos em vez dos testes que retornarão "OK". Isto é normal e pretendido no caso desse tipo de disco. Veja também [a resolução de problemas da extensão Azure para SAP][deployment-guide-5.3]
> 

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Resolução de problemas extensão azure para SAP

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Contadores de desempenho azure não aparecem de todo

O serviço AzureEnhancedMonitoring Windows recolhe métricas de desempenho em Azure. Se o serviço não tiver sido instalado corretamente ou se não estiver a funcionar no seu VM, não podem ser recolhidas métricas de desempenho.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>O diretório de instalação da extensão Azure para SAP está vazio

###### <a name="issue"></a>Problema

O diretório de\\instalação C: Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;versão>\\queda está vazia.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se se trata de uma questão de procuração (como descrito anteriormente). Pode ser necessário reiniciar a máquina `Set-AzVMAEMExtension` ou reexecutar o script de configuração.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Serviço de Extensão Azure para SAP não existe

###### <a name="issue"></a>Problema

O serviço AzureEnhancedMonitoring Windows não existe.

A saída azperflib.exe lança um erro:

![Execução de azperflib.exe indica que o serviço da extensão Azure para SAP não está em execução][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Solução

Se o serviço não existir, a extensão Azure para SAP não foi instalada corretamente. Reutilizar a extensão utilizando as etapas descritas para o seu cenário de implantação em [cenários de implantação de VMs para SAP em Azure][deployment-guide-3].

Depois de acionar a extensão, após uma hora, verifique novamente se os balcões de desempenho do Azure são fornecidos no VM Azure.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Serviço para extensão Azure para SAP existe, mas não arranca

###### <a name="issue"></a>Problema

O serviço AzureEnhancedMonitoring Windows existe e está ativado, mas não consegue arrancar. Para mais informações, consulte o registo do evento de candidatura.

###### <a name="solution"></a>Solução

A configuração está incorreta. Reinicie a extensão Azure para SAP no VM, conforme descrito no [Configure a extensão Azure para SAP][deployment-guide-4.5].

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Faltam alguns balcões de performance do Azure.

O serviço AzureEnhancedMonitoring Windows recolhe métricas de desempenho em Azure. O serviço obtém dados de várias fontes. Alguns dados de configuração são recolhidos localmente, e algumas métricas de desempenho são lidas a partir de Azure Diagnostics. Os balcões de armazenamento são utilizados a partir da sua exploração de madeira no nível de subscrição de armazenamento.

Se a resolução de problemas utilizando o SAP Note [1999351] não resolver o problema, refaça o script de `Set-AzVMAEMExtension` configuração. Pode ter de esperar uma hora porque os contadores de análise de armazenamento ou de diagnóstico podem não ser criados imediatamente após a sua ativação. Se o problema persistir, abra uma mensagem de apoio ao cliente SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Contadores de desempenho azure não aparecem de todo

As métricas de desempenho em Azure são recolhidas por um daemon. Se o daemon não estiver a funcionar, não podem ser recolhidas métricas de desempenho.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>O diretório de instalação da extensão Azure para SAP está vazio

###### <a name="issue"></a>Problema

O \\diretório\\\\var\\ lib waagent não tem um subdiretório para a extensão Azure para SAP.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se se trata de uma questão de procuração (como descrito anteriormente). Pode ser necessário reiniciar a máquina e/ou reexecutar o `Set-AzVMAEMExtension` script de configuração.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>A execução da extensão de Set-AzVMAEM E a extensão do teste-AzVMAEMEm mostram mensagens de aviso afirmando que os discos geridos padrão não são suportados

###### <a name="issue"></a>Problema

Ao executar mensagens de extensão de Set-AzVMAEM Ou Test-AzVMAEMComo Como estas são mostradas:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Executar azperfli.exe como descrito anteriormente pode obter um resultado que indique um estado não saudável. 

###### <a name="solution"></a>Solução

As mensagens são causadas pelo facto de os Discos Geridos Standard não estarem a fornecer as APIs utilizadas pela extensão SAP para sAP para verificar as estatísticas das Contas de Armazenamento Standard Azure. Isto não é uma questão de preocupação. A razão para a introdução dos dados de recolha das contas de armazenamento de discos padrão foi a aceleração de I/Os que ocorreu frequentemente. Os discos geridos evitarão essa estrangulamento limitando o número de discos numa conta de armazenamento. Portanto, não ter este tipo de dados não é crítico.


#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Faltam alguns balcões de performance do Azure.

As métricas de desempenho em Azure são recolhidas por um daemon, que obtém dados de várias fontes. Alguns dados de configuração são recolhidos localmente, e algumas métricas de desempenho são lidas a partir de Azure Diagnostics. Os balcões de armazenamento vêm dos registos da sua subscrição de armazenamento.

Para obter uma lista completa e atualizada de questões conhecidas, consulte a Nota SAP [1999351], que tem informações adicionais de resolução de problemas para a Extensão Azure para SAP.

Se a resolução de problemas utilizando o SAP Note [1999351] não resolver o problema, refaça o `Set-AzVMAEMExtension` script de configuração descrito no [Configure a extensão Azure para SAP][deployment-guide-4.5]. Pode ter de esperar uma hora porque os contadores de análise de armazenamento ou de diagnóstico podem não ser criados imediatamente após a sua ativação. Se o problema persistir, abra uma mensagem de apoio ao cliente SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.
