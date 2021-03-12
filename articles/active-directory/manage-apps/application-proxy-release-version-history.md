---
title: 'Azure Ative Directory Application Proxy: Versão lança histórico'
description: Este artigo lista todos os lançamentos de Azure Ative Directory Application Proxy e descreve novas funcionalidades e problemas fixos.
services: active-directory
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/17/2021
ms.subservice: app-mgmt
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0244e0ca66f452649d3136eb122d1c2666182ae2
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200971"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD Application Proxy: O histórico de lançamentos da versão
Este artigo lista as versões e funcionalidades do Azure Ative Directory (Azure AD) Application Proxy que foram lançados. A equipa Azure AD atualiza regularmente o Application Proxy com novas funcionalidades e funcionalidades. Os conectores Proxy da aplicação são atualizados automaticamente quando uma nova versão é lançada. 

Recomendamos que se certifique de que as atualizações automáticas estão ativadas para os seus conectores para garantir que tem as funcionalidades mais recentes e correções de erros. A Microsoft fornece suporte direto para a versão mais recente do conector e uma versão anterior.

Aqui está uma lista de recursos relacionados:

| Recurso                                         | Detalhes                                                      |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Como ativar o Proxy de Aplicação                  | Neste [tutorial](application-proxy-add-on-premises-application.md)são descritos os pré-requisitos para permitir a aplicação Proxy e instalar e registar um conector . |
| Compreenda os conectores Proxy de aplicação AD Azure | Saiba mais sobre [a gestão do conector](application-proxy-connectors.md) e como os conectores [atualizam automaticamente.](application-proxy-connectors.md#automatic-updates) |
| Download do conector proxy da aplicação Azure Ad    | [Descarregue o conector mais recente.](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) |

## <a name="1519750"></a>1.5.1975.0

### <a name="release-status"></a>Estado de lançamento

22 de julho de 2020: Lançado para download Esta versão só está disponível para instalação através da página de descarregamento. Um lançamento de upgrade automático desta versão será lançado mais tarde.

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
-   Melhor apoio aos ambientes nublados do Governo de Azure. Para etapas sobre como instalar corretamente o conector para a nuvem do Governo Azure, reveja os [passos de pré-requisitos](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) e [de instalação](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud).
- Suporte para utilizar o cliente web remote desktop Services com Application Proxy. Consulte [o Ambiente de Trabalho Remoto com O Azure AD Application Proxy](application-proxy-integrate-with-remote-desktop-services.md) para obter mais detalhes.
- Melhores negociações de extensão de websocket. 
- Suporte para encaminhamento otimizado entre grupos de conector e serviços de nuvem Proxy de aplicação com base na região. Consulte [o fluxo de tráfego otimizado com o Azure Ative Directory Application Proxy](application-proxy-network-topology.md) para obter mais detalhes. 

### <a name="fixed-issues"></a>Problemas corrigidos
- Corrigiu um problema de websocket que forçou cordas minúsculas.
- Corrigiu um problema que fez com que os conectores não respondessem ocasionalmente.

## <a name="1516260"></a>1.5.1626.0

### <a name="release-status"></a>Estado de lançamento

17 de julho de 2020: Lançado para download. Esta versão só está disponível para instalação através da página de descarregamento. Um lançamento de upgrade automático desta versão será lançado mais tarde.

### <a name="fixed-issues"></a>Problemas corrigidos
- Problema de fuga de memória resolvido presente na versão anterior
- Melhorias gerais para suporte a websocket

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Estado de lançamento

07 de abril de 2020: Lançado para download Esta versão só está disponível para instalação através da página de descarregamento. Um lançamento de upgrade automático desta versão será lançado mais tarde.

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
-   Os conectores utilizam apenas o TLS 1.2 para todas as ligações. Consulte [os pré-requisitos do Conector](application-proxy-add-on-premises-application.md#prerequisites) para obter mais detalhes.
- Sinalização melhorada entre os serviços Connector e Azure. Isto inclui o suporte de sessões fiáveis para a comunicação wcf entre os serviços Connector e Azure e melhorias no caching de DNS para as comunicações WebSocket.
- Suporte para configurar um proxy entre o Conector e a aplicação backend. Para obter mais informações consulte [Trabalhar com servidores proxy existentes no local.](application-proxy-configure-connectors-with-proxy-servers.md)

### <a name="fixed-issues"></a>Problemas corrigidos
- Removido caindo de volta para a porta 8080 para comunicações dos serviços Connector para Azure.
- Vestígios de depurg adicionados para comunicações WebSocket. 
- Resolvido preservando o atributo SameSite quando definido em cookies de aplicação backend.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Estado de lançamento

20 de setembro de 2018: Lançamento para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

- Adicionou suporte WebSocket para a aplicação QlikSense. Para saber mais sobre como integrar o QlikSense com o Application Proxy, consulte este [walkthrough](application-proxy-qlik.md). 
- Melhorou o assistente de instalação para facilitar a configuração de um representante de saída. 
- Desafine o TLS 1.2 como o protocolo predefinido para os conectores. 
- Adicionou um novo Contrato de Licença de End-User (EULA).  

### <a name="fixed-issues"></a>Problemas corrigidos

- Corrigi um erro que causou fugas de memória no conector.
- Atualizou a versão Azure Service Bus, que inclui uma correção de erro para problemas de tempo de conector.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Estado de lançamento

19 de janeiro de 2018: Lançamento para download

### <a name="fixed-issues"></a>Problemas corrigidos

- Suporte adicional para domínios personalizados que precisam de tradução de domínio no cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Estado de lançamento 

25 de maio de 2017: Lançado para download 

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias 

Melhor controlo sobre os limites de ligação de saída dos conectores. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Estado de lançamento

15 de abril de 2017: Lançamento para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

- Simplificação de embarque e gestão com menos portas necessárias. A Application Proxy exige agora a abertura de apenas duas portas de saída padrão: 443 e 80. O Application Proxy continua a utilizar apenas ligações de saída, pelo que ainda não precisa de componentes num DMZ. Para mais detalhes, consulte a nossa [documentação de configuração.](application-proxy-add-on-premises-application.md)  
- Se suportado pelo seu proxy ou firewall externo, pode agora abrir a sua rede por DNS em vez de alcance IP. Os serviços de procuração de aplicação requerem ligações apenas a *.msappproxy.net e *.servicebus.windows.net.


## <a name="earlier-versions"></a>Versões anteriores

Se estiver a utilizar uma versão de conector Proxy da Aplicação antes de 1.5.36.0, atualize a versão mais recente para garantir que tem as funcionalidades mais recentes totalmente suportadas.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [acesso remoto a aplicações no local através do Azure AD Application Proxy](application-proxy.md).
- Para começar a utilizar o Application Proxy, consulte [Tutorial: Adicione uma aplicação no local para acesso remoto através do Application Proxy](application-proxy-add-on-premises-application.md).
