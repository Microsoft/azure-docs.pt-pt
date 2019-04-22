---
title: Políticas de suporte do Azure Kubernetes Service (AKS)
description: Saiba mais sobre políticas de suporte do Azure Kubernetes Service (AKS), responsabilidade partilhada, funcionalidades de pré-visualização/Alpha/Beta.
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: f173fc7c794729eae8c60cceefa88d153800a816
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59502177"
---
# <a name="azure-kubernetes-service-aks-support-policies"></a>Políticas de suporte do Azure Kubernetes Service (AKS)

Este artigo fornece detalhes sobre as políticas de suporte técnico do AKS, limitações, e os detalhes, incluindo o gerenciamento de nós de trabalho, gerido controlam os componentes do plano, os componentes de código aberto de terceiros e segurança / gerenciamento de patches.

## <a name="service-updates--releases"></a>Atualizações de serviços e de versões

* Para informações de versão, veja o [notas de versão do AKS][1]
* Para funcionalidades em pré-visualização pública, consulte [funcionalidades de pré-visualização do AKS e projetos relacionados][2]

## <a name="what-is-managed"></a>O que é "gerido"

Ao contrário do IaaS na cloud componentes de base, como a computação ou o funcionamento em rede, que expõem os controles de baixo nível e opções de personalização para aos utilizadores tirarem partido, o serviço AKS fornece uma implementação de Kubernetes de chave na mão que representa o conjunto comum de configurações e capacidades necessárias para o Kubernetes. Os clientes que utilizam este serviço tem um número limitado de personalizações, implementação e outras opções. Isso também significa que os clientes não precisam se preocupar ou gerir os clusters do Kubernetes diretamente.

Com o AKS, o cliente recebe uma totalmente gerida **plano de controlo** – onde o plano de controlo contém todos os componentes e serviços necessários para operar e fornecer clusters do Kubernetes aos utilizadores finais. Todos os componentes do Kubernetes são mantidos e operados pela Microsoft.

Com o gerenciado **plano de controlo** os seguintes componentes são geridos e monitorizados pela Microsoft:

* Kubelet / servidor (es) de API do Kubernetes
* Etcd ou um arquivo de chave/valor compatível – incluindo qualidade de serviço, escalabilidade e tempo de execução
* Serviços DNS (por exemplo, o kube-dns / CoreDNS)
* Proxy de Kubernetes/redes

