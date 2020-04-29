---
title: Modelos de reutilização através das nuvens
description: Desenvolva modelos do Gestor de Recursos Azure que funcionam consistentemente para diferentes ambientes de nuvem. Crie novos ou atualizados modelos existentes para o Azure Stack.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: c5095efef5d4bef44993bdd9cd52dbdef17378a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80156111"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>Desenvolver modelos ARM para consistência da nuvem

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Um benefício fundamental do Azure é a consistência. Os investimentos de desenvolvimento para um local são reutilizáveis noutro. Um modelo de Gestor de Recursos Azure (ARM) torna as suas implementações consistentes e repetíveis em ambientes, incluindo as nuvens soberanas azure, Azure e Azure Stack. No entanto, para reutilizar modelos através das nuvens, é necessário considerar dependências específicas da nuvem, como explica este guia.

A Microsoft oferece serviços de nuvem inteligentes e prontos para a empresa em muitos locais, incluindo:

* A plataforma global Azure suportada por uma rede crescente de datacenters geridos pela Microsoft em regiões de todo o mundo.
* Nuvens soberanas isoladas como Azure Germany, Azure Government e Azure China 21Vianet. As nuvens soberanas proporcionam uma plataforma consistente com a maioria das mesmas grandes funcionalidades a que os clientes globais do Azure têm acesso.
* Azure Stack, uma plataforma híbrida de nuvem que permite fornecer serviços Azure do datacenter da sua organização. As empresas podem criar o Azure Stack nos seus próprios centros de dados, ou consumir os Serviços Azure dos prestadores de serviços, gerindo o Azure Stack nas suas instalações (por vezes conhecidas como regiões alojadas).

No centro de todas estas nuvens, o Azure Resource Manager fornece uma API que permite uma grande variedade de interfaces de utilizador comunicar com a plataforma Azure. Esta API dá-lhe poderosas capacidades de infraestrutura como código. Qualquer tipo de recurso disponível na plataforma cloud Azure pode ser implantado e configurado com o Gestor de Recursos Azure. Com um único modelo, pode implementar e configurar a sua aplicação completa para um estado final operacional.

![Ambientes azure](./media/templates-cloud-consistency/environments.png)

A consistência do Azure global, as nuvens soberanas, as nuvens hospedadas e uma nuvem no seu datacenter ajudam-no a beneficiar do Gestor de Recursos Azure. Você pode reutilizar os seus investimentos de desenvolvimento através destas nuvens quando configurar a implementação e configuração de recursos baseados em modelos.

No entanto, embora as nuvens globais, soberanas, hospedadas e híbridas forneçam serviços consistentes, nem todas as nuvens são idênticas. Como resultado, você pode criar um modelo com dependências em funcionalidades disponíveis apenas em uma nuvem específica.

O resto deste guia descreve as áreas a considerar quando planeia desenvolver novos ou atualizados modelos ARM existentes para O Stack Azure. Em geral, a sua lista de verificação deve incluir os seguintes itens:

* Verifique se as funções, pontos finais, serviços e outros recursos do seu modelo estão disponíveis nos locais de implementação do alvo.
* Guarde modelos aninhados e artefactos de configuração em locais acessíveis, garantindo o acesso através das nuvens.
* Utilize referências dinâmicas em vez de ligações e elementos de codificação dura.
* Certifique-se de que os parâmetros do modelo que utiliza funcionam nas nuvens-alvo.
* Verifique se existem propriedades específicas dos recursos, as nuvens-alvo.

Para uma introdução aos modelos ARM, consulte a [implementação do modelo](overview.md).

## <a name="ensure-template-functions-work"></a>Garantir o trabalho das funções do modelo

A sintaxe básica de um modelo ARM é JSON. Os modelos usam um superconjunto de JSON, estendendo a sintaxe com expressões e funções. O processador de idioma do modelo é frequentemente atualizado para suportar funções adicionais do modelo. Para uma explicação detalhada das funções do modelo disponível, consulte as [funções](template-functions.md)do modelo ARM .

