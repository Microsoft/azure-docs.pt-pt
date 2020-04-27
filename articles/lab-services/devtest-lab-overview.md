---
title: Sobre o Azure DevTest Labs [ Laboratórios Azure DevTest] Microsoft Docs
description: Saiba como a DevTest Labs pode facilitar a criação, gestão e monitorização de máquinas virtuais Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60561460"
---
# <a name="about-azure-devtest-labs"></a>Saiba mais sobre o Azure DevTest Labs
A Azure DevTest Labs permite aos desenvolvedores em equipas gerir eficientemente as máquinas virtuais (VMs) e os recursos PaaS sem esperar por aprovações.

A DevTest Labs cria laboratórios compostos por bases pré-configuradas ou modelos de Gestor de Recursos Azure. Estes têm todas as ferramentas e software necessários que você pode usar para criar ambientes. Você pode criar ambientes em poucos minutos, em oposição a horas ou dias.

Ao utilizar o DevTest Labs, pode testar as versões mais recentes das suas aplicações fazendo as seguintes tarefas:

- Fornecer rapidamente ambientes Windows e Linux utilizando modelos e artefactos reutilizáveis.
- Integre facilmente o pipeline de implementação com o DevTest Labs para aprovisionar ambientes a pedido.
- Aumentar os seus testes de carga, disponibilizando vários agentes de teste e criar ambientes pré-provisionados para treino e demonstrações.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Capacidades
A DevTest Labs fornece as seguintes capacidades aos desenvolvedores que trabalham com VMs:

- Crie VMs rapidamente seguindo menos de cinco passos simples.
- Escolha entre uma lista com curadoria de bases VM que são configuradas, aprovadas e autorizadas pelo líder da equipa ou ti que o centro de TI.
- Crie VMs a partir de imagens personalizadas pré-criadas que tenham todo o software e ferramentas já instaladas. 
- Crie VMs a partir de fórmulas que são essencialmente imagens personalizadas combinadas com as mais recentes construções do software que é instalado quando os VMs são criados. 
- Instale artefactos que sejam extensões implantadas em VMs depois de serem aprovisionados.
- Defina horários de paragem automática e arranque automático em VMs.
- Reclame um VM pré-criado sem passar pelo processo de criação.

A DevTest Labs fornece as seguintes capacidades aos desenvolvedores que trabalham com ambientes PaaS:

- Use o Gestor de Recursos para criar rapidamente ambientes PaaS seguindo menos de três passos simples.
- Escolha entre uma lista com curadoria de modelos de Gestor de Recursos, que são configurados, e autorizados pelo chumbo da equipa ou TI central.
- Rode um grupo de recursos vazios (caixa de areia) usando um modelo de Gestor de Recursos para explorar o Azure no contexto de um laboratório.

A DevTest Labs também permite que as TI centrais controlem os resíduos, otimizeos os custos dos recursos e permaneçam dentro dos orçamentos, fazendo as seguintes tarefas:  

- Definir horários de paragem automática e arranque automático em VMs.
- Definição de políticas sobre o número de VMs que os utilizadores podem criar.
- Definição de políticas sobre os tamanhos dos VMs e imagens de galeria que os utilizadores escolhem.
- Rastreio de custos e definição de alvos em laboratórios.
- Ser notificado sobre custos elevados para laboratórios para que possa tomar as medidas necessárias.

A DevTest Labs oferece os seguintes benefícios na criação, configuração e gestão de ambientes na nuvem.

## <a name="cost-control-and-governance"></a>Controlo e governação de custos
A DevTest Labs facilita o controlo dos custos, permitindo-lhe fazer as seguintes tarefas:

- [Detete políticas nos seus laboratórios](devtest-lab-get-started-with-lab-policies.md), como o número de VMs por utilizador ou por laboratório. 
- Crie [políticas para desligar e](devtest-lab-set-lab-policy.md) iniciar automaticamente VMs.
- Acompanhe os custos dos recursos vMs e PaaS dentro dos laboratórios para se manter dentro [do seu orçamento.](devtest-lab-configure-cost-management.md)
- Fique no contexto dos seus laboratórios para não aumentar os recursos fora deles.

## <a name="quickly-get-to-ready-to-test"></a>Rapidamente chegar ao teste pronto-a-teste
A DevTest Labs permite-lhe criar ambientes pré-provisionados equipados com tudo o que a sua equipa precisa para desenvolver e testar aplicações. Basta [reclamar os ambientes](devtest-lab-add-claimable-vm.md) onde a última boa construção da sua aplicação está instalada e começar a funcionar. Ou use recipientes para uma criação ambiental ainda mais rápida e mais magra.

## <a name="create-once-use-everywhere"></a>Criar uma vez, usar em todos os lugares
Capture e partilhe modelos e [artefactos](add-artifact-repository.md) [ambientais](devtest-lab-create-environment-from-arm.md) PaaS dentro da sua equipa ou organização - tudo no controlo de fontes - para criar facilmente ambientes de desenvolvimento e teste.

## <a name="worry-free-self-service"></a>Self-service sem preocupações
A DevTest Labs permite aos seus desenvolvedores e testadores criar em rápida e fácil criação de [vMs IaaS](devtest-lab-add-vm.md) e [recursos PaaS](devtest-lab-create-environment-from-arm.md) utilizando um conjunto de recursos pré-configurados.

## <a name="use-iaas-and-paas-resources"></a>Utilize recursos IaaS e PaaS 
Os desenvolvedores também podem girar os recursos paaS, tais como clusters Azure Service Fabric, a funcionalidade de Aplicações Web do Azure App Service e fazendas SharePoint, utilizando modelos de Gestor de Recursos. Para começar no PaaS em laboratórios, use os modelos do [repositório](devtest-lab-configure-use-public-environments.md) de ambiente público ou [ligue o laboratório ao seu próprio repositório Git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Também pode rastrear os custos destes recursos para se manter dentro do seu orçamento.

## <a name="integrate-with-your-existing-toolchain"></a>Integre com o seu atual chaveiro
Utilize plug-ins pré-fabricados ou a API para fornecer ambientes de desenvolvimento/teste diretamente a partir da sua ferramenta de integração contínua preferida [(CI),](devtest-lab-integrate-ci-cd-vsts.md)ambiente de desenvolvimento integrado (IDE) ou pipeline de libertação automatizado. Também pode utilizar a ferramenta abrangente de linha de comando.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- Para saber mais sobre devTest Labs, consulte os [conceitos de DevTest Labs.](devtest-lab-concepts.md)
- Para um passeio com instruções passo a passo, consulte [Tutorial: Instale um laboratório utilizando o Azure DevTest Labs](tutorial-create-custom-lab.md).