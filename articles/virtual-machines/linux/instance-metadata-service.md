---
title: Serviço de Metadados de Caso Azure para Linux
description: Saiba mais sobre o Serviço de Metadados Azure Instance e como fornece informações sobre casos de máquinas virtuais atualmente em execução em Linux.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: c9a4f5697fb667cde2cf3b4ddc3d637ff58149c9
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436594"
---
# <a name="azure-instance-metadata-service-imds"></a>Serviço de metadados de caso Azure (IMDs)

O Azure Instance Metadata Service (IMDS) fornece informações sobre casos de máquinas virtuais atualmente em execução. Pode usá-lo para gerir e configurar as suas máquinas virtuais.
Esta informação inclui o SKU, armazenamento, configurações de rede e eventos de manutenção futuros. Para obter uma lista completa dos dados disponíveis, consulte [as APIs de metadados](#metadata-apis).

O IMDS está disponível para casos de funcionamento de máquinas virtuais (VMs) e instâncias de conjunto de escala de máquina virtual. Todos os APIs suportam VMs criados e geridos através do [Azure Resource Manager](/rest/api/resources/). Apenas os pontos finais atestados e de rede suportam VMs criados utilizando o modelo de implementação clássico. O ponto final atestado fá-lo apenas em um grau limitado.

O IMDS é um ponto final de REST disponível num endereço IP bem conhecido e não roteado `169.254.169.254` (). Acede-lo apenas a partir do VM. A comunicação entre o VM e o IMDS nunca sai do hospedeiro.
Tenha os seus clientes HTTP a contornar os proxies web dentro do VM ao consultar o IMDS, e trate `169.254.169.254` o mesmo que [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Segurança

O ponto final do IMDS só está acessível a partir da caixa da máquina virtual em funcionamento num endereço IP não roteado. Além disso, qualquer pedido com `X-Forwarded-For` cabeçalho é rejeitado pelo serviço.
Os pedidos devem também conter um `Metadata: true` cabeçalho, a fim de garantir que o pedido efetivo foi diretamente pretendido e não uma parte da reorientação não intencional.

> [!IMPORTANT]
> O IMDS não é um canal para dados sensíveis. O ponto final está aberto a todos os processos no VM. Considere a informação exposta através deste serviço como informação partilhada a todas as aplicações que estão dentro do VM.

## <a name="usage"></a>Utilização

### <a name="access-azure-instance-metadata-service"></a>Acesso Azure Instance Metadados

Para aceder ao IMDS, crie um VM a partir do [Azure Resource Manager](/rest/api/resources/) ou do [portal Azure](https://portal.azure.com), e use as seguintes amostras.
Para mais exemplos, consulte [amostras de metadados de exemplo de exemplos](https://github.com/microsoft/azureimds).

Aqui está o código de amostra para recuperar todos os metadados, por exemplo. Para aceder a uma fonte de dados específica, consulte a secção [API dos metadados.](#metadata-apis) 

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

**Response**

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo a seguir é bastante impressa para a legibilidade.

```json
{
    "compute": {
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
    },
    "network": {
        "interface": [{
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
        }]
    }
}
```

### <a name="data-output"></a>Saída de dados

Por predefinição, o IMDS devolve dados em formato JSON `Content-Type: application/json` (). No entanto, algumas APIs podem devolver dados em diferentes formatos, se solicitado.
A tabela que se segue lista outros formatos de dados que as APIs podem suportar.

API | Formato de dados predefinidos | Outros formatos
--------|---------------------|--------------
/atestado | json | nenhum
/identidade | json | nenhum
/instância | json | texto
/horários eventos | json | nenhum

Para aceder a um formato de resposta não padrão, especifique o formato solicitado como um parâmetro de cadeia de consulta no pedido. Por exemplo:

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Para os nós de `/metadata/instance` folhas, o `format=json` não funciona. Para estas consultas, `format=text` é necessário especificar explicitamente porque o formato padrão é JSON.

### <a name="version"></a>Versão

O IMDS é versão e especificar a versão API no pedido HTTP é obrigatório.

As versões API suportadas são: 
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
> A versão 2020-10-01 pode ainda não estar disponível em todas as regiões.

À medida que as versões mais recentes são adicionadas, ainda pode aceder a versões mais antigas para compatibilidade se os seus scripts tiverem dependências de formatos de dados específicos.

Quando não especifica uma versão, obtém-se um erro, com uma lista das versões mais recentes suportadas.

> [!NOTE]
> A resposta é uma corda JSON. O exemplo a seguir indica a condição de erro quando a versão não é especificada. A resposta está bastante impressa para a legibilidade.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance"
```

**Response**

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

## <a name="metadata-apis"></a>APIs de metadados

O IMDS contém várias APIs representando diferentes fontes de dados.

API | Descrição | Versão introduzida
----|-------------|-----------------------
/atestado | Ver [Dados Attestados](#attested-data) | 2018-10-01
/identidade | Ver [Adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instância | Ver [Exemplo API](#instance-api) | 2017-04-02
/horários eventos | Ver [Eventos Agendados](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>API de exemplo

A API de caso expõe os metadados importantes para as instâncias VM, incluindo o VM, a rede e o armazenamento. Pode aceder às seguintes categorias através `instance/compute` de:

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
azEnvironment | O ambiente Azure em que o VM está em funcionamento. | 2018-10-01
customData | Esta funcionalidade encontra-se atualmente desativada. | 2019-02-01
isHostCompatibilityLayerVm | Identifica se o VM funciona na Camada de Compatibilidade do Anfitrião. | 2020-06-01
tipo de licença | O tipo de licença para [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Note que este só está presente para VMs ativados por AHB. | 2020-09-01
localização | A região de Azure em que o VM está em funcionamento. | 2017-04-02
name | O nome do VM. | 2017-04-02
oferta | Oferecer informações para a imagem VM. Isto só está presente para imagens implantadas na galeria de imagens Azure. | 2017-04-02
osProfile.adminUsername | Especifica o nome da conta de administração. | 2020-07-15
osProfile.computerName | Especifica o nome do computador. | 2020-07-15
osProfile.desativarpassasauthentication | Especifica se a autenticação de palavra-passe é desativada. Note que este só está presente para os VMs Linux. | 2020-10-01
osTipos | Linux ou Windows. | 2017-04-02
placementGroupId | Grupo de [colocação](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) do seu conjunto de escala de máquina virtual. | 2017-08-01
plano | [Plano](/rest/api/compute/virtualmachines/createorupdate#plan) que contenha o nome, produto e editor para um VM se for uma imagem do Azure Marketplace. | 2018-04-02
plataformaUpdateDomain |  [Atualize o domínio](../manage-availability.md) em que o VM está a funcionar. | 2017-04-02
plataformaFaultDomain | [Domínio de avarias](../manage-availability.md) em que o VM está a funcionar. | 2017-04-02
provedor | O provedor do VM. | 2018-10-01
publicKeys | [Coleção de chaves públicas](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) atribuídas ao VM e caminhos. | 2018-04-02
publicador | Editor da imagem VM. | 2017-04-02
resourceGroupName | [Grupo de recursos](../../azure-resource-manager/management/overview.md) para o seu VM. | 2017-08-01
resourceId | A [identificação totalmente qualificada](/rest/api/resources/resources/getbyid) do recurso. | 2019-03-11
sku | O SKU específico para a imagem VM. | 2017-04-02
securityProfile.secureBootEnabled | Identifica se a bota segura UEFI estiver ativada no VM. | 2020-06-01
securityProfile.virtualTpmEnabled | Identifica se o Módulo de Plataforma Fidedigna virtual (TPM) está ativado no VM. | 2020-06-01
armazenamentoProfile | Consulte [o perfil de armazenamento](#storage-metadata). | 2019-06-01
subscriptionId | Assinatura Azure para o VM. | 2017-08-01
etiquetas | [Etiquetas](../../azure-resource-manager/management/tag-resources.md) para o seu VM.  | 2017-08-01
tagsList | Tags formatadas como uma matriz JSON para uma análise programática mais fácil.  | 2019-06-04
versão | A versão da imagem VM. | 2017-04-02
vmId | [Identificador único](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para o VM. | 2017-04-02
vmScaleSetName | [O nome do conjunto](../../virtual-machine-scale-sets/overview.md) de escala de máquina virtual do conjunto de escala de máquina virtual. | 2017-12-01
vmSize | Ver [tamanho VM](../sizes.md). | 2017-04-02
zona | [Zona de Disponibilidade](../../availability-zones/az-overview.md) do seu VM. | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>Amostra 1: Acompanhe um VM em execução em Azure

Como prestador de serviços, poderá ter de rastrear o número de VMs que executam o seu software, ou ter agentes que precisam de rastrear a singularidade do VM. Para conseguir obter um ID único para um VM, use o `vmId` campo do IMDS.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Response**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>Amostra 2: Colocação de diferentes réplicas de dados

Para certos cenários, a colocação de diferentes réplicas de dados é de primordial importância. Por exemplo, a colocação de [réplicas HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou a colocação de contentores através de um [orquestrador](https://kubernetes.io/docs/user-guide/node-selection/) pode exigir que você saiba `platformFaultDomain` o e o `platformUpdateDomain` VM está em execução.
Também pode utilizar [Zonas de Disponibilidade](../../availability-zones/az-overview.md) para as instâncias para tomar estas decisões.
Pode consultar estes dados diretamente através do IMDS.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Response**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>Amostra 3: Obter mais informações sobre o VM durante o caso de apoio

Como prestador de serviços, poderá receber uma chamada de apoio onde pretender saber mais informações sobre o VM. Pedir ao cliente que partilhe os metadados do cálculo pode ser útil neste caso.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

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

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>Amostra 4: Obtenha o ambiente Azure onde o VM está em execução

Azure tem várias nuvens soberanas, como [o Governo de Azure.](https://azure.microsoft.com/overview/clouds/government/) Às vezes é preciso o ambiente Azure para tomar algumas decisões de tempo de execução. A amostra que se segue mostra como pode alcançar este comportamento.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Response**

```text
AzurePublicCloud
```

A nuvem e os valores do ambiente Azure estão listados aqui.

 Cloud   | Ambiente azul
---------|-----------------
[Todas as regiões globais de Azure geralmente disponíveis](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureusGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Metadados de rede 

Os metadados de rede fazem parte do caso API. As seguintes categorias de rede estão disponíveis através do `instance/network` ponto final.

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
ipv4/privateIpAddress | O endereço local IPv4 do VM. | 2017-04-02
ipv4/publicIpAddress | O endereço público do IPv4 do VM. | 2017-04-02
sub-rede/endereço | O endereço da sub-rede do VM. | 2017-04-02
sub-rede/prefixo | O prefixo do sub-rede. Exemplo: 24 | 2017-04-02
ipv6/ipAddress | O endereço local do IPv6 do VM. | 2017-04-02
macAddress | O endereço do Mac VM. | 2017-04-02

> [!NOTE]
> Todas as respostas da API são cordas JSON. Todas as respostas de exemplo são bastante impressas para a legibilidade.

#### <a name="sample-1-retrieve-network-information"></a>Amostra 1: Recuperar informações sobre a rede

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

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

#### <a name="sample-2-retrieve-public-ip-address"></a>Amostra 2: Recuperar endereço IP público

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Metadados de armazenamento

Os metadados de armazenamento fazem parte do caso API, em `instance/compute/storageProfile` ponto final.
Fornece detalhes sobre os discos de armazenamento associados ao VM. 

O perfil de armazenamento de um VM é dividido em três categorias: referência de imagem, disco de sistema operativo e discos de dados.

O objeto de referência de imagem contém as seguintes informações sobre a imagem do sistema operativo:

Dados    | Descrição
--------|-----------------
ID      | ID do Recurso
oferta   | Oferta da plataforma ou imagem
publicador | Editor de imagem
sku     | Imagem SKU
versão | Versão da plataforma ou imagem

O objeto do disco do sistema operativo contém as seguintes informações sobre o disco do sistema operativo utilizado pelo VM:

Dados    | Descrição
--------|-----------------
caching | Requisitos de caching
criar Opção | Informação sobre como o VM foi criado
difusasDiskSettings | Definições de disco efémero
diskSizeGB | Tamanho do disco em GB
image   | Disco rígido virtual de imagem de utilizador de origem
lun     | Número de unidade lógica do disco
geridoDisk | Parâmetros de disco geridos
name    | Nome do disco
vhd     | Disco rígido virtual
writeAcceleratorEnabled | Se está ou não `writeAccelerator` ativado no disco

A matriz de discos de dados contém uma lista de discos de dados anexados ao VM. Cada objeto de disco de dados contém as seguintes informações:

Dados    | Descrição
--------|-----------------
caching | Requisitos de caching
criar Opção | Informação sobre como o VM foi criado
difusasDiskSettings | Definições de disco efémero
diskSizeGB | Tamanho do disco em GB
encriptaçãoS | Definições de encriptação para o disco
image   | Disco rígido virtual de imagem de utilizador de origem
geridoDisk | Parâmetros de disco geridos
name    | Nome do disco
osTipos  | Tipo de sistema operativo incluído no disco
vhd     | Disco rígido virtual
writeAcceleratorEnabled | Se está ou não `writeAccelerator` ativado no disco

O exemplo a seguir mostra como consultar as informações de armazenamento do VM.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Response**

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo a seguir é bastante impressa para a legibilidade.

```json
{
    "dataDisks": [
      {
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
      }
    ],
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
}
```

## <a name="vm-tags"></a>Etiquetas VM

As etiquetas VM estão incluídas no caso API, no `instance/compute/tags` ponto final.
As etiquetas podem ter sido aplicadas ao seu Azure VM para organizar logicamente uma taxonomia. Pode recuperar as etiquetas atribuídas a um VM utilizando o seguinte pedido.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Response**

```text
Department:IT;Environment:Test;Role:WebRole
```

O `tags` campo é uma corda com as etiquetas delimitadas por pontos-e-vírguis. Esta saída pode ser um problema se os e-tím9is forem utilizados nas próprias etiquetas. Se um analisador for escrito para extrair programáticamente as etiquetas, deve confiar no `tagsList` campo. O `tagsList` campo é uma matriz JSON sem delimiters, e consequentemente é mais fácil de analisar.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04"
```

**Response**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>Dados atestados

O IMDS ajuda a fornecer garantias de que os dados fornecidos vêm do Azure. A Microsoft assina parte desta informação, para que possa confirmar que uma imagem no Azure Marketplace é a que está a correr no Azure.

### <a name="sample-1-get-attested-data"></a>Amostra 1: Obter dados atestados

> [!NOTE]
> Todas as respostas da API são cordas JSON. As respostas de exemplo a seguir são bastante impressas para a legibilidade.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

`Api-version` é um campo obrigatório. Consulte a [secção de utilização](#usage) para versões API suportadas.
`Nonce` é uma cadeia opcional de 10 dígitos. Se não for fornecido, o IMDS devolve o atual tempo de tempo universal coordenado no seu lugar.

> [!NOTE]
> Devido ao mecanismo de cache do IMDS, um valor anteriormente em cache `nonce` pode ser devolvido.

**Response**

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

A bolha de assinatura é uma versão assinada por [pkcs7](https://aka.ms/pkcs7)do documento. Contém o certificado utilizado para a assinatura, juntamente com certos detalhes específicos da VM. 

Para os VMs criados através da utilização do Azure Resource Manager, isto `vmId` `sku` inclui, `nonce` , , para `subscriptionId` a `timeStamp` criação e expiração do documento, e a informação do plano sobre a imagem. A informação do plano só é preenchida para imagens do Azure Marketplace. 

Para os VMs criados utilizando o modelo de implementação clássico, apenas `vmId` é garantido que serão povoados. Pode extrair o certificado da resposta e usá-lo para confirmar que a resposta é válida e vem do Azure.

O documento contém os seguintes campos:

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
tipo de licença | Tipo de licença para [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Note que este só está presente para VMs ativados por AHB. | 2020-09-01
nonce | Uma corda que pode ser opcionalmente fornecida com o pedido. Se não `nonce` for fornecida, utiliza-se a atual estação de tempo universal coordenada. | 2018-10-01
plano | O [plano Azure Marketplace Image](/rest/api/compute/virtualmachines/createorupdate#plan). Contém o ID do plano (nome), imagem ou oferta de produto (produto) e ID do editor (editor). | 2018-10-01
timestamp/createdOn | O tempo de tempo universal coordenado para quando o documento assinado foi criado. | 2018-20-01
timetamp/expiraOn | A hora de tempo universal coordenada para quando o documento assinado expirar. | 2018-10-01
vmId |  [Identificador único](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para o VM. | 2018-10-01
subscriptionId | A assinatura Azure para o VM. | 2019-04-30
sku | O SKU específico para a imagem VM. | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>Amostra 2: Validar que o VM está a funcionar em Azure

Os fornecedores no Azure Marketplace querem garantir que o seu software está licenciado para funcionar apenas em Azure. Se alguém copia o VHD para um ambiente no local, o vendedor precisa ser capaz de detetar isso. Através do IMDS, estes fornecedores podem obter dados assinados que garantem a resposta apenas a partir do Azure.

> [!NOTE]
> Esta amostra requer a instalação do utilitário JQ.

**Pedir**

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

**Response**

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

> [!NOTE]
> Devido ao mecanismo de cache do IMDS, um valor anteriormente em cache `nonce` pode ser devolvido.

O `nonce` documento assinado pode ser comparado se fornecer um parâmetro no pedido `nonce` inicial.

> [!NOTE]
> O certificado para a nuvem pública e cada nuvem soberana será diferente.

Cloud | Certificado
------|------------
[Todas as regiões globais de Azure geralmente disponíveis](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> Os certificados podem não ter uma correspondência exata `metadata.azure.com` para a nuvem pública. Por esta razão, a validação da certificação deve permitir um nome comum a partir de qualquer `.metadata.azure.com` subdomínio.

Nos casos em que o certificado intermédio não pode ser descarregado devido a restrições de rede durante a validação, pode fixar o certificado intermédio. Note que a Azure passa os certificados, que é prática pki padrão. Tem de atualizar os certificados fixados quando o capotamento acontecer. Sempre que for planeada uma alteração para atualizar o certificado intermédio, o blog Azure é atualizado e os clientes Azure são notificados. 

Pode encontrar os certificados intermédios no [repositório PKI.](https://www.microsoft.com/pki/mscorp/cps/default.htm) Os certificados intermédios para cada uma das regiões podem ser diferentes.

> [!NOTE]
> O certificado intermédio para a Azure China 21Vianet será da DigiCert Global Root CA, em vez de Baltimore.
Se fixar os certificados intermédios para a Azure China como parte de uma alteração da autoridade da cadeia de raízes, os certificados intermédios devem ser atualizados.

## <a name="managed-identity"></a>Identidade gerida

Uma identidade gerida, atribuída pelo sistema, pode ser ativada no VM. Também pode atribuir uma ou mais identidades geridas atribuídas ao VM.
Em seguida, pode solicitar fichas para identidades geridas do IMDS. Utilize estes tokens para autenticar com outros serviços Azure, como o Azure Key Vault.

Para obter passos detalhados para ativar esta funcionalidade, consulte [Adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events"></a>Eventos agendados
Pode obter o estado dos eventos agendados utilizando o IMDS. Em seguida, o utilizador pode especificar um conjunto de ações para executar sobre estes eventos. Para mais informações, consulte [eventos agendados.](scheduled-events.md)

## <a name="regional-availability"></a>Disponibilidade regional

O serviço está geralmente disponível em todas as nuvens Azure.

## <a name="sample-code-in-different-languages"></a>Código de amostra em diferentes línguas

A tabela que se segue lista amostras de IMDS chamando IMDS utilizando diferentes idiomas dentro do VM:

Linguagem      | Exemplo
--------------|----------------
Bash          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Ir            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Erros e depuragem

Se houver um elemento de dados não encontrado ou um pedido mal formado, o Serviço de Metadados de Instância retorna erros HTTP padrão. Por exemplo:

Código de estado de HTTP | Razão
-----------------|-------
200 OK |
400 Pedido Incorreto | Cabeçalho em falta `Metadata: true` ou parâmetro em falta ao consultar um nó de `format=json` folha.
404 Não Encontrado  | O elemento solicitado não existe.
405 Método Não Permitido | Apenas `GET` os pedidos são apoiados.
410 Desaparecidos | Reda o tempo após algum tempo por um máximo de 70 segundos.
Demasiados Pedidos 429 | A API suporta atualmente um máximo de 5 consultas por segundo.
Erro de serviço 500     | Tentar depois de algum tempo.

### <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Estou a perceber o `400 Bad Request, Required metadata header not specified` erro. O que isto significa?**

O IMDS requer que o cabeçalho `Metadata: true` seja passado no pedido. Passar este cabeçalho na chamada REST permite o acesso ao IMDS.

**Porque é que não estou a receber informações sobre o meu VM?**

Atualmente, o IMDS apenas suporta casos criados com o Azure Resource Manager.

**Criei o meu VM através do Azure Resource Manager há algum tempo. Porque é que não estou a ver informações de metadados computacional?**

Se criou o seu VM depois de setembro de 2016, adicione uma [etiqueta](../../azure-resource-manager/management/tag-resources.md) para começar a ver metadados compute. Se criou o seu VM antes de setembro de 2016, adicione ou remova extensões ou discos de dados à instância VM para atualizar metadados.

**Por que não vejo todos os dados preenchidos para uma nova versão?**

Se criou o seu VM depois de setembro de 2016, adicione uma [etiqueta](../../azure-resource-manager/management/tag-resources.md) para começar a ver metadados compute. Se criou o seu VM antes de setembro de 2016, adicione ou remova extensões ou discos de dados à instância VM para atualizar metadados.

**Porque é que estou a cometer o erro `500 Internal Server Error` `410 Resource Gone` ou?**

Relemisse o seu pedido. Para obter mais informações, consulte [o manuseamento de falhas transitórias.](/azure/architecture/best-practices/transient-faults) Se o problema persistir, crie um problema de suporte no portal Azure para o VM.

**Isto funcionaria para instâncias de escala de máquina virtual?**

Sim, o IMDS está disponível para instâncias de escala de máquina virtual.

**Atualizei as minhas etiquetas em conjuntos de escala de máquinas virtuais, mas não aparecem nos casos (ao contrário de VMs de exemplo único). Estou a fazer alguma coisa errada?**

Atualmente, as etiquetas para conjuntos de escala de máquinas virtuais só mostram o VM num reboot, reimagem ou alteração de disco para o caso.

**Porque é que o meu pedido está esgotado para a minha chamada para o serviço?**

As chamadas de metadados devem ser efetuadas a partir do endereço IP primário atribuído ao cartão de rede primário do VM. Além disso, se alterou as suas rotas, deve haver uma rota para o endereço 169.254.169.254/32 na tabela de encaminhamento local do seu VM.

   * <details>
        <summary>Verificação da sua mesa de encaminhamento</summary>

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
        1. Confirme que a interface corresponde ao NIC primário do VM e ao IP primário. Pode encontrar o NIC e o IP primários olhando para a configuração da rede no portal Azure, ou olhando-o para cima com o Azure CLI. Observe os IPs públicos e privados (e o endereço MAC se estiver a utilizar o CLI). Aqui está um exemplo do PowerShell CLI:
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
    </details>

## <a name="support"></a>Suporte

Se não conseguir obter uma resposta de metadados após várias tentativas, pode criar um problema de suporte no portal Azure.
Para **Tipo de Problemas**, selecione **Gestão**. Para **categoria**, selecione **Instance Medata Service**.

![Screenshot do suporte do serviço de metadados de exemplo](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Passos seguintes

[Adquirir um símbolo de acesso para o VM](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Eventos agendados](scheduled-events.md)

