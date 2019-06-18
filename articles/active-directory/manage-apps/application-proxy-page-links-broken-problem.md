---
title: Links na página não funcionam com uma aplicação de Proxy de aplicações | Documentos da Microsoft
description: Como resolver problemas com ligações quebradas em aplicações de Proxy de aplicações que tiver integrado com o Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8bb7326ed22217e56bdaf9a119529ba775b69a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783233"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Links na página não funcionam com uma aplicação de Proxy de aplicações

Este artigo ajuda-o a resolver o motivo pelo qual os links na sua aplicação de Proxy de aplicações do Azure Active Directory não funcionam corretamente.

## <a name="overview"></a>Descrição geral 
Depois de publicar uma aplicação de Proxy de aplicações, as ligações só que funcionam por predefinição no aplicativo são links para destinos contidos o URL de raiz publicados. As ligações dentro as aplicações não estão funcionando, o URL interno para o aplicativo provavelmente não inclui todos os destinos de ligações na aplicação.

**Por que isso acontece?** Quando clicar num link num aplicativo, o Proxy de aplicações tenta resolver o URL como um URL interno dentro do mesmo aplicativo ou como um URL disponível externamente. Se o link apontar para um URL interno que não está dentro do mesmo aplicativo, ele não pertencem a nenhum destes registos e resultar num erro não encontrado.

## <a name="ways-you-can-resolve-broken-links"></a>Formas que o possa resolver quebrada ligações

Existem três formas de resolver este problema. As opções abaixo no constam aumentar a complexidade.

1.  Certifique-se de que a URL interna é uma raiz que contém todas as ligações relevantes para a aplicação. Isso permite que todas as ligações que seja resolvido como conteúdo publicado dentro do mesmo aplicativo.

    Se alterar o URL interno, mas não deseja alterar a página de destino para os utilizadores, altere o URL da Home page para o URL interno publicado anteriormente. Isso pode ser feito ao aceder ao "Azure Active Directory" -&gt; registos de aplicações -&gt; selecione a aplicação -&gt; propriedades. Neste separador de propriedades, verá o campo "Home Page do URL", o que pode fazer para ser a página de destino pretendido.

2.  Se as aplicações utilizam nomes de domínio completamente qualificado (FQDN), utilize [domínios personalizados](application-proxy-configure-custom-domain.md) para publicar as suas aplicações. Esta funcionalidade permite que o mesmo URL a ser utilizado tanto internamente e externamente.

    Essa opção assegura que as ligações na sua aplicação estão acessíveis externamente por meio do Proxy de aplicações, uma vez que as ligações na aplicação para os URLs internos também são reconhecidas externamente. Todas as ligações ainda têm de pertencer a uma aplicação publicada. No entanto, com esta opção as ligações não têm de pertencer à mesma aplicação e podem pertencer a várias aplicações.

3.  Se nenhuma dessas opções são viáveis, existem várias opções para ativar a conversão de link inline. Estas opções incluem-se com o Intune Managed Browser, extensão as minhas aplicações, ou com a definição de tradução de ligação na sua aplicação. Para saber mais sobre cada uma destas opções e como para ativá-las, veja [redirecionar codificado links para as aplicações publicadas com o Proxy de aplicações do Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Passos Seguintes
[Trabalhar com servidores de proxy no local existentes](application-proxy-configure-connectors-with-proxy-servers.md)

