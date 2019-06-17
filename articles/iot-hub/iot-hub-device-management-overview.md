---
title: Descrição geral da gestão de dispositivo hub IoT do Azure | Documentos da Microsoft
description: Descrição geral da gestão de dispositivos no Azure IoT Hu - padrões de gestão de enterprise dispositivo ciclo de vida e o dispositivo como reinício, reposição de fábrica, atualização de firmware, configuração, twins de dispositivo, consultas, tarefas.
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: bdc55af23568b5785a831e81f352400c728c902e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60400989"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Descrição geral da gestão de dispositivos com o Hub IoT

O Hub IoT do Azure fornece as funcionalidades e um modelo de extensibilidade que permitem ao dispositivo e aos programadores de back-end criarem soluções de gestão de dispositivos robustas. Os dispositivos variam desde sensores restritos e microcontroladores com um objetivo único, a gateways poderosos que encaminham as comunicações para grupos de dispositivos.  Além disso, os casos de utilização e os requisitos para operadores de IoT variam significativamente entre os setores.  Apesar desta variação, a gestão de dispositivos com o Hub IoT fornece as capacidades, os padrões e as bibliotecas de código para atender um conjunto diverso de dispositivos e utilizadores finais.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Uma parte fundamental da criação de uma solução de IoT empresarial bem-sucedida é fornecer uma estratégia para a forma como os operadores vão processar a gestão contínua da coleção de dispositivos. Os operadores de IoT requerem ferramentas simples e fiáveis e aplicações e que lhes permitam concentrar-se em aspetos mais estratégicos das funções deles. Este artigo fornece:

* Uma breve descrição geral da abordagem do Hub IoT perante a gestão de dispositivos.
* Uma descrição dos princípios de gestão de dispositivos comuns.
* Uma descrição do ciclo de vida do dispositivo.
* Uma descrição geral dos padrões de gestão de dispositivos comuns.

## <a name="device-management-principles"></a>Princípios da gestão de dispositivos

O IoT inclui uma série de desafios de gestão de dispositivos únicos e qualquer solução de classe empresarial tem de abordar os seguintes princípios:

![Gráfico dos princípios da gestão de dispositivos](media/iot-hub-device-management-overview/image4.png)

* **Escala e automatização**: Soluções de IoT requerem ferramentas simples que consigam automatizar tarefas de rotina e permitam que uma equipa de operações relativamente pequena gerir milhões de dispositivos. Todos os dias, os operadores esperam processar operações com dispositivos remotamente, em massa, e serem alertados apenas quando ocorrem problemas que precisam da atenção direta deles.

* **Acessibilidade e compatibilidade**: O ecossistema de dispositivos é extremamente diversificado. As ferramentas de gestão têm de ser adaptadas, de modo a acomodarem uma multitude de classes de dispositivos, plataformas e protocolos. Os operadores têm de conseguir suportar muitos tipos de dispositivos, desde os mais restritos chips de processamento único incorporados a computadores poderosos e totalmente funcionais.

* **Reconhecimento de contexto**: Os ambientes de IoT são dinâmicos e em constante mudança. A fiabilidade do serviço é crucial. As operações de gestão de dispositivos devem ter em consideração os seguintes fatores para assegurar que esse período de indisponibilidade devido a manutenção não afeta operações empresariais críticas nem cria condições perigosas:

    * Janelas de manutenção de SLA
    * Estados de rede e de energia
    * Condições em utilização
    * Geolocalização de dispositivos

* **Assistência a muitas funções**: Suporte para os fluxos de trabalho exclusivos e os processos de funções de operações de IoT é crucial. A equipa de operações tem de trabalhar em harmonia com as restrições dadas de departamentos de TI internos.  Ela também tem de encontrar formas sustentáveis de fornecer informações de operações de dispositivos em tempo real a supervisores e outras funções de gestão empresarial.

## <a name="device-lifecycle"></a>Ciclo de vida dos dispositivos
Existe um conjunto de fases de gestão de dispositivos geral que são comuns a todos os projetos de IoT empresarial. No Azure IoT, existem cinco fases do ciclo de vida dos dispositivos:

![As cinco fases do ciclo de vida de dispositivos do Azure IoT: planear, aprovisionar, configurar, monitorizar, extinguir](./media/iot-hub-device-management-overview/image5.png)

Dentro de cada uma destas cinco fases, existem vários requisitos de operador de dispositivo que devem ser cumpridos para disponibilizar uma solução completa:

* **Planear**: Permitir aos operadores criarem um esquema de metadados de dispositivo que permite que sejam facilmente e com precisão consultar e segmentar um grupo de dispositivos para operações de gestão em massa. Pode utilizar os twins do dispositivo para armazenar metadados deste dispositivo sob a forma de etiquetas e propriedades.
  
    *Leitura adicional*: 
    * [Introdução aos dispositivos duplos](iot-hub-node-node-twin-getstarted.md)
    * [Compreender os dispositivos duplos](iot-hub-devguide-device-twins.md)
    * [Como utilizar propriedades dos dispositivos duplos](tutorial-device-twins.md)
    * [Melhores práticas para configuração do dispositivo dentro de uma solução de IoT](iot-hub-configuration-best-practices.md)

