---
title: Escreva criações Options for modules - Azure IoT Edge [ Microsoft Docs
description: Como utilizar as criaçõesOp no manifesto de implementação para configurar módulos em tempo de execução
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550347"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Como configurar o recipiente criar opções para módulos IoT Edge

O parâmetro **createOptions** no manifesto de implantação permite-lhe configurar os recipientes do módulo em tempo de execução. Este parâmetro expande o seu controlo sobre os módulos e permite tarefas como permitir ou restringir o acesso do módulo aos recursos do dispositivo anfitrião ou configurar a rede.

Os módulos IoT Edge são implementados como recipientes compatíveis com o Docker no seu dispositivo IoT Edge. Docker oferece muitas opções para criar recipientes, e essas opções aplicam-se também aos módulos IoT Edge. Para mais informações, consulte o [contentor Docker criar opções.](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)

## <a name="format-create-options"></a>Formato criar opções

O manifesto de implantação ioT Edge aceita criar opções formatadas como JSON. Por exemplo, tome as opções de criação que são automaticamente incluídas para cada módulo edgeHub:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

Este exemplo edgeHub utiliza o parâmetro **HostConfig.PortBindings** para mapear portas expostas no recipiente para uma porta no dispositivo hospedeiro.

Se utilizar as extensões Azure IoT Tools para Visual Studio ou Visual Studio Code, pode escrever as opções de criação em formato JSON no ficheiro **deployment.template.json.** Em seguida, quando utilizar a extensão para construir a solução IoT Edge ou gerar o manifesto de implantação, irá corrigir o JSON para si no formato que o tempo de funcionamento do IoT Edge espera. Por exemplo:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Uma dica para escrever criar `docker inspect` opções é usar o comando. Como parte do seu processo de `docker run <container name>`desenvolvimento, execute o módulo localmente utilizando . Assim que tiver o módulo a funcionar `docker inspect <container name>`da forma que quiser, corra. Este comando produz os detalhes do módulo no formato JSON. Encontre os parâmetros que configurado e copie o JSON. Por exemplo:

[![Resultados do docker](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png) inspecionar edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Cenários comuns

O contentor cria opções que permitem muitos cenários, mas aqui estão alguns que surgem mais frequentemente na construção de soluções IoT Edge:

* [Dar aos módulos acesso ao armazenamento do hospedeiro](how-to-access-host-storage-from-module.md)
* [Mapa porta anfitrião para porta de módulo](#map-host-port-to-module-port)
* [Restringir a memória do módulo e a utilização do CPU](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Mapa porta anfitrião para porta de módulo

Se o seu módulo precisa de comunicar com um serviço fora da solução IoT Edge, e não estiver a usar o encaminhamento de mensagens para o fazer, então precisa de mapear uma porta hospedeira para uma porta de módulos.

>[!TIP]
>Este mapeamento por porta não é necessário para a comunicação módulo-módulo no mesmo dispositivo. Se o módulo A precisar de consultar uma API alojada no módulo B, pode fazê-lo sem qualquer mapeamento portuário. O módulo B tem de expor uma porta `EXPOSE 8080`no seu estivador, por exemplo: . Em seguida, o módulo A pode consultar a API `http://ModuleB:8080/api`utilizando o nome do módulo B, por exemplo: .

Em primeiro lugar, certifique-se de que uma porta dentro do módulo está exposta para ouvir ligações. Pode fazê-lo com uma instrução [DE EXNo](https://docs.docker.com/engine/reference/builder/#expose) no ficheiro de estivador. Por exemplo, `EXPOSE 8080`. A instrução de exposição não se aplica ao protocolo TCP se não for especificada, ou pode especificar a UDP.

Em seguida, utilize a definição **portBindings** no grupo **HostConfig** do [recipiente Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) para mapear a porta exposta no módulo para uma porta no dispositivo hospedeiro. Por exemplo, se expôs a porta 8080 dentro do módulo e quiser mapear isso para a porta 80 do dispositivo hospedeiro, as opções de criação no ficheiro modelo.json pareceriam o seguinte exemplo:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Uma vez stringificada para o manifesto de implantação, a mesma configuração seria como o seguinte exemplo:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Restringir a memória do módulo e a utilização do CPU

Pode declarar quanto dos recursos hospedeiros um módulo pode usar. Este controlo é útil para garantir que um módulo não pode consumir muita memória ou utilização de CPU e evitar que outros processos decorrem no dispositivo. Pode gerir estas definições com o [recipiente Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) no grupo **HostConfig,** incluindo:

* **Memória**: Limite de memória em bytes. Por exemplo, 268435456 bytes = 256 MB.
* **MemorySwap**: Limite total de memória (memória + troca). Por exemplo, 536870912 bytes = 512 MB
* **Período do CpuPeriod**: O comprimento de um período de CPU em microsegundos. O valor por defeito é de 100000, pelo que, por exemplo, um valor de 25000 limita um contentor a 25% dos recursos da CPU.

No formato template.json, estes valores seriam como o seguinte exemplo:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Uma vez stringificados para o manifesto de implantação final, estes valores seriam como o seguinte exemplo:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Passos seguintes

Para mais exemplos de criação de opções em ação, consulte as seguintes amostras ioT Edge:

* [Visão Personalizada e Borda Azure IoT em um Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Amostra de armazenamento de bolhas Azure IoT Edge](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
