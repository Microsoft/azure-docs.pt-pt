---
title: Ligações de dispositivos de resolução de problemas à Azure IoT Central Microsoft Docs
description: Resolução de problemas por que não está a ver dados dos seus dispositivos na IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.openlocfilehash: 34a9350f830171a137ca3a63ecae2203edec92b2
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92127374"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Resolver problemas relacionados com a não apresentação dos dados dos seus dispositivos no Azure IoT Central

Este documento ajuda os desenvolvedores de dispositivos a descobrir por que os dados que os seus dispositivos estão a enviar para a IoT Central podem não estar a aparecer na aplicação.

Há duas áreas principais para investigar:

- Problemas de conectividade do dispositivo
  - Problemas de autenticação como o dispositivo têm credenciais inválidas
  - Problemas de conectividade de rede
  - O dispositivo não é aprovado, ou bloqueado
- Problemas de forma de carga útil do dispositivo

Este guia de resolução de problemas foca-se em problemas de conectividade do dispositivo e problemas de forma de carga útil do dispositivo.

## <a name="device-connectivity-issues"></a>Problemas de conectividade do dispositivo

Esta secção ajuda-o a determinar se os seus dados estão a chegar à IoT Central.

Se ainda não o fez, instale a ferramenta e a `az cli` `azure-iot` extensão.

