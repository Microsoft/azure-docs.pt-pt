---
title: Perfil de CDN Migrate Azure de Verizon Standard para Verizon Premium
description: Conheça os detalhes da migração de um perfil de Verizon Standard para Verizon Premium.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 61c472281c64fba451bb9b05f69ebee09bc763fd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260464"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrar um perfil De CDN Azure da Standard Verizon para Premium Verizon

Ao criar um perfil da Rede de Entrega de Conteúdos Azure (CDN) para gerir os seus pontos finais, o Azure CDN oferece quatro produtos diferentes para escolher. Para obter informações sobre os diferentes produtos e as suas funcionalidades disponíveis, consulte as [funcionalidades do produto Compare Azure CDN](cdn-features.md).

Se criou um **Padrão CDN Azure a partir do** perfil verizon e está a usá-lo para gerir os seus pontos finais cdN, tem a opção de atualizá-lo para um **Azure CDN Premium a partir do** perfil verizon. Quando atualizar, os seus pontos finais de CDN e todos os seus dados serão preservados. 

> [!IMPORTANT]
> Uma vez atualizado para um **Azure CDN Premium a partir do** perfil verizon, não pode mais tarde convertê-lo de volta para um **Azure CDN Standard a partir do** perfil verizon.
> 

Para atualizar um **Padrão CDN Azure a partir do** perfil verizon, contacte o Microsoft [Support](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Comparação de perfil
**O Azure CDN Premium dos** perfis da Verizon tem as seguintes diferenças-chave da **Norma Azure CDN dos** perfis verizon:
- Para certas funcionalidades de CDN Azure, tais como [compressão,](cdn-improve-performance.md) [regras de cache,](cdn-caching-rules.md)e [filtragem de geo-filtragem,](cdn-restrict-access-by-country.md)não pode utilizar a interface Azure CDN, deve utilizar o portal Verizon através do botão **Gerir.**
- API: Ao contrário da Standard Verizon, não é possível utilizar a API para controlar as funcionalidades que são acedidas a partir do portal Premium Verizon. No entanto, pode utilizar a API para controlar outras funcionalidades comuns, tais como criar/apagar um ponto final, purgar/carregar ativos em cache e permitir/desativar um domínio personalizado.
- Preços: Premium Verizon tem uma estrutura de preços diferente para transferências de dados do que a Standard Verizon. Para mais informações, consulte [os preços da Rede](https://azure.microsoft.com/pricing/details/cdn/)de Entrega de Conteúdos .

**O Azure CDN Premium dos** perfis da Verizon tem as seguintes funcionalidades adicionais:
- [Autenticação simbólica](cdn-token-auth.md): Permite que os utilizadores obtenham e utilizem um símbolo para obter recursos seguros.
- [Regras motor](cdn-rules-engine.md): Permite-lhe personalizar a forma como os pedidos http são tratados.
- Ferramentas de análise avançadas:
   - [Análise detalhada do HTTP](cdn-advanced-http-reports.md)
   - [Análise de desempenho de borda](cdn-edge-performance.md)
   - [Análise em tempo real](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o motor de regras, consulte [a referência do motor de regras do Azure CDN](cdn-rules-engine-reference.md).

