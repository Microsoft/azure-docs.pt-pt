---
title: Conectividade do serviço de suporte de desempenho da rede - Azure Log Analytics
description: Utilize a capacidade do Monitor de Conectividade do Serviço no Monitor de Desempenho da Rede para monitorizar a conectividade da rede a qualquer ponto final que tenha uma porta TCP aberta.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 93f3820b7cf1db85b9ff4cd514fe22efc75a90d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79249246"
---
# <a name="service-connectivity-monitor"></a>Monitor de Conectividade do Serviço

Pode utilizar a capacidade do Monitor de Conectividade do Serviço no Monitor de Desempenho da [Rede](network-performance-monitor.md) para monitorizar a conectividade da rede a qualquer ponto final que tenha uma porta TCP aberta. Estes pontos finais incluem websites, aplicações SaaS, aplicações PaaS e bases de dados SQL. 

Pode desempenhar as seguintes funções com o Monitor de Conectividade do Serviço: 

- Monitorize a conectividade da rede com as suas aplicações e serviços de rede a partir de várias agências ou locais. As aplicações e serviços de rede incluem o Office 365, Dynamics CRM, aplicações internas de linha de negócioe bases de dados SQL.
- Utilize testes incorporados para monitorizar a conectividade da rede com o Office 365 e os pontos finais Da Dinâmica 365. 
- Determine o tempo de resposta, a latência da rede e a perda de pacotes experimentadas ao ligar-se ao ponto final.
- Determine se o fraco desempenho da aplicação se deve à rede ou devido a algum problema no final do fornecedor de aplicações.
- Identifique pontos quentes na rede que possam estar a causar um fraco desempenho de aplicação ao ver a latência contribuída por cada salto num mapa de topologia.


