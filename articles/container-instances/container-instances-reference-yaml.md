---
title: Referência de YAML de instâncias de contêiner do Azure
description: Referência do arquivo YAML com suporte das instâncias de contêiner do Azure para configurar um grupo de contêineres
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/12/2019
ms.author: danlep
ms.openlocfilehash: 2e6be18371cf3ff96d1ce91d4dde26ff1f14021b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179907"
---
# <a name="yaml-reference-azure-container-instances"></a>Referência de YAML: Azure Container Instances

Este artigo aborda a sintaxe e as propriedades do arquivo YAML com suporte das instâncias de contêiner do Azure para configurar um [grupo de contêineres](container-instances-container-groups.md). Use um arquivo YAML para inserir a configuração do grupo para o comando [AZ container Create][az-container-create] no CLI do Azure. 

Um arquivo YAML é uma maneira conveniente de configurar um grupo de contêineres para implantações reproduzíveis. É uma alternativa concisa usar um modelo do [Resource Manager](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) ou os SDKs das instâncias de contêiner do Azure para criar ou atualizar um grupo de contêineres.

> [!NOTE]
> Essa referência se aplica a arquivos YAML para a versão `2018-10-01`da API REST de instâncias de contêiner do Azure.

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
      ports: # Exposed ports on the instance
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

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  name | Cadeia de caracteres | Sim | O nome do grupo de contêineres. |
|  apiVersion | Enum | Sim | 2018-10-01 |
|  location | Cadeia de caracteres | Não | O local do recurso. |
|  tags | object | Não | As marcas de recurso. |
|  identity | object | Não | A identidade do grupo de contêineres, se configurada. - [Objeto ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | object | Sim | [Objeto ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Objeto ContainerGroupIdentity

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  type | Enum | Não | O tipo de identidade usado para o grupo de contêineres. O tipo ' SystemAssigned, userassigned ' inclui uma identidade criada implicitamente e um conjunto de identidades atribuídas pelo usuário. O tipo ' none ' removerá todas as identidades do grupo de contêineres. -SystemAssigned, userassigned, SystemAssigned, userassigned, None |
|  userAssignedIdentities | object | Não | A lista de identidades de usuário associada ao grupo de contêineres. As referências de chave de dicionário de identidade do usuário serão Azure Resource Manager IDs de recurso no formato: '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName }'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Objeto ContainerGroupProperties

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  containers | array | Sim | Os contêineres dentro do grupo de contêineres. - [Objeto de contêiner](#Container) |
|  imageRegistryCredentials | array | Não | As credenciais de registro de imagem pelas quais o grupo de contêineres é criado. - [Objeto ImageRegistryCredential](#ImageRegistryCredential) |
|  restartPolicy | Enum | Não | Reinicie a política para todos os contêineres dentro do grupo de contêineres. - `Always`Sempre reiniciar – `OnFailure` reiniciar em caso de `Never` falha-nunca reinicie. -Sempre, OnFailure, nunca |
|  IP | object | Não | O tipo de endereço IP do grupo de contêineres. - [Objeto IpAddress](#IpAddress) |
|  osType | Enum | Sim | O tipo de sistema operacional exigido pelos contêineres no grupo de contêineres. -Windows ou Linux |
|  volumes | array | Não | A lista de volumes que podem ser montados por contêineres neste grupo de contêineres. - [Objeto de volume](#Volume) |
|  diagnósticos | object | Não | As informações de diagnóstico para um grupo de contêineres. - [Objeto ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  networkProfile | object | Não | As informações de perfil de rede para um grupo de contêineres. - [Objeto ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | Não | As informações de configuração de DNS para um grupo de contêineres. - [Objeto DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Objeto de contêiner

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  name | Cadeia de caracteres | Sim | O nome fornecido pelo usuário da instância de contêiner. |
|  properties | object | Sim | As propriedades da instância de contêiner. - [Objeto containerproperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Objeto ImageRegistryCredential

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  server | Cadeia de caracteres | Sim | O servidor do registro de imagem do Docker sem um protocolo como "http" e "https". |
|  username | Cadeia de caracteres | Sim | O nome de usuário para o registro privado. |
|  password | Cadeia de caracteres | Não | A senha para o registro particular. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Objeto IpAddress

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  ports | array | Sim | A lista de portas expostas no grupo de contêineres. - [Objeto Port](#Port) |
|  type | Enum | Sim | Especifica se o IP é exposto à Internet pública ou à VNET privada. -Público ou privado |
|  IP | Cadeia de caracteres | Não | O IP exposto à Internet pública. |
|  dnsNameLabel | Cadeia de caracteres | Não | O rótulo de nome DNS para o IP. |


<a id="Volume" />

### <a name="volume-object"></a>Objeto de volume

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  name | Cadeia de caracteres | Sim | O nome do volume. |
|  Azurefile | object | Não | O volume do arquivo do Azure. - [Objeto AzureFileVolume](#AzureFileVolume) |
|  emptyDir | object | Não | O volume de diretório vazio. |
|  secret | object | Não | O volume secreto. |
|  gitRepo | object | Não | O volume do repositório git. - [Objeto GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Objeto ContainerGroupDiagnostics

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Não | Informações do log Analytics do grupo de contêineres. - [Objeto LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Objeto ContainerGroupNetworkProfile

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  id | Cadeia de caracteres | Sim | O identificador de um perfil de rede. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Objeto DnsConfiguration

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  nameServers | array | Sim | Os servidores DNS para o grupo de contêineres. -Cadeia de caracteres |
|  searchDomains | Cadeia de caracteres | Não | Os domínios de pesquisa de DNS para pesquisa de nome de host no grupo de contêineres. |
|  options | Cadeia de caracteres | Não | As opções de DNS para o grupo de contêineres. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Objeto containerproperties

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  image | Cadeia de caracteres | Sim | O nome da imagem usada para criar a instância de contêiner. |
|  Comando | array | Não | Os comandos a serem executados na instância de contêiner no formato EXEC. -Cadeia de caracteres |
|  ports | array | Não | As portas expostas na instância do contêiner. - [Objeto ContainerPort](#ContainerPort) |
|  environmentVariables | array | Não | As variáveis de ambiente a serem definidas na instância de contêiner. - [Objeto EnvironmentVariable](#EnvironmentVariable) |
|  recursos | object | Sim | Os requisitos de recurso da instância de contêiner. - [Objeto ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | array | Não | As montagens de volume disponíveis para a instância de contêiner. - [Objeto VolumeMount](#VolumeMount) |
|  livenessProbe | object | Não | A investigação de tempo de vida. - [Objeto ContainerProbe](#ContainerProbe) |
|  readinessProbe | object | Não | A investigação de preparação. - [Objeto ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Objeto Port

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | Enum | Não | O protocolo associado à porta. -TCP ou UDP |
|  port | integer | Sim | O número da porta. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Objeto AzureFileVolume

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  shareName | Cadeia de caracteres | Sim | O nome do compartilhamento de arquivos do Azure a ser montado como um volume. |
|  readOnly | boolean | Não | O sinalizador que indica se o arquivo compartilhado do Azure montado como um volume é somente leitura. |
|  storageAccountName | Cadeia de caracteres | Sim | O nome da conta de armazenamento que contém o compartilhamento de arquivos do Azure. |
|  storageAccountKey | Cadeia de caracteres | Não | A chave de acesso da conta de armazenamento usada para acessar o compartilhamento de arquivos do Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Objeto GitRepoVolume

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  diretório | Cadeia de caracteres | Não | Nome do diretório de destino. Não deve conter ou começar com '.. '.  Se '. ' for fornecido, o diretório de volume será o repositório git.  Caso contrário, se especificado, o volume conterá o repositório git no subdiretório com o nome fornecido. |
|  repository | Cadeia de caracteres | Sim | URL do repositório |
|  Revisão | Cadeia de caracteres | Não | Confirme o hash para a revisão especificada. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Objeto LogAnalytics

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  workspaceId | Cadeia de caracteres | Sim | A ID do espaço de trabalho para o log Analytics |
|  workspaceKey | Cadeia de caracteres | Sim | A chave do espaço de trabalho para o log Analytics |
|  logType | Enum | Não | O tipo de log a ser usado. -ContainerInsights ou ContainerInstanceLogs |
|  do IdP | object | Não | Metadados para o log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Objeto ContainerPort

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | Enum | Não | O protocolo associado à porta. -TCP ou UDP |
|  port | integer | Sim | O número da porta exposta no grupo de contêineres. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Objeto EnvironmentVariable

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  name | Cadeia de caracteres | Sim | O nome da variável de ambiente. |
|  value | Cadeia de caracteres | Não | O valor da variável de ambiente. |
|  Parâmetro securevalue | Cadeia de caracteres | Não | O valor da variável de ambiente segura. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Objeto ResourceRequirements

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  pedidos | object | Sim | As solicitações de recurso desta instância de contêiner. - [Objeto ResourceRequests](#ResourceRequests) |
|  Aos | object | Não | Os limites de recurso desta instância de contêiner. - [Objeto ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Objeto VolumeMount

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  name | Cadeia de caracteres | Sim | O nome da montagem do volume. |
|  mountPath | Cadeia de caracteres | Sim | O caminho dentro do contêiner em que o volume deve ser montado. Não deve conter dois-pontos (:). |
|  readOnly | boolean | Não | O sinalizador que indica se a montagem do volume é somente leitura. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Objeto ContainerProbe

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  exec | object | Não | O comando de execução para o [objeto](#ContainerExec) Probe-ContainerExec |
|  httpGet | object | Não | As configurações http get para o objeto Probe- [ContainerHttpGet](#ContainerHttpGet) |
|  initialDelaySeconds | integer | Não | Os segundos de atraso iniciais. |
|  periodSeconds | integer | Não | O período de segundos. |
|  Limite | integer | Não | O limite de falha. |
|  successThreshold | integer | Não | O limite de êxito. |
|  timeoutSeconds | integer | Não | O tempo limite em segundos. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Objeto ResourceRequests

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Sim | A solicitação de memória em GB dessa instância de contêiner. |
|  cpu | number | Sim | A solicitação de CPU desta instância de contêiner. |
|  gpu | object | Não | A solicitação de GPU desta instância de contêiner. - [Objeto GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Objeto ResourceLimits

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Não | O limite de memória em GB dessa instância de contêiner. |
|  cpu | number | Não | O limite de CPU desta instância de contêiner. |
|  gpu | object | Não | O limite de GPU dessa instância de contêiner. - [Objeto GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Objeto ContainerExec

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  Comando | array | Não | Os comandos a serem executados no contêiner. -Cadeia de caracteres |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Objeto ContainerHttpGet

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  path | Cadeia de caracteres | Não | O caminho para investigação. |
|  port | integer | Sim | O número da porta a ser investigada. |
|  esquema | Enum | Não | O esquema. -http ou https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Objeto GpuResource

|  Name | Type | Requerido | Value |
|  ---- | ---- | ---- | ---- |
|  count | integer | Sim | A contagem do recurso de GPU. |
|  sku | Enum | Sim | A SKU do recurso de GPU. -K80, P100, V100 |


## <a name="next-steps"></a>Passos seguintes

Consulte o tutorial [implantar um grupo de vários contêineres usando um arquivo YAML](container-instances-multi-container-yaml.md).

Consulte exemplos de como usar um arquivo YAML para implantar grupos de contêineres em uma [rede virtual](container-instances-vnet.md) ou que [montam um volume externo](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

