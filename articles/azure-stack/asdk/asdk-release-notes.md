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
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 33f1ccf3f1c7bc657cc66efe7c5025356c954ad6
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58187766"
---
# <a name="asdk-release-notes"></a>Notas de versão ASDK

Este artigo fornece informações sobre as alterações, correções e problemas conhecidos no Azure Stack Development Kit (ASDK). Se não tiver a certeza qual é a versão que está a executar, pode [utilizar o portal para verificar](../azure-stack-updates.md#determine-the-current-version).

Mantenha-se atualizado com o que há de novo no ASDK inscrevendo-se para o [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11902069"></a>Compilação 1.1902.0.69

### <a name="new-features"></a>Novos recursos

- A compilação de 1902 introduz uma nova interface de utilizador no portal do administrador do Azure Stack para a criação de planos, ofertas, quotas e planos de suplementos. Para obter mais informações, incluindo capturas de ecrã, consulte [criar planos, ofertas e quotas](../azure-stack-create-plan.md).

- Para obter uma lista de outras alterações e melhorias nesta versão, consulte [esta secção](../azure-stack-update-1902.md#improvements) notas de versão na pilha do Azure.

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Problemas conhecidos

- Foi identificado um problema em que os pacotes mais 1450 bytes para uma carga balanceador interno (ILB) são ignorados. O problema é devido à definição de MTU no anfitrião que está a ser demasiado baixa para acomodar os pacotes VXLAN encapsulado que atravessam a função, que, no momento da elaboração 1901 foi movida para o anfitrião. Existem, pelo menos, dois cenários que podem surgir em que temos visto este problema se manifestar:

  - Consultas SQL para o SQL Always-On que é protegido por uma carga balanceador interno (ILB) e são mais 660 bytes.
  - Implementações de Kubernetes falharem se tentar ativar vários modelos de estrutura mestres.  

  O problema ocorre quando tem a comunicação entre uma VM e um ILB na mesma rede virtual, mas em sub-redes diferentes. Pode contornar este problema ao executar os comandos seguintes numa linha de comandos elevada no anfitrião ASDK:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

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
