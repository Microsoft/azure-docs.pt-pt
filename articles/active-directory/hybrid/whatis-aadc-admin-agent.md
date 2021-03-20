---
title: O que é o Azure AD Connect Admin Agent - Azure AD Connect | Microsoft Docs
description: Descreve as ferramentas usadas para sincronizar e monitorizar o ambiente no local com a Azure AD.
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
ms.openlocfilehash: da4e1cfc68dff4ad2bc0552c6d35fe1230779306
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91312963"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>O que é o Agente de Administração do Azure AD Connect? 
O Azure AD Connect Administration Agent é um novo componente do Azure Ative Directory Connect que pode ser instalado num servidor Azure Ative Directory Connect. É utilizado para recolher dados específicos do seu ambiente ative directory que ajudam um engenheiro de suporte da Microsoft a resolver problemas quando abre um caso de suporte. 

>[!NOTE]
>O agente administrativo não está instalado e ativado por defeito.  Tem de instalar o agente para recolher dados para ajudar em casos de suporte.

Quando instalado, o Agente de Administração AD Connect Azure aguarda pedidos específicos de dados do Azure Ative Directory, obtém os dados solicitados do ambiente de sincronização e envia-os para o Azure Ative Directory, onde é apresentado ao engenheiro de suporte da Microsoft. 

A informação que o Agente de Administração AD Connect do Azure AD obtém do seu ambiente não é armazenada de forma alguma - só é apresentada ao engenheiro de suporte da Microsoft para os ajudar na investigação e resolução de problemas do caso de suporte relacionado com o Azure Ative Directory Connect que abriu O Agente de Administração AD Connect Azure não está instalado no Azure AD Connect Server por padrão. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Instale o agente de administração Azure AD Connect no servidor Azure AD Connect 

Pré-requisitos:
1.    Azure AD Connect está instalado no servidor
2.    Azure AD Connect Health está instalado no servidor

![agente administrador](media/whatis-aadc-admin-agent/adminagent0.png)

Os binários do Agente de Administração AD Connect Azure são colocados no servidor AAD Connect. Para instalar o agente, faça o seguinte:

1.    Abrir a powershell no modo de administração
2.    Navegue para o diretório onde a aplicação está localizada cd "C:\Program Files\Microsoft Azure Ative Directory Connect\Tools"
3.    Executar ConfigureAdminAgent.ps1

Quando solicitado, introduza as suas credenciais de administração global Azure AD. Estas devem ser as mesmas credenciais registadas durante a instalação Azure AD Connect.

Depois de instalado o agente, verá os seguintes dois novos programas na lista "Adicionar/Remover Programas" no Painel de Controlo do seu servidor: 

![Screenshot que mostra a lista de Programas Add/Remove que inclui os novos programas adicionados.](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Que dados no meu serviço Sync são mostrados ao engenheiro de serviços da Microsoft? 
Quando abre um caso de suporte, o Microsoft Support Engineer pode ver, para um determinado utilizador, os dados relevantes no Ative Directory, o espaço de conector ative Directory no servidor Azure Ative Directory Connect, o espaço de conector Azure Ative Directory no servidor Azure Ative Directory Connect e o Metaverse no servidor Azure Ative Directory Connect. 

O Microsoft Support Engineer não pode alterar nenhum dado no seu sistema e não consegue ver nenhuma palavra-passe. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>E se eu não quiser que o engenheiro de suporte da Microsoft aceda aos meus dados? 
Uma vez instalado o agente, se não pretender que o engenheiro de serviço da Microsoft aceda aos seus dados para uma chamada de suporte, pode desativar a funcionalidade modificando o ficheiro de configuração de serviço, conforme descrito abaixo: 

1. Abra **C:\Ficheiros de programa\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** no bloco de notas.
2. Desative a definição **UserDataEnabled** como mostrado abaixo. Se a definição **UserDataEnabled** existir e estiver definida como verdadeira, então defina-a em falso. Se a regulação não existir, adicione a regulação como mostrado abaixo.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3. Guarde o ficheiro config.
4. Reinicie o serviço de agente de administração Ad Connect Azure, como mostrado abaixo

![Screenshot que mostra onde reiniciar o serviço de agente de administrador ad Ad Azure.](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
