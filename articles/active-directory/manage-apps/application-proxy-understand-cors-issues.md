---
title: Compreender e resolver problemas de CORS de Proxy de aplicações do Azure AD
description: Fornece uma compreensão de CORS no Proxy de aplicações do Azure AD e como identificar e resolver problemas CORS.
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
ms.openlocfilehash: 2b6adcf4231aa44a4f28d277e963efa16de8af81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399335"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Compreender e resolver problemas de CORS de Proxy de aplicações do Azure Active Directory

[Recursos de várias origens (CORS) de partilha](http://www.w3.org/TR/cors/) , às vezes, pode apresentar desafios para as aplicações e APIs publicar através do Proxy de aplicações de diretório Active Directory do Azure. Este artigo aborda problemas de CORS de Proxy de aplicações do Azure AD e soluções.

Segurança do navegador geralmente impede que uma página da web realize pedidos de AJAX para outro domínio. Esta restrição é chamada de *política de mesma origem*e impede que um site malicioso leia dados confidenciais de outro site. No entanto, às vezes, pode querer permitir que outros sites chamar a API web. CORS é uma norma W3C que permite que um servidor reduzir a política de mesma origem e permitir que alguns pedidos de várias origens, ao mesmo tempo que rejeita outros.

## <a name="understand-and-identify-cors-issues"></a>Compreender e a identificar problemas CORS

Dois URLs têm a mesma origem se tiverem esquemas idênticas, anfitriões e as portas ([RFC 6454](https://tools.ietf.org/html/rfc6454)), tais como:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Os seguintes URLs têm diferentes origens anterior dois:

-   http:\//contoso.net - domínio diferente
-   http:\//contoso.com:9000/foo.html - porta diferente
-   https:\//contoso.com/foo.html - esquema diferente
-   http:\//www.contoso.com/foo.html - subdomínio diferente

Política de mesma origem impede as aplicações acedem aos recursos a partir de outras origens, a menos que eles usam os cabeçalhos de controlo de acesso correto. Se os cabeçalhos CORS ausentes ou incorretos, falharem pedidos de várias origens. 

Pode identificar problemas CORS utilizando ferramentas de depuração de browser:

1. Inicie o browser e navegue para a aplicação web.
1. Prima **F12** para abrir a consola de depuração.
1. Tente reproduzir a transação e reveja a mensagem de consola. Uma violação de CORS produz um erro de consola sobre a origem.

Captura de ecrã seguinte, selecionando o **experimentar** botão gerou uma mensagem de erro CORS que https:\//corswebclient-contoso.msappproxy.net não foi encontrado no cabeçalho Access-Control-Allow-Origin.

![Problema CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Desafios CORS com o Proxy de aplicações

O exemplo seguinte mostra um típico cenário de CORS de Proxy de aplicações do Azure AD. Os anfitriões de servidor interno uma **CORSWebService** controlador da web API e um **CORSWebClient** que chama **CORSWebService**. Há uma solicitação AJAX partir **CORSWebClient** ao **CORSWebService**.

![Pedido de mesma origem no local](./media/application-proxy-understand-cors-issues/image1.png)

A aplicação de CORSWebClient funciona quando hospedá-lo no local, mas a falha para a carga ou erros de saída quando publicado através do Proxy de aplicações do Azure AD. Se publicou as aplicações CORSWebClient e CORSWebService separadamente como diferentes aplicações através do Proxy de aplicações, duas aplicações alojadas em domínios diferentes. Uma solicitação AJAX de CORSWebClient para CORSWebService é um pedido de várias origens e este falhar.

![Pedido de CORS do Proxy de aplicação](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Soluções para problemas de CORS do Proxy de aplicação

Pode resolver o problema CORS anterior em qualquer uma das várias formas.

### <a name="option-1-set-up-a-custom-domain"></a>Opção 1: Configurar um domínio personalizado

Utilizar um Proxy de aplicações do Azure AD [domínio personalizado](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) para publicar a partir da mesma origem, sem ter de fazer alterações às origens de aplicações, código ou cabeçalhos. 

### <a name="option-2-publish-the-parent-directory"></a>Opção 2: Publicar o diretório principal

Publica o diretório principal de ambas as aplicações. Esta solução funciona especialmente bem se tiver apenas duas aplicações no servidor web. Em vez de cada aplicação de publicação em separado, pode publicar o diretório principal comum, que resulta na mesma origem.

Os exemplos seguintes mostram o página de Proxy de aplicações do Azure AD para a aplicação de CORSWebClient do portal.  Quando o **URL interno** está definida como *contoso.com/CORSWebClient*, a aplicação não é possível efetuar pedidos com êxito para o *contoso.com/CORSWebService* directory, porque eles são várias origens. 

![Publicar aplicação individualmente](./media/application-proxy-understand-cors-issues/image4.png)

Em alternativa, defina o **URL interno** para publicar o diretório principal, que inclui ambos os *CORSWebClient* e *CORSWebService* diretórios:

![Publicar o diretório principal](./media/application-proxy-understand-cors-issues/image5.png)

Os URLs da aplicação resultante efetivamente resolver o problema CORS:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Opção 3: Atualizar os cabeçalhos HTTP

Adicione um cabeçalho de resposta HTTP personalizado no serviço web para comparar a solicitação de origem. Para Web sites em execução nos serviços de informação Internet (IIS), utilize o Gestor do IIS para modificar o cabeçalho:

![Adicionar cabeçalho de resposta personalizada no Gerenciador do IIS](./media/application-proxy-understand-cors-issues/image6.png)

Essa modificação não requer quaisquer alterações de código. Pode verificá-lo nos rastreios de Fiddler:

**A adição de cabeçalho de mensagem**\
HTTP/1.1 200 OK\
Cache-Control: não-cache\
Pragma: no-cache\
Content-Type: text/plain; charset=utf-8\
Expira:-1 \
Variar: Encoding\ aceite
Servidor: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin: https://corswebclient-contoso.msappproxy.net** \
X-AspNet-Version: 4.0.30319\
X-com tecnologia-por: ASP.NET\
Content-Length: 17

### <a name="option-4-modify-the-app"></a>Opção 4: Modificar a aplicação

Pode alterar a sua aplicação para suportar CORS ao adicionar o cabeçalho Access-Control-Allow-Origin, com os valores adequados. A maneira de adicionar o cabeçalho depende da linguagem de código da aplicação. Alterar o código é a opção menos recomendada, porque exige o maioria dos esforço.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Opção 5: Expandir o tempo de vida do token de acesso

Alguns problemas CORS não podem ser resolvidos, por exemplo, quando a sua aplicação redireciona para *login.microsoftonline.com* para autenticar, e o token de acesso expira. Os CORS chamar, em seguida, falhar. É uma solução para este cenário para expandir o tempo de vida do token de acesso, para que não expirar durante uma sessão de utilizador. Para obter mais informações sobre como fazer isso, consulte [durações de token configuráveis no Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Consulte também
- [Tutorial: Adicionar uma aplicação no local para acesso remoto através do Proxy de aplicações no Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Planear uma implementação do Proxy de aplicações do Azure AD](application-proxy-deployment-plan.md) 
- [Acesso remoto às aplicações no local através do Proxy de aplicações do Azure Active Directory](application-proxy.md) 
