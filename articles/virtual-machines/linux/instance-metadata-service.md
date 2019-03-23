---
title: Serviço de metadados de instância do Azure | Documentos da Microsoft
description: Interface rESTful para obter informações sobre computação, rede e eventos de manutenção futura da VM do Linux.
services: virtual-machines-linux
documentationcenter: ''
author: KumariSupriya
manager: harijayms
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/15/2019
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: c38b21d860e25c0f31122e75d822257e14ca01db
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351971"
---
# <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure

O serviço de metadados de instância do Azure fornece informações sobre as instâncias de máquina virtual que podem ser utilizadas para gerir e configurar as suas máquinas virtuais em execução.
Isto inclui informações como SKU, configuração de rede e eventos de manutenção futura. Para obter mais informações sobre o tipo de informações está disponível, consulte [categorias de metadados](#instance-metadata-data-categories).

Serviço de metadados de instância do Azure é um ponto final REST acessível para as VMs de IaaS criadas através do [do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).
O ponto final está disponível num endereço IP bem conhecido não encaminháveis internos (`169.254.169.254`) que podem ser acedidos apenas a partir de dentro da VM.

> [!IMPORTANT]
> Este serviço é **em disponibilidade geral** nas regiões do Azure.  Regularmente, ele recebe atualizações para expor as novas informações sobre as instâncias de máquina virtual. Esta página reflete a atualizados [categorias de dados](#instance-metadata-data-categories) disponíveis.

## <a name="service-availability"></a>Disponibilidade do serviço

O serviço está disponível em regiões do Azure em disponibilidade geral. Versão de API nem todos os poderão estar disponíveis em todas as regiões do Azure.

Regiões                                        | Disponibilidade?                                 | Versões Suportadas
-----------------------------------------------|-----------------------------------------------|-----------------
[Todas as regiões do Azure Global disponíveis em geral](https://azure.microsoft.com/regions/)     | Disponível em Geral   | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Disponível em Geral | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure China](https://www.azure.cn/)                                                           | Disponível em Geral | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[O Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | Disponível em Geral | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01

Esta tabela é atualizada quando existem atualizações de serviço e ou existem novas versões suportadas.

Para experimentar o serviço de metadados de instância, criar uma VM a partir [do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) ou o [portal do Azure](https://portal.azure.com) nas regiões acima e siga os exemplos abaixo.

## <a name="usage"></a>Utilização

### <a name="versioning"></a>Controlo de versões

O serviço de metadados de instância tem a mesma versão. Versões são obrigatórias e a versão atual no Global Azure é `2018-10-01`. Versões suportadas atuais são (2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01).

Como são adicionadas novas versões, as versões mais antigas podem ainda ser acedidas para compatibilidade, se seus scripts têm dependências em formatos de dados específicos.

Quando não é especificada nenhuma versão, é devolvido um erro com uma lista das versões suportadas do mais recentes.

> [!NOTE] 
> A resposta é uma cadeia de caracteres do JSON. A resposta de exemplo seguinte é impresso bastante para facilitar a leitura.

**Pedido**

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

### <a name="using-headers"></a>O uso de cabeçalhos

Quando consulta o serviço de metadados de instância, tem de fornecer o cabeçalho `Metadata: true` para garantir que o pedido não foi redirecionado inadvertidamente.

### <a name="retrieving-metadata"></a>Obter metadados

Metadados de instância estão disponível para executar VMs criados/geridos usando [do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Aceda a todas as categorias de dados para uma instância de máquina virtual com o pedido seguinte:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Todas as consultas de metadados de instância diferenciam maiúsculas de minúsculas.

### <a name="data-output"></a>Saída de dados
Por predefinição, o serviço de metadados de instância devolve dados no formato JSON (`Content-Type: application/json`). No entanto, diferentes APIs devolvem dados em diferentes formatos se solicitado.
A tabela seguinte é uma referência de outros formatos de dados que deve dar suporte a APIs.

API | Formato de dados padrão | Outros formatos
--------|---------------------|--------------
/instance | json | texto
/scheduledevents | json | nenhum
/attested | json | nenhum

Para aceder a um formato de resposta não predefinido, especifique o formato de pedido como um parâmetro de cadeia de consulta no pedido. Por exemplo:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>Segurança

O ponto de extremidade do serviço de metadados de instância é acessível apenas a partir de dentro da instância de máquina virtual em execução num endereço IP não encaminháveis internos. Além disso, qualquer pedido com um `X-Forwarded-For` cabeçalho é rejeitado pelo serviço.
Pedidos também tem de conter um `Metadata: true` cabeçalho para se certificar de que o pedido real foi diretamente previstas e não uma parte de redirecionamento não intencional.

### <a name="error"></a>Erro

Se houver um elemento de dados não foi encontrado ou um pedido com formato incorreto, o serviço de metadados de instância devolve erros de HTTP padrão. Por exemplo:

Código de estado de HTTP | Razão
----------------|-------
200 OK |
400 pedido inválido | Em falta `Metadata: true` cabeçalho
404 Não Encontrado | O elemento solicitado não existe 
405 Método não permitido | Apenas `GET` e `POST` os pedidos são suportados
429 demasiados pedidos | Atualmente, a API suporta um máximo de 5 consultas por segundo
Erro de serviço 500     | Tente novamente após algum tempo

### <a name="examples"></a>Exemplos

> [!NOTE]
> Todas as respostas de API são cadeias de caracteres do JSON. Todas as respostas de exemplo seguintes são bastante impresso para facilitar a leitura.

#### <a name="retrieving-network-information"></a>A obter informações de rede

**Pedido**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres do JSON. A resposta de exemplo seguinte é impresso bastante para facilitar a leitura.

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

#### <a name="retrieving-public-ip-address"></a>A obter o endereço IP público

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Obter todos os metadados para uma instância

**Pedido**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres do JSON. A resposta de exemplo seguinte é impresso bastante para facilitar a leitura.

```json
{
  "compute": {
    "azEnvironment": "AZUREPUBLICCLOUD",
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
    "sku": "5-6",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
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

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Obtenção de metadados na máquina Virtual do Windows

**Pedido**

Metadados de instância podem ser obtido no Windows por meio do utilitário de PowerShell `curl`: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2018-10-01 | select -ExpandProperty Content
```

Ou através do `Invoke-RestMethod` cmdlet:

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2018-10-01 -Method get 
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres do JSON. A resposta de exemplo seguinte é impresso bastante para facilitar a leitura.

```json
{
  "compute": {
    "azEnvironment": "AZUREPUBLICCLOUD",
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
    "sku": "5-6",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
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
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>Categorias de dados de metadados de instância

As seguintes categorias de dados estão disponíveis através do serviço de metadados de instância:

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
azEnvironment | Ambiente do Azure onde a VM está em execução no | 2018-10-01
localização | Região do Azure a VM está em execução | 2017-04-02
nome | Nome da VM | 2017-04-02
oferta | Oferecem informações para a imagem VM. Este valor só é aplicável imagens implementadas a partir da Galeria de imagens do Azure. | 2017-04-02
publicador | Publicador da imagem VM | 2017-04-02
sku | SKU específica para a imagem VM | 2017-04-02
versão | Versão da imagem VM | 2017-04-02
osType | Linux ou Windows | 2017-04-02
platformUpdateDomain |  [Domínio de atualização](manage-availability.md) a VM está em execução | 2017-04-02
platformFaultDomain | [Domínio de falha](manage-availability.md) a VM está em execução | 2017-04-02
vmId | [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a VM | 2017-04-02
vmSize | [Tamanho da VM](sizes.md) | 2017-04-02
subscriptionId | Subscrição do Azure para a Máquina Virtual | 2017-08-01
etiquetas | [Etiquetas](../../azure-resource-manager/resource-group-using-tags.md) para a Máquina Virtual  | 2017-08-01
resourceGroupName | [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md) para a Máquina Virtual | 2017-08-01
placementGroupId | [Grupo de colocação](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) de dimensionamento de máquinas virtuais definido | 2017-08-01
plano | [Planear](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) para uma VM no seu uma imagem de mercado do Azure, contém o nome, produto e fabricante | 2018-04-02
fornecedor | Fornecedor da VM | 2018-10-01
publicKeys | [Coleção de chaves públicas](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) atribuído à VM e caminhos | 2018-04-02
vmScaleSetName | [Nome do conjunto de dimensionamento de máquina virtual](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) de dimensionamento de máquinas virtuais definido | 2017-12-01
zona | [Zona de disponibilidade](../../availability-zones/az-overview.md) da sua máquina virtual | 2017-12-01
ipv4/privateIpAddress | Endereço IPv4 local da VM | 2017-04-02
ipv4/publicIpAddress | Endereço IPv4 público da VM | 2017-04-02
subnet/address | Endereço de sub-rede da VM | 2017-04-02
subnet/prefix | Prefixo de sub-rede, 24 de exemplo | 2017-04-02
ipv6/ipAddress | Endereço IPv6 local da VM | 2017-04-02
macAddress | Endereço mac VM | 2017-04-02
scheduledevents | Consulte [eventos agendados](scheduled-events.md) | 2017-08-01
identidade | Identidades geridas para recursos do Azure. Consulte [adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
atestado | Consulte [atestado dados](#attested-data) | 2018-10-01

## <a name="attested-data"></a>Dados do atestado

Metadados de instância responde no ponto final de http no 169.254.169.254. Parte do cenário servido pelo serviço de metadados de instância é fornecer garantias de que os dados respondidos é proveniente do Azure. Podemos assinar parte destas informações para que as imagens do marketplace podem ter a certeza de que é a imagem em execução no Azure.

### <a name="example-attested-data"></a>Dados de atestado de exemplo

 > [!NOTE]
> Todas as respostas de API são cadeias de caracteres do JSON. As respostas de exemplo seguintes são bastante impresso para facilitar a leitura.

 **Pedido**


 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

API-version é um campo obrigatório e a versão suportada para os dados de atestado é 2018-10-01.
Valor de uso único é uma cadeia de 10 dígitos opcional fornecida. Valor de uso único pode ser utilizado para controlar o pedido e se não for indicado, na cadeia de caracteres de resposta com codificação a UTC atual timestamp é retornada.

 **Resposta**
> [!NOTE]
> A resposta é uma cadeia de caracteres do JSON. A resposta de exemplo seguinte é impresso bastante para facilitar a leitura.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

 > O blob de assinatura é uma [pkcs7](https://aka.ms/pkcs7) assinado a versão do documento. Ela contém o certificado utilizado para assinar juntamente com os detalhes da VM como vmId, nonce, timeStamp para criação e expiração de documento e as informações de plano sobre a imagem. Apenas são preenchidas as informações de plano para imagens do local de mercado do Azure. O certificado pode ser extraído da resposta e utilizado para validar que a resposta é válida e estará disponível a partir do Azure.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Obtenção de metadados de atestado na máquina Virtual do Windows

 **Pedido**

Metadados de instância podem ser obtido no Windows por meio do utilitário de PowerShell `curl`:

 ```bash
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Ou através do `Invoke-RestMethod` cmdlet:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

API-version é um campo obrigatório e a versão suportada para os dados de atestado é 2018-10-01.
Valor de uso único é uma cadeia de 10 dígitos opcional fornecida. Valor de uso único pode ser utilizado para controlar o pedido e se não for indicado, na cadeia de caracteres de resposta com codificação a UTC atual timestamp é retornada.

 **Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres do JSON. A resposta de exemplo seguinte é impresso bastante para facilitar a leitura.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> O blob de assinatura é uma [pkcs7](https://aka.ms/pkcs7) assinado a versão do documento. Ela contém o certificado utilizado para assinar juntamente com os detalhes da VM como vmId, nonce, timeStamp para criação e expiração de documento e as informações de plano sobre a imagem. Apenas são preenchidas as informações de plano para imagens do local de mercado do Azure. O certificado pode ser extraído da resposta e utilizado para validar que a resposta é válida e estará disponível a partir do Azure.

## <a name="example-scenarios-for-usage"></a>Exemplos de cenários de utilização  

### <a name="tracking-vm-running-on-azure"></a>Acompanhamento de uma VM em execução no Azure

Como um fornecedor de serviços, poderá precisar de controlar o número de VMs que executam o seu software ou agentes que precisa controlar exclusividade da VM. Para conseguir obter um ID exclusivo para uma VM, utilize o `vmId` campo do serviço de metadados de instância.

**Pedido**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Resposta**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Colocação de contentores, domínio de atualização/falha baseado em partições de dados

Para determinados cenários, a colocação das réplicas de dados diferentes é de importância fortes. Por exemplo, [colocação de réplica do HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou o posicionamento de contentor através de um [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) podem exigir a saber o `platformFaultDomain` e `platformUpdateDomain` a VM está em execução no.
Também pode utilizar [zonas de disponibilidade](../../availability-zones/az-overview.md) para as instâncias tomar essas decisões.
Pode consultar estes dados diretamente através do serviço de metadados de instância.

**Pedido**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Resposta**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Obter mais informações sobre a VM durante o pedido de suporte

Como um fornecedor de serviços, poderá receber uma chamada de suporte local em que deseja saber mais informações sobre a VM. Pedir o cliente para partilhar os metadados de computação pode fornecer informações básicas para o suporte profissional para saber sobre o tipo de VM no Azure.

**Pedido**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres do JSON. A resposta de exemplo seguinte é impresso bastante para facilitar a leitura.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Obter o Ambiente do Azure no qual a VM está em execução

O Azure tem várias clouds soberanas, como [do Azure Government](https://azure.microsoft.com/overview/clouds/government/). Às vezes precisa tomar algumas decisões de tempo de execução do ambiente do Azure. O exemplo a seguir mostra como é possível obter este comportamento.

**Pedido**
``` bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Resposta**
```
AZUREPUBLICCLOUD
```

### <a name="validating-that-the-vm-is-running-in-azure"></a>Confirmar se a VM está a ser executada no Azure

 Fornecedores de Marketplace querem garantir que seu software está licenciado para executar apenas no Azure. Se alguém copia o VHD para internos, em seguida, eles devem ter uma forma de detetar que. Chamando o serviço de metadados de instância, fornecedores de Marketplace podem obter dados assinado que garante a resposta apenas a partir do Azure.
 **Pedido**
 > [!NOTE]
> Requer jq a serem instalados.

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2018-10-01 | jq -r '.["signature"]' > signature
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
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34"
}
```

Dados | Descrição
-----|------------
Valor de uso único | Cadeia de caracteres opcional com o pedido fornecido pelo utilizador. Se nenhum valor de uso único foi fornecido no pedido, o carimbo de hora UTC atual é devolvido
plano | [Planear](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) para uma VM na mesma é uma imagem do Azure Marketplace, contém o nome, produto e fabricante
timestamp/createdOn | O carimbo de hora em que o primeiro documento assinado foi criado
timestamp/expiresOn | O carimbo de hora em que o documento assinado expira
vmId |  [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a VM

#### <a name="verifying-the-signature"></a>A verificação da assinatura

Depois de obter a assinatura acima, pode verificar que a assinatura é da Microsoft. Também pode verificar o certificado intermédio e a cadeia de certificados.

> [!NOTE]
> O certificado para a cloud pública e cloud soberana será diferente.

 Regiões | Certificado
---------|-----------------
[Todas as regiões do Azure Global disponíveis em geral](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure China](https://www.azure.cn/)                                                           | metadata.azure.cn
[O Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```
### <a name="failover-clustering-in-windows-server"></a>Cluster de failover do Windows Server

Para determinados cenários, ao consultar o serviço de metadados de instância com o Clustering de ativação pós-falha, é necessário adicionar uma rota à tabela de roteamento.

1. Abra a linha de comandos com privilégios de administrador.

2. Execute o seguinte comando e tome nota do endereço da Interface de rede de destino (`0.0.0.0`) na tabela de rotas de IPv4.

```bat
route print
```

> [!NOTE] 
> A seguinte saída de exemplo de uma VM do Windows Server com o Cluster de ativação pós-falha ativados contém apenas a tabela de rotas IPv4 para manter a simplicidade.

```bat
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
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. Execute o comando seguinte e utilize o endereço da Interface de rede de destino (`0.0.0.0`) que é (`10.0.1.10`) neste exemplo.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Exemplos de chamar o serviço de metadados que utilizam idiomas diferentes dentro da VM

Idioma | Exemplo
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Ir  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>FAQ

1. Estou recebendo o erro `400 Bad Request, Required metadata header not specified`. O que é que isto quer dizer?
   * O serviço de metadados de instância requer o cabeçalho `Metadata: true` a serem passados no pedido. Passando esse cabeçalho a chamada REST permite o acesso ao serviço de metadados de instância.
2. Por que eu não estou obtendo informações de computação para a minha VM?
   * Atualmente, o serviço de metadados de instância só suporta instâncias criadas com o Azure Resource Manager. No futuro, o suporte para as VMs do serviço em nuvem podem ser adicionadas.
3. Tempo que criei minha máquina Virtual através do Gestor de recursos do Azure. Por que ainda não estou ver informações de metadados de computação?
   * Para todas as VMs criadas depois de Setembro de 2016, adicione uma [marca](../../azure-resource-manager/resource-group-using-tags.md) para começar a ver metadados de computação. Para VMs anteriores (criadas antes de Setembro de 2016), adicionar ou remover discos de dados ou extensões para a VM para atualizar os metadados.
4. Não estou a ver todos os dados preenchidos para a nova versão
   * Para todas as VMs criadas depois de Setembro de 2016, adicione uma [marca](../../azure-resource-manager/resource-group-using-tags.md) para começar a ver metadados de computação. Para VMs anteriores (criadas antes de Setembro de 2016), adicionar ou remover discos de dados ou extensões para a VM para atualizar os metadados.
5. Por que estou recebendo o erro `500 Internal Server Error`?
   * Repetir o pedido com base no sistema de término exponencial. Se o problema persistir, contacte o suporte do Azure.
6. Onde posso partilhar comentários/perguntas adicionais?
   * Envie seus comentários acerca https://feedback.azure.com.
7. Funciona para a instância de conjunto de dimensionamento de Máquina Virtual?
   * Sim do serviço de metadados está disponível para instâncias de conjunto de dimensionamento.
8. Como posso obter suporte para o serviço?
   * Para obter suporte para o serviço, crie um problema de suporte no portal do Azure para a VM em que não é possível obter a resposta de metadados depois de tentativas de longo.
9. Posso obter o pedido excedeu o tempo limite para a chamada para o serviço?
   * Chamadas de metadados têm de ser efetuadas do endereço IP primário atribuído à placa de rede da VM, além no caso de ter alterado as rotas aqui tem de ser uma rota para o endereço de 169.254.0.0/16 fora de sua placa de rede.
10. Atualizei meu etiquetas no conjunto de dimensionamento de máquina virtual, mas não são apresentados nas instâncias ao contrário de VMs?
    * Atualmente para ScaleSets etiquetas mostram apenas para a VM em reinício/recriação de imagem/ou um disco alterar para a instância.

    ![Suporte de metadados de instância](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [eventos agendados](scheduled-events.md)
