---
title: Executar um guião quando um serviço de tecido de serviço Azure começar
description: Aprenda a configurar uma política para um ponto de entrada de configuração de serviço de serviço e execute um script no tempo de arranque do serviço.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75464281"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Executar um script de arranque do serviço como uma conta de utilizador local ou conta de sistema
Antes de um serviço de serviço de fabricação executar, pode ser necessário executar algum trabalho de configuração ou configuração.  Por exemplo, configurar variáveis ambientais. Pode especificar um script a executar antes de o serviço executar o manifesto de serviço para o serviço. Ao configurar uma política de RunAs para o ponto de entrada de configuração do serviço, pode alterar a conta que a configuração executável é executada.  Um ponto de entrada separado permite-lhe executar uma configuração privilegiada por um curto período de tempo para que o anfitrião do serviço executável não precise de correr com privilégios elevados por longos períodos de tempo.

O ponto de entrada de configuração **(SetupEntryPoint** no manifesto de [serviço)](service-fabric-application-and-service-manifests.md)é um ponto de entrada privilegiado que, por padrão, funciona com as mesmas credenciais que o Service Fabric (normalmente a conta *NetworkService)* antes de qualquer outro ponto de entrada. O executível especificado pelo **EntryPoint** é tipicamente o anfitrião de serviço de longa duração. O **executável Do Ponto** de Entrada é executado após a saída executável do **SetupEntryPoint** com sucesso. O processo resultante é monitorizado e reiniciado, e recomeça com **setupEntryPoint** se alguma vez terminar ou se despenhar. 

## <a name="configure-the-service-setup-entry-point"></a>Configurar o ponto de entrada da configuração do serviço
Segue-se um simples exemplo de manifesto de serviço para um serviço apátrida que especifica um script de configuração *MySetup.bat* no serviço **SetupEntryPoint**.  **Os argumentos** são usados para passar argumentos para o guião quando corre.

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
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Configure a política para um ponto de entrada de configuração de serviço
Por predefinição, o ponto de entrada de configuração do serviço executado corre sob as mesmas credenciais que o Tecido de Serviço (normalmente a conta *NetworkService).*  No manifesto de aplicação, pode alterar as permissões de segurança para executar o script de arranque sob uma conta de sistema local ou uma conta de administrador.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Configure a política utilizando uma conta de sistema local
O exemplo manifesto de aplicação seguinte mostra como configurar o ponto de entrada de configuração do serviço para executar sob a conta do administrador do utilizador (ConfiguraçãoAdminUser).

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

Em primeiro lugar, crie uma secção **de Principados** com um nome de utilizador, como ConfiguraçãoAdminUser. A conta de utilizador Do ConfiguraçãoAdminUser é membro do grupo de sistemas administradores.

Em seguida, no âmbito da secção **ServiceManifestImport,** configure uma política para aplicar este principal ao **SetupEntryPoint**. Esta política diz ao Service Fabric que quando o ficheiro **MySetup.bat** é executado deve ser executado como SetupAdminUser (com privilégios de administrador). Uma vez que *não* aplicou uma apólice ao ponto de entrada principal, o código em **MyServiceHost.exe** passa pela conta **NetworkService** do sistema. Esta é a conta predefinida que todos os pontos de entrada de serviço são executados como.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Configure a política utilizando contas do sistema local
Muitas vezes, é preferível executar o script de arranque usando uma conta de sistema local em vez de uma conta de administrador. Executar a política RunAs como membro do grupo Administradores normalmente não funciona bem porque os computadores têm controlo de acesso ao utilizador (UAC) ativado por padrão. Nestes casos, a recomendação é executar o SetupEntryPoint como LocalSystem, em vez de como um utilizador local adicionado ao grupo administradores. O exemplo seguinte mostra a definição do SetupEntryPoint para funcionar como LocalSystem:

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
> Para os clusters Linux, para executar um serviço ou o ponto de entrada de configuração como **raiz,** pode especificar o **AccountType** como **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Executar um script a partir do ponto de entrada de configuração
Adicione agora um guião inicial ao projeto para executar sob privilégios de administrador. 

