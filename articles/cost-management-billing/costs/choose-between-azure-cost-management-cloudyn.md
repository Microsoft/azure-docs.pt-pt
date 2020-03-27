---
title: Escolher entre o Azure Cost Management e a Cloudyn
description: 'Este artigo ajuda-o a determinar qual é a melhor solução para as suas necessidades de gestão de custos: o Azure Cost Management ou a Cloudyn.'
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 9b17cdfbae7fccae146f01654fb755f23f00563c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80083215"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>Escolher entre o Azure Cost Management e a Cloudyn

A Cloudyn está a ser preterida até ao fim de 2020. As funcionalidades existentes da Cloudyn estão a ser integradas diretamente no portal do Azure sempre que possível. Com exceção dos clientes CSP, não é possível integrar novos clientes neste momento. O suporte do produto existente permanecerá até que seja totalmente preterido.

A Microsoft adquiriu a Cloudyn e está a migrar as suas funcionalidades de gestão de custos do portal da Cloudyn de forma nativa para o Azure. Para utilizar as novas funcionalidades, inicie sessão no portal do Azure e navegue até [Cost Management e Faturação](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) na lista de serviços do Azure. Em comparação com a Cloudyn, a experiência nativa oferece um desempenho melhorado e uma menor latência dos dados de aproximadamente oito horas.

A migração das principais funcionalidades para as categorias de ofertas Contrato Enterprise, Pay As You Go e MSDN para o Azure Cost Management está concluída. As subscrições CSP estão em processo de migração para o Azure Cost Management.

Se tiver uma categoria de oferta que ainda não foi migrada, deverá continuar a utilizar o portal da Cloudyn. Caso contrário, pode utilizar o Azure Cost Management.

## <a name="recommended-services-by-offer"></a>Serviços recomendados por oferta

| Ofertas do Microsoft Azure | Serviço de gestão de custos recomendado |
| --- | --- |
| Contrato Enterprise do Azure | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Contrato de Cliente da Microsoft | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Contrato de Cliente Microsoft suportado por parceiros | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |


## <a name="available-cost-management-features"></a>Funcionalidades do Cost Management disponíveis

Algumas das seguintes funcionalidades estão disponíveis na Cloudyn, mas todas elas estão agora disponíveis no Azure Cost Management.

- APIs
- Recomendações de otimização de custos do Azure, incluindo, mas não se limitando a:
    - Recomendações de encerramento e dimensionamento adequado de instâncias do Azure
    - Recomendações de Reserva do Azure
- Orçamentos
- Análise de custos
- Exportar dados para uma conta de armazenamento do Azure
- Menor latência
- Aplicação de modelo do Power BI
- Suporte de etiquetas de recursos
- Suporte de análise de custos entre clouds para o AWS

## <a name="next-steps"></a>Passos seguintes
- Saber mais sobre o [Azure Cost Management](../cost-management-billing-overview.md).