---
title: Desenvolvimento colaborativo distribuído dos recursos da Azure DevTest Labs
description: Fornece as melhores práticas para a criação de um ambiente de desenvolvimento distribuído e colaborativo para desenvolver recursos da DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9469591b1945adaffca973828d619d5d06655262
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76170112"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Boas práticas para o desenvolvimento distribuído e colaborativo dos recursos da Azure DevTest Labs
O desenvolvimento colaborativo distribuído permite que diferentes equipas ou pessoas desenvolvam e mantenham uma base de código. Para ser bem sucedido, o processo de desenvolvimento depende da capacidade de criar, partilhar e integrar informação. Este princípio de desenvolvimento chave pode ser usado dentro dos Laboratórios Azure DevTest. Existem vários tipos de recursos dentro de um laboratório que são comumente distribuídos entre diferentes laboratórios dentro de uma empresa. Os diferentes tipos de recursos estão focados em duas áreas:

- Recursos que são armazenados internamente dentro do laboratório (baseado em laboratório)
- Recursos que são armazenados em [repositórios externos que estão ligados ao laboratório](devtest-lab-add-artifact-repo.md) (baseado em repositório de código). 

Este documento descreve algumas boas práticas que permitem a colaboração e distribuição em várias equipas, garantindo a personalização e qualidade a todos os níveis.

## <a name="lab-based-resources"></a>Recursos baseados em laboratório

### <a name="custom-virtual-machine-images"></a>Imagens de máquina virtual personalizadas
Você pode ter uma fonte comum de imagens personalizadas que são implantadas em laboratórios todas as noites. Para obter informações detalhadas, consulte [a fábrica de imagem.](image-factory-create.md)    

### <a name="formulas"></a>Fórmulas
[As fórmulas](devtest-lab-manage-formulas.md) são específicas do laboratório e não têm um mecanismo de distribuição. Os membros do laboratório fazem todo o desenvolvimento de fórmulas. 

## <a name="code-repository-based-resources"></a>Recursos baseados em código
Existem duas características diferentes que são baseadas em repositórios de código, artefactos e ambientes. Este artigo analisa as características e como criar repositórios e fluxos de trabalho mais eficazes para permitir a capacidade de personalizar os artefactos e ambientes disponíveis a nível da organização ou da equipa.  Este fluxo de trabalho baseia-se na estratégia padrão de ramificação de código de [código.](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops) 

### <a name="key-concepts"></a>Conceitos-chave
A informação de origem dos artefactos inclui metadados, scripts. A informação de origem para ambientes inclui metadados e modelos de Gestor de Recursos com quaisquer ficheiros de suporte como scripts PowerShell, scripts DSC, ficheiros Zip, e assim por diante.  

### <a name="repository-structure"></a>Estrutura repositória  
A configuração mais comum para o controlo de códigos de origem (SCC) é criar uma estrutura de vários níveis para armazenar ficheiros de código (modelos de Gestor de Recursos, metadados e scripts) que são usados nos laboratórios. Especificamente, crie diferentes repositórios para armazenar recursos que são geridos pelos diferentes níveis do negócio:   

- Recursos em toda a empresa.
- Unidade de negócio/recursos de divisão
- Recursos específicos da equipa.

Cada um destes níveis liga-se a um repositório diferente onde o ramo principal é obrigado a ser da qualidade de produção. Os [ramos](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) de cada repositório seriam para o desenvolvimento desses recursos específicos (artefactos ou modelos). Esta estrutura alinha-se bem com os Laboratórios DevTest, pois pode facilmente ligar vários repositórios e múltiplos ramos ao mesmo tempo aos laboratórios da organização. O nome do repositório está incluído na interface do utilizador (UI) para evitar confusão quando há nomes, descrição e editor idênticos.
     
O diagrama seguinte mostra dois repositórios: um repositório da empresa que é mantido pela Divisão de TI, e um repositório de divisão mantido pela divisão R&D.

![Um ambiente de desenvolvimento distributivo e colaborativo da amostra](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Esta estrutura em camadas permite o desenvolvimento, mantendo um nível de qualidade mais elevado no ramo principal, ao mesmo tempo que ter múltiplos repositórios ligados a um laboratório permite uma maior flexibilidade.

## <a name="next-steps"></a>Passos seguintes    
Consulte os seguintes artigos:

- Adicione um repositório a um laboratório utilizando o [portal Azure](devtest-lab-add-artifact-repo.md) ou através do modelo de Gestão de [Recursos Azure](add-artifact-repository.md)
- [Artefactos de Laboratórios DevTest](devtest-lab-artifact-author.md)
- [Ambientes de Laboratórios DevTest.](devtest-lab-create-environment-from-arm.md)
