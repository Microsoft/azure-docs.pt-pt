---
title: Configure híbrido Azure Ative Directy junta-se a domínios geridos | Microsoft Docs
description: Saiba como configurar o Azure Ative Directory híbrido para domínios geridos.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4545e92767a427b8cd89af07ed4d06053685977a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578010"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Tutorial: configurar a associação ao Azure Active Directory para os domínios geridos

Neste tutorial, aprende-se a configurar o Azure Ative Directory (Azure AD) híbrido para dispositivos de domínio ativo. Este método suporta um ambiente gerido que inclui tanto no local o Ative Directory como o Azure AD.

Como um utilizador na sua organização, um dispositivo é uma identidade central que quer proteger. Pode utilizar a identidade de um dispositivo para proteger os seus recursos a qualquer momento e a partir de qualquer local. Pode atingir este objetivo gerindo identidades de dispositivos em Azure AD. Utilizar um dos seguintes métodos:

- Associação ao Azure AD
- Associação ao Azure AD Híbrido
- Registo do Azure AD

Este artigo centra-se na ad join híbrida Azure.

Trazer os seus dispositivos para a Azure AD maximiza a produtividade do utilizador através de um único sign-on (SSO) através da sua nuvem e recursos no local. Pode garantir o acesso à sua nuvem e recursos no local com [acesso condicional](../conditional-access/howto-conditional-access-policy-compliant-device.md) ao mesmo tempo.

Pode implementar um ambiente gerido utilizando [a sincronização de haxixe de palavra-passe (PHS)](../hybrid/whatis-phs.md) ou [a autenticação de passagem (PTA)](../hybrid/how-to-connect-pta.md) com [um único sinal sem costura](../hybrid/how-to-connect-sso.md). Estes cenários não requerem que configures um servidor da federação para autenticação.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar a associação ao Azure AD híbrido
> * Permitir dispositivos de nível inferior do Windows
> * Verificar dispositivos associados
> * Resolução de problemas

## <a name="prerequisites"></a>Pré-requisitos

- [O Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 ou mais tarde)
- As credenciais de um administrador global para o seu inquilino AZure AD
- As credenciais do administrador da empresa para cada uma das florestas

Familiarize-se com estes artigos:

- [O que é um ID do dispositivo?](overview.md)
- [Como: Planeie o seu Azure Ative Directory híbrido junte-se à implementação](hybrid-azuread-join-plan.md)
- [Validação controlada de associação do Azure AD híbrido](hybrid-azuread-join-control.md)

> [!NOTE]
> A Azure AD não suporta smartcards ou certificados em domínios geridos.

