---
title: Gateways para dispositivos a jusante - Azure IoT Edge | Microsoft Docs
description: Utilize o Azure IoT Edge para criar um dispositivo de gateway transparente, opaco ou proxy que envia dados de vários dispositivos a jusante para a nuvem ou processa-os localmente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f95068b66fdd7907bf06086f855473b156738847
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371108"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>De que forma um dispositivo IoT Edge pode ser utilizado como gateway

Os dispositivos IoT Edge podem funcionar como portais, proporcionando uma ligação entre outros dispositivos na rede e o IoT Hub.

O módulo hub IoT Edge funciona como IoT Hub, assim como pode lidar com ligações de qualquer dispositivo que tenha uma identidade com o IoT Hub, incluindo outros dispositivos IoT Edge. Este tipo de padrão de gateway é chamado *transparente* porque as mensagens podem passar de dispositivos a jusante para IoT Hub como se não houvesse uma porta de entrada entre eles.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Começando pela versão 1.2 do IoT Edge, os gateways transparentes podem lidar com ligações a jusante de outros dispositivos IoT Edge.
::: moniker-end

Para dispositivos que não podem ou não podem ligar-se ao IoT Hub por si só, os gateways IoT Edge podem fornecer essa ligação. Este tipo de padrão de gateway é chamado *de tradução* porque o dispositivo IoT Edge tem de executar o processamento em mensagens de dispositivo a jusante antes de poderem ser reencaminhados para o IoT Hub. Estes cenários requerem módulos adicionais na porta de entrada IoT Edge para lidar com os passos de processamento.

Os padrões de gateway transparentes e de tradução não são mutuamente exclusivos. Um único dispositivo IoT Edge pode funcionar como um gateway transparente e um gateway de tradução.

Todos os padrões de gateway fornecem os seguintes benefícios:

* **Analytics at the edge** – Use serviços de IA localmente para processar dados provenientes de dispositivos a jusante sem enviar telemetria de fidelidade total para a nuvem. Encontre e reaja a insights localmente e envie apenas um subconjunto de dados para o IoT Hub.
* **Isolamento do dispositivo a jusante** – O dispositivo gateway pode proteger todos os dispositivos a jusante da exposição à internet. Pode situar-se entre uma rede de tecnologia operacional (OT) que não tem conectividade e uma rede de tecnologias da informação (TI) que fornece acesso à web. Da mesma forma, os dispositivos que não têm a capacidade de se ligarem ao IoT Hub por si só podem ligar-se a um dispositivo de gateway.
* **Multiplexing de ligação** - Todos os dispositivos que se ligam ao IoT Hub através de um gateway IoT Edge podem utilizar a mesma ligação subjacente. Esta capacidade de multiplexing requer que o gateway IoT Edge utilize amQP como protocolo a montante.
* **Alisamento do tráfego** - O dispositivo IoT Edge implementará automaticamente o backoff exponencial se o IoT Hub acelerar o tráfego, enquanto persiste as mensagens localmente. Este benefício torna a sua solução resiliente a picos de tráfego.
* **Suporte offline** - O dispositivo gateway armazena mensagens e duas atualizações que não podem ser entregues no IoT Hub.

## <a name="transparent-gateways"></a>Gateways transparentes

No padrão transparente de gateway, os dispositivos que teoricamente poderiam ligar-se ao IoT Hub podem ligar-se a um dispositivo de gateway. Os dispositivos a jusante têm as suas próprias identidades IoT Hub e conectam-se utilizando protocolos MQTT ou AMQP. O gateway simplesmente transmite as comunicações entre os dispositivos e o Hub IoT. Tanto os dispositivos como os utilizadores que interagem com eles através do IoT Hub desconhecem que um portal está a mediar as suas comunicações. Esta falta de consciência significa que a porta de entrada é considerada *transparente.*

Para obter mais informações sobre como o hub IoT Edge gere a comunicação entre dispositivos a jusante e a nuvem, consulte [o tempo de execução Azure IoT Edge e a sua arquitetura.](iot-edge-runtime.md)

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Os dispositivos IoT Edge não podem estar a jusante de um gateway IoT Edge.

