---
title: Definições de cookies
titleSuffix: Azure AD B2C
description: Fornece definições para os cookies utilizados no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189519"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definições de cookies para Azure AD B2C

As seguintes secções fornecem informações sobre os cookies utilizados no Azure Ative Directory B2C (Azure AD B2C).

## <a name="samesite"></a>Mesmo site

O serviço Microsoft Azure AD AD B2C é compatível `SameSite=None` com `Secure` as configurações do navegador SameSite, incluindo suporte para com o atributo.

Para salvaguardar o acesso aos sites, os navegadores da Web introduzirão um novo modelo seguro por padrão que pressupõe que todos os cookies devem ser protegidos contra o acesso externo, salvo especificação em contrário. O navegador Chrome é o primeiro a implementar esta alteração, começando com o Chrome 80 em fevereiro de [2020.](https://www.chromium.org/updates/same-site) Para mais informações sobre a preparação para a mudança no Chrome, consulte [Developers: Prepare-se para novo SameSite=None; Fixe as definições](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) de cookies no Blog do Chromium.

Os desenvolvedores devem usar `SameSite=None`a nova definição de cookies, para designar cookies para acesso cross-site. Quando `SameSite=None` o atributo estiver `Secure` presente, deve ser utilizado um atributo adicional para que os cookies transversais só possam ser acedidos sobre ligações HTTPS. Valide e teste todas as suas aplicações, incluindo as aplicações que utilizam o Azure AD B2C.

Para obter mais informações, consulte:

* [Processar as alterações de cookies do SameSite no browser do Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Efeito nos websites dos clientes e serviços e produtos da Microsoft na versão 80 do Chrome ou posterior](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookies

A tabela seguinte lista os cookies utilizados no Azure AD B2C.

| Nome | Domain | Expiração | Objetivo |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Fim da [sessão](session-behavior.md) do navegador | Detém dados de adesão ao utilizador entre inquilinos. Os inquilinos um utilizador é membro e nível de adesão (Administrador ou Utilizador). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão](session-behavior.md) do navegador | Utilizado para encaminhar pedidos para a instância de produção adequada. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão](session-behavior.md) do navegador | Utilizado para acompanhar as transações (número de pedidos de autenticação ao Azure AD B2C) e a transação atual. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão](session-behavior.md) do navegador | Usado para manter a sessão SSO. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da sessão do [navegador,](session-behavior.md)autenticação bem-sucedida | Usado para manter o estado de pedido. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão](session-behavior.md) do navegador | Ficha de falsificação de pedido transversal utilizada para a proteção do CRSF. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão](session-behavior.md) do navegador | Utilizado para o encaminhamento da rede Azure AD B2C. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão](session-behavior.md) do navegador | Contexto |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão](session-behavior.md) do navegador | Usado para armazenar dados de adesão para o inquilino fornecedor de recursos. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, domínio de marca | Fim da [sessão](session-behavior.md) do navegador | Usado para armazenar o cookie de retransmissão. |
