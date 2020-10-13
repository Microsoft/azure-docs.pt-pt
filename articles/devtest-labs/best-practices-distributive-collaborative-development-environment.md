---
title: Desenvolvimento colaborativo distribuído dos recursos da Azure DevTest Labs
description: Fornece as melhores práticas para a criação de um ambiente de desenvolvimento distribuído e colaborativo para desenvolver recursos da DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c18bbbfd3ad727811cc28c424381e5caf32b1cfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483810"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Melhores práticas para desenvolvimento distribuído e colaborativo dos recursos da Azure DevTest Labs
O desenvolvimento colaborativo distribuído permite que diferentes equipas ou pessoas desenvolvam e mantenham uma base de código. Para ser bem sucedido, o processo de desenvolvimento depende da capacidade de criar, partilhar e integrar informação. Este princípio de desenvolvimento chave pode ser usado dentro da Azure DevTest Labs. Existem vários tipos de recursos dentro de um laboratório que são geralmente distribuídos entre diferentes laboratórios dentro de uma empresa. Os diferentes tipos de recursos concentram-se em duas áreas:

- Recursos que são armazenados internamente dentro do laboratório (baseado em laboratório)
- Recursos armazenados em [repositórios externos que estão ligados ao laboratório](devtest-lab-add-artifact-repo.md) (baseado em códigos baseados em repositórios). 

Este documento descreve algumas boas práticas que permitem a colaboração e distribuição em várias equipas, garantindo a personalização e qualidade a todos os níveis.

## <a name="lab-based-resources"></a>Recursos baseados em laboratório

### <a name="custom-virtual-machine-images"></a>Imagens de máquinas virtuais personalizadas
Você pode ter uma fonte comum de imagens personalizadas que são implementadas em laboratórios todas as noites. Para obter informações detalhadas, consulte [a fábrica de imagem.](image-factory-create.md)    

### <a name="formulas"></a>Fórmulas
[As fórmulas](devtest-lab-manage-formulas.md) são específicas do laboratório e não têm um mecanismo de distribuição. Os membros do laboratório fazem todo o desenvolvimento de fórmulas. 

## <a name="code-repository-based-resources"></a>Recursos baseados em repositórios de código
Existem duas características diferentes que são baseadas em repositórios de código, artefactos e ambientes. Este artigo analisa as funcionalidades e como configurar de forma mais eficaz repositórios e fluxo de trabalho para permitir a capacidade de personalizar os artefactos e ambientes disponíveis a nível de organização ou de equipa.  Este fluxo de trabalho baseia-se na estratégia padrão [de ramificação do código fonte.](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops) 

### <a name="key-concepts"></a>Conceitos-chave
A informação de origem dos artefactos inclui metadados, scripts. A informação de origem para ambientes inclui metadados e modelos de Gestor de Recursos com quaisquer ficheiros de suporte como scripts PowerShell, scripts DSC, ficheiros Zip, etc.  

### <a name="repository-structure"></a>Estrutura do repositório  
A configuração mais comum para o controlo de código fonte (SCC) é criar uma estrutura de vários níveis para armazenar ficheiros de código (modelos de gestor de recursos, metadados e scripts) que são usados nos laboratórios. Especificamente, crie diferentes repositórios para armazenar recursos que são geridos pelos diferentes níveis do negócio:   

- Recursos de toda a empresa.
- Unidade de negócio/Recursos de divisão
- Recursos específicos da equipa.

Cada um destes níveis liga-se a um repositório diferente, onde o ramo principal é obrigado a ser da qualidade de produção. Os [ramos](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) de cada repositório seriam para o desenvolvimento desses recursos específicos (artefactos ou modelos). Esta estrutura alinha-se bem com a DevTest Labs, uma vez que pode facilmente ligar vários repositórios e vários ramos ao mesmo tempo com os laboratórios da organização. O nome do repositório está incluído na interface do utilizador (UI) para evitar confusão quando existem nomes, descrições e editores idênticos.
     
O diagrama que se segue mostra dois repositórios: um repositório de empresa que é mantido pela Divisão de TI, e um repositório de divisão mantido pela divisão R&D.

![Um ambiente de desenvolvimento distributivo e colaborativo de amostra](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Esta estrutura em camadas permite o desenvolvimento, mantendo um nível mais elevado de qualidade no ramo principal, ao mesmo tempo que ter múltiplos repositórios ligados a um laboratório permite uma maior flexibilidade.

## <a name="next-steps"></a>Passos seguintes    
Consulte os seguintes artigos:

- Adicione um repositório a um laboratório usando o [portal Azure](devtest-lab-add-artifact-repo.md) ou através do [modelo de Gestão de Recursos Azure](add-artifact-repository.md)
- [Artefactos de Laboratórios DevTest](devtest-lab-artifact-author.md)
- [Ambientes de DevTest Labs](devtest-lab-create-environment-from-arm.md).
