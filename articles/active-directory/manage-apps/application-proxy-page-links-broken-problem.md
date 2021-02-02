---
title: Links na página não funcionam para uma aplicação de Procuração de Aplicação
description: Como resolver problemas com links quebrados nas aplicações application Proxy que integrou com Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a8ecb908063a40da32c1397fd4249811f9c2712
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254619"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Links na página não funcionam para uma aplicação de Procuração de Aplicação

Este artigo ajuda-o a resolver problemas por que as ligações na sua aplicação Azure Ative Directory Application Proxy não funcionam corretamente.

## <a name="overview"></a>Descrição Geral 
Após a publicação de uma aplicação Application Proxy, os únicos links que funcionam por padrão na aplicação são ligações a destinos contidos no URL raiz publicado. Os links dentro das aplicações não estão funcionando, o URL interno para a aplicação provavelmente não inclui todos os destinos de links dentro da aplicação.

**Porque é que isto acontece?** Ao clicar num link numa aplicação, o Application Proxy tenta resolver o URL como um URL interno dentro da mesma aplicação, ou como um URL disponível externamente. Se o link aponta para um URL interno que não está dentro da mesma aplicação, não pertence a nenhum destes baldes e resulta num erro não encontrado.

## <a name="ways-you-can-resolve-broken-links"></a>Formas de resolver ligações quebradas

Há três formas de resolver esta questão. As escolhas abaixo estão listadas em crescente complexidade.

1.  Certifique-se de que o URL interno é uma raiz que contém todos os links relevantes para a aplicação. Isto permite que todos os links sejam resolvidos como conteúdo publicado dentro da mesma aplicação.

    Se alterar o URL interno mas não quiser alterar a página de aterragem para os utilizadores, altere o URL da página inicial para o URL interno previamente publicado. Isto pode ser feito indo para "Azure Ative Directory" - &gt; Registos de Aplicações - &gt; selecione a aplicação - &gt; Branding. Na secção de marca, vê o campo "HOME Page URL", que pode ajustar para ser a página de aterragem desejada. Se ainda estiver a utilizar as antigas inscrições da App, o separador propriedades mostraria os detalhes do "URL de página inicial". 
    
    > [!IMPORTANT]
    > Para escoar as alterações acima, você precisa de direitos para modificar objetos de aplicação em Azure AD. O utilizador precisa de ser atribuído função [de Administrador de Aplicação](../roles/delegate-app-roles.md#assign-built-in-application-admin-roles) que concede direitos de modificação de aplicação em Azure AD ao utilizador.
    >

2.  Se as suas aplicações utilizarem nomes de domínio totalmente qualificados (FQDNs), utilize [domínios personalizados](application-proxy-configure-custom-domain.md) para publicar as suas aplicações. Esta funcionalidade permite que o mesmo URL seja utilizado tanto internamente como externamente.

    Esta opção garante que os links da sua aplicação são acessíveis externamente através do Application Proxy, uma vez que os links dentro da aplicação aos URLs internos também são reconhecidos externamente. Todos os links ainda precisam de pertencer a uma aplicação publicada. No entanto, com esta opção os links não necessitam de pertencer à mesma aplicação e podem pertencer a várias aplicações.

3.  Se nenhuma destas opções for viável, existem múltiplas opções para permitir a tradução de ligações inline. Estas opções incluem a utilização do Navegador Gerido Intune, a extensão das Minhas Apps ou a utilização da definição de tradução de link na sua aplicação. Para saber mais sobre cada uma destas opções e como as habilitar, consulte [redirecionar links codificados para aplicações publicadas com Azure AD Application Proxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Passos seguintes
[Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md)

