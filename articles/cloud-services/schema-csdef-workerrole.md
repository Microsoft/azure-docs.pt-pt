---
title: Def de serviços Cloud do Azure Esquema de WorkerRole | Documentos da Microsoft
services: cloud-services
ms.custom: ''
ms.date: 04/14/2015
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 41cd46bc-c479-43fa-96e5-d6c83e4e6d89
caps.latest.revision: 55
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 90a11c5bb81a0d29f5f8a1c1696732453aa4b1ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62095409"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Esquema de WorkerRole de definição dos serviços Cloud do Azure
A função de trabalho do Azure é uma função que é útil para desenvolvimento generalizado e pode efetuar o processamento em segundo plano para uma função da web.

A extensão de padrão para o ficheiro de definição de serviço é. csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Esquema de definição de serviço básico para uma função de trabalho.
Segue-se o formato básico do ficheiro de definição de serviço que contém uma função de trabalho.

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
    <LocalResources>
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    </LocalResources>
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    <Runtime executionContext="[limited|elevated]">
      <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
      </Environment>
      <EntryPoint>
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]" "/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>”>
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
        <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
        </Environment>
      </Task>
    </Startup>
    <Contents>
      <Content destination="<destination-folder-name>" >
        <SourceDirectory path="<local-source-directory>" />
      </Content>
    </Contents>
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementos de esquema
O ficheiro de definição de serviço inclui esses elementos, descritos em detalhe nas secções seguintes deste tópico:

