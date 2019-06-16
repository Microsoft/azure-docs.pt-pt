---
title: Remover os dados pessoais - Proxy de aplicações do Azure Active Directory | Documentos da Microsoft
description: Remova dados pessoais de conectores instalados em dispositivos para o Proxy de aplicações do Azure Active Directory.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 039f8c9f114dfd3542fefa7b1a1eea8656cbb9c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65782974"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Remover dados pessoais para o Proxy de aplicações do Azure Active Directory  

Proxy de aplicação do Active Directory do Azure requer que instale conectores nos seus dispositivos, que significa que pode haver dados pessoais nos seus dispositivos. Este artigo fornece passos para saber como eliminar os dados pessoais para melhorar a privacidade. 


## <a name="where-is-the-personal-data"></a>Onde posso encontrar os dados pessoais?
É possível que o Proxy de aplicações escrever dados pessoais para os seguintes tipos de registo:

- Registos de eventos do conector
- Registos de eventos do Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Remover dados pessoais de registos de eventos do Windows

Para obter informações sobre como configurar a retenção de dados para os registos de eventos do Windows, consulte [as definições para registos de eventos](https://technet.microsoft.com/library/cc952132.aspx). Para saber mais sobre os registos de eventos do Windows, consulte [registo de eventos do Windows usando](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Remover dados pessoais de registos de eventos do conector

Para garantir que os registos de Proxy da aplicação não tiver dados pessoais, pode:

- Eliminar ou ver os dados quando necessário, ou
- Desativar o registo

Utilize as secções seguintes para remover dados pessoais de registos de eventos do conector. Tem de concluir o processo de remoção de todos os dispositivos em que o conector é instalado.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Ver ou exportar dados específicos

Para ver ou exportar dados específicos, procure as entradas relacionadas em cada um dos logs de eventos de conector. Os registos estão localizados em `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. 

Uma vez que os registos são arquivos de texto, pode usar [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) em procurar entradas de texto relacionado com um utilizador.  

Para localizar dados pessoais, procure ficheiros de registo UserID. 

Para localizar dados pessoais com sessão iniciados por uma aplicação que utiliza a delegação restrita de Kerberos, procure esses componentes do tipo de nome de utilizador:

- Nome principal de utilizador no local
- Parte do nome de utilizador de nome principal de utilizador
- Parte do nome de utilizador de nome principal de utilizador no local
- Nome de conta de gestor (SAM) de contas de segurança de no local 


### <a name="delete-specific-data"></a>Eliminar dados específicos

Para eliminar dados específicos:

1. Reinicie o serviço de conector de Proxy de aplicações do Microsoft Azure AD para gerar um novo ficheiro de registo. O novo ficheiro de registo permite-lhe eliminar ou modificar os ficheiros de registo antigos. 
2. Siga os [modo de exibição ou exportar dados específicos](#view-or-export-specific-data) processo descrito anteriormente para encontrar informações de que precisam de eliminar. Pesquise todos os registos do conector.
3. Elimine os ficheiros de registo relevantes ou eliminar seletivamente os campos que contêm dados pessoais. Também pode eliminar todos os ficheiros de registo antigos se não precisar delas.

### <a name="turn-off-connector-logs"></a>Desativar a registos do conector

É uma opção para garantir que os registos do conector não contêm dados pessoais desativar a geração de log. Para parar a geração de registos do conector, remova a seguinte linha realçada de `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`. 

![Configuração](./media/application-proxy-remove-personal-data/01.png)


## <a name="next-steps"></a>Passos Seguintes

Para uma descrição geral do Proxy de aplicações, consulte [como fornecer acesso remoto seguro a aplicações no local](application-proxy.md).

