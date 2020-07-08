---
title: Criar regras analíticas personalizadas para detetar ameaças suspeitas com a Azure Sentinel Microsoft Docs
description: Use este tutorial para aprender a criar regras analíticas personalizadas para detetar ameaças suspeitas com Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77585112"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Tutorial: Criar regras analíticas personalizadas para detetar ameaças suspeitas

Depois de [ligar as suas fontes de dados](quickstart-onboard.md)   ao Azure Sentinel, pode criar regras personalizadas que podem procurar critérios específicos em todo o seu ambiente e gerar incidentes quando os critérios são compatíveis para que possa investigá-los. Este tutorial ajuda-o a criar regras personalizadas para detetar ameaças com o Azure Sentinel.

Este tutorial ajuda-o a detetar ameaças com o Azure Sentinel.
> [!div class="checklist"]
> * Criar regras analíticas
> * Automatizar respostas de ameaças

## <a name="create-custom-analytic-rules"></a>Criar regras analíticas personalizadas

Pode criar regras analíticas personalizadas para ajudá-lo a procurar os tipos de ameaças e anomalias que são suspeitas no seu ambiente. A regra garante que é notificado imediatamente, para que possa triagem, investigação e remediar as ameaças.

1. No portal Azure sob O Azure Sentinel, **selecione Analytics**.

