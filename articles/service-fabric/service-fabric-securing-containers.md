---
title: Importar certificados para um contêiner
description: Aprenda agora a importar arquivos de certificado para um serviço de contêiner Service Fabric.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: da4babd8f9d1a25a8514d0c6f1526b43a9723854
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614116"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importar um arquivo de certificado para um contêiner em execução no Service Fabric

Você pode proteger seus serviços de contêiner especificando um certificado. Service Fabric fornece um mecanismo para serviços dentro de um contêiner para acessar um certificado instalado nos nós em um cluster do Windows ou do Linux (versão 5,7 ou superior). O certificado deve ser instalado em um repositório de certificados em LocalMachine em todos os nós do cluster. A chave privada correspondente ao certificado deve estar disponível, acessível e-no Windows-exportável. As informações do certificado são fornecidas no manifesto do aplicativo sob a marca `ContainerHostPolicies`, como mostra o trecho a seguir:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Para clusters do Windows, ao iniciar o aplicativo, o tempo de execução exporta cada certificado referenciado e sua chave privada correspondente para um arquivo PFX, protegido com uma senha gerada aleatoriamente. Os arquivos PFX e de senha, respectivamente, podem ser acessados dentro do contêiner usando as seguintes variáveis de ambiente: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Para clusters do Linux, os certificados (PEM) são copiados do repositório especificado por X509StoreName para o contêiner. As variáveis de ambiente correspondentes no Linux são:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Como alternativa, se você já tiver os certificados no formulário necessário e quiser acessá-los dentro do contêiner, poderá criar um pacote de dados dentro do pacote do aplicativo e especificar o seguinte dentro do manifesto do aplicativo:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

O serviço ou processo de contêiner é responsável por importar os arquivos de certificado para o contêiner. Para importar o certificado, você pode usar `setupentrypoint.sh` scripts ou executar código personalizado dentro do processo de contêiner. Aqui está o código de C# exemplo no para importar o arquivo PFX:

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
Esse certificado PFX pode ser usado para autenticar o aplicativo ou serviço ou proteger a comunicação com outros serviços. Por padrão, os arquivos são ACLed apenas para o sistema. Você pode fazer a ACL para outras contas, conforme exigido pelo serviço.

Como uma próxima etapa, leia os seguintes artigos:

* [Implantar um contêiner do Windows para Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implantar um contêiner do Docker no Service Fabric no Linux](service-fabric-get-started-containers-linux.md)
