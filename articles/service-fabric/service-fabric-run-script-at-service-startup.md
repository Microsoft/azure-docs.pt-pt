---
title: Executar um script quando inicia um serviço de Azure Service Fabric | Documentos da Microsoft
description: Saiba como configurar uma política para um ponto de entrada de configuração de serviço do Service Fabric e executar um script no início do serviço de tempo de atividade.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 3ae43f7427996f8be15b22fec4406bbdfe8aa4fe
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838447"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Executar um script de arranque do serviço como uma conta de utilizador local ou conta de sistema
Antes de um serviço do Service Fabric executável é iniciado poderá ser necessário executar algum trabalho de configuração ou configuração.  Por exemplo, a configurar variáveis de ambiente. Pode especificar um script para executar antes do executável do serviço é iniciado no manifesto do serviço para o serviço. Ao configurar uma política de RunAs para o ponto de entrada de configuração de serviço que pode alterar a conta, o executável de configuração é executado sob.  Um ponto de entrada de configuração individual permite-lhe executar a configuração de privilégios elevados durante um curto período de tempo para que o executável de host de serviço não tem de executar com privilégios elevados por longos períodos de tempo.

O ponto de entrada de configuração (**SetupEntryPoint** no [manifesto de serviço](service-fabric-application-and-service-manifests.md)) é um ponto de entrada com privilégios que, por predefinição, é executado com as mesmas credenciais do Service Fabric (normalmente o  *NetworkService* conta) antes de qualquer outro ponto de entrada. O executável que é especificado por **EntryPoint** é, normalmente, o anfitrião do serviço de execução longa. O **EntryPoint** executável é executado a **SetupEntryPoint** executável é encerrado com êxito. O processo resultante é monitorizado e reiniciado e começa novamente com **SetupEntryPoint** se alguma vez terminar ou falhar. 

