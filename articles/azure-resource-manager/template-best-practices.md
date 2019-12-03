---
title: Melhores práticas para modelos
description: Descreve as abordagens recomendadas para a criação de modelos de Azure Resource Manager. Oferece sugestões para evitar problemas comuns ao usar modelos.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d4cf4364b2e835db3d53fa64682a99710ceb2b29
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689104"
---
# <a name="azure-resource-manager-template-best-practices"></a>Práticas recomendadas do modelo de Azure Resource Manager

Este artigo fornece recomendações sobre como construir seu modelo do Resource Manager. Essas recomendações ajudam a evitar problemas comuns ao usar um modelo para implantar uma solução.

Para obter recomendações sobre como controlar suas assinaturas do Azure, consulte [Azure Enterprise Scaffold: governança de assinatura prescritiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Para obter recomendações sobre como criar modelos que funcionam em todos os ambientes de nuvem do Azure, consulte [desenvolver modelos de Azure Resource Manager para consistência de nuvem](templates-cloud-consistency.md).

## <a name="template-limits"></a>Limites de modelo

Limite o tamanho do modelo para 4 MB e cada arquivo de parâmetro para 64 KB. O limite de 4 MB se aplica ao estado final do modelo depois que ele é expandido com definições de recursos iterativos e valores para variáveis e parâmetros. 

Você também está limitado a:

* parâmetros de 256
* 256 variáveis
* 800 recursos (incluindo a contagem de cópia)
* 64 valores de saída
* 24.576 caracteres em uma expressão de modelo

Você pode exceder alguns limites de modelo usando um modelo aninhado. Para obter mais informações, consulte [usando modelos vinculados ao implantar recursos do Azure](resource-group-linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, você pode combinar vários valores em um objeto. Para obter mais informações, consulte [objetos como parâmetros](resource-manager-objects-as-parameters.md).

## <a name="resource-group"></a>Grupo de recursos

Quando você implanta recursos em um grupo de recursos, o grupo de recursos armazena metadados sobre os recursos. Os metadados são armazenados no local do grupo de recursos.

Se a região do grupo de recursos estiver temporariamente indisponível, você não poderá atualizar recursos no grupo de recursos porque os metadados não estão disponíveis. Os recursos em outras regiões ainda funcionarão conforme o esperado, mas você não poderá atualizá-los. Para minimizar o risco, localize o grupo de recursos e os recursos na mesma região.

## <a name="parameters"></a>Parâmetros

As informações contidas nesta seção podem ser úteis quando você trabalha com [parâmetros](template-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Recomendações gerais para parâmetros

* Minimize o uso de parâmetros. Em vez disso, use variáveis ou valores literais para propriedades que não precisam ser especificadas durante a implantação.

* Use o camel case para nomes de parâmetro.

* Use parâmetros para configurações que variam de acordo com o ambiente, como SKU, tamanho ou capacidade.

* Use parâmetros para nomes de recursos que você deseja especificar para facilitar a identificação.

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

* Defina valores padrão para parâmetros que não são confidenciais. Ao especificar um valor padrão, é mais fácil implantar o modelo e os usuários de seu modelo veem um exemplo de um valor apropriado. Qualquer valor padrão para um parâmetro deve ser válido para todos os usuários na configuração de implantação padrão. 
   
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

* Para especificar um parâmetro opcional, não use uma cadeia de caracteres vazia como um valor padrão. Em vez disso, use um valor literal ou uma expressão de linguagem para construir um valor.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Não use um parâmetro para a versão de API para um tipo de recurso. As propriedades e os valores do recurso podem variar por número de versão. O IntelliSense em um editor de código não pode determinar o esquema correto quando a versão da API está definida como um parâmetro. Em vez disso, codifique a versão da API no modelo.

* Use `allowedValues` com moderação. Use-o somente quando você tiver certeza de que alguns valores não estão incluídos nas opções permitidas. Se você usar `allowedValues` muito amplamente, poderá bloquear implantações válidas não mantendo sua lista atualizada.

* Quando um nome de parâmetro em seu modelo corresponde a um parâmetro no comando de implantação do PowerShell, o Resource Manager resolve esse conflito de nomenclatura adicionando o sufixo **detemplate** ao parâmetro de modelo. Por exemplo, se você incluir um parâmetro chamado **ResourceGroupName** em seu modelo, ele entrará em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) . Durante a implantação, você será solicitado a fornecer um valor para **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Recomendações de segurança para parâmetros

* Sempre use parâmetros para nomes de usuário e senhas (ou segredos).

* Use `securestring` para todas as senhas e segredos. Se você passar dados confidenciais em um objeto JSON, use o tipo de `secureObject`. Parâmetros de modelo com tipos de objetos seguros ou de cadeia de caracteres segura não podem ser lidos após a implantação de recursos. 
   
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

* Não forneça valores padrão para nomes de usuário, senhas ou qualquer valor que exija um tipo de `secureString`.

* Não forneça valores padrão para propriedades que aumentem a área da superfície de ataque do aplicativo.

### <a name="location-recommendations-for-parameters"></a>Recomendações de local para parâmetros

* Use um parâmetro para especificar o local dos recursos e defina o valor padrão como `resourceGroup().location`. Fornecer um parâmetro Location permite que os usuários do modelo especifiquem um local para o qual eles têm permissão para implantar.

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

* Não especifique `allowedValues` para o parâmetro Location. Os locais especificados podem não estar disponíveis em todas as nuvens.

* Use o valor do parâmetro Location para recursos que provavelmente estejam no mesmo local. Essa abordagem minimiza o número de vezes que os usuários são solicitados a fornecer informações de local.

* Para recursos que não estão disponíveis em todos os locais, use um parâmetro separado ou especifique um valor de local literal.

## <a name="variables"></a>Variáveis

As informações a seguir podem ser úteis quando você trabalha com [variáveis](template-variables.md):

* Use o camel case para nomes de variáveis.

* Use variáveis para valores que você precisa usar mais de uma vez em um modelo. Se um valor for usado apenas uma vez, um valor embutido em código tornará seu modelo mais fácil de ler.

* Use variáveis para valores que você constrói de uma organização complexa de funções de modelo. Seu modelo é mais fácil de ler quando a expressão complexa só aparece em variáveis.

* Não use variáveis para `apiVersion` em um recurso. A versão da API determina o esquema do recurso. Geralmente, você não pode alterar a versão sem alterar as propriedades do recurso.

* Você não pode usar a função de [referência](resource-group-template-functions-resource.md#reference) na seção **variáveis** do modelo. A função de **referência** deriva seu valor do estado de tempo de execução do recurso. No entanto, as variáveis são resolvidas durante a análise inicial do modelo. Construa valores que precisam da função de **referência** diretamente na seção de **recursos** ou **saídas** do modelo.

* Inclua variáveis para nomes de recursos que devem ser exclusivos.

* Use um [loop de cópia em variáveis](resource-group-create-multiple.md#variable-iteration) para criar um padrão repetido de objetos JSON.

* Remova as variáveis não utilizadas.

## <a name="resource-dependencies"></a>Dependências de recursos

Ao decidir quais [dependências](resource-group-define-dependencies.md) definir, use as seguintes diretrizes:

* Use a função de **referência** e passe o nome do recurso para definir uma dependência implícita entre os recursos que precisam compartilhar uma propriedade. Não adicione um elemento de `dependsOn` explícito quando você já tiver definido uma dependência implícita. Essa abordagem reduz o risco de ter dependências desnecessárias.

* Defina um recurso filho como dependente de seu recurso pai.

* Os recursos com o [elemento Condition](conditional-resource-deployment.md) definido como false são removidos automaticamente da ordem de dependência. Defina as dependências como se o recurso sempre for implantado.

* Deixe as dependências em cascata sem defini-las explicitamente. Por exemplo, sua máquina virtual depende de um adaptador de rede virtual e a interface de rede virtual depende de uma rede virtual e de endereços IP públicos. Portanto, a máquina virtual é implantada após todos os três recursos, mas não defina explicitamente a máquina virtual como dependente de todos os três recursos. Essa abordagem esclarece a ordem de dependência e facilita a alteração do modelo posteriormente.

* Se um valor puder ser determinado antes da implantação, tente implantar o recurso sem uma dependência. Por exemplo, se um valor de configuração precisar do nome de outro recurso, talvez você não precise de uma dependência. Essa orientação nem sempre funciona porque alguns recursos verificam a existência do outro recurso. Se você receber um erro, adicione uma dependência.

## <a name="resources"></a>Recursos

As informações a seguir podem ser úteis quando você trabalha com [recursos](resource-group-authoring-templates.md#resources):

* Para ajudar outros colaboradores a entender a finalidade do recurso, especifique **comentários** para cada recurso no modelo:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-06-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Se você usar um *ponto de extremidade público* em seu modelo (como um ponto de extremidade público do armazenamento de BLOBs do Azure), *não codifique* o namespace. Use a função de **referência** para recuperar dinamicamente o namespace. Você pode usar essa abordagem para implantar o modelo em diferentes ambientes de namespace público sem alterar manualmente o ponto de extremidade no modelo. Defina a versão da API para a mesma versão que você está usando para a conta de armazenamento em seu modelo:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Se a conta de armazenamento for implantada no mesmo modelo que você está criando e o nome da conta de armazenamento não for compartilhado com outro recurso no modelo, você não precisará especificar o namespace do provedor ou o apiVersion quando fizer referência ao recurso. O exemplo a seguir mostra a sintaxe simplificada:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
       }
   }
   ```
     
   Você também pode fazer referência a uma conta de armazenamento existente que esteja em um grupo de recursos diferente:

   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```

* Atribua endereços IP públicos a uma máquina virtual somente quando um aplicativo o exigir. Para se conectar a uma VM (máquina virtual) para depuração, ou para fins administrativos ou de gerenciamento, use regras NAT de entrada, um gateway de rede virtual ou um Jumpbox.
   
     Para obter mais informações sobre como se conectar a máquinas virtuais, consulte:
   
   * [Executar VMs para uma arquitetura de N camadas no Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configurar o acesso do WinRM para VMs no Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Permitir acesso externo à sua VM usando o portal do Azure](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Permitir acesso externo à sua VM usando o PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Permitir acesso externo à sua VM Linux usando CLI do Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* A propriedade **domainNameLabel** para endereços IP públicos deve ser exclusiva. O valor de **domainNameLabel** deve ter entre 3 e 63 caracteres de comprimento e seguir as regras especificadas por esta expressão regular: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Como a função **uniquestring** gera uma cadeia de caracteres com 13 caracteres de comprimento, o parâmetro **dnsPrefixString** é limitado a 50 caracteres:

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

* Quando você adiciona uma senha a uma extensão de script personalizado, use a propriedade **commandToExecute** na propriedade **protectedSettings** :
   
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
   > Para garantir que os segredos sejam criptografados quando forem passados como parâmetros para VMs e extensões, use a propriedade **protectedSettings** das extensões relevantes.
   > 
   > 

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a estrutura do arquivo de modelo do Resource Manager, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](resource-group-authoring-templates.md).
* Para obter recomendações sobre como criar modelos que funcionam em todos os ambientes de nuvem do Azure, consulte [desenvolver modelos de Azure Resource Manager para consistência de nuvem](templates-cloud-consistency.md).
