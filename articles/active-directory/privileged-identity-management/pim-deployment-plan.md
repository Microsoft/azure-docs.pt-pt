---
title: Implementar o Azure AD Privileged Identity Management (PIM) | Documentos da Microsoft
description: Descreve como planear a implementação do Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05bf125d629ffef01a645dc407c341a984805520
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227036"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Implementar o Azure AD Privileged Identity Management (PIM)

Este guia passo a passo descreve como planear a implementação do Azure AD Privileged Identity Management (PIM) na sua organização.

> [!TIP]
> Neste documento, verá os itens marcados como:
> 
> :heavy_check_mark: **A Microsoft recomenda**
> 
> Estas são recomendações gerais e deve implementar apenas se aplicam-se às suas necessidades empresariais específicas.

## <a name="step-1-learn-about-pim"></a>Passo 1. Saiba mais sobre o PIM

O Azure AD Privileged Identity Management (PIM) ajuda-o a gerir as funções administrativas com privilégios no Azure AD, recursos do Azure e outros serviços Online da Microsoft. Num mundo onde as identidades privilegiadas são atribuídas e esquecidas, PIM fornece soluções, como acesso just-in-time, fluxos de trabalho de aprovação de solicitação e totalmente integrado acesso a revisões para que possa identificar, descobrir e impedir que atividades maliciosas de privilégio funções em tempo real. Implantar o PIM para gerir as funções com privilégios em toda a organização irá reduzir significativamente o risco ao analisar informações valiosas sobre as atividades das suas funções com privilégios.

### <a name="business-value-of-pim"></a>Valor comercial do PIM

