---
title: Solucionar os testes de disponibilidade do Aplicativo Azure insights | Microsoft Docs
description: Solucionar problemas de testes na Web no Aplicativo Azure insights. Receber alertas se um site ficar indisponível ou responder lentamente.
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
ms.openlocfilehash: c3f3d9437a6e796cc91ff1782b3a0774382c5f8b
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067064"
---
# <a name="troubleshooting"></a>Resolução de problemas

Este artigo o ajudará a solucionar problemas comuns que podem ocorrer ao usar o monitoramento de disponibilidade.

## <a name="ssltls-errors"></a>Erros de SSL/TLS

|Sintoma/mensagem de erro| Causas possíveis|
|--------|------|
|Não foi possível criar o canal seguro SSL/TLS  | Versão do SSL. Somente há suporte para TLS 1,0, 1,1 e 1,2. **Não há suporte para SSLv3.**
|Camada de registro do TLSv 1.2: Alerta (nível: Fatal, descrição: MAC de registro inadequado)| Consulte thread do StackExchange para obter [mais informações](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|A URL que está falhando é para uma CDN (rede de distribuição de conteúdo) | Isso pode ser causado por uma configuração incorreta na CDN |  

### <a name="possible-workaround"></a>Possível solução alternativa

* Se as URLs que estão enfrentando o problema forem sempre de recursos dependentes, é recomendável desabilitar a **análise de solicitações dependentes** para o teste na Web.

## <a name="test-fails-only-from-certain-locations"></a>O teste falha apenas de determinados locais

|Sintoma/mensagem de erro| Causas possíveis|
|----|---------|
|Uma tentativa de conexão falhou porque a parte conectada não respondeu corretamente após um período de tempo  | Os agentes de teste em determinados locais estão sendo bloqueados por um firewall.|
|    |O redirecionamento de determinados endereços IP está ocorrendo via (balanceadores de carga, gerenciadores de tráfego geográficos, rota expressa do Azure). 
|    |Se estiver usando o Azure ExpressRoute, há cenários em que os pacotes podem ser descartados em casos em que o [Roteamento Assimétrico ocorre](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Falha de teste intermitente com um erro de violação de protocolo

|Sintoma/mensagem de erro| Causas possíveis|
|----|---------|
a violação de protocolo CR deve ser seguida por LF | Isso ocorre quando cabeçalhos malformados são detectados. Especificamente, alguns cabeçalhos podem não estar usando CRLF para indicar o fim da linha, que viola a especificação HTTP e, portanto, falhará na validação no nível de WebRequest do .NET.
 || Isso também pode ser causado por balanceadores de carga ou CDNs.

> [!NOTE]
> A URL pode não falhar em navegadores que têm uma validação reduzida de cabeçalhos HTTP. Veja esta mensagem de blogue para uma explicação detalhada deste problema: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Perguntas de resolução de problemas comuns

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>O site parece ok, mas eu vejo falhas de teste? Por que Application Insights alertar-me?

   * Seu teste tem **solicitações dependentes de análise** habilitadas? Isso resulta em uma verificação estrita de recursos como scripts, imagens, etc. Esses tipos de falhas podem não ser perceptíveis em um navegador. Verifique todas as imagens, scripts, folhas de estilo e outros ficheiros carregados pela página. Se qualquer um deles falhar, o teste será relatado como com falha, mesmo que a página HTML principal seja carregada sem problema. Para dessensibilizar o teste para essas falhas de recurso, basta desmarcar as solicitações de análise dependentes da configuração de teste.

   * Para reduzir as chances de ruído de blips de rede transitórias, etc., certifique-se de que habilitar novas tentativas para a configuração de falhas de teste esteja marcada. Você também pode testar de mais locais e gerenciar o limite da regra de alerta de forma adequada para evitar problemas específicos do local, causando alertas indevidos.

   * Clique em qualquer um dos pontos vermelhos da experiência de disponibilidade ou em qualquer falha de disponibilidade do Gerenciador de pesquisa para ver os detalhes do motivo pelo qual relatamos a falha. O resultado do teste, juntamente com a telemetria correlacionada do lado do servidor (se habilitada), deve ajudar a entender por que o teste falhou. Causas comuns de problemas transitórios são problemas de rede ou conexão.

   * O tempo limite do teste foi atingido? Anulamos testes após 2 minutos. Se o seu teste de ping ou de várias etapas demorar mais de 2 minutos, relataremos como uma falha. Considere dividir o teste em vários que podem ser concluídos em durações menores.

   * Todos os locais relataram falha ou apenas alguns deles? Se apenas algumas falhas relatadas, isso pode ser devido a problemas de rede/CDN. Novamente, clicar nos pontos vermelhos deve ajudar a entender por que o local relatou falhas.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Eu não recebi um email quando o alerta foi disparado ou resolvido ou ambos?

Verifique a configuração de alertas clássicos para confirmar se seu email está listado diretamente ou se uma lista de distribuição em que você está está configurada para receber notificações. Se for, verifique a configuração da lista de distribuição para confirmar se ela pode receber emails externos. Verifique também se o administrador de email pode ter qualquer política configurada que possa causar esse problema.

### <a name="i-did-not-receive-the-webhook-notification"></a>Eu não recebi a notificação de webhook?

Verifique se o aplicativo que está recebendo a notificação de webhook está disponível e processa com êxito as solicitações de webhook. Consulte [isso](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) para obter mais informações.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Falha de teste intermitente com um erro de violação de protocolo?

O erro ("violação do protocolo.. CR tem de ser seguido por LF") indica um problema com o servidor (ou dependências). Isto acontece quando há cabeçalhos mal formados estão definidos na resposta. Pode ser causado por balanceadores de carga ou CDNs. Especificamente, alguns cabeçalhos podem não estar usando CRLF para indicar o fim da linha, que viola a especificação HTTP e, portanto, falha na validação no nível de WebRequest do .NET. Inspecione a resposta para os cabeçalhos de spot, que podem estar em violação.

> [!NOTE]
> A URL pode não falhar em navegadores que têm uma validação reduzida de cabeçalhos HTTP. Veja esta mensagem de blogue para uma explicação detalhada deste problema: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Não vejo nenhuma telemetria relacionada no lado do servidor para diagnosticar falhas de teste? *

Se tiver o Application Insights configurado para a sua aplicação do lado do servidor, poderá dever-se ao facto de a [amostragem](../../azure-monitor/app/sampling.md) estar em curso. Selecione um resultado de disponibilidade diferente.

### <a name="can-i-call-code-from-my-web-test"></a>Posso chamar um código a partir do meu teste Web?

Não. Os passos do teste devem estar no ficheiro .webtest. E não pode chamar outros testes Web nem utilizar ciclos. No entanto, existem vários plug-ins que poderão ser úteis.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Existe uma diferença entre “testes Web” e “testes de disponibilidade”?

Ambos os termos podem ser utilizados alternadamente. “Testes de disponibilidade” é um termo mais genérico que inclui os testes de ping de URL individuais, para além dos testes Web de vários passos.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Gostaria de utilizar testes de disponibilidade no nosso servidor interno, que é executado protegido por uma firewall.

   Existem duas soluções possíveis:

   * Configure a firewall para permitir pedidos recebidos a partir dos [endereços IP dos nossos agentes de teste Web](../../azure-monitor/app/ip-addresses.md).
   * Escreva o seu próprio código para testar periodicamente o seu servidor interno. Execute o código como um processo em segundo plano num servidor de teste atrás da firewall. O processo de teste pode enviar os resultados para o Application Insights através da API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) no pacote SDK core. Isto requer que o servidor de teste tenha acesso de envio para o ponto final de ingestão do Application Insights, mas é um risco de segurança muito inferior do que a alternativa de permitir pedidos recebidos. Os resultados aparecerão nas folhas de testes da Web de disponibilidade, embora a experiência seja ligeiramente simplificada do que está disponível para testes criados por meio do Portal. Os testes de disponibilidade personalizados também serão exibidos como resultados de disponibilidade em análises, pesquisa e métricas.

### <a name="uploading-a-multi-step-web-test-fails"></a>O carregamento de um teste Web com vários passos falhou

Alguns motivos podem acontecer:
   * Existe um limite de tamanho de 300 K.
   * Os ciclos não são suportados.
   * As referências a outros testes Web não são suportadas.
   * As origens de dados não são suportadas.

### <a name="my-multi-step-test-doesnt-complete"></a>O meu teste com vários passos não é concluído

Existe um limite de 100 pedidos por teste. Além disso, o teste será interrompido se for executado por mais de dois minutos.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Como executar um teste com certificados de cliente?

Não há suporte para isso no momento.

## <a name="who-receives-the-classic-alert-notifications"></a>Quem recebe as notificações de alerta (clássico)?

Esta seção se aplica somente a alertas clássicos e ajudará você a otimizar suas notificações de alerta para garantir que somente os destinatários desejados recebam notificações. Para entender mais sobre a diferença entre os [alertas clássicos](../platform/alerts-classic.overview.md)e a nova experiência de alertas, consulte o [artigo Visão geral de alertas](../platform/alerts-overview.md). Para controlar a notificação de alerta na nova experiência de alertas, use [grupos de ação](../platform/action-groups.md).

* Recomendamos o uso de destinatários específicos para notificações de alerta clássicas.

* Para alertas sobre falhas de X fora dos locais Y, a opção de caixa de seleção **em massa/grupo** , se habilitada, envia aos usuários com funções de administrador/coadministrador.  Essencialmente, _todos_ os administradores da _assinatura_ receberão notificações.

* Para alertas sobre as métricas de disponibilidade, a opção de caixa de seleção **em massa/grupo** , se habilitada, enviará aos usuários com funções de leitor, colaborador ou proprietário na assinatura. Na verdade, _todos_ os usuários com acesso à assinatura o recurso Application insights estão no escopo e receberão notificações. 

> [!NOTE]
> Se você usar a opção de caixa de seleção **Bulk/Group** no momento e desabilitá-la, não será possível reverter a alteração.

Use a nova experiência de alerta/alertas quase em tempo real se você precisar notificar os usuários com base em suas funções. Com os [grupos de ação](../platform/action-groups.md), você pode configurar notificações por email para os usuários com qualquer uma das funções colaborador/proprietário/leitor (não combinadas como uma única opção).

## <a name="next-steps"></a>Passos Seguintes

* [Teste na Web de várias etapas](availability-multistep.md)
* [Testes de ping de URL](monitor-web-app-availability.md)
