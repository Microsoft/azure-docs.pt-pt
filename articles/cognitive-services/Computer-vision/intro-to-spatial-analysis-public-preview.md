---
title: Introdução à análise espacial da Visão Computacional
titleSuffix: Azure Cognitive Services
description: Este documento explica os conceitos e características básicas de um recipiente de análise espacial da Visão Computacional.
services: cognitive-services
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 402ee6d5efdd489914cb7d283c7c46d4f7d175f6
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968063"
---
# <a name="introduction-to-computer-vision-spatial-analysis"></a>Introdução à análise espacial da Visão Computacional

A análise espacial da Visão Computacional é uma nova característica da Azure Cognitive Services Computer Vision que ajuda as organizações a maximizar o valor dos seus espaços físicos, compreendendo os movimentos e a presença das pessoas dentro de uma determinada área. Permite-lhe ingerir vídeos de CCTV ou câmaras de vigilância, executar habilidades de IA para extrair insights dos streams de vídeo e gerar eventos para serem usados por outros sistemas. Com a entrada de um fluxo de câmara, uma habilidade de IA pode fazer coisas como contar o número de pessoas que entram num espaço ou medir o cumprimento das diretrizes de distanciamento social.

## <a name="the-basics-of-spatial-analysis"></a>Os fundamentos da análise espacial

Hoje as operações centrais da análise espacial são todas construídas sobre um oleoduto que ingere vídeo, deteta pessoas no vídeo, rastreia as pessoas à medida que se movem ao longo do tempo, e gera eventos à medida que as pessoas interagem com regiões de interesse.

## <a name="spatial-analysis-terms"></a>Termos de análise espacial

| Termo | Definição |
|------|------------|
| Deteção de Pessoas | Este componente responde à pergunta "onde estão as pessoas nesta imagem"? Encontra humanos numa imagem e passa uma caixa de limites que indica a localização de cada pessoa para o componente de rastreio das pessoas. |
| Rastreio de pessoas | Este componente liga as deteções das pessoas ao longo do tempo à medida que as pessoas se movem em frente a uma câmara. Usa a lógica temporal sobre como as pessoas normalmente se movem e informações básicas sobre a aparência geral das pessoas para fazer isso. Não pode localizar pessoas através de várias câmaras ou reidentificar alguém que desapareceu por mais de um minuto. O Rastreio de Pessoas não utiliza marcadores biométricos como reconhecimento facial ou rastreio de marcha. |
| Região de Interesse | Esta é uma zona ou linha definida no vídeo de entrada como parte da configuração. Quando uma pessoa interage com a região do vídeo, o sistema gera um evento. Por exemplo, para a habilidade PersonCrossingLine, uma linha é definida no vídeo. Quando uma pessoa cruza essa linha, um evento é gerado. |
| Evento | Um evento é a produção primária da análise espacial. Cada habilidade emite um evento específico periodicamente (ex. uma vez por minuto) ou quando ocorre um gatilho específico. O evento inclui informações sobre o que ocorreu no vídeo de entrada, mas não inclui imagens ou vídeos. Por exemplo, a habilidade Do PeopleCount pode emitir um evento que contenha a contagem atualizada sempre que a contagem de pessoas muda (gatilho) ou uma vez a cada minuto (periodicamente). |

## <a name="example-use-cases-for-spatial-analysis"></a>Exemplo usar casos para análise espacial

Seguem-se os casos de uso de exemplo que tínhamos em mente à medida que desenhamos e testámos a análise espacial.

**Social Distancing Compliance** - Um espaço de escritório tem várias câmaras que usam análises espaciais para monitorizar a conformidade do distanciamento social medindo a distância entre as pessoas. O gestor de instalações pode usar mapas de calor mostrando estatísticas agregadas de conformidade de distanciamento social ao longo do tempo para ajustar o espaço de trabalho e facilitar o distanciamento social.

**Shopper Analysis** - Uma mercearia usa câmaras apontadas aos expositores de produtos para medir o impacto das mudanças de merchandising no tráfego da loja. O sistema permite ao gerente da loja identificar quais os novos produtos que mais mudam para o envolvimento.

