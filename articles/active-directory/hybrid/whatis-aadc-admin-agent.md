---
title: O que é o agente do Azure AD Connect Admin - Azure AD Connect | Documentos da Microsoft
description: Descreve as ferramentas usadas para sincronizar e monitorizar o seu ambiente no local com o Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17b99b7b03898ad72b4d9c7cc2ba9154855475ef
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488406"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>O que é o agente do Azure AD Connect administrador? 
O agente do Azure AD Connect administração é um componente novo do Azure Active Directory Connect que está instalado num servidor do Azure Active Directory Connect. É utilizado para recolher dados específicos do seu ambiente do Active Directory que o ajuda a um engenheiro para resolver problemas ao abrir um incidente de suporte de suporte da Microsoft.

Quando instalado, as esperas de agente do Azure AD Connect administração para pedidos específicos de dados do Azure Active Directory, obtém os dados solicitados do ambiente de sincronização e envia-os para o Azure Active Directory, onde é apresentada para a Microsoft suporta engenheiro.

As informações que o agente do Azure AD Connect administração obtém do seu ambiente não são armazenadas de forma alguma – só é apresentada para o engenheiro de suporte da Microsoft para ajudá-los a investigar e resolver problemas do Azure Active Directory Connect incidente de suporte relacionados que abriu

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>Como o agente do Azure AD Connect administrador está instalado no servidor do Azure AD Connect? 
Após a instalação do agente de administrador, verá os seguintes dois programas novo na lista de "Adicionar/remover programas" no painel de controlo do seu servidor: 

![agente de administrador](media/whatis-aadc-admin-agent/adminagent1.png)

Não remover ou anular instalar os programas, pois são uma parte crítica da sua instalação do Azure AD Connect.

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Os dados em meu serviço de sincronização são mostrados para o engenheiro de serviços da Microsoft?
Ao abrir um incidente de suporte, o engenheiro de suporte da Microsoft pode ver, para um determinado usuário, os dados relevantes no Active Directory, o espaço conector do Active Directory no servidor do Azure Active Directory Connect, o espaço conector do Azure Active Directory no Azure Servidor de Active Directory Connect e o Metaverse no servidor do Azure Active Directory Connect.

O engenheiro de suporte da Microsoft não é possível alterar a todos os dados no seu sistema e não pode ver as palavras-passe.

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>E se eu não quero o engenheiro de suporte da Microsoft para aceder aos meus dados? 
 
Se, não quiser chamar o engenheiro de serviços da Microsoft para aceder aos seus dados para uma chamada de suporte pode indicar isto ao abrir um suporte no portal do: 

  1.    Open **C:\Program Files\Microsoft Azure AD Connect administração Agent\AzureADConnectAdministrationAgentService.exe.config** no bloco de notas.
  2.    Desativar **UserDataEnabled** definição conforme mostrado abaixo. Se **UserDataEnabled** definição existe e está definida como true, em seguida, defino como false. Se a definição não existir, adicione a definição conforme mostrado abaixo.    
  `
 <appSettings>
   <add key="TraceFilename" value="ADAdministrationAgent.log" />
   <add key="UserDataEnabled" value="false" />
  </appSettings>
  `
  3.    Guarde o ficheiro de configuração.
  4.    Reinicie o serviço de agente do Azure AD Connect administração conforme mostrado abaixo

![agente de administrador](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).