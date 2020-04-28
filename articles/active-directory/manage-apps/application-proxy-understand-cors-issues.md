---
title: Compreender e resolver problemas de proxy cors de aplicação da AD Azure
description: Fornece uma compreensão do CORS em Procuração de Aplicação AD Azure, e como identificar e resolver problemas cors.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c49535ad11139ac5145d4f283374bf9cc6d71f52
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72025784"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Compreender e resolver problemas de aplicação de diretório ativo da Azure Proxy CORS

[A partilha de recursos de origem cruzada (CORS)](https://www.w3.org/TR/cors/) pode por vezes apresentar desafios para as aplicações e APIs que publica através do Proxy de Aplicação de Diretório Ativo Azure. Este artigo discute as questões e soluções da Aplicação AD Azure Proxy CORS.

A segurança do navegador geralmente impede uma página web de fazer pedidos DE AJAX para outro domínio. Esta restrição é chamada de *política de origem mesma*e impede um site malicioso de ler dados sensíveis de outro site. No entanto, às vezes é melhor deixar que outros sites chamem a sua Web API. CorS é um padrão W3C que permite que um servidor relaxe a política de origem e permita alguns pedidos de origem cruzada enquanto rejeita outros.

## <a name="understand-and-identify-cors-issues"></a>Compreender e identificar as questões do CORS

Dois URLs têm a mesma origem se tiverem esquemas, hospedeiros e portos idênticos[(RFC 6454),](https://tools.ietf.org/html/rfc6454)tais como:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Os seguintes URLs têm origens diferentes das duas anteriores:

-   http:\//contoso.net - Domínio diferente
-   http:\//contoso.com:9000/foo.html - Porto diferente
-   https:\//contoso.com/foo.html - Esquema diferente
-   http:\//www.contoso.com/foo.html - Subdomínio diferente

A política de origem impede que as aplicações acedam a recursos de outras origens, a menos que utilizem os cabeçalhos de controlo de acesso corretos. Se os cabeçalhos cors estiverem ausentes ou incorretos, os pedidos de origem cruzada falham. 

Pode identificar problemas cors utilizando ferramentas de depuração do navegador:

1. Lance o navegador e navegue na aplicação web.
1. Pressione **o F12** para trazer a consola de depuração.
1. Tente reproduzir a transação e reveja a mensagem da consola. Uma violação corsa produz um erro de consola sobre a origem.

Na seguinte imagem, a seleção do botão **Try It** \/causou uma mensagem de erro CORS que https: /corswebclient-contoso.msappproxy.net não foi encontrada no cabeçalho Access-Control-Allow-Origin.

![Edição cor-de-cor](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CORS desafia com procuração de aplicação

O exemplo seguinte mostra um cenário típico de procuração de aplicação ad-ad-azure proxy CORS. O servidor interno acolhe um controlador API web **CORSWebService** e um **CORSWebClient** que chama **CORSWebService**. Há um pedido do AJAX do **CORSWebClient** para **o CORSWebService.**

![Pedido de origem no local](./media/application-proxy-understand-cors-issues/image1.png)

A aplicação CORSWebClient funciona quando a hospeda no local, mas ou não carrega ou falha quando publicada através do Proxy de Aplicação AD Azure. Se publicou as aplicações CORSWebClient e CORSWebService separadamente como diferentes aplicações através do Application Proxy, as duas aplicações são alojadas em diferentes domínios. Um pedido do AJAX do CORSWebClient para corSWebService é um pedido de origem cruzada, e falha.

![Pedido de Procuração de Pedidos CORS](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Soluções para questões de Proxy DE Aplicação CORS

Pode resolver a questão cors a preceder de várias maneiras.

### <a name="option-1-set-up-a-custom-domain"></a>Opção 1: Criar um domínio personalizado

Utilize um [domínio personalizado](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) de Procuração de Aplicação AD Azure para publicar a partir da mesma origem, sem ter de fazer alterações nas origens da aplicação, código ou cabeçalhos. 

### <a name="option-2-publish-the-parent-directory"></a>Opção 2: Publicar o directório-mãe

Publique o directório-mãe de ambas as aplicações. Esta solução funciona especialmente bem se tiver apenas duas aplicações no servidor web. Em vez de publicar cada aplicação separadamente, pode publicar o diretório comum dos pais, que resulta na mesma origem.

Os exemplos seguintes mostram a página de Proxy de Aplicação adriada do portal Azure AD para a aplicação CORSWebClient.  Quando o **URL Interno** está definido para *contoso.com/CORSWebClient,* a aplicação não pode fazer pedidos bem sucedidos para o *diretório contoso.com/CORSWebService,* porque são de origem cruzada. 

![Publicar app individualmente](./media/application-proxy-understand-cors-issues/image4.png)

Em vez disso, delineie o **URL Interno** para publicar o directório-mãe, que inclui tanto os diretórios *CORSWebClient* como *CORSWebService:*

![Publicar diretório-mãe](./media/application-proxy-understand-cors-issues/image5.png)

Os URLs da aplicação resultante resolvem eficazmente a questão do CORS:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Opção 3: Atualizar os cabeçalhos HTTP

Adicione um cabeçalho de resposta HTTP personalizado no serviço web para corresponder ao pedido de origem. Para sites em execução em Serviços de Informação na Internet (IIS), utilize o Gestor IIS para modificar o cabeçalho:

![Adicione cabeçalho de resposta personalizado no Gestor IIS](./media/application-proxy-understand-cors-issues/image6.png)

Esta modificação não requer alterações de código. Pode verificar nos vestígios do Violinista:

**Postar a Adição do Cabeçalho**\
HTTP/1.1 200 OK\
Cache-Control: sem cache\
Pragma: sem cache\
Tipo de conteúdo: texto/planície; charset=utf-8\
Expira: -1\
Variação: Aceitar-codificar\
Servidor: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Acesso-Controlo-Acesso-Origem:\:https //corswebclient-contoso.msappproxy.net**\
X-AspNet-Versão: 4.0.30319\
X-Powered-By: ASP.NET\
Comprimento do conteúdo: 17

### <a name="option-4-modify-the-app"></a>Opção 4: Modificar a aplicação

Pode alterar a sua aplicação para suportar o CORS adicionando o cabeçalho access-Control-Allow-Origin, com valores apropriados. A forma de adicionar o cabeçalho depende da linguagem de código da aplicação. Alterar o código é a opção menos recomendada, porque requer mais esforço.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Opção 5: Prolongar a vida útil do token de acesso

Algumas questões cors não podem ser resolvidas, como quando a sua aplicação redireciona para *login.microsoftonline.com* para autenticar, e o token de acesso expira. A chamada corsa falha. Uma suposição para este cenário é prolongar o tempo de vida útil do token de acesso, para evitar que expire durante a sessão do utilizador. Para mais informações sobre como fazê-lo, consulte a [configurável vida útil em Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Consulte também
- [Tutorial: Adicione um pedido no local para acesso remoto através de Application Proxy no Diretório Ativo Azure](application-proxy-add-on-premises-application.md) 
- [Planeie uma implementação de proxy de aplicação ad azure](application-proxy-deployment-plan.md) 
- [Acesso remoto a aplicações no local através do Procurador de Aplicações de Diretório Ativo do Azure](application-proxy.md) 
