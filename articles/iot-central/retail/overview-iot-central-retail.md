---
title: Quais são as soluções de retalho Azure IoT Central | Microsoft Docs
description: Saiba como utilizar modelos de aplicação Azure IoT Central para construir logística conectada, centro de distribuição digital, análise na loja, monitorização de condições, check-out, gestão de inventário inteligente e soluções de retalho.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 56198cf661001bcfcec45d483d0f691234851894
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832288"
---
# <a name="what-are-the-iot-central-retail-solutions"></a>Quais são as soluções de retalho IoT Central?

A Azure IoT Central é uma plataforma de aplicações IoT que reduz os encargos e custos associados ao desenvolvimento, gestão e manutenção de soluções IoT de nível empresarial. Optar por construir com a Azure IoT Central dá-lhe a oportunidade de concentrar o seu tempo, dinheiro e energia na transformação do seu negócio com dados IoT, em vez de apenas manter e atualizar uma infraestrutura IoT complexa e em constante evolução.

Este artigo descreve vários modelos de aplicação IoT Central específicos de retalho. Como construtor de soluções, pode utilizar estes modelos para construir soluções IoT que otimizem as cadeias de fornecimento, melhorem as experiências na loja para os clientes e rastreiem o inventário de forma mais eficiente.

:::image type="content" source="media/overview-iot-central-retail/retail-app-templates.png" alt-text="Visão geral do retalho Azure IoT":::

As seguintes secções descrevem as capacidades destes modelos de aplicação:

## <a name="connected-logistics"></a>Logística associada

Os gastos globais em logística deverão atingir os 10,6 biliões de dólares em 2020. O transporte de mercadorias é responsável pela maior parte desta despesa e os prestadores de transporte marítimo estão sob intensa pressão e constrangimentos concorrenciais.

Pode utilizar sensores IoT para recolher e monitorizar condições ambientais tais como temperatura, humidade, inclinação, choque, luz e a localização de um carregamento. Você pode combinar telemetria recolhida a partir de sensores e dispositivos IoT com outras fontes de dados, tais como informações meteorológicas e de tráfego em sistemas de inteligência empresarial baseados na nuvem.

Os benefícios de uma solução logística conectada incluem:

* Monitorização de envios com rastreio e controlo em tempo real. 
* Integridade de envios com monitorização das condições ambientais em tempo real.
* Segurança contra roubo, perda ou danos de envios.
* Barreiras geográficas, otimização de rota, gestão de frota e análise de veículos.
* Previsão da partida e chegada prevista de envios.

As imagens seguintes mostram o painel de instrumentos fora da caixa no modelo de aplicação. O painel de instrumentos é totalmente personalizável para satisfazer os seus requisitos específicos de solução:

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard1.png" alt-text="Screenshot que mostra a metade superior do painel de operações logísticas conectado.":::

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard2.png" alt-text="Screenshot que mostra a metade inferior do painel de operações logísticas conectado.":::

Para saber mais, consulte o Deploy e caminhe através de um tutorial [de modelo de aplicação logística conectado.](./tutorial-iot-central-connected-logistics.md)

## <a name="digital-distribution-center"></a>Centro de distribuição digital

À medida que os fabricantes e os retalhistas estabelecem presenças mundiais, as suas cadeias de abastecimento ramificam-se e tornam-se mais complexas. Os consumidores esperam agora que estejam disponíveis grandes seleções de produtos e que esses bens cheguem no prazo de um ou dois dias após a compra. Os centros de distribuição devem adaptar-se a estas tendências, superando as ineficiências existentes.

Hoje, a dependência do trabalho manual significa que a recolha e embalagem representam 55-65% dos custos do centro de distribuição. A recolha manual e a embalagem também são tipicamente mais lentas do que os sistemas automatizados, e as necessidades de pessoal flutuantes rapidamente dificultam ainda mais o cumprimento dos volumes de envio. Esta flutuação sazonal resulta num elevado volume de negócios do pessoal e aumenta a probabilidade de erros dispendiosos.

