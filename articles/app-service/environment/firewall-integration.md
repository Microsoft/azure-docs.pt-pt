---
title: Bloqueie o tráfego de saída
description: Saiba como se integrar com o Azure Firewall para garantir o tráfego de saída dentro de um ambiente de Serviço de Aplicações.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 09/24/2020
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: 782074fc491c0b5e03ced36563bafa8679e78330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91264665"
---
# <a name="locking-down-an-app-service-environment"></a>Bloqueando um Ambiente de Serviço de Aplicações

O Ambiente de Serviço de Aplicações (ASE) tem uma série de dependências externas a que necessita de acesso para funcionar corretamente. O ASE vive no cliente Azure Virtual Network (VNet). Os clientes devem permitir o tráfego de dependência da ASE, o que é um problema para os clientes que querem bloquear todas as saídas do seu VNet.

Há uma série de pontos finais de entrada que são usados para gerir um ASE. O tráfego de gestão de entrada não pode ser enviado através de um dispositivo de firewall. Os endereços de origem para este tráfego são conhecidos e são publicados no documento de gestão de endereços de [gestão do App Service Environment.](./management-addresses.md) Existe também uma Tag de Serviço chamada AppServiceManagement que pode ser usada com Grupos de Segurança de Rede (NSGs) para garantir o tráfego de entrada.

As dependências de saída da ASE são quase totalmente definidas com FQDNs, que não têm endereços estáticos por trás deles. A falta de endereços estáticos significa que os grupos de segurança da rede não podem ser utilizados para bloquear o tráfego de saída de um ASE. Os endereços mudam muitas vezes o suficiente para que não se possa estabelecer regras com base na resolução atual e usá-lo para criar NSGs. 

A solução para garantir endereços de saída reside na utilização de um dispositivo de firewall que pode controlar o tráfego de saída com base em nomes de domínio. O Azure Firewall pode restringir o tráfego http e HTTPS de saída com base no FQDN do destino.  

## <a name="system-architecture"></a>Arquitetura do sistema

A implantação de um ASE com tráfego de saída através de um dispositivo de firewall requer a alteração de rotas na sub-rede ASE. As rotas operam a um nível de IP. Se não tiver cuidado na definição das suas rotas, pode forçar o tráfego de resposta da TCP a obter a partir de outro endereço. Quando o seu endereço de resposta é diferente do endereço para o qual o tráfego foi enviado, o problema é chamado de encaminhamento assimétrico e quebrará o TCP.

Tem de haver vias definidas para que o tráfego de entrada para a ASE possa responder da mesma forma que o trânsito entrou. As rotas devem ser definidas para pedidos de gestão de entrada e para pedidos de pedidos de entrada.

O tráfego de e para uma ASE deve respeitar as seguintes convenções

* O tráfego para Azure SQL, Storage e Event Hub não são suportados com a utilização de um dispositivo de firewall. Este tráfego deve ser enviado diretamente para esses serviços. A forma de fazer isso acontecer é configurar os pontos finais de serviço para estes três serviços. 
* As regras do quadro de rotas devem ser definidas que rememitim o tráfego de gestão de entrada de onde veio.
* As regras do quadro de rotas devem ser definidas que enviem o tráfego de aplicação de entrada de volta de onde veio. 
* Todo o tráfego que sai do ASE pode ser enviado para o seu dispositivo de firewall com uma regra de tabela de rota.

![ASE com fluxo de conexão Azure Firewall][5]

## <a name="locking-down-inbound-management-traffic"></a>Bloqueio do tráfego de gestão de entrada

Se a sua sub-rede ASE ainda não tiver um NSG atribuído, crie um. Dentro do NSG, detenda a primeira regra para permitir o tráfego a partir da Tag de Serviço chamada AppServiceManagement nas portas 454,455. A regra para permitir o acesso a partir da etiqueta AppServiceManagement é a única coisa que é necessária aos IPs públicos para gerir o seu ASE. Os endereços que estão por trás dessa Tag de Serviço são usados apenas para administrar o Serviço de Aplicações Azure. O tráfego de gestão que flui através destas ligações é encriptado e protegido com certificados de autenticação. O tráfego típico neste canal inclui coisas como comandos iniciados pelo cliente e sondas de saúde. 