AKS não é 100% gerenciada **cluster** solução. Alguns componentes (como nós de trabalho) tem determinados **partilhados responsabilidades** casos em que medidas para manter o cluster do AKS exigem interação do usuário. Por exemplo, nó de trabalho aplicação de patch de segurança do sistema operacional.

 **Gerido**, significa que a Microsoft e a equipe AKS implementa, funciona e é responsável pela disponibilidade e a funcionalidade destes serviços. **Os clientes não é possível alterar estes componentes**. A personalização é limitada para garantir uma experiência de usuário consistente e escalável. Para uma solução totalmente personalizável, consulte [mecanismo de AKS](https://github.com/Azure/aks-engine).

> [!NOTE]
> É importante compreender que nós de trabalho do serviço Kubernetes do Azure são apresentados no Portal do Azure como recursos de IaaS do Azure normal, apesar destas máquinas virtuais são implementadas num grupo de recursos do Azure personalizado (o prefixo MC\\*). Um utilizador pode alterar, SSH neles como máquinas de virtuais normais (no entanto, não é possível, alterar a imagem do SO base, e as alterações poderão não persistem após uma atualização ou reinício), e pode ligar a outros recursos do Azure aos mesmos, ou caso contrário, modificá-los. **No entanto, fazê-lo da gestão fora de banda e personalização significa que o próprio cluster do AKS pode tornar-se sem suporte. Evite a alteração do nó de trabalho, a menos que indicado pelo Support da Microsoft.**

## <a name="what-is-shared-responsibility"></a>O que é uma responsabilidade partilhada

No momento da criação de cluster, o AKS cria um número de nós de trabalho de Kubernetes definidos pelo cliente. Estes nós, conforme observado estão em que as cargas de trabalho do cliente são executadas. Os clientes o proprietário e podem ver ou modificar esses nós de trabalho.

Como estes nós estão em execução de código e armazenam dados confidenciais, o suporte da Microsoft tem **acesso limitado** ao cliente todos os nós de cluster. Suporte da Microsoft não é possível iniciar sessão, executar comandos ou ver os registos para esses nós sem permissão expressa de cliente e/ou assistência para executar comandos de depuração, conforme indicado pela equipe de suporte.

Devido à natureza confidencial estes de nós de trabalho, a Microsoft leva muito cuidado para limitar a gestão de "nos bastidores" de nós. Mesmo que o Kubernetes dominar nós, etcd e outras falhas de componentes (gerida pela Microsoft) continua sua carga de trabalho em execução em muitos casos. Se nós de trabalho são modificados sem cuidado, ele pode resultar na perda de dados e/ou carga de trabalho e tornar o cluster sem suporte.

## <a name="azure-kubernetes-service-support-coverage"></a>Cobertura de suporte do serviço Kubernetes do Azure

**A Microsoft fornece suporte técnico para o seguinte:**

* Conectividade a todos os componentes de Kubernetes fornecida e suportada pelo serviço do Kubernetes (por exemplo, o servidor de API)
* Plano de serviços (etcd de nós, o servidor de API do mestre do Kubernetes, kube-dns, por exemplo para controle de gerenciamento, tempo de atividade, QoS e operações do Kubernetes.
* O suporte de Etcd inclui cópias de segurança automatizadas e transparentes de todos os dados de etcd cada 30 minutos para a restauração de estado de planejamento e de cluster após desastre. Estas cópias de segurança não estão disponíveis diretamente para os clientes/utilizadores e são utilizadas para garantir a fiabilidade dos dados e consistência
* Quaisquer pontos de integração no driver do fornecedor de Cloud do Azure para o Kubernetes como integrações para outro Azure services, como balanceadores de carga, persistente Volumes, de rede (Kubernetes e CNI do Azure)
* Perguntas ou problemas em torno da personalização dos componentes de plano de controlo, como o servidor de API do Kubernetes, etcd e kube dns.
* Problemas sobre os tópicos de funcionamento em rede, como o Azure CNI, Kubenet ou outra rede de problemas de acesso e a funcionalidade (DNS resolução, perda de pacotes, encaminhamento e assim por diante).
  * Cenários de funcionamento em rede suportados incluem Kubenet (básico) e o Advanced Networking (Azure CNI) dentro do cluster e componentes associados, conectividade para outras aplicações e serviços do Azure. Além disso, os controladores de entrada e configuração de Balanceador de carga/entrada, o desempenho de rede e latência são suportadas pela Microsoft.

**A Microsoft fornece suporte técnico para o seguinte:**

* Consultadoria / utilização de "Procedimentos" Kubernetes perguntas, por exemplo como criar controladores de entrada personalizada, perguntas da carga de trabalho de aplicação, e pacotes de third-party/sistemas operacionais ou ferramentas estão fora do escopo.
  * Pedidos de suporte de consultadoria para o AKS cluster funcionalidade, personalização, por exemplo Kubernetes operações problemas/procedimentos-tos estão dentro do âmbito – de otimização.
* Projetos de código aberto de terceiros não são fornecidos como parte do Kubernetes controlam plano ou implementado com clusters do AKS, como Istio, Helm, o Envoy e outras pessoas.
  * Para projetos de código aberto de terceiros, como o Helm e Kured, melhor esforço de suporte é fornecido para obter exemplos e de aplicativos fornecidos na documentação da Microsoft e em que essa ferramenta de código aberto de terceiros se integra com o fornecedor de cloud do Azure do Kubernetes ou outros bugs específicos do AKS.
* Software de código fechada de terceiros – isso pode incluir ferramentas, software ou de dispositivos de rede de verificação de segurança.
* Questões sobre implementações de compilação "multi Clouds" ou de vários fornecedores não são suportados, por exemplo, executar uma solução de fornecedor de cloud pública do federados múltipla não é suportada.
* Personalizações de rede específicas, que não estão documentados em oficial [documentação AKS][3].
  > [!NOTE]
  > Problemas e erros em torno de grupos de segurança de rede é suportada. Por exemplo, o suporte pode responder a perguntas sobre NSGs atualizar a falhar ou comportamento inesperado de NSG ou Balanceador de carga.

## <a name="azure-kubernetes-service-support-coverage-worker-nodes"></a>Cobertura de suporte do serviço Kubernetes do Azure (nós de trabalho)

### <a name="microsoft-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Responsabilidades da Microsoft para nós de trabalho do Azure Kubernetes Service

Conforme observado no `shared responsibility` secção, Kubernetes, nós de trabalho enquadram-se um modelo de responsabilidade do joint, em que:

* Fornece a imagem de sistema operacional base necessárias adições (por exemplo, monitorização e agentes de rede)
* Distribuição automática de patches de sistema operativo para os nós de trabalho
* Correção automática de problemas com o Kubernetes controlar os componentes de plano em execução em nós de trabalho, tais como:
  * kube-proxy
  * componentes de dominar os túneis de rede que fornecem os caminhos de comunicação para o Kubernetes
  * kubelet
  * daemon do docker/moby

> [!NOTE]
> Se um componente de plano de controlo não operacional num nó de trabalho, a equipe AKS poderá ter de reiniciar o nó de trabalho inteiro para resolver o problema. Isso é realizado em nome de um utilizador e não executado, a menos que um Escalamento de cliente é feito (devido de acesso para a carga de trabalho de Active Directory de clientes e os dados). Sempre que possível pessoal AKS funcionará para fazer qualquer necessário reinicie a afetar o aplicativo.

### <a name="customer-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Responsabilidades do cliente para nós de trabalho do Azure Kubernetes Service

**Não, a Microsoft faz:**

- Automaticamente de nós de trabalho de reinicialização do SO do nível patches entrem em vigor **(atualmente, veja abaixo)**

Patches de sistema operativo são entregues a nós de trabalho, no entanto, é o **responsabilidade do cliente** reiniciar os nós de trabalho para que as alterações entrem em vigor. Esta aplicação de patches automática inclui bibliotecas compartilhadas, daemons, como SSHD e outros componentes de nível de sistema/OS.

Para atualizações do Kubernetes, **os clientes são responsáveis pela execução da atualização do** através do painel de controlo do Azure ou a CLI do Azure. Isto aplica-se para as atualizações que contém a segurança ou em aperfeiçoamentos na funcionalidade Kubernetes.

> [!NOTE]
> Como o AKS está um `managed service` incluem de nossas metas de final do serviço de remover a responsabilidade de patches, atualizações, recolha de registos e muito mais para que seja um serviço completamente gerido e prática mais. Estes itens (a reinicialização do nó, a aplicação de patches automática) podem ser removidos em versões futuras, à medida que aumentam a nossas capacidades de ponto a ponto de gestão.

### <a name="security-issues-and-patching"></a>Problemas de segurança e aplicação de patches

Em alguns casos (como) relacionadas, uma falha de segurança pode ser encontrada num ou mais dos componentes do serviço AKS. Em tais cenários, AKS irá corrigir todos os clusters afetados para mitigar o problema se possível, ou fornecer orientação sobre a atualização para os utilizadores.

Para nós de trabalho afetados por essa uma brecha de segurança, se um patch sem período de indisponibilidade do problema estiver disponível, a equipe AKS aplicar essa correção e notificar os utilizadores da alteração.

Se precisar de um patch de segurança reinicia o nó de trabalho, a Microsoft irá notificar cliente este requisito e o cliente é responsável de executar o reinício ou atualizar para obter o patch para o seu cluster. Se os utilizadores não aplicar os patches com orientação do AKS, o cluster continuará a ser vulneráveis ao problema (s).

### <a name="node-maintenance-and-access"></a>Manutenção de nó e o acesso

Porque nós de trabalho são uma responsabilidade partilhada e sob a propriedade dos clientes, os clientes podem iniciar sessão para estas funções de trabalho e efetuar alterações potencialmente perigoso, como atualizações de kernel, a remoção de pacotes e instalar novos pacotes.

Se os clientes executam ações destrutivas ou ações que acionam a componentes de plano de controlo para ir offline ou caso contrário, se tornar não funcional, o serviço AKS irá detetar esta falha e efetuar autoremediation para restaurar o nó de trabalho para o trabalho anterior Estado.

Embora os clientes podem entrar e alterar nós de trabalho, vale *desencorajado* porque esta pode tornar seu cluster sem suporte.

## <a name="network-ports-access-and-network-security-groups"></a>Portas de rede, acesso e os grupos de segurança de rede

Como um serviço gerido, o AKS tem requisitos específicos de sistema de rede e conectividade. Estes requisitos são menos flexíveis do que componentes de IaaS normais. Ao contrário de outros componentes de IaaS, determinadas operações (por exemplo, a personalização de regras do grupo de segurança de rede, bloquear a porta específica, listas de permissões de URL e assim por diante) podem processar o seu cluster sem suporte (por exemplo, regras de firewall a bloquear a porta 443 de saída).

> [!NOTE]
> Completamente o bloqueio de saída (por exemplo, listas de permissões de porta/domínio explícita) do seu cluster não é um cenário AKS suportado neste momento. A lista de URLs e portas está sujeitas a alterações sem aviso e pode ser fornecida pelo suporte do Azure através de um pedido de suporte. A lista fornecida é apenas para os clientes que estão dispostos a aceitar que *a disponibilidade do seu cluster poderá ser afetada em qualquer altura.*

## <a name="alphabeta-kubernetes-features-not-supported"></a>Recursos do Kubernetes Alpha/Beta (não suportados)

AKS só suporta funcionalidades estáveis dentro do projeto de Kubernetes a montante. Funcionalidades de Alpha/Beta disponíveis no Kubernetes de origem a não são suportadas, a menos que caso contrário comunicadas bem documentado.

Existem dois casos em que as funcionalidades Alpha ou Beta poderão ser revertidas antes da disponibilidade geral:

* Os clientes tenham satisfeito com o produto do AKS, suporte ou equipes de engenharia e tem sido pedidos explicitamente para experimentar esses novos recursos.
* Esses recursos foram [ativada através de um sinalizador de funcionalidade] [ 2] (explícita participar)

## <a name="preview-features--feature-flags"></a>Funcionalidades de pré-visualização / sinalizadores de recursos

Para recursos e funcionalidades que requerem a Comunidade de teste, expandida e comentários dos utilizadores, a Microsoft vai lançar novas funcionalidades de pré-visualização ou recursos por trás de um sinalizador de funcionalidade. Esses recursos devem ser considerados pré-lançamento / Beta e estão expostas para dar aos utilizadores uma oportunidade de experimentar estas novas funcionalidades.

No entanto, estes pré-visualizar / sinalizador de funcionalidade funcionalidades não são indicados para uso em produção – APIs, alteração de comportamento, correções de erros e outras alterações podem ser feitas que pode resultar em clusters instáveis e tempo de inatividade. Suporte para esses recursos está limitado ao relatório de bugs/problemas. Não ative estas funcionalidades em sistemas de produção ou subscrições.

> [!NOTE]
> Ativar funcionalidades de pré-visualização tem efeito do Azure **subscrição** nível. Não instale funcionalidades de pré-visualização na subscrição de produção como ele pode alterar o comportamento da API de predefinição afetar as operações normais.

## <a name="upstream-bugs-and-issues"></a>A montante bugs e problemas

Tendo em conta a velocidade do desenvolvimento no projeto do Kubernetes a montante, existem Invariavelmente bugs que não podem ser corrigidos ou trabalharam em dentro do sistema do AKS e em vez disso, precisam de patches maior para projetos a montante (por exemplo, Kubernetes, sistemas operacionais de nó/de trabalho e Kernels). Para componentes que possua (como o fornecedor de Cloud do Azure), o pessoal AKS/Azure é confirmado para corrigir o problema a montante na Comunidade.

Nos casos em que um problema de suporte técnico é causado raiz para um ou mais bugs a montante, suporta do AKS e engenharia fará os seguintes itens:

* Identificar e ligar os bugs a montante com todos os detalhes de suporte sobre por que isso tem impacto no seu cluster e/ou a carga de trabalho. Os clientes serão fornecidos com ligações para os repositórios/problemas necessárias para ver os problemas e ver quando uma nova versão do Kubernetes/outros incluirá o fix(es)
* Possíveis soluções alternativas ou atenuações: Em alguns casos, poderá ser possível solucionar o problema – nesse caso, um "[problema conhecido](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)" irá ser arquivado no repositório do AKS que explica:
  * O problema e a ligação a montante bugs
  * A solução e os detalhes em torno de persistência de atualização/outra da solução
  * Linhas cronológicas aproximadas para inclusão com base na cadência de lançamento a montante

[1]: https://github.com/Azure/AKS/releases
[2]: https://github.com/Azure/AKS/blob/master/previews.md
[3]: https://docs.microsoft.com/azure/aks/