No Visual Studio, clique no projeto de serviço e adicione um novo ficheiro chamado *MySetup.bat*.

Em seguida, certifique-se de que o ficheiro *MySetup.bat* está incluído no pacote de serviço. Por defeito, não é. Selecione o ficheiro, clique à direita para obter o menu de contexto e escolha **Propriedades**. Na caixa de diálogo Properties, certifique-se de que **o Diretório de Cópia para Saída** está definido para copiar se for mais **recente**. Veja a captura de ecrã abaixo.

![Estúdio Visual CopyToOutput para ficheiro de lote De ConfiguraçãoEntryPoint][image1]

Agora edite o ficheiro *MySetup.bat* e adicione os seguintes comandos definir uma variável ambiente do sistema e saída um ficheiro de texto:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Em seguida, construir e implementar a solução para um cluster de desenvolvimento local. Depois de o serviço ter começado, como mostra o [Service Fabric Explorer,](service-fabric-visualizing-your-cluster.md)pode ver que o ficheiro MySetup.bat foi bem sucedido de duas formas. Abra um pedido de comando PowerShell e escreva:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Em seguida, note o nome do nó onde o serviço foi implantado e iniciado no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Por exemplo, nó 2. Em seguida, navegue para a pasta de trabalho da instância de aplicação para encontrar o ficheiro out.txt que mostra o valor do **TestVariable**. Por exemplo, se este serviço foi implantado no nó 2, então pode ir para este caminho para o **MyApplicationType:**

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Executar comandos PowerShell a partir de um ponto de entrada de configuração
Para executar o PowerShell a partir do ponto **SetupEntryPoint,** pode executar **PowerShell.exe** num ficheiro de lote que aponta para um ficheiro PowerShell. Em primeiro lugar, adicione um ficheiro PowerShell ao projeto de serviço-- por exemplo, **MySetup.ps1**. Lembre-se de definir a cópia se a propriedade *mais recente* fora para que o ficheiro também esteja incluído no pacote de serviço. O exemplo seguinte mostra um ficheiro de lote de amostra que inicia um ficheiro PowerShell chamado MySetup.ps1, que define uma variável de ambiente de sistema chamada **TestVariable**.

MySetup.bat para iniciar um ficheiro PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

No ficheiro PowerShell, adicione o seguinte para definir uma variável ambiente do sistema:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Por predefinição, quando o ficheiro do lote é executado, olha para a pasta de aplicação chamada **trabalho** para ficheiros. Neste caso, quando o MySetup.bat corre, queremos que este encontre o ficheiro MySetup.ps1 na mesma pasta, que é a pasta do pacote de **código** de aplicação. Para alterar esta pasta, desloque a pasta de trabalho:
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

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Depurar um script de arranque localmente usando a redirecção da consola
Ocasionalmente, é útil para depurar propósitos para ver a saída da consola de executar um script de configuração. Pode definir uma política de reorientação da consola no ponto de entrada de configuração no manifesto de serviço, que escreve a saída para um ficheiro. A saída do ficheiro está escrita na pasta de aplicação chamada **log** no nó do cluster onde a aplicação é implementada e executada. 

> [!WARNING]
> Nunca utilize a política de reorientação da consola numa aplicação que é implantada na produção porque isso pode afetar o failover da aplicação. *Utilize-o apenas* para fins de desenvolvimento local e depuração.  
> 
> 

O seguinte exemplo manifesto de serviço mostra definir a redirecção da consola com um valor FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Se alterar agora o ficheiro MySetup.ps1 para escrever um comando **Echo,** este escreverá para o ficheiro de saída para fins de depuração:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Depois de depurar o seu script, remova imediatamente esta política de reorientação da consola.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* [Conheça a aplicação e segurança do serviço](service-fabric-application-and-service-security.md)
* [Compreender o modelo de aplicação](service-fabric-application-model.md)
* [Especificar recursos num manifesto de serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
