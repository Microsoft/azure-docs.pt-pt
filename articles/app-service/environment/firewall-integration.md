---
title: Bloqueando Ambiente do Serviço de Aplicativo tráfego de saída-Azure
description: Descreve como integrar com o Firewall do Azure para proteger o tráfego de saída
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
ms.date: 07/29/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3e24b95704249a8a0b7588112cbaa678ae1e2c86
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619133"
---
# <a name="locking-down-an-app-service-environment"></a>Bloqueando um Ambiente do Serviço de Aplicativo

O Ambiente do Serviço de Aplicativo (ASE) tem um número de dependências externas às quais ele requer acesso para funcionar corretamente. O ASE reside na VNet (rede virtual) do cliente do Azure. Os clientes devem permitir o tráfego de dependência do ASE, que é um problema para os clientes que desejam bloquear toda a saída de sua VNet.

Há várias dependências de entrada que um ASE tem. O tráfego de gerenciamento de entrada não pode ser enviado por meio de um dispositivo de firewall. Os endereços de origem para esse tráfego são conhecidos e são publicados no documento de [endereços de gerenciamento de ambiente do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/environment/management-addresses) . Você pode criar regras de grupo de segurança de rede com essas informações para proteger o tráfego de entrada.

As dependências de saída do ASE são quase totalmente definidas com FQDNs, que não têm endereços estáticos por trás delas. A falta de endereços estáticos significa que os NSGs (grupos de segurança de rede) não podem ser usados para bloquear o tráfego de saída de um ASE. Os endereços são alterados com frequência suficiente, pois não é possível configurar regras com base na resolução atual e usá-la para criar NSGs. 

A solução para proteger endereços de saída está em uso de um dispositivo de firewall que pode controlar o tráfego de saída com base em nomes de domínio. O Firewall do Azure pode restringir o tráfego HTTP e HTTPS de saída com base no FQDN do destino.  

## <a name="system-architecture"></a>Arquitetura do sistema

Implantar um ASE com tráfego de saída passando por um dispositivo de firewall requer a alteração de rotas na sub-rede do ASE. As rotas operam em um nível de IP. Se você não tiver cuidado ao definir suas rotas, poderá forçar o tráfego de resposta TCP para a origem de outro endereço. Isso é chamado de roteamento assimétrico e interromperá o TCP.

Deve haver rotas definidas para que o tráfego de entrada para o ASE possa responder da mesma maneira que o tráfego chegou. Isso é verdadeiro para solicitações de gerenciamento de entrada e é verdadeiro para solicitações de aplicativos de entrada.

O tráfego de e para um ASE deve obedecer às convenções a seguir

* O tráfego para o SQL do Azure, o armazenamento e o Hub de eventos não têm suporte com o uso de um dispositivo de firewall. Esse tráfego deve ser enviado diretamente para esses serviços. A maneira de fazer isso acontecer é configurar pontos de extremidade de serviço para esses três serviços. 
* As regras da tabela de rotas devem ser definidas para enviar o tráfego de gerenciamento de entrada de onde ele foi fornecido.
* As regras da tabela de rotas devem ser definidas para enviar o tráfego do aplicativo de entrada de onde ele foi fornecido. 
* Todo o outro tráfego que sai do ASE pode ser enviado ao seu dispositivo de firewall com uma regra de tabela de rotas.

![ASE com o fluxo de conexão do firewall do Azure][5]

## <a name="configuring-azure-firewall-with-your-ase"></a>Configurando o Firewall do Azure com seu ASE 

As etapas para bloquear a saída do ASE existente com o Firewall do Azure são:

1. Habilite pontos de extremidade de serviço para SQL, armazenamento e Hub de eventos em sua sub-rede do ASE. Para fazer isso, acesse o portal de rede > sub-redes e selecione Microsoft. EventHub, Microsoft. SQL e Microsoft. Storage na lista suspensa pontos de extremidade de serviço. Quando você tem pontos de extremidade de serviço habilitados para o SQL do Azure, todas as dependências do SQL do Azure que seus aplicativos também devem ser configuradas com pontos de extremidade de serviço. 

   ![selecionar pontos de extremidade de serviço][2]
  
