---
title: Conecte-se privadamente a uma App Web Azure usando o Ponto Final Privado
description: Conecte-se privadamente a uma Aplicação Web usando o Azure Private Endpoint
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 08/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 16ce03ad8d6af1908a9ea4d3b7759bb5eb3c1139
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961555"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Utilização de pontos finais privados para app Web Azure (pré-visualização)

> [!Note]
> Com a atualização de pré-visualização, divulgámos a funcionalidade de proteção de exfiltração de dados.
>
> A pré-visualização está disponível em todas as regiões públicas para As Aplicações Web PremiumV2 e Linux Web e Funções Premium Elásticas. 

Pode utilizar o Private Endpoint para a sua Azure Web App para permitir que os clientes localizados na sua rede privada acedam de forma segura à aplicação sobre o Private Link. O Private Endpoint utiliza um endereço IP a partir do seu espaço de endereço Azure VNet. O tráfego de rede entre um cliente na sua rede privada e a Web App atravessa o VNet e uma Ligação Privada na rede de espinha dorsal da Microsoft, eliminando a exposição da Internet pública.

A utilização de Ponto Final Privado para a sua Aplicação Web permite-lhe:

- Proteja a sua Web App configurando o Ponto Final Privado, eliminando a exposição pública.
- Ligue-se seguramente à Web App a partir de redes no local que se conectam ao VNet usando um VPN ou ExpressRoute private peering.
- Evite qualquer exfiltração de dados do seu VNet. 

Se necessitar apenas de uma ligação segura entre o seu VNet e a sua Web App, um Ponto Final de Serviço é a solução mais simples. Se também precisar de chegar à aplicação web a partir do local através de um Azure Gateway, um VNet com olhos regionais, ou um VNet globalmente espreitado, o Private Endpoint é a solução.  

Para mais informações, consulte [os Pontos finais de serviço.][serviceendpoint]

## <a name="conceptual-overview"></a>Descrição geral conceptual

Um Ponto Final Privado é uma interface de rede especial (NIC) para a sua App Web Azure numa sub-rede na sua Rede Virtual (VNet).
Quando cria um Ponto Final Privado para a sua Web App, proporciona uma conectividade segura entre os clientes da sua rede privada e a sua Web App. O Ponto Final Privado é atribuído a um endereço IP a partir do intervalo de endereços IP do seu VNet.
A ligação entre o Ponto Final Privado e a Web App utiliza uma [ligação privada][privatelink]segura. O Ponto Final Privado é utilizado apenas para fluxos de entrada na sua Web App. Os fluxos de saída não utilizarão este Ponto Final Privado, mas pode injetar fluxos de saída para a sua rede numa sub-rede diferente através da [funcionalidade de integração VNet][vnetintegrationfeature].

A sub-rede onde se liga o Ponto Final Privado pode ter outros recursos, não precisa de uma Sub-rede vazia dedicada.
Também pode implementar o Private Endpoint numa região diferente da Web App. 

> [!Note]
>A funcionalidade de integração VNet não pode utilizar a mesma sub-rede que o Private Endpoint, esta é uma limitação da funcionalidade de integração VNet.

Do ponto de vista da segurança:

- Quando ativa os Pontos Finais Privados da sua Web App, desativa todo o acesso público.
- Pode ativar vários Pontos Finais Privados em outros VNets e Subnets, incluindo VNets noutras regiões.
- O endereço IP do Private Endpoint NIC deve ser dinâmico, mas permanecerá o mesmo até eliminar o Ponto Final Privado.
- O NIC do Private Endpoint não pode ter um NSG associado.
- A sub-rede que acolhe o Private Endpoint pode ter um NSG associado, mas tem de desativar as políticas de rede para o Ponto Final Privado: ver [políticas de rede de desativação para pontos finais privados.][disablesecuritype] Como resultado, não é possível filtrar por nenhum NSG o acesso ao seu Ponto Final Privado.
- Quando ativa o Private Endpoint para a sua Web App, a configuração das [restrições][accessrestrictions] de acesso da Web App não é avaliada.
- Pode eliminar o risco de exfiltração de dados do VNet removendo todas as regras NSG onde o destino é a marca Internet ou os serviços Azure. Quando implementa um Ponto Final Privado para uma Aplicação Web, só pode chegar a esta Aplicação Web específica através do Ponto Final Privado. Se tiver outra Web App, tem de implementar outro Ponto Final Privado dedicado para esta outra Web App.

