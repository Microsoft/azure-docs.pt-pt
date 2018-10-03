---
title: Solução de Monitor de desempenho no Azure Log Analytics de rede | Documentos da Microsoft
description: Utilize a capacidade de Monitor de conectividade do serviço no Monitor de desempenho de rede para monitorizar a conectividade de rede para qualquer ponto final que tem uma porta TCP aberta.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.component: ''
ms.openlocfilehash: 04e348d2b144bdb8fd626c52f990a560d76c08a2
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044676"
---
# <a name="service-connectivity-monitor"></a>Monitor de Conectividade de Serviço

Pode utilizar a capacidade de Monitor de conectividade do serviço no [Monitor de desempenho de rede](log-analytics-network-performance-monitor.md) para monitorizar a conectividade de rede para qualquer ponto final que tem uma porta TCP aberta. Esses pontos de extremidade incluem Web sites, aplicações SaaS, PaaS aplicações e bases de dados SQL. 

Pode executar as seguintes funções com o Monitor de conectividade do serviço: 

- Monitorize a conectividade de rede às suas aplicações e serviços de rede de várias filiais ou localizações. Aplicações e serviços de rede incluem o Office 365, Dynamics CRM, aplicações de linha de negócio internas e bases de dados SQL.
- Testes internos de utilização para monitorizar a conectividade de rede para pontos finais do Office 365 e Dynamics 365. 
- Determine o tempo de resposta, latência de rede e perda de pacotes experiente ao ligar-se para o ponto final.
- Determine se o desempenho da aplicação insatisfatório é devido à rede ou devido a algum problema no final do fornecedor de aplicações.
- Identifica pontos de acesso na rede que podem estar provocando o desempenho da aplicação insatisfatório visualizando a latência contribuída cada salto num mapa de topologia.


