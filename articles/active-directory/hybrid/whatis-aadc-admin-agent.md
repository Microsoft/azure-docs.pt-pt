---
title: O que é o agente de administração do Azure AD Connect-Azure AD Connect | Microsoft Docs
description: Descreve as ferramentas usadas para sincronizar e monitorizar o seu ambiente no local com o Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 222dab87ee71870e564e426d7466555893cc565b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305208"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>O que é o Agente de Administração do Azure AD Connect? 
O agente de administração do Azure AD Connect é um novo componente do Azure Active Directory Connect que pode ser instalado em um servidor Azure Active Directory Connect. Ele é usado para coletar dados específicos de seu ambiente de Active Directory que ajuda um engenheiro de suporte da Microsoft a solucionar problemas quando você abre um caso de suporte. 

>[!NOTE]
>O agente de administração não está instalado e habilitado por padrão.  Você deve instalar o agente para coletar dados para ajudar com casos de suporte.

Quando instalado, o agente de administração do Azure AD Connect aguarda solicitações específicas de dados de Azure Active Directory, obtém os dados solicitados do ambiente de sincronização e os envia para Azure Active Directory, onde é apresentado ao suporte da Microsoft desenvolvida. 

As informações que o agente de administração do Azure AD Connect recupera do seu ambiente não são armazenadas de nenhuma forma-ele é exibido apenas para o engenheiro de suporte da Microsoft para ajudá-lo a investigar e solucionar problemas do Azure Active Directory Connect o caso de suporte relacionado que você abriu o agente de administração do Azure AD Connect não está instalado no servidor Azure AD Connect por padrão. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Instalar o agente de administração do Azure AD Connect no servidor Azure AD Connect 

Pré-requisitos:
1.  O Azure AD Connect está instalado no servidor
2.  O Azure AD Connect Health está instalado no servidor

![Agente de administração](media/whatis-aadc-admin-agent/adminagent0.png)

Os binários do agente de administração do Azure AD Connect são colocados no servidor do AAD Connect. Para instalar o agente, faça o seguinte:

1.  Abrir o PowerShell no modo de administrador
2.  Navegue até o diretório em que o aplicativo está localizado CD "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.  Executar ConfigureAdminAgent. ps1

Quando solicitado, insira suas credenciais de administrador global do Azure AD. Elas devem ser as mesmas credenciais inseridas durante a instalação Azure AD Connect.

Depois que o agente for instalado, você verá os dois novos programas a seguir na lista "Adicionar/remover programas" no painel de controle do seu servidor: 

![Agente de administração](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Quais dados no meu serviço de sincronização são mostrados para o engenheiro de serviços da Microsoft? 
Quando você abre um caso de suporte, o engenheiro de Suporte da Microsoft pode ver, para um determinado usuário, os dados relevantes em Active Directory, o espaço do conector do Active Directory no servidor Azure Active Directory Connect, o espaço do conector do Azure Active Directory no Azure Active Directory o servidor do Connect e o metaverso no servidor Azure Active Directory Connect. 

O engenheiro de Suporte da Microsoft não pode alterar nenhum dado em seu sistema e não pode ver nenhuma senha. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>E se eu não quiser que o engenheiro de suporte da Microsoft acesse meus dados? 
Depois que o agente for instalado, se você não quiser que o engenheiro de serviços da Microsoft acesse seus dados para uma chamada de suporte, você poderá desabilitar a funcionalidade modificando o arquivo de configuração de serviço, conforme descrito abaixo: 

1.  Abra **c:\Arquivos de programas\microsoft Azure ad Connect administração Agent\AzureADConnectAdministrationAgentService.exe.config** no bloco de notas.
2.  Desabilite a configuração **UserDataEnabled** , conforme mostrado abaixo. Se a configuração **UserDataEnabled** existir e estiver definida como true, defina-a como false. Se a configuração não existir, adicione a configuração conforme mostrado abaixo.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Salve o arquivo de configuração.
4.  Reinicie Azure AD Connect serviço do agente de administração, conforme mostrado abaixo

![Agente de administração](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
