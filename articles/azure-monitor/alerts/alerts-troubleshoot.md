---
title: Resolução de problemas alertas e notificações do Monitor Azure
description: Problemas comuns com alertas do Azure Monitor e possíveis soluções.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 08ac23fbb29829bf2a7a46e41f9a7f646ad97915
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586323"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Problemas de resolução de problemas nos alertas do Monitor Azure

Este artigo discute problemas comuns no alerta e notificações do Azure Monitor.

Os alertas do Azure Monitor notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Permitem identificar e resolver problemas antes que os utilizadores do seu sistema os notem. Para obter mais informações sobre o alerta, consulte [a visão geral dos alertas no Microsoft Azure](./alerts-overview.md).

Se tiver algum problema com um disparo de alerta ou não disparar quando esperado, consulte os artigos abaixo. Pode ver alertas "disparados" no portal Azure.

- [Alertas métricos do Monitor Azure na Microsoft Azure](alerts-troubleshoot-metric.md)  
- [Alertas de registo do monitor de azure na Microsoft Azure](alerts-troubleshoot-log.md)

Se o alerta disparar conforme o previsto pelo portal Azure, mas as notificações adequadas não ocorrerem, utilize as informações no resto deste artigo para resolver esse problema.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Ação ou notificação no meu alerta não funcionou como esperado

Se você pode ver um alerta de disparo no portal Azure, mas tem um problema com algumas das suas ações ou notificações, consulte as seguintes secções.

## <a name="did-not-receive-expected-email"></a>Não recebeu e-mail esperado

Se vir um alerta acionado no portal do Azure, mas não tiver recebido o e-mail que configurou para tal, siga estes passos:

1. **O e-mail foi suprimido por uma [regra de ação?](../alerts/alerts-action-rules.md)**

    Verifique no portal ao clicar no alerta acionado e veja no separador Histórico os [grupos de ações](./action-groups.md) suprimidos:

    ![Histórico de supressão de regras de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)

1. **É o tipo de ação "Email Azure Resource Manager Role"?**

    Esta ação apenas analisa as atribuições de funções do Gestor de Recursos Azure que estão no âmbito de subscrição e do tipo *Utilizador.*  Verifique se atribuiu a função ao nível da subscrição e não ao nível do recurso ou do grupo de recursos.

1. **O seu servidor de e-mail e caixa de correio estão a aceitar e-mails externos?**

    Verifique se os e-mails destes três endereços não estão bloqueados:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    É comum que listas de correio internas ou listas de distribuição bloqueiem os e-mails de endereços externos. Tem de ser permitido o correio a partir dos endereços de e-mail acima referidos.  
    Para testar, adicione um endereço de e-mail de trabalho regular (não uma lista de correio) ao grupo de ações e veja se os alertas chegam a esse e-mail.

1. **O e-mail foi processado por regras de caixa de entrada ou um filtro de spam?**

    Verifique se não existem regras da caixa de entrada que eliminem esses e-mails ou os movam para uma pasta lateral. Por exemplo, as regras da caixa de entrada podem capturar remetentes específicos ou palavras específicas no assunto.

    Além disso, verifique:

   - as definições de spam do seu cliente de e-mail (como Outlook, Gmail)
      - os limites do remetente / configurações de spam / definições de quarentena do seu servidor de e-mail (como Exchange, Microsoft 365, G-suite)
      - as definições do seu aparelho de segurança de e-mail, se houver (como Barracuda, Cisco).

