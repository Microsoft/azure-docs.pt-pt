---
title: Azure IoT Edge e Azure IoT Central | Microsoft Docs
description: Entenda como usar a Azure IoT Edge com uma aplicação IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: e0f3464420c5cb429f780999bf5983b2ab142567
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608636"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Ligar dispositivos Azure IoT Edge a uma aplicação do Azure IoT Central

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

O Azure IoT Edge move a análise de nuvem e a lógica de negócio personalizada para dispositivos para que a sua organização se concentre em insights de negócio em vez de gestão de dados. Dimensione a sua solução IoT embalando a sua lógica de negócio em recipientes padrão, coloque esses recipientes nos seus dispositivos e monitorize-os a partir da nuvem.

Este artigo descreve:

* Como os dispositivos IoT Edge se ligam a uma aplicação IoT Central.
* Como utilizar a IoT Central para gerir os seus dispositivos IoT Edge.

Para saber mais sobre ioT Edge, veja [o que é Azure IoT Edge?](../../iot-edge/about-iot-edge.md)

## <a name="iot-edge"></a>IoT Edge

IoT Edge é composto por três componentes:

* *Os módulos IoT Edge* são contentores que gerem serviços Azure, serviços de parceiros ou o seu próprio código. Os módulos são implantados em dispositivos IoT Edge e executados localmente nesses dispositivos. Para saber mais, consulte [os módulos Understand Azure IoT Edge](../../iot-edge/iot-edge-modules.md).
* O *tempo de execução IoT Edge* funciona em cada dispositivo IoT Edge e gere os módulos implantados em cada dispositivo. O tempo de funcionação é composto por dois módulos IoT Edge: *IoT Edge agent* e *IoT Edge hub*. Para saber mais, consulte [o tempo de execução Azure IoT Edge e a sua arquitetura.](../../iot-edge/iot-edge-runtime.md)
* Uma *interface baseada em nuvem permite-lhe* monitorizar e gerir remotamente dispositivos IoT Edge. IoT Central é um exemplo de uma interface de nuvem.

Um dispositivo IoT Edge pode ser:

* Um dispositivo autónomo composto por módulos.
* Um *dispositivo de gateway,* com dispositivos a jusante a ligarem-se a ele.

## <a name="iot-edge-as-a-gateway"></a>IoT Edge como porta de entrada

Um dispositivo IoT Edge pode funcionar como um gateway que fornece uma ligação entre outros dispositivos a jusante na rede e a sua aplicação IoT Central.

Existem dois padrões de gateway:

* No padrão transparente de *gateway,* o módulo hub IoT Edge comporta-se como IoT Central e lida com ligações de dispositivos registados na IoT Central. As mensagens passam dos dispositivos a jusante para a IoT Central como se não houvesse uma porta de entrada entre eles.

* No padrão de *gateway de tradução,* os dispositivos que não conseguem ligar-se à IoT Central por si só, conectam-se a um módulo IoT Edge personalizado. O módulo no dispositivo IoT Edge processa a entrada de mensagens de dispositivo a jusante e, em seguida, reencaminha-as para a IoT Central.

Os padrões transparentes e de tradução do gateway não são mutuamente exclusivos. Um único dispositivo IoT Edge pode funcionar como um gateway transparente e um gateway de tradução.

Para saber mais sobre os padrões de gateway IoT Edge, veja [como um dispositivo IoT Edge pode ser usado como porta de entrada](../../iot-edge/iot-edge-as-gateway.md).

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relações de dispositivo a jusante com uma porta de entrada e módulos

Os dispositivos a jusante podem ligar-se a um dispositivo de gateway IoT Edge através do módulo *hub IoT Edge.* Neste cenário, o dispositivo IoT Edge é um gateway transparente:

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="Diagrama de gateway transparente" border="false":::

Os dispositivos a jusante também podem ligar-se a um dispositivo de gateway IoT Edge através de um módulo personalizado. No cenário seguinte, os dispositivos a jusante conectam-se através de um módulo personalizado *Modbus.* Neste cenário, o dispositivo IoT Edge é um gateway de tradução:

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="Diagrama de conexão personalizada do módulo" border="false":::

O diagrama que se segue mostra ligações a um dispositivo de gateway IoT Edge através de ambos os tipos de módulos. Neste cenário, o dispositivo IoT Edge é simultaneamente um portal transparente e de tradução:

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="Diagrama de ligação utilizando ambos os módulos de ligação" border="false":::

Os dispositivos a jusante podem ligar-se a um dispositivo de gateway IoT Edge através de vários módulos personalizados. O diagrama a seguir mostra dispositivos a jusante que se conectam através de um módulo personalizado Modbus, um módulo personalizado BLE e o módulo *hub IoT Edge:*

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="Diagrama de ligação usando vários módulos personalizados" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>Dispositivos IoT Edge e IoT Central

Os dispositivos IoT Edge podem usar fichas *de assinatura de acesso partilhado* ou certificados X.509 para autenticar com a IoT Central. Pode registar manualmente os seus dispositivos IoT Edge na IoT Central antes de se ligarem pela primeira vez ou utilizar o Serviço de Provisionamento de Dispositivos para manusear o registo. Para saber mais, consulte [Get connected to Azure IoT Central](concepts-get-connected.md).

A IoT Central utiliza [modelos de dispositivos](concepts-device-templates.md) para definir como a IoT Central interage com um dispositivo. Por exemplo, um modelo de dispositivo especifica:

* Os tipos de telemetria e propriedades que um dispositivo envia para que o IoT Central possa interpretá-los e criar visualizações.
* O comando de um dispositivo responde para que o IoT Central possa exibir um UI para um operador usar para ligar para os comandos.

