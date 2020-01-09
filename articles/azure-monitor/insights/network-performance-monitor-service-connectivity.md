---
title: Conectividade do serviço de solução Monitor de Desempenho de Rede-Log Analytics do Azure
description: Use o recurso monitor de conectividade de serviço no Monitor de Desempenho de Rede para monitorar a conectividade de rede para qualquer ponto de extremidade que tenha uma porta TCP aberta.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 11975a86b5d9135b42dd51bdd787c2f76cabe259
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551510"
---
# <a name="service-connectivity-monitor"></a>Monitor de Conectividade do Serviço

Você pode usar o recurso do monitor de conectividade de serviço no [Monitor de desempenho de rede](network-performance-monitor.md) para monitorar a conectividade de rede para qualquer ponto de extremidade que tenha uma porta TCP aberta. Esses pontos de extremidade incluem sites, aplicativos SaaS, aplicativos PaaS e bancos de dados SQL. 

Você pode executar as seguintes funções com o monitor de conectividade de serviço: 

- Monitore a conectividade de rede para seus aplicativos e serviços de rede de várias filiais ou locais. Aplicativos e serviços de rede incluem o Office 365, Dynamics CRM, aplicativos internos de linha de negócios e bancos de dados SQL.
- Use testes internos para monitorar a conectividade de rede com os pontos de extremidade do Office 365 e do Dynamics 365. 
- Determine o tempo de resposta, a latência de rede e a perda de pacotes enfrentados durante a conexão com o ponto de extremidade.
- Determine se o desempenho insatisfatório do aplicativo é devido à rede ou devido a algum problema no fim do provedor do aplicativo.
- Identifique pontos de acesso na rede que podem estar causando mau desempenho de aplicativo exibindo a latência contribuída por cada salto em um mapa de topologia.


