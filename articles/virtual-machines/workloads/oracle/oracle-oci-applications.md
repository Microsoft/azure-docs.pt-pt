---
title: Arquiteturas para implementar aplicativos Oracle em Azure Virtual Machines Microsoft Docs
description: Arquiteturas de aplicações para implementar aplicativos Oracle incluindo E-Business Suite, JD Edwards EnterpriseOne, e PeopleSoft em máquinas virtuais Microsoft Azure com bases de dados em Azure ou em Oracle Cloud Infrastructure (OCI).
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 07/18/2019
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: aa481090e3483e58f6a88304e3e9d8c1a16df3c7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965923"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Arquiteturas para implementar aplicações da Oracle no Azure

A Microsoft e a Oracle trabalharam em conjunto para permitir que os clientes implementem aplicações oracle como a Oracle E-Business Suite, JD Edwards EnterpriseOne e PeopleSoft na nuvem. Com a introdução da [interconectividade](configure-azure-oci-networking.md) da rede privada de pré-visualização entre o Microsoft Azure e a Oracle Cloud Infrastructure (OCI), as aplicações oracle podem agora ser implementadas no Azure com as suas bases de dados back-end em Azure ou OCI. As aplicações da Oracle também podem ser integradas com o Azure Ative Directory, permitindo-lhe configurar uma única inscrição para que os utilizadores possam assinar na aplicação Oracle usando as suas credenciais Azure Ative Directory (Azure AD).

O OCI oferece várias opções de base de dados oracle para aplicações Oracle, incluindo DBaaS, Exadata Cloud Service, Oracle RAC e Infrastructure-as-a-Service (IaaS). Atualmente, a Base de Dados Autónoma não é um back-end suportado para aplicações oracle.

Existem [múltiplas opções](oracle-overview.md) para implementar aplicações Oracle em Azure, incluindo de forma altamente disponível e segura. O Azure também oferece [imagens VM da base de dados da Oracle](oracle-vm-solutions.md) que pode implementar se optar por executar as suas aplicações Oracle inteiramente no Azure.

As secções seguintes descrevem recomendações de arquitetura tanto da Microsoft como da Oracle para implementar a Oracle E-Business Suite, jD Edwards EnterpriseOne e PeopleSoft numa configuração de nuvem cruzada ou inteiramente em Azure. A Microsoft e a Oracle testaram estas aplicações e confirmaram que o desempenho cumpre os padrões estabelecidos pela Oracle para estas aplicações.

## <a name="architecture-considerations"></a>Considerações de arquitetura

As aplicações da Oracle são compostas por múltiplos serviços, que podem ser hospedados nas mesmas ou múltiplas máquinas virtuais em Azure e opcionalmente no OCI. 

As instâncias de aplicação podem ser configurada com pontos finais privados ou públicos. A Microsoft e a Oracle recomendam a criação de um *VM anfitrião de bastião* com um endereço IP público numa sub-rede separada para a gestão da aplicação. Em seguida, atribua apenas endereços IP privados às outras máquinas, incluindo o nível de base de dados. 

Ao configurar uma aplicação numa arquitetura de nuvem cruzada, é necessário planear para garantir que o espaço de endereço IP na rede virtual Azure não se sobrepõe ao espaço de endereço IP privado na rede de nuvem virtual OCI.

Para uma maior segurança, crie grupos de segurança de rede a um nível de sub-rede para garantir apenas tráfego em portas específicas e endereços IP. Por exemplo, as máquinas no nível médio só devem receber tráfego dentro da rede virtual. Nenhum tráfego externo deve chegar diretamente às máquinas de nível médio.

Para uma elevada disponibilidade, pode configurar casos redundantes dos diferentes servidores no mesmo conjunto de disponibilidade ou diferentes zonas de disponibilidade. As zonas de disponibilidade permitem-lhe atingir um SLA de 99,99% de tempo de subida, enquanto os conjuntos de disponibilidade permitem-lhe atingir um SLA de 99,95% na região. As arquiteturas de amostra mostradas neste artigo são implantadas em duas zonas de disponibilidade.

Ao implementar uma aplicação utilizando a interligação de nuvem cruzada, poderá continuar a utilizar um circuito ExpressRoute existente para ligar o seu ambiente Azure à sua rede no local. No entanto, precisa de um circuito ExpressRoute separado para a interligação ao OCI do que aquele que está ligado à sua rede no local.

## <a name="e-business-suite"></a>Suíte E-Business

