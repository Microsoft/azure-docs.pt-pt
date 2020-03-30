---
title: Redirecionamento de resolução de problemas para URL do Serviço de Aplicações
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como resolver o problema da reorientação quando o Portal de Aplicação Azure é usado com o Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 961ed17bcef19b445c2546a557725bb6bd8653cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293530"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Problemas problemas no Serviço de Aplicações de Resolução de Problemas em Gateway de Aplicações

Saiba como diagnosticar e resolver problemas que poderá encontrar quando o Azure App Service for utilizado como alvo de back-end com o Portal de Aplicações Azure.

## <a name="overview"></a>Descrição geral

Neste artigo, aprenderá a resolver os seguintes problemas:

> [!div class="checklist"]
> * O URL do serviço de aplicações é exposto no navegador quando há uma reorientação.
> * O domínio de cookies ARRAffinity do serviço de aplicações está definido para o nome do anfitrião do serviço de aplicações, example.azurewebsites.net, em vez do anfitrião original.

Quando uma aplicação de back-end envia uma resposta de reorientação, é melhor redirecionar o cliente para um URL diferente do especificado pela aplicação de back-end. Você pode querer fazê-lo quando um serviço de aplicações está hospedado atrás de um gateway de aplicação e exige que o cliente faça uma reorientação para o seu caminho relativo. Um exemplo é um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2. 

Quando o serviço de aplicações envia uma resposta de reorientação, utiliza o mesmo nome de anfitrião no cabeçalho de localização da sua resposta como o pedido que recebe do gateway da aplicação. Por exemplo, o cliente faz o pedido diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo gateway de aplicação contoso.com/path2. Não quer contornar a porta de entrada de aplicações.

Esta questão pode acontecer pelas seguintes razões principais:

- Tem uma reorientação configurada no seu serviço de aplicações. A reorientação pode ser tão simples como adicionar um corte de rasto ao pedido.
- Tem autenticação de Diretório Ativo Azure, o que causa a reorientação.

Além disso, quando utiliza serviços de aplicações por detrás de um gateway de aplicação, o nome de domínio associado ao gateway da aplicação (example.com) é diferente do nome de domínio do serviço de aplicações (por exemplo, example.azurewebsites.net). O valor de domínio para o cookie ARRAffinity definido pelo serviço de aplicações tem o nome de domínio example.azurewebsites.net, o que não é desejável. O nome original do anfitrião, example.com, deve ser o valor de nome de domínio no cookie.

## <a name="sample-configuration"></a>Configuração da amostra

- Ouvinte HTTP: Básico ou multi-site
- Piscina de endereços de back-end: Serviço de aplicações
- Definições HTTP: **Escolha o nome do anfitrião do endereço backend** ativado
- Sonda: **Escolha o nome do anfitrião das definições** http ativadas

## <a name="cause"></a>Causa

O Serviço de Aplicações é um serviço multiarrendatário, pelo que utiliza o cabeçalho do anfitrião no pedido para encaminhar o pedido para o ponto final correto. O nome de domínio padrão dos Serviços de Aplicação, *.azurewebsites.net (por exemplo, contoso.azurewebsites.net), é diferente do nome de domínio do gateway da aplicação (por exemplo, contoso.com). 

O pedido original do cliente tem o nome de domínio do gateway da aplicação, contoso.com, como nome anfitrião. É necessário configurar a porta de entrada da aplicação para alterar o nome do anfitrião no pedido original para o nome de anfitrião do serviço de aplicações quando encaminha o pedido para o back back back service do serviço de aplicações. Utilize o nome de **anfitrião pick do backend Address** na configuração de definição HTTP do gateway da aplicação. Utilize o nome de **anfitrião pick do backend HTTP Definições** na configuração da sonda de saúde.



