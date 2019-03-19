---
title: Resolver problemas do Gateway de aplicação do Azure com o serviço de aplicações – redirecionamento de URL do serviço de aplicações
description: Este artigo fornece informações sobre como resolver o problema de redirecionamento, quando o Gateway de aplicação do Azure é utilizado com o serviço de aplicações do Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 359d75f10f95b0e41ccd9a869d49247355f0d5d0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123186"
---
# <a name="troubleshoot-application-gateway-with-app-service--redirection-to-app-services-url"></a>Resolver problemas do Gateway de Aplicação com o Serviço de Aplicações – redirecionamento de URL do Serviço de Aplicações

 Saiba como diagnosticar e resolver problemas de redirecionamento com Gateway de aplicação em que o URL do serviço de aplicações é obter exposto.

## <a name="overview"></a>Descrição geral

Quando configura um público com acesso à aplicação de serviço do conjunto de back-end do Gateway de aplicação e se tiver um redirecionamento configurado no código da aplicação, poderá ver que quando acessar o Gateway de aplicação, será redirecionado pelo navegador diretamente para a aplicação URL do serviço.

Este problema pode ocorrer devido ao seguinte principal:

- Tem o redirecionamento configurado no seu serviço de aplicações. Redirecionamento pode ser tão simples quanto adicionar uma barra à solicitação.
- Tem a autenticação do Azure AD, que faz com que o redirecionamento.
- Ativou a opção "Escolher a nome de anfitrião do endereço de back-end" nas definições de HTTP do Gateway de aplicação.
- Não tem o seu domínio personalizado registrado com o serviço de aplicações.

## <a name="sample-configuration"></a>Configuração de exemplo

- Serviço de escuta de HTTP: Básico ou de múltiplos sites
- Conjunto de endereços de back-end: Serviço de Aplicações
- Definições de HTTP: "Selecionar nome de anfitrião do endereço de back-end" ativada
- Sonda: "Selecionar nome de anfitrião de definições de HTTP" ativada

## <a name="cause"></a>Causa

Um serviço de aplicações só podem ser acedido com os nomes de anfitriões configurado nas definições de domínio personalizado, por predefinição, é "example.azurewebsites.net" e se desejar acessar seu serviço de aplicações com o Gateway de aplicação com um nome de anfitrião não registado no serviço de aplicações ou com Do Gateway de aplicação FQDN, terá de substituir o nome de anfitrião no pedido original para o nome de anfitrião do serviço de aplicações.

Para fazê-lo com o Gateway de aplicação, vamos utilizar a opção "Selecionar nome de anfitrião de back-end endereço" nas definições de HTTP e para a sonda, para trabalhar, usamos "Escolher nome de anfitrião de back-end HTTP definições" na configuração da pesquisa.

![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Por isso, quando o serviço de aplicação faz um redirecionamento, utiliza o nome de anfitrião "example.azurewebsites.net" no cabeçalho Location, em vez do nome de anfitrião original, a menos que configurado em contrário. Pode verificar os exemplo resposta cabeçalhos de solicitação e abaixo.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://example.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.azurewebsites.net

X-Powered-By: ASP.NET
```
No exemplo acima, pode notar que o cabeçalho de resposta tem um código de estado de 301 para o redirecionamento e o cabeçalho de localização tem de nome de anfitrião do serviço de aplicações em vez do nome de anfitrião original "www.contoso.com".

## <a name="solution"></a>Solução

Este problema pode ser resolvido ao não ter um redirecionamento no lado do aplicativo, no entanto, se não for possível, que tem de passar o mesmo cabeçalho de anfitrião que recebe do Gateway de aplicação para o serviço de aplicações também em vez de fazer uma substituição de anfitrião.

Depois de fazer isso, o serviço de aplicações fará o redirecionamento (se houver) sobre o mesmo cabeçalho de anfitrião original que aponta para o Gateway de aplicação e não seu próprio.

Para conseguir isso, tem de possuir um domínio personalizado e siga o processo mencionado a seguir.

- Registre-se o domínio para a lista de domínio personalizado do serviço de aplicações. Para isso, tem de ter um CNAME no seu domínio personalizado que aponta para o FQDN de serviço de aplicações. Para obter mais informações, consulte [mapear um nome DNS existente personalizado para o serviço de aplicações do Azure](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Depois disso, o serviço de aplicações está pronto para aceitar o nome de anfitrião "www.contoso.com". Agora, altere a entrada CNAME no DNS para apontá-lo novamente para o FQDN do Gateway de aplicação. Por exemplo, "appgw.eastus.cloudapp.azure.com".

- Certifique-se de que o seu domínio "www.contoso.com" é resolvido para o FQDN do Gateway de aplicação quando o fizer uma consulta DNS.

- Defina a sonda personalizada para desativar "Escolher nome de anfitrião de back-end definições de HTTP". Isso pode ser feito a partir do portal, desmarcando a caixa de verificação nas definições de pesquisa e no PowerShell não for utilizado - PickHostNameFromBackendHttpSettings mude no comando Set-AzApplicationGatewayProbeConfig. No campo de nome de anfitrião da sonda, introduza o FQDN de serviço de aplicações "example.azurewebsites.net", tal como os pedidos de pesquisa enviados a partir do Gateway de aplicação irão ser isso no cabeçalho de anfitrião.

  > [!NOTE]
  > Ao fazer o próximo passo, certifique-se de que a sonda personalizada não está associada às suas definições de HTTP de back-end porque seus ainda de definições de HTTP tem a opção "Selecionar nome de anfitrião de back-end endereço" ativada neste momento.

- Configure as definições de HTTP do seu Gateway de aplicação para desativar "Escolher nome de anfitrião de back-end Address". Isso pode ser feito no portal, desmarcando a caixa de verificação e no PowerShell com o não - PickHostNameFromBackendAddress mude no comando Set-AzApplicationGatewayBackendHttpSettings.

- Associar a sonda personalizada para as definições de HTTP de back-end e verifique se o estado de funcionamento do back-end, se ele está em bom estado.

- Depois de o fazer, o Gateway de aplicação encaminhe o mesmo nome de anfitrião "www.contoso.com" para o serviço de aplicações e o redirecionamento terá lugar ao mesmo nome de anfitrião. Pode verificar os exemplo resposta cabeçalhos de solicitação e abaixo.
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
  ## <a name="next-steps"></a>Passos Seguintes

Se os passos anteriores não resolverem o problema, abra um [pedido de suporte](https://azure.microsoft.com/support/options/).
