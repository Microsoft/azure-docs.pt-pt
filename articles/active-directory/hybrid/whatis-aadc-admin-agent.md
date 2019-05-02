---
title: O que é o agente do Azure AD Connect Admin - Azure AD Connect | Documentos da Microsoft
description: Descreve as ferramentas usadas para sincronizar e monitorizar o seu ambiente no local com o Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab3fff4294b4cda3d1554ef2761d3f4acaca35
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687234"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>O que é o Agente de Administração do Azure AD Connect? 
O agente do Azure AD Connect administração é um componente novo do Azure Active Directory Connect que pode ser instalado num servidor do Azure Active Directory Connect. É utilizado para recolher dados específicos do seu ambiente do Active Directory que o ajuda a um engenheiro para resolver problemas ao abrir um incidente de suporte de suporte da Microsoft. 

>[!NOTE]
>O agente de administrador não está instalado e ativado por predefinição.  Tem de instalar o agente para recolher dados para ajudar a incidentes de suporte.

Quando instalado, as esperas de agente do Azure AD Connect administração para pedidos específicos para dados do Azure Active Directory, obtém os dados solicitados do ambiente de sincronização e envia-os para o Azure Active Directory, onde é apresentada para a Microsoft suportam a engenheiro. 

As informações que o agente do Azure AD Connect administração obtém do seu ambiente não são armazenadas de forma alguma – só é apresentada para o engenheiro de suporte da Microsoft para ajudá-los a investigar e resolver problemas do Azure Active Directory Connect incidente de suporte relacionados que abriu o agente do Azure AD Connect administração não está instalado no Azure AD Connect servidor por predefinição. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Instalar o agente do Azure AD Connect administração no servidor do Azure AD Connect 
Os binários do agente do Azure AD Connect administração são colocados no servidor do AAD Connect. Para instalar o agente, faça o seguinte: 



1. Powershell aberto no modo de administrador 
2. Navegue para o diretório onde o aplicativo é localizado cd "C:\Program Files\Microsoft Azure Connect\SetupFiles do diretório Active Directory" 
3. Executar a aplicação de AADConnectAdminAgentSetup.exe 
 
Quando lhe for pedido, introduza as credenciais de administrador global do Azure AD. 

>[!NOTE]
>Existe um problema conhecido, onde será solicitado para as suas credenciais várias vezes. Isso será corrigido na próxima versão.

Depois do agente está instalado, verá os seguintes dois programas novo na lista de "Adicionar/remover programas" no painel de controlo do seu servidor: 

![agente de administrador](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Os dados em meu serviço de sincronização são mostrados para o engenheiro de serviços da Microsoft? 
Quando abrir um incidente de suporte o engenheiro de suporte da Microsoft pode ver, para um determinado usuário, os dados relevantes no Active Directory, o espaço conector do Active Directory no servidor do Azure Active Directory Connect, o espaço conector do Azure Active Directory no Azure Servidor de Active Directory Connect e o Metaverse no servidor do Azure Active Directory Connect. 

O engenheiro de suporte da Microsoft não é possível alterar a todos os dados no seu sistema e não pode ver as palavras-passe. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>E se eu não quero o engenheiro de suporte da Microsoft para aceder aos meus dados? 
Assim que o agente estiver instalado, se não pretender que o engenheiro de serviços da Microsoft para aceder aos seus dados para uma chamada de suporte, pode desativar a funcionalidade ao modificar o ficheiro de configuração de serviço, conforme descrito abaixo: 

1.  Open **C:\Program Files\Microsoft Azure AD Connect administração Agent\AzureADConnectAdministrationAgentService.exe.config** no bloco de notas.
2.  Desativar **UserDataEnabled** definição conforme mostrado abaixo. Se **UserDataEnabled** definição existe e está definida como true, em seguida, defino como false. Se a definição não existir, adicione a definição conforme mostrado abaixo.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Guarde o ficheiro de configuração.
4.  Reinicie o serviço de agente do Azure AD Connect administração conforme mostrado abaixo

![agente de administrador](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
