---
title: Planejar uma implantação do painel de acesso do Azure Active Directory
description: Diretrizes sobre a implantação da funcionalidade do painel de acesso do Azure AD
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 448af7075297c2b18df1eceaeaac34d5da762489
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576527"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Planejar uma implantação do painel de acesso do Azure Active Directory

O painel de acesso Azure Active Directory é um portal baseado na Web que permite que você reduza os custos de suporte, aumente a produtividade e a segurança e reduza a frustração do usuário. O sistema inclui relatórios detalhados que controlam quando os usuários acessam o sistema e notificam os administradores sobre o uso indevido ou abuso.

O painel de acesso do Azure Active Directory permite que os usuários:

* Descubra e acesse todos os Azure Active Directory recursos conectados da sua empresa, como aplicativos.
* Solicite acesso a novos aplicativos e grupos ou gerencie o acesso a esses recursos para outras pessoas.
* Gerencie redefinições de senha de autoatendimento e configurações de autenticação multifator.
* Gerencie seus dispositivos.

Ele também permite que os administradores gerenciem:

* Termos de serviço
* Organizações
* Revisões de acesso


## <a name="benefits-of-azure-active-directory-access-panel-integration"></a>Benefícios da integração do painel de acesso do Azure Active Directory

O painel de acesso do Microsoft Azure Active Directory (AD do Azure) beneficia as empresas das seguintes maneiras:

**Fornece uma experiência de usuário intuitiva**: Habilitar o painel de acesso fornece aos usuários a capacidade de ter um único painel de inicialização para todos os seus aplicativos conectados de logon único do Azure. Como recursos como gerenciamento de grupo e redefinição de senha de autoatendimento são adicionados, os usuários continuam a ter um portal unificado para encontrar essas configurações. A experiência intuitiva permitirá que os usuários voltem a funcionar mais rapidamente e sejam mais produtivos, ao mesmo tempo em que reduzem sua frustração.

**Aumenta a produtividade**: Todos os aplicativos de usuário no painel de acesso têm o SSO (logon único) habilitado. Habilitar o logon único entre aplicativos empresariais e o Office 365 fornece uma experiência de entrada superior para os usuários existentes, reduzindo ou eliminando prompts de entrada adicionais. O painel de acesso permite o autoatendimento e a associação dinâmica e melhora a segurança geral do seu sistema de identidade, garantindo que as pessoas certas gerenciem o acesso aos aplicativos. O painel de acesso serve como uma página de aterrissagem coerente para que um usuário encontre recursos rapidamente e continue as tarefas de trabalho.

**Gerencia o custo**: Habilitar o painel de acesso com Azure Active Directory pode permitir o dimissão de infraestruturas locais. Ele reduz os custos de suporte, permitindo que os usuários tenham um portal consistente para localizar todos os seus aplicativos, solicitar acesso a recursos e gerenciar sua conta.

**Aumenta a flexibilidade e a segurança**: O painel de acesso permite que você acesse a segurança e a flexibilidade que uma plataforma de nuvem fornece. Os administradores podem facilmente alterar as configurações para aplicativos e recursos e acomodar novos requisitos de segurança sem afetar os usuários.

**Permite o acompanhamento robusto da auditoria e do uso**: A auditoria e o acompanhamento de uso para todos os recursos do usuário final permitem que você saiba quando os usuários estão usando seus recursos e garante que você avalie a segurança.

### <a name="licensing-considerations"></a>Considerações sobre licenciamento

O painel de acesso é gratuito e não requer licenças para usar em um todos os usuários básicos. No entanto, o número de objetos em seu diretório e os recursos que você deseja implantar podem exigir licenças adicionais. Os cenários comuns do AD do Azure incluem os seguintes recursos de segurança que têm requisitos de licenciamento.

