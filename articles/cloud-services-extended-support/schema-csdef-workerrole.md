---
title: Azure Cloud Services (suporte alargado) Def. WorkerRole Schema | Microsoft Docs
description: Informação relacionada com o esquema de função do trabalhador para os Serviços cloud (suporte alargado)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8d55f17ba0fe42dab5ac9c7d2e3c09400b3d7029
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744741"
---
# <a name="azure-cloud-services-extended-support-definition-workerrole-schema"></a>Azure Cloud Services (definição de suporte alargado) Esquema de apoio do TrabalhadorRole

O papel de trabalhador do Azure é um papel que é útil para o desenvolvimento generalizado, e pode executar o processamento de fundo para um papel web.

A extensão padrão para o ficheiro de definição de serviço é csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Esquema básico de definição de serviço para um papel de trabalhador.
O formato básico do ficheiro de definição de serviço que contém uma função de trabalhador é o seguinte.

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
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]"/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>">
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
O ficheiro de definição de serviço inclui estes elementos, descritos em detalhe nas secções subsequentes neste tópico:

[OperárioRole](#WorkerRole)

[ConfiguraçõesSets](#ConfigurationSettings)

[Definição](#Setting)

[Recursos Locais](#LocalResources)

[LocalStorage](#LocalStorage)

[Pontos Finais](#Endpoints)

[Ponto de entradaEndpoint](#InputEndpoint)

[Ponto Interno DeEndpoint](#InternalEndpoint)

[Ponto De OcorrênciaInputEndpoint](#InstanceInputEndpoint)

[AlocadoPublicPortFrom](#AllocatePublicPortFrom)

[Porto Fixo](#FixedPort)

[PortoRange Fixo](#FixedPortRange)

[Certificados](#Certificates)

[Certificado](#Certificate)

[Importações](#Imports)

[Importar](#Import)

[Runtime](#Runtime)

[Environment](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[Ponto ProgramEntry](#ProgramEntryPoint)

[Variável](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Arranque](#Startup)

[Tarefa](#Task)

[Conteúdos](#Contents)

[Conteúdo](#Content)

[Diretório de Origem](#SourceDirectory)

##  <a name="workerrole"></a><a name="WorkerRole"></a> OperárioRole
O `WorkerRole` elemento descreve um papel que é útil para o desenvolvimento generalizado, e pode executar o processamento de fundo para um papel web. Um serviço pode conter zero ou mais funções de trabalhador.

A tabela seguinte descreve os atributos do `WorkerRole` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|name|string|Obrigatório. O nome para o papel de trabalhador. O nome do papel deve ser único.|
|enableNativeCodeExecution|boolean|Opcional. O valor predefinido `true` é; execução de código nativo e confiança total são ativados por padrão. Desa esta atribuição `false` para desativar a execução de código nativo para o papel do trabalhador, e use a confiança parcial do Azure.|
|vmsize|string|Opcional. Desloque este valor para alterar o tamanho da máquina virtual que é atribuída a esta função. O valor predefinido é `Small`. Para obter uma lista de possíveis tamanhos de máquinas virtuais e seus [atributos, consulte os tamanhos da Máquina Virtual para Serviços cloud](available-sizes.md).|

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a> ConfiguraçõesSets
O `ConfigurationSettings` elemento descreve a coleção de configurações para um papel de trabalhador. Este elemento é o progenitor do `Setting` elemento.

##  <a name="setting"></a><a name="Setting"></a> Definição
O `Setting` elemento descreve um par de nomes e valores que especifica uma definição de configuração para uma instância de um papel.

A tabela seguinte descreve os atributos do `Setting` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|name|string|Obrigatório. Um nome único para a configuração.|

As definições de configuração para uma função são pares de nome e valor que são declarados no ficheiro de definição de serviço e definidos no ficheiro de configuração de serviço.

##  <a name="localresources"></a><a name="LocalResources"></a> Recursos Locais
O `LocalResources` elemento descreve a recolha de recursos de armazenamento locais para um papel de trabalhador. Este elemento é o progenitor do `LocalStorage` elemento.

##  <a name="localstorage"></a><a name="LocalStorage"></a> LocalStorage
O `LocalStorage` elemento identifica um recurso de armazenamento local que fornece espaço do sistema de ficheiros para o serviço em tempo de execução. Uma função pode definir zero ou mais recursos de armazenamento local.

> [!NOTE]
>  O `LocalStorage` elemento pode aparecer como uma criança do elemento para suportar a `WorkerRole` compatibilidade com versões anteriores do Azure SDK.

A tabela seguinte descreve os atributos do `LocalStorage` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|name|string|Obrigatório. Um nome único para a loja local.|
|cleanOnRoleRecycle|boolean|Opcional. Indica se a loja local deve ser limpa quando a função for reiniciada. O valor predefinido é `true`.|
|tamanhoInMb|int|Opcional. A quantidade desejada de espaço de armazenamento para alocar para a loja local, em MB. Se não for especificado, o espaço de armazenamento predefinido atribuído é de 100 MB. A quantidade mínima de espaço de armazenamento que pode ser atribuída é de 1 MB.<br /><br /> O tamanho máximo dos recursos locais depende do tamanho da máquina virtual. Para obter mais informações, consulte [os tamanhos da Máquina Virtual para Serviços cloud.](available-sizes.md)|

O nome do diretório atribuído ao recurso de armazenamento local corresponde ao valor previsto para o atributo de nome.

##  <a name="endpoints"></a><a name="Endpoints"></a> Pontos finais
O `Endpoints` elemento descreve a recolha de pontos finais de entrada (externos), internos e de exemplo para uma função. Este elemento é o progenitor `InputEndpoint` `InternalEndpoint` do, e `InstanceInputEndpoint` elementos.

A entrada e os pontos finais internos são atribuídos separadamente. Um serviço pode ter um total de 25 pontos finais de entrada, internos e de instância que podem ser atribuídos em todas as 25 funções permitidas num serviço. Por exemplo, se tiver 5 funções, pode atribuir 5 pontos finais de entrada por função ou pode atribuir 25 pontos finais de entrada a uma única função ou pode atribuir 1 ponto final de entrada cada a 25 funções.

> [!NOTE]
>  Cada papel destacado requer um exemplo por papel. O preencumeding por defeito para uma subscrição é limitado a 20 núcleos, pelo que está limitado a 20 instâncias de um papel. Se a sua aplicação requer mais instâncias do que as fornecidas pelo predefinido ver [Faturação, Gestão de Subscrição e Suporte à Quota](https://azure.microsoft.com/support/options/) para obter mais informações sobre o aumento da sua quota.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a> Ponto de entradaEndpoint
O `InputEndpoint` elemento descreve um ponto final externo a um papel de trabalhador.

Pode definir vários pontos finais que são uma combinação de pontos finais HTTP, HTTPS, UDP e TCP. Pode especificar qualquer número de porta que escolha para um ponto final de entrada, mas os números de porta especificados para cada função no serviço devem ser únicos. Por exemplo, se especificar que uma função utiliza a porta 80 para HTTP e a porta 443 para HTTPS, poderá então especificar que uma segunda função utiliza a porta 8080 para HTTP e a porta 8043 para HTTPS.

A tabela seguinte descreve os atributos do `InputEndpoint` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|name|string|Obrigatório. Um nome único para o ponto final externo.|
|protocolo|string|Obrigatório. O protocolo de transporte para o ponto final externo. Para um papel de trabalhador, os valores possíveis `HTTP` `HTTPS` `UDP` são, ou `TCP` .|
|porta|int|Obrigatório. A porta para o ponto final externo. Pode especificar qualquer número de porta que escolher, mas os números de porta especificados para cada função no serviço devem ser únicos.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (versão Azure SDK 1.7 ou superior).|
|certificado|string|Necessário para um ponto final HTTPS. O nome de um certificado definido por um `Certificate` elemento.|
|localPort|int|Opcional. Especifica uma porta utilizada para ligações internas no ponto final. O `localPort` atributo mapeia a porta externa no ponto final para uma porta interna numa função. Isto é útil em cenários em que uma função deve comunicar a um componente interno numa porta diferente daquela que é exposta externamente.<br /><br /> Se não for especificado, o valor `localPort` é o mesmo que o `port` atributo. Desabroque o valor de `localPort` "*" para atribuir automaticamente uma porta não atribuída que seja detetável utilizando a API de tempo de execução.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (versão Azure SDK 1.7 ou superior).<br /><br /> O `localPort` atributo só está disponível utilizando a versão Azure SDK 1.3 ou superior.|
|ignoreRoleInstanceStatus|boolean|Opcional. Quando o valor deste atributo é definido para `true` , o estado de um serviço é ignorado e o ponto final não será removido pelo equilibrador de carga. Definindo este valor útil `true` para depurar casos ocupados de um serviço. O valor predefinido é `false`. **Nota:** Um ponto final ainda pode receber tráfego mesmo quando o papel não está em um estado Ready.|
|loadBalancerProbe|string|Opcional. O nome da sonda de balançador de carga associada ao ponto final de entrada. Para obter mais informações, consulte [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md).|

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a> Ponto Interno DeEndpoint
O `InternalEndpoint` elemento descreve um ponto final interno para um papel de trabalhador. Um ponto final interno só está disponível para outras instâncias de função que estão dentro do serviço; não está disponível para clientes fora do serviço. Um papel de trabalhador pode ter até cinco pontos finais internos HTTP, UDP ou TCP.

A tabela seguinte descreve os atributos do `InternalEndpoint` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|name|string|Obrigatório. Um nome único para o ponto final interno.|
|protocolo|string|Obrigatório. O protocolo de transporte para o ponto final interno. Os valores possíveis `HTTP` `TCP` `UDP` são, , ou `ANY` .<br /><br /> Um valor de `ANY` especifica que qualquer protocolo, qualquer porta é permitido.|
|porta|int|Opcional. A porta utilizada para ligações internas equilibradas de carga no ponto final. Um ponto final equilibrado de carga utiliza duas portas. A porta utilizada para o endereço IP público e a porta utilizada no endereço IP privado. Normalmente estes são definidos para o mesmo, mas você pode optar por usar diferentes portas.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (versão Azure SDK 1.7 ou superior).<br /><br /> O `Port` atributo só está disponível utilizando a versão Azure SDK 1.3 ou superior.|

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a> Ponto De OcorrênciaInputEndpoint
O elemento descreve um ponto final de `InstanceInputEndpoint` entrada de exemplo para um papel de trabalhador. Um ponto final de entrada de exemplo está associado a uma instância de função específica, utilizando o reencaminhamento da porta no equilibrador de carga. Cada ponto final de entrada é mapeado para uma porta específica a partir de uma gama de portas possíveis. Este elemento é o progenitor do `AllocatePublicPortFrom` elemento.

O `InstanceInputEndpoint` elemento só está disponível utilizando a versão Azure SDK 1.7 ou superior.

A tabela seguinte descreve os atributos do `InstanceInputEndpoint` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|name|string|Obrigatório. Um nome único para o ponto final.|
|localPort|int|Obrigatório. Especifica a porta interna que todas as instâncias de função irão ouvir para receber o tráfego de entrada reencaminhado do equilibrador de carga. Os valores possíveis variam entre 1 e 65535, inclusive.|
|protocolo|string|Obrigatório. O protocolo de transporte para o ponto final interno. Os valores possíveis são `udp` ou `tcp`. Utilize `tcp` para tráfego baseado em http/https.|

##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a> AlocadoPublicPortFrom
O `AllocatePublicPortFrom` elemento descreve a gama de portas públicas que pode ser usada por clientes externos para aceder a cada ponto final de entrada de cada instância. O número de porta público (VIP) é atribuído a partir desta gama e atribuído a cada ponto final de instância individual durante a implantação e atualização do inquilino. Este elemento é o progenitor do `FixedPortRange` elemento.

O `AllocatePublicPortFrom` elemento só está disponível utilizando a versão Azure SDK 1.7 ou superior.

##  <a name="fixedport"></a><a name="FixedPort"></a> Porto Fixo
O `FixedPort` elemento especifica a porta para o ponto final interno, o que permite a carga de ligações equilibradas no ponto final.

O `FixedPort` elemento só está disponível utilizando a versão Azure SDK 1.3 ou superior.

A tabela seguinte descreve os atributos do `FixedPort` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|porta|int|Obrigatório. O porto para o ponto final interno. Isto tem o mesmo efeito que definir o `FixedPortRange` min e max para a mesma porta.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (versão Azure SDK 1.7 ou superior).|

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a> PortoRange Fixo
O `FixedPortRange` elemento especifica a gama de portas que são atribuídas ao ponto final interno ou ponto final de entrada de exemplo, e define a porta utilizada para ligações equilibradas de carga no ponto final.

> [!NOTE]
>  O `FixedPortRange` elemento funciona de forma diferente dependendo do elemento em que reside. Quando o `FixedPortRange` elemento está no `InternalEndpoint` elemento, abre todas as portas do equilibrador de carga dentro do alcance dos atributos min e max para todas as máquinas virtuais em que o papel funciona. Quando o `FixedPortRange` elemento está no `InstanceInputEndpoint` elemento, abre apenas uma porta dentro do alcance dos atributos min e max em cada máquina virtual que executa o papel.

O `FixedPortRange` elemento só está disponível utilizando a versão Azure SDK 1.3 ou superior.

A tabela seguinte descreve os atributos do `FixedPortRange` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|min|int|Obrigatório. A porta mínima da gama. Os valores possíveis variam entre 1 e 65535, inclusive (versão Azure SDK 1.7 ou superior).|
|max|string|Obrigatório. A porta máxima da gama. Os valores possíveis variam entre 1 e 65535, inclusive (versão Azure SDK 1.7 ou superior).|

##  <a name="certificates"></a><a name="Certificates"></a> Certificados
O `Certificates` elemento descreve a recolha de certificados para um papel de trabalhador. Este elemento é o progenitor do `Certificate` elemento. Uma função pode ter qualquer número de certificados associados. Para obter mais informações sobre a utilização do elemento certificado, consulte [alterar o ficheiro Definição de Serviço com um certificado](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="certificate"></a><a name="Certificate"></a> Certificado
O `Certificate` elemento descreve um certificado que está associado a um papel de trabalhador.

A tabela seguinte descreve os atributos do `Certificate` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|name|string|Obrigatório. Um nome para este certificado, que é usado para se referir a ele quando está associado a um `InputEndpoint` elemento HTTPS.|
|storeLocalização|string|Obrigatório. A localização da loja de certificados onde este certificado pode ser encontrado na máquina local. Os valores possíveis são `CurrentUser` `LocalMachine` e.|
|armazenarName|string|Obrigatório. O nome da loja de certificados onde este certificado reside na máquina local. Os valores possíveis incluem os nomes de lojas `My` incorporados, , , , , , , , `Root` , ou qualquer nome de `CA` loja `Trust` `Disallowed` `TrustedPeople` `TrustedPublisher` `AuthRoot` `AddressBook` personalizado. Se for especificado um nome de loja personalizado, a loja é criada automaticamente.|
|permissõesLevel|string|Opcional. Especifica as permissões de acesso dadas aos processos de função. Se pretender apenas processos elevados para poder aceder à chave privada, especifique `elevated` a permissão. `limitedOrElevated` a permissão permite que todos os processos de função acedam à chave privada. Os valores possíveis são `limitedOrElevated` ou `elevated`. O valor predefinido é `limitedOrElevated`.|

##  <a name="imports"></a><a name="Imports"></a> Importações
O `Imports` elemento descreve uma coleção de módulos de importação para uma função de trabalhador que adiciona componentes ao sistema operativo convidado. Este elemento é o progenitor do `Import` elemento. Este elemento é opcional e uma função pode ter apenas um bloco de tempo de execução.

O `Imports` elemento só está disponível utilizando a versão Azure SDK 1.3 ou superior.

##  <a name="import"></a><a name="Import"></a> Importação
O `Import` elemento especifica um módulo para adicionar ao sistema operativo do hóspede.

O `Import` elemento só está disponível utilizando a versão Azure SDK 1.3 ou superior.

A tabela seguinte descreve os atributos do `Import` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|nome do módulo|string|Obrigatório. O nome do módulo para importar. Os módulos de importação válidos são:<br /><br /> - RemoteAccess<br />- RemoteForwarder<br />- Diagnósticos<br /><br /> Os módulos RemoteAccess e RemoteForwarder permitem-lhe configurar a sua instância de função para ligações remotas de ambiente de trabalho. Para mais informações consulte [Extensões.](extensions.md)<br /><br /> O módulo de Diagnóstico permite-lhe recolher dados de diagnóstico para uma instância de função|

##  <a name="runtime"></a><a name="Runtime"></a> Tempo de execução
O `Runtime` elemento descreve uma coleção de configurações variáveis ambientais para um papel de trabalhador que controla o ambiente de execução do processo de hospedeiro Azure. Este elemento é o progenitor do `Environment` elemento. Este elemento é opcional e uma função pode ter apenas um bloco de tempo de execução.

O `Runtime` elemento só está disponível utilizando a versão Azure SDK 1.3 ou superior.

A tabela a seguir descreve os atributos do `Runtime` elemento:

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|execuçãoContexto|string|Opcional. Especifica o contexto em que o Processo de Função é lançado. O contexto padrão é `limited` .<br /><br /> -   `limited` – O processo é lançado sem privilégios de Administrador.<br />-   `elevated` – O processo é lançado com privilégios de Administrador.|

##  <a name="environment"></a><a name="Environment"></a> Ambiente
O `Environment` elemento descreve uma coleção de configurações variáveis ambientais para um papel de trabalhador. Este elemento é o progenitor do `Variable` elemento. Um papel pode ter qualquer número de variáveis ambientais definidas.

##  <a name="variable"></a><a name="Variable"></a> Variável
O `Variable` elemento especifica uma variável ambiental para definir no funcionamento do hóspede.

O `Variable` elemento só está disponível utilizando a versão Azure SDK 1.3 ou superior.

A tabela a seguir descreve os atributos do `Variable` elemento:

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|name|string|Obrigatório. O nome da variável ambiental a definir.|
|valor|string|Opcional. O valor a definir para a variável ambiental. Deve incluir um atributo de valor ou um `RoleInstanceValue` elemento.|

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a> RoleInstanceValue
O `RoleInstanceValue` elemento especifica o xPath a partir do qual recuperar o valor da variável.

A tabela seguinte descreve os atributos do `RoleInstanceValue` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|xpath|string|Opcional. Percurso de localização das definições de implantação, por exemplo. Para obter mais informações, consulte [variáveis de configuração com XPath](../cloud-services/cloud-services-role-config-xpath.md).<br /><br /> Deve incluir um atributo de valor ou um `RoleInstanceValue` elemento.|

##  <a name="entrypoint"></a><a name="EntryPoint"></a> Ponto de Entrada
O `EntryPoint` elemento especifica o ponto de entrada para uma função. Este elemento é o progenitor dos `NetFxEntryPoint` elementos. Estes elementos permitem especificar uma aplicação que não seja a WaWorkerHost.exe padrão para agir como o ponto de entrada da função.

O `EntryPoint` elemento só está disponível utilizando a versão Azure SDK 1.5 ou superior.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a> NetFxEntryPoint
O `NetFxEntryPoint` elemento especifica o programa a concorrer para um papel.

> [!NOTE]
>  O `NetFxEntryPoint` elemento só está disponível utilizando a versão Azure SDK 1.5 ou superior.

A tabela seguinte descreve os atributos do `NetFxEntryPoint` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|conjuntoName|string|Obrigatório. O caminho e o nome do ficheiro do conjunto que contém o ponto de entrada. O caminho é relativo à pasta **\\ %ROLEROOT%\Approot** (não especifique **\\ %ROLEROOT%\Approot** `commandLine` in, assume-se). **%ROLEROOT%** é uma variável ambiental mantida pelo Azure e representa a localização da pasta raiz para o seu papel. A pasta **\\ %ROLEROOT%\Approot** representa a pasta de aplicação para o seu papel.|
|targetFrameworkVersion|string|Obrigatório. A versão do quadro .NET em que foi construído o conjunto. Por exemplo, `targetFrameworkVersion="v4.0"`.|

##  <a name="programentrypoint"></a><a name="ProgramEntryPoint"></a> Ponto ProgramEntry
O `ProgramEntryPoint` elemento especifica o programa a concorrer para um papel. O `ProgramEntryPoint` elemento permite especificar um ponto de entrada do programa que não se baseia num conjunto .NET.

> [!NOTE]
>  O `ProgramEntryPoint` elemento só está disponível utilizando a versão Azure SDK 1.5 ou superior.

A tabela seguinte descreve os atributos do `ProgramEntryPoint` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|commandLine|string|Obrigatório. O caminho, o nome do ficheiro e quaisquer argumentos de linha de comando do programa para executar. O caminho é relativo à pasta **%ROLEROOT%\Approot** (não especifique **%ROLEROOT%\Approot** in commandLine, é assumido). **%ROLEROOT%** é uma variável ambiental mantida pelo Azure e representa a localização da pasta raiz para o seu papel. A pasta **%ROLEROOT%\Approot** representa a pasta de aplicação para o seu papel.<br /><br /> Se o programa terminar, o papel é reciclado, por isso, geralmente, define o programa para continuar a funcionar, em vez de ser um programa que apenas começa e executa uma tarefa finita.|
|setReadyOnProcessStart|boolean|Obrigatório. Especifica se a instância de função espera que o programa da linha de comando indique que foi iniciado. Este valor deve ser definido `true` neste momento. A definição do valor `false` é reservada para utilização futura.|

##  <a name="startup"></a><a name="Startup"></a> Arranque
O `Startup` elemento descreve uma coleção de tarefas que funcionam quando o papel é iniciado. Este elemento pode ser o pai do `Variable` elemento. Para obter mais informações sobre a utilização das tarefas de arranque de funções, consulte [Como configurar tarefas de arranque](../cloud-services/cloud-services-startup-tasks.md). Este elemento é opcional e uma função pode ter apenas um bloco de arranque.

A tabela seguinte descreve o atributo do `Startup` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|prioridade|int|Apenas para utilização interna.|

##  <a name="task"></a><a name="Task"></a> Tarefa
O `Task` elemento especifica a tarefa de arranque que ocorre quando a função começa. As tarefas de arranque podem ser usadas para executar tarefas que preparem a função para executar tais componentes de software ou executar outras aplicações. As tarefas executam na ordem em que aparecem dentro do `Startup` bloco de elementos.

O `Task` elemento só está disponível utilizando a versão Azure SDK 1.3 ou superior.

A tabela seguinte descreve os atributos do `Task` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|commandLine|string|Obrigatório. Um script, como um ficheiro CMD, contendo os comandos a executar. Os ficheiros de comando e lote de arranque devem ser guardados no formato ANSI. Os formatos de ficheiro que definem um marcador de encomenda de byte no início do ficheiro não serão processados corretamente.|
|execuçãoContexto|string|Especifica o contexto em que o script é executado.<br /><br /> -   `limited` [Predefinição] – Executar com os mesmos privilégios que o papel de anfitrião do processo.<br />-   `elevated` – Executar com privilégios de administrador.|
|taskType|string|Especifica o comportamento de execução do comando.<br /><br /> -   `simple` [Predefinição] – O sistema aguarda a saída da tarefa antes de serem lançadas outras tarefas.<br />-   `background` – O sistema não espera que a tarefa saia.<br />-   `foreground` – Semelhante ao passado, exceto que o papel não é reiniciado até que todas as tarefas de primeiro plano saiam.|

##  <a name="contents"></a><a name="Contents"></a> Conteúdo
O `Contents` elemento descreve a recolha de conteúdos para um papel de trabalhador. Este elemento é o progenitor do `Content` elemento.

O `Contents` elemento só está disponível utilizando a versão Azure SDK 1.5 ou superior.

##  <a name="content"></a><a name="Content"></a> Conteúdo
O `Content` elemento define a localização de origem dos conteúdos a copiar para a máquina virtual Azure e o caminho de destino para o qual é copiado.

O `Content` elemento só está disponível utilizando a versão Azure SDK 1.5 ou superior.

A tabela seguinte descreve os atributos do `Content` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|destino|string|Obrigatório. Localização na máquina virtual Azure à qual o conteúdo é colocado. Esta localização é relativa à pasta **%ROLEROOT%\Approot**.|

Este elemento é o elemento-mãe do `SourceDirectory` elemento.

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a> Diretório de Origem
O `SourceDirectory` elemento define o diretório local a partir do qual o conteúdo é copiado. Utilize este elemento para especificar o conteúdo local para copiar para a máquina virtual Azure.

O `SourceDirectory` elemento só está disponível utilizando a versão Azure SDK 1.5 ou superior.

A tabela seguinte descreve os atributos do `SourceDirectory` elemento.

| Atributo | Tipo | Description |
| --------- | ---- | ----------- |
|caminho|string|Obrigatório. Percurso relativo ou absoluto de um diretório local cujo conteúdo será copiado para a máquina virtual Azure. A expansão das variáveis ambientais no percurso do diretório é suportada.|

## <a name="see-also"></a>Ver também
[Sistema de Definição do Serviço de Nuvem (suporte alargado) Definição](schema-csdef-file.md).



