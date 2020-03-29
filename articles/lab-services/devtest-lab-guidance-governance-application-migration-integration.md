---
title: Migração e integração de candidaturas em Laboratórios Azure DevTest
description: Este artigo fornece orientações para a governação da infraestrutura azure DevTest Labs no contexto da migração e integração de aplicações.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 14641e9096fa9366334e9f7460ae55cda0e6c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644891"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Governação da infraestrutura azure DevTest Labs - Migração e integração de aplicações
Uma vez estabelecido o seu ambiente de laboratório de desenvolvimento/teste, você precisa pensar sobre as seguintes questões:

- Como se utiliza o ambiente dentro da sua equipa de projeto?
- Como garante que segue as políticas organizacionais necessárias e mantém a agilidade para acrescentar valor à sua aplicação?

## <a name="azure-marketplace-images-vs-custom-images"></a>Imagens do Azure Marketplace vs. imagens personalizadas

### <a name="question"></a>Pergunta
Quando devo usar uma imagem do Azure Marketplace vs. a minha própria imagem organizacional personalizada?

### <a name="answer"></a>Resposta
O Azure Marketplace deve ser utilizado por defeito, a menos que tenha preocupações específicas ou requisitos organizacionais. Alguns exemplos comuns incluem;

- Configuração complexa de software que requer que uma aplicação seja incluída como parte da imagem base.
- A instalação e configuração de uma aplicação podem demorar muitas horas, que não são uma utilização eficiente do tempo de computação a ser adicionado numa imagem do Azure Marketplace.
- Os desenvolvedores e testadores requerem acesso a uma máquina virtual rapidamente, e querem minimizar o tempo de configuração de uma nova máquina virtual.
- Conformidade ou condições regulamentares (por exemplo, políticas de segurança) que devem estar em vigor para todas as máquinas.

A utilização de imagens personalizadas não deve ser considerada de ânimo leve. Introduzem uma complexidade extra, já que agora tens de gerir ficheiros VHD para as imagens base subjacentes. Também precisa de remendar rotineiramente essas imagens base com atualizações de software. Estas atualizações incluem novas atualizações do sistema operativo (OS) e quaisquer atualizações ou alterações de configuração necessárias para o próprio pacote de software.

## <a name="formula-vs-custom-image"></a>Fórmula vs. imagem personalizada

### <a name="question"></a>Pergunta
Quando devo usar uma fórmula vs. imagem personalizada?

### <a name="answer"></a>Resposta
Tipicamente, o fator decisivo neste cenário é o custo e a reutilização.

Se tiver um cenário em que muitos utilizadores/laboratórios necessitem de uma imagem com muito software em cima da imagem base, então poderia reduzir o custo criando uma imagem personalizada. Isto significa que a imagem é criada uma vez. Reduz o tempo de configuração da máquina virtual e o custo incorrido devido ao funcionamento da máquina virtual quando ocorre a configuração.

No entanto, um fator adicional a notar é a frequência de alterações no seu pacote de software. Se executar diariamente as construções e exigir que esse software esteja nas máquinas virtuais dos seus utilizadores, considere usar uma fórmula em vez de uma imagem personalizada.

## <a name="use-custom-organizational-images"></a>Use imagens organizacionais personalizadas

### <a name="question"></a>Pergunta
Como posso configurar um processo facilmente repetível para trazer as minhas imagens organizacionais personalizadas para um ambiente de Laboratórios DevTest?

### <a name="answer"></a>Resposta
Veja [este vídeo no padrão image Factory](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). Este cenário é um cenário avançado, e os scripts fornecidos são apenas scripts de amostra. Se forem necessárias alterações, é necessário gerir e manter os scripts utilizados no seu ambiente.

Utilizando a DevTest Labs para criar um pipeline de imagem personalizado em Pipelines Azure:

