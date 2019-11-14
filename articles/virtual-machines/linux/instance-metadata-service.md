---
title: Serviço de metadados de instância do Azure
description: Interface RESTful para obter informações sobre a computação, a rede e os eventos de manutenção futuros da VM do Linux.
services: virtual-machines-linux
documentationcenter: ''
author: KumariSupriya
manager: harijayms
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: d18822cf7d4827016a55fa1b1a1408a003e933ab
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035985"
---
# <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure

O serviço de metadados de instância do Azure fornece informações sobre a execução de instâncias de máquina virtual que podem ser usadas para gerenciar e configurar suas máquinas virtuais.
Isso inclui informações como SKU, configuração de rede e futuros eventos de manutenção. Para obter mais informações sobre o tipo de informação disponível, consulte [APIs de metadados](#metadata-apis).

O serviço de metadados de instância do Azure é um ponto de extremidade REST acessível a todas as VMs de IaaS criadas por meio do [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).
O ponto de extremidade está disponível em um endereço IP não roteável e conhecido (`169.254.169.254`) que pode ser acessado somente de dentro da VM.

> [!IMPORTANT]
> Esse serviço está **geralmente disponível** em todas as regiões do Azure.  Ele recebe atualizações regularmente para expor novas informações sobre instâncias de máquina virtual. Esta página reflete as [APIs de metadados](#metadata-apis) atualizadas disponíveis.

## <a name="service-availability"></a>Disponibilidade do serviço

O serviço está disponível em regiões do Azure geralmente disponíveis. Nem toda a versão da API pode estar disponível em todas as regiões do Azure.

Regiões                                        | Ininterrupta?                                 | Versões Suportadas
-----------------------------------------------|-----------------------------------------------|-----------------
[Todas as regiões globais do Azure geralmente disponíveis](https://azure.microsoft.com/regions/)     | Disponível em Geral | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Disponível em Geral | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30
[Azure China](https://www.azure.cn/)                                                     | Disponível em Geral | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | Disponível em Geral | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30

Essa tabela é atualizada quando há atualizações de serviço e ou novas versões com suporte estão disponíveis.

Para testar o serviço de metadados de instância, crie uma VM de [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) ou o [portal do Azure](https://portal.azure.com) nas regiões acima e siga os exemplos abaixo.

## <a name="usage"></a>Utilização

### <a name="versioning"></a>Controlo de versões

O serviço de metadados de instância tem controle de versão e a especificação da versão de API na solicitação HTTP é obrigatória.

Você pode ver as versões mais recentes listadas nesta [tabela de disponibilidade](#service-availability).

À medida que versões mais recentes forem adicionadas, as versões mais antigas ainda poderão ser acessadas para compatibilidade se seus scripts tiverem dependências em formatos de dados específicos.

Quando nenhuma versão é especificada, um erro é retornado com uma lista das versões mais recentes com suporte.

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. A resposta de exemplo a seguir é bem impressa para facilitar a leitura.

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

### <a name="using-headers"></a>Usando cabeçalhos

Ao consultar o serviço de metadados de instância, você deve fornecer o `Metadata: true` de cabeçalho para garantir que a solicitação não tenha sido redirecionada de forma não intencional.

### <a name="retrieving-metadata"></a>Recuperando metadados

Os metadados de instância estão disponíveis para execução de VMs criadas/gerenciadas usando [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Acesse todas as categorias de dados para uma instância de máquina virtual usando a seguinte solicitação:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Todas as consultas de metadados de instância diferenciam maiúsculas de minúsculas.

### <a name="data-output"></a>Saída de dados

Por padrão, o serviço de metadados de instância retorna dados no formato JSON (`Content-Type: application/json`). No entanto, diferentes APIs retornam dados em formatos diferentes, se solicitado.
A tabela a seguir é uma referência de outras APIs de formatos de dados que podem dar suporte ao.

API | Formato de dados padrão | Outros formatos
--------|---------------------|--------------
/instance | json | texto
/scheduledevents | json | nenhuma
/attested | json | nenhuma

Para acessar um formato de resposta não padrão, especifique o formato solicitado como um parâmetro de cadeia de caracteres de consulta na solicitação. Por exemplo:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Para nós folha, a `format=json` não funciona. Para essas consultas `format=text` precisa ser especificado explicitamente se o formato padrão for JSON.

### <a name="security"></a>Segurança

O ponto de extremidade do serviço de metadados de instância é acessível somente de dentro da instância de máquina virtual em execução em um endereço IP não roteável. Além disso, qualquer solicitação com um cabeçalho de `X-Forwarded-For` é rejeitada pelo serviço.
As solicitações também devem conter um cabeçalho `Metadata: true` para garantir que a solicitação real tenha sido diretamente intencional e não faça parte do redirecionamento não intencional.

### <a name="error"></a>Erro

Se houver um elemento de dados não encontrado ou uma solicitação malformada, o serviço de metadados de instância retornará erros HTTP padrão. Por exemplo:

Código de status HTTP | Razão
----------------|-------
200 OK |
400 solicitação inadequada | Cabeçalho de `Metadata: true` ausente ou formato ausente ao consultar um nó folha
404 Não Encontrado | O elemento solicitado não existe
método 405 não permitido | Somente as solicitações `GET` e `POST` têm suporte
429 número excessivo de solicitações | A API atualmente dá suporte a um máximo de 5 consultas por segundo
Erro de serviço 500     | Tentar novamente após algum tempo

### <a name="examples"></a>Exemplos

> [!NOTE]
> Todas as respostas de API são cadeias de caracteres JSON. Todas as respostas de exemplo a seguir são bem impressas para facilitar a leitura.

#### <a name="retrieving-network-information"></a>Recuperando informações de rede

**Pedido**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. A resposta de exemplo a seguir é bem impressa para facilitar a leitura.

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

#### <a name="retrieving-public-ip-address"></a>Recuperando endereço IP público

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Recuperando todos os metadados de uma instância

**Pedido**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-03-11"
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. A resposta de exemplo a seguir é bem impressa para facilitar a leitura.

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

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Recuperando metadados na máquina virtual do Windows

**Pedido**

Os metadados da instância podem ser recuperados no Windows por meio do utilitário PowerShell `curl`: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-03-11 | select -ExpandProperty Content
```

Ou por meio do cmdlet `Invoke-RestMethod`:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-03-11 -Method get 
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. A resposta de exemplo a seguir é bem impressa para facilitar a leitura.

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
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
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

## <a name="metadata-apis"></a>APIs de metadados

#### <a name="the-following-apis-are-available-through-the-metadata-endpoint"></a>As seguintes APIs estão disponíveis por meio do ponto de extremidade de metadados:

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
atestados | Ver [dados atestados](#attested-data) | 2018-10-01
identidade | Identidades gerenciadas para recursos do Azure. Consulte [adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
instance | Consulte [API de instância](#instance-api) | 2017-04-02
scheduledevents | Consulte [eventos agendados](scheduled-events.md) | 2017-08-01

#### <a name="instance-api"></a>API de instância
##### <a name="the-following-compute-categories-are-available-through-the-instance-api"></a>As categorias de computação a seguir estão disponíveis por meio da API da instância do:

> [!NOTE]
> Por meio do ponto de extremidade de metadados, as categorias a seguir são acessadas por meio de instância/computação

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
azEnvironment | Ambiente do Azure em que a VM está sendo executada | 2018-10-01
customData | Ver [dados personalizados](#custom-data) | 2019-02-01
localização | Região do Azure em que a VM está sendo executada | 2017-04-02
nome | Nome da VM | 2017-04-02
oferta | Informações da oferta para a imagem da VM e estão presentes apenas para imagens implantadas na Galeria de imagens do Azure | 2017-04-02
osType | Linux ou Windows | 2017-04-02
placementGroupId | [Grupo de posicionamento](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) do conjunto de dimensionamento de máquinas virtuais | 2017-08-01
intenção | [Plano](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) contendo nome, produto e publicador para uma VM se sua imagem do Azure Marketplace | 2018-04-02
platformUpdateDomain |  [Atualizar domínio](manage-availability.md) no qual a VM está sendo executada | 2017-04-02
platformFaultDomain | [Domínio de falha](manage-availability.md) em que a VM está sendo executada | 2017-04-02
operador | Provedor da VM | 2018-10-01
publicKeys | [Coleção de chaves públicas](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) atribuídas à VM e aos caminhos | 2018-04-02
publisher | Editor da imagem da VM | 2017-04-02
resourceGroupName | [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md) para sua máquina virtual | 2017-08-01
resourceId | A ID [totalmente qualificada](https://docs.microsoft.com/rest/api/resources/resources/getbyid) do recurso | 2019-03-11
SKU | SKU específico para a imagem da VM | 2017-04-02
subscriptionId | Assinatura do Azure para a máquina virtual | 2017-08-01
etiquetas | [Marcas](../../azure-resource-manager/resource-group-using-tags.md) para sua máquina virtual  | 2017-08-01
marcações | Marcas formatadas como uma matriz JSON para facilitar a análise programática  | 2019-06-04
versão | Versão da imagem da VM | 2017-04-02
vmId | [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a VM | 2017-04-02
vmScaleSetName | Nome do conjunto de [dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) de sua definição de escala de máquina virtual | 2017-12-01
vmSize | [Tamanho da VM](sizes.md) | 2017-04-02
zona | [Zona de disponibilidade](../../availability-zones/az-overview.md) de sua máquina virtual | 2017-12-01

##### <a name="the-following-network-categories-are-available-through-the-instance-api"></a>As seguintes categorias de rede estão disponíveis por meio da API de instância:

> [!NOTE]
> Por meio do ponto de extremidade de metadados, as categorias a seguir são acessadas por meio de instância/rede/interface

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
ipv4/privateIpAddress | Endereço IPv4 local da VM | 2017-04-02
ipv4/publicIpAddress | Endereço IPv4 público da VM | 2017-04-02
sub-rede/endereço | Endereço de sub-rede da VM | 2017-04-02
sub-rede/prefixo | Prefixo de sub-rede, exemplo 24 | 2017-04-02
ipv6/ipAddress | Endereço IPv6 local da VM | 2017-04-02
macAddress | Endereço MAC da VM | 2017-04-02

## <a name="attested-data"></a>Dados atestados

Os metadados da instância respondem ao ponto de extremidade http em 169.254.169.254. Parte do cenário servido pelo serviço de metadados de instância é fornecer garantias de que os dados que responderam são provenientes do Azure. Nós entramos em parte dessas informações para que as imagens do Marketplace possam ter certeza de que sua imagem está em execução no Azure.

### <a name="example-attested-data"></a>Exemplo de dados atestados

> [!NOTE]
> Todas as respostas de API são cadeias de caracteres JSON. As respostas de exemplo a seguir são bem impressas para facilitar a leitura.

 **Pedido**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

API-Version é um campo obrigatório. Consulte a [seção de disponibilidade do serviço](#service-availability) para obter as versões de API com suporte.
Nonce é uma cadeia de caracteres de 10 dígitos opcional fornecida. O nonce pode ser usado para rastrear a solicitação e, se não for fornecido, na cadeia de caracteres codificada em resposta, o carimbo de data/hora UTC atual é retornado.

 **Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. A resposta de exemplo a seguir é bem impressa para facilitar a leitura.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> O blob de assinatura é uma versão do documento assinada [PKCS7](https://aka.ms/pkcs7) . Ele contém o certificado usado para assinar junto com os detalhes da VM, como vmId, nonce, SubscriptionId, carimbo de data/hora para a criação e a expiração do documento e as informações do plano sobre a imagem. As informações do plano só são populadas para imagens do Azure Market Place. O certificado pode ser extraído da resposta e usado para validar que a resposta é válida e proveniente do Azure.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Recuperando metadados atestados na máquina virtual do Windows

 **Pedido**

Os metadados da instância podem ser recuperados no Windows por meio do utilitário PowerShell `curl`:

 ```bash
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Ou por meio do cmdlet `Invoke-RestMethod`:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

API-Version é um campo obrigatório. Consulte a seção de disponibilidade do serviço para obter as versões de API com suporte.
Nonce é uma cadeia de caracteres de 10 dígitos opcional fornecida. O nonce pode ser usado para rastrear a solicitação e, se não for fornecido, na cadeia de caracteres codificada em resposta, o carimbo de data/hora UTC atual é retornado.

 **Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. A resposta de exemplo a seguir é bem impressa para facilitar a leitura.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> O blob de assinatura é uma versão do documento assinada [PKCS7](https://aka.ms/pkcs7) . Ele contém o certificado usado para assinar junto com os detalhes da VM, como vmId, nonce, SubscriptionId, carimbo de data/hora para a criação e a expiração do documento e as informações do plano sobre a imagem. As informações do plano só são populadas para imagens do Azure Market Place. O certificado pode ser extraído da resposta e usado para validar que a resposta é válida e proveniente do Azure.


## <a name="example-scenarios-for-usage"></a>Cenários de exemplo de uso  

### <a name="tracking-vm-running-on-azure"></a>Acompanhamento de uma VM em execução no Azure

Como um provedor de serviços, talvez seja necessário controlar o número de VMs que executam o software ou ter agentes que precisam controlar a exclusividade da VM. Para poder obter uma ID exclusiva para uma VM, use o campo `vmId` do serviço de metadados de instância.

**Pedido**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Resposta**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Colocação de contentores, domínio de atualização/falha baseado em partições de dados

Para determinados cenários, o posicionamento de réplicas de dados diferentes é de importância principal. Por exemplo, o [posicionamento da réplica do HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou o posicionamento do contêiner por meio de um [orquestrador](https://kubernetes.io/docs/user-guide/node-selection/) talvez você precise saber o `platformFaultDomain` e `platformUpdateDomain` a VM esteja em execução.
Você também pode usar [zonas de disponibilidade](../../availability-zones/az-overview.md) para que as instâncias tomem essas decisões.
Você pode consultar esses dados diretamente por meio do serviço de metadados de instância.

**Pedido**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Resposta**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Obter mais informações sobre a VM durante o pedido de suporte

Como um provedor de serviços, você pode receber uma chamada de suporte onde você gostaria de saber mais sobre a VM. Solicitar que o cliente compartilhe os metadados de computação pode fornecer informações básicas para que o profissional de suporte saiba mais sobre o tipo de VM no Azure.

**Pedido**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. A resposta de exemplo a seguir é bem impressa para facilitar a leitura.

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

O Azure tem várias nuvens soberanass, como o [Azure governamental](https://azure.microsoft.com/overview/clouds/government/). Às vezes, você precisa do ambiente do Azure para tomar algumas decisões de tempo de execução. O exemplo a seguir mostra como você pode obter esse comportamento.

**Pedido**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Resposta**
```bash
AzurePublicCloud
```
A nuvem e os valores do ambiente do Azure estão listados abaixo.

 Nuvem   | Ambiente do Azure
---------|-----------------
[Todas as regiões globais do Azure geralmente disponíveis](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China](https://azure.microsoft.com/global-infrastructure/china/)                  | AzureChinaCloud
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>Obtendo as marcas para a VM

As marcas podem ter sido aplicadas à sua VM do Azure para organizá-las logicamente em uma taxonomia. As marcas atribuídas a uma VM podem ser recuperadas usando a solicitação abaixo.

**Pedido**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Resposta**

```text
Department:IT;Environment:Test;Role:WebRole
```

O campo `tags` é uma cadeia de caracteres com as marcas delimitadas por ponto e vírgula. Isso pode ser um problema se o ponto e vírgulas forem usados nas próprias marcas. Se um analisador for escrito para extrair as marcas programaticamente, você deverá contar com o campo `tagsList`, que é uma matriz JSON sem delimitadores e, consequentemente, mais fácil de analisar.

**Pedido**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=text"
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

### <a name="validating-that-the-vm-is-running-in-azure"></a>Confirmar se a VM está a ser executada no Azure

Os fornecedores do Marketplace desejam garantir que seu software seja licenciado para ser executado somente no Azure. Se alguém copiar o VHD para o local, ele deverá ter a capacidade de detectá-lo. Chamando o serviço de metadados de instância, os fornecedores de Marketplace podem obter dados assinados que garantem a resposta somente do Azure.

> [!NOTE]
> Requer a instalação do JQ.

**Pedido**

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
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"
}
```

Dados | Descrição
-----|------------
nonce | Cadeia de caracteres opcional fornecida pelo usuário com a solicitação. Se nenhum nonce foi fornecido na solicitação, o carimbo de data/hora UTC atual será retornado
intenção | [Planejar](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) uma VM em que ela é uma imagem do Azure Marketplace, contém nome, produto e Publicador
timestamp/createdOn | O carimbo de data/hora em que o primeiro documento assinado foi criado
timestamp/expiresOn | O carimbo de data/hora em que o documento assinado expira
vmId |  [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a VM
subscriptionId | Assinatura do Azure para a máquina virtual, introduzida no `2019-04-30`

#### <a name="verifying-the-signature"></a>Verificando a assinatura

Depois de obter a assinatura acima, você pode verificar se a assinatura é da Microsoft. Além disso, você pode verificar o certificado intermediário e a cadeia de certificados. Por fim, você pode verificar se a ID da assinatura está correta.

> [!NOTE]
> O certificado para nuvem pública e soberanas Cloud será diferente.

 Nuvem | Certificado
---------|-----------------
[Todas as regiões globais do Azure geralmente disponíveis](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure China](https://azure.microsoft.com/global-infrastructure/china/)                  | metadata.azure.cn
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

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

Nos casos em que o certificado intermediário não pode ser baixado devido a restrições de rede durante a validação, o certificado intermediário pode ser fixado. No entanto, o Azure irá sobrepor os certificados de acordo com a prática PKI padrão. Os certificados fixados precisariam ser atualizados quando ocorrer uma sobreposição. Sempre que uma alteração para atualizar o certificado intermediário for planejada, o blog do Azure será atualizado e os clientes do Azure serão notificados. Os certificados intermediários podem ser encontrados [aqui](https://www.microsoft.com/pki/mscorp/cps/default.htm). Os certificados intermediários para cada uma das regiões podem ser diferentes.

### <a name="failover-clustering-in-windows-server"></a>Clustering de failover no Windows Server

Para determinados cenários, ao consultar o serviço de metadados de instância com clustering de failover, é necessário adicionar uma rota à tabela de roteamento.

1. Abra o prompt de comando com privilégios de administrador.

2. Execute o comando a seguir e anote o endereço da interface para o destino de rede (`0.0.0.0`) na tabela de rotas IPv4.

```bat
route print
```

> [!NOTE] 
> A saída de exemplo a seguir de uma VM do Windows Server com cluster de failover habilitado contém apenas a tabela de rotas IPv4 para simplificar.

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

1. Execute o comando a seguir e use o endereço da interface para o destino de rede (`0.0.0.0`), que é (`10.0.1.10`) neste exemplo.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="custom-data"></a>Dados personalizados
O serviço de metadados de instância fornece a capacidade para a VM ter acesso aos seus dados personalizados. Os dados binários devem ter menos de 64 KB e são fornecidos para a VM na forma codificada em base64.

Os dados personalizados do Azure podem ser inseridos na VM por meio de APIs REST, de cmdlets do PowerShell, da CLI (interface de linha de comando) do Azure ou de um modelo do ARM.

Para um exemplo de interface de linha de comando do Azure, consulte [dados personalizados e inicialização de nuvem em Microsoft Azure](https://azure.microsoft.com/blog/custom-data-and-cloud-init-on-windows-azure/).

Para um exemplo de modelo ARM, consulte [implantar uma máquina virtual com CustomData](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

Os dados personalizados estão disponíveis para todos os processos em execução na VM. É recomendável que os clientes não insiram informações secretas em dados personalizados.

Atualmente, há garantia de que os dados personalizados estejam disponíveis durante a inicialização de uma VM. Se forem feitas atualizações na VM, como adicionar discos ou redimensionar a VM, o serviço de metadados de instância não fornecerá dados personalizados. O fornecimento de dados personalizados persistentemente por meio do serviço de metadados de instância está atualmente em andamento.

#### <a name="retrieving-custom-data-in-virtual-machine"></a>Recuperando dados personalizados na máquina virtual
O serviço de metadados de instância fornece dados personalizados para a VM na forma codificada em base64. O exemplo a seguir decodifica a cadeia de caracteres codificada em base64.

> [!NOTE]
> Os dados personalizados neste exemplo são interpretados como uma cadeia de caracteres ASCII que lê "meus dados personalizados".

**Pedido**

```bash
curl -H "Metadata:true" "http://169.254.169.254/metadata/instance/compute/customData?api-version=2019-02-01&&format=text" | base64 --decode
```

**Resposta**

```text
My custom data.
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Exemplos de chamada de serviço de metadados usando diferentes idiomas dentro da VM

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
   * O serviço de metadados de instância requer que o `Metadata: true` de cabeçalho seja passado na solicitação. Passar esse cabeçalho na chamada REST permite o acesso ao serviço de metadados de instância.
2. Por que não estou obtendo informações de computação para minha VM?
   * Atualmente, o serviço de metadados de instância só dá suporte a instâncias criadas com Azure Resource Manager. No futuro, o suporte para VMs de serviço de nuvem pode ser adicionado.
3. Criei minha máquina virtual por meio de Azure Resource Manager um enquanto estava de volta. Por que não vejo informações de metadados de computação?
   * Para qualquer VM criada após setembro de 2016, adicione uma [marca](../../azure-resource-manager/resource-group-using-tags.md) para começar a ver os metadados de computação. Para VMs mais antigas (criadas antes de setembro de 2016), adicione/remova extensões ou discos de dados à VM para atualizar os metadados.
4. Não estou vendo todos os dados populados para a nova versão
   * Para qualquer VM criada após setembro de 2016, adicione uma [marca](../../azure-resource-manager/resource-group-using-tags.md) para começar a ver os metadados de computação. Para VMs mais antigas (criadas antes de setembro de 2016), adicione/remova extensões ou discos de dados à VM para atualizar os metadados.
5. Por que estou recebendo o erro `500 Internal Server Error`?
   * Repita a solicitação com base no sistema de retirada exponencial. Se o problema persistir, entre em contato com o suporte do Azure.
6. Onde posso compartilhar perguntas/comentários adicionais?
   * Envie seus comentários em https://feedback.azure.com.
7. Isso funcionaria para a instância do conjunto de dimensionamento de máquinas virtuais?
   * Sim, o serviço de metadados está disponível para instâncias do conjunto de dimensionamento.
8. Como fazer obter suporte para o serviço?
   * Para obter suporte para o serviço, crie um problema de suporte no portal do Azure para a VM em que você não consegue obter resposta de metadados após tentativas longas.
9. Obtenho o tempo limite da solicitação para minha chamada para o serviço?
   * As chamadas de metadados devem ser feitas do endereço IP primário atribuído à placa de rede da VM. Além disso, caso você tenha alterado suas rotas, deve haver uma rota para o endereço 169.254.0.0/16 da placa de rede.
10. Atualizei minhas marcas no conjunto de dimensionamento de máquinas virtuais, mas elas não aparecem nas instâncias diferentemente das VMs?
    * Atualmente, somente as marcas conjuntos mostram a VM em uma reinicialização/reimagem/ou uma alteração de disco para a instância.

    ![Suporte a metadados de instância](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [eventos agendados](scheduled-events.md)
