---
title: Bibliotecas de clientes para cofre de chaves Azure Microsoft Docs
description: Bibliotecas de clientes para cofre de chaves Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 9cc8c1e9406f6b92a445cd52a4ad26e71d83ef5a
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88264092"
---
# <a name="client-libraries-for-azure-key-vault"></a>Bibliotecas de clientes para cofre de chaves Azure

As bibliotecas de clientes do Azure Key Vault permitem o acesso programático à funcionalidade Key Vault a partir de uma variedade de idiomas, incluindo .NET, Python, Java e Javascript.

## <a name="client-libraries-per-language-and-object"></a>Bibliotecas de clientes por idioma e objeto

Cada SDK tem bibliotecas de clientes separadas para segredos, chaves e certificados, de acordo com a tabela abaixo.

| Linguagem | Segredos | Chaves | Certificados |
|--|--|--|--|
| .NET | - [Referência API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [Pacote NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Arranque rápido](../secrets/quick-create-net.md) | - [Referência API](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [Pacote NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [Referência API](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [Pacote NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [Referência API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Pacote PyPi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Arranque rápido](../secrets/quick-create-python.md) |- [Referência API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Pacote PyPi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Arranque rápido](../keys/quick-create-python.md) | - [Referência API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Pacote PyPi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Arranque rápido](../certificates/quick-create-python.md) |
| Java | - [Referência API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Arranque rápido](../secrets/quick-create-java.md) |- [Referência API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [Referência API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [Referência API](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [pacote npm](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Arranque rápido](../secrets/quick-create-node.md) |- [Referência API](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [pacote npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [Referência API](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [pacote npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>Passos seguintes

- Consulte o guia de desenvolvedores do [Azure Key Vault](developers-guide.md)
- Leia mais sobre [identidade gerida para Azure Key Vault](managed-identity.md)