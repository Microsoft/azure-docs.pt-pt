---
title: Escrever createOptions para módulos - Azure IoT Edge / Microsoft Docs
description: Como utilizar criaÇões no manifesto de implantação para configurar módulos em tempo de execução
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80550347"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Como configurar recipientes criar opções para módulos IoT Edge

O parâmetro **createOptions** no manifesto de implantação permite-lhe configurar os recipientes do módulo em tempo de execução. Este parâmetro expande o seu controlo sobre os módulos e permite tarefas como permitir ou restringir o acesso do módulo aos recursos do dispositivo anfitrião, ou configurar a rede.

Os módulos IoT Edge são implementados como recipientes compatíveis com o Docker no seu dispositivo IoT Edge. Docker oferece muitas opções para criar recipientes, e essas opções aplicam-se também aos módulos IoT Edge. Para obter mais informações, consulte [o recipiente Docker para criar opções.](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)

## <a name="format-create-options"></a>Formato criar opções

O manifesto de implementação IoT Edge aceita criar opções formatadas como JSON. Por exemplo, tome as opções de criação que são automaticamente incluídas para cada módulo edgeHub:

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

Este exemplo do EdgeHub utiliza o parâmetro **HostConfig.PortBindings** para mapear portas expostas no contentor para uma porta no dispositivo anfitrião.

Se utilizar as extensões Azure IoT Tools para Visual Studio ou Visual Studio Code, pode escrever as opções de criação no formato JSON no **deployment.template.jsno** ficheiro. Em seguida, quando utilizar a extensão para construir a solução IoT Edge ou gerar o manifesto de implantação, irá colocar o JSON no formato que o tempo de execução IoT Edge espera. Por exemplo:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Uma dica para escrever criar opções é usar o `docker inspect` comando. Como parte do seu processo de desenvolvimento, execute o módulo localmente `docker run <container name>` utilizando. Assim que tiver o módulo a funcionar da forma que quiser, `docker inspect <container name>` corra. Este comando produz os detalhes do módulo no formato JSON. Encontre os parâmetros que configuraram e copie o JSON. Por exemplo:

[![Resultados do estivador inspecionar edgeHub ](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Cenários comuns

As opções de criação de contentores permitem muitos cenários, mas aqui estão alguns que surgem com mais frequência na construção de soluções IoT Edge:

* [Dar aos módulos acesso ao armazenamento do anfitrião](how-to-access-host-storage-from-module.md)
* [Porta hospedeira de mapa para porta de módulo](#map-host-port-to-module-port)
* [Restringir a memória do módulo e o uso do CPU](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Porta hospedeira de mapa para porta de módulo

Se o seu módulo precisar de comunicar com um serviço fora da solução IoT Edge, e não estiver a utilizar o encaminhamento de mensagens para o fazer, então precisa de mapear uma porta hospedeira para uma porta de módulos.

>[!TIP]
>Este mapeamento de porta não é necessário para a comunicação módulo-módulo no mesmo dispositivo. Se o módulo A precisar de consultar um API alojado no módulo B, pode fazê-lo sem qualquer mapeamento de porta. O módulo B precisa de expor uma porta no seu estivador, por exemplo: `EXPOSE 8080` . Em seguida, o módulo A pode consultar a API usando o nome do módulo B, por exemplo: `http://ModuleB:8080/api` .

Em primeiro lugar, certifique-se de que uma porta no interior do módulo está exposta para ouvir as ligações. Pode fazê-lo utilizando uma instrução [EXPOR](https://docs.docker.com/engine/reference/builder/#expose) no ficheiro de estiva. Por exemplo, `EXPOSE 8080`. A instrução de exposição predefini no protocolo TCP se não for especificada, ou pode especificar UDP.

Em seguida, utilize a definição **portBindings** no grupo **HostConfig** do [recipiente Docker para criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) para mapear a porta exposta no módulo para uma porta no dispositivo anfitrião. Por exemplo, se expôs a porta 8080 dentro do módulo e quer mapear para a porta 80 do dispositivo anfitrião, as opções de criação no template.jsno ficheiro seriam o seguinte exemplo:

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

Uma vez stringified para o manifesto de implantação, a mesma configuração seria como o seguinte exemplo:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Restringir a memória do módulo e o uso do CPU

Pode declarar quanto dos recursos hospedeiros um módulo pode usar. Este controlo é útil para garantir que um módulo não pode consumir muita memória ou uso de CPU e impedir que outros processos sejam funcionando no dispositivo. Pode gerir estas definições com [o recipiente Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) no grupo **HostConfig,** incluindo:

* **Memória**: Limite de memória nos bytes. Por exemplo, 268435456 bytes = 256 MB.
* **MemorySwap**: Limite total de memória (memória + troca). Por exemplo, 536870912 bytes = 512 MB
* **CpuPeriod**: Duração de um período de CPU em microsegundos. O valor padrão é de 1000000, pelo que, por exemplo, um valor de 25000 limita um contentor a 25% dos recursos da CPU.

No template.jsno formato, estes valores seriam como o seguinte exemplo:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Uma vez stringificado para o manifesto de implantação final, estes valores seriam como o seguinte exemplo:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais exemplos de criar opções em ação, consulte as seguintes amostras IoT Edge:

* [Visão Personalizada e Borda IoT Azure em um Pi 3 de framboesa](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Amostra de armazenamento de bolhas Azure IoT Edge](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
