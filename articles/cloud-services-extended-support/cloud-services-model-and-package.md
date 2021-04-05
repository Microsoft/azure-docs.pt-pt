---
title: O que é o modelo e pacote do Azure Cloud Service (suporte alargado)
description: Descreve o modelo de serviço em nuvem (suporte alargado) (.csdef, .cscfg) e pacote (.cspkg) em Azure
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 3baea92d78cf15c35d2fb36692dab1edfd950699
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744765"
---
# <a name="what-is-the-azure-cloud-service-model-and-how-do-i-package-it"></a>O que é o modelo do Azure Cloud Service e como o embalo?

Um serviço de nuvem é criado a partir de três componentes, a definição de serviço *(.csdef)*, o serviço config *(.cscfg)* e um pacote de serviço *(.cspkg)*. Tanto os **ficheiros ServiceDefinition.csdef** como **ServiceConfig.cscfg** são baseados em XML e descrevem a estrutura do serviço de nuvem e como está configurado; coletivamente chamado de modelo. O **ServicePackage.cspkg** é um ficheiro zip que é gerado a partir do **ServiceDefinition.csdef** e, entre outras coisas, contém todas as dependências binárias necessárias. A Azure cria um serviço de nuvem tanto a partir do **ServicePackage.cspkg** como do **ServiceConfig.cscfg**.

Uma vez que o serviço de nuvem esteja em execução em Azure, pode reconfigurá-lo através do ficheiro **ServiceConfig.cscfg,** mas não é possível alterar a definição.

