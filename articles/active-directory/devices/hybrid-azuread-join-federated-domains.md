---
title: Configure hybrid Azure Ative Directory junta-se a domínios federados [ Microsoft Docs
description: Saiba como configurar o Diretório Ativo Azure híbrido para domínios federados.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46bb3517af31e328efae89afef8f3e83ccbc8bfa
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83778753"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Tutorial: Configurar a associação do Azure Active Directory híbrido para domínios federados

Tal como um utilizador na sua organização, um dispositivo é uma identidade central que pretende proteger. Pode usar a identidade de um dispositivo para proteger os seus recursos a qualquer momento e de qualquer local. Pode atingir este objetivo trazendo identidades de dispositivos e gerindo-as no Azure Ative Directory (Azure AD) utilizando um dos seguintes métodos:

- Associação ao Azure AD
- Associação ao Azure AD Híbrido
- Registo do Azure AD

Trazer os seus dispositivos para o Azure AD maximiza a produtividade dos utilizadores através de um único sinal (SSO) através da sua nuvem e recursos no local. Pode garantir acesso à sua nuvem e recursos no local com [Acesso Condicional](../conditional-access/howto-conditional-access-policy-compliant-device.md) ao mesmo tempo.

Um ambiente federado deve ter um fornecedor de identidade que suporte os seguintes requisitos. Se tiver um ambiente federado utilizando serviços da Federação de Diretórios Ativos (AD FS), então os requisitos abaixo já são suportados.

- **WiAorMULTIAUTHN reivindica:** Esta alegação é necessária para fazer a adesão híbrida azure para dispositivos de nível inferior Windows.
- **Protocolo WS-Trust:** Este protocolo é necessário para autenticar os dispositivos ad sinuosos do Windows atual ida e voltado com a AD Azure.
  Quando estiver a utilizar AD FS, tem de ativar os seguintes pontos finais wS-Trust:`/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Tanto as **adfs/services/trust/2005/windowstransport** e **adfs/services/trust/13/windowstransport** devem ser ativadas apenas como pontos finais intranet e NÃO devem ser expostas como pontos finais virados para a extranet através do Proxy de Aplicação Web. Para saber mais sobre como desativar os pontos finais do WS-Trust Windows, consulte [desativar os pontos finais do WS-Trust Windows no proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Pode ver quais os pontos finais ativados através **Service**da consola de gestão AD FS em  >  **pontos finais**de serviço .

Neste tutorial, você aprende a configurar o Híbrido Azure AD juntar-se a dispositivos de computadores ligados ao domínio Ative Directory em um ambiente federado usando AD FS.

Saiba como:

> [!div class="checklist"]
> * Configurar a associação ao Azure AD híbrido
> * Ativar dispositivos de nível inferior windows
> * Verificar o registo
> * Resolução de problemas

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial assume que está familiarizado com estes artigos:

- [O que é uma identidade de dispositivo?](overview.md)
- [Como planear a implementação do seu Azure Hybrid](hybrid-azuread-join-plan.md)
- [Como fazer a validação controlada do Híbrido Azure AD aderir](hybrid-azuread-join-control.md)

Para configurar o cenário neste tutorial, precisa do:

- Windows Server 2012 R2 com o AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) versão 1.1.819.0 ou posterior

Começando com a versão 1.1.819.0, o Azure AD Connect inclui um assistente que pode utilizar para configurar a adesão híbrida do Azure AD. O assistente simplifica significativamente o processo de configuração. O assistente relacionado:

- Configura os pontos de ligação de serviço (SCPs) para o registo do dispositivo
- Faz uma cópia segurança da fidedignidade da entidade confiadora do Azure AD existente
- Atualiza as regras de afirmação na confiança do Azure AD

Os passos de configuração deste artigo baseiam-se na utilização do assistente Azure AD Connect. Se tiver uma versão anterior do Azure AD Connect instalada, tem de atualizá-la para 1.1.819 ou mais tarde para utilizar o assistente. Se instalar a versão mais recente do Azure AD Connect não é uma opção para si, consulte [como configurar manualmente a adesão](hybrid-azuread-join-manual.md)híbrida azure .

A adesão à Hybrid Azure AD requer que os dispositivos tenham acesso aos seguintes recursos da Microsoft a partir da rede da sua organização:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Serviço de Token de Segurança da sua organização (STS) (Para domínios federados)
- `https://autologon.microsoftazuread-sso.com`(Se utilizar ou planeia utilizar SSO sem emenda)

Começando pelo Windows 10 1803, se o híbrido instantâneo Azure AD se juntar a um ambiente federado usando AD FS falha, contamos com o Azure AD Connect para sincronizar o objeto de computador em Azure AD que é posteriormente usado para completar o registo do dispositivo para a adesão híbrida azure AD. Verifique se o Azure AD Connect sincronizou os objetos informáticos dos dispositivos que pretende ser híbrido Azure AD juntou-se ao Azure AD. Se os objetos do computador pertencerem a unidades organizacionais específicas (OUs), também deve configurar as OUs para sincronizar em Azure AD Connect. Para saber mais sobre como sincronizar objetos de computador utilizando o Azure AD Connect, consulte a [filtragem do Configure utilizando o Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Se a sua organização necessitar de acesso à internet através de um proxy de saída, a Microsoft recomenda a implementação do [Web Proxy Auto-Discovery (WPAD)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) para ativar os computadores do Windows 10 para o registo do dispositivo com a AD Azure. Se encontrar problemas de configuração e gestão do WPAD, consulte a [deteção automática de Troubleshoot](/previous-versions/tn-archive/cc302643(v=technet.10)). 

Se não utilizar o WPAD e pretender configurar as definições de procuração no seu computador, pode fazê-lo a partir do Windows 10 1709. Para mais informações, consulte as definições do Configure WinHTTP utilizando um objeto de política de [grupo (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Se configurar as definições de procuração no seu computador utilizando as definições do WinHTTP, quaisquer computadores que não consigam ligar-se ao proxy configurado não conseguirão ligar-se à internet.

Se a sua organização necessitar de acesso à internet através de um proxy de saída autenticado, deve certificar-se de que os seus computadores Windows 10 podem autenticar com sucesso o proxy de saída. Uma vez que os computadores do Windows 10 executam o registo do dispositivo utilizando o contexto da máquina, é necessário configurar a autenticação por procuração de saída utilizando o contexto da máquina. Consulte o seu fornecedor de proxy de saída sobre os requisitos de configuração.

Para verificar se o dispositivo é capaz de aceder aos recursos da Microsoft acima na conta do sistema, pode utilizar o script de conectividade de registo de [dispositivos de teste.](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)

## <a name="configure-hybrid-azure-ad-join"></a>Configurar a associação ao Azure AD híbrido

Para configurar um Azure AD híbrido, junte-se utilizando o Azure AD Connect, precisa de:

- As credenciais de um administrador global para o seu inquilino Da AD Azure  
- As credenciais do administrador da empresa para cada uma das florestas
- As credenciais do seu administrador da AD FS

**Para configurar um Azure AD híbrido, junte-se utilizando o Azure AD Connect:**

1. Inicie a Ligação AD Azure e, em seguida, **selecione Configurar**.

   ![Bem-vindo](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na página de **tarefas Adicionais,** **selecione configurar as opções**do dispositivo , e, em seguida, selecione **Next**.

   ![Tarefas adicionais](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na página **'Visão Geral',** selecione **Next**.

   ![Descrição geral](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na página **De AD Connect to Azure,** introduza as credenciais de um administrador global para o seu inquilino Azure AD e, em seguida, selecione **Next**.

   ![Ligar ao Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na página opções do **Dispositivo,** selecione **Configure Hybrid Azure AD join**, e, em seguida, selecione **Next**.

   ![Opções do dispositivo](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na página **SCP,** complete os seguintes passos e, em seguida, selecione **Seguinte:**

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Selecione a floresta.
   1. Selecione o serviço de autenticação. Deve selecionar **o servidor AD FS** a menos que a sua organização tenha exclusivamente clientes do Windows 10 e tenha configurado a sincronização de computador/dispositivo, ou a sua organização utilize SSO sem emenda.
   1. Selecione **Adicionar** para introduzir as credenciais do administrador da empresa.

1. Na página dos **sistemas operativos do Dispositivo,** selecione os sistemas operativos que os dispositivos do seu ambiente de Diretório Ativo utilizam e, em seguida, selecione **Next**.

   ![Sistema operativo do dispositivo](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na página de configuração da **Federação,** introduza as credenciais do seu administrador AD FS e, em seguida, selecione **Next**.

   ![Configuração de federação](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na página **Ready to configure,** selecione **Configure**.

   ![Preparado para configurar](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na página completa da **Configuração,** selecione **Saída**.

   ![Configuração completa](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Ativar dispositivos de nível inferior windows

Se alguns dos seus dispositivos unidos pelo domínio forem dispositivos de nível inferior do Windows, deve:

- Configurar as definições de intranet local para o registo de dispositivos
- Instale a Microsoft Workplace Join para computadores de nível inferior windows

> [!NOTE]
> O suporte do Windows 7 terminou a 14 de janeiro de 2020. Para mais informações, o [Suporte para o Windows 7 terminou](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurar as definições de intranet local para o registo de dispositivos

Para completar com sucesso a adesão híbrida do Azure AD aos seus dispositivos de nível inferior windows e para evitar solicitações de certificadoquando os dispositivos autenticarem o Azure AD, pode empurrar uma política para os seus dispositivos unidos pelo domínio para adicionar os seguintes URLs à zona intranet local no Internet Explorer:

- `https://device.login.microsoftonline.com`
- STS da sua organização (para domínios federados)
- `https://autologon.microsoftazuread-sso.com`(Para SSO sem emenda)

Também deve ativar **as atualizações para** a barra de estado através do script na zona intranet local do utilizador.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Instale a Microsoft Workplace Join para computadores de nível inferior windows

Para registar dispositivos de nível inferior do Windows, as organizações devem instalar o [Microsoft Workplace Join para computadores não Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join para computadores não Windows 10 está disponível no Microsoft Download Center.

Pode implementar o pacote utilizando um sistema de distribuição de software como o [Microsoft Endpoint Configuration Manager](/configmgr/). O pacote suporta as opções de instalação silenciosa padrão com o `quiet` parâmetro. O atual ramo do Gestor de Configuração oferece benefícios em versões anteriores, como a capacidade de rastrear registos concluídos.

O instalador cria uma tarefa programada no sistema que funciona no contexto do utilizador. A tarefa é desencadeada quando o utilizador faz o sinal de si no Windows. A tarefa junta-se silenciosamente ao dispositivo com a AD Azure utilizando as credenciais do utilizador depois de autenticar com a AD Azure.

## <a name="verify-the-registration"></a>Verificar o registo

Aqui estão 3 formas de localizar e verificar o estado do dispositivo:

### <a name="locally-on-the-device"></a>Localmente no dispositivo

1. Abra o Windows PowerShell.
2. Introduza `dsregcmd /status`.
3. Verifique se tanto **azureAdJoined** como **DomainJoined** estão definidos para **SIM**.
4. Pode utilizar o **DeviceId** e comparar o estado do serviço utilizando o portal Azure ou o PowerShell.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

1. Vá para a página dos dispositivos usando um [link direto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Informações sobre como localizar um dispositivo podem ser encontradas em Como gerir identidades do [dispositivo usando o portal Azure](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#locate-devices).
3. Se a coluna **Registada** diz **Pendente,** então a Hybrid Azure AD Join ainda não está concluída. Em ambientes federados, isso só pode acontecer se não se registar e a ligação AAD estiver configurada para sincronizar os dispositivos.
4. Se a coluna **Registada** contiver uma **data/hora,** então a Hybrid Azure AD Join tiver concluído.

### <a name="using-powershell"></a>Com o PowerShell

Verifique o estado de registo do dispositivo no seu inquilino Azure utilizando o **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)**. Este cmdlet está no módulo PowerShell do [Diretório Ativo Azure](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Quando utilizar o cmdlet **Get-MSolDevice** para verificar os dados do serviço:

- Deve existir um objeto com o ID do **dispositivo** que corresponda ao ID do cliente Windows.
- O valor para **dispositivoTrustType** é **União de Domínios**. Esta configuração é equivalente ao estado **adesado** do Hybrid Azure na página **Dispositivos** do portal Azure AD.
- Para dispositivos utilizados no Acesso Condicional, o valor para **Ativado** é **Verdadeiro** e **o DeviceTrustLevel** é **gerido**.

1. Abra o Windows PowerShell Como um administrador.
2. Entre `Connect-MsolService` para ligar ao seu inquilino Azure.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Conte todos os dispositivos adatos Híbridos Azure (excluindo estado **pendente)**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Conte todos os Dispositivos Híbridos Azure AD com estado **pendente**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Lista de todos os dispositivos ad-ad híbridos Azure

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Lista de todos os Dispositivos Híbridos Azure AD com Estado **Pendente**

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Enumerar detalhes de um único dispositivo:

1. Introduza `get-msoldevice -deviceId <deviceId>` (este é o **Id do dispositivo** obtido localmente no dispositivo).
2. Certifique-se de que **Ativado** está definido como **Verdadeiro**.

## <a name="troubleshoot-your-implementation"></a>Resolver problemas relacionados com a implementação

Se tiver problemas com a conclusão do Hybrid Azure AD, junte-se a dispositivos Windows unidos pelo domínio, consulte:

- [Dispositivos de resolução de problemas utilizando comando dsregcmd](https://docs.microsoft.com/azure/active-directory/devices/troubleshoot-device-dsregcmd)
- [AD híbrido Desfilma Azure junta-se aos dispositivos atuais do Windows](troubleshoot-hybrid-join-windows-current.md)
- [Troubleshoot hybrid Azure AD juntam-se para dispositivos de nível inferior windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passos seguintes

Aprenda a [gerir as identidades do dispositivo utilizando o portal Azure](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
