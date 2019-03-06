---
title: Melhores práticas de modelos do Azure Resource Manager
description: Descreve as abordagens recomendadas para a criação de modelos Azure Resource Manager. Tem sugestões para evitar problemas comuns ao utilizar modelos.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2019
ms.author: tomfitz
ms.openlocfilehash: bcc529b02505359e6e4e320d4991a082797c5261
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440477"
---
# <a name="azure-resource-manager-template-best-practices"></a>Azure práticas recomendadas de modelo do Resource Manager

Este artigo oferece recomendações sobre como construir o modelo do Resource Manager. Estas recomendações ajudar a evitar problemas comuns ao utilizar um modelo para implementar uma solução.

Para obter recomendações sobre como regular as suas subscrições do Azure, veja [scaffold enterprise do Azure: Governação de subscrições prescritiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Para obter recomendações sobre como criar modelos que funcionam em todos os ambientes de cloud do Azure, veja [modelos de desenvolver o Azure Resource Manager para manter a consistência na cloud](templates-cloud-consistency.md).

## <a name="template-limits"></a>Limites do modelo

Limite o tamanho do seu modelo para 1 MB e cada ficheiro de parâmetros a 64 KB. O limite de 1 MB aplica-se para o estado final do modelo depois foi expandido com definições de recursos iterativo e valores de variáveis e parâmetros. 

Também está limitado a:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo o número de cópias)
* 64 valores de saída
* 24,576 caracteres numa expressão de modelo

