---
title: Modelos de ligação de sistemas de integrados do Azure Stack | Documentos da Microsoft
description: Determine decisões para vários nós do Azure Stack de planejamento da implantação.
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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: f58d4e4f8b0e095fe64489fb42f78ef82c5af359
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407821"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Modelos de ligação de sistemas de integrados do Azure Stack
Se estiver interessado em adquirir um sistema integrado do Azure Stack, terá de compreender [várias considerações de integração de datacenter](azure-stack-datacenter-integration.md) para implementação do Azure Stack determinar como o sistema se ajustarem ao seu datacenter. Além disso, terá de decidir como irá integrar o Azure Stack para seu ambiente de cloud híbrida. Este artigo fornece uma visão geral dessas decisões principais, incluindo a ligação do Azure, armazenamento de identidade e as decisões de modelo de faturação.

Se optar por comprar um sistema integrado, o seu fornecedor de hardware do fabricante de equipamento original (OEM) ajuda a guia para que grande parte do processo de planejamento mais detalhadamente. Eles também irão executar a implantação real.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Selecione um modelo de ligação de implementação do Azure Stack
Pode optar por implementar o Azure Stack seja ligado à internet (e para o Azure) ou desligado. Para tirar o máximo proveito da pilha do Azure, incluindo cenários de híbrido entre o Azure Stack e o Azure, iria querer implementar ligado ao Azure. Esta opção define quais opções estão disponíveis para o armazenamento de identidade (Azure Active Directory ou serviços de Federação do Active Directory) e o modelo de faturação (pague à medida que com base em utilização com base na capacidade de faturação ou faturação) conforme resumido no diagrama e a tabela seguinte: 

![Implementação de pilha do Azure e cenários de faturação](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> Esse é um ponto de decisão principais! A escolha de serviços de Federação do Active Directory (AD FS) ou Azure Active Directory (Azure AD) é uma decisão única que é necessário efetuar no momento da implementação. Não pode alterar isto mais tarde sem voltar a implementar todo o sistema.  


|Opções|Ligado ao Azure|Desligado do Azure|
|-----|:-----:|:-----:|
|Azure AD|![Suportadas](media/azure-stack-connection-models/check.png)| |
|AD FS|![Suportadas](media/azure-stack-connection-models/check.png)|![Suportadas](media/azure-stack-connection-models/check.png)|
|Faturação baseada no consumo|![Suportadas](media/azure-stack-connection-models/check.png)| |
|Faturação com base na capacidade|![Suportadas](media/azure-stack-connection-models/check.png)|![Suportadas](media/azure-stack-connection-models/check.png)|
|Licenciamento| Contrato Enterprise ou o fornecedor de soluções Cloud | Enterprise Agreement |
|Patch e atualização|Pacote de atualização pode ser baixado diretamente a partir da Internet para o Azure Stack |  Necessário<br><br>Também requer o suporte de dados amovível<br> e um dispositivo ligado separado |
| Registo | Automatizada | Necessário<br><br>Também requer o suporte de dados amovível<br> e um dispositivo ligado separado |

Depois de decidir o modelo de ligação do Azure a ser utilizado para a implementação do Azure Stack, decisões adicionais e dependentes da ligação devem ser feitas para o armazenamento de identidade e o método de faturação. 

## <a name="next-steps"></a>Passos Seguintes

[Decisões de implementação do Azure Stack ligadas do Azure](azure-stack-connected-deployment.md)

[Azure desligado decisões de implementação do Azure Stack](azure-stack-disconnected-deployment.md)
