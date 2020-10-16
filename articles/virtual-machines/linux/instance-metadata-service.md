---
title: Azure Instance Metadata Service
description: Saiba mais sobre o Serviço de Metadados Azure Instance e como fornece informações sobre as instâncias de máquina virtual atualmente em execução.
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
ms.openlocfilehash: ffa9502a42af9e927f82d7a135473ff702b76577
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970712"
---
# <a name="azure-instance-metadata-service-imds"></a>Serviço de metadados de caso Azure (IMDs)

O Azure Instance Medata Service (IMDS) fornece informações sobre casos de máquinas virtuais atualmente em funcionamento e pode ser usado para gerir e configurar as suas máquinas virtuais.
Esta informação inclui o SKU, armazenamento, configurações de rede e eventos de manutenção futuros. Para obter uma lista completa dos dados disponíveis, consulte [as APIs de metadados](#metadata-apis).
O Serviço de Metadados de Exemplo está disponível para executar casos de conjunto de máquinas virtuais e de escala de máquina virtual. Todos os APIs suportam VMs criados/geridos usando [o Azure Resource Manager](/rest/api/resources/). Apenas os pontos finais attestados e de rede suportam VMs clássicos (não-ARM) e Attested fá-lo apenas em um grau limitado.

O IMDS do Azure é um ponto final de REST que está disponível num endereço IP não-roteado bem conhecido ( `169.254.169.254` ), que só pode ser acedido a partir do VM. A comunicação entre o VM e o IMDS nunca sai do Hospedeiro.
É prática ser prática ter os seus clientes HTTP a contornar os proxies web dentro do VM ao consultar o IMDS e tratar `169.254.169.254` o mesmo que [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Segurança

O ponto final do Serviço de Metadados de Instância só está acessível a partir da caixa da máquina virtual em funcionamento num endereço IP não roteado. Além disso, qualquer pedido com `X-Forwarded-For` cabeçalho é rejeitado pelo serviço.
Os pedidos devem igualmente conter um `Metadata: true` cabeçalho para garantir que o pedido real foi diretamente pretendido e não uma parte da reorientação não intencional.

> [!IMPORTANT]
> O Serviço de Metadados de Exemplo não é um canal para dados sensíveis. O ponto final está aberto a todos os processos no VM. As informações expostas através deste serviço devem ser consideradas como informações partilhadas a todas as aplicações que estão dentro do VM.

## <a name="usage"></a>Usage

### <a name="accessing-azure-instance-metadata-service"></a>Aceder ao Serviço de Metadados de Exemplo de Azure

Para aceder ao Serviço de Metadados de Exemplo, crie um VM do [Azure Resource Manager](/rest/api/resources/) ou do [portal Azure](https://portal.azure.com), e siga as amostras abaixo.
Mais exemplos de como consultar o IMDS podem ser encontrados em [amostras de metadados de instância Azure](https://github.com/microsoft/azureimds).

Abaixo está o código de amostra para recuperar todos os metadados para um exemplo, para aceder a fonte de dados específica, ver secção [API de metadados.](#metadata-apis) 

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-06-01"
```

**Response**

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo a seguir é bastante impressa para a legibilidade.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
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
        "resourceId": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
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
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
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
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
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
        "subscriptionId": "8d10da13-8125-4ba9-a717-bf7490507b3d",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    }
}
```

### <a name="data-output"></a>Saída de dados

Por predefinição, o Serviço de Metadados de Exemplo retorna os dados em formato JSON `Content-Type: application/json` (). No entanto, algumas APIs são capazes de devolver dados em diferentes formatos, se solicitado.
A tabela a seguir é uma referência de outros formatos de dados que as APIs podem suportar.

API | Formato de dados predefinidos | Outros Formatos
--------|---------------------|--------------
/atestado | json | nenhuma
/identidade | json | nenhuma
/instância | json | texto
/horários eventos | json | nenhuma

Para aceder a um formato de resposta não padrão, especifique o formato solicitado como um parâmetro de cadeia de consulta no pedido. Por exemplo:

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Para os nós de folhas em /metadados/exemplo, o `format=json` não funciona. Para estas `format=text` consultas, é necessário especificar explicitamente, uma vez que o formato padrão é json.

### <a name="versioning"></a>Controlo de versões

O Serviço de Metadados de Exemplo é versão e especificar a versão API no pedido HTTP é obrigatório.

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

Note que quando a nova versão for lançada, levará algum tempo a chegar a todas as regiões.

À medida que as versões mais recentes são adicionadas, as versões mais antigas ainda podem ser acedidas para compatibilidade se os seus scripts tiverem dependências de formatos de dados específicos.

Quando nenhuma versão é especificada, um erro é devolvido com uma lista das versões mais recentes suportadas.

> [!NOTE]
> A resposta é uma corda JSON. O exemplo a seguir indica a condição de erro quando a versão não é especificada, a resposta é bastante impressa para a legibilidade.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance"
```

**Response**

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

O Serviço de Metadados contém várias APIs representando diferentes fontes de dados.

API | Descrição | Versão introduzida
----|-------------|-----------------------
/atestado | Ver [Dados Attestados](#attested-data) | 2018-10-01
/identidade | Ver [Adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instância | Ver [Exemplo API](#instance-api) | 2017-04-02
/horários eventos | Ver [Eventos Agendados](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>API de exemplo

A API de caso expõe os metadados importantes para as instâncias VM, incluindo o VM, a rede e o armazenamento. As seguintes categorias podem ser acedidas através de exemplo/cálculo:

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
azEnvironment | Ambiente azul onde o VM está em execução | 2018-10-01
customData | Esta funcionalidade encontra-se atualmente desativada. Atualizaremos esta documentação quando estiver disponível | 2019-02-01
isHostCompatibilityLayerVm | Identifica se o VM funciona na Camada de Compatibilidade do Anfitrião | 2020-06-01
localização | Região de Azure o VM está em execução | 2017-04-02
name | Nome do VM | 2017-04-02
oferta | Oferecer informações para a imagem VM e só está presente para imagens implementadas a partir da galeria de imagens Azure | 2017-04-02
osTipos | Linux ou Windows | 2017-04-02
placementGroupId | Grupo de [colocação](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) do seu conjunto de escala de máquina virtual | 2017-08-01
plano | [Plano](/rest/api/compute/virtualmachines/createorupdate#plan) contendo nome, produto e editor para um VM se for uma Imagem de Mercado Azure | 2018-04-02
plataformaUpdateDomain |  [Atualização](../manage-availability.md) do domínio em que o VM está a funcionar | 2017-04-02
plataformaFaultDomain | [Domínio de avaria](../manage-availability.md) em que o VM está a funcionar | 2017-04-02
provedor | Provedor do VM | 2018-10-01
publicKeys | [Coleção de Chaves Públicas](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) atribuídas ao VM e caminhos | 2018-04-02
publicador | Editor da imagem VM | 2017-04-02
resourceGroupName | [Grupo de recursos](../../azure-resource-manager/management/overview.md) para a sua Máquina Virtual | 2017-08-01
resourceId | O ID [totalmente qualificado](/rest/api/resources/resources/getbyid) do recurso | 2019-03-11
sku | SKU específico para a imagem VM | 2017-04-02
securityProfile.secureBootEnabled | Identifica se a bota segura UEFI está ativada no VM | 2020-06-01
securityProfile.virtualTpmEnabled | Identifica se o Módulo de Plataforma Fidedigna Virtual (TPM) está ativado no VM | 2020-06-01
armazenamentoProfile | Ver [Perfil de Armazenamento](#storage-metadata) | 2019-06-01
subscriptionId | Assinatura Azure para a Máquina Virtual | 2017-08-01
etiquetas | [Etiquetas](../../azure-resource-manager/management/tag-resources.md) para a sua Máquina Virtual  | 2017-08-01
tagsList | Tags formatadas como uma matriz JSON para uma análise programática mais fácil  | 2019-06-04
versão | Versão da imagem VM | 2017-04-02
vmId | [Identificador único](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para o VM | 2017-04-02
vmScaleSetName | [Conjunto de escala de máquina virtual Nome](../../virtual-machine-scale-sets/overview.md) do seu conjunto de escala de máquina virtual | 2017-12-01
vmSize | [Tamanho VM](../sizes.md) | 2017-04-02
zona | [Zona de disponibilidade](../../availability-zones/az-overview.md) da sua máquina virtual | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>Amostra 1: VM de rastreio em execução em Azure

Como prestador de serviços, poderá ser necessário rastrear o número de VMs que executam o seu software ou ter agentes que precisam de rastrear a singularidade do VM. Para conseguir obter um ID único para um VM, utilize o `vmId` campo do Serviço de Metadados de Exemplo.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Response**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>Amostra 2: Colocação de contentores, domínio de falha/atualização baseado em partículas de dados

Para certos cenários, a colocação de diferentes réplicas de dados é de primordial importância. Por exemplo, a colocação de [réplicas HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou a colocação de contentores através de um [orquestrador](https://kubernetes.io/docs/user-guide/node-selection/) pode necessitar de saber o `platformFaultDomain` que o `platformUpdateDomain` VM está a funcionar.
Também pode utilizar [Zonas de Disponibilidade](../../availability-zones/az-overview.md) para as instâncias para tomar estas decisões.
Pode consultar estes dados diretamente através do Serviço de Metadados de Exemplo.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Response**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>Amostra 3: Obter mais informações sobre o VM durante o caso de apoio

Como prestador de serviços, poderá receber uma chamada de apoio onde gostaria de saber mais informações sobre o VM. Pedir ao cliente que partilhe os metadados do cálculo pode fornecer informações básicas para que o profissional de suporte saiba sobre o tipo de VM no Azure.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Response**

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo a seguir é bastante impressa para a legibilidade.

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

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>Amostra 4: Obter Ambiente Azure onde o VM está em execução

Azure tem várias nuvens soberanas como [o Governo de Azure.](https://azure.microsoft.com/overview/clouds/government/) Às vezes é preciso o Ambiente Azure para tomar algumas decisões de tempo de execução. A amostra que se segue mostra como pode alcançar este comportamento.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Response**

```text
AzurePublicCloud
```

A nuvem e os valores do Ambiente Azure estão listados abaixo.

 Cloud   | Ambiente Azure
---------|-----------------
[Todas as regiões de Azure Global Geralmente disponíveis](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureusGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Metudatas de rede 

Os metadados de rede fazem parte do caso API. As seguintes categorias de Rede estão disponíveis através do ponto final de instância/rede.

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
ipv4/privateIpAddress | Endereço IPv4 local do VM | 2017-04-02
ipv4/publicIpAddress | Endereço IPv4 público do VM | 2017-04-02
sub-rede/endereço | Endereço de sub-rede do VM | 2017-04-02
sub-rede/prefixo | Prefixo de sub-rede, exemplo 24 | 2017-04-02
ipv6/ipAddress | Endereço IPv6 local do VM | 2017-04-02
macAddress | Endereço de mac VM | 2017-04-02

> [!NOTE]
> Todas as respostas da API são cordas JSON. Todas as respostas de exemplo são bastante impressas para a legibilidade.

#### <a name="sample-1-retrieving-network-information"></a>Amostra 1: Recuperar informações sobre a rede

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Response**

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo a seguir é bastante impressa para a legibilidade.

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
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Metudatas de armazenamento

Os metadados de armazenamento fazem parte do caso API em instância/computação/armazenamentoProfile ponto final.
Fornece detalhes sobre os discos de armazenamento associados ao VM. 

O perfil de armazenamento de um VM é dividido em três categorias: referência de imagem, disco de SO e discos de dados.

O objeto de referência de imagem contém as seguintes informações sobre a imagem do SO:

Dados    | Descrição
--------|-----------------
ID      | ID do Recurso
oferta   | Oferta da plataforma ou imagem de mercado
publicador | Editor de imagem
sku     | Sku de imagem
versão | Versão da plataforma ou imagem do mercado

O objeto do disco OS contém as seguintes informações sobre o disco de oss utilizado pelo VM:

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
writeAcceleratorEnabled | Se escrever Ou não OAccelerador está ativado no disco

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
osTipos  | Tipo de SO incluído no disco
vhd     | Disco rígido virtual
writeAcceleratorEnabled | Se escrever Ou não OAccelerador está ativado no disco

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

As tags VM estão incluídas no ponto final da API de instância/compute/tags.
As etiquetas podem ter sido aplicadas ao seu Azure VM para organizar logicamente uma taxonomia. As etiquetas atribuídas a um VM podem ser recuperadas utilizando o pedido abaixo.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Response**

```text
Department:IT;Environment:Test;Role:WebRole
```

O `tags` campo é uma corda com as etiquetas delimitadas por pontos-e-vírguis. Esta saída pode ser um problema se os e-tím9is forem utilizados nas próprias etiquetas. Se um analisador for escrito para extrair programáticamente as etiquetas, deve confiar no `tagsList` campo. O `tagsList` campo é uma matriz JSON sem delimiters, e consequentemente, mais fácil de analisar.

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

## <a name="attested-data"></a>Dados Atestados

Parte do cenário servido pelo Serviço de Metadados de Exemplo é fornecer garantias de que os dados fornecidos são provenientes do Azure. Assinamos parte desta informação para que as imagens do mercado possam ter a certeza de que é a sua imagem a correr no Azure.

### <a name="sample-1-getting-attested-data"></a>Amostra 1: Obtenção de dados atestados

> [!NOTE]
> Todas as respostas da API são cordas JSON. As respostas de exemplo a seguir são bastante impressas para a legibilidade.

**Pedir**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

A versão Api é um campo obrigatório. Consulte a [secção de utilização](#usage) para versões API suportadas.
Nonce é uma corda opcional de 10 dígitos. Se não for fornecida, o IMDS devolve o atual calendário utc no seu lugar.

> [!NOTE]
> Devido ao mecanismo de cache do IMDS, pode ser devolvido um valor de nonce previamente cached.

**Response**

> [!NOTE]
> A resposta é uma corda JSON. A resposta de exemplo a seguir é bastante impressa para a legibilidade.

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

A bolha de assinatura é uma versão assinada por [pkcs7](https://aka.ms/pkcs7) do documento. Contém o certificado utilizado para a assinatura juntamente com certos detalhes específicos da VM. Para os VMs ARM, isto inclui vmId, sku, nonce, subscriçãoId, timeStamp para criação e expiração do documento e informação do plano sobre a imagem. A informação do plano só é preenchida para imagens do Azure Marketplace. Para VMs clássicos (não-ARM), apenas o vmId é garantido para ser povoado. O certificado pode ser extraído da resposta e usado para validar que a resposta é válida e vem do Azure.
O documento contém os seguintes campos:

Dados | Descrição
-----|------------
nonce | Uma corda que pode ser opcionalmente fornecida com o pedido. Se não for fornecido nenhum nonce, o atual calendário UTC é usado
plano | O [plano Azure Marketplace Image](/rest/api/compute/virtualmachines/createorupdate#plan). Contém o id do plano (nome), imagem ou oferta do produto (produto) e id editor (editor).
timestamp/createdOn | O calendário utc para quando o documento assinado foi criado
timetamp/expiraOn | O calendário utc para quando o documento assinado expirar
vmId |  [Identificador único](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para o VM
subscriptionId | Assinatura Azure para a Máquina Virtual, introduzida em `2019-04-30`
sku | SKU específico para a imagem VM, introduzido em `2019-11-01`

> [!NOTE]
> Para VMs clássicos (não-ARM), apenas o vmId é garantido para ser povoado.

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>Amostra 2: Validação que o VM está em execução em Azure

Os fornecedores de marketplace querem garantir que o seu software está licenciado para funcionar apenas em Azure. Se alguém copia o VHD para o local, então deve ter a capacidade de detetar isso. Ao ligar para o Serviço de Metadados de Exemplo, os fornecedores do Marketplace podem obter dados assinados que garantem a resposta apenas a partir do Azure.

> [!NOTE]
> Requer que o JQ seja instalado.

**Pedir**

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2019-04-30" | jq -r '.["signature"]' > signature
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
> Devido ao mecanismo de cache do IMDS, pode ser devolvido um valor de nonce previamente cached.

A nonce no documento assinado pode ser comparada se tiver fornecido um parâmetro de nonce no pedido inicial.

> [!NOTE]
> O certificado para nuvem pública e nuvem soberana será diferente.

Cloud | Certificado
------|------------
[Todas as regiões de Azure Global Geralmente disponíveis](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> Há uma questão conhecida em torno do certificado usado para a assinatura. Os certificados podem não ter uma correspondência exata de `metadata.azure.com` nuvem pública. Por conseguinte, a validação da certificação deve permitir um nome comum a partir de qualquer `.metadata.azure.com` subdomínio.

Nos casos em que o certificado intermédio não possa ser descarregado devido a restrições de rede durante a validação, o certificado intermédio pode ser fixado. No entanto, o Azure irá reverter os certificados de acordo com a prática padrão do PKI. Os certificados fixados teriam de ser atualizados quando a capotamento acontecesse. Sempre que for planeada uma alteração para atualizar o certificado intermédio, o blog Azure será atualizado e os clientes Azure serão notificados. Os certificados intermédios podem ser [consultados aqui.](https://www.microsoft.com/pki/mscorp/cps/default.htm) Os certificados intermédios para cada uma das regiões podem ser diferentes.

> [!NOTE]
> O certificado intermédio para a Azure China 21Vianet será da DigiCert Global Root CA em vez de Baltimore.
Além disso, se tivesse fixado os certificados intermédios para a Azure China como parte da alteração da autoridade da cadeia de raízes, os certificados intermédios terão de ser atualizados.

## <a name="managed-identity-via-metadata-service"></a>Identidade gerida através do Serviço de Metadados

Um sistema de identidade gerida atribuída pode ser ativado no VM ou um ou mais identidades geridas atribuídas pelo utilizador podem ser atribuídas ao VM.
As fichas para identidades geridas podem então ser solicitadas ao Serviço de Metadados de Exemplo. Estes tokens podem ser usados para autenticar com outros serviços Azure, como o Azure Key Vault.

Para obter passos detalhados para ativar esta funcionalidade, consulte [Adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events-via-metadata-service"></a>Eventos Agendados via Serviço de Metadados
Pode obter o estado dos eventos programados através do serviço de metadados, para que o utilizador possa especificar um conjunto de ações para executar sobre estes eventos.  Consulte [os Eventos Agendados](scheduled-events.md) para mais detalhes. 

## <a name="regional-availability"></a>Disponibilidade Regional

O serviço está **geralmente disponível** em todas as Nuvens Azure.

## <a name="sample-code-in-different-languages"></a>Código de amostra em diferentes línguas

Amostras de serviço de metadados de chamada utilizando diferentes idiomas dentro do VM:

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

## <a name="error-and-debugging"></a>Erro e Debugging

Se houver um elemento de dados não encontrado ou um pedido mal formado, o Serviço de Metadados de Instância retorna erros HTTP padrão. Por exemplo:

Código de Estado HTTP | Razão
----------------|-------
200 OK |
400 Pedido Incorreto | Cabeçalho em `Metadata: true` falta ou parâmetro em falta ao consultar um nó de `format=json` folha
404 Não Encontrado | O elemento solicitado não existe
405 Método Não Permitido | Apenas `GET` os pedidos são suportados
410 Desaparecidos | Reda o tempo após algum tempo por um máximo de 70 segundos
Demasiados Pedidos 429 | A API suporta atualmente um máximo de 5 consultas por segundo
Erro de serviço 500     | Redando depois de algum tempo

### <a name="known-issues-and-faq"></a>Questões conhecidas e FAQ

1. Estou a perceber o `400 Bad Request, Required metadata header not specified` erro. O que é que isto quer dizer?
   * O Serviço de Metadados de Exemplo requer que o cabeçalho `Metadata: true` seja passado no pedido. Passar este cabeçalho na chamada REST permite o acesso ao Serviço de Metadados de Exemplo.
1. Porque é que não estou a receber informações sobre o meu VM?
   * Atualmente, o Serviço de Metadados de Exemplo apenas suporta casos criados com o Azure Resource Manager. No futuro, poderá ser adicionado suporte a VMs do Serviço Cloud.
1. Criei a minha Máquina Virtual através do Azure Resource Manager há algum tempo. Por que não vejo informação de metadados computacional?
   * Para quaisquer VMs criados após setembro de 2016, adicione uma [Tag](../../azure-resource-manager/management/tag-resources.md) para começar a ver metadados compute. Para os VMs mais antigos (criados antes de setembro de 2016), adicione/remova extensões ou discos de dados para os(s) instâncias VM(s) para atualizar metadados.
1. Não estou a ver todos os dados preenchidos para a nova versão
   * Para quaisquer VMs criados após setembro de 2016, adicione uma [Tag](../../azure-resource-manager/management/tag-resources.md) para começar a ver metadados compute. Para os VMs mais antigos (criados antes de setembro de 2016), adicione/remova extensões ou discos de dados para os(s) instâncias VM(s) para atualizar metadados.
1. Porque é que estou a cometer o erro `500 Internal Server Error` `410 Resource Gone` ou?
   * Repreita o seu pedido com base no sistema de retrocesso exponencial ou em outros métodos descritos no [manuseamento de falhas transitórias](/azure/architecture/best-practices/transient-faults). Se o problema persistir, crie um problema de suporte no portal Azure para o VM.
1. Isto funcionaria para instâncias de conjunto de escala de máquina virtual?
   * O serviço Yes Metadata está disponível para instâncias de definição de escala.
1. Atualizei as minhas etiquetas em Conjuntos de Escala de Máquina Virtual, mas não aparecem nos casos, ao contrário dos VMs de exemplo único?
   * Atualmente, as tags para Conjuntos de Escala só mostram ao VM num reboot, reimagem ou alteração de disco para a instância.
1. Recebo um pedido cronometrado para a minha chamada para o serviço?
   * As chamadas de metadados devem ser efetuadas a partir do endereço IP primário atribuído ao cartão de rede primário do VM. Além disso, no caso de ter alterado as suas rotas, deve haver uma rota para o endereço 169.254.169.254/32 na tabela de encaminhamento local da sua VM.
   * <details>
        <summary>Verificação da sua mesa de encaminhamento</summary>

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
        1. Confirme que a interface corresponde ao NIC primário do VM e ao IP primário. Pode encontrar o NIC/IP primário olhando para a configuração da rede no Portal Azure ou [olhando-o para cima com o Azure CLI](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-show). Observe os IPs públicos e privados (e o endereço MAC se utilizar o cli). Exemplo do PowerShell CLI:
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
    </details>

## <a name="support-and-feedback"></a>Suporte e Feedback

Envie o seu feedback e comentários sobre https://feedback.azure.com .

Para obter suporte para o serviço, crie um problema de suporte no portal Azure para o VM onde não é capaz de obter resposta de metadados após longas retrações.
Utilize o Tipo de Problema `Management` e selecione `Instance Metadata Service` como categoria.

![Apoio a metrões de exemplo](./media/instance-metadata-service/InstanceMetadata-support.png "Screenshot: Abrir um caso de suporte quando tem problemas com o Serviço de Metadados de Exemplo")

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre:
1. [Adquirir um símbolo de acesso para o VM](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).
1. [Eventos Agendados](scheduled-events.md)