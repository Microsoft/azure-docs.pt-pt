---
title: Cenários adicionais de licenciamento baseado em grupo-Azure Active Directory | Microsoft Docs
description: Mais cenários para Azure Active Directory licenciamento baseado em grupo
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 09/27/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cfdb8b979d20b77bcbf2f6b0d17855dfa0ac817
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034117"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Cenários, limitações e problemas conhecidos usando grupos para gerenciar o licenciamento no Azure Active Directory

Use as informações e exemplos a seguir para obter uma compreensão mais avançada do licenciamento baseado em grupo do Azure Active Directory (AD do Azure).

## <a name="usage-location"></a>Local de uso

Alguns serviços Microsoft não estão disponíveis em todas as localizações. Antes que uma licença possa ser atribuída a um usuário, o administrador precisa especificar a propriedade **local de uso** no usuário. No [portal do Azure](https://portal.azure.com), você pode especificar o local de uso nas **configurações**de **perfil** do **usuário** &gt; &gt;.

Para a atribuição de licença de grupo, todos os usuários sem um local de uso especificado herdam o local do diretório. Se você tiver usuários em vários locais, certifique-se de refletir isso corretamente em seus recursos de usuário antes de adicionar usuários a grupos com licenças.

> [!NOTE]
> A atribuição de licença de grupo nunca modificará um valor de local de uso existente em um usuário. É recomendável que você sempre defina o local de uso como parte do fluxo de criação do usuário no Azure AD (por exemplo, por meio da configuração do AAD Connect) – que garantirá que o resultado da atribuição de licença esteja sempre correto e que os usuários não recebam serviços em locais que não são permitidos.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Usar o licenciamento baseado em grupo com grupos dinâmicos

Você pode usar o licenciamento baseado em grupo com qualquer grupo de segurança, o que significa que ele pode ser combinado com grupos dinâmicos do Azure AD. Grupos dinâmicos executam regras em relação aos atributos de recurso do usuário para adicionar e remover automaticamente os usuários de grupos.

Por exemplo, você pode criar um grupo dinâmico para algum conjunto de produtos que deseja atribuir aos usuários. Cada grupo é preenchido por uma regra que adiciona usuários por seus atributos e cada grupo recebe as licenças que você deseja que eles recebam. Você pode atribuir o atributo local e sincronizá-lo com o Azure AD, ou você pode gerenciar o atributo diretamente na nuvem.

As licenças são atribuídas ao usuário logo depois de serem adicionadas ao grupo. Quando o atributo é alterado, o usuário deixa os grupos e as licenças são removidas.

### <a name="example"></a>Exemplo

Considere o exemplo de uma solução de gerenciamento de identidade local que decide quais usuários devem ter acesso aos serviços Web da Microsoft. Ele usa **extensionAttribute1** para armazenar um valor de cadeia de caracteres que representa as licenças que o usuário deve ter. O Azure AD Connect sincroniza com o Azure AD.

Os usuários podem precisar de uma licença, mas não de outra, ou podem precisar de ambas. Aqui está um exemplo, no qual você está distribuindo as licenças do Office 365 Enterprise E5 e do Enterprise Mobility + Security (EMS) para os usuários em grupos:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: serviços base

![Captura de tela dos serviços base do Office 365 Enterprise e5](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: usuários licenciados

![Captura de tela de Enterprise Mobility + Security usuários licenciados](./media/licensing-group-advanced/o365-e5-licensed-users.png)

Para este exemplo, modifique um usuário e defina seu extensionAttribute1 com o valor de `EMS;E5_baseservices;` se você quiser que o usuário tenha ambas as licenças. Você pode fazer essa modificação no local. Depois que a alteração é sincronizada com a nuvem, o usuário é adicionado automaticamente aos dois grupos e as licenças são atribuídas.

![Captura de tela mostrando como definir o extensionAttribute1 do usuário](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Tenha cuidado ao modificar a regra de associação de um grupo existente. Quando uma regra for alterada, a associação do grupo será reavaliada e os usuários que não corresponderem mais à nova regra serão removidos (os usuários que ainda corresponderem à nova regra não serão afetados durante esse processo). Esses usuários terão suas licenças removidas durante o processo, o que pode resultar em perda de serviço ou, em alguns casos, perda de dados.
> 
> Se você tiver um grupo dinâmico grande do qual depende para a atribuição de licença, considere validar quaisquer alterações importantes em um grupo de teste menor antes de aplicá-las ao grupo principal.

## <a name="multiple-groups-and-multiple-licenses"></a>Vários grupos e várias licenças

Um usuário pode ser membro de vários grupos com licenças. Aqui estão algumas coisas a serem consideradas:

- Várias licenças para o mesmo produto podem se sobrepor e fazem com que todos os serviços habilitados sejam aplicados ao usuário. O exemplo a seguir mostra dois grupos de licenciamento: Os *serviços de base E3* contêm os serviços de base para implantar primeiro, para todos os usuários. E os *Serviços estendidos do E3* contêm serviços adicionais (Sway e planejador) para implantar somente para alguns usuários. Neste exemplo, o usuário foi adicionado a ambos os grupos:

  ![Captura de tela de serviços habilitados](./media/licensing-group-advanced/view-enabled-services.png)

  Como resultado, o usuário tem 7 dos 12 serviços no produto habilitado, usando apenas uma licença para este produto.

- A seleção da licença *E3* mostra mais detalhes, incluindo informações sobre quais serviços estão habilitados para o usuário pela atribuição de licença de grupo.

## <a name="direct-licenses-coexist-with-group-licenses"></a>Licenças diretas coexistem com licenças de grupo

Quando um usuário herda uma licença de um grupo, não é possível remover ou modificar diretamente essa atribuição de licença nas propriedades do usuário. As alterações devem ser feitas no grupo e, em seguida, propagadas para todos os usuários.

No entanto, é possível atribuir a mesma licença de produto diretamente ao usuário, além da licença herdada. Você pode habilitar serviços adicionais do produto apenas para um usuário, sem afetar outros usuários.

As licenças atribuídas diretamente podem ser removidas e não afetam as licenças herdadas. Considere o usuário que herda uma licença do Office 365 Enterprise E3 de um grupo.

Inicialmente, o usuário herda a licença somente do grupo de *Serviços do E3 Basic* , que permite quatro planos de serviço.

1. Selecione **atribuir** para atribuir diretamente uma licença E3 ao usuário. Nesse caso, você vai desabilitar todos os planos de serviço, exceto o Yammer Enterprise.

    Como resultado, o usuário ainda usa apenas uma licença do produto E3. Mas a atribuição direta habilita o serviço do Yammer Enterprise somente para esse usuário. Você pode ver quais serviços são habilitados pela Associação de grupo versus a atribuição direta.

1. Quando você usa atribuição direta, as seguintes operações são permitidas:

   - O Yammer Enterprise pode ser desativado diretamente no recurso de usuário. A alternância **Ativar/desativar** na ilustração foi habilitada para esse serviço, em oposição às outras alternâncias de serviço. Como o serviço está habilitado diretamente no usuário, ele pode ser modificado.
   - Serviços adicionais também podem ser habilitados, como parte da licença atribuída diretamente.
   - O botão **remover** pode ser usado para remover a licença direta do usuário. Você pode ver que agora o usuário tem apenas a licença de grupo herdada e somente os serviços originais permanecem habilitados:

## <a name="managing-new-services-added-to-products"></a>Gerenciando novos serviços adicionados aos produtos

Quando o Microsoft adiciona um novo serviço a um plano de licença de produto, ele é habilitado por padrão em todos os grupos aos quais você atribuiu a licença de produto. Os usuários em seu locatário que assinaram notificações sobre alterações de produtos receberão emails antecipadamente notificando-os sobre as próximas adições de serviço.

Como administrador, você pode examinar todos os grupos afetados pela alteração e tomar medidas, como desabilitar o novo serviço em cada grupo. Por exemplo, se você criou grupos destinados apenas a serviços específicos para implantação, poderá revisitar esses grupos e verificar se todos os serviços recém-adicionados estão desabilitados.

Aqui está um exemplo de como esse processo pode ser:

1. Originalmente, você atribuiu o produto *Office 365 Enterprise E5* a vários grupos. Um desses grupos, chamado *O365 E5-Exchange* , foi projetado apenas para habilitar apenas o serviço *Exchange Online (plano 2)* para seus membros.

2. Você recebeu uma notificação da Microsoft de que o produto E5 será estendido com um novo *Microsoft Stream*de serviço. Quando o serviço se tornar disponível em seu locatário, você poderá fazer o seguinte:

3. Vá para a folha [**Azure Active Directory > licenças > todos os produtos**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) e selecione *Office 365 Enterprise E5*e, em seguida, selecione **grupos licenciados** para exibir uma lista de todos os grupos com esse produto.

4. Clique no grupo que você deseja examinar (nesse caso, *O365 E5-Exchange somente*). Isso abrirá a guia **licenças** . Clicar na licença E5 abrirá uma folha listando todos os serviços habilitados.
   > [!NOTE]
   > O serviço de *Microsoft Stream* foi adicionado e habilitado automaticamente neste grupo, além do serviço *Exchange Online* :

   ![Captura de tela do novo serviço adicionado a uma licença de grupo](./media/licensing-group-advanced/manage-new-services.png)

5. Se você quiser desabilitar o novo serviço neste grupo, clique na alternância **Ativar/desativar** ao lado do serviço e clique no botão **salvar** para confirmar a alteração. Agora, o Azure AD processará todos os usuários no grupo para aplicar a alteração; todos os novos usuários adicionados ao grupo não terão o serviço de *Microsoft Stream* habilitado.

   > [!NOTE]
   > Os usuários ainda podem ter o serviço habilitado por meio de alguma outra atribuição de licença (outro grupo do qual eles são membros ou uma atribuição de licença direta).

6. Se necessário, execute as mesmas etapas para outros grupos com este produto atribuído.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Use o PowerShell para ver quem tem licenças herdadas e diretas
Você pode usar um script do PowerShell para verificar se os usuários têm uma licença atribuída diretamente ou herdada de um grupo.

1. Execute o cmdlet `connect-msolservice` para autenticar e conectar-se ao seu locatário.

2. `Get-MsolAccountSku` pode ser usado para descobrir todas as licenças de produtos provisionadas no locatário.

   ![Captura de tela do cmdlet Get-Msolaccountsku](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Use o valor *AccountSkuId* para a licença em que você está interessado com [este script do PowerShell](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Isso produzirá uma lista de usuários que têm essa licença com as informações sobre como a licença é atribuída.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Usar logs de auditoria para monitorar a atividade de licenciamento baseada em grupo

Você pode usar os [logs de auditoria do Azure ad](../reports-monitoring/concept-audit-logs.md#audit-logs) para ver todas as atividades relacionadas ao licenciamento baseado em grupo, incluindo:
- Quem alterou as licenças em grupos
- Quando o sistema iniciou o processamento de uma alteração de licença de grupo e quando ele terminou
- quais alterações de licença foram feitas a um usuário como resultado de uma atribuição de licença de grupo.

>[!NOTE]
> Os logs de auditoria estão disponíveis na maioria das folhas na seção Azure Active Directory do Portal. Dependendo de onde você os acessa, os filtros podem ser aplicados previamente para mostrar apenas atividades relevantes ao contexto da folha. Se você não estiver vendo os resultados esperados, examine [as opções de filtragem](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) ou acesse os logs de auditoria não filtrados em [**Azure Active Directory atividade de > > logs de auditoria**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Descubra quem modificou uma licença de grupo

1. Defina o filtro de **atividade** para *definir a licença de grupo* e clique em **aplicar**.
2. Os resultados incluem todos os casos de licenças que estão sendo definidas ou modificadas em grupos.
   >[!TIP]
   > Você também pode digitar o nome do grupo no filtro de *destino* para o escopo dos resultados.

3. Selecione um item na lista para ver os detalhes do que mudou. Em *propriedades modificadas* , os valores antigo e novo para a atribuição de licença são listados.

Aqui está um exemplo de alterações de licença de grupo recentes, com detalhes:

![Alterações de licença do grupo de captura de tela](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Descubra quando as alterações de grupo iniciaram e concluíram o processamento

Quando uma licença for alterada em um grupo, o Azure AD começará a aplicar as alterações a todos os usuários.

1. Para ver quando os grupos começaram a ser processados, defina o filtro de **atividade** para *começar a aplicar a licença baseada em grupo aos usuários*. Observe que o ator para a operação é *Microsoft Azure ad licenciamento baseado em grupo* -uma conta de sistema que é usada para executar todas as alterações de licença de grupo.
   >[!TIP]
   > Clique em um item na lista para ver o campo *propriedades modificadas* – ele mostra as alterações de licença que foram selecionadas para processamento. Isso será útil se você tiver feito várias alterações em um grupo e não tiver certeza de qual deles foi processado.

2. Da mesma forma, para ver quando os grupos terminaram o processamento, use o valor do filtro *concluir a aplicação da licença baseada em grupo aos usuários*.
   > [!TIP]
   > Nesse caso, o campo *propriedades modificadas* contém um resumo dos resultados – isso é útil para verificar rapidamente se o processamento resultou em erros. Resultado do exemplo:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Para ver o log completo de como um grupo foi processado, incluindo todas as alterações de usuário, defina os seguintes filtros:
   - **Iniciado por (ator)** : "Licenciamento baseado em grupo do Microsoft Azure AD"
   - **Intervalo de datas** (opcional): intervalo personalizado para quando você sabe que um grupo específico iniciou e concluiu o processamento

Essa saída de exemplo mostra o início do processamento, todas as alterações resultantes do usuário e o término do processamento.

![Alterações de licença do grupo de captura de tela](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Clicar em itens relacionados a *alterar licença de usuário* mostrará detalhes de alterações de licença aplicadas a cada usuário individual.

## <a name="deleting-a-group-with-an-assigned-license"></a>Excluindo um grupo com uma licença atribuída

Não é possível excluir um grupo com uma licença ativa atribuída. Um administrador pode excluir um grupo que não percebe que ele fará com que as licenças sejam removidas dos usuários. por esse motivo, exigimos que todas as licenças sejam removidas do grupo primeiro, antes que ele possa ser excluído.

Ao tentar excluir um grupo no portal do Azure, você poderá ver uma notificação de erro como esta: falha na exclusão do grupo ![Screenshot @ no__t-1

Vá para a guia **licenças** no grupo e veja se há licenças atribuídas. Em caso afirmativo, remova essas licenças e tente excluir o grupo novamente.

Você poderá ver erros semelhantes ao tentar excluir o grupo por meio do PowerShell ou API do Graph. Se você estiver usando um grupo sincronizado do local, Azure AD Connect também poderá relatar erros se ele não conseguir excluir o grupo no Azure AD. Em todos esses casos, certifique-se de verificar se há licenças atribuídas ao grupo e removê-las primeiro.

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

Se você usar o licenciamento baseado em grupo, é uma boa ideia se familiarizar com a lista de limitações e problemas conhecidos a seguir.

- Atualmente, o licenciamento baseado em grupo não oferece suporte a grupos que contêm outros grupos (grupos aninhados). Se aplicar uma licença a um grupo aninhado, apenas os membros utilizadores de primeiro nível do grupo têm as licenças aplicadas.

- O recurso só pode ser usado com grupos de segurança e grupos do Office 365 que têm securityEnabled = TRUE.

- O [centro de administração Microsoft 365](https://admin.microsoft.com) atualmente não dá suporte ao licenciamento baseado em grupo. Se um usuário herdar uma licença de um grupo, essa licença aparecerá no portal de administração do Office como uma licença de usuário normal. Se você tentar modificar essa licença ou tentar remover a licença, o portal retornará uma mensagem de erro. As licenças de grupo herdadas não podem ser modificadas diretamente em um usuário.

- Quando as licenças são atribuídas ou modificadas para um grupo grande (por exemplo, 100.000 usuários), isso pode afetar o desempenho. Especificamente, o volume de alterações gerado pela automação do Azure AD pode afetar negativamente o desempenho da sua sincronização de diretórios entre o Azure AD e os sistemas locais.

- Se estiver a utilizar grupos dinâmicos para gerir a associação do seu utilizador, certifique-se de que o utilizador faz parte do grupo, o que é necessário para a atribuição de licenças. Caso contrário, [verifique o estado de processamento para a regra de associação](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) do grupo dinâmico.

- Em certas situações de alta carga, pode levar muito tempo para processar alterações de licença para grupos ou alterações de associação a grupos com licenças existentes. Se você vir que suas alterações levam mais de 24 horas para processar o tamanho do grupo de usuários 60K ou menos, [abra um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) para nos permitir investigar. 

- A automação do gerenciamento de licenças não reage automaticamente a todos os tipos de alterações no ambiente. Por exemplo, você pode estar sem licenças, fazendo com que alguns usuários estejam em um estado de erro. Para liberar a contagem de estações disponível, você pode remover algumas licenças atribuídas diretamente de outros usuários. No entanto, o sistema não reage automaticamente a essa alteração e corrige os usuários nesse estado de erro.

  Como uma solução alternativa para esses tipos de limitações, você pode ir para a folha **grupo** no Azure AD e clicar em **Reprocessar**. Esse comando processa todos os usuários nesse grupo e resolve os Estados de erro, se possível.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre outros cenários para gestão de licenças através do licenciamento baseado no grupo, veja:

* [O que é o licenciamento baseado em grupo no Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Atribuir licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
* [Como migrar os utilizadores licenciados individuais para o licenciamento baseado no grupo no Azure Active Directory](licensing-groups-migrate-users.md)
* [Como migrar os utilizadores entre licenças de produto através do licenciamento com o botão com base em grupo no Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Exemplos do PowerShell para licenciamento com o botão com base em grupo no Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
