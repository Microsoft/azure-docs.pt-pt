---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 92ccb6127e624ace9e719ffd23324b3a1b971f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89272138"
---
Num dispositivo Azure Stack Edge que tem a função de computação configurada, pode resolver problemas ou monitorizar o dispositivo utilizando dois conjuntos diferentes de comandos.

- Usando `iotedge` comandos. Estes comandos estão disponíveis para operações básicas para o seu dispositivo.
- Usando `dkrdbe` comandos. Estes comandos estão disponíveis para um vasto conjunto de operações para o seu dispositivo.

Para executar qualquer um dos comandos acima, é necessário ligar à [interface PowerShell](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>Use `iotedge` comandos

Para ver uma lista de comandos disponíveis, [ligue-se à interface PowerShell](#connect-to-the-powershell-interface) e utilize a `iotedge` função.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                                                                                                 Usage: iotedge COMMAND

Commands:
   check
   list
   logs
   restart

[10.100.10.10]: PS>
```

A tabela a seguir tem uma breve descrição dos comandos disponíveis `iotedge` para:

|command  |Description |
|---------|---------|
|`check`     | Realizar verificações automatizadas para problemas comuns de configuração e conectividade       |
|`list`     | Listar módulos         |
|`logs`     | Pegue os troncos de um módulo        |
|`restart`     | Parar e reiniciar um módulo         |


### <a name="use-dkrdbe-commands"></a>Use `dkrdbe` comandos

Para ver uma lista de comandos disponíveis, [ligue-se à interface PowerShell](#connect-to-the-powershell-interface) e utilize a `dkrdbe` função.

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
A tabela a seguir tem uma breve descrição dos comandos disponíveis `dkrdbe` para:

|command  |Description |
|---------|---------|
|`image`     | Gerir imagens. Para remover imagens não utilizadas, utilize: `dkrdbe image prune -a -f`       |
|`images`     | Listar imagens         |
|`inspect`     | Devolva informações de baixo nível em objetos Docker         |
|`login`     | Inscreva-se num registo do Docker         |
|`logout`     | Assine um registo do Docker         |
|`logs`     | Pegue os troncos de um recipiente        |
|`port`     | Lista mapeamentos portuários ou um mapeamento específico para o recipiente        |
|`ps`     | Listar contentores        |
|`pull`     | Puxe uma imagem ou um repositório de um registo         |
|`start`     | Inicie um ou mais contentores parados         |
|`stats`     | Apresentar uma transmissão ao vivo de estatísticas de utilização de recursos de contentores         |
|`stop`     | Parar um ou mais contentores de corrida        |
|`system`     | Gerir Docker         |
|`top`     | Mostrar os processos de funcionamento de um recipiente         |

Para obter ajuda para qualquer comando disponível, use `dkrdbe <command-name> --help` .

Por exemplo, para compreender o uso do `port` comando, escreva:

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

Os comandos disponíveis para a `dkrdbe` função utilizam os mesmos parâmetros utilizados para os comandos normais do estivador. Para as opções e parâmetros utilizados com o comando do estivador, vá ao [Utilizar o comando Docker](https://docs.docker.com/engine/reference/commandline/docker/).

### <a name="to-check-if-the-module-deployed-successfully"></a>Para verificar se o módulo foi implantado com sucesso

Os módulos computacional são contentores que têm uma lógica de negócio implementada. Para verificar se um módulo de computação é implantado com sucesso, verifique `ps` se o recipiente (correspondente ao módulo de computação) está em funcionamento.

Para obter a lista de todos os recipientes (incluindo os que são pausados), executar o `ps -a` comando.

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

Se houve um erro na criação da imagem do recipiente ou ao puxar a imagem, corra `logs edgeAgent` .  `EdgeAgent` é o recipiente de tempo de execução IoT Edge responsável pelo provisionamento de outros contentores.

Porque `logs edgeAgent` despeja todos os registos, uma boa maneira de ver os erros recentes é usar a opção. `--tail 20`


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>Para obter troncos de contentores

Para obter registos para um recipiente específico, primeiro liste o recipiente e, em seguida, pegue os registos para o recipiente que você está interessado.

1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Para obter a lista de contentores em execução, executar o `ps` comando.

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. Tome nota da identificação do recipiente para o recipiente para o que necessita dos registos.

4. Para obter os registos de um recipiente específico, executar o `logs` comando que fornece a identificação do contentor.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>Para monitorizar as estatísticas de utilização do dispositivo

Para monitorizar a memória, utilização do CPU e IO no dispositivo, utilize o `stats` comando.

1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Executar o `stats` comando de modo a desativar o fluxo de transmissão e puxar apenas o primeiro resultado.

   ```powershell
   dkrdbe stats --no-stream
   ```

   O exemplo a seguir mostra a utilização deste cmdlet:

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

