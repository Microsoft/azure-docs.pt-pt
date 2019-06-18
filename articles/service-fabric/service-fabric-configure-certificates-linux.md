---
title: Configurar certificados para aplicações do Azure Service Fabric no Linux | Documentos da Microsoft
description: Configurar certificados para a sua aplicação com o runtime do Service Fabric num cluster do Linux
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: v-jamebr
ms.openlocfilehash: c0580b75544a9613bc8caf2faaac11ba1ba6708e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60881373"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certificados e segurança em clusters do Linux

Este artigo fornece informações sobre como configurar certificados X.509 em clusters do Linux.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Localização e o formato de certificados X.509 em nós do Linux

Service Fabric geralmente espera certificados X.509 estar presente no */var/lib/sfcerts* diretório em nós de cluster do Linux. Isso é verdadeiro de certificados de cluster, certificados de cliente, etc. Em alguns casos, pode especificar uma localização que o *lib/var/sfcerts* pasta para certificados. Por exemplo, com o Reliable Services foi criada com o SDK de Java do Service Fabric, pode especificar uma localização diferente de pacote de configuração (Settings) para alguns certificados específicos de aplicativo. Para obter mais informações, consulte [certificados referenciado no pacote de configuração (Settings)](#certificates-referenced-in-the-configuration-package-settingsxml).

Para clusters do Linux, o Service Fabric espera certificados estar presente como qualquer um de um ficheiro. pem que contém o certificado e a chave privada ou como um ficheiro. crt, que contém o certificado e um arquivo de .key que contém a chave privada. Todos os ficheiros devem estar no formato PEM. 

Se instalar o certificado do Azure Key Vault, utilizando um uma [modelo do Resource Manager](./service-fabric-cluster-creation-create-template.md) ou [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric) comandos, o certificado está instalado no formato correto no */var/ lib/sfcerts* diretório em cada nó. Se instalar um certificado por meio de outro método, deve certificar-se de que o certificado foi instalado corretamente em nós de cluster.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certificados referenciado no manifesto do aplicativo

Certificados especificados no aplicativo do manifesto, por exemplo, através da [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) ou [ **EndpointCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element)elementos, tem de estar presente no */var/lib/sfcerts* diretório. Os elementos que são utilizados para especificar os certificados no manifesto do aplicativo não têm um atributo de caminho, para que os certificados devem estar presentes no diretório padrão. Estes elementos tirar opcional **X509StoreName** atributo. A predefinição é "Meu", que aponta para o */var/lib/sfcerts* diretório em nós do Linux. Qualquer outro valor é indefinido num cluster do Linux. Recomendamos que omitir o **X509StoreName** atributo para aplicações que são executadas em clusters do Linux. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certificados referenciado no pacote de configuração (Settings)

Para alguns serviços, pode configurar certificados X.509 no [ConfigPackage](./service-fabric-application-and-service-manifests.md) (por predefinição, Settings). Por exemplo, este é o caso quando declara certificados usados para proteger os canais RPC para os serviços de Reliable Services criados com os SDKs de Java ou .NET Core do Service Fabric. Existem duas formas de certificados de referência do pacote de configuração. Suporte varia entre os Java SDKs e .NET Core.

### <a name="using-x509-securitycredentialstype"></a>Usando X509 SecurityCredentialsType

Com os Java SDKs ou .NET, pode especificar **X509** para o **SecurityCredentialsType**. Isso corresponde à `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)) o tipo de `SecurityCredentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)).

O **X509** referência localiza o certificado num arquivo de certificados. O XML a seguir mostra os parâmetros utilizados para especificar a localização do certificado:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Para um serviço em execução no Linux, **LocalMachine**/**meu** aponta para a localização predefinida para certificados, o */var/lib/sfcerts* diretório. Para o Linux, quaisquer outras combinações de **CertificateStoreLocation** e **CertificateStoreName** são indefinido. 

Sempre especificar **LocalMachine** para o **CertificateStoreLocation** parâmetro. Não é necessário especificar o **CertificateStoreName** parâmetro porque ele é predefinido para "Meu". Com uma **X509** referência, os ficheiros de certificado tem de estar localizados no */var/lib/sfcerts* diretório no nó de cluster.  

O XML a seguir mostra um **TransportSettings** secção baseia neste estilo:

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

### <a name="using-x5092-securitycredentialstype"></a>Usando X509_2 SecurityCredentialsType

Com o SDK de Java, pode especificar **X509_2** para o **SecurityCredentialsType**. Isso corresponde à `X509Credentials2` ([Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2)) o tipo de `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)). 

Com uma **X509_2** referência, especifica um parâmetro de caminho, para que pode localizar o certificado num diretório diferente de */var/lib/sfcerts*.  O XML a seguir mostra os parâmetros utilizados para especificar a localização do certificado: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

O XML a seguir mostra um **TransportSettings** secção baseia neste estilo.

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
> O certificado é especificado como um ficheiro. crt no XML anterior. Isso implica que existe também um ficheiro de .key que contém a chave privada na mesma localização.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Configurar uma aplicação de Reliable Services para executar em clusters do Linux

Os SDKs do Service Fabric permite-lhe comunicar com o tempo de execução do Service Fabric APIs para aproveitar a plataforma. Ao executar qualquer aplicação que utilize esta funcionalidade em clusters do Linux seguros, terá de configurar a sua aplicação com um certificado que pode ser usado para validar com o tempo de execução do Service Fabric. Aplicativos que contêm os serviços do Service Fabric Reliable Service escritos com os SDKs de Java ou .NET Core exigem esta configuração. 

Para configurar uma aplicação, adicione uma [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) elemento sob o **certificados** etiqueta, que está localizada no **ApplicationManifest**  Etiquetar no *Applicationmanifest* ficheiro. O XML a seguir mostra um certificado referenciado por sua impressão digital: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Pode referenciar o certificado de cluster ou um certificado que instalar em cada nó de cluster. No Linux, os ficheiros de certificado tem de estar presentes no */var/lib/sfcerts* diretório. Para obter mais informações, consulte [local e o formato de certificados X.509 em nós do Linux](#location-and-format-of-x509-certificates-on-linux-nodes).



