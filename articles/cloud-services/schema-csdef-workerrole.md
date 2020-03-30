---
title: Azure Cloud Services Def. WorkerRole Schema Microsoft Docs
description: O papel do trabalhador Azure é utilizado para o desenvolvimento generalizado e pode realizar processamento de fundo para um papel web. Conheça o esquema do papel do trabalhador Azure.
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
author: tgore03
ms.author: tagore
ms.openlocfilehash: 26225442c72fb209bb1ac4cd2bf4777fb39542fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534376"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Definição de serviços de nuvem azure Funrole Schema
O papel do trabalhador Azure é um papel que é útil para o desenvolvimento generalizado, e pode realizar processamento de fundo para um papel web.

A extensão predefinida para o ficheiro de definição de serviço é .csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Esquema de definição de serviço básico para um papel de trabalhador.
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

## <a name="schema-elements"></a>Elementos Schema
O ficheiro de definição de serviço inclui estes elementos, descritos em detalhe em secções posteriores neste tópico:

[Função operária](#WorkerRole)

[ConfiguraçõesDefinições](#ConfigurationSettings)

[Definição](#Setting)

[Recursos Locais](#LocalResources)

[Armazenamento Local](#LocalStorage)

[Pontos Finais](#Endpoints)

[InputEndpoint](#InputEndpoint)

[Ponto de final interno](#InternalEndpoint)

[InstânciaInputEndpoint](#InstanceInputEndpoint)

[Atribuição de Portos Públicos](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certificados](#Certificates)

[Certificado](#Certificate)

[Importações](#Imports)

[Importar](#Import)

[Runtime](#Runtime)

[Ambiente](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[Ponto de Entrada de Programas](#ProgramEntryPoint)

[Variável](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Arranque](#Startup)

[Tarefa](#Task)

[Conteúdos](#Contents)

[Conteúdo](#Content)

[Diretório de Origem](#SourceDirectory)

##  <a name="workerrole"></a><a name="WorkerRole"></a>Função operária
O `WorkerRole` elemento descreve uma função que é útil para o desenvolvimento generalizado, e pode realizar processamento de fundo para um papel web. Um serviço pode conter zero ou mais funções de trabalhador.

A tabela seguinte descreve `WorkerRole` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|nome|string|Necessário. O nome para o papel de trabalhador. O nome do papel deve ser único.|
|enableNativeCodeExecution|boolean|Opcional. O valor `true`predefinido é; a execução de código nativo e a confiança total são ativadas por defeito. Desative este `false` atributo para desativar a execução de código nativo para o papel de trabalhador, e use a confiança parcial azure em vez disso.|
|vmsize|string|Opcional. Descoloque este valor para alterar o tamanho da máquina virtual que está atribuída a esta função. O valor predefinido é `Small`. Para obter uma lista de possíveis tamanhos de máquinavirtual e seus atributos, consulte [tamanhos de Máquina Virtual para Serviços cloud](cloud-services-sizes-specs.md).|

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a>ConfiguraçõesDefinições
O `ConfigurationSettings` elemento descreve a recolha de configurações para uma função de trabalhador. Este elemento é o `Setting` pai do elemento.

##  <a name="setting"></a><a name="Setting"></a>Definição
O `Setting` elemento descreve um nome e um par de valor que especifica uma configuração de configuração para uma instância de uma função.

A tabela seguinte descreve `Setting` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|nome|string|Necessário. Um nome único para a configuração.|

As definições de configuração para uma função são pares de nome e valor que são declarados no ficheiro de definição de serviço e definidos no ficheiro de configuração do serviço.

##  <a name="localresources"></a><a name="LocalResources"></a>Recursos Locais
O `LocalResources` elemento descreve a recolha de recursos de armazenamento locais para um papel de trabalhador. Este elemento é o `LocalStorage` pai do elemento.

##  <a name="localstorage"></a><a name="LocalStorage"></a>Armazenamento Local
O `LocalStorage` elemento identifica um recurso de armazenamento local que fornece espaço de sistema de ficheiros para o serviço em tempo de execução. Um papel pode definir zero ou mais recursos de armazenamento locais.

> [!NOTE]
>  O `LocalStorage` elemento pode aparecer como `WorkerRole` uma criança do elemento para suportar a compatibilidade com versões anteriores do SDK Azure.

A tabela seguinte descreve `LocalStorage` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|nome|string|Necessário. Um nome único para a loja local.|
|limpezaOnRoleRecycle|boolean|Opcional. Indica se a loja local deve ser limpa quando a função for reiniciada. O valor `true`predefinido é .|
|tamanhoInMb|int|Opcional. A quantidade desejada de espaço de armazenamento para alocar para a loja local, em MB. Se não especificado, o espaço de armazenamento predefinido atribuído é de 100 MB. A quantidade mínima de espaço de armazenamento que pode ser atribuído é de 1 MB.<br /><br /> O tamanho máximo dos recursos locais depende do tamanho da máquina virtual. Para mais informações, consulte [tamanhos de Máquina Virtual para Serviços cloud](cloud-services-sizes-specs.md).|

O nome do diretório atribuído ao recurso de armazenamento local corresponde ao valor previsto para o atributo do nome.

##  <a name="endpoints"></a><a name="Endpoints"></a>Pontos finais
O `Endpoints` elemento descreve a recolha de pontos finais de entrada (externas), internas e de entrada por exemplo para um papel. Este elemento é o `InputEndpoint` `InternalEndpoint`progenitor `InstanceInputEndpoint` dos elementos e elementos.

A entrada e os pontos finais internos são atribuídos separadamente. Um serviço pode ter um total de 25 pontos finais de entrada, internos e de entrada por exemplo que podem ser atribuídos entre as 25 funções permitidas num serviço. Por exemplo, se tiver 5 funções pode alocar 5 pontos finais de entrada por função ou pode alocar 25 pontos finais de entrada a uma única função ou pode alocar 1 ponto final de entrada cada um a 25 funções.

> [!NOTE]
>  Cada função implementada requer uma instância por papel. O fornecimento predefinido para uma subscrição está limitado a 20 núcleos e, portanto, está limitado a 20 instâncias de uma função. Se a sua aplicação necessitar de mais instâncias do que as fornecidas pelo fornecimento por defeito, consulte [faturação, gestão de assinaturas e suporte](https://azure.microsoft.com/support/options/) de quotas para obter mais informações sobre o aumento da sua quota.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a>InputEndpoint
O `InputEndpoint` elemento descreve um ponto final externo para um papel de trabalhador.

Pode definir vários pontos finais que são uma combinação de pontos finais HTTP, HTTPS, UDP e TCP. Pode especificar qualquer número de porta que escolha para um ponto final de entrada, mas os números de porta especificados para cada função no serviço devem ser únicos. Por exemplo, se especificar que uma função utiliza a porta 80 para HTTP e a porta 443 para HTTPS, poderá então especificar que uma segunda função utiliza a porta 8080 para HTTP e a porta 8043 para HTTPS.

A tabela seguinte descreve `InputEndpoint` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|nome|string|Necessário. Um nome único para o ponto final externo.|
|protocolo|string|Necessário. O protocolo de transporte para o ponto final externo. Para um papel de `HTTP`trabalhador, os valores possíveis são, `HTTPS`ou `UDP` `TCP`.|
|porta|int|Necessário. A porta para o ponto final externo. Pode especificar qualquer número de porta que escolha, mas os números de porta especificados para cada função no serviço devem ser únicos.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (versão Azure SDK 1.7 ou superior).|
|certificado|string|Necessário para um ponto final HTTPS. O nome de um `Certificate` certificado definido por um elemento.|
|localPorto|int|Opcional. Especifica uma porta utilizada para ligações internas no ponto final. O `localPort` atributo mapeia a porta externa no ponto final para uma porta interna sobre um papel. Isto é útil em cenários em que uma função deve comunicar a um componente interno numa porta diferente daquela que é exposta externamente.<br /><br /> Se não especificado, `localPort` o valor é `port` o mesmo que o atributo. Detete `localPort` o valor de "*" para atribuir automaticamente uma porta não atribuída que seja detetável utilizando a API em tempo de funcionação.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (versão Azure SDK 1.7 ou superior).<br /><br /> O `localPort` atributo só está disponível utilizando a versão 1.3 do Azure SDK.|
|ignorarOEstatuto de Funções|boolean|Opcional. Quando o valor deste atributo `true`é definido para , o estado de um serviço é ignorado e o ponto final não será removido pelo equilibrista de carga. Definindo este `true` valor como útil para depurar casos ocupados de um serviço. O valor predefinido é `false`. **Nota:** Um ponto final ainda pode receber tráfego mesmo quando o papel não está em um estado pronto.|
|loadBalancerProbe|string|Opcional. O nome da sonda de equilíbrio de carga associada ao ponto final de entrada. Para mais informações, consulte [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md).|

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a>Ponto de final interno
O `InternalEndpoint` elemento descreve um ponto final interno para um papel de trabalhador. Um ponto final interno está disponível apenas para outras instâncias de função que ocorrem dentro do serviço; não está disponível para clientes fora do serviço. Um papel de trabalhador pode ter até cinco pontos finais internos HTTP, UDP ou TCP.

A tabela seguinte descreve `InternalEndpoint` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|nome|string|Necessário. Um nome único para o ponto final interno.|
|protocolo|string|Necessário. O protocolo de transporte para o ponto final interno. Os valores `UDP`possíveis `ANY`são, `HTTP` `TCP`ou .<br /><br /> Um valor `ANY` de especificações que qualquer protocolo, qualquer porta é permitido.|
|porta|int|Opcional. A porta utilizada para ligações internas equilibradas de carga no ponto final. Um ponto final equilibrado da Carga utiliza duas portas. A porta utilizada para o endereço IP público e a porta utilizada no endereço IP privado. Normalmente estes são definidos para o mesmo, mas você pode escolher usar portas diferentes.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (versão Azure SDK 1.7 ou superior).<br /><br /> O `Port` atributo só está disponível utilizando a versão 1.3 do Azure SDK.|

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a>InstânciaInputEndpoint
O `InstanceInputEndpoint` elemento descreve um ponto final de entrada de exemplo para um papel de trabalhador. Um ponto final de entrada de exemplo está associado a uma instância de função específica utilizando o encaminhamento da porta no equilibrante de carga. Cada ponto final de entrada de cada instância é mapeado para uma porta específica a partir de uma gama de portas possíveis. Este elemento é o `AllocatePublicPortFrom` pai do elemento.

O `InstanceInputEndpoint` elemento só está disponível utilizando a versão 1.7 do SDK Azure.

A tabela seguinte descreve `InstanceInputEndpoint` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|nome|string|Necessário. Um nome único para o ponto final.|
|localPorto|int|Necessário. Especifica a porta interna que todas as instâncias de funções irão ouvir para receber o tráfego de entrada encaminhado do equilibrador de carga. Os valores possíveis variam entre 1 e 65535, inclusive.|
|protocolo|string|Necessário. O protocolo de transporte para o ponto final interno. Os valores possíveis são `udp` ou `tcp`. Utilizar `tcp` para tráfego baseado em http/https.|

##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a>Atribuição de Portos Públicos
O `AllocatePublicPortFrom` elemento descreve a gama de portas públicas que pode ser usada por clientes externos para aceder a cada ponto final de entrada de cada instância. O número de porta pública (VIP) é atribuído a partir desta gama e atribuído a cada ponto final de instância individual durante a implantação e atualização do inquilino. Este elemento é o `FixedPortRange` pai do elemento.

O `AllocatePublicPortFrom` elemento só está disponível utilizando a versão 1.7 do SDK Azure.

##  <a name="fixedport"></a><a name="FixedPort"></a>FixedPort
O `FixedPort` elemento especifica a porta para o ponto final interno, que permite a carga de ligações equilibradas no ponto final.

O `FixedPort` elemento só está disponível utilizando a versão 1.3 do SDK Azure.

A tabela seguinte descreve `FixedPort` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|porta|int|Necessário. A porta para o ponto final interno. Isto tem o mesmo `FixedPortRange` efeito que a definição do min e do máximo para a mesma porta.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (versão Azure SDK 1.7 ou superior).|

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a>FixedPortRange
O `FixedPortRange` elemento especifica a gama de portas que são atribuídas ao ponto final interno ou ponto final de entrada por exemplo, e define a porta utilizada para ligações equilibradas de carga no ponto final.

> [!NOTE]
>  O `FixedPortRange` elemento funciona de forma diferente dependendo do elemento em que reside. Quando `FixedPortRange` o elemento `InternalEndpoint` está no elemento, abre todas as portas do equilibrador de carga dentro do alcance do min e atributos máximos para todas as máquinas virtuais em que a função é executado. Quando `FixedPortRange` o elemento `InstanceInputEndpoint` está no elemento, abre apenas uma porta dentro do alcance do min e atributos máximos em cada máquina virtual que executa a função.

O `FixedPortRange` elemento só está disponível utilizando a versão 1.3 do SDK Azure.

A tabela seguinte descreve `FixedPortRange` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|min|int|Necessário. A porta mínima na gama. Os valores possíveis variam entre 1 e 65535, inclusive (versão Azure SDK 1.7 ou superior).|
|máximo|string|Necessário. A porta máxima da gama. Os valores possíveis variam entre 1 e 65535, inclusive (versão Azure SDK 1.7 ou superior).|

##  <a name="certificates"></a><a name="Certificates"></a>Certificados
O `Certificates` elemento descreve a cobrança de certificados para um papel de trabalhador. Este elemento é o `Certificate` pai do elemento. Um papel pode ter qualquer número de certificados associados. Para obter mais informações sobre a utilização do elemento certificado, consulte [Modificar o ficheiro Definição de Serviço com um certificado](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="certificate"></a><a name="Certificate"></a>Certificado
O `Certificate` elemento descreve um certificado que está associado a um papel de trabalhador.

A tabela seguinte descreve `Certificate` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|nome|string|Necessário. Um nome para este certificado, que é usado para se `InputEndpoint` referir a ele quando está associado a um elemento HTTPS.|
|lojaLocalização|string|Necessário. A localização do certificado onde este certificado pode ser encontrado na máquina local. Os valores possíveis são `CurrentUser` e. `LocalMachine`|
|nome da loja|string|Necessário. O nome do certificado onde este certificado reside na máquina local. Os valores possíveis incluem `CA`os `Trust` `Disallowed`nomes das lojas `My` `Root`incorporadas, , , , `TrustedPeople`, `TrustedPublisher`ou `AuthRoot` `AddressBook`qualquer nome de loja personalizado. Se for especificado um nome de loja personalizado, a loja é automaticamente criada.|
|permissãoN|string|Opcional. Especifica as permissões de acesso dadas aos processos de função. Se quiser apenas processos elevados para poder aceder `elevated` à chave privada, especifique a permissão. `limitedOrElevated`a permissão permite que todos os processos de função acedam à chave privada. Os valores possíveis são `limitedOrElevated` ou `elevated`. O valor predefinido é `limitedOrElevated`.|

##  <a name="imports"></a><a name="Imports"></a>Importações
O `Imports` elemento descreve uma coleção de módulos de importação para uma função de trabalhador que adiciona componentes ao sistema operativo convidado. Este elemento é o `Import` pai do elemento. Este elemento é opcional e um papel pode ter apenas um bloco de tempo de execução.

O `Imports` elemento só está disponível utilizando a versão 1.3 do SDK Azure.

##  <a name="import"></a><a name="Import"></a>Importação
O `Import` elemento especifica um módulo para adicionar ao sistema operativo do hóspede.

O `Import` elemento só está disponível utilizando a versão 1.3 do SDK Azure.

A tabela seguinte descreve `Import` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|móduloNome|string|Necessário. O nome do módulo para importar. Os módulos de importação válidos são:<br /><br /> - Acesso Remoto<br />- RemoteForwarder<br />- Diagnósticos<br /><br /> Os módulos RemoteAccess e RemoteForwarder permitem configurar a sua função para ligações remotas de ambiente de trabalho. Para mais informações, consulte a [Ligação remota](cloud-services-role-enable-remote-desktop-new-portal.md)do ambiente de trabalho .<br /><br /> O módulo de Diagnóstico permite-lhe recolher dados de diagnóstico para uma instância de função|

##  <a name="runtime"></a><a name="Runtime"></a>Tempo de execução
O `Runtime` elemento descreve uma coleção de configurações variáveis ambientais para uma função de trabalhador que controla o ambiente de tempo de execução do processo de acolhimento do Azure. Este elemento é o `Environment` pai do elemento. Este elemento é opcional e um papel pode ter apenas um bloco de tempo de execução.

O `Runtime` elemento só está disponível utilizando a versão 1.3 do SDK Azure.

A tabela seguinte descreve `Runtime` os atributos do elemento:

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|execuçãoContexto|string|Opcional. Especifica o contexto em que o Processo de Role é lançado. O contexto `limited`padrão é .<br /><br /> -   `limited`– O processo é lançado sem privilégios de Administrador.<br />-   `elevated`– O processo é lançado com privilégios administrador.|

##  <a name="environment"></a><a name="Environment"></a>Ambiente
O `Environment` elemento descreve uma coleção de configurações variáveis ambientais para um papel de trabalhador. Este elemento é o `Variable` pai do elemento. Um papel pode ter qualquer número de variáveis ambientais definidas.

##  <a name="variable"></a><a name="Variable"></a>Variável
O `Variable` elemento especifica uma variável ambiental a definir no funcionamento do hóspede.

O `Variable` elemento só está disponível utilizando a versão 1.3 do SDK Azure.

A tabela seguinte descreve `Variable` os atributos do elemento:

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|nome|string|Necessário. O nome da variável ambiental para definir.|
|valor|string|Opcional. O valor a definir para a variável ambiental. Deve incluir um atributo de `RoleInstanceValue` valor ou um elemento.|

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a>RoleInstanceValue
O `RoleInstanceValue` elemento especifica o xPath a partir do qual recuperar o valor da variável.

A tabela seguinte descreve `RoleInstanceValue` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|xpath|string|Opcional. Localização do caminho das definições de implantação, por exemplo. Para mais informações, consulte variáveis de [configuração com XPath](cloud-services-role-config-xpath.md).<br /><br /> Deve incluir um atributo de `RoleInstanceValue` valor ou um elemento.|

##  <a name="entrypoint"></a><a name="EntryPoint"></a>Ponto de entrada
O `EntryPoint` elemento especifica o ponto de entrada para uma função. Este elemento é o `NetFxEntryPoint` pai dos elementos. Estes elementos permitem especificar uma aplicação diferente do WaWorkerHost.exe padrão para agir como o ponto de entrada da função.

O `EntryPoint` elemento só está disponível utilizando a versão 1.5 do SDK Azure.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a>NetFxEntryPoint
O `NetFxEntryPoint` elemento especifica o programa para concorrer a um papel.

> [!NOTE]
>  O `NetFxEntryPoint` elemento só está disponível utilizando a versão 1.5 do SDK Azure.

A tabela seguinte descreve `NetFxEntryPoint` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|nome de montagem|string|Necessário. O caminho e o nome do ficheiro do conjunto que contém o ponto de entrada. O caminho é relativo à pasta `commandLine` ** \\%ROLEROOT%\Approot** (não especifique ** \\%ROLEROOT%\Approot** in, assume-se). **%ROLEROOT%** é uma variável ambiental mantida pelo Azure e representa a localização da pasta de raiz para o seu papel. A pasta ** \\%ROLEROOT%\Approot** representa a pasta de aplicação para o seu papel.|
|targetFrameworkVersion|string|Necessário. A versão do quadro .NET em que foi construída a montagem. Por exemplo, `targetFrameworkVersion="v4.0"`.|

##  <a name="programentrypoint"></a><a name="ProgramEntryPoint"></a>Ponto de Entrada de Programas
O `ProgramEntryPoint` elemento especifica o programa para concorrer a um papel. O `ProgramEntryPoint` elemento permite especificar um ponto de entrada do programa que não se baseia num conjunto .NET.

> [!NOTE]
>  O `ProgramEntryPoint` elemento só está disponível utilizando a versão 1.5 do SDK Azure.

A tabela seguinte descreve `ProgramEntryPoint` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|linha de comando|string|Necessário. O caminho, o nome do ficheiro e quaisquer argumentos da linha de comando do programa para executar. O caminho é relativo à pasta **%ROLEROOT%\Approot** (não especifique **%ROLEROOT%\Approot** in commandLine, assume-se). **%ROLEROOT%** é uma variável ambiental mantida pelo Azure e representa a localização da pasta de raiz para o seu papel. A pasta **%ROLEROOT%\Approot** representa a pasta de aplicação para o seu papel.<br /><br /> Se o programa terminar, o papel é reciclado, de modo que geralmente definir o programa para continuar a executar, em vez de ser um programa que apenas começa e executa uma tarefa finita.|
|setReadyOnProcessStart|boolean|Necessário. Especifica se a instância de funções aguarda que o programa da linha de comando sinalize que está iniciado. Este valor deve `true` ser definido neste momento. A definição `false` do valor está reservada para uso futuro.|

##  <a name="startup"></a><a name="Startup"></a>Arranque
O `Startup` elemento descreve uma coleção de tarefas que funcionam quando o papel é iniciado. Este elemento pode ser `Variable` o progenitor do elemento. Para obter mais informações sobre a utilização das tarefas de arranque de funções, consulte [como configurar tarefas de arranque.](cloud-services-startup-tasks.md) Este elemento é opcional e um papel pode ter apenas um bloco de arranque.

A tabela seguinte descreve o `Startup` atributo do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|prioridade|int|Apenas para utilização interna.|

##  <a name="task"></a><a name="Task"></a>Tarefa
O `Task` elemento especifica a tarefa de arranque que ocorre quando o papel começa. As tarefas de arranque podem ser usadas para executar tarefas que preparem a função de executar tais componentes de software ou executar outras aplicações. As tarefas executam na ordem `Startup` em que aparecem dentro do bloco de elementos.

O `Task` elemento só está disponível utilizando a versão 1.3 do SDK Azure.

A tabela seguinte descreve `Task` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|linha de comando|string|Necessário. Um guião, como um ficheiro CMD, contendo os comandos a executar. Os ficheiros de comando de arranque e de lote devem ser guardados no formato ANSI. Os formatos de ficheiro que fixam um marcador de encomenda byte no início do ficheiro não serão processados corretamente.|
|execuçãoContexto|string|Especifica o contexto em que o script é executado.<br /><br /> -   `limited`[Padrão] – Corra com os mesmos privilégios que o papel que acolhe o processo.<br />-   `elevated`– Correr com privilégios de administrador.|
|tarefaTipo|string|Especifica o comportamento de execução do comando.<br /><br /> -   `simple`[Padrão] – O sistema aguarda que a tarefa saia antes de serem lançadas quaisquer outras tarefas.<br />-   `background`– O sistema não aguarda a saída da tarefa.<br />-   `foreground`– Semelhante ao pano de fundo, exceto que o papel não é reiniciado até que todas as tarefas de primeiro plano saiam.|

##  <a name="contents"></a><a name="Contents"></a>Conteúdos
O `Contents` elemento descreve a recolha de conteúdos para um papel de trabalhador. Este elemento é o `Content` pai do elemento.

O `Contents` elemento só está disponível utilizando a versão 1.5 do SDK Azure.

##  <a name="content"></a><a name="Content"></a>Conteúdo
O `Content` elemento define a localização de origem do conteúdo a copiar para a máquina virtual Azure e o caminho de destino para o qual é copiado.

O `Content` elemento só está disponível utilizando a versão 1.5 do SDK Azure.

A tabela seguinte descreve `Content` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|destino|string|Necessário. Localização na máquina virtual Azure para a qual o conteúdo é colocado. Esta localização é relativa à pasta **%ROLEROOT%\Approot**.|

Este elemento é o `SourceDirectory` elemento-mãe do elemento.

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a>Diretório de Origem
O `SourceDirectory` elemento define o diretório local a partir do qual o conteúdo é copiado. Utilize este elemento para especificar o conteúdo local para copiar para a máquina virtual Azure.

O `SourceDirectory` elemento só está disponível utilizando a versão 1.5 do SDK Azure.

A tabela seguinte descreve `SourceDirectory` os atributos do elemento.

| Atributo | Tipo | Descrição |
| --------- | ---- | ----------- |
|path|string|Necessário. Percurso relativo ou absoluto de um diretório local cujo conteúdo será copiado para a máquina virtual Azure. A expansão das variáveis ambientais no caminho do diretório é apoiada.|

## <a name="see-also"></a>Veja também
[Serviço de Nuvem (clássico) Definição Schema](schema-csdef-file.md)



