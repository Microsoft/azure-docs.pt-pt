---
title: Configurar certificados para aplicativos de Service Fabric do Azure no Linux | Microsoft Docs
description: Configurar certificados para seu aplicativo com o tempo de execução de Service Fabric em um cluster do Linux
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2019
ms.author: chackdan
ms.openlocfilehash: 9599d59f7f23de4e54ce323aa4a2ad837d8ed074
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773261"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certificados e segurança em clusters do Linux

Este artigo fornece informações sobre como configurar certificados X. 509 em clusters do Linux.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Local e formato de certificados X. 509 em nós do Linux

Service Fabric geralmente espera que os certificados X. 509 estejam presentes no diretório */var/lib/sfcerts* em nós de cluster do Linux. Isso é verdadeiro para certificados de cluster, certificados de cliente, etc. Em alguns casos, você pode especificar um local diferente da pasta *var/lib/sfcerts* para certificados. Por exemplo, com Reliable Services criados usando o SDK do Java do Service Fabric, você pode especificar um local diferente por meio do pacote de configuração (Settings. xml) para alguns certificados específicos do aplicativo. Para saber mais, consulte [certificados referenciados no pacote de configuração (Settings. xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

Para clusters do Linux, Service Fabric espera que os certificados estejam presentes como um arquivo. pem que contenha o certificado e a chave privada ou como um arquivo. CRT que contenha o certificado e um arquivo. Key que contenha a chave privada. Todos os arquivos devem estar no formato PEM. 

Se você instalar o certificado de Azure Key Vault usando um modelo do [Resource Manager](./service-fabric-cluster-creation-create-template.md) ou comandos do [PowerShell](https://docs.microsoft.com/powershell/module/az.servicefabric/?view=azps-2.6.0) , o certificado será instalado no formato correto no diretório */var/lib/sfcerts* em cada nó. Se você instalar um certificado por meio de outro método, deverá verificar se o certificado está instalado corretamente em nós de cluster.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certificados referenciados no manifesto do aplicativo

Os certificados especificados no manifesto do aplicativo, por exemplo, por meio dos elementos [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) ou [**EndpointCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element) , devem estar presentes no diretório */var/lib/sfcerts* . Os elementos que são usados para especificar certificados no manifesto do aplicativo não usam um atributo Path, portanto, os certificados devem estar presentes no diretório padrão. Esses elementos usam um atributo opcional **X509StoreName** . O padrão é "My", que aponta para o diretório */var/lib/sfcerts* em nós do Linux. Qualquer outro valor é indefinido em um cluster do Linux. É recomendável omitir o atributo **X509StoreName** para aplicativos que são executados em clusters do Linux. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certificados referenciados no pacote de configuração (Settings. xml)

Para alguns serviços, você pode configurar certificados X. 509 no [ConfigPackage](./service-fabric-application-and-service-manifests.md) (por padrão, Settings. xml). Por exemplo, esse é o caso quando você declara certificados usados para proteger canais RPC para Reliable Services serviços criados com os SDKs Service Fabric .NET Core ou Java. Há duas maneiras de fazer referência a certificados no pacote de configuração. O suporte varia entre os SDKs do .NET Core e do Java.

### <a name="using-x509-securitycredentialstype"></a>Usando o X509 SecurityCredentialsType

Com os SDKs .NET ou Java, você pode especificar o **X509** para o **SecurityCredentialsType**. Isso corresponde `X509Credentials` ao[tipo](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/(.NET[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)) de `SecurityCredentials` ([.net](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)).

A referência **X509** localiza o certificado em um repositório de certificados. O XML a seguir mostra os parâmetros usados para especificar o local do certificado:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Para um serviço em execução no Linux, a **LocalMachine**/**aponta para** o local padrão para certificados, o diretório */var/lib/sfcerts* . Para o Linux, todas as outras combinações de **CertificateStoreLocation** e **CertificateStoreName** são indefinidas. 

Sempre especifique **LocalMachine** para o parâmetro **CertificateStoreLocation** . Não é necessário especificar o parâmetro **CertificateStoreName** porque o padrão é "My". Com uma referência **X509** , os arquivos de certificado devem estar localizados no diretório */var/lib/sfcerts* no nó do cluster.  

O XML a seguir mostra uma seção **TransportSettings** com base neste estilo:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x509_2-securitycredentialstype"></a>Usando X509_2 SecurityCredentialsType

Com o SDK do Java, você pode especificar **X509_2** para **SecurityCredentialsType**. Isso corresponde ao `X509Credentials2` tipo ([Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2)) de `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)). 

Com uma referência de **X509_2** , você especifica um parâmetro de caminho, para que você possa localizar o certificado em um diretório diferente de */var/lib/sfcerts*.  O XML a seguir mostra os parâmetros usados para especificar o local do certificado: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

O XML a seguir mostra uma seção **TransportSettings** com base nesse estilo.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> O certificado é especificado como um arquivo. CRT no XML anterior. Isso implica que também há um arquivo. Key que contém a chave privada no mesmo local.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Configurar um aplicativo Reliable Services para ser executado em clusters do Linux

Os SDKs de Service Fabric permitem que você se comunique com as APIs de tempo de execução de Service Fabric para aproveitar a plataforma. Ao executar qualquer aplicativo que usa essa funcionalidade em clusters Linux seguros, você precisa configurar seu aplicativo com um certificado que ele possa usar para validar com o tempo de execução de Service Fabric. Os aplicativos que contêm Service Fabric serviços de serviços confiáveis escritos usando o .NET Core ou os SDKs Java exigem essa configuração. 

Para configurar um aplicativo, adicione um elemento [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) sob a marca **certificados** , que está localizada sob a marca **ApplicationManifest** no arquivo *ApplicationManifest. xml* . O XML a seguir mostra um certificado referenciado por sua impressão digital: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Você pode fazer referência ao certificado de cluster ou a um certificado que você instala em cada nó de cluster. No Linux, os arquivos de certificado devem estar presentes no diretório */var/lib/sfcerts* Para saber mais, confira [localização e formato dos certificados X. 509 em nós do Linux](#location-and-format-of-x509-certificates-on-linux-nodes).



