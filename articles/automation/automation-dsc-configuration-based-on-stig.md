---
title: Configurar dados baseados no STIG para a Azure Automation State Configuration
description: Este artigo diz como configurar dados baseados no STIG para a Azure Automation State Configuration.
keywords: dsc,powershell,configuração,configuração
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d1b05f9e77d3530f3e883aa3f9d98de09c8f54c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83836996"
---
# <a name="configure-data-based-on-stig"></a>Configure dados com base no STIG

> Aplica-se a: Windows PowerShell 5.1

Criar conteúdo de configuração pela primeira vez pode ser um desafio.
Em muitos casos, o objetivo é automatizar a configuração dos servidores seguindo uma "linha de base" que, esperemos, se alinha a uma recomendação da indústria.

> [!NOTE]
> Este artigo refere-se a uma solução que é mantida pela comunidade Open Source.
> O suporte só está disponível sob a forma de colaboração gitHub, não da Microsoft.

## <a name="community-project-powerstig"></a>Projeto comunitário: PowerSTIG

Um projeto comunitário chamado [PowerSTIG](https://github.com/microsoft/powerstig) visa resolver este problema gerando conteúdo DSC com base em [informações públicas fornecidas](https://public.cyber.mil/stigs/) sobre o STIG (Guia técnico de implementação de segurança),

Lidar com linhas de base é mais complicado do que parece.
Muitas organizações precisam de [documentar exceções](https://github.com/microsoft/powerstig#powerstigdata) às regras e gerir esses dados em escala.
O PowerSTIG aborda o problema fornecendo [Recursos Compostos](https://github.com/microsoft/powerstig#powerstigdsc) para abordar cada área da configuração em vez de tentar resolver toda a gama de definições num ficheiro grande.

Uma vez geradas as configurações, pode utilizar os [scripts de configuração DSC](/powershell/scripting/dsc/configurations/configurations) para gerar ficheiros MOF e [fazer o upload dos ficheiros MOF para a Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registe os seus servidores a partir [de instalações no local](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou em [Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) para puxar configurações.

Para experimentar o PowerSTIG, visite a [PowerShell Gallery](https://www.powershellgallery.com) e descarregue a solução ou clique em "Project Site" para ver a [documentação.](https://github.com/microsoft/powerstig)

## <a name="next-steps"></a>Próximos passos

- Para compreender o PowerShell DSC, consulte a visão geral da [configuração do estado desejada do Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Descubra os recursos da PowerShell DSC em [Recursos DSC.](/powershell/scripting/dsc/resources/resources)
- Para obter mais detalhes sobre a configuração do Gestor de Configuração Local, consulte [configurar o Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaconfig).
