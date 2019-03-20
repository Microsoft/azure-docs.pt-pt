---
title: Cenários adicionais - Azure Active Directory de licenciamento baseado no grupo | Documentos da Microsoft
description: Mais cenários para o Azure Active Directory com base em grupo licenciamento
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d2faefd8443383e7afff8e3729bf6f1cf25c3a7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887035"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Cenários, limitações e problemas conhecidos, utilizar grupos para gerir o licenciamento no Azure Active Directory

Utilize as seguintes informações e exemplos para obter uma compreensão mais avançada do Azure Active Directory (Azure AD) com base no grupo de licenciamento.

## <a name="usage-location"></a>Localização de utilização

Alguns serviços Microsoft não estão disponíveis em todas as localizações. Antes de uma licença pode ser atribuída a um utilizador, o administrador tem de especificar o **localização de utilização** propriedade no utilizador. Na [do portal do Azure](https://portal.azure.com), pode especificar nos **utilizador** &gt; **perfil** &gt; **definições**.

Para a atribuição de licença de grupo, todos os utilizadores sem uma localização de utilização definida herdará a localização do diretório. Se tiver usuários em vários locais, certifique-se refletir que corretamente em seus objetos de utilizador antes de adicionar utilizadores a grupos com licenças.

> [!NOTE]
> Atribuição de licenças de grupo nunca irá modificar um valor de localização de utilização existente num utilizador. Recomendamos que sempre definir localização de utilização como parte do seu fluxo de criação do utilizador no Azure AD (por exemplo, através do AAD Connect configuração) – que garante que o resultado da atribuição de licenças sempre está correto e que os utilizadores não recebem serviços em locais que não são permitidas.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Utilizar o baseada em grupo licenciamento com grupos dinâmicos

Pode utilizar o licenciamento com o botão com base em grupo com qualquer grupo de segurança, o que significa que pode ser combinada com os grupos dinâmicos do Azure AD. Grupos dinâmicos executam regras contra utilizador atributos de objetos para adicionar e remover utilizadores de grupos automaticamente.

Por exemplo, pode criar um grupo dinâmico para um conjunto de produtos que pretende atribuir aos utilizadores. Cada grupo é preenchido por uma regra de adição de usuários por seus atributos, e cada grupo é atribuído as licenças que pretende que receber. Pode atribuir o atributo no local e sincronizá-la com o Azure AD, ou pode gerenciar o atributo diretamente na cloud.

Pouco tempo depois de serem adicionados ao grupo as licenças são atribuídas ao utilizador. Quando o atributo é alterado, o usuário deixar os grupos e as licenças são removidas.

### <a name="example"></a>Exemplo

Considere o exemplo de uma solução de gestão de identidade no local que decide quais os utilizadores devem ter acesso aos serviços web da Microsoft. Ele usa **extensionAttribute1** para armazenar um valor de cadeia de caracteres que representa as licenças de utilizador deve ter. O Azure AD Connect sincroniza-o com o Azure AD.

Os utilizadores poderão precisar de uma licença, mas não outro, ou ambos. Eis um exemplo, em que está distribuindo o Office 365 Enterprise E5 e o Enterprise Mobility + licenças Security (EMS) para os utilizadores nos grupos:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: serviços de bases

![Serviços de bases de captura de ecrã do Office 365 Enterprise E5](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: os usuários licenciados

![Captura de ecrã do Enterprise Mobility + Security utilizadores com licença](./media/licensing-group-advanced/o365-e5-licensed-users.png)

Para este exemplo, modificar um utilizador e defina a respetiva extensionAttribute1 para o valor de `EMS;E5_baseservices;` se pretender que o usuário tenha ambas as licenças. Pode fazer essa modificação no local. Após a alteração for sincronizado com a cloud, o utilizador é adicionado automaticamente a ambos os grupos e as licenças são atribuídas.

![Captura de ecrã que mostra como definir extensionAttribute1 do utilizador](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Tenha cuidado ao modificar a regra de associação de um grupo existente. Quando uma regra é alterada, a associação do grupo serão reavaliada e os utilizadores que já não correspondem a nova regra serão removidos (os utilizadores que ainda a nova regra não será afetada durante este processo de correspondência). Os usuários tenham cujas licenças são removidas durante o processo que pode resultar na perda de serviço, ou em alguns casos, perda de dados.
> 
> Se tiver um grande grupo dinâmico que dependem para atribuição de licenças, considere a validar alterações significativas num grupo de teste mais pequeno antes de aplicá-las para o grupo principal.

## <a name="multiple-groups-and-multiple-licenses"></a>Vários grupos e várias licenças

Um utilizador pode ser um membro de vários grupos com licenças. Seguem-se alguns aspetos a considerar:

- Várias licenças para o mesmo produto podem sobrepor-se, e eles resultam em todos os serviços ativados, que está a ser aplicados ao utilizador. O exemplo seguinte mostra dois grupos de licenciamento: *Serviços de bases E3* contém os serviços de base para implementar em primeiro lugar, a todos os utilizadores. E *E3 estendido serviços* contém serviços adicionais (Sway e Planner) para implementar apenas a alguns utilizadores. Neste exemplo, o utilizador foi adicionado a ambos os grupos:

  ![Captura de ecrã de serviços ativados](./media/licensing-group-advanced/view-enabled-services.png)

  Como resultado, o utilizador tem 7 dos 12 serviços no produto ativado, ao utilizar apenas uma licença para este produto.

- Selecionar o *E3* licença mostra mais detalhes, incluindo informações sobre os quais grupos causado quais serviços seja ativado para o utilizador.

  ![Captura de ecrã de serviços ativados por grupo](./media/licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Licenças diretas coexistam com licenças de grupo

Quando um utilizador herda uma licença de um grupo, não é possível remover ou modificar diretamente a essa atribuição de licenças nas propriedades do utilizador. Alterações tem de ser feitas no grupo e, em seguida, propagadas a todos os utilizadores.

No entanto, é possível, atribua a mesma licença de produto diretamente para o usuário, além de licenças herdadas. Pode habilitar serviços adicionais do produto apenas para um utilizador, sem afetar outros utilizadores.

Diretamente licenças atribuídas podem ser removidas e não afetam herdadas licenças. Considere o utilizador que herda de uma licença do Office 365 Enterprise E3 de um grupo.

1. Inicialmente, o utilizador herde a licença apenas a partir da *serviços básicos de E3* grupo, que permite que os quatro planos de serviço, conforme mostrado:

   ![Grupo de captura de ecrã de E3 ativado os serviços](./media/licensing-group-advanced/e3-group-enabled-services.png)

2. Pode selecionar **atribuir** diretamente atribuir uma licença de E3 ao usuário. Neste caso, vai desativar todos os planos de serviço Yammer Enterprise:

   ![Captura de ecrã de como atribuir uma licença diretamente a um utilizador](./media/licensing-group-advanced/assign-license-to-user.png)

3. Como resultado, o utilizador ainda usa apenas uma licença do produto E3. Mas a atribuição direta permite que o serviço de Yammer Enterprise para apenas esse utilizador. Pode ver quais os serviços estão ativados pela associação a grupos em comparação com a atribuição direta:

   ![Captura de ecrã do herdadas versus atribuição direta](./media/licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Quando utilizar a atribuição direta, são permitidas as seguintes operações:

   - Yammer Enterprise pode ser desativado no objeto do usuário diretamente. O **/desativar** alternância na ilustração foi ativada para este serviço, ao contrário de outros alternadores de serviço. Uma vez que o serviço é ativado diretamente no utilizador, pode ser modificado.
   - Serviços adicionais podem ser ativados, bem, como parte da licença atribuída diretamente.
   - O **remover** botão pode ser utilizado para remover a licença direta do usuário. Pode ver que o utilizador só tem agora a licença de grupo herdadas e apenas os serviços originais permanecerem ativados:

     ![Captura de ecrã que mostra como remover a atribuição direta](./media/licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Gerenciamento de novos serviços adicionados ao produtos
Quando a Microsoft adiciona um novo serviço a um produto, será ativada por predefinição em todos os grupos aos quais atribuiu a licença de produto. Os utilizadores no seu inquilino que estão a subscrever notificações sobre alterações de produto receberá emails antecipadamente notificar sobre as adições de futura do serviço.

Como administrador, pode rever todos os grupos afetados pela alteração e tomar medidas, como desabilitar o novo serviço em cada grupo. Por exemplo, se tiver criado grupos direcionando apenas os serviços específicos para a implementação, pode rever esses grupos e certifique-se de que quaisquer recentemente adicionadas serviços estão desativados.

Eis um exemplo deste processo pode ser, como:

1. Originalmente, atribuído a *Office 365 Enterprise E5* produto para vários grupos. Um desses grupos, chamado *do Office 365 E5 - Exchange só* foi concebido para permitir apenas o *Exchange Online (plano 2)* serviço para seus membros.

2. Recebeu uma notificação da Microsoft, que será expandido com um novo serviço - o produto de E5 *Microsoft Stream*. Quando o serviço fica disponível no seu inquilino, pode fazer o seguinte:

3. Vá para o [ **do Azure Active Directory > licenças > todos os produtos** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) painel e selecione *Office 365 Enterprise E5*, em seguida, selecione **grupos licenciados** para ver uma lista de todos os grupos com esse produto.

4. Clique no grupo que pretende rever (neste caso, *do Office 365 E5 - Exchange apenas*). Esta ação abre o **licenças** separador. Ao clicar na licença E5 será aberto um painel a listagem de todos os serviços ativados.
   > [!NOTE]
   > O *Microsoft Stream* serviço foi automaticamente adicionado e ativado neste grupo, para além da *Exchange Online* serviço:

   ![Captura de ecrã do novo serviço adicionado a uma licença de grupo](./media/licensing-group-advanced/manage-new-services.png)

5. Se pretender desativar o novo serviço neste grupo, clique nas **/desativar** Ativar/desativar junto do serviço e clique no **guardar** botão para confirmar a alteração. O Azure AD agora irá processar todos os utilizadores no grupo para aplicar a alteração; quaisquer novos utilizadores adicionados ao grupo não terão o *Microsoft Stream* serviço ativado.

   > [!NOTE]
   > Os utilizadores ainda podem ter o serviço através de alguns outra atribuição de licenças (outro grupo sejam membros de ou uma atribuição da licença direta).

6. Se for necessário, execute os mesmos passos para outros grupos com este produto atribuído.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Utilizar o PowerShell para ver quem tem herdadas e licenças diretas
Pode usar um script do PowerShell para verificar se os utilizadores têm uma licença atribuída diretamente ou herdada a partir de um grupo.

1. Execute o `connect-msolservice` cmdlet para se autenticar e ligar ao seu inquilino.

2. `Get-MsolAccountSku` pode ser utilizada para detetar todas as licenças de produto aprovisionado no inquilino.

   ![Captura de ecrã do cmdlet Get-Msolaccountsku](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Utilize o *AccountSkuId* valor para a qual está interessado em com licença [este script do PowerShell](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Isto produzirá uma lista de utilizadores com esta licença com as informações sobre como a licença é atribuída.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Utilizar registos de auditoria para monitorizar a atividade de licenciamento baseado no grupo

Pode usar [registos de auditoria do Azure AD](../reports-monitoring/concept-audit-logs.md#audit-logs) ver todas as atividades relacionadas com licenciamento baseado em grupo, incluindo:
- quem alterou licenças em grupos
- Quando o sistema começou a processar uma alteração de licença de grupo, e quando concluído
- que alterações de licença foram feitas a um utilizador como resultado de uma atribuição de licença de grupo.

>[!NOTE]
> Registos de auditoria estão disponíveis na maioria dos painéis na seção do portal do Azure Active Directory. Dependendo de onde acessá-los, os filtros podem ser previamente aplicados para mostrar apenas atividades relevantes para o contexto do painel. Se não está a ver os resultados esperados, examine [as opções de filtragem](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) ou aceder aos registos de auditoria não filtrado sob [ **do Azure Active Directory > atividade > registos de auditoria** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Descubra quem modificado de uma licença de grupo

1. Definir o **atividade** filtrar para *definir licença de grupo* e clique em **aplicar**.
2. Os resultados incluem todos os casos de licenças que está a ser definida ou modificado em grupos.
   >[!TIP]
   > Também pode escrever o nome do grupo na *destino* filtro para definir o âmbito os resultados.

3. Clique num item na vista de lista para ver os detalhes do que mudou. Sob *propriedades modificadas* antigos e novos valores para a atribuição de licença estão listados.

Eis um exemplo de alterações recentes de licença de grupo, com detalhes:

![Alterações de licença de grupo de captura de ecrã](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Saber quando as alterações ao grupo iniciado e termina o processamento

Quando altera uma licença num grupo, do Azure AD começará a aplicar as alterações a todos os utilizadores.

1. Para ver quando grupos iniciou o processamento, defina o **atividade** filtrar para *começar a aplicar licença baseada em grupo aos utilizadores*. Tenha em atenção que é o ator para a operação *Microsoft Azure AD com base em grupo licenciamento* -uma conta do sistema que é utilizada para executar todas as alterações de licença de grupo.
   >[!TIP]
   > Clique num item na lista para ver os *propriedades modificadas* campo - mostra as alterações de licença que foram escolhidas para processamento. Isto é útil se feitas várias alterações a um grupo e não estiver certo qual deles foi processada.

2. Da mesma forma, para ver a terminou de grupos de processamento, utilize o valor do filtro *concluir a aplicação de licença baseada em grupo aos utilizadores*.
   > [!TIP]
   > Neste caso, o *propriedades modificadas* campo contém um resumo dos resultados – isto é útil para verificar rapidamente se o processamento resultou em erros. Resultado do exemplo:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Para ver o registo completo para como um grupo foi processado, incluindo todas as alterações de utilizador, defina os seguintes filtros:
   - **Iniciado por (Ator)**: "Microsoft Azure AD com base em grupo licenciamento"
   - **Intervalo de datas** (opcional): intervalo personalizado para quando sabe que um grupo específico iniciado e termina o processamento

Esta saída de exemplo mostra o início do processamento, todas as alterações de usuário resultante e a conclusão do processamento.

![Alterações de licença de grupo de captura de ecrã](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Clicar em itens relacionados à *alterar licença de utilizador* irá mostrar os detalhes para as alterações de licença aplicadas a cada utilizador individual.

## <a name="deleting-a-group-with-an-assigned-license"></a>Eliminar um grupo com uma licença atribuída

Não é possível eliminar um grupo com uma licença do Active Directory atribuída. Um administrador pode eliminar um grupo de sem perceber que fará com que licenças a serem removidos da utilizadores - por esse motivo que necessitam de quaisquer licenças a remover do grupo em primeiro lugar, antes de poder ser eliminado.

Ao tentar eliminar um grupo no portal do Azure poderá ver uma notificação de erro como este: ![Falha na eliminação do grupo de captura de ecrã](./media/licensing-group-advanced/groupdeletionfailed.png)

Vá para o **licenças** separador no grupo e ver se existem quaisquer licenças atribuídas. Em caso afirmativo, remova essas licenças e tente eliminar o grupo novamente.

Poderá ver erros semelhantes ao tentar eliminar o grupo através do PowerShell ou Graph API. Se estiver a utilizar um grupo sincronizado a partir do local, o Azure AD Connect também pode comunicar erros se está a falhar ao eliminar o grupo no Azure AD. Nesses casos, não se esqueça de verificar se existem quaisquer licenças atribuídas ao grupo e removê-los primeiro.

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

Se utilizar o licenciamento baseado em grupo, é uma boa idéia para se familiarizar com a seguinte lista de limitações e problemas conhecidos.

- Licenciamento baseado em grupo atualmente não suporta grupos que contêm outros grupos (grupos aninhados). Se aplicar uma licença a um grupo aninhado, apenas os membros utilizadores de primeiro nível do grupo têm as licenças aplicadas.

- A funcionalidade só pode ser utilizada com grupos de segurança e grupos do Office 365 com securityEnabled = TRUE.

- O [portal de administração do Office 365](https://portal.office.com ) não suporta atualmente licenciamento baseado em grupo. Se um utilizador herda uma licença de um grupo, esta licença é apresentado no portal de administração do Office como uma licença de utilizador normal. Se tentar modificar essa licença ou tente remover a licença, o portal devolve uma mensagem de erro. Não não possível modificar licenças herdadas de grupo diretamente num utilizador.

- Quando as licenças são atribuídas ou modificadas durante um grande grupo (por exemplo, 100 000 utilizadores), ele poderia afetar o desempenho. Especificamente, o volume de alterações gerado pela automatização do Azure AD pode afetar negativamente o desempenho da sua sincronização de diretórios entre o Azure AD e sistemas no local.

- Se estiver a utilizar grupos dinâmicos para gerir a associação do seu utilizador, certifique-se de que o utilizador faz parte do grupo, o que é necessário para a atribuição de licenças. Caso contrário, [verifique o estado de processamento para a regra de associação](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) do grupo dinâmico. 

- Em determinadas situações de carga elevada, poderá demorar muito tempo a processar as alterações de licença para grupos ou as alterações na associação a grupos com licenças existentes. Se vir as suas alterações demorar mais de 24 horas para processar o grupo de tamanho de utilizadores de 60 mil ou menos, tente [abra um pedido de suporte](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) para permitir a investigar. 

- Automatização da gestão de licenças não reagirem automaticamente a todos os tipos de alterações no ambiente. Por exemplo, poderá ter ficado sem licenças, fazendo com que alguns usuários estejam no estado de erro. Para libertar o número de licenças disponíveis, pode remover algumas licenças atribuídas diretamente de outros utilizadores. No entanto, o sistema não automaticamente reagir a essa alteração e corrigir os utilizadores com esse Estado de erro.

  Como solução para esses tipos de limitações, pode ir para o **grupo** painel no Azure AD e clique em **Reprocessar**. Este comando processa todos os utilizadores nesse grupo e resolve os Estados de erro, se possível.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre outros cenários para gestão de licenças através do licenciamento baseado no grupo, veja:

* [O que é o licenciamento no Azure Active Directory com base em grupo?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Atribuir licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
* [Como migrar os utilizadores licenciados individuais para o licenciamento baseado no grupo no Azure Active Directory](licensing-groups-migrate-users.md)
* [Como migrar os utilizadores entre licenças de produto através do licenciamento com o botão com base em grupo no Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Exemplos do PowerShell para licenciamento com o botão com base em grupo no Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
