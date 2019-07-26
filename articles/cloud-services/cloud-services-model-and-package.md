---
title: O que é um modelo e pacote de serviço de nuvem | Microsoft Docs
description: Descreve o modelo de serviço de nuvem (. csdef,. cscfg) e o pacote (. cspkg) no Azure
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: gwallace
ms.openlocfilehash: 47d031e339b3677e0bf6ddcbad9456041c53c6e2
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359549"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>O que é o modelo de serviço de nuvem e como empacotá-lo?
Um serviço de nuvem é criado a partir de três componentes, a definição de serviço *(. csdef)* , a configuração do serviço *(. cscfg)* e um pacote de serviço *(. cspkg)* . Os arquivos Service **Definition. csdef** e **ServiceConfig. cscfg** são baseados em XML e descrevem a estrutura do serviço de nuvem e como ele é configurado; coletivamente chamado de modelo. O **cspkg** é um arquivo zip que é gerado a partir de um **webdefinition. csdef** e entre outras coisas, contém todas as dependências necessárias baseadas em binário. O Azure cria um serviço de nuvem por meio de Service **Package. cspkg** e **ServiceConfig. cscfg**.

Depois que o serviço de nuvem estiver em execução no Azure, você poderá reconfigurá-lo por meio do arquivo **ServiceConfig. cscfg** , mas não poderá alterar a definição.

