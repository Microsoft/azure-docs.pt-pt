---
title: A página da aplicação não é apresentada corretamente para aplicação Proxy de aplicação | Microsoft Docs
description: Orientação quando a página não estiver a ser apresentada corretamente numa Aplicação Proxy de aplicação que integrou com Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99b09e7b15427eb33e1e85edd89f4c8a37c4a3eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99254659"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>A página de aplicação não apresenta corretamente para uma aplicação de Procuração de Aplicação

Este artigo ajuda-o a resolver problemas com aplicações de Proxy de aplicação de diretório ativo Azure quando navega para a página, mas algo na página não parece correto.

## <a name="overview"></a>Descrição geral
Quando publica uma aplicação Application Proxy, apenas as páginas sob a sua raiz estão acessíveis ao aceder à aplicação. Se a página não estiver a ser apresentada corretamente, o URL interno de raiz utilizado para a aplicação pode estar a perder alguns recursos de página. Para resolver, certifique-se de ter publicado *todos os* recursos para a página como parte da sua aplicação.

Pode verificar se falta recursos é o problema abrindo o seu localizador de rede (como fiddler, ou ferramentas F12 no Internet Explorer/Microsoft Edge), carregando a página e procurando 404 erros. Isto indica que as páginas atualmente não podem ser encontradas e que é necessário publicá-las.

Como exemplo deste caso, assuma que publicou uma aplicação de despesas usando o URL `http://myapps/expenses` interno, mas a aplicação utiliza a folha de estilo `http://myapps/style.css` . Neste caso, a folha de estilo não é publicada na sua aplicação, pelo que o carregamento da app de despesas lança um erro de 404 enquanto tenta carregar o estilo.css. Neste exemplo, o problema resolve-se publicando a aplicação com um URL `http://myapp/` interno.

## <a name="problems-with-publishing-as-one-application"></a>Problemas com a publicação como uma aplicação

Se não for possível publicar todos os recursos dentro da mesma aplicação, é necessário publicar várias aplicações e ativar ligações entre elas.

Para tal, recomendamos a utilização da solução [de domínios personalizados.](application-proxy-configure-custom-domain.md) No entanto, esta solução requer que possua o certificado para o seu domínio e as suas aplicações utilizem nomes de domínio totalmente qualificados (FQDNs). Para outras opções, consulte a [documentação de links quebrados .](application-proxy-page-links-broken-problem.md)

## <a name="next-steps"></a>Passos seguintes
[Publicar aplicações com o Proxy da Aplicação do Azure AD](application-proxy-add-on-premises-application.md)
