---
title: Gerir segredos de aplicação de tecido de serviço Azure
description: Saiba como garantir valores secretos numa aplicação de Tecido de Serviço (plataforma-agnóstico).
ms.topic: conceptual
ms.date: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a11869c3b606ed9e74ce4f598109139fa1bb4164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89012828"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Gerir segredos encriptados em aplicações de Tecido de Serviço
Este guia acompanha-o através dos passos de gestão de segredos numa aplicação de Tecido de Serviço. Os segredos podem ser qualquer informação sensível, como cadeias de ligação de armazenamento, palavras-passe ou outros valores que não devem ser tratados em texto simples.

A utilização de segredos encriptados numa aplicação de Tecido de Serviço envolve três etapas:
* Crie um certificado de encriptação e criptografe segredos.
* Especifique segredos encriptados numa aplicação.
* Desencriptam segredos encriptados do código de serviço.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Crie um certificado de encriptação e criptografe segredos
A criação de um certificado de encriptação e a sua utilização para encriptar segredos varia entre o Windows e o Linux.
* [Crie um certificado de encriptação e criptografe segredos em clusters Windows.][secret-management-windows-specific-link]
* [Crie um certificado de encriptação e criptografe segredos em clusters Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Especificar segredos encriptados numa aplicação
O passo anterior descreve como encriptar um segredo com um certificado e produzir uma cadeia codificada base-64 para uso numa aplicação. Esta cadeia codificada base-64 pode ser especificada como um [parâmetro][parameters-link] encriptado na Settings.xml de um serviço ou como uma [variável][environment-variables-link] de ambiente encriptado na ServiceManifest.xml de um serviço.

Especifique um [parâmetro][parameters-link] encriptado no ficheiro de configuração Settings.xml do seu serviço com o `IsEncrypted` atributo definido para `true` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Especifique uma [variável de ambiente][environment-variables-link] encriptada no ficheiro ServiceManifest.xml do seu serviço com o `Type` atributo definido `Encrypted` para:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

Os segredos também devem ser incluídos na sua aplicação Service Fabric especificando um certificado no manifesto de inscrição. Adicione um elemento **SecretsCertificate** à **ApplicationManifest.xml** e inclua a impressão digital do certificado pretendido.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
> [!NOTE]
> Ao ativar uma aplicação que especifica um SecretsCertificate, o Service Fabric encontrará o certificado de correspondência e concederá a identidade que o pedido está a executar sob permissões completas para a chave privada do certificado. O Service Fabric também monitorizará o certificado para alterações e reencaminho as permissões em conformidade. Para detetar alterações nos certificados declarados pelo nome comum, o Service Fabric executa uma tarefa periódica que encontra todos os certificados correspondentes e compara-o com uma lista de impressões digitais em cache. Quando uma nova impressão digital é detetada, significa que um certificado por esse sujeito foi renovado. A tarefa é executado uma vez por minuto em cada nó do cluster.
>
> Embora o SecretsCertificate permita declarações baseadas no assunto, note que as definições encriptadas estão ligadas ao par de chaves que foi usado para encriptar a definição no cliente. Deve certificar-se de que o certificado de encriptação original (ou equivalente) corresponde à declaração baseada no assunto e que está instalado, incluindo a sua chave privada correspondente, em cada nó do cluster que possa acolher a aplicação. Todos os certificados válidos pelo tempo que correspondem à declaração baseada no assunto e construídos a partir do mesmo par-chave que o certificado de encriptação original são considerados equivalentes.
>

### <a name="inject-application-secrets-into-application-instances"></a>Injetar segredos de aplicação em instâncias de aplicação
Idealmente, a implantação em diferentes ambientes deve ser o mais automatizada possível. Isto pode ser realizado executando encriptação secreta em um ambiente de construção e fornecendo os segredos encriptados como parâmetros ao criar instâncias de aplicação.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Utilize parâmetros dominantes em Settings.xml
O ficheiro de configuração Settings.xml permite parâmetros sobrepúveis que podem ser fornecidos no tempo de criação da aplicação. Utilize o `MustOverride` atributo em vez de fornecer um valor para um parâmetro:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Para anular os valores em Settings.xml, declare um parâmetro de sobreposição para o serviço em ApplicationManifest.xml:

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

Agora o valor pode ser especificado como um parâmetro de *aplicação* ao criar uma instância da aplicação. A criação de uma instância de aplicação pode ser escrita usando o PowerShell, ou escrito em C#, para facilitar a integração num processo de construção.

Utilizando o PowerShell, o parâmetro é fornecido ao `New-ServiceFabricApplication` comando como uma tabela de [haxixe:](/previous-versions/windows/it-pro/windows-powershell-1.0/ee692803(v=technet.10))

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Utilizando C#, os parâmetros de aplicação são especificados num `ApplicationDescription` `NameValueCollection` como:

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
As APIs para aceder a [parâmetros][parameters-link] e [variáveis ambientais][environment-variables-link] permitem uma fácil desencriptação dos valores encriptados. Uma vez que o string encriptado contém informações sobre o certificado utilizado para encriptação, não é necessário especificar manualmente o certificado. O certificado só precisa de ser instalado no nó onde o serviço está a funcionar.

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
* Loja [de Segredos](service-fabric-application-secret-store.md) de Tecido de Serviço 
* Saiba mais sobre [aplicação e segurança de serviços](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md
