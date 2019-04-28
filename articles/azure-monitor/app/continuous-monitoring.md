---
title: Monitorização contínua do seu pipeline de lançamento de DevOps com o Azure DevOps e o Azure Application Insights | Documentos da Microsoft
description: Fornece instruções para configurar rapidamente a monitorização contínua com o Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 41999defb01e024773b6364f169a1ce3b1377237
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60902387"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Adicionar a monitorização contínua para o seu pipeline de lançamento

Serviços de DevOps do Azure integra-se com o Azure Application Insights para permitir a monitorização contínua do seu pipeline de lançamento do DevOps em todo o ciclo de vida do desenvolvimento de software. 

Serviços de DevOps do Azure agora suporta a monitorização contínua, por meio das quais os pipelines de versão podem incorporar dados de monitorização do Application Insights e outros recursos do Azure. Quando é detetado um alerta do Application Insights, a implementação pode permanecer Check ou revertida até que o alerta é resolvido. Se passam todas as verificações, implementações podem avançar automaticamente de teste para produção sem a necessidade de intervenção manual. 

## <a name="configure-continuous-monitoring"></a>Configurar a monitorização contínua

1. Selecione um projeto de serviços de DevOps do Azure existente.

2. Coloque o cursor sobre **criar e lançar** > selecione **versões** > clique o **sinal de adição** > **Criar definição de versão** > Procure **monitoramento** > **implementação de serviço de aplicações do Azure com a monitorização contínua.**

   ![Novo Pipeline de lançamento de serviços do Azure DevOps](media/continuous-monitoring/001.png)

3. Clique em **aplicam-se.**

4. Junto ao ponto de exclamação vermelho, selecione o texto azul para **ver tarefas do ambiente.**

   ![Tarefas da vista de ambiente](media/continuous-monitoring/002.png)

   Será exibida uma caixa de configuração, utilize a seguinte tabela para preencher os campos de entrada.

    | Parâmetro        | Value |
   | ------------- |:-----|
   | **Nome do ambiente**      | Nome que descreve o ambiente de pipeline de lançamento |
   | **Subscrição do Azure** | Lista pendente preenche com quaisquer subscrições do Azure ligadas para a organização de serviços do Azure DevOps|
   | **Nome do serviço de aplicações** | Introdução manual de um novo valor poderá ser necessária para este campo dependendo outras seleções |
   | **Grupo de Recursos**    | Lista pendente preenche com grupos de recursos disponíveis |
   | **Nome de recurso do Application Insights** | Baixo será preenchido com todos os recursos do Application Insights que correspondem ao grupo de recursos selecionado anteriormente.

5. Selecione **alertas de configurar o Application Insights**

6. Para regras de alerta predefinidas, selecione **salvar** > introduza um comentário descritivo > clique em **OK**

## <a name="modify-alert-rules"></a>Modificar regras de alerta

1. Para modificar as definições de alerta predefinidas, clique na caixa de com **reticências...**  à direita do **regras de alerta.**

   (As regras de alerta de out-of-box quatro estão presentes: Disponibilidade, pedidos falhados, tempo de resposta do servidor, exceções de servidor.)

2. Clique no símbolo de lista pendente junto a **disponibilidade.**

3. Modificar a disponibilidade **limiar** para cumprir os requisitos de nível de serviço.

   ![Modificar alerta](media/continuous-monitoring/003.png)

4. Selecione **OK** > **guardar** > introduza um comentário descritivo > clique em **OK.**

## <a name="add-deployment-conditions"></a>Adicionar condições de implementação

1. Clique em **Pipeline** > selecione o **pré** ou **condições de pós-implementação** símbolo consoante o estágio de que necessita de uma porta de monitorização contínua.

   ![Condições de pré-implementação](media/continuous-monitoring/004.png)

2. Definir **Gates** ao **ativado** > **gates aprovação**> clique em **adicionar.**

3. Selecione **do Azure Monitor** (esta opção dá-lhe a capacidade para alertas de acesso tanto a partir do Azure Monitor e o Application Insights)

    ![Azure Monitor](media/continuous-monitoring/005.png)

4. Introduza um **Gates tempo-limite** valor.

5. Introduza um **intervalo de amostragem.**

## <a name="deployment-gate-status-logs"></a>Registos de estado de porta de implementação

Depois de adicionar grupos de implementação, um alerta no Application Insights, que excede o limiar definido anteriormente, protege a sua implementação a partir de promoção de versão indesejados. Depois do alerta é resolvido, a implementação pode avançar automaticamente.

Para observar esse comportamento, selecione **versões** > nome da versão com o botão direito **abrir** > **registos.**

![Registos](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre Pipelines do Azure experimente estes [inícios rápidos.](https://docs.microsoft.com/azure/devops/pipelines)
