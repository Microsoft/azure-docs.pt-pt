---
title: FAQ de Saúde de Recursos Azure
description: Visão geral da Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: dc24688d8393b7a13e8c93d3f4d0171cce4c213c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570069"
---
# <a name="azure-resource-health-faq"></a>FAQ de Saúde de Recursos Azure
Aprenda as respostas a perguntas comuns sobre a Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>O que é o Azure Resource Health?
O estado de funcionamento dos recursos ajuda-o a diagnosticar e a obter suporte quando um problema do Azure afeta os seus recursos. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>Para que se destina a Saúde de Recursos?
Uma vez detetado um problema com um recurso, a Saúde do Recurso pode ajudá-lo a diagnosticar a causa principal. Ele fornece ajuda para mitigar o problema e suporte técnico quando você precisa de mais ajuda com problemas de serviço Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Que controlos de saúde são realizados pela Resource Health?
A saúde dos recursos realiza várias verificações com base no [tipo de recurso.](resource-health-checks-resource-types.md) Estes controlos destinam-se a implementar três tipos de questões: 
- Eventos não planeados, por exemplo, um reboot inesperado do anfitrião
- Eventos planeados, como atualizações programadas do HOST OS
- Eventos desencadeados pelas ações do utilizador, por exemplo, um utilizador reiniciando uma máquina virtual

## <a name="what-does-each-of-the-health-status-mean"></a>O que significa cada um dos estados de saúde?
Existem três diferentes estados de saúde:
- Disponível: Não existem problemas conhecidos na plataforma Azure que possam estar a afetar este recurso
- Indisponível: A saúde dos recursos detetou problemas que estão a afetar o recurso
- Desconhecido: A saúde dos recursos não pode determinar a saúde de um recurso porque deixou de receber informações sobre o mesmo. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>O que significa o estatuto desconhecido? Há algo de errado com o meu recurso?
O estado de saúde é definido para desconhecido quando a Saúde dos Recursos deixa de receber informações sobre um recurso específico. Embora este estatuto não seja uma indicação definitiva do estado do recurso, nos casos em que esteja a ter problemas, pode indicar que existe um problema de Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Como posso obter ajuda para um recurso indisponível?
Pode submeter um pedido de apoio da lâmina de saúde de recursos. Não precisa de um acordo de suporte com a Microsoft para abrir um pedido quando o recurso não está disponível porque os eventos da plataforma.

## <a name="does-resource-health-differentiate-between-unavailability-caused-by-platform-problems-versus-something-i-did"></a>A Resource Health diferencia entre a indisponibilidade causada por problemas de plataforma versus algo que fiz?
Sim, quando um recurso não está disponível, a Resource Health identifica a causa principal dentro de uma destas categorias: 
-   Ação iniciada pelo utilizador
-   Evento planeado 
-   Evento não planeado

No portal, as ações iniciadas pelo utilizador são mostradas usando um ícone de notificação azul, enquanto eventos planeados e não planeados são mostrados usando um ícone de aviso vermelho. Mais detalhes são fornecidos na visão geral da [Saúde dos Recursos.](Resource-health-overview.md)  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Posso integrar a Resource Health com as minhas ferramentas de monitorização?
A saúde dos recursos [suporta alertas baseados](resource-health-alert-arm-template-guide.md) em Registos de Atividade. Os alertas de Registo de Atividade utilizam [grupos de ação](../azure-monitor/alerts/action-groups.md) para notificar os utilizadores de que foi desencadeado um alerta. Os grupos de ação suportam uma variedade de canais de notificação, tais como ações de e-mail, SMS, webhook e ITSM.

## <a name="where-do-i-find-resource-health"></a>Onde encontro a Saúde dos Recursos?
Depois de iniciar sessão no portal Azure, existem várias formas de aceder à Saúde dos Recursos:
- Navegue para o seu recurso. Na navegação à esquerda, selecione **a saúde do recurso**
- Vá ao Serviço Azure Health Blade.  Na navegação à esquerda, selecione **a saúde dos recursos.**

Também pode utilizar a API de Saúde de Recursos para obter informações sobre a saúde dos seus recursos.

## <a name="is-resource-health-available-for-all-resource-types"></a>A Saúde dos Recursos está disponível para todos os tipos de recursos?
A lista de verificações de saúde e tipos de recursos suportados através da Saúde dos Recursos pode ser consultada [aqui.](resource-health-checks-resource-types.md)

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>O que devo fazer se o meu recurso estiver disponível, mas acredito que não é?"
Ao verificar a saúde de um recurso, bem abaixo do estado de saúde pode clicar **em Reportar Um Estado de saúde incorreto**. Antes de apresentar o relatório, tem a opção de fornecer detalhes adicionais sobre o motivo pelo qual acredita que o estado de saúde atual está incorreto.

## <a name="is-resource-health-available-for-all-azure-regions"></a>A Saúde dos Recursos está disponível para todas as regiões do Azure? 
A saúde dos recursos está disponível em todos os geos Azure.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Como é que a Saúde dos Recursos é diferente do estado de Azure ou do painel de instrumentos de saúde do serviço?
A informação fornecida pela Resource Health é mais específica do que a fornecida pelo estado Azure ou pelo painel de instrumentos de saúde do serviço.

Enquanto o [estado do Azure](https://status.azure.com) e o painel de saúde do serviço o informam sobre problemas de serviço que afetam um vasto conjunto de clientes (por exemplo, uma região Azure), a Resource Health expõe mais eventos granulares que são relevantes apenas para o recurso específico. Por exemplo, se um hospedeiro reiniciar inesperadamente, a Resource Health alerta apenas os clientes cujas máquinas virtuais estavam a funcionar nesse hospedeiro.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Preciso de ativar a Saúde dos Recursos para cada recurso?
Não, a informação de saúde está disponível para todos os tipos de recursos disponíveis através da Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Precisamos de capacitar a Saúde dos Recursos para a minha organização?
N.º  A Azure Resource Health é acessível dentro do portal Azure sem quaisquer requisitos de configuração.

## <a name="is-resource-health-available-free-of-charge"></a>A Saúde dos Recursos está disponível gratuitamente?
Sim.  A Azure Resource Health é gratuita.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Quais são as recomendações que a Resource Health fornece?
Com base no estado de saúde, a Resource Health fornece-lhe recomendações com o objetivo de reduzir o tempo que passou a resolver problemas. Para os recursos disponíveis, as recomendações focam-se em como resolver os problemas mais comuns que os clientes encontram. Se o recurso não estiver disponível devido a um evento não planeado do Azure, o foco será ajudá-lo durante e após o processo de recuperação. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Saúde dos Recursos:
-  [Visão geral da Saúde dos Recursos Azure](Resource-health-overview.md)
-  [Os tipos de recursos e verificações do estado de funcionamento disponíveis através do Azure Resource Health](resource-health-checks-resource-types.md)