Verifique se o Azure AD Connect sincronizou os objetos de computador dos dispositivos que pretende ser híbrido Azure AD ligados ao Azure AD. Se os objetos do computador pertencerem a unidades organizacionais específicas (OUs), configuure as OUs para sincronizar em Azure AD Connect. Para saber mais sobre como sincronizar objetos de computador utilizando o Azure AD Connect, consulte [a filtragem baseada em unidades organizacionais](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

> [!NOTE]
> Para obter a sincronização do registo do dispositivo para ter sucesso, como parte da configuração de registo do dispositivo, não exclua os atributos predefinidos do dispositivo da sua configuração de sincronização Azure AD Connect. Para saber mais sobre os atributos padrão do dispositivo sincronizados com o AAD, consulte [Atributos sincronizados por Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Começando pela versão 1.1.819.0, o Azure AD Connect inclui um assistente para configurar a ad AD híbrida. O assistente simplifica significativamente o processo de configuração. O assistente configura os pontos de ligação de serviço (SCPs) para o registo do dispositivo.

Os passos de configuração deste artigo baseiam-se na utilização do assistente no Azure AD Connect.

A adesão híbrida AD AD requer que os dispositivos tenham acesso aos seguintes recursos da Microsoft a partir da rede da sua organização:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Se utilizar ou pretender utilizar SSO sem costura)

> [!WARNING]
> Se a sua organização utilizar servidores proxy que intercetam tráfego SSL para cenários como prevenção de perda de dados ou restrições de inquilinos AZure AD, certifique-se de que o tráfego para https://device.login.microsoftonline.com ' ' é excluído do break-and-inspect TLS. A não exclusão https://device.login.microsoftonline.com pode causar interferência na autenticação do certificado do cliente, causando problemas com o registo do dispositivo e o acesso condicional baseado no dispositivo.

Se a sua organização necessitar de acesso à internet através de um proxy de saída, pode utilizar [a implementação da Web Proxy Auto-Discovery (WPAD)](/previous-versions/tn-archive/cc995261(v=technet.10)) para ativar os computadores do Windows 10 para o registo de dispositivos com Azure AD. Para resolver problemas que configuram e gerem o WPAD, consulte [a resolução de problemas de Deteção Automática](/previous-versions/tn-archive/cc302643(v=technet.10)). Nos dispositivos Windows 10 antes da atualização de 1709, o WPAD é a única opção disponível para configurar um proxy para trabalhar com a ad AD Híbrida Azure. 

Se não utilizar o WPAD, pode configurar as definições de procuração winHTTP no seu computador a partir do Windows 10 1709. Para obter mais informações, consulte [as Definições de Procuração winHTTP implementadas pela GPO](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Se configurar as definições de procuração no seu computador utilizando as definições winHTTP, quaisquer computadores que não possam ligar-se ao proxy configurado não conseguirão ligar-se à internet.

Se a sua organização necessitar de acesso à internet através de um representante de saída autenticado, certifique-se de que os seus computadores Windows 10 podem autenticar com sucesso o representante de saída. Como os computadores windows 10 executam o registo do dispositivo utilizando o contexto da máquina, configuram a autenticação por procuração de saída utilizando o contexto da máquina. Consulte o seu fornecedor de proxy de saída sobre os requisitos de configuração.

Verifique se o dispositivo pode aceder aos recursos da Microsoft acima, utilizando o script [de conectividade de registo do dispositivo de teste.](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/)

## <a name="configure-hybrid-azure-ad-join"></a>Configurar a associação ao Azure AD híbrido

Para configurar um AD híbrido Azure junta-se usando Azure AD Connect:

1. Inicie o Azure AD Connect e, em seguida, selecione **Configure**.

1. Em **tarefas adicionais**, selecione **opções de configuração** e, em seguida, selecione **Seguinte**.

   ![Tarefas adicionais](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. Em **Visão Geral**, selecione **Next**.

1. Em **Connect to Azure AD,** insira as credenciais de um administrador global para o seu inquilino AZure AD.  

1. Nas **opções do Dispositivo**, selecione **Configure Hybrid Azure AD ,** e, em seguida, selecione **Next**.

   ![Opções do dispositivo](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. Nos **sistemas operativos do Dispositivo**, selecione os sistemas operativos que os dispositivos no ambiente do seu Diretório Ativo utilizam e, em seguida, selecione **Next**.

   ![Sistema operativo do dispositivo](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. Na **configuração SCP**, para cada floresta onde deseja Azure AD Connect para configurar o SCP, complete os seguintes passos e, em seguida, selecione **Next**.

   1. Selecione a **Floresta**.
   1. Selecione um **serviço de autenticação.**
   1. **Selecione Adicionar** para introduzir as credenciais de administrador da empresa.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. Em **Pronto para configurar,** selecione **Configure**.

1. Na **configuração completa**, selecione **Exit**.

## <a name="enable-windows-down-level-devices"></a>Permitir dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos unidos pelo domínio forem dispositivos de nível inferior do Windows, deve:

- Configurar as definições de intranet local para o registo de dispositivos
- Configurar SSO sem costura
- Instale o Microsoft Workplace Join para computadores de nível inferior do Windows

> [!NOTE]
> O suporte do Windows 7 terminou a 14 de janeiro de 2020. Para mais informações, consulte [o suporte do Windows 7 terminado.](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020)

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurar as definições de intranet local para o registo de dispositivos

Para completar a ad AD híbrida adere aos seus dispositivos de nível baixo do Windows e para evitar pedidos de certificado quando os dispositivos autenticam a AD AZure, pode empurrar uma política para os seus dispositivos unidos pelo domínio para adicionar os seguintes URLs à zona intranet local no Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Também deve **ativar Permitir atualizações à barra de estado através** do script na zona intranet local do utilizador.

### <a name="configure-seamless-sso"></a>Configurar SSO sem costura

Para completar o AD híbrido Azure adere aos seus dispositivos de nível de baixo do Windows num domínio gerido que utilize sincronização de [haxixe](../hybrid/whatis-phs.md) de [palavra-passe](../hybrid/how-to-connect-pta.md) ou autenticação de passagem como método de autenticação em nuvem AD Azure, também deve [configurar SSO sem costura.](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Instale o Microsoft Workplace Join para computadores de nível inferior do Windows

Para registar dispositivos de nível inferior do Windows, as organizações devem instalar [o Microsoft Workplace Join para computadores não Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join para computadores não Windows 10 está disponível no Microsoft Download Center.

Pode implementar o pacote utilizando um sistema de distribuição de software como [o Microsoft Endpoint Configuration Manager](/configmgr/). O pacote suporta as opções de instalação silenciosa padrão com o `quiet` parâmetro. A versão atual do Configuration Manager oferece benefícios em versões anteriores, como a capacidade de rastrear as inscrições concluídas.

O instalador cria uma tarefa programada no sistema que funciona no contexto do utilizador. A tarefa é ativada quando o utilizador entra no Windows. A tarefa junta-se silenciosamente ao dispositivo com a Azure AD utilizando as credenciais do utilizador depois de autenticar com Azure AD.

## <a name="verify-the-registration"></a>Verificar o registo

Aqui estão 3 formas de localizar e verificar o estado do dispositivo:

### <a name="locally-on-the-device"></a>Localmente no dispositivo

1. Abra o Windows PowerShell.
2. Introduza `dsregcmd /status`.
3. Verifique se tanto **o AzureAdJoined** como **o DomainJoined** estão definidos para **SIM**.
4. Pode utilizar o **DeviceId** e comparar o estado do serviço utilizando o portal Azure ou o PowerShell.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

1. Aceda à página dos dispositivos utilizando um [link direto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Informações sobre como localizar um dispositivo podem ser encontradas em [Como gerir as identidades do dispositivo utilizando o portal Azure](./device-management-azure-portal.md).
3. Se a coluna **Registada** **disser Pendente,** então a Hybrid Azure AD Join não está concluída.
4. Se a coluna **Registada** contiver uma **data/hora,** então a Hybrid Azure AD Join foi concluída.

### <a name="using-powershell"></a>Com o PowerShell

Verifique o estado de registo do dispositivo no seu inquilino Azure utilizando **[a Get-MsolDevice](/powershell/module/msonline/get-msoldevice)**. Este cmdlet está no [módulo PowerShell do Diretório Ativo Azure](/powershell/azure/active-directory/install-msonlinev1).

Quando utilizar o **cmdlet Get-MSolDevice** para verificar os detalhes do serviço:

- Deve existir um objeto com o **ID** do dispositivo que corresponda ao ID do cliente Windows.
- O valor para **DeviceTrustType** é **o Domínio Unido**. Esta definição é equivalente ao **AD Híbrido Azure aderiu ao** estado na página **dispositivos** no portal AD Azure.
- Para dispositivos utilizados no Acesso Condicional, o valor para **Ativado** é **Verdadeiro** e **o DeviceTrustLevel** é **Gerido**.

1. Abra o Windows PowerShell Como um administrador.
2. Entre `Connect-MsolService` para ligar ao seu inquilino Azure.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Conte todos os dispositivos híbridos Azure AD (excluindo estado **pendente)**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Conte todos os AD híbridos Ad AD unidos com estado **pendente**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Listar todos os dispositivos híbridos Azure AD

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Listar todos os dispositivos híbridos Azure AD com estado **pendente**

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Listar detalhes de um único dispositivo:

1. `get-msoldevice -deviceId <deviceId>`Insira (Este é o **DeviceId** obtido localmente no dispositivo).
2. Certifique-se de que **Ativado** está definido como **Verdadeiro**.

## <a name="troubleshoot-your-implementation"></a>Resolver problemas relacionados com a implementação

Se sentir problemas em completar a ad AD híbrida para dispositivos Windows unidos pelo domínio, consulte:

- [Dispositivos de resolução de problemas utilizando comando dsregcmd](./troubleshoot-device-dsregcmd.md)
- [Resolver problemas de dispositivos associados ao Azure Active Directory híbrido](troubleshoot-hybrid-join-windows-current.md)
- [O Azure Ative Directory híbrido de resolução de problemas juntou-se a dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a gerir as identidades dos dispositivos utilizando o portal Azure.
> [!div class="nextstepaction"]
> [Gerir identidades do dispositivo](device-management-azure-portal.md)
