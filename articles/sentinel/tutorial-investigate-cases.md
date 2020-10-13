---
title: Investigar incidentes com Azure Sentinel. Microsoft Docs
description: Neste tutorial, aprenda a usar o Azure Sentinel para criar regras avançadas de alerta que gerem incidentes que pode atribuir e investigar.
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
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 648d456534e86075a243a84ac0485d95a7f28479
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843112"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Tutorial: Investigar incidentes com Azure Sentinel

> [!IMPORTANT]
> O gráfico de investigação está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Este tutorial ajuda-o a investigar incidentes com a Azure Sentinel. Depois de ligar as suas fontes de dados ao Azure Sentinel, pretende ser notificado quando algo suspeito acontece. Para que possa fazê-lo, o Azure Sentinel permite criar regras avançadas de alerta, que geram incidentes que pode atribuir e investigar.

Este artigo abrange:
> [!div class="checklist"]
> * Investigar incidentes
> * Use o gráfico de investigação
> * Responder a ameaças

Um incidente pode incluir vários alertas. É uma agregação de todas as provas relevantes para uma investigação específica. Um incidente é criado com base nas regras de análise que criou na página **Analytics.** As propriedades relacionadas com os alertas, como a gravidade e o estado, estão definidas ao nível dos incidentes. Depois de ter deixado o Azure Sentinel saber que tipo de ameaças procura e como encontrá-las, pode monitorizar ameaças detetadas investigando incidentes.

## <a name="prerequisites"></a>Pré-requisitos
- Só poderá investigar o incidente se utilizar os campos de mapeamento da entidade quando configurar a sua regra de análise. O gráfico da investigação requer que o seu incidente original inclua entidades.

- Se tiver um utilizador convidado que precise de atribuir incidentes, o utilizador deve ser atribuído ao papel de [Leitor de Diretório](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) no seu inquilino Azure AD. Os utilizadores regulares (não convidados) têm esta função atribuída por padrão.

## <a name="how-to-investigate-incidents"></a>Como investigar incidentes

1. Selecione **Incidentes**. A página **Incidentes** permite-lhe saber quantos incidentes tem, quantos estão abertos, quantos já definiu em **curso**e quantos estão fechados. Para cada incidente, pode ver a hora que ocorreu, e o estado do incidente. Olhe para a gravidade para decidir quais incidentes lidar primeiro.

    ![Ver gravidade do incidente](media/tutorial-investigate-cases/incident-severity.png)

1. Pode filtrar os incidentes conforme necessário, por exemplo, por estado ou gravidade.

1. Para iniciar uma investigação, selecione um incidente específico. À direita, pode ver informações detalhadas sobre o incidente, incluindo a sua gravidade, resumo do número de entidades envolvidas, os eventos brutos que desencadearam este incidente, e a identificação única do incidente.

1. Para ver mais detalhes sobre os alertas e entidades do incidente, **selecione Ver todos os detalhes** na página do incidente e rever os separadores relevantes que resumem as informações do incidente. No separador **Alertas,** reveja o próprio alerta. Pode ver todas as informações relevantes sobre o alerta – a consulta que desencadeou o alerta, o número de resultados devolvidos por consulta e a capacidade de executar playbooks nos alertas. Para aprofundar ainda mais o incidente, selecione o número de **Eventos**. Isto abre a consulta que gerou os resultados e os eventos que desencadearam o alerta no Log Analytics. No separador **Entidades,** pode ver todas as entidades que mapeou como parte da definição de regra de alerta.

    ![Ver detalhes do alerta](media/tutorial-investigate-cases/alert-details.png)

1. Se está a investigar ativamente um incidente, é uma boa ideia definir o estado do incidente **em andamento** até o fechar.

