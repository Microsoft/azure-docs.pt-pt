---
title: Configure certificados para pedidos no Linux
description: Configure certificados para a sua app com o tempo de execução do Tecido de Serviço num cluster Linux
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: a97c8b8315fe3be405aed9c6570004afb8fafd1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258669"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certificados e segurança em clusters Linux

Este artigo fornece informações sobre a configuração de certificados X.509 em clusters Linux.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Localização e formato de certificados X.509 nos nós Linux

A Service Fabric geralmente espera que os certificados X.509 estejam presentes no diretório */var/lib/sfcerts* nos nós do cluster Linux. Isto aplica-se aos certificados de cluster, certificados de cliente, etc. Em alguns casos, pode especificar um local diferente da pasta *var/lib/sfcerts* para certificados. Por exemplo, com serviços fiáveis construídos utilizando o Service Fabric Java SDK, pode especificar uma localização diferente através do pacote de configuração (Settings.xml) para alguns certificados específicos da aplicação. Para saber mais, consulte [Certificados referenciados no pacote de configuração (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

Para os clusters Linux, o Service Fabric espera que os certificados estejam presentes como um ficheiro .pem que contenha tanto o certificado como a chave privada ou como um ficheiro .crt que contenha o certificado e um ficheiro .key que contenha a chave privada. Todos os ficheiros devem estar no formato PEM. 

Se instalar o seu certificado a partir do Cofre de Chaves Azure utilizando um [modelo de Gestor de Recursos](./service-fabric-cluster-creation-create-template.md) ou comandos [PowerShell,](/powershell/module/az.servicefabric/?view=azps-2.6.0) o certificado é instalado no formato correto no diretório */var/lib/sfcerts* em cada nó. Se instalar um certificado através de outro método, deve certificar-se de que o certificado está corretamente instalado nos nós do cluster.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certificados referenciados no manifesto de pedido

Os certificados especificados no manifesto de aplicação, por exemplo, através dos elementos [**SecretsCertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) ou [**EndpointCertificate,**](./service-fabric-service-model-schema-elements.md#endpointcertificate-element) devem estar presentes no diretório */var/lib/sfcerts.* Os elementos utilizados para especificar certificados no manifesto de pedido não tomam um atributo de caminho, pelo que os certificados devem estar presentes no diretório predefinido. Estes elementos têm um atributo **opcional X509StoreName.** O padrão é "My", que aponta para o diretório */var/lib/sfcerts* nos nós Linux. Qualquer outro valor é indefinido num aglomerado Linux. Recomendamos que omita o atributo **X509StoreName** para aplicações que funcionam em clusters Linux. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certificados referenciados no pacote de configuração (Settings.xml)

Para alguns serviços, pode configurar os certificados X.509 no [ConfigPackage](./service-fabric-application-and-service-manifests.md) (por padrão, Settings.xml). Por exemplo, é o caso quando declara certificados utilizados para garantir canais RPC para serviços de serviços fiáveis construídos com os SDKs de Tecido de Serviço .NET Ou Java. Existem duas formas de referência de certificados no pacote de configuração. O suporte varia entre os SDKs .NET e Java.

### <a name="using-x509-securitycredentialstype"></a>Usando X509 SecurityCredentialsType

WIth os SDKs .NET ou Java, pode especificar **X509** para os **Sistemas de Segurança.** Isto corresponde ao `X509Credentials` tipo[(.NET](/previous-versions/azure/reference/mt124925(v=azure.100)) / [Java)](/java/api/system.fabric.x509credentials)de `SecurityCredentials` [(.NET](/previous-versions/azure/reference/mt124894(v=azure.100)) / [Java](/java/api/system.fabric.securitycredentials)).

A referência **X509** localiza o certificado numa loja de certificados. O seguinte XML mostra os parâmetros utilizados para especificar a localização do certificado:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Para um serviço em execução no Linux, **LocalMachine** / **Os meus** pontos apontam para a localização padrão dos certificados, o diretório */var/lib/sfcerts.* Para o Linux, quaisquer outras combinações de **CertificateStoreLocation** e **CertificateStoreName** estão indefinidas. 

Especifique sempre **a Escola Local** para o parâmetro **CertificateStoreLocation.** Não há necessidade de especificar o parâmetro **CertificateStoreName** porque não tem "O meu". Com uma referência **X509,** os ficheiros de certificado devem estar localizados no diretório */var/lib/sfcerts* no nó de cluster.  

O seguinte XML mostra uma secção **TransportSettings** baseada neste estilo:

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

Com o Java SDK, pode especificar **X509_2** para o **SecurityCredentialsType**. Isto corresponde ao `X509Credentials2` tipo[(Java)](/java/api/system.fabric.x509credentials2)de `SecurityCredentials` [(Java).](/java/api/system.fabric.securitycredentials) 

Com uma referência **X509_2,** especifique um parâmetro de percurso, para que possa localizar o certificado num diretório diferente de */var/lib/sfcerts*.  O seguinte XML mostra os parâmetros utilizados para especificar a localização do certificado: 

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

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Configure uma app Reliable Services para funcionar em clusters Linux

Os SDKs de tecido de serviço permitem-lhe comunicar com as APIs de execução do tecido de serviço para alavancar a plataforma. Quando executar qualquer aplicação que utilize esta funcionalidade em clusters Linux seguros, precisa configurar a sua aplicação com um certificado que pode usar para validar com o tempo de execução do Tecido de Serviço. As aplicações que contêm serviços de serviço fiável de tecido de serviço escritos utilizando os SDKs .NET Core ou Java requerem esta configuração. 

Para configurar uma aplicação, adicione um elemento [**SecretsCertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) sob a etiqueta **Certificados,** que está localizado sob a etiqueta **ApplicationManifest** no ficheiro *ApplicationManifest.xml.* O seguinte XML mostra um certificado referenciado pela sua impressão digital: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Pode fazer referência ao certificado de cluster ou a um certificado que instalou em cada nó de cluster. No Linux, os ficheiros de certificado devem estar presentes no diretório */var/lib/sfcerts.* Para saber mais, consulte [localização e formato de certificados X.509 nos nós Linux](#location-and-format-of-x509-certificates-on-linux-nodes).
