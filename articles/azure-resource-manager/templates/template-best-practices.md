---
title: Melhores práticas de modelos
description: Descreve abordagens recomendadas para a autoria de modelos do Gestor de Recursos Azure. Oferece sugestões para evitar problemas comuns ao usar modelos.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 870636d6457d842c89f261c2537644c17a335294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156417"
---
# <a name="arm-template-best-practices"></a>As melhores práticas do modelo ARM

Este artigo dá recomendações sobre como construir o seu modelo de Gestor de Recursos Azure (ARM). Estas recomendações ajudam-no a evitar problemas comuns ao utilizar um modelo ARM para implementar uma solução.

Para recomendações sobre como governar as suas subscrições do Azure, consulte [o andaime da empresa Azure: Prescritiva de governação por subscrição](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Para recomendações sobre como construir modelos que funcionem em todos os ambientes de nuvem Azure, consulte desenvolver modelos de [Gestor de Recursos Azure para consistência na nuvem](templates-cloud-consistency.md).

## <a name="template-limits"></a>Limites de modelo

Limite o tamanho do seu modelo a 4 MB, e cada ficheiro de parâmetro saque a 64 KB. O limite de 4 MB aplica-se ao estado final do modelo depois de ter sido expandido com definições de recursos iterativos, e valores para variáveis e parâmetros. 

Também está limitado a:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo a contagem de cópias)
* 64 valores de saída
* 24.576 caracteres em uma expressão de modelo

