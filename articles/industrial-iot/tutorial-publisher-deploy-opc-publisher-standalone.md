---
title: Implementar o Microsoft OPC Publisher
description: Neste tutorial aprende-se a implementar o Editor OPC em modo autónomo.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: c82d15541459b5b482e427fc707b92755aa02c6c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787755"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>Tutorial: Implementar a Editora OPC

O OPC Publisher é um produto Microsoft totalmente suportado, desenvolvido a céu aberto, que faz a ponte entre os ativos industriais e a nuvem Microsoft Azure. Fá-lo ligando-o aos ativos ativados pela OPC ou ao software de conectividade industrial e publica dados de telemetria ao [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) em vários formatos, incluindo o formato padrão IEC62541 OPC UA PubSub (a partir da versão 2.6).

Funciona no [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) como módulo ou em simples Docker como um recipiente. Uma vez que aproveita o [tempo de funcionamento da plataforma cruzada .NET,](https://docs.microsoft.com/dotnet/core/introduction)também funciona de forma nativa no Linux e windows 10.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Implementar o Editor OPC
> * Execute a mais recente versão lançada da OPC Publisher como um recipiente
> * Especifique opções de criar recipientes no portal Azure

Se não tiver uma subscrição do Azure, crie uma conta de teste gratuita

## <a name="prerequisites"></a>Pré-requisitos

- Um Hub IoT deve ser criado
- Um dispositivo IoT Edge deve ser criado

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>Implementar o Editor OPC do Azure Marketplace

1. Escolha a subscrição Azure para usar. Se não houver subscrição do Azure disponível, deve ser criada uma.
2. Escolha o Hub IoT para o qual o Editor OPC deve enviar dados para. Se não houver IoT Hub disponível, deve ser criado um.
3. Escolha o dispositivo IoT Edge em que o Editor OPC deve funcionar (ou introduza um nome para um novo dispositivo IoT Edge a ser criado).
4. Clique em Criar. A página "Definir módulos no dispositivo" para o dispositivo IoT Edge selecionado abre.
5. Clique em "OPCPublisher" para abrir a página "Update IoT Edge Module" da Editora OPC e, em seguida, selecione "Opções de Criação de Recipientes".
6. Especifique opções adicionais de criação de recipiente com base na sua utilização do OPC Publisher, consulte a secção seguinte abaixo.


### <a name="accessing-the-microsoft-container-registry-docker-containers-for-opc-publisher-manually"></a>Aceder manualmente aos contentores do Microsoft Container Registry Docker para o OPC Publisher

A versão mais recente da OPC Publisher pode ser executada manualmente através de:

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

Onde "nome" é o nome do recipiente.

## <a name="specifying-container-create-options-in-the-azure-portal"></a>Especificar o contentor criar opções no portal Azure
Ao implementar o OPC Publisher através do portal Azure, as opções de criação de contentores podem ser especificadas na página do Módulo de Borda IoT de Atualização da OPC Publisher. Estas opções de criação devem estar no formato JSON. Os argumentos da linha de comando da OPC Publisher podem ser especificados através da tecla Cmd, por exemplo:
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

Um conjunto típico de recipiente de módulo ioT edge criar opções para editor OPC é:
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

Com estas opções especificadas, a OPC Publisher irá ler o ficheiro de configuração `./pn.json` . O diretório de trabalho da OPC Publisher está definido para `/appdata` o arranque e, assim, a OPC Publisher irá ler o ficheiro dentro do `/appdata/pn.json` seu contentor Docker. O ficheiro de registo da OPC Publisher será escrito `/appdata` e o `CertificateStores` diretório (utilizado para certificados OPC UA) também será criado neste diretório. Para disponibilizar estes ficheiros no sistema de ficheiros do anfitrião IoT Edge, a configuração do recipiente requer um volume de montagem de encaixe. O `/iiotedge:/appdata` vinculação irá mapear o diretório `/appdata` para o diretório de `/iiotedge` anfitriões (que será criado pelo tempo de execução IoT Edge se não existir).
**Sem este volume de montagem de ligação, todos os ficheiros de configuração do OPC Publisher serão perdidos quando o recipiente for reiniciado.**

Uma ligação a um servidor OPC UA utilizando o seu nome de anfitrião sem um servidor DNS configurado na rede pode ser conseguida adicionando uma `ExtraHosts` entrada na `HostConfig` secção:

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>Passos seguintes 
Agora que implementou o módulo OPC Publisher Edge, o próximo passo é configurá-lo:

> [!div class="nextstepaction"]
> [Configure a Editora OPC](tutorial-publisher-configure-opc-publisher.md)