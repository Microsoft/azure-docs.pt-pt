---
title: Azure Defender for App Service - os benefícios e funcionalidades
description: Conheça os benefícios e funcionalidades do Azure Defender para o Serviço de Aplicações.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a7026757651d1b0510293101203f41a651c7b851
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850970"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Introdução ao Azure Defender para o Serviço de Aplicações

O Azure App Service é uma plataforma totalmente gerida para construir e hospedar as suas aplicações web e APIs sem se preocupar em ter de gerir a infraestrutura. Fornece informações de gestão, monitorização e operacionais para atender aos requisitos de desempenho, segurança e conformidade de nível empresarial. Para mais informações, consulte [o Azure App Service](https://azure.microsoft.com/services/app-service/).

**O Azure Defender for App Service** utiliza a escala da nuvem para identificar ataques direcionados a aplicações que passam por cima do Serviço de Aplicações. Os atacantes sondam aplicações web para encontrar e explorar fraquezas. Antes de serem encaminhados para ambientes específicos, os pedidos para aplicações em execução em Azure passam por vários gateways, onde são inspecionados e registados. Estes dados são então usados para identificar explorações e atacantes, e para aprender novos padrões que serão usados mais tarde.

Ao usar a visibilidade que o Azure tem como fornecedor de nuvem, o Security Center analisa os registos internos do Serviço de Aplicações para identificar a metodologia de ataque em vários alvos. Por exemplo, a metodologia inclui a digitalização generalizada e os ataques distribuídos. Este tipo de ataque normalmente vem de um pequeno subconjunto de IPs, e mostra padrões de rastejar para pontos finais semelhantes em vários anfitriões. Os ataques estão à procura de uma página ou plugin vulneráveis, e não podem ser identificados do ponto de vista de um único hospedeiro.


## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|[O Azure Defender for App Service](azure-defender.md) é faturado como mostrado na página de [preços](security-center-pricing.md)|
|Planos de Serviço de Aplicações suportados:|![Sim ](./media/icons/yes-icon.png) Básico, Standard, Premium, Isolado ou Linux<br>![Sem ](./media/icons/no-icon.png) Consumo Gratuito, Partilhado ou Partilhado<br>[Saiba mais sobre Planos de Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>O que protege o Azure Defender para o Serviço de Aplicações?

Com o plano de Serviço de Aplicações ativado, o Security Center avalia os recursos abrangidos pelo seu plano de Serviço de Aplicações e gera recomendações de segurança com base nas suas conclusões. O Security Center protege a instância VM em que o seu Serviço de Aplicações está a funcionar e a interface de gestão. Também monitoriza pedidos e respostas enviadas de e para as suas apps em execução no Serviço de Aplicações.

Se estiver a executar um plano de Serviço de Aplicações baseado no Windows, o Security Center também tem acesso às caixas de areia e VMs subjacentes. Juntamente com os dados de registo acima mencionados, a infraestrutura pode contar a história, desde um novo ataque que circula na natureza até compromissos nas máquinas de clientes. Portanto, mesmo que o Security Center seja implementado depois de uma aplicação web ter sido explorada, pode ser capaz de detetar ataques em curso.


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Proteger as suas aplicações Web e APIs do Serviço de Aplicações do Azure
Para proteger o seu plano de Serviço de Aplicações Azure com o Azure Defender para o Serviço de Aplicações:

- Certifique-se de que tem um plano de Serviço de Aplicações suportado que está associado a máquinas dedicadas. Os planos suportados estão listados acima na [Disponibilidade.](#availability)

- Ativar **o Azure Defender** na sua subscrição (pode opcionalmente ativar apenas o plano **Azure Defender for App Service)** conforme descrito no [Preço do Centro de Segurança Azure](security-center-pricing.md)

O Security Center está nativamente integrado com o Serviço de Aplicações, eliminando a necessidade de implantação e embarque - a integração é transparente.

>[!NOTE]
> A página de preços e definições lista uma série de casos para a sua **Quantidade de Recursos**. Isto representa o número total de instâncias computacional, em todos os planos do Serviço de Aplicações nesta subscrição, em execução no momento em que abriu a página do nível de preços.
>
> O Azure App Service oferece uma variedade de planos. O seu plano de Serviço de Aplicações define o conjunto de recursos computacionares para uma aplicação web a executar. Estes são equivalentes a fazendas de servidores em hospedagem web convencional. Uma ou mais aplicações podem ser configuradas para funcionar nos mesmos recursos informáticos (ou no mesmo plano de Serviço de Aplicações).
>
>Para validar a contagem, dirija-se aos "planos do Serviço de Aplicações" no Portal Azure, onde pode ver o número de casos de computação utilizados por cada plano. 



## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre Azure Defender para o Serviço de Aplicações. 

Para obter material relacionado, consulte os seguintes artigos: 

- Se um alerta é gerado pelo Security Center, ou recebido pelo Security Center a partir de um produto de segurança diferente, você pode exportá-lo. Para exportar os seus alertas para a Azure Sentinel, qualquer SIEM de terceiros, ou qualquer outra ferramenta externa, siga as instruções em [alertas de exportação para um SIEM](continuous-export.md).
- Para obter uma lista dos alertas do Serviço de Aplicações Azure, consulte a [tabela de alertas de referência.](alerts-reference.md#alerts-azureappserv)
- Para obter mais informações sobre os planos do Serviço de Aplicações, consulte [os planos do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/plans/)
- > [!div class="nextstepaction"]
    > [Ativar o Azure Defender](security-center-pricing.md)