Para aprender a instalar `az cli` o , consulte instalar o [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Para [instalar](/cli/azure/azure-cli-reference-for-IoT?view=azure-cli-latest#extension-reference-installation) a `azure-iot` extensão, executar o seguinte comando:

```cmd/bash
az extension add --name azure-iot
```

> [!NOTE]
> Pode ser solicitado a instalar a `uamqp` biblioteca na primeira vez que executar um comando de extensão.

Quando instalar a `azure-iot` extensão, inicie o seu dispositivo para ver se as mensagens que está a enviar estão a caminho da IoT Central.

Utilize os seguintes comandos para iniciar sê-lo na subscrição onde tem a sua aplicação IoT Central:

```cmd/bash
az login
az set account --subscription <your-subscription-id>
```

Para monitorizar a telemetria que o seu dispositivo está a enviar, utilize o seguinte comando:

```cmd/bash
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Se o dispositivo tiver sido ligado com sucesso à IoT Central, vê saída semelhante à seguinte:

```cmd/bash
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

Para monitorizar as atualizações da propriedade que o seu dispositivo está a trocar com a IoT Central, utilize o seguinte comando de pré-visualização:

```cmd/bash
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Se o dispositivo enviar com sucesso atualizações de propriedade, vê uma saída semelhante à seguinte:

```cmd/bash
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Se vir que os dados aparecem no seu terminal, então os dados estão a chegar à sua aplicação IoT Central.

Se não vir nenhum dado aparecer após alguns minutos, tente premir a `Enter` ou a chave no `return` teclado, caso a saída esteja presa.

Se ainda não está a ver nenhum dado aparecer no seu terminal, é provável que o seu dispositivo esteja com problemas de conectividade de rede, ou não esteja a enviar dados corretamente para a IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>Verifique o estado de provisionamento do seu dispositivo

Se os seus dados não estiverem a aparecer no monitor, verifique o estado de provisionamento do seu dispositivo executando o seguinte comando:

```cmd/bash
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

A seguinte saída mostra um exemplo de um dispositivo que está bloqueado de ligação:

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| Estado de provisionamento do dispositivo | Descrição | Possível mitigação |
| - | - | - |
| Aprovisionado | Não há problema imediatamente reconhecível. | N/D |
| Registado | O dispositivo ainda não está ligado à IoT Central. | Verifique se os registos do seu dispositivo têm problemas de conectividade. |
| Bloqueado | O dispositivo está bloqueado da ligação à IoT Central. | O dispositivo está bloqueado da ligação à aplicação IoT Central. Desbloqueie o dispositivo na IoT Central e recomprina. Para saber mais, consulte [dispositivos Block](concepts-get-connected.md#device-status-values). |
| Não aprovado | O dispositivo não está aprovado. | O dispositivo não está aprovado para ligar à aplicação IoT Central. Aprove o dispositivo na IoT Central e redaça. Para saber mais, consulte [Dispositivos Aprovados](concepts-get-connected.md#connect-without-registering-devices) |
| Não associada | O dispositivo não está associado a um modelo de dispositivo. | Associe o dispositivo a um modelo de dispositivo para que o IoT Central saiba analisar os dados. |

Saiba mais sobre [os códigos de estado do dispositivo](concepts-get-connected.md#device-status-values).

### <a name="error-codes"></a>Códigos de erro

Se ainda não conseguir diagnosticar por que razão os seus dados não aparecem `monitor-events` , o próximo passo é procurar códigos de erro reportados pelo seu dispositivo.

Inicie uma sessão de depurar o seu dispositivo ou recolha registos do seu dispositivo. Verifique se há códigos de erro que o dispositivo reporte.

As tabelas que se seguem mostram os códigos de erro comuns e as possíveis ações para mitigar.

Se estiver a ver questões relacionadas com o seu fluxo de autenticação:

| Código de erro | Descrição | Possível Mitigação |
| - | - | - |
| 400 | O corpo do pedido não é válido. Por exemplo, não pode ser analisado, ou o objeto não pode ser validado. | Certifique-se de que está a enviar o corpo de pedido correto como parte do fluxo de atestado, ou utilize um dispositivo SDK. |
| 401 | O sinal de autorização não pode ser validado. Por exemplo, expirou ou não se aplica ao URI do pedido. Este código de erro também é devolvido aos dispositivos como parte do fluxo de atestado TPM. | Certifique-se de que o seu dispositivo tem as credenciais corretas. |
| 404 | A instância do Serviço de Provisionamento de Dispositivos, ou um recurso como uma inscrição, não existe. | [Arquive um bilhete com apoio ao cliente.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) |
| 412 | O `ETag` pedido não corresponde ao recurso `ETag` existente, de acordo com o RFC7232. | [Arquive um bilhete com apoio ao cliente.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) |
| 429 | As operações estão a ser estranguladas pelo serviço. Para limites de serviço específicos, consulte [os limites do Serviço de Provisionamento de Dispositivos IoT Hub](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | Reduza a frequência da mensagem, divida responsabilidades entre mais dispositivos. |
| 500 | Ocorreu um erro interno. | [Preencha um bilhete com o apoio](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ao cliente para ver se eles podem ajudá-lo ainda mais. |

## <a name="payload-shape-issues"></a>Problemas de forma de carga útil

Quando tiver estabelecido que o seu dispositivo está a enviar dados para a IoT Central, o próximo passo é garantir que o seu dispositivo está a enviar dados num formato válido.

Existem duas categorias principais de questões comuns que fazem com que os dados do dispositivo não apareçam na IoT Central:

- Modelo do dispositivo para desajuste de dados do dispositivo:
  - Desfasamento no nome, como tipografias ou problemas de correspondência de casos.
  - Propriedades não modelos onde o esquema não é definido no modelo do dispositivo.
  - O desfasamento de Schema, tal como um tipo definido no modelo como `boolean` , mas os dados são uma cadeia.
  - O mesmo nome de telemetria é definido em várias interfaces, mas o dispositivo não é compatível com IoT Plug e Play.
- A forma de dados é inválida JSON. Para saber mais, consulte [telemetria, propriedade e cargas de comando.](concepts-telemetry-properties-commands.md)

Para detetar em que categorias está o seu problema, execute o comando mais adequado para o seu cenário:

- Para validar a telemetria, utilize o comando de pré-visualização:

    ```cmd/bash
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Para validar atualizações de propriedade, utilize o comando de pré-visualização

    ```cmd/bash
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

Pode ser solicitado a instalar a `uamqp` biblioteca na primeira vez que executar um `validate` comando.

A seguinte saída mostra exemplo de erro e mensagens de aviso do comando validado:

```cmd/bash
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Se preferir usar um GUI, use a vista de **dados** IoT Central Raw para ver se algo não está a ser modelado. A visão **de dados raw** não deteta se o dispositivo está a enviar JSON mal formado.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Screenshot da vista de dados brutos":::

Quando tiver detetado o problema, poderá necessitar de atualizar o firmware do dispositivo ou criar um novo modelo de dispositivo que modela dados previamente não modelados.

Se escolheu criar um novo modelo que modele os dados corretamente, migrar dispositivos do seu modelo antigo para o novo modelo. Para saber mais, consulte [Gerir os dispositivos na sua aplicação Azure IoT Central](howto-manage-devices.md).

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode arquivar um [bilhete de apoio Azure.](https://portal.azure.com/#create/Microsoft.Support)

Para mais informações, consulte o [suporte do Azure IoT e opções de ajuda.](../../iot-fundamentals/iot-support-help.md)