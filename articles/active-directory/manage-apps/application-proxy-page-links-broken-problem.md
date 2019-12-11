---
title: Os links na página não funcionam para um aplicativo de proxy de aplicativo
description: Como solucionar problemas com links desfeitos em aplicativos de proxy de aplicativo que você integrou ao Azure AD
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
ms.openlocfilehash: 268775d125e783b0c640e565c5f63e416cb197fd
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996957"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Os links na página não funcionam para um aplicativo de proxy de aplicativo

Este artigo ajuda você a solucionar problemas por que os links em seu aplicativo Proxy de Aplicativo do Azure Active Directory não funcionam corretamente.

## <a name="overview"></a>Visão geral 
Depois de publicar um aplicativo de proxy de aplicativo, os únicos links que funcionam por padrão no aplicativo são links para os destinos contidos na URL raiz publicada. Os links nos aplicativos não estão funcionando, a URL interna para o aplicativo provavelmente não inclui todos os destinos de links dentro do aplicativo.

**Por que isso acontece?** Ao clicar em um link em um aplicativo, o proxy de aplicativo tenta resolver a URL como uma URL interna dentro do mesmo aplicativo ou como uma URL disponível externamente. Se o link apontar para uma URL interna que não está dentro do mesmo aplicativo, ele não pertence a nenhum desses buckets e resulta em um erro não encontrado.

## <a name="ways-you-can-resolve-broken-links"></a>Maneiras de resolver links desfeitos

Há três maneiras de resolver esse problema. As opções a seguir estão listadas em complexidade crescente.

1.  Verifique se a URL interna é uma raiz que contém todos os links relevantes para o aplicativo. Isso permite que todos os links sejam resolvidos como conteúdo publicado no mesmo aplicativo.

    Se você alterar a URL interna, mas não quiser alterar a página de aterrissagem para os usuários, altere a URL da Home Page para a URL interna publicada anteriormente. Isso pode ser feito acessando "Azure Active Directory"-&gt; registros do aplicativo-&gt; selecione a identidade visual do&gt; de aplicativos. Na seção identidade visual, você vê o campo "URL da Home Page", que pode ser ajustado para ser a página de aterrissagem desejada. Se você ainda estiver usando a experiência de Registros de aplicativo herdada, a guia Propriedades mostrará os detalhes da "URL da Home Page". 
    
    > [!IMPORTANT]
    > Para fazer as alterações acima, você precisa de direitos para modificar objetos de aplicativo no Azure AD. O usuário precisa ser atribuído à função de "[administrador do aplicativo](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/roles-delegate-app-roles#assign-built-in-application-admin-roles)" que concede direitos de modificaion de aplicativo no Azure ad para o usuário. 
    >

2.  Se seus aplicativos usarem FQDNs (nomes de domínio totalmente qualificados), use [domínios personalizados](application-proxy-configure-custom-domain.md) para publicar seus aplicativos. Esse recurso permite que a mesma URL seja usada interna e externamente.

    Essa opção garante que os links em seu aplicativo sejam acessíveis externamente por meio do proxy de aplicativo, pois os links dentro do aplicativo para URLs internas também são reconhecidos externamente. Todos os links ainda precisam pertencer a um aplicativo publicado. No entanto, com essa opção, os links não precisam pertencer ao mesmo aplicativo e podem pertencer a vários aplicativos.

3.  Se nenhuma dessas opções for viável, haverá várias opções para habilitar a conversão de link embutido. Essas opções incluem o uso do Intune Managed Browser, a extensão My Apps ou o uso da configuração de conversão de link em seu aplicativo. Para saber mais sobre cada uma dessas opções e como habilitá-las, consulte [redirecionar links codificados para aplicativos publicados com o Azure proxy de aplicativo do AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Passos seguintes
[Trabalhar com servidores de proxy no local existentes](application-proxy-configure-connectors-with-proxy-servers.md)