Um dispositivo IoT Edge pode enviar telemetria, sincronizar os valores de propriedade e responder aos comandos da mesma forma que um dispositivo padrão. Assim, um dispositivo IoT Edge precisa de um modelo de dispositivo na IoT Central.

### <a name="iot-edge-device-templates"></a>Modelos de dispositivo IoT Edge

Os modelos de dispositivos IoT Central utilizam modelos para descrever as capacidades dos dispositivos. O diagrama a seguir mostra a estrutura do modelo para um dispositivo IoT Edge:

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="Estrutura do modelo para dispositivo IoT Edge ligado à IoT Central" border="false":::

IoT Central modela um dispositivo IoT Edge da seguinte forma:

* Cada modelo de dispositivo IoT Edge tem um modelo de capacidade.
* Para cada módulo personalizado listado no manifesto de implementação, é gerado um modelo de capacidade do módulo.
* Estabelece-se uma relação entre cada modelo de capacidade do módulo e um modelo de dispositivo.
* Um modelo de capacidade de módulo implementa uma ou mais interfaces de módulos.
* Cada interface de módulo contém telemetria, propriedades e comandos.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>Manifestos de implantação IoT Edge e modelos de dispositivos IoT Central

No IoT Edge, implementa-se e gere a lógica de negócio sob a forma de módulos. Os módulos IoT Edge são a unidade de computação mais pequena gerida pela IoT Edge, e podem conter serviços Azure Stream, ou o seu próprio código específico para a solução.

Um *manifesto de implantação* IoT Edge lista os módulos IoT Edge para implantar no dispositivo e como configurá-los. Para saber mais, consulte [Saiba como implementar módulos e estabelecer rotas no IoT Edge.](../../iot-edge/module-composition.md)

No Azure IoT Central, importa um manifesto de implantação para criar um modelo de dispositivo para o dispositivo IoT Edge.

O seguinte corte de código mostra um manifesto de implantação de IoT Edge exemplo:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

No corte anterior, pode ver:

* Há três módulos. O *agente IoT Edge* e os módulos do sistema de hub *IoT Edge* que estão presentes em todos os manifestos de implantação. O módulo **Simulado DesteperatureSensor** personalizado.
* As imagens do módulo público são retiradas de um repositório de registo de contentores Azure que não requer credenciais para se ligar. Para imagens de módulos privados, defina as credenciais de registo do contentor para utilizar na `registryCredentials` definição para o módulo *de agente IoT Edge.*
* O módulo **Simulado DesteperatureSensor** tem duas propriedades `"SendData": true` e `"SendInterval": 10` .

Quando importa este manifesto de implantação numa aplicação IoT Central, gera o seguinte modelo de dispositivo:

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="Screenshot mostrando o modelo do dispositivo criado a partir do manifesto de implementação.":::

Na imagem anterior pode ver:

* Um módulo chamado **SimulatedTemperatureSensor**. Os *módulos do sistema IoT Edge* e do hub *IoT Edge* não aparecem no modelo.
* Uma interface chamada **gestão** que inclui duas propriedades writable chamadas **SendData** e **SendInterval**.

O manifesto de implantação não inclui informações sobre a telemetria que o módulo **SimulatedTemperatureSensor** envia ou os comandos a que responde. Adicione estas definições ao modelo do dispositivo manualmente antes de publicá-lo.

Para saber mais, consulte [Tutorial: Adicione um dispositivo Azure IoT Edge à sua aplicação Azure IoT Central](tutorial-add-edge-as-leaf-device.md).

### <a name="update-a-deployment-manifest"></a>Atualizar um manifesto de implantação

Se criar uma nova [versão](howto-version-device-template.md) do modelo do dispositivo, pode substituir o manifesto de implementação por uma nova versão:

Quando substituir o manifesto de implementação, quaisquer dispositivos IoT Edge ligados descarregam o novo manifesto e atualizam os seus módulos. No entanto, a IoT Central não atualiza as interfaces do modelo do dispositivo com quaisquer alterações na configuração do módulo. Por exemplo, se substituir o manifesto mostrado no corte anterior pelo seguinte manifesto, não vê automaticamente a propriedade **SendUnits** na interface **de gestão** no modelo do dispositivo. Adicione manualmente o novo imóvel à interface **de gestão** para a IoT Central reconhecê-lo:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>Implementar o tempo de execução IoT Edge

Para saber onde pode executar o tempo de execução IoT Edge, consulte [os sistemas suportados Azure IoT Edge](../../iot-edge/support.md).

Também pode instalar o tempo de execução IoT Edge nos seguintes ambientes:

* [Instale ou desinstale a borda Azure IoT para o Linux](../../iot-edge/how-to-install-iot-edge.md)
* [Instalar e aprovisionar o Azure IoT Edge para Linux num dispositivo Windows (Pré-visualização)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Executar Azure IoT Edge em Máquinas Virtuais Ubuntu em Azure](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>Dispositivos de gateway IoT Edge

Se selecionou um dispositivo IoT Edge para ser um dispositivo de gateway, pode adicionar relações a jusante aos modelos de dispositivos para dispositivos que pretende ligar ao dispositivo gateway.

Para saber mais, consulte [como ligar os dispositivos através de um gateway transparente IoT Edge](how-to-connect-iot-edge-transparent-gateway.md).

## <a name="next-steps"></a>Passos seguintes

Se você é um desenvolvedor de dispositivos, um próximo passo sugerido é aprender a [desenvolver os seus próprios módulos IoT Edge](../../iot-edge/module-development.md).
