---
title: Arquitetura de Kit de desenvolvimento do Azure Stack | Documentos da Microsoft
description: Descreve a arquitetura do Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 9157acc7517aea56f087a3dbff0fe7114f8b4c87
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55958810"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Arquitetura do Development Kit do Microsoft Azure Stack
O Azure Stack Development Kit (ASDK) é uma implementação de nó único do Azure Stack em execução num só computador host. Compontents de encaminhamento de borda são instalados no computador anfitrião para fornecer capacidades de VPN e de NAT para o Azure Stack. Executam funções de infraestrutura do Azure Stack na camada de Hyper-V do computador anfitrião físico.


## <a name="virtual-machine-roles"></a>Funções de máquina virtual
O ASDK oferece serviços usando as seguintes VMs alojadas no computador de anfitrião do kit de desenvolvimento:

| Name | Descrição |
| ----- | ----- |
| **AzS-ACS01** | Serviços de armazenamento do Azure Stack.|
| **AzS-ADFS01** | Serviços de Federação do Active Directory (ADFS).  |
| **AzS-CA01** | Serviços de autoridade de certificados para serviços de função do Azure Stack.|
| **AzS-DC01** | Active Directory, DNS e DHCP dos serviços para o Microsoft Azure Stack.|
| **AzS-ERCS01** | Consola de recuperação de emergência VM. |
| **AzS-GWY01** | Serviços do Edge gateway tais como ligações de site-site VPN para redes de inquilino.|
| **AzS-NC01** | Controlador de rede, que gere os serviços de rede do Azure Stack.  |
| **AzS-SLB01** | O balanceamento de carga Multiplexador serviços no Azure Stack para os inquilinos e serviços de infraestrutura do Azure Stack.  |
| **AzS-SQL01** | Arquivo de dados internos para funções de infraestrutura do Azure Stack.  |
| **AzS-WAS01** | Portal de administrativo de pilha do Azure e serviços do Azure Resource Manager.|
| **AzS-WASP01**| Portal de utilizador (inquilino) do Azure Stack e serviços do Azure Resource Manager.|
| **AzS-XRP01** | Controlador de gestão de infraestrutura para o Microsoft Azure Stack, incluindo os fornecedores de recursos de computação, rede e armazenamento.|


## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre tarefas de administração de ASDK básicas](asdk-admin-basics.md)
