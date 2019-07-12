---
title: Migrar um perfil de CDN do Azure da Verizon Standard para Premium da Verizon | Documentos da Microsoft
description: Saiba mais sobre os detalhes da migração de um perfil do Standard da Verizon para Verizon Premium.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: c11155223220c075c28769a19875691783cce3bd
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593633"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrar um perfil de CDN do Azure da Standard Verizon para Premium da Verizon

Quando cria um perfil de rede de entrega conteúdo (CDN) para gerir os pontos finais, a CDN do Azure oferece quatro produtos diferentes para a sua escolha. Para obter informações sobre os diferentes produtos e as respetivas funcionalidades disponíveis, consulte [funcionalidades do produto da CDN do Azure de comparar](cdn-features.md).

Se criar um **CDN do Azure Standard da Verizon** criar perfis e usá-lo para gerir os pontos finais CDN, tem a opção de atualizá-lo para um **CDN do Azure Premium da Verizon** perfil. Quando atualizar, os pontos finais CDN e todos os seus dados serão preservados. 

> [!IMPORTANT]
> Assim que tiver atualizado para uma **CDN do Azure Premium da Verizon** perfil, não pode mais tarde convertê-lo novamente para um **CDN do Azure Standard da Verizon** perfil.
> 

Para atualizar uma **CDN do Azure Standard da Verizon** perfil de, contacto [Support da Microsoft](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Comparação de perfil
**O Azure CDN Premium da Verizon** os perfis têm as seguintes diferenças principais do **CDN do Azure Standard da Verizon** perfis:
- Para determinados da CDN do Azure funcionalidades como [compressão](cdn-improve-performance.md), [regras de colocação em cache](cdn-caching-rules.md), e [filtragem geográfica](cdn-restrict-access-by-country.md), não é possível utilizar a interface de CDN do Azure, tem de utilizar o portal da Verizon através do **Gerir** botão.
- API: Ao contrário com Standard da Verizon, é possível utilizar a API para controlar as funcionalidades que são acessadas a partir do portal do Premium da Verizon. No entanto, pode usar a API para controlar a outros recursos comuns, como criar/eliminar um ponto de extremidade, remover/carregamento ativos em cache e ativação/desativação de um domínio personalizado.
- Preços: Premium da Verizon tem uma estrutura de preços diferente para transferências de dados que Standard da Verizon. Para obter mais informações, consulte [preços de rede de entrega de conteúdos](https://azure.microsoft.com/pricing/details/cdn/).

**O Azure CDN Premium da Verizon** perfis têm as seguintes funcionalidades adicionais:
- [Autenticação de token](cdn-token-auth.md): Permite aos utilizadores obter e utilizar um token para obter recursos seguros.
- [Motor de regras](cdn-rules-engine.md): Permite-lhe personalizar a forma como os pedidos de HTTP são processados.
- Ferramentas de análise avançadas:
   - [Análise detalhada de HTTP](cdn-advanced-http-reports.md)
   - [Análise de desempenho do Microsoft Edge](cdn-edge-performance.md)
   - [Análise em tempo real](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o mecanismo de regras, consulte [referência do motor de regras de CDN do Azure](cdn-rules-engine-reference.md).

