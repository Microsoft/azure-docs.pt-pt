---
title: Gerir alertas de segurança no Centro de Segurança do Azure | Microsoft Docs
description: Este documento ajuda-o a utilizar as capacidades de Centro de Segurança do Azure para gerir e responder a alertas de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: v-mohabe
ms.openlocfilehash: 39849514d772f128434daad590de22f941245af7
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516105"
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Gerir e responder a alertas de segurança no Centro de Segurança do Azure
Este documento ajuda-o a utilizar o Centro de Segurança do Azure para gerir e responder a alertas de segurança.

> [!NOTE]
> Para ativar as deteções avançadas, atualize para o Centro de Segurança do Azure Standard. Está disponível uma avaliação gratuita. Para atualizar, selecione Escalão de Preço na [Política de Segurança](tutorial-security-policy.md). Veja [Preços do Centro de Segurança do Azure](security-center-pricing.md) para saber mais.
>
>

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e soluções de parceiros ligadas, tal como soluções de proteção de ponto final e firewall, para detetar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioritários é apresentada no Centro de Segurança juntamente com as informações necessárias para investigar rapidamente o problema e fornecer recomendações sobre como remediar um ataque.


> [!NOTE]
> Para obter mais informações sobre como funcionam as capacidades de deteção do Centro de Segurança, leia [Capacidades de Deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md).
>
>

## <a name="managing-security-alerts"></a>Gerir alertas de segurança
Pode rever os alertas atuais ao observar o mosaico **Alertas de segurança**. Siga os passos abaixo para ver mais detalhes sobre cada alerta:

