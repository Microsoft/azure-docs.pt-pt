---
title: Como a Cloud Foundry se integra com a Azure | Microsoft Docs
description: Descreve como a Cloud Foundry pode usar os serviços Azure para melhorar a experiência da Enterprise
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: ff2a6618b60ff2cfa5faa74c905e140466a14359
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563324"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrar o Cloud Foundry com o Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) é uma plataforma PaaS que funciona no topo da plataforma IaaS dos fornecedores de nuvem. Oferece uma experiência consistente de implementação de aplicações entre fornecedores de nuvem. Pode ainda integrar-se com vários serviços Azure, com HA de grau empresarial, escalabilidade e economia de custos.
Existem [6 subsistemas de Cloud Foundry,](https://docs.cloudfoundry.org/concepts/architecture/)que podem ser de forma flexível online, incluindo: Encaminhamento, Autenticação, Gestão do ciclo de vida da aplicação, gestão de serviços, Mensagens e Monitorização. Para cada um dos subsistemas, pode configurar cloud foundry para usar o serviço Azure correspondente. 

![Cloud Foundry na Arquitetura de Integração Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Alta Disponibilidade e Escalabilidade
### <a name="managed-disk"></a>Disco Gerido
Bosh usa CPI Azure (Cloud Provider Interface) para criar e eliminar rotinas de discos. Por predefinição, são utilizados discos não geridos. Requer que o cliente crie manualmente contas de armazenamento e, em seguida, configuure as contas em ficheiros manifestos CF. Isto deve-se à limitação do número de discos por conta de armazenamento.
Agora [o Disco Gerido](https://azure.microsoft.com/services/managed-disks/) está disponível, oferece armazenamento de disco seguro e fiável gerido para máquinas virtuais. O cliente já não precisa de lidar com a conta de armazenamento para escala e HA. A azure organiza os discos automaticamente. Quer se trate de uma implantação nova ou existente, o CPI Azure tratará da criação ou migração do disco gerido durante uma implementação de CF. É suportado com PCF 1.11. Também pode explorar a [orientação](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) do disco gerido pela Cloud Foundry para referência. 
### <a name="availability-zone-"></a>Zona de Disponibilidade *
Como uma plataforma de aplicação nativa em nuvem, cloud Foundry é projetado com [quatro níveis de alta disponibilidade](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Embora os três primeiros níveis de falhas de software possam ser manuseados pelo próprio sistema CF, a tolerância à falha da plataforma é fornecida pelos fornecedores de nuvem. Os principais componentes CF devem ser protegidos com a solução HA da plataforma ha de um fornecedor de nuvem. Isto inclui GoRouters, Diego Brains, base de dados CF e azulejos de serviço. Por predefinição, [o Conjunto de Disponibilidade de Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) é utilizado para a tolerância a falhas entre clusters num centro de dados.
A boa novidade é que [a Azure Availability Zone](../availability-zones/az-overview.md) é lançada agora, elevando a tolerância à falha para o próximo nível, com baixa redundância de latência em todos os centros de dados.
A zona de disponibilidade de Azure alcança HA colocando um conjunto de VMs em 2+ centros de dados, cada conjunto de VMs são redundantes para outros conjuntos. Se uma zona está em baixo, os outros conjuntos ainda estão vivos, isolados do desastre.
> [!NOTE] 
> A Zona de Disponibilidade azul ainda não é oferecida a todas as regiões, verifique o último [anúncio para a lista de regiões apoiadas.](../availability-zones/az-overview.md) Para a Fundição de Nuvem de Código Aberto, consulte [a Zona de Disponibilidade de Azure para obter orientações sobre a fundição da nuvem](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone)de código aberto .

## <a name="2-network-routing"></a>2. Encaminhamento de rede
Por padrão, o Azure basic load balancer é usado para os pedidos de API/apps CF, reencando-os para os Gorouters. Componentes CF como Diego Brain, MySQL, ERT também podem usar o equilibrador de carga para equilibrar o tráfego para HA. O Azure também fornece um conjunto de soluções totalmente geridas de equilíbrio de carga. Se estiver à procura de rescisão TLS/SSL ("offload SSL") ou por HTTP/HTTPS solicite processamento de camadas de aplicação, considere o Gateway de aplicação. Para uma elevada disponibilidade e carga de escala equilibrada na camada 4, considere o balanceador de carga padrão.
### <a name="azure-application-gateway-"></a>Gateway de aplicação Azure *
[O Azure Application Gateway](../application-gateway/overview.md) oferece várias capacidades de equilíbrio de carga de camada 7, incluindo descarregamento SSL, fim a fim TLS, Firewall de aplicação web, afinidade de sessão baseada em cookies e muito mais. Pode [configurar o Gateway de aplicações em Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). Para pcf, verifique as  [notas de lançamento do PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) para o teste POC.

### <a name="azure-standard-load-balancer-"></a>Balançador de carga padrão Azure *
Azure Load Balancer é um equilibrador de carga camada 4. É usado para distribuir o tráfego entre casos de serviços em um conjunto equilibrado de carga. A versão padrão fornece [funcionalidades avançadas](../load-balancer/load-balancer-overview.md) em cima da versão básica. Por exemplo, 1. O limite máximo da piscina de backend é aumentado de 100 para 1000 VMs.  2. Os pontos finais suportam agora vários conjuntos de disponibilidade em vez de um conjunto de disponibilidade única.  3. Recursos adicionais como portas HA, dados de monitorização mais ricos, e assim por diante. Se estiver a mudar-se para a Zona de Disponibilidade Azure, é necessário um balanceador de carga padrão. Para uma nova implementação, recomendamos que comece com o Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. Autenticação 
[Cloud Foundry User Account and Authentication](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) é o serviço central de gestão de identidade para CF e seus vários componentes. [O Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) é o serviço de gestão de identidade e diretório multi-inquilino da Microsoft. Por padrão, uAA é usado para autenticação cloud foundry. Como uma opção avançada, a UAA também suporta o Azure AD como uma loja de utilizadores externa. Os utilizadores de Azure AD podem aceder à Cloud Foundry usando a sua identidade LDAP, sem uma conta Cloud Foundry. Siga estes passos para [configurar o AD AZure para AAA em PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Armazenamento de dados para cloud foundry runtime system
A Cloud Foundry oferece uma grande extensibilidade para usar serviços de blobstore Azure ou Azure MySQL/PostgreSQL para armazenamento de sistema de execução de aplicações.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure Blobstore para cloud foundry cloud controller blobstore
A blobstore Cloud Controller é uma loja de dados crítica para pacotes, gotículas, pacotes e piscinas de recursos. Por predefinição, o servidor NFS é utilizado para a blobstore do Controlador de Nuvem. Para evitar um único ponto de avaria, utilize o Azure Blob Storage como loja externa. Consulte a [documentação](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) cloud foundry para obter antecedentes e [opções em Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL como Base de Dados de Tempo de Execução Elástica de Formação de Nuvens *
O tempo de execução elástico CF requer duas bases de dados principais do sistema:
#### <a name="ccdb"></a>CCDB 
A base de dados do Controlador de Nuvens.  O Cloud Controller fornece pontos finais rest API para os clientes acederem ao sistema. O CCDB armazena tabelas para orgs, espaços, serviços, funções de utilizador e muito mais para o controlador Cloud.
#### <a name="uaadb"></a>UAADB 
A base de dados de Conta de Utilizador e Autenticação. Armazena os dados relacionados com a autenticação do utilizador, por exemplo, nomes de utilizador encriptados e palavras-passe.

Por predefinição, pode ser utilizada uma base de dados do sistema local (MySQL). Para HA e para escala, a alavancagem Azure geriu os serviços MySQL ou PostgreSQL. Aqui está a instrução de permitir a [Azure MySQL/PostgreSQL para CCDB, UAADB e outras bases de dados do sistema com Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Corretor de Serviço Aberto
O corretor de serviços Azure oferece uma interface consistente para gerir o acesso da aplicação aos serviços Azure. O novo [projeto Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure) oferece uma forma simples e simples de fornecer serviços a aplicações em Cloud Foundry, OpenShift e Kubernetes. Consulte o [Azure Open Service Broker para o azulejo pcf](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) para obter instruções de implementação no PCF.

## <a name="6-metrics-and-logging"></a>6. Métricas e Registos
O Azure Log Analytics Nozzle é um componente cloud foundry, que encaminha métricas da mangueira de fogo do [loggregator cloud foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) para [os registos do Monitor Azure](https://azure.microsoft.com/services/log-analytics/). Com o Bocal, pode recolher, visualizar e analisar as métricas de saúde e desempenho do seu sistema CF em várias implementações.
Clique [aqui](./cloudfoundry-oms-nozzle.md) para aprender a implementar o Azure Log Analytics Nozzle para o ambiente Open Source e Pivotal Cloud Foundry e, em seguida, aceda aos dados a partir da consola de registos Azure Monitor. 
> [!NOTE]
> A partir de PCF 2.0, as métricas de saúde BOSH para VMs são encaminhadas para o Loggregator Firehose por padrão, e são integradas na consola de registos Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Economia de Custos
### <a name="cost-saving-for-devtest-environments"></a>Economia de custos para ambientes de dev/teste
#### <a name="b-series-"></a>Série B: *
Embora as séries F e D VM tenham sido comumente recomendadas para o ambiente de produção da Pivotal Cloud Foundry, a nova [série B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) "rebentavel" traz novas opções. Os VMs rebentados da série B são ideais para cargas de trabalho que não necessitam de um desempenho completo do CPU continuamente, como servidores web, pequenas bases de dados e ambientes de desenvolvimento e teste. Estas cargas de trabalho normalmente têm requisitos de desempenho rebeníveis. São $0.012/hora (B1) em comparação com $0,05/hora (F1), consulte a lista completa de tamanhos de [VM](../virtual-machines/sizes-general.md) e [preços](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) para detalhes. 
#### <a name="managed-standard-disk"></a>Disco Padrão gerido: 
Foram recomendados discos premium para um desempenho fiável na produção.  Com [o Disco Gerido,](https://azure.microsoft.com/services/managed-disks/)o armazenamento padrão também pode oferecer uma fiabilidade semelhante, com desempenho diferente. Para uma carga de trabalho que não seja sensível ao desempenho, como dev/Teste ou ambiente não crítico, os discos standard geridos oferecem uma opção alternativa com menor custo.  
### <a name="cost-saving-in-general"></a>Economia de custos em Geral 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Economia significativa de custos da VM com reservas Azure: 
Hoje em dia, todos os VM cf são cobrados usando preços "on-demand", mesmo que os ambientes normalmente permaneçam acordados indefinidamente. Agora pode reservar capacidade VM a um prazo de 1 ou 3 anos, e obter descontos de 45-65%. Os descontos são aplicados no sistema de faturação, sem alterações no seu ambiente. Para mais detalhes, consulte [como funcionam as reservas do Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/) 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Disco Premium gerido com tamanhos menores: 
Os discos geridos suportam tamanhos de disco mais pequenos, por exemplo P4 (32 GB) e P6(64 GB) tanto para discos premium como para discos standard. Se tiver pequenas cargas de trabalho, pode economizar custos ao migrar de discos premium padrão para discos premium geridos.
#### <a name="use-azure-first-party-services"></a>Utilize os serviços de primeira parte da Azure: 
Tirar partido do primeiro serviço partidário da Azure reduzirá o custo administrativo a longo prazo, além da HA e da fiabilidade mencionada nas secções acima. 

A Pivotal lançou um [ERT de Pequena Pegada](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) para clientes pcf, os componentes são co-localizados em apenas 4 VMs, com até 2500 instâncias de aplicação. A versão experimental está agora disponível através do [Azure Market place](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Passos Seguintes
As funcionalidades de integração do Azure estão disponíveis pela primeira vez com [o Open Source Cloud Foundry,](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/)antes de estar disponível no Pivotal Cloud Foundry. As funcionalidades marcadas com * ainda não estão disponíveis através do PCF. A integração da Cloud Foundry com a Azure Stack também não está abrangida por este documento.
Para o suporte do PCF nas funcionalidades marcadas com a integração *, ou Cloud Foundry com o Azure Stack, contacte o seu gestor de conta Pivotal e Microsoft para obter o estado mais recente.