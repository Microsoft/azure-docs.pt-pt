---
title: Remover dados pessoais-Proxy de Aplicativo do Azure Active Directory
description: Remova os dados pessoais dos conectores instalados nos dispositivos para Proxy de Aplicativo do Azure Active Directory.
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
ms.openlocfilehash: fe97956d99bd5c677e499b532ef85a1bb4d324ef
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275416"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Remover dados pessoais para Proxy de Aplicativo do Azure Active Directory

Proxy de Aplicativo do Azure Active Directory requer que você instale conectores em seus dispositivos, o que significa que pode haver dados pessoais em seus dispositivos. Este artigo fornece etapas sobre como excluir esses dados pessoais para melhorar a privacidade.

## <a name="where-is-the-personal-data"></a>Onde estão os dados pessoais?

É possível que o proxy de aplicativo grave dados pessoais nos seguintes tipos de log:

- Logs de eventos do conector
- Registos de eventos do Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Remover dados pessoais de logs de eventos do Windows

Para obter informações sobre como configurar a retenção de dados para os logs de eventos do Windows, consulte [configurações para logs de eventos](https://technet.microsoft.com/library/cc952132.aspx). Para saber mais sobre os logs de eventos do Windows, consulte [usando o log de eventos do Windows](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Remover dados pessoais dos logs de eventos do conector

Para garantir que os logs do proxy de aplicativo não tenham dados pessoais, você pode:

- Excluir ou exibir dados quando necessário ou
- Desligar registro em log

Use as seções a seguir para remover dados pessoais dos logs de eventos do conector. Você deve concluir o processo de remoção para todos os dispositivos nos quais o conector está instalado.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Exibir ou exportar dados específicos

Para exibir ou exportar dados específicos, pesquise as entradas relacionadas em cada um dos logs de eventos do conector. Os logs estão localizados em `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`.

Como os logs são arquivos de texto, você pode usar [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) para pesquisar entradas de texto relacionadas a um usuário.  

Para localizar dados pessoais, pesquise arquivos de log para UserID.

Para localizar dados pessoais registrados por um aplicativo que usa a delegação restrita de Kerberos, pesquise por esses componentes do tipo de nome de usuário:

- Nome UPN do usuário local
- Nome de usuário que faz parte do nome UPN
- Nome de usuário que faz parte do nome UPN local
- Nome da conta do SAM (Gerenciador de contas de segurança) local

### <a name="delete-specific-data"></a>Excluir dados específicos

Para excluir dados específicos:

1. Reinicie o serviço do conector de proxy de aplicativo Microsoft Azure AD para gerar um novo arquivo de log. O novo arquivo de log permite que você exclua ou modifique os arquivos de log antigos. 
1. Siga o processo [Exibir ou exportar dados específicos](#view-or-export-specific-data) descritos anteriormente para encontrar informações que precisam ser excluídas. Pesquise todos os logs do conector.
1. Exclua os arquivos de log relevantes ou exclua seletivamente os campos que contêm dados pessoais. Você também pode excluir todos os arquivos de log antigos se não precisar mais deles.

### <a name="turn-off-connector-logs"></a>Desativar logs do conector

Uma opção para garantir que os logs do conector não contenham dados pessoais é desativar a geração de log. Para interromper a geração de logs de conector, remova a seguinte linha realçada de `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`.

![Mostra um trecho de código com o código realçado a ser removido](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral do proxy de aplicativo, consulte [como fornecer acesso remoto seguro a aplicativos locais](application-proxy.md).