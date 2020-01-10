---
title: Executar um script quando um serviço de Service Fabric do Azure for iniciado
description: Saiba como configurar uma política para um ponto de entrada de instalação do serviço de Service Fabric e executar um script no momento da inicialização do serviço.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464281"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Executar um script de arranque do serviço como uma conta de utilizador local ou conta de sistema
Antes que um executável do serviço de Service Fabric seja iniciado, pode ser necessário executar alguma configuração ou trabalho de configuração.  Por exemplo, configurar variáveis de ambiente. Você pode especificar um script a ser executado antes que o executável do serviço seja iniciado no manifesto do serviço para o serviço. Ao configurar uma política RunAs para o ponto de entrada de instalação do serviço, você pode alterar a conta em que o executável de instalação é executado.  Um ponto de entrada de instalação separado permite que você execute uma configuração com altos privilégios por um curto período de tempo para que o executável do host de serviço não precise ser executado com altos privilégios por longos períodos de tempo.

O ponto de entrada de instalação (**SetupEntryPoint** no [manifesto do serviço](service-fabric-application-and-service-manifests.md)) é um ponto de entrada privilegiado que, por padrão, é executado com as mesmas credenciais que Service Fabric (normalmente a conta *NetworkService* ) antes de qualquer outro ponto de entrada. O executável especificado pelo **EntryPoint** normalmente é o host de serviço de longa execução. O executável do **ponto de entrada** é executado depois que o executável **SetupEntryPoint** é encerrado com êxito. O processo resultante é monitorado e reiniciado e começa novamente com **SetupEntryPoint** se ele for encerrado ou falhar. 

## <a name="configure-the-service-setup-entry-point"></a>Configurar o ponto de entrada da configuração do serviço
Veja a seguir um exemplo de manifesto de serviço simples para um serviço sem estado que especifica um script de instalação *mySetup. bat* no serviço **SetupEntryPoint**.  Os **argumentos** são usados para passar argumentos para o script quando ele é executado.

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
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Configurar a política para um ponto de entrada de instalação do serviço
Por padrão, o executável do ponto de entrada da instalação do serviço é executado com as mesmas credenciais que Service Fabric (normalmente a conta *NetworkService* ).  No manifesto do aplicativo, você pode alterar as permissões de segurança para executar o script de inicialização em uma conta do sistema local ou em uma conta de administrador.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Configurar a política usando uma conta do sistema local
O exemplo de manifesto de aplicativo a seguir mostra como configurar o ponto de entrada de configuração de serviço para ser executado na conta de administrador de usuário (SetupAdminUser).

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

Primeiro, crie uma seção de **entidades** com um nome de usuário, como SetupAdminUser. A conta de usuário SetupAdminUser é membro do grupo de sistemas administradores.

Em seguida, na seção **ServiceManifestImport** , configure uma política para aplicar essa entidade de segurança ao **SetupEntryPoint**. Essa política informa Service Fabric que quando o arquivo **mySetup. bat** é executado, ele deve ser executado como SetupAdminUser (com privilégios de administrador). Como você *não* aplicou uma política ao ponto de entrada principal, o código em **myservicehost. exe** é executado sob a conta **NetworkService** do sistema. Essa é a conta padrão para a qual todos os pontos de entrada de serviço são executados.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Configurar a política usando contas do sistema local
Geralmente, é preferível executar o script de inicialização usando uma conta do sistema local em vez de uma conta de administrador. A execução da política RunAs como um membro do grupo Administradores normalmente não funciona bem porque os computadores têm o UAC (controle de acesso do usuário) habilitado por padrão. Nesses casos, a recomendação é executar o SetupEntryPoint como LocalSystem, em vez de um usuário local adicionado ao grupo Administradores. O exemplo a seguir mostra a configuração do SetupEntryPoint para executar como LocalSystem:

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
> Para clusters do Linux, para executar um serviço ou o ponto de entrada de instalação como **raiz**, você pode especificar o **AccountType** como **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Executar um script do ponto de entrada de instalação
Agora, adicione um script de inicialização ao projeto para ser executado sob privilégios de administrador. 