As novas funções de modelo que são introduzidas no Azure Resource Manager não estão imediatamente disponíveis nas nuvens soberanas ou no Azure Stack. Para implementar um modelo com sucesso, todas as funções referenciadas no modelo devem estar disponíveis na nuvem-alvo.

As capacidades do Azure Resource Manager serão sempre introduzidas no Azure global primeiro. Pode utilizar o seguinte script PowerShell para verificar se as funções de modelo recentemente introduzidas também estão disponíveis no Azure Stack:

1. Faça um clone do repositório [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions)GitHub: .

1. Assim que tiver um clone local do repositório, ligue-se ao Gestor de Recursos Azure do destino com a PowerShell.

1. Importar o módulo psm1 e executar as funções de funções test-azureRmfuns cmdlet:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

O script implementa modelos múltiplos e minimizados, cada um contendo apenas funções de modelo únicas. A saída do script relata as funções de modelo suportadas e indisponíveis.

## <a name="working-with-linked-artifacts"></a>Trabalhar com artefactos ligados

Um modelo pode conter referências a artefactos ligados e conter um recurso de implantação que se liga a outro modelo. Os modelos ligados (também referidos como modelo aninhado) são recuperados pelo Gestor de Recursos no tempo de execução. Um modelo também pode conter referências a artefactos para extensões de máquinavirtual (VM). Estes artefactos são recuperados pela extensão VM que funciona dentro do VM para configuração da extensão VM durante a implementação do modelo.

As seguintes secções descrevem considerações para a consistência da nuvem ao desenvolver modelos que incluem artefactos externos ao modelo de implementação principal.

### <a name="use-nested-templates-across-regions"></a>Use modelos aninhados em todas as regiões

Os modelos podem ser decompostos em pequenos modelos reutilizáveis, cada um dos quais tem um propósito específico e pode ser reutilizado em cenários de implantação. Para executar uma implementação, especifice um único modelo conhecido como o modelo principal ou principal. Especifica os recursos para implantar, tais como redes virtuais, VMs e aplicações web. O modelo principal também pode conter uma ligação com outro modelo, o que significa que você pode nidificar modelos. Da mesma forma, um modelo aninhado pode conter ligações a outros modelos. Pode nidificar até cinco níveis de profundidade.

O seguinte código mostra como o parâmetro templateLink se refere a um modelo aninhado:

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

Considere onde e como armazenar quaisquer modelos ligados que utilizar. No tempo de execução, o Gestor de Recursos Azure requer acesso direto a quaisquer modelos ligados. Uma prática comum é usar o GitHub para armazenar os modelos aninhados. Um repositório GitHub pode conter ficheiros que são acessíveis publicamente através de um URL. Embora esta técnica funcione bem para a nuvem pública e as nuvens soberanas, um ambiente Azure Stack pode estar localizado numa rede corporativa ou numa localização remota desligada, sem qualquer acesso à Internet de saída. Nesses casos, o Gestor de Recursos Azure não recuperaria os modelos aninhados.

Uma melhor prática para implementações em nuvem cruzada é armazenar os seus modelos ligados num local acessível para a nuvem-alvo. Idealmente, todos os artefactos de implantação são mantidos e implantados a partir de um oleoduto de integração contínua/desenvolvimento contínuo (CI/CD). Em alternativa, pode armazenar modelos aninhados num recipiente de armazenamento de bolhas, a partir do qual o Azure Resource Manager pode recuperá-los.

Uma vez que o armazenamento de bolhas em cada nuvem utiliza um nome de domínio totalmente qualificado (FQDN), configure o modelo com a localização dos modelos ligados com dois parâmetros. Os parâmetros podem aceitar a entrada do utilizador no momento da implementação. Os modelos são tipicamente da autoria e partilhados por várias pessoas, por isso a melhor prática é usar um nome padrão para estes parâmetros. As convenções de nomeação ajudam a tornar os modelos mais reutilizáveis entre regiões, nuvens e autores.

