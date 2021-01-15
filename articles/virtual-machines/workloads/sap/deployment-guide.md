---
title: Azure Virtual Machines implantação para SAP NetWeaver / Microsoft Docs
description: Saiba como implementar software SAP em máquinas virtuais Linux em Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/16/2020
ms.author: sedusch
ms.openlocfilehash: c70de186468eb3efacc82c1d5c8802612475fd4d
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232799"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Implementação de máquinas virtuais Azure para SAP NetWeaver

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
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Implantação DBMS de máquinas virtuais Azure para SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching para VMs e VHDs)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Armazenamento Microsoft Azure)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Estrutura de uma implantação RDBMS)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Alta disponibilidade e recuperação de desastres com VMs Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e mais tarde)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e lançamentos anteriores)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Utilizando uma imagem do SQL Server do Mercado Azure)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Servidor Geral SQL para SAP no resumo do Azure)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Especificidades para SQL Server RDBMS)
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

[deployment-guide]:deployment-guide.md (Implantação de máquinas virtuais Azure para SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Recursos SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Implantação de um VM utilizando uma imagem personalizada)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Cenário 1: Implantação de um VM do Mercado Azul para SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Cenário 2: Implantação de um VM com uma imagem personalizada para SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Cenário 3: Mover um VM do local utilizando um Azure VHD não generalizado com SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Cenários de implementação de VMs para SAP no Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Implantação de cmdlets Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Baixar e importar cmdlets PowerShell relevantes para a SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Junte-se a um VM para um domínio no local - Apenas Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Faça o download, instale e ative o Agente Azure VM)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configure a extensão azul para SAP)
[deployment-guide-configure-new-extension-ps]:deployment-guide.md#2ad55a0d-9937-4943-9dd2-69bc2b5d3de0 (Configure a nova extensão Azure para SAP com Azure PowerShell)
[deployment-guide-configure-new-extension-cli]:deployment-guide.md#c8749c24-fada-42ad-b114-f9aae2dc37da (Configure a nova extensão Azure para SAP com Azure CLI)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Verificação de prontidão para extensão Azure para SAP)
[deployment-guide-5.1-new]:deployment-guide.md#7bf24f59-7347-4c7a-b094-4693e4687ee5 (Verificação de prontidão para nova extensão do Azure para SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Verificação de saúde para a extensão Azure para configuração SAP)
[deployment-guide-5.2-new]:deployment-guide.md#464ac96d-7d3c-435d-a5ae-3faf3bfef4b3 (Verificação de saúde para a nova extensão do Azure para a configuração DOM)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Extensão de Azure de resolução de problemas para SAP)
[deployment-guide-5.3-new]:deployment-guide.md#b7afb8ef-a64c-495d-bb37-2af96688c530 (Resolução de problemas da nova extensão do Azure para o SAP)
[deployment-guide-contact-support]:deployment-guide.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (Resolução de problemas Extensão Azure para SAP - Suporte de contato)
[deployment-guide-run-the-script]:deployment-guide.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (Resolução de problemas Extensão Azure para SAP - Executar o script de configuração)
[deployment-guide-redeploy-after-sysprep]:deployment-guide.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (Resolução de problemas Extensão Azure para SAP - Reimplantação após sysprep)
[deployment-guide-fix-internet-connection]:deployment-guide.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 ( Resolução de problemas Extensão Azure para SAP - Corrigir ligação à internet)


[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configurar extensão VM)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure o representante)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Verificações e resolução de problemas)

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

