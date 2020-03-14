---
title: Visão geral - Gémeos Digitais Azure [ Azure Digital Twins ] Microsoft Docs
description: Saiba mais sobre o Azure Digital Twins, uma solução do Azure IoT para inteligência espacial.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.date: 12/30/2019
ms.topic: overview
ms.service: digital-twins
services: digital-twins
ms.custom: mvc
ms.openlocfilehash: 91386eaf7753f2aefada2d1423a11b366c3ee924
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370904"
---
# <a name="overview-of-azure-digital-twins-preview"></a>Visão geral da pré-visualização de gémeos digitais Azure

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Azure Digital Twins Preview é um serviço Azure IoT que cria modelos abrangentes do ambiente físico. Pode criar gráficos de inteligência espacial para modelar as relações e interações entre pessoas, espaços e dispositivos.

Com as Gémeas Digitais Azure, pode consultar dados de um espaço físico e não de muitos sensores díspares. Este serviço ajuda-o a construir experiências reutilizáveis, altamente escaláveis e espaciais que ligam dados de streaming em todo o mundo digital e físico. As suas aplicações são melhoradas por estas características contextuais únicas e relevantes. 

A Azure Digital Twins aplica-se a todo o tipo de ambientes, tais como armazéns, escritórios, escolas, hospitais e bancos. Pode até ser usado para estádios, fábricas, parques de estacionamento, parques, grades inteligentes e cidades. Seguem-se alguns cenários em que as Gémeas Digitais Azure podem ser úteis:

- Preveja necessidades de manutenção para uma fábrica.
- Analise os requisitos energéticos em tempo real para uma rede elétrica.
- Otimizar a utilização do espaço disponível para um escritório.
- Acompanhe a temperatura diária em vários estados.
- Monitorize os caminhos ocupados dos drones.
- Identificar veículos autónomos.
- Analise os níveis de ocupação de um edifício.
- Encontre a caixa registadora mais movimentada da sua loja.

Seja qual for o seu cenário de negócio no mundo real, é provável que uma instância digital correspondente possa ser aprovisionada através da Azure Digital Twins.

O vídeo que se segue dá uma vista de olhos às Gémeas Digitais do Azure.

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Principais capacidades

A Azure Digital Twins tem as seguintes capacidades-chave.

### <a name="spatial-intelligence-graph"></a>Gráfico de inteligência espacial

O gráfico de [*inteligência espacial*](./concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph), ou gráfico *espacial,* é uma representação virtual do ambiente físico. Pode usá-lo para modelar as relações entre pessoas, lugares e dispositivos.

Considere uma aplicação de utilidade inteligente que envolve vários contadores de uso de eletricidade conectados em um bairro. A empresa de serviços inteligentes deve monitorizar e prever com precisão o uso e faturação de eletricidade. Cada dispositivo e sensor deve ser modelado com contexto sobre a localização e o cliente que deve ser faturado. Pode usar o gráfico de inteligência espacial para modelar este tipo de relacionamentos complexos.

### <a name="digital-twin-object-models"></a>Modelos de objetos gémeos digitais

