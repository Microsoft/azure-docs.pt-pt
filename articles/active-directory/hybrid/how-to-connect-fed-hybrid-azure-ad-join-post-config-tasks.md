---
title: 'Azure AD Connect: Tarefas de configuração do Azure AD híbrido associação post | Documentos da Microsoft'
description: Este detalhes de documento post tarefas de configuração necessários para concluir a associação do Azure AD híbrido
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9af969700f4f2dfbedc4833badd7e7349696302
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60244579"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Publicar tarefas de configuração para associação ao Azure AD Híbrido

Depois de executar o Azure AD Connect para configurar a sua organização para associação ao Azure AD híbrido, existem algumas etapas adicionais que tem de concluir para finalizar a que a instalação.  Executar apenas os passos que se aplicam para os seus dispositivos.

## <a name="1-configure-controlled-rollout-optional"></a>1. Configurar implementação controlada (opcional)
Todos os dispositivos associados a um domínio que executam o Windows 10 e Windows Server 2016 automaticamente Registre-se com o Azure AD depois que todos os passos de configuração forem concluídos. Se preferir uma implementação controlada, em vez deste registo automático, pode utilizar a política de grupo para seletivamente ativar ou desativar a implementação automática.  Esta política de grupo deve ser definida antes de iniciar o outro configuração passos: Azure AD
* Crie um objeto de política de grupo no Active Directory.
* Nomeie-(associar ex-híbrido do Azure AD).
* Editar & Ir para:  Configuração do computador > políticas > modelos administrativos > componentes do Windows > registo de dispositivos.

>[!NOTE]
>Para 2012R2 as definições de política estão em **configuração do computador > políticas > modelos administrativos > componentes do Windows > associação à área de trabalho > automaticamente à área de trabalho associar computadores cliente**

* Desative esta definição:  Registre-se a computadores associados a domínios como dispositivos.
* Aplicar e clique em OK.
* Ligue o GPO para o local de sua preferência (unidade organizacional, segurança, grupo, ou ao domínio para todos os dispositivos).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Configurar a rede com pontos finais de registo do dispositivo
Certifique-se de que os seguintes URLs são acessíveis a partir de computadores no interior da rede organizacional para o registo para o Azure AD:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementar o WPAD para dispositivos Windows 10
Se sua organização acede à Internet através de um proxy de saída, implemente o Proxy Auto-Discovery WPAD (Web) para permitir que os computadores Windows 10 para se registrar para o Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Configurar o SCP em qualquer florestas que não foram configuradas pelo Azure AD Connect 

O ponto de ligação de serviço (SCP) contém informações de inquilino do Azure AD que serão utilizadas pelos seus dispositivos para o registo automático.  Execute o script do PowerShell, ConfigureSCP.ps1, que transferiu a partir do Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Configurar a qualquer serviço de federação que não foi configurado pelo Azure AD Connect

Se a sua organização utilizar um serviço de Federação para iniciar sessão com o Azure AD, as regras de afirmação na sua confiança de entidade confiadora do Azure AD tem de permitir a autenticação do dispositivo. Se estiver a utilizar o federação com o AD FS, aceda a [ajuda do AD FS](https://aka.ms/aadrptclaimrules) para gerar as regras de afirmação. Se estiver a utilizar uma solução de Federação de terceiros, contacte o fornecedor para obter orientações.  

>[!NOTE]
>Se tiver dispositivos de nível inferior do Windows, o serviço tem de suportar emitir as afirmações authenticationmethod e wiaormultiauthn ao receber pedidos para a fidedignidade do Azure AD. No AD FS, deve adicionar uma regra de transformação de emissão que o método de autenticação pass-through.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Permitir que os dispositivos de nível inferior do Azure AD totalmente integrada SSO para Windows

Se sua organização utiliza a sincronização de Hash de palavra-passe ou autenticação pass-through para iniciar sessão Azure AD, ativar o SSO totalmente integrado do Azure AD com esse método de início de sessão para autenticar dispositivos de nível inferior do Windows: https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Definir a política do Azure AD para dispositivos de nível inferior do Windows

Para registar dispositivos de nível inferior do Windows, terá de certificar-se de que a política do Azure AD permite aos utilizadores registar dispositivos. 

* Iniciar sessão na sua conta no portal do Azure.
* Vá para:  O Azure Active Directory > dispositivos > definições do dispositivo
* Defina "Os utilizadores podem registar os dispositivos com o Azure AD" para todos.
* Clicar em Guardar

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Adicionar ponto final do Azure para dispositivos de nível inferior do Windows

Adicione o ponto de final de autenticação de dispositivo do Azure AD para as zonas da Intranet locais nos seus dispositivos de nível inferior do Windows para evitar avisos de certificado quando está a autenticar os dispositivos: https://device.login.microsoftonline.com 

Se estiver a utilizar [SSO totalmente integrado](how-to-connect-sso.md), também ativar a opção "Permitir que as atualizações da barra de estado por meio de script" nessa zona e adicione o seguinte ponto de extremidade: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Instalar o Microsoft Workplace Join em dispositivos de nível inferior do Windows

Este instalador cria uma tarefa agendada no sistema de dispositivo que é executado no contexto do usuário. A tarefa é acionada quando o utilizador inicia sessão Windows. A tarefa silenciosamente associa o dispositivo com o Azure AD com as credenciais de utilizador após a autenticação com a autenticação integrada do Windows. O Centro de download é em https://www.microsoft.com/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Configurar a política de grupo para permitir o registo do dispositivo

* Crie um objeto de política de grupo no Active Directory – se ainda não estiver criado.
* Nomeie-(associar ex-híbrido do Azure AD).
* Editar & Ir para:  Configuração do computador > políticas > modelos administrativos > componentes do Windows > registo de dispositivos
* Ative:  Registe-se os computadores associados a um domínio, como dispositivos
* Aplicar e clique em OK.
* Ligue o GPO para o local de sua preferência (unidade organizacional, segurança, grupo, ou ao domínio para todos os dispositivos).

>[!NOTE]
>Para 2012R2 as definições de política estão em **configuração do computador > políticas > modelos administrativos > componentes do Windows > associação à área de trabalho > automaticamente à área de trabalho associar computadores cliente**

## <a name="next-steps"></a>Passos Seguintes
[Configurar a repetição de escrita do dispositivo](how-to-connect-device-writeback.md)
