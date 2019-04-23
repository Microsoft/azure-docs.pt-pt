---
title: Definições de cookie - Azure Active Directory B2C | Documentos da Microsoft
description: Fornece definições para os cookies utilizados no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ac422a00a919903063c96ac096882036b99a63e3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786749"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Definições de cookies para o Azure Active Directory B2C

A tabela seguinte lista os cookies utilizados no Azure Active Directory B2C.

| Name | Domain | Expiração | Objetivo |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Fim da [sessão de browser](active-directory-b2c-token-session-sso.md) | Contém dados de associação do utilizador em inquilinos. Os inquilinos um utilizador é membro do e nível de associação (administrador ou utilizador). |
| x-ms-cpim-slice | login.microsoftonline.com, b2clogin.com, com marca de domínio | Fim da [sessão de browser](active-directory-b2c-token-session-sso.md) | Utilizado para encaminhar pedidos para a instância de produção adequados. |
| x-ms-cpim-trans | login.microsoftonline.com, b2clogin.com, com marca de domínio | Fim da [sessão de browser](active-directory-b2c-token-session-sso.md) | Utilizado para controlar as transações (número de pedidos de autenticação do Azure AD B2C) e a transação atual. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, com marca de domínio | Fim da [sessão de browser](active-directory-b2c-token-session-sso.md) | Utilizado para manter a sessão SSO. |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com, b2clogin.com, com marca de domínio | Fim da [sessão de browser](active-directory-b2c-token-session-sso.md), autenticação com êxito | Utilizado para manter o estado do pedido. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, com marca de domínio | Fim da [sessão de browser](active-directory-b2c-token-session-sso.md) | Token de falsificação de pedidos entre sites utilizado para a proteção de CRSF. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, com marca de domínio | Fim da [sessão de browser](active-directory-b2c-token-session-sso.md) | Utilizado para o encaminhamento de rede do Azure AD B2C. |
| x-ms-cpim-ctx | login.microsoftonline.com, b2clogin.com, com marca de domínio | Fim da [sessão de browser](active-directory-b2c-token-session-sso.md) | Contexto |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, com marca de domínio | Fim da [sessão de browser](active-directory-b2c-token-session-sso.md) | Utilizado para armazenar dados de associação para o inquilino do fornecedor de recursos. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, com marca de domínio | Fim da [sessão de browser](active-directory-b2c-token-session-sso.md) | Utilizado para armazenar o cookie de reencaminhamento. |

