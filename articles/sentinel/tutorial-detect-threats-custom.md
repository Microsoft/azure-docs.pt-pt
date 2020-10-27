---
title: Criar regras de análise personalizadas para detetar ameaças com o Azure Sentinel Microsoft Docs
description: Use este tutorial para aprender a criar regras de análise personalizadas para detetar ameaças de segurança com o Azure Sentinel. Aproveite o agrupamento de eventos e o agrupamento de alertas e compreenda o AUTO DISABLED.
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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: b5cf2b473b6b08dcd77f1a8612d19cea26fc16b9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546760"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Tutorial: Criar regras de análise personalizadas para detetar ameaças

Uma vez que tenha [conectado as suas fontes](quickstart-onboard.md) de dados ao Azure Sentinel, pode criar regras personalizadas que podem procurar critérios específicos em todo o seu ambiente e gerar incidentes quando os critérios são compatíveis para que possa investigá-los. Este tutorial ajuda-o a criar regras personalizadas para detetar ameaças com o Azure Sentinel.

Este tutorial ajuda-o a detetar ameaças com o Azure Sentinel.
> [!div class="checklist"]
> * Criar regras de análise
> * Automatizar respostas de ameaças

## <a name="create-custom-analytics-rules"></a>Criar regras de análise personalizadas

Pode criar regras de análise personalizadas para ajudá-lo a procurar os tipos de ameaças e anomalias que são suspeitas no seu ambiente. A regra garante que é notificado imediatamente, para que possa triagem, investigação e remediar as ameaças.

1. No portal Azure sob O Azure Sentinel, **selecione Analytics** .

1. Na barra de menu superior, selecione **+Criar** e **selecione a regra de consulta agendada.** Isto abre o **assistente de regras de Analytics** .

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query.png" alt-text="Criar consulta agendada":::

1. No separador **Geral,** forneça um **nome** único e uma **descrição.** No campo **tático,** pode escolher entre categorias de ataques para classificar a regra. Desaperte o alerta **severidade** conforme necessário. Quando cria a regra, o seu **Estado** é **Ativado** por padrão, o que significa que será executado imediatamente após terminar de criá-la. Se não quiser que seja executado imediatamente, selecione **Desativado** , e a regra será adicionada ao separador **regras Ative** e pode ativá-la a partir daí quando precisar.

    ![Comece a criar uma regra de análise personalizada](media/tutorial-detect-threats-custom/general-tab.png)

