---
title: Configurar a associação ao Azure Active Directory de híbrido para domínios geridos | Documentos da Microsoft
description: Saiba como configurar a associação ao Azure Active Directory de híbrido para domínios geridos.
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
ms.openlocfilehash: b24888934d7e89a13b1b07b7138be476575fc306
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204621"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Tutorial: Configurar a associação ao Azure Active Directory de híbrido para domínios geridos

Como um utilizador na sua organização, um dispositivo é uma identidade de núcleos que pretende proteger. Pode utilizar a identidade de um dispositivo para proteger os seus recursos em qualquer altura e a partir de qualquer local. Pode realizar esse objetivo, trazer as identidades de dispositivos e gerenciá-las no Azure Active Directory (Azure AD) através de um dos seguintes métodos:

- Associação ao Azure AD
- Associação ao Azure AD Híbrido
- Registo do Azure AD

Colocarem os seus dispositivos com o Azure AD maximiza a produtividade do usuário através do início de sessão único (SSO) na sua cloud e recursos no local. Pode proteger o acesso aos seus recursos na cloud e no local com o [acesso condicional](../active-directory-conditional-access-azure-portal.md) ao mesmo tempo.

Neste tutorial, saiba como configurar a associação do Azure AD híbrido para dispositivos de computadores associados a um domínio do Active Directory num ambiente gerenciado. 

Um ambiente gerenciado pode ser implementado por meio [sincronização de hash de palavra-passe (PHS)](../hybrid/whatis-phs.md) ou [autenticação pass-through (PTA)](../hybrid/how-to-connect-pta.md) com [totalmente integrada início de sessão único](../hybrid/how-to-connect-sso.md). Estes cenários não requerem que configurar um servidor de Federação para autenticação.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar a associação ao Azure AD híbrido
> * Permitir dispositivos de nível inferior do Windows
> * Verificar dispositivos associados
> * Resolução de problemas

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial parte do princípio de que esteja familiarizado com estes artigos:

