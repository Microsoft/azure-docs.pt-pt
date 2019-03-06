---
title: Governação da infraestrutura do Azure DevTest Labs
description: Este artigo fornece orientações de governação da infraestrutura do Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 75ce5d6a88b5398bd010cc363b4241bc90068f55
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436583"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Governação da infraestrutura do Azure DevTest Labs - integração e migração de aplicativos
Assim que o seu ambiente de laboratório de desenvolvimento/teste tiver sido estabelecida, precisa pensar sobre as seguintes perguntas:

- Como utilizar o ambiente na sua equipa de projeto?
- Como pode garantir que siga qualquer políticas organizacionais necessárias e manter a agilidade para acrescentar valor à sua aplicação?

## <a name="azure-marketplace-images-vs-custom-images"></a>Imagens do Azure Marketplace versus imagens personalizadas

### <a name="question"></a>Pergunta
Quando devo utilizar uma imagem do Marketplace do Azure vs. minha própria imagem personalizada do organizacional?

### <a name="answer"></a>Resposta
O Azure Marketplace deve ser utilizado por predefinição, a menos que tem preocupações específicas ou requisitos organizacionais. Alguns exemplos comuns incluem;

- Configuração do software complexo que precisa de uma aplicação para ser incluído como parte da imagem base.
- Instalação e configuração de um aplicativo podem demorar várias horas, o que não são um uso eficiente de tempo de computação para ser adicionado a uma imagem do Azure Marketplace.
- Os desenvolvedores e testadores necessitam de acesso a uma máquina virtual rapidamente e desejam minimizar o tempo de configuração de uma nova máquina virtual.
- Conformidade ou regulamentação condições (por exemplo, as políticas de segurança) que devem ser cumpridos para todas as máquinas.

Utilizar imagens personalizadas não deve ser considerado apenas superficialmente. Eles introduzem a complexidade extra, pois agora tem que gerir ficheiros VHD para tais subjacentes imagens base. Também tem de corrigir regularmente as imagens bases com atualizações de software. Estas atualizações incluem novas atualizações de sistema operativo (SO) e quaisquer atualizações ou alterações de configuração necessárias para o pacote de software propriamente dito.

## <a name="formula-vs-custom-image"></a>Fórmula versus a imagem personalizada

### <a name="question"></a>Pergunta
Quando devo utilizar uma fórmula versus a imagem personalizada?

### <a name="answer"></a>Resposta
Normalmente, o fator decisivo neste cenário, o custo é e reutilizar.

Se tiver um cenário em que muitos utilizadores/laboratórios requerem uma imagem com muita software parte superior da imagem base, poderia reduzir os custos através da criação de uma imagem personalizada. Isso significa que a imagem é criada uma vez. Ele reduz o tempo de configuração de máquina virtual e o custo incorrido porque a máquina virtual em execução quando ocorre a configuração.

No entanto, um fator adicional a observar é a frequência das alterações ao seu pacote de software. Se executar o diária baseia-se e exigir que o software seja em máquinas de virtuais dos seus utilizadores, considere a utilização de uma fórmula em vez de uma imagem personalizada.

## <a name="use-custom-organizational-images"></a>Utilizar imagens personalizadas de organizacionais

### <a name="question"></a>Pergunta
Como posso configurar um processo repetível facilmente para trazer as minhas imagens organizacionais personalizadas num ambiente de laboratórios DevTest?

### <a name="answer"></a>Resposta
Ver [este vídeo no padrão de fábrica de imagem](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). Este cenário é um cenário avançado e os scripts fornecidos são apenas os scripts de exemplo. Se forem necessárias quaisquer alterações, precisa gerenciar e manter os scripts utilizados no seu ambiente.

Com o DevTest Labs para criar um pipeline de imagem personalizada em Pipelines do Azure:

