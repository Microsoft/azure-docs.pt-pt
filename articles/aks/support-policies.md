---
title: Políticas de suporte para o Azure Kubernetes Service (AKS)
description: Saiba mais sobre políticas de suporte, responsabilidade partilhada e funcionalidades que estão em pré-visualização (ou alpha ou beta) do Azure Kubernetes Service (AKS).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 0d2c080be727d2ae13d6d9e5274f17cadffbe640
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786465"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Políticas de suporte do Azure Kubernetes Service

Este artigo fornece detalhes sobre as políticas de suporte técnico e limitações para o Azure Kubernetes Service (AKS). O artigo também detalha o gerenciamento de nós de trabalho, componentes de plano de controle gerenciado, componentes de código aberto de terceiros e gestão de segurança ou de patch.

## <a name="service-updates-and-releases"></a>Versões e atualizações de serviços

* Para informações de versão, consulte [notas de versão do AKS](https://github.com/Azure/AKS/releases).
* Para obter informações sobre as funcionalidades em pré-visualização, veja [AKS pré-visualizar funcionalidades e os projetos relacionados](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Gerido funcionalidades no AKS

Infraestrutura de base como um componentes de cloud de serviço (IaaS), como computação ou de componentes de rede, concede aos utilizadores acesso aos controles de baixo nível e opções de personalização. Por outro lado, o AKS fornece uma implementação de Kubernetes chave na mão, que oferece aos clientes o conjunto comum de configurações e recursos que forem necessários. Os clientes do AKS limitada personalização, implementação e outras opções. Estes clientes não precisam se preocupar sobre ou gerir clusters do Kubernetes diretamente.

Com o AKS, o cliente recebe uma totalmente gerida *plano de controlo*. O plano de controlo contém todos os componentes e serviços que o cliente precisa para operar e fornecer clusters do Kubernetes aos utilizadores finais. Todos os componentes do Kubernetes são mantidos e operados pela Microsoft.

A Microsoft gere e monitoriza os seguintes componentes através do painel de controle:

* Servidores Kubelet ou a API do Kubernetes
* Etcd ou um arquivo de chave-valor compatível, fornecendo Quality of Service (QoS), a escalabilidade e tempo de execução
* Serviços DNS (por exemplo, o kube dns ou o CoreDNS)
* Proxy de Kubernetes ou funcionamento em rede

AKS não é uma solução de cluster totalmente gerido. Alguns componentes, como nós de trabalho, tem *responsabilidade partilhada*, onde os utilizadores devem ajudar a manter o cluster do AKS. Entrada do usuário é necessária, por exemplo, para aplicar um patch de segurança de sistema operativo (SO) de nó de trabalho.

Os serviços estão *geridos* no sentido de que a Microsoft e a equipe AKS implementa, que funciona e é responsáveis pela disponibilidade do serviço e a funcionalidade. Os clientes não é possível alterar estes componentes gerenciados. Microsoft limitando a personalização para garantir uma experiência de usuário consistente e escalável. Para uma solução totalmente personalizável, consulte [mecanismo de AKS](https://github.com/Azure/aks-engine).

> [!NOTE]
> Nós de trabalho do AKS são apresentados no portal do Azure como recursos de IaaS do Azure normais. Mas estas máquinas virtuais são implementadas num grupo de recursos do Azure personalizado (o prefixo MC\\*). É possível alterar nós de trabalho do AKS. Por exemplo, pode utilizar o Secure Shell (SSH) para alterar a forma como alterar normais máquinas de virtuais de nós de trabalho do AKS (no entanto, não é possível, alterar a imagem do SO base, e as alterações poderão não persistem após uma atualização ou reinício), e pode anexar outros recursos do Azure para o AKS nós de trabalho. Mas quando são efetuadas alterações *gestão fora de banda e personalização,* o cluster do AKS pode tornar-se sem suporte. Evite alterar nós de trabalho, a menos que Support da Microsoft o Instrua a fazer alterações.

## <a name="shared-responsibility"></a>Responsabilidade partilhada

Quando é criado um cluster, o cliente define os nós de trabalho do Kubernetes AKS cria. Cargas de trabalho do cliente são executadas em nós. Os clientes o proprietário e podem ver ou modificar os nós de trabalho.

Como nós de cluster de cliente executam código privado e armazenam dados confidenciais, Support da Microsoft podem aceder aos mesmos apenas uma forma limitada. Support da Microsoft não consigo iniciar sessão, execute comandos no ou ver os registos para esses nós sem a permissão de cliente express ou assistência.

Como nós de trabalho são confidenciais, a Microsoft leva muito cuidado para limitar a gestão de segundo plano. Em muitos casos, a sua carga de trabalho irá continuar a ser executado mesmo se o Kubernetes dominar nós, etcd e outras falhas de componentes geridos pela Microsoft. Nós de trabalho de forma impensada modificado podem causar a perda de dados e cargas de trabalho e podem tornar o cluster sem suporte.

## <a name="aks-support-coverage"></a>Cobertura de suporte do AKS

A Microsoft fornece suporte técnico para o seguinte:

* Conectividade a todos os componentes do Kubernetes que o serviço de Kubernetes fornece e suporte, como o servidor de API.
* Plano de serviços (nós principais do Kubernetes, API server, etcd e kube-dns, por exemplo) para controle de gerenciamento, tempo de atividade, QoS e operações do Kubernetes.
* Etcd. O suporte inclui cópias de segurança automatizadas e transparentes de todos os dados de etcd cada 30 minutos para a restauração de estado de planejamento e de cluster após desastre. Estas cópias de segurança não estão diretamente disponíveis para os clientes ou utilizadores. Eles garantem a fiabilidade dos dados e consistência.
* Quaisquer pontos de integração no driver de fornecedor de cloud do Azure para o Kubernetes. Estes incluem integrações noutros serviços do Azure, como balanceadores de carga, volumes persistentes ou funcionamento em rede (Kubernetes e do Azure CNI).
* Perguntas ou problemas sobre a personalização dos componentes de plano de controlo, como o servidor de API do Kubernetes, etcd e kube dns.
* Problemas sobre o funcionamento em rede, como o Azure CNI, kubenet, ou outros acesso à rede e a funcionalidade problemas. Podem incluir a problemas de DNS resolução, perda de pacotes, encaminhamento e assim por diante. A Microsoft suporta vários cenários de rede:
  * Kubenet (básico) e o advanced networking (Azure CNI) dentro do cluster e componentes associados
  * Conectividade para outras aplicações e serviços do Azure
  * Controladores de entrada e de configurações de Balanceador de entrada ou de carga
  * Desempenho de rede e a latência

Microsoft não fornece suporte técnico para o seguinte:

* Perguntas sobre como utilizar o Kubernetes. Por exemplo, Support da Microsoft não fornece conselhos sobre como criar controladores de entrada personalizada, utilize a cargas de trabalho de aplicação ou aplicar os pacotes de software de terceiros ou de código-fonte aberto ou de ferramentas.
  > [!NOTE]
  > Support da Microsoft podem aconselhá-lo no AKS a funcionalidade de cluster, personalização e otimizar (por exemplo, problemas de operações do Kubernetes e procedimentos).
* Projetos de código-fonte aberto de terceiros que não são fornecidos como parte do Kubernetes controlam plano ou implementado com clusters do AKS. Esses projetos podem incluir Istio, Helm, o Envoy ou outras pessoas.
  > [!NOTE]
  > A Microsoft pode fornecer suporte de melhor esforço para projetos de código-fonte aberto de terceiros, como o Helm e Kured. Onde a ferramenta de código-fonte aberto de terceiros se integra com o fornecedor de cloud do Azure do Kubernetes ou outros bugs específicos do AKS, a Microsoft suporta exemplos e aplicações a partir de documentação da Microsoft.
* Software de código fechada de terceiros. Este software pode incluir dispositivos ou software de ferramentas de verificação de segurança e de rede.
* Questões sobre implementações de compilação multicloud ou vários fornecedores. Por exemplo, a Microsoft não suporta questões relacionadas à execução de uma solução de fornecedor de cloud multipublic federado.
* As personalizações que não estão listados de rede a [documentação AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > A Microsoft suporta problemas e erros relacionados com grupos de segurança de rede (NSGs). Por exemplo, Support da Microsoft pode responder a perguntas sobre uma falha NSG para atualizar ou um comportamento inesperado de Balanceador NSG ou de carga.

## <a name="aks-support-coverage-for-worker-nodes"></a>Cobertura de suporte de AKS para nós de trabalho

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilidades da Microsoft para nós de trabalho do AKS

A Microsoft e os clientes partilham responsabilidade por nós de trabalho do Kubernetes em que:

* A imagem do SO base requer adições (por exemplo, monitorização e agentes de rede).
* Os nós de trabalho recebem patches do sistema operacional automaticamente.
* Problemas relacionados com o Kubernetes controlam o plano de componentes que são executados em nós de trabalho são remediados automaticamente. Componentes incluem o seguinte:
  * Kube-proxy
  * componentes de dominar os túneis de rede que fornecem os caminhos de comunicação para o Kubernetes
  * kubelet
  * Daemon do docker ou Moby

> [!NOTE]
> No nó de trabalho, se um componente de plano de controlo não está operacional, a equipe AKS precisará reiniciar o nó de trabalho inteiro. Devido ao respetivo acesso restrito à carga de trabalho de Active Directory do cliente e os dados, a equipe AKS reinicia um nó de trabalho apenas se o problema de escala-o cliente. Sempre que possível, a equipe AKS trabalha para impedir que um reinício necessário a afetar a aplicação.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilidades do cliente para nós de trabalho do AKS

Microsoft automaticamente não reiniciar os nós de trabalho para aplicar patches de nível de sistema operacional. Embora os patches de SO são entregues para os nós de trabalho, o *cliente* é responsável por reiniciar os nós de trabalho para aplicar as alterações. Bibliotecas compartilhadas, daemons, tais como a unidade de estado sólido híbrida (SSHD) e outros componentes no nível do SO ou sistema sejam corrigidas automaticamente.

Os clientes são responsáveis pela execução de atualizações de Kubernetes. Eles podem executar atualizações através do painel de controle do Azure ou a CLI do Azure. Isto aplica-se para atualizações de segurança ou em aperfeiçoamentos na funcionalidade Kubernetes de conter.

> [!NOTE]
> Como o AKS é um *serviço gerido*, suas metas de final incluem a remoção de responsabilidade de patches, atualizações e recolha para tornar a gestão de serviço mais completo e automático de registos. À medida que aumenta a capacidade do serviço para a gestão de ponto-a-ponto, as versões futuras poderão omitir algumas funções (por exemplo, reiniciar o nó e aplicação de patches automática).

### <a name="security-issues-and-patching"></a>Problemas de segurança e aplicação de patches

Se uma falha de segurança for encontrada num ou mais componentes do AKS, a equipe AKS irá corrigir todos os clusters afetados para mitigar o problema. Em alternativa, a equipe irá dar aos utilizadores obter orientações de atualização.

Para nós de trabalho que um afeta de falha de segurança, se um patch sem período de indisponibilidade está disponível, a equipe AKS aplicar esse patch e notificar os utilizadores da alteração.

Quando um patch de segurança precisa reinicializações de nó de trabalho, a Microsoft irá notificar os clientes este requisito. O cliente é responsável por reiniciar ou atualizar para obter a correção de cluster. Se os utilizadores não aplicar os patches, de acordo com orientações do AKS, o cluster continuará a ser vulneráveis ao problema de segurança.

### <a name="node-maintenance-and-access"></a>Manutenção de nó e o acesso

Nós de trabalho são uma responsabilidade partilhada e são detidos pelos clientes. Por este motivo, os clientes têm a capacidade de entrar para os nós de trabalho e fazer alterações potencialmente prejudiciais, tais como atualizações de kernel e a instalação ou remoção de pacotes.

Se os clientes fazer alterações destrutivas ou fazer com que controle os componentes de plano ficar offline ou se tornar não-funcionais, AKS detectará esta falha e restaurar automaticamente o nó de trabalho para o estado de funcionamento anterior.

Embora os clientes podem iniciar sessão no e alterar nós de trabalho, isso não é recomendado porque as alterações podem criar um cluster, sem suporte.

## <a name="network-ports-access-and-nsgs"></a>Portas de rede, acesso e NSGs

Como um serviço gerido, o AKS tem requisitos específicos de sistema de rede e conectividade. Estes requisitos são menos flexíveis do que os requisitos para componentes de IaaS normais. AKS, operações, como personalizar as regras do NSG, bloquear uma porta específica (por exemplo, através de regras de firewall que bloqueia a porta 443 de saída) e URLs da lista de permissões podem tornar o seu cluster sem suporte.

> [!NOTE]
> Atualmente, AKS não permite que bloqueie completamente de saída do seu cluster (por exemplo, domínio explícito ou listas de permissões de porta). A lista de URLs e portas está sujeitas a alterações sem aviso. Pode obter a lista atualizada através da criação de um pedido de suporte do Azure. A lista é apenas para os clientes que estão dispostos a aceitar que sua disponibilidade de cluster poderá ser afetada *em qualquer altura.*

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Funcionalidades não suportadas no Kubernetes alfa e beta

AKS suporta apenas estáveis funcionalidades dentro do projeto de Kubernetes a montante. A menos que caso contrário, documentados, o AKS não suporta funcionalidades de alfa e beta que estão disponíveis no projeto do Kubernetes a montante.

Em dois cenários, recursos alpha ou beta poderão implementados antes de estarem em disponibilidade geral:

* Os clientes tenham satisfeito com o produto do AKS, suporte ou equipes de engenharia e tem sido pedidos para experimentar esses novos recursos.
* Esses recursos foram [ativada por um sinalizador de funcionalidade](https://github.com/Azure/AKS/blob/master/previews.md). Os clientes têm escolher explicitamente para usar esses recursos.

## <a name="preview-features-or-feature-flags"></a>Funcionalidades de pré-visualização ou sinalizadores de recurso

Para recursos e funcionalidade que exigem testes estendidos e comentários dos utilizadores, a Microsoft lança novas funcionalidades de pré-visualização ou recursos por trás de um sinalizador de funcionalidade. Considere esses recursos como versão de pré-lançamento beta ou de recursos.

Funcionalidades de pré-visualização ou funcionalidades de sinalizador de funcionalidade não se destina à produção. As alterações em curso em APIs e comportamento, correções de erros e outras alterações podem resultar em clusters instáveis e tempo de inatividade.

Funcionalidades em pré-visualização pública estão se enquadra na suporte "melhor esforço" como esses recursos estão em pré-visualização e não se destina à produção e são suportados pelas equipes de suporte técnico de AKS durante o horário comercial só. Para obter mais informações veja:

* [FAQ de suporte do Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Funcionalidades de pré-visualização em vigor do Azure *subscrição* nível. Não instale funcionalidades de pré-visualização numa assinatura de produção. Numa assinatura de produção, funcionalidades de pré-visualização podem alterar o comportamento de API padrão e afetar as operações normais.

## <a name="upstream-bugs-and-issues"></a>A montante bugs e problemas

Bugs de dada a velocidade do desenvolvimento no projeto do Kubernetes a montante, Invariavelmente surgem. Alguns desses bugs não podem ser corrigido ou contornado dentro do sistema AKS. Em vez disso, as correções de erros requerem patches maior para projetos a montante (por exemplo, Kubernetes, sistemas de operativos de nó ou de trabalho e kernels). Para os componentes que a Microsoft possui (por exemplo, o fornecedor de cloud do Azure), é confirmadas para corrigir problemas a montante na Comunidade do AKS e a equipe do Azure.

Quando um problema de suporte técnico é a raiz causado por um ou mais bugs a montante, as equipes de suporte e de engenharia do AKS irão:

* Identificar e ligar os bugs a montante com todos os detalhes de suporte para ajudar a explicar por que este problema afeta o seu cluster ou a carga de trabalho. Os clientes recebem links para os repositórios necessários para que possam ver os problemas e ver quando uma nova versão irá fornecer correções.
* Fornece possíveis soluções alternativas ou atenuações. Se o problema pode ser mitigado, um [problema conhecido](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) será arquivado no repositório do AKS. Explica o arquivamento de problema conhecido:
  * O problema, incluindo links para bugs a montante.
  * A solução e os detalhes sobre uma atualização ou de outra persistência da solução.
  * Aproximada linhas cronológicas para a inclusão do problema, com base na cadência de lançamento a montante.
