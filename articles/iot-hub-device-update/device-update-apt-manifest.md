---
title: Compreenda a atualização do dispositivo para o manifesto Azure IoT Hub APT | Microsoft Docs
description: Compreenda como a Atualização do Dispositivo para o IoT Hub utiliza um manifesto apto para uma atualização baseada em pacotes.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b68b78499aa3bf0d84d8bd0fa5ab55d1f969113
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680093"
---
# <a name="device-update-apt-manifest"></a>Manifesto APT de atualização de dispositivos

O APT Manifest é um ficheiro JSON que descreve os detalhes de atualização exigidos pelo APT Update Handler. Este ficheiro pode ser importado para a Atualização do Dispositivo para IoT Hub, tal como qualquer outra atualização.

[Saiba mais](import-update.md) sobre a importação de atualizações para a Atualização do Dispositivo.

## <a name="overview"></a>Descrição Geral

Quando um manifesto APT é entregue a um Agente de Atualização de Dispositivos como atualização, o agente processará o manifesto e realizará as operações necessárias. Estas operações incluem o descarregamento e instalação dos pacotes especificados no ficheiro APT Manifesto e suas dependências.

A Atualização do Dispositivo suporta o APT UpdateType e o APT Update Handler. Este suporte permite ao Agente de Atualização do Dispositivo avaliar os pacotes Debian instalados e atualizar os pacotes necessários. 

## <a name="schema"></a>Esquema

Um ficheiro APT Manifesto é um ficheiro JSON com um esquema de versão.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

Exemplo:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

O nome deste Manifesto da APT. Este pode ser o nome ou iD que é significativo para os seus cenários. Por exemplo, `contoso-iot-edge`.

### <a name="version"></a>versão

Um número de versão para este Manifesto APT. Por exemplo, `1.0.0.0`.


### <a name="packages"></a>pacotes

Uma lista de objetos que contêm propriedades específicas do pacote.

#### <a name="name"></a>name

O nome ou identificação do pacote. Por exemplo, `iotedge`.

#### <a name="version"></a>versão

Os critérios de versão pretendidos para o pacote. Por exemplo, `1.0.8-2`.

Atualmente apenas o número exato da versão é suportado. O número da versão é a versão do pacote Debian desejada em formato [epoch:]upstream_version[-debian_revision].

**época** é um int não assinado.

**upstream_version** podem incluir alfanuméricos e personagens como "."""+", """"."". Deve começar com um dígito.
> [!NOTE]
> '1.0.8' é igual a '1.0.8-0'

Por exemplo, **`"name":"iotedge" and "version":"1.0.8-2"`** é equivalente a instalar um pacote usando o comando `apt-get install iotedge=1.0.8-2`

> [!NOTE]
> O valor da versão não contém um sinal igual

Se a versão for omitida, a versão mais recente disponível do pacote especificado será instalada.

[Saiba mais](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) sobre como os pacotes Debian são versados.

> [!NOTE]
> O gestor de pacotes APT ignora os requisitos de versão dados por um pacote quando as embalagens dependentes a instalar estão a ser automaticamente resolvidas. A menos que sejam dadas versões explícitas de pacotes dependentes, utilizarão as mais recentes, mesmo que o próprio pacote possa especificar um requisito rigoroso (=) numa determinada versão. Esta resolução automática pode conduzir a erros relativos a uma dependência insatisfeita. [Saiba mais](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Se estiver a atualizar uma versão específica do daemon de segurança Azure IoT Edge, então deve incluir a versão desejada do pacote e o `iotedge` seu pacote dependente no seu manifesto `libiothsm-std` APT.
[Saiba mais](https://docs.microsoft.com/azure/iot-edge/how-to-update-iot-edge#update-the-security-daemon)

> [!NOTE]
> Um manifesto apto pode ser usado para atualizar o agente de Atualização do Dispositivo e as suas dependências. Enuda o nome do agente de atualização do dispositivo e a versão desejada no manifesto apt, como faria em qualquer outro pacote. Este manifesto apto pode então ser importado e implantado através da Atualização do Dispositivo para o gasoduto IoT Hub. 

## <a name="removing-packages"></a>Remoção de pacotes

Também pode utilizar um manifesto apto para remover as embalagens instaladas do seu dispositivo. Um único manifesto apto pode ser usado para remover, adicionar e atualizar vários pacotes. Para remover uma embalagem, adicione um sinal negativo "-" após o nome do pacote. Não deve incluir um número de versão para os pacotes que está a remover. Remover pacotes através de um manifesto apto não remove as suas dependências e configurações.

Exemplo:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
Este manifesto apto removerá a embalagem "foo" do(s) dispositivo(s) para o qual é implantada. 

## <a name="recommended-value-for-installed-criteria"></a>Valor recomendado para critérios instalados

O Critério Instalado para um Manifesto APT é `<name>-<version>` onde está o nome do Manifesto `<name>` APT e é a versão do Manifesto `<version>` da APT. Por exemplo, `contoso-iot-edge-1.0.0.0`. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>Orientações sobre a criação de um Manifesto APT

Ao criar o Manifesto APT, existem algumas diretrizes a ter em mente:

- Certifique-se sempre de que o Manifesto APT é um ficheiro json bem formado
- Cada Manifesto APT deve ter uma versão única. Tente criar uma metodologia padronizada para incrementar a versão do Manifesto APT, para que faça sentido para os seus cenários e possa ser facilmente seguida
- No que diz respeito ao estado desejado de cada embalagem individual, especifique o nome e a versão exatos do pacote que pretende instalar no seu dispositivo. Valide sempre os valores contra o repositório de pacotes que pretende utilizar como fonte para o pacote
- Certifique-se de que as embalagens do Manifesto APT estão listadas na ordem em que devem ser instaladas/removidas
- Valide sempre a instalação de embalagens num dispositivo de teste para garantir que o resultado é desejado
- Ao instalar uma versão específica de um pacote (por `iotedge 1.0.9-1` exemplo, é melhor que também tenha no Manifesto APT as versões explícitas dos pacotes dependentes a instalar (por exemplo, `libiothsm 1.0.9-1` )
- Embora não seja mandatado, certifique-se sempre de que o seu Manifesto APT é cumulativo para evitar colocar o seu dispositivo num estado desconhecido. Uma atualização cumulativa garantirá que os seus dispositivos têm a versão desejada de cada pacote que lhe interessa, mesmo que o dispositivo tenha ignorado uma implementação de APT Update devido a uma falha na instalação ou que esteja a ser retirado offline

Por exemplo:

**Manifesto base APT**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**MÁ ATUALIZAÇÃO**

Esta atualização inclui o pacote de barras, mas não o pacote foo.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**BOA ATUALIZAÇÃO**

Esta atualização inclui o pacote foo, e também inclui pacote de bar.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Importar nova atualização](import-update.md)

