---
title: Alertas de métricas de configuração
titleSuffix: Azure Cognitive Services
description: Como configurar os seus alertas Métricas Advisor usando ganchos para e-mail, web e Azure DevOps.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 30d8fdf99da7a4854db0985bed6256ecd6f7a366
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420925"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Como fazer: Configurar alertas e receber notificações usando um gancho

Depois de uma anomalia ser detetada pelo Metrics Advisor, será ativada uma notificação de alerta com base em definições de alerta, utilizando um gancho. Uma definição de alerta pode ser usada com múltiplas configurações de deteção, vários parâmetros estão disponíveis para personalizar a sua regra de alerta.

## <a name="create-a-hook"></a>Criar um gancho

Metrics Advisor suporta três tipos diferentes de ganchos: gancho de e-mail, gancho web e Azure DevOps. Pode escolher o que trabalha para o seu cenário específico.       

### <a name="email-hook"></a>Gancho de e-mail

> [!Note]
> Métricas Os administradores de recursos do Advisor precisam de configurar as definições de Email e inserir informações relacionadas com SMTP no Metrics Advisor antes de os alertas de anomalia poderem ser enviados. O administrador do grupo de recursos ou administrador de subscrição precisa de atribuir pelo menos uma função *de Administrador de Consultor de Métricas de Serviços Cognitivos* no separador de controlo de acesso do recurso Metrics Advisor. [Saiba mais sobre a configuração das definições de e-mail](../faq.md#how-to-set-up-email-settings-and-enable-alerting-by-email).

Para criar um gancho de e-mail, estão disponíveis os seguintes parâmetros: 

Um gancho de e-mail é o canal para alertas de anomalias a enviar para endereços de e-mail especificados na secção **email.** Serão enviados dois tipos de e-mails de alerta: *Feed de dados não estão disponíveis* alertas, e *relatórios de incidentes* que contêm uma ou múltiplas anomalias. 

|Parâmetro |Descrição  |
|---------|---------|
| Nome | Nome do gancho de e-mail |
| E-mail para| Endereços de e-mail que enviariam alerta para|
| External link | Campo opcional que permite um redirecionamento personalizado, como para notas de resolução de problemas. |
| Título de alerta de anomalia personalizado | Suportes de modelo de `${severity}` título, `${alertSettingName}` , , , , , , `${datafeedName}` `${metricName}` `${detectConfigName}` `${timestamp}` `${topDimension}` `${incidentCount}` , `${anomalyCount}`

Depois de clicar **em OK,** será criado um gancho de e-mail. Pode usá-lo em quaisquer definições de alerta para receber alertas de anomalias. 

### <a name="web-hook"></a>Gancho de teia

> [!Note]
> * Utilize o método de pedido **POST.**
> * O corpo de pedido será semelhante a:  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * Quando um gancho web é criado ou modificado, a API será chamada como um teste com um corpo de pedido vazio. A sua API precisa de devolver um código 200 HTTP.

Um gancho web é o ponto de entrada de todas as informações disponíveis no serviço Metrics Advisor, e chama uma api fornecida pelo utilizador quando um alerta é ativado. Todos os alertas podem ser enviados através de um gancho web.

Para criar um gancho web, terá de adicionar as seguintes informações:

|Parâmetro |Descrição  |
|---------|---------|
|Ponto final     | O endereço da API deve ser chamado quando um alerta é acionado.        |
|Nome de utilizador / Senha | Para autenticação no endereço API. Deixe isto preto se não for necessária a autenticação.         |
|Cabeçalho     | Cabeçalhos personalizados na chamada da API.        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="janela de criação de gancho web.":::

Quando uma notificação é empurrada através de um gancho web, você pode usar as seguintes APIs para obter detalhes do alerta. Desaponte a *tabela de tempos* e *alerteSettingGuid* no seu serviço API, que está a ser empurrado para, em seguida, use as seguintes consultas: 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

O Metrics Advisor também suporta a criação automática de um item de trabalho em Azure DevOps para rastrear problemas/bugs quando qualquer anomalia detetada. Todos os alertas podem ser enviados através dos ganchos Azure DevOps.

Para criar um gancho Azure DevOps, terá de adicionar as seguintes informações

|Parâmetro |Descrição  |
|---------|---------|
| Nome | Um nome para o gancho |
| Organização | A organização a que os seus DevOps pertencem |
| Project | O projeto específico em DevOps. |
| Token de Acesso |  Um símbolo para autenticar devOps. | 

> [!Note]
> Tem de conceder permissões de escrita se quiser que o Metrics Advisor crie itens de trabalho baseados em alertas de anomalias. Depois de criar ganchos, pode usá-los em qualquer uma das suas definições de alerta. Gerencie os ganchos na página **de definições** do gancho.

## <a name="add-or-edit-alert-settings"></a>Adicionar ou editar definições de alerta

Vá à página de detalhes das métricas para encontrar a secção **de definições de alerta,** no canto inferior esquerdo da página de detalhes das métricas. Lista todas as definições de alerta que se aplicam à configuração de deteção selecionada. Quando uma nova configuração de deteção é criada, não há definição de alerta e nenhum alerta será enviado.  
Pode utilizar o **add,** **editar** e **eliminar** ícones para modificar as definições de alerta.

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Item do menu de definições de alerta.":::

Clique nos botões **de adicionar** ou **editar** para obter uma janela para adicionar ou editar as definições de alerta.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Adicionar ou editar definições de alerta":::

**Nome de definição de** alerta : O nome desta definição de alerta. Será exibido no título de e-mail de alerta.

**Ganchos** : A lista de ganchos para enviar alertas para.

A secção marcada na imagem acima são as definições para uma configuração de deteção. Pode definir diferentes definições de alerta para diferentes configurações de deteção. Escolha a configuração do alvo utilizando a terceira lista de drop-down nesta janela. 

### <a name="filter-settings"></a>Definições de filtro 

Seguem-se as definições do filtro para uma configuração de deteção.

**Alerta Para** 4 opções para filtrar anomalias:

* **Anomalias em todas as séries** : Todas as anomalias serão incluídas no alerta.         
* **Anomalias no grupo da série** : Filtrar série por valores de dimensão. Definir valores específicos para algumas dimensões. As anomalias só serão incluídas no alerta quando a série corresponder ao valor especificado.       
* **Anomalias nas séries favoritas** : Apenas a série marcada como favorita será incluída no alerta.        |
* **Anomalias no top N de todas as séries** : Este filtro é para o caso de que você só se importa com a série cujo valor está no top N. Vamos olhar para trás alguns picos de tempo, e verificar se o valor da série nestes horários está no top N. Se a contagem "in top n" for maior do que o número especificado, a anomalia será incluída num alerta.        |

**As opções de anomalia do filtro** são um filtro adicional com as seguintes opções:

- **gravidade** : A anomalia só será incluída quando a gravidade da anomalia estiver dentro do intervalo especificado.
- **Snooze** : Pare temporariamente os alertas para anomalias nos próximos pontos N (período), quando acionado em alerta.
    - **snooze tipo** : Quando definido para **série,** uma anomalia desencadeada só irá sonecar a sua série. Para **a Metric** , uma anomalia desencadeada irá sonecar todas as séries desta métrica.
    - **número de soneca:** o número de pontos (período) para soneca.
    - **reset para não-sucessivas** : Quando selecionado, uma anomalia desencadeada só irá sonecar as próximas anomalias sucessivas. Se um dos seguintes pontos de dados não for uma anomalia, o soneca será reposto a partir desse ponto; Quando não for selecionado, uma anomalia desencadeada irá sonerá os próximos pontos n (período), mesmo que os pontos de dados sucessivos não sejam anomalias.
- **valor** (opcional) : Filtro por valor. Apenas valores de ponto que satisfaçam a condição, a anomalia será incluída. Se utilizar o valor correspondente de outra métrica, os nomes de dimensão das duas métricas devem ser consistentes.

Anomalias não filtradas serão enviadas em alerta.

### <a name="add-cross-metric-settings"></a>Adicione configurações métricas cruzadas

Clique **+ Adicione as definições métricas cruzadas** na página de definições de alerta para adicionar outra secção.

O seletor **do operador** é a relação lógica de cada secção, para determinar se enviam um alerta.


|Operador  |Descrição  |
|---------|---------|
|AND     | Envie apenas um alerta se uma série corresponder a cada secção de alerta, e todos os pontos de dados são anomalias. Se as métricas tiverem nomes de dimensão diferentes, um alerta nunca será acionado.         |
|OR     | Envie o alerta se pelo menos uma secção contiver anomalias.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="Operador para secção de definição de alerta múltiplo":::

## <a name="next-steps"></a>Passos seguintes

- [Ajustar a deteção de anomalias através de comentários](anomaly-feedback.md)
- [Diagnosticar um incidente.](diagnose-incident.md)
- [Configurar métricas e otimizar a configuração da deteção](configure-metrics.md)