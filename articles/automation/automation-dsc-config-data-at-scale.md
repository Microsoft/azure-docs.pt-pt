---
title: Dados de configuração em escala – automação do Azure
description: Saiba como configurar dados em escala para configuração de estado na automação do Azure.
keywords: DSC, PowerShell, configuração, instalação
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1a77a366ee8e06b2d8c47eb3b47eeaf9ae809598
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028298"
---
# <a name="configuration-data-at-scale"></a>Dados de configuração à escala

> Aplica-se a: Windows PowerShell 5,1

O gerenciamento de centenas ou milhares de servidores pode ser um desafio.
Os clientes forneceram comentários de que o aspecto mais difícil é realmente gerenciar [dados de configuração](/powershell/scripting/dsc/configurations/configdata).
Organizar informações em constructos lógicos, como localização, tipo e ambiente.

> [!NOTE]
> Este artigo refere-se a uma solução mantida pela comunidade de código-fonte aberto.
> O suporte está disponível apenas na forma de colaboração do GitHub, não da Microsoft.

## <a name="community-project-datum"></a>Projeto da Comunidade: Datum

Uma solução mantida pela comunidade chamada [Datum](https://github.com/gaelcolas/Datum) foi criada para resolver esse desafio.
A Datum se baseia em incríveis ideias de outras plataformas de gerenciamento de configuração e implementa o mesmo tipo de solução para o DSC do PowerShell.
As informações são [organizadas em arquivos de texto](https://github.com/gaelcolas/Datum#3-intended-usage) com base em ideias lógicas.
Os exemplos seriam:

- Configurações que devem ser aplicadas globalmente
- Configurações que devem ser aplicadas a todos os servidores em um local
- Configurações que devem ser aplicadas a todos os servidores de banco de dados
- Configurações de servidor individual

Essas informações são organizadas no formato de arquivo que você preferir (JSON, YAML ou PSD1).
Em seguida, os cmdlets são fornecidos para gerar arquivos de dados de configuração, [consolidando as informações](https://github.com/gaelcolas/Datum#datum-tree) de cada arquivo no modo de exibição único de uma função de servidor ou servidor.

Depois que os arquivos de dados tiverem sido gerados, você poderá usá-los com [scripts de configuração DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) para gerar arquivos MOF e [carregar os arquivos MOF para a automação do Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registre os servidores no [local](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [no Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) para efetuar pull das configurações.

Para experimentar a Datum, visite a [Galeria do PowerShell](https://www.powershellgallery.com/packages/datum/) e baixe a solução ou clique em "site do projeto" para exibir a [documentação](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da configuração de estado desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Recursos de DSC](/powershell/scripting/dsc/resources/resources)
- [Configurando o Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaconfig)
