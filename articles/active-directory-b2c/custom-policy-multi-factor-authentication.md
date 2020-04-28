---
title: Autenticação multi-factor no Diretório Ativo Azure B2C [ Microsoft Docs
description: Como permitir a autenticação multi-factor em aplicações viradas para o consumidor garantidas pelo Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25724ba82e57c5e3800fa1a989dd4f504df1c163
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78189281"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Ativar a autenticação de vários fatores no Diretório Ativo Azure B2C

O Azure Ative Directory B2C (Azure AD B2C) integra-se diretamente com a [Autenticação Multi-Factor Azure](../active-directory/authentication/multi-factor-authentication.md) para que possa adicionar uma segunda camada de segurança às experiências de inscrição e inscrição nas suas aplicações. Permite a autenticação de vários fatores sem escrever uma única linha de código. Se já criou fluxos de utilizador de inscrição e de inscrição, ainda pode ativar a autenticação de vários fatores.

Esta funcionalidade ajuda as aplicações a lidar com cenários como o seguinte:

- Não é necessário autenticação multifactor para aceder a uma aplicação, mas exige-se que aceda a outra. Por exemplo, o cliente pode assinar um pedido de seguro automóvel com uma conta social ou local, mas deve verificar o número de telefone antes de aceder ao pedido de seguro de casa registado no mesmo diretório.
- Não é necessário autenticação multifactor para aceder a uma aplicação em geral, mas exige-se que aceda às porções sensíveis dentro dela. Por exemplo, o cliente pode iniciar sessão num pedido bancário com uma conta social ou local e verificar o saldo da conta, mas deve verificar o número de telefone antes de tentar uma transferência bancária.

## <a name="set-multi-factor-authentication"></a>Definir a autenticação de vários fatores

Quando cria um fluxo de utilizador, tem a opção de ativar a autenticação de vários fatores.

![Definir a autenticação de vários fatores](./media/custom-policy-multi-factor-authentication/add-policy.png)

Definir **a autenticação multifactor** para **ativar**.

Pode utilizar o **fluxo do utilizador run** para verificar a experiência. Confirme o seguinte cenário:

Uma conta de cliente é criada no seu inquilino antes que ocorra o passo de autenticação de vários fatores. Durante o passo, o cliente é solicitado a fornecer um número de telefone e verificar. Se a verificação for bem sucedida, o número de telefone é anexado à conta para posterior utilização. Mesmo que o cliente cancele ou despede,o cliente pode ser solicitado a verificar um número de telefone novamente durante o próximo check-in com a autenticação multifactor ativada.

## <a name="add-multi-factor-authentication"></a>Adicionar autenticação de vários fatores

É possível permitir a autenticação de vários fatores num fluxo de utilizador que criou anteriormente.

Para permitir a autenticação de vários fatores:

1. Abra o fluxo do utilizador e, em seguida, selecione **Propriedades**.
2. Ao lado da **autenticação multifactor,** selecione **Ativado**.
3. Clique em **Guardar** no início da página.


