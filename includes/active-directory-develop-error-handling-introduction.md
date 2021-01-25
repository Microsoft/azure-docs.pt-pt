---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760670"
---
Este artigo apresenta uma visão geral dos diferentes tipos de erros e recomendações para lidar com erros comuns de inscrição.

## <a name="msal-error-handling-basics"></a>Básicos de manipulação de erros msal

As exceções na Microsoft Authentication Library (MSAL) destinam-se a que os desenvolvedores de aplicações resolvam problemas, e não para exibirem os utilizadores finais. As mensagens de exceção não são localizadas.

Ao processar exceções e erros, pode utilizar o próprio tipo de exceção e o código de erro para distinguir entre exceções.  Para obter uma lista de códigos de erro, consulte [códigos de autenticação ad Ad Azure](../articles/active-directory/develop/reference-aadsts-error-codes.md).

Durante a experiência de entrada, poderá encontrar erros sobre consentimentos, acesso condicional (MFA, Gestão de Dispositivos, restrições baseadas na localização), emissão e resgate de fichas e propriedades do utilizador.

A secção seguinte fornece mais detalhes sobre o tratamento de erros para a sua aplicação.