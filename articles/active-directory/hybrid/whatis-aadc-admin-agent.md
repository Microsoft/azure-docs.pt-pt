---
title: O que é o Agente de Administração azure AD Connect - Azure AD Connect [ Microsoft Docs
description: Descreve as ferramentas usadas para sincronizar e monitorizar o seu ambiente no local com a AD Azure.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79f68635820125161ed4f5777e27a20de9e6fbe8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80049385"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>O que é o Agente de Administração do Azure AD Connect? 
O Agente de Administração Azure AD Connect é um novo componente do Azure Ative Directory Connect que pode ser instalado num servidor Azure Ative Directory Connect. É usado para recolher dados específicos do seu ambiente De Diretório Ativo que ajuda um engenheiro de suporte da Microsoft a resolver problemas quando abre um caso de suporte. 

>[!NOTE]
>O agente administrativo não está instalado e ativado por predefinição.  Deve instalar o agente para recolher dados para ajudar nos casos de suporte.

Quando instalado, o Agente de Administração Azure AD Connect aguarda pedidos específicos de dados do Azure Ative Directory, obtém os dados solicitados do ambiente de sincronização e envia-os para o Azure Ative Directory, onde é apresentado ao engenheiro de suporte da Microsoft. 

A informação que o Agente de Administração Azure AD Connect recupera do seu ambiente não é armazenada de forma alguma - só é apresentada ao engenheiro de suporte da Microsoft para os ajudar a investigar e resolver problemas o caso de suporte relacionado com o Azure Ative Directory Connect que abriu O Agente de Administração Azure AD Connect não está instalado no Servidor de Ligação Azure AD por padrão. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Instale o Agente de Administração Azure AD Connect no servidor Azure AD Connect 

Pré-requisitos:
1.    Azure AD Connect está instalado no servidor
2.    Azure AD Connect Health está instalado no servidor

![agente administrativo](media/whatis-aadc-admin-agent/adminagent0.png)

Os binários do Agente de Administração Azure AD Connect são colocados no servidor AAD Connect. Para instalar o agente, faça o seguinte:

1.    Abra a powershell no modo de administração
2.    Navegue para o diretório onde a aplicação está localizada cd "C:\Program Files\Microsoft Azure Ative Directory Connect\Tools"
3.    Executar ConfigureAdminAgent.ps1

Quando solicitado, por favor introduza as suas credenciais de administração global Azure AD. Estas devem ser as mesmas credenciais introduzidas durante a instalação Azure AD Connect.

Após a instalação do agente, verá os seguintes dois novos programas na lista "Adicionar/Remover Programas" no Painel de Controlo do seu servidor: 

![agente administrativo](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Que dados no meu serviço Sync são mostrados ao engenheiro de serviços da Microsoft? 
Ao abrir um caso de suporte, o Microsoft Support Engineer pode ver, para um determinado utilizador, os dados relevantes no Ative Directory, o espaço do conector Ative Directory no servidor Azure Ative Directory Connect, o espaço de conector de diretório Ativo Azure no servidor Azure Ative Directory Connect e o Metaverse no servidor Azure Ative Directory Connect. 

O Microsoft Support Engineer não pode alterar quaisquer dados no seu sistema e não pode ver nenhuma palavra-passe. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>E se eu não quiser que o engenheiro de suporte da Microsoft aceda aos meus dados? 
Uma vez instalado o agente, se não quiser que o engenheiro de serviçoda Microsoft aceda aos seus dados para uma chamada de suporte, pode desativar a funcionalidade modificando o ficheiro config de serviço conforme descrito abaixo: 

1.    Open **C:\Program Files\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** in notepad.
2.    Desative a definição ativada pelo **utilizador,** como mostrado abaixo. Se a definição **UserDataEnabled** existe e está definida como verdadeira, então defina-a como falsa. Se a definição não existir, adicione a definição como mostrado abaixo.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.    Guarde o ficheiro de config.
4.    Reinicia o serviço de agente de administração da AD Connect, como mostrado abaixo

![agente administrativo](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
