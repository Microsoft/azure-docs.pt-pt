---
title: Migrar o perfil da CDN do Azure da Verizon Standard para Verizon Premium
description: Saiba mais sobre os detalhes da migração de um perfil de Verizon Standard para Verizon Premium.
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
ms.openlocfilehash: 8ab66117be4f05550b00defafc883108646be283
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083063"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrar um perfil da CDN do Azure da Verizon padrão para a Premium Verizon

Quando você cria um perfil de CDN (rede de distribuição de conteúdo) do Azure para gerenciar seus pontos de extremidade, a CDN do Azure oferece quatro produtos diferentes para sua escolha. Para obter informações sobre os diferentes produtos e seus recursos disponíveis, consulte [comparar recursos do produto CDN do Azure](cdn-features.md).

Se você tiver criado um perfil **padrão da CDN do Azure da Verizon** e usá-lo para gerenciar seus pontos de extremidade CDN, terá a opção de atualizá-lo para um perfil **da CDN Premium do Azure da Verizon** . Quando você atualizar, seus pontos de extremidade CDN e todos os seus dados serão preservados. 

> [!IMPORTANT]
> Depois de ter atualizado para um perfil **da CDN Premium do Azure da Verizon** , você não poderá convertê-lo mais tarde para um perfil **padrão da CDN do Azure da Verizon** .
> 

Para atualizar um perfil **da CDN padrão do Azure da Verizon** , entre em contato com [suporte da Microsoft](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Comparação de perfis
Os perfis **da CDN Premium do Azure da Verizon** têm as seguintes diferenças principais dos perfis **da CDN standard do Azure da Verizon** :
- Para determinados recursos da CDN do Azure, como [compactação](cdn-improve-performance.md), [regras de cache](cdn-caching-rules.md)e [filtragem geográfica](cdn-restrict-access-by-country.md), você não pode usar a interface da CDN do Azure, você deve usar o portal Verizon por meio do botão **gerenciar** .
- API: ao contrário da Verizon padrão, você não pode usar a API para controlar os recursos que são acessados do portal Premium da Verizon. No entanto, você pode usar a API para controlar outros recursos comuns, como criar/excluir um ponto de extremidade, limpar/carregar ativos em cache e habilitar/desabilitar um domínio personalizado.
- Preço: a Premium Verizon tem uma estrutura de preços diferente para transferências de dados do que a Verizon Standard. Para obter mais informações, consulte [preços de rede de distribuição de conteúdo](https://azure.microsoft.com/pricing/details/cdn/).

Os perfis **da CDN Premium do Azure da Verizon** têm os seguintes recursos adicionais:
- [Autenticação de token](cdn-token-auth.md): permite que os usuários obtenham e usem um token para buscar recursos seguros.
- [Mecanismo de regras](cdn-rules-engine.md): permite que você personalize como as solicitações HTTP são manipuladas.
- Ferramentas de análise avançadas:
   - [Análise de HTTP detalhada](cdn-advanced-http-reports.md)
   - [Análise de desempenho do Microsoft Edge](cdn-edge-performance.md)
   - [Análise em tempo real](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o mecanismo de regras, consulte [referência do mecanismo de regras da CDN do Azure](cdn-rules-engine-reference.md).

