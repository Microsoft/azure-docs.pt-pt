---
title: Alertas de configuração - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Este artigo descreve como configurar e aceder a alertas métricos para Azure Database for PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: 8b59bcf3f25921a348e9c5f25e83619d8596ebd5
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489868"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Utilize o portal Azure para configurar alertas em métricas para a Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

Este artigo mostra-lhe como configurar a Base de Dados Azure para alertas PostgreSQL utilizando o portal Azure. Pode receber um alerta com base nas [métricas de monitorização](concepts-hyperscale-monitoring.md) dos seus serviços Azure.

Vamos configurar um alerta para desencadear quando o valor de uma métrica especificada atravessar um limiar. O alerta dispara quando a condição é recebida pela primeira vez, e continua a disparar depois.

Pode configurar um alerta para fazer as seguintes ações quando ativa:
* Envie notificações por e-mail ao administrador de serviço e coadministrators.
* Envie e-mail para e-mails adicionais que especifique.
* Chame um webhook.

Pode configurar e obter informações sobre as regras de alerta utilizando:
* [Portal do Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST do Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta sobre uma métrica a partir do portal Azure
1. No [portal Azure,](https://portal.azure.com/)selecione a Base de Dados Azure para o servidor PostgreSQL que pretende monitorizar.

2. Na secção **de monitorização** da barra lateral, selecione **Alertas** como mostrado:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="Selecione Regras de Alerta":::

3. Selecione **Nova regra de alerta** (+ ícone).

4. A página **de regras Create** abre conforme mostrado abaixo. Preencha as informações necessárias:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="Selecione Regras de Alerta":::

5. Dentro da secção **Condição,** **selecione Adicionar**.

6. Selecione uma métrica da lista de sinais a serem alertados. Neste exemplo, selecione "Storage percent".
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="Selecione Regras de Alerta":::

7. Configure a lógica de alerta:

    * **Operador** (ex. "Maior do que")
    * **Valor limiar** (ex. 85 por cento)
    * **A granularidade** da agregação deve ser satisfeita antes do alerta de disparo (ex. "Nos últimos 30 minutos")
    * e **Frequência de avaliação** (ex. "1 minuto")
   
   Selecione **Fazer** quando concluído.

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="Selecione Regras de Alerta" com um nome, nome curto, subscrição e grupo de recursos.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="Selecione Regras de Alerta" para enviar notificações aos proprietários, colaboradores e leitores de subscrição.
   
    Selecione **OK** quando concluído.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="Selecione Regras de Alerta":::

11. Especifique um nome de regra de alerta, descrição e severidade.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="Selecione Regras de Alerta"::: 

12. Selecione **Criar a regra de alerta** para criar o alerta.

    Em poucos minutos, o alerta está ativo e dispara como descrito anteriormente.

### <a name="managing-alerts"></a>Gerir alertas

Uma vez criado um alerta, pode selecioná-lo e fazer as seguintes ações:

* Veja um gráfico que mostra o limiar métrico e os valores reais do dia anterior relevantes para este alerta.
* **Editar** ou **eliminar** a regra de alerta.
* **Desative** ou **Ative** o alerta, se pretender parar temporariamente ou retomar as notificações.

## <a name="suggested-alerts"></a>Alertas sugeridos

### <a name="disk-space"></a>Espaço em disco

A monitorização e alerta são importantes para todos os grupos de servidores Hyperscale (Citus) de produção. A base de dados PostgreSQL subjacente requer espaço livre para funcionar corretamente. Se o disco ficar cheio, o nó do servidor de base de dados ficará offline e recusar-se-á a iniciar até que o espaço esteja disponível. Nessa altura, requer um pedido de suporte da Microsoft para corrigir a situação.

Recomendamos a definição de alertas de espaço em cada nó em cada grupo de servidores, mesmo para utilização não-produção. Os alertas de utilização do espaço em disco fornecem o aviso prévio necessário para intervir e manter os nós saudáveis. Para obter melhores resultados, experimente uma série de alertas a 75%, 85% e 95% de utilização. As percentagens a escolher dependem da velocidade de ingestão de dados, uma vez que a ingestão rápida de dados preenche o disco mais rapidamente.

À medida que o disco se aproxima do seu limite de espaço, experimente estas técnicas para obter mais espaço livre:

* Rever a política de retenção de dados. Mova os dados mais antigos para o armazenamento frio, se possível.
* Considere [adicionar nós](howto-hyperscale-scaling.md#add-worker-nodes) ao grupo de servidor e reequilibtar fragmentos. O reequilíbrio distribui os dados por mais computadores.
* Considere [aumentar a capacidade](howto-hyperscale-scaling.md#increase-or-decrease-vcores-on-nodes) dos nós dos trabalhadores. Cada trabalhador pode ter até 2 TiB de armazenamento. No entanto, a adição de nós deve ser tentada antes de redimensionar os nós porque a adição de nós completa-se mais rapidamente.

### <a name="cpu-usage"></a>Utilização da CPU

A monitorização da utilização do CPU é útil para estabelecer uma linha de base para o desempenho. Por exemplo, pode notar que o uso do CPU é geralmente de cerca de 40-60%. Se o uso do CPU começar subitamente a pairar cerca de 95%, pode reconhecer uma anomalia. O uso do CPU pode refletir o crescimento orgânico, mas também pode revelar uma consulta desviada. Ao criar um alerta de CPU, deslovide uma granularidade de agregação longa para capturar aumentos prolongados e ignorar picos momentâneos.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [configurar webhooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
* Obtenha uma [visão geral da recolha de métricas](../azure-monitor/platform/data-platform.md) para se certificar de que o seu serviço está disponível e responsivo.