---
title: Sobre o Azure DevTest Labs | Documentos da Microsoft
description: Saiba como laboratórios Dev/Test torna fácil de criar, gerir e monitorizar máquinas virtuais do Azure
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
ms.openlocfilehash: e07149865d2dda52e33003964c2852a8aaccf76f
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493200"
---
# <a name="about-azure-devtest-labs"></a>Sobre o Azure DevTest Labs
O Azure DevTest Labs permite que os desenvolvedores em equipes Self-gerenciar com eficiência máquinas virtuais (VMs) e recursos de PaaS sem aguardar aprovação.

DevTest Labs cria laboratórios consistindo em bases de previamente configuradas ou modelos Azure Resource Manager. Estes têm de todas as ferramentas necessárias e software que pode utilizar para criar ambientes. Pode criar ambientes dentro de alguns minutos, em vez de horas ou dias.

Ao utilizar DevTest Labs, pode testar as versões mais recentes das suas aplicações, efetuando as seguintes tarefas:

- Aprovisione rapidamente ambientes Windows e Linux utilizando modelos e artefactos reutilizáveis.
- Integre facilmente o seu pipeline de implementação com o DevTest Labs para aprovisionar ambientes sob demanda.
- Aumentar verticalmente o seu teste ao aprovisionar vários agentes de teste de carga e criar ambientes pré-aprovisionados para formação e demonstrações.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>Capacidades
DevTest Labs fornece as seguintes capacidades para programadores que trabalham com VMs:

- Crie rapidamente VMs por menos de cinco passos simples.
- Escolha entre uma lista organizada de bases VM que estão configurados, aprovado e autorizada pelo chefe de equipe ou central IT.
- Crie VMs a partir de imagens personalizadas previamente criadas que tem o software e ferramentas que já instaladas. 
- Crie VMs a partir de imagens personalizadas, combinadas com as compilações mais recente do software instalado quando as VMs são criadas.
- Instale artefactos que são implementadas em VMs, depois que eles são provisionados de extensões.
- Definir o encerramento automático e início automático agendas em VMs.
- Solicitar uma VM previamente criada sem passar pelo processo de criação.

DevTest Labs fornece as seguintes capacidades para programadores que trabalham com ambientes de PaaS:

- Utilize o Gestor de recursos para criar rapidamente ambientes de PaaS através dos seguintes menos de três passos simples.
- Escolha entre uma lista organizada dos modelos do Resource Manager, que são configuradas e autorizada pelo chefe de equipe ou central IT.
- Prepare um grupo de recursos vazio (área de segurança), utilizando um modelo do Resource Manager para explorar o Azure no contexto de um laboratório.

Os laboratórios DevTest permitem também central departamento de TI controlar desperdiça, otimizar os custos de recursos e mantenha-se dentro de orçamentos, efetuando as seguintes tarefas: 

- Definir agendas de encerramento automático e início automático em VMs.
- Definir políticas de no número de VMs que os utilizadores podem criar.
- Definir políticas de tamanhos das VMs e a imagens de galeria que os utilizadores escolherem.
- Controlar os custos e a definição de destinos de laboratórios práticos.
- Sendo notificado nos custos estimados elevados para laboratórios para que possa tirar as ações necessárias.

DevTest Labs fornece as seguintes vantagens em criar, configurar e gerir ambientes na cloud.

## <a name="control-costs-and-governance"></a>Controlar os custos e governação
Laboratórios Dev/Test torna mais fácil controlar os custos, permitindo-lhe efetuar as seguintes tarefas:

- Definir políticas em seus laboratórios, como o número de VMs por utilizador ou por laboratório. 
- Crie políticas para encerrar e iniciar VMs automaticamente.
- Controlar os custos em recursos de PaaS e VMs funcionando no interior de laboratórios para se manter dentro do seu orçamento.
- Mantenha-se dentro do contexto de seus laboratórios, portanto, não lance recursos fora-los.

## <a name="quickly-get-to-ready-to-test"></a>Obter rapidamente o teste de preparado
DevTest Labs permite-lhe criar ambientes pré-aprovisionados equipados com tudo o que sua equipa necessita para desenvolver e testar aplicações. Simplesmente confirme os ambientes em que a última boa compilação da sua aplicação está instalada e começar a trabalhar. Ou utilize contentores para a criação do ambiente ainda mais rápida, mais simples.

## <a name="create-once-use-everywhere"></a>Crie uma vez, utilize em todo o lado
Capturar e compartilhar modelos de ambiente de PaaS e artefactos dentro da sua equipa ou organização — tudo no controlo de origem — para facilmente criar para programadores e os ambientes de teste.

## <a name="save-time-on-setup"></a>Poupar tempo no programa de configuração  
Pode facilmente criar VMs de IaaS e PaaS recursos utilizando um conjunto de recursos pré-configurados.

## <a name="use-iaas-and-paas-resources"></a>Utilizar recursos de IaaS e PaaS 
Os desenvolvedores podem também Prepare recursos de PaaS, como clusters do Azure Service Fabric, a funcionalidade de aplicações Web do serviço de aplicações do Azure e farms do SharePoint, utilizando modelos do Resource Manager. Para começar a utilizar sobre o PaaS nos laboratórios, utilizar os modelos a partir do repositório do ambiente público ou ligar o laboratório a seu próprio repositório de Git. Também pode controlar os custos nesses recursos para se manter dentro do seu orçamento.

## <a name="integrate-with-your-existing-toolchain"></a>Integra na sua coleção de ferramentas existente
Utilizar o plug-ins criados previamente ou a API para aprovisionar desenvolvimento/teste diretamente os ambientes da sua ferramenta preferida de integração contínua (CI), integrado (IDE) do ambiente de desenvolvimento ou de automatizada de pipeline de lançamento. Também pode utilizar a ferramenta de linha de comandos abrangente.

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- Para saber mais sobre o DevTest Labs, veja [conceitos de laboratórios DevTest](devtest-lab-concepts.md).
- Para obter instruções com instruções passo a passo, consulte [Tutorial: Configurar um laboratório com o Azure DevTest Labs](tutorial-create-custom-lab.md).


