---
title: Dados de configuração à escala - Automação Azure
description: Saiba como configurar dados à escala para configuração do Estado na Automação Azure.
keywords: dsc,powershell,configuração,configuração,configuração
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 644ea1c00af7e71ff56852298fff18e5293c137b
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585562"
---
# <a name="configuration-data-at-scale"></a>Dados de configuração à escala

> Aplica-se a: Windows PowerShell 5.1

Gerir centenas ou milhares de servidores pode ser um desafio.
Os clientes forneceram feedback de que o aspeto mais difícil é, na verdade, gerir dados de [configuração.](/powershell/scripting/dsc/configurations/configdata)
Organizar informação através de construções lógicas como localização, tipo e ambiente.

> [!NOTE]
> Este artigo refere-se a uma solução que é mantida pela comunidade Open Source.
> O suporte só está disponível sob a forma de colaboração gitHub, não da Microsoft.

## <a name="community-project-datum"></a>Projeto comunitário: Datum

Uma solução comunitária chamada [Datum](https://github.com/gaelcolas/Datum) foi criada para resolver este desafio.
A Datum baseia-se em grandes ideias de outras plataformas de gestão de configuração e implementa o mesmo tipo de solução para o PowerShell DSC.
A informação é [organizada em ficheiros](https://github.com/gaelcolas/Datum#3-intended-usage) de texto baseados em ideias lógicas.
Exemplos seriam:

- Definições que devem aplicar-se globalmente
- Definições que devem aplicar-se a todos os servidores num local
- Definições que devem aplicar-se a todos os servidores de base de dados
- Definições individuais do servidor

Esta informação é organizada no formato de ficheiro que prefere (JSON, Yaml ou PSD1).
Em seguida, são fornecidos cmdlets para gerar ficheiros de dados de [configuração, consolidando a informação](https://github.com/gaelcolas/Datum#datum-tree) de cada ficheiro para uma única visão de uma função de servidor ou servidor.

Uma vez gerados os ficheiros de dados, pode utilizá-los com [scripts de Configuração DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) para gerar ficheiros MOF e [carregar os ficheiros MOF para a Automação Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registe os seus servidores a partir do [local](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [em Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) para puxar configurações.

Para experimentar o Datum, visite a [PowerShell Gallery](https://www.powershellgallery.com/packages/datum/) e descarregue a solução ou clique em "Site do Projeto" para ver a [documentação](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral de configuração de estado desejada do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Recursos dSC](/powershell/scripting/dsc/resources/resources)
- [Configurar o Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaconfig)
