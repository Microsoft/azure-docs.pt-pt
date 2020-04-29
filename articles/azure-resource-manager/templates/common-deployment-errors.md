---
title: Resolver erros de implementação comuns
description: Descreve como resolver erros comuns quando implementa recursos para o Azure utilizando o Gestor de Recursos Azure.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: bc1568c53cdb5518f694d77a2f28f3cf77296ee2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460386"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Resolver problemas comuns de erros de implementação do Azure com o Azure Resource Manager

Este artigo descreve alguns erros comuns de implementação do Azure e fornece informações para resolver os erros. Se não encontrar o código de erro para o seu erro de implementação, consulte [localizar código](#find-error-code)de erro .

Se procura informações sobre um código de erro e essa informação não é fornecida neste artigo, avise-nos. Na parte inferior desta página, pode deixar feedback. O feedback é rastreado com problemas gitHub.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Códigos de erro

| Código de erro | Mitigação | Mais informações |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Siga as restrições de nomeação para contas de armazenamento. | [Resolver o nome da conta de armazenamento](error-storage-account-name.md) |
| Propriedade imobiliáriaNão Pode SerDefinida | Verifique as propriedades da conta de armazenamento disponível. | [armazenamentoContas](/azure/templates/microsoft.storage/storageaccounts) |
| Atribuição Falhada | O cluster ou região não tem recursos disponíveis ou não pode suportar o tamanho de VM solicitado. Retry o pedido mais tarde, ou solicitar um tamanho VM diferente. | [Questões de provisionamento e atribuição para problemas de](../../virtual-machines/linux/troubleshoot-deployment-new-vm.md)fornecimento e atribuição de linux, provisionamento e atribuição para falhas na atribuição de [Janelas](../../virtual-machines/windows/troubleshoot-deployment-new-vm.md) e [Troubleshoot](../../virtual-machines/troubleshooting/allocation-failure.md)|
| Outra OperaçãoInProgress | Aguarde que a operação simultânea esteja concluída. | |
| AutorizaçãoFalhada | A sua conta ou diretor de serviço não tem acesso suficiente para completar a implementação. Verifique a função a que a sua conta pertence e o seu acesso ao âmbito de implementação.<br><br>Poderá receber este erro quando um fornecedor de recursos necessário não estiver registado. | [Controlo de Acesso Baseado em Funções do Azure](../../role-based-access-control/role-assignments-portal.md)<br><br>[Resolver o registo](error-register-resource-provider.md) |
| BadRequest | Enviou valores de implantação que não correspondem ao esperado pelo Gestor de Recursos. Verifique a mensagem de estado interno para obter ajuda na resolução de problemas. | [Referência do modelo](/azure/templates/) e [locais suportados](resource-location.md) |
| Conflito | Está a pedir uma operação que não seja permitida no estado atual do recurso. Por exemplo, a redimensionamento do disco só é permitida quando se cria um VM ou quando o VM é deallo. | |
| ImplementaçãoActive AndUneditável | Aguarde a implantação simultânea deste grupo de recursos para completar. | |
| ImplementaçãoFailFailedCleanUp | Quando se implanta em modo completo, os recursos que não estejam no modelo são eliminados. Obtém este erro quando não tem permissões adequadas para eliminar todos os recursos que não estão no modelo. Para evitar o erro, altere o modo de implementação para incremental. | [Modos de implementação do Gestor de Recursos Azure](deployment-modes.md) |
| ImplantaçãoNameInvalidCharacters | O nome de implantação só pode conter letra, dígito, '-', '.' ou '_'. | |
| ImplantaçãoNameLengthLimitExceeded | Os nomes de implantação estão limitados a 64 caracteres.  | |
| Implantação Falhada | O erro de implementação Falhado é um erro geral que não fornece os detalhes necessários para resolver o erro. Procure nos detalhes do erro um código de erro que forneça mais informações. | [Encontre código de erro](#find-error-code) |
| DeploymentQuotaExceeded | Se atingir o limite de 800 implantações por grupo de recursos, elimine as implementações da história que já não são necessárias. | [Resolver erro quando a contagem de distribuição exceder 800](deployment-quota-exceeded.md) |
| DnsRecordInUse | O nome de registo DNS deve ser único. Introduza um nome diferente. | |
| Imagem Não Encontrada | Verifique as definições de imagem vM. |  |
| InUseSubnetCanCanOEliminado | Você pode obter este erro ao tentar atualizar um recurso, e o pedido é processado apagando e criando o recurso. Certifique-se de especificar todos os valores inalterados. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidaçãoTokenTenant | Obtenha acesso ao inquilino apropriado. Só pode obter o símbolo do inquilino a que a sua conta pertence. | |
| InválidoContentLink | Provavelmente tentou ligar-se a um modelo aninhado que não está disponível. Verifique duas vezes o URI que forneceu para o modelo aninhado. Se o modelo existir numa conta de armazenamento, certifique-se de que o URI está acessível. Talvez precises de passar um símbolo da SAS. Atualmente, não é possível ligar a um modelo que se encontra numa conta de armazenamento atrás de uma [firewall de Armazenamento Azure](../../storage/common/storage-network-security.md). Considere mover o seu modelo para outro repositório, como o GitHub. | [Modelos ligados](linked-templates.md) |
| Implementação inválida | Ao ser implantado ao nível de subscrição, forneceu um local diferente para um nome de implementação usado anteriormente. | [Implementações de nível de subscrição](deploy-to-subscription.md) |
| Parâmetro inválido | Um dos valores que forneceu para um recurso não corresponde ao valor esperado. Este erro pode resultar de muitas condições diferentes. Por exemplo, uma palavra-passe pode ser insuficiente, ou um nome blob pode estar incorreto. A mensagem de erro deve indicar qual o valor que tem de ser corrigido. | |
| InvalidRequestContent | Os valores de implantação incluem valores que não são reconhecidos, ou valores necessários estão faltando. Confirme os valores para o seu tipo de recurso. | [Referência de modelo](/azure/templates/) |
| Formulário de Pedido inválido | Ative o depuramento da exploração de depuração ao executar a implementação e verifique o conteúdo do pedido. | [Depuração de madeira](#enable-debug-logging) |
| Espaço Nomede Recursos Inválidos | Verifique o espaço de nome de recursos que especificou na propriedade do **tipo.** | [Referência de modelo](/azure/templates/) |
| Referência de Recursos Inválidos | O recurso ainda não existe ou é incorretamente referenciado. Verifique se precisa de adicionar uma dependência. Verifique se a sua utilização da função **de referência** inclui os parâmetros necessários para o seu cenário. | [Resolver dependências](error-not-found.md) |
| Recurso InválidoType | Verifique o tipo de recurso especificado na propriedade do **tipo.** | [Referência de modelo](/azure/templates/) |
| Inscrição InválidaRegistrationState | Registe a sua subscrição junto do fornecedor de recursos. | [Resolver o registo](error-register-resource-provider.md) |
| InvalidTemplate | Verifique se o seu modelo sintaxe está a verificar se há erros. | [Resolver modelo inválido](error-invalid-template.md) |
| Dependência circular de modelo inválido | Remover dependências desnecessárias. | [Resolver dependências circulares](error-invalid-template.md#circular-dependency) |
| LinkedAuthorizationFailed | Verifique se a sua conta pertence ao mesmo inquilino que o grupo de recursos para o qual está a implantar. | |
| LinkedInvalidPropertyId | A identificação do recurso para um recurso não está a resolver corretamente. Verifique se fornece todos os valores necessários para o ID de recursos, incluindo ID de subscrição, nome do grupo de recursos, tipo de recurso, nome de recurso-mãe (se necessário) e nome de recurso. | |
| Localização Necessária | Forneça uma localização para o recurso. | [Definir localização](resource-location.md) |
| Segmentos de Recursos desajustados | Certifique-se de que o recurso aninhado tem um número correto de segmentos no nome e no tipo. | [Resolver segmentos de recursos](error-invalid-template.md#incorrect-segment-lengths)
| Faltade registosForlocation | Verifique o estado de registo do fornecedor de recursos e as localizações suportadas. | [Resolver o registo](error-register-resource-provider.md) |
| Registo de Subscrições Desaparecidas | Registe a sua subscrição junto do fornecedor de recursos. | [Resolver o registo](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Verifique o estado de registo do fornecedor de recursos. | [Resolver o registo](error-register-resource-provider.md) |
| NotFound | Pode estar a tentar implantar um recurso dependente em paralelo com um recurso-mãe. Verifique se precisa adicionar uma dependência. | [Resolver dependências](error-not-found.md) |
| OperaçãoNotallowed | A implantação está a tentar uma operação que exceda a quota para a subscrição, grupo de recursos ou região. Se possível, reveja a sua implantação para se manter dentro das quotas. Caso contrário, considere pedir uma alteração às suas quotas. | [Resolver quotas](error-resource-quota.md) |
| ParentResourceNotFound | Certifique-se de que existe um recurso dos pais antes de criar os recursos infantis. | [Resolver o recurso dos pais](error-parent-resource.md) |
| PasswordTooLong | Pode ter selecionado uma palavra-passe com demasiados caracteres ou convertido o valor da sua palavra-passe para uma corda segura antes de a passar como parâmetro. Se o modelo incluir um parâmetro de **corda seguro,** não precisa converter o valor numa corda segura. Forneça o valor da palavra-passe como texto. |  |
| PrivateIPAddressInReservedRange | O endereço IP especificado inclui uma gama de endereços exigida pelo Azure. Altere o endereço IP para evitar o alcance reservado. | [Endereços IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | O endereço IP especificado está fora do intervalo da sub-rede. Alterar o endereço IP para se enquadrar no intervalo da sub-rede. | [Endereços IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Algumas propriedades não podem ser alteradas num recurso implantado. Ao atualizar um recurso, limite as alterações às propriedades permitidas. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | A sua subscrição inclui uma política de recursos que impede uma ação que está a tentar realizar durante a implementação. Encontre a política que bloqueia a ação. Se possível, altere a sua implantação para satisfazer as limitações da apólice. | [Resolver políticas](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Forneça um nome de recurso que não inclua um nome reservado. | [Nomes de recursos reservados](error-reserved-resource-name.md) |
| Grupo de RecursosSendo Eliminado | Aguarde a eliminação. | |
| RecursosGroupNotFound | Verifique o nome do grupo de recursos-alvo para a implantação. O grupo de recursos alvo já deve existir na sua subscrição. Verifique o seu contexto de subscrição. | [PowerShell Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| Recursos Não Encontrados | A sua implantação refere um recurso que não pode ser resolvido. Verifique se a sua utilização da função **de referência** inclui os parâmetros necessários para o seu cenário. | [Resolver referências](error-not-found.md) |
| ResourceQuotaExceeded | A implantação está a tentar criar recursos que excedam a quota para a subscrição, grupo de recursos ou região. Se possível, reveja a sua infraestrutura para se manter dentro das quotas. Caso contrário, considere pedir uma alteração às suas quotas. | [Resolver quotas](error-resource-quota.md) |
| SkuNotAvailable | Selecione SKU (como o tamanho VM) disponível para a localização que selecionou. | [Resolver sku](error-sku-not-available.md) |
| Armazenamento Conta Já Existe | Indique um nome único para a conta de armazenamento. | [Resolver o nome da conta de armazenamento](error-storage-account-name.md)  |
| Armazenamento Contajátomada | Indique um nome único para a conta de armazenamento. | [Resolver o nome da conta de armazenamento](error-storage-account-name.md) |
| ArmazenamentoAccountNotFound | Verifique a subscrição, o grupo de recursos e o nome da conta de armazenamento que está a tentar utilizar. | |
| SubnetsNotInSameVnet | Uma máquina virtual só pode ter uma rede virtual. Ao implantar vários NICs, certifique-se de que pertencem à mesma rede virtual. | [Múltiplos NICs](../../virtual-machines/windows/multiple-nics.md) |
| AssinaturaSNão Registadas | Ao implementar recursos de rede, o fornecedor de recursos Microsoft.Network é automaticamente registado na subscrição. Às vezes, o registo automático não termina a tempo. Para evitar este erro intermitente, registe o fornecedor de recursos Microsoft.Network antes da implementação. | [Resolver o registo](error-register-resource-provider.md) |
| Dependência Circular do ModeloResource | Remover dependências desnecessárias. | [Resolver dependências circulares](error-invalid-template.md#circular-dependency) |
| Grupos de Recursos De TooManyTarget | Reduzir o número de grupos de recursos para uma única implantação. | [Implementação entre grupos de recursos](cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Encontre código de erro

Existem dois tipos de erros que pode receber:

* erros de validação
* erros de implementação

Os erros de validação resultam de cenários que podem ser determinados antes da implementação. Estes incluem erros de sintaxe no modelo ou tentativas de implementação de recursos que excederiam as quotas de subscrição. Os erros de implementação resultam de condições que ocorrem durante o processo de implementação. Estes incluem a tentativa de acesso a um recurso que está a ser implementado em paralelo.

Ambos os tipos de erros devolvem um código de erro que deverá utilizar para resolver os problemas da implementação. Ambos os tipos de erros são apresentados no [registo de atividades](../management/view-activity-logs.md). No entanto, os erros de validação não aparecem no histórico de implementação porque a implementação não chegou a ser iniciada.

### <a name="validation-errors"></a>Erros de validação

Ao implementar através do portal, será apresentado um erro de validação depois de submeter os valores.

![mostrar erro de validação do portal](./media/common-deployment-errors/validation-error.png)

Selecione a mensagem para obter mais detalhes. Na imagem seguinte, vê-se um erro de implementação de modelos **inválidos** e uma mensagem que indica uma implementação bloqueada pela política.

![mostrar detalhes de validação](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Erros de implementação

Quando a operação passa na validação, mas falha durante a implementação, recebe um erro de implementação.

Para ver os códigos de erro e as mensagens de implementação com o PowerShell, utilize:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Para ver os códigos de erro e as mensagens de implementação com a CLI do Azure, utilize:

```azurecli-interactive
az deployment group operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

No portal, selecione a notificação.

![erro de notificação](./media/common-deployment-errors/notification.png)

Veja mais detalhes sobre o destacamento. Selecione a opção para obter mais informações acerca do erro.

![implantação falhou](./media/common-deployment-errors/deployment-failed.png)

Vê a mensagem de erro e os códigos de erro. Observe que existem dois códigos de erro. O primeiro código de erro (**DeploymentFailed**) é um erro geral que não fornece os detalhes necessários para resolver o erro. O segundo código de erro (**StorageAccountNotFound**) fornece os detalhes necessários.

![detalhes do erro](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Ativar a exploração de depuração

Às vezes é preciso mais informação sobre o pedido e resposta para saber o que correu mal. Durante a implementação, pode solicitar que informações adicionais são registadas durante uma implantação.

### <a name="powershell"></a>PowerShell

No PowerShell, defina o parâmetro **DeploymentDebugLogLevel** para Todos, Conteúdos de Resposta ou Conteúdo de Pedido.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Examinar o conteúdo do pedido com o seguinte cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Ou, o conteúdo da resposta com:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Esta informação pode ajudá-lo a determinar se um valor no modelo está a ser incorretamente definido.

### <a name="azure-cli"></a>CLI do Azure

Atualmente, o Azure CLI não suporta ligar a exploração de depuração, mas pode recuperar o abate de depuração.

Examinar as operações de implantação com o seguinte comando:

```azurecli
az deployment group operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Examine o conteúdo do pedido com o seguinte comando:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Examine o conteúdo da resposta com o seguinte comando:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Modelo aninhado

Para registar informações sobre depuração para um modelo aninhado, utilize o elemento **dedepuração.**

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2016-09-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>Criar um modelo de resolução de problemas

Em alguns casos, a maneira mais fácil de resolver o seu modelo é testar partes do mesmo. Pode criar um modelo simplificado que lhe permite focar-se na parte que acredita estar a causar o erro. Por exemplo, suponha que está a receber um erro ao fazer referência a um recurso. Em vez de lidar com um modelo inteiro, crie um modelo que desfaça a parte que pode estar a causar o seu problema. Pode ajudá-lo a determinar se está a passar nos parâmetros certos, usando corretamente as funções do modelo e obtendo o recurso que espera.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

Ou suponha que está a ter erros de implantação que acredita estarem relacionados com dependências incorretamente definidas. Teste o seu modelo dividindo-o em modelos simplificados. Primeiro, crie um modelo que implemente apenas um único recurso (como um Servidor SQL). Quando tiver a certeza de que tem esse recurso corretamente definido, adicione um recurso que dependa dele (como uma Base de Dados SQL). Quando tiver esses dois recursos corretamente definidos, adicione outros recursos dependentes (como políticas de auditoria). Entre cada implementação de teste, elimine o grupo de recursos para se certificar de que testa adequadamente as dependências.

## <a name="next-steps"></a>Passos seguintes

* Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações](template-tutorial-troubleshoot.md) do modelo de gestor de recursos de resolução de problemas
* Para conhecer as ações de auditoria, consulte operações de [auditoria com o Gestor de Recursos.](../management/view-activity-logs.md)
* Para aprender sobre as ações para determinar os erros durante a implementação, consulte as operações de [implantação do View](deployment-history.md).