- [Introdução: Prepare os VMs em minutos através da criação de uma fábrica de imagem em Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Fábrica de Imagem – Parte 2! Configuração de Oleodutos Azure e Laboratório de Fábrica para Criar VMs](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Fábrica de Imagem – Parte 3: Guardar imagens personalizadas e distribuir por vários laboratórios](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Vídeo: Custom Image Factory com Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>Padrões para configurar a configuração da rede

### <a name="question"></a>Pergunta
Como posso garantir que o desenvolvimento e o teste das máquinas virtuais não conseguem chegar à internet pública? Existem padrões recomendados para configurar a configuração da rede?

### <a name="answer"></a>Resposta
Sim. Há dois aspetos a ter em conta : tráfego de entrada e saída.

**Tráfego de entrada** – Se a máquina virtual não tiver um endereço IP público, então não pode ser contactado pela internet. Uma abordagem comum é garantir que seja definida uma política de nível de subscrição, de modo a que nenhum utilizador seja capaz de criar um endereço IP público.

**Tráfego de saída** – Se pretender evitar que as máquinas virtuais vão diretamente para a internet pública e force o tráfego através de uma firewall corporativa, então pode encaminhar o tráfego no local através de rota expressa ou VPN, utilizando o encaminhamento forçado.

> [!NOTE]
> Se tiver um servidor proxy que bloqueie o tráfego sem configurações de procuração, não se esqueça de adicionar exceções à conta de armazenamento de artefactos do laboratório, .

Também pode utilizar grupos de segurança de rede para máquinas virtuais ou subredes. Este passo adiciona uma camada adicional de proteção para permitir/bloquear o tráfego.

## <a name="new-vs-existing-virtual-network"></a>Nova rede virtual vs. existente

### <a name="question"></a>Pergunta
Quando devo criar uma nova rede virtual para o meu ambiente DevTest Labs vs. usando uma rede virtual existente?

### <a name="answer"></a>Resposta
Se os seus VMs precisam interagir com a infraestrutura existente, então deve considerar a utilização de uma rede virtual existente dentro do seu ambiente DevTest Labs. Além disso, se utilizar o ExpressRoute, poderá querer minimizar a quantidade de VNets/Subnets para que não fragmente o seu espaço de endereço IP que seja atribuído para uso nas subscrições. Também deve considerar a utilização do padrão de observação VNet aqui (modelo Hub-Spoke). Esta abordagem permite a comunicação vnet/subnet através de subscrições dentro de uma determinada região, embora espreitar por regiões é uma característica futura na rede Azure.

Caso contrário, cada ambiente da DevTest Labs poderia ter a sua própria rede virtual. No entanto, note que existem [limites](../azure-resource-manager/management/azure-subscription-service-limits.md) no número de redes virtuais por subscrição. O valor padrão é de 50, embora este limite possa ser aumentado para 100.

## <a name="shared-public-or-private-ip"></a>IP partilhado, público ou privado

### <a name="question"></a>Pergunta
Quando devo usar um IP partilhado vs. IP público vs. IP privado?

### <a name="answer"></a>Resposta
Se utilizar uma VPN ou Rota Expresso de site-a-site, considere utilizar iPs privados para que as suas máquinas estejam acessíveis através da sua rede interna e inacessíveis através da internet pública.

> [!NOTE]
> Os donos de laboratóriopodem alterar esta política de sub-rede para garantir que ninguém cria acidentalmente endereços IP públicos para os seus VMs. O proprietário da subscrição deve criar uma política de subscrição que impeça a criação de IPs públicos.

Ao usar iPs públicos partilhados, as máquinas virtuais de um laboratório partilham um endereço IP público. Esta abordagem pode ser útil quando precisa evitar violar os limites dos endereços IP públicos para uma determinada subscrição.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Limites do número de máquinas virtuais por utilizador ou laboratório

### <a name="question"></a>Pergunta
Existe uma regra em termos de quantas máquinas virtuais devo definir por utilizador, ou por laboratório?

### <a name="answer"></a>Resposta
Ao considerar o número de máquinas virtuais por utilizador ou por laboratório, existem três preocupações principais:

- O **custo global** que a equipa pode gastar em recursos no laboratório. É fácil girar muitas máquinas. Para controlar os custos, um mecanismo é limitar o número de VMs por utilizador e/ou por laboratório
- O número total de máquinas virtuais num laboratório é impactado pelas quotas de nível de [subscrição](../azure-resource-manager/management/azure-subscription-service-limits.md) disponíveis. Um dos limites superiores é 800 grupos de recursos por subscrição. A Tualmente, a DevTest Labs cria um novo grupo de recursos para cada VM (a menos que sejam utilizados IPs públicos partilhados). Se houver 10 laboratórios numa subscrição, os laboratórios poderão caber cerca de 79 máquinas virtuais em cada laboratório (800 limite superior – 10 grupos de recursos para os próprios 10 laboratórios) = 79 máquinas virtuais por laboratório.
- Se o laboratório estiver ligado às instalações via Rota Expresso (por exemplo), existem **espaços de endereço IP definidos disponíveis** para a VNet/Subnet. Para garantir que os VMs no laboratório não deixam de ser criados (erro: não conseguem obter endereço IP), os donos do laboratório podem especificar os VMs max por laboratório alinhados com o espaço de endereço IP disponível.

## <a name="use-resource-manager-templates"></a>Utilizar os modelos do Resource Manager

### <a name="question"></a>Pergunta
Como posso usar modelos de Gestor de Recursos no meu Ambiente de Laboratórios DevTest?

### <a name="answer"></a>Resposta
Você implementa os seus modelos de Gestor de Recursos em um ambiente DevTest Labs usando passos mencionados na funcionalidade Ambientes no artigo [de laboratórios DevTest.](devtest-lab-test-env.md) Basicamente, você verifica os seus modelos de Gestor de Recursos num Repositório Git (ou Azure Repos ou GitHub), e adiciona um [repositório privado para os seus modelos](devtest-lab-test-env.md) ao laboratório.

Este cenário pode não ser útil se estiver a usar o DevTest Labs para alojar máquinas de desenvolvimento, mas pode ser útil se estiver a construir um ambiente de encenação, que é representativo da produção.

Também vale a pena notar que o número de máquinas virtuais por laboratório ou por opção de utilizador limita apenas o número de máquinas criadas de forma nativa no próprio laboratório, e não por quaisquer ambientes (modelos de Gestor de Recursos).

## <a name="next-steps"></a>Passos seguintes
Ver [Utilize ambientes em Laboratórios DevTest](devtest-lab-test-env.md).