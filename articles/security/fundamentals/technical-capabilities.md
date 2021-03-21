---
title: Capacidades técnicas de segurança em Azure - Microsoft Azure
description: Introdução aos serviços de segurança em Azure que o ajudam a proteger dados, recursos e aplicações na nuvem.
services: security
author: TerryLanfear
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 02/04/2021
ms.author: terrylan
ms.openlocfilehash: 00a44bd0a6176270e70eca030fcfada46effe3a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735782"
---
# <a name="azure-security-technical-capabilities"></a>Capacidades técnicas da segurança do Azure
Este artigo fornece uma introdução aos serviços de segurança em Azure que o ajudam a proteger os seus dados, recursos e aplicações na nuvem e a atender às necessidades de segurança do seu negócio.

## <a name="azure-platform"></a>Plataforma Azure

[O Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) é uma plataforma na nuvem composta por infraestruturas e serviços de aplicações, com serviços de dados integrados e análises avançadas, e ferramentas e serviços de desenvolvimento, alojados nos centros públicos de dados de nuvem da Microsoft. Os clientes usam o Azure para muitas capacidades e cenários diferentes, desde computação básica, networking e armazenamento, até serviços de aplicações móveis e web, até cenários de nuvem completa como Internet das Coisas, e podem ser usados com tecnologias de código aberto, e implantados como nuvem híbrida ou hospedados dentro do datacenter de um cliente. O Azure fornece tecnologia de nuvem como blocos de construção para ajudar as empresas a poupar custos, inovar rapidamente e gerir os sistemas de forma proativa. Quando você baseia ou migra os ativos de TI para um fornecedor de nuvem, você está confiando nas habilidades dessa organização para proteger suas aplicações e dados com os serviços e os controlos que eles fornecem para gerir a segurança dos seus ativos baseados na nuvem.

O Microsoft Azure é o único fornecedor de computação em nuvem que oferece um seguro, plataforma de aplicação consistente e infraestrutura-como-serviço para as equipas trabalharem dentro das suas diferentes habilidades em nuvem e níveis de complexidade do projeto, com serviços de dados integrados e análises que descobrem inteligência a partir de dados onde quer que ela exista, tanto através da Microsoft como das plataformas não-Microsoft, quadros e ferramentas abertos, fornecendo escolha para integrar a nuvem com as instalações, bem como implantando serviços de nuvem Azure dentro de datacenters nas premissas. Como parte da Microsoft Trusted Cloud, os clientes confiam no Azure para a segurança líder do setor, fiabilidade, conformidade, privacidade, e a vasta rede de pessoas, parceiros e processos para apoiar organizações na nuvem.

Com o Microsoft Azure, pode:

- Acelere a inovação com a nuvem.

- As decisões do negócio de energia & aplicações com insights.

- Construa livremente e desloque-se em qualquer lugar.

- Proteger os seus negócios.

## <a name="security-technical-capabilities-to-fulfill-your-responsibility"></a>Capacidades técnicas de segurança para cumprir a sua responsabilidade

A Microsoft Azure fornece serviços que o ajudam a satisfazer as suas necessidades de segurança, privacidade e conformidade. A imagem a seguir ajuda a explicar vários serviços Azure disponíveis para construir uma infraestrutura de aplicação segura e conforme com base nos padrões da indústria.

