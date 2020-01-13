---
title: Perguntas frequentes Azure Resource Health
description: Visão geral do Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 9f626dc4366cbdd9215704de97bb03fad19422eb
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912771"
---
# <a name="azure-resource-health-faq"></a>Perguntas frequentes Azure Resource Health
Conheça as respostas a perguntas comuns sobre Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>O que é Azure Resource Health?
O estado de funcionamento dos recursos ajuda-o a diagnosticar e a obter suporte quando um problema do Azure afeta os seus recursos. Informa-o relativamente ao estado de funcionamento atual e passado dos seus recursos e ajuda-o a atenuar problemas. O Resource Health fornece suporte técnico quando precisar de ajuda com problemas de serviço do Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>Qual é o Resource Health pretendido?
Depois que um problema com um recurso for detectado, Resource Health poderá ajudá-lo a diagnosticar a causa raiz. Ele fornece ajuda para atenuar o problema e o suporte técnico quando você precisar de mais ajuda com os problemas de serviço do Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Quais verificações de integridade são executadas por Resource Health?
O Resource Health executa várias verificações com base no [tipo de recurso](resource-health-checks-resource-types.md). Essas verificações foram projetadas para implementar três tipos de problemas: 
- Eventos não planejados, por exemplo uma reinicialização inesperada do host
- Eventos planejados, como atualizações agendadas do sistema operacional host
- Eventos disparados por ações do usuário, por exemplo, um usuário Reinicializando uma máquina virtual

## <a name="what-does-each-of-the-health-status-mean"></a>O que significa cada status de integridade?
Há três status de integridade diferentes:
- Disponível: não há nenhum problema conhecido na plataforma do Azure que possa afetar esse recurso
- Indisponível: o Resource Health detectou problemas que estão afetando o recurso
- Desconhecido: o Resource Health não pode determinar a integridade de um recurso porque ele parou de receber informações sobre ele. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>O que significa o status desconhecido? Há algo errado com meu recurso?
O status de integridade é definido como desconhecido quando Resource Health para de receber informações sobre um recurso específico. Embora esse status não seja uma indicação definitiva do estado do recurso, nos casos em que você está enfrentando problemas, isso pode indicar que há um problema do Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Como posso obter ajuda para um recurso que não está disponível?
Você pode enviar uma solicitação de suporte da folha Resource Health. Você não precisa de um contrato de suporte com a Microsoft para abrir uma solicitação quando o recurso não está disponível porque os eventos da plataforma.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Resource Health diferenciar entre maiúsculas e minúsculas de indisponibilidade por problemas de plataforma em relação a algo que eu fiz?
Sim, quando um recurso está indisponível, Resource Health identifica a causa raiz dentro de uma destas categorias: 
-   Ação iniciada pelo usuário
-   Evento planejado 
-   Evento não planejado

No portal, as ações iniciadas pelo usuário são mostradas usando um ícone de notificação azul, enquanto eventos planejados e não planejados são mostrados usando um ícone de aviso vermelho. Mais detalhes são fornecidos na [visão geral de Resource Health](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Posso integrar Resource Health com minhas ferramentas de monitoramento?
O Resource Health tem [suporte](resource-health-alert-arm-template-guide.md) para alertas baseados em log de atividades. Os alertas do log de atividades usam [grupos de ações](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) para notificar os usuários de que um alerta foi disparado. Os grupos de ação dão suporte a uma variedade de canais de notificação, como ações de email, SMS, webhook e ITSM.

## <a name="where-do-i-find-resource-health"></a>Onde encontro Resource Health?
Depois de fazer logon no portal do Azure, há várias maneiras que você pode acessar Resource Health:
- Navegue até o recurso. No painel de navegação à esquerda, selecione **Resource Health**
- Vá para a folha integridade de serviço do Azure.  No painel de navegação à esquerda, selecione **Resource Health**.
- Abra a folha **ajuda + suporte** selecionando o ponto de interrogação no canto superior direito do portal e, em seguida, selecionando **ajuda + suporte**. Depois que a folha for aberta, selecione **Resource Health**

Você também pode usar a API Resource Health para obter informações sobre a integridade de seus recursos.

## <a name="is-resource-health-available-for-all-resource-types"></a>Resource Health está disponível para todos os tipos de recurso?
A lista de verificações de integridade e tipos de recursos com suporte por meio de Resource Health pode ser encontrada [aqui](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>O que devo fazer se meu recurso estiver sendo mostrado disponível, mas acredito que ele não está? "
Ao verificar a integridade de um recurso, diretamente sob o status de integridade, você pode clicar em **relatar o status de integridade incorreto**. Antes de enviar o relatório, você tem a opção de fornecer detalhes adicionais sobre o motivo pelo qual você acredita que o status de integridade atual está incorreto.

## <a name="is-resource-health-available-for-all-azure-regions"></a>O está Resource Health disponível para todas as regiões do Azure? 
O Resource Health está disponível em todos os áreas geográficas do Azure.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Como o Resource Health é diferente do status do Azure ou do painel de integridade do serviço?
As informações fornecidas pelo Resource Health são mais específicas do que as fornecidas pelo status do Azure ou pelo painel de integridade do serviço.

Enquanto o [status do Azure](https://status.azure.com) e o painel de integridade do serviço informam sobre problemas de serviço que afetam um amplo conjunto de clientes (por exemplo, uma região do Azure), Resource Health expõe eventos mais granulares que são relevantes apenas para o recurso específico. Por exemplo, se um host for reinicializado inesperadamente, o Resource Health alertará somente os clientes cujas máquinas virtuais estavam em execução nesse host.

É importante observar que, para fornecer visibilidade completa dos eventos que afetam seus recursos, Resource Health também superfícies de eventos publicados no painel de integridade do serviço.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>É necessário ativar Resource Health para cada recurso?
Não, as informações de integridade estão disponíveis para todos os tipos de recursos disponíveis por meio de Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Precisamos habilitar Resource Health para minha organização?
Não.  Azure Resource Health está acessível dentro do portal do Azure sem nenhum requisito de configuração.

## <a name="is-resource-health-available-free-of-charge"></a>O Resource Health está disponível gratuitamente?
Sim.  Azure Resource Health é gratuita.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Quais são as recomendações que o Resource Health fornece?
Com base no status de integridade, Resource Health fornece recomendações com o objetivo de reduzir o tempo que você gastou Solucionando problemas. Para os recursos disponíveis, as recomendações se concentram em como resolver os problemas mais comuns encontrados pelos clientes. Se o recurso não estiver disponível devido a um evento não planejado do Azure, o foco estará para ajudá-lo durante e após o processo de recuperação. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Resource Health:
-  [Visão geral de Azure Resource Health](Resource-health-overview.md)
-  [Os tipos de recursos e verificações do estado de funcionamento disponíveis através do Azure Resource Health](resource-health-checks-resource-types.md)
