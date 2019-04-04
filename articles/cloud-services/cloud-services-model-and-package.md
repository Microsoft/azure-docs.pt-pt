---
title: O que é um modelo de serviço em nuvem e um pacote | Documentos da Microsoft
description: Descreve o modelo de serviço de nuvem (. csdef,. cscfg) e do pacote (. cspkg) no Azure
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 4ce2feb5-0437-496c-98da-1fb6dcb7f59e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 9c9f7dfd9ecbf085da19fc010e497caef8c18629
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917316"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>O que é o modelo de serviço em nuvem e como empacotá-lo?
Um serviço em nuvem é criado a partir de três componentes, a definição de serviço *(. csdef)*, a configuração de serviço *(. cscfg)* e um pacote de serviço *(. cspkg)*. Ambas as **servicedefinition. Csdef** e **ServiceConfig.cscfg** ficheiros são baseados em XML e descrever a estrutura de serviço em nuvem e de configuração; coletivamente chamados o modelo. O **ServicePackage.cspkg** é um ficheiro zip que é gerado a partir do **servicedefinition. Csdef** e entre outras coisas, contém todos os com base em binário dependências necessárias. O Azure cria um serviço em nuvem, tanto o **ServicePackage.cspkg** e o **ServiceConfig.cscfg**.

Quando o serviço em nuvem está em execução no Azure, pode reconfigurá-lo através da **ServiceConfig.cscfg** ficheiro, mas não é possível alterar a definição.

