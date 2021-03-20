---
title: Executar um script quando um serviço de tecido de serviço Azure começar
description: Saiba como configurar uma política para um ponto de entrada de configuração de serviço de serviço de Serviço e executar um script na hora de arranque do serviço.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012532"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Executar um script de arranque do serviço como uma conta de utilizador local ou conta de sistema
Antes de um serviço de tecido de serviço ser executado, pode ser necessário executar algum trabalho de configuração ou configuração.  Por exemplo, configurar variáveis ambientais. Pode especificar um script a ser executado antes do serviço executável começar no manifesto de serviço para o serviço. Ao configurar uma política RunAs para o ponto de entrada de configuração de serviço, pode alterar qual a conta que a configuração executa.  Um ponto de entrada de configuração separado permite-lhe executar uma configuração privilegiada por um curto período de tempo para que o anfitrião de serviço não precise de correr com privilégios elevados por longos períodos de tempo.

O ponto de entrada de configuração **(SetupEntryPoint** no manifesto de [serviço](service-fabric-application-and-service-manifests.md)) é um ponto de entrada privilegiado que, por padrão, funciona com as mesmas credenciais que o Service Fabric (normalmente a conta *NetworkService)* antes de qualquer outro ponto de entrada. O executável que é especificado pelo **EntryPoint** é tipicamente o anfitrião de serviço de longa duração. O **EntryPoint** executável é executado após a execução do **SetupEntryPoint** com sucesso. O processo resultante é monitorizado e reiniciado, e recomeça com **o SetupEntryPoint** se alguma vez terminar ou falhar. 

## <a name="configure-the-service-setup-entry-point"></a>Configurar o ponto de entrada da configuração do serviço
O seguinte é um simples exemplo de manifesto de serviço para um serviço apátrida que especifica um script de configuração *MySetup.bat* no serviço **SetupEntryPoint**.  **Os argumentos** são usados para passar argumentos para o guião quando é executado.

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
Por predefinição, o ponto de entrada de configuração de serviço executa com as mesmas credenciais que o Service Fabric (normalmente a conta *NetworkService).*  No manifesto de aplicação, pode alterar as permissões de segurança para executar o script de arranque numa conta do sistema local ou numa conta de administrador.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Configure a política utilizando uma conta do sistema local
O exemplo manifesto da aplicação que se segue mostra como configurar o ponto de entrada de configuração do serviço para ser executado na conta de administrador do utilizador (SetupAdminUser).

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

Em primeiro lugar, crie uma secção **principal** com um nome de utilizador, como o SetupAdminUser. A conta de utilizador SetupAdminUser é membro do grupo de sistema Administradores.

Em seguida, sob a secção **ServiceManifestImport,** configurar uma política para aplicar este principal ao **SetupEntryPoint**. Esta política diz ao Service Fabric que quando o ficheiro **MySetup.bat** é executado deve funcionar como SetupAdminUser (com privilégios de administrador). Uma vez que *não* aplicou uma política ao ponto de entrada principal, o código em **MyServiceHost.exe** funciona sob a conta **networkService** do sistema. Esta é a conta padrão que todos os pontos de entrada de serviço são executados como.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Configure a política utilizando contas do sistema local
Muitas vezes, é preferível executar o script de arranque usando uma conta do sistema local em vez de uma conta de administrador. Executar a política RunAs como membro do grupo de Administradores normalmente não funciona bem porque os computadores têm o Controlo de Acesso ao Utilizador (UAC) ativado por padrão. Nestes casos, a recomendação é executar o SetupEntryPoint como LocalSystem, em vez de como um utilizador local adicionado ao grupo de Administradores. O exemplo a seguir mostra a definição do SetupEntryPoint para funcionar como Sistema Local:

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
> Para os clusters Linux, para executar um serviço ou o ponto de entrada de configuração como **raiz,** pode especificar o  **'AccountType'** como **Sistema Local**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Executar um script a partir do ponto de entrada de configuração
Agora adicione um script de arranque ao projeto para executar sob privilégios de administrador. 

