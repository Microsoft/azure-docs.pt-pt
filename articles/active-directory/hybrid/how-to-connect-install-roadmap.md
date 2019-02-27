---
title: Mapa de instalação do Azure AD Connect e do Azure AD Connect Health. | Microsoft Docs
description: Este documento fornece uma descrição geral das opções e dos caminhos de instalação disponíveis para instalar o Azure AD Connect e o Azure AD Connect Health.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1851a6c27e89da71e75c59e025fee3800d0cd973
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876964"
---
# <a name="azure-ad-connect-and-azure-ad-connect-health-installation-roadmap"></a>Mapa de instalação do Azure AD Connect e do Azure AD Connect Health

## <a name="install-azure-ad-connect"></a>Instalar o Azure AD Connect

> [!IMPORTANT]
> A Microsoft não suporta a modificação ou operação de sincronizações do Azure AD Connect fora das ações anteriormente documentadas. Qualquer destas ações pode resultar num estado inconsistente ou não suportado da sincronização do Azure AD Connect. Como resultado, a Microsoft não pode possível fornecer o suporte técnico para implementações deste tipo.

Pode encontrar a transferência do Azure AD Connect no [Centro de Transferências da Microsoft](https://go.microsoft.com/fwlink/?LinkId=615771).

| Solução | Cenário |
| --- | --- |
| Antes de iniciar – [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) |<li>Passos a completar antes de iniciar a instalação do Azure AD Connect.</li> |
| [Definições rápidas](how-to-connect-install-express.md) |<li>Se tiver uma única floresta do AD, é esta a opção que se recomenda utilizar.</li> <li>Início de sessão do utilizador com a mesma palavra-passe, utilizando a sincronização de palavra-passe.</li> |
| [Definições personalizadas](how-to-connect-install-custom.md) |<li>Utilizadas se tiver várias florestas. Suporta muitas [topologias](plan-connect-topologies.md) no local.</li> <li>Personalize a sua opção de início de sessão, como a autenticação pass-through, o ADFS para federação ou utilize um fornecedor de identidade de terceiros.</li> <li>Personalize as funcionalidades de sincronização, como a filtragem e repetição de escrita.</li> |
| [Atualizar do DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Utilizada se tiver um servidor existente do DirSync já em execução.</li> |
| [Atualizar do Azure AD Sync ou do Azure AD Connect](how-to-upgrade-previous-version.md) |<li>Há vários métodos diferentes, consoante a sua preferência.</li> |

[Após a instalação](how-to-connect-post-installation.md), deve verificar se está a funcionar de acordo com o esperado e atribuir licenças aos utilizadores.

### <a name="next-steps-to-install-azure-ad-connect"></a>Passos seguintes para Instalar o Azure AD Connect
|Tópico |Ligação|  
| --- | --- |
|Transferir o Azure AD Connect | [Transferir o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalar utilizando as definições rápidas | [Instalação rápida do Azure AD Connect](./how-to-connect-install-express.md)|
|Instalar utilizando as definições personalizadas | [Instalação personalizada do Azure AD Connect](./how-to-connect-install-custom.md)|
|Atualização do DirSync | [Atualizar da ferramenta de sincronização do Azure AD (DirSync)](./how-to-dirsync-upgrade-get-started.md)|
|Após a instalação | [Verificar a instalação e atribuir licenças](how-to-connect-post-installation.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Saber mais sobre como instalar o Azure AD Connect
Terá também de se preparar para questões [operacionais](how-to-connect-sync-operations.md). Pode pretender ter um servidor de reserva a que possa efetuar a ativação pós-falha facilmente em caso de [desastre](how-to-connect-sync-operations.md#disaster-recovery). Se planear efetuar alterações frequentes na configuração, deverá planear um servidor no [modo de teste](how-to-connect-sync-operations.md#staging-mode).

|Tópico |Ligação|  
| --- | --- |
|Topologias suportadas | [Topologias do Azure AD Connect](plan-connect-topologies.md)|
|Conceitos de design | [Conceitos de design do Azure AD Connect](plan-connect-design-concepts.md)|
|Contas utilizadas para a instalação | [Mais informações acerca das credenciais e permissões do Azure AD Connect](reference-connect-accounts-permissions.md)|
|Planeamento operacional | [Sincronização do Azure AD Connect: Considerações e tarefas operacionais](how-to-connect-sync-operations.md)|
|Opções de início de sessão do utilizador | [Opções de início de sessão de Utilizador do Azure AD Connect](plan-connect-user-signin.md)|

## <a name="configure-sync-features"></a>Configurar funcionalidades de sincronização
O Azure AD Connect inclui várias funcionalidades que pode ativar como opção ou que estão ativadas por predefinição. Algumas das funcionalidades poderão requerer, às vezes, mais configuração em determinados cenários e topologias.

A [filtragem](how-to-connect-sync-configure-filtering.md) é utilizada quando pretende limitar os objetos que são sincronizados para o Azure AD. Por predefinição, são sincronizados todos os utilizadores, contactos, grupos e computadores com Windows 10. Pode alterar a filtragem com base em domínios, UOs ou atributos.

A [sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md) sincroniza o hash de palavra-passe no Active Directory para o Azure AD. O utilizador final pode utilizar a mesma palavra-passe no local e na nuvem, mas geri-la apenas numa única localização. Uma vez que utiliza o Active Directory no local como autoridade,pode também utilizar a sua própria política de palavras-passe.

A [repetição de escrita de palavras-passe](../authentication/quickstart-sspr.md) permitirá que os utilizadores alterem e reponham as respetivas palavras-passe na nuvem e a aplicação da sua política de palavras-passe no local.

A [repetição de escrita do dispositivo](how-to-connect-device-writeback.md) permitirá que um dispositivo registado no Azure AD seja rescrito no Active Directory no local, para que possa ser utilizado para acesso condicional.

A funcionalidade [impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) está ativada por predefinição e protege o diretório na nuvem de sofrer várias eliminações ao mesmo tempo. Por predefinição, permite 500 eliminações por execução. Pode alterar esta definição consoante o tamanho da sua organização.

A [atualização automática](how-to-connect-install-automatic-upgrade.md) está ativada por predefinição para instalações com definições rápidas e garante que o Azure AD Connect é sempre atualizado para a versão mais recente.

### <a name="next-steps-to-configure-sync-features"></a>Passos seguintes para configurar as funcionalidades de sincronização
|Tópico |Ligação|  
| --- | --- |
|Configurar a filtragem | [Sincronização do Azure AD Connect: Configure filtering](how-to-connect-sync-configure-filtering.md) (Sincronização do Azure AD Connect: Configurar a Filtragem)|
|Sincronização de hash de palavra-passe | [Sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md)|
|Autenticação pass-through | [Autenticação pass-through](how-to-connect-pta.md)
|Repetição de escrita de palavras-passe | [Introdução à gestão de palavras-passe](../authentication/quickstart-sspr.md)|
|Repetição de escrita do dispositivo | [Ativar a repetição de escrita do dispositivo no Azure AD Connect](how-to-connect-device-writeback.md)|
|Impedir eliminações acidentais | [Sincronização do Azure AD Connect: Prevent accidental deletes](how-to-connect-sync-feature-prevent-accidental-deletes.md) (Sincronização do Azure AD Connect: Impedir eliminações acidentais)|
|Atualização automática | [Azure AD Connect: Atualização automática](how-to-connect-install-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Personalizar a sincronização do Azure AD Connect
A sincronização do Azure AD Connect vem com uma configuração predefinida, concebida para funcionar com a maior parte dos clientes e topologias. Mas há sempre situações em que a configuração predefinida não funciona e deve ser ajustada. É suportada para efetuar alterações, como documentado nesta secção e tópicos ligados.

Se nunca trabalhou com uma topologia de sincronização, será boa ideia começar por entender as noções básicas e os termos utilizados, descritos nos [conceitos técnicos](how-to-connect-sync-technical-concepts.md). O Azure AD Connect é a evolução do MIIS2003, ILM2007 e FIM2010. Mesmo se alguns elementos são idênticos, muitos foram também alterados.

A [configuração predefinida](concept-azure-ad-connect-sync-default-configuration.md) presume que pode existir mais do que uma floresta na configuração. Nessas topologias, um objeto de utilizador pode ser representado como um contacto noutra floresta. O utilizador pode também ter uma caixa de correio ligada noutra floresta de recursos. O comportamento da configuração predefinida está descrito em [utilizadores e contactos](concept-azure-ad-connect-sync-user-and-contacts.md).

O modelo de configuração em sincronização é designado por [aprovisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md). Os fluxos de atributos avançadas utilizam [funções](reference-connect-sync-functions-reference.md) para expressar transformações de atributos. Pode ver e examinar a configuração toda utilizando as ferramentas fornecidas com o Azure AD Connect. Se precisar de efetuar alterações na configuração, certifique-se de que segue as [melhores práticas](how-to-connect-sync-best-practices-changing-default-configuration.md), para que seja mais fácil adotar novas versões.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Passos seguintes para personalizar a sincronização do Azure AD Connect
|Tópico |Ligação|  
| --- | --- |
|Todos os artigos acerca da sincronização do Azure AD Connect | [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md)|
|Conceitos técnicos | [Sincronização do Azure AD Connect: Technical Concepts](how-to-connect-sync-technical-concepts.md) (Sincronização do Azure AD Connect: Conceitos Técnicos)|
|Entender a configuração predefinida | [Sincronização do Azure AD Connect: Understanding the default configuration](concept-azure-ad-connect-sync-default-configuration.md) (Sincronização do Azure AD Connect: Compreender a configuração predefinida)|
|Entender utilizadores e contactos | [Sincronização do Azure AD Connect: Entender utilizadores e contactos](concept-azure-ad-connect-sync-user-and-contacts.md)|
|Aprovisionamento declarativo | [Sincronização do Azure AD Connect: Understanding Declarative Provisioning Expressions](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) (Sincronização do Azure AD Connect: Compreender as Expressões do Aprovisionamento Declarativo)|
|Alterar a configuração predefinida | [Melhores práticas para alterar a configuração predefinida](how-to-connect-sync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Configurar as funcionalidades de federação

O Azure AD Connect oferece várias funcionalidades que simplificam a federação com o Azure AD através do AD FS e a gestão da confiança de federação. O Azure AD Connect suporta o AD FS no Windows Server 2012R2 ou posterior.

[Atualize o certificado SSL do farm do AD FS](how-to-connect-fed-ssl-update.md), mesmo se não estiver a utilizar o Azure AD Connect para gerir a confiança da federação.

[Adicione um servidor do AD FS](how-to-connect-fed-management.md#addadfsserver) ao seu farm para expandir o farm conforme necessário.

[Repare a confiança](how-to-connect-fed-management.md#repairthetrust) com o Azure AD com apenas alguns cliques.

O ADFS pode ser configurado para suportar [vários domínios](how-to-connect-install-multiple-domains.md). Pode, por exemplo, ter vários domínios superiores que precisa de utilizar para a federação.

Se o servidor do AD FS não tiver sido configurado para atualizar automaticamente os certificados a partir do Azure AD ou se utilizar uma solução que não seja o AD FS, será notificado quando tiver de [atualizar os certificados](how-to-connect-fed-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Passos seguintes para configurar as funcionalidades de federação
|Tópico |Ligação|  
| --- | --- |
|Todos os artigos do AD FS | [Azure AD Connect e a federação](how-to-connect-fed-whatis.md)|
|Configuração do ADFS com subdomínios | [Suporte para Vários Domínios para Federação com o Azure AD](how-to-connect-install-multiple-domains.md)|
|Gerir o farm do AD FS | [Gestão e personalização do AD FS com o Azure AD Connect](how-to-connect-fed-management.md)|
|Atualizar manualmente certificados de federação | [Renovar Certificados de Federação do Office 365 e do Azure AD](how-to-connect-fed-o365-certs.md)|


## <a name="get-started-with-azure-ad-connect-health"></a>Introdução ao Azure AD Connect Health
Para começar a utilizar o Azure AD Connect Health, utilize os passos seguintes:

1. [Obtenha o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [inicie uma versão de avaliação](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Transfira e instale os Agentes do Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) nos servidores de identidade.
3. Veja o dashboard do Azure AD Connect Health em [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Lembre-se de que só poderá ver dados no dashboard do Azure AD Connect Health depois de instalar os Agentes do Azure AD Connect Health nos servidores de destino.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Transferir e instalar o Agente do Azure AD Connect Health
* Certifique-se de que [cumpre os requisitos](how-to-connect-health-agent-install.md#requirements) para o Azure AD Connect Health.
* Introdução ao Azure AD Connect Health para AD FS
    * [Transferir o Agente do Azure AD Connect Health para o AD FS.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Veja a instruções de instalação](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Introdução ao Azure AD Connect Health para sincronização
    * [Transferir e instalar a versão mais recente do Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). O Agente de Estado de Funcionamento para sincronização será instalado como parte da instalação do Azure AD Connect (versão 1.0.9125.0 ou superior).
* Introdução ao Azure AD Connect Health para AD DS
    * [Transfira o Agente do Azure AD Connect Health para AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Veja a instruções de instalação](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).


## <a name="azure-ad-connect-health-portal"></a>Portal do Azure AD Connect Health
O portal do Azure AD Connect Health mostra vistas de alertas, monitorização de desempenho e análise de utilização. O URL https://aka.ms/aadconnecthealth leva-o para o painel principal do Azure AD Connect Health. Pode considerar um painel como uma janela. No painel principal, verá **Início Rápido**, serviços no Azure AD Connect Health e opções adicionais de configuração. Veja a captura de ecrã seguinte e as breves explicações a seguir à captura de ecrã. Após implementar os agentes, o serviço de estado de funcionamento identifica automaticamente os serviços monitorizados pelo Azure AD Connect Health.

> [!NOTE]
> Para obter informações de licenciamento, veja [FAQ do Azure AD Connect Health](reference-connect-health-faq.md) ou a [página de Preços do Azure AD](https://aka.ms/aadpricing).
    
![Portal do Azure AD Connect Health](./media/whatis-hybrid-identity-health/portalsidebar.png)

* **Início Rápido**: Quando seleciona esta opção, o **início rápido** é aberto o painel. Pode transferir o Agente do Azure AD Connect Health, selecionando **Obter ferramentas**. Também pode aceder à documentação e fornecer comentários.
* **O Azure Active Directory Connect (sincronização)**: Esta opção mostra os servidores do Azure AD Connect que o Azure AD Connect Health está atualmente a monitorizar. A entrada **Erros de sincronização** irá mostrar erros de sincronização básicos do primeiro serviço de sincronização integrado por categorias. Quando seleciona a entrada **Serviços de sincronização**, o painel que aparece mostra informações sobre os servidores do Azure AD Connect. Saiba mais acerca das capacidades em [Utilizar o Azure AD Connect Health para sincronização](how-to-connect-health-sync.md).
* **Serviços de Federação do Active Directory**: Esta opção mostra todos os serviços do AD FS que o Azure AD Connect Health está atualmente a monitorizar. Quando seleciona uma instância, o painel que aparece mostra informações sobre essa instância de serviço. Estas informações incluem uma descrição geral, as propriedades, os alertas, a monitorização e a análise da utilização. Saiba mais acerca das capacidades em [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md).
* **Serviços de domínio do Active Directory**: Esta opção mostra todas as florestas do AD DS que o Azure AD Connect Health está atualmente a monitorizar. Quando seleciona uma floresta, o painel que aparece mostra informações sobre essa floresta. Estas informações incluem uma descrição geral de informações essenciais, o dashboard de Controladores de Domínio, o dashboard de Estado de Replicação, alertas e monitorização. Saiba mais acerca das capacidades em [Utilizar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md).
* **Configurar**: Esta secção inclui opções para ativar ou desativar o seguinte:

  - A entrada **Definições** inclui configurações básicas dos seus agentes. Ativa de definição de atualização automática atualizar automaticamente o agente do Azure AD Connect Health para a versão mais recente: Logo que ficarem disponíveis, serão automaticamente atualizadas para as versões mais recentes do agente do Azure AD Connect Health. Opção ativada por predefinição. Permitir o acesso da Microsoft aos dados de estado de funcionamento do diretório do Azure AD para apenas para fins de resolução de problemas: Se esta opção estiver ativada, a Microsoft pode ver os mesmos dados que vê. Estas informações podem ajudar a resolver problemas e na assistência com problemas. Opção desativada por predefinição.
* O **Controlo de acesso baseado em funções (IAM)** é a secção para gerir o acesso aos dados do Connect Health na base da função. 

## <a name="next-steps"></a>Próximos Passos

- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições rápidas](how-to-connect-install-express.md)
- [Definições personalizadas](how-to-connect-install-custom.md)
- [Sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md)|
- [Autenticação pass-through](how-to-connect-pta.md)
- [Azure AD Connect e a federação](how-to-connect-fed-whatis.md)
- [Instalar os agentes do Azure AD Connect Health](how-to-connect-health-agent-install.md) 
- [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md)