![Monitor de Conectividade do Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Configuração 
Para abrir a configuração do Monitor de Desempenho da Rede, abra a [solução](network-performance-monitor.md) do Monitor de Desempenho da Rede e selecione **Configurar**.

![Configurar o Monitor de Desempenho de Rede](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Configure os agentes de Log Analytics para monitorização
Ative as seguintes regras de firewall nos nódosos utilizados para monitorização para que a solução possa descobrir a topologia dos seus nódosos até ao ponto final do serviço: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Criar testes de Monitor de Conectividade de Serviço 

Comece a criar os seus testes para monitorizar a conectividade da rede com os pontos finais do serviço.

1. Selecione o separador Monitor de Conectividade do **Serviço.**
2. Selecione **Adicionar Teste**e introduza o nome e a descrição do teste. Pode criar no máximo 450 testes por espaço de trabalho. 
3. Selecione o tipo de teste:<br>

    * Selecione **Web** para monitorizar a conectividade com um serviço que responda a pedidos HTTP/S, como outlook.office365.com ou bing.com.<br>
    * Selecione **Rede** para monitorizar a conectividade com um serviço que responda aos pedidos de TCP mas não responda a pedidos HTTP/S, tais como um servidor SQL, servidor FTP ou porta SSH. 
    * Por exemplo: Para criar um teste web para uma conta de armazenamento blob, selecione **Web** e introduza o alvo como *a sua conta de armazenamento*.blob.core.windows.net. Da mesma forma, pode criar testes para outros armazenamento de mesa, armazenamento de filas e Ficheiros Azure utilizando [este link.](https://docs.microsoft.com/azure/storage/common/storage-account-overview#storage-account-endpoints)
4. Se não quiser efetuar medições de rede, tais como latência de rede, perda de pacotes e descoberta de topologia, limpe a caixa de verificação de medições de **rede Perform.** Mantenha-o selecionado para obter o máximo benefício da capacidade. 
5. No **Target,** introduza o endereço URL/FQDN/IP para o qual pretende monitorizar a conectividade da rede.
6. No **número do Porto,** insira o número de porta do serviço alvo. 
7. Na Frequência de **Teste,** introduza um valor para a frequência com que pretende que o teste seja executado. 
8. Selecione os nódosos a partir dos quais pretende monitorizar a conectividade da rede ao serviço. Certifique-se de que o número de agentes adicionados por teste é inferior a 150. Qualquer agente pode testar no máximo 150 pontos/agentes finais.

    >[!NOTE]
    > Para os nós baseados no servidor do Windows, a capacidade utiliza pedidos baseados em TCP para realizar as medições de rede. Para os nós baseados no cliente do Windows, a capacidade utiliza pedidos baseados no ICMP para realizar as medições de rede. Em alguns casos, a aplicação alvo bloqueia os pedidos baseados no ICMP quando os nós são baseados no cliente do Windows. A solução é incapaz de efetuar medições de rede. Recomendamos que utilize nós baseados no servidor do Windows nestes casos. 

9. Se não quiser criar eventos de saúde para os itens que seleciona, limpe a monitorização da **saúde dos alvos abrangidos por este teste**. 
10. Escolha as condições de monitorização. Pode estabelecer limiares personalizados para a geração de eventos de saúde, entrando em valores-limiar. Sempre que o valor da circunstância ultrapassa o limiar selecionado para o par de rede ou subrede selecionado, gera-se um evento de saúde. 
11. Selecione **Guardar** para salvar a configuração. 

    ![Configurações de teste do Monitor de Conectividade do Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Instruções 

Vá à vista do painel de instrumentos do Monitor de Desempenho da Rede. Para obter um resumo da saúde dos diferentes testes que criou, veja a página do Monitor de Conectividade do **Serviço.** 

![Página do Monitor de Conectividade do Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Selecione o azulejo para visualizar os detalhes dos testes na página **de Testes.** Na mesa à esquerda, pode ver a saúde pontual e o valor do tempo de resposta ao serviço, latência da rede e perda de pacotes para todos os testes. Utilize o controlo do gravador de rede para visualizar o instantâneo da rede noutra altura no passado. Selecione o teste na tabela que pretende investigar. Nas tabelas do painel à direita, pode-se ver a tendência histórica dos valores de perda, latência e tempo de resposta. Selecione a ligação Detalhes de **Teste** para visualizar o desempenho de cada nó.

![Testes de Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

Na vista **Test Nodes,** pode observar a conectividade da rede a partir de cada nó. Selecione o nó que tem degradação do desempenho. Este é o nó onde a aplicação é observada como sendo lenta.

Determine se o fraco desempenho da aplicação se deve à rede ou a um problema no fim do fornecedor de aplicações observando a correlação entre o tempo de resposta da aplicação e a latência da rede. 

* **Emissão de candidatura:** Um aumento no tempo de resposta, mas a coerência na latência da rede sugere que a rede está a funcionar bem e que o problema pode dever-se a um problema no final da aplicação. 

    ![Problema de aplicação do Monitor de Conectividade do Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Problema da rede:** Um pico no tempo de resposta acompanhado de um aumento correspondente na latência da rede sugere que o aumento do tempo de resposta pode dever-se a um aumento da latência da rede. 

    ![Problema da rede de monitor de conectividade de serviço](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Depois de determinar que o problema se deve à rede, selecione o link de visão **topologia** para identificar o salto problemático no mapa de topologia. Um exemplo é mostrado na imagem seguinte. Da latência total de 105 ms entre o nó e o ponto final da aplicação, 96 ms é devido ao lúpulo marcado a vermelho. Depois de identificar o salto problemático, pode tomar medidas corretivas. 

![Testes de Monitor de Conectividade de Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnóstico 

Se observar uma anormalidade, siga estes passos:

* Se o tempo de resposta ao serviço, a perda da rede e a latência forem mostrados como NA, uma ou mais das seguintes razões podem ser a causa:

    - A aplicação está em baixo.
    - O nó usado para verificar a conectividade da rede com o serviço está em baixo.
    - O alvo introduzido na configuração do teste está incorreto.
    - O nó não tem conectividade de rede.

* Se for demonstrado um tempo de resposta ao serviço válido, mas a perda de rede e a latência são mostradas como NA, uma ou mais das seguintes razões podem ser a causa:

    - Se o nó utilizado para verificar a conectividade da rede com o serviço for uma máquina cliente Do Windows, ou o serviço alvo está a bloquear pedidos do ICMP ou uma firewall de rede está a bloquear pedidos do ICMP originários do nó.
    - A caixa de verificação de **medições** de rede Executar está em branco na configuração do teste. 

* Se o tempo de resposta ao serviço for NA, mas a perda de rede e a latência são válidas, o serviço-alvo pode não ser uma aplicação web. Editar a configuração do teste e escolher o tipo de teste como **Rede** em vez de **Web**. 

* Se a aplicação estiver a decorrer lentamente, determine se o fraco desempenho da aplicação se deve à rede ou a um problema no final do fornecedor de aplicações.

## <a name="gcc-office-urls-for-us-government-customers"></a>GCC Office URLs para clientes do Governo dos EUA
Para a região do Governo dos EUA, a Virgínia, apenas urLs DOD são incorporados em NPM. Os clientes que usam URLs GCC precisam de criar testes personalizados e adicionar cada URL individualmente.

| Campo | GCC |
|:---   |:--- |
| Office 365 Portal e partilhado | portal.apps.mil |
| Escritório 365 auth e identidade | Login.microsoftonline.us <br> Api.login.microsoftonline.com <br> Clientconfig.microsoftonline-p.net <br> login.microsoftonline.com <br> Login.microsoftonline-p.com <br> Login.windows.net <br> loginex.microsoftonline.com <br> Login-us.microsoftonline.com <br> Nexus.microsoftonline-p.com <br> Mscrl.microsoft.com <br> secure.aadcdn.microsoftonline-p.com |
| Office Online | Adminwebservice.gov.us.microsoftonline.com <br>  Adminwebservice-s1-bn1a.microsoftonline.com <br> Adminwebservice-s1-dm2a.microsoftonline.com <br> Becws.gov.us.microsoftonline.com <br> Provisioningapi.gov.us.microsoftonline.com <br> Officehome.msocdn.us <br> prod.msocdn.us <br> Portal.office365.us <br> Webshell.suite.office365.us <br> * www.office365.us <br> Activation.sls.microsoft.com <br> Crl.microsoft.com <br> Go.microsoft.com <br> Insertmedia.bing.office.net <br> Ocsa.officeapps.live.com <br> Ocsredir.officeapps.live.com <br> Ocws.officeapps.live.com <br> office15client.microsoft.com <br>Officecdn.microsoft.com <br> Officecdn.microsoft.com.edgesuite.net <br> Officepreviewredir.microsoft.com <br> officeredir.microsoft.com <br> Ols.officeapps.live.com  <br> R.office.microsoft.com <br> Cdn.odc.officeapps.live.com <br> Odc.officeapps.live.com <br> Officeclient.microsoft.com |
| Exchange Online | Outlook.office365.us <br> Attachments.office365-net.us <br> Autodiscover-s.office365.us <br> Manage.office365.us <br> Scc.office365.us |
| Equipas MS | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Passos seguintes
[Pesquisar registos](../../azure-monitor/log-query/log-query-overview.md) para visualizar registos de desempenho de rede detalhados.
