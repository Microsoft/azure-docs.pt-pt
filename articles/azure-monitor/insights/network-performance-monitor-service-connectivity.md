---
title: Conectividade de serviço de serviço de solução monitor de desempenho de rede - Azure Log Analytics
description: Utilize a capacidade do Monitor de Conectividade de Serviço no Monitor de Desempenho da Rede para monitorizar a conectividade da rede a qualquer ponto final que tenha uma porta TCP aberta.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 18c07266cec68aaf6e95fe4085b9d21244fecff4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761044"
---
# <a name="service-connectivity-monitor"></a>Monitor de Conectividade do Serviço

Pode utilizar a capacidade do Monitor de Conectividade de Serviço no [Monitor de Desempenho da Rede](network-performance-monitor.md) para monitorizar a conectividade da rede a qualquer ponto final que tenha uma porta TCP aberta. Esses pontos finais incluem websites, aplicações SaaS, aplicações PaaS e bases de dados SQL. 

Pode executar as seguintes funções com o Monitor de Conectividade de Serviço: 

- Monitorize a conectividade da rede com as suas aplicações e serviços de rede a partir de várias sucursais ou locais. Aplicações e serviços de rede incluem Microsoft 365, Dynamics CRM, aplicações internas de linha de negócios e bases de dados SQL.
- Utilize testes incorporados para monitorizar a conectividade da rede com os pontos finais microsoft 365 e Dynamics 365. 
- Determine o tempo de resposta, a latência da rede e a perda de pacote sofrida ao ligar-se ao ponto final.
- Determinar se o fraco desempenho da aplicação se deve à rede ou devido a algum problema no final do fornecedor de aplicações.
- Identifique os pontos quentes na rede que possam estar a causar um fraco desempenho da aplicação ao visualizar a latência contribuida por cada salto num mapa de topologia.


