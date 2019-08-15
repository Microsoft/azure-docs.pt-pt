---
title: Implantação de máquinas virtuais do Azure para SAP NetWeaver | Microsoft Docs
description: Saiba como implantar o software SAP em máquinas virtuais Linux no Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: gwallace
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
ms.openlocfilehash: 23f2d9add5838fc0c9ac5764921a2f3cc6ae7ab7
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67709901"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Implantação de máquinas virtuais do Azure para SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]: https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]: https://launchpad.support.sap.com/#/notes/1619720
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
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Implantação de DBMS de máquinas virtuais do Azure para SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Cache para VMs e VHDs)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (RAID de software)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Armazenamento do Microsoft Azure)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Estrutura de uma implantação de RDBMS)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Alta disponibilidade e recuperação de desastre com VMs do Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e posterior)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e versões anteriores)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Usando uma imagem SQL Server do Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Resumo geral de SQL Server para SAP no Azure)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Especificações para SQL Server RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configuração de armazenamento)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup e restauração)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Considerações de desempenho para backup e restauração)
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

[deployment-guide]:deployment-guide.md (Implantação de máquinas virtuais do Azure para SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Recursos do SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Implantando uma VM usando uma imagem personalizada)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Cenário 1: Implantando uma VM do Azure Marketplace para SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Cenário 2: Implantando uma VM com uma imagem personalizada para SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Cenário 3: Movendo uma VM do local usando um VHD do Azure não generalizado com o SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Cenários de implantação de VMs para SAP no Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Implantando cmdlets Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Baixar e importar cmdlets do PowerShell relevantes para o SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Ingressar uma VM em um domínio local-somente Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Baixar, instalar e habilitar o agente de VM do Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (CLI do Azure)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurar a extensão de monitoramento avançado do Azure para SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Verificação de preparação para o monitoramento avançado do Azure para SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Verificação de integridade para a infraestrutura de monitoramento do Azure)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Solução de problemas do monitoramento do Azure para SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configurar o monitoramento)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Verificações e solução de problemas para configurar o monitoramento de ponta a ponta)

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

[planning-guide]:planning-guide.md (Planejamento e implementação de máquinas virtuais do Azure para SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Os)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Alta disponibilidade e recuperação de desastre para SAP NetWeaver em execução em máquinas virtuais do Azure)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Alta disponibilidade para servidores de aplicativos SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Usando a inicialização automática para instâncias SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Somente em nuvem – implantações de máquinas virtuais no Azure sem dependências na rede do cliente local)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Entre instalações-implantação de uma ou várias VMs SAP no Azure totalmente integradas à rede local)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiões do Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domínios de falha)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domínios de atualização)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Conjuntos de disponibilidade do Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Conceito de máquinas virtuais Microsoft Azure)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Armazenamento Premium do Azure)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Movendo uma VM do local para o Azure com um disco não generalizado)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Implantando uma VM com uma imagem específica do cliente)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparação para mover uma VM do local para o Azure com um disco não generalizado)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparação para implantar uma VM com uma imagem específica do cliente para SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparando VMs com SAP para o Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Diferença entre um disco do Azure e uma imagem do Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Carregando um VHD do local para o Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copiando discos entre contas de armazenamento do Azure)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Estrutura de VM/VHD para implantações do SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Configurando a montagem automática para discos anexados)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Cenário de demonstração/treinamento de VM única com SAP NetWeaver)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Conceitos de implantação somente em nuvem de instâncias do SAP)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solução de monitoramento do Azure para SAP)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure rede)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Repositório Armazenamento do Microsoft Azure e discos de dados)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]: https://support.sap.com/pam (Matriz de disponibilidade de produto SAP)
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
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Implantar e gerenciar máquinas virtuais usando modelos de Azure Resource Manager e o CLI do Azure)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Gerenciar máquinas virtuais usando o Azure Resource Manager e o PowerShell)
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

As máquinas virtuais do Azure são a solução para organizações que precisam de recursos de computação e armazenamento, em tempo mínimo, e sem ciclos de aquisição longos. Você pode usar as máquinas virtuais do Azure para implantar aplicativos clássicos, como aplicativos baseados no SAP NetWeaver, no Azure. Estenda a confiabilidade e a disponibilidade de um aplicativo sem recursos locais adicionais. As máquinas virtuais do Azure dão suporte à conectividade entre locais, para que você possa integrar as máquinas virtuais do Azure nos domínios locais da sua organização, nuvens privadas e o cenário do sistema SAP.

Neste artigo, abordaremos as etapas para implantar aplicativos SAP em VMs (máquinas virtuais) no Azure, incluindo opções de implantação alternativas e solução de problemas. Este artigo baseia-se nas informações em [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide]. Ele também complementa a documentação de instalação do SAP e as notas SAP, que são os principais recursos para instalar e implantar o software SAP.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

A configuração de uma máquina virtual do Azure para implantação de software SAP envolve várias etapas e recursos. Antes de começar, verifique se você atende aos pré-requisitos para instalar o software SAP em máquinas virtuais no Azure.

### <a name="local-computer"></a>Computador local

Para gerenciar VMs Windows ou Linux, você pode usar um script do PowerShell e o portal do Azure. Para ambas as ferramentas, você precisa de um computador local executando o Windows 7 ou uma versão posterior do Windows. Se você quiser gerenciar apenas VMs do Linux e quiser usar um computador Linux para essa tarefa, poderá usar CLI do Azure.

### <a name="internet-connection"></a>Conexão com a Internet

