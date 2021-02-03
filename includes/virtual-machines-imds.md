---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 0b04ebd9672990738d77bc5ae09d7f7fae4ffb9d
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500468"
---
# <a name="azure-instance-metadata-service-imds"></a>Serviço de metadados de caso Azure (IMDs)

O Azure Instance Metadata Service (IMDS) fornece informações sobre casos de máquinas virtuais atualmente em execução. Pode usá-lo para gerir e configurar as suas máquinas virtuais.
Esta informação inclui o SKU, armazenamento, configurações de rede e eventos de manutenção futuros. Para obter uma lista completa dos dados disponíveis, consulte o Resumo das [Categorias endpoint](#endpoint-categories).

O IMDS está disponível para casos de funcionamento de máquinas virtuais (VMs) e instâncias de conjunto de escala de máquina virtual. Todos os pontos finais suportam VMs criados e geridos através do [Azure Resource Manager](/rest/api/resources/). Apenas a categoria Attested e a parte de rede da categoria Exemplo suportam VMs criados utilizando o modelo de implementação clássico. O ponto final atestado fá-lo apenas em um grau limitado.

O IMDS é uma API REST que está disponível num endereço IP bem conhecido e não roteado `169.254.169.254` (). Só é possível aceder a partir do VM. A comunicação entre o VM e o IMDS nunca sai do hospedeiro.
Tenha os seus clientes HTTP a contornar os proxies web dentro do VM ao consultar o IMDS, e trate `169.254.169.254` o mesmo que [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="usage"></a>Utilização

### <a name="access-azure-instance-metadata-service"></a>Acesso Azure Instance Metadados

Para aceder ao IMDS, crie um VM a partir do [Azure Resource Manager](/rest/api/resources/) ou do [portal Azure](https://portal.azure.com), e use as seguintes amostras.
Para mais exemplos, consulte [amostras de metadados de exemplo de exemplos](https://github.com/microsoft/azureimds).

Aqui está o código de amostra para recuperar todos os metadados, por exemplo. Para aceder a uma fonte de dados específica, consulte [as Categorias Endpoint](#endpoint-categories) para uma visão geral de todas as funcionalidades disponíveis.

**Pedir**

> [!IMPORTANT]
> Este exemplo contorna os proxies. **Tem de** contornar os proxies ao consultar o IMDS. Consulte [proxies](#proxies) para obter informações adicionais.

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

---

**Response**

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo a seguir é bastante impressa para a legibilidade.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Segurança e autenticação

O Serviço de Metadados de Exemplo só está acessível dentro de uma caixa de máquina virtual em execução num endereço IP não roteável. Os VMs limitam-se a interagir com metadados/funcionalidade que se referem a si mesmos. A API é apenas HTTP e nunca sai do hospedeiro.

A fim de garantir que os pedidos se destinam diretamente ao IMDS e evitar a reorientação não intencional ou indesejada dos pedidos, solicita:
- **Deve** conter o cabeçalho `Metadata: true`
- **Não** deve conter um `X-Forwarded-For` cabeçalho

Qualquer pedido que não satisfaça **ambos estes** requisitos será rejeitado pelo serviço.

> [!IMPORTANT]
> O IMDS **não** é um canal para dados sensíveis. A API é não autenticada e aberta a todos os processos no VM. As informações expostas através deste serviço devem ser consideradas como informações partilhadas a todas as aplicações que estão dentro do VM.

## <a name="proxies"></a>Proxies

O IMDS **não** se destina a ser utilizado por detrás de um representante e fazê-lo não é suportado. A maioria dos clientes HTTP fornece uma opção para desativar os proxies nos seus pedidos, e esta funcionalidade deve ser utilizada ao comunicar com o IMDS. Consulte a documentação do seu cliente para mais detalhes.

> [!IMPORTANT]
> Mesmo que não saiba de nenhuma configuração de procuração no seu ambiente, **ainda deve anular quaisquer definições padrão de procuração de clientes**. As configurações proxy podem ser automaticamente descobertas, e não conseguir contornar tais configurações expõe-no a riscos de interrupção caso a configuração da máquina seja alterada no futuro.

## <a name="rate-limiting"></a>Rate limiting (Limitação de taxa)

Em geral, os pedidos ao IMDS limitam-se a 5 pedidos por segundo. Os pedidos que excedam este limiar serão rejeitados com 429 respostas. Os pedidos à categoria [Identidade Gerida](#managed-identity) estão limitados a 20 pedidos por segundo e 5 pedidos simultâneos.

## <a name="http-verbs"></a>Verbos HTTP

Os seguintes verbos HTTP são atualmente suportados:

| Verbo | Descrição |
|------|-------------|
| `GET` | Recuperar o recurso solicitado

## <a name="parameters"></a>Parâmetros

Os pontos finais podem suportar parâmetros necessários e/ou opcionais. Consulte [Schema](#schema) e a documentação para o ponto final específico em questão para obter mais detalhes.

### <a name="query-parameters"></a>Parâmetros de consulta

Os pontos finais do IMDS suportam parâmetros de cadeia de consulta HTTP. Por exemplo: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

Especifica os parâmetros:

| Name | Valor |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

Os pedidos com nomes de parâmetros de consulta duplicados serão rejeitados.

### <a name="route-parameters"></a>Parâmetros de rota

Para alguns pontos finais que devolvem bolhas json maiores, apoiamos parâmetros de rota appending para o ponto final do pedido para filtrar até um subconjunto da resposta: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
Os parâmetros correspondem aos índices/teclas que seriam usados para descer o objeto json se interagisse com uma representação analisada.

Por exemplo, `/metatadata/instance` devolve o objeto json:
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

Se quisermos filtrar a resposta apenas para a propriedade computacional, enviaremos o pedido: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Da mesma forma, se quisermos filtrar para uma propriedade aninhada ou elemento de matriz específico, mantemos as teclas de appending: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
filtraria o primeiro elemento da `Network.interface` propriedade e devolveria:

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> Quando se filtra para um nó de folha, `format=json` não funciona. Para estas `format=text` consultas, é necessário especificar explicitamente, uma vez que o formato padrão é json.

## <a name="schema"></a>Esquema

### <a name="data-format"></a>Formato de dados

Por predefinição, o IMDS devolve dados em formato JSON `Content-Type: application/json` (). No entanto, os pontos finais que suportam a filtragem de resposta (ver [Parâmetros de Rota)](#route-parameters)também suportam o formato `text` .

Para aceder a um formato de resposta não padrão, especifique o formato solicitado como um parâmetro de cadeia de consulta no pedido. Por exemplo:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

Nas respostas json, todos os primitivos serão do tipo `string` , e os valores em falta ou inaplicáveis estão sempre incluídos, mas serão definidos para uma corda vazia.

### <a name="versioning"></a>Controlo de versões

O IMDS é versão e especificar a versão API no pedido HTTP é obrigatório. A única exceção a este requisito é o ponto final das [versões,](#versions) que pode ser usado para recuperar dinamicamente as versões API disponíveis.

À medida que as versões mais recentes são adicionadas, as versões mais antigas ainda podem ser acedidas para compatibilidade se os seus scripts tiverem dependências de formatos de dados específicos.

Quando não especifica uma versão, obtém-se um erro com uma lista das versões mais recentes suportadas:
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>Versões API suportadas
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> A versão 2020-10-01 está neste momento a ser lançada e pode ainda não estar disponível em todas as regiões.

### <a name="swagger"></a>Swagger

Uma definição completa de Swagger para IMDS está disponível em: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Disponibilidade regional

O serviço está **geralmente disponível** em todas as Nuvens Azure.

## <a name="root-endpoint"></a>Ponto final de raiz

O ponto final da raiz é `http://169.254.169.254/metadata` .

## <a name="endpoint-categories"></a>Categorias de ponto final

A API IMDS contém várias categorias de pontos finais que representam diferentes fontes de dados, cada uma das quais contém um ou mais pontos finais. Consulte cada categoria para mais detalhes.

| Raiz de categoria | Descrição | Versão introduzida |
|---------------|-------------|--------------------|
| `/metadata/attested` | Ver [Dados Attestados](#attested-data) | 2018-10-01
| `/metadata/identity` | Ver [Identidade Gerida via IMDS](#managed-identity) | 2018-02-01
| `/metadata/instance` | Ver [Metadados de Exemplo](#instance-metadata) | 2017-04-02
| `/metadata/scheduledevents` | Ver [Eventos Agendados via IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | [Versões](#versions) | N/D

## <a name="versions"></a>Versões

> [!NOTE]
> Esta funcionalidade foi lançada juntamente com a versão 2020-10-01, que se encontra atualmente a ser lançada e que ainda não está disponível em todas as regiões.

### <a name="list-api-versions"></a>Lista de versões API

Devolve o conjunto de versões API suportadas.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Parâmetros

Nenhum (este ponto final não é versado).

#### <a name="response"></a>Resposta

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>Metadados de exemplo

### <a name="get-vm-metadata"></a>Obtenha metadados VM

Expõe os metadados importantes para a instância VM, incluindo computação, rede e armazenamento. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Parâmetros

| Name | Obrigatório/Opcional | Descrição |
|------|-------------------|-------------|
| `api-version` | Obrigatório | A versão usada para servir o pedido.
| `format` | Opcional* | O formato `json` (ou `text` ) da resposta. *Nota: Pode ser necessário quando se utilizam parâmetros de pedido

Este ponto final suporta a filtragem de resposta através de [parâmetros de rota](#route-parameters).

#### <a name="response"></a>Resposta

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Desagregação do esquema:

**Computação**

| Dados | Descrição | Versão introduzida |
|------|-------------|--------------------|
| `azEnvironment` | Ambiente azul onde o VM está em execução | 2018-10-01
| `customData` | Esta funcionalidade encontra-se atualmente desativada. Atualizaremos esta documentação quando estiver disponível | 2019-02-01
| `isHostCompatibilityLayerVm` | Identifica se o VM funciona na Camada de Compatibilidade do Anfitrião | 2020-06-01
| `licenseType` | Tipo de licença para [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Isto só está presente para VMs ativados por AHB | 2020-09-01
| `location` | Região de Azure o VM está em execução | 2017-04-02
| `name` | Nome do VM | 2017-04-02
| `offer` | Oferecer informações para a imagem VM e só está presente para imagens implementadas a partir da galeria de imagens Azure | 2017-04-02
| `osProfile.adminUsername` | Especifica o nome da conta administradora | 2020-07-15
| `osProfile.computerName` | Especifica o nome do computador | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Especifica se a autenticação de palavra-passe é desativada. Isto só está presente para os VMs linux | 2020-10-01
| `osType` | Linux ou Windows | 2017-04-02
| `placementGroupId` | Grupo de [colocação](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) do seu conjunto de escala de máquina virtual | 2017-08-01
| `plan` | [Plano](/rest/api/compute/virtualmachines/createorupdate#plan) contendo nome, produto e editor para um VM se for uma Imagem de Mercado Azure | 2018-04-02
| `platformUpdateDomain` |  [Atualização](../articles/virtual-machines/manage-availability.md) do domínio em que o VM está a funcionar | 2017-04-02
| `platformFaultDomain` | [Domínio de avaria](../articles/virtual-machines/manage-availability.md) em que o VM está a funcionar | 2017-04-02
| `provider` | Provedor do VM | 2018-10-01
| `publicKeys` | [Coleção de Chaves Públicas](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) atribuídas ao VM e caminhos | 2018-04-02
| `publisher` | Editor da imagem VM | 2017-04-02
| `resourceGroupName` | [Grupo de recursos](../articles/azure-resource-manager/management/overview.md) para a sua Máquina Virtual | 2017-08-01
| `resourceId` | O ID [totalmente qualificado](/rest/api/resources/resources/getbyid) do recurso | 2019-03-11
| `sku` | SKU específico para a imagem VM | 2017-04-02
| `securityProfile.secureBootEnabled` | Identifica se a bota segura UEFI está ativada no VM | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Identifica se o Módulo de Plataforma Fidedigna Virtual (TPM) está ativado no VM | 2020-06-01
| `storageProfile` | Ver Perfil de Armazenamento abaixo | 2019-06-01
| `subscriptionId` | Assinatura Azure para a Máquina Virtual | 2017-08-01
| `tags` | [Etiquetas](../articles/azure-resource-manager/management/tag-resources.md) para a sua Máquina Virtual  | 2017-08-01
| `tagsList` | Tags formatadas como uma matriz JSON para uma análise programática mais fácil  | 2019-06-04
| `version` | Versão da imagem VM | 2017-04-02
| `vmId` | [Identificador único](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para o VM | 2017-04-02
| `vmScaleSetName` | [Conjunto de escala de máquina virtual Nome](../articles/virtual-machine-scale-sets/overview.md) do seu conjunto de escala de máquina virtual | 2017-12-01
| `vmSize` | [Tamanho VM](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Zona de disponibilidade](../articles/availability-zones/az-overview.md) da sua máquina virtual | 2017-12-01

**Perfil de armazenamento**

O perfil de armazenamento de um VM é dividido em três categorias: referência de imagem, disco de SO e discos de dados.

O objeto de referência de imagem contém as seguintes informações sobre a imagem do SO:

| Dados | Descrição |
|------|-------------|
| `id` | ID do Recurso
| `offer` | Oferta da plataforma ou imagem de mercado
| `publisher` | Editor de imagem
| `sku` | Sku de imagem
| `version` | Versão da plataforma ou imagem do mercado

O objeto do disco OS contém as seguintes informações sobre o disco de oss utilizado pelo VM:

| Dados | Descrição |
|------|-------------|
| `caching` | Requisitos de caching
| `createOption` | Informação sobre como o VM foi criado
| `diffDiskSettings` | Definições de disco efémero
| `diskSizeGB` | Tamanho do disco em GB
| `image`   | Disco rígido virtual de imagem de utilizador de origem
| `lun`     | Número de unidade lógica do disco
| `managedDisk` | Parâmetros de disco geridos
| `name`    | Nome do disco
| `vhd`     | Disco rígido virtual
| `writeAcceleratorEnabled` | Se escrever Ou não OAccelerador está ativado no disco

A matriz de discos de dados contém uma lista de discos de dados anexados ao VM. Cada objeto de disco de dados contém as seguintes informações:

Dados | Descrição |
-----|-------------|
| `caching` | Requisitos de caching
| `createOption` | Informação sobre como o VM foi criado
| `diffDiskSettings` | Definições de disco efémero
| `diskSizeGB` | Tamanho do disco em GB
| `encryptionSettings` | Definições de encriptação para o disco
| `image` | Disco rígido virtual de imagem de utilizador de origem
| `managedDisk` | Parâmetros de disco geridos
| `name` | Nome do disco
| `osType` | Tipo de SO incluído no disco
| `vhd` | Disco rígido virtual
| `writeAcceleratorEnabled` | Se escrever Ou não OAccelerador está ativado no disco

**Rede**

| Dados | Descrição | Versão introduzida |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Endereço IPv4 local do VM | 2017-04-02
| `ipv4.publicIpAddress` | Endereço IPv4 público do VM | 2017-04-02
| `subnet.address` | Endereço de sub-rede do VM | 2017-04-02
| `subnet.prefix` | Prefixo de sub-rede, exemplo 24 | 2017-04-02
| `ipv6.ipAddress` | Endereço IPv6 local do VM | 2017-04-02
| `macAddress` | Endereço de mac VM | 2017-04-02

**Etiquetas VM**

As tags VM estão incluídas no ponto final da API de instância/compute/tags.
As etiquetas podem ter sido aplicadas ao seu Azure VM para organizar logicamente uma taxonomia. As etiquetas atribuídas a um VM podem ser recuperadas utilizando o pedido abaixo.

O `tags` campo é uma corda com as etiquetas delimitadas por pontos-e-vírguis. Esta saída pode ser um problema se os e-tím9is forem utilizados nas próprias etiquetas. Se um analisador for escrito para extrair programáticamente as etiquetas, deve confiar no `tagsList` campo. O `tagsList` campo é uma matriz JSON sem delimiters, e consequentemente, mais fácil de analisar.


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Amostra 1: VM de rastreio em execução em Azure

Como prestador de serviços, poderá ser necessário rastrear o número de VMs que executam o seu software ou ter agentes que precisam de rastrear a singularidade do VM. Para conseguir obter um ID único para um VM, utilize o `vmId` campo do Serviço de Metadados de Exemplo.

**Pedir**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"| ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Response**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>Amostra 2: Colocação de diferentes réplicas de dados

Para certos cenários, a colocação de diferentes réplicas de dados é de primordial importância. Por exemplo, a colocação de [réplicas HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou a colocação de contentores através de um [orquestrador](https://kubernetes.io/docs/user-guide/node-selection/) pode exigir que você saiba `platformFaultDomain` o e o `platformUpdateDomain` VM está em execução.
Também pode utilizar [Zonas de Disponibilidade](../articles/availability-zones/az-overview.md) para as instâncias para tomar estas decisões.
Pode consultar estes dados diretamente através do IMDS.

**Pedir**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
0
```

#### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>Amostra 3: Obter mais informações sobre o VM durante o caso de apoio

Como prestador de serviços, poderá receber uma chamada de apoio onde gostaria de saber mais informações sobre o VM. Pedir ao cliente que partilhe os metadados do cálculo pode fornecer informações básicas para que o profissional de suporte saiba sobre o tipo de VM no Azure.

**Pedir**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Response**

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo a seguir é bastante impressa para a legibilidade.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>Amostra 4: Obtenha o Ambiente Azure onde o VM está em execução

Azure tem várias nuvens soberanas como [o Governo de Azure.](https://azure.microsoft.com/overview/clouds/government/) Às vezes é preciso o Ambiente Azure para tomar algumas decisões de tempo de execução. A amostra que se segue mostra como pode alcançar este comportamento.

**Pedir**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Response**

```
AzurePublicCloud
```

A nuvem e os valores do ambiente Azure estão listados aqui.

| Cloud | Ambiente azul |
|-------|-------------------|
| [Todas as regiões globais de Azure geralmente disponíveis](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureusGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-5-retrieve-network-information"></a>Amostra 5: Recuperar informações sobre a rede

**Pedir**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Response**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-6-retrieve-public-ip-address"></a>Amostra 6: Recuperar endereço IP público

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Dados atestados

### <a name="get-attested-data"></a>Obter dados atestados

O IMDS ajuda a fornecer garantias de que os dados fornecidos vêm do Azure. A Microsoft assina parte desta informação, para que possa confirmar que uma imagem no Azure Marketplace é a que está a correr no Azure.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Parâmetros

| Name | Obrigatório/Opcional | Descrição |
|------|-------------------|-------------|
| `api-version` | Obrigatório | A versão usada para servir o pedido.
| `nonce` | Opcional | Uma corda de 10 dígitos que serve como um nó criptográfico. Se não for fornecido qualquer valor, o IMDS utiliza a atual placa de tempo UTC.

#### <a name="response"></a>Resposta

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> Devido ao mecanismo de cache do IMDS, pode ser devolvido um valor de nonce previamente cached.

A bolha de assinatura é uma versão de documento assinada por [pkcs7.](https://aka.ms/pkcs7) Contém o certificado utilizado para a assinatura juntamente com certos detalhes específicos da VM.

Para os VMs criados através da utilização do Azure Resource Manager, o documento `vmId` `sku` inclui, `nonce` , , , para a `subscriptionId` `timeStamp` criação e expiração do documento, e a informação do plano sobre a imagem. A informação do plano só é preenchida para imagens do Azure Marketplace.

Para os VMs criados utilizando o modelo de implementação clássico, apenas o `vmId` é garantido para ser povoado. Pode extrair o certificado da resposta e usá-lo para confirmar que a resposta é válida e vem do Azure.

O documento descodificado contém os seguintes campos:

| Dados | Descrição | Versão introduzida |
|------|-------------|--------------------|
| `licenseType` | Tipo de licença para [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Isto só está presente para VMs ativados por AHB. | 2020-09-01
| `nonce` | Uma corda que pode ser opcionalmente fornecida com o pedido. Se não `nonce` for fornecida, utiliza-se a atual estação de tempo universal coordenada. | 2018-10-01
| `plan` | O [plano Azure Marketplace Image](/rest/api/compute/virtualmachines/createorupdate#plan). Contém o ID do plano (nome), imagem ou oferta de produto (produto) e ID do editor (editor). | 2018-10-01
| `timestamp.createdOn` | O calendário utc para quando o documento assinado foi criado | 2018-20-01
| `timestamp.expiresOn` | O calendário utc para quando o documento assinado expirar | 2018-10-01
| `vmId` | [Identificador único](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para o VM | 2018-10-01
| `subscriptionId` | Assinatura Azure para a Máquina Virtual | 2019-04-30
| `sku` | SKU específico para a imagem VM | 2019-11-01

> [!NOTE]
> Para VMs Clássicos (não-Azure Resource Manager), apenas o vmId está garantido para ser povoado.

Documento de exemplo:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Amostra 1: Validar que o VM está a funcionar em Azure

Os fornecedores no Azure Marketplace querem garantir que o seu software está licenciado para funcionar apenas em Azure. Se alguém copia o VHD para um ambiente no local, o vendedor precisa ser capaz de detetar isso. Através do IMDS, estes fornecedores podem obter dados assinados que garantem a resposta apenas a partir do Azure.

> [!NOTE]
> Esta amostra requer a instalação do utilitário JQ.

**Validação**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Verifique se a assinatura é da Microsoft Azure e verifique se a cadeia de certificados tem erros.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Resultados**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Verifique se a assinatura é da Microsoft Azure e verifique se a cadeia de certificados tem erros.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> Devido ao mecanismo de cache do IMDS, um valor anteriormente em cache `nonce` pode ser devolvido.

O `nonce` documento assinado pode ser comparado se fornecer um parâmetro no pedido `nonce` inicial.

> [!NOTE]
> O certificado para a nuvem pública e cada nuvem soberana será diferente.

| Cloud | Certificado |
|-------|-------------|
| [Todas as regiões globais de Azure geralmente disponíveis](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> Os certificados podem não ter uma correspondência exata `metadata.azure.com` para a nuvem pública. Por esta razão, a validação da certificação deve permitir um nome comum a partir de qualquer `.metadata.azure.com` subdomínio.

Nos casos em que o certificado intermédio não pode ser descarregado devido a restrições de rede durante a validação, pode fixar o certificado intermédio. Azure passa os certificados, que é prática pki padrão. Tem de atualizar os certificados fixados quando o capotamento acontecer. Sempre que for planeada uma alteração para atualizar o certificado intermédio, o blog Azure é atualizado e os clientes Azure são notificados. 

Pode encontrar os certificados intermédios no [repositório PKI.](https://www.microsoft.com/pki/mscorp/cps/default.htm) Os certificados intermédios para cada uma das regiões podem ser diferentes.

> [!NOTE]
> O certificado intermédio para a Azure China 21Vianet será da DigiCert Global Root CA, em vez de Baltimore.
Se fixar os certificados intermédios para a Azure China como parte de uma alteração da autoridade da cadeia de raízes, os certificados intermédios devem ser atualizados.


## <a name="managed-identity"></a>Identidade gerida

Uma identidade gerida, atribuída pelo sistema, pode ser ativada no VM. Também pode atribuir uma ou mais identidades geridas atribuídas ao VM.
Em seguida, pode solicitar fichas para identidades geridas do IMDS. Utilize estes tokens para autenticar com outros serviços Azure, como o Azure Key Vault.

Para obter passos detalhados para ativar esta funcionalidade, consulte [Adquirir um token de acesso](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events"></a>Eventos agendados
Pode obter o estado dos eventos agendados utilizando o IMDS. Em seguida, o utilizador pode especificar um conjunto de ações para executar sobre estes eventos. Para mais informações, consulte [eventos agendados para linux](../articles/virtual-machines/linux/scheduled-events.md) ou [eventos agendados para Windows.](../articles/virtual-machines/windows/scheduled-events.md)

## <a name="sample-code-in-different-languages"></a>Código de amostra em diferentes línguas

A tabela que se segue lista amostras de IMDS chamando IMDS utilizando diferentes idiomas dentro do VM:

| Linguagem | Exemplo |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Ir | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Erros e depuragem

Se houver um elemento de dados não encontrado ou um pedido mal formado, o Serviço de Metadados de Instância retorna erros HTTP padrão. Por exemplo:

| Código de estado de HTTP | Razão |
|------------------|--------|
| `200 OK` | O pedido foi bem sucedido.
| `400 Bad Request` | Cabeçalho em `Metadata: true` falta ou parâmetro em falta ao consultar um nó de `format=json` folha
| `404 Not Found` | O elemento solicitado não existe
| `405 Method Not Allowed` | O método HTTP (verbo) não é suportado no ponto final.
| `410 Gone` | Reda o tempo após algum tempo por um máximo de 70 segundos
| `429 Too Many Requests` | Os [limites da taxa de](#rate-limiting) API foram ultrapassados
| `500 Service Error` | Redando depois de algum tempo

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Estou a perceber o `400 Bad Request, Required metadata header not specified` erro. O que isto significa?**

O IMDS requer que o cabeçalho `Metadata: true` seja passado no pedido. Passar este cabeçalho na chamada REST permite o acesso ao IMDS.

**Porque é que não estou a receber informações sobre o meu VM?**

Atualmente, o IMDS apenas suporta casos criados com o Azure Resource Manager.

**Criei o meu VM através do Azure Resource Manager há algum tempo. Porque é que não estou a ver informações de metadados computacional?**

Se criou o seu VM depois de setembro de 2016, adicione uma [etiqueta](../articles/azure-resource-manager/management/tag-resources.md) para começar a ver metadados compute. Se criou o seu VM antes de setembro de 2016, adicione ou remova extensões ou discos de dados à instância VM para atualizar metadados.

**Por que não vejo todos os dados preenchidos para uma nova versão?**

Se criou o seu VM depois de setembro de 2016, adicione uma [etiqueta](../articles/azure-resource-manager/management/tag-resources.md) para começar a ver metadados compute. Se criou o seu VM antes de setembro de 2016, adicione ou remova extensões ou discos de dados à instância VM para atualizar metadados.

**Porque é que estou a cometer o erro `500 Internal Server Error` `410 Resource Gone` ou?**

Relemisse o seu pedido. Para obter mais informações, consulte [o manuseamento de falhas transitórias.](/azure/architecture/best-practices/transient-faults) Se o problema persistir, crie um problema de suporte no portal Azure para o VM.

**Isto funcionaria para instâncias de escala de máquina virtual?**

Sim, o IMDS está disponível para instâncias de escala de máquina virtual.

**Atualizei as minhas etiquetas em conjuntos de escala de máquinas virtuais, mas não aparecem nos casos (ao contrário de VMs de exemplo único). Estou a fazer alguma coisa errada?**

Atualmente, as etiquetas para conjuntos de escala de máquinas virtuais só mostram o VM num reboot, reimagem ou alteração de disco para o caso.

**Porque é que o meu pedido está esgotado para a minha chamada para o serviço?**

As chamadas de metadados devem ser efetuadas a partir do endereço IP primário atribuído ao cartão de rede primário do VM. Além disso, se alterou as suas rotas, deve haver uma rota para o endereço 169.254.169.254/32 na tabela de encaminhamento local do seu VM.

#### <a name="windows"></a>[Windows](#tab/windows/)

1. Despeje a sua mesa de encaminhamento local e procure a entrada IMDS. Por exemplo:
    ```console
    > route print
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
      169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
    ... (continues) ...
    ```
1. Verifique se existe uma rota para `169.254.169.254` , e note a interface de rede correspondente (por exemplo, `172.16.69.7` ).
1. Despeje a configuração da interface e encontre a interface que corresponde à referida na tabela de encaminhamento, observando o endereço MAC (físico).
    ```console
    > ipconfig /all
    ... (continues) ...
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
       Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
       Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
       DHCP Enabled. . . . . . . . . . . : Yes
       Autoconfiguration Enabled . . . . : Yes
       Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
       IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
       Subnet Mask . . . . . . . . . . . : 255.255.255.0
    ... (continues) ...
    ```
1. Confirme que a interface corresponde ao NIC primário do VM e ao IP primário. Pode encontrar o NIC e o IP primários olhando para a configuração da rede no portal Azure, ou olhando-o para cima com o Azure CLI. Note os IPs privados (e o endereço MAC se estiver a utilizar o CLI). Aqui está um exemplo do PowerShell CLI:
    ```powershell
    $ResourceGroup = '<Resource_Group>'
    $VmName = '<VM_Name>'
    $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
    foreach($NicName in $NicNames)
    {
        $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
        Write-Host $NicName, $Nic.primary, $Nic.macAddress
    }
    # Output: wintest767 True 00-0D-3A-E5-1C-C0
    ```
1. Se não corresponderem, atualize a tabela de encaminhamento de modo a que o NIC e o IP primários sejam alvo.

#### <a name="linux"></a>[Linux](#tab/linux/)

 1. Despeje a sua mesa de encaminhamento local com um comando como `netstat -r` e procure a entrada IMDS (por exemplo):
    ```console
    ~$ netstat -r
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
    default         _gateway        0.0.0.0         UG        0 0          0 eth0
    168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
    169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
    172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
    ```
1. Verifique se existe uma rota para `169.254.169.254` , e note a interface de rede correspondente (por `eth0` exemplo).
1. Despeje a configuração da interface para a interface correspondente na tabela de encaminhamento (note que o nome exato do ficheiro de configuração pode variar)
    ```console
    ~$ cat /etc/netplan/50-cloud-init.yaml
    network:
    ethernets:
        eth0:
            dhcp4: true
            dhcp4-overrides:
                route-metric: 100
            dhcp6: false
            match:
                macaddress: 00:0d:3a:e4:c7:2e
            set-name: eth0
    version: 2
    ```
1. Se estiver a utilizar um IP dinâmico, observe o endereço MAC. Se estiver a utilizar um IP estático, poderá observar os IP(s) listados e/ou o endereço MAC.
1. Confirme que a interface corresponde ao NIC primário do VM e ao IP primário. Pode encontrar o NIC e o IP primários olhando para a configuração da rede no portal Azure, ou olhando-o para cima com o Azure CLI. Note os IPs privados (e o endereço MAC se estiver a utilizar o CLI). Aqui está um exemplo do PowerShell CLI:
    ```powershell
    $ResourceGroup = '<Resource_Group>'
    $VmName = '<VM_Name>'
    $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
    foreach($NicName in $NicNames)
    {
        $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
        Write-Host $NicName, $Nic.primary, $Nic.macAddress
    }
    # Output: ipexample606 True 00-0D-3A-E4-C7-2E
    ```
1. Se não corresponderem, atualize a tabela de encaminhamento de modo a que o NIC/IP primário seja alvo.

---

**Agrupamento de falha no Windows Server**

Quando se consulta o IMDS com o agrupamento de failover, às vezes é necessário adicionar uma rota à mesa de encaminhamento. Eis como:

1. Abra uma linha de comandos com privilégios de administrador.

1. Executar o seguinte comando e anotar o endereço da Interface para o Destino da Rede ( `0.0.0.0` ) na Tabela de Rota IPv4.

```bat
route print
```

> [!NOTE]
> A saída de exemplo a seguir é de um VM do Servidor do Windows com um cluster de falha ativado. Para simplificar, a saída contém apenas a Tabela de Rota IPv4.

```
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

Executar o seguinte comando e utilizar o endereço da Interface para Destino de Rede ( `0.0.0.0` que é ) neste `10.0.1.10` exemplo.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="support"></a>Suporte

Se não conseguir obter uma resposta de metadados após várias tentativas, pode criar um problema de suporte no portal Azure.

## <a name="product-feedback"></a>Comentários sobre o produto

Pode fornecer feedback e ideias do produto ao nosso canal de feedback do utilizador no Serviço de Metadados > Exemplo de Máquinas Virtuais: https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627

## <a name="next-steps"></a>Passos seguintes

[Adquirir um símbolo de acesso para o VM](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Eventos agendados para Linux](../articles/virtual-machines/linux/scheduled-events.md)

[Eventos agendados para Windows](../articles/virtual-machines/windows/scheduled-events.md)
