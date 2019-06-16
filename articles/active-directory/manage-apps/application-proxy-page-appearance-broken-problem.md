---
title: Página de aplicativo não é apresentados corretamente para uma aplicação de Proxy de aplicações | Documentos da Microsoft
description: Orientações para quando a página não é apresentada corretamente num aplicativo de Proxy de aplicações tiver integrado com o Azure AD
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
ms.openlocfilehash: 99c2c53f3bf723c1d45edf2e62cd404615924c9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783725"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Página de aplicativo não é apresentados corretamente para uma aplicação de Proxy de aplicações

Este artigo ajuda-o a resolver problemas com aplicações de Proxy de aplicações do Azure Active Directory ao navegar para a página, mas algo na página não parece correto.

## <a name="overview"></a>Descrição geral
Quando publica uma aplicação de Proxy de aplicações, somente as páginas na sua raiz estão acessíveis quando o acesso à aplicação. Se a página não é apresentada corretamente, a URL interna de raiz utilizada para a aplicação pode estar em falta alguns recursos de página. Para resolver, certifique-se de que publica *todos os* os recursos para a página como parte da sua aplicação.

Pode verificar se a recursos em falta é o problema ao abrir o seu controlador de rede (como o Fiddler ou F12 ferramentas no Internet Explorer/Microsoft Edge), para carregar a página e procurando 404 erros. Que indica as páginas atualmente a não não possível localizar e que tem de publicá-los.

Como um exemplo de neste caso, suponha que publicou um aplicativo de despesas usando a URL interna `http://myapps/expenses`, mas a aplicação utiliza a folha de estilos `http://myapps/style.css`. Neste caso, a folha de estilo não é publicada na sua aplicação, para carregar a aplicação de despesas de lançar um erro 404 ao tentar carregar style. css. Neste exemplo, o problema é resolvido ao publicar a aplicação com um URL interno `http://myapp/`.

## <a name="problems-with-publishing-as-one-application"></a>Problemas com a publicação de como uma aplicação

Se não for possível publicar todos os recursos dentro do mesmo aplicativo, precisa publicar várias aplicações e ativar as ligações entre eles.

Para fazer isso, recomendamos que utilize o [domínios personalizados](application-proxy-configure-custom-domain.md) solução. No entanto, essa solução requer que o certificado for o proprietário para o seu domínio e as aplicações utilizam nomes de domínio completamente qualificado (FQDN). Para outras opções, consulte a [resolver problemas de ligações quebradas documentação](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Passos Seguintes
[Publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md)
