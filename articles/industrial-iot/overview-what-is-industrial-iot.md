---
title: Visão geral do Azure Industrial IoT
description: Este artigo fornece uma visão geral da IoT Industrial. Explica a conectividade do piso da loja e os componentes de segurança no IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: ce582f810f483f2e5d3fdda2c3379ecad3842d51
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813277"
---
# <a name="what-is-industrial-iot-iiot"></a>O que é IoT Industrial (IIoT)?

![Propriedade industrial](media/overview-what-is-Industrial-IoT/icon-255-px.png)

O Microsoft Azure Industrial Internet of Things (IIoT) é um conjunto de módulos e serviços Azure que integram a potência da nuvem em pisos industriais e de lojas de fabrico. Utilizando interfaces abertas padrão da indústria, como a arquitetura unificada de [comunicações de plataformas abertas (OPC UA),](https://opcfoundation.org/about/opc-technologies/opc-ua/)o Azure IIoT fornece-lhe a capacidade de integrar dados de ativos e sensores - incluindo aqueles que já estão a operar no chão da sua fábrica - na nuvem Azure. Ter os seus dados na nuvem permite que seja usado de forma mais rápida e flexível como feedback para o desenvolvimento de processos empresariais e industriais transformadores.

## <a name="discover-register-and-manage-your-industrial-assets-with-azure"></a>Descubra, registe e gere os seus Ativos Industriais com a Azure

O Azure Industrial IoT permite que os operadores de fábrica descubram servidores ativados pela OPC UA numa rede fabril e registem-nos no Azure IoT Hub. O pessoal das operações pode subscrever e reagir a eventos no chão da fábrica de qualquer parte do mundo, receber alertas e alarmes, e reagir a eles em tempo real.

O IIoT fornece um conjunto de Microservices que implementam a funcionalidade UA OPC. As APIs de REPOUSO Microservices espelham os serviços OPC UA edge-side. São protegidos através da autenticação e autorização da AAUTH apoiadas pelo Azure Ative Directory (AAD). Isto permite que as suas aplicações em nuvem naveguem em espaços de endereço do servidor ou leiam/escrevam variáveis e executem métodos usando HTTPS e cargas simples de OPC UA JSON. Os serviços de borda são implementados como módulos Azure IoT Edge e executados no local. Os microserviços em nuvem são implementados como ASP.NET microserviços com interface REST e executados em Serviços Azure Kubernetes geridos ou autónomos no Azure App Service. Para serviços de borda e nuvem, o IIoT fornece recipientes Docker pré-construídos no Microsoft Container Registry (MCR), removendo este passo para o cliente. Os serviços de borda e nuvem estão a alavancar-se uns aos outros e devem ser usados em conjunto. O IIoT também fornece scripts de implementação fáceis de usar que permitem implantar toda a plataforma com um único comando.

Além disso, as APIs REST podem ser utilizadas com qualquer linguagem de programação através da sua especificação aberta de API (Swagger). Isto significa que ao integrar o OPC UA em soluções de gestão de nuvem, os desenvolvedores são livres de escolher tecnologia que corresponda às suas habilidades, interesses e escolhas de arquitetura. Por exemplo, um desenvolvedor web de stack completo que desenvolve uma aplicação para um painel de alarme e evento pode escrever lógica para responder a eventos em JavaScript ou TypeScript sem se intensificar em um OPC UA SDK, C, C++, Java ou C#.

## <a name="manage-certificates-and-trust-groups"></a>Gerir certificados e grupos de confiança

A Azure Industrial IoT gere certificados de aplicação da UA EA EdC de máquinas de chão de fábrica e sistemas de controlo para manter o cliente da OPC UA seguro para a comunicação do servidor. Restringe o cliente que pode falar com qual servidor. O armazenamento de chaves privadas e a assinatura de certificados é apoiado pela Azure Key Vault, que suporta a segurança baseada em hardware (HSM).

## <a name="industrial-iot-components"></a>Componentes IoT industriais

As soluções Azure IIoT são construídas a partir de componentes específicos. Estes incluem o seguinte.

- **Pelo menos um Hub Azure IoT.**
- **Dispositivos IoT Edge.**
- **Módulos de Borda Industrial.**

### <a name="iot-hub"></a>IoT Hub
O [Azure IoT Hub], https://azure.microsoft.com/services/iot-hub/ funciona como um centro de mensagens central para comunicações seguras e bidicionais entre qualquer aplicação IoT e os dispositivos que gere. É uma plataforma de nuvem aberta e flexível como um serviço (PaaS) que suporta SDKs de código aberto e vários protocolos. 

Recolher os seus dados industriais e empresariais num Hub IoT permite-lhe armazenar os seus dados de forma segura, realizar análises de negócios e eficiência sobre os mesmos, e gerar relatórios a partir dos mesmos. Também pode aplicar serviços e ferramentas microsoft Azure, como [o Power BI,](https://powerbi.microsoft.com)nos seus dados consolidados.

### <a name="iot-edge-devices"></a>Dispositivos IoT Edge
Os [serviços de borda](https://azure.microsoft.com/services/iot-edge/) são implementados como módulos Azure IoT Edge e executados no local. Os microserviços em nuvem são implementados como ASP.NET microserviços com interface REST e executados em Serviços Azure Kubernetes geridos ou autónomos no Azure App Service. Para serviços de borda e nuvem, fornecemos recipientes Docker pré-construídos no Microsoft Container Registry (MCR), removendo este passo para o cliente. Os serviços de borda e nuvem estão a alavancar-se uns aos outros e devem ser usados em conjunto. Também fornecemos scripts de implementação fáceis de usar que permitem implantar toda a plataforma com um único comando.

Um dispositivo IoT Edge é composto por Módulos Edge Runtime e Edge.
- **Os Módulos Edge** são recipientes estivadores, que são a unidade mais pequena de computação, como a OPC Publisher e a OPC Twin. 
- **O dispositivo edge** é utilizado para implantar tais módulos, que funcionam como mediadores entre o servidor OPC UA e o IoT Hub na nuvem.

### <a name="industrial-edge-modules"></a>Módulos de borda industrial
- **Editor OPC**: A Editora OPC funciona dentro da IoT Edge. Conecta-se aos servidores da UA OPC e publica dados de telemetria codificados JSON destes servidores no formato OPC UA "Pub/Sub" ao Azure IoT Hub. Todos os protocolos de transporte suportados pelo cliente SDK do Azure IoT Hub podem ser utilizados, ou seja, HTTPS, AMQP e MQTT.
- **OPC Twin**: O OPC Twin é composto por microserviços que utilizam O Azure IoT Edge e IoT Hub para ligar a nuvem e a rede de fábrica. O OPC Twin fornece descoberta, registo e controlo remoto de dispositivos industriais através de APIs REST. OPC Twin não requer um OPC Uniified Architecture (OPC UA) SDK. É agnóstico de linguagem de programação, e pode ser incluído num fluxo de trabalho sem servidor.
- **Discovery**: O módulo de descoberta, representado pela identidade do descobridor, fornece serviços de descoberta no limite, que incluem a descoberta do servidor OPC UA. Se a descoberta estiver configurada e ativada, o módulo enviará os resultados de uma sonda de digitalização através do caminho de telemetria IoT Edge e IoT Hub para o serviço de onboarding. O serviço processa os resultados e atualiza todas as identidades relacionadas no Registo.

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o que é o IoT Industrial, pode ler sobre a Plataforma IoT Industrial e a Editora OPC:

> [!div class="nextstepaction"]
> [O que é a Editora OPC?](overview-what-is-opc-publisher.md)