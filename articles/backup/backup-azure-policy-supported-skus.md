---
title: SKUs de VM com suporte para o Azure Policy
description: Um artigo que descreve os SKUs de VM suportados (por Editor, Oferta de Imagem e Imagem SKU) que são suportados para as Políticas de Azure incorporadas fornecidas pela Backup
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 4658d1beffe707682e173491edea1eac0db9c811
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "82183624"
---
# <a name="supported-vm-skus-for-azure-policy"></a>SKUs de VM com suporte para o Azure Policy

O Azure Backup fornece uma política incorporada (utilizando a Política Azure) que pode ser atribuída a **todos os VMs Azure em um local especificado dentro de um grupo de subscrição ou recursos**. Quando esta política é atribuída a um determinado âmbito, todos os novos VMs criados nesse âmbito são automaticamente configurados para cópia de segurança para um **cofre existente no mesmo local e subscrição**. A tabela abaixo lista todos os SKUs VM apoiados por esta política.

## <a name="supported-vms"></a>VMs suportados*

**Nome da política:** Configurar backup em VMs de um local para um cofre central existente no mesmo local

Editor de Imagem | Oferta de imagem | Imagem SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Centro de dados Windows Server 2012 (Centro de Dados 2012)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Datacenter (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (Centro de Dados 2012-R2)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Datacenter (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Datacenter Windows Server 2016 (Centro de Dados 2016)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter - Núcleo do Servidor (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter - Server Core (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Núcleo do Servidor de Datacenter 2019 do Windows Server com Contentores (2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Remote Desktop Session Host 2016 (2016-Datacenter-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Centro de dados Windows Server 2019 (Centro de Dados 2019)
MicrosoftWindowsServer | WindowsServer | Núcleo do Servidor de Datacenter do Windows Server 2019 (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Núcleo do Servidor de Datacenter 2019 do Windows Server (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Núcleo do servidor do datacenter do Windows Server 2019 com contentores (2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Núcleo do Servidor de Datacenter 2019 do Windows Server com Contentores (2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Centro de dados Windows Server 2019 com Contentores (2019-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Centro de dados Windows Server 2019 com Contentores (2019-Datacenter-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-com-Contentores-pequenadisco
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-com-Contentores-pequenadisco
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Todos os SKUs de imagem
MicrosoftSQLServer | SQL2016SP1-WS2016 | Todos os SKUs de imagem
MicrosoftSQLServer | SQL2016-WS2016 | Todos os SKUs de imagem
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Todos os SKUs de imagem
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Todos os SKUs de imagem
MicrosoftSQLServer | SQL2016-WS2012R2 | Todos os SKUs de imagem
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Todos os SKUs de imagem
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Todos os SKUs de imagem
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Todos os SKUs de imagem
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Todos os SKUs de imagem
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Todos os SKUs de imagem
MicrosoftRServer | MLServer-WS2016 | Todos os SKUs de imagem
MicrosoftVisualStudio | VisualStudio | Todos os SKUs de imagem
MicrosoftVisualStudio | Windows | Todos os SKUs de imagem
MicrosoftDynamicsAX | Dynamics | Pré-Req-AX7-Onebox-U8
microsoft-anúncios | janelas-dado-ciência-vm | Todos os SKUs de imagem
MicrosoftWindowsDesktop | Windows-10 | Todos os SKUs de imagem
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | SLES-HPC-Prioridade | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | SLES-Priority | 12.X
SUSE | SLES-BYOS | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | SLES-Standard | 12.X
Canónico | UbuntuServer | 14.04.0-LTS
Canónico | UbuntuServer | 14.04.1-LTS
Canónico | UbuntuServer | 14.04.2-LTS
Canónico | UbuntuServer | 14.04.3-LTS
Canónico | UbuntuServer | 14.04.4-LTS
Canónico | UbuntuServer | 14.04.5-DAILY-LTS
Canónico | UbuntuServer | 14.04.5-LTS
Canónico | UbuntuServer | 16.04-DAILY-LTS
Canónico | UbuntuServer | 16.04-LTS
Canónico | UbuntuServer | 16.04.0-LTS
Canónico | UbuntuServer | 18.04-DAILY-LTS
Canónico | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | CentOS-LVM | 6.X, 7.X
OpenLogic | CentOS-SRIOV | 6.X, 7.X
cloudera | cloudera-centos-os | 7.X
