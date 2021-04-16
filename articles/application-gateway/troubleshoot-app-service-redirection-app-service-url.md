---
title: Reorientação de resolução de problemas para URL do Serviço de Aplicações
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como resolver problemas na resolução do problema de reorientação quando o Azure Application Gateway é usado com o Azure App Service
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/15/2021
ms.author: jaysoni
ms.openlocfilehash: 6aad1cf1269a7c3dc082482c39fdc4a079fc3240
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514891"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Problemas no Serviço de Aplicações de Resolução de Problemas no Gateway de Aplicações

Saiba como diagnosticar e resolver problemas que poderá encontrar quando o Azure App Service for usado como alvo de back-end com o Azure Application Gateway.

## <a name="overview"></a>Descrição Geral

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

Desa parte do nome do anfitrião no cabeçalho de localização para o nome de domínio do gateway de aplicação. Para isso, crie uma [regra de reescrita](./rewrite-http-headers.md) com uma condição que avalie se o cabeçalho de localização na resposta contém azurewebsites.net. Deve também realizar uma ação para reescrever o cabeçalho de localização para ter o nome de anfitrião do gateway de aplicação. Para obter mais informações, consulte instruções sobre [como reescrever o cabeçalho de localização](./rewrite-http-headers.md#modify-a-redirection-url).

> [!NOTE]
> O suporte de reescrita do cabeçalho HTTP só está disponível para o [Standard_v2 e WAF_v2 SKU](./application-gateway-autoscaling-zone-redundant.md) do Gateway de Aplicações. Recomendamos [migrar para v2](./migrate-v1-v2.md) para a Reescrita do Cabeçalho e [outras capacidades avançadas](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) que estejam disponíveis com v2 SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Solução alternativa: Use um nome de domínio personalizado

A utilização da funcionalidade de Domínio Personalizado do Serviço de Aplicações é outra solução para redirecionar sempre o tráfego para o nome de domínio da App Gateway `www.contoso.com` (no nosso exemplo). Esta configuração também serve como solução para o problema dos cookies ARR Affinity. Por predefinição, o domínio de cookies ARRAffinity é definido para o nome de anfitrião padrão do Serviço de Aplicações (example.azurewebsites.net) em vez do nome de domínio do Gateway de Aplicação. Portanto, o navegador nesses casos rejeitará o cookie devido à diferença nos nomes de domínio do pedido e do cookie.

Pode seguir o método dado tanto para os problemas de conformidade entre o domínio de redirecionamento e o domínio de cookies da ARRAffinity. Este método necessitará que você tenha acesso à zona DNS do seu domínio personalizado.

**Passo 1**: Desista um domínio personalizado no Serviço de Aplicações e verifique a propriedade do domínio adicionando os [registos DE & DENS NNS CNAME](../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id).
Os registos seriam semelhantes a
-  `www.contoso.com` EM CNAME `contoso.azurewebsite.net`
-  `asuid.www.contoso.com` EM TXT `<verification id string>` "


**Passo 2**: O registo CNAME no passo anterior só era necessário para a verificação do domínio. Em última análise, precisamos do tráfego para a rota através do Gateway de Aplicação. Pode, assim, modificar `www.contoso.com` o CNAME da Aplicação agora para apontar para o FQDN do Gateway de aplicação. Para definir um FQDN para o seu Gateway de aplicações, navegue para o seu recurso de endereço IP público e atribua uma "etiqueta de nome DNS" para o mesmo. O registo CNAME atualizado deve agora olhar como 
-  `www.contoso.com` EM CNAME `contoso.eastus.cloudapp.azure.com`


**Passo 3**: Desative o "Nome de anfitrião do endereço de backend" para a definição HTTP associada.

No PowerShell, não utilize o `-PickHostNameFromBackendAddress` interruptor no `Set-AzApplicationGatewayBackendHttpSettings` comando.


**Passo 4**: Para que as sondas determinem o backend como saudável e um tráfego operacional, defina uma Sonda de Saúde personalizada com o campo Host como domínio personalizado ou predefinido do Serviço de Aplicações.

No PowerShell, não utilize o `-PickHostNameFromBackendHttpSettings` interruptor no comando e utilize o domínio personalizado ou `Set-AzApplicationGatewayProbeConfig` predefinido do Serviço de Aplicações no interruptor -HostName da sonda.

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
