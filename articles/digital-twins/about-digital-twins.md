---
title: Descrição geral do Azure Digital Twins | Microsoft Docs
description: Saiba mais sobre o Azure Digital Twins, uma solução do Azure IoT para inteligência espacial.
author: julieseto
ms.author: jseto
ms.date: 12/14/2018
ms.topic: overview
ms.service: digital-twins
services: digital-twins
manager: bertvanhoof
ms.custom: mvc
ms.openlocfilehash: 41a6b040c04c3a212a7ee89897b29f5ec96048d7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072182"
---
# <a name="overview-of-azure-digital-twins"></a>Descrição geral do Azure Digital Twins

O Azure Digital Twins é um serviço do Azure IoT que cria modelos abrangentes do ambiente físico. Ele pode criar gráficos de inteligência geográficos para modelar as relações e interações entre pessoas, espaços e dispositivos.

Duplos Digital do Azure, pode consultar dados a partir de um espaço físico, em vez de vários sensores diferentes. Este serviço ajuda-o a criar experiências de reutilizáveis, altamente dimensionáveis e com reconhecimento de spatially vinculados a dados de transmissão em fluxo em todo o mundo digital e físico. As suas aplicações, são aprimoradas por estas funcionalidades contextuais exclusivamente relevantes. Os gémeos Digital do Azure podem ser utilizados para as seguintes tarefas de exemplo para:

- Prever as necessidades de manutenção de uma fábrica.
- Analise os requisitos de energia em tempo real para uma rede elétrica.
- Otimize a utilização de espaço disponível para um office.

Os gémeos Digital do Azure aplica-se a todos os tipos de ambientes. Apenas alguns exemplos são armazéns, escritórios, instituições de ensino, hospitais e bancos. Ele ainda pode ser usado para stadiums, fábricas, muitos de estacionamento, parques, grades inteligentes e cidades. Os gémeos Digital do Azure podem ser utilizados nos seguintes cenários de exemplo para:

- Monitorizar a temperatura diária em vários Estados.
- Monitorizar os caminhos de drones ocupado.
- Identifica veículos autónomos.
- Analise os níveis de ocupação de um edifício.
- Encontre a caixa Registadora mais ocupada no seu arquivo.

O que é o seu cenário de negócio do mundo real, é muito provável, que uma instância correspondente digital pode ser aprovisionada através de duplos Digital do Azure.

O vídeo seguinte assume um olhar mais atento duplos Digital do Azure.

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Principais capacidades

Os gémeos Digital do Azure tem as principais capacidades seguintes.

### <a name="spatial-intelligence-graph"></a>Gráfico de inteligência espacial

O [ *gráfico intelligence geográficos*](./concepts-objectmodel-spatialgraph.md), ou *gráfico geográfico*, é uma representação virtual do ambiente físico. Pode usá-lo para modelar as relações entre as pessoas, lugares e dispositivos.

Considere um aplicativo de utilitário inteligente que envolve vários medidores de utilização de eletricidade ligados num ambiente. A empresa prestadora inteligente com precisão tem de monitorizar e prever a utilização de eletricidade e de faturação. Cada dispositivo e o sensor devem ser modelado com contexto sobre a localização e o cliente que está a ser faturados. Pode usar o gráfico de inteligência geográficos para modelar esses tipos de relações complexas.

### <a name="digital-twin-object-models"></a>Modelos de objetos gémeos digitais

[Modelos de objeto do gémeo digital](./concepts-objectmodel-spatialgraph.md) são protocolos de predefinições do dispositivo e o esquema de dados. Eles se alinham necessidades específicas de domínio de sua solução para acelerar e simplificar o desenvolvimento.

Um exemplo é que um aplicativo de ocupação de sala pode usar tipos de espaço predefinidas, como o campus, edifício, piso e sala.

### <a name="multiple-and-nested-tenants"></a>Vários inquilinos aninhados

Pode criar soluções que dimensionar de forma segura e podem ser reutilizadas para vários inquilinos. Também pode criar vários subtenants que podem ser acedidas e utilizadas de forma isolada e segura.

Um exemplo é uma aplicação de utilização de espaço que está configurada para isolar os dados de um inquilino dos dados dos outros inquilinos dentro de um prédio único. Ou a aplicação é usada para combinar dados de um único inquilino com vários prédios.

### <a name="advanced-compute-capabilities"></a>Capacidades de computação avançadas

