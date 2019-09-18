---
title: Autenticação multifator no Azure Active Directory B2C | Microsoft Docs
description: Como habilitar a autenticação multifator em aplicativos voltados para o consumidor protegidos por Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0b872fcfbc2ead2cebdd32ff718b582c13af314e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065569"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Habilitar a autenticação multifator no Azure Active Directory B2C

O Azure Active Directory B2C (Azure AD B2C) integra-se diretamente com a [autenticação multifator do Azure](../active-directory/authentication/multi-factor-authentication.md) para que você possa adicionar uma segunda camada de segurança às experiências de inscrição e de entrada em seus aplicativos. Você habilita a autenticação multifator sem escrever uma única linha de código. Se você já tiver criado fluxos de usuário de inscrição e de entrada, ainda poderá habilitar a autenticação multifator.

Esse recurso ajuda os aplicativos a lidar com cenários como o seguinte:

- Você não requer a autenticação multifator para acessar um aplicativo, mas você precisa dele para acessar outro. Por exemplo, o cliente pode entrar em um aplicativo de seguro automaticamente com uma conta local ou social, mas deve verificar o número de telefone antes de acessar o aplicativo de seguro de residência registrado no mesmo diretório.
- Você não requer a autenticação multifator para acessar um aplicativo em geral, mas você precisa dele para acessar as partes confidenciais dentro dele. Por exemplo, o cliente pode entrar em um aplicativo bancário com uma conta local ou social e verificar o saldo da conta, mas deve verificar o número de telefone antes de tentar uma transferência de transmissão.

## <a name="set-multi-factor-authentication"></a>Definir autenticação multifator

Ao criar um fluxo de usuário, você tem a opção de habilitar a autenticação multifator.

![Definir autenticação multifator](./media/active-directory-b2c-reference-mfa/add-policy.png)

Defina a **autenticação multifator** como **habilitado**.

Você pode usar **executar fluxo de usuário** para verificar a experiência. Confirme o cenário a seguir:

Uma conta de cliente é criada em seu locatário antes que a etapa da autenticação multifator ocorra. Durante a etapa, o cliente será solicitado a fornecer um número de telefone e verificá-lo. Se a verificação for bem-sucedida, o número de telefone será anexado à conta para uso posterior. Mesmo que o cliente cancele ou descarte, o cliente pode ser solicitado a verificar um número de telefone novamente durante a próxima entrada com a autenticação multifator habilitada.

## <a name="add-multi-factor-authentication"></a>Adicionar autenticação multifator

É possível habilitar a autenticação multifator em um fluxo de usuário que você criou anteriormente.

Para habilitar a autenticação multifator:

1. Abra o fluxo de usuário e, em seguida, selecione **Propriedades**.
2. Ao lado de **autenticação multifator**, selecione **habilitado**.
3. Clique em **Guardar** no início da página.


