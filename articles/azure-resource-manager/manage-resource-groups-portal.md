---
title: Gerir grupos do Azure Resource Manager com o portal do Azure | Documentos da Microsoft
description: Utilize o portal do Azure para gerir os seus grupos do Azure Resource Manager.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 2f31809a0d80079730966e2ecdb017b68563949e
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825109"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gerir grupos de recursos do Azure Resource Manager com o portal do Azure

Saiba como utilizar o [portal do Azure](https://portal.azure.com) com [do Azure Resource Manager](resource-group-overview.md) para gerir os seus grupos de recursos do Azure. Para gerir recursos do Azure, veja [recursos de gerir o Azure com o portal do Azure](./manage-resources-portal.md).

Outros artigos sobre a gestão de grupos de recursos:

- [Gerir grupos de recursos do Azure com a CLI do Azure](./manage-resources-cli.md)
- [Gerir grupos de recursos do Azure com o Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Decida como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização. Em geral, adicione recursos que partilham o mesmo ciclo de vida no mesmo grupo de recursos para que possa facilmente implantar, atualizar e eliminá-los como um grupo.

O grupo de recursos armazena metadados sobre os recursos. Por conseguinte, quando especifica uma localização para o grupo de recursos, está a especificar onde esses metadados estão armazenados. Por motivos de conformidade, poderá ter de certificar que os dados estão armazenados numa determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando especificar uma localização para o grupo de recursos, especifica onde esses metadados estão armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **grupos de recursos**

    ![Adicionar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selecione **Adicionar**.
4. Introduza os seguintes valores:

    - **Subscrição**: Selecione a sua subscrição do Azure. 
    - **Grupo de recursos**: Introduza um nome de grupo de recursos novo. 
    - **Região**: Selecione uma localização do Azure, como **E.U.A. Central**.

    ![Criar grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selecione **rever + criar**
6. Selecione **Criar**. Demora alguns segundos para criar um grupo de recursos.
7. Selecione **atualizar** no menu superior para atualizar a lista do grupo de recursos e, em seguida, selecione o grupo de recursos recentemente criado para abri-lo. Ou selecione **notificação**(o ícone de sino) na parte superior e, em seguida, selecione **vá para o grupo de recursos** para abrir o grupo de recursos recém-criado

    ![Ir para o grupo de recursos](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Listar grupos de recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Para listar os grupos de recursos, selecione **grupos de recursos**

    ![procurar grupos de recursos](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Para personalizar as informações apresentadas para os grupos de recursos, selecione **editar colunas**. Captura de ecrã seguinte mostra as colunas de adição, que poderia adicionar à apresentação de:

## <a name="open-resource-groups"></a>Grupos de recursos aberto

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Grupos de recursos**.
3. Selecione o grupo de recursos que pretende abrir.

## <a name="delete-resource-groups"></a>Eliminar grupos de recursos

1. Abra o grupo de recursos que pretende eliminar.  Ver [abrir grupos de recursos](#open-resource-groups).
2. Selecione **Eliminar grupo de recursos**.

    ![Eliminar grupo de recursos do azure](./media/manage-resource-groups-portal/delete-group.png)

Para obter mais informações sobre como o Azure Resource Manager ordena a eliminação de recursos, consulte [eliminação do grupo de recursos do Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Implementar recursos para um grupo de recursos

Depois de criar um modelo do Resource Manager, pode utilizar o portal do Azure para implementar os recursos do Azure. Para criar um modelo, consulte [início rápido: Criar e implementar modelos Azure Resource Manager com o portal do Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Para implementar um modelo com o portal, consulte [implementar recursos com modelos do Resource Manager e o portal do Azure](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mover para outro grupo de recursos ou subscrição

Pode mover os recursos no grupo para outro grupo de recursos. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Grupos de recursos de bloqueio

Bloqueio evita que outros utilizadores na sua organização acidentalmente eliminem ou modifiquem recursos críticos, como o recurso, grupo de recursos ou subscrição do Azure. 

1. Abra o grupo de recursos que pretende eliminar.  Ver [abrir grupos de recursos](#open-resource-groups).
2. No painel esquerdo, selecione **bloqueia**.
3. Para adicionar um bloqueio para o grupo de recursos, selecione **adicionar**.
4. Introduza **nome de bloqueio**, **tipo de bloqueio**, e **notas**. Os tipos de bloqueio incluem **só de leitura**, e **eliminar**.

    ![grupo de recursos do azure de bloqueio](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Para obter mais informações, consulte [bloquear recursos pode prevenir alterações inesperadas](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Grupos de recursos de etiqueta

Pode aplicar etiquetas a grupos de recursos e recursos para organizar logicamente os recursos. Para obter informações, consulte [utilizar etiquetas para organizar os recursos do Azure](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Grupos de recursos de exportação para modelos

Depois de configurar o seu grupo de recursos com êxito, poderá ver o modelo do Resource Manager para o grupo de recursos. Exportar o modelo oferece duas vantagens:

- Automatize implementações futuras da solução porque o modelo contém toda a infraestrutura completa.
- Aprenda a sintaxe do modelo ao procurar em JavaScript Object Notation (JSON) que representa a sua solução.

Existem duas formas de exportar um modelo:

- Pode exportar o modelo utilizado para a implementação. O modelo exportado inclui todos os parâmetros e variáveis exatamente como apareciam no modelo original. Esta abordagem é útil se tiver implementado recursos através do portal e agora pretende ver o modelo para criar esses recursos. Este modelo pode ser utilizado imediatamente. 
- Pode exportar um modelo gerado que representa o estado atual do grupo de recursos. O modelo exportado não é baseado em qualquer modelo que utilizou para a implementação. Em vez disso, ele cria um modelo que é um "instantâneo" ou "cópia de segurança" do grupo de recursos. O modelo exportado tem muitos valores codificados e provavelmente não tantos parâmetros como normalmente seriam definidos. Utilize esta opção para voltar a implementar recursos para o mesmo grupo de recursos. Para utilizar este modelo para outro grupo de recursos, poderá ter de significativamente modificá-lo.

### <a name="export-templates-from-deployment-history"></a>Exportar modelos do histórico de implementação

Este método exporta os modelos para determinadas implementações. Se tiver alterado os recursos a partir do portal ou adicionadas/removidas recursos em várias implementações, veja [exportar modelos de grupos de recursos](#export-templates-from-resource-groups).

1. Abra o grupo de recursos que pretende eliminar.  Ver [abrir grupos de recursos](#open-resource-groups).
2. No painel esquerdo, selecione **implementações**, ou selecione a ligação sob **implementações**.  Na captura de ecrã seguinte, mostra **bem-sucedido 4** porque havia quatro implementações separadas com quatro nomes de implementação diferentes. Poderá ver **1 com êxito**.

    ![modelos de exportação do grupo de recursos do Azure](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history.png)

3. Selecione uma das implementações da lista.
4. No painel esquerdo, selecione **modelo**. O Resource Manager obtém os seguintes seis ficheiros para si:

    - **Modelo** - O modelo que define a infraestrutura para a sua solução. Quando criou a conta do Storage através do portal, o Resource Manager utilizou um modelo para a implementar e guardou esse modelo para consulta futura.
    - **Parâmetros** - Um ficheiro de parâmetros que pode utilizar para transmitir valores durante a implementação. Contém os valores que indicou durante a primeira implementação. Pode alterar qualquer um destes valores quando reimplementar o modelo.
    - **CLI** -ficheiro de script de uma CLI do Azure que pode utilizar para implementar o modelo.
    - **PowerShell** - Um ficheiro de script do Azure PowerShell que pode utilizar para implementar o modelo.
    - **.NET** - Uma classe .NET que pode utilizar para implementar o modelo.
    - **.NET** - Uma classe Ruby que pode utilizar para implementar o modelo.

    Por predefinição, o portal apresenta o modelo.

5. Selecione **transferir** para exportar um modelo para o computador local.

    ![modelos de exportação do grupo de recursos do Azure](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history-download.png)

<a name="export-templates-from-resource-groups"></a>
### <a name="export-templates-from-resource-groups"></a>Exportar modelos de grupos de recursos

Se tiver alterado os recursos do portal ou adicionar/remover recursos em várias implementações, obter um modelo a partir do histórico de implementação não reflete o estado atual do grupo de recursos. Esta secção mostra-lhe como exportar um modelo que reflita o estado atual do grupo de recursos. Destina-se como um instantâneo do grupo de recursos, que pode utilizar para Reimplementar no mesmo grupo de recursos. Para utilizar o modelo exportado para outras soluções, tem de modificá-lo significativamente.

1. Abra o grupo de recursos que pretende eliminar.  Ver [abrir grupos de recursos](#open-resource-groups).
2. No painel esquerdo, selecione **script de automação**. O Resource Manager obtém os seguintes seis ficheiros para si:

    - **Modelo** - O modelo que define a infraestrutura para a sua solução. Quando criou a conta do Storage através do portal, o Resource Manager utilizou um modelo para a implementar e guardou esse modelo para consulta futura.
    - **Parâmetros** - Um ficheiro de parâmetros que pode utilizar para transmitir valores durante a implementação. Contém os valores que indicou durante a primeira implementação. Pode alterar qualquer um destes valores quando reimplementar o modelo.
    - **CLI** -ficheiro de script de uma CLI do Azure que pode utilizar para implementar o modelo.
    - **PowerShell** - Um ficheiro de script do Azure PowerShell que pode utilizar para implementar o modelo.
    - **.NET** - Uma classe .NET que pode utilizar para implementar o modelo.
    - **.NET** - Uma classe Ruby que pode utilizar para implementar o modelo.

    Por predefinição, o portal apresenta o modelo.
3. Selecione **transferir** para exportar um modelo para o computador local.

Alguns modelos exportados tem algumas edições para que poderem ser utilizados. Para saber como desenvolver modelos, veja a [tutoriais passo a passo](/azure/azure-resource-manager/).

### <a name="export-template-before-deploying"></a>Exportar modelo antes de implementar

Pode utilizar o portal para definir um recurso.  Antes de implementar o recurso, pode ver e exportar um modelo. Para obter instruções, consulte [início rápido: Criar e implementar modelos Azure Resource Manager com o portal do Azure](./resource-manager-quickstart-create-templates-use-the-portal.md).

### <a name="fix-export-issues"></a>Corrigir problemas de exportação

Nem todos os tipos de recursos suportam a função de modelo de exportação. Apenas verá exportar problemas ao exportar a partir de um grupo de recursos em vez do seu histórico de implementação. Se a última implementação representar com precisão o estado atual do grupo de recursos, deve exportar o modelo do histórico de implementação em vez do grupo de recursos. Só deve exporte de um grupo de recursos quando efetuar alterações ao grupo de recursos que não são definidos num único modelo.

Para resolver problemas de exportação, adicione manualmente os recursos em falta no seu modelo. A mensagem de erro inclui os tipos de recurso que não podem ser exportados. Localize o tipo de recurso na [Referência a modelos](/azure/templates/). Por exemplo, para adicionar manualmente um gateway de rede virtual, veja a [referência ao modelo Microsoft.Network/virtualNetworkGateways](/azure/templates/microsoft.network/virtualnetworkgateways). A referência de modelo dá-lhe o JSON para adicionar o recurso ao seu modelo.

Depois de obter o formato JSON para o recurso, terá de obter os valores de recursos. Pode ver os valores para o recurso usando a operação GET na REST API para o tipo de recurso. Por exemplo, para obter os valores para o seu gateway de rede virtual, consulte [Gateways de rede Virtual - obter](/rest/api/network-gateway/virtualnetworkgateways/get).

## <a name="manage-access-to-resource-groups"></a>Gerir o acesso aos grupos de recursos

[O controlo de acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) é a forma de gerir o acesso a recursos no Azure. Para obter mais informações, consulte [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter o Azure Resource Manager, veja [descrição geral do Azure Resource Manager](./resource-group-overview.md).
- Para obter a sintaxe do modelo do Resource Manager, veja [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](./resource-group-authoring-templates.md).
- Para saber como desenvolver modelos, veja a [tutoriais passo a passo](/azure/azure-resource-manager/).
- Para ver os esquemas de modelo do Azure Resource Manager, consulte [referência de modelo](/azure/templates/).