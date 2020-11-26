---
title: Configuração de aplicativoS Azure REST API - Autenticação
description: Páginas de referência para autenticação usando a API de Configuração de Aplicação Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183469"
---
# <a name="authentication"></a>Autenticação

Todos os pedidos HTTP devem ser autenticados. São apoiados os seguintes esquemas de autenticação.

## <a name="hmac"></a>HMAC

[A autenticação HMAC](./rest-api-authentication-hmac.md) utiliza um segredo gerado aleatoriamente para assinar cargas de pedido. Os detalhes sobre como os pedidos que utilizam este método de autenticação são autorizados podem ser encontrados na secção [de autorização HMAC.](./rest-api-authorization-hmac.md)

## <a name="azure-active-directory"></a>Azure Active Directory

[A autenticação do Azure Ative Directory (Azure AD)](../active-directory/authentication/overview-authentication.md) utiliza um token ao portador que é obtido a partir do Azure Ative Directory para autenticar pedidos. Os detalhes sobre como os pedidos que utilizam este método de autenticação são autorizados podem ser encontrados na secção de [autorização Azure AD.](./rest-api-authorization-azure-ad.md)