1. Os incidentes podem ser atribuídos a um utilizador específico. Para cada incidente pode atribuir um proprietário, definindo o campo **do proprietário do Incidente.** Todos os incidentes começam como não atribuídos. Também pode adicionar comentários para que outros analistas possam entender o que investigou e quais as suas preocupações em torno do incidente.

    ![Atribuir incidente ao utilizador](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. **Selecione Investigar** para ver o mapa da investigação.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Use o gráfico de investigação para mergulhar profundamente

O gráfico de investigação permite que os analistas façam as perguntas certas para cada investigação. O gráfico de investigação ajuda-o a compreender o âmbito, e a identificar a causa principal, de uma potencial ameaça à segurança, correlacionando dados relevantes com qualquer entidade envolvida. Pode mergulhar mais profundamente e investigar qualquer entidade apresentada no gráfico selecionando-a e escolhendo entre diferentes opções de expansão.  
  
O gráfico de investigação fornece-lhe:

- Contexto visual **a partir de dados brutos**: O gráfico visual ao vivo exibe as relações de entidade extraídas automaticamente dos dados brutos. Isto permite-lhe ver facilmente ligações em diferentes fontes de dados.

- **Descoberta completa do âmbito da investigação**: Expanda o seu âmbito de investigação utilizando consultas de exploração incorporadas para emergir todo o âmbito de uma violação.

- **Etapas de investigação incorporadas**: Utilize opções de exploração predefinidas para se certificar de que está a fazer as perguntas certas perante uma ameaça.

Para usar o gráfico de investigação:

1. Selecione um incidente e, em seguida, **selecione Investigar**. Isto leva-o ao gráfico da investigação. O gráfico fornece um mapa ilustrativo das entidades diretamente ligadas ao alerta e cada recurso conectado mais.

   > [!IMPORTANT] 
   > Só poderá investigar o incidente se utilizar os campos de mapeamento da entidade quando configurar a sua regra de análise. O gráfico da investigação requer que o seu incidente original inclua entidades.

   ![Ver mapa](media/tutorial-investigate-cases/map1.png)

1. Selecione uma entidade para abrir o painel **de Entidades** para que possa rever informações sobre essa entidade.

    ![Ver entidades no mapa](media/tutorial-investigate-cases/map-entities.png)
  
1. Expanda a sua investigação pairando sobre cada entidade para revelar uma lista de questões que foram desenhadas pelos nossos especialistas em segurança e analistas por tipo de entidade para aprofundar a sua investigação. Chamamos a estas opções **consultas de exploração.**

    ![Explore mais detalhes](media/tutorial-investigate-cases/exploration-cases.png)

   Por exemplo, num computador pode solicitar alertas relacionados. Se selecionar uma consulta de exploração, os direitos resultantes são adicionados de volta ao gráfico. Neste exemplo, a seleção de **alertas relacionados** devolveu os seguintes alertas para o gráfico:

    ![Ver alertas relacionados](media/tutorial-investigate-cases/related-alerts.png)

1. Para cada consulta de exploração, pode selecionar a opção de abrir os resultados do evento bruto e a consulta utilizada no Log Analytics, selecionando **Eventos \> **.

1. Para entender o incidente, o gráfico dá-lhe uma linha temporal paralela.

    ![Ver linha do tempo no mapa](media/tutorial-investigate-cases/map-timeline.png)

1. Pairar sobre a linha do tempo para ver quais as coisas no gráfico ocorreram em que ponto no tempo.

    ![Use a linha do tempo no mapa para investigar alertas](media/tutorial-investigate-cases/use-timeline.png)

## <a name="closing-an-incident"></a>Fechar um incidente

Uma vez resolvido um incidente particular (por exemplo, quando a sua investigação tiver chegado à sua conclusão), deverá definir o estado do incidente para **Fechado**. Quando o fizer, será solicitado que classifique o incidente especificando a razão pela qual está a fechá-lo. Este passo é obrigatório. Clique **em Selecionar classificação** e escolha um dos seguintes da lista de drop-down:

- True Positive - atividade suspeita
- Benign Positivo - suspeito mas esperado
- Falso Positivo - lógica de alerta incorreto
- False Positive - dados incorretos
- Indeterminado

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="Screenshot que destaca as classificações disponíveis na lista de classificação Select.":::

Depois de escolher a classificação adequada, adicione algum texto descritivo no campo **Comentário.** Isto será útil no caso de precisar de se referir a este incidente. Clique **em Aplicar** quando terminar e o incidente será encerrado.

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="Screenshot que destaca as classificações disponíveis na lista de classificação Select.":::

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a investigar incidentes usando Azure Sentinel. Continue a tutorial para [responder a ameaças usando livros de jogadas automatizados](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Responda às ameaças](tutorial-respond-threats-playbook.md) de automatizar as suas respostas a ameaças.

