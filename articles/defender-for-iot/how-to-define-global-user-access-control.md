---
title: Definir o controlo de acesso de utilizador global
description: Em grandes organizações, as permissões de utilizador podem ser complexas e podem ser determinadas por uma estrutura organizacional global, além da estrutura padrão do site e da zona.
ms.date: 12/08/2020
ms.topic: article
ms.openlocfilehash: 8f5f8df56a5dcb4152fc0ae9fcae3d504d6cf3e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784293"
---
# <a name="define-global-access-control"></a>Definir controlo global de acesso

Em grandes organizações, as permissões de utilizador podem ser complexas e podem ser determinadas por uma estrutura organizacional global, além da estrutura padrão do site e da zona.

Para apoiar a procura de permissões de acesso ao utilizador que sejam globais e mais complexas, você pode criar uma topologia de negócios global baseada em unidades de negócio, regiões e sites. Em seguida, pode definir permissões de acesso ao utilizador em torno destas entidades.

Trabalhar com ferramentas de acesso para topologia de negócios ajuda as organizações a implementar estratégias de confiança zero, controlando melhor onde os utilizadores gerem e analisam dispositivos na plataforma Azure Defender para IoT.

## <a name="about-access-groups"></a>Sobre grupos de acesso

O controlo global do acesso é estabelecido através da criação de grupos de acesso ao utilizador. Os grupos de acesso consistem em regras relativas às quais os utilizadores podem aceder a entidades empresariais específicas. Trabalhar com grupos permite-lhe controlar o acesso de visualização e configuração ao Defender para IoT para funções específicas de utilizador em unidades de negócio relevantes, regiões e sites.

Por exemplo, permitir que os analistas de segurança de um grupo ative directory acedam a todas as linhas de produção de automóveis e vidros da Europa Ocidental, juntamente com uma linha de plásticos numa região.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="Diagrama do grupo de Diretoria Ativa do Analista de Segurança.":::

Antes de criar grupos de acesso, recomendamos que:

- Crie cuidadosamente a sua topologia de negócios. Para obter mais informações sobre topologia de negócios, consulte [Trabalhar com vistas para o mapa do site.](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)

- Planeie quais os utilizadores associados aos grupos de acesso que cria. Estão disponíveis duas opções para atribuir os utilizadores a grupos de acesso:

  - **Atribuir grupos de grupos de Diretório Ativo**: Verifique se criou uma instância ative de Diretório para integrar-se na consola de gestão no local.
  
  - **Atribuir utilizadores locais**: Verifique se criou utilizadores. Para mais informações, consulte [Definir utilizadores.](how-to-create-and-manage-users.md#define-users)

Os utilizadores de administração não podem ser designados para grupos de acesso. Estes utilizadores têm acesso a todas as entidades de topologia de negócios por padrão.

## <a name="create-access-groups"></a>Criar grupos de acesso

Esta secção descreve como criar grupos de acesso. Unidades e regiões de negócios globais padrão são criadas para o primeiro grupo que cria. Pode editar as entidades predefinicionárias quando definir o seu primeiro grupo.

Para criar grupos:

1. Selecione **Grupos** de Acesso a partir do menu lateral da consola de gestão no local.

2. Selecione :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::. Na caixa de diálogo **do Grupo Add Access,** insira um nome para o grupo de acesso. A consola suporta 64 caracteres. Atribua o nome de uma forma que o ajude a distinguir facilmente este grupo de outros grupos.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="A caixa de diálogo do Grupo Add Access onde cria grupos de acesso.":::

3. Se aparecer a opção Atribuir uma opção **Grupo de Diretório Ativo,** pode atribuir um grupo de utilizadores ative directory a este grupo de acesso.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Atribua um grupo de Diretório Ativo na caixa de diálogo do Grupo De Acesso Criar.":::

   Se a opção não aparecer e pretender incluir grupos de Diretório Ativo em grupos de acesso, selecione **Definições do Sistema**. No painel **integrações,** defina os grupos. Introduza um nome de grupo exatamente como aparece nas configurações do Ative Directory e em minúsculas.

5. No painel **de Utilizadores,** atribua o número de utilizadores necessários ao grupo. Também pode atribuir utilizadores a diferentes grupos. Se trabalhar desta forma, deve criar e guardar o grupo de acesso e as regras e, em seguida, atribuir os utilizadores ao grupo a partir do painel de **Utilizadores.**

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="Gerencie as funções dos seus utilizadores e atribua-as conforme necessário.":::

6. Crie regras na caixa de diálogo **de *nomes* add rules** com base nos requisitos de acesso da topologia do seu negócio. As opções que aparecem aqui são baseadas na topologia que desenhou nas janelas de **Visão de Empresa** e **Gestão de Locais.** 

   Pode criar mais do que uma regra por grupo. Pode precisar de criar mais do que uma regra por grupo quando estiver a trabalhar com granularidade de acesso complexa em vários locais. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="Adicione uma regra ao seu sistema.":::

As regras que cria aparecem na caixa de diálogo **do Grupo Add Access.** Lá, pode apagá-los ou editá-los.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="Veja e edite todos os seus grupos de acesso a partir desta janela.":::

### <a name="about-rules"></a>Sobre regras

Quando estiver a criar regras, esteja atento às seguintes informações:

- Quando um grupo de acesso contém várias regras, a lógica da regra agrega todas as regras. Por exemplo, as regras usam e lógica, não lógica OR.

- Para que uma regra seja aplicada com sucesso, deve atribuir sensores a zonas na janela **de Gestão** do Local.

- Pode atribuir apenas um elemento por regra. Por exemplo, pode atribuir uma unidade de negócio, uma região e um site para cada regra. Crie mais regras para o grupo se, por exemplo, quiser que os utilizadores de um grupo ative de Diretório tenham acesso a diferentes unidades de negócio em diferentes regiões.

- Se alterar uma entidade e a alteração afetar a lógica da regra, a regra será eliminada. Se as alterações feitas a uma entidade de topologia afetarem a lógica da regra de modo a que todas as regras sejam eliminadas, o grupo de acesso permanece, mas os utilizadores não podem entrar na consola de gestão no local. Os utilizadores são notificados para contactar o seu administrador para iniciar siva.

- Se nenhuma unidade de negócio ou região for selecionada, os utilizadores terão acesso a todas as unidades e regiões de negócio definidas.

## <a name="see-also"></a>Ver também

[Sobre o Defender para utilizadores de consolas IoT](how-to-create-and-manage-users.md)
