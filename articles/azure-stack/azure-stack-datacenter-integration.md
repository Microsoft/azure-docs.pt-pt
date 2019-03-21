---
title: Sistemas integrados de considerações de integração do Centro de dados gerais para o Azure Stack | Documentos da Microsoft
description: Saiba o que pode fazer agora a planear e preparar a integração do Centro de dados com vários nós do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: 1c21cc778ed398c28077ef1a20ebbb7b295c2baa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012394"
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Sistemas integrados de considerações de integração do Centro de dados para o Azure Stack
Se estiver interessado num sistema integrado do Azure Stack, deve compreender as principais considerações de planeamento, à implantação e como o sistema se encaixa no seu datacenter. Este artigo fornece uma visão geral dessas questões para ajudar a tomar decisões importantes de infraestrutura para o seu sistema de vários nó do Azure Stack. Uma compreensão destas considerações ajuda ao trabalhar com o fornecedor do hardware de OEM, à medida que implementam o Azure Stack para o seu datacenter.  

> [!NOTE]
> O Azure Stack, sistemas de vários nós apenas podem ser adquiridos de fornecedores de hardware autorizado. 

Para implementar o Azure Stack, terá de fornecer informações de planeamento para o seu fornecedor de solução antes de implementação é iniciada ajudar o processo de rapidamente e sem problemas. As informações necessárias intervalos em funcionamento em rede, segurança e informações de identidade com muitas decisões importantes que podem exigir conhecimento de muitas áreas diferentes e tomadores de decisões. Por conseguinte, poderá ter de pessoas de várias equipas na sua organização para se certificar de que tem todas as informações necessárias prontas antes de inicia a implementação. Pode ajudar a comunicar com o seu fornecedor de hardware ao recolher estas informações, como podem ter conselhos úteis para tomar suas decisões.

Ao mesmo tempo a pesquisar e recolher as informações necessárias, poderá ter de fazer algumas alterações de configuração de pré-implantação para o seu ambiente de rede. Isso pode incluir a reservar os espaços de endereços IP para a solução do Azure Stack, configurar seus roteadores, comutadores e firewalls para se preparar para a conectividade com os comutadores de solução nova do Azure Stack. Lembre-se de que tem o especialista de área do assunto interessado até ajudá-lo em seus planos.

## <a name="capacity-planning-considerations"></a>Considerações de planeamento de capacidade
Ao avaliar uma solução de pilha do Azure para aquisição, opções de configuração de hardware devem ser tomadas que têm um impacto direto na capacidade geral de sua solução do Azure Stack. Estes incluem as escolhas clássicas de CPU, densidade de memória, configuração de armazenamento e dimensionamento de solução geral (por exemplo, o número de servidores). Ao contrário de uma solução de Virtualização tradicionais, não é aplicável a aritmética simple desses componentes para determinar a capacidade utilizável. O primeiro motivo é que o Azure Stack foi concebido para alojar os componentes de infraestrutura ou de gestão dentro da solução em si. O segundo motivo é que alguns da capacidade da solução está reservado para oferecer suporte a resiliência; a atualização de software da solução de uma forma que minimiza as interrupções de cargas de trabalho de inquilino. 

