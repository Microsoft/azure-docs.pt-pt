---
title: O que é um modelo e pacote de serviço sinuoso do Serviço cloud Microsoft Docs
description: Descreve o modelo de serviço em nuvem (.csdef, .cscfg) e o pacote (.cspkg) em Azure
services: cloud-services
author: tanmaygore
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 32603f4ab33e020245861e5dc66d2ade545fa627
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148314"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>O que é o modelo do Serviço cloud e como o embala?
Um serviço na nuvem é criado a partir de três componentes, a definição de serviço *(.csdef)* , o config de serviço *(.cscfg)* e um pacote de serviço *(.cspkg)* . Tanto os **ficheiros ServiceDefinition.csdef** e **ServiceConfig.cscfg** são baseados em XML e descrevem a estrutura do serviço de nuvem e como é configurado; coletivamente chamado o modelo. O **ServicePackage.cspkg** é um ficheiro zip que é gerado a partir do **ServiceDefinition.csdef** e, entre outras coisas, contém todas as dependências binárias necessárias. O Azure cria um serviço na nuvem tanto do **ServicePackage.cspkg** como do **ServiceConfig.cscfg**.

Uma vez que o serviço de nuvem esteja em funcionamento em Azure, pode reconfigurá-lo através do ficheiro **ServiceConfig.cscfg,** mas não pode alterar a definição.

