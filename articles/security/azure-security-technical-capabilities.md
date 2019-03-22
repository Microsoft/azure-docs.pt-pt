---
title: Capacidades técnicas de segurança do Azure | Documentos da Microsoft
description: Saiba mais sobre serviços informáticos com base na cloud que incluem uma ampla seleção de instâncias de computação e serviços que podem aumenta e reduz automaticamente para satisfazer as necessidades do seu aplicativo ou a empresa.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 76944d98d6a5fe7c3f83ce12bb2fdf1a34d5e234
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57839105"
---
# <a name="azure-security-technical-capabilities"></a>Capacidades técnicas de segurança do Azure

Para ajudá-lo do Azure atual e potencial clientes compreenderem e usar os recursos relacionados à segurança vários disponíveis no e que envolvem a plataforma Azure, a Microsoft desenvolveu uma série de White Papers, visões gerais de segurança, práticas recomendadas, e Listas de verificação. Os tópicos vão em termos de amplitude e profundidade e são atualizados periodicamente. Este documento é parte dessa série, conforme resumido na secção Abstrata abaixo. Obter mais informações sobre esta série de segurança do Azure podem ser encontradas em (URL).

## <a name="azure-platform"></a>Plataforma Azure

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) é composta de uma plataforma de cloud de infraestrutura e serviços de aplicações, com os serviços de dados integrada e análises avançadas e ferramentas de programador e serviços, hospedado em dados de cloud pública da Microsoft centros. Os clientes utilizam o Azure para muitos cenários, de computação básico, rede e armazenamento, a dispositivos móveis e as diferentes capacidades e serviços de aplicações web, para cenários de nuvem completo como a Internet das coisas e podem ser usados com tecnologias de código aberto e implantados como a cloud híbrida ou alojada no datacenter de um cliente. O Azure fornece a tecnologia de cloud como blocos de construção para ajudar as empresas a economizar custos, inovar rapidamente e gerir sistemas de maneira proativa. Quando criar ou migrar ativos de TI para um fornecedor de cloud, está a depender das capacidades dessa organização para proteger as suas aplicações e dados com os serviços e os controles fornecem para gerir a segurança dos seus ativos baseados na nuvem.

O Microsoft Azure é o único fornecedor de informática na cloud que oferece uma plataforma de aplicações seguras e consistentes e a infraestrutura-como-serviço das equipas trabalharem em seus conjuntos de competências de cloud diferente e níveis de complexidade do projeto, com os serviços de dados integrada e análises que descobrir intelligence a partir de dados sempre que existir, Microsoft e de plataformas não-Microsoft, abra estruturas e ferramentas, fornecendo à escolha para a integração na cloud com locais também implementar serviços cloud do Azure num datacenters no local. Como parte da Cloud fidedigna da Microsoft, os clientes confiam no Azure para segurança líderes do setor, confiabilidade, conformidade, privacidade e a vasta rede de pessoas, parceiros e processos para suportar as organizações na cloud.

Com o Microsoft Azure, pode:

- Acelere a inovação com a cloud.

- & Aplicações com informações de decisões de negócio de energia.

- Crie livremente e implemente em qualquer lugar.

- Proteger seus negócios.

## <a name="scope"></a>Âmbito