Para baixar e executar as ferramentas e os scripts necessários para a implantação do software SAP, você deve estar conectado à Internet. A VM do Azure que está executando a extensão de monitoramento avançado do Azure para SAP também precisa de acesso à Internet. Se a VM do Azure fizer parte de uma rede virtual do Azure ou um domínio local, verifique se as configurações de proxy relevantes estão definidas, conforme descrito em [Configurar o proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Subscrição do Microsoft Azure

Você precisa de uma conta ativa do Azure.

### <a name="topology-and-networking"></a>Topologia e rede

Você precisa definir a topologia e a arquitetura da implantação do SAP no Azure:

* Contas de armazenamento do Azure a serem usadas
* Rede virtual em que você deseja implantar o sistema SAP
* Grupo de recursos no qual você deseja implantar o sistema SAP
* Região do Azure onde você deseja implantar o sistema SAP
* Configuração do SAP (duas ou três camadas)
* Tamanhos de VM e o número de discos de dados adicionais a serem montados nas VMs
* Configuração de CTS (SAP Correction and transporte System)

Crie e configure as contas de armazenamento do Azure (se necessário) ou as redes virtuais do Azure antes de começar o processo de implantação de software SAP. Para obter informações sobre como criar e configurar esses recursos, consulte [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Dimensionamento do SAP

Conheça as seguintes informações para o dimensionamento do SAP:

* Carga de trabalho SAP projetada, por exemplo, usando a ferramenta de dimensionador rápido do SAP e o número de SAPS (SAP Application Performance Standard)
* Recursos de CPU e consumo de memória necessários do sistema SAP
* Operações de e/s (entrada/saída) necessárias por segundo
* Largura de banda de rede necessária para a eventual comunicação entre as VMs no Azure
* Largura de banda de rede necessária entre ativos locais e o sistema SAP implantado pelo Azure

### <a name="resource-groups"></a>Grupos de recursos

No Azure Resource Manager, você pode usar grupos de recursos para gerenciar todos os recursos do aplicativo em sua assinatura do Azure. Para obter mais informações, veja [Descrição geral do Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Recursos

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos do SAP

Ao configurar sua implantação de software SAP, você precisa dos seguintes recursos SAP:

* Nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure com suporte para a implantação de software SAP
  * Informações de capacidade importantes para tamanhos de VM do Azure
  * Software SAP com suporte e combinações de so (sistema operacional) e banco de dados
  * Versão de kernel do SAP necessária para Windows e Linux em Microsoft Azure

* O SAP Note [2015553] lista os pré-requisitos para implantações de software SAP com suporte no SAP no Azure.
* A nota SAP [2178632] tem informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure.
* A nota SAP [1409604] tem a versão do agente de host do SAP necessária para Windows no Azure.
* A nota SAP [2191498] tem a versão do agente de host do SAP necessária para Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento SAP no Linux no Azure.
* A nota SAP [1984787] tem informações gerais sobre o SuSE Linux Enterprise Server 12.
* A observação do SAP [2002167] tem informações gerais sobre o Red Hat Enterprise Linux 7. x.
* A observação do SAP [2069760] tem informações gerais sobre o Oracle Linux 7. x.
* A nota SAP [1999351] tem informações adicionais para solução de problemas para a extensão de monitoramento avançado do Azure para SAP.
* O SAP Note [1597355] tem informações gerais sobre o espaço de permuta para Linux.
* A [página de SCN do SAP no Azure](https://wiki.scn.sap.com/wiki/x/Pia7Gg) tem notícias e uma coleção de recursos úteis.
* O [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* Cmdlets do PowerShell específicos do SAP que fazem parte do [Azure PowerShell][azure-ps].
* Comandos CLI do Azure específicos do SAP que fazem parte do [CLI do Azure][azure-cli].

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos do Windows

Estes artigos da Microsoft abrangem implantações do SAP no Azure:

* [Planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP NetWeaver (este artigo)][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Cenários de implantação para software SAP em VMs do Azure

Você tem várias opções para implantar VMs e discos associados no Azure. É importante entender as diferenças entre as opções de implantação, pois você pode executar etapas diferentes para preparar suas VMs para implantação com base no tipo de implantação escolhido.

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Cenário 1: Implantando uma VM do Azure Marketplace para SAP

Você pode usar uma imagem fornecida pela Microsoft ou por terceiros no Azure Marketplace para implantar sua VM. O Marketplace oferece algumas imagens padrão do sistema operacional do Windows Server e diferentes distribuições do Linux. Você também pode implantar uma imagem que inclui SKUs do sistema de gerenciamento de banco de dados (DBMS), por exemplo, Microsoft SQL Server. Para obter mais informações sobre como usar imagens com SKUs do DBMS, consulte [implantação de DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide].

O fluxograma a seguir mostra a sequência de etapas específicas do SAP para implantar uma VM do Azure Marketplace:

![Fluxograma de implantação de VM para sistemas SAP usando uma imagem de VM do Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Criar uma máquina virtual usando o portal do Azure

A maneira mais fácil de criar uma nova máquina virtual com uma imagem do Azure Marketplace é usando o portal do Azure.

1.  Aceda a <https://portal.azure.com/#create/hub>.  Ou, no menu portal do Azure, selecione **+ novo**.
1.  Selecione **computação**e, em seguida, selecione o tipo de sistema operacional que você deseja implantar. Por exemplo, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) ou Oracle Linux 7,2. A exibição de lista padrão não mostra todos os sistemas operacionais com suporte. Selecione **ver tudo** para obter uma lista completa. Para obter mais informações sobre os sistemas operacionais com suporte para a implantação de software SAP, consulte a observação do SAP [1928533].
1.  Na próxima página, examine os termos e condições.
1.  Na caixa **selecionar um modelo de implantação** , selecione **Gerenciador de recursos**.
1.  Selecione **Criar**.

O assistente orienta você pela definição dos parâmetros necessários para criar a máquina virtual, além de todos os recursos necessários, como interfaces de rede e contas de armazenamento. Alguns desses parâmetros são:

1. **Noções básicas**:
   * **Nome**: O nome do recurso (o nome da máquina virtual).
   * **Tipo de disco da VM**: Selecione o tipo de disco do disco do sistema operacional. Se você quiser usar o armazenamento Premium para seus discos de dados, é recomendável usar o armazenamento Premium para o disco do sistema operacional também.
   * **Nome de usuário e senha** ou **chave pública SSH**: Insira o nome de usuário e a senha que é criado durante o provisionamento. Para uma máquina virtual do Linux, você pode inserir a chave pública de Secure Shell (SSH) que você usa para entrar no computador.
   * **Assinatura**: Selecione a assinatura que você deseja usar para provisionar a nova máquina virtual.
   * **Grupo de recursos**: O nome do grupo de recursos para a VM. Você pode inserir o nome de um novo grupo de recursos ou o nome de um grupo de recursos que já existe.
   * **Local**: Onde implantar a nova máquina virtual. Se você quiser conectar a máquina virtual à sua rede local, certifique-se de selecionar o local da rede virtual que conecta o Azure à sua rede local. Para obter mais informações, consulte [Microsoft Azure rede][planning-guide-microsoft-azure-networking] em [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide].
1. **Tamanho**:

     Para obter uma lista de tipos de VM com suporte, consulte a observação do SAP [1928533]. Certifique-se de selecionar o tipo de VM correto se desejar usar o armazenamento Premium do Azure. Nem todos os tipos de VM dão suporte ao armazenamento Premium. Para obter mais informações, [consulte armazenamento: Armazenamento do Microsoft Azure e discos][planning-guide-storage-microsoft-azure-storage-and-data-disks] de dados e o [armazenamento Premium do Azure][planning-guide-azure-premium-storage] no [planejamento e na implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide].

1. **Definições**:
   * **Armazenamento**
     * **Tipo de disco**: Selecione o tipo de disco do disco do sistema operacional. Se você quiser usar o armazenamento Premium para seus discos de dados, é recomendável usar o armazenamento Premium para o disco do sistema operacional também.
     * **Usar discos gerenciados**: Se você quiser usar Managed Disks, selecione Sim. Para obter mais informações sobre Managed Disks, consulte o capítulo [Managed disks][planning-guide-managed-disks] no guia de planejamento.
     * **Conta de armazenamento**: Selecione uma conta de armazenamento existente ou crie uma nova. Nem todos os tipos de armazenamento funcionam para a execução de aplicativos SAP. Para obter mais informações sobre tipos de armazenamento, consulte [estrutura de armazenamento de uma VM para implantações de RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Rede**
     * **Rede virtual** e **sub-rede**: Para integrar a máquina virtual à sua intranet, selecione a rede virtual que está conectada à sua rede local.
     * **Endereço IP público**: Selecione o endereço IP público que você deseja usar ou insira parâmetros para criar um novo endereço IP público. Você pode usar um endereço IP público para acessar sua máquina virtual pela Internet. Certifique-se de também criar um grupo de segurança de rede para ajudar a proteger o acesso à sua máquina virtual.
     * **Grupo de segurança de rede**: Para obter mais informações, consulte [controlar o fluxo de tráfego de rede com grupos de segurança de rede][virtual-networks-nsg].
   * **Extensões**: Você pode instalar extensões de máquina virtual adicionando-as à implantação. Você não precisa adicionar extensões nesta etapa. As extensões necessárias para o suporte do SAP são instaladas posteriormente. Consulte [o capítulo configurar a extensão de monitoramento avançado do Azure para SAP][deployment-guide-4.5] neste guia.
   * **Alta disponibilidade**: Selecione um conjunto de disponibilidade ou insira os parâmetros para criar um novo conjunto de disponibilidade. Para obter mais informações, consulte [conjuntos de disponibilidade do Azure][planning-guide-3.2.3].
   * **Monitorização**
     * **Diagnóstico de inicialização**: Você pode selecionar **desabilitar** para o diagnóstico de inicialização.
     * **Diagnóstico do SO convidado**: Você pode selecionar **desabilitar** para monitoramento de diagnóstico.

1. **Resumo**:

   Examine suas seleções e selecione **OK**.

Sua máquina virtual está implantada no grupo de recursos selecionado.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Criar uma máquina virtual usando um modelo

Você pode criar uma máquina virtual usando um dos modelos SAP publicados no [repositório GitHub Azure-QuickStart-templates][azure-quickstart-templates-github]. Você também pode criar manualmente uma máquina virtual usando o [portal do Azure][virtual-machines-windows-tutorial], o [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]ou o [CLI do Azure][virtual-machines-linux-tutorial].

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (SAP-2-Tier-Marketplace-Image)][sap-templates-2-tier-marketplace-image]

  Para criar um sistema de duas camadas usando apenas uma máquina virtual, use este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)-Managed disks** (SAP-2-Tier-Marketplace-Image-MD)][sap-templates-2-tier-marketplace-image-md]

  Para criar um sistema de duas camadas usando apenas uma máquina virtual e Managed Disks, use este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais)** (SAP-3-Tier-Marketplace-Image)][sap-templates-3-tier-marketplace-image]

  Para criar um sistema de três camadas usando várias máquinas virtuais, use este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais)-Managed disks** (SAP-3-Tier-Marketplace-Image-MD)][sap-templates-3-tier-marketplace-image-md]

  Para criar um sistema de três camadas usando várias máquinas virtuais e Managed Disks, use este modelo.

Na portal do Azure, insira os seguintes parâmetros para o modelo:

1. **Noções básicas**:
   * **Assinatura**: A assinatura a ser usada para implantar o modelo.
   * **Grupo de recursos**: O grupo de recursos a ser usado para implantar o modelo. Você pode criar um novo grupo de recursos ou pode selecionar um grupo de recursos existente na assinatura.
   * **Local**: Onde implantar o modelo. Se você selecionou um grupo de recursos existente, o local desse grupo de recursos será usado.

