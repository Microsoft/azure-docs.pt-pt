---
title: Reorientação de resolução de problemas para URL do Serviço de Aplicações
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como resolver problemas na resolução do problema de reorientação quando o Azure Application Gateway é usado com o Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: f3a3ba3ee908204668ad9d7201ddfddec0a26f28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89595949"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Problemas no Serviço de Aplicações de Resolução de Problemas no Gateway de Aplicações

Saiba como diagnosticar e resolver problemas que poderá encontrar quando o Azure App Service for usado como alvo de back-end com o Azure Application Gateway.

## <a name="overview"></a>Descrição geral

Neste artigo, você vai aprender a resolver problemas sobre as seguintes questões:

* O URL do serviço de aplicações é exposto no navegador quando há uma reorientação.
* O domínio de cookies do serviço de aplicações ARRAffinity está definido para o nome do anfitrião do serviço de aplicações, example.azurewebsites.net, em vez do anfitrião original.

Quando uma aplicação de back-end envia uma resposta de redirecionamento, é melhor redirecionar o cliente para um URL diferente do especificado pela aplicação back-end. É melhor fazê-lo quando um serviço de aplicações está hospedado atrás de um gateway de aplicações e requer que o cliente faça uma reorientação para o seu caminho relativo. Um exemplo é um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2. 

Quando o serviço de aplicações envia uma resposta de redirecionamento, utiliza o mesmo nome de anfitrião no cabeçalho de localização da sua resposta que o pedido que recebe do gateway da aplicação. Por exemplo, o cliente faz o pedido diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo gateway de aplicação contoso.com/path2. Não queres contornar o portal de aplicações.

Esta questão pode acontecer pelas seguintes razões:

- Tem a reorientação configurada no seu serviço de aplicações. A reorientação pode ser tão simples como adicionar um corte de fuga ao pedido.
- Tem autenticação do Azure Ative Directory, que provoca a reorientação.

Além disso, quando utiliza serviços de aplicações por detrás de um gateway de aplicações, o nome de domínio associado ao gateway de aplicações (example.com) é diferente do nome de domínio do serviço de aplicações (por exemplo, example.azurewebsites.net). O valor de domínio para o cookie ARRAffinity definido pelo serviço de aplicações tem o nome de domínio example.azurewebsites.net, o que não é desejável. O nome original do anfitrião, example.com, deve ser o valor do nome de domínio no cookie.

## <a name="sample-configuration"></a>Configuração da amostra

- OUVINTE HTTP: Básico ou multi-site
- Piscina de endereços back-end: Serviço de Aplicações
- Definições HTTP: **Escolha o nome anfitrião do Endereço de Backend** ativado
- Sonda: **Escolha o nome anfitrião a partir de definições HTTP** ativadas

## <a name="cause"></a>Causa

O App Service é um serviço multitenante, pelo que utiliza o cabeçalho do anfitrião no pedido para encaminhar o pedido para o ponto final correto. O nome de domínio padrão dos Serviços de Aplicações, *.azurewebsites.net (por exemplo, contoso.azurewebsites.net), é diferente do nome de domínio do gateway de aplicação (por exemplo, contoso.com). 

O pedido original do cliente tem o nome de domínio do gateway de aplicação, contoso.com, como nome de anfitrião. É necessário configurar o gateway da aplicação para alterar o nome de anfitrião no pedido original para o nome de anfitrião do serviço de aplicações quando este encaminha o pedido para o serviço de aplicações de volta. Utilize o nome **de anfitrião pick-switch do Backend Address** na configuração de definição HTTP do gateway de aplicações. Utilize o nome de anfitrião do interruptor **Pick Hostname a partir de definições HTTP backend** na configuração da sonda de saúde.



