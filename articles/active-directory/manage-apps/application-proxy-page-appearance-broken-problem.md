---
title: A página do aplicativo não é exibida corretamente para o aplicativo de proxy de aplicativo | Microsoft Docs
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
ms.openlocfilehash: 13e73f0ed56648ce162f00d6df5e7b86a922ca01
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381422"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Página de aplicativo não é apresentados corretamente para uma aplicação de Proxy de aplicações

Este artigo ajuda-o a resolver problemas com aplicações de Proxy de aplicações do Azure Active Directory ao navegar para a página, mas algo na página não parece correto.

## <a name="overview"></a>Descrição geral
Quando publica uma aplicação de Proxy de aplicações, somente as páginas na sua raiz estão acessíveis quando o acesso à aplicação. Se a página não é apresentada corretamente, a URL interna de raiz utilizada para a aplicação pode estar em falta alguns recursos de página. Para resolver, certifique-se de que publica *todos os* os recursos para a página como parte da sua aplicação.

Você pode verificar se os recursos ausentes são o problema abrindo o controlador de rede (como o Fiddler, ou as ferramentas F12 no Internet Explorer/Microsoft Edge), carregando a página e procurando por erros 404. Que indica as páginas atualmente a não não possível localizar e que tem de publicá-los.

Como um exemplo de neste caso, suponha que publicou um aplicativo de despesas usando a URL interna `http://myapps/expenses`, mas a aplicação utiliza a folha de estilos `http://myapps/style.css`. Neste caso, a folha de estilo não é publicada na sua aplicação, para carregar a aplicação de despesas de lançar um erro 404 ao tentar carregar style. css. Neste exemplo, o problema é resolvido ao publicar a aplicação com um URL interno `http://myapp/`.

## <a name="problems-with-publishing-as-one-application"></a>Problemas com a publicação de como uma aplicação

Se não for possível publicar todos os recursos dentro do mesmo aplicativo, precisa publicar várias aplicações e ativar as ligações entre eles.

Para fazer isso, recomendamos que utilize o [domínios personalizados](application-proxy-configure-custom-domain.md) solução. No entanto, essa solução requer que o certificado for o proprietário para o seu domínio e as aplicações utilizam nomes de domínio completamente qualificado (FQDN). Para outras opções, consulte a [resolver problemas de ligações quebradas documentação](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Passos Seguintes
[Publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md)