## <a name="what-would-you-like-to-know-more-about"></a>O que deseja saber mais sobre?
* Quero saber mais sobre o [servicedefinition. Csdef](#csdef) e [ServiceConfig.cscfg](#cscfg) ficheiros.
* Já sabe sobre isso, dê-me [alguns exemplos](#next-steps) sobre o que posso configurar.
* Para criar o [ServicePackage.cspkg](#cspkg).
* Estou usando o Visual Studio e pretendo...
  * [Criar um serviço cloud][vs_create]
  * [Reconfigurar um serviço cloud existente][vs_reconfigure]
  * [Implementar um projeto de serviço em nuvem][vs_deploy]
  * [Ambiente de trabalho remoto numa instância de serviço cloud][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
O **servicedefinition. Csdef** ficheiro Especifica as definições que são utilizadas pelo Azure para configurar um serviço em nuvem. O [esquema de definição de serviço do Azure (. csdef ficheiro)](/previous-versions/azure/reference/ee758711(v=azure.100)) fornece o formato permitido para um ficheiro de definição de serviço. O exemplo seguinte mostra as definições que podem ser definidas para as funções Web e de trabalho:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Pode consultar o [esquema de definição de serviço](/previous-versions/azure/reference/ee758711(v=azure.100)) para uma melhor compreensão do esquema XML usado aqui, no entanto, aqui está uma explicação rápida de alguns dos elementos:

**Sites**  
Contém as definições para os Web sites ou aplicações web alojadas no IIS7.

**InputEndpoints**  
Contém as definições de pontos finais que são utilizadas para contactar o serviço de nuvem.

**InternalEndpoints**  
Contém as definições de pontos finais que são utilizados pelas instâncias de função para comunicar entre si.

**ConfigurationSettings**  
Contém as definições de configuração para as funcionalidades de uma função específica.

**Certificados**  
Contém as definições para certificados que são necessários para uma função. O exemplo de código anterior mostra um certificado que é utilizado para a configuração do Azure Connect.

**LocalResources**  
Contém as definições para os recursos de armazenamento local. Um recurso de armazenamento local é um diretório reservado no sistema de ficheiros da máquina virtual em que uma instância de uma função está em execução.

**Importações**  
Contém as definições para módulos importados. Exemplo de código anterior mostra os módulos para conexão de área de trabalho remoto e o Azure Connect.

**Arranque**  
Contém tarefas que estão a ser executadas quando a função for iniciada. As tarefas são definidas num ficheiro executável ou. cmd.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
A configuração das definições do serviço em nuvem é determinada pelos valores na **serviceconfiguration. Cscfg** ficheiro. Especifique o número de instâncias que pretende implementar para cada função neste ficheiro. Os valores para as definições de configuração que definiu no ficheiro de definição de serviço são adicionados ao arquivo de configuração de serviço. Os thumbprints para quaisquer certificados de gestão que estão associados com o serviço em nuvem também são adicionados ao ficheiro. O [esquema de configuração de serviço do Azure (. cscfg ficheiro)](/previous-versions/azure/reference/ee758710(v=azure.100)) fornece o formato permitido para um ficheiro de configuração do serviço.

O ficheiro de configuração de serviço não é empacotado com o aplicativo, mas é carregado para o Azure como um arquivo separado e é utilizado para configurar o serviço em nuvem. Pode carregar um novo ficheiro de configuração de serviço sem implementar novamente o seu serviço cloud. Os valores de configuração para o serviço cloud podem ser alterados enquanto o serviço em nuvem está em execução. O exemplo seguinte mostra as definições de configuração que podem ser definidas para as funções Web e de trabalho:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Pode consultar o [esquema de configuração do serviço](/previous-versions/azure/reference/ee758710(v=azure.100)) para melhor compreender o esquema XML utilizado aqui, no entanto, aqui está uma explicação rápida dos elementos:

**Instâncias**  
Configura o número de instâncias para a função em execução. Para impedir que o seu serviço cloud potencialmente ficarem indisponíveis durante as atualizações, recomenda-se que implemente mais de uma instância de suas funções com acesso à web. Ao implementar mais de uma instância, é a cumprir as diretrizes a [do Azure de computação ao nível do contrato serviço (SLA)](https://azure.microsoft.com/support/legal/sla/), que garante a conectividade externa de 99,95% para as funções de acesso à Internet quando dois ou mais instâncias de função são implementada para um serviço.

**ConfigurationSettings**  
Configura as definições para as instâncias em execução para uma função. O nome do `<Setting>` elementos têm de corresponder as definições de configuração no ficheiro de definição do serviço.

**Certificados**  
Configura os certificados que são utilizados pelo serviço. O exemplo de código anterior mostra como definir o certificado para o módulo de RemoteAccess. O valor do *thumbprint* atributo tem de ser definido para o thumbprint do certificado a utilizar.

<p/>

> [!NOTE]
> O thumbprint do certificado pode ser adicionado ao arquivo de configuração utilizando um editor de texto. Ou, o valor pode ser adicionado sobre o **certificados** separador do **propriedades** página da função no Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definir portas para instâncias de função
Azure permite que o ponto de entrada apenas uma função da web. O que significa que todo o tráfego ocorre por meio de um endereço IP. Pode configurar os Web sites para partilhar uma porta ao configurar o cabeçalho de anfitrião para direcionar a solicitação para a localização correta. Também pode configurar seus aplicativos para ouça as conhecidas portas no endereço IP.

O exemplo a seguir mostra a configuração de uma função da web com uma aplicação web e de Web site. O Web site está configurado como a localização de entrada predefinida na porta 80 e as aplicações web estão configuradas para receber pedidos de um cabeçalho de anfitrião alternativo que é chamado "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostheader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Alteração da configuração de uma função
Pode atualizar a configuração do seu serviço cloud durante a execução no Azure, sem que o serviço offline. Para alterar as informações de configuração, pode carregar um novo arquivo de configuração, ou editar o ficheiro de configuração no local e aplicá-la ao seu serviço em execução. As seguintes alterações podem ser feitas para a configuração de um serviço:

* **Alterando os valores das definições de configuração**  
  Quando uma definição de alterações de configuração, uma instância de função pode optar por aplicar a alteração, enquanto a instância está online ou a reciclar a instância corretamente e aplicar a alteração ao mesmo tempo a instância está offline.
* **Alterar a topologia de serviço de instâncias de função**  
  Alterações de topologia não afetam as instâncias em execução, exceto em que uma instância está a ser removida. Todas as instâncias restantes geralmente não precisam de ser reciclado; No entanto, pode optar por reciclar instâncias de função em resposta a uma alteração de topologia.
* **Alterar o thumbprint do certificado**  
  Apenas pode atualizar um certificado quando uma instância de função está offline. Se um certificado é adicionado, eliminado ou alterado enquanto uma instância de função está online, Azure normalmente demora a instância de offline para atualizar o certificado e colocá-lo novamente online após concluir a alteração.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Lidar com alterações de configuração com eventos de tempo de execução do serviço
O [biblioteca de tempo de execução do Azure](/previous-versions/azure/reference/mt419365(v=azure.100)) inclui a [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee741722(v=azure.100)) espaço de nomes, que fornece classes para interação com o ambiente do Azure a partir de uma função. O [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) classe define os seguintes eventos são gerados antes e depois uma alteração de configuração:

* **[Alterar](/previous-versions/azure/reference/ee758134(v=azure.100)) eventos**  
  Isto ocorre antes da alteração de configuração é aplicada a uma instância especificada de uma função, dando-lhe uma oportunidade de se desliga as instâncias de função, se necessário.
* **[Alterado](/previous-versions/azure/reference/ee758129(v=azure.100)) eventos**  
  Ocorre após a alteração de configuração é aplicada a uma instância especificada de uma função.

> [!NOTE]
> Uma vez que as alterações de certificado colocar sempre as instâncias de uma função offline, eles não gerou os eventos RoleEnvironment.Changing ou RoleEnvironment.Changed.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Para implementar uma aplicação como um serviço cloud no Azure, primeiro deve empacotar o aplicativo no formato apropriado. Pode utilizar o **CSPack** ferramenta da linha de comandos (instalado com o [SDK do Azure](https://azure.microsoft.com/downloads/)) para criar o ficheiro de pacote como uma alternativa ao Visual Studio.

**CSPack** usa o conteúdo do ficheiro de definição de serviço e ficheiro de configuração de serviço para definir o conteúdo do pacote. **CSPack** gera um ficheiro de pacote de aplicação (. cspkg) que pode carregar para o Azure com o [portal do Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Por predefinição, o pacote é denominado `[ServiceDefinitionFileName].cspkg`, mas pode especificar um nome diferente utilizando o `/out` opção de **CSPack**.

**CSPack** está localizado em  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (no windows) está disponível ao executar o **linha de comandos do Microsoft Azure** atalho que é instalado com o SDK.  
> 
> Execute o programa de CSPack.exe por si só para ver a documentação sobre todos os comutadores possíveis e comandos.
> 
> 

<p />

> [!TIP]
> Executar o seu serviço cloud localmente na **emulador de computação do Microsoft Azure**, utilize o **/copyonly** opção. Esta opção copia os ficheiros binários para a aplicação para um esquema de diretório a partir do qual podem ser executadas no emulador de computação.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Comando de exemplo para empacotar um serviço em nuvem
O exemplo seguinte cria um pacote de aplicação que contém as informações para uma função da web. O comando Especifica o ficheiro de definição de serviço para utilizar, o diretório onde os ficheiros binários podem ser encontrados, e o nome do ficheiro de pacote.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Se a aplicação contém uma função da web e uma função de trabalho, é utilizado o seguinte comando:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Onde as variáveis são definidas da seguinte forma:

| Variável | Valor |
| --- | --- |
| \[DirectoryName\] |O subdiretório sob o diretório de raiz do projeto que contém o ficheiro. csdef do projeto do Azure. |
| \[ServiceDefinition\] |O nome do ficheiro de definição de serviço. Por predefinição, este arquivo é intitulado servicedefinition. Csdef. |
| \[OutputFileName\] |O nome do ficheiro de pacote gerado. Normalmente, isso é definido como o nome da aplicação. Se não for especificado nenhum nome de ficheiro, o pacote de aplicação é criado como \[ApplicationName\]. cspkg. |
| \[RoleName\] |O nome da função, conforme definido no ficheiro de definição do serviço. |
| \[RoleBinariesDirectory] |A localização dos ficheiros binários para a função. |
| \[VirtualPath\] |Os diretórios físicos para cada caminho virtual definido na secção de locais de definição do serviço. |
| \[PhysicalPath\] |Os diretórios físicos do conteúdo para cada caminho virtual definido no nó de site da definição de serviço. |
| \[RoleAssemblyName\] |O nome do ficheiro binário para a função. |

## <a name="next-steps"></a>Passos Seguintes
Estou criando um pacote de serviço de nuvem e pretendo...

* [Configurar o ambiente de trabalho remoto para uma instância de serviço cloud][remotedesktop]
* [Implementar um projeto de serviço em nuvem][deploy]

Estou usando o Visual Studio e pretendo...

* [Criar um novo serviço cloud][vs_create]
* [Reconfigurar um serviço cloud existente][vs_reconfigure]
* [Implementar um projeto de serviço em nuvem][vs_deploy]
* [Configurar o ambiente de trabalho remoto para uma instância de serviço cloud][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
