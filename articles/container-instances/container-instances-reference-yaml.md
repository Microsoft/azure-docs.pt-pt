---
title: Referência de YAML para grupo de contêineres
description: Referência do arquivo YAML com suporte das instâncias de contêiner do Azure para configurar um grupo de contêineres
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896575"
---
# <a name="yaml-reference-azure-container-instances"></a>Referência de YAML: instâncias de contêiner do Azure

Este artigo aborda a sintaxe e as propriedades do arquivo YAML com suporte das instâncias de contêiner do Azure para configurar um [grupo de contêineres](container-instances-container-groups.md). Use um arquivo YAML para inserir a configuração do grupo para o comando [AZ container Create][az-container-create] no CLI do Azure. 

Um arquivo YAML é uma maneira conveniente de configurar um grupo de contêineres para implantações reproduzíveis. É uma alternativa concisa usar um modelo do [Resource Manager](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) ou os SDKs das instâncias de contêiner do Azure para criar ou atualizar um grupo de contêineres.

> [!NOTE]
> Essa referência se aplica a arquivos YAML para as instâncias de contêiner do Azure versão da API REST `2018-10-01`.

## <a name="schema"></a>Esquema 

O esquema para o arquivo YAML segue, incluindo comentários para realçar as propriedades da chave. Para obter uma descrição das propriedades nesse esquema, consulte a seção [valores de propriedade](#property-values) .

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>Valores de propriedade

As tabelas a seguir descrevem os valores que você precisa definir no esquema.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Objeto Microsoft. ContainerInstance/containerGroups

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  nome | string | Sim | O nome do grupo de contêineres. |
|  apiVersion | Enum | Sim | 2018-10-01 |
|  localização | string | Não | O local do recurso. |
|  etiquetas | objeto | Não | As marcas de recurso. |
|  identidade | objeto | Não | A identidade do grupo de contêineres, se configurada. - [objeto ContainerGroupIdentity](#ContainerGroupIdentity) |
|  propriedades | objeto | Sim | [Objeto ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Objeto ContainerGroupIdentity

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  tipo | Enum | Não | O tipo de identidade usado para o grupo de contêineres. O tipo ' SystemAssigned, userassigned ' inclui uma identidade criada implicitamente e um conjunto de identidades atribuídas pelo usuário. O tipo ' none ' removerá todas as identidades do grupo de contêineres. -SystemAssigned, userassigned, SystemAssigned, userassigned, None |
|  userAssignedIdentities | objeto | Não | A lista de identidades de usuário associada ao grupo de contêineres. As referências de chave de dicionário de identidade do usuário serão Azure Resource Manager IDs de recurso no formato: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} '. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Objeto ContainerGroupProperties

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  containers | array | Sim | Os contêineres dentro do grupo de contêineres.[objeto contêiner](#Container)  -  |
|  imageRegistryCredentials | array | Não | As credenciais de registro de imagem pelas quais o grupo de contêineres é criado. - [objeto ImageRegistryCredential](#ImageRegistryCredential) |
|  restartPolicy | Enum | Não | Reinicie a política para todos os contêineres dentro do grupo de contêineres. - `Always` sempre reiniciar-`OnFailure` reiniciar em caso de falha-`Never` nunca reiniciar. -Sempre, OnFailure, nunca |
|  IP | objeto | Não | O tipo de endereço IP do grupo de contêineres. - [objeto IPAddress](#IpAddress) |
|  osType | Enum | Sim | O tipo de sistema operacional exigido pelos contêineres no grupo de contêineres. -Windows ou Linux |
|  volumes | array | Não | A lista de volumes que podem ser montados por contêineres neste grupo de contêineres. - [objeto de volume](#Volume) |
|  diagnósticos | objeto | Não | As informações de diagnóstico para um grupo de contêineres. - [objeto ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  networkProfile | objeto | Não | As informações de perfil de rede para um grupo de contêineres. - [objeto ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | objeto | Não | As informações de configuração de DNS para um grupo de contêineres. - [objeto DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Objeto de contêiner

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  nome | string | Sim | O nome fornecido pelo usuário da instância de contêiner. |
|  propriedades | objeto | Sim | As propriedades da instância de contêiner.[objeto contêineres](#ContainerProperties)  -  |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Objeto ImageRegistryCredential

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  servidor | string | Sim | O servidor do registro de imagem do Docker sem um protocolo como "http" e "https". |
|  o nome de utilizador | string | Sim | O nome de usuário para o registro privado. |
|  palavra-passe | string | Não | A senha para o registro particular. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Objeto IpAddress

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  ports | array | Sim | A lista de portas expostas no grupo de contêineres.[objeto de porta](#Port)  -  |
|  tipo | Enum | Sim | Especifica se o IP é exposto à Internet pública ou à VNET privada. -Público ou privado |
|  IP | string | Não | O IP exposto à Internet pública. |
|  dnsNameLabel | string | Não | O rótulo de nome DNS para o IP. |


<a id="Volume" />

### <a name="volume-object"></a>Objeto de volume

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  nome | string | Sim | O nome do volume. |
|  azurefile | objeto | Não | O volume do arquivo do Azure. - [objeto AzureFileVolume](#AzureFileVolume) |
|  emptyDir | objeto | Não | O volume de diretório vazio. |
|  segredo | objeto | Não | O volume secreto. |
|  gitRepo | objeto | Não | O volume do repositório git. - [objeto GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Objeto ContainerGroupDiagnostics

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objeto | Não | Informações do log Analytics do grupo de contêineres. - [objeto LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Objeto ContainerGroupNetworkProfile

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  ID | string | Sim | O identificador de um perfil de rede. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Objeto DnsConfiguration

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  nameServers | array | Sim | Os servidores DNS para o grupo de contêineres. -Cadeia de caracteres |
|  searchDomains | string | Não | Os domínios de pesquisa de DNS para pesquisa de nome de host no grupo de contêineres. |
|  options | string | Não | As opções de DNS para o grupo de contêineres. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Objeto containerproperties

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  imagem | string | Sim | O nome da imagem usada para criar a instância de contêiner. |
|  command | array | Não | Os comandos a serem executados na instância de contêiner no formato EXEC. -Cadeia de caracteres |
|  ports | array | Não | As portas expostas na instância do contêiner. - [objeto ContainerPort](#ContainerPort) |
|  environmentVariables | array | Não | As variáveis de ambiente a serem definidas na instância de contêiner. - [objeto EnvironmentVariable](#EnvironmentVariable) |
|  recursos | objeto | Sim | Os requisitos de recurso da instância de contêiner. - [objeto ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | array | Não | As montagens de volume disponíveis para a instância de contêiner. - [objeto VolumeMount](#VolumeMount) |
|  livenessProbe | objeto | Não | A investigação de tempo de vida. - [objeto ContainerProbe](#ContainerProbe) |
|  readinessProbe | objeto | Não | A investigação de preparação. - [objeto ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Objeto Port

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  protocol | Enum | Não | O protocolo associado à porta. -TCP ou UDP |
|  porta | número inteiro | Sim | O número da porta. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Objeto AzureFileVolume

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  shareName | string | Sim | O nome do compartilhamento de arquivos do Azure a ser montado como um volume. |
|  readOnly | boolean | Não | O sinalizador que indica se o arquivo compartilhado do Azure montado como um volume é somente leitura. |
|  storageAccountName | string | Sim | O nome da conta de armazenamento que contém o compartilhamento de arquivos do Azure. |
|  storageAccountKey | string | Não | A chave de acesso da conta de armazenamento usada para acessar o compartilhamento de arquivos do Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Objeto GitRepoVolume

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  diretório | string | Não | Nome do diretório de destino. Não deve conter ou começar com '.. '.  Se '. ' for fornecido, o diretório de volume será o repositório git.  Caso contrário, se especificado, o volume conterá o repositório git no subdiretório com o nome fornecido. |
|  repository | string | Sim | URL do repositório |
|  revision | string | Não | Confirme o hash para a revisão especificada. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Objeto LogAnalytics

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | Sim | A ID do espaço de trabalho para o log Analytics |
|  workspaceKey | string | Sim | A chave do espaço de trabalho para o log Analytics |
|  logType | Enum | Não | O tipo de log a ser usado. -ContainerInsights ou ContainerInstanceLogs |
|  do IdP | objeto | Não | Metadados para o log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Objeto ContainerPort

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  protocol | Enum | Não | O protocolo associado à porta. -TCP ou UDP |
|  porta | número inteiro | Sim | O número da porta exposta no grupo de contêineres. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Objeto EnvironmentVariable

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  nome | string | Sim | O nome da variável de ambiente. |
|  valor | string | Não | O valor da variável de ambiente. |
|  Parâmetro securevalue | string | Não | O valor da variável de ambiente segura. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Objeto ResourceRequirements

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  pedidos | objeto | Sim | As solicitações de recurso desta instância de contêiner. - [objeto ResourceRequests](#ResourceRequests) |
|  limites | objeto | Não | Os limites de recurso desta instância de contêiner. - [objeto ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Objeto VolumeMount

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  nome | string | Sim | O nome da montagem do volume. |
|  mountPath | string | Sim | O caminho dentro do contêiner em que o volume deve ser montado. Não deve conter dois-pontos (:). |
|  readOnly | boolean | Não | O sinalizador que indica se a montagem do volume é somente leitura. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Objeto ContainerProbe

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  exec | objeto | Não | O comando de execução para o [objeto](#ContainerExec) Probe-ContainerExec |
|  httpGet | objeto | Não | As configurações http get para o objeto Probe- [ContainerHttpGet](#ContainerHttpGet) |
|  initialDelaySeconds | número inteiro | Não | Os segundos de atraso iniciais. |
|  periodSeconds | número inteiro | Não | O período de segundos. |
|  Limite | número inteiro | Não | O limite de falha. |
|  successThreshold | número inteiro | Não | O limite de êxito. |
|  timeoutSeconds | número inteiro | Não | O tempo limite em segundos. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Objeto ResourceRequests

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | número | Sim | A solicitação de memória em GB dessa instância de contêiner. |
|  cpu | número | Sim | A solicitação de CPU desta instância de contêiner. |
|  gpu | objeto | Não | A solicitação de GPU desta instância de contêiner. - [objeto GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Objeto ResourceLimits

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | número | Não | O limite de memória em GB dessa instância de contêiner. |
|  cpu | número | Não | O limite de CPU desta instância de contêiner. |
|  gpu | objeto | Não | O limite de GPU dessa instância de contêiner. - [objeto GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Objeto ContainerExec

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  command | array | Não | Os comandos a serem executados no contêiner. -Cadeia de caracteres |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Objeto ContainerHttpGet

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  Caminho | string | Não | O caminho para investigação. |
|  porta | número inteiro | Sim | O número da porta a ser investigada. |
|  esquema | Enum | Não | O esquema. -http ou https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Objeto GpuResource

|  Nome | Tipo | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  count | número inteiro | Sim | A contagem do recurso de GPU. |
|  SKU | Enum | Sim | A SKU do recurso de GPU. -K80, P100, V100 |


## <a name="next-steps"></a>Passos seguintes

Consulte o tutorial [implantar um grupo de vários contêineres usando um arquivo YAML](container-instances-multi-container-yaml.md).

Consulte exemplos de como usar um arquivo YAML para implantar grupos de contêineres em uma [rede virtual](container-instances-vnet.md) ou que [montam um volume externo](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

