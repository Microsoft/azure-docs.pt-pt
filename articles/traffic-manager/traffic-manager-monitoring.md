---
title: Monitorização de pontos finais Gestor de tráfego do Azure | Documentos da Microsoft
description: Este artigo pode ajudá-lo a compreender como o Gestor de tráfego utiliza a monitorização do ponto final e ativação pós-falha do ponto de extremidade automática, para ajudar os clientes do Azure, implementar aplicações de elevada disponibilidade
services: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: kumud
ms.openlocfilehash: 083bdf9c5aec640fbbd7757b307ac47178e0b14b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076144"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitorização de pontos finais do Gestor de tráfego

O Gestor de tráfego do Azure inclui a monitorização de ponto final incorporado e de ativação pós-falha do ponto de extremidade automática. Esta funcionalidade ajuda-o a fornecer aplicações de elevada disponibilidade que são resilientes a falhas de ponto de extremidade, incluindo falhas de região do Azure.

## <a name="configure-endpoint-monitoring"></a>Configurar a monitorização do ponto final

Para configurar a monitorização do ponto final, tem de especificar as seguintes definições no perfil do Traffic Manager:

* **Protocolo**. Selecione HTTP, HTTPS ou TCP como protocolo que o Gestor de tráfego utiliza quando o ponto final de pesquisa para verificar o respetivo estado de funcionamento. Monitorização de HTTPS não verifica se o seu certificado SSL não é válido – só verifica que o certificado está presente.
* **Porta**. Escolha a porta utilizada para o pedido.
* **Caminho**. Esta definição de configuração é válida apenas para os protocolos HTTP e HTTPS, para que o caminho de especificar a definição é necessária. Fornecer esta definição para o TCP resultados de protocolo num erro de monitorização. Para o protocolo HTTP e HTTPS, dê o caminho relativo e o nome da página Web ou o ficheiro que acede a monitorização. Uma barra (/) é uma entrada válida para o caminho relativo. Este valor indica que o ficheiro está no diretório de raiz (predefinição).
* **Definições de cabeçalho personalizado** esta definição ajuda a adicionar cabeçalhos HTTP específicos para o estado de funcionamento de configuração verifica que o Gestor de tráfego envia para pontos finais sob um perfil. Os cabeçalhos personalizados podem ser especificados a um nível de perfil até ser aplicável a todos os pontos finais esse perfil and / or num nível de ponto final aplicável apenas para esse ponto final. Pode usar os cabeçalhos personalizados para ter as verificações do Estado de funcionamento para pontos finais num ambiente multi-inquilino ser roteados corretamente ao especificar um cabeçalho de anfitrião ao seu destino. Também pode utilizar esta definição, adicionando os cabeçalhos exclusivos que podem ser utilizados para identificar o Gestor de tráfego teve origem pedidos de HTTP (S) e processa-os de forma diferente. Pode especificar até oito seprated de pares de valor do cabeçalho: por uma vírgula. Por exemplo, "header1:value1, header2:value2". 
* **Intervalos de código de estado de espera** esta definição permite-lhe especificar vários intervalos de código de êxito no formato 200 299, 301 301. Se estes códigos de estado são recebidos como resposta a partir de um ponto de extremidade quando uma verificação de estado de funcionamento é iniciada, o Gestor de tráfego marca esses pontos de extremidade como bom estado de funcionamento. Pode especificar um máximo de 8 intervalos de código de estado. Esta definição é aplicável apenas para o protocolo HTTP e HTTPS e para todos os pontos finais. Esta definição é no nível de perfil do Gestor de tráfego e por predefinição, o valor de 200 é definido como o código de estado de êxito.
* **Intervalo de pesquisa**. Este valor Especifica a frequência com que um ponto de extremidade é verificado para seu estado de funcionamento de um agente de pesquisa do Gestor de tráfego. Pode especificar dois valores aqui: 30 segundos (pesquisa normal) e 10 segundos (pesquisa rápida). Se não forem fornecidos valores, o perfil define como um valor predefinido de 30 segundos. Visite o [preço de Gestor de tráfego](https://azure.microsoft.com/pricing/details/traffic-manager) página para obter mais informações sobre os preços de pesquisa rápida.
* **Pela tolerar o número de falhas de**. Este valor Especifica como várias falhas de um agente de pesquisa do Gestor de tráfego tolera antes de os marcar esse ponto final danificada. O valor pode variar entre 0 e 9. Um valor de 0 significa que uma única falha de monitorização pode causar o ponto de extremidade seja marcado como mau estado de funcionamento. Se for especificado nenhum valor, ele usa o valor predefinido de 3.
* **Tempo limite da pesquisa**. Esta propriedade especifica o período de tempo que o agente do Gestor de tráfego de pesquisa deve aguardar antes de considerar que verificam a uma falha quando uma sonda de verificação de estado de funcionamento é enviada para o ponto final. Se o intervalo de pesquisa é definido como 30 segundos, em seguida, pode definir o valor de tempo limite entre 5 e 10 segundos. Se for especificado nenhum valor, ele usa um valor predefinido de 10 segundos. Se o intervalo de pesquisa está definido para 10 segundos, em seguida, pode definir o valor de tempo limite entre 5 e 9 segundos. Se for especificado nenhum valor de tempo limite, ele usa um valor padrão de 9 segundos.

    ![Monitorização de pontos finais do Gestor de tráfego](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Figura:  Monitorização de pontos finais do Gestor de tráfego**

## <a name="how-endpoint-monitoring-works"></a>Como funciona a monitorização do ponto final

Se o protocolo de monitorização é definido como HTTP ou HTTPS, o agente do Gestor de tráfego de pesquisa faz um pedido GET para o ponto de extremidade usando o protocolo, porta e caminho relativo, tendo em conta. Se ele recebe de volta uma resposta 200 OK, ou qualquer uma das respostas configuradas no **esperado o código de estado \*intervalos**, em seguida, esse ponto final é considerado em bom estado de funcionamento. Se a resposta é um valor diferente, ou, se for recebida nenhuma resposta dentro do período de tempo limite especificado, em seguida, o Gestor de tráfego de pesquisa agente tenta novamente, de acordo com a definição de pela tolerar número de falhas (sem tenta restabelecer é feita se esta definição é 0). Se o número de falhas consecutivas é maior do que a definição de pela tolerar número de falhas, em seguida, esse ponto final está marcado como mau estado de funcionamento. 

Se o protocolo de monitorização é TCP, o agente de pesquisa do Gestor de tráfego inicia um pedido de ligação de TCP utilizando a porta especificada. Se o ponto final de responder ao pedido com uma resposta para estabelecer a ligação, essa verificação de estado de funcionamento está marcada como um êxito e o agente de pesquisa do Gestor de tráfego repõe a conexão TCP. Se a resposta é um valor diferente, ou se for recebida nenhuma resposta dentro do período de tempo limite especificado, o Gestor de tráfego de pesquisa agente tenta novamente, de acordo com a definição de pela tolerar número de falhas (sem tenta restabelecer é feita se esta definição é 0). Se o número de falhas consecutivas é maior do que a definição de pela tolerar número de falhas, em seguida, esse ponto final é marcado mau estado de funcionamento.

Em todos os casos, o Gestor de tráfego sondas a partir de várias localizações e a determinação de falhas consecutivas acontece em cada região. Isso também significa que os pontos finais estão a receber sondas de estado de funcionamento do Gestor de tráfego com uma freqüência mais alta que a definição utilizada para o intervalo de pesquisa.

>[!NOTE]
>Para HTTP ou HTTPS, protocolo de monitorização, uma prática comum no lado do ponto final é implementar uma página personalizada dentro de seu aplicativo - por exemplo, /health.aspx. Utilizar este caminho para a monitorização, pode executar verificações específicas do aplicativo, como a verificação de contadores de desempenho ou verificar a disponibilidade de base de dados. Com base nestas verificações personalizadas, a página devolve um código de status HTTP adequado.

Todos os pontos finais num perfil de Gestor de tráfego partilham as definições de monitorização. Se precisar usar diferentes configurações de monitorização para diferentes pontos de extremidade, pode criar [aninhada de perfis do Gestor de tráfego](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Estado de perfil e ponto final

Pode ativar e desativar os perfis do Gestor de tráfego e pontos de extremidade. No entanto, uma alteração no estado do ponto final também poderão ocorrer como um resultado do Gestor de tráfego automatizada as definições e processos.

### <a name="endpoint-status"></a>Estado do ponto final 

Pode ativar ou desativar um ponto de extremidade específico. O serviço subjacente, que ainda possa ser bom estado de funcionamento, não é afetado. Alterar o estado do ponto de extremidade controla a disponibilidade do ponto de extremidade no perfil do Gestor de tráfego. Quando um Estado de ponto final está desabilitado, o Gestor de tráfego não verifica se o seu estado de funcionamento e o ponto final não está incluído numa resposta DNS.

### <a name="profile-status"></a>Estado do perfil

Utilizar a definição do Estado do perfil, pode ativar ou desativar um perfil específico. Enquanto o estado do ponto final afeta um único ponto final, o estado do perfil afeta o perfil completo, incluindo todos os pontos finais. Quando desativa um perfil, os pontos finais não são verificados relativamente à integridade e não existem pontos finais estão incluídos numa resposta DNS. Uma [NXDOMAIN](https://tools.ietf.org/html/rfc2308) código de resposta é retornado para a consulta DNS.

### <a name="endpoint-monitor-status"></a>Estado do monitor de ponto final

Estado do monitor de ponto final é um valor gerado pelo Gestor de tráfego, que mostra o estado do ponto de extremidade. Não é possível alterar esta definição manualmente. O estado do monitor de ponto final é uma combinação dos resultados de monitorização do ponto final e o estado de ponto final configurado. Os valores possíveis de estado do monitor de ponto final são mostrados na tabela a seguir:

| Estado do perfil | Estado do ponto final  | Estado do monitor de ponto final | Notas |
| --- | --- | --- | --- |
| Desativado |Ativado |Inativa |O perfil foi desativado. Embora o estado de ponto final estiver ativado, o estado do perfil (desativado) tem precedência. Pontos finais nos perfis desativados não são monitorizados. Um código de resposta NXDOMAIN é retornado para a consulta DNS. |
| &lt;any&gt; |Desativado |Desativado |O ponto final foi desativado. Pontos de extremidade desativados não são monitorizados. O ponto final não está incluído nas respostas DNS, por conseguinte, não receber o tráfego. |
| Ativado |Ativado |Online |O ponto final está a ser monitorizado e está em bom estado. Ele está incluído nas respostas DNS e pode receber o tráfego. |
| Ativado |Ativado |Degradado |Ponto final de monitorização do Estado de funcionamento verificações estão a falhar. O ponto final não está incluído nas respostas DNS e não recebe o tráfego. <br>Uma exceção é se todos os pontos finais estão degradados, caso em que todas elas são consideradas a ser devolvido na resposta da consulta).</br>|
| Ativado |Ativado |CheckingEndpoint |O ponto final está a ser monitorizado, mas os resultados da primeira sonda ainda não foram recebidos. CheckingEndpoint é um estado temporário que normalmente ocorre imediatamente após a adição ou ativar um ponto de extremidade no perfil. Um ponto de extremidade neste estado está incluído nas respostas DNS e pode receber o tráfego. |
| Ativado |Ativado |Parada |A aplicação de web ou serviço cloud que o ponto final aponta para não está em execução. Verifique as definições de aplicação de web ou serviço cloud. Também pode acontecer se o ponto final é do tipo aninhado ponto de extremidade e o perfil de subordinado está desativado ou está inativo. <br>Um ponto final com um estado parado, não é monitorizado. Ele não está incluído nas respostas DNS e não recebe o tráfego. Uma exceção é se todos os pontos finais estão degradados, caso em que todos eles serão considerados a ser devolvido na resposta da consulta.</br>|

Para obter detalhes sobre como o estado do monitor de ponto final é calculado para aninhados pontos de extremidade, consulte [aninhada de perfis do Gestor de tráfego](traffic-manager-nested-profiles.md).

>[!NOTE]
> Um Estado de monitor do ponto final parado pode acontecer no serviço de aplicações, se a sua aplicação web não está em execução no escalão Standard ou superior. Para obter mais informações, consulte [integração do Gestor de tráfego com o serviço de aplicações](/azure/app-service/web-sites-traffic-manager).

### <a name="profile-monitor-status"></a>Estado do monitor de perfil

O estado do monitor de perfil é uma combinação do Estado do perfil configurado e os valores de estado do monitor de ponto final para todos os pontos finais. Os valores possíveis são descritos na tabela a seguir:

| Estado do perfil (conforme configurado) | Estado do monitor de ponto final | Estado do monitor de perfil | Notas |
| --- | --- | --- | --- |
| Desativado |&lt;qualquer&gt; ou um perfil com não existem pontos finais definidos. |Desativado |O perfil foi desativado. |
| Ativado |O estado de, pelo menos, um ponto final está degradado. |Degradado |Reveja os valores de estado do ponto de extremidade individuais para determinar quais pontos de extremidade ainda mais necessitam de atenção. |
| Ativado |O estado de, pelo menos, um ponto final é Online. Não existem pontos finais possuem um status de Degraded. |Online |O serviço está a aceitar o tráfego. Não são necessárias mais ações. |
| Ativado |O estado de, pelo menos, um ponto final é CheckingEndpoint. Não existem pontos finais estão em Estado Online ou Degraded. |CheckingEndpoints |O estado de transição ocorre quando um perfil se criadas ou ativadas. O estado de funcionamento do ponto final está a ser verificado pela primeira vez. |
| Ativado |Os Estados de todos os pontos finais no perfil são desativado ou parado ou o perfil não tem definidos pontos finais. |Inativa |Não existem pontos finais estão ativos, mas o perfil ainda está ativado. |

## <a name="endpoint-failover-and-recovery"></a>Ativação pós-falha do ponto final e recuperação

O Gestor de tráfego verifica periodicamente o estado de funcionamento de cada ponto de extremidade, incluindo os pontos finais de mau estado de funcionamento. Gestor de tráfego detetar quando um ponto final se tornar íntegro novamente e coloca-o de volta em rotação.

Um ponto de extremidade é mau estado de funcionamento quando qualquer um dos seguintes eventos ocorra:
- Se o protocolo de monitorização é HTTP ou HTTPS:
    - Uma resposta que não 200 ou uma resposta que não inclua o intervalo de estado especificado no **esperado intervalos de código de estado** definir, é recebida (incluindo um código de 2xx diferente ou um redirecionamento 301/302).
- Se o protocolo de monitorização é TCP: 
    - É recebida uma resposta que não seja ACK ou SYN-ACK em resposta ao pedido de SINCRONIZAÇÃO enviado pelo Gestor de tráfego para tentar um estabelecimento da conexão.
- Tempo limite. 
- Qualquer outro problema de ligação resultante o ponto final a não ser acessível.

Para obter mais informações sobre as verificações de falha na resolução de problemas, consulte [estado de resolução de problemas degradado no Gestor de tráfego do Azure](traffic-manager-troubleshooting-degraded.md). 

A linha cronológica na figura a seguir é uma descrição detalhada do processo de monitorização do ponto final do Gestor de tráfego que tem as seguintes definições: a monitorização de protocolo é HTTP, o intervalo de pesquisa é de 30 segundos, número de falhas tolerados é 3, valor de tempo limite é de 10 segundos e TTL de DNS é 30 segundos.

![Sequência de ativação pós-falha e reativação pós-falha de ponto final do Gestor de tráfego](./media/traffic-manager-monitoring/timeline.png)

**Figura:  Tráfego manager endpoint ativação pós-falha e recuperação sequência**

1. **GET**. Para cada ponto de extremidade, o sistema de monitoramento do Gestor de tráfego efetua um pedido GET no caminho especificado nas definições de monitorização.
2. **Intervalo de código OK ou personalizadas de 200 especificadas definições de monitorização de perfil do Gestor de tráfego** . O sistema de monitorização espera um HTTP 200 OK ou o ou intervalo de código personalizado especificado a mensagem de definições a serem retornados dentro de 10 segundos da monitorização de perfil do Gestor de tráfego. Ao receber esta resposta, ele reconhece que o serviço está disponível.
3. **30 segundos entre verificações**. A verificação de estado de funcionamento do ponto final é repetida a cada 30 segundos.
4. **Serviço indisponível**. O serviço fica indisponível. O Gestor de tráfego não saberá até a próxima verificação de estado de funcionamento.
5. **Tenta aceder ao caminho de monitorização**. O sistema de monitorização efetua um pedido GET, mas não recebeu uma resposta dentro do período de tempo limite de 10 segundos (em alternativa, que não 200 recebida uma resposta pode ser). Em seguida, tenta três vezes mais, em intervalos de 30 segundos. Se um das tentativas for bem-sucedida, o número de tentativas é reposto.
6. **Estado definido como Degraded**. Após a quarta falha consecutiva, o sistema de monitorização marca o estado do ponto de extremidade indisponível como Degraded.
7. **O tráfego é desviado para outros pontos de extremidade**. Os servidores de nomes do DNS do Gestor de tráfego são atualizados e o Gestor de tráfego já não retorna o ponto final em resposta a consultas DNS. Novas ligações são direcionadas para outros, pontos de extremidade disponíveis. No entanto, as respostas DNS anteriores que incluem esse ponto de extremidade podem ainda ser armazenadas em cache por servidores DNS recursivos e clientes DNS. Os clientes continuam a utilizar o ponto final até que expire a cache DNS. Como o cache DNS expira, os clientes fazer novas consultas DNS e sejam direcionados para diferentes pontos de extremidade. A duração de cache é controlada pela definição da TTL no perfil do Gestor de tráfego, por exemplo, de 30 segundos.
8. **Verificações de estado de funcionamento continuar**. O Gestor de tráfego continuará a verificar o estado de funcionamento do ponto de extremidade, enquanto tem um Degraded status. Gestor de tráfego detetar quando o ponto final retorna ao estado de funcionamento.
9. **Serviço fica online novamente**. O serviço torna-se disponível. O ponto final mantém o estado de Degraded no Gestor de tráfego, até que o sistema de monitorização realiza sua próxima verificação de estado de funcionamento.
10. **O tráfego para o serviço CVS**. O Gestor de tráfego envia um obter pedido e recebe uma resposta de 200 estado OK. O serviço devolveu um bom estado. Os servidores de nomes do Gestor de tráfego são atualizados, e começar a distribuir o nome do serviço DNS nas respostas DNS. Tráfego devolve para o ponto final como as respostas DNS em cache que devolvem outros pontos de extremidade expirem e, como ligações existentes para outros pontos finais estão terminados.

    > [!NOTE]
    > Como o Gestor de tráfego funciona ao nível do DNS, não é possível influenciar a ligações existentes para qualquer ponto final. Quando este direciona o tráfego entre pontos de extremidade (seja pelas definições de perfil alteradas ou durante a ativação pós-falha ou reativação pós-falha), o Gestor de tráfego direciona novas ligações a pontos finais disponíveis. No entanto, os outros pontos de extremidade poderão continuar a receber tráfego através de ligações existentes até que essas sessões estão terminadas. Para ativar o tráfego drenar a partir de ligações existentes, os aplicativos devem limitar a duração de sessão utilizada com cada ponto de extremidade.

## <a name="traffic-routing-methods"></a>Métodos de encaminhamento de tráfego

Quando um ponto de extremidade tem o estado Degraded, já não é devolvido em resposta a consultas DNS. Em vez disso, um ponto final alternativo é escolhido e retornado. O método de encaminhamento de tráfego de mensagens em fila configurado no perfil determina como o ponto final alternativo é escolhido.

* **Prioridade**. Uma lista prioritária de formulário de pontos de extremidade. O primeiro ponto de extremidade disponível na lista é sempre retornado. Se um Estado de ponto final está degradado, o próximo ponto de extremidade disponível é devolvido.
* **Weighted**. Qualquer ponto de extremidade disponível é escolhido aleatoriamente com base nos seus atribuídos pesos e as ponderações de outros disponíveis pontos finais.
* **Desempenho**. O ponto final mais próximo do utilizador final é devolvido. Se esse ponto final não estiver disponível, o Gestor de tráfego move o tráfego para os pontos de extremidade na região do Azure mais próxima seguinte. Pode configurar planos de ativação pós-falha alternativo para o encaminhamento de tráfego de desempenho, utilizando [aninhada de perfis do Gestor de tráfego](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geográfica**. O ponto final mapeado para servir a localização geográfica baseada no pedido de consulta IP é devolvido. Se esse ponto final não estiver disponível, outro ponto final não será selecionado para ativação pós-falha, uma vez que uma localização geográfica pode ser mapeada apenas para um ponto final num perfil (mais detalhes estão no [FAQ](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Como melhor prática, quando utiliza o encaminhamento geográfico, recomendamos que os clientes para utilizar perfis do Gestor de tráfego aninhados com mais de um ponto de extremidade como os pontos finais do perfil.
* **MultiValue** vários pontos de extremidade mapeados para endereços IPv4/IPv6 são devolvidos. Quando uma consulta é recebida para este perfil, os pontos finais de bom estado de funcionamento são devolvidos com base no **contagem de registos de máximo em resposta** valor que especificou. O número predefinido de respostas é dois pontos de extremidade.
* **Sub-rede** é devolvido o ponto final mapeado para um conjunto de intervalos de endereços IP. Quando é recebido um pedido desse endereço IP, o ponto final devolveu é a que é mapeada para esse endereço IP. 

Para obter mais informações, consulte [métodos de encaminhamento de tráfego do Gestor de tráfego](traffic-manager-routing-methods.md).

> [!NOTE]
> Uma exceção ao comportamento normal de encaminhamento de tráfego ocorre quando todos os pontos de extremidade elegíveis tem um Estado degradado. Torna o Gestor de tráfego "melhor esforço" tenta e *responde como se todos os Degraded status pontos finais, na verdade, estão num estado online*. Este comportamento é preferível a alternativa, o que seria não devolveu qualquer ponto de extremidade na resposta DNS. Desativado ou parado pontos de extremidade não são monitorizados, portanto, eles não são considerados elegíveis para o tráfego.
>
> Esta condição é normalmente provocada por uma configuração incorreta do serviço, tais como:
>
> * Verifica uma lista de controlo de acesso [ACL] bloquear o estado de funcionamento do Gestor de tráfego.
> * Uma configuração incorreta de porta ou protocolo no perfil do Gestor de tráfego de monitorização.
>
> A conseqüência desse comportamento é que, se verificações de estado de funcionamento do Gestor de tráfego não estão configuradas corretamente, ele poderá aparecer de tráfego de encaminhamento como se o Gestor de tráfego *é* a funcionar corretamente. No entanto, neste caso, ativação pós-falha do ponto final não pode acontecer que afeta a disponibilidade geral do aplicativo. É importante verificar que o perfil mostra um Estado Online, não um status de Degraded. Um Estado Online indica que as verificações de estado de funcionamento do Gestor de tráfego estão a funcionar conforme esperado.

Para obter mais informações sobre resolução de problemas de falha verificações de estado de funcionamento, consulte [estado de resolução de problemas degradado no Gestor de tráfego do Azure](traffic-manager-troubleshooting-degraded.md).



## <a name="next-steps"></a>Passos Seguintes

Saiba [como funciona o Gestor de tráfego](traffic-manager-how-it-works.md)

Saiba mais sobre o [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportada pelo Gestor de tráfego

Saiba como [criar um perfil do Gestor de tráfego](traffic-manager-manage-profiles.md)

[Resolver problemas do Estado de Degraded](traffic-manager-troubleshooting-degraded.md) num ponto de extremidade do Gestor de tráfego
