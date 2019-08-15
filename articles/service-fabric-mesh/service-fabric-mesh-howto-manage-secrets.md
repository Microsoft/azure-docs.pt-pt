---
title: Gerenciar segredos do aplicativo de malha de Service Fabric do Azure | Microsoft Docs
description: Gerencie os segredos do aplicativo para que você possa criar e implantar com segurança um aplicativo de malha Service Fabric.
services: service-fabric-mesh
keywords: segredos
author: athinanthny
ms.author: atsenthi
ms.date: 4/2/2019
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: ef3f04437aca7b6ad9aab8806d54e65d00159d87
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036174"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Gerenciar segredos do aplicativo de malha Service Fabric
A malha Service Fabric dá suporte a segredos como recursos do Azure. Um segredo de malha Service Fabric pode ser qualquer informação de texto confidencial, como cadeias de conexão de armazenamento, senhas ou outros valores que devem ser armazenados e transmitidos com segurança. Este artigo mostra como usar o Service Fabric Serviço de Repositório Seguro para implantar e manter segredos.

Um segredo de aplicativo de malha consiste em:
* Um recurso de **segredos** , que é um contêiner que armazena segredos de texto. Os segredos contidos no recurso de **segredos** são armazenados e transmitidos com segurança.
* Um ou mais recursos de **segredos/valores** que são armazenados no contêiner de recursos de **segredos** . Cada recurso de **segredos/valores** é diferenciado por um número de versão. Você não pode modificar uma versão de um recurso de **segredos/valores** , anexar apenas uma nova versão.

O gerenciamento de segredos consiste nas seguintes etapas:
1. Declare um recurso de **segredos** de malha em um arquivo YAML ou JSON do modelo de recurso do Azure usando as definições de ContentType e SecretsStoreRef do tipo inlinedValue.
2. Declare os recursos de **segredos/valores** de malha em um arquivo YAML ou JSON do modelo de recurso do Azure que será armazenado no recurso de **segredos** (da etapa 1).
3. Modifique o aplicativo de malha para referenciar valores de segredo de malha.
4. Implantar ou fazer a atualização sem interrupção do aplicativo de malha para consumir valores secretos.
5. Use os comandos da CLI do Azure "AZ" para Serviço de Repositório Seguro gerenciamento do ciclo de vida.

## <a name="declare-a-mesh-secrets-resource"></a>Declarar um recurso de segredos de malha
Um recurso de segredos de malha é declarado em um arquivo JSON ou YAML do modelo de recurso do Azure usando a definição de tipo inlinedValue. O recurso de segredos de malha dá suporte a Serviço de Repositório Seguro segredos de origem. 
>
Veja a seguir um exemplo de como declarar recursos de segredos de malha em um arquivo JSON:

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
Veja a seguir um exemplo de como declarar recursos de segredos de malha em um arquivo YAML:
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

## <a name="declare-mesh-secretsvalues-resources"></a>Declarar recursos de valores/segredos de malha
Os recursos de segredos/valores de malha têm uma dependência dos recursos de segredos de malha definidos na etapa anterior.

Sobre a relação entre os campos "value:" e "Name:" da seção "Resources": a segunda parte da cadeia de caracteres "Name:" delimitada por dois-pontos é o número de versão usado para um segredo e o nome antes dos dois-pontos precisa corresponder ao valor de segredo de malha para o qual ele tem um Estados. Por exemplo, para elemento ```name: mysecret:1.0```, o número de versão é 1,0 e o ```mysecret``` nome deve corresponder ao definido ```"value": "mysecret"```anteriormente.

>
Veja a seguir um exemplo de como declarar recursos de valores/segredos de malha em um arquivo JSON:

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
Veja a seguir um exemplo de como declarar recursos de valores/segredos de malha em um arquivo YAML:
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

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Modificar o aplicativo de malha para referenciar valores secretos de malha
Service Fabric aplicativos de malha precisam estar cientes das duas cadeias de caracteres a seguir para consumir Serviço de Repositório Seguro valores secretos:
1. Microsoft. ServiceFabricMesh/segredos. Name contém o nome do arquivo e conterá o valor dos segredos em texto não criptografado.
2. A variável de ambiente Windows ou Linux "Fabric_SettingPath" contém o caminho de diretório para onde os arquivos que contêm Serviço de Repositório Seguro valores de segredos estarão acessíveis. Isso é "C:\Settings" para Windows – hospedado e "/var/Settings" para aplicativos de malha hospedados em Linux, respectivamente.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Implantar ou usar uma atualização sem interrupção para o aplicativo de malha consumir valores secretos
A criação de segredos e/ou valores/segredos com controle de versão é limitada a implantações declaradas do modelo de recurso. A única maneira de criar esses recursos é passando um arquivo de modelo de recurso JSON ou YAML usando o comando **AZ mesh Deployment** da seguinte maneira:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>CLI do Azure comandos para o gerenciamento do ciclo de vida Serviço de Repositório Seguro

### <a name="create-a-new-secrets-resource"></a>Criar um novo recurso de segredos
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Passe o **modelo-arquivo** ou **modelo-URI** (mas não ambos).

Por exemplo:
- AZ mesh implantação Create--c:\MyMeshTemplates\SecretTemplate1.txt
- AZ mesh implantação Create--https:\//www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Mostrar um segredo
Retorna a descrição do segredo (mas não o valor).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Excluir um segredo

- Um segredo não pode ser excluído enquanto está sendo referenciado por um aplicativo de malha.
- A exclusão de um recurso de segredos exclui todas as versões de segredos/recursos.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>Listar segredos na assinatura
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Listar segredos no grupo de recursos
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

## <a name="next-steps"></a>Passos Seguintes 
Para saber mais sobre a malha de Service Fabric, leia a visão geral:
- [Visão geral da malha de Service Fabric](service-fabric-mesh-overview.md)
