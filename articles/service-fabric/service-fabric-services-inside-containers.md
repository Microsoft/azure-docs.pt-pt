---
title: Contentorize os seus serviços de tecido de serviço Azure no Windows
description: Saiba como contentorizar os seus serviços de serviços fiáveis e de atores fiáveis no Windows.
ms.topic: conceptual
ms.date: 5/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e0c5f3ecf0334ad2190d8542e54388a2a25d30fb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999812"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Colocar o Service Fabric Reliable Services e o Reliable Actors em contentores no Windows

O Service Fabric suporta microserviços de tecido de serviço de contentorização (Serviços Fiáveis e Serviços baseados em Atores Fiáveis). Para mais informações, consulte [os recipientes de tecido de serviço.](service-fabric-containers-overview.md)

Este documento fornece orientações para que o seu serviço seja funcionado dentro de um contentor windows.

> [!NOTE]
> Atualmente esta funcionalidade funciona apenas para o Windows. Para executar contentores, o cluster deve estar a funcionar no Windows Server 2016 com contentores.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Passos para contentorizar a sua Aplicação de Tecido de Serviço

1. Abra a sua aplicação de Tecido de Serviço no Estúdio Visual.

2. Adicione [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) de classe ao seu projeto. O código desta classe é um ajudante para carregar corretamente os binários de tempo de execução do Tecido de Serviço no interior da sua aplicação quando estiver a correr dentro de um recipiente.

3. Para cada pacote de código que pretende contentorizar, inicialize o carregador no ponto de entrada do programa. Adicione o construtor estático mostrado no seguinte corte de código no ficheiro de ponto de entrada do programa.

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

4. Construa e [embale](service-fabric-package-apps.md#Package-App) o seu projeto. Para construir e criar um pacote, clique com o botão direito no projeto de aplicação no Solution Explorer e escolha o comando **Pacote.**

5. Para cada pacote de código que necessitar de contentorizar, executar o script PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). A utilização é a seguinte:

    Full.NET
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
      O guião cria uma pasta com artefactos Docker no $dockerPackageOutputDirectoryPath. Modifique o Dockerfile gerado para `expose` quaisquer portas, executar scripts de configuração e assim por diante. com base nas suas necessidades.

6. Em seguida, tens de [construir](service-fabric-get-started-containers.md#Build-Containers) e [empurrar](service-fabric-get-started-containers.md#Push-Containers) o teu pacote de contentores Docker para o teu repositório.

7. Modifique o ApplicationManifest.xml e ServiceManifest.xml para adicionar a sua imagem de recipiente, informações de repositório, autenticação de registo e mapeamento porta-a-hospedeiro. Para modificar os manifestos, consulte [Criar uma aplicação de recipiente de tecido de serviço Azure](service-fabric-get-started-containers.md). A definição de pacote de código no manifesto de serviço tem de ser substituída pela imagem correspondente do recipiente. Certifique-se de que altera o Ponto de Entrada para um tipo ContainerHost.

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

8. Adicione o mapeamento porta-a-anfitrião para o seu replicador e ponto final de serviço. Uma vez que ambas as portas são atribuídas em tempo de execução pela Service Fabric, o ContainerPort está definido para zero para utilizar a porta atribuída para mapeamento.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Para configurar o modo de isolamento do recipiente, consulte o modo de [isolamento de configuração]( ./service-fabric-get-started-containers.md#configure-isolation-mode). O Windows suporta dois modos de isolamento para contentores: processo e Hyper-V. Os seguintes snippets mostram como o modo de isolamento é especificado no ficheiro manifesto de aplicação.

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
> Por predefinição, as aplicações service Fabric têm acesso ao tempo de funcionamento do Tecido de Serviço, sob a forma de um ponto final que aceita pedidos específicos da aplicação. Considere desativar este acesso quando a aplicação apresentar código não fidedidrúlico. Para mais informações, consulte as [melhores práticas de segurança em Service Fabric.](service-fabric-best-practices-security.md#platform-isolation) Para desativar o acesso ao tempo de funcionamento do Tecido de Serviço, adicione a seguinte definição na secção Políticas do manifesto de aplicação correspondente ao manifesto de serviço importado, da seguinte forma:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Para testar esta aplicação, é necessário implantá-la num cluster que esteja a executar a versão 5.7 ou superior. Para as versões de tempo de execução 6.1 ou inferiores, é necessário editar e atualizar as definições do cluster para ativar esta funcionalidade de pré-visualização. Siga os passos deste [artigo](service-fabric-cluster-fabric-settings.md) para adicionar a definição mostrada a seguir.
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

11. Em [seguida, implemente](service-fabric-deploy-remove-applications.md) o pacote de aplicações editado para este cluster.

Deverá agora ter uma aplicação de tecido de serviço contentorizada a executar o seu cluster.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-get-started-containers.md).
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.