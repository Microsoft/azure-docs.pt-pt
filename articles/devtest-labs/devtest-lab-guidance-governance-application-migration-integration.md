---
title: Migração e integração de aplicações em Azure DevTest Labs
description: Este artigo fornece orientações para a governação da infraestrutura da Azure DevTest Labs no contexto da migração e integração de aplicações.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8d86728eefc46c74b49ac610e2207ce5e7ae6a9d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87289359"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Governação da infraestrutura Azure DevTest Labs - Migração e integração de aplicações
Uma vez estabelecido o seu ambiente de laboratório de desenvolvimento/teste, tem de pensar nas seguintes questões:

- Como utilizar o ambiente dentro da sua equipa de projeto?
- Como garantir que segue as políticas organizacionais necessárias e mantém a agilidade para acrescentar valor à sua aplicação?

## <a name="azure-marketplace-images-vs-custom-images"></a>Imagens do Azure Marketplace vs. imagens personalizadas

### <a name="question"></a>Pergunta
Quando devo usar uma imagem do Azure Marketplace vs. minha própria imagem organizacional personalizada?

### <a name="answer"></a>Resposta
O Azure Marketplace deve ser utilizado por padrão, a menos que tenha preocupações específicas ou requisitos organizacionais. Alguns exemplos comuns incluem;

- Configuração complexa de software que requer que uma aplicação seja incluída como parte da imagem base.
- A instalação e instalação de uma aplicação pode demorar muitas horas, o que não é uma utilização eficiente do tempo de computação a ser adicionado numa imagem do Azure Marketplace.
- Os desenvolvedores e testadores requerem acesso a uma máquina virtual rapidamente, e querem minimizar o tempo de configuração de uma nova máquina virtual.
- Condições de conformidade ou regulamentação (por exemplo, políticas de segurança) que devem estar em vigor para todas as máquinas.

A utilização de imagens personalizadas não deve ser considerada de ânimo leve. Introduzem uma complexidade extra, pois agora é preciso gerir ficheiros VHD para as imagens base subjacentes. Também precisa de corrigir rotineiramente essas imagens base com atualizações de software. Estas atualizações incluem novas atualizações do sistema operativo (OS) e quaisquer atualizações ou alterações de configuração necessárias para o próprio pacote de software.

## <a name="formula-vs-custom-image"></a>Fórmula vs. imagem personalizada

### <a name="question"></a>Pergunta
Quando devo usar uma fórmula vs. imagem personalizada?

### <a name="answer"></a>Resposta
Tipicamente, o fator decisivo neste cenário é o custo e a reutilização.

Se tiver um cenário em que muitos utilizadores/laboratórios necessitem de uma imagem com muito software em cima da imagem base, então poderá reduzir o custo criando uma imagem personalizada. Isto significa que a imagem é criada uma vez. Reduz o tempo de configuração da máquina virtual e o custo incorrido devido ao funcionamento da máquina virtual quando ocorre a configuração.

No entanto, um fator adicional a notar é a frequência de alterações ao seu pacote de software. Se executar diariamente construções e exigir que o software esteja nas máquinas virtuais dos seus utilizadores, considere usar uma fórmula em vez de uma imagem personalizada.

## <a name="use-custom-organizational-images"></a>Use imagens organizacionais personalizadas

### <a name="question"></a>Pergunta
Como posso configurar um processo facilmente repetível para trazer as minhas imagens organizacionais personalizadas para um ambiente de DevTest Labs?