1. Crie uma sub-rede chamada AzureFirewallSubnet na VNet onde o seu ASE existe. Siga as instruções na [documentação do firewall do Azure](https://docs.microsoft.com/azure/firewall/) para criar o Firewall do Azure.
1. Na interface do usuário do firewall do Azure > regras > coleção de regras de aplicativo, selecione Adicionar coleção de regras de aplicativo. Forneça um nome, uma prioridade e um conjunto de permissões. Na seção marcas de FQDN, forneça um nome, defina os endereços de origem como * e selecione a marca Ambiente do Serviço de Aplicativo FQDN e a Windows Update. 
   
   ![Adicionar regra de aplicativo][1]
   
1. Na interface do usuário do firewall do Azure > regras > coleção de regras de rede, selecione Adicionar coleção de regras de rede. Forneça um nome, uma prioridade e um conjunto de permissões. Na seção regras, forneça um nome, selecione **qualquer**, defina * para endereços de origem e de destino e defina as portas como 123. Essa regra permite que o sistema execute a sincronização de relógio usando o NTP. Crie outra regra da mesma maneira que a porta 12000 para ajudar a fazer a triagem de qualquer problema do sistema.

   ![Adicionar regra de rede NTP][3]

1. Crie uma tabela de rotas com os endereços de gerenciamento de [ambiente do serviço de aplicativo endereços de gerenciamento]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) com um próximo salto da Internet. As entradas da tabela de rotas são necessárias para evitar problemas de roteamento assimétrico. Adicione rotas para as dependências de endereço IP anotadas abaixo nas dependências de endereço IP com um próximo salto da Internet. Adicione uma rota de dispositivo virtual à sua tabela de rotas para 0.0.0.0/0 com o próximo salto sendo o seu endereço IP privado do firewall do Azure. 

   ![Criando uma tabela de rotas][4]
   
1. Atribua a tabela de rotas que você criou à sua sub-rede do ASE.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Implantando seu ASE por trás de um firewall

