---
title: Configurar a autenticação multi-factor Azure para o Windows Virtual Desktop - Azure
description: Como configurar a autenticação multi-factor Azure para uma maior segurança no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 16abe8d155a0d7d7f65c69e6305da62bd8813ea4
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85361154"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Ativar a autenticação multi-factor do Azure para o Windows Virtual Desktop

O cliente Windows para Windows Virtual Desktop é uma excelente opção para integrar o Windows Virtual Desktop com a sua máquina local. No entanto, ao configurar a sua conta virtual do Windows desktop no Cliente Windows, existem certas medidas que terá de tomar para se manter a si e aos seus utilizadores seguros.

Quando iniciar sedível, o cliente pede o seu nome de utilizador, palavra-passe e Azure MFA. Depois disso, da próxima vez que assinar, o cliente lembrar-se-á do seu token da sua Aplicação empresarial Azure Ative.Empresarial. Quando selecionar **Lembre-se de mim,** os seus utilizadores podem iniciar sômposição após reiniciar o cliente sem necessidade de reentrar nas suas credenciais.

Embora lembrar credenciais seja conveniente, também pode tornar as implementações em cenários da Enterprise ou dispositivos pessoais menos seguras. Para proteger os seus utilizadores, terá de se certificar de que o cliente continua a pedir credenciais de Autenticação Multi-Factor (MFA) do Azure. Este artigo irá mostrar-lhe como configurar a política de acesso condicional para o Windows Virtual Desktop para ativar esta definição.

## <a name="prerequisites"></a>Pré-requisitos

Eis o que precisa para começar:

- Atribua aos utilizadores uma licença que inclua O Azure Ative Directory Premium P1 ou P2.
- Um grupo de Diretório Ativo Azure com os seus utilizadores designados como membros do grupo.
- Ativar o Azure MFA para todos os seus utilizadores. Para obter mais informações sobre como fazê-lo, consulte [Como exigir uma verificação em duas etapas para um utilizador](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> A seguinte definição também se aplica ao [cliente web virtual do Windows Desktop.](https://rdweb.wvd.microsoft.com/webclient/index.html)

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

Esta secção irá mostrar-lhe como criar uma política de Acesso Condicional que requer autenticação de vários fatores ao ligar-se ao Windows Virtual Desktop.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
2. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Conditional Access**.
3. Selecione **Nova política**.
4. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
5. Em **Atribuições**, selecione **Utilizadores e grupos**.
   - Em **Incluir**, **selecione Selecione utilizadores e grupos**  >  **Utilizadores e grupos** > Escolha o grupo criado na fase de pré-requisitos.
   - Selecione **Done** (Concluído).
6. Em **aplicativos ou ações cloud**  >  **Inclua**, **selecione apps Select**.
   - Escolha **o Ambiente de Trabalho Virtual do Windows** (App ID 9cdead84-a844-4324-93f2-b2e6bb768d07), em seguida, **Selecione**, e depois **Feito**.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem da página de aplicações ou ações cloud. As aplicações do Windows Virtual Desktop e do Windows Virtual Desktop Client estão em destaque a vermelho.](media/cloud-apps-enterprise.png)

     >[!NOTE]
     >Para encontrar o ID da aplicação que pretende selecionar, vá a **Aplicações empresariais** e selecione **as Aplicações** microsoft a partir do menu suspenso do tipo de aplicação.

7. Sob **controlos de acesso**  >  **Grant**, selecione Grant **access**, **Require multi-factor authentication**, e, em seguida, **Selecione**.
8. In **Access controls**  >  **Session**, selecione a frequência de **inscrição,** descreva o valor para **1** e a unidade para **Horas**, e, em seguida, selecione **Select**.
9. Confirme as suas definições e defina **Ativar** a política para **on**.
10. **Selecione Criar** para ativar a sua política.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre as políticas de Acesso Condicional](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Saiba mais sobre o sinal do utilizador na frequência](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
