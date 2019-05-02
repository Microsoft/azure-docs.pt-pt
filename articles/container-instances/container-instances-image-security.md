---
title: Considerações de segurança do Azure Container Instances
description: Recomendações para proteger os imagens e segredos para o Azure Container Instances e considerações de segurança geral para qualquer plataforma de contentores
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: dc516277d79e37500e73e1aee6b88c908acb9b1c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64944000"
---
# <a name="security-considerations-for-azure-container-instances"></a>Considerações de segurança do Azure Container Instances

Este artigo apresenta considerações de segurança para utilizar o Azure Container Instances para executar aplicações de contentor. Os tópicos incluem:

> [!div class="checklist"]
> * **Recomendações de segurança** para o gerenciamento de imagens e os segredos do Azure Container Instances
> * **Considerações para o ecossistema de contentor** em todo o ciclo de vida do contentor, para qualquer plataforma de contentor

## <a name="security-recommendations-for-azure-container-instances"></a>Recomendações de segurança do Azure Container Instances

### <a name="use-a-private-registry"></a>Utilizar um registo privado

Os contentores são criados a partir de imagens que estão armazenadas num ou mais repositórios. Estes repositórios podem pertencer a um registo público, como [Docker Hub](https://hub.docker.com), ou para um registo privado. Um exemplo de um registo privado é o [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), que pode ser instalado no local ou numa cloud privada virtual. Também pode utilizar os serviços de registo de contentores privados baseados na cloud, incluindo [Azure Container Registry](../container-registry/container-registry-intro.md). 

Uma imagem de contentor publicamente disponível não garante a segurança. Imagens de contentor consistem em várias camadas de software, e cada camada de software pode ter vulnerabilidades. Para ajudar a reduzir a ameaça de ataques, deve armazenar e recuperar imagens de um registo privado, como o Azure Container Registry ou o Docker Trusted Registry. Além de fornecer um registo privado gerido, o Azure Container Registry suporta [autenticação baseada em principais de serviço](../container-registry/container-registry-authentication.md) através do Azure Active Directory para fluxos de autenticação básica. Esta autenticação inclui acesso baseado em funções para (solicitação) só de leitura, escrita (push) e permissões de proprietário.

### <a name="monitor-and-scan-container-images"></a>Monitorizar e analisar imagens de contentores

Monitorização de segurança e soluções de análise, como [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) e [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) estão disponíveis através do Azure Marketplace. Pode usá-los para analisar imagens de contentor num registo privado e identificar potenciais vulnerabilidades. É importante compreender a profundidade das análises que oferecem as soluções diferentes. 

### <a name="protect-credentials"></a>Proteger credenciais

Contentores podem distribuídos por vários clusters e regiões do Azure. Por isso, tem de proteger as credenciais necessárias para inícios de sessão ou de acesso à API, como palavras-passe ou tokens. Certifique-se de que apenas os utilizadores com privilégios podem aceder esses contentores em trânsito e em inatividade. Inventariar todos os segredos de credenciais e, em seguida, exigem que os desenvolvedores a utilizar ferramentas de gestão de segredos emergentes que foram concebidas para plataformas de contentores.  Certifique-se de que a sua solução inclui bases de dados encriptados, encriptação de TLS para os dados de segredos em trânsito e de menor privilégio [controlo de acesso baseado em funções](../role-based-access-control/overview.md). [O Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) é um serviço cloud que salvaguarda as chaves de encriptação e os segredos (tal como certificados, cadeias de ligação e palavras-passe) para aplicações em contentores. Uma vez que estes dados são sensíveis e crítico para a empresa, o acesso seguro à sua chave de cofres para que apenas aplicativos autorizados e os utilizadores podem aceder aos mesmos.

## <a name="considerations-for-the-container-ecosystem"></a>Considerações para o ecossistema de contentor

As seguintes medidas de segurança, bem implementados e geridos de forma eficaz, podem ajudar a proteger e proteger o seu ecossistema de contentor. Estas medidas aplicam-se em todo o ciclo de vida do contentor, desde o desenvolvimento por meio da implantação de produção e a uma variedade de plataformas, anfitriões e orquestradores de contentor. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Utilize a gestão de vulnerabilidade como parte do seu ciclo de vida do desenvolvimento de contentor 

Ao utilizar a gestão de vulnerabilidades em vigor em todo o ciclo de vida do desenvolvimento de contentores, aumentar as probabilidades que identificar e resolver questões de segurança antes que se tornem um problema mais sério. 

### <a name="scan-for-vulnerabilities"></a>Verificar a existência de vulnerabilidades 

Novas vulnerabilidades são detetadas o tempo todo, pelo que a análise de e identificação de vulnerabilidades são um processo contínuo. Incorpore análise em todo o ciclo de vida do contentor de vulnerabilidades:

* Como uma verificação final no seu pipeline de desenvolvimento, deve realizar uma análise de vulnerabilidade em contentores antes de enviar imagens para um registo público ou privado. 
* Continue a analisar imagens de contentores no Registro para identificar quaisquer falhas de alguma forma existância durante o desenvolvimento e para resolver quaisquer vulnerabilidades detetadas recentemente que podem ocorrer no código usado nas imagens do contentor.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Vulnerabilidades de imagem do mapa para contentores em execução 

Tem de ter um meio de vulnerabilidades de mapeamento identificados em imagens de contentor para a execução de contentores, para que os problemas de segurança podem ser atenuados ou resolvidos.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Certifique-se de que apenas as aprovadas imagens são utilizadas no seu ambiente 

Existe suficiente alteração e volatilidade num ecossistema de contentor sem permitir também a contentores desconhecida. Permitir que as imagens de contentor aprovados apenas. Ter processos e ferramentas para monitorizar e impedir a utilização de imagens de contentor não aprovadas. 

É uma maneira eficiente de reduzir a superfície de ataque e ao impedir que os desenvolvedores erros críticos de segurança controlar o fluxo de imagens de contentor no seu ambiente de desenvolvimento. Por exemplo, pode aprovar uma única distribuição do Linux como uma imagem base, preferencialmente, um que seja enxuto (Alpine ou CoreOS em vez de Ubuntu), para minimizar a superfície de potenciais ataques. 

Imagem de assinatura ou impressão digital pode fornecer uma cadeia de custódia que permite-lhe verificar a integridade dos contentores. Por exemplo, o Azure Container Registry suporta do Docker [confiança de conteúdo](https://docs.docker.com/engine/security/trust/content_trust) modelar, que permite que os publicadores de imagem assinar as imagens que são enviadas para um registo e os consumidores de imagem para extrair apenas assinado imagens.

### <a name="permit-only-approved-registries"></a>Permitir apenas os registos aprovados 

É uma extensão de garantir que o seu ambiente usa apenas as imagens aprovadas permitir apenas a utilização de registos de contentores aprovados. Exigir a utilização de registos de contentores aprovados reduz a exposição a risco ao limitar o potencial para a introdução de vulnerabilidades desconhecidas ou problemas de segurança. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Certifique-se a integridade de imagens em todo o ciclo de vida 

Parte da gestão de segurança em todo o ciclo de vida do contentor é para assegurar a integridade das imagens de contentor no Registro e como eles são alterados ou implementados em produção. 

* Imagens com vulnerabilidades, mesmo que pequenas, não devem ser permitidas a execução num ambiente de produção. Idealmente, todas as imagens implementadas em produção devem ser salvos num registo privado acessível para algumas pessoas. Mantenha o número de imagens de produção de pequeno e certifique-se de que eles podem ser geridos eficientemente.

* Porque é difícil identificar a origem do software a partir de uma imagem de contentor publicamente disponível, crie imagens de origem para garantir que os dados de conhecimento da origem da camada. Quando surge uma vulnerabilidade numa imagem de contentor criada automaticamente, os clientes podem encontrar um caminho mais rápido para a resolução. Com uma imagem pública, os clientes têm de determinar a raiz de uma imagem pública para corrigi-lo ou obter outra imagem segura a partir do publicador. 

* Não é garantida que uma imagem analisada exaustivamente e implementada em produção ser atualizado para o tempo de vida do aplicativo. Podem ser reportadas vulnerabilidades de segurança para camadas da imagem que não eram conhecidas anteriormente ou que foram introduzidas após a implementação em produção. 

  Realize auditorias periódicas imagens implementadas em produção para identificar as imagens que estão Desatualizadas ou não tem sido atualizadas há algum tempo. Poderá utilizar metodologias de implementação "blue-Green" e sem interrupção mecanismos de atualização para atualizar imagens de contentores sem tempo de inatividade. Pode examinar imagens com as ferramentas descritas na secção anterior. 

* Utilize um pipeline de integração contínua (CI) com análise para criar imagens seguras e emiti-las para o seu registo privado de segurança integrada. A análise de vulnerabilidade incorporada na solução de CI assegura que as imagens que passam em todos os testes são enviadas para o registo privado a partir do qual as cargas de trabalho em produção são implementadas. 

  Pipeline de CI garante que as imagens vulneráveis não são enviadas para o registo privado que é utilizado para implementações de carga de trabalho de produção. Também automatiza a análise se houver um número significativo de imagens de segurança de imagem. Caso contrário, auditar manualmente as imagens relativamente a vulnerabilidades pode ser extremamente moroso e suscetível a erros. 

### <a name="enforce-least-privileges-in-runtime"></a>Impor o mínimo de privilégios no tempo de execução 

O conceito de menos privilégios é uma prática recomendada de básicos de segurança que também se aplica aos contentores. Quando uma vulnerabilidade é explorada, em geral, dá-o acesso do invasor e privilégios igual da aplicação comprometida ou processo. Garantir que contentores funcionam com os privilégios mais baixos e acesso necessário para concluir o trabalho reduz a exposição a risco. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Reduzir a superfície de ataque de contentor através da remoção de privilégios desnecessários 

Também pode minimizar a potencial superfície de ataque ao remover quaisquer processos não utilizados e desnecessários ou privilégios de tempo de execução do contentor. Executam contentores com privilégios como raiz. Se um utilizador mal intencionado ou carga de trabalho escapes num contentor com privilégios, o contentor, em seguida, executará como raiz nesse sistema.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Ficheiros de lista de permissões e executáveis que o contentor está autorizado a aceder ou executar 

Reduzindo o número de variáveis ou incertezas ajuda-o a manter um ambiente estável e confiável. Limitação de contentores, para que possam aceder ou executar apenas pré-aprovados ou na lista de permissões ficheiros e executáveis é um método comprovado de limitar a exposição ao risco.  

É muito mais fácil gerir uma lista de permissões quando ele é implementado desde o início. Uma lista de permissões fornece uma medida de controle e capacidade de gestão à medida que saiba quais arquivos e executáveis são necessários para o aplicativo funcione corretamente. 

Uma lista de permissões não só reduz a superfície de ataque, mas também pode fornecer uma linha de base para anomalias e impedir que os casos de utilização dos cenários de abertas de contentor e de "vizinho ruidoso". 

### <a name="enforce-network-segmentation-on-running-containers"></a>Impor a segmentação de rede em contentores em execução  

Para ajudar a proteger contentores numa sub-rede de riscos de segurança em outra sub-rede, manter a segmentação de rede (ou segmentação de nano) ou segregação entre contentores em execução. Manutenção de segmentação de rede também pode ser necessária para utilizar contentores em setores que são necessários para atender a conformidade com as.  

Por exemplo, a ferramenta do parceiro [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) fornece uma abordagem automatizada para segmentação de nano. Aqua monitoriza as atividades de rede do contentor no tempo de execução. Ele identifica todas as ligações de rede de entrada e saída de/para outros contentores, serviços, endereços IP e da internet pública. Segmentação de nano é criada automaticamente com base no tráfego monitorizado. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorizar o acesso de utilizador e a atividade do contentor 

Tal como acontece com qualquer ambiente de TI, consistentemente, deve monitorizar atividade e o utilizador o acesso ao seu ecossistema de contentor para identificar rapidamente qualquer atividade suspeita ou maliciosa. O Azure fornece soluções, incluindo de monitorização do contentor:

* [Monitor do Azure para contentores](../azure-monitor/insights/container-insights-overview.md) para monitorizar o desempenho das cargas de trabalho implementadas nos ambientes do Kubernetes alojados no Azure Kubernetes Service (AKS). Monitor do Azure para contentores dá-lhe visibilidade de desempenho por memória de coleta e métricas de processador de controladores, nós e contentores que estão disponíveis no Kubernetes por meio da API de métricas. 

* O [solução de monitorização de contentores do Azure](../azure-monitor/insights/containers.md) ajuda-o ver e gerir outros Docker e o Windows anfitriões de contentor numa única localização. Por exemplo:

  * Ver informações detalhadas de auditoria que mostra os comandos utilizados com contentores. 
  * Resolver problemas de contentores ao visualizar e procurar registos centralizados sem ter de ver remotamente os anfitriões de Docker ou do Windows.  
  * Encontre os contentores que podem ser desnecessárias e consumindo recursos em excesso num anfitrião.
  * Ver centralizada de CPU, memória, armazenamento e informações de utilização e desempenho de rede para contentores.  

  A solução suporta orquestradores de contentor, incluindo Docker Swarm, DC/OS, Kubernetes, Service Fabric e não Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorizar a atividade de recursos de contentor 

Monitorize a atividade de recursos, como arquivos, rede e outros recursos que acedem a seus contentores. A monitorização de atividade de recursos e o consumo é útil para monitorizar o desempenho e uma medida de segurança. 

[O Azure Monitor](../azure-monitor/overview.md) permite a monitorização dos componentes essenciais para serviços do Azure, permitindo que a coleção de métricas, registos de atividades e os registos de diagnóstico. Por exemplo, o registo de atividade indica quando são criados ou modificados os novos recursos. 

Estão disponíveis métricas que fornecem estatísticas de desempenho de recursos diferentes e, mesmo, do sistema operativo no interior de uma máquina virtual. Pode ver estes dados com um dos exploradores no portal do Azure e criar alertas com base nestas métricas. O Azure Monitor fornece que as métricas mais rápidas do pipeline (5 minutos até 1 minuto), para que deve usá-lo para notificações e alertas críticos de tempo. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Todos os acessos de utilizador administrativo de contentor para auditoria de registo 

Manter uma trilha de auditoria precisa de acesso administrativo para o seu ecossistema de contentor, o registo de contentor e a imagens de contentor. Estes registos, poderão ser necessários para fins de auditoria e serão útil como evidências forenses após qualquer incidente de segurança. Pode utilizar o [solução de monitorização de contentores do Azure](../azure-monitor/insights/containers.md) para atingir esse objetivo. 

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a gestão de vulnerabilidades de contentor com soluções a partir [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) e [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).

* Saiba mais sobre [segurança do contentor no Azure](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).