---
title: Repositório de segredos do Azure Service Fabric central
description: Este artigo descreve como usar o repositório de segredos centrais no Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: bc6ea6260bf50d5b4f8e294e0a3827426f90bee3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980930"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Repositório de segredos centrais no Azure Service Fabric 
Este artigo descreve como usar o armazenamento de segredos centrais (CSS) no Azure Service Fabric para criar segredos em aplicativos Service Fabric. O CSS é um cache de repositório de segredo local que mantém dados confidenciais, como senha, tokens e chaves, criptografados na memória.

## <a name="enable-central-secrets-store"></a>Habilitar repositório de segredos centrais
Adicione o script a seguir à sua configuração de cluster em `fabricSettings` para habilitar o CSS. Recomendamos que você use um certificado diferente de um certificado de cluster para CSS. Verifique se o certificado de criptografia está instalado em todos os nós e se `NetworkService` tem permissão de leitura para a chave privada do certificado.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Declarar um recurso secreto
Você pode criar um recurso secreto usando o modelo de Azure Resource Manager ou a API REST.

### <a name="use-resource-manager"></a>Usar o Gerenciador de recursos

Use o modelo a seguir para usar o Gerenciador de recursos para criar o recurso secreto. O modelo cria um `supersecret` recurso secreto, mas nenhum valor é definido para o recurso secreto ainda.


```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```

### <a name="use-the-rest-api"></a>Utilizar a API REST

Para criar um `supersecret` recurso secreto usando a API REST, faça uma solicitação PUT para `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Você precisa do certificado do cluster ou do certificado do cliente administrador para criar um recurso secreto.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-the-secret-value"></a>Definir o valor secreto

### <a name="use-the-resource-manager-template"></a>Usar o modelo do Resource Manager

Use o modelo do Resource Manager a seguir para criar e definir o valor secreto. Este modelo define o valor secreto para o `supersecret` recurso secreto como versão `ver1`.
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
### <a name="use-the-rest-api"></a>Utilizar a API REST

Use o script a seguir para usar a API REST para definir o valor secreto.
```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="use-the-secret-in-your-application"></a>Usar o segredo em seu aplicativo

Siga estas etapas para usar o segredo em seu aplicativo Service Fabric.

1. Adicione uma seção no arquivo **Settings. xml** com o trecho a seguir. Observe aqui que o valor está no formato {`secretname:version`}.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importe a seção em **ApplicationManifest. xml**.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   A variável de ambiente `SecretPath` apontará para o diretório onde todos os segredos são armazenados. Cada parâmetro listado na seção `testsecrets` é armazenado em um arquivo separado. O aplicativo agora pode usar o segredo da seguinte maneira:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Monte os segredos em um contêiner. A única alteração necessária para tornar os segredos disponíveis dentro do contêiner é `specify` um ponto de montagem no `<ConfigPackage>`.
O trecho a seguir é o **ApplicationManifest. xml**modificado.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   Os segredos estão disponíveis no ponto de montagem dentro de seu contêiner.

1. Você pode associar um segredo a uma variável de ambiente de processo especificando `Type='SecretsStoreRef`. O trecho a seguir é um exemplo de como associar a versão de `supersecret` `ver1` à variável de ambiente `MySuperSecret` no **manifesto. xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a [segurança de aplicativos e serviços](service-fabric-application-and-service-security.md).
