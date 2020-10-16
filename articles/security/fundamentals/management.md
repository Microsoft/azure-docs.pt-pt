---
title: Melhorar a segurança da gestão remota no Azure | Microsoft Docs
description: Este artigo discute medidas para melhorar a segurança da gestão remota enquanto administra ambientes do Microsoft Azure, incluindo serviços na nuvem, máquinas virtuais e aplicações personalizadas.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 2431feba-3364-4a63-8e66-858926061dd3
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2020
ms.author: terrylan
ms.openlocfilehash: 73d82efed438d447c7af3bfc54d5c3fc22cdd819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87921933"
---
# <a name="security-management-in-azure"></a>Gestão de segurança no Azure
Os subscritores do Azure poderão gerir os respetivos ambientes de nuvem a partir de vários dispositivos, incluindo estações de trabalho de gestão, PCs de programadores e, até mesmo, dispositivos de utilizador final com privilégios que tenham permissões específicas de tarefas. Em alguns casos, as funções administrativas são efetuadas através das consolas baseadas na Web, como o [Portal do Azure](https://azure.microsoft.com/features/azure-portal/). Noutros casos, poderão existir ligações diretas para o Azure a partir de sistemas no local através de Redes Privadas Virtuais (VPNs), Serviços de Terminal, protocolos de aplicações cliente ou (através de programação) a API de Gestão de Serviço do Azure (SMAPI). Além disso, os pontos finais de cliente podem ser um domínio associado ou isolado e não gerido, como tablets ou smartphones.

Apesar de estas diversas capacidades de gestão e acesso fornecerem um conjunto avançado de opções, esta variabilidade pode acarretar um risco significativo para uma implementação de nuvem. Pode ser difícil gerir, controlar e auditar as ações administrativas. Este variabilidade também poderá acarretar ameaças de segurança através do acesso não regulado para os pontos finais de cliente que são utilizados para gerir os Cloud Services. A utilização das estações de trabalho gerais ou pessoais para desenvolver e gerir a infraestrutura abre vetores de ameaças imprevisíveis, como navegação na Web (por exemplo, ataques de tipo “watering hole”) ou e-mail (engenharia social e phishing).

![Um diagrama que mostra as diferentes formas de uma ameaça montar um ataque.](./media/management/typical-management-network-topology.png)

A possibilidade de ataques aumenta neste tipo de ambiente, porque é difícil construir políticas de segurança e mecanismos para gerir adequadamente o acesso às interfaces do Azure (por exemplo, SMAPI) a partir de pontos finais bastante diversos.

### <a name="remote-management-threats"></a>Ameaças da gestão remota
Os atacantes frequentemente tentam obter acesso privilegiado comprometendo as credenciais da conta (por exemplo, através de ataque de força bruta às palavras-passe, phishing e recolha de credenciais) ou ludibriando os utilizadores na execução de código prejudicial (por exemplo, em sites prejudiciais conduzidos através de transferências ou em anexos de e-mail prejudiciais). Num ambiente de nuvem gerido remotamente, as falhas da conta podem levar a um maior risco devido a um acesso em qualquer lugar e em qualquer altura.

Mesmo com controlos apertados em contas de administrador principais, podem ser utilizadas contas de utilizador de nível inferior para explorar as fraquezas na estratégia de segurança de cada um. A falta de formação apropriada em segurança também pode levar a falhas através de divulgação ou exposição acidental das informações da conta.

Quando uma estação de trabalho do utilizador também é utilizada para tarefas administrativas, pode ficar comprometida em muitos pontos diferentes. Quer um utilizador esteja a navegar na Web, utilizando ferramentas open source de terceiros, ou a abrir um ficheiro de documento prejudicial que contém um trojan.

Em geral, a maior parte dos ataques direcionados que resultam em falhas de dados pode dever-se a explorações do browser, plug-ins (por exemplo, Flash, PDF, Java) e spear phishing (e-mail) em computadores. Nestes computadores poderão ter permissões de nível administrativo ou nível de serviço para aceder aos servidores em direto ou aos dispositivos de rede de operações, quando utilizados para a programação ou para a gestão de outros recursos.

### <a name="operational-security-fundamentals"></a>Noções básicas da segurança operacional
Para operações e uma gestão mais seguras, pode minimizar a superfície de ataque de um cliente reduzindo o número de pontos de entrada possíveis. Isto pode ser feito através dos princípios de segurança: “separação de funções” e “segregação dos ambientes”.

Isole as funções sensíveis umas das outras para diminuir a probabilidade de um erro num nível conduzir a uma violação noutro. Exemplos:

* As tarefas administrativas não devem ser combinadas com atividades que possam comprometer a segurança (por exemplo, software maligno no e-mail de um administrador que, em seguida, infeta diretamente um servidor da infraestrutura).
* Uma estação de trabalho utilizada para operações de elevada sensibilidade não deve ser o mesmo sistema que é utilizado para finalidades de alto risco como navegação na Internet.

Reduza a superfície de ataque do sistema removendo software desnecessário. Exemplo:

* Uma estação de trabalho administrativa padrão de suporte ou de desenvolvimento não deve exigir a instalação de um cliente de e-mail ou outras aplicações de produtividade, caso o principal objetivo do dispositivo seja gerir Cloud Services.

Os sistemas de cliente que têm acesso de administrador aos componentes da infraestrutura devem estar sujeitos à política mais estrita possível para reduzir riscos de segurança. Exemplos:

* As políticas de segurança podem incluir definições da Política de Grupo que negam o acesso aberto à Internet a partir do dispositivo e utilizar uma configuração de firewall restritiva.
* Utilize VPNs com segurança IPsec seo acesso direto for necessário.
* Configure domínios do Active Directory de gestão e de desenvolvimento separados.
* Isole e filtre o tráfego de rede da estação de trabalho de gestão.
* Utilize software antimalware.
* Implemente o Multi-Factor Authentication para reduzir o risco de credenciais roubadas.

Consolidar os recursos de acesso e eliminar os pontos finais não geridos também simplifica as tarefas de gestão.

### <a name="providing-security-for-azure-remote-management"></a>Fornecer segurança para gestão remota do Azure
O Azure oferece mecanismos de segurança para ajudar os administradores que gerem Virtual Machines e Cloud Services do Azure. Estes mecanismos incluem:

* Autenticação e [controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* Monitorização, registo e auditoria.
* Certificados e comunicações encriptadas.
* Um portal de gestão Web.
* Filtragem de pacotes de rede.

Com a configuração da segurança do lado do cliente e a implementação de datacenter de um gateway de gestão, é possível restringir e monitorizar o acesso de administrador aos dados e às aplicações cloud.

> [!NOTE]
> Algumas recomendações neste artigo poderão resultar numa maior utilização de dados, de rede ou de utilização de recursos de computação e poderão aumentar os custos de licenciamento ou de subscrição.
>
>

## <a name="hardened-workstation-for-management"></a>Estação de trabalho protegida para gestão
O objetivo da proteção de uma estação de trabalho consiste em eliminar todas as funções, exceto as mais críticas, necessárias para que funcione, tornando a possível superfície de ataque tão reduzida quanto possível. A proteção do sistema inclui minimizar o número de aplicações e serviços instalados, limitar a execução das aplicações, restringir o acesso à rede apenas ao necessário e manter o sistema sempre atualizado. Além disso, utilizar uma estação de trabalho para gestão protegida segrega as ferramentas e as atividades administrativas de outras tarefas do utilizador final.

Num ambiente de empresa no local, pode limitar a superfície de ataque da sua infraestrutura física através de redes de gestão dedicadas, salas de servidores com acesso por cartão e estações de trabalho executadas em áreas protegidas da rede. Num modelo de TI híbrido ou na nuvem, ser diligente relativamente aos serviços de gestão protegidos pode ser mais complexo devido à falta de acesso físico aos recursos de TI. Implementar soluções de proteção requer uma cuidada configuração do software, processos centrados na segurança e políticas abrangentes.

Utilizar requisitos de espaço de software reduzidos de menor privilégio numa estação de trabalho bloqueada para gestão da cloud (bem como para o desenvolvimento de aplicações) pode reduzir o risco de incidentes de segurança ao uniformizar os ambientes de gestão e de desenvolvimento remotos. Uma configuração da estação de trabalho protegida pode ajudar a evitar que as contas utilizadas para gerir recursos de nuvem críticos fiquem comprometidas fechando muitas vias comuns utilizadas pelo software maligno e pelos exploits. Especificamente, pode utilizar o [Windows AppLocker](https://technet.microsoft.com/library/dd759117.aspx) e a tecnologia Hyper-V para controlar e isolar o comportamento do sistema cliente e mitigar as ameaças, incluindo e-mail ou navegação na Internet.

Numa estação de trabalho protegida, o administrador executa uma conta de utilizador padrão (o que bloqueia a execução de nível administrativo) e as aplicações associadas são controladas mediante uma lista de permissões. Os elementos básicos de uma estação de trabalho protegida são os seguintes:

* Análise ativa e aplicação de patches. Implemente o software antimalware, efetue análises de vulnerabilidade regulares e atualize todas as estações de trabalho utilizando a atualização de segurança mais recente atempadamente.
* Funcionalidade limitada. Desinstale todas as aplicações que não são necessárias e desative os serviços desnecessários (arranque).
* Proteção da rede. Utilize as regras de Firewall do Windows para permitir apenas endereços IP, portas e URLs válidos relacionados com a gestão do Azure. Certifique-se de que as ligações remotas de entrada para a estação de trabalho também estão bloqueadas.
* Restrição de execução. Permita apenas um conjunto de ficheiros executáveis predefinidos que são necessários para serem executados para a gestão (referido como “predefinição-negar”). Por predefinição, os utilizadores não devem ter permissão para executar programas, a menos que estes esteja explicitamente definido na lista de permissões.
* Menor privilégio. Os utilizadores da estação de trabalho de gestão não devem ter privilégios administrativos no computador local. Desta forma, não podem alterar a configuração do sistema ou dos ficheiros de sistema, intencional ou acidentalmente.

Pode impor tudo isto através de [Objetos de Política de Grupo](../../active-directory-domain-services/manage-group-policy.md) (GPOs) no Active Directory Domain Services (AD DS) e aplicá-los através do seu domínio de gestão (local) a todas as contas de gestão.

### <a name="managing-services-applications-and-data"></a>Gerir serviços, aplicações e dados
A configuração dos Cloud Services do Azure é efetuada através do Portal do Azure ou do SMAPI, através da interface de linha de comandos do Windows PowerShell ou de uma aplicação personalizada que tira partido destas interfaces RESTful. Os serviços com estes mecanismos incluem o Azure Active Directory (Azure AD), o Storage do Azure, os Web Sites do Azure, a Azure Virtual Network, entre outros.

Máquina Virtual: as aplicações implementadas fornecem as suas próprias ferramentas e interfaces conforme necessário, como, por exemplo, a Consola de Gestão da Microsoft (MMC), uma consola de gestão Empresarial (tal como o Microsoft System Center ou o Windows Intune) ou outra aplicação de gestão, por exemplo, Microsoft SQL Server Management Studio. Estas ferramentas normalmente residem num ambiente empresarial ou numa rede do cliente e podem depender de protocolos de rede específicos, como o protocolo RDP (Remote Desktop Protocol), que necessitam de ligações diretas com monitorização de estado. Algumas poderão ter interfaces Web que não devem ser acessíveis através da Internet ou publicadas abertamente.

Pode restringir o acesso à gestão dos serviços de infraestrutura e plataforma no Azure através da utilização de [autenticação multifator](/azure/active-directory/authentication/multi-factor-authentication), [certificados de gestão X.509](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/) e regras de firewall. O Portal do Azure e o SMAPI requerem TLS (Transport Layer Security). No entanto, os serviços e as aplicações que implementa no Azure exigem que tome as medidas de proteção adequadas com base na sua aplicação. Estes mecanismos podem ser ativados frequentemente de modo mais fácil através de uma configuração da estação de trabalho protegida normalizada.

### <a name="management-gateway"></a>Gateway de gestão
Para centralizar todo o acesso administrativo e simplificar a monitorização e o registo, pode implementar um servidor dedicado do [Gateway de Ambiente de Trabalho Remoto](https://technet.microsoft.com/library/dd560672) (Gateway de RD) na rede no local, ligado ao ambiente do Azure.

Um Gateway de ambiente de trabalho remoto é um serviço proxy baseado na política RDP que impõe requisitos de segurança. Implementar o Gateway de RD juntamente com o Windows Server Network Access Protection (NAP) ajuda a garantir que apenas os clientes que cumprem os critérios de estado de funcionamento de segurança específicos estabelecidos pelo Objetos de Política de Grupo (GPOs) dos Serviços de Domínio do Active Directory (AD DS) podem ligar-se. Além disso:

* Aprovisione um [certificado de gestão do Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx) no Gateway de RD, para que seja o único anfitrião autorizado a aceder ao portal do Azure.
* Associe o Gateway de RD ao mesmo [domínio de gestão](https://technet.microsoft.com/library/bb727085.aspx) que o das estações de trabalho do administrador. Isto é necessário quando estiver a utilizar o ExpressRoute ou a rede de VPNs IPsec dentro de um domínio que tenha uma confiança unidirecional para o Azure AD, ou se forem credenciais de federação entre a instância do AD DS no local e o Azure AD.
* Configure uma [política de autorização de ligações do cliente](https://technet.microsoft.com/library/cc753324.aspx) para permitir que o Gateway de RD verifique se o nome do computador cliente é válido (associado ao domínio) e se tem permissão para aceder ao portal do Azure.
* Utilize IPsec para a [VPN do Azure](https://azure.microsoft.com/documentation/services/vpn-gateway/) para obter mais proteção de tráfego de gestão contra a espionagem e o furto de tokens ou considere uma ligação da Internet isolada através do [Azure ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).
* Ative o Multi-Factor Authentication (através do [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)) ou a autenticação por smart card para administradores que iniciem sessão através do Gateway de RD.
* Configure as [restrições de endereço IP](https://azure.microsoft.com/blog/2013/08/27/confirming-dynamic-ip-address-restrictions-in-windows-azure-web-sites/) da origem ou o [Grupos de Segurança de Rede](/azure/virtual-network/security-overview) no Azure para minimizar o número de pontos finais de gestão permitidos.

## <a name="security-guidelines"></a>Diretrizes de segurança
Em geral, ajudar a proteger estações de trabalho do administrador para utilização com a cloud é semelhante às práticas utilizadas em qualquer estação de trabalho no local, como, por exemplo, compilação minimizada e permissões restritivas. Alguns aspetos exclusivos da gestão de nuvem são mais comparáveis à gestão empresarial remota ou sem rede IP. Estes incluem a utilização e auditoria de credenciais, acesso remoto com segurança avançada e deteção de ameaças e resposta.

### <a name="authentication"></a>Autenticação
Pode utilizar as restrições de início de sessão do Azure para restringir os endereços IP de origem de acederem às ferramentas administrativas e aos pedidos de acesso de auditoria. Para ajudar o Azure a identificar clientes de gestão (estações de trabalho e/ou aplicações), pode configurar tanto o SMAPI (através de ferramentas desenvolvidas pelo cliente, como os cmdlets Windows PowerShell) como o portal Azure para exigir a instalação de certificados de gestão do lado do cliente, além dos certificados TLS/SSL. Recomendamos também que o acesso de administrador exija uma autenticação multifator.

Algumas aplicações ou serviços que implementa no Azure podem ter os seus próprios mecanismos de autenticação para acesso de administrador e de utilizador final, enquanto que outros tiram o máximo partido do Azure AD. Dependendo se está a utilizar credenciais de federação através doa Serviços de Federação do Active Directory (AD FS), a utilizar a sincronização de diretórios ou a gerir contas de utilizador apenas na nuvem, utilizar o [Microsoft Identity Manager](https://technet.microsoft.com/library/mt218776.aspx) (parte do Azure AD Premium) ajuda-o a gerir os ciclos de tempo de identidade entre os recursos.

### <a name="connectivity"></a>Conectividade
Estão disponíveis diversos mecanismos para o ajudar a efetuar ligações de cliente seguras às suas Azure Virtual Networks. Dois destes mecanismos, a [ede de VPNs](https://channel9.msdn.com/series/Azure-Site-to-Site-VPN) (S2S) e a [VPN ponto a site](/azure/vpn-gateway/vpn-gateway-point-to-site-create) (P2S), permitem a utilização do padrão de indústria IPsec (S2S) ou o [Secure Socket Tunneling Protocol](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) (SSTP) (P2S) para encriptação e túnel. Quando o Azure está a ligar à gestão de serviços do Azure destinados ao público, como o portal do Azure, o Azure requer HTTPS (Hypertext Transfer Protocol Secure).

Uma estação de trabalho autónoma protegida que não se ligar ao Azure através de um Gateway de RD deve utilizar a VPN ponto a site baseada em SSTP para criar a ligação inicial à Rede Virtual do Azure e, em seguida, estabelecer a ligação RDP a máquinas virtuais individuais a partir do túnel VPN.

### <a name="management-auditing-vs-policy-enforcement"></a>Auditoria de gestão versus aplicação de políticas
Normalmente, existem duas abordagens para ajudar a proteger os processos de gestão: a auditoria e a aplicação de políticas. Utilizar as duas abordagens proporciona controlos abrangentes, mas pode não ser possível em todas as situações. Além disso, cada abordagem tem diferentes níveis de risco, custos e esforços associados à gestão da segurança, especialmente porque estão relacionados ao nível de confiança colocados nos indivíduos e nas arquiteturas do sistema.

A monitorização, o registo e a auditoria fornecem uma base para controlar e compreender as atividades administrativas, mas podem nem sempre ser viáveis para auditar todas as ações em detalhe devido à quantidade de dados gerados. A auditoria da eficácia das políticas de gestão é a melhor prática.

A aplicação de políticas que inclui os controlos de acesso restritos estabelece os mecanismos programáticos que podem reger as ações do administrador e ajuda a garantir que todas as medidas de proteção possíveis estão a ser utilizadas. O registo fornece uma prova da aplicação, para além de um registo de quem fez o quê, a partir de onde e quando. O registo também permite auditar e verificar as informações sobre a forma como os administradores seguem as políticas, além de fornecer uma evidência das atividades

## <a name="client-configuration"></a>Configuração do cliente
Recomendamos três configurações primárias para uma estação de trabalho protegida. Os maiores diferenciadores entre essas configurações são o custo, a utilização e a acessibilidade, embora mantendo um perfil de segurança semelhante em todas as opções. A tabela seguinte fornece uma breve análise dos benefícios e riscos de cada uma. (Tenha em atenção que “PC empresarial” refere-se a uma configuração padrão do PC de secretária que seria implementada para todos os utilizadores do domínio, independentemente das funções.)

| Configuração | Benefícios | Desvantagens |
| --- | --- | --- |
| Estação de trabalho autónoma protegida |Estação de trabalho controlada de forma apertada |Custo mais elevado para os computadores dedicados |
| - | Risco reduzido de explorações de aplicações |Esforço de gestão aumentado |
| - | Clara separação das funções | - |
| PC empresarial como máquina virtual |Custos de hardware reduzidos | - |
| - | Segregação da função e das aplicações | - |

É importante que a estação de trabalho protegida seja o anfitrião e não o convidado, sem nada entre o sistema operativo anfitrião e o hardware. Seguir o “princípio de origem limpo” (também conhecido como “origem segura”) significa que o anfitrião deve ser o mais protegido. Caso contrário, a estação de trabalho protegida (convidado) está sujeita a ataques no sistema em que está alojada.

Pode segregar mais as funções administrativas através de imagens dedicadas do sistema para cada estação de trabalho protegida que tenha apenas as ferramentas e as permissões necessárias para gerir o Azure e as aplicações em nuvem, com GPOs do DS AD local específicos para as tarefas necessárias.

Para ambientes de TI que não tenham nenhuma infraestrutura no local (por exemplo, sem acesso a uma instância local do AD DS para GPOs, porque todos os servidores estão na nuvem), um serviço como o [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx) pode simplificar a implementação e a gestão das configurações das estação de trabalho.

### <a name="stand-alone-hardened-workstation-for-management"></a>Estação de trabalho autónoma protegida para gestão
Com uma estação de trabalho autónoma protegida, os administradores têm um PC ou portátil que podem utilizar para as tarefas administrativas e outro PC ou portátil separado para as tarefas não administrativas. Uma estação de trabalho dedicada para gerir os serviços do Azure não necessita de outras aplicações instaladas. Além disso, utilizar estações de trabalho que suportam um [Trusted Platform Module](https://technet.microsoft.com/library/cc766159) (TPM) ou tecnologia de criptografia ao nível do hardware semelhante ajuda na autenticação dos dispositivos e na prevenção de determinados ataques. O TPM também pode suportar proteção de volume completo da unidade do sistema ao utilizar a [Encriptação de Unidade BitLocker](https://technet.microsoft.com/library/cc732774.aspx).

No cenário de estação de trabalho autónoma protegida (mostrado abaixo), a instância local do Firewall do Windows (ou uma firewall para cliente que não seja da Microsoft) está configurada para bloquear as ligações de entrada, tais como RDP. O administrador pode iniciar sessão na estação de trabalho protegida e iniciar uma sessão do RDP que liga ao Azure depois de estabelecer uma ligação VPN com uma Azure Virtual Network, mas não pode iniciar sessão num PC empresarial e utilizar o RDP para ligar à estação de trabalho protegida.

![Um diagrama que mostra o cenário de estação de trabalho endurecido autónomo.](./media/management/stand-alone-hardened-workstation-topology.png)

### <a name="corporate-pc-as-virtual-machine"></a>PC empresarial como máquina virtual
Nos casos em que uma estação de trabalho autónoma protegida separada tem custos proibitivos ou impraticáveis, a estação de trabalho protegida pode alojar uma máquina virtual para efetuar as tarefas não administrativas.

![Um diagrama que mostra a estação de trabalho endurecida que alberga uma máquina virtual para executar tarefas não administrativas.](./media/management/hardened-workstation-enabled-with-hyper-v.png)

Para evitar várias riscos de segurança que podem surgir da utilização de uma estação de trabalho para gestão de sistemas e outras tarefas de trabalho diárias, pode implementar uma máquina virtual de Hyper-V do Windows para a estação de trabalho protegida. Esta máquina virtual pode ser utilizada como PC empresarial. O ambiente do PC empresarial pode permanecer isolado do Anfitrião, o que reduz a superfície de ataque e faz com que as atividades diárias do utilizador (por exemplo, o e-mail) deixem de coexistir com as tarefas administrativas sensíveis.

A máquina virtual do PC empresarial é executada num espaço protegido e fornece as aplicações de utilizador. O anfitrião continua a ser uma “origem limpa” e aplica as políticas de rede restritas no sistema operativo raiz (por exemplo, bloqueio do acesso RDP a partir da máquina virtual).

## <a name="best-practices"></a>Melhores práticas
Considere as seguintes diretrizes adicionais quando estiver a gerir aplicações e dados no Azure.

### <a name="dos-and-donts"></a>O que deve fazer e o que não deve fazer
Não parta do princípio que, pelo facto de uma estação de trabalho estar bloqueada, os outros requisitos de segurança comuns não precisam de ser aplicados. O risco potencial é superior devido aos níveis de acesso elevados que geralmente as contas de administrador possuem. Os exemplos de riscos e as respetivas práticas de segurança alternativas são apresentados na tabela abaixo.

| O que não deve fazer | O que deve fazer |
| --- | --- |
| Não envie por e-mail credenciais para acesso a administrador ou outros segredos (por exemplo, TLS/SSL ou certificados de gestão) |Mantenha a confidencialidade entregando os nomes de conta e as palavras-passe pessoalmente ou por telefone (mas não os armazenando num voice mail); efetue uma instalação remota dos certificados de cliente/servidor (através de uma sessão encriptada); transfira a partir de uma partilha de rede protegida ou distribua manualmente através do suporte de dados amovível. |
| - | A gestão dos ciclos de vida dos certificados de gestão deve ser feita de forma pró-ativa. |
| Não armazene palavras-passe da conta não encriptadas ou sem hash no armazenamento de aplicações (como folhas de cálculo, sites do SharePoint ou partilhas de ficheiros). |Estabeleça princípios de gestão de segurança e políticas de proteção do sistema e aplique-os ao ambiente de desenvolvimento. |
| - | Utilize o certificado [Enhanced Mitigation Experience Toolkit 5.5](https://technet.microsoft.com/security/jj653751) com afixação de regras para garantir um acesso adequado aos sites do SSL/TLS do Azure. |
| Não partilhe as contas ou as palavras-passe entre administradores nem reutilize palavras-passe em várias contas de utilizador ou serviços, especialmente nas redes sociais ou para outras atividades não administrativas. |Crie uma conta Microsoft dedicada para gerir a subscrição do Azure, uma conta que não seja utilizada para o e-mail pessoal. |
| Não envie por e-mail os ficheiros de configuração. |Os perfis e ficheiros de configuração devem ser instalados a partir de uma origem fidedigna (por exemplo, uma pen USB encriptada) e não a partir de um mecanismo que possa ficar facilmente comprometido, como o e-mail. |
| Não utilize palavras-passe de início de sessão fracas ou simples. |Aplique políticas de palavras-passe seguras, ciclos de expiração (mudança na primeira utilização), tempos limite da consola e bloqueios automáticos das contas. Utilize um sistema de gestão de palavras-passe cliente com Multi-Factor Authentication para acesso ao cofre de palavras-passe. |
| Não exponha as portas de gestão à Internet. |Bloqueie as portas e os endereços IP do Azure para restringir o acesso de gestão. Para obter mais informações, consulte o documento técnico [Segurança de Rede do Azure](https://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx). |
| - | Utilize firewalls, VPNs e NAP para todas as ligações de gestão. |

## <a name="azure-operations"></a>Operações do Azure
Na operação do Microsoft Azure, os engenheiros de operações e o pessoal do suporte que acedem aos sistemas de produção do Azure utilizam [PCs de secretária protegidos com VMs](#stand-alone-hardened-workstation-for-management) com aprovisionamento para o acesso à rede empresarial interna e às respetivas aplicações (como e-mail, intranet, etc.). Todos os computadores da estação de trabalho de gestão têm TPMs, a unidade de arranque do anfitrião é encriptada com BitLocker e estão associados a uma unidade organizacional (UO) especial no domínio empresarial principal da Microsoft.

A proteção do sistema é aplicada através da Política de Grupo, com a atualização de software centralizada. Para auditoria e análise, os registos de eventos (por exemplo, segurança e AppLocker) são recolhidos a partir das estações de trabalho de gestão e guardados numa localização central.

Além disso, as caixas de atalhos dedicadas na rede da Microsoft que necessitam da autenticação de dois fatores são utilizadas para ligar à rede de produção do Azure.

## <a name="azure-security-checklist"></a>Lista de verificação de segurança do Azure
Minimizar o número de tarefas que os administradores podem realizar numa estação de trabalho protegida ajuda a minimizar a superfície de ataque no ambiente de gestão e desenvolvimento. Utilize as seguintes tecnologias para ajudar a proteger a estação de trabalho protegida:

* Proteção do IE. O browser Internet Explorer (ou qualquer browser para esse fim) é um ponto de entrada chave para código prejudicial devido às suas interações extensas com servidores externos. Reveja as suas políticas de cliente e aplique a execução no modo protegido, desativando os suplementos, desativando as transferências de ficheiros e utilizando a filtragem [Microsoft SmartScreen](https://technet.microsoft.com/library/jj618329.aspx). Certifique-se de que os avisos de segurança são apresentados. Tire partido das zonas de Internet e crie uma lista de sites fidedignos para os quais configurou uma proteção razoável. Bloqueie todos os outros sites e códigos no browser, tal como ActiveX e Java.
* Utilizador padrão. A execução como utilizador normal proporciona um número de benefícios, fazendo com que o roubo de credenciais de administrador através de software maligno se torne mais difícil. Além disso, uma conta de utilizador padrão não tem privilégios elevados no sistema operativo raiz e muitas APIs e opções de configuração são bloqueadas por predefinição.
* AppLocker. Pode utilizar o [AppLocker](https://technet.microsoft.com/library/ee619725.aspx) para restringir os programas e os scripts que os utilizadores podem executar. Pode executar o AppLocker no modo de auditoria ou de imposição. Por predefinição, o AppLocker tem uma regra que permite aos utilizadores que tenham um token de administrador a execução de todos os códigos no cliente. Esta regra existe para impedir que os administradores se bloqueiem a si próprios, e aplica-se apenas ao tokens elevados. Consulte também Integridade do Código como parte da [segurança de núcleo](https://technet.microsoft.com/library/dd348705.aspx) do Windows Server.
* Assinatura de código. A assinatura de código de todas as ferramentas e scripts utilizadas por administradores fornece um mecanismo gerível para a implementação de políticas de bloqueio de aplicações. Os hashes não se ajustam com alterações rápidas ao código e os caminhos de ficheiros não fornecem um elevado nível de segurança. Deve combinar regras do AppLocker com uma [política de execução](https://technet.microsoft.com/library/ee176961.aspx) do PowerShell que só permite o código de assinatura específico e os scripts a serem [executados](https://technet.microsoft.com/library/hh849812.aspx).
* Política de Grupo. Crie uma política administrativa global que seja aplicada a qualquer estação de trabalho do domínio utilizada para gestão (e bloqueie o acesso de todos os outros utilizadores) e às contas de utilizador autenticadas nessas estações de trabalho.
* Aprovisionamento de segurança melhorada. Salvaguarde a imagem da estação de trabalho protegida de linha de base para ajudar a proteger contra adulteração. Utilize medidas de segurança, como a encriptação e o isolamento, para armazenar imagens, máquinas virtuais e scripts e restringir o acesso (utilize talvez um processo auditável de entrada/saída).
* Aplicação de patches. Mantenha uma compilação consistente (ou tenha imagens separadas para o desenvolvimento, as operações e outras tarefas administrativas), procure alterações e software maligno regularmente, mantenha atualizada a compilação e ative apenas os computadores quando for necessário.
* Encriptação. Certifique-se de que as estações de trabalho de gestão têm um TPM para ativar de forma mais segura o [Sistema de Encriptação de Ficheiros](https://technet.microsoft.com/library/cc700811.aspx) (EFS) e o BitLocker.
* Governação. Utilize os GPOs do AD DS para controlar todas as interfaces Windows dos administradores, como a partilha de ficheiros. Inclua estações de trabalho de gestão nos processos de auditoria, monitorização e de registo. Controle todos os acessos e utilizações de administrador e programador.

## <a name="summary"></a>Resumo
Utilizar uma configuração de estação de trabalho protegida para administrar os Cloud Services, as Virtual Machines e as aplicações do Azure pode ajudar a evitar vários riscos e ameaças que podem resultar da gestão remota de uma infraestrutura de TI crítica. Tanto o Azure como o Windows fornecem mecanismos que pode utilizar para ajudar a proteger e controlar as comunicações, a autenticação e o comportamento do cliente.

## <a name="next-steps"></a>Passos seguintes
Os recursos seguintes estão disponíveis para fornecer informações mais gerais acerca do Azure e dos serviços relacionados da Microsoft, para além de itens específicos referenciados neste documento:

* [Proteger o Acesso Privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access) – obtenha os detalhes técnicos para estruturação e criação de uma estação de trabalho administrativa segura para gestão do Azure
* [Centro de Confiança da Microsoft](https://microsoft.com/en-us/trustcenter/cloudservices/azure) – saiba mais acerca das capacidades da plataforma Azure que protegem os recursos de infraestrutura do Azure e as cargas de trabalho que são executadas no Azure
* [Microsoft Security Response Center](https://www.microsoft.com/msrc) – onde podem ser comunicadas as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, por e-mail para [secure@microsoft.com](mailto:secure@microsoft.com)
