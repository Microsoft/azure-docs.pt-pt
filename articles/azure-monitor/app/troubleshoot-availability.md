---
title: Resolução de problemas nos seus testes de disponibilidade de aplicações Azure
description: Testes web de resolução de problemas em Azure Application Insights. Receber alertas se um site ficar indisponível ou responder lentamente.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/19/2020
ms.reviewer: sdash
ms.openlocfilehash: 849701caf73a4fb289773c67bccaab2e0f39dbd1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583732"
---
# <a name="troubleshooting"></a>Resolução de problemas

Este artigo irá ajudá-lo a resolver problemas comuns que podem ocorrer quando se utiliza a monitorização da disponibilidade.

## <a name="troubleshooting-report-steps-for-ping-tests"></a>Etapas de relatório de resolução de problemas para testes de ping

O Relatório de Resolução de Problemas permite-lhe diagnosticar facilmente problemas comuns que fazem com que os seus **testes** de ping falhem.

![Animação de navegar a partir do separador de disponibilidade selecionando uma falha nos detalhes de transação de ponta a ponta para ver o relatório de resolução de problemas](./media/troubleshoot-availability/availability-to-troubleshooter.gif)

1. No separador de disponibilidade do seu recurso Application Insights, selecione globalmente ou um dos testes de disponibilidade.
2. Selecione **Falhou** e, em seguida, um teste em "Drill into" à esquerda ou selecione um dos pontos no enredo de dispersão.
3. Na página de detalhes de transação de ponta a ponta, selecione um evento e, em seguida, em "Resumo do relatório de resolução de problemas" selecione **[Vá a passo]** para ver o relatório de resolução de problemas.

> [!NOTE]
>  Se o passo de reutilização da ligação estiver presente, então a resolução do DNS, o estabelecimento de ligação e as etapas de transporte TLS não estarão presentes.

|Passo | Mensagem de erro | Causa possível |
|-----|---------------|----------------|
| Reutilização de ligação | n/a | Normalmente dependente de uma ligação previamente estabelecida, o que significa que o passo de teste web é dependente. Portanto, não seria necessário um dSNS, ligação ou passo SSL. |
| Resolução de DNS | O nome remoto não podia ser resolvido: "o seu URL" | O processo de resolução do DNS falhou, muito provavelmente devido a registos DNS mal configurados ou falhas temporárias do servidor DNS. |
| Estabelecimento de conexão | Uma tentativa de ligação falhou porque a parte ligada não respondeu corretamente após um período de tempo. | Em geral, significa que o seu servidor não está a responder ao pedido HTTP. Uma causa comum é que os nossos agentes de teste estão a ser bloqueados por uma firewall no seu servidor. Se quiser testar dentro de uma Rede Virtual Azure, deve adicionar a etiqueta de serviço Disponibilidade ao seu ambiente.|
| Transporte TLS  | O cliente e o servidor não podem comunicar porque não possuem um algoritmo comum.| Apenas os TLS 1.0, 1.1 e 1.2 são suportados. SSL não é suportado. Este passo não valida os certificados SSL e apenas estabelece uma ligação segura. Este passo só aparece quando ocorrer um erro. |
| Cabeçalho de resposta de receção | Não é possível ler os dados da ligação de transporte. A ligação estava fechada. | O seu servidor cometeu um erro de protocolo no cabeçalho de resposta. Por exemplo, a ligação fechada pelo seu servidor quando a resposta não está totalmente. |
| Corpo de resposta recebendo | Não foi possível ler os dados da ligação de transporte: A ligação foi fechada. | O seu servidor cometeu um erro de protocolo no corpo de resposta. Por exemplo, Ligação fechada pelo seu servidor quando a resposta não é totalmente lida ou o tamanho do pedaço está errado no corpo de resposta em pedaços. |
| Redirecionar validação de limites | Esta página tem muitos redirecionamentos. Este ciclo será encerrado aqui uma vez que este pedido excedeu o limite para redirecionamentos automáticos. | Há um limite de 10 redirecionamentos por teste. |
| Validação do código de estado | `200 - OK` não corresponde ao estado esperado `400 - BadRequest` . | O código de estado devolvido que é contado como um sucesso. 200 é o código que indica que foi devolvida uma página Web normal. |
| Validação de conteúdos | O texto requerido "olá" não apareceu na resposta. | A corda não é uma combinação exata sensível a casos na resposta, por exemplo a cadeia "Bem-vindo!". Deve ser uma corda simples, sem caracteres wildcard (por exemplo, um asterisco). Se o conteúdo da sua página mudar, poderá ter de atualizar a cadeia. Apenas os caracteres ingleses são suportados com correspondência de conteúdo. |
  
