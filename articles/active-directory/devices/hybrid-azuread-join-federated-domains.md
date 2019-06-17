---
title: Configurar a associação ao Azure Active Directory de híbrido para domínios federados | Documentos da Microsoft
description: Saiba como configurar a associação ao Azure Active Directory de híbrido para domínios federados.
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
ms.openlocfilehash: ea834a0fc1d92cc8d2326bd94dde2e0a983c90a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110748"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Tutorial: Configurar a associação ao Azure Active Directory de híbrido para domínios federados

De forma semelhante a um utilizador, um dispositivo é outra identidade de núcleos que pretende proteger e utilizá-la para proteger os seus recursos em qualquer altura e a partir de qualquer local. Pode realizar esse objetivo por género e a gestão de identidades de dispositivos no Azure AD através de um dos seguintes métodos:

- Associação ao Azure AD
- Associação ao Azure AD Híbrido
- Registo do Azure AD

Ao trazer os seus dispositivos para o Azure AD, maximiza a produtividade dos seus utilizadores através do início de sessão único (SSO) nos seus recursos na cloud e no local. Ao mesmo tempo, pode proteger o acesso aos seus recursos na cloud e no local com o [acesso condicional](../active-directory-conditional-access-azure-portal.md).

Neste tutorial, saiba como configurar a associação do Azure AD híbrido para dispositivos de computadores associados a um domínio do AD no ambiente federado através do AD FS.

> [!NOTE]
> Se seu ambiente federado estiver a utilizar um fornecedor de identidade que não sejam do AD FS, terá de se certificar de que o fornecedor de identidade suporta o protocolo WS-Trust. WS-Trust é necessário para autenticar seu Windows atual híbrida do Azure AD associado a um dispositivos com o Azure AD. Além disso, se tiver dispositivos que precisa de associação do Azure AD híbrido de nível inferior do Windows, o fornecedor de identidade irá necessitar para suportar afirmações WIAORMULTIAUTHN. 


> [!div class="checklist"]
> * Configurar a associação ao Azure AD híbrido
> * Permitir dispositivos de nível inferior do Windows
> * Verificar o registo
> * Resolução de problemas

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial parte do princípio de que está familiarizado com:

- [Introdução à gestão de identidade de dispositivo no Azure Active Directory](../device-management-introduction.md)
- [Como planear a sua implementação associada híbrida do Azure Active Directory](hybrid-azuread-join-plan.md)
- [Como fazer a validação controlada de associação do Azure AD híbrido](hybrid-azuread-join-control.md)

Para configurar o cenário neste tutorial, precisa do:

- Windows Server 2012 R2 com o AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) versão 1.1.819.0 ou superior.

A partir da versão 1.1.819.0, o Azure AD Connect fornece um assistente para configurar a associação do Azure AD híbrido. O assistente permite-lhe simplificar significativamente o processo de configuração. O assistente relacionado:

- Configura os pontos de ligação de serviço (SCP) para o registo de dispositivos
- Faz uma cópia segurança da fidedignidade da entidade confiadora do Azure AD existente
- Atualiza as regras de afirmação na confiança do Azure AD

Os passos de configuração neste artigo baseiam-se neste assistente. Se tiver uma versão mais antiga do Azure AD Connect instalada, tem de atualizá-lo para a versão 1.1.819 ou superior. Se instalar a versão mais recente do Azure AD Connect não é uma opção para si, veja [como configurar manualmente a associação ao Azure AD híbrido](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual).

A associação do Azure AD híbrido requer que os dispositivos tenham acesso aos seguintes recursos da Microsoft a partir da rede da sua organização:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- O STS da sua organização (domínios federados)
- `https://autologon.microsoftazuread-sso.com` (Se estiver a utilizar ou planear utilizar o SSO totalmente integrado)

