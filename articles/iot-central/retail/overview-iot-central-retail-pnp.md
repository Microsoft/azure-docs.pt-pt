---
title: Construção de soluções de retalho com a Azure IoT Central [ Microsoft Docs
description: Saiba utilizar modelos de aplicação Azure IoT Central para construir logística conectada, centro de distribuição digital, análise na loja, monitorização de condições, check-out, gestão inteligente de inventários e soluções de retalho.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 808a17fcf303c6eb72f11f5379fa172302dc782a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77059862"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Criar soluções para o comércio com o Azure IoT Central



A Azure IoT Central é uma plataforma de aplicações IoT que reduz o fardo e o custo associados ao desenvolvimento, gestão e manutenção de soluções ioT de nível empresarial. Optar por construir com a Azure IoT Central dá-lhe a oportunidade de concentrar o seu tempo, dinheiro e energia na transformação do seu negócio com dados ioT, em vez de apenas manter e atualizar uma infraestrutura ioT complexa e em constante evolução.

Este artigo descreve vários modelos de aplicação IoT Central específicos para o retalho. Como construtor de soluções, pode utilizar estes modelos para construir soluções IoT que otimizam as cadeias de abastecimento, melhoram as experiências na loja para os clientes e rastreiem o inventário de forma mais eficiente.

> [!div class="mx-imgBorder"]
> ![Visão geral do retalho Azure IoT](./media/overview-iot-central-retail/retail-app-templates.png)

As seguintes secções descrevem as capacidades destes modelos de aplicação:

## <a name="connected-logistics"></a>Logística associada

Os gastos globais de logística deverão atingir os 10,6 biliões de dólares em 2020. O transporte de mercadorias representa a maior parte destes gastos e os fornecedores de transporte estão sob intensa pressão e constrangimentos concorrenciais.

Pode utilizar sensores IoT para recolher e monitorizar condições ambientais como temperatura, humidade, inclinação, choque, luz e a localização de um carregamento. Pode combinar telemetria recolhida a partir de sensores ioT e dispositivos com outras fontes de dados, como informações meteorológicas e de tráfego em sistemas de inteligência empresarial baseados na nuvem.

Os benefícios de uma solução logística conectada incluem:

* Monitorização de envio com rastreio e rastreio em tempo real. 
* Integridade do envio com monitorização da condição ambiente em tempo real.
* Segurança de roubo, perda ou danos de envios.
* Geo-esgrima, otimização de rotas, gestão da frota e análise de veículos.
* Previsão de partida previsível e chegada de envios.

As seguintes imagens mostram o painel de instrumentos fora da caixa no modelo de aplicação. O painel de instrumentos é totalmente personalizável para satisfazer os seus requisitos específicos de solução:

> [!div class="mx-imgBorder"]
> ![Painel logístico conectado](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![Painel logístico conectado](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Para saber mais, consulte o Deploy e caminhe através de um tutorial [de modelo de aplicação logística conectado.](./tutorial-iot-central-connected-logistics-pnp.md)

## <a name="digital-distribution-center"></a>Centro de distribuição digital

À medida que os fabricantes e os retalhistas estabelecem presenças mundiais, as suas cadeias de abastecimento ramificam-se e tornam-se mais complexas. Os consumidores esperam agora que estejam disponíveis grandes seleções de produtos e que esses bens cheguem dentro de um ou dois dias após a compra. Os centros de distribuição devem adaptar-se a estas tendências, ao mesmo tempo que ultrapassam as ineficiências existentes. 

Hoje, uma dependência do trabalho manual significa que escolher e embalar representa 55-65% dos custos do centro de distribuição. A recolha e embalagem manual também são tipicamente mais lentas do que os sistemas automatizados, e as necessidades de pessoal flutuantes rapidamente dificultam ainda mais o atingem os volumes de envio. Esta flutuação sazonal resulta num elevado volume de negócios do pessoal e aumenta a probabilidade de erros dispendiosos.

Soluções baseadas em câmaras ativadas por IoT podem proporcionar benefícios transformacionais permitindo um ciclo de feedback digital. Dados de todo o centro de distribuição conduzem a insights atuais que, por sua vez, resultam em melhores dados.

Os benefícios de um centro de distribuição digital incluem:

* As câmaras monitorizam os bens à medida que chegam e movem-se através do sistema transportador.
* Identificação automática de bens defeituosos.
* Rastreio de ordem eficiente.
* Custos reduzidos, melhor produtividade e utilização otimizada.

A imagem que se segue mostra o painel de instrumentos fora da caixa no modelo de aplicação. O painel de instrumentos é totalmente personalizável para satisfazer os seus requisitos específicos de solução: 

> [!div class="mx-imgBorder"]
> ![Painel do Centro de Distribuição Digital](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Para saber mais, consulte o [Deploy e caminhe através](./tutorial-iot-central-digital-distribution-center-pnp.md) de um tutorial de modelo de aplicação do centro de distribuição digital.

## <a name="in-store-analytics---condition-monitoring"></a>Análise na loja - monitorização de condições

Para muitos retalhistas, as condições ambientais dentro das suas lojas são um diferenciador chave dos seus concorrentes. Os retalhistas querem manter condições agradáveis dentro das suas lojas em benefício dos seus clientes.  

Como construtor de soluções, pode utilizar o modelo de aplicação de monitorização da condição de análise IoT Central para construir uma solução de ponta a ponta. O modelo de aplicação permite ligar-se digitalmente e monitorizar um ambiente de loja de retalho utilizando diferentes tipos de dispositivos de sensores. Estes dispositivos sensores geram telemetria que você pode converter em insights de negócio ajudando o retalhista a reduzir os custos operacionais e criar uma grande experiência para os seus clientes.

Utilize o modelo de aplicação para:

* Ligue uma variedade de sensores IoT a uma instância de aplicação IoT Central.
* Monitorize e gerencie a saúde da rede de sensores, bem como quaisquer dispositivos de gateway no ambiente.
* Crie regras personalizadas em torno das condições ambientais dentro de uma loja para desencadear alertas para os gestores de lojas.
* Transforme as condições ambientais dentro da sua loja em insights que a equipa de lojas de retalho pode usar para melhorar a experiência do cliente.
* Exportar os conhecimentos agregados sobre os pedidos de negócio existentes ou novos para fornecer informações úteis e atempadas ao pessoal retalhista.

O modelo de aplicação vem com um conjunto de modelos de dispositivoe utiliza um conjunto de dispositivos simulados para povoar o painel de instrumentos. 

A imagem que se segue mostra o painel de instrumentos fora da caixa no modelo de aplicação. O painel de instrumentos é totalmente personalizável para satisfazer os seus requisitos específicos de solução: 

> [!div class="mx-imgBorder"]
> ![Monitorização da condição de análise na loja](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Para saber mais, consulte a aplicação de análise Create na loja no tutorial [Central Azure IoT.](./tutorial-in-store-analytics-create-app-pnp.md)

## <a name="in-store-analytics---checkout"></a>Análise na loja - check-out

Para alguns retalhistas, a experiência de check-out dentro das suas lojas é um diferenciador chave dos seus concorrentes. Os retalhistas querem oferecer uma experiência de check-out suave dentro das suas lojas para incentivar os clientes a regressar.  

Como construtor de soluções, pode utilizar o modelo de aplicação de check-out ioT Central na loja para construir uma solução que fornece insights de toda a zona de checkout de uma loja para o pessoal de retalho. Por exemplo, os sensores podem fornecer informações sobre comprimentos de fila e tempos médios de espera para cada pista de check-out.

Utilize o modelo de aplicação para:

* Ligue uma variedade de sensores IoT a uma instância de aplicação IoT Central.
* Monitorize e gerencie a saúde da rede de sensores, bem como quaisquer dispositivos de gateway no ambiente.
* Crie regras personalizadas em torno da condição de check-out dentro de uma loja para desencadear alertas para o pessoal de retalho.
* Transforme as condições de check-out dentro da loja em insights que a equipa de lojas de retalho pode usar para melhorar a experiência do cliente.
* Exportar os conhecimentos agregados sobre os pedidos de negócio existentes ou novos para fornecer informações úteis e atempadas ao pessoal retalhista.

O modelo de aplicação vem com um conjunto de modelos de dispositivos e usa um conjunto de dispositivos simulados para povoar o painel com dados de ocupação de faixas. 

A imagem que se segue mostra o painel de instrumentos fora da caixa no modelo de aplicação. O painel de instrumentos é totalmente personalizável para satisfazer os seus requisitos específicos de solução: 

> [!div class="mx-imgBorder"]
> ![Check-out de Análise Na Loja](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Para saber mais, consulte a aplicação de análise Create na loja no tutorial [Central Azure IoT.](./tutorial-in-store-analytics-create-app-pnp.md)

## <a name="smart-inventory-management"></a>Gestão de inventário inteligente

O inventário é o stock de bens que um retalhista detém. A gestão do inventário é fundamental para garantir que o produto certo está no lugar certo na hora certa. Um retalhista deve equilibrar os custos de armazenamento de demasiado inventário face aos custos de não dispor de objetos suficientes em existências para satisfazer a procura.

Os dados ioT gerados a partir de etiquetas, balizas e câmaras de identificação de radiofrequência (RFID) proporcionam oportunidades para melhorar os processos de gestão de inventário. Pode combinar telemetria recolhida a partir de sensores ioT e dispositivos com outras fontes de dados, como informações meteorológicas e de tráfego em sistemas de inteligência empresarial baseados na nuvem.

Os benefícios da gestão inteligente do inventário incluem:

* Reduzir o risco de os itens estarem esgotados e garantir o nível de atendimento ao cliente desejado. 
* Análise aprofundada e insights sobre a precisão do inventário em tempo quase real.
* Ferramentas para ajudar a decidir sobre a quantidade certa de inventário para realizar para satisfazer as ordens do cliente.

Este modelo de aplicação centra-se na conectividade do dispositivo, e na configuração e gestão de dispositivos de leitor de baixa energia RFID e Bluetooth (BLE).

A imagem que se segue mostra o painel de instrumentos fora da caixa no modelo de aplicação. O painel de instrumentos é totalmente personalizável para satisfazer os seus requisitos específicos de solução:

> [!div class="mx-imgBorder"]
> ![Painel de Gestão de Inventário Inteligente](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Para saber mais, consulte o [Deploy e caminhe através](./tutorial-iot-central-smart-inventory-management-pnp.md) de um tutorial de modelo de aplicação de gestão de inventário inteligente.

## <a name="micro-fulfillment-center"></a>Centro do micro-cumprimento

No panorama do retalho cada vez mais competitivo, os retalhistas enfrentam constantemente pressões para colmatar o fosso entre a procura e o cumprimento. Uma nova tendência que tem surgido para fazer face à crescente procura dos consumidores é alojar o inventário perto dos clientes finais e das lojas que visitam.

O modelo de aplicação do centro de micro-realização IoT Central permite aos construtores de soluções monitorizar e gerir todos os aspetos dos seus centros de realização totalmente automatizados. O modelo inclui um conjunto de sensores de monitorização de condição simulada e portadores robóticos para acelerar o processo de desenvolvimento da solução. Estes dispositivos de sensores captam sinais significativos que podem ser convertidos em insights de negócio, permitindo aos retalhistas reduzir os seus custos operacionais e criar experiências para os seus clientes.

O modelo de aplicação permite:: 

- Ligue perfeitamente diferentes tipos de sensores IoT, como robôs ou sensores de monitorização de condições a uma instância de aplicação IoT Central.
- Monitorize e gerencie a saúde da rede de sensores, e quaisquer dispositivos de gateway no ambiente.
- Crie regras personalizadas em torno das condições ambientais dentro de um centro de realização para desencadear alertas apropriados.
- Transforme as condições ambientais dentro do seu centro de realização em insights que podem ser alavancados pela equipa de armazém de retalho.
- Exportar os conhecimentos agregados sobre os pedidos de negócio existentes ou novos em benefício dos membros do pessoal retalhista.

A imagem que se segue mostra o painel de instrumentos fora da caixa no modelo de aplicação. O painel de instrumentos é totalmente personalizável para satisfazer os seus requisitos específicos de solução:

> [!div class="mx-imgBorder"]
> ![Centro de Micro-realização](./media/overview-iot-central-retail/MFC-Dashboard.png)

Para saber mais, consulte o Deploy e caminhe pelo tutorial [de modelo de aplicação do centro de micro-realização.](./tutorial-micro-fulfillment-center-pnp.md)

## <a name="next-steps"></a>Passos seguintes

Para começar a construir uma solução de retalho:

* Inicie-se com a aplicação de análise Create a store no tutorial [Central Azure IoT](./tutorial-in-store-analytics-create-app-pnp.md) que o acompanha como construir uma solução com um dos modelos de aplicação de análise na loja.
* [Desdobre e caminhe através de um modelo de aplicação logística conectado](./tutorial-iot-central-connected-logistics-pnp.md).
* [Desdobre e caminhe através de um modelo](./tutorial-iot-central-digital-distribution-center-pnp.md)de aplicação do centro de distribuição digital .
* [Desdobre e caminhe através de um modelo de aplicação](./tutorial-iot-central-smart-inventory-management-pnp.md)de gestão de inventário inteligente .
* [Desloque e caminhe pelo modelo de aplicação do centro de micro-realização](./tutorial-micro-fulfillment-center-pnp.md).
* Saiba mais sobre a IoT Central na visão geral da [IoT Central.](../preview/overview-iot-central.md)
