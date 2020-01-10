---
title: Organizar seus recursos com grupos de gerenciamento – governança do Azure
description: Saiba mais sobre os grupos de gestão, como as permissões destes funcionam e como utilizá-los.
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.date: 12/18/2019
ms.topic: overview
ms.openlocfilehash: 507f4575e6d8daa16a1ed7db3d429d2810a63a7c
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750249"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizar recursos com os grupos de gestão do Azure

Se a sua organização tiver várias subscrições, poderá precisar de uma forma de gerir eficazmente o acesso, as políticas e a conformidade para essas subscrições. Os grupos de gestão do Azure fornecem um nível de âmbito acima das subscrições. Estes permitem-lhe organizar as subscrições em contentores chamados "grupos de gestão" e aplicar as suas condições de governação aos grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as condições aplicadas ao grupo de gestão. Os grupos de gestão dão-lhe capacidades de gestão de nível empresarial em grande escala, independentemente do seu tipo de subscrição. Todas as subscrições num único grupo de gestão têm de confiar no mesmo inquilino do Azure Active Directory.

Por exemplo, pode aplicar políticas a um grupo de gestão que limita as regiões disponíveis para a criação de máquinas virtuais (VM). Esta política seria aplicada a todos os grupos de gestão, subscrições e recursos existentes nesse grupo de gestão ao apenas permitir a criação de VMs nessa região.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarquia de grupos de gestão e de subscrições

Pode criar uma estrutura flexível de grupos de gestão e de subscrições para organizar os seus recursos numa hierarquia para assegurar uma gestão unificada de acesso e política. O diagrama seguinte mostra um exemplo de criação de uma hierarquia de governação com grupos de gestão.

![Exemplo de uma árvore hierárquica do grupo de gestão](./media/tree.png)

Pode criar uma hierarquia que se aplique a uma política, para, por exemplo, limitar as localizações das VMs à Região E.U.A. Oeste no grupo com o nome "Produção". Essa política será herdada para todas as assinaturas de EA que são descendentes desse grupo de gerenciamento e serão aplicadas a todas as VMs nessas assinaturas. Esta política de segurança não pode ser alterada pelo proprietário do recurso ou da subscrição, o que permite uma melhor governação.

Outro cenário em que utilizaria os grupos de gestão seria para fornecer acesso de utilizador a várias subscrições. Ao mover várias subscrições para esse grupo de gestão, pode criar uma atribuição de [controlo de acesso baseado em funções](../../role-based-access-control/overview.md) (RBAC) no grupo de gestão, o que faz com que todas as subscrições herdem esse acesso.
Uma única atribuição no grupo de gestão pode permitir que os utilizadores tenham acesso a tudo o que precisam, sem a necessidade de criar scripts de RBAC para diferentes subscrições.

### <a name="important-facts-about-management-groups"></a>Factos importantes sobre os grupos de gestão

- Um único diretório suporta 10 000 grupos de gestão.
- Uma árvore de grupo de gestão pode suportar até seis níveis de profundidade.
  - Este limite não inclui o nível da Raiz ou o nível da subscrição.
