---
title: Ativar o writeback de password do Azure Ative Directory
description: Neste tutorial, aprende-se a ativar a redefinição da palavra-passe de autosserviço Azure AD utilizando o Azure AD Connect para sincronizar as alterações para um ambiente de Serviços de Domínio de Diretório Ativo no local.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: fc5291544f1cd64caa7e4ab1dd7d541604706920
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741172"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Tutorial: Permitir que a palavra-passe de autosserviço do Azure Ative Directory reponha a gravação para um ambiente no local

Com o Azure Ative Directory (Azure AD) a redefinir a palavra-passe de autosserviço (SSPR), os utilizadores podem atualizar a sua palavra-passe ou desbloquear a sua conta através de um navegador web. Num ambiente híbrido onde o Azure AD está ligado a um ambiente no local Ative Directory Domain Services (AD DS), este cenário pode fazer com que as palavras-passe sejam diferentes entre os dois diretórios.

A writeback de palavra-passe pode ser usada para sincronizar alterações de palavra-passe no AZure AD de volta ao ambiente AD DS no local. O Azure AD Connect fornece um mecanismo seguro para enviar estas alterações de senha de volta para um diretório existente no local a partir de Azure AD.

> [!IMPORTANT]
> Este tutorial mostra a um administrador como permitir que a palavra-passe de autosserviço seja reposta para um ambiente no local. Se é um utilizador final já registado para redefinição da palavra-passe de autosserviço e precisa de voltar à sua conta, vá a https://aka.ms/sspr .
>
> Se a sua equipa de TI não tiver ativado a capacidade de redefinir a sua própria palavra-passe, contacte o seu helpdesk para obter assistência adicional.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure as permissões necessárias para a writeback de palavra-passe
> * Ativar a opção de writeback de palavra-passe no Azure AD Connect
> * Ativar a gravação de palavra-passe no Azure AD SSPR

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Um inquilino Azure AD em funcionamento com pelo menos um Azure AD Premium P1 ou uma licença de julgamento habilitada.
    * Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
    * Para obter mais informações, consulte [os requisitos de licenciamento para Azure AD SSPR](concept-sspr-licensing.md).
* Uma conta com privilégios *de administrador global.*
* Azure AD configurado para reset de senha de autosserviço.
    * Se necessário, [complete o tutorial anterior para ativar a Azure AD SSPR](tutorial-enable-sspr.md).
* Um ambiente AD DS existente no local configurado com uma versão atual do Azure AD Connect.
    * Se necessário, configufique o Azure AD Connect utilizando as definições [Express](../hybrid/how-to-connect-install-express.md) ou [Custom.](../hybrid/how-to-connect-install-custom.md)
    * Para utilizar a gravação de palavra-passe, os seus Controladores de Domínio devem ser o Windows Server 2012 ou mais tarde.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Configure permissões de conta para Azure AD Connect

O Azure AD Connect permite sincronizar utilizadores, grupos e credenciais entre um ambiente AD DS no local e Azure AD. Normalmente instala o Azure AD Connect num Windows Server 2012 ou num computador posterior que se junta ao domínio AD DS no local.

Para trabalhar corretamente com a gravação do SSPR, a conta especificada no Azure AD Connect deve ter as permissões e opções adequadas definidas. Se não tiver a certeza de que conta está atualmente a ser utilizada, abra o Azure AD Connect e selecione a opção **de configuração atual Do View.** A conta a que precisa de adicionar permissões está listada nos **Diretórios Sincronizados.** As seguintes permissões e opções devem ser definidas na conta:

* **Repor palavra-passe**
* **Escrever permissões** em `lockoutTime`
* **Escrever permissões** em `pwdLastSet`
* **Direitos alargados** para "Unexpire Password" no objeto raiz de *cada domínio* naquela floresta, se não já definido.

Se não atribuir estas permissões, o writeback pode parecer configurado corretamente, mas os utilizadores encontram erros quando gerem as suas palavras-passe no local a partir da nuvem. As permissões devem ser aplicadas a **este objeto e a todos os objetos descendentes** para que apareçam "Unexpire Password".  

> [!TIP]
>
> Se as palavras-passe de algumas contas de utilizador não forem escritas de volta para o diretório no local, certifique-se de que a herança não é desativada para a conta no ambiente DS DS em pré-m. As permissões de escrita para palavras-passe devem ser aplicadas a objetos descendentes para que a funcionalidade funcione corretamente.

Para configurar as permissões adequadas para a gravação da palavra-passe, complete os seguintes passos:

1. No ambiente AD DS no local, abra **Utilizadores e Computadores de Diretório Ativo** com uma conta que tenha as permissões adequadas *de administrador de domínio.*
1. A partir do menu **Ver,** certifique-se de que **as funcionalidades Avançadas** estão ligadas.
1. No painel esquerdo, selecione o objeto que representa a raiz do domínio e selecione **Properties**  >  **Security**  >  **Advanced**.
1. A partir do separador **Permissões,** **selecione Adicionar**.
1. Para **o Principal**, selecione a conta a que devem ser aplicadas permissões (a conta utilizada pelo Azure AD Connect).
1. Na lista **Aplica-se à** lista de drop-down, selecione **objetos de utilizador descendentes**.
1. Em *Permissões,* selecione a caixa para a seguinte opção:
    * **Repor palavra-passe**
