---
title: Gerir segredos de aplicação de malha de malha de tecido de serviço Azure
description: Gerencie a aplicação Secrets para que possa criar e implementar de forma segura uma aplicação de malha de tecido de serviço.
ms.date: 4/2/2019
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3be0c2b21c3405f4f42b2ff4d02ca95c78956de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626965"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Gerir segredos de aplicação de malha de tecido de serviço

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

A Malha de Tecido de Serviço suporta segredos como recursos Azure. Um segredo de malha de tecido de serviço pode ser qualquer informação de texto sensível, como cadeias de ligação de armazenamento, palavras-passe ou outros valores que devem ser armazenados e transmitidos de forma segura. Este artigo mostra como usar o Serviço de Loja Segura de Tecido de Serviço para implementar e manter segredos.

Uma aplicação de malha Secreta consiste em:
* Um recurso **Secrets,** que é um recipiente que armazena segredos de texto. Os segredos **contidos** no recurso Secrets são armazenados e transmitidos de forma segura.
* Um ou mais recursos **Secrets/Values** que estão armazenados no contentor de recursos **Secrets.** Cada recurso **Segredos/Valores** distingue-se por um número de versão. Não é possível modificar uma versão de um recurso **Secrets/Values,** apenas anexar uma nova versão.

A Gestão de Segredos consiste nos seguintes passos:
1. Declare um recurso Mesh **Secrets** num ficheiro Azure Resource Model YAML ou JSON utilizando definições de conteúdo inlinedValue e SecretsStoreRef.
2. Declare os recursos **de Segredos/Valores** de Malha num ficheiro Azure Resource Model YAML ou JSON que será armazenado no recurso **Secrets** (a partir do passo 1).
3. Modificar a aplicação de malha para referenciar valores secretos da malha.
4. Implementar ou rolar a aplicação Mesh para consumir valores secretos.
5. Utilize comandos CLI Azure "az" para a gestão do ciclo de vida do Serviço de Loja Segura.

## <a name="declare-a-mesh-secrets-resource"></a>Declare um recurso Segredos de Malha
Um recurso Mesh Secrets é declarado num ficheiro Azure Resource Model JSON ou YAML utilizando uma definição do tipo InlinedValue. O recurso Mesh Secrets suporta segredos de origem do Serviço Secure Store. 
>
Segue-se um exemplo de como declarar os recursos da Mesh Secrets num ficheiro JSON:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    }
  ]
}
```
Segue-se um exemplo de como declarar os recursos da Mesh Secrets num ficheiro YAML:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>Declarar recursos de Segredos/Valores de Malha
Os recursos de Segredos/Valores de Malha têm uma dependência dos recursos da Malha Segredos definidos no passo anterior.

No que diz respeito à relação entre os campos "valor:" e "nome:" da segunda parte da "linha":" delimitada por um cólon é o número de versão utilizado para um segredo, e o nome antes do cólon precisa de corresponder ao valor secreto da malha para o qual tem uma dependência. Por exemplo, para o elemento ```name: mysecret:1.0``` , o número da versão é 1.0 e o nome deve corresponder ao ```mysecret``` previamente definido ```"value": "mysecret"``` .

>
Segue-se um exemplo de como declarar os recursos de Segredos/Valores de Malha num ficheiro JSON:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx/2019.02.28",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/sfbpHttpsCertificate.pfx"
      ],
      "properties": {
        "value": "[parameters('sfbpHttpsCertificate')]"
      }
    }
  ],
}
```
Segue-se um exemplo de como declarar os recursos de Segredos/Valores de Malha num ficheiro YAML:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Modificar a aplicação de malha para referência valores secretos de malha
As aplicações de malha de tecido de serviço precisam estar cientes das seguintes duas cordas para consumir valores secretos do Serviço de Loja Segura:
1. Microsoft.ServiceFabricMesh/Secrets.name contém o nome do ficheiro e conterá o valor Secrets em texto simples.
2. A variável ambiente Windows ou Linux "Fabric_SettingPath" contém o caminho do diretório para onde os ficheiros que contêm valores de Segredos de Serviço de Loja Segura estarão acessíveis. Trata-se de "C:\Definições" para aplicações de malha com windows e "var/configurações", respectivamente.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Implementar ou utilizar um upgrade rolante para aplicação de Malha para consumir valores secretos
Criar Segredos e/ou Versões/Valores está limitado a implementações declaradas por modelo de recursos. A única forma de criar estes recursos é passando um modelo de recurso JSON ou ficheiro YAML utilizando o comando **de implantação de malha az** da seguinte forma:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Azure CLI comanda para gestão de ciclo de vida do Serviço de Loja Segura

### <a name="create-a-new-secrets-resource"></a>Criar um novo recurso Secrets
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Passe o **modelo-file** ou **o modelo-uri** (mas não ambos).

Por exemplo:
- az implantação de malha criar --c:\MyMeshTemplates\SecretTemplate1.txt
- az implantação de malha criar --https: \/ /www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Mostrar um segredo
Devolve a descrição do segredo (mas não o valor).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Apagar um segredo

- Um segredo não pode ser apagado enquanto estiver a ser referenciado por uma aplicação de malha.
- A eliminação de um recurso Secrets elimina todas as versões Secrets/Resources.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>Segredos da lista na subscrição
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Segredos da Lista no Grupo de Recursos
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Listar todas as versões de um segredo
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Mostrar valor de versão secreta
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Excluir valor de versão secreta
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>Passos seguintes 
Para saber mais sobre a Malha de Tecido de Serviço, leia a visão geral:
- [Visão geral da malha de tecido de serviço](service-fabric-mesh-overview.md)
