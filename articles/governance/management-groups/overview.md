---
title: Organize os seus recursos com grupos de gestão - Azure Governance
description: Saiba mais sobre os grupos de gestão, como as permissões destes funcionam e como utilizá-los.
ms.date: 01/22/2021
ms.topic: overview
ms.custom: contperf-fy21q1
ms.openlocfilehash: 9843b824c62e0f9919d7a71a717d9aad03dcbd9c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590834"
---
# <a name="what-are-azure-management-groups"></a>O que são os grupos de gestão do Azure?

Se a sua organização tiver várias subscrições, poderá precisar de uma forma de gerir eficazmente o acesso, as políticas e a conformidade para essas subscrições. Os grupos de gestão do Azure fornecem um nível de âmbito acima das subscrições. Estes permitem-lhe organizar as subscrições em contentores chamados "grupos de gestão" e aplicar as suas condições de governação aos grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as condições aplicadas ao grupo de gestão. Os grupos de gestão dão-lhe capacidades de gestão de nível empresarial em grande escala, independentemente do seu tipo de subscrição.
Todas as subscrições num único grupo de gestão têm de confiar no mesmo inquilino do Azure Active Directory.

Por exemplo, pode aplicar políticas a um grupo de gestão que limita as regiões disponíveis para a criação de máquinas virtuais (VM). Esta política seria aplicada a todos os grupos de gestão, subscrições e recursos existentes nesse grupo de gestão ao apenas permitir a criação de VMs nessa região.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarquia de grupos de gestão e de subscrições

Pode criar uma estrutura flexível de grupos de gestão e de subscrições para organizar os seus recursos numa hierarquia para assegurar uma gestão unificada de acesso e política. O seguinte diagrama mostra um exemplo de criação de uma hierarquia de governação com grupos de gestão.

:::image type="complex" source="./media/tree.png" alt-text="Diagrama de uma hierarquia de grupo de gestão de amostras." border="false":::
   Diagrama de um grupo de gestão de raiz que detém grupos de gestão e subscrições. Alguns grupos de gestão de crianças detêm grupos de gestão, alguns possuem assinaturas, e alguns detêm ambos. Um dos exemplos na hierarquia da amostra são quatro níveis de grupos de gestão, sendo o nível infantil todas as subscrições.
:::image-end:::

Pode criar uma hierarquia que se aplique a uma política, para, por exemplo, limitar as localizações das VMs à Região E.U.A. Oeste no grupo com o nome "Produção". Esta política será herdada por todas as subscrições do Contrato Enterprise (EA) que são descendentes desse grupo de gestão e será aplicada a todas as VMs nessas subscrições. Esta política de segurança não pode ser alterada pelo proprietário do recurso ou da subscrição, o que permite uma melhor governação.

Outro cenário em que utilizaria os grupos de gestão seria para fornecer acesso de utilizador a várias subscrições. Ao mover várias subscrições sob esse grupo de gestão, pode criar uma [atribuição de papel Azure](../../role-based-access-control/overview.md) no grupo de gestão, que herdará esse acesso a todas as subscrições. Uma atribuição no grupo de gestão pode permitir que os utilizadores tenham acesso a tudo o que precisam em vez de scriptar O RBAC sobre diferentes subscrições.

### <a name="important-facts-about-management-groups"></a>Factos importantes sobre grupos de gestão

- Um único diretório suporta 10 000 grupos de gestão.
- Uma árvore de grupo de gestão pode suportar até seis níveis de profundidade.
  - Este limite não inclui o nível da Raiz ou o nível da subscrição.
