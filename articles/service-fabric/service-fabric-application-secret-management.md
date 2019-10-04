---
title: Gerenciar segredos do aplicativo Service Fabric do Azure | Microsoft Docs
description: Saiba como proteger valores secretos em um aplicativo Service Fabric (independente de plataforma).
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 9854ad7118684e1a5e57b0809d733d812ad64176
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828840"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Gerenciar segredos criptografados em aplicativos Service Fabric
Este guia orienta você pelas etapas de gerenciamento de segredos em um aplicativo Service Fabric. Os segredos podem ser qualquer informação confidencial, como cadeias de conexão de armazenamento, senhas ou outros valores que não devem ser manipulados em texto sem formatação.

O uso de segredos criptografados em um aplicativo Service Fabric envolve três etapas:
* Configurar um certificado de criptografia e criptografar segredos.
* Especifique segredos criptografados em um aplicativo.
* Descriptografar segredos criptografados do código de serviço.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Configurar um certificado de criptografia e criptografar segredos
Configurar um certificado de criptografia e usá-lo para criptografar segredos varia entre o Windows e o Linux.
* [Configure um certificado de criptografia e criptografe segredos em clusters do Windows.][secret-management-windows-specific-link]
* [Configure um certificado de criptografia e criptografe segredos em clusters do Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Especificar segredos criptografados em um aplicativo
A etapa anterior descreve como criptografar um segredo com um certificado e produzir uma cadeia de caracteres codificada em base 64 para uso em um aplicativo. Essa cadeia de caracteres codificada em base 64 pode ser especificada como um [parâmetro][parameters-link] criptografado no Settings. XML de um serviço ou como uma [variável de ambiente][environment-variables-link] criptografada no Service manifest. XML de um serviço.

Especifique um [parâmetro][parameters-link] criptografado no arquivo de configuração Settings. XML do seu serviço com o atributo `IsEncrypted` definido como `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Especifique uma [variável de ambiente][environment-variables-link] criptografada no arquivo Service manifest. XML do serviço com o atributo `Type` definido como `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

Os segredos também podem ser incluídos no seu aplicativo Service Fabric especificando um certificado no manifesto do aplicativo. Adicione um elemento **SecretsCertificate** a **ApplicationManifest. xml** e inclua a impressão digital do certificado desejado.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

### <a name="inject-application-secrets-into-application-instances"></a>Injetar segredos do aplicativo em instâncias do aplicativo
Idealmente, a implantação em ambientes diferentes deve ser a mais automatizada possível. Isso pode ser feito executando a criptografia secreta em um ambiente de compilação e fornecendo os segredos criptografados como parâmetros ao criar instâncias de aplicativo.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Usar parâmetros substituíveis em Settings. xml
O arquivo de configuração Settings. xml permite parâmetros substituíveis que podem ser fornecidos no momento da criação do aplicativo. Use o atributo `MustOverride` em vez de fornecer um valor para um parâmetro:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Para substituir valores em Settings. xml, declare um parâmetro override para o serviço em ApplicationManifest. xml:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Agora, o valor pode ser especificado como um *parâmetro de aplicativo* ao criar uma instância do aplicativo. A criação de uma instância de aplicativo pode ser inserida em C#script usando o PowerShell ou escrita para facilitar a integração em um processo de compilação.

Usando o PowerShell, o parâmetro é fornecido para o comando `New-ServiceFabricApplication` como uma [tabela de hash](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Usando C#o, os parâmetros de aplicativo são especificados em um `ApplicationDescription` como um `NameValueCollection`:

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Descriptografar segredos criptografados do código de serviço
As APIs para acessar [parâmetros][parameters-link] e [variáveis de ambiente][environment-variables-link] permitem a descriptografia fácil de valores criptografados. Como a cadeia de caracteres criptografada contém informações sobre o certificado usado para criptografia, você não precisa especificar o certificado manualmente. O certificado precisa ser instalado apenas no nó em que o serviço está sendo executado.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a [segurança de aplicativos e serviços](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