[WorkerRole](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[Definição](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Pontos finais](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certificados](#Certificates)

[Certificado](#Certificate)

[Importações](#Imports)

[Importar](#Import)

[Tempo de execução](#Runtime)

[Ambiente](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Variable](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Arranque](#Startup)

[Tarefa](#Task)

[Conteúdo](#Contents)

[Conteúdo](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WorkerRole"></a> WorkerRole
O `WorkerRole` elemento descreve uma função que é útil para desenvolvimento generalizado e pode efetuar o processamento em segundo plano para uma função da web. Um serviço pode conter zero ou mais funções de trabalho.

A tabela seguinte descreve os atributos do `WorkerRole` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Necessário. O nome para a função de trabalho. Nome da função tem de ser exclusivo.|
|enableNativeCodeExecution|boolean|Opcional. O valor predefinido é `true`; nativo execução de código e de confiança total estão ativadas por predefinição. Defina este atributo como `false` desabilite a execução de código nativo para a função de trabalho e utilizar em vez disso, a confiança parcial do Azure.|
|vmsize|string|Opcional. Defina este valor para alterar o tamanho da máquina virtual que foi atribuída a esta função. O valor predefinido é `Small`. Para obter uma lista de tamanhos de máquinas de virtuais possíveis e seus atributos, consulte [tamanhos de Máquina Virtual para serviços em nuvem](cloud-services-sizes-specs.md).|

##  <a name="ConfigurationSettings"></a> ConfigurationSettings
O `ConfigurationSettings` elemento descreve a coleção de definições de configuração para uma função de trabalho. Este elemento é o principal do `Setting` elemento.

##  <a name="Setting"></a> Definição
O `Setting` elemento descreve um par de nome e valor que especifica uma definição de configuração para uma instância de uma função.

A tabela seguinte descreve os atributos do `Setting` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Necessário. Um nome exclusivo para a definição de configuração.|

As definições de configuração para uma função são pares nome / valor que é declarados no ficheiro de definição do serviço e defina no ficheiro de configuração do serviço.

##  <a name="LocalResources"></a> LocalResources
O `LocalResources` elemento descreve a coleção de recursos de armazenamento local para uma função de trabalho. Este elemento é o principal do `LocalStorage` elemento.

##  <a name="LocalStorage"></a> LocalStorage
O `LocalStorage` elemento identifica um recurso de armazenamento local, que fornece o espaço do sistema de ficheiros para o serviço em tempo de execução. Uma função pode definir a zero ou mais recursos de armazenamento local.

> [!NOTE]
>  O `LocalStorage` elemento pode aparecer como um filho do `WorkerRole` elemento para oferecer suporte a compatibilidade com versões anteriores do SDK do Azure.

A tabela seguinte descreve os atributos do `LocalStorage` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Necessário. Um nome exclusivo para o armazenamento local.|
|cleanOnRoleRecycle|boolean|Opcional. Indica se o arquivo local deve ser limpos quando a função é reiniciada. Valor predefinido é `true`.|
|sizeInMb|int|Opcional. A quantidade desejada de espaço de armazenamento para alocar para o armazenamento local, em MB. Se não for especificado, o espaço de armazenamento padrão atribuído é 100 MB. A quantidade mínima de espaço de armazenamento que pode ser atribuído é 1 MB.<br /><br /> O tamanho máximo dos recursos locais é depende do tamanho de máquina virtual. Para obter mais informações, consulte [tamanhos de Máquina Virtual para serviços em nuvem](cloud-services-sizes-specs.md).|

O nome do diretório alocado para o recurso de armazenamento local corresponde como o valor fornecido para o atributo de nome.

##  <a name="Endpoints"></a> Pontos finais
O `Endpoints` elemento descreve a coleção de entrada (externo), interna e pontos finais para uma função de entrada de instância. Este elemento é o principal do `InputEndpoint`, `InternalEndpoint`, e `InstanceInputEndpoint` elementos.

Entrada e de pontos finais internos são alocados em separado. Um serviço pode ter um total de entrada de 25, interno, e pontos de extremidade que podem ser alocados entre as funções de 25 permitidas num serviço de entrada de instância. Por exemplo, se o ter 5 funções pode alocar 5 pontos de extremidade de entrada por função ou pode alocar 25 pontos de extremidade de entrada para uma única função ou pode alocá 1 ponto final de entrada cada 25 funções.

> [!NOTE]
>  Cada função implementada requer uma instância por função. O padrão de aprovisionamento de uma subscrição está limitado a 20 núcleos e, portanto, está limitado a 20 instâncias de uma função. Se seu aplicativo requer mais instâncias, que é fornecido por predefinição Consulte de aprovisionamento [faturação, gestão de subscrições e suporte de Quota](https://azure.microsoft.com/support/options/) para obter mais informações no sentido de aumentar a quota.

##  <a name="InputEndpoint"></a> Ao InputEndpoint
O `InputEndpoint` elemento descreve um ponto final externo para uma função de trabalho.

Pode definir vários pontos de extremidade que são uma combinação de HTTP, HTTPS, UDP e os pontos finais TCP. Pode especificar qualquer número de porta que escolher para um ponto de final de entrada, mas os números de porta especificados para cada função no serviço tem de ser exclusivos. Por exemplo, se especificar que uma função utiliza a porta 80 para HTTP e a porta 443 para HTTPS, poderá especificar que uma segunda função utiliza a porta 8080 para HTTP e a porta 8043 para HTTPS.

A tabela seguinte descreve os atributos do `InputEndpoint` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Necessário. Um nome exclusivo para o ponto final externo.|
|protocol|string|Necessário. O protocolo de transporte para o ponto final externo. Para uma função de trabalho, os valores possíveis são `HTTP`, `HTTPS`, `UDP`, ou `TCP`.|
|port|int|Necessário. A porta para o ponto final externo. Pode especificar qualquer número de porta que escolher, mas os números de porta especificados para cada função no serviço tem de ser exclusivos.<br /><br /> Intervalo de valores possíveis entre 1 e 65535, inclusive (do Azure SDK versão 1.7 ou superior).|
|certificado|string|É necessário para um ponto final HTTPS. O nome de um certificado definido por um `Certificate` elemento.|
|localPort|int|Opcional. Especifica uma porta utilizada para ligações internas no ponto final. O `localPort` atributo mapeia a porta externa no ponto final a uma porta interna numa função. Isto é útil em cenários em que uma função têm de comunicar para um componente interno numa porta que diferente da que é exposto externamente.<br /><br /> Se não for especificado, o valor de `localPort` é o mesmo que o `port` atributo. Defina o valor da `localPort` para "*" para atribuir automaticamente uma porta não alocada que é detectável usando a API de tempo de execução.<br /><br /> Intervalo de valores possíveis entre 1 e 65535, inclusive (do Azure SDK versão 1.7 ou superior).<br /><br /> O `localPort` atributo só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.|
|ignoreRoleInstanceStatus|boolean|Opcional. Quando o valor deste atributo é definido como `true`, o estado de um serviço é ignorado e o ponto final não será removido pelo balanceador de carga. Definir este valor para `true` útil na depuração de ocupado instâncias de um serviço. O valor predefinido é `false`. **Nota:** Um ponto de extremidade pode receber tráfego, mesmo quando a função não está no estado pronto.|
|loadBalancerProbe|string|Opcional. O nome da sonda de Balanceador de carga associado com o ponto final de entrada. Para obter mais informações, consulte [esquema de LoadBalancerProbe](schema-csdef-loadbalancerprobe.md).|

##  <a name="InternalEndpoint"></a> InternalEndpoint
O `InternalEndpoint` elemento descreve um ponto final interno para uma função de trabalho. Um ponto final interno está disponível apenas para outras instâncias de função em execução no serviço; não está disponível para os clientes fora o serviço. Uma função de trabalho pode ter até cinco pontos HTTP, UDP ou TCP finais internos.

A tabela seguinte descreve os atributos do `InternalEndpoint` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Necessário. Um nome exclusivo para o ponto final interno.|
|protocol|string|Necessário. O protocolo de transporte para o ponto final interno. Os valores possíveis são `HTTP`, `TCP`, `UDP`, ou `ANY`.<br /><br /> Um valor de `ANY` Especifica que qualquer protocolo, qualquer porta é permitida.|
|port|int|Opcional. A porta utilizada para ligações com balanceamento de carga interno no ponto final. Ponto final utiliza duas portas de uma carga balanceada. A porta utilizada para o endereço IP público e a porta utilizada no endereço IP privado. Normalmente, estes são elas são definidas com o mesmo, mas pode optar por utilizar portas diferentes.<br /><br /> Intervalo de valores possíveis entre 1 e 65535, inclusive (do Azure SDK versão 1.7 ou superior).<br /><br /> O `Port` atributo só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.|

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint
O `InstanceInputEndpoint` elemento descreve um instância ponto final de entrada para uma função de trabalho. Um ponto de final de entrada de instância é associado a uma instância de função específica usando o encaminhamento de porta no balanceador de carga. Cada ponto de final de entrada de instância é mapeado para uma porta específica de um intervalo de portas possíveis. Este elemento é o principal do `AllocatePublicPortFrom` elemento.

O `InstanceInputEndpoint` elemento só está disponível a utilizar a versão do Azure SDK 1.7 ou superior.

A tabela seguinte descreve os atributos do `InstanceInputEndpoint` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Necessário. Um nome exclusivo para o ponto final.|
|localPort|int|Necessário. Especifica a porta interna que todas as instâncias de função irão escutar para receber o tráfego de entrada reencaminhado do Balanceador de carga. Intervalo de valores possíveis entre 1 e 65535, inclusive.|
|protocol|string|Necessário. O protocolo de transporte para o ponto final interno. Os valores possíveis são `udp` ou `tcp`. Utilize `tcp` para tráfego baseado em http/https.|

##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom
O `AllocatePublicPortFrom` elemento descreve o intervalo de portas públicas que pode ser utilizado por clientes externos para aceder a cada ponto de final de entrada de instância. O número da porta (VIP) público é alocado a partir deste intervalo e atribuído a cada ponto de extremidade de instância de função individuais durante a implementação de inquilino e a atualização. Este elemento é o principal do `FixedPortRange` elemento.

O `AllocatePublicPortFrom` elemento só está disponível a utilizar a versão do Azure SDK 1.7 ou superior.

##  <a name="FixedPort"></a> FixedPort
O `FixedPort` elemento Especifica a porta para o ponto final interno, que permite ligações com balanceamento no ponto final de carga.

O `FixedPort` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `FixedPort` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|port|int|Necessário. A porta para o ponto final interno. Isso tem o mesmo efeito que a definição do `FixedPortRange` min e max à mesma porta.<br /><br /> Intervalo de valores possíveis entre 1 e 65535, inclusive (do Azure SDK versão 1.7 ou superior).|

##  <a name="FixedPortRange"></a> FixedPortRange
O `FixedPortRange` elemento Especifica o intervalo de portas que são atribuídas ao ponto final interno ou o ponto final de entrada de instância e a porta utilizada para a carga de conjuntos com balanceamento de ligações no ponto final.

> [!NOTE]
>  O `FixedPortRange` elemento funciona de forma diferente consoante o elemento no qual reside. Quando o `FixedPortRange` elemento está no `InternalEndpoint` elemento, este abre-se todas as portas no balanceador de carga dentro do intervalo dos atributos min e max para todas as máquinas virtuais em que a função é executada. Quando o `FixedPortRange` elemento está no `InstanceInputEndpoint` elemento, este abre-se apenas uma porta no intervalo dos atributos min e max em cada máquina virtual a executar a função.

O `FixedPortRange` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `FixedPortRange` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|min.|int|Necessário. A porta mínima no intervalo. Intervalo de valores possíveis entre 1 e 65535, inclusive (do Azure SDK versão 1.7 ou superior).|
|max|string|Necessário. A porta máxima no intervalo. Intervalo de valores possíveis entre 1 e 65535, inclusive (do Azure SDK versão 1.7 ou superior).|

##  <a name="Certificates"></a> Certificados
O `Certificates` elemento descreve a coleção de certificados para uma função de trabalho. Este elemento é o principal do `Certificate` elemento. Uma função pode ter qualquer número de certificados associados. Para obter mais informações sobre como utilizar o elemento de certificados, consulte [modificar o ficheiro de definição de serviço com um certificado](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a> Certificado
O `Certificate` elemento descreve um certificado que está associado uma função de trabalho.

A tabela seguinte descreve os atributos do `Certificate` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Necessário. Um nome para este certificado, o que é utilizado para fazer referência a ele quando é associado a um HTTPS `InputEndpoint` elemento.|
|storeLocation|string|Necessário. A localização do arquivo de certificados em que este certificado pode ser encontrado no computador local. Os valores possíveis são `CurrentUser` e `LocalMachine`.|
|storeName|string|Necessário. O nome do arquivo de certificados em que este certificado reside no computador local. Os valores possíveis incluem os nomes de arquivo internos `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`, ou por qualquer nome de arquivo personalizado. Se não for especificado um nome de arquivo personalizado, o arquivo é criado automaticamente.|
|permissionLevel|string|Opcional. Especifica as permissões de acesso para os processos de função. Se pretender que os processos elevados só para poder aceder à chave privada, em seguida, especifique `elevated` permissão. `limitedOrElevated` permissão permite que todos os processos de função aceder à chave privada. Os valores possíveis são `limitedOrElevated` ou `elevated`. O valor predefinido é `limitedOrElevated`.|

##  <a name="Imports"></a> Importações
O `Imports` elemento descreve uma coleção de módulos de importação para uma função de trabalho, que adicionam componentes para o sistema operativo convidado. Este elemento é o principal do `Import` elemento. Este elemento é opcional e uma função pode ter apenas um bloco de tempo de execução.

O `Imports` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

##  <a name="Import"></a> Importar
O `Import` elemento Especifica um módulo para adicionar ao sistema operativo convidado.

O `Import` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `Import` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|moduleName|string|Necessário. O nome do módulo para importar. Módulos de importação válidos são:<br /><br /> -   RemoteAccess<br />-   RemoteForwarder<br />-Diagnostics<br /><br /> Os módulos de RemoteAccess e RemoteForwarder permitem-lhe configurar a sua instância de função para ligações de ambiente de trabalho remotas. Para obter mais informações, consulte [ativar a ligação de ambiente de trabalho remota](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> O módulo de diagnóstico permite-lhe recolher dados de diagnóstico para uma instância de função|

##  <a name="Runtime"></a> Runtime
O `Runtime` elemento descreve uma coleção de variáveis definições de ambiente para uma função de trabalho que controlam o ambiente de tempo de execução do processo de anfitrião do Azure. Este elemento é o principal do `Environment` elemento. Este elemento é opcional e uma função pode ter apenas um bloco de tempo de execução.

O `Runtime` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `Runtime` elemento:

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|executionContext|string|Opcional. Especifica o contexto em que o processo de função é iniciado. O contexto predefinido é `limited`.<br /><br /> -   `limited` – O processo é iniciado sem privilégios de administrador.<br />-   `elevated` – O processo é iniciado com privilégios de administrador.|

##  <a name="Environment"></a> Environment
O `Environment` elemento descreve uma coleção de definições de variáveis de ambiente para uma função de trabalho. Este elemento é o principal do `Variable` elemento. Uma função pode ter qualquer número de variáveis de ambiente definidas.

##  <a name="Variable"></a> Variável
O `Variable` elemento Especifica uma variável de ambiente para definir em operativo convidado.

O `Variable` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `Variable` elemento:

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Necessário. O nome da variável de ambiente para definir.|
|value|string|Opcional. O valor a definir para a variável de ambiente. Tem de incluir um atributo de valor ou uma `RoleInstanceValue` elemento.|

##  <a name="RoleInstanceValue"></a> RoleInstanceValue
O `RoleInstanceValue` elemento Especifica o xPath para obter o valor da variável.

A tabela seguinte descreve os atributos do `RoleInstanceValue` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|xpath|string|Opcional. Caminho de localização das definições de implementação para a instância. Para obter mais informações, consulte [variáveis de configuração com XPath](cloud-services-role-config-xpath.md).<br /><br /> Tem de incluir um atributo de valor ou uma `RoleInstanceValue` elemento.|

##  <a name="EntryPoint"></a> EntryPoint
O `EntryPoint` elemento Especifica o ponto de entrada para uma função. Este elemento é o principal do `NetFxEntryPoint` elementos. Estes elementos permitem-lhe especificar uma aplicação diferente da predefinição WaWorkerHost.exe para atuar como o ponto de entrada da função.

O `EntryPoint` elemento só está disponível a utilizar a versão do SDK do Azure 1.5 ou superior.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint
O `NetFxEntryPoint` elemento Especifica o programa seja executado para uma função.

> [!NOTE]
>  O `NetFxEntryPoint` elemento só está disponível a utilizar a versão do SDK do Azure 1.5 ou superior.

A tabela seguinte descreve os atributos do `NetFxEntryPoint` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|assemblyName|string|Necessário. O nome de ficheiro e caminho do assembly que contém o ponto de entrada. O caminho é relativo para a pasta  **\\%ROLEROOT%\Approot** (não especificar  **\\%ROLEROOT%\Approot** no `commandLine`, presume-se). **% ROLEROOT %** é uma variável de ambiente é mantida pelo Azure e representa a localização da pasta raiz para a sua função. O  **\\%ROLEROOT%\Approot** pasta representa a pasta de aplicativos para a sua função.|
|targetFrameworkVersion|string|Necessário. A versão do .NET framework em que o assembly foi criado. Por exemplo, `targetFrameworkVersion="v4.0"`.|

##  <a name="ProgramEntryPoint"></a> ProgramEntryPoint
O `ProgramEntryPoint` elemento Especifica o programa seja executado para uma função. O `ProgramEntryPoint` elemento permite-lhe especificar um ponto de entrada de programa que não se baseia num assembly do .NET.

> [!NOTE]
>  O `ProgramEntryPoint` elemento só está disponível a utilizar a versão do SDK do Azure 1.5 ou superior.

A tabela seguinte descreve os atributos do `ProgramEntryPoint` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|commandLine|string|Necessário. O caminho, nome de ficheiro e os argumentos da linha de comando do programa a executar. O caminho é relativo para a pasta **%ROLEROOT%\Approot** (não especificar **%ROLEROOT%\Approot** na linha de comandos, presume-se). **% ROLEROOT %** é uma variável de ambiente é mantida pelo Azure e representa a localização da pasta raiz para a sua função. O **%ROLEROOT%\Approot** pasta representa a pasta de aplicativos para a sua função.<br /><br /> Se o programa é concluído, a função é reciclada, geralmente definir o programa continue a executar, em vez de ser um programa que apenas é iniciado e executa uma tarefa finita.|
|setReadyOnProcessStart|boolean|Necessário. Especifica se a instância de função aguarda que o programa de linha de comandos sinalizar que é iniciado. Este valor tem de ser definido como `true` neste momento. Definindo o valor como `false` está reservado para utilização futura.|

##  <a name="Startup"></a> Startup
O `Startup` elemento descreve uma coleção de tarefas que são executadas quando a função é iniciada. Este elemento pode ser o pai do `Variable` elemento. Para obter mais informações sobre como utilizar as tarefas de arranque de função, veja [como configurar tarefas de arranque](cloud-services-startup-tasks.md). Este elemento é opcional e uma função pode ter apenas um bloco de arranque.

A tabela seguinte descreve o atributo do `Startup` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|prioridade|int|Apenas para utilização interna.|

##  <a name="Task"></a> Tarefa
O `Task` elemento Especifica a tarefa de arranque que acontece quando a função for iniciada. Tarefas de arranque podem ser utilizadas para executar tarefas que preparam a função para executar essa instalação de componentes de software ou executar outras aplicações. Tarefas são executadas na ordem em que aparecem dentro de `Startup` bloco do elemento.

O `Task` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `Task` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|commandLine|string|Necessário. Um script, como um arquivo CMD, que contém os comandos para executar. Ficheiros de comando e em lote de arranque tem de ser guardados no formato ANSI. Formatos de arquivo que definir um marcador de ordem de bytes no início do ficheiro não irão processar corretamente.|
|executionContext|string|Especifica o contexto em que o script é executado.<br /><br /> -   `limited` [Default] – executar com os mesmos privilégios que a função que aloja o processo.<br />-   `elevated` – Execute com privilégios de administrador.|
|taskType|string|Especifica o comportamento de execução do comando.<br /><br /> -   `simple` [Default] – o sistema aguarda a tarefa sair antes de quaisquer outras tarefas são iniciadas.<br />-   `background` – O sistema não espere que a tarefa sair.<br />-   `foreground` – Semelhante ao plano de fundo, exceto a função não for reiniciada, até que todas as tarefas de primeiro plano sair.|

##  <a name="Contents"></a> Conteúdo
O `Contents` elemento descreve a coleção de conteúdo para uma função de trabalho. Este elemento é o principal do `Content` elemento.

O `Contents` elemento só está disponível a utilizar a versão do SDK do Azure 1.5 ou superior.

##  <a name="Content"></a> Conteúdo
O `Content` elemento define a localização de origem do conteúdo seja copiado para a máquina virtual do Azure e o caminho de destino para o qual é copiado.

O `Content` elemento só está disponível a utilizar a versão do SDK do Azure 1.5 ou superior.

A tabela seguinte descreve os atributos do `Content` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|destination|string|Necessário. Localização da máquina virtual do Azure para o qual o conteúdo é colocado. Esta localização é relativo para a pasta **%ROLEROOT%\Approot**.|

Este elemento é o elemento principal do `SourceDirectory` elemento.

##  <a name="SourceDirectory"></a> SourceDirectory
O `SourceDirectory` elemento define o diretório local a partir do qual o conteúdo é copiado. Utilize este elemento para especificar o conteúdo local para copiar para a máquina virtual do Azure.

O `SourceDirectory` elemento só está disponível a utilizar a versão do SDK do Azure 1.5 ou superior.

A tabela seguinte descreve os atributos do `SourceDirectory` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|caminho|string|Necessário. Caminho relativo ou absoluto de um diretório local cujos conteúdos serão copiados para a máquina virtual do Azure. Expansão de variáveis de ambiente no caminho do diretório é suportada.|

## <a name="see-also"></a>Consultar Também
[Esquema de definição do cloud Service (clássico)](schema-csdef-file.md)