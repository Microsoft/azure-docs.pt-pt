---
title: Contas corporativas corporativas e Partner Center
description: Como verificar se sua empresa tem uma conta corporativa configurada com a Microsoft, criar uma nova conta corporativa ou configurar várias contas corporativas para usar com o Partner Center.
author: ChJenk
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 4783d3ac8ef78929621179d87f9c8948f6dd079c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812004"
---
# <a name="company-work-accounts-and-partner-center"></a>Contas corporativas corporativas e Partner Center

O Partner Center usa contas corporativas corporativas, também conhecidas como locatários de Azure Active Directory (AD), para gerenciar o acesso à conta para vários usuários, permissões de controle, grupos de hosts e aplicativos e manter dados de perfil. Vinculando o domínio de conta de email de trabalho da sua empresa à sua conta do Partner Center, os funcionários de sua empresa podem entrar no Partner Center para gerenciar ofertas do Marketplace usando seus próprios nomes de domínio e senhas de conta corporativa.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Verifique se sua empresa já tem uma conta corporativa

Se sua empresa assinou um serviço de nuvem da Microsoft, como Azure, Microsoft Intune ou Office 365, você já tem um domínio de conta de email de trabalho (também conhecido como um locatário de Azure Active Directory) que pode ser usado com o Partner Center.

Siga estas etapas para verificar:
1. Entre no portal de administração do Azure em https://portal.azure.com.
2. Selecione **Azure Active Directory** no menu de navegação à esquerda e, em seguida, selecione **nomes de domínio personalizados**.
3. Se você já tiver uma conta corporativa, seu nome de domínio será listado.

Se sua empresa ainda não tiver uma conta corporativa, ela será criada para você durante o processo de registro do Partner Center.

## <a name="set-up-multiple-work-accounts"></a>Configurar várias contas corporativas

Antes de decidir usar uma conta de trabalho existente, considere quantos usuários na conta corporativa precisarão acessar o Partner Center. Se você tiver usuários na conta corporativa que não precisarão acessar o Partner Center, convém considerar a criação de várias contas corporativas para que somente os usuários que precisarem acessar o Partner Center sejam representados em uma conta específica.

## <a name="create-a-new-work-account"></a>Criar uma nova conta de trabalho

Para criar uma nova conta de trabalho para sua empresa, siga as etapas abaixo. Talvez seja necessário solicitar assistência de quem tem permissões administrativas na conta de Microsoft Azure da sua empresa.

1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).
2. No menu de navegação à esquerda, selecione o **Azure Active Directory** -> **usuários**.
3. Selecione **novo usuário** e crie uma nova conta de trabalho do Azure inserindo um nome e endereço de email. Verifique se a **função de diretório** está definida como **usuário** e marque a caixa de seleção **Mostrar senha** na parte inferior para exibir e anotar a senha gerada automaticamente.
4. Selecione **criar** para salvar o novo usuário.

O endereço de email para a conta de usuário deve ser um nome de domínio verificado em seu diretório. Você pode listar todos os domínios verificados em seu diretório selecionando **Azure Active Directory** -> **nomes de domínio personalizados** no menu de navegação esquerdo.

Para saber mais sobre como adicionar domínios personalizados no Azure Active Directory, consulte [Adicionar ou associar um domínio no Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Solucionar problemas de entrada de email de trabalho

Se você estiver tendo problemas para entrar em sua conta corporativa (também conhecida como seu locatário do Azure AD), encontre o cenário no diagrama abaixo que melhor corresponda à sua situação e siga as etapas recomendadas.

![Diagrama para solução de problemas de entrada de conta corporativa](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Passos seguintes

- [Gerenciar sua conta do Marketplace comercial no Partner Center](./manage-account.md) 