As soluções baseadas em câmaras ativadas por IoT podem proporcionar benefícios transformacionais, permitindo um ciclo de feedback digital. Dados de todo o centro de distribuição conduzem a insights acccáveis que, por sua vez, resultam em melhores dados.

Os benefícios de um centro de distribuição digital incluem:

* As câmaras monitorizam os bens à medida que chegam e se movem através do sistema transportador.
* Identificação automática de bens defeituosos.
* Rastreio de ordem eficiente.
* Redução de custos, melhoria da produtividade e utilização otimizada.

A imagem que se segue mostra o painel de instrumentos fora da caixa no modelo de aplicação. O painel de instrumentos é totalmente personalizável para satisfazer os seus requisitos específicos de solução: 

:::image type="content" source="media/overview-iot-central-retail/digital-distribution-center-dashboard.png" alt-text="Painel de instrumentos do Centro de Distribuição Digital":::

Para saber mais, consulte o Implementar e caminhe através de um tutorial [de modelo de aplicação de centro de distribuição digital.](./tutorial-iot-central-digital-distribution-center.md)

## <a name="in-store-analytics---condition-monitoring"></a>Análise na loja - monitorização de condições

Para muitos retalhistas, as condições ambientais dentro das suas lojas são um diferenciador chave dos seus concorrentes. Os retalhistas querem manter condições agradáveis dentro das suas lojas em benefício dos seus clientes.  

Como construtor de soluções, pode utilizar o modelo de aplicação de monitorização da condição de análise na loja IoT Central para construir uma solução de ponta a ponta. O modelo de aplicação permite ligar-se digitalmente e monitorizar um ambiente de loja de retalho utilizando diferentes tipos de dispositivos sensores. Estes dispositivos sensores geram telemetria que pode converter em insights de negócio ajudando o retalhista a reduzir os custos operacionais e criar uma grande experiência para os seus clientes.

Utilize o modelo de aplicação para:

* Ligue diferentes tipos de sensores IoT a uma instância de aplicação IoT Central.
* Monitorize e gere a saúde da rede de sensores e de quaisquer dispositivos de gateway no ambiente.
* Crie regras personalizadas em torno das condições ambientais dentro de uma loja para desencadear alertas para os gerentes da loja.
* Transforme as condições ambientais dentro da sua loja em insights que a equipa da loja de retalho pode usar para melhorar a experiência do cliente.
* Exporte os conhecimentos agregados sobre aplicações comerciais existentes ou novas para fornecer informações úteis e oportunas ao pessoal retalhista.

O modelo de aplicação vem com um conjunto de modelos de dispositivo e usa um conjunto de dispositivos simulados para povoar o painel de instrumentos. 

A imagem que se segue mostra o painel de instrumentos fora da caixa no modelo de aplicação. O painel de instrumentos é totalmente personalizável para satisfazer os seus requisitos específicos de solução: 

:::image type="content" source="media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png" alt-text="Monitorização da condição de analítica na loja":::

Para saber mais, consulte a aplicação de análise Create [in-store no tutorial Azure IoT Central.](./tutorial-in-store-analytics-create-app.md)

## <a name="in-store-analytics---checkout"></a>Análise na loja - check-out

Para alguns retalhistas, a experiência de check-out dentro das suas lojas é um diferenciador chave dos seus concorrentes. Os retalhistas querem oferecer uma experiência de check-out suave dentro das suas lojas para incentivar os clientes a regressarem.  

Como construtor de soluções, pode utilizar o modelo de aplicação de check-out de check-out da IoT Central para construir uma solução que fornece insights de toda a zona de check-out de uma loja para o pessoal de retalho. Por exemplo, os sensores podem fornecer informações sobre os comprimentos das filas e os tempos médios de espera para cada faixa de check-out.

Utilize o modelo de aplicação para:

