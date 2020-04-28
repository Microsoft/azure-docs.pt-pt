---
title: Certificados de importação num contentor
description: Aprenda agora a importar ficheiros de certificados num serviço de contentores de tecido de serviço.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: da4babd8f9d1a25a8514d0c6f1526b43a9723854
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614116"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importar um ficheiro de certificado num contentor em funcionamento no Tecido de Serviço

Pode assegurar os seus serviços de contentores especificando um certificado. O Service Fabric fornece um mecanismo de serviços dentro de um contentor para aceder a um certificado instalado nos nós de um cluster Windows ou Linux (versão 5.7 ou superior). O certificado deve ser instalado num certificado em localmachine em todos os nós do cluster. A chave privada correspondente ao certificado deve estar disponível, acessível e - no Windows - exportável. As informações do certificado são `ContainerHostPolicies` fornecidas no manifesto de candidatura sob a etiqueta, como mostra o seguinte corte:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Para os clusters Windows, ao iniciar a aplicação, o tempo de execução exporta cada certificado referenciado e a sua chave privada correspondente num ficheiro PFX, protegido com uma palavra-passe gerada aleatoriamente. Os ficheiros PFX e password, respectivamente, são acessíveis no interior do recipiente utilizando as seguintes variáveis ambientais: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Para os clusters Linux, os certificados (PEM) são copiados da loja especificada pelo X509StoreName no recipiente. As variáveis ambientais correspondentes no Linux são:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Em alternativa, se já tiver os certificados no formulário exigido e quiser acessá-lo dentro do recipiente, pode criar um pacote de dados dentro do pacote de aplicações e especificar o seguinte dentro do manifesto de aplicação:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

O serviço ou processo do contentor é responsável pela importação dos ficheiros do certificado para o contentor. Para importar o certificado, `setupentrypoint.sh` pode utilizar scripts ou executar código personalizado no processo do contentor. Aqui está o código da amostra em C# para importar o ficheiro PFX:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Este certificado PFX pode ser utilizado para autenticar a aplicação ou serviço ou uma comunicação segura com outros serviços. Por predefinição, os ficheiros são apenas ACLed para SYSTEM. Pode aclit-lo para outras contas conforme exigido pelo serviço.

Como próximo passo, leia os seguintes artigos:

* [Implemente um recipiente Windows para o Serviço de Tecido no Windows Server 2016](service-fabric-get-started-containers.md)
* [Desloque um recipiente Docker para o tecido de serviço em Linux](service-fabric-get-started-containers-linux.md)
