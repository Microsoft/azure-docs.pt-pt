---
title: Gerir segredos da aplicação do Azure Service Fabric | Documentos da Microsoft
description: Saiba como proteger os valores secretos num aplicativo de Service Fabric (independente de plataforma).
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 77aa6d48f499a94f8fe2f3ef4e2444a34d3b3176
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894738"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Gerir os segredos encriptados em aplicações do Service Fabric
Este guia explica os passos para gerir segredos na aplicação do Service Fabric. Segredos podem ser qualquer informações confidenciais, como cadeias de ligação de armazenamento, as palavras-passe ou outros valores que não devem ser processados em texto simples.

Usando os segredos encriptados num aplicativo de Service Fabric envolve três etapas:
* Configurar um certificado de encriptação e encriptar os segredos.
* Especifique os segredos encriptados num aplicativo.
* Desencriptar os segredos encriptados a partir do código de serviço.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Configurar um certificado de encriptação e encriptar os segredos
Configurar um certificado de encriptação e utilizá-la para encriptar os segredos variam entre Windows e Linux.
* [Configurar um certificado de encriptação e encriptar os segredos em clusters do Windows.][secret-management-windows-specific-link]
* [Configurar um certificado de encriptação e encriptar os segredos em clusters do Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Especifique os segredos encriptados num aplicativo
O passo anterior descreve como encriptar um segredo com um certificado e produzir uma cadeia codificada de base 64 para uso num aplicativo. Esta cadeia com codificação base 64 pode ser especificada um encriptadas [parâmetro] [ parameters-link] em Settings de um serviço ou um encriptadas [variável de ambiente] [ environment-variables-link] no servicemanifest. XML de um serviço.

Especifique um encriptado [parâmetro] [ parameters-link] no ficheiro de configuração de Settings do seu serviço com o `IsEncrypted` atributo definido como `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Especifique um encriptado [variável de ambiente] [ environment-variables-link] no ficheiro servicemanifest. XML de seu serviço com o `Type` atributo definido como `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

### <a name="inject-application-secrets-into-application-instances"></a>Inserir os segredos da aplicação nas instâncias da aplicação
O ideal é que a implantação em ambientes diferentes deve ser o mais automatizada possível. Pode fazê-ao efetuar a encriptação secreta num ambiente de compilação e ao fornecer os segredos encriptados como parâmetros durante a criação de instâncias da aplicação.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Utilizar os parâmetros substituíveis em Settings
O ficheiro de configuração Settings permite que os parâmetros substituíveis, que podem ser fornecidos no momento da criação de aplicativos. Utilize o `MustOverride` atributo em vez de fornecer um valor para um parâmetro:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Para substituir valores em Settings, declare um parâmetro de substituição para o serviço no applicationmanifest. XML:

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

Agora o valor pode ser especificado como um *parametr aplikace* durante a criação de uma instância da aplicação. Criar uma instância de aplicação pode ser colocado em script com o PowerShell, ou por escrito em C#, para uma integração fácil num processo de compilação.

Com o PowerShell, o parâmetro é fornecido para o `New-ServiceFabricApplication` comando como uma [tabela de hash](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Com o C#, os parâmetros de aplicação estão especificados numa `ApplicationDescription` como uma `NameValueCollection`:

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

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Desencriptar os segredos encriptados a partir do código de serviço
As APIs para acessar [parâmetros] [ parameters-link] e [variáveis de ambiente] [ environment-variables-link] permitem a descriptografia fácil de valores encriptados. Uma vez que a cadeia encriptada contém informações sobre o certificado utilizado para encriptação, não é necessário especificar manualmente o certificado. O certificado apenas tem de ser instalado no nó que o serviço está em execução.

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

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [aplicações e segurança de serviço](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