- [O que é uma identidade de dispositivo?](overview.md)
- [Como planear a implementação de associação do Azure AD híbrido](hybrid-azuread-join-plan.md)
- [Como fazer a validação controlada de associação do Azure AD híbrido](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD não suporta smart cards ou certificados em domínios geridos.

Para configurar o cenário neste artigo, precisa da [a versão mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 ou posterior) instalado.

Certifique-se de que o Azure AD Connect tenha sincronizado os objetos de computador dos dispositivos à que sua escolha para serem híbridos associados ao Azure AD para o Azure AD. Se os objetos de computador pertencem a específicas unidades organizacionais (UOs), também tem de configurar as UOs a sincronização no Azure AD Connect. Para saber mais sobre como sincronizar objetos de computador com o Azure AD Connect, consulte [configurar a filtragem por com o Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

A partir da versão 1.1.819.0, o Azure AD Connect inclui um assistente que pode utilizar para configurar a associação do Azure AD híbrido. O assistente simplifica significativamente o processo de configuração. O assistente configura os pontos de ligação de serviço (SCP) para o registo do dispositivo.

Os passos de configuração neste artigo baseiam-se sobre como utilizar o assistente no Azure AD Connect.

Associação do Azure AD híbrido requer dispositivos tenham acesso aos recursos da Microsoft seguintes na rede da sua organização:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Se utilizar ou planeia utilizar o SSO totalmente integrado)

Se sua organização necessita de acesso à internet através de um proxy de saída, a Microsoft recomenda [implementando Web Proxy Auto-Discovery (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) para permitir que os computadores Windows 10 para o registo de dispositivos com o Azure AD. Se tiver problemas, configurar e gerir o WPAD, veja [resolver problemas de deteção automática](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Se não usar WPAD e precisar de configurar as definições de proxy no seu computador, pode fazer por isso, a partir do Windows 10 1709. Para obter mais informações, consulte [definições de WinHTTP configurar usando um objeto de política de grupo (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Se configurar as definições de proxy no seu computador, utilizando as definições de WinHTTP, todos os computadores que não é possível ligar para o proxy configurado irão falhar ligar à internet.

Se sua organização necessita de acesso à internet através de um proxy autenticado de saída, tem de certificar-se de que os seus computadores Windows 10 podem autenticar com êxito para o proxy de saída. Porque os computadores Windows 10 são executados registo de dispositivos com o contexto da máquina, tem de configurar a autenticação de proxy de saída com o contexto da máquina. Consulte o seu fornecedor de proxy de saída sobre os requisitos de configuração.

## <a name="configure-hybrid-azure-ad-join"></a>Configurar a associação ao Azure AD híbrido

Para configurar uma associação doi Azure AD híbrido com o Azure AD Connect, precisa das:

- As credenciais de um administrador global do inquilino do Azure AD
- As credenciais de administrador de empresa para cada uma das florestas

**Para configurar uma associação do Azure AD híbrido com o Azure AD Connect:**

1. Iniciar o Azure AD Connect e, em seguida, selecione **configurar**.

   ![Bem-vindo](./media/hybrid-azuread-join-managed-domains/11.png)

1. Sobre o **tarefas adicionais** página, selecione **configurar opções do dispositivo**e, em seguida, selecione **seguinte**.

   ![Tarefas adicionais](./media/hybrid-azuread-join-managed-domains/12.png)

1. Sobre o **descrição geral** página, selecione **próxima**.

   ![Descrição geral](./media/hybrid-azuread-join-managed-domains/13.png)

1. Na página **Ligar ao Azure AD**, introduza as credenciais de um administrador global do inquilino do Azure AD.  

   ![Ligar ao Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. Sobre o **opções do dispositivo** página, selecione **associação ao configurar híbrido do Azure AD**e, em seguida, selecione **seguinte**.

   ![Opções do dispositivo](./media/hybrid-azuread-join-managed-domains/15.png)

1. Sobre o **SCP** página, para cada floresta onde pretende que o Azure AD Connect para configurar o SCP, conclua os passos seguintes e, em seguida, selecione **próxima**:

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Selecione a floresta.
   1. Selecione o serviço de autenticação.
   1. Selecione **adicionar** para introduzir as credenciais de administrador de empresa.

1. Sobre o **sistemas operativos de dispositivos** página, selecione os sistemas operativos que dispositivos na sua utilização do ambiente do Active Directory e, em seguida, selecione **próxima**.

   ![Sistema operativo do dispositivo](./media/hybrid-azuread-join-managed-domains/17.png)

1. Sobre o **pronto para configurar** página, selecione **configurar**.

   ![Preparado para configurar](./media/hybrid-azuread-join-managed-domains/19.png)

1. Sobre o **concluir a configuração** página, selecione **saída**.

   ![Configuração completa](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Permitir que os dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos associados a um domínio são dispositivos de nível inferior do Windows, faça o seguinte:

- Configurar as definições de intranet local para o registo de dispositivos
- Configurar o SSO totalmente integrado
- Instalar computadores de nível inferior da Microsoft à área de trabalho Junte-se para Windows

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurar as definições de intranet local para o registo de dispositivos

o concluir com êxito a associação do Azure AD híbrido dos seus dispositivos de nível inferior do Windows e para evitar avisos de certificado quando os dispositivos serão autenticados no Azure AD, pode enviar uma política para os seus dispositivos associados a um domínio para adicionar os seguintes URLs para a zona de local intranet na Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Também tem de ativar **permitir atualizações à barra de estado por meio de script** na zona de local intranet do usuário.

### <a name="configure-seamless-sso"></a>Configurar o SSO totalmente integrado

Para concluir com êxito a associação do Azure AD híbrido dos seus dispositivos de nível inferior do Windows num domínio gerido, que utiliza [PHS].... /Hybrid/whatis-phs.MD) ou [PTA](../hybrid/how-to-connect-pta.md) como método de autenticação do Azure AD na cloud, também tem [configurar o SSO totalmente integrado](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Instalar computadores de nível inferior da Microsoft à área de trabalho Junte-se para Windows

Para registar dispositivos de nível inferior do Windows, tem de instalar as organizações [Microsoft Workplace Join para computadores Windows de 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join para computadores não - Windows 10 está disponível no Microsoft Download Center.

Pode implementar o pacote com um sistema de distribuição de software, como [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). O pacote suporta as opções de instalação silenciosa padrão com o `quiet` parâmetro. O current branch do Configuration Manager oferece vantagens significativas em relação às versões anteriores, como a capacidade de controlar registos concluídos.

O instalador cria uma tarefa agendada no sistema que é executado no contexto de utilizador. A tarefa é acionada quando o utilizador inicia sessão Windows. A tarefa silenciosamente associa o dispositivo com o Azure AD com as credenciais de utilizador após autentica com o Azure AD.

## <a name="verify-the-registration"></a>Verificar o registo

Para verificar o estado de registo do dispositivo no seu inquilino do Azure, pode utilizar o **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** cmdlet no [módulo Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Quando utiliza a **Get-MSolDevice** cmdlet para verificar os detalhes do serviço:

- Um objeto com o **ID de dispositivo** que corresponde ao ID do Windows cliente tem de existir.
- O valor de **DeviceTrustType** tem de ser **Associados a um domínio**. Esta definição é equivalente a **Azure AD híbrido associou** Estado no **dispositivos** página no portal do Azure AD.
- Para dispositivos que são utilizados no acesso condicional, o valor para **Enabled** tem de ser **True** e **DeviceTrustLevel** tem de ser **gerida**.

**Para verificar os detalhes do serviço**:

1. Abra o Windows PowerShell como administrador.
1. Introduza `Connect-MsolService` para ligar ao seu inquilino do Azure.  
1. Introduza `get-msoldevice -deviceId <deviceId>`.
1. Certifique-se de que **Ativado** está definido como **Verdadeiro**.

## <a name="troubleshoot-your-implementation"></a>Resolver problemas relacionados com a implementação

Se ocorrerem problemas com a conclusão da associação do Azure AD híbrido para dispositivos associados a domínios do Windows, consulte:

- [Resolver problemas de associação do Azure AD híbrido para dispositivos atuais do Windows](troubleshoot-hybrid-join-windows-current.md)
- [Resolver problemas de associação do Azure AD híbrido para dispositivos de nível inferior do Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passos Seguintes

Saiba como [gerir identidades de dispositivos com o portal do Azure](device-management-azure-portal.md).
