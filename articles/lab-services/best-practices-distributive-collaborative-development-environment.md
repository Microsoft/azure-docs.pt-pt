---
title: Distribuído de desenvolvimento colaborativo de recursos do Azure DevTest Labs | Documentos da Microsoft
description: Disponibiliza as melhores práticas para configurar um ambiente de desenvolvimento distribuído e de colaboração para desenvolver recursos dos laboratórios DevTest.
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
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: d8892b2d00008c9d67f8bc28d1abb7d562dfd95c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079889"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Melhores práticas para o desenvolvimento distribuído e de colaboração de recursos do Azure DevTest Labs
Desenvolvimento de colaboração distribuído permite que equipes diferentes ou as pessoas para desenvolver e manter um código de base. Para ser concluída com êxito, o processo de desenvolvimento depende a capacidade de criar, partilhar e integrar as informações. Este princípio de desenvolvimento chave pode ser utilizado no Azure DevTest Labs. Existem vários tipos de recursos dentro de um laboratório que normalmente são distribuídos entre laboratórios diferentes dentro de uma empresa. Os diferentes tipos de recursos se concentram em duas áreas:

- Recursos que estão armazenados internamente no laboratório (baseado em laboratório)
- Recursos que estão armazenados no [repositórios externos que estão ligados ao laboratório](devtest-lab-add-artifact-repo.md) (com base no repositório de código). 

Este documento descreve algumas das melhores práticas que permitem a colaboração e a distribuição por várias equipes, garantindo a personalização e a qualidade em todos os níveis.

## <a name="lab-based-resources"></a>Recursos baseados em laboratório

### <a name="custom-virtual-machine-images"></a>Imagens de máquina virtual personalizada
Pode ter uma origem comum de imagens personalizadas que são implementadas nos laboratórios de forma noturna. Para obter informações detalhadas, consulte [factory imagem](image-factory-create.md).    

### <a name="formulas"></a>Fórmulas
[Fórmulas](devtest-lab-manage-formulas.md) são específicas do laboratório e não tiver um mecanismo de distribuição. Os membros de laboratório fazem todo o desenvolvimento de fórmulas. 

## <a name="code-repository-based-resources"></a>Recursos de baseados no repositório de código
Existem dois recursos diferentes que se baseiam em repositórios de códigos, artefatos e ambientes. Este artigo vai sobre os recursos e a forma mais eficaz configurar repositórios e fluxo de trabalho para permitir a capacidade de personalizar os artefatos disponíveis e os ambientes no nível de equipe ou ao nível da organização.  Este fluxo de trabalho baseia-se na norma [estratégia de ramificação de controle de código-fonte](/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Conceitos-chave
As informações de origem para artefactos incluem metadados, scripts. As informações de origem para ambientes incluem modelos do Resource Manager e metadados com quaisquer ficheiros de suporte, como scripts do PowerShell, DSC scripts, arquivos Zip e assim por diante.  

### <a name="repository-structure"></a>Estrutura de repositório  
A configuração mais comuns para o controle de código de origem (SCC) é configurar uma estrutura de várias camada para armazenar os arquivos de código (modelos do Resource Manager, metadados e scripts) que são utilizados para nos laboratórios. Especificamente, crie repositórios diferentes para armazenar recursos que são geridos pelos diferentes níveis da empresa:   

- Recursos de toda a empresa.
- Recursos de divisão/unidade-wide Business
- Específicas para a equipe de recursos.

Níveis de ligação para um repositório diferente onde o ramo principal tem de ser da qualidade de produção. O [ramos](/devops/repos/git/git-branching-guidance?view=azure-devops) em cada repositório seria para o desenvolvimento desses recursos específicos (artefactos ou modelos). Esta estrutura alinha bem com o DevTest Labs, pode ligar facilmente vários repositórios e vários ramos em simultâneo para laboratórios da organização. O nome do repositório está incluído na interface de utilizador (IU) para evitar confusão quando existem nomes idênticos, a descrição e o publicador.
     
O diagrama seguinte mostra dois repositórios: um repositório de empresa que é mantido pela divisão de TI e um repositório de divisão mantido pela divisão de R & D.

![Um ambiente de desenvolvimento distributive e de colaboração de exemplo](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Esta estrutura em camadas permite a programação, mantendo um nível mais elevado de qualidade no ramo principal enquanto ter vários repositórios ligados a um laboratório permite maior flexibilidade.

## <a name="next-steps"></a>Passos Seguintes    
Consulte os seguintes artigos:

- Adicionar um repositório para um laboratório usando a [portal do Azure](devtest-lab-add-artifact-repo.md) ou através de [modelo Azure Resource Manager](add-artifact-repository.md)
- [Artefactos de DevTest Labs](devtest-lab-artifact-author.md)
- [Ambientes de DevTest Labs](devtest-lab-create-environment-from-arm.md).
