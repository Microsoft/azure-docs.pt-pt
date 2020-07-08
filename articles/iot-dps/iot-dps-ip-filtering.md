---
title: Filtros de ligação IP Azure IoT DPS / Microsoft Docs
description: Como utilizar a filtragem IP para bloquear ligações de endereços IP específicos para a sua instância DPS Azure IoT. Pode bloquear ligações a partir de endereços IP individuais ou de gamas de endereços IP.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79284918"
---
# <a name="use-ip-filters"></a>Utilizar filtros de IP

A segurança é um aspeto importante de qualquer solução IoT. Por vezes, é necessário especificar explicitamente os endereços IP a partir dos quais os dispositivos podem ligar-se como parte da sua configuração de segurança. A funcionalidade *de filtro IP* para um Serviço de Provisionamento de Dispositivos IoT Hub (DPS) permite-lhe configurar regras para rejeitar ou aceitar tráfego a partir de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando utilizar

Existem dois casos específicos de utilização em que é útil bloquear ligações a um ponto final DPS a partir de certos endereços IP:

* O seu DPS só deve receber tráfego a partir de uma gama especificada de endereços IP e rejeitar tudo o resto. Por exemplo, está a utilizar o seu DPS com [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) para criar ligações privadas entre um DPS e os seus dispositivos.

* Tem de rejeitar o tráfego de endereços IP que tenham sido identificados como suspeitos pelo administrador do DPS.

## <a name="how-filter-rules-are-applied"></a>Como as regras do filtro são aplicadas

As regras do filtro IP são aplicadas ao nível da instância DPS. Por isso, as regras do filtro IP aplicam-se a todas as ligações de dispositivos e aplicações de back-end utilizando qualquer protocolo suportado.

Qualquer tentativa de ligação a partir de um endereço IP que corresponda a uma regra IP rejeitada no seu caso DPS recebe um código de estado 401 não autorizado e descrição. A mensagem de resposta não menciona a regra ip.

## <a name="default-setting"></a>Definição predefinida

Por predefinição, a grelha **de filtro IP** no portal para DPS está vazia. Esta definição predefinida significa que o seu DPS aceita ligações a partir de qualquer endereço IP. Esta definição predefinida é equivalente a uma regra que aceita o intervalo de endereço IP 0.0.0.0/0.

![Definições de filtro IP padrão IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicione ou edite uma regra de filtro IP

Para adicionar uma regra do filtro IP, selecione **+ Adicionar regra do filtro IP**.

![Adicione uma regra do filtro IP a um DPS IoT](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Depois de selecionar **a Regra do Filtro IP,** preencha os campos.

![Depois de selecionar Adicione uma regra de filtro IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Forneça um **nome** para a regra do filtro IP. Esta deve ser uma corda alfanumérica única, insensível até 128 caracteres. Apenas os caracteres alfanuméricos ASCII de 7 bits mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` são aceites.

* Forneça um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 192.168.100.0/22 representa os endereços 1024 IPv4 de 192.168.100.0 a 192.168.103.255.

* Selecione **Permitir** ou **Bloquear** como **ação** para a regra do filtro IP.

Depois de preencher os campos, **selecione Guardar** para guardar a regra. Vê um alerta a notificá-lo de que a atualização está em andamento.

![Notificação sobre a poupança de uma regra de filtro IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

A opção **Adicionar** é desativada quando atinge o máximo de 10 regras de filtro IP.

Para editar uma regra existente, selecione os dados que pretende alterar, faça a alteração e, em seguida, **selecione Guardar** para guardar a sua edição.

> [!NOTE]
> Rejeitar endereços IP pode impedir que outros Serviços Azure interajam com a instância DPS.

## <a name="delete-an-ip-filter-rule"></a>Eliminar uma regra de filtro IP

Para eliminar uma regra do filtro IP, selecione o ícone do caixote do lixo nessa linha e, em seguida, **selecione Guardar**. A regra é removida e a mudança é guardada.

![Eliminar uma regra de filtro IP IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Atualizar regras do filtro IP em código

Pode recuperar e modificar o filtro IP DPS utilizando o ponto final REST do Fornecedor de Recursos Azure. Ver `properties.ipFilterRules` no [método createorupdate](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

Atualmente, a atualização das regras do filtro IP não é suportada com Azure CLI ou Azure PowerShell, mas pode ser realizada com modelos do Azure Resource Manager. Consulte os [modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) para obter orientação sobre a utilização de modelos de Gestor de Recursos. Os exemplos do modelo que se seguem mostram como criar, editar e eliminar as regras do filtro IP DPS.

### <a name="add-an-ip-filter-rule"></a>Adicione uma regra de filtro IP

O exemplo do modelo a seguir cria uma nova regra do filtro IP chamada "AllowAll" que aceita todo o tráfego.

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

Atualize os atributos de regra do filtro IP do modelo com base nos seus requisitos.

| Atributo                | Descrição |
| ------------------------ | ----------- |
| **Nome de filtro**           | Forneça um nome para a regra do filtro IP. Esta deve ser uma corda alfanumérica única, insensível até 128 caracteres. Apenas os caracteres alfanuméricos ASCII de 7 bits mais {'-', ':', '/', \' '.', '.', '+', '%', '_', '#', '*', '!', '!', '',', '', '', '=', '@', '', '', ''' |
| **Ação**               | Os valores aceites são **Aceitar**   ou **Rejeitar**   como ação para a regra do filtro IP. |
| **ipMask**               | Forneça um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 192.168.100.0/22 representa os endereços 1024 IPv4 de 192.168.100.0 a 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Atualizar uma regra do filtro IP

O exemplo do modelo a seguir atualiza a regra do filtro IP denominada "AllowAll", mostrada anteriormente, para rejeitar todo o tráfego.

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

### <a name="delete-an-ip-filter-rule"></a>Eliminar uma regra de filtro IP

O exemplo do modelo a seguir elimina todas as regras do filtro IP para a instância DPS.

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



## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra do filtro IP

As regras do filtro IP são aplicadas por ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitação ou rejeição.

Por exemplo, se pretender aceitar endereços no intervalo 192.168.100.0/22 e rejeitar tudo o resto, a primeira regra da grelha deve aceitar o intervalo de endereços 192.168.100.0/22. A próxima regra deve rejeitar todos os endereços utilizando o intervalo 0.0.0.0/0.

Pode alterar a ordem das regras do filtro IP na grelha clicando nos três pontos verticais no início de uma linha e utilizando arrasto e gota.

Para guardar a nova ordem de regra do filtro IP, clique em **Guardar**.

![Altere a ordem das suas regras de filtro IP DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Próximos passos

Para explorar ainda mais o DPS de gestão, consulte:

* [Compreender endereços IP DPS do IoT](iot-dps-understand-ip-address.md)
* [Configure DPS usando o CLI Azure](how-to-manage-dps-with-cli.md)
* [Controlo do acesso ao DPS](how-to-control-access.md)
