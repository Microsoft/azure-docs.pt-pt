---
title: Converter configurações para recursos compósitos para configuração do Estado de Automação Azure
description: Este artigo diz como converter configurações em recursos compósitos para a Configuração do Estado da Automação Azure.
keywords: dsc,powershell,configuração,configuração
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5833308f03d328d988ac818d83ff5ee618a4c79c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563577"
---
# <a name="convert-configurations-to-composite-resources"></a>Converter configurações em recursos compostos

> Aplica-se a: Windows PowerShell 5.1

Assim que começar a autorizar configurações, pode rapidamente criar "cenários" que gerem grupos de definições.
Exemplos seriam:

- criar um servidor web
- criar um servidor DNS
- criar um servidor SharePoint
- configurar um cluster SQL
- gerir definições de firewall
- gerir definições de palavras-passe

Se estiver interessado em partilhar este trabalho com outros, a melhor opção é embalar a configuração como [Um Recurso Composto.](/powershell/scripting/dsc/resources/authoringresourcecomposite)
Criar recursos compósitos pela primeira vez pode ser esmagador.

> [!NOTE]
> Este artigo refere-se a uma solução que é mantida pela comunidade Open Source.
> O suporte só está disponível sob a forma de colaboração gitHub, não da Microsoft.

## <a name="community-project-compositeresource"></a>Projeto comunitário: CompositeResource

Uma solução de manutenção da comunidade chamada [CompositeResource](https://github.com/microsoft/compositeresource) foi criada para resolver este desafio.

A CompositeResource automatiza o processo de criação de um novo módulo a partir da sua configuração.
Começa por [dotar](https://devblogs.microsoft.com/scripting/how-to-reuse-windows-powershell-functions-in-scripts/) o script de configuração na sua estação de trabalho (ou construir servidor) para que seja carregado na memória.
Em seguida, em vez de executar a configuração para gerar um ficheiro MOF, utilize a função fornecida pelo módulo CompositeResource para automatizar uma conversão.
O cmdlet carregará o conteúdo da sua configuração, obterá a lista de parâmetros e gerará um novo módulo com tudo o que precisa.

Uma vez gerado um módulo, pode incrementar a versão e adicionar notas de lançamento sempre que fizer alterações e publicá-la no seu próprio [repositório PowerShellGet](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Uma vez criado um módulo de recursos compostos que contenha a sua configuração (ou múltiplas configurações), pode utilizá-los na [Experiência de Autorização Composível](./compose-configurationwithcompositeresources.md) em Azure, ou adicioná-los a [scripts de configuração DSC](/powershell/scripting/dsc/configurations/configurations) para gerar ficheiros MOF e [enviar os ficheiros MOF para a Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registe os seus servidores a partir [de instalações no local](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) ou em [Azure](./automation-dsc-onboarding.md#enable-azure-vms) para puxar configurações.
A mais recente atualização do projeto também publicou [livros para](https://www.powershellgallery.com/packages?q=DscGallerySamples) a Azure Automation para automatizar o processo de importação de configurações da Galeria PowerShell.

Para experimentar automatizar a criação de recursos compósitos para a DSC, visite a [Galeria PowerShell](https://www.powershellgallery.com/packages/compositeresource/) e descarregue a solução ou clique em "Project Site" para ver a [documentação.](https://github.com/microsoft/compositeresource)

## <a name="next-steps"></a>Passos seguintes

- Para compreender o PowerShell DSC, consulte a visão geral da [configuração do estado desejada do Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Descubra os recursos da PowerShell DSC em [Recursos DSC.](/powershell/scripting/dsc/resources/resources)
- Para obter mais detalhes sobre a configuração do Gestor de Configuração Local, consulte [configurar o Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaconfig).
