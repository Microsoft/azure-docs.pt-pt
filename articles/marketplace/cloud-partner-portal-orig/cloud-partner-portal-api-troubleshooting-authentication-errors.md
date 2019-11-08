---
title: Solucionando problemas de erros comuns de autenticação | Azure Marketplace
description: Fornece assistência com erros comuns de autenticação ao usar as APIs de Portal do Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 0990e9aedf17f6d4ad01e4911e47efd60001f3d7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827374"
---
# <a name="troubleshooting-common-authentication-errors"></a>Solucionando problemas de erros comuns de autenticação

Este artigo fornece assistência com erros comuns de autenticação ao usar as APIs de Portal do Cloud Partner.

## <a name="unauthorized-error"></a>Erro não autorizado

Se você receber erros de `401 unauthorized` consistentemente, verifique se você tem um token de acesso válido.  Se você ainda não tiver feito isso, crie um aplicativo Azure Active Directory básico (AD do Azure) e uma entidade de serviço conforme descrito em [usar o portal para criar um aplicativo Azure Active Directory e uma entidade de serviço que possa acessar recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Em seguida, use o aplicativo ou uma solicitação HTTP POST simples para verificar seu acesso.  Você incluirá a ID do locatário, a ID do aplicativo, a ID do objeto e a chave secreta para obter o token de acesso, conforme mostrado na imagem a seguir:

![Solucionando problemas do erro 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Erro proibido

Se você receber um erro de `403 forbidden`, verifique se a entidade de serviço correta foi adicionada à sua conta de editor na Portal do Cloud Partner.
Siga as etapas na página [pré-requisitos](./cloud-partner-portal-api-prerequisites.md) para adicionar a entidade de serviço ao Portal.

Se a entidade de serviço correta tiver sido adicionada, verifique todas as outras informações. Preste muita atenção à ID de objeto inserida no Portal. Há duas IDs de objeto na página de registro do aplicativo Azure Active Directory, e você deve usar a ID do objeto local. Você pode encontrar o valor correto acessando a página **registros de aplicativo** para seu aplicativo e clicando no nome do aplicativo em **aplicativo gerenciado no diretório local**. Isso leva você para as propriedades locais do aplicativo, onde você pode encontrar a ID de objeto correta na página **Propriedades** , conforme mostrado na figura a seguir. Além disso, certifique-se de usar a ID correta do editor ao adicionar a entidade de serviço e fazer a chamada à API.

![Solucionando problemas do erro 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
