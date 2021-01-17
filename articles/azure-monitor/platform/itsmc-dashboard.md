---
title: Investigação de erro usando painel de instrumentos
description: Este documento contém informações sobre a investigação de erros usando o dashboard
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9291689b362b5cbe651a72220196dd30b40745cf
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540749"
---
# <a name="error-investigation-using-the-dashboard"></a>Investigação de erro usando o painel

Esta página contém informações sobre o painel de conector ITSM. Este painel irá ajudá-lo a investigar o estado do seu conector ITSM.

## <a name="how-to-view-the-dashboard"></a>Como ver o painel

Para ver os erros no painel de instrumentos, deve seguir os próximos passos:

1. Em **Todos os recursos,** procure **o ServiceDesk(o *seu nome de espaço de trabalho)***:

   ![Screenshot que mostra recursos recentes no portal Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

2. Em **Fontes de Dados** do Espaço de Trabalho no painel esquerdo, selecione **ItsM Connections**:

   ![Screenshot que mostra o item do menu ITSM Connections.](media/itsmc-overview/add-new-itsm-connection.png)

3. Em **Resumo** no **Conector de Gestão** de Serviço de TI da caixa esquerda, selecione **Ver Resumo**:

    ![Screenshot que mostra o resumo da visualização.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Em **Resumo** no **Conector de Gestão** de Serviço de TI da caixa esquerda, clique no gráfico:

    ![Screenshot que mostra clique de gráfico.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Utilizando este painel de instrumentos poderá rever o estado e os erros no seu conector.
    ![Screenshot que mostra o estado do conector.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="dashboard-elements"></a>Elementos do painel de instrumentos

O painel contém informações sobre os alertas enviados para a ferramenta ITSM utilizando este conector.
O painel é dividido em quatro partes:

1. Artigo de trabalho Criado: O gráfico e a tabela abaixo contêm a contagem do item de trabalho por tipo. Se clicar no gráfico ou na mesa, pode ver mais detalhes sobre os itens de trabalho.
    ![Screenshot que mostra o item de trabalho criado.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. Computadores com impacto: As tabelas contêm detalhes sobre os itens de configuração que criaram itens de configuração.
    Ao clicar em linhas nas tabelas, pode obter mais detalhes sobre os itens de configuração.
    A tabela contém um número limitado de linhas se quiser ver toda a lista que pode clicar em "Ver tudo".
    ![Screenshot que mostra computadores com impacto.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. Estado do conector: O gráfico e a tabela abaixo contêm mensagens sobre o estado do conector. Ao clicar no gráfico nas linhas da tabela, pode obter mais detalhes sobre as mensagens do estado do conector.
    A tabela contém um número limitado de linhas se quiser ver toda a lista que pode clicar em "Ver tudo".
    ![Screenshot que mostra o estado do conector.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. Regras de alerta: As tabelas contêm a informação sobre o número de regras de alerta detetadas.
    Ao clicar em linhas nas tabelas, pode obter mais detalhes sobre as regras que foram detetadas.
    A tabela contém um número limitado de linhas se quiser ver toda a lista que pode clicar em "Ver tudo".
    ![Screenshot que mostra regras de alerta.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)