## <a name="what-would-you-like-to-know-more-about"></a>O que gostaria de saber mais?
* Quero saber mais sobre os [ficheiros ServiceDefinition.csdef](#csdef) e [ServiceConfig.cscfg.](#cscfg)
* Já sei disso, dá-me [alguns exemplos](#next-steps) sobre o que posso configurar.
* Quero criar o [ServicePackage.cspkg.](#cspkg)
* Estou a usar o Estúdio Visual e quero...
  * [Criar um serviço de nuvem][vs_create]
  * [Reconfigurar um serviço de nuvem existente][vs_reconfigure]
  * [Implementar um projeto de Serviço de Nuvem][vs_deploy]
  * [Ambiente de trabalho remoto em uma instância de serviço na nuvem][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
O ficheiro **ServiceDefinition.csdef** especifica as definições utilizadas pelo Azure para configurar um serviço na nuvem. O [Schema de Definição de Serviço Azure (.csdef File)](/previous-versions/azure/reference/ee758711(v=azure.100)) fornece o formato admissível para um ficheiro de definição de serviço. O exemplo seguinte mostra as definições que podem ser definidas para as funções Web e Trabalhador:

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

Pode consultar o [Schema](/previous-versions/azure/reference/ee758711(v=azure.100)) de Definição de Serviço para uma melhor compreensão do esquema XML utilizado aqui, no entanto, aqui está uma explicação rápida de alguns dos elementos:

**Sites**  
Contém as definições para websites ou aplicações web que estão hospedadas no IIS7.

**Pontos de entrada**  
Contém as definições para pontos finais que são usados para contactar o serviço de nuvem.

**Pontos De Final Internos**  
Contém as definições para pontos finais que são usados por exemplos de papéis para comunicar uns com os outros.

**ConfiguraçõesDefinições**  
Contém as definições de definição para características de uma função específica.

**Certificados**  
Contém as definições para certificados que são necessários para um papel. O exemplo de código anterior mostra um certificado que é usado para a configuração do Azure Connect.

**Recursos Locais**  
Contém as definições para os recursos de armazenamento locais. Um recurso de armazenamento local é um diretório reservado no sistema de ficheiros da máquina virtual em que está a decorrer um exemplo de função.

**Importações**  
Contém as definições para os módulos importados. O exemplo de código anterior mostra os módulos de Ligação remota de ambiente de trabalho e ligação azure.

**Arranque**  
Contém tarefas que são executadas quando o papel começa. As tarefas são definidas num ficheiro .cmd ou executável.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
A configuração das definições para o seu serviço na nuvem é determinada pelos valores do ficheiro **ServiceConfiguration.cscfg.** Especifica o número de casos que pretende implementar para cada função neste ficheiro. Os valores para as definições de configuração que definiu no ficheiro de definição de serviço são adicionados ao ficheiro de configuração do serviço. As impressões digitais de quaisquer certificados de gestão que estejam associados ao serviço de nuvem também são adicionadas ao ficheiro. O Esquema de Configuração de [Serviço Azure (.cscfg File)](/previous-versions/azure/reference/ee758710(v=azure.100)) fornece o formato admissível para um ficheiro de configuração de serviço.

O ficheiro de configuração do serviço não está embalado com a aplicação, mas é enviado para o Azure como um ficheiro separado e é utilizado para configurar o serviço na nuvem. Pode fazer o upload de um novo ficheiro de configuração de serviço sem recolocar o seu serviço na nuvem. Os valores de configuração para o serviço de nuvem podem ser alterados enquanto o serviço de nuvem está em execução. O exemplo seguinte mostra as definições de configuração que podem ser definidas para as funções Web e Trabalhador:

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

Pode consultar o [Schema](/previous-versions/azure/reference/ee758710(v=azure.100)) de Configuração de Serviço para melhor compreender o esquema XML utilizado aqui, no entanto, aqui está uma explicação rápida dos elementos:

**Instâncias**  
Configura o número de instâncias de execução para o papel. Para evitar que o seu serviço na nuvem se torne potencialmente indisponível durante as atualizações, recomenda-se que implemente mais de uma instância das suas funções viradas para a Web. Ao implementar mais de um caso, está a aderindo às diretrizes do Acordo de Nível de Serviço de [Computação Azure (SLA),](https://azure.microsoft.com/support/legal/sla/)que garante 99,95% de conectividade externa para funções viradas para a Internet quando duas ou mais instâncias de função são implementadas para um serviço.

**ConfiguraçõesDefinições**  
Refigura as definições para as instâncias de execução para um papel. O nome dos elementos `<Setting>` deve coincidir com as definições de definição no ficheiro de definição de serviço.

**Certificados**  
Refigura os certificados utilizados pelo serviço. O exemplo de código anterior mostra como definir o certificado para o módulo RemoteAccess. O valor do atributo de *impressão digital* deve ser definido para a impressão digital do certificado a utilizar.

<p/>

> [!NOTE]
> A impressão digital do certificado pode ser adicionada ao ficheiro de configuração utilizando um editor de texto. Ou, o valor pode ser adicionado no separador **Certificados** da página **Propriedades** do papel no Estúdio Visual.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definição de portas para exemplos de papéis
O Azure permite apenas um ponto de entrada para um papel web. O que significa que todo o tráfego ocorre através de um endereço IP. Pode configurar os seus websites para partilhar uma porta configurando o cabeçalho do anfitrião para direcionar o pedido para a localização correta. Também pode configurar as suas aplicações para ouvir portas bem conhecidas no endereço IP.

A amostra seguinte mostra a configuração para um papel web com um website e aplicação web. O site está configurado como o local de entrada predefinido na porta 80, e as aplicações web estão configuradas para receber pedidos de um cabeçalho de anfitrião alternativo que é chamado de "mail.mysite.cloudapp.net".

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


## <a name="changing-the-configuration-of-a-role"></a>Alterar a configuração de um papel
Pode atualizar a configuração do seu serviço na nuvem enquanto está em funcionamento em Azure, sem desligar o serviço. Para alterar as informações de configuração, pode carregar um novo ficheiro de configuração ou editar o ficheiro de configuração no lugar e aplicá-lo ao seu serviço de execução. As seguintes alterações podem ser feitas na configuração de um serviço:

* **Alterar os valores das definições de configuração**  
  Quando uma definição de configuração muda, uma instância de função pode optar por aplicar a alteração enquanto a instância está on-line, ou reciclar a instância graciosamente e aplicar a alteração enquanto a instância estiver offline.
* **Alterar a topologia de serviço de exemplos**  
  As alterações de topologia não afetam as situações de execução, exceto quando uma instância está a ser removida. Todos os casos restantes geralmente não precisam de ser reciclados; no entanto, pode optar por reciclar exemplos em resposta a uma mudança de topologia.
* **Alterar a impressão digital do certificado**  
  Só é possível atualizar um certificado quando uma instância de funções está offline. Se um certificado for adicionado, apagado ou alterado enquanto uma instância de função está online, o Azure toma graciosamente a instância offline para atualizar o certificado e trazê-lo de volta on-line após a alteração estar completa.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Manipulação alterações de configuração com eventos de tempo de execução de serviço
A [Biblioteca De Runtime Azure](/previous-versions/azure/reference/mt419365(v=azure.100)) inclui o espaço de nome [Microsoft.WindowsAzure.ServiceRuntime,](/previous-versions/azure/reference/ee741722(v=azure.100)) que fornece aulas para interagir com o ambiente Azure a partir de uma função. A classe [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) define os seguintes eventos que são levantados antes e depois de uma mudança de configuração:

* **[Mudança de](/previous-versions/azure/reference/ee758134(v=azure.100)) evento**  
  Isto ocorre antes da alteração de configuração ser aplicada a um determinado exemplo de uma função que lhe dá a oportunidade de retirar as instâncias de função, se necessário.
* **[Evento alterado](/previous-versions/azure/reference/ee758129(v=azure.100))**  
  Ocorre após a alteração de configuração ser aplicada a um caso especificado de uma função.

> [!NOTE]
> Como as alterações de certificados tomam sempre as instâncias de um papel offline, não aumentam o RoleEnvironment.Changing ou RoleEnvironment.Alterou os eventos.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
> [!NOTE]
> O tamanho máximo do pacote que pode ser implantado é de 600MB

Para implementar uma aplicação como serviço na nuvem em Azure, deve primeiro embalar a aplicação no formato apropriado. Pode utilizar a ferramenta de linha de comando **CSPack** (instalada com o [Azure SDK)](https://azure.microsoft.com/downloads/)para criar o ficheiro de pacote como alternativa ao Estúdio Visual.

**O CSPack** utiliza o conteúdo do ficheiro de definição de serviço e do ficheiro de configuração do serviço para definir o conteúdo da embalagem. **O CSPack** gera um ficheiro de pacote de aplicação (.cspkg) que pode enviar para o Azure utilizando o [portal Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Por predefinição, o pacote chama-se `[ServiceDefinitionFileName].cspkg`, mas pode especificar um nome diferente utilizando a opção `/out` do **CSPack**.

**CSPack** está localizado em  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (no windows) está disponível executando o atalho de comando Microsoft **Azure** que está instalado com o SDK.  
> 
> Execute o programa CSPack.exe por si só para ver documentação sobre todos os possíveis interruptores e comandos.
> 
> 

<p />

> [!TIP]
> Execute o seu serviço de cloud localmente no **Emulator microsoft Azure Compute,** use a opção **/copyonly.** Esta opção copia os ficheiros binários para a aplicação para um layout de diretório a partir do qual podem ser executados no emulador de cálculo.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Comando de exemplo para embalar um serviço na nuvem
O exemplo seguinte cria um pacote de aplicação que contém a informação para um papel web. O comando especifica o ficheiro de definição de serviço a utilizar, o diretório onde podem ser encontrados ficheiros binários e o nome do ficheiro do pacote.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Se a aplicação contiver uma função web e um papel de trabalhador, é utilizado o seguinte comando:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Quando as variáveis forem definidas da seguinte forma:

| Variável | Valor |
| --- | --- |
| \[de\] Nome de Diretório |O subdiretório no âmbito do diretório do projeto raiz que contém o ficheiro .csdef do projeto Azure. |
| \[\] de Definição de Serviços |O nome do ficheiro de definição de serviço. Por predefinição, este ficheiro chama-se ServiceDefinition.csdef. |
| \[OutputFileName\] |O nome do ficheiro de pacote gerado. Normalmente, isto está definido para o nome da aplicação. Se não for especificado o nome de ficheiro, o pacote de aplicação é criado como \[Nome de aplicação\].cspkg. |
| \[RoleName\] |O nome do papel definido no ficheiro de definição de serviço. |
| \[RoleBinariesDirectory] |A localização dos ficheiros binários para o papel. |
| \[\] VirtualPath |Os diretórios físicos de cada caminho virtual definidos na secção Sites da definição de serviço. |
| \[\] PhysicalPath |Os diretórios físicos dos conteúdos para cada percurso virtual definidos no nó do site da definição de serviço. |
| \[RoleAssemblyName\] |O nome do ficheiro binário para o papel. |

## <a name="next-steps"></a>Passos seguintes
Estou a criar um pacote de serviço sinuoso e quero...

* [Configurar o ambiente de trabalho remoto para uma instância de serviço na nuvem][remotedesktop]
* [Implementar um projeto de Serviço de Nuvem][deploy]

Estou a usar o Estúdio Visual e quero...

* [Criar um novo serviço de nuvem][vs_create]
* [Reconfigurar um serviço de nuvem existente][vs_reconfigure]
* [Implementar um projeto de Serviço de Nuvem][vs_deploy]
* [Configurar o ambiente de trabalho remoto para uma instância de serviço na nuvem][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md



