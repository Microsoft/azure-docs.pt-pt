---
title: Monitorize a saúde dos recursos da Azure CDN| Microsoft Docs
description: Saiba como monitorizar a saúde dos seus recursos Azure CDN utilizando a Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 81c92b6ef5bdf89391364bc238f55200eb3679f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778168"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorizar o estado de funcionamento dos recursos da CDN do Azure
  
A azure CDN Resource health é um subconjunto de saúde de [recursos Azure](../service-health/resource-health-overview.md).  Pode utilizar a saúde dos recursos Azure para monitorizar a saúde dos recursos da CDN e receber orientações acccionáveis para resolver problemas.

>[!IMPORTANT] 
>A saúde dos recursos Azure CDN apenas atualmente é responsável pela saúde da entrega global de CDN e das capacidades de API.  A saúde dos recursos Azure CDN não verifica pontos finais individuais do CDN.
>
>Os sinais que alimentam a saúde dos recursos Azure CDN podem estar até 15 minutos atrasados.

## <a name="how-to-find-azure-cdn-resource-health"></a>Como encontrar a saúde dos recursos da Azure CDN

1. No [portal Azure,](https://portal.azure.com)navegue pelo seu perfil CDN.

2. Clique no botão **Definições.**

    ![Botão Definições](./media/cdn-resource-health/cdn-profile-settings.png)

3. No *Suporte + resolução de problemas,* clique na **saúde do recurso.**

    ![Saúde dos recursos CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Também pode encontrar recursos CDN listados no azulejo de *saúde recursos* na lâmina *de suporte Help +.*  Pode rapidamente obter o *suporte Help +* clicando no **círculo?** no canto superior direito do portal.
>
> ![Ajuda + suporte](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Mensagens específicas do Azure CDN

Os estados relacionados com a saúde dos recursos Azure CDN podem ser encontrados abaixo.

|Mensagem | Ação Recomendada |
|---|---|
|Pode ter parado, removido ou configurado de forma incorreta um ou mais dos seus pontos finais da CDN | Pode ter parado, removido ou configurado de forma incorreta um ou mais dos seus pontos finais da CDN.|
|Lamentamos, mas o serviço de gestão da CDN está indisponível neste momento | Consulte aqui as atualizações de estado; Se o seu problema persistir após o tempo de resolução esperado, contacte o suporte.|
|Lamentamos, mas os pontos finais da CDN podem estar a ser afetados por problemas em curso de alguns dos nossos fornecedores da CDN | Consulte aqui as atualizações de estado; Utilize a ferramenta Troubleshoot para aprender a testar a sua origem e o ponto final da CDN; Se o seu problema persistir após o tempo de resolução esperado, contacte o suporte. |
|Lamentamos, mas as alterações da configuração do ponto final da CDN estão a ter atrasos de propagação | Consulte aqui as atualizações de estado; Se as alterações de configuração não forem totalmente propagadas no tempo esperado, contacte o suporte.|
|Lamentamos, mas estamos a ter problemas ao carregar o portal suplementar | Consulte aqui as atualizações de estado; Se o seu problema persistir após o tempo de resolução esperado, contacte o suporte.|
Lamentamos, estamos a ter problemas com alguns dos nossos fornecedores da CDN | Consulte aqui as atualizações de estado; Se o seu problema persistir após o tempo de resolução esperado, contacte o suporte. |

## <a name="next-steps"></a>Passos seguintes

- [Leia uma visão geral da saúde dos recursos Azure](../service-health/resource-health-overview.md)
- [Problemas de resolução de problemas com compressão CDN](./cdn-troubleshoot-compression.md)
- [Problemas de resolução de problemas com 404 erros](./cdn-troubleshoot-endpoint.md)