O [a folha de cálculo de Planeador de capacidade do Azure Stack](https://aka.ms/azstackcapacityplanner) ajuda a tirar informado com relação à Planejando a capacidade de duas formas: qualquer um do ao selecionar uma oferta de hardware e tentar de acordo com uma combinação de recursos ou através da definição a carga de trabalho de pilha do Azure destina-se para ser executada para ver o hardware disponível SKUs que pode oferecer suporte a ele. Por fim, a folha de cálculo destina-se como um guia para ajudar na tomada de decisões associadas ao Azure Stack planejamento e à configuração. 

A folha de cálculo não se destina a servir como um substituto para sua própria investigação e análise.  A Microsoft faz qualquer declaração nem oferece quaisquer garantias, expressas ou implícitas, quanto às informações fornecidas na planilha.



## <a name="management-considerations"></a>Considerações de gestão
O Azure Stack é um sistema selado, onde a infra-estrutura está bloqueada para baixo em ambos permissões de uma perspectiva de rede e. Listas de controlo de acesso (ACLs) de rede são aplicadas para bloquear todo o tráfego de entrada não autorizado e todas as comunicações desnecessárias entre os componentes de infraestrutura. Isso torna difícil para os utilizadores não autorizados a aceder ao sistema.

Para operações e gestão diária, existe sem acesso de administrador sem restrições à infraestrutura. Operadores do Azure Stack tem de gerir o sistema através do portal de administrador ou através do Azure Resource Manager (através do PowerShell ou a API REST). Não é possível aceder ao sistema por outras ferramentas de gestão, como o Gestor de Hyper-V ou o Gestor de clusters de ativação pós-falha. Para ajudar a proteger o sistema, não pode ser instalado o software de terceiros (por exemplo, agentes) dentro dos componentes da infraestrutura do Azure Stack. Interoperabilidade com o software de gerenciamento e segurança externa ocorre através do PowerShell ou a API REST.

Quando um nível mais elevado de acesso é necessário para problemas que não são resolvidos através de passos de mediação de alerta de resolução de problemas, deve trabalhar com Support da Microsoft. Graças ao suporte, existe um método para fornecer acesso de administrador completo temporário para o sistema execute operações mais avançadas. 

## <a name="identity-considerations"></a>Considerações de identidade

### <a name="choose-identity-provider"></a>Escolha o fornecedor de identidade
Precisará considerar qual o fornecedor de identidade que pretende utilizar para a implementação do Azure Stack, Azure AD ou AD FS. Não pode mudar os fornecedores de identidade após a implantação sem uma reimplantação completa do sistema. Se não é o proprietário da conta do Azure AD e estiver a utilizar uma conta fornecida a pelo seu fornecedor de serviços Cloud e, se decidir mudar o provedor e usar um diferente do Azure AD da conta, neste momento terá de contactar o seu fornecedor de soluções para voltar a implementar a solução f ou seu custo.

Sua escolha de fornecedor de identidade não tem efeito em máquinas de virtuais de inquilino, o sistema de identidade e contas que utilizam, se eles podem aderir a um domínio do Active Directory, etc. Isso é separado.

Pode saber mais sobre como escolher um fornecedor de identidade no [artigo de modelos de ligação de sistemas integrados do Azure Stack](./azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>Integração do AD FS e do Graph
Se optar por implementar o Azure Stack através do AD FS como o fornecedor de identidade, tem de integrar a instância do AD FS no Azure Stack com uma instância do AD FS existente por meio de uma fidedignidade de Federação. Isso permite que as identidades numa floresta existente do Active Directory para autenticar com recursos no Azure Stack.

Também pode integrar o serviço de gráficos no Azure Stack com o Active Directory existente. Isto permite-lhe gerir baseada em funções controlo de acesso (RBAC) no Azure Stack. Quando o acesso a um recurso é delegado, o componente de gráfico procura a conta de utilizador na floresta do Active Directory existente, utilizando o protocolo LDAP.

O diagrama seguinte mostra o fluxo de tráfego do AD FS e o gráfico integrado.
![Diagrama que mostra o fluxo de tráfego do AD FS e o gráfico](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Modelo de licenciamento
Deve decidir qual modelo de licenciamento que pretende utilizar. As opções disponíveis dependem se implementar o Azure Stack ligado à internet:
- Para uma [ligado implementação](azure-stack-connected-deployment.md), pode escolher pay-as que use ou baseada em capacidade de licenciamento. Pay-as que use requer uma ligação para o Azure para a utilização de relatório, que, em seguida, está a ser faturada através do Azure commerce. 
- Apenas com base na capacidade o licenciamento é suportado se [implementar desligado](azure-stack-disconnected-deployment.md) da internet. 

Para obter mais informações sobre os modelos de licenciamento, consulte [pacotes e preços do Microsoft Azure Stack](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Decisões de nomenclatura

Precisará pensar sobre como deve planejar seu espaço de nomes do Azure Stack, especialmente o nome da região e o nome de domínio externo. O nome de domínio completamente qualificado (FQDN) externo da sua implementação do Azure Stack para pontos finais de destinado ao público é a combinação destes dois nomes: &lt; *região*&gt;.&lt; *fqdn*&gt;. Por exemplo, *east.cloud.fabrikam.com*. Neste exemplo, os portais do Azure Stack seria disponíveis nos seguintes URLs:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> O nome de região que escolher para a sua implementação do Azure Stack tem de ser exclusivo e aparecerá nos endereços de portais. 

A tabela seguinte resume essas decisões de nomenclatura de domínio.

| Name | Descrição | 
| -------- | ------------- | 
|Nome da região | O nome da sua primeira região do Azure Stack. Este nome é utilizado como parte do FQDN para os públicos endereços IP virtuais (VIPs) que gere o Azure Stack. Normalmente, o nome da região seria um identificador de localização física, como uma localização do datacenter.<br><br>O nome de região deve incluir apenas letras e números entre 0 a 9. Não existem carateres especiais, como "-" ou "#", etc. são permitidos.| 
| Nome de domínio externo | O nome da zona de sistema de nomes de domínio (DNS) para pontos de extremidade com face externa VIPs. Utilizado no FQDN para estes VIPs públicas. | 
| Nome de domínio (interno) privada | O nome de domínio (e zona DNS interna) criado no Azure Stack para a gestão de infraestrutura. 
| | |

## <a name="certificate-requirements"></a>Requisitos de certificado

Para a implementação, terá de fornecer certificados Secure Sockets Layer (SSL) para os pontos finais públicos. Num alto nível, os certificados têm os seguintes requisitos:

- Pode utilizar um certificado de caráter universal único ou pode utilizar um conjunto de certificados dedicados e utilizar carateres universais apenas para pontos de extremidade como o armazenamento e o Key Vault.
- Os certificados podem ser emitidos por uma autoridade de certificado fidedigno pública (AC) ou uma AC geridas pelo cliente.

Para obter mais informações sobre quais PKI os certificados são necessários para implementar o Azure Stack e como obtê-las, consulte, [requisitos de certificados de infraestrutura de chave pública do Azure Stack](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> As informações do certificado PKI fornecidas devem ser usadas como orientação geral. Antes de adquirir todos os certificados PKI para o Azure Stack, trabalhe com o seu parceiro de hardware de OEM. Eles irão fornecer mais orientações de certificado e os requisitos.


## <a name="time-synchronization"></a>Sincronização de hora
Tem de escolher uma hora específica servidor com é usado para sincronizar o Azure Stack.  Sincronização de hora é essencial para o Azure Stack e respetivas funções de infraestrutura, como ele é utilizado para gerar os tíquetes Kerberos que são utilizados para autenticar serviços internos entre si.

Tem de especificar que um IP para o servidor de sincronização de hora, embora a maioria dos componentes na infraestrutura pode resolver um URL, alguns só pode suportar endereços IP. Se estiver a utilizar a opção de implementação desligado, tem de especificar um servidor de tempo na sua rede empresarial que estiver-se de que pode ser contactado a partir da rede de infraestrutura no Azure Stack.

## <a name="connect-azure-stack-to-azure"></a>Ligar o Azure Stack para o Azure

Para cenários de cloud híbrida, terá de planear como pretende ligar o Azure Stack para o Azure. Existem dois métodos suportados para ligar redes virtuais no Azure Stack para redes virtuais no Azure: 
- **Site-site**. Uma ligação de rede privada virtual (VPN) através de IPsec (IKE v1 e IKE v2). Este tipo de ligação requer um dispositivo VPN ou de encaminhamento e acesso remoto (RRAS). Para obter mais informações sobre gateways de VPN no Azure, consulte [sobre o Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). A comunicação por este túnel é encriptada e é segura. No entanto, a largura de banda é limitada pelo débito máximo do túnel (100-200 Mbps).
- **NAT de saída**. Por predefinição, todas as máquinas virtuais no Azure Stack terão conetividade para redes externas através de NAT de saída. Cada rede virtual que é criado no Azure Stack é um endereço IP público atribuído ao mesmo. Se a máquina virtual diretamente é atribuída um endereço IP público, ou se encontrar atrás de um balanceador de carga com um endereço IP público, ele terá acesso de saída através de NAT de saída usando o VIP da rede virtual. Isso funciona apenas para comunicação que é iniciada pela máquina virtual e se destina a redes externas (internet ou intranet). Não pode ser utilizado para comunicar com a máquina virtual a partir de fora.

### <a name="hybrid-connectivity-options"></a>Opções de conectividade híbrida

Para conectividade híbrida, é importante considerar que tipo de implementação que pretende oferecer e onde será implementado. Precisará considerar a necessidade de isolar o tráfego de rede por inquilino e, se terá uma implementação de intranet ou internet.

- **O Azure Stack de inquilino único**. Uma implementação do Azure Stack que procura, pelo menos de uma perspectiva de rede, como se fosse um inquilino. Podem existir que muitas subscrições de inquilino, mas como qualquer serviço de intranet, todo o tráfego viaja sobre as mesmas redes. Tráfego de rede de uma subscrição percorre a mesma conexão de rede como outra subscrição e não precisa ser isolada através de um túnel encriptado.

- **Multi-inquilino do Azure Stack**. Uma implementação do Azure Stack onde o tráfego de cada subscrição de inquilino que está vinculado a redes externas ao Azure Stack tem de ser isolado do tráfego de rede dos outros inquilinos.
 
- **Implementação de intranet**. Uma implementação do Azure Stack que encontra-se numa intranet corporativa, normalmente, no espaço de endereços IP privados e atrás de firewalls de um ou mais. Os endereços IP públicos não são verdadeiramente públicos, como não pode ser encaminhados diretamente através da internet pública.

- **Implementação de Internet**. Uma implementação do Azure Stack que está ligada ao público da internet e utiliza encaminháveis para a internet endereços IP públicos de para o intervalo de VIP público. A implementação ainda pode ser usada por trás de uma firewall, mas o intervalo de VIP público seja diretamente acessível a partir da internet pública e o Azure.
 
A tabela seguinte resume os cenários de conectividade híbrida, com os profissionais de TI, contras e casos de utilização.

| Cenário | Método de conectividade | Profissionais de TI | Contras | Vale a |
| -- | -- | --| -- | --|
| Único inquilino do Azure Stack, implementação de intranet | NAT de saída | Uma melhor largura de banda para transferências mais rápidas. Simples de implementar; Não existem gateways necessários. | Tráfego encriptado não; sem isolamento ou encriptação fora do conjunto. | Implementações em empresas em que todos os inquilinos são igualmente confiáveis.<br><br>Empresas que têm um circuito do ExpressRoute do Azure para o Azure. |
| Multi-inquilino do Azure Stack, implementação de intranet | VPN site a site | Tráfego de inquilino de VNet para o destino é seguro. | Largura de banda é limitada pelo túnel VPN de site a site.<br><br>Requer um gateway na rede virtual e um dispositivo VPN na rede de destino. | Implementações em empresas em que alguns tráfego de inquilino tem de estar protegidas de outros inquilinos. |
| Único inquilino do Azure Stack, implementação de internet | NAT de saída | Uma melhor largura de banda para transferências mais rápidas. | Tráfego encriptado não; sem isolamento ou encriptação fora do conjunto. | Alojamento de cenários em que o inquilino obtém sua própria implementação do Azure Stack e um circuito dedicado para o ambiente do Azure Stack. Por exemplo, ExpressRoute e troca de etiqueta multiprotocolo (MPLS).
| Multi-inquilino do Azure Stack, implementação de internet | VPN site a site | Tráfego de inquilino de VNet para o destino é seguro. | Largura de banda é limitada pelo túnel VPN de site a site.<br><br>Requer um gateway na rede virtual e um dispositivo VPN na rede de destino. | Cenários em que o provedor quer oferecer uma nuvem de multi-inquilino de alojamento, em que os inquilinos não confiam entre si e o tráfego tem de estar encriptado.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Com o ExpressRoute

Pode ligar o Azure Stack ao Azure via [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para intranet de inquilino único e cenários de vários inquilinos. Precisará de um circuito ExpressRoute aprovisionado através de [um fornecedor de conectividade](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

O diagrama seguinte mostra o ExpressRoute para um cenário de inquilino individual (em que "Ligação do cliente" é o circuito do ExpressRoute).

![Cenário de ExpressRoute do diagrama que mostra inquilino único](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

O diagrama seguinte mostra o ExpressRoute para um cenário de multi-inquilino.

![Cenário de ExpressRoute do diagrama que mostra com vários inquilinos](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Monitorização externos
Para obter uma vista única de todos os alertas de seus dispositivos e a implementação do Azure Stack e integrar alertas no IT service management fluxos de trabalho existentes para a emissão de permissões, pode [integre o Azure Stack com o Centro de dados externodesoluçõesdemonitorização](azure-stack-integrate-monitor.md).

Incluído com a solução do Azure Stack, o anfitrião de ciclo de vida do hardware é um computador fora do Azure Stack que executa as ferramentas de gestão fornecido pelo fornecedor de OEM para hardware. Pode usar essas ferramentas ou outras soluções que se integram diretamente de soluções de monitorização existentes no seu datacenter.

A tabela seguinte resume a lista de opções atualmente disponíveis.

| Área | Solução de monitorização externa |
| -- | -- |
| Software de pilha do Azure | [Pacote de gestão do Azure Stack para o Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios Plug-in](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>Chamadas de API baseada em REST | 
| Servidores físicos (BMCs através de IPMI) | Hardware de OEM - pacote de gestão do fornecedor do Operations Manager<br>Solução de fornecidos pelo fornecedor de hardware de OEM<br>Fornecedor de hardware Nagios plug-ins.<br>OEM parceiros com suporte (incluída) de solução de monitorização | 
| Dispositivos de rede (SNMP) | Deteção de dispositivos de rede do Operations Manager<br>Solução de fornecidos pelo fornecedor de hardware de OEM<br>Comutador de Nagios Plug-in |
| Monitorização de estado de funcionamento de subscrição do inquilino | [Pacote de gestão do System Center para o Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Tenha em atenção os seguintes requisitos:
- A solução que utiliza tem de ser sem agente. Não é possível instalar agentes de terceiros dentro de componentes do Azure Stack. 
- Se pretender utilizar o System Center Operations Manager, Operations Manager 2012 R2 ou o Operations Manager 2016 é necessário.

## <a name="backup-and-disaster-recovery"></a>Cópia de segurança e recuperação após desastre

Planejando a recuperação após desastre e cópia de segurança de envolve o planejamento para ambas do Azure Stack infraestrutura subjacente que aloja máquinas de virtuais de IaaS e serviços de PaaS e para aplicações de inquilino e os dados. Tem de planear para estas separadamente.

### <a name="protect-infrastructure-components"></a>Proteger os componentes da infraestrutura

Pode [cópia de segurança do Azure Stack](azure-stack-backup-back-up-azure-stack.md) componentes de infraestrutura para SMB partilham que especificar:

- Precisará de uma partilha de ficheiros SMB externa num servidor de ficheiros baseado no Windows existente ou um dispositivo de terceiros.
- Deve usar essa mesma partilha para a cópia de segurança de comutadores de rede e o anfitrião de ciclo de vida do hardware. O fornecedor do hardware de OEM ajuda fornece orientações sobre a cópia de segurança e restauro desses componentes, já que elas são externas ao Azure Stack. É responsável por executar os fluxos de trabalho de cópia de segurança baseados em recomendações do fornecedor do OEM.

Se ocorrer a perda catastrófica de dados, pode utilizar a cópia de segurança de infra-estrutura para dados de implementação de propagar novamente, como implantação entradas e identificadores, contas de serviço, certificado de raiz AC, recursos federados (em implementações desligados), planos, ofertas, as subscrições, quotas, atribuições de política e a função RBAC e segredos do Key Vault.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Proteger aplicações de inquilino em máquinas de virtuais de IaaS

O Azure Stack não criar uma cópia de segurança de dados e aplicações de inquilino. Tem de planear para proteção de recuperação após desastre e cópia de segurança para um destino externo para o Azure Stack. Proteção de inquilino for uma actividade orientada por inquilino. Para máquinas de virtuais de IaaS, os inquilinos podem utilizar tecnologias de convidado para proteger as pastas de arquivos, dados de aplicação e o estado do sistema. No entanto, como um provedor de enterprise ou o serviço, pode querer oferecem uma solução de cópia de segurança e recuperação no mesmo datacenter ou externamente numa nuvem.

Para fazer backup de máquinas virtuais Linux ou do IaaS do Windows, tem de utilizar os produtos de cópia de segurança com acesso ao sistema operativo convidado para proteger o ficheiro, pasta, estado do sistema operativo e dados da aplicação. Pode utilizar o Azure Backup, System Center Data Center Protection Manager, ou produtos de terceiros suportados.

Para replicar dados para uma localização secundária e orquestrar a ativação pós-falha do aplicativo, se ocorrer um desastre, pode utilizar o Azure Site Recovery ou a produtos de terceiros suportados. Além disso, as aplicações que suportam os replicação nativa, como o Microsoft SQL Server, podem replicar dados para outra localização onde o aplicativo está em execução.

## <a name="learn-more"></a>Saiba mais

- Para obter informações sobre casos de utilização, de compra, parceiros e fornecedores de hardware de OEM, consulte a [do Azure Stack](https://azure.microsoft.com/overview/azure-stack/) página do produto.
- Para obter informações sobre o plano e a disponibilidade geográfica para o Azure Stack, sistemas integrados, consulte o white paper: [Azure Stack: Uma extensão do Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Passos Seguintes
[Modelos de ligação de implementação do Azure Stack](azure-stack-connection-models.md)