- [Introdução: Prepare-se as VMs em minutos por configurar uma fábrica de imagem no Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Imagem Factory – parte 2! Configurar o laboratório de fábrica e Pipelines do Azure para criar VMs](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Imagem Factory – parte 3: Guardar imagens personalizadas e distribuir a vários laboratórios](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Vídeo: Fábrica de imagem personalizada do Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>Padrões para definir a configuração de rede

### <a name="question"></a>Pergunta
Como posso Certifique-se de que o desenvolvimento e teste de máquinas virtuais são não é possível aceder à internet pública? Existem quaisquer padrões recomendados para definir a configuração de rede?

### <a name="answer"></a>Resposta
Sim. Existem dois aspetos a considerar – o tráfego de entrada e saído.

**Tráfego de entrada** – se a máquina virtual não tem um endereço IP público, em seguida, ele não pode ser acessado pela internet. Uma abordagem comum é garantir que está definida uma política de nível de assinatura, que nenhum utilizador é capaz de criar um endereço IP público.

**Tráfego de saída** -se de que pretende impedir que as máquinas virtuais aceder diretamente à internet pública e forçar o tráfego através de uma firewall empresarial, em seguida, pode encaminhar o tráfego no local através de express route ou VPN, através da utilização forçada encaminhamento.

> [!NOTE]
> Se tiver um servidor proxy que bloqueia o tráfego sem as definições de proxy, não se esqueça de adicionar exceções para a conta de armazenamento de artefactos do laboratório.

Também pode usar grupos de segurança de rede para máquinas virtuais ou sub-redes. Este passo adiciona uma camada adicional de proteção para permitir / bloquear o tráfego.

## <a name="new-vs-existing-virtual-network"></a>Novo vs. rede virtual existente

### <a name="question"></a>Pergunta
Quando devo criar uma nova rede virtual para meu ambiente de DevTest Labs vs. utilizar uma rede virtual existente?

### <a name="answer"></a>Resposta
Se as suas VMs precisam interagir com a infraestrutura existente, em seguida, deve considerar utilizar uma rede virtual existente no seu ambiente do DevTest Labs. Além disso, se utilizar o ExpressRoute, pode querer minimizar a quantidade de VNets / sub-redes, de modo a que não fragmentados seu espaço de endereços IP que é atribuído para utilização nas subscrições. Também deve considerar a utilização o VNet peering padrão aqui (modelo de Hub-and-Spoke). Esta abordagem permite comunicação de vnet/sub-rede em várias subscrições dentro de uma determinada região, embora o peering entre regiões é uma funcionalidade de cópia de segurança-trazer no sistema de rede do Azure.

Caso contrário, cada ambiente de DevTest Labs poderia ter sua própria rede virtual. No entanto, tenha em atenção que existem [limites](../azure-subscription-service-limits.md) no número de redes virtuais por subscrição. O período predefinido é 50, apesar deste limite pode ser gerado a 100.

## <a name="shared-public-or-private-ip"></a>IP partilhado, pública ou privada

### <a name="question"></a>Pergunta
Quando devo utilizar um IP partilhado versus o IP público versus privado IP?

### <a name="answer"></a>Resposta
Se utilizar um site-site VPN ou Expressroute, considere a utilização de IPs privados para que as suas máquinas são acessíveis por meio de sua rede interna e acessível pela internet pública.

> [!NOTE]
> Os proprietários de laboratório podem alterar esta política de sub-rede para se certificar de que ninguém acidental cria endereços IP públicos para as suas VMs. O proprietário da subscrição deve criar uma política de subscrição para impedir que os IPs públicos que está sendo criado.

Quando utilizar IPs públicos partilhado, as máquinas virtuais num laboratório de partilhar um endereço IP público. Essa abordagem pode ser útil quando precisa evitar que viola os limites em endereços IP públicos de uma determinada subscrição.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Limites do número de máquinas virtuais por utilizador ou de laboratório

### <a name="question"></a>Pergunta
Existe uma regra em termos de número de máquinas virtuais que deve ser definida por utilizador ou por laboratório?

### <a name="answer"></a>Resposta
Ao considerar o número de máquinas virtuais por utilizador ou por laboratório, existem três preocupações principais:

- O **custo global** que a equipe pode gastar em recursos no laboratório. É fácil criar várias máquinas. Para controlar os custos, é um mecanismo para limitar o número de VMs por utilizador e/ou por laboratório
- O número total de máquinas virtuais num laboratório é afetado pela [quotas de nível de subscrição](../azure-subscription-service-limits.md) disponíveis. É um dos limites superiores 800 grupos de recursos por subscrição. DevTest Labs atualmente cria um novo grupo de recursos para cada VM (a menos que são utilizados os IPs públicos partilhado). Se existirem 10 laboratórios numa subscrição, laboratórios poderiam caber aproximadamente 79 máquinas de virtuais em cada laboratório (800 limite superior – 10 grupos de recursos para os 10 laboratórios propriamente ditos) = 79 máquinas virtuais por laboratório.
- Se o laboratório estiver ligado a no local através de Express Route (por exemplo), existem **definidos espaços de endereços IP disponíveis** para VNet/subrede. Para garantir que as VMs no laboratório não não possível criar (erro: não é possível obter o endereço IP), os proprietários de laboratório, podem especificar as máximas VMs por laboratório alinhado com o espaço de endereço IP disponível.

## <a name="use-resource-manager-templates"></a>Utilizar os modelos do Resource Manager

### <a name="question"></a>Pergunta
Como posso utilizar modelos do Resource Manager no meu ambiente de laboratórios DevTest?

### <a name="answer"></a>Resposta
Implementar modelos do Resource Manager num ambiente de DevTest Labs, utilizando os passos mencionados na [funcionalidade de ambientes no DevTest labs](devtest-lab-test-env.md) artigo. Basicamente, verifique os modelos do Resource Manager para um repositório de Git (repositórios do Azure ou GitHub) e adicione um [repositório privado para seus modelos](devtest-lab-test-env.md) ao laboratório.

Este cenário não pode ser útil se estiver a utilizar o DevTest Labs para máquinas de desenvolvimento do host, mas pode ser útil se estiver criando um ambiente de teste, que é representativo da produção.

Também vale a pena observar que o número de máquinas virtuais por laboratório ou por opção do usuário apenas limita o número de máquinas criadas de forma nativa no laboratório em si e não por qualquer ambientes (modelos do Resource Manager).

## <a name="next-steps"></a>Passos Seguintes
Ver [utilizar ambientes no DevTest Labs](devtest-lab-test-env.md).