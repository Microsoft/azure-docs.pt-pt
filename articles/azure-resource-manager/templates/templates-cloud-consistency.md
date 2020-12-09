---
title: Moldes de reutilização através de nuvens
description: Desenvolver modelos de Gestor de Recursos Azure (modelos ARM) que funcionam de forma consistente para diferentes ambientes em nuvem. Crie modelos novos ou atualizados para Azure Stack.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 806556a8da97ec84fe8141b95198b4a7da95c062
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928363"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>Desenvolver modelos ARM para consistência na nuvem

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Um dos principais benefícios do Azure é a consistência. Os investimentos de desenvolvimento para uma localização são reutilizáveis noutro. Um modelo de Gestor de Recursos Azure (modelo ARM) torna as suas implementações consistentes e repetíveis em todos os ambientes, incluindo as nuvens soberanas globais de Azure, Azure e Azure Stack. No entanto, para reutilizar modelos através das nuvens, é necessário considerar dependências específicas da nuvem, como explica este guia.

A Microsoft oferece serviços inteligentes e prontos para a empresa em muitos locais, incluindo:

* A plataforma global Azure apoiada por uma rede crescente de datacenters geridos pela Microsoft em regiões de todo o mundo.
* Nuvens soberanas isoladas como Azure Alemanha, Governo Azure e Azure China 21Vianet. As nuvens soberanas fornecem uma plataforma consistente com a maioria das mesmas grandes funcionalidades a que os clientes globais do Azure têm acesso.
* Azure Stack, uma plataforma de nuvem híbrida que permite fornecer serviços Azure do datacenter da sua organização. As empresas podem criar o Azure Stack nos seus próprios datacenters, ou consumir serviços Azure de prestadores de serviços, executando a Azure Stack nas suas instalações (por vezes conhecidas como regiões hospedadas).

No centro de todas estas nuvens, o Azure Resource Manager fornece uma API que permite uma grande variedade de interfaces de utilizador para comunicar com a plataforma Azure. Esta API dá-lhe poderosas capacidades de infraestrutura como código. Qualquer tipo de recurso disponível na plataforma cloud Azure pode ser implementado e configurado com o Azure Resource Manager. Com um único modelo, pode implementar e configurar a sua aplicação completa para um estado final operacional.

![Ambientes azuis](./media/templates-cloud-consistency/environments.png)

A consistência do Azure global, as nuvens soberanas, nuvens hospedeiras e uma nuvem no seu datacenter ajudam-no a beneficiar do Azure Resource Manager. Pode reutilizar os seus investimentos de desenvolvimento através destas nuvens quando configurar a implementação e configuração de recursos baseados em modelos.

No entanto, embora as nuvens globais, soberanas, hospedadas e híbridas ofereçam serviços consistentes, nem todas as nuvens são idênticas. Como resultado, pode criar um modelo com dependências de funcionalidades disponíveis apenas numa nuvem específica.

O resto deste guia descreve as áreas a ter em conta quando se planeia desenvolver novos ou atualizar modelos ARM existentes para a Azure Stack. Em geral, a sua lista de verificação deve incluir os seguintes itens:

* Verifique se as funções, pontos finais, serviços e outros recursos do seu modelo estão disponíveis nos locais de implantação do alvo.
* Guarde modelos aninhados e artefactos de configuração em locais acessíveis, garantindo o acesso através das nuvens.
* Utilize referências dinâmicas em vez de ligações e elementos de codificação rígido.
* Certifique-se de que os parâmetros do modelo que utiliza funcionam nas nuvens-alvo.
* Verifique se as propriedades específicas do recurso estão disponíveis nas nuvens-alvo.

Para uma introdução aos modelos ARM, consulte [a implementação do modelo](overview.md).

## <a name="ensure-template-functions-work"></a>Garantir que as funções do modelo funcionam

A sintaxe básica de um modelo ARM é JSON. Os modelos usam um superconjunto de JSON, estendendo a sintaxe com expressões e funções. O processador de linguagem de modelo é frequentemente atualizado para suportar funções adicionais do modelo. Para obter uma explicação detalhada das funções do modelo disponíveis, consulte as [funções do modelo ARM](template-functions.md).