![Monitor de Conectividade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Configuração 
Para abrir a configuração de Monitor de desempenho de rede, abra a [solução Monitor de desempenho de rede](log-analytics-network-performance-monitor.md) e selecione **configurar**.

![Configurar o Monitor de desempenho de rede](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Configurar agentes do Operations Management Suite para monitorização
Ative as seguintes regras de firewall em nós utilizados para monitorização de modo a que a solução pode detetar a topologia de seus nós para o ponto final de serviço: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Criar testes de Monitor de conectividade do serviço 

Comece a criar seus testes para monitorizar a conectividade de rede para os pontos finais de serviço.

1. Selecione o **Monitor de conectividade do serviço** separador.
2. Selecione **Add Test**e introduza o nome do teste e a descrição. 
3. Selecione o tipo de teste:<br>

    * Selecione **Web** para monitorizar a conectividade a um serviço que responde a pedidos HTTP/S, como outlook.office365.com ou bing.com.<br>
    * Selecione **rede** para monitorizar a conectividade a um serviço que responde a pedidos TCP, mas não responder a pedidos HTTP/S, como um SQL server, o servidor de FTP ou a porta SSH. 
4. Se não pretender executar medições de rede, tais como latência de rede, perda de pacotes e descoberta de topologia, desmarque a **executar medições de rede** caixa de verificação. Ele selecionado para obter o máximo benefício da capacidade de manter. 
5. Na **destino**, introduza o endereço URL/FQDN/IP ao qual pretende monitorizar a conectividade de rede.
6. Na **número de porta**, introduza o número de porta de serviço de destino. 
7. Na **frequência de teste**, introduza um valor para a frequência com que pretende que o teste para ser executado. 
8. Selecione os nós partir do qual pretende monitorizar a conectividade de rede ao serviço. 

    >[!NOTE]
    > Para nós baseado no servidor do Windows, o recurso utiliza solicitações com base em TCP para efetuar as medições de rede. Para nós baseada no cliente do Windows, o recurso utiliza solicitações com base em ICMP para executar as medições de rede. Em alguns casos, o aplicativo de destino bloqueia as solicitações de entrada com base em ICMP quando os nós são baseados no cliente do Windows. A solução é não é possível executar medições de rede. Recomendamos que utilize nós baseado no servidor do Windows em tais casos. 

9. Se não pretender criar eventos de estado de funcionamento para os itens que selecionar, desmarque **ativar a monitorização de estado de funcionamento nos destinos abrangidos por este teste**. 
10. Escolha a monitorização de condições. Pode definir limiares personalizados para a geração de evento de estado de funcionamento ao introduzir os valores de limiar. Sempre que o valor da condição mais do seu limiar selecionado para a rede selecionada ou par sub-rede, é gerado um evento de estado de funcionamento. 
11. Selecione **guardar** para guardar a configuração. 

    ![Configurações de teste do Monitor de conectividade do serviço](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Instruções 

Ir para a vista de dashboard de Monitor de desempenho de rede. Para obter um resumo do Estado de funcionamento dos testes diferentes que criou, observe a **Monitor de conectividade do serviço** página. 

![Página de Monitor de conectividade do serviço](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Selecione o mosaico para ver os detalhes dos testes no **testes** página. Na tabela à esquerda, pode ver o estado de funcionamento do ponto anterior no tempo e o valor do tempo de resposta do serviço, latência de rede e perda de pacotes de todos os testes. Use o controle de gravação de estado de rede para ver o instantâneo de rede em outro momento no passado. Selecione o teste na tabela que pretende investigar. Nos gráficos no painel à direita, pode ver a tendência histórica dos valores de tempo de resposta, latência e perda. Selecione o **detalhes de teste** ligação para ver o desempenho de cada nó.

![Testes de Monitor de conectividade do serviço](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

Na **nós de teste** vista, pode observar que a conectividade de rede de cada nó. Selecione o nó que tem uma degradação do desempenho. Este é o nó em que o aplicativo é observado estar em execução lenta.

Determine se o desempenho da aplicação insatisfatório é devido à rede ou um problema no final do fornecedor de aplicativo ao observar a correlação entre o tempo de resposta do aplicativo e a latência de rede. 

* **Problema de aplicação:** um pico no tempo de resposta, mas a consistência na latência de rede sugere que a rede estiver funcionando e que o problema poderá ser devido a um problema no final do aplicativo. 

    ![Problema de aplicativo de Monitor de conectividade do serviço](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

* **Problema de rede:** um pico no tempo de resposta acompanhado de um pico correspondente na latência de rede sugere que o aumento no tempo de resposta pode ser devido a um aumento na latência de rede. 

    ![Problema de Monitor de conectividade do serviço de rede](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

Depois de determinar que o problema é devido a rede, selecione o **topologia** ligação de vista para identificar o salto problemáticos do mapa de topologia. Um exemplo é mostrado na imagem seguinte. Sem a latência total 105 ms entre o nó e o ponto final da aplicação, 96 ms é devido ao salto marcado em vermelho. Depois de identificar o salto problemáticos, pode tomar medidas corretivas. 

![Testes de Monitor de conectividade do serviço](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnóstico 

Se observar um abnormality, siga estes passos:

* Se o tempo de resposta do serviço, a perda de rede e a latência, são apresentadas como ND, um ou mais das seguintes razões poderão ser a causa:

    - O aplicativo está inativo.
    - O nó utilizado para a verificar a conectividade de rede para o serviço está inativo.
    - O destino introduzido na configuração de teste está incorreto.
    - O nó não tem qualquer conectividade de rede.

* Se um tempo de resposta do serviço válida é mostrado, mas a perda de rede, bem como a latência, é apresentada como ND, um ou mais das seguintes razões poderão ser a causa:

    - Se o nó utilizado para a verificar a conectividade de rede para o serviço for uma máquina de cliente do Windows, o serviço de destino está a bloquear pedidos ICMP ou uma firewall de rede está a bloquear pedidos ICMP que têm a partir do nó de origem.
    - O **executar medições de rede** caixa de verificação está em branco na configuração do teste. 

* Se, mas o tempo de resposta do serviço é NA perda de rede, bem como latência é válida, o serviço de destino não pode ser um aplicativo web. Editar a configuração de teste e escolha o tipo de teste como **rede** em vez de **Web**. 

* Se a aplicação está em execução lenta, determine se o desempenho da aplicação insatisfatório é devido à rede ou um problema no final do fornecedor de aplicações.


## <a name="next-steps"></a>Passos Seguintes
[Pesquisar registos](log-analytics-log-searches.md) para ver os registos de dados de desempenho de rede detalhada.
