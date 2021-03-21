---
title: Filtros de ligação de IP do Azure IoT DPS | Microsoft Docs
description: Como utilizar a filtragem de IP para bloquear ligações de endereços IP específicos à sua instância do Azure IoT DPS. Pode bloquear ligações de endereços IP individuais ou de intervalos de endereços.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: e1b175a176255da465433b2db45cb3cb67d360d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934506"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Utilizar os filtros de ligação IP do Azure IoT DPS

A segurança é um aspeto importante de qualquer solução IoT. Por vezes, como parte da sua configuração de segurança, tem de especificar explicitamente os endereços IP a partir dos quais os dispositivos se podem ligar. A funcionalidade de *filtro de IP* do Serviço Aprovisionamento de Dispositivos no Hub IoT (DPS) permite-lhe configurar regras para rejeitar ou aceitar o tráfego de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando utilizar

Há dois casos de utilização nos quais é útil bloquear as ligações a um ponto final do DPS a partir de determinados endereços IP:

* O seu DPS só deve receber tráfego de um intervalo especificado de endereços IP e rejeitar tudo o resto. Por exemplo, está a utilizar o DPS com o [Azure ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) para criar ligações privadas entre um DPS e os seus dispositivos.

* Tem de rejeitar o tráfego de endereços IP que o administrador do DPS identificou como sendo suspeitos.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro de IPs são aplicadas ao nível das instâncias do DPS. Dessa forma, essas regras são aplicadas a todas as ligações de dispositivos e aplicações de back-end que utilizem qualquer protocolo suportado.

Qualquer tentativa de ligação de um endereço IP que corresponda a uma regra de IP de rejeição na sua instância do DPS recebe o código de estado 401 não autorizado e uma descrição. A mensagem da resposta não menciona a regra de IP.

## <a name="default-setting"></a>Definição predefinida

Por predefinição, a grelha **Filtro de IP** no portal do DPS está vazia. Esta predefinição significa que o DPS aceita ligações de todos os endereços IP. Esta predefinição é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

![Predefinições do filtro de IP do IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro de IP

Para adicionar uma regra de filtro de IP, selecione **+ Adicionar Regra de Filtro de IP**.

![Adicionar uma regra de filtro de IP a um IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Depois de selecionar **Adicionar Regra de Filtro de IP**, preencha os campos.

![Depois de selecionar Adicionar uma regra de Filtro de IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Indique um **nome** para a regra de Filtro de IP. O nome tem de ser uma cadeia alfanumérica exclusiva, sensível a maiúsculas e minúsculas com um máximo de 128 carateres. Só são aceites os carateres alfanuméricos ASCII 7-bit mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Indique um endereço IPv4 individual ou um bloco de endereços IP em notação CIDR. Por exemplo, em notação CIDR, 192.168.100.0/22 representa os endereços IPv4 1024 de 192.168.100.0 a 192.168.103.255.

* Selecione **Permitir** ou **Bloquear** como a **ação** da regra de filtro de IP.

Depois de preencher os campos, selecione **Guardar** para guardar a regra. Pode ver um alerta que o notifica que a atualização está em curso.

![Notificação relativa a guardar uma regra de filtro de IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

A opção **Adicionar** é desativada quando atinge o máximo de 100 regras de filtro IP.

Para editar uma regra existente, selecione os dados que pretende alterar, faça a alteração e selecione **Guardar** para guardar a edição.

> [!NOTE]
> A rejeição de endereços IP pode impedir que outros serviços do Azure interajam com a instância do DPS.

## <a name="delete-an-ip-filter-rule"></a>Eliminar uma regra de filtro de IP

Para eliminar uma regra de filtro de IP, selecione o ícone de caixote do lixo nessa linha e selecione **Guardar**. A regra é removida e a alteração guardada.

![Eliminar uma regra de filtro de IP do IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Atualizar as regras de filtro de IP no código

Pode obter e modificar o filtro de IP do DPS com o ponto final REST do fornecedor de recursos do Azure. Veja `properties.ipFilterRules` no [método createorupdate](/rest/api/iot-dps/iotdpsresource/createorupdate).

A atualização das regras de filtros de IP do DPS não é suportada atualmente com a CLI do Azure nem com o Azure PowerShell, mas pode ser feita com os modelos do Azure Resource Manager. Veja [Modelos do Azure Resource Manager](../azure-resource-manager/templates/overview.md) para obter orientações sobre a utilização destes modelos. Os exemplos de modelos seguintes mostram como criar, editar e eliminar regras de filtros de IP do DPS.

### <a name="add-an-ip-filter-rule"></a>Adicionar uma regra de filtro IP

O exemplo de modelo seguinte cria uma regra de filtro de IP nova com o nome "AllowAll", que aceita todo o tráfego.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Atualize os atributos da regra de filtro de IP do modelo de acordo com os seus requisitos.

| Atributo                | Descrição |
| ------------------------ | ----------- |
| **FilterName**           | Indique um nome para a regra de Filtro de IP. O nome tem de ser uma cadeia alfanumérica exclusiva, sensível a maiúsculas e minúsculas com um máximo de 128 carateres. Apenas os caracteres alfanuméricos ASCII de 7 bits mais  `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`   são aceites. |
| **Ação**               | Os valores aceites como a ação da regra de filtro de IP são **Aceitar** ou  **Rejeitar** . |
| **ipMask**               | Indique um endereço IPv4 individual ou um bloco de endereços IP em notação CIDR. Por exemplo, em notação CIDR, 192.168.100.0/22 representa os endereços IPv4 1024 de 192.168.100.0 a 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Atualizar uma regra de filtro de IP

O exemplo de modelo seguinte atualiza a regra de filtro de IP denominada "AllowAll", mostrada acima, para rejeitar todo o tráfego.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Eliminar uma regra de filtro de IP

O exemplo de modelo seguinte elimina todas as regras de filtro de IP da instância do DPS.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>Avaliação das regras de filtros de IP

As regras de filtros de IP são aplicadas por ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Por exemplo, se quiser aceitar os endereços no intervalo 192.168.100.0/22 e rejeitar todos os outros, a primeira regra na grelha deve aceitar o intervalo de endereços 192.168.100.0/22. A regra seguinte deve rejeitar todos os endereços mediante a utilização do intervalo 0.0.0.0/0.

Pode alterar a ordem das regras dos filtros de IP na grelha ao clicar nos três pontos verticais no início de uma linha e arrastar e largar.

Para guardar a ordem nova das regras dos filtros de IP, clique em **Guardar**.

![Alterar a ordem das regra dos filtros de IP do DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Passos seguintes

Para explorar ainda mais a gestão do DPS, veja:

* [Compreender os endereços IP do IoT DPS](iot-dps-understand-ip-address.md)
* [Configurar dPS utilizando o CLI Azure](quick-setup-auto-provision-cli.md)
* [Controlar o acesso ao DPS](how-to-control-access.md)