* [Autenticação Multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Associação baseada em grupo](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Requisito de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Proteção de identidade](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Consulte o [Guia de licenciamento completo para Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Pré-requisitos para implantar o painel de acesso do AD do Azure

Os pré-requisitos a seguir devem ser concluídos antes do início deste projeto.

* [Integração de SSO de aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Infraestrutura de usuário e grupo do Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Planejar a implantação do painel de acesso do Azure AD

A tabela a seguir descreve os principais casos de uso para uma implantação do painel de acesso:

| Área| Descrição |
| - | - |
| Aceder| O portal do painel de acesso pode ser acessado de dispositivos corporativos e pessoais na rede corporativa. |
|Aceder | O portal do painel de acesso é acessível para dispositivos corporativos fora da rede corporativa. |
| Auditoria| Os dados de uso são baixados em sistemas corporativos pelo menos a cada 29 dias. |
| Governação| O ciclo de vida das atribuições de usuário aos aplicativos e grupos conectados do Azure AD é definido e monitorado. |
| Segurança| O acesso aos recursos é controlado por meio de atribuições de usuário e de grupo. Somente usuários autorizados podem gerenciar o acesso a recursos. |
| Desempenho| As linhas do tempo de propagação de atribuição de acesso são documentadas e monitoradas. |
| Experiência do usuário| Os usuários estão cientes dos recursos do painel de acesso e de como usá-los.|
| Experiência do usuário| Os usuários podem gerenciar automaticamente seu acesso a aplicativos e grupos.|
| Experiência do usuário| Os usuários podem gerenciar suas contas. |
| Experiência do usuário| Os usuários estão cientes da compatibilidade do navegador. |
| Suporte| Os usuários podem encontrar suporte para problemas do painel de acesso. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Práticas recomendadas para implantar o painel de acesso do AD do Azure

A funcionalidade do painel de acesso pode ser habilitada gradualmente. Recomendamos a seguinte ordem de implantação:

1. As Minhas Aplicações
   * Inicializador de aplicativo
   * Gerenciamento de aplicativo de autoatendimento
   * Integração do Microsoft Office 365

1. Descoberta de aplicativo de autoatendimento
   * Reposição personalizada de palavra-passe
   * Configurações de autenticação multifator
   * Gestão de dispositivos
   * Termos de utilização
   * Gerenciar organizações

1. Os Meus Grupos
   * Gestão de grupos self-service
1. Revisões de acesso
   * Gerenciamento de análise de acesso

Começar com meus aplicativos apresenta os usuários ao portal como um local comum para acessar recursos. A adição da descoberta de aplicativos de autoatendimento se baseia na experiência de meus aplicativos. Meus grupos e revisões de acesso se baseiam nos recursos de autoatendimento.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Planejar configurações para o painel de acesso do Azure AD

A tabela a seguir lista várias configurações importantes do painel de acesso e os valores típicos que você pode usar:

| Configuração| Valores típicos |
| - | - |
| Determinar os grupos-piloto| Identifique o grupo de segurança do Azure AD a ser usado e verifique se todos os membros do piloto fazem parte do grupo. |
| Determine o grupo ou grupos a serem habilitados para produção.| Identifique o grupo de segurança do Azure AD ou os grupos do AD sincronizados com o Azure AD, a serem usados. Verifique se todos os membros do piloto fazem parte do grupo. |
| Permitir que os usuários usem o logon único para quais tipos de aplicativos| SSO Federado, OAuth, SSO de senha, proxy de aplicativo |
| Permitir que os usuários usem a redefinição de senha de autoatendimento| Sim |
| Permitir que os usuários usem a autenticação multifator| Sim |
| Permitir que os usuários usem o gerenciamento de grupo de autoatendimento para quais tipos de grupos| Grupos de segurança, grupos do O365 |
| Permitir que os usuários usem o gerenciamento de aplicativo de autoatendimento| Sim |
| Permitir que os usuários usem as revisões de acesso| Sim |

### <a name="plan-consent-strategy"></a>Planejar estratégia de consentimento

Os usuários ou administradores devem consentir os termos de uso e as políticas de privacidade do aplicativo. Se possível dadas suas regras de negócios, aconselhamos o uso do consentimento do administrador, o que oferece aos usuários uma experiência melhor.

Para usar o consentimento do administrador, você deve ser um administrador global do locatário e os aplicativos devem ser:

* Registrado em seu locatário ou

* Registrado em outro locatário do Azure AD e anteriormente consentido pelo menos um usuário final.

Para obter mais informações, consulte [Configurar a maneira como os usuários finais consentim em um aplicativo no Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, eles normalmente fazem isso devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [Verifique se você está participando dos participantes certos](../fundamentals/active-directory-deployment-plans.md) e se as funções de Stakeholder no projeto são bem compreendidas.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Informe de forma proativa aos usuários como e quando sua experiência será alterada e como obter suporte, se necessário.

Embora o painel de acesso normalmente não crie problemas de usuário, é importante se preparar. Crie guias e uma lista de todos os recursos para sua equipe de suporte antes do início.

#### <a name="communications-templates"></a>Modelos de comunicação

A Microsoft fornece [modelos personalizáveis para emails e outras comunicações](https://aka.ms/APTemplates) para o painel de acesso. Você pode adaptar esses ativos para uso em outros canais de comunicação, conforme apropriado para sua cultura corporativa.

## <a name="plan-your-sso-configuration"></a>Planejar sua configuração de SSO

Quando um usuário entra em um aplicativo, ele passa por um processo de autenticação e precisa provar quem eles estão. Sem logon único, uma senha armazenada no aplicativo e o usuário precisa saber essa senha. Com credenciais de usuários de logon único (SSO) são passadas para o aplicativo para que não precisem reinserir senhas para cada aplicativo.

Para iniciar aplicativos em meus aplicativos, o SSO (logon único) deve ser habilitado para os aplicativos.

O Azure AD dá suporte a três maneiras diferentes de habilitar o [logon único para aplicativos](what-is-single-sign-on.md):

* **Logon único federado** 
    * Permite que um aplicativo Redirecione para o Azure AD para autenticação de usuário em vez de solicitar uma senha. 
    * O tem suporte para aplicativos que usam protocolos como SAML 2,0, WS-Federation ou OpenID Connect e é o modo mais avançado de logon único.

* **Logon único baseado em senha** 
    * Habilita o armazenamento de senha de aplicativo seguro e a reprodução usando uma extensão de navegador da Web ou aplicativo móvel. 
    * Aproveita o processo de entrada existente fornecido pelo aplicativo, mas permite que um administrador gerencie as senhas. Não é necessário que o usuário saiba a senha.

* **Logon único existente** 
    * Permite que o Azure AD utilize qualquer logon único existente que tenha sido configurado para o aplicativo.
    * Permite que esses aplicativos sejam vinculados aos portais do painel de acesso do Office 365 ou do Azure AD. 
    * Habilita relatórios adicionais no Azure AD quando os aplicativos são iniciados lá. 
    * Inclui o uso de Aplicativo Azure proxy e o modo de logon único vinculado.

Saiba como configurar o modo SSO de um aplicativo aqui: [Logon único para aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Para obter a melhor experiência com a página meus aplicativos, é recomendável começar com a integração de aplicativos de nuvem disponíveis para SSO Federado. O SSO Federado permite que os usuários tenham uma experiência consistente com um único clique em seu aplicativo iniciando superfícies e tende a ser mais robusto no controle de configuração.

Use o SSO Federado com o Azure AD (OpenID Connect/SAML) quando um aplicativo oferecer suporte a ele, em vez de SSO baseado em senha e ADFS.

Para obter mais informações sobre como implantar e configurar aplicativos SaaS, consulte o [plano de implantação de SSO do SaaS](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Planejar a implantação da extensão do navegador meus aplicativos

Se os aplicativos de SSO baseados em senha forem disponibilizados para os usuários finais, eles precisarão instalar a extensão de entrada segura de meus aplicativos para entrar. A extensão executa um script que transmite a senha para o formulário de entrada do aplicativo. Os usuários serão solicitados a instalar a extensão quando iniciarem o aplicativo SSO baseado em senha pela primeira vez. Mais informações sobre a extensão podem ser encontradas em nossa documentação sobre [a instalação da extensão do navegador do painel de acesso](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Se for necessário integrar aplicativos SSO baseados em senha, você deverá definir um mecanismo para implantar a extensão em escala com os [navegadores com suporte](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). As opções incluem:

* [Política de Grupo para o Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [System Center Configuration Manager (SCCM) para o Internet Explorer](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)

* [Download e configuração controlados pelo usuário para Chrome, Firefox, Microsoft Edge ou IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Saiba mais: [Como configurar o logon único com senha](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Os usuários que não usam aplicativos SSO baseados em senha também se beneficiam da extensão. Esses benefícios incluem a capacidade de iniciar qualquer aplicativo de sua barra de pesquisa, localizar acesso a aplicativos usados recentemente e ter um link para a página meus aplicativos.

Veja o que o usuário verá ao iniciar um aplicativo SSO baseado em senha pela primeira vez:

![Captura de tela de instalação da extensão do navegador meus aplicativos ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Planejar o acesso móvel

Um navegador protegido com a política do Intune (Microsoft Edge ou Intune Managed Browser) será necessário para que os usuários móveis iniciem aplicativos de SSO baseados em senha. Um navegador protegido por política permite a transferência da senha salva para o aplicativo. O Microsoft Edge ou o navegador gerenciado fornece um conjunto de recursos de proteção de dados da Web. Você também pode usar o Microsoft Edge para cenários empresariais em dispositivos iOS e Android. O Microsoft Edge dá suporte aos mesmos cenários de gerenciamento que o Intune Managed Browser e melhora a experiência do usuário final. Saiba mais: [Gerenciar o acesso à Web usando um navegador protegido por política Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>Planejar a implantação de meus aplicativos

A base do painel de acesso é o iniciador do aplicativo meus aplicativos, que os [https://myapps.microsoft.com](https://myapps.microsoft.com/)usuários acessam em. As páginas meus aplicativos fornecem aos usuários um único lugar para iniciar seu trabalho e obter seus aplicativos necessários. Aqui, os usuários encontram uma lista de todos os aplicativos aos quais têm acesso de logon único. 

![Uma captura de tela do painel de aplicativos](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Os mesmos aplicativos serão mostrados no iniciador de aplicativos do Office 365 quando os usuários estiverem usando o portal do Office 365.

Planeje a ordem na qual você adicionará aplicativos ao iniciador meus aplicativos e se você os fará de forma gradual ou todos de uma só vez. Para fazer isso, crie um inventário de aplicativos listando o tipo de autenticação e quaisquer integrações de SSO (logon único) existentes para cada aplicativo.

#### <a name="add-applications-to-the-my-apps-panel"></a>Adicionar aplicativos ao painel meus aplicativos

Qualquer aplicativo habilitado para SSO do Azure AD pode ser adicionado ao inicializador meus aplicativos. Outros aplicativos são adicionados usando a opção de SSO vinculado. Você pode configurar um bloco de aplicativo que se vincule à URL do seu aplicativo Web existente. O SSO vinculado permite que você comece a direcionar usuários para o portal meus aplicativos sem precisar migrar todos os aplicativos para Azure Active Directory SSO. Você pode migrar gradualmente para os aplicativos configurados pelo SSO do Azure AD sem interromper a experiência dos usuários.

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Planejar se deseja usar meus aplicativos ou um portal existente

Seus usuários já podem ter um aplicativo ou portal diferente de meus aplicativos. Nesse caso, decida se os dois portais serão compatíveis ou se você usará apenas um.

Se um portal existente já estiver sendo usado como um ponto de partida para os usuários, você poderá integrar a funcionalidade de meus aplicativos usando "URLs de acesso do usuário". As URLs de acesso do usuário funcionam como links diretos para os aplicativos disponíveis no portal meus aplicativos. Essas URLs podem ser inseridas em qualquer site existente. Quando um usuário clica no link, ele inicia o aplicativo no portal meus aplicativos.

Você pode encontrar a propriedade URL de acesso do usuário na área Propriedades do aplicativo na portal do Azure.

![Uma captura de tela do painel de aplicativos](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Planejar descoberta e acesso de aplicativos de autoatendimento

Depois que um conjunto principal de aplicativos é implantado na página meus aplicativos de um usuário, é recomendável habilitar recursos de gerenciamento de aplicativo de autoatendimento. A descoberta de aplicativo de autoatendimento permite:
* Os usuários encontrem novos aplicativos que eles podem adicionar aos meus aplicativos. 
* Os usuários podem adicionar aplicativos opcionais que você talvez não saiba que precisam durante a instalação.

Os fluxos de trabalho de aprovação estão disponíveis para aprovação explícita para acessar aplicativos. Os usuários que são aprovadores receberão notificações no portal meus aplicativos quando houver uma solicitação pendente de acesso ao aplicativo.

## <a name="plan-self-service-group-membership"></a>Planejar Associação de grupo de autoatendimento 

Você pode permitir que os usuários criem e gerenciem seus próprios grupos de segurança ou grupos do Office 365 no Azure Active Directory (AD do Azure). O proprietário do grupo pode aprovar ou negar solicitações de associação e delegar o controle de associação de grupo. Os recursos de gerenciamento de grupo de autoatendimento não estão disponíveis para grupos de segurança ou listas de distribuição habilitadas para email.

Para planejar a associação de grupo de autoatendimento, determine se você permitirá que todos os usuários em sua organização criem e gerenciem grupos ou apenas um subconjunto de usuários. Se um subconjunto de usuários, você precisará configurar um grupo ao qual essas pessoas são adicionadas. Consulte [Configurar o gerenciamento de grupo de autoatendimento no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) para obter detalhes sobre como habilitar esses cenários.

## <a name="plan-reporting-and-auditing"></a>Planejar relatórios e auditoria

O Azure AD fornece [relatórios que oferecem informações técnicas e de negócios](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). Trabalhe com seus proprietários de aplicativos técnicos e de negócios para assumir a propriedade e consumir esses relatórios regularmente. A tabela a seguir fornece alguns exemplos de cenários de relatório típicos.

|   | Faça a gestão do risco.| Aumentar a produtividade| Governação e conformidade |
|  - |- | - | - |
| Tipos de relatório|  Uso e permissões do aplicativo.| Atividade de provisionamento de conta| Revisar quem está acessando os aplicativos |
| Ações potenciais| Acesso de auditoria; revogar permissões| Corrigir quaisquer erros de provisionamento| Revogar acesso |

O Azure AD mantém a maioria dos dados de auditoria por 30 dias. Os dados estão disponíveis por meio do portal de administração do Azure ou da API para que você faça o download em seus sistemas de análise.

#### <a name="auditing"></a>Auditoria

Os logs de auditoria para acesso ao aplicativo estão disponíveis por 30 dias. Se a auditoria de segurança em sua empresa exigir maior retenção, os logs precisarão ser exportados para uma ferramenta SIEM (Event Information and Management, gerenciamento de informações de segurança), como Splunk ou ArcSight.

Para backups de auditoria, relatórios e recuperação de desastres, documente a frequência necessária de download, o sistema de destino é e quem é responsável por gerenciar cada backup. Talvez você não precise de backups de auditoria e de relatórios separados. O backup de recuperação de desastre deve ser uma entidade separada.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Implantar aplicativos no painel meus aplicativos dos usuários

Depois que um aplicativo tiver sido configurado para SSO, os grupos receberão acesso. Os usuários nos grupos atribuídos terão acesso e verão o aplicativo em seus aplicativos e no iniciador de aplicativos do Office 365

Consulte [atribuir usuários e grupos a um aplicativo no Active Directory](methods-for-assigning-users-and-groups.md).

Se, durante o teste ou a implantação, você quiser adicionar os grupos, mas ainda não permitir que os aplicativos sejam mostrados em meus aplicativos, consulte [ocultar um aplicativo da experiência do usuário em Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Implantar aplicativos Microsoft Office 365 em meus aplicativos

Para aplicativos do Office 365, os usuários recebem uma cópia do Office com base em licenças atribuídas a eles. Um pré-requisito para o acesso aos aplicativos do Office é atribuir usuários às licenças corretas vinculadas aos aplicativos do Office. Quando você atribuir a um usuário a licença, eles verão automaticamente os aplicativos associados à licença em sua página meus aplicativos e no inicializador de aplicativos do O365.

Se você quiser ocultar um conjunto de aplicativos do Office de usuários, há uma opção para ocultar aplicativos do portal meus aplicativos e, ao mesmo tempo, permitir o acesso do portal do O365. Encontre essas configurações na parte configurações do usuário do aplicativo. Saiba mais: [Ocultar um aplicativo da experiência do usuário em Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Uma captura de tela da configuração de como ocultar aplicativos](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Implantar recursos de autoatendimento de aplicativo

O acesso ao aplicativo de autoatendimento permite que os usuários autodescubram e solicitem acesso a qualquer aplicativo. Os usuários recebem a liberdade de obter acesso aos aplicativos de que precisam sem passar por um grupo de ti a cada vez para solicitar acesso. Quando um usuário solicita acesso e é aprovado automaticamente ou manualmente por um proprietário de aplicativo, eles são adicionados a um grupo no back-end. O relatório está habilitado em quem solicitou, aprovou ou removeu o acesso e dá a você controle sobre o gerenciamento das funções atribuídas.

Você pode delegar a aprovação de solicitações de acesso do aplicativo para os aprovadores de negócios. O aprovador de negócios pode definir as senhas de acesso ao aplicativo diretamente na página meus aplicativos do aprovador de negócios.

Saiba mais: [Como usar o acesso de aplicativo de autoatendimento](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![Uma captura de tela de configuração do gerenciamento de aplicativos de autoatendimento](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Validar sua implantação

Verifique se a implantação do painel de acesso foi exaustivamente testada e se um plano de reversão está em vigor.

Os testes a seguir devem ser conduzidos com dispositivos de propriedade corporativa e dispositivos pessoais. Esses casos de teste também devem refletir seus casos de uso de negócios. A seguir estão alguns casos com base nos requisitos de negócios de exemplo deste documento e em cenários técnicos típicos. Adicione outras pessoas específicas às suas necessidades.

#### <a name="application-sso-access-test-case-examples"></a>Exemplos de casos de teste de acesso de SSO de aplicativo:


| Caso prático| Resultado esperado |
| - | -|
| O usuário entra no portal meus aplicativos| O usuário pode entrar e ver seus aplicativos |
| O usuário inicia um aplicativo SSO Federado| O usuário é conectado automaticamente ao aplicativo |
| O usuário inicia um aplicativo de SSO de senha pela primeira vez| O usuário precisa instalar a extensão meus aplicativos |
| O usuário inicia um aplicativo de SSO de senha uma hora subsequente| O usuário é conectado automaticamente ao aplicativo |
| O usuário inicia um aplicativo do portal do O365| O usuário é conectado automaticamente ao aplicativo |
| O usuário inicia um aplicativo do Managed Browser| O usuário é conectado automaticamente ao aplicativo |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Exemplos de casos de teste de recursos de autoatendimento do aplicativo


| Caso prático| Resultado esperado |
| - | - |
| O usuário pode gerenciar a associação ao aplicativo| O usuário pode adicionar/remover membros que têm acesso ao aplicativo |
| O usuário pode editar o aplicativo| O usuário pode editar a descrição e as credenciais do aplicativo para aplicativos de SSO de senha |

### <a name="rollback-steps"></a>Etapas de reversão

É importante planejar o que fazer caso sua implantação não vá conforme planejado. Se a configuração de SSO falhar durante a implantação, você deve entender como [solucionar problemas de SSO](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) e reduzir o impacto para os usuários. Em circunstâncias extremas, talvez seja necessário [reverter o SSO](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>Gerenciar sua implementação

A Microsoft recomenda usar a função menos privilegiada para realizar uma tarefa necessária dentro de Azure Active Directory. A Microsoft recomenda [examinar as diferentes funções disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolher a correta para resolver suas necessidades para cada pessoa para este aplicativo. Algumas funções podem precisar ser aplicadas temporariamente e removidas após a conclusão da implantação.

| Pessoas fictícias| Funções| Função do Azure AD  |
| - | -| -|
| Administrador de assistência técnica| Suporte da camada 1| Nenhum |
| Administrador de identidade| Configurar e depurar quando os problemas afetam o Azure AD| Admin Global |
| Administrador do aplicativo| Atestado de usuário no aplicativo, configuração de usuários com permissões| Nenhum |
| Administradores de infraestrutura| Proprietário da substituição do certificado| Admin Global |
| Proprietário da empresa/participante| Atestado de usuário no aplicativo, configuração de usuários com permissões| Nenhum |

É recomendável usar [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) para gerenciar suas funções para fornecer auditoria adicional, controle e revisão de acesso para usuários com permissões de diretório.

### <a name="troubleshoot-access-panel-issues"></a>Solucionar problemas do painel de acesso

Crie guias de solução de problemas para sua organização de suporte com cenários comuns e apontando para a documentação da Microsoft sobre suas resoluções. Talvez você queira criar guias que interrompem o suporte nas camadas usadas pela sua organização.

Consulte os guias de solução de problemas abaixo para referência:

[Aplicativos não aparecendo](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Aplicativos inesperados aparecem](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[O usuário não pode entrar no painel de acesso](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problemas ao usar o acesso de aplicativo de autoatendimento](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problemas com a extensão do navegador](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Passos Seguintes

[Planejar uma implantação de Azure Active Directory autenticação multifator](https://aka.ms/deploymentplans/mfa)
