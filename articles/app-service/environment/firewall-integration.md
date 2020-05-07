---
title: Bloquear o tráfego de saída
description: Aprenda a integrar-se com o Azure Firewall para garantir o tráfego de saída de dentro de um ambiente de Serviço de Aplicações.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 03/31/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4c25c64268b38e5929c73891f7c48e79b9b8593e
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856040"
---
# <a name="locking-down-an-app-service-environment"></a>Bloquear um ambiente de serviço de aplicações

O App Service Environment (ASE) tem uma série de dependências externas a que requer acesso para funcionar corretamente. A ASE vive na rede virtual Azure (VNet). Os clientes devem permitir o tráfego de dependência da ASE, o que é um problema para os clientes que querem bloquear todas as saídas do seu VNet.

Há uma série de pontos finais de entrada que são usados para gerir uma ASE. O tráfego de gestão de entrada não pode ser enviado através de um dispositivo de firewall. Os endereços de origem para este tráfego são conhecidos e são publicados no documento de endereços de gestão do [App Service Environment.](https://docs.microsoft.com/azure/app-service/environment/management-addresses) Existe também uma etiqueta de serviço chamada AppServiceManagement que pode ser usada com Grupos de Segurança de Rede (NSGs) para garantir o tráfego de entrada.

As dependências de saída da ASE são quase inteiramente definidas com FQDNs, que não têm endereços estáticos por trás deles. A falta de endereços estáticos significa que os grupos de segurança da rede não podem ser utilizados para bloquear o tráfego de saída de uma ASE. Os endereços mudam com frequência suficiente para que não se possa estabelecer regras com base na resolução atual e usá-las para criar NSGs. 

A solução para assegurar endereços de saída encontra-se na utilização de um dispositivo de firewall que pode controlar o tráfego de saída com base em nomes de domínio. O Azure Firewall pode restringir o tráfego HTTP e HTTPS de saída com base no FQDN do destino.  

## <a name="system-architecture"></a>Arquitetura do sistema

A implantação de uma ASE com tráfego de saída através de um dispositivo de firewall requer a alteração das rotas na sub-rede ASE. As rotas funcionam a um nível IP. Se não tiver cuidado na definição das suas rotas, pode forçar o tráfego de resposta da TCP a fonte de outra morada. Quando o seu endereço de resposta é diferente do endereço para o que o tráfego foi enviado, o problema é chamado de encaminhamento assimétrico e quebrará o TCP.

Tem de haver rotas definidas para que o tráfego de entrada para a ASE possa responder da mesma forma que o tráfego entrou. As rotas devem ser definidas para pedidos de gestão de entrada e para pedidos de pedidos de entrada.

O tráfego de e para uma ASE deve respeitar as seguintes convenções

* O tráfego para Azure SQL, Storage e Event Hub não são suportados com a utilização de um dispositivo de firewall. Este tráfego deve ser enviado diretamente para esses serviços. A forma de fazer isso acontecer é configurar os pontos finais do serviço para esses três serviços. 
* Devem ser definidas as regras da tabela de rotas que enviam o tráfego de gestão de regresso de onde veio.
* Devem ser definidas as regras da tabela de rotas que enviam o tráfego de aplicações de regresso de onde veio. 
* Todo o tráfego que sai da ASE pode ser enviado para o seu dispositivo de firewall com uma regra de mesa de rota.

![ASE com fluxo de ligação de firewall Azure][5]

## <a name="locking-down-inbound-management-traffic"></a>Bloquear o tráfego de gestão de entrada

Se a sua sub-rede ASE ainda não tiver um NSG atribuído, crie um. Dentro do NSG, estabeleça a primeira regra para permitir o tráfego da etiqueta de serviço chamada AppServiceManagement nas portas 454, 455. A regra para permitir o acesso a partir da etiqueta AppServiceManagement é a única coisa que é necessária aos IPs públicos para gerir a sua ASE. Os endereços que estão por trás dessa Etiqueta de Serviço são usados apenas para administrar o Serviço de Aplicações Azure. O tráfego de gestão que flui através destas ligações é encriptado e protegido com certificados de autenticação. O tráfego típico neste canal inclui coisas como comandos iniciados pelo cliente e sondas de saúde. 

AsEs que são feitas através do portal com uma nova subnet são feitas com um NSG que contém a regra de permitir a etiqueta AppServiceManagement.  

A Sua ASE também deve permitir pedidos de entrada a partir da etiqueta Balancer de carga na porta 16001. Os pedidos do Balancer de Carga na porta 16001 são manter os controlos vivos entre o Balancer de Carga e as extremidades dianteiras da ASE. Se a porta 16001 estiver bloqueada, a sua ASE não será saudável.

## <a name="configuring-azure-firewall-with-your-ase"></a>Configurar firewall azure com a sua ASE 

Os passos para travar a saída da sua ASE existente com firewall Azure são:

1. Ative pontos finais de serviço para SQL, Storage e Event Hub na sua subnet ASE. Para ativar os pontos finais do serviço, entre no portal de rede > subnets e selecione Microsoft.EventHub, Microsoft.SQL e Microsoft.Storage from the Service endpoints dropdown. Quando tiver pontos finais de serviço habilitados ao Azure SQL, quaisquer dependências Azure SQL que as suas aplicações tenham devem ser configuradas com pontos finais de serviço também. 

   ![selecionar pontos finais de serviço][2]
  
1. Crie uma subnet chamada AzureFirewallSubnet no VNet onde a sua ASE existe. Siga as instruções na [documentação da Firewall Azure](https://docs.microsoft.com/azure/firewall/) para criar o seu Firewall Azure.

1. A partir do Azure Firewall UI > Regras > coleção de regras de aplicação, selecione Adicionar a recolha de regras de aplicação. Forneça um nome, prioridade e definir permitir. Na secção de tags FQDN, forneça um nome, detete os endereços de origem para * e selecione a etiqueta FQDN do Serviço de Aplicações e a Atualização do Windows. 
   
   ![Adicionar regra de aplicação][1]
   
1. A partir do Azure Firewall UI > Rules > Network rule collection, selecione Adicionar coleção de regras de rede. Forneça um nome, prioridade e definir permitir. Na secção Regras sob endereços IP, forneça um nome, selecione um ptocol de **Qualquer,** conjunto * para endereços Fonte e Destino, e coloque as portas em 123. Esta regra permite que o sistema execute a sincronização do relógio utilizando o NTP. Crie outra regra da mesma forma para a porta 12000 para ajudar a triagem de quaisquer problemas do sistema. 

   ![Adicionar regra de rede NTP][3]
   
1. A partir do Azure Firewall UI > Rules > Network rule collection, selecione Adicionar coleção de regras de rede. Forneça um nome, prioridade e definir permitir. Na secção Regras sob etiquetas de serviço, forneça um nome, selecione um protocolo de **Qualquer,** set * para endereços Source, selecione uma etiqueta de serviço do AzureMonitor e coloque as portas em 80.443. Esta regra permite que o sistema forneça ao Monitor Azure informações sobre saúde e métricas.

   ![Adicionar regra de rede de etiquetas de serviço NTP][6]
   
1. Crie uma tabela de rotas com os endereços de gestão dos endereços de [gestão]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) do App Service Environment com um próximo salto de Internet. As entradas da tabela de rotas são necessárias para evitar problemas de encaminhamento assimétricos. Adicione rotas para as dependências de endereçoip abaixo anotadas nas dependências de endereçoIP com um próximo lúpulo da Internet. Adicione uma rota de Aparelho Virtual à sua tabela de rotas para 0.0.0.0/0 com o próximo salto sendo o seu endereço IP privado Azure Firewall. 

   ![Criação de uma tabela de rotas][4]
   
1. Atribua a tabela de rotas que criou à sua subnet ASE.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Implantando a sua ASE atrás de uma firewall

Os passos para implantar a sua ASE atrás de uma firewall são os mesmos que configurar a sua ASE existente com uma Firewall Azure, exceto que terá de criar a sua subnet ASE e, em seguida, seguir os passos anteriores. Para criar a sua ASE numa subnet pré-existente, precisa de utilizar um modelo de Gestor de Recursos, tal como descrito no documento sobre a Criação da [Sua ASE com um modelo](https://docs.microsoft.com/azure/app-service/environment/create-from-template)de Gestor de Recursos .

## <a name="application-traffic"></a>Tráfego de aplicação 

Os passos acima permitirão que a sua ASE funcione sem problemas. Ainda precisa configurar as coisas para acomodar as suas necessidades de aplicação. Existem dois problemas para aplicações numa ASE que está configurada com o Azure Firewall.  

- As dependências de aplicação devem ser adicionadas ao Firewall Azure ou à tabela de rotas. 
- Devem ser criadas rotas para o tráfego de aplicações para evitar problemas de encaminhamento assimétricos

Se as suas aplicações tiverem dependências, elas precisam de ser adicionadas à sua Firewall Azure. Criar regras de Aplicação para permitir regras de tráfego http/HTTPS e regras de rede para tudo o resto. 

Se souber do intervalo de endereços de onde virá o tráfego de pedido de pedido de pedido, pode adicioná-lo à tabela de rotas que é atribuída à sua subnet ASE. Se a gama de endereços for grande ou não especificada, então pode utilizar um aparelho de rede como o Gateway aplicação para lhe dar um endereço para adicionar à sua tabela de rotas. Para mais detalhes sobre a configuração de um Gateway de Aplicação com o seu ILB ASE, leia [Integrando o seu ILB ASE com um Gateway de Aplicação](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

Esta utilização do Gateway de Aplicação é apenas um exemplo de como configurar o seu sistema. Se seguisse este caminho, teria de adicionar uma rota à tabela de rotas da rede ASE para que o tráfego de resposta enviado para o Gateway de Aplicação fosse diretamente para lá. 

## <a name="logging"></a>Registo 

A Firewall Azure pode enviar registos para registos de Armazenamento Azure, Hub de Eventos ou Monitor Estoque. Para integrar a sua aplicação com qualquer destino suportado, vá ao portal Azure Firewall > Registos de Diagnóstico e ative os registos para o seu destino pretendido. Se integrar os registos do Monitor Azure, poderá ver o registo de registos de qualquer tráfego enviado para o Azure Firewall. Para ver o tráfego que está a ser negado, abra o seu portal de espaço de trabalho Log Analytics > Registos e introduza uma consulta como 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Integrar o seu Firewall Azure com registos Do Monitor Azure é útil quando se consegue obter uma aplicação a funcionar quando não se sabe de todas as dependências da aplicação. Pode saber mais sobre os registos do Monitor Azure a partir de dados de [registo do Analyze no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).
 
## <a name="dependencies"></a>Dependências

As seguintes informações só são necessárias se pretender configurar um aparelho de firewall que não seja o Firewall Azure. 

- Os serviços de endpoint de serviço devem ser configurados com pontos finais de serviço.
- As dependências do endereço IP destinam-se ao tráfego não HTTP/S (tanto o tráfego tCP como o uDP)
- Os pontos finais FQDN HTTP/HTTPS podem ser colocados no seu dispositivo de firewall.
- Os pontos finais Wildcard HTTP/HTTPS são dependências que podem variar com a sua ASE com base numa série de qualificações. 
- As dependências do Linux só são uma preocupação se estiver a implementar aplicações Linux na sua ASE. Se não estiver a implementar aplicações Linux na sua ASE, então estes endereços não precisam de ser adicionados à sua firewall. 

#### <a name="service-endpoint-capable-dependencies"></a>Dependências de Endpoint de serviço 

| Ponto Final |
|----------|
| SQL do Azure |
| Storage do Azure |
| Hub de Eventos do Azure |

#### <a name="ip-address-dependencies"></a>Dependências de endereçoip

| Ponto Final | Detalhes |
|----------| ----- |
| \*:123 | Verificação do relógio NTP. O tráfego é verificado em vários pontos finais no porto 123 |
| \*:12000 | Esta porta é utilizada para uma monitorização do sistema. Se bloqueado, então algumas questões serão mais difíceis de triagem, mas a sua ASE continuará a operar |
| 40.77.24.27:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 40.77.24.27:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.90.249.229:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.90.249.229:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 104.45.230.69:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 104.45.230.69:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.82.184.151:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.82.184.151:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |

Com uma Firewall Azure, obtém automaticamente tudo abaixo configurado com as tags FQDN. 

#### <a name="fqdn-httphttps-dependencies"></a>Dependências fQDN HTTP/HTTPS 

| Ponto Final |
|----------|
|graph.microsoft.com:443 |
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
|prod.microsoftmetrics.com:443 |
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
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|gcs.prod.monitoring.core.windows.net:80|
|gcs.prod.monitoring.core.windows.net:443|
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Dependências wildcard HTTP/HTTPS 

| Ponto Final |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
| \*.identity.azure.net:443 |
| \*.ctldl.windowsupdate.com:80 |
| \*.ctldl.windowsupdate.com:443 |

#### <a name="linux-dependencies"></a>Dependências linux 

| Ponto Final |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
| \*.cdn.mscr.io:443 |
|mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>Dependências de Gov dos EUA

Para as ASEs nas regiões norte-americanas Gov, siga as instruções na [Configuração da Firewall Azure com a sua](https://docs.microsoft.com/azure/app-service/environment/firewall-integration#configuring-azure-firewall-with-your-ase) secção ASE deste documento para configurar uma Firewall Azure com a sua ASE.

Se quiser utilizar um dispositivo diferente do Azure Firewall em US Gov 

* Os serviços de endpoint de serviço devem ser configurados com pontos finais de serviço.
* Os pontos finais FQDN HTTP/HTTPS podem ser colocados no seu dispositivo de firewall.
* Os pontos finais Wildcard HTTP/HTTPS são dependências que podem variar com a sua ASE com base numa série de qualificações.

O Linux não está disponível nas regiões norte-americanas gov e, portanto, não está listado como uma configuração opcional.

#### <a name="service-endpoint-capable-dependencies"></a>Dependências de Endpoint de serviço ####

| Ponto Final |
|----------|
| SQL do Azure |
| Storage do Azure |
| Hub de Eventos do Azure |

#### <a name="ip-address-dependencies"></a>Dependências de endereçoip

| Ponto Final | Detalhes |
|----------| ----- |
| \*:123 | Verificação do relógio NTP. O tráfego é verificado em vários pontos finais no porto 123 |
| \*:12000 | Esta porta é utilizada para uma monitorização do sistema. Se bloqueado, então algumas questões serão mais difíceis de triagem, mas a sua ASE continuará a operar |
| 40.77.24.27:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 40.77.24.27:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.90.249.229:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.90.249.229:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 104.45.230.69:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 104.45.230.69:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.82.184.151:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.82.184.151:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |

#### <a name="dependencies"></a>Dependências ####

| Ponto Final |
|----------|
| \*.ctldl.windowsupdate.com:80 |
| \*.management.usgovcloudapi.net:80 |
| \*.update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com
|ocsp.digicert.0 |
|ocsp.msocsp.co|
|ocsp.verisign.0 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*.management.usgovcloudapi.net:443 |
|\*.update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
