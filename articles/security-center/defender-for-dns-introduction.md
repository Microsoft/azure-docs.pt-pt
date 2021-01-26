---
title: Azure Defender para DNS - os benefícios e funcionalidades
description: Conheça os benefícios e funcionalidades do Azure Defender para DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 957e39f7629337182c3e19a1a514c42883666301
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797005"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Introdução ao Azure Defender para DNS

[O Azure DNS](../dns/dns-overview.md) é um serviço de hospedagem para domínios DNS que fornece resolução de nomes utilizando a infraestrutura Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure.

O Azure Defender for DNS fornece uma camada adicional de proteção para os seus recursos em nuvem através de:

- monitorização contínua de todas as consultas de DNS a partir dos seus recursos Azure
- executando análise de segurança avançada para alertá-lo sobre atividade suspeita

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Pré-visualizar<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preços:|**Azure Defender para DNS** é faturado como mostrado [na página de preços](security-center-pricing.md)|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![No](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Quais são os benefícios do Azure Defender para o DNS?

O Azure Defender para o DNS protege contra questões, incluindo:

- Exfiltração de dados dos seus recursos Azure utilizando túneis DNS
- Malware comunicando com o servidor C&C
- Comunicação com domínios maliciosos como phishing e mineração de cripto
- Ataques DNS - comunicação com DNS maliciosos resolver 

Uma lista completa dos alertas fornecidos pelo Azure Defender para o DNS está na página de referência dos [alertas](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Dependências

O Azure Defender para o DNS não usa nenhum agente. 

Para proteger a sua camada de DNS, ative o Azure Defender para DNS para cada uma das suas subscrições, conforme descrito no [Enable Azure Defender](security-center-pricing.md#enable-azure-defender).


## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu sobre o Azure Defender para o DNS. Para material relacionado, consulte o seguinte artigo: 

- Os alertas de segurança podem ser gerados pelo Security Center ou recebidos pelo Security Center a partir de diferentes produtos de segurança. Para exportar todos estes alertas para a Azure Sentinel, qualquer SIEM de terceiros, ou qualquer outra ferramenta externa, siga as instruções em [alertas de exportação para um SIEM](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Ativar o Azure Defender](security-center-pricing.md#enable-azure-defender)