---
title: Configurar a junção de Azure Active Directory híbrida para domínios gerenciados | Microsoft Docs
description: Saiba como configurar a junção de Azure Active Directory híbrida para domínios gerenciados.
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
ms.openlocfilehash: 2768dedf80ab567582322bba4b4190b31400284f
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76167484"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Tutorial: configurar a associação ao Azure Active Directory para os domínios geridos

Como um usuário em sua organização, um dispositivo é uma identidade principal que você deseja proteger. Você pode usar a identidade de um dispositivo para proteger seus recursos a qualquer momento e de qualquer local. Você pode realizar essa meta colocando as identidades do dispositivo e gerenciando-as no Azure Active Directory (AD do Azure) usando um dos seguintes métodos:

- Associação ao Azure AD
- Associação ao Azure AD Híbrido
- Registo do Azure AD

Trazer seus dispositivos para o Azure AD maximiza a produtividade do usuário por meio de SSO (logon único) em sua nuvem e recursos locais. Você pode proteger o acesso a seus recursos locais e de nuvem com [acesso condicional](../active-directory-conditional-access-azure-portal.md) ao mesmo tempo.

Neste tutorial, você aprenderá a configurar o ingresso do Azure AD híbrido para dispositivos Active Directory computadores ingressados no domínio em um ambiente gerenciado. 

Um ambiente gerenciado pode ser implantado por meio de [PHS (sincronização de hash de senha)](../hybrid/whatis-phs.md) ou [PTA (autenticação de passagem)](../hybrid/how-to-connect-pta.md) com [logon único contínuo](../hybrid/how-to-connect-sso.md). Esses cenários não exigem que você configure um servidor de Federação para autenticação.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar a associação ao Azure AD híbrido
> * Permitir dispositivos de nível inferior do Windows
> * Verificar dispositivos associados
> * Resolução de problemas

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você esteja familiarizado com estes artigos:

- [O que é uma identidade de dispositivo?](overview.md)
- [Como planejar sua implementação de ingresso no Azure AD híbrido](hybrid-azuread-join-plan.md)
- [Como fazer a validação controlada do ingresso no Azure AD híbrido](hybrid-azuread-join-control.md)

> [!NOTE]
> O Azure AD não dá suporte a cartões inteligentes ou certificados em domínios gerenciados.

Para configurar o cenário neste artigo, você precisa da [versão mais recente do Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 ou posterior) instalado.