1. **Definições**:
   * **ID do sistema SAP**: A ID do sistema SAP (SID).
   * **Tipo de so**: O sistema operacional que você deseja implantar, por exemplo, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) ou Oracle Linux 7,2.

     A exibição de lista não mostra todos os sistemas operacionais com suporte. Para obter mais informações sobre os sistemas operacionais com suporte para a implantação de software SAP, consulte a observação do SAP [1928533].
   * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS que o novo sistema fornece. Se você não tiver certeza de quantos SAPS o sistema precisará, pergunte ao seu parceiro de tecnologia SAP ou ao integrador de sistemas.
   * **Disponibilidade do sistema** (somente modelo de três camadas): A disponibilidade do sistema.

     Selecione **ha** para uma configuração adequada para uma instalação de alta disponibilidade. Dois servidores de banco de dados e dois servidores para ABAP SAP central Services (ASCS) são criados.
   * **Tipo de armazenamento** (somente modelo de duas camadas): O tipo de armazenamento a ser usado.

     Para sistemas maiores, é altamente recomendável usar o armazenamento Premium do Azure. Para obter mais informações sobre tipos de armazenamento, consulte estes recursos:
      * [Uso do armazenamento de SSD Premium do Azure para instância do SAP DBMS][2367194]
      * [Estrutura de armazenamento de uma VM para implantações de RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Armazenamento do Microsoft Azure][storage-introduction]
   * **Nome de usuário do administrador** e **senha do administrador**: Um nome de usuário e uma senha.
     Um novo usuário é criado para entrar na máquina virtual.
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e sub-rede são criadas ou uma sub-rede existente é usada. Se você já tiver uma rede virtual conectada à sua rede local, selecione **existente**.
   * **ID da sub-rede**: Se você deseja implantar a VM em uma VNet existente em que você tem uma sub-rede definida, a VM deve ser atribuída, nomear a ID dessa sub-rede específica. A ID geralmente tem esta aparência:/subscriptions/&lt;ID da assinatura >&lt;nome do grupo de recursos&lt;/resourceGroups/>/Providers/Microsoft.Network/virtualNetworks/nome da rede virtual >/Subnets/&lt;nome da sub-rede >

1. **Termos e condições**:  
    Examine e aceite os termos legais.

1. Selecione **Comprar**.

O agente de VM do Azure é implantado por padrão quando você usa uma imagem do Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Definir configurações de proxy

Dependendo de como sua rede local está configurada, talvez seja necessário configurar o proxy em sua VM. Se sua VM estiver conectada à sua rede local via VPN ou ExpressRoute, a VM poderá não ser capaz de acessar a Internet e não poderá baixar as extensões necessárias ou coletar dados de monitoramento. Para obter mais informações, consulte [Configurar o proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Ingressar em um domínio (somente Windows)

Se sua implantação do Azure estiver conectada a uma instância de DNS ou Active Directory local por meio de uma conexão VPN site a site do Azure ou ExpressRoute (isso é chamado de *entre instalações* em [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide]), espera-se que a VM ingresse em um domínio local. Para obter mais informações sobre as considerações para essa tarefa, consulte [unir uma VM a um domínio local (somente Windows)][deployment-guide-4.3].

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configurar o monitoramento

Para ter certeza de que o SAP dá suporte ao seu ambiente, configure a extensão de monitoramento do Azure para SAP, conforme descrito em [Configurar a extensão de monitoramento avançado do Azure para SAP][deployment-guide-4.5]. Verifique os pré-requisitos para monitoramento do SAP e as versões mínimas necessárias do kernel do SAP e do agente de host SAP, nos recursos listados em [recursos do SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Verificação de monitoramento

Verifique se o monitoramento está funcionando, conforme descrito em [verificações e solução de problemas para configurar o monitoramento de ponta a ponta][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Etapas pós-implantação

Depois de criar a VM e a VM ser implantada, você precisa instalar os componentes de software necessários na VM. Devido à sequência de implantação/instalação de software nesse tipo de implantação de VM, o software a ser instalado já deve estar disponível, no Azure, em outra VM ou como um disco que possa ser anexado. Ou, considere o uso de um cenário entre instalações, no qual a conectividade com os ativos locais (compartilhamentos de instalação) é fornecida.

Depois de implantar sua VM no Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP em sua VM como você faria em um ambiente local. Para instalar o software SAP em uma VM do Azure, a SAP e a Microsoft recomendam que você carregue e armazene a mídia de instalação do SAP em VHDs do Azure ou Managed Disks, ou que você crie uma VM do Azure que funcione como um servidor de arquivos que tenha toda a mídia de instalação do SAP necessária.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Cenário 2: Implantando uma VM com uma imagem personalizada para SAP

Como versões diferentes de um sistema operacional ou DBMS têm requisitos de patch diferentes, as imagens encontradas no Azure Marketplace podem não atender às suas necessidades. Em vez disso, você pode querer criar uma VM usando sua própria imagem de VM do so/DBMS, que pode ser implantada novamente mais tarde.
Você usa etapas diferentes para criar uma imagem privada para Linux do que criar uma para o Windows.

---
> ![Windows][Logo_Windows] Windows
>
> Para preparar uma imagem do Windows que você pode usar para implantar várias máquinas virtuais, as configurações do Windows (como o nome de host e SID do Windows) devem ser abstratas ou generalizadas na VM local. Você pode usar o [Sysprep](https://msdn.microsoft.com/library/hh825084.aspx) para fazer isso.
>
> ![Linux][Logo_Linux] Linux
>
> Para preparar uma imagem do Linux que você pode usar para implantar várias máquinas virtuais, algumas configurações do Linux devem ser abstratas ou generalizadas na VM local. Você pode usar `waagent -deprovision` para fazer isso. Para obter mais informações, consulte [capturar uma máquina virtual Linux em execução no Azure][virtual-machines-linux-capture-image] e o [Guia do usuário do agente Linux do Azure][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
Você pode preparar e criar uma imagem personalizada e, em seguida, usá-la para criar várias novas VMs. Isso é descrito em [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide]. Configure o conteúdo do banco de dados usando o Gerenciador de provisionamento de software SAP para instalar um novo sistema SAP (restaura um backup de banco de dados de um disco que está anexado à máquina virtual) ou restaurando diretamente um backup de banco de dados do armazenamento do Azure, se o DBMS dá suporte a ele. Para obter mais informações, consulte [implementação de DBMS de máquinas virtuais do Azure para SAP NetWeaver][dbms-guide]. Se você já tiver instalado um sistema SAP em sua VM local (especialmente para sistemas de duas camadas), poderá adaptar as configurações do sistema SAP após a implantação da VM do Azure usando o procedimento de renomeação do sistema com suporte do SAP (Gerenciador de provisionamento de software) da SAP Observação [1619720]). Caso contrário, você pode instalar o software SAP depois de implantar a VM do Azure.

O fluxograma a seguir mostra a sequência de etapas específicas do SAP para implantar uma VM de uma imagem personalizada:

![Fluxograma de implantação de VM para sistemas SAP usando uma imagem de VM no Marketplace particular][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Criar uma máquina virtual usando o portal do Azure

A maneira mais fácil de criar uma nova máquina virtual por meio de uma imagem de disco gerenciado é usando o portal do Azure. Para obter mais informações sobre como criar uma imagem de disco de gerenciamento, leia [capturar uma imagem gerenciada de uma VM generalizada no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Aceda a <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Ou, no menu portal do Azure, selecione **imagens**.
1.  Selecione a imagem de disco gerenciado que você deseja implantar e clique em **criar VM**

O assistente orienta você pela definição dos parâmetros necessários para criar a máquina virtual, além de todos os recursos necessários, como interfaces de rede e contas de armazenamento. Alguns desses parâmetros são:

1. **Noções básicas**:
   * **Nome**: O nome do recurso (o nome da máquina virtual).
   * **Tipo de disco da VM**: Selecione o tipo de disco do disco do sistema operacional. Se você quiser usar o armazenamento Premium para seus discos de dados, é recomendável usar o armazenamento Premium para o disco do sistema operacional também.
   * **Nome de usuário e senha** ou **chave pública SSH**: Insira o nome de usuário e a senha que é criado durante o provisionamento. Para uma máquina virtual do Linux, você pode inserir a chave pública de Secure Shell (SSH) que você usa para entrar no computador.
   * **Assinatura**: Selecione a assinatura que você deseja usar para provisionar a nova máquina virtual.
   * **Grupo de recursos**: O nome do grupo de recursos para a VM. Você pode inserir o nome de um novo grupo de recursos ou o nome de um grupo de recursos que já existe.
   * **Local**: Onde implantar a nova máquina virtual. Se você quiser conectar a máquina virtual à sua rede local, certifique-se de selecionar o local da rede virtual que conecta o Azure à sua rede local. Para obter mais informações, consulte [Microsoft Azure rede][planning-guide-microsoft-azure-networking] em [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide].
1. **Tamanho**:

     Para obter uma lista de tipos de VM com suporte, consulte a observação do SAP [1928533]. Certifique-se de selecionar o tipo de VM correto se desejar usar o armazenamento Premium do Azure. Nem todos os tipos de VM dão suporte ao armazenamento Premium. Para obter mais informações, [consulte armazenamento: Armazenamento do Microsoft Azure e discos][planning-guide-storage-microsoft-azure-storage-and-data-disks] de dados e o [armazenamento Premium do Azure][planning-guide-azure-premium-storage] no [planejamento e na implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide].

1. **Definições**:
   * **Armazenamento**
     * **Tipo de disco**: Selecione o tipo de disco do disco do sistema operacional. Se você quiser usar o armazenamento Premium para seus discos de dados, é recomendável usar o armazenamento Premium para o disco do sistema operacional também.
     * **Usar discos gerenciados**: Se você quiser usar Managed Disks, selecione Sim. Para obter mais informações sobre Managed Disks, consulte o capítulo [Managed disks][planning-guide-managed-disks] no guia de planejamento.
   * **Rede**
     * **Rede virtual** e **sub-rede**: Para integrar a máquina virtual à sua intranet, selecione a rede virtual que está conectada à sua rede local.
     * **Endereço IP público**: Selecione o endereço IP público que você deseja usar ou insira parâmetros para criar um novo endereço IP público. Você pode usar um endereço IP público para acessar sua máquina virtual pela Internet. Certifique-se de também criar um grupo de segurança de rede para ajudar a proteger o acesso à sua máquina virtual.
     * **Grupo de segurança de rede**: Para obter mais informações, consulte [controlar o fluxo de tráfego de rede com grupos de segurança de rede][virtual-networks-nsg].
   * **Extensões**: Você pode instalar extensões de máquina virtual adicionando-as à implantação. Você não precisa adicionar a extensão nesta etapa. As extensões necessárias para o suporte do SAP são instaladas posteriormente. Consulte [o capítulo configurar a extensão de monitoramento avançado do Azure para SAP][deployment-guide-4.5] neste guia.
   * **Alta disponibilidade**: Selecione um conjunto de disponibilidade ou insira os parâmetros para criar um novo conjunto de disponibilidade. Para obter mais informações, consulte [conjuntos de disponibilidade do Azure][planning-guide-3.2.3].
   * **Monitorização**
     * **Diagnóstico de inicialização**: Você pode selecionar **desabilitar** para o diagnóstico de inicialização.
     * **Diagnóstico do SO convidado**: Você pode selecionar **desabilitar** para monitoramento de diagnóstico.

1. **Resumo**:

   Examine suas seleções e selecione **OK**.

Sua máquina virtual está implantada no grupo de recursos selecionado.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Criar uma máquina virtual usando um modelo

Para criar uma implantação usando uma imagem de sistema operacional privada do portal do Azure, use um dos modelos SAP a seguir. Esses modelos são publicados no [repositório Azure-QuickStart-templates do GitHub][azure-quickstart-templates-github]. Você também pode criar manualmente uma máquina virtual usando o [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (SAP-2-Tier-User-Image)][sap-templates-2-tier-user-image]

  Para criar um sistema de duas camadas usando apenas uma máquina virtual, use este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)-imagem de disco gerenciado** (SAP-2-Tier-User-Image-MD)][sap-templates-2-tier-user-image-md]

  Para criar um sistema de duas camadas usando apenas uma máquina virtual e uma imagem de disco gerenciado, use este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais)** (SAP-3-Tier-User-Image)][sap-templates-3-tier-user-image]

  Para criar um sistema de três camadas usando várias máquinas virtuais ou sua própria imagem de sistema operacional, use este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais) – imagem de disco gerenciada** (SAP-3-Tier-User-Image-MD)][sap-templates-3-tier-user-image-md]

  Para criar um sistema de três camadas usando várias máquinas virtuais ou sua própria imagem de so e uma imagem de disco gerenciado, use este modelo.