## <a name="what-would-you-like-to-know-more-about"></a>Sobre o que gostaria de saber mais?
* Quero saber mais sobre os [ficheiros ServiceDefinition.csdef](#csdef) e [ServiceConfig.cscfg.](#cscfg)
* Já sei disso, dê-me [alguns exemplos](#next-steps) sobre o que posso configurar.
* Quero criar o [ServicePackage.cspkg](#cspkg).
* Estou a usar o Estúdio Visual e quero...
  * [Criar um serviço de nuvem] [vs_create]
  * [Reconfigure um serviço de nuvem existente] [vs_reconfigure]
  * [Implementar um projeto de Serviço de Nuvem] [vs_deploy]
  * [Ambiente de trabalho remoto em uma instância de serviço em nuvem] [remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiçoDefinition.csdef
O ficheiro **ServiceDefinition.csdef** especifica as definições que são utilizadas pela Azure para configurar um serviço de cloud. O [Esquema de Definição de Serviço Azure (.csdef File)](schema-csdef-file.md) fornece o formato admissível para um ficheiro de definição de serviço. O exemplo a seguir mostra as definições que podem ser definidas para as funções Web e Worker:

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

Pode consultar o [esquema de definição](schema-csdef-file.md)de serviço) para uma melhor compreensão do esquema XML aqui utilizado, no entanto, aqui está uma explicação rápida de alguns dos elementos:

**Sites**  
Contém as definições para sites ou aplicações web que estão hospedadas no IIS7.

**InputEndpoints**  
Contém as definições para pontos finais que são utilizados para contactar o serviço de nuvem.

**Pontos de Final de Sação Internos**  
Contém as definições para pontos finais que são usados por instâncias de função para comunicar uns com os outros.

**ConfiguraçõesSets**  
Contém as definições de definição para características de um papel específico.

**Certificados**  
Contém as definições de certificados necessários para um papel. O exemplo de código anterior mostra um certificado que é utilizado para a configuração do Azure Connect.

**Recursos Locais**  
Contém as definições para os recursos de armazenamento locais. Um recurso de armazenamento local é um diretório reservado no sistema de ficheiros da máquina virtual em que um caso de papel está em execução.

**Importações**  
Contém as definições para módulos importados. O exemplo de código anterior mostra os módulos de Conexão remota de ambiente de trabalho e Azure Connect.

**Arranque**  
Contém tarefas que são executadas quando a função começa. As tarefas são definidas num ficheiro .cmd ou executável.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
A configuração das definições para o seu serviço na nuvem é determinada pelos valores no ficheiro **ServiceConfiguration.cscfg.** Especifica o número de casos que pretende implementar para cada função neste ficheiro. Os valores para as definições de configuração que definiu no ficheiro de definição de serviço são adicionados ao ficheiro de configuração de serviço. As impressões digitais de quaisquer certificados de gestão associados ao serviço de nuvem também são adicionadas ao ficheiro. O [Esquema de Configuração de Serviço Azure (.cscfg File)](schema-cscfg-file.md) fornece o formato admissível para um ficheiro de configuração de serviço.

O ficheiro de configuração de serviço não é embalado com a aplicação, mas é enviado para a Azure como um ficheiro separado e é utilizado para configurar o serviço de cloud. Pode carregar um novo ficheiro de configuração de serviço sem recolocar o seu serviço na nuvem. Os valores de configuração do serviço na nuvem podem ser alterados enquanto o serviço de nuvem estiver em execução. O exemplo a seguir mostra as definições de configuração que podem ser definidas para as funções Web e Worker:

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

Pode consultar melhor o esquema de [configuração](schema-cscfg-file.md) de serviço usado aqui, no entanto, aqui está uma explicação rápida dos elementos:

**de Instâncias**  
Configura o número de casos de execução para o papel. Para evitar que o seu serviço na nuvem se torne potencialmente indisponível durante as atualizações, recomenda-se que implemente mais de uma instância das suas funções viradas para a Web. Ao implementar mais de um exemplo, está a aderir às diretrizes do Acordo de [Nível de Serviço Azure Compute (SLA),](https://azure.microsoft.com/support/legal/sla/)que garante 99,95% de conectividade externa para funções viradas para a Internet quando duas ou mais instâncias de função são implementadas para um serviço.

**ConfiguraçõesSets**  
Configura as definições para as instâncias de execução para um papel. O nome dos `<Setting>` elementos deve corresponder às definições de definição no ficheiro de definição de serviço.

**Certificados**  
Configura os certificados utilizados pelo serviço. O exemplo de código anterior mostra como definir o certificado para o módulo RemoteAccess. O valor do atributo *de impressão digital* deve ser definido na impressão digital do certificado a utilizar.

<p/>

> [!NOTE]
> A impressão digital do certificado pode ser adicionada ao ficheiro de configuração utilizando um editor de texto. Ou, o valor pode ser adicionado no **separador Certificados** da página **Propriedades** da função no Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definição de portas para instâncias de função
O Azure permite apenas um ponto de entrada para uma função web. O que significa que todo o tráfego ocorre através de um endereço IP. Pode configurar os seus websites para partilhar uma porta configurando o cabeçalho do anfitrião para direcionar o pedido para a localização correta. Também pode configurar as suas aplicações para ouvir portas bem conhecidas no endereço IP.

A amostra que se segue mostra a configuração para uma função web com um site e aplicação web. O site está configurado como o local de entrada padrão na porta 80, e as aplicações web estão configuradas para receber pedidos de um cabeçalho de anfitrião alternativo que é chamado de "mail.mysite.cloudapp.net".

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
Pode atualizar a configuração do seu serviço na nuvem enquanto está em funcionamento no Azure, sem tirar o serviço offline. Para alterar as informações de configuração, pode fazer o upload de um novo ficheiro de configuração ou editar o ficheiro de configuração no lugar e aplicá-lo ao seu serviço de execução. Podem ser feitas as seguintes alterações à configuração de um serviço:

* **Alterar os valores das definições de configuração**  
  Quando uma configuração muda, uma instância de função pode optar por aplicar a alteração enquanto a instância está online, ou reciclar o caso graciosamente e aplicar a alteração enquanto a instância está offline.
* **Alteração da topologia do serviço de instâncias de papel**  
  As alterações de topologia não afetam as instâncias de execução, exceto quando um caso está a ser removido. Todas as instâncias restantes geralmente não precisam de ser recicladas; no entanto, pode optar por reciclar instâncias de papel em resposta a uma mudança de topologia.
* **Alterar a impressão digital do certificado**  
  Só é possível atualizar um certificado quando uma instância de função estiver offline. Se um certificado for adicionado, eliminado ou alterado enquanto uma instância de função estiver on-line, a Azure toma graciosamente o caso offline para atualizar o certificado e trazê-lo de volta on-line após a alteração estar completa.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Mudanças de configuração de manuseamento com eventos de tempo de execução de serviço
A Azure Runtime Library inclui o espaço de nomes Microsoft.WindowsAzure.ServiceRuntime, que fornece aulas para interagir com o ambiente Azure a partir de um papel. A classe RoleEnvironment define os seguintes eventos que são levantados antes e depois de uma alteração de configuração:

* **Mudança de evento**  
  Isto ocorre antes da alteração de configuração ser aplicada a uma instância especificada de uma função que lhe dá a oportunidade de retirar as instâncias de função, se necessário.
* **Evento alterado**  
  Ocorre após a alteração da configuração ser aplicada a uma instância especificada de uma função.

> [!NOTE]
> Como as alterações de certificados tomam sempre os casos de um papel offline, não levantam o RoleEnvironment.Change ou RoleEnvironment.Alterar eventos.
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
> [!NOTE]
> O tamanho máximo do pacote que pode ser implantado é de 600MB

Para implementar uma aplicação como serviço em nuvem em Azure, tem primeiro de embalar a aplicação no formato apropriado. Pode utilizar a ferramenta de linha de comando **CSPack** (instalada com o [Azure SDK](https://azure.microsoft.com/downloads/)) para criar o ficheiro de pacote como alternativa ao Visual Studio.

**O CSPack** utiliza o conteúdo do ficheiro de definição de serviço e do ficheiro de configuração de serviço para definir o conteúdo do pacote. **O CSPack** gera um ficheiro de pacote de aplicações (.cspkg) que pode fazer o upload para a Azure utilizando o [portal Azure](../cloud-services/cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Por predefinição, o pacote é `[ServiceDefinitionFileName].cspkg` nomeado, mas pode especificar um nome diferente utilizando a `/out` opção de **CSPack**.

**CSPack** está localizado em  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (no windows) está disponível executando o **atalho de pedido de comando do Microsoft Azure** que está instalado com o SDK.  
> 
> Executar o programa CSPack.exe por si só para ver documentação sobre todos os possíveis interruptores e comandos.
> 
> 

<p />

> [!TIP]
> Executar o seu serviço de nuvem localmente no **Microsoft Azure Compute Emulator,** utilize a opção **/copyonly.** Esta opção copia os ficheiros binários para a aplicação a um layout de diretório a partir do qual podem ser executados no emulador computacional.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Comando de exemplo para embalar um serviço de nuvem
O exemplo a seguir cria um pacote de aplicações que contém as informações para uma função web. O comando especifica o ficheiro de definição de serviço a utilizar, o diretório onde podem ser encontrados ficheiros binários e o nome do ficheiro do pacote.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Se a aplicação contiver uma função web e uma função de trabalhador, é utilizado o seguinte comando:

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
| \[Nome do diretório\] |O subdiretório no diretório do projeto raiz que contém o ficheiro .csdef do projeto Azure. |
| \[Definição de Serviço\] |O nome do ficheiro de definição de serviço. Por predefinição, este ficheiro chama-se ServiceDefinition.csdef. |
| \[Nome de Filtro de Saída\] |O nome do ficheiro de pacote gerado. Normalmente, isto é definido para o nome da aplicação. Se não for especificado o nome do ficheiro, o pacote de aplicações é criado como \[ ApplicationName \] .cspkg. |
| \[RoleName\] |O nome da função tal como definido no ficheiro de definição de serviço. |
| \[RoleBinariesDirectory] |A localização dos ficheiros binários para o papel. |
| \[VirtualPath\] |Os diretórios físicos para cada percurso virtual definido na secção Sites da definição de serviço. |
| \[CaminhoFísico\] |Os diretórios físicos dos conteúdos para cada caminho virtual definido no nó do site da definição de serviço. |
| \[RoleAssemblyName\] |O nome do ficheiro binário para o papel. |

## <a name="next-steps"></a>Passos seguintes 
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).

