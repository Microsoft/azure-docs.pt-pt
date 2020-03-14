---
title: Configure hybrid Azure Ative Directory junta-se a domínios geridos  Microsoft Docs
description: Saiba como configurar o Azure Ative Directory híbrido para domínios geridos.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd00972c2da0d3d5dafe76a8619e0f0ccaedc19
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239113"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Tutorial: configurar a associação ao Azure Active Directory para os domínios geridos

Neste tutorial, você aprende a configurar o diretório híbrido Azure Ative (Azure AD) para dispositivos unidos pelo domínio Ative Directory. Este método suporta um ambiente gerido que inclui tanto o Ative Directory no local como o Azure AD.

Tal como um utilizador na sua organização, um dispositivo é uma identidade central que pretende proteger. Pode usar a identidade de um dispositivo para proteger os seus recursos a qualquer momento e de qualquer local. Pode atingir este objetivo gerindo identidades de dispositivos em Azure AD. Utilize um dos métodos seguintes:

- Associação ao Azure AD
- Associação ao Azure AD Híbrido
- Registo do Azure AD

Este artigo centra-se na adesão híbrida azure a D.C.

Trazer os seus dispositivos para o Azure AD maximiza a produtividade dos utilizadores através de um único sinal (SSO) através da sua nuvem e recursos no local. Pode garantir acesso à sua nuvem e recursos no local com [Acesso Condicional](../active-directory-conditional-access-azure-portal.md) ao mesmo tempo.

Pode implementar um ambiente gerido utilizando a sincronização de [hash (PHS)](../hybrid/whatis-phs.md) ou a [autenticação pass-through (PTA)](../hybrid/how-to-connect-pta.md) com [um único sinal individual sem emenda](../hybrid/how-to-connect-sso.md). Estes cenários não requerem que configure um servidor da federação para autenticação.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar a associação ao Azure AD híbrido
> * Permitir dispositivos de nível inferior do Windows
> * Verificar dispositivos associados
> * Resolução de problemas

## <a name="prerequisites"></a>Pré-requisitos

- O [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 ou mais tarde)
- As credenciais de um administrador global para o seu inquilino Da AD Azure
- As credenciais do administrador da empresa para cada uma das florestas

Familiarize-se com estes artigos:

- [O que é uma identidade de dispositivo?](overview.md)
- [Como: Planeie a sua direção ativa híbrida Azure aderir à implementação](hybrid-azuread-join-plan.md)
- [Validação controlada da adesão híbrida azure](hybrid-azuread-join-control.md)

> [!NOTE]
> A Azure AD não suporta smartcards ou certificados em domínios geridos.

Verifique se o Azure AD Connect sincronizou os objetos informáticos dos dispositivos que pretende ser híbrido Azure AD juntou-se ao Azure AD. Se os objetos do computador pertencerem a unidades organizacionais específicas (OUs), configure as OUs para sincronizar em Azure AD Connect. Para saber mais sobre como sincronizar objetos de computador utilizando o Azure AD Connect, consulte a [filtragem baseada em unidade organizacional](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Começando com a versão 1.1.819.0, o Azure AD Connect inclui um assistente para configurar a adesão híbrida azure AD. O assistente simplifica significativamente o processo de configuração. O assistente configura os pontos de ligação de serviço (SCPs) para o registo do dispositivo.

Os passos de configuração deste artigo baseiam-se na utilização do assistente no Azure AD Connect.

A adesão à Hybrid Azure AD requer que os dispositivos tenham acesso aos seguintes recursos da Microsoft a partir da rede da sua organização:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Se utilizar ou planeia utilizar SSO sem emenda)

