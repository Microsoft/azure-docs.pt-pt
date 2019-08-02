---
title: Proteger a administração remota do seu gateway na Austrália do Azure
description: Orientação sobre como configurar a administração remota segura nas regiões australianas para atender aos requisitos específicos da política, regulamentos e legislações do governo australiano.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 827dffc1c7544d9373b5f8d4426ea8c448fa25ab
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571604"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>Proteger a administração remota do seu gateway na Austrália do Azure

É fundamental para a disponibilidade e a integridade de qualquer sistema que as atividades administrativas sejam conduzidas com segurança e sejam controladas. As atividades administrativas devem ser feitas em um dispositivo seguro, em uma conexão segura e serem apoiadas por processos de autenticação e autorização fortes. A administração remota segura garante que somente as ações autorizadas sejam executadas e somente por administradores autorizados.

Este artigo fornece informações sobre como implementar um recurso de administração remota segura para um sistema acessível pela Internet hospedado no Azure que se alinha com as diretrizes do consumidor australiano do ACSC (centro de segurança da Austrália) e a intenção das informações do ACSC Manual de segurança (ISM).

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Requisitos de ACSC (centro de segurança da Austrália) australiano

Os requisitos de segurança gerais para sistemas da Comunidade são definidos no ISM. Para auxiliar as entidades da Comunidade no fornecimento de administração segura, o [acsc publicou o acsc Protect: Administração segura](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

Este documento discute a importância da administração segura e sugere um método de implementação de um ambiente de administração seguro. O documento descreve os elementos de uma solução de administração segura da seguinte maneira:

|Elemento   |Descrição   |
|---|---|
|Controle de acesso privilegiado   |Controlar o acesso a contas com privilégios é um controle de segurança fundamental que protegerá contas privilegiadas contra uso indevido. A metodologia de controle de acesso abrange os conceitos de ' privilégios mínimos ' e ' precisa ter ', bem como processos e procedimentos para gerenciar contas de serviço e movimentos da equipe.   |
|Autenticação multifator   |A implementação de fatores adicionais de autenticação além de nomes de acessadores e senhas, como tokens físicos ou cartões inteligentes, pode ajudar a proteger ativos críticos. Se um adversário comprometer as credenciais para contas com privilégios, uma vez que todas as ações administrativas precisarão passar por alguma forma de autenticação multifator, as consequências podem ser bastante reduzidas.|
|Estações de trabalho privilegiadas|O uso de um ambiente seguro conhecido para tarefas administrativas pode resultar em menos risco de comprometimento da rede devido à implementação de controles de segurança adicionais.|
|Registro em log e auditoria   |A geração automatizada, a coleta e a análise de segurança e de eventos relacionados a administradores de estações de trabalho, servidores, dispositivos de rede e caixas de salto permitirão a detecção de comprometimentos e tentativas de comprometimento. A automação permite que as organizações respondam mais rapidamente, reduzindo as implicações de um comprometimento.|
|Segmentação e diferenciação de rede|Segmentar uma rede em zonas lógicas, como diferentes domínios de segurança, e separar ainda mais essas redes lógicas, restringindo os tipos de dados que fluem de uma zona para outra, restringe a movimentação lateral. A segmentação impede que um adversário obtenha acesso a recursos adicionais.|
|Caixas de salto|Uma caixa de salto é um servidor de acesso remoto protegido, geralmente utilising o software Serviços de Área de Trabalho Remota ou Secure Shell (SSH) da Microsoft. As caixas de salto atuam como um ponto de etapa para os administradores que acessam sistemas críticos com todas as ações administrativas executadas do host dedicado.|
|

Este artigo fornece uma arquitetura de referência sobre como os elementos acima podem ser usados para a administração segura de sistemas implantados no Azure.

## <a name="architecture"></a>Arquitetura

Fornecer um recurso de administração segura requer vários componentes que trabalham juntos para formar uma solução coesa. Na arquitetura de referência fornecida, os componentes são mapeados para os elementos descritos [em acsc Protect: Administração segura](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

![Arquitetura de administração remota segura do Azure](media/remote-admin.png)

## <a name="components"></a>Componentes

A arquitetura foi projetada para garantir que uma conta com privilégios receba apenas as permissões necessárias, seja identificada com segurança e, em seguida, fornecido acesso a interfaces administrativas somente de um dispositivo autorizado e por meio de comunicações seguras mecanismos que são controlados e auditados.

|Solução| Componentes|Elementos|
|---|---|---|
|Proteger dispositivos |<ul><li>Estação de trabalho privilegiada</li><li>Dispositivo móvel</li><li>Microsoft Intune</li><li>Política de Grupo</li><li>Servidor de salto/host de bastiões</li><li>Administração just in time (JIT)</li></ul> |<ul><li>Estações de trabalho privilegiadas</li><li>Caixas de salto</li></ul>|
|Comunicação Segura |<ul><li>Portal do Azure</li><li>Gateway de VPN do Azure</li><li>Gateway de Área de Trabalho Remota (RD)</li><li>NSGs (grupos de segurança de rede)</li></ul> |<ul><li>Segmentação e diferenciação de rede</li></ul>|
|Autenticação forte |<ul><li>Controlador de domínio (DC)</li><li>Azure Active Directory (Azure AD)</li><li>Servidor de políticas de rede (NPS)</li><li>MFA do Azure</li></ul> |<ul><li>Autenticação multifator</li></ul> |
|Autorização forte |<ul><li>IAM (gerenciamento de identidade e acesso)</li><li>Privileged Identity Management (PIM)</li><li>Acesso Condicional</li></ul>|<ul><li>Controle de acesso privilegiado</li></ul>|
|||

>[!NOTE]
>Para obter mais informações sobre o elemento logging e Auditing, consulte o artigo sobre [log, auditoria e visibilidade do gateway](gateway-log-audit-visibility.md)

## <a name="administration-workflow"></a>Fluxo de trabalho de administração

A administração de sistemas implantados no Azure é dividida em duas categorias distintas, administrando a configuração do Azure e administrando as cargas de trabalho implantadas no Azure. A configuração do Azure é realizada por meio do portal do Azure e a administração da carga de trabalho é concluída por meio de mecanismos administrativos, como protocolo RDP (RDP), Secure Shell (SSH) ou para recursos de PaaS, usando ferramentas como o SQL Management Studio.

Obter acesso para administração é um processo de várias etapas que envolve os componentes listados na arquitetura e requer acesso ao portal do Azure e à configuração do Azure antes que o acesso possa ser feito para as cargas de trabalho do Azure.

>[!NOTE]
> As etapas descritas aqui são o processo geral usando os componentes GUI (interface gráfica do usuário) do Azure. Essas etapas também podem ser concluídas usando outras interfaces, como o PowerShell.

### <a name="azure-configuration-and-azure-portal-access"></a>Configuração do Azure e acesso de portal do Azure

|Passo |Descrição |
|---|---|
|Entrada de estação de trabalho privilegiada |O administrador faz logon na estação de trabalho privilegiada usando credenciais administrativas. Os controles de Política de Grupo impedem que contas não administrativas se autentiquem na estação de trabalho privilegiada e impede que as contas administrativas se autentiquem em estações de trabalho sem privilégios. O Microsoft Intune gerencia a conformidade da estação de trabalho privilegiada para garantir que ela esteja atualizada com patches de software, Antimalware e outros requisitos de conformidade. |
|portal do Azure entrar |O administrador abre um navegador da Web para a portal do Azure, que é criptografada usando TLS (segurança de camada de transporte) e entra no usando credenciais administrativas. A solicitação de autenticação é processada por meio de Azure Active Directory diretamente ou por meio de mecanismos de autenticação, como Serviços de Federação do Active Directory (AD FS) (AD FS) ou autenticação de passagem. |
|MFA do Azure |O Azure MFA envia uma solicitação de autenticação para o dispositivo móvel registrado da conta privilegiada. O dispositivo móvel é gerenciado pelo Intune para garantir a conformidade com os requisitos de segurança. O administrador deve autenticar primeiro para o dispositivo móvel e, em seguida, para o aplicativo Microsoft Authenticator usando um PIN ou um sistema biométrico antes que a tentativa de autenticação seja autorizada a MFA do Azure. |
|Acesso Condicional |As políticas de acesso condicional verificam a tentativa de autenticação para garantir que ela atenda aos requisitos necessários, como o endereço IP do qual a conexão está vindo, a associação de grupo para a conta com privilégios e o status de conformidade e gerenciamento do Estação de trabalho privilegiada conforme relatado pelo Intune. |
|Privileged Identity Management (PIM) |Por meio do portal do Azure o administrador agora pode ativar ou solicitar ativação para as funções privilegiadas para as quais elas têm autorização por meio do PIM. O PIM garante que as contas privilegiadas não tenham privilégios administrativos em posição e que todas as solicitações de acesso privilegiado sejam apenas para o tempo necessário para executar tarefas administrativas. O PIM também fornece registro em log de todas as solicitações e ativações para fins de auditoria. |
|Gestão de Identidades e Acessos|Depois que a conta privilegiada tiver sido identificada com segurança e as funções ativadas, o administrador receberá acesso às assinaturas e aos recursos do Azure para os quais foram atribuídas permissões por meio do gerenciamento de acesso e identidade.|
|

Depois que a conta privilegiada tiver concluído as etapas para obter acesso administrativo ao portal do Azure, o acesso às cargas de trabalho poderá ser configurado e as conexões administrativas poderão ser feitas.

### <a name="azure-workload-administration"></a>Administração de carga de trabalho do Azure

|Passo |Descrição|
|---|---|
|Acesso just in time (JIT)|Para obter acesso a máquinas virtuais, o administrador usa o JIT para solicitar acesso ao RDP para o servidor de salto do endereço IP do gateway de área de trabalho remota e RDP ou SSH do servidor de salto para as máquinas virtuais de carga de trabalho relevantes.|
|Gateway de VPN do Azure|O administrador agora estabelece uma conexão VPN IPSec ponto a site de sua estação de trabalho privilegiada para o gateway de VPN do Azure, que executa a autenticação de certificado para estabelecer a conexão.|
|Gateway de área de trabalho remota|O administrador agora tenta uma conexão RDP com o servidor de salto com o gateway de área de trabalho remota especificado na configuração de Conexão de Área de Trabalho Remota. O gateway de área de trabalho remota tem um endereço IP privado acessível por meio da conexão de gateway de VPN do Azure. As políticas no gateway de RD controlam se a conta com privilégios está autorizada a acessar o servidor de salto solicitado. O gateway de área de trabalho remota solicita as credenciais do administrador e encaminha a solicitação de autenticação para o servidor de diretivas de rede (NPS).|
|Servidor de políticas de rede (NPS)|O NPS recebe a solicitação de autenticação do gateway de área de trabalho remota e valida o nome de usuário e a senha em relação a Active Directory antes de enviar uma solicitação para Azure Active Directory para disparar uma solicitação de autenticação do Azure MFA.|
|MFA do Azure|O Azure MFA envia uma solicitação de autenticação para o dispositivo móvel registrado da conta privilegiada. O dispositivo móvel é gerenciado pelo Intune para garantir a conformidade com os requisitos de segurança. O administrador deve autenticar primeiro para o dispositivo móvel e, em seguida, para o aplicativo Microsoft Authenticator usando um PIN ou um sistema biométrico antes que a tentativa de autenticação seja autorizada a MFA do Azure.|
|Servidor de salto|Depois de autenticado com êxito, a conexão RDP é criptografada usando o protocolo TLS e, em seguida, enviada por meio do túnel IPSec criptografado para o gateway de VPN do Azure, por meio do gateway de área de trabalho remota e no servidor de salto. No servidor de salto, o administrador pode agora RDP ou SSH para as máquinas virtuais de carga de trabalho, conforme especificado na solicitação JIT.|
|

## <a name="general-guidance"></a>Documentação de orientação geral

Ao implementar os componentes listados neste artigo, as seguintes diretrizes gerais se aplicam:

* Valide a disponibilidade da região dos serviços, garantindo que todos os dados permaneçam dentro dos locais autorizados e implantem na AU central ou na AU Central 2 como a primeira preferência para cargas de trabalho PROTEGIDAs

* Consulte o *relatório de certificação do Azure-acsc – publicação 2018 protegida* para obter o status de certificação de serviços individuais e executar autoavaliações em quaisquer componentes relevantes não incluídos no relatório de acordo com o *Guia do consumidor do acsc – Microsoft Azure em protegido*

* Garanta a conectividade de rede e qualquer configuração de proxy necessária para acessar os componentes de autenticação necessários, como Azure AD, ADFS e PTA

* Use Azure Policy para monitorar e impor a conformidade com os requisitos

* Verifique se as máquinas virtuais, especialmente Domínio do Active Directory controladores, estão armazenadas em contas de armazenamento criptografadas e Utilise Azure Disk Encryption

* Crie e mantenha a governança e os processos de gerenciamento de privilégios administrativos e de identidade robustos para baseia os controles técnicos listados neste artigo

|Resource|URL|
|---|---|
|Documentos normativos da Austrália e conformidade da política|[Documentos normativos da Austrália e conformidade da política](https://aka.ms/au-irap)|
|Produtos do Azure-regiões australianas e não regionais|[Produtos do Azure-regiões australianas e não regionais](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Estratégias para mitigar incidentes de segurança cibernéticos|[Estratégias para mitigar incidentes de segurança cibernéticos](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ACSC proteger: Administração segura|[ACSC proteger: Administração segura](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|Como: Integrar a sua infraestrutura de Gateway de ambiente de trabalho remoto usando a extensão de servidor de políticas de rede (NPS) e o Azure AD|[Integrar o Gateway RD ao NPS e ao Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-rdg)|

## <a name="component-guidance"></a>Diretrizes de componente

Esta seção fornece informações sobre a finalidade de cada componente e sua função na arquitetura geral segura de administração remota. Links adicionais são fornecidos para acessar recursos úteis, como documentação de referência, guias e tutoriais.

## <a name="secure-devices"></a>Proteger dispositivos

Os dispositivos físicos usados por usuários privilegiados para executar funções administrativas são alvos valiosos para atores mal-intencionados. Manter a segurança e a integridade dos dispositivos físicos e garantir que eles sejam livres de software mal-intencionado e protegê-los contra o comprometimento é uma parte fundamental do fornecimento de um recurso de administração remota seguro. Isso envolve uma configuração de segurança de alta prioridade, conforme especificado nas oito estratégias essenciais do ACSC para atenuar incidentes de segurança cibernéticos, como lista de permissões de aplicativos, aplicação de patch de aplicativos, proteção de aplicativos e aplicação de patch em sistemas operacionais. Esses recursos devem ser instalados, configurados, auditados, validados e relatados para garantir que o estado de um dispositivo esteja em conformidade com os requisitos de organização.

### <a name="privileged-workstation"></a>Estação de trabalho privilegiada

A estação de trabalho privilegiada é uma máquina protegida que pode ser usada para executar tarefas administrativas e só é acessível para contas administrativas. A estação de trabalho privilegiada deve ter políticas e configuração in-loco para limitar o software que pode ser executado, seu acesso a recursos de rede e a Internet e credenciais devem ser protegidos caso o dispositivo seja roubado ou comprometido. |

|Recursos|Associar|
|---|---|
|Visão geral da arquitetura de estações de trabalho com acesso privilegiado|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)|
|Protegendo material de referência de acesso privilegiado|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|
|

### <a name="mobile-device"></a>Dispositivo móvel

Um dispositivo móvel tem um risco maior de perda acidental ou roubo devido à sua portabilidade e tamanho e precisa ser protegido adequadamente. O dispositivo móvel fornece um fator mais forte para a autenticação, considerando sua capacidade de impor a autenticação de acesso ao dispositivo, a rastreabilidade por meio de serviços de localização, funções de criptografia e a capacidade de ser apagada remotamente. Ao usar um dispositivo móvel como um fator de autenticação adicional para o Azure, o dispositivo deve ser configurado para usar o aplicativo Microsoft Authenticator com autenticação de PIN ou biométrica, e não por chamadas telefônicas ou mensagens de texto.

|Recursos|Associar|
|---|---|
|Métodos de autenticação do Azure AD|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)|
|Como usar o aplicativo Microsoft Authenticator|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|
|

### <a name="microsoft-intune"></a>Microsoft Intune

O Intune é o componente do Enterprise Mobility + Security que gerencia dispositivos móveis e aplicativos. Ele se integra em conjunto com outros componentes, como Azure Active Directory para identidade e controle de acesso e proteção de informações do Azure para proteção de dados. O Intune fornece políticas para estações de trabalho e dispositivos móveis para definir os requisitos de conformidade para acessar recursos e fornece recursos de relatório e auditoria para obter informações sobre o status dos dispositivos administrativos.

|Recursos|Associar|
|---|---|
|Documentação do Microsoft Intune|[https://docs.microsoft.com/intune/](https://docs.microsoft.com/intune/)|
|Introdução à conformidade do dispositivo no Intune|[https://docs.microsoft.com/intune/device-compliance-get-started](https://docs.microsoft.com/intune/device-compliance-get-started)|
|

### <a name="group-policy"></a>Política de Grupo

Política de Grupo é usado para controlar a configuração de sistemas operacionais e aplicativos. As políticas de segurança controlam as configurações de autenticação, autorização e auditoria de um sistema. Política de Grupo é usado para proteger a estação de trabalho privilegiada, proteger credenciais administrativas e restringir contas sem privilégios de acessar dispositivos privilegiados.

|Recursos|Associar|
|---|---|
|Permitir a configuração de Política de Grupo de entrada localmente|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|
|

### <a name="jump-server--bastion-host"></a>Servidor de salto/host de bastiões

O host de servidor de salto/bastiões é um ponto central para a administração. Ele tem as ferramentas necessárias para executar tarefas administrativas, mas também tem o acesso à rede necessário para se conectar aos recursos em portas administrativas. O servidor de salto é o ponto central para administrar cargas de trabalho de máquina virtual neste artigo, mas também pode ser configurado como o ponto autorizado para administrar recursos de PaaS (plataforma como serviço), como SQL. O acesso aos recursos de PaaS pode ser restrito por serviço usando controles de identidade e de rede.

|Recursos|Associar|
|---|---|
|Implementando hosts administrativos seguros|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|
|

### <a name="just-in-time-jit-access"></a>Acesso just in time (JIT)

O JIT é um recurso da central de segurança do Azure que usa NSGs (grupos de segurança de rede) para bloquear o acesso a protocolos administrativos, como RDP e SSH em máquinas virtuais. Aplicativos hospedados em máquinas virtuais continuam a funcionar normalmente, mas para que o acesso administrativo seja obtido, ele deve ser solicitado somente pode ser concedido por um período de tempo definido. Todas as solicitações são registradas para fins de auditoria.

|Recursos |Associar |
|---|---|
|Gerenciar o acesso JIT (just in time)|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|
|Automatizando o acesso à VM just in time do Azure|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access)|
|

## <a name="secure-communication"></a>Comunicação segura

O tráfego de comunicações para atividades de administração pode conter informações altamente confidenciais, como credenciais administrativas e deve ser gerenciado e protegido de acordo. Fornecer comunicação segura envolve recursos de criptografia confiáveis para evitar interceptação e segmentação de rede e restrições que limitam o tráfego administrativo aos pontos de extremidade autorizados e controlam a movimentação lateral se um sistema for comprometido.

### <a name="azure-portal"></a>Portal do Azure

As comunicações com o portal do Azure são criptografadas usando o protocolo TLS e o uso do portal do Azure foi certificado pelo ACSC. As entidades da Comunidade devem seguir as recomendações no *Guia do consumidor do acsc* e configurar seus navegadores da Web para garantir que estejam usando a versão mais recente do TLS e com algoritmos de criptografia com suporte.

|Recursos |Associar |
|---|---|
|Visão geral da criptografia do Azure – criptografia em trânsito|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit)|
|

### <a name="azure-vpn-gateway"></a>Gateway de VPN do Azure

O gateway de VPN do Azure fornece a conexão criptografada segura da estação de trabalho privilegiada para o Azure. O gateway de VPN do Azure foi certificado pelo ACSC para fornecer comunicação IPSec segura. As entidades da Comunidade devem configurar o gateway de VPN do Azure de acordo com o guia do consumidor do ACSC, o relatório de certificação do ACSC e outras diretrizes específicas.

|Recursos |Associar |
|---|---|
|Sobre conexões ponto a site|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about)|
|Detalhes de criptografia do gateway de VPN do Azure|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto)|
|Configuração do gateway de VPN do Azure|[https://aka.ms/AzGovAUSecurity](https://aka.ms/AzGovAUSecurity)|
|

### <a name="remote-desktop-rd-gateway"></a>Gateway de Área de Trabalho Remota (RD)

O gateway de área de trabalho remota é um mecanismo seguro para controlar e authorising conexões RDP com sistemas. Ele funciona encapsulando o tráfego RDP no protocolo HTTPS (HyperText Transfer Protocol Secure) e criptografado usando TLS. O TLS fornece uma camada adicional de segurança para o tráfego administrativo.

|Recursos |Associar |
|---|---|
|Arquitetura de Serviços de Área de Trabalho Remota|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|
|

### <a name="network-security-groups-nsgs"></a>NSGs (grupos de segurança de rede)

Função NSGs como ACLs (listas de controle de acesso) para tráfego de rede entrando ou saindo de sub-redes ou máquinas virtuais. O NSGs fornece segmentação de rede e fornece um mecanismo para controlar e limitar os fluxos de comunicação permitidos entre sistemas. NSGs são um componente fundamental da JIT (administração just-in-time) para permitir ou negar acesso a protocolos administrativos.

|Recursos |Associar |
|---|---|
|Visão geral dos grupos de segurança do Azure|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Como: Planejar redes virtuais|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
|

## <a name="strong-authentication"></a>Autenticação forte

Identificar com segurança os usuários privilegiados antes de conceder acesso a sistemas é um componente fundamental da administração segura. Os mecanismos devem estar em vigor para proteger as credenciais associadas a uma conta privilegiada e impedir que os atores mal-intencionados obtenham acesso a sistemas por meio de roubo de credencial ou representação.

### <a name="domain-controller-dc"></a>Controlador de domínio (DC)

Em um nível alto, um DC hospeda uma cópia do banco de dados Active Directory, que contém todos os usuários, computadores e grupos dentro de um domínio. Os DCs executam a autenticação para usuários e computadores. Os DCs nessa arquitetura são hospedados como máquinas virtuais no Azure e fornecem serviços de autenticação para contas com privilégios que se conectam a servidores de salto e máquinas virtuais de carga de trabalho.

|Recursos |Associar |
|---|---|
|Visão geral de Active Directory Domain Services|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|
|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

O Azure AD é o serviço de autenticação do Azure. Ele contém a nuvem

identidades e fornece autenticação e autorização para um ambiente do Azure. O Azure AD pode ser sincronizado com o Active Directory por meio de Azure AD Connect e pode fornecer autenticação federada por meio de Serviços de Federação do Active Directory (AD FS) (AD FS) e Azure AD Connect. O Azure AD é um componente fundamental da administração segura.

|Recursos |Associar |
|---|---|
|Documentação do Azure Active Directory|[https://docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)|
|Documentação de identidade híbrida|[https://docs.microsoft.com/azure/active-directory/hybrid](https://docs.microsoft.com/azure/active-directory/hybrid)|
|

### <a name="network-policy-server-nps"></a>Servidor de políticas de rede (NPS)

Um NPS é um servidor de autenticação e política que fornece autenticação avançada e processos de autorização. O servidor NPS nessa arquitetura é fornecido para integrar a autenticação do Azure MFA com solicitações de autenticação de gateway de área de trabalho remota. O NPS tem um plug-in específico para dar suporte à integração com o Azure MFA no Azure AD.

|Recursos |Associar |
|---|---|
|Documentação do servidor de políticas de rede|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)|
|

### <a name="azure-mfa"></a>MFA do Azure

O Azure MFA é um serviço de autenticação fornecido no Azure Active Directory para habilitar solicitações de autenticação além de um nome de usuário e senha para acessar recursos de nuvem, como o portal do Azure. O Azure MFA dá suporte a uma variedade de métodos de autenticação e essa arquitetura usa o aplicativo Microsoft Authenticator para aumentar a segurança e a integração com o NPS.

|Recursos |Associar |
|---|---|
|Como funciona: Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)|
|Como: Implementar o Multi-Factor Authentication do Azure baseado em cloud|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)|
|

## <a name="strong-authorisation"></a>Autorização forte

Depois que uma conta com privilégios tiver sido identificada com segurança, ela poderá receber acesso aos recursos. Autorização controla e gerencia os privilégios que são atribuídos a uma conta específica. Os processos de autorização fortes se alinham com a estratégia de oito essenciais do ACSC para atenuar incidentes de segurança cibernéticos de restrição de privilégios administrativos.

### <a name="identity-and-access-management"></a>Gestão de acesso e identidades

O acesso para executar ações privilegiadas no Azure é baseado em funções atribuídas a essa conta. O Azure inclui uma gama abrangente e granular de funções com permissões específicas para executar tarefas específicas. Essas funções podem ser concedidas em vários níveis, como uma assinatura ou um grupo de recursos. A atribuição de função e o gerenciamento de permissões baseiam-se em contas e grupos no Azure Active Directory e são gerenciados por meio do controle de acesso (IAM) no Azure.

|Recursos |Associar |
|---|---|
|Controle de acesso baseado em função do Azure|[https://docs.microsoft.com/azure/role-based-access-control](https://docs.microsoft.com/azure/role-based-access-control)|
|Entender as definições de função|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)|
|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

O PIM é um componente Azure Active Directory que controla o acesso a funções com privilégios. As contas privilegiadas não exigem acesso privilegiado permanente ou em posição, mas podem ser concedidas à capacidade de solicitar acesso privilegiado por um período de tempo para concluir atividades privilegiadas. O PIM fornece controles adicionais sobre a manutenção e restrição do acesso privilegiado, bem como o registro em log e a auditoria para rastrear instâncias de uso de privilégios.

|Recursos |Associar |
|---|---|
|Documentação do Privileged Identity Management (PIM)|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)|
|Comece a utilizar o PIM|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started)|
|

### <a name="conditional-access"></a>Acesso condicional

O acesso condicional é um componente do Azure Active Directory que permite ou nega o acesso a recursos com base em condições. Essas condições podem ser baseadas no local de rede, tipo de dispositivo, status de conformidade, associação de grupo e muito mais. O acesso condicional é usado para impor MFA, gerenciamento de dispositivos e conformidade por meio do Intune e Associação de grupo de contas administrativas.

|Recursos |Associar |
|---|---|
|Documentação de Acesso Condicional|[https://docs.microsoft.com/azure/active-directory/conditional-access](https://docs.microsoft.com/azure/active-directory/conditional-access)|
|Como: Exigir dispositivos gerenciados para acesso ao aplicativo de nuvem com acesso condicional|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|
|

## <a name="next-steps"></a>Passos Seguintes

Examine o artigo sobre [Gerenciamento de tráfego de entrada de gateway e controle](gateway-ingress-traffic.md) para obter detalhes sobre como controlar fluxos de tráfego por meio de seus componentes de gateway no Azure.
