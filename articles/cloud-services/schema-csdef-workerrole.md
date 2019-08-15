---
title: Def. de serviços de nuvem do Azure. Esquema WorkerRole | Microsoft Docs
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
author: georgewallace
ms.author: gwallace
manager: gwallace
ms.openlocfilehash: ac1cce61a9b9b22c079066147d6a318f778999db
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945882"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Esquema WorkerRole de definição de serviços de nuvem do Azure
A função de trabalho do Azure é uma função que é útil para desenvolvimento generalizado e pode executar o processamento em segundo plano para uma função Web.

A extensão padrão para o arquivo de definição de serviço é. csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Esquema de definição de serviço básico para uma função de trabalho.
O formato básico do arquivo de definição de serviço que contém uma função de trabalho é o seguinte.

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
O arquivo de definição de serviço inclui esses elementos, descritos em detalhes nas seções subsequentes neste tópico:

[WorkerRole](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[Definição](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Extremidade](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certificados](#Certificates)

[Certificado](#Certificate)

[Importar](#Imports)

[Importar](#Import)

[Appmodel](#Runtime)

[Ambiente](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Variable](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Inicialização](#Startup)

[Tarefa](#Task)

[Índice](#Contents)

[Conteúdo](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WorkerRole"></a>WorkerRole
O `WorkerRole` elemento descreve uma função que é útil para desenvolvimento generalizado e pode executar o processamento em segundo plano para uma função Web. Um serviço pode conter zero ou mais funções de trabalho.

A tabela a seguir descreve os atributos do `WorkerRole` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|Cadeia de caracteres|Necessário. O nome da função de trabalho. O nome da função deve ser exclusivo.|
|enableNativeCodeExecution|boolean|Opcional. O valor padrão é `true`; a execução de código nativo e a confiança total estão habilitadas por padrão. Defina esse atributo como `false` para desabilitar a execução de código nativo para a função de trabalho e use a confiança parcial do Azure em vez disso.|
|vmsize|Cadeia de caracteres|Opcional. Defina esse valor para alterar o tamanho da máquina virtual que é alocada para essa função. O valor predefinido é `Small`. Para obter uma lista de tamanhos de máquinas virtuais possíveis e seus atributos, consulte [tamanhos de máquina virtual para serviços de nuvem](cloud-services-sizes-specs.md).|

##  <a name="ConfigurationSettings"></a>ConfigurationSettings
O `ConfigurationSettings` elemento descreve a coleção de definições de configuração para uma função de trabalho. Esse elemento é o pai do `Setting` elemento.

##  <a name="Setting"></a>Configuração
O `Setting` elemento descreve um par de nome e valor que especifica um parâmetro de configuração para uma instância de uma função.

A tabela a seguir descreve os atributos do `Setting` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|Cadeia de caracteres|Necessário. Um nome exclusivo para a definição de configuração.|

Os parâmetros de configuração para uma função são pares de nome e valor declarados no arquivo de definição de serviço e definidos no arquivo de configuração de serviço.

##  <a name="LocalResources"></a>LocalResources
O `LocalResources` elemento descreve a coleção de recursos de armazenamento local para uma função de trabalho. Esse elemento é o pai do `LocalStorage` elemento.

##  <a name="LocalStorage"></a>LocalStorage
O `LocalStorage` elemento identifica um recurso de armazenamento local que fornece o espaço do sistema de arquivos para o serviço em tempo de execução. Uma função pode definir zero ou mais recursos de armazenamento local.

> [!NOTE]
>  O `LocalStorage` elemento pode aparecer como um filho `WorkerRole` do elemento para dar suporte à compatibilidade com versões anteriores do SDK do Azure.

A tabela a seguir descreve os atributos do `LocalStorage` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|Cadeia de caracteres|Necessário. Um nome exclusivo para o repositório local.|
|cleanOnRoleRecycle|boolean|Opcional. Indica se o repositório local deve ser limpo quando a função é reiniciada. O valor padrão `true`é.|
|sizeInMb|int|Opcional. A quantidade desejada de espaço de armazenamento a ser alocada para o repositório local, em MB. Se não for especificado, o espaço de armazenamento padrão alocado será de 100 MB. A quantidade mínima de espaço de armazenamento que pode ser alocada é 1 MB.<br /><br /> O tamanho máximo dos recursos locais depende do tamanho da máquina virtual. Para obter mais informações, consulte [tamanhos de máquina virtual para serviços de nuvem](cloud-services-sizes-specs.md).|

O nome do diretório alocado para o recurso de armazenamento local corresponde ao valor fornecido para o atributo Name.

##  <a name="Endpoints"></a>Extremidade
O `Endpoints` elemento descreve a coleção de pontos de extremidade de entrada (externo), interno e de entrada de instância para uma função. Esse elemento é o pai dos `InputEndpoint`elementos, `InternalEndpoint`e. `InstanceInputEndpoint`

Os pontos de extremidade internos e de entrada são alocados separadamente. Um serviço pode ter um total de 25 pontos de extremidade de entrada, internos e de entrada de instância que podem ser alocados entre as 25 funções permitidas em um serviço. Por exemplo, se tiver 5 funções, você poderá alocar 5 pontos de extremidade de entrada por função ou pode alocar 25 pontos de extremidade de entrada para uma única função, ou você pode alocar 1 ponto final de entrada a cada 25 funções.

> [!NOTE]
>  Cada função implantada requer uma instância por função. O provisionamento padrão de uma assinatura é limitado a 20 núcleos e, portanto, é limitado a 20 instâncias de uma função. Se seu aplicativo exigir mais instâncias do que o fornecido pelo provisionamento padrão [, consulte cobrança, gerenciamento de assinaturas e suporte a cota](https://azure.microsoft.com/support/options/) para obter mais informações sobre como aumentar sua cota.

##  <a name="InputEndpoint"></a>InputEndpoint
O `InputEndpoint` elemento descreve um ponto de extremidade externo para uma função de trabalho.

Você pode definir vários pontos de extremidade que são uma combinação de pontos de extremidade HTTP, HTTPS, UDP e TCP. Você pode especificar qualquer número de porta que escolher para um ponto de extremidade de entrada, mas os números de porta especificados para cada função no serviço devem ser exclusivos. Por exemplo, se você especificar que uma função usa a porta 80 para HTTP e a porta 443 para HTTPS, você pode especificar que uma segunda função usa a porta 8080 para HTTP e a porta 8043 para HTTPS.

A tabela a seguir descreve os atributos do `InputEndpoint` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|Cadeia de caracteres|Necessário. Um nome exclusivo para o ponto de extremidade externo.|
|protocol|Cadeia de caracteres|Necessário. O protocolo de transporte para o ponto de extremidade externo. Para uma função de trabalho, os valores `HTTP`possíveis `HTTPS`são `UDP`,, `TCP`ou.|
|port|int|Necessário. A porta para o ponto de extremidade externo. Você pode especificar qualquer número de porta que escolher, mas os números de porta especificados para cada função no serviço devem ser exclusivos.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1,7 ou superior).|
|certificado|Cadeia de caracteres|Necessário para um ponto de extremidade HTTPS. O nome de um certificado definido por um `Certificate` elemento.|
|localPort|int|Opcional. Especifica uma porta usada para conexões internas no ponto de extremidade. O `localPort` atributo mapeia a porta externa no ponto de extremidade para uma porta interna em uma função. Isso é útil em cenários em que uma função deve se comunicar com um componente interno em uma porta diferente da que é exposta externamente.<br /><br /> Se não for especificado, o valor `localPort` de será o mesmo que `port` o atributo. Defina o valor de `localPort` como "*" para atribuir automaticamente uma porta não alocada que seja detectável usando a API de tempo de execução.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1,7 ou superior).<br /><br /> O `localPort` atributo só está disponível usando o SDK do Azure versão 1,3 ou superior.|
|ignoreRoleInstanceStatus|boolean|Opcional. Quando o valor desse atributo é definido como `true`, o status de um serviço é ignorado e o ponto de extremidade não será removido pelo balanceador de carga. Definir esse valor como `true` útil para depurar instâncias ocupadas de um serviço. O valor predefinido é `false`. **Nota:** Um ponto de extremidade ainda pode receber tráfego mesmo quando a função não está em um estado pronto.|
|loadBalancerProbe|Cadeia de caracteres|Opcional. O nome da investigação do balanceador de carga associada ao ponto de extremidade de entrada. Para obter mais informações, consulte [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md).|

##  <a name="InternalEndpoint"></a>InternalEndpoint
O `InternalEndpoint` elemento descreve um ponto de extremidade interno para uma função de trabalho. Um ponto de extremidade interno está disponível somente para outras instâncias de função em execução no serviço; Ele não está disponível para clientes fora do serviço. Uma função de trabalho pode ter até cinco pontos de extremidade internos HTTP, UDP ou TCP.

A tabela a seguir descreve os atributos do `InternalEndpoint` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|Cadeia de caracteres|Necessário. Um nome exclusivo para o ponto de extremidade interno.|
|protocol|Cadeia de caracteres|Necessário. O protocolo de transporte para o ponto de extremidade interno. Os valores possíveis `HTTP`são `TCP` `UDP`,, ou. `ANY`<br /><br /> Um valor de `ANY` especifica que qualquer protocolo, qualquer porta é permitida.|
|port|int|Opcional. A porta usada para conexões de balanceamento de carga interno no ponto de extremidade. Um ponto de extremidade com balanceamento de carga usa duas portas. A porta usada para o endereço IP público e a porta usada no endereço IP privado. Normalmente, elas são definidas para o mesmo, mas você pode optar por usar portas diferentes.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1,7 ou superior).<br /><br /> O `Port` atributo só está disponível usando o SDK do Azure versão 1,3 ou superior.|

##  <a name="InstanceInputEndpoint"></a>InstanceInputEndpoint
O `InstanceInputEndpoint` elemento descreve um ponto de extremidade de entrada de instância para uma função de trabalho. Um ponto de extremidade de entrada de instância está associado a uma instância de função específica usando o encaminhamento de porta no balanceador de carga. Cada ponto de extremidade de entrada de instância é mapeado para uma porta específica de um intervalo de portas possíveis. Esse elemento é o pai do `AllocatePublicPortFrom` elemento.

O `InstanceInputEndpoint` elemento só está disponível usando o SDK do Azure versão 1,7 ou superior.

A tabela a seguir descreve os atributos do `InstanceInputEndpoint` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|Cadeia de caracteres|Necessário. Um nome exclusivo para o ponto de extremidade.|
|localPort|int|Necessário. Especifica a porta interna que todas as instâncias de função escutarão a fim de receber o tráfego de entrada encaminhado do balanceador de carga. Os valores possíveis variam entre 1 e 65535, inclusive.|
|protocol|Cadeia de caracteres|Necessário. O protocolo de transporte para o ponto de extremidade interno. Os valores possíveis são `udp` ou `tcp`. Use `tcp` para tráfego baseado em http/https.|

##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom
O `AllocatePublicPortFrom` elemento descreve o intervalo de portas públicas que pode ser usado por clientes externos para acessar cada ponto de extremidade de entrada de instância. O número da porta pública (VIP) é alocado desse intervalo e atribuído a cada ponto de extremidade de instância de função individual durante a implantação e a atualização do locatário. Esse elemento é o pai do `FixedPortRange` elemento.

O `AllocatePublicPortFrom` elemento só está disponível usando o SDK do Azure versão 1,7 ou superior.

##  <a name="FixedPort"></a>FixedPort
O `FixedPort` elemento Especifica a porta para o ponto de extremidade interno, que permite conexões com balanceamento de carga no ponto de extremidade.

O `FixedPort` elemento só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do `FixedPort` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|port|int|Necessário. A porta para o ponto de extremidade interno. Isso tem o mesmo efeito que definir o `FixedPortRange` mínimo e o máximo como a mesma porta.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1,7 ou superior).|

##  <a name="FixedPortRange"></a> FixedPortRange
O `FixedPortRange` elemento Especifica o intervalo de portas que são atribuídas ao ponto de extremidade interno ou de entrada de instância e define a porta usada para conexões com balanceamento de carga no ponto de extremidade.

> [!NOTE]
>  O `FixedPortRange` elemento funciona de forma diferente, dependendo do elemento no qual ele reside. Quando o `FixedPortRange` elemento está `InternalEndpoint` no elemento, ele abre todas as portas no balanceador de carga dentro do intervalo dos atributos mínimo e máximo para todas as máquinas virtuais em que a função é executada. Quando o `FixedPortRange` elemento está `InstanceInputEndpoint` no elemento, ele abre apenas uma porta dentro do intervalo dos atributos mínimo e máximo em cada máquina virtual que executa a função.

O `FixedPortRange` elemento só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do `FixedPortRange` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|mín.|int|Necessário. A porta mínima no intervalo. Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1,7 ou superior).|
|máx.|Cadeia de caracteres|Necessário. A porta máxima no intervalo. Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1,7 ou superior).|

##  <a name="Certificates"></a>Certificado
O `Certificates` elemento descreve a coleção de certificados para uma função de trabalho. Esse elemento é o pai do `Certificate` elemento. Uma função pode ter qualquer número de certificados associados. Para obter mais informações sobre como usar o elemento Certificates, consulte [Modificar o arquivo de definição de serviço com um certificado](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a>Certificate
O `Certificate` elemento descreve um certificado associado a uma função de trabalho.

A tabela a seguir descreve os atributos do `Certificate` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|Cadeia de caracteres|Necessário. Um nome para esse certificado, que é usado para fazer referência a ele quando ele está associado a um `InputEndpoint` elemento HTTPS.|
|storeLocation|Cadeia de caracteres|Necessário. O local do repositório de certificados em que esse certificado pode ser encontrado no computador local. Os valores possíveis `CurrentUser` são `LocalMachine`e.|
|storeName|Cadeia de caracteres|Necessário. O nome do repositório de certificados em que esse certificado reside no computador local. Os valores `My`possíveis incluem os nomes de repositório internos, `Root` `Disallowed` `TrustedPeople` ,`TrustedPublisher` `Trust` `CA` ,,,,,,ouqualquernomederepositóriopersonalizado.`AuthRoot` `AddressBook` Se um nome de repositório personalizado for especificado, o repositório será criado automaticamente.|
|permissionLevel|Cadeia de caracteres|Opcional. Especifica as permissões de acesso dadas aos processos de função. Se você quiser que apenas processos elevados possam acessar a chave privada, especifique `elevated` a permissão. `limitedOrElevated`a permissão permite que todos os processos de função acessem a chave privada. Os valores possíveis são `limitedOrElevated` ou `elevated`. O valor predefinido é `limitedOrElevated`.|

##  <a name="Imports"></a>Importar
O `Imports` elemento descreve uma coleção de módulos de importação para uma função de trabalho que adiciona componentes ao sistema operacional convidado. Esse elemento é o pai do `Import` elemento. Esse elemento é opcional e uma função pode ter apenas um bloco de tempo de execução.

O `Imports` elemento só está disponível usando o SDK do Azure versão 1,3 ou superior.

##  <a name="Import"></a>Importe
O `Import` elemento especifica um módulo a ser adicionado ao sistema operacional convidado.

O `Import` elemento só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do `Import` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|moduleName|Cadeia de caracteres|Necessário. O nome do módulo a ser importado. Os módulos de importação válidos são:<br /><br /> -   RemoteAccess<br />-   RemoteForwarder<br />-Diagnóstico<br /><br /> Os módulos RemoteAccess e RemoteForwarder permitem que você configure sua instância de função para conexões de área de trabalho remota. Para obter mais informações, consulte [habilitar conexão de área de trabalho remota](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> O módulo diagnóstico permite coletar dados de diagnóstico para uma instância de função|

##  <a name="Runtime"></a>Appmodel
O `Runtime` elemento descreve uma coleção de configurações de variável de ambiente para uma função de trabalho que controla o ambiente de tempo de execução do processo de host do Azure. Esse elemento é o pai do `Environment` elemento. Esse elemento é opcional e uma função pode ter apenas um bloco de tempo de execução.

O `Runtime` elemento só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do `Runtime` elemento:

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|executionContext|Cadeia de caracteres|Opcional. Especifica o contexto no qual o processo de função é iniciado. O contexto padrão é `limited`.<br /><br /> -   `limited`– O processo é iniciado sem privilégios de administrador.<br />-   `elevated`– O processo é iniciado com privilégios de administrador.|

##  <a name="Environment"></a>Ambiente
O `Environment` elemento descreve uma coleção de configurações de variável de ambiente para uma função de trabalho. Esse elemento é o pai do `Variable` elemento. Uma função pode ter qualquer número de variáveis de ambiente definidas.

##  <a name="Variable"></a>Ela
O `Variable` elemento Especifica uma variável de ambiente a ser definida na operação de convidado.

O `Variable` elemento só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do `Variable` elemento:

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|Cadeia de caracteres|Necessário. O nome da variável de ambiente a ser definida.|
|value|Cadeia de caracteres|Opcional. O valor a ser definido para a variável de ambiente. Você deve incluir um atributo Value ou um `RoleInstanceValue` elemento.|

##  <a name="RoleInstanceValue"></a> RoleInstanceValue
O `RoleInstanceValue` elemento Especifica o XPath do qual recuperar o valor da variável.

A tabela a seguir descreve os atributos do `RoleInstanceValue` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|xpath|Cadeia de caracteres|Opcional. Caminho do local das configurações de implantação da instância. Para obter mais informações, consulte [variáveis de configuração com XPath](cloud-services-role-config-xpath.md).<br /><br /> Você deve incluir um atributo Value ou um `RoleInstanceValue` elemento.|

##  <a name="EntryPoint"></a> EntryPoint
O `EntryPoint` elemento Especifica o ponto de entrada para uma função. Esse elemento é o pai dos `NetFxEntryPoint` elementos. Esses elementos permitem que você especifique um aplicativo que não seja o WaWorkerHost. exe padrão para atuar como o ponto de entrada de função.

O `EntryPoint` elemento só está disponível usando o SDK do Azure versão 1,5 ou superior.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint
O `NetFxEntryPoint` elemento Especifica o programa a ser executado para uma função.

> [!NOTE]
>  O `NetFxEntryPoint` elemento só está disponível usando o SDK do Azure versão 1,5 ou superior.

A tabela a seguir descreve os atributos do `NetFxEntryPoint` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|assemblyName|Cadeia de caracteres|Necessário. O caminho e o nome do arquivo do assembly que contém o ponto de entrada. O caminho é relativo à pasta  **\\%ROLEROOT%\Approot** (não especifique  **\\%ROLEROOT%\Approot** em `commandLine`, é pressuposto). **% ROLEROOT%** é uma variável de ambiente mantida pelo Azure e representa o local da pasta raiz para sua função. A pasta%ROLEROOT%\Approot representa a pasta do aplicativo para sua função.  **\\**|
|targetFrameworkVersion|Cadeia de caracteres|Necessário. A versão do .NET Framework na qual o assembly foi criado. Por exemplo, `targetFrameworkVersion="v4.0"`.|

##  <a name="ProgramEntryPoint"></a> ProgramEntryPoint
O `ProgramEntryPoint` elemento Especifica o programa a ser executado para uma função. O `ProgramEntryPoint` elemento permite que você especifique um ponto de entrada do programa que não se baseia em um assembly .net.

> [!NOTE]
>  O `ProgramEntryPoint` elemento só está disponível usando o SDK do Azure versão 1,5 ou superior.

A tabela a seguir descreve os atributos do `ProgramEntryPoint` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|commandLine|Cadeia de caracteres|Necessário. O caminho, o nome do arquivo e todos os argumentos de linha de comando do programa a ser executado. O caminho é relativo à pasta **%ROLEROOT%\Approot** (não especifique **%ROLEROOT%\Approot** em CommandLine, ele é presumido). **% ROLEROOT%** é uma variável de ambiente mantida pelo Azure e representa o local da pasta raiz para sua função. A pasta **%ROLEROOT%\Approot** representa a pasta do aplicativo para sua função.<br /><br /> Se o programa terminar, a função será reciclada, de modo geral, defina o programa para continuar a ser executado, em vez de ser um programa que simplesmente inicia e executa uma tarefa finita.|
|setReadyOnProcessStart|boolean|Necessário. Especifica se a instância de função aguarda até que o programa de linha de comando o sinalize seja iniciado. Esse valor deve ser definido como `true` neste momento. Definir o valor como `false` é reservado para uso futuro.|

##  <a name="Startup"></a>Inicialização
O `Startup` elemento descreve uma coleção de tarefas que são executadas quando a função é iniciada. Esse elemento pode ser o pai do `Variable` elemento. Para obter mais informações sobre como usar as tarefas de inicialização de função, consulte [como configurar tarefas de inicialização](cloud-services-startup-tasks.md). Esse elemento é opcional e uma função pode ter apenas um bloco de inicialização.

A tabela a seguir descreve o atributo do `Startup` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|priority|int|Somente para uso interno.|

##  <a name="Task"></a>Agenda
O `Task` elemento Especifica a tarefa de inicialização que ocorre quando a função é iniciada. As tarefas de inicialização podem ser usadas para executar tarefas que preparam a função para executar esses componentes de software de instalação ou executar outros aplicativos. As tarefas são executadas na ordem em que aparecem dentro do `Startup` bloco de elemento.

O `Task` elemento só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do `Task` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|commandLine|Cadeia de caracteres|Necessário. Um script, como um arquivo CMD, que contém os comandos a serem executados. O comando de inicialização e os arquivos em lotes devem ser salvos no formato ANSI. Os formatos de arquivo que definem um marcador de ordem de byte no início do arquivo não serão processados corretamente.|
|executionContext|Cadeia de caracteres|Especifica o contexto no qual o script é executado.<br /><br /> -   `limited`[Padrão] – execute com os mesmos privilégios que a função que hospeda o processo.<br />-   `elevated`– Execute com privilégios de administrador.|
|taskType|Cadeia de caracteres|Especifica o comportamento de execução do comando.<br /><br /> -   `simple`[Padrão] – o sistema aguarda a tarefa ser encerrada antes de qualquer outra tarefa ser iniciada.<br />-   `background`– O sistema não aguarda a tarefa ser encerrada.<br />-   `foreground`– Semelhante ao plano de fundo, exceto que a função não é reiniciada até que todas as tarefas de primeiro plano sejam encerradas.|

##  <a name="Contents"></a>Índice
O `Contents` elemento descreve a coleção de conteúdo para uma função de trabalho. Esse elemento é o pai do `Content` elemento.

O `Contents` elemento só está disponível usando o SDK do Azure versão 1,5 ou superior.

##  <a name="Content"></a>Disputa
O `Content` elemento define o local de origem do conteúdo a ser copiado para a máquina virtual do Azure e o caminho de destino para o qual ele é copiado.

O `Content` elemento só está disponível usando o SDK do Azure versão 1,5 ou superior.

A tabela a seguir descreve os atributos do `Content` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|destination|Cadeia de caracteres|Necessário. Local na máquina virtual do Azure na qual o conteúdo é colocado. Esse local é relativo à pasta **%ROLEROOT%\Approot**.|

Esse elemento é o elemento pai do `SourceDirectory` elemento.

##  <a name="SourceDirectory"></a>SourceDirectory
O `SourceDirectory` elemento define o diretório local do qual o conteúdo é copiado. Use este elemento para especificar o conteúdo local a ser copiado para a máquina virtual do Azure.

O `SourceDirectory` elemento só está disponível usando o SDK do Azure versão 1,5 ou superior.

A tabela a seguir descreve os atributos do `SourceDirectory` elemento.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|path|Cadeia de caracteres|Necessário. Caminho relativo ou absoluto de um diretório local cujo conteúdo será copiado para a máquina virtual do Azure. Há suporte para a expansão de variáveis de ambiente no caminho de diretório.|

## <a name="see-also"></a>Consultar Também
[Esquema de definição do serviço de nuvem (clássico)](schema-csdef-file.md)