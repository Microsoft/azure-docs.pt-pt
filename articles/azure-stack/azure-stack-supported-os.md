---
title: Sistemas operativos convidados suportados para o Azure Stack | Documentos da Microsoft
description: Estes sistemas de operativos de convidado pode ser utilizados no Azure Stack.
services: azure-stack
documentationcenter: ''
author: Brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: d6e9531edf8915e4b6c2636372b756002fca10fd
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382404"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Sistemas de operativos convidados suportados no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

## <a name="windows"></a>Windows

O Azure Stack suporta os sistemas de operativos de convidado do Windows listados na tabela a seguir:

| Sistema operativo | Descrição | Disponível no Marketplace |
| --- | --- | --- | --- | --- | --- |
| Versão 1709 do Windows Server | 64 bits | Núcleo de contentores |
| Windows Server 2016 | 64 bits |  Centro de dados, núcleo de Datacenter, Datacenter com contentores |
| Windows Server 2012 R2 | 64 bits |  Datacenter |
| Windows Server 2012 | 64 bits |  Datacenter |
| Windows Server 2008 R2 SP1 | 64 bits |  Datacenter |
| Windows Server 2008 SP2 | 64 bits |  Utilize a sua própria imagem |
| Windows 10 *(ver nota 1)* | 64 bits, Pro e Enterprise | Utilize a sua própria imagem |

***Nota 1:*** *para implementar sistemas operativos de cliente de Windows 10 no Azure Stack, tem de ter [Windows por licenciamento de utilizadores](https://www.microsoft.com/en-us/Licensing/product-licensing/windows10.aspx) ou comprar através de um fornecedor de alojamento qualificado do multi-inquilino ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).*

Imagens do Marketplace estão disponíveis para pagamento-como-utiliza ou licenciamento BYOL (EA/SPLA). Não é suportada a utilização de ambos numa única instância do Azure Stack. Durante a implementação, o Azure Stack injeta uma versão adequada do agente convidado na imagem.

 Edições de Datacenter estão disponíveis no mercado para download; os clientes podem trazer as suas próprias imagens de servidor, incluindo outras edições. Imagens do cliente Windows não estão disponíveis no Marketplace.

## <a name="linux"></a>Linux

Distribuições do Linux listadas como disponível no Marketplace incluem o agente Linux de Azure de Windows necessárias (WALA). Se trouxer a sua própria imagem para o Azure Stack, siga as diretrizes [imagens do Linux de adicionar ao Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Imagens personalizadas devem ser criadas com a versão mais recente de WALA pública. As versões mais antigas que 2.2.18 podem não funcionar corretamente no Azure Stack.
>
> [cloud-init](https://cloud-init.io/) , neste momento, não é suportado no Azure Stack.

| Distribuição | Descrição | Publicador | Marketplace |
| --- | --- | --- | --- | --- | --- |
| 6.9 baseada em centOS | 64 bits | Rogue Wave | Sim |
| Com base no centOS 7.4 | 64 bits | Rogue Wave | Sim |
| ClearLinux | 64 bits | ClearLinux.org | Sim |
| Contentor Linux |  64 bits | CoreOS | Estável |
| Debian 8 "Jessie" | 64 bits | credativ |  Sim |
| Debian 9 "Stretch" | 64 bits | credativ | Sim |
| Red Hat Enterprise Linux 7.x | 64 bits | Red Hat |Utilize a sua própria imagem |
| SLES 11SP4 | 64 bits | SUSE | Sim |
| SLES 12SP3 | 64 bits | SUSE | Sim |
| Ubuntu 14.04-LTS | 64 bits | Canónico | Sim |
| Ubuntu 16.04-LTS | 64 bits | Canónico | Sim |
| Ubuntu 18.04-LTS | 64 bits | Canónico | Sim |

Outras distribuições do Linux podem ser suportadas no futuro.

Para obter informações de suporte do Red Hat Enterprise Linux, consulte [Red Hat e o Azure Stack: Perguntas mais frequentes](https://access.redhat.com/articles/3413531).
