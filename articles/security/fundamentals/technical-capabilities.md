---
title: Recursos técnicos de segurança no Azure-Microsoft Azure
description: Introdução aos serviços de segurança no Azure que ajudam a proteger seus dados, recursos e aplicativos na nuvem.
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
ms.openlocfilehash: fbce475e1f783595fb67e62e15d5a503a4c687ed
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780600"
---
# <a name="azure-security-technical-capabilities"></a>Recursos técnicos de segurança do Azure
Este artigo fornece uma introdução aos serviços de segurança no Azure que ajudam a proteger seus dados, recursos e aplicativos na nuvem e atender às necessidades de segurança de sua empresa.

## <a name="azure-platform"></a>Plataforma Azure

O [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) é uma plataforma de nuvem composta por serviços de infraestrutura e aplicativos, com serviços de dados integrados e análises avançadas, além de ferramentas e serviços de desenvolvedor, hospedados nos data centers de nuvem pública da Microsoft. Os clientes usam o Azure para muitas capacidades e cenários diferentes, desde computação básica, rede e armazenamento até serviços de aplicativos Web e móveis, até cenários de nuvem completos como Internet das Coisas e podem ser usados com tecnologias de software livre e implantados como nuvem híbrida ou hospedado no datacenter de um cliente. O Azure fornece tecnologia de nuvem como blocos de construção para ajudar as empresas a economizarem custos, inovar rapidamente e gerenciar sistemas de forma proativa. Ao se basear ou migrar ativos de ti para um provedor de nuvem, você estará confiando na capacidade dessa organização de proteger seus aplicativos e dados com os serviços e os controles que eles fornecem para gerenciar a segurança de seus ativos baseados em nuvem.

Microsoft Azure é o único provedor de computação em nuvem que oferece uma plataforma de aplicativo segura e consistente e uma infraestrutura como serviço para que as equipes trabalhem em seus diferentes habilidades de nuvem e níveis de complexidade do projeto, com serviços de dados integrados e a análise que revela inteligência dos dados onde quer que existam, em plataformas Microsoft e não Microsoft, em estruturas e ferramentas abertas, oferecendo opções para integrar a nuvem com o local e implantar os serviços de nuvem do Azure no data centers locais. Como parte da nuvem confiável da Microsoft, os clientes contam com o Azure para segurança, confiabilidade, conformidade, privacidade e a vasta rede de pessoas, parceiros e processos para dar suporte a organizações na nuvem.

Com Microsoft Azure, você pode:

- Acelere a inovação com a nuvem.

- Decisões de negócios de energia & aplicativos com informações.

- Crie livremente e implante em qualquer lugar.

- Proteja seus negócios.

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>Recursos técnicos de segurança para atenderr sua responsabilidade

O Microsoft Azure fornece serviços que ajudam a atender às suas necessidades de segurança, privacidade e conformidade. A imagem a seguir ajuda a explicar vários serviços do Azure disponíveis para você criar uma infraestrutura de aplicativo segura e compatível com base nos padrões do setor.