Pode exceder alguns limites de modelo usando um modelo aninhado. Para mais informações, consulte [A utilização de modelos ligados ao utilizar recursos Azure](linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, pode combinar vários valores num objeto. Para mais informações, consulte [objetos como parâmetros](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="resource-group"></a>Grupo de recursos

Quando se desloque recursos a um grupo de recursos, o grupo de recursos armazena metadados sobre os recursos. Os metadados são armazenados na localização do grupo de recursos.

Se a região do grupo de recursos estiver temporariamente indisponível, não pode atualizar os recursos no grupo de recursos porque os metadados não estão disponíveis. Os recursos noutras regiões continuarão a funcionar como esperado, mas não é possível atualizá-los. Para minimizar o risco, localize o seu grupo de recursos e recursos na mesma região.

## <a name="parameters"></a>Parâmetros

A informação nesta secção pode ser útil quando trabalha com [parâmetros](template-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Recomendações gerais para parâmetros

* Minimize o uso de parâmetros. Em vez disso, utilize variáveis ou valores literais para propriedades que não precisam de ser especificadas durante a implementação.

* Use a caixa de camelo para nomes de parâmetros.

* Utilize parâmetros para configurações que variam de acordo com o ambiente, como SKU, tamanho ou capacidade.

* Utilize parâmetros para nomes de recursos que pretende especificar para uma identificação fácil.

* Forneça uma descrição de todos os parâmetros dos metadados:

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

* Defina valores predefinidos para parâmetros que não sejam sensíveis. Ao especificar um valor predefinido, é mais fácil implementar o modelo, e os utilizadores do seu modelo vêem um exemplo de um valor apropriado. Qualquer valor predefinido para um parâmetro deve ser válido para todos os utilizadores na configuração de implementação predefinida. 
   
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

* Não utilize um parâmetro para a versão API para um tipo de recurso. As propriedades e valores dos recursos podem variar em função do número da versão. O IntelliSense num editor de código não consegue determinar o esquema correto quando a versão API está definida para um parâmetro. Em vez disso, código rígido a versão API no modelo.

* Use `allowedValues` com moderação. Utilize-o apenas quando tiver de se certificar de que alguns valores não estão incluídos nas opções permitidas. Se utilizar `allowedValues` de forma demasiado ampla, poderá bloquear implementações válidas não mantendo a sua lista atualizada.

* Quando um nome de parâmetro no seu modelo corresponde a um parâmetro no comando de implementação powerShell, o Gestor de Recursos resolve este conflito de nomeação adicionando o modelo de pós-fixação **FromTemplate.** Por exemplo, se incluir um parâmetro chamado **ResourceGroupName** no seu modelo, entra em conflito com o parâmetro **ResourceGroupName** no cmdlet [new-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Durante a implementação, é-lhe solicitado que forneça um valor para **o ResourceGroupNameFromFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Recomendações de segurança para parâmetros

* Utilize sempre parâmetros para nomes de utilizadores e palavras-passe (ou segredos).

* Use `securestring` para todas as senhas e segredos. Se passar dados sensíveis num objeto JSON, utilize o `secureObject` tipo. Os parâmetros do modelo com cordas seguras ou tipos de objetos seguros não podem ser lidos após a implementação do recurso. 
   
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

* Não forneça valores predefinidos para nomes de utilizadores, `secureString` palavras-passe ou qualquer valor que exija um tipo.

* Não forneça valores predefinidos para propriedades que aumentem a área de superfície de ataque da aplicação.

### <a name="location-recommendations-for-parameters"></a>Recomendações de localização para parâmetros

* Utilize um parâmetro para especificar a localização dos recursos `resourceGroup().location`e defina o valor predefinido para . O fornecimento de um parâmetro de localização permite aos utilizadores do modelo especificar uma localização para a qual têm permissão para implantar.

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

* Não especifique `allowedValues` o parâmetro de localização. Os locais que especifica podem não estar disponíveis em todas as nuvens.

* Utilize o valor do parâmetro de localização para recursos que possam estar no mesmo local. Esta abordagem minimiza o número de vezes que os utilizadores são convidados a fornecer informações de localização.

* Para recursos que não estejam disponíveis em todos os locais, use um parâmetro separado ou especifique um valor de localização literal.

## <a name="variables"></a>Variáveis

As seguintes informações podem ser úteis quando se trabalha com [variáveis:](template-variables.md)

* Use a caixa de camelo para nomes variáveis.

* Utilize variáveis para valores que precisa de usar mais do que uma vez num modelo. Se um valor for utilizado apenas uma vez, um valor codificado facilita a leitura do seu modelo.

* Utilize variáveis para valores que constrói a partir de um arranjo complexo de funções de modelo. O seu modelo é mais fácil de ler quando a expressão complexa só aparece em variáveis.

* Não use variáveis `apiVersion` num recurso. A versão API determina o esquema do recurso. Muitas vezes, não se pode alterar a versão sem alterar as propriedades para o recurso.

* Não pode utilizar a função de [referência](template-functions-resource.md#reference) na secção **variáveis** do modelo. A função **de referência** obtém o seu valor a partir do estado de funcionamento do recurso. No entanto, as variáveis são resolvidas durante a análise inicial do modelo. Construa valores que necessitem da função de **referência** diretamente na secção de **recursos** ou **saídas** do modelo.

* Inclua variáveis para nomes de recursos que devem ser únicos.

* Utilize um ciclo de [cópia em variáveis](copy-variables.md) para criar um padrão repetido de objetos JSON.

* Remova variáveis não utilizadas.

## <a name="resource-dependencies"></a>Dependências de recursos

Ao decidir quais [as dependências](define-resource-dependency.md) a definir, utilize as seguintes orientações:

* Utilize a função **de referência** e passe no nome do recurso para definir uma dependência implícita entre os recursos que precisam de partilhar um imóvel. Não adicione um `dependsOn` elemento explícito quando já definiu uma dependência implícita. Esta abordagem reduz o risco de ter dependências desnecessárias.

* Desloque um recurso para crianças como dependente do seu recurso principal.

* Os recursos com o [elemento de condição](conditional-resource-deployment.md) definido para falso são automaticamente removidos da ordem de dependência. Detete as dependências como se o recurso estivesse sempre implantado.

* Deixe as dependências cascatarem sem as definir explicitamente. Por exemplo, a sua máquina virtual depende de uma interface de rede virtual, e a interface de rede virtual depende de uma rede virtual e endereços IP públicos. Portanto, a máquina virtual é implantada após os três recursos, mas não configura explicitamente a máquina virtual como dependente dos três recursos. Esta abordagem clarifica a ordem de dependência e facilita a mudança do modelo mais tarde.

* Se um valor puder ser determinado antes da implantação, tente implementar o recurso sem dependência. Por exemplo, se um valor de configuração precisar do nome de outro recurso, pode não precisar de uma dependência. Esta orientação nem sempre funciona porque alguns recursos verificam a existência do outro recurso. Se receber um erro, adicione uma dependência.

## <a name="resources"></a>Recursos

As seguintes informações podem ser úteis quando trabalha com [recursos:](template-syntax.md#resources)

* Para ajudar outros contribuintes a compreender o propósito do recurso, especifique **comentários** para cada recurso no modelo:
   
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

* Se utilizar um *ponto final público* no seu modelo (como um ponto final de armazenamento Azure Blob), não código *sinuoso* o espaço de nome. Utilize a função **de referência** para recuperar dinamicamente o espaço de nome. Pode utilizar esta abordagem para implementar o modelo para diferentes ambientes de espaço de nome público sem alterar manualmente o ponto final do modelo. Delineie a versão API para a mesma versão que está a usar para a conta de armazenamento no seu modelo:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Se a conta de armazenamento for implantada no mesmo modelo que está a criar e o nome da conta de armazenamento não for partilhado com outro recurso no modelo, não precisa especificar o espaço de nome do fornecedor ou a apiVersão quando refere o recurso. O exemplo que se segue mostra a sintaxe simplificada:
   
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

* Atribuir endereços IP públicos a uma máquina virtual apenas quando uma aplicação o exigir. Para ligar a uma máquina virtual (VM) para depuração, ou para fins de gestão ou administrativas, utilize regras nat de entrada, um gateway de rede virtual ou uma caixa de salto.
   
     Para obter mais informações sobre a ligação a máquinas virtuais, consulte:
   
   * [Executar VMs para uma arquitetura n-tier em Azure](../../guidance/guidance-compute-n-tier-vm.md)
   * [Configurar acesso WinRM para VMs em Gestor de Recursos Azure](../../virtual-machines/windows/winrm.md)
   * [Permitir o acesso externo ao seu VM utilizando o portal Azure](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Permitir o acesso externo ao seu VM utilizando o PowerShell](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Permitir o acesso externo ao seu Linux VM utilizando o Azure CLI](../../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* A propriedade **domainNameLabel** para endereços IP públicos deve ser única. O valor **domainNameLabel** deve ter entre 3 e 63 caracteres de comprimento `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`e seguir as regras especificadas por esta expressão regular: . Como a função **string única** gera uma cadeia com 13 caracteres de comprimento, o parâmetro **dnsPrefixString** está limitado a 50 caracteres:

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

* Quando adicionar uma palavra-passe a uma extensão de script personalizada, utilize a propriedade **commandToExecute** na propriedade **ProtectedSettings:**
   
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
   > Para garantir que os segredos são encriptados quando são passados como parâmetros para VMs e extensões, utilize a propriedade **protectedSettings** das extensões relevantes.
   > 
   > 

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a estrutura do ficheiro do modelo, consulte [Compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
* Para recomendações sobre como construir modelos que funcionem em todos os ambientes de nuvem Azure, consulte [Desenvolver modelos ARM para consistência da nuvem](templates-cloud-consistency.md).
