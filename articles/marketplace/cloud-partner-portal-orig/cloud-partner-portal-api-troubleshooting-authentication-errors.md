---
title: Resolução de problemas de erros de autenticação comuns | O Azure Marketplace
description: Fornece assistência com erros de autenticação comuns ao utilizar as APIs de Portal de parceiro de Cloud.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ddf3c9ce26a1538d91f1e6d6bcc04fd0d18e7936
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935809"
---
# <a name="troubleshooting-common-authentication-errors"></a>Resolução de problemas de erros de autenticação comuns

Este artigo fornece assistência com erros de autenticação comuns ao utilizar as APIs de Portal de parceiro de Cloud.

## <a name="unauthorized-error"></a>Erro não autorizado

Se obtiver consistentemente `401 unauthorized` erros, certifique-se de que tem um token de acesso válido.  Se ainda não o fez, crie uma aplicação básica do Azure Active Directory (Azure AD) e um principal de serviço, conforme descrito em [utilize o portal para criar um Azure Active Directory principal de aplicações e serviço que pode aceder a recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Em seguida, utilize a aplicação ou um pedido de HTTP POST simple para verificar o seu acesso.  Irá incluir o ID de inquilino, ID da aplicação, ID de objeto e a chave secreta para obter o token de acesso, conforme mostrado na imagem seguinte:

![Resolução de problemas do erro 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Erro proibido

Se obtiver um `403 forbidden` erro, certifique-se de que o principal de serviço correto foi adicionado à sua conta de editor no Portal de parceiros de nuvem.
Siga os passos a [pré-requisitos](./cloud-partner-portal-api-prerequisites.md) página para adicionar o seu principal de serviço para o portal.

Se o principal de serviço correto foi adicionado, em seguida, verifique se todas as outras informações. Preste muita atenção para o ID de objeto entrou no portal. Existem dois IDs de objeto na página de registo de aplicação do Azure Active Directory e tem de utilizar o ID de objeto local. Pode encontrar o valor correto ao aceder a **registos de aplicações** página para a sua aplicação e clicar no nome da aplicação em **aplicação gerida no diretório local**. Isto leva-o às propriedades da aplicação, onde é possível encontrar o ID de objeto correto no locais do **propriedades** página, conforme mostrado na figura a seguir. Além disso, certifique-se de que utiliza o ID de publicador correto ao adicionar o principal de serviço e fazer a chamada de API.

![Resolução de problemas do erro 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
