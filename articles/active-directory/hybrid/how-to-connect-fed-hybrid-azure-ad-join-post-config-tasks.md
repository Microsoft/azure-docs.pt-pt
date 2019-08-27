---
title: 'Azure AD Connect: Tarefas de pós-configuração de ingresso no Azure AD híbrido | Microsoft Docs'
description: Este documento detalha as tarefas de configuração necessárias para concluir a junção híbrida do Azure AD
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
ms.openlocfilehash: 39e76abcac94a877e6bc7ea5c417c77c8c2febff
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032701"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Publicar tarefas de configuração para associação ao Azure AD Híbrido

Depois de executar Azure AD Connect para configurar sua organização para o ingresso no Azure AD híbrido, há algumas etapas adicionais que você deve concluir para finalizar essa configuração.  Execute apenas as etapas que se aplicam aos seus dispositivos.

## <a name="1-configure-controlled-rollout-optional"></a>1. Configurar a distribuição controlada (opcional)
Todos os dispositivos ingressados no domínio que executam o Windows 10 e o Windows Server 2016 se registram automaticamente com o Azure AD após a conclusão de todas as etapas de configuração. Se você preferir uma distribuição controlada em vez desse registro automático, poderá usar a diretiva de grupo para habilitar ou desabilitar seletivamente a distribuição automática.  Essa política de grupo deve ser definida antes de iniciar as outras etapas de configuração:
* Crie um objeto de política de grupo em seu Active Directory.
* Nomeie-o (por exemplo, ingresso no Azure AD híbrido).
* Editar & ir para:  Configurações do computador > políticas > Modelos Administrativos > componentes do Windows > registro de dispositivo.

>[!NOTE]
>Para 2012R2, as configurações de política estão em **configuração do computador > políticas > Modelos Administrativos > componentes do Windows > Workplace Join > ingressar automaticamente nos computadores cliente do workplace**

* Desabilite essa configuração:  Registrar computadores ingressados no domínio como dispositivos.
* Aplique e clique em OK.
* Vincule o GPO ao local de sua escolha (unidade organizacional, grupo de segurança ou domínio para todos os dispositivos).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Configurar a rede com pontos de extremidade de registro de dispositivo
Verifique se as URLs a seguir estão acessíveis de computadores dentro de sua rede organizacional para registro no Azure AD:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementar o WPAD para dispositivos Windows 10
Se sua organização acessa a Internet por meio de um proxy de saída, implemente a descoberta automática de proxy da Web (WPAD) para permitir que os computadores com Windows 10 se registrem no Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Configure o SCP em qualquer floresta que não tenha sido configurada pelo Azure AD Connect 

O ponto de conexão de serviço (SCP) contém as informações de locatário do Azure AD que serão usadas por seus dispositivos para o registro automático.  Execute o script do PowerShell, ConfigureSCP. ps1, que você baixou de Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Configurar qualquer serviço de Federação que não tenha sido configurado pelo Azure AD Connect

Se sua organização usa um serviço de Federação para entrar no Azure AD, as regras de declaração em sua confiança de terceira parte confiável do Azure AD devem permitir a autenticação do dispositivo. Se você estiver usando a Federação com AD FS, vá para [AD FS ajuda](https://aka.ms/aadrptclaimrules) para gerar as regras de declaração. Se você estiver usando uma solução de Federação que não seja da Microsoft, entre em contato com esse provedor para obter diretrizes.  

>[!NOTE]
>Se você tiver dispositivos de nível inferior do Windows, o serviço deverá dar suporte à emissão de declarações AuthenticationMethod e wiaormultiauthn ao receber solicitações para a relação de confiança do Azure AD. No AD FS, você deve adicionar uma regra de transformação de emissão que passe pelo método de autenticação.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Habilitar o SSO contínuo do Azure AD para dispositivos de nível inferior do Windows

Se sua organização usa a sincronização de hash de senha ou a autenticação de passagem para entrar no AD do Azure, habilite o SSO contínuo do Azure AD com esse método de entrada para autenticar https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso dispositivos de nível inferior do Windows:. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Definir política do Azure AD para dispositivos de nível inferior do Windows

Para registrar dispositivos de nível inferior do Windows, você precisa certificar-se de que a política do Azure AD permite que os usuários registrem dispositivos. 

* Faça logon em sua conta no portal do Azure.
* Vá para:  Dispositivos Azure Active Directory > > configurações do dispositivo
* Defina "os usuários podem registrar seus dispositivos com o Azure AD" para todos.
* Clicar em Guardar

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Adicionar ponto de extremidade do Azure AD a dispositivos de nível inferior do Windows

Adicione o ponto de extremidade de autenticação de dispositivo do Azure AD às zonas de intranet local em seus dispositivos de nível inferior do Windows para evitar prompts de certificado ao autenticar os dispositivos: https://device.login.microsoftonline.com 

Se você estiver usando o [SSO contínuo](how-to-connect-sso.md), também habilite "permitir atualizações da barra de status por script" nessa zona e adicione o seguinte ponto de extremidade: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Instalar o Microsoft Workplace Join em dispositivos de nível inferior do Windows

Esse instalador cria uma tarefa agendada no sistema do dispositivo que é executado no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa une silenciosamente o dispositivo ao Azure AD com as credenciais do usuário após a autenticação usando a autenticação integrada do Windows. O centro de download está https://www.microsoft.com/download/details.aspx?id=53554 em. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Configurar a política de grupo para permitir o registro do dispositivo

* Crie um objeto de política de grupo em seu Active Directory--se ainda não tiver sido criado.
* Nomeie-o (por exemplo, ingresso no Azure AD híbrido).
* Editar & ir para:  Configurações do computador > políticas > Modelos Administrativos > componentes do Windows > registro de dispositivo
* Desabilitar  Registrar computadores ingressados no domínio como dispositivos
* Aplique e clique em OK.
* Vincule o GPO ao local de sua escolha (unidade organizacional, grupo de segurança ou domínio para todos os dispositivos).

>[!NOTE]
>Para 2012R2, as configurações de política estão em **configuração do computador > políticas > Modelos Administrativos > componentes do Windows > Workplace Join > ingressar automaticamente nos computadores cliente do workplace**

## <a name="next-steps"></a>Passos seguintes
[Configurar write-back de dispositivo](how-to-connect-device-writeback.md)
