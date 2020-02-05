---
title: Criando soluções de varejo com o Azure IoT Central | Microsoft Docs
description: Saiba como usar os modelos de aplicativo IoT Central do Azure para criar logística conectada, centro de distribuição digital, análise na loja, monitoramento de condição, check-out, gerenciamento de estoque inteligente e soluções de varejo.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 171f06ad238f862adbd7071ee10d81133d7a6855
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022142"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Criar soluções para o comércio com o Azure IoT Central



O Azure IoT Central é uma plataforma de aplicativo IoT que reduz a carga e o custo associados ao desenvolvimento, gerenciamento e manutenção de soluções de IoT de nível empresarial. Optar por criar com o Azure IoT Central oferece a oportunidade de concentrar seu tempo, dinheiro e energia ao transformar seus negócios com dados de IoT, em vez de apenas manter e atualizar uma infraestrutura de IoT complexa e continuamente em constante evolução.

Este artigo descreve vários modelos de aplicativo IoT Central específicos do varejo. Como um Solution Builder, você pode usar esses modelos para criar soluções de IoT que otimizam as cadeias de fornecimento, melhorar as experiências de armazenamento para os clientes e acompanhar o inventário com mais eficiência.

> [!div class="mx-imgBorder"]
> ![Azure IoT Retail Overview](./media/overview-iot-central-retail/retail-app-templates.png)

As seções a seguir descrevem os recursos desses modelos de aplicativos:

## <a name="connected-logistics"></a>Logística interligada

Os gastos globais de logística deverão atingir os 10,6 biliões de dólares em 2020. O transporte de contas de bens para a maioria desses gastos e provedores de envio está sob pressão intensa e restrições competitivas.

Você pode usar sensores IoT para coletar e monitorar condições de ambiente, como temperatura, umidade, inclinação, choque, luz e o local de uma remessa. Você pode combinar telemetria Obtida de sensores e dispositivos IoT com outras fontes de dados, como informações sobre clima e tráfego em sistemas business intelligence baseados em nuvem.

Os benefícios de uma solução de logística conectada incluem:

* Monitoramento de remessa com rastreamento e acompanhamento em tempo real. 
* Integridade de remessa com monitoramento de condição de ambiente em tempo real.
* Segurança contra roubo, perda ou danos de remessas.
* Isolamento geográfico, otimização de rota, gerenciamento de frota e análise de veículo.
* Previsão de saída previsível e chegada de remessas.

As capturas de tela a seguir mostram o painel integrado no modelo de aplicativo. O painel é totalmente personalizável para atender aos seus requisitos de solução específicos:

> [!div class="mx-imgBorder"]
> ![](./media/overview-iot-central-retail/connected-logistics-dashboard1.png) de painel de logística conectado

> [!div class="mx-imgBorder"]
> ![](./media/overview-iot-central-retail/connected-logistics-dashboard2.png) de painel de logística conectado

Para saber mais, consulte o tutorial [implantar e percorrer um modelo de aplicativo de logística conectado](./tutorial-iot-central-connected-logistics-pnp.md) .

## <a name="digital-distribution-center"></a>Centro de distribuição digital

À medida que os fabricantes e varejistas estabelecem presença em todo o mundo, suas cadeias de fornecimento se ramificam e se tornam mais complexas. Os consumidores agora esperam que grandes seleções de produtos estejam disponíveis e para que essas mercadorias cheguem em um ou dois dias após a compra. Os centros de distribuição devem se adaptar a essas tendências enquanto superam as ineficiências existentes. 

Hoje, uma dependência do trabalho manual significa que escolher e embalar representa 55-65% dos custos do centro de distribuição. A separação e o empacotamento manual também são normalmente mais lentos do que os sistemas automatizados, e a flutuação rápida das necessidades de equipe torna ainda mais difícil atender aos volumes de envio. Essa flutuação sazonal resulta em um grande giro da equipe e aumenta a probabilidade de erros dispendiosos.

Soluções baseadas em câmeras habilitadas para IoT podem fornecer benefícios transformativos habilitando um loop de comentários digitais. Os dados de todo o centro de distribuição levam a informações acionáveis que, por sua vez, resultam em dados melhores.

Os benefícios de um centro de distribuição digital incluem:

* As câmeras monitoram os bens à medida que chegam e passam pelo sistema transportador.
* Identificação automática de bens com falha.
* Acompanhamento eficiente de pedidos.
* Custos reduzidos, melhor produtividade e utilização otimizada.

