---
title: Certificados de importação num contentor
description: Aprenda agora a importar ficheiros de certificados para um serviço de contentores de tecido de serviço.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 426aa2ebbfb87fe2c80e0d1aff3eeecbe0e2472d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89050748"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importar um ficheiro de certificado para um contentor em execução no Tecido de Serviço

> [!NOTE]
> Para os clusters de tecido de serviço em execução em Azure, recomenda-se a utilização de [Aplicação de Tecido de Serviço Identidade Gerida](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) para obter certificados de pedido de dentro de um contentor. A Identidade Gerida dá o isolamento de segredos e certificados ao nível do serviço, e permite que o fornecimento de certificado de pedido faça parte do fluxo de trabalho da aplicação, em vez do fluxo de trabalho da infraestrutura. O mecanismo CertificateRef será depreciado numa futura versão.

Pode assegurar os seus serviços de contentores especificando um certificado. O Service Fabric fornece um mecanismo de serviços dentro de um contentor para aceder a um certificado instalado nos nós num aglomerado Windows ou Linux (versão 5.7 ou superior). O certificado deve ser instalado numa loja de certificados ao abrigo da LocalMachine em todos os nós do cluster. A chave privada correspondente ao certificado deve estar disponível, acessível e - no Windows - exportável. As informações do certificado são fornecidas no manifesto de pedido sob a `ContainerHostPolicies` etiqueta, como mostra o seguinte corte:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Para os clusters Windows, ao iniciar a aplicação, o tempo de execução exporta cada certificado referenciado e a sua chave privada correspondente para um ficheiro PFX, protegido com uma palavra-passe gerada aleatoriamente. Os ficheiros PFX e password, respectivamente, são acessíveis no interior do contentor utilizando as seguintes variáveis ambientais: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Para os clusters Linux, os certificados (PEM) são copiados da loja especificada pelo X509StoreName para o recipiente. As variáveis ambientais correspondentes no Linux são:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Por favor, note que tanto o `PEM` ficheiro como o ficheiro contêm o certificado e a chave privada não `PrivateKey` encriptada.

Em alternativa, se já tiver os certificados no formulário exigido e quiser acessá-lo dentro do contentor, pode criar um pacote de dados dentro do seu pacote de aplicações e especificar o seguinte dentro do seu manifesto de aplicação:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

O serviço ou processo do contentor é responsável pela importação dos ficheiros de certificados para o contentor. Para importar o certificado, pode utilizar `setupentrypoint.sh` scripts ou executar código personalizado dentro do processo do contentor. Aqui está o código de amostra em C# para a importação do ficheiro PFX:

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
Este certificado PFX pode ser utilizado para autenticar a aplicação ou serviço ou comunicação segura com outros serviços. Por predefinição, os ficheiros são ACLED apenas para o SISTEMA. Pode aCL-lo para outras contas conforme exigido pelo serviço.

Como passo seguinte, leia os seguintes artigos:

* [Implementar um contentor Windows para o tecido de serviço no Windows Server 2016](service-fabric-get-started-containers.md)
* [Colocar um recipiente Docker para o tecido de serviço em Linux](service-fabric-get-started-containers-linux.md)
