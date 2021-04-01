---
title: App extensões em Azure Ative Directory B2C | Microsoft Docs
description: Restaurar a aplicação de extensões b2c.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc536fa4292d794e8d89a2564ad10a3c10dd0a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94560865"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: App de extensões

Quando um diretório AD B2C AD É criado, uma aplicação chamada `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` é criada automaticamente dentro do novo diretório. Esta aplicação, designada por **app de extensões b2c,** está visível nos *registos da App.* É utilizado pelo serviço Azure AD B2C para armazenar informações sobre utilizadores e atributos personalizados. Se for eliminada, o AAD B2C não funcionará corretamente e o ambiente de produção será afetado.

> [!IMPORTANT]
> Não elimine a aplicação de extensões b2c a não ser que esteja a planear eliminar imediatamente o seu inquilino. Se a aplicação permanecer eliminada por mais de 30 dias, as informações do utilizador serão permanentemente perdidas.

## <a name="verifying-that-the-extensions-app-is-present"></a>Verificação de que a app de extensões está presente

Para verificar se a aplicação de extensões b2c está presente:

1. Dentro do seu inquilino Azure AD B2C, clique em **Todos os serviços** no menu de navegação à esquerda.
1. Procure e abra **as inscrições da App.**
1. Procure uma app que comece com **a app de extensões B2c**

## <a name="recover-the-extensions-app"></a>Recuperar a app de extensões

Se acidentalmente eliminar a aplicação de extensões b2c, tem 30 dias para a recuperar. Pode restaurar a aplicação utilizando a API do gráfico:

1. Navegue para [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Faça login no site como administrador global para o diretório Azure AD B2C para o que pretende restaurar a aplicação eliminada. Este administrador global deve ter um endereço de e-mail semelhante ao seguinte: `username@{yourTenant}.onmicrosoft.com` .
1. Emita um HTTP GET contra o URL `https://graph.windows.net/myorganization/deletedApplications` com versão api=1.6. Esta operação irá listar todas as aplicações que foram eliminadas nos últimos 30 dias.
1. Encontre a aplicação na lista onde o nome começa com 'b2c-extensions-app' e copie o seu `objectid` valor patrimonial.
1. Emita um POST HTTP contra o URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore` . Substitua a `{OBJECTID}` parte do URL pelo passo `objectid` anterior.

Deverá agora poder [ver a aplicação restaurada](#verifying-that-the-extensions-app-is-present) no portal Azure.

> [!NOTE]
> Um pedido só pode ser restaurado se tiver sido eliminado nos últimos 30 dias. Se já se passaram mais de 30 dias, os dados serão permanentemente perdidos. Para mais assistência, preencha um bilhete de apoio.