As novas funções do modelo que são introduzidas no Azure Resource Manager não estão imediatamente disponíveis nas nuvens soberanas ou na Pilha Azure. Para implementar um modelo com sucesso, todas as funções referenciadas no modelo devem estar disponíveis na nuvem-alvo.

As capacidades do Azure Resource Manager serão sempre introduzidas no Azure global primeiro. Pode utilizar o seguinte script PowerShell para verificar se as funções do modelo recentemente introduzidas também estão disponíveis no Azure Stack:

1. Faça um clone do repositório gitHub: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions) .

1. Assim que tiver um clone local do repositório, ligue-se ao Gestor de Recursos Azure do destino com o PowerShell.

1. Importe o módulo psm1 e execute o Test-AzureRmTemplateFunctions cmdlet:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

O script implementa vários modelos minimizados, cada um contendo apenas funções únicas do modelo. A saída do script relata as funções de modelo suportadas e indisponíveis.

## <a name="working-with-linked-artifacts"></a>Trabalhar com artefactos ligados

Um modelo pode conter referências a artefactos ligados e conter um recurso de implantação que se liga a outro modelo. Os modelos ligados (também referidos como modelo aninhado) são recuperados pelo Gestor de Recursos em tempo de execução. Um modelo também pode conter referências a artefactos para extensões de máquina virtual (VM). Estes artefactos são recuperados pela extensão VM que corre no interior do VM para configuração da extensão VM durante a implementação do modelo.

As seguintes secções descrevem considerações para a consistência da nuvem ao desenvolver modelos que incluem artefactos externos ao modelo de implantação principal.

### <a name="use-nested-templates-across-regions"></a>Use modelos aninhados em regiões

Os modelos podem ser decompostos em pequenos modelos reutilizáveis, cada um dos quais tem um propósito específico e pode ser reutilizado através de cenários de implantação. Para executar uma implementação, especifique um único modelo conhecido como o modelo principal ou mestre. Especifica os recursos a implementar, tais como redes virtuais, VMs e aplicações web. O modelo principal também pode conter uma ligação com outro modelo, o que significa que você pode nidificar modelos. Da mesma forma, um modelo aninhado pode conter links para outros modelos. Podes nidificar até cinco níveis de profundidade.

O código a seguir mostra como o parâmetro templateLink se refere a um modelo aninhado:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

O Gestor de Recursos Azure avalia o modelo principal no tempo de execução e recupera e avalia cada modelo aninhado. Depois de todos os modelos aninhados serem recuperados, o modelo é achatado e o processamento posterior é iniciado.

### <a name="make-linked-templates-accessible-across-clouds"></a>Tornar os modelos ligados acessíveis através das nuvens

Considere onde e como armazenar quaisquer modelos ligados que utilize. No tempo de execução, o Azure Resource Manager procura — e, portanto, requer acesso direto a - quaisquer modelos ligados. Uma prática comum é usar o GitHub para armazenar os modelos aninhados. Um repositório GitHub pode conter ficheiros acessíveis publicamente através de um URL. Embora esta técnica funcione bem para a nuvem pública e as nuvens soberanas, um ambiente Azure Stack pode estar localizado numa rede corporativa ou numa localização remota desligada, sem qualquer acesso à Internet de saída. Nesses casos, o Azure Resource Manager não conseguiria recuperar os modelos aninhados.

Uma melhor prática para implementações de nuvem cruzada é armazenar os seus modelos ligados num local acessível para a nuvem-alvo. Idealmente, todos os artefactos de implantação são mantidos e implantados a partir de um gasoduto de integração contínua/desenvolvimento contínuo (CI/CD). Em alternativa, pode armazenar modelos aninhados num recipiente de armazenamento de bolhas, a partir do qual o Azure Resource Manager os pode recuperar.