## <a name="what-would-you-like-to-know-more-about"></a>Como você gostaria de saber mais?
* Quero saber mais sobre os arquivos. [csdef](#csdef) e [ServiceConfig. cscfg](#cscfg) .
* Já sei disso, dê-me [alguns exemplos](#next-steps) sobre o que posso configurar.
* Quero criar o [pacote. cspkg](#cspkg).
* Estou usando o Visual Studio e quero...
  * [Criar um serviço de nuvem][vs_create]
  * [Reconfigurar um serviço de nuvem existente][vs_reconfigure]
  * [Implantar um projeto de serviço de nuvem][vs_deploy]
  * [Área de trabalho remota em uma instância de serviço de nuvem][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
O arquivo de Service **Definition. csdef** especifica as configurações que são usadas pelo Azure para configurar um serviço de nuvem. O [esquema de definição de serviço do Azure (arquivo. csdef)](/previous-versions/azure/reference/ee758711(v=azure.100)) fornece o formato permitido para um arquivo de definição de serviço. O exemplo a seguir mostra as configurações que podem ser definidas para as funções Web e de trabalho:

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

Você pode consultar o [esquema de definição de serviço](/previous-versions/azure/reference/ee758711(v=azure.100)) para ter uma melhor compreensão do esquema XML usado aqui. no entanto, aqui está uma explicação rápida de alguns dos elementos:

**Sites**  
Contém as definições para sites ou aplicativos Web hospedados no IIS7.

**InputEndpoints**  
Contém as definições para pontos de extremidade que são usados para contatar o serviço de nuvem.

**InternalEndpoints**  
Contém as definições para pontos de extremidade que são usados por instâncias de função para se comunicar entre si.

**ConfigurationSettings**  
Contém as definições de configuração para recursos de uma função específica.

**Certificados**  
Contém as definições para certificados que são necessários para uma função. O exemplo de código anterior mostra um certificado que é usado para a configuração do Azure Connect.

**LocalResources**  
Contém as definições para recursos de armazenamento local. Um recurso de armazenamento local é um diretório reservado no sistema de arquivos da máquina virtual na qual uma instância de uma função está sendo executada.

**Importar**  
Contém as definições para módulos importados. O exemplo de código anterior mostra os módulos para Conexão de Área de Trabalho Remota e o Azure Connect.

**Inicialização**  
Contém tarefas que são executadas quando a função é iniciada. As tarefas são definidas em um arquivo. cmd ou executável.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
A configuração das configurações para seu serviço de nuvem é determinada pelos valores no arquivo **. cscfg** do Service Configuration. Você especifica o número de instâncias que deseja implantar para cada função neste arquivo. Os valores para as definições de configuração que você definiu no arquivo de definição de serviço são adicionados ao arquivo de configuração de serviço. As impressões digitais para quaisquer certificados de gerenciamento associados ao serviço de nuvem também são adicionadas ao arquivo. O [esquema de configuração do serviço do Azure (arquivo. cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100)) fornece o formato permitido para um arquivo de configuração de serviço.

O arquivo de configuração de serviço não é empacotado com o aplicativo, mas é carregado no Azure como um arquivo separado e é usado para configurar o serviço de nuvem. Você pode carregar um novo arquivo de configuração de serviço sem reimplantar o serviço de nuvem. Os valores de configuração para o serviço de nuvem podem ser alterados enquanto o serviço de nuvem está em execução. O exemplo a seguir mostra as definições de configuração que podem ser definidas para as funções Web e de trabalho:

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

Você pode consultar o [esquema de configuração do serviço](/previous-versions/azure/reference/ee758710(v=azure.100)) para entender melhor o esquema XML usado aqui, mas aqui está uma explicação rápida dos elementos:

**Ocasiões**  
Configura o número de instâncias em execução para a função. Para impedir que o serviço de nuvem fique indisponível durante as atualizações, é recomendável implantar mais de uma instância de suas funções voltadas para a Web. Ao implantar mais de uma instância, você está cumprindo as diretrizes no [SLA (contrato de nível de serviço de computação) do Azure](https://azure.microsoft.com/support/legal/sla/), que garante a 99,95% de conectividade externa para funções voltadas para a Internet quando duas ou mais instâncias de função são implantadas para um serviço .

**ConfigurationSettings**  
Define as configurações para as instâncias em execução para uma função. O nome dos `<Setting>` elementos deve corresponder às definições de configuração no arquivo de definição de serviço.

**Certificados**  
Configura os certificados que são usados pelo serviço. O exemplo de código anterior mostra como definir o certificado para o módulo RemoteAccess. O valor do atributo de *impressão digital* deve ser definido como a impressão digital do certificado a ser usado.

<p/>

> [!NOTE]
> A impressão digital do certificado pode ser adicionada ao arquivo de configuração usando um editor de texto. Ou, o valor pode ser adicionado na guia **certificados** da página **Propriedades** da função no Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definindo portas para instâncias de função
O Azure permite apenas um ponto de entrada para uma função Web. O que significa que todo o tráfego ocorre por meio de um endereço IP. Você pode configurar seus sites para compartilhar uma porta Configurando o cabeçalho de host para direcionar a solicitação para o local correto. Você também pode configurar seus aplicativos para escutar portas conhecidas no endereço IP.

O exemplo a seguir mostra a configuração de uma função Web com um site e um aplicativo Web. O site é configurado como o local de entrada padrão na porta 80 e os aplicativos Web são configurados para receber solicitações de um cabeçalho de host alternativo chamado "mail.mysite.cloudapp.net".

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
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Alterando a configuração de uma função
Você pode atualizar a configuração do seu serviço de nuvem enquanto ele está em execução no Azure, sem colocar o serviço offline. Para alterar as informações de configuração, você pode carregar um novo arquivo de configuração ou editar o arquivo de configuração no local e aplicá-lo ao serviço em execução. As seguintes alterações podem ser feitas na configuração de um serviço:

* **Alterando os valores das definições de configuração**  
  Quando uma definição de configuração é alterada, uma instância de função pode optar por aplicar a alteração enquanto a instância estiver online ou para reciclar a instância normalmente e aplicar a alteração enquanto a instância estiver offline.
* **Alterando a topologia de serviço das instâncias de função**  
  As alterações de topologia não afetam as instâncias em execução, exceto onde uma instância está sendo removida. Todas as instâncias restantes geralmente não precisam ser recicladas; no entanto, você pode optar por reciclar instâncias de função em resposta a uma alteração de topologia.
* **Alterando a impressão digital do certificado**  
  Você só pode atualizar um certificado quando uma instância de função está offline. Se um certificado for adicionado, excluído ou alterado enquanto uma instância de função estiver online, o Azure usará a instância offline para atualizar o certificado e o colocará online novamente após a conclusão da alteração.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Manipulando alterações de configuração com eventos de tempo de execução de serviço
A [biblioteca de tempo de execução do Azure](/previous-versions/azure/reference/mt419365(v=azure.100)) inclui o namespace [Microsoft. WindowsAzure. inruntime](/previous-versions/azure/reference/ee741722(v=azure.100)) , que fornece classes para interagir com o ambiente do Azure de uma função. A classe [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) define os seguintes eventos que são gerados antes e depois de uma alteração de configuração:

* **[Alterando](/previous-versions/azure/reference/ee758134(v=azure.100)) evento**  
  Isso ocorre antes que a alteração de configuração seja aplicada a uma instância especificada de uma função, dando a oportunidade de desativar as instâncias de função, se necessário.
* **Evento [alterado](/previous-versions/azure/reference/ee758129(v=azure.100))**  
  Ocorre depois que a alteração de configuração é aplicada a uma instância especificada de uma função.

> [!NOTE]
> Como as alterações de certificado sempre usam as instâncias de uma função offline, elas não geram os eventos RoleEnvironment. Changing ou RoleEnvironment. Changed.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Para implantar um aplicativo como um serviço de nuvem no Azure, você deve primeiro empacotar o aplicativo no formato apropriado. Você pode usar a ferramenta de linha de comando **CSPack** (instalada com o [SDK do Azure](https://azure.microsoft.com/downloads/)) para criar o arquivo de pacote como uma alternativa ao Visual Studio.

**CSPack** usa o conteúdo do arquivo de definição de serviço e o arquivo de configuração de serviço para definir o conteúdo do pacote. O **CSPack** gera um arquivo de pacote de aplicativos (. cspkg) que você pode carregar no Azure usando o [portal do Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Por padrão, o pacote é nomeado `[ServiceDefinitionFileName].cspkg`, mas você pode especificar um nome diferente usando a `/out` opção de **CSPack**.

**CSPack** está localizado em  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> O CSPack. exe (no Windows) está disponível executando o **Microsoft Azure** atalho de prompt de comando que é instalado com o SDK.  
> 
> Execute o programa CSPack. exe sozinho para ver a documentação sobre todos os comandos e opções possíveis.
> 
> 

<p />

> [!TIP]
> Execute o serviço de nuvem localmente no **emulador de computação Microsoft Azure**, use a opção **/copyOnly** . Essa opção copia os arquivos binários do aplicativo para um layout de diretório do qual eles podem ser executados no emulador de computação.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Exemplo de comando para empacotar um serviço de nuvem
O exemplo a seguir cria um pacote de aplicativos que contém as informações de uma função Web. O comando especifica o arquivo de definição de serviço a ser usado, o diretório em que os arquivos binários podem ser encontrados e o nome do arquivo de pacote.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Se o aplicativo contiver uma função Web e uma função de trabalho, o seguinte comando será usado:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Onde as variáveis são definidas da seguinte maneira:

| Variável | Value |
| --- | --- |
| \[DirectoryName\] |O subdiretório no diretório do projeto raiz que contém o arquivo. csdef do projeto do Azure. |
| \[ServiceDefinition\] |O nome do arquivo de definição de serviço. Por padrão, esse arquivo é chamado de userdefinition. csdef. |
| \[OutputFileName\] |O nome do arquivo de pacote gerado. Normalmente, isso é definido como o nome do aplicativo. Se nenhum nome de arquivo for especificado, o pacote de aplicativo será \[criado\]como ApplicationName. cspkg. |
| \[RoleName\] |O nome da função, conforme definido no arquivo de definição de serviço. |
| \[RoleBinariesDirectory] |O local dos arquivos binários para a função. |
| \[VirtualPath\] |Os diretórios físicos de cada caminho virtual definido na seção sites da definição de serviço. |
| \[PhysicalPath\] |Os diretórios físicos do conteúdo para cada caminho virtual definido no nó do site da definição de serviço. |
| \[RoleAssemblyName\] |O nome do arquivo binário para a função. |

## <a name="next-steps"></a>Passos Seguintes
Estou criando um pacote de serviço de nuvem e quero...

* [Configurar a área de trabalho remota para uma instância de serviço de nuvem][remotedesktop]
* [Implantar um projeto de serviço de nuvem][deploy]

Estou usando o Visual Studio e quero...

* [Criar um novo serviço de nuvem][vs_create]
* [Reconfigurar um serviço de nuvem existente][vs_reconfigure]
* [Implantar um projeto de serviço de nuvem][vs_deploy]
* [Configurar a área de trabalho remota para uma instância de serviço de nuvem][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
