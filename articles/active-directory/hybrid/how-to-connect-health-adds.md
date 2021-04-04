---
title: Utilizar o Azure AD Connect Health com o AD DS | Microsoft Docs
description: Esta é a página do Azure AD Connect Health que irá discutir como monitorizar o AD DS.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 19e3cf15-f150-46a3-a10c-2990702cd700
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0686a4dafae17bafc65a03c985a65aecba70e60c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98017022"
---
# <a name="using-azure-ad-connect-health-with-ad-ds"></a>Utilizar o Azure AD Connect Health com o AD DS
A seguinte documentação é específica para monitorizar os Serviços de Domínio do Active Directory com o Azure AD Connect Health. As versões suportadas do AD DS são Windows Server Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016.

Para obter informações sobre como monitorizar o AD FS com o Azure AD Connect Health, veja [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md). Além disso, para obter informações sobre como monitorizar o Azure AD Connect (Sincronização) com o Azure AD Connect Health, veja [Utilizar o Azure AD Connect Health para Sincronização](how-to-connect-health-sync.md).

![Azure AD Connect Health para AD DS](./media/how-to-connect-health-adds/domainservicesnapshot.PNG)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Alertas para o Azure AD Connect Health para AD DS
A secção Alertas no Azure AD Connect Health para AD DS disponibiliza-lhe uma lista de alertas ativos e resolvidos, relacionados com os seus controladores de domínio. Selecionar um alerta ativo ou resolvido abre um novo painel com informações adicionais, juntamente com passos de resolução e ligações para documentação de suporte. Cada tipo de alerta pode ter uma ou mais instâncias, que correspondem a cada um dos controladores de domínio afetados por esse alerta específico. Perto da parte inferior do painel de alertas, pode fazer duplo clique num controlador de domínio afetado para abrir um painel adicional com mais detalhes sobre essa instância de alerta.

Neste painel, pode ativar notificações por e-mail para os alertas e alterar o intervalo de tempo na vista. Expandir o intervalo de tempo permite-lhe ver os alertas resolvidos anteriores.

![Erro de sincronização do Azure AD Connect](./media/how-to-connect-health-adds/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Dashboard de Controladores de Domínio
Este dashboard fornece uma vista topológica do seu ambiente, em conjunto com as principais métricas operacionais e estado de funcionamento de cada um dos controladores de domínio monitorizado. As métricas apresentadas ajudam a identificar rapidamente quaisquer controladores de domínio que possam exigir uma investigação adicional. Por predefinição, só é apresentado um subconjunto das colunas. No entanto, pode obter o conjunto de colunas completo ao fazer duplo clique no comando das colunas. Selecionar as colunas mais importantes para si faz com que este dashboard seja um local único e fácil de utilizar para ver o estado de funcionamento do seu ambiente do AD DS.

![Controladores de Domínio](./media/how-to-connect-health-adds/aadconnect-health-adds-domainsandsites-dashboard.png)

Os controladores de domínio podem ser agrupados pelo respetivo domínio ou site, o que é útil para compreender a topologia do ambiente. Por último, se fizer duplo clique no cabeçalho do painel, o dashboard é maximizado para utilizar os imóveis do ecrã disponíveis. Esta vista mais abrangente é útil quando são apresentadas várias colunas.

## <a name="replication-status-dashboard"></a>Dashboard de Estado de Replicação
Este dashboard fornece uma vista do estado de replicação e a topologia de replicação dos controladores de domínio monitorizados. O estado da mais recente tentativa de replicação está listado, em conjunto com a documentação útil para qualquer erro encontrado. Pode fazer duplo clique num controlador de domínio com um erro para abrir um painel novo com informações como detalhes sobre o erro, passos de resolução recomendados e ligações para a documentação de resolução de problemas.

![Estado de Replicação](./media/how-to-connect-health-adds/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Monitorização
Esta funcionalidade fornece as tendências gráficas de diferentes contadores de desempenho, que são continuamente recolhidas de cada um dos controladores de domínio monitorizados. O desempenho de um controlador de domínio pode facilmente ser comparado em todos os outros controladores de domínio monitorizados na sua floresta. Além disso, pode ver vários contadores de desempenho lado a lado, que é útil quando resolver problemas no seu ambiente.

![Monitorização](./media/how-to-connect-health-adds/aadconnect-health-adds-monitoring.png)

Por predefinição, podemos pré-selecionar quatro contadores de desempenho. No entanto, pode incluir outros clicando no comando de filtro e selecionando ou desmarcando contadores de desempenho pretendidos. Além disso, pode fazer duplo clique num gráfico de contador de desempenho específico para abrir um painel novo, que inclui os pontos de dados para cada um dos controladores de domínio monitorizados.

## <a name="related-links"></a>Ligações relacionadas
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operações do Azure AD Connect Health](how-to-connect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Usando Azure AD Connect Health para sincronização](how-to-connect-health-sync.md)
* [FAQ do Azure AD Connect Health](reference-connect-health-faq.md)
* [Azure AD Connect Health Version History](reference-connect-health-version-history.md)