![Capacidades técnicas de segurança disponíveis- Grande imagem](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Gerir e controlar a identidade e o acesso ao utilizador

O Azure ajuda-o a proteger as informações empresariais e pessoais, permitindo-lhe gerir identidades e credenciais de utilizador e controlar o acesso.

### <a name="azure-active-directory"></a>Azure Active Directory

As soluções de gestão de identidade e acesso da Microsoft ajudam a proteger o acesso a aplicações e recursos em todo o datacenter corporativo e na nuvem, permitindo níveis adicionais de validação, tais como a autenticação de vários fatores e políticas de Acesso Condicional. A monitorização de atividades suspeitas através de relatórios de segurança avançada, de auditorias e de alertas ajuda a mitigar potenciais problemas de segurança. [O Azure Ative Directory Premium](../../active-directory/fundamentals/active-directory-whatis.md) fornece um único sign-on a milhares de aplicações na nuvem e acesso a aplicações web que executa no local.

Os benefícios de segurança do Azure Ative Directory (Azure AD) incluem a capacidade de:

- Crie e faça a gestão de uma única identidade para cada utilizador em toda a sua empresa híbrida, ao manter os utilizadores, os grupos e os dispositivos sincronizados.

- Forneça acesso único às suas aplicações, incluindo milhares de aplicações SaaS pré-integradas.

- Ativar a segurança do acesso a aplicações mediante a aplicação da Multi-Factor Authentication baseada em regras, tanto nas aplicações no local, como na cloud.

- Providenciar acesso remoto seguro a aplicações web no local através do Azure AD Application Proxy.

O [portal Azure Ative Directory](https://aad.portal.azure.com/) está disponível como parte do portal Azure. A partir deste painel, você pode obter uma visão geral do estado da sua organização, e gerir facilmente o diretório, utilizadores ou acesso à aplicação.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

Seguem-se as capacidades de gestão de identidade do Azure:

- Início de sessão único

- Autenticação multifator

- Relatórios de monitorização de segurança, alertas e aprendizagem automática

- Gestão da identidade e do acesso ao consumidor

- Registo de dispositivo

- Privileged Identity Management

- Proteção de identidade

#### <a name="single-sign-on"></a>Início de sessão único

[Um único sinal de sso significa](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) poder aceder a todas as aplicações e recursos que precisa para fazer negócios, fazendo a sessão apenas uma vez utilizando uma única conta de utilizador. Uma vez assinado, pode aceder a todas as aplicações de que necessita sem ser obrigado a autenticar (por exemplo, digitar uma palavra-passe) uma segunda vez.

Muitas organizações confiam no software como aplicações de serviço (SaaS) como a Microsoft 365, Box e Salesforce para a produtividade do utilizador final. Historicamente, o pessoal de TI precisava de criar e atualizar individualmente as contas de utilizador em cada aplicação SaaS, e os utilizadores tinham de se lembrar de uma palavra-passe para cada aplicação SaaS.

[O Azure AD estende o Ative Directory para o interior da nuvem,](../../active-directory/manage-apps/what-is-single-sign-on.md)permitindo que os utilizadores utilizem a sua conta organizacional primária para não só iniciar em seus dispositivos e recursos da empresa ligados ao domínio, mas também todas as aplicações web e SaaS necessárias para o seu trabalho.

Não só os utilizadores não têm de gerir vários conjuntos de nomes de utilizador e palavras-passe, como o acesso à aplicação pode ser automaticamente aprovisionado ou desprovisionado com base em grupos organizacionais e no seu estatuto de colaborador. [O Azure AD introduz controlos de governação](../../active-directory/manage-apps/view-applications-portal.md) de segurança e acesso que lhe permitem gerir centralmente o acesso dos utilizadores através das aplicações SaaS.

#### <a name="multi-factor-authentication"></a>Autenticação multifator

[A Azure AD Multi-Factor Authentication (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) é um método de autenticação que requer a utilização de mais de um método de verificação e adiciona uma segunda camada crítica de segurança às entradas e transações do utilizador. [O MFA ajuda a salvaguardar](../../active-directory/authentication/concept-mfa-howitworks.md) o acesso a dados e aplicações ao mesmo tempo que satisfaz a procura do utilizador por um processo simples de inscrição. Fornece autenticação forte através de uma série de opções de verificação — chamada telefónica, mensagem de texto ou código de verificação ou verificação de aplicações móveis e fichas OAuth de terceiros.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Relatórios de monitorização de segurança, alertas e aprendizagem automática

Monitorização de segurança e alertas e relatórios baseados em aprendizagem automática que identificam padrões de acesso inconsistentes podem ajudá-lo a proteger o seu negócio. Você pode usar os relatórios de acesso e utilização do Azure Ative Directory para ganhar visibilidade na integridade e segurança do diretório da sua organização. Com esta informação, um administrador de diretório pode determinar melhor onde podem estar os riscos de segurança possíveis para que possam planear adequadamente esses riscos.

No portal Azure ou através do [portal Azure Ative Directory,](https://aad.portal.azure.com/) [os relatórios](../../active-directory/reports-monitoring/overview-reports.md) são categorizados das seguintes formas:

- Relatórios de anomalias – contenham sinais em eventos que achamos ser anómalos. O nosso objetivo é sensibilizar para essa atividade e permitir que possa decidir se um evento é suspeito.

- Relatórios integrados de aplicações – forneça informações sobre como as aplicações em nuvem estão a ser usadas na sua organização. O Azure Ative Directory oferece integração com milhares de aplicações em nuvem.

- Relatórios de erro – indicam erros que podem ocorrer quando o provisionamento de contas a aplicações externas.

- Relatórios específicos do utilizador – mostrar o dispositivo e assinar dados de atividade para um utilizador específico.

- Registos de atividade – contêm um registo de todos os eventos auditados nas últimas 24 horas, nos últimos 7 dias, ou nos últimos 30 dias, e alterações de atividade em grupo, e atividade de reset e registo de passwords.

#### <a name="consumer-identity-and-access-management"></a>Gestão da identidade e do acesso ao consumidor

[O Azure Ative Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gestão de identidades altamente disponível, global e identitário para aplicações viradas para o consumidor que escala para centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os consumidores podem iniciar sessão em todas as suas aplicações através de experiências personalizáveis, utilizando as suas contas de redes sociais existentes ou criando novas credenciais.

No passado, os programadores de aplicações que quisessem [inscrever-se e inscrever-se nos consumidores nas](../../active-directory-b2c/overview.md) suas aplicações teriam escrito o seu próprio código. E teriam de utilizar sistemas ou bases de dados no local para armazenar os nomes de utilizador e as palavras-passe. O Azure Ative Directory B2C oferece à sua organização uma melhor forma de integrar a gestão da identidade do consumidor em aplicações com a ajuda de uma plataforma segura e baseada em padrões e um grande conjunto de políticas extensíveis.

Quando utiliza o Azure Ative Directory B2C, os seus consumidores podem inscrever-se nas suas aplicações utilizando as suas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de e-mail e palavra-passe, ou nome de utilizador e senha).

#### <a name="device-registration"></a>Registo de dispositivo

[O registo de dispositivos Azure AD](../../active-directory/devices/overview.md) é a base para cenários de [acesso condicional](../../active-directory/devices/overview.md) baseados no dispositivo. Quando um dispositivo está registado, o registo do dispositivo Azure AD fornece ao dispositivo uma identidade que é utilizada para autenticar o dispositivo quando o utilizador assina. O dispositivo autenticado, e os atributos do dispositivo, podem então ser utilizados para impor políticas de Acesso Condicional para aplicações que estejam hospedadas na nuvem e no local.

Quando combinado com uma solução [de gestão de dispositivos móveis (MDM),](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) como o Intune, os atributos do dispositivo no Azure Ative Directory são atualizados com informações adicionais sobre o dispositivo. Isto permite-lhe criar regras de Acesso Condicional que impõem o acesso dos dispositivos para cumprir os seus padrões de segurança e conformidade.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[A Azure Ative Directory (AD) Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) permite-lhe gerir, controlar e monitorizar as suas identidades privilegiadas e aceder a recursos em Azure AD, bem como outros serviços online da Microsoft como o Microsoft 365 ou o Microsoft Intune.

Por vezes, os utilizadores precisam de realizar operações privilegiadas em recursos Azure ou Microsoft 365, ou outras aplicações SaaS. Isto muitas vezes significa que as organizações têm de lhes dar acesso privilegiado permanente em Azure AD. Este é um risco crescente de segurança para os recursos hospedados na nuvem porque as organizações não conseguem monitorizar suficientemente o que esses utilizadores estão a fazer com os seus privilégios administrativos. Além disso, se uma conta de utilizador com acesso privilegiado estiver comprometida, essa violação pode afetar a sua segurança total na nuvem. A Azure AD Gestão de Identidade Privilegiada ajuda a resolver este risco.

A Azure AD Gestão de Identidade Privilegiada permite-lhe:

- Ver quais os utilizadores que são administradores AD Azure

- Ativar a pedido, acesso administrativo "just in time" aos Serviços Online do Microsoft como o Microsoft 365 e o Intune

- Obtenha relatórios sobre o histórico de acesso do administrador e alterações nas atribuições de administradores

- Receba alertas sobre o acesso a um papel privilegiado

#### <a name="identity-protection"></a>Proteção de identidade

[A Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) é um serviço de segurança que fornece uma visão consolidada de deteções de riscos e potenciais vulnerabilidades que afetam as identidades da sua organização. A Proteção de Identidade utiliza as capacidades de deteção de anomalias do Azure Ative Directory existentes (disponíveis através dos Relatórios de Atividade Anómalas da Azure AD), e introduz novos tipos de deteção de riscos que podem detetar anomalias em tempo real.

## <a name="secure-resource-access"></a>Acesso seguro a recursos

O controlo de acesso em Azure começa numa perspetiva de faturação. O proprietário de uma conta Azure, acedida através da visita ao [Centro de Conta Azure,](https://account.windowsazure.com/subscriptions)é o Administrador de Conta (AA). As subscrições são um recipiente para faturação, mas também funcionam como uma fronteira de segurança: cada subscrição tem um Administrador de Serviço (SA) que pode adicionar, remover e modificar os recursos Azure nessa subscrição utilizando o portal Azure. A SA padrão de uma nova subscrição é o AA, mas o AA pode alterar a SA no Centro de Conta Azure.

![Acesso seguro a recursos em Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

As assinaturas também têm uma associação com um diretório. O diretório define um conjunto de utilizadores. Estes podem ser utilizadores do trabalho ou da escola que criaram o diretório, ou podem ser utilizadores externos (isto é, Contas Microsoft). As subscrições são acessíveis por um subconjunto dos utilizadores de diretórios que foram designados como Administrador de Serviço (SA) ou Co-Administrator (CA); a única exceção é que, por razões antigas, as Contas Microsoft (anteriormente Windows Live ID) podem ser atribuídas como SA ou CA sem estarem presentes no diretório.

As empresas orientadas para a segurança devem concentrar-se em dar aos empregados as permissões exatas de que necessitam. Demasiadas permissões podem expor uma conta aos agressores. Poucas permissões significam que os empregados não conseguem fazer o seu trabalho de forma eficiente. [O controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) ajuda a resolver este problema oferecendo uma gestão de acesso de grãos finos para o Azure.

![Acesso seguro a recursos](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Com o RBAC do Azure, pode fazer a segregação de deveres na sua equipa e conceder aos utilizadores apenas a quantidade de acesso de que precisam para desempenhar as suas funções. Em vez de dar permissão ilimitada a todos na sua subscrição ou recursos Azure, só pode permitir certas ações. Por exemplo, use o Azure RBAC para permitir que um funcionário gere máquinas virtuais numa subscrição, enquanto outro pode gerir bases de dados SQL dentro da mesma subscrição.

![Acesso seguro a recursos usando Azure RBAC](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Segurança e encriptação de dados

Uma das chaves para a proteção de dados na nuvem é a contabilização dos possíveis estados em que os seus dados podem ocorrer e quais os controlos disponíveis para esse estado. Para a segurança de dados Azure e as melhores práticas de encriptação, as recomendações estão em torno dos seguintes estados de dados.

- Repouso: Isto inclui todos os objetos de armazenamento de informação, recipientes e tipos que existem estáticamente em meios físicos, seja em disco magnético ou ótico.
- Em trânsito: Quando os dados são transferidos entre componentes, locais ou programas, como através da rede, através de um autocarro de serviço (de instalações para nuvem e vice-versa, incluindo ligações híbridas como o ExpressRoute), ou durante um processo de entrada/saída, é considerado como estando em movimento.

### <a name="encryption-at-rest"></a>Encriptação de dados inativos

A encriptação em repouso é discutida em detalhe na [Encriptação de Dados Azure-at-Rest](encryption-atrest.md).

### <a name="encryption-in-transit"></a>Encriptação em trânsito

A proteção dos dados em trânsito deve ser parte essencial da sua estratégia de proteção de dados. Uma vez que os dados estão a mover-se de muitos locais, a recomendação geral é que utilize sempre protocolos SSL/TLS para trocar dados em diferentes locais. Em algumas circunstâncias, você pode querer isolar todo o canal de comunicação entre o seu local e infraestrutura em nuvem usando uma rede privada virtual (VPN).

Para os dados que se deslocam entre a sua infraestrutura no local e a Azure, deve considerar as salvaguardas adequadas, tais como HTTPS ou VPN.

Para as organizações que precisam de garantir o acesso de várias estações de trabalho localizadas no local para Azure, utilize [a VPN site-to-site da Azure.](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)

Para as organizações que precisam de garantir o acesso de uma estação de trabalho localizada no local para Azure, utilize [a VPN Ponto-a-Local](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Conjuntos de dados maiores podem ser movidos sobre uma ligação WAN dedicada de alta velocidade, como [o ExpressRoute.](https://azure.microsoft.com/services/expressroute/) Se optar por utilizar o ExpressRoute, também pode encriptar os dados ao nível da aplicação utilizando [SSL/TLS](https://web.archive.org/web/20150221085231/http://support.microsoft.com:80/kb/257591) ou outros protocolos para uma proteção adicional.

Se estiver a interagir com o Azure Storage através do portal Azure, todas as transações ocorrem através de HTTPS. [A API de armazenamento](/rest/api/storageservices/) REST sobre HTTPS também pode ser usada para interagir com [o Azure Storage](https://azure.microsoft.com/services/storage/) e a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

As organizações que não protegem os dados em trânsito são mais suscetíveis a [ataques man-in-the-middle](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14)), escutas e [sequestros](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14))de sessão. Esses ataques podem ser o primeiro passo para obter acesso a dados confidenciais.

Você pode saber mais sobre a opção Azure VPN lendo o artigo [Planeamento e design para VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="enforce-file-level-data-encryption"></a>Impor encriptação de dados de nível de ficheiro

[O Azure RMS](/azure/information-protection/what-is-azure-rms) utiliza políticas de encriptação, identidade e autorização para ajudar a proteger os seus ficheiros e e-mails. O Azure RMS funciona em vários dispositivos - telefones, tablets e Computadores protegendo tanto dentro da sua organização como fora da sua organização. Esta capacidade é possível porque o Azure RMS adiciona um nível de proteção que permanece com os dados, mesmo quando deixa os limites da sua organização.

Quando utiliza o Azure RMS para proteger os seus ficheiros, está a utilizar a criptografia padrão da indústria com o apoio total do [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf). Quando utiliza o Azure RMS para proteção de dados, tem a garantia de que a proteção permanece com o ficheiro, mesmo que seja copiado para armazenamento que não esteja sob o controlo de TI, como um serviço de armazenamento em nuvem. O mesmo ocorre para ficheiros partilhados via e-mail, o ficheiro é protegido como anexo a uma mensagem de correio eletrónico, com instruções sobre como abrir o anexo protegido.
Ao planear a adoção do Azure RMS recomendamos o seguinte:

- Instale a [aplicação de partilha RMS](/azure/information-protection/rms-client/sharing-app-windows). Esta aplicação integra-se com as aplicações do Office, instalando um add-in do Office para que os utilizadores possam proteger facilmente os ficheiros diretamente.

- Configure aplicações e serviços para apoiar o Azure RMS

- Crie [modelos personalizados](/azure/information-protection/configure-policy-templates) que reflitam os seus requisitos de negócio. Por exemplo: um modelo para dados ultrassecretos que devem ser aplicados em todos os e-mails relacionados com alta-secret.

As organizações que são fracas na classificação de [dados](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e na proteção de ficheiros podem ser mais suscetíveis a fugas de dados. Sem a proteção adequada dos ficheiros, as organizações não poderão obter informações sobre negócios, monitorizar abusos e impedir o acesso malicioso aos ficheiros.

> [!Note]
> Pode saber mais sobre o Azure RMS lendo o artigo [Começar com a Azure Rights Management](/azure/information-protection/requirements).

## <a name="secure-your-application"></a>Proteja a sua aplicação
Embora a Azure seja responsável por garantir a infraestrutura e plataforma em que a sua aplicação funciona, é da sua responsabilidade garantir a sua própria aplicação. Por outras palavras, precisa de desenvolver, implementar e gerir o código de aplicação e o conteúdo de forma segura. Sem isso, o código de aplicação ou conteúdo ainda pode ser vulnerável a ameaças.

### <a name="web-application-firewall"></a>Firewall de aplicações Web
[A firewall de aplicações web (WAF)](../../web-application-firewall/ag/ag-overview.md) é uma funcionalidade do [Application Gateway](../../application-gateway/overview.md) que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns.

A firewall de aplicações Web baseia-se nas regras dos [conjuntos de regras de núcleo OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9. Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em várias camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

Algumas das vulnerabilidades Web comuns contra as quais a firewall de aplicações Web protege incluem:

- Proteção contra injeção de SQL

- Proteção contra scripting entre sites

- Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros

- Proteção contra violações de protocolo HTTP

- Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta

- Prevenção de contra bots, crawlers e scanners

- Deteção de configurações comuns de aplicações (isto é, Apache, IIS, etc.)

> [!Note]
> Para obter uma lista mais detalhada das regras e das suas proteções consulte os [seguintes conjuntos de regras principais:](../../web-application-firewall/ag/ag-overview.md)

O Azure também fornece várias funcionalidades fáceis de usar para ajudar a garantir o tráfego de entrada e saída para a sua aplicação. O Azure também ajuda os clientes a proteger o seu código de aplicação, fornecendo funcionalidades fornecidas externamente para digitalizar a sua aplicação web para vulnerabilidades.

- [Configuração autenticação do Diretório Ativo Azure para a sua aplicação](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Proteja o tráfego da sua aplicação, permitindo a Segurança da Camada de Transporte (TLS/SSL) - HTTPS](../../app-service/configure-ssl-bindings.md)

  - [Forçar todo o tráfego de entrada sobre a ligação HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Permitir a segurança estrita dos transportes (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Restringir o acesso à sua aplicação através do endereço IP do cliente](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Restringir o acesso à sua app pelo comportamento do cliente - solicitar frequência e concordância](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Digitalize o seu código de aplicação web para vulnerabilidades usando a Verificação de Segurança de Tinfoil](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Configure a autenticação mútua TLS para exigir certificados de cliente para se conectar à sua aplicação web](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Configure um certificado de cliente para uso a partir da sua app para se conectar de forma segura a recursos externos](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Remova os cabeçalhos padrão do servidor para evitar que as ferramentas recolham impressões digitais na sua aplicação](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Ligue de forma segura a sua aplicação a recursos numa rede privada utilizando a VPN Point-To-Site](../../app-service/web-sites-integrate-with-vnet.md)

- [Ligue de forma segura a sua aplicação a recursos numa rede privada utilizando ligações híbridas](../../app-service/app-service-hybrid-connections.md)

O Azure App Service utiliza a mesma solução antimalware utilizada pelos Serviços de Nuvem Azure e máquinas virtuais. Para saber mais sobre isso, consulte a nossa [documentação antimalware.](antimalware.md)

## <a name="secure-your-network"></a>Proteja a sua rede
O Microsoft Azure inclui uma infraestrutura de rede robusta para suportar os requisitos de conectividade da sua aplicação e serviço. A conectividade da rede é possível entre os recursos localizados em Azure, entre os recursos alojados no local e a Azure, e de e para a Internet e Azure.

A [infraestrutura da rede Azure](/previous-versions/azure/virtual-machines/windows/infrastructure-example) permite-lhe ligar de forma segura os recursos Azure uns aos outros com [redes virtuais (VNets)](../../virtual-network/virtual-networks-overview.md). Um VNet é uma representação da sua própria rede na nuvem. Um VNet é um isolamento lógico da rede de nuvem Azure dedicada à sua subscrição. Pode ligar VNets às suas redes no local.

![Proteja a sua rede (proteja)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Se necessitar de um controlo básico de acesso ao nível da rede (com base no endereço IP e nos protocolos TCP ou UDP), então pode utilizar [grupos de segurança de rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md). Um Grupo de Segurança de Rede (NSG) é uma firewall de filtragem de pacotes básicos e imponente e permite-lhe controlar o acesso com base num [5-tuple](https://www.techopedia.com/definition/28190/5-tuple).

A rede Azure suporta a capacidade de personalizar o comportamento de encaminhamento para o tráfego de rede nas suas Redes Virtuais Azure. Pode fazê-lo configurando [rotas definidas](../../virtual-network/virtual-networks-udr-overview.md) pelo utilizador em Azure.

[O túnel forçado](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode utilizar para garantir que os seus serviços não estão autorizados a iniciar uma ligação a dispositivos na Internet.

O Azure suporta a conectividade de ligação DEDICADA WAN à sua rede de instalações e a uma Rede Virtual Azure com [o ExpressRoute.](../../expressroute/expressroute-introduction.md) A ligação entre a Azure e o seu site utiliza uma ligação dedicada que não passa pela Internet pública. Se a sua aplicação Azure estiver a funcionar em vários datacenters, pode utilizar [o Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) para encaminhar pedidos dos utilizadores de forma inteligente em todas as instâncias da aplicação. Também pode encaminhar o tráfego para serviços que não estão a funcionar em Azure se estiverem acessíveis a partir da Internet.

O Azure também suporta conectividade privada e segura aos seus recursos PaaS (por exemplo, Azure Storage e SQL Database) da sua Rede Virtual Azure com [Ligação Privada Azure](https://docs.microsoft.com/azure/private-link/private-link-overview). O recurso PaaS está mapeado para um [ponto final privado](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) na sua rede virtual. A ligação entre o ponto final privado na sua rede virtual e o seu recurso PaaS utiliza a rede de espinha dorsal da Microsoft e não passa pela Internet pública. Expor o seu serviço à internet pública já não é necessário. Também pode utilizar o Azure Private Link para aceder aos serviços de clientes e parceiros hospedados no Azure na sua rede virtual.  Além disso, o Azure Private Link permite-lhe criar o seu próprio [serviço de ligação privada](https://docs.microsoft.com/azure/private-link/private-link-service-overview) na sua rede virtual e entregá-lo aos seus clientes em particular nas suas redes virtuais. A configuração e consumo usando o Azure Private Link é consistente em todos os serviços Azure PaaS, propriedade do cliente e parceiros partilhados.

## <a name="virtual-machine-security"></a>Segurança da máquina virtual

[A Azure Virtual Machines](../../virtual-machines/index.yml) permite-lhe implementar uma vasta gama de soluções de computação de forma ágil. Com suporte para serviços Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Azure BizTalk, pode implementar qualquer carga de trabalho e qualquer idioma em quase qualquer sistema operativo.

Com o Azure, pode utilizar [software antimalware](antimalware.md) de fornecedores de segurança como Microsoft, Symantec, Trend Micro e Kaspersky para proteger as suas máquinas virtuais de ficheiros maliciosos, adware e outras ameaças.

O Microsoft Antimalware para Azure Cloud Services e Virtual Machines é uma capacidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares maliciosos. O Microsoft Antimalware fornece alertas configuráveis quando o software malicioso ou indesejado tenta instalar-se ou funcionar nos seus sistemas Azure.

[O Azure Backup](../../backup/backup-overview.md) é uma solução escalável que protege os dados da sua aplicação com investimento de capital zero e custos operacionais mínimos. Os erros de aplicação podem corromper os seus dados, e os erros humanos podem introduzir erros nas suas aplicações. Com o Azure Backup, as suas máquinas virtuais que executam o Windows e o Linux estão protegidas.

[A Azure Site Recovery](../../site-recovery/site-recovery-overview.md) ajuda a orquestrar a replicação, failover e a recuperação de cargas de trabalho e aplicações para que estejam disponíveis a partir de um local secundário se a sua localização primária diminuir.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Garantir o cumprimento: Lista de verificação de diligências dos serviços em nuvem

A Microsoft desenvolveu [a Lista de Verificação de Due Diligence dos Serviços de Nuvem](https://aka.ms/cloudchecklist.download) para ajudar as organizações a exercerem as devidas diligências, uma vez que consideram uma mudança para a nuvem. Fornece uma estrutura para uma organização de qualquer dimensão e tipo - empresas privadas e organizações do sector público, incluindo governos a todos os níveis e sem fins lucrativos - para identificar o seu próprio desempenho, serviço, gestão de dados e objetivos e requisitos de governação. Isto permite-lhes comparar as ofertas de diferentes fornecedores de serviços em nuvem, formando, em última análise, a base para um acordo de serviço na nuvem.

A lista de verificação fornece um quadro que alinha cláusula por cláusula com uma nova norma internacional para acordos de serviços na nuvem, ISO/IEC 19086. Esta norma oferece um conjunto unificado de considerações para as organizações para ajudá-las a tomar decisões sobre a adoção em nuvem, e criar um terreno comum para comparar ofertas de serviços em nuvem.

A lista de verificação promove uma mudança completamente verificada para a nuvem, fornecendo orientação estruturada e uma abordagem consistente e repetível para escolher um fornecedor de serviços na nuvem.

A adoção em nuvem já não é simplesmente uma decisão tecnológica. Como os requisitos da lista de verificação tocam em todos os aspetos de uma organização, eles servem para convocar todos os decisores internos chave - o CIO e CISO, bem como profissionais legais, de gestão de risco, compras e conformidade. Isto aumenta a eficiência do processo de tomada de decisão e as decisões fundamentais no raciocínio correto, reduzindo assim a probabilidade de imprevistos imprevistos à adoção.

Além disso, a lista de verificação:

- Expõe tópicos-chave de discussão para os decisores no início do processo de adoção em nuvem.

- Apoia discussões detalhadas sobre regulamentos e objetivos próprios da organização para a privacidade, informações pessoais e segurança de dados.

- Ajuda as organizações a identificar quaisquer problemas potenciais que possam afetar um projeto em nuvem.

- Fornece um conjunto consistente de perguntas, com os mesmos termos, definições, métricas e entregas para cada fornecedor, para simplificar o processo de comparação de ofertas de diferentes fornecedores de serviços em nuvem.

## <a name="azure-infrastructure-and-application-security-validation"></a>Validação de infraestruturas e aplicações da Azure

[A Azure Operational Security](operational-security.md) refere-se aos serviços, controlos e funcionalidades disponíveis para os utilizadores para proteger os seus dados, aplicações e outros ativos no Microsoft Azure.

![validação de segurança (detetar)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

O Azure Operational Security baseia-se numa estrutura que incorpora o conhecimento adquirido através de várias capacidades que são únicas para a Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center e uma profunda consciência do panorama da ameaça à cibersegurança.

### <a name="microsoft-azure-monitor"></a>Microsoft Azure Monitor

[O Azure Monitor](../../azure-monitor/index.yml) é a solução de gestão de TI para a nuvem híbrida. Utilizado sozinho ou para ampliar a implementação do System Center existente, os registos do Azure Monitor conferem-lhe a máxima flexibilidade e controlo para a gestão baseada na nuvem da sua infraestrutura.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Com o Azure Monitor, pode gerir qualquer instância em qualquer nuvem, incluindo no local, Azure, AWS, Windows Server, Linux, VMware e OpenStack, a um custo inferior ao das soluções competitivas. Construído para o mundo em nuvem, o Azure Monitor oferece uma nova abordagem para gerir a sua empresa que é a forma mais rápida e rentável de enfrentar novos desafios de negócio e acomodar novas cargas de trabalho, aplicações e ambientes em nuvem.

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Os registos do Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics) fornecem serviços de monitorização recolhendo dados de recursos geridos para um repositório central. Estes dados podem incluir dados de eventos ou de desempenho ou dados personalizados fornecidos através da API. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação.

![Registos do Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Este método permite-lhe consolidar dados a partir de uma variedade de fontes, para que possa combinar dados dos seus serviços Azure com o ambiente existente no local. Também separa claramente a recolha dos dados das ações tomadas em relação aos mesmos, para que todas as ações estejam disponíveis para todos os tipos de dados.

### <a name="azure-security-center"></a>Centro de Segurança do Azure

O [Centro de Segurança do Azure](../../security-center/security-center-introduction.md) ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

O Centro de Segurança analisa o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Uma lista de recomendações orienta-o no processo de configuração de controlos necessários.

Os exemplos incluem:

- Aprovisionamento de antimalware para ajudar a identificar e remover o software malicioso

- Configurar grupos e regras de segurança da rede para controlar o tráfego para os VMs

- Aprovisionamento de firewalls de aplicação Web para ajudar a proteger-se contra ataques que visam as suas Web Apps

- Implementação de atualizações do sistema em falta

- Abordagem de configurações de SO que não correspondam às linhas de base recomendadas

O Centro de Segurança recolhe, analisa e integra automaticamente os dados de registo a partir dos recursos do Azure, da rede e das soluções de parceiros, como os programas antimalware e as firewalls. Quando são detetadas ameaças, é criado um alerta de segurança. Os exemplos incluem a deteção de:

- VMs comprometidos comunicando com endereços IP maliciosos conhecidos

- Software maligno avançado detetado ao utilizar a comunicação de erros do Windows

- Ataques de força bruta contra VMs

- Alertas de segurança a partir de programas antimalware e firewalls integrados

### <a name="azure-monitor"></a>Monitor azul

[O Azure Monitor](../../azure-monitor/overview.md) fornece indicações sobre tipos específicos de recursos. Oferece visualização, consulta, encaminhamento, alerta, escala automática e automação em dados tanto da infraestrutura Azure (Log de Atividade) como de cada recurso Azure individual (Registos de Diagnóstico).

As aplicações em nuvem são complexas com muitas partes móveis. A monitorização fornece dados para garantir que a sua aplicação permanece em funcionamento em um estado saudável. Também o ajuda a evitar potenciais problemas ou resolver problemas.

![Diagrama que mostra que pode usar dados de monitorização para obter informações profundas sobre a sua aplicação.](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png)
Além disso, pode utilizar dados de monitorização para obter informações profundas sobre a sua aplicação. Esse conhecimento pode ajudá-lo a melhorar o desempenho da aplicação ou a manutenção, ou automatizar ações que de outra forma exigiriam uma intervenção manual.

A auditoria da segurança da sua rede é vital para detetar vulnerabilidades de rede e garantir o cumprimento do seu modelo de segurança de TI e governação regulamentar. Com a vista do Grupo de Segurança, pode recuperar as regras de segurança do Grupo de Segurança de Rede configuradas e as regras de segurança, bem como as regras de segurança eficazes. Com a lista de regras aplicadas, pode determinar as portas que estão abertas e a vulnerabilidade da rede SS.

### <a name="network-watcher"></a>Observador de rede

[O Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional que lhe permite monitorizar e diagnosticar condições a nível de rede dentro, e a partir de Azure. As ferramentas de diagnóstico e visualização da rede disponíveis com o Network Watcher ajudam-no a compreender, diagnosticar e a obter informações sobre a sua rede em Azure. Este serviço inclui captura de pacotes, próximo lúpulo, verificação do fluxo IP, vista do grupo de segurança, registos de fluxo NSG. A monitorização do nível de cenário proporciona uma visão final dos recursos da rede em contraste com a monitorização individual dos recursos de rede.

### <a name="storage-analytics"></a>Análise de armazenamento

[O Storage Analytics](/rest/api/storageservices/fileservices/storage-analytics) pode armazenar métricas que incluem estatísticas de transações agregadas e dados de capacidade sobre pedidos a um serviço de armazenamento. As transações são reportadas tanto ao nível da operação da API como ao nível do serviço de armazenamento, e a capacidade é reportada ao nível do serviço de armazenamento. Os dados das métricas podem ser usados para analisar o uso do serviço de armazenamento, diagnosticar problemas com pedidos feitos contra o serviço de armazenamento, e melhorar o desempenho das aplicações que usam um serviço.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço extensível de Gestão de Desempenho de Aplicações (APM) para desenvolvedores web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. Além de detetar automaticamente anomalias de desempenho, Inclui poderosas ferramentas de análise para ajudá-lo a diagnosticar problemas e a entender o que os utilizadores fazem com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade. Funciona para apps em uma grande variedade de plataformas, incluindo .NET, Node.js e Java EE, hospedados no local ou na nuvem. Integra-se com o seu processo DevOps e tem pontos de ligação para várias ferramentas de desenvolvimento.

Monitoriza:

- **Taxas de pedidos, tempos de resposta e taxas de falhas** - saiba quais são as páginas mais populares, em que alturas do dia e onde estão localizados os seus utilizadores. Veja que páginas têm o melhor desempenho. Se os tempos de resposta e as taxas de falhas aumentarem quando há mais pedidos, é possível que tenha um problema relativo a recursos.

- **Taxas de dependência, tempos de resposta e taxas de falhas** - veja se os serviços externos o estão a atrasar.

- **Exceções** - Analise as estatísticas agregadas, ou escolha instâncias específicas e fure o traço da pilha e os pedidos relacionados. São reportadas exceções do servidor e do browser.

- **Vistas de página e desempenho de carga** - reportados pelos browsers dos utilizadores.

- **Chamadas AJAX a partir de páginas web** - taxas, tempos de resposta e taxas de falha.

- **O utilizador e a sessão contam.**

- **Contadores de desempenho** dos computadores de servidor Windows ou Linux, como CPU, memória e utilização de rede.

- **Diagnóstico do anfitrião** do Docker ou do Azure.

- **Registos de rastreio de diagnóstico** da sua aplicação - para que possa correlacionar eventos de rastreio a pedidos.

- **Eventos e métricas personalizados** que você próprio escreve no código do cliente ou servidor, para acompanhar eventos de negócios como itens vendidos, ou jogos ganhos.

Normalmente, a infraestrutura da sua aplicação é composta por vários componentes, como uma máquina virtual, uma conta de armazenamento e uma rede virtual, ou uma aplicação Web, uma base de dados, um servidor de base de dados e serviços de terceiros. Não vê estes componentes como entidades separadas. Em vez disso, vê-os como partes relacionadas e interdependentes de uma única entidade. Deve implementá-los, geri-los e monitorizá-los como um grupo. [O Azure Resource Manager](../../azure-resource-manager/management/overview.md) permite-lhe trabalhar com os recursos na sua solução como grupo.

Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Utiliza um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, como de teste e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação.

**Vantagens da utilização do Resource Manager**

O Resource Manager oferece várias vantagens:

- Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

- Pode implementar repetidamente a solução durante todo o ciclo de vida de desenvolvimento e ter a confiança de que os recursos são implementados num estado consistente.

- Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

- Pode definir as dependências entre recursos, para que sejam implantados na ordem correta.

- Pode aplicar o controlo de acesso a todos os serviços do seu grupo de recursos porque o controlo de acesso baseado em funções Azure (Azure RBAC) está nativamente integrado na plataforma de gestão.

- Pode aplicar etiquetas a recursos para organizar logicamente todos os recursos na sua subscrição.

- Pode clarificar a faturação da sua organização visualizando os custos de um grupo de recursos partilhando a mesma etiqueta.

> [!Note]
> O Resource Manager proporciona uma nova forma de implementar e gerir as suas soluções. Se utilizou o modelo de implementação anterior e quer saber das alterações, consulte a [Implementação do Gestor de Recursos compreensivo e a implementação clássica](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-step"></a>Passo seguinte

O programa [Azure Security Benchmark](../benchmarks/introduction.md) inclui uma coleção de recomendações de segurança que pode usar para ajudar a proteger os serviços que utiliza no Azure.
