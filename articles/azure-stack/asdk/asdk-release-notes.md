---
title: Notas de versão do Kit de desenvolvimento do Microsoft Azure Stack | Documentos da Microsoft
description: Melhorias, correções e problemas conhecidos ao Kit de desenvolvimento do Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: bb9e5ba960251f728e14106ab1c586e1d3ef373f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538651"
---
# <a name="asdk-release-notes"></a>Notas de versão ASDK

Este artigo fornece informações sobre as alterações, correções e problemas conhecidos no Azure Stack Development Kit (ASDK). Se não tiver a certeza qual é a versão que está a executar, pode [utilizar o portal para verificar](../azure-stack-updates.md#determine-the-current-version).

Mantenha-se atualizado com o que há de novo no ASDK inscrevendo-se para o [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11902069"></a>Compilação 1.1902.0.69

### <a name="changes"></a>Alterações

- A compilação de 1902 introduz uma nova interface de utilizador no portal do administrador do Azure Stack para a criação de planos, ofertas, quotas e planos de suplementos. Para obter mais informações, incluindo capturas de ecrã, consulte [criar planos, ofertas e quotas](../azure-stack-create-plan.md).

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

## <a name="build-11901095"></a>Compilação 1.1901.0.95

Consulte a [informações de compilação importante nas notas de versão do Azure Stack](../azure-stack-update-1901.md#build-reference).

### <a name="changes"></a>Alterações

Esta compilação inclui os seguintes aprimoramentos para o Azure Stack:

- Componentes BGP e NAT são agora implementados no anfitrião físico. Isto elimina a necessidade de ter dois endereços de IP públicos ou empresariais para implementar o ASDK e também simplifica a implantação.
- Sistemas de cópias de segurança podem agora de integrados do Azure Stack [ser validado](asdk-validate-backup.md) utilizando o **asdk installer.ps1** script do PowerShell.

### <a name="new-features"></a>Novos recursos

- Para obter uma lista de novas funcionalidades nesta versão, consulte [esta secção](../azure-stack-update-1901.md#new-features) notas de versão da pilha do Azure.

### <a name="fixed-and-known-issues"></a>Problemas conhecidos e fixos

- Para obter uma lista dos problemas corrigidos nesta versão, consulte [esta secção](../azure-stack-update-1901.md#fixed-issues) notas de versão da pilha do Azure. Para obter uma lista dos problemas conhecidos, consulte [esta secção](../azure-stack-update-1901.md#known-issues-post-installation).
- Tenha em atenção que [correções disponíveis do Azure Stack](../azure-stack-update-1901.md#azure-stack-hotfixes) não se aplicam ao ASDK de pilha do Azure.

## <a name="build-118110101"></a>Compilação 1.1811.0.101

### <a name="changes"></a>Alterações

Esta compilação inclui os seguintes aprimoramentos para o Azure Stack:  

- Existe um conjunto de novo hardware mínimo e recomendado e requisitos de software para o ASDK. Estes novos recomendado especificações estão documentados em [considerações de planeamento de implementação do Azure Stack](asdk-deploy-considerations.md). Como a plataforma do Azure Stack evoluiu, mais serviços estão agora disponíveis e mais recursos podem ser necessários. As especificações de maior refletem estas recomendações revisadas.

### <a name="new-features"></a>Novos recursos

Para obter uma lista de novas funcionalidades nesta versão, consulte [esta secção](../azure-stack-update-1811.md#new-features) notas de versão da pilha do Azure.

### <a name="fixed-and-known-issues"></a>Problemas conhecidos e fixos

Para obter uma lista dos problemas corrigidos nesta versão, consulte [esta secção](../azure-stack-update-1811.md#fixed-issues) notas de versão da pilha do Azure. Para obter uma lista dos problemas conhecidos, consulte [esta secção](../azure-stack-update-1811.md#known-issues-post-installation).