## <a name="common-troubleshooting-questions"></a>Questões comuns de resolução de problemas

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>O site está okay mas vejo as falhas de teste? Porque é que a Application Insights está a alertar-me?

   * O seu teste tem **pedidos dependentes da Parse** ativados? Isso resulta numa verificação rigorosa de recursos como scripts, imagens, etc. Este tipo de falhas pode não ser percetível num browser. Verifique todas as imagens, scripts, folhas de estilo e outros ficheiros carregados pela página. Se algum deles falhar, o teste é reportado como falhado, mesmo que a página principal html carregue sem problemas. Para dessensibilizar o teste a tais falhas de recursos, basta desmarcar os Pedidos Dependentes de Parse da configuração do teste.

   * Para reduzir as probabilidades de ruído de blips de rede transitórias, etc., certifique-se de que é verificada a configuração de tentativas de falha de teste. Também pode testar a partir de mais locais e gerir o limiar da regra de alerta de acordo para evitar problemas específicos da localização que causem alertas indevidos.

   * Clique em qualquer um dos pontos vermelhos da experiência de experiência do enredo de dispersão de disponibilidade, ou qualquer falha de disponibilidade do explorador de Pesquisa para ver os detalhes do porquê de termos relatado a falha. O resultado do teste, juntamente com a telemetria correlacionada do lado do servidor (se ativado) devem ajudar a entender por que o teste falhou. As causas comuns de problemas transitórios são questões de rede ou de ligação.

   * O tempo de 200 testes foi para o intervalo? Abortamos os testes após 2 minutos. Se o seu teste de ping ou várias etapas demorar mais de 2 minutos, vamos denunciá-lo como uma falha. Considere quebrar o teste em vários que podem completar em durações mais curtas.

   * Todos os locais reportaram falhas, ou apenas alguns deles? Se apenas algumas falhas reportadas, pode ser devido a problemas de rede/CDN. Mais uma vez, clicar nos pontos vermelhos deve ajudar a entender por que a localização reportou falhas.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Não recebi um e-mail quando o alerta foi disparado, ou resolvido ou ambos?

Consulte a configuração de alertas clássicos para confirmar que o seu e-mail está listado diretamente, ou uma lista de distribuição em que está está configurada para receber notificações. Se for, verifique a configuração da lista de distribuição para confirmar que pode receber e-mails externos. Verifique também se o seu administrador de correio pode ter alguma política configurada que possa causar este problema.

### <a name="i-did-not-receive-the-webhook-notification"></a>Não recebi a notificação do webhook?

Verifique se a aplicação que recebe a notificação do webhook está disponível e processa com sucesso os pedidos do webhook. Consulte [isto](../alerts/alerts-log-webhook.md) para mais informações.

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>Estou a receber 403 erros proibidos, o que significa isto?