AsEs que são feitas através do portal com uma nova sub-rede são feitas com um NSG que contém a regra de permitir a etiqueta AppServiceManagement.  

O seu ASE também deve permitir pedidos de entrada a partir da etiqueta balanceador de carga na porta 16001. Os pedidos do Balanceador de Carga na porta 16001 são controlos vivos entre o Balançador de Carga e as extremidades dianteiras ASE. Se a porta 16001 estiver bloqueada, o seu ASE não ficará saudável.

## <a name="configuring-azure-firewall-with-your-ase"></a>Configurar firewall Azure com o seu ASE 

Os passos para bloquear a saída do seu ASE existente com a Azure Firewall são:

1. Ative os pontos finais do serviço para SQL, Storage e Event Hub na sua sub-rede ASE. Para ativar os pontos finais do serviço, entre no portal de rede > sub-redes e selecione Microsoft.EventHub, Microsoft.SQL e Microsoft.Storage a partir do dropdown dos pontos finais do Serviço. Quando tiver pontos finais de serviço ativados para o Azure SQL, quaisquer dependências Azure SQL que as suas aplicações tenham devem ser configuradas também com pontos finais de serviço. 

   ![selecione pontos finais de serviço][2]
  
1. Crie uma sub-rede chamada AzureFirewallSubnet no VNet onde o seu ASE existe. Siga as instruções da [documentação Azure Firewall](../../firewall/index.yml) para criar o seu Azure Firewall.

1. A partir da coleção de regras de > regras de aplicação Azure Firewall UI >, selecione a recolha de regras de aplicação Add. Forneça um nome, prioridade e definir Permitir. Na secção de tags FQDN, forneça um nome, desapedaça os endereços de origem para * e selecione a FQDN Tag FQDN do Ambiente de Aplicações e a Atualização do Windows. 
   
   ![Adicionar regra de aplicação][1]
   
1. A partir da coleção de regras de > regras > de rede de > de firewall Azure Firewall, selecione Add network rule collection. Forneça um nome, prioridade e definir Permitir. Na secção Regras nos endereços IP, forneça um nome, selecione um protocolo de **Qualquer**, definido * para endereços de Origem e Destino, e definir as portas para 123. Esta regra permite que o sistema execute sincronização do relógio utilizando NTP. Crie outra regra da mesma forma para o porto 12000 para ajudar a triagem de quaisquer problemas do sistema. 

   ![Adicionar regra de rede NTP][3]
   
1. A partir da coleção de regras de > regras > de rede de > de firewall Azure Firewall, selecione Add network rule collection. Forneça um nome, prioridade e definir Permitir. Na secção Regras ao abrigo das Etiquetas de Serviço, forneça um nome, selecione um protocolo de **Qualquer**, definido * para endereços Source, selecione uma etiqueta de serviço do AzureMonitor e desaver as portas para 80.443. Esta regra permite ao sistema fornecer ao Monitor Azure informações sobre saúde e métricas.

   ![Adicione regra de rede de tag de serviço NTP][6]
   
1. Crie uma tabela de rotas com os endereços de gestão de [app Service Environment]( ./management-addresses.md) com um próximo salto de Internet. As entradas da tabela de rotas são necessárias para evitar problemas de encaminhamento assimétricos. Adicione rotas para as dependências de endereços IP anotados abaixo nas dependências de endereços IP com um próximo salto de Internet. Adicione uma rota de Aparelho Virtual à sua mesa de rota para 0.0.0.0/0 com o próximo lúpulo sendo o seu endereço IP privado Azure Firewall. 

   ![Criação de uma tabela de rotas][4]
   
