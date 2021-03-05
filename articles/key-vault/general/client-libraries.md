---
title: Bibliotecas de clientes para cofre de chaves Azure | Microsoft Docs
description: Bibliotecas de clientes para cofre de chaves Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 2ae98c104296629be44af1385289829224407293
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216518"
---
# <a name="client-libraries-for-azure-key-vault"></a>Bibliotecas de clientes para cofre de chaves Azure

As bibliotecas de clientes do Azure Key Vault permitem o acesso programático à funcionalidade Key Vault a partir de uma variedade de idiomas, incluindo .NET, Python, Java e JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Bibliotecas de clientes por idioma e objeto

Cada SDK tem bibliotecas de clientes separadas para cofre chave, segredos, chaves e certificados, de acordo com a tabela abaixo.

| Linguagem | Segredos | Chaves | Certificados | Cofre-chave (avião de gestão) |
|--|--|--|--|--|
| .NET | - [Referência API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [Pacote NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Arranque rápido](../secrets/quick-create-net.md) | - [Referência API](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [Pacote NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [Arranque rápido](../keys/quick-create-net.md) | - [Referência API](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [Pacote NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [Arranque rápido](../certificates/quick-create-net.md) | - [Referência API](/dotnet/api/microsoft.azure.management.keyvault?view=azure-dotnet)<br>- [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [Referência API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Pacote PyPi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Arranque rápido](../secrets/quick-create-python.md) |- [Referência API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Pacote PyPi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Arranque rápido](../keys/quick-create-python.md) | - [Referência API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Pacote PyPi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Arranque rápido](../certificates/quick-create-python.md) | - [Referência API](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault?view=azure-python)<br> - [Pacote PyPi](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [Referência API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Arranque rápido](../secrets/quick-create-java.md) |- [Referência API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [Arranque rápido](../keys/quick-create-java.md) | - [Referência API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [Arranque rápido](../certificates/quick-create-java.md) |- [Referência API](/java/api/com.microsoft.azure.management.keyvault)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [Referência API](/javascript/api/@azure/keyvault-secrets/)<br>- [pacote npm](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Arranque rápido](../secrets/quick-create-node.md) |- [Referência API](/javascript/api/@azure/keyvault-keys/)<br>- [pacote npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [Arranque rápido](../keys/quick-create-node.md)| - [Referência API](/javascript/api/@azure/keyvault-certificates/)<br>- [pacote npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [Arranque rápido](../certificates/quick-create-node.md) |  - [Referência API](/javascript/api/@azure/arm-keyvault/)<br>- [pacote npm](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Passos seguintes

- Consulte o guia de desenvolvedores do [Azure Key Vault](developers-guide.md)
- Ler mais sobre [autenticação num cofre de chaves](authentication.md)
- Leia mais sobre [a garantia de acesso a um Cofre de Chaves](secure-your-key-vault.md)