![Gateway de aplicação altera nome de anfitrião](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Quando o serviço de aplicações faz uma reorientação, utiliza o nome de anfitrião ultrapassado contoso.azurewebsites.net no cabeçalho de localização em vez do nome de anfitrião original contoso.com, salvo configuração em contrário. Verifique os seguintes exemplos de pedidos e cabeçalhos de resposta.
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
No exemplo anterior, note que o cabeçalho de resposta tem um código de estado de 301 para reorientação. O cabeçalho de localização tem o nome de anfitrião do serviço de aplicações em vez do nome original do anfitrião `www.contoso.com` .

## <a name="solution-rewrite-the-location-header"></a>Solução: Reescrever o cabeçalho de localização

Desa parte do nome do anfitrião no cabeçalho de localização para o nome de domínio do gateway de aplicação. Para isso, crie uma [regra de reescrita](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) com uma condição que avalie se o cabeçalho de localização na resposta contém azurewebsites.net. Deve também realizar uma ação para reescrever o cabeçalho de localização para ter o nome de anfitrião do gateway de aplicação. Para obter mais informações, consulte instruções sobre [como reescrever o cabeçalho de localização](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> O suporte de reescrita do cabeçalho HTTP só está disponível para o [Standard_v2 e WAF_v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) do Gateway de Aplicações. Se utilizar v1 SKU, recomendamos que [migrar de V1 para v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2). Pretende utilizar a reescrita e [outras capacidades avançadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) que estão disponíveis com v2 SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Solução alternativa: Use um nome de domínio personalizado

Se utilizar v1 SKU, não pode reescrever o cabeçalho de localização. Esta capacidade só está disponível para v2 SKU. Para resolver o problema de redirecionamento, passe o mesmo nome de anfitrião que o gateway de aplicações também recebe para o serviço de aplicações, em vez de fazer uma substituição de anfitrião.

O serviço de aplicações agora faz a reorientação (se houver) no mesmo cabeçalho original do anfitrião que aponta para o gateway da aplicação e não para o seu próprio.

Você deve possuir um domínio personalizado e seguir este processo:

- Registe o domínio na lista de domínios personalizados do serviço de aplicações. Você deve ter um CNAME no seu domínio personalizado que aponta para o FQDN do serviço de aplicações. Para obter mais informações, consulte [mapear um nome DNS personalizado existente para o Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

    ![Lista de domínio personalizado de serviço de aplicações](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- O seu serviço de aplicações está pronto para aceitar o nome de `www.contoso.com` anfitrião. Altere a sua entrada CNAME em DNS para apontá-la de volta para o FQDN do gateway de aplicações, por exemplo, `appgw.eastus.cloudapp.azure.com` .

- Certifique-se de que o seu domínio `www.contoso.com` se resolve com o FQDN do gateway de aplicações quando fizer uma consulta de DNS.

- Defina a sua sonda personalizada para desativar **o nome de anfitrião das definições HTTP Backend**. No portal Azure, limpe a caixa de verificação nas definições da sonda. No PowerShell, não utilize o interruptor **-PickHostNameFromBackendHttpSettings** no comando **Set-AzApplicationGatewayProbeConfig.** No campo de nome de anfitrião da sonda, insira o FQDN do seu serviço de aplicações, example.azurewebsites.net. Os pedidos de sonda enviados a partir do gateway de aplicação transportam este FQDN no cabeçalho do hospedeiro.

  > [!NOTE]
  > Para o próximo passo, certifique-se de que a sua sonda personalizada não está associada às definições HTTP de back-end. As definições HTTP ainda têm o **nome de anfitrião pick-host do** switch backend Address ativo neste ponto.

- Defina as definições HTTP do gateway de aplicações para desativar **o nome de anfitrião do Backend Address**. No portal Azure, limpe a caixa de verificação. No PowerShell, não utilize o interruptor **-PickHostNameFromBackendAddress** no comando **Set-AzApplicationGatewayBackendHttpSettings.**

- Associe a sonda personalizada de volta às definições HTTP de fundo e verifique se a parte de trás está saudável.

- O gateway da aplicação deve agora encaminhar o mesmo nome de anfitrião `www.contoso.com` para o serviço de aplicações. A reorientação acontece no mesmo nome de hospedeiro. Verifique os seguintes exemplos de pedidos e cabeçalhos de resposta.

Para implementar os passos anteriores utilizando o PowerShell para uma configuração existente, utilize o script PowerShell da amostra que se segue. Note como não usamos os interruptores **-PickHostname** na configuração de configurações de sonda e HTTP.

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

Se os passos anteriores não resolverem o problema, abra um [bilhete de apoio](https://azure.microsoft.com/support/options/).
