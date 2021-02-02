---
title: Remover dados pessoais - Azure Ative Directory Application Proxy
description: Remova os dados pessoais dos conectores instalados em dispositivos para Proxy de Aplicação de Diretório Ativo Azure.
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90913ba8f7fbe8158a5cfea01e49a175180677b6
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258953"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Remover dados pessoais para Proxy de Aplicação de Diretório Ativo Azure

O Azure Ative Directory Application Proxy requer que instale conectores nos seus dispositivos, o que significa que pode haver dados pessoais nos seus dispositivos. Este artigo fornece passos para como eliminar esses dados pessoais para melhorar a privacidade.

## <a name="where-is-the-personal-data"></a>Onde estão os dados pessoais?

É possível que o Application Proxy escreva dados pessoais para os seguintes tipos de registo:

- Registos de eventos de conector
- Registos de eventos do Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Remover dados pessoais dos registos de eventos do Windows

Para obter informações sobre como configurar a retenção de dados para os registos de eventos do Windows, consulte [Definições para registos de eventos](https://technet.microsoft.com/library/cc952132.aspx). Para saber mais sobre os registos de eventos do Windows, consulte [o Windows Event Log](/windows/win32/wes/using-windows-event-log).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Remover dados pessoais dos registos de eventos do Connector

Para garantir que os registos Proxy de aplicação não possuem dados pessoais, também pode:

- Eliminar ou ver dados quando necessário, ou
- Desligue o registo

Utilize as seguintes secções para remover dados pessoais dos registos de eventos do conector. Deve completar o processo de remoção de todos os dispositivos nos quais o conector está instalado.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Ver ou exportar dados específicos

Para visualizar ou exportar dados específicos, procure entradas relacionadas em cada um dos registos de eventos do conector. Os registos estão localizados em `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace` .

Uma vez que os registos são ficheiros de texto, pode utilizar [o findstr](/windows-server/administration/windows-commands/findstr) para procurar entradas de texto relacionadas com um utilizador.  

Para encontrar dados pessoais, procure ficheiros de registo para UserID.

Para encontrar dados pessoais registados por uma aplicação que utiliza a Delegação Restrita Kerberos, procure estes componentes do tipo de nome de utilizador:

- Nome principal do utilizador no local
- Nome de utilizador parte do nome principal do utilizador
- Nome de utilizador parte do nome principal do utilizador no local
- Nome da conta de segurança no local (SAM)

### <a name="delete-specific-data"></a>Eliminar dados específicos

Para eliminar dados específicos:

1. Reinicie o serviço de conector de aplicação AD do Microsoft Azure para gerar um novo ficheiro de registo. O novo ficheiro de registo permite-lhe eliminar ou modificar os ficheiros de registo antigos. 
1. Siga o processo [de Visualização ou exportação de dados específicos](#view-or-export-specific-data) descrito anteriormente para encontrar informações que precisam de ser eliminadas. Procure em todos os registos do conector.
1. Ou elimina os ficheiros de registo relevantes ou elimina seletivamente os campos que contêm dados pessoais. Também pode eliminar todos os ficheiros de registo antigos se já não precisar deles.

### <a name="turn-off-connector-logs"></a>Desligue os registos do conector

Uma opção para garantir que os registos do conector não contêm dados pessoais é desligar a geração de registos. Para parar de gerar registos de conector, remova a seguinte linha realçada de `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` .

![Mostra um corte de código com o código realçado para remover](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral do Application Proxy, consulte [como fornecer acesso remoto seguro a aplicações no local.](application-proxy.md)