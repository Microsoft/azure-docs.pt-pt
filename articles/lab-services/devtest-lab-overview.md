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
ms.openlocfilehash: 599b4325e0eb9aa7f6ccca2626c6d4b14f38149d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339334"
---
# <a name="about-azure-devtest-labs"></a>Sobre o Azure DevTest Labs
O Azure DevTest Labs é um serviço que permite que os desenvolvedores numa equipe para máquinas de virtuais de forma eficiente Self-serviços e/ou recursos de PaaS que precisam para desenvolver, testar, formação e demonstrações etc., sem aguardar aprovações constantes das ferramentas que precisam. 

O laboratório já consiste em bases de previamente configuradas ou modelos do Resource Manager com todas as ferramentas necessárias e software que os desenvolvedores podem usar para criar ambientes. Os desenvolvedores podem criar os seus ambientes em poucos minutos em vez de horas ou dias. 

Com o DevTest Labs, pode testar a versão mais recente da sua aplicação ao fazer as seguintes tarefas:

- Aprovisionar rapidamente ambientes Windows e Linux com modelos e artefactos reutilizáveis
- Integre facilmente o seu pipeline de implementação com o DevTest Labs para aprovisionar ambientes sob demanda
- Aumentar verticalmente o seu teste ao aprovisionar vários agentes de teste de carga e criar ambientes pré-aprovisionados para formação e demonstrações.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>Capacidades
DevTest Labs fornece as seguintes capacidades para programadores que trabalham com máquinas virtuais:

- Crie rapidamente uma máquina virtual ao seguir menos de cinco etapas simples.
- Escolha entre uma lista organizada de bases VM que estão configurados, aprovado e autorizada pelo chefe de equipe ou central IT.
- Crie máquinas a partir de imagens personalizadas previamente criadas que tem o software e ferramentas instaladas na imagem. 
- Crie máquinas de fórmulas que são, essencialmente, imagens personalizadas e uma compilação mais recente do software que está instalado no momento da criação da máquina virtual.
- Instale artefactos que são extensões implementadas na VM depois de ser aprovisionada.
- Configurar o encerramento automático e automaticamente agendas de início nas máquinas para ter o encerramento no final do dia e, em seguida, a cópia de segurança e em execução manhã seguinte.
- Simplesmente solicitar uma máquina virtual pré-criados sem ter do processo de criação da máquina. 

DevTest Labs fornece as seguintes capacidades para programadores que trabalham com ambientes de PaaS:

- Crie ambientes de PaaS do Azure Resource Manager seguindo rapidamente menos de três passos simples.
- Escolha entre uma lista organizada dos modelos do Resource Manager, que são configurados, aprovado e autorizada pelo chefe de equipe ou central IT.
- Prepare um grupo de recursos vazio (área de segurança) através de um modelo de Gestor de recursos para explorar todo do Azure, ainda, permanecendo no contexto do laboratório.
- DevTest Labs fornece as seguintes vantagens em criar, configurar e gerir ambientes de desenvolvimento e teste na cloud

Para além de um modelo de gestão personalizada de mensagens em fila para desenvolvedores numa equipe, permite que o serviço central que a TI controlar desperdiça, otimizar os custos de recursos e mantenha-se dentro de orçamentos, efetuando as seguintes tarefas: 

- Encerramento automático de definição e automática inicie agendas em máquinas virtuais.
- Pode criar a definição de políticas no número de utilizadores de máquinas virtuais.
- Definir políticas sobre os tamanhos de máquinas virtuais e a Galeria de utilizadores de imagens pode escolher entre.
- Controlar os custos e os destinos de definição em laboratório.
- Sendo notificado nos custos estimados elevados para o laboratório, de modo a que possa tomar ações necessárias. 

DevTest Labs fornece as seguintes vantagens em criar, configurar e gerir ambientes na cloud:

## <a name="cost-control-and-governance"></a>Controle de custos e governação
Laboratórios Dev/Test torna mais fácil controlar os custos, permitindo-lhe efetuar as seguintes tarefas:

- Definir políticas no seu laboratório - como o número de máquinas virtuais (VM) por utilizador e do número de VMs por laboratório. 
- Crie políticas para encerrar e iniciar VMs automaticamente.
- Permite-lhe controlar os custos nas máquinas virtuais e os recursos de PaaS rotacionados no interior do seu laboratório, de modo a que podem ser mantidos dentro do seu orçamento predefinido. 
- Ajuda os desenvolvedores a se manter dentro do contexto do laboratório, para que eles não acabam de lançar todos os recursos fora no grupo de recursos subjacentes ou subscrição.

## <a name="quickly-get-to-ready-to-test"></a>Obter rapidamente o teste de preparado
Os laboratórios DevTest permitem criar ambientes pré-aprovisionados com tudo o que sua equipa necessita para começar a desenvolver e testar aplicações. Simplesmente solicitar a ambientes em que a última boa compilação da sua aplicação está instalada e começar a trabalhar de imediato. Em alternativa, utilize os contentores para a criação do ambiente ainda mais rápida e simples.

## <a name="create-once-use-everywhere"></a>Crie uma vez, utilize em todo o lado
Capturar e compartilhar modelos de ambiente de PaaS e artefactos dentro da sua equipa ou organização – tudo no controlo de origem - para criar o desenvolvedor e facilmente os ambientes de teste.

## <a name="worry-free-self-service"></a>Livre Self-Service
DevTest Labs permite que seus desenvolvedores e testadores rapidamente e facilmente cria máquinas de virtuais (IaaS) e recursos de PaaS dentro de alguns cliques usando um conjunto de recursos pré-configurados para os mesmos.

## <a name="use-iaas-and-paas-resources"></a>Utilizar recursos de IaaS e PaaS 
DevTest Labs também permite aos programadores acelerar a recursos de PaaS, como aplicações Web, clusters do Service Fabric, Farms do SharePoint e assim por diante. dentro do laboratório usando um modelo do Resource Manager. Utilizar modelos do Resource Manager do nosso repositório do ambiente público ou ligue o laboratório a seu próprio repositório de Git para começar a utilizar sobre o PaaS nos laboratórios. Também pode controlar os custos nesses recursos para se manterem dentro de seus orçamentos predefinidos. 

## <a name="integrate-with-your-existing-toolchain"></a>Integra na sua coleção de ferramentas existente
Utilize o plug-ins criados previamente ou nossa API para aprovisionar ambientes de Dev/Test diretamente a partir de sua ferramenta preferida de integração contínua (CI), ambiente de desenvolvimento (IDE) integrado ou automatizada de pipeline de lançamento. Também pode utilizar a nossa ferramenta de linha de comandos abrangente.

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos: 

- Para saber mais sobre o DevTest Labs, veja [conceitos de laboratórios DevTest](devtest-lab-concepts.md)
- Para obter instruções com instruções passo a passo, consulte [Tutorial: Configurar um laboratório com o Azure DevTest Labs](tutorial-create-custom-lab.md)


