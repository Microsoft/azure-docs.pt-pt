---
title: Prontidão de produção e boas práticas - Azure
description: Este artigo fornece orientações sobre como configurar e implementar o módulo Live Video Analytics em ambientes de produção.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c34e05e184cfa6f0933701a76177fae3eed70c0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87071944"
---
# <a name="production-readiness-and-best-practices"></a>Prontidão de produção e boas práticas

Este artigo fornece orientações sobre como configurar e implementar o módulo Live Video Analytics em ambientes de produção. Também deve rever [Prepare-se para implementar a sua solução IoT Edge em](../../iot-edge/production-checklist.md) artigo de produção sobre a preparação da sua solução IoT Edge. 

> [!NOTE]
> Deve consultar os departamentos de TI das suas organizações sobre aspetos relacionados com a segurança.

## <a name="running-the-module-as-a-local-user"></a>Executar o módulo como um utilizador local

Quando implementa o Live Video Analytics no módulo IoT Edge para um dispositivo de borda, por padrão funciona com privilégios elevados. Quando o fizer, se verificar os registos do módulo ( `sudo iotedge logs {name-of-module}` ), verá o seguinte:

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

As secções abaixo discutem como pode abordar o aviso acima.

### <a name="creating-and-using-a-local-user-account"></a>Criar e utilizar uma conta de utilizador local

Pode e deve executar o módulo Live Video Analytics no IoT Edge em produção usando uma conta com o mínimo de privilégios possível. Os seguintes comandos, por exemplo, mostram como pode criar uma conta de utilizador local num Linux VM:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

Em seguida, no manifesto de implantação, pode definir as variáveis de ambiente LOCAL_USER_ID e LOCAL_GROUP_ID para aquele utilizador e grupo não-raiz:

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>Concessão de permissões ao armazenamento de dispositivos

O módulo Live Video Analytics no IoT Edge requer a capacidade de escrever ficheiros para o sistema de ficheiros local quando:

* Utilizando uma propriedade twin módulo [[applicationDataDirectory],](module-twin-configuration-schema.md#module-twin-properties)onde deve especificar um diretório no sistema de ficheiros local para armazenar dados de configuração.
* Utilizando um gráfico de mídia para gravar vídeo na nuvem, o módulo requer a utilização de um diretório no dispositivo de borda como cache (ver artigo [de gravação contínua](continuous-video-recording-concept.md) de vídeo para obter mais informações).
* [Gravação num ficheiro local](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources), onde deve especificar um caminho de arquivo para o vídeo gravado.

Se pretender utilizar qualquer um dos acima, deve garantir que a conta de utilizador acima tem acesso ao diretório relevante. Considere a aplicaçãoDataDirectory, por exemplo. Pode criar um diretório no dispositivo de borda e ligar o armazenamento do dispositivo ao armazenamento do módulo. 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

Em seguida, nas opções de criação para o módulo de borda no manifesto de implantação, pode adicionar uma definição de ligações mapeando o diretório ("var/local/mediaservices/") acima a um diretório no módulo (como "/var/lib/azuremediaservices/"). E usaria este último diretório como o valor para a aplicaçãoDataDirectory.

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

Se olhar para os gráficos de mídia de amostra para o arranque rápido e tutoriais, como [a gravação contínua de vídeo,](continuous-video-recording-tutorial.md)note que o diretório de cache de mídia (localMediaCachePath) utiliza uma subdiretório ao abrigo da aplicaçãoDataDirectory. Esta é a abordagem recomendada, uma vez que a cache contém dados transitórios.

### <a name="naming-video-assets-or-files"></a>Nomenclatura dos ficheiros ou recursos de vídeo

Os gráficos de mídia permitem a criação de ativos na nuvem ou ficheiros mp4 no limite. Os ativos dos meios de comunicação podem ser gerados através de [uma gravação contínua](continuous-video-recording-tutorial.md) de vídeo ou através de uma gravação de vídeo baseada em [eventos](event-based-video-recording-tutorial.md). Embora estes ativos e ficheiros possam ser nomeados como deseja, a estrutura de nomeação recomendada para um ativo de mídia contínua baseado em gravação de vídeo é " &lt; anytext &gt; -${System.GraphTopologyName}-${System.GraphInstanceName}". Como exemplo, pode definir o activoNamePattern na pia do ativo da seguinte forma:

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

Para os ativos gerados por gravação de vídeo baseados em eventos, o padrão de nomeação recomendado é " &lt; anytext &gt; -${System.DateTime}". A variável do sistema garante que os ativos não são substituídos se os eventos acontecerem ao mesmo tempo. Como exemplo, pode definir o activoNamePattern na Pia de Ativos da seguinte forma:

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

Se estiver a executar várias instâncias do mesmo gráfico, pode usar o nome de topologia do gráfico e o nome da instância para diferenciar. Como exemplo, pode definir o activoNamePattern na pia do ativo da seguinte forma:

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName} -${System.DateTime}"
```

Para os videoclips de mp4 gerados por vídeo baseados em eventos na borda, o padrão de nomeação recomendado deve incluir DateTime e, para várias instâncias do mesmo gráfico, recomendar a utilização das variáveis do sistema GraphTopologyName e GraphInstanceName. Como exemplo, pode definir filePathPattern na pia de ficheiro da seguinte forma: 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

Ou 

```
"filePathPattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```