1. Na barra de menu superior, selecione **+Criar** e **selecione a regra de consulta agendada.** Isto abre o **assistente de regras de Analytics**.

    ![Criar consulta agendada](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. No separador **Geral,** forneça um **nome**único, e uma **Descrição**. No campo **tático,** pode escolher entre categorias de ataques para classificar a regra. Desaperte o alerta **severidade** conforme necessário. Quando cria a regra, o seu **Estado** é **Ativado** por padrão, o que significa que será executado imediatamente após terminar de criá-la. Se não quiser que seja executado imediatamente, selecione **Desativado**, e a regra será adicionada ao separador **regras Ative** e pode ativá-la a partir daí quando precisar.

    ![Comece a criar uma regra analítica personalizada](media/tutorial-detect-threats-custom/general-tab.png)

1. No separador lógica de **regras Definida,** pode escrever uma consulta diretamente no campo **de consulta regra,** ou criar a consulta no Log Analytics e, em seguida, copiá-la e colá-la lá.
 
   ![Criar consulta em Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Consulte a área **de pré-visualização** de Resultados para a direita, onde o Azure Sentinel mostra o número de resultados (eventos de registo) que a consulta irá gerar, alterando o voo à medida que escreve e configura a sua consulta. O gráfico mostra o número de resultados durante o período de tempo definido, que é determinado pelas definições na secção **de agendamento de consultas.**
    - Se vir que a sua consulta desencadearia alertas demasiado ou demasiado frequentes, pode definir uma linha de base na secção **limiar de alerta.**

      Aqui está uma consulta de amostra que o alertaria quando um número anómalo de recursos é criado na Atividade Azure.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > O comprimento da consulta deve ser entre 1 e 10.000 caracteres e não pode conter \* "pesquisa" ou "união". \*

    1. Utilize a secção **de entidades** do Mapa para ligar parâmetros dos resultados da sua consulta a entidades reconhecidas pelo Azure Sentinel. Estas entidades formam a base para uma análise mais aprofundada, incluindo o agrupamento de alertas em incidentes no **separador de definições de Incidentes.**
    1. Na secção **de agendamento de consultas,** definir os seguintes parâmetros:

       1. **Desacomo se questione** para controlar a frequência com que a consulta é executada - com a frequência de cada 5 minutos ou com frequência como uma vez por dia.

       1. Defina **os dados do Lookup do último** para determinar o período de tempo dos dados abrangidos pela consulta - por exemplo, pode consultar os últimos 10 minutos de dados, ou as últimas 6 horas de dados.

       > [!NOTE]
       > Estas duas configurações são independentes uma da outra, até um ponto. Pode executar uma consulta num curto intervalo que cubra um período de tempo mais longo do que o intervalo (na verdade, tendo consultas sobrepostas), mas não pode executar uma consulta num intervalo superior ao período de cobertura, caso contrário terá lacunas na cobertura geral da consulta.

    1. Utilize a secção **de limiar de alerta** para definir uma linha de base. Por exemplo, **deteter Alerta de Geração quando** o número de resultados de consulta é maior do **que** e introduza o número 1000 se quiser que a regra gere um alerta apenas se a consulta gerar mais de 1000 resultados cada vez que executa. Como este é um campo obrigatório, se não quiser definir uma linha de base – isto é, se quiser que o seu alerta registe cada evento – introduza 0 no campo de números.

    1. Na secção **Supressão,** pode rodar a **consulta stop running após o alerta for gerada** definição **Se,** uma vez que recebe um alerta, pretender suspender o funcionamento desta regra por um período de tempo superior ao intervalo de consulta. Se ligar isto, deve definir **parar de fazer a consulta durante** o tempo que a consulta deve parar de funcionar, até 24 horas.

1. No separador **Definições de Incidentes,** pode escolher se e como o Azure Sentinel transforma alertas em incidentes acionáveis. Se este separador for deixado em paz, o Azure Sentinel criará um único incidente separado de cada alerta. Pode optar por não ter incidentes criados, ou agrupar vários alertas num único incidente, alterando as definições neste separador.

    1. Na secção Definições de **Incidentes,** **Crie incidentes a partir de alertas desencadeados por esta regra de análise** é definido por padrão para **Enabled**, o que significa que O Azure Sentinel criará um único incidente separado de cada alerta desencadeado pela regra.<br></br>Se não quiser que esta regra resulte na criação de incidentes (por exemplo, se esta regra for apenas para recolher informações para análise posterior), desabro quanto a **Deficientes**.

    1. Na secção **de agrupamento de alertas,** se pretender que um único incidente seja gerado a partir de um grupo de alertas semelhantes ou recorrentes, desemocione **os alertas relacionados com o Grupo, desencadeados por esta regra de análise, em incidentes** a **Enabled**, e desafine os seguintes parâmetros.

    1. **Limitar o grupo aos alertas criados dentro do prazo selecionado:**<br></br> Determine o prazo dentro do qual os alertas semelhantes ou recorrentes serão agrupados. Todos os alertas correspondentes dentro deste prazo gerarão colectivamente um incidente ou um conjunto de incidentes (dependendo das definições de agrupamento abaixo). Alertas fora deste período de tempo gerarão um incidente separado ou conjunto de incidentes.

    2. **Alertas de grupo desencadeados por esta regra de análise num único incidente:** Escolha a base em que os alertas serão agrupados em conjunto:

        - **O grupo alerta para um único incidente se todas as entidades corresponderem:** <br></br>Os alertas são agrupados em conjunto se partilharem valores idênticos para cada uma das entidades mapeadas (definidas no separador lógica da regra definida acima). Esta é a definição recomendada.

        - **Agrupam todos os alertas desencadeados por esta regra num único incidente:** <br></br>Todos os alertas gerados por esta regra são agrupados mesmo que não partilhem valores idênticos.

        - **O grupo alerta para um único incidente se as entidades selecionadas corresponderem:** <br></br>Os alertas são agrupados se partilharem valores idênticos para algumas das entidades mapeadas (que pode selecionar a partir da lista de drop-down). É melhor utilizar esta definição se, por exemplo, pretender criar incidentes separados com base nos endereços IP de origem ou alvo.

    3. **Reaberta incidentes de correspondência fechados**: Se um incidente tiver sido encerrado (o que significa que a questão subjacente foi resolvido), e posteriormente ser gerado outro alerta que teria sido agrupado nesse incidente, defina esta definição para **Enabled** se quiser que o incidente fechado seja reaberto, e saia como **Desativado** se quiser que o alerta crie um novo incidente.

1. No separador **respostas automatizadas,** selecione quaisquer livros de reprodução que pretenda executar automaticamente quando um alerta é gerado pela regra personalizada. Para obter mais informações sobre a criação e automatização de livros, consulte [Responder às ameaças](tutorial-respond-threats-playbook.md).

1. Selecione **Rever e criar** para rever todas as definições da sua nova regra de alerta e, em seguida, selecione Criar para **rubricar a sua regra de alerta**.
  
1. Após a criação do alerta, uma regra personalizada é adicionada à tabela de acordo com **as regras Ative**. A partir desta lista pode ativar, desativar ou eliminar cada regra.

1. Para ver os resultados das regras de alerta que cria, vá à página **Incidentes,** onde pode triagem, investigação de [incidentes](tutorial-investigate-cases.md)e remediar as ameaças.


> [!NOTE]
> Os alertas gerados no Azure Sentinel estão disponíveis através da [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Para obter mais informações, consulte a documentação de [alertas de segurança do gráfico da Microsoft](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu a detetar ameaças usando Azure Sentinel.

Para aprender a automatizar as suas respostas a ameaças, [Crie respostas automáticas de ameaças em Azure Sentinel](tutorial-respond-threats-playbook.md).

