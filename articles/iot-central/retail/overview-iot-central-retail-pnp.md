---
title: Criando soluções de varejo com o Azure IoT Central | Microsoft Docs
description: Aprenda a criar logística conectada, centro de distribuição digital, monitoramento de condição de análise no repositório, check-out e gerenciamento de estoque inteligente, soluções de varejo com o Azure IoT Central usando modelos de aplicativos.
author: KishorIoT
ms.author: nandab
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 7da5d7b80dfd07b742af5ff6225f26207747e58c
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615331"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Criando soluções de varejo com o Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

O Azure IoT Central é uma plataforma de aplicativo de Internet das Coisas (IoT) para construtores de solução que simplifica os desafios de criação de & de gerenciamento de aplicativos escalonáveis. Neste artigo, destacaremos vários modelos de aplicativos específicos de varejo dentro de IoT Central. Os construtores de solução podem aproveitar os modelos publicados para criar soluções de IoT para otimizar a cadeia de fornecedores, melhorar a experiência na loja para os clientes e acompanhar o inventário com mais eficiência.

> [!div class="mx-imgBorder"]
> Visão geral do ![Azure IoT Retail](./media/overview-iot-central-retail/retail-app-templates.png)


## <a name="what-is-connected-logistics-solution"></a>O que é uma solução de logística conectada?
Espera-se que gastos de logística global atinjam $10.6 TRN em 2020, o maior PIB por setor. O transporte de mercadorias é a maioria (70%) de despesas de logística total. Os provedores de envio estão sob pressão competitiva intensa e restrições. os provedores de 3PL estão lidando com intervalos de tempo cada vez maiores e custos de compensação crescente. A logística é ainda mais progotada pelos riscos apresentados por geopolítica, eventos Extreme Climatic e crime. 

Com a ajuda dos sensores IoT, podemos coletar & monitorar as condições de ambiente, ou seja, o local da temperatura, umidade, inclinação, choque, luz e remessa por meio de GPS em transformação multimodal, ou seja, ar, água, terra. Os dados coletados de sensores, dispositivos, clima & eventos podem ser integrados a sistemas de Business Intelligence baseados em nuvem. Os benefícios da solução de logística conectada são,
* Transferência de remessa com rastreamento em tempo real & rastreamento 
* Integridade da remessa com condições de ambiente em tempo real monitorando & cadeia Cold
* Segurança contra roubo, perda ou danos de remessa
* Isolamento geográfico, otimização de rota, gerenciamento de frota. Análise de veículo
* Prever & previsibilidade com as entradas de & de saída 

