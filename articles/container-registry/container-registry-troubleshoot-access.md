---
title: Problemas de rede de resolução de problemas com registo
description: Sintomas, causas e resolução de problemas comuns ao aceder a um registo de contentores Azure numa rede virtual ou atrás de uma firewall
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: f84b11418344bfeaf790377c1d8644fbc7d7d636
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743374"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Problemas de rede de resolução de problemas com registo

Este artigo ajuda-o a resolver problemas que pode encontrar ao aceder a um registo de contentores Azure numa rede virtual ou atrás de uma firewall. 

## <a name="symptoms"></a>Sintomas

Pode incluir um ou mais dos seguintes:

* Incapaz de empurrar ou puxar imagens e recebe erro `dial tcp: lookup myregistry.azurecr.io`
* Incapaz de empurrar ou puxar imagens e recebe erro do Azure CLI `Could not connect to the registry login server`
* Não é possível retirar imagens do registo ao Serviço Azure Kubernetes ou a outro serviço Azure
* Não consegue aceder a um registo por trás de um representante https e recebe erro `Error response from daemon: login attempt failed with status: 403 Forbidden`
* Não é possível configurar as definições de rede virtual e recebe erro `Failed to save firewall and virtual network settings for container registry`
* Não pode aceder ou visualizar as definições de registo no portal Azure ou gerir o registo utilizando o CLI Azure
* Não é possível adicionar ou modificar configurações de rede virtuais ou regras de acesso público
* As tarefas ACR são incapazes de empurrar ou puxar imagens
* O Centro de Segurança Azure não pode digitalizar imagens no registo, ou os resultados da varredura não aparecem no Centro de Segurança Azure

## <a name="causes"></a>Causas