[planning-guide]:planning-guide.md (Azure Virtual Machines planejamento e implementação para SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Recursos)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Alta disponibilidade e recuperação de desastres para SAP NetWeaver em execução em Azure Virtual Machines)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Alta disponibilidade para servidores de aplicações SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Utilização de Arranque Automático para casos SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Apenas em nuvem - Implementações de Máquinas Virtuais em Azure sem dependências na rede de clientes no local)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Instalações cruzadas - Implantação de VMs SAP individuais ou múltiplos em Azure totalmente integrados com a rede no local)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiões de Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domínios de avaria)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Atualizar domínios)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Conjuntos de disponibilidade de Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Conceito de máquinas virtuais Microsoft Azure)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Mover um VM das instalações para Azure com um disco não generalizado)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Implantação de um VM com uma imagem específica do cliente)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparação para a deslocação de um VM das instalações para Ozure com um disco não generalizado)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparação para implantação de um VM com uma imagem específica do cliente para SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparação de VMs com SAP para Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Diferença entre um disco Azure e uma imagem Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (O upload de um VHD de instalações para Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Cópia de discos entre contas de armazenamento Azure)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Estrutura VM/VHD para implantações DE SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Definição automática para discos anexos)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solução de monitorização Azure para SAP)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Networking Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Armazenamento: Microsoft Azure Storage e discos de dados)

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
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Implementar e gerir máquinas virtuais utilizando modelos de Gestor de Recursos Azure e o CLI Azure)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Gerir máquinas virtuais utilizando o Azure Resource Manager e o PowerShell)
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
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../windows/quick-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../windows/upload-image.md
[virtual-machines-windows-tutorial]:../../windows/quick-create-portal.md
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
[qs-configure-powershell-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md
[qs-configure-cli-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md
[howto-assign-access-powershell]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md
[howto-assign-access-cli]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

A Azure Virtual Machines é a solução para organizações que precisam de recursos de computação e armazenamento, em tempo mínimo, e sem ciclos de aquisição longos. Você pode usar Azure Virtual Machines para implementar aplicações clássicas, como aplicações baseadas em SAP NetWeaver, em Azure. Alargar a fiabilidade e disponibilidade de uma aplicação sem recursos adicionais no local. A Azure Virtual Machines suporta a conectividade transversal, para que possa integrar as Máquinas Virtuais Azure nos domínios da sua organização, nuvens privadas e paisagem do sistema SAP.

Neste artigo, cobrimos as etapas para implantar aplicações SAP em máquinas virtuais (VMs) em Azure, incluindo opções de implementação alternativas e resolução de problemas. Este artigo baseia-se na informação no planeamento e implementação de [Máquinas Virtuais Azure para o SAP NetWeaver][planning-guide]. Complementa também a documentação de instalação DA SAP e as Notas SAP, que são os principais recursos para a instalação e implantação de software SAP.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

A criação de uma máquina virtual Azure para a implementação de software SAP envolve vários passos e recursos. Antes de começar, certifique-se de que cumpre os pré-requisitos para a instalação do software SAP em máquinas virtuais em Azure.

### <a name="local-computer"></a>Computador local

Para gerir o Windows ou o Linux VMs, pode utilizar um script PowerShell e o portal Azure. Para ambas as ferramentas, precisa de um computador local com o Windows 7 ou uma versão posterior do Windows. Se quiser gerir apenas os VMs Linux e pretender utilizar um computador Linux para esta tarefa, pode utilizar o Azure CLI.

### <a name="internet-connection"></a>Conexão à Internet

Para descarregar e executar as ferramentas e scripts necessários para a implementação do software SAP, tem de estar ligado à Internet. O Azure VM que está a executar a Extensão Azure para SAP também precisa de acesso à Internet. Se o Azure VM fizer parte de uma rede virtual Azure ou de um domínio no local, certifique-se de que as definições de procuração relevantes estão definidas, conforme descrito no [Configure the proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Subscrição do Microsoft Azure

Precisa de uma conta ativa do Azure.

### <a name="topology-and-networking"></a>Topologia e networking

Você precisa definir a topologia e arquitetura da implantação sap em Azure:

* Contas de armazenamento Azure a utilizar
* Rede virtual onde pretende implantar o sistema SAP
* Grupo de recursos ao qual pretende implantar o sistema SAP
* Região de Azure onde pretende implantar o sistema SAP
* Configuração SAP (de dois níveis ou de três níveis)
* Tamanhos VM e o número de discos de dados adicionais a montar nos VMs
* Configuração do Sistema de Correção e Transporte SAP (CTS)

Crie e configuure as contas de armazenamento Azure (se necessário) ou redes virtuais Azure antes de iniciar o processo de implementação do software SAP. Para obter informações sobre como criar e configurar estes recursos, consulte [o planeamento e implementação de Máquinas Virtuais Azure para o SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Tamanho SAP

Conheça as seguintes informações, para o tamanho SAP:

* Carga de trabalho PROJETADA SAP, por exemplo, utilizando a ferramenta SAP Quick Sizer e o número SAP Application Performance Standard (SAPS)
* Recurso cpu necessário e consumo de memória do sistema SAP
* Operações de entrada/saída necessárias (E/S) por segundo
* Largura de banda de rede necessária de eventual comunicação entre VMs em Azure
* Largura de banda de rede necessária entre os ativos no local e o sistema SAP implantado em Azure

### <a name="resource-groups"></a>Grupos de recursos

No Azure Resource Manager, pode utilizar grupos de recursos para gerir todos os recursos da aplicação na sua subscrição Azure. Para obter mais informações, veja [Descrição geral do Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Recursos

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos SAP

Quando estiver a configurar a sua implementação de software SAP, precisa dos seguintes recursos SAP:

* Nota SAP [1928533], que tem:
  * Lista de tamanhos Azure VM que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade dos tamanhos Azure VM
  * Combinações suportadas de software SAP e sistema operativo (OS) e de base de dados
  * Versão kernel SAP necessária para Windows e Linux no Microsoft Azure

* O SAP Note [2015553] lista pré-requisitos para implementações de software SAP suportadas pelo SAP em Azure.
* A Nota [SAP 2178632] tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
* SAP Nota [1409604] tem a versão necessária do Agente anfitrião SAP para windows em Azure.
* Sap Note [2191498] tem a versão necessária do Agente anfitrião SAP para Linux em Azure.
* Sap Nota [2243692] tem informações sobre o licenciamento SAP em Linux em Azure.
* Sap Note [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* SAP Nota [2002167] tem informações gerais sobre Red Hat Enterprise Linux 7.x.
* SAP Nota [2069760] tem informações gerais sobre o Oracle Linux 7.x.
* Sap Note [1999351] tem informações adicionais de resolução de problemas para a extensão Azure para SAP.
* Sap Nota [1597355] tem informações gerais sobre a troca de espaço para Linux.
* [A SAP na página Azure SCN](https://wiki.scn.sap.com/wiki/x/Pia7Gg) tem novidades e uma coleção de recursos úteis.
* [A SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* Cmdlets PowerShell específicos da SAP que fazem parte da [Azure PowerShell][azure-ps].
* Comandos Azure CLI específicos da SAP que fazem parte do [Azure CLI][azure-cli].

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos windows

Estes artigos da Microsoft cobrem as implementações DOA em Azure:

* [Azure Virtual Machines planejamento e implementação para SAP NetWeaver][planning-guide]
* [Implementação de Máquinas Virtuais Azure para SAP NetWeaver (este artigo)][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Cenários de implantação para software SAP em VMs Azure

Tem várias opções para implantar VMs e discos associados em Azure. É importante entender as diferenças entre as opções de implementação, pois pode tomar diferentes passos para preparar os seus VMs para implementação com base no tipo de implementação que escolher.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Cenário 1: Implantação de um VM do Mercado Azul para SAP

Pode utilizar uma imagem fornecida pela Microsoft ou por terceiros no Azure Marketplace para implementar o seu VM. O Marketplace oferece algumas imagens padrão de SO do Windows Server e diferentes distribuições linux. Também pode implementar uma imagem que inclua SKUs de gestão de bases de dados (DBMS), por exemplo, Microsoft SQL Server. Para obter mais informações sobre a utilização de imagens com SKUs DBMS, consulte [a implementação DBMS das Máquinas Virtuais Azure para SAP NetWeaver][dbms-guide].

O seguinte fluxograma mostra a sequência específica do SAP de passos para a implantação de um VM do Mercado Azure:

![Fluxograma de implantação de VM para sistemas SAP utilizando uma imagem VM do Mercado Azure][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Criar uma máquina virtual utilizando o portal Azure

A forma mais fácil de criar uma nova máquina virtual com uma imagem do Azure Marketplace é utilizando o portal Azure.

1.  Aceda a <https://portal.azure.com/#create/hub>.  Ou, no menu do portal Azure, selecione **+ Novo**.
1.  **Selecione Compute** e, em seguida, selecione o tipo de sistema operativo que pretende implementar. Por exemplo, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) ou Oracle Linux 7.2. A vista de lista padrão não mostra todos os sistemas operativos suportados. Selecione **ver tudo** para obter uma lista completa. Para obter mais informações sobre sistemas operativos suportados para a implementação de software SAP, consulte a Nota SAP [1928533].
1.  Na página seguinte, rever os termos e condições.
1.  Na **caixa de modelo de implementação Selecione,** selecione **Resource Manager**.
1.  Selecione **Criar**.

O assistente orienta-o através da definição dos parâmetros necessários para criar a máquina virtual, além de todos os recursos necessários, como interfaces de rede e contas de armazenamento. Alguns destes parâmetros são:

1. **Básicos:**
   * **Nome**: O nome do recurso (nome da máquina virtual).
   * **Tipo de disco VM**: Selecione o tipo de disco do disco OS. Se pretender utilizar o Armazenamento Premium para os seus discos de dados, recomendamos também a utilização de Armazenamento Premium para o disco OS.
   * **Nome de utilizador e palavra-passe** ou **chave pública SSH**: Introduza o nome de utilizador e a palavra-passe do utilizador criado durante o provisionamento. Para uma máquina virtual Linux, pode introduzir a chave secure shell (SSH) pública que utiliza para iniciar serção na máquina.
   * **Subscrição**: Selecione a subscrição que pretende utilizar para a disponibilização da nova máquina virtual.
   * **Grupo de recursos**: O nome do grupo de recursos para o VM. Pode introduzir o nome de um novo grupo de recursos ou o nome de um grupo de recursos que já existe.
   * **Localização**: Onde implantar a nova máquina virtual. Se pretender ligar a máquina virtual à sua rede no local, certifique-se de que seleciona a localização da rede virtual que liga o Azure à sua rede no local. Para obter mais informações, consulte [a rede microsoft Azure][planning-guide-microsoft-azure-networking] em [planeamento e implementação de Máquinas Virtuais Azure para SAP NetWeaver][planning-guide].
1. **Tamanho:**

     Para obter uma lista de tipos de VM suportados, consulte a Nota SAP [1928533]. Certifique-se de que seleciona o tipo VM correto se quiser utilizar o Azure Premium Storage. Nem todos os tipos de VM suportam o Armazenamento Premium. Para obter mais informações, consulte [Armazenamento: Microsoft Azure Storage e discos de dados][planning-guide-storage-microsoft-azure-storage-and-data-disks] e [armazenamento Azure para cargas de trabalho SAP](./planning-guide-storage.md) em planeamento e implementação de [Máquinas Virtuais Azure para SAP NetWeaver][planning-guide].

1. **Definições:**
   * **Armazenamento**
     * **Tipo de disco**: Selecione o tipo de disco do disco OS. Se pretender utilizar o Armazenamento Premium para os seus discos de dados, recomendamos também a utilização de Armazenamento Premium para o disco OS.
     * **Utilize discos geridos**: Se pretender utilizar Discos Geridos, selecione Sim. Para obter mais informações sobre Discos Geridos, consulte o capítulo [Discos Geridos](./planning-guide-storage.md#microsoft-azure-storage-resiliency) no guia de planeamento.
     * **Conta de armazenamento**: Selecione uma conta de armazenamento existente ou crie uma nova. Nem todos os tipos de armazenamento funcionam para executar aplicações SAP. Para obter mais informações sobre tipos de armazenamento, consulte [a estrutura de armazenamento de um VM para implantações RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Rede**
     * **Rede virtual** e **Sub-rede**: Para integrar a máquina virtual com a sua intranet, selecione a rede virtual que está ligada à sua rede no local.
     * **Endereço IP público**: Selecione o endereço IP público que pretende utilizar ou introduza parâmetros para criar um novo endereço IP público. Pode utilizar um endereço IP público para aceder à sua máquina virtual através da Internet. Certifique-se de que também cria um grupo de segurança de rede para ajudar a garantir o acesso à sua máquina virtual.
     * **Grupo de segurança da rede**: Para obter mais informações, consulte o fluxo de tráfego da rede de controlo com [grupos de segurança da rede][virtual-networks-nsg].
   * **Extensões**: Pode instalar extensões de máquinas virtuais adicionando-as à implantação. Não precisa de adicionar extensões neste passo. As extensões necessárias para o suporte SAP são instaladas mais tarde. Consulte o capítulo [Configure a Extensão azulada para SAP][deployment-guide-4.5] neste guia.
   * **Alta Disponibilidade**: Selecione um conjunto de disponibilidade ou introduza os parâmetros para criar um novo conjunto de disponibilidade. Para mais informações, consulte [os conjuntos de disponibilidade do Azure.][planning-guide-3.2.3]
   * **Monitorização**
     * **Diagnóstico de arranque**: Pode selecionar **Desativar** para diagnósticos de arranque.
     * **Diagnósticos de SO do hóspede**: Pode selecionar **Desativar** para monitorizar diagnósticos.

1. **Resumo:**

   Reveja as suas seleções e, em seguida, selecione **OK**.

A sua máquina virtual está implantada no grupo de recursos que selecionou.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Crie uma máquina virtual usando um modelo

Pode criar uma máquina virtual utilizando um dos modelos SAP publicados no [repositório GitHub de azure-quickstart.][azure-quickstart-templates-github] Também pode criar manualmente uma máquina virtual utilizando o [portal Azure][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]ou [Azure CLI][virtual-machines-linux-tutorial].

* [**Modelo de configuração de dois níveis (apenas uma máquina virtual)** (imagem de mercado de 2 camadas)][sap-templates-2-tier-marketplace-image]

  Para criar um sistema de duas camadas utilizando apenas uma máquina virtual, utilize este modelo.
* [**Modelo de configuração de dois níveis (apenas uma máquina virtual) - Discos geridos** (sap-2-tier marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Para criar um sistema de duas camadas utilizando apenas uma máquina virtual e Discos Geridos, utilize este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais)** (imagem de mercado de 3 camadas)][sap-templates-3-tier-marketplace-image]

  Para criar um sistema de três camadas utilizando várias máquinas virtuais, utilize este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais) - Discos geridos** (sap-3-tier marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Para criar um sistema de três níveis utilizando várias máquinas virtuais e Discos Geridos, utilize este modelo.

No portal Azure, introduza os seguintes parâmetros para o modelo:

1. **Básicos:**
   * **Assinatura**: A subscrição a utilizar para implementar o modelo.
   * **Grupo de recursos**: O grupo de recursos a utilizar para implantar o modelo. Pode criar um novo grupo de recursos ou pode selecionar um grupo de recursos existente na subscrição.
   * **Localização**: Onde implantar o modelo. Se selecionar um grupo de recursos existente, a localização desse grupo de recursos é utilizada.

1. **Definições:**
   * **ID do sistema SAP**: O ID do sistema SAP (SID).
   * **Tipo de SISTEMA**: O sistema operativo que pretende implantar, por exemplo, o Windows Server 2012 R2, o SUSE Linux Enterprise Server 12 (SLES 12), o Red Hat Enterprise Linux 7.2 (RHEL 7.2) ou o Oracle Linux 7.2.

     A vista da lista não mostra todos os sistemas operativos suportados. Para obter mais informações sobre sistemas operativos suportados para a implementação de software SAP, consulte a Nota SAP [1928533].
   * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS que o novo sistema fornece. Se não tiver a certeza de quantos SAPS o sistema necessita, pergunte ao seu Parceiro tecnológico SAP ou Integrador de Sistema.
   * **Disponibilidade do sistema** (apenas modelo de três camadas): A disponibilidade do sistema.

     Selecione **HA** para uma configuração adequada para uma instalação de alta disponibilidade. São criados dois servidores de base de dados e dois servidores para os Serviços Centrais ABAP SAP (ASCS).
   * **Tipo de armazenamento** (apenas modelo de duas camadas): O tipo de armazenamento a utilizar.

     Para sistemas maiores, recomendamos vivamente a utilização do Azure Premium Storage. Para obter mais informações sobre tipos de armazenamento, consulte estes recursos:
      * [Utilização de Armazenamento SSD Azure Premium para ASD Instance][2367194]
      * [Estrutura de armazenamento de um VM para implantações RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho da Máquina Virtual Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Storage do Microsoft Azure][storage-introduction]
   * **Nome de utilizador admin** e **senha de administração**: Nome de utilizador e senha.
     É criado um novo utilizador para iniciar sessão na máquina virtual.
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e sub-rede são criadas ou se é utilizada uma sub-rede existente. Se já tem uma rede virtual que está ligada à sua rede no local, selecione **Existing**.
   * **ID da sub-rede**: Se pretender colocar o VM num VNet existente onde tenha uma sub-rede definida, o VM deve ser atribuído, nomeie o ID dessa sub-rede específica. O ID geralmente é assim: /subscrições/ &lt; id de subscrição>/resourceGroups/ &lt; nome do grupo de recursos>/fornecedores/Microsoft.Network/virtualNetworks/ nome de rede virtual>&lt; /sub-redes/ &lt; nome da sub-rede>

1. **Termos e condições:**  
    Reveja e aceite os termos legais.

1. Selecione **Comprar**.

O Agente VM Azure é implantado por padrão quando utiliza uma imagem do Mercado Azure.

#### <a name="configure-proxy-settings"></a>Configurar definições de proxy

Dependendo da configuração da sua rede no local, poderá ter de configurar o representante no seu VM. Se o seu VM estiver ligado à sua rede de acesso através de VPN ou ExpressRoute, o VM pode não conseguir aceder à Internet e não poderá descarregar as extensões VM necessárias ou recolher informações de infraestrutura Azure para o agente DOM Host através da extensão SAP para Azure. Para obter mais informações, consulte [Configure o representante.][deployment-guide-configure-proxy]

#### <a name="join-a-domain-windows-only"></a>Junte-se a um domínio (apenas Windows)

Se a sua implantação de Azure estiver ligada a um diretório ativo ou ao DNS no local através de uma ligação VPN local-a-local ou ExpressRoute (isto é chamado *de cross-premissas* no planeamento e implementação de [Máquinas Virtuais Azure para o SAP NetWeaver),][planning-guide]espera-se que o VM esteja a unir um domínio no local. Para obter mais informações sobre considerações para esta tarefa, consulte [Juntar um VM a um domínio no local (apenas Windows)][deployment-guide-4.3].

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configurar extensão VM

Para se certificar de que a SAP suporta o seu ambiente, crie a Extensão Azure para SAP, conforme descrito no [Configure the Azure Extension for SAP][deployment-guide-4.5]. Verifique os pré-requisitos para o SAP, e as versões mínimas exigidas do SAP Kernel e do Agente Anfitrião SAP, nos recursos listados nos [recursos SAP][deployment-guide-2.2].

#### <a name="vm-extension-for-sap-check"></a>Extensão VM para verificação SAP

Verifique se a extensão VM para SAP está a funcionar, conforme descrito em [Checks and Troubleshooting][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Passos pós-implementação

Depois de criar o VM e o VM ser implantado, tem de instalar os componentes de software necessários no VM. Devido à sequência de instalação de implementação/software neste tipo de implantação de VM, o software a instalar já deve estar disponível, seja em Azure, em outro VM, ou como um disco que possa ser ligado. Ou, considere usar um cenário transversal, no qual é dada conectividade aos ativos no local (partes de instalação).

Depois de implementar o seu VM em Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP no seu VM como faria num ambiente no local. Para instalar o software SAP num Azure VM, tanto o SAP como a Microsoft recomendam que carreque e armazene os meios de instalação SAP em VHDs ou Discos Geridos Azure, ou que crie um VM Azure que funcione como um servidor de ficheiros que tenha todos os meios de instalação SAP necessários.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Cenário 2: Implantação de um VM com uma imagem personalizada para SAP

Uma vez que diferentes versões de um sistema operativo ou DBMS têm diferentes requisitos de patch, as imagens que encontra no Azure Marketplace podem não satisfazer as suas necessidades. Em vez disso, pode querer criar um VM utilizando a sua própria imagem DES/DBMS VM, que pode ser novamente implantada mais tarde.
Usa diferentes passos para criar uma imagem privada para o Linux do que para criar uma para o Windows.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Para preparar uma imagem do Windows que pode utilizar para implantar várias máquinas virtuais, as definições do Windows (como o Windows SID e o nome de anfitrião) devem ser resumidas ou generalizadas nas VM no local. Podes usar [o Sysprep](/previous-versions/windows/it-pro/windows-8.1-and-8/hh825084(v=win.10)) para fazer isto.
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> Para preparar uma imagem Linux que pode utilizar para implantar várias máquinas virtuais, algumas definições de Linux devem ser abstraadas ou generalizadas nos VM no local. Podes usar `waagent -deprovision`  para fazer isto. Para obter mais informações, consulte [Capture a linux virtual machine que funciona no Azure][virtual-machines-linux-capture-image] e no guia de utilizador do [agente Azure Linux][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
Pode preparar e criar uma imagem personalizada e depois usá-la para criar vários novos VMs. Isto é descrito no [planeamento e implementação de Máquinas Virtuais Azure para SAP NetWeaver][planning-guide]. Crie o seu conteúdo de base de dados utilizando o SAP Software Provisioning Manager para instalar um novo sistema SAP (restaura uma cópia de segurança de base de dados a partir de um disco que está ligado à máquina virtual) ou restaurando diretamente uma cópia de segurança da base de dados a partir do armazenamento do Azure, se o seu DBMS o suportar. Para obter mais informações, consulte [a implementação DBMS das Máquinas Virtuais Azure para SAP NetWeaver][dbms-guide]. Se já instalou um sistema SAP nas suas VM (especialmente para sistemas de dois níveis), pode adaptar as definições do sistema SAP após a implantação do Azure VM utilizando o procedimento de Renome de Sistema suportado pelo SAP Software Provisioning Manager (NOTA SAP [1619720).] Caso contrário, pode instalar o software SAP depois de implementar o Azure VM.

O seguinte fluxograma mostra a sequência específica do SAP de passos para a implantação de um VM a partir de uma imagem personalizada:

![Fluxograma de implantação de VM para sistemas SAP utilizando uma imagem VM no Mercado privado][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Criar uma máquina virtual utilizando o portal Azure

A forma mais fácil de criar uma nova máquina virtual a partir de uma imagem de Disco Gerido é utilizando o portal Azure. Para obter mais informações sobre como criar uma Imagem de Disco gerido, leia [Capturar uma imagem gerida de um VM generalizado em Azure](../../windows/capture-image-resource.md)

1.  Aceda a <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Ou, no menu do portal Azure, selecione **Images**.
1.  Selecione a imagem do disco gerido que pretende implementar e clique em **Criar VM**

O assistente orienta-o através da definição dos parâmetros necessários para criar a máquina virtual, além de todos os recursos necessários, como interfaces de rede e contas de armazenamento. Alguns destes parâmetros são:

1. **Básicos:**
   * **Nome**: O nome do recurso (nome da máquina virtual).
   * **Tipo de disco VM**: Selecione o tipo de disco do disco OS. Se pretender utilizar o Armazenamento Premium para os seus discos de dados, recomendamos também a utilização de Armazenamento Premium para o disco OS.
   * **Nome de utilizador e palavra-passe** ou **chave pública SSH**: Introduza o nome de utilizador e a palavra-passe do utilizador criado durante o provisionamento. Para uma máquina virtual Linux, pode introduzir a chave secure shell (SSH) pública que utiliza para iniciar serção na máquina.
   * **Subscrição**: Selecione a subscrição que pretende utilizar para a disponibilização da nova máquina virtual.
   * **Grupo de recursos**: O nome do grupo de recursos para o VM. Pode introduzir o nome de um novo grupo de recursos ou o nome de um grupo de recursos que já existe.
   * **Localização**: Onde implantar a nova máquina virtual. Se pretender ligar a máquina virtual à sua rede no local, certifique-se de que seleciona a localização da rede virtual que liga o Azure à sua rede no local. Para obter mais informações, consulte [a rede microsoft Azure][planning-guide-microsoft-azure-networking] em [planeamento e implementação de Máquinas Virtuais Azure para SAP NetWeaver][planning-guide].
1. **Tamanho:**

     Para obter uma lista de tipos de VM suportados, consulte a Nota SAP [1928533]. Certifique-se de que seleciona o tipo VM correto se quiser utilizar o Azure Premium Storage. Nem todos os tipos de VM suportam o Armazenamento Premium. Para obter mais informações, consulte [Armazenamento: Microsoft Azure Storage e discos de dados][planning-guide-storage-microsoft-azure-storage-and-data-disks] e [armazenamento Azure para cargas de trabalho SAP](./planning-guide-storage.md) em planeamento e implementação de [Máquinas Virtuais Azure para SAP NetWeaver][planning-guide].

1. **Definições:**
   * **Armazenamento**
     * **Tipo de disco**: Selecione o tipo de disco do disco OS. Se pretender utilizar o Armazenamento Premium para os seus discos de dados, recomendamos também a utilização de Armazenamento Premium para o disco OS.
     * **Utilize discos geridos**: Se pretender utilizar Discos Geridos, selecione Sim. Para obter mais informações sobre Discos Geridos, consulte o capítulo [Discos Geridos](./planning-guide-storage.md#microsoft-azure-storage-resiliency) no guia de planeamento.
   * **Rede**
     * **Rede virtual** e **Sub-rede**: Para integrar a máquina virtual com a sua intranet, selecione a rede virtual que está ligada à sua rede no local.
     * **Endereço IP público**: Selecione o endereço IP público que pretende utilizar ou introduza parâmetros para criar um novo endereço IP público. Pode utilizar um endereço IP público para aceder à sua máquina virtual através da Internet. Certifique-se de que também cria um grupo de segurança de rede para ajudar a garantir o acesso à sua máquina virtual.
     * **Grupo de segurança da rede**: Para obter mais informações, consulte o fluxo de tráfego da rede de controlo com [grupos de segurança da rede][virtual-networks-nsg].
   * **Extensões**: Pode instalar extensões de máquinas virtuais adicionando-as à implantação. Não precisa de adicionar extensão neste passo. As extensões necessárias para o suporte SAP são instaladas mais tarde. Consulte o capítulo [Configure a Extensão azulada para SAP][deployment-guide-4.5] neste guia.
   * **Alta Disponibilidade**: Selecione um conjunto de disponibilidade ou introduza os parâmetros para criar um novo conjunto de disponibilidade. Para mais informações, consulte [os conjuntos de disponibilidade do Azure.][planning-guide-3.2.3]
   * **Monitorização**
     * **Diagnóstico de arranque**: Pode selecionar **Desativar** para diagnósticos de arranque.
     * **Diagnósticos de SO do hóspede**: Pode selecionar **Desativar** para monitorizar diagnósticos.

1. **Resumo:**

   Reveja as suas seleções e, em seguida, selecione **OK**.

A sua máquina virtual está implantada no grupo de recursos que selecionou.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Crie uma máquina virtual usando um modelo

Para criar uma implementação utilizando uma imagem privada de SO a partir do portal Azure, utilize um dos seguintes modelos SAP. Estes modelos são publicados no [repositório GitHub azure-quickstart.][azure-quickstart-templates-github] Também pode criar manualmente uma máquina virtual, utilizando [o PowerShell.][virtual-machines-upload-image-windows-resource-manager]

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (imagem de utilizador de 2 camadas)][sap-templates-2-tier-user-image]

  Para criar um sistema de duas camadas utilizando apenas uma máquina virtual, utilize este modelo.
* [**Modelo de configuração de dois níveis (apenas uma máquina virtual) - Imagem de disco gerido** (sap-2-tier user-image-md)][sap-templates-2-tier-user-image-md]

  Para criar um sistema de duas camadas utilizando apenas uma máquina virtual e uma imagem de disco gerido, utilize este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais)** (imagem de utilizador de 3 níveis)][sap-templates-3-tier-user-image]

  Para criar um sistema de três camadas utilizando várias máquinas virtuais ou a sua própria imagem de SO, utilize este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais) - Imagem de disco gerido** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Para criar um sistema de três camadas utilizando várias máquinas virtuais ou a sua própria imagem de SO e uma imagem de disco gerido, utilize este modelo.

No portal Azure, introduza os seguintes parâmetros para o modelo:

1. **Básicos:**
   * **Assinatura**: A subscrição a utilizar para implementar o modelo.
   * **Grupo de recursos**: O grupo de recursos a utilizar para implantar o modelo. Pode criar um novo grupo de recursos ou selecionar um grupo de recursos existente na subscrição.
   * **Localização**: Onde implantar o modelo. Se selecionar um grupo de recursos existente, a localização desse grupo de recursos é utilizada.
1. **Definições:**
   * **ID do sistema SAP**: O ID do sistema SAP.
   * **Tipo de SISTEMA**: O tipo de sistema operativo que pretende implantar (Windows ou Linux).
   * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS que o novo sistema fornece. Se não tiver a certeza de quantos SAPS o sistema necessita, pergunte ao seu Parceiro tecnológico SAP ou Integrador de Sistema.
   * **Disponibilidade do sistema** (apenas modelo de três camadas): A disponibilidade do sistema.

     Selecione **HA** para uma configuração adequada para uma instalação de alta disponibilidade. São criados dois servidores de base de dados e dois servidores para ASCS.
   * **Tipo de armazenamento** (apenas modelo de duas camadas): O tipo de armazenamento a utilizar.

     Para sistemas maiores, recomendamos vivamente a utilização do Azure Premium Storage. Para obter mais informações sobre os tipos de armazenamento, consulte os seguintes recursos:
      * [Utilização de Armazenamento SSD Azure Premium para ASD Instance][2367194]
      * [Estrutura de armazenamento de um VM para implantações RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho de máquina virtual Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Storage do Microsoft Azure][storage-introduction]
   * **Imagem do utilizador VHD URI** (apenas modelo de imagem de disco não gerido): O URI da imagem privada de SO VHD, por exemplo, https:// &lt; nome de conta>.blob.core.windows.net/vhds/userimage.vhd.
   * **Conta de armazenamento de imagem do utilizador** (apenas modelo de imagem de disco não gerido): O nome da conta de armazenamento onde a imagem privada do SO é armazenada, por exemplo, o nome de conta> em https:// nome &lt; &lt;>.blob.core.windows.net/vhds/userimage.vhds.
   * **userImageId** (apenas modelo de imagem de disco gerido): ID da imagem do disco gerido que pretende utilizar
   * **Nome de utilizador admin** e **senha de administração**: O nome de utilizador e a palavra-passe.

     É criado um novo utilizador para iniciar sessão na máquina virtual.
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e sub-rede é criada ou se é utilizada uma sub-rede existente. Se já tem uma rede virtual que está ligada à sua rede no local, selecione **Existing**.
   * **ID da sub-rede**: Se pretender colocar o VM num VNet existente onde tenha uma sub-rede definida, o VM deve ser atribuído, nomeie o ID dessa sub-rede específica. O ID geralmente é assim: /subscrições/ &lt; id de subscrição>/resourceGroups/ &lt; nome do grupo de recursos>/fornecedores/Microsoft.Network/virtualNetworks/ nome de rede virtual>&lt; /sub-redes/ &lt; nome da sub-rede>

1. **Termos e condições:**  
    Reveja e aceite os termos legais.

1. Selecione **Comprar**.

#### <a name="install-the-vm-agent-linux-only"></a>Instale o Agente VM (apenas Linux)

Para utilizar os modelos descritos na secção anterior, o Agente Linux já deve ser instalado na imagem do utilizador, ou a implementação falhará. Faça o download e instale o Agente VM na imagem do utilizador, conforme descrito no [Download, instale e ative o Agente VM Azure][deployment-guide-4.4]. Se não utilizar os modelos, também pode instalar o Agente VM mais tarde.

#### <a name="join-a-domain-windows-only"></a>Junte-se a um domínio (apenas Windows)

Se a sua implantação de Azure estiver ligada a um diretório ativo ou ao DNS no local através de uma ligação VPN site-to-site Azure ou Azure ExpressRoute (isto é chamado *de cross-premissas* no planeamento e implementação de [Máquinas Virtuais Azure para SAP NetWeaver),][planning-guide]espera-se que o VM esteja a unir um domínio no local. Para obter mais informações sobre considerações para este passo, consulte [Juntar um VM a um domínio no local (apenas Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configurar definições de proxy

Dependendo da configuração da sua rede no local, poderá ter de configurar o representante no seu VM. Se o seu VM estiver ligado à sua rede de acesso através de VPN ou ExpressRoute, o VM pode não conseguir aceder à Internet, e não poderá descarregar as extensões VM necessárias ou recolher informações de infraestrutura Azure para o agente do HOSPEDEIRO SAP através da extensão SAP para Azure, consulte [configurar o representante][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Configure Extensão VM Azure para SAP

Para se certificar de que a SAP suporta o seu ambiente, crie a Extensão Azure para SAP, conforme descrito no [Configure the Azure Extension for SAP][deployment-guide-4.5]. Verifique os pré-requisitos para o SAP, e as versões mínimas exigidas do SAP Kernel e do Agente Anfitrião SAP, nos recursos listados nos [recursos SAP][deployment-guide-2.2].

#### <a name="sap-vm-extension-check"></a>Verificação de extensão SAP VM

Verifique se a extensão VM para SAP está a funcionar, conforme descrito em [Checks and Troubleshooting][deployment-guide-troubleshooting-chapter].


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Cenário 3: Mover um VM no local utilizando um Azure VHD não generalizado com SAP

Neste cenário, planeia mover um sistema SAP específico de um ambiente no local para Azure. Pode fazê-lo carregando o VHD que tem o SISTEMA, os binários SAP e, eventualmente, os binários DBMS, além dos VHDs com os dados e ficheiros de registo do DBMS, para Azure. Ao contrário do cenário descrito no [Cenário 2: Implantação de um VM com uma imagem personalizada para SAP][deployment-guide-3.3], neste caso, mantém o nome de anfitrião, SAP SID e contas de utilizador SAP no VM Azure, porque foram configurados no ambiente no local. Não precisa generalizar o sistema operativo. Este cenário aplica-se mais frequentemente a cenários de cross-premis onde parte da paisagem do SAP corre no local e parte dele funciona em Azure.

Neste cenário, o Agente VM **não** é instalado automaticamente durante a implantação. Uma vez que o Agente VM e a Extensão Azure para SAP são obrigados a executar o SAP NetWeaver no Azure, é necessário descarregar, instalar e ativar ambos os componentes manualmente depois de criar a máquina virtual.

Para obter mais informações sobre o Agente Azure VM, consulte os seguintes recursos.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> [Visão geral do agente de máquinas virtuais Azure][virtual-machines-windows-agent-user-guide]
>
> ![Logotipo linux.][Logo_Linux] Linux
>
> [Guia de Utilizador de Agente Azure Linux][virtual-machines-linux-agent-user-guide]
>
>

---

O seguinte fluxograma mostra a sequência de passos para mover um VM no local utilizando um Azure VHD não generalizado:

![Fluxograma de implantação de VM para sistemas SAP utilizando um disco VM][deployment-guide-figure-400]

Se o disco já estiver carregado e definido em Azure (ver [Azure Virtual Machines planejando e implementação para SAP NetWeaver),][planning-guide]faça as tarefas descritas nas próximas secções.

#### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para criar uma implementação utilizando um disco de OS privado através do portal Azure, utilize o modelo SAP publicado no [repositório GitHub de azure-quickstart][azure-quickstart-templates-github]. Também pode criar manualmente uma máquina virtual, utilizando o PowerShell.

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (disco de utilizador de 2 camadas)][sap-templates-2-tier-os-disk]

  Para criar um sistema de duas camadas utilizando apenas uma máquina virtual, utilize este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual) - Disco gerido** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Para criar um sistema de duas camadas utilizando apenas uma máquina virtual e um Disco Gerido, utilize este modelo.

No portal Azure, introduza os seguintes parâmetros para o modelo:

1. **Básicos:**
   * **Assinatura**: A subscrição a utilizar para implementar o modelo.
   * **Grupo de recursos**: O grupo de recursos a utilizar para implantar o modelo. Pode criar um novo grupo de recursos ou selecionar um grupo de recursos existente na subscrição.
   * **Localização**: Onde implantar o modelo. Se selecionar um grupo de recursos existente, a localização desse grupo de recursos é utilizada.
1. **Definições:**
   * **ID do sistema SAP**: O ID do sistema SAP.
   * **Tipo de SISTEMA**: O tipo de sistema operativo que pretende implantar (Windows ou Linux).
   * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS que o novo sistema fornece. Se não tiver a certeza de quantos SAPS o sistema necessita, pergunte ao seu Parceiro tecnológico SAP ou Integrador de Sistema.
   * **Tipo de armazenamento** (apenas modelo de duas camadas): O tipo de armazenamento a utilizar.

     Para sistemas maiores, recomendamos vivamente a utilização do Azure Premium Storage. Para obter mais informações sobre os tipos de armazenamento, consulte os seguintes recursos:
      * [Utilização de Armazenamento SSD Azure Premium para ASD Instance][2367194]
      * [Estrutura de armazenamento de um VM para implantações RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho da Máquina Virtual Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Storage do Microsoft Azure][storage-introduction]
   * **Disco de OS VHD URI** (apenas modelo de disco não gerido): O URI do disco privado de OS, por exemplo, https:// &lt; nome de conta>.blob.core.windows.net/vhds/osdisk.vhd.
   * **Disco de OS Gerido ID** (apenas modelo de disco gerido): O ID do disco de disco gerido OS, /subscrições/92d102f7-81a5-4df7-9877-54987ba97d9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e sub-rede são criadas ou se é utilizada uma sub-rede existente. Se já tem uma rede virtual que está ligada à sua rede no local, selecione **Existing**.
   * **ID da sub-rede**: Se pretender colocar o VM num VNet existente onde tenha uma sub-rede definida, o VM deve ser atribuído, nomeie o ID dessa sub-rede específica. O ID geralmente é assim: /subscrições/ &lt; id de subscrição>/resourceGroups/ &lt; nome do grupo de recursos>/fornecedores/Microsoft.Network/virtualNetworks/ nome de rede virtual>&lt; /sub-redes/ &lt; nome da sub-rede>

1. **Termos e condições:**  
    Reveja e aceite os termos legais.

1. Selecione **Comprar**.

#### <a name="install-the-vm-agent"></a>Instalar o Agente da VM

Para utilizar os modelos descritos na secção anterior, o Agente VM tem de ser instalado no disco OS ou a implementação falhará. Faça o download e instale o Agente VM no VM, conforme descrito no [Download, instale e ative o Agente VM Azure][deployment-guide-4.4].

Se não utilizar os modelos descritos na secção anterior, também pode instalar o Agente VM posteriormente.

#### <a name="join-a-domain-windows-only"></a>Junte-se a um domínio (apenas Windows)

Se a sua implantação de Azure estiver ligada a um diretório ativo ou ao DNS no local através de uma ligação VPN local-a-local ou ExpressRoute (isto é chamado *de cross-premissas* no planeamento e implementação de [Máquinas Virtuais Azure para o SAP NetWeaver),][planning-guide]espera-se que o VM esteja a unir um domínio no local. Para obter mais informações sobre considerações para esta tarefa, consulte [Juntar um VM a um domínio no local (apenas Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configurar definições de proxy

Dependendo da configuração da sua rede no local, poderá ter de configurar o representante no seu VM. Se o seu VM estiver ligado à sua rede de acesso através de VPN ou ExpressRoute, o VM pode não conseguir aceder à Internet, e não poderá descarregar as extensões VM necessárias ou recolher informações de infraestrutura Azure para o agente do HOSPEDEIRO SAP através da extensão SAP para Azure, consulte [configurar o representante][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Configure Extensão VM Azure para SAP

Para se certificar de que a SAP suporta o seu ambiente, crie a Extensão Azure para SAP, conforme descrito no [Configure the Azure Extension for SAP][deployment-guide-4.5]. Verifique os pré-requisitos para o SAP, e as versões mínimas exigidas do SAP Kernel e do Agente Anfitrião SAP, nos recursos listados nos [recursos SAP][deployment-guide-2.2].

#### <a name="sap-vm-check"></a>Verificação SAP VM

Verifique se a extensão VM para SAP está a funcionar, conforme descrito em [Checks and Troubleshooting][deployment-guide-troubleshooting-chapter].

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Atualizar a configuração da extensão Azure para SAP

Atualizar a configuração da extensão Azure para SAP em qualquer um dos seguintes cenários:
* A equipa conjunta Microsoft/SAP alarga as capacidades da extensão VM e solicita mais ou menos contadores.
* A Microsoft introduz uma nova versão da infraestrutura Azure subjacente que fornece os dados, e a Extensão Azure para SAP precisa de ser adaptada a essas alterações.
* Monte discos de dados adicionais no seu Azure VM ou remova um disco de dados. Neste cenário, atualize a recolha de dados relacionados com o armazenamento. Alterar a sua configuração adicionando ou eliminando pontos finais ou atribuindo endereços IP a um VM não afeta a configuração da extensão.
* Muda o tamanho do seu Azure VM, por exemplo, do tamanho A5 para qualquer outro tamanho VM.
* Adiciona novas interfaces de rede ao seu Azure VM.

Para atualizar as definições, atualize a configuração da extensão Azure para SAP seguindo os passos em [Configurar a Extensão azul para SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Tarefas detalhadas para a implementação de software SAP

Esta secção tem passos detalhados para a realização de tarefas específicas no processo de configuração e implantação.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implementar cmdlets Azure PowerShell

Siga os passos descritos no artigo [Instale o módulo Azure PowerShell](/powershell/azure/install-az-ps)

Consulte frequentemente as atualizações dos cmdlets PowerShell, que normalmente são atualizados mensalmente. Siga os passos descritos [neste](/powershell/azure/install-az-ps#update-the-azure-powershell-module) artigo. Salvo indicação em contrário na Nota [SAP 1928533] ou SAP Note [2015553,]recomendamos que trabalhe com a versão mais recente dos cmdlets Azure PowerShell.

Para verificar a versão dos cmdlets Azure PowerShell instalados no seu computador, execute este comando PowerShell:

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implementar Azure CLI

Siga os passos descritos no artigo [Instale o Azure CLI](/cli/azure/install-azure-cli)

Consulte frequentemente as atualizações para o Azure CLI, que normalmente é atualizado mensalmente.

Para verificar a versão do Azure CLI que está instalada no seu computador, execute este comando:

```console
az --version
```

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Junte-se a um VM para um domínio no local (apenas Windows)

Se colocar os VMs SAP num cenário transversal, onde os ative directy e DNS no local são estendidos em Azure, espera-se que os VMs se juntem a um domínio no local. Os passos detalhados que toma para se juntar a um VM a um domínio no local, e o software adicional necessário para ser membro de um domínio no local, varia consoados pelo cliente. Normalmente, para se juntar a um VM num domínio no local, é necessário instalar software adicional, como software antimalware, e software de backup ou monitorização.

Neste cenário, também precisa de se certificar de que se as definições de procuração da Internet forem forçadas quando um VM se junta a um domínio no seu ambiente, a Conta do Sistema Local do Windows (S-1-5-18) no VM do Hóspede tem as mesmas definições de procuração. A opção mais fácil é forçar o representante utilizando uma Política de Grupo de domínio, que se aplica aos sistemas do domínio.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Faça o download, instale e ative o Agente Azure VM

Para máquinas virtuais que são implantadas a partir de uma imagem de SO que não é generalizada (por exemplo, uma imagem que não tenha origem na Preparação do Sistema windows, ou sisprep, ferramenta), é necessário descarregar, instalar e ativar manualmente o Agente VM Azure.

Se colocar um VM do Mercado Azure, este passo não é necessário. As imagens do Mercado Azure já têm o Agente Azure VM.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Descarregue o Agente Azure VM:
   1.  Descarregue o [pacote de instaladores Azure VM Agent](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Guarde o pacote MSI do Agente VM localmente num computador ou servidor pessoal.
1. Instale o Agente Azure VM:
   1.  Ligue-se ao Azure VM implantado utilizando o Protocolo de Ambiente de Trabalho Remoto (RDP).
   1.  Abra uma janela do Windows Explorer no VM e selecione o directório-alvo para o ficheiro MSI do Agente VM.
   1.  Arraste o ficheiro MSI do agente Azure VM do seu computador/servidor local para o directório-alvo do Agente VM no VM.
   1.  Clique duas vezes no ficheiro MSI no VM.
1. Para os VMs que se unem aos domínios no local, certifique-se de que eventuais definições de procuração na Internet também se aplicam à conta do Sistema Local do Windows (S-1-5-18) no VM, conforme descrito no [Configure o representante][deployment-guide-configure-proxy]. O Agente VM funciona neste contexto e precisa de ser capaz de se conectar ao Azure.

Não é necessária qualquer interação do utilizador para atualizar o Agente VM Azure. O Agente VM é atualizado automaticamente e não requer um reinício de VM.

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

Se o agente já estiver instalado, para atualizar o Agente Azure Linux, faça os passos descritos em [Atualizar o Agente Azure Linux num VM para a versão mais recente do GitHub][virtual-machines-linux-update-agent].

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configure o representante

Os passos que toma para configurar o proxy no Windows são diferentes da forma como configura o representante no Linux.

#### <a name="windows"></a>Windows

As configurações proxy devem ser configurada corretamente para que a conta do Sistema Local aceda à Internet. Se as definições de procuração não forem definidas pela Política de Grupo, pode configurar as definições para a conta do Sistema Local.

1. Ir para **iniciar,** inserir **gpedit.msc** e, em seguida, selecionar **Enter**.
1. Selecione   >  **modelos administrativos de** configuração do computador Windows  >  **Components** Internet  >  **Explorer**. Certifique-se de que a definição **Faça as definições de procuração por máquina (em vez de por utilizador)** estar desativadas ou não configuradas.
1. No **Painel de Controlo,** aceda às   >  **opções de Internet** do Network and Sharing Center .
1. No separador **'Ligações',** selecione o botão **de definições LAN.**
1. Desmarque a caixa de verificação **Detetar definições automaticamente**.
1. Selecione o **servidor proxy para a sua** caixa de verificação LAN e, em seguida, insira o endereço de procuração e a porta.
1. Selecione o botão **Advanced.**
1. Na caixa **exceções, insira** o endereço IP **168.63.129.16**. Selecione **OK**.

#### <a name="linux"></a>Linux

Configure o proxy correto no ficheiro de configuração do Microsoft Azure Guest Agent, que está localizado em \\ etc \\ waagent.conf.

Definir os seguintes parâmetros:

1. **ANFITRIÃO DE PROCURAÇÃO HTTP**. Por exemplo, coloque-o em **proxy.corp.local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **Porta de procuração HTTP**. Por exemplo, coloque-o a **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Reinicie o agente.

   ```console
   sudo service waagent restart
   ```

As definições de procuração em \\ etc \\ waagent.conf também se aplicam às extensões VM necessárias. Se quiser utilizar os repositórios Azure, certifique-se de que o tráfego para estes repositórios não está a passar pela sua intranet no local. Se criou rotas definidas pelo utilizador para permitir a escavação forçada, certifique-se de que adiciona uma rota que encaminha o tráfego para os repositórios diretamente para a Internet, e não através da sua ligação VPN site-to-site.

* **SLES**

  Também precisa adicionar rotas para os endereços IP listados em \\ etc \\ regiãoserverclnt.cfg. A seguinte figura mostra um exemplo:

  ![Túnel forçado][deployment-guide-figure-50]


* **RHEL**

  Também precisa adicionar rotas para os endereços IP dos anfitriões listados em \\ etc \\ yum.repos.d \\ rhui-load-balancers. Por exemplo, veja a figura anterior.

* **Oracle Linux**

  Não há repositórios para o Oracle Linux em Azure. Você precisa configurar seus próprios repositórios para Oracle Linux ou usar os repositórios públicos.

Para obter mais informações sobre as rotas definidas pelo utilizador, consulte [as rotas definidas pelo Utilizador e o encaminhamento IP][virtual-networks-udr-overview].

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configure a extensão azul para SAP

> [!NOTE]
> Declaração geral de apoio:  
> O suporte para a extensão Azure para SAP é fornecido através dos canais de suporte SAP. Se precisar de assistência com a Extensão Azure para SAP, abra um caso de apoio com [o SAP Support](https://support.sap.com/). 

Quando tiver preparado o VM como descrito nos [cenários de implementação de VMs para SAP on Azure,][deployment-guide-3]o Agente VM Azure está instalado na máquina virtual. O próximo passo é implantar a Extensão Azure para o SAP, que está disponível no Repositório de Extensão Azure nos datacenters globais do Azure. Para obter mais informações, consulte [o planeamento e implementação de Máquinas Virtuais Azure para o SAP NetWeaver][planning-guide-9.1].

Estamos em vias de lançar uma nova versão da Extensão Azure para SAP. A nova extensão utiliza a identidade atribuída ao sistema da máquina virtual para obter informações sobre os discos anexos, interfaces de rede e a própria máquina virtual. Para poder aceder a estes recursos, a identidade do sistema da máquina virtual necessita de permissão do Leitor para a máquina virtual, disco DE, discos de dados e interfaces de rede. Atualmente, recomendamos apenas instalar a nova extensão nos seguintes cenários:

1. Pretende instalar a extensão com modelos de gestor de recursos Terraform, Azure ou com outros meios que não o Azure CLI ou o Azure PowerShell
1. Pretende instalar a extensão em SUSE SLES 15 ou superior.
1. Suporte microsoft ou SAP pede-lhe para instalar a nova extensão
1. Você quer usar disco ultra azul ou discos geridos padrão

Para estes cenários, siga os passos do capítulo [Configure a nova Extensão Azure para SAP com Azure PowerShell][deployment-guide-configure-new-extension-ps] para Azure PowerShell ou [Configure a nova Extensão Azure para SAP com Azure CLI][deployment-guide-configure-new-extension-cli] para Azure CLI.

Siga [o Azure PowerShell][deployment-guide-4.5.1] ou [o Azure CLI][deployment-guide-4.5.2] para instalar e configurar a versão padrão da Extensão Azure para SAP.

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell para Linux e Windows VMs

Para instalar a extensão Azure para SAP utilizando o PowerShell:

1. Certifique-se de que instalou a versão mais recente do cmdlet Azure PowerShell. Para obter mais informações, consulte [a implementação de cmdlets Azure PowerShell][deployment-guide-4.1].  
1. Execute o seguinte cmdlet do PowerShell.
    Para obter uma lista de ambientes disponíveis, executar cmdlet `Get-AzEnvironment` . Se quiser utilizar o Azure global, o seu ambiente é **azureCloud.** Para Azure China 21Vianet, selecione **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Depois de introduzir os dados da sua conta, o script implementa as extensões necessárias e permite as funcionalidades necessárias. Esta ação poderá demorar alguns minutos.
Para obter mais informações sobre `Set-AzVMAEMExtension` , consulte [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Execução bem sucedida do cmdlet Azure específico da SAP Set-AzVMAEMExtension][deployment-guide-figure-900]

A `Set-AzVMAEMExtension` configuração faz todos os passos para configurar a recolha de dados do anfitrião para o SAP.

A saída do script inclui as seguintes informações:

* A confirmação de que a recolha de dados para o disco de SO e todos os discos de dados adicionais foi configurada.
* As duas próximas mensagens confirmam a configuração das Métricas de Armazenamento para uma conta de armazenamento específica.
* Uma linha de saída dá o estado da atualização real da extensão VM para a configuração SAP.
* Outra linha de saída confirma que a configuração foi implementada ou atualizada.
* A última linha de saída é informativa. Mostra as suas opções para testar a extensão VM para a configuração SAP.
* Para verificar se todas as etapas da Extensão VM do Azure para a configuração SAP foram executadas com sucesso, e que a Infraestrutura Azure fornece os dados necessários, proceda com a verificação de prontidão para a extensão do Azure para SAP, conforme descrito no [controlo de prontidão para a extensão de Azure para SAP][deployment-guide-5.1].
* Aguarde 15-30 minutos para que o Azure Diagnostics recolha os dados relevantes.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI para Linux VMs

Para instalar a extensão Azure para SAP utilizando o Azure CLI:

   1. Instale o CLI clássico do Azure, conforme descrito na [Instalação do Clássico CLI][azure-cli]azul .
   1. Inscreva-se na sua conta Azure:

      ```console
      azure login
      ```

   1. Mude para o modo Azure Resource Manager:

      ```console
      azure config mode arm
      ```

   1. Ativar a extensão do Azure para o SAP:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Instalar com O Azure CLI 2.0

   1. Instale o Azure CLI 2.0, conforme descrito na [Instalação Azure CLI 2.0][azure-cli-2].
   1. Inscreva-se na sua conta Azure:

      ```azurecli
      az login
      ```

   1. Instalar extensão Azure CLI AEM
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Instale a extensão com
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Verifique se a extensão Azure para SAP está ativa no VM Azure Linux. Verifique se o ficheiro \\ var \\ lib \\ AzureEnhancedMonitor \\ PerfCounters existe. Se existir, num pedido de comando, execute este comando para exibir informações recolhidas pela Extensão Azure para SAP:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   O resultado tem o seguinte aspeto:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-powershell"></a><a name="2ad55a0d-9937-4943-9dd2-69bc2b5d3de0"></a>Configure a nova extensão Azure para SAP com Azure PowerShell

A nova extensão VM para SAP utiliza uma Identidade Gerida atribuída ao VM para aceder aos dados de monitorização e configuração do VM. Para instalar a nova Extensão Azure para SAP utilizando o PowerShell, primeiro tem de atribuir essa identidade ao VM e conceder esse acesso identitário a todos os recursos que estão a ser utilizados por esse VM, por exemplo discos e interfaces de rede.

> [!NOTE]
> Os seguintes passos requerem privilégios do Proprietário sobre o grupo de recursos ou recursos individuais (máquina virtual, discos de dados, etc.)

1. Certifique-se de que utiliza o Agente anfitrião SAP 7.21 PL 47 ou superior.
1. Certifique-se de desinstalar a versão atual da Extensão VM para SAP. Não é suportado para instalar ambas as versões da Extensão VM para SAP na mesma máquina virtual.
1. Certifique-se de que instalou a versão mais recente do cmdlet Azure PowerShell (pelo menos 4.3.0). Para obter mais informações, consulte [a implementação de cmdlets Azure PowerShell][deployment-guide-4.1].
1. Execute o seguinte cmdlet do PowerShell.
    Para obter uma lista de ambientes disponíveis, executar cmdlet `Get-AzEnvironment` . Se quiser utilizar o Azure global, o seu ambiente é **azureCloud.** Para Azure China 21Vianet, selecione **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-cli"></a><a name="c8749c24-fada-42ad-b114-f9aae2dc37da"></a>Configure a nova extensão Azure para SAP com Azure CLI

A nova extensão VM para SAP utiliza uma Identidade Gerida atribuída ao VM para aceder aos dados de monitorização e configuração do VM. Para instalar a nova Extensão Azure para SAP utilizando o Azure CLI, primeiro tem de atribuir essa identidade ao VM e conceder esse acesso identitário a todos os recursos que estão a ser utilizados por esse VM, por exemplo discos e interfaces de rede.

> [!NOTE]
> Os seguintes passos requerem privilégios do Proprietário sobre o grupo de recursos ou recursos individuais (máquina virtual, discos de dados, etc.)

1. Certifique-se de que utiliza o Agente anfitrião SAP 7.21 PL 47 ou superior.
1. Certifique-se de desinstalar a versão atual da Extensão VM para SAP. Não é suportado para instalar ambas as versões da Extensão VM para SAP na mesma máquina virtual.
1. Instale o Azure CLI 2.0, conforme descrito na [Instalação Azure CLI 2.0][azure-cli-2].

1. Inscreva-se na sua conta Azure:

   ```azurecli
   az login
   ```

1. Siga os passos na [Configuração identidades geridas para recursos Azure num Azure VM utilizando][qs-configure-cli-windows-vm] o artigo Azure CLI para permitir uma identidade gerida System-Assigned para o VM. User-Assigned Identidades Geridas não são suportadas pela extensão VM para SAP. No entanto, pode ativar ambos, uma identidade atribuída ao sistema e uma identidade atribuída ao utilizador.

   Exemplo:
   ```azurecli
   az vm identity assign -g <resource-group-name> -n <vm name>
   ```

1. Atribuir o acesso à Identidade Gerida ao grupo de recursos do VM ou a todas as interfaces de rede, discos geridos e o próprio VM como descrito na [Atribuição de um acesso de identidade gerido a um recurso utilizando o Azure CLI][howto-assign-access-cli]

    Exemplo:

    ```azurecli
    # Azure CLI on Linux
    spID=$(az resource show -g <resource-group-name> -n <vm name> --query identity.principalId --out tsv --resource-type Microsoft.Compute/virtualMachines)
    rgId=$(az group show -g <resource-group-name> --query id --out tsv)
    az role assignment create --assignee $spID --role 'Reader' --scope $rgId

    # Azure CLI on Windows/PowerShell
    $spID=az resource show -g <resource-group-name> -n <vm name> --query identity.principalId --out tsv --resource-type Microsoft.Compute/virtualMachines
    $rgId=az group show -g <resource-group-name> --query id --out tsv
    az role assignment create --assignee $spID --role 'Reader' --scope $rgId
    ```

1. Executar o seguinte comando Azure CLI para instalar a Extensão Azure para SAP.
    A extensão é atualmente suportada apenas no AzureCloud. Azure China 21Vianet, Governo Azure ou qualquer outro ambiente especial ainda não são apoiados.

    ```azurecli
    # Azure CLI on Linux
    ## For Linux machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Linux --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'

    ## For Windows machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Windows --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'

    # Azure CLI on Windows/PowerShell
    ## For Linux machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Linux --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{\"system\":\"SAP\"}'

    ## For Windows machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Windows --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{\"system\":\"SAP\"}'
    ```

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Verificações e resolução de problemas

Depois de ter implantado o seu Azure VM e configurar a extensão Azure relevante para o SAP, verifique se todos os componentes da extensão estão a funcionar como esperado.

Verifique a verificação de prontidão para a extensão Azure para SAP, conforme descrito na [verificação de prontidão para a extensão Azure para SAP][deployment-guide-5.1]. Se todos os resultados de verificação de prontidão forem positivos e todos os contadores de desempenho relevantes parecerem OK, a extensão Azure para SAP foi configurada com sucesso. Pode proceder à instalação do Agente Anfitrião SAP, conforme descrito nas Notas SAP em [recursos SAP][deployment-guide-2.2]. Se a verificação de prontidão indicar que faltam balcões, verifique o exame de saúde da Extensão Azure para SAP, conforme descrito no [Controlo de Saúde para a extensão do Azure para a configuração SAP][deployment-guide-5.2]. Para obter mais opções de resolução de problemas, consulte [a extensão de Azure de resolução de problemas para SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Verificação de prontidão para a extensão do Azure para o SAP

> [!NOTE]
> Existem duas versões da extensão VM. Este capítulo cobre a extensão VM predefinida. Se instalou a nova extensão VM, consulte o capítulo [Readiness check for the new Azure Extension for SAP][deployment-guide-5.1-new]

Esta verificação garante que todas as métricas de desempenho que aparecem dentro da sua aplicação SAP são fornecidas pela extensão Azure subjacente para SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Faça a verificação de prontidão num VM do Windows

1. Inscreva-se na máquina virtual Azure (não é necessário utilizar uma conta de administração).
1. Abra uma janela de Linha de Comandos.
1. Na pressão de comando, altere o diretório para a pasta de instalação da Extensão Azure para SAP: C: \\ \\ Pacotes Plugins \\ Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ &lt; versão>\\ queda

   A *versão* no caminho para a extensão pode variar. Se vir pastas para várias versões da extensão na pasta de instalação, verifique a configuração do serviço AzureEnhancedMonitoring Windows e, em seguida, mude para a pasta indicada como *Caminho para executável*.

   ![Propriedades de serviço que executam a Extensão Azure para SAP][deployment-guide-figure-1000]

1. Na pronta do comando, corra **azperflib.exe** sem parâmetros.

   > [!NOTE]
   > Azperflib.exe funciona em loop e atualiza os contadores recolhidos a cada 60 segundos. Para terminar o ciclo, feche a janela de aviso de comando.
   >
   >

Se a extensão Azure para SAP não estiver instalada, ou se o serviço AzureEnhancedMonitoring não estiver a funcionar, a extensão não foi configurada corretamente. Para obter informações detalhadas sobre como implementar a extensão, consulte [a resolução de problemas da extensão Azure para SAP][deployment-guide-5.3].

> [!NOTE]
> O Azperflib.exe é um componente que não pode ser usado para fins próprios. Trata-se de um componente que fornece exclusivamente dados de infraestrutura Azure relacionados com o VM para o Agente Anfitrião SAP.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Verifique a saída de azperflib.exe

Azperflib.exe produção mostra todos os contadores de desempenho Azure povoados para SAP. Na parte inferior da lista de contadores recolhidos, um resumo e um indicador de saúde mostram o estado da Extensão Azure para o SAP.

![Saída do controlo de saúde executando azperflib.exe, o que indica que não existem problemas][deployment-guide-figure-1100]
<a name="figure-11"></a>

Verifique o resultado devolvido para a produção total dos **Contadores,** que é reportada como vazia, e para o **estado de saúde,** mostrado no valor anterior.

Interpretar os valores resultantes da seguinte forma:

| Azperflib.exe valores de resultados | Extensão Azure para o estado de saúde do SAP |
| --- | --- |
| **Chamadas API - não disponível** | Os contadores que não estão disponíveis podem não ser aplicáveis à configuração da máquina virtual, ou são erros. Ver **Estado de Saúde**. |
| **Total de contadores - vazio** |Os dois balcões de armazenamento Azure podem estar vazios: <ul><li>Armazenamento Ler Op Latência Servidor msec</li><li>Armazenamento Ler Op Latência E2E msec</li></ul>Todos os outros balcões devem ter valores. |
| **Estado da saúde** |Só OK se o estado de retorno mostrar **OK**. |
| **Diagnóstico** |Informação detalhada sobre o estado de saúde. |

Se o valor **do estado da saúde** não estiver **OK,** siga as instruções em [Health check for Azure Extension for SAP configuration][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Faça a verificação de prontidão num Linux VM

1. Ligue-se à Máquina Virtual Azure utilizando sSH.

1. Verifique a saída da extensão Azure para SAP.

   a.  Executar `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Resultado esperado**: Lista de devoluções de contadores de desempenho. O ficheiro não deve estar vazio.

   b. Executar `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Resultado esperado**: Devolve uma linha onde o erro não é **nenhum**, por exemplo, **3;config; Erro;;0;0; nenhum;0;1456416792;tst-servercs;**

   c. Executar `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Resultado esperado**: Devoluções como vazias ou não existe.

Se o controlo anterior não tiver sido bem sucedido, efetue estes controlos adicionais:

1. Certifique-se de que o waagent está instalado e ativado.

   a.  Executar `sudo ls -al /var/lib/waagent/`

     **Resultado esperado**: Lista o conteúdo do diretório waagent.

   b.  Executar `ps -ax | grep waagent`

   **Resultado esperado**: Apresenta uma entrada semelhante a: `python /usr/sbin/waagent -daemon`

1. Certifique-se de que a extensão Azure para SAP está instalada e em funcionamento.

   a.  Executar `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Resultado esperado**: Lista o conteúdo da Extensão Azure para o diretório SAP.

   b. Executar `ps -ax | grep AzureEnhanced`

   **Resultado esperado**: Apresenta uma entrada semelhante a: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Instale o Agente anfitrião SAP, conforme descrito na Nota [SAP 1031096,]e verifique a saída de `saposcol` .

   a.  Executar `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Executar `dump ccm`

   c.  Verifique se a métrica **de acesso de monitorização Virtualization_Configuration\melhorada** é **verdadeira**.

Se já tiver um servidor de aplicação SAP NetWeaver ABAP instalado, abra a transação ST06 e verifique se a monitorização está ativada.

Se algum destes controlos falhar e para obter informações detalhadas sobre como recolocar a extensão, consulte [a resolução de problemas da extensão Azure para SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-new-azure-extension-for-sap"></a><a name="7bf24f59-7347-4c7a-b094-4693e4687ee5"></a>Verificação de prontidão para a nova extensão do Azure para SAP

> [!NOTE]
> Existem duas versões da extensão VM. Este capítulo abrange a nova extensão VM. Se tiver instalado a extensão VM predefinida, consulte o capítulo [Readiness Check for Azure Extension for SAP][deployment-guide-5.1].

Esta verificação garante que todas as métricas de desempenho que aparecem dentro da sua aplicação SAP são fornecidas pela extensão Azure subjacente para SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Faça a verificação de prontidão num VM do Windows

1. Inscreva-se na máquina virtual Azure (não é necessário utilizar uma conta de administração).
1. Abra um navegador web e navegue para http://127.0.0.1:11812/azure4sap/metrics
1. O navegador deve exibir ou descarregar um ficheiro XML que contenha os dados de monitorização da sua máquina virtual. Se não for esse o caso, certifique-se de que a Extensão Azure para SAP está instalada.

##### <a name="check-the-content-of-the-xml-file"></a>Verifique o conteúdo do ficheiro XML

O ficheiro XML a que pode aceder http://127.0.0.1:11812/azure4sap/metrics contém todos os contadores de desempenho Azure povoados para SAP. Contém também um indicador sumário e sanitário do estado da Extensão Azure para o SAP.

Verifique o valor do elemento **Descrição da Saúde do Fornecedor.** Se o valor não for **OK,** siga as instruções em [Health check para a nova extensão Azure para a configuração SAP][deployment-guide-5.2-new].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Faça a verificação de prontidão num Linux VM

1. Ligue-se à Máquina Virtual Azure utilizando sSH.

1. Verifique a saída do seguinte comando

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **Resultado esperado**: Devolve um documento XML que contém a informação de monitorização da máquina virtual, dos seus discos e interfaces de rede.

Se o controlo anterior não tiver sido bem sucedido, efetue estes controlos adicionais:

1. Certifique-se de que o waagent está instalado e ativado.

   a.  Executar `sudo ls -al /var/lib/waagent/`

     **Resultado esperado**: Lista o conteúdo do diretório waagent.

   b.  Executar `ps -ax | grep waagent`

   **Resultado esperado**: Apresenta uma entrada semelhante a: `python /usr/sbin/waagent -daemon`

1. Certifique-se de que a extensão Azure para SAP está instalada e em funcionamento.

   a.  Executar `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'`

   **Resultado esperado**: Lista o conteúdo da Extensão Azure para o diretório SAP.

   b. Executar `ps -ax | grep AzureEnhanced`

   **Resultado esperado**: Apresenta uma entrada semelhante a: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`

1. Instale o Agente anfitrião SAP, conforme descrito na Nota [SAP 1031096,]e verifique a saída de `saposcol` .

   a.  Executar `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Executar `dump ccm`

   c.  Verifique se a métrica **de acesso de monitorização Virtualization_Configuration\melhorada** é **verdadeira**.

Se já tiver um servidor de aplicação SAP NetWeaver ABAP instalado, abra a transação ST06 e verifique se a monitorização está ativada.

Se algum destes controlos falhar e para obter informações detalhadas sobre como recolocar a extensão, consulte [a resolução de problemas da nova extensão Azure para SAP][deployment-guide-5.3-new].

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Verificação de saúde para a extensão Azure para configuração SAP

> [!NOTE]
> Existem duas versões da extensão VM. Este capítulo cobre a extensão VM predefinida. Se tiver instalado a nova extensão VM, consulte o capítulo [Health check para a nova extensão Azure para a configuração SAP][deployment-guide-5.2-new].

Se alguns dos dados da infraestrutura não forem entregues corretamente, como indicado no ensaio descrito na [verificação de prontidão para a extensão de Azure para SAP,][deployment-guide-5.1]verifique `Test-AzVMAEMExtension` se a infraestrutura Azure e a Extensão Azure para SAP estão configuradas corretamente.

1. Certifique-se de que instalou a versão mais recente do cmdlet Azure PowerShell, conforme descrito na [implementação de cmdlets Azure PowerShell][deployment-guide-4.1].
1. Execute o seguinte cmdlet do PowerShell. Para obter uma lista de ambientes disponíveis, execute o cmdlet `Get-AzEnvironment` . Para utilizar o Azure global, selecione o ambiente **AzureCloud.** Para Azure China 21Vianet, selecione **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. O script testa a configuração da máquina virtual selecionada.

   ![Saída de teste bem sucedido da Extensão Azure para SAP][deployment-guide-figure-1300]

Certifique-se de que todos os resultados dos exames de saúde **estão bem**. Se algumas verificações não apresentarem **OK,** executar o cmdlet de atualização como descrito no [Configure a Extensão Azure para SAP][deployment-guide-4.5]. Aguarde 15 minutos e repita os controlos descritos na [verificação de prontidão para a extensão de Azure para][deployment-guide-5.1] a extensão de SAP e [verificação de saúde para extensão de Azure para configuração SAP][deployment-guide-5.2]. Se as verificações ainda indicarem um problema com alguns ou todos os contadores, consulte [a resolução de problemas da extensão Azure para SAP][deployment-guide-5.3].

> [!Note]
> Pode experimentar alguns avisos nos casos em que utiliza Discos Azure Standard Geridos. Serão apresentados avisos em vez dos testes que retornam "OK". Isto é normal e pretendido no caso desse tipo de disco. Ver também ver [Resolução de Problemas da Extensão Azure para SAP][deployment-guide-5.3]
> 

### <a name="health-check-for-the-new-azure-extension-for-sap-configuration"></a><a name="464ac96d-7d3c-435d-a5ae-3faf3bfef4b3"></a>Verificação de saúde para a nova extensão do Azure para a configuração DOM

> [!NOTE]
> Existem duas versões da extensão VM. Este capítulo abrange a nova extensão VM. Se tiver instalado a extensão VM predefinida, consulte o capítulo [Health check para a extensão Azure para a configuração SAP][deployment-guide-5.2].

Se alguns dos dados da infraestrutura não forem entregues corretamente, como indicado no teste descrito na [verificação de prontidão para a extensão de Azure para SAP,][deployment-guide-5.1-new]verifique `Get-AzVMExtension` se a extensão Azure para SAP está instalada. O `Test-AzVMAEMExtension` ainda não apoia a nova extensão. Assim que o cmdlet apoiar a nova extensão, iremos atualizar este artigo.

1. Certifique-se de que instalou a versão mais recente do cmdlet Azure PowerShell, conforme descrito na [implementação de cmdlets Azure PowerShell][deployment-guide-4.1].
1. Execute o seguinte cmdlet do PowerShell. Para obter uma lista de ambientes disponíveis, execute o cmdlet `Get-AzEnvironment` . Para utilizar o Azure global, selecione o ambiente **AzureCloud.** Para Azure China 21Vianet, selecione **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. O cmdlet testa a configuração da extensão VM para SAP na máquina virtual selecionada.

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Extensão de Azure de resolução de problemas para SAP

> [!NOTE]
> Existem duas versões da extensão VM. Este capítulo cobre a extensão VM predefinida. Se tiver instalado a nova extensão VM, consulte o capítulo [Troubleshooting the new Azure Extension for SAP][deployment-guide-5.3-new].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Logotipo do Windows.][Logo_Windows] Os contadores de desempenho do Azure não aparecem

O serviço AzureEnhancedMonitoring Windows recolhe métricas de desempenho em Azure. Se o serviço não tiver sido instalado corretamente ou se não estiver a funcionar no seu VM, não podem ser recolhidas métricas de desempenho.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>O diretório de instalação da Extensão Azure para SAP está vazio

###### <a name="issue"></a>Problema

O diretório de instalação C: \\ \\ Pacotes Plugins \\ Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ &lt;>gota está \\ vazia.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se se trata de um problema de procuração (como descrito anteriormente). Pode ser necessário reiniciar a máquina ou repetir o `Set-AzVMAEMExtension` script de configuração.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Serviço para extensão Azure para SAP não existe

###### <a name="issue"></a>Problema

O serviço AzureEnhancedMonitoring Windows não existe.

Azperflib.exe saída lança um erro:

![Execução de azperflib.exe indica que o serviço da Extensão Azure para SAP não está em execução][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Solução

Se o serviço não existir, a Extensão Azure para SAP não foi instalada corretamente. Reimplante a extensão utilizando os passos descritos para o seu cenário de implantação em [cenários de implementação de VMs para SAP em Azure][deployment-guide-3].

Depois de implementar a extensão, após uma hora, verifique novamente se os contadores de desempenho Azure estão fornecidos no Azure VM.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Serviço para extensão Azure para SAP existe, mas não começa

###### <a name="issue"></a>Problema

O serviço AzureEnhancedMonitoring Windows existe e está ativado, mas não é iniciado. Para mais informações, consulte o registo do evento de aplicação.

###### <a name="solution"></a>Solução

A configuração está incorreta. Reinicie a extensão azul para SAP no VM, conforme descrito no [Configure the Azure Extension for SAP][deployment-guide-4.5].

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Logotipo do Windows.][Logo_Windows] Faltam alguns balcões de desempenho da Azure.

O serviço AzureEnhancedMonitoring Windows recolhe métricas de desempenho em Azure. O serviço obtém dados de várias fontes. Alguns dados de configuração são recolhidos localmente, e algumas métricas de desempenho são lidas a partir de Azure Diagnostics. Os contadores de armazenamento são utilizados a partir da sua sessão de registo no nível de subscrição de armazenamento.

Se a resolução de problemas utilizando o SAP Note [1999351] não resolver o problema, reexamque o script de `Set-AzVMAEMExtension` configuração. Pode ter de esperar uma hora porque os contadores de análise de armazenamento ou de diagnóstico podem não ser criados imediatamente após a sua ativação. Se o problema persistir, abra uma mensagem de suporte ao cliente SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Logotipo linux.][Logo_Linux] Os contadores de desempenho do Azure não aparecem

As métricas de desempenho em Azure são recolhidas por um daemon. Se o daemon não estiver a funcionar, não podem ser recolhidas métricas de desempenho.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>O diretório de instalação da Extensão Azure para SAP está vazio

###### <a name="issue"></a>Problema

O diretório \\ var \\ lib \\ waagent \\ não tem uma subdireção para a Extensão Azure para SAP.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se se trata de um problema de procuração (como descrito anteriormente). Pode ser necessário reiniciar a máquina e/ou refazer o `Set-AzVMAEMExtension` script de configuração.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>A execução de Set-AzVMAEMExtension e Test-AzVMAEMExtension mostram mensagens de aviso afirmando que os Discos Geridos Padrão não são suportados

###### <a name="issue"></a>Problema

Ao executar mensagens Set-AzVMAEMExtension ou Test-AzVMAEMExtension como estas são mostradas:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Executar azperfli.exe como descrito anteriormente pode obter um resultado que indique um estado não saudável. 

###### <a name="solution"></a>Solução

As mensagens são causadas pelo facto de os Discos Geridos Standard não estarem a entregar as APIs utilizadas pela Extensão SAP para o SAP para verificar as estatísticas das Contas de Armazenamento Standard Azure. Isto não é uma questão de preocupação. A razão para introduzir os dados de recolha das contas standard de armazenamento de discos foi o estrangulamento das entradas e saídas que ocorreram frequentemente. Os discos geridos evitarão esse estrangulamento limitando o número de discos numa conta de armazenamento. Portanto, não ter este tipo de dados não é crítico.


#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Logotipo linux.][Logo_Linux] Faltam alguns balcões de desempenho da Azure.

As métricas de desempenho em Azure são recolhidas por um daemon, que obtém dados de várias fontes. Alguns dados de configuração são recolhidos localmente, e algumas métricas de desempenho são lidas a partir de Azure Diagnostics. Os balcões de armazenamento provêm dos registos da sua subscrição de armazenamento.

Para obter uma lista completa e atualizada de questões conhecidas, consulte o SAP Note [1999351,]que dispõe de informações adicionais de resolução de problemas para a extensão de Azure para SAP.

Se a resolução de problemas utilizando o SAP Note [1999351] não resolver o problema, refaça o script de `Set-AzVMAEMExtension` configuração descrito no [Configure the Azure Extension for SAP][deployment-guide-4.5]. Pode ter de esperar uma hora porque os contadores de análise de armazenamento ou de diagnóstico podem não ser criados imediatamente após a sua ativação. Se o problema persistir, abra uma mensagem de suporte ao cliente SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.

### <a name="troubleshooting-the-new-azure-extension-for-sap"></a><a name="b7afb8ef-a64c-495d-bb37-2af96688c530"></a>Resolução de problemas da nova extensão do Azure para o SAP

> [!NOTE]
> Existem duas versões da extensão VM. Este capítulo abrange a nova extensão VM. Se tiver instalado a extensão VM predefinida, consulte o capítulo [Resolução de Problemas da Extensão Azure para SAP][deployment-guide-5.3].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Logotipo do Windows.][Logo_Windows] Os contadores de desempenho do Azure não aparecem

O processo AzureEnhancedMonitoring recolhe métricas de desempenho em Azure. Se o processo não estiver a decorrer no seu VM, não podem ser recolhidas métricas de desempenho.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>O diretório de instalação da Extensão Azure para SAP está vazio

###### <a name="issue"></a>Problema

O diretório de instalação C: \\ \\ Pacotes Plugins \\ Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows \\ &lt; versão> está vazia.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se se trata de um problema de procuração (como descrito anteriormente). Pode ser necessário reiniciar a máquina ou voltar a instalar a extensão VM.

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Logotipo do Windows.][Logo_Windows] Faltam alguns balcões de desempenho da Azure.

O processo AzureEnhancedMonitoring Windows recolhe métricas de desempenho em Azure. O processo obtém dados de várias fontes. Alguns dados de configuração são recolhidos localmente, e algumas métricas de desempenho são lidas a partir do Azure Monitor.

Se a resolução de problemas utilizando o SAP Note [1999351] não resolver o problema, abra uma mensagem de apoio ao cliente SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux. Por favor, anexe a versão C: \\ \\ Pacotes Plugins \\ Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows \\ &lt;>logapp.txt ao \\ incidente.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Logotipo linux.][Logo_Linux] Os contadores de desempenho do Azure não aparecem

As métricas de desempenho em Azure são recolhidas por um daemon. Se o daemon não estiver a funcionar, não podem ser recolhidas métricas de desempenho.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>O diretório de instalação da Extensão Azure para SAP está vazio

###### <a name="issue"></a>Problema

O diretório \\ var \\ lib \\ waagent \\ não tem uma subdireção para a Extensão Azure para SAP.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se se trata de um problema de procuração (como descrito anteriormente). Pode ser necessário reiniciar a máquina e/ou voltar a instalar a extensão VM.

#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Logotipo linux.][Logo_Linux] Faltam alguns balcões de desempenho da Azure.

As métricas de desempenho em Azure são recolhidas por um daemon, que obtém dados de várias fontes. Alguns dados de configuração são recolhidos localmente, e algumas métricas de desempenho são lidas a partir do Azure Monitor.

Para obter uma lista completa e atualizada de questões conhecidas, consulte o SAP Note [1999351,]que dispõe de informações adicionais de resolução de problemas para a extensão de Azure para SAP.

Se a resolução de problemas utilizando o SAP Note [1999351] não resolver o problema, instale novamente a extensão, tal como descrito no [Configure the Azure Extension for SAP][deployment-guide-4.5]. Se o problema persistir, abra uma mensagem de suporte ao cliente SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux. Por favor, anexe o ficheiro de registo /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux- &lt; versão>/logapp.txt ao incidente.

## <a name="azure-extension-error-codes"></a>Códigos de erro de extensão Azure

| ID de erro | Descrição do erro | Solução |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | Falta a configuração da aplicação. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | Nenhuma identificação de implementação na app config. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | Sem RoleInstanceId na app config. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | Sem RoleInstanceId na app config. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Não consigo ler a configuração do Azure. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | O ficheiro de configuração da aplicação está em falta. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | Sem tamanho VM na app config. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | O contador GlobalMemoryStatusEx falhou. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | MaxHwFrequency contador falhou. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | Os contadores da NIC falharam. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | O contador de mapeamento do disco falhou. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | O contador de nomes do processador falhou. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | O contador de mapeamento do disco falhou. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | O 'tipo de disco' métrico está em falta no ficheiro de configuração de extensão config.xml. O 'tipo de disco' juntamente com outros balcões foi introduzido em v2.2.0.68 12/16/2015. Se implementou a extensão antes de 12/16/2015, utiliza o ficheiro de configuração antigo. O quadro de extensão Azure atualiza automaticamente a extensão para uma versão mais recente, mas o config.xml permanece inalterado. Para atualizar a configuração, descarregue e execute o script de configuração mais recente do PowerShell. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | Sem cache de disco. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | Sem saída SLA de disco. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | Sem disco SLA IOPS. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | O contador de mapeamento do disco falhou. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | O último contador de alterações de hardware falhou. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | Os contadores nic falharam | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | Devido ao sysprep do VM, o seu Windows SID mudou. | [recolocar após sysprep][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>str/007 | O acesso à análise de armazenamento falhou. <br /><br />Como a população de dados de análise de armazenamento de um VM recém-criado pode precisar de até meia hora, o erro pode desaparecer após algum tempo. Se o erro ainda aparecer, reencambam o script de configuração. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="str_010"></a>str/010 | Sem balcões de Storage Analytics. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="str_009"></a>str/009 | Storage Analytics falhou. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="wad_004"></a>wad/004 | Configuração do BAD WAD. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="wad_002"></a>wad/002 | Formato INESPERADO WAD. | [suporte de contato][deployment-guide-contact-support] |
| <a name="wad_001"></a>wad/001 | Não foram encontrados balcões do WAD. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/040 | Balcões do WAD encontrados. | [suporte de contato][deployment-guide-contact-support] |
| <a name="wad_003"></a>wad/003 | Não consigo ler a mesa do WAD. Não há nenhuma ligação com a mesa WAD. Pode haver várias causas disto:<br /><br /> 1) configuração desatualizada <br />2) nenhuma ligação de rede ao Azure <br />3) problemas com a configuração do WAD | [executar script de configuração][deployment-guide-run-the-script]<br />[corrigir a ligação à internet][deployment-guide-fix-internet-connection]<br />[suporte de contato][deployment-guide-contact-support] |
| <a name="prf_011"></a>prf/011 | As métricas do Perfmon NIC falharam. | [suporte de contato][deployment-guide-contact-support] |
| <a name="prf_012"></a>prf/012 | As métricas do disco perfmon falharam. | [suporte de contato][deployment-guide-contact-support] |
| <a name="prf_013"></a>prf/013 | Algumas métricas pré-hão falharam. | [suporte de contato][deployment-guide-contact-support] |
| <a name="prf_014"></a>prf/014 | Perfmon não conseguiu criar um balcão. | [suporte de contato][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | Nenhum fornecedor métrico configurado. | [suporte de contato][deployment-guide-contact-support] |
| <a name="str_006"></a>str/006 | Bad Storage Analytics config. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="str_032"></a>str/032 | As métricas de Storage Analytics falharam. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | Um dos fornecedores métricos falhou. | [executar script de configuração][deployment-guide-run-the-script] |
| <a name="str_034"></a>str/034 | O fio do fornecedor falhou. | [suporte de contato][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>Orientações detalhadas sobre as soluções fornecidas

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>Executar o script de configuração

Siga os passos do capítulo [Configure a Extensão Azure para SAP][deployment-guide-4.5] neste guia para instalar novamente a extensão. Note que alguns balcões podem precisar de até 30 minutos para o provisionamento.

Se os erros não desaparecerem, [contacte o suporte][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>Contactar o Suporte

Erro inesperado ou não há solução conhecida. Recolha o ficheiro AzureEnhancedMonitoring_service.log localizado na pasta C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \drop (Windows) ou /var/log/azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux (Linux) e contacte o suporte DA SAP para mais assistência.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>Recolocação após sysprep

Se planeia construir uma imagem de SO (que pode incluir software SAP), recomenda-se que esta imagem não inclua a extensão Azure para SAP. Deverá instalar a extensão Azure para SAP depois de ter sido implantada a nova instância da imagem geralizada do SO.

No entanto, se a sua imagem de SO generalizada e sisprepped já contiver a Extensão Azure para SAP, pode aplicar a seguinte solução para reconfigurar a extensão, na instância VM recentemente implantada:

* No caso VM recentemente implantado, elimine o conteúdo das seguintes pastas:  
  C:\Pacotes\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEncedMonitoring.AzureCATExtensionHandler \\ \<version\> \Status

* Siga os passos do capítulo [Configure a Extensão Azure para SAP][deployment-guide-4.5] neste guia para instalar novamente a extensão.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>Corrigir a ligação à Internet

A Máquina Virtual Microsoft Azure que executa a extensão Azure para SAP requer acesso à Internet. Se este Azure VM fizer parte de uma Rede Virtual Azure ou de um domínio no local, certifique-se de que as definições de procuração relevantes estão definidas. Estas definições também devem ser válidas para que a conta Do Sistema Local aceda à Internet. Siga o capítulo [Configure o representante][deployment-guide-configure-proxy] neste guia.

Além disso, se necessitar de definir um endereço IP estático para o seu Azure VM, não o coloque manualmente dentro do Azure VM, mas desajuste-o utilizando o [portal Azure PowerShell](../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md), [Azure CLI](../../../virtual-network/virtual-networks-static-private-ip-arm-cli.md) [Azure](../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). O IP estático é propagado através do serviço Azure DHCP.

A definição manual de um endereço IP estático dentro do VM Azure não é suportada e pode levar a problemas com a extensão Azure para SAP.