A captura de tela a seguir mostra o painel integrado no modelo de aplicativo. O painel é totalmente personalizável para atender aos seus requisitos de solução específicos: 

> [!div class="mx-imgBorder"]
> ![](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png) do Painel de Instrumentos do Centro de Distribuição Digital

Para saber mais, consulte o tutorial [implantar e percorrer um modelo de aplicativo do centro de distribuição digital](./tutorial-iot-central-digital-distribution-center-pnp.md) .

## <a name="in-store-analytics---condition-monitoring"></a>Análise na loja – monitoramento de condição

Para muitos varejistas, as condições ambientais em suas lojas são um importante diferencial de seus concorrentes. Os varejistas querem manter condições agradáveis em suas lojas para o benefício de seus clientes.  

Como um construtor de soluções, você pode usar o IoT Central modelo de aplicativo de monitoramento de condição de análise no repositório para criar uma solução de ponta a ponta. O modelo de aplicação permite ligar-se digitalmente e monitorizar um ambiente de loja de retalho utilizando diferentes tipos de dispositivos de sensores. Esses dispositivos de sensor geram telemetria que você pode converter em informações de negócios ajudando o varejista a reduzir os custos operacionais e a criar uma ótima experiência para seus clientes.

Use o modelo de aplicativo para:

* Conecte uma variedade de sensores IoT a uma instância de aplicativo IoT Central.
* Monitore e gerencie a integridade da rede do sensor, bem como quaisquer dispositivos de gateway no ambiente.
* Crie regras personalizadas em volta das condições ambientais em um repositório para disparar alertas para gerentes de loja.
* Transforme as condições ambientais em seu armazenamento em informações que a equipe de loja de varejo pode usar para melhorar a experiência do cliente.
* Exporte as ideias agregadas em aplicativos de negócios novos ou existentes para fornecer informações úteis e oportunas para a equipe de varejo.

O modelo de aplicativo é fornecido com um conjunto de modelos de dispositivo e usa um conjunto de dispositivos simulados para popular o painel. 

A captura de tela a seguir mostra o painel integrado no modelo de aplicativo. O painel é totalmente personalizável para atender aos seus requisitos de solução específicos: 

> [!div class="mx-imgBorder"]
> ![](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png) de monitorização da condição de análise na loja

Para saber mais, consulte o tutorial [criar um aplicativo de análise na loja no Azure IOT central](./tutorial-in-store-analytics-create-app-pnp.md) .

## <a name="in-store-analytics---checkout"></a>Análise na loja-check-out

Para alguns varejistas, a experiência de check-out em suas lojas é um importante diferencial de seus concorrentes. Os varejistas desejam fornecer uma experiência de check-out suave em suas lojas para incentivar os clientes a retornarem.  

Como um construtor de soluções, você pode usar a IoT Central modelo de aplicativo de check-out da análise no repositório para criar uma solução que fornece informações sobre a zona de check-out de uma loja para a equipe de varejo. Por exemplo, os sensores podem fornecer informações sobre comprimentos de fila e tempos de espera médios para cada raia de check-out.

Use o modelo de aplicativo para:

* Conecte uma variedade de sensores IoT a uma instância de aplicativo IoT Central.
* Monitore e gerencie a integridade da rede do sensor, bem como quaisquer dispositivos de gateway no ambiente.
* Crie regras personalizadas em volta da condição de check-out dentro de um repositório para disparar alertas para a equipe de varejo.
* Transforme as condições de check-out dentro da loja em informações que a equipe da loja de varejo pode usar para melhorar a experiência do cliente.
* Exporte as ideias agregadas em aplicativos de negócios novos ou existentes para fornecer informações úteis e oportunas para a equipe de varejo.

O modelo de aplicativo vem com um conjunto de modelos de dispositivo e usa um conjunto de dispositivos simulados para popular o painel com dados de ocupação de Lane. 

A captura de tela a seguir mostra o painel integrado no modelo de aplicativo. O painel é totalmente personalizável para atender aos seus requisitos de solução específicos: 

> [!div class="mx-imgBorder"]
> ![](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png) de check-out de análise na loja

Para saber mais, consulte o tutorial [criar um aplicativo de análise na loja no Azure IOT central](./tutorial-in-store-analytics-create-app-pnp.md) .

## <a name="smart-inventory-management"></a>Gestão de inventário inteligente

O inventário é o estoque de mercadorias que um varejista retém. O gerenciamento de estoque é essencial para garantir que o produto correto esteja no lugar certo no momento certo. Um varejista deve equilibrar os custos de armazenamento em excesso de estoque em relação aos custos de não ter itens suficientes em estoque para atender à demanda.

