---
title: Ativar a reescrita da palavra-passe do Diretório Ativo azure
description: Neste tutorial, aprende-se a permitir que a palavra-passe de autosserviço da Azure AD reponha a reescrita utilizando o Azure AD Connect para sincronizar as alterações para um ambiente de Serviços de Domínio de Diretório Ativo no local.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 04/24/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd4815187e829cff56893988874e4dcac3b8985e
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143764"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Tutorial: Enable Azure Ative Diretório auto-service password repor reescrita para um ambiente no local

Com o reset de autosserviço do Azure Ative Directory (Azure AD), os utilizadores podem atualizar a sua palavra-passe ou desbloquear a sua conta através de um navegador web. Num ambiente híbrido onde o Azure AD está ligado a um ambiente de Serviços de Domínio ativo de diretório (AD DS) no local, este cenário pode fazer com que as palavras-passe sejam diferentes entre os dois diretórios.

A reescrita de palavra-passe pode ser usada para sincronizar as alterações de palavra-passe no Azure AD de volta ao seu ambiente AD DS no local. O Azure AD Connect fornece um mecanismo seguro para enviar estas alterações de senha de volta para um diretório existente no local a partir de Azure AD.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure as permissões necessárias para a reescrita de palavra-passe
> * Ativar a opção de redação de palavra-passe no Azure AD Connect
> * Ativar a reescrita da palavra-passe no Azure AD SSPR

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Um inquilino do Azure AD em funcionamento com, pelo menos, uma licença de avaliação ativada.
    * Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
    * Para mais informações, consulte [os requisitos de licenciamento para a Azure AD SSPR](concept-sspr-licensing.md).
* Uma conta com privilégios *de administrador global.*
* Azure AD configurado para reset de senha de autosserviço.
    * Se necessário, complete o tutorial anterior para ativar o [Azure AD SSPR](tutorial-enable-sspr.md).
* Um ambiente AD DS existente no local configurado com uma versão atual do Azure AD Connect.
    * Se necessário, configure o Azure AD Connect utilizando as definições [Express](../hybrid/how-to-connect-install-express.md) ou [Custom.](../hybrid/how-to-connect-install-custom.md)
    * Para utilizar a Palavra-passe Writeback, os seus Controladores de Domínio devem ser do Windows Server 2008 R2 ou posteriormente.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Configure permissões de conta para Azure AD Connect

O Azure AD Connect permite sincronizar utilizadores, grupos e credenciais entre um ambiente AD DS no local e a AD Azure. Normalmente instala o Azure AD Connect num Windows Server 2012 ou posterior computador que esteja ligado ao domínio AD DS no local.

Para funcionar corretamente com a reescrita da SSPR, a conta especificada no Azure AD Connect deve ter as permissões e opções adequadas definidas. Se não tiver a certeza de qual a conta que está atualmente a ser utilizada, abra o Azure AD Connect e selecione a opção de **configuração atual do View.** A conta a que precisa adicionar permissões está listada em **Diretórios Sincronizados**. As seguintes permissões e opções devem ser definidas na conta:

* **Repor palavra-passe**
* **Escrever permissões** em`lockoutTime`
* **Escrever permissões** em`pwdLastSet`
* **Direitos alargados** para "Palavra-passe não expirada" em qualquer um deles:
   * O objeto raiz de *cada domínio* naquela floresta
   * As unidades organizacionais do utilizador (OUs) que pretende estar ao seu alcance para o SSPR

Se não atribuir estas permissões, a reprodução parece estar configurada corretamente, mas os utilizadores encontram erros quando gerem as suas palavras-passe no local a partir da nuvem. Devem ser aplicadas permissões a **Este objeto e a todos os objetos descendentes** para que apareça "Palavra-passe não expirada".  

Para configurar as permissões adequadas para a reescrita de palavra-passe, complete os seguintes passos:

1. No seu ambiente AD DS no local, abra **utilizadores e computadores de diretório ativo** com uma conta que tenha as permissões adequadas do administrador de *domínio.*
1. A partir do menu **'Ver',** certifique-se de que as **funcionalidades Avançadas** estão ligadas.
1. No painel esquerdo, selecione o objeto que representa a raiz do domínio e selecione **Properties** > **Security** > **Advanced**.
1. A partir do separador **Permissões,** selecione **Adicionar**.
1. Para **principal,** selecione a conta à a que as permissões devem ser aplicadas (a conta utilizada pelo Azure AD Connect).
1. Na lista **Aplica-se à** lista de abandono, selecione **objetos de utilizador descendentes**.
1. Em *Permissões,* selecione a caixa para a seguinte opção:
    * **Repor palavra-passe**
1. Em *Propriedades*, selecione as caixas para as seguintes opções. Você precisa percorrer a lista para encontrar estas opções, que podem já estar definidas por padrão:
    * **Escreva lockoutTime**
    * **Escreva pwdLastSet**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Quando estiver pronto, selecione **Aplicar / OK** para aplicar as alterações e sair de quaisquer caixas de diálogo abertas.

Quando atualiza as permissões, pode demorar até uma hora ou mais para que estas permissões se reproduzam a todos os objetos do seu diretório.

