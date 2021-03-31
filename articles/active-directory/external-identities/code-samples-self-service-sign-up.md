---
title: Amostras de código de conector API para fluxos de utilizador - Azure AD
description: Amostras de código para conectores API em fluxos de inscrição de autosserviço para identidades externas do Diretório Ativo Azure.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3995395cfd6256689bedc7a4a3c83effc65c0b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87905947"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>Amostras para inscrição de autosserviço de identidades externas

As tabelas que se seguem fornecem links para amostras de código para alavancar as APIs web nos fluxos de utilizador de inscrição de autosserviço utilizando [conectores API](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Inícios rápidos de função api azure

| Sample                                                                                                                          | Descrição                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Esta amostra de Função .NET Core Azure demonstra como limitar as inscrições a domínios específicos do inquilino e validar informações fornecidas pelo utilizador. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Esta amostra Node.js Azure Function demonstra como limitar as inscrições a domínios específicos do inquilino e validar informações fornecidas pelo utilizador.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Esta amostra python Azure Function demonstra como limitar as inscrições a domínios específicos do inquilino e validar as informações fornecidas pelo utilizador.    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>Fluxos de trabalho de aprovação personalizadas

| Sample | Descrição |
|--------| ----------- |
| [Fluxo de trabalho de aprovação manual](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | Esta amostra demonstra um fluxo de trabalho de aprovação de ponta a ponta para gerir a criação de conta de utilizador convidado em inscrição de self-service |

## <a name="identity-verification"></a>Verificação de identidade

| Sample                                                                                                            | Descrição                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Esta amostra mostra como verificar a identidade do utilizador como parte da sua inscrição de self-service utilizando um conector API para integrar com iDologia. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | Esta amostra mostra como verificar a identidade do utilizador como parte da sua inscrição de autosserviço utilizando um conector API para integrar com a Experian. |
