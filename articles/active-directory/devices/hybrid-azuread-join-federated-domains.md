---
title: Configure híbrido Azure Ative Directy junta-se a domínios federados Microsoft Docs
description: Saiba como configurar o Azure Ative Directory híbrido para domínios federados.
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
ms.openlocfilehash: cc2f7d3ce5f8329038fea4ecbb5242015fb3fd0d
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860138"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Tutorial: Configurar a associação do Azure Active Directory híbrido para domínios federados

Como um utilizador na sua organização, um dispositivo é uma identidade central que quer proteger. Pode utilizar a identidade de um dispositivo para proteger os seus recursos a qualquer momento e a partir de qualquer local. Pode atingir este objetivo trazendo identidades de dispositivos e gerindo-as no Azure Ative Directory (Azure AD) utilizando um dos seguintes métodos:

- Associação ao Azure AD
- Associação ao Azure AD Híbrido
- Registo do Azure AD

Trazer os seus dispositivos para a Azure AD maximiza a produtividade do utilizador através de um único sign-on (SSO) através da sua nuvem e recursos no local. Pode garantir o acesso à sua nuvem e recursos no local com [acesso condicional](../conditional-access/howto-conditional-access-policy-compliant-device.md) ao mesmo tempo.

Um ambiente federado deve ter um fornecedor de identidade que apoie os seguintes requisitos. Se tiver um ambiente federado utilizando os Serviços da Federação de Diretórios Ativos (FS AD), então os requisitos abaixo já estão suportados.

- **WIAORMULTIAUTHN reivindicação:** Esta alegação é necessária para fazer a junção híbrida Azure AD para dispositivos de nível baixo do Windows.
- **Protocolo WS-Trust:** Este protocolo é necessário para autenticar dispositivos híbridos Azure AD atuais do Windows com Azure AD.
  Quando estiver a utilizar o AD FS, tem de ativar os seguintes pontos finais WS-Trust: `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Tanto **adfs/serviços/trust/2005/windowstransport** e **adfs/services/trust/13/windowstransport** devem ser ativados apenas como pontos finais virados para a intranet e NÃO devem ser expostos como pontos finais virados para a extranet através do Proxy da Aplicação Web. Para saber mais sobre como desativar WS-Trust pontos finais do Windows, consulte [pontos finais do Windows desativar WS-Trust no proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Pode ver quais os pontos finais que estão ativados através da consola de gestão AD FS em **Service**  >  **Endpoints**.

Neste tutorial, aprende-se a configurar a ad AD híbrida adere a dispositivos de computadores de domínio ativo num ambiente federado utilizando AD FS.

Saiba como:

> [!div class="checklist"]
> * Configurar a associação ao Azure AD híbrido
> * Ativar dispositivos de nível de downlevel do Windows
> * Verificar o registo
> * Resolução de Problemas

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que está familiarizado com estes artigos:

- [O que é um ID do dispositivo?](overview.md)
- [Como planear a sua AD híbrida Azure aderir à implementação](hybrid-azuread-join-plan.md)
- [Como fazer validação controlada da azure híbrida aderir](hybrid-azuread-join-control.md)

Para configurar o cenário neste tutorial, precisa do:

- Windows Server 2012 R2 com o AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) versão 1.1.819.0 ou mais tarde

Começando pela versão 1.1.819.0, o Azure AD Connect inclui um assistente que pode utilizar para configurar a ad AD híbrida. O assistente simplifica significativamente o processo de configuração. O assistente relacionado:

- Configura os pontos de ligação de serviço (SCPs) para o registo do dispositivo
- Faz uma cópia segurança da fidedignidade da entidade confiadora do Azure AD existente
- Atualiza as regras de afirmação na confiança do Azure AD

Os passos de configuração deste artigo baseiam-se na utilização do assistente Azure AD Connect. Se tiver uma versão anterior do Azure AD Connect instalada, tem de atualizá-la para 1.1.819 ou mais tarde para utilizar o assistente. Se instalar a versão mais recente do Azure AD Connect não é uma opção para si, veja [como configurar manualmente a ad AD híbrida](hybrid-azuread-join-manual.md)híbrida .

A adesão híbrida AD AD requer que os dispositivos tenham acesso aos seguintes recursos da Microsoft a partir da rede da sua organização:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Serviço de Token de Segurança (STS) da sua organização (Para domínios federados)
- `https://autologon.microsoftazuread-sso.com` (Se utilizar ou pretender utilizar SSO sem costura)