![Gateway de aplicação altera nome anfitrião](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Quando o serviço de aplicações faz uma reorientação, utiliza o nome de anfitrião ultrapassado contoso.azurewebsites.net no cabeçalho de localização em vez do nome original do anfitrião contoso.com, salvo configuração em contrário. Verifique os seguintes cabeçalhos de pedido e resposta.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
No exemplo anterior, note que o cabeçalho de resposta tem um código de estado de 301 para reorientação. O cabeçalho de localização tem o nome de `www.contoso.com`anfitrião do serviço de aplicações em vez do nome original do anfitrião .

## <a name="solution-rewrite-the-location-header"></a>Solução: Reescrever o cabeçalho de localização

Dete teo nome do anfitrião no cabeçalho de localização para o nome de domínio do gateway da aplicação. Para isso, crie uma regra de [reescrita](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) com uma condição que avalie se o cabeçalho de localização na resposta contém azurewebsites.net. Deve também realizar uma ação para reescrever o cabeçalho de localização para ter o nome de anfitrião do gateway da aplicação. Para mais informações, consulte as instruções sobre [como reescrever o cabeçalho de localização](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> O suporte de reescrita do cabeçalho HTTP só está disponível para o [Standard_v2 e WAF_v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) do Application Gateway. Se utilizar v1 SKU, recomendamos que [emigra de v1 para v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2). Pretende utilizar reescrita e [outras capacidades avançadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) que estão disponíveis com v2 SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Solução alternativa: Use um nome de domínio personalizado

Se utilizar o V1 SKU, não pode reescrever o cabeçalho de localização. Esta capacidade só está disponível para v2 SKU. Para resolver o problema da reorientação, passe o mesmo nome de anfitrião que o gateway da aplicação também recebe para o serviço de aplicações, em vez de fazer uma sobreposição de anfitriões.

O serviço de aplicações agora faz a reorientação (se houver) no mesmo cabeçalho original do anfitrião que aponta para o gateway da aplicação e não para o seu próprio.

Deve possuir um domínio personalizado e seguir este processo:

- Registe o domínio na lista de domínios personalizados do serviço de aplicações. Deve ter um CNAME no seu domínio personalizado que aponta para o FQDN do serviço de aplicações. Para mais informações, consulte [mapeie um nome dNS personalizado existente para o Serviço de Aplicações Azure](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

    ![Lista de domínio personalizado de serviço de aplicativos](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- O seu serviço de aplicações `www.contoso.com`está pronto para aceitar o nome de anfitrião . Altere a sua entrada CNAME no DNS para a indicar de volta `appgw.eastus.cloudapp.azure.com`para o FQDN do gateway da aplicação, por exemplo, .

- Certifique-se de `www.contoso.com` que o seu domínio se resolve com o FQDN do portal de aplicação quando fizer uma consulta de DNS.

- Desative a sua sonda personalizada para desativar o **nome do anfitrião a partir das definições de BACKEnd HTTP**. No portal Azure, limpe a caixa de verificação nas definições da sonda. No PowerShell, não utilize o interruptor **-PickHostNameFromBackendHttpSettings** no comando **Set-AzApplicationGatewayProbeConfig.** No campo de nome de anfitrião da sonda, insira o FQDN do seu serviço de aplicações, example.azurewebsites.net. Os pedidos de sonda enviados a partir do gateway da aplicação transportam este FQDN no cabeçalho do hospedeiro.

  > [!NOTE]
  > Para o próximo passo, certifique-se de que a sua sonda personalizada não está associada às definições de HTTP de back-end. As definições http ainda têm o nome de **anfitrião Pick do** switch Backend Address ativado neste ponto.

- Detete as definições http do seu gateway de aplicação para desativar **o nome do anfitrião do endereço backend**. No portal Azure, limpe a caixa de verificação. No PowerShell, não utilize o interruptor **-PickHostNameFromBackendAddress** no comando **Set-AzApplicationGatewayBackendHttpSettings.**

- Associe a sonda personalizada de volta às definições de HTTP de back-end e verifique se a parte de trás é saudável.

- O portal da aplicação deve `www.contoso.com`agora encaminhar o mesmo nome de anfitrião, para o serviço de aplicações. A redirecção acontece no mesmo nome de anfitrião. Verifique os seguintes cabeçalhos de pedido e resposta.

Para implementar os passos anteriores utilizando o PowerShell para uma configuração existente, utilize o script PowerShell da amostra que se segue. Note como não usamos os interruptores **-PickHostname** na configuração da sonda e http.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Passos seguintes

Se os passos anteriores não resolverem o problema, abra um bilhete de [apoio.](https://azure.microsoft.com/support/options/)
