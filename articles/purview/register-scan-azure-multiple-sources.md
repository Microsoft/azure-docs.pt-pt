---
title: Como digitalizar várias fontes do Azure
description: Saiba como digitalizar toda uma subscrição ou grupo de recursos do Azure no seu catálogo de dados Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: 098f62365971fd634001706ab99fd414a6b25056
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123569"
---
# <a name="register-and-scan-azure-multiple-sources"></a>Registar e digitalizar várias fontes do Azure

Este artigo descreve como registar uma Fonte Múltipla Azure (Azure Subscriptions ou Grupos de Recursos) em Purview e configurar uma verificação nele.

## <a name="supported-capabilities"></a>Capacidades suportadas

Azure multiple Source suporta digitalizações para capturar metadados e esquemas na maioria dos tipos de recursos Azure que o Purview suporta. Também classifica os dados automaticamente com base nas regras de classificação do sistema e personalizados.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registar fontes de dados, crie uma conta Azure Purview. Para obter mais informações sobre a criação de uma conta Desemis, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).
- Você precisa ser um Azure Purview Data Source Admin
- Configuração da autenticação conforme descrito nas secções abaixo

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>Criação de autenticação para enumeração de recursos no âmbito de uma subscrição ou grupo de recursos

1. Navegue para a subscrição ou para o grupo de recursos no portal Azure.  
1. Selecione **Access Control (IAM)**   do menu de navegação à esquerda 
1. Tem de ser proprietário ou administrador de acesso ao utilizador para adicionar uma função na subscrição ou grupo de recursos. Selecione *+Adicionar* botão. 
1. Desave a Função **leitora** e introduza o nome da sua conta Azure Purview (que representa o seu MSI) na caixa de entrada Select. Clique em *Guardar* para terminar a atribuição de funções.

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>Criação de autenticação para digitalizar recursos no âmbito de uma subscrição ou grupo de recursos

Existem duas formas de configurar a autenticação para a Azure múltipla fonte:

- Identidade Gerida
- Principal de Serviço

> [!NOTE]
> Tem de configurar a autenticação em cada recurso dentro da sua subscrição ou grupo de recursos, que pretende registar e digitalizar. Os tipos de recursos de armazenamento Azure (Azure blob storage e Azure Data Lake Storage Gen2) facilitam a sua adição do MSI ou DomAdor de Serviços ao nível do grupo de subscrição/grupo de recursos como leitor de dados blob de armazenamento. As permissões escorrem então para cada conta de armazenamento dentro desse grupo de subscrição ou recursos. Para todos os outros tipos de recursos, deve aplicar o MSI ou o principal de serviço em cada recurso, ou dispositivo um script para o fazer. Aqui está como adicionar permissões em cada tipo de recurso dentro de um grupo de subscrição ou recursos.
    
