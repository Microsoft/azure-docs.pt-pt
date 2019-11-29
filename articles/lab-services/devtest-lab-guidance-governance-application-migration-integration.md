---
title: Migração e integração de aplicativos no Azure DevTest Labs
description: Este artigo fornece diretrizes para governança de Azure DevTest Labs infraestrutura no contexto de migração e integração de aplicativos.
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
ms.openlocfilehash: 25342cfbb8ac7ad5538b1f009c75f1d101bfc047
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560637"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Governança de Azure DevTest Labs infraestrutura – migração de aplicativos e integração
Depois que o ambiente de laboratório de desenvolvimento/teste tiver sido estabelecido, você precisará pensar nas seguintes perguntas:

- Como você utiliza o ambiente dentro de sua equipe de projeto?
- Como garantir que você siga as políticas organizacionais necessárias e mantenha a agilidade para agregar valor ao seu aplicativo?

## <a name="azure-marketplace-images-vs-custom-images"></a>Imagens do Azure Marketplace versus imagens personalizadas

### <a name="question"></a>Pergunta
Quando devo usar uma imagem do Azure Marketplace versus minha própria imagem organizacional personalizada?

### <a name="answer"></a>Resposta
O Azure Marketplace deve ser usado por padrão, a menos que você tenha preocupações específicas ou requisitos organizacionais. Alguns exemplos comuns incluem;

- Configuração de software complexo que exige que um aplicativo seja incluído como parte da imagem base.
- A instalação e a configuração de um aplicativo podem levar muitas horas, o que não é um uso eficiente do tempo de computação a ser adicionado em uma imagem do Azure Marketplace.
- Os desenvolvedores e testadores precisam acessar uma máquina virtual rapidamente e desejam minimizar o tempo de configuração de uma nova máquina virtual.
- Condições de conformidade ou regulatórias (por exemplo, políticas de segurança) que devem estar em vigor para todos os computadores.

O uso de imagens personalizadas não deve ser considerado levemente. Elas apresentam complexidade extra, pois agora você precisa gerenciar arquivos VHD para essas imagens base subjacentes. Você também precisa corrigir rotineiramente essas imagens base com atualizações de software. Essas atualizações incluem novas atualizações de sistema operacional (SO) e todas as atualizações ou alterações de configuração necessárias para o próprio pacote de software.

## <a name="formula-vs-custom-image"></a>Fórmula versus imagem personalizada

### <a name="question"></a>Pergunta
Quando devo usar uma fórmula versus uma imagem personalizada?

### <a name="answer"></a>Resposta
Normalmente, o fator decisivo nesse cenário é o custo e a reutilização.

Se você tiver um cenário em que muitos usuários/laboratórios exigem uma imagem com muitos softwares sobre a imagem base, você pode reduzir o custo criando uma imagem personalizada. Isso significa que a imagem é criada uma vez. Ele reduz o tempo de configuração da máquina virtual e o custo incorrido devido à máquina virtual em execução quando a instalação ocorre.

No entanto, um fator adicional a ser observado é a frequência das alterações no pacote de software. Se você executar compilações diárias e exigir que o software esteja nas máquinas virtuais de seus usuários, considere usar uma fórmula em vez de uma imagem personalizada.

## <a name="use-custom-organizational-images"></a>Usar imagens organizacionais personalizadas

### <a name="question"></a>Pergunta
Como posso configurar um processo facilmente reproduzível para trazer minhas imagens organizacionais personalizadas em um ambiente do DevTest Labs?

### <a name="answer"></a>Resposta
Consulte [este vídeo no padrão de fábrica de imagens](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). Esse cenário é um cenário avançado, e os scripts fornecidos são apenas scripts de exemplo. Se forem necessárias alterações, você precisará gerenciar e manter os scripts usados em seu ambiente.

Usando o DevTest Labs para criar um pipeline de imagem personalizada no Azure Pipelines:

