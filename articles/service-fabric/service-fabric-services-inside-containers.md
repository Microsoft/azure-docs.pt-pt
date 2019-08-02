---
title: Colocar em contêineres seus serviços de Service Fabric do Azure no Windows
description: Saiba como colocar seus Service Fabric em contêineres Reliable Services e Reliable Actors serviços no Windows.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: anmolah
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/23/2018
ms.author: anmola
ms.openlocfilehash: 0cb48a2272ce854005f9f3db5b6a9abf62cc7015
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599199"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Colocar seu Service Fabric em contêineres Reliable Services e Reliable Actors no Windows

O Service Fabric dá suporte ao contêiner Service Fabric de microserviços (Reliable Services e serviços confiáveis baseados em ator). Para obter mais informações, consulte contêineres do [Service Fabric](service-fabric-containers-overview.md).

Este documento fornece orientação para colocar seu serviço em execução dentro de um contêiner do Windows.

> [!NOTE]
> Atualmente, esse recurso funciona apenas para Windows. Para executar contêineres, o cluster deve estar em execução no Windows Server 2016 com contêineres.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Etapas para colocar seu aplicativo em contêinerização Service Fabric

1. Abra seu aplicativo Service Fabric no Visual Studio.

2. Adicione a classe [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) ao seu projeto. O código nessa classe é um auxiliar para carregar corretamente os binários de tempo de execução Service Fabric dentro de seu aplicativo durante a execução dentro de um contêiner.

3. Para cada pacote de código que você deseja colocar em contêineres, inicialize o carregador no ponto de entrada do programa. Adicione o construtor estático mostrado no trecho de código a seguir ao seu arquivo de ponto de entrada do programa.

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. Crie e [empacote](service-fabric-package-apps.md#Package-App) seu projeto. Para criar e criar um pacote, clique com o botão direito do mouse no projeto de aplicativo em Gerenciador de Soluções e escolha o comando **pacote** .

5. Para cada pacote de código que você precisa colocar em contêineres, execute o script do PowerShell [CreateDockerPackage. ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). O uso é o seguinte:

    .NET completo
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      O script cria uma pasta com artefatos do Docker em $dockerPackageOutputDirectoryPath. Modifique o Dockerfile gerado para `expose` qualquer porta, execute os scripts de instalação e assim por diante. com base em suas necessidades.

6. Em seguida, você precisa [criar](service-fabric-get-started-containers.md#Build-Containers) e [enviar por push](service-fabric-get-started-containers.md#Push-Containers) o pacote de contêiner do Docker para o repositório.

7. Modifique o ApplicationManifest. xml e o inmanifest. xml para adicionar a imagem de contêiner, as informações do repositório, a autenticação do registro e o mapeamento de porta a host. Para modificar os manifestos, consulte [criar um aplicativo de contêiner de Service Fabric do Azure](service-fabric-get-started-containers.md). A definição do pacote de códigos no manifesto do serviço precisa ser substituída pela imagem de contêiner correspondente. Certifique-se de alterar o EntryPoint para um tipo ContainerHost.

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. Adicione o mapeamento de porta a host para o replicador e o ponto de extremidade de serviço. Como ambas as portas são atribuídas em tempo de execução por Service Fabric, o ContainerPort é definido como zero para usar a porta atribuída para mapeamento.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Para configurar o modo de isolamento de contêiner, consulte [Configurar o modo de isolamento]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). O Windows suporta dois modos de isolamento para contentores: processo e Hyper-V. Os trechos de código a seguir mostram como o modo de isolamento é especificado no arquivo de manifesto do aplicativo.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

> [!NOTE] 
> Por padrão, os aplicativos Service Fabric têm acesso ao tempo de execução do Service Fabric, na forma de um ponto de extremidade que aceita solicitações específicas do aplicativo. Considere desabilitar esse acesso quando o aplicativo hospedar código não confiável. Para obter mais informações, consulte [práticas recomendadas de segurança em Service Fabric](service-fabric-best-practices-security.md#platform-isolation). Para desabilitar o acesso ao Service Fabric Runtime, adicione a configuração a seguir na seção políticas do manifesto do aplicativo correspondente ao manifesto do serviço importado, da seguinte maneira:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Para testar esse aplicativo, você precisa implantá-lo em um cluster que esteja executando a versão 5,7 ou superior. Para versões de tempo de execução 6,1 ou inferior, você precisa editar e atualizar as configurações de cluster para habilitar esse recurso de visualização. Siga as etapas neste [artigo](service-fabric-cluster-fabric-settings.md) para adicionar a configuração mostrada a seguir.
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. Em seguida, [implante](service-fabric-deploy-remove-applications.md) o pacote de aplicativos editado nesse cluster.

Agora você deve ter um aplicativo de Service Fabric em contêiner em execução no cluster.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-get-started-containers.md).
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.
