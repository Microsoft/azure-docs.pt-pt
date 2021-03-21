---
title: Visão geral do ambiente do serviço de aplicações
description: Visão geral sobre o Ambiente de Serviço de Aplicações
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 03/02/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 23b23340550ded3642d19500270f06cfb6faf8cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735102"
---
# <a name="app-service-environment-overview"></a>Visão geral do ambiente do serviço de aplicações 

> [!NOTE]
> Este artigo é sobre o App Service Environment v3 (pré-visualização)
> 

O Ambiente de Serviço de Aplicações do Azure é uma funcionalidade do Serviço de Aplicações do Azure que fornece um ambiente totalmente isolado e dedicado para executar com segurança as aplicações do Serviço de Aplicações em grande escala. Esta capacidade pode alojar:

- Aplicações Web do Windows
- Aplicações Web do Linux
- Contentores do Docker
- Funções

Os ambientes de Serviço de Aplicações (ASEs) são adequados para cargas de trabalho de aplicações que exigem:

- Em alta escala.
- Isolamento e acesso a uma rede segura.
- Utilização de memória elevada.
- Pedidos elevados por segundo (RPS). Você pode fazer várias ASEs em uma única região de Azure ou em várias regiões de Azure. Esta flexibilidade torna as ASEs ideais para a escala horizontal de aplicações apátridas com um elevado requisito de RPS.

As aplicações de anfitrião da ASE de apenas um cliente e fazê-lo em um dos seus VNets. Os clientes têm um controlo otimizado sobre o tráfego de rede de aplicações de entrada e saída. As aplicações podem estabelecer ligações seguras de alta velocidade em VPNs aos recursos da empresa no local.

A ASEv3 vem com o seu próprio nível de preços, V2 isolado.
Os Ambientes de Serviço de Aplicações v3 fornecem um ambiente envolvente para proteger as suas aplicações numa sub-rede da sua rede e fornece a sua própria implantação privada do Azure App Service.
É possível utilizar múltiplos ASEs para dimensionar horizontalmente. As aplicações em execução em ASEs podem ter o seu acesso protegido por dispositivos a montante, tais como firewalls de aplicações Web (WAFs). Para obter mais informações, veja Firewall de aplicações Web (WAF).

## <a name="usage-scenarios"></a>Cenários de utilização

O Ambiente de Serviço de Aplicações tem muitos casos de uso, incluindo:

- Aplicações internas de linha de negócio
- Candidaturas que precisam de mais de 30 instâncias ASP
- Sistema de inquilino único para satisfazer requisitos internos de conformidade ou segurança
- Hospedagem de aplicações isoladas em rede
- Aplicações multi-camadas

Existem várias funcionalidades de networking que permitem que as aplicações no Serviço de Aplicações multi-inquilinos atinjam recursos isolados da rede ou se tornem a rede isolada. Estas funcionalidades estão ativadas ao nível da aplicação.  Com um ASE, não há nenhuma configuração adicional nas aplicações para que estejam no VNet. As aplicações são implantadas num ambiente isolado de rede que já se encontra num VNet. Além da rede de hospedagem ase de aplicações isoladas, é também um sistema de inquilino único. Não há outros clientes a usar o ASE. Se realmente precisa de uma história de isolamento completo, também pode colocar o seu ASE em hardware dedicado. Entre hospedagem de aplicações isoladas em rede, arrendamento único e a capacidade 

## <a name="dedicated-environment"></a>Ambiente dedicado
Um ASE é dedicado exclusivamente a uma única subscrição e pode acolher 200 instâncias totais do Plano de Serviço de Aplicações em vários planos de Serviço de Aplicações. A palavra "exemplo" refere-se ao plano de escala horizontal do plano app. Cada instância é equivalente a um papel de trabalhador. Enquanto um ASE pode ter 200 instâncias totais, um único plano de Serviço de Aplicações V2 isolado pode realizar 100 instâncias. O ASE pode realizar dois planos de Serviço de Aplicações com 100 instâncias em cada um, 200 planos de Serviço de Aplicações de instância única, ou tudo no meio.

Um ASE é composto por front-ends e trabalhos. Os front-ends são responsáveis pela terminação de HTTP/HTTPS e pelo balanceamento de carga automático de pedidos de aplicações num ASE. Os front-ends são adicionados automaticamente à medida que os planos do Serviço de Aplicações no ASE são aumentados horizontalmente.

