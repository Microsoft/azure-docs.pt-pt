---
title: Bloquear o tráfego de saída do ambiente de serviço de aplicações - Azure
description: Descreve como integrar o Firewall do Azure para proteger o tráfego de saída
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6ae7037ad4cd532b6661a56e6e37a88df3eb54a2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121711"
---
# <a name="locking-down-an-app-service-environment"></a>O bloqueio de um ambiente de serviço de aplicações

O ambiente de serviço de aplicações (ASE) tem um número de dependências externas, que requer acesso para funcionar corretamente. O ASE mora no cliente de rede Virtual do Azure (VNet). Os clientes têm de permitir o tráfego de dependência do ASE, o que é um problema para os clientes que pretende bloquear todos os saída da sua VNet.

Há um número de dependências de entrada que tenha um ASE. Não é possível enviar o tráfego de entrada de gestão através de um dispositivo de firewall. Os endereços de origem para este tráfego são conhecidos e são publicados na [endereços de gestão do ambiente de serviço de aplicações](https://docs.microsoft.com/azure/app-service/environment/management-addresses) documento. Pode criar regras do grupo de segurança de rede com essas informações para proteger o tráfego de entrada.

As dependências de saída do ASE quase que totalmente são definidas com FQDNs, que não têm endereços estáticos por trás delas. A falta de endereços estáticos significa que grupos de segurança de rede (NSGs) não pode ser utilizados para bloquear o tráfego de saída do ASE. Os endereços são alterados com frequência suficiente uma não é possível configurar regras com base na resolução atual e usá-lo para criar NSGs. 

A solução para proteger os endereços de saída está em utilização de um dispositivo de firewall que pode controlar o tráfego de saída com base nos nomes de domínio. Firewall do Azure pode restringir o tráfego de HTTP e HTTPS de saída com base no FQDN do destino.  

## <a name="configuring-azure-firewall-with-your-ase"></a>Configurar a Firewall do Azure com o seu ASE 

Os passos para bloquear a saída do ASE existente com o Firewall do Azure são:

1. Ative pontos finais de serviço para o SQL, armazenamento e o Hub de eventos na sub-rede do ASE. Para fazer isso, vá para o portal de rede > sub-redes e selecione eventhub Microsoft. SQL e Microsoft. Storage a partir da lista de pontos finais de serviço pendente. Quando tem pontos finais de serviço ativados para o Azure SQL, todas as dependências de SQL do Azure com as suas aplicações devem ser configuradas com pontos finais de serviço também. 

   ![Selecione os pontos finais de serviço][2]
  
1. Crie uma sub-rede denominada AzureFirewallSubnet na VNet onde existe o seu ASE. Siga as instruções a [documentação de Firewall do Azure](https://docs.microsoft.com/azure/firewall/) para criar a Firewall do Azure.
1. Desde a interface do Usuário de Firewall do Azure > regras > coleção de regras de aplicação, selecione coleção de regras de aplicação de adicionar. Forneça um nome, prioridade e defina permitir. Na secção de etiquetas FQDN, forneça um nome, defina os endereços de origem para o * e selecione a etiqueta de FQDN de ambiente de serviço de aplicações e a atualização do Windows. 
   
   ![Adicionar regra de aplicação][1]
   
1. Desde a interface do Usuário de Firewall do Azure > regras > coleção de regras de rede, selecione a coleção de regras de rede de adicionar. Forneça um nome, prioridade e defina permitir. Na secção regras, dê um nome, selecione **qualquer**, defina * para endereços de origem e destino e defina as portas para 123. Esta regra permite que o sistema execute a sincronização do relógio usando NTP. Crie outra regra da mesma forma, a porta 12000 para ajudar a triagem de problemas de sistema.

   ![Adicionar regra de rede do NTP][3]

1. Criar uma tabela de rotas com os endereços de gestão do [endereços de gestão do ambiente de serviço de aplicações]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) com um salto seguinte da Internet. As entradas de tabela de rota são necessárias para evitar problemas de encaminhamento assimétricos. Adicione rotas para as dependências de endereço IP indicadas abaixo nas dependências de endereço IP com um salto seguinte da Internet. Adicione uma rota de aplicação Virtual à sua tabela de rotas para 0.0.0.0/0 com o salto seguinte a ser o seu endereço IP privado do Firewall do Azure. 

   ![Criar uma tabela de rotas][4]
   
1. Atribua a tabela de rotas que criou para a sub-rede do ASE.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Implementar o ASE atrás de uma firewall

Os passos para implementar o ASE atrás de uma firewall são o mesmo que configurar o ASE existente com uma Firewall do Azure, exceto que terá de criar a sub-rede do ASE e, em seguida, siga os passos anteriores. Para criar o ASE numa sub-rede já existente, tem de utilizar um modelo do Resource Manager, conforme descrito no documento [criar o ASE com um modelo do Resource Manager](https://docs.microsoft.com/azure/app-service/environment/create-from-template).

## <a name="application-traffic"></a>Tráfego de aplicativo 

Os passos acima irão permitir que o ASE para operar sem problemas. Ainda tem de configurar as coisas para acomodar as necessidades da sua aplicação. Há dois problemas para aplicações num ASE que está configurado com a Firewall do Azure.  

- Dependências de aplicações tem de ser adicionadas para o Firewall do Azure ou a tabela de rotas. 
- As rotas têm de ser criadas para o tráfego de aplicativo evitar problemas de encaminhamento assimétrico

Se as aplicações têm dependências, terá de ser adicionado à Firewall do Azure. Crie regras de aplicações para permitir tráfego HTTP/HTTPS e regras de rede para todo o resto. 

Se sabe o intervalo de endereços que o tráfego de pedido de aplicação serão provenientes, pode adicionar que à tabela de rotas que está atribuído a sub-rede do ASE. Se o intervalo de endereços for grande ou não especificado, em seguida, pode utilizar uma aplicação de rede, como o Gateway de aplicação para que tenha um endereço para adicionar à sua tabela de rota. Para obter detalhes sobre como configurar um Gateway de aplicação com o ASE de ILB, leia [integrar o ASE de ILB com um Gateway de aplicação](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

![ASE com o fluxo de ligação de Firewall do Azure][5]

Esta utilização do Gateway de aplicação é apenas um exemplo de como configurar o seu sistema. Se seguir esse caminho, em seguida, precisaria adicionar uma rota para a tabela de rotas de sub-rede do ASE, para que o tráfego de resposta enviado para o Gateway de aplicação teria aceder aí diretamente. 

## <a name="logging"></a>Registo 

Firewall do Azure pode enviar registos ao armazenamento do Azure, Hub de eventos, ou registos do Azure Monitor. Para integrar a sua aplicação com qualquer destino suportado, aceda ao portal do Firewall do Azure > os registos de diagnóstico e ativar os registos para o destino pretendido. Se integrar com os registos do Azure Monitor, em seguida, pode ver o registo para qualquer tráfego enviado para o Firewall do Azure. Para ver o tráfego que está a ser negado, abra o portal de área de trabalho do Log Analytics > registos e introduza uma consulta como 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Integrar o Firewall do Azure com os registos do Azure Monitor é muito útil ao primeiro obter uma aplicação a funcionar quando não estiver ciente de todas as dependências de aplicativo. Pode saber mais sobre os registos do Azure Monitor de [analisar dados de registo no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
 
## <a name="dependencies"></a>Dependências

As seguintes informações só são necessário se pretender configurar uma aplicação de firewall que não seja a Firewall do Azure. 

- Serviços com capacidade de ponto final de serviço devem ser configurados com pontos finais de serviço.
- Dependências de endereço IP são para o tráfego de não-HTTP/S (tráfego TCP e UDP)
- Pontos finais HTTP/HTTPS de FQDN podem ser colocados no seu dispositivo de firewall.
- Pontos finais HTTP/HTTPS de caráter universal são dependências que podem variar com o seu ASE com base num número de qualificadores. 
- Dependências de Linux são apenas uma preocupação, se estiver a implementar aplicações do Linux em ASE. Se não estiver a implementar aplicações do Linux em ASE, estes endereços não é necessário ser adicionado à firewall. 

#### <a name="service-endpoint-capable-dependencies"></a>Dependências com capacidade de ponto final de serviço 

| Ponto Final |
|----------|
| SQL do Azure |
| Storage do Azure |
| Hub de Eventos do Azure |

#### <a name="ip-address-dependencies"></a>Dependências de endereço IP

| Ponto Final | Detalhes |
|----------| ----- |
| \*:123 | Verificação de relógio NTP. O tráfego é verificado em vários pontos de extremidade na porta 123 |
| \*:12000 | Esta porta é utilizada para alguma monitorização do sistema. Se bloqueado, em seguida, alguns problemas será mais difícil de triagem mas seu ASE irá continuar a funcionar |
| 40.77.24.27:80 | Necessários para monitorizar e alertar relativamente a problemas de ASE |
| 40.77.24.27:443 | Necessários para monitorizar e alertar relativamente a problemas de ASE |
| 13.90.249.229:80 | Necessários para monitorizar e alertar relativamente a problemas de ASE |
| 13.90.249.229:443 | Necessários para monitorizar e alertar relativamente a problemas de ASE |
| 104.45.230.69:80 | Necessários para monitorizar e alertar relativamente a problemas de ASE |
| 104.45.230.69:443 | Necessários para monitorizar e alertar relativamente a problemas de ASE |
| 13.82.184.151:80 | Necessários para monitorizar e alertar relativamente a problemas de ASE |
| 13.82.184.151:443 | Necessários para monitorizar e alertar relativamente a problemas de ASE |

Com um Firewall do Azure, obtém automaticamente abaixo tudo configurado com as etiquetas do FQDN. 

#### <a name="fqdn-httphttps-dependencies"></a>Dependências de FQDN HTTP/HTTPS 

| Ponto Final |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Dependências HTTP/HTTPS de caráter universal 

| Ponto Final |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |

#### <a name="linux-dependencies"></a>Dependências do Linux 

| Ponto Final |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registo 1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
