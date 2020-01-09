---
title: Monitorizar a identidade e o acesso no Centro de Segurança do Azure | Microsoft Docs
description: Saiba como utilizar a capacidade de identidade e acesso no Centro de Segurança do Azure para monitorizar a atividade de acesso dos seus utilizadores e os problemas relacionados com identidades.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552989"
---
# <a name="monitor-identity-and-access-preview"></a>Monitorar identidade e acesso (visualização)
Quando a central de segurança identifica possíveis vulnerabilidades de segurança, ele cria recomendações que orientam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

Este artigo explica a página de **identidade e acesso** da seção segurança de recursos da central de segurança do Azure.

Para obter uma lista completa das recomendações que podem ser exibidas nesta página, consulte [recomendações de identidade e acesso](recommendations-reference.md#recs-identity).

> [!NOTE]
> O monitoramento de identidade e acesso está em visualização e disponível somente na camada Standard da central de segurança. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança.
>

A identidade deve ser o plano de controle da sua empresa, e a proteção de identidades deve ser sua prioridade máxima. O perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança se torna menos informando a defesa da sua rede e mais sobre como proteger seus dados, bem como gerenciar a segurança de seus aplicativos e usuários. Hoje em dia, com mais dados e mais aplicações a mudarem para a cloud, a identidade torna-se no novo perímetro.

Através da monitorização das atividades de identidade, poderá levar a cabo ações proativas antes da ocorrência de um incidente ou ações reativas para parar uma tentativa de ataque. O painel identidade & Access fornece recomendações como:

- Ativar o MFA para contas com privilégios na sua subscrição
- Remover contas externas com permissões de escrita da sua subscrição
- Remover as contas externas com privilégios da sua subscrição

> [!NOTE]
> Se sua assinatura tiver mais de 600 contas, a central de segurança não poderá executar as recomendações de identidade em relação à sua assinatura. As recomendações que não são executadas são listadas em "avaliações não disponíveis" abaixo.
A central de segurança não pode executar as recomendações de identidade em relação a agentes de administração do provedor de soluções de nuvem (CSP).
>

## <a name="monitor-identity-and-access"></a>Monitorizar a identidade e o acesso

Abra a lista de problemas identificados de identidade e acesso selecionando **identidade & acesso** na barra lateral da central de segurança (em **recursos**) ou na página Visão geral. 

Em **identidade & acesso**, há duas guias:

- **Visão geral**: recomendações identificadas pela central de segurança.
- **Assinaturas**: lista de suas assinaturas e o estado de segurança atual de cada uma.

[Acesso ao ![& identidade](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>Secção de descrição geral
Em **visão geral**, há uma lista de recomendações. A primeira coluna indica a recomendação. A segunda coluna mostra o número total de assinaturas afetadas por essa recomendação. A terceira coluna mostra a gravidade do problema.

1. Selecione uma recomendação. A janela recomendações é aberta e exibe:

   - Descrição da recomendação
   - Lista de assinaturas não íntegras e íntegras
   - Lista de recursos que não foram verificados devido a uma avaliação com falha ou que o recurso está em uma assinatura em execução na camada gratuita e não é avaliado

    [![janela de recomendações](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. Selecione uma assinatura na lista para obter detalhes adicionais.

### <a name="subscriptions-section"></a>Seção assinaturas
Em **assinaturas**, há uma lista de assinaturas. A primeira coluna lista as assinaturas. A segunda coluna mostra o número total de recomendações para cada assinatura. A terceira coluna mostra as severidades dos problemas.

[guia assinaturas do ![](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. Selecionar uma subscrição. É aberta uma vista de resumida com três separadores:

   - **Recomendações**: com base nas avaliações realizadas pelo centro de segurança que falharam.
   - **Passados avaliações**: lista de avaliações realizadas pelo centro de segurança passado.
   - **Avaliações indisponíveis**: lista de avaliações que não foram executadas devido a um erro ou porque a assinatura tem mais de 600 contas.

   Em **recomendações** é uma lista das recomendações para a assinatura e a severidade selecionadas de cada recomendação.

   [![recomendações para a assinatura Select](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. Selecione uma recomendação para obter uma descrição da recomendação, uma lista de assinaturas não íntegras e íntegras e uma lista de recursos não verificados.

   [![descrição da recomendação](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   Sob **transmitido avaliações** é uma lista de avaliações aprovadas.  Gravidade dessas avaliações é sempre verde.

   [![avaliações aprovadas](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. Selecione uma avaliação aprovada na lista para obter uma descrição da avaliação e uma lista de assinaturas íntegras. Há uma guia para assinaturas não íntegras que lista todas as assinaturas que falharam.

   [![avaliações aprovadas](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> Se você criou uma política de acesso condicional que exige MFA, mas tem exclusões definidas, a avaliação de recomendação do MFA da central de segurança considera a política como não compatível, pois permite que alguns usuários entrem no Azure sem MFA.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte os seguintes artigos:

- [Proteger as máquinas e aplicações no Centro de Segurança do Azure](security-center-virtual-machine-protection.md)
- [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
- [Protegendo o serviço e os dados do SQL Azure na central de segurança do Azure](security-center-sql-service-recommendations.md)