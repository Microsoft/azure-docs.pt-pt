---
title: Criar configurações a partir de servidores existentes para configuração do Estado da Automação Azure
description: Este artigo diz como criar configurações a partir de servidores existentes para configuração do Estado de Automação Azure.
keywords: dsc,powershell,configuração,configuração,configuração
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 775fae09c4d618551327669362cd28a0ae2cc801
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837013"
---
# <a name="create-configurations-from-existing-servers"></a>Criar configurações a partir de servidores existentes

> Aplica-se a: Windows PowerShell 5.1

Criar configurações a partir de servidores existentes pode ser uma tarefa desafiante.
Podes não querer *todas as* definições, só aquelas com as quais te preocupas.
Mesmo assim, precisa de saber em que ordem as definições devem ser aplicadas para que a configuração se aplique com sucesso.

> [!NOTE]
> Este artigo refere-se a uma solução que é mantida pela comunidade Open Source.
> O suporte só está disponível sob a forma de colaboração gitHub, não da Microsoft.

## <a name="community-project-reversedsc"></a>Projeto comunitário: ReverseDSC

Uma solução de manutenção comunitária chamada [ReverseDSC](https://github.com/microsoft/reversedsc) foi criada para trabalhar nesta área começando o SharePoint.

A solução baseia-se no [recurso SharePointDSC](https://github.com/powershell/sharepointdsc) e estende-o para orquestrar a recolha de [informações](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) dos servidores sharePoint existentes.
A versão mais recente tem [vários modos](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) de extração para determinar que nível de informação incluir.

O resultado da utilização da solução é gerar dados de [configuração](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) a serem utilizados com scripts de configuração SharePointDSC.

Uma vez gerados os ficheiros de dados, pode utilizá-los com [scripts de Configuração DSC](/powershell/scripting/dsc/overview/overview) para gerar ficheiros MOF e [carregar os ficheiros MOF para a Automação Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registe os seus servidores a partir do [local](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [em Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) para puxar configurações.

Para experimentar o ReverseDSC, visite a [Galeria PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) e descarregue a solução ou clique em "Site do Projeto" para ver a [documentação](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Passos seguintes

- Para compreender o PowerShell DSC, consulte a [visão geral da Configuração do Estado do Windows PowerShell.](/powershell/scripting/dsc/overview/overview)
- Saiba mais sobre os recursos da PowerShell DSC nos [Recursos DSC](/powershell/scripting/dsc/resources/resources).
- Para mais detalhes sobre a configuração do Gestor de Configuração Local, consulte [configurar o Gestor](/powershell/scripting/dsc/managing-nodes/metaconfig)de Configuração Local .