Uma vez que o armazenamento de bolhas em cada nuvem usa um nome de domínio totalmente qualificado (FQDN), configurar o modelo com a localização dos modelos ligados com dois parâmetros. Os parâmetros podem aceitar a entrada do utilizador no momento da implementação. Os modelos são tipicamente da autoria e partilhados por várias pessoas, por isso uma boa prática é usar um nome padrão para estes parâmetros. As convenções de nomeação ajudam a tornar os modelos mais reutilizáveis em regiões, nuvens e autores.

No código seguinte, `_artifactsLocation` é utilizado para apontar para um único local, contendo todos os artefactos relacionados com a implantação. Note que é fornecido um valor predefinido. No momento da implementação, se não for especificado qualquer valor de `_artifactsLocation` entrada, o valor predefinido é utilizado. O `_artifactsLocationSasToken` é usado como entrada para o `sasToken` . O valor padrão deve ser uma cadeia vazia para cenários onde o `_artifactsLocation` não é seguro - por exemplo, um repositório público do GitHub.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Ao longo do modelo, as ligações são geradas combinando o URI base (a partir do `_artifactsLocation` parâmetro) com um caminho relativo a artefactos e o `_artifactsLocationSasToken` . O código que se segue mostra como especificar a ligação ao modelo aninhado utilizando a função do modelo uri:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Utilizando esta abordagem, é utilizado o valor predefinido para o `_artifactsLocation` parâmetro. Se os modelos ligados precisarem de ser recuperados de um local diferente, a entrada do parâmetro pode ser usada no tempo de implementação para anular o valor predefinido - não é necessária qualquer alteração ao próprio modelo.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Use _artifactsLocation em vez de ligações de hardcoding

Além de ser usado para modelos aninhados, o URL no `_artifactsLocation` parâmetro é usado como base para todos os artefactos relacionados de um modelo de implantação. Algumas extensões VM incluem um link para um script armazenado fora do modelo. Para estas extensões, não deve codificar os links. Por exemplo, as extensões personalizadas do Script Script e do PowerShell DSC podem ligar-se a um script externo no GitHub, como mostrado:

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

A codificação das ligações com o script potencialmente impede que o modelo se desloque com sucesso para outro local. Durante a configuração do recurso VM, o agente VM que corre dentro do VM inicia um download de todos os scripts ligados na extensão VM e, em seguida, armazena os scripts no disco local do VM. Esta abordagem funciona como os links do modelo aninhado explicados anteriormente na secção "Use modelos aninhados em regiões".

Gestor de recursos recupera modelos aninhados no tempo de execução. Para as extensões VM, a recuperação de quaisquer artefactos externos é realizada pelo agente VM. Além do diferente iniciador da recuperação do artefacto, a solução na definição do modelo é a mesma. Utilize o parâmetro _artifactsLocation com um valor padrão da trajetória base onde todos os artefactos são armazenados (incluindo os scripts de extensão VM) e o `_artifactsLocationSasToken` parâmetro para a entrada para o sasToken.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Para construir o URI absoluto de um artefacto, o método preferido é usar a função do modelo uri, em vez da função do modelo concat. Ao substituir links codificados com os scripts na extensão VM pela função do modelo uri, esta funcionalidade no modelo está configurada para a consistência da nuvem.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

Com esta abordagem, todos os artefactos de implantação, incluindo scripts de configuração, podem ser armazenados no mesmo local com o próprio modelo. Para alterar a localização de todos os links, basta especificar um URL base diferente para os _parâmetros de designLocalização_.

## <a name="factor-in-differing-regional-capabilities"></a>Fator nas diferentes capacidades regionais