[Os modelos de objetos duplos digitais](./concepts-objectmodel-spatialgraph.md#digital-twins-object-models) são protocolos de dispositivos pré-definidos e esquema de dados. Alinham as necessidades específicas de domínio da sua solução para acelerar e simplificar o desenvolvimento.

Por exemplo, uma aplicação de ocupação de quarto pode usar tipos de espaço pré-definidos, tais como campus, edifício, piso e quarto.

### <a name="multiple-and-nested-tenants"></a>Vários inquilinos aninhados

Você pode construir soluções que escalam de forma segura e podem ser reutilizadas para vários inquilinos. Você também pode criar vários subinquilinos que podem ser acedidos e usados de forma isolada e segura.

Um exemplo é uma aplicação de utilização espacial que está configurada para isolar os dados de um inquilino dos dados de outros inquilinos dentro de um único edifício. Ou a aplicação é usada para combinar dados para um único inquilino com inúmeros edifícios.

### <a name="advanced-compute-capabilities"></a>Capacidades de computação avançadas

Com [funções definidas pelo utilizador,](./concepts-user-defined-functions.md)pode definir e executar funções personalizadas contra [os dados](./concepts-device-ingress.md) do dispositivo de entrada para enviar sinais para pontos finais pré-definidos. Esta capacidade avançada melhora a personalização e automatização das tarefas do dispositivo.

Um exemplo é uma aplicação de agricultura inteligente que inclui uma função definida pelo utilizador para avaliar as leituras do sensor de humidade do solo e a previsão meteorológica. A aplicação envia então sinais sobre as necessidades de irrigação.

### <a name="built-in-access-control"></a>Controlo de acesso incorporado

Utilizando funcionalidades de acesso e gestão de identidade, tais como [controlo de acesso baseado em papéis](./security-role-based-access-control.md) e [Diretório Ativo Azure,](./security-authenticating-apis.md)pode controlar de forma segura o acesso a indivíduos e dispositivos.

Um exemplo é uma aplicação de gestão de instalações que está configurada para permitir aos ocupantes de uma sala definir a temperatura dentro de um intervalo especificado. Os gestores de instalações podem definir a temperatura em qualquer quarto para qualquer valor.

### <a name="ecosystem"></a>Ecossistema

Pode ligar uma instância de Gémeos Digitais Azure a muitos serviços Azure poderosos. Estes serviços incluem Azure Stream Analytics, Azure AI e Azure Storage. Incluem também o Azure Maps, microsoft Mixed Reality, Dynamics 365 ou Office 365.

Um exemplo é uma aplicação inteligente de construção de escritórios que usa as Gémeas Digitais Azure para representar equipas e dispositivos localizados em muitos andares. À medida que os dispositivos transmitem dados ao vivo para a instância Digital Twin, o Stream Analytics processa esses dados para fornecer insights chave atuais. Os dados são armazenados no Armazenamento Azure e convertidos num formato de ficheiro compartilhável. O ficheiro é distribuído por toda a organização usando o Office 365.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Soluções que tiram partido do Azure Digital Twins

A Azure Digital Twins é útil para representar o mundo físico e as suas muitas relações. Simplifica a modelação ioT, o processamento de dados, o manuseamento de eventos e o rastreio do dispositivo. Considere apenas alguns dos seguintes cenários em várias indústrias. Beneficiam da sua utilização para:

* Mostre a uma empresa de gestão imobiliária os níveis de ocupação de um espaço ao longo do tempo para recolher informações sobre as melhores formas de configurar o seu edifício de escritórios.
* Desencadear bilhetes de encomenda de trabalho para uma aplicação móvel. Use-o para despachar seguranças e agendar serviços de limpeza e outros em um espaço de retalho ou local desportivo.
* Mostre a um ocupante do edifício quais os quartos que estão ocupados num edifício em tempo real. Em seguida, ajude o ocupante reserva espaços de trabalho que se adaptem às suas necessidades.
* Pista onde os ativos estão localizados dentro de um espaço.
* Otimize o carregamento de veículos elétricos modelando as preferências dos utilizadores e as restrições da rede de energia.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Gémeas Digitais Azure no contexto de outros serviços IoT

O Azure Digital Twins utiliza o Hub IoT do Azure para ligar os dispositivos de IoT e os sensores que mantêm tudo atualizado com o mundo físico. O diagrama que se segue mostra como as Gémeas Digitais Azure se relacionam com outros serviços da Azure IoT.

[![Azure Digital Twins é um serviço construído no topo do Azure IoT Hub](media/overview/azure-digital-twins-in-iot-ecosystem.png)](media/overview/azure-digital-twins-in-iot-ecosystem.png#lightbox)

Para mais informações sobre ioT, leia [tecnologias e soluções Azure IoT.](../iot-fundamentals/iot-services-and-technologies.md)

## <a name="next-steps"></a>Passos seguintes

Vá a uma pequena demonstração sobre As Gémeas Digitais Azure:

>[!div class="nextstepaction"]
>[Quickstart: Encontre quartos disponíveis utilizando gémeos digitais Azure](./quickstart-view-occupancy-dotnet.md)

Olhe atentamente para uma aplicação de gestão de instalações utilizando as Gémeas Digitais Azure:

>[!div class="nextstepaction"]
>[Tutorial: Deploy Azure Digital Twins and configure a spatial graph](./tutorial-facilities-setup.md) (Tutorial: Implementar o Azure Digital Twins e configurar um gráfico espacial)

Saiba mais sobre os principais conceitos do Azure Digital Twins:

>[!div class="nextstepaction"]
>[Compreenda o modelo de objeto sonísma digital twins e gráfico de inteligência espacial](./concepts-objectmodel-spatialgraph.md)