![Monitor de Conectividade do Serviço](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Configuração 
Para abrir a configuração para Monitor de Desempenho de Rede, abra a [solução monitor de desempenho de rede](network-performance-monitor.md) e selecione **Configurar**.

![Configurar o Monitor de Desempenho de Rede](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Configurar agentes de Log Analytics para monitoramento
Habilite as seguintes regras de firewall nos nós usados para monitoramento para que a solução possa descobrir a topologia de seus nós para o ponto de extremidade de serviço: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Criar testes do monitor de conectividade de serviço 

Comece a criar seus testes para monitorar a conectividade de rede com os pontos de extremidade de serviço.

1. Selecione a guia **Monitor de conectividade de serviço** .
2. Selecione **Adicionar teste**e insira o nome e a descrição do teste. Você pode criar um máximo de 450 testes por espaço de trabalho. 
3. Selecione o tipo de teste:<br>

    * Selecione **Web** para monitorar a conectividade com um serviço que responde a solicitações HTTP/S, como outlook.office365.com ou Bing.com.<br>
    * Selecione **rede** para monitorar a conectividade com um serviço que responde às solicitações TCP, mas não responde a solicitações HTTP/S, como um SQL Server, servidor FTP ou porta SSH. 
    * Por exemplo: para criar um teste na Web para uma conta de armazenamento de BLOBs, selecione **Web** e insira destino como *yourstorageaccount*. blob.Core.Windows.net. Da mesma forma, você pode criar testes para outros armazenamentos de tabelas, armazenamento de fila e arquivos do Azure usando [este link.](https://docs.microsoft.com/azure/storage/common/storage-account-overview#storage-account-endpoints)
4. Se você não quiser executar medidas de rede, como latência de rede, perda de pacotes e descoberta de topologia, desmarque a caixa de seleção **executar medidas de rede** . Mantenha-o selecionado para obter o máximo benefício do recurso. 
5. Em **destino**, insira a URL/FQDN/endereço IP para o qual você deseja monitorar a conectividade de rede.
6. Em **número da porta**, insira o número da porta do serviço de destino. 
7. Em **frequência de teste**, insira um valor para a frequência com que você deseja que o teste seja executado. 
8. Selecione os nós dos quais você deseja monitorar a conectividade de rede com o serviço. Verifique se o número de agentes adicionados por teste é menor que 150. Qualquer agente pode testar o máximo de 150 pontos de extremidade/agentes.

    >[!NOTE]
    > Para nós baseados no Windows Server, a funcionalidade usa solicitações baseadas em TCP para executar as medições de rede. Para nós baseados no cliente Windows, a funcionalidade usa solicitações baseadas em ICMP para executar as medições de rede. Em alguns casos, o aplicativo de destino bloqueia solicitações baseadas em ICMP de entrada quando os nós são baseados no cliente Windows. A solução não pode executar medições de rede. Recomendamos que você use nós baseados no Windows Server nesses casos. 

9. Se você não quiser criar eventos de integridade para os itens selecionados, desmarque **habilitar monitoramento de integridade nos destinos cobertos por este teste**. 
10. Escolha as condições de monitoramento. Você pode definir limites personalizados para a geração de eventos de integridade inserindo valores de limite. Sempre que o valor da condição ficar acima do limite selecionado para o par de rede ou de sub-rede selecionado, um evento de integridade será gerado. 
11. Selecione **salvar** para salvar a configuração. 

    ![Configurações de teste do monitor de conectividade de serviço](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Instruções 

Vá para a exibição de painel do Monitor de Desempenho de Rede. Para obter um resumo da integridade dos diferentes testes que você criou, consulte a página **Monitor de conectividade de serviço** . 

![Página do monitor de conectividade de serviço](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Selecione o bloco para exibir os detalhes dos testes na página **testes** . Na tabela à esquerda, você pode exibir a integridade pontual e o valor do tempo de resposta do serviço, a latência da rede e a perda de pacotes para todos os testes. Use o controle gravador de estado de rede para exibir o instantâneo de rede em outro momento no passado. Selecione o teste na tabela que você deseja investigar. Nos gráficos no painel à direita, você pode exibir a tendência histórica dos valores de perda, latência e tempo de resposta. Selecione o link **detalhes do teste** para exibir o desempenho de cada nó.

![Testes do monitor de conectividade de serviço](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

Na exibição **nós de teste** , você pode observar a conectividade de rede de cada nó. Selecione o nó que tem degradação de desempenho. Este é o nó em que o aplicativo está sendo executado lentamente.

Determine se o desempenho insatisfatório do aplicativo é devido à rede ou a um problema no fim do provedor de aplicativo, observando a correlação entre o tempo de resposta do aplicativo e a latência de rede. 

* **Problema do aplicativo:** Um pico no tempo de resposta, mas a consistência na latência de rede sugere que a rede está funcionando bem e o problema pode ser devido a um problema na finalização do aplicativo. 

    ![Problema do aplicativo monitor de conectividade de serviço](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Problema de rede:** Um pico no tempo de resposta acompanhado de um pico correspondente na latência de rede sugere que o aumento no tempo de resposta pode ser devido a um aumento na latência de rede. 

    ![Problema de rede do monitor de conectividade de serviço](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Depois de determinar que o problema é devido à rede, selecione o link de exibição de **topologia** para identificar o salto problemático no mapa de topologia. Um exemplo é mostrado na imagem a seguir. Fora da latência total de 105 a MS entre o nó e o ponto de extremidade do aplicativo, 96 MS é devido ao salto marcado em vermelho. Depois de identificar o salto problemático, você pode tomar uma ação corretiva. 

![Testes do monitor de conectividade de serviço](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnósticos 

Se você observar uma anormalidade, siga estas etapas:

* Se o tempo de resposta do serviço, a perda de rede e a latência forem mostrados como NA, um ou mais dos seguintes motivos pode ser a causa:

    - O aplicativo está inoperante.
    - O nó usado para verificar a conectividade de rede com o serviço está inoperante.
    - O destino inserido na configuração de teste está incorreto.
    - O nó não tem nenhuma conectividade de rede.

* Se um tempo de resposta de serviço válido for mostrado, mas a perda de rede, bem como a latência, for mostrada como NA, um ou mais dos seguintes motivos pode ser a causa:

    - Se o nó usado para verificar a conectividade de rede com o serviço for um computador cliente Windows, o serviço de destino está bloqueando solicitações ICMP ou um firewall de rede está bloqueando solicitações ICMP originadas do nó.
    - A caixa de seleção **executar medidas de rede** está em branco na configuração de teste. 

* Se o tempo de resposta do serviço for NA, mas a perda de rede e a latência forem válidas, o serviço de destino poderá não ser um aplicativo Web. Edite a configuração de teste e escolha o tipo de teste como **rede** em vez de **Web**. 

* Se o aplicativo estiver em execução lenta, determine se o desempenho insatisfatório do aplicativo é devido à rede ou a um problema no fim do provedor de aplicativos.

## <a name="gcc-office-urls-for-us-government-customers"></a>URLs de GCC Office para clientes do governo dos EUA
Para a região da Virgínia do governo dos EUA, somente as URLs do DOD são NPM internas. Os clientes que usam URLs GCC precisam criar testes personalizados e adicionar cada URL individualmente.

| Campo | GCC |
|:---   |:--- |
| Portal do Office 365 e compartilhado | portal.apps.mil |
| Identidade e autenticação do Office 365 | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www. office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS Teams | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Passos seguintes
[Pesquisar logs](../../azure-monitor/log-query/log-query-overview.md) para exibir registros de dados de desempenho de rede detalhados.