### <a name="keeping-your-vm-clean"></a>Mantendo o seu VM limpo

O Linux VM que está a utilizar como dispositivo de borda pode tornar-se sem resposta se não for gerido periodicamente. É essencial manter as caches limpas, eliminar embalagens desnecessárias e remover também os recipientes não usados do VM. Para isso, aqui está um conjunto de comandos recomendados, pode utilizar no seu VM de borda.

1. `sudo apt-get clean`

    O comando apt-get limpo limpa o repositório local de ficheiros de pacotes recuperados que são deixados em /var/cache. Os diretórios que limpa são /var/cache/apt/archives/ e /var/cache/apt/archives/parcial/. Os únicos ficheiros que deixa em /var/cache/apt/archives são o ficheiro de bloqueio e o subdiretório parcial. O comando limpo apt-get é geralmente usado para limpar o espaço do disco conforme necessário, geralmente como parte da manutenção regular. Para obter mais informações, consulte a [Limpeza com apt-get](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html).
1. `sudo apt-get autoclean`

    A opção apt-get auto-lean, como apt-get clean, limpa o repositório local de ficheiros de pacotes recuperados, mas apenas remove ficheiros que já não podem ser descarregados e não são úteis. Ajuda a evitar que o seu cache cresça demasiado grande.
1. `sudo apt-get autoremove1`

    A opção de remoção automática remove pacotes que foram automaticamente instalados porque algum outro pacote os exigia mas, com os outros pacotes removidos, já não são necessários
1. `sudo docker image ls` – Fornece uma lista de imagens do Docker no seu sistema de bordas
1. `sudo docker system prune `

    Docker tem uma abordagem conservadora para limpar objetos não usados (muitas vezes referidos como "recolha de lixo"), como imagens, contentores, volumes e redes: estes objetos geralmente não são removidos a menos que você explicitamente peça Docker para fazê-lo. Isto pode fazer com que o Docker use espaço extra em disco. Para cada tipo de objeto, Docker fornece um comando de ameixa. Além disso, pode utilizar ameixas secas do sistema de estivadores para limpar vários tipos de objetos ao mesmo tempo. Para obter mais informações, consulte [os objetos estivadores não reutilizados.](https://docs.docker.com/config/pruning/)
1. `sudo docker rmi REPOSITORY:TAG`

    À medida que as atualizações acontecem no módulo de borda, o seu docker pode ter versões mais antigas do módulo de borda ainda presente. Neste caso, é aconselhável utilizar o comando rmi do docker para remover imagens específicas identificadas pela etiqueta da versão de imagem.

## <a name="next-steps"></a>Passos seguintes

[Quickstart: Get start - Live Video Analytics on IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