![Recursos técnicos de segurança disponíveis – panorama geral](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Gerenciar e controlar a identidade e o acesso do usuário

O Azure ajuda a proteger informações pessoais e de negócios, permitindo que você gerencie credenciais e identidades de usuário e controle o acesso.

### <a name="azure-active-directory"></a>Azure Active Directory

As soluções de gerenciamento de acesso e identidade da Microsoft ajudam a ti a proteger o acesso a aplicativos e recursos no datacenter corporativo e na nuvem, permitindo níveis adicionais de validação, como a autenticação multifator e o acesso condicional Policie. A monitorização de atividades suspeitas através de relatórios de segurança avançada, de auditorias e de alertas ajuda a mitigar potenciais problemas de segurança. O [Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) fornece logon único para milhares de aplicativos de nuvem e acesso a aplicativos Web executados localmente.

Os benefícios de segurança do Azure Active Directory (AD do Azure) incluem a capacidade de:

- Crie e gerencie uma única identidade para cada usuário em sua empresa híbrida, mantendo os usuários, grupos e dispositivos sincronizados.

- Forneça acesso de logon único para seus aplicativos, incluindo milhares de aplicativos SaaS previamente integrados.

- Habilite a segurança de acesso do aplicativo impondo a autenticação multifator baseada em regras para aplicativos locais e na nuvem.

- Provisione o acesso remoto seguro a aplicativos Web locais por meio do Azure Proxy de Aplicativo do AD.

O [portal de Azure Active Directory](https://aad.portal.azure.com/) está disponível como parte do portal do Azure. Nesse painel, você pode obter uma visão geral do estado da sua organização e gerenciar facilmente o diretório, os usuários ou o acesso ao aplicativo.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

A seguir estão os principais recursos de gerenciamento de identidade do Azure:

- Início de sessão único

- Autenticação multifator

- Monitoramento de segurança, alertas e relatórios baseados em Machine Learning

- Gestão de acesso e identidade do consumidor

- Registo de dispositivo

- Privileged Identity Management

- Proteção de identidade

#### <a name="single-sign-on"></a>Início de sessão único

O [SSO (logon único)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) significa poder acessar todos os aplicativos e recursos de que você precisa para fazer negócios, entrando apenas uma vez usando uma única conta de usuário. Depois de conectado, você pode acessar todos os aplicativos necessários sem a necessidade de autenticação (por exemplo, digite uma senha) uma segunda vez.

Muitas organizações contam com aplicativos SaaS (software como serviço) como Office 365, Box e Salesforce para produtividade do usuário final. Historicamente, a equipe de ti precisava criar e atualizar individualmente as contas de usuário em cada aplicativo SaaS, e os usuários precisavam lembrar uma senha para cada aplicativo SaaS.

[O Azure ad estende o Active Directory local para a nuvem](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), permitindo que os usuários usem suas contas organizacionais primárias para não apenas entrar em seus dispositivos ingressados no domínio e recursos da empresa, mas também todos os aplicativos Web e SaaS necessários para seus trabalho.

Não apenas os usuários não precisam gerenciar vários conjuntos de nomes de usuário e senhas, o acesso ao aplicativo pode ser automaticamente provisionado ou desprovisionado com base em grupos organizacionais e seu status como funcionário. O [Azure ad introduz controles de governança de segurança e acesso](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) que permitem que você gerencie centralmente o acesso dos usuários em aplicativos SaaS.

#### <a name="multi-factor-authentication"></a>Autenticação multifator

A autenticação multifator do [Azure (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) é um método de autenticação que requer o uso de mais de um método de verificação e adiciona uma segunda camada crítica de segurança a entradas e transações do usuário. O [MFA ajuda a proteger](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) o acesso a dados e aplicativos enquanto atende à demanda do usuário por um processo de entrada simples. Ele fornece autenticação forte por meio de uma variedade de opções de verificação: chamada telefônica, mensagem de texto ou notificação de aplicativo móvel ou código de verificação e tokens OAuth de terceiros.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitoramento de segurança, alertas e relatórios baseados em Machine Learning

Monitoramento de segurança e alertas e relatórios baseados em aprendizado de máquina que identificam padrões de acesso inconsistentes podem ajudá-lo a proteger seus negócios. Você pode usar os relatórios de acesso e uso do Azure Active Directory para obter visibilidade da integridade e da segurança do diretório da sua organização. Com essas informações, um administrador de diretório pode determinar melhor onde possíveis riscos de segurança podem se situar para que eles possam planejar adequadamente a atenuação desses riscos.

No portal do Azure ou por meio do [portal de Azure Active Directory](https://aad.portal.azure.com/), os [relatórios](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) são categorizados das seguintes maneiras:

- Relatórios de anomalias – contêm eventos de entrada que descobrimos ser anormais. Nossa meta é fazer você conhecer essa atividade e permitir que você possa decidir se um evento é suspeito.

- Relatórios de aplicativos integrados – forneça informações sobre como os aplicativos em nuvem estão sendo usados na sua organização. O Azure Active Directory oferece integração com milhares de aplicativos em nuvem.

- Relatórios de erros – indicam os erros que podem ocorrer ao provisionar contas para aplicativos externos.

- Relatórios específicos do usuário – exibem dados de atividade do dispositivo e de entrada para um usuário específico.

- Logs de atividade – contêm um registro de todos os eventos auditados nas últimas 24 horas, nos últimos 7 dias ou nos últimos 30 dias, e as alterações de atividade de grupo e atividade de registro e redefinição de senha.

#### <a name="consumer-identity-and-access-management"></a>Gestão de acesso e identidade do consumidor

O [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gerenciamento de identidade global e altamente disponível para aplicativos voltados para o consumidor que pode ser dimensionado para centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os consumidores podem iniciar sessão em todas as suas aplicações através de experiências personalizáveis, utilizando as suas contas de redes sociais existentes ou criando novas credenciais.

No passado, os desenvolvedores de aplicativos que desejavam [inscrever e entrar em consumidores](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) em seus aplicativos teriam escrito seu próprio código. E teriam de utilizar sistemas ou bases de dados no local para armazenar os nomes de utilizador e as palavras-passe. A Azure Active Directory B2C oferece à sua organização uma maneira melhor de integrar o gerenciamento de identidade do consumidor em aplicativos com a ajuda de uma plataforma segura, baseada em padrões, e um grande conjunto de políticas extensível.

Quando você usa Azure Active Directory B2C, seus consumidores podem se inscrever para seus aplicativos usando suas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de email e senha, ou nome de usuário e senha).

#### <a name="device-registration"></a>Registo de dispositivo

O [registro de dispositivos do Azure ad](https://docs.microsoft.com/azure/active-directory/device-management-introduction) é a base para cenários de [acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-on-premises-setup) com base em dispositivo. Quando um dispositivo é registrado, o registro de dispositivo do Azure AD fornece ao dispositivo uma identidade que é usada para autenticar o dispositivo quando o usuário faz logon. O dispositivo autenticado e os atributos do dispositivo podem ser usados para impor políticas de acesso condicional para aplicativos hospedados na nuvem e localmente.

Quando combinado com uma solução de [MDM (gerenciamento de dispositivo móvel)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) , como o Intune, os atributos de dispositivo no Azure Active Directory são atualizados com informações adicionais sobre o dispositivo. Isso permite que você crie regras de acesso condicional que impõem o acesso de dispositivos para atender aos seus padrões de segurança e conformidade.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) permite que você gerencie, controle e monitore suas identidades com privilégios e o acesso aos recursos no Azure AD, bem como a outros serviços online da Microsoft, como o Office 365 ou Microsoft Intune.

Às vezes, os usuários precisam executar operações privilegiadas no Azure ou nos recursos do Office 365 ou em outros aplicativos SaaS. Isso geralmente significa que as organizações precisam fornecer acesso privilegiado permanente no Azure AD. Esse é um risco crescente de segurança para recursos hospedados na nuvem, pois as organizações não podem monitorar suficientemente o que esses usuários estão fazendo com seus privilégios de administrador. Além disso, se uma conta de usuário com acesso privilegiado for comprometida, essa violação poderá afetar a segurança geral da nuvem. Azure AD Privileged Identity Management ajuda a resolver esse risco.

Azure AD Privileged Identity Management permite que você:

- Veja quais usuários são administradores do Azure AD

- Habilitar o acesso administrativo "Just-in-time" sob demanda aos serviços online da Microsoft, como o Office 365 e o Intune

- Obter relatórios sobre o histórico de acesso de administrador e alterações nas atribuições de administrador

- Obter alertas sobre o acesso a uma função com privilégios

#### <a name="identity-protection"></a>Proteção de identidade

[Azure ad Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é um serviço de segurança que fornece uma visão consolidada dos eventos de risco e possíveis vulnerabilidades que afetam as identidades da sua organização. A proteção de identidade usa os recursos de detecção de anomalias de Azure Active Directory existentes (disponíveis por meio dos relatórios de atividade anômala do Azure AD) e introduz novos tipos de evento de risco que podem detectar anomalias em tempo real.

## <a name="secure-resource-access"></a>Proteger o acesso a recursos

O controle de acesso no Azure inicia de uma perspectiva de cobrança. O proprietário de uma conta do Azure, acessada visitando a [centro de contas do Azure](https://account.windowsazure.com/subscriptions), é o administrador da conta (AA). As assinaturas são um contêiner para cobrança, mas também agem como um limite de segurança: cada assinatura tem um administrador de serviços (SA) que pode adicionar, remover e modificar os recursos do Azure nessa assinatura usando o portal do Azure. O SA padrão de uma nova assinatura é o AA, mas o AA pode alterar o SA no Centro de Contas do Azure.

![Acesso a recursos protegidos no Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

As assinaturas também têm uma associação com um diretório. O diretório define um conjunto de usuários. Eles podem ser usuários do trabalho ou da escola que criou o diretório, ou podem ser usuários externos (ou seja, contas da Microsoft). As assinaturas são acessíveis por um subconjunto desses usuários de diretório que foram atribuídos como o SA (administrador de serviços) ou a CA (coadministrador); a única exceção é que, por motivos herdados, as contas da Microsoft (anteriormente Windows Live ID) podem ser atribuídas como SA ou CA sem estarem presentes no diretório.

As empresas orientadas a segurança devem se concentrar em fornecer aos funcionários as permissões exatas de que precisam. Muitas permissões podem expor uma conta a invasores. Permissões insuficientes significam que os funcionários não podem realizar seu trabalho com eficiência. O [RBAC (controle de acesso baseado em função) do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) ajuda a resolver esse problema oferecendo gerenciamento de acesso refinado para o Azure.

![Acesso a recursos protegidos](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Ao utilizar o RBAC, pode segregar funções na sua equipa e conceder apenas a quantidade de acesso a utilizadores que precisam para desempenhar as suas funções. Em vez de fornecer a todos permissões irrestritas em sua assinatura ou recursos do Azure, você pode permitir apenas determinadas ações. Por exemplo, use o RBAC para permitir que um funcionário gerencie máquinas virtuais em uma assinatura, enquanto outro pode gerenciar bancos de dados SQL dentro da mesma assinatura.

![Acesso a recursos protegidos no Azure (RBAC)](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Segurança e encriptação de dados

Uma das chaves para a proteção de dados na nuvem é a conta dos possíveis Estados nos quais os dados podem ocorrer e quais controles estão disponíveis para esse estado. Para as práticas recomendadas de segurança de dados e criptografia do Azure, as recomendações estão relacionadas aos Estados de dados a seguir.

- Em repouso: Isso inclui todos os objetos de armazenamento de informações, contêineres e tipos que existem estaticamente na mídia física, seja ele um disco magnético ou óptico.

- Em trânsito: Quando os dados estão sendo transferidos entre componentes, locais ou programas, como pela rede, em um barramento de serviço (do local para a nuvem e vice-versa, incluindo conexões híbridas, como o ExpressRoute) ou durante um processo de entrada/saída, é pensa em como estando em movimento.

### <a name="encryption-at-rest"></a>Encriptação inativa

Para obter a criptografia em repouso, faça o seguinte:

Suporte a pelo menos um dos modelos de criptografia recomendados detalhados na tabela a seguir para criptografar dados.

| Modelos de criptografia |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Criptografia do servidor | Criptografia do servidor | Criptografia do servidor | Criptografia de cliente
| Criptografia do lado do servidor usando chaves de serviço gerenciado | Criptografia do lado do servidor usando chaves gerenciadas pelo cliente no Azure Key Vault | Criptografia do lado do servidor usando chaves gerenciadas do cliente local |
| • Provedores de recursos do Azure executam as operações de criptografia e descriptografia <br> • A Microsoft gerencia as chaves <br>• Funcionalidade de nuvem completa | • Provedores de recursos do Azure executam as operações de criptografia e descriptografia<br>• O cliente controla as chaves por meio de Azure Key Vault<br>• Funcionalidade de nuvem completa | • Provedores de recursos do Azure executam as operações de criptografia e descriptografia <br>• O cliente controla as chaves locais <br> • Funcionalidade de nuvem completa| • Os serviços do Azure não podem ver dados descriptografados <br>• Os clientes mantêm chaves locais (ou em outros repositórios seguros). As chaves não estão disponíveis para os serviços do Azure <br>• Funcionalidade de nuvem reduzida|

### <a name="enabling-encryption-at-rest"></a>Habilitando a criptografia em repouso

**Identificar todos os locais de armazenamento de dados**

A meta da criptografia em repouso é criptografar todos os dados. Isso elimina a possibilidade de dados importantes ausentes ou de todos os locais persistentes. Enumere todos os dados armazenados pelo seu aplicativo.

> [!Note]
> Não apenas "dados de aplicativo" ou "PII", mas quaisquer dados relacionados ao aplicativo, incluindo metadados de conta (mapeamentos de assinatura, informações de contrato, PII).

Considere quais lojas você está usando para armazenar dados. Por exemplo:

- Armazenamento externo (por exemplo, SQL Azure, Document DB, HDInsights, Data Lake, etc.)

- Armazenamento temporário (qualquer cache local que inclui dados de locatário)

- Cache na memória (pode ser colocado no arquivo de paginação).

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Aproveite o suporte de criptografia em repouso existente no Azure

Para cada loja que você usar, aproveite o suporte de criptografia em repouso existente.

- Armazenamento do Azure: Consulte [criptografia do serviço de armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/storage-service-encryption),

- SQL Azure: Consulte [Transparent Data Encryption (TDE), SQL Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)

- Armazenamento de disco local & VM ([Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

Para a VM e o armazenamento de disco local, use Azure Disk Encryption quando houver suporte:

#### <a name="iaas"></a>IaaS

Os serviços com VMs IaaS (Windows ou Linux) devem usar [Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) para criptografar volumes que contêm dados do cliente.

#### <a name="paas-v2"></a>PaaS v2

Os serviços em execução na PaaS v2 usando Service Fabric podem usar o Azure Disk Encryption para o conjunto de dimensionamento de máquinas virtuais [VMSS] para criptografar suas VMs de PaaS v2.

#### <a name="paas-v1"></a>PaaS v1

Atualmente, não há suporte para Azure Disk Encryption no PaaS v1. Portanto, você deve usar a criptografia de nível de aplicativo para criptografar dados persistentes em repouso.  Isso inclui, mas não se limita a, dados de aplicativo, arquivos temporários, logs e despejos de memória.

A maioria dos serviços deve tentar aproveitar a criptografia de um provedor de recursos de armazenamento. Alguns serviços precisam fazer criptografia explícita, por exemplo, qualquer material de chave persistente (certificados, chaves raiz/mestre) deve ser armazenado em Key Vault.

Se você oferecer suporte à criptografia do lado do serviço com chaves gerenciadas pelo cliente, precisará ser uma maneira para o cliente obter a chave para nós. A maneira recomendada e com suporte para fazer isso integrando com Azure Key Vault (AKV). Nesse caso, os clientes podem adicionar e gerenciar suas chaves no Azure Key Vault. Um cliente pode aprender a usar o AKV por meio [de introdução com Key Vault](https://go.microsoft.com/fwlink/?linkid=521402).

Para integrar com Azure Key Vault, você adicionaria código para solicitar uma chave de AKV quando necessário para descriptografia.

- Consulte [Azure Key Vault – passo a passo](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) para obter informações sobre como integrar com o akv.

Se você oferecer suporte a chaves gerenciadas pelo cliente, será necessário fornecer uma UX para o cliente especificar qual Key Vault (ou Key Vault URI) usar.

Como a criptografia em repouso envolve a criptografia de dados de host, de infraestrutura e de locatário, a perda das chaves devido à falha do sistema ou a atividades mal-intencionadas pode significar que todos os dados criptografados são perdidos. Portanto, é fundamental que sua solução de criptografia em repouso tenha uma história de recuperação de desastre abrangente resiliente a falhas do sistema e a atividades mal-intencionadas.

Os serviços que implementam a criptografia em repouso geralmente ainda são suscetíveis às chaves de criptografia ou aos dados deixados não criptografados na unidade do host (por exemplo, no arquivo de paginação do sistema operacional do host.) Portanto, os serviços devem garantir que o volume do host para seus serviços seja criptografado. Para facilitar, essa equipe de computação habilitou a implantação da criptografia de host, que usa as extensões e NKP do [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) para o serviço DCM e o agente para criptografar o volume do host.

A maioria dos serviços é implementada em VMs do Azure padrão. Esses serviços devem obter [criptografia de host](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) automaticamente quando a computação o habilita. Para serviços em execução em clusters gerenciados de computação, a criptografia do host é habilitada automaticamente à medida que o Windows Server 2016 é distribuído.

### <a name="encryption-in-transit"></a>Criptografia em trânsito

Proteger os dados em trânsito deve ser parte essencial da sua estratégia de proteção de dados. Como os dados são movidos para frente e para trás de vários locais, a recomendação geral é que você sempre use protocolos SSL/TLS para trocar dados entre diferentes locais. Em algumas circunstâncias, talvez você queira isolar todo o canal de comunicação entre sua infraestrutura local e de nuvem usando uma VPN (rede virtual privada).

Para a movimentação de dados entre sua infraestrutura local e o Azure, você deve considerar as proteções apropriadas, como HTTPS ou VPN.

Para organizações que precisam proteger o acesso de várias estações de trabalho localizadas localmente para o Azure, use a [VPN site a site do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Para organizações que precisam proteger o acesso de uma estação de trabalho localizada no local para o Azure, use [VPN ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Conjuntos de dados maiores podem ser movidos em um link WAN dedicado de alta velocidade, como o [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se você optar por usar o ExpressRoute, também poderá criptografar os dados no nível do aplicativo usando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para proteção adicional.

Se você estiver interagindo com o armazenamento do Azure por meio do portal do Azure, todas as transações ocorrerão via HTTPS. A [API REST de armazenamento](https://msdn.microsoft.com/library/azure/dd179355.aspx) sobre HTTPS também pode ser usada para interagir com o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) e o [banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/).

As organizações que não protegem dados em trânsito são mais suscetíveis a [ataques man-in-the-Middle](https://technet.microsoft.com/library/gg195821.aspx), [espionagem](https://technet.microsoft.com/library/gg195641.aspx)e seqüestro de sessão. Esses ataques podem ser a primeira etapa para obter acesso a dados confidenciais.

Você pode saber mais sobre a opção de VPN do Azure lendo o artigo [planejamento e design para o gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Impor a criptografia de dados no nível do arquivo

O [Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) usa políticas de criptografia, identidade e autorização para ajudar a proteger seus arquivos e email. Azure RMS funciona em vários dispositivos — telefones, tablets e PCs, protegendo-os dentro de sua organização e fora da sua organização. Esse recurso é possível porque Azure RMS adiciona um nível de proteção que permanece com os dados, mesmo quando eles saem dos limites da sua organização.

Ao usar o Azure RMS para proteger seus arquivos, você está usando a criptografia padrão da indústria com suporte total ao [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf). Ao aproveitar o Azure RMS para proteção de dados, você tem a garantia de que a proteção permanece com o arquivo, mesmo que ele seja copiado para o armazenamento que não está sob o controle dele, como um serviço de armazenamento em nuvem. O mesmo ocorre para arquivos compartilhados por email, o arquivo é protegido como um anexo a uma mensagem de email, com instruções sobre como abrir o anexo protegido.
Ao planejar a adoção de Azure RMS, recomendamos o seguinte:

- Instale o [aplicativo RMS Sharing](https://technet.microsoft.com/library/dn339006.aspx). Esse aplicativo se integra com aplicativos do Office instalando um suplemento do Office para que os usuários possam proteger facilmente os arquivos diretamente.

- Configurar aplicativos e serviços para dar suporte ao Azure RMS

- Crie [modelos personalizados](https://technet.microsoft.com/library/dn642472.aspx) que reflitam seus requisitos de negócios. Por exemplo: um modelo para dados secretos principais que devem ser aplicados em todos os emails relacionados ao segredo principal.

As organizações que são fracas na [classificação de dados](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e na proteção de arquivos podem ser mais suscetíveis à perda de dados. Sem a proteção adequada de arquivos, as organizações não poderão obter informações de negócios, monitorar o abuso e impedir o acesso mal-intencionado a arquivos.

> [!Note]
> Você pode saber mais sobre Azure RMS lendo o artigo [introdução com o Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application"></a>Proteger seu aplicativo
Embora o Azure seja responsável por proteger a infraestrutura e a plataforma em que seu aplicativo é executado, é sua responsabilidade proteger seu próprio aplicativo. Em outras palavras, você precisa desenvolver, implantar e gerenciar o código e o conteúdo do aplicativo de maneira segura. Sem isso, o código do aplicativo ou o conteúdo ainda pode estar vulnerável a ameaças.

### <a name="web-application-firewall"></a>Firewall de aplicações Web
O [WAF (firewall do aplicativo Web)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) é um recurso do [Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) que fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns.

A firewall de aplicações Web baseia-se nas regras dos [conjuntos de regras de núcleo OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9. Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em várias camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

Algumas das vulnerabilidades Web comuns contra as quais a firewall de aplicações Web protege incluem:

- Proteção contra injeção de SQL

- Proteção contra scripting entre sites

- Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros

- Proteção contra violações de protocolo HTTP

- Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta

- Prevenção de contra bots, crawlers e scanners

- Detecção de incorretas configurações de aplicativo comuns (isto é, Apache, IIS, etc.)

> [!Note]
> Para obter uma lista mais detalhada de regras e suas proteções, consulte os seguintes [conjuntos de regras principais](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets):

O Azure também fornece vários recursos fáceis de usar para ajudar a proteger o tráfego de entrada e de saída para seu aplicativo. O Azure também ajuda os clientes a protegerem o código do aplicativo fornecendo funcionalidade externamente para verificar se há vulnerabilidades no aplicativo Web.

- [Configurar a autenticação Azure Active Directory para seu aplicativo](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Proteger o tráfego para seu aplicativo habilitando a segurança da camada de transporte (TLS/SSL)-HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

  - [Forçar todo o tráfego de entrada pela conexão HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Habilitar HSTS (segurança de transporte estrita)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Restringir o acesso ao seu aplicativo por endereço IP do cliente](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Restringir o acesso ao seu aplicativo pelo comportamento do cliente – frequência de solicitação e simultaneidade](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Verificar se há vulnerabilidades no código do aplicativo Web usando a verificação do tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Configurar a autenticação mútua TLS para exigir que os certificados do cliente se conectem ao seu aplicativo Web](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [Configurar um certificado de cliente para uso do seu aplicativo para se conectar com segurança a recursos externos](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Remova os cabeçalhos de servidor padrão para evitar que as ferramentas sejam impressão digital de seu aplicativo](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Conectar com segurança seu aplicativo com recursos em uma rede privada usando VPN ponto a site](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [Conectar com segurança seu aplicativo com recursos em uma rede privada usando Conexões Híbridas](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

Azure App serviço usa a mesma solução antimalware usada pelos serviços de nuvem do Azure e máquinas virtuais. Para saber mais sobre isso, consulte nossa [documentação Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware).

## <a name="secure-your-network"></a>Proteja a sua rede
O Microsoft Azure inclui uma infraestrutura de rede robusta para dar suporte aos seus requisitos de conectividade de serviço e aplicativo. A conectividade de rede é possível entre os recursos localizados no Azure, entre os recursos hospedados no local e o Azure e de e para a Internet e o Azure.

A [infraestrutura de rede do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) permite que você conecte com segurança os recursos do Azure entre si com [redes virtuais (VNets)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Uma VNet é uma representação de sua própria rede na nuvem. Uma VNet é um isolamento lógico da rede de nuvem do Azure dedicada à sua assinatura. Você pode conectar o VNets às suas redes locais.

![Proteger sua rede (proteger)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Se você precisar de um controle de acesso básico no nível da rede (com base no endereço IP e nos protocolos TCP ou UDP), poderá usar [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). Um NSG (grupo de segurança de rede) é um Firewall básico de filtragem de pacotes com estado e permite que você controle o acesso com base em uma [tupla de 5](https://www.techopedia.com/definition/28190/5-tuple).

A rede do Azure dá suporte à capacidade de personalizar o comportamento de roteamento para o tráfego de rede em suas redes virtuais do Azure. Você pode fazer isso Configurando [rotas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) no Azure.

O [túnel forçado](https://www.petri.com/azure-forced-tunneling) é um mecanismo que você pode usar para garantir que seus serviços não tenham permissão para iniciar uma conexão com dispositivos na Internet.

O Azure dá suporte à conectividade de link WAN dedicada à sua rede local e a uma rede virtual do Azure com o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). O link entre o Azure e seu site usa uma conexão dedicada que não passa pela Internet pública. Se o aplicativo do Azure estiver sendo executado em vários datacenters, você poderá usar o [Gerenciador de tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) para rotear solicitações de usuários de forma inteligente entre instâncias do aplicativo. Você também pode rotear o tráfego para os serviços que não estão em execução no Azure se eles estiverem acessíveis pela Internet.

## <a name="virtual-machine-security"></a>Segurança de máquinas virtuais

As [máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/) permitem implantar uma ampla variedade de soluções de computação de maneira ágil. Com suporte do Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Serviços BizTalk do Azure, pode implementar qualquer carga de trabalho, qualquer linguagem, em praticamente qualquer sistema operativo.

Com o Azure, você pode usar o [software antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) de fornecedores de segurança como Microsoft, Symantec, Trend Micro e Kaspersky para proteger suas máquinas virtuais contra arquivos mal-intencionados, adwares e outras ameaças.

O Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais é um recurso de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. O Microsoft Antimalware fornece alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta se instalar ou executar em seus sistemas do Azure.

O [backup do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) é uma solução escalonável que protege os dados do aplicativo com zero investimento de capital e custos operacionais mínimos. Os erros das aplicações podem danificar os dados e os erros humanos podem introduzir erros nas suas aplicações. Com o backup do Azure, suas máquinas virtuais executando Windows e Linux são protegidas.

[Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) ajuda a orquestrar a replicação, o failover e a recuperação de cargas de trabalho e aplicativos para que fiquem disponíveis em um local secundário se o seu local principal ficar inativo.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Garantir a conformidade: Lista de verificação de auditoria detalhada dos serviços de nuvem

A Microsoft desenvolveu [a lista de verificação de auditoria detalhada dos serviços de nuvem](https://aka.ms/cloudchecklist.download) para ajudar as organizações a exercerem uma auditoria detalhada, pois consideram uma mudança para a nuvem. Ele fornece uma estrutura para uma organização de qualquer tamanho e tipo — empresas privadas e organizações de setor público, incluindo o governo em todos os níveis e sem fins lucrativos — para identificar seus próprios objetivos de desempenho, serviço, gerenciamento de dados e governança e requirement. Isso permite que eles comparem as ofertas de diferentes provedores de serviço de nuvem, criando, por fim, a base de um contrato de serviço de nuvem.

A lista de verificação fornece uma estrutura que alinha cláusulas por cláusula com um novo padrão internacional para contratos de serviço de nuvem, ISO/IEC 19086. Esse padrão oferece um conjunto unificado de considerações para organizações para ajudá-los a tomar decisões sobre a adoção da nuvem e criar um aterramento comum para comparar as ofertas de serviços de nuvem.

A lista de verificação promove uma mudança completa de verificados para a nuvem, fornecendo orientação estruturada e uma abordagem consistente e reproduzível para escolher um provedor de serviços de nuvem.

A adoção da nuvem não é mais simplesmente uma decisão de tecnologia. Como os requisitos da lista de verificação se tocam em todos os aspectos de uma organização, eles servem para reunirr todos os principais tomadores de decisão internos — o CIO e o CISO, bem como os profissionais jurídico, de gerenciamento de riscos, de compras e de conformidade. Isso aumenta a eficiência do processo de tomada de decisão e as decisões básicas em raciocínio de som, reduzindo assim a probabilidade de obstáculos imprevistos de adoção.

Além disso, a lista de verificação:

- Expõe tópicos importantes de discussão para tomadores de decisão no início do processo de adoção de nuvem.

- Dá suporte a discussões de negócios completas sobre regulamentos e os próprios objetivos da organização para privacidade, PII (informações de identificação pessoal) e segurança de dados.

- Ajuda as organizações a identificar quaisquer possíveis problemas que possam afetar um projeto de nuvem.

- Fornece um conjunto consistente de perguntas, com os mesmos termos, definições, métricas e resultados finais para cada provedor, para simplificar o processo de comparação de ofertas de diferentes provedores de serviços de nuvem.

## <a name="azure-infrastructure-and-application-security-validation"></a>Validação de segurança de aplicativo e infraestrutura do Azure

[Segurança operacional do Azure] (https://docs.microsoft.com/azure/security/fundamentals/operational-security refere-se aos serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros ativos no Microsoft Azure.

![validação de segurança (detectar)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

A segurança operacional do Azure baseia-se em uma estrutura que incorpora o conhecimento obtido por meio de vários recursos que são exclusivos à Microsoft, incluindo o SDL (ciclo de vida do desenvolvimento de segurança da Microsoft), o programa Microsoft Security Response Centre e conscientização profunda do panorama de ameaças do segurança cibernética.

### <a name="microsoft-azure-monitor"></a>Monitor do Microsoft Azure

[Azure monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é a solução de gerenciamento de ti para a nuvem híbrida. Usado sozinho ou para estender sua implantação existente do System Center, Azure Monitor logs oferece a você o máximo de flexibilidade e controle para o gerenciamento baseado em nuvem de sua infraestrutura.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Com o Azure Monitor, você pode gerenciar qualquer instância em qualquer nuvem, incluindo local, Azure, AWS, Windows Server, Linux, VMware e OpenStack, a um custo menor do que as soluções competitivas. Criado para o mundo em nuvem, a Azure Monitor oferece uma nova abordagem para gerenciar sua empresa que é a maneira mais rápida e econômica de atender a novos desafios de negócios e acomodar novas cargas de trabalho, aplicativos e ambientes de nuvem.

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Os logs de Azure monitor](https://azure.microsoft.com/documentation/services/log-analytics) fornecem serviços de monitoramento coletando dados de recursos gerenciados em um repositório central. Estes dados podem incluir dados de eventos ou de desempenho ou dados personalizados fornecidos através da API. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação.

![Registos do Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Esse método permite consolidar dados de uma variedade de fontes, para que você possa combinar dados de seus serviços do Azure com seu ambiente local existente. Também separa claramente a recolha dos dados das ações tomadas em relação aos mesmos, para que todas as ações estejam disponíveis para todos os tipos de dados.

### <a name="azure-security-center"></a>Centro de Segurança do Azure

O [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

O Centro de Segurança analisa o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Uma lista de recomendações orienta-o no processo de configuração de controlos necessários.

Os exemplos incluem:

- Aprovisionamento de antimalware para ajudar a identificar e remover o software malicioso

- Configurando grupos de segurança de rede e regras para controlar o tráfego para VMs

- Aprovisionamento de firewalls de aplicação Web para ajudar a proteger-se contra ataques que visam as suas Web Apps

- Implementação de atualizações do sistema em falta

- Abordagem de configurações de SO que não correspondam às linhas de base recomendadas

O Centro de Segurança recolhe, analisa e integra automaticamente os dados de registo a partir dos recursos do Azure, da rede e das soluções de parceiros, como os programas antimalware e as firewalls. Quando são detetadas ameaças, é criado um alerta de segurança. Os exemplos incluem a deteção de:

- VMs comprometidas se comunicando com endereços IP mal-intencionados conhecidos

- Software maligno avançado detetado ao utilizar a comunicação de erros do Windows

- Ataques de força bruta contra VMs

- Alertas de segurança a partir de programas antimalware e firewalls integrados

### <a name="azure-monitor"></a>Azure monitor

[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) fornece ponteiros para informações sobre tipos específicos de recursos. Ele oferece visualização, consulta, roteamento, alertas, dimensionamento automático e automação nos dados da infraestrutura do Azure (log de atividades) e de cada recurso individual do Azure (logs de diagnóstico).

Os aplicativos de nuvem são complexos com muitas partes móveis. O monitoramento fornece dados para garantir que seu aplicativo permaneça em funcionamento em um estado íntegro. Ele também ajuda a afastarr problemas em potencial ou a solucionar problemas anteriores.

![Azure monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png) além disso, você pode usar os dados de monitoramento para obter informações aprofundadas sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a manutenção do aplicativo, ou automatizar ações que, caso contrário, exigirão intervenção manual.

A auditoria da segurança de rede é vital para detectar vulnerabilidades de rede e garantir a conformidade com a segurança de ti e o modelo de governança regulatória. Com a exibição de grupo de segurança, você pode recuperar o grupo de segurança de rede e as regras de segurança configuradas, bem como as regras de segurança em vigor. Com a lista de regras aplicadas, você pode determinar as portas que estão abertas e a vulnerabilidade da rede SS.

### <a name="network-watcher"></a>Observador de rede

O observador de [rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) é um serviço regional que permite monitorar e diagnosticar condições em um nível de rede em, para e do Azure. As ferramentas de diagnóstico e visualização de rede disponíveis com o observador de rede ajudam você a entender, diagnosticar e obter informações sobre sua rede no Azure. Esse serviço inclui captura de pacote, próximo salto, verificação de fluxo de IP, exibição de grupo de segurança, logs de fluxo de NSG. O monitoramento em nível de cenário fornece uma exibição de ponta a ponta dos recursos de rede em contraste com o monitoramento de recursos de rede individual.

### <a name="storage-analytics"></a>Análise de armazenamento

[Análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) pode armazenar métricas que incluem estatísticas de transação agregadas e dados de capacidade sobre solicitações para um serviço de armazenamento. As transações são relatadas no nível de operação da API, bem como no nível de serviço de armazenamento, e a capacidade é relatada no nível de serviço de armazenamento. Os dados de métricas podem ser usados para analisar o uso do serviço de armazenamento, diagnosticar problemas com solicitações feitas no serviço de armazenamento e melhorar o desempenho de aplicativos que usam um serviço.

### <a name="application-insights"></a>Application Insights

O [Application insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de gerenciamento de desempenho de aplicativos (APM) extensível para desenvolvedores da Web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. Além de detetar automaticamente anomalias de desempenho, Ele inclui ferramentas de análise poderosas para ajudá-lo a diagnosticar problemas e a entender o que os usuários fazem com seu aplicativo. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade. Ele funciona para aplicativos em uma ampla variedade de plataformas, incluindo .NET, Node. js e Java EE, hospedados localmente ou na nuvem. Ele se integra ao seu processo de devOps e tem pontos de conexão para várias ferramentas de desenvolvimento.

Monitoriza:

- **Taxas de pedidos, tempos de resposta e taxas de falhas** - saiba quais são as páginas mais populares, em que alturas do dia e onde estão localizados os seus utilizadores. Veja que páginas têm o melhor desempenho. Se os tempos de resposta e as taxas de falhas aumentarem quando há mais pedidos, é possível que tenha um problema relativo a recursos.

- **Taxas de dependência, tempos de resposta e taxas de falhas** - veja se os serviços externos o estão a atrasar.

- **Exceções** – analise as estatísticas agregadas ou selecione instâncias específicas e aprofunde-se no rastreamento de pilha e nas solicitações relacionadas. São reportadas exceções do servidor e do browser.

- **Vistas de página e desempenho de carga** - reportados pelos browsers dos utilizadores.

- **Chamadas AJAX de páginas da Web** – taxas, tempos de resposta e taxas de falha.

- **Contagens de usuário e sessão.**

- **Contadores de desempenho** dos computadores de servidor Windows ou Linux, como CPU, memória e utilização de rede.

- **Diagnóstico do anfitrião** do Docker ou do Azure.

- **Registos de rastreio de diagnóstico** da sua aplicação - para que possa correlacionar eventos de rastreio a pedidos.

- Os **eventos personalizados e** as métricas que você escreve no código do cliente ou do servidor, para acompanhar eventos de negócios, como itens vendidos ou jogos ganhos.

Normalmente, a infraestrutura da sua aplicação é composta por vários componentes, como uma máquina virtual, uma conta de armazenamento e uma rede virtual, ou uma aplicação Web, uma base de dados, um servidor de base de dados e serviços de terceiros. Não vê estes componentes como entidades separadas. Em vez disso, vê-os como partes relacionadas e interdependentes de uma única entidade. Deve implementá-los, geri-los e monitorizá-los como um grupo. [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permite que você trabalhe com os recursos em sua solução como um grupo.

Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Utiliza um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, como de teste e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação.

**Os benefícios do uso do Resource Manager**

O Resource Manager oferece várias vantagens:

- Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

- Pode implementar repetidamente a solução durante todo o ciclo de vida de desenvolvimento e ter a confiança de que os recursos são implementados num estado consistente.

- Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

- Você pode definir as dependências entre os recursos, para que eles sejam implantados na ordem correta.

- Pode aplicar o controlo de acesso a todos os serviços no seu grupo de recursos porque o Controlo de Acesso Baseado em Funções (RBAC) está integrado de forma nativa na plataforma de gestão.

- Pode aplicar etiquetas a recursos para organizar logicamente todos os recursos na sua subscrição.

- Pode clarificar a faturação da sua organização visualizando os custos de um grupo de recursos partilhando a mesma etiqueta.

> [!Note]
> O Resource Manager proporciona uma nova forma de implementar e gerir as suas soluções. Se você usou o modelo de implantação anterior e deseja saber mais sobre as alterações, consulte [noções básicas sobre a implantação do Resource Manager e a implantação clássica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre segurança lendo alguns dos nossos tópicos de segurança aprofundados:

- [Auditoria e registo](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Crime cibernético](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Design e segurança operacional](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Encriptação](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Gerenciamento de identidade e acesso](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Segurança da rede](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Gestão de ameaças](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