**Gerir o risco** -proteger a sua organização ao aplicar o princípio do [acesso de privilégio mínimo](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e acesso just-in-time. Minimizando o número de atribuições permanentes de utilizadores a funções com privilégios e a imposição de aprovações e MFA para elevação, podem reduzir significativamente os riscos de segurança relacionados com o acesso com privilégios na sua organização. Impor o acesso de privilégio e just-in-time, pelo menos, também permitirá que visualizar um histórico de acesso a funções com privilégios e rastrear problemas de segurança à medida que acontecem.

**Governação e conformidade de endereços** -implementar PIM cria um ambiente de governação de identidade em curso. Elevação just-in-time de identidades privilegiadas fornece uma forma do PIM controlar as atividades de acesso privilegiado na sua organização. Também poderá ver e receber notificações para todas as atribuições de funções elegíveis e permanentes dentro de sua organização. Por meio de revisão de acesso, pode regularmente de auditoria e remover desnecessárias identidades privilegiadas e certificar-se de que sua organização está em conformidade com as mais rigorosas normas de segurança, acesso e identidades.

**Reduzir os custos** -reduzir os custos, eliminando ineficiências, erro humano e problemas de segurança ao implementar o PIM corretamente. O resultado é uma redução de crimes informáticos associados com identidades privilegiadas, que são dispendiosos e difíceis de recuperar a partir de. PIM também ajudará sua organização a reduzir os custos associados com informações de acesso de auditoria, que diz respeito à conformidade com normas e regulamentos.

Para obter mais informações, consulte [o que é o Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Requisitos de licenciamento

Para utilizar o PIM, o diretório tem de ter um dos seguintes pagos ou licenças de avaliação:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Para obter mais informações, consulte [os requisitos para utilizar o PIM de licença](subscription-requirements.md).

### <a name="key-pim-terminology"></a>Terminologia de PIM de chave

| Termo ou conceito | Descrição |
| --- | --- |
| Elegível | Uma atribuição de função que requer que um utilizador efetuar uma ou mais ações para utilizar a função. Se um utilizador se tornou qualificado para uma função, o que significa que os utilizadores podem ativar a função quando precisam de executar tarefas com privilégios. Não existe nenhuma diferença no acesso concedido a qualquer pessoa com uma permanente em comparação com uma atribuição de função elegível. A única diferença é que, algumas pessoas não precisam que o acesso o tempo todo. |
| ativar | O processo de efetuar uma ou mais ações para utilizar uma função que é elegível para um utilizador. Ações podem incluir a efetuar uma verificação de autenticação multifator (MFA), fornecer uma justificação de negócio ou o pedido de aprovação de aprovadores designados. |
| acesso just-in-time (JIT) | Um modelo no qual os utilizadores recebem permissões temporárias para efetuar tarefas com privilégios, que impede que os utilizadores não autorizados ou mal-intencionados de obter acesso depois das permissões tiverem expirado. O acesso é concedido somente quando os utilizadores a necessidade. |
| princípio de acesso de privilégio mínimo | Uma prática recomendada de segurança em que cada utilizador é fornecido com apenas os privilégios mínimos necessários para realizar as tarefas que estão autorizados a efetuar. Esta prática minimiza o número de administradores globais e em vez disso, utiliza funções de administrador específicas para determinados cenários. |

Para obter mais informações, consulte [terminologia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-pim-works"></a>Visão geral do funcionamento do PIM

1. PIM ser configurado para que os utilizadores são elegíveis para funções privilegiadas.
1. Quando um utilizador elegível tem de utilizar a respetiva função com privilégios, que os utilizadores a ativar a função no PIM.
1. Consoante as definições do PIM configuradas para a função, o utilizador tem de concluir alguns passos (como efetuar a autenticação multifator, obterem a aprovação ou especificando um motivo.)
1. Assim que o utilizador ativa a respetiva função com êxito, ele obterá a função para um período de tempo pré-configurado.
1. Os administradores podem ver um histórico de todas as atividades do PIM no log de auditoria. Também pode proteger os seus inquilinos e estar em conformidade com funcionalidades PIM, como revisões de acesso e alertas.

Para obter mais informações, consulte [o que é o Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-pim"></a>Funções que podem ser geridas pelo PIM

**Funções do Azure AD** – estas funções são todas as funções de diretório dentro do Azure Active Directory (como Administrador Global, o administrador do Exchange e o administrador de segurança). Pode ler mais sobre as funções e a respetiva funcionalidade na [permissões da função de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Para obter ajuda a determinar quais funções para atribuir aos seus administradores, consulte [menos funções privilegiadas pela tarefa](../users-groups-roles/roles-delegate-by-task.md).

**Funções de recursos do Azure** – estas funções são associadas a um recurso do Azure, grupo de recursos, subscrição ou grupo de gestão. PIM fornece acesso just-in-time para ambas as funções incorporadas como proprietário, o administrador de acesso de utilizador e contribuinte, que [funções personalizadas](../../role-based-access-control/custom-roles.md). Para obter mais informações sobre as funções de recursos do Azure, consulte [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md).

Para obter mais informações, consulte [funções no PIM não é possível gerir](pim-roles.md).

## <a name="step-2-plan-your-deployment"></a>Passo 2. Planear a sua implementação

Esta secção concentra-se no que precisa de fazer antes de implementar o PIM na sua organização. É essencial para seguir as instruções e compreender os conceitos nesta secção como eles irão orientá-lo para criar o melhor plano de identidades com privilégios da sua organização.

### <a name="identify-your-stakeholders"></a>Identificar os interessados

A seção a seguir ajuda a identificar todos os participantes envolvidos no projeto e tem de terminar sessão, reveja ou mantenha-se informado. Ele inclui tabelas separadas para implantar o PIM para funções do Azure AD e o PIM para funções de recursos do Azure. Adicione intervenientes para a tabela seguinte, conforme adequado para a sua organização.

- Então, = início de sessão desativado neste projeto
- R = revisão este projeto e fornecer comentários
- Eu = Informed deste projeto

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>Participantes: Funções do PIM do Azure AD

| Name | Função | Ação |
| --- | --- | --- |
| Nome e e-mail | **Arquiteto de identidade ou de Administrador Global do Azure**<br/>Um representante da equipa de gestão de identidade, responsável por definir como esta alteração é alinhada com a infraestrutura de gestão de identidade de núcleos na sua organização. | SO/R/I |
| Nome e e-mail | **Proprietário do serviço / Gestor de linha**<br/>Um representante dos proprietários IT de um serviço ou um grupo de serviços. Eles são a chave na tomada de decisões e ajudar a distribuir o PIM para a sua equipa. | SO/R/I |
| Nome e e-mail | **Proprietário de segurança**<br/>Um representante da equipa de segurança que possam assinar se o plano cumpre os requisitos de segurança da sua organização. | SO/R |
| Nome e e-mail | **IT suporte manager / suporte técnico**<br/>Um representante da organização de suporte de TI que pode fornecer comentários sobre o suporte para esta alteração de uma perspectiva de suporte técnico. | R/I |
| Nome e e-mail para os usuários do piloto | **Utilizadores com função privilegiada**<br/>O grupo de utilizadores para os quais a gestão de identidades com privilégios é implementado. Precisam de saber como ativar as suas funções depois do PIM é implementado. | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>Participantes: Funções de recursos do PIM do Azure

| Name | Função | Ação |
| --- | --- | --- |
| Nome e e-mail | **Subscrição / proprietário do recurso**<br/>Um representante dos proprietários IT de cada subscrição ou recurso que pretende implementar o PIM para | SO/R/I |
| Nome e e-mail | **Proprietário de segurança**<br/>Um representante da equipa de segurança que possam assinar se o plano cumpre os requisitos de segurança da sua organização. | SO/R |
| Nome e e-mail | **IT suporte manager / suporte técnico**<br/>Um representante da organização de suporte de TI que pode fornecer comentários sobre o suporte para esta alteração de uma perspectiva de suporte técnico. | R/I |
| Nome e e-mail para os usuários do piloto | **Utilizadores de funções do RBAC**<br/>O grupo de utilizadores para os quais a gestão de identidades com privilégios é implementado. Precisam de saber como ativar as suas funções depois do PIM é implementado. | I |

### <a name="enable-pim"></a>Ativar PIM

Como parte do processo de planejamento, primeiro tem de consentir e ativar PIM ao seguir nossos [começar a utilizar o documento PIM](pim-getting-started.md). Ativar PIM dá-lhe acesso a algumas funcionalidades que são projetadas especificamente para ajudar com a sua implementação.

Se seu objetivo é implementar o PIM para recursos do Azure, deve seguir nossos [detetar recursos do Azure para gerir no documento PIM](pim-resource-roles-discover-resources.md). Apenas os proprietários de cada recurso, grupo de recursos e subscrição poderá descobri-las dentro do PIM. Se for um Administrador Global a tentar implementar o PIM para recursos do Azure, pode [elevar o acesso para gerir todas as subscrições do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) dar acesso a todos os recursos do Azure no diretório para a deteção. No entanto, aconselhamos que obtenha aprovação de cada um dos seus proprietários de subscrições antes de gerir os respetivos recursos com o PIM.

### <a name="enforce-principle-of-least-privilege"></a>Impor o princípio do menor privilégio

É importante certificar-se de que tem aplicada o princípio do menor privilégio na sua organização para o Azure AD e as funções de recursos do Azure.

#### <a name="azure-ad-roles"></a>Funções do Azure AD

Para funções do Azure AD, é comum que as organizações atribuir a função de Administrador Global para um número significativo dos administradores quando a maioria dos administradores precisa de um só ou funções de administrador específico dois. As atribuições também tendem a ficar com função privilegiada não gerido.

> [!NOTE]
> Armadilhas comuns na delegação de funções:
>
> - O administrador responsável pelo Exchange é concedido a função de Administrador Global, mesmo que apenas têm a função de administrador do Exchange para realizar seu trabalho diário.
> - A função de Administrador Global é atribuída a um administrador do Office, uma vez que o administrador tem de funções de administrador de segurança e o SharePoint e é mais fácil simplesmente delegar uma função.
> - O administrador foi atribuído uma função de administrador de segurança para executar uma tarefa muito tempo atrás, mas nunca foi removido.

Siga estes passos para impor o princípio de privilégio mínimo para as funções do Azure AD.

1. Compreender a granularidade das funções lendo e compreendendo os [disponíveis funções de administrador do Azure AD](../users-groups-roles/directory-assign-admin-roles.md#available-roles). E sua equipe também devem referenciar [funções de administrador pela tarefa de identidade no Azure AD](../users-groups-roles/roles-delegate-by-task.md), que explica a função com privilégios mínimos para tarefas específicas.

1. Lista que tem privilegiado funções na sua organização. Pode utilizar o [assistente PIM](pim-security-wizard.md#run-the-wizard) para aceder a uma página semelhante ao seguinte.

    ![Descobrir funções privilegiadas](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Para todos os administradores globais na sua organização, descubra por que precisam da função. Com base na leitura da documentação de anterior, se a tarefa da pessoa pode ser executada por uma ou mais funções de administrador granular, deve removê-los da função de Administrador Global e fazer atribuições em conformidade no Azure Active Directory (como uma referência: Atualmente o Microsoft tem apenas 10 administradores com a função de Administrador Global. Saiba mais em [como a Microsoft usa PIM](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. Para todas as outras funções do Azure AD, reveja a lista de atribuições, identificar os administradores que já não precisam da função e removê-los a partir de suas atribuições.

Para automatizar passos 3 e 4, pode utilizar a função de revisão de acesso no interior do PIM. Os passos a seguir [iniciar uma revisão de acesso para funções do Azure AD no PIM](pim-how-to-start-security-review.md), pode configurar uma revisão de acesso para cada função do Azure AD com um ou mais membros.

![Criar uma revisão de acesso](./media/pim-deployment-plan/create-access-review.png)

Deve definir os revisores **membros (auto)**. Isto irá enviar um e-mail a todos os membros na função para obtê-los para confirmar se precisarem de acesso. Também deve ativar **exigir razão na aprovação** nas definições avançadas para que os utilizadores podem indicar por que precisam da função. Com base nessas informações, será capaz de remover os utilizadores a funções desnecessárias e delegar funções de administrador mais granulares no caso dos administradores globais.

As revisões de acesso contam com mensagens de e-mail para notificar os utilizadores para rever o acesso às funções. Se tem privilegiado contas que não têm e-mails ligados, certifique-se de que preencher o campo de e-mail secundário essas contas. Para obter mais informações, consulte [atributo proxyAddresses do Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Funções de recursos do Azure

Para acessar recursos e subscrições do Azure, pode configurar um processo de revisão de acesso semelhante para rever as funções em cada subscrição ou ao recurso. O objetivo desse processo é minimizar as atribuições de proprietário e o administrador de acesso de utilizador anexadas a cada subscrição ou recurso, bem como para remover atribuições desnecessárias. No entanto, as organizações, muitas vezes, delegar tarefas para o proprietário de cada subscrição ou recurso porque têm uma melhor compreensão das funções específicas (funções especialmente personalizadas).

Se for um administrador de TI com a função de Administrador Global a tentar implementar o PIM para recursos do Azure na sua organização, pode [elevar o acesso para gerir todas as subscrições do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para obter acesso a cada subscrição. Em seguida, pode encontrar cada proprietário da subscrição e trabalhar com eles para remover atribuições desnecessárias e minimizar a atribuição de função de proprietário.

Os utilizadores com a função de proprietário para uma subscrição do Azure também podem utilizar [revisões para recursos do Azure de acesso](pim-resource-roles-start-access-review.md) auditar e remover atribuições de funções desnecessárias semelhantes ao processo descrito anteriormente para funções do Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>Decidir quais as atribuições de funções devem ser protegidas pelo PIM

Depois de limpar as atribuições de função com privilégios na sua organização, terá de decidir que funções para proteger com o PIM.

Se uma função é protegida pelo PIM, os utilizadores elegíveis atribuídos devem elevar para utilizar os privilégios concedidos pela função. O processo de elevação pode também incluir obterem aprovação, efetuar a autenticação multifator, e/ou com um motivo por que eles esteja a ativar. Também pode controlar o PIM Elevações através de notificações e registos de eventos de auditoria do PIM e o Azure AD.

Escolher quais funções para proteger com o PIM podem ser difícil e serão diferentes para cada organização. Esta seção fornece nosso conselhos sobre as melhores práticas para o Azure AD e funções de recursos do Azure.

#### <a name="azure-ad-roles"></a>Funções do Azure AD

É importante priorizar a proteger as funções do Azure AD que têm o maior número de permissões. As funções do Azure AD de 10 principais geridas pelo PIM com base nos padrões de utilização entre todos os clientes do PIM, são:

1. Administrador global
1. Administrador de segurança
1. Administrador de utilizadores
1. Administrador do Exchange
1. Administrador do SharePoint
1. Administrador do Intune
1. Leitor de segurança
1. Administrador de serviço
1. Administrador de faturação
1. Administrador do Skype para Empresas

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** gerir todos os seus administradores globais e administradores de segurança com o PIM como primeiro passo, pois são aqueles que podem fazer mais danos quando comprometido.

É importante considerar que dados e a permissão estão mais confidenciais para a sua organização. Por exemplo, algumas organizações poderão querer proteger a respetiva função de administrador do Power BI ou a respetiva função de administrador de Equipes com o PIM, pois têm a capacidade de aceder a dados e/ou alterar os fluxos de trabalho principal.

Se existirem quaisquer funções com utilizadores convidados atribuídos, eles são particularmente vulneráveis a ataques.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** gerir todas as funções com utilizadores convidados com o PIM para reduzir o risco associados a contas de utilizador convidado comprometido.

As funções do leitor, como o leitor de diretório, o leitor do Centro de mensagens e o leitor de segurança são, às vezes, consideradas menos importantes em comparação com outras funções à medida que não tem permissão de escrita. No entanto, temos visto alguns clientes também proteger estas funções, uma vez que os invasores que passaram a acesso a estas contas podem ser capazes de ler dados confidenciais, como informações de identificação pessoal (PII). Deve levar isso em consideração ao decidir se as funções do leitor na sua organização tem de ser geridos com o PIM.

#### <a name="azure-resource-roles"></a>Funções de recursos do Azure

Ao decidir qual atribuições de funções devem ser geridas com o PIM para recursos do Azure, primeiro tem de identificar os subscrições/recursos que são mais vitais para a sua organização. São exemplos de tais subscrições/recursos:

- Recursos que alojam os dados mais confidenciais
- Recursos principais, aplicativos do lado do cliente dependem

Se for um Administrador Global a ter problemas para decidir quais as subscrições/recursos são mais importantes, deve entrar em contacto para proprietários de subscrições na sua organização para reunir uma lista de recursos geridos por cada subscrição. Em seguida, deve trabalhar em conjunto com os proprietários de subscrições para agrupar os recursos com base no nível de gravidade, no caso de que ficarem comprometidas (baixa, média, alta). Deve priorizar gerem os recursos com o PIM com base neste nível de gravidade.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** trabalhar com proprietários de recursos/subscrição dos serviços críticos para configurar o fluxo de trabalho do PIM para todas as funções dentro de subscrições/recursos confidenciais.

PIM para recursos do Azure suporta contas de serviço com limite de tempo. Deve tratar as contas de serviço exatamente da mesma forma como trataria uma conta de utilizador normal.

Para subscrições/recursos que não são tão críticos, não terá de configurar o PIM para todas as funções. No entanto, ainda é necessário proteger as funções de proprietário e o administrador de acesso de utilizador com o PIM.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** gerir funções de proprietário e funções de administrador de acesso de utilizador de todas as subscrições/recursos com o PIM.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Decidir quais atribuições de função devem ser permanentes ou elegíveis

Depois de decidir a lista de funções para serem geridos pelo PIM, deve decidir quais usuários deverá obter a função elegível em comparação com a função ativa permanentemente. Funções ativas permanentemente são as funções normais atribuídas através do Azure Active Directory e os recursos do Azure, embora só podem ser atribuídas a funções elegíveis no PIM.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** tem zero ativas permanentemente atribuições de funções do Azure AD e funções de recursos do Azure que não seja recomendável [duas contas de acesso de emergência break glass](../users-groups-roles/directory-emergency-access.md), que deve ter o função de Administrador Global permanente.

Mesmo que recomendamos zero administrador permanente, às vezes é difícil para as organizações a atingir esse objetivo imediato. Seguem-se aspetos a considerar ao tomar esta decisão:

- Frequência de elevação – se o utilizador necessita apenas de uma vez, a atribuição com privilégios que eles não devem ter a atribuição permanente. Por outro lado, se o utilizador tem da função para o seu trabalho diário e utilizar o PIM seria reduzir significativamente a produtividade, eles podem ser considerados para a função permanente.
- Casos específicos da organização – se a pessoa que está a ser atribuída a função elegível for de uma equipe muito distante ou um executivo high-ranking para o ponto que, se comunicar e impor o processo de elevação, é difícil, eles podem ser considerados para a função permanente.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** -o a configurar o acesso recorrente revisões para os utilizadores com atribuições de função permanente (deve ter qualquer). Saiba mais sobre a revisão de acesso recorrentes na seção final desse plano de implementação

### <a name="draft-your-pim-settings"></a>As definições do PIM de rascunho

Antes de implementar sua solução PIM, é boa prática para esboçar as definições do PIM para cada função com privilégios de que sua organização utiliza. Esta secção tem alguns exemplos de definições do PIM para funções específicas (eles são apenas para referência e podem ser diferentes para a sua organização). Cada uma destas definições é explicada detalhadamente com recomendações da Microsoft após as tabelas.

#### <a name="pim-settings-for-azure-ad-roles"></a>Definições do PIM para funções do Azure AD

| Função | Requerer MFA | Notificação | Pedido de incidente | Exigir aprovação | Aprovador | Duração da ativação | Administrador permanente |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrador Global | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros administradores globais | Uma Hora | Contas de acesso de emergência |
| Administrador do Exchange | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | Nenhuma | 2 horas | Nenhuma |
| Administrador de Suporte Técnico | :x: | :x: | :heavy_check_mark: | :x: | Nenhuma | 8 horas | Nenhuma |

#### <a name="pim-settings-for-azure-resource-roles"></a>Definições do PIM para funções de recursos do Azure

| Função | Requerer MFA | Notificação | Exigir aprovação | Aprovador | Duração da ativação | Administração do Active Directory | Expiração do Active Directory | Expiração elegível |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Proprietário de subscrições críticas | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros proprietários da subscrição | Uma Hora | Nenhuma | n/d | 3 meses |
| Administrador de acesso de utilizador de subscrições menos críticas | :heavy_check_mark: | :heavy_check_mark: | :x: | Nenhuma | Uma Hora | Nenhuma | n/d | 3 meses |
| Contribuinte de Máquina Virtual | :x: | :heavy_check_mark: | :x: | Nenhuma | Duração de 3 horas | Nenhuma | n/d | 6 meses |

A tabela seguinte descreve cada uma das definições.

| Definição | Descrição |
| --- | --- |
| Função | Nome da função que está a definir as definições para. |
| Requerer MFA | Se o utilizador elegível tem de executar a MFA antes de ativar a função.<br/><br/> :heavy_check_mark: **A Microsoft recomenda** impor o MFA para todas as funções de administrador, especialmente se as funções têm utilizadores convidados. |
| Notificação | Se definido como true, o Administrador Global, administrador com função privilegiada e o administrador de segurança da organização irão receber uma notificação por e-mail quando um utilizador elegível ativa a função.<br/><br/>**Nota:** Algumas organizações não tem um endereço de e-mail associado para suas contas de administrador, para receber estas notificações de e-mail, deve definir um endereço de e-mail alternativo, para que os administradores irão receber estes e-mails. |
| Pedido de incidente | Se o utilizador elegível precisar gravar um número de pedido de incidente ao ativar a respetiva função. Esta definição ajuda uma organização, identificar cada ativação com um número de incidente interno para atenuar ativações indesejadas.<br/><br/> :heavy_check_mark: **A Microsoft recomenda** tirar partido de números de incidentes do pedido de suporte para associar o PIM com o seu sistema interno. Isso é particularmente útil para os aprovadores que precisam de contexto para a ativação. |
| Exigir aprovação | Se o utilizador elegível tem de obter aprovação para ativar a função.<br/><br/> :heavy_check_mark: **A Microsoft recomenda** configurar a aprovação para funções com mais permissão. Com base nos padrões de utilização de todos os clientes do PIM, o Administrador Global, administrador de utilizadores, administrador do Exchange, administrador de segurança e palavra-passe de administrador são as funções mais comuns com a configuração de aprovação. |
| Aprovador | Se é necessária para ativar a função elegível, listar as pessoas que devem aprovar o pedido de aprovação. Por predefinição, o PIM define o aprovador para ser todos os utilizadores que são um administrador com função privilegiada, quer sejam permanentes ou elegíveis.<br/><br/>**Nota:** Se um utilizador é elegível para uma função do Azure AD e um aprovador da função, não poderão aprovar propriamente ditas.<br/><br/> :heavy_check_mark: **A Microsoft recomenda** que escolha os aprovadores ser aqueles que são mais bem informadas sobre a função específica e seus usuários frequentes, em vez de um Administrador Global. |
| Duração da ativação | O período de tempo que um utilizador será ativado na função antes que ele irá expirar. |
| Administrador permanente | Lista de utilizadores que será um administrador permanente para a função (nunca terá de ativar).<br/><br/> :heavy_check_mark: **A Microsoft recomenda** tem zero administrador permanente para todas as funções, exceto para os administradores globais. Leia mais sobre isso no que deve ser feita elegível e que deve ser ativa permanentemente secção deste plano. |
| Administração do Active Directory | Para recursos do Azure, o administrador do Active Directory é a lista de utilizadores que nunca terá de ativar para utilizar a função. Isso não é referido como administrador permanente, como em funções do Azure AD porque pode definir um prazo de expiração para quando o utilizador vai perder esta função. |
| Expiração do Active Directory | Uma atribuição de função ativa para funções de recursos do Azure expirar após este período de tempo. Pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou ativa permanentemente. |
| Expiração elegível | Uma atribuição de função elegível para funções de recursos do Azure expirar após este período de tempo. Pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou permanentemente elegível. |

## <a name="step-3-implement-your-solution"></a>Passo 3. Implementar a sua solução

A base de planejamento apropriado é a base sobre a qual pode implementar uma aplicação com êxito com o Azure Active Directory.  Ele fornece segurança inteligente e integração que simplifica a integração, reduzindo o tempo para Implantações bem-sucedidas.  Esta combinação garante que seu aplicativo é integrado com facilidade ainda mitigando período de indisponibilidade para os utilizadores finais.

### <a name="identify-test-users"></a>Identificar os utilizadores de teste

Utilize esta secção para identificar um conjunto de utilizadores e ou grupos de utilizadores para validar a implementação. Com base nas definições que selecionou na secção de planeamento, identifique os utilizadores que pretende testar para cada função.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** fizer os proprietários de serviços de cada função do Azure AD para os utilizadores de teste para que possam familiarizar-se com o processo e se tornar um advocator interno para a implementação.

Nesta tabela, identificar os utilizadores de teste que irão verificar se as definições para cada função está a funcionar.

| Nome da função | Utilizadores de teste |
| --- | --- |
| &lt;Nome da função&gt; | &lt;Utilizadores para testar a função&gt; |
| &lt;Nome da função&gt; | &lt;Utilizadores para testar a função&gt; |

### <a name="test-implementation"></a>Implementação de teste

Agora que identificou os utilizadores de teste, utilize este passo para configurar o PIM para os seus utilizadores de teste. Se a organização pretende incorporar o fluxo de trabalho do PIM em seu próprio aplicativo interno em vez de usar a interface do usuário do PIM no portal do Azure, todas as operações no PIM também recebem suporte dos nossos [graph API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-pim-for-azure-ad-roles"></a>Configurar o PIM para funções do Azure AD

1. [Configurar as definições de função de diretório do Azure AD](pim-how-to-change-default-settings.md) com base em o planeado.

1. Navegue para **funções do Azure AD**, clique em **funções**e, em seguida, selecione a função que acabou de configurar.

1. Para o grupo de utilizadores de teste, se já for um administrador permanente, pode torná-los elegíveis ao pesquisá-los e convertê-los de permanente para elegíveis ao clicar nos três pontos na respetiva linha. Se eles não têm as atribuições de funções, ainda, pode [fazer uma nova atribuição elegível](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Repita os passos 1 a 3 para todas as funções que deseja testar.

1. Assim que tiver configurado os utilizadores de teste, deve enviar-lhes a ligação para saber como [ativar a respetiva função do Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-pim-for-azure-resource-roles"></a>Configurar o PIM para funções de recursos do Azure

1. [Configurar as definições de função de recursos do Azure](pim-resource-roles-configure-role-settings.md) para uma função dentro de uma subscrição ou recurso que pretende testar.

1. Navegue para **recursos do Azure** para essa subscrição e clique em **funções**, selecione a função que acabou de configurar.

1. Para o grupo de utilizadores de teste, se já for administrador do Active Directory, pode torná-los elegíveis pesquisando-os e [atualizar a respetiva atribuição de função](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Se eles não têm a função ainda, pode [atribuir uma nova função](pim-resource-roles-assign-roles.md#assign-a-role).

1. Repita os passos 1 a 3 para todas as funções que deseja testar.

1. Assim que tiver configurado os utilizadores de teste, deve enviar-lhes a ligação para saber como [ativar a respetiva função de recursos do Azure](pim-resource-roles-activate-your-roles.md).

Deve usar esse estágio para verificar que se toda a configuração definida para obter as funções estão funcionando corretamente. Utilize a seguinte tabela para documentar os seus testes. Também deve usar neste estágio para otimizar a comunicação com os utilizadores afetados.

| Função | Comportamento esperado durante a ativação | Resultados reais |
| --- | --- | --- |
| Administrador Global | (1) exigir a MFA<br/>(2) exigem a aprovação<br/>(3) aprovador recebe a notificação e pode aprovar<br/>(4) função expira após o tempo predefinido |  |
| Proprietário da subscrição *X* | (1) exigir a MFA<br/>(2) atribuição elegível expira após o período de tempo configurado |  |

### <a name="communicate-pim-to-affected-stakeholders"></a>Comunicar PIM aos participantes afetados

Implantar o PIM apresentará passos adicionais para os utilizadores de funções com privilégios. Embora o PIM reduz consideravelmente os problemas de segurança associados com identidades privilegiadas, a alteração tem de ser efetivamente comunicado antes da implantação de ao nível do inquilino. Dependendo do número de administradores afetados, as organizações, muitas vezes, optar por criar um documento interno, um vídeo ou um e-mail sobre a alteração. Com frequência incluído nestas comunicações incluem:

- O que é o PIM
- O que é o benefício para a organização
- Que serão afetados
- Quando será PIM implementada
- Quais etapas adicionais serão necessárias para os utilizadores ativem a respetiva função
    - Deve enviar ligações para a nossa documentação:
    - [Ativar as funções do Azure AD](pim-how-to-activate-role.md)
    - [Ativar as funções de recursos do Azure](pim-resource-roles-activate-your-roles.md)
- Informações de contacto ou uma ligação de suporte técnico para quaisquer problemas associados com o PIM

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** configurar tempo com a sua equipa de suporte técnico/suporte para orientá-lo no fluxo de trabalho do PIM (se a organização tiver um suporte de TI interno equipe). Disponibilize-lhes com os documentação adequados atualizados, bem como as informações de contacto.

### <a name="move-to-production"></a>Mover para produção

Assim que o teste for concluída com sucesso, mover o PIM para produção, repetindo todas as etapas nas fases de teste para todos os utilizadores de cada função que definiu na sua configuração do PIM. PIM para funções do Azure AD, as organizações, muitas vezes, testar e implementar PIM para administradores globais antes de testar e implementar PIM para outras funções. Enquanto isso para recursos do Azure, as organizações normalmente testar e distribuam o PIM uma subscrição do Azure ao mesmo tempo.

### <a name="in-the-case-a-rollback-is-needed"></a>No caso é necessária uma reversão

Se tiver falhado PIM funcionar como pretendido no ambiente de produção, os seguintes passos de reversão podem ajudá-lo para reverter para um Estado válido antes de configurar o PIM:

#### <a name="azure-ad-roles"></a>Funções do Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Open **do Azure AD Privileged Identity Management**.
1. Clique em **funções do Azure AD** e, em seguida, clique em **funções**.
1. Para cada função que tenha configurado, clique nas reticências (**...** ) para todos os utilizadores com uma atribuição elegível.
1. Clique nas **tornar permanente** opção para tornar a atribuição de função permanentes.

#### <a name="azure-resource-roles"></a>Funções de recursos do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Open **do Azure AD Privileged Identity Management**.
1. Clique em **recursos do Azure** e, em seguida, clique numa subscrição ou recurso que pretende reverter.
1. Clique em **funções**.
1. Para cada função que tenha configurado, clique nas reticências (**...** ) para todos os utilizadores com uma atribuição elegível.
1. Clique nas **tornar permanente** opção para tornar a atribuição de função permanentes.

## <a name="step-4-next-steps-after-deploying-pim"></a>Passo 4. Passos seguintes depois de implementar o PIM

Implementação com êxito o PIM na produção é um avanço significativo em termos de proteger a sua organização do privilegiado identidades. Com a implantação do PIM vem funcionalidades adicionais do PIM que deve usar para segurança e conformidade.

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>Alertas PIM de utilização para salvaguardar o acesso privilegiado

Deve utilizar alerta uma funcionalidade incorporada do PIM para proteger melhor seu inquilino. Para obter mais informações, consulte [alertas de segurança](pim-how-to-configure-security-alerts.md#security-alerts). Estes alertas incluem: os administradores não estiverem a utilizar funções com privilégios, estão a ser atribuídas a funções fora do PIM, funções estão a ser ativadas com demasiada frequência e muito mais. Para proteger completamente a sua organização, deve regularmente percorra a lista de alertas e corrigir os problemas. Pode ver e corrigir os alertas da seguinte forma:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Open **do Azure AD Privileged Identity Management**.
1. Clique em **funções do Azure AD** e, em seguida, clique em **alertas**.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** lida com todos os alertas marcados com gravidade elevada imediatamente. Para os alertas de gravidade média e baixa, deve manter-se informado e fazer alterações, se acreditar que existe uma ameaça de segurança.

Se qualquer um dos alertas específicos não são úteis ou não se aplica à sua organização, sempre pode ignorar o alerta da página de alertas. Poderá sempre reverter esta exoneração mais tarde na página de definições do Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Configurar as revisões de acesso recorrentes para audite regularmente as identidades privilegiadas da sua organização

As revisões de acesso são a melhor maneira para fazer os utilizadores com funções com privilégios ou revisores específicos se cada utilizador tem da identidade com privilégios. As revisões de acesso são ótimas para reduzir a superfície de ataque e mantenha-se em conformidade. Para obter mais informações sobre como iniciar uma revisão de acesso, consulte [as revisões de acesso de funções do Azure AD](pim-how-to-start-security-review.md) e [as revisões de acesso a funções de recursos do Azure](pim-resource-roles-start-access-review.md). Para algumas organizações, realizar a revisão de acesso periódica é necessário para se manter em conformidade com as leis e regulamentos, ao mesmo tempo, para que outros, revisão de acesso é a melhor forma de aplicar o princípio de privilégio mínimo em toda a organização.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** configurar trimestralmente as revisões de acesso do Azure AD e funções de recursos do Azure.

Na maioria dos casos, o revisor para funções do Azure AD é os próprios usuários enquanto o revisor para funções de recursos do Azure é o proprietário da subscrição, o que a função está em. No entanto, muitas vezes, é o caso em que as empresas têm contas que não estão ligadas com o endereço de e-mail de qualquer pessoa específica privilegiadas. Nesses casos, ninguém lê e analisa o acesso.

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** adicionar um endereço de e-mail secundário para todas as contas com atribuições de função com privilégios que não estão ligadas a um endereço de e-mail verificado regularmente

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Obter o máximo proveito de seu log de auditoria para melhorar a segurança e conformidade

O registo de auditoria é o local onde pode manter-se atualizado e estar em conformidade com regulamentos. Atualmente o PIM armazena um histórico de 30 dias do histórico de sua organização dentro do respetivo registo de auditoria incluindo:

- Ativação/desativação de funções elegíveis
- Atividades de atribuição de função dentro e fora do PIM
- Alterações nas definições de função
- Atividades de pedido/aprovar/negar de mensagens em fila para ativação de função com a configuração de aprovação
- Atualizar alertas

Pode aceder a estes registos de auditoria, se for um Administrador Global ou um administrador com função privilegiada. Para obter mais informações, consulte [histórico para funções do Azure AD de auditorias](pim-how-to-use-audit-log.md) e [histórico para funções de recursos do Azure de auditorias](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** que tenha, pelo menos, um administrador ler através de todos os eventos numa base semanal de auditoria e exportar seus eventos de auditoria mensalmente.

Se quiser armazenar automaticamente seus eventos de auditoria para um período mais longo do tempo, o log de auditoria do PIM é automaticamente sincronizado para o [registos de auditoria do Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: **A Microsoft recomenda** configurar [monitorização do registo do Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) para arquivar eventos de auditoria numa conta de armazenamento do Azure para a necessidade de segurança e conformidade.
