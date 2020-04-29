---
title: Monitorização do ponto final do Gestor de Tráfego azure [ Microsoft Docs
description: Este artigo pode ajudá-lo a entender como o Traffic Manager utiliza a monitorização de pontos finais e falha automática do ponto final para ajudar os clientes azure a implementar aplicações de alta disponibilidade
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: rohink
ms.openlocfilehash: 61aafbe8cb12e93d72f5efd01155f06fb3ec0c28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80757272"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitorização do ponto final do Gestor de Tráfego

O Azure Traffic Manager inclui monitorização de ponto final incorporado e falha automática do ponto final. Esta funcionalidade ajuda-o a fornecer aplicações de alta disponibilidade que são resistentes a falhas de pontofinal, incluindo falhas na região de Azure.

## <a name="configure-endpoint-monitoring"></a>Configurar a monitorização do ponto final

Para configurar a monitorização do ponto final, deve especificar as seguintes definições no perfil do Gestor de Tráfego:

* **Protocolo**. Escolha HTTP, HTTPS ou TCP como o protocolo que o Gestor de Tráfego utiliza ao sondar o seu ponto final para verificar a sua saúde. A monitorização HTTPS não verifica se o seu certificado TLS/SSL é válido - apenas verifica se o certificado está presente.
* **Porto.** Escolha a porta utilizada para o pedido.
* **Caminho.** Esta definição de configuração é válida apenas para os protocolos HTTP e HTTPS, para os quais é necessária a definição do caminho. A disponibilização desta definição para o protocolo de monitorização do TCP resulta num erro. Para o protocolo HTTP e HTTPS, dê o caminho relativo e o nome da página web ou o ficheiro a que acede o monitor. Um corte para a frente (/) é uma entrada válida para o caminho relativo. Este valor implica que o ficheiro está no diretório raiz (padrão).
* **Definições personalizadas do cabeçalho** Esta definição de configuração ajuda-o a adicionar cabeçalhos HTTP específicos aos controlos de saúde que o Gestor de Tráfego envia para pontos finais sob um perfil. Os cabeçalhos personalizados podem ser especificados a um nível de perfil a ser aplicável para todos os pontos finais desse perfil e/ou a um nível final aplicável apenas a esse ponto final. Você pode usar cabeçalhos personalizados para ter verificações de saúde em pontos finais em um ambiente multi-inquilino ser encaminhado corretamente para o seu destino, especificando um cabeçalho anfitrião. Também pode utilizar esta definição adicionando cabeçalhos únicos que podem ser usados para identificar pedidos http(S) originados do Gestor de Tráfego e processa-os de forma diferente. Pode especificar até oito cabeçalhos: pares de valor costurados por uma vírposta. Por exemplo, "header1:value1,header2:value2". 
* **Intervalos de código de estado esperados** Esta definição permite especificar várias gamas de códigos de sucesso no formato 200-299, 301-301. Se estes códigos de estado forem recebidos como resposta a partir de um ponto final quando um exame de saúde é iniciado, o Gestor de Tráfego marca esses pontos finais como saudáveis. Pode especificar um máximo de 8 intervalos de código de estado. Esta definição é aplicável apenas ao protocolo HTTP e HTTPS e a todos os pontos finais. Esta definição encontra-se ao nível do perfil do Gestor de Tráfego e, por padrão, o valor 200 é definido como o código de estado de sucesso.
* **Intervalo de sondagem.** Este valor especifica a frequência com que um ponto final é verificado para a sua saúde a partir de um agente de sondagem do Gestor de Tráfego. Pode especificar dois valores aqui: 30 segundos (sondagem normal) e 10 segundos (sondagem rápida). Se não forem fornecidos valores, o perfil fixa-se num valor predefinido de 30 segundos. Visite a página de [Preços do Gestor](https://azure.microsoft.com/pricing/details/traffic-manager) de Tráfego para saber mais sobre preços de sondagem rápida.
* **Número tolerado de falhas.** Este valor especifica quantas falhas um agente de sondagem do Traffic Manager tolera antes de marcar esse ponto final como insalubre. O seu valor pode variar entre 0 e 9. Um valor de 0 significa que uma única falha de monitorização pode fazer com que esse ponto final seja marcado como insalubre. Se não for especificado qualquer valor, utiliza o valor predefinido de 3.
* **Intervalo da sonda.** Esta propriedade especifica a quantidade de tempo que o agente de sondagem do Gestor de Tráfego deve esperar antes de considerar que verifique uma falha quando uma sonda de verificação de saúde é enviada para o ponto final. Se o intervalo de sondagem estiver definido para 30 segundos, pode definir o valor do tempo entre 5 e 10 segundos. Se não for especificado qualquer valor, utiliza um valor predefinido de 10 segundos. Se o intervalo de sondagem estiver definido para 10 segundos, pode definir o valor do tempo entre 5 e 9 segundos. Se não for especificado o valor do timeout, utiliza um valor predefinido de 9 segundos.

    ![Monitorização do ponto final do Gestor de Tráfego](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Figura: Monitorização do ponto final do gestor de tráfego**

## <a name="how-endpoint-monitoring-works"></a>Como funciona a monitorização do ponto final

Se o protocolo de monitorização for definido como HTTP ou HTTPS, o agente de sondagem do Gestor de Tráfego faz um pedido GET ao ponto final utilizando o protocolo, porta e caminho relativo dado. Se recuperar uma resposta de 200 OK, ou qualquer uma das respostas configuradas nas **gamas de \*código de estado esperados,** então esse ponto final é considerado saudável. Se a resposta for de um valor diferente, ou, se não for recebida qualquer resposta dentro do prazo de tempo especificado, então o Gestor de Tráfego que sonda re-tente de acordo com a definição de Número tolerado de Falhas (não são feitas retentativas se esta definição for de 0). Se o número de falhas consecutivas for superior à definição de Número tolerado de Falhas, então esse ponto final é marcado como insalubre. 

Se o protocolo de monitorização for TCP, o gestor de sons do Gestor de Tráfego inicia um pedido de ligação TCP utilizando a porta especificada. Se o ponto final responder ao pedido com uma resposta para estabelecer a ligação, esse exame de saúde é marcado como um sucesso e o agente de sondagem do Gestor de Tráfego repõe a ligação TCP. Se a resposta for de um valor diferente, ou se não for recebida qualquer resposta dentro do prazo de tempo especificado, o Gestor de Tráfego que sonda re-tentativas de acordo com a definição de Número tolerado de Falhas (não são feitas retentativas se esta definição for de 0). Se o número de falhas consecutivas for superior à definição de Número tolerado de Falhas, então esse ponto final é marcado como insalubre.

Em todos os casos, o Gestor de Tráfego sonda de vários locais e a determinação de falha consecutiva ocorre em cada região. Isto também significa que os pontos finais estão a receber sondas de saúde do Traffic Manager com uma frequência mais elevada do que a regulação utilizada para o Intervalo de Sondagem.

>[!NOTE]
>Para o protocolo de monitorização HTTP ou HTTPS, uma prática comum no lado final é implementar uma página personalizada dentro da sua aplicação - por exemplo, /health.aspx. Utilizando este caminho para monitorização, pode efetuar verificações específicas da aplicação, tais como verificar contadores de desempenho ou verificar a disponibilidade da base de dados. Com base nestas verificações personalizadas, a página devolve um código de estado HTTP apropriado.

Todos os pontos finais de uma definição de monitorização de partilha de perfil do Gestor de Tráfego. Se necessitar de utilizar diferentes definições de monitorização para diferentes pontos finais, pode criar [perfis aninhados do Traffic Manager](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Estado do ponto final e perfil

Pode ativar e desativar perfis e pontos finais do Gestor de Tráfego. No entanto, uma alteração no estado do ponto final também pode ocorrer como resultado de configurações e processos automatizados do Gestor de Tráfego.

### <a name="endpoint-status"></a>Estado do ponto final

Pode ativar ou desativar um ponto final específico. O serviço subjacente, que ainda pode ser saudável, não é afetado. A alteração do estado do ponto final controla a disponibilidade do ponto final no perfil do Gestor de Tráfego. Quando um estado de ponto final é desativado, o Gestor de Tráfego não verifica a sua saúde e o ponto final não está incluído numa resposta DNS.

### <a name="profile-status"></a>Estado do perfil

Utilizando a definição de estado do perfil, pode ativar ou desativar um perfil específico. Embora o estado do ponto final afete um único ponto final, o estado do perfil afeta todo o perfil, incluindo todos os pontos finais. Quando desativa um perfil, os pontos finais não são verificados para a saúde e não estão incluídos pontos finais numa resposta DNS. Um código de resposta [NXDOMAIN](https://tools.ietf.org/html/rfc2308) é devolvido para a consulta dNS.

### <a name="endpoint-monitor-status"></a>Estado do monitor endpoint

O estado do monitor endpoint é um valor gerado pelo Gestor de Tráfego que mostra o estado do ponto final. Não é possível alterar manualmente esta definição. O estado do monitor do ponto final é uma combinação dos resultados da monitorização do ponto final e do estado do ponto final configurado. Os valores possíveis do estado do monitor do ponto final são apresentados no quadro seguinte:

| Estado do perfil | Estado do ponto final | Estado do monitor endpoint | Notas |
| --- | --- | --- | --- |
| Desativado |Ativado |Inativa |O perfil foi desativado. Embora o estado do ponto final esteja ativado, o estado do perfil (Desativado) tem precedência. Os pontos finais dos perfis desativados não são monitorizados. Um código de resposta NXDOMAIN é devolvido para a consulta dNS. |
| &lt;qualquer&gt; |Desativado |Desativado |O ponto final foi desativado. Os pontos finais desativados não são monitorizados. O ponto final não está incluído nas respostas do DNS, portanto, não recebe tráfego. |
| Ativado |Ativado |Online |O ponto final é monitorizado e saudável. Está incluído nas respostas do DNS e pode receber tráfego. |
| Ativado |Ativado |Degradado |Os controlos de saúde do ponto final estão a falhar. O ponto final não está incluído nas respostas do DNS e não recebe tráfego. <br>Uma exceção a esta situação é se todos os pontos finais forem degradados, caso em que todos eles são considerados devolvidos na resposta à consulta).</br>|
| Ativado |Ativado |Ponto de verificação Endpoint |O ponto final é monitorizado, mas os resultados da primeira sonda ainda não foram recebidos. Verificar endpoint é um estado temporário que geralmente ocorre imediatamente após adicionar ou ativar um ponto final no perfil. Um ponto final neste estado está incluído nas respostas do DNS e pode receber tráfego. |
| Ativado |Ativado |Parada |A aplicação web a que o ponto final aponta não está a funcionar. Verifique as definições da aplicação web. Isto também pode acontecer se o ponto final for de ponto final aninhado tipo e o perfil da criança estiver desativado ou estiver inativo. <br>Um ponto final com um estado de paragem não é monitorizado. Não está incluído nas respostas do DNS e não recebe tráfego. Uma exceção a esta questão é se todos os pontos finais forem degradados, caso em que todos eles serão considerados devolvidos na resposta à consulta.</br>|

Para obter mais detalhes sobre como o estado do monitor de ponto final é calculado para pontos finais aninhados, consulte [os perfis do Gestor de Tráfego aninhadas](traffic-manager-nested-profiles.md).

>[!NOTE]
> Um estado de monitor stop Endpoint pode acontecer no Serviço de Aplicações se a sua aplicação web não estiver a funcionar no nível Standard ou acima. Para mais informações, consulte a integração do Gestor de Tráfego com o Serviço de [Aplicações.](/azure/app-service/web-sites-traffic-manager)

### <a name="profile-monitor-status"></a>Estado do monitor de perfil

O estado do monitor de perfil é uma combinação do estado do perfil configurado e dos valores de estado do monitor do ponto final para todos os pontos finais. Os valores possíveis são descritos no quadro seguinte:

| Estado do perfil (conforme configurado) | Estado do monitor endpoint | Estado do monitor de perfil | Notas |
| --- | --- | --- | --- |
| Desativado |&lt;qualquer&gt; ou um perfil sem pontos finais definidos. |Desativado |O perfil foi desativado. |
| Ativado |O estatuto de pelo menos um ponto final está degradado. |Degradado |Reveja os valores de estado do ponto final individuais para determinar quais os pontos finais que requerem mais atenção. |
| Ativado |O estado de pelo menos um ponto final está online. Nenhum ponto final tem um estatuto degradado. |Online |O serviço está a aceitar o trânsito. Não são necessárias mais ações. |
| Ativado |O estado de pelo menos um ponto final é verificar endpoint. Não existem pontos finais em estado online ou degradado. |Verificação de Pontos Finais |Este estado de transição ocorre quando um perfil se criado ou ativado. A saúde do ponto final está a ser verificada pela primeira vez. |
| Ativado |Os estatutos de todos os pontos finais do perfil são desativados ou parados, ou o perfil não tem pontos finais definidos. |Inativa |Não existem pontos finais ativos, mas o perfil ainda está ativado. |

## <a name="endpoint-failover-and-recovery"></a>Falha e recuperação de endpoint

O Gestor de Tráfego verifica periodicamente a saúde de todos os pontos finais, incluindo pontos finais pouco saudáveis. O Gestor de Tráfego deteta quando um ponto final fica saudável e o faz voltar à rotação.

Um ponto final não é saudável quando ocorre qualquer um dos seguintes eventos:

- Se o protocolo de monitorização for HTTP ou HTTPS:
    - Uma resposta não-200, ou uma resposta que não inclua o intervalo de estado especificado na definição **de código de estado esperado,** é recebida (incluindo um código 2xx diferente, ou um redirecionamento 301/302).
- Se o protocolo de monitorização for TCP: 
    - Uma resposta diferente da ACK ou da SYN-ACK é recebida em resposta ao pedido da SYN enviado pelo Traffic Manager para tentar um estabelecimento de ligação.
- Intervalo. 
- Qualquer outro problema de ligação que resulte no ponto final não é alcançável.

Para obter mais informações sobre verificações falhadas de resolução de problemas, consulte [o estado dedegradação](traffic-manager-troubleshooting-degraded.md)de Resolução de Problemas no Gestor de Tráfego azure . 

A cronologia na figura seguinte é uma descrição detalhada do processo de monitorização do ponto final do Gestor de Tráfego que tem as seguintes definições: o protocolo de monitorização é HTTP, o intervalo de sondagem é de 30 segundos, o número de falhas toleradas é de 3, o valor do tempo é de 10 segundos e o DNS TTL é de 30 segundos.

![Falha no ponto final do Gestor de Tráfego e sequência de recuo](./media/traffic-manager-monitoring/timeline.png)

**Figura: Falha no ponto final do gestor de tráfego e sequência de recuperação**

1. **VAI.** Para cada ponto final, o sistema de monitorização do Gestor de Tráfego executa um pedido GET no caminho especificado nas definições de monitorização.
2. **200 OK ou gama de códigos personalizados especificadas definições** de monitorização do perfil do Gestor de Tráfego . O sistema de monitorização espera que uma mensagem de monitorização http 200 OK ou a gama de códigos ou códigos personalizados especificada para o perfil do Gestor de Tráfego seja devolvida dentro de 10 segundos. Quando recebe esta resposta, reconhece que o serviço está disponível.
3. **30 segundos entre as verificações.** O exame de saúde final é repetido a cada 30 segundos.
4. **Serviço indisponível**. O serviço fica indisponível. O Gestor de Tráfego não saberá até ao próximo exame de saúde.
5. **Tenta aceder à via de monitorização**. O sistema de monitorização executa um pedido GET, mas não recebe uma resposta no prazo de 10 segundos (em alternativa, pode ser recebida uma resposta não-200). Tenta mais três vezes, em intervalos de 30 segundos. Se uma das tentativas for bem sucedida, então o número de tentativas é reposto.
6. **Estado definido para Degradado**. Após uma quarta falha consecutiva, o sistema de monitorização marca o estado final indisponível como Degradado.
7. **O tráfego é desviado para outros pontos finais.** Os servidores de nome DNS do Gestor de Tráfego são atualizados e o Traffic Manager já não devolve o ponto final em resposta a consultas de DNS. Novas ligações são direcionadas para outros pontos finais disponíveis. No entanto, respostas DNS anteriores que incluem este ponto final podem ainda ser cached por servidores DNS recursivos e clientes DNS. Os clientes continuam a utilizar o ponto final até que a cache DNS expire. À medida que a cache DNS expira, os clientes fazem novas consultas de DNS e são direcionados para diferentes pontos finais. A duração da cache é controlada pela definição TTL no perfil do Gestor de Tráfego, por exemplo, 30 segundos.
8. **Os controlos de saúde continuam.** O Gestor de Tráfego continua a verificar a saúde do ponto final enquanto tem um estatuto degradado. O Gestor de Tráfego deteta quando o ponto final volta à saúde.
9. **O serviço volta online.** O serviço fica disponível. O ponto final mantém o seu estado degradado no Traffic Manager até que o sistema de monitorização realize o seu próximo exame de saúde.
10. **O tráfego para o serviço retoma.** O Gestor de Tráfego envia um pedido GET e recebe uma resposta de 200 OK. O serviço regressou a um estado saudável. Os servidores de nome do Gestor de Tráfego são atualizados e começam a distribuir o nome DNS do serviço em respostas DNS. O tráfego regressa ao ponto final à medida que as respostas dNS em cache que devolvem outros pontos finais expiram, e como as ligações existentes a outros pontos finais são terminadas.

    > [!NOTE]
    > Como o Gestor de Tráfego trabalha ao nível do DNS, não pode influenciar as ligações existentes a qualquer ponto final. Quando direciona o tráfego entre pontos finais (seja por definições de perfil alteradas, quer durante o failover ou failback), o Traffic Manager direciona novas ligações para pontos finais disponíveis. No entanto, outros pontos finais poderão continuar a receber tráfego através das ligações existentes até que essas sessões sejam encerradas. Para permitir que o tráfego escorra das ligações existentes, as aplicações devem limitar a duração da sessão utilizada em cada ponto final.

## <a name="traffic-routing-methods"></a>Métodos de encaminhamento de tráfego

Quando um ponto final tem um estatuto degradado, já não é devolvido em resposta a consultas de DNS. Em vez disso, um ponto final alternativo é escolhido e devolvido. O método de encaminhamento de tráfego configurado no perfil determina como o ponto final alternativo é escolhido.

* **Prioridade.** Os pontos finais formam uma lista prioritária. O primeiro ponto final disponível na lista é sempre devolvido. Se um estado de ponto final estiver degradado, o próximo ponto final disponível é devolvido.
* **Ponderado.** Qualquer ponto final disponível é escolhido aleatoriamente com base nos seus pesos atribuídos e nos pesos dos outros pontos finais disponíveis.
* **Desempenho.** O ponto final mais próximo do utilizador final é devolvido. Se esse ponto final não estiver disponível, o Traffic Manager desloca o tráfego para os pontos finais da região de Azure mais próxima. Pode configurar planos alternativos de failover para o tráfego de desempenho através da utilização de [perfis aninhados do Traffic Manager](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geográfico.** O ponto final mapeado para servir a localização geográfica com base no pedido de consulta IP's é devolvido. Se esse ponto final não estiver disponível, outro ponto final não será selecionado para falhar, uma vez que uma localização geográfica só pode ser mapeada para um ponto final num perfil (mais detalhes estão no [FAQ).](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method) Como uma boa prática, ao utilizar o encaminhamento geográfico, recomendamos que os clientes utilizem perfis aninhados do Traffic Manager com mais de um ponto final como pontofinal do perfil.
* **MultiValor** São devolvidos vários pontos finais mapeados para endereços IPv4/IPv6. Quando uma consulta é recebida para este perfil, os pontos finais saudáveis são devolvidos com base na contagem máxima de **registo no** valor de resposta que especificou. O número padrão de respostas é de dois pontos finais.
* **Sub-rede** O ponto final mapeado para um conjunto de gamas de endereços IP é devolvido. Quando um pedido é recebido desse endereço IP, o ponto final devolvido é o que está mapeado para esse endereço IP. 

Para obter mais informações, consulte [os métodos de encaminhamento de tráfego do Gestor de Tráfego](traffic-manager-routing-methods.md).

> [!NOTE]
> Uma exceção ao comportamento normal de encaminhamento de tráfego ocorre quando todos os pontos finais elegíveis têm um estatuto degradado. O Gestor de Tráfego faz uma tentativa de "melhor esforço" e *responde como se todos os pontos finais do estado degradados estivessem realmente num estado online.* Este comportamento é preferível à alternativa, que seria não devolver qualquer ponto final na resposta do DNS. Por conseguinte, os pontos finais desativados ou parados não são monitorizados, pelo que não são considerados elegíveis para o tráfego.
>
> Esta condição é geralmente causada por uma configuração inadequada do serviço, tais como:
>
> * Uma lista de controlo de acessos [ACL] bloqueando os controlos de saúde do Gestor de Tráfego.
> * Uma configuração inadequada da porta de monitorização ou protocolo no perfil do gestor de tráfego.
>
> A consequência deste comportamento é que, se os controlos de saúde do Gestor de Tráfego não estiverem corretamente configurados, pode parecer que o traffic-routing está *a* funcionar corretamente. No entanto, neste caso, não pode ocorrer uma falha no ponto final que afeta a disponibilidade global de aplicações. É importante verificar se o perfil mostra um estado Online, não um estado degradado. Um estado online indica que os controlos de saúde do Gestor de Tráfego estão a funcionar como esperado.

Para obter mais informações sobre a resolução de problemas, consulte o estado degradado de Resolução de Problemas no Gestor de [Tráfego de Azure.](traffic-manager-troubleshooting-degraded.md)

## <a name="faqs"></a>FAQs

* [O Gestor de Tráfego é resiliente às falhas da região de Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-traffic-manager-resilient-to-azure-region-failures)

* [Como é que a escolha da localização do grupo de recursos afeta o Gestor de Tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Como determino a saúde atual de cada ponto final?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-determine-the-current-health-of-each-endpoint)

* [Posso monitorizar os pontos finais HTTPS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-monitor-https-endpoints)

* [Uso um endereço IP ou um nome DNS ao adicionar um ponto final?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Que tipos de endereços IP posso usar ao adicionar um ponto final?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Posso usar diferentes tipos de endereços de ponto final dentro de um único perfil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [O que acontece quando o tipo de registo de uma consulta é diferente do tipo de registo associado ao tipo de endereçamento dos pontos finais?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Posso usar um perfil com iPv4 / IPv6 endereçado pontos finais num perfil aninhado?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Parei um ponto final de aplicação web no meu perfil de Traffic Manager, mas não estou a receber nenhum tráfego mesmo depois de o reiniciar. Como posso consertar isto?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Posso usar o Traffic Manager mesmo que a minha aplicação não tenha suporte para HTTP ou HTTPS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Que respostas específicas são necessárias a partir do ponto final quando se utiliza a monitorização do TCP?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [A que velocidade o Traffic Manager afasta os meus utilizadores de um ponto final pouco saudável?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Como posso especificar diferentes definições de monitorização para diferentes pontos finais num perfil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Como posso atribuir cabeçalhos HTTP ao Gestor de Tráfego cheques de saúde para os meus pontos finais?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Que cabeçalho de hospedeiro usam os controlos de saúde finais?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-host-header-do-endpoint-health-checks-use)

* [Quais são os endereços IP de que provêm os controlos de saúde?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Quantos exames de saúde posso esperar do Gerente de Tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Como posso ser notificado se um dos meus pontos finais cai?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Passos seguintes

Saiba como funciona o [Gestor de Tráfego](traffic-manager-how-it-works.md)

Saiba mais sobre os [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportados pelo Traffic Manager

Saiba como [criar um perfil de Gestor](traffic-manager-manage-profiles.md) de Tráfego

[Problemas desclassificação do estado](traffic-manager-troubleshooting-degraded.md) num ponto final do Gestor de Tráfego
