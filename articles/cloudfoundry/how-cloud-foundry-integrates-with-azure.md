---
title: Como a Cloud Foundry se integra com o Azure Microsoft Docs
description: Descreve como a Cloud Foundry pode usar os serviços Azure para melhorar a experiência da Enterprise
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 04ef72f7ec70b370305395ae8de8180f4594b43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277336"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrar o Cloud Foundry com o Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) é uma plataforma PaaS que funciona no topo da plataforma IaaS dos fornecedores de nuvem. Oferece uma experiência consistente de implementação de aplicações entre fornecedores de nuvem. Também pode integrar-se com vários serviços Azure, com ha de grau empresarial, escalabilidade e poupança de custos.
Existem [6 subsistemas de Cloud Foundry,](https://docs.cloudfoundry.org/concepts/architecture/)que podem ser de forma flexível online, incluindo: Encaminhamento, Autenticação, Gestão do ciclo de vida da aplicação, gestão de serviços, mensagens e monitorização. Para cada um dos subsistemas, pode configurar a Cloud Foundry para utilizar o serviço de correspondente Azure. 

![Fundry cloud na arquitetura de integração azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Elevada disponibilidade e escalabilidade
### <a name="managed-disk"></a>Disco Gerido
A Bosh utiliza o Azure CPI (Cloud Provider Interface) para criar e eliminar rotinas de disco. Por defeito, são utilizados discos não geridos. Requer que o cliente crie manualmente contas de armazenamento e, em seguida, configure as contas em ficheiros manifestos cf. Isto deve-se à limitação do número de discos por conta de armazenamento.
Agora o [Disco Gerido](https://azure.microsoft.com/services/managed-disks/) está disponível, oferece armazenamento de disco seguro e fiável para máquinas virtuais. O cliente já não precisa de lidar com a conta de armazenamento para escala e HA. O Azure organiza discos automaticamente. Seja uma implantação nova ou existente, o Azure CPI tratará da criação ou migração do disco gerido durante uma implantação do CF. É suportado com PCF 1.11. Também pode explorar a orientação de cloud foundry [managed disk](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) de origem aberta para referência. 
### <a name="availability-zone-"></a>Zona de Disponibilidade *
Como uma plataforma de aplicação nativa de nuvem, a Cloud Foundry é projetada com [quatro níveis de alta disponibilidade.](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html) Embora os três primeiros níveis de falhas de software possam ser tratados pelo próprio sistema CF, a tolerância à falha da plataforma é fornecida pelos fornecedores de nuvem. Os componentes CF-chave devem ser protegidos com a solução HA da plataforma de um fornecedor de nuvem. Isto inclui GoRouters, Diego Brains, base de dados CF e azulejos de serviço. Por padrão, o Conjunto de [Disponibilidade azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) é usado para a tolerância à falha entre clusters num centro de dados.
A boa novidade é que a [Azure Availability Zone](https://docs.microsoft.com/azure/availability-zones/az-overview ) é agora lançada, elevando a tolerância à falha para o próximo nível, com baixa redundância de latência em todos os centros de dados.
A Zona de Disponibilidade Azure obtém HA colocando um conjunto de VMs em centros de dados de 2+, cada conjunto de VMs são redundantes para outros conjuntos. Se uma zona estiver em baixo, os outros conjuntos ainda estão vivos, isolados do desastre.
> [!NOTE] 
> A Zona de Disponibilidade Azure ainda não é oferecida a todas as regiões, confira o mais recente [anúncio da lista de regiões apoiadas.](https://docs.microsoft.com/azure/availability-zones/az-overview) Para a Foundry de Nuvem de Código Aberto, consulte a [Zona de Disponibilidade Azure para obter orientação](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone)de cloud foundry de código aberto .

## <a name="2-network-routing"></a>2. Encaminhamento de Rede
Por padrão, o equilíbrio de carga básico azure é usado para os pedidos de API/apps cf incoming, reencaminhando-os para os Gorouters. Componentes CF como Diego Brain, MySQL, ERT também podem usar o equilibrador de carga para equilibrar o tráfego para HA. O Azure também fornece um conjunto de soluções de equilíbrio de carga totalmente geridas. Se estiver à procura de rescisão de TLS ("SSL offload") ou por HTTP/HTTPS solicitar processamento de camada de pedido, considere application Gateway. Para uma elevada disponibilidade e equilíbrio de carga de escalabilidade na camada 4, considere o equilibrador de carga padrão.
### <a name="azure-application-gateway-"></a>Gateway de aplicação azure *
O Portal de [Aplicações Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) oferece várias capacidades de equilíbrio de carga de camada 7, incluindo descarregamento ssl, final para fim SSL, Firewall de aplicação web, afinidade de sessão baseada em cookies e muito mais. Pode configurar o Gateway de [Aplicação na Fundição](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway)de Nuvem de Código Aberto . Para pcf, verifique as notas de [lançamento do PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) para o teste POC.

### <a name="azure-standard-load-balancer-"></a>Equilíbrio de carga padrão Azure *
O equilíbrio de carga Azure é um equilibrador de carga de camada 4. É usado para distribuir o tráfego entre casos de serviços em um conjunto equilibrado de carga. A versão padrão fornece [funcionalidades avançadas](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) em cima da versão básica. Por exemplo 1. O limite máximo do pool de backend é aumentado de 100 para 1000 VMs.  2. Os pontos finais suportam agora vários conjuntos de disponibilidade em vez de conjuntos de disponibilidade individuais.  3. Funcionalidades adicionais como portas HA, dados de monitorização mais ricos, e assim por diante. Se estiver a mudar-se para a Zona de Disponibilidade Azure, é necessário um equilíbrio de carga padrão. Para uma nova implementação, recomendamos que comece com o Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. Autenticação 
[Cloud Foundry User Account and Authentication](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) é o serviço central de gestão de identidade para CF e seus vários componentes. [O Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é o serviço multi-inquilino, diretório baseado na nuvem e gestão de identidade. Por padrão, os UAA são usados para autenticação cloud foundry. Como uma opção avançada, a UAA também suporta a Azure AD como uma loja de utilizadores externo. Os utilizadores de AD Azure podem aceder à Cloud Foundry utilizando a sua identidade LDAP, sem uma conta Cloud Foundry. Siga estes passos para [configurar o Azure AD para UAA em PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Armazenamento de dados para sistema de execução de fundição em nuvem
A Cloud Foundry oferece uma grande extibilidade para utilizar os serviços Azure blobstore ou Azure MySQL/PostgreSQL para armazenamento do sistema de execução de aplicações.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Loja Azure Blobstore para cloud foundry Cloud Controller blobstore
A blobstore cloud controller é uma loja de dados crítica para buildpacks, gotículas, pacotes e piscinas de recursos. Por predefinição, o servidor NFS é utilizado para blobstore do Controlador de Nuvem. Para evitar um único ponto de avaria, utilize o Armazenamento Azure Blob como loja externa. Consulte a documentação da [Cloud Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) para obter antecedentes e [opções em Foundry de Nuvem Pivotal](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL como Cloud Foundry Elastic Run Time Database *
O tempo de funcionar elástico do CF requer duas bases de dados importantes do sistema:
#### <a name="ccdb"></a>CCDB 
A base de dados do Controlador de Nuvem.  O Cloud Controller fornece pontos finais REST API para os clientes acederem ao sistema. CcDB armazena tabelas para orgs, espaços, serviços, funções de utilizador e muito mais para controlador Cloud.
#### <a name="uaadb"></a>UAADB 
A base de dados para Conta de Utilizador e Autenticação. Armazena os dados relacionados com a autenticação do utilizador, por exemplo, nomes de utilizadores encriptados e palavras-passe.

Por predefinição, pode ser utilizada uma base de dados do sistema local (MySQL). Para ha e à escala, a alavancagem Azure geriu os serviços MySQL ou PostgreSQL. Aqui está a instrução de [habilitar o Azure MySQL/PostgreSQL para CCDB, UAADB e outras bases](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service)de dados do sistema com a Open Source Cloud Foundry .

## <a name="5-open-service-broker"></a>5. Corretor de Serviço aberto
O corretor de serviços Azure oferece uma interface consistente para gerir o acesso da aplicação aos serviços Azure. O novo open service broker para o [projeto Azure](https://github.com/Azure/open-service-broker-azure) fornece uma forma única e simples de fornecer serviços a aplicações em Cloud Foundry, OpenShift e Kubernetes. Consulte o Corretor de [ServiçoS Abertos Azure para](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) obter instruções de implementação no PCF.

## <a name="6-metrics-and-logging"></a>6. Métricas e Exploração Madeireira
O bocal de análise de registo azure é um componente de Cloud Foundry, que encaminha métricas desde a mangueira de [loggregator Cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) até aos [registos do Monitor Azure](https://azure.microsoft.com/services/log-analytics/). Com o Bocal, pode recolher, visualizar e analisar as métricas de saúde e desempenho do seu sistema CF em várias implementações.
Clique [aqui](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) para aprender a implementar o bocal de análise de log azure para o ambiente de Open Source e Pivotal Cloud Foundry e, em seguida, aceda aos dados da consola de logs Do Monitor Azure. 
> [!NOTE]
> A partir de PCF 2.0, as métricas de saúde BOSH para VMs são reencaminhadas para a Mangueira de Fogo loggregator por padrão, e são integradas na consola de logs Do Monitor Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Economia de Custos
### <a name="cost-saving-for-devtest-environments"></a>Economia de custos para Ambientes Dev/Teste
#### <a name="b-series-"></a>Série B: *
Enquanto as séries F e D VM eram geralmente recomendadas para o ambiente de produção da Fundação de Nuvem Pivotal, a nova [série B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) "burstable" traz novas opções. Os VMs de série B são ideais para cargas de trabalho que não necessitam de todo o desempenho do CPU continuamente, como servidores web, pequenas bases de dados e ambientes de desenvolvimento e teste. Estas cargas de trabalho normalmente têm requisitos de desempenho insuportáveis. É $0.012/hora (B1) em comparação com $0,05/hora (F1), consulte a lista completa de tamanhos de [VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) e [preços](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) para detalhes. 
#### <a name="managed-standard-disk"></a>Disco Padrão Gerido: 
Foram recomendados discos premium para um desempenho fiável na produção.  Com [o Disco Gerido,](https://azure.microsoft.com/services/managed-disks/)o armazenamento padrão também pode oferecer uma fiabilidade semelhante, com um desempenho diferente. Para uma carga de trabalho que não seja sensível ao desempenho, como dev/test ou ambiente não crítico, os discos standard geridos oferecem uma opção alternativa com um custo mais baixo.  
### <a name="cost-saving-in-general"></a>Economia de custos em geral 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Economia significativa de custos de VM com reservas Azure: 
Hoje em dia, todos os VMs cf são cobrados usando preços "on-demand", embora os ambientes normalmente permaneçam em pé indefinidamente. Agora pode reservar a capacidade de VM num prazo de 1 ou 3 anos, e ganhar descontos de 45-65%. Os descontos são aplicados no sistema de faturação, sem alterações no seu ambiente. Para mais detalhes, consulte [como funcionam as reservas do Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/) 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Disco Premium Gerido com tamanhos menores: 
Os discos geridos suportam tamanhos de discos mais pequenos, por exemplo P4 (32 GB) e P6 (64 GB) para discos premium e standard. Se tiver pequenas cargas de trabalho, pode economizar custos ao migrar de discos premium standard para discos premium geridos.
#### <a name="use-azure-first-party-services"></a>Utilize serviços de primeira festa azure: 
Tirar partido do primeiro serviço partidário da Azure reduzirá o custo administrativo a longo prazo, para além do HA e da fiabilidade mencionados em secções acima. 

A Pivotal lançou um [ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) de pequena pegada para clientes PCF, os componentes são co-localizados em apenas 4 VMs, funcionando até 2500 instâncias de aplicação. A versão experimental está agora disponível através do [Azure Market Place.](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)

## <a name="next-steps"></a>Passos Seguintes
As funcionalidades de integração azure estão disponíveis pela primeira vez com [a Open Source Cloud Foundry,](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/)antes de estar disponível na Fundição de Nuvem Pivotal. As funcionalidades marcadas com * ainda não estão disponíveis através do PCF. A integração da Cloud Foundry com o Azure Stack também não está coberta neste documento.
Para suporte de PCF nas funcionalidades marcadas com *, ou integração da Cloud Foundry com o Azure Stack, contacte o seu gestor de conta Pivotal e Microsoft para obter o seu estado mais recente. 

