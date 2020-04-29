---
title: Loja de Segredos Centrais de Tecido de Serviço Azure
description: Este artigo descreve como usar a Central Secrets Store em Tecido de Serviço Azure.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 4087e7ccdcb2281c4a08af155d35a10c66147a85
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770417"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Central Secrets Store em Tecido de Serviço Azure 
Este artigo descreve como usar a Central Secrets Store (CSS) em Tecido de Serviço Azure para criar segredos em aplicações de Tecido de Serviço. CSS é uma cache de loja secreta local que guarda dados confidenciais, tais como uma palavra-passe, fichas e chaves, encriptadas na memória.

## <a name="enable-central-secrets-store"></a>Ativar a Central Secrets Store
Adicione o seguinte script à `fabricSettings` configuração do cluster para ativar o CSS. Recomendamos que utilize um certificado diferente de um certificado de cluster para CSS. Certifique-se de que o certificado de encriptação `NetworkService` está instalado em todos os nós e que tenha lido permissão para a chave privada do certificado.
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
## <a name="declare-a-secret-resource"></a>Declare um recurso secreto
Pode criar um recurso secreto utilizando a API REST.
  > [!NOTE] 
  > Se o cluster estiver a utilizar a autenticação do windows, o pedido REST é enviado através de um canal HTTP não seguro. A recomendação é usar um cluster baseado em X509 com pontos finais seguros.

Para criar `supersecret` um recurso secreto utilizando a API `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`REST, faça um pedido DE PUT para . Precisa do certificado de cluster ou do certificado de cliente administrativo para criar um recurso secreto.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Definir o valor secreto

Utilize o seguinte script para utilizar a API REST para definir o valor secreto.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Examinar o valor secreto
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Use o segredo na sua aplicação

Siga estes passos para utilizar o segredo na sua aplicação Service Fabric.

1. Adicione uma secção no ficheiro **definições.xml** com o seguinte corte. Note aqui que o valor`secretname:version`está no formato { }.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importar a secção em **ApplicationManifest.xml**.
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

   A variável `SecretPath` ambiental vai apontar para o diretório onde todos os segredos são armazenados. Cada parâmetro listado `testsecrets` na secção é armazenado num ficheiro separado. A aplicação pode agora usar o segredo da seguinte forma:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Monte os segredos num contentor. A única alteração necessária para disponibilizar os segredos dentro do recipiente é a `specify` um ponto de montagem em `<ConfigPackage>`.
O seguinte corte é o **ApplicationManifest.xml**modificado .  

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
   Os segredos estão disponíveis sob o ponto de montagem dentro do seu recipiente.

1. Pode ligar um segredo a uma variável de ambiente de processo, especificando `Type='SecretsStoreRef`. O seguinte corte é um exemplo de `supersecret` `ver1` como ligar `MySuperSecret` a versão à variável ambiental em **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a aplicação e segurança](service-fabric-application-and-service-security.md)do serviço.
