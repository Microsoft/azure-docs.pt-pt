---
title: Configurar dados à escala para configuração do Estado da Automação Azure
description: Este artigo diz como configurar dados em escala para a Configuração do Estado da Automação Azure.
keywords: dsc,powershell,configuração,configuração
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fbd721bc54ce2de6b7923b0f582c53ffc7da1a73
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83837030"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>Configurar dados à escala para configuração do Estado da Automação Azure

> Aplica-se a: Windows PowerShell 5.1

Gerir centenas ou milhares de servidores pode ser um desafio.
Os clientes forneceram feedback de que o aspeto mais difícil é, na verdade, gerir [dados de configuração.](/powershell/scripting/dsc/configurations/configdata)
Organizar informações através de construções lógicas como localização, tipo e ambiente.

> [!NOTE]
> Este artigo refere-se a uma solução que é mantida pela comunidade Open Source.
> O suporte só está disponível sob a forma de colaboração gitHub, não da Microsoft.

## <a name="community-project-datum"></a>Projeto comunitário: Datum

Foi criada uma solução comunitária chamada [Datum](https://github.com/gaelcolas/Datum) para resolver este desafio.
A Datum baseia-se em grandes ideias de outras plataformas de gestão de configuração e implementa o mesmo tipo de solução para o PowerShell DSC.
A informação é [organizada em ficheiros de texto](https://github.com/gaelcolas/Datum#3-intended-usage) com base em ideias lógicas.
Exemplos seriam:

- Configurações que devem ser aplicadas globalmente
- Definições que devem ser aplicadas a todos os servidores num local
- Definições que devem ser aplicadas a todos os servidores de base de dados
- Definições individuais do servidor

Esta informação é organizada no formato de ficheiro que prefere (JSON, Yaml ou PSD1).
Em seguida, os cmdlets são fornecidos para gerar ficheiros de dados de [configuração, consolidando a informação](https://github.com/gaelcolas/Datum#datum-tree) de cada ficheiro para uma única visão de um servidor ou função de servidor.

Uma vez gerados os ficheiros de dados, pode usá-los com [scripts de configuração DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) para gerar ficheiros MOF e [fazer o upload dos ficheiros MOF para a Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registe os seus servidores a partir [de instalações no local](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou em [Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) para puxar configurações.

Para experimentar datum, visite a [Galeria PowerShell](https://www.powershellgallery.com/packages/datum/) e descarregue a solução ou clique em "Project Site" para ver a [documentação.](https://github.com/gaelcolas/Datum#2-getting-started--concepts)

## <a name="next-steps"></a>Próximos passos

- Para compreender o PowerShell DSC, consulte a visão geral da [configuração do estado desejada do Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Descubra os recursos da PowerShell DSC em [Recursos DSC.](/powershell/scripting/dsc/resources/resources)
- Para obter mais detalhes sobre a configuração do Gestor de Configuração Local, consulte [configurar o Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaconfig).