Pode exceder alguns limites de modelo ao utilizar um modelo aninhado. Para obter mais informações, consulte [utilizar modelos ligados durante a implantação de recursos do Azure](resource-group-linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, pode combinar diversos valores num objeto. Para obter mais informações, consulte [objetos como parâmetros](resource-manager-objects-as-parameters.md).

## <a name="resource-group"></a>Grupo de recursos

Ao implementar recursos para um grupo de recursos, o grupo de recursos armazena metadados sobre os recursos. Os metadados são armazenados na localização do grupo de recursos.

Se a região do grupo de recursos está temporariamente indisponível, não é possível atualizar recursos no grupo de recursos, porque os metadados não estão disponível. Os recursos noutras regiões continuarão a funcionar conforme esperado, mas não é possível atualizá-los. Para minimizar o risco, localize o seu grupo de recursos e recursos na mesma região.

## <a name="parameters"></a>Parâmetros
As informações nesta secção podem ser úteis quando trabalha com [parâmetros](resource-group-authoring-templates.md#parameters).

### <a name="general-recommendations-for-parameters"></a>Recomendações gerais para parâmetros

* Minimize a utilização de parâmetros. Em alternativa, utilize variáveis ou valores literais para propriedades que não precisam de ser especificado durante a implementação.

* Utilize maiúscula para nomes de parâmetro.

* Utilize parâmetros para as definições que variam de acordo com o ambiente, como o SKU, tamanho ou capacidade.

* Utilize parâmetros para nomes de recursos que pretende especificar para fácil identificação.

* Forneça uma descrição de cada parâmetro nos metadados:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Defina valores predefinidos para os parâmetros que não são confidenciais. Ao especificar um valor predefinido, é mais fácil de implementar o modelo e os utilizadores do seu modelo ver um exemplo de um valor adequado. Qualquer valor predefinido para um parâmetro tem de ser válido para todos os utilizadores na configuração de implementação padrão. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Para especificar um parâmetro opcional, não utilize uma cadeia vazia como um valor predefinido. Em alternativa, utilize um valor literal ou uma expressão de linguagem para construir um valor.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Não utilize um parâmetro para a versão de API para um tipo de recurso. Propriedades de recursos e os valores podem variar consoante o número de versão. IntelliSense num editor de código não é possível determinar o esquema correto quando a versão de API está definida como um parâmetro. Em vez disso, versão de codificar a API no modelo.

* Utilize `allowedValues` com moderação. Utilize-o apenas quando deve certificar-se de que alguns valores não estão incluídos nas opções permitidas. Se usar `allowedValues` amplamente, poderão bloquear implementações válidas ao manter a sua lista não atualizados.

* Quando um nome de parâmetro no seu modelo corresponde a um parâmetro no comando de implementação do PowerShell, o Resource Manager resolve este conflito de nomes, adicionando o sufixo **FromTemplate** para o parâmetro de modelo. Por exemplo, se incluir um parâmetro denominado **ResourceGroupName** no seu modelo, está em conflito com o **ResourceGroupName** parâmetro no [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet. Durante a implementação, lhe for pedido para fornecer um valor para **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Recomendações de segurança para os parâmetros

* Utilize sempre os parâmetros para os nomes de utilizador e palavras-passe (ou segredos).

* Utilize `securestring` para todas as palavras-passe e segredos. Se passar dados confidenciais num objeto JSON, utilize o `secureObject` tipo. Parâmetros de modelo com a cadeia segura ou tipos de objeto segura não não possível ler após a implementação de recursos. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Não fornecem os valores predefinidos para nomes de utilizador, palavras-passe ou qualquer valor que requer uma `secureString` tipo.

* Não fornecem os valores predefinidos para as propriedades que aumentam a área de superfície de ataque do aplicativo.

### <a name="location-recommendations-for-parameters"></a>Recomendações de localização para os parâmetros

* Utilizar um parâmetro para especificar a localização para os recursos e defina o valor predefinido `resourceGroup().location`. Fornecer um parâmetro de localização permite aos utilizadores do modelo de especificar uma localização que tenham permissão para implementar.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Não especificar `allowedValues` para o parâmetro de localização. As localizações que especificar poderão não estar disponíveis em todas as nuvens.

* Utilize o valor do parâmetro de localização para os recursos que é provável que estejam na mesma localização. Essa abordagem minimiza o número de vezes que os utilizadores são solicitados a fornecer informações de localização.

* Para obter recursos que não estão disponíveis em todas as localizações, utilizar um parâmetro separado ou especificar um valor literal de localização.

## <a name="variables"></a>Variáveis

As seguintes informações podem ser úteis quando trabalha com [variáveis](resource-group-authoring-templates.md#variables):

* Utilize variáveis para valores que tem de utilizar mais do que uma vez num modelo. Se for utilizado um valor apenas uma vez, um valor embutido em torna seu modelo mais fácil de ler.

* Utilize variáveis para valores que constrói a partir de um arranjo complexo de funções de modelo. O modelo é mais fácil de ler quando a expressão complexa aparece apenas em variáveis.

* Não utilize variáveis para `apiVersion` num recurso. A versão de API determina o esquema do recurso. Muitas vezes, é possível alterar a versão sem alterar as propriedades para o recurso.

* Não é possível utilizar o [referência](resource-group-template-functions-resource.md#reference) funcionar a **variáveis** secção do modelo. O **referência** seu valor de estado de runtime do recurso é derivada de função. No entanto, as variáveis são resolvidas durante a análise inicial do modelo. Construção de valores essa necessidade a **referência** diretamente na função o **recursos** ou **produz** secção do modelo.

* Inclua variáveis para nomes de recursos que tem de ser exclusivos.

* Utilize um [ciclo de cópia em variáveis](resource-group-create-multiple.md#variable-iteration) para criar um padrão repetido de objetos JSON.

* Remova as variáveis não utilizadas.

## <a name="resource-dependencies"></a>Dependências de recursos

Ao decidir o que [dependências](resource-group-define-dependencies.md) para definir, utilize as seguintes diretrizes:

* Utilize o **referência** de função e transmita o nome de recurso para definir uma dependência implícita entre os recursos que precisam de partilhar uma propriedade. Não adicione explícita `dependsOn` elemento quando que já definiu uma dependência implícita. Essa abordagem reduz o risco de ter dependências desnecessárias.

* Defina um recurso de subordinado como dependente de seu recurso principal.

* Recursos com o [elemento condition](resource-group-authoring-templates.md#condition) definido como false são automaticamente removidos da ordem de dependência. Defina as dependências como se o recurso está implementado sempre.

* Permitir que as dependências cascata sem definir explicitamente. Por exemplo, sua máquina virtual depende de uma interface de rede virtual e a interface de rede virtual depende de uma rede virtual e endereços IP públicos. Por conseguinte, a máquina virtual está implementados Afinal de contas três recursos, mas não for definida explicitamente a máquina virtual como dependentes em todos os recursos de três. Esta abordagem esclarece a ordem de dependência e torna mais fácil de alterar o modelo mais tarde.

* Se um valor pode ser determinado antes da implantação, experimente a implementar o recurso sem uma dependência. Por exemplo, se um valor de configuração tem o nome do recurso de outro, não poderá ter uma dependência. Esta orientação nem sempre funciona porque alguns recursos verificar a existência do outro recurso. Se receber um erro, adicione uma dependência.

## <a name="resources"></a>Recursos

As seguintes informações podem ser úteis quando trabalha com [recursos](resource-group-authoring-templates.md#resources):

* Para ajudar a outros contribuintes compreender a finalidade do recurso, especifique **comentários** para cada recurso do modelo:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Se utilizar um *ponto final público* no seu modelo (por exemplo, um Blob do Azure armazenamento ponto final público), *não embutir* o espaço de nomes. Utilize o **referência** função para recuperar dinamicamente o espaço de nomes. Pode utilizar esta abordagem para implementar o modelo em ambientes de outro namespace público sem alterar manualmente o ponto final no modelo. Defina a versão de API para a mesma versão que está a utilizar para a conta de armazenamento no seu modelo:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Se a conta de armazenamento é implementada no mesmo modelo em que está a criar, não precisa de especificar o espaço de nomes do fornecedor quando referencia o recurso. O exemplo seguinte mostra a sintaxe simplificada:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Se tiver outros valores no seu modelo que estão configurados para utilizar um espaço de nomes público, alterar estes valores para refletir o mesmo **referência** função. Por exemplo, pode definir o **storageUri** propriedade do perfil de diagnóstico de máquina virtual:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Também pode fazer referência a uma conta de armazenamento existente que esteja no grupo de recursos diferente:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Atribua endereços IP públicos a uma máquina virtual apenas quando um aplicativo exigi-lo. Para ligar a uma máquina virtual (VM) de depuração ou de gestão ou fins administrativos, use regras NAT de entrada, um gateway de rede virtual ou uma jumpbox.
   
     Para obter mais informações sobre como ligar a máquinas virtuais, consulte:
   
   * [Executar VMs para uma arquitetura de N camadas no Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configurar o acesso ao WinRM para VMs no Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Permitir acesso externo à sua VM com o portal do Azure](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Permitir acesso externo à sua VM com o PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Permitir acesso externo a sua VM do Linux ao utilizar a CLI do Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* O **domainNameLabel** propriedade para endereços IP públicos têm de ser exclusiva. O **domainNameLabel** valor tem de ter entre 3 e 63 carateres de comprimento e siga as regras especificadas por essa expressão regular: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Uma vez que o **uniqueString** função gera uma cadeia de 13 carateres de comprimento, o **dnsPrefixString** parâmetro é limitado a 50 carateres:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Quando adiciona uma palavra-passe para uma extensão de script personalizado, utilize o **commandToExecute** propriedade na **protectedSettings** propriedade:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Para garantir que os segredos são encriptados quando são transmitidos como parâmetros para VMs e as extensões, utilize o **protectedSettings** propriedade das extensões relevantes.
   > 
   > 

## <a name="outputs"></a>Saídas

Se utilizar um modelo para criar os endereços IP públicos, incluir uma [produz secção](resource-group-authoring-templates.md#outputs) que devolve detalhes do endereço IP e o nome de domínio completamente qualificado (FQDN). Pode utilizar valores de saída facilmente obter detalhes sobre os endereços IP públicos e FQDNs após a implementação.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre a estrutura do ficheiro de modelo do Resource Manager, consulte [compreender a estrutura e a sintaxe de modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para obter recomendações sobre como criar modelos que funcionam em todos os ambientes de cloud do Azure, veja [modelos de desenvolver o Azure Resource Manager para manter a consistência na cloud](templates-cloud-consistency.md).