* Ligue diferentes tipos de sensores IoT a uma instância de aplicação IoT Central.
* Monitorize e gere a saúde da rede de sensores e de quaisquer dispositivos de gateway no ambiente.
* Crie regras personalizadas em torno da condição de check-out dentro de uma loja para desencadear alertas para o pessoal de retalho.
* Transforme as condições de check-out dentro da loja em insights que a equipa da loja de retalho pode usar para melhorar a experiência do cliente.
* Exporte os conhecimentos agregados sobre aplicações comerciais existentes ou novas para fornecer informações úteis e oportunas ao pessoal retalhista.

O modelo de aplicação vem com um conjunto de modelos de dispositivos e usa um conjunto de dispositivos simulados para povoar o painel com dados de ocupação da faixa de rodagem. 

A imagem que se segue mostra o painel de instrumentos fora da caixa no modelo de aplicação. O painel de instrumentos é totalmente personalizável para satisfazer os seus requisitos específicos de solução: 

:::image type="content" source="media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png" alt-text="Check-out de Análise na Loja":::

Para saber mais, consulte a aplicação de análise Create [in-store no tutorial Azure IoT Central.](./tutorial-in-store-analytics-create-app.md)

## <a name="smart-inventory-management"></a>Gestão de inventário inteligente

O inventário é o stock de bens que um retalhista detém. A gestão do inventário é fundamental para garantir que o produto certo está no lugar certo na hora certa. Um retalhista deve equilibrar os custos de armazenar demasiado inventário em vez dos custos de não ter itens suficientes em stock para satisfazer a procura.

Os dados ioT gerados a partir de tags, balizas e câmaras de identificação de radiofrequências (RFID) oferecem oportunidades para melhorar os processos de gestão de inventário. Você pode combinar telemetria recolhida a partir de sensores e dispositivos IoT com outras fontes de dados, tais como informações meteorológicas e de tráfego em sistemas de inteligência empresarial baseados na nuvem.

Os benefícios da gestão inteligente do inventário incluem:

* Reduzir o risco de os itens estarem esgotados e garantir o nível de atendimento ao cliente pretendido. 
* Análise aprofundada e insights sobre a precisão do inventário em tempo quase real.
* Ferramentas para ajudar a decidir sobre a quantidade certa de inventário para atender as ordens do cliente.

Este modelo de aplicação centra-se na conectividade do dispositivo e na configuração e gestão de dispositivos de leitores rfid e bluetooth de baixa energia (BLE).

A imagem que se segue mostra o painel de instrumentos fora da caixa no modelo de aplicação. O painel de instrumentos é totalmente personalizável para satisfazer os seus requisitos específicos de solução:

:::image type="content" source="media/overview-iot-central-retail/smart-inventory-management-dashboard.png" alt-text="Painel de gestão de inventário inteligente":::

Para saber mais, consulte o Deploy e caminhe através de um tutorial [de modelo de aplicação de gestão de inventário inteligente.](./tutorial-iot-central-smart-inventory-management.md)

## <a name="micro-fulfillment-center"></a>Centro do micro-cumprimento

No panorama retalhista cada vez mais competitivo, os retalhistas enfrentam constantemente pressão para colmatar o fosso entre a procura e o cumprimento. Uma nova tendência que tem surgido para fazer face à crescente procura dos consumidores é alojar o inventário perto dos clientes finais e das lojas que visitam.

O modelo de aplicação do centro de micro-cumprimento central IoT permite aos construtores de soluções monitorizar e gerir todos os aspetos dos seus centros de realização totalmente automatizados. O modelo inclui um conjunto de sensores de monitorização de condições simulados e portadores robóticos para acelerar o processo de desenvolvimento da solução. Estes dispositivos sensores captam sinais significativos que podem ser convertidos em insights de negócio, permitindo aos retalhistas reduzir os seus custos operacionais e criar experiências para os seus clientes.

O modelo de aplicação permite-lhe: 

