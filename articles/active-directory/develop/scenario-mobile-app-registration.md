---
title: Aplicação móvel que chamadas de web APIs - configuração de código da aplicação | Plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação móvel que chamadas de web APIs (configuração de código da aplicação)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eac9d1dfce79ac4ad9d49a6cfe6b7dee7f6681a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075164"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Aplicação móvel que chamadas de web APIs - registo de aplicações

Este artigo contém as instruções de registo de aplicação para a criação de um aplicativo móvel.

## <a name="supported-account-types"></a>Tipos de conta suportados

Os tipos de conta suportados nas aplicações móveis dependem a experiência de que pretende ativar e os utilizadores que se destina a sua aplicação.

## <a name="platform-configuration-and-redirect-uris"></a>Configuração de plataforma e URIs de redirecionamento  

Ao criar uma aplicação móvel, o passo de registo mais crítico é o URI de redirecionamento. Isto pode ser definido através da [configuração de plataforma no painel da autenticação](https://aka.ms/MobileAppReg).

Esta experiência irá permitir que a aplicação obter início de sessão único (SSO) através do Microsoft Authenticator (e o Portal da empresa do Intune no Android), bem como suporte de políticas de gestão de dispositivos.

Se preferir configurar manualmente o URI de redirecionamento, pode fazê-lo por meio de manifesto do aplicativo. O formato recomendado é o seguinte:

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - O hash de assinatura de Android pode ser gerado usando as chaves de lançamento ou depuração através do comando da ferramenta de chave.

## <a name="api-permissions"></a>Permissões de API

Aplicativos móveis chamam APIs em nome do utilizador com sessão iniciada. A aplicação tem de pedir permissões delegadas, também conhecidas como âmbitos. Consoante a experiência desejada, isso pode ser feito estaticamente através do portal do Azure ou dinamicamente em tempo de execução. Registar estaticamente permissões permite aos administradores aprovar facilmente a sua aplicação e é recomendada.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Adquirir um token](scenario-mobile-acquire-token.md)
