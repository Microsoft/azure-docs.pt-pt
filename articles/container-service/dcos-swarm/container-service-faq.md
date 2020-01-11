---
title: PRETERIDO Serviço de contêiner do Azure-perguntas frequentes
description: Respostas às perguntas mais frequentes sobre o Azure Container Service, um serviço que simplifica a criação, configuração e gestão de um cluster de máquinas virtuais para executar aplicações de contentor do Docker .
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: conceptual
ms.date: 03/28/2017
ms.author: mlearned
ms.custom: H1Hack27Feb201
ms.openlocfilehash: 317862b694b93de44422ac3c28575c732ffc5be5
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887925"
---
# <a name="deprecated-container-service-frequently-asked-questions"></a>PRETERIDO Perguntas frequentes sobre o serviço de contêiner

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

## <a name="orchestrators"></a>Orquestradores

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Que orquestradores de contentor suporta no Azure Container Service? 

Existe suporte para DC/SO de código aberto, Docker Swarm e Kubernetes. Para obter mais informações, consulte [Descrição Geral](../kubernetes/container-service-intro-kubernetes.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>Suporta o modo Docker Swarm? 

Atualmente, o modo Swarm não é suportado, mas é no plano de serviço. 

### <a name="does-azure-container-service-support-windows-containers"></a>O Azure Container Service suporta contentores do Windows?  

Atualmente são suportados contentores de Linux com todos os orquestradores. O suporte para contentores do Windows com Kubernetes está em pré-visualização.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Recomenda um orquestrador específico no Azure Container Service? 
Geralmente não recomendamos um orquestrador específico. Se tiver experiência com um dos orquestradores suportados, pode aplicar essa experiência no Azure Container Service. As tendências de dados sugerem, no entanto, que o DC/SO seja comprovado pela produção de Macrodados e as cargas de trabalho de IoT, o Kubernetes é adequado para cargas de trabalho de cloud nativa e o Docker Swarm é conhecido pela integração com ferramentas de Docker e uma fácil uma curva de aprendizagem.

Dependendo do seu cenário, também pode criar e gerir soluções de contentor personalizadas com outros serviços do Azure. Estes serviços incluem [Máquinas Virtuais](../../virtual-machines/linux/overview.md), [Service Fabric](../../service-fabric/service-fabric-overview.md), [Aplicações Web](../../app-service/overview.md) e [Batch](../../batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Qual é a diferença entre o Azure Container Service e o Motor de ACS? 
O Azure Container Service é um serviço do Azure apoiado pelo SLA com funcionalidades no portal do Azure, ferramentas da linha de comandos do Azure e APIs do Azure. O serviço permite-lhe implementar e gerir rapidamente clusters que executam ferramentas de orquestração do contentor padrão com um número relativamente pequeno de opções de configuração. 

O [Motor de ACS](https://github.com/Azure/acs-engine) é um projeto de código aberto que permite aos utilizadores avançados personalizarem a configuração de cluster a todos os níveis. Esta capacidade de alterar a configuração da infraestrutura e do software significa que não oferecemos SLA para o Motor de ACS. O suporte é processado através do projeto de código aberto no GitHub, em vez de através de canais oficiais da Microsoft. 

Para obter mais detalhes, veja a nossa [política de suporte para contentores](https://support.microsoft.com/en-us/help/4035670/support-policy-for-containers).

## <a name="cluster-management"></a>Gestão de clusters

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Como criar chaves SSH para o meu cluster?

Pode utilizar as ferramentas padrão no seu sistema operativo para criar um par de chaves SSH RSA público e privado para autenticação relativamente às máquinas virtuais Linux do cluster. Para obter os passos, consulte a documentação de orientação [SO X e Linux](../../virtual-machines/linux/mac-create-ssh-keys.md) ou [Windows](../../virtual-machines/linux/ssh-from-windows.md). 

Se você usar comandos de CLI do Azure para implantar um cluster do serviço de contêiner, as chaves SSH poderão ser geradas automaticamente para o cluster.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Como posso criar um serviço principal para o meu cluster de Kubernetes?

Também são necessários uma palavra-passe e o ID principal de serviço do Azure Active Directory para criar um cluster de Kubernetes no Azure Container Service. Para obter mais informações, veja [About the service principal for a Kubernetes cluster (Sobre o principal de serviço para um cluster de Kubernetes)](../../container-service/kubernetes/container-service-kubernetes-service-principal.md).

Se utilizar os [comandos do CLI do Azure](../../container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) para implementar um cluster de Kubernetes, as credenciais do principal de serviço podem ser geradas automaticamente para o cluster.

### <a name="how-large-a-cluster-can-i-create"></a>Que tamanho de cluster posso criar?
Pode criar clusters com um, três ou cinco nós principais. Pode escolher até cem nós de agente.

> [!IMPORTANT]
> Relativamente a clusters maiores e consoante o tamanho da VM que escolher para os nós, poderá ter de aumentar a quota de núcleos da sua subscrição. Para pedir um aumento de quota, abra um [pedido de suporte ao cliente online](../../azure-portal/supportability/how-to-create-azure-support-request.md), sem custos. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Como posso aumentar o número de modelos de estrutura mestres depois de ser criado um cluster? 
Assim que o cluster for criado, o número de modelos de estrutura mestres é fixo e não pode ser alterado. Durante a criação do cluster, idealmente deve selecionar múltiplos modelos de estrutura mestres para elevada disponibilidade.

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Como posso aumentar o número de agentes depois de ser criado um cluster? 
Pode ampliar o número de agentes no cluster com o portal do Azure ou ferramentas da linha de comandos. Consulte [Dimensionar um cluster do Azure Container Service](../../container-service/kubernetes/container-service-scale.md).

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Quais são os URLs dos modelos de estrutura mestres e agentes? 
Os URLs dos recursos do cluster no Azure Container Service baseiam-se no prefixo do nome DNS que fornece e no nome da região do Azure que escolheu para a implementação. Por exemplo, o nome de domínio completamente qualificado (FQDN) do nó principal está neste formato:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Pode encontrar URLs frequentemente utilizados para o seu cluster no portal do Azure, o Explorador de Recursos do Azure ou outras ferramentas do Azure.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>Como posso saber qual a versão do orquestrador em execução no meu cluster?

* DC/OS: veja a [documentação do Mesosphere](https://docs.mesosphere.com/1.7/usage/cli/command-reference/)
* Docker Swarm: executar `docker version`
* Kubernetes: executar `kubectl version`

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>Como posso atualizar o orquestrador após a implementação?

Atualmente, o Azure Container Service não fornece ferramentas para atualizar a versão do orquestrador implementada num cluster. Se o Container Service suportar uma versão posterior, pode implementar um novo cluster. Outra opção consiste em utilizar ferramentas específicas do orquestrador, se estiverem disponíveis para atualizar um cluster no local. Por exemplo, veja [Atualizar DC/OS](http://docs.mesosphere.com/1.12/installing/production/upgrading).
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Onde posso encontrar a cadeia de ligação SSH do meu cluster?

Pode encontrar a cadeia de ligação no portal do Azure ou através das ferramentas da linha de comandos do Azure. 

1. No portal, navegue para o grupo de recursos da implementação do cluster.  

2. Clique em **Descrição Geral** e clique na ligação das **Implementações** em **Essenciais**. 

3. No painel **Histórico de implementação**, clique na implementação que tem um nome que começa por **microsoft-acs** seguido de uma data de implementação. Exemplo: microsoft-acs-201701310000.  

4. Na página **Resumo**, em **Saídas**, as várias ligações de cluster são fornecidas. O **SSHMaster0** fornece uma cadeia de ligação SSH ao primeiro modelo de estrutura mestre no cluster de serviço do contentor. 

Como mencionado anteriormente, também pode utilizar ferramentas do Azure para encontrar o FQDN do modelo de estrutura mestre. Efetue uma ligação SSH ao modelo de estrutura mestre através do FQDN do modelo de estrutura mestre e do nome de utilizador que especificou quando criou o cluster. Por exemplo:

```bash
ssh userName@masterFQDN –A –p 22 
```

Para mais informações, consulte [Connect to an Azure Container Service cluster (Ligar a um cluster do Azure Container Service)](../../container-service/kubernetes/container-service-connect.md).

### <a name="my-dns-name-resolution-isnt-working-on-windows-what-should-i-do"></a>A minha resolução do nome DNS não está a funcionar no Windows. O que devo fazer?

Há alguns problemas de DNS conhecidos no Windows cujas correções ainda estão sendo bloqueadas ativamente. Verifique se você está usando a versão mais atualizada do mecanismo ACS e do Windows (com [KB4074588](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4074588) e [KB4089848](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4089848) instalados) para que seu ambiente possa se beneficiar disso. Caso contrário, consulte a tabela abaixo para obter os passos de mitigação:

| Sintoma DNS | Solução  |
|-------------|-------------|
|Quando o contentor de carga de trabalho estiver instável e falhar, o espaço de nomes de rede é limpo | Volte a implementar quaisquer serviços afetados |
| Acesso de VIP do serviço encontra-se interrompido | Configure um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) para efetuar sempre uma execução pod normal (não privilegiada) |
|Quando o nó do contentor que está a ser executado fica indisponível, as consultas DNS podem falhar, resultando na "entrada de cache negativa" | Execute os seguintes contentores afetados no interior: <ul><li> `New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxCacheTtl -Value 0 -Type DWord`</li><li>`New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxNegativeCacheTtl -Value 0 -Type DWord`</li><li>`Restart-Service dnscache` </li></ul><br> Se isto não resolver o problema, então, tente desativar completamente a colocação em cache de DNS: <ul><li>`Set-Service dnscache -StartupType disabled`</li><li>`Stop-Service dnscache`</li></ul> |

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais](../../container-service/kubernetes/container-service-intro-kubernetes.md) sobre o Azure Container Service.
* Implementar um cluster do serviço de contentor através do [portal](../../container-service/dcos-swarm/container-service-deployment.md) ou do [ CLI do Azure](../../container-service/dcos-swarm/container-service-create-acs-cluster-cli.md).