1. No separador lógica de **regras Definida,** pode escrever uma consulta diretamente no campo **de consulta regra,** ou criar a consulta no Log Analytics e, em seguida, copiá-la e colá-la lá.
 
   ![Criar consulta em Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Consulte a área **de pré-visualização** de Resultados para a direita, onde o Azure Sentinel mostra o número de resultados (eventos de registo) que a consulta irá gerar, alterando o voo à medida que escreve e configura a sua consulta. O gráfico mostra o número de resultados durante o período de tempo definido, que é determinado pelas definições na secção **de agendamento de consultas.**
    - Se vir que a sua consulta desencadearia alertas demasiado ou demasiado frequentes, pode definir uma linha de base na secção **limiar de alerta.**

      Aqui está uma consulta de amostra que o alertaria quando um número anómalo de recursos é criado na Atividade Azure.

      ```kusto
      AzureActivity
      | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
      | where ActivityStatus == "Succeeded"
      | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
      ```

        > [!NOTE]
        > - O comprimento da consulta deve ser entre 1 e 10.000 caracteres e não pode conter \* "pesquisa" ou "união". \*
        >
        > - A utilização de funções ADX para criar consultas do Azure Data Explorer dentro da janela de consulta Log Analytics **não é suportada** .

    1. Utilize a secção **de entidades** do Mapa para ligar parâmetros dos resultados da sua consulta a entidades reconhecidas pelo Azure Sentinel. Estas entidades formam a base para uma análise mais aprofundada, incluindo o agrupamento de alertas em incidentes no **separador de definições de Incidentes.**
  
    1. Na secção **de agendamento de consultas,** definir os seguintes parâmetros:

       1. **Desacomo se questione** para controlar a frequência com que a consulta é executada - com a frequência de cada 5 minutos ou com frequência como uma vez por dia.

       1. Defina **os dados do Lookup do último** para determinar o período de tempo dos dados abrangidos pela consulta - por exemplo, pode consultar os últimos 10 minutos de dados, ou as últimas 6 horas de dados.

          > [!NOTE]
          > **Intervalos de consulta e período de retrocesso**
          > - Estas duas configurações são independentes uma da outra, até um ponto. Pode executar uma consulta num curto intervalo que cubra um período de tempo mais longo do que o intervalo (na verdade, tendo consultas sobrepostas), mas não pode executar uma consulta num intervalo superior ao período de cobertura, caso contrário terá lacunas na cobertura geral da consulta.
          >
          > **Atraso na ingestão**
          > - Para ter em conta **a latência** que pode ocorrer entre a geração de um evento na fonte e a sua ingestão no Azure Sentinel, e para garantir uma cobertura completa sem duplicação de dados, o Azure Sentinel executa regras de análise programadas com um **atraso de cinco minutos** em função da hora programada.

    1. Utilize a secção **de limiar de alerta** para definir uma linha de base. Por exemplo, **desacordo O alerta de Geração quando** o número de resultados de consulta é maior do **que** e introduza o número 1000 se quiser que a regra gere um alerta apenas se a consulta devolver mais de 1000 resultados cada vez que for executado. Este é um campo obrigatório, por isso, se não quiser definir uma linha de base – ou seja, se quiser que o seu alerta registe todos os eventos – introduza 0 no campo de números.
    
    1. No **âmbito do agrupamento event,** escolha uma de duas formas de lidar com o agrupamento de **eventos** em **alertas:** 

       - **Agrupe todos os eventos num único alerta** (a definição predefinida). A regra gera um único alerta cada vez que executa, desde que a consulta retorne mais resultados do que o limiar de **alerta** especificado acima. O alerta inclui um resumo de todos os eventos devolvidos nos resultados. 

       - **Desencadeie um alerta para cada evento.** A regra gera um alerta único para cada evento devolvido pela consulta. Isto é útil se quiser que os eventos sejam exibidos individualmente, ou se pretender agrupar os mesmos por determinados parâmetros - por utilizador, nome de anfitrião ou outra coisa qualquer. Pode definir estes parâmetros na consulta.
    
       Atualmente, o número de alertas que uma regra pode gerar está limitado a 20. Se, numa regra específica, **o agrupamento de eventos** estiver definido para **desencadear um alerta para cada evento** , e a consulta da regra retorna mais de 20 eventos, cada um dos primeiros 19 eventos gerará um alerta único, e o 20º alerta irá resumir todo o conjunto de eventos devolvidos. Por outras palavras, o vigésimo alerta é o que teria sido gerado no âmbito do **Grupo todos os eventos numa única** opção de alerta.

       > [!NOTE]
       > Qual é a diferença entre **Eventos** e **Alertas?**
       >
       > - Um **evento** é uma descrição de uma única ocorrência. Por exemplo, uma única entrada num ficheiro de registo pode contar como um evento. Neste contexto, um evento refere-se a um único resultado devolvido por uma consulta numa regra de análise.
       >
       > - Um **alerta** é uma coleção de eventos que, juntos, são significativos do ponto de vista da segurança. Um alerta poderia conter um único evento se o evento tivesse implicações significativas em segurança - um login administrativo de um país estrangeiro fora do horário de expediente, por exemplo.
       >
       > - A propósito, o que são **incidentes?** A lógica interna de Azure Sentinel cria **incidentes** a partir de **alertas** ou grupos de alertas. A fila dos incidentes é o ponto focal do trabalho dos analistas - triagem, investigação e reparação.
       > 
       > O Azure Sentinel ingere eventos crus de algumas fontes de dados, e já processados alertas de outras pessoas. É importante notar com qual está a lidar a qualquer momento.

       > [!IMPORTANT]
       > O agrupamento de eventos está atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. Na secção **Supressão,** pode rodar a **consulta stop running após o alerta for gerada** definição **Se,** uma vez que recebe um alerta, pretender suspender o funcionamento desta regra por um período de tempo superior ao intervalo de consulta. Se ligar isto, deve definir **parar de fazer a consulta durante** o tempo que a consulta deve parar de funcionar, até 24 horas.

1. No separador **Definições de Incidentes,** pode escolher se e como o Azure Sentinel transforma alertas em incidentes acionáveis. Se este separador for deixado em paz, o Azure Sentinel criará um único incidente separado de cada alerta. Pode optar por não ter incidentes criados, ou agrupar vários alertas num único incidente, alterando as definições neste separador.

   > [!IMPORTANT]
   > O separador de definições de incidentes encontra-se atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. Na secção Definições de **Incidentes,** **Crie incidentes a partir de alertas desencadeados por esta regra de análise** é definido por padrão para **Enabled** , o que significa que O Azure Sentinel criará um único incidente separado de cada alerta desencadeado pela regra.
       - Se não quiser que esta regra resulte na criação de incidentes (por exemplo, se esta regra for apenas para recolher informações para análise posterior), desabro quanto a **Deficientes** .

    1. Na secção **de agrupamento de alertas,** se pretender que um único incidente seja gerado a partir de um grupo de até 150 alertas semelhantes ou recorrentes (ver nota), detete **os alertas relacionados com o Grupo, desencadeados por esta regra de análise, em incidentes** a **Enabled,** e definir os seguintes parâmetros.

    - **Limitar o grupo aos alertas criados dentro do prazo selecionado** : Determine o prazo dentro do qual os alertas semelhantes ou recorrentes serão agrupados. Todos os alertas correspondentes dentro deste prazo gerarão colectivamente um incidente ou um conjunto de incidentes (dependendo das definições de agrupamento abaixo). Alertas fora deste período de tempo gerarão um incidente separado ou conjunto de incidentes.

    - **Alertas de grupo desencadeados por esta regra de análise num único incidente:** Escolha a base em que os alertas serão agrupados em conjunto:

        - **O grupo alerta para um único incidente se todas as entidades corresponderem:** Os alertas são agrupados se partilharem valores idênticos para cada uma das entidades mapeadas (definidas no separador lógica de regra definida acima). Esta é a definição recomendada.

        - **Grupo todos os alertas desencadeados por esta regra num único incidente** : Todos os alertas gerados por esta regra são agrupados mesmo que não partilhem valores idênticos.

        - **O grupo alerta para um único incidente se as entidades selecionadas corresponderem:** Os alertas são agrupados se partilharem valores idênticos para algumas das entidades mapeadas (que pode selecionar a partir da lista de suspensos). É melhor utilizar esta definição se, por exemplo, pretender criar incidentes separados com base nos endereços IP de origem ou alvo.

    - **Reaberta incidentes de correspondência fechados** : Se um incidente tiver sido resolvido e fechado, e mais tarde em outro alerta for gerado que deve pertencer a esse incidente, defina esta definição para **Enabled** se quiser que o incidente fechado seja reaberto, e deixe como **Desativado** se quiser que o alerta crie um novo incidente.
    
        > [!NOTE]
        > Até 150 alertas podem ser agrupados num único incidente. Se mais de 150 alertas forem gerados por uma regra que os agrupará num único incidente, um novo incidente será gerado com os mesmos detalhes do incidente que o original, e os alertas em excesso serão agrupados no novo incidente.

1. No separador **respostas automatizadas,** selecione quaisquer livros de reprodução que pretenda executar automaticamente quando um alerta é gerado pela regra personalizada. Para obter mais informações sobre a criação e automatização de livros, consulte [Responder às ameaças](tutorial-respond-threats-playbook.md).

1. Selecione **Rever e criar** para rever todas as definições da sua nova regra de alerta e, em seguida, selecione Criar para **rubricar a sua regra de alerta** .
  
1. Após a criação do alerta, uma regra personalizada é adicionada à tabela de acordo com **as regras Ative** . A partir desta lista pode ativar, desativar ou eliminar cada regra.

1. Para ver os resultados das regras de alerta que cria, vá à página **Incidentes,** onde pode triagem, investigação de [incidentes](tutorial-investigate-cases.md)e remediar as ameaças.


> [!NOTE]
> Os alertas gerados no Azure Sentinel estão disponíveis através da [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Para obter mais informações, consulte a documentação de [alertas de segurança do gráfico da Microsoft](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Uma regra programada não executou, ou aparece com AUTO DISABLED adicionado ao nome

É uma ocorrência rara que uma regra de consulta programada não funciona, mas pode acontecer. O Azure Sentinel classifica as falhas na frente como transitórias ou permanentes, com base no tipo específico da falha e nas circunstâncias que o levaram.

#### <a name="transient-failure"></a>Falha transitória

Uma falha transitória ocorre devido a uma circunstância que é temporária e em breve voltará ao normal, altura em que a execução da regra será bem sucedida. Alguns exemplos de falhas que Azure Sentinel classifica como transitórios são:

- Uma consulta de regras demora muito tempo a esgotar-se e a esgotar-se.
- Problemas de conectividade entre fontes de dados e Log Analytics, ou entre Log Analytics e Azure Sentinel.
- Qualquer outra falha nova e desconhecida é considerada transitória.

Em caso de falha transitória, Azure Sentinel continua a tentar executar a regra novamente após intervalos pré-determinados e cada vez maiores, até um ponto. Depois disso, a regra só voltará a ser executada na sua próxima hora programada. Uma regra nunca será desativada automaticamente devido a uma falha transitória.

#### <a name="permanent-failure---rule-auto-disabled"></a>Falha permanente - regra auto-desativada

Uma falha permanente ocorre devido a uma alteração das condições que permitem a execução da regra, que sem intervenção humana não voltará ao seu estatuto anterior. Seguem-se alguns exemplos de falhas que são classificadas como permanentes:

- O espaço de trabalho alvo (sobre o qual a consulta de regras funcionou) foi eliminado.
- A tabela-alvo (sobre a qual a consulta de regras funcionou) foi suprimida.
- Azure Sentinel tinha sido removido do espaço de trabalho do alvo.
- Uma função utilizada pela consulta de regras já não é válida; foi modificado ou removido.
- As permissões a uma das fontes de dados da consulta de regras foram alteradas.
- Uma das fontes de dados da consulta de regras foi eliminada ou desligada.

**Em caso de um número pré-determinado de falhas permanentes consecutivas, do mesmo tipo e da mesma regra,** Azure Sentinel para de tentar executar a regra, e também toma os seguintes passos:

- Desativa a regra.
- Adiciona as palavras **"AUTO DISABLED"** ao início do nome da regra.
- Acrescenta a razão da falha (e da desativação) à descrição da regra.

Pode determinar facilmente a presença de quaisquer regras de desativadas automáticas, classificando a lista de regras pelo nome. As regras de desativação automática estarão no topo da lista.

Os gestores da SOC devem verificar regularmente a lista de regras para a presença de regras de desativação automática.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a detetar ameaças usando Azure Sentinel.

Para aprender a automatizar as suas respostas a ameaças, [Crie respostas automáticas de ameaças em Azure Sentinel](tutorial-respond-threats-playbook.md).

