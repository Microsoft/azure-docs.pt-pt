---
title: Remover dados pessoais - Procuração de aplicação de diretório ativo Azure
description: Remova os dados pessoais dos conectores instalados em dispositivos para o Proxy de Aplicação de Diretório Ativo Azure.
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74275416"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Remover dados pessoais para procuração de aplicação de diretório ativo azure

A Procuração de Aplicação de Diretório Ativo Azure requer que instale conectores nos seus dispositivos, o que significa que pode haver dados pessoais nos seus dispositivos. Este artigo fornece passos para como eliminar esses dados pessoais para melhorar a privacidade.

## <a name="where-is-the-personal-data"></a>Onde estão os dados pessoais?

É possível que o Proxy de Aplicação escreva dados pessoais para os seguintes tipos de registo:

- Registos de eventos de conector
- Registos de eventos do Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Remova dados pessoais dos registos de eventos do Windows

Para obter informações sobre como configurar a retenção de dados para os registos de eventos do Windows, consulte [as Definições para os registos de eventos](https://technet.microsoft.com/library/cc952132.aspx). Para saber sobre os registos de eventos do Windows, consulte [o Registo de Eventos do Windows](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Remova os dados pessoais dos registos de eventos do Connector

Para garantir que os registos do Proxy de Aplicação não têm dados pessoais, também pode:

- Eliminar ou ver dados quando necessário, ou
- Desligue a exploração madeireira

Utilize as seguintes secções para remover dados pessoais dos registos de eventos do conector. Deve completar o processo de remoção de todos os dispositivos em que o conector está instalado.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Ver ou exportar dados específicos

Para visualizar ou exportar dados específicos, procure entradas relacionadas em cada um dos registos de eventos do conector. Os registos estão localizados a `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`.

Uma vez que os registos são ficheiros de texto, pode utilizar o [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) para procurar entradas de texto relacionadas com um utilizador.  

Para encontrar dados pessoais, procure ficheiros de registo para UserID.

Para encontrar dados pessoais registados por uma aplicação que utilize a Delegação Limitada kerberos, procure estes componentes do tipo de nome de utilizador:

- Nome principal do utilizador no local
- Username parte do nome principal do utilizador
- Username parte do nome principal do utilizador no local
- Nome do gestor de contas de segurança no local (SAM)

### <a name="delete-specific-data"></a>Eliminar dados específicos

Para eliminar dados específicos:

1. Reiniciar o serviço de conector proxy proxy da aplicação ad da Microsoft Azure para gerar um novo ficheiro de registo. O novo ficheiro de registo permite-lhe eliminar ou modificar os ficheiros de registo antigos. 
1. Siga o processo de [ver ou exportar dados específicos](#view-or-export-specific-data) descritos anteriormente para encontrar informações que precisam de ser eliminadas. Procure em todos os registos do conector.
1. Ou elimina os ficheiros de registo relevantes ou elimina seletivamente os campos que contêm dados pessoais. Também pode eliminar todos os ficheiros de registo antigos se já não precisar deles.

### <a name="turn-off-connector-logs"></a>Desligue os registos do conector

Uma opção para garantir que os registos do conector não contêm dados pessoais é desligar a geração de registo. Para parar de gerar registos de conector, retire a seguinte linha realçada de `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`.

![Mostra um corte de código com o código realçado para remover](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral do Proxy de Aplicação, consulte [como fornecer acesso remoto seguro a aplicações no local](application-proxy.md).