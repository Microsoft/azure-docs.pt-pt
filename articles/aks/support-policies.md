---
title: Políticas de suporte para o serviço kubernetes do Azure (AKS)
description: Saiba mais sobre as políticas de suporte do AKS (serviço de kubernetes do Azure), a responsabilidade compartilhada e os recursos que estão na visualização (ou Alpha ou beta).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 8c1736fcd4356e0778c3b36301b4490764efab76
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240890"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Políticas de suporte para o serviço kubernetes do Azure

Este artigo fornece detalhes sobre as políticas de suporte técnico e as limitações do AKS (serviço kubernetes do Azure). O artigo também detalha o gerenciamento de nós de trabalho, componentes do plano de controle gerenciado, componentes de software livre de terceiros e gerenciamento de segurança ou patch.

## <a name="service-updates-and-releases"></a>Atualizações de serviço e versões

* Para obter informações de versão, consulte [notas de versão do AKS](https://github.com/Azure/AKS/releases).
* Para obter informações sobre os recursos na versão prévia, consulte [recursos de visualização do AKs e projetos relacionados](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Recursos gerenciados no AKS

Os componentes de nuvem de IaaS (infraestrutura como serviço) de base, como componentes de computação ou de rede, oferecem aos usuários acesso a controles de baixo nível e opções de personalização. Por outro lado, o AKS fornece uma implantação kubernetes completa que fornece aos clientes o conjunto comum de configurações e recursos de que precisam. Os clientes do AKS têm personalização, implantação e outras opções limitadas. Esses clientes não precisam se preocupar nem gerenciar clusters kubernetes diretamente.

Com o AKS, o cliente obtém um *plano de controle*totalmente gerenciado. O plano de controle contém todos os componentes e serviços que o cliente precisa para operar e fornecer clusters kubernetes para os usuários finais. Todos os componentes do kubernetes são mantidos e operados pela Microsoft.

A Microsoft gerencia e monitora os seguintes componentes por meio do painel de controle:

* Servidores de API Kubelet ou kubernetes
* Etcd ou um repositório de chave-valor compatível, fornecendo qualidade de serviço (QoS), escalabilidade e tempo de execução
* Serviços DNS (por exemplo, Kube-DNS ou CoreDNS)
* Proxy kubernetes ou rede

O AKS não é uma solução de cluster completamente gerenciada. Alguns componentes, como nós de trabalho, têm *responsabilidade compartilhada*, em que os usuários devem ajudar a manter o cluster AKs. A entrada do usuário é necessária, por exemplo, para aplicar um patch de segurança do sistema operacional do nó de trabalho (SO).

Os serviços são *gerenciados* no sentido de que a Microsoft e a equipe AKs implantam, operam e são responsáveis pela disponibilidade e funcionalidade do serviço. Os clientes não podem alterar esses componentes gerenciados. A Microsoft limita a personalização para garantir uma experiência de usuário consistente e escalonável. Para obter uma solução totalmente personalizável, consulte [AKs Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> Os nós de trabalho do AKS aparecem no portal do Azure como recursos comuns de IaaS do Azure. Mas essas máquinas virtuais são implantadas em um grupo de recursos do Azure personalizado (\\prefixado com MC *). É possível alterar os nós de trabalho do AKS. Por exemplo, você pode usar Secure Shell (SSH) para alterar nós de trabalho do AKS da maneira como altera as máquinas virtuais normais (não é possível, no entanto, alterar a imagem do sistema operacional base, e as alterações podem não persistir por uma atualização ou reinicialização) e você pode anexar outros recursos do Azure ao AKS nós de trabalho. Mas quando você faz alterações *fora do gerenciamento de banda e da personalização,* o cluster AKs pode se tornar incompatível. Evite alterar os nós de trabalho, a menos que Suporte da Microsoft o Direcione para fazer alterações.

## <a name="shared-responsibility"></a>Responsabilidade partilhada

Quando um cluster é criado, o cliente define os nós de trabalho kubernetes que o AKS cria. As cargas de trabalho do cliente são executadas nesses nós. Os clientes possuem e podem exibir ou modificar os nós de trabalho.

Como os nós de cluster do cliente executam código particular e armazenam dados confidenciais, Suporte da Microsoft podem acessá-los apenas de uma maneira limitada. Suporte da Microsoft não pode entrar no, executar comandos no ou exibir logs para esses nós sem permissão ou assistência do cliente do Express.

Como os nós de trabalho são confidenciais, a Microsoft tem muito cuidado para limitar seu gerenciamento em segundo plano. Em muitos casos, sua carga de trabalho continuará a ser executada mesmo que os nós mestre kubernetes, etcd e outros componentes gerenciados pela Microsoft falhem. Nós de trabalho modificados com cuidado podem causar perdas de dados e cargas de trabalho e podem renderizar o cluster sem suporte.

## <a name="aks-support-coverage"></a>Cobertura de suporte do AKS

A Microsoft fornece suporte técnico para o seguinte:

* Conectividade com todos os componentes do kubernetes que o serviço kubernetes fornece e dá suporte, como o servidor de API.
* Gerenciamento, tempo de atividade, QoS e operações dos serviços de plano de controle kubernetes (nós mestre kubernetes, servidor de API, etcd e Kube-DNS, por exemplo).
* Etcd. O suporte inclui backups automatizados e transparentes de todos os dados do etcd a cada 30 minutos para planejamento de desastre e restauração de estado do cluster. Esses backups não estão diretamente disponíveis para clientes ou usuários. Eles garantem a confiabilidade e a consistência dos dados.
* Quaisquer pontos de integração no driver do provedor de nuvem do Azure para kubernetes. Isso inclui integrações em outros serviços do Azure, como balanceadores de carga, volumes persistentes ou rede (kubernetes e CNI do Azure).
* Perguntas ou problemas sobre a personalização de componentes do plano de controle, como o servidor de API kubernetes, o etcd e o Kube-DNS.
* Problemas de rede, como o Azure CNI, kubenet ou outros problemas de acesso à rede e funcionalidade. Problemas podem incluir resolução DNS, perda de pacotes, roteamento e assim por diante. A Microsoft dá suporte a vários cenários de rede:
  * Kubenet (básico) e rede avançada (Azure CNI) dentro do cluster e componentes associados
  * Conectividade com outros serviços e aplicativos do Azure
  * Controladores de entrada e configurações de entrada ou de balanceador de carga
  * Desempenho e latência de rede

A Microsoft não fornece suporte técnico para o seguinte:

* Perguntas sobre como usar o kubernetes. Por exemplo, Suporte da Microsoft não fornece conselhos sobre como criar controladores de entrada personalizados, usar cargas de trabalho de aplicativo ou aplicar pacotes ou ferramentas de software de terceiros ou de código aberto.
  > [!NOTE]
  > Suporte da Microsoft pode recomendar a funcionalidade, a personalização e o ajuste do cluster AKS (por exemplo, problemas e procedimentos de operações do kubernetes).
* Projetos de software livre de terceiros que não são fornecidos como parte do plano de controle kubernetes ou implantados com clusters AKS. Esses projetos podem incluir İSTİO, Helm, Envoy ou outros.
  > [!NOTE]
  > A Microsoft pode fornecer suporte de melhor esforço para projetos de software livre de terceiros, como Helm e Kured. Onde a ferramenta de software livre de terceiros se integra com o provedor de nuvem do kubernetes Azure ou com outros bugs específicos do AKS, a Microsoft dá suporte a exemplos e aplicativos da documentação da Microsoft.
* Software de código-fonte fechado de terceiros. Esse software pode incluir ferramentas de verificação de segurança e dispositivos de rede ou software.
* Problemas sobre o desenvolvimento de multinuvem ou de vários fornecedores. Por exemplo, a Microsoft não dá suporte a problemas relacionados à execução de uma solução de fornecedor de nuvem multipública federada.
* Personalizações de rede diferentes daquelas listadas na [documentação do AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > A Microsoft oferece suporte a problemas e bugs relacionados a NSGs (grupos de segurança de rede). Por exemplo, Suporte da Microsoft pode responder a perguntas sobre uma falha de NSG para atualizar ou um comportamento inesperado de NSG ou de balanceador de carga.

## <a name="aks-support-coverage-for-worker-nodes"></a>Cobertura de suporte do AKS para nós de trabalho

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilidades da Microsoft para nós de trabalho AKS

A Microsoft e os clientes compartilham responsabilidade para nós de trabalho kubernetes em que:

* A imagem do sistema operacional base tem adições necessárias (como agentes de rede e monitoramento).
* Os nós de trabalho recebem patches do sistema operacional automaticamente.
* Problemas com os componentes do plano de controle kubernetes executados nos nós de trabalho são corrigidos automaticamente. Os componentes incluem o seguinte:
  * Kube-proxy
  * Túneis de rede que fornecem caminhos de comunicação para os componentes mestres do kubernetes
  * Kubelet
  * Docker ou daemon do Moby

> [!NOTE]
> Em um nó de trabalho, se um componente de plano de controle não estiver operacional, a equipe AKS poderá precisar reinicializar componentes individuais ou o nó de trabalho inteiro. Essas operações de reinicialização são automatizadas e fornecem correção automática para problemas comuns. Essas reinicializações ocorrem apenas no nível do _nó_ e não no cluster, a menos que sejam uma manutenção ou interrupção de emergência.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilidades do cliente para nós de trabalho do AKS

A Microsoft não reinicia automaticamente os nós de trabalho para aplicar patches no nível do sistema operacional. Embora os patches do sistema operacional sejam entregues aos nós de trabalho, o *cliente* é responsável por reinicializar os nós de trabalho para aplicar as alterações. Bibliotecas compartilhadas, daemons como SSHD (unidade híbrida de estado sólido) e outros componentes no nível do sistema ou do sistema operacional são automaticamente corrigidos.

Os clientes são responsáveis por executar atualizações do kubernetes. Eles podem executar atualizações por meio do painel de controle do Azure ou do CLI do Azure. Isso se aplica a atualizações que contêm melhorias de segurança ou funcionalidade no kubernetes.

> [!NOTE]
> Como o AKS é um *serviço gerenciado*, seus objetivos finais incluem a remoção de responsabilidade por patches, atualizações e coleta de log para tornar o gerenciamento de serviços mais completo e prático. À medida que a capacidade do serviço de gerenciamento de ponta a ponta aumenta, as versões futuras podem omitir algumas funções (por exemplo, reinicialização de nó e aplicação automática de patches).

### <a name="security-issues-and-patching"></a>Problemas de segurança e aplicação de patch

Se uma falha de segurança for encontrada em um ou mais componentes do AKS, a equipe do AKS aplicará patches a todos os clusters afetados para atenuar o problema. Como alternativa, a equipe dará aos usuários diretrizes de atualização.

Para nós de trabalho que uma falha de segurança afeta, se um patch de tempo de inatividade zero estiver disponível, a equipe AKS aplicará esse patch e notificará os usuários sobre a alteração.

Quando um patch de segurança exigir reinicializações de nó de trabalho, a Microsoft notificará os clientes desse requisito. O cliente é responsável por reinicializar ou atualizar para obter o patch do cluster. Se os usuários não aplicarem os patches de acordo com as diretrizes do AKS, o cluster continuará a ficar vulnerável ao problema de segurança.

### <a name="node-maintenance-and-access"></a>Manutenção e acesso do nó

Os nós de trabalho são uma responsabilidade compartilhada e pertencem aos clientes. Por isso, os clientes têm a capacidade de entrar em seus nós de trabalho e fazer alterações potencialmente prejudiciais, como atualizações de kernel e instalar ou remover pacotes.

Se os clientes fizerem alterações destrutivas ou fizerem com que os componentes do plano de controle fiquem offline ou se tornarem não funcionais, o AKS detectará essa falha e restaurará automaticamente o nó de trabalho para o estado de funcionamento anterior.

Embora os clientes possam entrar e alterar os nós de trabalho, isso não é recomendado porque as alterações podem tornar um cluster sem suporte.

## <a name="network-ports-access-and-nsgs"></a>Portas de rede, acesso e NSGs

Como um serviço gerenciado, o AKS tem requisitos específicos de rede e conectividade. Esses requisitos são menos flexíveis do que os requisitos para componentes de IaaS normais. No AKS, operações como a personalização de regras do NSG, o bloqueio de uma porta específica (por exemplo, o uso de regras de firewall que bloqueiam a porta de saída 443) e URLs de lista de permissões podem tornar o cluster incompatível.

> [!NOTE]
> Atualmente, o AKS não permite que você bloqueie completamente o tráfego de saída do cluster. Para controlar a lista de URLs e portas que seu cluster pode usar para tráfego de saída, consulte [limitar o tráfego de saída](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Recursos de kubernetes alfa e Beta sem suporte

O AKS dá suporte apenas a recursos estáveis no projeto upstream kubernetes. Salvo indicação em contrário, o AKS não dá suporte a recursos alfa e beta disponíveis no projeto upstream kubernetes.

Em dois cenários, os recursos alfa ou beta podem ser distribuídos antes de estarem geralmente disponíveis:

* Os clientes foram atendidos com as equipes de produto, suporte ou engenharia AKS e foram solicitados a experimentar esses novos recursos.
* Esses recursos foram [habilitados por um sinalizador de recurso](https://github.com/Azure/AKS/blob/master/previews.md). Os clientes devem optar explicitamente por usar esses recursos.

## <a name="preview-features-or-feature-flags"></a>Recursos de visualização ou sinalizadores de recurso

Para recursos e funcionalidades que exigem testes estendidos e comentários do usuário, a Microsoft lança novos recursos ou recursos de visualização por trás de um sinalizador de recurso. Considere esses recursos como recursos de pré-lançamento ou beta.

Recursos de visualização ou recursos de sinalizador de recurso não são destinados para produção. Alterações contínuas em APIs e comportamento, correções de bugs e outras alterações podem resultar em clusters e tempo de inatividade instáveis.

Os recursos na visualização pública estão sob o suporte de "melhor esforço", pois esses recursos estão em versão prévia e não são destinados à produção e são suportados pelas equipes de suporte técnico do AKS durante o horário comercial. Para obter informações adicionais, consulte:

* [Perguntas frequentes do suporte do Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Os recursos de visualização entram em vigor no nível de *assinatura* do Azure. Não instale os recursos de visualização em uma assinatura de produção. Em uma assinatura de produção, os recursos de visualização podem alterar o comportamento da API padrão e afetar as operações regulares.

## <a name="upstream-bugs-and-issues"></a>Problemas e bugs de upstream

Devido à velocidade de desenvolvimento no projeto kubernetes upstream, surgem bugs invariavelmente. Alguns desses bugs não podem ser corrigidos ou solucionados no sistema AKS. Em vez disso, as correções de bugs exigem patches maiores para projetos upstream (como kubernetes, sistemas operacionais de nó ou de trabalho, e kernels). Para os componentes que a Microsoft possui (como o provedor de nuvem do Azure), o AKS e o pessoal do Azure estão comprometidos em corrigir problemas upstream na Comunidade.

Quando um problema de suporte técnico é causado por um ou mais bugs de upstream, o suporte a AKS e as equipes de engenharia vão:

* Identifique e vincule os bugs de upstream com quaisquer detalhes de suporte para ajudar a explicar por que esse problema afeta o cluster ou a carga de trabalho. Os clientes recebem links para os repositórios necessários para que possam assistir aos problemas e ver quando uma nova versão fornecerá correções.
* Fornecer possíveis soluções alternativas ou atenuações. Se o problema puder ser mitigado, um [problema conhecido](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) será arquivado no repositório AKs. O arquivamento de problemas conhecidos explica:
  * O problema, incluindo links para bugs de upstream.
  * A solução alternativa e os detalhes sobre uma atualização ou outra persistência da solução.
  * Cronogramas aproximados para a inclusão do problema, com base na cadência da versão de upstream.
