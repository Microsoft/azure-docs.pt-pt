---
title: Configurar alertas - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Este artigo descreve como configurar e aceder a alertas métricos para a Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 0e2eb4ab13319779ae209e58253c6a5f2ccb75da
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462433"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Utilize o portal Azure para configurar alertas sobre métricas para a Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

Este artigo mostra-lhe como configurar a Base de Dados Azure para alertas PostgreSQL utilizando o portal Azure. Pode receber um alerta com base em [métricas de monitorização](concepts-hyperscale-monitoring.md) dos seus serviços Azure.

Vamos criar um alerta para desencadear quando o valor de uma métrica especificada atravessar um limiar. O alerta dispara quando a condição é satisfeita pela primeira vez, e continua a disparar depois.

Pode configurar um alerta para fazer as seguintes ações quando dispara:
* Envie notificações por e-mail ao administrador de serviço e aos coadministradores.
* Envie e-mail para e-mails adicionais que especifice.
* Chame um gancho de teia.

Pode configurar e obter informações sobre as regras de alerta usando:
* [Portal do Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta sobre uma métrica do portal Azure
1. No [portal Azure,](https://portal.azure.com/)selecione a Base de Dados Azure para o servidor PostgreSQL que pretende monitorizar.

2. Na secção **de monitorização** da barra lateral, selecione **Alertas** conforme mostrado:

   ![Selecione Regras de Alerta](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Selecione **Nova regra de alerta** (ícone+).

4. A página de **regras Create** abre como mostrado abaixo. Preencha as informações necessárias:

   ![Adicionar forma de alerta métrico](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. Dentro da secção **Condição,** **selecione Adicionar**.

6. Selecione uma métrica da lista de sinais a alertar. Neste exemplo, selecione "Storage percent".
   
   ![Selecione métrica](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Configure a lógica de alerta:

    * **Operador** (ex. "Maior que")
    * **Valor limiar** (ex. 85 por cento)
    * Quantidade de tempo de **granularidade de agregação** a regra métrica deve ser satisfeita antes do alerta disparar (ex. "Nos últimos 30 minutos")
    * e **Frequência de avaliação** (ex. "1 minuto")
   
   Selecione **Feito** quando estiver completo.

   ![Selecione métrica](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. Dentro da secção **Grupos de Ação,** selecione **Create New** para criar um novo grupo para receber notificações no alerta.

9. Preencha o formulário "Adicionar grupo de ação" com um nome, nome curto, subscrição e grupo de recursos.

    ![Grupo de ação](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Configure um tipo de ação de **e-mail/SMS/Push/Voice.**
    
    Escolha "Email Azure Resource Manager Role" para enviar notificações aos proprietários de subscrições, colaboradores e leitores.
   
    Selecione **OK** quando estiver concluído.

    ![Grupo de ação](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Especifique um nome de regra de alerta, descrição e gravidade.

    ![Grupo de ação](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Selecione **Criar a regra** de alerta para criar o alerta.

    Em alguns minutos, o alerta está ativo e é disparado conforme descrito anteriormente.

### <a name="managing-alerts"></a>Gestão de alertas

Uma vez criado um alerta, pode selecioná-lo e fazer as seguintes ações:

* Veja um gráfico que mostre o limiar métrico e os valores reais do dia anterior relevantes para este alerta.
* **Editar** ou **Eliminar** a regra de alerta.
* **Desative** ou **Ative** o alerta, caso pretenda parar ou retomar temporariamente a receção de notificações.

## <a name="suggested-alerts"></a>Alertas sugeridos

### <a name="disk-space"></a>Espaço em disco

A monitorização e alerta é importante para cada grupo de servidores de hiperescala de produção (Citus). A base de dados PostgreSQL subjacente requer espaço de disco gratuito para funcionar corretamente. Se o disco ficar cheio, o nó do servidor de base de dados ficará offline e recusar-se-á a iniciar até que o espaço esteja disponível. Nessa altura, requer um pedido de suporte da Microsoft para corrigir a situação.

Recomendamos a definição de alertas de espaço em disco em cada nó de cada grupo de servidores, mesmo para uso não produtivo. Os alertas de utilização do espaço do disco fornecem o aviso prévio necessário para intervir e manter os nódosos saudáveis. Para obter os melhores resultados, experimente uma série de alertas a 75%, 85%, e 95% de utilização. As percentagens a escolher dependem da velocidade de ingestão de dados, uma vez que a ingestão rápida de dados preenche o disco mais rapidamente.

À medida que o disco se aproxima do seu limite de espaço, experimente estas técnicas para obter mais espaço livre:

* Reveja a política de retenção de dados. Mova dados mais antigos para armazenamento frio, se possível.
* Considere [adicionar nós](howto-hyperscale-scaling.md#add-worker-nodes) ao grupo do servidor e reequilibrar fragmentos. O reequilíbrio distribui os dados por mais computadores.
* Considere [aumentar a capacidade](howto-hyperscale-scaling.md#increase-vcores) dos nódosos dos trabalhadores. Cada trabalhador pode ter até 2 TiB de armazenamento. No entanto, a adição de nódosos deve ser tentada antes de redimensionar os nódosos porque a adição de nódosos completa-se mais rapidamente.

### <a name="cpu-usage"></a>Utilização do CPU

A monitorização da utilização do CPU é útil para estabelecer uma linha de base para o desempenho. Por exemplo, pode notar que o uso de CPU costuma rondar os 40-60%. Se o uso de CPU começar subitamente a rondar os 95%, pode reconhecer uma anomalia. O uso do CPU pode refletir o crescimento orgânico, mas também pode revelar uma consulta perdida. Ao criar um alerta de CPU, delineie uma granularidade de agregação longa para capturar aumentos prolongados e ignorar picos momentâneos.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [configurar webhooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
* Obtenha uma [visão geral da recolha de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para garantir que o seu serviço está disponível e reativo.
