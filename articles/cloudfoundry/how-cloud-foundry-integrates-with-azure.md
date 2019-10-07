---
title: Como o Cloud Foundry se integra ao Azure | Microsoft Docs
description: Descreve como Cloud Foundry pode usar os serviços do Azure para aprimorar a experiência empresarial
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: e341cc5beeb8e8362a848bb1e208ddf1dc773978
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976790"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrar o Cloud Foundry com o Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) é uma plataforma PaaS em execução na plataforma IaaS dos provedores de nuvem. Ele oferece uma experiência de implantação de aplicativo consistente entre os provedores de nuvem. Ele também pode se integrar a vários serviços do Azure, com alta disponibilidade de nível empresarial, escalabilidade e economia de custos.
Há [6 subsistemas de Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), que podem ser escalonáveis com flexibilidade online, incluindo: Roteamento, autenticação, gerenciamento do ciclo de vida do aplicativo, gerenciamento de serviços, mensagens e monitoramento. Para cada um dos subsistemas, você pode configurar Cloud Foundry para usar o serviço do Azure correspondente. 

![Cloud Foundry na arquitetura de integração do Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Alta disponibilidade e escalabilidade
### <a name="managed-disk"></a>Disco gerenciado
O Bosh usa o Azure CPI (interface do provedor de nuvem) para criar e excluir rotinas de disco. Por padrão, discos não gerenciados são usados. Ele exige que o cliente crie contas de armazenamento manualmente e, em seguida, configure as contas em arquivos de manifesto do CF. Isso ocorre devido à limitação do número de discos por conta de armazenamento.
Agora, o [disco gerenciado](https://azure.microsoft.com/services/managed-disks/) está disponível, oferece armazenamento gerenciado seguro e confiável em disco para máquinas virtuais. O cliente não precisa mais lidar com a conta de armazenamento para escala e HA. O Azure organiza os discos automaticamente. Seja uma implantação nova ou existente, o Azure CPI manipulará a criação ou a migração do disco gerenciado durante uma implantação do CF. Ele tem suporte com o PCF 1,11. Você também pode explorar as diretrizes de Cloud Foundry de [disco gerenciado](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) de software livre para referência. 
### <a name="availability-zone-"></a>Zona de disponibilidade *
Como uma plataforma de aplicativo de nuvem nativa, Cloud Foundry é projetada com [quatro níveis de alta disponibilidade](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Embora os três primeiros níveis de falhas de software possam ser tratados pelo próprio sistema do CF, a tolerância a falhas da plataforma é fornecida pelos provedores de nuvem. Os principais componentes do CF devem ser protegidos com a solução de HA da plataforma do provedor de nuvem. Isso inclui os blocos GoRouters, Diego cérebro, banco de dados CF e serviço. Por padrão, o [conjunto de disponibilidade do Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) é usado para tolerância a falhas entre clusters em um Data Center.
O bom novo é que a [zona de disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview ) é lançada agora, trazendo a tolerância a falhas para o próximo nível, com redundância de baixa latência entre data centers.
A zona de disponibilidade do Azure alcança a HA colocando um conjunto de VMs em 2 + data centers, cada conjunto de VMs é redundante para outros conjuntos. Se uma zona estiver inativa, os outros conjuntos ainda estarão em tempo real, isolados do desastre.
> [!NOTE] 
> A zona de disponibilidade do Azure ainda não é oferecida para todas as regiões, verifique o anúncio mais recente da [lista de regiões com suporte](https://docs.microsoft.com/azure/availability-zones/az-overview). Para Cloud Foundry de software livre, verifique [a área de disponibilidade do Azure para obter diretrizes de Cloud Foundry de](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone)software livre.

## <a name="2-network-routing"></a>2. Roteamento de rede
Por padrão, o balanceador de carga básico do Azure é usado para solicitações de aplicativos/API do CF de entrada, encaminhando-os para o Gorouters. Os componentes do CF, como Diego Brain, MySQL, ERT também podem usar o balanceador de carga para balancear o tráfego para HA. O Azure também fornece um conjunto de soluções de balanceamento de carga totalmente gerenciadas. Se você estiver procurando por terminação de TLS ("descarregamento SSL") ou por processamento de camada de aplicativo de solicitação HTTP/HTTPS, considere o gateway de aplicativo. Para alta disponibilidade e balanceamento de carga de escalabilidade na camada 4, considere o balanceador de carga padrão.
### <a name="azure-application-gateway-"></a>Aplicativo Azure gateway *
O [Gateway de aplicativo Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) oferece vários recursos de balanceamento de carga de camada 7, incluindo descarregamento de SSL, SSL de ponta a ponta, firewall de aplicativo Web, afinidade de sessão baseada em cookie e muito mais. Você pode [Configurar o gateway de aplicativo em Cloud Foundry de código aberto](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). Para PCF, verifique as [notas de versão de pcf 2,1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) para teste de VDC.

### <a name="azure-standard-load-balancer-"></a>Standard Load Balancer do Azure *
Azure Load Balancer é um balanceador de carga de camada 4. Ele é usado para distribuir o tráfego entre instâncias de serviços em um conjunto de balanceamento de carga. A versão padrão fornece [recursos avançados](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) sobre a versão básica. Por exemplo 1. O limite máximo do pool de back-end é gerado de 100 a 1000 VMs.  2. Os pontos de extremidade agora dão suporte a vários conjuntos de disponibilidade em vez de um conjunto de disponibilidade único.  3. Recursos adicionais, como portas de alta disponibilidade, dados de monitoramento mais ricos e assim por diante. Se você estiver migrando para a zona de disponibilidade do Azure, o Load Balancer Standard será necessário. Para uma nova implantação, recomendamos que você comece com o Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. Authentication 
[Cloud Foundry conta de usuário e autenticação](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) é o serviço de gerenciamento de identidade central para o CF e seus vários componentes. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Por padrão, a UAA é usada para autenticação Cloud Foundry. Como uma opção avançada, a UAA também dá suporte ao Azure AD como um armazenamento de usuário externo. Os usuários do Azure AD podem acessar Cloud Foundry usando sua identidade LDAP, sem uma conta de Cloud Foundry. Siga estas etapas para [Configurar o Azure ad para o UAA em PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Armazenamento de dados para o sistema de tempo de execução Cloud Foundry
O Cloud Foundry oferece excelente extensibilidade para usar o Azure blobstore ou os serviços MySQL/PostgreSQL do Azure para o armazenamento do sistema de tempo de execução de aplicativo.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Blobstore do Azure para Cloud Foundry blobstore Cloud Controller
O blobstore do Cloud Controller é um armazenamento de dados crítico para buildpacks, dropletss, pacotes e pools de recursos. Por padrão, o servidor NFS é usado para blobstore do controlador de nuvem. Para evitar um ponto único de falha, use o armazenamento de BLOBs do Azure como armazenamento externo. Confira a [documentação do Cloud Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) para obter informações sobre o plano de fundo e [as opções na Cloud Foundry dinâmica](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL como Cloud Foundry banco de dados de tempo de execução elástico *
O tempo de execução elástico do CF requer dois bancos de dados do sistema principais:
#### <a name="ccdb"></a>CCDB 
O banco de dados do controlador de nuvem.  O Cloud Controller fornece pontos de extremidade da API REST para que os clientes acessem o sistema. O CCDB armazena tabelas para organizações, espaços, serviços, funções de usuário e muito mais para o controlador de nuvem.
#### <a name="uaadb"></a>UAADB 
O banco de dados para a conta de usuário e a autenticação. Ele armazena os dados relacionados à autenticação do usuário, por exemplo, nomes de usuário e senhas criptografados.

Por padrão, um MySQL (banco de dados do sistema local) pode ser usado. Para HA e escala, aproveite os serviços do MySQL gerenciados do Azure ou PostgreSQL. Aqui está a instrução de [habilitação do Azure MySQL/PostgreSQL para ccdB, UAADB e outros bancos de dados do sistema com Cloud Foundry de código aberto](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Abrir Service Broker
O Azure Service Broker oferece uma interface consistente para gerenciar o acesso do aplicativo aos serviços do Azure. A nova [Service Broker aberta para o projeto do Azure](https://github.com/Azure/open-service-broker-azure) fornece uma maneira única e simples de fornecer serviços a aplicativos em Cloud Foundry, OpenShift e kubernetes. Consulte o [bloco abrir Service Broker do Azure para PCF](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) para obter instruções de implantação em PCF.

## <a name="6-metrics-and-logging"></a>6. Métricas e registro em log
O bocal do Azure Log Analytics é um componente Cloud Foundry, que encaminha as métricas da [Cloud Foundry agregador firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) para [os logs do Azure monitor](https://azure.microsoft.com/services/log-analytics/). Com o bocal, você pode coletar, exibir e analisar as métricas de desempenho e integridade do sistema do CF em várias implantações.
Clique [aqui](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) para saber como implantar o bocal do Azure log Analytics no ambiente de Cloud Foundry de código-fonte aberto e dinâmico e, em seguida, acessar os dados no console de logs do Azure monitor. 
> [!NOTE]
> No PCF 2,0, as métricas de integridade BOSH para VMs são encaminhadas para o fIREHOSE agregador por padrão e são integradas ao console de logs de Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Economia de custos
### <a name="cost-saving-for-devtest-environments"></a>Economia de custos para ambientes de desenvolvimento/teste
#### <a name="b-series-"></a>Série B: *
Embora a série de VMs F e D fosse comumente recomendada para o ambiente de produção de Cloud Foundry dinâmico, a nova [série B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) "expansível" traz novas opções. As VMs expansível da série B são ideais para cargas de trabalho que não precisam do desempenho total da CPU continuamente, como servidores Web, bancos de dados pequenos e ambientes de desenvolvimento e teste. Essas cargas de trabalho normalmente têm requisitos de desempenho intermitentes. É $0.012/hora (B1) em comparação a $0,05/Hora (F1), consulte a lista completa de [tamanhos](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) e [preços](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) de VM para obter detalhes. 
#### <a name="managed-standard-disk"></a>Disco padrão gerenciado: 
Os discos Premium eram recomendados para um desempenho confiável na produção.  Com o [disco gerenciado](https://azure.microsoft.com/services/managed-disks/), o armazenamento Standard também pode fornecer confiabilidade semelhante, com um desempenho diferente. Para a carga de trabalho que não faz distinção de desempenho, como desenvolvimento/teste ou ambiente não crítico, os discos Standard gerenciados oferecem uma opção alternativa com menor custo.  
### <a name="cost-saving-in-general"></a>Economia de custos em geral 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Economia significativa de custos de VM com reservas do Azure: 
Hoje, todas as VMs do CF são cobradas usando preços "sob demanda", mesmo que os ambientes normalmente permaneçam indefinidamente. Agora você pode reservar a capacidade da VM em um período de 1 ou 3 anos e obter descontos de 45-65%. Os descontos são aplicados no sistema de cobrança, sem alterações no seu ambiente. Para obter detalhes, consulte [como as reservas do Azure funcionam](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Disco Premium gerenciado com tamanhos menores: 
Os discos gerenciados dão suporte a tamanhos de disco menores, por exemplo P4 (32 GB) e P6 (64 GB) para discos Premium e Standard. Se você tiver pequenas cargas de trabalho, poderá economizar custos ao migrar de discos Premium Standard para discos Premium gerenciados.
#### <a name="use-azure-first-party-services"></a>Use os serviços de primeira parte do Azure: 
Tirar proveito do serviço de primeira parte do Azure reduzirá o custo de administração de longo prazo, além de HA e confiabilidade mencionados nas seções acima. 

A Pivotal lançou um [pequeno ERT de espaço](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) para clientes PCF, os componentes estão colocalizados em apenas 4 VMS, executando até 2500 instâncias de aplicativos. A versão de avaliação agora está disponível por meio [do Azure Market Place](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Próximos Passos
Os recursos de integração do Azure estão disponíveis primeiro com [Cloud Foundry de código aberto](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), antes de serem disponibilizados na Cloud Foundry dinâmica. Os recursos marcados com * ainda não estão disponíveis por meio de PCF. A integração do Cloud Foundry com o Azure Stack também não é abordada neste documento.
Para suporte a PCF nos recursos marcados com *, ou Cloud Foundry integração com o Azure Stack, entre em contato com o seu gerente dinâmico e de conta Microsoft para obter o status mais recente. 

