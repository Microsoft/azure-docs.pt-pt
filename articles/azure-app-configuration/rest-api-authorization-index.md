---
title: Configuração de aplicativoS Azure REST API - Autorização
description: Páginas de referência para autorização usando a API de Configuração de Aplicação Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 54f9cfab1f49837a3765c978de6deeb9e5e7d644
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424177"
---
# <a name="authorization"></a>Autorização

A autorização refere-se ao procedimento utilizado para determinar as permissões que um ouvinte tem ao fazer um pedido. Existem vários modelos de autorização. O modelo de autorização utilizado para um pedido depende do método [de autenticação](./rest-api-authentication-index.md) utilizado. Os modelos de autorização estão listados abaixo.

## <a name="hmac"></a>HMAC

O modelo [de modelo](./rest-api-authorization-hmac.md) de autorização associado à autenticação HMAC divide as permissões em apenas leitura ou leitura. Consulte a página [de autorização da HMAC](./rest-api-authorization-hmac.md) para mais detalhes.

## <a name="azure-active-directory"></a>Azure Active Directory

O [modelo de autorização](./rest-api-authorization-azure-ad.md) associado à autenticação Azure Ative Directory (Azure AD) utiliza o Azure RBAC para controlar permissões. Consulte a página de [autorização Azure AD](./rest-api-authorization-azure-ad.md) para obter mais detalhes.