- Ligue perfeitamente diferentes tipos de sensores IoT, tais como robôs ou sensores de monitorização de condições a uma instância de aplicação IoT Central.
- Monitorize e gere a saúde da rede de sensores, e quaisquer dispositivos de gateway no ambiente.
- Crie regras personalizadas em torno das condições ambientais dentro de um centro de cumprimento para desencadear alertas apropriados.
- Transforme as condições ambientais dentro do seu centro de realização em insights que podem ser alavancados pela equipa de armazém de retalho.
- Exportar os conhecimentos agregados para os pedidos de negócio existentes ou novos em benefício dos membros do pessoal de retalho.

A imagem que se segue mostra o painel de instrumentos fora da caixa no modelo de aplicação. O painel de instrumentos é totalmente personalizável para satisfazer os seus requisitos específicos de solução:

:::image type="content" source="media/overview-iot-central-retail/MFC-Dashboard.png" alt-text="Centro de Micro-Realização":::

Para saber mais, consulte o Implementar e caminhe pelo tutorial [do modelo de aplicação do centro de micro-realização.](./tutorial-micro-fulfillment-center.md)

## <a name="video-analytics---object-and-motion-detection"></a>Análise de vídeo - deteção de objetos e movimentos

O modelo *de análise de vídeo IoT Central - modelo de aplicação de deteção de objetos e movimentos* permite-lhe experimentar rapidamente como implementar, gerir e monitorizar uma solução que utiliza câmaras de borda inteligentes para detetar objetos e movimento.

A aplicação de análise de vídeo utiliza um módulo [de análise de vídeo ao vivo (LVA)](#live-video-analytics) em execução em IoT Edge. O módulo LVA fornece uma plataforma para construir aplicações de vídeo inteligentes que se estendem pela borda e pela nuvem. Pode utilizar a plataforma para melhorar as soluções IoT, como a aplicação de análise de vídeo, com deteção de objetos e movimentos.

O modelo de aplicação inclui quatro dashboards de aplicação:

* **O Getting Started** fornece links para recursos para ajudá-lo a começar a usar o modelo de aplicação.
* **O Painel de Demonstração** fornece uma ilustração dos tipos de informação que pode apresentar a partir das suas câmaras ligadas.
* **(Amostra) A Real Camera Management** utiliza câmaras simuladas para mostrar como consegue gerir as suas câmaras a partir da aplicação.
* **(Amostra) O Monitor de Câmara Real** utiliza câmaras simuladas para mostrar como pode monitorizar as suas câmaras a partir da aplicação.

:::image type="content" source="media/overview-iot-central-retail/live-video-analytics.png" alt-text="Análise de vídeo - deteção de objetos e movimentos":::

Para saber mais sobre a arquitetura da solução, consulte a arquitetura da [aplicação video analytics.](architecture-video-analytics.md)

Para aprender a implementar a solução, consulte a aplicação de análise de vídeo create no tutorial [Azure IoT Central.](tutorial-video-analytics-deploy.md)

### <a name="live-video-analytics"></a>Análise de vídeo ao vivo

[A análise de vídeo ao vivo](https://github.com/Azure/live-video-analytics) fornece uma plataforma para você construir aplicações de vídeo inteligentes que atravessam a borda e a nuvem. A plataforma oferece a capacidade de capturar, gravar, analisar vídeos ao vivo e publicar os resultados, que podem ser de vídeo ou vídeo, para os serviços Azure. Os serviços Azure podem estar a funcionar na nuvem ou na borda. Pode utilizar a plataforma para melhorar as soluções IoT com análise de vídeo.

## <a name="next-steps"></a>Passos seguintes

Para começar a construir uma solução de retalho:

* Começa com a [aplicação de análise da Create in-store no tutorial Azure IoT Central](./tutorial-in-store-analytics-create-app.md) que te acompanha como construir uma solução com um dos modelos de aplicação de análise na loja.
* [Implemente e caminhe através do modelo de aplicação de análise de vídeo](./tutorial-video-analytics-deploy.md).
* [Implemente e caminhe através de um modelo de aplicação logística conectada.](./tutorial-iot-central-connected-logistics.md)
