---
title: Gerir os segredos da aplicação do Tecido de Serviço Azure
description: Saiba como garantir valores secretos numa aplicação de Tecido de Serviço (plataforma-agnóstica).
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 4a489993f982993d5703a9b46d42fffaa6134038
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259061"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Gerir segredos encriptados em aplicações de Tecido de Serviço
Este guia acompanha-o através dos passos de gestão de segredos numa aplicação de Tecido de Serviço. Os segredos podem ser qualquer informação sensível, como cordas de ligação de armazenamento, palavras-passe ou outros valores que não devem ser tratados em texto simples.

A utilização de segredos encriptados numa aplicação de Tecido de Serviço envolve três passos:
* Configurar um certificado de encriptação e encriptar segredos.
* Especifique segredos encriptados numa aplicação.
* Desencriptar segredos encriptados do código de serviço.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Configurar um certificado de encriptação e encriptar segredos
A criação de um certificado de encriptação e a sua utilização para encriptar segredos varia entre windows e Linux.
* [Configurar um certificado de encriptação e encriptar segredos em clusters do Windows.][secret-management-windows-specific-link]
* [Configurar um certificado de encriptação e encriptar segredos em clusters Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Especificar segredos encriptados numa aplicação
O passo anterior descreve como encriptar um segredo com um certificado e produzir uma cadeia codificada base-64 para ser usada numa aplicação. Esta cadeia codificada base-64 pode ser especificada como [um parâmetro][parameters-link] encriptado nas Definições.xml de um serviço ou como uma [variável ambiente][environment-variables-link] encriptada no ServiceManifest.xml de um serviço.

Especifique um [parâmetro][parameters-link] encriptado no ficheiro de configuração Definições.xml do seu serviço com o conjunto de atributo `IsEncrypted` para `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Especifique uma [variável ambiente][environment-variables-link] encriptada no ficheiro ServiceManifest.xml do seu serviço com o conjunto de atributo `Type` para `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

Os segredos também devem ser incluídos na sua aplicação Service Fabric, especificando um certificado no manifesto de candidatura. Adicione um elemento **SecretsCertificate** ao **ApplicationManifest.xml** e inclua a impressão digital do certificado pretendido.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

### <a name="inject-application-secrets-into-application-instances"></a>Injete segredos de aplicação em instâncias de aplicação
Idealmente, a implantação para diferentes ambientes deve ser o mais automatizada possível. Isto pode ser realizado executando encriptação secreta em um ambiente de construção e fornecendo os segredos encriptados como parâmetros ao criar instâncias de aplicação.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Utilize parâmetros muito ridíveis em Definições.xml
O ficheiro de configuração Definições.xml permite parâmetros sobre-verriáveis que podem ser fornecidos no momento da criação da aplicação. Utilize o atributo `MustOverride` em vez de fornecer um valor para um parâmetro:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Para sobrepor os valores em Definições.xml, declare um parâmetro de sobreposição para o serviço em ApplicationManifest.xml:

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

Agora o valor pode ser especificado como parâmetro de *aplicação* ao criar uma instância da aplicação. A criação de uma instância de aplicação C#pode ser escrita usando powerShell, ou escrita em, para uma fácil integração num processo de construção.

Utilizando o PowerShell, o parâmetro é fornecido ao comando `New-ServiceFabricApplication` como [uma tabela de hash:](https://technet.microsoft.com/library/ee692803.aspx)

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Utilização, C#os parâmetros de aplicação são especificados num `ApplicationDescription` como `NameValueCollection`:

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

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Desencriptar segredos encriptados do código de serviço
As APIs para aceder a [parâmetros][parameters-link] e [variáveis ambientais][environment-variables-link] permitem uma desencriptação fácil de valores encriptados. Uma vez que a cadeia encriptada contém informações sobre o certificado utilizado para encriptação, não precisa de especificar manualmente o certificado. O certificado só precisa de ser instalado no nó em que o serviço está a funcionar.

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
* Loja de [Segredos de](service-fabric-application-secret-store.md) Tecido de Serviço 
* Saiba mais sobre [a aplicação e segurança](service-fabric-application-and-service-security.md) do serviço

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md