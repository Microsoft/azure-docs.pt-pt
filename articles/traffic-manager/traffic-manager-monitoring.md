---
title: Azure Traffic Manager monitorizando | Microsoft Docs
description: Este artigo pode ajudá-lo a entender como o Gestor de Tráfego utiliza a monitorização de pontos finais e falha automática no ponto final para ajudar os clientes do Azure a implementar aplicações de alta disponibilidade
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2021
ms.author: duau
ms.openlocfilehash: 455eeb83ef5a9608c077de24b8d3d4722d26a822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742714"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitorização do ponto final do Gestor de Tráfego

O Gestor de Tráfego Azure inclui monitorização de pontos finais incorporados e falha de ponto final automático. Esta funcionalidade ajuda-o a fornecer aplicações de alta disponibilidade que são resistentes à falha no ponto final, incluindo falhas na região de Azure.

## <a name="configure-endpoint-monitoring"></a>Configure a monitorização do ponto final

Para configurar a monitorização do ponto final, tem de especificar as seguintes definições no perfil do Gestor de Tráfego:

* **Protocolo**. Escolha HTTP, HTTPS ou TCP como o protocolo que o Gestor de Tráfego utiliza ao sondar o seu ponto final para verificar a sua saúde. A monitorização HTTPS não verifica se o seu certificado TLS/SSL é válido - apenas verifica se o certificado está presente.
* **Porto**. Escolha a porta utilizada para o pedido.
* **Caminho.** Esta definição de configuração é válida apenas para os protocolos HTTP e HTTPS, para os quais é necessária especificar a definição do percurso. O fornecimento desta definição para o protocolo de monitorização TCP resulta num erro. Para protocolo HTTP e HTTPS, forneça o caminho relativo e o nome da página web ou do ficheiro a que a monitorização acede. Um corte para a frente (/) é uma entrada válida para o caminho relativo. Este valor implica que o ficheiro está no diretório de raiz (predefinição).
* **Definições personalizadas do cabeçalho**. Esta definição de configuração ajuda-o a adicionar cabeçalhos HTTP específicos às verificações de saúde que o Traffic Manager envia para pontos finais sob um perfil. Os cabeçalhos personalizados podem ser especificados a um nível de perfil para serem aplicáveis a todos os pontos finais nesse perfil e/ou a um nível de ponto final aplicável apenas a esse ponto final. Você pode usar cabeçalhos personalizados para verificações de saúde de pontos finais em um ambiente multi-inquilino. Desta forma, pode ser encaminhado corretamente para o seu destino, especificando um cabeçalho hospedeiro. Também pode utilizar esta definição adicionando cabeçalhos únicos que podem ser usados para identificar pedidos HTTP(S originados pelo Traffic Manager e processá-los de forma diferente. Pode especificar até oito pares de cabeçalho:valor separados por uma vírgula. Por exemplo, "header1:value1, header2:value2". 
* **Os intervalos previstos de código de estado**. Esta definição permite especificar várias gamas de códigos de sucesso no formato 200-299, 301-301. Se estes códigos de estado forem recebidos como resposta de um ponto final quando um exame de saúde é feito, o Gestor de Tráfego assinala esses pontos finais como saudáveis. Pode especificar um máximo de oito intervalos de código de estado. Esta definição é aplicável apenas ao protocolo HTTP e HTTPS e a todos os pontos finais. Esta definição está ao nível do perfil do Gestor de Tráfego e por predefinição o valor 200 é definido como o código de estado de sucesso.
* **Intervalo de sondagem**. Este valor especifica a frequência com que um ponto final é verificado para a sua saúde a partir de um agente de sondagem do Traffic Manager. Pode especificar dois valores aqui: 30 segundos (sondagem normal) e 10 segundos (sondagem rápida). Se não forem fornecidos valores, o perfil define-se para um valor predefinido de 30 segundos. Visite a página [de preços do Gestor de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager) para saber mais sobre preços rápidos de sondagem.
* **Número tolerado de falhas.** Este valor especifica quantas falhas um agente de sondagem do Traffic Manager tolera antes de marcar esse ponto final como insalubre. O seu valor pode variar entre 0 e 9. Um valor de 0 significa que uma única falha de monitorização pode fazer com que esse ponto final seja marcado como insalubre. Se não for especificado qualquer valor, utiliza o valor predefinido de 3.
* **Tempo limite de sonda**. Esta propriedade especifica a quantidade de tempo que o agente de sondagem do Traffic Manager deve esperar antes de considerar uma verificação da sonda de saúde para um ponto final uma falha. Se o intervalo de sondagem estiver definido para 30 segundos, então pode definir o valor do intervalo entre 5 e 10 segundos. Se não for especificado qualquer valor, utiliza um valor predefinido de 10 segundos. Se o intervalo de sondagem estiver definido para 10 segundos, então pode definir o valor do intervalo entre 5 e 9 segundos. Se não for especificado nenhum valor de tempo limite, utiliza um valor predefinido de 9 segundos.

    ![Monitorização do ponto final do Gestor de Tráfego](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Figura: Monitorização do ponto final do Gestor de Tráfego**

## <a name="how-endpoint-monitoring-works"></a>Como funciona a monitorização do ponto final

Quando o protocolo de monitorização é definido como HTTP ou HTTPS, o agente de sondagem do Gestor de Tráfego faz um pedido GET ao ponto final utilizando o protocolo, porta e caminho relativo dado. Um ponto final é considerado saudável se o agente de sondagem receber uma resposta de 200 OK, ou qualquer uma das respostas configuradas nas **gamas de \* códigos de estado esperados**. Se a resposta for um valor diferente ou nenhuma resposta for recebida dentro do período de tempo limite, o agente de sondagem do Gestor de Tráfego reatteça de acordo com a definição de Número Tolerado de Falhas. Não são feitas reattemptas se esta definição for 0. O ponto final é marcado como insalubre se o número de falhas consecutivas for superior ao número tolerado de falhas.

Quando o protocolo de monitorização é TCP, o agente de sondagem do Gestor de Tráfego cria um pedido de ligação TCP utilizando a porta especificada. Se o ponto final responder ao pedido com uma resposta para estabelecer a ligação, esse exame de saúde é marcado como um sucesso. O agente de sondagem do Gestor de Tráfego reinicia a ligação TCP. Nos casos em que a resposta é de um valor diferente ou nenhuma resposta é recebida dentro do período de tempo limite, o agente de sondagem do Gestor de Tráfego recandidata de acordo com a definição de Número Tolerado de Falhas. Não são feitas reattemptas se esta definição for 0. Se o número de falhas consecutivas for superior ao número tolerado de falhas, então esse ponto final não é saudável.

Em todos os casos, o Traffic Manager sonda de vários locais. O fracasso consecutivo determina o que acontece em cada região. É por isso que os pontos finais estão a receber sondas de saúde do Traffic Manager com uma frequência mais alta do que a configuração usada para o Intervalo de Sondagem.

>[!NOTE]
>Para o protocolo de monitorização HTTP ou HTTPS, uma prática comum no lado do ponto final é implementar uma página personalizada dentro da sua aplicação - por exemplo, /saúde.aspx. Utilizando este caminho para monitorização, pode efetuar verificações específicas da aplicação, tais como verificar contadores de desempenho ou verificar a disponibilidade da base de dados. Com base nestas verificações personalizadas, a página devolve um código de estado HTTP apropriado.

Todos os pontos finais de uma definição de monitorização de partilha de perfil do Gestor de Tráfego. Se precisar de utilizar diferentes definições de monitorização para diferentes pontos finais, pode criar [perfis de Gestor de Tráfego aninhados](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Ponto final e estado do perfil

Pode ativar e desativar perfis e pontos finais do Traffic Manager. No entanto, uma alteração no estado do ponto final também pode ocorrer devido às configurações e processos automatizados do Gestor de Tráfego.

### <a name="endpoint-status"></a>Estado do ponto final

Pode ativar ou desativar um ponto final específico. O serviço subjacente, que ainda pode ser saudável, não é afetado. A alteração do estado do ponto final controla a disponibilidade do ponto final no perfil de Gestor de Tráfego. Quando um estado de ponto final é desativado, o Traffic Manager não verifica a sua saúde e o ponto final não está incluído numa resposta ao DNS.

### <a name="profile-status"></a>Estado do perfil

Utilizando a definição de estado de perfil, pode ativar ou desativar um perfil específico. Embora o estado do ponto final afete um único ponto final, o estado do perfil afeta todo o perfil, incluindo todos os pontos finais. Quando desativa um perfil, os pontos finais não são verificados para saúde e nenhum ponto final é incluído numa resposta ao DNS. Um código de resposta [NXDOMAIN](https://tools.ietf.org/html/rfc2308) é devolvido para a consulta DNS.

### <a name="endpoint-monitor-status"></a>Estado do monitor de ponto final

O estado do monitor de ponto final é um valor gerado pelo Gestor de Tráfego que mostra o estado do ponto final. Não é possível alterar esta definição manualmente. O estado do monitor de ponto final é uma combinação dos resultados da monitorização do ponto final e do estado do ponto final configurado. Os valores possíveis do estado do monitor de ponto final são indicados no quadro seguinte:

| Estado do perfil | Estado do ponto final | Estado do monitor de ponto final | Notas |
| --- | --- | --- | --- |
| Desativado |Ativado |Inativa |O perfil foi desativado. Embora o estado do ponto final esteja Ativado, o estado do perfil (Desativado) tem precedência. Os pontos finais nos perfis desativados não são monitorizados. Um código de resposta NXDOMAIN é devolvido para a consulta DNS. |
| &lt;qualquer&gt; |Desativado |Desativado |O ponto final foi desativado. Os pontos finais desativados não são monitorizados. O ponto final não está incluído nas respostas do DNS, como tal não recebe tráfego. |
| Ativado |Ativado |Online |O ponto final é monitorizado e saudável. Está incluído nas respostas do DNS e pode receber tráfego. |
| Ativado |Ativado |Degradado |Os controlos de saúde de monitorização do ponto final estão a falhar. O ponto final não está incluído nas respostas do DNS e não recebe tráfego. <br>Uma exceção é se todos os pontos finais estiverem degradados. Nesse caso, todos são considerados devolvidos na resposta de consulta).</br>|
| Ativado |Ativado |VerificaçãoEndpoint |O ponto final é monitorizado, mas os resultados da primeira sonda ainda não foram recebidos. CheckingEndpoint é um estado temporário que ocorre geralmente imediatamente após adicionar ou ativar um ponto final no perfil. Um ponto final neste estado está incluído nas respostas do DNS e pode receber tráfego. |
| Ativado |Ativado |Parada |A aplicação web a que o ponto final aponta não está a funcionar. Verifique as definições da aplicação web. Este estado também pode acontecer se o ponto final for do tipo aninhado e o perfil da criança ficar incapacitado ou estiver inativo. <br>Um ponto final com um estado stop não é monitorizado. Não está incluído nas respostas do DNS e não recebe tráfego. Uma exceção é se todos os pontos finais estiverem degradados. Nesse caso, todos serão considerados devolvidos na resposta de consulta.</br>|

Para obter mais informações sobre como o estado do monitor de ponto final é calculado para os pontos finais aninhados, consulte [os perfis do Gestor de Tráfego aninhado](traffic-manager-nested-profiles.md).

>[!NOTE]
> Um estado de monitor de ponto final parado pode acontecer no Serviço de Aplicações se a sua aplicação web não estiver a funcionar no nível Standard ou acima. Para mais informações, consulte a [integração do Traffic Manager com o Serviço de Aplicações.](../app-service/web-sites-traffic-manager.md)

### <a name="profile-monitor-status"></a>Estado do monitor de perfil

O estado do monitor de perfil é uma combinação do estado de perfil configurado e dos valores de estado do monitor de ponto final para todos os pontos finais. Os valores possíveis são descritos no quadro seguinte:

| Estado do perfil (conforme configurado) | Estado do monitor de ponto final | Estado do monitor de perfil | Notas |
| --- | --- | --- | --- |
| Desativado |&lt;qualquer &gt; ou um perfil sem pontos finais definidos. |Desativado |O perfil foi desativado. |
| Ativado |O estado de pelo menos um ponto final está degradado. |Degradado |Reveja os valores de estado de ponto final individuais para determinar quais os pontos finais que requerem mais atenção. |
| Ativado |O estado de pelo menos um ponto final é online. Nenhum ponto final tem um estatuto degradado. |Online |O serviço está a aceitar o trânsito. Não são necessárias mais ações. |
| Ativado |O estado de pelo menos um ponto final é CheckingEndpoint. Não existem pontos finais em estado online ou degradado. |Verificação Deponts de Final |Este estado de transição ocorre quando um perfil se criado ou ativado. A saúde do ponto final está a ser verificada pela primeira vez. |
| Ativado |Os estados de todos os pontos finais no perfil são desativado ou parados, ou o perfil não tem pontos finais definidos. |Inativa |Nenhum ponto final está ativo, mas o perfil ainda está ativado. |

## <a name="endpoint-failover-and-recovery"></a>Falha e recuperação do ponto final

O Gestor de Tráfego verifica periodicamente a saúde de todos os pontos finais, incluindo pontos finais pouco saudáveis. O Gestor de Tráfego deteta quando um ponto final se torna saudável e o traz de volta à rotação.

Um ponto final não é saudável quando ocorre qualquer um dos seguintes eventos:

- Se o protocolo de monitorização for HTTP ou HTTPS:
    - Uma resposta não-200, ou uma resposta que não inclua o intervalo de estado especificado na definição de **código de estado esperado,** é recebida. (Incluindo um código 2xx diferente, ou um redirecionamento 301/302).
- Se o protocolo de monitorização for TCP: 
    - Uma resposta diferente da ACK ou SYN-ACK é recebida em resposta ao pedido SYN enviado pelo Traffic Manager para tentar um estabelecimento de ligação.
- Tempo limite. 
- Qualquer outro problema de ligação que resulte em que o ponto final não seja alcançável.

Para obter mais informações sobre a resolução de problemas de verificações falhadas, consulte o estado degradado da [resolução de problemas no Gestor de Tráfego da Azure](traffic-manager-troubleshooting-degraded.md). 

A cronologia no seguinte número é uma descrição detalhada do processo de monitorização do ponto final do Gestor de Tráfego que tem as seguintes definições:

* O protocolo de monitorização é HTTP.
* O intervalo de sondagem é de 30 segundos.
* Número de falhas toleradas é 3.
* O valor de tempo limite é de 10 segundos.
* DNS TTL é de 30 segundos.

![Falha no ponto final do Gestor de Tráfego e sequência de recuo](./media/traffic-manager-monitoring/timeline.png)

**Figura: Falha no ponto final do gestor de tráfego e sequência de recuperação**

1. **GET**. Para cada ponto final, o sistema de monitorização do Gestor de Tráfego faz um pedido GET sobre o caminho especificado nas definições de monitorização.
2. **200 OK ou gama de códigos personalizados especificadas definições de monitorização do perfil do Gestor de Tráfego**. O sistema de monitorização espera que um HTTP 200 OK ou um código de estado na gama especificado nas definições de monitorização seja devolvido dentro de 10 segundos. Quando recebe esta resposta, reconhece que o serviço está disponível.
3. **30 segundos entre verificações**. O exame de saúde do ponto final é repetido a cada 30 segundos.
4. **Serviço indisponível**. O serviço fica indisponível. O gerente de trânsito não saberá até ao próximo exame de saúde.
5. **Tentativas de acesso ao caminho de monitorização.** O sistema de monitorização faz um pedido GET, mas não recebe uma resposta dentro do período de tempo limite de 10 segundos. Em seguida, tenta mais três vezes, em intervalos de 30 segundos. Se uma das tentativas for bem sucedida, então o número de tentativas é reiniciado.
6. **Estado definido para Degradado**. Após uma quarta falha consecutiva, o sistema de monitorização marca o estado de ponto final indisponível como Degradado.
7. **O tráfego é desviado para outros pontos finais.** Os servidores de nome DNS do Gestor de Tráfego são atualizados e o Gestor de Tráfego já não devolve o ponto final em resposta a consultas de DNS. As novas ligações são direcionadas para outros pontos finais disponíveis. No entanto, as respostas anteriores do DNS que incluem este ponto final ainda podem ser cached por servidores DNS recursivos e clientes DNS. Os clientes continuam a utilizar o ponto final até que a cache DNS expire. À medida que a cache DNS expira, os clientes fazem novas consultas de DNS e são direcionados para diferentes pontos finais. A duração da cache é controlada pela definição TTL no perfil de Gestor de Tráfego, por exemplo, 30 segundos.
8. **Os controlos de saúde continuam.** O Gestor de Tráfego continua a verificar a saúde do ponto final enquanto tem um estado degradado. O Gestor de Tráfego deteta quando o ponto final volta à saúde.
9. **O serviço volta a estar online.** O serviço fica disponível. O ponto final mantém o seu estado degradado no Gestor de Tráfego até que o sistema de monitorização faça o seu próximo exame de saúde.
10. **O tráfego para o serviço retoma.** O Gestor de Tráfego envia um pedido GET e recebe uma resposta de estado de 200 OK. O serviço regressou a um estado saudável. Os servidores de nome do Gestor de Tráfego são atualizados e começam a distribuir o nome DNS do serviço nas respostas dns. O tráfego regressa ao ponto final à medida que as respostas dns em cache que devolvem outros pontos finais expiram, e como as ligações existentes para outros pontos finais estão terminando.

    > [!NOTE]
    > Como o Gestor de Tráfego funciona ao nível do DNS, não pode influenciar as ligações existentes a qualquer ponto final. Quando direciona o tráfego entre pontos finais (quer por definições de perfil alteradas, quer durante o failover ou failback), o Gestor de Tráfego direciona novas ligações para os pontos finais disponíveis. No entanto, outros pontos finais podem continuar a receber tráfego através das ligações existentes até que essas sessões sejam encerradas. Para permitir que o tráfego escorra das ligações existentes, as aplicações devem limitar a duração da sessão utilizada com cada ponto final.

## <a name="traffic-routing-methods"></a>Métodos de encaminhamento de tráfego

Quando um ponto final tem um estatuto degradado, já não é devolvido em resposta a consultas de DNS. Em vez disso, um ponto final alternativo é escolhido e devolvido. O método de encaminhamento de tráfego configurado no perfil determina como o ponto final alternativo é escolhido.

* **Prioridade**. Os pontos finais formam uma lista prioritária. O primeiro ponto final disponível na lista é sempre devolvido. Se um estado de ponto final for degradado, o próximo ponto final disponível é devolvido.
* **Ponderado.** Todos os pontos finais disponíveis são escolhidos aleatoriamente com base nos seus pesos atribuídos e nos pesos dos outros pontos finais disponíveis.
* **Desempenho**. O ponto final mais próximo do utilizador final é devolvido. Se esse ponto final não estiver disponível, o Gestor de Tráfego desloca o tráfego para os pontos finais na próxima região de Azure. Pode configurar planos alternativos de failover para o encaminhamento de tráfego de desempenho utilizando [perfis de Gestor de Tráfego aninhados](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geográfico.** O ponto final mapeado para servir a localização geográfica com base no pedido de consulta IP's é devolvido. Se esse ponto final não estiver disponível, outro ponto final não será selecionado para falhar, uma vez que uma localização geográfica só pode ser mapeada para um ponto final num perfil. (Mais detalhes estão nas [FAQ).](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method) Como uma boa prática, ao utilizar o encaminhamento geográfico, recomendamos que os clientes utilizem perfis de Gestor de Tráfego aninhados com mais de um ponto final como ponto final do perfil.
* **MultiValue** Vários pontos finais mapeados para endereços IPv4/IPv6 são devolvidos. Quando uma consulta é recebida para este perfil, os pontos finais saudáveis são devolvidos com base na **contagem máxima de registo no** valor de resposta que especificou. O número predefinido de respostas é de dois pontos finais.
* **Sub-rede** O ponto final mapeado para um conjunto de intervalos de endereços IP é devolvido. Quando um pedido é recebido a partir desse endereço IP, o ponto final devolvido é o mapeado para esse endereço IP. 

Para obter mais informações, consulte [os métodos de encaminhamento de tráfego do Gestor de Tráfego](traffic-manager-routing-methods.md).

> [!NOTE]
> Uma exceção ao comportamento normal de encaminhamento de tráfego ocorre quando todos os pontos finais elegíveis têm um estatuto degradado. O Gestor de Tráfego faz uma tentativa de "melhor esforço" e *responde como se todos os pontos finais de estado degradados estivessem realmente num estado online.* Este comportamento é preferível à alternativa, que seria não devolver nenhum ponto final na resposta ao DNS. Os pontos finais desativados ou parados não são monitorizados, pelo que não são considerados elegíveis para o tráfego.
>
> Esta condição é geralmente causada por uma configuração inadequada do serviço, tais como:
>
> * Uma lista de controlo de acessos [ACL] bloqueando os controlos de saúde do Traffic Manager.
> * Uma configuração inadequada da porta de monitorização ou protocolo no perfil do gestor de tráfego.
>
> A consequência deste comportamento é que se os controlos de saúde do Traffic Manager não estiverem configurados corretamente, pode parecer do encaminhamento de tráfego como se o Traffic Manager *estivesse* a funcionar corretamente. No entanto, neste caso, a falência do ponto final não pode acontecer, o que afeta a disponibilidade global de aplicações. É importante verificar se o perfil mostra um estado Online, não um estado degradado. Um estado online indica que os controlos de saúde do Gestor de Tráfego estão a funcionar como esperado.

Para obter mais informações sobre a resolução de problemas de verificação de saúde falhada, consulte [o estado degradado da resolução de problemas no Gestor de Tráfego da Azure](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>FAQs

* [O Gestor de Tráfego é resiliente às falhas da região de Azure?](./traffic-manager-faqs.md#is-traffic-manager-resilient-to-azure-region-failures)

* [Como é que a escolha da localização do grupo de recursos afeta o Gestor de Tráfego?](./traffic-manager-faqs.md#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Como determino a saúde atual de cada ponto final?](./traffic-manager-faqs.md#how-do-i-determine-the-current-health-of-each-endpoint)

* [Posso monitorizar os pontos finais do HTTPS?](./traffic-manager-faqs.md#can-i-monitor-https-endpoints)

* [Uso um endereço IP ou um nome DNS ao adicionar um ponto final?](./traffic-manager-faqs.md#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Que tipos de endereços IP posso usar ao adicionar um ponto final?](./traffic-manager-faqs.md#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Posso utilizar diferentes tipos de endpoint num único perfil?](./traffic-manager-faqs.md#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [O que acontece quando o tipo de registo de uma consulta de entrada é diferente do tipo de registo associado ao tipo de endereço dos pontos finais?](./traffic-manager-faqs.md#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Posso usar um perfil com iPv4/ IPv6 endpoints endpoints num perfil aninhado?](./traffic-manager-faqs.md#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Parei um ponto final de aplicação web no meu perfil de Gerente de Tráfego, mas não estou a receber nenhum tráfego mesmo depois de o ter reiniciado. Como posso resolver isto?](./traffic-manager-faqs.md#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Posso utilizar o Traffic Manager mesmo que a minha aplicação não tenha suporte para HTTP ou HTTPS?](./traffic-manager-faqs.md#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Que respostas específicas são necessárias a partir do ponto final durante a utilização do controlo TCP?](./traffic-manager-faqs.md#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Quão rápido o Traffic Manager afasta os meus utilizadores de um ponto final pouco saudável?](./traffic-manager-faqs.md#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Como posso especificar diferentes definições de monitorização para diferentes pontos finais num perfil?](./traffic-manager-faqs.md#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Como posso atribuir cabeçalhos HTTP aos controlos de saúde do Gestor de Tráfego para os meus pontos finais?](./traffic-manager-faqs.md#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Que cabeçalho de anfitrião usam os controlos de saúde do ponto final?](./traffic-manager-faqs.md#what-host-header-do-endpoint-health-checks-use)

* [Quais são os endereços IP de onde provêm os controlos sanitários?](./traffic-manager-faqs.md#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Quantos cheques de saúde para o meu ponto final posso esperar do Gerente de Tráfego?](./traffic-manager-faqs.md#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Como posso ser notificado se um dos meus pontos finais vai para baixo?](./traffic-manager-faqs.md#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Passos seguintes

Saiba [como funciona o Gestor de Tráfego](traffic-manager-how-it-works.md)

Saiba mais sobre os [métodos de encaminhamento de tráfego suportados](traffic-manager-routing-methods.md) pelo Traffic Manager

Saiba como [criar um perfil de Gestor de Tráfego](traffic-manager-manage-profiles.md)

[Resolução de problemas Degradado estado](traffic-manager-troubleshooting-degraded.md) em um ponto final do Gestor de Tráfego