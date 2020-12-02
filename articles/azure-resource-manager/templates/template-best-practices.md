---
title: Melhores práticas de modelos
description: Descreve abordagens recomendadas para a autoria de modelos do Gestor de Recursos Azure. Oferece sugestões para evitar problemas comuns ao usar modelos.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: c62bde8fc8cfc79330d13b7b2ff4f778dadf1339
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497984"
---
# <a name="arm-template-best-practices"></a>As melhores práticas do modelo ARM

Este artigo mostra-lhe como utilizar as práticas recomendadas ao construir o seu modelo ARM. Estas recomendações ajudam-no a evitar problemas comuns ao utilizar um modelo ARM para implementar uma solução.

## <a name="template-limits"></a>Limites de modelo

Limite o tamanho do seu modelo a 4 MB e cada ficheiro de parâmetro a 64 KB. O limite de 4-MB aplica-se ao estado final do modelo depois de ter sido expandido com definições de recursos iterativos e valores para variáveis e parâmetros.

Também se limita a:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo a contagem de cópias)
* 64 valores de saída
* 24.576 caracteres em uma expressão de modelo

Pode exceder alguns limites de modelo usando um modelo aninhado. Para obter mais informações, consulte [utilizando modelos ligados ao implementar recursos Azure](linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, pode combinar vários valores num objeto. Para obter mais informações, consulte [os Objetos como parâmetros.](/azure/architecture/building-blocks/extending-templates/objects-as-parameters)

## <a name="resource-group"></a>Grupo de recursos

Quando implementa recursos para um grupo de recursos, o grupo de recursos armazena metadados sobre os recursos. Os metadados são armazenados na localização do grupo de recursos.

Se a região do grupo de recursos estiver temporariamente indisponível, não é possível atualizar recursos no grupo de recursos porque os metadados não estão disponíveis. Os recursos noutras regiões continuarão a funcionar como esperado, mas não é possível atualizá-los. Para minimizar o risco, localize o seu grupo de recursos e recursos na mesma região.

## <a name="parameters"></a>Parâmetros

As informações nesta secção podem ser úteis quando se trabalha com [parâmetros](template-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Recomendações gerais para parâmetros

* Minimize o uso de parâmetros. Em vez disso, utilize variáveis ou valores literais para propriedades que não precisam de ser especificadas durante a implantação.

* Use a caixa de camelo para nomes de parâmetros.

* Utilize parâmetros para as definições que variam de acordo com o ambiente, como o SKU, o tamanho ou a capacidade.

* Utilize parâmetros para nomes de recursos que pretende especificar para uma identificação fácil.

* Fornecer uma descrição de todos os parâmetros nos metadados:

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

* Defina valores predefinidos para parâmetros que não são sensíveis. Ao especificar um valor padrão, é mais fácil implementar o modelo, e os utilizadores do seu modelo vêem um exemplo de um valor apropriado. Qualquer valor predefinido para um parâmetro deve ser válido para todos os utilizadores na configuração de implementação predefinido. 
   
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

* Para especificar um parâmetro opcional, não utilize uma corda vazia como valor predefinido. Em vez disso, use um valor literal ou uma expressão linguística para construir um valor.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Use `allowedValues` com moderação. Use-o apenas quando tiver de se certificar de que alguns valores não estão incluídos nas opções permitidas. Se utilizar `allowedValues` de forma demasiado ampla, poderá bloquear implementações válidas não mantendo a sua lista atualizada.

* Quando um nome de parâmetro no seu modelo corresponde a um parâmetro no comando de implementação PowerShell, o Gestor de Recursos resolve este conflito de nomeação adicionando o pós-fixo **FromTemplate** ao parâmetro do modelo. Por exemplo, se incluir um parâmetro chamado **ResourceGroupName** no seu modelo, ele entra em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Durante a implementação, é solicitado que forneça um valor para **o ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Recomendações de segurança para parâmetros

* Utilize sempre parâmetros para nomes de utilizador e palavras-passe (ou segredos).

* Use `securestring` para todas as palavras-passe e segredos. Se passar dados sensíveis num objeto JSON, utilize o `secureObject` tipo. Os parâmetros do modelo com tipos de conjuntos seguros ou de objetos seguros não podem ser lidos após a implementação do recurso. 
   
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

* Não forneça valores predefinidos para nomes de utilizador, palavras-passe ou qualquer valor que exija um `secureString` tipo.

* Não forneça valores padrão para propriedades que aumentem a área da superfície de ataque da aplicação.

### <a name="location-recommendations-for-parameters"></a>Recomendações de localização para parâmetros

* Utilize um parâmetro para especificar a localização dos recursos e desafine o valor predefinido para `resourceGroup().location` . Fornecer um parâmetro de localização permite aos utilizadores do modelo especificar um local para o qual têm permissão para implementar.

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

* Não especifique `allowedValues` o parâmetro de localização. As localizações que especifica podem não estar disponíveis em todas as nuvens.

* Utilize o valor do parâmetro de localização para os recursos que são suscetíveis de estar no mesmo local. Esta abordagem minimiza o número de vezes que os utilizadores são convidados a fornecer informações sobre a localização.

* Para recursos que não estão disponíveis em todos os locais, use um parâmetro separado ou especifique um valor de localização literal.

## <a name="variables"></a>Variáveis

As seguintes informações podem ser úteis quando se trabalha com [variáveis:](template-variables.md)

* Use a caixa de camelo para nomes variáveis.

* Use variáveis para valores que precisa usar mais do que uma vez num modelo. Se um valor for utilizado apenas uma vez, um valor codificado por código rígido facilita a leitura do seu modelo.

* Use variáveis para valores que constrói a partir de um arranjo complexo de funções de modelo. O seu modelo é mais fácil de ler quando a expressão complexa só aparece em variáveis.

* Não é possível utilizar a função [de referência](template-functions-resource.md#reference) na secção de **variáveis** do modelo. A função de **referência** obtém o seu valor a partir do estado de execução do recurso. No entanto, as variáveis são resolvidas durante a análise inicial do modelo. Construa valores que necessitem da função **de referência** diretamente na secção de **recursos** ou **saídas** do modelo.

* Incluir variáveis para nomes de recursos que devem ser únicos.

* Utilize um [laço de cópia em variáveis](copy-variables.md) para criar um padrão repetido de objetos JSON.

* Remova as variáveis não ususadas.

## <a name="api-version"></a>Versão API

Desaprova a `apiVersion` propriedade numa versão API codificada por código rígido para o tipo de recurso. Ao criar um novo modelo, recomendamos que utilize a versão API mais recente para um tipo de recurso. Para determinar os valores disponíveis, consulte [a referência do modelo](/azure/templates/).

Quando o seu modelo funcionar como esperado, recomendamos que continue a usar a mesma versão API. Ao utilizar a mesma versão API, não tem de se preocupar em quebrar alterações que possam ser introduzidas em versões posteriores.

Não utilize um parâmetro para a versão API. As propriedades e valores dos recursos podem variar de acordo com a versão API. O IntelliSense num editor de código não consegue determinar o esquema correto quando a versão API está definida como parâmetro. Se passar numa versão API que não corresponde às propriedades do seu modelo, a implementação falhará.

Não utilize variáveis para a versão API. Em particular, não utilize a [função dos fornecedores](template-functions-resource.md#providers) para obter dinamicamente versões API durante a implementação. A versão API recuperada dinamicamente pode não corresponder às propriedades do seu modelo.

## <a name="resource-dependencies"></a>Dependências de recursos

Ao decidir quais [as dependências](define-resource-dependency.md) a definir, utilize as seguintes orientações:

* Use a função **de referência** e passe no nome do recurso para definir uma dependência implícita entre recursos que precisam de partilhar um imóvel. Não adicione um elemento explícito `dependsOn` quando já definiu uma dependência implícita. Esta abordagem reduz o risco de ter dependências desnecessárias. Para um exemplo de definição de uma dependência implícita, consulte [a dependência implícita](define-resource-dependency.md#reference-and-list-functions).

* Desaprote um recurso para crianças como dependente do seu recurso principal.

* Os recursos com o [elemento de condição](conditional-resource-deployment.md) definido para falso são automaticamente removidos da ordem de dependência. Desloque as dependências como se o recurso fosse sempre implantado.

* Deixe as dependências em cascata sem as definir explicitamente. Por exemplo, a sua máquina virtual depende de uma interface de rede virtual, e a interface de rede virtual depende de uma rede virtual e endereços IP públicos. Portanto, a máquina virtual é implantada após os três recursos, mas não definir explicitamente a máquina virtual como dependente dos três recursos. Esta abordagem clarifica a ordem de dependência e facilita a alteração do modelo mais tarde.

* Se um valor puder ser determinado antes da implantação, tente implementar o recurso sem dependência. Por exemplo, se um valor de configuração precisar do nome de outro recurso, pode não precisar de uma dependência. Esta orientação nem sempre funciona porque alguns recursos verificam a existência do outro recurso. Se receber um erro, adicione uma dependência.

## <a name="resources"></a>Recursos

As seguintes informações podem ser úteis quando se trabalha com [recursos:](template-syntax.md#resources)

* Para ajudar outros contribuintes a compreender a finalidade do recurso, **especifique comentários** para cada recurso no modelo:
   
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

* Se utilizar um *ponto final público* no seu modelo (como um ponto final público de armazenamento Azure Blob), não *codigem* o espaço de nomes. Utilize a função **de referência** para recuperar dinamicamente o espaço de nomes. Pode utilizar esta abordagem para implementar o modelo em diferentes ambientes do espaço de nome público sem alterar manualmente o ponto final no modelo. Desaça a versão API para a mesma versão que está a usar para a conta de armazenamento no seu modelo:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Se a conta de armazenamento for implantada no mesmo modelo que está a criar e o nome da conta de armazenamento não for partilhado com outro recurso no modelo, não precisa de especificar o espaço de nome do fornecedor ou a apiversão quando referenciar o recurso. O exemplo a seguir mostra a sintaxe simplificada:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
       }
   }
   ```
     
   Também pode fazer referência a uma conta de armazenamento existente que se encontra num grupo de recursos diferente:

   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```

* Atribua endereços IP públicos a uma máquina virtual apenas quando uma aplicação o exigir. Para ligar a uma máquina virtual (VM) para depurar, ou para fins de gestão ou administrativa, utilize regras NAT de entrada, um gateway de rede virtual ou uma caixa de salto.
   
     Para obter mais informações sobre a ligação a máquinas virtuais, consulte:
   
   * [Executar VMs para uma arquitetura n-tier em Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
   * [Configurar o acesso winrm para VMs em Azure Resource Manager](../../virtual-machines/windows/winrm.md)
   * [Permitir o acesso externo ao seu VM utilizando o portal Azure](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Permitir o acesso externo ao seu VM utilizando o PowerShell](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Permitir o acesso externo ao seu Linux VM utilizando o Azure CLI](../../virtual-machines/linux/nsg-quickstart.md)

* O **domínio Propriedade DeNameLabel** para endereços IP públicos deve ser único. O **valor de domínioNabel** deve ter entre 3 e 63 caracteres de comprimento e seguir as regras especificadas por esta expressão regular: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` . Como a função **un uniqueString** gera uma cadeia de 13 caracteres de comprimento, o parâmetro **dnsPrefixString** está limitado a 50 caracteres:

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

* Quando adicionar uma palavra-passe a uma extensão de script personalizada, utilize a propriedade **commandToExecute** na propriedade **protectedSettings:**
   
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
   > Para garantir que os segredos são encriptados quando são passados como parâmetros para VMs e extensões, use a propriedade **protectedSettings** das extensões relevantes.
   > 

## <a name="use-test-toolkit"></a>Use o kit de ferramentas de teste

O kit de ferramentas de teste do modelo ARM é um script que verifica se o seu modelo utiliza práticas recomendadas. Quando o seu modelo não está em conformidade com as práticas recomendadas, devolve uma lista de avisos com alterações sugeridas. O kit de ferramentas de teste pode ajudá-lo a aprender a implementar as melhores práticas no seu modelo.

Depois de ter concluído o seu modelo, execute o kit de ferramentas de teste para ver se existem formas de melhorar a sua implementação. Para obter mais informações, consulte [o kit de ferramentas de teste do modelo ARM](test-toolkit.md).

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a estrutura do ficheiro do modelo, consulte [compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
* Para recomendações sobre como construir modelos que funcionam em todos os ambientes de nuvem Azure, consulte [modelos de DESENVOLVIMENTO ARM para a consistência da nuvem.](templates-cloud-consistency.md)