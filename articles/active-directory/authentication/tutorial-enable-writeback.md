---
title: Ativar repetição de escrita de palavras-passe do Azure AD
description: Neste tutorial, irá ativar a repetição de escrita de palavras-passe para obter alterações de palavra-passe de cloud iniciada para o AD no local como parte do Azure AD Connect.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: dabe0ad1a556ee43f3e6cae0e1cd421db5cde0fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60413965"
---
# <a name="tutorial-enabling-password-writeback"></a>Tutorial: Ativar a repetição de escrita de palavra-passe

Neste tutorial, irá ativar a repetição de escrita de palavras-passe para o seu ambiente híbrido. A repetição de escrita de palavras-passe é utilizada para sincronizar alterações de palavra-passe no Azure Active Directory (Azure AD) para o seu ambiente de Serviços de Domínio do Active Directory (AD DS) no local. A repetição de escrita de palavras-passe está ativada como parte do Azure AD Connect, para oferecer um mecanismo seguro para enviar as alterações de palavra-passe para um diretório local existente do Azure AD. Pode encontrar mais detalhes sobre os funcionamentos internos da repetição de escrita de palavras-passe no artigo [O que é a repetição de escrita de palavras-passe](concept-sspr-writeback.md).

> [!div class="checklist"]
> * Ativar a opção Repetição de escrita de palavras-passe no Azure AD Connect
> * Optar ativamente por participar na reposição personalizada de palavra-passe (SSPR) de repetição de escrita de palavras-passe

## <a name="prerequisites"></a>Pré-requisitos

* Aceder a um inquilino do Azure AD em funcionamento com, pelo menos, uma licença de avaliação atribuída.
* Uma conta com privilégios de Administrador Global no inquilino do Azure AD.
* Um servidor existente configurado com uma versão atual do [Azure AD Connect](../hybrid/how-to-connect-install-express.md).
* Os tutoriais de reposição personalizada de palavra-passe (SSPR) anteriores foram concluídos.

## <a name="enable-password-writeback-option-in-azure-ad-connect"></a>Ativar a opção Repetição de escrita de palavras-passe no Azure AD Connect

Para ativar a repetição de escrita de palavras-passe, primeiro iremos precisar de ativar a funcionalidade do servidor em que instalou o Azure AD Connect.

1. Para configurar e ativar a repetição de escrita de palavras-passe, inicie sessão no seu servidor do Azure AD Connect e inicie o assistente de configuração **Azure AD Connect**.
2. Na página de **Boas-vindas**, selecione **Configurar**.
3. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Seguinte**.
4. Na página **Ligar ao Azure AD**, introduza uma credencial de administrador global e, em seguida, selecione **Seguinte**.
5. Nas páginas de filtragem **Ligar diretórios** e **Domínio/UO**, selecione **Seguinte**.
6. Na página **Funcionalidades opcionais**, selecione a caixa junto a **Repetição de escrita de palavras-passe** e selecione **Seguinte**.
7. Na página **Pronto a configurar**, selecione **Configurar** e aguarde que o processo termine.
8. Quando vir a configuração a concluir, selecione **Sair**.

## <a name="enable-password-writeback-option-in-sspr"></a>Ativar a opção Repetição de escrita de palavras-passe no SSPR

Ativar a funcionalidade de repetição de escrita de palavras-passe no Azure AD Connect é apenas metade da história. Permitir que a SSPR utilize a repetição de escrita de palavras-passe encerra o ciclo, e permite que os utilizadores alterem ou reponham a palavra-passe para também ter essa palavra-passe definida no local.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador Global.
2. Navegue até o **Azure Active Directory**, clique em **Reposição de Palavra-passe** e, em seguida, escolha **Integração no local**.
3. Defina a opção **Repetir a escrita de palavras-passe no diretório no local** para **Sim**.
4. Defina a opção **Permitir que os utilizadores desbloqueiem as contas sem repor a palavra-passe** para **Sim**.
5. Clicar em **Guardar**

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ativou a repetição de escrita de palavras-passe para a reposição personalizada de palavra-passe. Deixe a janela do portal do Azure aberta e avance para o próximo tutorial para configurar definições adicionais relacionadas com a reposição personalizada de palavra-passe antes de implementar a solução num piloto.

> [!div class="nextstepaction"]
> [Ativar SSPR no ecrã de início de sessão do Windows](tutorial-sspr-windows.md)
