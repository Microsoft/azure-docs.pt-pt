---
title: Configure dados baseados no STIG para configuração do Estado da Automação Azure
description: Este artigo diz como configurar dados baseados no STIG para a Configuração do Estado da Automação Azure.
keywords: dsc,powershell,configuração,configuração,configuração
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d1b05f9e77d3530f3e883aa3f9d98de09c8f54c2
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836996"
---
# <a name="configure-data-based-on-stig"></a>Configure dados com base no STIG

> Aplica-se a: Windows PowerShell 5.1

Criar conteúdo de configuração pela primeira vez pode ser um desafio.
Em muitos casos, o objetivo é automatizar a configuração dos servidores seguindo uma "linha de base" que, esperemos, se alinha com uma recomendação da indústria.

> [!NOTE]
> Este artigo refere-se a uma solução que é mantida pela comunidade Open Source.
> O suporte só está disponível sob a forma de colaboração gitHub, não da Microsoft.

## <a name="community-project-powerstig"></a>Projeto comunitário: PowerSTIG

Um projeto comunitário chamado [PowerSTIG](https://github.com/microsoft/powerstig) visa resolver esta questão gerando conteúdo dSC com base em [informações públicas](https://public.cyber.mil/stigs/) fornecidas sobre o STIG (Security Technical Implementation Guide),

Lidar com linhas de base é mais complicado do que parece.
Muitas organizações precisam de [documentar exceções](https://github.com/microsoft/powerstig#powerstigdata) às regras e gerir esses dados em escala.
O PowerSTIG aborda o problema fornecendo [Recursos Compósitos](https://github.com/microsoft/powerstig#powerstigdsc) para abordar cada área da configuração em vez de tentar abordar toda a gama de configurações num ficheiro grande.

Uma vez geradas as configurações, pode utilizar os [scripts de Configuração DSC](/powershell/scripting/dsc/configurations/configurations) para gerar ficheiros MOF e [carregar os ficheiros MOF para a Automação Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registe os seus servidores a partir do [local](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [em Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) para puxar configurações.

Para experimentar o PowerSTIG, visite a [PowerShell Gallery](https://www.powershellgallery.com) e descarregue a solução ou clique em "Site do Projeto" para ver a [documentação](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Passos seguintes

- Para compreender o PowerShell DSC, consulte a [visão geral da Configuração do Estado do Windows PowerShell.](/powershell/scripting/dsc/overview/overview)
- Saiba mais sobre os recursos da PowerShell DSC nos [Recursos DSC](/powershell/scripting/dsc/resources/resources).
- Para mais detalhes sobre a configuração do Gestor de Configuração Local, consulte [configurar o Gestor](/powershell/scripting/dsc/managing-nodes/metaconfig)de Configuração Local .