![Diagrama - Padrão de gateway transparente](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

A partir da versão 1.2.0, os dispositivos IoT Edge podem ligar-se através de gateways transparentes.

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>Relacionamento entre pais e filhos

Você declara relações transparentes de gateway no IoT Hub, definindo o gateway IoT Edge como o *pai* de uma *criança* dispositivo a jusante que se conecta a ele.

A relação pai/filho é estabelecida em três pontos na configuração do gateway:

#### <a name="cloud-identities"></a>Identidades em nuvem

Todos os dispositivos num cenário de gateway transparente precisam de identidades em nuvem para que possam autenticar para o IoT Hub. Quando criar ou atualizar a identidade do dispositivo, pode configurar os dispositivos de pai ou filho do dispositivo. Esta configuração autoriza o dispositivo de porta de entrada dos pais a manusear a autenticação dos seus dispositivos para crianças.

>[!NOTE]
>A definição do dispositivo principal no IoT Hub costumava ser um passo opcional para dispositivos a jusante que utilizam a autenticação de chaves simétricas. No entanto, a partir da versão 1.1.0, todos os dispositivos a jusante devem ser atribuídos a um dispositivo-mãe.
>
>Pode configurar o hub IoT Edge para voltar ao comportamento anterior, definindo a variável ambiente **AuthenticationMode** para o valor **CloudAndScope**.

Os dispositivos infantis só podem ter um pai. Cada pai pode ter até 100 filhos.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Os dispositivos IoT Edge podem ser pais e filhos em relações transparentes de gateway. Pode criar-se uma hierarquia de múltiplos dispositivos IoT Edge que reportam entre si. O nó superior de uma hierarquia de gateway pode ter até cinco gerações de crianças. Por exemplo, um dispositivo IoT Edge pode ter cinco camadas de dispositivos IoT Edge ligados como crianças abaixo dele. Mas o dispositivo IoT Edge na quinta geração não pode ter filhos, IoT Edge ou outros.
::: moniker-end

#### <a name="gateway-discovery"></a>Descoberta gateway

Um dispositivo infantil precisa de ser capaz de encontrar o seu dispositivo-mãe na rede local. Configure os dispositivos de gateway com um **nome de hospedeiro**, um nome de domínio totalmente qualificado (FQDN) ou um endereço IP, que os seus dispositivos para crianças utilizarão para o localizar.

Nos dispositivos IoT a jusante, utilize o parâmetro **gatewayHostname** na cadeia de ligação para apontar para o dispositivo principal.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Nos dispositivos IoT Edge a jusante, utilize o parâmetro **parent_hostname** no ficheiro config.yaml para apontar para o dispositivo principal.
::: moniker-end

#### <a name="secure-connection"></a>Ligação segura

Os dispositivos dos pais e das crianças também precisam de autenticar as suas ligações entre si. Cada dispositivo necessita de uma cópia de um certificado de CA de raiz partilhada que os dispositivos infantis utilizam para verificar se estão a ligar-se ao portal adequado.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Quando vários gateways IoT Edge se ligam entre si numa hierarquia de gateway, todos os dispositivos da hierarquia devem utilizar uma cadeia de certificados única.
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>Capacidades do dispositivo por trás de gateways transparentes

Todos os primitivos IoT Hub que trabalham com o pipeline de mensagens IoT Edge também suportam cenários transparentes de gateway. Cada gateway IoT Edge tem capacidades de loja e de encaminho para mensagens que passam por ela.

Utilize a tabela seguinte para ver como as diferentes capacidades do IoT Hub são suportadas para dispositivos em comparação com dispositivos por trás de gateways.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

| Funcionalidade | Dispositivo IoT | IoT atrás de um portal |
| ---------- | ---------- | -------------------- |
| [Mensagens dispositivo-a-nuvem (D2C)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Sim - IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - ioT D2C infantil](./media/iot-edge-as-gateway/check-yes.png) |
| [Mensagens cloud-to-device (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Sim - IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - IoT criança C2D](./media/iot-edge-as-gateway/check-yes.png) |
| [Métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Sim - Método direto IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - método direto ioT infantil](./media/iot-edge-as-gateway/check-yes.png) |
| [Gémeos do dispositivo](../iot-hub/iot-hub-devguide-device-twins.md) e [gémeos módulos](../iot-hub/iot-hub-devguide-module-twins.md) | ![Sim - Gémeos IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - gémeos IoT crianças](./media/iot-edge-as-gateway/check-yes.png) |
| [Upload de arquivo](../iot-hub/iot-hub-devguide-file-upload.md) | ![Sim - Upload de ficheiros IoT](./media/iot-edge-as-gateway/check-yes.png) | ![No - IoT child file upload](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| Funcionalidade | Dispositivo IoT | IoT atrás de um portal | Dispositivo do IoT Edge | IoT Edge atrás de um portal |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [Mensagens dispositivo-a-nuvem (D2C)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Sim - IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - ioT D2C infantil](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - criança IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Mensagens cloud-to-device (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Sim - IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - IoT criança C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Não - IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![Não - IoT Edge criança C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [Métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Sim - Método direto IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - método direto ioT infantil](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - Método direto IoT Edge](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - método direto IoT Edge infantil](./media/iot-edge-as-gateway/check-yes.png) |
| [Gémeos do dispositivo](../iot-hub/iot-hub-devguide-device-twins.md) e [gémeos módulos](../iot-hub/iot-hub-devguide-module-twins.md) | ![Sim - Gémeos IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - gémeos IoT crianças](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - Gémeos IoT Edge](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - criança IoT Edge gémeos](./media/iot-edge-as-gateway/check-yes.png) |
| [Upload de arquivo](../iot-hub/iot-hub-devguide-file-upload.md) | ![Sim - Upload de ficheiros IoT](./media/iot-edge-as-gateway/check-yes.png) | ![No - IoT child file upload](./media/iot-edge-as-gateway/crossout-no.png) | ![No - Upload de ficheiro IoT Edge](./media/iot-edge-as-gateway/crossout-no.png) | ![No - IoT Edge child file upload](./media/iot-edge-as-gateway/crossout-no.png) |
| Imagem de recipiente puxa |   |   | ![Sim - Puxação do recipiente IoT Edge](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - puxar o recipiente IoT Edge infantil](./media/iot-edge-as-gateway/check-yes.png) |
| Upload blob |   |   | ![Sim - IoT Edge blob upload](./media/iot-edge-as-gateway/check-yes.png) | ![Sim - upload de blob ioT Edge infantil](./media/iot-edge-as-gateway/check-yes.png) |

**As imagens do contentor** podem ser descarregadas, armazenadas e entregues de dispositivos parentais para dispositivos infantis.

**As bolhas**, incluindo pacotes de suporte e troncos, podem ser carregadas de dispositivos infantis para dispositivos-mãe.

::: moniker-end

## <a name="translation-gateways"></a>Gateways de tradução

Se os dispositivos a jusante não conseguirem ligar-se ao IoT Hub, então o gateway IoT Edge tem de funcionar como tradutor. Muitas vezes, este padrão é necessário para dispositivos que não suportam MQTT, AMQP ou HTTP. Uma vez que estes dispositivos não conseguem ligar-se ao IoT Hub, também não conseguem ligar-se ao módulo hub IoT Edge sem algum pré-processamento.

Os módulos personalizados ou de terceiros que são frequentemente específicos do hardware ou protocolo do dispositivo a jusante precisam de ser implantados no gateway IoT Edge. Estes módulos de tradução pegam nas mensagens recebidas e transformam-nas num formato que pode ser entendido pelo IoT Hub.

Existem dois padrões para os gateways de tradução: *tradução protocolar* e *tradução de identidade.*

![Diagrama - padrões de gateway de tradução](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>Tradução do protocolo

No padrão de gateway de tradução protocolar, apenas o gateway IoT Edge tem uma identidade com IoT Hub. O módulo de tradução recebe mensagens de dispositivos a jusante, traduz-as num protocolo suportado e, em seguida, o dispositivo IoT Edge envia as mensagens em nome dos dispositivos a jusante. Toda a informação parece que vem de um dispositivo, o portal. Os dispositivos a jusante devem incorporar informações de identificação adicionais nas suas mensagens se as aplicações na cloud quiserem analisar os dados por dispositivo. Além disso, os primitivos IoT Hub como gémeos e métodos diretos são apenas suportados para o dispositivo gateway, e não para dispositivos a jusante. As portas neste padrão são consideradas *opacas* em contraste com gateways transparentes, porque obscurecem as identidades dos dispositivos a jusante.

A tradução protocolar suporta dispositivos que estão limitados a recursos. Muitos dispositivos existentes estão a produzir dados que podem alimentar insights de negócios; no entanto, não foram projetados com conectividade em nuvem em mente. Gateways opacos permitem que estes dados sejam desbloqueados e utilizados numa solução IoT.

### <a name="identity-translation"></a>Tradução de identidade

O padrão de gateway de tradução de identidade baseia-se na tradução protocolar, mas o gateway IoT Edge também fornece uma identidade de dispositivo IoT Hub em nome dos dispositivos a jusante. O módulo de tradução é responsável pela compreensão do protocolo utilizado pelos dispositivos a jusante, fornecendo-lhes identidade e traduzindo as suas mensagens em primitivos IoT Hub. Os dispositivos a jusante aparecem no Hub IoT como dispositivos de primeira classe com duplos e métodos. Um utilizador pode interagir com os dispositivos no Hub IoT e não tem conhecimento do dispositivo do gateway intermédio.

A tradução de identidade proporciona os benefícios da tradução do protocolo e permite ainda uma gestão completa dos dispositivos a jusante a partir da nuvem. Todos os dispositivos da sua solução IoT aparecem no IoT Hub independentemente do protocolo que utilizam.

### <a name="device-capabilities-behind-translation-gateways"></a>Capacidades do dispositivo por trás de gateways de tradução

A tabela seguinte explica como as funcionalidades do IoT Hub são estendidas a dispositivos a jusante em ambos os padrões de gateway de tradução.

| Funcionalidade | Tradução do protocolo | Tradução de identidade |
| ---------- | -------------------- | -------------------- |
| Identidades armazenadas no registo de identidade do IoT Hub | Apenas a identidade do dispositivo de gateway | Identidades de todos os dispositivos conectados |
| Dispositivo duplo | Só o portal tem um dispositivo e gémeos módulos | Cada dispositivo conectado tem o seu próprio dispositivo gémeo |
| Métodos diretos e mensagens nuvem-para-dispositivo | A nuvem só pode dirigir-se ao dispositivo gateway | A nuvem pode endereçar cada dispositivo conectado individualmente |
| [Aceleradores e quotas do Hub IoT](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Aplicar no dispositivo gateway | Aplicar a cada dispositivo |

Ao utilizar o padrão de tradução do protocolo, todos os dispositivos que se conectam através desse gateway partilham a mesma fila nuvem-dispositivo, que pode conter no máximo 50 mensagens. Utilize este padrão apenas quando poucos dispositivos estão a ligar-se através de cada porta de entrada de campo, e o tráfego entre nuvens e dispositivos é baixo.

O tempo de execução IoT Edge não inclui capacidades de protocolo ou tradução de identidade. Estes padrões requerem módulos personalizados ou de terceiros que são frequentemente específicos do hardware e do protocolo utilizados. [O Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) contém vários módulos de tradução protocolar para escolher. Para obter uma amostra que utilize o padrão de tradução de identidade, consulte [o Kit de Arranque Azure Edge LoRaWAN](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="next-steps"></a>Passos seguintes

Aprenda os três passos para configurar uma porta de entrada transparente:

* [Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo a jusante no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Ligar um dispositivo a jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
