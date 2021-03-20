---
title: 'Azure AD Connect: Hybrid Azure AD juntam-se a tarefas de configuração pós-| Microsoft Docs'
description: Este documento detalha as tarefas de configuração de posts necessárias para completar a junção híbrida AZure AD
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
ms.topic: how-to
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da5cefbacbd3851d2609a687c1948d9bcba5ffae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88612474"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Publicar tarefas de configuração para associação ao Azure AD Híbrido

Depois de ter executado o Azure AD Connect para configurar a sua organização para a ad AZure Híbrida, existem alguns passos adicionais que deve completar para finalizar essa configuração.  Realize apenas os passos que se aplicam aos seus dispositivos.

## <a name="1-configure-controlled-rollout-optional"></a>1. Configurar o lançamento controlado (Opcional)
Todos os dispositivos ligados ao domínio que executam o Windows 10 e o Windows Server 2016 registam-se automaticamente com AZure AD assim que todos os passos de configuração estiverem completos. Se preferir uma saída controlada em vez deste registo automático, pode utilizar a política de grupo para ativar ou desativar de forma seletiva o lançamento automático.  Esta política de grupo deve ser definida antes de iniciar os outros passos de configuração:
* Crie um objeto de política de grupo no seu Diretório Ativo.
* Nomeá-lo (ex-Hybrid Azure AD aderir).
* Editar e ir para: Políticas de configuração de computador > > modelos administrativos > componentes do Windows > registo do dispositivo.

>[!NOTE]
>Para 2012R2 as definições de política estão na **Configuração do Computador > Políticas > Modelos Administrativos > Componentes do Windows > Workplace Juntam-se > automaticamente no local de trabalho juntam-se a computadores clientes**

* Ativar esta definição: Registar computadores unidos pelo domínio como dispositivos.
* Aplique e clique OK.
* Ligue o GPO à localização da sua escolha (unidade organizacional, grupo de segurança ou ao domínio para todos os dispositivos).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Rede de configuração com pontos finais de registo de dispositivos
Certifique-se de que os seguintes URLs estão acessíveis a partir de computadores dentro da sua rede organizacional para registo a Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementar WPAD para dispositivos Windows 10
Se a sua organização aceder à Internet através de um proxy de saída, implemente web Proxy Auto-Discovery (WPAD) para permitir que os computadores do Windows 10 se registem no AZure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Configurar o SCP em quaisquer florestas que não foram configuradas pela Azure AD Connect 

O ponto de ligação de serviço (SCP) contém as informações do seu inquilino Azure AD que serão utilizados pelos seus dispositivos para registo automático.  Execute o script PowerShell, ConfigureSCP.ps1, que descarregou a partir do Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Configurar qualquer serviço da federação que não tenha sido configurado pela Azure AD Connect

Se a sua organização utilizar um serviço de federação para iniciar sedutação no Azure AD, as regras de reclamação no seu Azure AD confiam na confiança do partido deve permitir a autenticação do dispositivo. Se estiver a utilizar a federação com FS AD, vá à [Ajuda AD FS](https://aka.ms/aadrptclaimrules) para gerar as regras de reclamação. Se estiver a utilizar uma solução de federação não-Microsoft, contacte o fornecedor para obter orientação.  

>[!NOTE]
>Se tiver dispositivos de nível inferior ao Windows, o serviço deve suportar a emissão das reclamações de métodos de autenticação e wiaormultiauthn ao receber pedidos ao fundo Azure AD. Em AD FS, deve adicionar uma regra de transformação de emissão que passa pelo método de autenticação.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Ativar SSO sem emenda AD AD para dispositivos de nível inferior do Windows

Se a sua organização utilizar a Sincronização de Hash password ou a autenticação pass-through para iniciar sedutação no Azure AD, ative o SSO sem emenda AD Ad com esse método de entrada para autenticar dispositivos de nível inferior do Windows:  https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso . 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Desconfie da política AD do Azure para dispositivos de nível inferior do Windows

Para registar dispositivos de nível inferior do Windows, é necessário certificar-se de que a política Azure AD permite que os utilizadores registem dispositivos. 

* Faça login na sua conta no portal Azure.
* Ir para: Azure Ative Directory > Devices > Device
* "Os utilizadores podem registar os seus dispositivos com Azure AD" para ALL.
* Clicar em Guardar

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Adicione o ponto final AD AD do Windows a dispositivos de nível inferior ao Windows

Adicione o ponto final de autenticação do dispositivo AZure AD às zonas intranet locais nos dispositivos de nível inferior do Windows para evitar indicações de certificado ao autenticar os dispositivos: `https://device.login.microsoftonline.com` 

Se estiver a utilizar [o Seamless SSO,](how-to-connect-sso.md)também ative "Permitir atualizações da barra de estado através do script" nessa zona e adicione o seguinte ponto final: `https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Instalar o Microsoft Workplace Adere aos dispositivos de nível inferior do Windows

Este instalador cria uma tarefa programada no sistema do dispositivo que funciona no contexto do utilizador. A tarefa é ativada quando o utilizador entra no Windows. A tarefa junta-se silenciosamente ao dispositivo com a Azure AD com as credenciais do utilizador depois de autenticar utilizando a Autenticação Integrada do Windows. O centro de descarregamento está em https://www.microsoft.com/download/details.aspx?id=53554 . 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Configurar a política de grupo para permitir o registo do dispositivo

Para obter informações sobre como permitir a junção híbrida Azure AD para dispositivos individuais, consulte [validação controlada da ad híbrida Azure](../devices/hybrid-azuread-join-control.md).

## <a name="next-steps"></a>Passos seguintes
[Configurar a redução do dispositivo](how-to-connect-device-writeback.md)
