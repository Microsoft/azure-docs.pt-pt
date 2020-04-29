---
title: Resolução de problemas Alertas e notificações do Monitor Azure
description: Problemas comuns com alertas do Monitor Azure e possíveis soluções.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132332"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Problemas de resolução de problemas nos alertas do Monitor Azure 

Este artigo discute problemas comuns no alerta do Monitor Azure.

Os alertas do Monitor Azure notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Permitem identificar e resolver problemas antes que os utilizadores do seu sistema os notem. Para obter mais informações sobre alerta, consulte [a visão geral dos alertas no Microsoft Azure](alerts-overview.md).

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Ação ou notificação no meu alerta não funcionou como esperado

Se puder ver um alerta de saque no portal Azure, mas tiver algum problema com algumas das suas ações ou notificações, consulte as seguintes secções.

## <a name="did-not-receive-expected-email"></a>Não recebeu e-mail esperado

Se conseguir ver um alerta de saque no portal Azure, mas não recebeu o e-mail que configurado sobre o mesmo, siga estes passos: 

1. **O e-mail foi suprimido por uma regra de [ação?](alerts-action-rules.md)** 

    Verifique clicando no alerta de fogo disparado no portal e consulte o separador de histórico para [grupos](action-groups.md)de ação suprimidos: 

    ![História de supressão de regras de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)

1. **O tipo de ação "Email Azure Resource Manager Role"?**

    Esta ação apenas analisa as atribuições de funções do Gestor de Recursos do Azure que estão no âmbito da subscrição e do tipo *User*.  Certifique-se de que atribuiu o papel ao nível da subscrição e não ao nível dos recursos ou do grupo de recursos.

1. **O seu servidor de e-mail e caixa de correio estão a aceitar e-mails externos?**

    Verifique se os e-mails destes três endereços não estão bloqueados:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    É comum que listas internas de correio ou listas de distribuição bloqueiem e-mails de endereços de e-mail externos. Precisa de listas brancas nos endereços de e-mail acima.  
    Para testar, adicione um endereço de e-mail de trabalho regular (não uma lista de correio) ao grupo de ação e veja se os alertas chegam a esse e-mail. 

1. **O e-mail foi processado por regras de caixa de entrada ou um filtro de spam?** 

    Verifique se não existem regras de caixa de entrada que apaguem esses e-mails ou os movam para uma pasta lateral. Por exemplo, as regras da caixa de entrada podem capturar remetentes específicos ou palavras específicas no assunto.

    Além disso, verifique:
    
      - as definições de spam do seu cliente de e-mail (como Outlook, Gmail)
      - os limites do remetente / definições de spam / definições de quarentena do seu servidor de e-mail (como Exchange, Office 365, G-suite)
      - as definições do seu aparelho de segurança por e-mail, se houver (como Barracuda, Cisco). 