Na portal do Azure, insira os seguintes parâmetros para o modelo:

1. **Noções básicas**:
   * **Assinatura**: A assinatura a ser usada para implantar o modelo.
   * **Grupo de recursos**: O grupo de recursos a ser usado para implantar o modelo. Você pode criar um novo grupo de recursos ou selecionar um grupo de recursos existente na assinatura.
   * **Local**: Onde implantar o modelo. Se você selecionou um grupo de recursos existente, o local desse grupo de recursos será usado.
1. **Definições**:
   * **ID do sistema SAP**: A ID do sistema SAP.
   * **Tipo de so**: O tipo de sistema operacional que você deseja implantar (Windows ou Linux).
   * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS que o novo sistema fornece. Se você não tiver certeza de quantos SAPS o sistema precisará, pergunte ao seu parceiro de tecnologia SAP ou ao integrador de sistemas.
   * **Disponibilidade do sistema** (somente modelo de três camadas): A disponibilidade do sistema.

     Selecione **ha** para uma configuração adequada para uma instalação de alta disponibilidade. Dois servidores de banco de dados e dois servidores para ASCS são criados.
   * **Tipo de armazenamento** (somente modelo de duas camadas): O tipo de armazenamento a ser usado.

     Para sistemas maiores, é altamente recomendável usar o armazenamento Premium do Azure. Para obter mais informações sobre tipos de armazenamento, consulte os seguintes recursos:
      * [Uso do armazenamento de SSD Premium do Azure para instância do SAP DBMS][2367194]
      * [Estrutura de armazenamento de uma VM para implantações de RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Armazenamento do Microsoft Azure][storage-introduction]
   * **URI do VHD da imagem do usuário** (somente modelo de imagem de disco não gerenciado): O URI do VHD de imagem do sistema operacional privado, por exemplo&lt;, https://AccountName >. blob. Core. Windows. net/VHDs/userimage. vhd.
   * **Conta de armazenamento de imagem do usuário** (somente modelo de imagem de disco não gerenciado): O nome da conta de armazenamento em que a imagem do sistema operacional particular é armazenada &lt;, por exemplo,&lt;AccountName > em https://AccountName >. blob. Core. Windows. net/VHDs/userimage. vhd.
   * userimageid (somente modelo de imagem de disco gerenciado): ID da imagem de disco gerenciado que você deseja usar
   * **Nome de usuário do administrador** e **senha do administrador**: O nome de usuário e a senha.

     Um novo usuário é criado para entrar na máquina virtual.
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e uma sub-rede são criadas ou se uma sub-rede existente é usada. Se você já tiver uma rede virtual conectada à sua rede local, selecione **existente**.
   * **ID da sub-rede**: Se você deseja implantar a VM em uma VNet existente em que você tem uma sub-rede definida, a VM deve ser atribuída, nomear a ID dessa sub-rede específica. A ID geralmente tem esta aparência:/subscriptions/&lt;ID da assinatura >&lt;nome do grupo de recursos&lt;/resourceGroups/>/Providers/Microsoft.Network/virtualNetworks/nome da rede virtual >/Subnets/&lt;nome da sub-rede >

1. **Termos e condições**:  
    Examine e aceite os termos legais.

1. Selecione **Comprar**.

#### <a name="install-the-vm-agent-linux-only"></a>Instalar o agente de VM (somente Linux)

Para usar os modelos descritos na seção anterior, o agente do Linux já deve estar instalado na imagem do usuário ou a implantação falhará. Baixe e instale o agente de VM na imagem do usuário, conforme descrito em [baixar, instalar e habilitar o agente de VM do Azure][deployment-guide-4.4]. Se você não usar os modelos, também poderá instalar o agente de VM mais tarde.

#### <a name="join-a-domain-windows-only"></a>Ingressar em um domínio (somente Windows)

