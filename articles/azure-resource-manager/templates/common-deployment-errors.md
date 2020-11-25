---
title: Resolver erros de implementação comuns
description: Descreve como resolver erros comuns quando implementa recursos para o Azure usando o Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.openlocfilehash: fb7e476a5b4416282546d321a5e9a0127b7a4364
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000713"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Resolver problemas comuns de erros de implementação do Azure com o Azure Resource Manager

Este artigo descreve alguns erros comuns de implementação do Azure, e fornece informações para resolver os erros. Se não encontrar o código de erro para o erro de implementação, veja [Localizar código de erro](#find-error-code).

Se procura informações sobre um código de erro e essa informação não é fornecida neste artigo, avise-nos. Na parte inferior desta página, pode deixar feedback. O feedback é rastreado com problemas do GitHub.

## <a name="error-codes"></a>Códigos de erro

| Código de erro | Mitigação | Mais informações |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Siga as restrições de nomeação para contas de armazenamento. | [Resolver o nome da conta de armazenamento](error-storage-account-name.md) |
| ContaPropertyCannotBeSet | Verifique as propriedades da conta de armazenamento disponível. | [armazenamento Contas](/azure/templates/microsoft.storage/storageaccounts) |
| AtribuiçãoFailada | O cluster ou região não tem recursos disponíveis ou não pode suportar o tamanho de VM solicitado. Reda o pedido mais tarde, ou solicite um tamanho VM diferente. | [Questões de provisionamento e atribuição de problemas de linux,](../../virtual-machines/troubleshooting/troubleshoot-deployment-new-vm-linux.md) [provisionamento e atribuição de](../../virtual-machines/troubleshooting/troubleshoot-deployment-new-vm-windows.md) falhas na atribuição de janelas e [resolução de problemas](../../virtual-machines/troubleshooting/allocation-failure.md)|
| Outra OperaçãoInProgress | Aguarde que a operação simultânea esteja concluída. | |
| Autorizações Destruídas | A sua conta ou diretor de serviço não tem acesso suficiente para completar a implementação. Verifique a função a que a sua conta pertence e o seu acesso ao âmbito de implementação.<br><br>Pode receber este erro quando um fornecedor de recursos necessário não estiver registado. | [Controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Resolver o registo](error-register-resource-provider.md) |
| BadRequest | Enviaste valores de implantação que não correspondem ao esperado pelo Gestor de Recursos. Verifique a mensagem de estado interior para obter ajuda com a resolução de problemas. | [Referência do modelo](/azure/templates/) e [locais suportados](resource-location.md) |
| Conflito | Está a pedir uma operação que não é permitida no estado atual dos recursos. Por exemplo, o redimensionamento do disco só é permitido quando se cria um VM ou quando o VM é transacionado. | |
| ImplementaçãoActiveAndUneditable | Aguarde a implementação simultânea deste grupo de recursos para completar. | |
| ImplementaçãoFailedCleanUp | Quando implementa em modo completo, quaisquer recursos que não estejam no modelo são eliminados. Obtém-se este erro quando não tem permissões adequadas para eliminar todos os recursos que não estão no modelo. Para evitar o erro, altere o modo de implementação para incremental. | [Modos de implementação do Azure Resource Manager](deployment-modes.md) |
| ImplantaçãoNameInvalidCharacters | O nome de implantação só pode conter letra, dígito, '-', '.' ou '_'. | |
| ImplementaçãoNameLengthLimitExceed | Os nomes de implantação estão limitados a 64 caracteres.  | |
| ImplantaçãoFailed | O erro de Implementação É um erro geral que não fornece os detalhes necessários para resolver o erro. Procure nos detalhes de erro um código de erro que forneça mais informações. | [Encontrar código de erro](#find-error-code) |
| DeploymentQuotaExceeded | Se atingir o limite de 800 implementações por grupo de recursos, elimine as implementações do histórico que já não são necessárias. | [Resolver erro quando a contagem de implementação excede 800](deployment-quota-exceeded.md) |
| ImplementaçãoSizeExceed | Simplifique o seu modelo para reduzir o tamanho. | [Resolver erros de tamanho do modelo](error-job-size-exceeded.md) |
| DnsRecordInUse | O nome do registo do DNS deve ser único. Insira um nome diferente. | |
| ImagemNotFound | Verifique as definições de imagem em VM. |  |
| InUseSubnetCannotBedeleted | Pode obter este erro ao tentar atualizar um recurso, e o pedido é processado eliminando e criando o recurso. Certifique-se de especificar todos os valores inalterados. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Obtenha acesso ao inquilino apropriado. Só pode obter o sinal do inquilino a que a sua conta pertence. | |
| InvalidContentLink | Provavelmente tentou ligar-se a um modelo aninhado que não está disponível. Verifique novamente o URI que forneceu para o modelo aninhado. Se o gabarito existir numa conta de armazenamento, certifique-se de que o URI está acessível. Talvez precises de passar um sinal sas. Atualmente, não é possível ligar a um modelo que está numa conta de armazenamento por trás de uma [firewall de armazenamento Azure](../../storage/common/storage-network-security.md). Considere mover o seu modelo para outro repositório, como o GitHub. | [Modelos ligados](linked-templates.md) |
| IndundosDeploylocação | Ao implementar ao nível da subscrição, forneceu um local diferente para um nome de implementação usado anteriormente. | [Implementações de nível de subscrição](deploy-to-subscription.md) |
| InvalidParameter | Um dos valores que forneceu para um recurso não corresponde ao valor esperado. Este erro pode resultar de muitas condições diferentes. Por exemplo, uma palavra-passe pode ser insuficiente, ou um nome blob pode estar incorreto. A mensagem de erro deve indicar qual o valor a ter de ser corrigido. | |
| InálduloRequestContent | Os valores de implantação incluem valores que não são reconhecidos, ou os valores necessários estão em falta. Confirme os valores do seu tipo de recurso. | [Referência de modelo](/azure/templates/) |
| InvalidRequestFormat | Ativar a depuração ao executar a implementação e verificar o conteúdo do pedido. | [Registo de depurar](#enable-debug-logging) |
| InvalidResourceNamespace | Verifique o espaço de nome de recursos especificado na propriedade **tipo.** | [Referência de modelo](/azure/templates/) |
| InvalidResourceReference | O recurso ou ainda não existe ou está incorretamente referenciado. Verifique se precisa de adicionar uma dependência. Verifique se a sua utilização da função de **referência** inclui os parâmetros necessários para o seu cenário. | [Resolver dependências](error-not-found.md) |
| InvalidResourceType | Verifique o tipo de recurso especificado na propriedade **tipo.** | [Referência de modelo](/azure/templates/) |
| InválidaSsubscriçõesRegistrationState | Registe a sua subscrição junto do fornecedor de recursos. | [Resolver o registo](error-register-resource-provider.md) |
| InvalidTemplate | Verifique se a sintaxe do seu modelo é de erros. | [Resolver modelo inválido](error-invalid-template.md) |
| InvalideTePlateCircularDependency | Remova dependências desnecessárias. | [Resolver dependências circulares](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | Simplifique o seu modelo para reduzir o tamanho. | [Resolver erros de tamanho do modelo](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Verifique se a sua conta pertence ao mesmo inquilino que o grupo de recursos para o qual está a implantar. | |
| LinkedInvalidPropertyId | A identificação de recursos para um recurso não está a ser resolvida corretamente. Verifique se fornece todos os valores necessários para o ID de recurso, incluindo iD de subscrição, nome do grupo de recursos, tipo de recurso, nome de recurso principal (se necessário) e nome de recurso. | |
| LocalizaçãoRequired | Fornecer uma localização para o recurso. | [Definir localização](resource-location.md) |
| IncompatibilidadesResourceSegments | Certifique-se de que o recurso aninhado tem o número correto de segmentos no nome e no tipo. | [Resolver segmentos de recursos](error-invalid-template.md#incorrect-segment-lengths)
| Registo Desaparecido ParaLocação | Verifique o estado de registo do fornecedor de recursos e os locais suportados. | [Resolver o registo](error-register-resource-provider.md) |
| SubscriptionRegistration MissingSubscription | Registe a sua subscrição junto do fornecedor de recursos. | [Resolver o registo](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Verifique o estado de registo do fornecedor de recursos. | [Resolver o registo](error-register-resource-provider.md) |
| NotFound | Pode estar a tentar implantar um recurso dependente em paralelo com um recurso dos pais. Verifique se precisa de adicionar uma dependência. | [Resolver dependências](error-not-found.md) |
| OperaçãoNotAllowed | A implantação está a tentar uma operação que exceda a quota para a subscrição, grupo de recursos ou região. Se possível, reveja a sua implementação para se manter dentro das quotas. Caso contrário, considere solicitar uma alteração às suas quotas. | [Resolver quotas](error-resource-quota.md) |
| ParentResourceNotFound | Certifique-se de que existe um recurso principal antes de criar os recursos para crianças. | [Resolver recurso dos pais](error-parent-resource.md) |
| PasswordTooLong | Pode ter selecionado uma palavra-passe com demasiados caracteres ou convertido o valor da palavra-passe para uma cadeia segura antes de a passar como parâmetro. Se o modelo incluir um parâmetro **de corda seguro,** não precisa converter o valor numa cadeia segura. Forneça o valor da palavra-passe como texto. |  |
| PrivateIPAddressInReservedRange | O endereço IP especificado inclui um intervalo de endereços exigido pelo Azure. Altere o endereço IP para evitar o alcance reservado. | [Endereços IP](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInsubnet | O endereço IP especificado está fora do intervalo da sub-rede. Altere o endereço IP para se enquadrar no intervalo da sub-rede. | [Endereços IP](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed | Algumas propriedades não podem ser alteradas num recurso implantado. Ao atualizar um recurso, limite as suas alterações às propriedades permitidas. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | A sua subscrição inclui uma política de recursos que impede uma ação que está a tentar executar durante a implementação. Encontre a política que bloqueia a ação. Se possível, altere a sua implementação para satisfazer as limitações da apólice. | [Resolver políticas](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Forneça um nome de recurso que não inclua um nome reservado. | [Nomes de recursos reservados](error-reserved-resource-name.md) |
| Grupo de RecursosBeingDeleted | Espere que a supressão esteja completa. | |
| Grupo de RecursosNotFound | Verifique o nome do grupo de recursos-alvo para a implantação. O grupo de recursos-alvo já deve existir na sua subscrição. Verifique o contexto da subscrição. | [Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| RecursosNotFound | A sua implantação refere um recurso que não pode ser resolvido. Verifique se a sua utilização da função de **referência** inclui os parâmetros necessários para o seu cenário. | [Resolver referências](error-not-found.md) |
| ResourceQuotaExceeded | A implantação está a tentar criar recursos que excedam a quota para a subscrição, grupo de recursos ou região. Se possível, reveja a sua infraestrutura para se manter dentro das quotas. Caso contrário, considere solicitar uma alteração às suas quotas. | [Resolver quotas](error-resource-quota.md) |
| SkuNotAvailable | Selecione SKU (tal tamanho VM) que está disponível para o local que selecionou. | [Resolver SKU](error-sku-not-available.md) |
| ArmazenamentoCocountAlreadyExists | Indique um nome único para a conta de armazenamento. | [Resolver o nome da conta de armazenamento](error-storage-account-name.md)  |
| ArmazenamentoAccountAlreadyTaken | Indique um nome único para a conta de armazenamento. | [Resolver o nome da conta de armazenamento](error-storage-account-name.md) |
| ArmazenamentoAccountNotFound | Verifique a subscrição, o grupo de recursos e o nome da conta de armazenamento que está a tentar usar. | |
| SubnetsNotInSameVnet | Uma máquina virtual só pode ter uma rede virtual. Ao implementar vários NICs, certifique-se de que pertencem à mesma rede virtual. | [Múltiplos NICs](../../virtual-machines/windows/multiple-nics.md) |
| SubscriçãoNotFound | Uma subscrição especificada para implementação não pode ser acedida. Pode ser que o ID de subscrição esteja errado, o utilizador que implementa o modelo não tem permissões adequadas para implementar na subscrição, ou o ID de subscrição está no formato errado. Ao utilizar implementações aninhadas para [implantar através de âmbitos,](cross-scope-deployment.md)forneça o GUID para a subscrição. | |
| SubscriçãoNotRegista | Ao utilizar um recurso, o fornecedor de recursos deve estar registado para a sua subscrição. Quando utiliza um modelo de Gestor de Recursos Azure para implantação, o fornecedor de recursos é automaticamente registado na subscrição. Às vezes, o registo automático não completa a tempo. Para evitar este erro intermitente, registe o fornecedor de recursos antes da sua utilização. | [Resolver o registo](error-register-resource-provider.md) |
| ModeloResourceCircularDependency | Remova dependências desnecessárias. | [Resolver dependências circulares](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Reduza o número de grupos de recursos para uma única implantação. | [Implantação de âmbito transversal](cross-scope-deployment.md) |

## <a name="find-error-code"></a>Encontrar código de erro

Existem dois tipos de erros que pode receber:

* erros de validação
* erros de implementação

Os erros de validação resultam de cenários que podem ser determinados antes da implementação. Estes incluem erros de sintaxe no modelo ou tentativas de implementação de recursos que excederiam as quotas de subscrição. Os erros de implementação resultam de condições que ocorrem durante o processo de implementação. Estes incluem a tentativa de acesso a um recurso que está a ser implementado em paralelo.

Ambos os tipos de erros devolvem um código de erro que deverá utilizar para resolver os problemas da implementação. Ambos os tipos de erros são apresentados no [registo de atividades](../management/view-activity-logs.md). No entanto, os erros de validação não aparecem no histórico de implementação porque a implementação não chegou a ser iniciada.

### <a name="validation-errors"></a>Erros de validação

Ao implementar através do portal, será apresentado um erro de validação depois de submeter os valores.

![mostrar erro de validação do portal](./media/common-deployment-errors/validation-error.png)

Selecione a mensagem para obter mais detalhes. Na imagem a seguir, vê-se um erro **de desafetação de InvalidTemplate e** uma mensagem que indica uma implementação bloqueada pela política.

![mostrar detalhes de validação](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Erros de implementação

Quando a operação passa na validação, mas falha durante a implementação, recebe um erro de implementação.

Para ver os códigos de erro e as mensagens de implementação com o PowerShell, utilize:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Para ver os códigos de erro e as mensagens de implementação com a CLI do Azure, utilize:

```azurecli-interactive
az deployment operation group list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

No portal, selecione a notificação.

![erro de notificação](./media/common-deployment-errors/notification.png)

Vê mais detalhes sobre a implantação. Selecione a opção para obter mais informações acerca do erro.

![implantação falhou](./media/common-deployment-errors/deployment-failed.png)

Vê a mensagem de erro e os códigos de erro. Observe que existem dois códigos de erro. O primeiro código de erro (**DeploymentFailed**) é um erro geral que não fornece os detalhes necessários para resolver o erro. O segundo código de erro (**StorageAccountNotFound**) fornece os detalhes necessários.

![detalhes de erro](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Ativar a exploração madeireira de depurar

Às vezes é preciso mais informação sobre o pedido e a resposta para saber o que correu mal. Durante a implementação, pode solicitar que informações adicionais são registadas durante uma implementação.

### <a name="powershell"></a>PowerShell

Em PowerShell, desenrida o parâmetro **DeploymentDebugLogLevel** para All, ResponseContent ou RequestContent.

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

Estas informações podem ajudá-lo a determinar se um valor no modelo está a ser incorretamente definido.

### <a name="azure-cli"></a>CLI do Azure

Atualmente, o Azure CLI não suporta ligar a exploração madeireira de depuração, mas pode recuperar a exploração madeireira de depuração.

Examinar as operações de implantação com o seguinte comando:

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Examinar o conteúdo do pedido com o seguinte comando:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Examinar o conteúdo da resposta com o seguinte comando:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Modelo aninhado

Para registar informações de depurar para um modelo aninhado, utilize o elemento **de depurado.**

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

## <a name="create-a-troubleshooting-template"></a>Crie um modelo de resolução de problemas

Em alguns casos, a maneira mais fácil de resolver problemas o seu modelo é testar partes do mesmo. Pode criar um modelo simplificado que lhe permite focar-se na parte que acredita estar a causar o erro. Por exemplo, suponha que está a receber um erro ao fazer referência a um recurso. Em vez de lidar com um modelo inteiro, crie um modelo que retorne a peça que pode estar a causar o seu problema. Pode ajudá-lo a determinar se está a passar os parâmetros certos, usando as funções do modelo corretamente, e obtendo o recurso que espera.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Ou suponha que esteja a ter erros de implantação que acredita estarem relacionados com dependências incorretamente definidas. Teste o seu modelo quebrando-o em modelos simplificados. Em primeiro lugar, crie um modelo que implemente apenas um único recurso (como um SQL Server). Quando tiver a certeza de que tem esse recurso corretamente definido, adicione um recurso que depende dele (como uma Base de Dados SQL). Quando tiver esses dois recursos corretamente definidos, adicione outros recursos dependentes (como políticas de auditoria). Entre cada implementação de teste, elimine o grupo de recursos para se certificar de que testa adequadamente as dependências.

## <a name="next-steps"></a>Passos seguintes

* Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações de modelos do Gestor de Recursos de Resolução de Problemas](template-tutorial-troubleshoot.md)
* Para conhecer as ações de auditoria, consulte [as operações de Auditoria com o Gestor de Recursos.](../management/view-activity-logs.md)
* Para obter ações para determinar os erros durante a implementação, consulte [as operações de implantação](deployment-history.md)da visualização .