No Visual Studio, clique à direita no projeto de serviço e adicione um novo ficheiro chamado *MySetup.bat*.

Em seguida, certifique-se de que o ficheiro *MySetup.bat* está incluído no pacote de serviço. Por defeito, não é. Selecione o ficheiro, clique com o botão direito para obter o menu de contexto e escolha **Propriedades**. Na caixa de diálogo Properties, certifique-se de que **o Copy to Output Directory** está definido para copiar se for mais **recente**. Veja a captura de ecrã abaixo.

![CopyToOutput do estúdio visual para ficheiro de lote configuradoPoint][image1]

Agora edite o ficheiro *MySetup.bat* e adicione os seguintes comandos definir uma variável ambiente do sistema e desemordenar um ficheiro de texto:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Em seguida, construa e implemente a solução para um cluster de desenvolvimento local. Depois de o serviço ter começado, como mostra o [Service Fabric Explorer,](service-fabric-visualizing-your-cluster.md)pode ver que o ficheiro MySetup.bat foi bem sucedido de duas formas. Abra uma pronta e tipo de comando PowerShell:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Em seguida, note o nome do nó onde o serviço foi implantado e iniciado no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Por exemplo, Nó 2. Em seguida, navegue na pasta de trabalho de instância de aplicação para encontrar o ficheiro out.txt que mostre o valor do **TestVariable**. Por exemplo, se este serviço foi implantado no Nó 2, então você pode ir a este caminho para o **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Executar comandos PowerShell a partir de um ponto de entrada de configuração
Para executar o PowerShell a partir do ponto **SetupEntryPoint,** pode **executáPowerShell.exe** num ficheiro de lote que aponta para um ficheiro PowerShell. Em primeiro lugar, adicione um ficheiro PowerShell ao projeto de serviço -- por exemplo, **MySetup.ps1**. Lembre-se de definir a Cópia se a propriedade *mais recente* para que o ficheiro também esteja incluído no pacote de serviço. O exemplo a seguir mostra um ficheiro de lote de amostra que inicia um ficheiro PowerShell chamado MySetup.ps1, que define uma variável ambiente do sistema chamada **TestVariable**.

MySetup.bat iniciar um ficheiro PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

No ficheiro PowerShell, adicione o seguinte para definir uma variável ambiente do sistema:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Por predefinição, quando o ficheiro do lote é executado, olha para a pasta de aplicação chamada **trabalho** para ficheiros. Neste caso, quando MySetup.bat é executado, queremos que este encontre o ficheiro MySetup.ps1 na mesma pasta, que é a pasta do **pacote de código** de aplicação. Para alterar esta pasta, desa estada de trabalho:
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

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Depurar um script de startup localmente usando a redirecionamento da consola
Ocasionalmente, é útil para depurar propósitos para ver a saída da consola executar um script de configuração. Pode definir uma política de redirecionamento da consola no ponto de entrada de configuração no manifesto de serviço, que escreve a saída para um ficheiro. A saída do ficheiro é escrita para a pasta de aplicação chamada **log** no nó de cluster onde a aplicação é implantada e executada. 

> [!WARNING]
> Nunca utilize a política de redirecionamento da consola numa aplicação que seja implementada na produção porque isso pode afetar o failover da aplicação. *Utilize-o apenas* para fins de desenvolvimento local e depuragem.  
> 
> 

O exemplo manifesto de serviço que se segue mostra a definição da reorientação da consola com um valor FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Se agora alterar o ficheiro MySetup.ps1 para escrever um comando **Echo,** este escreverá no ficheiro de saída para fins de depurar:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Depois de depurar o seu script, remova imediatamente esta política de redirecionamento da consola.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre aplicação e segurança de serviços](service-fabric-application-and-service-security.md)
* [Compreender o modelo de aplicação](service-fabric-application-model.md)
* [Especificar recursos num manifesto de serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
