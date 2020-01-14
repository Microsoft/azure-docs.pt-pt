---
title: Definições de cookie-Azure Active Directory B2C | Microsoft Docs
description: Fornece definições para os cookies usados no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66de4559ed006735f53ff993cce29370428b9998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930897"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Definições de cookies para Azure Active Directory B2C

A tabela a seguir lista os cookies usados no Azure Active Directory B2C.

| Nome | Domain | Expiração | Finalidade |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Fim da [sessão do navegador](session-behavior.md) | Mantém os dados de associação do usuário entre locatários. Os locatários de que um usuário é membro e nível de associação (administrador ou usuário). |
| x-ms-cpim-slice | login.microsoftonline.com, b2clogin.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Usado para rotear solicitações para a instância de produção apropriada. |
| x-ms-cpim-trans | login.microsoftonline.com, b2clogin.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Usado para acompanhar as transações (número de solicitações de autenticação para Azure AD B2C) e a transação atual. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Usado para manter a sessão de SSO. |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com, b2clogin.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md), autenticação bem-sucedida | Usado para manter o estado da solicitação. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Token de solicitação entre sites forjado usado para proteção CRSF. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Usado para Azure AD B2C roteamento de rede. |
| x-ms-cpim-ctx | login.microsoftonline.com, b2clogin.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Contexto |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Usado para armazenar dados de associação para o locatário do provedor de recursos. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Usado para armazenar o cookie de retransmissão. |