Os trabalhos são funções que alojam aplicações de cliente. Os trabalhos estão disponíveis em três tamanhos fixos:

- Dois vCPU/8 GB RAM
- Quatro vCPU/16 GB RAM
- Oito vCPU/32 GB RAM

Os clientes não precisam de gerir as extremidades dianteiras e os trabalhadores. Todas as infraestruturas são automaticamente. À medida que os planos do Serviço de Aplicações são criados ou reduzidos verticalmente num ASE, a infraestrutura necessária é adicionada ou removida, conforme adequado.

Há uma taxa para instâncias isoladas do plano de aplicações V2. Se não tem planos de Serviço de Aplicações no seu ASE, é cobrado como se tivesse um plano de Serviço de Aplicações com uma instância dos dois principais trabalhadores.

## <a name="virtual-network-support"></a>Suporte de rede virtual
A funcionalidade ASE é uma implementação do Serviço de Aplicações Azure diretamente na rede virtual Azure Resource Manager de um cliente. Existe sempre um ASE numa sub-rede de uma rede virtual. Pode utilizar as funcionalidades de segurança das redes virtuais para controlar as comunicações de rede de entrada e saída para as suas aplicações.

Os Grupos de Segurança de Rede restringem as comunicações de rede de entrada para a sub-rede onde um ASE se encontra. Pode utilizar NSGs para executar aplicações atrás de dispositivos e serviços a montante, tais como WAFs e fornecedores de SaaS de rede.

As aplicações também precisam de aceder frequentemente a recursos da empresa, tais como bases de dados internas e serviços Web. Se implementar o ASE numa rede virtual que tenha uma ligação VPN à rede no local, as aplicações no ASE podem aceder aos recursos no local. Esta capacidade é verdadeira, independentemente de a VPN ser uma rede de VPNs ou uma VPN Azure ExpressRoute.

## <a name="preview"></a>Pré-visualizar
O App Service Environment v3 está em pré-visualização pública.  Algumas funcionalidades estão a ser adicionadas durante a progressão da pré-visualização. As atuais limitações do ASEv3 incluem:

- Incapacidade de escalar um plano de Serviço de Aplicações para além de 50 instâncias
- Incapacidade de obter um contentor de um registo privado
- Incapacidade de funcionalidades de Serviço de Aplicações não suportadas atualmente para passar pelo VNet do cliente
- Nenhum modelo de implementação externa com um ponto final acessível à Internet
- Sem suporte de linha de comando (AZ CLI e PowerShell)
- Sem capacidade de upgrade de ASEv2 para ASEv3
- Sem suporte FTP
- Sem suporte para algumas funcionalidades do Serviço de Aplicações que passam pelo VNet do cliente. Backup/restauro, referências do Cofre chave nas definições de aplicações, utilizando um registo privado de contentores, e o registo de diagnóstico para armazenamento não funcionará com pontos finais de serviço ou pontos finais privados
    
### <a name="asev3-preview-architecture"></a>Arquitetura de pré-visualização ASEv3
Na pré-visualização da ASEv3, o ASE utilizará pontos finais privados para suportar o tráfego de entrada. O ponto final privado será substituído por equilibradores de carga por GA. Durante a pré-visualização, o ASE não terá construído suporte para um ponto final acessível à Internet. Pode adicionar um Gateway de Aplicação para tal finalidade. A ASE precisa de recursos em duas sub-redes.  O tráfego de entrada fluirá através de um ponto final privado. O ponto final privado pode ser colocado em qualquer sub-rede desde que tenha um endereço disponível que possa ser utilizado por pontos finais privados.  A sub-rede de saída deve estar vazia e delegada no Microsoft.Web/hostingEnvironments. Enquanto usado pelo ASE, a sub-rede de saída não pode ser usada para mais nada.

Com o ASEv3, não existem requisitos de rede de entrada ou saída na sub-rede ASE. Pode controlar o tráfego com grupos de segurança de rede e tabelas de rotas e só afetará o tráfego da sua aplicação. Não apague o ponto final privado associado ao seu ASE, uma vez que essa ação não pode ser desfeita. O ponto final privado utilizado para o ASE é utilizado para todas as aplicações no ASE. 
