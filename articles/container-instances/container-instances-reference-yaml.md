---
title: Referência YAML para grupo de contentores
description: Referência para o ficheiro YAML suportado por Instâncias de Contentores Azure para configurar um grupo de contentores
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896575"
---
# <a name="yaml-reference-azure-container-instances"></a>Referência YAML: Instâncias de contentores azure

Este artigo abrange a sintaxe e as propriedades do ficheiro YAML suportado pela Azure Container Instances para configurar um grupo de [contentores](container-instances-container-groups.md). Utilize um ficheiro YAML para inserir a configuração do grupo para o [recipiente az criar][az-container-create] comando no ClI Azure. 

Um ficheiro YAML é uma forma conveniente de configurar um grupo de contentores para implementações reprodutíveis. É uma alternativa concisa à utilização de um [modelo de Gestor](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) de Recursos ou dos SDKs de Instâncias de Contentores Azure para criar ou atualizar um grupo de contentores.

> [!NOTE]
> Esta referência aplica-se aos ficheiros YAML para `2018-10-01`a versão API REST API do Portal das Instâncias de Contentores Do IE .

## <a name="schema"></a>Esquema 

Segue-se o esquema do ficheiro YAML, incluindo comentários para destacar as propriedades-chave. Para obter uma descrição das propriedades neste esquema, consulte a secção [de valores da Propriedade.](#property-values)

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

## <a name="property-values"></a>Valores patrimoniais

As tabelas que se seguem descrevem os valores necessários para definir no esquema.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.ContainerInstance/containerGroups

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  nome | string | Sim | O nome do grupo de contentores. |
|  apiVersion | enum | Sim | 2018-10-01 |
|  localização | string | Não | A localização dos recursos. |
|  etiquetas | objeto | Não | As etiquetas de recursos. |
|  identidade | objeto | Não | A identidade do grupo de contentores, se configurado. - [Objeto de identidade containerGroup](#ContainerGroupIdentity) |
|  propriedades | objeto | Sim | [Objeto ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Objeto de identidade containerGroup

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  tipo | enum | Não | O tipo de identidade utilizado para o grupo de contentores. O tipo 'SystemAssigned, UserAssigned' inclui uma identidade implicitamente criada e um conjunto de identidades atribuídas ao utilizador. O tipo 'Nenhum' removerá quaisquer identidades do grupo de contentores. - SistemaAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentidades | objeto | Não | A lista de identidades dos utilizadores associadas ao grupo de contentores. As principais referências do dicionário de identidade do utilizador serão iDs de recursos do Gestor de Recursos do Azure no formulário: '/subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Objeto ContainerGroupProperties

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  containers | array | Sim | Os contentores dentro do grupo de contentores. - [Objeto de contentor](#Container) |
|  imagensRegistryCredenciais | array | Não | As credenciais de registo de imagem pelas quais o grupo de contentores é criado. - [Objeto de Credencial de Registo de Imagem](#ImageRegistryCredential) |
|  reiniciarPolítica | enum | Não | Reinicie a política para todos os contentores dentro do grupo de contentores. - `Always`Reinicie `OnFailure` sempre- Reinicie a falha- `Never` Nunca reinicie. - Sempre, OnFailure, Nunca |
|  ipAddress | objeto | Não | O tipo de endereço IP do grupo de contentores. - [Objeto IpAddress](#IpAddress) |
|  osType | enum | Sim | O tipo de sistema operativo exigido pelos contentores do grupo de contentores. - Janelas ou Linux |
|  volumes | array | Não | A lista de volumes que podem ser montados por contentores neste grupo de contentores. - [Objeto de volume](#Volume) |
|  diagnósticos | objeto | Não | A informação de diagnóstico de um grupo de contentores. - [Objeto de ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  perfil de rede | objeto | Não | A informação do perfil da rede para um grupo de contentores. - [Objeto De Perfil de Rede de Contentores](#ContainerGroupNetworkProfile) |
|  dnsConfig | objeto | Não | As informações do DNS config para um grupo de contentores. - [Objeto de Configuração Dns](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Objeto de contentor

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  nome | string | Sim | O nome fornecido pelo utilizador da instância do recipiente. |
|  propriedades | objeto | Sim | As propriedades da instância do contentor. - [Objeto ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Objeto de Credencial de Registo de Imagem

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  servidor | string | Sim | O servidor de registo de imagem Docker sem um protocolo como "http" e "https". |
|  o nome de utilizador | string | Sim | O nome de utilizador do registo privado. |
|  palavra-passe | string | Não | A senha do registo privado. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Objeto IpAddress

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  ports | array | Sim | A lista de portas expostas no grupo de contentores. - [Objeto de porta](#Port) |
|  tipo | enum | Sim | Especifica se o IP está exposto à internet pública ou ao VNET privado. - Público ou Privado |
|  ip | string | Não | O IP exposto à internet pública. |
|  dnsNameLabel | string | Não | A etiqueta de nome Dns para o IP. |


<a id="Volume" />

### <a name="volume-object"></a>Objeto de volume

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  nome | string | Sim | O nome do volume. |
|  azureFile | objeto | Não | O volume do Ficheiro Azure. - [Objeto AzureFileVolume](#AzureFileVolume) |
|  emptyDir | objeto | Não | O volume vazio do diretório. |
|  segredo | objeto | Não | O volume secreto. |
|  gitRepo | objeto | Não | O volume git repo. - [Objeto GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Objeto de ContainerGroupDiagnostics

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objeto | Não | Informação de análise de registo de grupo de contentores. - [Objeto LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Objeto De Perfil de Rede de Contentores

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  ID | string | Sim | O identificador para um perfil de rede. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Objeto de Configuração Dns

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  nomeServidores | array | Sim | Os servidores DNS para o grupo de contentores. - corda |
|  pesquisarDomínios | string | Não | Os domínios de pesquisa dNS para pesquisa de nome de anfitrião no grupo de contentores. |
|  opções | string | Não | As opções de DNS para o grupo de contentores. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Objeto ContainerProperties

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  image | string | Sim | O nome da imagem usada para criar a instância do recipiente. |
|  command | array | Não | Os comandos para executar dentro da instância do contentor em forma de executivo. - corda |
|  ports | array | Não | As portas expostas na instância do contentor. - [Objeto containerPort](#ContainerPort) |
|  ambienteVariáveis | array | Não | As variáveis ambientais a definir na instância do recipiente. - [Objeto variável ambiente](#EnvironmentVariable) |
|  resources | objeto | Sim | Os requisitos de recursos da instância do contentor. - [Objeto de Requisitos de Recursos](#ResourceRequirements) |
|  volumeMounts | array | Não | O volume é montado à disposição da instância do recipiente. - [VolumeMonte objeto](#VolumeMount) |
|  livenessProbe | objeto | Não | A sonda de vivacidade. - [Objeto ContainerProbe](#ContainerProbe) |
|  prontidãoSonda | objeto | Não | A sonda de prontidão. - [Objeto ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Objeto de porta

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  protocolo | enum | Não | O protocolo associado à porta. - TCP ou UDP |
|  porta | número inteiro | Sim | O número da porta. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Objeto AzureFileVolume

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  shareName | string | Sim | O nome da parte do Ficheiro Azure deve ser montado como um volume. |
|  lerSó | boolean | Não | A bandeira indicando se o Ficheiro Azure partilhado montado como volume é apenas lido. |
|  storageAccountName | string | Sim | O nome da conta de armazenamento que contém a parte do Ficheiro Azure. |
|  armazenamentoAccountKey | string | Não | A chave de acesso à conta de armazenamento utilizada para aceder à partilha do Ficheiro Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Objeto GitRepoVolume

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  diretório | string | Não | Nome de diretório alvo. Não deve conter ou começar com ".".  Se for fornecido '.', o diretório de volume será o repositório git.  Caso contrário, se especificado, o volume conterá o repositório git no subdiretório com o nome dado. |
|  repositório | string | Sim | URL repositório |
|  revision | string | Não | Comprometa o hash para a revisão especificada. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Objeto LogAnalytics

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | Sim | O id espaço de trabalho para análise de log analytics |
|  espaço de trabalhoChave | string | Sim | A chave espaço de trabalho para análise de log analytics |
|  logType | enum | Não | O tipo de registo a utilizar. - ContainerInsights ou ContainerInstanceLogs |
|  do IdP | objeto | Não | Metadados para análise de registo. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Objeto containerPort

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  protocolo | enum | Não | O protocolo associado à porta. - TCP ou UDP |
|  porta | número inteiro | Sim | O número da porta exposto dentro do grupo de contentores. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Objeto variável ambiente

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  nome | string | Sim | O nome da variável ambiental. |
|  valor | string | Não | O valor da variável ambiental. |
|  secureValue | string | Não | O valor da variável ambiente seguro. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Objeto de Requisitos de Recursos

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  pedidos | objeto | Sim | Os pedidos de recursos desta instância de contentores. - [Objeto de Pedidos de Recursos](#ResourceRequests) |
|  limites | objeto | Não | Os limites de recursos desta instância do contentor. - [Objeto De RecursosLimites](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMonte objeto

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  nome | string | Sim | O nome do monte de volume. |
|  mountPath | string | Sim | O caminho dentro do recipiente onde o volume deve ser montado. Não deve conter cólon (:). |
|  lerSó | boolean | Não | A bandeira indicando se o suporte de volume é apenas de leitura. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Objeto ContainerProbe

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  executivo | objeto | Não | O comando de execução para sondar - [Objeto ContainerExec](#ContainerExec) |
|  httpGet | objeto | Não | O http Obter configurações para sondar - [Objeto ContainerHttpGet](#ContainerHttpGet) |
|  inicialDelaySeconds | número inteiro | Não | Os segundos de atraso inicial. |
|  períodoSeconds | número inteiro | Não | Os segundos do período. |
|  falhaLimiar | número inteiro | Não | O limiar de falha. |
|  sucessoThreshold | número inteiro | Não | O limiar de sucesso. |
|  timeoutSeconds | número inteiro | Não | Os segundos de tempo. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Objeto de Pedidos de Recursos

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  memóriaInGB | número | Sim | O pedido de memória em GB desta instância de contentor. |
|  cpu | número | Sim | O pedido da CPU deste caso de contentor. |
|  gpu | objeto | Não | O pedido da GPU deste caso de contentor. - [Objeto gpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Objeto De RecursosLimites

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  memóriaInGB | número | Não | O limite de memória em GB desta instância de contentor. |
|  cpu | número | Não | O limite de CPU desta instância de contentor. |
|  gpu | objeto | Não | O limite de GPU deste caso de contentor. - [Objeto gpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Objeto ContainerExec

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  command | array | Não | As ordens para executar dentro do recipiente. - corda |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Objeto contentorhttpGet

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  path | string | Não | O caminho para a sonda. |
|  porta | número inteiro | Sim | O número da porta para sondar. |
|  regime | enum | Não | O esquema. - http ou https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Objeto gpuResource

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  count | número inteiro | Sim | A contagem do recurso da GPU. |
|  sku | enum | Sim | O SKU do recurso GPU. - K80, P100, V100 |


## <a name="next-steps"></a>Passos seguintes

Consulte o tutorial [Implante um grupo multi-contentores utilizando um ficheiro YAML](container-instances-multi-container-yaml.md).

Consulte exemplos de utilização de um ficheiro YAML para implantar grupos de contentores numa [rede virtual](container-instances-vnet.md) ou que monte um [volume externo](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

