---
title: Gerir segredos da aplicação do Azure Service Fabric malha | Documentos da Microsoft
description: Gerir segredos da aplicação para que possa criar e implementar uma aplicação de malha do Service Fabric em segurança.
services: service-fabric-mesh
keywords: segredos
author: aljo-microsoft
ms.author: aljo
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: 36d0b49f1b9fb1ca5d13283146d134137a5cb028
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900646"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Gerir segredos da aplicação do Service Fabric malha
Malha do Service Fabric suporta segredos como recursos do Azure. Um segredo de malha do Service Fabric pode ser qualquer informação de texto confidenciais, como cadeias de ligação de armazenamento, as palavras-passe ou outros valores que devem ser armazenados e transmitidos de forma segura. Este artigo mostra como utilizar o serviço de Store seguro do Service Fabric para implementar e manter segredos.

Um aplicativo de malha segredo consiste em:
* R **segredos** recurso, o que é um contentor que armazena os segredos de texto. Segredos contidos a **segredos** recursos são armazenados e transmitidos de forma segura.
* Um ou mais **segredos/valores** recursos que estão armazenados no **segredos** contentor de recursos. Cada **segredos/valores** recursos distingue-se por um número de versão. Não é possível modificar uma versão de um **segredos/valores** recursos, apenas acrescentar uma nova versão.

Gerir segredos inclui os seguintes passos:
1. Declarar uma malha **segredos** recurso num ficheiro JSON ou YAML de modelo de recursos do Azure com o tipo de inlinedValue e definições de contentType SecretsStoreRef.
2. Declarar a malha **segredos/valores** recursos num ficheiro JSON ou YAML de modelo de recursos do Azure que serão armazenados na **segredos** recursos (a partir do passo 1).
3. Modificar o aplicativo de malha para fazer referência a valores secretos da malha.
4. Implementar ou a aplicação de malha para consumir os valores secretos a atualização sem interrupção.
5. Comandos do Azure de utilização "az" CLI para a gestão de ciclo de vida do serviço de Store seguro.

## <a name="declare-a-mesh-secrets-resource"></a>Declare um recurso de segredos de malha
Um recurso de segredos de malha é declarado num JSON de modelo de recursos do Azure ou o ficheiro YAML usando o tipo de inlinedValue e definições de contentType SecretsStoreRef. O recurso de segredos de malha suporta segredos de serviço Store seguro obtido. 
>
Segue-se um exemplo de como declarar os recursos de malha segredos num ficheiro JSON:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "metadata": {
        "description": "Location of the resources."
      }
    },
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MySecret.txt",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "My Mesh Application Secret",
        "contentType": "SecretsStoreRef",
        "value": "mysecret",
      }
    },
  ]
}
```
Segue-se um exemplo de como declarar os recursos de malha segredos num ficheiro YAML:
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

## <a name="declare-mesh-secretsvalues-resources"></a>Declarar os recursos de malha segredos/valores
Recursos de segredos/valores de malha têm uma dependência nos recursos de malha segredos definidos no passo anterior.

Sobre a relação entre a seção "recursos" "valor:" e "nome:" campos: a segunda parte do "nome:" cadeia de caracteres delimitada por vírgula é o número de versão utilizado para um segredo e o nome antes dos dois pontos tem de corresponder ao valor secreto malha para a qual tem um dependência. Por exemplo, para o elemento ```name: mysecret:1.0```, o número de versão é 1.0 e o nome ```mysecret``` tem de corresponder ao definido anteriormente ```"value": "mysecret"```.

>
Segue-se um exemplo de como declarar os recursos de malha segredos/valores num ficheiro JSON:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "my-secret-value-v1": {
      "type": "string",
      "metadata": {
        "description": "My Mesh Application Secret Value."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MySecret.txt",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]",
      "properties": {
        "kind": "inlinedValue",
        "description": "My Mesh Application Secret",
        "contentType": "SecretsStoreRef",
        "value": "mysecret",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "mysecret:1.0",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        'Microsoft.ServiceFabricMesh/secrets/MySecret.txt'
      ],
      "properties": {
        "value": "[parameters('my-secret-value-v1)]"
      }
    }
  ]
}
```
Segue-se um exemplo de como declarar os recursos de malha segredos/valores num ficheiro YAML:
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

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Modificar a aplicação de malha para fazer referência a valores de segredo de malha
Malha de recursos de infraestrutura do serviço de aplicações tem de ter em consideração as seguintes cadeias de caracteres de dois para poder consumir valores do Secure Store segredo do serviço:
1. Microsoft.ServiceFabricMesh/Secrets.name contém o nome do ficheiro e conterá o valor de segredos em texto não encriptado.
2. A variável de ambiente Windows ou Linux "Fabric_SettingPath" contém o caminho do diretório para onde os ficheiros que contém os valores de proteger segredos de serviço de Store estarão acessíveis. Este é o "C:\Settings" para alojado no Windows e "/ var/settings" para aplicações de malha alojado no Linux, respetivamente.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Implementar ou utilizar uma atualização sem interrupção para a aplicação de malha para consumir valores secretos
A criação de segredos e/ou com a versão de segredos/valores é limitado ao modelo de recurso declarado implementações. A única forma de criar esses recursos está passando num recurso modelo JSON ou YAML ficheiro com o **implementação de malha az** comando da seguinte forma:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Comandos da CLI do Azure para a gestão de ciclo de vida do serviço de Store seguro

### <a name="create-a-new-secrets-resource"></a>Criar um novo recurso de segredos
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Transmitir o **arquivo de modelo** ou **uri de modelo** (mas não ambos).

Por exemplo:
- az mesh deployment create --c:\MyMeshTemplates\SecretTemplate1.txt
- az mesh deployment create -- https://www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Mostrar um segredo
Devolve a descrição do segredo (mas não o valor).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Eliminar um segredo

- Não é possível eliminar um segredo enquanto está a ser referenciado por um aplicativo de malha.
- A eliminar um recurso de segredos elimina todas as versões de segredos/recursos.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>Lista os segredos na subscrição
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Lista os segredos num grupo de recursos
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Listar todas as versões de um segredo
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Mostrar valor de versão do segredo
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Eliminar valor de versão do segredo
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>Passos Seguintes 
Para saber mais sobre a malha de recursos de infraestrutura do serviço, leia a visão geral:
- [Descrição geral de malha do Service Fabric](service-fabric-mesh-overview.md)