> [!WARNING]
> Se a sua organização utilizar servidores proxy que intercetam tráfego SSL para cenários como prevenção de perda de dados ou restrições de inquilinos AZure AD, certifique-se de que o tráfego para https://device.login.microsoftonline.com ' ' é excluído do break-and-inspect TLS. A não exclusão https://device.login.microsoftonline.com pode causar interferência na autenticação do certificado do cliente, causando problemas com o registo do dispositivo e o acesso condicional baseado no dispositivo.

Começando pelo Windows 10 1803, se o híbrido instantâneo Azure AD se unir para um ambiente federado usando AD FS falha, contamos com Azure AD Connect para sincronizar o objeto do computador em Azure AD que é posteriormente usado para completar o registo do dispositivo para a ad AD híbrida Azure. Verifique se o Azure AD Connect sincronizou os objetos de computador dos dispositivos que pretende ser híbrido Azure AD ligados ao Azure AD. Se os objetos do computador pertencerem a unidades organizacionais específicas (OUs), também deve configurar as OUs para sincronizar no Azure AD Connect. Para saber mais sobre como sincronizar objetos de computador utilizando o Azure AD Connect, consulte [a filtragem Configure utilizando o Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Se a sua organização necessitar de acesso à internet através de um proxy de saída, a Microsoft recomenda [a implementação da Web Proxy Auto-Discovery (WPAD)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) para permitir o registo de computadores do Windows 10 com Azure AD. Se encontrar problemas que configuram e gerem o WPAD, consulte [a deteção automática de Resolução de Problemas](/previous-versions/tn-archive/cc302643(v=technet.10)). 

Se não utilizar o WPAD e quiser configurar as definições de procuração no seu computador, pode fazê-lo a partir do Windows 10 1709. Para obter mais informações, consulte [as definições de Configure WinHTTP utilizando um objeto de política de grupo (GPO)](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Se configurar as definições de procuração no seu computador utilizando as definições winHTTP, quaisquer computadores que não possam ligar-se ao proxy configurado não conseguirão ligar-se à internet.

Se a sua organização necessitar de acesso à internet através de um representante de saída autenticado, deve certificar-se de que os seus computadores Windows 10 podem autenticar com sucesso o representante de saída. Uma vez que os computadores windows 10 executam o registo do dispositivo utilizando o contexto da máquina, tem de configurar a autenticação por procuração de saída utilizando o contexto da máquina. Consulte o seu fornecedor de proxy de saída sobre os requisitos de configuração.

Para verificar se o dispositivo é capaz de aceder aos recursos acima da Microsoft sob a conta do sistema, pode utilizar o script [de Conectividade de Registo do Dispositivo de Teste.](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)

## <a name="configure-hybrid-azure-ad-join"></a>Configurar a associação ao Azure AD híbrido

Para configurar uma AD híbrida Azure juntando-se usando Azure AD Connect, você precisa:

- As credenciais de um administrador global para o seu inquilino AZure AD  
- As credenciais do administrador da empresa para cada uma das florestas
- As credenciais do seu administrador da AD FS

**Para configurar um AD híbrido Azure junta-se usando Azure AD Connect**:

1. Inicie o Azure AD Connect e, em seguida, selecione **Configure**.

   ![Bem-vindo](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na página **de tarefas adicionais,** selecione **opções do dispositivo configurar** e, em seguida, selecione **Seguinte**.

   ![Tarefas adicionais](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na página **'Vista Geral',** selecione **Seguinte**.

   ![Descrição geral](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na página **Connect to Azure AD,** insira as credenciais de um administrador global para o seu inquilino AD Azure e, em seguida, selecione **Next**.

   ![Ligar ao Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na página de opções do **Dispositivo,** selecione **Configure Hybrid Azure AD ,** e, em seguida, selecione **Next**.

   ![Opções do dispositivo](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na página **SCP,** complete os seguintes passos e, em seguida, selecione **Seguinte**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Selecione a floresta.
   1. Selecione o serviço de autenticação. Tem de selecionar **o servidor AD FS,** a menos que a sua organização tenha exclusivamente clientes windows 10 e tenha sincronizado computador/dispositivo configurado, ou a sua organização utilize SSO sem costura.
   1. **Selecione Adicionar** para introduzir as credenciais de administrador da empresa.

1. Na página de **sistemas operativos do Dispositivo,** selecione os sistemas operativos que os dispositivos do ambiente ative directory utilizam e, em seguida, selecione **Next**.

   ![Sistema operativo do dispositivo](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na página de configuração da **Federação,** insira as credenciais do administrador da AD FS e, em seguida, selecione **Seguinte**.

   ![Configuração de federação](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na página **Pronto para configurar,** selecione **Configurar**.

   ![Preparado para configurar](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na página completa da **Configuração,** selecione **Sair**.

   ![Configuração completa](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Ativar dispositivos de nível de downlevel do Windows

Se alguns dos seus dispositivos unidos pelo domínio forem dispositivos de nível de downlevel do Windows, deve:

- Configurar as definições de intranet local para o registo de dispositivos
- Instale o Microsoft Workplace Join para computadores de nível baixo do Windows

> [!NOTE]
> O suporte do Windows 7 terminou a 14 de janeiro de 2020. Para mais informações, [o Suporte ao Windows 7 terminou.](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurar as definições de intranet local para o registo de dispositivos

Para completar com sucesso a ad AD híbrida híbrida dos seus dispositivos de nível de downlevel windows e para evitar pedidos de certificado quando os dispositivos autenticam a AZure AD, pode empurrar uma política para os seus dispositivos unidos pelo domínio para adicionar os seguintes URLs à zona intranet local no Internet Explorer:

- `https://device.login.microsoftonline.com`
- STS da sua organização (Para domínios federados)
- `https://autologon.microsoftazuread-sso.com` (para SSO sem costura)

Também deve **ativar Permitir atualizações à barra de estado através** do script na zona intranet local do utilizador.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Instale o Microsoft Workplace Join para computadores de nível baixo do Windows

Para registar dispositivos de nível de down-vel do Windows, as organizações devem instalar [o Microsoft Workplace Join para computadores não Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join para computadores não Windows 10 está disponível no Microsoft Download Center.

Pode implementar o pacote utilizando um sistema de distribuição de software como [o Microsoft Endpoint Configuration Manager](/configmgr/). O pacote suporta as opções de instalação silenciosa padrão com o `quiet` parâmetro. O atual ramo do Gestor de Configuração oferece benefícios em versões anteriores, como a capacidade de rastrear as inscrições concluídas.

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
3. Se a coluna **Registada** **disser Pendente,** então a Hybrid Azure AD Join não está concluída. Em ambientes federados, isto só pode acontecer se não se registar e a ligação AAD estiver configurada para sincronizar os dispositivos.
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

Se sentir problemas com a conclusão da azure híbrida ad para dispositivos Windows ligados ao domínio, consulte:

- [Dispositivos de resolução de problemas utilizando comando dsregcmd](./troubleshoot-device-dsregcmd.md)
- [Híbrido Azure AD de resolução de problemas junta-se aos dispositivos atuais do Windows](troubleshoot-hybrid-join-windows-current.md)
- [Híbrido Azure AD de resolução de problemas junta-se a dispositivos de nível de downlevel do Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passos seguintes

Saiba como [gerir as identidades dos dispositivos utilizando o portal Azure](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