Se sua implantação do Azure estiver conectada a uma instância de DNS ou Active Directory local por meio de uma conexão VPN site a site do Azure ou Azure ExpressRoute (isso é chamado de *entre instalações* em [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide]), espera-se que a VM ingresse em um domínio local. Para obter mais informações sobre as considerações para esta etapa, consulte [unir uma VM a um domínio local (somente Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Definir configurações de proxy

Dependendo de como sua rede local está configurada, talvez seja necessário configurar o proxy em sua VM. Se sua VM estiver conectada à sua rede local via VPN ou ExpressRoute, a VM poderá não ser capaz de acessar a Internet e não poderá baixar as extensões necessárias ou coletar dados de monitoramento. Para obter mais informações, consulte [Configurar o proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Configurar a monitorização

Para ter certeza de que o SAP dá suporte ao seu ambiente, configure a extensão de monitoramento do Azure para SAP, conforme descrito em [Configurar a extensão de monitoramento avançado do Azure para SAP][deployment-guide-4.5]. Verifique os pré-requisitos para monitoramento do SAP e as versões mínimas necessárias do kernel do SAP e do agente de host SAP, nos recursos listados em [recursos do SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Verificação de monitoramento

Verifique se o monitoramento está funcionando, conforme descrito em [verificações e solução de problemas para configurar o monitoramento de ponta a ponta][deployment-guide-troubleshooting-chapter].


### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Cenário 3: Movendo uma VM local usando um VHD do Azure não generalizado com o SAP

Nesse cenário, você planeja mover um sistema SAP específico de um ambiente local para o Azure. Você pode fazer isso carregando o VHD que tem o sistema operacional, os binários do SAP e, eventualmente, os binários do DBMS, além dos VHDs com os arquivos de dados e de log do DBMS, para o Azure. Ao contrário do cenário descrito [no cenário 2: Implantando uma VM com uma imagem personalizada para][deployment-guide-3.3]SAP, nesse caso, você mantém o nome do host, o SID do SAP e as contas de usuário SAP na VM do Azure, pois eles foram configurados no ambiente local. Você não precisa generalizar o sistema operacional. Esse cenário aplica-se com mais frequência a cenários entre instalações em que parte da estrutura SAP é executada localmente e parte dela é executada no Azure.

Nesse cenário, o agente de VM **não** é instalado automaticamente durante a implantação. Como o agente de VM e a extensão de monitoramento avançado do Azure para SAP são necessários para executar o SAP NetWeaver no Azure, você precisa baixar, instalar e habilitar os dois componentes manualmente depois de criar a máquina virtual.

Para obter mais informações sobre o agente de VM do Azure, consulte os recursos a seguir.

---
> ![Windows][Logo_Windows] Windows
>
> [Visão geral do agente de máquina virtual do Azure][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Guia do usuário do agente Linux do Azure][virtual-machines-linux-agent-user-guide]
>
>

---

O fluxograma a seguir mostra a sequência de etapas para mover uma VM local usando um VHD do Azure não generalizado:

![Fluxograma de implantação de VM para sistemas SAP usando um disco de VM][deployment-guide-figure-400]

Se o disco já tiver sido carregado e definido no Azure (consulte [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide]), execute as tarefas descritas nas próximas seções.

#### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para criar uma implantação usando um disco de sistema operacional privado por meio do portal do Azure, use o modelo SAP publicado no [repositório Azure-QuickStart-templates do GitHub][azure-quickstart-templates-github]. Você também pode criar manualmente uma máquina virtual usando o PowerShell.

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (SAP-2-Tier-User-Disk)][sap-templates-2-tier-os-disk]

  Para criar um sistema de duas camadas usando apenas uma máquina virtual, use este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual) – disco gerenciado** (SAP-2-Tier-User-Disk-MD)][sap-templates-2-tier-os-disk-md]

  Para criar um sistema de duas camadas usando apenas uma máquina virtual e um disco gerenciado, use este modelo.

Na portal do Azure, insira os seguintes parâmetros para o modelo:

1. **Noções básicas**:
   * **Assinatura**: A assinatura a ser usada para implantar o modelo.
   * **Grupo de recursos**: O grupo de recursos a ser usado para implantar o modelo. Você pode criar um novo grupo de recursos ou selecionar um grupo de recursos existente na assinatura.
   * **Local**: Onde implantar o modelo. Se você selecionou um grupo de recursos existente, o local desse grupo de recursos será usado.
1. **Definições**:
   * **ID do sistema SAP**: A ID do sistema SAP.
   * **Tipo de so**: O tipo de sistema operacional que você deseja implantar (Windows ou Linux).
   * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS que o novo sistema fornece. Se você não tiver certeza de quantos SAPS o sistema precisará, pergunte ao seu parceiro de tecnologia SAP ou ao integrador de sistemas.
   * **Tipo de armazenamento** (somente modelo de duas camadas): O tipo de armazenamento a ser usado.

     Para sistemas maiores, é altamente recomendável usar o armazenamento Premium do Azure. Para obter mais informações sobre tipos de armazenamento, consulte os seguintes recursos:
      * [Uso do armazenamento de SSD Premium do Azure para instância do SAP DBMS][2367194]
      * [Estrutura de armazenamento de uma VM para implantações de RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Armazenamento do Microsoft Azure][storage-introduction]
   * **URI do VHD de disco do so** (somente modelo de disco não gerenciado): O URI do disco do sistema operacional privado, por exemplo,&lt;https://AccountName >. blob. Core. Windows. net/VHDs/OSDisk. vhd.
   * **ID do disco gerenciado do disco do so** (somente modelo de disco gerenciado): A ID do disco do sistema operacional do disco gerenciado,/subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e sub-rede são criadas ou se uma sub-rede existente é usada. Se você já tiver uma rede virtual conectada à sua rede local, selecione **existente**.
   * **ID da sub-rede**: Se você deseja implantar a VM em uma VNet existente em que você tem uma sub-rede definida, a VM deve ser atribuída, nomear a ID dessa sub-rede específica. A ID geralmente tem esta aparência:/subscriptions/&lt;ID da assinatura >&lt;nome do grupo de recursos&lt;/resourceGroups/>/Providers/Microsoft.Network/virtualNetworks/nome da rede virtual >/Subnets/&lt;nome da sub-rede >

1. **Termos e condições**:  
    Examine e aceite os termos legais.

1. Selecione **Comprar**.

#### <a name="install-the-vm-agent"></a>Instalar o agente de VM

Para usar os modelos descritos na seção anterior, o agente de VM deve ser instalado no disco do sistema operacional ou a implantação falhará. Baixe e instale o agente de VM na VM, conforme descrito em [baixar, instalar e habilitar o agente de VM do Azure][deployment-guide-4.4].

Se você não usar os modelos descritos na seção anterior, também poderá instalar o agente de VM posteriormente.

#### <a name="join-a-domain-windows-only"></a>Ingressar em um domínio (somente Windows)

Se sua implantação do Azure estiver conectada a uma instância de DNS ou Active Directory local por meio de uma conexão VPN site a site do Azure ou ExpressRoute (isso é chamado de *entre instalações* em [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide]), espera-se que a VM ingresse em um domínio local. Para obter mais informações sobre as considerações para essa tarefa, consulte [unir uma VM a um domínio local (somente Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Definir configurações de proxy

Dependendo de como sua rede local está configurada, talvez seja necessário configurar o proxy em sua VM. Se sua VM estiver conectada à sua rede local via VPN ou ExpressRoute, a VM poderá não ser capaz de acessar a Internet e não poderá baixar as extensões necessárias ou coletar dados de monitoramento. Para obter mais informações, consulte [Configurar o proxy][deployment-guide-configure-proxy].

#### <a name="configure-monitoring"></a>Configurar a monitorização

Para ter certeza de que o SAP dá suporte ao seu ambiente, configure a extensão de monitoramento do Azure para SAP, conforme descrito em [Configurar a extensão de monitoramento avançado do Azure para SAP][deployment-guide-4.5]. Verifique os pré-requisitos para monitoramento do SAP e as versões mínimas necessárias do kernel do SAP e do agente de host SAP, nos recursos listados em [recursos do SAP][deployment-guide-2.2].

#### <a name="monitoring-check"></a>Verificação de monitoramento

Verifique se o monitoramento está funcionando, conforme descrito em [verificações e solução de problemas para configurar o monitoramento de ponta a ponta][deployment-guide-troubleshooting-chapter].

## <a name="update-the-monitoring-configuration-for-sap"></a>Atualizar a configuração de monitoramento para SAP

Atualize a configuração de monitoramento do SAP em qualquer um dos seguintes cenários:
* A equipe conjunta Microsoft/SAP estende os recursos de monitoramento e solicita mais ou menos contadores.
* A Microsoft apresenta uma nova versão da infraestrutura subjacente do Azure que fornece os dados de monitoramento, e a extensão de monitoramento avançado do Azure para SAP precisa ser adaptada para essas alterações.
* Você monta discos de dados adicionais para sua VM do Azure ou remove um disco de dados. Nesse cenário, atualize a coleção de dados relacionados ao armazenamento. Alterar sua configuração adicionando ou excluindo pontos de extremidade ou atribuindo endereços IP a uma VM não afeta a configuração de monitoramento.
* Você altera o tamanho da VM do Azure, por exemplo, de tamanho A5 para qualquer outro tamanho de VM.
* Você adiciona novas interfaces de rede à sua VM do Azure.

Para atualizar as configurações de monitoramento, atualize a infraestrutura de monitoramento seguindo as etapas em [Configurar a extensão de monitoramento avançado do Azure para SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Tarefas detalhadas para a implantação de software SAP

Esta seção apresenta etapas detalhadas para realizar tarefas específicas no processo de configuração e implantação.

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implantar cmdlets Azure PowerShell

1. Vá para [Microsoft Azure downloads](https://azure.microsoft.com/downloads/).
1. Em **ferramentas de linha de comando**, em **PowerShell**, selecione **instalação do Windows**.
1. Na caixa de diálogo Gerenciador de download da Microsoft, para o arquivo baixado (por exemplo, WindowsAzurePowershellGet. 3F. 3F. 3fnew. exe), selecione **executar**.
1. Para executar Microsoft Web Platform Installer (Microsoft Web PI), selecione **Sim**.
1. Uma página parecida com esta é exibida:

   ![Página de instalação para Azure PowerShell cmdlets][deployment-guide-figure-500]<a name="figure-5"></a>

1. Selecione **instalar**e aceite os termos de licença para software Microsoft.
1. O PowerShell está instalado. Selecione **concluir** para fechar o assistente de instalação.

Verifique frequentemente se há atualizações para os cmdlets do PowerShell, que geralmente são atualizados mensalmente. A maneira mais fácil de verificar se há atualizações é fazer as etapas de instalação anteriores, até a página de instalação mostrada na etapa 5. A data de lançamento e o número de versão dos cmdlets estão incluídos na página mostrada na etapa 5. A menos que declarado de outra forma no SAP Note [1928533] ou sap Note [2015553], recomendamos que você trabalhe com a versão mais recente dos cmdlets do Azure PowerShell.

Para verificar a versão dos cmdlets Azure PowerShell que estão instalados no computador, execute este comando do PowerShell:
```powershell
(Get-Module Az.Compute).Version
```
O resultado é semelhante a este:

![Resultado da verificação de versão do cmdlet Azure PowerShell][deployment-guide-figure-600]
<a name="figure-6"></a>

Se a versão do cmdlet do Azure instalada no seu computador for a versão atual, a primeira página do assistente de instalação o indicará adicionando **(instalado)** ao título do produto (consulte a captura de tela a seguir). Seus cmdlets do Azure PowerShell estão atualizados. Para fechar o assistente de instalação, selecione **sair**.

![Página de instalação para Azure PowerShell cmdlets indicando que a versão mais recente dos cmdlets Azure PowerShell está instalada][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implantar CLI do Azure

1. Vá para [Microsoft Azure downloads](https://azure.microsoft.com/downloads/).
1. Em **ferramentas de linha de comando**, em **interface de linha de comando do Azure**, selecione o link **instalar** para seu sistema operacional.
1. Na caixa de diálogo Gerenciador de download da Microsoft, para o arquivo baixado (por exemplo, WindowsAzureXPlatCLI. 3F. 3F. 3fnew. exe), selecione **executar**.
1. Para executar Microsoft Web Platform Installer (Microsoft Web PI), selecione **Sim**.
1. Uma página parecida com esta é exibida:

   ![Página de instalação para Azure PowerShell cmdlets][deployment-guide-figure-500]<a name="figure-5"></a>

1. Selecione **instalar**e aceite os termos de licença para software Microsoft.
1. O CLI do Azure está instalado. Selecione **concluir** para fechar o assistente de instalação.

Verifique frequentemente se há atualizações para CLI do Azure, que geralmente são atualizadas mensalmente. A maneira mais fácil de verificar se há atualizações é fazer as etapas de instalação anteriores, até a página de instalação mostrada na etapa 5.

Para verificar a versão do CLI do Azure que está instalado em seu computador, execute este comando:
```
azure --version
```

O resultado é semelhante a este:

![Resultado da verificação de versão de CLI do Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Ingressar uma VM em um domínio local (somente Windows)

Se você implantar VMs SAP em um cenário entre instalações, onde Active Directory locais e o DNS são estendidos no Azure, espera-se que as VMs ingressem em um domínio local. As etapas detalhadas que você leva para unir uma VM a um domínio local, e o software adicional necessário para ser membro de um domínio local, varia de acordo com o cliente. Normalmente, para ingressar uma VM em um domínio local, você precisa instalar software adicional, como software antimalware e software de backup ou monitoramento.

Nesse cenário, você também precisa garantir que, se as configurações de proxy da Internet forem forçadas quando uma VM ingressar em um domínio em seu ambiente, a conta do sistema local do Windows (S-1-5-18) na VM convidada terá as mesmas configurações de proxy. A opção mais fácil é forçar o proxy usando um Política de Grupo de domínio, que se aplica aos sistemas no domínio.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Baixar, instalar e habilitar o agente de VM do Azure

Para máquinas virtuais implantadas de uma imagem do sistema operacional que não é generalizada (por exemplo, uma imagem que não se origina na preparação do sistema do Windows ou Sysprep, ferramenta), você precisa baixar, instalar e habilitar manualmente o agente de VM do Azure.

Se você implantar uma VM do Azure Marketplace, essa etapa não será necessária. As imagens do Azure Marketplace já têm o agente de VM do Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Baixe o agente de VM do Azure:
   1.  Baixe o [pacote do instalador do agente de VM do Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Armazene o pacote do MSI do agente de VM localmente em um computador ou servidor pessoal.
1. Instale o agente de VM do Azure:
   1.  Conecte-se à VM do Azure implantada usando protocolo RDP (RDP).
   1.  Abra uma janela do Windows Explorer na VM e selecione o diretório de destino para o arquivo MSI do agente de VM.
   1.  Arraste o arquivo MSI do instalador do agente de VM do Azure do computador/servidor local para o diretório de destino do agente de VM na VM.
   1.  Clique duas vezes no arquivo MSI na VM.
1. Para VMs que ingressaram em domínios locais, certifique-se de que as configurações eventual de proxy da Internet também se aplicam à conta do sistema local do Windows (S-1-5-18) na VM, conforme descrito em [Configurar o proxy][deployment-guide-configure-proxy]. O agente de VM é executado nesse contexto e precisa ser capaz de se conectar ao Azure.

Nenhuma interação do usuário é necessária para atualizar o agente de VM do Azure. O agente de VM é atualizado automaticamente e não requer uma reinicialização de VM.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Use os seguintes comandos para instalar o agente de VM para Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) ou Oracle Linux**

  ```
  sudo yum install WALinuxAgent
  ```

Se o agente já estiver instalado, para atualizar o agente Linux do Azure, execute as etapas descritas em [atualizar o agente Linux do Azure em uma VM para a versão mais recente do GitHub][virtual-machines-linux-update-agent].

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configurar o proxy

As etapas executadas para configurar o proxy no Windows são diferentes da maneira como você configura o proxy no Linux.

#### <a name="windows"></a>Windows

As configurações de proxy devem ser configuradas corretamente para que a conta sistema local Acesse a Internet. Se as configurações de proxy não forem definidas por Política de Grupo, você poderá definir as configurações para a conta sistema local.

1. Vá para **Iniciar**, insira **gpedit. msc**e, em seguida, selecione **Enter**.
1. Selecione **configuração** > do computador**modelos administrativos** > componentesdo > Windows**Internet Explorer**. Certifique-se de que a configuração **fazer com que as configurações de proxy por computador (em vez de por usuário)** esteja desabilitada ou não esteja configurada.
1. No **painel de controle**, acesse central > de **rede e compartilhamento** **Opções da Internet**.
1. Na guia **conexões** , selecione o botão **configurações de LAN** .
1. Desmarque a caixa de seleção **detectar automaticamente as configurações** .
1. Marque a caixa de seleção **usar um servidor proxy para sua LAN** e, em seguida, insira o endereço e a porta do proxy.
1. Selecione o botão **avançado** .
1. Na caixa **exceções** , insira o endereço IP **168.63.129.16**. Selecione **OK**.

#### <a name="linux"></a>Linux

Configure o proxy correto no arquivo de configuração do agente convidado Microsoft Azure, que está localizado em \\etc\\waagent. conf.

Defina os seguintes parâmetros:

1. **Host de proxy http**. Por exemplo, defina-o como **proxy. Corp. local**.
   ```
   HttpProxy.Host=<proxy host>

   ```
1. **Porta proxy http**. Por exemplo, defina-o como **80**.
   ```
   HttpProxy.Port=<port of the proxy host>

   ```
1. Reinicie o agente.

   ```
   sudo service waagent restart
   ```

As configurações de proxy \\no\\, etc, waagent. conf, também se aplicam às extensões de VM necessárias. Se você quiser usar os repositórios do Azure, certifique-se de que o tráfego para esses repositórios não passe pela intranet local. Se você criou rotas definidas pelo usuário para habilitar o túnel forçado, certifique-se de adicionar uma rota que roteia o tráfego para os repositórios diretamente na Internet, e não através de sua conexão VPN site a site.

* **SLES**

  Você também precisa adicionar rotas para os endereços IP listados em \\etc\\regionserverclnt. cfg. A figura a seguir mostra um exemplo:

  ![Túnel forçado][deployment-guide-figure-50]


* **RHEL**

  Você também precisa adicionar rotas para os endereços IP dos hosts listados em \\etc\\yum. repositórios. d\\rhui-Load-balancers. Para obter um exemplo, consulte a figura anterior.

* **Oracle Linux**

  Não há repositórios para Oracle Linux no Azure. Você precisa configurar seus próprios repositórios para Oracle Linux ou usar os repositórios públicos.

Para obter mais informações sobre rotas definidas pelo usuário, consulte [rotas definidas pelo usuário e encaminhamento de IP][virtual-networks-udr-overview].

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configurar a extensão de monitoramento avançado do Azure para SAP

Quando você preparou a VM conforme descrito em [cenários de implantação de VMs para SAP no Azure][deployment-guide-3], o agente de VM do Azure é instalado na máquina virtual. A próxima etapa é implantar a extensão de monitoramento avançado do Azure para SAP, que está disponível no repositório de extensões do Azure nos data centers globais do Azure. Para obter mais informações, consulte [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide-9.1].

Você pode usar o PowerShell ou o CLI do Azure para instalar e configurar a extensão de monitoramento avançado do Azure para SAP. Para instalar a extensão em uma VM Windows ou Linux usando um computador Windows, consulte [Azure PowerShell][deployment-guide-4.5.1]. Para instalar a extensão em uma VM do Linux usando uma área de trabalho do Linux, consulte [CLI do Azure][deployment-guide-4.5.2].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell para VMs Linux e Windows

Para instalar a extensão de monitoramento avançado do Azure para SAP usando o PowerShell:

1. Verifique se você instalou a versão mais recente do cmdlet Azure PowerShell. Para obter mais informações, consulte Implantando [cmdlets Azure PowerShell][deployment-guide-4.1].  
1. Execute o seguinte cmdlet do PowerShell.
    Para obter uma lista de ambientes disponíveis, `commandlet Get-AzEnvironment`execute. Se você quiser usar o Azure global, seu ambiente será **AzureCloud**. Para o Azure na China, selecione **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Depois de inserir os dados da conta e identificar a máquina virtual do Azure, o script implantará as extensões necessárias e habilitará os recursos necessários. Isso pode levar vários minutos.
Para obter mais informações `Set-AzVMAEMExtension`sobre o, consulte [set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Execução bem-sucedida de cmdlets do Azure específicos do SAP Set-AzVMAEMExtension][deployment-guide-figure-900]

A `Set-AzVMAEMExtension` configuração faz todas as etapas para configurar o monitoramento de host para SAP.

A saída do script inclui as seguintes informações:

* Confirmação de que o monitoramento do disco do sistema operacional e de todos os discos de dados adicionais foi configurado.
* As duas próximas mensagens confirmam a configuração de métricas de armazenamento para uma conta de armazenamento específica.
* Uma linha de saída fornece o status da atualização real da configuração de monitoramento.
* Outra linha de saída confirma que a configuração foi implantada ou atualizada.
* A última linha de saída é informativa. Ele mostra as opções para testar a configuração de monitoramento.
* Para verificar se todas as etapas do monitoramento avançado do Azure foram executadas com êxito e se a infraestrutura do Azure fornece os dados necessários, continue com a verificação de preparação para a extensão de monitoramento avançado do Azure para SAP, conforme descrito em [ Verificação de preparação para o monitoramento avançado do Azure para SAP][deployment-guide-5.1].
* Aguarde 15-30 minutos para Diagnóstico do Azure coletar os dados relevantes.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>CLI do Azure para VMs Linux

Para instalar a extensão de monitoramento avançado do Azure para SAP usando CLI do Azure:

   1. Instale a CLI clássica do Azure, conforme descrito em [instalar a CLI clássica do Azure][azure-cli].
   1. Entre com sua conta do Azure:

      ```
      azure login
      ```

   1. Alternar para o modo de Azure Resource Manager:

      ```
      azure config mode arm
      ```

   1. Habilitar o monitoramento avançado do Azure:

      ```
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Instalar usando o CLI do Azure 2,0

   1. Instale o CLI do Azure 2,0, conforme descrito em [instalar CLI do Azure 2,0][azure-cli-2].
   1. Entre com sua conta do Azure:

      ```
      az login
      ```

   1. Instalar a extensão CLI do Azure AEM
  
      ```
      az extension add --name aem
      ```
  
   1. Instalar a extensão com
  
      ```
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Verifique se a extensão de monitoramento avançado do Azure está ativa na VM Linux do Azure. Verifique se o arquivo \\var\\lib\\AzureEnhancedMonitor\\PerfCounters existe. Se ele existir, em um prompt de comando, execute este comando para exibir as informações coletadas pelo monitor avançado do Azure:

   ```
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   A saída tem a seguinte aparência:
   ```
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Verificações e solução de problemas de monitoramento de ponta a ponta

Depois de implantar sua VM do Azure e configurar a infraestrutura de monitoramento do Azure relevante, verifique se todos os componentes da extensão de monitoramento avançado do Azure estão funcionando conforme o esperado.

Execute a verificação de preparação para a extensão de monitoramento avançado do Azure para SAP, conforme descrito em [verificação de preparação para a extensão de monitoramento avançado do Azure para SAP][deployment-guide-5.1]. Se todos os resultados da verificação de preparação forem positivos e todos os contadores de desempenho relevantes aparecerem OK, o monitoramento do Azure foi configurado com êxito. Você pode prosseguir com a instalação do agente de host do SAP, conforme descrito nas notas SAP em [recursos SAP][deployment-guide-2.2]. Se a verificação de preparação indicar que os contadores estão ausentes, execute a verificação de integridade para a infraestrutura de monitoramento do Azure, conforme descrito em [verificação de integridade para configuração de infraestrutura de monitoramento do Azure][deployment-guide-5.2]. Para obter mais opções de solução de problemas, consulte [Solucionando problemas do monitoramento do Azure para SAP][deployment-guide-5.3].

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Verificação de preparação para a extensão de monitoramento avançado do Azure para SAP

Essa verificação garante que todas as métricas de desempenho que aparecem dentro de seu aplicativo SAP sejam fornecidas pela infraestrutura de monitoramento do Azure subjacente.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Executar a verificação de preparação em uma VM do Windows

1. Entre na máquina virtual do Azure (não é necessário usar uma conta de administrador).
1. Abra uma janela de Linha de Comandos.
1. No prompt de comando, altere o diretório para a pasta de instalação da extensão de monitoramento avançado do Azure para SAP: C:\\Packages\\plugins\\Microsoft. AzureCAT. AzureEnhancedMonitoring.\\AzureCATExtensionHandler&lt;versão\\> Drop

   A *versão* no caminho para a extensão de monitoramento pode variar. Se você vir pastas para várias versões da extensão de monitoramento na pasta de instalação, verifique a configuração do serviço Windows AzureEnhancedMonitoring e, em seguida, alterne para a pasta indicada como *caminho para o executável*.

   ![Propriedades do serviço que executa a extensão de monitoramento avançado do Azure para SAP][deployment-guide-figure-1000]

1. No prompt de comando, execute **azperflib. exe** sem parâmetros.

   > [!NOTE]
   > O Azperflib. exe é executado em um loop e atualiza os contadores coletados a cada 60 segundos. Para encerrar o loop, feche a janela do prompt de comando.
   >
   >

Se a extensão de monitoramento avançado do Azure não estiver instalada ou o serviço AzureEnhancedMonitoring não estiver em execução, a extensão não foi configurada corretamente. Para obter informações detalhadas sobre como implantar a extensão, consulte [Solucionando problemas da infraestrutura de monitoramento do Azure para SAP][deployment-guide-5.3].

> [!NOTE]
> O Azperflib. exe é um componente que não pode ser usado para fins próprios. É um componente que fornece dados de monitoramento do Azure relacionados à VM para o agente de host do SAP.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Verificar a saída de azperflib. exe

A saída de Azperflib. exe mostra todos os contadores de desempenho do Azure preenchidos para SAP. Na parte inferior da lista de contadores coletados, um indicador de resumo e integridade mostra o status do monitoramento do Azure.

![Saída da verificação de integridade executando azperflib. exe, que indica que não existem problemas][deployment-guide-figure-1100]
<a name="figure-11"></a>

Verifique o resultado retornado para a saída **total de contadores** , que é relatada como vazia, e para o status de **integridade**, mostrado na figura anterior.

Interprete os valores resultantes da seguinte maneira:

| Valores de resultado de Azperflib. exe | Status de integridade do monitoramento do Azure |
| --- | --- |
| **Chamadas à API-não disponível** | Os contadores que não estão disponíveis podem não ser aplicáveis à configuração da máquina virtual ou são erros. Consulte **status de integridade**. |
| **Total de contadores-vazio** |Os dois contadores de armazenamento do Azure a seguir podem estar vazios: <ul><li>Servidor de latência operacional de leitura de armazenamento MS</li><li>Latência de operação de leitura de armazenamento E2E MS</li></ul>Todos os outros contadores devem ter valores. |
| **Status de integridade** |OK somente se o status de retorno mostrar **OK**. |
| **Diagnóstico** |Informações detalhadas sobre o status de integridade. |

Se o valor do **status de integridade** não estiver **OK**, siga as instruções em [verificação de integridade para a configuração da infraestrutura de monitoramento do Azure][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Executar a verificação de preparação em uma VM do Linux

1. Conecte-se à máquina virtual do Azure usando SSH.

1. Verifique a saída da extensão de monitoramento avançado do Azure.

   a.  Execute `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Resultado esperado**: Retorna a lista de contadores de desempenho. O arquivo não deve ficar vazio.

   b. Execute `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Resultado esperado**: Retorna uma linha em que o erro é **None**, por exemplo, **3; config; Erro;; 0; 0; nenhum; 0; 1456416792; TST-servercs;**

   c. Execute `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Resultado esperado**: Retorna como vazio ou não existe.

Se a verificação anterior não foi bem-sucedida, execute estas verificações adicionais:

1. Verifique se o waagent está instalado e habilitado.

   a.  Execute `sudo ls -al /var/lib/waagent/`

     **Resultado esperado**: Lista o conteúdo do diretório waagent.

   b.  Execute `ps -ax | grep waagent`

   **Resultado esperado**: Exibe uma entrada semelhante a:`python /usr/sbin/waagent -daemon`

1. Verifique se a extensão de monitoramento avançado do Azure está instalada e em execução.

   a.  Execute `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Resultado esperado**: Lista o conteúdo do diretório de extensão de monitoramento avançado do Azure.

   b. Execute `ps -ax | grep AzureEnhanced`

   **Resultado esperado**: Exibe uma entrada semelhante a:`python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Instale o agente de host do SAP conforme descrito em SAP Note [1031096]e verifique a `saposcol`saída de.

   a.  Execute `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Execute `dump ccm`

   c.  Verifique se a métrica de **acesso de monitoramento Virtualization_Configuration\Enhanced** é **verdadeira**.

Se você já tiver um servidor de aplicativos ABAP do SAP NetWeaver instalado, abra a transação ST06 e verifique se o monitoramento avançado está habilitado.

Se qualquer uma dessas verificações falhar e para obter informações detalhadas sobre como reimplantar a extensão, consulte [Solucionando problemas da infraestrutura de monitoramento do Azure para SAP][deployment-guide-5.3].

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Verificação de integridade para a configuração da infraestrutura de monitoramento do Azure

Se alguns dos dados de monitoramento não forem entregues corretamente conforme indicado pelo teste descrito em [verificação de preparação para o monitoramento avançado do Azure para SAP][deployment-guide-5.1], execute `Test-AzVMAEMExtension` o cmdlet para verificar se a infraestrutura de monitoramento do Azure e o monitoramento a extensão para SAP está configurada corretamente.

1. Verifique se você instalou a versão mais recente do cmdlet Azure PowerShell, conforme descrito em Implantando [cmdlets Azure PowerShell][deployment-guide-4.1].
1. Execute o seguinte cmdlet do PowerShell. Para obter uma lista de ambientes disponíveis, execute o `Get-AzEnvironment`cmdlet. Para usar o Azure global, selecione o ambiente **AzureCloud** . Para o Azure na China, selecione **AzureChinaCloud**.
   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Insira os dados da conta e identifique a máquina virtual do Azure.

   ![Página de entrada do cmdlet do Azure específico para SAP Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

1. O script testa a configuração da máquina virtual selecionada.

   ![Saída do teste bem-sucedido da infraestrutura de monitoramento do Azure para SAP][deployment-guide-figure-1300]

Verifique se todos os resultados da verificação de integridade estão **OK**. Se algumas verificações não exibirem **OK**, execute o cmdlet Update conforme descrito em [Configurar a extensão de monitoramento avançado do Azure para SAP][deployment-guide-4.5]. Aguarde 15 minutos e repita as verificações descritas em [verificação de preparação para monitoramento avançado do Azure para SAP][deployment-guide-5.1] e [verificação de integridade para a configuração de infraestrutura de monitoramento do Azure][deployment-guide-5.2]. Se as verificações ainda indicarem um problema com alguns ou todos os contadores, consulte [Solucionando problemas da infraestrutura de monitoramento do Azure para SAP][deployment-guide-5.3].

> [!Note]
> Você pode experimentar alguns avisos nos casos em que usa discos do Azure Standard gerenciados. Os avisos serão exibidos em vez dos testes que retornam "OK". Isso é normal e pretendido no caso desse tipo de disco. Confira também consulte [Solucionando problemas da infraestrutura de monitoramento do Azure para SAP][deployment-guide-5.3]
> 

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Solução de problemas da infraestrutura de monitoramento do Azure para SAP

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Os contadores de desempenho do Azure não são mostrados

O serviço do Windows AzureEnhancedMonitoring coleta métricas de desempenho no Azure. Se o serviço não tiver sido instalado corretamente ou se não estiver em execução em sua VM, nenhuma métrica de desempenho poderá ser coletada.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>O diretório de instalação da extensão de monitoramento avançado do Azure está vazio

###### <a name="issue"></a>Problema

O diretório de instalação C\\:\\Packages plugins\\Microsoft. AzureCAT. AzureEnhancedMonitoring\\. AzureCATExtensionHandler\\&lt;versão > Drop está vazio.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se esse é um problema de proxy (conforme descrito anteriormente). Talvez seja necessário reiniciar o computador ou executar novamente o `Set-AzVMAEMExtension` script de configuração.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>O serviço de monitoramento avançado do Azure não existe

###### <a name="issue"></a>Problema

O serviço do Windows AzureEnhancedMonitoring não existe.

A saída de Azperflib. exe gera um erro:

![A execução de azperflib. exe indica que o serviço da extensão de monitoramento avançado do Azure para SAP não está em execução][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Solução

Se o serviço não existir, a extensão de monitoramento avançado do Azure para SAP não foi instalada corretamente. Reimplante a extensão usando as etapas descritas para seu cenário de implantação em [cenários de implantação de VMs para SAP no Azure][deployment-guide-3].

Depois de implantar a extensão, após uma hora, verifique novamente se os contadores de desempenho do Azure são fornecidos na VM do Azure.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>O serviço para monitoramento avançado do Azure existe, mas não é iniciado

###### <a name="issue"></a>Problema

O serviço do Windows AzureEnhancedMonitoring existe e está habilitado, mas não é iniciado. Para obter mais informações, verifique o log de eventos do aplicativo.

###### <a name="solution"></a>Solução

A configuração está incorreta. Reinicie a extensão de monitoramento para a VM, conforme descrito em [Configurar a extensão de monitoramento avançado do Azure para SAP][deployment-guide-4.5].

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Alguns contadores de desempenho do Azure estão ausentes

O serviço do Windows AzureEnhancedMonitoring coleta métricas de desempenho no Azure. O serviço obtém dados de várias fontes. Alguns dados de configuração são coletados localmente e algumas métricas de desempenho são lidas de Diagnóstico do Azure. Os contadores de armazenamento são usados de seu registro em log no nível de assinatura de armazenamento.

Se a solução de problemas usando o SAP Note [1999351] não resolver o problema, `Set-AzVMAEMExtension` execute novamente o script de configuração. Talvez seja necessário aguardar uma hora porque os contadores de diagnóstico ou análise de armazenamento podem não ser criados imediatamente depois de serem habilitados. Se o problema persistir, abra uma mensagem de suporte ao cliente do SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Os contadores de desempenho do Azure não são mostrados

As métricas de desempenho no Azure são coletadas por um daemon. Se o daemon não estiver em execução, nenhuma métrica de desempenho poderá ser coletada.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>O diretório de instalação da extensão de monitoramento avançado do Azure está vazio

###### <a name="issue"></a>Problema

O diretório \\var\\lib\\waagent\\ não tem um subdiretório para a extensão de monitoramento avançado do Azure.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se esse é um problema de proxy (conforme descrito anteriormente). Talvez seja necessário reiniciar o computador e/ou executar novamente o `Set-AzVMAEMExtension` script de configuração.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>A execução de Set-AzVMAEMExtension e Test-AzVMAEMExtension mostram mensagens de aviso informando que não há suporte para Managed Disks padrão

###### <a name="issue"></a>Problema

Ao executar as mensagens Set-AzVMAEMExtension ou Test-AzVMAEMExtension como estas são mostradas:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Executando o azperfli. exe conforme descrito anteriormente, você pode obter um resultado que está indicando um estado não íntegro. 

###### <a name="solution"></a>Solução

As mensagens são causadas pelo fato de que as Managed Disks padrão não estão fornecendo as APIs usadas pela extensão de monitoramento para verificar as estatísticas das contas de armazenamento do Azure padrão. Isso não é uma questão de preocupação. O motivo para a introdução do monitoramento para contas de Armazenamento em Disco padrão foi a limitação de e/s que ocorreu com frequência. O Managed disks evitará essa limitação limitando o número de discos em uma conta de armazenamento. Portanto, não ter esse tipo de dados de monitoramento não é crítico.


#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Alguns contadores de desempenho do Azure estão ausentes

As métricas de desempenho no Azure são coletadas por um daemon, que obtém dados de várias fontes. Alguns dados de configuração são coletados localmente e algumas métricas de desempenho são lidas de Diagnóstico do Azure. Os contadores de armazenamento são provenientes dos logs em sua assinatura de armazenamento.

Para obter uma lista completa e atualizada de problemas conhecidos, consulte a observação do SAP [1999351], que tem informações adicionais de solução de problemas para o monitoramento aprimorado do Azure para SAP.

Se a solução de problemas usando o SAP Note [1999351] não resolver o problema, execute `Set-AzVMAEMExtension` novamente o script de configuração conforme descrito em [Configurar a extensão de monitoramento avançado do Azure para SAP][deployment-guide-4.5]. Talvez seja necessário aguardar uma hora porque os contadores de diagnóstico ou análise de armazenamento podem não ser criados imediatamente depois de serem habilitados. Se o problema persistir, abra uma mensagem de suporte ao cliente do SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.
