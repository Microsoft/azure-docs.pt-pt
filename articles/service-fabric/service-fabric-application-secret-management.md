---
title: Gerir segredos de aplicação de Service Fabric do Azure | Microsoft Docs
description: Saiba como proteger valores secretos numa aplicação de Service Fabric.
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
ms.date: 03/21/2018
ms.author: vturecek
ms.openlocfilehash: 85eb1cd40986bd6fb83c80a274046bbae3756b7e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295458"
---
# <a name="manage-secrets-in-service-fabric-applications"></a>Gerir os segredos em aplicações de Service Fabric
Este guia explica os passos de gestão de segredos numa aplicação de Service Fabric. Os segredos podem ser qualquer informações confidenciais, tais como cadeias de ligação de armazenamento, as palavras-passe ou outros valores que não devem ser processados em texto simples.

[O Cofre de chaves do Azure] [ key-vault-get-started] é aqui utilizada como uma localização de armazenamento seguro para certificados e como uma forma de obter certificados instalados em clusters de Service Fabric no Azure. Se não estiver a implementar no Azure, não terá de utilizar o Cofre de chaves para gerir os segredos em aplicações de Service Fabric. No entanto, *utilizando* segredos numa aplicação está na nuvem plataforma agnóstica para permitir que as aplicações implementadas para um cluster alojado em qualquer lugar. 

## <a name="obtain-a-data-encipherment-certificate"></a>Obter um certificado de encriptação de dados
Um certificado de encriptação de dados é utilizado estritamente para a encriptação e desencriptação da configuração de valores em Settings.xml de um serviço e não é utilizado para autenticação ou assinatura de texto de cifra. O certificado tem de cumprir os seguintes requisitos:

* O certificado tem de conter uma chave privada.
* O certificado tem de ser criado para a troca de chaves, exportável para um ficheiro Personal Information Exchange (. pfx).
* A utilização de chave do certificado tem de incluir a encriptação de dados (10) e não deve incluir a autenticação de servidor ou a autenticação de cliente. 
  
  Por exemplo, quando criar um certificado autoassinado com o PowerShell, o `KeyUsage` sinalizador deve ser definido `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalar o certificado do cluster
Este certificado tem de ser instalado em cada nó no cluster. Será utilizado no tempo de execução para desencriptar valores armazenados em Settings.xml de um serviço. Consulte [como criar um cluster com o Azure Resource Manager] [ service-fabric-cluster-creation-via-arm] para obter instruções de configuração. 

## <a name="encrypt-application-secrets"></a>Encriptar os segredos de aplicação
Quando implementar uma aplicação, encriptar o segredo valores com o certificado e inseri-los no ficheiro de configuração de Settings.xml de um serviço. O SDK de Service Fabric tem funções incorporadas de encriptação e desencriptação de secretas. Segredo valores podem ser encriptados no momento da compilação e, em seguida, desencriptados e ler programaticamente com código do serviço. 

O seguinte comando do PowerShell é utilizado para encriptar um segredo. Este comando só encripta o valor; faz **não** assinar o texto de cifra. Tem de utilizar o mesmo certificado de encriptação está instalado no seu cluster para produzir o ficheiro de encriptação para os valores de segredos:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

A cadeia codificada resultante da base-64 contém tanto o segredo ficheiro de encriptação, bem como informações sobre o certificado que foi utilizado para encriptar.  A cadeia com codificação base 64 pode ser inserida no parâmetro no ficheiro de configuração do seu serviço Settings.xml com o `IsEncrypted` atributo definido como `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```

### <a name="inject-application-secrets-into-application-instances"></a>Inserir segredos de aplicação no instâncias da aplicação
Idealmente, a implementação em ambientes diferentes deve ser como automatizada quanto possível. Isto pode ser efetuado ao efetuar a encriptação secreta num ambiente de compilação e fornecer os segredos encriptados como parâmetros ao criar instâncias da aplicação.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Utilizar parâmetros substituíveis em Settings.xml
O ficheiro de configuração Settings.xml permite substituíveis parâmetros que podem ser fornecidos no momento de criação de aplicações. Utilize o `MustOverride` atributo em vez de fornecer um valor para um parâmetro:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Para substituir valores em Settings.xml, declare um parâmetro de substituição para o serviço no ApplicationManifest.xml:

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

Agora pode ser especificado o valor como um *parâmetro de aplicação* quando criar uma instância da aplicação. Criar uma instância de aplicação pode ser convertidos em script com o PowerShell, ou escrito em c#, para integração fácil num processo de compilação.

Utilizar o PowerShell, o parâmetro é fornecido para o `New-ServiceFabricApplication` comando como um [tabela hash](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
PS C:\Users\vturecek> New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Parâmetros de aplicação com c#, estão especificados num `ApplicationDescription` como um `NameValueCollection`:

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

## <a name="decrypt-secrets-from-service-code"></a>Desencriptar segredos do código do serviço
Pode ler valores encriptados fora Settings.xml, desencriptação-los com o certificado de encriptação utilizado para encriptar o segredo. Os serviços no Service Fabric são executados ao abrigo do serviço de rede por predefinição no Windows e não tem acesso a certificados instalados no nó sem alguma configuração adicional.

Quando utilizar um certificado de encriptação de dados, tem de certificar-se de que o serviço de rede ou o serviço da conta de qualquer utilizador está a ser executado tem acesso à chave privada do certificado. Service Fabric processará conceder acesso para o seu serviço automaticamente se o configurar para o fazer. Esta configuração pode ser efetuada no ApplicationManifest.xml definir utilizadores e as políticas de segurança para os certificados. No exemplo seguinte, a conta de serviço de rede é dado acesso de leitura para um certificado definido pelo respetivo thumbprint:

```xml
<ApplicationManifest … >
    <Principals>
        <Users>
            <User Name="Service1" AccountType="NetworkService" />
        </Users>
    </Principals>
  <Policies>
    <SecurityAccessPolicies>
      <SecurityAccessPolicy GrantRights=”Read” PrincipalRef="Service1" ResourceRef="MyCert" ResourceType="Certificate"/>
    </SecurityAccessPolicies>
  </Policies>
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

> [!NOTE]
> Quando copiar um thumbprint de certificado do arquivo de snap-in de certificado no Windows, um caráter invisível é colocado no início da cadeia de thumbprint. Este caráter invisível pode originar um erro ao tentar localizar um certificado ao thumbprint, por isso, certifique-se eliminar este caráter adicional.
> 
> 

### <a name="use-application-secrets-in-service-code"></a>Utilizar segredos de aplicação com código do serviço
Permite que a API para aceder aos valores de configuração de Settings.xml num pacote de configuração para desencriptar fácil dos valores que tenham o `IsEncrypted` atributo definido como `true`. Uma vez que o texto encriptado contém informações sobre o certificado utilizado para encriptação, não precisa de localizar o certificado manualmente. O certificado apenas tem de ser instalado no nó que o serviço está em execução. Chamar simplesmente o `DecryptValue()` método para obter o valor original de segredos:

```csharp
ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");
SecureString mySecretValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue()
```

## <a name="next-steps"></a>Próximos Passos
Saiba mais sobre [aplicações e segurança de serviço](service-fabric-application-and-service-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-secret-management/overview.png
