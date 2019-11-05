---
title: Interface da CLI do agente do computador conectado do Azure
description: Documentação de referência para a CLI do agente de máquina conectada do Azure
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513201"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Interface da CLI do agente do computador conectado do Azure

A ferramenta `Azcmagent` (agente do computador conectado do Azure) é usada para configurar e solucionar problemas de uma conexão de computadores não Azure com o Azure.

O próprio agente é um processo de daemon chamado `himdsd` no Linux e um serviço do Windows chamado `himds` no Windows.

No uso normal, `azcmagent connect` é usado para estabelecer uma conexão entre este computador e o Azure e `azcmagent disconnect` se você decidir que não deseja mais essa conexão. Os outros comandos são para solução de problemas ou outros casos especiais.

## <a name="options"></a>Opções

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>CONSULTE TAMBÉM

* [azcmagent Connect](#azcmagent-connect) – conecta este computador ao Azure
* [azcmagent desconectar – desconecta](#azcmagent-disconnect) este computador do Azure
* [reconectar azcmagent](#azcmagent-reconnect) -reconecta este computador ao Azure
* [azcmagent show](#azcmagent-show) – Obtém os metadados do computador e o status do agente. Isso é útil principalmente para solução de problemas.
* [versão do azcmagent](#azcmagent-version) – exibir a versão do agente de gerenciamento híbrido

## <a name="azcmagent-connect"></a>azcmagent conectar

Conecta este computador ao Azure

### <a name="synopsis"></a>Resumo

Cria um recurso no Azure que representa este computador.

Isso usa as opções de autenticação fornecidas para criar um recurso no Azure Resource Manager que representa esse computador. O recurso está na assinatura e no grupo de recursos solicitado, e os dados sobre a máquina são armazenados na região do Azure especificada pelo parâmetro local.
O nome do recurso padrão é o nome do host deste computador, se não for substituído.

Um certificado correspondente à identidade atribuída pelo sistema dessa máquina é então baixado e armazenado localmente. Depois que essa etapa concluir o serviço de **metadados da máquina conectada do Azure** e o agente de configuração do convidado começar a sincronizar com a nuvem do Azure.

Opções de autenticação:

* `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>` de token de acesso
* ID da entidade de serviço e segredo `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Entrada do dispositivo (interativa) `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

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

## <a name="azcmagent-disconnect"></a>desconectar azcmagent

Desconecta este computador do Azure

### <a name="synopsis"></a>Resumo

Exclui o recurso no Azure que representa este servidor.

Esse comando usa as opções de autenticação fornecidas para remover o Azure Resource Manager recurso que representa esse computador. Após esse ponto, o **serviço de metadados do computador conectado do Azure** e o agente de configuração do convidado serão desconectados. Este comando não interrompe ou remove os serviços: Remova o pacote para fazer isso.

Esse comando requer privilégios mais altos do que a função "integração de máquinas conectadas do Azure".

Quando um computador estiver desconectado, use `azcmagent connect`, não `azcmagent reconnect` se você quiser criar um novo recurso para ele no Azure.

Opções de autenticação:

* `azcmagent disconnect --access-token <>` de token de acesso
* ID da entidade de serviço e segredo `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* `azcmagent disconnect --tenant-id <>` de entrada do dispositivo interativo

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

## <a name="azcmagent-reconnect"></a>reconectar azcmagent

Reconecta este computador ao Azure

### <a name="synopsis"></a>Resumo

Reconecte o computador com credenciais inválidas para o Azure.

Se um computador já tiver um recurso no Azure, mas não conseguir se autenticar nele, ele poderá ser reconectado usando esse comando. Isso será possível se uma máquina tiver sido desativada por tempo suficiente para que seu certificado expire (pelo menos 45 dias).

Se um computador tiver sido desconectado com `azcmagent disconnect`, use `azcmagent connect` em vez disso.

Esse comando usa as opções de autenticação fornecidas para recuperar novas credenciais correspondentes ao recurso de Azure Resource Manager que representa esse computador.

Este comando requer privilégios mais altos do que a função de **integração do computador conectado do Azure** .

Opções de autenticação

* `azcmagent reconnect --access-token <>` de token de acesso
* ID da entidade de serviço e segredo `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* `azcmagent reconnect --tenant-id <>` de entrada do dispositivo interativo

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

## <a name="azcmagent-show"></a>azcmagent mostrar

Obtém os metadados do computador e o status do agente. Isso é útil principalmente para solução de problemas.

### <a name="synopsis"></a>Resumo

Obtém os metadados do computador e o status do agente. Isso é útil principalmente para solução de problemas.


### <a name="syntax"></a>Sintaxe

```
azcmagent show [flags]
```

### <a name="options"></a>Opções

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>versão do azcmagent

Exibir a versão do agente de gerenciamento híbrido

### <a name="synopsis"></a>Resumo

Exibir a versão do agente de gerenciamento híbrido

### <a name="syntax"></a>Sintaxe

```none
azcmagent version [flags]
```

### <a name="options"></a>Opções

```none
  -h, --help   help for version
```