Este erro indica que é necessário adicionar exceções de firewall para permitir que os agentes de disponibilidade testem o url do seu teste de destino. Para obter uma lista completa dos endereços IP do agente para permitir, consulte o [artigo de exceção IP](./ip-addresses.md#availability-tests).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Falha de teste intermitente com um erro de violação do protocolo?

O erro ("violação do protocolo.. CR tem de ser seguido por LF") indica um problema com o servidor (ou dependências). Isto acontece quando há cabeçalhos mal formados estão definidos na resposta. Pode ser causado por balanceadores de carga ou CDNs. Especificamente, alguns cabeçalhos podem não estar a usar o CRLF para indicar o fim da linha, o que viola a especificação HTTP e, portanto, falha na validação ao nível .NET WebRequest. Inspecione a resposta aos cabeçalhos, que podem estar em violação.

> [!NOTE]
> O URL pode não falhar nos navegadores que tenham uma validação descontraída dos cabeçalhos HTTP. Veja esta mensagem de blogue para uma explicação detalhada deste problema: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Não vejo nenhuma telemetria relacionada do lado do servidor para diagnosticar falhas de teste?*

Se tiver o Application Insights configurado para a sua aplicação do lado do servidor, poderá dever-se ao facto de a [amostragem](./sampling.md) estar em curso. Selecione um resultado de disponibilidade diferente.

### <a name="can-i-call-code-from-my-web-test"></a>Posso chamar um código a partir do meu teste Web?

N.º Os passos do teste devem estar no ficheiro .webtest. E não pode chamar outros testes Web nem utilizar ciclos. No entanto, existem vários plug-ins que poderão ser úteis.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Existe uma diferença entre “testes Web” e “testes de disponibilidade”?

Ambos os termos podem ser utilizados alternadamente. “Testes de disponibilidade” é um termo mais genérico que inclui os testes de ping de URL individuais, para além dos testes Web de vários passos.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Gostaria de utilizar testes de disponibilidade no nosso servidor interno, que é executado protegido por uma firewall.

   Existem duas soluções possíveis:

   * Configure a firewall para permitir pedidos recebidos a partir dos [endereços IP dos nossos agentes de teste Web](./ip-addresses.md).
   * Escreva o seu próprio código para testar periodicamente o seu servidor interno. Execute o código como um processo em segundo plano num servidor de teste atrás da firewall. O processo de teste pode enviar os resultados para o Application Insights através da API [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) no pacote SDK core. Isto requer que o servidor de teste tenha acesso de envio para o ponto final de ingestão do Application Insights, mas é um risco de segurança muito inferior do que a alternativa de permitir pedidos recebidos. Os resultados aparecerão nas lâminas de testes web de disponibilidade, embora a experiência seja ligeiramente simplificada a partir do que está disponível para testes criados através do portal. Os testes de disponibilidade personalizada também aparecerão como resultados de disponibilidade em Analytics, Search e Metrics.

### <a name="uploading-a-multi-step-web-test-fails"></a>O carregamento de um teste Web com vários passos falhou

Algumas razões para isto pode acontecer:
   * Existe um limite de tamanho de 300 K.
   * Os ciclos não são suportados.
   * As referências a outros testes Web não são suportadas.
   * As origens de dados não são suportadas.

### <a name="my-multi-step-test-doesnt-complete"></a>O meu teste com vários passos não é concluído

Existe um limite de 100 pedidos por teste. Além disso, o teste é interrompido se for mais de dois minutos.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Como executar um teste com certificados de cliente?

Atualmente, isto não é apoiado.

## <a name="who-receives-the-classic-alert-notifications"></a>Quem recebe as notificações de alerta (clássicas)?

Esta secção aplica-se apenas a alertas clássicos e irá ajudá-lo a otimizar as suas notificações de alerta para garantir que apenas os seus destinatários pretendidos recebem notificações. Para saber mais sobre a diferença entre [alertas clássicos](../alerts/alerts-classic.overview.md)e a experiência dos novos alertas consulte o artigo Visão geral dos [alertas.](../alerts/alerts-overview.md) Para controlar a notificação de alerta nos novos alertas, utilize [grupos de ação](../alerts/action-groups.md).

* Recomendamos a utilização de destinatários específicos para notificações de alerta clássicas.

* Para alertas sobre falhas de X fora de locais Y, a opção de caixa de **verificação a granel/grupo,** se ativada, envia para utilizadores com funções de administração/coadministração.  _Essencialmente, todos os_ administradores da _subscrição_ receberão notificações.

* Para alertas sobre métricas de disponibilidade, a opção de caixa de **verificação a granel/grupo,** se ativada, envia para os utilizadores com funções de proprietário, colaborador ou leitor na subscrição. Com efeito, _todos os_ utilizadores com acesso à subscrição o recurso Application Insights estão no âmbito e receberão notificações. 

> [!NOTE]
> Se utilizar atualmente a opção caixa de **verificação a granel/grupo** e desativá-la, não poderá reverter a alteração.

Utilize os novos alertas de experiência de alerta/quase em tempo real se necessitar de notificar os utilizadores com base nas suas funções. Com [grupos de ação,](../alerts/action-groups.md)pode configurar notificações de e-mail para os utilizadores com qualquer uma das funções de contribuinte/proprietário/leitor (não combinados como uma única opção).

## <a name="next-steps"></a>Passos seguintes

* [Testes web em várias etapas](availability-multistep.md)
* [Testes de ping de URL](monitor-web-app-availability.md)