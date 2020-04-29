---
title: Capacidades técnicas de segurança no Azure - Microsoft Azure
description: Introdução a serviços de segurança no Azure que o ajudam a protegê-lo de dados, recursos e aplicações na nuvem.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2019
ms.author: TomSh
ms.openlocfilehash: 61afad1d9994fd703bd8df047d1861baddeae997
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76845355"
---
# <a name="azure-security-technical-capabilities"></a>Capacidades técnicas da segurança do Azure
Este artigo fornece uma introdução aos serviços de segurança em Azure que o ajudam a proteger os seus dados, recursos e aplicações na nuvem e a atender às necessidades de segurança do seu negócio.

## <a name="azure-platform"></a>Plataforma Azure

O [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) é uma plataforma cloud composta por serviços de infraestruturas e aplicações, com serviços de dados integrados e análises avançadas, e ferramentas e serviços de desenvolvedores, hospedados nos centros de dados públicos da Microsoft. Os clientes usam o Azure para muitas capacidades e cenários diferentes, desde a computação básica, networking e armazenamento, até serviços de aplicações móveis e web, até cenários de nuvem completa como Internet das Coisas, e podem ser usados com tecnologias de código aberto, e implementados como nuvem híbrida ou hospedados dentro do datacenter de um cliente. O Azure fornece tecnologia em nuvem como blocos de construção para ajudar as empresas a poupar custos, inovar rapidamente e gerir sistemas de forma proativa. Quando você constrói, ou migra ativos de TI para um fornecedor de nuvem, você está contando com as habilidades dessa organização para proteger suas aplicações e dados com os serviços e os controles que eles fornecem para gerir a segurança dos seus ativos baseados na nuvem.

O Microsoft Azure é o único fornecedor de computação em nuvem que oferece uma plataforma de aplicação segura e consistente e um serviço de infraestruturas como um serviço para as equipas trabalharem dentro das suas diferentes habilidades em nuvem e níveis de complexidade do projeto, com serviços de dados integrados e análises que descobrem inteligência a partir de dados onde quer que ela exista, tanto em plataformas microsoft como não-Microsoft, quadros abertos e ferramentas, proporcionando escolha para integrar a nuvem com as instalações, bem como implementar serviços de nuvem Azure dentro de instalações de dados. Como parte da Microsoft Trusted Cloud, os clientes confiam no Azure para a segurança líder do setor, fiabilidade, conformidade, privacidade e a vasta rede de pessoas, parceiros e processos para apoiar organizações na nuvem.

Com o Microsoft Azure, pode:

- Acelere a inovação com a nuvem.

- As decisões de negócios de poder & apps com insights.

- Construir livremente e implantar em qualquer lugar.

- Proteger os seus negócios.

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>Capacidades técnicas de segurança para cumprir a sua responsabilidade

O Microsoft Azure presta serviços que o ajudam a satisfazer as suas necessidades de segurança, privacidade e conformidade. A imagem seguinte ajuda a explicar vários serviços Azure disponíveis para construir uma infraestrutura de aplicação segura e conforme com base nos padrões do setor.

