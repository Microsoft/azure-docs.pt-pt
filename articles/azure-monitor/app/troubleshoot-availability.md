---
title: Resolver problemas de seus testes de disponibilidade do Application Insights do Azure | Documentos da Microsoft
description: Resolver problemas de testes da web no Azure Application Insights. Receber alertas se um site ficar indisponível ou responder lentamente.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 87bc87d7d105d581f0143e87044fb0337c0fd7f6
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304961"
---
# <a name="troubleshooting"></a>Resolução de problemas

Este artigo ajuda a resolver problemas comuns que podem ocorrer ao utilizar a monitorização de disponibilidade.

## <a name="ssltls-errors"></a>Erros SSL/TLS

|Mensagem de erro/sintoma| Causas possíveis|
|--------|------|
|Não foi possível criar o canal seguro do SSL/TLS  | Versão SSL. São suportados apenas TLS 1.0, 1.1 e 1.2. **Não é suportado o SSLv3.**
|Camada de registo TLSv1.2: Alerta (nível: Fatal, Descrição: Bad Record MAC)| Consulte StackExchange thread para [mais informações](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|URL que está a falhar é uma CDN (rede de entrega de conteúdos) | Isto pode ser causado por uma configuração incorreta na sua CDN |  

### <a name="possible-workaround"></a>Solução possível

* Se os URLs que o problema são sempre para recursos dependentes, é recomendável desativar **analisar pedidos dependentes** para o teste da web.

## <a name="test-fails-only-from-certain-locations"></a>Falha de teste apenas a partir de determinadas localizações

|Mensagem de erro/sintoma| Causas possíveis|
|----|---------|
|Uma tentativa de ligação falhou porque a entidade ligada não respondeu adequadamente após um período de tempo  | Agentes de teste em determinadas localizações estão a ser bloqueados por uma firewall.|
|    |Redirecionamento de determinados endereços IP que está a ocorrer através de (balanceadores de carga, os gestores de tráfego geográfico, Express Route do Azure.) 
|    |Se utilizar o Azure ExpressRoute, existem cenários onde os pacotes podem ser ignorados em casos em que [encaminhamento assimétrico ocorre](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Falha de teste intermitente com um erro de violação do protocolo

|Mensagem de erro/sintoma| Causas possíveis|
|----|---------|
CR de violação do protocolo deve ser seguido por LF | Isto ocorre quando são detetados cabeçalhos mal formados. Especificamente, alguns cabeçalhos poderão não estar a utilizar CRLF para indicar o fim da linha, que viola a especificação de HTTP e, portanto, falhará validação a nível de .NET WebRequest.
 || Também pode ser causado por balanceadores de carga ou CDNs.

> [!NOTE]
> O URL não pode falhar em browsers que tenham uma validação simples de cabeçalhos HTTP. Veja esta mensagem de blogue para uma explicação detalhada deste problema: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Perguntas de resolução de problemas comuns

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Site está okay mas vejo falhas de teste? Por que motivo é que o Application Insights-me é alertas?

   * O seu teste tem **analisar pedidos dependentes** ativada? Que resulta numa verificação rigorosa nos recursos, como scripts, imagens, etc. Esses tipos de falhas podem não ser evidentes num navegador. Verifique todas as imagens, scripts, folhas de estilo e outros ficheiros carregados pela página. Se qualquer um deles falhar, o teste é reportado como falhado, mesmo se página principal HTML carrega sem problemas. Para dessensibilizar o teste para tais falhas recursos, basta desmarcar a analisar pedidos dependentes da configuração do teste.

   * Para reduzir as probabilidades de ruído de blips de rede transitórios etc., certifique-se ativar tentativas para falhas de teste de configuração está marcada. Também pode testar a partir de mais localizações e gerir o limiar de regra de alerta em conformidade para evitar problemas específicos da localização que causam alertas indevidos.

   * Clique em qualquer um dos vermelhos a experiência de disponibilidade do ou qualquer falha de disponibilidade a partir do Explorador de pesquisa para ver os detalhes de por que estamos reportou que a falha. O resultado do teste, juntamente com a telemetria correlacionada do lado do servidor (se ativada) deve ajudar a compreender o motivo pelo qual o teste falhou. Causas comuns de problemas transitórios problemas de rede ou a ligação.

   * Foi o limite de tempo de teste? Podemos abortar testes depois de 2 minutos. Se o ping ou o teste com vários passos demora mais de 2 minutos, vamos relatá-lo como uma falha. Considere dividir o teste em vários aqueles que podem ser concluídas em durações mais curtas.

   * Todas as localizações comunicaram falha, ou apenas alguns deles? Se apenas algumas comunicadas falhas, pode ser devido a problemas de rede de CDN. Novamente, ao clicar nos pontos vermelhos deve ajudar a compreender por que a localização comunicadas falhas.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Eu não recebeu uma mensagem de e-mail quando o alerta acionado ou resolvido ou ambos?

Verifique a configuração de alertas clássicos para confirmar o seu e-mail é diretamente aparece na lista ou uma lista de distribuição que estiver a utilizar está configurada para receber notificações. Se for, em seguida, verifique a configuração de lista de distribuição para confirmar a pode receber mensagens de e-mail externas. Também pode verificar se o seu administrador de correio pode ter qualquer políticas configuradas, que podem fazer com que este problema.

### <a name="i-did-not-receive-the-webhook-notification"></a>Eu não recebeu a notificação de webhook?

Verifique se o aplicativo receber a notificação de webhook está disponível e processa com êxito os pedidos de webhook. Ver [isso](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) para obter mais informações.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Falha de teste intermitente com um erro de violação do protocolo?

O erro ("violação do protocolo.. CR tem de ser seguido por LF") indica um problema com o servidor (ou dependências). Isto acontece quando há cabeçalhos mal formados estão definidos na resposta. Pode ser causado por balanceadores de carga ou CDNs. Especificamente, alguns cabeçalhos poderão não estar a utilizar CRLF para indicar o fim da linha, que viola a especificação de HTTP e, portanto, a falha de validação a nível de .NET WebRequest. Inspecione a resposta a cabeçalhos spot, que poderá estar em violação.

> [!NOTE]
> O URL não pode falhar em browsers que tenham uma validação simples de cabeçalhos HTTP. Veja esta mensagem de blogue para uma explicação detalhada deste problema: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Não vejo qualquer relacionados com a telemetria de lado do servidor para diagnosticar falhas de teste? *

Se tiver o Application Insights configurado para a sua aplicação do lado do servidor, poderá dever-se ao facto de a [amostragem](../../azure-monitor/app/sampling.md) estar em curso. Selecione um resultado de disponibilidade diferente.

### <a name="can-i-call-code-from-my-web-test"></a>Posso chamar um código a partir do meu teste Web?

Não. Os passos do teste devem estar no ficheiro .webtest. E não pode chamar outros testes Web nem utilizar ciclos. No entanto, existem vários plug-ins que poderão ser úteis.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Existe uma diferença entre “testes Web” e “testes de disponibilidade”?

Ambos os termos podem ser utilizados alternadamente. “Testes de disponibilidade” é um termo mais genérico que inclui os testes de ping de URL individuais, para além dos testes Web de vários passos.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Gostaria de utilizar testes de disponibilidade no nosso servidor interno, que é executado protegido por uma firewall.

   Existem duas soluções possíveis:

   * Configure a firewall para permitir pedidos recebidos a partir dos [endereços IP dos nossos agentes de teste Web](../../azure-monitor/app/ip-addresses.md).
   * Escreva o seu próprio código para testar periodicamente o seu servidor interno. Execute o código como um processo em segundo plano num servidor de teste atrás da firewall. O processo de teste pode enviar os resultados para o Application Insights através da API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) no pacote SDK core. Isto requer que o servidor de teste tenha acesso de envio para o ponto final de ingestão do Application Insights, mas é um risco de segurança muito inferior do que a alternativa de permitir pedidos recebidos. Os resultados não aparecem nos painéis de testes Web de disponibilidade, mas aparecem como resultados de disponibilidade no Analytics, no Search e no Explorador de Métricas.

### <a name="uploading-a-multi-step-web-test-fails"></a>O carregamento de um teste Web com vários passos falhou

Alguns motivos pelos quais que isto pode acontecer:
   * Existe um limite de tamanho de 300 K.
   * Os ciclos não são suportados.
   * As referências a outros testes Web não são suportadas.
   * As origens de dados não são suportadas.

### <a name="my-multi-step-test-doesnt-complete"></a>O meu teste com vários passos não é concluído

Existe um limite de 100 pedidos por teste. Além disso, o teste é interrompido se for executado durante mais de dois minutos.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Como executar um teste com certificados de cliente?

Isso não é atualmente suportado.

## <a name="who-receives-the-classic-alert-notifications"></a>Quem receberá notificações de alerta (clássicas)?

Esta secção apenas se aplica a alertas clássicos e irá ajudá-lo a otimizar suas notificações de alerta para garantir que apenas os destinatários desejados recebem notificações. Para saber mais sobre a diferença entre [alertas clássicos](../platform/alerts-classic.overview.md)e a nova experiência de alertas, consulte a [artigo de descrição geral de alertas](../platform/alerts-overview.md). Para controlar o alerta de notificação no novos alertas experiência de utilização [grupos de ação](../platform/action-groups.md).

* Recomendamos a utilização de destinatários específicos para as notificações de alerta clássicas.

* Para alertas em falhas de X fora de localizações de Y, o **em massa/grupo** opção de caixa de verificação, se estiver ativada, envia para os utilizadores com funções de administrador/coadministrador.  Essencialmente _todos os_ administradores da _subscrição_ irão receber notificações.

* Para alertas em métricas de disponibilidade do **em massa/grupo** opção de caixa de verificação se estiver ativada, envia para os utilizadores com funções de proprietário, contribuinte ou leitor na subscrição. Na verdade, _todos os_ os utilizadores com acesso à subscrição do recurso do Application Insights estão no âmbito e irá receber notificações. 

> [!NOTE]
> Se utilizar atualmente o **em massa/grupo** opção de caixa de verificação e desativá-la, não será capaz de reverter a alteração.

Utilize os nova experiência/perto-realtime os alertas do alerta se tiver de notificar os utilizadores com base nas suas funções. Com o [grupos de ação](../platform/action-groups.md), pode configurar notificações por e-mail aos utilizadores com qualquer uma das funções Contribuidor/proprietário/leitor (não combinadas em conjunto como uma única opção).

## <a name="next-steps"></a>Passos Seguintes

* [Testes web de vários passos](availability-multistep.md)
* [Testes de ping do URL](monitor-web-app-availability.md)