* Uma firewall de cliente ou procuração impede o acesso - [solução](#configure-client-firewall-access)
* Regras públicas de acesso à rede no registo impedem o acesso - [solução](#configure-public-access-to-registry)
* A configuração da rede virtual impede o acesso - [solução](#configure-vnet-access)
* Você tenta integrar o Azure Security Center ou certos outros serviços Azure com um registo que tem um ponto final privado, ponto final de serviço ou regras públicas de acesso IP - [solução](#configure-service-access)

## <a name="further-diagnosis"></a>Diagnóstico adicional 

Faça o comando [de check-health az acr](/cli/azure/acr#az-acr-check-health) para obter mais informações sobre a saúde do ambiente de registo e acesso opcional a um registo-alvo. Por exemplo, diagnosticar certos problemas de conectividade ou configuração da rede. 

Consulte [a saúde de um registo de contentores Azure](container-registry-check-health.md) para obter exemplos de comando. Se forem reportados erros, reveja a [referência de erro](container-registry-health-error-reference.md) e as seguintes secções para obter soluções recomendadas.

> [!NOTE]
> Alguns sintomas de conectividade da rede também podem ocorrer quando há problemas com autenticação ou autorização de registo. Consulte [o registo de resolução de problemas](container-registry-troubleshoot-login.md).

## <a name="potential-solutions"></a>Possíveis soluções

### <a name="configure-client-firewall-access"></a>Configure o acesso à firewall do cliente

Para aceder a um registo por trás de uma firewall do cliente ou servidor de procuração, configure as regras de firewall para aceder ao REST público do registo e pontos finais de dados. Se os [pontos finais de dados dedicados](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) estiverem ativados, precisa de regras para aceder:

* Ponto final DO REST: `<registryname>.azurecr.io`
* Ponto final de dados: `<registry-name>.<region>.data.azurecr.io`

Para um registo geo-replicado, configuure o acesso ao ponto final de dados para cada réplica regional.

Por trás de um representante https, certifique-se de que tanto o seu cliente Docker como o Daemon Docker estão configurados para comportamento de procuração.

Os registos de recursos registados na tabela ContainerRegistryLoginEvents podem ajudar a diagnosticar uma tentativa de ligação que está bloqueada.

Links relacionados:

* [Configure regras para aceder a um registo de contentores Azure atrás de uma firewall](container-registry-firewall-access-rules.md)
* [CONFIGURAÇÃO DE PROCURAÇÃO HTTP/HTTPS](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Geo-replicação no Registo do Contentor de Azure](container-registry-geo-replication.md)
* [Registo de registo de contentores Azure para avaliação e auditoria de diagnóstico](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>Configure o acesso público ao registo

Se aceder a um registo através da internet, confirme que o registo permite o acesso à rede pública a partir do seu cliente. Por predefinição, um registo de contentores Azure permite o acesso aos pontos finais do registo público de todas as redes. Um registo pode limitar o acesso a redes selecionadas ou endereços IP selecionados. 

Se o registo estiver configurado para uma rede virtual com um ponto final de serviço, o acesso à rede pública desativa também desativa o acesso ao ponto final do serviço. Se o seu registo estiver configurado para uma rede virtual com Private Link, as regras de rede IP não se aplicam aos pontos finais privados do registo. 

Links relacionados:

* [Configurar regras públicas de rede IP](container-registry-access-selected-networks.md)
* [Conecte-se privadamente a um registo de contentores Azure usando a Azure Private Link](container-registry-private-link.md)
* [Restringir o acesso a um registo de contentores utilizando um ponto final de serviço numa rede virtual Azure](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Configure acesso VNet

Confirme que a rede virtual está configurada com um ponto final privado para Private Link ou um ponto final de serviço (pré-visualização). Atualmente, um ponto final do Azure Bastion não é suportado.

Reveja as regras e etiquetas de serviço da NSG utilizadas para limitar o tráfego de outros recursos da rede ao registo. 

Se um ponto final de serviço do registo estiver configurado, confirme que uma regra de rede é adicionada ao registo que permite o acesso a partir dessa sub-rede de rede. O ponto final de serviço suporta apenas o acesso a partir de máquinas virtuais e clusters AKS na rede.

Se pretender restringir o acesso ao registo utilizando uma rede virtual numa subscrição Azure diferente, certifique-se de que regista o `Microsoft.ContainerRegistry` fornecedor de recursos nessa subscrição. [Registe o fornecedor de recursos](../azure-resource-manager/management/resource-providers-and-types.md) do Registo de Contentores Azure utilizando o portal Azure CLI ou outras ferramentas Azure.

Se o Azure Firewall ou uma solução semelhante estiver configurado na rede, verifique se o tráfego de saída de outros recursos, como um cluster AKS, está habilitado a chegar aos pontos finais do registo.

Se um ponto final privado estiver configurado, confirme que o DNS resolve a FQDN pública do registo, tal como *myregistry.azurecr.io* ao endereço IP privado do registo. Utilize um utilitário de rede como `dig` ou para a procura de `nslookup` DNS.

Links relacionados:

* [Conecte-se privadamente a um registo de contentores Azure usando a Azure Private Link](container-registry-private-link.md)
* [Restringir o acesso a um registo de contentores utilizando um ponto final de serviço numa rede virtual Azure](container-registry-vnet.md)
* [Regras de rede de saída exigidas e FQDNs para clusters AKS](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: Resolução DNS de depurar](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Tags de serviço de rede virtual](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Configure acesso ao serviço

Atualmente, o Azure Security Center não pode realizar [a verificação de vulnerabilidade de imagem](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) num registo que restringe o acesso a pontos finais privados, sub-redes selecionadas ou endereços IP. Além disso, os recursos dos seguintes serviços não podem aceder a um registo de contentores com restrições de rede:

* Azure DevOps Services 
* Azure Container Instances
* Tarefas do Azure Container Registry

Se for necessário aceder ou integração destes serviços Azure com o seu registo de contentores, remova a restrição de rede. Por exemplo, remova os pontos finais privados do registo ou remova ou modifique as regras de acesso público do registo.

Links relacionados:

* [Digitalização da imagem do registo do contentor Azure pelo Centro de Segurança](../security-center/azure-container-registry-integration.md)
* Fornecer [feedback](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [Configurar regras públicas de rede IP](container-registry-access-selected-networks.md)
* [Conecte-se privadamente a um registo de contentores Azure usando a Azure Private Link](container-registry-private-link.md)


## <a name="advanced-troubleshooting"></a>Resolução de problemas avançados

Se a [recolha de registos de recursos](container-registry-diagnostics-audit-logs.md) estiver ativada no registo, reveja o registo De ContainterRegistryLoginEvents. Este registo armazena eventos e estado de autenticação, incluindo a identidade de entrada e endereço IP. Consultar o registo de [falhas de autenticação do registo](container-registry-diagnostics-audit-logs.md#registry-authentication-failures). 

Links relacionados:

* [Registos para avaliação e auditoria de diagnóstico](container-registry-diagnostics-audit-logs.md)
* [FaQ do registo de contentores](container-registry-faq.md)
* [Linha de Base de Segurança Azure para registo de contentores Azure](security-baseline.md)
* [Melhores práticas do Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Passos seguintes

Se não resolver o seu problema aqui, consulte as seguintes opções.

* Outros tópicos de resolução de problemas do registo incluem:
  * [Login de registo de resolução de problemas](container-registry-troubleshoot-login.md) 
  * [Resolver problemas de desempenho de registo](container-registry-troubleshoot-performance.md)
* [Opções de apoio comunitário](https://azure.microsoft.com/support/community/)
* [Perguntas e Respostas da Microsoft](https://docs.microsoft.com/answers/products/)
* [Abrir um pedido de suporte](https://azure.microsoft.com/support/create-ticket/)


