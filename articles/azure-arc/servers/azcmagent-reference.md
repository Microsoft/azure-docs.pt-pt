---
title: Interface da CLI do Azure Connected Machine Agent
description: Documentação de referência para o agente da máquina ligada ao Azure CLI
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513201"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Interface da CLI do Azure Connected Machine Agent

A `Azcmagent` ferramenta (Agente de Máquinas Conectadas Azure) é utilizada para configurar e resolver problemas uma ligação de máquinas não-azure ao Azure.

O próprio agente é um processo `himdsd` de daemon chamado `himds` Linux, e um Serviço Windows chamado no Windows.

Em uso `azcmagent connect` normal, é usado para estabelecer uma ligação entre esta máquina e Azure, e `azcmagent disconnect` se você decidir que você não quer mais essa ligação. Os outros comandos são para resolução de problemas ou outros casos especiais.

## <a name="options"></a>Opções

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>VER TAMBÉM

* [azcmagent connect](#azcmagent-connect) - Liga esta máquina ao Azure
* [azcmagent desconexão](#azcmagent-disconnect) - Desliga esta máquina de Azure
* [azcmagent reconectar](#azcmagent-reconnect) - Reconecta esta máquina a Azure
* [azcmagent show](#azcmagent-show) - Obtém metadados de máquina e estado de agente. Isto é essencialmente útil para resolução de problemas.
* [versão azcmagent](#azcmagent-version) - Mostrar a versão Hybrid Management Agent

## <a name="azcmagent-connect"></a>azcmagent conectar

Liga esta máquina ao Azure

### <a name="synopsis"></a>Sinopse

Cria um recurso em Azure representando esta máquina.

Isto utiliza as opções de autenticação fornecidas para criar um recurso no Gestor de Recursos Azure que representa esta máquina. O recurso está no grupo de subscrição e recursos solicitado, e os dados sobre a máquina são armazenados na região de Azure especificados pelo parâmetro de localização.
O nome de recurso predefinido é o nome de anfitrião desta máquina se não for ultrapassado.

Um certificado correspondente à Identidade Atribuída ao Sistema desta máquina é então descarregado e armazenado localmente. Assim que este passo completar o Serviço de **Metadados de Máquinas Conectadas Azure** e o Agente de Configuração do Hóspede começam a sincronizar com a nuvem Azure.

Opções de autenticação:

* Ficha de acesso`azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* Id e segredo do principal de serviço`azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Sinal de inscrição no dispositivo (Interativo)`azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>Sintaxe

```none
azcmagent connect [flags]
```

### <a name="options"></a>Opções

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>desconexão azcmagent

Desliga esta máquina de Azure

### <a name="synopsis"></a>Sinopse

Elimina o recurso em Azure que representa este servidor.

Este comando utiliza as opções de autenticação fornecidas para remover o recurso Do Gestor de Recursos Azure que representa esta máquina. Depois deste ponto, o Serviço de Metadados de **Máquinas Conectadas Azure** e o Agente de Configuração do Hóspede serão desligados. Este comando não para nem remove os serviços: retire a embalagem para o fazer.

Este comando requer privilégios mais elevados do que a função "Azure Connected Machine Onboarding".

Uma vez desligada uma `azcmagent connect`máquina, `azcmagent reconnect` utilize, não se quiser criar um novo recurso para a máquina em Azure.

Opções de Autenticação:

* Ficha de acesso`azcmagent disconnect --access-token <>`
* Id e segredo do principal de serviço`azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Sinal de dispositivo interativo`azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>Sintaxe

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>Opções

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>azcmagent reconectar

Reconecta esta máquina ao Azure

### <a name="synopsis"></a>Sinopse

Religue a máquina com credenciais inválidas para o Azure.

Se uma máquina já tiver um recurso em Azure mas não for capaz de autenticar, pode ser reconectada utilizando este comando. Isto é possível se uma máquina tiver sido desligada o tempo suficiente para que o seu certificado expirasse (pelo menos 45 dias).

Se uma máquina foi `azcmagent disconnect`desligada, utilize `azcmagent connect` em vez disso.

Este comando utiliza as opções de autenticação fornecidas para recuperar novas credenciais correspondentes ao recurso Do Gestor de Recursos Azure que representa esta máquina.

Este comando requer privilégios mais elevados do que a função de embarque da **Máquina Conectada Azure.**

Opções de Autenticação

* Ficha de acesso`azcmagent reconnect --access-token <>`
* Id e segredo do principal de serviço`azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Sinal de dispositivo interativo`azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>Sintaxe

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>Opções

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>show azcmagent

Obtém metadados de máquinas e estatuto de agente. Isto é essencialmente útil para resolução de problemas.

### <a name="synopsis"></a>Sinopse

Obtém metadados de máquinas e estatuto de agente. Isto é essencialmente útil para resolução de problemas.


### <a name="syntax"></a>Sintaxe

```
azcmagent show [flags]
```

### <a name="options"></a>Opções

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>versão azcmagent

Mostrar a versão do Agente de Gestão Híbrida

### <a name="synopsis"></a>Sinopse

Mostrar a versão do Agente de Gestão Híbrida

### <a name="syntax"></a>Sintaxe

```none
azcmagent version [flags]
```

### <a name="options"></a>Opções

```none
  -h, --help   help for version
```