- Cada grupo de gestão e subscrição só podem suportar um elemento principal.
- Cada grupo de gestão pode ter vários elementos subordinados.
- Todos os grupos de gestão e subscrições estão contidos numa única hierarquia em cada diretório. Veja [Factos importantes sobre o grupo de gestão de Raiz](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Grupo de gestão de raiz para cada diretório

É atribuído a cada diretório um único grupo de gestão de nível superior denominado grupo de gestão de "Raiz".
Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. Este grupo de gestão de raiz permite a aplicação de políticas globais e atribuições do RBAC ao nível do diretório. Inicialmente, o [Administrador Global do Azure AD tem de se elevar](../../role-based-access-control/elevate-access-global-admin.md) à função de Administrador de Acesso de Utilizador deste grupo de raiz. Após elevar esse acesso, o administrador pode atribuir qualquer função RBAC a outros utilizadores do diretório ou grupos para gerir a hierarquia. Enquanto administrador, pode atribuir à sua própria conta a função de proprietário do grupo de gestão de raiz.

### <a name="important-facts-about-the-root-management-group"></a>Factos importantes sobre o grupo de gestão de Raiz

- Por predefinição, o nome a apresentar do grupo de gestão de raiz é **Grupo de raiz do inquilino**. O ID é o ID do Azure Active Directory.
- Para alterar o nome a apresentar, a sua conta tem de estar atribuída à função de Proprietário ou Contribuinte no grupo de gestão de raiz. Consulte [alterar o nome de um grupo de gerenciamento](manage.md#change-the-name-of-a-management-group) para atualizar o nome de um grupo de gerenciamento.
- O grupo de gestão de raiz não pode ser movido nem eliminado, ao contrário de outros grupos de gestão.  
- Todos os grupos de gestão e subscrições ficam associados ao grupo de gestão de raiz dentro do diretório.
  - Todos os recursos no diretório ficam associados ao grupo de gestão de raiz para gestão global.
  - Quando são criadas, as novas subscrições são colocadas automaticamente por predefinição no grupo de gestão de raiz.
- Todos os clientes do Azure podem ver o grupo de gestão de raiz, mas nem todos os clientes têm acesso para gerir esse mesmo grupo de gestão de raiz.
  - Todos os utilizadores com acesso a uma subscrição podem ver o contexto em que essa subscrição se insere na hierarquia.  
  - Ninguém recebe acesso predefinido ao grupo de gestão de raiz. Os Administradores Globais do Azure AD são os únicos utilizadores que se podem elevar para obter acesso.  Assim que tiverem acesso ao grupo de gestão de raiz, os administradores globais podem atribuir qualquer função RBAC a outros utilizadores para os encarregar da gestão.  

> [!IMPORTANT]
> Qualquer atribuição de acesso de utilizador ou atribuição de política no grupo de gestão de raiz **aplica-se a todos os recursos dentro do diretório**.
> Por este motivo, todos os clientes devem avaliar a necessidade de ter itens definidos neste âmbito.
> O acesso de utilizador e as atribuições de política devem ser "Obrigatório" apenas neste âmbito.  

## <a name="initial-setup-of-management-groups"></a>Configuração inicial dos grupos de gestão

Quando um utilizador começa a utilizar grupos de gestão, ocorre um processo de configuração inicial. Na primeira etapa, o grupo de gestão de raiz é criado no diretório. Uma vez criado este grupo, todas as subscrições existentes no diretório tornam-se elementos subordinados do grupo de gestão de raiz. Este processo existe para garantir que existe apenas uma hierarquia de grupo de gestão num diretório. A única hierarquia dentro do diretório permite aos clientes administrativos aplicar o acesso global e políticas que os outros clientes não podem ignorar. Todas as atribuições feitas ao nível da raiz serão aplicadas a toda a hierarquia, o que inclui todos os grupos de gestão, subscrições, grupos de recursos e recursos dentro desse Inquilino do Azure AD.

## <a name="trouble-seeing-all-subscriptions"></a>Problemas ao ver todas as subscrições

Alguns diretórios que começaram a utilizar os grupos de gestão numa fase inicial da pré-visualização, antes de 25 de junho de 2018, podem ter um problema em que nem todas as subscrições estão dentro da hierarquia. O processo que permite incluir todas as subscrições na hierarquia foram implementados após uma atribuição de função ou política realizada no grupo de gestão de raiz no diretório. 

### <a name="how-to-resolve-the-issue"></a>Como resolver o problema

Existem duas opções para resolver este problema.

1. Remover todas as atribuições de Função e de Política do grupo de gestão de raiz
   1. Ao remover quaisquer atribuições de política e de função do grupo de gestão de raiz, o serviço preenche quaisquer subscrições na hierarquia para o ciclo do próximo dia.  Este processo consiste em garantir que não é concedido acesso de forma acidente ou uma atribuição de política a todas as subscrições de inquilinos.
   1. A melhora forma de realizar este processo sem afetar os seus serviços consiste em basicamente aplicar as atribuições de função ou de política um nível abaixo do Grupo de gestão de raiz. Então pode remover todas as atribuições do âmbito de raiz.
1. Chame a API diretamente para iniciar o processo de preenchimento
   1. Qualquer cliente no diretório pode chamar as APIs *TenantBackfillStatusRequest* ou *StartTenantBackfillRequest*. Quando a API StartTenantBackfillRequest é chamada, lança o processo inicial de configuração de mover todas as subscrições para a hierarquia. Este processo também inicia a imposição de todas as novas subscrições como sendo um filho do grupo de gestão de raiz. Este processo pode ser feito sem alterar as atribuições no nível de raiz. Ao chamar a API, está a indicar que não há problema em que qualquer atribuição de política ou acesso na raiz seja aplicada a todas as subscrições.

Se tiver dúvidas sobre este processo de preenchimento, contacte: managementgroups@microsoft.com  
  
## <a name="management-group-access"></a>Acesso de grupo de gestão

Os grupos de gestão do Azure suportam o [Controlo de Acesso Baseado em Funções (RBAC)](../../role-based-access-control/overview.md) para todos os acessos de recursos e definições de função.
Estas permissões são herdadas pelos recursos subordinados existentes na hierarquia. Qualquer função de RBAC pode ser atribuída a um grupo de gerenciamento que herdará a hierarquia para os recursos.
Por exemplo, a função RBAC de contribuidor de VM pode ser atribuída a um grupo de gestão. Esta função não desempenha qualquer ação no grupo de gestão, mas será herdada por todas as VMs nesse grupo de gestão.

A tabela seguinte mostra a lista de funções e as ações suportadas nos grupos de gestão.

| Nome da Função RBAC             | Create | Mudar o nome | Mover** | Eliminar | Atribuir Acesso | Atribuir Política | Leitura  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|Proprietário                       | X      | X      | X      | X      | X             | X             | X     |
|Contribuinte                 | X      | X      | X      | X      |               |               | X     |
|Contribuidor MG*             | X      | X      | X      | X      |               |               | X     |
|Leitor                      |        |        |        |        |               |               | X     |
|Leitor MG*                  |        |        |        |        |               |               | X     |
|Contribuidor de Política de Recursos |        |        |        |        |               | X             |       |
|Administrador de Acesso de Utilizador   |        |        |        |        | X             | X             |       |

*: As funções Contribuidor MG e Leitor MG apenas permitem aos utilizadores realizar essas ações no âmbito do grupo de gestão.  
\* *: As atribuições de função no grupo de gerenciamento raiz não são necessárias para mover uma assinatura ou grupo de gerenciamento de e para ela.  Veja [Manage your resources with management groups](manage.md) (Gerir os recursos com grupos de gestão) para obter detalhes sobre como mover itens dentro da hierarquia.

## <a name="custom-rbac-role-definition-and-assignment"></a>Definição e atribuição de função RBAC personalizadas

O suporte à função RBAC personalizada para grupos de gerenciamento tem suporte atualmente com algumas [limitações](#limitations).  Você pode definir o escopo do grupo de gerenciamento no escopo atribuível da definição de função.  Essa função personalizada de RBAC estará disponível para atribuição nesse grupo de gerenciamento e qualquer grupo de gerenciamento, assinatura, grupo de recursos ou recurso sob ele. Essa função personalizada herdará a hierarquia, como qualquer função interna.    

### <a name="example-definition"></a>Exemplo de definição
A [definição e a criação de uma função personalizada](../../role-based-access-control/custom-roles.md) não são alteradas com a inclusão de grupos de gerenciamento. Use o caminho completo para definir o grupo de gerenciamento **/Providers/Microsoft.Management/managementgroups/{GroupId}** . 

Use a ID do grupo de gerenciamento e não o nome de exibição do grupo de gerenciamento. Esse erro comum ocorre porque ambos são campos definidos personalizados ao criar um grupo de gerenciamento. 

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>Problemas com a interrupção da definição de função e o caminho da hierarquia de atribuição
As definições de função são um escopo atribuível em qualquer lugar dentro da hierarquia do grupo de gerenciamento. Uma definição de função pode ser definida em um grupo de gerenciamento pai enquanto a atribuição de função real existe na assinatura filho. Como há uma relação entre os dois itens, você receberá um erro ao tentar separar a atribuição de sua definição. 

Por exemplo: Vamos examinar uma pequena seção de uma hierarquia para um Visual. 

![subárvore](./media/subtree.png)

Digamos que haja uma função personalizada definida no grupo de gerenciamento de marketing. Essa função personalizada é então atribuída nas duas assinaturas de avaliação gratuitas.  

Se tentarmos mover uma dessas assinaturas para ser um filho do grupo de gerenciamento de produção, essa mudança quebraria o caminho da atribuição de função de assinatura para a definição de função do grupo de gerenciamento de marketing. Nesse cenário, você receberá um erro informando que a movimentação não é permitida, pois ela interromperá essa relação.  

Há algumas opções diferentes para corrigir esse cenário:
- Remova a atribuição de função da assinatura antes de mover a assinatura para uma nova MG pai.
- Adicione a assinatura ao escopo atribuível da definição de função.
- Altere o escopo atribuível dentro da definição de função. No exemplo acima, você pode atualizar os escopos atribuíveis do marketing para o grupo de gerenciamento raiz para que a definição possa ser alcançada por ambas as ramificações da hierarquia.   
- Crie uma função personalizada adicional que será definida na outra ramificação.  Essa nova função exigirá que a atribuição de função seja alterada também na assinatura.  

### <a name="limitations"></a>Limitações  
Há limitações que existem ao usar funções personalizadas em grupos de gerenciamento. 

 - Você só pode definir um grupo de gerenciamento nos escopos atribuíveis de uma nova função.  Essa limitação está em vigor para reduzir o número de situações em que as definições de função e as atribuições de função são desconectadas.  Isso acontece quando uma assinatura ou grupo de gerenciamento com uma atribuição de função é movido para um pai diferente que não tem a definição de função.   
 - As ações do plano de dados RBAC não podem ser definidas nas funções personalizadas do grupo de gerenciamento.  Essa restrição está em vigor, pois há um problema de latência com ações RBAC atualizando os provedores de recursos do plano de dados. Esse problema de latência está sendo trabalhado e essas ações serão desabilitadas da definição de função para reduzir os riscos.
 - O Azure Resource Manager não valida a existência do grupo de gerenciamento no escopo atribuível da definição de função.  Se houver uma ID de grupo de gerenciamento de digitação ou incorreta listada, a definição de função ainda será criada.   

## <a name="moving-management-groups-and-subscriptions"></a>Movendo grupos de gerenciamento e assinaturas 

Para que um grupo de gerenciamento ou assinatura seja um filho de outro grupo de gerenciamento, três regras precisam ser avaliadas como true.

Se você estiver fazendo a ação mover, precisará de: 

-  Permissões de gravação de gravação de grupo de gerenciamento e atribuição de função na assinatura ou grupo de gerenciamento filho.
   - **Proprietário** de exemplo de função interna
- Acesso de gravação do grupo de gerenciamento no grupo de gerenciamento pai de destino.
   - Exemplo de função interna: **proprietário**, **colaborador**, colaborador do **grupo de gerenciamento**
- Acesso de gravação do grupo de gerenciamento no grupo de gerenciamento pai existente.
   - Exemplo de função interna: **proprietário**, **colaborador**, colaborador do **grupo de gerenciamento**

**Exceção**: se o destino ou o grupo de gerenciamento pai existente for o grupo de gerenciamento raiz, os requisitos de permissões não se aplicarão. Como o grupo de gerenciamento raiz é o ponto de aterrissagem padrão para todos os novos grupos de gerenciamento e assinaturas, você não precisa de permissões para mover um item.

Se a função de proprietário na assinatura for herdada do grupo de gerenciamento atual, seus destinos de movimentação serão limitados. Você só pode mover a assinatura para outro grupo de gerenciamento no qual você tem a função de proprietário. Você não pode movê-lo para um grupo de gerenciamento em que você é um colaborador, pois você perderia a propriedade da assinatura. Se você estiver diretamente atribuído à função de proprietário da assinatura (não herdada do grupo de gerenciamento), poderá movê-la para qualquer grupo de gerenciamento no qual você seja um colaborador. 

## <a name="audit-management-groups-using-activity-logs"></a>Auditar os grupos de gestão que utilizam registos de atividades

Os grupos de gestão são suportados no [Registo de Atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md). Pode procurar todos os eventos que acontecem a um grupo de gestão na mesma localização central de outros recursos do Azure.  Por exemplo, pode ver todas as alterações de Atribuições de Funções ou de Atribuição de Política feitas a um grupo de gestão específico.

![Registos de atividades com Grupos de Gestão](media/al-mg.png)

Quando quiser consultar Grupos de Gestão fora do portal do Azure, o âmbito de destino dos grupos de gestão é semelhante a **"/ providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os grupos de gestão, veja:

- [Criar grupos de gestão para organizar recursos do Azure](create.md)
- [Como alterar, eliminar ou gerir os seus grupos de gestão](manage.md)
- [Rever os grupos de gestão no Módulo de Recursos do Azure PowerShell](/powershell/module/az.resources#resources)
- [Rever os grupos de gestão na API REST](/rest/api/resources/managementgroups)
- [Rever os grupos de gestão na CLI do Azure](/cli/azure/account/management-group)
