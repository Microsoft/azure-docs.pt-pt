---
title: Configure certificados para candidaturas no Linux
description: Configure certificados para a sua aplicação com o tempo de execução do Tecido de Serviço num cluster Linux
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: 802e76614f51e1f6479a311e61a49d83b8125546
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282578"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certificados e segurança nos clusters Linux

Este artigo fornece informações sobre a configuração de certificados X.509 em clusters Linux.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Localização e formato de certificados X.509 em nódeos Linux

Service Fabric geralmente espera que os certificados X.509 estejam presentes no diretório */var/lib/sfcerts* nos nós do cluster Linux. Isto é verdade em certificados de cluster, certificados de cliente, etc. Em alguns casos, pode especificar uma localização diferente da pasta *var/lib/sfcerts* para certificados. Por exemplo, com serviços fiáveis construídos utilizando o Tecido de Serviço Java SDK, pode especificar uma localização diferente através do pacote de configuração (Definições.xml) para alguns certificados específicos da aplicação. Para saber mais, consulte [os Certificados referenciados no pacote de configuração (Definições.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

Para os clusters Linux, o Service Fabric espera que os certificados estejam presentes como um ficheiro .pem que contenha tanto o certificado como a chave privada ou como um ficheiro .crt que contenha o certificado e um ficheiro .key que contenha a chave privada. Todos os ficheiros devem estar em formato PEM. 

Se instalar o seu certificado a partir do Cofre de Chaves Azure utilizando um modelo de Gestor de [Recursos](./service-fabric-cluster-creation-create-template.md) ou comandos [PowerShell,](https://docs.microsoft.com/powershell/module/az.servicefabric/?view=azps-2.6.0) o certificado é instalado no formato correto no diretório */var/lib/sfcerts* em cada nó. Se instalar um certificado através de outro método, deve certificar-se de que o certificado está corretamente instalado nos nós do cluster.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certificados referenciados no manifesto de candidatura

Os certificados especificados no manifesto de candidatura, por exemplo, através dos elementos [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) ou [**EndpointCertificate,** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element) devem estar presentes no diretório */var/lib/sfcerts.* Os elementos utilizados para especificar certificados no manifesto de candidatura não têm um atributo de caminho, pelo que os certificados devem estar presentes no diretório predefinido. Estes elementos têm um atributo opcional **X509StoreName.** O padrão é "My", que aponta para o diretório */var/lib/sfcerts* nos nós linux. Qualquer outro valor é indefinido num cluster Linux. Recomendamos que omita o atributo **X509StoreName** para aplicações que funcionam em clusters Linux. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certificados referenciados no pacote de configuração (Definições.xml)

Para alguns serviços, pode configurar certificados X.509 no [ConfigPackage](./service-fabric-application-and-service-manifests.md) (por predefinição, Definições.xml). Por exemplo, é o caso quando declara certificados usados para garantir canais RPC para serviços fiáveis construídos com o Service Fabric .NET Core ou Java SDKs. Existem duas formas de referência de certificados no pacote de configuração. O suporte varia entre o Núcleo .NET e os SDKs de Java.

### <a name="using-x509-securitycredentialstype"></a>Utilização de Credenciais de Segurança X509

WIth os SDKs .NET ou Java, pode especificar **X509** para o Tipo de **Credenciais**de Segurança . Isto corresponde ao tipo `X509Credentials`[(.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)) `SecurityCredentials`[(.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java).](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)

A referência **X509** localiza o certificado numa loja de certificados. O XML seguinte mostra os parâmetros utilizados para especificar a localização do certificado:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Para um serviço em execução no Linux, **LocalMachine**/**Os meus** pontos para a localização padrão dos certificados, o diretório */var/lib/sfcerts.* Para o Linux, quaisquer outras combinações de **CertificateStoreLocation** e **CertificateStoreName** estão indefinidas. 

Especifique sempre **a LocalMachine** para o parâmetro **CertificateStoreLocation.** Não há necessidade de especificar o parâmetro **CertificateStoreName** porque não se aplica a "My". Com referência **X509,** os ficheiros de certificado devem estar localizados no diretório */var/lib/sfcerts* no nó do cluster.  

O XML seguinte mostra uma secção **TransportSettings** com base neste estilo:

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

### <a name="using-x509_2-securitycredentialstype"></a>Utilização X509_2 Credenciais de segurançaType

Com o Java SDK, pode especificar **X509_2** para o Tipo de **Credenciais**de Segurança . Isto corresponde ao tipo `X509Credentials2`[(Java)](https://docs.microsoft.com/java/api/system.fabric.x509credentials2)tipo `SecurityCredentials`[(Java).](https://docs.microsoft.com/java/api/system.fabric.securitycredentials) 

Com uma **referência X509_2,** especifice um parâmetro de percurso, para que possa localizar o certificado num diretório diferente do */var/lib/sfcerts*.  O XML seguinte mostra os parâmetros utilizados para especificar a localização do certificado: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

O XML seguinte mostra uma secção **TransportSettings** baseada neste estilo.

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
> O certificado é especificado como um ficheiro .crt no XML anterior. Isto implica que existe também um ficheiro .key contendo a chave privada no mesmo local.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Configure uma aplicação de Serviços Fiáveis para funcionar em clusters Linux

Os SDKs de Tecido de Serviço permitem-lhe comunicar com as APIs de tempo de execução do Tecido de Serviço para alavancar a plataforma. Quando executa qualquer aplicação que utilize esta funcionalidade em clusters Linux seguros, precisa configurar a sua aplicação com um certificado que possa usar para validar com o tempo de funcionamento do Tecido de Serviço. Aplicações que contenham serviços de serviço fidedigno de tecido de serviço escritos usando os SDKs .NET Core ou Java requerem esta configuração. 

Para configurar uma aplicação, adicione um elemento [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) sob a etiqueta **Certificates,** que está localizado sob a etiqueta **ApplicationManifest** no ficheiro *ApplicationManifest.xml.* O xML seguinte mostra um certificado referenciado pela sua impressão digital: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Pode fazer referência ao certificado de cluster ou a um certificado que instala em cada nó de cluster. No Linux, os ficheiros de certificado devem estar presentes no diretório */var/lib/sfcerts.* Para saber mais, consulte [localização e formato de certificados X.509 nos nós linux](#location-and-format-of-x509-certificates-on-linux-nodes).



