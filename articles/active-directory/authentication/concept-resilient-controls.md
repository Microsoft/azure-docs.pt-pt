---
title: Criar uma estratégia de gestão de controlo de acesso resilientes no Azure Active Directory
description: Este documento fornece orientação sobre estratégias de uma organização deve adotar para fornecer resiliência para reduzir o risco de bloqueio durante as interrupções imprevistas
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/19/2018
ms.author: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: b006a4fbb8d1059f5096f5c1585853953b69042f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082148"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Criar uma estratégia de gestão de controlo de acesso resilientes no Azure Active Directory

>[!NOTE]
> As informações contidas neste documento representam a visão atual da Microsoft Corporation sobre os assuntos discutidos na data da publicação. Como a Microsoft deve reagir às mudanças do mercado, não devem ser interpretadas como um compromisso por parte da Microsoft e a Microsoft não garante a precisão de qualquer informação apresentada após a data da publicação.

As organizações que contam com um controle de acesso único, como autenticação multifator (MFA) ou uma localização de rede único, para proteger seus sistemas de TI são suscetíveis a falhas de acesso às suas aplicações e recursos se esse controlo de acesso único ficar indisponível ou mal configurados. Por exemplo, um desastre natural pode resultar na indisponibilidade de grandes segmentos de infra-estrutura de telecomunicações nem de redes corporativas. Tal uma interrupção poderia impedir que os utilizadores finais e os administradores a capacidade de iniciar sessão.

Este documento fornece orientação sobre estratégias de uma organização deve adotar para fornecer resiliência para reduzir o risco de bloqueio durante as interrupções imprevistas com os seguintes cenários:

 1. As organizações podem aumentar seus resiliência para reduzir o risco de bloqueio **antes de uma interrupção** ao implementar estratégias de redução ou planos de contingência.
 2. As organizações podem continuar a aceder a aplicações e recursos que escolherem **durante uma interrupção** por ter estratégias de atenuação e planos de contingência em vigor.
 3. Certifique-se de preservar a informações, tais como registos, as organizações **após uma interrupção** e antes de eles reverter qualquer contingências eles implementados.
 4. As organizações que ainda não foram implementadas estratégias de prevenção ou planos alternativos podem ser capazes de implementar **opções de emergência** para lidar com a interrupção.

## <a name="key-guidance"></a>Principais orientações

Existem quatro principais pedidas neste documento:

* Evite o bloqueio de administrador, utilizando as contas de acesso de emergência.
* Implementar o MFA através do acesso condicional (AC) em vez de MFA por utilizador.
* Reduza o bloqueio do utilizador através de vários controlos de acesso condicional (AC).
* Reduza o bloqueio do utilizador através do aprovisionamento de vários métodos de autenticação ou equivalentes para cada utilizador.

## <a name="before-a-disruption"></a>Antes de uma interrupção

Minimização de interrupção real tem de ser principal foco de uma organização lidar com problemas de controlo de acesso que podem surgir. Mitigar inclui planeamento de um evento real além de implementar estratégias para tornar-se de controlos de acesso e operações não são afetadas durante as interrupções.

### <a name="why-do-you-need-resilient-access-control"></a>Por que precisa de controlo de acesso resilientes?

 A identidade é o plano de controlo de utilizadores que acedem às aplicações e recursos. O sistema de identidade controla quais usuários e sob quais condições, como controlos de acesso ou os requisitos de autenticação, os utilizadores obtêm acesso aos aplicativos. Quando um ou mais requisitos de controlo de acesso ou autenticação não estão disponíveis para os utilizadores sejam autenticados devido a circunstâncias imprevistas, as organizações podem assistir a um ou ambos dos seguintes problemas:

* **Bloqueio de administrador:** Os administradores não é possível gerir o inquilino ou serviços.
* **Bloqueio de utilizador:** Os utilizadores não é possível aceder a aplicações ou recursos.

### <a name="administrator-lockout-contingency"></a>Contingência de bloqueio de administrador

Para desbloquear o acesso de administrador ao seu inquilino, deve criar contas de acesso de emergência. Estas contas de acesso de emergência, também conhecidas como *quebrar o vidro* contas, permitir o acesso gerir a configuração do Azure AD quando os procedimentos de acesso de conta com privilégios normais não estão disponíveis. Pelo menos duas contas de acesso de emergência devem ser criadas a seguir a [recomendações da conta de acesso de emergência]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

