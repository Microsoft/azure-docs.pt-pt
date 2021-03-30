---
title: Definições de cookies
titleSuffix: Azure AD B2C
description: Fornece definições para os cookies utilizados no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e44e2e1eb37e808e60134a6fba5051552e84029
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85389348"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definições de cookies para Azure AD B2C

As seguintes secções fornecem informações sobre os cookies utilizados no Azure Ative Directory B2C (Azure AD B2C).

## <a name="samesite"></a>SameSite

O serviço Microsoft Azure AD B2C é compatível com as configurações do navegador SameSite, incluindo suporte `SameSite=None` para o `Secure` atributo.

Para salvaguardar o acesso a sites, os navegadores da Web introduzirão um novo modelo seguro por padrão que pressupõe que todos os cookies devem ser protegidos do acesso externo, salvo especificação em contrário. O navegador Chrome é o primeiro a implementar esta mudança, começando com [o Chrome 80 em fevereiro de 2020.](https://www.chromium.org/updates/same-site) Para obter mais informações sobre a preparação para a mudança no Chrome, consulte [Developers: Get Ready for New SameSite=None; Definições de cookies seguras](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) no Blog de Crómio.

Os desenvolvedores devem usar a nova configuração de `SameSite=None` cookies, para designar cookies para acesso cross-site. Quando o `SameSite=None` atributo está presente, deve ser utilizado um atributo adicional `Secure` para que os cookies de cross-site só possam ser acedidos sobre as ligações HTTPS. Valide e teste todas as suas aplicações, incluindo as aplicações que utilizam a Azure AD B2C.

Para obter mais informações, consulte:

* [Processar as alterações de cookies do SameSite no browser do Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Efeito nos websites dos clientes e nos serviços e produtos da Microsoft na versão 80 ou posterior do Chrome](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookies

A tabela que se segue lista os cookies utilizados no Azure AD B2C.

| Name | Domínio | Expiração | Objetivo |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Fim da [sessão](session-behavior.md) do navegador | Contém dados de adesão de utilizadores entre inquilinos. Os inquilinos um utilizador é membro e nível de adesão (Administrador ou Utilizador). |
| `x-ms-cpim-slice` | domínio b2clogin.com, login.microsoftonline.com e de marca | Fim da [sessão](session-behavior.md) do navegador | Utilizado para encaminhar os pedidos para a instância de produção apropriada. |
| `x-ms-cpim-trans` | domínio b2clogin.com, login.microsoftonline.com e de marca | Fim da [sessão](session-behavior.md) do navegador | Utilizado para rastrear as transações (número de pedidos de autenticação ao Azure AD B2C) e a transação em curso. |
| `x-ms-cpim-sso:{Id}` | domínio b2clogin.com, login.microsoftonline.com e de marca | Fim da [sessão](session-behavior.md) do navegador | Usado para manter a sessão SSO. |
| `x-ms-cpim-cache:{id}_n` | domínio b2clogin.com, login.microsoftonline.com e de marca | Fim da sessão do [navegador,](session-behavior.md)autenticação bem sucedida | Usado para manter o estado de pedido. |
| `x-ms-cpim-csrf` | domínio b2clogin.com, login.microsoftonline.com e de marca | Fim da [sessão](session-behavior.md) do navegador | Ficha de falsificação de pedidos de cross-site utilizada para proteção de CRSF. |
| `x-ms-cpim-dc` | domínio b2clogin.com, login.microsoftonline.com e de marca | Fim da [sessão](session-behavior.md) do navegador | Utilizado para o encaminhamento da rede Azure AD B2C. |
| `x-ms-cpim-ctx` | domínio b2clogin.com, login.microsoftonline.com e de marca | Fim da [sessão](session-behavior.md) do navegador | Contexto |
| `x-ms-cpim-rp` | domínio b2clogin.com, login.microsoftonline.com e de marca | Fim da [sessão](session-behavior.md) do navegador | Utilizado para armazenar dados de adesão para o inquilino do fornecedor de recursos. |
| `x-ms-cpim-rc` | domínio b2clogin.com, login.microsoftonline.com e de marca | Fim da [sessão](session-behavior.md) do navegador | Usado para armazenar o cookie de retransmissão. |
