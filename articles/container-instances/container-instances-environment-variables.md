---
title: Definir variáveis de ambiente no Azure Container Instances
description: Saiba como configurar as variáveis de ambiente nos contentores que executar no Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/21/2019
ms.author: danlep
ms.openlocfilehash: 3e7e292f36296ce09af89f03e8b154b57e18b55c
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370003"
---
# <a name="set-environment-variables"></a>Definir variáveis de ambiente

Definir variáveis de ambiente nas instâncias de contentor permite-lhe fornecer configuração dinâmica da aplicação ou o contentor de execução do script. Para definir as variáveis de ambiente num contentor, especificá-los quando cria uma instância de contentor. Pode definir variáveis de ambiente quando inicia um contentor com o [CLI do Azure](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)e o [portal do Azure](#azure-portal-example).

Por exemplo, se executar o Microsoft [aci-wordcount] [ aci-wordcount] imagem de contentor, pode modificar seu comportamento ao especificar as seguintes variáveis de ambiente:

*NumWords*: O número de palavras enviado para STDOUT.

*MinLength*: O número mínimo de carateres de uma palavra para a mesma a contar. Um número mais alto ignora palavras comuns, como "de" e "a".

Se precisar de passar segredos como variáveis de ambiente, o Azure Container Instances suporta [secure valores](#secure-values) para contentores do Windows e Linux.

## <a name="azure-cli-example"></a>Exemplo da CLI do Azure

Para ver a saída padrão do [aci-wordcount] [ aci-wordcount] contentor, primeiro a executá-lo com isso [criar contentor de az] [ az-container-create] comando (não variáveis de ambiente especificadas):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Para modificar a saída, iniciar um segundo contentor com o `--environment-variables` argumento adicionado, especificar valores para o *NumWords* e *MinLength* variáveis. (Neste exemplo partem do princípio de estiver a executar a CLI num shell de Bash ou o Azure Cloud Shell. Se utilizar a linha de comandos do Windows, especifique as variáveis com aspas duplas, como `--environment-variables "NumWords"="5" "MinLength"="8"`.)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Assim que é apresentado como estado de ambos os contêineres *Terminated* (utilizar [show de contentor az] [ az-container-show] para verificar o estado), exibir seus logs com [registos de contentor az] [ az-container-logs] para ver a saída.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

A saída dos contentores mostram como modificar o comportamento de script do segundo do contentor através da definição de variáveis de ambiente.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Exemplo do PowerShell do Azure

Definir variáveis de ambiente no PowerShell é semelhante da CLI, mas utiliza o `-EnvironmentVariable` argumento da linha de comandos.

Primeiro, inicie o [aci-wordcount] [ aci-wordcount] contentor na respetiva configuração predefinida, com isso [New-AzContainerGroup] [ new-Azcontainergroup] comando:

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Agora, execute o seguinte procedimento [New-AzContainerGroup] [ new-Azcontainergroup] comando. Isso Especifica o *NumWords* e *MinLength* variáveis de ambiente depois de preencher uma variável de matriz, `envVars`:

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Depois de estado de ambos os contêineres *Terminated* (utilizar [Get-AzContainerInstanceLog] [ azure-instance-log] para verificar o estado), extrair os registos com o [ Get-AzContainerInstanceLog] [ azure-instance-log] comando.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

O resultado para cada contentor mostra como modificar o script a executar o contentor através da definição de variáveis de ambiente.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Exemplo do portal do Azure

Para definir as variáveis de ambiente quando inicia um contentor no portal do Azure, especifique-os na **configuração** página ao criar o contentor.

Quando implementar com o portal, está atualmente limitado a três variáveis e tem de introduzi-los no seguinte formato: `"variableName":"value"`

Para ver um exemplo, inicie o [aci-wordcount] [ aci-wordcount] contentor com o *NumWords* e *MinLength* variáveis.

1. Na **Configuration**, defina o **política de reinício** para *em caso de falha*
2. Introduza `"NumWords":"5"` para a primeira variável, selecione **Sim** sob **adicionar variáveis de ambiente adicionais**e introduza `"MinLength":"8"` para a segunda variável. Selecione **OK** para verificar e, em seguida, implementar o contentor.

![Página de portal que mostra o ambiente de variável caixas de texto e botão de ativar][portal-env-vars-01]

Para ver os registos do contentor, em **configurações** selecionar **contentores**, em seguida, **registos**. É semelhante à saída mostra o CLI anterior e o PowerShell seções, que pode ver como o comportamento do script foi modificado pelas variáveis de ambiente. São apresentadas apenas cinco palavras, cada um com um comprimento mínimo de oito caracteres.

![Portal mostrar saída de registo de contentor][portal-env-vars-02]

## <a name="secure-values"></a>Valores seguros

Objetos com valores seguros destinam-se para conter informações confidenciais como palavras-passe ou chaves para a sua aplicação. Usar valores seguros para variáveis de ambiente é mais segura e mais flexível do que incluí-lo na imagem do seu contentor. Outra opção consiste em utilizar os volumes secretos, descritos em [Monte um volume secreto no Azure Container Instances](container-instances-volume-secret.md).

Variáveis de ambiente com valores seguros não estão visíveis nas propriedades do seu contentor – os seus valores podem ser acedidos apenas a partir de dentro do contentor. Por exemplo, as propriedades do contentor visto no portal do Azure ou exibição de CLI do Azure apenas nome de uma variável segura, não o seu valor.

Definir uma variável de ambiente seguro, especificando o `secureValue` propriedade em vez de regular `value` para o tipo da variável. As duas variáveis definidas no YAML seguinte demonstram os dois tipos de variável.

### <a name="yaml-deployment"></a>Implementação de YAML

Criar um `secure-env.yaml` ficheiro pelo seguinte fragmento.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Execute o seguinte comando para implementar o grupo de contentores com YAML (ajustar o nome do grupo de recursos conforme necessário):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Verifique se as variáveis de ambiente

Executar o [show de contentor az] [ az-container-show] comando para consultar as variáveis de ambiente do seu contentor:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

A resposta JSON mostra a variável de ambiente inseguro chave e valor, mas apenas o nome da variável de ambiente seguro:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Com o [exec de contentor az] [ az-container-exec] comando, que permite a execução de um comando num contentor em execução, pode verificar que a variável de ambiente seguro foi definida. Execute o seguinte comando para iniciar uma sessão interativa bash no contentor:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Assim que tiver aberto uma shell interativa dentro do contêiner, pode acessar o `SECRET` valor da variável:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Passos Seguintes

Cenários de baseado em tarefas, como um grande conjunto de dados com vários contentores, de processamento em lote podem beneficiar de variáveis de ambiente personalizadas em tempo de execução. Para obter mais informações sobre a execução de contentores com base em tarefas, consulte [executar tarefas em contentores no Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/azurerm/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
