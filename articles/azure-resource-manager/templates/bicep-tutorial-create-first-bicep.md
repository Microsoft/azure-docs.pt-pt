---
title: Tutorial - Criar & implementar ficheiros Bicep do Gestor de Recursos Azure
description: Crie o seu primeiro ficheiro Bicep para a implementação de recursos Azure. No tutorial, você aprende sobre a sintaxe do ficheiro Bicep e como implementar uma conta de armazenamento.
author: mumian
ms.date: 03/02/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: a0e4d4e56974e2f5bb44a3a300ce185c18582463
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748162"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>Tutorial: Criar e implementar primeiro ficheiro Azure Resource Manager Bicep

Este tutorial apresenta-o a [Bicep.](./bicep-overview.md) Mostra como criar um ficheiro Bicep de arranque e implantá-lo para a Azure. Você vai aprender sobre a estrutura do ficheiro Bicep e as ferramentas que você precisa para trabalhar com ficheiros Bicep. Leva cerca de **12 minutos** para completar este tutorial, mas o tempo real variará com base em quantas ferramentas precisa instalar.

Este tutorial é o primeiro de uma série. À medida que progride através da série, modifica o ficheiro Bicep inicial passo a passo até que tenha explorado todas as partes centrais de um ficheiro Bicep. Estes elementos são os blocos de construção para ficheiros Bicep muito mais complexos. Esperamos que até ao final da série esteja confiante em criar os seus próprios ficheiros Bicep e pronto para automatizar as suas implementações com ficheiros Bicep.

