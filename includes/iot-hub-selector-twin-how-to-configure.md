> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Introdução

No [começar a utilizar dispositivos duplos do IoT Hub][lnk-twin-tutorial], aprendeu a definir através de metadados do dispositivo *etiquetas*. Recebido condições de dispositivo de uma aplicação de dispositivo utilizando *comunicadas propriedades*e, em seguida, consultar esta informação utilizando uma linguagem como o SQL Server.

Este tutorial descreve como utilizar o dispositivo duplo *pretendido propriedades* e *comunicadas propriedades* para configurar remotamente a aplicações de dispositivos. Reportado e propriedades pretendidas num dispositivo duplo ative uma configuração de vários passo de uma aplicação de dispositivo e fornecem a visibilidade do estado desta operação em todos os dispositivos. Pode encontrar mais informações sobre a função de configurações de dispositivo [descrição geral da gestão de dispositivos do IoT hub][lnk-dm-overview].

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Um nível elevado, utilizar dispositivos duplos permite que a solução de back-end especificar a configuração pretendida para os dispositivos geridos, em vez de enviar comandos específicos. O dispositivo é responsável pela como configurar a melhor forma de atualizar a configuração da sua (importante nos cenários de IoT onde condições de dispositivo específico afetam a capacidade de realizar imediatamente comandos específicos), ao reporting continuamente o estado atual e potenciais condições de erro do processo de atualização. Este padrão é instrumental para a gestão de grandes conjuntos de dispositivos, como proporciona a visibilidade total de back-end de solução do Estado do processo de configuração em todos os dispositivos.

> [!TIP]
> Em cenários em que os dispositivos são controlados de forma interativa mais (por exemplo, ativar uma ventoinha a partir de uma aplicação controlados pelo utilizador), considere a utilização de [direcionar métodos][lnk-methods].

Neste tutorial, o solução de back-end altera a configuração de telemetria do dispositivo-alvo para que a aplicação de dispositivo aplica-se uma atualização da configuração. Por exemplo, uma atualização da configuração seria possível que requerem um reinício de módulo de software, o qual este tutorial simula com um atraso simple.

O solução de back-end armazena a configuração nas propriedades do dispositivo duplo pretendido da seguinte forma:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

Uma vez que as configurações podem ser objetos complexos, são atribuídas os IDs exclusivos (hashes ou [GUIDs][lnk-guid]).


A aplicação de dispositivo relatórios configuração atual espelhamento a propriedade pretendida **telemetryConfig** nas propriedades de comunicado:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Tenha em atenção como os que relatados **telemetryConfig** tem uma propriedade adicional **estado**, utilizada para comunicar o estado do processo de atualização de configuração.

Quando uma nova configuração desejada é recebida, a aplicação de dispositivo relatórios uma configuração pendente alterando o estado:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "configId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Em seguida, em algum momento posterior, a aplicação de dispositivo relatórios o êxito ou falha desta operação atualizando a propriedade. O solução de back-end pode consultar o estado do processo de configuração em todos os dispositivos em qualquer altura.

Este tutorial mostrar-lhe como:

* Criar uma aplicação de dispositivo simulado que recebe atualizações de configuração de back-end da solução e relatórios de várias atualizações como *comunicadas propriedades* na configuração de processo de atualização.
* Crie uma aplicação de back-end que atualiza a configuração pretendida de um dispositivo e, em seguida, consulta o processo de atualização da configuração.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