1. Em *Propriedades*, selecione as caixas para as seguintes opções. Percorra a lista para encontrar estas opções, que podem já estar definidas por padrão:
    * **Escrever lockoutTime**
    * **Escrever pwdLastSet**

    [![Desconfifique as permissões adequadas em Utilizadores ativos e computadores para a conta que é utilizada pelo Azure AD Connect ](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png)](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Quando estiver pronto, **selecione Aplicar / OK** para aplicar as alterações e sair de quaisquer caixas de diálogo abertas.

Quando atualiza permissões, pode demorar até uma hora ou mais para que estas permissões se reproduzam em todos os objetos do seu diretório.

As políticas de palavra-passe no ambiente AD DS no local podem impedir que os resets de palavra-passe sejam corretamente processados. Para que a gravação da palavra-passe funcione de forma mais eficiente, a política de grupo para *a idade mínima* de senha deve ser definida para 0. Esta definição pode ser encontrada em **Políticas de > de Configuração do Computador > definições do Windows > definições de segurança > políticas de conta** dentro de `gpedit.msc` .

Se atualizar a política de grupo, aguarde que a política atualizada se reproduza ou utilize o `gpupdate /force` comando.

> [!Note]
> Para que as palavras-passe sejam alteradas imediatamente, a descodução da palavra-passe deve ser definida para 0. No entanto, se os utilizadores aderirem às políticas no local, e a *idade mínima* de senha for definida para um valor superior a zero, a writeback de palavra-passe ainda funciona após a avaliação das políticas no local.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Ativar a gravação de palavra-passe no Azure AD Connect

Uma das opções de configuração no Azure AD Connect é para a gravação de palavras-passe. Quando esta opção está ativada, os eventos de alteração de palavra-passe fazem com que o Azure AD Connect sincronize as credenciais atualizadas de volta ao ambiente AD DS no local.

Para ativar a gravação de SSPR, primeiro ative a opção de writeback no Azure AD Connect. A partir do seu servidor Azure AD Connect, complete os seguintes passos:

1. Inicie sedumento no seu servidor AZure AD Connect e inicie o assistente de configuração **AZure AD Connect.**
1. Na página de **Boas-vindas**, selecione **Configurar**.
1. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Seguinte**.
1. Na página **Connect to Azure AD,** insira uma credencial de administrador global para o seu inquilino Azure e, em seguida, selecione **Next**.
1. Nas páginas de filtragem **Ligar diretórios** e **Domínio/UO**, selecione **Seguinte**.
1. Na página **Funcionalidades opcionais**, selecione a caixa junto a **Repetição de escrita de palavras-passe** e selecione **Seguinte**.

    ![Configurar Azure AD Connect para writeback de palavra-passe](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Na página **Pronto a configurar**, selecione **Configurar** e aguarde que o processo termine.
1. Quando vir a configuração a concluir, selecione **Sair**.

## <a name="enable-password-writeback-for-sspr"></a>Ativar a gravação de palavra-passe para SSPR

Com a gravação de palavra-passe ativada no Azure AD Connect, agora configurar Azure AD SSPR para writeback. Quando permite que o SSPR utilize a gravação de palavra-passe, os utilizadores que alterem ou reiniciem a sua palavra-passe têm essa palavra-passe atualizada sincronizada de volta ao ambiente AD DS no local.

Para ativar a gravação de palavra-passe em SSPR, complete os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Procure e selecione **Azure Ative Directory**, selecione **Password reset**, em seguida, escolha **a integração no local**.
1. Descreva a opção de **escrever palavras-passe no seu diretório no local?** *Yes*
1. Definir a opção para **permitir que os utilizadores desbloqueiem contas sem redefinir a sua palavra-passe?** *Yes*

    ![Ativar a palavra-passe de autosserviço AZure para a gravação de password](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Quando estiver pronto, **selecione Guardar**.

## <a name="clean-up-resources"></a>Limpar recursos

Se já não pretender utilizar a funcionalidade de writeback SSPR que configuraste como parte deste tutorial, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Ative Directory**, selecione **Password reset**, em seguida, escolha **a integração no local**.
1. Descreva a opção para **escrever palavras-passe no seu diretório no local?** *No*
1. Definir a opção para **permitir que os utilizadores desbloqueiem contas sem redefinir a sua palavra-passe?** *No*

Se já não pretender utilizar qualquer funcionalidade de palavra-passe, complete os seguintes passos do seu servidor AZure AD Connect:

1. Inicie sedumento no seu servidor AZure AD Connect e inicie o assistente de configuração **AZure AD Connect.**
1. Na página de **Boas-vindas**, selecione **Configurar**.
1. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Seguinte**.
1. Na página **Connect to Azure AD,** insira uma credencial de administrador global para o seu inquilino Azure e, em seguida, selecione **Next**.
1. Nas páginas de filtragem **Ligar diretórios** e **Domínio/UO**, selecione **Seguinte**.
1. Na página **de funcionalidades opcionais,** desseleccione a caixa ao lado do **writeback de Password** e selecione **Next**.
1. Na página **Pronto a configurar**, selecione **Configurar** e aguarde que o processo termine.
1. Quando vir a configuração a concluir, selecione **Sair**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu que o Azure AD SSPR fosse retratado para um ambiente AD DS no local. Aprendeu a:

> [!div class="checklist"]
> * Configure as permissões necessárias para a writeback de palavra-passe
> * Ativar a opção de writeback de palavra-passe no Azure AD Connect
> * Ativar a gravação de palavra-passe no Azure AD SSPR

> [!div class="nextstepaction"]
> [Avaliar o risco de início de sessão](tutorial-risk-based-sspr-mfa.md)
