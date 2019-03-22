---
title: Investigar Incidentes e Alertas no Centro de Segurança do Azure | Microsoft Docs
description: Este documento ajuda-o a utilizar a funcionalidade de investigação do Centro de Segurança do Azure para investigar alertas e incidentes de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a8e894a9-8781-4749-ae8f-8c8e01658566
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 6ba21c6eacd5b72d13706c08f0cc9883ccc91388
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107479"
---
# <a name="investigate-incidents-and-alerts-in-azure-security-center-preview"></a>Investigar Incidentes e Alertas no Centro de Segurança do Azure (Pré-visualização)
Este documento ajuda-o a utilizar a funcionalidade de investigação do Centro de Segurança do Azure para investigar alertas e incidentes de segurança.

## <a name="what-is-investigation-in-security-center"></a>O que é a investigação no Centro de Segurança?
A funcionalidade Investigação do Centro de Segurança permite-lhe triar, compreender o âmbito e acompanhar a origem de potenciais [incidentes de segurança](https://docs.microsoft.com/azure/security-center/security-center-incident).

O objetivo é facilitar o processo de investigação ao ligar todas as entidades ([alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), utilizadores, computadores e incidentes) que estão envolvidos no incidente que está a investigar.  O Centro de Segurança fá-lo ao correlacionar os dados relevantes às entidades envolvidas e ao expor essa correlação através de um gráfico em direto que o ajuda a navegar pelos objetos e a visualizar as informações importantes.


> [!NOTE]
> * [Alertas personalizados](security-center-custom-alert.md) não são suportados na funcionalidade de investigação do Centro de segurança.
> * Investigação só é suportada para os alertas com base nos dados recolhidos a partir de servidores do Windows.


## <a name="how-investigation-works"></a>Como funciona a Investigação?
A Investigação é composta por um gráfico que ocupa a área central do dashboard de investigação. O gráfico está sempre focado numa entidade específica e apresenta as entidades que lhe estão associadas. Uma entidade pode ser um alerta de segurança, um utilizador, um computador ou um incidente.

![Mapa](./media/security-center-investigation/security-center-investigation-fig1.png)

O utilizador pode clicar numa entidade no gráfico para navegar de entidade em entidade. O gráfico centra-se automaticamente na entidade selecionada e nas respetivas entidades associadas. As entidades que já não são relevantes podem ser removidas do gráfico.

### <a name="investigation-path"></a>Caminho da investigação
Enquanto o utilizador navega para entidades diferentes, o caminho da investigação ajuda-o a fazer o acompanhamento do contexto da investigação e permite uma navegação rápida. O incidente que contém os resultados da investigação está sempre no incidente mais à esquerda do caminho de investigação.

![Caminho](./media/security-center-investigation/security-center-investigation-fig2.png)

### <a name="general-information"></a>Informações gerais
Quando uma entidade é apresentada no gráfico, os separadores mostram informações adicionais sobre a mesma. O separador **Informações** apresenta informações gerais sobre a entidade a partir de várias origens de informações disponíveis.

![Informações gerais](./media/security-center-investigation/security-center-investigation-fig3.png)

O separador Informações mostra as informações relevantes para o incidente selecionado no mapa. Os incidentes são contentores que incluem os resultados de uma investigação. Cada investigação ocorre no contexto de um incidente.

Um incidente só é criado quando um utilizador clica no botão **Iniciar investigação** de um alerta específico. A capacidade básica disponível para o investigador é marcar entidades, como utilizador, computador ou alerta. Quando uma entidade é marcada como associada, é indicado um motivo. A partir deste momento, esta entidade aparece diretamente no incidente no gráfico e na lista de entidades do incidente.

### <a name="entities"></a>Entidades

O separador **Entidades** mostra todas as entidades relacionadas, agrupadas por tipo. É útil em dois cenários: quando existem demasiados entidades para apresentar no gráfico e quando os nomes das entidades são demasiado longos e é mais fácil examiná-los de forma tabular.

![Entidades](./media/security-center-investigation/security-center-investigation-fig4.png)

### <a name="search"></a>Pesquisa

O separador **Pesquisa** apresenta todos os tipos de registos que estão disponíveis para a entidade. Para cada tipo de registo, pode ver o número de registos que estão disponíveis. Clicar em cada tipo de registo leva-o para o ecrã de pesquisa. No ecrã de pesquisa, pode otimizar a pesquisa e utilizar as várias funcionalidades de pesquisa, como a definição de alertas. Na versão atual, o separador Pesquisa só está disponível para entidades de utilizadores e computadores.

![Pesquisa](./media/security-center-investigation/security-center-investigation-fig5.png)

### <a name="exploration"></a>Exploração

O separador **Exploração** permite ao investigador examinar os dados associados a vários problemas relacionados com a entidade. Por exemplo, quando é investigado um computador, a lista de processos executados no mesmo é apresentada neste separador. Em alguns casos, o separador Exploração apresenta dados que podem indicar um problema suspeito. O investigador pode examinar os dados no separador ou abri-los no ecrã de pesquisa para examinar grandes conjuntos de dados e para utilizar as opções de pesquisa avançadas, como a filtragem e a exportação para Excel.

![Exploração](./media/security-center-investigation/security-center-investigation-fig6.png)

### <a name="timeline"></a>Linha cronológica

A maioria dos dados que são apresentados no gráfico e nos vários separadores é relevante para períodos de tempo específicos. Este âmbito de tempo é definido com o seletor de âmbito de tempo, no canto superior esquerdo do gráfico. O investigador tem à disposição vários métodos para selecionar o âmbito de tempo.

![Linha cronológica](./media/security-center-investigation/security-center-investigation-fig7.png)

Os itens seguintes são sensíveis ao âmbito de tempo:

- Relação utilizador-computador no gráfico, só são apresentados os utilizadores que iniciaram sessão no computador neste âmbito de tempo.
- Que alertas são apresentados quando examina computadores e utilizadores: só são apresentados os alertas que ocorrem dentro do âmbito de tempo.
- O separador Entidades segue a mesma lógica do gráfico.

Os itens seguintes vão ser apresentados, independentemente do âmbito de tempo selecionado:

- Quando é apresentado um alerta, são sempre apresentadas todas as entidades contidas no mesmo, como utilizadores e computadores.
- Se um incidente contiver uma entidade, esta é apresentada.

> [!NOTE]
> Os separadores **Pesquisa** e **Exploração** só mostram registos neste âmbito de tempo.

## <a name="how-to-perform-an-investigation"></a>Como fazer uma investigação?

Pode começar a investigação a partir de um incidente de segurança ou a partir de um alerta. A opção que escolher irá variar consoante as suas necessidades. Os passos seguintes são utilizados para iniciar uma investigação a partir de um alerta:

1.  Abra o dashboard do **Centro de Segurança**.
2.  Clique em **Alertas de Segurança** e selecione o incidente que quer investigar.
3.  Na página do incidente, clique no botão **Iniciar investigação** e é apresentado o dashboard **Investigação**.

    ![Alerta](./media/security-center-investigation/security-center-investigation-fig8.png)

4. Neste dashboard, pode selecionar a entidade no mapa, o que faz com que sejam apresentadas as informações relevantes sobre a mesma no lado direito do ecrã.

    ![Dashboard Investigação](./media/security-center-investigation/security-center-investigation-fig9.png)

A partir daqui, pode explorar as entidades que estiveram envolvidas neste incidente e explorar mais detalhes sobre cada uma.

## <a name="see-also"></a>Consulte também
Neste documento, aprendeu a utilizar a funcionalidade Investigação do Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de Resolução de Problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