Oracle E-Business Suite (EBS) é um conjunto de aplicações, incluindo Gestão da Cadeia de Abastecimento (SCM) e Gestão de Relacionamento com o Cliente (CRM). Para tirar partido do portfólio de bases de dados gerido pelo OCI, o EBS pode ser implementado utilizando a interligação entre o Microsoft Azure e o OCI. Nesta configuração, os níveis de apresentação e aplicação são executados em Azure e o nível de base de dados em OCI, conforme ilustrado no seguinte diagrama de arquitetura (Figura 1).

![Arquitetura cross-cloud da Suíte E-Business](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Figura 1: Arquitetura cross-cloud da E-Business Suite* 

Nesta arquitetura, a rede virtual em Azure está ligada a uma rede de nuvem virtual em OCI usando a interligação cross-cloud. O nível de aplicação é criado em Azure, enquanto a base de dados é criada no OCI. Recomenda-se a implantação de cada componente na sua própria sub-rede com grupos de segurança de rede, de forma a permitir o tráfego apenas a partir de sub-redes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente em Azure com bases de dados Oráculo altamente disponíveis configuradas usando a Oracle Data Guard em duas zonas de disponibilidade numa região. O seguinte diagrama (Figura 2) é um exemplo deste padrão arquitetónico:

![Arquitetura Azure-só da Suíte E-Business](media/oracle-oci-applications/ebs-arch-azure.png)

*Figura 2: E-Business Suite Azure-only architecture*

As seguintes secções descrevem os diferentes componentes a um nível elevado.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Nível de aplicação (médio)

O nível de aplicação está isolado na sua própria sub-rede. Existem várias máquinas virtuais preparadas para tolerância a falhas e gestão fácil de patchs. Estes VMs podem ser apoiados por armazenamento partilhado, que é oferecido por Azure NetApp Files e Ultra SSDs. Esta configuração permite uma implementação mais fácil de patches sem tempo de inatividade. As máquinas do nível de aplicação devem ser frontalizadas por um balançador de carga pública de modo a que os pedidos ao nível de aplicação EBS sejam processados mesmo que uma máquina no nível esteja offline devido a uma falha.

### <a name="load-balancer"></a>Balanceador de carga

Um equilibrador de carga Azure permite-lhe distribuir tráfego em várias instâncias da sua carga de trabalho para garantir uma elevada disponibilidade. Neste caso, é criado um equilibrador de carga pública, uma vez que os utilizadores estão autorizados a aceder à aplicação EBS pela web. O equilibrador de carga distribui a carga a ambas as máquinas no nível médio. Para uma maior segurança, permita o tráfego apenas de utilizadores que acedam ao sistema a partir da sua rede corporativa utilizando uma VPN site-site ou ExpressRoute e grupos de segurança de rede.

### <a name="database-tier"></a>Nível de base de dados

Este nível acolhe a base de dados Oráculo e está separada na sua própria sub-rede. Recomenda-se a adição de grupos de segurança de rede que apenas permitem o tráfego do nível de aplicação para o nível de base de dados na porta de dados específica da Oracle 1521.

A Microsoft e a Oracle recomendam uma configuração de alta disponibilidade. A alta disponibilidade em Azure pode ser alcançada através da criação de duas bases de dados Oracle em duas zonas de disponibilidade com a Oracle Data Guard, ou utilizando o Oracle Database Exadata Cloud Service em OCI. Ao utilizar o Oracle Database Exadata Cloud Service, a sua base de dados é implantada em duas sub-redes. Também pode configurar a Oracle Database em VMs em OCI em dois domínios de disponibilidade com a Oracle Data Guard.


### <a name="identity-tier"></a>Nível de identidade

O nível de identidade contém o VM EBS Asserter. O EBS Asserter permite sincronizar identidades do Oracle Identity Cloud Service (IDCS) e Azure AD. O Asserter EBS é necessário porque o EBS não suporta protocolos de assinatura único como SAML 2.0 ou OpenID Connect. O Asserter EBS consome o token de ligação OpenID (gerado pelo IDCS), valida-o e, em seguida, cria uma sessão para o utilizador em EBS. 

Embora esta arquitetura mostre a integração IDCS, o acesso unificado Azure AD e o único sign-on também podem ser ativados com o Oracle Access Manager com o Diretório de Internet oracle ou diretório unificado da Oracle. Para obter mais informações, consulte os whitepapers sobre [a implantação do Oráculo EBS com integração IDCS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) ou [implementação de Oráculo EBS com integração OAM](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Para uma elevada disponibilidade, recomenda-se que implemente servidores redundantes do Asserter EBS em várias zonas de disponibilidade com um equilibrador de carga à sua frente.

Uma vez configurada a sua infraestrutura, o E-Business Suite pode ser instalado seguindo o guia de instalação fornecido pela Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

A Oacle's JD Edwards EnterpriseOne é um conjunto integrado de aplicações de software abrangente de planeamento de recursos empresariais. Trata-se de uma aplicação multi-camadas que pode ser configurada com um backend de base de dados Oracle ou SQL Server. Esta secção discute detalhes sobre a implementação da JD Edwards EnterpriseOne com uma base de dados Oracle back-end, quer no OCI, quer no Azure.

Na seguinte arquitetura recomendada (Figura 3), a administração, apresentação e níveis médios são implantados na rede virtual em Azure. A base de dados é implantada numa rede de nuvem virtual no OCI.

Tal como acontece com a E-Business Suite, pode configurar um nível de bastião opcional para fins administrativos seguros. Utilize o anfitrião VM de bastião como servidor de salto para aceder às instâncias de aplicação e base de dados.

![JD Edwards EnterpriseOne arquitetura cross-cloud](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Figura 3: JD Edwards EnterpriseOne arquitetura cross-cloud*

Nesta arquitetura, a rede virtual em Azure está ligada à rede de nuvem virtual em OCI usando a interligação cross-cloud. O nível de aplicação é criado em Azure, enquanto a base de dados é criada no OCI. Recomenda-se a implantação de cada componente na sua própria sub-rede com grupos de segurança de rede, de forma a permitir o tráfego apenas a partir de sub-redes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente em Azure com bases de dados Oráculo altamente disponíveis configuradas usando a Oracle Data Guard em duas zonas de disponibilidade numa região. O seguinte diagrama (Figura 4) é um exemplo deste padrão arquitetónico:

![JD Edwards EnterpriseOne Azure-only architecture](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Figura 4: JD Edwards EnterpriseOne Azure-only architecture*

As seguintes secções descrevem os diferentes componentes a um nível elevado.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Nível administrativo

Como o nome sugere, este nível é usado para tarefas administrativas. Pode esculpir uma sub-rede separada para o nível administrativo. Os serviços e servidores deste nível são usados principalmente para a instalação e administração da aplicação. Portanto, as instâncias individuais destes servidores são suficientes. Não são necessárias instâncias redundantes para a elevada disponibilidade da sua aplicação.

Os componentes deste nível são os seguintes:
    
 - **Servidor de provisionamento** - Este servidor é utilizado para a implementação de ponta a ponta dos diferentes componentes da aplicação. Comunica com os casos nos outros níveis, incluindo os casos no nível da base de dados, sobre a porta 22. Acolhe a Consola de Gestor de Servidores para JD Edwards EnterpriseOne.
 - **Servidor de implementação** - Este servidor é principalmente necessário para a instalação da JD Edwards EnterpriseOne. Durante o processo de instalação, este servidor funciona como o repositório central para ficheiros e pacotes de instalação necessários. O software é distribuído ou implementado para outros servidores e clientes deste servidor.
 - **Cliente de desenvolvimento** - Este servidor contém componentes que funcionam num navegador web, bem como aplicações nativas.

### <a name="presentation-tier"></a>Camada física de apresentação

Este nível contém vários componentes, tais como Serviços de Interface de Aplicação (AIS), Quadro de Desenvolvimento de Aplicações (ADF) e Servidores de Aplicações java (JAS). Os servidores deste nível comunicam com os servidores no nível médio. São frontados por um equilibrador de carga que encaminha o tráfego para o servidor necessário com base no número da porta e URL em que o tráfego é recebido. Recomenda-se que implemente várias instâncias de cada tipo de servidor para uma elevada disponibilidade.

Seguem-se os componentes deste escalão:
    
- **Serviços de Interface de Aplicação (AIS)** - O servidor AIS fornece a interface de comunicação entre aplicações móveis JD Edwards EnterpriseOne e JD Edwards EnterpriseOne.
- **Java Application Server (JAS)** - O JAS recebe pedidos do equilibrador de carga e passa-o para o nível médio para executar tarefas complicadas. O JAS tem a capacidade de executar uma lógica de negócio simples.
- **BI Publisher Server (BIP)** - Este servidor apresenta relatórios baseados nos dados recolhidos pela aplicação JD Edwards EnterpriseOne. Você pode projetar e controlar como o relatório apresenta os dados com base em diferentes modelos.
- **Servidor de Serviços Empresariais (BSS)** - O BSS permite a troca de informações e interoperabilidade com outras aplicações da Oracle.
- **Servidor de Eventos em Tempo Real (RTE)** - O Servidor RTE permite-lhe configurar notificações para sistemas externos sobre transações que ocorram no sistema JDE EnterpriseOne. Utiliza um modelo de subscrição e permite que sistemas de terceiros subscrevam eventos. Para carregar os pedidos de equilíbrio para ambos os servidores RTE, certifique-se de que os servidores estão num cluster.
- **Servidor do Quadro de Desenvolvimento de Aplicações (ADF)** - O servidor ADF é utilizado para executar aplicações JD Edwards EnterpriseOne desenvolvidas com a Oracle ADF. Isto é implantado num servidor Oracle WebLogic com tempo de execução ADF.

### <a name="middle-tier"></a>Nível médio

O nível médio contém o servidor lógico e o servidor de lote. Neste caso, ambos os servidores são instalados na mesma máquina virtual. No entanto, para cenários de produção, recomenda-se que implemente servidor de lógica e servidor de lote em servidores separados. Vários servidores são implantados no nível médio em duas zonas de disponibilidade para uma maior disponibilidade. Deve ser criado um equilibrador de carga Azure e estes servidores devem ser colocados no seu pool de backend para garantir que ambos os servidores estão ativos e a processar pedidos.

Os servidores do nível médio recebem pedidos dos servidores no nível de apresentação e apenas do balançador de carga pública. Devem ser criadas regras de grupo de segurança de rede para negar o tráfego de qualquer endereço que não seja a sub-rede de nível de apresentação e o balançador de carga. Uma regra NSG também pode ser criada para permitir o tráfego no porto 22 do hospedeiro de bastião para fins de gestão. Poderá utilizar o balançador de carga pública para carregar os pedidos de equilíbrio entre os VMs no nível médio.

Os dois componentes seguintes encontram-se no escalão médio:

- **Servidor lógico** - Contenha a lógica de negócio ou as funções de negócio.
- **Servidor de lote** - Utilizado para processamento de lotes

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

O pacote de aplicações PeopleSoft da Oracle contém software para recursos humanos e gestão financeira. O conjunto de aplicações é multi-tiered e as aplicações incluem sistemas de gestão de recursos humanos (HRMS), gestão de relacionamento com o cliente (CRM), finanças e gestão da cadeia de fornecimento (FSCM), e gestão do desempenho da empresa (EPM).

Recomenda-se que cada nível do pacote de software seja implantado na sua própria sub-rede. Uma base de dados Oracle ou Microsoft SQL Server é necessária como base de dados de backend para a aplicação. Esta secção discute detalhes sobre a implementação do PeopleSoft com um backend da base de dados oracle.

Segue-se uma arquitetura canónica para a implantação da suite de aplicação PeopleSoft numa arquitetura de nuvem cruzada (Figura 5).

![Arquitetura de nuvem cruzada PeopleSoft](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Figura 5: Arquitetura de nuvem cruzada PeopleSoft*

Nesta arquitetura de amostra, a rede virtual em Azure está ligada à rede de nuvem virtual em OCI usando a interligação cross-cloud. O nível de aplicação é criado em Azure, enquanto a base de dados é criada no OCI. Recomenda-se a implantação de cada componente na sua própria sub-rede com grupos de segurança de rede, de forma a permitir o tráfego apenas a partir de sub-redes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente em Azure com bases de dados Oráculo altamente disponíveis configuradas usando a Oracle Data Guard em duas zonas de disponibilidade numa região. O seguinte diagrama (Figura 6) é um exemplo deste padrão arquitetónico:

![Arquitetura só para o PovoSoft Azure](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Figura 6: Arquitetura só para o PovoSSoft Azure*

As seguintes secções descrevem os diferentes componentes a um nível elevado.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Camada física da aplicação

O nível de aplicação contém instâncias dos servidores de aplicações Dosoft, servidores web PeopleSoft, pesquisa elástica e Agendador de Processos PeopleSoft. É criado um equilibrador de carga Azure para aceitar pedidos de utilizadores que sejam encaminhados para o servidor apropriado no nível de aplicação.

Para uma elevada disponibilidade, considere a criação de casos redundantes de cada servidor no nível de aplicação em diferentes zonas de disponibilidade. O equilibrador de carga Azure pode ser configurado com várias piscinas traseiras para direcionar cada pedido para o servidor certo.

### <a name="peopletools-client"></a>Cliente PeopleTools

O Cliente PeopleTools é utilizado para realizar atividades de administração, tais como desenvolvimento, migração e upgrade. Como o Cliente PeopleTools não é necessário para alcançar uma elevada disponibilidade da sua aplicação, não são necessários servidores redundantes do Cliente PeopleTools.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Passos seguintes

Utilize [scripts Terraform](https://github.com/microsoft/azure-oracle) para configurar aplicações Oracle em Azure e estabelecer conectividade cruzada com o OCI.

Para obter mais informações e documentos brancos sobre o OCI, consulte a documentação da [Oracle Cloud.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