* **Aprovisionar**: Em segurança aprovisionar novos dispositivos ao IoT Hub e permitir aos operadores descobrir imediatamente as capacidades do dispositivo.  Utilize o registo de dispositivos do Hub para criar identidades e credenciais de dispositivos flexíveis e efetuar esta operação em massa através de uma tarefa. Crie dispositivos para comunicar as respetivas capacidades e condições através de propriedades dos dispositivos no twin do dispositivo.
  
    *Leitura adicional*: 
    * [Gerir identidades do dispositivo](iot-hub-devguide-identity-registry.md)
    * [Gestão em massa de identidades de dispositivos](iot-hub-bulk-identity-mgmt.md)
    * [Como utilizar propriedades dos dispositivos duplos](tutorial-device-twins.md)
    * [Melhores práticas para configuração do dispositivo dentro de uma solução de IoT](iot-hub-configuration-best-practices.md)
    * [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](https://azure.microsoft.com/documentation/services/iot-dps)

* **Configurar**: Facilite a alterações de configuração em massa e as atualizações de firmware para dispositivos, mantendo o estado de funcionamento e segurança. Efetue estas operações de gestão de dispositivos em massa com as propriedades pretendidas ou com métodos diretos e tarefas de difusão.
  
    *Leitura adicional*:
    * [Como utilizar propriedades dos dispositivos duplos](tutorial-device-twins.md)
    * [Configurar e monitorizar dispositivos de IoT em escala](iot-hub-auto-device-config.md)
    * [Melhores práticas para configuração do dispositivo dentro de uma solução de IoT](iot-hub-configuration-best-practices.md)

* **Monitor**: Monitorizar o estado de funcionamento de coleção do dispositivo global, o estado de operações contínuas e alertar os operadores relativamente a problemas que poderão precisar da atenção deles.  Aplique o twin do dispositivo para permitir aos dispositivos comunicarem as condições de funcionamento em tempo real e o estado das operações de atualização. Crie relatórios de dashboards eficientes que emitem os problemas mais imediatos com consultas twin do dispositivo.
  
    *Leitura adicional*: 
    * [Como utilizar propriedades dos dispositivos duplos](tutorial-device-twins.md)
    * [Linguagem de consulta do IoT Hub para dispositivos duplos, tarefas e encaminhamento de mensagens](iot-hub-devguide-query-language.md)
    * [Configurar e monitorizar dispositivos de IoT em escala](iot-hub-auto-device-config.md)
    * [Melhores práticas para configuração do dispositivo dentro de uma solução de IoT](iot-hub-configuration-best-practices.md)

* **Extinguir**: Substituir ou desativar dispositivos após uma falha, ciclo de atualização ou no fim da duração do serviço.  Utilize o twin do dispositivo para manter informações do dispositivo se o dispositivo físico estiver a ser substituído, ou arquivar se estiver a ser retirado. Utilize o registo de identidades do Hub IoT para revogar de forma segura identidades e credenciais de dispositivos.
  
    *Leitura adicional*: 
    * [Como utilizar propriedades dos dispositivos duplos](tutorial-device-twins.md)
    * [Gerir identidades do dispositivo](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Padrões da gestão de dispositivos

O Hub IoT permite o conjunto de padrões de gestão de dispositivos seguinte. O [tutoriais da gestão de dispositivos](iot-hub-node-node-device-management-get-started.md) mostram-lhe mais detalhadamente como pode expandir estes padrões para se ajustar ao seu cenário exato e como estruturar novos padrões com base nestes modelos de núcleo.

* **Reiniciar**: A aplicação de back-end informa o dispositivo através de um método que iniciou um reinício.  O dispositivo utiliza as propriedades reportadas no para atualizar o respetivo estado de reinício.
  
    ![Gráfico do padrão de reinício de gestão de dispositivos](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Reposição de fábrica**: A aplicação de back-end informa o dispositivo através de um método que iniciou uma reposição de fábrica. O dispositivo utiliza as propriedades reportadas para atualizar o respetivo estado da reposição de fábrica.
  
    ![Gráfico do padrão de reposição de fábrica de gestão de dispositivos](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Configuração**: A aplicação de back-end utiliza as propriedades pretendidas para configurar o software em execução no dispositivo. O dispositivo utiliza as propriedades reportadas para atualizar o respetivo estado de configuração.
  
    ![Gráfico do padrão de configuração de gestão de dispositivos](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Atualização de firmware**: A aplicação de back-end utiliza uma configuração de gestão de dispositivos automática para selecionar os dispositivos para receber a atualização, para dizer aos dispositivos onde encontrar a atualização e para monitorizar o processo de atualização. O dispositivo inicia um processo de vários passos para transferir, certifique-se e aplique a imagem de firmware e, em seguida, reinicie o dispositivo antes de voltar a ligar ao serviço IoT Hub. Ao longo do processo de vários passos, o dispositivo utiliza as propriedades reportadas para atualizar o respetivo progresso e estado.
  
    ![Gráfico do padrão de atualização de firmware de gestão de dispositivos](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **Relatório de estado e progresso**: A solução de fazer uma cópia final executa consultas de twin do dispositivo, num conjunto de dispositivos, para comunicar o estado e o progresso das ações em execução nos dispositivos.
  
    ![Gráfico do padrão de estado e progresso dos relatórios de gestão de dispositivos](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>Próximos Passos

As capacidades, padrões e bibliotecas de código que o Hub IoT fornece para gestão de dispositivos permitem criar aplicações IoT que satisfazem os requisitos do operador de IoT empresarial em cada fase do ciclo de vida do dispositivo.

Para saber mais sobre as funcionalidades de gestão de dispositivos no IoT Hub, veja a [introdução à gestão de dispositivos](iot-hub-node-node-device-management-get-started.md) tutorial.