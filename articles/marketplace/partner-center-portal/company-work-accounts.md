---
title: Contas de trabalho da empresa e o Centro de parceiros
description: Como verificar se a sua empresa tem uma conta profissional configurar com a Microsoft, criar uma nova conta de trabalho ou, configurar várias contas profissionais para utilizar com o Centro de parceiros.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 05/30/2019
ms.openlocfilehash: df8ab370e8874e07f8985ecfb3a772848a2e2b21
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806285"
---
# <a name="company-work-accounts-and-partner-center"></a>Contas de trabalho da empresa e o Centro de parceiros

Centro de parceiros utiliza contas de trabalho da empresa, também conhecido como os inquilinos do Azure Active Directory (AD), para gerir o acesso de conta para múltiplos utilizadores, permissões de controlo, grupos de anfitriões e aplicações e manter os dados de perfil. Vinculando o domínio de conta de e-mail de trabalho da sua empresa para a sua conta do Centro de parceiros, os funcionários da sua empresa podem iniciar sessão no Centro de parceiros para gerir os seus próprios nomes de utilizador de conta de trabalho e as palavras-passe a utilizar as ofertas do marketplace.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Verificar se a sua empresa já tem uma conta profissional

Se a sua empresa tiver subscrito para um serviço cloud da Microsoft, como o Azure, o Microsoft Intune ou o Office 365, já tem um trabalho conta domínio de e-mail (também referido como um inquilino do Azure Active Directory) que pode ser utilizado com o Centro de parceiros.

Siga estes passos para verificar:
1. Inicie sessão no portal do administrador do Azure em https://portal.azure.com.
2. Selecione **do Azure Active Directory** no menu de navegação à esquerda e em seguida, selecione **nomes de domínio personalizado**.
3. Se já tiver uma conta profissional, seu nome de domínio será listado.

Se a sua empresa já não tiver uma conta profissional, será criada para durante o processo de inscrição do Centro de parceiros.

## <a name="set-up-multiple-work-accounts"></a>Configurar várias contas profissionais

Antes de decidir utilizar uma conta de trabalho existente, considere o número de utilizadores na conta profissional, terá de aceder ao centro de parceiros. Se tiver usuários na conta profissional que não precisa de aceder ao centro de parceiros, convém considerar a criação de várias contas de trabalho, para que apenas os utilizadores que terão de aceder ao centro de parceiros são representados numa determinada conta.

## <a name="create-a-new-work-account"></a>Criar uma nova conta de trabalho

Para criar uma nova conta de trabalho para a sua empresa, siga os passos abaixo. Terá de solicitar assistência a partir de quem tem permissões administrativas na conta do Microsoft Azure da sua empresa.

1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).
2. A partir do menu de navegação esquerdo, selecione o **do Azure Active Directory** -> **utilizadores**.
3. Selecione **novo utilizador** e criar uma nova conta de trabalho do Azure ao introduzir um nome e endereço de e-mail. Certifique-se de que o **função de diretório** está definida como **utilizador** e selecione o **mostrar palavra-passe** caixa de verificação na parte inferior para ver e tome nota da senha geradas automaticamente.
4. Selecione **criar** para guardar o novo utilizador.

O endereço de e-mail da conta de utilizador tem de ser um nome de domínio verificado no seu diretório. Pode listar todos os domínios verificados no seu diretório, selecionando **do Azure Active Directory** -> **nomes de domínio personalizado** no menu de navegação à esquerda.

Para saber mais sobre como adicionar domínios personalizados no Azure Active Directory, veja [adicionar ou associar um domínio no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Resolver problemas de início de sessão-no e-mail de trabalho

Se estiver a ter problemas ao iniciar sessão na sua conta profissional (também conhecido como inquilino do Azure AD), localize que o cenário no diagrama abaixo que melhor corresponde à sua situação e siga os passos recomendados.

![Diagrama para início de sessão-na conta de trabalho de resolução de problemas](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Passos Seguintes

- [Gerir a sua conta do Marketplace comercial no Centro de parceiros](./manage-account.md) 