Verifique se Azure AD Connect sincronizou os objetos de computador dos dispositivos que você deseja que sejam ingressados no Azure AD híbrido no Azure AD. Se os objetos de computador pertencerem a UOs (unidades organizacionais) específicas, você também deverá configurar as UOs para sincronização no Azure AD Connect. Para saber mais sobre como sincronizar objetos de computador usando Azure AD Connect, consulte [Configurar a filtragem usando Azure ad Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

A partir da versão 1.1.819.0, Azure AD Connect inclui um assistente que você pode usar para configurar o ingresso no Azure AD híbrido. O assistente simplifica significativamente o processo de configuração. O assistente configura os pontos de conexão de serviço (SCPs) para o registro do dispositivo.

As etapas de configuração neste artigo baseiam-se no uso do assistente no Azure AD Connect.

O ingresso no Azure AD híbrido exige que os dispositivos tenham acesso aos seguintes recursos da Microsoft de dentro da rede da sua organização:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (se você usar ou planeja usar o SSO contínuo)

Se sua organização exigir acesso à Internet por meio de um proxy de saída, a Microsoft recomenda [implementar o Web Proxy Auto-Discovery (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) para habilitar computadores com Windows 10 para o registro de dispositivos com o Azure AD. Se você encontrar problemas ao configurar e gerenciar o WPAD, consulte [solucionar problemas de detecção automática](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Se você não usar o WPAD e precisar definir as configurações de proxy no seu computador, poderá fazer isso começando com o Windows 10 1709. Para obter mais informações, consulte [definir configurações de WinHTTP usando um objeto de política de grupo (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Se você definir as configurações de proxy no seu computador usando as configurações do WinHTTP, todos os computadores que não puderem se conectar ao proxy configurado falharão ao se conectar à Internet.

Se sua organização exigir acesso à Internet por meio de um proxy de saída autenticado, você deverá garantir que seus computadores com Windows 10 possam se autenticar com êxito no proxy de saída. Como os computadores com Windows 10 executam o registro de dispositivo usando o contexto do computador, você deve configurar a autenticação de proxy de saída usando o contexto da máquina. Consulte o seu fornecedor de proxy de saída sobre os requisitos de configuração.

Para verificar se o dispositivo é capaz de acessar os recursos da Microsoft acima na conta do sistema, você pode usar o script de [conectividade de registro de dispositivo de teste](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="configure-hybrid-azure-ad-join"></a>Configurar a associação ao Azure AD híbrido

Para configurar uma associação doi Azure AD híbrido com o Azure AD Connect, precisa das:

- As credenciais de um administrador global para seu locatário do Azure AD
- As credenciais de administrador corporativo para cada uma das florestas

**Para configurar uma junção híbrida do Azure AD usando Azure AD Connect:**

1. Inicie Azure AD Connect e, em seguida, selecione **Configurar**.

   ![Bem-vindo](./media/hybrid-azuread-join-managed-domains/11.png)

1. Na página **tarefas adicionais** , selecione **Configurar opções de dispositivo**e, em seguida, selecione **Avançar**.

   ![Tarefas adicionais](./media/hybrid-azuread-join-managed-domains/12.png)

1. Na página **visão geral** , selecione **Avançar**.

   ![Visão geral](./media/hybrid-azuread-join-managed-domains/13.png)

1. Na página **Ligar ao Azure AD**, introduza as credenciais de um administrador global do inquilino do Azure AD.  

   ![Ligar ao Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. Na página **Opções do dispositivo** , selecione **Configurar ingresso no Azure ad híbrido**e, em seguida, selecione **Avançar**.

   ![Opções de dispositivo](./media/hybrid-azuread-join-managed-domains/15.png)

1. Na página **SCP** , para cada floresta em que você deseja Azure ad Connect para configurar o SCP, conclua as etapas a seguir e, em seguida, selecione **Avançar**:

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Selecione a floresta.
   1. Selecione o serviço de autenticação.
   1. Selecione **Adicionar** para inserir as credenciais de administrador corporativo.

1. Na página **sistemas operacionais do dispositivo** , selecione os sistemas operacionais que os dispositivos em seu ambiente de Active Directory usam e, em seguida, selecione **Avançar**.

   ![Sistema operativo do dispositivo](./media/hybrid-azuread-join-managed-domains/17.png)

1. Na página **pronto para configurar** , selecione **Configurar**.

   ![Preparado para configurar](./media/hybrid-azuread-join-managed-domains/19.png)

1. Na página **configuração concluída** , selecione **sair**.

   ![Configuração completa](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Habilitar dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos ingressados no domínio forem dispositivos de nível inferior do Windows, você deverá:

- Configurar as definições de intranet local para o registo de dispositivos
- Configurar SSO contínuo
- Instalar o Microsoft Workplace Join para computadores com nível inferior do Windows

> [!NOTE]
> O suporte do Windows 7 terminou em 14 de janeiro de 2020. Para obter mais informações, o [suporte para o Windows 7 foi encerrado](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurar as definições de intranet local para o registo de dispositivos

Para concluir com êxito a junção híbrida do Azure AD de seus dispositivos de nível inferior do Windows e para evitar prompts de certificado quando os dispositivos se autenticarem no Azure AD, você poderá enviar uma política para seus dispositivos ingressados no domínio para adicionar as URLs a seguir à zona da intranet local na Internet Explorer

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Você também deve habilitar **a permissão permitir atualizações na barra de status por meio do script** na zona da intranet local do usuário.

### <a name="configure-seamless-sso"></a>Configurar SSO contínuo

Para concluir com êxito a junção híbrida do Azure AD de seus dispositivos de nível inferior do Windows em um domínio gerenciado que usa [PHS](../hybrid/whatis-phs.md) ou [PTA](../hybrid/how-to-connect-pta.md) como seu método de autenticação de nuvem do Azure AD, você também deve [Configurar o SSO contínuo](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Instalar o Microsoft Workplace Join para computadores com nível inferior do Windows

Para registrar dispositivos de nível inferior do Windows, as organizações devem instalar [o Microsoft Workplace Join para computadores que não são do Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). O Microsoft Workplace Join para computadores que não são do Windows 10 está disponível no centro de download da Microsoft.

Você pode implantar o pacote usando um sistema de distribuição de software como [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro `quiet`. A ramificação atual do Configuration Manager oferece benefícios em relação às versões anteriores, como a capacidade de controlar os registros concluídos.

O instalador cria uma tarefa agendada no sistema que é executado no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa une silenciosamente o dispositivo ao Azure AD usando as credenciais do usuário após a autenticação com o Azure AD.

## <a name="verify-the-registration"></a>Verificar o registo

Para verificar o estado de registro do dispositivo em seu locatário do Azure, você pode usar o cmdlet **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** no [módulo Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Quando você usa o cmdlet **Get-MSolDevice** para verificar os detalhes do serviço:

- Um objeto com a **ID do dispositivo** que corresponde à ID no cliente Windows deve existir.
- O valor de **DeviceTrustType** tem de ser **Associados a um domínio**. Essa configuração é equivalente ao estado **ingressado do Azure ad híbrido** na página **dispositivos** no portal do Azure AD.
- Para dispositivos que são usados no acesso condicional, o valor de **habilitado** deve ser **true** e **DeviceTrustLevel** deve ser **gerenciado**.

**Para verificar os detalhes do serviço**:

1. Abra o Windows PowerShell Como um administrador.
1. Insira `Connect-MsolService` para se conectar ao seu locatário do Azure.  
1. Introduza `get-msoldevice -deviceId <deviceId>`.
1. Certifique-se de que **Ativado** está definido como **Verdadeiro**.

## <a name="troubleshoot-your-implementation"></a>Resolver problemas relacionados com a implementação

Se você tiver problemas com a conclusão da junção híbrida do Azure AD para dispositivos Windows ingressados no domínio, consulte:

- [Solucionar problemas de ingresso no Azure AD híbrido para dispositivos atuais do Windows](troubleshoot-hybrid-join-windows-current.md)
- [Solucionar problemas de ingresso no Azure AD híbrido para dispositivos de nível inferior do Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passos seguintes

Saiba como [gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md).