![Monitor de Conectividade do Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Configuração 
Para abrir a configuração para Monitor de Desempenho de Rede, abra a [solução monitor de desempenho](network-performance-monitor.md) da rede e selecione **Configure**.

![Configurar o Monitor de Desempenho de Rede](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Configure os agentes do Log Analytics para monitorização
Ative as seguintes regras de firewall nos nos nosmos utilizados para a monitorização para que a solução possa descobrir a topologia dos seus nos dois até ao ponto final de serviço: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Criar testes de Monitor de Conectividade de Serviço 

Comece a criar os seus testes para monitorizar a conectividade da rede com os pontos finais de serviço.

1. Selecione o separador **Monitor de Conectividade de Serviço.**
2. Selecione **Add Test**, e introduza o nome e descrição do teste. Pode criar um máximo de 450 testes por espaço de trabalho. 
3. Selecione o tipo de teste:<br>

    * Selecione **Web** para monitorizar a conectividade a um serviço que responda a pedidos HTTP/S, como outlook.office365.com ou bing.com.<br>
    * Selecione **Rede** para monitorizar a conectividade a um serviço que responda aos pedidos de TCP mas não responda a pedidos HTTP/S, tais como um servidor SQL, servidor FTP ou porta SSH. 
    * Por exemplo: Para criar um teste web para uma conta de armazenamento de bolhas, selecione **Web** e introduza o alvo como *sua conta*de blob.core.windows.net. Da mesma forma, pode criar testes para outros armazenamento de mesa, armazenamento de filas e Ficheiros Azure utilizando [este link.](../../storage/common/storage-account-overview.md#storage-account-endpoints)
4. Se não quiser efetuar medições de rede, tais como latência de rede, perda de pacotes e descoberta de topologia, limpe a caixa **de verificação de medições de rede Perform.** Mantenha-o selecionado para obter o máximo benefício da capacidade. 
5. No **Target,** insira o endereço URL/FQDN/IP ao qual pretende monitorizar a conectividade da rede.
6. No **número do Porto,** insira o número de porta do serviço alvo. 
7. Na **Frequência de Teste,** insira um valor para a frequência com que pretende que o teste seja executado. 
8. Selecione os nós a partir dos quais pretende monitorizar a conectividade da rede ao serviço. Certifique-se de que o número de agentes adicionados por teste é inferior a 150. Qualquer agente pode testar o máximo de 150 pontos finais/agentes.

    >[!NOTE]
    > Para os nós baseados no servidor do Windows, a capacidade utiliza pedidos baseados em TCP para executar as medições de rede. Para os nós baseados no cliente do Windows, a capacidade utiliza pedidos baseados em ICMP para efetuar as medições de rede. Em alguns casos, a aplicação-alvo bloqueia a entrada de pedidos baseados no ICMP quando os nós são baseados no cliente do Windows. A solução não é capaz de efetuar medições de rede. Recomendamos que utilize nós baseados no servidor do Windows nestes casos. 

9. Se não quiser criar eventos de saúde para os itens selecionados, **desimpam a monitorização da saúde nos alvos abrangidos por este teste**. 
10. Escolha as condições de monitorização. Pode definir limiares personalizados para a geração de eventos de saúde, introduzindo valores-limiar. Sempre que o valor da condição for superior ao limiar selecionado para o par de rede ou sub-rede selecionado, é gerado um evento de saúde. 
11. **Selecione Guardar** para guardar a configuração. 

    ![Configurações de teste do Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Instruções 

Aceda à vista do painel do Monitor de Desempenho da Rede. Para obter um resumo da saúde dos diferentes testes que criou, veja a página do Monitor de Conectividade de **Serviço.** 

![Página do Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Selecione o azulejo para ver os detalhes dos testes na página **Testes.** Na tabela à esquerda, pode ver a saúde pontual e o valor do tempo de resposta do serviço, latência da rede e perda de pacotes para todos os testes. Utilize o controlo do gravador de estado da rede para visualizar a imagem da rede noutra altura do passado. Selecione o teste na tabela que pretende investigar. Nas tabelas no painel à direita, pode-se ver a tendência histórica dos valores de perda, latência e tempo de resposta. Selecione o link **'Detalhes de Teste'** para visualizar o desempenho de cada nó.

![Testes do Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

Na vista **"Nós de Teste",** pode observar a conectividade da rede a partir de cada nó. Selecione o nó que tem degradação do desempenho. Este é o nó onde a aplicação é observada para estar a correr devagar.

Determinar se o fraco desempenho da aplicação se deve à rede ou a um problema no final do fornecedor de aplicações, observando a correlação entre o tempo de resposta da aplicação e a latência da rede. 

* **Emissão de aplicação:** Um pico no tempo de resposta, mas a consistência na latência da rede sugere que a rede está a funcionar bem e o problema pode dever-se a um problema no final da aplicação. 

    ![Problema de aplicação do Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Problema de rede:** Um pico no tempo de resposta acompanhado de um aumento correspondente na latência da rede sugere que o aumento do tempo de resposta pode ser devido a um aumento da latência da rede. 

    ![Problema de rede do Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Depois de determinar que o problema é por causa da rede, selecione a ligação de visualização **topologia** para identificar o salto problemático no mapa da topologia. Um exemplo é mostrado na imagem seguinte. Da latência total de 105 ms entre o nó e o ponto final da aplicação, 96 ms é devido ao lúpulo marcado a vermelho. Depois de identificar o salto problemático, pode tomar medidas corretivas. 

![Topologia do ponto final do Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnóstico 

Se observar uma anormalidade, siga estes passos:

* Se o tempo de resposta do serviço, a perda de rede e a latência forem apresentados como NA, uma ou mais das seguintes razões podem ser a causa:

    - A candidatura está em baixo.
    - O nó utilizado para verificar a conectividade da rede ao serviço está em baixo.
    - O alvo introduzido na configuração do teste está incorreto.
    - O nó não tem conectividade de rede.

* Se for mostrado um tempo de resposta de serviço válido, mas a perda de rede, bem como a latência, forem mostradas como NA, uma ou mais das seguintes razões podem ser a causa:

    - Se o nó utilizado para verificar a conectividade da rede ao serviço for uma máquina cliente Windows, ou o serviço-alvo está a bloquear pedidos de ICMP ou uma firewall de rede está a bloquear pedidos de ICMP originários do nó.
    - A caixa **de verificação de medições de rede Perform** está em branco na configuração do teste. 

* Se o tempo de resposta do serviço for NA, mas a perda de rede, bem como a latência, forem válidos, o serviço-alvo pode não ser uma aplicação web. Edite a configuração do teste e escolha o tipo de teste como **Rede** em vez de **Web**. 

* Se a aplicação estiver a correr lentamente, determine se o fraco desempenho da aplicação se deve à rede ou a um problema no final do fornecedor de aplicações.

## <a name="gcc-office-urls-for-us-government-customers"></a>URLs do Escritório GCC para clientes do Governo dos EUA
Para a região do governo dos EUA, apenas URLs DOD são NPM embutidos. Os clientes que usam URLs GCC precisam criar testes personalizados e adicionar cada URL individualmente.

| Campo | GCC |
|:---   |:--- |
| Portal do Escritório 365 e partilhado | portal.apps.mil |
| Auth e identidade do Escritório 365 | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www.office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| Equipas MS | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Passos seguintes
[Faça uma pesquisa de registos](../log-query/log-query-overview.md) para visualizar registos de dados de desempenho detalhados da rede.

