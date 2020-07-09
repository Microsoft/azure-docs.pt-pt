---
title: Azure Peering Service FAQ
description: Saiba mais sobre as FAQs do Serviço de Peering microsoft Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 55c5e6c5b718dc2de295b9b4418ddc8607a69f8f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84872051"
---
# <a name="peering-service-faq"></a>Serviço de Observação FAQ

Este artigo explica as perguntas mais frequentes sobre as ligações do Serviço de Pares Azure.


**Q. Quem são os clientes-alvo?**

R. Os clientes-alvo são empresas que se conectam à nuvem da Microsoft usando a internet como transporte.

**Q. Os clientes podem inscrever-se no Serviço de Peering com vários fornecedores?** 

R. Sim, os clientes podem inscrever-se no Serviço de Peering com vários fornecedores na mesma região ou em diferentes regiões, mas não para o mesmo prefixo.

**Q. Os clientes podem selecionar um ISP único para os seus locais por região geográfica?**

R. Sim, os clientes podem fazê-lo. Selecione o parceiro ISP por região que se adapte às suas necessidades empresariais e operacionais.

**Q. O que é um Microsoft Edge PoP?**

R. É um local físico onde a Microsoft se interliga com outras redes. Na localização Microsoft Edge PoP, estão hospedados serviços como Azure Front Door e Azure CDN. Para mais informações, consulte [a Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-features).

## <a name="peering-service-unique-characteristics"></a>Serviço de Observação: Características únicas

**Q. Como é que o Serviço de Observação é diferente do acesso normal à Internet?**

R. Os parceiros que se registaram no Microsoft Peering Service estão a trabalhar com a Microsoft para oferecer um encaminhamento otimizado e conectividade fiável aos serviços da Microsoft.  

**Q. Como é que o Serviço de Observação é diferente do ExpressRoute?**

R. Azure ExpressRoute é uma ligação privada e dedicada a partir de um ou vários locais de clientes. Embora o Peering Service ofereça conectividade pública otimizada e não suporte qualquer conectividade privada, também oferece conectividade otimizada para fugas de internet locais.

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre o Serviço de Peering, consulte [a visão geral do Serviço de Peering](about.md).
- Para encontrar um prestador de serviços, consulte [parceiros e locais do Serviço de Observação.](location-partners.md)
- Para embarcar numa ligação de Serviço de Peering, consulte [o Serviço de Observação de Bordo](onboarding-model.md).
- Para registar uma ligação de Serviço de Peering, consulte [registar uma ligação de Serviço de Pares - portal Azure](azure-portal.md).
- Para medir a telemetria, consulte [a telemetria de ligação medida](measure-connection-telemetry.md).