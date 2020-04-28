---
title: (DEPRECIADO) Segurança de contentores no Serviço de Contentores Azure
description: Considerações para a segurança dos contentores Docker implantados no Serviço de Contentores Azure e serviços azure relacionados.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/28/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d90d872175febf775e7d0892e133883f1a8ff8a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75552394"
---
# <a name="deprecated-securing-docker-containers-in-azure-container-service"></a>(DEPRECIADO) Proteger contentores Docker no Serviço de Contentores Azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Este artigo apresenta considerações e recomendações para proteger contentores d Docker implementados no Azure Container Service. Muitas destas considerações aplicam-se geralmente aos contentores do Docker implementados no Azure ou noutros ambientes. 

## <a name="image-security"></a>Segurança das imagens

Os contentores são criados a partir de imagens que estão armazenadas num ou mais repositórios. Estes repositórios podem pertencer a registos de contentores públicos ou privados. O[Docker Hub](https://hub.docker.com/) é um exemplo de um registo público. Um exemplo de um registo privado é o [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), que pode ser instalado no local ou numa cloud privada virtual. Também existem serviços de registo de contentores privados baseados na cloud, entre os quais o [Azure Container Registry](../../container-registry/container-registry-intro.md).

### <a name="public-and-private-images"></a>Imagens públicas e privadas
De um modo geral, tal como sucede com qualquer pacote de software publicado publicamente, as imagens de contentores disponíveis de forma pública não garantem a segurança. As imagens de contentores consistem em várias camadas de software, sendo que cada camada de software pode ter vulnerabilidades. É fundamental compreender a origem das imagens de contentores, incluindo os proprietários das mesmas (para saber se é uma origem fiável ou não), em que consistem as camadas de software e as versões do mesmo. 

Por exemplo, se aceder ao [repositório nginx](https://hub.docker.com/_/nginx/) oficial no Docker Hub e navegar para o separador **Etiquetas**, verá vulnerabilidades com códigos de cores em cada imagem. Cada cor representa a vulnerabilidade de uma camada de software da imagem. Para obter mais informações sobre a análise de vulnerabilidades no Docker Hub, veja [Understanding official repos on Docker Hub](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/) (Compreender os repositórios oficiais no Docker Hub).

![Imagens de Nginx no Docker Hub](./media/container-service-security/docker-hub-nginx.png)

As empresas preocupam-se bastante com a segurança e, para se protegerem de ataques à segurança, devem armazenar e obter imagens de registos privados, como o Azure Container Registry ou o Docker Trusted Registry. Para além de fornecer um registo privado gerido, o Azure Container Registry suporta a [autenticação baseada em principais de serviço](../../container-registry/container-registry-authentication.md) através do Azure Active Directory para fluxos de autenticação básicos, incluindo acesso baseado em funções para permissões só de leitura, escrita e proprietário.

### <a name="image-security-scanning"></a>Análise de segurança das imagens

Mesmo se utilizar um registo privado, é útil utilizar soluções de análise de imagens para confirmar a segurança. Cada camada de software numa imagem de contentor é potencialmente suscetível a vulnerabilidades, independentemente das outras camadas da imagem do contentor. À medida que cada vez mais empresas começam a implementar as cargas de trabalho de produção baseadas em tecnologias de contentores, a análise das imagens assume uma importância para garantir a prevenção de ameaças à segurança. 

As soluções de monitorização de segurança e de análise, como o [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) e [Aqua Security](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry), entre outros, podem ser utilizados para analisar imagens de contentores em registos privados e identificar potenciais vulnerabilidades. É importante compreender a profundidade das análises de cada solução. Por exemplo, algumas podem fazer apenas verificações cruzadas de camadas das imagens relativamente a vulnerabilidades conhecidas. Estas soluções poderão não conseguir verificar o software de camadas de imagens criado através de determinado software de gestor de pacotes. Outras soluções têm uma integração de análises mais profunda e podem encontrar vulnerabilidades em qualquer software de pacotes.

### <a name="production-deployment-rules-and-audit"></a>Regras de implementação e auditoria de produção
Quando uma aplicação é implementada em produção, é essencial definir algumas regras para garantir que as imagens utilizadas nos ambientes de produção são seguras e não contêm vulnerabilidades.

* Como regra, as imagens com vulnerabilidades, mesmo que pequenas, não devem ser executadas em ambientes de produção. Além disso, idealmente, todas as imagens implementadas em produção devem ser guardadas num registo privado que só deve estar acessível a algumas pessoas. Também é importante ter poucas imagens de produção, de modo a garantir que podem ser geridos eficientemente.

* Uma vez que é difícil identificar a origem do software a partir de uma imagem de contentor publicamente disponível, é uma boa prática criar imagens a partir da origem, para garantir que a origem da camada está identificada. Quando surge uma vulnerabilidade numa imagem de contentor criada automaticamente, os clientes podem encontrar um caminho mais rápido para a resolução. Nas imagens públicas, os clientes têm de encontrar a raiz das mesmas para a corrigir ou obter outra imagem segura junto do publicador.

* Não é garantido que uma imagem analisada exaustivamente e implementada em produção se mantenha atualizada durante o ciclo de vida da aplicação. Podem ser reportadas vulnerabilidades de segurança para camadas da imagem que não eram conhecidas anteriormente ou que foram introduzidas após a implementação em produção. É necessário fazer auditorias periódicas às imagens implementadas em produção, para identificar as que possam estar desatualizadas ou que não tenham sido atualizadas há algum tempo. Para atualizar as imagens de contentores sem tempo de interrupção, é possível utilizar metodologias de implementação “blue-green” e mecanismos de atualizações sem interrupções. A análise das imagens pode ser feita com as ferramentas descritas na secção anterior. 

* Um pipeline de integração contínua (CI) para criar imagens e a análise de segurança integrada podem ajudar a manter registos privados seguros com imagens de contentores seguras. A análise de vulnerabilidade incorporada na solução de CI assegura que as imagens que passam em todos os testes são enviadas para o registo privado a partir do qual as cargas de trabalho em produção são implementadas. O pipeline de CI garante que as imagens vulneráveis não são enviadas para o registo privado utilizado para as implementações de cargas de trabalho em produção. Também automatiza a análise de segurança de imagens se houver um número significativamente grande de imagens. Caso contrário, auditar manualmente as imagens relativamente a vulnerabilidades pode ser extremamente moroso e suscetível a erros.

## <a name="host-level-container-isolation"></a>Isolamento de contentores ao nível do anfitrião
Quando um cliente implementa aplicações de contentor em recursos do Azure, estas são implementadas ao nível da subscrição nos grupos de recursos e não são multi-inquilino. Isto significa que, se um cliente partilhar uma subscrição com outras pessoas, não podem ser criados limites entre duas implementações na mesma subscrição. Por conseguinte, a segurança ao nível do contentor. não é garantida. 

Também é muito importante compreender que os contentores partilham o kernel e os recursos do anfitrião (que, no Azure Container Service, são VMs do Azure num cluster). Por este motivo, os contentores em execução em produção têm de ser executados no modo de utilizador não privilegiado. Um contentor a ser executado com privilégios de raiz pode comprometer todo o ambiente. Se tiver acesso ao nível de raiz num contentor, os hackers podem obter acesso aos privilégios de raiz completos no anfitrião. Além disso, é importante executar contentores com sistemas de ficheiros só de leitura. Isto impede que uma pessoa que tenha acesso ao contentor comprometido escreva scripts maliciosos no sistema de ficheiros e obtenha acesso a outros ficheiros. Da mesma forma, é importante limitar os recursos (por exemplo, a memória, a CPU e a largura de banda de rede) alocados a um contentor. Isto ajuda a evitar que os hackers acolham recursos e prossigam atividades ilegais, como fraude de cartões de crédito ou mineração de moedas, o que poderia impedir que outros contentores funcionassem no hospedeiro ou no cluster.

## <a name="orchestrator-considerations"></a>Considerações de orquestração

Cada orquestrador disponível no Azure Container Service tem as suas próprias considerações de segurança. Por exemplo, deve limitar o acesso SSH direto aos nós do orquestrador no Container Service. Em alternativa, deve utilizar a IU ou as ferramentas de linha de comandos de cada orquestrador (como `kubectl` para Kubernetes) para gerir o ambiente de contentores sem aceder aos anfitriões.

Para obter informações adicionais sobre a segurança específica de cada orquestrador, veja os recursos seguintes:

* **Kubernetes**: [Security Best Practices for Kubernetes Deployment](https://kubernetes.io/blog/2016/08/security-best-practices-kubernetes-deployment/) (Melhores Práticas de Segurança para Implementação de Kubernetes)

* **DC/OS**: [Securing Your Cluster](https://docs.mesosphere.com/1.12/administering-clusters/securing-your-cluster) (Proteger o Seu Cluster)

* **Docker Swarm**: [Docker Security](https://www.docker.com/docker-security) (Segurança do Docker)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a arquitetura do Docker e a segurança dos contentores, veja [Introduction to Container Security](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf) (Introdução à Segurança dos Contentores).

* Para obter mais informações sobre a segurança da plataforma Azure, veja o [Centro de Segurança do Azure](https://www.microsoft.com/trustcenter/cloudservices/azure).