- [Armazenamento de Blobs do Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Armazenamento do Azure Data Lake Ger1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan) (Armazenamento do Azure Data Lake Gen2)
- [Base de Dados SQL do Azure](register-scan-azure-sql-database.md)
- [Exemplo gerido pela base de dados Azure SQL](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-an-azure-multiple-source"></a>Registar uma Fonte Múltipla Azure

Para registar uma nova Fonte Azure múltipla no seu catálogo de dados, faça o seguinte:

1. Navegue para a sua conta Desemis
1. Selecione **Fontes** na navegação à esquerda
1. Selecione **Registar-se**
1. Nas **fontes de registo**, selecione **Azure (múltiplos)**
1. Selecione **Continuar**

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Registar várias fontes do Azure":::

No ecrã **'Registar' (Azure multiple),** faça o seguinte:

1. Introduza um **Nome** que a fonte de dados será listada no Catálogo 
1. Opcionalmente escolha um **grupo de gestão** para filtrar para baixo para
1. **Selecione uma subscrição ou um grupo de recursos específicos** sob uma determinada subscrição no dropdown. O âmbito de inscrição será definido para a subscrição selecionada ou para o grupo de recursos  
1. Selecione uma **coleção** ou crie uma nova (Opcional)
1. **Termine** para registar a fonte de dados

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Configurar a fonte múltipla do Azure":::

## <a name="creating-and-running-a-scan"></a>Criar e executar uma digitalização

Para criar e executar uma nova digitalização, faça o seguinte:

1. Navegue para a secção **Fontes**

1. Selecione a fonte de dados que registou

1. Clique em detalhes de visualização e Selecione **+ Nova digitalização** ou use o ícone de ação rápida de digitalização no azulejo de origem

1. Preencha o *nome* e selecione todos os tipos de recursos que pretende digitalizar dentro desta fonte

    1. Pode deixá-lo como *All* (Isto inclui futuros tipos de recursos que podem não existir atualmente dentro desse grupo de subscrição ou recursos)
    1. Pode **selecionar especificamente os tipos de recursos** que pretende digitalizar. Se escolher esta opção, os futuros tipos de recursos que possam ser criados dentro desta subscrição ou grupo de recursos não serão incluídos para digitalizações, a menos que a digitalização seja explicitamente editada no futuro
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Azure múltipla Sondagem de Origem":::

1. Selecione a credencial para ligar aos recursos dentro da sua fonte de dados. 
    1. Pode selecionar uma **credencial ao nível dos pais** como MSI ou uma credencial de tipo principal de serviço, que pode optar por utilizar para todos os tipos de recursos sob a subscrição ou grupo de recursos
    1. Também pode **selecionar** especificamente o tipo de recurso e aplicar uma credencial diferente para esse tipo de recurso
    1. Cada credencial será considerada como o método de autenticação para todos os recursos sob um determinado tipo
    1. Deve definir a credencial escolhida sobre os recursos, a fim de analisá-los com sucesso, como descrito nesta [secção](#setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group) acima
1. Dentro de cada tipo pode selecionar para digitalizar todos os recursos ou um subconjunto deles pelo nome.
    1. Se deixar a opção como **todos,** então os recursos futuros desse tipo também serão digitalizados em futuras pesquisas
    1. Se selecionar contas de armazenamento específicas ou bases de dados SQL, então os recursos futuros criados desse tipo dentro deste grupo de subscrição ou recursos não serão incluídos para digitalizações, a menos que a digitalização seja explicitamente editada no futuro
 
1.  Clique em **continuar** para continuar. Testaremos o acesso para verificar se aplicou o MSI do Purview como leitor no grupo de subscrição ou recursos. Se uma mensagem de erro for lançada, siga as instruções [aqui](#setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)

1.  Selecione **os conjuntos de regras** de digitalização para cada tipo de recurso escolhido no passo anterior. Também pode criar conjuntos de regras de digitalização em linha.
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Seleção de conjunto de regras de digitalização múltipla Azure":::

1. Escolha o seu gatilho de digitalização. Pode programar para funcionar **semanal/mensalmente** ou **uma vez**

1. Reveja a sua digitalização e selecione Guardar para completar a configuração   

## <a name="viewing-your-scans-and-scan-runs"></a>Visualizar os seus exames e digitalizar corre

1. Ver detalhes da origem clicando nos detalhes da **visualização** no azulejo sob a secção de fontes. 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Detalhes de origem azure"::: 

1. Veja os detalhes da execução da verificação navegando na página de detalhes da **digitalização.**
    1. A *barra de estado* é um breve resumo sobre o estado de funcionamento dos recursos das crianças. Será exibido no nível de grupo de subscrição ou recursos
    1. Verde significa sucesso, enquanto vermelho significa falhado. Cinzento significa que a tomografia ainda está em andamento.
    1. Pode clicar em cada digitalização para ver mais detalhes finos

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Azure múltiplos detalhes de digitalização":::

1. Veja um resumo da recente verificação falhada na parte inferior da página de detalhes da origem. Também pode clicar em visualizar mais detalhes granulares relativos a estas corridas.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Gerir as suas digitalizações - editar, excluir ou cancelar
Para gerir ou apagar uma verificação, faça o seguinte:

- Navegue para o centro de gestão. Selecione fontes de dados sob as Fontes e a secção de digitalização e, em seguida, selecione na fonte de dados desejada

- Selecione a digitalização que gostaria de fazer. Pode editar a digitalização selecionando Editar

- Pode eliminar a sua verificação selecionando Eliminar
- Se um scan está em execução, você pode cancelá-lo também

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)    
