---
title: A página da aplicação não é apresentada corretamente para aplicação Proxy app [ Proxy] Microsoft Docs
description: Orientação quando a página não está a ser apresentada corretamente numa Aplicação de Procuração de Aplicações que integrou com a AD Azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13e73f0ed56648ce162f00d6df5e7b86a922ca01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381422"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Página de aplicação não é apresentada corretamente para uma aplicação proxy de aplicação

Este artigo ajuda-o a resolver problemas com aplicações de procuração de aplicação de diretório sinuoso ativo do Azure quando navega para a página, mas algo na página não parece correto.

## <a name="overview"></a>Descrição geral
Quando publica uma aplicação Proxy de aplicação, apenas as páginas sob a sua raiz são acessíveis ao aceder à aplicação. Se a página não estiver a ser apresentada corretamente, o URL interno da raiz utilizado para a aplicação pode estar a faltar alguns recursos de página. Para resolver, certifique-se de ter publicado *todos os* recursos para a página como parte da sua aplicação.

Pode verificar se os recursos em falta são o problema abrindo o seu localizador de rede (como ferramentas Fiddler, ou F12 no Internet Explorer/Microsoft Edge), carregando a página e procurando 404 erros. Isso indica que as páginas atualmente não podem ser encontradas e que é necessário publicá-las.

Como exemplo deste caso, assuma que publicou uma `http://myapps/expenses`aplicação de despesas `http://myapps/style.css`usando o URL interno, mas a aplicação usa a folha de estilos . Neste caso, a folha de estilos não é publicada na sua aplicação, por isso carregar a app de despesas lança um erro de 404 enquanto tenta carregar style.css. Neste exemplo, o problema resolve-se publicando `http://myapp/`a aplicação com um URL interno .

## <a name="problems-with-publishing-as-one-application"></a>Problemas com a publicação como uma aplicação

Se não for possível publicar todos os recursos dentro da mesma aplicação, é necessário publicar várias aplicações e ativar ligações entre eles.

Para tal, recomendamos a utilização da solução de [domínios personalizados.](application-proxy-configure-custom-domain.md) No entanto, esta solução requer que possua o certificado para o seu domínio e as suas aplicações utilizam nomes de domínio totalmente qualificados (FQDNs). Para outras opções, consulte a documentação de [links quebrados](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Passos seguintes
[Publicar aplicações com o Proxy da Aplicação do Azure AD](application-proxy-add-on-premises-application.md)
