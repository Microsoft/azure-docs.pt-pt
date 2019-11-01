---
title: Service Fabric do Azure – usando Service Fabric referências de keyvault de aplicativo | Microsoft Docs
description: Este artigo explica como usar o suporte do Service-Fabric KeyVaultReference para segredos do aplicativo.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 09/20/2019
ms.author: atsenthi
ms.openlocfilehash: b0f1a081727721ea0325276cf9edd52c6d71fb6b
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243863"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Suporte do KeyVaultReference para aplicativos Service Fabric (versão prévia)

Um desafio comum ao criar aplicativos em nuvem é como armazenar com segurança os segredos exigidos pelo seu aplicativo. Por exemplo, talvez você queira armazenar as credenciais do repositório de contêiner no keyvault e fazer referência a ela no manifesto do aplicativo. Service Fabric KeyVaultReference usa Service Fabric identidade gerenciada e facilita a referência a segredos do keyvault. O restante deste artigo fornece detalhes sobre como usar Service Fabric KeyVaultReference e inclui alguns usos típicos.

## <a name="prerequisites"></a>Pré-requisitos

- Identidade gerenciada para o aplicativo (MIT)
    
    Service Fabric suporte a KeyVaultReference usa a identidade gerenciada do aplicativo e, portanto, os aplicativos que planejam usar o KeyVaultReferences devem usar a identidade gerenciada. Siga este [documento](concepts-managed-identity.md) para habilitar a identidade gerenciada para seu aplicativo.

- CSS (repositório de segredos central).

    O repositório de segredos central (CSS) é o cache de segredos locais criptografados do Service Fabric, KeyVaultReference uma vez que o fetche é armazenado em cache em CSS.

    Adicione o abaixo à sua configuração de cluster em `fabricSettings` para habilitar todos os recursos necessários para o suporte do KeyVaultReference.

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
                }
                ],
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > É recomendável usar um certificado de criptografia separado para o CSS. Você pode adicioná-lo na seção "CentralSecretService".

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```

- Conceder permissão de acesso de identidade gerenciada do aplicativo para o keyvault

    Referencie este [documento](how-to-grant-access-other-resources.md) para ver como conceder acesso de identidade gerenciada ao keyvault. Observe também que se você estiver usando a identidade gerenciada atribuída pelo sistema, a identidade gerenciada será criada somente após a implantação do aplicativo.

## <a name="keyvault-secret-as-application-parameter"></a>Segredo do keyvault como parâmetro de aplicativo
Digamos que o aplicativo precise ler a senha do banco de dados de back-end armazenada no keyvault, Service Fabric suporte a KeyVaultReference o torna mais fácil. O exemplo abaixo lê `DBPassword` segredo do keyvault usando o suporte a Service Fabric KeyVaultReference.

- Adicionar uma seção a Settings. xml

    Defina `DBPassword` parâmetro com o tipo `KeyVaultReference` e o valor `<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Referencie a nova seção em ApplicationManifest. xml no `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Usando o KeyVaultReference em seu aplicativo

    Service Fabric na instanciação do serviço resolverá o parâmetro KeyVaultReference usando a identidade gerenciada do aplicativo. Cada parâmetro listado em `<Section  Name=dbsecrets>` será um arquivo na pasta apontada por EnvironmentVariable SecretPath. O C# trecho de código abaixo mostra como ler DBPassword em seu aplicativo.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Para o cenário de contêiner, você pode usar o ponto de montagem para controlar onde o `secrets` será montado.

## <a name="keyvault-secret-as-environment-variable"></a>Segredo do keyvault como variável de ambiente

Service Fabric variáveis de ambiente agora dão suporte ao tipo KeyVaultReference, abaixo o exemplo mostra como associar uma variável de ambiente a um segredo armazenado no keyvault.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Segredo do keyvault como senha do repositório de contêiner
KeyVaultReference é um tipo com suporte para o contêiner RepositoryCredentials, o exemplo abaixo mostra como usar uma referência de keyvault como senha do repositório de contêiner.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>FAQ
- A identidade gerenciada precisa ser habilitada para o suporte do KeyVaultReference, a ativação do aplicativo falhará se KeyVaultReference for usado sem habilitar a identidade gerenciada.

- Se você estiver usando a identidade atribuída pelo sistema, ela será criada somente depois que o aplicativo for implantado e isso criará uma dependência circular. Depois que o aplicativo for implantado, você poderá conceder à permissão de acesso de identidade atribuída pelo sistema ao keyvault. Você pode encontrar a identidade atribuída pelo sistema por nome {cluster}/{Application Name}/{ServiceName}

- O keyvault precisa estar na mesma assinatura que o cluster do Service Fabric. 

## <a name="next-steps"></a>Passos seguintes

* [Documentação do Azure keyvault](https://docs.microsoft.com/azure/key-vault/)
