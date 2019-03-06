---
title: Modelos de reutilização entre nuvens - Azure Resource Manager
description: Desenvolva modelos Azure Resource Manager que funcionam de forma consistente para ambientes de cloud diferentes. Criar um novo ou atualizar os modelos existentes para o Azure Stack.
services: azure-resource-manager
documentationcenter: na
author: marcvaneijk
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: aa55dce4b06eb895ef0b8745978d644e4e2f52b5
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408858"
---
# <a name="develop-azure-resource-manager-templates-for-cloud-consistency"></a>Desenvolver modelos Azure Resource Manager para consistência de cloud

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Dos principais benefícios do Azure é a consistência. Os investimentos de desenvolvimento para um único local são reutilizáveis em outro. Um modelo torna as suas implementações repetíveis e consistentes ambientes, incluindo o global Azure, clouds soberanas do Azure e no Azure Stack. Para reutilizar os modelos em várias clouds, no entanto, terá de considerar as dependências específicas da nuvem como este guia explica.

A Microsoft disponibiliza serviços cloud inteligente pronto para empresas em vários locais, incluindo:

* A plataforma do Azure global suportada por uma rede crescente de datacenters geridos pela Microsoft em regiões em todo o mundo.
* Clouds soberanas isolados, como o Azure Alemanha, o Azure Government e Azure China (Azure operado pela 21Vianet). Clouds soberanas fornecem uma plataforma consistente com a maioria das mesmas funcionalidades fantásticas que os clientes do Azure global têm acesso a.
* O Azure Stack, uma plataforma de cloud híbrida que lhe permite fornecer serviços do Azure entre o datacenter da sua organização. As empresas podem configurar o Azure Stack em seus próprios datacenters ou consumir serviços do Azure de fornecedores de serviços, com o Azure Stack em suas instalações (por vezes conhecidas como regiões alojados).

No núcleo do todas essas nuvens, o Azure Resource Manager fornece uma API que permite uma grande variedade de interfaces do usuário para comunicar com a plataforma do Azure. Esta API dá-lhe poderosos recursos de infraestrutura como código. Qualquer tipo de recurso que está disponível na plataforma cloud do Azure pode ser implementado e configurado com o Azure Resource Manager. Com um só modelo, pode implementar e configurar a sua aplicação completa para um Estado de funcionamento operacional final.

![Ambientes do Azure](./media/templates-cloud-consistency/environments.png)

A consistência do global Azure, clouds soberanas, hospedado nuvens e uma cloud no seu centro de dados ajuda-o a se beneficiar do Azure Resource Manager. Quando configurar a implementação de recursos baseados em modelos e a configuração, pode reutilizar seus investimentos de desenvolvimento nessas nuvens.

No entanto, mesmo que o global, soberanas, hospedado e clouds híbridas fornecem serviços consistentes, nem todas as clouds são idênticas. Como resultado, pode criar um modelo com as dependências de funcionalidades disponíveis apenas numa nuvem específica.

O resto deste guia descreve as áreas a considerar quando planear desenvolver novos ou atualizar a modelos Azure Resource Manager existente para o Azure Stack. Em geral, a sua lista de verificação deve incluir os seguintes itens:

* Certifique-se de que as funções, pontos de extremidade, serviços e outros recursos no seu modelo estão disponíveis nas localizações de implementação de destino.
* Store aninhada de modelos e artefactos de configuração em locais acessíveis, garantindo o acesso em várias clouds.
* Utilize referências dinâmicas em vez de ligações e elementos de embutir no código.
* Certifique-se de que os parâmetros de modelo que utilizar trabalham nas clouds do destino.
* Certifique-se de que as propriedades de recursos específicos estão disponíveis as nuvens de destino.