1. **Não foi subscrita acidentalmente do grupo de ação?**

    Os e-mails de alerta disponibilizam uma ligação para anular a subscrição do grupo de ações. Para verificar se anulou a subscrição acidentalmente deste grupo de ações:

    1. Abra o grupo de ações no portal e verifique a coluna Estado:

    ![coluna de estado do grupo de ações](media/alerts-troubleshoot/action-group-status.png)

    2. Procure a confirmação da anulação da subscrição no e-mail:

    ![subscrição anulada do grupo de ações de alerta](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Para voltar a subscrever, utilize a ligação no e-mail de confirmação de anulação da subscrição que recebeu ou remova o endereço de e-mail do grupo de ações e, em seguida, adicione-o novamente. 
 
1. **Foi classificado limitado devido a muitos e-mails que vão para um único endereço de e-mail?**

    Cada endereço de e-mail [não pode receber](alerts-rate-limiting.md) mais de 100 e-mails por hora. Se ultrapassar este limiar, as notificações por e-mail adicionais serão removidas.  Verifique se recebeu uma mensagem a indicar que o endereço de e-mail atingiu esse limite temporariamente: 
 
   ![Taxa de e-mail limitada](media/alerts-troubleshoot/email-paused.png)

   Se quiser receber um volume elevado de notificações sem limitação de taxa, considere a possibilidade de utilizar uma ação diferente, como o webhook, a aplicação lógica, a função do Azure ou os runbooks de automatização – nenhum dos quais tem uma limitação de taxa. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Não recebeu SMS esperado, chamada de voz ou notificação push

Se vir um alerta acionado no portal, mas não tiver recebido o SMS, a chamada de voz ou a notificação push que configurou para tal, siga estes passos: 

1. **A ação foi suprimida por uma [regra de ação?](../alerts/alerts-action-rules.md)**

    Verifique no portal ao clicar no alerta acionado e veja no separador Histórico os [grupos de ações](./action-groups.md) suprimidos: 

    ![Histórico de supressão de regras de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)

   Se a supressão não tiver sido intencional, poderá modificar, desativar ou eliminar a regra de ação.
 
1. **SMS/ voz: O seu número de telefone está correto?**

   Verifique a ação de SMS para eventuais erros de digitação no código do país ou no número de telefone.
 
1. **SMS/voz: foi taxa limitada?**

    Os SMS e as chamadas de voz têm uma limitação de taxa de não mais de uma notificação de cinco em cinco minutos para cada número de telemóvel. Se ultrapassar este limiar, as notificações serão ignoradas.

      - Chamada de voz – verifique o histórico de chamadas e veja se recebeu uma chamada diferente do Azure nos cinco minutos anteriores.
      - SMS – no histórico de mensagens, procure uma mensagem a indicar que o seu número de telemóvel tem uma limitação de taxa.

    Se quiser receber um volume elevado de notificações sem limitação de taxa, considere a possibilidade de utilizar uma ação diferente, como o webhook, a aplicação lógica, a função do Azure ou os runbooks de automatização – nenhum dos quais tem uma limitação de taxa. 
 
1. **SMS: Desinscreve-se acidentalmente do grupo de ação?**

    Abra o histórico de SMS e verifique se optou por não entregar SMS deste grupo de ação específico (utilizando a resposta de action_group_short_name) ou de todos os grupos de ação (utilizando a resposta STOP). Para subscrever novamente, envie o comando SMS relevante (ATIVAR action_group_short_name ou INICIAR) ou remova a ação SMS do grupo de ações e, em seguida, adicione-a de volta.  Para obter mais informações, veja [Comportamento dos alertas de SMS em grupos de ações](alerts-sms-behavior.md).

1. **Bloqueou acidentalmente as notificações no seu telefone?**

   A maioria dos telemóveis permite bloquear chamadas ou SMS de números de telefone ou códigos curtos específicos ou permite bloquear notificações push de aplicações específicas (como a aplicação móvel do Azure). Para verificar se bloqueou acidentalmente as notificações no telemóvel, procure na documentação específica o sistema operativo e modelo do seu telemóvel, ou teste com um telemóvel e número de telefone diferentes.

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Esperava outro tipo de ação para desencadear, mas não 
   
Se vir um alerta acionado no portal, mas a ação configurada não tiver sido acionada, siga estes passos:

1. **A ação foi suprimida por uma regra de ação?**

    Verifique no portal ao clicar no alerta acionado e veja no separador Histórico os [grupos de ações](./action-groups.md) suprimidos:

    ![Histórico de supressão de regras de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)
 
    Se a supressão não tiver sido intencional, poderá modificar, desativar ou eliminar a regra de ação.

1. **Um webhook não disparou?**

    1. **Os endereços IP de origem foram bloqueados?**
    
       Adicione os [endereços IP](./action-groups.md#action-specific-information) de que o webhook é chamado para a sua lista de admissões.

    1. **O ponto final do webhook funciona corretamente?**

       Confirme que o ponto final de webhook que configurou está correto e a funcionar devidamente. Verifique os registos do webhook ou instrumente o código para que possa ser analisado (por exemplo, registe o payload de entrada).

    1. **Está a chamar o Slack ou o Microsoft Teams?**  
    Cada um destes pontos finais espera um formato JSON específico. Siga [estas instruções](../alerts/action-groups-logic-app.md) para configurar uma ação da aplicação lógica como alternativa.

    1. **O teu webhook tornou-se insío e devolveu erros?** 

        O nosso período de tempo limite para uma resposta do webhook são 10 segundos. A chamada do webhook será repetida duas vezes no máximo quando são devolvidos os códigos de estado HTTP seguintes: 408, 429, 503, 504 ou quando o ponto final HTTP não responde. A primeira repetição ocorre ao fim de 10 segundos. A segunda e última repetição ocorre ao fim de 100 segundos. Se a segunda repetição falhar, não são feitas novas tentativas de chamar o ponto final durante 30 minutos, para qualquer grupo de ações.

## <a name="action-or-notification-happened-more-than-once"></a>Ação ou notificação aconteceu mais de uma vez 

Se tiver recebido uma notificação de um alerta (por exemplo, um e-mail ou um SMS) mais de uma vez ou se a ação do alerta (por exemplo, um webhook ou uma função do Azure) tiver sido acionada várias vezes, siga estes passos: 

1. **É mesmo o mesmo alerta?** 

    Em certos casos, vários alertas semelhantes são acionados por volta da mesma hora. Assim, pode parecer que o mesmo alerta acionou as ações várias vezes. Por exemplo, uma regra de alerta do registo de atividades pode ser configurada para ser acionada quando inicia e quando termina um evento (bem sucedido ou com falha) ao não filtrar pelo campo de estado do evento. 

    Para verificar se estas ações ou notificações provêm de alertas diferentes, examine os detalhes do alerta, como o carimbo de data/hora e o ID de alerta ou o ID de correlação. Em alternativa, verifique a lista de alertas acionados no portal. Se for esse o caso, terá de adaptar a lógica da regra de alerta ou configurar a fonte de alerta. 

1. **A ação repete-se em vários grupos de ação?** 

    Quando um alerta é acionado, cada um dos grupos de ações é processado de forma independente. Por conseguinte, se uma ação (para um endereço de e-mail, por exemplo) aparecer em vários grupos de ações acionados, ela será chamada uma vez por cada grupo de ações. 

    Para verificar os grupos de ações que foram acionados, verifique o separador do histórico de alertas. Aí, poderá ver os grupos de ações definidos na regra de alerta e os grupos de ações adicionados ao alerta pelas regras de ação : 

    ![Ação repetida em múltiplos grupos de ação](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Ação ou notificação tem um conteúdo inesperado

Se tiver recebido o alerta, mas acredita que alguns dos campos estão em falta ou incorretos, siga estes passos: 

1. **Escolheste o formato correto para a ação?** 

    Cada tipo de ação (e-mail, webhook, etc.) tem dois formatos – o formato padrão, o formato legado e o [novo formato de esquema comum.](../alerts/alerts-common-schema.md) Quando criar um grupo de ação, especifique o formato que quer para a ação – diferentes ações nos grupos de ação podem ter formatos diferentes. 

    Por exemplo, para a ação webhook: 

    ![opção de esquema de ação webhook](media/alerts-troubleshoot/webhook.png)

    Verifique se o formato especificado ao nível da ação é o esperado. Por exemplo, pode ter desenvolvido um código que responde a alertas (webhook, função, aplicação lógica, etc.) e esperar um formato, mas posteriormente na ação você ou outra pessoa especifica um formato diferente.  

    Adicionalmente, verifique o formato do payload (JSON) dos [alertas de registo de atividade](../alerts/activity-log-alerts-webhook.md), dos [alertas de pesquisa de registo](../alerts/alerts-log-webhook.md) (tanto para o Application Insights como para o Log analytics), dos [alertas de métricas](alerts-metric-near-real-time.md#payload-schema), do [esquema de alertas comuns](../alerts/alerts-common-schema-definitions.md) e dos [alertas de métricas clássicas](./alerts-webhooks.md) preteridos.

 
1. **Alertas de registo de atividade: A informação está disponível no registo de atividades?** 

    [Os alertas de registo de atividade](./activity-log-alerts.md) são alertas baseados em eventos escritos para o Registo de Atividades do Azure, tais como eventos sobre a criação, atualização ou eliminação de recursos Azure, eventos de saúde de serviço e recursos, ou resultados de Azure Advisor e Azure Policy. Se tiver recebido um alerta baseado no registo de atividades, mas alguns dos campos necessários estiverem em falta ou incorretos, verifique primeiro os eventos no próprio registo de atividades. Se o recurso do Azure não tiver gravado os campos de que está à procura no evento de registo de atividades, estes campos não serão incluídos no alerta correspondente. 

## <a name="action-rule-is-not-working-as-expected"></a>A regra de ação não está a funcionar conforme o esperado 

Se vir um alerta acionado no portal, mas uma regra de ação relacionada não tiver funcionado como esperado, siga estes passos: 

1. **A regra de ação está ativada?** 

    Verifique, na coluna de estado da regra de ação, se a função de ação relacionada está ativada. 

    ![gráfico](media/alerts-troubleshoot/action-rule-status.png) 

    Se a regra de ação não estiver ativada, pode fazê-lo ao selecioná-la e ao clicar em Ativar. 

1. **É um alerta de saúde do serviço?** 

    Os alertas de estado de funcionamento de serviço (serviço de monitorização = “Service Health”) não são afetados pelas regras de ação. 

1. **A regra da ação atuou no alerta?** 

    Verifique se a regra de alerta processou o alerta ao clicar no alerta acionado no portal e observar o separador Histórico.

    Veja a seguir um exemplo de regra de ação que suprime todos os grupos de ações: 
 
     ![Histórico de supressão de regras de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)

    Veja a seguir um exemplo de uma regra de ação que adiciona outro grupo de ações:

    ![Ação repetida em múltiplos grupos de ação](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **O âmbito da regra de ação e o filtro correspondem ao alerta acionado?** 

    Se pensa que a regra de ação devia ter acionado, mas não o fez, ou que não devia ter acionado, mas o fez, examine cuidadosamente o âmbito da regra de ação e as condições do filtro ao compará-los com as propriedades do alerta acionado. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Como encontrar o alerta de um alerta de disparo

Ao abrir um caso sobre um alerta de disparo específico (como – se não recebeu a sua notificação por e-mail), terá de fornecer o ID de alerta. 

Para localizá-lo, siga estes passos:

1. No portal Azure, navegue para a lista de alertas de disparos e encontre esse alerta específico. Pode utilizar os filtros para o ajudar a localizá-lo. 

1. Clique no alerta para abrir os detalhes do alerta. 

1. Desloque-se nos campos de alerta do primeiro separador (o separador resumo) até o localizar e copie-o. Este campo também inclui um botão de ajuda "Copy to Clipboard" que pode utilizar.  

    ![encontrar iD alerta](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problemas na criação, atualização ou eliminação de regras de ação no portal Azure

Se tiver recebido um erro ao tentar criar, atualizar ou eliminar uma [regra de ação,](../alerts/alerts-action-rules.md)siga estes passos: 

1. **Recebeu um erro de permissão?**  

    Deve ter o [papel de Monitoring Contributor incorporado,](../../role-based-access-control/built-in-roles.md#monitoring-contributor)ou as permissões específicas relacionadas com regras de ação e alertas.

1. **Verificou os parâmetros da regra de ação?**  

    Verifique a documentação da [regra de ação,](../alerts/alerts-action-rules.md)ou a regra de [ação PowerShell Set-AzActionRule](/powershell/module/az.alertsmanagement/set-azactionrule) command. 


## <a name="next-steps"></a>Passos seguintes
- Se utilizar um alerta de registo, consulte também [alertas de registo de resolução de problemas](./alerts-troubleshoot-log.md).
- Volte ao [portal Azure](https://portal.azure.com) para verificar se resolveu o seu problema com orientação acima