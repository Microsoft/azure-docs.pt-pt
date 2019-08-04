---
title: Configurar a junção de Azure Active Directory híbrida para domínios federados | Microsoft Docs
description: Saiba como configurar a junção de Azure Active Directory híbrida para domínios federados.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05c81b5cde9e9c64d2d69bea1d14a18394f31e2a
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774607"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Tutorial: Configurar a junção de Azure Active Directory híbrida para domínios federados

Como um usuário em sua organização, um dispositivo é uma identidade principal que você deseja proteger. Você pode usar a identidade de um dispositivo para proteger seus recursos a qualquer momento e de qualquer local. Você pode realizar essa meta colocando as identidades do dispositivo e gerenciando-as no Azure Active Directory (AD do Azure) usando um dos seguintes métodos:

- Associação ao Azure AD
- Associação ao Azure AD Híbrido
- Registo do Azure AD

Trazer seus dispositivos para o Azure AD maximiza a produtividade do usuário por meio de SSO (logon único) em sua nuvem e recursos locais. Você pode proteger o acesso a seus recursos locais e de nuvem com [acesso condicional](../active-directory-conditional-access-azure-portal.md) ao mesmo tempo.

Um ambiente federado deve ter um provedor de identidade que dê suporte aos requisitos a seguir. Se você tiver um ambiente federado usando Serviços de Federação do Active Directory (AD FS) (AD FS), os requisitos abaixo já terão suporte.

- **Declaração de WIAORMULTIAUTHN:** Essa declaração é necessária para fazer uma junção híbrida do Azure AD para dispositivos de nível inferior do Windows.
- **Protocolo WS-Trust:** Esse protocolo é necessário para autenticar dispositivos adicionados ao Azure AD híbridos atuais do Windows com o Azure AD.
  Quando você estiver usando AD FS, será necessário habilitar os seguintes pontos de extremidade WS-Trust:`/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> O **ADFS/Services/Trust/2005/windowstransport** ou **ADFS/Services/Trust/13/windowstransport** devem ser habilitados como pontos de extremidade voltados para a intranet e não devem ser expostos como pontos de extremidade voltados para a extranet por meio do proxy de aplicativo Web. Para saber mais sobre como desabilitar os pontos de extremidade do WIndows do WS-Trust, confira [desabilitar pontos de extremidade do Windows do WS-Trust no proxy](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Você pode ver quais pontos de extremidade são habilitados por meio do console de gerenciamento de AD FS em**pontos de extremidade**de **serviço** > .

Neste tutorial, você aprenderá a configurar o ingresso do Azure AD híbrido para Active Directory dispositivos de computadores ingressados no domínio em um ambiente federado usando AD FS.

Saiba como:

> [!div class="checklist"]
> * Configurar a associação ao Azure AD híbrido
> * Habilitar dispositivos de nível inferior do Windows
> * Verificar o registo
> * Resolução de problemas

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que o you'e esteja familiarizado com estes artigos:

- [O que é uma identidade de dispositivo?](overview.md)
- [Como planejar sua implementação de ingresso no Azure AD híbrido](hybrid-azuread-join-plan.md)
- [Como fazer a validação controlada do ingresso no Azure AD híbrido](hybrid-azuread-join-control.md)

Para configurar o cenário neste tutorial, precisa do:

- Windows Server 2012 R2 com o AD FS
- [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) versão 1.1.819.0 ou posterior

A partir da versão 1.1.819.0, Azure AD Connect inclui um assistente que você pode usar para configurar o ingresso no Azure AD híbrido. O assistente simplifica significativamente o processo de configuração. O assistente relacionado:

- Configura os pontos de conexão de serviço (SCPs) para o registro do dispositivo
- Faz uma cópia segurança da fidedignidade da entidade confiadora do Azure AD existente
- Atualiza as regras de afirmação na confiança do Azure AD

As etapas de configuração neste artigo baseiam-se no uso do assistente de Azure AD Connect. Se você tiver uma versão anterior do Azure AD Connect instalada, deverá atualizá-la para 1.1.819 ou posterior para usar o assistente. Se a instalação da versão mais recente do Azure AD Connect não for uma opção para você, consulte [como configurar manualmente a junção híbrida do Azure ad](hybrid-azuread-join-manual.md).

O ingresso no Azure AD híbrido exige que os dispositivos tenham acesso aos seguintes recursos da Microsoft de dentro da rede da sua organização:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- STS (serviço de token de segurança) da sua organização (para domínios federados)
- `https://autologon.microsoftazuread-sso.com`(Se você usar ou planeja usar o SSO contínuo)

