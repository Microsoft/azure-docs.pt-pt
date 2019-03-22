---
title: Investigar alertas com a pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Utilize este tutorial para saber como investigar alertas com sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 08f84a3e037301837da984b0891d978027dc2e11
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083169"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Tutorial: Detete ameaças com a pré-visualização de sentinela do Azure

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tutorial ajuda-o a detetar ameaças com sentinela do Azure.

Depois de [ligado suas origens de dados](quickstart-onboard.md) ao Azure sentinela, pretende ser notificado quando acontece algo suspeito. Para que possa fazer isso, sentinela Azure lhe permite que criar regras de alertas que geram casos que pode atribuir e avançadas utilização para investigar profundamente anomalias e ameaças no seu ambiente. 


> [!div class="checklist"]
> * Criar regras de deteção
> * Responder a ameaças

## <a name="create-detection-rules"></a>Criar regras de deteção

Para investigar os casos, primeiro tem de criar regras de deteção. Regras de deteção baseiam-se sobre os tipos de ameaças e anomalias que podem ser suspeitas no seu ambiente que pretende saber sobre imediatamente, garantir que estão anexados, investigou e remediadas. 

1. No portal do Azure em Azure sentinela, selecione **Analytics**.

   ![Análise](./media/tutorial-detect-threats/alert-rules.png)

2. Na barra de menus superior, clique em **+ adicionar**.  

   ![Criar regra de alerta](./media/tutorial-detect-threats/create-alert-rule.png)

3. Sob **criar regra de alerta**, forneça um nome descritivo e definir o **gravidade** conforme necessário. 

4. Criar a consulta do Log Analytics e, em seguida, cole-o para o **regra de alerta de conjunto** campo. Eis uma consulta de exemplo que iria alertá-lo quando um número anómalo de recursos é criado na atividade do Azure.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

5. Na **mapeamento de entidade** secção, utilize os campos em **tipo de entidade** para mapear as colunas na sua consulta para campos de entidade reconhecidos pelo sentinela do Azure. Para cada campo, mapear a coluna relevante na consulta que criou no Log Analytics, para o campo de entidade apropriado. Selecione o nome de coluna relevantes sob o **propriedade**. Cada entidade inclui vários campos, por exemplo, SID, GUID, etc. Pode mapear a entidade de acordo com qualquer um dos campos, não apenas a entidade de nível superior.

6. Definir condições de acionamento de alertas sob **acionador do alerta**. Isso define as condições que acionam o alerta. 

7. Definir o **frequência** para a frequência com que a consulta é executada - como frequentemente como a cada 5 minutos ou raramente vez por dia. 

8. Definir o **período** para controlar a janela de tempo para a quantidade de dados a consulta é executada no - por exemplo, pode ser executada a cada hora em 60 minutos de dados.

9. Também pode definir o **supressão**. Supressão é útil quando deseja parar alertas duplicados de que está a ser acionada para o mesmo incidente. Dessa forma, pode parar alertas a partir de que está a ser acionada durante um período específico. Isto pode ajudar a evitar alertas duplicados para o mesmo incidente e permitem que suprimir alertas consecutivas durante um período de tempo. Por exemplo, se o **agendamento de alerta** **frequência** está definida como 60 minutos e o **alertar o período de agendamento** está definido como duas horas, e os resultados da consulta ultrapassado o definido limiar, ele irá acionar um alerta duas vezes, uma vez quando ele é detectado pela primeira vez nos últimos 60 minutos, e novamente quando estiver nos primeiro 60 minutos das 2 horas de dados que está a ser objeto de amostragem. Recomendamos que, se um alerta é acionado, a supressão deve ser para a quantidade de tempo definido no período de alerta. No nosso exemplo, pode desejar definir supressão durante 60 minutos, para que apenas são acionados alertas para eventos que ocorreram durante a hora mais recente.

8. Depois que cole a consulta no **regra de alerta do conjunto** campo, pode ver de imediato uma simulação de alerta abaixo **simulação de alerta de lógica** para que pode obter a compreensão da quantidade de dados será gerado ao longo de um intervalo de tempo específico para o alerta que criou. Isso dependerá de definir para **frequência** e **limiar**. Se vir que em média, o alerta será acionado com demasiada frequência, vai querer definir o número de resultados superior, para que fique acima sua linha de base média.

9. Clique em **criar** para inicializar a regra de alerta. Depois do alerta é criado, é criado um incidente que contém o alerta. Pode ver as regras de deteção definido como linhas na **análises de segurança** separador. Também pode ver o número de correspondências para cada regra - alertas acionados. Nesta lista, pode ativar, desativar ou eliminar cada regra. Pode também direito-Selecione as reticências (...) no final da linha para cada alerta editar, desativar, clonar, Mostrar correspondências ou eliminar uma regra. O **Analytics** página é uma galeria de todas as suas regras de alerta Active Directory, incluindo modelos, ativar e cria com base nos modelos de regras de alerta.

1. Os resultados das regras de alerta podem ser vistos na **casos** página, onde pode fazer a triagem, [investigar casos](tutorial-investigate-cases.md), e remediar essas ameaças.



## <a name="respond-to-threats"></a>Responder a ameaças

Sentinel do Azure dá-lhe duas opções principais para responder a ameaças com playbooks. Pode definir um playbook para serem executados automaticamente quando um alerta é acionado, ou pode executar um playbook manualmente em resposta a um alerta.

- Defina um playbook para ser executada automaticamente quando um alerta é acionado quando configura o playbook. 

- Executar manualmente um playbook de dentro do alerta, clicando **ver playbooks** e, em seguida, selecionando um manual a ser executado.




## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu como começar a deteção de ameaças usando sentinela do Azure. Avance para o tutorial [como responder a ameaças com playbooks automatizadas](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Responder a ameaças](tutorial-respond-threats-playbook.md) para automatizar suas respostas a ameaças.