### <a name="out-of-box-experience"></a>Experiência inicial do box
Os parceiros podem aproveitar o modelo para desenvolver soluções de logística conectadas de ponta a ponta & os benefícios descritos. Esse modelo publicado concentra-se na conectividade do dispositivo, na configuração & gerenciamento de dispositivos no IoT Central. 

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![painel de logística conectado](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Observe que o painel acima é uma experiência de exemplo e você pode personalizar completamente esse aplicativo para se ajustar ao seu caso de uso desejado.

Introdução ao [tutorial de ponta a ponta](./tutorial-iot-central-connected-logistics-pnp.md) que explica como criar uma solução que aproveita um dos modelos de solução de logística conectada.



## <a name="what-is-digital-distribution-center-solution"></a>O que é a solução do centro de distribuição digital?
À medida que mais fabricantes e varejistas estabelecem presença em todo o mundo, suas cadeias de fornecimento foram ramificadas para se tornarem mais complexas do que nunca. Os centros de distribuição estão se tornando um desafio principal. O centro de distribuição/depósitos estão sentindo a Brunt da pressão do comércio eletrônico. Os consumidores agora esperam que as várias seleções de produtos estejam disponíveis e para que essas mercadorias cheguem em um ou dois dias após a compra. Os centros de distribuição devem se adaptar a essas tendências enquanto superam as ineficiências existentes. 

Hoje, uma superdependência do trabalho manual significa a separação e a embalagem das contas de 55-65% dos custos do centro de distribuição. Embora seja inadequado que a mão-de-obra manual reduz o centro de distribuição, a flutuação rápida das necessidades de equipe (a equipe de Natal é exibida em 10 vezes) torne-a ainda mais difícil de atender aos volumes de remessa. Essa flutuação sazonal resulta em rotatividade alta e a probabilidade de erros e a necessidade de aumentos de retrabalho dispendioso também.
Soluções baseadas em câmeras habilitadas para IoT podem fornecer benefícios transformativos habilitando um loop de comentários digitais. Esse influxo de dados de todo o centro de distribuição leva a informações acionáveis que, por sua vez, resultam em dados melhores.

Os benefícios são, 
* As câmeras monitoram os bens à medida que chegam e passam pelo sistema transportador
* Identificar mercadorias com falhas e enviá-las para reparo
* Manter o controle de pedidos com eficiência
* Reduza o custo, melhor produtividade & maximizar a utilização

### <a name="out-of-box-experience"></a>Experiência inicial do box
Os parceiros podem aproveitar esse modelo de aplicativo para criar o centro de distribuição digital para obter informações acionáveis & acima dos benefícios descritos. O modelo publicado está concentrado na configuração de conectividade do dispositivo & o gerenciamento de dispositivos de câmera e borda no IoT Central. 

> [!div class="mx-imgBorder"]
> Painel do centro de distribuição digital ![](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Observe que o painel acima é uma experiência de exemplo e você pode personalizar completamente esse aplicativo para se ajustar ao seu caso de uso desejado.

Introdução ao [tutorial de ponta a ponta](./tutorial-iot-central-digital-distribution-center-pnp.md) que explica como criar uma solução utilizando um dos modelos do centro de distribuição digital.



## <a name="what-is-in-store-analytics-condition-monitoring"></a>O que é monitoramento de condição de análise no repositório?
No panorama competitivo de hoje, os varejistas estão procurando novas maneiras de oferecer aos clientes algo exclusivo ou especial para impulsionar o tráfego por meio de seus armazenamentos físicos. Muitos varejistas reconhecem a importância das condições ambientais em sua loja como uma ferramenta para diferenciar seus concorrentes. Os varejistas querem garantir que eles mantenham condições agradáveis em suas lojas em todos os momentos para fornecer uma experiência confortável aos seus clientes.  

O modelo de aplicativo de monitoramento da condição de análise no repositório no IoT Central fornece o construtor de soluções com uma tela que pode ser utilizada para criar uma solução de ponta a ponta. O modelo de aplicativo permite que eles se conectem e monitorem digitalmente um ambiente de loja de varejo usando uma variedade de dispositivos de sensor. Esses dispositivos de sensor capturam sinais significativos que podem ser convertidos em informações de negócios, permitindo que os varejistas reduzam seus custos operacionais e criem experiências que seus clientes adoram.

O modelo de aplicativo permite que você:

*  Conecte diretamente uma variedade de sensores IoT a uma instância de aplicativo IoT Central.
*  Monitore e gerencie a integridade da rede do sensor, bem como os dispositivos de gateway no ambiente.
*  Crie regras personalizadas em volta das condições ambientais em um repositório para disparar alertas apropriados.
*  Transforme as condições de ambiente em seu repositório em informações que podem ser aproveitadas pela equipe da loja de varejo.
* Exporte as informações agregadas em aplicativos de negócios novos ou existentes, capacitando os membros da equipe de varejo.

### <a name="out-of-box-experience"></a>Experiência inicial do box
O modelo de aplicativo vem com um conjunto de modelos de dispositivo e uma experiência de operador pronta para uso. Ele aproveita um conjunto de dispositivos simulados para popular os elementos do painel. Depois de implantar um aplicativo de IoT Central usando o modelo [de aplicativo de monitoramento de condição de análise no repositório](https://aka.ms/conditiontemplate) , você irá para o painel de aplicativo padrão, conforme mostrado abaixo. 

> [!div class="mx-imgBorder"]
> ![monitoramento de condição de análise no repositório](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Observe que o painel acima é uma experiência de exemplo e você pode personalizar completamente esse aplicativo para se ajustar ao seu caso de uso desejado. 

Introdução ao [tutorial de ponta a ponta](./tutorial-in-store-analytics-create-app-pnp.md) que explica como criar uma solução usando um dos modelos de monitoramento de condição de análise no repositório.



## <a name="what-is-in-store-analytics-checkout"></a>O que é o check-out da análise na loja?
No cenário cada vez mais competitivo, os varejistas modernos enfrentam constantemente a pressão crescente para fornecer uma experiência na loja que exceda as expectativas dos clientes e as mantenha voltadas para o futuro. Embora vários varejistas tenham começado a implantar a tecnologia para atender a essa necessidade, uma área que é, em grande parte, despercebida, é a experiência de check-out.

O modelo de aplicativo de check-out da análise no repositório no IoT Central permite que os integradores de solução criem experiências que capacitam a equipe de varejo com informações significativas sobre a zona de check-out de sua loja. Ele aproveita um conjunto de dispositivos simulados para determinar o status de ocupação de cada uma das pistas de check-out em uma loja de varejo. Os sensores permitem que você capture a contagem de pessoas, bem como o tempo de espera médio para cada uma das pistas de check-out.

O modelo ajuda o Solution Builder a acelerar seus planos de entrada no mercado fornecendo uma solução de IoT de linha de base que permite que eles: 

* Conecte diretamente uma variedade de sensores IoT a uma instância de aplicativo IoT Central.
* Monitore e gerencie a integridade da rede do sensor, bem como os dispositivos de gateway no ambiente.
* Crie regras personalizadas em volta da condição de check-out dentro de um repositório para disparar alertas apropriados.
* Transforme as condições de check-out em seu repositório em informações que podem ser aproveitadas pela equipe da loja de varejo.
* Exporte as informações agregadas em aplicativos de negócios novos ou existentes, capacitando os membros da equipe de varejo.

### <a name="out-of-box-experience"></a>Experiência inicial do box
O modelo de aplicativo vem com um conjunto de modelos de dispositivo e uma experiência de operador pronta para uso. Ele aproveita um conjunto de dispositivos simulados para popular os elementos do painel. Depois de implantar um aplicativo IoT Central usando o modelo de aplicativo [de check-out da análise no repositório](https://aka.ms/checkouttemplate) , você irá para o painel do aplicativo padrão, conforme mostrado abaixo. 

> [!div class="mx-imgBorder"]
> ![check-out da análise na loja](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Observe que o painel acima é uma experiência de exemplo e você pode personalizar completamente esse aplicativo para se ajustar ao seu caso de uso desejado. 


Introdução ao [tutorial de ponta a ponta](./tutorial-in-store-analytics-create-app-pnp.md) que explica como criar uma solução que aproveita um dos modelos de check-out da análise na loja.


## <a name="what-is-smart-inventory-management-solution"></a>O que é a solução de gerenciamento de inventário inteligente?
"Inventário" é o estoque de mercadorias mantidas por um varejista. Cada varejista precisa de inventário para cuidar de fornecimento e tempo de avanço de logística. O inventário é, sem dúvida, o recurso mais valioso que cada varejista precisa fazer. No mundo de omnichannel de hoje, o gerenciamento de estoque é um requisito crítico para garantir que o produto certo esteja no lugar certo no momento certo. O armazenamento de estoque excessivo ou muito pequeno pode prejudicar os negócios do varejista. Todos os varejistas de ano estão perdendo 8-10% da receita devido à falta de recursos de gerenciamento de estoque.

Dados de IoT habilitados pela RFID (identificação de frequência de rádio), beacons & câmera é a oportunidade de lidar com esse grande desafio em escala. A conectividade e a análise em tempo real inerentes aos sinais de IoT se tornaram o alterador de jogo para o infortúnios de inventário do varejista.  Os dados coletados de sensores, dispositivos, clima & eventos podem ser integrados a sistemas de Business Intelligence baseados em nuvem.  
Os benefícios do gerenciamento de inventário inteligente são, 
* Protege a organização contra esgotamentos de estoque e garante o nível de serviço do cliente desejado. 
* Análise detalhada & informações sobre a precisão do inventário quase em tempo real
* Decida a quantidade certa de inventário que é suficiente para os pedidos do cliente

### <a name="out-of-box-experience"></a>Experiência inicial do box
Os parceiros podem aproveitar o modelo para desenvolver soluções de gerenciamento de inventário inteligente de ponta a ponta & os benefícios descritos. Esse modelo publicado concentra-se na conectividade do dispositivo, configuração & gerenciamento de RFID & leitores de BLE (Bluetooth de baixa energia) no IoT Central. 

> [!div class="mx-imgBorder"]
> Painel de gerenciamento de inventário inteligente do ![](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Observe que o painel acima é uma experiência de exemplo e você pode personalizar completamente esse aplicativo para se ajustar ao seu caso de uso desejado. 

Introdução ao [tutorial de ponta a ponta](./tutorial-iot-central-smart-inventory-management-pnp.md) que orienta você sobre como criar uma solução usando um dos modelos de gerenciamento de inventário inteligente.


## <a name="next-steps"></a>Passos seguintes
Para começar a criar uma solução de varejo:
* Introdução ao tutorial de [ponta a ponta](./tutorial-in-store-analytics-create-app-pnp.md) que explica como criar uma solução usando um dos modelos de aplicativo de análise no repositório.
* Saiba como implantar o [modelo de solução de logística conectada](./tutorial-iot-central-connected-logistics-pnp.md)
* Saiba como implantar o [modelo do centro de distribuição digital](./tutorial-iot-central-digital-distribution-center-pnp.md)
* Saiba como implantar o [modelo de gerenciamento de inventário inteligente](./tutorial-iot-central-smart-inventory-management-pnp.md)
* Saiba mais sobre IoT Central consulte [IOT central visão geral](../core/overview-iot-central-pnp.md)