Os dados de IoT gerados de marcas, beacons e câmeras de identificação de frequência de rádio (RFID) fornecem oportunidades para melhorar os processos de gerenciamento de inventário. Você pode combinar telemetria Obtida de sensores e dispositivos IoT com outras fontes de dados, como informações sobre clima e tráfego em sistemas business intelligence baseados em nuvem.

Os benefícios do gerenciamento de inventário inteligente incluem:

* Reduzir o risco de itens estarem fora de estoque e garantir o nível de serviço do cliente desejado. 
* Análise detalhada e informações sobre a precisão do inventário quase em tempo real.
* Ferramentas para ajudar a decidir a quantidade certa de estoque a ser mantida para atender a pedidos de clientes.

Esse modelo de aplicativo se concentra na conectividade do dispositivo e na configuração e no gerenciamento de dispositivos de leitor de RFID e Bluetooth de baixa energia (BLE).

A captura de tela a seguir mostra o painel integrado no modelo de aplicativo. O painel é totalmente personalizável para atender aos seus requisitos de solução específicos:

> [!div class="mx-imgBorder"]
> ![smart inventory management dashboard](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Para saber mais, confira o tutorial [implantar e percorrer um modelo de aplicativo de gerenciamento de inventário inteligente](./tutorial-iot-central-smart-inventory-management-pnp.md) .

## <a name="micro-fulfillment-center"></a>Centro de microatendimento

No cenário de varejo cada vez mais competitivo, os varejistas enfrentam constantemente a pressão para fechar a lacuna entre a demanda e o cumprimento. Uma nova tendência que surgiu para lidar com a demanda crescente do consumidor é colocar o inventário próximo aos clientes finais e às lojas que eles visitam.

O modelo de aplicativo do centro de microatendimento IoT Central permite que os integradores de solução monitorem e gerenciem todos os aspectos de seus centros de preenchimento totalmente automatizados. O modelo inclui um conjunto de sensores simulados de monitoramento de condição e operadoras robótica para acelerar o processo de desenvolvimento da solução. Esses dispositivos de sensor capturam sinais significativos que podem ser convertidos em informações de negócios, permitindo que os varejistas reduzam seus custos operacionais e criem experiências para seus clientes.

O modelo de aplicativo permite que você: 

- Ligue perfeitamente diferentes tipos de sensores IoT, como robôs ou sensores de monitorização de condições a uma instância de aplicação IoT Central.
- Monitorize e gerencie a saúde da rede de sensores, e quaisquer dispositivos de gateway no ambiente.
- Crie regras personalizadas em volta das condições ambientais em um centro de preenchimento para disparar os alertas apropriados.
- Transforme as condições ambientais em seu centro de atendimento em informações que podem ser aproveitadas pela equipe do Retail warehouse.
- Exporte as informações agregadas em aplicativos de negócios novos ou existentes para o benefício dos membros da equipe de varejo.

A captura de tela a seguir mostra o painel integrado no modelo de aplicativo. O painel é totalmente personalizável para atender aos seus requisitos de solução específicos:

> [!div class="mx-imgBorder"]
> ![Centro de Micro-realização](./media/overview-iot-central-retail/MFC-Dashboard.png)

Para saber mais, confira o tutorial [implantar e percorrer o modelo de aplicativo do centro de distribuição](./tutorial-iot-central-smart-inventory-management-pnp.md) .

## <a name="next-steps"></a>Passos seguintes

Para começar a criar uma solução de varejo:

* Introdução ao tutorial [criar um aplicativo de análise na loja no Azure IOT central](./tutorial-in-store-analytics-create-app-pnp.md) que explica como criar uma solução com um dos modelos de aplicativo de análise no repositório.
* [Implante e percorra um modelo de aplicativo de logística conectado](./tutorial-iot-central-connected-logistics-pnp.md).
* [Implante e percorra um modelo de aplicativo do centro de distribuição digital](./tutorial-iot-central-digital-distribution-center-pnp.md).
* [Implante e percorra um modelo de aplicativo de gerenciamento de inventário inteligente](./tutorial-iot-central-smart-inventory-management-pnp.md).
* [Implante e percorra o modelo de aplicativo do centro de microatendimento](./tutorial-iot-central-smart-inventory-management-pnp.md).
* Saiba mais sobre IoT Central na [IOT central visão geral](../preview/overview-iot-central.md).
