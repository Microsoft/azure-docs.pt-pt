---
title: Filtros de ligação IP Azure IoT DPS / Microsoft Docs
description: Como utilizar a filtragem IP para bloquear ligações de endereços IP específicos para a sua instância DPS Azure IoT. Pode bloquear ligações a partir de endereços IP individuais ou ip.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284918"
---
# <a name="use-ip-filters"></a>Utilizar filtros de IP

A segurança é um aspeto importante de qualquer solução IoT. Por vezes, é necessário especificar explicitamente os endereços IP a partir dos quais os dispositivos podem ligar-se como parte da sua configuração de segurança. A funcionalidade *de filtragem IP* para um Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS) permite configurar regras para rejeitar ou aceitar o tráfego a partir de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando utilizar

Existem dois casos específicos de utilização em que é útil bloquear ligações a um ponto final dPS a partir de certos endereços IP:

* O seu DPS deve receber tráfego apenas a partir de uma gama especificada de endereços IP e rejeitar tudo o resto. Por exemplo, está a utilizar o seu DPS com [a Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) para criar ligações privadas entre um DPS e os seus dispositivos.

* Você precisa rejeitar o tráfego de endereços IP que foram identificados como suspeitos pelo administrador do DPS.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras do filtro IP são aplicadas ao nível da instância DPS. Por isso, as regras do filtro IP aplicam-se a todas as ligações de dispositivos e aplicações back-end utilizando qualquer protocolo suportado.

Qualquer tentativa de ligação de um endereço IP que corresponda a uma regra de IP rejeitada na sua instância DPS recebe um código e descrição não autorizados de estado 401. A mensagem de resposta não menciona a regra do PI.

## <a name="default-setting"></a>Definição predefinida

Por predefinição, a grelha do **filtro IP** no portal para DPS está vazia. Esta definição predefinida significa que o seu DPS aceita ligações a partir de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

![Definições de filtro IP padrão IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro IP

Para adicionar uma regra do filtro IP, **selecione + Adicionar regra**do filtro IP .

![Adicione uma regra de filtro IP a um DPS IoT](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Depois de selecionar a regra do **filtro IP,** preencha os campos.

![Depois de selecionar Adicionar uma regra de filtro IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Forneça um **nome** para a regra do filtro IP. Esta deve ser uma corda alfanumérica única, insensível e insensível a 128 caracteres. Apenas são aceites os caracteres alfanuméricos ascii de 7 bits mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Forneça um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, no ponto CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255.

* Selecione **Permitir** ou **Bloquear** como **ação** para a regra do filtro IP.

Depois de preencher os campos, selecione **Guardar** para salvar a regra. Vê um alerta a informar-lhe que a atualização está em andamento.

![Notificação sobre salvar uma regra de filtro IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

A opção **Adicionar** é desativada quando atingir o máximo de 10 regras de filtro IP.

Para editar uma regra existente, selecione os dados que pretende alterar, faça a alteração e, em seguida, selecione **Guardar** para salvar a sua edição.

> [!NOTE]
> A rejeição de endereços IP pode impedir que outros Serviços Azure interajam com a instância DPS.

## <a name="delete-an-ip-filter-rule"></a>Eliminar uma regra de filtro IP

Para eliminar uma regra do filtro IP, selecione o ícone do caixote do lixo nessa linha e, em seguida, selecione **Guardar**. A regra é removida e a mudança é salva.

![Eliminar uma regra de filtro IP IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Atualizar regras de filtro IP em código

Pode recuperar e modificar o filtro IP DPS utilizando o ponto final REST do Fornecedor de recursos Azure. Consulte `properties.ipFilterRules` no [método de criação ou atualização](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

Atualizar as regras de filtro IP DPS não é atualmente suportado com Azure CLI ou Azure PowerShell, mas pode ser realizado com modelos de Gestor de Recursos Azure. Veja, modelos de [Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) para orientação sobre a utilização de modelos de Gestor de Recursos. Os exemplos de modelo que se seguem mostram como criar, editar e eliminar as regras de filtro IP DPS.

### <a name="add-an-ip-filter-rule"></a>Adicione uma regra de filtro IP

O exemplo do modelo seguinte cria uma nova regra do filtro IP chamada "AllowAll" que aceita todo o tráfego.

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

Atualize os atributos da regra do filtro IP do modelo com base nos seus requisitos.

| Atributo                | Descrição |
| ------------------------ | ----------- |
| **Nome do filtro**           | Forneça um nome para a regra do filtro IP. Esta deve ser uma corda alfanumérica única, insensível e insensível a 128 caracteres. Apenas os caracteres alfanuméricos de 7 bits ASCII mais {'',', ':', '/', '\'', '.'', '+', '%', '_', '#', '*', '?', '!', '(',',', '=', '@', '',',',''''''''''' |
| **Ação**               | Os valores aceites são **Aceitar** ou **Rejeitar** como a ação para a regra do filtro IP. |
| **ipMask**               | Forneça um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, no ponto CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Atualizar uma regra de filtro IP

O exemplo do modelo seguinte atualiza a regra do filtro IP denominada "AllowAll", mostrada anteriormente, para rejeitar todo o tráfego.

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

O exemplo do modelo seguinte elimina todas as regras do filtro IP para a instância DPS.

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



## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras do filtro IP são aplicadas por ordem e a primeira regra que corresponde ao endereço IP determina a aceitação ou rejeita a ação.

Por exemplo, se quiser aceitar endereços no intervalo 192.168.100.0/22 e rejeitar tudo o resto, a primeira regra da grelha deve aceitar o intervalo de endereços 192.168.100.0/22. A seguinte regra deve rejeitar a todos os endereços mediante a utilização do intervalo 0.0.0.0/0.

Pode alterar a ordem das suas regras de filtro IP na grelha clicando nos três pontos verticais no início de uma linha e utilizando arrasto e gota.

Para guardar a sua nova ordem de regra do filtro IP, clique em **Guardar**.

![Alterar a ordem das suas regras de filtro IP DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Passos Seguintes

Para explorar ainda mais o DPS de gestão, consulte:

* [Compreender endereços IP ioT DPS](iot-dps-understand-ip-address.md)
* [Configure dPS utilizando o Azure CLI](how-to-manage-dps-with-cli.md)
* [Controlar o acesso ao DPS](how-to-control-access.md)
