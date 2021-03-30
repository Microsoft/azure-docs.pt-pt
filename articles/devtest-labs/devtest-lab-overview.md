---
title: Sobre a Azure DevTest Labs | Microsoft Docs
description: Saiba como a DevTest Labs pode facilitar a criação, gestão e monitorização de máquinas virtuais Azure
ms.topic: article
ms.date: 06/20/2020
ms.openlocfilehash: b7e6493e5fb1c99dfa7962ed464dca3ebc6b0c7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480206"
---
# <a name="about-azure-devtest-labs"></a>Saiba mais sobre o Azure DevTest Labs
A Azure DevTest Labs permite que os desenvolvedores em equipas auto-gerem eficientemente os recursos virtuais (VMs) e PaaS sem esperar por aprovações.

A DevTest Labs cria laboratórios compostos por bases pré-configuradas ou modelos de Gestor de Recursos Azure. Estes têm todas as ferramentas e software necessários que você pode usar para criar ambientes. Pode criar ambientes em poucos minutos, ao contrário de horas ou dias.

Ao utilizar a DevTest Labs, pode testar as versões mais recentes das suas aplicações fazendo as seguintes tarefas:

- Fornece rapidamente ambientes Windows e Linux utilizando modelos e artefactos reutilizáveis.
- Integre facilmente o pipeline de implementação com o DevTest Labs para aprovisionar ambientes a pedido.
- Dimensione os seus testes de carga fornecendo vários agentes de teste e criar ambientes pré-aprovisionados para treinos e demonstrações.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Capacidades
A DevTest Labs fornece as seguintes capacidades aos desenvolvedores que trabalham com VMs:

- Crie VMs rapidamente seguindo menos de cinco passos simples.
- Escolha entre uma lista com curadoria de bases VM que sejam configuradas, aprovadas e autorizadas pelo líder da equipa ou ti central.
- Crie VMs a partir de imagens personalizadas pré-criadas que tenham todo o software e ferramentas já instalados. 
- Crie VMs a partir de fórmulas que são essencialmente imagens personalizadas combinadas com as mais recentes construções do software que é instalado quando os VMs são criados. 
- Instale artefactos que sejam extensões implantadas em VMs depois de serem a provisionadas.
- Descoda automaticamente e inicie os horários de arranque automático em VMs.
- Reivindicar um VM pré-criado sem passar pelo processo de criação.

A DevTest Labs fornece as seguintes capacidades para programadores que trabalham com ambientes PaaS:

- Utilize o Gestor de Recursos para criar rapidamente ambientes PaaS seguindo menos de três passos simples.
- Escolha entre uma lista com curadoria de modelos de Gestor de Recursos, configurados e autorizados pelo líder da equipa ou ti central.
- Rode um grupo de recursos vazio (caixa de areia) usando um modelo de Gestor de Recursos para explorar o Azure no contexto de um laboratório.

A DevTest Labs também permite que as TI centrais controlem os resíduos, otimizem os custos dos recursos e permaneçam dentro dos orçamentos, fazendo as seguintes tarefas:  

- Configurar horários de paragem automática e de arranque automático em VMs.
- Definição de políticas sobre o número de VMs que os utilizadores podem criar.
- Definição de políticas sobre tamanhos de VMs e imagens de galeria que os utilizadores escolhem.
- Rastreio de custos e fixação de alvos em laboratórios.
- Ser notificado com elevados custos projetados para laboratórios para que possa tomar as medidas necessárias.

A DevTest Labs oferece os seguintes benefícios na criação, configuração e gestão de ambientes na nuvem.

## <a name="cost-control-and-governance"></a>Controlo de custos e governação
A DevTest Labs facilita o controlo dos custos, permitindo-lhe fazer as seguintes tarefas:

- [Desajuste de políticas nos seus laboratórios,](devtest-lab-set-lab-policy.md)tais como o número de VMs por utilizador ou por laboratório. 
- Criar [políticas para desligar automaticamente](devtest-lab-set-lab-policy.md) e iniciar VMs.
- Os custos de rastreio em VMs e recursos PaaS girados dentro de laboratórios para permanecer dentro [do seu orçamento](devtest-lab-configure-cost-management.md).
- Mantenham-se no contexto dos vossos laboratórios para não criarem recursos fora deles.

## <a name="quickly-get-to-ready-to-test"></a>Rapidamente começar a preparar-se para testar
A DevTest Labs permite criar ambientes pré-aprovisionados equipados com tudo o que a sua equipa precisa para desenvolver e testar aplicações. Basta [reivindicar os ambientes](devtest-lab-add-claimable-vm.md) onde a última boa construção da sua aplicação está instalada e começar a trabalhar. Ou use recipientes para uma criação de ambiente ainda mais rápida e mais magra.

## <a name="create-once-use-everywhere"></a>Criar uma vez, usar em todos os lugares
Capture e partilhe [modelos](devtest-lab-create-environment-from-arm.md) e [artefactos](add-artifact-repository.md) de ambiente PaaS dentro da sua equipa ou organização - tudo em controlo de fontes - para criar facilmente ambientes de desenvolvimento e teste.

## <a name="worry-free-self-service"></a>Autosserviço sem preocupações
A DevTest Labs permite aos seus desenvolvedores e testadores criar de forma rápida e fácil os recursos [de IaaS VMs](devtest-lab-add-vm.md) e [PaaS](devtest-lab-create-environment-from-arm.md) utilizando um conjunto de recursos pré-configurados.

## <a name="use-iaas-and-paas-resources"></a>Utilizar recursos iaaS e PaaS 
Os desenvolvedores também podem girar os recursos paaS, tais como clusters de tecido de serviço Azure, a funcionalidade de Aplicações Web do Azure App Service, e as fazendas SharePoint, usando modelos de Gestor de Recursos. Para começar no PaaS em laboratórios, use os modelos do [repositório](devtest-lab-configure-use-public-environments.md) de ambiente público ou [ligue o laboratório ao seu próprio repositório git.](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories) Também pode acompanhar os custos destes recursos para se manter dentro do seu orçamento.

## <a name="integrate-with-your-existing-toolchain"></a>Integre-se com a sua ferramenta existente
Utilize plug-ins pré-fabricados ou a API para providenciar ambientes de desenvolvimento/teste diretamente da sua ferramenta de integração contínua preferida [(CI),](devtest-lab-integrate-ci-cd.md)ambiente de desenvolvimento integrado (IDE) ou gasoduto de libertação automatizado. Também pode utilizar a ferramenta de linha de comando abrangente.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- Para saber mais sobre a DevTest Labs, consulte os [conceitos da DevTest Labs.](devtest-lab-concepts.md)
- Para uma passagem com instruções passo a passo, consulte [Tutorial: Crie um laboratório utilizando a Azure DevTest Labs](tutorial-create-custom-lab.md).