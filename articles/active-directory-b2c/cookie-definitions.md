---
title: Definições de cookies
titleSuffix: Azure AD B2C
description: Fornece definições para os cookies usados no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 399b63cab2594610260997f8e5ecef9c3c05318f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712817"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definições de cookies para Azure AD B2C

As seguintes secções fornecem informações sobre os cookies utilizados no Azure Ative Directory B2C (Azure AD B2C).

## <a name="samesite"></a>Mesmo site

O serviço Microsoft Azure AD AD B2C é compatível com configurações de navegador sameSite, incluindo suporte para `SameSite=None` com o atributo `Secure`.

Para salvaguardar o acesso aos sites, os navegadores da Web introduzirão um novo modelo seguro por padrão que pressupõe que todos os cookies devem ser protegidos contra o acesso externo, salvo especificação em contrário. O navegador Chrome é o primeiro a implementar esta alteração, começando com o Chrome 80 em fevereiro de [2020.](https://www.chromium.org/updates/same-site) Para mais informações sobre a preparação para a mudança no Chrome, consulte [Developers: Prepare-se para novo SameSite=None; Fixe as definições](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) de cookies no Blog do Chromium.

Os desenvolvedores devem usar a nova definição de cookies, `SameSite=None`, para designar cookies para acesso ao cross-site. Quando o atributo `SameSite=None` estiver presente, deve ser utilizado um atributo adicional `Secure` para que os cookies transversais só possam ser acedidos sobre ligações HTTPS. Valide e teste todas as suas aplicações, incluindo as aplicações que utilizam o Azure AD B2C.

Para mais informações, consulte [effect em websites de clientes e serviços e produtos da Microsoft na versão 80 ou posterior](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)do Chrome .

## <a name="cookies"></a>Cookies

A tabela seguinte lista os cookies utilizados no Azure AD B2C.

| Nome | Domain | Expiração | Finalidade |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Fim da [sessão do navegador](session-behavior.md) | Mantém os dados de associação do usuário entre locatários. Os locatários de que um usuário é membro e nível de associação (administrador ou usuário). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão do navegador](session-behavior.md) | Usado para rotear solicitações para a instância de produção apropriada. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão do navegador](session-behavior.md) | Usado para acompanhar as transações (número de solicitações de autenticação para Azure AD B2C) e a transação atual. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão do navegador](session-behavior.md) | Usado para manter a sessão de SSO. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão do navegador](session-behavior.md), autenticação bem-sucedida | Usado para manter o estado da solicitação. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão do navegador](session-behavior.md) | Token de solicitação entre sites forjado usado para proteção CRSF. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão do navegador](session-behavior.md) | Usado para Azure AD B2C roteamento de rede. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão do navegador](session-behavior.md) | Contexto |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão do navegador](session-behavior.md) | Usado para armazenar dados de associação para o locatário do provedor de recursos. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão do navegador](session-behavior.md) | Usado para armazenar o cookie de retransmissão. |