### <a name="answer"></a>Resposta
Veja [este vídeo no padrão da Image Factory.](./devtest-lab-faq.md#blog-post) Este cenário é um cenário avançado, e os scripts fornecidos são apenas scripts de amostra. Se forem necessárias alterações, é necessário gerir e manter os scripts utilizados no seu ambiente.

Utilizar a DevTest Labs para criar um pipeline de imagem personalizado em Azure Pipelines:

- [Introdução: Prepare os VMs em minutos, instalando uma fábrica de imagens em Azure DevTest Labs](./devtest-lab-faq.md#blog-post)
- [Fábrica de Imagem – Parte 2! Configurar gasodutos Azure e Laboratório de Fábrica para criar VMs](./devtest-lab-faq.md#blog-post)
- [Image Factory – Parte 3: Guardar imagens personalizadas e distribuir para vários laboratórios](./devtest-lab-faq.md#blog-post)
- [Vídeo: Fábrica de Imagem Personalizada com Laboratórios Azure DevTest](./devtest-lab-faq.md#blog-post)

## <a name="patterns-to-set-up-network-configuration"></a>Padrões para configurar a configuração da rede

### <a name="question"></a>Pergunta
Como posso garantir que as máquinas virtuais de desenvolvimento e de teste não conseguem chegar à internet pública? Existem padrões recomendados para configurar a configuração da rede?

### <a name="answer"></a>Resposta
Sim. Há dois aspetos a ter em conta : tráfego de entrada e saída.

**Tráfego de entrada** – Se a máquina virtual não tiver um endereço IP público, então não pode ser alcançada pela internet. Uma abordagem comum consiste em assegurar que seja definida uma política de nível de subscrição, de modo a que nenhum utilizador seja capaz de criar um endereço IP público.

**Tráfego de saída** – Se pretender evitar que as máquinas virtuais vão diretamente para a internet pública e force o tráfego através de uma firewall corporativa, então pode encaminhar o tráfego para o local através de via expressa ou VPN, utilizando o encaminhamento forçado.

> [!NOTE]
> Se tiver um servidor proxy que bloqueie o tráfego sem configurações de procuração, não se esqueça de adicionar exceções à conta de armazenamento de artefactos do laboratório, .

Também pode utilizar grupos de segurança de rede para máquinas virtuais ou sub-redes. Este passo adiciona uma camada adicional de proteção para permitir/bloquear o tráfego.

## <a name="new-vs-existing-virtual-network"></a>Nova vs. rede virtual existente

### <a name="question"></a>Pergunta
Quando devo criar uma nova rede virtual para o meu ambiente DevTest Labs vs. usando uma rede virtual existente?

### <a name="answer"></a>Resposta
Se os seus VMs precisam interagir com a infraestrutura existente, então deve considerar a utilização de uma rede virtual existente dentro do seu ambiente DevTest Labs. Além disso, se utilizar o ExpressRoute, poderá querer minimizar a quantidade de VNets / Subnets para que não fragmente o espaço de endereço IP que é atribuído para utilização nas subscrições. Também deve considerar a utilização do padrão de observação VNet aqui (modelo Hub-Spoke). Esta abordagem permite a comunicação vnet/subnet através de subscrições dentro de uma determinada região, embora espreitar por todas as regiões seja uma característica próxima na rede Azure.

Caso contrário, cada ambiente da DevTest Labs poderia ter a sua própria rede virtual. No entanto, note que existem [limites](../azure-resource-manager/management/azure-subscription-service-limits.md) ao número de redes virtuais por subscrição. O valor por defeito é de 50, embora este limite possa ser aumentado para 100.

## <a name="shared-public-or-private-ip"></a>IP compartilhado, público ou privado

### <a name="question"></a>Pergunta
Quando devo utilizar um IP partilhado vs. IP público vs. IP privado?

### <a name="answer"></a>Resposta
Se utilizar uma VPN site-to-site ou Rota Expressa, considere utilizar iPs privados para que as suas máquinas estejam acessíveis através da sua rede interna e inacessíveis através da internet pública.

> [!NOTE]
> Os proprietários de laboratórios podem alterar esta política de sub-rede para garantir que ninguém cria acidentalmente endereços IP públicos para os seus VMs. O proprietário da subscrição deve criar uma política de subscrição que impeça a criação de IPs públicos.

Ao utilizar iPs públicos partilhados, as máquinas virtuais em um laboratório partilham um endereço IP público. Esta abordagem pode ser útil quando precisa evitar violar os limites dos endereços IP públicos para uma determinada subscrição.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Limites de número de máquinas virtuais por utilizador ou laboratório

### <a name="question"></a>Pergunta
Existe uma regra em termos de quantas máquinas virtuais devo definir por utilizador, ou por laboratório?

### <a name="answer"></a>Resposta
Ao considerar o número de máquinas virtuais por utilizador ou por laboratório, existem três principais preocupações:

- O **custo global** que a equipa pode gastar em recursos no laboratório. É fácil girar muitas máquinas. Para controlar os custos, um dos mecanismos é limitar o número de VMs por utilizador e/ou por laboratório
- O número total de máquinas virtuais num laboratório é impactado pelas [quotas de nível](../azure-resource-manager/management/azure-subscription-service-limits.md) de subscrição disponíveis. Um dos limites superiores é de 800 grupos de recursos por subscrição. A DevTest Labs cria atualmente um novo grupo de recursos para cada VM (a menos que sejam utilizados IPs públicos partilhados). Se houver 10 laboratórios numa subscrição, os laboratórios poderão caber aproximadamente 79 máquinas virtuais em cada laboratório (800 limite superior – 10 grupos de recursos para os 10 laboratórios em si) = 79 máquinas virtuais por laboratório.
- Se o laboratório estiver ligado às instalações via Via Expressa (por exemplo), **existem espaços de endereço IP definidos disponíveis** para a VNet/Subnet. Para garantir que os VMs no laboratório não deixam de ser criados (erro: não consegue obter endereço IP), os proprietários de laboratório podem especificar os VMs máximos por laboratório alinhados com o espaço de endereço IP disponível.

## <a name="use-resource-manager-templates"></a>Utilizar os modelos do Resource Manager

### <a name="question"></a>Pergunta
Como posso usar modelos de Gestor de Recursos no meu Ambiente de Laboratórios DevTest?

### <a name="answer"></a>Resposta
Você implementa os seus modelos de Gestor de Recursos num ambiente DevTest Labs usando os passos mencionados na funcionalidade Ambientes no artigo [de laboratórios DevTest.](devtest-lab-test-env.md) Basicamente, você verifica os seus modelos de Gestor de Recursos em um Repositório Git (ou Azure Repos ou GitHub), e adicione um [repositório privado para os seus modelos](devtest-lab-test-env.md) no laboratório.

Este cenário pode não ser útil se estiver a usar a DevTest Labs para hospedar máquinas de desenvolvimento, mas pode ser útil se estiver a construir um ambiente de preparação, que é representativo da produção.

Vale também a pena notar que o número de máquinas virtuais por laboratório ou por opção de utilizador limita apenas o número de máquinas criadas nativamente no próprio laboratório, e não por quaisquer ambientes (modelos de Gestor de Recursos).

## <a name="next-steps"></a>Passos seguintes
Ver [Ambientes de Utilização em Laboratórios DevTest](devtest-lab-test-env.md).
