---
title: Investigue incidentes com o Azure Sentinel | Microsoft Docs
description: Use este tutorial para aprender a investigar incidentes com o Azure Sentinel.
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
ms.openlocfilehash: 026689b68244052c3ad7cb50cd90d7319b6760ef
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71229410"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Tutorial: Investigue incidentes com o Azure Sentinel

> [!IMPORTANT]
> O grafo de investigação está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Este tutorial ajuda você a investigar incidentes com o Azure Sentinel. Depois de conectar suas fontes de dados ao Azure Sentinel, você deseja ser notificado quando algo suspeito acontecer. Para permitir que você faça isso, o Azure Sentinel permite que você crie regras de alerta avançadas, que geram incidentes que você pode atribuir e investigar.

Este artigo aborda:
> [!div class="checklist"]
> * Investigar incidentes
> * Usar o grafo de investigação
> * Responder a ameaças

Um incidente pode incluir vários alertas. É uma agregação de todas as evidências relevantes para uma investigação específica. Um incidente é criado com base em regras analíticas que você criou na página **análise** . As propriedades relacionadas aos alertas, como severidade e status, são definidas no nível do incidente. Depois de permitir que o Azure Sentinel saiba quais tipos de ameaças você está procurando e como encontrá-las, você pode monitorar ameaças detectadas investigando incidentes.

## <a name="prerequisites"></a>Pré-requisitos
Você só poderá investigar o incidente se tiver usado os campos de mapeamento de entidade ao configurar sua regra analítica. O grafo de investigação requer que seu incidente original inclua entidades.

## <a name="how-to-investigate-incidents"></a>Como investigar incidentes

1. Selecione **incidentes**. A página **incidentes** permite que você saiba quantos incidentes você tem, quantas estão abertas, quantos você definiu **em andamento**e quantas estão fechadas. Para cada incidente, você pode ver a hora em que ocorreu e o status do incidente. Examine a severidade para decidir quais incidentes tratar primeiro.

    ![Exibir severidade do incidente](media/tutorial-investigate-cases/incident-severity.png)

1. Você pode filtrar os incidentes conforme necessário, por exemplo, por status ou severidade.

1. Para iniciar uma investigação, selecione um incidente específico. À direita, você pode ver informações detalhadas para o incidente, incluindo sua gravidade, o resumo do número de entidades envolvidas, os eventos brutos que dispararam esse incidente e a ID exclusiva do incidente.

1. Para exibir mais detalhes sobre os alertas e entidades no incidente, selecione **Exibir detalhes completos** na página incidente e examine as guias relevantes que resumem as informações do incidente. Na guia **alertas** , examine o próprio alerta. Você pode ver todas as informações relevantes sobre o alerta – a consulta que disparou o alerta, o número de resultados retornados por consulta e a capacidade de executar guias estratégicos nos alertas. Para detalhar ainda mais o incidente, selecione o número de **eventos**. Isso abre a consulta que gerou os resultados e os eventos que dispararam o alerta no Log Analytics. Na guia **entidades** , você pode ver todas as entidades que você mapeou como parte da definição da regra de alerta.

    ![Ver detalhes do alerta](media/tutorial-investigate-cases/alert-details.png)

1. Se você estiver investigando um incidente ativamente, é uma boa ideia definir o status do incidente como **em andamento** até fechá-lo.

1. Os incidentes podem ser atribuídos a um usuário específico. Para cada incidente, você pode atribuir um proprietário, definindo o campo **proprietário do incidente** . Todos os incidentes começam como não atribuídos. Você também pode adicionar comentários para que outros analistas possam entender o que você investigou e quais são suas preocupações em relação ao incidente.

    ![Atribuir incidente ao usuário](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Selecione **investigar** para exibir o mapa de investigação.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Use o grafo de investigação para aprofundar-se

O grafo de investigação permite que os analistas façam as perguntas certas para cada investigação. O grafo de investigação ajuda você a entender o escopo e a identificar a causa raiz de uma possível ameaça à segurança correlacionando os dados relevantes com qualquer entidade envolvida. Você pode aprofundar-se e investigar qualquer entidade apresentada no grafo selecionando-a e escolhendo entre diferentes opções de expansão.  
  
O grafo de investigação fornece:

- **Contexto visual de dados brutos**: A dinâmica, o Visual Graph exibe as relações de entidade extraídas automaticamente dos dados brutos. Isso permite que você veja facilmente as conexões entre diferentes fontes de dados.

- **Descoberta completa do escopo da investigação**: Expanda seu escopo de investigação usando consultas de exploração internas para trazer o escopo completo de uma violação.

- **Etapas de investigação internas**: Use opções de exploração predefinidas para verificar se você está fazendo as perguntas certas diante de uma ameaça.

Para usar o grafo de investigação:

1. Selecione um incidente e, em seguida, selecione **investigar**. Isso levará você ao grafo de investigação. O grafo fornece um mapa ilustrativo das entidades conectadas diretamente ao alerta e a cada recurso conectado.

   > [!IMPORTANT] 
   > Você só poderá investigar o incidente se tiver usado os campos de mapeamento de entidade ao configurar sua regra analítica. O grafo de investigação requer que seu incidente original inclua entidades.

   ![Ver mapa](media/tutorial-investigate-cases/map1.png)

1. Selecione uma entidade para abrir o painel **entidades** para que você possa examinar as informações sobre essa entidade.

    ![Exibir entidades no mapa](media/tutorial-investigate-cases/map-entities.png)
  
1. Expanda sua investigação passando o mouse sobre cada entidade para revelar uma lista de perguntas projetadas por nossos especialistas em segurança e analistas por tipo de entidade para aprofundar sua investigação. Chamamos essas consultas de **exploração**de opções.

    ![Explorar mais detalhes](media/tutorial-investigate-cases/exploration-cases.png)

   Por exemplo, em um computador, você pode solicitar alertas relacionados. Se você selecionar uma consulta de exploração, os direitos resultantes serão adicionados de volta ao grafo. Neste exemplo, a seleção de **alertas relacionados** retornou os seguintes alertas para o grafo:

    ![Ver alertas relacionados](media/tutorial-investigate-cases/related-alerts.png)

1. Para cada consulta de exploração, você pode selecionar a opção para abrir os resultados do evento bruto e a consulta usada em Log Analytics, selecionando **eventos\>** .

1. Para entender o incidente, o grafo fornece uma linha do tempo paralela.

    ![Exibir linha do tempo no mapa](media/tutorial-investigate-cases/map-timeline.png)

1. Passe o mouse sobre a linha do tempo para ver quais coisas no grafo ocorreram em que ponto no tempo.

    ![Usar linha do tempo no mapa para investigar alertas](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você aprendeu como começar a investigar incidentes usando o Azure Sentinel. Continue no tutorial sobre [como responder a ameaças usando guias estratégicos automatizados](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Responda às ameaças](tutorial-respond-threats-playbook.md) para automatizar suas respostas a ameaças.

