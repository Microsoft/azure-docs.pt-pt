---
title: Repositório de segredos Service Fabric | Microsoft Docs
description: Este artigo descreve como usar Service Fabric repositório de segredos.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 5315a8806f45e40204e8500e97c3440bfa9ab8b2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077352"
---
#  <a name="service-fabric-secrets-store"></a>Armazenamento de segredos Service Fabric
Este artigo descreve como criar e usar segredos em Service Fabric aplicativos usando o Service Fabric repositório de segredos (CSS). O CSS é um cache de repositório de segredo local, usado para manter dados confidenciais, como senha, tokens e chaves criptografados na memória.

## <a name="enabling-secrets-store"></a>Habilitando o armazenamento de segredos
 Adicione o abaixo à sua configuração de cluster em `fabricSettings` para habilitar o CSS. É recomendável usar um certificado diferente do certificado de cluster para CSS. Verifique se o certificado de criptografia está instalado em todos os nós e `NetworkService` tem permissão de leitura para a chave privada do certificado.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "parameters":  [
            "name":  "CentralSecretService"
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
```
## <a name="declare-secret-resource"></a>Declarar recurso secreto
Você pode criar um recurso secreto usando o modelo do Resource Manager ou usando a API REST.

* Com o modelo do Resource Manager
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
O modelo acima cria `supersecret` recurso secreto, mas nenhum valor é definido para o recurso secreto ainda.

* Utilizar a API REST

Para criar um recurso secreto, `supersecret` fazer uma solicitação PUT para `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Você precisa do certificado do cluster ou do certificado do cliente administrador para criar um segredo.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>Definir valor secreto
* Com o modelo do Resource Manager

O modelo do Resource Manager abaixo cria e define o valor para `supersecret` secreta com a versão `ver1`.
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
* Utilizar a API REST

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>Usando o segredo em seu aplicativo

1.  Adicione uma seção no arquivo Settings. XML com o conteúdo abaixo. Observe aqui que o valor é do formato {`secretname:version`}

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. Agora, importe a seção em ApplicationManifest. xml
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

A variável de ambiente ' SecretPath ' apontará para o diretório onde todos os segredos são armazenados. Cada parâmetro listado na seção `testsecrets` será armazenado em um arquivo separado. O aplicativo agora pode usar o segredo, conforme mostrado abaixo
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. Montando segredos em um contêiner

Somente a alteração necessária para disponibilizar os segredos dentro do contêiner é especificar um MountPoint em `<ConfigPackage>`.
Aqui está o ApplicationManifest. xml modificado  

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
Os segredos estarão disponíveis no ponto de montagem dentro do contêiner.

4. Associando o segredo a uma variável de ambiente 

Você pode associar o segredo a uma variável de ambiente de processo especificando Type = ' SecretsStoreRef '. Aqui está um exemplo de como associar `supersecret` versão `ver1` à variável de ambiente `MySuperSecret` no manifesto. xml.

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a [segurança de aplicativos e serviços](service-fabric-application-and-service-security.md)