---
title: Proteja as suas aplicações web e APIs do Azure App Service
description: Este artigo ajuda-o a começar a proteger as suas aplicações web e APIs do Azure App Service no Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 2d81e1a1218add504e1e35015276b6924da0e3e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77616471"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Proteja as suas aplicações web e APIs do Azure App Service

O Azure App Service é uma plataforma totalmente gerida para construir e hospedar as suas aplicações web e APIs sem se preocupar em ter de gerir a infraestrutura. Fornece informações de gestão, monitorização e operacionais para atender aos requisitos de desempenho, segurança e conformidade de nível empresarial. Para mais informações, consulte [o Azure App Service](https://azure.microsoft.com/services/app-service/).

Para permitir uma proteção avançada de ameaças para o seu plano de Serviço de Aplicações Azure, deve:

* Subscreva o nível de preços standard do Azure Security Center
* Ativar o plano de Serviço de Aplicações como mostrado abaixo. O Security Center está nativamente integrado com o Serviço de Aplicações, eliminando a necessidade de implantação e embarque - a integração é transparente.
* Tenha um plano de Serviço de Aplicações que esteja associado a máquinas dedicadas. Os planos suportados são: Básico, Standard, Premium, Isolado ou Linux. O Centro de Segurança não suporta os planos gratuitos, partilhados ou de consumo. Para mais informações, consulte [os Planos de Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/plans/)

Com o plano de Serviço de Aplicações ativado, o Security Center avalia os recursos abrangidos pelo seu plano de Serviço de Aplicações e gera recomendações de segurança com base nas suas conclusões. O Security Center protege a instância VM em que o seu Serviço de Aplicações está a funcionar e a interface de gestão. Também monitoriza pedidos e respostas enviadas de e para as suas apps em execução no Serviço de Aplicações.

O Security Center aproveita a escala da nuvem e a visibilidade que o Azure tem como fornecedor de nuvem, para monitorizar os ataques comuns de aplicações web. O Security Center pode descobrir ataques às suas aplicações e identificar ataques emergentes - mesmo quando os atacantes estão na fase de reconhecimento, procurando identificar vulnerabilidades através de várias aplicações hospedadas pelo Azure. Como um serviço nativo do Azure, o Security Center também está numa posição única para oferecer análises de segurança baseadas no anfitrião que cobrem os nós de computação subjacentes a este PaaS, permitindo ao Centro de Segurança detetar ataques contra aplicações web que já foram exploradas. Para mais detalhes, consulte [a proteção contra ameaças para o Serviço de Aplicações Azure.](threat-protection.md#app-services)


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Habilitar a monitorização e proteção do Serviço de Aplicações

1. No portal Azure, escolha Centro de Segurança.
2. Vá a **definições de preços &** e escolha uma subscrição.
3. No **nível de preços,** na linha de serviço da **App,** altere o seu plano para **Ativar**.

    [![Ativar serviços de aplicações na subscrição standard tier](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> O número de casos listados para a sua **Quantidade de Recursos** representa o número total de instâncias de cálculo, em todos os planos do Serviço de Aplicações nesta subscrição, em execução no momento em que abriu a lâmina de nível de preços.
>
> O Azure App Service oferece uma variedade de planos. O seu plano de Serviço de Aplicações define o conjunto de recursos computacionares para uma aplicação web a executar. Estes são equivalentes a fazendas de servidores em hospedagem web convencional. Uma ou mais aplicações podem ser configuradas para funcionar nos mesmos recursos informáticos (ou no mesmo plano de Serviço de Aplicações).
>
>Para validar a contagem, dirija-se aos "planos do Serviço de Aplicações" no Portal Azure, onde pode ver o número de casos de computação utilizados por cada plano. 






Para desativar a monitorização e recomendações para o seu Serviço de Aplicações, repita este processo e altere o seu plano **de Serviço de Aplicações** para **Deficientes.**



## <a name="see-also"></a>Veja também
Neste artigo, aprendeu a utilizar as capacidades de monitorização no Centro de Segurança do Azure. Para saber mais sobre o Azure Security Center, consulte os seguintes artigos:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md): saiba como configurar definições de segurança no Centro de Segurança do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerir e responder a alertas de segurança.
* [Serviços de aplicações](security-center-virtual-machine-protection.md#app-services): Veja uma lista dos ambientes do seu serviço app com resumos de saúde.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): encontre publicações no blogue acerca da segurança e conformidade do Azure.