- [Introdução: Obtenha VMs prontas em minutos Configurando uma fábrica de imagens no Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Fábrica de imagens – parte 2! Instalação Azure Pipelines e laboratório de fábrica para criar VMs](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Fábrica de imagens – parte 3: salvar imagens personalizadas e distribuí-las para vários laboratórios](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Vídeo: fábrica de imagens personalizada com Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>Padrões para configurar a configuração de rede

### <a name="question"></a>Pergunta
Como fazer garantir que as máquinas virtuais de desenvolvimento e teste não sejam capazes de alcançar a Internet pública? Há padrões recomendados para definir a configuração de rede?

### <a name="answer"></a>Resposta
Sim. Há dois aspectos a serem considerados – tráfego de entrada e de saída.

**Tráfego de entrada** – se a máquina virtual não tiver um endereço IP público, ela não poderá ser acessada pela Internet. Uma abordagem comum é garantir que uma política de nível de assinatura esteja definida, de modo que nenhum usuário seja capaz de criar um endereço IP público.

**Tráfego de saída** – se você quiser impedir que máquinas virtuais passem diretamente para a Internet pública e forçar o tráfego por meio de um firewall corporativo, poderá rotear o tráfego local por meio de rota expressa ou VPN usando o roteamento forçado.

> [!NOTE]
> Se você tiver um servidor proxy que bloqueia o tráfego sem configurações de proxy, não se esqueça de adicionar exceções à conta de armazenamento do artefato do laboratório,.

Você também pode usar grupos de segurança de rede para máquinas virtuais ou sub-redes. Esta etapa adiciona uma camada adicional de proteção para permitir/bloquear o tráfego.

## <a name="new-vs-existing-virtual-network"></a>Novo vs. rede virtual existente

### <a name="question"></a>Pergunta
Quando devo criar uma nova rede virtual para meu ambiente do DevTest Labs versus usar uma rede virtual existente?

### <a name="answer"></a>Resposta
Se suas VMs precisarem interagir com a infraestrutura existente, você deve considerar o uso de uma rede virtual existente dentro do ambiente do DevTest Labs. Além disso, se você usar o ExpressRoute, talvez queira minimizar a quantidade de VNets/sub-redes para não fragmentar o espaço de endereço IP que é atribuído para uso nas assinaturas. Você também deve considerar o uso do padrão de emparelhamento VNet aqui (modelo hub-spoke). Essa abordagem permite a comunicação de vnet/sub-rede entre assinaturas em uma determinada região, embora o emparelhamento entre regiões seja um recurso ativo na rede do Azure.

Caso contrário, cada ambiente do DevTest Labs poderia ter sua própria rede virtual. No entanto, observe que há [limites](../azure-subscription-service-limits.md) no número de redes virtuais por assinatura. O valor padrão é 50, embora esse limite possa ser gerado para 100.

## <a name="shared-public-or-private-ip"></a>IP compartilhado, público ou privado

### <a name="question"></a>Pergunta
Quando devo usar um IP compartilhado vs. IP público vs. IP privado?

### <a name="answer"></a>Resposta
Se você usar uma VPN site a site ou uma rota expressa, considere usar IPs privados para que seus computadores possam ser acessados por meio de sua rede interna e inacessíveis via Internet pública.

> [!NOTE]
> Os proprietários de laboratório podem alterar essa política de sub-rede para garantir que ninguém crie acidentalmente endereços IP públicos para suas VMs. O proprietário da assinatura deve criar uma política de assinatura impedindo que IPs públicos sejam criados.

Ao usar IPs públicos compartilhados, as máquinas virtuais em um laboratório compartilham um endereço IP público. Essa abordagem pode ser útil quando você precisa evitar a violação dos limites em endereços IP públicos para uma determinada assinatura.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Limites de número de máquinas virtuais por usuário ou laboratório

### <a name="question"></a>Pergunta
Há uma regra em termos de quantas máquinas virtuais devo definir por usuário ou por laboratório?

### <a name="answer"></a>Resposta
Ao considerar o número de máquinas virtuais por usuário ou por laboratório, há três preocupações principais:

- O **custo geral** que a equipe pode gastar em recursos no laboratório. É fácil criar vários computadores. Para controlar os custos, um mecanismo é limitar o número de VMs por usuário e/ou por laboratório
- O número total de máquinas virtuais em um laboratório é afetado pelas [cotas de nível de assinatura](../azure-subscription-service-limits.md) disponíveis. Um dos limites superiores é de 800 grupos de recursos por assinatura. Atualmente, o DevTest Labs cria um novo grupo de recursos para cada VM (a menos que IPs públicos compartilhados sejam usados). Se houver 10 laboratórios em uma assinatura, os laboratórios poderão se ajustar a aproximadamente 79 máquinas virtuais em cada laboratório (800 limite superior – 10 grupos de recursos para os 10 laboratórios em si) = 79 máquinas virtuais por laboratório.
- Se o laboratório estiver conectado ao local via rota expressa (por exemplo,), há espaços de **endereço IP definidos disponíveis** para a VNet/sub-rede. Para garantir que as VMs no laboratório não sejam criadas (erro: não é possível obter o endereço IP), os proprietários do laboratório podem especificar o máximo de VMs por laboratório alinhado com o espaço de endereço IP disponível.

## <a name="use-resource-manager-templates"></a>Utilizar os modelos do Gestor de Recursos

### <a name="question"></a>Pergunta
Como posso usar modelos do Resource Manager em meu ambiente do DevTest Labs?

### <a name="answer"></a>Resposta
Você implanta seus modelos do Resource Manager em um ambiente do DevTest Labs usando as etapas mencionadas no artigo [ambientes do DevTest Labs](devtest-lab-test-env.md) . Basicamente, você verifica os modelos do Resource Manager em um repositório git (Azure Repos ou GitHub) e adiciona um [repositório privado para seus modelos](devtest-lab-test-env.md) ao laboratório.

Esse cenário pode não ser útil se você estiver usando o DevTest Labs para hospedar máquinas de desenvolvimento, mas pode ser útil se você estiver criando um ambiente de preparo, que é representativo da produção.

Também vale a pena observar que o número de máquinas virtuais por laboratório ou opção por usuário limita apenas o número de máquinas criadas nativamente no laboratório em si e não por nenhum ambiente (modelos do Resource Manager).

## <a name="next-steps"></a>Passos seguintes
Confira [usar ambientes no DevTest Labs](devtest-lab-test-env.md).