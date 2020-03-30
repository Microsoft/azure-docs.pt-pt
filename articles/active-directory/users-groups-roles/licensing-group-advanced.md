---
title: Cenários adicionais de licenciamento baseados em grupo - Azure AD / Microsoft Docs
description: Mais cenários para licenciamento baseado em grupo azure Ative Diretório
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139d7e0cf2b57cc466dc97370b90a599257ce755
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266289"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Cenários, limitações e questões conhecidas usando grupos para gerir licenciamento no Diretório Ativo Azure

Utilize as seguintes informações e exemplos para obter uma compreensão mais avançada do licenciamento baseado em grupo azure Ative Directory (Azure AD).

## <a name="usage-location"></a>Localização de utilização

Alguns serviços Microsoft não estão disponíveis em todas as localizações. Antes de uma licença poder ser atribuída a um utilizador, o administrador tem de especificar a propriedade de **localização de utilização** no utilizador. No [portal Azure,](https://portal.azure.com)pode especificar a localização de utilização nas **Definições**de **Perfil** &gt; **do Utilizador** &gt; .

Para a atribuição de licença de grupo, quaisquer utilizadores sem localização de utilização especificada herdam a localização do diretório. Se tiver utilizadores em vários locais, certifique-se de que reflete isso corretamente nos recursos dos seus utilizadores antes de adicionar utilizadores a grupos com licenças.

> [!NOTE]
> A atribuição de licença seletiva nunca modificará o valor de localização de utilização existente num utilizador. Recomendamos que detetete sempre a localização de utilização como parte do fluxo de criação do utilizador em Azure AD (por exemplo, através da configuração AAD Connect) - que garanta que o resultado da atribuição da licença está sempre correto e que os utilizadores não recebem serviços em locais que não são permitidos.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Utilize licenciamento baseado em grupo com grupos dinâmicos

Você pode usar licenciamento baseado em grupo com qualquer grupo de segurança, o que significa que pode ser combinado com grupos dinâmicos Azure AD. Os grupos dinâmicos executam regras contra os atributos de recursos do utilizador para adicionar e remover automaticamente os utilizadores de grupos.

Por exemplo, pode criar um grupo dinâmico para alguns conjuntos de produtos que pretende atribuir aos utilizadores. Cada grupo é povoado por uma regra que adiciona os utilizadores pelos seus atributos, e cada grupo é atribuído às licenças que pretende que recebam. Pode atribuir o atributo no local e sincronizá-lo com a AD Azure, ou pode gerir o atributo diretamente na nuvem.

As licenças são atribuídas ao utilizador pouco depois de serem adicionadas ao grupo. Quando o atributo é alterado, o utilizador deixa os grupos e as licenças são removidas.

### <a name="example"></a>Exemplo

Considere o exemplo de uma solução de gestão de identidade no local que decida quais os utilizadores que devem ter acesso aos serviços web da Microsoft. Utiliza **extensõesAttribute1** para armazenar um valor de cadeia que represente as licenças que o utilizador deve ter. O Azure AD Connect sincroniza-o com o Azure AD.

Os utilizadores podem precisar de uma licença, mas não de outra, ou podem precisar de ambas. Aqui está um exemplo, no qual está a distribuir licenças do Office 365 Enterprise E5 e da Enterprise Mobility + Security (EMS) aos utilizadores em grupos:

#### <a name="office-365-enterprise-e5-base-services"></a>Escritório 365 Enterprise E5: serviços de base

![Screenshot dos serviços base da Enterprise E5 do Office 365](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Mobilidade Empresarial + Segurança: utilizadores licenciados

![Screenshot da Enterprise Mobility + Security utilizadores licenciados](./media/licensing-group-advanced/o365-e5-licensed-users.png)

Para este exemplo, modifique um utilizador e defino `EMS;E5_baseservices;` a sua extensãoAttribute1 ao valor de se pretender que o utilizador tenha ambas as licenças. Pode fazer esta modificação no local. Após a alteração sincronizar com a nuvem, o utilizador é automaticamente adicionado a ambos os grupos e as licenças são atribuídas.

![Screenshot mostrando como definir a extensão do utilizadorAttribute1](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Tenha cuidado ao modificar a regra de adesão de um grupo existente. Quando uma regra for alterada, a adesão ao grupo será reavaliada e os utilizadores que deixarem de corresponder à nova regra serão removidos (os utilizadores que ainda correspondam à nova regra não serão afetados durante este processo). Estes utilizadores terão as suas licenças removidas durante o processo, o que pode resultar na perda de serviço, ou em alguns casos, na perda de dados.
> 
> Se tiver um grande grupo dinâmico de que depende para a atribuição de licença, considere validar quaisquer alterações importantes num grupo de teste menor antes de as aplicar ao grupo principal.

## <a name="multiple-groups-and-multiple-licenses"></a>Múltiplos grupos e várias licenças

Um utilizador pode ser membro de vários grupos com licenças. Eis algumas coisas a considerar:

- Várias licenças para o mesmo produto podem sobrepor-se, e resultam na aplicação de todos os serviços habilitados ao utilizador. O exemplo que se segue mostra dois grupos de licenciamento: os *serviços de base E3* contêm os serviços de fundação para implantar primeiro, para todos os utilizadores. E *os serviços alargados E3* contêm serviços adicionais (Sway e Planner) para implantar apenas em alguns utilizadores. Neste exemplo, o utilizador foi adicionado a ambos os grupos:

  ![Screenshot dos serviços habilitados](./media/licensing-group-advanced/view-enabled-services.png)

  Como resultado, o utilizador tem 7 dos 12 serviços do produto ativados, enquanto utiliza apenas uma licença para este produto.

- A seleção da licença *E3* mostra mais detalhes, incluindo informações sobre quais os serviços que estão habilitados para o utilizador através da atribuição de licença de grupo.

## <a name="direct-licenses-coexist-with-group-licenses"></a>Licenças diretas coexistem com licenças de grupo

Quando um utilizador herda uma licença de um grupo, não pode remover ou modificar diretamente essa atribuição de licença nas propriedades do utilizador. As alterações devem ser feitas no grupo e depois propagadas a todos os utilizadores.

É possível, no entanto, atribuir a mesma licença de produto diretamente ao utilizador, além da licença herdada. Pode ativar serviços adicionais do produto apenas para um utilizador, sem afetar outros utilizadores.

As licenças diretamente atribuídas podem ser removidas e não afetam as licenças herdadas. Considere o utilizador que herda uma licença Do Office 365 Enterprise E3 de um grupo.

Inicialmente, o utilizador herda a licença apenas do grupo de *serviços básicos E3,* que permite quatro planos de serviço.

1. Selecione **Atribuir** para atribuir diretamente uma licença E3 ao utilizador. Neste caso, vaides desativar todos os planos de serviço, exceto a Yammer Enterprise.

    Como resultado, o utilizador ainda utiliza apenas uma licença do produto E3. Mas a atribuição direta permite o serviço Yammer Enterprise apenas para esse utilizador. Pode ver quais os serviços que são ativados pela associação do grupo contra a atribuição direta.

1. Quando utilizar a atribuição direta, são permitidas as seguintes operações:

   - A Yammer Enterprise pode ser desligada diretamente do recurso do utilizador. O toggle **On/Off** na ilustração foi ativado para este serviço, em oposição aos outros toggles de serviço. Uma vez que o serviço está ativado diretamente no utilizador, pode ser modificado.
   - Serviços adicionais também podem ser ativados, como parte da licença diretamente atribuída.
   - O botão **Remover** pode ser utilizado para remover a licença direta do utilizador. Pode ver que o utilizador tem agora apenas a licença de grupo herdada e apenas os serviços originais permanecem ativados:

## <a name="managing-new-services-added-to-products"></a>Gestão de novos serviços adicionados aos produtos

Quando a Microsoft adiciona um novo serviço a um plano de licença de produto, é ativado por padrão em todos os grupos aos quais atribuiu a licença do produto. Os utilizadores do seu inquilino que estejam subscritos a notificações sobre alterações de produto receberão e-mails com antecedência notificando-os sobre as próximas adições de serviço.

Como administrador, pode rever todos os grupos afetados pela mudança e tomar medidas, tais como desativar o novo serviço em cada grupo. Por exemplo, se criou grupos direcionados apenas para serviços específicos para implantação, pode revisitar esses grupos e certificar-se de que quaisquer serviços recém-adicionados são desativados.

Aqui está um exemplo do que este processo pode parecer:

1. Originalmente, atribuiu o produto *Office 365 Enterprise E5* a vários grupos. Um desses grupos, chamado *O365 E5 - Exchange apenas* foi projetado para permitir apenas o serviço *Exchange Online (Plano 2)* para os seus membros.

2. Recebeu uma notificação da Microsoft de que o produto E5 será estendido com um novo serviço - *Microsoft Stream*. Quando o serviço estiver disponível no seu inquilino, pode fazer o seguinte:

3. Vá ao [**Diretório Ativo Azure > Licenças > Todas as**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) lâminas de produtos e selecione Office *365 Enterprise E5,* em seguida, selecione **Grupos Licenciados** para ver uma lista de todos os grupos com esse produto.

4. Clique no grupo que pretende rever (neste caso, *O365 E5 - Apenas em troca).* Isto abrirá o separador **Licenças.** Clicar na licença E5 abrirá uma lâmina com todos os serviços habilitados.
   > [!NOTE]
   > O serviço *Microsoft Stream* foi automaticamente adicionado e ativado neste grupo, além do serviço *Exchange Online:*

   ![Screenshot de novo serviço adicionado a uma licença de grupo](./media/licensing-group-advanced/manage-new-services.png)

5. Se pretender desativar o novo serviço neste grupo, clique no toggle **On/Off** ao lado do serviço e clique no botão **Guardar** para confirmar a alteração. A Azure AD irá agora processar todos os utilizadores do grupo para aplicar a alteração; quaisquer novos utilizadores adicionados ao grupo não terão o serviço *Microsoft Stream* ativado.

   > [!NOTE]
   > Os utilizadores podem ainda ter o serviço ativado através de outra atribuição de licença (outro grupo de que são membros ou uma atribuição de licença direta).

6. Se necessário, execute os mesmos passos para outros grupos com este produto atribuído.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Use powerShell para ver quem herdou e licenças diretas
Pode utilizar um script PowerShell para verificar se os utilizadores têm uma licença atribuída diretamente ou herdada de um grupo.

1. Execute `connect-msolservice` o cmdlet para autenticar e ligar-se ao seu inquilino.

2. `Get-MsolAccountSku`pode ser usado para descobrir todas as licenças de produto provisionadas no inquilino.

   ![Screenshot do Get-Msolaccountsku cmdlet](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Utilize o valor *AccountSkuId* para a licença em que está interessado neste [script PowerShell](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Isto produzirá uma lista de utilizadores que possuem esta licença com a informação sobre como a licença é atribuída.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Utilize registos de auditoria para monitorizar a atividade de licenciamento baseada em grupo

Pode utilizar registos de [auditoria da Azure AD](../reports-monitoring/concept-audit-logs.md#audit-logs) para ver toda a atividade relacionada com licenciamento baseado em grupo, incluindo:
- que mudaram licenças em grupos
- quando o sistema começou a processar uma mudança de licença de grupo, e quando terminou
- que alterações de licença foram feitas a um utilizador como resultado de uma atribuição de licença de grupo.

>[!NOTE]
> Os registos de auditoria estão disponíveis na maioria das lâminas da secção de Diretório Ativo Azure do portal. Dependendo do local onde acede, os filtros podem ser pré-aplicados para mostrar apenas atividade relevante para o contexto da lâmina. Se não estiver a ver os resultados que espera, examine [as opções de filtragem](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) ou aceda aos registos de auditoria não filtrados no âmbito do [**Diretório Ativo do Azure > Registos**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit)de Auditoria > Auditoria .

### <a name="find-out-who-modified-a-group-license"></a>Descubra quem modificou uma licença de grupo

1. Delineie o filtro **de atividade** para definir a *licença de grupo* e clique em **Aplicar**.
2. Os resultados incluem todos os casos de licenças definidas ou modificadas em grupos.
   >[!TIP]
   > Também pode digitar o nome do grupo no filtro *Target* para ver os resultados.

3. Selecione um item na lista para ver os detalhes do que mudou. No âmbito *das Propriedades Modificadas* estão listados valores antigos e novos para a atribuição da licença.

Aqui está um exemplo de recentes alterações na licença de grupo, com detalhes:

![Alterações na licença do grupo screenshot](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Saiba quando as mudanças de grupo começaram e terminou o processamento

Quando uma licença muda num grupo, a Azure AD começará a aplicar as alterações a todos os utilizadores.

1. Para ver quando os grupos começaram a processar, delineie o filtro **De Atividade** para *começar a aplicar licença baseada em grupo aos utilizadores*. Note que o ator para a operação é o *Licenciamento baseado no Grupo Microsoft Azure AD* - uma conta de sistema que é usada para executar todas as alterações de licença de grupo.
   >[!TIP]
   > Clique num item na lista para ver o campo *Propriedades Modificadas* - mostra as alterações de licença que foram recolhidas para processamento. Isto é útil se fizer várias alterações num grupo e não tiver a certeza de qual foi processado.

2. Da mesma forma, para ver quando os grupos terminaram o processamento, utilize o valor do filtro *Acabamento aplicando licença baseada em grupo aos utilizadores*.
   > [!TIP]
   > Neste caso, o campo *Propriedades Modificadas* contém um resumo dos resultados - isto é útil para verificar rapidamente se o processamento resultou em algum erro. Resultado do exemplo:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Para ver o registo completo de como um grupo foi processado, incluindo todas as alterações do utilizador, detetete os seguintes filtros:
   - **Iniciado por (Ator)**: "Licenciamento baseado em grupo microsoft Azure AD"
   - **Gama de Datas** (opcional): gama personalizada para quando souber que um grupo específico começou e terminou o processamento

Esta saída da amostra mostra o início do processamento, todas as alterações resultantes do utilizador e o fim do processamento.

![Alterações na licença do grupo screenshot](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Clicar em itens relacionados com a licença de *utilizador Alterar* mostrará detalhes sobre as alterações de licença aplicadas a cada utilizador individual.

## <a name="deleting-a-group-with-an-assigned-license"></a>Eliminar um grupo com uma licença atribuída

Não é possível eliminar um grupo com uma licença ativa atribuída. Um administrador poderia eliminar um grupo que não percebesse que irá fazer com que as licenças sejam removidas dos utilizadores - por isso exigimos que quaisquer licenças sejam removidas do grupo primeiro, antes de poderem ser eliminadas.

Ao tentar apagar um grupo no portal Azure pode ver ![uma notificação de erro como esta: A eliminação do grupo Screenshot falhou](./media/licensing-group-advanced/groupdeletionfailed.png)

Vá ao separador **Licenças** do grupo e veja se há licenças atribuídas. Se sim, retire as licenças e tente apagar o grupo novamente.

Pode ver erros semelhantes ao tentar eliminar o grupo através da PowerShell ou da API graph. Se estiver a utilizar um grupo sincronizado a partir do local, o Azure AD Connect também pode reportar erros se não conseguir eliminar o grupo em Azure AD. Em todos estes casos, certifique-se de verificar se existem licenças atribuídas ao grupo, e remova-as primeiro.

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

Se utilizar o licenciamento baseado em grupo, é uma boa ideia familiarizar-se com a seguinte lista de limitações e questões conhecidas.

- O licenciamento baseado em grupo não suporta, atualmente, grupos que contêm outros grupos (grupos aninhados). Se aplicar uma licença a um grupo aninhado, apenas os membros utilizadores de primeiro nível do grupo têm as licenças aplicadas.

- A funcionalidade só pode ser utilizada com grupos de segurança e grupos do Office 365 que têm segurançaEnabled=TRUE.

- O centro de administração microsoft [365](https://admin.microsoft.com) não suporta atualmente o licenciamento baseado em grupo. Se um utilizador herdar uma licença de um grupo, esta licença aparece no portal de administração do Office como uma licença de utilizador regular. Se tentar modificar essa licença ou tentar remover a licença, o portal devolve uma mensagem de erro. As licenças de grupo herdadas não podem ser modificadas diretamente num utilizador.

- Quando as licenças são atribuídas ou modificadas para um grande grupo (por exemplo, 100.000 utilizadores), pode ter impacto no desempenho. Especificamente, o volume de alterações geradas pela automatização da AD Azure pode ter um impacto negativo no desempenho da sincronização do seu diretório entre os sistemas Azure AD e on-premises.

- Se estiver a utilizar grupos dinâmicos para gerir a associação do seu utilizador, certifique-se de que o utilizador faz parte do grupo, o que é necessário para a atribuição de licenças. Caso contrário, [verifique o estado de processamento para a regra de associação](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) do grupo dinâmico.

- Em determinadas situações de alta carga, pode levar muito tempo para processar alterações de licença para grupos ou alterações de membros em grupos com licenças existentes. Se você ver suas alterações demorar mais de 24 horas para processar o tamanho do grupo de utilizadores de 60K ou menos, por favor [abram um bilhete](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) de suporte para nos permitir investigar. 

- A automatização da gestão de licenças não reage automaticamente a todos os tipos de alterações no ambiente. Por exemplo, pode ter ficado sem licenças, fazendo com que alguns utilizadores estejam em estado de erro. Para libertar a contagem de lugares disponível, pode remover algumas licenças diretamente atribuídas a outros utilizadores. No entanto, o sistema não reage automaticamente a esta alteração e corrige os utilizadores nesse estado de erro.

  Como sutique para este tipo de limitações, pode ir à lâmina **do Grupo** em Azure AD e clicar em **Reprocessar**. Este comando processa todos os utilizadores desse grupo e resolve os estados de erro, se possível.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre outros cenários para gestão de licenças através do licenciamento baseado no grupo, veja:

* [O que é o licenciamento baseado em grupo no Azure Ative Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Atribuir licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
* [Como migrar os utilizadores licenciados individuais para o licenciamento baseado no grupo no Azure Active Directory](licensing-groups-migrate-users.md)
* [Como migrar os utilizadores entre licenças de produtos utilizando licenciamento baseado em grupo no Diretório Ativo azure](../users-groups-roles/licensing-groups-change-licenses.md)
* [Exemplos da PowerShell para licenciamento baseado em grupo no Diretório Ativo Azure](../users-groups-roles/licensing-ps-examples.md)
