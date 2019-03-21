---
title: Azure AD SSPR no ecrã de início de sessão do Windows 10
description: Neste tutorial, vai ativar a reposição de palavra-passe no ecrã de início de sessão do Windows 10 para reduzir as chamadas de suporte técnico.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f8e0b79d5aebd1e92dd71bba72efa7430aa475b
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224656"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>Tutorial: Reposição de palavras-passe do Azure AD no ecrã de início de sessão

Neste tutorial, vai permitir aos utilizadores repor as respetivas palavras-passe no ecrã de início de sessão do Windows 10. Com a nova Atualização de 10 de abril de 2018 do Windows, os utilizadores que tenham dispositivos **associados ao Azure AD** ou **associados ao Azure AD híbrido** podem utilizar uma ligação "Repor palavra-passe" no ecrã de início de sessão. Quando clicam nesta ligação, são encaminhados para a experiência de reposição personalizada de palavra-passe (SSPR) que já conhecem.

> [!div class="checklist"]
> * Configurar a ligação Repor palavra-passe com o Intune
> * Opcionalmente, pode configurar através do Registo do Windows
> * Compreender o que os utilizadores vão ver

## <a name="prerequisites"></a>Pré-requisitos

* Tem em execução, pelo menos, Windows 10, versão de Abril de 2018 atualização e os dispositivos tem de ser:
   * [Azure AD associado](../device-management-azure-portal.md) ou
   * [Azure híbrido associado ao AD](../device-management-hybrid-azuread-joined-devices-setup.md), com conectividade de rede para um controlador de domínio.
* Tem de ativar o Azure de reposição de palavras-passe self-service de AD.
* Se os dispositivos Windows 10 estão por trás de um servidor proxy ou de uma firewall, tem de adicionar os URLs `passwordreset.microsoftonline.com` e `ajax.aspnetcdn.com` para o tráfego HTTPS (porta 443) permitido lista de URLs.
* SSPR para o Windows 10 apenas é suportado com proxies de nível de máquina
* Reveja as limitações abaixo antes de tentar esta funcionalidade no seu ambiente.
* Se utilizar uma imagem, antes de sysprep Certifique-se de que a cache da web é limpo para o administrador incorporado antes de efetuar o passo de CopyProfile. Podem encontrar mais informações sobre isso no artigo de suporte [fraco desempenho ao utilizar o perfil de usuário padrão personalizado](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).

## <a name="configure-reset-password-link-using-intune"></a>Configurar a ligação Repor palavra-passe com o Intune

Implementar a alteração da configuração para ativar a reposição de palavra-passe no ecrã de início de sessão com o Intune é o método mais flexível. O Intune permite implementar a alteração de configuração num grupo específico de máquinas que definir. Este método exige a inscrição do dispositivo no Intune.

### <a name="create-a-device-configuration-policy-in-intune"></a>Criar uma política de configuração de dispositivos no Intune

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e clique em **Intune**.
2. Aceda a **Configuração de dispositivos** > **Perfis** > **Criar Perfil** para criar um perfil de configuração de dispositivos novo
   * Dê um nome relevante ao perfil
   * Opcionalmente, indique uma descrição relevante do perfil
   * Plataforma **Windows 10 e posterior**
   * Tipo de perfil **Personalizado**