No seguinte código, `_artifactsLocation` é utilizado para apontar para um único local, contendo todos os artefactos relacionados com a implantação. Note que é fornecido um valor predefinido. No momento da implantação, se não `_artifactsLocation`for especificado qualquer valor de entrada, o valor predefinido é utilizado. O `_artifactsLocationSasToken` é utilizado como `sasToken`entrada para o . O valor padrão deve ser uma corda `_artifactsLocation` vazia para cenários em que o não está seguro - por exemplo, um repositório público do GitHub.

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

Ao longo do modelo, as ligações são geradas combinando a base URI (a partir do `_artifactsLocation` parâmetro) com um caminho relativo ao artefacto e o `_artifactsLocationSasToken`. O seguinte código mostra como especificar a ligação ao modelo aninhado utilizando a função do modelo uri:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
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

Ao utilizar esta abordagem, `_artifactsLocation` é utilizado o valor predefinido para o parâmetro. Se os modelos ligados precisarem de ser recuperados de um local diferente, a entrada do parâmetro pode ser usada no momento da implementação para sobrepor o valor padrão — não é necessária qualquer alteração ao próprio modelo.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Use _artifactsLocation em vez de links de codificação de difícil

Além de ser usado para modelos `_artifactsLocation` aninhados, o URL no parâmetro é usado como base para todos os artefactos relacionados de um modelo de implantação. Algumas extensões VM incluem uma ligação a um script armazenado fora do modelo. Para estas extensões, não deve codificar duramente as ligações. Por exemplo, as extensões Custom Script e PowerShell DSC podem ligar-se a um script externo no GitHub, como mostrado:

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

Codificar as ligações ao script potencialmente impede que o modelo se desloque com sucesso para outro local. Durante a configuração do recurso VM, o agente VM que corre dentro do VM inicia um download de todos os scripts ligados na extensão VM, e depois armazena os scripts no disco local do VM. Esta abordagem funciona como as ligações do modelo aninhado explicadas anteriormente na secção "Use modelos aninhados em todas as regiões".

O Gestor de Recursos recupera modelos aninhados no tempo de execução. Para extensões VM, a recuperação de quaisquer artefactos externos é realizada pelo agente VM. Além do diferente iniciador da recuperação do artefacto, a solução na definição do modelo é a mesma. Utilize o parâmetro _artifactsLocation com um valor predefinido do caminho base onde todos os artefactos `_artifactsLocationSasToken` são armazenados (incluindo as scripts de extensão VM) e o parâmetro para a entrada para o sasToken.

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

Para construir o URI absoluto de um artefacto, o método preferido é usar a função do modelo uri, em vez da função do modelo concat. Ao substituir links codificados para os scripts na extensão VM com a função do modelo uri, esta funcionalidade no modelo é configurada para a consistência da nuvem.

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

Com esta abordagem, todos os artefactos de implementação, incluindo scripts de configuração, podem ser armazenados no mesmo local com o próprio modelo. Para alterar a localização de todos os links, basta especificar um URL de base diferente para os _parâmetros de localização_de artefactos .

## <a name="factor-in-differing-regional-capabilities"></a>Fator em diferentes capacidades regionais