Começando com o Windows 10 versão 1803 o, se falhar a associação do Azure AD híbrido instantânea para o ambiente federado através do AD FS, Contamos com o Azure AD Connect para sincronizar o objeto de computador no Azure AD que, em seguida, é utilizado para concluir o registo do dispositivo para o Azure AD híbrido Junte-se. Certifique-se de que o Azure AD Connect sincronizou os objetos de computador dos dispositivos que pretende que sejam associados ao Azure AD híbrido para o Azure AD. Se os objetos de computador pertencerem a unidades organizacionais (UO) específicas, essas UOs também têm de ser configuradas para sincronização no Azure AD Connect. Para obter mais informações sobre como sincronizar objetos de computador com o Azure AD Connect, consulte o artigo sobre [configurar a filtragem com o Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering).

Se sua organização necessita de acesso à Internet através de um proxy de saída, a Microsoft recomenda [implementando Web Proxy Auto-Discovery (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) para permitir que os computadores Windows 10 fazer o registo de dispositivos com o Azure AD. Se tiver problemas com a com a configuração e gestão WPAD, aceda a [resolução de problemas de deteção automática](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Se não estiver a utilizar o WPAD e tem de configurar as definições de proxy no seu computador, pode fazer por isso, a partir do Windows 10 1709, por [configurar definições de WinHTTP usando um objeto de política de grupo (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Se configurar as definições de proxy no seu computador usando as configurações de WinHTTP, todos os computadores que não é possível ligar para o proxy configurado irão falhar ligar à internet.

Se sua organização precisar de acesso à Internet através de um proxy de saída autenticado, tem de se certificar de que os seus computadores com Windows 10 podem ser autenticados com êxito no proxy de saída. Uma vez que os computadores com Windows 10 executam o registo de dispositivos utilizando o contexto da máquina, é necessário configurar a autenticação de proxy de saída utilizando o contexto da máquina. Consulte o seu fornecedor de proxy de saída sobre os requisitos de configuração.

## <a name="configure-hybrid-azure-ad-join"></a>Configurar a associação ao Azure AD híbrido

Para configurar uma associação doi Azure AD híbrido com o Azure AD Connect, precisa das:

- Credenciais de um administrador global para o inquilino do Azure AD.  
- As credenciais de administrador da empresa para cada uma das florestas.
- Credenciais do seu administrador do AD FS.

**Para configurar uma associação do Azure AD híbrido com o Azure AD Connect:**

1. Inicie o Azure AD Connect e, em seguida, clique em **Configurar**.

   ![Bem-vindo](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na página **Tarefas adicionais**, selecione **Configurar opções do dispositivo** e clique em **Seguinte**.

   ![Tarefas adicionais](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na página **Descrição geral**, clique em **Seguinte**.

   ![Descrição geral](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na página **Ligar ao Azure AD**, introduza as credenciais de um administrador global para o inquilino do Azure AD e clique em **Seguinte**.

   ![Ligar ao Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na página **Opções do dispositivo**, selecione **Configurar a associação ao Azure AD Híbrido** e, em seguida, clique em **Seguinte**.

   ![Opções do dispositivo](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na página **SCP**, execute os passos seguintes e clique em **Seguinte**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Selecione a floresta.
   1. Selecione o serviço de autenticação. Tem de selecionar o servidor do AD FS, a menos que a sua organização tenha exclusivamente clientes do Windows 10 e tiver configurado a sincronização do computador/dispositivo ou a sua organização esteja a utilizar SeamlessSSO.
   1. Clique em **Adicionar** para introduzir as credenciais de administrador da empresa.

1. Na página **Sistemas operativos dos dispositivos**, selecione os sistemas operativos utilizados pelos dispositivos no seu ambiente do Active Directory e, em seguida, clique em **Seguinte**.

   ![Sistema operativo do dispositivo](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na página **Configuração de federação**, introduza as credenciais do administrador do AD FS e clique em **Seguinte**.

   ![Configuração de federação](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na página **Preparado para configurar**, clique em **Configurar**.

   ![Preparado para configurar](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na página **Configuração completa**, clique em **Sair**.

   ![Configuração completa](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>Permitir dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos associados a um domínio são dispositivos de nível inferior do Windows, terá de:

- Configurar as definições de intranet local para o registo de dispositivos
- instalar computadores de nível inferior da Microsoft à área de trabalho Junte-se para Windows

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurar as definições de intranet local para o registo de dispositivos

Para concluir com êxito a associação ao Azure AD híbrido dos seus dispositivos de nível inferior do Windows e para evitar avisos de certificado quando os dispositivos são autenticados no Azure AD, pode aplicar uma política aos seus dispositivos associados a um domínio para adicionar os seguintes URLs à zona de intranet local no Internet Explorer:

- `https://device.login.microsoftonline.com`
- O Serviço de Tokens de Segurança (STS - domínios federados) da sua organização
- `https://autologon.microsoftazuread-sso.com` (para SSO totalmente integrado).

Além disso, tem de ativar **Permitir atualizações à barra de estado por meio de script** na zona de intranet local do utilizador.

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>instalar computadores de nível inferior da Microsoft à área de trabalho Junte-se para Windows

Para registar dispositivos de nível inferior do Windows, tem de instalar as organizações [Microsoft Workplace Join para computadores Windows de 10](https://www.microsoft.com/download/details.aspx?id=53554) disponível no Microsoft Download Center.

Pode implementar o pacote com um sistema de distribuição de software, como [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). O pacote suporta as opções de instalação silenciosa padrão com o parâmetro silencioso. O current branch do Configuration Manager oferece vantagens significativas em relação às versões anteriores, como a capacidade de controlar registos concluídos.

O instalador cria uma tarefa agendada no sistema que é executado no contexto de utilizador. A tarefa é acionada quando o usuário faz um início de sessão no Windows. A tarefa silenciosamente associa o dispositivo com o Azure AD com as credenciais de utilizador depois de autenticar com o Azure AD.

## <a name="verify-the-registration"></a>Verificar o registo

Para verificar o estado de registo do dispositivo no seu inquilino do Azure, pode utilizar o cmdlet **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** no **[módulo PowerShell do Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** .

Ao utilizar o cmdlet **Get-MSolDevice** para verificar os detalhes do serviço:

- Um objeto com o **ID de dispositivo** que corresponde ao ID do Windows cliente tem de existir.
- O valor de **DeviceTrustType** tem de ser **Associados a um domínio**. Isto é equivalente ao estado **Associado ao Azure AD híbrido** na página Dispositivos no portal do Azure AD.
- O valor para **Enabled** tem de ser **True** e **DeviceTrustLevel** tem de ser **gerida** para dispositivos que são utilizados no acesso condicional.

**Para verificar os detalhes do serviço:**

1. Abra o **Windows PowerShell** como administrador.
1. Escreva `Connect-MsolService` para ligar ao seu inquilino do Azure.  
1. Digite `get-msoldevice -deviceId <deviceId>`.
1. Certifique-se de que **Ativado** está definido como **Verdadeiro**.

## <a name="troubleshoot-your-implementation"></a>Resolver problemas relacionados com a implementação

Se estiver a ter problemas a completar a associação ao Azure AD híbrido dos dispositivos Windows de domínio associado, veja:

- [Troubleshooting Hybrid Azure AD join for Windows current devices](troubleshoot-hybrid-join-windows-current.md) (Resolver problemas com a associação híbrida ao Azure AD para dispositivos Windows atuais)
- [Troubleshooting Hybrid Azure AD join for Windows down-level devices](troubleshoot-hybrid-join-windows-legacy.md) (Resolver problemas com a associação híbrida ao Azure AD para dispositivos Windows de nível inferior)

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre a gestão de identidades de dispositivo no portal do Azure AD, consulte [gerir identidades de dispositivos no portal do Azure](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
