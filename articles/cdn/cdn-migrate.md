---
title: Migrar o perfil cdn do Azure de Verizon Standard para Verizon Premium
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
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: c1302822715a37ef1b85007130067ac02a8fd17a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84887583"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrar um perfil Azure CDN de Standard Verizon para Premium Verizon

Quando cria um perfil da Rede de Entrega de Conteúdos Azure (CDN) para gerir os seus pontos finais, a Azure CDN oferece quatro produtos diferentes para escolher. Para obter informações sobre os diferentes produtos e as suas funcionalidades disponíveis, consulte [as funcionalidades do produto Compare Azure CDN](cdn-features.md).

Se criou um **Azure CDN Standard a partir do** perfil Verizon e está a usá-lo para gerir os seus pontos finais cdN, tem a opção de atualizá-lo para um **Azure CDN Premium a partir do** perfil Verizon. Ao atualizar, os seus pontos finais CDN e todos os seus dados serão preservados. 

> [!IMPORTANT]
> Uma vez atualizado para um **Azure CDN Premium do** perfil Verizon, não pode mais tarde convertê-lo de volta para um **Azure CDN Standard do** perfil Verizon.
> 

Para atualizar um **Azure CDN Standard a partir do** perfil verizon, contacte o Microsoft [Support](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Comparação de perfis
**O Azure CDN Premium dos** perfis Verizon tem as seguintes diferenças-chave da **Azure CDN Standard dos** perfis Verizon:
- Para certas funcionalidades do Azure CDN, tais como [compressão,](cdn-improve-performance.md) [regras de caching](cdn-caching-rules.md)e [geo filtragem,](cdn-restrict-access-by-country.md)não é possível utilizar a interface Azure CDN, tem de utilizar o portal Verizon através do botão **Gerir.**
- API: Ao contrário do Standard Verizon, não é possível utilizar a API para controlar as funcionalidades que são acedidas a partir do portal Premium Verizon. No entanto, pode utilizar a API para controlar outras funcionalidades comuns, tais como criar/eliminar um ponto final, purgar/carregar ativos em cache e permitir/desativar um domínio personalizado.
- Preços: Premium Verizon tem uma estrutura de preços diferente para transferências de dados do que a Standard Verizon. Para obter mais informações, consulte [os preços da Rede de Entrega de Conteúdos.](https://azure.microsoft.com/pricing/details/cdn/)

**O Azure CDN Premium dos** perfis Verizon tem as seguintes funcionalidades adicionais:
- [Autenticação simbólica](cdn-token-auth.md): Permite que os utilizadores obtenham e utilizem um símbolo para obter recursos seguros.
- [Regras do motor](cdn-rules-engine.md): Permite-lhe personalizar a forma como os pedidos HTTP são tratados.
- Ferramentas de análise avançadas:
   - [Análise detalhada http](cdn-advanced-http-reports.md)
   - [Análise de desempenho de borda](cdn-edge-performance.md)
   - [Análise em tempo real](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o motor de regras, consulte [a referência do motor das regras da Azure CDN](cdn-rules-engine-reference.md).

