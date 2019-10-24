---
title: Privileged Identity Management de implantação (PIM) – Azure Active Directory | Microsoft Docs
description: Descreve como planejar a implantação do Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a1263d494ff26ff6ab87d39bc864271f14457b3
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756271"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management de implantação (PIM)

Este guia passo a passo descreve como planejar a implantação do Privileged Identity Management (PIM) em sua organização do Azure Active Directory (AD do Azure).

> [!TIP]
> Ao longo deste artigo, você verá itens marcados como:
> 
> : heavy_check_mark: a **Microsoft recomenda**
> 
> Essas são recomendações gerais e você só deve implementar se elas se aplicarem às suas necessidades empresariais específicas.

## <a name="learn-about-privileged-identity-management"></a>Saiba mais sobre o Privileged Identity Management

Azure AD Privileged Identity Management ajuda a gerenciar funções administrativas privilegiadas no Azure AD, recursos do Azure e outros serviços online da Microsoft. Em um mundo em que identidades com privilégios são atribuídas e esquecidas, o Privileged Identity Management fornece soluções como acesso just-in-time, fluxos de trabalho de aprovação de solicitação e revisões de acesso totalmente integradas para que você possa identificar, descobrir e impedir mal-intencionado atividades de funções com privilégios em tempo real. A implantação de Privileged Identity Management para gerenciar suas funções privilegiadas em toda a organização reduzirá consideravelmente o risco, ao mesmo tempo que identificando informações valiosas sobre as atividades de suas funções privilegiadas.

### <a name="business-value-of-privileged-identity-management"></a>Valor comercial de Privileged Identity Management