Se a sua organização necessitar de acesso à internet através de um proxy de saída, recomendamos a implementação do [Web Proxy Auto-Discovery (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) para ativar os computadores do Windows 10 para o registo do dispositivo com a AD Azure. Para resolver problemas de configuração e gestão do WPAD, consulte a [Deteção Automática de Resolução de Problemas](/previous-versions/tn-archive/cc302643(v=technet.10)).

Se não utilizar o WPAD, pode configurar as definições de procuração no seu computador a partir do Windows 10 1709. Para mais informações, consulte as Definições de [Proxy WinHTTP implementadas por GPO](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Se configurar as definições de procuração no seu computador utilizando as definições do WinHTTP, quaisquer computadores que não consigam ligar-se ao proxy configurado não conseguirão ligar-se à internet.

Se a sua organização necessitar de acesso à internet através de um proxy autenticado de saída, certifique-se de que os seus computadores Windows 10 podem autenticar com sucesso o proxy de saída. Uma vez que os computadores do Windows 10 executam o registo do dispositivo utilizando o contexto da máquina, configure a autenticação proxy de saída utilizando o contexto da máquina. Consulte o seu fornecedor de proxy de saída sobre os requisitos de configuração.

Verifique se o dispositivo pode aceder aos recursos da Microsoft acima na conta do sistema utilizando o script de conectividade de registo de dispositivos de [teste.](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)

## <a name="configure-hybrid-azure-ad-join"></a>Configurar a associação ao Azure AD híbrido

Para configurar um Azure AD híbrido, junte-se utilizando o Azure AD Connect:

1. Inicie a Ligação AD Azure e, em seguida, **selecione Configurar**.

   ![Bem-vindo](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. Em **tarefas adicionais,** selecione as opções do **dispositivo Configure**, e, em seguida, selecione **Next**.

   ![Tarefas adicionais](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. Em **visão geral,** selecione **Next**.

   ![Descrição geral](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. Em **Connect to Azure AD,** insira as credenciais de um administrador global para o seu inquilino Azure AD.  

   ![Ligar ao Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. Nas **opções do Dispositivo,** selecione **Configure Hybrid Azure AD join**, and then select **Next**.

   ![Opções do dispositivo](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. Na **configuração SCP**, para cada floresta onde pretende que o Azure AD Connect configure o SCP, complete os seguintes passos e, em seguida, selecione **Next**.

   1. Selecione a **Floresta**.
   1. Selecione um Serviço de **Autenticação**.
   1. Selecione **Adicionar** para introduzir as credenciais do administrador da empresa.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. Nos **sistemas operativos do Dispositivo,** selecione os sistemas operativos que os dispositivos utilizam no seu ambiente de Diretório Ativo e, em seguida, selecione **Next**.

   ![Sistema operativo do dispositivo](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. **Em preparar a configuração,** selecione **Configurar**.

   ![Preparado para configurar](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. Na **Configuração completa,** selecione **Saída**.

   ![Configuração completa](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>Permitir dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos unidos pelo domínio forem dispositivos de nível inferior do Windows, deve:

- Configurar as definições de intranet local para o registo de dispositivos
- Configure sem emenda SSO
- Instale a Microsoft Workplace Join para computadores de nível inferior do Windows

> [!NOTE]
> O suporte do Windows 7 terminou a 14 de janeiro de 2020. Para mais informações, consulte o [suporte do Windows 7 terminado](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurar as definições de intranet local para o registo de dispositivos

Para completar a adesão híbrida do Azure AD aos seus dispositivos de nível inferior windows e para evitar solicitações de certificado quando os dispositivos autenticarem o Azure AD, pode empurrar uma política para os seus dispositivos unidos pelo domínio para adicionar os seguintes URLs à zona intranet local no Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Também deve ativar **as atualizações para** a barra de estado através do script na zona intranet local do utilizador.

### <a name="configure-seamless-sso"></a>Configure sem emenda SSO

Para completar a adesão híbrida do Azure AD aos seus dispositivos de nível inferior windows num domínio gerido que utiliza sincronização de hash de [palavra-passe](../hybrid/whatis-phs.md) ou [autenticação pass-through](../hybrid/how-to-connect-pta.md) como o seu método de autenticação em nuvem Azure AD, também deve [configurar sem emenda SSO](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Instale a Microsoft Workplace Join para computadores de nível inferior do Windows

Para registar dispositivos de nível inferior do Windows, as organizações devem instalar o [Microsoft Workplace Join para computadores não Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join para computadores não Windows 10 está disponível no Microsoft Download Center.

Pode implementar o pacote utilizando um sistema de distribuição de software como o [Microsoft Endpoint Configuration Manager](/configmgr/). O pacote suporta as opções de instalação silenciosa padrão com o parâmetro `quiet`. A versão atual do Gestor de Configuração oferece benefícios em versões anteriores, como a capacidade de rastrear registos concluídos.

O instalador cria uma tarefa programada no sistema que funciona no contexto do utilizador. A tarefa é desencadeada quando o utilizador faz o sinal de si no Windows. A tarefa junta-se silenciosamente ao dispositivo com a AD Azure utilizando as credenciais do utilizador depois de autenticar com a AD Azure.

## <a name="verify-the-registration"></a>Verificar o registo

Verifique o estado de registo do dispositivo no seu inquilino Azure utilizando o **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** . Este cmdlet está no módulo PowerShell do [Diretório Ativo Azure](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Quando utilizar o cmdlet **Get-MSolDevice** para verificar os dados do serviço:

- Deve existir um objeto com o ID do **dispositivo** que corresponda ao ID do cliente Windows.
- O valor para **dispositivoTrustType** é **União de Domínios**. Esta configuração é equivalente ao estado **adesado** do Hybrid Azure na página **Dispositivos** do portal Azure AD.
- Para dispositivos utilizados no Acesso Condicional, o valor para **Ativado** é **Verdadeiro** e **o DeviceTrustLevel** é **gerido**.

Para verificar os detalhes do serviço:

1. Abra o Windows PowerShell como administrador.
1. Insira `Connect-MsolService` para ligar ao seu inquilino Azure.  
1. Introduza `get-msoldevice -deviceId <deviceId>`.
1. Certifique-se de que **Ativado** está definido como **Verdadeiro**.

## <a name="troubleshoot-your-implementation"></a>Resolver problemas relacionados com a implementação

Se tiver problemas em completar o Hybrid Azure AD, junte-se a dispositivos Windows unidos pelo domínio, consulte:

- [Resolução de problemas híbrido Azure Ative Directory juntou-se a dispositivos](troubleshoot-hybrid-join-windows-current.md)
- [Resolução de problemas híbrido Azure Ative Directory juntou-se a dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passos seguintes

Avançar para o próximo artigo para aprender a gerir as identidades do dispositivo utilizando o portal Azure.
> [!div class="nextstepaction"]
> [Gerir identidades do dispositivo](device-management-azure-portal.md)