No Visual Studio, clique com o botão direito do mouse no projeto de serviço e adicione um novo arquivo chamado *mySetup. bat*.

Em seguida, verifique se o arquivo *mySetup. bat* está incluído no pacote de serviço. Por padrão, não é. Selecione o arquivo, clique com o botão direito do mouse para obter o menu de contexto e escolha **Propriedades**. Na caixa de diálogo Propriedades, verifique se a **cópia para o diretório de saída** está definida como **copiar se for mais recente**. Veja a captura de ecrã abaixo.

![Arquivo em lotes do Visual Studio CopyToOutput para SetupEntryPoint][image1]

Agora edite o arquivo *mySetup. bat* e adicione os seguintes comandos para definir uma variável de ambiente do sistema e gerar um arquivo de texto:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Em seguida, compile e implante a solução em um cluster de desenvolvimento local. Depois que o serviço for iniciado, conforme mostrado na [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), você poderá ver que o arquivo mySetup. bat foi bem-sucedido de duas maneiras. Abra um prompt de comando do PowerShell e digite:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Em seguida, observe o nome do nó em que o serviço foi implantado e iniciado no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Por exemplo, o nó 2. Em seguida, navegue até a pasta de trabalho da instância do aplicativo para encontrar o arquivo out. txt que mostra o valor de **TestVariable**. Por exemplo, se esse serviço foi implantado no nó 2, você pode ir para esse caminho para o **Myapplicationtype**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Executar comandos do PowerShell de um ponto de entrada de instalação
Para executar o PowerShell do ponto de **SetupEntryPoint** , você pode executar o **PowerShell. exe** em um arquivo em lotes que aponta para um arquivo do PowerShell. Primeiro, adicione um arquivo do PowerShell ao projeto de serviço, por exemplo, **mySetup. ps1**. Lembre-se de definir a propriedade *copiar se for mais recente* para que o arquivo também seja incluído no pacote de serviço. O exemplo a seguir mostra um arquivo em lotes de exemplo que inicia um arquivo do PowerShell chamado mySetup. ps1, que define uma variável de ambiente do sistema chamada **TestVariable**.

MySetup. bat para iniciar um arquivo do PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

No arquivo do PowerShell, adicione o seguinte para definir uma variável de ambiente do sistema:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Por padrão, quando o arquivo em lotes é executado, ele examina a pasta do aplicativo chamada **trabalho** para arquivos. Nesse caso, quando mySetup. bat é executado, queremos que isso localize o arquivo mySetup. ps1 na mesma pasta, que é a pasta do **pacote de código** do aplicativo. Para alterar essa pasta, defina a pasta de trabalho:
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

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Depurar um script de inicialização localmente usando o redirecionamento de console
Ocasionalmente, é útil para fins de depuração para ver a saída do console da execução de um script de instalação. Você pode definir uma política de redirecionamento de console no ponto de entrada de instalação no manifesto do serviço, que grava a saída em um arquivo. A saída do arquivo é gravada na pasta do aplicativo chamada **log** no nó do cluster em que o aplicativo é implantado e executado. 

> [!WARNING]
> Nunca use a política de redirecionamento de console em um aplicativo implantado em produção porque isso pode afetar o failover do aplicativo. Use isso *apenas* para fins de depuração e desenvolvimento locais.  
> 
> 

O exemplo de manifesto de serviço a seguir mostra a configuração do redirecionamento de console com um valor de FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Se agora você alterar o arquivo mySetup. ps1 para gravar um comando **Echo** , isso gravará no arquivo de saída para fins de depuração:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Depois de depurar o script, remova imediatamente essa política de redirecionamento de console.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre a segurança de aplicativos e serviços](service-fabric-application-and-service-security.md)
* [Entender o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