1. Atribua a tabela de rotas que criou para a sua sub-rede ASE.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Implantar o seu ASE atrás de uma firewall

Os passos para implantar o seu ASE atrás de uma firewall são os mesmos que configurar o seu ASE existente com uma Firewall Azure, exceto que terá de criar a sua sub-rede ASE e, em seguida, seguir os passos anteriores. Para criar o seu ASE numa sub-rede pré-existente, tem de utilizar um modelo de Gestor de Recursos, conforme descrito no documento sobre a criação do [seu ASE com um modelo de Gestor de Recursos.](./create-from-template.md)

## <a name="application-traffic"></a>Tráfego de aplicações 

Os passos acima permitirão que o seu ASE funcione sem problemas. Você ainda precisa configurar coisas para acomodar as suas necessidades de aplicação. Existem dois problemas para aplicações num ASE que está configurado com Azure Firewall.  

- As dependências de aplicações devem ser adicionadas à Firewall Azure ou à tabela de rotas. 
- Devem ser criadas rotas para o tráfego de aplicações para evitar problemas de encaminhamento assimétrico

Se as suas aplicações tiverem dependências, elas têm de ser adicionadas ao seu Azure Firewall. Crie regras de aplicação para permitir o tráfego HTTP/HTTPS e as regras da Rede para tudo o resto. 

Se souber do intervalo de endereço de onde virá o tráfego de pedidos de aplicação, pode adicioná-lo à tabela de rotas que está atribuída à sua sub-rede ASE. Se a gama de endereços for grande ou não especificada, então pode utilizar um aparelho de rede como o Gateway de Aplicação para lhe dar um endereço para adicionar à sua tabela de rotas. Para mais detalhes sobre a configuração de um Gateway de aplicações com o seu ILB ASE, leia [integrando o seu ILB ASE com um Gateway de aplicações](./integrate-with-application-gateway.md)

Esta utilização do Gateway de Aplicações é apenas um exemplo de como configurar o seu sistema. Se seguisse este caminho, teria de adicionar uma rota para a tabela de rotas da sub-rede ASE para que o tráfego de resposta enviado para o Gateway de Aplicação fosse diretamente para lá. 

## <a name="logging"></a>Registo 

O Azure Firewall pode enviar registos para registos Azure Storage, Event Hub ou Azure Monitor. Para integrar a sua aplicação com qualquer destino suportado, vá ao portal Azure Firewall > Registos de Diagnóstico e ative os registos para o destino pretendido. Se integrar-se com os registos do Azure Monitor, poderá ver a registar-se para qualquer tráfego enviado para a Firewall do Azure. Para ver o tráfego que está a ser negado, abra o seu portal de espaço de trabalho Log Analytics > Logs e insira uma consulta como 

```kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

A integração do seu Azure Firewall com registos Azure Monitor é útil quando se inicia uma aplicação a funcionar quando não tem conhecimento de todas as dependências da aplicação. Pode saber mais sobre os registos do Azure Monitor a partir de dados de [registo de análise no Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).
 
## <a name="dependencies"></a>Dependências

As seguintes informações só são necessárias se desejar configurar um aparelho de firewall que não seja o Azure Firewall. 

- Os serviços de endpoint de serviço devem ser configurados com pontos finais de serviço.
- As dependências do endereço IP destinam-se ao tráfego não-HTTP/S (tráfego TCP e UDP)
- Os pontos finais FQDN HTTP/HTTPS podem ser colocados no seu dispositivo de firewall.
- Os pontos finais wildcard HTTP/HTTPS são dependências que podem variar com o seu ASE com base em uma série de qualificações. 
- As dependências do Linux só são uma preocupação se estiveres a implementar aplicações Linux no teu ASE. Se não estiver a implementar aplicações Linux no seu ASE, então estes endereços não precisam de ser adicionados à sua firewall. 

#### <a name="service-endpoint-capable-dependencies"></a>Dependências capazes de Endpoint de serviço 

| Ponto final |
|----------|
| SQL do Azure |
| Storage do Azure |
| Hub de Eventos do Azure |

#### <a name="ip-address-dependencies"></a>Dependências de endereços IP

| Ponto final | Detalhes |
|----------| ----- |
| \*:123 | Verificação do relógio NTP. O trânsito é verificado em vários pontos finais na porta 123 |
| \*:12000 | Esta porta é utilizada para alguma monitorização do sistema. Se estiver bloqueado, então alguns problemas serão mais difíceis de triagem, mas o seu ASE continuará a operar |
| 40.77.24.27:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 40.77.24.27:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.90.249.229:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.90.249.229:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 104.45.230.69:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 104.45.230.69:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.82.184.151:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.82.184.151:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |

Com uma Firewall Azure, obtém automaticamente tudo abaixo configurado com as tags FQDN. 

#### <a name="fqdn-httphttps-dependencies"></a>DEPENDÊNCIAS FQDN HTTP/HTTPS 

| Ponto final |
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
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|ocsp.digicert.com:443 |
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

#### <a name="wildcard-httphttps-dependencies"></a>Dependências de WILDCARD HTTP/HTTPS 

| Ponto final |
|----------|
|gr-Prod- \* .cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
| \*.identity.azure.net:443 |
| \*.ctldl.windowsupdate.com:80 |
| \*.ctldl.windowsupdate.com:443 |

#### <a name="linux-dependencies"></a>Dependências de Linux 

| Ponto final |
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
|oryx-cdn.microsoft.io:443 |
| \*.cdn.mscr.io:443 |
|mcr.microsoft.com:443 |
|\*.data.mcr.microsoft.com:443 |
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

## <a name="us-gov-dependencies"></a>Dependências gov dos EUA

Para as ASEs nas regiões gov dos EUA, siga as instruções na [Firewall Azure Configurante com a sua](#configuring-azure-firewall-with-your-ase) secção ASE deste documento para configurar uma Firewall Azure com o seu ASE.

Se quiser usar um dispositivo diferente do Azure Firewall em US Gov 

* Os serviços de endpoint de serviço devem ser configurados com pontos finais de serviço.
* Os pontos finais FQDN HTTP/HTTPS podem ser colocados no seu dispositivo de firewall.
* Os pontos finais wildcard HTTP/HTTPS são dependências que podem variar com o seu ASE com base em uma série de qualificações.

O Linux não está disponível nas regiões gov dos EUA e, portanto, não está listado como uma configuração opcional.

#### <a name="service-endpoint-capable-dependencies"></a>Dependências capazes de Endpoint de serviço ####

| Ponto final |
|----------|
| SQL do Azure |
| Storage do Azure |
| Hub de Eventos do Azure |

#### <a name="ip-address-dependencies"></a>Dependências de endereços IP

| Ponto final | Detalhes |
|----------| ----- |
| \*:123 | Verificação do relógio NTP. O trânsito é verificado em vários pontos finais na porta 123 |
| \*:12000 | Esta porta é utilizada para alguma monitorização do sistema. Se estiver bloqueado, então alguns problemas serão mais difíceis de triagem, mas o seu ASE continuará a operar |
| 40.77.24.27:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 40.77.24.27:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.90.249.229:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.90.249.229:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 104.45.230.69:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 104.45.230.69:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.82.184.151:80 | Necessário para monitorizar e alertar sobre os problemas da ASE |
| 13.82.184.151:443 | Necessário para monitorizar e alertar sobre os problemas da ASE |

#### <a name="dependencies"></a>Dependências ####

| Ponto final |
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
|mscrl.microsoft.com:80
|ocsp.digicert.com:80 |
|ocsp.verisign.com:80 |
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
|gcs.monitoring.core.usgovcloudapi.net:443 |
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
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
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
