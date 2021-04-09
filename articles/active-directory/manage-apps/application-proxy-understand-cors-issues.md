---
title: Compreender e resolver problemas de aplicação Azure AD CORS
description: Fornece uma compreensão do CORS no Azure AD Application Proxy, e como identificar e resolver problemas de CORS.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: b57fc7e3af99819c9b27b6bc796e501d1db02818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259173"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Entender e resolver problemas do CORS do Proxy de Aplicações no AAD

[A partilha de recursos de origem cruzada (CORS)](https://www.w3.org/TR/cors/) pode por vezes apresentar desafios para as aplicações e APIs que publica através do Azure Ative Directory Application Proxy. Este artigo discute questões e soluções de procuração de Azure AD.

A segurança do navegador geralmente impede que uma página web esmutando pedidos AJAX para outro domínio. Esta restrição é chamada *de política de origem da mesma origem*, e impede um site malicioso de ler dados sensíveis de outro site. No entanto, às vezes pode querer que outros sites liguem para a sua API web. O CORS é um padrão W3C que permite que um servidor relaxe a mesma política de origem e permita alguns pedidos de origem cruzada enquanto rejeita outros.

## <a name="understand-and-identify-cors-issues"></a>Compreender e identificar problemas de CORS

Dois URLs têm a mesma origem se tiverem esquemas, hospedeiros e portos idênticos[(RFC 6454),](https://tools.ietf.org/html/rfc6454)tais como:

-   http: \/ /contoso.com/foo.html
-   http: \/ /contoso.com/bar.html

Os URLs que se seguem têm origens diferentes das duas anteriores:

-   http: \/ /contoso.net - Domínio diferente
-   http: \/ /contoso.com:9000/foo.html - Porta diferente
-   https: \/ /contoso.com/foo.html - Esquema diferente
-   http: \/ /www.contoso.com/foo.html - Subdomínio diferente

A política de origem da mesma origem impede que as aplicações acedam a recursos de outras origens, a menos que utilizem os cabeçalhos de controlo de acesso corretos. Se os cabeçalhos CORS estiverem ausentes ou incorretos, os pedidos de origem cruzada falham. 

Pode identificar problemas de CORS utilizando ferramentas de depuragem de navegador:

1. Inicie o browser e navegue até à aplicação Web.
1. Prima **F12** para abrir a consola de depuração.
1. Tente reproduzir a transação e leia a mensagem da consola. Uma violação do CORS produz um erro na consola sobre a origem.

Na imagem seguinte, a seleção do botão **Try It** causou uma mensagem de erro CORS que https: \/ /corswebclient-contoso.msappproxy.net não foi encontrada no cabeçalho Access-Control-Allow-Origin.

![Problema cors](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CORS desafia com Aplicação Proxy

O exemplo a seguir mostra um cenário típico de aplicação AD AD Proxy CORS. O servidor interno acolhe um controlador API web **CORSWebService** e um **CORSWebClient** que chama **CORSWebService**. Há um pedido AJAX do **CORSWebClient** para **o CORSWebService**.

![Pedido de origem no local](./media/application-proxy-understand-cors-issues/image1.png)

A aplicação CORSWebClient funciona quando a hospeda no local, mas não consegue carregar ou erros quando publicada através do Azure AD Application Proxy. Se publicou as aplicações CORSWebClient e CORSWebService separadamente como diferentes aplicações através do Application Proxy, as duas aplicações estão hospedadas em diferentes domínios. Um pedido ajax do CORSWebClient para o CORSWebService é um pedido de origem cruzada, e falha.

![Pedido de procuração de aplicativos CORS](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Soluções para problemas de Procuração de Aplicações CORS

Pode resolver o problema cors anterior de qualquer uma das várias formas.

### <a name="option-1-set-up-a-custom-domain"></a>Opção 1: Criar um domínio personalizado

Utilize um [domínio personalizado](./application-proxy-configure-custom-domain.md) Azure AD Application Proxy para publicar da mesma origem, sem ter de escruissar quaisquer alterações às origens da aplicação, código ou cabeçalhos. 

### <a name="option-2-publish-the-parent-directory"></a>Opção 2: Publicar o diretório dos pais

Publique o diretório dos dois aplicativos. Esta solução funciona especialmente bem se tiver apenas duas aplicações no servidor web. Em vez de publicar cada aplicação separadamente, pode publicar o diretório parental comum, o que resulta na mesma origem.

Os exemplos a seguir mostram a página de procuração de aplicações Ad do portal Azure para a aplicação CORSWebClient.  Quando o **URL Interno** está definido para *contoso.com/CORSWebClient,* a aplicação não pode fazer pedidos bem sucedidos ao *diretório contoso.com/CORSWebService,* porque são de origem cruzada. 

![Publicar app individualmente](./media/application-proxy-understand-cors-issues/image4.png)

Em vez disso, desaveja o **URL interno** para publicar o diretório dos pais, que inclui tanto os diretórios *CORSWebClient* como *o CORSWebService:*

![Publicar diretório de pais](./media/application-proxy-understand-cors-issues/image5.png)

Os URLs de aplicação resultantes resolvem eficazmente a questão do CORS:

- https: \/ /corswebclient-contoso.msappproxy.net/CORSWebService
- https: \/ /corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Opção 3: Atualizar cabeçalhos HTTP

Adicione um cabeçalho de resposta HTTP personalizado no serviço web para corresponder ao pedido de origem. Para sites em execução em Serviços de Informação da Internet (IIS), utilize o IIS Manager para modificar o cabeçalho:

![Adicione cabeçalho de resposta personalizado no IIS Manager](./media/application-proxy-understand-cors-issues/image6.png)

Esta modificação não requer alterações de código. Pode verificar nos vestígios do Fiddler:

**Poste a adição do cabeçalho**\
HTTP/1.1 200 OK\
Cache-Control: no-cache\
Pragma: sem cache\
Tipo de conteúdo: texto/planície; charset=utf-8\
Expira: -1\
Variar: Aceitar-Codificar\
Servidor: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Acesso-Controlo-Permitir-Origem: https \: //corswebclient-contoso.msappproxy.net**\
Versão X-AspNet: 4.0.30319\
X-Powered-By: ASP.NET\
Comprimento do conteúdo: 17

### <a name="option-4-modify-the-app"></a>Opção 4: Modificar a aplicação

Pode alterar a sua aplicação para apoiar o CORS adicionando o cabeçalho Access-Control-Allow-Origin, com valores adequados. A forma de adicionar o cabeçalho depende do idioma de código da aplicação. Mudar o código é a opção menos recomendada, pois requer mais esforço.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Opção 5: Prolongar a vida útil do token de acesso

Alguns problemas do CORS não podem ser resolvidos, como quando a sua aplicação redireciona para *login.microsoftonline.com* para autenticar, e o token de acesso expira. A chamada do CORS falhará. Uma solução para este cenário é prolongar a duração do token de acesso para impedir que expire durante uma sessão do utilizador. Para obter mais informações sobre como fazê-lo, consulte [as vidas simbólicas em Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Ver também
- [Tutorial: Adicionar uma aplicação no local para acesso remoto através de Aplicação Proxy em Diretório Ativo Azure](application-proxy-add-on-premises-application.md) 
- [Planear uma implementação do Proxy de Aplicações do Azure AD](application-proxy-deployment-plan.md) 
- [Acesso remoto a aplicações no local através do Azure Ative Directory Application Proxy](application-proxy.md)