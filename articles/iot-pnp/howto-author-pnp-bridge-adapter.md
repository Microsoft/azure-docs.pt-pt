---
title: Como construir um adaptador para a ponte IoT Plug and Play | Microsoft Docs
description: Identifique os componentes adaptadores de ficha ioT e reproduzir. Aprenda a estender a ponte escrevendo o seu próprio adaptador.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9a7028dfaeb94e87366de7acfa8cebc4c2f4c767
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98746833"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>Estender a ponte IoT Plug and Play
A [ponte IoT Plug and Play](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) permite-lhe ligar os dispositivos existentes ligados a uma porta de entrada para o seu hub IoT. Utiliza a ponte para mapear interfaces IoT Plug e Play para os dispositivos anexados. Uma interface IoT Plug and Play define a telemetria que um dispositivo envia, as propriedades sincronizadas entre o dispositivo e a nuvem, e os comandos a que o dispositivo responde. Pode instalar e configurar a aplicação de ponte de código aberto nos gateways Windows ou Linux. Além disso, a ponte pode ser executada como um módulo de tempo de execução Azure IoT Edge.

Este artigo explica em detalhe como:

- Estenda a ponte IoT Plug e Play com um adaptador.
- Implementar chamadas comuns para um adaptador de ponte.

Para um exemplo simples que mostra como usar a ponte, consulte [como ligar a amostra de ponte IoT Plug e Play que funciona no Linux ou Windows ao IoT Hub](howto-use-iot-pnp-bridge.md).

As orientações e amostras deste artigo assumem uma familiaridade básica com [as Gémeas Digitais Azure](../digital-twins/overview.md) e [ioT Plug and Play](overview-iot-plug-and-play.md). Além disso, este artigo assume a familiaridade com a forma de [construir e implantar a ponte IoT Plug and Play](howto-build-deploy-extend-pnp-bridge.md).

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>Guia de design para estender a ponte IoT Plug e Play com um adaptador

Para aumentar as capacidades da ponte, pode autoriar os seus próprios adaptadores de pontes.

A ponte utiliza adaptadores para:

- Estabeleça uma ligação entre um dispositivo e a nuvem.
- Ativar o fluxo de dados entre um dispositivo e a nuvem.
- Ativar a gestão do dispositivo a partir da nuvem.

Todos os adaptadores de ponte devem:

- Criar uma interface digital de gémeos.
- Utilize a interface para ligar a funcionalidade do lado do dispositivo a capacidades baseadas na nuvem, tais como telemetria, propriedades e comandos.
- Estabeleça comunicação de controlo e dados com o hardware ou firmware do dispositivo.

Cada adaptador de ponte interage com um tipo específico de dispositivo baseado na forma como o adaptador se conecta e interage com o dispositivo. Mesmo que a comunicação com um dispositivo utilize um protocolo de aperto de mão, um adaptador de ponte pode ter várias formas de interpretar os dados do dispositivo. Neste cenário, o adaptador de ponte utiliza informações para o adaptador no ficheiro de configuração para determinar a *configuração* da interface que o adaptador deve utilizar para analisar os dados.

Para interagir com o dispositivo, um adaptador de ponte utiliza um protocolo de comunicação suportado pelo dispositivo e APIs fornecido pelo sistema operativo subjacente, quer pelo fornecedor do dispositivo.

Para interagir com a nuvem, um adaptador de ponte utiliza APIs fornecidos pelo Dispositivo Azure IoT C SDK para enviar telemetria, criar interfaces gémeas digitais, enviar atualizações de propriedade e criar funções de retorno para atualizações e comandos de propriedade.

### <a name="create-a-bridge-adapter"></a>Criar um adaptador de ponte

A ponte espera que um adaptador de ponte implemente as APIs definidas na interface [_PNP_ADAPTER:](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296)

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

Nesta interface:

- `PNPBRIDGE_ADAPTER_CREATE` cria o adaptador e configura os recursos de gestão da interface. Um adaptador também pode contar com parâmetros globais do adaptador para a criação do adaptador. Esta função é chamada uma vez para um único adaptador.
- `PNPBRIDGE_COMPONENT_CREATE` cria as interfaces digitais de clientes gémeos e liga as funções de retorno. O adaptador inicia o canal de comunicação para o dispositivo. O adaptador pode configurar os recursos para ativar o fluxo de telemetria, mas não começa a reportar telemetria até `PNPBRIDGE_COMPONENT_START` ser chamada. Esta função é chamada uma vez para cada componente de interface no ficheiro de configuração.
- `PNPBRIDGE_COMPONENT_START` é chamado para deixar o adaptador de ponte começar a encaminhar a telemetria do dispositivo para o cliente digital twin. Esta função é chamada uma vez para cada componente de interface no ficheiro de configuração.
- `PNPBRIDGE_COMPONENT_STOP` para o fluxo de telemetria.
- `PNPBRIDGE_COMPONENT_DESTROY` destrói o cliente digital twin e os recursos de interface associados. Esta função é chamada uma vez para cada componente de interface no ficheiro de configuração quando a ponte é demolida ou quando ocorre um erro fatal.
- `PNPBRIDGE_ADAPTER_DESTROY` limpa os recursos do adaptador de ponte.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Interação do núcleo da ponte com adaptadores de ponte

