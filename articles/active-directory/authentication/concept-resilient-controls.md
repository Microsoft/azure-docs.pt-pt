---
title: Criar uma estratégia de gerenciamento de controle de acesso resiliente-Azure Active Directory
description: Este documento fornece orientação sobre estratégias que uma organização deve adotar para fornecer resiliência para reduzir o risco de bloqueio durante interrupções imprevistas
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/19/2018
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9be48d8f403d3ddde993ebdcf0142b55e52afce
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779680"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Crie uma estratégia de gerenciamento de controle de acesso resiliente com Azure Active Directory

>[!NOTE]
> As informações contidas neste documento representam a visão atual da Microsoft Corporation sobre os problemas discutidos na data da publicação. Como a Microsoft deve responder às mudanças nas condições do mercado, ela não deve ser interpretada como um compromisso por parte da Microsoft, e a Microsoft não pode garantir a precisão das informações apresentadas após a data da publicação.

As organizações que dependem de um único controle de acesso, como a MFA (autenticação multifator) ou um único local de rede, para proteger seus sistemas de ti são suscetíveis a falhas de acesso a seus aplicativos e recursos se esse controle de acesso único ficar indisponível ou configurado incorretamente. Por exemplo, um desastre natural pode resultar na indisponibilidade de grandes segmentos de infraestrutura de telecomunicações ou redes corporativas. Essa interrupção pode impedir que os usuários finais e os administradores possam entrar.

Este documento fornece orientação sobre estratégias que uma organização deve adotar para fornecer resiliência para reduzir o risco de bloqueio durante interrupções imprevistas com os seguintes cenários:

 1. As organizações podem aumentar sua resiliência para reduzir o risco de bloqueio **antes de uma interrupção** implementando estratégias de mitigação ou planos de contingência.
 2. As organizações podem continuar a acessar aplicativos e recursos que eles escolhem **durante uma interrupção** , tendo estratégias de mitigação e planos de contingência em vigor.
 3. As organizações devem garantir que preservam informações, como logs, **após uma interrupção** e antes de reverterem quaisquer contingências implementadas.
 4. As organizações que não implementaram estratégias de prevenção ou planos alternativos podem ser capazes de implementar **Opções de emergência** para lidar com a interrupção.

## <a name="key-guidance"></a>Diretrizes principais

Há quatro principais argumentos neste documento:

* Evite o bloqueio do administrador usando contas de acesso de emergência.
* Implemente o MFA usando o acesso condicional (CA) em vez de MFA por usuário.
* Mitigar o bloqueio do usuário usando vários controles de acesso condicional (AC).
* Reduza o bloqueio do usuário Provisionando vários métodos de autenticação ou equivalentes para cada usuário.

## <a name="before-a-disruption"></a>Antes de uma interrupção

A mitigação de uma interrupção real deve ser o foco principal de uma organização em lidar com problemas de controle de acesso que podem surgir. A mitigação inclui o planejamento de um evento real, além de implementar estratégias para garantir que os controles de acesso e as operações não sejam afetados durante as interrupções.

### <a name="why-do-you-need-resilient-access-control"></a>Por que você precisa de controle de acesso resiliente?

 Identidade é o plano de controle de usuários que acessam aplicativos e recursos. O sistema de identidade controla quais usuários e sob quais condições, como controles de acesso ou requisitos de autenticação, os usuários obtêm acesso aos aplicativos. Quando um ou mais requisitos de autenticação ou controle de acesso não estão disponíveis para que os usuários se autentiquem devido a circunstâncias imprevistas, as organizações podem enfrentar um ou ambos os seguintes problemas:

* **Bloqueio de administrador:** Os administradores não podem gerenciar o locatário ou os serviços.
* **Bloqueio de usuário:** Os usuários não podem acessar aplicativos ou recursos.

### <a name="administrator-lockout-contingency"></a>Contingência de bloqueio de administrador

Para desbloquear o acesso de administrador ao seu locatário, você deve criar contas de acesso de emergência. Essas contas de acesso de emergência, também conhecidas como contas de *ruptura* , permitem o acesso para gerenciar a configuração do Azure ad quando os procedimentos de acesso à conta com privilégios normais não estão disponíveis. Pelo menos duas contas de acesso de emergência devem ser criadas seguindo as [recomendações da conta de acesso de emergência]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

