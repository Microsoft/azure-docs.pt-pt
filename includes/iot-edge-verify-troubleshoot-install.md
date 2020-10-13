---
title: Após a instalação e o provisionamento, verifique o sucesso e a resolução de problemas
description: ficheiro de inclusão
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979723"
---
## <a name="verify-successful-setup"></a>Verificar a configuração bem sucedida

Verifique o estado do serviço IoT Edge. Deve estar listado como em execução.  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

Examine os registos de serviço.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

Se acabou de instalar o tempo de execução IoT Edge, poderá ver uma lista de erros do tempo entre a execução **de Deploy-IoTEdge** e **Initialize-IoTEdge**. Estes erros são esperados, uma vez que o serviço está a tentar começar antes de ser configurado.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

Executar a [ferramenta de resolução de problemas](../articles/iot-edge/troubleshoot.md#run-the-check-command) para verificar se há erros de configuração e rede mais comuns.

```powershell
iotedge check
```

Até que implante o seu primeiro módulo para IoT Edge no seu dispositivo, o módulo de sistema **$edgeHub** não será implantado no dispositivo. Como resultado, a verificação automatizada devolverá um erro para a `Edge Hub can bind to ports on host` verificação de conectividade. Este erro pode ser ignorado a menos que ocorra depois de ter implantado um módulo no dispositivo.

Finalmente, listar os módulos de execução:

```powershell
iotedge list
```

Após uma nova instalação, o único módulo que deve ver em execução é **edgeAgent**.

## <a name="tips-and-troubleshooting"></a>Sugestões e resolução de problemas

Nos dispositivos com restrições de recursos, recomenda-se vivamente que desempeciu a variável do ambiente *OptimizeForPerformance* para *falso,* de acordo com as instruções do [guia de resolução de problemas](../articles/iot-edge/troubleshoot.md).

Se o seu dispositivo não conseguir ligar-se ao IoT Hub e a sua rede tiver um servidor proxy, siga os passos no [Configure o seu dispositivo IoT Edge para comunicar através de um servidor proxy](../articles/iot-edge/how-to-configure-proxy-support.md).

# <a name="linux"></a>[Linux](#tab/linux)

Nos dispositivos Linux, precisa de privilégios elevados para executar `iotedge` comandos. Depois de instalar o tempo de funcionamento, inscreva-se na sua máquina e volte a entrar para atualizar automaticamente as suas permissões. Até lá, use `sudo` para executar comandos com privilégios elevados.

# <a name="windows"></a>[Windows](#tab/windows)

Nos dispositivos Windows que executam contentores windows, o motor de contentores Moby foi instalado como parte do IoT Edge. O motor Moby foi projetado para funcionar em paralelo com Docker Desktop. Pode utilizar `docker` comandos se quiser interagir diretamente com os recipientes do seu dispositivo. No entanto, deve especificamente direcionar o motor Moby para o caso de Docker Desktop também estar instalado no dispositivo.

Por exemplo, para listar todas as imagens do Docker, utilize o seguinte comando:

```powershell
docker images
```

Para listar todas as imagens moby, modifique o mesmo comando com um ponteiro para o motor Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

O motor URI está listado na saída do script de instalação, ou pode encontrá-lo na secção de definições de tempo de funcionamento do contentor para o ficheiro config.yaml.

![moby_runtime uri em config.yaml](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
