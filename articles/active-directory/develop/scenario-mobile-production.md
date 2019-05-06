---
title: Aplicação móvel que chamadas de APIs web (passar para produção) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação móvel que chama as APIs da Web (passar para produção)
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
ms.openlocfilehash: d37d2de561a6f5841bf17a47fef86ad7639750d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074954"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Aplicação móvel que chama o web APIs - mover para produção

Este artigo fornece detalhes sobre a melhorar a qualidade e a fiabilidade da sua aplicação para movê-lo para produção.

## <a name="handling-errors-in-mobile-applications"></a>Tratamento de erros em aplicativos móveis

Os fluxos diferentes que destacamos até agora, existem diversas condições de erro que pode ser causado. O cenário principal para processar é silenciosas falhas e de contingência para interação. Existem condições adicionais, que também deve considerar para produção, incluindo o não situações de rede, as falhas de serviço, admin é necessário consentimento e outros casos específicos de cenário.

Cada biblioteca MSAL tem conteúdo de código e wiki de exemplo que se aprofunda-se lidar com essas condições.

- [Wiki de Android de MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Mitigação e investigar problemas

Recolha de dados irá ajudar a sua aplicação diagnosticar problemas. Para obter mais detalhes sobre o tipo de dados pode recolher, consulte cada wiki de plataformas MSAL.

- Os utilizadores podem pedir ajuda quando se deparar com um problema. É uma prática recomendada capturar e armazenar temporariamente os registos e permitir que os utilizadores para carregá-los em algum lugar. A MSAL fornece extensões de registo para capturar informações detalhadas sobre a autenticação
- Se estiver disponível, ative a telemetria através de MSAL para recolher dados sobre a forma como os utilizadores estão a iniciar sessão na sua aplicação.

## <a name="testing-your-app"></a>Testar a aplicação

Não se esqueça de testar a aplicação com o [lista de verificação de integração](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
