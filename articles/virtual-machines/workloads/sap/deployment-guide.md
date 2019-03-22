---
title: Implementação de máquinas virtuais do Azure para SAP NetWeaver | Documentos da Microsoft
description: Saiba como implementar software SAP em máquinas virtuais do Linux no Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/26/2018
ms.author: sedusch
ms.openlocfilehash: 2d296281f6865030bcdfec33d8c69cc313a358a5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011900"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Implementação de máquinas virtuais do Azure para SAP NetWeaver

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
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Implementação de DBMS de máquinas virtuais do Azure para SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Colocação em cache para VMs e VHDs)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (RAID de software)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Armazenamento do Microsoft Azure)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Estrutura de uma implementação de RDBMS)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Elevada disponibilidade e recuperação após desastre com VMs do Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e posterior)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e versões anteriores)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Utilizar uma imagem do SQL Server no Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Geral SQL Server para o SAP no resumo do Azure)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Informações específicas para o SQL Server RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configuração de armazenamento)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Cópia de segurança e restauro)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Considerações de desempenho para a cópia de segurança e restauro)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Other)
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

[deployment-guide]:deployment-guide.md (Implementação de máquinas virtuais do Azure para SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Recursos SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Implementar uma VM ao utilizar uma imagem personalizada)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Cenário 1: Implementar uma VM no Azure Marketplace para SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Cenário 2: Implementar uma VM com uma imagem personalizada para SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Cenário 3: Mover uma VM no local com um VHD de Azure não generalizado com o SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Cenários de implementação de VMs do SAP no Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Implementação de cmdlets do Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Transferir e importar os cmdlets do PowerShell relevantes para o SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Associar uma VM a um domínio no local - apenas Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Transferir, instalar e ativar o agente da VM do Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (CLI do Azure)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurar a extensão de monitorização avançada do Azure para SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Verificação de disponibilidade para avançada monitorização do Azure para SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Verificar estado de funcionamento da infraestrutura de monitorização do Azure)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Resolução de problemas de monitorização para SAP do Azure)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configurar a monitorização)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configurar o proxy)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Verificações e resolução de problemas para configurar a monitorização de ponto a ponto)

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

[msdn-set-azurermvmaemextension]:https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmaemextension

[planning-guide]:planning-guide.md (Máquinas de virtuais de planeamento e implementação de SAP do Azure)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Recursos)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Elevada disponibilidade e recuperação após desastre para o SAP NetWeaver em máquinas de virtuais do Azure em execução)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Elevada disponibilidade para servidores de aplicações SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Utilizar o início automático para as instâncias do SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Apenas na cloud - implementações de máquinas virtuais no Azure sem dependências na rede de cliente no local)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Totalmente integrado em vários locais - implantação de uma única ou várias VMs de SAP no Azure com a rede no local)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiões do Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domínios de falha)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domínios de atualização)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Conjuntos de disponibilidade do Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Conceito de máquinas virtuais do Microsoft Azure)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Armazenamento Premium do Azure)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Mover uma VM no local para o Azure com um disco não generalizada)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Implementar uma VM com uma imagem específica do cliente)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparação para mover uma VM no local para o Azure com um disco não generalizada)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparação para implementar uma VM com uma imagem específica do cliente para o SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparar VMs SAP para o Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Diferença entre um disco do Azure e uma imagem do Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Carregar um VHD no local para o Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copiar os discos entre contas de armazenamento do Azure)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Estrutura VM/VHD para implementações de SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Definição automontar para discos anexados)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (VM única com o SAP NetWeaver cenário de demonstração/treinamento)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Conceitos de implementação apenas na Cloud de instâncias do SAP)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solução de monitorização para SAP do Azure)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Armazenamento Premium do Azure)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Managed Disks)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Sistema de rede do Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Armazenamento: Discos de dados e de armazenamento do Microsoft Azure)

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Matriz de disponibilidade do produto SAP)
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
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Implementar e gerir máquinas virtuais com modelos Azure Resource Manager e a CLI do Azure)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Gerir máquinas virtuais com o Azure Resource Manager e PowerShell)
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

Máquinas virtuais do Azure é a solução para organizações que precisam de recursos de computação e armazenamento, no mínimo de tempo e sem ciclos de aprovisionamento demorada. Pode utilizar máquinas virtuais do Azure para implementar aplicações clássicas, assim como os aplicativos baseados em SAP NetWeaver no Azure. Expanda a fiabilidade e disponibilidade sem recursos no local adicionais de um aplicativo. Máquinas virtuais do Azure suporta a conectividade em vários locais, para que possa integrar máquinas virtuais do Azure em domínios no local, nuvens privadas e ambiente de sistema SAP da sua organização.

Neste artigo, vamos abordar os passos para implementar aplicações SAP em máquinas virtuais (VMs) no Azure, incluindo opções de implementação alternativos e resolução de problemas. Este artigo baseia-se nas informações da [máquinas virtuais do Azure de planeamento e implementação para o SAP NetWeaver][planning-guide]. Ele também complementa a documentação de instalação SAP e SAP Notes, que são os recursos principais para instalação e implantação de SAP software.

## <a name="prerequisites"></a>Pré-requisitos

Como configurar uma máquina virtual do Azure para a implementação de software SAP envolve vários passos e recursos. Antes de começar, certifique-se de que cumpre os pré-requisitos para instalar o software SAP em máquinas virtuais no Azure.

### <a name="local-computer"></a>Computador local

Para gerir VMs do Linux ou do Windows, pode usar um script do PowerShell e o portal do Azure. Para ambas as ferramentas, precisa de um computador local executando o Windows 7 ou uma versão posterior do Windows. Se pretender gerir apenas as VMs do Linux e pretender utilizar um computador com Linux para esta tarefa, pode utilizar a CLI do Azure.

### <a name="internet-connection"></a>Ligação à Internet

