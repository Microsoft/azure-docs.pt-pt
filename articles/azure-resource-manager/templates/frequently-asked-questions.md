---
title: Modelo ARM frequentemente fez perguntas
description: Perguntas frequentes (FAQ) sobre modelos de Gestor de Recursos Azure (modelos ARM).
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e64253aa79cfaeb2655bb091d038dc7c98b198bd
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419410"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>Perguntas frequentes sobre modelos ARM

Este artigo responde frequentemente a perguntas sobre modelos de Gestor de Recursos Azure (modelos ARM).

## <a name="getting-started"></a>Introdução

* **O que são modelos ARM, e por que devo usá-los?**

  Os modelos ARM são ficheiros JSON onde define o que pretende implantar no Azure. Os modelos ajudam-no a implementar uma solução de infraestrutura como código para o Azure. A sua organização pode implantar repetida e de forma fiável a infraestrutura necessária para diferentes ambientes.

  Para saber mais sobre como os modelos ARM o ajudam a gerir a sua infraestrutura Azure, veja [quais são os modelos ARM?](overview.md)

* **Como é que começo com modelos?**

  Para simplificar os modelos ARM, precisa das ferramentas certas. Recomendamos a instalação do [Código do Estúdio Visual](https://code.visualstudio.com/) e a extensão das [ferramentas Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Para uma rápida introdução a estas ferramentas, consulte [Quickstart: Crie modelos ARM com Código de Estúdio Visual](quickstart-create-templates-use-visual-studio-code.md).

  Quando estiver pronto para aprender a criar modelos ARM, inicie a [série tutorial de principiante nos modelos ARM](template-tutorial-create-first-template.md). Estes tutoriais levam-no passo a passo através do processo de construção de um modelo ARM. Aprende-se sobre as diferentes secções do modelo e como trabalhar em conjunto. Este conteúdo também está disponível como [módulo Microsoft Learn.](/learn/modules/authoring-arm-templates/)

* **Devo utilizar modelos ARM ou Terraform para implantar em Azure?**

  Use a opção que mais gosta. Ambos os serviços ajudam-no a automatizar implementações para a Azure.

  Acreditamos que há benefícios em usar modelos ARM sobre outros serviços de infraestrutura como código. Para conhecer esses benefícios, consulte [por que escolher modelos ARM?](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>Construir 2020

* **Perdi a sua apresentação no Microsoft Build 2020. A apresentação está disponível para visualização?**

  Sim, por favor, [assista a qualquer hora.](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions)

* **Onde posso obter mais informações sobre as novas funcionalidades que anunciou na Build?**

  Para informações gerais sobre funcionalidades que estamos a trabalhar, junte-se ao nosso [grupo Azure Advisors Deployments Yammer](https://aka.ms/ARMMeet).

  Para saber mais sobre o novo idioma do modelo, [inscreva-se para notificações](https://aka.ms/armLangUpdates).

  Para saber mais sobre as especificações do modelo, consulte [as especificações do modelo do Gestor de Recursos Azure (Preview)](template-specs.md).

## <a name="creating-and-testing-templates"></a>Criação e teste de modelos

* **Onde posso aprender sobre as melhores práticas para modelos ARM?**

  Para recomendações sobre como implementar os seus modelos, consulte as [melhores práticas do modelo ARM](template-best-practices.md). Depois de criar um modelo, executar o [conjunto de ferramentas de teste ARM](https://github.com/azure/arm-ttk). Verifica se o seu modelo corresponde às práticas recomendadas.

* **Criei o meu ambiente através do portal. Existe alguma forma de obter o modelo de um grupo de recursos existente?**

  Sim, pode [exportar o modelo](export-template-portal.md) de um grupo de recursos. O modelo exportado é um bom ponto de partida para aprender sobre modelos, mas você provavelmente vai querer revê-lo antes de usá-lo em um ambiente de produção.

  Ao exportar o modelo, pode selecionar quais os recursos que pretende incluir no modelo.

* **Posso criar um grupo de recursos num modelo ARM e distribuir recursos para ele?**

  Sim, pode criar um grupo de recursos num modelo quando implementar o modelo ao nível da sua subscrição Azure. Para um exemplo de criação de um grupo de recursos e de implantação de recursos, consulte [o grupo de recursos e recursos.](deploy-to-subscription.md#resource-groups)

* **Posso criar uma subscrição num modelo ARM?**

  Sim, para obter mais informações, consulte [programáticamente criar subscrições Azure com as mais recentes APIs](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md).

* **Como posso testar o meu modelo antes de o implementar?**

  Recomendamos que se escorram o conjunto de [ferramentas de teste ARM](https://github.com/azure/arm-ttk) e o [funcionamento do "e se"](template-deploy-what-if.md) nos seus modelos antes de os implantar. O kit de ferramentas de teste verifica se o seu modelo utiliza as melhores práticas. Fornece avisos quando identifica alterações que podem melhorar a forma como implementou o seu modelo.

  A operação "e se" mostra as alterações que o seu modelo irá fazer para o seu ambiente. Pode ver mudanças não intencionais antes de serem implantadas. E se também der retornos quaisquer erros que possa detetar durante a validação do pré-voo. Por exemplo, se o seu modelo contiver um erro sintático, retorna esse erro. Também devolve quaisquer erros que possa determinar sobre o estado final dos recursos mobilizados. Por exemplo, se o seu modelo implementar uma conta de armazenamento com um nome que já está em uso, e se retornar esse erro.

* **Onde posso encontrar informações sobre as propriedades disponíveis para cada tipo de recurso?**

  O Código VS fornece intellisense para trabalhar com as propriedades dos recursos. Também pode ver a referência do [modelo](/azure/templates/) para propriedades e descrições.

* **Preciso criar várias instâncias de um tipo de recurso. Como posso criar um iterador no meu modelo?**

  Utilize o elemento de cópia para especificar mais de uma instância. Pode utilizar cópias em [recursos,](copy-resources.md) [propriedades,](copy-properties.md) [variáveis](copy-variables.md)e [saídas.](copy-outputs.md)

## <a name="template-language"></a>Linguagem do modelo

* **Ouvi dizer que estás a trabalhar numa nova linguagem de modelo. Onde posso saber mais sobre isto?**

  Para conhecer a nova linguagem, veja [o que é Bicep (Preview)?](bicep-overview.md)

* **Existe um plano para apoiar a criação de modelos na YAML?**

  Atualmente, não há nenhum plano para apoiar a YAML. Acreditamos que a nova linguagem de modelo irá oferecer uma solução mais fácil de usar do que YAML ou JSON.

* **Ainda posso escrever modelos em JSON depois de o novo modelo ter sido lançado?**

  Sim, pode continuar a usar modelos JSON.

* **Você vai oferecer uma ferramenta para converter os meus modelos JSON para a nova linguagem de modelo?**

  Sim. Consulte [os modelos ARM de conversão entre JSON e Bicep](bicep-decompile.md).

## <a name="template-specs"></a>Especificações de Modelo

* **Como posso começar com o lançamento de pré-visualização de Especificações de Modelos?**

  Instale a versão mais recente do PowerShell ou do Azure CLI. Para a Azure PowerShell, utilize [a versão 5.0.0 ou mais tarde](/powershell/azure/install-az-ps). Para O Azure CLI, utilize [a versão 2.14.2 ou posterior](/cli/azure/install-azure-cli).

* **Como estão relacionadas as especificações do modelo e as plantas Azure?**

  A Azure Blueprints utilizará as especificações do modelo na sua implementação substituindo o recurso por `blueprint definition` um `template spec` recurso. Vamos fornecer um caminho de migração para converter a definição de planta em uma especificação de modelo, mas a definição de planta APIs ainda será suportada. Não há alterações no `blueprint assignment` recurso. As plantas continuarão a ser uma experiência de utilização para compor um ambiente governado em Azure.

* **As especificações do modelo substituem os modelos ligados?**

  Não, mas as especificações do modelo são projetadas para funcionar bem com modelos ligados. Não é preciso mover o modelo ligado para um ponto final acessível ao público antes de implementar o modelo dos pais. Em vez disso, embala o modelo dos pais e os seus artefactos ao criar a especificação do modelo.

* **As especificações do modelo podem ser partilhadas através de subscrições?**

  Sim, podem ser usados em todas as subscrições desde que o utilizador tenha lido o acesso à especificação do modelo. As especificações do modelo não podem ser usadas entre os inquilinos.

## <a name="scripts-in-templates"></a>Scripts em modelos

* **Posso incluir um script no meu modelo para fazer tarefas que não são possíveis num modelo?**

  Sim, use [scripts de implementação.](deployment-script-template.md) Pode incluir scripts Azure PowerShell ou Azure CLI nos seus modelos. A funcionalidade está em pré-visualização.

* **Posso ainda utilizar extensões de script personalizadas e configuração de estado desejada (DSC)?**

  Essas opções ainda estão disponíveis e não mudaram. Os scripts de implementação são projetados para executar ações que não estão relacionadas com o hóspede VM. Se precisar de executar um script num sistema operativo anfitrião num VM, então a extensão de script personalizado e/ou DSC seria uma escolha melhor. No entanto, os scripts de implementação têm vantagens, tais como definir a duração do tempo limite.

* **Os scripts de implantação são apoiados no Governo de Azure?**

  Sim, podes usar scripts de implantação nos EUA Gov Arizona e us Gov Virginia.

## <a name="preview-changes-before-deployment"></a>Alterações de pré-visualização antes da implementação

* **Posso visualizar as alterações que irão ocorrer antes de implementar um modelo?**

  Sim, use a [funcionalidade "e se".](template-deploy-what-if.md) Avalia o estado atual do seu ambiente e compara-o com o estado que existirá após a implantação. Pode examinar as alterações resumidas para se certificar de que o modelo não tem resultados inesperados.

* **Posso usar o "e se" com os modos incrementais e completos?**

  Sim, ambos os [modos de implantação](deployment-modes.md) são suportados. Para um exemplo de utilização do modo incremental, consulte [executar o funcionamento do que se - funcionamento](template-deploy-what-if.md#run-what-if-operation). Para um exemplo de utilização do modo completo, consulte [Confirmar a eliminação](template-deploy-what-if.md#confirm-deletion).

* **E se funciona com modelos ligados?**

  Sim, e se avaliar o estado do modelo dos pais e os seus modelos ligados.

* **Posso usar o "e se" num gasoduto Azure?**

  Sim, pode usar o "e se" para verificar se o Gasoduto deve continuar.

* **Quando uso o "e se", vejo mudanças de propriedades que não estão no meu modelo. Espera-se este "barulho"?**

  Estamos a trabalhar para reduzir o barulho. Ajuda-nos a melhorar, apresentando problemas no nosso gitHub repo aqui: https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>Visualizador de modelo

* **Há alguma maneira de visualizar o meu modelo ARM e os seus recursos?**

  Temos uma [extensão do Código VS, com contribuição comunitária,](https://aka.ms/ARMVisualizer) que faz um excelente trabalho na visualização do seu modelo ARM. Mostra os recursos que estás a usar e as relações entre eles.

* **Posso usar o visualizador de modelo fora do Código VS?**

  O visualizador do modelo está a ser pré-visualizado no portal. Para mais informações, assista a esta [curta sessão da Build.](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions)

## <a name="deployment-limits"></a>Limites de implantação

* **A quantos grupos de recursos posso implementar numa única operação de implantação?**

  No passado, este limite era de cinco grupos de recursos. Foi recentemente aumentado para 800 grupos de recursos. Para obter mais informações, consulte [Criar grupos de recursos e recursos ao nível da subscrição.](deploy-to-subscription.md)

* **Tenho um erro em limitar-me a 800 destacamentos no histórico de implantação. O que devo fazer?**

  Estamos a mudar a forma como o histórico de implantação de um grupo de recursos é mantido. No passado, teve de apagar manualmente as implementações deste histórico para evitar este erro. A partir de junho de 2020, apagaremos automaticamente as implementações da história à medida que se aproxima do limite. Para obter mais informações, consulte [as supressões automáticas do histórico de implantação](deployment-history-deletions.md).

  Excluir uma implantação da história não afeta os recursos implantados.

## <a name="templates-and-devops"></a>Modelos e DevOps

* **Posso integrar modelos ARM em Pipelines Azure?**

  Sim. Para obter uma explicação de como utilizar modelos e oleodutos, consulte [Tutorial: Integração contínua de modelos ARM com Pipelines Azure](deployment-tutorial-pipeline.md) e [Integrar modelos ARM com Pipelines Azure](add-template-to-azure-pipelines.md).

* **Posso usar as ações do GitHub para implementar um modelo?**

  Sim, consulte [os modelos DE IMPLEMENTAÇÃO DO ARM utilizando as ações do GitHub](deploy-github-actions.md).

## <a name="next-steps"></a>Passos seguintes

Para uma introdução aos modelos ARM, veja [quais são os modelos ARM?](overview.md)
