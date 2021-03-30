---
title: Pré-requisitos da API - Azure Marketplace
description: Pré-requisitos para a utilização das APIs do Portal do Parceiro cloud.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: 374cc3398d1037b19ceddcbdaafd333f643e44ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91261163"
---
# <a name="api-prerequisites"></a>Pré-requisitos da API

> [!NOTE]
> As APIs do Portal do Parceiro Cloud estão integradas e continuarão a trabalhar no Partner Center. A transição introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a transição para o Partner Center. Utilize apenas APIs de CPP para produtos já existentes que já estavam integrados antes da transição para o Partner Center; novos produtos devem utilizar APIs de submissão do Partner Center.

Precisa de dois ativos programáticos necessários para utilizar apis do Cloud Partner Portal: um diretor de serviço e um token de acesso Azure Ative Directory (Azure AD).

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>Criar diretor de serviço no inquilino do Azure Ative Directory

Primeiro, você precisa criar um diretor de serviço no seu inquilino AZure AD. Este inquilino será atribuído o seu próprio conjunto de permissões no Portal do Parceiro Cloud. O seu código vai chamar APIs usando este inquilino em vez das suas credenciais pessoais. Para obter uma explicação completa da criação de um principal de serviço, consulte [Como: Use o portal para criar uma aplicação AD AD Azure que possa aceder aos recursos.](../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="add-service-principal-to-your-account"></a>Adicione o principal do serviço à sua conta

Agora que criou o principal de serviço no seu inquilino, pode adicioná-lo como utilizador à sua conta Partner Center Portal. Tal como um utilizador, o titular do serviço pode ser proprietário ou colaborador do portal. Para mais detalhes, consulte **os próximos passos** abaixo.

## <a name="next-steps"></a>Passos seguintes

Consulte [aplicações AD manage Azure](partner-center-portal/manage-account.md#manage-azure-ad-applications).
