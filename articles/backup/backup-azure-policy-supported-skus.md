---
title: SKUs de VM com suporte para o Azure Policy
description: Um artigo que descreve as VM SKUs suportadas (por Publisher, Image Offer e Image SKU) que são suportadas para as políticas azure incorporadas fornecidas pela Backup
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 4658d1beffe707682e173491edea1eac0db9c811
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183624"
---
# <a name="supported-vm-skus-for-azure-policy"></a>SKUs de VM com suporte para o Azure Policy

A Azure Backup fornece uma política incorporada (utilizando a Política Azure) que pode ser atribuída a **todos os VMs Azure num local especificado dentro**de um grupo de subscrição ou de recursos. Quando esta política é atribuída a um dado âmbito, todos os novos VMs criados nesse âmbito são automaticamente configurados para backup para um **cofre existente no mesmo local e subscrição**. A tabela abaixo lista todas as VM SKUs apoiadas por esta política.

## <a name="supported-vms"></a>VMs suportados*

**Nome da política:** Configure cópias de segurança em VMs de um local para um cofre central existente no mesmo local

Editor de Imagem | Oferta de Imagem | Imagem SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [disco pequeno] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter (2012-Datacenter)
MicrosoftWindowsServer | WindowsServer | [disco pequeno] Windows Server 2012 Datacenter (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | [disco pequeno] Windows Server 2012 R2 Datacenter (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter (2016-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter - Núcleo de Servidor (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [disco pequeno] Windows Server 2016 Datacenter - Núcleo de Servidor (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [disco pequeno] Windows Server 2016 Datacenter (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Núcleo de servidor do Datacenter Do Windows Server 2019 com contentores (2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Remote Desktop Session Host 2016 (2016-Datacenter-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (2019-Datacenter)
MicrosoftWindowsServer | WindowsServer | Núcleo de servidor do Datacenter Do Windows Server 2019 (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [disco pequeno] Núcleo de servidor do Datacenter Do Windows Server 2019 (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Núcleo de servidor do Servidor do Windows 2019 com contentores (2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | [disco pequeno] Núcleo de servidor do Datacenter Do Windows Server 2019 com contentores (2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [disco pequeno] Windows Server 2019 Datacenter (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter com Contentores (2019-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | [disco pequeno] Windows Server 2019 Datacenter com Contentores (2019-Datacenter-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-with-Containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-with-Containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Toda a imagem skus
MicrosoftSQLServer | SQL2016SP1-WS2016 | Toda a imagem skus
MicrosoftSQLServer | SQL2016-WS2016 | Toda a imagem skus
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Toda a imagem skus
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Toda a imagem skus
MicrosoftSQLServer | SQL2016-WS2012R2 | Toda a imagem skus
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Toda a imagem skus
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Toda a imagem skus
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Toda a imagem skus
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Toda a imagem skus
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Toda a imagem skus
MicrosoftRServer | MLServer-WS2016 | Toda a imagem skus
MicrosoftVisualStudio | VisualStudio | Toda a imagem skus
MicrosoftVisualStudio | Windows | Toda a imagem skus
MicrosoftDynamicsAX | Dynamics | Pré-Req-AX7-Onebox-U8
microsoft-anúncios | janelas-data-ciência-vm | Toda a imagem skus
MicrosoftWindowsDesktop | Windows-10 | Toda a imagem skus
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | Prioridade SLES-HPC | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | Prioridade sles | 12.X
SUSE | SLES-BYOS | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | Padrão SLES | 12.X
Canónico | UbuntuServer | 14.04.0-LTS
Canónico | UbuntuServer | 14.04.1-LTS
Canónico | UbuntuServer | 14.04.2-LTS
Canónico | UbuntuServer | 14.04.3-LTS
Canónico | UbuntuServer | 14.04.4-LTS
Canónico | UbuntuServer | 14.04.5-DIÁRIO-LTS
Canónico | UbuntuServer | 14.04.5-LTS
Canónico | UbuntuServer | 16.04-DIÁRIO-LTS
Canónico | UbuntuServer | 16.04-LTS
Canónico | UbuntuServer | 16.04.0-LTS
Canónico | UbuntuServer | 18.04-DIÁRIO-LTS
Canónico | UbuntuServer | 18.04-LTS
Oracle | Oráculo-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | CentOS-LVM | 6.X, 7.X
OpenLogic | CentOS-SRIOV | 6.X, 7.X
cloudera | cloudera-centos-os | 7.X
