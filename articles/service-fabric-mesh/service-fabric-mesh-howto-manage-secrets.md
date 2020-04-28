---
title: Gerir segredos de aplicação de malha de tecido de tecido azure
description: Gerir os Segredos da Aplicação para que possa criar e implementar de forma segura uma aplicação de malha de tecido de serviço.
ms.date: 4/2/2019
ms.topic: conceptual
ms.openlocfilehash: d7946092a0bebe374404870fcd711ad33cc98b11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75461930"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Gerir os segredos de aplicação da malha de tecido de serviço
Service Fabric Mesh suporta Segredos como recursos Azure. Um segredo de malha de tecido de serviço pode ser qualquer informação sensível de texto, tais como cordas de ligação de armazenamento, palavras-passe ou outros valores que devem ser armazenados e transmitidos de forma segura. Este artigo mostra como usar o Serviço de Loja Segura de Tecido de Serviço para implementar e manter segredos.

Uma aplicação de malha secreta consiste em:
* Um recurso **Secrets,** que é um recipiente que armazena segredos de texto. Os segredos contidos no recurso **Secrets** são armazenados e transmitidos de forma segura.
* Um ou mais **recursos Secrets/Values** que estão armazenados no contentor de recursos **Secrets.** Cada recurso **Secrets/Values** distingue-se por um número de versão. Não é possível modificar uma versão de um recurso **Secrets/Values,** apenas anexar uma nova versão.

A Gestão de Segredos consiste nos seguintes passos:
1. Declare um recurso De **Malha Secrets** num ficheiro YAML ou JSON do Modelo de Recursos Azure utilizando definições de tipo Valor InlinedValue e SecretsStoreRef.
2. Declare recursos de **Segredos/Valores** de Malha num ficheiro YAML ou JSON modelo de recursos Azure que será armazenado no recurso **Secrets** (a partir do passo 1).
3. Modificar a aplicação de malha para referência Valores secretos da malha.
4. Implementar ou fazer upgrade da aplicação Malha para consumir valores secretos.
5. Utilize comandos CLI "az" Azure para gestão do ciclo de vida secure Store Service.

## <a name="declare-a-mesh-secrets-resource"></a>Declare um recurso de Segredos de Malha
Um recurso De Malha Secrets é declarado num ficheiro Azure Resource Model JSON ou YAML utilizando a definição do tipo InlinedValue. O recurso Mesh Secrets suporta segredos de origem do Secure Store Service. 
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

## <a name="declare-mesh-secretsvalues-resources"></a>Declarar segredos de malha/recursos de valores
Os recursos da Malha Secrets/Values têm uma dependência dos recursos da Malha Secrets definidos no passo anterior.

No que diz respeito à relação entre os campos "recursos" da secção "valor:" e "nome:": a segunda parte da corda "nome:" delimitada por um cólon é o número de versão usado para um segredo, e o nome antes do cólon precisa de corresponder ao valor secreto da Malha para o qual tem uma dependência. Por exemplo, ```name: mysecret:1.0```para o elemento , o número ```mysecret``` da versão é ```"value": "mysecret"```1.0 e o nome deve coincidir com o previamente definido .

>
Segue-se um exemplo de como declarar os recursos da Malha Secrets/Values num ficheiro JSON:

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
Segue-se um exemplo de como declarar os recursos da Malha Secrets/Values num ficheiro YAML:
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

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Modificar aplicação de malha para valores secretos de malha de referência
As aplicações de malha de tecido de serviço devem estar cientes das seguintes duas cordas para consumir valores secretos do Secure Store Service:
1. Microsoft.ServiceFabricMesh/Secrets.name contém o nome do ficheiro, e conterá o valor Segredos em texto simples.
2. A variável ambiental "Fabric_SettingPath" do Windows ou do Ambiente Linux contém o caminho do diretório para onde serão acessíveis ficheiros que contenham valores de Segredos de Serviço de Loja Segura. Isto é "C:\Definições" para aplicações de malha hospedadas pelo Windows e "/var/" para aplicações de malha hospedadas em Linux, respectivamente.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Implementar ou utilizar um upgrade rolante para aplicação de malha para consumir valores secretos
Criar Segredos e/ou valores versados limita-se ao modelo de recursos declarados implementações. A única maneira de criar estes recursos é através de um ficheiro JSON ou YAML modelo de recursos utilizando o comando de implantação de **malha az** da seguinte forma:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Comandos Azure CLI para gestão de ciclo de vida secure store

### <a name="create-a-new-secrets-resource"></a>Criar um novo recurso Secrets
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Passe quer **o modelo-ficheiro** ou **o modelo-uri** (mas não ambos).

Por exemplo:
- az implementação de malha criar --c:\MyMeshTemplates\SecretTemplate1.txt
- az malha implementação criar\/--https: /www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Mostrar um segredo
Devolve a descrição do segredo (mas não o valor).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Apagar um Segredo

- Um segredo não pode ser apagado enquanto estiver a ser referenciado por um pedido de malha.
- Eliminar um recurso Secrets elimina todas as versões Segredos/Recursos.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>List Secrets em Subscrição
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>List Secrets no Grupo de Recursos
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Listar todas as versões de um segredo
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Mostrar valor da versão secreta
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Excluir valor da versão secreta
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>Passos seguintes 
Para saber mais sobre a Malha de Tecido de Serviço, leia a visão geral:
- [Visão geral da malha de tecido de serviço](service-fabric-mesh-overview.md)
