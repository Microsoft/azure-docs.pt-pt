---
title: Investigar casos com a pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Utilize este tutorial para saber como investigar casos com sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 7c4994863f3c145c7095bcc12dd69ff02fab8455
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540283"
---
# <a name="tutorial-investigate-cases-with-azure-sentinel-preview"></a>Tutorial: Investigar casos com a pré-visualização de sentinela do Azure

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tutorial ajuda-o a detetar ameaças com sentinela do Azure.

Depois de [ligado suas origens de dados](quickstart-onboard.md) ao Azure sentinela, pretende ser notificado quando acontece algo suspeito. Para que possa fazer isso, sentinela Azure lhe permite que criar regras de alertas que geram casos que pode atribuir e avançadas utilização para investigar profundamente anomalias e ameaças no seu ambiente. 

> [!div class="checklist"]
> * Crie casos
> * Investigar casos
> * Responder a ameaças

## <a name="investigate-cases"></a>Investigar casos

Um caso pode incluir vários alertas. É uma agregação de todas as evidências relevantes para uma investigação específica. Um caso é criado com base em alertas que definiu na **Analytics** página. As propriedades relacionadas com os alertas, como o estado e gravidade são definidas ao nível do incidente. Depois de deixar que o Azure sentinela saber quais são os tipos de ameaças que procura e como localizá-las, pode monitorizar ameaças que são detetadas investigando casos. 

1. Selecione **casos**. O **casos** página permite-lhe saber como muitos casos, tem, quantas têm abrir, quantos tiver definido **em curso**, e quantas estão fechadas. Para cada caso, pode ver a hora em que ocorreu e o estado do caso. Ver a gravidade para decidir o que lidar com o primeiro. Na **casos** página, clique nas **alertas** guia para ver todos os alertas relacionados com um caso. Entidades que mapeou anteriormente como parte do cenário pode ser visto no **entidades** separador.  Pode filtrar os casos, conforme necessário, por exemplo, ao Estado ou gravidade. Se olharmos para o **casos** separador, irá ver casos abertos que contêm alertas acionados por suas regras de deteção definidas na **Analytics**. Na parte superior, verá os casos ativos, novos casos e, em casos de progresso. Também pode ver uma descrição geral de todos os seus casos de por gravidade.

  ![Dashboard de alerta](./media/tutorial-investigate-cases/cases.png)

2. Para começar uma investigação, clique num caso específico. À direita, pode ver informações detalhadas para o caso, incluindo a respetiva gravidade, resumo do número de entidades envolvidas (com base no seu mapeamento). Cada caso tenha um ID exclusivo. A gravidade do cenário é determinada de acordo com o incluído no caso de alerta mais grave.  

1. Para ver mais detalhes sobre os alertas e as entidades no caso, clique em **ver os detalhes completos** no caso de páginas e rever os separadores relevantes que resumem as informações do incidente.  Vista completa caso consolida todas as evidências no alerta, os alertas associados e entidades.

1. Na **alertas** separador, reveja o alerta em si - quando foi acionada e por quanto excedeu os limites que definir. Pode ver todas as informações relevantes sobre o alerta – a consulta que acionou o alerta, o número de resultados devolvidos por consulta e a capacidade de executar playbooks sobre os alertas. Para desagregar para baixo ainda mais para o caso, clique no número de resultados. Esta ação abre a consulta que gerou os resultados e os resultados que acionou o alerta no Log Analytics.

3. Na **entidades** guia, pode ver todas as entidades que mapeou como parte da definição da regra de alerta. 

4. Se estiver a investigar ativamente um caso, é uma boa idéia para definir o estado de casos para **em curso** até que fechá-lo. Também pode fechar o caso, onde **fechado resolvido** é o estado de casos que indicam que a um incidente foi processado, enquanto **fechado ignoradas** é o estado de casos que não requerem o tratamento. Explicações são necessárias, explicando seu raciocínio para fechar um incidente.

5. Casos podem ser atribuídos a um utilizador específico. Para cada caso, pode atribuir um proprietário, ao definir o caso **proprietário** campo. Todos os casos início, como não atribuídos. Pode ir para os casos e filtrar por seu nome para ver todos os casos em que é proprietário. 

5. Clique em **investigar** para ver o mapa de investigação e o âmbito da violação com passos de remediação. 



## <a name="respond-to-threats"></a>Responder a ameaças

Sentinel do Azure dá-lhe duas opções principais para responder a ameaças com playbooks. Pode definir um playbook para serem executados automaticamente quando um alerta é acionado, ou pode executar um playbook manualmente em resposta a um alerta.

- Pode definir um playbook para ser executada automaticamente quando um alerta é acionado quando configura o playbook. 

- Pode executar um playbook de dentro do alerta, manualmente, clicando em **ver playbooks** e, em seguida, selecionando um manual a ser executado.




## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu como começar a investigar casos a utilização de sentinela do Azure. Avance para o tutorial [como responder a ameaças com playbooks automatizadas](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Responder a ameaças](tutorial-respond-threats-playbook.md) para automatizar suas respostas a ameaças.

