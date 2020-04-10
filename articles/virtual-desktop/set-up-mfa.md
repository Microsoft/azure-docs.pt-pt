---
title: Configurar a autenticação de vários fatores Azure para windows virtual desktop - Azure
description: Como configurar a autenticação multifactor Azure para uma maior segurança no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998480"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Configurar o Multi-Factor Authentication do Azure

O cliente Windows para Windows Virtual Desktop é uma excelente opção para integrar o Windows Virtual Desktop com a sua máquina local. No entanto, ao configurar a sua conta de Ambiente de Trabalho Virtual do Windows no Cliente Windows, existem certas medidas que terá de tomar para se manter a si e aos seus utilizadores seguros.

Quando faz o primeiro login, o cliente pede o seu nome de utilizador, senha e MFA Azure. Depois disso, da próxima vez que iniciar a sua inscrição, o cliente lembrar-se-á do seu símbolo da sua Aplicação Empresarial de Diretório Ativo Azure (AD). Quando selecionar **Lembre-se**de mim, os seus utilizadores podem iniciar sessão após reiniciar o cliente sem precisarem de reentrar nas suas credenciais.

Embora lembrar credenciais seja conveniente, também pode tornar as implementações em cenários da Enterprise ou dispositivos pessoais menos seguros. Para proteger os seus utilizadores, terá de se certificar de que o cliente continua a pedir credenciais de autenticação multi-factor (MFA) do Azure. Este artigo irá mostrar-lhe como configurar a política de acesso condicional para o Windows Virtual Desktop para ativar esta definição.

## <a name="prerequisites"></a>Pré-requisitos

Eis o que precisa de começar:

- Atribuir a todos os seus utilizadores uma das seguintes licenças:
  - Microsoft 365 E3 ou E5
  - Diretório Ativo Azure Premium P1 ou P2
  - Mobilidade Empresarial + Segurança E3 ou E5
- Um grupo de Diretório Ativo Azure com os seus utilizadores atribuídos como membros do grupo.
- Ative o Azure MFA para todos os seus utilizadores. Para obter mais informações sobre como fazê-lo, consulte [como exigir uma verificação em duas etapas para um utilizador](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

>[!NOTE]
>A seguinte definição também se aplica ao [cliente web do Windows Virtual Desktop](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="opt-in-to-the-conditional-access-policy"></a>Opte pela política de acesso condicional

1. Diretório **Ativo Open Azure.**

2. Vá ao separador **Todas as aplicações.** No menu "Tipo de aplicação", selecione **Aplicações Empresariais**e, em seguida, procure o **Windows Virtual Desktop Client**.

    ![Uma imagem do separador Todas as aplicações. O utilizador introduziu o "cliente de ambiente de trabalho virtual do Windows" na barra de pesquisa, e a aplicação apareceu nos resultados da pesquisa.](media/all-applications-search.png)

3. Selecione **Acesso Condicional**.

    ![Uma imagem mostrando o utilizador a pairar sobre o cursor de rato sobre o separador De Acesso Condicional.](media/conditional-access-location.png)

4. Selecione **+ Nova política**.

   ![Uma imagem da página de Acesso Condicional. O utilizador está a pairar sobre o novo botão de política.](media/new-policy-button.png)

5. Introduza um **nome** para a **regra**e, em seguida, **selecione** o nome *do **grupo** que criou nos pré-requisitos.

6. **Selecione,** selecione **Done**.

7. Em seguida, abra **aplicativos ou ações cloud.**

8. No painel **Select,** selecione a aplicação **Windows Virtual Desktop** Enterprise.

    ![Uma imagem das aplicações da Cloud ou página de ações. O utilizador selecionou a aplicação Windows Virtual Desktop selecionando a marca de verificação ao lado. A aplicação selecionada é realçada a vermelho.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >Deve também ver a aplicação Windows Virtual Desktop Client selecionada no lado esquerdo do ecrã, como mostra a imagem seguinte. Precisa tanto das aplicações Windows Virtual Desktop e Windows Virtual Desktop Client Enterprise para que a apólice funcione.
    >
    > ![Uma imagem das aplicações da Cloud ou página de ações. As aplicações Windows Virtual Desktop e Windows Virtual Desktop Client estão em destaque a vermelho.](media/cloud-apps-enterprise-selected.png)

9. Selecione **Selecione**

10. Em seguida, abra **Grant** 

11. Selecione **Exigir autenticação de vários fatores**e, em seguida, selecione **Exigir um dos comandos selecionados**.
   
    ![Uma imagem da página do Grant. "Exigir autenticação multi-factor" é selecionado.](media/grant-page.png)

    >[!NOTE]
    >Se tiver dispositivos inscritos em MDM na sua organização e não quiser que mostrem a solicitação do MFA, também pode selecionar o **dispositivo Exigir que seja marcado como conforme**.

12. Selecione **Sessão**.

13. Desloque a **frequência de início de sessão** para **Ativa**e, em seguida, mude o seu valor para **1 Horas**.

    ![Uma imagem da página da Sessão. O menu da sessão mostra que os menus de entrega de frequência sessão foram alterados para "1" e "Horas".](media/sign-in-frequency.png)
   
    >[!NOTE]
    >As sessões ativas no ambiente do Windows Virtual Desktop continuarão a funcionar à medida que altera a política. No entanto, se desligar ou assinar, terá de fornecer as suas credenciais novamente após 60 minutos. À medida que altera as definições, pode prolongar o período de tempo de paragem o quanto quiser (desde que se alinhe com a política de segurança da sua organização).
    >
    >A definição predefinida é uma janela rolante de 90 dias, o que significa que o cliente pedirá aos utilizadores que voltem a iniciar sessão quando tentarem aceder a um recurso depois de terem estado inativos na sua máquina durante 90 dias ou mais.

14. Ativar a política.

15. Selecione **Criar** para confirmar a apólice.

Está tudo concluído! Sinta-se à vontade para testar a política para garantir que a sua lista de autorizações funcione como pretendido.
