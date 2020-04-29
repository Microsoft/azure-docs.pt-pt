---
title: Proteja as suas aplicações web do Azure App Service e APIs
description: Este artigo ajuda-o a começar a proteger as suas aplicações web do Azure App Service e APIs no Azure Security Center.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77616471"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Proteja as suas aplicações web do Azure App Service e APIs

O Azure App Service é uma plataforma totalmente gerida para construir e hospedar as suas aplicações web e APIs sem se preocupar em ter de gerir a infraestrutura. Fornece informações de gestão, monitorização e operacional para satisfazer os requisitos de desempenho, segurança e conformidade de nível empresarial. Para mais informações, consulte [o Azure App Service](https://azure.microsoft.com/services/app-service/).

Para permitir uma proteção avançada contra ameaças para o seu plano de Serviço de Aplicações Azure, deve:

* Subscreva o nível de preços standard do Azure Security Center
* Ative o plano de Serviço de Aplicações como mostrado abaixo. O Security Center está integrado de forma nativa com o App Service, eliminando a necessidade de implantação e embarque - a integração é transparente.
* Tenha um plano de Serviço de Aplicações que esteja associado a máquinas dedicadas. Os planos suportados são: Básico, Standard, Premium, Isolado ou Linux. O Centro de Segurança não apoia os planos de consumo gratuito, partilhado ou de consumo. Para mais informações, consulte planos de serviço de [aplicações.](https://azure.microsoft.com/pricing/details/app-service/plans/)

Com o plano de Serviço de Aplicações ativado, o Security Center avalia os recursos abrangidos pelo seu plano de Serviço de Aplicações e gera recomendações de segurança com base nas suas conclusões. O Security Center protege a instância VM em que o seu Serviço de Aplicações está a funcionar e a interface de gestão. Também monitoriza pedidos e respostas enviados de e para as suas aplicações em execução no App Service.

O Security Center aproveita a escala da nuvem e a visibilidade que o Azure tem como fornecedor de nuvem, para monitorizar os ataques comuns da aplicação web. O Security Center pode descobrir ataques às suas aplicações e identificar ataques emergentes - mesmo quando os atacantes estão em fase de reconhecimento, procurando identificar vulnerabilidades em várias aplicações hospedadas no Azure. Como um serviço azure-nativo, o Security Center também está numa posição única para oferecer análises de segurança baseadas em hospedeiros cobrindo os nós de computação subjacentes para este PaaS, permitindo ao Centro de Segurança detetar ataques contra aplicações web que já foram exploradas. Para mais detalhes, consulte a proteção de ameaças para o Serviço de [Aplicações Azure](threat-protection.md#app-services).


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Permitir a monitorização e proteção do Serviço de Aplicações

1. No portal Azure, escolha O Centro de Segurança.
2. Vá às **definições de Preços &** e escolha uma subscrição.
3. Sob **o nível**de Preços , na linha de **serviço** app, alternar o seu plano para **Ativado**.

    [![Ativar serviços de aplicações na subscrição do standard tier](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> O número de instâncias listadas para a sua Quantidade de **Recursos** representa o número total de instâncias computacionais, em todos os planos do Serviço de Aplicações nesta subscrição, funcionando no momento em que abriu a lâmina de nível de preços.
>
> O Azure App Service oferece uma variedade de planos. O seu plano de Serviço de Aplicações define o conjunto de recursos computacionais para uma aplicação web a executar. Estes são equivalentes a quintas de servidores em hospedagem web convencional. Uma ou mais aplicações podem ser configuradas para executar os mesmos recursos de computação (ou no mesmo plano de Serviço de Aplicações).
>
>Para validar a contagem, dirija-se aos "planos do Serviço de Aplicações" no Portal Azure, onde poderá ver o número de instâncias computacionais utilizadas por cada plano. 






Para desativar a monitorização e recomendações para o seu Serviço de Aplicações, repita este processo e altere o seu plano de Serviço de **Aplicações** para **Deficientes**.



## <a name="see-also"></a>Consulte também
Neste artigo, aprendeu a utilizar as capacidades de monitorização no Centro de Segurança do Azure. Para saber mais sobre o Azure Security Center, consulte os seguintes artigos:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md): saiba como configurar definições de segurança no Centro de Segurança do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerir e responder a alertas de segurança.
* [Serviços](security-center-virtual-machine-protection.md#app-services)de aplicações : Veja uma lista dos ambientes de serviço da sua App com resumos de saúde.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): encontre publicações no blogue acerca da segurança e conformidade do Azure.
