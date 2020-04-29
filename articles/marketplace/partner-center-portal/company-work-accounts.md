---
title: Contas de trabalho da empresa e Centro de Parceiros
description: Como verificar se a sua empresa tem uma conta de trabalho criada com a Microsoft, criar uma nova conta de trabalho ou criar várias contas de trabalho para usar com o Partner Center.
author: dsindona
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 3c0ee2f8d5291f7904435dea32d913adeaaf25c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262371"
---
# <a name="company-work-accounts-and-partner-center"></a>Contas de trabalho da empresa e Centro de Parceiros

O Partner Center utiliza contas de trabalho da empresa, também conhecidas como inquilinos do Azure Ative Directory (AD), para gerir o acesso à conta para vários utilizadores, permissões de controlo, grupos de anfitriões e aplicações e manter dados de perfil. Ao ligar o domínio da conta de e-mail de trabalho da sua empresa à sua conta Partner Center, os colaboradores da sua empresa podem inscrever-se no Partner Center para gerir as ofertas de marketplace utilizando os nomes de utilizador e senhas da sua própria conta de trabalho.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Verifique se a sua empresa já tem uma conta de trabalho

Se a sua empresa subscreveu um serviço de cloud da Microsoft, como o Azure, o Microsoft Intune ou o Office 365, então já tem um domínio de conta de e-mail de trabalho (também referido como um inquilino do Azure Ative Directory) que pode ser usado com partner Center.

Siga estes passos para verificar:
1. Inscreva-se no portal de https://portal.azure.comadministração Azure em .
2. Selecione **O Diretório Ativo Azure** a partir do menu de navegação à esquerda e, em seguida, selecione nomes de **domínio personalizados**.
3. Se já tiver uma conta de trabalho, o seu nome de domínio será listado.

Se a sua empresa ainda não tiver uma conta de trabalho, será criada uma para si durante o processo de inscrição do Partner Center.

## <a name="set-up-multiple-work-accounts"></a>Configurar várias contas de trabalho

Antes de decidir utilizar uma conta de trabalho existente, considere quantos utilizadores na conta de trabalho terão de aceder ao Partner Center. Se tiver utilizadores na conta de trabalho que não precisarão de aceder ao Partner Center, poderá considerar a criação de várias contas de trabalho, de modo que apenas os utilizadores que precisarão de aceder ao Partner Center estão representados numa determinada conta.

## <a name="create-a-new-work-account"></a>Criar uma nova conta de trabalho

Para criar uma nova conta de trabalho para a sua empresa, siga os passos abaixo. Poderá necessitar de solicitar assistência a quem tiver permissões administrativas na conta microsoft Azure da sua empresa.

1. Inscreva-se no [portal Microsoft Azure](https://portal.azure.com).
2. A partir do menu de navegação à esquerda, selecione os -> **Utilizadores**de **Diretório Ativo Azure**.
3. Selecione **Novo utilizador** e crie uma nova conta de trabalho Azure inserindo um nome e endereço de e-mail. Certifique-se de que a **função De Diretório** está definida para **o Utilizador** e selecione a caixa de verificação **de palavra-passe de série** na parte inferior para visualizar e tome nota da palavra-passe autogerada.
4. Selecione **Criar** para salvar o novo utilizador.

O endereço de e-mail da conta de utilizador deve ser um nome de domínio verificado no seu diretório. Pode listar todos os domínios verificados no seu diretório selecionando nomes de -> **domínio personalizados** de **Diretório Ativo Azure**no menu de navegação à esquerda.

Para saber mais sobre a adição de domínios personalizados no Diretório Ativo Azure, consulte [Adicionar ou associar um domínio em Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Sessão de email de trabalho de resolução de problemas

Se está com dificuldades em iniciar sessão na sua conta de trabalho (também conhecida como seu inquilino DaD Azure), encontre o cenário no diagrama abaixo que melhor corresponda à sua situação e siga os passos recomendados.

![Diagrama para resolução de problemas de conta de trabalho](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Passos seguintes

- [Gerencie a sua conta de mercado comercial no Partner Center](./manage-account.md) 