Para obter uma introdução aos modelos do Azure Resource Manager, consulte [implementação do modelo](resource-group-overview.md#template-deployment).

## <a name="ensure-template-functions-work"></a>Certifique-se de que as funções de modelo de trabalho

A sintaxe básica de um modelo do Resource Manager é um JSON. Modelos de utilizam um superconjunto do JSON, estendendo a sintaxe com expressões e funções. O processador de linguagem de modelo é frequentemente atualizado para suportar as funções de modelo adicional. Para obter uma explicação detalhada das funções de modelo disponíveis, consulte [funções de modelo do Azure Resource Manager](resource-group-template-functions.md).

Novas funções de modelo que são introduzidas para o Azure Resource Manager não estão imediatamente disponíveis em clouds independentes ou no Azure Stack. Para implementar um modelo com êxito, todas as funções referenciadas no modelo tem de estar disponíveis na cloud de destino. 

Capacidades do Gestor de recursos do Azure serão sempre introduzidas para o global Azure pela primeira vez. Pode utilizar o seguinte script do PowerShell para verificar se as funções de modelo recém-lançado também estão disponíveis no Azure Stack: 

1. Tornar um clone do repositório do GitHub: [ https://github.com/marcvaneijk/arm-template-functions ](https://github.com/marcvaneijk/arm-template-functions).

1. Assim que tiver um clone local do repositório, ligue para o destino do Azure Resource Manager com o PowerShell.

1. Importe o módulo de psm1 e execute o cmdlet Test-AzureRmureRmTemplateFunctions:

  ```powershell
  # Import the module
  Import-module <path to local clone>\AzTemplateFunctions.psm1

  # Execute the Test-AzureRmTemplateFunctions cmdlet
  Test-AzureRmTemplateFunctions -path <path to local clone>
  ```

O script implementa vários minimizado modelos, cada um contendo as funções de modelo apenas exclusivo. A saída do script reporta as funções de modelo suportada e está indisponível.

## <a name="working-with-linked-artifacts"></a>Trabalhar com artefactos ligados

Um modelo pode conter referências a artefactos ligados e conter um recurso de implementação que liga a outro modelo. Os modelos ligados (também conhecidos como modelo aninhado) são obtidos pelo Gestor de recursos em tempo de execução. Um modelo também pode conter referências a artefactos para extensões de máquina virtual (VM). Estes artefactos são obtidos pela extensão de VM em execução dentro de uma VM para a configuração da extensão VM durante a implementação do modelo. 

As secções seguintes descrevem considerações sobre a consistência da cloud durante o desenvolvimento de modelos que incluem os artefatos que são externos ao modelo de implementação principais.

### <a name="use-nested-templates-across-regions"></a>Utilizar modelos aninhados em várias regiões

Os modelos podem ser decompostos em modelos pequenos e reutilizáveis, cada um deles tem uma finalidade específica e podem ser reutilizados em cenários de implementação. Para executar uma implementação, especifique um modelo único, conhecido como modelo mestre ou principal. Especifica os recursos para implementar, como redes virtuais, VMs e aplicações web. O modelo principal também pode conter uma ligação para outro modelo, que significa que pode aninhar modelos. Da mesma forma, um modelo aninhado pode conter ligações para outros modelos. Pode aninhar até cinco níveis de profundidade.

O código seguinte mostra como o parâmetro templateLink refere-se a um modelo aninhado:

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
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

O Azure Resource Manager avalia o modelo principal no tempo de execução e obtém e avalia cada modelo aninhado. Afinal de contas modelos aninhados são recuperados, o modelo foi simplificado e processamento adicional é iniciada.

### <a name="make-linked-templates-accessible-across-clouds"></a>Disponibilizar modelos ligados em clouds

Considere onde e como armazenar qualquer ligado modelos que utilizar. No tempo de execução, o Azure Resource Manager obtém — e, portanto, requer acesso direto ao — qualquer ligado modelos. Uma prática comum é utilizar o GitHub para armazenar os modelos aninhados. Um repositório do GitHub pode conter ficheiros que estão acessíveis publicamente por meio de um URL. Embora essa técnica funciona bem para a cloud pública e as clouds soberanas, um ambiente do Azure Stack pode ser localizado numa rede empresarial ou numa localização remota desligada, sem qualquer acesso à Internet de saída. Nesses casos, o Azure Resource Manager Falha ao obter os modelos aninhados. 

É uma prática recomendada para implementações entre Clouds armazenar os seus modelos ligados numa localização que esteja acessível para a nuvem de destino. O ideal é que todos os artefatos de implementação são mantidos no e implementados a partir de um pipeline de desenvolvimento (CI/CD) integração contínua/contínua. Em alternativa, pode armazenar modelos aninhados num contentor de armazenamento de BLOBs, do qual o Azure Resource Manager pode recuperá-los. 

Uma vez que o armazenamento de BLOBs em cada nuvem utiliza um nome de domínio completamente qualificado do ponto de extremidade diferente (FQDN), configure o modelo com a localização dos modelos ligados com dois parâmetros. Parâmetros podem aceitar a entrada do usuário no momento da implementação. Modelos normalmente são criados e partilhados por várias pessoas, para que a prática recomendada é usar um nome padrão para esses parâmetros. As convenções de nomenclatura ajudam a tornar os modelos mais reutilizável em regiões, nuvens e autores.

No código a seguir, `_artifactsLocation` é usado para apontar para uma única localização, que contém todos os artefactos relacionados com a implementação. Tenha em atenção que é fornecido um valor predefinido. No momento da implementação, se for especificado nenhum valor de entrada para `_artifactsLocation`, é utilizado o valor predefinido. O `_artifactsLocationSasToken` é utilizado como entrada para o `sasToken`. O valor predefinido deve ser uma cadeia vazia para cenários em que o `_artifactsLocation` não está protegida — por exemplo, um repositório do GitHub público.

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

Em todo o modelo, as ligações são geradas, combinando o URI de base (da `_artifactsLocation` parâmetro) com um caminho relativo do artefacto e a `_artifactsLocationSasToken`. O código seguinte mostra como especificar a ligação para o modelo aninhado usando a função de modelo do uri:

```json
"resources": [
  {
    "name": "shared",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
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

Ao utilizar esta abordagem, o valor predefinido para o `_artifactsLocation` parâmetro é utilizado. Se os modelos ligados tem de ser obtido a partir de uma localização diferente, o parâmetro de entrada pode ser utilizado no momento da implementação para substituir o valor predefinido, não é necessária nenhuma alteração para o próprio modelo.

### <a name="use-artifactslocation-instead-of-hardcoding-links"></a>Utilizar artifactslocation em vez de codificar ligações

Além de que está a ser utilizado para modelos aninhados, o URL no `_artifactsLocation` parâmetro é utilizado como base para todos os artefactos de um modelo de implementação de relacionados. Algumas extensões VM incluem um link para um script armazenado fora do modelo. Para estas extensões, deve codificar as ligações. Por exemplo, as extensões de Script personalizado e o PowerShell DSC podem ligar a um script externo no GitHub, conforme mostrado: 

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

Codificar as ligações para o script potencialmente impede que o modelo de implementação com êxito para outra localização. Durante a configuração do recurso VM, o agente VM em execução dentro da VM inicia um download de todos os scripts ligado na extensão da VM e, em seguida, armazena os scripts no disco local da VM. Funções esta abordagem, como as ligações de modelo aninhado anteriormente explicado na seção "Utilização nested modelos em várias regiões".

O Resource Manager obtém modelos aninhados em tempo de execução. Em extensões de VM, a obtenção de todos os artefactos externos é feita pelo agente de VM. Além do iniciador diferente da obtenção de artefacto, a solução na definição do modelo é o mesmo. Utilize o parâmetro de artifactslocation com um valor padrão do caminho de base onde todos os artefactos são armazenados (incluindo os scripts de extensão VM) e o `_artifactsLocationSasToken` parâmetro para a entrada para o sasToken.

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

Para construir o URI absoluto de um artefato, o método preferencial é usar a função de modelo de uri, em vez da função de modelo de concat. Ao substituir a função de modelo de uri codificado links para os scripts na extensão VM, essa funcionalidade no modelo está configurada para manter a consistência na cloud.

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

Com esta abordagem, todos os artefatos de implementação, incluindo scripts de configuração, podem ser armazenados na mesma localização com o próprio modelo. Para alterar a localização de todas as ligações, só precisa de especificar um URL de base diferente para o _artifactsLocation parâmetros_.

## <a name="factor-in-differing-regional-capabilities"></a>Considerar diferentes capacidades regionais

Com o fluxo contínuo de atualizações e novos serviços que introduziu para o Azure e o desenvolvimento ágil [regiões podem ser diferentes](https://azure.microsoft.com/regions/services/) na disponibilidade de serviços ou atualizações. Após os testes internos rigorosos, os novos serviços ou as atualizações para os serviços existentes, normalmente, são introduzidas para uma pequena audiência de clientes participando de um programa de validação. Após a validação de cliente concluída com êxito, os serviços ou as atualizações são disponibilizadas dentro de um subconjunto de regiões do Azure, em seguida, introduziu a mais regiões, implementadas em Clouds soberanas e potencialmente disponibilizadas para clientes do Azure Stack também.

Sabendo que regiões do Azure e clouds podem diferir em seus serviços disponíveis, pode tomar algumas decisões proativa sobre seus modelos. É um bom lugar para começar, examinando os fornecedores de recursos disponíveis para uma nuvem. Um fornecedor de recursos indica o conjunto de recursos e operações que estão disponíveis para um serviço do Azure.

Um modelo implementa e configura os recursos. Um tipo de recurso é fornecido por um fornecedor de recursos. Por exemplo, o fornecedor de recursos de computação (Microsoft. Compute), fornece vários tipos de recursos, tais como virtualMachines e availabilitySets. Cada fornecedor de recursos fornece uma API no Azure Resource Manager definido por um contrato comuns, permitindo uma experiência de criação unificada, consistente entre todos os fornecedores de recursos. No entanto, um fornecedor de recursos que está disponível no global Azure poderá não estar disponível numa cloud soberana ou uma região do Azure Stack.

![Fornecedores de recursos](./media/templates-cloud-consistency/resource-providers.png) 

Para verificar os fornecedores de recursos que estão disponíveis numa determinada nuvem, execute o seguinte script na interface de linha de comandos do Azure ([CLI](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Também pode utilizar o seguinte cmdlet do PowerShell para ver os fornecedores de recursos disponíveis:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Verificar a versão de todos os tipos de recursos

Um conjunto de propriedades é comum para todos os tipos de recursos, mas cada recurso também tem suas próprias propriedades específicas. Novos recursos e as propriedades relacionadas são adicionadas aos tipos de recurso existente às vezes, por meio de uma nova versão de API. Um recurso num modelo tem sua própria propriedade da versão de API - `apiVersion`. Este controlo de versões garante que uma configuração de recursos existente num modelo não é afetada por alterações na plataforma.

Novas versões de API que introduziu para tipos de recursos existente no global Azure talvez não seja disponíveis em todas as regiões, clouds soberanas ou do Azure Stack. Para ver uma lista dos fornecedores de recursos disponíveis, tipos de recursos e as versões de API para uma nuvem, pode utilizar o Explorador de recursos no portal do Azure. Procure o Explorador de recursos no menu de todos os serviços. Expanda o nó de fornecedores no Explorador de recursos para retornar todos os fornecedores de recursos disponíveis, seus tipos de recursos e as versões de API nessa nuvem.

Para listar a versão de API disponível para todos os tipos de recurso numa determinada nuvem na CLI do Azure, execute o seguinte script:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Também pode utilizar o seguinte cmdlet do PowerShell:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Fazer referência a recursos locais com um parâmetro

Um modelo sempre é implementado num grupo de recursos que reside numa região. Além da implantação propriamente dita, cada recurso num modelo também tem uma propriedade de localização que utilizar para especificar a região para implementar no. Para desenvolver o seu modelo de consistência na cloud, terá de forma dinâmica para fazer referência a recursos locais, porque cada Azure Stack pode conter nomes de localização exclusivo. Normalmente, os recursos são implementados na mesma região que o grupo de recursos, mas para suportar cenários como a disponibilidade de aplicações em várias regiões, pode ser útil distribuir recursos entre regiões.

Mesmo que podia codificar os nomes de região ao especificar as propriedades de recurso num modelo, essa abordagem não garante a que o modelo pode ser implementado a outros ambientes do Azure Stack, uma vez que o nome da região é muito provável que não existe lá.

Para acomodar a regiões diferentes, adicione uma localização do parâmetro de entrada para o modelo com um valor predefinido. O valor predefinido será utilizado se não for especificado nenhum valor durante a implementação. 

A função de modelo `[resourceGroup()]` retorna um objeto que contém os seguintes pares de chave/valor:

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

Ao referenciar a chave de localização do objeto na defaultValue dos parâmetros de entrada, o Azure Resource Manager, no tempo de execução, substituirá a `[resourceGroup().location]` função de modelo com o nome da localização do grupo de recursos é implementar o modelo.

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
    "name": "storageaccount1",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "location": "[parameters('location')]",
    ...
```

Com esta função de modelo, pode implementar o modelo para qualquer cloud sem mesmo saber os nomes de região com antecedência. Além disso, uma localização para um recurso específico no modelo pode ser diferente da localização do grupo de recursos. Neste caso, pode configurá-lo ao utilizar os parâmetros de entrada adicionais para esse recurso específico, enquanto os outros recursos no mesmo modelo em continuar a utilizar o parâmetro de entrada de localização inicial.

### <a name="track-versions-using-api-profiles"></a>Controlar versões através de perfis de API

Pode ser muito difíceis de fazer para controlar todos os fornecedores de recursos disponíveis e as versões de API relacionadas que estão presentes no Azure Stack. Por exemplo, no momento da escrita, a versão mais recente do API para **Compute/availabilitysets** no Azure é `2018-04-01`, enquanto a versão de API disponível comuns para o Azure e o Azure Stack é `2016-03-30`. A versão de API comuns para **Storage/storageaccounts** partilhado entre todas as localizações do Azure e o Azure Stack é `2016-01-01`, enquanto a versão mais recente da API no Azure é `2018-02-01`.

Por esse motivo, o Resource Manager introduziu o conceito de perfis de API para os modelos. Sem perfis de API, cada recurso num modelo está configurado com um `apiVersion` elemento que descreve a versão de API para esse recurso específico.

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
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "apiVersion": "2016-03-30",
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

Uma versão de perfil de API atua como um alias de uma única versão de API por tipo de recurso comuns para o Azure e o Azure Stack. Em vez de especificar uma versão de API para cada recurso num modelo, especificar apenas a versão de perfil de API num novo elemento de raiz chamado `apiProfile` e omitir a `apiVersion` elemento para recursos individuais.

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
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
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

O perfil de API garante que as versões de API estão disponíveis em localizações, pelo que não tem de verificar manualmente a apiVersions que estão disponíveis numa localização específica. Para garantir que as versões de API referenciadas por seu perfil de API estão presentes num ambiente do Azure Stack, os operadores do Azure Stack tem de manter a solução atualizada com base na política para o suporte. Se um sistema é mais de seis meses de desatualizados, ele é considerado em conformidade e o ambiente tem de ser atualizado.

O perfil de API não é um elemento necessário num modelo. Mesmo que adicione o elemento, ele só será utilizado para os recursos para que não `apiVersion` está especificado. Esse elemento permite alterações graduais, mas não requer quaisquer alterações aos modelos existentes.

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
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
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

## <a name="check-endpoint-references"></a>Referências de ponto final de verificação

Recursos podem ter referências a outros serviços na plataforma. Por exemplo, um IP público pode ter um nome DNS público atribuído ao mesmo. A cloud pública, clouds soberanas e soluções do Azure Stack tem seus próprios espaços de nomes de ponto de extremidade distintos. Na maioria dos casos, um recurso requer apenas um prefixo como entrada no modelo. Durante o tempo de execução, o Azure Resource Manager acrescenta o valor de ponto final ao mesmo. Alguns valores de ponto final tem de ser especificado explicitamente no modelo. 

> [!NOTE]
> Desenvolver modelos de consistência na cloud, não espaços de nomes de ponto final de codificar.

Os dois exemplos seguintes são comuns espaços de nomes de ponto final que têm de ser explicitamente especificados durante a criação de um recurso:

* Contas de armazenamento (blob, fila, tabela e ficheiro)
* Cadeias de ligação de bases de dados e a Cache de Redis do Azure

Espaços de nomes de ponto final podem também servir na saída de um modelo de informações para o utilizador quando a implementação estiver concluída. Seguem-se exemplos comuns:

* Contas de armazenamento (blob, fila, tabela e ficheiro)
* Cadeias de ligação (MySql, SQLServer, SQL Azure, personalizado, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Gestor de Tráfego
* domainNameLabel de um endereço IP público
* Serviços em nuvem

Em geral, evite pontos finais de codificado num modelo. A prática recomendada é usar a função de modelo de referência para recuperar os pontos de extremidade dinamicamente. Por exemplo, o ponto final mais comumente embutido em código é o espaço de nomes de ponto final para contas de armazenamento. Cada conta de armazenamento tem um FQDN exclusivo que é criado pela concatenação do nome da conta de armazenamento com o espaço de nomes de ponto final. Uma conta de armazenamento de Blobs com o nome mystorageaccount1 resultados no FQDNs diferentes dependendo da cloud:

* **mystorageaccount1.blob.Core.Windows.NET** quando criada na cloud do Azure global.
* **mystorageaccount1.blob.Core.chinacloudapi.CN** quando criados na cloud do Azure China.

A seguinte função de modelo de referência recupera o espaço de nomes de ponto final do fornecedor de recursos de armazenamento:

```json
"diskUri":"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Ao substituir o valor codificado do ponto final de conta de armazenamento com o `reference` função de modelo, pode utilizar o mesmo modelo para implementar em diferentes ambientes com êxito sem fazer quaisquer alterações à referência de ponto final.

### <a name="refer-to-existing-resources-by-unique-id"></a>Consulte os recursos existentes por ID exclusivo

Também pode consultar um recurso existente do mesmo ou outro recurso e grupo, dentro da mesma subscrição ou outra subscrição, no mesmo inquilino na mesma nuvem. Para obter as propriedades de recurso, tem de utilizar o identificador exclusivo para o recurso em si. O `resourceId` função de modelo obtém o ID exclusivo de um recurso, como o SQL Server, tal como mostra o código seguinte: 

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Em seguida, pode utilizar o `resourceId` funcionar dentro do `reference` função de modelo para recuperar as propriedades de uma base de dados. O objeto de retorno contém o `fullyQualifiedDomainName` propriedade que contém o valor de ponto final completo. Este valor é recuperada no tempo de execução e fornece o espaço de nomes de ponto final específico do ambiente de cloud. Para definir a cadeia de ligação sem codificar o espaço de nomes de ponto final, pode consultar a propriedade do objeto de retorno diretamente na cadeia de ligação, conforme mostrado:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Considere as propriedades de recurso

Recursos específicos em ambientes do Azure Stack tem propriedades exclusivas, que tem de considerar no seu modelo.

### <a name="ensure-vm-images-are-available"></a>Certifique-se de que as imagens VM estão disponíveis

O Azure fornece uma seleção avançada de imagens de VM. Estas imagens são criadas e preparadas para a implementação da Microsoft e parceiros. As imagens formam a base para as VMs na plataforma. No entanto, um modelo consistente com a nuvem deve consultar apenas os parâmetros disponíveis — em particular, o publicador, oferta, SKU e das imagens de VM disponíveis para o Azure global, clouds soberanas do Azure ou uma solução do Azure Stack.

Para obter uma lista das imagens de VM disponíveis numa localização, execute o seguinte comando da CLI do Azure:

```azurecli-interactive
az vm image list -all
```

Pode recuperar a mesma lista com o cmdlet do Azure PowerShell [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) e especifique a localização que quiser com o `-Location` parâmetro. Por exemplo:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Este comando demora alguns minutos para retornar todas as imagens disponíveis na região Europa ocidental da cloud do Azure global.

Se tiver efetuado com que estas imagens de VM disponíveis para o Azure Stack, o armazenamento disponível seria consumido. Para acomodar até mesmo a menor unidade de escala, o Azure Stack permite-lhe selecionar as imagens que pretende adicionar a um ambiente.

O exemplo de código seguinte mostra uma abordagem consistente para fazer referência para o publicador, oferta e parâmetros SKU em modelos do Azure Resource Manager:

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

### <a name="check-local-vm-sizes"></a>Verificar os tamanhos de VM locais

Para desenvolver o seu modelo de consistência na cloud, terá de certificar-se de que pretende que o tamanho da VM está disponível em todos os ambientes de destino. Tamanhos de VM são um agrupamento de capacidades e características de desempenho. Alguns tamanhos VM dependem do hardware que a VM é executado no. Por exemplo, se pretender implementar uma VM com otimização de GPU, o hardware que executa o hipervisor tem de ter o hardware GPUs.

Quando a Microsoft apresenta um novo tamanho de VM que tem determinadas dependências de hardware, o tamanho da VM é, normalmente, disponibilizado pela primeira vez num pequeno subconjunto de regiões na cloud do Azure. Mais tarde, ela fica disponível para outras regiões e clouds. Para certificar-se de que o tamanho da VM existe em cada nuvem que implementar, pode obter os tamanhos disponíveis com o seguinte comando da CLI do Azure:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Para o Azure PowerShell, utilize:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Para obter uma lista completa de serviços disponíveis, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Verifique o uso de Managed Disks do Azure no Azure Stack

Gerido identificador de discos de armazenamento para um inquilino do Azure. Em vez de criar explicitamente uma conta de armazenamento e especificar o URI para um disco rígido virtual (VHD), pode utilizar discos geridos implicitamente realizar essas ações quando implementar uma VM. Discos geridos melhoram a disponibilidade ao colocar todos os discos das VMs no mesmo conjunto em unidades de armazenamento diferente de disponibilidade. Além disso, as VHDs existentes podem ser convertidos do plano padrão para o armazenamento Premium com muito menos tempo de inatividade.

Embora os discos geridos estão no plano para o Azure Stack, eles não são atualmente suportados. Até que seja, pode desenvolver modelos consistentes com a cloud para o Azure Stack ao especificar explicitamente os VHDs com a `vhd` elemento no modelo para o recurso VM, conforme mostrado:

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

Por outro lado, para especificar uma configuração de disco gerido num modelo, remova o `vhd` elemento da configuração do disco.

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

As mesmas alterações também se aplicam [discos de dados](../virtual-machines/windows/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Certifique-se de que as extensões de VM estão disponíveis no Azure Stack

Outra consideração para manter a consistência na cloud é o uso de [extensões de máquina virtual](../virtual-machines/windows/extensions-features.md) para configurar os recursos dentro de uma VM. Nem todas as extensões de VM estão disponíveis no Azure Stack. Um modelo, pode especificar os recursos dedicados para a extensão VM, a criação de dependências e condições dentro do modelo.

Por exemplo, se quiser configurar uma VM com o Microsoft SQL Server, a extensão de VM pode configurar o SQL Server como parte da implementação do modelo. Considere o que acontece se o modelo de implementação também contém um servidor de aplicações configurado para criar uma base de dados na VM com o SQL Server. Além de também usar uma extensão de VM para os servidores de aplicações, pode configurar a dependência de servidor de aplicativos do retorno de êxito do recurso de extensão de VM do SQL Server. Esta abordagem garante que a VM a executar o SQL Server é configurado e está disponível quando o servidor de aplicações é instruído para criar a base de dados.

A abordagem declarativa do modelo permite-lhe definir o estado final de recursos e as respetivas dependências entre, enquanto a plataforma se encarrega da lógica necessária para as dependências.

#### <a name="check-that-vm-extensions-are-available"></a>Verifique se as extensões de VM estão disponíveis

Existem muitos tipos de extensões de VM. Ao desenvolver o modelo de consistência de nuvem, certifique-se de utilizar apenas as extensões que estão disponíveis em todas as regiões os destinos de modelo.

Para obter uma lista das extensões de VM que estão disponíveis para uma região específica (neste exemplo, `myLocation`), execute o seguinte comando da CLI do Azure:

```azurecli-interactive
az vm extension image list --location myLocation
```

Também pode executar o Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) cmdlet e utilizar `-Location` para especificar a localização da imagem de máquina virtual. Por exemplo:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Certifique-se de que estão disponíveis versões

Uma vez que as extensões de VM são recursos do Resource Manager original, eles têm suas próprias versões de API. Como mostra o código a seguir, o tipo de extensão VM é um recurso aninhado no fornecedor de recursos Microsoft. Compute.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

A versão de API do recurso de extensão VM tem de estar presente em todas as localizações que pretende direcionar com seu modelo. A dependência de localização funciona como o fornecedor de recursos de versão de API disponibilidade discutido anteriormente na secção "Verificar a versão de todos os tipos de recursos".

Para obter uma lista das versões de API disponíveis para o recurso de extensão VM, utilize o [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) cmdlet com o **Microsoft. Compute** fornecedor de recursos, conforme mostrado:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Também pode utilizar as extensões de VM em conjuntos de dimensionamento de máquina virtual. Aplicarem as mesmas condições de localização. Para desenvolver o seu modelo de consistência de nuvem, certifique-se de que as versões de API estão disponíveis em todas as localizações que planeia implementar para. Para obter as versões de API do recurso de extensão VM para conjuntos de dimensionamento, utilize o mesmo cmdlet como antes, mas especifique o que tipo de recurso de conjuntos de dimensionamento de máquina virtual conforme mostrado:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Cada extensão específica também tem a mesma versão. Esta versão é mostrada no `typeHandlerVersion` propriedade de extensão de VM. Certifique-se de que a versão especificada no `typeHandlerVersion` elemento das seu modelo extensões de VM estão disponíveis nos locais onde planeia implementar o modelo. Por exemplo, o código a seguir especifica a versão 1.7:

```json
{
    "name": "MyCustomScriptExtension",
    "type": "extensions",
    "apiVersion": "2016-03-30",
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

Para obter uma lista das versões disponíveis para uma extensão VM específica, utilize o [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) cmdlet. O exemplo seguinte obtém as versões disponíveis para a extensão VM de PowerShell DSC (Desired State Configuration do) partir **myLocation**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Para obter uma lista de publicadores, utilize o [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) comando. Para o tipo de pedido, utilize o [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) Command.

## <a name="tips-for-testing-and-automation"></a>Dicas para teste e automatização

É um desafio para controlar todas as respetivas definições, capacidades e limitações ao criar um modelo. A abordagem comum é desenvolver e testar modelos em relação a uma nuvem individual antes de outros locais de destino. No entanto, o anterior que testa é executadas no processo de criação, a menos de resolução de problemas e conversão de código, que será necessário fazer sua equipe de desenvolvimento. Implementações que falham devido a dependências de localização podem ser demoradas resolver problemas. É por isso que recomendamos que teste automatizado mais cedo possível no ciclo de criação. Por fim, terá menos tempo de desenvolvimento e menos recursos e artefactos da sua cloud consistente se tornará ainda mais valiosos.

A imagem seguinte mostra um exemplo típico de um processo de desenvolvimento para uma equipe usando um ambiente de desenvolvimento integrado (IDE). Em diferentes fases na linha da tempo, os tipos de teste diferentes são executados. Aqui, dois desenvolvedores trabalhar para a mesma solução, mas este cenário aplica-se igualmente a um único desenvolvedor ou uma grande equipe. Cada desenvolvedor, normalmente, cria uma cópia local do repositório central, permitindo que cada um deles trabalhar na cópia local, sem afetar as outras que poderá estar a trabalhar nos mesmos arquivos.

![Fluxo de trabalho](./media/templates-cloud-consistency/workflow.png) 

Considere as sugestões seguintes para teste e automatização:

* Faça uso de ferramentas de teste. Por exemplo, o Visual Studio Code e o Visual Studio incluem IntelliSense e outros recursos que podem ajudá-lo a validar os seus modelos.
* Para melhorar a qualidade do código durante o desenvolvimento no IDE do local, execute a análise de código estático com testes de unidade e testes de integração. 
* Para obter uma experiência ainda melhor durante o desenvolvimento inicial, testes de unidade e testes de integração devem apenas ser avisar quando é encontrado um problema e continue com os testes. Dessa forma, pode identificar os problemas resolvidos e priorizar a ordem das alterações, também referida como implementação orientado a testes (TDD).
* Lembre-se de que alguns testes podem ser executadas sem a ser ligado ao Azure Resource Manager. Outros, como teste de implementação do modelo, exigem o Resource Manager para executar determinadas ações que não podem ser executadas offline.
* Teste de um modelo de implementação em relação a validação de API não é igual a uma implantação real. Além disso, mesmo se implementar um modelo a partir de um ficheiro local, todas as referências para aninhados modelos no modelo são obtidas pelo Resource Manager diretamente e artefatos referenciados pelas extensões VM são obtidos pelo agente de VM em execução dentro da VM implementada.

## <a name="next-steps"></a>Passos Seguintes

* [Considerações sobre os modelos do Azure Resource Manager](../azure-stack/user/azure-stack-develop-templates.md)
* [Melhores práticas de modelos do Azure Resource Manager](resource-group-authoring-templates.md)
