---
title: App de extensões no Diretório Ativo Azure B2C [ Microsoft Docs
description: Restaurar a aplicação b2c-extensões.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 547b625996a65999c32c1b73699e3b408be01de3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188601"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: App de extensões

Quando um diretório Azure AD B2C `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` é criado, uma aplicação chamada é automaticamente criada dentro do novo diretório. Esta aplicação, referida como a **app b2c-extensions,** é visível nos registos da *App.* É utilizado pelo serviço Azure AD B2C para armazenar informações sobre utilizadores e atributos personalizados. Se a aplicação for eliminada, o Azure AD B2C não funcionará corretamente e o seu ambiente de produção será afetado.

> [!IMPORTANT]
> Não elimine a aplicação b2c-extensions a menos que esteja a planear apagar imediatamente o seu inquilino. Se a aplicação permanecer eliminada por mais de 30 dias, as informações do utilizador serão permanentemente perdidas.

## <a name="verifying-that-the-extensions-app-is-present"></a>Verificando se a aplicação de extensões está presente

Para verificar se a aplicação b2c-extensões está presente:

1. Dentro do seu inquilino Azure AD B2C, clique em **Todos os serviços** no menu de navegação à esquerda.
1. Procure e abra **as inscrições da App.**
1. Procure uma app que comece com **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Recuperar a app de extensões

Se apagou acidentalmente a aplicação b2c-extensions, tem 30 dias para recuperá-la. Pode restaurar a aplicação utilizando a API graph:

1. Navegue [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)para .
1. Inicie sessão no site como administrador global do diretório Azure AD B2C para o que pretende restaurar a aplicação eliminada. Este administrador global deve ter um endereço `username@{yourTenant}.onmicrosoft.com`de e-mail semelhante ao seguinte: .
1. Emita um HTTP `https://graph.windows.net/myorganization/deletedApplications` GET contra o URL com api-version=1.6. Esta operação listará todas as aplicações que foram eliminadas nos últimos 30 dias.
1. Encontre a aplicação na lista onde o nome começa com 'b2c-extension-app' e copie o seu `objectid` valor patrimonial.
1. Emita uma PUBLICAÇÃO `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`HTTP contra o URL . Substitua `{OBJECTID}` a parte do `objectid` URL pela parte do passo anterior.

Deverá agora poder [ver a aplicação restaurada](#verifying-that-the-extensions-app-is-present) no portal Azure.

> [!NOTE]
> Uma aplicação só pode ser restaurada se tiver sido eliminada nos últimos 30 dias. Se já passaram mais de 30 dias, os dados serão permanentemente perdidos. Para mais assistência, preencha um bilhete de apoio.