## <a name="configure-the-service-setup-entry-point"></a>Configurar o ponto de entrada da configuração do serviço
Segue-se um exemplo de manifesto do serviço simples para um serviço sem estado, que especifica um script de configuração *MySetup.bat* no serviço **SetupEntryPoint**.  **Argumentos** é usado para passar os argumentos ao script quando ele é executado.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Configurar a política para um ponto de entrada de configuração de serviço
Por predefinição, o executável de ponto de entrada de configuração de serviço é executado sob as mesmas credenciais do Service Fabric (normalmente, o *NetworkService* conta).  No manifesto do aplicativo, pode alterar as permissões de segurança para executar o script de arranque com uma conta de sistema local ou uma conta de administrador.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Configurar a política com uma conta de sistema local
O exemplo de manifesto de aplicação seguinte mostra como configurar o ponto de entrada de configuração de serviço para executar na conta de administrador do utilizador (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

Primeiro, crie uma **principais** seção com um nome de utilizador, como SetupAdminUser. A conta de utilizador SetupAdminUser é um membro do grupo administradores do sistema.

Em seguida, sob o **ServiceManifestImport** secção, configure uma política para aplicar este principal para **SetupEntryPoint**. Esta política indica ao Service Fabric que quando o **MySetup.bat** ficheiro é executá-lo deve ser executado como SetupAdminUser (com privilégios de administrador). Uma vez que tem *não* aplicada uma política para o ponto de entrada principal, o código na **MyServiceHost.exe** seja executado sob o sistema **NetworkService** conta. Esta é a conta predefinida que todos os pontos de entrada de serviço são executados como.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Configurar a política, utilizando as contas de sistema local
Muitas vezes, é preferível para executar o script de arranque com uma conta de sistema local, em vez de uma conta de administrador. A política de RunAs a executar como um membro do grupo Administradores, normalmente, não funciona bem porque os computadores têm controlo de acesso utilizador (UAC) ativada por predefinição. Nesses casos, a recomendação é ser executado o SetupEntryPoint como LocalSystem, em vez de como um utilizador local adicionado ao grupo Administradores. O exemplo seguinte mostra a definição SetupEntryPoint para ser executado como LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> Para clusters do Linux, para executar um serviço ou a configuração do ponto de entrada como **raiz**, pode especificar a **AccountType** como **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Executar um script a partir do ponto de entrada de configuração
Agora, adicione um script de arranque para o projeto para executar com privilégios de administrador. 

No Visual Studio, clique com o botão direito do projeto de serviço e adicionar um novo arquivo chamado *MySetup.bat*.

Em seguida, certifique-se de que o *MySetup.bat* arquivo está incluído no pacote de serviço. Por predefinição, não é. Selecione o ficheiro, faça duplo clique para obter o menu de contexto e escolha **propriedades**. Na caixa de diálogo de propriedades, certifique-se de que **Copy to Output Directory** está definida como **copiar se for mais recente**. Veja a captura de ecrã abaixo.

![Visual Studio CopyToOutput para o arquivo em lotes SetupEntryPoint][image1]

Agora editar a *MySetup.bat* de ficheiros e adicionar os seguintes comandos definir uma variável de ambiente de sistema e um arquivo de texto de saída:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Em seguida, criar e implementar a solução para um cluster de desenvolvimento local. Depois do serviço foi iniciado, conforme mostrado na [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), pode ver que o ficheiro de MySetup.bat foi concluída com êxito de uma duas maneiras. Abra uma linha de comandos do PowerShell e escreva:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Em seguida, tome nota do nome do nó onde o serviço foi implementado e iniciado no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Por exemplo, 2 de nó. Em seguida, navegue para a pasta de trabalho de instância de aplicação para encontrar o ficheiro de out.txt que mostra o valor de **TestVariable**. Por exemplo, se este serviço for implementado para o nó 2, em seguida, pode aceder a este caminho para o **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Execute comandos do PowerShell a partir de um ponto de entrada de configuração
Para executar o PowerShell a partir da **SetupEntryPoint** ponto, pode executar **PowerShell.exe** num arquivo em lotes que aponta para um ficheiro do PowerShell. Primeiro, adicione um ficheiro do PowerShell para o projeto de serviço – por exemplo, **MySetup.ps1**. Não se esqueça de definir o *copiar se for mais recente* propriedade para que o ficheiro também está incluído no pacote de serviço. O exemplo seguinte mostra um ficheiro de batch de exemplo que inicia um arquivo de PowerShell chamado MySetup.ps1, que define uma variável de ambiente de sistema chamada **TestVariable**.

MySetup.bat para iniciar um ficheiro do PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

No arquivo do PowerShell, adicione o seguinte para definir uma variável de ambiente de sistema:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Por predefinição, quando o arquivo em lotes é executado, ele analisa a pasta de aplicação denominada **trabalhar** para ficheiros. Neste caso, quando é executada MySetup.bat, queremos que pode encontrar o ficheiro de MySetup.ps1 na mesma pasta, que é o aplicativo **pacote de código** pasta. Para alterar o dessa pasta, defina a pasta de trabalho:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Depurar um script de inicialização localmente usando o redirecionamento de consola
Ocasionalmente, é útil para fins para ver o resultado da execução de um script de configuração consola de depuração. Pode definir uma política de redirecionamento de consola no ponto de entrada de configuração no manifesto do serviço, que grava a saída num arquivo. O resultado de ficheiro é escrito para a pasta de aplicativo chamada **log** no nó de cluster em que a aplicação é implementada e executar. 

> [!WARNING]
> Nunca use a política de redirecionamento de consola num aplicativo que é implementado em produção, porque isso pode afetar o failover de aplicativos. *Apenas* usá-lo para o desenvolvimento local e fins de depuração.  
> 
> 

O exemplo de manifesto do serviço seguinte mostra a definir o redirecionamento de consola com um valor de FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Se alterar o ficheiro de MySetup.ps1 para escrever um **eco** de comando, a gravação será feita no ficheiro de saída para fins de depuração:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Depois de depurar o script, imediatamente remova esta política de redirecionamento de consola.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre a segurança do serviço e de aplicação](service-fabric-application-and-service-security.md)
* [Compreender o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos num manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
