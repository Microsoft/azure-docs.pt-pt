---
title: Criar alertas para a Azure Cosmos DB usando o Azure Monitor
description: Aprenda a configurar alertas para a Azure Cosmos DB usando o Azure Monitor.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 07/16/2020
ms.openlocfilehash: 1042638dc622e6675c997bc6db8df1d072824816
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099917"
---
# <a name="create-alerts-for-azure-cosmos-db-using-azure-monitor"></a>Criar alertas para a Azure Cosmos DB usando o Azure Monitor
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Os alertas são utilizados para configurar testes recorrentes para monitorizar a disponibilidade e capacidade de resposta dos seus recursos DB Azure Cosmos. Os alertas podem enviar-lhe uma notificação sob a forma de um e-mail, ou executar uma Função Azure quando uma das suas métricas atinge o limiar ou se um evento específico for registado no registo de atividade.

Pode receber um alerta com base nas métricas ou nos eventos de registo de atividade na sua conta Azure Cosmos:

* **Métricas** - O alerta dispara quando o valor de uma métrica especificada cruza um limiar que atribui. Por exemplo, quando o total de unidades de pedido consumidas exceder 1000 RU/s. Este alerta é desencadeado tanto quando a condição é primeiramente satisfeita e depois quando essa condição já não está a ser satisfeita. Consulte o artigo [de referência de dados de monitorização](monitor-cosmos-db-reference.md#metrics) para diferentes métricas disponíveis no Azure Cosmos DB.

* **Eventos de registo de atividades** – Este alerta dispara quando ocorre um determinado evento. Por exemplo, quando as chaves da sua conta Azure Cosmos são acedidas ou renovadas.

Pode configurar alertas a partir do painel DB Azure Cosmos ou do serviço Azure Monitor no portal Azure. Ambas as interfaces oferecem as mesmas opções. Este artigo mostra-lhe como configurar alertas para a Azure Cosmos DB usando o Azure Monitor.

## <a name="create-an-alert-rule"></a>Criar uma regra de alerta

Esta secção mostra como criar um alerta quando recebe um código de estado HTTP 429, que é recebido quando os pedidos são limitados. Por exemplo, poderá querer receber um alerta quando há 100 ou mais pedidos limitados de tarifas. Este artigo mostra-lhe como configurar um alerta para tal cenário utilizando o código de estado HTTP. Pode usar os passos semelhantes para configurar outros tipos de alertas também, basta escolher uma condição diferente com base na sua exigência.

1. Inscreva-se no [portal Azure.](https://portal.azure.com/)

1. Selecione **Monitor** da barra de navegação à esquerda e selecione **Alertas** .

1. Selecione o botão nova regra de alerta para abrir o painel de regras de alerta Criar.  

1. Preencha a secção **Âmbito:**

   * Abra o **painel de recursos Select** e configuure o seguinte:

   * Escolha o nome **da sua subscrição.**

   * Selecione **Azure Cosmos DB conta** para o **tipo de recurso** .

   * A **localização** da sua conta Azure Cosmos.

   * Depois de preencher os detalhes, é apresentada uma lista de contas da Azure Cosmos no âmbito selecionado. Escolha aquele para o qual deseja configurar alertas e selecione **Feito** .

1. Preencha a secção **condição:**

   * Abra o painel **de condições Select** para abrir a página lógica de sinal de **configuração** e configurar o seguinte:

   * Selecione um sinal. O **tipo de sinal** pode ser uma **Métrica** ou um Registo de **Atividades.** Escolha **métricas** para este cenário. Porque você quer receber um alerta quando há questões limite de taxa na métrica total das unidades de pedido.

   * Selecione **Tudo** para o **serviço Monitor**

   * Escolha um **nome signal** . Para obter um alerta para códigos de estado HTTP, escolha o sinal **de Unidades de Pedido Total.**

   * No separador seguinte, pode definir a lógica para desencadear um alerta e usar o gráfico para ver as tendências da sua conta Azure Cosmos. A métrica **total das Unidades de Pedido** suporta dimensões. Estas dimensões permitem filtrar a métrica. Se não selecionar nenhuma dimensão, este valor é ignorado.

   * Escolha **StatusCode** como **o nome Dimension.** **Selecione Adicione o valor personalizado** e desacione o código de estado para 429.

   * Na **lógica alerta,** desa um **limite** para **estático** . O limiar estático utiliza um valor limiar definido pelo utilizador para avaliar a regra, enquanto os limiares dinâmicos utilizam algoritmos de aprendizagem automática incorporados para aprender continuamente o padrão de comportamento métrico e calcular os limiares automaticamente.

   * Coloque o **operador** em **Maior do que** , o tipo de **agregação** para **total** , e o **valor limiar** para **100** . Com esta lógica, se o seu cliente vê mais de 100 pedidos que têm um código de estado 429, o alerta é desencadeado. Também pode configurar o tipo de agregação, granularidade agregação e a frequência de avaliação com base na sua exigência.

   * Depois de preencher o formulário, selecione **Fazer** . A imagem que se segue mostra os detalhes da lógica de alerta:

     :::image type="content" source="./media/create-alerts/configure-alert-logic.png" alt-text="Configure a lógica de receber alertas para pedidos de taxa limitada/429":::

1. Preencha a secção **do grupo Action:**

   * No painel **de regras Create,** selecione um grupo de ação existente ou crie um novo grupo de ação. Um grupo de ação permite-lhe definir as medidas a tomar quando ocorre uma condição de alerta. Para este exemplo, crie um novo grupo de ação para receber uma notificação por e-mail quando o alerta é desencadeado. Abra o painel **do grupo de ação Add** e preencha os seguintes detalhes:

   * **Nome do grupo de** ação - O nome do grupo de ação deve ser único dentro de um grupo de recursos.

   * **Nome curto** - Nome curto do grupo de ação, este valor está incluído em notificações de e-mail e SMS para identificar qual grupo de ação esteve na origem da notificação.

   * Escolha a subscrição e o grupo de recursos em que este grupo de ação será criado.  

   * Forneça um nome para a sua ação e selecione **e-mail/SMS mensagem/Push/Voice** como o **Tipo de Ação** . A imagem que se segue mostra os detalhes do tipo de ação:

     :::image type="content" source="./media/create-alerts/configure-alert-action-type.png" alt-text="Configure a lógica de receber alertas para pedidos de taxa limitada/429":::

1. Preencha a secção **de detalhes da regra de alerta:**

   * Defina um nome para a regra, forneça uma descrição opcional, o nível de gravidade do alerta, escolha se permite a regra após a criação de regras e, em seguida, selecione **Criar alerta de regra** para criar o alerta de regra métrica.

Depois de criar o alerta, estará ativo dentro de 10 minutos.

## <a name="common-alerting-scenarios"></a>Cenários de alerta comuns

Seguem-se alguns cenários em que pode utilizar alertas:

* Quando as chaves de uma conta Azure Cosmos forem atualizadas.
* Quando os dados ou o uso de um índice de um recipiente, base de dados ou uma região exceder um determinado número de bytes.
* Quando o consumo de RU/s normalizado é maior do que uma determinada percentagem. A métrica de consumo ru normalizada dá a máxima utilização de produção dentro de um conjunto de réplicas. Para aprender, consulte o artigo [ru/s normalizado.](monitor-normalized-request-units.md)  
* Quando uma região é adicionada, removida, ou se fica offline.
* Quando uma base de dados ou um recipiente é criado, eliminado ou atualizado.
* Quando a produção da sua base de dados ou do recipiente for alterada.

## <a name="next-steps"></a>Passos seguintes

* Como monitorizar a [métrica ru/s normalizada](monitor-normalized-request-units.md) no recipiente Azure Cosmos.
* Como monitorizar o [produção ou solicitar o uso](monitor-request-unit-usage.md) da unidade de uma operação em Azure Cosmos DB.