A seguinte lista descreve o que acontece quando a ponte começa:

1. Quando a ponte começa, o gestor do adaptador de ponte olha através de cada componente de interface definido no ficheiro de configuração e chama `PNPBRIDGE_ADAPTER_CREATE` o adaptador apropriado. O adaptador pode utilizar parâmetros de configuração do adaptador global para configurar recursos para suportar as *várias configurações* de interface .
1. Para cada dispositivo no ficheiro de configuração, o gestor de ponte inicia a criação de interfaces chamando `PNPBRIDGE_COMPONENT_CREATE` o adaptador de ponte apropriado.
1. O adaptador recebe quaisquer configurações de configuração do adaptador opcional para o componente de interface e utiliza estas informações para configurar ligações ao dispositivo.
1. O adaptador cria as interfaces digitais do cliente gémeo e liga as funções de retorno para atualizações e comandos de propriedade. O estabelecimento de ligações ao dispositivo não deve bloquear o retorno das chamadas após a criação de interfaces gémeas digitais ter sucesso. A ligação ativa do dispositivo é independente do cliente de interface ativa que a ponte cria. Se uma ligação falhar, o adaptador assume que o dispositivo está inativo. O adaptador de ponte pode optar por voltar a tentar fazer esta ligação.
1. Após a manjedoura adaptadora de ponte criar todos os componentes de interface especificados no ficheiro de configuração, regista todas as interfaces com O Azure IoT Hub. A inscrição é uma chamada bloqueada e assíncronea. Quando a chamada termina, aciona uma chamada no adaptador de ponte que pode começar a manusear as chamadas de propriedade e comando da nuvem.
1. O gestor do adaptador de ponte chama então `PNPBRIDGE_INTERFACE_START` cada componente e o adaptador de ponte começa a reportar telemetria ao cliente digital twin.

### <a name="design-guidelines"></a>Orientações de design

Siga estas orientações quando desenvolver um novo adaptador de ponte:

- Determine quais as capacidades do dispositivo suportadas e qual é a definição de interface dos componentes que utilizam este adaptador.
- Determine quais a interface e os parâmetros globais que o adaptador necessita definidos no ficheiro de configuração.
- Identifique a comunicação do dispositivo de baixo nível necessária para suportar as propriedades e comandos dos componentes.
- Determinar como o adaptador deve analisar os dados brutos do dispositivo e convertê-los nos tipos de telemetria que a definição de interface IoT Plug e Play especifica.
- Implemente a interface do adaptador de ponte descrita anteriormente.
- Adicione o novo adaptador ao manifesto do adaptador e construa a ponte.

### <a name="enable-a-new-bridge-adapter"></a>Ativar um novo adaptador de ponte

Ativa os adaptadores na ponte adicionando uma referência em [adapter_manifest.c:](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c)

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> As chamadas do adaptador de ponte são invocadas sequencialmente. Um adaptador não deve bloquear uma chamada porque isto impede que o núcleo da ponte progrida.

### <a name="sample-camera-adapter"></a>Adaptador de câmera de amostra

O [adaptador de câmara](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) descreve um adaptador de câmara de amostra que pode ativar.

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>Exemplos de código para cenários/callbacks de adaptadores comuns

A secção seguinte fornecerá detalhes sobre como um adaptador para a ponte implementaria chamadas para uma série de cenários e utilizações comuns Esta secção abrange as seguintes chamadas:
- [Receba atualização de propriedade (nuvem para dispositivo)](#receive-property-update-cloud-to-device)
- [Reportar uma atualização de propriedade (dispositivo para nuvem)](#report-a-property-update-device-to-cloud)
- [Enviar telemetria (dispositivo para nuvem)](#send-telemetry-device-to-cloud)
- [Receba a chamada de atualização de comando a partir da nuvem e processe-a no lado do dispositivo (nuvem para dispositivo)](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [Responda à atualização do comando no lado do dispositivo (dispositivo a nuvem)](#respond-to-command-update-on-the-device-side-device-to-cloud)

Os exemplos abaixo baseiam-se no adaptador de [amostra de sensores ambientais](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor).

### <a name="receive-property-update-cloud-to-device"></a>Receba atualização de propriedade (nuvem para dispositivo)
O primeiro passo é registar uma função de retorno:

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
O próximo passo é implementar a função de retorno para ler a atualização da propriedade no dispositivo:

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>Reportar uma atualização de propriedade (dispositivo para nuvem)
Em qualquer ponto após a criação do seu componente, o seu dispositivo pode reportar propriedades à nuvem com estado: 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>Enviar telemetria (dispositivo para nuvem)
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>Receba a chamada de atualização de comando a partir da nuvem e processe-a no lado do dispositivo (nuvem para dispositivo)
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>Responda à atualização do comando no lado do dispositivo (dispositivo a nuvem)

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a ponte IoT Plug and Play, visite o repositório [IoT Plug e Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub.
