---
title: Autenticação multi-factor no Azure Ative Directy B2C Microsoft Docs
description: Como ativar a autenticação multi-factor em aplicações viradas para o consumidor protegidas pelo Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 69096e5f650a131c5af7ec4da60b7cbca225a56f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87116601"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Ativar a autenticação de vários fatores no Azure Ative Directory B2C

O Azure Ative Directory B2C (Azure AD B2C) integra-se diretamente com [a Autenticação Multi-Factor Azure](../active-directory/authentication/multi-factor-authentication.md) para que possa adicionar uma segunda camada de segurança às experiências de inscrição e inscrição nas suas aplicações. Ativa a autenticação de vários fatores sem escrever uma única linha de código. Se já criou os fluxos de entrada e de inscrição do utilizador, ainda pode ativar a autenticação de vários fatores.

Esta funcionalidade ajuda as aplicações a lidar com cenários como:

- Não é necessário autenticação multi-factor para aceder a uma aplicação, mas precisa que aceda a outra. Por exemplo, o cliente pode assinar um pedido de seguro automóvel com uma conta social ou local, mas deve verificar o número de telefone antes de aceder ao pedido de seguro de casa registado no mesmo diretório.
- Não é necessário a autenticação de vários fatores para aceder a uma aplicação em geral, mas exige que aceda às partes sensíveis dentro dela. Por exemplo, o cliente pode iniciar scontabilidade bancária com uma conta social ou local e verificar o saldo da conta, mas deve verificar o número de telefone antes de tentar uma transferência bancária.

## <a name="set-multi-factor-authentication"></a>Definir a autenticação de vários fatores

Quando cria um fluxo de utilizador, tem a opção de ativar a autenticação de vários fatores.

![Definir a autenticação de vários fatores](./media/custom-policy-multi-factor-authentication/add-policy.png)

Definir **a autenticação multifactor** para **ativar**.

Pode utilizar **o fluxo do utilizador executar** para verificar a experiência. Confirme o seguinte cenário:

Uma conta de cliente é criada no seu inquilino antes do passo de autenticação multi-factor. Durante o passo, o cliente é solicitado a fornecer um número de telefone e verificar. Se a verificação for bem sucedida, o número de telefone é anexado à conta para posterior utilização. Mesmo que o cliente cancele ou desa caia, o cliente pode ser solicitado a verificar um número de telefone novamente durante a próxima entrada com autenticação multi-factor ativada.

## <a name="add-multi-factor-authentication"></a>Adicionar autenticação multi-factor

É possível ativar a autenticação de vários fatores num fluxo de utilizador que criou anteriormente.

Para permitir a autenticação de vários fatores:

1. Abra o fluxo do utilizador e, em seguida, **selecione Propriedades**.
2. Junto à **autenticação multifactor,** selecione **Ativado**.
3. Clique em **Guardar** no início da página.


