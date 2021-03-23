---
title: Plataforma Azure Industrial IoT
description: Este artigo fornece uma visão geral da Plataforma IoT Industrial e seus componentes.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 24932b17c630190cb3121d5310a865758fa6a920
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801558"
---
# <a name="what-is-the-industrial-iot-iiot-platform"></a>O que é a Plataforma De IoT Industrial (IIoT)?

A Azure Industrial IoT Platform é um conjunto microsoft de módulos e serviços que são implantados no Azure. Estes módulos e serviços abraçaram totalmente a abertura. Especificamente, aplicamos a plataforma gerida da Azure como uma oferta de serviço (PaaS), software de código aberto licenciado através da licença DO MIT, padrões internacionais abertos de comunicação (OPC UA, AMQP, MQTT, HTTP) e interfaces (Open API), e modelos de dados industriais abertos (OPC UA) na borda e na nuvem.

## <a name="enabling-shopfloor-connectivity"></a>Habilitação da conectividade do piso de loja 

A Plataforma Azure Industrial IoT abrange a conectividade industrial dos ativos do shopfloor (incluindo a descoberta de ativos ativados pela OPC UA), normaliza os seus dados no formato OPC UA e transmite dados de telemetria de ativos à Azure no formato OPC UA PubSub. Lá, armazena os dados da telemetria numa base de dados em nuvem. Além disso, a plataforma permite o acesso seguro aos ativos do piso de loja através da OPC UA a partir da nuvem. As capacidades de gestão do dispositivo (incluindo a configuração de segurança) também são incorporadas. A funcionalidade OPC UA foi construída usando a tecnologia de contentores Docker para facilitar a implementação e gestão. Para ativos não-OPC UA, estabelecemos uma parceria com os principais fornecedores de conectividade industrial e ajudámo-los a apresentar o seu software de adaptador OPC UA ao Azure IoT Edge. Estes adaptadores estão disponíveis no Azure Marketplace.

## <a name="industrial-iot-components-iot-edge-modules-and-cloud-microservices"></a>Componentes IoT industriais: módulos IoT Edge e microserviços em nuvem

Os serviços de borda são implementados como módulos Azure IoT Edge e executados no local. Os microserviços em nuvem são implementados como ASP.NET microserviços com interface REST e executados em Serviços Azure Kubernetes geridos ou autónomos no Azure App Service. Para serviços de borda e nuvem, fornecemos recipientes Docker pré-construídos no Microsoft Container Registry (MCR), removendo este passo para o cliente. Os serviços de borda e nuvem estão a alavancar-se uns aos outros e devem ser usados em conjunto. Também fornecemos scripts de implementação fáceis de usar que permitem implantar toda a plataforma com um único comando.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o que é a Plataforma Azure Industrial IoT, pode aprender sobre a Editora OPC:

> [!div class="nextstepaction"]
> [O que é a Editora OPC?](overview-what-is-opc-publisher.md)