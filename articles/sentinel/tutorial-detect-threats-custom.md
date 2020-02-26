---
title: Criar regras analíticas personalizadas para detetar ameaças suspeitas com o Azure Sentinel Microsoft Docs
description: Use este tutorial para aprender a criar regras analíticas personalizadas para detetar ameaças suspeitas com o Azure Sentinel.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585112"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Tutorial: Criar regras analíticas personalizadas para detetar ameaças suspeitas

Depois de [ligar as suas fontes](quickstart-onboard.md) de dados ao Azure Sentinel, pode criar regras personalizadas que possam procurar critérios específicos em todo o seu ambiente e gerar incidentes quando os critérios são combinados para que possa investigá-los. Este tutorial ajuda-o a criar regras personalizadas para detetar ameaças com o Azure Sentinel.

Este tutorial ajuda-o a detetar ameaças com o Azure Sentinel.
> [!div class="checklist"]
> * Criar regras analíticas
> * Automatizar respostas de ameaças

## <a name="create-custom-analytic-rules"></a>Criar regras analíticas personalizadas

Você pode criar regras analíticas personalizadas para ajudá-lo a procurar os tipos de ameaças e anomalias que são suspeitas no seu ambiente. A regra garante que seja notificado imediatamente, para que possa triagem, investigar e remediar as ameaças.

1. No portal Azure sob o Azure Sentinel, selecione **Analytics**.