A partir do Windows 10 1803, se a junção do Azure AD híbrido instantânea para um ambiente federado usando AD FS falhar, dependeremos Azure AD Connect para sincronizar o objeto de computador no Azure AD que é usado posteriormente para concluir o registro do dispositivo para o Azure híbrido Ingresso no AD. Verifique se Azure AD Connect sincronizou os objetos de computador dos dispositivos que você deseja que sejam ingressados no Azure AD híbrido no Azure AD. Se os objetos de computador pertencerem a UOs (unidades organizacionais) específicas, você também deverá configurar as UOs para sincronização no Azure AD Connect. Para saber mais sobre como sincronizar objetos de computador usando Azure AD Connect, consulte [Configurar a filtragem usando Azure ad Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Se sua organização exigir acesso à Internet por meio de um proxy de saída, a Microsoft recomenda [implementar o Web Proxy Auto-Discovery (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) para habilitar computadores com Windows 10 para o registro de dispositivos com o Azure AD. Se você encontrar problemas ao configurar e gerenciar o WPAD, consulte [solucionar problemas de detecção automática](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Se você não usar o WPAD e quiser definir as configurações de proxy no seu computador, poderá fazer isso começando com o Windows 10 1709. Para obter mais informações, consulte [definir configurações de WinHTTP usando um objeto de política de grupo (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Se você definir as configurações de proxy no seu computador usando as configurações do WinHTTP, todos os computadores que não puderem se conectar ao proxy configurado falharão ao se conectar à Internet.

Se sua organização exigir acesso à Internet por meio de um proxy de saída autenticado, você deverá garantir que seus computadores com Windows 10 possam se autenticar com êxito no proxy de saída. Como os computadores com Windows 10 executam o registro de dispositivo usando o contexto do computador, você deve configurar a autenticação de proxy de saída usando o contexto da máquina. Consulte o seu fornecedor de proxy de saída sobre os requisitos de configuração.

## <a name="configure-hybrid-azure-ad-join"></a>Configurar a associação ao Azure AD híbrido

Para configurar uma junção híbrida do Azure AD usando Azure AD Connect, você precisará de:

- As credenciais de um administrador global para seu locatário do Azure AD  
- As credenciais de administrador corporativo para cada uma das florestas
- As credenciais do seu administrador de AD FS

**Para configurar uma junção híbrida do Azure ad usando Azure ad Connect**:

1. Inicie Azure AD Connect e, em seguida, selecione **Configurar**.

   ![Bem-vindo](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na página **tarefas adicionais** , selecione **Configurar opções de dispositivo**e, em seguida, selecione **Avançar**.

   ![Tarefas adicionais](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na página **visão geral** , selecione **Avançar**.

   ![Descrição geral](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na página **conectar ao Azure ad** , insira as credenciais de um administrador global para seu locatário do Azure AD e, em seguida, selecione **Avançar**.

   ![Ligar ao Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na página **Opções do dispositivo** , selecione **Configurar ingresso no Azure ad híbrido**e, em seguida, selecione **Avançar**.

   ![Opções do dispositivo](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na página **SCP** , conclua as etapas a seguir e, em seguida, selecione **Avançar**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Selecione a floresta.
   1. Selecione o serviço de autenticação. Você deve selecionar **AD FS Server** , a menos que sua organização tenha exclusivamente clientes do Windows 10 e tenha configurado a sincronização de computador/dispositivo ou a sua organização Use o SSO contínuo.
   1. Selecione **Adicionar** para inserir as credenciais de administrador corporativo.

1. Na página **sistemas operacionais do dispositivo** , selecione os sistemas operacionais que os dispositivos em seu ambiente de Active Directory usam e, em seguida, selecione **Avançar**.

   ![Sistema operativo do dispositivo](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na página **configuração da Federação** , insira as credenciais do administrador do AD FS e, em seguida, selecione **Avançar**.

   ![Configuração de federação](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na página **pronto para configurar** , selecione **Configurar**.

   ![Preparado para configurar](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na página **configuração concluída** , selecione **sair**.

   ![Configuração completa](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Habilitar dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos ingressados no domínio forem dispositivos de nível inferior do Windows, você deverá:

- Configurar as definições de intranet local para o registo de dispositivos
- Instalar o Microsoft Workplace Join para computadores com nível inferior do Windows

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurar as definições de intranet local para o registo de dispositivos

Para concluir com êxito a junção híbrida do Azure AD de seus dispositivos de nível inferior do Windows e para evitar prompts de certificado quando os dispositivos se autenticarem no Azure AD, você poderá enviar uma política para seus dispositivos ingressados no domínio para adicionar as URLs a seguir à zona da intranet local na Internet Explorer

- `https://device.login.microsoftonline.com`
- O STS da sua organização (para domínios federados)
- `https://autologon.microsoftazuread-sso.com`(Para SSO contínuo)

Você também deve habilitar **a permissão permitir atualizações na barra de status por meio do script** na zona da intranet local do usuário.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Instalar o Microsoft Workplace Join para computadores com nível inferior do Windows

Para registrar dispositivos de nível inferior do Windows, as organizações devem instalar [o Microsoft Workplace Join para computadores que não são do Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). O Microsoft Workplace Join para computadores que não são do Windows 10 está disponível no centro de download da Microsoft.

Você pode implantar o pacote usando um sistema de distribuição de software como [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). O pacote oferece suporte às opções de instalação silenciosa padrão `quiet` com o parâmetro. A ramificação atual do Configuration Manager oferece benefícios em relação às versões anteriores, como a capacidade de controlar os registros concluídos.

O instalador cria uma tarefa agendada no sistema que é executado no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa une silenciosamente o dispositivo ao Azure AD usando as credenciais do usuário após a autenticação com o Azure AD.

## <a name="verify-the-registration"></a>Verificar o registo

Para verificar o estado de registro do dispositivo em seu locatário do Azure, você pode usar o cmdlet **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** no [módulo Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Quando você usa o cmdlet **Get-MSolDevice** para verificar os detalhes do serviço:

- Um objeto com a **ID do dispositivo** que corresponde à ID no cliente Windows deve existir.
- O valor de **DeviceTrustType** tem de ser **Associados a um domínio**. Essa configuração é equivalente ao estado **Unido do Azure ad híbrido** em **dispositivos** no portal do Azure AD.
- Para dispositivos que são usados no acesso condicional, o valor de **habilitado** deve ser **true** e **DeviceTrustLevel** deve ser **gerenciado**.

**Para verificar os detalhes do serviço**:

1. Abra o Windows PowerShell como administrador.
1. Insira `Connect-MsolService` para se conectar ao seu locatário do Azure.  
1. Introduza `get-msoldevice -deviceId <deviceId>`.
1. Certifique-se de que **Ativado** está definido como **Verdadeiro**.

## <a name="troubleshoot-your-implementation"></a>Resolver problemas relacionados com a implementação

Se você tiver problemas com a conclusão da junção híbrida do Azure AD para dispositivos Windows ingressados no domínio, consulte:

- [Solucionar problemas de ingresso no Azure AD híbrido para dispositivos atuais do Windows](troubleshoot-hybrid-join-windows-current.md)
- [Solucionar problemas de ingresso no Azure AD híbrido para dispositivos de nível inferior do Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passos seguintes

Saiba como [gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