3. Configure as **Definições**
   * **Adicione** a Definição de OMA-URI seguinte para ativar a ligação Repor palavra-passe
      * Indique um nome relevante para explicar o que a definição faz
      * Opcionalmente, indique uma descrição relevante da definição
      * **OMA-URI** definido como `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * **Tipo de dados** definido como **Inteiro**
      * **Valor** definido como **1**
      * Clique em **OK**
   * Clique em **OK**
4. Clique em **Criar**.

### <a name="assign-a-device-configuration-policy-in-intune"></a>Atribuir uma política de configuração de dispositivos no Intune

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Criar um grupo ao qual aplicar a política de configuração de dispositivos

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e clique em **Azure Active Directory**.
2. Navegue até **Utilizadores e grupos** > **Todos os grupos** > **Novo grupo**
3. Indique um nome para o grupo e, em **tipo de associação**, escolha **Atribuído**
   * Em **Membros**, escolha os dispositivos Windows 10 associados ao Azure AD aos quais pretende aplicar a política
   * Clique em **Selecionar**
4. Clique em **Criar**.

Estão disponíveis mais informações sobre a criação de grupos no artigo [Manage access to resources with Azure Active Directory groups](../fundamentals/active-directory-manage-groups.md) (Gerir o acesso a recursos com grupos do Azure Active Directory).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Atribuir a política de configuração de dispositivos ao grupo de dispositivos

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e clique em **Intune**.
2. Aceda a **Configuração de dispositivos** > **Perfis** > clique no perfil criado anteriormente para localizar o perfil de configuração de dispositivos que criámos acima.
3. Atribuir o perfil a um grupo de dispositivos 
   * Clique em **Atribuições** > em **Incluir** > **Selecionar grupos a incluir**
   * Selecione o grupo criado anteriormente e clique em **Selecionar**
   * Clique em **Guardar**

   ![Atribuição][Assignment]

Criou e atribuiu agora uma política de configuração de dispositivos para ativar a ligação Repor palavra-passe no ecrã de início de sessão com o Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Configurar a ligação Repor palavra-passe com o registo

1. Inicie sessão no PC Windows com as credenciais administrativas
2. Execute **regedit** como administrador
3. Defina a chave de registo seguinte
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>O que os utilizadores veem

Agora que a política está configurada e atribuída, que alterações há para o utilizador? Como é que podem saber que é permitido repor a palavra-passe no ecrã de início de sessão?

![LoginScreen][LoginScreen]

Quando os utilizadores tentam iniciar sessão, veem agora experiência no ecrã de início de sessão de reposição de uma ligação de reposição de palavra-passe que abre-se a palavra-passe self-service. Com esta funcionalidade, os utilizadores podem repor as palavras-passe sem terem de utilizar outro dispositivo para aceder a um browser.

Os seus utilizadores podem obter orientações sobre como utilizar esta funcionalidade em [Reset your work or school password](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in) (Repor a palavra-passe da conta escolar ou profissional)

O registo de auditoria do Azure AD irá incluir informações sobre o endereço IP e ClientType onde ocorreu a reposição de palavra-passe.

![Ecrã de exemplo de início de sessão com reposição de palavra-passe no registo de auditoria do Azure AD](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

Quando os utilizadores, repor a palavra-passe a partir do ecrã de início de sessão de um dispositivo Windows 10, é criada uma conta temporária de baixo privilégio chamada "defaultuser1". Esta conta é utilizada para manter o processo de reposição de palavra-passe segura. A própria conta tem uma palavra-passe gerada aleatoriamente, não é exibida para o início de sessão de dispositivo e será automaticamente removida depois do utilizador repõe a palavra-passe. Vários perfis de "defaultuser" podem existir, mas podem ser ignorados com segurança.

## <a name="limitations"></a>Limitações

Ao testar esta funcionalidade com o Hyper-V, a ligação "Repor palavra-passe" não aparece.

* Aceda à VM com a qual está a testar, clique em **Ver** e desmarque **Sessão avançada**.

Ao testar esta funcionalidade com o ambiente de trabalho remoto ou uma sessão de VM avançada, a ligação "Repor palavra-passe" não aparece.

* A reposição de palavras-passe não é suportada em Ambientes de Trabalho Remotos atualmente.

Se Ctrl + Alt + Del for exigido pela política nas versões do Windows 10 antes de 1809, **Repor palavra-passe** não funcionará.

Se as notificações do ecrã de bloqueio são desativadas, **Repor palavra-passe** não funcionará.

As seguintes definições de política são conhecidas por interferir com a capacidade de repor as palavras-passe

   * HideFastUserSwitching é definido como ativado ou 1
   * DontDisplayLastUserName é definido como ativado ou 1
   * NoLockScreen é definido como ativado ou 1
   * EnableLostMode é definido no dispositivo
   * Explorer.exe é substituído por um shell personalizado

Esta funcionalidade não funciona para redes com rede autenticação 802.1x implementada e a opção "Executar imediatamente antes do início de sessão do utilizador". Para redes com rede autenticação 802.1x implementado é recomendado para utilizar a autenticação de computador para ativar esta funcionalidade.

Para cenários híbridos associados a um domínio, o fluxo de trabalho SSPR irá concluir com êxito sem a necessidade de um controlador de domínio do Active Directory. Se um utilizador conclui o processo de reposição de palavra-passe quando a comunicação para um controlador de domínio do Active Directory não estiver disponível, semelhante a quando está a trabalhar remotamente, o utilizador não conseguir iniciar sessão no dispositivo até que o dispositivo pode comunicar com um controlador de domínio e Atualize a credencial em cache. **Conectividade com um controlador de domínio é necessário para utilizar a nova palavra-passe pela primeira vez**.

## <a name="clean-up-resources"></a>Limpar recursos

Se decidir que já não quer utilizar a funcionalidade que configurou como parte deste tutorial, elimine o perfil de configuração de dispositivos do Intune que criou ou a chave de registo.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, permitiu aos utilizadores repor as respetivas palavras-passe no ecrã de início de sessão do Windows 10. Avance para o próximo tutorial para ver como o Azure Identity Protection pode ser integrado na reposição personalizada de palavra-passe e nas experiências de Multi-Factor Authentication.

> [!div class="nextstepaction"]
> [Avaliar o risco de início de sessão](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Atribuir a política de configuração de dispositivos do Intune a um grupo de dispositivos Windows 10"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Ligação Repor palavra-passe no ecrã de início de sessão do Windows 10"
