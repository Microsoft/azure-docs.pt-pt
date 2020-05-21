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
ms.openlocfilehash: d783ce7193faed4110d07988d3f3f05951c80a45
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711402"
---
# <a name="create-configurations-from-existing-servers-for-azure-automation-state-configuration"></a>Criar configurações a partir de servidores existentes para configuração do Estado da Automação Azure

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

## <a name="next-steps"></a>Próximos passos

- [Visão geral de configuração de estado desejada do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Recursos dSC](/powershell/scripting/dsc/resources/resources)
- [Configurar o Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaconfig)
