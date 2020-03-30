---
title: Configuração baseada no STIG para usar na configuração do estado - Automação Azure
description: Conheça as configurações baseadas no STIG para configuração estatal na Automação Azure.
keywords: dsc,powershell,configuração,configuração,configuração
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028269"
---
# <a name="configuration-based-on-stig"></a>Configuração baseada em STIG

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
Em seguida, registe os seus servidores a partir do [local](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [em Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) para puxar configurações.

Para experimentar o PowerSTIG, visite a [PowerShell Gallery](https://www.powershellgallery.com) e descarregue a solução ou clique em "Site do Projeto" para ver a [documentação](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral de configuração de estado desejada do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Recursos dSC](/powershell/scripting/dsc/resources/resources)
- [Configurar o Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaconfig)