1. No dashboard do Centro de Segurança, verá o mosaico **Alertas de segurança**.

    ![Mosaico Alertas de segurança no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. Clique no mosaico para abrir os **Alertas de segurança** para ver mais detalhes sobre os alertas.

   ![Os Alertas de segurança no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Na parte inferior desta página encontram-se os detalhes de cada alerta. Para ordenar, clique na coluna pela qual pretende ordenar. A definição para cada coluna é indicada abaixo:

* **Descrição**: Uma breve explicação do alerta.
* **Contagem**: Uma lista de todos os alertas desse tipo específico que foram detectados em um dia específico.
* **Detectado por**: O serviço que foi responsável por disparar o alerta.
* **Data**: A data em que o evento ocorreu.
* **Estado**: O estado atual para esse alerta. Existem dois tipos de estados:
  * **Ativo**: O alerta de segurança foi detectado.
  * **Ignorado**: O alerta de segurança foi Descartado pelo usuário. Esse status é normalmente usado para alertas que foram investigados e que foram mitigados ou não foram um ataque real.
* **Gravidade**: O nível de severidade, que pode ser alto, médio ou baixo.

> [!NOTE]
> Os alertas de segurança gerados pelo Centro de Segurança também serão apresentado no Registo de Atividades do Azure. Para obter mais informações sobre como aceder ao Registo de Atividades do Azure, leia [Ver registos de atividades para auditar as ações em recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
>


### <a name="alert-severity"></a>Gravidade do alerta

-   **Alta**: Há uma alta probabilidade de o recurso ser comprometido. Você deve vê-lo imediatamente. A central de segurança tem alta confiança tanto na intenção mal-intencionada quanto nas conclusões usadas para emitir o alerta. Por exemplo, um alerta que detecta a execução de uma ferramenta mal-intencionada conhecida, como Mimikatz, uma ferramenta comum usada para roubo de credenciais. 
-   **Médio**: Essa é provavelmente uma atividade suspeita que pode indicar que um recurso está comprometido.
A confiança da central de segurança na análise ou na localização é média e a confiança da intenção mal-intencionada é média a alta. Normalmente, elas seriam aprendizado de máquina ou detecções baseadas em anomalias. Por exemplo, uma tentativa de entrada de um local anormal.
-   **Baixo**: Isso pode ser um ataque benigno positivo ou bloqueado. 
    - A central de segurança não tem confiança suficiente de que a intenção é mal-intencionada e a atividade pode ser inocente. Por exemplo, a limpeza de log é uma ação que pode ocorrer quando um invasor tenta ocultar suas faixas, mas em muitos casos é uma operação de rotina executada pelos administradores.
    - A central de segurança normalmente não informa quando os ataques foram bloqueados, a menos que seja um caso interessante que sugerimos que você examine. 
-   **Informação**: Você só verá alertas informativos quando fizer uma busca detalhada em um incidente de segurança ou se usar a API REST com uma ID de alerta específica. Um incidente normalmente é composto por vários alertas, alguns dos quais podem aparecer por conta própria para que sejam apenas informativos, mas no contexto dos outros alertas podem ser mais valiosos.  

> [!NOTE]
> Se você estiver usando a versão da API **2015-06-01-Preview** , haverá diferenças em quais tipos de severidade de alarme são aplicados a quais cenários, do que está listado acima.  

### <a name="filtering-alerts"></a>Filtragem de alertas
Pode filtrar os alertas com base na data, no estado e na gravidade. A filtragem de alertas pode ser útil para cenários onde necessita de limitar o âmbito dos alertas de segurança mostrados. Por exemplo, pode pretender resolver alertas de segurança que ocorreram nas últimas 24 horas, porque está a investigar uma potencial violação no sistema.

1. Clique em **Filtrar** nos **Alertas de Segurança**. O **Filtro** abre-se e o utilizador seleciona os valores de data, de estado e de gravidade que pretende ver.

    ![Filtragem de alertas no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Responder a alertas de segurança
Selecione um alerta de segurança para obter mais informações sobre o(s) evento(s) que acionaram o alerta e quais os passos (se existirem) necessários para remediar um ataque. Os alertas de segurança estão agrupados por tipo e data. Ao clicar num alerta de segurança abre-se uma página que contém uma lista dos alertas agrupados.

![Responder a alertas de segurança no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

Neste caso, os alertas que foram acionados referem-se a atividade suspeita do protocolo RDP (Remote Desktop Protocol). A primeira coluna mostra quais os recursos que foram atacados, a segunda mostra o número de vezes que o recurso foi atacado, a terceira mostra a hora do ataque, a quarta mostra o estado do alerta e a quinta mostra a gravidade do ataque. Depois de rever estas informações, clique no recurso que foi atacado.

![Sugestões para o que fazer sobre alertas de segurança no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

No campo **Descrição** , você encontrará mais detalhes sobre esse evento. Estes detalhes adicionais facultam informações aprofundadas sobre o que acionou o alerta de segurança, o recurso de destino, quando aplicável, o endereço IP de origem e as recomendações sobre como remediar.  Em certos casos, o endereço IP de origem está vazio (não disponível) porque nem todos os registos de eventos de segurança do Windows incluem o endereço IP.

A remediação sugerida pelo Centro de Segurança varia de acordo com o alerta de segurança. Em certos casos, poderá ter de utilizar outras capacidades do Azure para implementar a remediação recomendada. Por exemplo, a correção para esse ataque é não permitir o endereço IP que está gerando esse ataque usando uma [ACL de rede](../virtual-network/virtual-networks-acl.md) ou uma regra de grupo de [segurança de rede](../virtual-network/security-overview.md#security-rules) . Para obter mais informações sobre os diferentes tipos de alertas, leia [tipos de alertas de segurança](security-center-alerts-overview.md#security-alert-types).

> [!NOTE]
> O Centro de Segurança lançou uma pré-visualização limitada de um novo conjunto de deteções que tiram partido dos registos de auditoria, um framework de auditoria comum, para detetar comportamentos maliciosos em computadores Linux. [Envie-nos](mailto:ASC_linuxdetections@microsoft.com) um e-mail com os seus IDs de subscrição para aderir à pré-visualização.


## <a name="see-also"></a>Consulte também
Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Lidar com Incidentes de Segurança no Centro de Segurança do Azure](security-center-incident.md)
* [Capacidades de Deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md)
* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
