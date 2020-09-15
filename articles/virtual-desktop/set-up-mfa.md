---
title: Configurar a autenticação multi-factor Azure para o Windows Virtual Desktop - Azure
description: Como configurar a autenticação multi-factor Azure para uma maior segurança no Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e31693eafcf32de1460cfa5b74ae35ffd05b5a67
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089926"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Ativar o Multi-Factor Authentication do Microsoft Azure para o Windows Virtual Desktop

>[!IMPORTANT]
> Se estiver a visitar esta página a partir da documentação virtual do Windows Desktop (clássica), certifique-se de [que regressa à documentação virtual do Windows Desktop (clássico)](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) uma vez terminada.

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

Eis como criar uma política de acesso condicional que requer autenticação de vários fatores ao ligar-se ao Windows Virtual Desktop:

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
2. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Conditional Access**.
3. Selecione **Nova política**.
4. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
5. Em **Atribuições**, selecione **Utilizadores e grupos**.
6. Em **Incluir**, **selecione Selecione utilizadores e grupos**  >  **Utilizadores e grupos** > Escolha o grupo que criou na fase [de pré-requisitos.](#prerequisites)
7. Selecione **Concluído**.
8. Em **aplicativos ou ações cloud**  >  **Inclua**, **selecione apps Select**.
9. Selecione uma das seguintes aplicações com base na versão do Windows Virtual Desktop que está a utilizar.
   
   - Se estiver a utilizar o Windows Virtual Desktop (clássico), escolha estas aplicações:
       
       - **Windows Virtual Desktop** (App ID 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Cliente virtual do Windows Desktop** (App ID fa4345a4-a730-4230-84a8-7d9651b86739), que lhe permitirá definir políticas no cliente web
       
        Depois disso, avance para o passo 11.

   - Se estiver a utilizar o Windows Virtual Desktop, escolha esta aplicação em vez disso:
       
       -  **Windows Virtual Desktop** (App ID 9cdead84-a844-4324-93f2-b2e6bb768d07)
       
        Depois disso, vai para o passo 10.

   >[!IMPORTANT]
   > Não selecione a aplicação chamada Fornecedor de Gestor de Recursos Virtual Desktop Azure (50e95039-b200-4007-bc97-8d5790743a63). Esta aplicação é usada apenas para recuperar o feed do utilizador e não deve ter MFA.

10. Vá às aplicações do Cliente **condições,**  >  **Client apps**em seguida, selecione onde pretende aplicar a política para:
    
    - Selecione **Browser** se quiser que a política se aplique ao cliente web.
    - Selecione **aplicativos Móveis e clientes de desktop** se quiser aplicar a política a outros clientes.
    - Selecione ambas as caixas de verificação se quiser aplicar a apólice a todos os clientes.
   
    > [!div class="mx-imgBorder"]
    > ![Uma imagem da página de aplicativos do Cliente. O utilizador selecionou as aplicações móveis e a caixa de verificação dos clientes de ambiente de trabalho.](media/select-apply.png)

11. Uma vez selecionado a sua aplicação, escolha **Select**e, em seguida, selecione **Fazer**.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem da página de aplicações ou ações cloud. As aplicações do Windows Virtual Desktop e do Windows Virtual Desktop Client estão em destaque a vermelho.](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >Para encontrar o ID da aplicação que pretende selecionar, vá a **Aplicações empresariais** e selecione **as Aplicações** microsoft a partir do menu suspenso do tipo de aplicação.

12. Sob **controlos de acesso**  >  **Grant**, selecione Grant **access**, **Require multi-factor authentication**, e, em seguida, **Selecione**.
13. In **Access controls**  >  **Session**, selecione a frequência de **inscrição,** descreva o valor para **1** e a unidade para **Horas**, e, em seguida, selecione **Select**.
14. Confirme as suas definições e defina **Ativar** a política para **on**.
15. **Selecione Criar** para ativar a sua política.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre as políticas de Acesso Condicional](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Saiba mais sobre o sinal do utilizador na frequência](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
