---
title: FAQ do Estado de funcionamento do recurso do Azure | Documentos da Microsoft
description: Descrição geral do Estado de funcionamento de recursos do Azure
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.workload: Supportability
ms.openlocfilehash: 03c5fb0f399e43af2584743655eeea8af4c5b2f5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924455"
---
# <a name="azure-resource-health-faq"></a>FAQ do Estado de funcionamento do recurso do Azure
Aprenda as respostas a perguntas comuns sobre o Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>O que é o Azure Resource Health?
O estado de funcionamento dos recursos ajuda-o a diagnosticar e a obter suporte quando um problema do Azure afeta os seus recursos. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>O que é o estado de funcionamento do recurso destinado a?
Assim que foi detetado um problema com um recurso, o estado de funcionamento do recurso pode ajudar a diagnosticar a causa raiz. Ele fornece ajuda a mitigar o problema e o suporte técnico quando precisar de mais ajuda com problemas de serviço do Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Verifica o estado de funcionamento são efetuados pelo Resource Health?
Estado de funcionamento do recurso executa várias verificações com base na [tipo de recurso](resource-health-checks-resource-types.md). Estas verificações foram concebidas para implementar três tipos de problemas: 
- Eventos não planeados, por exemplo um reinício de anfitrião inesperado
- Eventos programados, como atualizações de SO do host agendada
- Eventos acionados pelas ações do usuário, por exemplo, um utilizador reiniciar uma máquina virtual

## <a name="what-does-each-of-the-health-status-mean"></a>O que cada um o estado de funcionamento significa?
Existem três Estados de funcionamento de diferentes:
- Disponível: Não existem problemas conhecidos na plataforma do Azure que pode estar a afetar este recurso
- Indisponível: Estado de funcionamento do recurso detetou problemas que estejam a afetar o recurso
- Desconhecido: Estado de funcionamento do recurso não pode determinar o estado de funcionamento de um recurso uma vez que parou de receber informações sobre ele. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>O que significa o estado desconhecido? Há algo de errado com o meu recurso?
O estado de funcionamento é definido como unknown quando o estado de funcionamento do recurso para receber informações sobre um recurso específico. Enquanto este estado não é uma indicação definitiva do Estado do recurso, nos casos em que estiver tendo problemas, ele pode indicar que existe um problema do Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Como posso obter ajuda para um recurso que não está disponível?
Pode submeter um pedido de suporte a partir do painel de estado de funcionamento do recurso. Não é necessário um contrato de suporte com a Microsoft para abrir um pedido, quando o recurso não está disponível porque os eventos de plataforma.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Estado de funcionamento do recurso diferenciar entre indisponibilidade Case por problemas de plataforma em comparação com algo que fiz?
Sim, quando um recurso não está disponível, o estado de funcionamento do recurso identifica a causa de raiz dentro de uma destas categorias: 
-   Ação iniciada pelo utilizador
-   Evento planeado 
-   Evento não planeado

No portal, as ações iniciadas pelo utilizador são apresentadas com um ícone de notificação azul, enquanto planeados e não planeados de eventos são apresentados com um ícone de aviso vermelho. Obter mais detalhes são fornecidos na [descrição geral do Estado de funcionamento do recurso](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Posso integrar o estado de funcionamento de recursos com minhas ferramentas de monitoramento?
Estado de funcionamento do recurso [suporte de pré-visualização](resource-health-alert-arm-template-guide.md) para registo de atividades com base em alertas. Utilização de alertas de registo de atividades [grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) para notificar os utilizadores que foi acionado um alerta. Os grupos de ação suportam uma variedade de canais de notificação, como e-mail, SMS, webhook e ações do ITSM.

## <a name="where-do-i-find-resource-health"></a>Onde posso encontrar o Resource Health?
Depois de iniciar sessão portal do Azure, existem várias formas de acessar o estado de funcionamento do recurso:
- Navegue para o seu recurso. No painel de navegação esquerdo, selecione **estado de funcionamento do recurso**
- Aceda ao painel do Azure Service Health.  No painel de navegação esquerdo, selecione **estado de funcionamento do recurso**.
- Abra o **ajuda + suporte** painel selecionar o ponto de interrogação no canto superior direito do portal e selecionando **ajuda + suporte**. Quando abre o painel, selecione **estado de funcionamento do recurso**

Também pode utilizar a API de estado de funcionamento do recurso para obter informações sobre o estado de funcionamento dos seus recursos.

## <a name="is-resource-health-available-for-all-resource-types"></a>Estado de funcionamento do recurso está disponível para todos os tipos de recursos?
A lista de verificações de estado de funcionamento e os tipos de recursos oferecidos por meio do Estado de funcionamento do recurso pode ser encontrada [aqui](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>O que devo fazer se o meu recurso está a mostrar disponível, mas acredito que não é?"
Ao verificar o estado de funcionamento de um recurso, o estado de funcionamento em pode clicar em **comunicou o estado de funcionamento incorreto**. Antes de submeter o relatório, tem a opção de fornecer detalhes adicionais sobre por que ache que o estado de funcionamento atual está incorreto.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Estado de funcionamento do recurso está disponível para todas as regiões do Azure? 
Estado de funcionamento do recurso está disponível em todas as geos do Azure.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Como é diferente do Estado do Azure ou o dashboard de estado de funcionamento do serviço Resource Health?
As informações fornecidas pelo Estado de funcionamento do recurso são mais específicas do que o que é fornecido pelo Estado do Azure ou o dashboard de estado de funcionamento do serviço.

Enquanto [estado do Azure](https://status.azure.com) e o dashboard de estado de funcionamento do serviço informá-lo sobre problemas de serviço que afetam um vasto leque de clientes (por exemplo uma região do Azure), o estado de funcionamento do recurso expõe mais granulares eventos que são relevantes apenas para o recurso específico. Por exemplo, se um anfitrião reinicia inesperadamente, o Resource Health alerta apenas aqueles clientes cujas máquinas virtuais estavam em execução nesse anfitrião.

É importante observar que para lhe fornecer visibilidade completa de eventos que afetem seus recursos, o estado de funcionamento do recurso também apresenta eventos publicados no dashboard do Estado de funcionamento do serviço.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>É necessário ativar o estado de funcionamento do recurso para cada recurso?
Não, as informações de estado de funcionamento estão disponíveis para todos os tipos de recursos disponíveis através do Estado de funcionamento do recurso. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>É necessário ativar o estado de funcionamento do recurso para a minha organização?
Não.  Estado de funcionamento de recursos do Azure está acessível no portal do Azure sem quaisquer requisitos de configuração.

## <a name="is-resource-health-available-free-of-charge"></a>É o Resource Health disponíveis gratuitamente?
Sim.  Estado de funcionamento de recursos do Azure é gratuito.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Quais são as recomendações que fornece o estado de funcionamento do recurso?
Com base no estado de funcionamento, estado de funcionamento do recurso fornece recomendações com o objetivo de reduzir o tempo que gasto na solução de problemas. Para recursos disponíveis, o foco de recomendações sobre como resolver os clientes de problemas mais comuns encontrados. Se o recurso está indisponível devido a um evento não planeado do Azure, o foco será nos ajudar o usuário durante e após o processo de recuperação. 

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o estado de funcionamento do recurso:
-  [Descrição geral do Estado de funcionamento de recursos do Azure](Resource-health-overview.md)
-  [Os tipos de recursos e verificações do estado de funcionamento disponíveis através do Azure Resource Health](resource-health-checks-resource-types.md)