Nos registos HTTP web da sua Web App, encontrará o IP de origem do cliente. Esta funcionalidade é implementada utilizando o protocolo TCP Proxy, reencaminhando a propriedade IP do cliente até à Web App. Para obter mais informações, consulte [obter informações de ligação utilizando o TCP Proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Visão geral global do ponto final privado da Web App](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Quando utilizar o Private Endpoint para a Web App, o URL solicitado deve corresponder ao nome da sua Aplicação Web. Por defeito mywebappname.azurewebsites.net.

Por padrão, sem Private Endpoint, o nome público da sua aplicação web é um nome canónico para o cluster.
Por exemplo, a resolução de nomes será:

|Nome |Tipo |Valor |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


Quando implementa um Ponto Final Privado, atualizamos a entrada de DNS para apontar para o nome canónico mywebapp.privatelink.azurewebsites.net.
Por exemplo, a resolução de nomes será:

|Nome |Tipo |Valor |Observação |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|<-- Este IP público não é o seu Ponto Final Privado, receberá um erro de 403|

Tem de configurar um servidor DNS privado ou uma zona privada Azure DNS, para testes que pode modificar a entrada do anfitrião da sua máquina de teste.
A zona DE DNS que precisa de criar é: **privatelink.azurewebsites.net**. Registe o registo da sua Web App com um registo A e o Private Endpoint IP.
Por exemplo, a resolução de nomes será:

|Nome |Tipo |Valor |Observação |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|<-- Gere esta entrada no seu sistema DNS para apontar para o seu endereço IP private endpoint|

Após esta configuração DNS, pode chegar à sua Web App em privado com o nome padrão mywebappname.azurewebsites.net.


Se precisar de usar um nome DNS personalizado, tem de adicionar o nome personalizado na sua Web App. Durante a pré-visualização, o nome personalizado deve ser validado como qualquer nome personalizado, utilizando a resolução pública de DNS. Para mais informações, consulte [a validação personalizada do DNS.][dnsvalidation]

Para a consola Kudu, ou Kudu REST API (implementação com agentes auto-hospedados da Azure DevOps, por exemplo), deve criar dois registos na sua zona privada Azure DNS ou no seu servidor DNS personalizado. 

| Nome | Tipo | Valor |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | A | PrivateEndpointIP | 



## <a name="pricing"></a>Preços

Para obter detalhes sobre os preços, consulte [os preços do Azure Private Link][pricing].

## <a name="limitations"></a>Limitações

Quando utilizar a Função Azure em Plano Premium Elástico com Ponto Final Privado, para executar ou executar a função no portal Azure Web, tem de ter acesso direto à rede ou receberá um erro HTTP 403. Por outras palavras, o seu navegador deve ser capaz de chegar ao Ponto Final Privado para executar a função a partir do portal Azure Web. 

Durante a pré-visualização, apenas a ranhura de produção é exposta por trás do Private Endpoint, outras ranhuras devem ser alcançadas pelo Public Endpoint.

Estamos a melhorar regularmente o recurso Private Link e o Private Endpoint, consulte [este artigo][pllimitations] para obter informações atualizadas sobre limitações.

## <a name="next-steps"></a>Passos seguintes

- Para implementar o Private Endpoint para a sua Web App através do portal, consulte [como ligar-se privadamente a uma Aplicação Web com o Portal][howtoguide1]
- Para implementar o Private Endpoint para a sua Web App utilizando o Azure CLI, consulte [como ligar-se privadamente a uma Aplicação Web com o Azure CLI][howtoguide2]
- Para implementar o Ponto Final Privado para a sua Aplicação Web utilizando o PowerShell, consulte [como ligar-se privadamente a uma Aplicação Web com o PowerShell][howtoguide3]
- Para implementar o Ponto Final Privado para a sua Aplicação Web utilizando o modelo Azure, consulte [como ligar-se privadamente a uma Aplicação Web com o modelo Azure][howtoguide4]
- Exemplo de ponta a ponta, como ligar uma aplicação web frontend a uma aplicação web de backend segura com a injeção de VNet e ponto final privado com o modelo ARM, consulte este [arranque rápido][howtoguide5]
- Exemplo de ponta a ponta, como ligar uma aplicação web frontend a uma aplicação web de backend segura com injeção VNet e ponto final privado com terraforme, consulte esta [amostra][howtoguide6]


<!--Links-->
[serviceendpoint]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[privatelink]: ../../private-link/private-link-overview.md
[vnetintegrationfeature]: ../web-sites-integrate-with-vnet.md
[disablesecuritype]: ../../private-link/disable-private-endpoint-network-policy.md
[accessrestrictions]: ../app-service-ip-restrictions.md
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: ../app-service-web-tutorial-custom-domain.md
[pllimitations]: ../../private-link/private-endpoint-overview.md#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: ../../private-link/create-private-endpoint-webapp-portal.md
[howtoguide2]: ../scripts/cli-deploy-privateendpoint.md
[howtoguide3]: ../scripts/powershell-deploy-private-endpoint.md
[howtoguide4]: ../scripts/template-deploy-private-endpoint.md
[howtoguide5]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection
[howtoguide6]: ../scripts/terraform-secure-backend-frontend.md