**Gestão de Filas** - As câmaras apontadas para as filas de checkout fornecem alertas aos gestores quando o tempo de espera se torna demasiado longo, permitindo-lhes abrir mais linhas. Os dados históricos sobre o abandono da fila dão uma ideia do comportamento dos consumidores.

**Construção Ocupação & Análise** - Um edifício de escritórios usa câmaras focadas nas entradas de espaços-chave para medir a pegada e como as pessoas usam o local de trabalho. Os insights permitem ao gestor do edifício ajustar o serviço e o layout para melhor servir os ocupantes.

**Deteção mínima do pessoal** - Num centro de dados, as câmaras monitorizam a atividade em torno dos servidores. Quando os funcionários estão a consertar fisicamente equipamento sensível, duas pessoas são sempre obrigadas a estar presentes durante a reparação por razões de segurança. As câmaras são usadas para verificar se esta orientação é seguida.

**Otimização do Local de Trabalho** - Num restaurante casual rápido, as câmaras na cozinha são usadas para gerar informação agregada sobre o fluxo de trabalho dos colaboradores. Isto é usado pelos gestores para melhorar processos e formação para a equipa.

## <a name="considerations-when-choosing-a-use-case"></a>Considerações na escolha de um caso de uso

**Evite alertas críticos de segurança** - A análise espacial não foi concebida para alertar em tempo real de segurança crítica. Não deve ser invocado para cenários em que são necessários alertas em tempo real para desencadear uma intervenção para evitar ferimentos, como desligar uma peça de maquinaria pesada quando uma pessoa está presente. Pode ser usado para a redução de risco usando estatísticas e intervenção para reduzir o comportamento de risco, como pessoas que entram numa área restrita/proibida.

**Evite a utilização para decisões relacionadas com o emprego** - A análise espacial fornece métricas probabilísticas relativas à localização e movimento de pessoas dentro de um espaço. Embora estes dados possam ser úteis para a melhoria agregada do processo, os dados não são um bom indicador do desempenho individual dos trabalhadores e não devem ser utilizados para tomar decisões relacionadas com o emprego.

**Evite a utilização de decisões relacionadas com os cuidados de saúde** - A análise espacial fornece dados probabilísticos e parciais relacionados com os movimentos das pessoas. Os dados não são adequados para tomar decisões relacionadas com a saúde.

**Evite a utilização em espaços protegidos** - Proteja a privacidade dos indivíduos avaliando as localizações e posições das câmaras, ajustando ângulos e regiões de interesses para que não negligenciem áreas protegidas, como casas de banho.

**Considere cuidadosamente a utilização em escolas ou centros de acolhimento de idosos** - A análise espacial não foi fortemente testada com dados que contenham menores de 18 anos ou adultos com mais de 65 anos. Recomendamos que os clientes avaliem cuidadosamente as taxas de erro para o seu cenário em ambientes onde estas idades predominam.

**Considere cuidadosamente a utilização em espaços públicos** - Avaliar as localizações e posições das câmaras, ajustar ângulos e regiões de interesses para minimizar a recolha de espaços públicos. A iluminação e o clima em espaços públicos, como ruas e parques, terão um impacto significativo no desempenho do sistema de análise espacial, sendo extremamente difícil proporcionar uma divulgação eficaz em espaços públicos.

## <a name="spatial-analysis-gating-for-public-preview"></a>Análise espacial para visualização pública

Para garantir que a análise espacial é usada para cenários para os para os qual foi projetada, estamos a disponibilizar esta tecnologia aos clientes através de um processo de aplicação. Para ter acesso à análise espacial, terá de começar por preencher o nosso formulário de admissão online. [Inicie a sua candidatura aqui.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

O acesso à análise espacial de visualização pública está sujeito ao exclusivo critério de elegibilidade da Microsoft, processo de verificação e disponibilidade para suportar um número limitado de clientes durante esta pré-visualização fechada. Na pré-visualização pública, estamos à procura de clientes que tenham uma relação significativa com a Microsoft, estejam interessados em trabalhar connosco nos casos de uso recomendados, e cenários adicionais que estejam em conformidade com os nossos compromissos responsáveis de IA.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Características e limitações para a análise espacial](https://docs.microsoft.com/legal/cognitive-services/computer-vision/accuracy-and-limitations?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)
