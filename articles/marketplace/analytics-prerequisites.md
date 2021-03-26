---
title: Pré-requisitos para aceder programaticamente a dados de análise
description: Conheça os requisitos que precisa de cumprir antes de poder aceder programáticamente aos dados de análise de marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 1cdd3dba8203ce9e8daeaa963f1722389d89d19d
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563825"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>Pré-requisitos para aceder programaticamente a dados de análise

Antes de poder aceder programáticamente aos dados de análise de marketplace comercial, tem de cumprir os seguintes requisitos.

## <a name="commercial-marketplace-enrollment"></a>Inscrição no mercado comercial

Para aceder programaticamente a dados de análise de marketplace comercial, precisa de estar inscrito no programa de marketplace comercial e ter uma conta Partner Center. Para saber como, consulte [Criar uma conta de mercado comercial no Partner Center.](./partner-center-portal/create-account.md)

## <a name="create-azure-active-directory-application"></a>Criar aplicação de Diretório Ativo Azure

As credenciais regulares dos utilizadores não podem ser utilizadas para o acesso programático de dados de análise de mercado comercial. Uma aplicação Azure Ative Directory (Azure AD) precisa de ser criada juntamente com um segredo para aceder às APIs analíticas. Para aprender a criar uma aplicação AD AZure e secreta, consulte [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](../active-directory/develop/quickstart-register-app.md).

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>Associar a candidatura da AD AZure ao inquilino do Centro De Parceiros

A aplicação AZure AD que criou no portal Azure tem de ser ligada à sua conta Partner Center. Os passos são os seguintes:

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard).
1. No canto superior direito, selecione o ícone de engrenagem e, em seguida, **selecione as definições de Conta**.
1. No menu **de definições de contas,** selecione **a gestão do Utilizador.**
1. Selecione **aplicações AD Azure** e, em seguida, selecione **+ Criar aplicação AD Azure**.
1. Selecione a aplicação AD AZure que criou no portal Azure e, em seguida, selecione **Next**.
1. Selecione a caixa de verificação **manager (Windows)** e, em seguida, selecione **Adicionar**.

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="Ilustra a página de aplicação Create AZure AD com as caixas de verificação para selecionar funções.":::

## <a name="generate-an-azure-ad-token"></a>Gerar um sinal AD AZure

Você precisa gerar um token AD Azure usando o ID de Aplicação (cliente). Este ID ajuda a identificar exclusivamente a sua aplicação de cliente na plataforma de identidade da Microsoft e o segredo do cliente do passo anterior. Para que os passos gerem um token AD Azure, consulte [o Serviço para atender chamadas usando credenciais de cliente (segredo ou certificado partilhado)](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md).

> [!NOTE]
> O sinal é válido por uma hora.

## <a name="next-steps"></a>Passos seguintes

- [Paradigma de acesso programático](analytics-programmatic-access.md)