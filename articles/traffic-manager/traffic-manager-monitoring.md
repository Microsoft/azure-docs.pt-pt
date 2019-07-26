---
title: Monitoramento de ponto de extremidade do Gerenciador de tráfego do Azure | Microsoft Docs
description: Este artigo pode ajudá-lo a entender como o Gerenciador de tráfego usa o monitoramento de ponto de extremidade e o failover automático para ajudar os clientes do Azure a implantar aplicativos de alta disponibilidade
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: allensu
ms.openlocfilehash: e06d2ce93ac7c534f2c729dce794e66e3ee894d8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333809"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitoramento de ponto de extremidade do Gerenciador de tráfego

O Gerenciador de tráfego do Azure inclui monitoramento de ponto de extremidade interno e failover de ponto de extremidade automático. Esse recurso ajuda a fornecer aplicativos de alta disponibilidade que são resilientes a falhas de ponto de extremidade, incluindo falhas de região do Azure.

## <a name="configure-endpoint-monitoring"></a>Configurar o monitoramento de ponto de extremidade

Para configurar o monitoramento de ponto de extremidade, você deve especificar as seguintes configurações no seu perfil do Gerenciador de tráfego:

* **Protocolo**. Escolha HTTP, HTTPS ou TCP como o protocolo que o Gerenciador de tráfego usa ao investigar seu ponto de extremidade para verificar sua integridade. O monitoramento de HTTPS não verifica se o certificado SSL é válido – ele verifica apenas se o certificado está presente.
* **Porta**. Escolha a porta usada para a solicitação.
* **Caminho**. Essa configuração é válida somente para os protocolos HTTP e HTTPS, para os quais a especificação da configuração de caminho é necessária. O fornecimento dessa configuração para o protocolo de monitoramento TCP resulta em um erro. Para o protocolo HTTP e HTTPS, forneça o caminho relativo e o nome da página da Web ou o arquivo que o monitoramento acessa. Uma barra (/) é uma entrada válida para o caminho relativo. Esse valor implica que o arquivo está no diretório raiz (padrão).
* **Configurações de cabeçalho personalizadas** Essa definição de configuração ajuda a adicionar cabeçalhos HTTP específicos às verificações de integridade que o Gerenciador de tráfego envia aos pontos de extremidade em um perfil. Os cabeçalhos personalizados podem ser especificados em um nível de perfil para serem aplicáveis a todos os pontos de extremidade nesse perfil e/ou em um nível de ponto de extremidades aplicável somente a esse ponto de extremidade. Você pode usar cabeçalhos personalizados para que as verificações de integridade de pontos de extremidade em um ambiente multilocatário sejam roteadas corretamente para seu destino, especificando um cabeçalho de host. Você também pode usar essa configuração adicionando cabeçalhos exclusivos que podem ser usados para identificar solicitações HTTP (S) originadas do Gerenciador de tráfego e processá-las de forma diferente. Você pode especificar até oito pares de cabeçalho: valor seprated por uma vírgula. Por exemplo, "header1: value1, header2: value2". 
* **Intervalos de códigos de status esperados** Essa configuração permite que você especifique vários intervalos de códigos de êxito no formato 200-299, 301-301. Se esses códigos de status forem recebidos como resposta de um ponto de extremidade quando uma verificação de integridade for iniciada, o Gerenciador de tráfego marcará esses pontos como íntegros. Você pode especificar um máximo de 8 intervalos de código de status. Essa configuração é aplicável somente ao protocolo HTTP e HTTPS e a todos os pontos de extremidade. Essa configuração está no nível do perfil do Traffic Manager e, por padrão, o valor 200 é definido como o código de status de êxito.
* **Intervalo de investigação**. Esse valor especifica com que frequência um ponto de extremidade é verificado para sua integridade de um agente de investigação do Gerenciador de tráfego. Você pode especificar dois valores aqui: 30 segundos (investigação normal) e 10 segundos (investigação rápida). Se nenhum valor for fornecido, o perfil definirá como um valor padrão de 30 segundos. Visite a página de [preços do Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) para saber mais sobre os preços de investigação rápida.
* **Número de falhas toleradas**. Esse valor especifica quantas falhas um agente de investigação do Traffic Manager tolera antes de marcar esse ponto de extremidade como não íntegro. Seu valor pode variar entre 0 e 9. Um valor de 0 significa que uma única falha de monitoramento pode fazer com que esse ponto de extremidade seja marcado como não íntegro. Se nenhum valor for especificado, ele usará o valor padrão de 3.
* **Tempo limite de investigação**. Essa propriedade especifica a quantidade de tempo que o agente de investigação do Traffic Manager deve aguardar antes de considerar que verificar uma falha quando um teste de verificação de integridade é enviado para o ponto de extremidade. Se o intervalo de investigação for definido como 30 segundos, você poderá definir o valor de tempo limite entre 5 e 10 segundos. Se nenhum valor for especificado, ele usará um valor padrão de 10 segundos. Se o intervalo de investigação for definido como 10 segundos, você poderá definir o valor de tempo limite entre 5 e 9 segundos. Se nenhum valor de tempo limite for especificado, ele usará um valor padrão de 9 segundos.

    ![Monitoramento de ponto de extremidade do Gerenciador de tráfego](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Figuras  Monitoramento de ponto de extremidade do Gerenciador de tráfego**

## <a name="how-endpoint-monitoring-works"></a>Como funciona o monitoramento do ponto de extremidade

Se o protocolo de monitoramento estiver definido como HTTP ou HTTPS, o agente de investigação do Traffic Manager fará uma solicitação GET para o ponto de extremidade usando o protocolo, a porta e o caminho relativo fornecidos. Se obtiver novamente uma resposta 200-OK ou qualquer uma das respostas configuradas nos **intervalos de código \*de status esperados**, esse ponto de extremidade será considerado íntegro. Se a resposta for um valor diferente, ou se nenhuma resposta for recebida dentro do período de tempo limite especificado, o agente de investigação do Traffic Manager tentará novamente de acordo com a configuração de número de falhas toleradas (nenhuma nova tentativa será feita se essa configuração for 0). Se o número de falhas consecutivas for maior que a configuração de número de falhas toleradas, esse ponto de extremidade será marcado como não íntegro. 

Se o protocolo de monitoramento for TCP, o agente de investigação do Traffic Manager iniciará uma solicitação de conexão TCP usando a porta especificada. Se o ponto de extremidade responder à solicitação com uma resposta para estabelecer a conexão, essa verificação de integridade será marcada como êxito e o agente de investigação do Traffic Manager redefinirá a conexão TCP. Se a resposta for um valor diferente, ou se nenhuma resposta for recebida dentro do período de tempo limite especificado, o agente de investigação do Traffic Manager tentará novamente de acordo com a configuração de número de falhas toleradas (nenhuma nova tentativa será feita se essa configuração for 0). Se o número de falhas consecutivas for maior que a configuração de número de falhas toleradas, esse ponto de extremidade será marcado como não íntegro.

Em todos os casos, o Gerenciador de tráfego investiga de vários locais e a determinação de falha consecutiva ocorre dentro de cada região. Isso também significa que os pontos de extremidade estão recebendo investigações de integridade do Gerenciador de tráfego com uma frequência maior do que a configuração usada para o intervalo de investigação.

>[!NOTE]
>Para o protocolo de monitoramento HTTP ou HTTPS, uma prática comum no lado do ponto de extremidade é implementar uma página personalizada em seu aplicativo, por exemplo,/Health.aspx. Usando esse caminho para monitoramento, você pode executar verificações específicas do aplicativo, como verificação de contadores de desempenho ou verificação da disponibilidade do banco de dados. Com base nessas verificações personalizadas, a página retorna um código de status HTTP apropriado.

Todos os pontos de extremidade em um perfil do Gerenciador de tráfego compartilham configurações de monitoramento. Se precisar usar configurações de monitoramento diferentes para pontos de extremidade diferentes, você poderá criar [perfis aninhados do Gerenciador de tráfego](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Status do ponto de extremidade e perfil

Você pode habilitar e desabilitar os perfis e os pontos de extremidade do Gerenciador de tráfego. No entanto, uma alteração no status do ponto de extremidade também pode ocorrer como resultado de configurações e processos automatizados do Gerenciador de tráfego.

### <a name="endpoint-status"></a>Estado do ponto final

Você pode habilitar ou desabilitar um ponto de extremidade específico. O serviço subjacente, que ainda pode estar íntegro, não é afetado. Alterar o status do ponto de extremidade controla a disponibilidade do ponto de extremidade no perfil do Gerenciador de tráfego. Quando um status de ponto de extremidade é desabilitado, o Gerenciador de tráfego não verifica sua integridade e o ponto de extremidade não é incluído em uma resposta DNS.

### <a name="profile-status"></a>Status do perfil

Usando a configuração status do perfil, você pode habilitar ou desabilitar um perfil específico. Enquanto o status do ponto de extremidade afeta um único ponto de extremidade, o status do perfil afeta todo o perfil, incluindo todos os pontos de extremidades. Quando você desabilita um perfil, os pontos de extremidade não são verificados quanto à integridade e nenhum ponto de extremidade é incluído em uma resposta DNS. Um código de resposta [NXDOMAIN](https://tools.ietf.org/html/rfc2308) é retornado para a consulta DNS.

### <a name="endpoint-monitor-status"></a>Status do monitor de ponto de extremidade

O status do monitor de ponto de extremidade é um valor gerado pelo Gerenciador de tráfego que mostra o status do ponto de extremidade. Você não pode alterar essa configuração manualmente. O status do monitor do ponto de extremidade é uma combinação dos resultados do monitoramento do ponto de extremidade e do status do ponto de extremidade configurado. Os valores possíveis do status do monitor de ponto de extremidade são mostrados na tabela a seguir:

| Status do perfil | Estado do ponto final | Status do monitor de ponto de extremidade | Notas |
| --- | --- | --- | --- |
| Desativado |Enabled |Inativa |O perfil foi desabilitado. Embora o status do ponto de extremidade esteja habilitado, o status do perfil (desabilitado) tem precedência. Os pontos de extremidade em perfis desabilitados não são monitorados. Um código de resposta NXDOMAIN é retornado para a consulta DNS. |
| &lt;outro&gt; |Desativado |Desativado |O ponto de extremidade foi desabilitado. Os pontos de extremidade desabilitados não são monitorados. O ponto de extremidade não está incluído nas respostas DNS, portanto, não recebe tráfego. |
| Enabled |Enabled |Online |O ponto de extremidade é monitorado e está íntegro. Ele é incluído nas respostas DNS e pode receber tráfego. |
| Enabled |Enabled |Degradado |As verificações de integridade de monitoramento do ponto de extremidade estão falhando. O ponto de extremidade não está incluído nas respostas DNS e não recebe tráfego. <br>Uma exceção a isso é se todos os pontos de extremidade estiverem degradados; nesse caso, todos eles serão considerados retornados na resposta da consulta).</br>|
| Enabled |Enabled |CheckingEndpoint |O ponto de extremidade é monitorado, mas os resultados da primeira investigação ainda não foram recebidos. CheckingEndpoint é um estado temporário que geralmente ocorre imediatamente após a adição ou habilitação de um ponto de extremidade no perfil. Um ponto de extremidade nesse estado é incluído nas respostas DNS e pode receber tráfego. |
| Enabled |Enabled |Parada |O aplicativo Web para o qual o ponto de extremidade aponta não está em execução. Verifique as configurações do aplicativo Web. Isso também pode acontecer se o ponto de extremidade for do tipo ponto de extremidade aninhado e o perfil filho estiver desabilitado ou estiver inativo. <br>Um ponto de extremidade com um status parado não é monitorado. Ele não está incluído nas respostas DNS e não recebe tráfego. Uma exceção a isso é se todos os pontos de extremidade estiverem degradados; nesse caso, todos eles serão considerados retornados na resposta da consulta.</br>|

Para obter detalhes sobre como o status do monitor de ponto de extremidade é calculado para pontos de extremidades aninhados, consulte [perfis aninhados do Gerenciador de tráfego](traffic-manager-nested-profiles.md).

>[!NOTE]
> Um status de monitor de ponto de extremidade parado poderá ocorrer no serviço de aplicativo se seu aplicativo Web não estiver em execução na camada Standard ou superior. Para obter mais informações, consulte [integração do Gerenciador de tráfego com o serviço de aplicativo](/azure/app-service/web-sites-traffic-manager).

### <a name="profile-monitor-status"></a>Status do monitor de perfil

O status do monitor de perfil é uma combinação do status do perfil configurado e os valores de status do monitor do ponto de extremidade para todos os pontos de extremidade. Os valores possíveis são descritos na tabela a seguir:

| Status do perfil (conforme configurado) | Status do monitor de ponto de extremidade | Status do monitor de perfil | Notas |
| --- | --- | --- | --- |
| Desativado |&lt;qualquer&gt; um ou um perfil sem pontos de extremidade definidos. |Desativado |O perfil foi desabilitado. |
| Enabled |O status de pelo menos um ponto de extremidade está degradado. |Degradado |Examine os valores de status do ponto de extremidade individual para determinar quais pontos de extremidades exigem mais atenção. |
| Enabled |O status de pelo menos um ponto de extremidade está online. Nenhum ponto de extremidade tem um status degradado. |Online |O serviço está aceitando tráfego. Não são necessárias mais ações. |
| Enabled |O status de pelo menos um ponto de extremidade é CheckingEndpoint. Nenhum ponto de extremidade está no status online ou degradado. |Verificando pontos |Esse estado de transição ocorre quando um perfil é criado ou habilitado. A integridade do ponto de extremidade está sendo verificada pela primeira vez. |
| Enabled |Os status de todos os pontos de extremidade no perfil são desabilitados ou interrompidos ou o perfil não tem pontos de extremidade definidos. |Inativa |Nenhum ponto de extremidade está ativo, mas o perfil ainda está habilitado. |

## <a name="endpoint-failover-and-recovery"></a>Failover e recuperação de ponto de extremidade

O Gerenciador de tráfego verifica periodicamente a integridade de cada ponto de extremidade, incluindo pontos de extremidades não íntegros. O Gerenciador de tráfego detecta quando um ponto de extremidade se torna íntegro e o coloca de volta em rotação.

Um ponto de extremidade não está íntegro quando ocorre um dos seguintes eventos:

- Se o protocolo de monitoramento for HTTP ou HTTPS:
    - Uma resposta diferente de 200 ou uma resposta que não inclua o intervalo de status especificado na configuração **intervalos de códigos de status esperados** , será recebida (incluindo um código 2xx diferente ou um redirecionamento 301/302).
- Se o protocolo de monitoramento for TCP: 
    - Uma resposta diferente de ACK ou SYN-ACK é recebida em resposta à solicitação SYN enviada pelo Gerenciador de tráfego para tentar um estabelecimento de conexão.
- Cedido. 
- Qualquer outro problema de conexão que resulte no ponto de extremidade não está acessível.

Para obter mais informações sobre como solucionar problemas de verificações com falha, consulte [Solucionando problemas de status degradado no Gerenciador de tráfego do Azure](traffic-manager-troubleshooting-degraded.md). 

A linha do tempo na figura a seguir é uma descrição detalhada do processo de monitoramento do ponto de extremidade do Gerenciador de tráfego que tem as seguintes configurações: o protocolo de monitoramento é HTTP, o intervalo de investigação é de 30 segundos, o número de falhas toleradas é 3, o valor de tempo limite é de 10 segundos e o TTL do DNS é de 30 segundos.

![Failover de ponto de extremidade do Gerenciador de tráfego e sequência de failback](./media/traffic-manager-monitoring/timeline.png)

**Figuras  Failover de ponto de extremidade do Gerenciador de tráfego e sequência de recuperação**

1. **OBTER**. Para cada ponto de extremidade, o sistema de monitoramento do Traffic Manager executa uma solicitação GET no caminho especificado nas configurações de monitoramento.
2. **200 OK ou o intervalo de códigos personalizado especificou configurações de monitoramento de perfil do Gerenciador de tráfego** . O sistema de monitoramento espera um HTTP 200 OK ou a mensagem de configurações de monitoramento do perfil do Gerenciador de tráfego especificada no intervalo de códigos personalizado para ser retornada em 10 segundos. Quando ele recebe essa resposta, ele reconhece que o serviço está disponível.
3. **30 segundos entre cheques**. A verificação de integridade do ponto de extremidade é repetida a cada 30 segundos.
4. **Serviço indisponível**. O serviço se torna indisponível. O Gerenciador de tráfego não saberá até a próxima verificação de integridade.
5. **Tenta acessar o caminho de monitoramento**. O sistema de monitoramento executa uma solicitação GET, mas não recebe uma resposta dentro do período de tempo limite de 10 segundos (como alternativa, uma resposta não 200 pode ser recebida). Em seguida, ele tenta mais três vezes, em intervalos de 30 segundos. Se uma das tentativas for bem-sucedida, o número de tentativas será redefinido.
6. **Status definido como degradado**. Após uma quarta falha consecutiva, o sistema de monitoramento marca o status do ponto de extremidade indisponível como degradado.
7. O **tráfego é desviado para outros pontos de extremidade**. Os servidores de nome DNS do Traffic Manager são atualizados e o Gerenciador de tráfego não retorna mais o ponto de extremidade em resposta às consultas DNS. Novas conexões são direcionadas para outros pontos de extremidade disponíveis. No entanto, as respostas DNS anteriores que incluem esse ponto de extremidade ainda podem ser armazenadas em cache por servidores DNS recursivos e clientes DNS. Os clientes continuam a usar o ponto de extremidade até que o cache DNS expire. Como o cache DNS expira, os clientes fazem novas consultas DNS e são direcionados a diferentes pontos de extremidade. A duração do cache é controlada pela configuração TTL no perfil do Gerenciador de tráfego, por exemplo, 30 segundos.
8. As **verificações de integridade continuam**. O Gerenciador de tráfego continua verificando a integridade do ponto de extremidade enquanto ele tem um status degradado. O Gerenciador de tráfego detecta quando o ponto de extremidade retorna à integridade.
9. O **serviço volta a ficar online**. O serviço se torna disponível. O ponto de extremidade mantém seu status degradado no Gerenciador de tráfego até que o sistema de monitoramento execute sua próxima verificação de integridade.
10. O **tráfego para o serviço**é retomado. O Traffic Manager envia uma solicitação GET e recebe uma resposta de status de 200 OK. O serviço retornou a um estado íntegro. Os servidores de nome do Gerenciador de tráfego são atualizados e começam a entregar o nome DNS do serviço em respostas DNS. O tráfego retorna ao ponto de extremidade, uma vez que as respostas de DNS em cache que retornam outros pontos de extremidades expiram e, conforme as conexões existentes com outros pontos de extremidade, são encerradas.

    > [!NOTE]
    > Como o Gerenciador de tráfego funciona no nível do DNS, ele não pode influenciar as conexões existentes com nenhum ponto de extremidade. Quando ele direciona o tráfego entre os pontos de extremidade (por configurações de perfil alteradas ou durante o failover ou failback), o Gerenciador de tráfego direciona novas conexões para os pontos de extremidade disponíveis. No entanto, outros pontos de extremidade podem continuar a receber tráfego por meio de conexões existentes até que essas sessões sejam encerradas. Para permitir que o tráfego dissipe de conexões existentes, os aplicativos devem limitar a duração da sessão usada com cada ponto de extremidade.

## <a name="traffic-routing-methods"></a>Métodos de roteamento de tráfego

Quando um ponto de extremidade tem um status degradado, ele não é mais retornado em resposta a consultas DNS. Em vez disso, um ponto de extremidade alternativo é escolhido e retornado. O método de roteamento de tráfego configurado no perfil determina como o ponto de extremidade alternativo é escolhido.

* **Prioridade**. Os pontos de extremidade formam uma lista priorizada. O primeiro ponto de extremidade disponível na lista é sempre retornado. Se um status de ponto de extremidade for degradado, o próximo ponto de extremidade disponível será retornado.
* **Ponderado**. Qualquer ponto de extremidade disponível é escolhido aleatoriamente com base em seus pesos atribuídos e os pesos dos outros pontos de extremidade disponíveis.
* **Desempenho**. O ponto de extremidade mais próximo ao usuário final é retornado. Se esse ponto de extremidade não estiver disponível, o Gerenciador de tráfego moverá o tráfego para os pontos de extremidade na região do Azure mais próxima. Você pode configurar planos de failover alternativos para o roteamento de tráfego de desempenho usando [perfis aninhados do Gerenciador de tráfego](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geográfico**. O ponto de extremidade mapeado para servir a localização geográfica com base no IP da solicitação de consulta é retornado. Se esse ponto de extremidade não estiver disponível, outro ponto de extremidade não será selecionado para failover, pois uma localização geográfica pode ser mapeada somente para um ponto de extremidade em um perfil (mais detalhes estão nas [perguntas frequentes](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Como prática recomendada, ao usar o roteamento geográfico, recomendamos que os clientes usem perfis aninhados do Gerenciador de tráfego com mais de um ponto de extremidade como pontos de extremidades do perfil.
* Vários **valores** São retornados vários pontos de extremidade mapeados para endereços IPv4/IPv6. Quando uma consulta é recebida para esse perfil, os pontos de extremidade íntegros são retornados com base na **contagem máxima de registros no** valor de resposta especificado. O número padrão de respostas é dois pontos de extremidade.
* **Sub-rede** O ponto de extremidade mapeado para um conjunto de intervalos de endereços IP é retornado. Quando uma solicitação é recebida desse endereço IP, o ponto de extremidade retornado é aquele mapeado para esse endereço IP. 

Para obter mais informações, consulte [métodos de roteamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md).

> [!NOTE]
> Uma exceção ao comportamento normal de roteamento de tráfego ocorre quando todos os pontos de extremidade qualificados têm um status degradado. O Gerenciador de tráfego faz uma tentativa de "melhor esforço" e *responde como se todos os pontos de extremidade de status degradados realmente estiverem em um estado online*. Esse comportamento é preferível à alternativa, o que seria não retornar nenhum ponto de extremidade na resposta DNS. Os pontos de extremidade desabilitados ou interrompidos não são monitorados, portanto, eles não são considerados qualificados para o tráfego.
>
> Essa condição é normalmente causada pela configuração imprópria do serviço, como:
>
> * Uma lista de controle de acesso [ACL] bloqueando as verificações de integridade do Gerenciador de tráfego.
> * Uma configuração incorreta da porta de monitoramento ou do protocolo no perfil do Gerenciador de tráfego.
>
> A consequência desse comportamento é que, se as verificações de integridade do Traffic Manager não estiverem configuradas corretamente, elas poderão ser exibidas no roteamento de tráfego, como *se* o Gerenciador de tráfego estivesse funcionando corretamente. No entanto, nesse caso, o failover de ponto de extremidade não pode ocorrer, o que afeta a disponibilidade geral do aplicativo. É importante verificar se o perfil mostra um status online, não um status degradado. Um status online indica que as verificações de integridade do Traffic Manager estão funcionando conforme o esperado.

Para obter mais informações sobre a solução de problemas de verificações de integridade com falha, consulte [Solucionando problemas de status degradado no Gerenciador de tráfego do Azure](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>FAQs

* [O Gerenciador de tráfego é resiliente a falhas de região do Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-traffic-manager-resilient-to-azure-region-failures)

* [Como a escolha do local do grupo de recursos afeta o Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Como fazer determinar a integridade atual de cada ponto de extremidade?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-determine-the-current-health-of-each-endpoint)

* [Posso monitorar pontos de extremidade HTTPS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-monitor-https-endpoints)

* [Eu uso um endereço IP ou um nome DNS ao adicionar um ponto de extremidade?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Que tipos de endereços IP posso usar ao adicionar um ponto de extremidade?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Posso usar diferentes tipos de endereçamento de ponto de extremidade em um único perfil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [O que acontece quando o tipo de registro de uma consulta de entrada é diferente do tipo de registro associado ao tipo de endereçamento dos pontos de extremidade?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Posso usar um perfil com pontos de extremidade endereçados por IPv4/IPv6 em um perfil aninhado?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Parei um ponto de extremidade do aplicativo Web no meu perfil do Gerenciador de tráfego, mas não estou recebendo nenhum tráfego mesmo depois de reiniciá-lo. Como posso corrigir isso?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Posso usar o Gerenciador de tráfego mesmo que meu aplicativo não tenha suporte para HTTP ou HTTPS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Quais respostas específicas são necessárias do ponto de extremidade ao usar o monitoramento de TCP?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Com que velocidade o Gerenciador de tráfego move meus usuários para fora de um ponto de extremidade não íntegro?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Como especificar diferentes configurações de monitoramento para diferentes pontos de extremidade em um perfil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Como posso atribuir cabeçalhos HTTP às verificações de integridade do Gerenciador de tráfego aos meus pontos de extremidade?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Qual cabeçalho de host as verificações de integridade do ponto de extremidade usam?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-host-header-do-endpoint-health-checks-use)

* [Quais são os endereços IP dos quais as verificações de integridade se originam?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Quantas verificações de integridade no meu ponto de extremidade posso esperar do Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Como posso ser notificado se um dos meus pontos de extremidade ficar inativo?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Passos Seguintes

Saiba [como funciona o Gerenciador de tráfego](traffic-manager-how-it-works.md)

Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de tráfego

Saiba como [criar um perfil do Gerenciador de tráfego](traffic-manager-manage-profiles.md)

[Solucionar problemas de status degradado](traffic-manager-troubleshooting-degraded.md) em um ponto de extremidade do Gerenciador de tráfego