Para transferir e executar as ferramentas e scripts que são necessários para a implementação de software SAP, tem de estar ligado à Internet. VM do Azure que está a executar o Enhanced Monitoring a extensão do Azure para SAP também tem acesso à Internet. Se a VM do Azure faz parte de uma rede virtual do Azure ou o domínio no local, certifique-se de que as definições de proxy relevantes estão definidas, conforme descrito em [configurar o proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Subscrição do Microsoft Azure

Precisa de uma conta do Azure Active Directory.

### <a name="topology-and-networking"></a>Topologia e de rede

Tem de definir a topologia e a arquitetura da implementação do SAP no Azure:

* Contas de armazenamento do Azure para ser utilizado
* Rede virtual onde pretende implementar o sistema SAP
* Grupo de recursos para o qual pretende implementar o sistema SAP
* Região do Azure onde pretende implementar o sistema SAP
* Configuração de SAP (camada de dois ou três camadas)
* Tamanhos de VM e o número de discos de dados adicionais de ser montado para as VMs
* Configuração de correção de SAP e transporte System (CTS)

Criar e configurar contas de armazenamento do Azure (se necessário) ou redes virtuais do Azure antes de começar o processo de implantação de software SAP. Para obter informações sobre como criar e configurar estes recursos, consulte [máquinas virtuais do Azure de planeamento e implementação para o SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Dimensionamento do SAP

Sabe as informações seguintes, para o dimensionamento de SAP:

* Carga de trabalho SAP prevista, por exemplo, ao utilizar a ferramenta de Sizer rápido do SAP e a SAP aplicação desempenho padrão (SAPS) número
* Necessário consumo da CPU e memória de recursos do sistema SAP
* Operações necessárias de (e/s) de entrada/saída por segundo
* Necessária largura de banda de rede de comunicação eventual entre VMs no Azure
* Largura de banda de rede necessária entre ativos no local e o sistema SAP implementadas no Azure

### <a name="resource-groups"></a>Grupos de recursos

No Gestor de recursos do Azure, pode utilizar grupos de recursos para gerir todos os recursos de aplicação na sua subscrição do Azure. Para obter mais informações, consulte [descrição geral do Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Recursos

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos SAP

Quando estiver a configurar a implementação de software do SAP, terá dos seguintes recursos SAP:

* A nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure que são suportados para a implementação de SAP software
  * Informações de capacidade importante para tamanhos de VM do Azure
  * Software SAP suportado e o sistema operativo (SO) e combinações de base de dados
  * Versão de kernel do SAP necessária para Windows e Linux no Microsoft Azure

* A nota SAP [2015553] apresenta uma lista de pré-requisitos para implementações de software SAP suportadas para SAP no Azure.
* A nota SAP [2178632] tem informações detalhadas sobre todas as monitorizações métricas comunicadas para o SAP no Azure.
* A nota SAP [1409604] tem a versão necessária do agente de anfitrião do SAP para o Windows no Azure.
* A nota SAP [2191498] tem a versão necessária do agente de anfitrião do SAP para o Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento de SAP no Linux no Azure.
* A nota SAP [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A nota SAP [2002167] tem informações gerais sobre o Red Hat Enterprise Linux 7.x.
* A nota SAP [2069760] tem informações gerais sobre o Oracle Linux 7.x.
* A nota SAP [1999351] tem informações adicionais de resolução de problemas avançada de monitorização a extensão do Azure para SAP.
* A nota SAP [1597355] tem informações gerais sobre o espaço de comutação de Linux.
* [SAP na página do Azure SCN](https://wiki.scn.sap.com/wiki/x/Pia7Gg) tem notícias e uma coleção de recursos úteis.
* [WIKI de Comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as necessárias SAP Notes para o Linux.
* Cmdlets do PowerShell SAP específicos que fazem parte de [do Azure PowerShell][azure-ps].
* Comandos do CLI do Azure de SAP específicos que fazem parte de [CLI do Azure][azure-cli].

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos do Windows

Estes artigos de Microsoft abrangem implementações de SAP no Azure:

* [Máquinas de virtuais de planeamento e implementação para o SAP NetWeaver do Azure][planning-guide]
* [Implementação de máquinas virtuais do Azure para SAP NetWeaver (Este artigo)][deployment-guide]
* [Implementação de DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Cenários de implementação de software SAP em VMs do Azure

Tem várias opções para implementar VMs e os discos associados no Azure. É importante compreender as diferenças entre as opções de implementação, porque pode dar passos diferentes para preparar as suas VMs para a implementação com base no tipo de implementação que escolher.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Cenário 1: Implementar uma VM no Azure Marketplace para SAP

Pode utilizar uma imagem fornecida pela Microsoft ou por uma aplicação de terceiros no Azure Marketplace para implementar a sua VM. O Marketplace oferece algumas imagens de sistema operacional padrão do Windows Server e distribuições do Linux. Também pode implementar uma imagem que inclua a gestão de base de dados SKUs de sistema (DBMS), por exemplo, o Microsoft SQL Server. Para obter mais informações sobre o uso de imagens com SKUs do DBMS, consulte [implementação de DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide].

O fluxograma seguinte mostra a sequência de SAP específicas de passos para implementar uma VM do Azure Marketplace:

![Fluxograma de fase de implementação da VM para os sistemas SAP com uma imagem VM do Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Criar uma máquina virtual com o portal do Azure

É a maneira mais fácil para criar uma nova máquina virtual com uma imagem do Azure Marketplace através do portal do Azure.

1.  Aceda a <https://portal.azure.com/#create/hub>.  Ou, no menu do portal do Azure, selecione **+ novo**.
1.  Selecione **computação**e, em seguida, selecione o tipo de sistema operativo que pretende implementar. Por exemplo, Windows Server 2012 R2, 12 do SUSE Linux Enterprise Server (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) ou Oracle Linux 7.2. A vista de lista predefinida não mostra que todos os sistemas operativos suportados. Selecione **ver todas as** para obter uma lista completa. Para obter mais informações sobre sistemas operativos suportados para a implementação de software SAP, consulte a nota SAP [1928533].
1.  Na página seguinte, reveja os termos e condições.
1.  Na **selecionar um modelo de implementação** caixa, selecione **Resource Manager**.
1.  Selecione **Criar**.

O assistente orienta-o pela configuração de parâmetros necessários para criar a máquina virtual, além de todos os recursos necessários, como interfaces de rede e contas de armazenamento. Alguns desses parâmetros são:

1. **Noções básicas**:
   * **Nome**: O nome do recurso (o nome de máquina virtual).
   * **Tipo de disco VM**: Selecione o tipo de disco do disco do SO. Se pretender utilizar o armazenamento Premium para os discos de dados, recomendamos que utilize o armazenamento Premium para o disco do SO também.
   * **Nome de utilizador e palavra-passe** ou **chave pública SSH**: Introduza o nome de utilizador e palavra-passe do utilizador que é criado durante o aprovisionamento. Para uma máquina virtual do Linux, pode introduzir a chave Secure Shell (SSH) pública que utilizar para iniciar sessão máquina.
   * **Subscrição**: Selecione a subscrição que pretende utilizar para aprovisionar a nova máquina virtual.
   * **Grupo de recursos**: O nome do grupo de recursos para a VM. Pode introduzir o nome de um novo grupo de recursos ou o nome do grupo de recursos que já existe.
   * **Localização**: Onde pretende implementar a nova máquina virtual. Se pretender ligar-se a máquina virtual à sua rede no local, certifique-se de que selecionar a localização da rede virtual que se liga do Azure à sua rede no local. Para obter mais informações, consulte [sistema de rede do Microsoft Azure] [ planning-guide-microsoft-azure-networking] na [planeamento de máquinas virtuais do Azure e implementação para o SAP NetWeaver] [ planning-guide].
1. **Tamanho**:

     Para obter uma lista dos tipos VM suportados, consulte a nota SAP [1928533]. Certifique-se de que seleciona o tipo VM correto, se pretender utilizar o armazenamento Premium do Azure. Nem todos os tipos de VM suportam armazenamento Premium. Para obter mais informações, consulte [armazenamento: Discos de dados e de armazenamento do Microsoft Azure] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] e [armazenamento Premium do Azure] [ planning-guide-azure-premium-storage] no [máquinas virtuais do Azure planeamento e implementação para SAP NetWeaver][planning-guide].

1. **Definições**:
   * **Armazenamento**
     * **Tipo de disco**: Selecione o tipo de disco do disco do SO. Se pretender utilizar o armazenamento Premium para os discos de dados, recomendamos que utilize o armazenamento Premium para o disco do SO também.
     * **Utilizar discos geridos**: Se pretender utilizar os Managed Disks, selecione Sim. Para obter mais informações sobre os Managed Disks, consulte o capítulo [Managed Disks] [ planning-guide-managed-disks] no guia de planejamento.
     * **Conta de armazenamento**: Selecione uma conta de armazenamento existente ou crie um novo. Nem todos os tipos de armazenamento funcionam para executar aplicações SAP. Para obter mais informações sobre os tipos de armazenamento, consulte [estrutura de armazenamento de uma VM para implementações de RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Rede**
     * **Rede virtual** e **sub-rede**: Para integrar a máquina virtual com a sua intranet, selecione a rede virtual que está ligada à sua rede no local.
     * **Endereço IP público**: Selecione o endereço IP público que pretende utilizar, ou introduzir parâmetros para criar um novo endereço IP público. Pode utilizar um endereço IP público para aceder à sua máquina virtual através da Internet. Certifique-se de que também é criar um grupo de segurança de rede para o ajudar a proteger o acesso à sua máquina virtual.
     * **Grupo de segurança de rede**: Para obter mais informações, consulte [controlar o fluxo de tráfego de rede com grupos de segurança de rede][virtual-networks-nsg].
   * **Extensões**: É possível instalar extensões de máquina virtual, adicionando-as para a implementação. Não é necessário adicionar extensões neste passo. As extensões necessárias para o suporte do SAP são instaladas mais tarde. Consulte o capítulo [configurar a extensão do Azure melhorada de monitorização para SAP] [ deployment-guide-4.5] neste guia.
   * **Elevada disponibilidade**: Selecione um conjunto de disponibilidade ou, introduza os parâmetros para criar um novo conjunto de disponibilidade. Para obter mais informações, consulte [conjuntos de disponibilidade do Azure][planning-guide-3.2.3].
   * **Monitorização**
     * **Diagnóstico de arranque**: Pode selecionar **desativar** para diagnóstico de arranque.
     * **Diagnóstico do SO convidado**: Pode selecionar **desativar** para o monitoramento de diagnósticos.

1. **Resumo**:

   Reveja as suas seleções e, em seguida, selecione **OK**.

A máquina virtual é implementada no grupo de recursos que selecionou.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Criar uma máquina virtual utilizando um modelo

Pode criar uma máquina virtual utilizando um dos modelos publicados no SAP a [repositório do GitHub de modelos de início rápido do azure][azure-quickstart-templates-github]. Também pode criar manualmente uma máquina virtual utilizando o [portal do Azure][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms], ou [da CLI do Azure] [virtual-machines-linux-tutorial].

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (sap-2-escalão--imagem do marketplace)][sap-templates-2-tier-marketplace-image]

  Para criar um sistema de duas camadas com apenas uma máquina virtual, utilize este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual) - Managed Disks** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Para criar um sistema de duas camadas com apenas uma máquina virtual e Managed Disks, utilize este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais)** (sap-3-escalão--imagem do marketplace)][sap-templates-3-tier-marketplace-image]

  Para criar um sistema de três camadas com várias máquinas virtuais, utilize este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais) - Managed Disks** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Para criar um sistema de três camadas com várias máquinas virtuais e Managed Disks, utilize este modelo.

No portal do Azure, introduza os seguintes parâmetros para o modelo:

1. **Noções básicas**:
   * **Subscrição**: A subscrição a utilizar para implementar o modelo.
   * **Grupo de recursos**: O grupo de recursos para utilizar para implementar o modelo. Pode criar um novo grupo de recursos, ou pode selecionar um grupo de recursos existentes na subscrição.
   * **Localização**: Onde pretende implementar o modelo. Se tiver selecionado um grupo de recursos existente, a localização do grupo de recursos é utilizada.

1. **Definições**:
   * **ID de sistema SAP**: O ID de sistema SAP (SID).
   * **Tipo de SO**: O sistema operativo que pretende implementar, por exemplo, o Windows Server 2012 R2, 12 do SUSE Linux Enterprise Server (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) ou Oracle Linux 7.2.

     A vista de lista não mostra que todos os sistemas operativos suportados. Para obter mais informações sobre sistemas operativos suportados para a implementação de software SAP, consulte a nota SAP [1928533].
   * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS fornece o novo sistema. Se tiver a não certeza SAPS quantos requer que o sistema, peça ao seu parceiro de tecnologia de SAP ou integrador de sistemas.
   * **Disponibilidade do sistema** (apenas modelo de três camadas): A disponibilidade do sistema.

     Selecione **HA** para uma configuração que é adequada para uma instalação de elevada disponibilidade. Dois servidores de base de dados e dois servidores para o ABAP SAP Central Services (ASCS) são criados.
   * **Tipo de armazenamento** (apenas modelo de duas camadas): O tipo de armazenamento a utilizar.

     Para sistemas maiores, é altamente recomendável utilizar o armazenamento Premium do Azure. Para obter mais informações sobre os tipos de armazenamento, consulte estes recursos:
      * [Utilização do armazenamento SSD Premium do Azure para a instância do SAP DBMS][2367194]
      * [Estrutura de armazenamento de uma VM para implementações de RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Armazenamento de elevado desempenho para cargas de trabalho de Máquina Virtual do Azure][storage-premium-storage-preview-portal]
      * [Introdução ao armazenamento do Microsoft Azure][storage-introduction]
   * **Nome de utilizador administrador** e **palavra-passe de administrador**: Um nome de utilizador e palavra-passe.
     É criado um novo utilizador, para iniciar sessão para a máquina virtual.
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e uma sub-rede são criadas ou se é utilizada uma sub-rede existente. Se já tiver uma rede virtual que está ligada à sua rede no local, selecione **existentes**.
   * **ID de sub-rede**: Se pretender implementar a VM para uma VNet já existente em que tem uma sub-rede definida a VM deve ser atribuída para nomear o ID dessa sub-rede. O ID é normalmente fica assim: /subscriptions/&lt;id da subscrição > /resourceGroups/&lt;nome do grupo de recursos > /providers/Microsoft.Network/virtualNetworks/&lt;nome da rede virtual > /subnets/&lt;nome da sub-rede >

1. **Termos e condições**:  
    Reveja e aceite os termos legais.

1. Selecione **Comprar**.

O agente da VM do Azure é implementado por predefinição, quando utiliza uma imagem do Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Configurar definições de proxy

Dependendo da sua rede no local é configuração, poderá ter de configurar o proxy na sua VM. Se a sua VM está ligada à sua rede no local através de VPN ou ExpressRoute, a VM poderá não conseguir aceder à Internet e não poderá transferir as extensões necessárias ou recolher dados de monitorização. Para obter mais informações, consulte [configurar o proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Aderir a um domínio (apenas Windows)

Se a implementação do Azure está ligada a uma instância no local do Active Directory ou o DNS por meio de uma ligação de VPN de site a site do Azure ou o ExpressRoute (isto é denominado *em vários locais* no [planejamento de máquinas virtuais do Azure e a implementação para o SAP NetWeaver][planning-guide]), é esperado que a VM estiver ingressando num domínio no local. Para obter mais informações sobre as considerações para esta tarefa, consulte [associar uma VM a um domínio no local (apenas Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configurar a monitorização

Para garantir que o SAP oferece suporte a seu ambiente, configurar a extensão de monitorização do Azure para SAP, conforme descrito em [configurar a extensão do Azure melhorada de monitorização para SAP][deployment-guide-4.5]. Verifique os pré-requisitos para a monitorização do SAP e versões mínimas necessárias do Kernel do SAP e o agente de anfitrião do SAP, nos recursos listados na [recursos SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Verificação de monitoramento

Verifique se a monitorização está funcionando, conforme descrito em [verificações e resolução de problemas para configurar a monitorização de ponto-a-ponto][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Passos de pós-implementação

Depois de criar a VM e a VM é implementada, tem de instalar os componentes de software necessárias na VM. Devido a seqüência de instalação de implantação de software neste tipo de implementação da VM, o software seja instalado já deve estar disponível no Azure, em outra VM, ou como um disco que pode ser anexado. Em alternativa, considere a utilização de um cenário em vários locais, na qual conectividade com o local é dado a ativos (partilhas de instalação).

Depois de implementar a VM no Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP na sua VM, tal como faria num ambiente no local. Para instalar o SAP software numa VM do Azure, SAP e a Microsoft recomenda que carregar e armazenar a mídia de instalação de SAP em VHDs do Azure ou de Managed Disks, ou que cria uma VM do Azure funciona como um servidor de ficheiros que tem todas as necessárias SAP mídia de instalação.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Cenário 2: Implementar uma VM com uma imagem personalizada para SAP

Como versões diferentes de um sistema operativo ou o DBMS têm requisitos diferentes de patch, as imagens que encontrará no Azure Marketplace não podem satisfazer as suas necessidades. Em vez disso, poderá querer criar uma VM com a sua própria imagem de VM de SO/DBMS, que pode implementar novamente mais tarde.
Utilize os passos diferentes para criar uma imagem privada para Linux do que para criar um para Windows.

- - -
> ![Windows][Logo_Windows] Windows
>
> Para preparar uma imagem do Windows que pode utilizar para implementar várias máquinas virtuais, as definições do Windows (como o SID do Windows e o nome de anfitrião) tem de ser abstraídas ou generalizadas na VM no local. Pode usar [sysprep](https://msdn.microsoft.com/library/hh825084.aspx) para fazer isso.
>
> ![Linux][Logo_Linux] Linux
>
> Para preparar uma imagem de Linux que pode utilizar para implementar várias máquinas virtuais, algumas definições de Linux tem de ser abstraídas ou generalizadas na VM no local. Pode usar `waagent -deprovision` para fazer isso. Para obter mais informações, consulte [capturar uma máquina virtual do Linux em execução no Azure] [ virtual-machines-linux-capture-image] e o [guia de utilizador do agente Linux do Azure][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

- - -
Pode preparar e criar uma imagem personalizada e, em seguida, utilizá-lo para criar múltiplas VMs novas. Isso é descrito em [máquinas virtuais do Azure de planeamento e implementação para o SAP NetWeaver][planning-guide]. Configurar o conteúdo de base de dados utilizando o Gestor de aprovisionamento de Software do SAP para instalar um novo sistema SAP (restaura uma cópia de segurança da base de dados a partir de um disco que está ligado à máquina virtual) ou diretamente restaurando uma cópia de segurança da base de dados do armazenamento do Azure, se seu DBMS oferece suporte a ele. Para obter mais informações, consulte [implementação de DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide]. Se já tiver instalado um sistema SAP na sua VM no local (especialmente para sistemas de duas camadas), pode adaptar as definições do sistema SAP após a implementação da VM do Azure, utilizando o procedimento de mudar o nome do sistema de mensagens em fila suportado pelo SAP Software aprovisionamento Manager (SAP Tenha em atenção [1619720]). Caso contrário, pode instalar o software SAP depois de implementar a VM do Azure.

O fluxograma a seguir mostra a sequência de SAP específicas de passos para implementar uma VM a partir de uma imagem personalizada:

![Fluxograma de fase de implementação da VM para os sistemas SAP com uma imagem VM no Marketplace privada][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Criar uma máquina virtual com o portal do Azure

É a maneira mais fácil para criar uma nova máquina virtual a partir de uma imagem de disco gerido com o portal do Azure. Para obter mais informações sobre como criar uma imagem de disco gerir, leia [capturar uma imagem gerida de uma VM generalizada no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Aceda a <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Ou, no menu do portal do Azure, selecione **imagens**.
1.  Selecione a imagem de disco gerido que pretende implementar e clique em **criar VM**

O assistente orienta-o pela configuração de parâmetros necessários para criar a máquina virtual, além de todos os recursos necessários, como interfaces de rede e contas de armazenamento. Alguns desses parâmetros são:

1. **Noções básicas**:
   * **Nome**: O nome do recurso (o nome de máquina virtual).
   * **Tipo de disco VM**: Selecione o tipo de disco do disco do SO. Se pretender utilizar o armazenamento Premium para os discos de dados, recomendamos que utilize o armazenamento Premium para o disco do SO também.
   * **Nome de utilizador e palavra-passe** ou **chave pública SSH**: Introduza o nome de utilizador e palavra-passe do utilizador que é criado durante o aprovisionamento. Para uma máquina virtual do Linux, pode introduzir a chave Secure Shell (SSH) pública que utilizar para iniciar sessão máquina.
   * **Subscrição**: Selecione a subscrição que pretende utilizar para aprovisionar a nova máquina virtual.
   * **Grupo de recursos**: O nome do grupo de recursos para a VM. Pode introduzir o nome de um novo grupo de recursos ou o nome do grupo de recursos que já existe.
   * **Localização**: Onde pretende implementar a nova máquina virtual. Se pretender ligar-se a máquina virtual à sua rede no local, certifique-se de que selecionar a localização da rede virtual que se liga do Azure à sua rede no local. Para obter mais informações, consulte [sistema de rede do Microsoft Azure] [ planning-guide-microsoft-azure-networking] na [planeamento de máquinas virtuais do Azure e implementação para o SAP NetWeaver] [ planning-guide].
1. **Tamanho**:

     Para obter uma lista dos tipos VM suportados, consulte a nota SAP [1928533]. Certifique-se de que seleciona o tipo VM correto, se pretender utilizar o armazenamento Premium do Azure. Nem todos os tipos de VM suportam armazenamento Premium. Para obter mais informações, consulte [armazenamento: Discos de dados e de armazenamento do Microsoft Azure] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] e [armazenamento Premium do Azure] [ planning-guide-azure-premium-storage] no [máquinas virtuais do Azure planeamento e implementação para SAP NetWeaver][planning-guide].

1. **Definições**:
   * **Armazenamento**
     * **Tipo de disco**: Selecione o tipo de disco do disco do SO. Se pretender utilizar o armazenamento Premium para os discos de dados, recomendamos que utilize o armazenamento Premium para o disco do SO também.
     * **Utilizar discos geridos**: Se pretender utilizar os Managed Disks, selecione Sim. Para obter mais informações sobre os Managed Disks, consulte o capítulo [Managed Disks] [ planning-guide-managed-disks] no guia de planejamento.
   * **Rede**
     * **Rede virtual** e **sub-rede**: Para integrar a máquina virtual com a sua intranet, selecione a rede virtual que está ligada à sua rede no local.
     * **Endereço IP público**: Selecione o endereço IP público que pretende utilizar, ou introduzir parâmetros para criar um novo endereço IP público. Pode utilizar um endereço IP público para aceder à sua máquina virtual através da Internet. Certifique-se de que também é criar um grupo de segurança de rede para o ajudar a proteger o acesso à sua máquina virtual.
     * **Grupo de segurança de rede**: Para obter mais informações, consulte [controlar o fluxo de tráfego de rede com grupos de segurança de rede][virtual-networks-nsg].
   * **Extensões**: É possível instalar extensões de máquina virtual, adicionando-as para a implementação. Não é necessário adicionar a extensão neste passo. As extensões necessárias para o suporte do SAP são instaladas mais tarde. Consulte o capítulo [configurar a extensão do Azure melhorada de monitorização para SAP] [ deployment-guide-4.5] neste guia.
   * **Elevada disponibilidade**: Selecione um conjunto de disponibilidade ou, introduza os parâmetros para criar um novo conjunto de disponibilidade. Para obter mais informações, consulte [conjuntos de disponibilidade do Azure][planning-guide-3.2.3].
   * **Monitorização**
     * **Diagnóstico de arranque**: Pode selecionar **desativar** para diagnóstico de arranque.
     * **Diagnóstico do SO convidado**: Pode selecionar **desativar** para o monitoramento de diagnósticos.

1. **Resumo**:

   Reveja as suas seleções e, em seguida, selecione **OK**.

A máquina virtual é implementada no grupo de recursos que selecionou.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Criar uma máquina virtual utilizando um modelo

Para criar uma implementação com uma imagem de SO privada do portal do Azure, utilize um dos seguintes modelos SAP. Estes modelos são publicados na [repositório do GitHub de modelos de início rápido do azure][azure-quickstart-templates-github]. Também pode criar manualmente uma máquina virtual, utilizando [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (sap-2-escalão--imagem do utilizador)][sap-templates-2-tier-user-image]

  Para criar um sistema de duas camadas com apenas uma máquina virtual, utilize este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual) - imagem do disco gerido** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Para criar um sistema de duas camadas com apenas uma máquina virtual e uma imagem de disco gerido, utilize este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais)** (sap-3-escalão--imagem do utilizador)][sap-templates-3-tier-user-image]

  Para criar um sistema de três camadas com várias máquinas virtuais ou sua própria imagem de sistema operacional, utilize este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais) - imagem do disco gerido** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Para criar um sistema de três camadas com várias máquinas virtuais ou sua própria imagem de sistema operacional e uma imagem de disco gerido, utilize este modelo.

No portal do Azure, introduza os seguintes parâmetros para o modelo:

1. **Noções básicas**:
   * **Subscrição**: A subscrição a utilizar para implementar o modelo.
   * **Grupo de recursos**: O grupo de recursos para utilizar para implementar o modelo. Pode criar um novo grupo de recursos ou selecione um grupo de recursos existentes na subscrição.
   * **Localização**: Onde pretende implementar o modelo. Se tiver selecionado um grupo de recursos existente, a localização do grupo de recursos é utilizada.
1. **Definições**:
   * **ID de sistema SAP**: O ID de sistema SAP.
   * **Tipo de SO**: O tipo de sistema operativo que pretende implementar (Windows ou Linux).
   * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS fornece o novo sistema. Se tiver a não certeza SAPS quantos requer que o sistema, peça ao seu parceiro de tecnologia de SAP ou integrador de sistemas.
   * **Disponibilidade do sistema** (apenas modelo de três camadas): A disponibilidade do sistema.

     Selecione **HA** para uma configuração que é adequada para uma instalação de elevada disponibilidade. Dois servidores de base de dados e dois servidores para ASCS são criados.
   * **Tipo de armazenamento** (apenas modelo de duas camadas): O tipo de armazenamento a utilizar.

     Para sistemas maiores, é altamente recomendável utilizar o armazenamento Premium do Azure. Para obter mais informações sobre os tipos de armazenamento, consulte os seguintes recursos:
      * [Utilização do armazenamento SSD Premium do Azure para a instância do SAP DBMS][2367194]
      * [Estrutura de armazenamento de uma VM para implementações de RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Armazenamento de elevado desempenho para cargas de trabalho de máquina virtual do Azure][storage-premium-storage-preview-portal]
      * [Introdução ao armazenamento do Microsoft Azure][storage-introduction]
   * **URI de VHD da imagem de utilizador** (disco não gerido imagem modelo): O URI do sistema operacional privado de imagem VHD, por exemplo, https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
   * **Conta de armazenamento de imagem do utilizador** (disco não gerido imagem modelo): O nome da conta de armazenamento onde está armazenada a imagem de SO privada, por exemplo, &lt;accountname > em https://&lt;accountname >.blob.core.windows.net/vhds/userimage.vhd.
   * **userImageId** (disco gerido imagem modelo): ID da imagem de disco gerido que pretende utilizar
   * **Nome de utilizador administrador** e **palavra-passe de administrador**: O nome de utilizador e palavra-passe.

     É criado um novo utilizador, para iniciar sessão para a máquina virtual.
   * **Sub-rede nova ou existente**: Determina se é criada uma nova rede virtual e uma sub-rede ou se é utilizada uma sub-rede existente. Se já tiver uma rede virtual que está ligada à sua rede no local, selecione **existentes**.
   * **ID de sub-rede**: Se pretender implementar a VM para uma VNet já existente em que tem uma sub-rede definida a VM deve ser atribuída para nomear o ID dessa sub-rede. O ID é normalmente fica assim: /subscriptions/&lt;id da subscrição > /resourceGroups/&lt;nome do grupo de recursos > /providers/Microsoft.Network/virtualNetworks/&lt;nome da rede virtual > /subnets/&lt;nome da sub-rede >

1. **Termos e condições**:  
    Reveja e aceite os termos legais.

1. Selecione **Comprar**.

#### <a name="install-the-vm-agent-linux-only"></a>Instale o agente VM (apenas Linux)

Para utilizar os modelos descritos na secção anterior, já deve estar instalado o agente do Linux na imagem do utilizador ou a implementação irá falhar. Transfira e instale o agente da VM na imagem do utilizador, conforme descrito em [transferir, instalar e ativar o agente da VM do Azure][deployment-guide-4.4]. Se não usar os modelos, também pode instalar o agente de VM mais tarde.

#### <a name="join-a-domain-windows-only"></a>Aderir a um domínio (apenas Windows)

Se a implementação do Azure está ligada a uma instância de Active Directory ou o DNS no local através de uma ligação de VPN de site a site do Azure ou Azure ExpressRoute (isto é denominado *em vários locais* no [máquinas virtuais do Azure planeamento e implementação para o SAP NetWeaver][planning-guide]), é esperado que a VM estiver ingressando num domínio no local. Para obter mais informações sobre as considerações para este passo, consulte [associar uma VM a um domínio no local (apenas Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configurar definições de proxy

Dependendo da sua rede no local é configuração, poderá ter de configurar o proxy na sua VM. Se a sua VM está ligada à sua rede no local através de VPN ou ExpressRoute, a VM poderá não conseguir aceder à Internet e não poderá transferir as extensões necessárias ou recolher dados de monitorização. Para obter mais informações, consulte [configurar o proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Configurar a monitorização

Para garantir que o SAP oferece suporte a seu ambiente, configurar a extensão de monitorização do Azure para SAP, conforme descrito em [configurar a extensão do Azure melhorada de monitorização para SAP][deployment-guide-4.5]. Verifique os pré-requisitos para a monitorização do SAP e versões mínimas necessárias do Kernel do SAP e o agente de anfitrião do SAP, nos recursos listados na [recursos SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Verificação de monitoramento

Verifique se a monitorização está funcionando, conforme descrito em [verificações e resolução de problemas para configurar a monitorização de ponto-a-ponto][deployment-guide-troubleshooting-chapter].


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Cenário 3: Mover uma VM no local usando um VHD de Azure não generalizado com o SAP

Neste cenário, planeia mover um sistema SAP específico a partir de um ambiente no local para o Azure. Pode fazê-lo ao carregar o VHD que tenha o sistema operacional, os binários do SAP e, eventualmente, os binários do DBMS, além dos VHDs com os arquivos de dados e de registo do DBMS, para o Azure. Ao contrário do cenário descrito [cenário 2: Implementar uma VM com uma imagem personalizada para SAP][deployment-guide-3.3], neste caso, mantenha o nome de anfitrião, o SID do SAP, e contas de usuário SAP na VM do Azure, dado que estes foram configurados no ambiente no local. Não é necessário generalizar o sistema operacional. Este cenário aplica-se com mais freqüência para cenários em vários locais em que parte do ambiente SAP é executado no local e parte do mesmo é executado no Azure.

Neste cenário, o agente da VM é **não** instalados automaticamente durante a implementação. Uma vez que o agente da VM e avançada de monitorização a extensão do Azure para SAP são necessários para executar o SAP NetWeaver no Azure, terá de transferir, instalar e ativar os dois componentes manualmente depois de criar a máquina virtual.

Para obter mais informações sobre o agente da VM do Azure, consulte os seguintes recursos.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Descrição geral do agente da Máquina Virtual do Azure][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Guia de utilizador do agente Linux do Azure][virtual-machines-linux-agent-user-guide]
>
>

- - -

O fluxograma a seguir mostra a sequência de passos para mover uma VM no local através de um VHD de Azure não generalizada:

![Fluxograma de fase de implementação da VM para os sistemas SAP utilizando um disco VM][deployment-guide-figure-400]

Se o disco já está carregado e definido no Azure (veja [máquinas virtuais do Azure de planeamento e implementação para o SAP NetWeaver][planning-guide]), fazer as tarefas descritas no prazo de seções.

#### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para criar uma implementação utilizando um disco de SO privado através do portal do Azure, utilize o modelo SAP publicado no [repositório do GitHub de modelos de início rápido do azure][azure-quickstart-templates-github]. Também pode criar manualmente uma máquina virtual, com o PowerShell.

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (sap 2-escalão-utilizador-disco)][sap-templates-2-tier-os-disk]

  Para criar um sistema de duas camadas com apenas uma máquina virtual, utilize este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual) - disco gerido** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Para criar um sistema de duas camadas com apenas uma máquina virtual e um disco gerido, utilize este modelo.

No portal do Azure, introduza os seguintes parâmetros para o modelo:

1. **Noções básicas**:
   * **Subscrição**: A subscrição a utilizar para implementar o modelo.
   * **Grupo de recursos**: O grupo de recursos para utilizar para implementar o modelo. Pode criar um novo grupo de recursos ou selecione um grupo de recursos existentes na subscrição.
   * **Localização**: Onde pretende implementar o modelo. Se tiver selecionado um grupo de recursos existente, a localização do grupo de recursos é utilizada.
1. **Definições**:
   * **ID de sistema SAP**: O ID de sistema SAP.
   * **Tipo de SO**: O tipo de sistema operativo que pretende implementar (Windows ou Linux).
   * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS fornece o novo sistema. Se tiver a não certeza SAPS quantos requer que o sistema, peça ao seu parceiro de tecnologia de SAP ou integrador de sistemas.
   * **Tipo de armazenamento** (apenas modelo de duas camadas): O tipo de armazenamento a utilizar.

     Para sistemas maiores, é altamente recomendável utilizar o armazenamento Premium do Azure. Para obter mais informações sobre os tipos de armazenamento, consulte os seguintes recursos:
      * [Utilização do armazenamento SSD Premium do Azure para a instância do SAP DBMS][2367194]
      * [Estrutura de armazenamento de uma VM para implementações de RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Armazenamento de elevado desempenho para cargas de trabalho de Máquina Virtual do Azure][storage-premium-storage-preview-portal]
      * [Introdução ao armazenamento do Microsoft Azure][storage-introduction]
   * **URI de VHD de disco do SO** (apenas modelo de disco não gerido): O URI do disco de SO privado, por exemplo, https://&lt;accountname >.blob.core.windows.net/vhds/osdisk.vhd.
   * **Id do disco gerido disco do SO** (apenas modelo de disco gerido): O Id do disco do SO de discos geridos, /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e uma sub-rede são criados, ou se é utilizada uma sub-rede existente. Se já tiver uma rede virtual que está ligada à sua rede no local, selecione **existentes**.
   * **ID de sub-rede**: Se pretender implementar a VM para uma VNet já existente em que tem uma sub-rede definida a VM deve ser atribuída para nomear o ID dessa sub-rede. O ID é normalmente fica assim: /subscriptions/&lt;id da subscrição > /resourceGroups/&lt;nome do grupo de recursos > /providers/Microsoft.Network/virtualNetworks/&lt;nome da rede virtual > /subnets/&lt;nome da sub-rede >

1. **Termos e condições**:  
    Reveja e aceite os termos legais.

1. Selecione **Comprar**.

#### <a name="install-the-vm-agent"></a>Instalar o agente da VM

Para utilizar os modelos descritos na secção anterior, o agente da VM tem de estar instalado no disco do SO ou a implementação irá falhar. Transfira e instale o agente da VM na VM, conforme descrito em [transferir, instalar e ativar o agente da VM do Azure][deployment-guide-4.4].

Se não usar os modelos descritos na secção anterior, também pode instalar o agente da VM posteriormente.

#### <a name="join-a-domain-windows-only"></a>Aderir a um domínio (apenas Windows)

Se a implementação do Azure está ligada a uma instância no local do Active Directory ou o DNS por meio de uma ligação de VPN de site a site do Azure ou o ExpressRoute (isto é denominado *em vários locais* no [planejamento de máquinas virtuais do Azure e a implementação para o SAP NetWeaver][planning-guide]), é esperado que a VM estiver ingressando num domínio no local. Para obter mais informações sobre as considerações para esta tarefa, consulte [associar uma VM a um domínio no local (apenas Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configurar definições de proxy

Dependendo da sua rede no local é configuração, poderá ter de configurar o proxy na sua VM. Se a sua VM está ligada à sua rede no local através de VPN ou ExpressRoute, a VM poderá não conseguir aceder à Internet e não poderá transferir as extensões necessárias ou recolher dados de monitorização. Para obter mais informações, consulte [configurar o proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Configurar a monitorização

Para garantir que o SAP oferece suporte a seu ambiente, configurar a extensão de monitorização do Azure para SAP, conforme descrito em [configurar a extensão do Azure melhorada de monitorização para SAP][deployment-guide-4.5]. Verifique os pré-requisitos para a monitorização do SAP e versões mínimas necessárias do Kernel do SAP e o agente de anfitrião do SAP, nos recursos listados na [recursos SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Verificação de monitoramento

Verifique se a monitorização está funcionando, conforme descrito em [verificações e resolução de problemas para configurar a monitorização de ponto-a-ponto][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Atualizar a configuração de monitorização para SAP

Atualize a configuração de monitorização de SAP em qualquer um dos seguintes cenários:
* A equipe da Microsoft/SAP conjunta expande as capacidades de monitorização e pede contadores mais ou menos.
* A Microsoft apresenta uma nova versão da infraestrutura do Azure subjacente que fornece os dados de monitorização e avançada de monitorização a extensão do Azure para SAP tem de ser adaptada a essas alterações.
* Montar os discos de dados adicionais à sua VM do Azure ou remover um disco de dados. Neste cenário, atualize a recolha de dados relacionados com o armazenamento. Alterar a sua configuração ao adicionar ou eliminar pontos finais ou através da atribuição de endereços IP a uma VM não afeta a configuração de monitorização.
* Alterar o tamanho da VM do Azure, por exemplo, do tamanho das séries A5 para qualquer tamanho de VM.
* Adicionar novas interfaces de rede para a VM do Azure.

Para atualizar as definições de monitorização, atualizar a infraestrutura de monitorização ao seguir os passos em [configurar a extensão do Azure melhorada de monitorização para SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Tarefas detalhadas para a implementação de software SAP

Esta secção descreve em pormenor passos para efetuar tarefas específicas no processo de configuração e implantação.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implementar os cmdlets do Azure PowerShell

1. Aceda a [Downloads do Microsoft Azure](https://azure.microsoft.com/downloads/).
1. Sob **ferramentas de linha de comandos**, em **PowerShell**, selecione **Windows instalar**.
1. Na caixa de diálogo de Gerenciador de Download da Microsoft, para o ficheiro transferido (por exemplo, WindowsAzurePowershellGet.3f.3f.3fnew.exe), selecione **executar**.
1. Para executar o Microsoft Web Platform Installer (instalador de plataforma do Microsoft Web), selecione **Sim**.
1. Uma página que parece que esta opção é apresentada:

   ![Página de instalação de cmdlets do Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

1. Selecione **instalar**e, em seguida, aceite os termos de licença de Software da Microsoft.
1. O PowerShell está instalado. Selecione **concluir** para fechar o Assistente de instalação.

Verifique com frequência para atualizações para os cmdlets do PowerShell, que normalmente são atualizadas mensalmente. É a maneira mais fácil para procurar atualizações realizar os passos de instalação anterior, até a página de instalação mostrada no passo 5. O número de data e a versão de lançamento dos cmdlets está incluído na página mostrado no passo 5. Salvo indicação em contrário na nota SAP [1928533] ou a nota SAP [2015553], recomendamos que trabalhe com a versão mais recente dos cmdlets Azure PowerShell.

Para verificar a versão dos cmdlets do PowerShell do Azure que estão instalados no seu computador, execute este comando do PowerShell:
```powershell
(Get-Module AzureRm.Compute).Version
```
O resultado tem o seguinte aspeto:

![Resultado da verificação de versão de cmdlet do PowerShell do Azure][deployment-guide-figure-600]
<a name="figure-6"></a>

Se a versão de cmdlet do Azure instalada no seu computador é a versão atual, a primeira página do Assistente de instalação do indica-lo adicionando **(instalado)** para o título do produto (veja a captura de ecrã seguinte). Os cmdlets do PowerShell Azure estão atualizados. Para fechar o Assistente de instalação, selecione **saída**.

![Página de instalação para os cmdlets do Azure PowerShell com a indicação de que a versão mais recente dos cmdlets Azure PowerShell são instalados][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implementar a CLI do Azure

1. Aceda a [Downloads do Microsoft Azure](https://azure.microsoft.com/downloads/).
1. Sob **ferramentas de linha de comandos**, em **interface de linha de comandos do Azure**, selecione o **instalar** ligação para o seu sistema operativo.
1. Na caixa de diálogo de Gerenciador de Download da Microsoft, para o ficheiro transferido (por exemplo, WindowsAzureXPlatCLI.3f.3f.3fnew.exe), selecione **executar**.
1. Para executar o Microsoft Web Platform Installer (instalador de plataforma do Microsoft Web), selecione **Sim**.
1. Uma página que parece que esta opção é apresentada:

   ![Página de instalação de cmdlets do Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

1. Selecione **instalar**e, em seguida, aceite os termos de licença de Software da Microsoft.
1. CLI do Azure está instalada. Selecione **concluir** para fechar o Assistente de instalação.

Verifique com frequência para atualizações para a CLI do Azure, que normalmente é atualizada mensalmente. É a maneira mais fácil para procurar atualizações realizar os passos de instalação anterior, até a página de instalação mostrada no passo 5.

Para verificar a versão da CLI do Azure que está instalado no seu computador, execute este comando:
```
azure --version
```

O resultado tem o seguinte aspeto:

![Resultado da verificação da versão da CLI do Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Associar uma VM a um domínio no local (apenas Windows)

Se implementar VMs de SAP num cenário em vários locais, em que são expandidas do Active Directory no local e o DNS no Azure, espera-se que as VMs são associação a um domínio no local. Os passos detalhados que tomar para associar uma VM a um domínio no local e o software adicional necessário para ser um membro de um domínio no local, varia pelo cliente. Normalmente, para associar uma VM a um domínio no local, terá de instalar software adicional, como o software antimalware e o software de cópia de segurança ou monitorização.

Neste cenário, terá também de certificar-se de que, se as definições de proxy de Internet são forçadas quando uma VM é associado um domínio no seu ambiente, o Windows conta Sistema Local (S-1-5-18) na VM de convidado tem as mesmas definições de proxy. A opção mais fácil é forçar o proxy através de um diretiva de grupo, que se aplicam aos sistemas no domínio do domínio.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Transferir, instalar e ativar o agente da VM do Azure

Para máquinas virtuais implementadas a partir de uma imagem de sistema operacional que não está generalizada (por exemplo, uma imagem que não tem origem na ferramenta de preparação do sistema Windows, ou sysprep,), terá de transferir, instalar e ativar o agente da VM do Azure manualmente.

Se implementar uma VM do Azure Marketplace, este passo não é necessário. As imagens do Azure Marketplace já têm o agente da VM do Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Transferir o agente VM do Azure:
   1.  Transfira o [pacote de instalador do agente de VM do Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Store o pacote MSI do agente de VM localmente num computador pessoal ou servidor.
1. Instale o agente VM do Azure:
   1.  Ligar à VM do Azure implementadas com o protocolo RDP (Remote Desktop).
   1.  Abra uma janela do Explorador do Windows na VM e selecione o diretório de destino para o ficheiro MSI do agente de VM.
   1.  Arraste o ficheiro MSI de instalador do agente de VM do Azure do seu computador local/servidor para o diretório de destino do agente da VM na VM.
   1.  Clique duas vezes o arquivo MSI na VM.
1. Para as VMs que estão associadas a domínios no local, certifique-se de que as definições de proxy de Internet eventual também se aplicam à conta de sistema Local do Windows (S-1-5-18) na VM, conforme descrito em [configurar o proxy][deployment-guide-configure-proxy]. O agente da VM é executado nesse contexto e tem de ser capaz de ligar ao Azure.

Sem interação do utilizador é necessário para atualizar o agente da VM do Azure. O agente da VM é atualizado automaticamente e não requer um reinício VM.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Utilize os seguintes comandos para instalar o agente da VM para Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) ou no Oracle Linux**

  ```
  sudo yum install WALinuxAgent
  ```

Se o agente já estiver instalado, para atualizar o agente Linux do Azure, efetue os passos descritos em [atualizar o agente do Linux do Azure numa VM para a versão mais recente a partir do GitHub][virtual-machines-linux-update-agent].

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configurar o proxy

Os passos que efetuar para configurar o proxy no Windows são diferentes da forma como configurar o proxy no Linux.

#### <a name="windows"></a>Windows

Definições de proxy devem ser configuradas corretamente para a conta Sistema Local aceder à Internet. Se as definições de proxy não estão definidas pela diretiva de grupo, pode configurar as definições para a conta Sistema Local.

1. Aceda a **começar**, introduza **gpedit. msc**e, em seguida, selecione **Enter**.
1. Selecione **configuração do computador** > **modelos administrativos** > **componentes do Windows**  >   **Internet Explorer**. Certifique-se de que a definição **tornar o proxy de definições por computador (em vez de por utilizador)** está desativada ou não configurado.
1. Na **painel de controlo**, aceda à **Centro de compartilhamento e rede** > **opções da Internet**.
1. Sobre o **ligações** separador, selecione a **definições de LAN** botão.
1. Limpar o **detetar automaticamente as definições** caixa de verificação.
1. Selecione o **utilizar um servidor proxy para a rede local** caixa de verificação e, em seguida, introduza o endereço de proxy e a porta.
1. Selecione o **avançadas** botão.
1. Na **exceções** , introduza o endereço IP **168.63.129.16**. Selecione **OK**.

#### <a name="linux"></a>Linux

Configurar o proxy correto no ficheiro de configuração do agente de convidado do Microsoft Azure, que está localizado em \\etc\\waagent.conf.

Defina os seguintes parâmetros:

1. **Anfitrião do proxy HTTP**. Por exemplo, defina-o como **proxy.corp.local**.
   ```
   HttpProxy.Host=<proxy host>

   ```
1. **Porta de proxy HTTP**. Por exemplo, defina-o como **80**.
   ```
   HttpProxy.Port=<port of the proxy host>

   ```
1. Reinicie o agente.

   ```
   sudo service waagent restart
   ```

As definições de proxy no \\etc\\waagent.conf também se aplicam para as extensões VM necessárias. Se pretender utilizar os repositórios do Azure, certifique-se de que o tráfego para estes repositórios não vai por meio da intranet no local. Se tiver criado as rotas definidas pelo utilizador para ativar o protocolo de túnel forçado, certifique-se de que adicione uma rota que encaminha o tráfego para os repositórios diretamente à Internet e não através da sua ligação de VPN de site a site.

* **SLES**

  Também tem de adicionar as rotas para os endereços IP listados na \\etc\\regionserverclnt.cfg. A figura a seguir mostra um exemplo:

  ![Túnel forçado][deployment-guide-figure-50]


* **RHEL**

  Também tem de adicionar as rotas para os endereços IP dos anfitriões listados na \\etc\\yum.repos.d\\rhui-balanceadores de carga. Por exemplo, veja a figura anterior.

* **Oracle Linux**

  Não há nenhum repositório para Oracle Linux no Azure. Precisa configurar seus próprios repositórios para Oracle Linux ou utilizar os repositórios públicos.

Para obter mais informações sobre as rotas definidas pelo utilizador, consulte [rotas definidas pelo utilizador e reencaminhamento IP][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configurar a extensão de monitorização avançada do Azure para SAP

Quando já preparou a VM conforme descrito em [cenários de implementação de VMs do SAP no Azure][deployment-guide-3], o agente de VM do Azure está instalado na máquina virtual. A próxima etapa é implantar a extensão do Azure melhorada de monitorização para SAP, o que está disponível no repositório de extensão do Azure nos datacenters do Azure global. Para obter mais informações, consulte [máquinas virtuais do Azure de planeamento e implementação para o SAP NetWeaver][planning-guide-9.1].

Pode utilizar o PowerShell ou CLI do Azure para instalar e configurar a extensão do Azure melhorada de monitorização para SAP. Para instalar a extensão numa VM do Linux ou do Windows ao utilizar uma máquina Windows, consulte [do Azure PowerShell][deployment-guide-4.5.1]. Para instalar a extensão numa VM do Linux com uma área de trabalho do Linux, veja [CLI do Azure][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>O Azure PowerShell para Linux e VMs do Windows

Para instalar a extensão do Azure melhorada de monitorização para SAP com o PowerShell:

1. Certifique-se de que instalou a versão mais recente do cmdlet do PowerShell do Azure. Para obter mais informações, consulte [cmdlets de implementar o Azure PowerShell][deployment-guide-4.1].  
1. Execute o seguinte cmdlet do PowerShell.
    Para obter uma lista dos ambientes disponíveis, execute `commandlet Get-AzureRmEnvironment`. Se pretender utilizar o global Azure, o ambiente está **AzureCloud**. Para o Azure na China, selecione **AzureChinaCloud**.

    ```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Connect-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Depois de introduzir os dados da sua conta e identificar a máquina virtual do Azure, o script implementa as extensões necessárias e permite que as funcionalidades necessárias. Esta ação pode demorar vários minutos.
Para obter mais informações sobre `Set-AzureRmVMAEMExtension`, consulte [Set-AzureRmVMAEMExtension][msdn-set-azurermvmaemextension].

![Execução bem-sucedida de SAP específicos do Azure cmdlet Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

O `Set-AzureRmVMAEMExtension` configuração faz todos os passos para configurar o anfitrião de monitorização para SAP.

Saída do script inclui as seguintes informações:

* Confirmação de que a monitorização de disco do SO e todos os discos de dados adicionais foi configurada.
* As próximas duas mensagens Certifique-se a configuração de métricas de armazenamento para uma conta de armazenamento específico.
* Uma linha de saída apresenta o estado da atualização real da configuração de monitorização.
* Outra linha da saída confirma que a configuração foi implementada ou atualizada.
* A última linha da saída se a fins informativa. Mostra as opções para testar a configuração de monitorização.
* Para verificar que todos os passos do Azure Enhanced Monitoring foram executados com êxito e que a infraestrutura do Azure fornece os dados necessários, prossiga com a verificação de preparação para a extensão do Azure melhorada de monitorização para SAP, conforme descrito em [Verificação de disponibilidade para avançada monitorização do Azure para SAP][deployment-guide-5.1].
* Aguarde 15 a 30 minutos para obter um diagnóstico do Azure recolher os dados relevantes.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>CLI do Azure para VMs do Linux

Para instalar a extensão do Azure melhorada de monitorização para SAP com CLI do Azure:

   1. Instalar a CLI clássica do Azure, conforme descrito em [instalar o Azure CLI clássica][azure-cli].
   1. Inicie sessão com a sua conta do Azure:

      ```
      azure login
      ```

   1. Mude para o modo Azure Resource Manager:

      ```
      azure config mode arm
      ```

   1. Ative a monitorização avançada do Azure:

      ```
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Instalar a CLI do Azure 2.0

   1. Instalar a CLI 2.0 do Azure, conforme descrito em [instalar o Azure CLI 2.0][azure-cli-2].
   1. Inicie sessão com a sua conta do Azure:

      ```
      az login
      ```

   1. Instalar a extensão de AEM de CLI do Azure
  
      ```
      az extension add --name aem
      ```
  
   1. Instalar a extensão com
  
      ```
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Certifique-se de que a extensão de monitorização de avançada para Azure está ativa na VM do Linux do Azure. Verifique se o ficheiro \\var\\lib\\AzureEnhancedMonitor\\PerfCounters existe. Se existir, no prompt de comando, execute este comando para apresentar as informações recolhidas pelo Monitor avançada do Azure:

   ```
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   O resultado tem o seguinte aspeto:
   ```
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Verificações e resolução de problemas para monitorização de ponto a ponto

Depois de ter implementado a sua VM do Azure e configurar a infraestrutura de monitorização do Azure relevante, verifique se todos os componentes da extensão de monitorização de avançada para Azure funcionam conforme esperado.

Executar a verificação de preparação para a extensão do Azure melhorada de monitorização para SAP, conforme descrito em [verificação de disponibilidade para a extensão do Azure melhorada de monitorização para SAP][deployment-guide-5.1]. Se todos os resultados de verificação de preparação são positivos e todos os contadores de desempenho relevantes aparecem OK, a monitorização do Azure foi configurada com êxito. Pode continuar com a instalação do agente de anfitrião do SAP, tal como descrito nas notas para o SAP no [recursos SAP][deployment-guide-2.2]. Se a verificação de preparação para indicar que os contadores estão em falta, executar a verificação de estado de funcionamento da infraestrutura de monitorização do Azure, conforme descrito em [verificação de estado de funcionamento de configuração da infraestrutura de monitorização do Azure] [ deployment-guide-5.2]. Para obter mais opções de resolução de problemas, consulte [monitorização de resolução de problemas do Azure para SAP][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Verificação de disponibilidade para a extensão do Azure melhorada de monitorização para SAP

Esta verificação certifica-se de que todas as métricas de desempenho que aparecem dentro do seu aplicativo SAP são fornecidas pela infraestrutura de monitorização do Azure subjacente.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Executar a verificação de disponibilidade numa VM do Windows

1. Inicie sessão na máquina virtual do Azure (com uma conta de administrador não é necessário).
1. Abra uma janela de Linha de Comandos.
1. No prompt de comando, altere o diretório para a pasta de instalação do Enhanced Monitoring a extensão do Azure para SAP: C:\\pacotes\\plug-ins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;versão >\\soltar

   O *versão* no caminho para a extensão de monitorização pode variar. Se vir pastas para várias versões da extensão de monitorização na pasta de instalação, verifique a configuração do serviço Windows AzureEnhancedMonitoring e, em seguida, mude para a pasta indicada como *caminho para o executável* .

   ![Propriedades de serviço em execução avançada de monitorização a extensão do Azure para SAP][deployment-guide-figure-1000]

1. Na linha de comandos, execute **azperflib.exe** sem quaisquer parâmetros.

   > [!NOTE]
   > Azperflib.exe é executado num loop e atualiza os contadores coletados 60 em 60 segundos. Para encerrar o loop, feche a janela de linha de comandos.
   >
   >

Se a extensão de monitorização de avançada para do Azure não está instalada ou o serviço de AzureEnhancedMonitoring não está em execução, a extensão não foi configurada corretamente. Para obter informações detalhadas sobre como implementar a extensão, consulte [resolução de problemas da infraestrutura de monitorização do Azure para SAP][deployment-guide-5.3].

> [!NOTE]
> O Azperflib.exe é um componente que não pode ser utilizado para fins de próprios. É um componente que fornece dados relacionados com a VM para o agente de anfitrião do SAP de monitorização do Azure.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Verificar a saída do azperflib.exe

Saída de Azperflib.exe mostra que todos preenchidos contadores de desempenho do Azure para SAP. Na parte inferior da lista de contadores coletados, um indicador de estado de funcionamento e o resumo mostra o estado da monitorização do Azure.

![Resultado da verificação de estado de funcionamento executando azperflib.exe, que indica que não existam problemas][deployment-guide-figure-1100]
<a name="figure-11"></a>

Verificar o resultado retornado para o **total de contadores** saída, que é comunicada como vazio e, para **estado de funcionamento**, mostrado na figura anterior.

Interprete os valores resultantes da seguinte forma:

| Valores de resultado Azperflib.exe | Monitorização de estado de funcionamento do Azure |
| --- | --- |
| **Chamadas à API - não disponível** | Os contadores que não estão disponíveis podem não ser aplicável para a configuração de máquina virtual, ou erros. Ver **estado de funcionamento**. |
| **Total de contadores - vazio** |Os seguintes dois contadores de armazenamento do Azure podem estar vazios: <ul><li>Latência de operações de leitura do armazenamento de servidor MS</li><li>Latência de operações de leitura de armazenamento MS de E2E</li></ul>Todos os outros contadores tem de ter valores. |
| **Estado de funcionamento** |Apenas OK se devolver o estado mostra **OK**. |
| **Diagnóstico** |Obter informações detalhadas sobre o estado de funcionamento. |

Se o **estado de funcionamento** hodnota není **OK**, siga as instruções no [verificação de estado de funcionamento de configuração da infraestrutura de monitorização do Azure] [ deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Executar a verificação de disponibilidade numa VM do Linux

1. Para se ligar a máquina de Virtual do Azure através de SSH.

1. Verificar a saída da extensão de monitorização de avançada para Azure.

   a.  Execute `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Era esperado o resultado**: Devolve a lista de contadores de desempenho. O ficheiro não deve estar vazio.

   b. Execute `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Era esperado o resultado**: Uma linha de devolve onde está o erro **none**, por exemplo, **3; configuração; Erro; 0; 0; Nenhum; 0; 1456416792; tst-servercs;**

   c. Execute `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Era esperado o resultado**: Devolve vazia ou não existe.

Se a verificação anterior não foi concluída com êxito, execute estas verificações adicionais:

1. Certifique-se de que o waagent está instalado e ativado.

   a.  Execute `sudo ls -al /var/lib/waagent/`

     **Era esperado o resultado**: Lista o conteúdo do diretório waagent.

   b.  Execute `ps -ax | grep waagent`

   **Era esperado o resultado**: Apresenta uma entrada semelhante a: `python /usr/sbin/waagent -daemon`

1. Certifique-se de que a extensão de monitorização de avançada para Azure está instalado e em execução.

   a.  Execute `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Era esperado o resultado**: Lista o conteúdo do diretório de extensão de monitorização avançada do Azure.

   b. Execute `ps -ax | grep AzureEnhanced`

   **Era esperado o resultado**: Apresenta uma entrada semelhante a: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Instalar o agente de anfitrião do SAP, tal como descrito na nota SAP [1031096]e verificar a saída do `saposcol`.

   a.  Execute `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Execute `dump ccm`

   c.  Verifique se o **acesso de monitorização de Virtualization_Configuration\Enhanced** métrica é **verdadeiro**.

Se já tiver um servidor de aplicações SAP NetWeaver ABAP instalado, abra a transação ST06 e verifique se a monitorização avançada está ativada.

Se qualquer uma dessas verificações falhar e para obter informações detalhadas sobre como implementar novamente a extensão, consulte [resolução de problemas da infraestrutura de monitorização do Azure para SAP][deployment-guide-5.3].

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Estado de funcionamento Verifique a configuração de infraestrutura de monitorização do Azure

Se alguns da monitorização não os dados são entregues corretamente, conforme indicado pelo teste descrito em [verificação de disponibilidade para avançada monitorização do Azure para SAP][deployment-guide-5.1], execute o `Test-AzureRmVMAEMExtension` cmdlet para verificar se a Monitorização de infraestrutura e a extensão de monitorização para SAP do Azure está configurado corretamente.

1. Certifique-se de que instalou a versão mais recente do cmdlet do PowerShell do Azure, conforme descrito em [cmdlets de implementar o Azure PowerShell][deployment-guide-4.1].
1. Execute o seguinte cmdlet do PowerShell. Para obter uma lista dos ambientes disponíveis, execute o cmdlet `Get-AzureRmEnvironment`. Para utilizar o global Azure, selecione o **AzureCloud** ambiente. Para o Azure na China, selecione **AzureChinaCloud**.
   ```powershell
   $env = Get-AzureRmEnvironment -Name <name of the environment>
   Connect-AzureRmAccount -Environment $env
   Set-AzureRmContext -SubscriptionName <subscription name>
   Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Introduza os dados da sua conta e identificar a máquina virtual do Azure.

   ![Página de entrada do SAP específicas VMConfigForSAP_GUI de teste de cmdlet do Azure][deployment-guide-figure-1200]

1. O script testa a configuração da máquina virtual que selecionou.

   ![Saída de teste com êxito da infraestrutura de monitorização do Azure para SAP][deployment-guide-figure-1300]

Certifique-se de que todos os resultados da verificação de estado de funcionamento está **OK**. Se não são apresentadas algumas verificações **OK**, execute o cmdlet de atualização, conforme descrito na [configurar avançada de monitorização a extensão do Azure para SAP][deployment-guide-4.5]. Aguarde 15 minutos e repita as verificações descritas em [verificação de disponibilidade para avançada monitorização do Azure para SAP] [ deployment-guide-5.1] e [verificação de estado de funcionamento de configuração da infraestrutura de monitorização de Azure] [deployment-guide-5.2]. Se as verificações ainda indicarem um problema com alguns ou todos os contadores, veja [resolução de problemas da infraestrutura de monitorização do Azure para SAP][deployment-guide-5.3].

> [!Note]
> Pode experimentar alguns avisos nos casos em que o Managed Disks Standard do Azure que utilizar. Avisos serão apresentados em vez dos testes de retornar "OK". Isso é normal e previsto em caso desse tipo de disco. Consulte também [resolução de problemas da infraestrutura de monitorização do Azure para SAP][deployment-guide-5.3]
> 

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Resolução de problemas da infraestrutura de monitorização do Azure para SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Contadores de desempenho do Azure não aparecem em todos os

O serviço do Windows AzureEnhancedMonitoring recolhe métricas de desempenho no Azure. Se o serviço não foi instalado corretamente ou se não está em execução na sua VM, não existem métricas de desempenho podem ser recolhidas.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>O diretório de instalação da extensão de monitorização de avançada para Azure está vazio

###### <a name="issue"></a>Problema

O diretório de instalação c:\\pacotes\\plug-ins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;versão >\\lista está vazia.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se este é um problema de proxy (como descrito anteriormente). Poderá ter de reiniciar a máquina ou execute novamente o `Set-AzureRmVMAEMExtension` script de configuração.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Serviço de Azure Enhanced Monitoring não existe

###### <a name="issue"></a>Problema

O serviço Windows AzureEnhancedMonitoring não existe.

Saída de Azperflib.exe emite um erro:

![Execução de azperflib.exe indica que o serviço de extensão de monitorização de avançada para Azure para SAP não está em execução][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Solução

Se o serviço não existir, a extensão do Azure melhorada de monitorização para SAP não foi instalada corretamente. Voltar a implementar a extensão, utilizando os passos descritos para o seu cenário de implementação no [cenários de implementação de VMs para o SAP no Azure][deployment-guide-3].

Depois de implementar a extensão, depois de uma hora, verifique novamente se os contadores de desempenho do Azure são fornecidos na VM do Azure.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Serviço de Azure Enhanced Monitoring existe, mas não iniciar

###### <a name="issue"></a>Problema

O serviço Windows AzureEnhancedMonitoring existe e está ativado, mas não iniciar. Para obter mais informações, consulte o registo de eventos do aplicativo.

###### <a name="solution"></a>Solução

A configuração está incorreta. Reinicie a extensão de monitorização para a VM, conforme descrito em [configurar a extensão do Azure melhorada de monitorização para SAP][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Alguns contadores de desempenho do Azure estão em falta

O serviço do Windows AzureEnhancedMonitoring recolhe métricas de desempenho no Azure. O serviço obtém dados de várias origens. Alguns dados de configuração são recolhidos localmente e algumas métricas de desempenho são lidos a partir do diagnóstico do Azure. Contadores de armazenamento são utilizadas a partir do seu registro de log no nível da subscrição de armazenamento.

Se a resolução de problemas ao utilizar a nota SAP [1999351] não resolver o problema, execute novamente o `Set-AzureRmVMAEMExtension` script de configuração. Poderá ter de esperar uma hora, porque os contadores de análise ou diagnósticos de armazenamento podem não ser criados imediatamente após eles estão ativados. Se o problema persistir, abra uma mensagem de suporte de cliente SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Contadores de desempenho do Azure não aparecem em todos os

Métricas de desempenho no Azure são recolhidas por um daemon. Se o daemon não está em execução, não existem métricas de desempenho podem ser recolhidas.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>O diretório de instalação da extensão Azure Enhanced Monitoring está vazio

###### <a name="issue"></a>Problema

O diretório \\var\\lib\\waagent\\ não tem um subdiretório para a extensão Azure Enhanced Monitoring.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se este é um problema de proxy (como descrito anteriormente). Poderá ter de reiniciar a máquina e/ou volte a executar o `Set-AzureRmVMAEMExtension` script de configuração.

##### <a name="the-execution-of-set-azurermvmaemextension-and-test-azurermvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>A execução do Set-AzureRmVMAEMExtension e Test-AzureRmVMAEMExtension Mostrar mensagens de aviso a indicar que o Standard Managed Disks não são suportados

###### <a name="issue"></a>Problema

Quando são mostradas em execução Set-AzureRmVMAEMExtension ou de Test-AzureRmVMAEMExtension mensagens como esta:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Executar azperfli.exe, conforme descrito anteriormente pode obter um resultado que é que indica um mau estado. 

###### <a name="solution"></a>Solução

As mensagens são causadas pelo fato de que o Standard Managed Disks não fornecendo as APIs usadas pela extensão de monitorização para verificar as estatísticas das contas de armazenamento do Azure Standard. Não é uma questão de preocupação. Razão para introduzir a monitorização para contas de armazenamento de disco Standard foi limitação de e/s que ocorreu com frequência. Discos geridos evitará essa limitação ao limitar o número de discos numa conta de armazenamento. Por conseguinte, não ter esse tipo de dados de monitorização não é crítico.


#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Alguns contadores de desempenho do Azure estão em falta

Métricas de desempenho no Azure são recolhidas por um daemon, que obtém dados de várias origens. Alguns dados de configuração são recolhidos localmente e algumas métricas de desempenho são lidos a partir do diagnóstico do Azure. Contadores de armazenamento são provenientes os registos na sua subscrição de armazenamento.

Para obter uma lista completa e atualizada dos problemas conhecidos, consulte a nota SAP [1999351], que tem informações adicionais de resolução de problemas de avançada monitorização do Azure para SAP.

Se a resolução de problemas ao utilizar a nota SAP [1999351] não resolver o problema, volte a executar o `Set-AzureRmVMAEMExtension` conforme descrito num script de configuração [configurar avançada de monitorização a extensão do Azure para SAP] [deployment-guide-4.5]. Poderá ter de esperar por uma hora, porque os contadores de análise ou diagnósticos de armazenamento podem não ser criados imediatamente após eles estão ativados. Se o problema persistir, abra uma mensagem de suporte de cliente SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.
