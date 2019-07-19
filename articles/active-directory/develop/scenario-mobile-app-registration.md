---
title: Aplicativo móvel que chama as APIs da Web – configuração de código do aplicativo | Plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo móvel que chama APIs da Web (configuração de código do aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43ae9da51f68c9765a36d27c993d1c9935d61fa
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326128"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Aplicativo móvel que chama APIs Web-registro de aplicativo

Este artigo contém as instruções de registro do aplicativo para criar um aplicativo móvel.

## <a name="supported-account-types"></a>Tipos de conta suportados

Os tipos de conta com suporte em aplicativos móveis dependem da experiência que você deseja habilitar e dos usuários que seu aplicativo está direcionando.

## <a name="platform-configuration-and-redirect-uris"></a>Configuração de plataforma e URIs de redirecionamento  

Ao criar um aplicativo móvel, a etapa de registro mais crítica é o URI de redirecionamento. Isso pode ser definido por meio da [configuração de plataforma na folha autenticação](https://aka.ms/MobileAppReg).

Essa experiência permitirá que seu aplicativo obtenha SSO (logon único) por meio do Microsoft Authenticator (e Portal da Empresa do Intune no Android), bem como suporte a políticas de gerenciamento de dispositivos.

Se preferir configurar manualmente o URI de redirecionamento, você poderá fazer isso por meio do manifesto do aplicativo. O formato recomendado é o seguinte:

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - O hash de assinatura do Android pode ser gerado usando as chaves Release ou debug por meio do comando keytool.

## <a name="api-permissions"></a>Permissões de API

Os aplicativos móveis chamam APIs em nome do usuário conectado. Seu aplicativo precisa solicitar permissões delegadas, também chamadas de escopos. Dependendo da experiência desejada, isso pode ser feito estaticamente por meio do portal do Azure ou dinamicamente em tempo de execução. O registro estático das permissões permite que os administradores aprovem facilmente seu aplicativo e é recomendado.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Adquirindo um token](scenario-mobile-acquire-token.md)
