---
title: Como o Cloud Foundry se integra no Azure | Documentos da Microsoft
description: Descreve como o Cloud Foundry pode utilizar os serviços do Azure para melhorar a experiência de Enterprise
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 7cbffdd40e574c7e906a9388b70ca9d32fd84649
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550178"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrar o Cloud Foundry com o Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) é uma plataforma de PaaS em execução na plataforma de IaaS de fornecedores de cloud. Ele oferece a experiência de implementação de aplicações consistente entre fornecedores de serviços cloud. Pode também integrar vários serviços do Azure, com HA, de nível empresarial de escalabilidade e reduções de custos.
Existem [6 subsistemas do Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), que pode ser flexível dimensionamento online, incluindo: Encaminhamento, autenticação, gestão de ciclo de vida de aplicações, gestão de serviço, monitorização e de mensagens. Para cada um os subsistemas, é possível configurar o Cloud Foundry para utilizar correspondent o serviço do Azure. 

![Cloud Foundry na arquitetura de integração do Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Elevada disponibilidade e escalabilidade
### <a name="managed-disk"></a>Disco gerido
Bosh utiliza a medição de CPI do Azure (Interface do fornecedor de Cloud) para a criação de disco e as rotinas de eliminação. Por predefinição, são utilizados discos não geridos. Ele requer o cliente manualmente criar contas de armazenamento, em seguida, configure as contas em arquivos de manifesto CF. Isso é devido à limitação do número de discos por conta de armazenamento.
Agora [disco gerido](https://azure.microsoft.com/services/managed-disks/) está disponível, oferece armazenamento de disco gerido, segura e fiável para máquinas virtuais. Cliente já não precisar lidar com a conta de armazenamento para dimensionamento e HA. Azure organiza discos automaticamente. Se se trata de um novo ou uma implementação existente, a medição de CPI do Azure irá processar a criação ou migração do disco gerido durante uma implantação CF. É suportado com o PCF 1.11. Também pode explorar o código-fonte aberto Cloud Foundry [documentação de orientação do disco gerido](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) para referência. 
### <a name="availability-zone-"></a>Zona de disponibilidade *
Como uma plataforma de aplicações nativas da cloud, Cloud Foundry destina-se com [quatro de nível de elevada disponibilidade](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Enquanto os primeiros três níveis de falhas de software podem ser manipulados pelo próprio sistema de CF, tolerância a falhas de plataforma é fornecida pelo fornecedor de serviços cloud. Os principais componentes de CF devem ser protegidos com a plataforma de um fornecedor de cloud a solução HA. Isto inclui GoRouters, Diego cérebro, CF mosaicos de base de dados e serviço. Por predefinição, [conjunto de disponibilidade do Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) é utilizado para tolerância a falhas, entre clusters num centro de dados.
É bem novo, [zona de disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview ) é lançada, agora, levando a tolerância a falhas para o próximo nível, com redundância de baixa latência em centros de dados.
Zona de disponibilidade do Azure alcança HA ao colocar um conjunto de VMs em centros de dados 2 +, cada conjunto de VMs são redundantes para outros conjuntos. Se uma zona estiver desativado, os conjuntos de outros são ainda em direto, isolada de desastre.
> [!NOTE] 
> Zona de disponibilidade do Azure não está disponível para todas as regiões ainda, verifique a versão mais recente [anúncio para obter a lista de regiões suportadas](https://docs.microsoft.com/azure/availability-zones/az-overview). Para abrir origem Cloud Foundry, verifique [zona de disponibilidade do Azure para obter orientações do código-fonte aberto Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Encaminhamento de rede
Por predefinição, o Balanceador de carga básico do Azure é utilizado para pedidos recebidos de aplicações e API CF, reenviando-os para o Gorouters. Componentes de CF como Diego cérebro, MySQL, ERT também pode utilizar o Balanceador de carga para balancear o tráfego para HA. O Azure também fornece um conjunto de soluções de balanceamento de carga totalmente geridos. Se estiver à procura de encerramento de TLS ("descarga de SSL") ou por processamento de camada de aplicativo de pedidos HTTP/HTTPS, considere o Gateway de aplicação. Para elevada disponibilidade e na camada 4 de balanceamento de carga de escalabilidade, considere o Balanceador de carga standard.
### <a name="azure-application-gateway-"></a>Gateway de aplicação do Azure *
[O Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) oferece várias capacidades, incluindo descarregamento SSL, ponto a ponto SSL, Firewall de aplicações Web, afinidade de sessão baseada em cookies e muito mais de balanceamento de carga layer 7. Pode [configurar o Gateway de aplicação na Cloud Foundry com origem aberto](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). Para o PCF, consulte a [notas de versão 2.1 do PCF](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) para teste de prova de conceito.

### <a name="azure-standard-load-balancer-"></a>Balanceador de carga Standard do Azure *
O Balanceador de carga do Azure é um balanceador de carga de camada 4. É utilizado para distribuir o tráfego entre instâncias de serviços num conjunto com balanceamento de carga. A versão standard fornece [funcionalidades avançadas](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) sobre a versão básica. Por exemplo, 1. O limite máximo do conjunto de back-end é gerado de 100 a 1000 VMs.  2. Os pontos finais agora suportam vários conjuntos de disponibilidade em vez de conjunto de disponibilidade única.  3. Funcionalidades adicionais, como portas HA, dados de monitorização mais avançados e assim por diante. Se estiver a mover para a zona de disponibilidade do Azure, o Balanceador de carga standard é necessário. Para uma nova implementação, recomendamos que comece com o Balanceador de carga Standard do Azure. 

## <a name="3-authentication"></a>3. Authentication 
[Cloud Foundry conta de utilizador e autenticação](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) é o serviço de gestão de identidade central para CF e seus diversos componentes. [O Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é multi-inquilino, com base na cloud diretório e identidade do serviço de gestão da Microsoft. Por predefinição, o UAA é utilizado para autenticação de Cloud Foundry. Como uma opção avançada, UAA também oferece suporte do Azure AD como um arquivo de utilizador externo. Utilizadores do Azure AD podem aceder à Cloud Foundry usando a identidade LDAP, sem uma conta do Cloud Foundry. Siga estes passos para [configurar o Azure AD para UAA no PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Armazenamento de dados para o sistema de tempo de execução do Cloud Foundry
Cloud Foundry oferece extensibilidade excelente para utilizar blobstore do Azure ou serviços MySQL/PostgreSQL do Azure para armazenamento de sistema de tempo de execução de aplicações.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Blobstore do Azure para o controlador de Cloud do Cloud Foundry blobstore
O controlador de Cloud blobstore é um arquivo de dados críticos para buildpacks, droplets, pacotes e agrupamentos de recursos. Por predefinição, o servidor NFS é utilizado para blobstore de controlador de Cloud. Para evitar ponto único de falha, utilize o armazenamento de Blobs do Azure como armazenamento externo. Veja a [documentação de Cloud Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) para obter informações, e [opções no Cloud Foundry da Pivotal](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>Base de dados de tempo de execução do MySQL/PostgreSQL, como o Cloud Foundry elástico *
Tempo de execução elástico CF requer duas bases de dados de sistema importante:
#### <a name="ccdb"></a>CCDB 
A base de dados do controlador de Cloud.  Controlador de cloud fornece pontos finais da API de REST aceder ao sistema, os clientes. CCDB armazena tabelas para organizações, espaços, serviços, funções de utilizador e mais para o controlador de Cloud.
#### <a name="uaadb"></a>UAADB 
A base de dados para a conta de utilizador e autenticação. Ele armazena a autenticação de utilizador relacionadas com os dados, por exemplo encriptados nomes de utilizador e palavras-passe.

Por predefinição, pode ser utilizada uma base de dados do sistema local (MySQL). Para HA e dimensionamento, tire partido do Azure MySQL gerido ou serviços de PostgreSQL. Eis a instrução de [ativar o Azure MySQL/PostgreSQL para CCDB, UAADB e outras bases de dados do sistema com o Open origem Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Abrir o Open Service Broker
Mediador de serviço do Azure oferece uma interface consistente para gerir o acesso da aplicação aos serviços do Azure. A nova [Open Service Broker para o projeto do Azure](https://github.com/Azure/open-service-broker-azure) fornece uma forma simples e única para fornecer serviços para aplicações em Cloud Foundry, OpenShift e Kubernetes. Consulte a [Azure Open Service Broker para o mosaico PCF](https://network.pivotal.io/products/azure-open-service-broker-pcf/) para obter instruções de implementação são executadas no PCF.

## <a name="6-metrics-and-logging"></a>6. Métricas e registo
O Nozzle do Azure Log Analytics é um componente de Cloud Foundry, que reencaminha métricas a partir do [Cloud Foundry loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) para [registos do Azure Monitor](https://azure.microsoft.com/services/log-analytics/). Com o Nozzle do utilizador, pode recolher, ver e analisar as métricas de estado de funcionamento e desempenho de sistema do CF entre múltiplas implementações.
Clique em [aqui](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) para saber como implementar o Nozzle do Azure Log Analytics para o código-fonte aberto e de ambiente de Cloud Foundry da Pivotal e, em seguida, aceder aos dados do Azure Monitor registos de consola. 
> [!NOTE]
> Do PCF 2.0, as métricas de estado de funcionamento BOSH para as VMs são reencaminhadas para o Loggregator Firehose por predefinição e estão integradas ao console de registos do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Economia de custo
### <a name="cost-saving-for-devtest-environments"></a>A guardar para ambientes de desenvolvimento/teste de custos
#### <a name="b-series-"></a>Série B: *
Embora seja série F e D VM foram normalmente recomendado para o ambiente de produção do Cloud Foundry da Pivotal, o novo "burstable" [série B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) traz novas opções. As VMs burstable de série B são ideais para cargas de trabalho que não tem um desempenho total da CPU continuamente, como servidores web, desenvolvimento e pequenas bases de dados e os ambientes de teste. Estas cargas de trabalho normalmente têm requisitos de desempenho burstable. Ele é de US $0.012/ hora (B1) em comparação com us $ 0,05/hora (F1), consulte a lista completa de [tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) e [preços](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) para obter detalhes. 
#### <a name="managed-standard-disk"></a>Disco gerido de padrão: 
Os discos Premium foram recomendados para um desempenho fiável na produção.  Com o [disco gerido](https://azure.microsoft.com/services/managed-disks/), armazenamento standard também pode fornecer confiabilidade semelhante, com desempenho diferente. Para a carga de trabalho que não sensíveis a desempenho, como o ambiente não críticas ou de programador/teste, os discos geridos standard oferecem uma opção alternativa com custo mais baixo.  
### <a name="cost-saving-in-general"></a>Em geral a economia de custo 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Significativo VM custo a guardar com as reservas do Azure: 
Hoje em dia que todas as VMs de CF são faturadas com preços "por demanda", apesar dos ambientes, normalmente, manter-se indefinidamente. Agora pode reservar capacidade VM num período de 1 ou 3 anos e ganhe descontos 45 e 65%. Se aplica qualquer desconto no sistema de faturação, sem alterações ao seu ambiente. Para obter detalhes, consulte [como Azure reservas funciona](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Disco de Premium com tamanhos menores de gerido: 
Managed disks suporte menores tamanhos de disco, por exemplo, P4(32 GB) e P6(64 GB) para premium e os discos standard. Se tiver cargas de trabalho pequenas, pode poupar custos ao migrar de discos premium padrão para os discos geridos premium.
#### <a name="use-azure-first-party-services"></a>Utilize os serviços de terceiros primeiro do Azure: 
Tirar partido do serviço de proprietário do Azure irá reduzir a administração de longa duração de custos, além de HA e confiabilidade mencionado em secções. 

Pivotal lançou uma [ERT de superfície pequena](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) para clientes PCF, os componentes estiver localizados conjuntamente em apenas 4 VMs, em execução até 2500 instâncias da aplicação. A versão de avaliação está agora disponível através de [mercado do Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Próximos Passos
Funcionalidades de integração do Azure pela primeira vez estão disponíveis com [aberto origem Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), antes de ficarem disponível no Cloud Foundry da Pivotal. Recursos marcados com * ainda não estiverem disponíveis através do PCF. Cloud Foundry integração com o Azure Stack ou não é abrangida neste documento.
Para suporte PCF sobre os recursos marcados com *, ou integração da Cloud Foundry com o Azure Stack, contacte o seu Gestor de conta da Pivotal e da Microsoft para obter o estado mais recente. 

