---
title: 'Procuração de aplicação da AD Azure: Versão versão lançar histórico / Microsoft Docs'
description: Este artigo lista todas as versões da Procuração de Aplicação AD Azure e descreve novas funcionalidades e questões fixas
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2020
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: f027fbce66a73306165a0ad35d1ba3faa7a5c0bc
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983896"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Procuração de aplicação da AD Azure: Histórico de lançamento de versão
Este artigo lista as versões e funcionalidades do Azure Ative Directory (Azure AD) Application Proxy que foram lançados. A equipa da AD Azure atualiza regularmente o Application Proxy com novas funcionalidades e funcionalidades. Os conectores Proxy da aplicação são atualizados automaticamente quando uma nova versão é lançada. 

Recomendamos que certifique-se de que as atualizações automáticas estão ativadas para os seus conectores para garantir que possui as mais recentes funcionalidades e correções de erros. A Microsoft fornece suporte direto para a última versão do conector e uma versão anterior.

Aqui está uma lista de recursos relacionados:

Recurso |  Detalhes
--------- | --------- |
Como ativar o Proxy de Aplicação | Neste [tutorial](application-proxy-add-on-premises-application.md)são descritos os pré-requisitos para permitir o Proxy da Aplicação e instalar e registar um conector .
Compreender os conectores de procuração de aplicação da AD Azure | Saiba mais sobre a gestão do [conector](application-proxy-connectors.md) e como os conectores [atualizam automaticamente](application-proxy-connectors.md#automatic-updates).
Transferência de conector proxy de aplicação da AD Azure |  [Descarregue o mais recente conector.](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Estado de lançamento

07 de abril de 2020: Lançado para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
-   Os conectores utilizam apenas TLS 1.2 para todas as ligações. Consulte [os pré-requisitos do Connector](application-proxy-add-on-premises-application.md#before-you-begin) para obter mais detalhes.
- Melhor sinalização entre os serviços Connector e Azure. Isto inclui o suporte a sessões fiáveis para a comunicação wcf entre os serviços Connector e Azure e melhorias de cache dNS para comunicações WebSocket.
- Suporte para configurar um proxy entre o Connector e a aplicação backend. Para mais informações consulte [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="fixed-issues"></a>Problemas corrigidos
- Removido recuando para a porta 8080 para comunicações dos serviços Connector para Azure.
- Adicione vestígios de depuração para comunicações WebSocket. 
- Resolvida preservando o atributo do SameSite quando definido em cookies de aplicação de backend.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Estado de lançamento

20 de setembro de 2018: Lançado para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

- Suporte WebSocket adicionado para a aplicação QlikSense. Para saber mais sobre como integrar o QlikSense com o Application Proxy, veja este [walkthrough](application-proxy-qlik.md). 
- Melhorou o assistente de instalação para facilitar a configuração de um proxy de saída. 
- Detete o TLS 1.2 como o protocolo predefinido para os conectores. 
- Adicionou um novo Acordo de Licença de Utilizador Final (EULA).  

### <a name="fixed-issues"></a>Problemas corrigidos

- Fixou um bug que causou algumas fugas de memória no conector.
- Atualizou a versão Azure Service Bus, que inclui uma correção de bugs para problemas de tempo de conector.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Estado de lançamento

19 de janeiro de 2018: Lançado para download

### <a name="fixed-issues"></a>Problemas corrigidos

- Suporte adicional para domínios personalizados que precisam de tradução de domínio no cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Estado de lançamento 

25 de maio de 2017: Lançado para download 

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 

Controlo melhorado dos limites de ligação de saída dos conectores. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Estado de lançamento

15 de abril de 2017: Lançado para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

- Embarque simplificado e gestão com menos portas necessárias. Aplicação Proxy agora requer a abertura apenas de duas portas padrão de saída: 443 e 80. Application Proxy continua a utilizar apenas ligações de saída, pelo que ainda não precisa de componentes num DMZ. Para mais detalhes, consulte a nossa [documentação de configuração.](application-proxy-add-on-premises-application.md)  
- Se suportado pelo seu proxy externo ou firewall, pode agora abrir a sua rede por DNS em vez do intervalo IP. Os serviços de Procuração de Aplicação requerem ligações a *.msappproxy.net e *.servicebus.windows.net apenas.


## <a name="earlier-versions"></a>Versões anteriores

Se estiver a utilizar uma versão do conector Proxy de aplicação mais cedo do que 1.5.36.0, atualize a versão mais recente para garantir que possui as mais recentes funcionalidades totalmente suportadas.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o acesso remoto a aplicações no local através do [Procurador de Aplicação AD Azure](application-proxy.md).
- Para começar a utilizar o Proxy da Aplicação, consulte [Tutorial: Adicione uma aplicação no local para acesso remoto através de Procuração](application-proxy-add-on-premises-application.md)de Aplicações .
