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
ms.author: rkarlin
ms.openlocfilehash: 582912160c8ed514401be3522e52dcc6eb45d263
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65235756"
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
* **Contagem de**: Uma lista de todos os alertas deste tipo específico que foram detetados num dia específico.
* **Detetado por**: O serviço que foi responsável por ter acionado o alerta.
* **Data**: A data em que ocorreu o evento.
* **estado**: O estado atual para esse alerta. Existem dois tipos de estados:
  * **Active Directory**: Foi detetado o alerta de segurança.
  * **Dispensado**: O alerta de segurança foi dispensado pelo utilizador. Este estado é normalmente utilizado para os alertas que foram investigadas e mitigado ou considerado como não sendo um ataque real.
* **Gravidade**: O nível de gravidade, que pode ser alta, média ou baixa.

> [!NOTE]
> Os alertas de segurança gerados pelo Centro de Segurança também serão apresentado no Registo de Atividades do Azure. Para obter mais informações sobre como aceder ao Registo de Atividades do Azure, leia [Ver registos de atividades para auditar as ações em recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
>


### <a name="alert-severity"></a>Gravidade do alerta

-   **Alta**: Há uma grande probabilidade de que o recurso é comprometido. Deve analisar o problema imediatamente. Centro de segurança tem alta confiança as más intenções e as descobertas utilizadas para emitir o alerta. Por exemplo, um alerta que Deteta a execução de uma ferramenta maliciosa conhecida, como o Mimikatz, uma ferramenta de comum usada para roubo de credenciais. 
-   **Médio**: Isto é, provavelmente, uma atividade suspeita que possam indicar que um recurso é comprometido.
Confiança do Centro de segurança na análise ou finding é médio e a confiança de más intenções é médio a alto. Normalmente, seriam o machine learning ou deteções de anomalias com base. Por exemplo, um tentativa de uma localização anómala de início de sessão.
-   **Baixa**: Pode ser um positivo benigno ou um ataque de bloqueado. 
    - Centro de segurança não é suficientemente seguro que a intenção é maliciosa e a atividade pode ser inofensiva. Por exemplo, o log claro é uma ação que pode ocorrer quando um atacante tenta ocultar os ataques, mas em muitos casos é uma operação de rotina realizada por administradores.
    - Centro de segurança não normalmente indicam quando foram bloqueados ataques, a menos que seja um caso interessante que sugerimos que examinar. 
-   **Informativa**: Apenas verá alertas informativos quando desagregar para um incidente de segurança, ou se usar a API de REST com um ID de alerta específico. Um incidente é geralmente constituído por um número de alertas, alguns dos quais podem aparecer na sua própria conta para ser apenas informativa, mas no contexto dos outros alertas podem ser digna de uma análise detalhada.  

> [!NOTE]
> Se estiver a utilizar o **2015-06-01-pré-visualização** versão de API, em seguida, existem diferenças no qual alarme tipos de gravidade são aplicados aos quais cenários, de que está listado acima.  

### <a name="filtering-alerts"></a>Filtragem de alertas
Pode filtrar os alertas com base na data, no estado e na gravidade. A filtragem de alertas pode ser útil para cenários onde necessita de limitar o âmbito dos alertas de segurança mostrados. Por exemplo, pode pretender resolver alertas de segurança que ocorreram nas últimas 24 horas, porque está a investigar uma potencial violação no sistema.

1. Clique em **Filtrar** nos **Alertas de Segurança**. O **Filtro** abre-se e o utilizador seleciona os valores de data, de estado e de gravidade que pretende ver.

    ![Filtragem de alertas no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Responder a alertas de segurança
Selecione um alerta de segurança para obter mais informações sobre o(s) evento(s) que acionaram o alerta e quais os passos (se existirem) necessários para remediar um ataque. Os alertas de segurança estão agrupados por tipo e data. Ao clicar num alerta de segurança abre-se uma página que contém uma lista dos alertas agrupados.

![Responder a alertas de segurança no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

Neste caso, os alertas que foram acionados referem-se a atividade suspeita do protocolo RDP (Remote Desktop Protocol). A primeira coluna mostra quais os recursos que foram atacados, a segunda mostra o número de vezes que o recurso foi atacado, a terceira mostra a hora do ataque, a quarta mostra o estado do alerta e a quinta mostra a gravidade do ataque. Depois de rever estas informações, clique no recurso que foi atacado.

![Sugestões para o que fazer sobre alertas de segurança no Centro de Segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

No campo **Descrição** irá encontrar mais detalhes sobre este evento. Estes detalhes adicionais facultam informações aprofundadas sobre o que acionou o alerta de segurança, o recurso de destino, quando aplicável, o endereço IP de origem e as recomendações sobre como remediar.  Em certos casos, o endereço IP de origem está vazio (não disponível) porque nem todos os registos de eventos de segurança do Windows incluem o endereço IP.

A remediação sugerida pelo Centro de Segurança varia de acordo com o alerta de segurança. Em certos casos, poderá ter de utilizar outras capacidades do Azure para implementar a remediação recomendada. Por exemplo, a remediação para este ataque é colocar na lista de proibições o endereço IP que está a gerar este ataque ao utilizar um [ACL de rede](../virtual-network/virtual-networks-acl.md) ou uma regra de [grupo de segurança de rede](../virtual-network/security-overview.md#security-rules). Para obter mais informações sobre os diferentes tipos de alertas, leia [Alertas de Segurança por Tipo no Centro de Segurança do Azure](security-center-alerts-type.md).

> [!NOTE]
> O Centro de Segurança lançou uma pré-visualização limitada de um novo conjunto de deteções que tiram partido dos registos de auditoria, um framework de auditoria comum, para detetar comportamentos maliciosos em computadores Linux. [Envie-nos](mailto:ASC_linuxdetections@microsoft.com) um e-mail com os seus IDs de subscrição para aderir à pré-visualização.


## <a name="see-also"></a>Consulte também
Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Lidar com Incidentes de Segurança no Centro de Segurança do Azure](security-center-incident.md)
* [Capacidades de Deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md)
* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
