---
title: Crie regras analíticas personalizadas para detectar ameaças suspeitas com o Azure Sentinel | Microsoft Docs
description: Use este tutorial para aprender a criar regras analíticas personalizadas para detectar ameaças suspeitas com o Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 18c11198f6b81e72e371b3ab06ed3a7330078c52
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023783"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Tutorial: Criar regras analíticas personalizadas para detectar ameaças suspeitas

Depois de [conectar suas fontes de dados](quickstart-onboard.md) to Azure Sentinel, você pode criar regras personalizadas que podem Pesquisar critérios específicos em seu ambiente e gerar incidentes quando os critérios forem correspondidos, para que você possa investigar-los. Este tutorial ajuda você a criar regras personalizadas para detectar ameaças com o Azure Sentinel.

Este tutorial ajuda você a detectar ameaças com o Azure Sentinel.
> [!div class="checklist"]
> * Criar regras analíticas
> * Automatizar respostas de ameaças

## <a name="create-custom-analytic-rules"></a>Criar regras analíticas personalizadas

Você pode criar regras analíticas personalizadas para ajudá-lo a Pesquisar os tipos de ameaças e anomalias suspeitas em seu ambiente. A regra garante que você seja notificado imediatamente, para que você possa fazer a triagem, investigar e corrigir as ameaças.

1. No portal do Azure em Azure Sentinel, selecione **análise**.

1. Na barra de menus superior, selecione **+ criar** e selecione **regra de consulta agendada**. Isso abre o **Assistente de criação de regra personalizada**.

    ![Criar consulta agendada](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Na guia **geral** , forneça um nome descritivo e uma descrição. Defina a **severidade do alerta** conforme necessário. Ao criar a regra, você pode habilitá-la, o que fará com que ela seja executada imediatamente depois que você terminar de criá-la. Se você criá-lo como desabilitado, a regra será adicionada à guia **regras ativas** e você poderá habilitá-la a partir daí, quando precisar.

    ![Começar a criar uma regra analítica personalizada](media/tutorial-detect-threats-custom/general-tab.png)

1. Na guia **configurações** , você pode gravar uma consulta diretamente ou criar a consulta em log Analytics e, em seguida, colá-la no campo de **consulta de pesquisa** . Conforme você altera e configura sua consulta, o Azure Sentinel simula os resultados da consulta na janela de **visualização de resultados** , à direita. Isso permite que você se aprofunde na quantidade de dados que seria gerado em um intervalo de tempo específico para o alerta que você está criando. A quantidade depende do que você definiu para **Executar consulta a cada** e **Pesquisar dados do último**. Se você vir que, em média, seu alerta dispararia alertas com muita frequência, você poderá definir o número de resultados em maior, para que esteja acima da sua linha de base média.

   ![Criar consulta no Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   Aqui está um exemplo de consulta que alertaria quando um número anormal de recursos for criado na atividade do Azure.

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > O comprimento da consulta deve estar entre 1 e 1, 0000 caracteres e não pode conter "Search \*" ou "Union \*".

    1. Em **agendamento de consulta**, defina os seguintes parâmetros:

        1.  Defina **Executar consulta a cada** para definir a **frequência** com a qual a consulta é executada com frequência a cada 5 minutos ou com pouca frequência como uma vez por dia.

        1.  Defina **dados de pesquisa do último** para controlar a janela de tempo para a quantidade de dados em que a consulta é executada, por exemplo, pode ser executada a cada hora, em até 60 minutos de dados.

        1. Você pode definir o Azure Sentinel para **interromper a execução da consulta depois que o alerta for gerado** se você quiser apenas receber um alerta uma vez depois que ele ocorrer. Você deve definir a janela durante a qual a consulta deve parar de ser executada, até 24 horas.

    1. Defina condições de gatilho de alerta em **gatilho de alerta**. Em **mapeamento de entidade**, você pode mapear as colunas em sua consulta para os campos de entidade reconhecidos pelo Azure Sentinel. Para cada campo, mapeie a coluna relevante na consulta que você criou em Log Analytics ao campo de entidade apropriado. Cada entidade inclui vários campos, por exemplo, SID e GUID. Você pode mapear a entidade de acordo com qualquer campo, não apenas a entidade de nível superior.

1.  Na guia **automatizar respostas** , selecione os guias estratégicos que você deseja executar automaticamente quando um alerta for gerado pela regra personalizada. Para obter mais informações sobre como criar e automatizar os guias estratégicos, consulte [responder a ameaças](tutorial-respond-threats-playbook.md).

    ![Automatizar a resposta a ameaças no Azure Sentinel](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. Selecione **revisão** para examinar todas as configurações de sua nova regra de alerta e, em seguida, selecione **criar para inicializar a regra de alerta**.

   ![Examine sua consulta personalizada](media/tutorial-detect-threats-custom/review-tab.png)

1.  Depois que o alerta é criado, uma regra personalizada é adicionada à tabela em **regras ativas**. Nessa lista, você pode habilitar, desabilitar ou excluir cada regra.

1.  Para exibir os resultados das regras de alerta que você cria, vá para a página **incidentes** , na qual você pode fazer a triagem, [investigar incidentes](tutorial-investigate-cases.md)e corrigir as ameaças.


> [!NOTE]
> Os alertas gerados no Azure Sentinel estão disponíveis por meio do [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Para obter mais informações, consulte a [documentação de alertas de segurança do Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a começar a detectar ameaças usando o Azure Sentinel.

Para saber como automatizar suas respostas a ameaças, [Configure as respostas de ameaças automatizadas no Azure Sentinel](tutorial-respond-threats-playbook.md).