Com o desenvolvimento ágil e o fluxo contínuo de atualizações e novos serviços introduzidos no Azure, [as regiões podem diferir](https://azure.microsoft.com/regions/services/) na disponibilidade de serviços ou atualizações. Após rigorosos testes internos, novos serviços ou atualizações aos serviços existentes são geralmente introduzidos a uma pequena audiência de clientes que participam num programa de validação. Após a validação bem sucedida do cliente, os serviços ou atualizações são disponibilizados dentro de um subconjunto de regiões Azure, depois introduzidos em mais regiões, lançados para as nuvens soberanas, e potencialmente disponibilizados para os clientes Azure Stack também.

Sabendo que as regiões e nuvens do Azure podem diferir nos seus serviços disponíveis, pode tomar algumas decisões proativas sobre os seus modelos. Um bom lugar para começar é examinando os fornecedores de recursos disponíveis para uma nuvem. Um fornecedor de recursos informa-lhe o conjunto de recursos e operações que estão disponíveis para um serviço Azure.

Um modelo implementa e configura recursos. Um tipo de recurso é fornecido por um fornecedor de recursos. Por exemplo, o fornecedor de recursos computacional (Microsoft.Compute), fornece vários tipos de recursos, tais como virtualMachines e disponibilidadeSets. Cada fornecedor de recursos fornece uma API ao Azure Resource Manager definida por um contrato comum, permitindo uma experiência de autoria consistente e unificada em todos os fornecedores de recursos. No entanto, um fornecedor de recursos disponível no Azure global pode não estar disponível numa nuvem soberana ou numa região do Azure Stack.

![Fornecedores de recursos](./media/templates-cloud-consistency/resource-providers.png)

Para verificar os fornecedores de recursos que estão disponíveis numa determinada nuvem, execute o seguinte script na interface da linha de comando Azure[(CLI](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Também pode utilizar o seguinte cmdlet PowerShell para ver os fornecedores de recursos disponíveis:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Verifique a versão de todos os tipos de recursos

Um conjunto de propriedades é comum para todos os tipos de recursos, mas cada recurso também tem as suas próprias propriedades específicas. Novas funcionalidades e propriedades relacionadas são adicionadas aos tipos de recursos existentes, por vezes, através de uma nova versão API. Um recurso em um modelo tem a sua própria propriedade versão API - `apiVersion` . Esta versão garante que uma configuração de recursos existente num modelo não é afetada por alterações na plataforma.

As novas versões API introduzidas nos tipos de recursos existentes no Azure global podem não estar imediatamente disponíveis em todas as regiões, nuvens soberanas ou Azure Stack. Para visualizar uma lista dos fornecedores de recursos disponíveis, tipos de recursos e versões API para uma nuvem, pode utilizar o Resource Explorer no portal Azure. Procure por Resource Explorer no menu Todos os Serviços. Expandir o nó de Fornecedores no Resource Explorer para devolver todos os fornecedores de recursos disponíveis, os seus tipos de recursos e versões API nessa nuvem.

Para listar a versão API disponível para todos os tipos de recursos numa determinada nuvem em Azure CLI, execute o seguinte script:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Também pode utilizar o seguinte cmdlet PowerShell:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Consulte as localizações dos recursos com um parâmetro

Um modelo é sempre implantado num grupo de recursos que reside numa região. Além da própria implantação, cada recurso em um modelo também tem uma propriedade de localização que você usa para especificar a região para implementar dentro Para desenvolver o seu modelo para a consistência da nuvem, você precisa de uma forma dinâmica de se referir a localizações de recursos, porque cada Azure Stack pode conter nomes de localização únicos. Normalmente, os recursos são mobilizados na mesma região que o grupo de recursos, mas para apoiar cenários como a disponibilidade de aplicações entre regiões, pode ser útil para espalhar recursos por todas as regiões.

Mesmo que possa codificar os nomes da região ao especificar as propriedades dos recursos num modelo, esta abordagem não garante que o modelo possa ser implantado em outros ambientes Azure Stack, porque o nome da região provavelmente não existe lá.

Para acomodar diferentes regiões, adicione uma localização de parâmetro de entrada ao modelo com um valor padrão. O valor predefinido será utilizado se não for especificado qualquer valor durante a implementação.

A função do modelo `[resourceGroup()]` devolve um objeto que contém os seguintes pares de teclas/valor:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Ao fazer referência à chave de localização do objeto no modelo padrão do parâmetro de entrada, o Azure Resource Manager substituirá, em tempo de execução, a `[resourceGroup().location]` função do modelo pelo nome da localização do grupo de recursos para o qual o modelo é implantado.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Com esta função de modelo, pode implementar o seu modelo em qualquer nuvem sem sequer conhecer previamente os nomes da região. Além disso, uma localização para um recurso específico no modelo pode diferir da localização do grupo de recursos. Neste caso, pode configugá-lo utilizando parâmetros de entrada adicionais para esse recurso específico, enquanto os outros recursos no mesmo modelo ainda utilizam o parâmetro inicial de entrada de localização.

### <a name="track-versions-using-api-profiles"></a>Versões de rastreio usando perfis API

Pode ser muito desafiante acompanhar todos os fornecedores de recursos disponíveis e versões API relacionadas que estão presentes no Azure Stack. Por exemplo, no momento da escrita, a versão API mais recente para **Microsoft.Compute/availabilitySets** em Azure é `2018-04-01` , enquanto a versão API disponível comum a Azure e Azure Stack é `2016-03-30` . A versão API comum para **Microsoft.Storage/storageAcounts partilhados** entre todas as localizações do Azure e do Azure Stack é `2016-01-01` , enquanto a versão API mais recente em Azure é `2018-02-01` .

Por esta razão, o Gestor de Recursos introduziu o conceito de perfis API nos modelos. Sem perfis API, cada recurso num modelo é configurado com um `apiVersion` elemento que descreve a versão API para esse recurso específico.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

Uma versão de perfil API funciona como um pseudónimo para uma única versão API por tipo de recurso comum a Azure e Azure Stack. Em vez de especificar uma versão API para cada recurso num modelo, especifica apenas a versão do perfil API num novo elemento raiz chamado `apiProfile` e omite o `apiVersion` elemento para os recursos individuais.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

O perfil da API garante que as versões API estão disponíveis em locais, pelo que não tem de verificar manualmente as apiversões que estão disponíveis num local específico. Para garantir que as versões API referenciadas pelo seu perfil API estão presentes num ambiente Azure Stack, os operadores do Azure Stack devem manter a solução atualizada com base na política de suporte. Se um sistema estiver mais de seis meses desatualizado, é considerado fora de conformidade e o ambiente deve ser atualizado.

O perfil da API não é um elemento necessário num modelo. Mesmo que adicione o elemento, só será utilizado para recursos para os quais não `apiVersion` é especificado. Este elemento permite alterações graduais, mas não requer alterações nos modelos existentes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Verifique referências de ponto final

Os recursos podem ter referências a outros serviços na plataforma. Por exemplo, um IP público pode ter um nome DNS público atribuído a ele. A nuvem pública, as nuvens soberanas e as soluções Azure Stack têm os seus próprios espaços de nome de ponto final distintos. Na maioria dos casos, um recurso requer apenas um prefixo como entrada no modelo. Durante o tempo de funcionamento, o Azure Resource Manager anexa-lhe o valor do ponto final. Alguns valores do ponto final devem ser explicitamente especificados no modelo.

> [!NOTE]
> Para desenvolver modelos para a consistência da nuvem, não código rígido espaço de nome.

Os dois exemplos seguintes são espaços comuns de nomes de ponto final que precisam de ser explicitamente especificados ao criar um recurso:

* Contas de armazenamento (bolha, fila, mesa e arquivo)
* Cadeias de ligação para bases de dados e Cache Azure para Redis

Os espaços de nome de ponto final também podem ser utilizados na saída de um modelo como informação para o utilizador quando a implementação estiver concluída. Seguem-se exemplos comuns:

* Contas de armazenamento (bolha, fila, mesa e arquivo)
* Cadeias de conexão (MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Gestor de Tráfego
* nome de domínioNabel de um endereço IP público
* Serviços em nuvem

Em geral, evite pontos finais codificados num modelo. A melhor prática é utilizar a função do modelo de referência para recuperar os pontos finais dinamicamente. Por exemplo, o ponto final mais comumente codificado é o espaço de nome de ponto final para contas de armazenamento. Cada conta de armazenamento tem uma FQDN única que é construída através da concatenação do nome da conta de armazenamento com o espaço de nome do ponto final. Uma conta de armazenamento blob chamada mystorageaccount1 resulta em diferentes FQDNs dependendo da nuvem:

* `mystorageaccount1.blob.core.windows.net` quando criado na nuvem global de Azure.
* `mystorageaccount1.blob.core.chinacloudapi.cn` quando criado na nuvem Azure China 21Vianet.

A função de modelo de referência a seguir recupera o espaço de nome do ponto final do fornecedor de recursos de armazenamento:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Ao substituir o valor codificado do ponto final da conta de armazenamento pela `reference` função do modelo, pode utilizar o mesmo modelo para implementar em diferentes ambientes com sucesso sem fazer alterações na referência do ponto final.

### <a name="refer-to-existing-resources-by-unique-id"></a>Consulte os recursos existentes por ID único

Você também pode se referir a um recurso existente do mesmo ou outro grupo de recursos, e dentro da mesma subscrição ou outra subscrição, dentro do mesmo inquilino na mesma nuvem. Para recuperar as propriedades dos recursos, deve utilizar o identificador único para o próprio recurso. A `resourceId` função do modelo recupera o ID único de um recurso como o SQL Server, como mostra o seguinte código:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Em seguida, pode utilizar a `resourceId` função dentro da `reference` função do modelo para recuperar as propriedades de uma base de dados. O objeto de retorno contém a `fullyQualifiedDomainName` propriedade que detém o valor total do ponto final. Este valor é recuperado em tempo de execução e fornece o espaço de nome específico do ponto final do ambiente em nuvem. Para definir a cadeia de ligação sem codificar o espaço de nome do ponto final, pode consultar a propriedade do objeto de retorno diretamente na cadeia de ligação, conforme mostrado:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Considere propriedades de recursos

Recursos específicos dentro dos ambientes Azure Stack têm propriedades únicas que deve considerar no seu modelo.

### <a name="ensure-vm-images-are-available"></a>Certifique-se de que as imagens VM estão disponíveis

A azure fornece uma rica seleção de imagens VM. Estas imagens são criadas e preparadas para serem implantadas pela Microsoft e pelos parceiros. As imagens formam a base para VMs na plataforma. No entanto, um modelo consistente em nuvem deve referir-se apenas aos parâmetros disponíveis - em particular, a editora, a oferta, e sKU das imagens VM disponíveis para o Azure global, nuvens soberanas Azure ou uma solução Azure Stack.

Para obter uma lista das imagens VM disponíveis num local, execute o seguinte comando Azure CLI:

```azurecli-interactive
az vm image list -all
```

Pode recuperar a mesma lista com o cmdlet [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) e especificar a localização desejada com o `-Location` parâmetro. Por exemplo:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Este comando leva alguns minutos para devolver todas as imagens disponíveis na região da Europa Ocidental da nuvem azure global.

Se disponibilizou estas imagens VM para o Azure Stack, todo o armazenamento disponível seria consumido. Para acomodar até a unidade de menor escala, o Azure Stack permite-lhe selecionar as imagens que pretende adicionar a um ambiente.

A amostra de código que se segue mostra uma abordagem consistente para se referir aos parâmetros editor, oferta e SKU nos seus modelos ARM:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Verifique os tamanhos de VM locais

Para desenvolver o seu modelo para a consistência da nuvem, tem de se certificar de que o tamanho VM que pretende está disponível em todos os ambientes-alvo. Os tamanhos de VM são um agrupamento de características e capacidades de desempenho. Alguns tamanhos VM dependem do hardware em que o VM funciona. Por exemplo, se quiser implementar um VM otimizado pela GPU, o hardware que executa o hipervisor precisa de ter as GPUs de hardware.

Quando a Microsoft introduz um novo tamanho de VM que tem certas dependências de hardware, o tamanho VM é normalmente disponibilizado primeiro num pequeno subconjunto de regiões na nuvem Azure. Mais tarde, é disponibilizado a outras regiões e nuvens. Para se certificar de que o tamanho VM existe em cada nuvem para onde se implanta, pode recuperar os tamanhos disponíveis com o seguinte comando Azure CLI:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Para a Azure PowerShell, utilize:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Para obter uma lista completa dos serviços disponíveis, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable)

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Verifique a utilização de discos geridos Azure em Azure Stack

Os discos geridos tratam do armazenamento para um inquilino da Azure. Em vez de criar explicitamente uma conta de armazenamento e especificar o URI para um disco rígido virtual (VHD), pode utilizar discos geridos para realizar implicitamente estas ações quando implementar um VM. Os discos geridos aumentam a disponibilidade colocando todos os discos de VMs na mesma disponibilidade definida em diferentes unidades de armazenamento. Além disso, os VHDs existentes podem ser convertidos de Standard para Premium com significativamente menos tempo de inatividade.

Embora os discos geridos estejam no roteiro para a Azure Stack, atualmente não são suportados. Até que sejam, pode desenvolver modelos consistentes em nuvem para Azure Stack especificando explicitamente VHDs usando o `vhd` elemento no modelo para o recurso VM como mostrado:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Em contraste, para especificar uma configuração de disco gerida num modelo, remova o `vhd` elemento da configuração do disco.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

As mesmas alterações aplicam também [discos de dados.](../../virtual-machines/using-managed-disks-template-deployments.md)

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Verifique se as extensões VM estão disponíveis no Azure Stack

Outra consideração para a consistência da nuvem é o uso de [extensões de máquinas virtuais](../../virtual-machines/extensions/features-windows.md) para configurar os recursos dentro de um VM. Nem todas as extensões VM estão disponíveis em Azure Stack. Um modelo pode especificar os recursos dedicados à extensão VM, criando dependências e condições dentro do modelo.

Por exemplo, se pretender configurar um VM que executa o Microsoft SQL Server, a extensão VM pode configurar o SQL Server como parte da implementação do modelo. Considere o que acontece se o modelo de implementação também contiver um servidor de aplicação configurado para criar uma base de dados no VM que executa o SQL Server. Além de utilizar também uma extensão VM para os servidores de aplicações, pode configurar a dependência do servidor de aplicações na devolução bem sucedida do recurso de extensão VM do SQL Server. Esta abordagem garante que o VM que executa o SQL Server está configurado e disponível quando o servidor de aplicações é instruído para criar a base de dados.

A abordagem declarativa do modelo permite-lhe definir o estado final dos recursos e suas interdependeções, enquanto a plataforma cuida da lógica necessária para as dependências.

#### <a name="check-that-vm-extensions-are-available"></a>Verifique se as extensões VM estão disponíveis

Existem muitos tipos de extensões VM. Ao desenvolver o modelo para a consistência da nuvem, certifique-se de usar apenas as extensões que estão disponíveis em todas as regiões os alvos do modelo.

Para obter uma lista das extensões VM disponíveis para uma região específica (neste exemplo, `myLocation` ), executar o seguinte comando Azure CLI:

```azurecli-interactive
az vm extension image list --location myLocation
```

Também pode executar o cmdeto Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) e utilizar `-Location` para especificar a localização da imagem da máquina virtual. Por exemplo:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Certifique-se de que as versões estão disponíveis

Uma vez que as extensões VM são recursos de gestor de recursos de primeira parte, eles têm as suas próprias versões API. Como o código seguinte mostra, o tipo de extensão VM é um recurso aninhado no fornecedor de recursos Microsoft.Compute.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

A versão API do recurso de extensão VM deve estar presente em todas as localizações que pretende atingir com o seu modelo. A dependência da localização funciona como a disponibilidade de versão API do fornecedor de recursos discutida anteriormente na secção "Verificar a versão de todos os tipos de recursos".

Para obter uma lista das versões API disponíveis para o recurso de extensão VM, utilize o cmdlet [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) com o fornecedor de recursos **Microsoft.Compute** como mostrado:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Também pode utilizar extensões VM em conjuntos de escala de máquina virtual. Aplicam-se as mesmas condições de localização. Para desenvolver o seu modelo de consistência na nuvem, certifique-se de que as versões API estão disponíveis em todas as localizações que planeia implementar. Para recuperar as versões API do recurso de extensão VM para conjuntos de escala, utilize o mesmo cmdlet de antes, mas especifique o tipo de recurso da balança de máquina virtual como mostrado:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Cada extensão específica também é ver versão. Esta versão é mostrada na `typeHandlerVersion` propriedade da extensão VM. Certifique-se de que a versão especificada no `typeHandlerVersion` elemento das extensões VM do seu modelo está disponível nos locais onde planeia implementar o modelo. Por exemplo, o seguinte código especifica a versão 1.7:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

Para obter uma lista das versões disponíveis para uma extensão VM específica, utilize o [cmdlet Get-AzureRmVMExtensionImage.](/powershell/module/az.compute/get-azvmextensionimage) O exemplo a seguir recupera as versões disponíveis para a extensão VM do PowerShell DSC (Configuração do Estado Desejado) a partir do **myLocation**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Para obter uma lista de editores, use o comando [Get-AzureRmVmImagePublisher.](/powershell/module/az.compute/get-azvmimagepublisher) Para solicitar o tipo, utilize o recomendação [Get-AzureRmVMExtensionImageType.](/powershell/module/az.compute/get-azvmextensionimagetype)

## <a name="tips-for-testing-and-automation"></a>Dicas para testes e automatização

É um desafio acompanhar todas as configurações, capacidades e limitações relacionadas enquanto autoria um modelo. A abordagem comum é desenvolver e testar modelos contra uma única nuvem antes que outros locais sejam alvo. No entanto, quanto mais cedo os testes forem realizados no processo de autoria, menos resolução de problemas e reescrita de códigos da sua equipa de desenvolvimento terão de fazer. As implementações que falham devido às dependências da localização podem ser morosas para resolver problemas. É por isso que recomendamos testes automatizados o mais cedo possível no ciclo de autoria. Em última análise, vai precisar de menos tempo de desenvolvimento e menos recursos, e os seus artefactos consistentes em nuvem tornar-se-ão ainda mais valiosos.

A imagem a seguir mostra um exemplo típico de um processo de desenvolvimento para uma equipa que utiliza um ambiente de desenvolvimento integrado (IDE). Em diferentes fases da linha do tempo, são executados diferentes tipos de teste. Aqui, dois desenvolvedores estão a trabalhar na mesma solução, mas este cenário aplica-se igualmente a um único desenvolvedor ou a uma grande equipa. Cada desenvolvedor normalmente cria uma cópia local de um repositório central, permitindo que cada um trabalhe na cópia local sem afetar os outros que podem estar trabalhando nos mesmos ficheiros.

![O diagrama mostra dois conjuntos de testes unitários e testes de integração em paralelo no I D E local, que se fundem no fluxo de desenvolvimento C I/C D em testes unitários, em seguida, testes de integração, em seguida, implantação de testes, em seguida, implantação.](./media/templates-cloud-consistency/workflow.png)

Considere as seguintes dicas para testes e automatização:

* Utilize ferramentas de teste. Por exemplo, Visual Studio Code e Visual Studio incluem IntelliSense e outras funcionalidades que podem ajudá-lo a validar os seus modelos.
* Para melhorar a qualidade do código durante o desenvolvimento no IDE local, realize análise de código estático com testes de unidade e testes de integração.
* Para uma experiência ainda melhor durante o desenvolvimento inicial, os testes de unidade e os testes de integração só devem alertar quando um problema for encontrado e proceder com os testes. Desta forma, pode identificar as questões a tratar e priorizar a ordem das alterações, também referida como implementação orientada para o teste (TDD).
* Esteja ciente de que alguns testes podem ser realizados sem ser ligado ao Azure Resource Manager. Outros, como a implementação do modelo de teste, exigem que o Gestor de Recursos execute determinadas ações que não podem ser realizadas offline.
* Testar um modelo de implementação contra a API de validação não é igual a uma implementação real. Além disso, mesmo que implemente um modelo a partir de um ficheiro local, quaisquer referências a modelos aninhados no modelo são recuperados diretamente pelo Gestor de Recursos, e os artefactos referenciados por extensões VM são recuperados pelo agente VM que corre dentro do VM implantado.

## <a name="next-steps"></a>Passos seguintes

* [Considerações do modelo do Gestor de Recursos Azure](/azure-stack/user/azure-stack-develop-templates)
* [Melhores práticas para modelos ARM](template-syntax.md)