1. Na barra de menu superior, **selecione +Criar** e selecione regra de **consulta agendada**. Isto abre o assistente de **regras da Analytics.**

    ![Criar consulta programada](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. No separador **Geral,** forneça um **nome**único e uma **Descrição.** No campo **das Táticas,** pode escolher entre categorias de ataques para classificar a regra. Destete o alerta **Severidade** conforme necessário. Quando criar a regra, o seu **Estado** é **ativado** por padrão, o que significa que será executado imediatamente após terminar de criá-la. Se não quiser que seja executado imediatamente, selecione **Desativado**, e a regra será adicionada ao seu separador de **regras Ative** e pode permitir a partir daí quando precisar.

    ![Comece a criar uma regra analítica personalizada](media/tutorial-detect-threats-custom/general-tab.png)

1. No separador lógica da **regra definida,** pode escrever uma consulta diretamente no campo de consulta de **regras,** ou criar a consulta no Log Analytics e, em seguida, copiá-la e colar lá.
 
   ![Criar consulta em Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Consulte a área de **pré-visualização dos Resultados** à direita, onde o Azure Sentinel mostra o número de resultados (eventos de registo) que a consulta gerará, alterando no voo à medida que escreve e configura a sua consulta. O gráfico mostra o número de resultados durante o período de tempo definido, que é determinado pelas definições na secção de agendamento de **consulta.**
    - Se vir que a sua consulta desencadearia demasiados ou alertas muito frequentes, pode definir uma linha de base na secção de limiar de **alerta.**

      Aqui está uma consulta de amostra que o alertaria quando um número anómalo de recursos é criado na Atividade Azure.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > O comprimento da consulta deve ser entre 1 e 10.000 caracteres e não pode conter "pesquisa \*" ou "união \*".

    1. Utilize a secção de **entidades do Mapa** para ligar parâmetros dos resultados da sua consulta a entidades reconhecidas pelo Azure Sentinel. Estas entidades formam a base para uma análise mais aprofundada, incluindo o agrupamento de alertas em incidentes no separador **de definições de Incidentes.**
    1. Na secção de agendamento de **consultas,** defina os seguintes parâmetros:

       1. Definir **Executar consulta cada** vez mais para controlar a frequência da consulta - tão frequentemente como a cada 5 minutos ou tão raramente como uma vez por dia.

       1. Defina **os dados de pesquisa do último** para determinar o período de tempo dos dados abrangidos pela consulta - por exemplo, pode consultar os últimos 10 minutos de dados, ou as últimas 6 horas de dados.

       > [!NOTE]
       > Estas duas configurações são independentes umas das outras, até um ponto. Pode fazer uma consulta num curto intervalo, cobrindo um período de tempo mais longo do que o intervalo (com efeito, ter consultas sobrepostas), mas não pode fazer uma consulta num intervalo que exceda o período de cobertura, caso contrário terá lacunas na cobertura global de consulta.

    1. Utilize a secção de limiar de **alerta** para definir uma linha de base. Por exemplo, definir **Gerar alerta quando o número de resultados de consulta** para É maior do **que** e insira o número 1000 se quiser que a regra gere um alerta apenas se a consulta gerar mais de 1000 resultados cada vez que corre. Como este é um campo necessário, se não quiser definir uma linha de base – isto é, se quiser que o seu alerta registe todos os eventos – insira 0 no campo de números.

    1. Na secção **Supressão,** pode rodar a **consulta stop running após o alerta é gerado** regulação **se,** uma vez que você tiver um alerta, você quer suspender o funcionamento desta regra por um período de tempo superior ao intervalo de consulta. Se ligar isto, tem de definir **a consulta para parar** de fazer o tempo que a consulta deve parar de funcionar, até 24 horas.

1. No separador **Definições** de Incidentes, pode escolher se e como o Azure Sentinel transforma alertas em incidentes atol. Se este separador for deixado sozinho, o Azure Sentinel criará um único incidente separado de cada alerta. Pode optar por não ter incidentes criados, ou agrupar vários alertas num único incidente, alterando as definições neste separador.

    1. Na secção **Definições** de **Incidentes, criar incidentes a partir de alertas desencadeados por esta regra de análise** é definido por padrão para **Enabled**, o que significa que o Azure Sentinel criará um único incidente separado de cada alerta desencadeado pela regra.<br></br>Se não quiser que esta regra resulte na criação de quaisquer incidentes (por exemplo, se esta regra for apenas para recolher informações para análise posterior), dere a informação para **deficientes**.

    1. Na secção de agrupamento de **Alerta,** se pretender que um único incidente seja gerado a partir de um grupo de alertas semelhantes ou **recorrentes, coloque alertas relacionados com o Grupo, desencadeados por esta regra de análise, em incidentes** à **Enabled,** e defina os seguintes parâmetros.

    1. **Limite o grupo aos alertas criados dentro do prazo selecionado:**<br></br> Determine o prazo dentro do qual os alertas semelhantes ou recorrentes serão agrupados. Todos os alertas correspondentes dentro deste prazo gerarão coletivamente um incidente ou um conjunto de incidentes (dependendo das definições de agrupamento abaixo). Os alertas fora deste período de tempo gerarão um incidente separado ou um conjunto de incidentes.

    2. **Alertas de grupo desencadeados por esta regra de análise num único incidente por**: Escolha a base em que os alertas serão agrupados:

        - **O grupo alerta para um único incidente se todas as entidades corresponderem:** <br></br>Os alertas são agrupados se partilharem valores idênticos para cada uma das entidades mapeadas (definidas no separador lógico de regra definida acima). Esta é a definição recomendada.

        - **Grupo todos os alertas desencadeados por esta regra num único incidente:** <br></br>Todos os alertas gerados por esta regra são agrupados, mesmo que não partilhem valores idênticos.

        - **O grupo alerta para um único incidente se as entidades selecionadas corresponderem:** <br></br>Os alertas são agrupados se partilharem valores idênticos para algumas das entidades mapeadas (que pode selecionar na lista de lançamentos). Pode querer utilizar esta definição se, por exemplo, pretender criar incidentes separados com base na fonte ou endereços IP de destino.

    3. **Reabrir incidentes fechados**de correspondência : Se um incidente foi encerrado (o que significa que a questão subjacente foi resolvida), e posteriormente é gerado outro alerta que teria sido agrupado nesse incidente, definir esta definição para **Enabled** se quiser que o incidente fechado seja reaberto, e deixe como **Deficiente** se quiser que o alerta crie um novo incidente.

1. No separador **de respostas Automatizadas,** selecione quaisquer playbooks que pretenda executar automaticamente quando um alerta for gerado pela regra personalizada. Para obter mais informações sobre a criação e automatização de playbooks, consulte [Responder a ameaças](tutorial-respond-threats-playbook.md).

1. Selecione **Rever e crie** para rever todas as definições para a sua nova regra de alerta e, em seguida, selecione **Criar para inicializar a sua regra**de alerta .
  
1. Após a criação do alerta, é adicionada uma regra personalizada à tabela de acordo com **as regras ativas**. A partir desta lista pode ativar, desativar ou eliminar cada regra.

1. Para ver os resultados das regras de alerta que cria, vá à página **Incidents,** onde pode triagem, [investigar incidentes](tutorial-investigate-cases.md)e remediar as ameaças.


> [!NOTE]
> Os alertas gerados no Azure Sentinel estão disponíveis através da [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Para mais informações, consulte a documentação de [alertas](https://aka.ms/graphsecurityreferencebetadocs)da Microsoft Graph Security .

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a detetar ameaças usando o Azure Sentinel.

Para aprender a automatizar as suas respostas a ameaças, [instale respostas de ameaça automatizadas no Azure Sentinel](tutorial-respond-threats-playbook.md).