- Cada grupo de gestão e subscrição só podem suportar um elemento principal.
- Cada grupo de gestão pode ter muitos subordinados.
- Todos os grupos de gestão e subscrições estão contidos numa única hierarquia em cada diretório. Veja [Factos importantes sobre o grupo de gestão de Raiz](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Grupo de gestão de raiz para cada diretório

É atribuído a cada diretório um único grupo de gestão de nível superior denominado grupo de gestão de "Raiz". Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. Este grupo de gestão de raízes permite que políticas globais e atribuições de funções Azure sejam aplicadas ao nível do diretório. Inicialmente, o [Administrador Global do Azure AD tem de se elevar](../../role-based-access-control/elevate-access-global-admin.md) à função de Administrador de Acesso de Utilizador deste grupo de raiz. Após elevar o acesso, o administrador pode atribuir qualquer papel de Azure a outros utilizadores ou grupos de diretórios para gerir a hierarquia. Enquanto administrador, pode atribuir à sua própria conta a função de proprietário do grupo de gestão de raiz.

### <a name="important-facts-about-the-root-management-group"></a>Factos importantes sobre o grupo de gestão de Raiz

- Por predefinição, o nome a apresentar do grupo de gestão de raiz é **Grupo de raiz do inquilino**. O ID é o ID do Azure Active Directory.
- Para alterar o nome a apresentar, a sua conta tem de estar atribuída à função de Proprietário ou Contribuinte no grupo de gestão de raiz. Consulte [alterar o nome de um grupo de gestão](manage.md#change-the-name-of-a-management-group) para atualizar o nome de um grupo de gestão.
- O grupo de gestão de raiz não pode ser movido nem eliminado, ao contrário de outros grupos de gestão.  
- Todos os grupos de gestão e subscrições ficam associados ao grupo de gestão de raiz dentro do diretório.
  - Todos os recursos no diretório ficam associados ao grupo de gestão de raiz para gestão global.
  - Quando são criadas, as novas subscrições são colocadas automaticamente por predefinição no grupo de gestão de raiz.
- Todos os clientes do Azure podem ver o grupo de gestão de raiz, mas nem todos os clientes têm acesso para gerir esse mesmo grupo de gestão de raiz.
  - Todos os utilizadores com acesso a uma subscrição podem ver o contexto em que essa subscrição se insere na hierarquia.  
  - Ninguém recebe acesso predefinido ao grupo de gestão de raiz. Os Administradores Globais do Azure AD são os únicos utilizadores que se podem elevar para obter acesso. Uma vez que tenham acesso ao grupo de gestão de raiz, os administradores globais podem atribuir qualquer papel de Azure a outros utilizadores para gerir  
    que.
- Na SDK, o grupo de gestão de raiz, ou 'Raiz de Inquilino', funciona como um grupo de gestão.

> [!IMPORTANT]
> Qualquer atribuição de acesso de utilizador ou atribuição de política no grupo de gestão de raiz **aplica-se a todos os recursos dentro do diretório**. Por este motivo, todos os clientes devem avaliar a necessidade de ter itens definidos neste âmbito. O acesso ao utilizador e as atribuições de políticas devem ser "Must Have" apenas neste  
> âmbito.

## <a name="initial-setup-of-management-groups"></a>Configuração inicial dos grupos de gestão

Quando um utilizador começa a utilizar grupos de gestão, ocorre um processo de configuração inicial. Na primeira etapa, o grupo de gestão de raiz é criado no diretório. Uma vez criado este grupo, todas as subscrições existentes no diretório tornam-se elementos subordinados do grupo de gestão de raiz.
Este processo existe para garantir que existe apenas uma hierarquia de grupo de gestão num diretório. A única hierarquia dentro do diretório permite aos clientes administrativos aplicar o acesso global e políticas que os outros clientes não podem ignorar. Todas as atribuições feitas ao nível da raiz serão aplicadas a toda a hierarquia, o que inclui todos os grupos de gestão, subscrições, grupos de recursos e recursos dentro desse Inquilino do Azure AD.

## <a name="trouble-seeing-all-subscriptions"></a>Problemas ao ver todas as subscrições

Alguns diretórios que começaram a utilizar os grupos de gestão numa fase inicial da pré-visualização, antes de 25 de junho de 2018, podem ter um problema em que nem todas as subscrições estão dentro da hierarquia. O processo que permite incluir todas as subscrições na hierarquia foram implementados após uma atribuição de função ou política realizada no grupo de gestão de raiz no diretório.

### <a name="how-to-resolve-the-issue"></a>Como resolver o problema

Existem duas opções para resolver este problema.

- Remover todas as atribuições de Função e de Política do grupo de gestão de raiz
  - Ao remover quaisquer atribuições de política e função do grupo de gestão de raiz, o serviço reencha todas as subscrições para a hierarquia no próximo ciclo noturno. Este processo consiste em garantir que não é concedido acesso de forma acidente ou uma atribuição de política a todas as subscrições de inquilinos.
  - A melhora forma de realizar este processo sem afetar os seus serviços consiste em basicamente aplicar as atribuições de função ou de política um nível abaixo do Grupo de gestão de raiz. Então pode remover todas as atribuições do âmbito de raiz.
- Chame a API diretamente para iniciar o processo de preenchimento
  - Qualquer cliente no diretório pode chamar as APIs _TenantBackfillStatusRequest_ ou _StartTenantBackfillRequest_. Quando a API StartTenantBackfillRequest é chamada, lança o processo inicial de configuração de mover todas as subscrições para a hierarquia. Este processo também inicia a imposição de todas as novas subscrições como sendo um filho do grupo de gestão de raiz.
    Este processo pode ser feito sem alterar as atribuições no nível de raiz. Ao chamar a API, está a indicar que não há problema em que qualquer atribuição de política ou acesso na raiz seja aplicada a todas as subscrições.

Se tiver dúvidas sobre este processo de preenchimento, contacte: `managementgroups@microsoft.com`
  
## <a name="management-group-access"></a>Acesso de grupo de gestão

Os grupos de gestão Azure apoiam [o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md) para todos os acessos de recursos e definições de funções.
Estas permissões são herdadas pelos recursos subordinados existentes na hierarquia. Qualquer papel de Azure pode ser atribuído a um grupo de gestão que herdará a hierarquia aos recursos. Por exemplo, o colaborador VM de função Azure pode ser atribuído a um grupo de gestão. Esta função não desempenha qualquer ação no grupo de gestão, mas será herdada por todas as VMs nesse grupo de gestão.

A tabela seguinte mostra a lista de funções e as ações suportadas nos grupos de gestão.

| Nome de papel Azure             | Criar | Mudar o Nome | Mover-se\*\* | Eliminar | Atribuir Acesso | Atribuir Política | Ler  |
|:-------------------------- |:------:|:------:|:--------:|:------:|:-------------:| :------------:|:-----:|
|Proprietário                       | X      | X      | X        | X      | X             | X             | X     |
|Contribuinte                 | X      | X      | X        | X      |               |               | X     |
|Colaborador da MG\*            | X      | X      | X        | X      |               |               | X     |
|Leitor                      |        |        |          |        |               |               | X     |
|Leitor MG\*                 |        |        |          |        |               |               | X     |
|Contribuidor de Política de Recursos |        |        |          |        |               | X             |       |
|Administrador de Acesso dos Utilizadores   |        |        |          |        | X             | X             |       |

\*: MG Colaborador e MG Reader apenas permitem que os utilizadores façam essas ações no âmbito do grupo de gestão.  
\*\*: As atribuições de funções no grupo de gestão Root não são necessárias para mover um grupo de subscrição ou gestão de e para ele. Veja [Manage your resources with management groups](manage.md) (Gerir os recursos com grupos de gestão) para obter detalhes sobre como mover itens dentro da hierarquia.

## <a name="azure-custom-role-definition-and-assignment"></a>Definição e atribuição de funções personalizadas Azure

O suporte de função personalizado Azure para grupos de gestão está atualmente em pré-visualização com [algumas limitações.](#limitations) Pode definir o âmbito do grupo de gestão no âmbito designado pela Definição de Função. Essa função personalizada da Azure estará então disponível para atribuição nesse grupo de gestão e em qualquer grupo de gestão, subscrição, grupo de recursos ou recurso sob o mesmo. Esta função personalizada herdará a hierarquia como qualquer função incorporada.  

### <a name="example-definition"></a>Definição de exemplo

[Definir e criar um papel personalizado](../../role-based-access-control/custom-roles.md) não muda com a inclusão de grupos de gestão. Utilize o caminho completo para definir o grupo de gestão **/fornecedores/Microsoft.Management/managementgroups/{groupId}**.

Utilize o ID do grupo de gestão e não o nome de exibição do grupo de gestão. Este erro comum acontece uma vez que ambos são campos definidos sob medida ao criar um grupo de gestão.

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

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>Questões com a quebra da definição de função e caminho da hierarquia de atribuição

As definições de função são de âmbito atribuível em qualquer lugar da hierarquia do grupo de gestão. Uma definição de papel pode ser definida num grupo de gestão de pais enquanto a atribuição de funções real existe na subscrição da criança. Uma vez que existe uma relação entre os dois itens, receberá um erro ao tentar separar a atribuição da sua definição.

Por exemplo, vamos olhar para uma pequena parte de uma hierarquia para um visual.

:::image type="complex" source="./media/subtree.png" alt-text="Diagrama de um subconjunto da hierarquia do grupo de gestão da amostra." border="false":::
   O diagrama centra-se no grupo de gestão de raiz com grupos de gestão de crianças I T e Marketing. O grupo de gestão I T tem um único grupo de gestão infantil chamado Production, enquanto o grupo de gestão de Marketing tem duas subscrições de crianças Free Trial.
:::image-end:::

Digamos que há um papel personalizado definido no grupo de gestão de marketing. Essa função personalizada é então atribuída nas duas subscrições de teste gratuito.  

Se tentarmos mover uma dessas assinaturas para ser uma criança do grupo de gestão de produção, este movimento quebraria o caminho da atribuição de funções de subscrição para a definição de papel do grupo de gestão de Marketing. Neste cenário, receberá um erro dizendo que a mudança não é permitida, uma vez que vai quebrar esta relação.  

Existem algumas opções diferentes para corrigir este cenário:
- Remova a atribuição de funções da subscrição antes de mover a subscrição para um novo MG dos pais.
- Adicione a subscrição ao âmbito atribuível da Definição de Função.
- Altere o âmbito de aplicação na definição de função. No exemplo acima, pode atualizar os âmbitos atribuíveis do Marketing ao Root Management Group para que a definição possa ser alcançada por ambos os ramos da hierarquia.  
- Crie outro Papel Personalizado que seja definido no outro ramo. Esta nova função requer que a atribuição de funções seja alterada também na subscrição.  

### <a name="limitations"></a>Limitações  

Existem limitações quando se utilizam funções personalizadas em grupos de gestão. 

 - Só é possível definir um grupo de gestão nos âmbitos atribuíveis de um novo papel. Esta limitação está em vigor para reduzir o número de situações em que as definições de funções e atribuições de funções são desligadas. Esta situação acontece quando uma subscrição ou grupo de gestão com uma atribuição de papel se muda para um pai diferente que não tem a definição de papel.  
 - As ações do plano de dados do fornecedor de recursos não podem ser definidas em funções personalizadas do grupo de gestão. Esta restrição está em vigor, uma vez que há um problema de latência com a atualização dos fornecedores de recursos de planos de dados. Esta questão da latência está a ser trabalhada e estas ações serão desativadas da definição de papel para reduzir quaisquer riscos.
 - O Gestor de Recursos Azure não valida a existência do grupo de gestão no âmbito atribuível da definição de função. Se houver um erro ou um ID de grupo de gestão incorreto listado, a definição de função ainda é criada.
 - A atribuição de funções para um papel com _dataActions_ não é suportada. Em vez disso, crie a atribuição de funções no âmbito de subscrição.

> [!IMPORTANT]
> A adição de um grupo de gestão `AssignableScopes` está atualmente em pré-visualização. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção.
> Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="moving-management-groups-and-subscriptions"></a>Grupos de gestão móvel e subscrições 

Para mover um grupo de gestão ou subscrição para ser filho de outro grupo de gestão, três regras precisam de ser avaliadas como verdadeiras.

Se estás a fazer a ação de movimento, precisas: 

- O grupo de gestão escreve e atribui permissão de atribuição de papéis no grupo de subscrição ou gestão de crianças.
  - Exemplo de função incorporada **Proprietário**
- O grupo de gestão escreve acesso ao grupo de gestão de pais alvo.
  - Exemplo de função incorporada: **Proprietário,** **Contribuinte,** **Colaborador do Grupo de Gestão**
- O grupo de gestão escreve acesso ao grupo de gestão de pais existente.
  - Exemplo de função incorporada: **Proprietário,** **Contribuinte,** **Colaborador do Grupo de Gestão**

**Exceção**: Se o objetivo ou o grupo de gestão parental existente for o grupo de gestão Root, os requisitos de permissões não se aplicam. Uma vez que o grupo de gestão Root é o local de aterragem padrão para todos os novos grupos de gestão e subscrições, você não precisa de permissões nele para mover um item.

Se a função do Proprietário na subscrição for herdada do grupo de gestão atual, os seus alvos de movimento são limitados. Só pode mover a subscrição para outro grupo de gestão onde tem a função De Proprietário. Não pode movê-lo para um grupo de gestão onde é um contribuinte porque perderia a propriedade da subscrição. Se você está diretamente designado para o papel de Proprietário para a subscrição (não herdado do grupo de gestão), você pode movê-lo para qualquer grupo de gestão onde você é um contribuinte.

## <a name="audit-management-groups-using-activity-logs"></a>Auditar os grupos de gestão que utilizam registos de atividades

Os grupos de gestão são suportados no [Registo de Atividades do Azure](../../azure-monitor/essentials/platform-logs-overview.md). Pode procurar todos os eventos que acontecem a um grupo de gestão na mesma localização central de outros recursos do Azure. Por exemplo, pode ver todas as alterações de Atribuições de Funções ou de Atribuição de Política feitas a um grupo de gestão específico.

:::image type="content" source="./media/al-mg.png" alt-text="Screenshot de Registos de Atividade e operações relacionadas com o grupo de gestão selecionado." border="false":::

Quando quiser consultar Grupos de Gestão fora do portal do Azure, o âmbito de destino dos grupos de gestão é semelhante a **"/ providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os grupos de gestão, veja:

- [Criar grupos de gestão para organizar recursos do Azure](./create-management-group-portal.md)
- [Como alterar, eliminar ou gerir os seus grupos de gestão](./manage.md)
- Ver opções para [como proteger a sua hierarquia de recursos](./how-to/protect-resource-hierarchy.md)
