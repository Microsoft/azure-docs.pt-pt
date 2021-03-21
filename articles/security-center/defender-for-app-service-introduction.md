---
title: Azure Defender for App Service - os benefícios e funcionalidades
description: Conheça as capacidades do Azure Defender para o Serviço de Aplicações e como capacitá-lo na sua subscrição
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ec4ac5d355266a46b33d89fd25c2665493773f5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100819"
---
# <a name="protect-your-web-apps-and-apis"></a>Proteja as suas aplicações web e APIs

## <a name="prerequisites"></a>Pré-requisitos

O Security Center está nativamente integrado com o Serviço de Aplicações, eliminando a necessidade de implantação e embarque - a integração é transparente.

Para proteger o seu plano de Serviço de Aplicações Azure com o Azure Defender para o Serviço de Aplicações, necessitará de:

- Um plano de Serviço de Aplicações suportado associado a máquinas dedicadas. Os planos apoiados estão listados na [Disponibilidade.](#availability)

- Azure Defender ativou na sua subscrição como descrito no [Quickstart: Enable Azure Defender](enable-azure-defender.md).

    > [!TIP]
    > Pode opcionalmente ativar planos individuais no Azure Defender (como o Azure Defender for App Service).

## <a name="availability"></a>Disponibilidade

| Aspeto                       | Detalhes                                                                                                                                                                                        |
|------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Estado de libertação:               | Disponibilidade Geral (GA)                                                                                                                                                                      |
| Preços:                     | [Azure Defender for App Service](azure-defender.md) é faturado como mostrado nos preços do Security [Center](https://azure.microsoft.com/pricing/details/security-center/)<br>A faturação é de acordo com o total de instâncias computacional em todos os planos       |
| Planos de Serviço de Aplicações suportados: | [Todos os planos do Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/plans/) são suportados, exceto [as Funções Azure no plano de consumo.](../azure-functions/functions-scale.md) |
| Nuvens:                      | ![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![No](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)                                                     |
|                              |                                                                                                                                                                                                |

## <a name="what-are-the-benefits-of-azure-defender-for-app-service"></a>Quais são os benefícios do Azure Defender para o Serviço de Aplicações?

O Azure App Service é uma plataforma totalmente gerida para construir e hospedar as suas aplicações web e APIs. Uma vez que a plataforma está totalmente gerida, não tens de te preocupar com a infraestrutura. Fornece informações de gestão, monitorização e operacionais para atender aos requisitos de desempenho, segurança e conformidade de nível empresarial. Para mais informações, consulte [o Azure App Service](https://azure.microsoft.com/services/app-service/).

**O Azure Defender for App Service** utiliza a escala da nuvem para identificar ataques direcionados a aplicações que passam por cima do Serviço de Aplicações. Os atacantes sondam aplicações web para encontrar e explorar fraquezas. Antes de serem encaminhados para ambientes específicos, os pedidos para aplicações em execução em Azure passam por vários gateways, onde são inspecionados e registados. Estes dados são então usados para identificar explorações e atacantes, e para aprender novos padrões que serão usados mais tarde.

Quando ativa o Azure Defender para o Serviço de Aplicações, beneficia imediatamente dos seguintes serviços oferecidos por este plano Azure Defender:

- **Secure** - Security Center avalia os recursos abrangidos pelo seu plano de Serviço de Aplicações e gera recomendações de segurança com base nas suas conclusões. Utilize as instruções detalhadas nestas recomendações para endurecer os seus recursos de Serviço de Aplicação.

- **Detect** - Azure Defender deteta uma multiplicidade de ameaças aos seus recursos do Serviço de Aplicações através da monitorização:
    - a instância VM em que o seu Serviço de Aplicações está em execução, e a sua interface de gestão
    - os pedidos e respostas enviados para e a partir das suas aplicações de Serviço de Aplicações
    - as caixas de areia subjacentes e vMs
    - Registos internos do Serviço de Aplicações - disponíveis graças à visibilidade que o Azure tem como fornecedor de nuvem

Como solução nativa da nuvem, o Azure Defender pode identificar metodologias de ataque aplicáveis a vários alvos. Por exemplo, a partir de um único hospedeiro seria difícil identificar um ataque distribuído a partir de um pequeno subconjunto de IPs, rastejando para pontos finais semelhantes em vários anfitriões.

Os dados de registo e a infraestrutura em conjunto podem contar a história: de um novo ataque que circula na natureza até aos compromissos nas máquinas de clientes. Portanto, mesmo que o Security Center seja implementado depois de uma aplicação web ter sido explorada, pode ser capaz de detetar ataques em curso.


## <a name="what-threats-can-azure-defender-for-app-service-detect"></a>Que ameaças pode o Azure Defender para o Serviço de Aplicações detetar?

### <a name="threats-by-mitre-attck-tactics"></a>Ameaças por táticas MITRE ATT&CK

O Azure Defender monitoriza muitas ameaças aos seus recursos do Serviço de Aplicações. Os alertas cobrem quase a lista completa de táticas MITRE ATT&CK do pré-ataque ao comando e controlo. O Azure Defender pode detetar:

- **Ameaças pré-ataque -** O Defender pode detetar a execução de vários tipos de scanners de vulnerabilidade que os atacantes usam frequentemente para sondar aplicações para fraquezas.

- **Ameaças de acesso iniciais**  -  [O Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) alimenta estes alertas que incluem desencadear um alerta quando um conhecido endereço IP malicioso se conecta à interface FTP do Serviço de Aplicações Azure.

- **Ameaças de execução** - O Defender pode detetar tentativas de executar comandos de alto privilégio, comandos Linux num Serviço de Aplicações do Windows, comportamento de ataque sem ficheiros, ferramentas de mineração de moeda digital e muitas outras atividades suspeitas e maliciosas de execução de código.

### <a name="dangling-dns-detection"></a>Deteção de DNS pendente

O Azure Defender for App Service também identifica quaisquer entradas dns restantes no seu registo DNS quando um website do Serviço de Aplicações é desativado - estes são conhecidos como entradas de DNS pendentes. Quando remove um website e não remove o seu domínio personalizado do seu registo DNS, a entrada de DNS está a apontar para um recurso inexistente e o seu subdomínio está vulnerável a uma aquisição. O Azure Defender não digitaliza o seu registo DNS para as entradas de DNS *pendentes;* alerta-o quando um website do Serviço de Aplicações é desativado e o seu domínio personalizado (entrada DNS) não é eliminado.

As aquisições de subdomínios são uma ameaça comum e de alta gravidade para as organizações. Quando um ator ameaça deteta uma entrada de DNS pendente, eles criam o seu próprio site no endereço de destino. O tráfego destinado ao domínio da organização é então direcionado para o site do ator ameaça, e eles podem usar esse tráfego para uma ampla gama de atividades maliciosas.

A proteção DENGLING DNS está disponível se os seus domínios são geridos com Azure DNS ou um registrador de domínio externo e se aplica ao Serviço de Aplicações tanto no Windows como no Linux.

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="Um exemplo de um alerta do Azure Defender sobre uma entrada de DNS descoberta. Permita ao Azure Defender para o Serviço de Aplicações receber este e outros alertas para o seu ambiente." lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

Saiba mais sobre o DNS pendente e a ameaça de aquisição de subdomínio, em [Prevenir entradas de DNS pendentes e evitar a aquisição de subdomínios](../security/fundamentals/subdomain-takeover.md).

Para obter uma lista completa dos alertas do Serviço de Aplicações Azure, consulte a [tabela de alertas de referência.](alerts-reference.md#alerts-azureappserv)

> [!NOTE]
> O Defender pode não desencadear alertas dns pendentes se o seu domínio personalizado não apontar diretamente para um recurso do Serviço de Aplicações, ou se o Defender não tiver monitorizado o tráfego para o seu website desde que a proteção de DNS pendente foi ativada (porque não haverá registos para ajudar a identificar o domínio personalizado).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre Azure Defender para o Serviço de Aplicações. 

Para obter material relacionado, consulte os seguintes artigos: 

- Para exportar os seus alertas para a Azure Sentinel, qualquer SIEM de terceiros, ou qualquer outra ferramenta externa, siga as instruções em [alertas stream para uma solução siem, soar ou it service Management](export-to-siem.md).
- Para obter uma lista dos alertas do Azure Defender para o Serviço de Aplicações, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azureappserv).
- Para obter mais informações sobre os planos do Serviço de Aplicações, consulte [os planos do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/plans/)
> [!div class="nextstepaction"]
> [Ativar o Azure Defender](enable-azure-defender.md)