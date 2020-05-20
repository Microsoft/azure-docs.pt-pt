---
title: Serviço de Metadados de Instância Azure
description: Interface RESTful para obter informações sobre a computação, rede e eventos de manutenção de VMs Linux.
services: virtual-machines-linux
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines-linux
ms.subservice: monitoring
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: ce3463d39b17e7099f85945caa92d1f009b696c0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649846"
---
# <a name="azure-instance-metadata-service"></a>Serviço de Metadados de Instância Azure

O Serviço de Metadados de InstânciaS Azure (IMDS) fornece informações sobre casos de máquinas virtuais atualmente em execução e pode ser usado para gerir e configurar as suas máquinas virtuais.
Isto inclui o SKU, armazenamento, configurações de rede e eventos de manutenção futuros. Para obter uma lista completa dos dados disponíveis, consulte [os metadados APIs](#metadata-apis). Por exemplo, o Serviço de Metadados está disponível tanto para o Conjunto de VM como para a escala de máquina virtual Instâncias. Só está disponível para executar VMs criados/geridos utilizando o [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). 

O Serviço de Metadados de Instância do Azure é um ponto final REST que está disponível num endereço IP não resaída conhecido ( `169.254.169.254` ), que só pode ser acedido a partir do VM.

## <a name="security"></a>Segurança

O ponto final do Serviço de Metadados de Instância só é acessível a partir da instância da máquina virtual em execução num endereço IP não resaída. Além disso, qualquer pedido com `X-Forwarded-For` cabeçalho é rejeitado pelo serviço.
Os pedidos devem também conter um `Metadata: true` cabeçalho para garantir que o pedido efetivo foi diretamente concebido e não uma parte de uma reorientação não intencional.

> [!IMPORTANT]
> Por exemplo, o Serviço de Metadados não é um canal para dados sensíveis. O ponto final está aberto a todos os processos no VM. As informações expostas através deste serviço devem ser consideradas como informações partilhadas para todas as aplicações que executam dentro do VM.

## <a name="usage"></a>Utilização

### <a name="accessing-azure-instance-metadata-service"></a>Aceder ao Serviço de Metadados de Instância Azure

Para aceder ao Serviço de Metadados de Instância, crie um VM do Gestor de [Recursos Azure](https://docs.microsoft.com/rest/api/resources/) ou do [portal Azure,](https://portal.azure.com)e siga as amostras abaixo.
Mais exemplos de como consultar o IMDS podem ser encontrados em amostras de [metadados de instância](https://github.com/microsoft/azureimds)seletiva .

Abaixo está o código da amostra para recuperar todos os metadados, por exemplo, para aceder a fonte seletiva de dados específica, ver secção [Demetação API.](#metadata-apis) 

**Pedir**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo que se segue é bastante impressa para a legibilidade.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
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
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

### <a name="data-output"></a>Saída de dados

Por padrão, o Serviço de Metadados de Instância devolve dados no formato JSON `Content-Type: application/json` (). No entanto, diferentes APIs devolvem dados em diferentes formatos, se solicitados.
A tabela a seguir é uma referência de outros formatos de dados que as APIs podem suportar.

API | Formato de Dados Predefinidos | Outros Formatos
--------|---------------------|--------------
/instância | json | texto
/eventos agendados | json | nenhum
/atestado | json | nenhum

Para aceder a um formato de resposta não predefinido, especifique o formato solicitado como parâmetro de corda de consulta no pedido. Por exemplo:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Para os narizes de folha, não `format=json` funciona. Para estas consultas `format=text` deve ser explicitamente especificada se o formato predefinido for json.

### <a name="versioning"></a>Controlo de versões

O Serviço de Metadados de Instância é versão e especificar a versão API no pedido HTTP é obrigatório.

Seguem-se as versões de serviço suportadas: 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15.

Note-se que quando a nova versão for lançada, levará algum tempo a ser lançada para todas as regiões. Atualmente, a versão 2019-11-01 ainda está a ser implementada e pode não estar disponível em todas as regiões.

À medida que as versões mais recentes são adicionadas, as versões mais antigas ainda podem ser acedidas para compatibilidade se os seus scripts tiverem dependências em formatos de dados específicos.

Quando nenhuma versão é especificada, um erro é devolvido com uma lista das versões mais recentes suportadas.

> [!NOTE]
> A resposta é uma corda JSON. O exemplo seguinte indica a condição de erro quando a versão não é especificada, a resposta é bastante impressa para a legibilidade.

**Pedir**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Resposta**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

## <a name="metadata-apis"></a>APIs de metadados
O IMDS contém múltiplas interfaces API que representam diferentes fontes de dados. 

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
instância | Ver [Instância API](#instance-api) | 2017-04-02
atestado | Ver [Dados Attested](#attested-data) | 2018-10-01
identidade | Ver [Adquirir um sinal de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
eventos agendados | Ver [Eventos Agendados](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>Instância API

A API expõe os metadados importantes para os casos vm, incluindo o VM, rede e armazenamento. As seguintes categorias podem ser acedidas por exemplo/cálculo:

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
azAmbiente | Ambiente Azure onde o VM está a correr | 2018-10-01
personalizadoData | Esta funcionalidade está atualmente desativada, e atualizaremos esta documentação quando estiver disponível | 2019-02-01
localização | Região de Azure o VM está a decorrer | 2017-04-02
name | Nome do VM | 2017-04-02
oferta | Forneça informações para a imagem vm e só está presente para imagens implantadas na galeria de imagens Azure | 2017-04-02
osType | Linux ou Windows | 2017-04-02
colocaçãoGroupId | Grupo de [colocação](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) do seu conjunto de escala de máquina virtual | 2017-08-01
plano | [Plano](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) contendo nome, produto e editor para um VM se for uma Imagem de Mercado Azure | 2018-04-02
platformUpdateDomain |  [Domínio de atualização](manage-availability.md) que o VM está a executar | 2017-04-02
plataformaFaultDomain | [Domínio de falha](manage-availability.md) o VM está a correr | 2017-04-02
fornecedor | Fornecedor do VM | 2018-10-01
publicKeys | [Coleção de Chaves Públicas](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) atribuídas ao VM e caminhos | 2018-04-02
publicador | Editor da imagem VM | 2017-04-02
resourceGroupName | [Grupo de recursos](../../azure-resource-manager/management/overview.md) para a sua Máquina Virtual | 2017-08-01
resourceId | A identificação [totalmente qualificada](https://docs.microsoft.com/rest/api/resources/resources/getbyid) do recurso | 2019-03-11
sku | SKU específico para a imagem VM | 2017-04-02
armazenamentoPerfil | Ver [Perfil de Armazenamento](#storage-metadata) | 2019-06-01
subscriptionId | Assinatura Azure para a Máquina Virtual | 2017-08-01
etiquetas | [Etiquetas](../../azure-resource-manager/management/tag-resources.md) para a sua Máquina Virtual  | 2017-08-01
tagsList | Tags formatadas como uma matriz JSON para uma análise programática mais fácil  | 2019-06-04
versão | Versão da imagem VM | 2017-04-02
vmId | [Identificador único](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para o VM | 2017-04-02
vmScaleSetName | [Conjunto](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) de escala de máquina virtual Nome do seu conjunto de escala de máquina virtual | 2017-12-01
vmSize | [Tamanho VM](sizes.md) | 2017-04-02
zona | [Zona de Disponibilidade](../../availability-zones/az-overview.md) da sua máquina virtual | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>Amostra 1: Rastreio vM em execução em Azure

Como prestador de serviços, poderá ser necessário rastrear o número de VMs que executam o seu software ou ter agentes que precisam de rastrear a singularidade do VM. Para obter um ID único para um VM, use o `vmId` campo do Serviço de Metadados de Instância.

**Pedir**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Resposta**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>Amostra 2: Colocação de contentores, domínio baseado em partições de dados/domínio de atualização

Para certos cenários, a colocação de diferentes réplicas de dados é de primordial importância. Por exemplo, a colocação de [réplicas hDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou de contentor através de um [orquestrador](https://kubernetes.io/docs/user-guide/node-selection/) pode precisar de saber o `platformFaultDomain` e o `platformUpdateDomain` VM está em execução.
Também pode utilizar [Zonas de Disponibilidade](../../availability-zones/az-overview.md) para os casos para tomar estas decisões.
Pode consultar estes dados diretamente através do Serviço de Metadados de Instância.

**Pedir**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Resposta**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>Amostra 3: Obter mais informações sobre o VM durante o caso de suporte

Como prestador de serviços, poderá receber uma chamada de apoio onde gostaria de saber mais informações sobre o VM. Pedir ao cliente que partilhe os metadados computacionais pode fornecer informações básicas para que o profissional de suporte saiba sobre o tipo de VM no Azure.

**Pedir**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo que se segue é bastante impressa para a legibilidade.

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
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
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>Amostra 4: Obter o Ambiente Azure onde o VM está em execução

Azure tem várias nuvens soberanas como [o Governo de Azure.](https://azure.microsoft.com/overview/clouds/government/) Às vezes é preciso o Ambiente Azure para tomar algumas decisões de tempo de corrido. A amostra que se segue mostra como pode alcançar este comportamento.

**Pedir**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Resposta**

```bash
AzurePublicCloud
```

A nuvem e os valores do Ambiente Azure estão listados abaixo.

 Nuvem   | Ambiente Azure
---------|-----------------
[Todas as regiões globais de Azure geralmente disponíveis](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Metadados de Rede 

Os metadados de rede fazem parte da instância API. As seguintes categorias de Rede estão disponíveis através do ponto final da rede/ instância/rede.

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
ipv4/privateIpAddress | Endereço IPv4 local do VM | 2017-04-02
ipv4/publicIpAddress | Endereço Público IPv4 do VM | 2017-04-02
subnet/endereço | Endereço de sub-rede do VM | 2017-04-02
subnet/prefixo | Prefixo de sub-rede, exemplo 24 | 2017-04-02
ipv6/ipAddress | Endereço IPv6 local do VM | 2017-04-02
macAddress | Endereço vM mac | 2017-04-02

> [!NOTE]
> Todas as respostas da API são cordas JSON. Todas as seguintes respostas exemplo são bastante impressas para a legibilidade.

#### <a name="sample-1-retrieving-network-information"></a>Amostra 1: Recuperação de informações da rede

***Pedir***

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

***Resposta***

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo que se segue é bastante impressa para a legibilidade.

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

#### <a name="sample-2-retrieving-public-ip-address"></a>Amostra 2: Recuperação do endereço IP público

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"

```

## <a name="storage-metadata"></a>Metadados de Armazenamento

Os metadados de armazenamento fazem parte da instância API em instância/cálculo/ponto final do perfil.
Fornece detalhes sobre os discos de armazenamento associados ao VM. 

O perfil de armazenamento de um VM está dividido em três categorias - referência de imagem, disco OS e discos de dados.

O objeto de referência da imagem contém as seguintes informações sobre a imagem de OS:

Dados    | Descrição
--------|-----------------
ID      | ID do Recurso
oferta   | Oferta da plataforma ou imagem de mercado
publicador | Editor de imagem
sku     | Sku de imagem
versão | Versão da plataforma ou imagem de mercado

O objeto do disco OS contém as seguintes informações sobre o disco OS utilizado pelo VM:

Dados    | Descrição
--------|-----------------
cache | Requisitos de caching
criarOpção | Informação sobre como o VM foi criado
definições difusdisk | Definições de disco efémero
diskSizeGB | Tamanho do disco em GB
image   | Disco rígido virtual de imagem de utilizador de origem
Lun     | Número lógico da unidade do disco
geriuDisk | Parâmetros de disco geridos
name    | Nome do disco
vhd     | Disco rígido virtual
escreverAcceleratorEnabled | Se escrever Ou não O Acelerador está ativado no disco

A matriz de discos de dados contém uma lista de discos de dados ligados ao VM. Cada objeto de disco de dados contém as seguintes informações:

Dados    | Descrição
--------|-----------------
cache | Requisitos de caching
criarOpção | Informação sobre como o VM foi criado
definições difusdisk | Definições de disco efémero
diskSizeGB | Tamanho do disco em GB
encriptaçãoDefinições | Definições de encriptação para o disco
image   | Disco rígido virtual de imagem de utilizador de origem
geriuDisk | Parâmetros de disco geridos
name    | Nome do disco
osType  | Tipo de SO incluído no disco
vhd     | Disco rígido virtual
escreverAcceleratorEnabled | Se escrever Ou não O Acelerador está ativado no disco

Segue-se um exemplo de como consultar as informações de armazenamento do VM.

**Pedir**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo que se segue é bastante impressa para a legibilidade.

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

As etiquetas VM estão incluídas na instância API em instância/ponto final de computação/etiquetas.
As etiquetas podem ter sido aplicadas ao seu Azure VM para logicamente organizá-las numa taxonomia. As etiquetas atribuídas a um VM podem ser recuperadas utilizando o pedido abaixo.

**Pedir**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Resposta**

```text
Department:IT;Environment:Test;Role:WebRole
```

O `tags` campo é uma corda com as etiquetas delimitadas por pontos evícidos. Isto pode ser um problema se os pontos-evípis forem usados nas próprias etiquetas. Se um parser for escrito para extrair programáticamente as etiquetas, deve confiar no `tagsList` campo que é uma matriz JSON sem delimitadores e, consequentemente, mais fácil de analisar.

**Pedir**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=json"
```

**Resposta**

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

## <a name="attested-data"></a>Dados Atados

Parte do cenário servido pelo Serviço de Metadados de Instância é fornecer garantias de que os dados fornecidos são provenientes do Azure. Assinamos parte desta informação para que as imagens do mercado possam ter a certeza de que é a sua imagem a correr no Azure.

### <a name="sample-1-getting-attested-data"></a>Amostra 1: Obtenção de dados atestados

> [!NOTE]
> Todas as respostas da API são cordas JSON. As seguintes respostas exemplo são bastante impressas para a legibilidade.

***Pedir***

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

A versão Api é um campo obrigatório. Consulte a [secção de utilização](#usage) para versões API suportadas.
Nonce é uma corda opcional de 10 dígitos. Caso não seja fornecido, o IMDS devolve a atual marca ção utc no seu lugar. Devido ao mecanismo de cache do IMDS, um valor nonce previamente emcached pode ser devolvido.

 ***Resposta***

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo que se segue é bastante impressa para a legibilidade.

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

A bolha de assinatura é uma versão assinada [pkcs7](https://aka.ms/pkcs7) do documento. Contém o certificado utilizado para a assinatura juntamente com os detalhes vM como vmId, sku, nonce, subscriçãoId, timeStamp para criação e expiração do documento e informações do plano sobre a imagem. A informação do plano é apenas povoada para imagens do Azure Marketplace. O certificado pode ser extraído da resposta e utilizado para validar que a resposta é válida e vem do Azure.

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>Amostra 2: Validando que o VM está em execução em Azure

Os fornecedores de marketplace querem garantir que o seu software está licenciado para funcionar apenas no Azure. Se alguém copia o VHD para o local, então deve ter a capacidade de detetar isso. Ao ligar para o Serviço de Metadados de Instância, os fornecedores do Marketplace podem obter dados assinados que garantam a resposta apenas do Azure.

> [!NOTE]
> Requer a instalação do JQ.

***Pedir***

```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Resposta**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

Dados | Descrição
-----|------------
nonce | O utilizador forneceu uma corda opcional com o pedido. Se nenhum nonce foi fornecido no pedido, o atual carimbo de tempo UTC é devolvido
plano | [Plano](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) para um VM na sua Imagem de Mercado Azure, contém nome, produto e editor
carimbo de tempo/criadoOn | O carimbo de tempo UTC em que o primeiro documento assinado foi criado
carimbo de tempo/expiraçãoOn | A marca de tempo UTC em que o documento assinado expira
vmId |  [Identificador único](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para o VM
subscriptionId | Assinatura Azure para a Máquina Virtual, introduzida em`2019-04-30`
sku | SKU específico para a imagem VM, introduzido em`2019-11-01`

#### <a name="sample-3-verifying-the-signature"></a>Amostra 3: Verificar a assinatura

Assim que tiver a assinatura acima, pode verificar se a assinatura é da Microsoft. Também pode verificar o certificado intermédio e a cadeia de certificados. Por último, pode verificar se o ID de subscrição está correto.

> [!NOTE]
> O certificado para nuvem pública e nuvem soberana será diferente.

 Nuvem | Certificado
---------|-----------------
[Todas as regiões globais de Azure geralmente disponíveis](https://azure.microsoft.com/regions/)     | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | *.metadata.azure.cn
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | *.metadata.microsoftazure.de

Há uma emissão conhecida em torno do certificado usado para assinar. Os certificados podem não ter uma correspondência exata `metadata.azure.com` para a nuvem pública. Por conseguinte, a validação da certificação deve permitir um nome comum de qualquer `.metadata.azure.com` subdomínio.

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

Nos casos em que o certificado intermédio não possa ser descarregado devido a restrições de rede durante a validação, o certificado intermédio pode ser fixado. No entanto, o Azure passará os certificados de acordo com a prática padrão do PKI. Os certificados fixados teriam de ser atualizados quando o capotamento acontecesse. Sempre que estiver prevista uma alteração para atualizar o certificado intermédio, o blog do Azure será atualizado e os clientes do Azure serão notificados. Os certificados intermédios podem ser encontrados [aqui.](https://www.microsoft.com/pki/mscorp/cps/default.htm) Os certificados intermédios para cada uma das regiões podem ser diferentes.

> [!NOTE]
>O certificado intermédio para azure China 21Vianet será da DigiCert Global Root CA em vez de Baltimore.
Além disso, se tivesse fixado os certificados intermédios para a Azure China como parte da alteração da autoridade da cadeia de raízes, os certificados intermédios terão de ser atualizados.

## <a name="managed-identity-via-metadata-service"></a>Identidade Gerida via Serviço de Metadados
O utilizador pode ativar a identidade gerida num VM e, em seguida, aproveitar o Serviço de Metadados de Instância para passar o símbolo para aceder aos serviços Azure. As aplicações que estão a ser utilizadas num VM podem agora solicitar um sinal do ponto final do serviço de metadados de instância seletiva e, em seguida, usar o símbolo para autenticar serviços na nuvem, incluindo o cofre chave.
Para obter passos detalhados para ativar esta funcionalidade, consulte [Adquirir um símbolo de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events-via-metadata-service"></a>Eventos agendados via Serviço de Metadados
Pode obter o estado dos eventos programados através do serviço de metadados, então o utilizador pode especificar um conjunto de ações para executar sobre estes eventos.  Consulte [os Eventos Agendados](scheduled-events.md) para mais detalhes. 

## <a name="regional-availability"></a>Disponibilidade Regional

O serviço está **geralmente disponível** em todas as regiões do Azure. Isto inclui: 
1. [Todas as regiões globais de Azure geralmente disponíveis](https://azure.microsoft.com/regions/)
2. [Azure Government](https://azure.microsoft.com/overview/clouds/government/)  
3. [Azure China 21Vianet](https://www.azure.cn/) 
4. [Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/) 

## <a name="sample-code-in-different-languages"></a>Código da amostra em diferentes idiomas

Amostras de serviço de metadados de chamada utilizando diferentes idiomas dentro do VM:

Idioma | Exemplo
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Ir  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Puppet | https://github.com/keirans/azuremetadata

## <a name="error-and-debugging"></a>Erro e Depuração

Se houver um elemento de dados não encontrado ou um pedido mal formado, o Serviço de Metadados de Instância devolve erros padrão http. Por exemplo:

Código de Estado HTTP | Razão
----------------|-------
200 OK |
400 Mau Pedido | Cabeçalho em falta `Metadata: true` ou falta do formato ao consultar um nó de folha
404 Não Encontrado | O elemento solicitado não existe
Método 405 não permitido | Apenas `GET` os pedidos são suportados
410 Desaparecidos | Retente depois de algum tempo por um máximo de 70 segundos
Demasiados Pedidos 429 | A API suporta atualmente um máximo de 5 consultas por segundo
Erro de serviço 500     | Voltar a tentar depois de algum tempo

### <a name="known-issues-and-faq"></a>Questões conhecidas e FAQ

1. Estou a cometer um `400 Bad Request, Required metadata header not specified` erro. O que é que isto quer dizer?
   * O Serviço de Metadados de Instância exige que o cabeçalho `Metadata: true` seja passado no pedido. Passar este cabeçalho na chamada REST permite o acesso ao Serviço de Metadados de Instância.
2. Porque não estou a receber informações de cálculo para o meu VM?
   * Atualmente, o Serviço de Metadados de Instância apenas suporta casos criados com o Gestor de Recursos Azure. No futuro, poderá ser adicionado apoio aos VMs do Cloud Service.
3. Criei a minha Máquina Virtual através do Azure Resource Manager há uns tempos. Por que não vejo informações de metadados computacionais?
   * Para quaisquer VMs criados após o set 2016, adicione uma [Tag](../../azure-resource-manager/management/tag-resources.md) para começar a ver metadados computacionais. Para VMs mais antigos (criados antes de 2016), adicione/remova extensões ou discos de dados ao VM para atualizar metadados.
4. Não vejo todos os dados povoados para nova versão
   * Para quaisquer VMs criados após o set 2016, adicione uma [Tag](../../azure-resource-manager/management/tag-resources.md) para começar a ver metadados computacionais. Para VMs mais antigos (criados antes de 2016), adicione/remova extensões ou discos de dados ao VM para atualizar metadados.
5. Porque é que estou a cometer o `500 Internal Server Error` erro?
   * Volte a tentar o seu pedido com base no sistema de back off exponencial. Se o problema persistir, contacte o suporte do Azure.
6. Onde partilho perguntas/comentários adicionais?
   * Envie os seus comentários em https://feedback.azure.com .
7. Isto funcionaria para a instância de conjunto de escala de máquina virtual?
   * Sim O serviço de metadados está disponível para instâncias de conjunto de escala.
8. Como posso obter apoio para o serviço?
   * Para obter suporte para o serviço, crie um problema de suporte no portal Azure para o VM onde não é capaz de obter resposta de metadados após longas tentativas.
9. Tenho um pedido cronometrado para a minha chamada para o serviço?
   * As chamadas de metadados devem ser feitas a partir do endereço IP primário atribuído ao cartão de rede primário do VM, além de ter alterado as suas rotas, deve haver uma rota para 169.254.0.0/16 endereço fora do seu cartão de rede.
10. Atualizei as minhas etiquetas em conjunto de máquinas virtuais, mas não aparecem em casos diferentes dos VMs?
    * Atualmente, as tags ScaleSets apenas mostram ao VM uma reinicialização/reimagem/ou uma alteração do disco na instância.

## <a name="support-and-feedback"></a>Apoio e Feedback

Envie o seu feedback e comentários sobre https://feedback.azure.com .
Para obter suporte para o serviço, crie um problema de suporte no portal Azure para o VM onde não é capaz de obter resposta de metadados após longas tentativas.

![Suporte de metadados de instância](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre:
1.  [Adquira um símbolo](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)de acesso para o VM .
2.  [Eventos Agendados](scheduled-events.md) 