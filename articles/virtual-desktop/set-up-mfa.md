---
title: Configurar a autenticação de vários fatores Azure para windows virtual desktop - Azure
description: Como configurar a autenticação de multi-factors Azure para maior segurança no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 069d2a153e307ed94032ce1d980f26521969fc56
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508348"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Ativar a autenticação de vários fatores azure para o windows virtual desktop

O cliente Windows para Windows Virtual Desktop é uma excelente opção para integrar o Windows Virtual Desktop com a sua máquina local. No entanto, ao configurar a sua conta de Ambiente de Trabalho Virtual do Windows no Cliente Windows, existem certas medidas que terá de tomar para se manter a si e aos seus utilizadores seguros.

Quando faz o primeiro login, o cliente pede o seu nome de utilizador, senha e MFA Azure. Depois disso, da próxima vez que iniciar a sua inscrição, o cliente lembrar-se-á do seu símbolo da sua Aplicação Empresarial de Diretório Ativo Azure (AD). Quando selecionar **Lembre-se**de mim, os seus utilizadores podem iniciar sessão após reiniciar o cliente sem precisarem de reentrar nas suas credenciais.

Embora lembrar credenciais seja conveniente, também pode tornar as implementações em cenários da Enterprise ou dispositivos pessoais menos seguros. Para proteger os seus utilizadores, terá de se certificar de que o cliente continua a pedir credenciais de autenticação multi-factor (MFA) do Azure. Este artigo irá mostrar-lhe como configurar a política de acesso condicional para o Windows Virtual Desktop para ativar esta definição.

## <a name="prerequisites"></a>Pré-requisitos

Eis o que precisa de começar:

- Atribuir aos utilizadores uma licença que inclua o Azure Ative Directory Premium P1 ou P2.
- Um grupo de Diretório Ativo Azure com os seus utilizadores atribuídos como membros do grupo.
- Ative o Azure MFA para todos os seus utilizadores. Para obter mais informações sobre como fazê-lo, consulte [como exigir uma verificação em duas etapas para um utilizador](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> A seguinte definição também se aplica ao [cliente web do Windows Virtual Desktop](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

Esta secção irá mostrar-lhe como criar uma política de Acesso Condicional que requer a autenticação de vários fatores ao ligar-se ao Windows Virtual Desktop.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até ao**Acesso Condicional**de**Segurança** >  **do Diretório** > Ativo do Azure.
1. Selecione **Nova política.**
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**.
   1. Em **Incluir**, **selecione Utilizadores e grupos Selecione** > utilizadores**e grupos** > Escolha o grupo criado na fase de pré-requisitos.
   1. Selecione **Done** (Concluído).
1. Em **aplicativos ou ações** > cloud**Inclua**, **selecione selecione aplicações**.
   1. Escolha **o Windows Virtual Desktop** e o Windows Virtual Desktop **Client**, e selecione **Selecione** e depois **feito**.
   ![Uma imagem das aplicações da Cloud ou página de ações. As aplicações Windows Virtual Desktop e Windows Virtual Desktop Client estão em destaque a vermelho.](media/cloud-apps-enterprise-selected.png)
1. Sob **controlos** > de**acesso,** selecione **acesso ao grant**, exija a **autenticação de vários fatores,** e depois **selecione**.
1. Sob **controlos** > de acesso**Sessão**, selecione **frequência de início de acesso,** detete o valor para **1** e a unidade para **Horas**, e depois **selecione**.
1. Confirme as suas definições e ajuste **a política de ativação** para **On**.
1. Selecione **Criar** para ativar a sua política.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre as políticas de Acesso Condicional](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Saiba mais sobre o sinal do utilizador na frequência](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