### <a name="mitigating-user-lockout"></a>Mitigando o bloqueio do usuário

 Para reduzir o risco de bloqueio de usuário, use políticas de acesso condicional com vários controles para dar aos usuários uma opção de como eles acessarão aplicativos e recursos. Ao dar a um usuário a opção entre, por exemplo, entrar com MFA **ou** entrar por meio de um dispositivo gerenciado **ou** entrar pela rede corporativa, se um dos controles de acesso não estiver disponível, o usuário terá outras opções para continuar a trabalhar.

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Incorpore os seguintes controles de acesso em suas políticas de acesso condicional existentes para a organização:

1. Provisione vários métodos de autenticação para cada usuário que dependem de diferentes canais de comunicação, por exemplo, o aplicativo Microsoft Authenticator (baseado na Internet), o token OATH (gerado no dispositivo) e o SMS (telefonia).
2. Implante o Windows Hello para empresas em dispositivos Windows 10 para atender aos requisitos de MFA diretamente da entrada do dispositivo.
3. Use dispositivos confiáveis por meio [do ingresso híbrido do Azure ad](https://docs.microsoft.com/azure/active-directory/devices/overview) ou [Microsoft Intune dispositivos gerenciados](https://docs.microsoft.com/intune/planning-guide). Os dispositivos confiáveis melhorarão a experiência do usuário, pois o próprio dispositivo confiável pode atender aos requisitos de autenticação fortes da política sem um desafio de MFA para o usuário. Em seguida, a MFA será necessária ao registrar um novo dispositivo e ao acessar aplicativos ou recursos de dispositivos não confiáveis.
4. Use políticas baseadas em risco do Azure AD Identity Protection que impeçam o acesso quando o usuário ou a entrada está em risco no lugar de políticas de MFA fixas.

>[!NOTE]
> Políticas baseadas em risco exigem licenças [Azure ad Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) .

O exemplo a seguir descreve as políticas que você deve criar para fornecer um controle de acesso resiliente para o usuário acessar seus aplicativos e recursos. Neste exemplo, você precisará de um grupo de segurança **AppUsers** com os usuários de destino aos quais deseja conceder acesso, um grupo chamado **CoreAdmins** com os administradores principais e um grupo chamado **EmergencyAccess** com as contas de acesso de emergência.
Esse conjunto de políticas de exemplo concederá aos usuários selecionados no **AppUsers**, o acesso aos aplicativos selecionados se eles estiverem se conectando de um dispositivo confiável ou fornecerem autenticação forte, por exemplo, MFA. Ele exclui as contas de emergência e os principais administradores.

**Políticas de mitigação de CA definidas:**

* Política 1: Bloquear o acesso a pessoas fora dos grupos de destino
  * Usuários e grupos: Incluir todos os usuários. Excluir AppUsers, CoreAdmins e EmergencyAccess
  * Aplicativos de nuvem: Incluir todos os aplicativos
  * Situações (Nenhum)
  * Controle de concessão: Bloquear
* Política 2: Conceda acesso ao AppUsers que exija MFA ou dispositivo confiável.
  * Usuários e grupos: Inclua AppUsers. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos de nuvem: Incluir todos os aplicativos
  * Situações (Nenhum)
  * Controle de concessão: Conceder acesso, exigir autenticação multifator, exigir que o dispositivo esteja em conformidade. Para vários controles: Requer um dos controles selecionados.

### <a name="contingencies-for-user-lockout"></a>Contingências para bloqueio de usuário

Como alternativa, sua organização também pode criar políticas de contingência. Para criar políticas de contingência, você deve definir os critérios de compensação entre a continuidade dos negócios, o custo operacional, o custo financeiro e os riscos de segurança. Por exemplo, você pode ativar uma política de contingência somente para um subconjunto de usuários, para um subconjunto de aplicativos, para um subconjunto de clientes ou de um subconjunto de locais. As políticas de contingência darão aos administradores e usuários finais acesso a aplicativos e recursos, durante uma interrupção quando nenhum método de mitigação foi implementado.
Entender sua exposição durante uma interrupção ajuda a reduzir o risco e é uma parte crítica do processo de planejamento. Para criar seu plano de contingência, primeiro determine os seguintes requisitos de negócios da sua organização:

1. Determine seus aplicativos de missão crítica antecipadamente: Quais são os aplicativos aos quais você deve conceder acesso, mesmo com uma postura de risco/segurança menor? Crie uma lista desses aplicativos e verifique se seus outros participantes (negócios, segurança, legal, liderança) concordam que, se todo o controle de acesso desaparecer, esses aplicativos ainda devem continuar em execução. Você provavelmente vai acabar com categorias de:
   * **Categoria 1 aplicativos de missão crítica** que não podem ficar indisponíveis por mais de alguns minutos, por exemplo, aplicativos que afetam diretamente a receita da organização.
   * **Categoria 2 aplicativos importantes** que a empresa precisa para ser acessada em algumas horas.
   * **Categoria 3 aplicativos de baixa prioridade** que podem resistir a uma interrupção de alguns dias.
2. Para aplicativos na categoria 1 e 2, a Microsoft recomenda que você planeje previamente o tipo de nível de acesso que deseja permitir:
   * Deseja permitir acesso completo ou sessão restrita, como limitar downloads?
   * Deseja permitir o acesso a parte do aplicativo, mas não a todo o aplicativo?
   * Deseja permitir o acesso do operador de informações e bloquear o acesso de administrador até que o controle de acesso seja restaurado?
3. Para esses aplicativos, a Microsoft também recomenda que você planeje quais vias de acesso você abrirá deliberadamente e quais serão fechadas:
   * Deseja permitir que o navegador só acesse e bloqueie clientes avançados que podem salvar dados offline?
   * Deseja permitir o acesso somente para usuários dentro da rede corporativa e manter usuários fora do bloqueio?
   * Deseja permitir o acesso de determinados países ou regiões somente durante a interrupção?
   * Você deseja políticas para as políticas de contingência, especialmente para aplicativos de missão crítica, para falha ou êxito se um controle de acesso alternativo não estiver disponível?

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Uma política de acesso condicional de contingência é uma **política desabilitada** que OMITE a MFA do Azure, a MFA de terceiros, os controles baseados em risco ou no dispositivo. Em seguida, quando sua organização decidir ativar seu plano de contingência, os administradores poderão habilitar a política e desabilitar as políticas comuns baseadas em controle.

>[!IMPORTANT]
> A desabilitação de políticas que impõem a segurança em seus usuários, mesmo temporariamente, reduzirá sua postura de segurança enquanto o plano de contingência estiver em vigor.

* Configure um conjunto de políticas de fallback se uma interrupção em um tipo de credencial ou um mecanismo de controle de acesso impactar o acesso aos seus aplicativos. Configure uma política em estado desabilitado que exija ingresso no domínio como um controle, como um backup para uma política ativa que requer um provedor de MFA de terceiros.
* Reduza o risco de atores ruins adivinharem senhas, quando a MFA não for necessária, seguindo as práticas no [Guia de senha](https://aka.ms/passwordguidance) White Paper.
* Implante a [SSPR (redefinição de senha de autoatendimento) do Azure ad](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) e a [proteção de senha do Azure ad](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) para garantir que os usuários não usem a senha e os termos comuns que você optar por proibir.
* Use políticas que restringem o acesso nos aplicativos se um determinado nível de autenticação não for Obtido em vez de simplesmente fazer fallback para acesso completo. Por exemplo:
  * Configure uma política de backup que envia a declaração de sessão restrita para o Exchange e o SharePoint.
  * Se sua organização usa Microsoft Cloud App Security, considere voltar a uma política que envolva MCAS e, em seguida, MCAS permitirá o acesso somente leitura, mas não os carregamentos.
* Nomeie suas políticas para garantir que seja fácil encontrá-las durante uma interrupção. Inclua os seguintes elementos no nome da política:
  * Um *número de rótulo* para a política.
  * Texto a ser mostrado, esta política destina-se apenas a emergências. Por exemplo: **HABILITAR EM EMERGÊNCIA**
  * A *interrupção* à qual ele se aplica. Por exemplo: **Durante a interrupção do MFA**
  * Um *número de sequência* para mostrar a ordem em que você deve ativar as políticas.
  * Os *aplicativos* aos quais ele se aplica.
  * Os *controles* que serão aplicados.
  * As *condições* necessárias.
  
Esse padrão de nomenclatura para as políticas de contingência será o seguinte: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

O exemplo a seguir: **Um exemplo de política de CA de contingência para restaurar o acesso a aplicativos de colaboração de missão crítica**é uma contingência corporativa típica. Nesse cenário, a organização normalmente requer MFA para todos os acessos do Exchange Online e do SharePoint Online, e a interrupção nesse caso é que o provedor de MFA para o cliente tem uma interrupção (seja o Azure MFA, provedor de MFA local ou MFA de terceiros). Essa política atenua essa interrupção, permitindo que usuários direcionados específicos acessem esses aplicativos de dispositivos Windows confiáveis somente quando acessarem o aplicativo de sua rede corporativa confiável. Ele também excluirá as contas de emergência e os administradores principais dessas restrições. Os usuários de destino obterão acesso ao Exchange Online e ao SharePoint Online, enquanto outros usuários ainda não terão acesso aos aplicativos devido à interrupção. Este exemplo exigirá um local de rede nomeado **CorpNetwork** e um grupo de segurança **ContingencyAccess** com os usuários de destino, um grupo chamado **CoreAdmins** com os administradores de núcleo e um grupo chamado **EmergencyAccess** com as contas de acesso de emergência. A contingência requer quatro políticas para fornecer o acesso desejado. 

**Exemplo de políticas de AC de contingência para restaurar o acesso a aplicativos de colaboração de missão crítica:**

* Política 1: Exigir dispositivos ingressados no domínio para o Exchange e o SharePoint
  * Nome: EM001-HABILITAR EM EMERGÊNCIA: Interrupção do MFA [1/4]-Exchange SharePoint-exigir ingresso no Azure AD híbrido
  * Usuários e grupos: Inclua ContingencyAccess. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos de nuvem: Exchange Online e SharePoint Online
  * Situações Any
  * Controle de concessão: Exigir ingresso no domínio
  * Estado: Desativado
* Política 2: Bloquear plataformas diferentes do Windows
  * Nome: EM002-HABILITAR EM EMERGÊNCIA: Interrupção de MFA [2/4]-Exchange SharePoint-bloquear acesso, exceto Windows
  * Usuários e grupos: Incluir todos os usuários. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos de nuvem: Exchange Online e SharePoint Online
  * Situações A plataforma de dispositivo inclui todas as plataformas, excluir janelas
  * Controle de concessão: Bloquear
  * Estado: Desativado
* Política 3: Bloquear redes que não sejam CorpNetwork
  * Nome: EM003-HABILITAR EM EMERGÊNCIA: Interrupção de MFA [3/4]-Exchange SharePoint-bloquear acesso, exceto rede corporativa
  * Usuários e grupos: Incluir todos os usuários. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos de nuvem: Exchange Online e SharePoint Online
  * Situações Os locais incluem qualquer local, excluir CorpNetwork
  * Controle de concessão: Bloquear
  * Estado: Desativado
* Política 4: Bloquear EAS explicitamente
  * Nome: EM004-HABILITAR EM EMERGÊNCIA: Interrupção de MFA [4/4]-Exchange-Block EAS para todos os usuários
  * Usuários e grupos: Incluir todos os usuários
  * Aplicativos de nuvem: Incluir o Exchange Online
  * Situações Aplicativos cliente: Exchange Active Sync
  * Controle de concessão: Bloquear
  * Estado: Desativado

Ordem de ativação:

1. Exclua ContingencyAccess, CoreAdmins e EmergencyAccess da política de MFA existente. Verifique se um usuário no ContingencyAccess pode acessar o SharePoint Online e o Exchange Online.
2. Habilitar política 1: Verifique se os usuários em dispositivos ingressados no domínio que não estão nos grupos de exclusão são capazes de acessar o Exchange Online e o SharePoint Online. Verifique se os usuários no grupo excluir podem acessar o SharePoint Online e o Exchange de qualquer dispositivo.
3. Habilitar política 2: Verifique se os usuários que não estão no grupo de exclusão não podem acessar o SharePoint Online e o Exchange Online de seus dispositivos móveis. Verifique se os usuários no grupo excluir podem acessar o SharePoint e o Exchange de qualquer dispositivo (Windows/iOS/Android).
4. Habilitar política 3: Verifique se os usuários que não estão nos grupos de exclusão não podem acessar o SharePoint e o Exchange na rede corporativa, mesmo com um computador ingressado no domínio. Verifique se os usuários no grupo excluir podem acessar o SharePoint e o Exchange em qualquer rede.
5. Habilitar política 4: Verifique se todos os usuários não podem obter o Exchange Online dos aplicativos de email nativos em dispositivos móveis.
6. Desabilite a política de MFA existente para o SharePoint Online e o Exchange Online.

Neste próximo exemplo, **exemplo B-políticas de AC de contingência para permitir o acesso móvel ao Salesforce**, o acesso de um aplicativo de negócios é restaurado. Nesse cenário, o cliente normalmente exige que seus funcionários de vendas acessem o Salesforce (configurado para logon único com o Azure AD) de dispositivos móveis para serem permitidos somente de dispositivos em conformidade. Nesse caso, a interrupção é que há um problema na avaliação da conformidade do dispositivo e a interrupção está ocorrendo em um momento confidencial em que a equipe de vendas precisa acessar o Salesforce para fechar as negociações. Essas políticas de contingência concederão aos usuários críticos acesso ao Salesforce de um dispositivo móvel para que possam continuar a fechar as negociações e não interromper os negócios. Neste exemplo, **SalesforceContingency** contém todos os funcionários de vendas que precisam manter o acesso e **SalesAdmins** contém os administradores necessários do Salesforce.

**Exemplo B-políticas da AC de contingência:**

* Política 1: Bloquear todos que não estão na equipe do SalesContingency
  * Nome: EM001-HABILITAR EM EMERGÊNCIA: Interrupção de conformidade do dispositivo [1/2]-Salesforce-bloquear todos os usuários, exceto SalesforceContingency
  * Usuários e grupos: Incluir todos os usuários. Excluir SalesAdmins e SalesforceContingency
  * Aplicativos de nuvem: Equipe.
  * Situações Nenhuma
  * Controle de concessão: Bloquear
  * Estado: Desativado
* Política 2: Bloquear a equipe de vendas de qualquer plataforma que não seja a móvel (para reduzir a área de ataque da superfície)
  * Nome: EM002-HABILITAR EM EMERGÊNCIA: Interrupção de conformidade do dispositivo [2/2]-Salesforce-bloquear todas as plataformas, exceto iOS e Android
  * Usuários e grupos: Inclua SalesforceContingency. Excluir SalesAdmins
  * Aplicativos de nuvem: Salesforce
  * Situações A plataforma de dispositivo inclui todas as plataformas, excluir iOS e Android
  * Controle de concessão: Bloquear
  * Estado: Desativado

Ordem de ativação:

1. Exclua SalesAdmins e SalesforceContingency da política de conformidade do dispositivo existente para o Salesforce. Verifique se um usuário no grupo SalesforceContingency pode acessar o Salesforce.
2. Habilitar política 1: Verifique se os usuários fora do SalesContingency não podem acessar o Salesforce. Verifique se os usuários no SalesAdmins e SalesforceContingency podem acessar o Salesforce.
3. Habilitar política 2: Verifique se os usuários no grupo SalesContingency não podem acessar o Salesforce de seus laptops Windows/Mac, mas ainda podem acessar de seus dispositivos móveis. Verifique se o SalesAdmin ainda pode acessar o Salesforce de qualquer dispositivo.
4. Desabilite a política de conformidade do dispositivo existente para o Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Implantar a sincronização de hash de senha mesmo se você for federado ou usar a autenticação de passagem

O bloqueio do usuário também pode ocorrer se as seguintes condições forem verdadeiras:

- Sua organização usa uma solução de identidade híbrida com autenticação de passagem ou Federação.
- Seus sistemas de identidade locais (como Active Directory, AD FS ou um componente dependente) não estão disponíveis. 
 
Para ser mais resiliente, sua organização deve [habilitar a sincronização de hash de senha](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), pois ela permite que você [alterne para usando a sincronização de hash de senha](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) se os sistemas de identidade locais estiverem inativos.

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft
 Habilite a sincronização de hash de senha usando o assistente de Azure AD Connect, independentemente de sua organização usar Federação ou autenticação de passagem.

>[!IMPORTANT]
> Não é necessário converter usuários de autenticação federada para gerenciada para usar a sincronização de hash de senha.

## <a name="during-a-disruption"></a>Durante uma interrupção

Se você optou por implementar um plano de mitigação, poderá sobreviver automaticamente a uma única interrupção do controle de acesso. No entanto, se você optou por criar um plano de contingência, poderá ativar suas políticas de contingência durante a interrupção do controle de acesso:

1. Habilite suas políticas de contingência que concedem aos usuários direcionados, acesso a aplicativos específicos, de redes específicas.
2. Desabilite suas políticas baseadas em controle regular.

### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Dependendo de quais atenuações ou contingências são usadas durante uma interrupção, sua organização pode conceder acesso apenas a senhas. Nenhuma proteção é um risco de segurança considerável que deve ser avaliado com cuidado. As organizações devem:

1. Como parte da sua estratégia de controle de alterações, documente cada alteração e o estado anterior para poder reverter quaisquer contingências que você implementou assim que os controles de acesso estiverem totalmente operacionais.
2. Suponha que os atores mal-intencionados tentarão coletar senhas por meio de ataques de phishing ou de irrigação de senha enquanto você desabilita a MFA. Além disso, os atores ruins já podem ter senhas que anteriormente não concederam acesso a nenhum recurso que possa ser tentado durante essa janela. Para usuários críticos, como executivos, você pode reduzir parcialmente esse risco redefinindo suas senhas antes de desabilitar a MFA para elas.
3. Arquive todas as atividades de entrada para identificar quem acessa o que durante o tempo que o MFA foi desabilitado.
4. Fazer triagem de [todos os eventos de risco](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) relatados durante esta janela.

## <a name="after-a-disruption"></a>Após uma interrupção

Desfaça as alterações feitas como parte do plano de contingência ativado depois que o serviço for restaurado que causou a interrupção. 

1. Habilitar as políticas regulares
2. Desabilite suas políticas de contingência. 
3. Reverter quaisquer outras alterações feitas e documentadas durante a interrupção.
4. Se você usou uma conta de acesso de emergência, lembre-se de regenerar as credenciais e proteger fisicamente os novos detalhes de credenciais como parte dos procedimentos da sua conta de acesso de emergência.
5. Continue a fazer a triagem de [todos os eventos de risco](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) relatados após a interrupção de atividade suspeita.
6. Revogue todos os tokens de atualização que foram emitidos [usando o PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) para direcionar um conjunto de usuários. A revogação de todos os tokens de atualização é importante para contas privilegiadas usadas durante a interrupção e fazê-lo irá forçá-los a autenticar novamente e a atender ao controle das políticas restauradas.

## <a name="emergency-options"></a>Opções de emergência

 No caso de uma emergência e sua organização não implementar anteriormente um plano de mitigação ou contingência, siga as recomendações na seção contingências [para bloqueio de usuário](#contingencies-for-user-lockout) se eles já usarem políticas de acesso condicional para impor a MFA.
Se sua organização estiver usando políticas herdadas de MFA por usuário, você poderá considerar a seguinte alternativa:

1. Se você tiver o endereço IP de saída da rede corporativa, poderá adicioná-los como IPs confiáveis para habilitar a autenticação somente para a rede corporativa.
   1. Se você não tiver o inventário de endereços IP de saída ou precisar habilitar o acesso dentro e fora da rede corporativa, poderá adicionar todo o espaço de endereço IPv4 como IPs confiáveis, especificando 0.0.0.0/1 e 128.0.0.0/1.

>[!IMPORTANT]
 > Se você ampliar os endereços IP confiáveis para desbloquear o acesso, os eventos de risco associados a endereços IP (por exemplo, viagens impossíveis ou locais desconhecidos) não serão gerados.

>[!NOTE]
 > A configuração de [IPs confiáveis](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) para o Azure MFA só está disponível com [licenças Azure ad Premium](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>Saber mais

* [Documentação de autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Gerir o acesso de emergência de contas administrativas no Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Configurar locais nomeados no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Como configurar dispositivos ingressados no Azure Active Directory híbrido](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Guia de Implementação do Windows Hello para Empresas](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Diretrizes de senha-Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [O que são condições em Azure Active Directory acesso condicional?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [O que são controles de acesso no Azure Active Directory acesso condicional?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