Com o desenvolvimento ágil e o fluxo contínuo de atualizações e novos serviços introduzidos no Azure, [as regiões podem diferir](https://azure.microsoft.com/regions/services/) na disponibilidade de serviços ou atualizações. Após rigorosos testes internos, novos serviços ou atualizações aos serviços existentes são geralmente introduzidos a uma pequena audiência de clientes que participam num programa de validação. Após validação bem-sucedida do cliente, os serviços ou atualizações são disponibilizados dentro de um subconjunto das regiões do Azure, depois introduzidos em mais regiões, lançados para as nuvens soberanas, e potencialmente disponibilizados para os clientes do Azure Stack também.

Sabendo que as regiões e nuvens de Azure podem diferir nos seus serviços disponíveis, pode tomar algumas decisões proativas sobre os seus modelos. Um bom lugar para começar é examinando os fornecedores de recursos disponíveis para uma nuvem. Um fornecedor de recursos diz-lhe o conjunto de recursos e operações que estão disponíveis para um serviço Azure.

Um modelo implanta e confunde recursos. Um tipo de recurso é fornecido por um fornecedor de recursos. Por exemplo, o fornecedor de recursos computacionais (Microsoft.Compute), fornece vários tipos de recursos, tais como máquinas virtuais e conjuntos de disponibilidade. Cada fornecedor de recursos fornece uma API ao Gestor de Recursos Azure definida por um contrato comum, permitindo uma experiência de autor consistente e unificada em todos os fornecedores de recursos. No entanto, um fornecedor de recursos que esteja disponível no Global Azure pode não estar disponível numa nuvem soberana ou numa região de Azure Stack.

![Fornecedores de recursos](./media/templates-cloud-consistency/resource-providers.png)

Para verificar os fornecedores de recursos que estão disponíveis numa determinada nuvem, execute o seguinte script na interface da linha de comando Azure[(CLI):](/cli/azure/install-azure-cli)

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Também pode utilizar o seguinte cmdlet PowerShell para ver os fornecedores de recursos disponíveis:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Verifique a versão de todos os tipos de recursos

Um conjunto de propriedades é comum para todos os tipos de recursos, mas cada recurso também tem as suas próprias propriedades específicas. Novas funcionalidades e propriedades relacionadas são adicionadas aos tipos de recursos existentes às vezes através de uma nova versão API. Um recurso num modelo tem a sua `apiVersion`própria propriedade de versão API - . Esta versão garante que uma configuração de recurso existente num modelo não é afetada por alterações na plataforma.

Novas versões API introduzidas aos tipos de recursos existentes no Azure global podem não estar imediatamente disponíveis em todas as regiões, nuvens soberanas ou Azure Stack. Para ver uma lista dos fornecedores de recursos disponíveis, tipos de recursos e versões API para uma nuvem, pode utilizar o Resource Explorer no portal Azure. Procure o Explorador de Recursos no menu Todos os Serviços. Expanda o nó de Fornecedores no Resource Explorer para devolver todos os fornecedores de recursos disponíveis, os seus tipos de recursos e versões API nessa nuvem.

Para listar a versão API disponível para todos os tipos de recursos numa determinada nuvem em Azure CLI, executar o seguinte script:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Também pode utilizar o seguinte cmdlet PowerShell:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Consulte as localizações de recursos com um parâmetro

Um modelo é sempre implantado num grupo de recursos que reside numa região. Além da implantação em si, cada recurso num modelo também tem uma propriedade de localização que você usa para especificar a região para implantar. Para desenvolver o seu modelo para a consistência da nuvem, você precisa de uma forma dinâmica de se referir a locais de recursos, porque cada Stack Azure pode conter nomes de localização únicos. Normalmente, os recursos são implantados na mesma região que o grupo de recursos, mas para apoiar cenários como a disponibilidade de candidaturas entre regiões, pode ser útil para distribuir recursos por regiões.

Mesmo que possa codificar duramente os nomes da região ao especificar as propriedades dos recursos num modelo, esta abordagem não garante que o modelo possa ser implantado para outros ambientes do Azure Stack, porque o nome da região provavelmente não existe lá.

Para acomodar diferentes regiões, adicione uma localização de parâmetro de entrada ao modelo com um valor predefinido. O valor predefinido será utilizado se não for especificado qualquer valor durante a implantação.

A função do `[resourceGroup()]` modelo devolve um objeto que contém os seguintes pares chave/valor:

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

Ao referir a chave de localização do objeto no predefinidoValue do parâmetro de entrada, `[resourceGroup().location]` o Gestor de Recursos Azure substituirá, no prazo de funcionamento, a função do modelo pelo nome da localização do grupo de recursos para o que o modelo está implantado.

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

Com esta função de modelo, você pode implementar o seu modelo para qualquer nuvem sem sequer saber os nomes da região com antecedência. Além disso, uma localização para um recurso específico no modelo pode diferir da localização do grupo de recursos. Neste caso, pode configurá-lo utilizando parâmetros de entrada adicionais para esse recurso específico, enquanto os outros recursos no mesmo modelo ainda utilizam o parâmetro de entrada inicial de localização.

### <a name="track-versions-using-api-profiles"></a>Versões de rastreio usando perfis API

Pode ser muito desafiante acompanhar todos os fornecedores de recursos disponíveis e versões API relacionadas que estão presentes no Azure Stack. Por exemplo, no momento da escrita, a mais recente versão API para **Microsoft.Compute/disponibilidadeSets** em Azure é `2018-04-01`, `2016-03-30`enquanto a versão API disponível é comum a Azure e Azure Stack é . A versão API comum para **microsoft.Storage/storageAccounts** shared entre todos `2016-01-01`os locais do Azure `2018-02-01`e DoI Stack é , enquanto a mais recente versão API em Azure é .

Por esta razão, o Gestor de Recursos introduziu o conceito de perfis DePI aos modelos. Sem perfis API, cada recurso num modelo `apiVersion` é configurado com um elemento que descreve a versão API para esse recurso específico.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Uma versão de perfil API funciona como um pseudónimo para uma única versão API por tipo de recurso comum ao Azure e ao Azure Stack. Em vez de especificar uma versão API para cada recurso num modelo, especifica apenas a versão do perfil API num novo elemento raiz chamado `apiProfile` e omite o `apiVersion` elemento para os recursos individuais.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

O perfil DaPi garante que as versões API estão disponíveis em todos os locais, pelo que não tem de verificar manualmente as apiVersões que estão disponíveis num local específico. Para garantir que as versões API referenciadas pelo seu perfil API estão presentes num ambiente Azure Stack, os operadores do Azure Stack devem manter a solução atualizada com base na política de suporte. Se um sistema estiver fora de prazo há mais de seis meses, é considerado fora de conformidade e o ambiente deve ser atualizado.

O perfil da API não é um elemento necessário num modelo. Mesmo que adicione o elemento, só será utilizado `apiVersion` para recursos para os quais não é especificado. Este elemento permite alterações graduais, mas não requer alterações nos modelos existentes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="check-endpoint-references"></a>Verificar referências de ponto final

Os recursos podem ter referências a outros serviços na plataforma. Por exemplo, um IP público pode ter um nome público de DNS atribuído a ele. A nuvem pública, as nuvens soberanas e as soluções Azure Stack têm os seus próprios espaços de nome final distintos. Na maioria dos casos, um recurso requer apenas um prefixo como entrada no modelo. Durante o período de funcionação, o Gestor de Recursos Azure adere ao valor final. Alguns valores finais precisam de ser explicitamente especificados no modelo.

> [!NOTE]
> Para desenvolver modelos para a consistência da nuvem, não código duro espaços de nome final.

Os dois exemplos seguintes são espaços comuns de nomes finais que precisam de ser explicitamente especificados na criação de um recurso:

* Contas de armazenamento (blob, fila, mesa e arquivo)
* Cordas de ligação para bases de dados e Cache Azure para Redis

Os espaços de nome de ponto final também podem ser usados na saída de um modelo como informação para o utilizador quando a implementação estiver concluída. Seguem-se exemplos comuns:

* Contas de armazenamento (blob, fila, mesa e arquivo)
* Cordas de ligação (MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Gestor de Tráfego
* domínioNameLabel de um endereço IP público
* Serviços em nuvem

Em geral, evite pontos finais codificados num modelo. A melhor prática é usar a função do modelo de referência para recuperar os pontos finais dinamicamente. Por exemplo, o ponto final mais codificado é o espaço de nome final para contas de armazenamento. Cada conta de armazenamento tem um FQDN único que é construído concatenando o nome da conta de armazenamento com o espaço de nome final. Uma conta de armazenamento blob chamada mystorageaccount1 resulta em diferentes FQDNs dependendo da nuvem:

* **mystorageaccount1.blob.core.windows.net** quando criado na nuvem azure global.
* **mystorageaccount1.blob.core.chinacloudapi.cn** quando criada na nuvem Azure China 21Vianet.

A função de modelo de referência seguinte recupera o espaço de nome final do fornecedor de recursos de armazenamento:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Ao substituir o valor codificado do ponto final `reference` da conta de armazenamento pela função do modelo, pode utilizar o mesmo modelo para implantar em diferentes ambientes com sucesso sem fazer alterações na referência do ponto final.

### <a name="refer-to-existing-resources-by-unique-id"></a>Consulte os recursos existentes por ID único

Também pode consultar um recurso existente do mesmo ou outro grupo de recursos, e dentro da mesma subscrição ou outra subscrição, dentro do mesmo inquilino na mesma nuvem. Para recuperar as propriedades do recurso, deve utilizar o identificador único para o próprio recurso. A `resourceId` função do modelo recupera o ID único de um recurso como o SQL Server, como mostra o seguinte código:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Em seguida, `resourceId` pode utilizar `reference` a função dentro da função do modelo para recuperar as propriedades de uma base de dados. O objeto de `fullyQualifiedDomainName` retorno contém a propriedade que detém o valor final completo. Este valor é recuperado no tempo de execução e fornece o espaço de nome final específico do ambiente em nuvem. Para definir a cadeia de ligação sem codificar duramente o espaço de nome final, pode consultar a propriedade do objeto de retorno diretamente na cadeia de ligação, como mostrado:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Considere propriedades de recursos

Recursos específicos dentro dos ambientes do Azure Stack têm propriedades únicas que deve considerar no seu modelo.

### <a name="ensure-vm-images-are-available"></a>Certifique-se de que as imagens VM estão disponíveis

O Azure fornece uma rica seleção de imagens VM. Estas imagens são criadas e preparadas para serem implantadas pela Microsoft e pelos parceiros. As imagens formam a base para VMs na plataforma. No entanto, um modelo consistente em nuvem deve referir-se apenas aos parâmetros disponíveis — em particular, a editora, a oferta e o SKU das imagens VM disponíveis para as imagens globais do Azure, Azure soberanos ou uma solução Azure Stack.

Para recuperar uma lista das imagens VM disponíveis num local, execute o seguinte comando Azure CLI:

```azurecli-interactive
az vm image list -all
```

Pode recuperar a mesma lista com o Cmdlet [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) do Azure `-Location` PowerShell e especificar a localização que pretende com o parâmetro. Por exemplo:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Este comando leva alguns minutos para devolver todas as imagens disponíveis na região da Europa Ocidental da nuvem azure global.

Se disponibilizasse estas imagens VM para o Azure Stack, todo o armazenamento disponível seria consumido. Para acomodar até a unidade de menor escala, o Azure Stack permite-lhe selecionar as imagens que pretende adicionar a um ambiente.

A seguinte amostra de código mostra uma abordagem consistente para se referir aos parâmetros editor, oferta e SKU nos seus modelos ARM:

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

### <a name="check-local-vm-sizes"></a>Verifique os tamanhos vm locais

Para desenvolver o seu modelo para a consistência da nuvem, você precisa ter certeza de que o tamanho VM que deseja está disponível em todos os ambientes-alvo. Os tamanhos vm são um agrupamento de características e capacidades de desempenho. Alguns tamanhos vm dependem do hardware em que o VM funciona. Por exemplo, se quiser implementar um VM otimizado pela GPU, o hardware que executa o hipervisor precisa de ter as GPUs de hardware.

Quando a Microsoft introduz um novo tamanho de VM que tem certas dependências de hardware, o tamanho VM é geralmente disponibilizado primeiro num pequeno subconjunto de regiões na nuvem Azure. Mais tarde, é disponibilizado a outras regiões e nuvens. Para se certificar de que o tamanho VM existe em cada nuvem para a qual se implanta, pode recuperar os tamanhos disponíveis com o seguinte comando Azure CLI:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Para a Azure PowerShell, utilize:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Para obter uma lista completa de serviços disponíveis, consulte [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable)

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Verifique a utilização de discos geridos azure em Azure Stack

Os discos geridos tratam do armazenamento de um inquilino Azure. Em vez de criar explicitamente uma conta de armazenamento e especificar o URI para um disco rígido virtual (VHD), pode utilizar discos geridos para executar implicitamente estas ações quando implementa um VM. Os discos geridos aumentam a disponibilidade colocando todos os discos de VMs na mesma disponibilidade definida em diferentes unidades de armazenamento. Além disso, os VHDs existentes podem ser convertidos de armazenamento Standard para Premium com significativamente menos tempo de inatividade.

Embora os discos geridos estejam no roteiro para o Azure Stack, atualmente não são suportados. Até que estejam, pode desenvolver modelos consistentes em nuvem para o Azure `vhd` Stack, especificando explicitamente vHDs utilizando o elemento no modelo para o recurso VM, como mostrado:

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

Em contraste, para especificar uma configuração de `vhd` disco gerida num modelo, retire o elemento da configuração do disco.

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

As mesmas alterações também aplicam discos de [dados.](../../virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Verifique se as extensões VM estão disponíveis no Azure Stack

Outra consideração para a consistência da nuvem é o uso de [extensões de máquinas virtuais](../../virtual-machines/windows/extensions-features.md) para configurar os recursos dentro de um VM. Nem todas as extensões VM estão disponíveis no Azure Stack. Um modelo pode especificar os recursos dedicados à extensão VM, criando dependências e condições dentro do modelo.

Por exemplo, se pretender configurar um VM que executa o Microsoft SQL Server, a extensão VM pode configurar o Servidor SQL como parte da implementação do modelo. Considere o que acontece se o modelo de implementação também contiver um servidor de aplicação configurado para criar uma base de dados no VM executando o Servidor SQL. Além de utilizar também uma extensão VM para os servidores de aplicações, pode configurar a dependência do servidor de aplicação na devolução bem-sucedida do recurso de extensão VM do Servidor SQL. Esta abordagem garante que o VM que executa o Servidor SQL está configurado e disponível quando o servidor de aplicação é instruído para criar a base de dados.

A abordagem declarativa do modelo permite definir o estado final dos recursos e das suas interdependências, enquanto a plataforma cuida da lógica necessária para as dependências.

#### <a name="check-that-vm-extensions-are-available"></a>Verifique se as extensões VM estão disponíveis

Existem muitos tipos de extensões VM. Ao desenvolver o modelo para a consistência da nuvem, certifique-se de que utiliza apenas as extensões disponíveis em todas as regiões os alvos do modelo.

Para recuperar uma lista das extensões VM que estão disponíveis `myLocation`para uma região específica (neste exemplo, ), executar o seguinte comando Azure CLI:

```azurecli-interactive
az vm extension image list --location myLocation
```

Também pode executar o Azure PowerShell [Get-AzureRmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) cmdlet e usar `-Location` para especificar a localização da imagem da máquina virtual. Por exemplo:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Certifique-se de que as versões estão disponíveis

Uma vez que as extensões vm são recursos de gestor de recursos de primeira parte, eles têm as suas próprias versões API. Como mostra o seguinte código, o tipo de extensão VM é um recurso aninhado no fornecedor de recursos Microsoft.Compute.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

A versão API do recurso de extensão VM deve estar presente em todos os locais que pretende atingir com o seu modelo. A dependência da localização funciona como a disponibilidade da versão API do fornecedor de recursos discutida anteriormente na secção "Verificar a versão de todos os tipos de recursos".

Para obter uma lista das versões API disponíveis para o recurso de extensão VM, utilize o [cmdlet Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) com o fornecedor de recursos **Microsoft.Compute,** como mostrado:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Também pode utilizar extensões VM em conjuntos de escala de máquinas virtuais. Aplicam-se as mesmas condições de localização. Para desenvolver o seu modelo para a consistência da nuvem, certifique-se de que as versões API estão disponíveis em todos os locais para os qual planeia implementar. Para recuperar as versões API do recurso de extensão VM para conjuntos de escala, utilize o mesmo cmdlet que antes, mas especifique o tipo de recurso da escala virtual da máquina como mostrado:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Cada extensão específica também é versão. Esta versão é mostrada `typeHandlerVersion` na propriedade da extensão VM. Certifique-se de que a `typeHandlerVersion` versão especificada no elemento das extensões VM do seu modelo está disponível nos locais onde planeia implementar o modelo. Por exemplo, o seguinte código especifica a versão 1.7:

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

Para obter uma lista das versões disponíveis para uma extensão VM específica, utilize o cmdlet [Get-AzureRmVMExtensionImage.](/powershell/module/az.compute/get-azvmextensionimage) O exemplo seguinte recupera as versões disponíveis para a extensão VM PowerShell DSC (Configuração de Estado Desejado) do **myLocation:**

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Para obter uma lista de editores, utilize o comando [Get-AzureRmVmImagePublisher.](/powershell/module/az.compute/get-azvmimagepublisher) Para solicitar o tipo, utilize o recomendação [Get-AzureRmVMExtensionImageType.](/powershell/module/az.compute/get-azvmextensionimagetype)

## <a name="tips-for-testing-and-automation"></a>Dicas para testes e automação

É um desafio acompanhar todas as configurações, capacidades e limitações relacionadas enquanto é autor de um modelo. A abordagem comum é desenvolver e testar modelos contra uma única nuvem antes que outros locais sejam alvo. No entanto, quanto mais cedo os testes forem realizados no processo de autoria, menos resolução de problemas e reescrita de código a sua equipa de desenvolvimento terá de fazer. As implementações que falham devido às dependências de localização podem ser morosas para resolver problemas. É por isso que recomendamos testes automatizados o mais cedo possível no ciclo de autoria. Em última análise, precisará de menos tempo de desenvolvimento e menos recursos, e os seus artefactos consistentes com nuvem tornar-se-ão ainda mais valiosos.

A imagem que se segue mostra um exemplo típico de um processo de desenvolvimento para uma equipa que utiliza um ambiente de desenvolvimento integrado (IDE). Em diferentes fases da linha do tempo, diferentes tipos de teste são executados. Aqui, dois desenvolvedores estão a trabalhar na mesma solução, mas este cenário aplica-se igualmente a um único desenvolvedor ou a uma grande equipa. Cada desenvolvedor normalmente cria uma cópia local de um repositório central, permitindo que cada um trabalhe na cópia local sem afetar os outros que podem estar a trabalhar nos mesmos ficheiros.

![Fluxo de trabalho](./media/templates-cloud-consistency/workflow.png)

Considere as seguintes dicas para testes e automatização:

* Faça uso de ferramentas de teste. Por exemplo, visual studio code e estúdio visual incluem IntelliSense e outras funcionalidades que podem ajudá-lo a validar os seus modelos.
* Para melhorar a qualidade do código durante o desenvolvimento do IDE local, efetue análise de código estático com testes de unidade e testes de integração.
* Para uma experiência ainda melhor durante o desenvolvimento inicial, os testes de unidade e os testes de integração só devem alertar quando um problema é encontrado e prosseguir com os testes. Desta forma, pode identificar as questões a resolver e priorizar a ordem das alterações, também referida como implantação orientada para o teste (TDD).
* Esteja ciente de que alguns testes podem ser realizados sem estar ligado ao Azure Resource Manager. Outros, como a implementação do modelo de teste, exigem que o Gestor de Recursos execute certas ações que não podem ser realizadas offline.
* Testar um modelo de implementação contra a API de validação não é igual a uma implementação real. Além disso, mesmo que você implemente um modelo a partir de um ficheiro local, quaisquer referências a modelos aninhados no modelo são recuperados diretamente pelo Gestor de Recursos, e os artefactos referenciados por extensões VM são recuperados pelo agente VM que corre dentro do VM implantado.

## <a name="next-steps"></a>Passos seguintes

* [Considerações de modelo de gestor de recursos azure](/azure-stack/user/azure-stack-develop-templates)
* [Boas práticas para modelos ARM](template-syntax.md)
