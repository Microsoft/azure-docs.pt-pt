---
title: Converter configurações em recursos compósitos para configuração do Estado - Automação Azure
description: Aprenda a converter configurações em recursos compósitos para configuração do Estado na Automação Azure.
keywords: dsc,powershell,configuração,configuração,configuração
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a39b038d31d1b4a614ff0acf7df2586706bb0404
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585517"
---
# <a name="convert-configurations-to-composite-resources"></a>Converter configurações em recursos compostos

> Aplica-se a: Windows PowerShell 5.1

Assim que começar a autoria de configurações, pode criar rapidamente "cenários" que gerem grupos de configurações.
Exemplos seriam:

- criar um servidor web
- criar um servidor DNS
- criar um servidor SharePoint
- configurar um cluster SQL
- gerir definições de firewall
- gerir as definições de palavra-passe

Se está interessado em partilhar este trabalho com outros, a melhor opção é embalar a configuração como [Recurso Composto.](/powershell/scripting/dsc/resources/authoringresourcecomposite)
Criar recursos compósitos pela primeira vez pode ser esmagador.

> [!NOTE]
> Este artigo refere-se a uma solução que é mantida pela comunidade Open Source.
> O suporte só está disponível sob a forma de colaboração gitHub, não da Microsoft.

## <a name="community-project-compositeresource"></a>Projeto comunitário: CompositeResource

Foi criada uma solução comunitária chamada [CompositeResource](https://github.com/microsoft/compositeresource) para resolver este desafio.

A CompositeResource automatiza o processo de criação de um novo módulo a partir da sua configuração.
Comece por [fornecer](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) o script de configuração na sua estação de trabalho (ou construir servidor) para que seja carregado na memória.
Em seguida, em vez de executar a configuração para gerar um ficheiro MOF, utilize a função fornecida pelo módulo CompositeResource para automatizar uma conversão.
O cmdlet carregará o conteúdo da sua configuração, obterá a lista de parâmetros e gerará um novo módulo com tudo o que precisa.

Depois de ter gerado um módulo, pode incrementar a versão e adicionar notas de lançamento sempre que fizer alterações e publicá-la no seu próprio [repositório PowerShellGet](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Depois de criar um módulo de recurso composto que contenha a sua configuração (ou múltiplas configurações), pode utilizá-los na Experiência de [Autor Composável](/azure/automation/compose-configurationwithcompositeresources) em Azure, ou adicioná-los a [scripts de Configuração DSC](/powershell/scripting/dsc/configurations/configurations) para gerar ficheiros MOF e [carregar os ficheiros MOF para a Automação Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registe os seus servidores a partir do [local](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [em Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) para puxar configurações.
A mais recente atualização do projeto também publicou livros de [execução](https://www.powershellgallery.com/packages?q=DscGallerySamples) para a Azure Automation para automatizar o processo de importação de configurações da PowerShell Gallery.

Para experimentar a automatização da criação de recursos compósitos para a DSC, visite a [PowerShell Gallery](https://www.powershellgallery.com/packages/compositeresource/) e descarregue a solução ou clique em "Site do Projeto" para ver a [documentação](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral de configuração de estado desejada do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Recursos dSC](/powershell/scripting/dsc/resources/resources)
- [Configurar o Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaconfig)