### <a name="mitigating-user-lockout"></a>Redução de bloqueio do utilizador

 Para mitigar o risco de bloqueio do utilizador, utilize políticas de acesso condicional com vários controles para dar aos utilizadores uma opção de como eles acessarão aplicações e recursos. Ao dar um utilizador a escolha entre, por exemplo, iniciar sessão com a MFA **ou** iniciar sessão a partir de um dispositivo gerido **ou** tem do utilizador iniciar sessão a partir da rede empresarial, se um dos controles de acesso não está disponível outras opções para continuar a trabalhar.

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Incorpore os seguintes controlos de acesso nas políticas de acesso condicional existentes para a organização:

1. Aprovisione vários métodos de autenticação para cada utilizador que se baseiam nos canais de comunicação diferentes, por exemplo a aplicação Microsoft Authenticator (baseado na internet), token OATH (gerado no dispositivo) e SMS (telephonic).
2. Implemente o Windows Hello para empresas em dispositivos Windows 10 para satisfazer os requisitos de MFA diretamente a partir do início de sessão no dispositivo.
3. Utilizar dispositivos fidedignos através de [associação ao Azure AD híbrido](https://docs.microsoft.com/azure/active-directory/devices/overview) ou [dispositivos geridos do Microsoft Intune](https://docs.microsoft.com/intune/planning-guide). Dispositivos fidedignos irão melhorar a experiência de utilizador porque o dispositivo fidedigno em si pode satisfazer os requisitos de autenticação forte de política sem um desafio MFA para o utilizador. MFA, em seguida, será necessário quando inscrever um dispositivo novo e ao aceder a aplicações ou recursos dos dispositivos não fidedignos.
4. Utilize do Azure AD identity baseado no risco políticas de proteção que impeça o acesso quando o utilizador ou o início de sessão está em risco em vez de políticas MFA fixos.

>[!NOTE]
> Políticas baseadas no risco requerem [do Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) licenças.

O exemplo a seguir descreve as políticas, tem de criar para fornecer um controlo de acesso resilientes para o utilizador aceder às aplicações e recursos. Neste exemplo, vai precisar de um grupo de segurança **AppUsers** com os utilizadores de destino que pretende dar acesso a, um grupo chamado **CoreAdmins** com os administradores de núcleos e um grupo chamado  **EmergencyAccess** com as contas de acesso de emergência.
Este conjunto de políticas de exemplo irá conceder a utilizadores selecionados no **AppUsers**, acesso a aplicações selecionadas, se eles são a ligar a partir de um dispositivo fidedigno ou fornecer uma autenticação segura, por exemplo, MFA. Ela exclui as contas de emergência e administradores de núcleo.

**Conjunto de políticas de atenuação de AC:**

* Política de 1: Bloquear o acesso às pessoas fora de grupos de destino
  * Utilizadores e grupos: Inclua todos os utilizadores. Excluir AppUsers, CoreAdmins e EmergencyAccess
  * Aplicações na cloud: Incluir todas as aplicações
  * Condições: (Nenhum)
  * Controlo da concessão: Bloquear
* Política de 2: Conceder acesso para AppUsers exigir MFA ou dispositivo fidedigno.
  * Utilizadores e grupos: Inclua AppUsers. Excluir CoreAdmins e EmergencyAccess
  * Aplicações na cloud: Incluir todas as aplicações
  * Condições: (Nenhum)
  * Controlo da concessão: Conceder acesso, exigir autenticação multifator, pedir que o dispositivo para estar em conformidade. Para vários controles: Exigir um dos controlos selecionados.

### <a name="contingencies-for-user-lockout"></a>Contingências para bloqueio do utilizador

Em alternativa, a sua organização também pode criar políticas de contingência. Para criar políticas de contingência, tem de definir critérios de compromisso entre a continuidade do negócio, custo operacional, custo financeiro e riscos de segurança. Por exemplo, pode ativar uma política de contingência apenas a um subconjunto de utilizadores, para um subconjunto de aplicações, para um subconjunto de clientes ou a partir de um subconjunto das localizações. Políticas de contingência proporciona administradores e usuários finais acesso a aplicações e recursos, durante uma interrupção quando nenhum método de atenuação foi implementado.
Compreender a sua exposição durante uma interrupção ajuda a reduzir o risco e é uma parte crucial do seu processo de planejamento. Para criar o seu plano de contingência, determine primeiro os seguintes requisitos comerciais da sua organização:

1. Determine as suas aplicações fundamentais antecipadamente: Quais são as aplicações que tem de dar acesso, mesmo com uma postura de segurança de risco/inferior? Crie uma lista destas aplicações e certificar-se de que os outros interessados (empresas, liderança legais, de segurança,) todos os concordam que se o controlo de acesso de todos os desaparecer, esses aplicativos ainda devem continuar a executar. Provavelmente vai final com as categorias de:
   * **Categoria 1 aplicações fundamentais** que não pode estar indisponível por mais de alguns minutos, por exemplo, as aplicações que afetam diretamente a receita da organização.
   * **Aplicações importantes de categoria 2** que a empresa precisa estar acessível em algumas horas.
   * **Aplicações de baixa prioridade de categoria 3** que possa suportar uma interrupção de alguns dias.
2. Para aplicações de categoria 1 e 2, a Microsoft recomenda a que planear previamente o tipo de nível de acesso que pretende permitir:
   * Pretende permitir o acesso total ou sessão restrita, como limitar downloads?
   * Pretende permitir o acesso para a parte da aplicação, mas não a aplicação toda?
   * Pretende permitir o acesso às informações da função de trabalho e bloquear o acesso de administrador até que seja restaurado o controlo de acesso?
3. Para essas aplicações, a Microsoft recomenda também que planear as avenidas de acesso deliberadamente vai abrir e quais as que será fechada:
   * Deseja permitir browser apenas acesso e bloquear clientes avançados que podem guardar os dados offline?
   * Pretende permitir o acesso apenas para os utilizadores dentro da rede empresarial e manter fora utilizadores bloqueados?
   * Pretende permitir o acesso de determinados países ou regiões apenas durante a interrupção?
   * Deseja que as políticas para as políticas de contingência, especialmente para aplicações fundamentais, uma falha ou êxito se um controlo de acesso alternativo não está disponível?

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Uma política de acesso condicional de contingência é uma **desativado política** que omite controles de Azure MFA, MFA de terceiros, com base no risco ou com base no dispositivo. Em seguida, quando sua organização decide ativar o plano de contingência, os administradores podem ativar a política e desative as políticas com base no controle regulares.

>[!IMPORTANT]
> Desativar as políticas que impõem segurança os seus utilizadores, até mesmo temporariamente, irá reduzir sua postura de segurança enquanto o plano de contingência está em vigor.

* Configure um conjunto de políticas de contingência se uma interrupção num tipo de credencial ou um acesso controlo mecanismo impactos de acesso às suas aplicações. Configure uma política no estado desativado, que requer a associação a um domínio como um controle, como uma cópia de segurança para uma política do Active Directory que necessita de um fornecedor MFA de terceiros.
* Reduzir o risco de mal-intencionadas, a adivinhação de palavras-passe, quando MFA não é necessário, ao seguir as práticas de [orientações de palavra-passe](https://aka.ms/passwordguidance) white paper.
* Implementar [do Azure AD Self-Service palavra-passe de reposição (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) e [proteção de palavra-passe do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) para se certificar de que os utilizadores não utilizar a palavra-passe e optar por banir de termos comuns.
* Utilize as políticas que restringem o acesso a aplicações, se um determinado nível de autenticação não é obtido em vez de simplesmente reverter para acesso total. Por exemplo:
  * Configure uma política de cópia de segurança que envia a afirmação de sessão restrito ao Exchange e SharePoint.
  * Se a sua organização utiliza o Microsoft Cloud App Security, considere reverter para uma política que seja aplicada MCAS e, em seguida, acesso só de leitura permite MCAS, mas não carrega.
* Atribuir nomes às políticas para se certificar de que é fácil encontrá-los durante uma interrupção. Incluem os seguintes elementos no nome da política:
  * R *Etiquetar número* para a política.
  * Texto a mostrar, esta política destina-se apenas a emergências. Por exemplo: **PERMITIR DE EMERGÊNCIA**
  * O *interrupção* aplica-se. Por exemplo: **Durante a interrupção do MFA**
  * R *número de sequência* para mostrar a ordem, terá de ativar as políticas.
  * O *aplicações* aplica-se.
  * O *controles* será aplicada.
  * O *condições* requer.
  
Este padrão de nomenclatura para as políticas de contingência será da seguinte forma: 

`
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
`

O exemplo seguinte: **O exemplo A - política de AC de contingência para restaurar o acesso a aplicações de colaboração de missão crítica**, é uma contingência empresarial típica. Neste cenário, a organização normalmente requer MFA para todos os acessos de Exchange Online e SharePoint Online e, nesse caso, a interrupção é que o fornecedor do MFA para o cliente tem um período de indisponibilidade (quer a MFA do Azure, no local fornecedor do MFA, ou MFA de terceiros). Esta política atenua este período de inatividade, permitindo que os utilizadores de destinados específicos acesso a estas aplicações de dispositivos fidedignos do Windows apenas quando estão a aceder a aplicação a partir da respetiva rede empresarial fidedigno. Ele irá também excluir contas de emergência e administradores de núcleo essas restrições. Os utilizadores de destinados, em seguida, irão obter acesso ao Exchange Online e SharePoint Online, enquanto outros utilizadores ainda não terão acesso às aplicações devido a falha. Esse exemplo exigirá uma localização de rede nomeado **CorpNetwork** e um grupo de segurança **ContingencyAccess** com os utilizadores de destino, um grupo chamado **CoreAdmins** com o Os administradores de núcleos e um grupo chamado **EmergencyAccess** com as contas de acesso de emergência. A contingência requer quatro políticas para fornecer o acesso desejado. 

**Exemplo A - políticas de AC de contingência para restaurar o acesso a aplicações de colaboração de missão crítica:**

* Política de 1: Exigir que os dispositivos associados a um domínio para o Exchange e SharePoint
  * Nome: EM001 - ATIVAR EM EMERGÊNCIA: Interrupção da MFA [1/4] - Exchange SharePoint – exigir a associação do Azure AD híbrido
  * Utilizadores e grupos: Inclua ContingencyAccess. Excluir CoreAdmins e EmergencyAccess
  * Aplicações na cloud: Exchange Online e SharePoint Online
  * Condições: Qualquer
  * Controlo da concessão: Exigir a associação a um domínio
  * Estado: Desativado
* Política de 2: Impedir as plataformas que não sejam Windows
  * Nome: EM002 - ATIVAR EM EMERGÊNCIA: Interrupção da MFA acesso de - Exchange SharePoint - bloco de [2/4], exceto o Windows
  * Utilizadores e grupos: Inclua todos os utilizadores. Excluir CoreAdmins e EmergencyAccess
  * Aplicações na cloud: Exchange Online e SharePoint Online
  * Condições: Plataforma incluem todas as plataformas de dispositivos, excluir o Windows
  * Controlo da concessão: Bloquear
  * Estado: Desativado
* Política de 3: Redes de bloco que não seja CorpNetwork
  * Nome: EM003 - ATIVAR EM EMERGÊNCIA: Interrupção da MFA acesso de - Exchange SharePoint - bloco de [3/4], exceto a rede empresarial
  * Utilizadores e grupos: Inclua todos os utilizadores. Excluir CoreAdmins e EmergencyAccess
  * Aplicações na cloud: Exchange Online e SharePoint Online
  * Condições: Localizações incluem qualquer localização, excluir CorpNetwork
  * Controlo da concessão: Bloquear
  * Estado: Desativado
* Política de 4: Bloquear explicitamente o EAS
  * Nome: EM004 - ATIVAR EM EMERGÊNCIA: Interrupção da MFA [4/4] - Exchange - bloco EAS para todos os utilizadores
  * Utilizadores e grupos: Inclua todos os utilizadores
  * Aplicações na cloud: Incluir o Exchange Online
  * Condições: Aplicações de cliente: Exchange Active Sync
  * Controlo da concessão: Bloquear
  * Estado: Desativado

Ordem de ativação:

1. Exclua ContingencyAccess CoreAdmins e EmergencyAccess da política MFA existente. Certifique-se de que um utilizador no ContingencyAccess pode acessar o SharePoint Online e Exchange Online.
2. Ative política de 1: Certifique-se de que os utilizadores em dispositivos associados a um domínio que não estão a ser os excluir grupos são capazes de aceder ao Exchange Online e SharePoint Online. Certifique-se de que os utilizadores no grupo de exclusão podem aceder a SharePoint Online e o Exchange de qualquer dispositivo.
3. Ative política de 2: Certifique-se de que os utilizadores que não estão no grupo de exclusão não é possível obter ao SharePoint Online e Exchange Online a partir dos respetivos dispositivos móveis. Certifique-se de que os utilizadores no grupo de exclusão podem aceder a SharePoint e o Exchange de qualquer dispositivo (Windows/iOS/Android).
4. Ative política de 3: Certifique-se de que os utilizadores que não estão a ser os excluir grupos não é possível aceder ao SharePoint e Exchange fora da rede corporativa, mesmo com um domínio associado a um computador. Certifique-se de que os utilizadores no grupo de exclusão podem aceder a SharePoint e o Exchange de qualquer rede.
5. Ative política de 4: Certifique-se de que todos os utilizadores não é possível obter Exchange Online das aplicações de e-mail nativo nos dispositivos móveis.
6. Desative a política MFA existente para o SharePoint Online e Exchange Online.

Neste próximo exemplo, **exemplo B - políticas de CA de contingência para permitir o acesso móvel a Salesforce**, o acesso de uma aplicação de negócio é restaurado. Neste cenário, o cliente normalmente requer o acesso de funcionários de vendas para o Salesforce (configurado para início de sessão único com o Azure AD) de dispositivos móveis para apenas permitido a partir de dispositivos em conformidade. A interrupção nesse caso, é que há um problema com a avaliação de conformidade do dispositivo e a falha está a acontecer ao mesmo tempo confidencial em que as vendas da equipe de necessidades de acesso ao Salesforce para fechar negócios. Estas políticas de contingência irão conceder críticos aos utilizadores acesso ao Salesforce de um dispositivo móvel para que eles podem continuar a fechar negócios e não interrompem o negócio. Neste exemplo, **SalesforceContingency** contém todos os funcionários de vendas que precisam para manter o acesso e **SalesAdmins** contém necessário admins do Salesforce.

**Exemplo B - políticas de AC de contingência:**

* Política de 1: Bloquear todas as pessoas não na equipe do SalesContingency
  * Nome: EM001 - ATIVAR EM EMERGÊNCIA: Interrupção de conformidade do dispositivo [1/2] - Salesforce - os utilizadores de todos os blocos, exceto SalesforceContingency
  * Utilizadores e grupos: Inclua todos os utilizadores. Excluir SalesAdmins e SalesforceContingency
  * Aplicações na cloud: Salesforce.
  * Condições: Nenhuma
  * Controlo da concessão: Bloquear
  * Estado: Desativado
* Política de 2: Bloquear a equipe de vendas a partir de qualquer plataforma que não seja mobile (para reduzir a área de superfície de ataque)
  * Nome: EM002 - ATIVAR EM EMERGÊNCIA: Interrupção de conformidade do dispositivo [2/2]. - Salesforce - bloquear todas as plataformas exceto iOS e Android
  * Utilizadores e grupos: Inclua SalesforceContingency. Excluir SalesAdmins
  * Aplicações na cloud: Salesforce
  * Condições: Plataforma incluem todas as plataformas de dispositivos, excluir o iOS e Android
  * Controlo da concessão: Bloquear
  * Estado: Desativado

Ordem de ativação:

1. Exclua SalesAdmins e SalesforceContingency a política de conformidade do dispositivo existente para o Salesforce. Certifique-se de que um utilizador no grupo de SalesforceContingency pode aceder a Salesforce.
2. Ative política de 1: Certifique-se de que os utilizadores fora da SalesContingency não é possível aceder a Salesforce. Verifique se os utilizadores a SalesAdmins e SalesforceContingency podem aceder ao Salesforce.
3. Ative política de 2: Certifique-se de que os utilizadores no grupo de SalesContingency não é possível aceder a Salesforce a partir de seus laptops de Windows/Mac, mas ainda podem aceder a partir dos respetivos dispositivos móveis. Certifique-se de que os SalesAdmin ainda pode aceder Salesforce a partir de qualquer dispositivo.
4. Desative a política de conformidade do dispositivo existente para o Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Implementar sincronização de hash de palavra-passe, mesmo que está federados ou utiliza a autenticação pass-through

Bloqueio de utilizador também pode ocorrer se as condições seguintes forem verdadeiras:

- Sua organização utiliza uma solução de identidade híbrida com a autenticação pass-through ou Federação.
- Os sistemas de identidade no local (por exemplo, Active Directory, AD FS ou um componente dependente) não estão disponíveis. 
 
Para ser mais resiliente, sua organização deve [ativar a sincronização de hash de palavra-passe](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), porque permite-lhe [passar a utilizar a sincronização de hash de palavra-passe](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) se seus sistemas de identidade no local estão indisponíveis.

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft
 Ative a sincronização de hash de palavra-passe utilizando o Assistente do Azure AD Connect, independentemente se a sua organização utiliza a autenticação de Federação ou pass-through.

>[!IMPORTANT]
> Não é necessário para converter os utilizadores de federado para a autenticação gerida para utilizar a sincronização de hash de palavra-passe.

## <a name="during-a-disruption"></a>Durante uma interrupção

Se tiver optado para implementar um plano de atenuação, será capaz de sobreviver automaticamente uma interrupção de controlo de acesso único. No entanto, se tiver optado por criar um plano de contingência, será possível ativar as políticas de contingência durante a interrupção de controlo de acesso:

1. Ative as políticas de contingência que concedem utilizadores direcionados, acesso a aplicações específicas, a partir de redes específicas.
2. Desative as políticas com base no controle regulares.

### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Dependendo de qual atenuações ou contingências são utilizadas durante uma interrupção, a sua organização pode conceder acesso com palavras-passe apenas. Não existem salvaguarda é um risco de segurança considerável, que deve ser considerado cuidadosamente. Tem das organizações:

1. Como parte da sua estratégia de controlo de alteração, documente todas as alterações e o estado anterior para poder reverter qualquer contingências implementado, assim como os controlos de acesso são totalmente operacionais.
2. Partem do princípio de que os atores maliciosos irão tentar recolher as palavras-passe através de ataques de spray ou phishing de palavra-passe enquanto tiver desativado a MFA. Além disso, maus intervenientes já poderá ter palavras-passe que anteriormente não concedeu acesso a qualquer recurso que pode ser tentado durante esta janela. Para obter utilizadores críticos, tais como executivos, parcialmente capazes de atenuar esse risco ao repor as palavras-passe antes de desativar a MFA para os mesmos.
3. Arquive atividade todos os início de sessão para identificar quem aceder o que, durante o período de tempo que MFA foi desativada.
4. [Triagem de todos os eventos de risco comunicados](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) durante esta janela.

## <a name="after-a-disruption"></a>Após uma interrupção

Anule as alterações efetuadas como parte do plano de contingência ativado assim que o serviço é restaurado, responsável pela interrupção. 

1. Ativar as políticas regulares
2. Desative as políticas de contingência. 
3. Reverta as alterações feitas e documentadas durante a interrupção.
4. Se utilizou uma conta de acesso de emergência, não se esqueça de voltar a gerar as credenciais e proteger fisicamente os novos detalhes de credenciais como parte de seus procedimentos de conta de acesso de emergência.
5. Continuar a [triagem todos os eventos de risco comunicados](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) após a interrupção de atividade suspeita.
6. Revogar todos os tokens de atualização que foram emitidos [com o PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) para um conjunto de utilizadores de destino. Revogar todos os tokens de atualização é importante para contas com privilégios utilizadas durante as interrupções e fazê-lo irá forçá-los para autenticar e cumprir o controle das políticas restauradas.

## <a name="emergency-options"></a>Opções de emergência

 Em caso de emergência e a sua organização anteriormente não implementar uma atenuação ou o plano de contingência, em seguida, siga as recomendações na [contingências para bloqueio de utilizador](#contingencies-for-user-lockout) secção se já utilizar o acesso condicional políticas para impor a MFA.
Se a sua organização utilizar políticas de legado de MFA por utilizador, em seguida, pode considerar a alternativa seguintes:

1. Se tiver o endereço IP de saída de rede empresarial, pode adicioná-los como IPs fidedignos para ativar a autenticação apenas à rede empresarial.
   1. Se não tiver o inventário de endereços IP de saída ou -lhe pedido para ativar o acesso dentro e fora da rede empresarial, pode adicionar todo o espaço de endereços de IPv4 como IPs fidedignos, especificando 0.0.0.0/1 e 128.0.0.0/1.

>[!IMPORTANT]
 > Se ampliar os endereços IP fidedignos para desbloquear o acesso ao, eventos de risco associados com endereços IP (por exemplo, deslocação impossível ou localizações desconhecidas) não serão gerados.

>[!NOTE]
 > Configurando [IPs fidedignos](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) para a MFA do Azure só está disponível com [licenças do Azure AD Premium](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>Saiba mais

* [Documentação de autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Gerir o acesso de emergência de contas administrativas no Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Configurar localizações com nome no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Como configurar híbrida do Azure Active Directory associados a um dispositivos](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Guia de Implementação do Windows Hello para Empresas](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Documentação de orientação de palavra-passe - Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Quais são condições no acesso condicional do Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Quais são os controles de acesso no acesso condicional do Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
