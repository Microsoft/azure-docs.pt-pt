---
title: Contas de trabalho da empresa e Partner Center
description: Como verificar se a sua empresa tem uma conta de trabalho criada com a Microsoft, criar uma nova conta de trabalho ou configurar várias contas de trabalho para utilizar com o Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 05/30/2019
ms.openlocfilehash: 5b4aadc506b2f4a251ccef5e9488066b609ba5a5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130532"
---
# <a name="company-work-accounts-and-partner-center"></a>Contas de trabalho da empresa e Partner Center

Partner Center utiliza contas de trabalho da empresa, também conhecidas como inquilinos do Azure Ative Directory (AD), para gerir o acesso à conta de vários utilizadores, permissões de controlo, grupos de anfitriões e aplicações, e manter dados de perfil. Ao associar o domínio da conta de e-mail de trabalho da sua empresa à sua conta Partner Center, os colaboradores da sua empresa podem inscrever-se no Partner Center para gerir ofertas de marketplace utilizando os nomes de utilizador e senhas de uso da sua própria conta de trabalho.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Verifique se a sua empresa já tem uma conta de trabalho

Se a sua empresa subscreveu um serviço de cloud da Microsoft como a Azure, Microsoft Intune ou Microsoft 365, então já tem um domínio de conta de e-mail de trabalho (também referido como um inquilino do Azure Ative Directory) que pode ser usado com Partner Center.

Siga estes passos para verificar:
1. Inscreva-se no portal de administração Azure em https://portal.azure.com .
2. Selecione **O Diretório Ativo Azure** a partir do menu de navegação à esquerda e, em seguida, selecione **nomes de domínio personalizados** .
3. Se já tiver uma conta de trabalho, o seu nome de domínio será listado.

Se a sua empresa ainda não tiver uma conta de trabalho, uma será criada para si durante o processo de inscrição do Partner Center.

## <a name="set-up-multiple-work-accounts"></a>Configurar várias contas de trabalho

Antes de decidir utilizar uma conta de trabalho existente, considere quantos utilizadores na conta de trabalho precisarão de aceder ao Partner Center. Se tiver utilizadores na conta de trabalho que não precisarão de aceder ao Partner Center, poderá considerar a criação de várias contas de trabalho, para que apenas os utilizadores que necessitem de aceder ao Partner Center estejam representados numa determinada conta.

## <a name="create-a-new-work-account"></a>Criar uma nova conta de trabalho

Para criar uma nova conta de trabalho para a sua empresa, siga os passos abaixo. Poderá ter de solicitar assistência a quem tiver permissões administrativas na conta Microsoft Azure da sua empresa.

1. Inicie sessão no [portal do Microsoft Azure](https://portal.azure.com).
2. A partir do menu de navegação à esquerda, selecione os Utilizadores **do Diretório Ativo Azure**  ->  **Users** .
3. Selecione **Novo utilizador** e crie uma nova conta de trabalho Azure introduzindo um nome e endereço de e-mail. Certifique-se de que a **função de Diretório** está definida para **Utilizador** e selecione a caixa de verificação **'Mostrar Palavra-passe'** na parte inferior para visualizar e tome nota da palavra-passe autogerada.
4. Selecione **Criar** para salvar o novo utilizador.

O endereço de e-mail para a conta de utilizador deve ser um nome de domínio verificado no seu diretório. Pode listar todos os domínios verificados no seu diretório selecionando nomes de domínio personalizado do **Azure Ative Directory**  ->  **Custom domain names** no menu de navegação à esquerda.

Para saber mais sobre a adição de domínios personalizados no Azure Ative Directory, consulte [Adicionar ou associar um domínio em Azure AD](../../active-directory/fundamentals/add-custom-domain.md).

## <a name="troubleshoot-work-email-sign-in"></a>Sessão de trabalho de resolução de problemas e-mail de e-mail

Se tiver dificuldade em inscrever-se na sua conta de trabalho (também conhecida como o seu inquilino Azure AD), encontre o cenário no diagrama abaixo que melhor corresponda à sua situação e siga os passos recomendados.

![Diagrama para resolução de problemas de sessão de trabalho de assinatura](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Passos seguintes

- [Gerir a sua conta de mercado comercial no Partner Center](./manage-account.md)