Se quiser saber sobre os benefícios da utilização do Bicep e por que deve automatizar a implementação com ficheiros Bicep, consulte [Bicep](./bicep-overview.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>Obter ferramentas

Comecemos por ter a certeza de que tem as ferramentas necessárias para criar e implantar ficheiros Bicep. Instale estas ferramentas na sua máquina local.

### <a name="editor"></a>Editor

Para criar ficheiros Bicep, precisa de um bom editor. Recomendamos o Código do Estúdio Visual com a extensão Bicep. Se necessitar de instalar estas ferramentas, consulte [Quickstart: Crie ficheiros Bicep com Código de Estúdio Visual](quickstart-create-bicep-use-visual-studio-code.md).

### <a name="command-line-deployment"></a>Implantação da linha de comando

Também precisa do mais recente Azure PowerShell ou do mais recente Azure CLI para implementar o ficheiro Bicep. Para as instruções de instalação, consulte:

- [Instalar o Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows)
- [Instale o Azure CLI no Linux](/cli/azure/install-azure-cli-linux)
- [Instalar a CLI do Azure no macOS](/cli/azure/install-azure-cli-macos)

Depois de instalar o Azure PowerShell ou o Azure CLI, certifique-se de que faz sê-lo pela primeira vez. Para obter ajuda, consulte [Iniciar s-se-in - PowerShell](/powershell/azure/install-az-ps#sign-in) ou [Iniciar sação - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

Está pronto para começar a aprender sobre o Bicep.

## <a name="create-your-first-bicep-file"></a>Crie o seu primeiro ficheiro Bicep

1. Código de estúdio visual aberto com a extensão Bicep instalada.
1. A partir do menu **Ficheiro,** selecione **Novo Ficheiro** para criar um novo ficheiro.
1. A partir do menu **'Ficheiro',** **selecione Guardar como**.
1. Nomeie o _ficheiro_ e selecione a extensão do ficheiro _bicep._ O nome completo do ficheiro é _azuredeploy.bicep_.
1. Guarde o ficheiro para a sua estação de trabalho. Selecione um caminho que seja fácil de lembrar porque irá fornecer esse caminho mais tarde ao implementar o ficheiro Bicep.
1. Copiar e colar os seguintes conteúdos no ficheiro:

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    Aqui está o seu ambiente visual Studio Code:

    ![Código visual do estúdio primeiro arquivo Bicep](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    A declaração de recursos tem quatro componentes:

    - **recurso**: palavra-chave.
    - **nome simbólico** (stg): Um nome simbólico é um identificador para fazer referência ao recurso em todo o seu ficheiro bicep. Não é o nome do recurso quando for implantado. O nome do recurso é definido pela propriedade **do nome.**  Veja o quarto componente desta lista.
    - **tipo de recurso** Microsoft.Storage/storageAccounts@2019-06-01 (): É composto pelo fornecedor de recursos (Microsoft.Storage), tipo de recurso (armazenamentoCocounts) e apiVersion (2019-06-01). Cada fornecedor de recursos publica as suas próprias versões API, pelo que este valor é específico do tipo. Pode encontrar mais tipos e apiversões para vários recursos Azure a partir da [referência ao modelo ARM](/azure/templates/).
    - **propriedades** (tudo dentro = {...}): Estas são as propriedades específicas que gostaria de especificar para o tipo de recurso dado. Estas são exatamente as mesmas propriedades disponíveis para você em um modelo ARM. Cada recurso tem uma `name` propriedade. A maioria dos recursos também tem um `location` imóvel, que define a região onde o recurso é implantado. As outras propriedades variam de acordo com o tipo de recurso e a versão API. É importante entender a ligação entre a versão API e as propriedades disponíveis, por isso vamos entrar em mais detalhes.

        Para esta conta de armazenamento, pode ver a versão API no [storageAccounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts). Note que não adicionou todas as propriedades ao seu ficheiro Bicep. Muitas das propriedades são opcionais. O `Microsoft.Storage` fornecedor de recursos pode lançar uma nova versão API, mas a versão que está a implementar não tem de mudar. Pode continuar a usar essa versão e saber que os resultados da sua implementação serão consistentes.

        Se você vê uma versão API mais antiga, como [armazenamentoAcounts 2016-05-01,](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)verá que um conjunto menor de propriedades está disponível.

        Se decidir alterar a versão API para um recurso, certifique-se de que avalia as propriedades dessa versão e ajusta o seu ficheiro Bicep adequadamente.

1. Substitua `{provide-unique-name}` incluindo os aparelhos encaracolados `{}` por um nome único de conta de armazenamento.

    > [!IMPORTANT]
    > O nome da conta do Storage tem de ser exclusivo em todo o Azure. O nome deve ter apenas letras ou números minúsculos. Não pode ter mais de 24 caracteres. Você pode tentar um padrão de nomeação como usar **a loja1** como um prefixo e, em seguida, adicionar as suas iniciais e a data de hoje. Por exemplo, o nome que usa pode parecer **store1abc09092019**.

    Adivinhar um nome único para uma conta de armazenamento não é fácil e não funciona bem para automatizar grandes implementações. Mais tarde nesta série tutorial, você usará funcionalidades Bicep que facilitam a criação de um nome único.

1. Guarde o ficheiro.

Parabéns, criaste o teu primeiro ficheiro Bicep.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para começar a trabalhar com a Azure PowerShell/Azure CLI, inscreva-se com as suas credenciais Azure.

Selecione os separadores nas seguintes secções de código para escolher entre Azure PowerShell e Azure CLI. Os exemplos do CLI neste artigo são escritos para a concha bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az login
```

---

Se tiver várias subscrições do Azure, selecione a subscrição que pretende utilizar. Substitua `[SubscriptionID/SubscriptionName]` e os suportes quadrados `[]` por informações de subscrição:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Criar grupo de recursos

Quando implementa um ficheiro Bicep, especifica um grupo de recursos que conterá os recursos. Antes de executar o comando de implementação, crie o grupo de recursos com a CLI do Azure ou o Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-bicep-file"></a>Implementar ficheiro Bicep

Bicep é uma abstração transparente sobre os modelos Azure Resource Manager (modelos ARM). Cada ficheiro Bicep compila-se com um modelo ARM padrão antes de ser implantado. Pode compilar o seu ficheiro Bicep num modelo ARM antes de o implementar ou de implementar diretamente o seu ficheiro Bicep. Para implementar o ficheiro Bicep, utilize o Azure CLI ou o Azure PowerShell. Utilize o grupo de recursos que criou. Dê um nome à implementação para que possa identificá-lo facilmente no histórico de implantação. Por conveniência, também crie uma variável que armazena o caminho para o ficheiro Bicep. Esta variável torna mais fácil executar os comandos de implementação, porque não tem de reescrever o caminho sempre que implementa. Substitua `{provide-the-path-to-the-bicep-file}` incluindo os aparelhos encaracolados `{}` pelo caminho para o seu ficheiro Bicep pelo nome de extensão _.bicep._

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar este cmdlet de implementação, você deve ter a [versão mais recente](/powershell/azure/install-az-ps) de Azure PowerShell.

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

O comando de implantação retorna os resultados. Procure `ProvisioningState` para ver se o destacamento foi bem sucedido.

> [!NOTE]
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implementação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir do menu esquerdo, selecione **Grupos de Recursos.**

1. Selecione o grupo de recursos implementar no último procedimento. O nome predefinido é **myResourceGroup**. Não verá nenhum recurso implantado dentro do grupo de recursos.

1. Note-se no canto superior direito da visão geral, o estado da implantação é visualizado. Selecione **1 Conseguiu**.

   ![Ver estado de implantação](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. Vê-se um histórico de implantação para o grupo de recursos. Selecione **firstbicep**.

   ![Selecione implantação](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. Vê-se um resumo da implantação. Há uma conta de armazenamento implantada. Note à esquerda, pode ver entradas, saídas e o modelo utilizado durante a implementação.

   ![Ver resumo de implementação](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se está a avançar para o próximo tutorial, não precisa de eliminar o grupo de recursos.

Se está a parar agora, é melhor apagar o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Criou um ficheiro Bicep simples para implantar no Azure.  Nos tutoriais posteriores, aprende-se a adicionar parâmetros, variáveis, saídas e módulos a um ficheiro Bicep. Estas funcionalidades são os blocos de construção para ficheiros Bicep muito mais complexos.

> [!div class="nextstepaction"]
> [Adicionar parâmetros](bicep-tutorial-add-parameters.md)
