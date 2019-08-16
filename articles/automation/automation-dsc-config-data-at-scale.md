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
ms.openlocfilehash: f6bb89370c85389d6c41306ed224d27d710cd7c8
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559407"
---
# <a name="configuration-data-at-scale"></a>Dados de configuração em escala

> Aplica-se a: Windows PowerShell 5.1

O gerenciamento de centenas ou milhares de servidores pode ser um desafio.
Os clientes forneceram comentários de que o aspecto mais difícil é realmente gerenciar [dados de configuração](/powershell/dsc/configurations/configdata).
Organizar informações em constructos lógicos, como localização, tipo e ambiente.

> [!NOTE]
> Este artigo refere-se a uma solução mantida pela comunidade de código-fonte aberto.
> O suporte está disponível apenas na forma de colaboração do GitHub, não da Microsoft.

## <a name="community-project-datum"></a>Projeto da Comunidade: Referência

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

Depois que os arquivos de dados tiverem sido gerados, você poderá usá-los com [scripts de configuração DSC](/powershell/dsc/configurations/write-compile-apply-configuration) para gerar arquivos MOF e [carregar os arquivos MOF para a automação do Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registre os servidores no [local](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) ou [no Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) para efetuar pull das configurações.

Para experimentar a Datum, visite a [Galeria do PowerShell](https://www.powershellgallery.com/packages/datum/) e baixe a solução ou clique em "site do projeto" para exibir a [documentação](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Passos Seguintes

- [Visão geral da configuração de estado desejado do Windows PowerShell](/powershell/dsc/overview/overview)
- [Recursos de DSC](/powershell/dsc/resources/resources)
- [Configurando o Configuration Manager local](/powershell/dsc/managing-nodes/metaconfig)