Com o [funções definidas pelo utilizador](./concepts-user-defined-functions.md), pode definir e executar funções personalizadas em relação a entrada [dados de dispositivo](./concepts-device-ingress.md) para enviar sinais para pontos finais predefinidos. Esta capacidade avançada melhora a personalização e automação de tarefas de dispositivo.

Um exemplo é um aplicativo de agricultura inteligente que inclui uma função definida pelo utilizador para avaliar as leituras dos sensores de moisture solo e a previsão meteorológica. A aplicação, em seguida, envia os sinais sobre as necessidades de irrigação tipo.

### <a name="built-in-access-control"></a>Controlo de acesso incorporado

Ao utilizar as funcionalidades de gestão de identidades e acesso como [controlo de acesso baseado em funções](./security-role-based-access-control.md) e [Azure Active Directory](./security-authenticating-apis.md), com segurança pode controlar o acesso para utilizadores individuais e dispositivos.

Um exemplo é uma aplicação de gestão de recursos que está configurada para permitir occupants de uma sala de definir a temperatura dentro de um intervalo especificado. Gestores de instalações têm permissão para definir a temperatura em qualquer espaço para qualquer valor.

### <a name="ecosystem"></a>Ecossistema

Pode ligar-se uma instância de duplos Digital do Azure para muitos serviços do Azure poderosos. Esses serviços incluem o Azure Stream Analytics, IA do Azure e armazenamento do Azure. Eles também incluem o Azure Maps, realidade mista da Microsoft, do Dynamics 365 ou do Office 365.

Um exemplo é um aplicativo que usa duplos Digital do Azure para representar as equipes e dispositivos localizados em vários andares de edifício de escritórios inteligente. Como dispositivos transmitir dados em direto para a instância de gémeo Digital aprovisionada, o Stream Analytics processa esses dados para fornecer informações acionáveis de chave. Os dados são armazenados no armazenamento do Azure e convertidos num formato de arquivo partilhável. O arquivo é distribuído em toda a organização através do Office 365.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Soluções que tiram partido do Azure Digital Twins

Os gémeos Digital do Azure é útil para representar o mundo físico e suas relações muitos. Ele simplifica a Modelagem de IoT, processamento de dados, manipulação de eventos e de controlo do dispositivo. Considere alguns dos cenários a seguir em várias indústrias. Eles se beneficiam de seu uso para:

* Mostre os níveis de ocupação de um espaço de uma empresa de gestão de propriedade ao longo do tempo para recolher informações sobre as melhores formas de configurar seu edifício de escritórios.
* Pedidos de ordem de trabalho de Acionador para uma aplicação móvel. Utilize-o para expedir a proteção de segurança e agenda janitorial e outros serviços no local de espaço ou Esportes de varejo.
* Mostre occupant uma construção que salas estão ocupadas num edifício em tempo real. Em seguida, ajudam a occupant reservar os espaços de trabalho que se adequam às suas necessidades.
* Controle onde os recursos estão localizados dentro de um espaço.
* Otimize o veículo electric cobrar modelando as preferências do usuário e restrições de rede energética.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Gémeos Digital do Azure no contexto de outros serviços de IoT

O Azure Digital Twins utiliza o Hub IoT do Azure para ligar os dispositivos de IoT e os sensores que mantêm tudo atualizado com o mundo físico. O diagrama seguinte mostra como duplos Digital do Azure se relaciona com outros serviços do Azure IoT.

![O Azure Digital Twins é um serviço criado com base no Hub IoT do Azure][1]

Para obter mais informações sobre o IoT, veja [do Azure IoT tecnologias e soluções](https://docs.microsoft.com/azure/iot-fundamentals/iot-services-and-technologies).

## <a name="next-steps"></a>Passos Seguintes

Ir para uma breve demonstração sobre duplos Digital do Azure:

>[!div class="nextstepaction"]
>[Quickstart: Salas disponíveis ao utilizar o gémeos Digital do Azure](./quickstart-view-occupancy-dotnet.md)

Examinar um aplicativo de gerenciamento de recursos utilizando duplos Digital do Azure:

>[!div class="nextstepaction"]
>[Tutorial: Implementar duplos Digital do Azure e configurar um gráfico espacial](./tutorial-facilities-setup.md)

Saiba mais sobre os principais conceitos do Azure Digital Twins:

>[!div class="nextstepaction"]
>[Compreender o modelo de objeto de duplos Digital e gráfico de inteligência espaciais](./concepts-objectmodel-spatialgraph.md)

<!-- Images -->
[1]: media/overview/azure-digital-twins-in-iot-ecosystem.png