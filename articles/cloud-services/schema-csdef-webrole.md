---
title: Esquema de Def. WebRole dos serviços de nuvem do Azure | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 85368e4e-a0db-4c02-8dbc-8e2928fa6091
caps.latest.revision: 60
author: tgore03
ms.author: tagore
ms.openlocfilehash: 1e4413f3495790e5e477f424bb5debcfbc186ade
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385464"
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Esquema de WebRole de definição dos serviços de nuvem do Azure
A função Web do Azure é uma função que é personalizada para programação de aplicativo Web com suporte do IIS 7, como ASP.NET, PHP, Windows Communication Foundation e FastCGI.

A extensão padrão para o arquivo de definição de serviço é. csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Esquema de definição de serviço básico para uma função Web  
O formato básico de um arquivo de definição de serviço que contém uma função Web é o seguinte.

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
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
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
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
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Elementos de esquema  
O arquivo de definição de serviço inclui esses elementos, descritos em detalhes nas seções subsequentes neste tópico:  

[WebRole](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[Definição](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Extremidade](#Endpoints)

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

[Variable](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Sites](#Sites)

[Site](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Associações](#Bindings)

[Ligação](#Binding)

[Startup](#Startup)

[Tarefa](#Task)

[Índice](#Contents)

[Conteúdo](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a> WebRole  
O elemento `WebRole` descreve uma função que é personalizada para programação de aplicativo Web, com suporte do IIS 7 e do ASP.NET. Um serviço pode conter zero ou mais funções Web.

A tabela a seguir descreve os atributos do elemento `WebRole`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|string|Necessário. O nome da função Web. O nome da função deve ser exclusivo.|  
|enableNativeCodeExecution|boolean|Opcional. O valor padrão é `true`; a execução de código nativo e a confiança total são habilitadas por padrão. Defina esse atributo como `false` para desabilitar a execução de código nativo para a função Web e use a confiança parcial do Azure em vez disso.|  
|vmsize|string|Opcional. Defina esse valor para alterar o tamanho da máquina virtual que é alocada para a função. O valor predefinido é `Small`. Para obter mais informações, consulte [tamanhos de máquina virtual para serviços de nuvem](cloud-services-sizes-specs.md).|  

##  <a name="ConfigurationSettings"></a>ConfigurationSettings  
O elemento `ConfigurationSettings` descreve a coleção de definições de configuração para uma função Web. Esse elemento é o pai do elemento `Setting`.

##  <a name="Setting"></a>Configuração  
O elemento `Setting` descreve um par de nome e valor que especifica um parâmetro de configuração para uma instância de uma função.

A tabela a seguir descreve os atributos do elemento `Setting`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|string|Necessário. Um nome exclusivo para a definição de configuração.|  

Os parâmetros de configuração para uma função são pares de nome e valor declarados no arquivo de definição de serviço e definidos no arquivo de configuração de serviço.

##  <a name="LocalResources"></a>LocalResources  
O elemento `LocalResources` descreve a coleção de recursos de armazenamento local para uma função Web. Esse elemento é o pai do elemento `LocalStorage`.

##  <a name="LocalStorage"></a>LocalStorage  
O elemento `LocalStorage` identifica um recurso de armazenamento local que fornece o espaço do sistema de arquivos para o serviço em tempo de execução. Uma função pode definir zero ou mais recursos de armazenamento local.

> [!NOTE]
>  O elemento `LocalStorage` pode aparecer como um filho do elemento `WebRole` para dar suporte à compatibilidade com versões anteriores do SDK do Azure.

A tabela a seguir descreve os atributos do elemento `LocalStorage`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|string|Necessário. Um nome exclusivo para o repositório local.|  
|cleanOnRoleRecycle|boolean|Opcional. Indica se o repositório local deve ser limpo quando a função é reiniciada. O valor padrão é `true`.|  
|sizeInMb|int|Opcional. A quantidade desejada de espaço de armazenamento a ser alocada para o repositório local, em MB. Se não for especificado, o espaço de armazenamento padrão alocado será de 100 MB. A quantidade mínima de espaço de armazenamento que pode ser alocada é 1 MB.<br /><br /> O tamanho máximo dos recursos locais depende do tamanho da máquina virtual. Para obter mais informações, consulte [tamanhos de máquina virtual para serviços de nuvem](cloud-services-sizes-specs.md).|  
  
O nome do diretório alocado para o recurso de armazenamento local corresponde ao valor fornecido para o atributo Name.

##  <a name="Endpoints"></a>Extremidade  
O elemento `Endpoints` descreve a coleção de pontos de extremidade de entrada (externo), interno e de entrada de instância para uma função. Esse elemento é o pai dos elementos `InputEndpoint`, `InternalEndpoint`e `InstanceInputEndpoint`.

Os pontos de extremidade internos e de entrada são alocados separadamente. Um serviço pode ter um total de 25 pontos de extremidade de entrada, internos e de entrada de instância que podem ser alocados entre as 25 funções permitidas em um serviço. Por exemplo, se tiver 5 funções, você poderá alocar 5 pontos de extremidade de entrada por função ou pode alocar 25 pontos de extremidade de entrada para uma única função, ou você pode alocar 1 ponto final de entrada a cada 25 funções.

> [!NOTE]
>  Cada função implantada requer uma instância por função. O provisionamento padrão de uma assinatura é limitado a 20 núcleos e, portanto, é limitado a 20 instâncias de uma função. Se seu aplicativo exigir mais instâncias do que o fornecido pelo provisionamento padrão [, consulte cobrança, gerenciamento de assinaturas e suporte a cota](https://azure.microsoft.com/support/options/) para obter mais informações sobre como aumentar sua cota.

##  <a name="InputEndpoint"></a>InputEndpoint  
O elemento `InputEndpoint` descreve um ponto de extremidade externo para uma função Web.

Você pode definir vários pontos de extremidade que são uma combinação de pontos de extremidade HTTP, HTTPS, UDP e TCP. Você pode especificar qualquer número de porta que escolher para um ponto de extremidade de entrada, mas os números de porta especificados para cada função no serviço devem ser exclusivos. Por exemplo, se você especificar que uma função Web usa a porta 80 para HTTP e a porta 443 para HTTPS, você pode especificar que uma segunda função Web usa a porta 8080 para HTTP e a porta 8043 para HTTPS.

A tabela a seguir descreve os atributos do elemento `InputEndpoint`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|string|Necessário. Um nome exclusivo para o ponto de extremidade externo.|  
|protocol|string|Necessário. O protocolo de transporte para o ponto de extremidade externo. Para uma função Web, os valores possíveis são `HTTP`, `HTTPS`, `UDP`ou `TCP`.|  
|porta|int|Necessário. A porta para o ponto de extremidade externo. Você pode especificar qualquer número de porta que escolher, mas os números de porta especificados para cada função no serviço devem ser exclusivos.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1,7 ou superior).|  
|certificado|string|Necessário para um ponto de extremidade HTTPS. O nome de um certificado definido por um elemento `Certificate`.|  
|localPort|int|Opcional. Especifica uma porta usada para conexões internas no ponto de extremidade. O atributo `localPort` mapeia a porta externa no ponto de extremidade para uma porta interna em uma função. Isso é útil em cenários em que uma função deve se comunicar com um componente interno em uma porta diferente da que é exposta externamente.<br /><br /> Se não for especificado, o valor de `localPort` será o mesmo que o atributo `port`. Defina o valor de `localPort` como "*" para atribuir automaticamente uma porta não alocada que é detectável usando a API de tempo de execução.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1,7 ou superior).<br /><br /> O atributo `localPort` só está disponível usando o SDK do Azure versão 1,3 ou superior.|  
|ignoreRoleInstanceStatus|boolean|Opcional. Quando o valor desse atributo é definido como `true`, o status de um serviço é ignorado e o ponto de extremidade não será removido pelo balanceador de carga. Definir esse valor como `true` útil para a depuração de instâncias ocupadas de um serviço. O valor predefinido é `false`. **Observação:**  Um ponto de extremidade ainda pode receber tráfego mesmo quando a função não está em um estado pronto.|  
|loadBalancerProbe|string|Opcional. O nome da investigação do balanceador de carga associada ao ponto de extremidade de entrada. Para obter mais informações, consulte [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md).|  

##  <a name="InternalEndpoint"></a>InternalEndpoint  
O elemento `InternalEndpoint` descreve um ponto de extremidade interno para uma função Web. Um ponto de extremidade interno está disponível somente para outras instâncias de função em execução no serviço; Ele não está disponível para clientes fora do serviço. As funções Web que não incluem o elemento `Sites` só podem ter um único ponto de extremidade interno HTTP, UDP ou TCP.

A tabela a seguir descreve os atributos do elemento `InternalEndpoint`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|string|Necessário. Um nome exclusivo para o ponto de extremidade interno.|  
|protocol|string|Necessário. O protocolo de transporte para o ponto de extremidade interno. Os valores possíveis são `HTTP`, `TCP`, `UDP`ou `ANY`.<br /><br /> Um valor de `ANY` especifica que qualquer protocolo, qualquer porta é permitida.|  
|porta|int|Opcional. A porta usada para conexões de balanceamento de carga interno no ponto de extremidade. Um ponto de extremidade com balanceamento de carga usa duas portas. A porta usada para o endereço IP público e a porta usada no endereço IP privado. Normalmente, elas são definidas para o mesmo, mas você pode optar por usar portas diferentes.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1,7 ou superior).<br /><br /> O atributo `Port` só está disponível usando o SDK do Azure versão 1,3 ou superior.|  

##  <a name="InstanceInputEndpoint"></a>InstanceInputEndpoint  
O elemento `InstanceInputEndpoint` descreve um ponto de extremidade de entrada de instância para uma função Web. Um ponto de extremidade de entrada de instância está associado a uma instância de função específica usando o encaminhamento de porta no balanceador de carga. Cada ponto de extremidade de entrada de instância é mapeado para uma porta específica de um intervalo de portas possíveis. Esse elemento é o pai do elemento `AllocatePublicPortFrom`.

O elemento `InstanceInputEndpoint` só está disponível usando o SDK do Azure versão 1,7 ou superior.

A tabela a seguir descreve os atributos do elemento `InstanceInputEndpoint`.
  
| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|string|Necessário. Um nome exclusivo para o ponto de extremidade.|  
|localPort|int|Necessário. Especifica a porta interna que todas as instâncias de função escutarão a fim de receber o tráfego de entrada encaminhado do balanceador de carga. Os valores possíveis variam entre 1 e 65535, inclusive.|  
|protocol|string|Necessário. O protocolo de transporte para o ponto de extremidade interno. Os valores possíveis são `udp` ou `tcp`. Use `tcp` para tráfego baseado em http/https.|  
  
##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
O elemento `AllocatePublicPortFrom` descreve o intervalo de portas públicas que pode ser usado por clientes externos para acessar cada ponto de extremidade de entrada de instância. O número da porta pública (VIP) é alocado desse intervalo e atribuído a cada ponto de extremidade de instância de função individual durante a implantação e a atualização do locatário. Esse elemento é o pai do elemento `FixedPortRange`.

O elemento `AllocatePublicPortFrom` só está disponível usando o SDK do Azure versão 1,7 ou superior.

##  <a name="FixedPort"></a>FixedPort  
O elemento `FixedPort` especifica a porta para o ponto de extremidade interno, que permite conexões com balanceamento de carga no ponto de extremidade.

O elemento `FixedPort` só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do elemento `FixedPort`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|porta|int|Necessário. A porta para o ponto de extremidade interno. Isso tem o mesmo efeito que definir o `FixedPortRange` mín. e máx. para a mesma porta.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1,7 ou superior).|  

##  <a name="FixedPortRange"></a> FixedPortRange  
O elemento `FixedPortRange` especifica o intervalo de portas que são atribuídas ao ponto de extremidade interno ou de entrada de instância e define a porta usada para conexões com balanceamento de carga no ponto de extremidade.

> [!NOTE]
>  O elemento `FixedPortRange` funciona de maneira diferente, dependendo do elemento no qual ele reside. Quando o elemento `FixedPortRange` está no elemento `InternalEndpoint`, ele abre todas as portas no balanceador de carga dentro do intervalo dos atributos mínimo e máximo para todas as máquinas virtuais em que a função é executada. Quando o elemento `FixedPortRange` está no elemento `InstanceInputEndpoint`, ele abre apenas uma porta dentro do intervalo dos atributos mínimo e máximo em cada máquina virtual que executa a função.

O elemento `FixedPortRange` só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do elemento `FixedPortRange`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|min.|int|Necessário. A porta mínima no intervalo. Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1,7 ou superior).|  
|máx|string|Necessário. A porta máxima no intervalo. Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1,7 ou superior).|  

##  <a name="Certificates"></a>Certificado  
O elemento `Certificates` descreve a coleção de certificados para uma função Web. Esse elemento é o pai do elemento `Certificate`. Uma função pode ter qualquer número de certificados associados. Para obter mais informações sobre como usar o elemento Certificates, consulte [Modificar o arquivo de definição de serviço com um certificado](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a>Certificate  
O elemento `Certificate` descreve um certificado associado a uma função Web.

A tabela a seguir descreve os atributos do elemento `Certificate`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|string|Necessário. Um nome para esse certificado, que é usado para fazer referência a ele quando ele está associado a um elemento de `InputEndpoint` HTTPS.|  
|storeLocation|string|Necessário. O local do repositório de certificados em que esse certificado pode ser encontrado no computador local. Os valores possíveis são `CurrentUser` e `LocalMachine`.|  
|storeName|string|Necessário. O nome do repositório de certificados em que esse certificado reside no computador local. Os valores possíveis incluem os nomes de armazenamento internos `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`ou qualquer nome de repositório personalizado. Se um nome de repositório personalizado for especificado, o repositório será criado automaticamente.|  
|permissionLevel|string|Opcional. Especifica as permissões de acesso dadas aos processos de função. Se você quiser que apenas processos elevados possam acessar a chave privada, especifique `elevated` permissão. `limitedOrElevated` permissão permite que todos os processos de função acessem a chave privada. Os valores possíveis são `limitedOrElevated` ou `elevated`. O valor predefinido é `limitedOrElevated`.|  

##  <a name="Imports"></a>Importar  
O elemento `Imports` descreve uma coleção de módulos de importação para uma função Web que adiciona componentes ao sistema operacional convidado. Esse elemento é o pai do elemento `Import`. Esse elemento é opcional e uma função pode ter apenas um bloco Imports. 

O elemento `Imports` só está disponível usando o SDK do Azure versão 1,3 ou superior.

##  <a name="Import"></a>Importe  
O elemento `Import` especifica um módulo a ser adicionado ao sistema operacional convidado.

O elemento `Import` só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do elemento `Import`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|moduleName|string|Necessário. O nome do módulo a ser importado. Os módulos de importação válidos são:<br /><br /> -   RemoteAccess<br />-   RemoteForwarder<br />-Diagnóstico<br /><br /> Os módulos RemoteAccess e RemoteForwarder permitem que você configure sua instância de função para conexões de área de trabalho remota. Para obter mais informações, consulte [habilitar conexão de área de trabalho remota](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> O módulo diagnóstico permite coletar dados de diagnóstico para uma instância de função.|  

##  <a name="Runtime"></a>Appmodel  
O elemento `Runtime` descreve uma coleção de configurações de variável de ambiente para uma função Web que controla o ambiente de tempo de execução do processo de host do Azure. Esse elemento é o pai do elemento `Environment`. Esse elemento é opcional e uma função pode ter apenas um bloco de tempo de execução.

O elemento `Runtime` só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do elemento `Runtime`:  

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|executionContext|string|Opcional. Especifica o contexto no qual o processo de função é iniciado. O contexto padrão é `limited`.<br /><br /> -   `limited` – o processo é iniciado sem privilégios de administrador.<br />-   `elevated` – o processo é iniciado com privilégios de administrador.|  

##  <a name="Environment"></a>Ambiente  
O elemento `Environment` descreve uma coleção de configurações de variável de ambiente para uma função Web. Esse elemento é o pai do elemento `Variable`. Uma função pode ter qualquer número de variáveis de ambiente definidas.

##  <a name="Variable"></a>Ela  
O elemento `Variable` especifica uma variável de ambiente a ser definida na operação de convidado.

O elemento `Variable` só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do elemento `Variable`:  

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|string|Necessário. O nome da variável de ambiente a ser definida.|  
|valor|string|Opcional. O valor a ser definido para a variável de ambiente. Você deve incluir um atributo Value ou um elemento `RoleInstanceValue`.|  

##  <a name="RoleInstanceValue"></a> RoleInstanceValue  
O elemento `RoleInstanceValue` especifica o xPath do qual recuperar o valor da variável.

A tabela a seguir descreve os atributos do elemento `RoleInstanceValue`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|xpath|string|Opcional. Caminho do local das configurações de implantação da instância. Para obter mais informações, consulte [variáveis de configuração com XPath](cloud-services-role-config-xpath.md).<br /><br /> Você deve incluir um atributo Value ou um elemento `RoleInstanceValue`.|  

##  <a name="EntryPoint"></a> EntryPoint  
O elemento `EntryPoint` especifica o ponto de entrada para uma função. Esse elemento é o pai dos elementos de `NetFxEntryPoint`. Esses elementos permitem que você especifique um aplicativo que não seja o WaWorkerHost. exe padrão para atuar como o ponto de entrada de função.

O elemento `EntryPoint` só está disponível usando o SDK do Azure versão 1,5 ou superior.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint  
O elemento `NetFxEntryPoint` especifica o programa a ser executado para uma função.

> [!NOTE]
>  O elemento `NetFxEntryPoint` só está disponível usando o SDK do Azure versão 1,5 ou superior.

A tabela a seguir descreve os atributos do elemento `NetFxEntryPoint`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|assemblyName|string|Necessário. O caminho e o nome do arquivo do assembly que contém o ponto de entrada. O caminho é relativo à pasta **\\%ROLEROOT%\Approot** (não especifique **\\%ROLEROOT%\Approot** no `commandLine`, ele é presumido). **% ROLEROOT%** é uma variável de ambiente mantida pelo Azure e representa o local da pasta raiz para sua função. A pasta **\\%ROLEROOT%\Approot** representa a pasta do aplicativo para sua função.<br /><br /> Para as funções HWC, o caminho é sempre relativo à pasta **\\%ROLEROOT%\Approot\bin** .<br /><br /> Para funções Web do IIS e IIS Express completas, se o assembly não puder ser encontrado em relação à pasta **\\%ROLEROOT%\Approot** , o **\\%ROLEROOT%\Approot\bin** será pesquisado.<br /><br /> Esse comportamento de fallback para o IIS completo não é uma prática recomendada e talvez seja removido em versões futuras.|  
|targetFrameworkVersion|string|Necessário. A versão do .NET Framework na qual o assembly foi criado. Por exemplo, `targetFrameworkVersion="v4.0"`.|  

##  <a name="Sites"></a>Sites  
O elemento `Sites` descreve uma coleção de sites e aplicativos Web que são hospedados em uma função Web. Esse elemento é o pai do elemento `Site`. Se você não especificar um elemento `Sites`, sua função Web será hospedada como função Web herdada e você só poderá ter um site hospedado em sua função Web. Esse elemento é opcional e uma função pode ter apenas um bloco de sites.

O elemento `Sites` só está disponível usando o SDK do Azure versão 1,3 ou superior.

##  <a name="Site"></a>Locais  
O elemento `Site` especifica um site ou aplicativo Web que faz parte da função Web.

O elemento `Site` só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do elemento `Site`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|string|Necessário. Nome do site ou aplicativo.|  
|physicalDirectory|string|O local do diretório de conteúdo para a raiz do site. O local pode ser especificado como um caminho absoluto ou relativo ao local do. csdef.|  

##  <a name="VirtualApplication"></a>VirtualApplication  
O elemento `VirtualApplication` define um aplicativo no Serviços de Informações da Internet (IIS) 7 é um agrupamento de arquivos que fornece conteúdo ou fornece serviços sobre protocolos, como HTTP. Quando você cria um aplicativo no IIS 7, o caminho do aplicativo torna-se parte da URL do site.

O elemento `VirtualApplication` só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do elemento `VirtualApplication`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|string|Necessário. Especifica um nome para identificar o aplicativo virtual.|  
|physicalDirectory|string|Necessário. Especifica o caminho no computador de desenvolvimento que contém o aplicativo virtual. No emulador de computação, o IIS é configurado para recuperar conteúdo deste local. Ao implantar no Azure, o conteúdo do diretório físico é empacotado junto com o restante do serviço. Quando o pacote de serviço é implantado no Azure, o IIS é configurado com o local do conteúdo desempacotado.|  

##  <a name="VirtualDirectory"></a>VirtualDirectory  
O elemento `VirtualDirectory` especifica um nome de diretório (também conhecido como caminho) que você especifica no IIS e mapeia para um diretório físico em um servidor local ou remoto.

O elemento `VirtualDirectory` só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do elemento `VirtualDirectory`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|string|Necessário. Especifica um nome para identificar o diretório virtual.|  
|valor|physicalDirectory|Necessário. Especifica o caminho no computador de desenvolvimento que contém o conteúdo do site ou do diretório virtual. No emulador de computação, o IIS é configurado para recuperar conteúdo deste local. Ao implantar no Azure, o conteúdo do diretório físico é empacotado junto com o restante do serviço. Quando o pacote de serviço é implantado no Azure, o IIS é configurado com o local do conteúdo desempacotado.|  

##  <a name="Bindings"></a>Associações  
O elemento `Bindings` descreve uma coleção de associações para um site. É o elemento pai do elemento `Binding`. O elemento é necessário para cada elemento de `Site`. Para obter mais informações sobre como configurar pontos de extremidade, consulte [habilitar a comunicação para instâncias de função](cloud-services-enable-communication-role-instances.md).

O elemento `Bindings` só está disponível usando o SDK do Azure versão 1,3 ou superior.

##  <a name="Binding"></a>Vinculação  
O elemento `Binding` especifica as informações de configuração necessárias para que as solicitações se comuniquem com um site ou aplicativo Web.

O elemento `Binding` só está disponível usando o SDK do Azure versão 1,3 ou superior.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|string|Necessário. Especifica um nome para identificar a associação.|  
|endpointName|string|Necessário. Especifica o nome do ponto de extremidade ao qual associar.|  
|hostHeader|string|Opcional. Especifica um nome de host que permite hospedar vários sites, com nomes de host diferentes, em uma única combinação de endereço IP/número de porta.|  

##  <a name="Startup"></a>Inicialização  
O elemento `Startup` descreve uma coleção de tarefas que são executadas quando a função é iniciada. Esse elemento pode ser o pai do elemento `Variable`. Para obter mais informações sobre como usar as tarefas de inicialização de função, consulte [como configurar tarefas de inicialização](cloud-services-startup-tasks.md). Esse elemento é opcional e uma função pode ter apenas um bloco de inicialização.

A tabela a seguir descreve o atributo do elemento `Startup`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|prioridade|int|Apenas para utilização interna.|  

##  <a name="Task"></a>Agenda  
O elemento `Task` especifica a tarefa de inicialização que ocorre quando a função é iniciada. As tarefas de inicialização podem ser usadas para executar tarefas que preparam a função para executar esses componentes de software de instalação ou executar outros aplicativos. As tarefas são executadas na ordem em que aparecem dentro do bloco do elemento `Startup`.

O elemento `Task` só está disponível usando o SDK do Azure versão 1,3 ou superior.

A tabela a seguir descreve os atributos do elemento `Task`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|commandLine|string|Necessário. Um script, como um arquivo CMD, que contém os comandos a serem executados. O comando de inicialização e os arquivos em lotes devem ser salvos no formato ANSI. Os formatos de arquivo que definem um marcador de ordem de byte no início do arquivo não serão processados corretamente.|  
|executionContext|string|Especifica o contexto no qual o script é executado.<br /><br /> -   `limited` [padrão] – execute com os mesmos privilégios que a função que hospeda o processo.<br />-   `elevated` – execute com privilégios de administrador.|  
|taskType|string|Especifica o comportamento de execução do comando.<br /><br /> -   `simple` [padrão] – o sistema aguarda a tarefa ser encerrada antes de qualquer outra tarefa ser iniciada.<br />-   `background` – o sistema não aguarda a tarefa ser encerrada.<br />-   `foreground` – semelhante ao plano de fundo, exceto que a função não é reiniciada até que todas as tarefas de primeiro plano sejam encerradas.|  

##  <a name="Contents"></a>Índice  
O elemento `Contents` descreve a coleção de conteúdo para uma função Web. Esse elemento é o pai do elemento `Content`.

O elemento `Contents` só está disponível usando o SDK do Azure versão 1,5 ou superior.

##  <a name="Content"></a>Disputa  
O elemento `Content` define o local de origem do conteúdo a ser copiado para a máquina virtual do Azure e o caminho de destino para o qual ele é copiado.

O elemento `Content` só está disponível usando o SDK do Azure versão 1,5 ou superior.

A tabela a seguir descreve os atributos do elemento `Content`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|destination|string|Necessário. Local na máquina virtual do Azure na qual o conteúdo é colocado. Esse local é relativo à pasta **%ROLEROOT%\Approot**.|  

Esse elemento é o elemento pai do elemento `SourceDirectory`.

##  <a name="SourceDirectory"></a>SourceDirectory  
O elemento `SourceDirectory` define o diretório local do qual o conteúdo é copiado. Use este elemento para especificar o conteúdo local a ser copiado para a máquina virtual do Azure.

O elemento `SourceDirectory` só está disponível usando o SDK do Azure versão 1,5 ou superior.

A tabela a seguir descreve os atributos do elemento `SourceDirectory`.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|Caminho|string|Necessário. Caminho relativo ou absoluto de um diretório local cujo conteúdo será copiado para a máquina virtual do Azure. Há suporte para a expansão de variáveis de ambiente no caminho de diretório.|  
  
## <a name="see-also"></a>Veja também
[Esquema de definição do serviço de nuvem (clássico)](schema-csdef-file.md)




