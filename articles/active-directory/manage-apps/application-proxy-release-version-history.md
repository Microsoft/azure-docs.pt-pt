---
title: 'Proxy de aplicações do Azure AD: Histórico de versões | Documentos da Microsoft'
description: Este artigo apresenta uma lista de todas as versões do Proxy de aplicações do Azure AD e descreve os novos recursos e problemas fixos
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
ms.date: 04/05/2019
ms.subservice: manage-apps
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7af05769861f98ec192789d90f8a61f5b6638d1d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783295"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Proxy de aplicações do Azure AD: Histórico de versões
Este artigo lista as versões e os recursos do Proxy de aplicações do Azure Active Directory (Azure AD) que foram lançados. A equipa do Azure AD atualiza regularmente o Proxy de aplicações com novos recursos e funcionalidades. Conectores de Proxy de aplicação são atualizados automaticamente quando for lançada uma nova versão.

Aqui está uma lista de recursos relacionados:

Resource |  Detalhes
--------- | --------- |
Como ativar o Proxy de aplicações | Pré-requisitos para ativar o Proxy da aplicação e instalar e registar um conector são descritas nesta [tutorial](application-proxy-add-on-premises-application.md).
Compreender os conectores de Proxy de aplicações do Azure AD | Obter mais informações sobre [gestão de conector](application-proxy-connectors.md) e, como conectores [atualização automática](application-proxy-connectors.md#automatic-updates).
Baixe o conector de Proxy de aplicações do Azure AD |  [Transferir o conector mais recente](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Estado da versão

20 de Setembro de 2018: Lançado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos

- Foi adicionado suporte de WebSocket para o aplicativo QlikSense. Para saber mais sobre como integrar QlikSense com Proxy de aplicações, veja este [passo a passo](application-proxy-qlik.md). 
- Melhorado o Assistente de instalação para que seja mais fácil de configurar um proxy de saída. 
- Definir o TLS 1.2 como o protocolo de padrão para os conectores. 
- Adicionar um contrato de licença do novo utilizador final (EULA).  

### <a name="fixed-issues"></a>Problemas corrigidos

- Foi corrigido um erro que causaram alguns vazamentos de memória no conector.
- Atualizar a versão de Service bus do Azure, que inclui uma correção de erros para problemas de tempo limite de conector.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Estado da versão

19 de Janeiro de 2018: Lançado para download

### <a name="fixed-issues"></a>Problemas corrigidos

- Foi adicionado suporte para domínios personalizados que necessitam de tradução de domínio no cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Estado da versão 

25 de Maio de 2017: Lançado para download 

### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos 

Maior controlo sobre limites de ligação de saída dos conectores. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Estado da versão

15 de Abril de 2017: Lançado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aperfeiçoamentos

- Integração simplificada e gestão com menos as portas necessárias. Proxy de aplicações agora requer a abertura apenas duas portas de saída padrão: 443 e 80. Proxy da aplicação continua a utilizar as ligações de saída apenas, pelo que ainda não tem quaisquer componentes numa rede de Perímetro. Para obter detalhes, consulte nosso [documentação de configuração](application-proxy-add-on-premises-application.md).  
- Se for suportado pela sua firewall ou proxy externo, pode agora abrir sua rede pelo DNS, em vez de intervalo de IP. Serviços de Proxy de aplicações necessitam de ligações para *. msappproxy.net e *. servicebus.windows.net apenas.


## <a name="earlier-versions"></a>Versões anteriores

Se estiver a utilizar uma versão do conector de Proxy da aplicação anteriores ao 1.5.36.0, atualize para a versão mais recente para se certificar de que tem a versão mais recente suportada totalmente os recursos.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [acesso remoto para aplicações no local através do Proxy de aplicações do Azure AD](application-proxy.md).
- Para começar a utilizar o Proxy de aplicações, veja [Tutorial: Adicionar uma aplicação no local para acesso remoto através do Proxy de aplicações](application-proxy-add-on-premises-application.md).