![Capacidades técnicas de segurança disponíveis - Grande imagem](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Gerir e controlar a identidade e o acesso ao utilizador

O Azure ajuda-o a proteger informações pessoais e empresariais, permitindo-lhe gerir as identidades e credenciais dos utilizadores e controlar o acesso.

### <a name="azure-active-directory"></a>Azure Active Directory

As soluções de gestão de identidade e acesso da Microsoft ajudam a proteger o acesso a aplicações e recursos através do datacenter corporativo e na nuvem, permitindo níveis adicionais de validação, tais como a autenticação de vários fatores e políticas de Acesso Condicional. A monitorização de atividades suspeitas através de relatórios de segurança avançada, de auditorias e de alertas ajuda a mitigar potenciais problemas de segurança. [O Azure Ative Directory Premium](../../active-directory/active-directory-whatis.md) fornece um único sinal para milhares de aplicações na nuvem e acesso a aplicações web que executa no local.

Os benefícios de segurança do Azure Ative Directory (Azure AD) incluem a capacidade de:

- Crie e faça a gestão de uma única identidade para cada utilizador em toda a sua empresa híbrida, ao manter os utilizadores, os grupos e os dispositivos sincronizados.

- Forneça acesso único às suas aplicações, incluindo milhares de aplicações SaaS pré-integradas.

- Ativar a segurança do acesso a aplicações mediante a aplicação da Multi-Factor Authentication baseada em regras, tanto nas aplicações no local, como na cloud.

- Provisão segura acesso remoto a aplicações web no local através do Proxy de Aplicação AD Azure.

O [portal Azure Ative Diretório](https://aad.portal.azure.com/) está disponível como parte do portal Azure. A partir deste dashboard, você pode obter uma visão geral do estado da sua organização, e gerir facilmente o diretório, utilizadores ou acesso à aplicação.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

Seguem-se as principais capacidades de gestão de identidade do Azure:

- Início de sessão único

- Multi-factor authentication

- Relatórios de monitorização de segurança, alertas e relatórios baseados em machine learning

- Identidade do consumidor e gestão de acesso

- Registo de dispositivo

- Privileged Identity Management

- Proteção de identidade

#### <a name="single-sign-on"></a>Início de sessão único

[Um único sinal (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) significa poder aceder a todas as aplicações e recursos necessários para fazer negócios, assinando apenas uma vez usando uma única conta de utilizador. Uma vez inscrito, pode aceder a todas as aplicações de que necessita sem ser obrigado a autenticar (por exemplo, digitar uma palavra-passe) uma segunda vez.

Muitas organizações confiam no software como um serviço (SaaS) aplicações como Office 365, Box e Salesforce para a produtividade do utilizador final. Historicamente, o pessoal de TI precisava de criar e atualizar individualmente as contas dos utilizadores em cada aplicação SaaS, e os utilizadores tinham de se lembrar de uma palavra-passe para cada aplicação SaaS.

[A Azure AD estende-se no local Ative Directory para a nuvem,](../../active-directory/manage-apps/what-is-single-sign-on.md)permitindo que os utilizadores utilizem a sua conta organizacional primária para não só assinar em dispositivos e recursos da empresa, mas também todas as aplicações web e SaaS necessárias para o seu trabalho.

Não só os utilizadores não têm de gerir vários conjuntos de nomes de utilizadores e palavras-passe, como o acesso à aplicação pode ser automaticamente provisionado ou desprovisionado com base em grupos organizacionais e no seu estatuto de funcionário. [A Azure AD introduz controlos](../../active-directory/active-directory-enterprise-apps-manage-sso.md) de governança de segurança e acesso que lhe permitem gerir centralmente o acesso dos utilizadores através das aplicações SaaS.

#### <a name="multi-factor-authentication"></a>Multi-factor authentication

[A Autenticação Multi-Factor (MFA) azure](../../active-directory/authentication/multi-factor-authentication.md) é um método de autenticação que requer a utilização de mais de um método de verificação e adiciona uma segunda camada crítica de segurança aos insines e transações do utilizador. [O MFA ajuda a salvaguardar](../../active-directory/authentication/concept-mfa-howitworks.md) o acesso a dados e aplicações, ao mesmo tempo que satisfaz a procura do utilizador por um processo simples de iniciar sessão. Fornece autenticação forte através de uma gama de opções de verificação : chamada telefónica, mensagem de texto ou código de notificação ou verificação de aplicações móveis e fichas oAuth de terceiros.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Relatórios de monitorização de segurança, alertas e relatórios baseados em machine learning

Monitorização de segurança e alertas e relatórios baseados em machine learning que identifiquem padrões de acesso inconsistentes podem ajudá-lo a proteger o seu negócio. Pode utilizar os relatórios de acesso e utilização do Azure Ative Directory para obter visibilidade na integridade e segurança do diretório da sua organização. Com esta informação, um administrador de diretório pode determinar melhor onde possíveis riscos de segurança podem estar para que possam planear adequadamente mitigar esses riscos.

No portal Azure ou através do [portal Azure Ative Diretório,](https://aad.portal.azure.com/) [os relatórios](../../active-directory/active-directory-reporting-azure-portal.md) são categorizados das seguintes formas:

- Relatórios de anomalias - contêm sinal em eventos que considerámos anómalos. O nosso objetivo é torná-lo consciente de tal atividade e permitir que você possa decidir se um evento é suspeito.

- Relatórios de aplicações integrados – forneçam informações sobre como as aplicações em nuvem estão a ser usadas na sua organização. O Azure Ative Directory oferece integração com milhares de aplicações na nuvem.

- Relatórios de erro – indicam erros que podem ocorrer ao fornecer contas a aplicações externas.

- Relatórios específicos do utilizador – dispositivo de visualização e registo de dados de atividade para um utilizador específico.

- Registos de atividade – contêm um registo de todos os eventos auditados nas últimas 24 horas, últimos 7 dias, ou durar 30 dias, e alterações na atividade do grupo, e reset e atividade de registo de passwords.

#### <a name="consumer-identity-and-access-management"></a>Identidade do consumidor e gestão de acesso

O [Azure Ative Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gestão de identidade altamente disponível, global e global, para aplicações viradas para o consumidor que escala para centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os consumidores podem iniciar sessão em todas as suas aplicações através de experiências personalizáveis, utilizando as suas contas de redes sociais existentes ou criando novas credenciais.

No passado, os desenvolvedores de aplicações que quisessem [inscrever-se e inscrever-se nos consumidores](../../active-directory-b2c/overview.md) nas suas aplicações teriam escrito o seu próprio código. E teriam de utilizar sistemas ou bases de dados no local para armazenar os nomes de utilizador e as palavras-passe. O Azure Ative Directory B2C oferece à sua organização uma melhor forma de integrar a gestão da identidade dos consumidores em aplicações com a ajuda de uma plataforma segura e baseada em padrões e um grande conjunto de políticas extensíveis.

Quando utiliza o Azure Ative Directory B2C, os seus consumidores podem inscrever-se nas suas aplicações utilizando as suas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de e-mail e palavra-passe, ou nome de utilizador e palavra-passe).

#### <a name="device-registration"></a>Registo de dispositivo

O [registo do dispositivo Azure AD](../../active-directory/devices/overview.md) é a base para cenários de [acesso condicional](../../active-directory/devices/overview.md) baseados em dispositivos. Quando um dispositivo está registado, o registo do dispositivo Azure AD fornece ao dispositivo uma identidade que é usada para autenticar o dispositivo quando o utilizador faz a entrada. O dispositivo autenticado, e os atributos do dispositivo, podem então ser utilizados para impor políticas de Acesso Condicional para aplicações que estejam alojadas na nuvem e no local.

Quando combinado com uma solução de gestão de [dispositivos móveis (MDM),](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) como o Intune, os atributos do dispositivo no Diretório Ativo Azure são atualizados com informações adicionais sobre o dispositivo. Isto permite-lhe criar regras de Acesso Condicional que impõem o acesso dos dispositivos para cumprir os seus padrões de segurança e conformidade.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

A [Azure Ative Directory (AD) Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) permite-lhe gerir, controlar e monitorizar as suas identidades privilegiadas e o acesso aos recursos em Azure AD, bem como outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Por vezes, os utilizadores precisam de realizar operações privilegiadas em recursos Azure ou Office 365, ou outras aplicações SaaS. Isto muitas vezes significa que as organizações têm de lhes dar acesso privilegiado permanente em Azure AD. Este é um risco crescente de segurança para os recursos hospedados na nuvem porque as organizações não conseguem monitorizar suficientemente o que esses utilizadores estão a fazer com os seus privilégios de administração. Além disso, se uma conta de utilizador com acesso privilegiado for comprometida, essa violação pode afetar a sua segurança total na nuvem. A Azure AD Privileged Identity Management ajuda a resolver este risco.

A Azure AD Privileged Identity Management permite-lhe:

- Veja quais os utilizadores que são administradores da Azure AD

- Ativar a pedido, acesso administrativo "mesmo a tempo" aos Serviços Online da Microsoft, como o Office 365 e o Intune

- Obtenha relatórios sobre o histórico de acesso do administrador e alterações nas atribuições de administradores

- Receba alertas sobre o acesso a um papel privilegiado

#### <a name="identity-protection"></a>Proteção de identidade

[A Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) é um serviço de segurança que fornece uma visão consolidada sobre deteções de risco e potenciais vulnerabilidades que afetam as identidades da sua organização. A Identity Protection utiliza as capacidades de deteção de anomalias existentes do Azure Ative Directory (disponíveis através dos Relatórios de Atividade Anómala da Azure AD), e introduz novos tipos de deteção de risco que podem detetar anomalias em tempo real.

## <a name="secure-resource-access"></a>Acesso seguro a recursos

O controlo de acesso em Azure começa numa perspetiva de faturação. O proprietário de uma conta Azure, acessada através da visita ao Centro de [Contas Azure,](https://account.windowsazure.com/subscriptions)é o Administrador de Conta (AA). As subscrições são um recipiente para faturação, mas também funcionam como um limite de segurança: cada subscrição tem um Administrador de Serviço (SA) que pode adicionar, remover e modificar os recursos do Azure nessa subscrição utilizando o portal Azure. A SA padrão de uma nova subscrição é a AA, mas a AA pode alterar a SA no Centro de Conta Azure.

![Acesso seguro a recursos em Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

As assinaturas também têm uma associação com um diretório. O diretório define um conjunto de utilizadores. Estes podem ser utilizadores do trabalho ou escola que criaram o diretório, ou podem ser utilizadores externos (isto é, Contas Microsoft). As subscrições são acessíveis por um subconjunto dos utilizadores de diretórios que tenham sido designados como Administrador de Serviço (SA) ou Coadministrador (CA); a única exceção é que, por razões antigas, as Contas Microsoft (anteriormente Windows Live ID) podem ser atribuídas como SA ou CA sem estarem presentes no diretório.

As empresas orientadas para a segurança devem concentrar-se em dar aos empregados as permissões exatas de que necessitam. Demasiadas permissões podem expor uma conta aos agressores. Poucas permissões significam que os empregados não conseguem fazer o seu trabalho de forma eficiente. [O Azure Role-Based Access Control (RBAC)](../../role-based-access-control/overview.md) ajuda a resolver este problema oferecendo uma gestão de acesso de grãos finos para o Azure.

![Acesso seguro a recursos](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Ao utilizar o RBAC, pode segregar funções na sua equipa e conceder apenas a quantidade de acesso a utilizadores que precisam para desempenhar as suas funções. Em vez de dar permissões sem restrições a todos na sua subscrição ou recursos Azure, só pode permitir certas ações. Por exemplo, utilize o RBAC para permitir que um funcionário gere as máquinas virtuais numa subscrição, enquanto outro pode gerir bases de dados SQL dentro da mesma subscrição.

![Acesso seguro a recursos em Azure (RBAC)](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Segurança e encriptação de dados

Uma das chaves para a proteção de dados na nuvem é a contabilização dos possíveis estados em que os seus dados podem ocorrer, e quais os controlos disponíveis para esse estado. Para as melhores práticas de segurança e encriptação de dados do Azure, as recomendações estão em torno dos seguintes estados de dados.

- Repouso: Isto inclui todos os objetos de armazenamento de informação, recipientes e tipos que existem estáticamente nos meios físicos, seja em disco magnético ou ótico.

- Em trânsito: Quando os dados são transferidos entre componentes, locais ou programas, como através da rede, através de um autocarro de serviço (desde as instalações até à nuvem e vice-versa, incluindo ligações híbridas como a ExpressRoute), ou durante um processo de entrada/saída, é considerado como estando em movimento.

### <a name="encryption-at-rest"></a>Encriptação inativa

Para obter encriptação em repouso, faça cada um dos seguintes:

Suporte pelo menos um dos modelos de encriptação recomendados detalhados na tabela seguinte para encriptar dados.

| Modelos de encriptação |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Encriptação do servidor | Encriptação do servidor | Encriptação do servidor | Encriptação do cliente
| Encriptação do lado do servidor usando chaves geridas pelo serviço | Encriptação do lado do servidor usando chaves geridas pelo cliente no cofre de chaves Azure | Encriptação do lado do servidor usando em chaves geridas pelo cliente no local |
| • Os Fornecedores de Recursos Azure realizam as operações de encriptação e desencriptação <br> • Microsoft gere as chaves <br>• Funcionalidade completa da nuvem | • Os Fornecedores de Recursos Azure realizam as operações de encriptação e desencriptação<br>• O cliente controla as chaves através do Cofre de Chaves Azure<br>• Funcionalidade completa da nuvem | • Os Fornecedores de Recursos Azure realizam as operações de encriptação e desencriptação <br>• Cliente controla chaves No local <br> • Funcionalidade completa da nuvem| • Os serviços do Azure não conseguem ver dados desencriptados <br>• Os clientes mantêm as chaves no local (ou noutras lojas seguras). As chaves não estão disponíveis para os serviços do Azure <br>• Redução da funcionalidade da nuvem|

### <a name="enabling-encryption-at-rest"></a>Habilitar a encriptação em repouso

**Identifique todos os locais dos dados das suas lojas**

O objetivo da encriptação em repouso é encriptar todos os dados. Ao fazê-lo elimina a possibilidade de falta de dados importantes ou de todos os locais persificados. Enumerar todos os dados armazenados pela sua aplicação.

> [!Note]
> Não apenas "dados de aplicação" ou "PII", mas quaisquer dados relativos à aplicação, incluindo metadados de conta (mapeamentos de assinaturas, informações sobre contratos, PII).

Considere quais as lojas que está a usar para armazenar dados. Por exemplo:

- Armazenamento externo (por exemplo, SQL Azure, Documento DB, HDInsights, Data Lake, etc.)

- Armazenamento temporário (qualquer cache local que inclua dados de inquilinos)

- Cache de memória (pode ser colocado no ficheiro da página.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Aproveite a encriptação existente no suporte de descanso em Azure

Para cada loja que utiliza, aproveite a encriptação existente no suporte de descanso.

- Armazenamento Azure: Ver encriptação do serviço de [armazenamento Azure para dados em repouso,](../../storage/common/storage-service-encryption.md)

- SQL Azure: Ver [Encriptação transparente de dados (TDE), SQL sempre encriptado](https://msdn.microsoft.com/library/mt163865.aspx)

- VM & armazenamento local de discos[(Encriptação do Disco Azure)](../azure-security-disk-encryption-overview.md)

Para armazenamento de discos VM e Local utilize encriptação de disco Azure onde suportado:

#### <a name="iaas"></a>IaaS

Os serviços com VMs IaaS (Windows ou Linux) devem utilizar a [Encriptação do Disco Azure](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) para encriptar volumes que contenham dados do cliente.

#### <a name="paas-v2"></a>PaaS v2

Os serviços em execução no PaaS v2 utilizando o Service Fabric podem utilizar encriptação de disco Azure para conjunto de escala de máquina virtual [VMSS] para encriptar os seus VMs V2 PaaS.

#### <a name="paas-v1"></a>PaaS v1

A tualmente, a Encriptação do Disco Azure não é suportada no PaaS v1. Por isso, deve utilizar a encriptação do nível de aplicação para encriptar dados persistidos em repouso.  Isto inclui, mas não se limita a, dados de aplicação, ficheiros temporários, registos e despejos de colisões.

A maioria dos serviços deve tentar alavancar a encriptação de um fornecedor de recursos de armazenamento. Alguns serviços têm de fazer encriptação explícita, por exemplo, qualquer material-chave persistido (Certificados, chaves raiz/mestre) deve ser armazenado no Cofre chave.

Se apoiar a encriptação do lado do serviço com chaves geridas pelo cliente, tem de haver uma forma de o cliente nos entregar a chave. A forma suportada e recomendada de o fazer integrando-se com o Azure Key Vault (AKV). Neste caso, os clientes podem adicionar e gerir as suas chaves no Cofre de Chaves Azure. Um cliente pode aprender a usar akV via [Getting Started with Key Vault](https://go.microsoft.com/fwlink/?linkid=521402).

Para integrar com o Azure Key Vault, adicionaria código para solicitar uma chave do AKV quando necessário para a desencriptação.

- Consulte [o Cofre chave Azure – Passo a passo](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) para obter informações sobre como integrar-se com o AKV.

Se suportar as chaves geridas pelo cliente, precisa de fornecer um UX para o cliente especificar qual o Cofre chave (ou Cofre de Chaves URI) a utilizar.

Como a encriptação em Rest envolve a encriptação de dados de hospedagem, infraestruturas e inquilinos, a perda das chaves devido a falha do sistema ou atividade maliciosa pode significar que todos os dados encriptados estão perdidos. Por isso, é fundamental que a sua solução de Encriptação em Repouso tenha uma história abrangente de recuperação de desastres resiliente a falhas do sistema e atividade maliciosa.

Os serviços que implementam a Encriptação em Repouso são geralmente ainda suscetíveis às chaves de encriptação ou dados que ficam desencriptados na unidade do hospedeiro (por exemplo, no ficheiro de página do oso anfitrião.) Por conseguinte, os serviços devem assegurar que o volume de acolhimento dos seus serviços seja encriptado. Para facilitar esta equipa da Compute permitiu a implementação da Encriptação do Anfitrião, que utiliza [o BitLocker](https://technet.microsoft.com/library/dn306081.aspx) NKP e as extensões ao serviço DCM e ao agente para encriptar o volume do anfitrião.

A maioria dos serviços são implementados em VMs-padrão Azure. Tais serviços devem obter encriptação do [anfitrião](../azure-security-disk-encryption-overview.md) automaticamente quando a Compute o ativar. Para serviços em execução em clusters geridos computamente a encriptação do anfitrião é ativada automaticamente à medida que o Windows Server 2016 é lançado.

### <a name="encryption-in-transit"></a>Encriptação em trânsito

Proteger os dados em trânsito deve ser uma parte essencial da sua estratégia de proteção de dados. Uma vez que os dados estão a mover-se de vários locais, a recomendação geral é que utilize sempre protocolos SSL/TLS para trocar dados em diferentes locais. Em algumas circunstâncias, você pode querer isolar todo o canal de comunicação entre as suas instalações e infraestruturas em nuvem usando uma rede privada virtual (VPN).

Para os dados que se movem entre a sua infraestrutura no local e o Azure, deve considerar salvaguardas adequadas, como HTTPS ou VPN.

Para organizações que precisam de garantir o acesso de várias estações de trabalho localizadas no local para Azure, utilize a [VPN local-a-local do Azure.](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)

Para organizações que precisam de garantir o acesso de uma estação de trabalho localizada no local para Azure, utilize [vpN ponto-a-local](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Conjuntos de dados maiores podem ser movidos sobre uma ligação WAN dedicada de alta velocidade, como [expressRoute](https://azure.microsoft.com/services/expressroute/). Se optar por utilizar o ExpressRoute, também pode encriptar os dados ao nível da aplicação utilizando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para uma maior proteção.

Se estiver a interagir com o Azure Storage através do Portal Azure, todas as transações ocorrem via HTTPS. [O Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) over HTTPS também pode ser usado para interagir com o [Armazenamento Azure](https://azure.microsoft.com/services/storage/) e a Base de [Dados SQL Azure.](https://azure.microsoft.com/services/sql-database/)

As organizações que não protegem os dados em trânsito são mais suscetíveis a [ataques entre homens](https://technet.microsoft.com/library/gg195821.aspx)e médios, [escutas](https://technet.microsoft.com/library/gg195641.aspx)e sequestro sessões. Esses ataques podem ser o primeiro passo para obter acesso a dados confidenciais.

Pode saber mais sobre a opção Azure VPN lendo o artigo [Planeamento e design para VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="enforce-file-level-data-encryption"></a>Impor encriptação de dados de nível de ficheiro

[O Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) utiliza políticas de encriptação, identidade e autorização para ajudar a proteger os seus ficheiros e e-mails. O Azure RMS funciona em vários dispositivos - telefones, tablets e Computadores, protegendo tanto dentro da sua organização como fora da sua organização. Esta capacidade é possível porque o Azure RMS adiciona um nível de proteção que permanece com os dados, mesmo quando deixa os limites da sua organização.

Quando utiliza o Azure RMS para proteger os seus ficheiros, está a utilizar criptografia padrão da indústria com suporte total de [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf). Ao alavancar o Azure RMS para proteção de dados, tem a garantia de que a proteção permanece com o ficheiro, mesmo que seja copiado para armazenamento que não esteja sob o controlo de TI, como um serviço de armazenamento em nuvem. O mesmo ocorre para ficheiros partilhados por e-mail, o ficheiro está protegido como anexo a uma mensagem de e-mail, com instruções sobre como abrir o anexo protegido.
Ao planear a adoção de RMS Azure recomendamos o seguinte:

- Instale a aplicação de [partilha RMS.](https://technet.microsoft.com/library/dn339006.aspx) Esta aplicação integra-se com aplicações do Office instalando um add-in do Office para que os utilizadores possam facilmente proteger os ficheiros diretamente.

- Configure aplicações e serviços para apoiar o Azure RMS

- Crie [modelos personalizados](https://technet.microsoft.com/library/dn642472.aspx) que reflitam os seus requisitos de negócio. Por exemplo: um modelo para dados ultrassecretos que devem ser aplicados em todos os e-mails relacionados com segredo de topo.

As organizações que são fracas na [classificação](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) de dados e na proteção de ficheiros podem ser mais suscetíveis à fuga de dados. Sem uma proteção adequada dos ficheiros, as organizações não poderão obter informações sobre o negócio, monitorizar para abuso e impedir o acesso malicioso aos ficheiros.

> [!Note]
> Pode saber mais sobre o Azure RMS lendo o artigo [Getting Started with Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application"></a>Proteja a sua aplicação
Enquanto o Azure é responsável por garantir a infraestrutura e plataforma em que a sua aplicação funciona, é da sua responsabilidade garantir a sua própria aplicação. Por outras palavras, é necessário desenvolver, implementar e gerir o seu código de aplicação e conteúdo de forma segura. Sem isso, o seu código de aplicação ou conteúdo ainda pode ser vulnerável a ameaças.

### <a name="web-application-firewall"></a>Firewall de aplicação Web
Firewall de [aplicação web (WAF)](../../application-gateway/waf-overview.md) é uma característica do [Application Gateway](../../application-gateway/overview.md) que fornece proteção centralizada das suas aplicações web a partir de explorações e vulnerabilidades comuns.

A firewall de aplicações Web baseia-se nas regras dos [conjuntos de regras de núcleo OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9. Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em várias camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

Algumas das vulnerabilidades Web comuns contra as quais a firewall de aplicações Web protege incluem:

- Proteção contra injeção de SQL

- Proteção contra scripting entre sites

- Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros

- Proteção contra violações de protocolo HTTP

- Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta

- Prevenção de contra bots, crawlers e scanners

- Deteção de configurações de aplicações comuns (isto é, Apache, IIS, etc.)

> [!Note]
> Para obter uma lista mais pormenorizada das regras e respetivas proteções, consulte os [seguintes conjuntos](../../application-gateway/waf-overview.md)de regras core:

O Azure também fornece várias funcionalidades fáceis de usar para ajudar a garantir o tráfego de entrada e saída para a sua aplicação. O Azure também ajuda os clientes a garantir o seu código de aplicação, fornecendo funcionalidades fornecidas externamente para digitalizar a sua aplicação web para vulnerabilidades.

- [Configuração da autenticação de Diretório Ativo Azure para a sua app](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Proteja o tráfego na sua aplicação, permitindo a Segurança da Camada de Transporte (TLS/SSL) - HTTPS](../../app-service/configure-ssl-bindings.md)

  - [Forçar todo o tráfego de entrada sobre conexão HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Permitir segurança rigorosa dos transportes (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Restringir o acesso à sua app pelo endereço IP do cliente](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Restrinja o acesso à sua app pelo comportamento do cliente - solicitar frequência e conmoedação](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Digitale o seu código de aplicação web para vulnerabilidades usando a Digitalização de Segurança Do Tinfoil](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Configure a autenticação mútua tLS para exigir certificados de cliente para se conectar à sua aplicação web](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Configure um certificado de cliente para uso a partir da sua app para ligar de forma segura a recursos externos](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Remova os cabeçalhos padrão do servidor para evitar que as ferramentas de impressão digital da sua aplicação](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Ligue a sua aplicação de forma segura com recursos numa rede privada utilizando vpN point-to-site](../../app-service/web-sites-integrate-with-vnet.md)

- [Ligue a sua aplicação de forma segura com recursos numa rede privada utilizando Conexões Híbridas](../../app-service/app-service-hybrid-connections.md)

O Azure App Service utiliza a mesma solução Antimalware utilizada pelos Serviços de Nuvem Azure e máquinas virtuais. Para saber mais sobre isso consulte a nossa [documentação Antimalware](antimalware.md).

## <a name="secure-your-network"></a>Proteja a sua rede
O Microsoft Azure inclui uma robusta infraestrutura de networking para suportar os requisitos de conectividade de aplicação e de serviço. A conectividade da rede é possível entre os recursos localizados no Azure, entre as instalações e o Azure hospedados recursos, e de e para a Internet e Azure.

A [infraestrutura](../../virtual-machines/windows/infrastructure-example.md) de rede Azure permite-lhe ligar os recursos Azure de forma segura uns aos outros com [redes virtuais (VNets)](../../virtual-network/virtual-networks-overview.md). Um VNet é uma representação da sua própria rede na nuvem. Um VNet é um isolamento lógico da rede de nuvem Azure dedicada à sua subscrição. Pode ligar os VNets às suas redes no local.

![Proteja a sua rede (proteger)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Se necessitar de um controlo básico do nível de rede (com base no endereço IP e nos protocolos TCP ou UDP), então pode utilizar [grupos](../../virtual-network/virtual-network-vnet-plan-design-arm.md)de segurança de rede . Um Grupo de Segurança de Rede (NSG) é uma firewall de filtragem de pacotes básicos e estatais e permite-lhe controlar o acesso com base num [túnica de 5-tuple](https://www.techopedia.com/definition/28190/5-tuple).

A rede Azure suporta a capacidade de personalizar o comportamento de encaminhamento para tráfego de rede nas suas Redes Virtuais Azure. Pode fazê-lo configurando [rotas definidas](../../virtual-network/virtual-networks-udr-overview.md) pelo utilizador em Azure.

[A escavação forçada](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode utilizar para garantir que os seus serviços não estão autorizados a iniciar uma ligação a dispositivos na Internet.

O Azure suporta a conectividade dedicada à ligação WAN à sua rede no local e a uma Rede Virtual Azure com [expressRoute.](../../expressroute/expressroute-introduction.md) A ligação entre o Azure e o seu site utiliza uma ligação dedicada que não passa pela Internet pública. Se a sua aplicação Azure estiver a funcionar em vários datacenters, pode utilizar o Gestor de [Tráfego Do Azure](../../traffic-manager/traffic-manager-overview.md) para encaminhar os pedidos dos utilizadores de forma inteligente em todos os casos da aplicação. Também pode encaminhar o tráfego para serviços que não estão a funcionar em Azure se estiverem acessíveis a partir da Internet.

## <a name="virtual-machine-security"></a>Segurança da máquina virtual

[As Máquinas Virtuais Azure](../../virtual-machines/index.yml) permitem implementar uma vasta gama de soluções computoradas de forma ágil. Com suporte para Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Azure BizTalk Services, pode implementar qualquer carga de trabalho e qualquer idioma em quase qualquer sistema operativo.

Com o Azure, pode utilizar [software antimalware](antimalware.md) de fornecedores de segurança como a Microsoft, Symantec, Trend Micro e Kaspersky para proteger as suas máquinas virtuais de ficheiros maliciosos, adware e outras ameaças.

O Microsoft Antimalware para Serviços de Nuvem Azure e Máquinas Virtuais é uma capacidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares maliciosos. O Microsoft Antimalware fornece alertas configuráveis quando conhecidos software malicioso ou indesejado tenta instalar-se ou funcionar nos seus sistemas Azure.

[A Azure Backup](../../backup/backup-overview.md) é uma solução escalável que protege os dados da sua aplicação com investimento de capital zero e custos mínimos de exploração. Erros de aplicação podem corromper os seus dados, e erros humanos podem introduzir bugs nas suas aplicações. Com o Azure Backup, as suas máquinas virtuais que executam windows e Linux estão protegidas.

[A Azure Site Recovery](../../site-recovery/site-recovery-overview.md) ajuda a orquestrar a replicação, a falha e a recuperação de cargas de trabalho e apps para que estejam disponíveis a partir de um local secundário se a sua localização primária descer.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Garantir a conformidade: Lista de verificação de diligências dos serviços na nuvem

A Microsoft desenvolveu a Lista de Verificação de Due Diligence dos [Serviços Cloud](https://aka.ms/cloudchecklist.download) para ajudar as organizações a exercitarem as devidas diligências, uma vez que consideram uma mudança para a nuvem. Fornece uma estrutura para uma organização de qualquer dimensão e tipo - empresas privadas e organizações do setor público, incluindo governo a todos os níveis e sem fins lucrativos - para identificar o seu próprio desempenho, serviço, gestão de dados e objetivos e requisitos de governação. Isto permite-lhes comparar as ofertas de diferentes fornecedores de serviços na nuvem, formando finalmente a base para um acordo de serviço na nuvem.

A lista de verificação fornece um quadro que alinha cláusula a cláusula com uma nova norma internacional para acordos de serviços na nuvem, ISO/IEC 19086. Esta norma oferece um conjunto unificado de considerações para as organizações ajudá-las a tomar decisões sobre a adoção em nuvem, e criar um terreno comum para comparar ofertas de serviços em nuvem.

A lista de verificação promove uma mudança completamente vetada para a nuvem, fornecendo orientação estruturada e uma abordagem consistente e repetível para escolher um fornecedor de serviços na nuvem.

A adoção em nuvem já não é simplesmente uma decisão tecnológica. Como os requisitos da lista de verificação tocam em todos os aspetos de uma organização, eles servem para convocar todos os principais decisores internos - o CIO e cISO, bem como profissionais legais, de gestão de risco, aquisições e compliance. Isto aumenta a eficiência do processo de decisão e das decisões fundamentais em raciocínio sã, reduzindo assim a probabilidade de imprevistos de bloqueios à adoção.

Além disso, a lista de verificação:

- Expõe tópicos de discussão para os decisores no início do processo de adoção em nuvem.

- Apoia discussões minuciosas sobre os regulamentos e os objetivos da própria organização para a privacidade, informação pessoalmente identificável (PII) e segurança de dados.

- Ajuda as organizações a identificar quaisquer potenciais problemas que possam afetar um projeto em nuvem.

- Fornece um conjunto consistente de perguntas, com os mesmos termos, definições, métricas e entregas para cada fornecedor, para simplificar o processo de comparação de ofertas de diferentes fornecedores de serviços na nuvem.

## <a name="azure-infrastructure-and-application-security-validation"></a>Validação da segurança da infraestrutura azur e da aplicação

[A Segurança Operacional Azure](operational-security.md) refere-se aos serviços, controlos e funcionalidades disponíveis para os utilizadores para proteger os seus dados, aplicações e outros ativos no Microsoft Azure.

![validação de segurança (detetar)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

A Segurança Operacional Azure baseia-se num quadro que incorpora o conhecimento adquirido através de várias capacidades exclusivas da Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Centre e uma profunda consciência do panorama das ameaças à cibersegurança.

### <a name="microsoft-azure-monitor"></a>Microsoft Azure Monitor

[O Azure Monitor](../../azure-monitor/index.yml) é a solução de gestão de TI para a nuvem híbrida. Utilizados sozinhos ou para alargar a implementação do Centro de Sistemas existente, os registos do Azure Monitor conferem-lhe a máxima flexibilidade e controlo para a gestão baseada na nuvem da sua infraestrutura.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Com o Monitor Azure, pode gerir qualquer instância em qualquer nuvem, incluindo no local, Azure, AWS, Windows Server, Linux, VMware e OpenStack, a um custo mais baixo do que soluções competitivas. Construído para o mundo em nuvem, o Azure Monitor oferece uma nova abordagem para gerir a sua empresa que é a forma mais rápida e rentável de enfrentar novos desafios de negócio e acomodar novas cargas de trabalho, aplicações e ambientes em nuvem.

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Os registos do Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics) fornecem serviços de monitorização recolhendo dados de recursos geridos para um repositório central. Estes dados podem incluir dados de eventos ou de desempenho ou dados personalizados fornecidos através da API. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação.

![Registos do Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Este método permite-lhe consolidar dados de várias fontes, para que possa combinar dados dos seus serviços Azure com o ambiente existente no local. Também separa claramente a recolha dos dados das ações tomadas em relação aos mesmos, para que todas as ações estejam disponíveis para todos os tipos de dados.

### <a name="azure-security-center"></a>Centro de Segurança do Azure

O [Centro de Segurança do Azure](../../security-center/security-center-intro.md) ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

O Centro de Segurança analisa o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Uma lista de recomendações orienta-o no processo de configuração de controlos necessários.

Os exemplos incluem:

- Aprovisionamento de antimalware para ajudar a identificar e remover o software malicioso

- Configurar grupos e regras de segurança de rede para controlar o tráfego para VMs

- Aprovisionamento de firewalls de aplicação Web para ajudar a proteger-se contra ataques que visam as suas Web Apps

- Implementação de atualizações do sistema em falta

- Abordagem de configurações de SO que não correspondam às linhas de base recomendadas

O Centro de Segurança recolhe, analisa e integra automaticamente os dados de registo a partir dos recursos do Azure, da rede e das soluções de parceiros, como os programas antimalware e as firewalls. Quando são detetadas ameaças, é criado um alerta de segurança. Os exemplos incluem a deteção de:

- VMs comprometidos comunicando com endereços IP maliciosos conhecidos

- Software maligno avançado detetado ao utilizar a comunicação de erros do Windows

- Ataques à força bruta contra VMs

- Alertas de segurança a partir de programas antimalware e firewalls integrados

### <a name="azure-monitor"></a>Monitor Azure

[O Monitor Azure](../../azure-monitor/overview.md) fornece indicações sobre tipos específicos de recursos. Oferece visualização, consulta, encaminhamento, alerta, escala automática e automatização em dados tanto da infraestrutura Azure (Registo de Atividade) como de cada recurso Azure individual (Registos de Diagnóstico).

As aplicações em nuvem são complexas com muitas partes móveis. A monitorização fornece dados para garantir que a sua aplicação permanece em funcionamento num estado saudável. Também o ajuda a evitar potenciais problemas ou problemas para além dos.

![Além](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png) disso, o Monitor Azure pode utilizar dados de monitorização para obter informações profundas sobre a sua aplicação. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a manutenção da aplicação, ou automatizar ações que de outra forma exigiriam intervenção manual.

A auditoria à segurança da sua rede é vital para detetar vulnerabilidades de rede e garantir o cumprimento do seu modelo de segurança e governança regulamentar. Com a visão do Grupo de Segurança, pode recuperar as regras configuradas do Grupo de Segurança da Rede e de segurança, bem como as regras de segurança eficazes. Com a lista de regras aplicadas, pode determinar as portas abertas e a vulnerabilidade da rede SS.

### <a name="network-watcher"></a>Observador de rede

[O Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional que lhe permite monitorizar e diagnosticar condições a nível de rede, de e para o Azure. As ferramentas de diagnóstico e visualização de rede disponíveis com o Network Watcher ajudam-no a compreender, diagnosticar e obter informações sobre a sua rede em Azure. Este serviço inclui a captura de pacotes, o próximo salto, verificação de fluxo IP, vista de grupo de segurança, registos de fluxo NSG. A monitorização do nível dos cenários proporciona uma visão final dos recursos de rede em contraste com a monitorização individual dos recursos da rede.

### <a name="storage-analytics"></a>Análise de armazenamento

[O Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) pode armazenar métricas que incluem estatísticas de transações agregadas e dados de capacidade sobre pedidos a um serviço de armazenamento. As transações são reportadas tanto ao nível da operação API como ao nível do serviço de armazenamento, e a capacidade é reportada ao nível do serviço de armazenamento. Os dados das métricas podem ser usados para analisar o uso do serviço de armazenamento, diagnosticar problemas com pedidos feitos contra o serviço de armazenamento, e melhorar o desempenho de aplicações que utilizam um serviço.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço extensível de Gestão de Desempenho de Aplicações (APM) para desenvolvedores web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. Além de detetar automaticamente anomalias de desempenho, Inclui ferramentas de análise poderosas para ajudá-lo a diagnosticar problemas e a entender o que os utilizadores fazem com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade. Funciona para apps em uma grande variedade de plataformas, incluindo .NET, Node.js e Java EE, hospedados no local ou na nuvem. Integra-se com o seu processo de devOps, e tem pontos de ligação a várias ferramentas de desenvolvimento.

Monitoriza:

- **Taxas de pedidos, tempos de resposta e taxas de falhas** - saiba quais são as páginas mais populares, em que alturas do dia e onde estão localizados os seus utilizadores. Veja que páginas têm o melhor desempenho. Se os tempos de resposta e as taxas de falhas aumentarem quando há mais pedidos, é possível que tenha um problema relativo a recursos.

- **Taxas de dependência, tempos de resposta e taxas de falhas** - veja se os serviços externos o estão a atrasar.

- **Exceções** - Analise as estatísticas agregadas, ou escolha instâncias específicas e perfure os vestígios da pilha e pedidos relacionados. São reportadas exceções do servidor e do browser.

- **Vistas de página e desempenho de carga** - reportados pelos browsers dos utilizadores.

- **Chamadas do AJAX de páginas web** - taxas, tempos de resposta e taxas de falha.

- **Conta gens de utilizador e sessão.**

- **Contadores de desempenho** dos computadores de servidor Windows ou Linux, como CPU, memória e utilização de rede.

- **Diagnóstico do anfitrião** do Docker ou do Azure.

- **Registos de rastreio de diagnóstico** da sua aplicação - para que possa correlacionar eventos de rastreio a pedidos.

- **Eventos e métricas personalizados** que escreve no código do cliente ou servidor, para acompanhar eventos de negócios como itens vendidos ou jogos ganhos.

Normalmente, a infraestrutura da sua aplicação é composta por vários componentes, como uma máquina virtual, uma conta de armazenamento e uma rede virtual, ou uma aplicação Web, uma base de dados, um servidor de base de dados e serviços de terceiros. Não vê estes componentes como entidades separadas. Em vez disso, vê-os como partes relacionadas e interdependentes de uma única entidade. Deve implementá-los, geri-los e monitorizá-los como um grupo. [O Azure Resource Manager](../../azure-resource-manager/management/overview.md) permite-lhe trabalhar com os recursos da sua solução como grupo.

Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Utiliza um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, como de teste e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação.

**Vantagens da utilização do Resource Manager**

O Resource Manager oferece várias vantagens:

- Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

- Pode implementar repetidamente a solução durante todo o ciclo de vida de desenvolvimento e ter a confiança de que os recursos são implementados num estado consistente.

- Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

- Pode definir as dependências entre recursos, para que sejam implantados na ordem correta.

- Pode aplicar o controlo de acesso a todos os serviços no seu grupo de recursos porque o Controlo de Acesso Baseado em Funções (RBAC) está integrado de forma nativa na plataforma de gestão.

- Pode aplicar etiquetas a recursos para organizar logicamente todos os recursos na sua subscrição.

- Pode clarificar a faturação da sua organização visualizando os custos de um grupo de recursos partilhando a mesma etiqueta.

> [!Note]
> O Resource Manager proporciona uma nova forma de implementar e gerir as suas soluções. Se usou o modelo de implementação anterior e quer aprender sobre as mudanças, consulte a Implementação do Gestor de [Recursos e a implantação clássica.](../../azure-resource-manager/management/deployment-models.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre segurança lendo alguns dos nossos tópicos de segurança aprofundados:

- [Auditoria e registo](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Cibercrime](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Conceção e segurança operacional](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Encriptação](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Gestão de identidades e acessos](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Segurança da rede](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Gestão de ameaças](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
