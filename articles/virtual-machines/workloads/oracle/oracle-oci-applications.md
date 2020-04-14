---
title: Arquiteturas para implementar aplicações da Oracle em Máquinas Virtuais Azure [ Microsoft Docs
description: Arquiteturas de aplicações para implementar aplicações Oracle, incluindo E-Business Suite, JD Edwards EnterpriseOne, e PeopleSoft em máquinas virtuais Microsoft Azure com bases de dados em Azure ou na Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: mimckitt
ms.custom: ''
ms.openlocfilehash: 20e751b322d06ac176ee5c634d92e0efe874baac
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263306"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Arquiteturas para implementar aplicações da Oracle no Azure

A Microsoft e a Oracle trabalharam em conjunto para permitir que os clientes implementem aplicações da Oracle, como a Oracle E-Business Suite, a JD Edwards EnterpriseOne e a PeopleSoft na nuvem. Com a introdução da [interconectividade da rede privada](configure-azure-oci-networking.md) de pré-visualização entre o Microsoft Azure e o Oracle Cloud Infrastructure (OCI), as aplicações oracle podem agora ser implementadas no Azure com as suas bases de dados back-end em Azure ou OCI. As aplicações Oracle também podem ser integradas com o Azure Ative Directory, permitindo-lhe configurar um único sinal para que os utilizadores possam assinar na aplicação Oracle usando as suas credenciais Azure Ative Directory (Azure AD).

O OCI oferece várias opções de base de dados oracle para aplicações Oracle, incluindo DBaaS, Exadata Cloud Service, Oracle RAC e Infrastructure-as-a-Service (IaaS). Atualmente, a Base de Dados Autónoma não é um back-end suportado para aplicações Oracle.

Existem [várias opções](oracle-overview.md) para implementar aplicações Oracle em Azure, incluindo de forma altamente disponível e segura. O Azure também oferece [imagens VM](oracle-vm-solutions.md) da base de dados Oracle que pode implementar se optar por executar as suas aplicações Oracle inteiramente no Azure.

As seguintes secções delineiam recomendações de arquitetura tanto da Microsoft como da Oracle para implementar a Oracle E-Business Suite, jD Edwards EnterpriseOne e PeopleSoft numa configuração transversal ou inteiramente em Azure. A Microsoft e a Oracle testaram estas aplicações e confirmaram que o desempenho cumpre os padrões estabelecidos pela Oracle para estas aplicações.

## <a name="architecture-considerations"></a>Considerações de arquitetura

As aplicações oracle são compostas por múltiplos serviços, que podem ser hospedados nas mesmas ou múltiplas máquinas virtuais em Azure e opcionalmente em OCI. 

As instâncias de candidatura podem ser configuradas com pontos finais privados ou públicos. A Microsoft e a Oracle recomendam a criação de um *VM de anfitrião* de bastião com um endereço IP público numa subnet separada para a gestão da aplicação. Em seguida, atribua apenas endereços IP privados às outras máquinas, incluindo o nível de base de dados. 

Ao configurar uma aplicação numa arquitetura cross-cloud, é necessário planear para garantir que o espaço de endereço IP na rede virtual Azure não se sobreponha ao espaço de endereçoip privado na rede de nuvem virtual OCI.

Para uma maior segurança, criar grupos de segurança de rede a nível de subnet para garantir apenas o tráfego em portas específicas e endereços IP é permitido. Por exemplo, as máquinas do nível médio só devem receber tráfego dentro da rede virtual. Nenhum tráfego externo deve chegar diretamente às máquinas de nível médio.

Para uma elevada disponibilidade, pode configurar instâncias redundantes dos diferentes servidores no mesmo conjunto de disponibilidade ou em diferentes zonas de disponibilidade. As zonas de disponibilidade permitem-lhe atingir um SLA de 99,99%, enquanto os conjuntos de disponibilidade permitem-lhe atingir um SLA de 99,95% na região. As arquiteturas de amostras mostradas neste artigo são implantadas em duas zonas de disponibilidade.

Ao implementar uma aplicação utilizando a interligação cross-cloud, poderá continuar a utilizar um circuito ExpressRoute existente para ligar o seu ambiente Azure à sua rede no local. No entanto, necessita de um circuito ExpressRoute separado para a interligação com o OCI do que aquele que está ligado à sua rede no local.

## <a name="e-business-suite"></a>Suíte E-Business

Oracle E-Business Suite (EBS) é um conjunto de aplicações que incluem gestão da cadeia de fornecimento (SCM) e Gestão da Relação com o Cliente (CRM). Para tirar partido do portfólio de bases de dados gerido pela OCI, o EBS pode ser implementado utilizando a interligação entre o Microsoft Azure e o OCI. Nesta configuração, os níveis de apresentação e aplicação funcionam em Azure e no nível de base de dados em OCI, como ilustrado no seguinte diagrama de arquitetura (Figura 1).

![Arquitetura transversal e-business suite](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Figura 1: E-Business Suite arquitetura cross-cloud* 

Nesta arquitetura, a rede virtual em Azure está ligada a uma rede de nuvem virtual em OCI utilizando a interligação cross-cloud. O nível de aplicação está criado em Azure, enquanto a base de dados está configurada no OCI. Recomenda-se a colocação de cada componente na sua própria sub-rede com grupos de segurança de rede para permitir o tráfego apenas a partir de subredes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente em Azure com bases de dados oráculo altamente disponíveis configuradas usando a Oracle Data Guard em duas zonas de disponibilidade numa região. O diagrama seguinte (Figura 2) é um exemplo deste padrão arquitetónico:

![Arquitetura só e-Business Suite Azure](media/oracle-oci-applications/ebs-arch-azure.png)

*Figura 2: Arquitetura só e-Business Suite Azure*

As seguintes secções descrevem os diferentes componentes a um nível elevado.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Nível de aplicação (médio)

O nível de aplicação está isolado na sua própria sub-rede. Existem múltiplas máquinas virtuais configuradas para a tolerância à falha e gestão fácil de patch. Estes VMs podem ser apoiados por armazenamento partilhado, que é oferecido por Ficheiros Azure NetApp e Ultra SSDs. Esta configuração permite uma implementação mais fácil de patches sem tempo de inatividade. As máquinas do nível de aplicação devem ser encabeçadas por um equilibrista de carga pública, de modo a que os pedidos ao nível de aplicação EBS sejam processados mesmo que uma máquina do nível esteja offline devido a uma falha.

### <a name="load-balancer"></a>Load balancer

Um equilibrador de carga Azure permite-lhe distribuir tráfego em várias instâncias da sua carga de trabalho para garantir uma elevada disponibilidade. Neste caso, é criado um equilibrista de carga pública, uma vez que os utilizadores podem aceder à aplicação EBS pela web. O equilibrador de carga distribui a carga a ambas as máquinas no nível médio. Para maior segurança, permita o tráfego apenas dos utilizadores que acedam ao sistema a partir da sua rede corporativa utilizando uma VPN ou ExpressRoute site-to-site e grupos de segurança de rede.

### <a name="database-tier"></a>Nível de base de dados

Este nível acolhe a base de dados oracle e é separado na sua própria sub-rede. Recomenda-se adicionar grupos de segurança de rede que só permitem o tráfego do nível de aplicação para o nível de base de dados na porta de base de dados 1521 específica da Oracle.

A Microsoft e a Oracle recomendam uma configuração de alta disponibilidade. A elevada disponibilidade em Azure pode ser alcançada através da criação de duas bases de dados Oracle em duas zonas de disponibilidade com a Oracle Data Guard, ou utilizando o Oracle Database Exadata Cloud Service em OCI. Ao utilizar o Oracle Database Exadata Cloud Service, a sua base de dados está implantada em duas subredes. Também pode configurar a Oracle Database em VMs em OCI em dois domínios de disponibilidade com a Oracle Data Guard.


### <a name="identity-tier"></a>Nível de identidade

O nível de identidade contém o EBS Asserter VM. A EBS Asserter permite sincronizar identidades do Oracle Identity Cloud Service (IDCS) e da Azure AD. O Asserter EBS é necessário porque a EBS não suporta protocolos de inscrição simples como o SAML 2.0 ou o OpenID Connect. O EBS Asserter consome o token de ligação OpenID (gerado pelo IDCS), valida-o e cria uma sessão para o utilizador em EBS. 

Embora esta arquitetura mostre a integração do IDCS, o acesso unificado da Azure AD e o único sign-on também podem ser ativados com o Oracle Access Manager com o Diretório de Internet oracle ou o Diretório Unificado oracle. Para mais informações, consulte os livros brancos sobre a implantação do [Oracle EBS com integração iDCS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) ou implantação do [Oracle EBS com integração oam](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Para uma elevada disponibilidade, recomenda-se que implemente servidores redundantes do Asserter EBS em várias zonas de disponibilidade com um equilibrista de carga à sua frente.

Uma vez instalada a sua infraestrutura, a E-Business Suite pode ser instalada seguindo o guia de instalação fornecido pela Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

A Oracle's JD Edwards EnterpriseOne é um conjunto integrado de aplicações de software abrangente de planeamento de recursos empresariais. É uma aplicação multi-camadas que pode ser configurada com um backend de base de dados Oracle ou SQL Server. Esta secção discute detalhes sobre a implementação da JD Edwards EnterpriseOne com uma base de dados oracle back-end, quer em OCI quer em Azure.

Na seguinte arquitetura recomendada (Figura 3), a administração, apresentação e níveis médios são implantados na rede virtual em Azure. A base de dados está implantada numa rede virtual de nuvem em OCI.

Tal como acontece com a E-Business Suite, pode criar um bastião opcional para fins administrativos seguros. Utilize o anfitrião do bastião VM como um servidor de salto para aceder às instâncias de aplicação e base de dados.

![JD Edwards EnterpriseOne arquitetura cross-cloud](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Figura 3: JD Edwards EnterpriseOne arquitetura cross-cloud*

Nesta arquitetura, a rede virtual em Azure está ligada à rede de nuvem virtual em OCI utilizando a interligação cross-cloud. O nível de aplicação está criado em Azure, enquanto a base de dados está configurada no OCI. Recomenda-se a colocação de cada componente na sua própria sub-rede com grupos de segurança de rede para permitir o tráfego apenas a partir de subredes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente em Azure com bases de dados oráculo altamente disponíveis configuradas usando a Oracle Data Guard em duas zonas de disponibilidade numa região. O diagrama seguinte (Figura 4) é um exemplo deste padrão arquitetónico:

![Arquitetura só jD Edwards EnterpriseOne Azure](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Figura 4: Arquitetura só jD Edwards EnterpriseOne Azure*

As seguintes secções descrevem os diferentes componentes a um nível elevado.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Nível administrativo

Como o nome sugere, este nível é utilizado para tarefas administrativas. Pode esculpir uma sub-rede separada para o nível administrativo. Os serviços e servidores deste nível são usados principalmente para a instalação e administração da aplicação. Por isso, casos únicos destes servidores são suficientes. Não são necessários casos redundantes para a elevada disponibilidade da sua aplicação.

Os componentes deste nível são os seguintes:
    
 - **Servidor** de fornecimento - Este servidor é utilizado para a implementação de ponta a ponta dos diferentes componentes da aplicação. Comunica com as instâncias nos outros níveis, incluindo as instâncias no nível de base de dados, sobre a porta 22. Acolhe a Consola de Gestor de Servidores da JD Edwards EnterpriseOne.
 - **Servidor de implementação** - Este servidor é principalmente necessário para a instalação de JD Edwards EnterpriseOne. Durante o processo de instalação, este servidor funciona como o repositório central para ficheiros e pacotes de instalação necessários. O software é distribuído ou implantado para outros servidores e clientes deste servidor.
 - **Cliente de desenvolvimento** - Este servidor contém componentes que funcionam num navegador web, bem como aplicações nativas.

### <a name="presentation-tier"></a>Camada física de apresentação

Este nível contém vários componentes, tais como Serviços de Interface de Aplicação (AIS), Estrutura de Desenvolvimento de Aplicações (ADF) e Servidores de Aplicações Java (JAS). Os servidores deste nível comunicam com os servidores no nível médio. São encabeçados por um equilibrista de carga que encaminha o tráfego para o servidor necessário com base no número da porta e URL em que o tráfego é recebido. Recomenda-se que implemente várias instâncias de cada tipo de servidor para uma elevada disponibilidade.

Seguem-se os componentes deste nível:
    
- Serviços de Interface de **Aplicação (AIS)** - O servidor AIS fornece a interface de comunicação entre as aplicações móveis JD Edwards EnterpriseOne e a JD Edwards EnterpriseOne.
- **Java Application Server (JAS)** - O JAS recebe pedidos do equilibrador de carga e passa-o para o nível médio para executar tarefas complicadas. O JAS tem a capacidade de executar uma lógica de negócio simples.
- **BI Publisher Server (BIP)** - Este servidor apresenta relatórios com base nos dados recolhidos pela aplicação JD Edwards EnterpriseOne. Pode projetar e controlar a forma como o relatório apresenta os dados com base em diferentes modelos.
- **Business Services Server (BSS)** - O BSS permite o intercâmbio de informações e interoperabilidade com outras aplicações da Oracle.
- Servidor de Eventos em **Tempo Real (RTE)** - O Servidor RTE permite-lhe configurar notificações a sistemas externos sobre transações que ocorrem no sistema JDE EnterpriseOne. Utiliza um modelo de subscritor e permite que sistemas de terceiros subscrevam eventos. Para carregar pedidos de equilíbrio para ambos os servidores RTE, certifique-se de que os servidores estão num cluster.
- **Application Development Framework (ADF) Server** - O servidor ADF é usado para executar aplicações JD Edwards EnterpriseOne desenvolvidas com a Oracle ADF. Isto é implantado num servidor Oracle WebLogic com tempo de execução ADF.

### <a name="middle-tier"></a>Nível médio

O nível médio contém o servidor lógico e o servidor de lote. Neste caso, ambos os servidores estão instalados na mesma máquina virtual. No entanto, para cenários de produção, recomenda-se que implemente servidorlógico lógico e servidor de lote em servidores separados. Vários servidores são implantados no nível médio em duas zonas de disponibilidade para uma maior disponibilidade. Deve ser criado um balancedor de carga Azure e estes servidores devem ser colocados no seu pool de backend para garantir que ambos os servidores estão ativos e processam pedidos.

Os servidores do nível médio recebem pedidos dos servidores apenas no nível de apresentação e do equilibrador de carga público. As regras do grupo de segurança da rede devem ser criadas para negar o tráfego de qualquer endereço que não seja a subnet de nível de apresentação e o equilibrador de carga. Também pode ser criada uma regra nsg para permitir o tráfego no porto 22 do anfitrião do bastião para fins de gestão. Poderá utilizar o equilibrador de carga público para carregar pedidos de equilíbrio entre os VMs no nível médio.

Os dois componentes seguintes estão no nível médio:

- **Servidor lógico** - Conter a lógica do negócio ou funções empresariais.
- **Servidor de lote** - Usado para processamento de lote

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

A suíte de aplicações PeopleSoft da Oracle contém software para recursos humanos e gestão financeira. O conjunto de aplicações é multi-tiered e as aplicações incluem sistemas de gestão de recursos humanos (HRMS), gestão de relacionamento com o cliente (CRM), gestão financeira e de cadeia de fornecimento (FSCM) e gestão de desempenho empresarial (EPM).

Recomenda-se que cada nível da suíte de software seja implantado na sua própria sub-rede. Uma base de dados Oracle ou Microsoft SQL Server é necessária como base de dados de backend para a aplicação. Esta secção discute detalhes sobre a implementação do PeopleSoft com um backend de base de dados Oracle.

Segue-se uma arquitetura canónica para a implantação da suíte de aplicação PeopleSoft numa arquitetura transversal (Figura 5).

![PeopleSoft arquitetura cross-cloud](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Figura 5: Arquitetura de nuvem cruzada PeopleSoft*

Nesta arquitetura de amostra, a rede virtual em Azure está ligada à rede de nuvem virtual em OCI utilizando a interligação de nuvem cruzada. O nível de aplicação está criado em Azure, enquanto a base de dados está configurada no OCI. Recomenda-se a colocação de cada componente na sua própria sub-rede com grupos de segurança de rede para permitir o tráfego apenas a partir de subredes específicas em portas específicas.

A arquitetura também pode ser adaptada para implantação inteiramente em Azure com bases de dados oráculo altamente disponíveis configuradas usando a Oracle Data Guard em duas zonas de disponibilidade numa região. O diagrama seguinte (Figura 6) é um exemplo deste padrão arquitetónico:

![Arquitetura só para o PeopleSoft Azure](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Figura 6: Arquitetura só para o PeopleSoft Azure*

As seguintes secções descrevem os diferentes componentes a um nível elevado.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Camada física da aplicação

O nível de aplicação contém instâncias dos servidores de aplicações PeopleSoft, servidores web PeopleSoft, pesquisa elástica e Programador de Processos PeopleSoft. Um balancedor de carga Azure é criado para aceitar pedidos de utilizadores que são encaminhados para o servidor apropriado no nível de aplicação.

Para uma elevada disponibilidade, considere configurar casos redundantes de cada servidor no nível de aplicação em diferentes zonas de disponibilidade. O equilibrador de carga Azure pode ser configurado com várias piscinas traseiras para direcionar cada pedido para o servidor certo.

### <a name="peopletools-client"></a>Cliente PeopleTools

O Cliente PeopleTools é utilizado para realizar atividades administrativas, tais como desenvolvimento, migração e upgrade. Como o Cliente PeopleTools não é necessário para alcançar uma elevada disponibilidade da sua aplicação, não são necessários servidores redundantes do Cliente PeopleTools.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Passos seguintes

Utilize [scripts Terraform](https://github.com/microsoft/azure-oracle) para configurar aplicações Oracle em Azure e estabelecer conectividade cross-cloud com OCI.

Para mais informações e documentos brancos sobre o OCI, consulte a documentação da [Oracle Cloud.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
