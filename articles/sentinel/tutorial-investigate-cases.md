---
title: Investigue incidentes com o Azure Sentinel Preview | Microsoft Docs
description: Use este tutorial para aprender a investigar incidentes com o Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: bad3fddd6caf7e6eb455e59280f181c787b95a4e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780391"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel-preview"></a>Tutorial: Investigar incidentes com o Azure Sentinel Preview

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tutorial ajuda você a detectar ameaças com o Azure Sentinel.

Depois de [conectar suas fontes de dados](quickstart-onboard.md) ao Azure Sentinel, você deseja ser notificado quando algo suspeito acontecer. Para permitir que você faça isso, o Azure Sentinel permite que você crie regras de alerta avançadas, que geram incidentes que você pode atribuir e usar para investigar profundamente as anomalias e ameaças em seu ambiente. 

> [!div class="checklist"]
> * Criar incidentes
> * Investigar incidentes
> * Responder a ameaças

## <a name="investigate-incidents"></a>Investigar incidentes

um incidente pode incluir vários alertas. É uma agregação de todas as evidências relevantes para uma investigação específica. um incidente é criado com base nos alertas definidos na página de **análise** . As propriedades relacionadas aos alertas, como severidade e status, são definidas no nível do incidente. Depois de permitir que o Azure Sentinel saiba quais tipos de ameaças você está procurando e como encontrá-las, você pode monitorar as ameaças detectadas pela investigação de incidentes. 

1. Selecione **incidentes**. A página **incidentes** permite que você saiba quantos incidentes você tem, quantas estão abertas, quantos você definiu **em andamento**e quantas estão fechadas. Para cada incidente, você pode ver a hora em que ocorreu e o status do incidente. Examine a severidade para decidir o que tratar primeiro. Na página **incidentes** , clique na guia **alertas** para ver todos os alertas relacionados a um incidente. As entidades que você mapeou anteriormente como parte do incidente podem ser exibidas na guia **entidades** .  Você pode filtrar os incidentes conforme necessário, por exemplo, por status ou severidade. Ao examinar a guia **incidentes** , você verá incidentes abertos que contêm alertas disparados por suas regras de detecção definidas no **Analytics**. Na parte superior, você verá seus incidentes ativos, novos incidentes e incidentes em andamento. Você também pode ver uma visão geral de todos os seus incidentes por gravidade.

   ![Painel de alerta](./media/tutorial-investigate-cases/cases.png)

2. Para iniciar uma investigação, clique em um incidente específico. À direita, você pode ver informações detalhadas para o incidente, incluindo sua gravidade, o resumo do número de entidades envolvidas (com base no seu mapeamento). Cada incidente tem uma ID exclusiva. A severidade do incidente é determinada de acordo com o alerta mais grave incluído no incidente.  

1. Para exibir mais detalhes sobre os alertas e entidades no incidente, clique em **Exibir detalhes completos** na página incidente e examine as guias relevantes que resumem as informações do incidente.  A exibição de incidente completa consolida todas as evidências no alerta, os alertas associados e as entidades.

1. Na guia **alertas** , examine o próprio alerta – quando ele foi disparado e por quanto excedeu os limites definidos. Você pode ver todas as informações relevantes sobre o alerta – a consulta que disparou o alerta, o número de resultados retornados por consulta e a capacidade de executar guias estratégicos nos alertas. Para fazer uma busca detalhada ainda mais no incidente, clique no número de ocorrências. Isso abre a consulta que gerou os resultados e os resultados que dispararam o alerta no Log Analytics.

3. Na guia **entidades** , você pode ver todas as entidades que você mapeou como parte da definição da regra de alerta. 

4. Se você estiver investigando um incidente ativamente, é uma boa ideia definir o status do incidente como **em andamento** até fechá-lo. Você também pode fechar o incidente, em que **Closed resolvido** é o status de incidentes que indicam que um incidente foi tratado, enquanto **Closed ignorado** é o status de incidentes que não exigem tratamento. Explicações são necessárias explicando seu raciocínio para fechar um incidente.

5. Os incidentes podem ser atribuídos a um usuário específico. Para cada incidente, você pode atribuir um proprietário, definindo o campo **proprietário** do incidente. Todos os incidentes começam como não atribuídos. Você pode entrar nos incidentes e filtrar por seu nome para ver todos os incidentes que você possui. 

5. Clique em **investigar** para exibir o mapa de investigação e o escopo da violação com as etapas de correção. 



## <a name="respond-to-threats"></a>Responder a ameaças

O Azure Sentinel oferece duas opções principais para responder a ameaças usando guias estratégicos. Você pode definir um guia estratégico para ser executado automaticamente quando um alerta é disparado ou pode executar um manual manualmente em resposta a um alerta.

- Você pode definir um guia estratégico para ser executado automaticamente quando um alerta for disparado quando você configurar o guia estratégico. 

- Você pode executar manualmente um guia estratégico de dentro do alerta, clicando em **Exibir guias estratégicos** e, em seguida, selecionando um guia estratégico para execução.




## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você aprendeu como começar a investigar incidentes usando o Azure Sentinel. Continue no tutorial sobre [como responder a ameaças usando guias estratégicos automatizados](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Responda às ameaças](tutorial-respond-threats-playbook.md) para automatizar suas respostas a ameaças.

