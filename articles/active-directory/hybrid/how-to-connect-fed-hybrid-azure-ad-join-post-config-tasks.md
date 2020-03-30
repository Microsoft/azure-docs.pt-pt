---
title: 'Azure AD Connect: Hybrid Azure AD juntam-se a tarefas de configuração de posts / Microsoft Docs'
description: Este documento detalha as tarefas de configuração de post necessárias para completar a adi do Hybrid Azure
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
ms.openlocfilehash: ffb8243041bb93ba8be6a65bb83df6f84affaee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049667"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Publicar tarefas de configuração para associação ao Azure AD Híbrido

Depois de ter executado o Azure AD Connect para configurar a sua organização para a adesão do Hybrid Azure AD, existem alguns passos adicionais que deve completar para finalizar essa configuração.  Efetue apenas os passos aplicáveis aos seus dispositivos.

## <a name="1-configure-controlled-rollout-optional"></a>1. Configurar o lançamento controlado (Opcional)
Todos os dispositivos ligados ao domínio que executam o Windows 10 e o Windows Server 2016 registam-se automaticamente com o Azure AD assim que todos os passos de configuração estiverem completos. Se preferir um lançamento controlado em vez deste registo automático, pode utilizar a política do grupo para ativar ou desativar seletivamente o lançamento automático.  Esta política de grupo deve ser definida antes de iniciar os outros passos de configuração:
* Crie um objeto de política de grupo no seu Diretório Ativo.
* Nomeie-o (ex- Hybrid Azure AD join).
* Editar e ir a: Políticas de configuração de computador > > modelos administrativos > componentes do Windows > Registo do Dispositivo.

>[!NOTE]
>Para 2012R2, as definições de política estão em **Políticas de configuração de computador > > modelos administrativos > componentes do Windows > se juntar em > automaticamente no local de trabalho juntem-se aos computadores dos clientes**

* Ativar esta definição: Registe os computadores unidos pelo domínio como dispositivos.
* Aplicar e clicar OK.
* Ligue o GPO à localização da sua escolha (unidade organizacional, grupo de segurança ou ao domínio de todos os dispositivos).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Configurar rede com pontos finais de registo do dispositivo
Certifique-se de que os seguintes URLs estão acessíveis a partir de computadores dentro da sua rede organizacional para registo em Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementar wPAD para dispositivos Windows 10
Se a sua organização aceder à Internet através de um proxy de saída, implemente o Web Proxy Auto-Discovery (WPAD) para permitir que os computadores do Windows 10 se registem no Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Configure o SCP em quaisquer florestas que não tenham sido configuradas pela Azure AD Connect 

O ponto de ligação de serviço (SCP) contém as informações do seu inquilino Azure AD que serão utilizados pelos seus dispositivos para registo automático.  Execute o script PowerShell, ConfigureSCP.ps1, que descarregou a partir do Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Configure qualquer serviço da federação que não tenha sido configurado pela Azure AD Connect

Se a sua organização utilizar um serviço da federação para iniciar sessão no Azure AD, as regras de reclamação no seu fundo de confiança azure AD devem permitir a autenticação do dispositivo. Se estiver a usar a federação com AD FS, vá à [Ajuda AD FS](https://aka.ms/aadrptclaimrules) para gerar as regras de reclamação. Se estiver a utilizar uma solução de federação não microsoft, contacte esse fornecedor para obter orientação.  

>[!NOTE]
>Se tiver dispositivos de nível inferior windows, o serviço deve suportar a emissão do método de autenticação e das reclamações wiaormultiauthn ao receber pedidos para o fundo Azure AD. Em AD FS, deve adicionar uma regra de transformação de emissão que passa pelo método de autenticação.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Ativar o SSO SSO SEm Emenda A D ad azul para dispositivos de nível inferior windows

Se a sua organização utilizar a Sincronização de Password Hash ou a Autenticação Pass-through para iniciar sessão no Azure AD, ative o https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ssoAzure AD Seamless SSO com esse método de entrada para autenticar dispositivos de nível inferior do Windows: . 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Definir a política de Anúncios Azure para dispositivos de nível inferior windows

Para registar dispositivos de nível inferior do Windows, é necessário certificar-se de que a política de AD Azure permite que os utilizadores registem dispositivos. 

* Faça login na sua conta no portal Azure.
* Ir a: Diretório Ativo > Dispositivos > Dispositivos
* Definir "Os utilizadores podem registar os seus dispositivos com AD Azure" para ALL.
* Clicar em Guardar

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Adicione o ponto final do Azure AD aos dispositivos de nível inferior do Windows

Adicione o ponto final de autenticação do dispositivo Azure AD às zonas intranet locais nos dispositivos de nível inferior do Windows para evitar solicitações de certificado ao autenticar os dispositivos:`https://device.login.microsoftonline.com` 

Se estiver a utilizar [o SSO Sso sem emenda,](how-to-connect-sso.md)também ative "Permitir atualizações da barra de estado através do script" nessa zona e adicionar o seguinte ponto final:`https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Instale a Microsoft Workplace Join em dispositivos de nível inferior windows

Este instalador cria uma tarefa programada no sistema do dispositivo que funciona no contexto do utilizador. A tarefa é desencadeada quando o utilizador faz o sinal de si no Windows. A tarefa junta-se silenciosamente ao dispositivo com a AD Azure com as credenciais do utilizador após a autenticação utilizando a Autenticação Integrada do Windows. O centro de https://www.microsoft.com/download/details.aspx?id=53554descarregamento está em . 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Configure a política do grupo para permitir o registo do dispositivo

* Crie um objeto de política de grupo no seu Diretório Ativo-- se ainda não for criado.
* Nomeie-o (ex- Hybrid Azure AD join).
* Editar & ir para: Políticas de configuração de > de computador > modelos administrativos > componentes do Windows > Registo de Dispositivos
* Ativar: Registar computadores ligados ao domínio como dispositivos
* Aplicar e clicar OK.
* Ligue o GPO à localização da sua escolha (unidade organizacional, grupo de segurança ou ao domínio de todos os dispositivos).

>[!NOTE]
>Para 2012R2, as definições de política estão em **Políticas de configuração de computador > > modelos administrativos > componentes do Windows > se juntar em > automaticamente no local de trabalho juntem-se aos computadores dos clientes**

## <a name="next-steps"></a>Passos seguintes
[Configurar a reescrita do dispositivo](how-to-connect-device-writeback.md)