O ponto focal deste White Paper diz respeito a recursos de segurança e a funcionalidade que suporta componentes de principais do Microsoft Azure, ou seja [armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction), [base de dados do Microsoft Azure SQL](https://docs.microsoft.com/azure/sql-database/), [ Modelo de máquina virtual do Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/)e as ferramentas e a infraestrutura que gerir tudo. Esse foco de documento no Microsoft Azure capacidades técnicas disponíveis para, como os clientes a cumprir as respetivas funções na proteção da segurança e privacidade dos seus dados.

A importância de compreender esse modelo de responsabilidade partilhada é essencial para os clientes que estão a ser movidos para a cloud. Os fornecedores de cloud oferecem vantagens consideráveis para os esforços de segurança e conformidade, mas essas vantagens não absolve o cliente de proteção de seus usuários, aplicativos e ofertas de serviço.

Para soluções de IaaS, o cliente é responsável, ou tem uma responsabilidade partilhada para proteger e gerenciar o sistema operativo, configuração de rede, aplicativos, identidade, os clientes e dados.  Compilação de soluções de PaaS em implementações de IaaS, o cliente ainda é responsável, ou tem uma responsabilidade partilhada para proteger e gerenciar aplicativos, identidade, os clientes e dados. Para soluções de SaaS, Nonetheless, o cliente continua a ser responsáveis. Eles tem de garantir que dados são classificados corretamente e que partilham uma responsabilidade para gerir os seus utilizadores e dispositivos de ponto de extremidade.

Este documento não fornece uma abordagem detalhada de qualquer um dos componentes de plataforma Microsoft Azure relacionados, como Web Sites do Azure, Azure Active Directory, HDInsight, serviços de multimédia e outros serviços que são colocados em camadas sobre os componentes principais. Embora, é fornecido um nível mínimo de informações gerais, os leitores devem familiarizados com conceitos básicos do Azure, conforme descrito em outras referências fornecida pela Microsoft e incluídos nas ligações fornecidas neste white paper.

## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>Capacidades técnicas de segurança disponíveis para cumprir a responsabilidade do usuário (cliente) - uma visão geral

O Microsoft Azure fornece serviços que podem ajudar os clientes a satisfazer as necessidades de segurança, privacidade e conformidade. A imagem seguinte ajuda a explicar vários serviços do Azure disponíveis para os utilizadores criar uma infraestrutura de aplicação segura e em conformidade com base nas normas da indústria.

![Imagem de grande de capacidades técnicas de segurança disponíveis](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>Gerir e controlar o acesso de utilizador e de identidade (proteger)

Azure ajuda a proteger a empresa e informações pessoais, permitindo-lhe gerir identidades de utilizador e as credenciais e controlar o acesso.

### <a name="azure-active-directory"></a>Azure Active Directory

Microsoft identidades e acessos gestão soluções ajudar a TI a proteger o acesso aos recursos e aplicações em todo o datacenter Corporativo e para a cloud, permitindo níveis adicionais de validação, como a autenticação multifator e acesso condicional políticas. A monitorização de atividades suspeitas através de relatórios de segurança avançada, de auditorias e de alertas ajuda a mitigar potenciais problemas de segurança. [O Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) fornece início de sessão único a milhares de cloud aplicações (SaaS) e acesso a aplicações web que são executadas no local.

Benefícios de segurança do Azure Active Directory (Azure AD) incluem a capacidade de:

- Criar e gerir uma identidade única para cada utilizador em toda a empresa híbrida, manter os utilizadores, grupos e dispositivos em sincronização.

- Fornece acesso de início de sessão único para as aplicações, incluindo milhares de aplicações de SaaS previamente integradas.

- Ativar a segurança de acesso de aplicação através da imposição de regras com base no multi-factor Authentication para ambos os locais e aplicações na cloud.

- Aprovisionar o acesso remoto seguro a aplicativos da web no local através do Proxy de aplicações do Azure AD.

O [portal do Azure Active Directory](https://aad.portal.azure.com/) está disponível uma parte do portal do Azure. Neste dashboard, pode obter uma descrição geral do Estado da sua organização e aprofundar facilmente gerir o diretório, os usuários ou acesso à aplicação.

![Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

Seguem-se as capacidades de gestão de identidade do Azure core:

- Início de sessão único

- Multi-Factor Authentication

- Monitorização de segurança, alertas e relatórios baseados em aprendizagem automática

- Gestão de acesso e identidade do consumidor

- Registo de dispositivo

- Privileged Identity Management

- Proteção de identidade

#### <a name="single-sign-on"></a>Início de sessão único

[Início de sessão único (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) significa que pode aceder a todas as aplicações e recursos de que precisa para fazer negócios, ao iniciar sessão apenas uma vez com uma conta de utilizador único. Depois de iniciar sessão, pode aceder a todos os aplicativos necessários sem ser necessária para autenticar (por exemplo, escreva uma palavra-passe) uma segunda vez.

Muitas organizações contam com software como um aplicativo de serviço (SaaS), como o Office 365, a caixa e Salesforce para produtividade do utilizador final. Historicamente, a equipe de TI necessários para individualmente criar e atualizar as contas de utilizador em cada aplicação SaaS, e os utilizadores tinham de memorizar uma palavra-passe para cada aplicação SaaS.

[O Azure AD estende o Active Directory no local para a cloud](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), permitindo que os utilizadores utilizem suas contas institucionais primária não só para iniciam sessão para os seus dispositivos associados a domínios e recursos da empresa, mas também todos os web e aplicações SaaS necessários para seu trabalho.

Não só os utilizadores não têm de gerir vários conjuntos de nomes de utilizador e palavras-passe, o acesso da aplicação pode ser aprovisionados ou desaprovisionados automaticamente com base nos grupos organizacionais e o respetivo estado como um funcionário. [O Azure AD introduz controles de governação de acesso e segurança](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) que permitem-lhe gerir centralmente o acesso dos utilizadores em aplicações SaaS.

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[O Azure multi-factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) é um método de autenticação que requer a utilização de mais do que um método de verificação e adiciona uma segunda camada crítica de segurança aos inícios de sessão de utilizador e de transações. [MFA ajuda a salvaguardar](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) acesso a dados e aplicações, satisfazendo um processo de início de sessão simples. Proporciona uma autenticação segura através de um conjunto de opções de verificação — telefonema, mensagem de texto ou aplicação móvel de notificação ou verificação de código e de terceiros tokens OAuth.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorização de segurança, alertas e relatórios baseados em aprendizagem automática

Monitorização de segurança e alertas e relatórios baseados em aprendizagem de máquina identificam padrões de acesso inconsistentes podem ajudar a proteger a sua empresa. Pode utilizar relatórios de utilização e de acesso do Azure Active Directory para obter visibilidade sobre a integridade e segurança de diretório da sua organização. Com essas informações, um administrador do pode determinar melhor onde podem existir possíveis riscos de segurança para que eles podem planear adequadamente atenuar esses riscos.

No portal do Azure ou através da [portal do Azure Active Directory](https://aad.portal.azure.com/), [relatórios](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) são categorizados das seguintes formas:

- Relatórios de anomalias – contém eventos que Descobrimos ser anómala de início de sessão. Nosso objetivo é conscientizá-lo de tal atividade e permitem que poderá decidir sobre se um evento está suspeito.

- Relatórios de aplicação integrados – fornecem informações sobre como as aplicações na cloud estão a ser utilizadas na sua organização. Azure Active Directory oferece integração com milhares de aplicações na cloud.

- Relatórios de erros – indicam erros que possam ocorrer quando o aprovisionamento de contas em aplicativos externos.

- Relatórios de utilizador específico – apresentar dispositivos/início de sessão nos dados de atividade para um utilizador específico.

- Registos de atividade – conter um registo de todos os eventos auditados dentro as últimas 24 horas, últimos 7 dias, ou últimos 30 dias e as alterações de atividade de grupo e atividade de registo e reposição de palavra-passe.

#### <a name="consumer-identity-and-access-management"></a>Gestão de acesso e identidade do consumidor

[O Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gestão de identidades global altamente disponível para aplicações voltadas para consumidores que se dimensionam até centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os consumidores podem iniciar sessão em todas as suas aplicações através de experiências personalizáveis, utilizando as suas contas de redes sociais existentes ou criando novas credenciais.

Nos passado, desenvolvedores de aplicativos que queriam [Inscreva-se e iniciar sessão em consumidores](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) nas suas aplicações teriam de escrever o seu próprio código. E teriam de utilizar sistemas ou bases de dados no local para armazenar os nomes de utilizador e as palavras-passe. O Azure Active Directory B2C oferece uma maneira melhor de integrar a gestão de identidades de consumidor nas aplicações com a ajuda de uma plataforma segura, baseada em normas e um grande conjunto de políticas extensíveis de sua organização.

Quando utiliza o Azure Active Directory B2C, os consumidores podem inscrever-se para as suas aplicações através das respetivas contas de redes sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de e-mail e palavra-passe, ou nome de utilizador e palavra-passe).

#### <a name="device-registration"></a>Registo de dispositivo

[Registo de dispositivos do Azure AD](https://docs.microsoft.com/azure/active-directory/device-management-introduction) é a base com base no dispositivo [acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-on-premises-setup) cenários. Quando um dispositivo é registado, o registo de dispositivos do Azure AD fornece ao dispositivo uma identidade que é utilizado para autenticar o dispositivo quando o utilizador inicia sessão. O dispositivo autenticado e os atributos do dispositivo podem então ser utilizados para aplicar políticas de acesso condicional para aplicações alojadas na nuvem e no local.

Quando combinado com um [gestão de dispositivos móveis (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) solução, como o Intune, os atributos do dispositivo no Azure Active Directory são atualizados com informações adicionais sobre o dispositivo. Isto permite criar regras de acesso condicional que impõem o acesso a partir de dispositivos para cumprir as normas de segurança e conformidade.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[O Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) permite-lhe gere, controla e monitorizar as identidades privilegiadas e aceder aos recursos do Azure AD, bem como outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Por vezes, os utilizadores precisam realizar operações privilegiadas em recursos do Azure ou do Office 365 ou outras aplicações SaaS. Isso geralmente significa que as organizações têm a conceder-lhes acesso com privilégios permanente no Azure AD. Este é um risco de segurança de cada vez maior para recursos alojados na cloud, uma vez que as organizações suficientemente não podem monitorizar o que os utilizadores estão a fazer com seus privilégios de administrador. Além disso, se uma conta de utilizador com acesso privilegiado for comprometida, que uma violação pode afetar a segurança na cloud global dos mesmos. O Azure AD Privileged Identity Management ajuda a resolver esse risco.

O Azure AD Privileged Identity Management permite-lhe:

- Ver os utilizadores que são os administradores do Azure AD

- Ativar a pedido, "just-in-time" acesso administrativo ao Microsoft Online Services como o Office 365 e Intune

- Obter relatórios sobre o histórico de acesso de administrador e as alterações em atribuições de administrador

- Obter alertas sobre o acesso a uma função com privilégios

#### <a name="identity-protection"></a>Proteção de identidade

[O Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é um serviço de segurança que fornece uma vista consolidada de eventos de risco e potenciais vulnerabilidades que afetam as identidades da sua organização. Identity Protection utiliza capacidades de deteção de anomalias existente do Azure do Active Directory (disponíveis por meio de relatórios de atividade anómala do Azure AD) e introduz novos tipos de eventos de risco que podem detetar anomalias em tempo real.

## <a name="secured-resource-access-in-azure"></a>Acesso a recursos protegidos no Azure

Controlo de acesso no Azure inicia uma perspetiva de faturação. O proprietário de uma conta do Azure, acedido ao visitar a [Centro de contas do Azure](https://account.windowsazure.com/subscriptions), é o administrador de conta (AA). As subscrições são contentores para faturação, mas também agir como um limite de segurança: cada subscrição tem um serviço de administrador (SA) que pode adicionar, remover e modificar recursos do Azure dessa subscrição com o portal do Azure. O padrão SA de uma nova subscrição é AA, mas o AA pode alterar o SA no Centro de contas do Azure.

![Acesso a recursos protegidos no Azure](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

As subscrições também têm uma associação com um diretório. O diretório define um conjunto de utilizadores. Estes podem ser os utilizadores o trabalho ou escola que criou o diretório ou podem ser utilizadores externos (ou seja, o Microsoft Accounts). As assinaturas podem ser acedidas por um subconjunto dos utilizadores do diretório que foram atribuídas como administrador de serviço (SA) ou Coadministrador (AC); a única exceção é que, por motivos de herança, Accounts da Microsoft (anteriormente conhecido como Windows Live ID) podem ser atribuídos como SA ou AC sem que estejam presentes no diretório.

As empresas dedicadas à segurança devem se concentrar em dar aos funcionários as exatas permissões necessárias. Demasiadas permissões podem expor uma conta para os atacantes. Permissões insuficientes significam que os funcionários não é possível obter a trabalhar com eficiência. [Do Azure com base em função de controlo de acesso (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) ajuda a solucionar esse problema, oferecendo a gestão de acessos detalhada para o Azure.

![Acesso a recursos protegidos](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

Ao utilizar o RBAC, pode segregar funções na sua equipa e conceder apenas a quantidade de acesso a utilizadores que precisam para desempenhar as suas funções. Em vez de dar todas as pessoas sem restrições permissões na sua subscrição do Azure ou recursos, pode permitir que apenas determinadas ações. Por exemplo, utilize o RBAC para permitir que um funcionário gerir máquinas virtuais numa subscrição, enquanto outro pode gerir bases de dados do SQL na mesma subscrição.

![Acesso a recursos protegidos no Azure (RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Criptografia e segurança de dados do Azure (proteger)

Uma das chaves para proteção de dados na cloud é gestão de contas para os Estados possíveis em que os dados podem ocorrer e quais controles estão disponíveis para esse Estado. Para práticas de melhor segurança e encriptação de dados do Azure, as recomendações de ser em torno de Estados dos seguintes dados.

- Inativos: Isto inclui informações de todos os objetos de armazenamento, contentores e tipos existentes estaticamente em mídia física, ser-magnético ou ótico disco.

- Em trânsito: Quando dados estão a ser transferidos entre componentes, locais ou programas, tal como através da rede, num barramento de serviço (a partir de locais para a cloud e vice-versa, incluindo as ligações híbridas, como o ExpressRoute), ou durante um processo de entrada/saída, é a ideia de como estar em movimento.

### <a name="encryption-at-rest"></a>Encriptação inativa

Para alcançar a encriptação em repouso, fazer cada um dos seguintes:

Oferecer suporte a, pelo menos, um dos modelos de criptografia recomendados detalhados na seguinte tabela para encriptar os dados.

| Modelos de encriptação |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Encriptação do servidor | Encriptação do servidor | Encriptação do servidor | Encriptação de cliente
| Utilizar chaves geridas do serviço de encriptação do lado do servidor | Encriptação do lado do servidor com chaves de Customer-Managed no Azure Key Vault | Chaves geridas de encriptação do lado do servidor a utilizar no cliente local |
| • Os fornecedores de recursos do azure realizar as operações de encriptação e desencriptação <br> • Microsoft gerencia as chaves <br>• Funcionalidade de cloud completa | • Os fornecedores de recursos do azure realizar as operações de encriptação e desencriptação<br>• Cliente controla as chaves através do Azure Key Vault<br>• Funcionalidade de cloud completa | • Os fornecedores de recursos do azure realizar as operações de encriptação e desencriptação <br>• Cliente controla as chaves no local <br> • Funcionalidade de cloud completa| • Serviços do azure não podem ver os dados descriptografados <br>• Clientes manter as chaves no local (ou em outras lojas de segurança). As chaves não estão disponíveis para os serviços do Azure <br>• Reduzido funcionalidades da cloud|

### <a name="enabling-encryption-at-rest"></a>Ativar a encriptação em repouso

**Identificar as localizações de todos os seus dados de arquivos**

O objetivo de encriptação em repouso é encriptar todos os dados. Se o fizer, elimina a possibilidade de perder dados importantes ou todas as localizações persistentes. Enumere todos os dados armazenados pela sua aplicação. 

> [!Note] 
> Não apenas "dados de aplicação" ou "PII' mas nenhum dado relacionado à aplicação, incluindo conta metadados (subscrição mapeamentos de informações do contrato, PII).

Considere quais arquivos estão a utilizar para armazenar dados. Por exemplo:

- Armazenamento externo (por exemplo, SQL Azure, Document DB, HDInsights, Data Lake, etc.)

- Armazenamento temporário (qualquer cache local que inclui dados de inquilino)

- Cache de memória (pode ser inserida o ficheiro de paginação.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>A encriptação existentes no suporte de rest no Azure

Para cada loja que utilizar, tirar partido da encriptação existente no suporte de Rest.

- Armazenamento do Azure: Ver [encriptação do serviço de armazenamento do Azure para dados Inativos](https://docs.microsoft.com/azure/storage/storage-service-encryption),

- SQL Azure: Consulte [encriptação de dados transparente (TDE), SQL Always encriptada](https://msdn.microsoft.com/library/mt163865.aspx)

- Armazenamento de disco de VM & Local ([do Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

Para o armazenamento de disco VM e o Local de utilizar o Azure Disk Encryption suportados:

#### <a name="iaas"></a>IaaS

Devem utilizar os serviços com VMs de IaaS (Windows ou Linux) [do Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) para criptografar volumes que contêm dados do cliente.

#### <a name="paas-v2"></a>PaaS v2

Serviços em execução em PaaS v2 com o Service Fabric pode utilizar a encriptação de discos do Azure para o conjunto de dimensionamento da Máquina Virtual [VMSS] para encriptar as suas VMs do PaaS v2.

#### <a name="paas-v1"></a>PaaS v1

Atualmente o Azure Disk Encryption não é suportado no PaaS v1. Por conseguinte, tem de utilizar a encriptação ao nível do aplicativo para criptografar dados persistentes em repouso.  Isto inclui, mas não se limita aos dados da aplicação, arquivos temporários, registos e despejos de memória.

A maioria dos serviços deve tentar tirar partido da encriptação de um fornecedor de recursos de armazenamento. Alguns serviços tem de fazer a encriptação explícita, por exemplo, o material de chave qualquer persistentes (certificados, de raiz / principal de chaves) têm de ser armazenadas no Key Vault.

Se tiver suporte para encriptação do lado do serviço com chaves geridas pelo cliente deve haver uma forma para o cliente obter a chave para nós. A forma recomendada e com suporte para fazê-lo através da integração com o Azure Key Vault (AKV). Neste caso os clientes podem adicionar e gerir as suas chaves no Azure Key Vault. Um cliente pode saber como utilizar AKV via [introdução ao Key Vault](https://go.microsoft.com/fwlink/?linkid=521402).

Para integrar com o Azure Key Vault, adicionaria código para solicitar uma chave de AKV quando necessário para desencriptação.

- Ver [do Azure Key Vault – passo a passo](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) para obter informações sobre como integrar com AKV.

Se tiver suporte para chaves gerida pelo cliente, terá de fornecer uma experiência do Usuário do cliente especificar quais Cofre de chaves (ou o URI do Cofre de chave) para utilizar.

À medida que a encriptação inativa envolve a encriptação de anfitrião, os dados de infraestrutura e de inquilino, a perda de chaves devido uma falha de sistema ou atividades maliciosas podem significar a todos os dados encriptados é perdido. Portanto, é essencial que a encriptação na solução de Rest tem uma história de recuperação de desastre abrangente resiliente a falhas do sistema e atividade maliciosa.

Serviços que implementam a encriptação em repouso normalmente estão ainda sujeitos às chaves de encriptação ou dados a ser deixados sem criptografia da unidade de anfitrião (por exemplo, no ficheiro página de sistema operacional host.) Por conseguinte, os serviços tem de garantir que o volume do anfitrião para os serviços é encriptado. Para facilitar este computação equipe tiver ativado a implementação de criptografia de anfitrião, que usa [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) NKP e extensões para o serviço de DCM e o agente para encriptar o volume do anfitrião.

A maioria dos serviços são implementadas em VMs do Azure standard. Esses serviços devem obter [anfitrião encriptação](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) automaticamente quando computação ativa o mesmo. Para serviços em execução na computação clusters geridos anfitrião encriptação está ativada automaticamente como o Windows Server 2016 é implementado.

### <a name="encryption-in-transit"></a>Encriptação em trânsito

Proteger dados em trânsito, deve ser parte essencial da sua estratégia de proteção de dados. Uma vez que os dados é ir e vir a partir de vários locais, a recomendação geral é que sempre usar protocolos SSL/TLS para trocar dados entre diferentes localizações. Em algumas circunstâncias, poderá querer isolar o canal de toda comunicação entre o local e na cloud infraestrutura através de uma rede privada virtual (VPN).

Para dados mover entre a infraestrutura no local e o Azure, deve considerar as proteções adequadas como HTTPS ou VPN.

Para organizações que precisam para proteger o acesso a partir de várias estações de trabalho localizados no local para o Azure, utilize [VPN de site a site do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Para organizações que precisam para proteger o acesso a partir de uma estação de trabalho localizado no local para o Azure, utilize [Point-to-Site VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Conjuntos de dados maiores podem ser movidos através de um link WAN de alta velocidade dedicado, como [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se optar por utilizar o ExpressRoute, também pode criptografar os dados ao nível do aplicativo utilizando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para maior proteção.

Se estiver a interagir com o armazenamento do Azure através do Portal do Azure, todas as transações ocorrem através de HTTPS. [API REST do Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx) HTTPS também pode ser utilizado para interagir com a ativação pós-falha [armazenamento do Azure](https://azure.microsoft.com/services/storage/) e [base de dados do Azure SQL](https://azure.microsoft.com/services/sql-database/).

As organizações que não as protegem os dados em trânsito estão mais suscetíveis para [ataques man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [escutas](https://technet.microsoft.com/library/gg195641.aspx)e ao seqüestro de sessão. Esses ataques podem ser o primeiro passo para terem acesso aos dados confidenciais.

Pode saber mais sobre a opção de VPN do Azure ao ler o artigo [planear e conceber para o Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Impor a encriptação de dados de nível de ficheiro

[O Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) utiliza políticas de encriptação, identidade e autorização para ajudar a proteger os ficheiros e e-mail. O Azure RMS funciona em vários dispositivos — telemóveis, tablets e PCs ao proteger tanto dentro da sua organização e fora da sua organização. Esta capacidade é possível porque o Azure RMS adiciona um nível de proteção permanece com os dados, mesmo quando sai dos limites da organização.

Ao utilizar o Azure RMS para proteger os seus ficheiros, vai utilizar criptografia de norma da indústria com suporte total de [FIPS 140-2](https://csrc.nist.gov/groups/STM/cmvp/standards.html). Ao tirar partido do Azure RMS para proteção de dados, tem a garantia de que a proteção permanece com o ficheiro, mesmo que seja copiado para o armazenamento que não esteja sob o controlo de TI, tal como um serviço de armazenamento na cloud. O mesmo ocorre para ficheiros partilhados por email, o ficheiro está protegido como um anexo a uma mensagem de e-mail com instruções sobre como abrir o anexo protegido.
Quando planear a adoção do Azure RMS, recomendamos o seguinte:

- Instalar o [aplicação de partilha RMS](https://technet.microsoft.com/library/dn339006.aspx). Esta aplicação se integra com o Office aplicações através da instalação de um escritório suplemento para que os utilizadores possam facilmente proteger ficheiros diretamente.

- Configurar aplicações e serviços para suportar o Azure RMS

- Crie [modelos personalizados](https://technet.microsoft.com/library/dn642472.aspx) que refletem os requisitos de negócio. Por exemplo: um modelo para dados secretos principais que devem ser aplicados em todos os principal segredo relacionados com mensagens de correio eletrónico.

As organizações que estão fracas no [classificação de dados](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e proteção de ficheiros pode ser mais suscetível a fuga de dados. Sem proteção de ficheiros adequados, as organizações não poderá obter informações de negócio, monitorizar abusos e impedir o acesso mal-intencionado a ficheiros.

> [!Note]
> Pode saber mais sobre o Azure RMS ao ler o artigo [introdução ao Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application-protect"></a>Proteger a sua aplicação (proteger)
Embora o Azure é responsável por proteger a infraestrutura e plataforma que seu aplicativo é executado, é sua responsabilidade para proteger a sua aplicação em si. Em outras palavras, precisa desenvolver, implementar e gerir o seu código da aplicação e conteúdo de forma segura. Sem isso, seu código do aplicativo ou conteúdo ainda pode ser vulnerável a ameaças.

### <a name="web-application-firewall-waf"></a>Firewall de aplicações Web (WAF)
[Firewall de aplicações Web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) é uma funcionalidade do [Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) que fornece proteção centralizada das suas aplicações web de exploits e vulnerabilidades comuns.

A firewall de aplicações Web baseia-se nas regras dos [conjuntos de regras de núcleo OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9. Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em várias camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

Algumas das vulnerabilidades Web comuns contra as quais a firewall de aplicações Web protege incluem:

- Proteção contra injeção de SQL

- Proteção contra scripting entre sites

- Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros

- Proteção contra violações de protocolo HTTP

- Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta

- Prevenção de contra bots, crawlers e scanners

- Deteção de aplicação incorretas comuns (ou seja, Apache, IIS, etc.)

> [!Note]
> Para obter uma lista mais detalhada das regras e das respetivas proteções, veja o seguinte procedimento [conjuntos de regras de núcleo](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets):

Azure também fornece vários recursos de fácil utilização para o ajudar a proteger o tráfego de entrada e saído para a sua aplicação. O Azure também ajuda os clientes a proteger seu código de aplicativo, fornecendo externamente fornecido funcionalidade para analisar a sua aplicação web para vulnerabilidades.

- [Configurar a autenticação do Azure Active Directory para a sua aplicação](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Proteger o tráfego para a sua aplicação, permitindo a Transport Layer Security (TLS/SSL) - HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

  - [Forçar todo o tráfego de entrada através de ligação HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Ativar a segurança de transporte Strict (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Restringir o acesso à sua aplicação por endereço IP do cliente](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Restringir o acesso à sua aplicação, o comportamento do cliente - frequência de solicitação e simultaneidade](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Analisar o código da aplicação web para vulnerabilidades de utilizar a análise do Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Configurar a autenticação mútua de TLS para exigir certificados de cliente para ligar à sua aplicação web](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [Configurar um certificado de cliente para a utilização da sua aplicação para ligar em segurança a recursos externos](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Remover os cabeçalhos de servidor padrão para evitar as ferramentas da impressão digital a sua aplicação](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Ligar em segurança a sua aplicação com os recursos numa rede privada através de VPN ponto a Site](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [Ligar em segurança a sua aplicação com os recursos numa rede privada através de ligações híbridas](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

Serviço de aplicações do Azure utiliza a mesma solução de Antimalware utilizada por máquinas virtuais e serviços Cloud do Azure. Para saber mais sobre isso consulte nossos [documentação de Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="secure-your-network-protect"></a>Proteja a sua rede (proteger)
Microsoft Azure inclui uma infraestrutura de rede robusta para suportar a sua aplicação e os requisitos de conectividade do serviço. Conectividade de rede é possível entre recursos localizados no Azure, entre no local e recursos, alojados no Azure e de e para a Internet e o Azure.

O [infraestrutura de rede do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) permite-lhe ligar em segurança recursos do Azure entre si com [redes virtuais (VNets)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Uma VNet é uma representação da sua própria rede na cloud. Uma VNet é um isolamento lógico da rede na cloud do Azure dedicado à sua subscrição. Pode ligar VNets para as suas redes no local.

![Proteja a sua rede (proteger)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

Se precisar de controlo de acesso de nível de rede básica (com base no endereço IP e os protocolos TCP ou UDP), então pode usar [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). Um grupo de segurança de rede (NSG) é um pacote com monitoração de estado básico, filtragem de firewall e permite-lhe controlar o acesso com base numa [5 cadeias de identificação](https://www.techopedia.com/definition/28190/5-tuple).

Redes do Azure suportam a capacidade de personalizar o comportamento de encaminhamento de tráfego de rede nas redes virtuais do Azure. Pode fazer isso configurando [rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) no Azure.

[Túnel forçado](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode utilizar para se certificar de que os seus serviços não têm permissão para iniciar uma ligação para dispositivos na Internet.

O Azure suporta dedicado a conectividade da ligação WAN para sua rede no local e uma rede Virtual do Azure com [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). A ligação entre o Azure e o seu site utiliza uma ligação dedicada que não passa pela Internet pública. Se a aplicação do Azure está em execução em vários datacenters, pode utilizar [Gestor de tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) para encaminhar os pedidos dos utilizadores de forma inteligente entre instâncias da aplicação. Também pode encaminhar o tráfego para serviços não está em execução no Azure se podem ser acedidos a partir da Internet.

## <a name="virtual-machine-security-protect"></a>Segurança de máquinas virtuais (proteger)

[Máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/) permite-lhe implementar uma vasta gama de soluções de computação de forma ágil. Com suporte do Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Serviços BizTalk do Azure, pode implementar qualquer carga de trabalho, qualquer linguagem, em praticamente qualquer sistema operativo.

Com o Azure, pode utilizar [antimalware software](https://docs.microsoft.com/azure/security/azure-security-antimalware) dos fornecedores de segurança, como a Microsoft, Symantec, Trend Micro e Kaspersky para proteger as suas máquinas virtuais contra ficheiros maliciosos, adware e outras ameaças.

O Microsoft Antimalware para serviços Cloud do Azure e máquinas virtuais é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outro software malicioso. Antimalware da Microsoft fornece alertas configuráveis quando conhecido software malicioso ou indesejável se tentar instalar-se ou executar em seus sistemas do Azure.

[O Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) é uma solução dimensionável que protege os dados da aplicação sem investimento de capital e um mínimo os custos operacionais. Os erros das aplicações podem danificar os dados e os erros humanos podem introduzir erros nas suas aplicações. Com o Azure Backup, as máquinas virtuais que executam o Windows e Linux são protegidas.

[O Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) ajuda a orquestrar a replicação, ativação pós-falha e recuperação de cargas de trabalho e aplicações para que eles estejam disponíveis de uma localização secundária se a sua localização principal ficar inativo.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>Certifique-se de conformidade: Serviços devido a lista de verificação de cuidado em nuvem (proteger)

Microsoft desenvolvida [o Cloud Services devida diligência lista de verificação de](https://aka.ms/cloudchecklist.download) para ajudar as organizações exercer devida diligência ao considerar mover para a cloud. Ele fornece uma estrutura para uma organização de qualquer tamanho e tipo — empresas privadas e organizações do setor público, incluindo Governo em todos os níveis e organizações sem fins lucrativos — para identificar o seu próprio desempenho, serviço, gestão de dados e os objetivos de governação e requisitos. Isto permite-los e comparar as ofertas de fornecedores de serviços cloud diferentes, em última análise, que formam a base para um contrato de serviço cloud.

A lista de verificação fornece uma estrutura que se alinha cláusula pela cláusula com uma nova norma internacional para contratos de serviços cloud, ISO/IEC 19086. Este padrão oferece um conjunto unificado de considerações para as organizações para ajudá-los para tomar decisões sobre a adoção da cloud e criar uma base comum para comparar ofertas de serviços cloud.

A lista de verificação promove uma movimentação minuciosamente avaliada para a cloud, fornecendo diretrizes estruturadas e uma abordagem consistente e passível de repetição para escolher um fornecedor de serviços cloud.

Adoção da cloud já não é simplesmente uma decisão de tecnologia. Uma vez que os requisitos de lista de verificação superficialmente a todos os aspectos de uma organização, que servem para convene todas as chaves internos-os tomadores de decisão — o CIO e CISO, bem como informações legais, corre o risco de profissionais de gestão, aprovisionamento e a conformidade. Isso aumenta a eficiência do processo de tomada de decisões e decisões de raiz no som raciocínio, reduzindo assim a probabilidade de obstáculos imprevistos à adoção.

Além disso, a lista de verificação:

- Expõe tópicos de discussão de chave para tomadores de decisão no início do processo de adoção da cloud.

- Suporta discussões de negócios completa sobre os objetivos da organização e regulamentos de privacidade, informações de identificação pessoal (PII) e segurança de dados.

- Ajuda as organizações a identificar problemas potenciais que podem afetar um projeto em nuvem.

- Fornece um conjunto consistente de perguntas, com os mesmos termos, definições, métricas e resultados finais para cada fornecedor, para simplificar o processo de comparação de ofertas de fornecedores de serviços cloud diferentes.

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Azure validação de segurança de infra-estrutura e de aplicativos (detetar)

[Segurança operacional Azure](https://docs.microsoft.com/azure/security/azure-operational-security) refere-se a serviços, controles e recursos disponíveis aos utilizadores para proteger seus dados, aplicações e outros ativos no Microsoft Azure.

![validação de segurança (detetar)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

Segurança operacional do Azure baseia-se uma estrutura que incorpore os dados recolhidos através de um vários recursos que são exclusivos da Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa de centro de resposta de segurança Microsoft e o conhecimento profundo do campo das ameaças de cibersegurança.

### <a name="microsoft-azure-monitor"></a>Monitor do Microsoft Azure

[O Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é a solução de gestão de TI para a cloud híbrida. Usada sozinha ou para expandir a sua implementação existente do System Center, os registos de Monitor do Azure dá-lhe a máxima flexibilidade e o controle para a gestão baseada na nuvem da sua infraestrutura.

![Azure Monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

Com o Azure Monitor, pode gerenciar qualquer instância em qualquer cloud, incluindo no local, Azure, AWS, Windows Server, Linux, VMware e OpenStack, a um custo menor do que soluções competitivas. Criado para o mundo de cloud em primeiro lugar, o Azure Monitor oferece uma nova abordagem para gerir a sua empresa que é a forma mais rápida e rentável para atender aos novos desafios de negócios e acomodar novas cargas de trabalho, aplicações e ambientes na cloud.

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Registos de Monitor do Azure](https://azure.microsoft.com/documentation/services/log-analytics) fornece serviços de monitorização através da recolha de dados de recursos geridos num repositório central. Estes dados podem incluir dados de eventos ou de desempenho ou dados personalizados fornecidos através da API. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação.

![Registos do Azure Monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

Este método permite-lhe consolidar os dados de uma variedade de origens, para que pode combinar dados dos seus serviços do Azure com os seus existentes ambiente no local. Também separa claramente a recolha dos dados das ações tomadas em relação aos mesmos, para que todas as ações estejam disponíveis para todos os tipos de dados.

### <a name="azure-security-center"></a>Centro de Segurança do Azure

O [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

O Centro de Segurança analisa o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Uma lista de recomendações orienta-o no processo de configuração de controlos necessários.

Os exemplos incluem:

- Aprovisionamento de antimalware para ajudar a identificar e remover o software malicioso

- Configurar grupos de segurança de rede e regras para controlar o tráfego para VMs

- Aprovisionamento de firewalls de aplicação Web para ajudar a proteger-se contra ataques que visam as suas Web Apps

- Implementação de atualizações do sistema em falta

- Abordagem de configurações de SO que não correspondam às linhas de base recomendadas

O Centro de Segurança recolhe, analisa e integra automaticamente os dados de registo a partir dos recursos do Azure, da rede e das soluções de parceiros, como os programas antimalware e as firewalls. Quando são detetadas ameaças, é criado um alerta de segurança. Os exemplos incluem a deteção de:

- VMs comprometidas a comunicar com endereços IP maliciosos conhecidos

- Software maligno avançado detetado ao utilizar a comunicação de erros do Windows

- Ataques de força bruta nas VMs

- Alertas de segurança a partir de programas antimalware e firewalls integrados

### <a name="azure-monitor"></a>Azure monitor

[O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) fornece indicadores para informações sobre tipos específicos de recursos. Ele oferece a visualização, consulta, encaminhamento, alertas, dimensionamento automático e automatização nos dados da infraestrutura do Azure (registo de atividade) e cada recurso do Azure individual (registos de diagnóstico).

Aplicações na cloud são complexas com muitas partes móveis. A monitorização fornece dados para garantir que seu aplicativo mantém-se e em execução em bom estado. Também ajuda-o a protele potenciais problemas ou resolução de problemas anteriores são.

![O Azure Monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) além disso, pode utilizar dados de monitorização para obter informações aprofundadas sobre a sua aplicação. Esse conhecimento pode ajudá-lo a melhorar o desempenho da aplicação ou a capacidade de manutenção, ou automatize ações que caso contrário, requer intervenção manual.

A auditoria de segurança de rede é vital para detectar vulnerabilidades de rede e assegurar a conformidade com o modelo de normas de governação e segurança de TI. Com a vista de grupo de segurança, pode recuperar o grupo de segurança de rede configurado e as regras de segurança, bem como as regras de segurança efetivas. Com a lista de regras aplicadas, é possível determinar as portas que estão abertas e ss vulnerabilidade de rede.

### <a name="network-watcher"></a>Observador de rede

[Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de rede no, a e do Azure. Diagnóstico de rede e ferramentas de visualização disponíveis com o observador de rede ajudam a compreender, diagnosticar e obter informações da sua rede no Azure. Este serviço inclui a captura de pacotes, próximo salto, fluxo de IP verificar, a vista de grupo de segurança, os registos de fluxo do NSG. A monitorização ao nível do cenário fornece uma vista de ponta a ponta dos recursos de rede em comparação com a monitorização de recursos de rede individuais.

### <a name="storage-analytics"></a>Análise de armazenamento

[Análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) pode armazenar métricas que incluem dados de estatísticas e a capacidade de agregadas de transações sobre pedidos para um serviço de armazenamento. Transações são comunicadas ao nível da operação ambas API, bem como o nível de serviço de armazenamento e capacidade é comunicada o nível de serviço de armazenamento. Dados de métricas podem ser utilizados para analisar a utilização do serviço de armazenamento, diagnóstico de problemas com pedidos efetuados para o serviço de armazenamento e para melhorar o desempenho das aplicações que utilizam um serviço.

### <a name="application-insights"></a>Application Insights

[O Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de gestão de desempenho de aplicações (APM) extensível para desenvolvedores da web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. Além de detetar automaticamente anomalias de desempenho, Ele inclui ferramentas de análise poderosas para ajudar a diagnosticar problemas e para compreender o que os utilizadores fazem com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade. Funciona para aplicações numa grande variedade de plataformas, incluindo .NET, node. js e Java EE, alojado no local ou na cloud. Ele se integra com o seu processo de devOps e tem pontos de ligação a um várias ferramentas de desenvolvimento.

Monitoriza:

- **Taxas de pedidos, tempos de resposta e taxas de falhas** - saiba quais são as páginas mais populares, em que alturas do dia e onde estão localizados os seus utilizadores. Veja que páginas têm o melhor desempenho. Se os tempos de resposta e as taxas de falhas aumentarem quando há mais pedidos, é possível que tenha um problema relativo a recursos.

- **Taxas de dependência, tempos de resposta e taxas de falhas** - veja se os serviços externos o estão a atrasar.

- **Exceções** - analisar as estatísticas agregadas ou escolha instâncias específicas e explore o rastreio de pilha e pedidos relacionados. São reportadas exceções do servidor e do browser.

- **Vistas de página e desempenho de carga** - reportados pelos browsers dos utilizadores.

- **Chamadas de AJAX a páginas da web** -taxas, tempos de resposta e taxas de falhas.

- **Contagens de utilizadores e de sessão.**

- **Contadores de desempenho** dos computadores de servidor Windows ou Linux, como CPU, memória e utilização de rede.

- **Diagnóstico do anfitrião** do Docker ou do Azure.

- **Registos de rastreio de diagnóstico** da sua aplicação - para que possa correlacionar eventos de rastreio a pedidos.

- **Métricas e eventos personalizados** que escreve manualmente no código de cliente ou servidor, para controlar eventos comerciais, como artigos vendidos, ou jogos ganhos.

Normalmente, a infraestrutura da sua aplicação é composta por vários componentes, como uma máquina virtual, uma conta de armazenamento e uma rede virtual, ou uma aplicação Web, uma base de dados, um servidor de base de dados e serviços de terceiros. Não vê estes componentes como entidades separadas. Em vez disso, vê-os como partes relacionadas e interdependentes de uma única entidade. Deve implementá-los, geri-los e monitorizá-los como um grupo. [O Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permite-lhe trabalhar com os recursos na sua solução como um grupo.

Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Utiliza um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, como de teste e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação.

**As vantagens da utilização do Resource Manager**

O Resource Manager oferece várias vantagens:

- Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

- Pode implementar repetidamente a solução durante todo o ciclo de vida de desenvolvimento e ter a confiança de que os recursos são implementados num estado consistente.

- Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

- Pode definir as dependências entre os recursos, para que sejam implementados na ordem correta.

- Pode aplicar o controlo de acesso a todos os serviços no seu grupo de recursos porque o Controlo de Acesso Baseado em Funções (RBAC) está integrado de forma nativa na plataforma de gestão.

- Pode aplicar etiquetas a recursos para organizar logicamente todos os recursos na sua subscrição.

- Pode clarificar a faturação da sua organização visualizando os custos de um grupo de recursos partilhando a mesma etiqueta.

> [!Note]
> O Resource Manager proporciona uma nova forma de implementar e gerir as suas soluções. Se utilizou o modelo de implementação e quiser anteriores para saber mais sobre as alterações, consulte [Gestor de recursos de compreensão de implementação clássica e](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>Passos Seguintes

Obter mais informações sobre segurança lendo alguns dos nossos tópicos de segurança detalhados:

- [Auditoria e registo](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Cibercrime](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Estrutura e segurança operacional](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Encriptação](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Gestão de identidades e acesso](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Segurança da rede](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Gestão de ameaças](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
