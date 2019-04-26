---
title: Monitorizar o estado de funcionamento de recursos do CDN do Azure | Documentos da Microsoft
description: Saiba como monitorizar o estado de funcionamento dos seus recursos do CDN do Azure com o Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: ad4bf7ae97a08f89b9d82e1d4e025a5bd5d47fc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324717"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorizar o estado de funcionamento de recursos do CDN do Azure
  
Estado de funcionamento de recursos do CDN do Azure é um subconjunto das [estado de funcionamento de recursos do Azure](../resource-health/resource-health-overview.md).  Pode usar o estado de funcionamento de recursos do Azure para monitorizar o estado de funcionamento de recursos CDN e receber orientação acionável para resolver problemas.

>[!IMPORTANT] 
>Estado de funcionamento de recursos do CDN do Azure atualmente apenas contas para o estado de funcionamento de entrega CDN global e capacidades de API.  Estado de funcionamento de recursos do CDN do Azure não verifica os pontos finais CDN individuais.
>
>Os sinais de feed de estado de funcionamento de recursos de CDN do Azure podem ser até 15 minutos atrasados.

## <a name="how-to-find-azure-cdn-resource-health"></a>Como localizar o estado de funcionamento de recursos de CDN do Azure

1. Na [portal do Azure](https://portal.azure.com), navegue para o perfil de CDN.

2. Clique nas **definições** botão.

    ![Botão Definições](./media/cdn-resource-health/cdn-profile-settings.png)

3. Sob *suporte + resolução de problemas*, clique em **estado de funcionamento do recurso**.

    ![Estado de funcionamento de recursos CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Também pode encontrar recursos CDN listados na *estado de funcionamento do recurso* mosaico no *ajuda + suporte* painel.  Pode obter rapidamente *ajuda + suporte* ao clicar o dentro de um círculo **?** no canto superior direito do portal.
>
> ![Ajuda + suporte](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Mensagens de específicas do CDN do Azure

Estados relacionados com o estado de funcionamento de recursos de CDN do Azure podem ser encontrados abaixo.

|Mensagem | Ação Recomendada |
|---|---|
|Pode ter parado, removido ou configurado de forma incorreta um ou mais dos seus pontos finais da CDN | Pode ter parado, removido ou configurado de forma incorreta um ou mais dos seus pontos finais da CDN.|
|Lamentamos, mas o serviço de gestão da CDN está indisponível neste momento | Verifique aqui as atualizações de estado Se o problema persistir após o período de resolução esperado, contacte o suporte.|
|Lamentamos, mas os pontos finais da CDN podem estar a ser afetados por problemas em curso de alguns dos nossos fornecedores da CDN | Verifique aqui as atualizações de estado Utilize a ferramenta de resolução de problemas para saber como testar a origem e o ponto final de CDN Se o problema persistir após o período de resolução esperado, contacte o suporte. |
|Lamentamos, mas as alterações da configuração do ponto final da CDN estão a ter atrasos de propagação | Verifique aqui as atualizações de estado Se as alterações de configuração não estão propagadas totalmente na hora prevista, contacte o suporte.|
|Lamentamos, mas estamos a ter problemas ao carregar o portal suplementar | Verifique aqui as atualizações de estado Se o problema persistir após o período de resolução esperado, contacte o suporte.|
Lamentamos, estamos a ter problemas com alguns dos nossos fornecedores da CDN | Verifique aqui as atualizações de estado Se o problema persistir após o período de resolução esperado, contacte o suporte. |

## <a name="next-steps"></a>Passos Seguintes

- [Leia uma visão geral do Estado de funcionamento de recursos do Azure](../resource-health/resource-health-overview.md)
- [Resolver problemas com a compactação de CDN](./cdn-troubleshoot-compression.md)
- [Resolver problemas com 404 erros](./cdn-troubleshoot-endpoint.md)