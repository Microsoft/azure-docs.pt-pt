---
title: Monitorize a saúde dos recursos do Azure CDN. Microsoft Docs
description: Aprenda a monitorizar a saúde dos seus recursos Azure CDN utilizando a Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6710f5e5b873f751ad21068acdc15d38574f8378
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593446"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorizar a saúde dos recursos da Azure CDN
  
A saúde dos recursos Azure CDN é um subconjunto da saúde dos [recursos Azure.](../resource-health/resource-health-overview.md)  Você pode usar a saúde de recursos Azure para monitorizar a saúde dos recursos de CDN e receber orientação acionável para resolver problemas.

>[!IMPORTANT] 
>A saúde dos recursos da CDN Azure apenas atualmente representa a saúde da entrega global de CDN e capacidades de API.  A saúde dos recursos Azure CDN não verifica os pontos finais individuais do CDN.
>
>Os sinais que alimentam a saúde dos recursos azure CDN podem estar atrasados até 15 minutos.

## <a name="how-to-find-azure-cdn-resource-health"></a>Como encontrar a saúde dos recursos do Azure CDN

1. No [portal Azure,](https://portal.azure.com)navegue para o seu perfil De CDN.

2. Clique no botão **Definições.**

    ![Botão Definições](./media/cdn-resource-health/cdn-profile-settings.png)

3. Em *Suporte + resolução de problemas,* clique em **Saúde de Recursos.**

    ![Saúde dos recursos da CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Também pode encontrar recursos CDN listados no azulejo de *saúde* de Recursos na lâmina *de suporte Help +.*  Você pode rapidamente chegar a *Ajuda + suporte* clicando no círculo **?** no canto superior direito do portal.
>
> ![Ajuda + suporte](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Mensagens específicas do CDN azure

Os estados relacionados com a saúde dos recursos DoC Azure podem ser encontrados abaixo.

|Mensagem | Ação Recomendada |
|---|---|
|Pode ter parado, removido ou configurado de forma incorreta um ou mais dos seus pontos finais da CDN | Pode ter parado, removido ou configurado de forma incorreta um ou mais dos seus pontos finais da CDN.|
|Lamentamos, mas o serviço de gestão da CDN está indisponível neste momento | Consulte aqui as atualizações de estado; Se o seu problema persistir após o tempo de resolução esperado, o suporte de contato.|
|Lamentamos, mas os pontos finais da CDN podem estar a ser afetados por problemas em curso de alguns dos nossos fornecedores da CDN | Consulte aqui as atualizações de estado; Utilize a ferramenta Troubleshoot para aprender a testar a sua origem e o ponto final da CDN; Se o seu problema persistir após o tempo de resolução esperado, o suporte de contato. |
|Lamentamos, mas as alterações da configuração do ponto final da CDN estão a ter atrasos de propagação | Consulte aqui as atualizações de estado; Se as alterações de configuração não forem totalmente propagadas no tempo esperado, suporte de contato.|
|Lamentamos, mas estamos a ter problemas ao carregar o portal suplementar | Consulte aqui as atualizações de estado; Se o seu problema persistir após o tempo de resolução esperado, o suporte de contato.|
Lamentamos, estamos a ter problemas com alguns dos nossos fornecedores da CDN | Consulte aqui as atualizações de estado; Se o seu problema persistir após o tempo de resolução esperado, o suporte de contato. |

## <a name="next-steps"></a>Passos seguintes

- [Leia uma visão geral da saúde dos recursos do Azure](../resource-health/resource-health-overview.md)
- [Problemas de resolução de problemas com compressão da CDN](./cdn-troubleshoot-compression.md)
- [Problemas de resolução de problemas com 404 erros](./cdn-troubleshoot-endpoint.md)