1. **Desprendeu acidentalmente o grupo de ação?** 

    Os e-mails de alerta fornecem um link para cancelar a subscrição do grupo de ação. Para verificar se acidentalmente desregistou deste grupo de ação:

    1. Abra o grupo de ação no portal e verifique a coluna Status:

    ![coluna de estado de grupo de ação](media/alerts-troubleshoot/action-group-status.png)

    2. Procure no seu e-mail a confirmação da subscrição:

    ![sem subscrição do grupo de ação alerta](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Para subscrever novamente – utilize o link no e-mail de confirmação de cancelamento de subscrição que recebeu, ou remova o endereço de e-mail do grupo de ação e, em seguida, adicione-o novamente. 
 
1. **Foi classificado limitado devido a muitos e-mails que vão para um único endereço de e-mail?** 

    O e-mail é [limitado](alerts-rate-limiting.md) a não mais de 100 e-mails a cada hora para cada endereço de e-mail. Se passar este limiar, as notificações adicionais de e-mail são retiradas.  Verifique se recebeu uma mensagem indicando que o seu endereço de e-mail foi temporariamente limitado: 
 
   ![Taxa de e-mail limitada](media/alerts-troubleshoot/email-paused.png)

   Se quiser receber um elevado volume de notificações sem limitar as taxas, considere utilizar uma ação diferente, como webhook, logic app, função Azure ou livros de automação, nenhum dos quais é limitado. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Não recebeu SMS esperado, chamada de voz ou notificação push

Se conseguir ver um alerta de disparo no portal, mas não recebeu o SMS, a chamada de voz ou a notificação de pressão que configuraste sobre o mesmo, siga estes passos: 

1. **A ação foi suprimida por uma regra de [ação?](alerts-action-rules.md)** 

    Verifique clicando no alerta de fogo disparado no portal e consulte o separador de histórico para [grupos](action-groups.md)de ação suprimidos: 

    ![História de supressão de regras de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)

   Se isso não foi intencional, pode modificar, desativar ou eliminar a regra de ação.
 
1. **SMS / voz: O seu número de telefone está correto?**

   Verifique a ação SMS para obter tipopos no código do país ou número de telefone. 
 
1. **SMS/voz: tem sido limitado?** 

    SMS e chamadas de voz são limitadas a não mais do que uma notificação a cada cinco minutos por número de telefone. Se passar este limiar, as notificações serão retiradas. 

      - Chamada de voz - verifique o histórico da sua chamada e veja se recebeu uma chamada diferente de Azure nos cinco minutos anteriores. 
      - SMS - verifique o seu histórico de SMS para obter uma mensagem indicando que o seu número de telefone foi limitado. 

    Se quiser receber um elevado volume de notificações sem limitar as taxas, considere utilizar uma ação diferente, como webhook, logic app, função Azure ou livros de automação, nenhum dos quais é limitado. 
 
1. **SMS: Desprendeu acidentalmente o sinugértido do grupo de ação?**

    Abra o seu histórico de SMS e verifique se optou por não entregar SMS deste grupo de ação específico (utilizando a resposta de desativação action_group_short_name) ou de todos os grupos de ação (utilizando a resposta STOP). Para subscrever novamente, ou envie o comando SMS relevante (ENABLE action_group_short_name ou START), ou remova a ação SMS do grupo de ação e, em seguida, adicione-o novamente.  Para obter mais informações, consulte o comportamento de [alerta de SMS em grupos de ação](alerts-sms-behavior.md).

1. **Bloqueou acidentalmente as notificações no seu telemóvel?**

   A maioria dos telemóveis permite bloquear chamadas ou SMS a partir de números de telefone específicos ou códigos curtos, ou bloquear notificações push de aplicações específicas (como a aplicação móvel Azure). Para verificar se bloqueou acidentalmente as notificações no seu telemóvel, procure na documentação específica para o seu sistema operativo e modelo do seu telefone ou teste com um telefone e número de telefone diferentes. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Esperava outro tipo de ação para desencadear, mas não 

Se conseguir ver um alerta de disparo no portal, mas a sua ação configurada não disparou, siga estes passos: 

1. **A ação foi suprimida por uma regra de ação?** 

    Verifique clicando no alerta de fogo disparado no portal e consulte o separador de histórico para [grupos](action-groups.md)de ação suprimidos: 

    ![História de supressão de regras de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)
 
    Se isso não foi intencional, pode modificar, desativar ou eliminar a regra de ação. 

1. **Um gancho de teia não disparou?**

    1. **Os endereços IP de origem foram bloqueados?**
    
       Whitelist os [endereços IP](action-groups.md#action-specific-information) de que o webhook é chamado.

    1. **O seu ponto final do webhook funciona corretamente?**

       Verifique se o ponto final do webhook configurado está correto e o ponto final está a funcionar corretamente. Verifique os registos do seu webhook ou o seu código para que possa investigar (por exemplo, registar a carga útil de entrada). 

    1. **Estás a ligar para o Slack ou para a Microsoft Teams?**  
    Cada um destes pontos finais espera um formato JSON específico. Siga [estas instruções](action-groups-logic-app.md) para configurar uma ação lógica de aplicação.

    1. **O teu gancho de teia ficou sem resposta ou voltou a cometer erros?** 

        O nosso período de tempo para uma resposta webhook é de 10 segundos. A chamada webhook será novamente experimentada até duas vezes mais quando os seguintes códigos de estado HTTP forem devolvidos: 408, 429, 503, 504, ou quando o ponto final http não responder. A primeira repetição ocorre ao fim de 10 segundos. A segunda e última tentativa acontece após 100 segundos. Se a segunda repetição falhar, não são feitas novas tentativas de chamar o ponto final durante 30 minutos, para qualquer grupo de ações.

## <a name="action-or-notification-happened-more-than-once"></a>Ação ou notificação aconteceu mais de uma vez 

Se recebeu uma notificação para um alerta (como um e-mail ou um SMS) mais de uma vez, ou a ação do alerta (como webhook ou função Azure) foi desencadeada várias vezes, siga estes passos: 

1. **É mesmo o mesmo alerta?** 

    Em alguns casos, vários alertas semelhantes são disparados por volta da mesma hora. Então, pode parecer que o mesmo alerta desencadeou as suas ações várias vezes. Por exemplo, uma regra de alerta de registo de atividade pode ser configurada para disparar tanto quando um evento começou, como quando tiver terminado (bem sucedido ou falhado), não filtrando no campo de estado do evento. 

    Para verificar se estas ações ou notificações vieram de diferentes alertas, examine os detalhes de alerta, tais como a sua marca de tempo e o id de alerta ou o seu id de correlação. Em alternativa, consulte a lista de alertas disparados no portal. Se for esse o caso, terá de adaptar a lógica da regra de alerta ou configurar a fonte de alerta. 

1. **A ação repete-se em vários grupos de ação?** 

    Quando um alerta é disparado, cada um dos seus grupos de ação é processado de forma independente. Assim, se uma ação (como um endereço de e-mail) aparecer em vários grupos de ação desencadeados, seria chamada uma vez por grupo de ação. 

    Para verificar que grupos de ação foram acionados, verifique o separador de histórico de alerta. Veria que ambos os grupos de ação se definiam na regra de alerta, e grupos de ação adicionados ao alerta pelas regras de ação: 

    ![Ação repetida em múltiplos grupos de ação](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Ação ou notificação tem um conteúdo inesperado

Se recebeu o alerta, mas acredita que alguns dos seus campos estão desaparecidos ou incorretos, siga estes passos: 

1. **Escolheu o formato correto para a ação?** 

    Cada tipo de ação (e-mail, webhook, etc.) tem dois formatos – o formato padrão, o formato legado e o [mais recente formato de esquema comum.](alerts-common-schema.md) Ao criar um grupo de ação, especifice o formato que deseja por ação – diferentes ações nos grupos de ação podem ter formatos diferentes. 

    Por exemplo, para ação webhook: 

    ![opção esquema de ação webhook](media/alerts-troubleshoot/webhook.png)

    Verifique se o formato especificado ao nível de ação é o que espera. Por exemplo, pode ter desenvolvido um código que responda a alertas (webhook, função, aplicação lógica, etc.), esperando um formato, mas mais tarde na ação que você ou outra pessoa especificaram um formato diferente.  

    Além disso, verifique o formato de carga útil (JSON) para [obter alertas](activity-log-alerts-webhook.md)de registo de atividade, para [alertas](alerts-log-webhook.md) de pesquisa de registo (tanto de Informações de Aplicação como de análise de registo), para [alertas métricos,](alerts-metric-near-real-time.md#payload-schema)para o esquema de [alerta comum,](alerts-common-schema-definitions.md)e para os [alertas métricos clássicos](alerts-webhooks.md)depreciados.

 
1. **Alertas de registo de atividade: A informação disponível no registo de atividades?** 

    [Os alertas](activity-log-alerts.md) de registo de atividade são alertas que se baseiam em eventos escritos no Registo de Atividades do Azure, tais como eventos sobre a criação, atualização ou alocação de recursos Azure, eventos de saúde de serviço e recursos, ou resultados do Azure Advisor e da Política Azure. Se recebeu um alerta com base no registo de atividade, mas alguns campos de que necessita estão em falta ou incorretos, verifique primeiro os eventos no próprio registo de atividade. Se o recurso Azure não tiver escrito os campos que procura no seu evento de registo de atividade, esses campos não serão incluídos no alerta correspondente. 

## <a name="action-rule-is-not-working-as-expected"></a>A regra da ação não está a funcionar como esperado. 

Se você pode ver um alerta de saque no portal, mas uma regra de ação relacionada não funcionou como esperado, siga estes passos: 

1. **A regra de ação está ativada?** 

    Verifique a coluna de estado da regra de ação para verificar se a função de ação conexa está ativada. 

    ![gráfico](media/alerts-troubleshoot/action-rule-status.png) 

    Se não estiver ativado, pode ativar a regra de ação selecionando-a e clicando em Ativar. 

1. **É um alerta de saúde de serviço?** 

    Os alertas de saúde do serviço (serviço de monitorização = "Saúde do Serviço") não são afetados pelas regras de ação. 

1. **A regra da ação agiu no seu alerta?** 

    Verifique se a regra de ação processou o seu alerta clicando no alerta disparado no portal e veja o separador de histórico.

    Eis um exemplo de regra de ação que suprime todos os grupos de ação: 
 
     ![História de supressão de regras de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)

    Aqui está um exemplo de uma regra de ação adicionando outro grupo de ação:

    ![Ação repetida em múltiplos grupos de ação](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **O âmbito da regra de ação e o filtro coincidem com o alerta disparado?** 

    Se acha que a regra de ação devia ter disparado, mas não o fez, ou que não devia ter disparado, mas disparou, examinando cuidadosamente o âmbito da regra de ação e as condições de filtro contra as propriedades do alerta disparado. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Como encontrar a identidade de alerta de um alerta disparado

Ao abrir um caso sobre um alerta específico de saque (como – caso não tenha recebido a notificação por e-mail), terá de fornecer o id de alerta. 

Para localizá-lo, siga estes passos:

1. No portal Azure, navegue para a lista de alertas disparados e encontre esse alerta específico. Pode usar os filtros para ajudá-lo a localizá-lo. 

1. Clique no alerta para abrir os detalhes do alerta. 

1. Desloque-se para baixo nos campos de alerta do primeiro separador (o separador resumo) até o localizar e copiá-lo. Este campo também inclui um botão de ajuda "Copiar para clipboard" que pode utilizar.  

    ![encontrar id alerta](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problemas de criação, atualização ou eliminação de regras de ação no portal Azure

Se tiver recebido um erro ao tentar criar, atualizar ou eliminar uma regra de [ação,](alerts-action-rules.md)siga estes passos: 

1. **Recebeu um erro de permissão?**  

    Deve ter o papel integrado do [Colaborador de Monitorização,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)ou as permissões específicas relacionadas com regras de ação e alertas.

1. **Verificou os parâmetros da regra de ação?**  

    Verifique a [documentação](alerts-action-rules.md)da regra de ação , ou a regra de [ação PowerShell Set-AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) comando. 


## <a name="next-steps"></a>Passos seguintes
- Se utilizar um alerta de registo, consulte também alertas de [registo de resolução de problemas](alert-log-troubleshoot.md).
- Volte ao [portal Azure](https://portal.azure.com) para verificar se resolveu o seu problema com orientação acima 
