---
title: MFA e SSPR baseadas no risco com o Azure Identity Protection
description: Neste tutorial, vai ativar as integrações do Azure Identity Protection para o Multi-Factor Authentication e a reposição personalizada de palavra-passe, para reduzir o comportamento de risco.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35039dc05103ac6528f668fd76e1372ed7cc0708
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370564"
---
# <a name="tutorial-use-risk-events-to-trigger-multi-factor-authentication-and-password-changes"></a>Tutorial: Usar eventos de risco para o acionador multi-factor Authentication e alterações de palavra-passe

Neste tutorial, vai ativar funcionalidades do Azure Active Directory (Azure AD) Identity Protection, uma funcionalidade do Azure AD Premium P2 que é mais do que apenas uma ferramenta de monitorização e relatórios. Para proteger as identidades da sua organização, pode configurar políticas baseadas em risco que respondam automaticamente aos comportamentos de risco. Estas políticas podem bloquear ou iniciar imediatamente a remediação, incluindo a exigência de alteração da palavra-passe e a imposição do Multi-Factor Authentication.

As políticas do Azure AD Identity Protection podem ser utilizadas em acréscimo às políticas de acesso condicional existentes, como uma camada adicional de proteção. Os utilizadores poderão nunca acionar um comportamento de risco que requeira uma destas políticas mas, como administrador, sabe que eles estão protegidos.

Alguns itens que podem acionar um evento de risco incluem:

* Utilizadores com fuga de credenciais
* Inícios de sessão de endereços IP anónimos
* Deslocação impossível para localizações atípicas
* Inícios de sessão de dispositivos infetados
* Inícios de sessão de endereços IP com atividade suspeita
* Inícios de sessão de localizações desconhecidas

Podem ser encontradas mais informações sobre o Azure AD Identity Protection no artigo [O que é o Azure AD Identity Protection](../active-directory-identityprotection.md)

> [!div class="checklist"]
> * Permitir o registo na MFA do Azure
> * Permitir alterações de palavra-passe baseadas em risco
> * Ativar a Multi-Factor Authentication baseada em risco

## <a name="prerequisites"></a>Pré-requisitos

* Acesso a um inquilino de trabalho do Azure AD com, pelo menos, uma licença de avaliação do Azure AD Premium P2 atribuída.
* Uma conta com privilégios de Administrador Global no inquilino do Azure AD.
* Ter concluído os tutoriais anteriores de reposição personalizada de palavra-passe (SSPR) e Multi-Factor Authentication (MFA).

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>Ativar políticas baseadas em risco para SSPR e MFA

A ativação das políticas baseadas em risco é um processo simples. Os passos abaixo irão guiá-lo ao longo de um exemplo de configuração.

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>Permitir aos utilizadores registarem-se no Multi-Factor Authentication

O Azure AD Identity Protection inclui uma política predefinida que pode ajudá-lo para obter os seus utilizadores se registou para o multi-factor Authentication e identificar facilmente o estado do registo atual. A ativação desta política não começa a pedir aos utilizadores para executarem o Multi-Factor Authentication, mas irá pedir-lhes que façam o pré-registo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Clique em **Todos os serviços** e, em seguida, procure **Azure AD Identity Protection**.
1. Clique em **Registo na MFA**.
1. Defina Impor Política como **Ativado**.
   1. Definir esta política irá exigir que todos os utilizadores registem os métodos para preparar a utilização do Multi-Factor Authentication.
1. Clique em **Guardar**.

   ![Exigir que os utilizadores para se registar para MFA no início de sessão](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>Permitir alterações de palavra-passe baseadas em risco

A Microsoft trabalha com investigadores, entidades responsáveis pela aplicação da lei, várias equipas de segurança da Microsoft e outras origens fidedignas para localizar os pares de nome de utilizador e palavra-passe. Quando um destes pares corresponde a uma conta no seu ambiente, pode ser acionada uma alteração de palavra-passe baseada em risco, com a política seguinte.

1. Clique em Política de risco de utilizador.
1. Em **Condições**, selecione **Risco de utilizador** e, em seguida, escolha **Médio e acima**.
1. Clique em "Selecionar" e em "Concluído"
1. Em **Acesso**, escolha **Permitir acesso** e selecione **Exigir alteração da palavra-passe**.
1. Clique em "Selecionar"
1. Defina Impor Política como **Ativado**.
1. Clicar em **Guardar**

### <a name="enable-risk-based-multi-factor-authentication"></a>Ativar a Multi-Factor Authentication baseada em risco

A maioria dos utilizadores tem um comportamento normal que pode ser controlado. Quando fugirem a esta norma, pode ser arriscado permitir-lhes iniciar sessão. Pode querer bloquear esse utilizador ou talvez pedir-lhe que execute o Multi-Factor Authentication para provar que realmente é quem afirma ser. Para ativar uma política que exija a MFA quando é detetado um risco de início de sessão, ative a política seguinte.

1. Clique em Política de risco de início de sessão
1. Em **Condições**, selecione **Risco de utilizador** e, em seguida, escolha **Médio e acima**.
1. Clique em "Selecionar" e em "Concluído"
1. Em **Acesso**, escolha **Permitir acesso** e, em seguida, selecione **Exigir autenticação multifator**.
1. Clique em "Selecionar"
1. Defina Impor Política como **Ativado**.
1. Clicar em **Guardar**

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver concluído os testes e já não quiser ter as políticas baseadas em risco ativadas, volte a cada política que pretende desativar e defina **Impor Política** como **Desativado**.