As etapas para implantar seu ASE por trás de um firewall são as mesmas que a configuração do ASE existente com um firewall do Azure, exceto que você precisará criar sua sub-rede do ASE e, em seguida, seguir as etapas anteriores. Para criar seu ASE em uma sub-rede pré-existente, você precisa usar um modelo do Resource Manager, conforme descrito no documento sobre como [criar seu ase com um modelo do Resource Manager](https://docs.microsoft.com/azure/app-service/environment/create-from-template).

## <a name="application-traffic"></a>Tráfego do aplicativo 

As etapas acima permitirão que seu ASE opere sem problemas. Você ainda precisa configurar coisas para acomodar suas necessidades de aplicativo. Há dois problemas para aplicativos em um ASE configurado com o Firewall do Azure.  

- As dependências de aplicativo devem ser adicionadas ao firewall do Azure ou à tabela de rotas. 
- As rotas devem ser criadas para o tráfego do aplicativo para evitar problemas de roteamento assimétrico

Se seus aplicativos tiverem dependências, eles precisarão ser adicionados ao seu firewall do Azure. Crie regras de aplicativo para permitir o tráfego HTTP/HTTPS e as regras de rede para todo o resto. 

Se você souber o intervalo de endereços do qual o tráfego de solicitação do aplicativo virá, você poderá adicioná-lo à tabela de rotas que é atribuída à sua sub-rede do ASE. Se o intervalo de endereços for grande ou não especificado, você poderá usar um dispositivo de rede como o gateway de aplicativo para fornecer um endereço a ser adicionado à sua tabela de rotas. Para obter detalhes sobre como configurar um gateway de aplicativo com o ASE ILB, leia [integrando seu ILB ase com um gateway de aplicativo](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

Esse uso do gateway de aplicativo é apenas um exemplo de como configurar seu sistema. Se você seguir esse caminho, precisaria adicionar uma rota à tabela de rotas de sub-rede do ASE para que o tráfego de resposta enviado para o gateway de aplicativo fique lá diretamente. 

## <a name="logging"></a>Registo 

O Firewall do Azure pode enviar logs para o armazenamento do Azure, Hub de eventos ou logs de Azure Monitor. Para integrar seu aplicativo a qualquer destino com suporte, vá para o portal do firewall do Azure > logs de diagnóstico e habilite os logs para o destino desejado. Se você se integrar com os logs do Azure Monitor, poderá ver o registro em log para qualquer tráfego enviado ao firewall do Azure. Para ver o tráfego que está sendo negado, abra seu Log Analytics portal de espaço de trabalho > logs e insira uma consulta como 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
A integração do firewall do Azure com logs de Azure Monitor é muito útil quando se obtém um aplicativo funcionando quando você não está ciente de todas as dependências do aplicativo. Você pode saber mais sobre os logs de Azure Monitor de [analisar dados de log em Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
 
## <a name="dependencies"></a>Dependências

As informações a seguir só serão necessárias se você quiser configurar um dispositivo de firewall diferente do firewall do Azure. 

- Serviços com capacidade de ponto de extremidade de serviço devem ser configurados com pontos de extremidade de serviço.
- As dependências de endereço IP são para tráfego não HTTP/S (tráfego TCP e UDP)
- Os pontos de extremidade HTTP/HTTPS do FQDN podem ser colocados no seu dispositivo de firewall.
- Os pontos de extremidade HTTP/HTTPS curinga são dependências que podem variar com o ASE com base em vários qualificadores. 
- As dependências do Linux são apenas uma preocupação se você estiver implantando aplicativos do Linux em seu ASE. Se você não estiver implantando aplicativos do Linux em seu ASE, esses endereços não precisarão ser adicionados ao firewall. 

#### <a name="service-endpoint-capable-dependencies"></a>Dependências compatíveis com ponto de extremidade de serviço 

| Ponto Final |
|----------|
| Azure SQL |
| Storage do Azure |
| Hub de Eventos do Azure |

#### <a name="ip-address-dependencies"></a>Dependências de endereço IP

| Ponto Final | Detalhes |
|----------| ----- |
| \*:123 | Verificação do relógio de NTP. O tráfego é verificado em vários pontos de extremidade na porta 123 |
| \*:12000 | Essa porta é usada para alguns monitoramentos do sistema. Se for bloqueado, alguns problemas serão mais difíceis de fazer a triagem, mas o ASE continuará a operar |
| 40.77.24.27:80 | Necessário para monitorar e alertar sobre problemas do ASE |
| 40.77.24.27:443 | Necessário para monitorar e alertar sobre problemas do ASE |
| 13.90.249.229:80 | Necessário para monitorar e alertar sobre problemas do ASE |
| 13.90.249.229:443 | Necessário para monitorar e alertar sobre problemas do ASE |
| 104.45.230.69:80 | Necessário para monitorar e alertar sobre problemas do ASE |
| 104.45.230.69:443 | Necessário para monitorar e alertar sobre problemas do ASE |
| 13.82.184.151:80 | Necessário para monitorar e alertar sobre problemas do ASE |
| 13.82.184.151:443 | Necessário para monitorar e alertar sobre problemas do ASE |

Com um firewall do Azure, você obtém automaticamente tudo o que está abaixo configurado com as marcas de FQDN. 

#### <a name="fqdn-httphttps-dependencies"></a>Dependências de HTTP/HTTPS FQDN 

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
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Dependências HTTP/HTTPS de curinga 

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
|registry-1.docker.io:443 |
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
