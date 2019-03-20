---
title: Crie contentores seus serviços do Azure Service Fabric no Windows
description: Aprenda a colocar num contentor de seus serviços do Service Fabric Reliable Services e Reliable Actors no Windows.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: anmolah
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/23/2018
ms.author: aljo, anmola
ms.openlocfilehash: 147607bbea65199ff97459711ad6301a4ae93aa4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079831"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Colocar num contentor do Service Fabric Reliable Services e Reliable Actors no Windows

O Service Fabric suporta empacotar microsserviços de Service Fabric (Reliable Services e serviços de Reliable Actor com base). Para obter mais informações, consulte [contentores de recursos de infraestrutura do serviço](service-fabric-containers-overview.md).

Este documento fornece orientações para o serviço em execução num contentor do Windows.

> [!NOTE]
> Atualmente esta funcionalidade funciona apenas para Windows. Para executar contentores, tem de executar o cluster no Windows Server 2016 com contentores.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Passos para colocar num contentor da sua aplicação do Service Fabric

1. Abra a aplicação do Service Fabric no Visual Studio.

2. Adicionar a classe [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) ao seu projeto. O código nessa classe é um auxiliar para carregar corretamente os binários de runtime do Service Fabric dentro do seu aplicativo quando em execução num contentor.

3. Para cada pacote de código que gostaria de colocar num contentor, inicializar o carregador na entrada de programa do ponto. Adicione o construtor estático mostrado no seguinte fragmento de código ao seu ficheiro de ponto de entrada do programa.

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

4. Criar e [pacote](service-fabric-package-apps.md#Package-App) seu projeto. Para compilar e criar um pacote, clique com o botão direito do projeto de aplicativo no Explorador de soluções e escolha o **pacote** comando.

5. Para cada pacote do código tem de colocar num contentor, execute o script do PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). A utilização é o seguinte:

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
      O script cria uma pasta com os artefactos de Docker em $dockerPackageOutputDirectoryPath. Modificar o Dockerfile gerado para `expose` qualquer portas, executadas scripts de configuração e assim por diante. com base nas suas necessidades.

6. Em seguida precisa [crie](service-fabric-get-started-containers.md#Build-Containers) e [push](service-fabric-get-started-containers.md#Push-Containers) seu pacote de contentor do Docker para o seu repositório.

7. Modificar a applicationmanifest. XML e o servicemanifest. XML para adicionar a imagem de contentor, informações do repositório, autenticação de registo e mapeamento de porta-a-anfitrião. Para modificar os manifestos, consulte [criar uma aplicação de contentor do Azure Service Fabric](service-fabric-get-started-containers.md). A definição de pacote de código no manifesto do serviço tem de ser substituído com a imagem de contentor correspondente. Certifique-se alterar o ponto de entrada para um tipo de ContainerHost.

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

8. Adicione o mapeamento de porta-a-anfitrião para o seu replicator e o ponto final de serviço. Uma vez que ambas estas portas são atribuídas em tempo de execução pelo Service Fabric, o ContainerPort é definida como zero para utilizar a porta atribuída para o mapeamento.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Para configurar o modo de isolamento de contentor, veja [modo de isolamento de configurar]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). O Windows suporta dois modos de isolamento para contentores: processo e Hyper-V. Os fragmentos seguintes mostram como o modo de isolamento é especificado no arquivo de manifesto do aplicativo.

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

10. Para testar esta aplicação, terá de implementá-la a um cluster que esteja executando a versão 5.7 ou superior. Para versões de tempo de execução 6.1 ou inferiores, terá de editar e atualizar as definições de cluster para ativar esta funcionalidade de pré-visualização. Siga os passos desta [artigo](service-fabric-cluster-fabric-settings.md) para adicionar a definição mostrada a seguir.
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

11. Próxima [implementar](service-fabric-deploy-remove-applications.md) o pacote de aplicação editado para este cluster.

Agora, deve ter uma aplicação em contentores do Service Fabric com o seu cluster.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-get-started-containers.md).
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.