**Gerenciar riscos** – proteja sua organização, impondo o princípio de acesso de [privilégios mínimos](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e acesso just-in-time. Ao minimizar o número de atribuições permanentes de usuários a funções privilegiadas e impor aprovações e MFA para elevação, você pode reduzir significativamente os riscos de segurança relacionados ao acesso privilegiado em sua organização. A imposição de privilégios mínimos e o acesso just-in-time também permitirá que você exiba um histórico de acesso a funções privilegiadas e rastreie problemas de segurança à medida que eles acontecem.

**Atender à conformidade e governança** -a implantação de Privileged Identity Management cria um ambiente para governança de identidade contínua. A elevação just-in-time de identidades com privilégios fornece uma maneira para Privileged Identity Management manter o controle das atividades de acesso privilegiado em sua organização. Você também poderá exibir e receber notificações para todas as atribuições de funções permanentes e qualificadas dentro de sua organização. Por meio da revisão de acesso, você pode auditar e remover regularmente identidades com privilégios desnecessárias e garantir que sua organização esteja em conformidade com os padrões de identidade, acesso e segurança mais rigorosos.

**Reduza** custos-reduza os custos eliminando ineficiências, erros humanos e problemas de segurança implantando Privileged Identity Management corretamente. O resultado líquido é uma redução de crimes virtuais associados a identidades com privilégios, que são dispendiosas e difíceis de se recuperar. Privileged Identity Management também ajudará a sua organização a reduzir o custo associado à auditoria de informações de acesso quando se trata de cumprir normas e padrões.

Para obter mais informações, consulte [o que é Azure ad Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Requisitos de licenciamento

Para usar Privileged Identity Management, seu diretório deve ter uma das seguintes licenças pagas ou de avaliação:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) e5
- Microsoft 365 M5

Para obter mais informações, consulte [requisitos de licença para usar Privileged Identity Management](subscription-requirements.md).

### <a name="key-terminology"></a>Terminologia principal

| Termo ou conceito | Descrição |
| --- | --- |
| direito | Uma atribuição de função que exige que um usuário execute uma ou mais ações para usar a função. Se um usuário se tornou qualificado para uma função, isso significa que eles podem ativar a função quando precisam executar tarefas privilegiadas. Não há nenhuma diferença no acesso dado a alguém com uma atribuição de funções qualificada e permanente. A única diferença é que algumas pessoas não precisam desse acesso o tempo todo. |
| ativar | O processo de executar uma ou mais ações para usar uma função à qual um usuário está qualificado. As ações podem incluir a execução de uma verificação de MFA (autenticação multifator), o fornecimento de uma justificativa de negócios ou a solicitação de aprovação de aprovadores designados. |
| acesso just-in-time (JIT) | Um modelo no qual os usuários recebem permissões temporárias para executar tarefas privilegiadas, o que impede que usuários mal-intencionados ou não autorizados obtenham acesso depois que as permissões tiverem expirado. O acesso é concedido somente quando os usuários precisam dele. |
| princípio de acesso de privilégios mínimos | Uma prática de segurança recomendada na qual cada usuário é fornecido apenas com os privilégios mínimos necessários para realizar as tarefas que eles estão autorizados a executar. Essa prática minimiza o número de administradores globais e, em vez disso, usa funções de administrador específicas para determinados cenários. |

Para obter mais informações, consulte [terminologia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Visão geral de alto nível de como o Privileged Identity Management funciona

1. Privileged Identity Management é configurado para que os usuários estejam qualificados para funções privilegiadas.
1. Quando um usuário elegível precisa usar sua função privilegiada, ele ativa a função no Privileged Identity Management.
1. Dependendo das configurações de Privileged Identity Management configuradas para a função, o usuário deve concluir determinadas etapas (como executar a autenticação multifator, obter aprovação ou especificar um motivo).
1. Depois que o usuário ativar a função com êxito, ele receberá a função por um período de tempo pré-configurado.
1. Os administradores podem exibir um histórico de todas as atividades de Privileged Identity Management no log de auditoria. Eles também podem proteger ainda mais suas organizações do Azure AD e cumprir a conformidade usando Privileged Identity Management recursos como revisões de acesso e alertas.

Para obter mais informações, consulte [o que é Azure ad Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Funções que podem ser gerenciadas pelo Privileged Identity Management

**Funções do Azure ad** – essas funções estão todas em Azure Active Directory (como administrador global, administrador do Exchange e administrador de segurança). Você pode ler mais sobre as funções e sua funcionalidade nas [permissões de função de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Para obter ajuda para determinar quais funções atribuir a seus administradores, consulte [funções menos privilegiadas por tarefa](../users-groups-roles/roles-delegate-by-task.md).

**Funções de recurso do Azure** – essas funções são vinculadas a um recurso do Azure, grupo de recursos, assinatura ou grupo de gerenciamento. O Privileged Identity Management fornece acesso just-in-time a funções internas, como proprietário, administrador de acesso do usuário e colaborador, bem como [funções personalizadas](../../role-based-access-control/custom-roles.md). Para obter mais informações sobre as funções de recurso do Azure, consulte [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md).

Para obter mais informações, consulte [funções que você não pode gerenciar em Privileged Identity Management](pim-roles.md).

## <a name="plan-your-deployment"></a>Planeie a sua implementação

Esta seção se concentra no que você precisa fazer antes de implantar Privileged Identity Management em sua organização. É essencial seguir as instruções e entender os conceitos desta seção, pois elas vão orientá-lo a criar o melhor plano adaptado para as identidades privilegiadas de sua organização.

### <a name="identify-your-stakeholders"></a>Identificar seus participantes

A seção a seguir ajuda a identificar todos os participantes que estão envolvidos no projeto e precisam desativá-los, revisá-los ou se manter informados. Ele inclui tabelas separadas para a implantação de Privileged Identity Management para funções do Azure AD e Privileged Identity Management para funções de recurso do Azure. Adicione os participantes à tabela a seguir, conforme apropriado para sua organização.

- Portanto = fazer logoff neste projeto
- R = examinar este projeto e fornecer entrada
- I = informado deste projeto

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Participantes: Privileged Identity Management para funções do Azure AD

| Nome | Função | Ação |
| --- | --- | --- |
| Nome e email | **Arquiteto de identidade ou administrador global do Azure**<br/>Um representante da equipe de gerenciamento de identidades responsável por definir como essa alteração é alinhada com a principal infra-estrutura de gerenciamento de identidade em sua organização. | PORTANTO/R/I |
| Nome e email | **Proprietário do serviço/Gerenciador de linha**<br/>Um representante dos proprietários de ti de um serviço ou de um grupo de serviços. Eles são fundamentais para tomar decisões e ajudar a distribuir Privileged Identity Management para sua equipe. | PORTANTO/R/I |
| Nome e email | **Proprietário da segurança**<br/>Um representante da equipe de segurança que pode se desconectar que o plano atende aos requisitos de segurança de sua organização. | PORTANTO/R |
| Nome e email | **Gerente de suporte de ti/assistência técnica**<br/>Um representante da organização de suporte de ti que pode fornecer informações sobre a compatibilidade dessa mudança de uma perspectiva de assistência técnica. | R/I |
| Nome e email para usuários piloto | **Usuários com privilégios de função**<br/>O grupo de usuários para o qual o Privileged Identity Management é implementado. Eles precisarão saber como ativar suas funções quando Privileged Identity Management for implementado. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Participantes: Privileged Identity Management para funções de recurso do Azure

| Nome | Função | Ação |
| --- | --- | --- |
| Nome e email | **Proprietário da assinatura/recurso**<br/>Um representante dos proprietários de ti de cada assinatura ou recurso que você deseja implantar Privileged Identity Management | PORTANTO/R/I |
| Nome e email | **Proprietário da segurança**<br/>Um representante da equipe de segurança que pode se desconectar que o plano atende aos requisitos de segurança de sua organização. | PORTANTO/R |
| Nome e email | **Gerente de suporte de ti/assistência técnica**<br/>Um representante da organização de suporte de ti que pode fornecer informações sobre a compatibilidade dessa mudança de uma perspectiva de assistência técnica. | R/I |
| Nome e email para usuários piloto | **Usuários da função RBAC**<br/>O grupo de usuários para o qual o Privileged Identity Management é implementado. Eles precisarão saber como ativar suas funções quando Privileged Identity Management for implementado. | I |

### <a name="enable-privileged-identity-management"></a>Habilitar Privileged Identity Management

Como parte do processo de planejamento, primeiro você deve consentir e habilitar Privileged Identity Management seguindo nosso artigo [começar a usar Privileged Identity Management](pim-getting-started.md) . A habilitação de Privileged Identity Management fornece acesso a alguns recursos projetados especificamente para ajudar na implantação.

Se seu objetivo for implantar Privileged Identity Management para recursos do Azure, siga nosso artigo [descobrir recursos do Azure para gerenciar no Privileged Identity Management](pim-resource-roles-discover-resources.md) . Somente os proprietários de cada recurso, grupo de recursos e assinatura poderão descobri-los dentro do Privileged Identity Management. Se você for um administrador global tentando implantar Privileged Identity Management para seus recursos do Azure, você pode [elevar o acesso para gerenciar todas as assinaturas do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para conceder acesso a todos os recursos do Azure no diretório para descoberta. No entanto, aconselhamos que você obtenha aprovação de cada um dos seus proprietários de assinatura antes de gerenciar seus recursos com o Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Impor o princípio de privilégios mínimos

É importante certificar-se de que você impô o princípio de privilégios mínimos em sua organização para o Azure AD e suas funções de recurso do Azure.

#### <a name="azure-ad-roles"></a>Funções do Azure AD

Para funções do Azure AD, é comum que as organizações atribuam a função de administrador global a um número significativo de administradores quando a maioria dos administradores precisar apenas de uma ou duas funções de administrador específicas. Atribuições de função com privilégios também tendem a ser deixados não gerenciados.

> [!NOTE]
> Armadilhas comuns na delegação de função:
>
> - O administrador responsável pelo Exchange recebe a função de administrador global, mesmo que precise apenas da função de administrador do Exchange para executar o trabalho diário.
> - A função de administrador global é atribuída a um administrador do Office porque o administrador precisa de funções de administrador de segurança e do SharePoint e é mais fácil apenas delegar uma função.
> - O administrador foi atribuído a uma função de administrador de segurança para executar uma tarefa há muito tempo, mas nunca foi removido.

Siga estas etapas para impor o princípio de privilégios mínimos para suas funções do Azure AD.

1. Entenda a granularidade das funções lendo e compreendendo as funções de [administrador do Azure ad disponíveis](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Você e sua equipe também devem referenciar [funções de administrador por tarefa de identidade no Azure ad](../users-groups-roles/roles-delegate-by-task.md), o que explica a função de privilégios mínimos para tarefas específicas.

1. Liste quem tem funções com privilégios em sua organização. Você pode usar o [Assistente de Privileged Identity Management](pim-security-wizard.md#run-the-wizard) para acessar uma página semelhante à seguinte.

    ![Painel descobrir funções privilegiadas mostrando quem tem funções com privilégios](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Para todos os administradores globais em sua organização, descubra por que eles precisam da função. Com base na leitura da documentação anterior, se o trabalho da pessoa puder ser executado por uma ou mais funções de administrador granulares, você deverá removê-los da função de administrador global e fazer atribuições adequadamente dentro de Azure Active Directory (como referência: No momento, a Microsoft tem apenas 10 administradores com a função de administrador global. Saiba mais em [como a Microsoft usa o Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. Para todas as outras funções do Azure AD, examine a lista de atribuições, identifique os administradores que não precisam mais da função e remova-os de suas atribuições.

Para automatizar as duas últimas etapas, você pode usar as revisões de acesso no Privileged Identity Management. Seguindo as etapas em [iniciar uma revisão de acesso para funções do Azure AD no Privileged Identity Management](pim-how-to-start-security-review.md), você pode configurar uma revisão de acesso para cada função do Azure AD que tenha um ou mais membros.

![Criar um painel de revisão de acesso para funções do Azure AD](./media/pim-deployment-plan/create-access-review.png)

Você deve definir os revisores como **Membros (próprio)** . Isso enviará um email para todos os membros na função para confirmar se eles precisam do acesso. Você também deve ativar o **motivo necessário na aprovação** nas configurações avançadas para que os usuários possam declarar por que precisam da função. Com base nessas informações, você poderá remover usuários de funções desnecessárias e delegar funções de administrador mais granulares no caso de administradores globais.

As revisões de acesso dependem de emails para notificar as pessoas para revisar o acesso às funções. Se você tiver contas com privilégios que não têm emails vinculados, não se esqueça de preencher o campo email secundário nessas contas. Para obter mais informações, consulte o [atributo proxyAddresses no Azure ad](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Funções de recursos do Azure

Para assinaturas e recursos do Azure, você pode configurar um processo de revisão de acesso semelhante para examinar as funções em cada assinatura ou recurso. O objetivo desse processo é minimizar as atribuições de administrador de acesso de usuário e proprietário anexadas a cada assinatura ou recurso, bem como para remover atribuições desnecessárias. No entanto, as organizações geralmente delegam tarefas para o proprietário de cada assinatura ou recurso, pois eles têm uma melhor compreensão das funções específicas (especialmente funções personalizadas).

Se você for um administrador de ti com a função de administrador global tentando implantar Privileged Identity Management para recursos do Azure em sua organização, poderá [elevar o acesso para gerenciar todas as assinaturas do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para obter acesso a cada assinatura. Em seguida, você pode encontrar cada proprietário de assinatura e trabalhar com eles para remover atribuições desnecessárias e minimizar a atribuição de função de proprietário.

Os usuários com a função de proprietário de uma assinatura do Azure também podem utilizar [revisões de acesso para recursos do Azure](pim-resource-roles-start-access-review.md) para auditar e remover atribuições de função desnecessárias semelhantes ao processo descrito anteriormente para funções do Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Decida quais atribuições de função devem ser protegidas por Privileged Identity Management

Depois de limpar as atribuições de função com privilégios em sua organização, você precisará decidir quais funções serão protegidas com Privileged Identity Management.

Se uma função for protegida por Privileged Identity Management, os usuários qualificados atribuídos a ela deverão elevar para usar os privilégios concedidos pela função. O processo de elevação também pode incluir a obtenção de aprovação, a execução da autenticação multifator e/ou o fornecimento de um motivo pelo qual eles estão sendo ativados. Privileged Identity Management também pode controlar as elevações por meio de notificações e os logs de eventos de auditoria do Privileged Identity Management e do Azure AD.

Escolher quais funções proteger com Privileged Identity Management pode ser difícil e será diferente para cada organização. Esta seção fornece nosso Conselho de práticas recomendadas para Azure AD e funções de recurso do Azure.

#### <a name="azure-ad-roles"></a>Funções do Azure AD

É importante priorizar a proteção de funções do Azure AD que têm o maior número de permissões. Com base nos padrões de uso entre todos os clientes Privileged Identity Management, as 10 principais funções do Azure AD gerenciadas pelo Privileged Identity Management são:

1. Administrador global
1. Administrador de segurança
1. Administrador do usuário
1. Administrador do Exchange
1. Administrador do SharePoint
1. Administrador do Intune
1. Leitor de segurança
1. Administrador de serviço
1. Administrador de faturação
1. Administrador do Skype for Business

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você gerencie todos os seus administradores globais e administradores de segurança usando Privileged Identity Management como uma primeira etapa, pois eles são aqueles que podem fazer mais danos quando comprometidos.

É importante considerar quais dados e permissões são mais confidenciais para sua organização. Por exemplo, algumas organizações podem querer proteger sua função de administrador de Power BI ou sua função de administrador de equipes usando Privileged Identity Management, pois têm a capacidade de acessar dados e/ou alterar fluxos de trabalho principais.

Se houver funções com usuários convidados atribuídos, elas serão particularmente vulneráveis a ataques.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você gerencie todas as funções com usuários convidados usando Privileged Identity Management para reduzir o risco associado a contas de usuário convidado comprometidas.

As funções de leitor, como o leitor de diretório, o leitor do centro de mensagens e o leitor de segurança, às vezes são consideradas menos importantes em comparação com outras funções, pois não têm permissão de gravação. No entanto, vimos que alguns clientes também protegem essas funções porque os invasores que obtiveram acesso a essas contas podem ser capazes de ler dados confidenciais, como PII (informações de identificação pessoal). Você deve levar isso em consideração ao decidir se as funções de leitor em sua organização precisam ser gerenciadas usando Privileged Identity Management.

#### <a name="azure-resource-roles"></a>Funções de recursos do Azure

Ao decidir quais atribuições de função devem ser gerenciadas usando Privileged Identity Management para o recurso do Azure, você deve primeiro identificar as assinaturas/recursos que são mais vitais para sua organização. Exemplos de tais assinaturas/recursos são:

- Recursos que hospedam os dados mais confidenciais
- Recursos que os aplicativos principais e voltados para o cliente dependem

Se você for um administrador global com problemas para decidir quais assinaturas/recursos são mais importantes, você deve entrar em contato com os proprietários da assinatura em sua organização para reunir uma lista de recursos gerenciados por cada assinatura. Em seguida, você deve trabalhar com os proprietários da assinatura para agrupar os recursos com base no nível de severidade, caso eles sejam comprometidos (baixo, médio, alto). Você deve priorizar o gerenciamento de recursos com Privileged Identity Management com base nesse nível de severidade.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você trabalhe com proprietários de recursos/assinatura de serviços críticos para configurar Privileged Identity Management fluxo de trabalho para todas as funções dentro de assinaturas/recursos confidenciais.

Privileged Identity Management para recursos do Azure dá suporte a contas de serviço com limite de tempo. Você deve tratar as contas de serviço exatamente da mesma forma como trataria uma conta de usuário normal.

Para assinaturas/recursos que não são tão críticos, você não precisará configurar Privileged Identity Management para todas as funções. No entanto, você ainda deve proteger as funções proprietário e administrador de acesso do usuário com Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você gerencie funções de proprietário e funções de administrador de acesso de usuário de todas as assinaturas/recursos usando Privileged Identity Management.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Decidir quais atribuições de função devem ser permanentes ou qualificadas

Depois de decidir a lista de funções a serem gerenciadas pelo Privileged Identity Management, você deve decidir quais usuários devem obter a função qualificada versus a função ativa permanentemente. Funções ativas permanentemente são as funções normais atribuídas por meio de Azure Active Directory e recursos do Azure, enquanto as funções qualificadas só podem ser atribuídas em Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você tenha zero atribuições permanentemente ativas para funções do Azure AD e funções de recurso do Azure além das [duas contas de acesso de emergência de interrupção](../users-groups-roles/directory-emergency-access.md)recomendadas, que devem ter a Função de administrador global.

Embora seja recomendável nenhum administrador em pé, às vezes é difícil para as organizações alcançarem isso imediatamente. Aqui estão as coisas a serem consideradas ao tomar essa decisão:

- Frequência de elevação – se o usuário precisar apenas da atribuição privilegiada uma vez, eles não deverão ter a atribuição permanente. Por outro lado, se o usuário precisar da função de seu trabalho diário e o uso de Privileged Identity Management reduziria muito sua produtividade, eles poderão ser considerados para a função permanente.
- Casos específicos para sua organização – se a pessoa que está sendo dada a função qualificada for de uma equipe muito distante ou um executivo de alta classificação até o ponto de comunicação e imposição do processo de elevação for difícil, eles poderão ser considerados para a função permanente.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você configure revisões de acesso recorrente para usuários com atribuições de função permanentes (caso você tenha algum). Saiba mais sobre a revisão de acesso recorrente na seção final deste plano de implantação

### <a name="draft-your-privileged-identity-management-settings"></a>Rascunhar suas configurações de Privileged Identity Management

Antes de implementar sua solução de Privileged Identity Management, é uma prática recomendada rascunhar suas configurações de Privileged Identity Management para cada função privilegiada que sua organização usa. Esta seção tem alguns exemplos de configurações de Privileged Identity Management para funções específicas (elas são apenas para referência e podem ser diferentes para sua organização). Cada uma dessas configurações é explicada em detalhes com as recomendações da Microsoft após as tabelas.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Configurações de Privileged Identity Management para funções do Azure AD

| Função | Requerer MFA | Notificação | Tíquete de incidente | Exigir aprovação | Aprovador | Duração da ativação | Administrador permanente |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrador Global | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros administradores globais | Uma Hora | Contas de acesso de emergência |
| Administrador do Exchange | :heavy_check_mark: | :heavy_check_mark: | w.x.y. | w.x.y. | Nenhuma | 2 horas | Nenhuma |
| Administrador de assistência técnica | w.x.y. | w.x.y. | :heavy_check_mark: | w.x.y. | Nenhuma | 8 horas | Nenhuma |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Configurações de Privileged Identity Management para funções de recurso do Azure

| Função | Requerer MFA | Notificação | Exigir aprovação | Aprovador | Duração da ativação | Administrador ativo | Expiração ativa | Expiração qualificada |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Proprietário de assinaturas críticas | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros proprietários da assinatura | Uma Hora | Nenhuma | n/d | 3 meses |
| Administrador de acesso do usuário de assinaturas menos críticas | :heavy_check_mark: | :heavy_check_mark: | w.x.y. | Nenhuma | Uma Hora | Nenhuma | n/d | 3 meses |
| Colaborador da máquina virtual | w.x.y. | :heavy_check_mark: | w.x.y. | Nenhuma | 3 horas | Nenhuma | n/d | 6 meses |

A tabela a seguir descreve cada uma das configurações.

| Definição | Descrição |
| --- | --- |
| Função | Nome da função para a qual você está definindo as configurações. |
| Requerer MFA | Se o usuário qualificado precisa executar a MFA antes de ativar a função.<br/><br/> : heavy_check_mark: a **Microsoft recomenda** que você aplique a MFA para todas as funções de administrador, especialmente se as funções tiverem usuários convidados. |
| Notificação | Se definido como verdadeiro, administrador global, administrador de função com privilégios e administrador de segurança na organização receberá uma notificação por email quando um usuário elegível ativar a função.<br/><br/>**Observação:** Algumas organizações não têm um endereço de email vinculado a suas contas de administrador, para obter essas notificações por email, você deve definir um endereço de email alternativo para que os administradores recebam esses emails. |
| Tíquete de incidente | Se o usuário qualificado precisa registrar um número de tíquete de incidente ao ativar sua função. Essa configuração ajuda uma organização a identificar cada ativação com um número de incidente interno para atenuar as ativações indesejadas.<br/><br/> : heavy_check_mark: a **Microsoft recomenda** aproveitar os números de tíquetes de incidentes para vincular Privileged Identity Management ao seu sistema interno. Isso é particularmente útil para Aprovadores que precisam de contexto para a ativação. |
| Exigir aprovação | Se o usuário elegível precisa obter aprovação para ativar a função.<br/><br/> : heavy_check_mark: a **Microsoft recomenda** que você configure a aprovação para funções com a maior permissão. Com base nos padrões de uso de todos os Privileged Identity Management clientes, administrador global, administrador de usuário, administrador do Exchange, administrador de segurança e administrador de senha são as funções mais comuns com a configuração de aprovação. |
| Aprovador | Se a aprovação for necessária para ativar a função qualificada, liste as pessoas que devem aprovar a solicitação. Por padrão, Privileged Identity Management define o aprovador para ser todos os usuários que são um administrador de função com privilégios, sejam eles permanentes ou qualificados.<br/><br/>**Observação:** Se um usuário estiver qualificado para uma função do Azure AD e um Aprovador da função, ele não poderá se aprovar.<br/><br/> : heavy_check_mark: a **Microsoft recomenda** que você escolha aprovadores para serem aqueles que são mais especializados sobre a função específica e seus usuários frequentes, em vez de um administrador global. |
| Duração da ativação | O período de tempo que um usuário será ativado na função antes de expirar. |
| Administrador permanente | Lista de usuários que serão um administrador permanente para a função (nunca precisará ser ativada).<br/><br/> : heavy_check_mark: a **Microsoft recomenda** que você tenha zero administrador em todas as funções, exceto os administradores globais. Leia mais sobre isso na seção quem deve se tornar qualificado e quem deve estar permanentemente ativo deste plano. |
| Administrador ativo | Para recursos do Azure, o administrador ativo é a lista de usuários que nunca precisarão ser ativados para usar a função. Isso não é conhecido como administrador permanente, como nas funções do Azure AD, porque você pode definir um tempo de expiração para quando o usuário perder essa função. |
| Expiração ativa | Uma atribuição de função ativa para funções de recurso do Azure expira após esse período de tempo configurado. Você pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou permanentemente ativo. |
| Expiração qualificada | Uma atribuição de função qualificada para funções de recurso do Azure expira após esse período de tempo configurado. Você pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou ser permanentemente qualificado. |

## <a name="implement-your-solution"></a>Implementar sua solução

A base do planejamento adequado é a base na qual você pode implantar um aplicativo com êxito com Azure Active Directory.  Ele fornece segurança e integração inteligentes que simplificam o uso e reduzem o tempo para implantações bem-sucedidas.  Essa combinação garante que seu aplicativo seja integrado com facilidade e, ao mesmo tempo, reduza a hora para os usuários finais.

### <a name="identify-test-users"></a>Identificar usuários de teste

Use esta seção para identificar um conjunto de usuários e grupos de usuários para validar a implementação. Com base nas configurações que você selecionou na seção planejamento, identifique os usuários que você deseja testar para cada função.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você faça com que os proprietários de serviço de cada função do Azure ad sejam os usuários de teste para que possam se familiarizar com o processo e se tornar um defensor interno para a distribuição.

Nesta tabela, identifique os usuários de teste que irão verificar se as configurações de cada função estão funcionando.

| Nome da função | Usuários de teste |
| --- | --- |
| nome do &lt;Role &gt; | &lt;Users testar a função &gt; |
| nome do &lt;Role &gt; | &lt;Users testar a função &gt; |

### <a name="test-implementation"></a>Implementação de teste

Agora que você identificou os usuários de teste, use esta etapa para configurar Privileged Identity Management para os usuários de teste. Se sua organização deseja incorporar Privileged Identity Management fluxo de trabalho em seu próprio aplicativo interno, em vez de usar Privileged Identity Management na portal do Azure, todas as operações no Privileged Identity Management também têm suporte por meio de nossa [API do Graph](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Configurar Privileged Identity Management para funções do Azure AD

1. [Defina as configurações de função do Azure ad](pim-how-to-change-default-settings.md) com base no que você planejou.

1. Navegue até **funções do Azure ad**, clique em **funções**e selecione a função que você acabou de configurar.

1. Para o grupo de usuários de teste, se eles já forem um administrador permanente, você poderá torná-los qualificados pesquisando-os e convertendo-os de permanente para qualificado clicando nos três pontos em sua linha. Se eles ainda não tiverem as atribuições de função, você poderá [criar uma nova atribuição qualificada](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Repita as etapas de 1-3 para todas as funções que você deseja testar.

1. Depois de configurar os usuários de teste, você deve enviar a eles o link para saber como [ativar sua função do Azure ad](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Configurar Privileged Identity Management para funções de recurso do Azure

1. [Defina as configurações de função de recurso do Azure](pim-resource-roles-configure-role-settings.md) para uma função dentro de uma assinatura ou recurso que você deseja testar.

1. Navegue até **recursos do Azure** para essa assinatura e clique em **funções**, selecione a função que você acabou de configurar.

1. Para o grupo de usuários de teste, se eles já forem um administrador ativo, você poderá torná-los qualificados pesquisando-os e [atualizando sua atribuição de função](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Se eles ainda não tiverem a função, você poderá [atribuir uma nova função](pim-resource-roles-assign-roles.md#assign-a-role).

1. Repita as etapas de 1-3 para todas as funções que você deseja testar.

1. Depois de configurar os usuários de teste, você deve enviar a eles o link para saber como [ativar sua função de recurso do Azure](pim-resource-roles-activate-your-roles.md).

Você deve usar esse estágio para verificar se toda a configuração configurada para as funções está funcionando corretamente. Use a tabela a seguir para documentar seus testes. Você também deve usar esse estágio para otimizar a comunicação com os usuários afetados.

| Função | Comportamento esperado durante a ativação | Resultados reais |
| --- | --- | --- |
| Administrador Global | (1) exigir MFA<br/>(2) exigir aprovação<br/>(3) o aprovador recebe a notificação e pode aprovar<br/>(4) a função expira após o tempo predefinido |  |
| Proprietário da assinatura *X* | (1) exigir MFA<br/>(2) a atribuição qualificada expira após o período de tempo configurado |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Comunicar Privileged Identity Management com os participantes afetados

A implantação de Privileged Identity Management apresentará etapas adicionais para usuários de funções com privilégios. Embora Privileged Identity Management reduza significativamente os problemas de segurança associados a identidades com privilégios, a alteração precisa ser efetivamente comunicada antes da implantação em todo o locatário. Dependendo do número de administradores afetados, as organizações geralmente optam por criar um documento interno, um vídeo ou um email sobre a alteração. Com frequência, são incluídas nessas comunicações:

- O que é o PIM
- Qual é o benefício para a organização
- Quem será afetado
- Quando o PIM será distribuído
- Quais etapas adicionais serão necessárias para que os usuários ativem sua função
    - Você deve enviar links para a nossa documentação:
    - [Ativar funções do Azure AD](pim-how-to-activate-role.md)
    - [Ativar funções de recurso do Azure](pim-resource-roles-activate-your-roles.md)
- Link de informações de contato ou assistência técnica para quaisquer problemas associados ao PIM

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você configure o tempo com sua equipe de suporte/assistência técnica para orientá-las no fluxo de trabalho de Privileged Identity Management (se sua organização tiver uma equipe de suporte de ti interna). Forneça a eles as documentações apropriadas, bem como suas informações de contato.

### <a name="move-to-production"></a>Mover para produção

Depois que o teste for concluído e bem-sucedido, mova Privileged Identity Management para produção repetindo todas as etapas nas fases de teste para todos os usuários de cada função que você definiu em sua configuração de Privileged Identity Management. Para Privileged Identity Management para funções do Azure AD, as organizações geralmente testam e distribuem Privileged Identity Management para administradores globais antes de testar e distribuir Privileged Identity Management para outras funções. Enquanto isso, para o recurso do Azure, as organizações normalmente testam e distribuem Privileged Identity Management uma assinatura do Azure por vez.

### <a name="in-the-case-a-rollback-is-needed"></a>No caso, é necessária uma reversão

Se Privileged Identity Management não funcionar conforme desejado no ambiente de produção, as etapas de reversão a seguir podem ajudá-lo a reverter para um estado válido conhecido antes de configurar Privileged Identity Management:

#### <a name="azure-ad-roles"></a>Funções do Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Abra **Azure ad Privileged Identity Management**.
1. Clique em **funções do Azure ad** e clique em **funções**.
1. Para cada função configurada, clique nas reticências ( **...** ) para todos os usuários com uma atribuição qualificada.
1. Clique na opção **tornar permanente** para tornar a atribuição de função permanente.

#### <a name="azure-resource-roles"></a>Funções de recursos do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Abra **Azure ad Privileged Identity Management**.
1. Clique em **recursos do Azure** e, em seguida, clique em uma assinatura ou um recurso que você deseja reverter.
1. Clique em **funções**.
1. Para cada função configurada, clique nas reticências ( **...** ) para todos os usuários com uma atribuição qualificada.
1. Clique na opção **tornar permanente** para tornar a atribuição de função permanente.

## <a name="next-steps-after-deploying"></a>Próximas etapas após a implantação

A implantação bem-sucedida de Privileged Identity Management em produção é um avanço significativo em termos de proteção das identidades privilegiadas de sua organização. Com a implantação do Privileged Identity Management vem com recursos de Privileged Identity Management adicionais que você deve usar para segurança e conformidade.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Use Privileged Identity Management alertas para proteger seu acesso privilegiado

Você deve utilizar a funcionalidade interna de alerta do Privileged Identity Management para proteger melhor seu locatário. Para obter mais informações, consulte [alertas de segurança](pim-how-to-configure-security-alerts.md#security-alerts). Esses alertas incluem: os administradores não estão usando funções privilegiadas, as funções estão sendo atribuídas fora do Privileged Identity Management, as funções estão sendo ativadas com muita frequência e muito mais. Para proteger totalmente sua organização, você deve percorrer regularmente a lista de alertas e corrigir os problemas. Você pode exibir e corrigir os alertas da seguinte maneira:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Abra **Azure ad Privileged Identity Management**.
1. Clique em **funções do Azure ad** e clique em **alertas**.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você lide com todos os alertas marcados com alta gravidade imediatamente. Para alertas de severidade média e baixa, você deve se manter informado e fazer alterações se acreditar que há uma ameaça de segurança.

Se qualquer um dos alertas específicos não for útil ou não se aplicar à sua organização, você sempre poderá ignorar o alerta na página alertas. Você sempre pode reverter esse descarte posteriormente na página de configurações do Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Configurar revisões de acesso recorrentes para auditar regularmente as identidades privilegiadas de sua organização

As revisões de acesso são a melhor maneira de solicitar aos usuários atribuídos com funções privilegiadas ou revisores específicos se cada usuário precisar da identidade privilegiada. As revisões de acesso serão ótimas se você quiser reduzir a superfície de ataque e permanecer em conformidade. Para obter mais informações sobre como iniciar uma revisão de acesso, consulte revisões de acesso de [funções do Azure ad](pim-how-to-start-security-review.md) e [revisões de acesso de funções de recursos do Azure](pim-resource-roles-start-access-review.md). Para algumas organizações, é necessário realizar uma análise de acesso periódica para permanecer em conformidade com leis e regulamentos enquanto, para outros, a análise de acesso é a melhor maneira de impor a entidade de privilégio mínimo em toda a sua organização.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você configure as revisões de acesso trimestral para todas as suas funções de recurso do Azure AD e Azure.

Na maioria dos casos, o revisor para funções do Azure AD é o proprietário dos próprios usuários, enquanto o revisor das funções de recurso do Azure é a proprietária da assinatura, na qual a função está. No entanto, geralmente é o caso em que as empresas têm contas com privilégios que não estão vinculadas a endereço de email de qualquer pessoa específica. Nesses casos, ninguém lê e revisa o acesso.

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você adicione um endereço de email secundário para todas as contas com atribuições de função com privilégios que não estejam vinculadas a um endereço de email marcado regularmente

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Aproveite ao máximo seu log de auditoria para melhorar a segurança e a conformidade

O log de auditoria é o local onde você pode se manter atualizado e estar em conformidade com as regulamentações. O Privileged Identity Management atualmente armazena um histórico de 30 dias de todo o histórico de sua organização dentro de seu log de auditoria, incluindo:

- Ativação/desativação de funções qualificadas
- Atividades de atribuição de função dentro e fora do Privileged Identity Management
- Alterações nas configurações de função
- Solicitar/aprovar/negar atividades para ativação de função com a configuração de aprovação
- Atualizar para alertas

Você pode acessar esses logs de auditoria se for um administrador global ou um administrador de função com privilégios. Para obter mais informações, consulte [histórico de auditoria para funções do Azure ad](pim-how-to-use-audit-log.md) e [histórico de auditoria para funções de recurso do Azure](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que pelo menos um administrador Leia todos os eventos de auditoria semanalmente e exporte seus eventos de auditoria mensalmente.

Se você quiser armazenar automaticamente seus eventos de auditoria por um período de tempo maior, Privileged Identity Management log de auditoria será sincronizado automaticamente nos [logs de auditoria do Azure ad](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: a **Microsoft recomenda** que você configure o [monitoramento de log do Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) para arquivar eventos de auditoria em uma conta de armazenamento do Azure para a necessidade de segurança e conformidade.
