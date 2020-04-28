---
title: Links na página não funcionam para uma aplicação de procuração de aplicação
description: Como resolver problemas com links quebrados em aplicações de Proxy de aplicação que integrou com a AD Azure
services: active-directory
author: msmimart
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca782b5a190704911472d70c414d12afa7e558f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75430233"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Links na página não funcionam para uma aplicação de procuração de aplicação

Este artigo ajuda-o a resolver o porquê de as ligações na sua aplicação de procuração de pedido de diretório ativo Azure não funcionarem corretamente.

## <a name="overview"></a>Descrição geral 
Após a publicação de uma aplicação Proxy de aplicação, os únicos links que funcionam por padrão na aplicação são ligações a destinos contidos no URL raiz publicado. Os links dentro das aplicações não estão funcionando, o URL interno para a aplicação provavelmente não inclui todos os destinos de links dentro da aplicação.

**Porque é que isto acontece?** Ao clicar num link numa aplicação, o Application Proxy tenta resolver o URL como um URL interno dentro da mesma aplicação, ou como um URL externo disponível. Se o link apontar para um URL interno que não esteja dentro da mesma aplicação, não pertence a nenhum destes baldes e resulta num erro não encontrado.

## <a name="ways-you-can-resolve-broken-links"></a>Formas de resolver links quebrados

Há três formas de resolver esta questão. As escolhas abaixo estão listadas em crescente complexidade.

1.  Certifique-se de que o URL interno é uma raiz que contém todos os links relevantes para a aplicação. Isto permite que todos os links sejam resolvidos como conteúdo publicado dentro da mesma aplicação.

    Se alterar o URL interno mas não quiser alterar a página de aterragem para os utilizadores, altere o URL da página inicial para o URL interno previamente publicado. Isto pode ser feito indo para "Azure Ative Diretório" -&gt; Registos de Aplicações -&gt; selecione a aplicação -&gt; Branding. Na secção de marca, vê o campo "HOME Page URL", que pode ajustar para ser a página de aterragem desejada. Se ainda estiver a utilizar os registos da Aplicação do legado, o separador de propriedades mostraria os detalhes do "HOME Page URL". 
    
    > [!IMPORTANT]
    > Para efazer as alterações acima, é necessário alterar os objetos de aplicação em Azure AD. O utilizador deve ser atribuído ao utilizador a função de Administrador de [Aplicação](../users-groups-roles/roles-delegate-app-roles.md#assign-built-in-application-admin-roles) que concede direitos de modificação de aplicações em Azure AD ao utilizador.
    >

2.  Se as suas aplicações utilizarem nomes de domínio totalmente qualificados (FQDNs), utilize [domínios personalizados](application-proxy-configure-custom-domain.md) para publicar as suas aplicações. Esta função permite que o mesmo URL seja utilizado interna e externamente.

    Esta opção garante que os links da sua aplicação são acessíveis externamente através do Proxy de Aplicação, uma vez que os links dentro da aplicação aos URLs internos também são reconhecidos externamente. Todos os links ainda precisam pertencer a uma aplicação publicada. No entanto, com esta opção, os links não precisam de pertencer à mesma aplicação e podem pertencer a múltiplas aplicações.

3.  Se nenhuma destas opções for viável, existem múltiplas opções para permitir a tradução de ligação inline. Estas opções incluem a utilização do Navegador Gerido Intune, a extensão das Minhas Aplicações ou a utilização da definição de tradução de link na sua aplicação. Para saber mais sobre cada uma destas opções e como as permitir, consulte [redirecionar links codificados para aplicações publicadas com o Procurador de Aplicação AD Azure](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Passos seguintes
[Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md)