As políticas de password no ambiente AD DS no local podem impedir que os resets de palavra-passe sejam corretamente processados. Para que a reescrita de palavra-passe funcione de forma mais eficiente, a política de grupo para a idade mínima da *palavra-passe* deve ser definida para 0. Esta definição pode ser encontrada em **políticas de configuração de computador > > definições do Windows > Definições de segurança > políticas** de conta dentro de `gpedit.msc`.

Se atualizar a política do grupo, aguarde que `gpupdate /force` a política atualizada se reproduza ou utilize o comando.

> [!Note]
> Para que as palavras-passe sejam alteradas imediatamente, a reescrita da palavra-passe deve ser definida para 0. No entanto, se os utilizadores aderirem às políticas no local, e a idade mínima da *palavra-passe* for fixada para um valor superior a zero, a redação da palavra-passe continuará a funcionar após a avaliação das políticas no local. 

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Ativar a reescrita da palavra-passe no Azure AD Connect

Uma das opções de configuração no Azure AD Connect é para a reescrita de palavra-passe. Quando esta opção está ativada, os eventos de mudança de palavra-passe fazem com que o Azure AD Connect sincronize as credenciais atualizadas de volta ao ambiente AD DS no local.

Para permitir o redefinição da palavra-passe de autosserviço, primeiro ative a opção de recompra de reprodução no Azure AD Connect. A partir do seu servidor Azure AD Connect, complete os seguintes passos:

1. Inicie sessão no seu servidor Azure AD Connect e inicie o assistente de configuração **Azure AD Connect.**
1. Na página de **Boas-vindas**, selecione **Configurar**.
1. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Seguinte**.
1. Na página **De AD Connect to Azure,** insira uma credencial de administrador global para o seu inquilino Azure e, em seguida, selecione **Next**.
1. Nas páginas de filtragem **Ligar diretórios** e **Domínio/UO**, selecione **Seguinte**.
1. Na página **Funcionalidades opcionais**, selecione a caixa junto a **Repetição de escrita de palavras-passe** e selecione **Seguinte**.

    ![Configure Azure AD Connect para reescrita de palavra-passe](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Na página **Pronto a configurar**, selecione **Configurar** e aguarde que o processo termine.
1. Quando vir a configuração a concluir, selecione **Sair**.

## <a name="enable-password-writeback-for-sspr"></a>Ativar a reescrita da palavra-passe para SSPR

Com a reescrita de palavra-passe ativada no Azure AD Connect, configurar agora o Azure AD SSPR para reescrever. Quando permite ao SSPR utilizar a reescrita de palavra-passe, os utilizadores que alteram ou reseta a sua palavra-passe têm essa palavra-passe atualizada sincronizada de volta para o ambiente AD DS no local.

Para permitir a reescrita da palavra-passe no SSPR, complete os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Procure e selecione **Azure Ative Directory**, selecione **reset de palavra-passe**e, em seguida, escolha a **integração no local**.
1. Detete a opção de **escrever palavras-passe para o seu diretório no local?** *Yes*
1. Definir a opção para **permitir que os utilizadores desbloqueiem contas sem reajustar a sua palavra-passe para** *Sim*.

    ![Ativar o reset da palavra-passe autosserviço azure AD para reescrita de palavra-passe](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Quando estiver pronto, selecione **Guardar**.

## <a name="clean-up-resources"></a>Limpar recursos

Se já não pretender utilizar a funcionalidade de redação sspr que configuracomo parte deste tutorial, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Ative Directory**, selecione **reset de palavra-passe**e, em seguida, escolha a **integração no local**.
1. Detete a opção de **escrever palavras-passe para o seu diretório no local?** *No*
1. Definir a opção para permitir que os utilizadores *No* **desbloqueiem contas sem reajustar a sua palavra-passe?**

Se já não pretender utilizar qualquer funcionalidade de senha, complete os seguintes passos do seu servidor Azure AD Connect:

1. Inicie sessão no seu servidor Azure AD Connect e inicie o assistente de configuração **Azure AD Connect.**
1. Na página de **Boas-vindas**, selecione **Configurar**.
1. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Seguinte**.
1. Na página **De AD Connect to Azure,** insira uma credencial de administrador global para o seu inquilino Azure e, em seguida, selecione **Next**.
1. Nas páginas de filtragem **Ligar diretórios** e **Domínio/UO**, selecione **Seguinte**.
1. Na página **de funcionalidades Opcionais,** desmarque a caixa ao lado da reescrita da **Palavra-passe** e selecione **Next**.
1. Na página **Pronto a configurar**, selecione **Configurar** e aguarde que o processo termine.
1. Quando vir a configuração a concluir, selecione **Sair**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu que a Azure AD SSPR reescrevesse para um ambiente AD DS no local. Aprendeu a:

> [!div class="checklist"]
> * Configure as permissões necessárias para a reescrita de palavra-passe
> * Ativar a opção de redação de palavra-passe no Azure AD Connect
> * Ativar a reescrita da palavra-passe no Azure AD SSPR

> [!div class="nextstepaction"]
> [Avaliar o risco de início de sessão](tutorial-risk-based-sspr-mfa.md)
