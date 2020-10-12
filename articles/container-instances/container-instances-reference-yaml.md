---
title: Referência YAML para grupo de contentores
description: Referência para o ficheiro YAML suportado por Azure Container Instances para configurar um grupo de contentores
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: d0ec8d13eebba1c60f5a52f8c43bdd8b90eeb913
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87084765"
---
# <a name="yaml-reference-azure-container-instances"></a>Referência YAML: Instâncias de contentores Azure

Este artigo cobre a sintaxe e as propriedades do ficheiro YAML suportado por Azure Container Instances para configurar um [grupo de contentores](container-instances-container-groups.md). Utilize um ficheiro YAML para inserir a configuração do grupo no [contentor Az criar][az-container-create] comando no Azure CLI. 

Um ficheiro YAML é uma forma conveniente de configurar um grupo de contentores para implementações reprodutíveis. É uma alternativa concisa à utilização de um [modelo de Gestor de Recursos](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) ou dos SDKs de instâncias de contentores Azure para criar ou atualizar um grupo de contentores.

> [!NOTE]
> Esta referência aplica-se aos ficheiros YAML para a versão API de Casos de Substituição do Contentor Azure `2019-12-01` .

## <a name="schema"></a>Esquema 

Segue-se o esquema do ficheiro YAML, incluindo comentários para destacar as propriedades-chave. Para obter uma descrição das propriedades neste esquema, consulte a secção [de valores de propriedade.](#property-values)

```yml
name: string  # Name of the container group
apiVersion: '2019-12-01'
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
  sku: string # SKU for the container group
  encryptionProperties:
    vaultBaseUrl: string
    keyName: string
    keyVersion: string
  initContainers: # Array of init containers in the group
  - name: string
    properties:
      image: string
      command:
      - string
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      volumeMounts:
      - name: string
        mountPath: string
        readOnly: boolean
```

## <a name="property-values"></a>Valores patrimoniais

As tabelas que se seguem descrevem os valores necessários para definir no esquema.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.ContainerInstance/containerGroups objeto

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome do grupo de contentores. |
|  apiVersion | enum | Sim | 2018-10-01 |
|  localização | cadeia (de carateres) | No | A localização do recurso. |
|  etiquetas | objeto | Não | As etiquetas de recursos. |
|  identidade | objeto | Não | A identidade do grupo de contentores, se configurado. - [Objeto ContainerGroupIdentity](#containergroupidentity-object) |
|  propriedades | objeto | Sim | [Objeto ContainerGroupProperties](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>Objeto ContainerGroupIdentity

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  tipo | enum | Não | O tipo de identidade usada para o grupo de contentores. O tipo 'SystemAssigned, UserAssigned' inclui uma identidade implicitamente criada e um conjunto de identidades atribuídas pelo utilizador. O tipo 'Nenhum' removerá quaisquer identidades do grupo de contentores. - Sistemas Assintados, Dotados de Utilizador, Dossigned, UserAssigned, None |
|  nomeações para assediadas do utilizador | objeto | Não | A lista de identidades de utilizador associadas ao grupo de contentores. As referências-chave do dicionário de identidade do utilizador serão IDs de recursos do Gestor de Recursos Azure no formulário: '/subscrições/{subscriçãoD}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |




### <a name="containergroupproperties-object"></a>Objeto ContainerGroupProperties

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  containers | matriz | Sim | Os contentores dentro do grupo de contentores. - [Objeto de recipiente](#container-object) |
|  imagenRegistryCredentis | matriz | Não | As credenciais de registo de imagem através das quais o grupo de contentores é criado. - [Objeto imageRegistryCredential](#imageregistrycredential-object) |
|  reiniciarPolícia | enum | Não | Reinicie a política para todos os contentores dentro do grupo de contentores. - `Always` Reiniciar sempre- `OnFailure` Reiniciar em avarias- `Never` Nunca reinicie. - Sempre, OnFailure, Nunca |
|  ipAddress | objeto | Não | O tipo de endereço IP do grupo de contentores. - [Objeto IpAddress](#ipaddress-object) |
|  osTipos | enum | Sim | O tipo de sistema de funcionamento exigido pelos recipientes do grupo de contentores. - Janelas ou Linux |
|  volumes | matriz | Não | A lista de volumes que podem ser montados por contentores neste grupo de contentores. - [Objeto de volume](#volume-object) |
|  diagnósticos | objeto | Não | A informação de diagnóstico de um grupo de contentores. - [Objeto deDiagnostics ContainerGroup](#containergroupdiagnostics-object) |
|  redeProfile | objeto | Não | A informação do perfil da rede para um grupo de contentores. - [Objeto ContainerGroupNetworkProfile](#containergroupnetworkprofile-object) |
|  dnsConfig | objeto | Não | O DNS config informações para um grupo de contentores. - [Objeto dnsConfiguration](#dnsconfiguration-object) |
| sku | enum | Não | O SKU para um grupo de contentores - Standard ou Dedicado |
| encriptaçãoProperties | objeto | Não | As propriedades de encriptação de um grupo de contentores. - [Objeto de encriptaçãoProperties](#encryptionproperties-object) | 
| initContainers | matriz | Não | Os recipientes init para um grupo de contentores. - [Objeto InitContainerDefinition](#initcontainerdefinition-object) |




### <a name="container-object"></a>Objeto de recipiente

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome fornecido pelo utilizador da instância do recipiente. |
|  propriedades | objeto | Sim | As propriedades do caso do contentor. - [Objeto de contentores](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>Objeto imageRegistryCredential

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  servidor | string | Sim | O servidor de registo de imagem Docker sem um protocolo como "http" e "https". |
|  nome de utilizador | string | Sim | O nome de utilizador para o registo privado. |
|  palavra-passe | cadeia (de carateres) | No | A senha do registo privado. |




### <a name="ipaddress-object"></a>Objeto IpAddress

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  ports | matriz | Sim | A lista de portas expostas no grupo de contentores. - [Objeto de porta](#port-object) |
|  tipo | enum | Sim | Especifica se o IP está exposto à internet pública ou ao VNET privado. - Público ou Privado |
|  ip | cadeia (de carateres) | No | O IP exposto à internet pública. |
|  dnsNameLabel | cadeia (de carateres) | No | A etiqueta de nome Dns para o IP. |




### <a name="volume-object"></a>Objeto de volume

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome do volume. |
|  azureFile | objeto | Não | O volume do ficheiro Azure. - [Objeto AzureFileVolume](#azurefilevolume-object) |
|  emptyDir | objeto | Não | O volume de diretório vazio. |
|  segredo | objeto | Não | O volume secreto. |
|  gitRepo | objeto | Não | O volume de repo git. - [Objeto GitRepoVolume](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>Objeto deDiagnostics ContainerGroup

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objeto | Não | Informação de análise de registo de grupo de contentores. - [Objeto LogAnalytics](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>Objeto ContainerGroupNetworkProfile

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  ID | string | Sim | O identificador para um perfil de rede. |




### <a name="dnsconfiguration-object"></a>Objeto dnsConfiguration

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  nomeServadores | matriz | Sim | Os servidores DNS para o grupo de contentores. - corda |
|  searchDomains | cadeia (de carateres) | No | Os domínios de pesquisa de DNS para pesquisa de nome hospedeiro no grupo de contentores. |
|  opções | cadeia (de carateres) | No | As opções DNS para o grupo de contentores. |


### <a name="encryptionproperties-object"></a>Objeto de encriptaçãoProperties

| Nome  | Tipo  | Necessário  | Valor |
|  ---- | ---- | ---- | ---- |
| cofreBaseUrl  | string    | Sim   | A url base keyvault. |
| nome chave   | string    | Sim   | O nome chave da encriptação. |
| keyVersion    | string    | Sim   | A versão chave de encriptação. |

### <a name="initcontainerdefinition-object"></a>Objeto InitContainerDefinition

| Nome  | Tipo  | Necessário  | Valor |
|  ---- | ---- | ---- | ---- |
| name  | string |  Sim | O nome do recipiente init. |
| propriedades    | objeto    | Sim   | As propriedades do recipiente init. - [Objeto InitContainerPropertiesDefinition](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>Objeto de contentores

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  image | string | Sim | O nome da imagem usada para criar a instância do recipiente. |
|  command | matriz | Não | As ordens para executar dentro da instância do recipiente sob forma de executivo. - corda |
|  ports | matriz | Não | As portas expostas no caso do recipiente. - [Objeto ContainerPort](#containerport-object) |
|  ambienteVariables | matriz | Não | As variáveis ambientais a definir no caso do recipiente. - [Objeto ambientalmente variavel](#environmentvariable-object) |
|  resources | objeto | Sim | Os requisitos de recursos da instância do recipiente. - [Objeto de RecursosRequirements](#resourcerequirements-object) |
|  volumes | matriz | Não | O volume está disponível para a indicação do recipiente. - [Objeto VolumeMount](#volumemount-object) |
|  livenessProbe | objeto | Não | A sonda de vida. - [Objeto De ContainerProbe](#containerprobe-object) |
|  prontidãoProbe | objeto | Não | A sonda de preparação. - [Objeto De ContainerProbe](#containerprobe-object) |




### <a name="port-object"></a>Objeto de porta

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  protocolo | enum | Não | O protocolo associado ao porto. - TCP ou UDP |
|  porta | número inteiro | Sim | O número da porta. |




### <a name="azurefilevolume-object"></a>Objeto AzureFileVolume

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  partilhar Nome | string | Sim | O nome da partilha do Ficheiro Azure deve ser montado em volume. |
|  lerOn | boolean | Não | A bandeira indicando se o Ficheiro Azure partilhado como volume é apenas lido. |
|  storageAccountName | string | Sim | O nome da conta de armazenamento que contém a parte do Ficheiro Azure. |
|  armazenamentoSata | cadeia (de carateres) | No | A chave de acesso à conta de armazenamento utilizada para aceder à partilha do Ficheiro Azure. |




### <a name="gitrepovolume-object"></a>Objeto GitRepoVolume

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  diretório | cadeia (de carateres) | No | Nome do diretório alvo. Não deve conter ou começar com ".".  Se ''. for fornecido, o diretório de volume será o repositório de git.  Caso contrário, se especificado, o volume conterá o repositório de git no subdiretório com o nome próprio. |
|  repositório | string | Sim | URL do repositório |
|  revision | cadeia (de carateres) | No | Comprometa o haxixe para a revisão especificada. |



### <a name="loganalytics-object"></a>Objeto LogAnalytics

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | Sim | O id do espaço de trabalho para análise de log |
|  workspaceKey | string | Sim | A chave do espaço de trabalho para a análise de registos |
|  logType | enum | Não | O tipo de registo a ser utilizado. - ContainerInsights ou ContainerInstanceLogs |
|  do IdP | objeto | Não | Metadados para análise de registos. |


### <a name="initcontainerpropertiesdefinition-object"></a>Objeto InitContainerPropertiesDefinition

| Nome  | Tipo  | Necessário  | Valor |
|  ---- | ---- | ---- | ---- |
| image | cadeia (de carateres)    | No    | A imagem do recipiente init. |
| command   | matriz | Não    | O comando para executar dentro do recipiente init em forma de executivo. - corda |
| ambienteVariables | matriz  | Não |As variáveis ambientais a definir no recipiente init. - [Objeto ambientalmente variavel](#environmentvariable-object)
| volumes |matriz   | Não    | O volume está disponível para o recipiente init. - [Objeto VolumeMount](#volumemount-object)

### <a name="containerport-object"></a>Objeto ContainerPort

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  protocolo | enum | Não | O protocolo associado ao porto. - TCP ou UDP |
|  porta | número inteiro | Sim | O número da porta exposto dentro do grupo de contentores. |




### <a name="environmentvariable-object"></a>Objeto ambientalmente variavel

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome da variável ambiental. |
|  valor | cadeia (de carateres) | No | O valor da variável ambiental. |
|  secureValue | cadeia (de carateres) | No | O valor da variável ambiente seguro. |




### <a name="resourcerequirements-object"></a>Objeto de RecursosRequirements

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  pedidos | objeto | Sim | Os pedidos de recursos desta instância de contentor. - [Objeto RecursosRequests](#resourcerequests-object) |
|  limites | objeto | Não | Os limites de recursos deste caso do contentor. - [Objeto ResourceLimits](#resourcelimits-object) |




### <a name="volumemount-object"></a>Objeto VolumeMount

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome do volume montado. |
|  mountPath | string | Sim | O caminho dentro do recipiente onde o volume deve ser montado. Não deve conter cólon (:). |
|  lerOn | boolean | Não | A bandeira indicando se o suporte de volume é apenas de leitura. |




### <a name="containerprobe-object"></a>Objeto De ContainerProbe

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  executivo | objeto | Não | O comando de execução para sondar - [Objeto ContainerExec](#containerexec-object) |
|  httpGet | objeto | Não | As definições http Get para sondar - [Objeto ContainerHttpGet](#containerhttpget-object) |
|  delays iniciais | número inteiro | Não | Os segundos de atraso iniciais. |
|  periodSegundos | número inteiro | Não | Os segundos do período. |
|  falhaThreshold | número inteiro | Não | O limiar de falha. |
|  sucessoThreshold | número inteiro | Não | O limiar de sucesso. |
|  timeoutSegundos | número inteiro | Não | Os segundos de intervalo. |




### <a name="resourcerequests-object"></a>Objeto RecursosRequests

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  memóriaInGB | número | Sim | O pedido de memória em GB deste caso de recipiente. |
|  cpu | número | Sim | O pedido da CPU desta instância de contentor. |
|  gpu | objeto | Não | O pedido da GPU desta instância de contentor. - [Objeto gpuResource](#gpuresource-object) |




### <a name="resourcelimits-object"></a>Objeto ResourceLimits

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  memóriaInGB | número | Não | O limite de memória em GB deste caso de recipiente. |
|  cpu | número | Não | O limite do CPU deste caso de contentor. |
|  gpu | objeto | Não | O limite de GPU deste caso de contentor. - [Objeto gpuResource](#gpuresource-object) |




### <a name="containerexec-object"></a>Objeto ContainerExec

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  command | matriz | Não | As ordens para executar dentro do contentor. - corda |




### <a name="containerhttpget-object"></a>Objeto ContainerHttpGet

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  caminho | cadeia (de carateres) | No | O caminho para sondar. |
|  porta | número inteiro | Sim | O número da porta para sondar. |
|  regime | enum | Não | O esquema. - http ou https |




### <a name="gpuresource-object"></a>Objeto gpuResource

|  Nome | Tipo | Necessário | Valor |
|  ---- | ---- | ---- | ---- |
|  count | número inteiro | Sim | A contagem do recurso GPU. |
|  sku | enum | Sim | O SKU do recurso GPU. - K80, P100, V100 |


## <a name="next-steps"></a>Passos seguintes

Consulte o tutorial [Implementar um grupo multi-contentores utilizando um ficheiro YAML](container-instances-multi-container-yaml.md).

Veja exemplos de utilização de um ficheiro YAML para implantar grupos de contentores numa [rede virtual](container-instances-vnet.md) ou que [suportem um volume externo](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

