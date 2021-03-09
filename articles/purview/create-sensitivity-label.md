---
title: Aplicar automaticamente rótulos de sensibilidade aos seus dados
description: Aprenda a criar etiquetas de sensibilidade e aplique-as automaticamente aos seus dados durante uma varredura.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: 37ac292acc76c681ea38b2ae881ff8cd2ae5ec3c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502451"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>Rotular automaticamente os seus dados em Azure Purview

Este artigo descreve como criar etiquetas de sensibilidade microsoft Information Protection (MIP) e aplicá-las automaticamente aos seus ativos Azure em Azure Purview.

## <a name="what-are-sensitivity-labels"></a>O que são rótulos de sensibilidade? 

Para fazer o trabalho, as pessoas na sua organização colaboram com outros dentro e fora da organização. Os dados nem sempre ficam na sua nuvem, e muitas vezes vagueiam por todo o lado, através de dispositivos, apps e serviços. 

Quando os seus dados vagueiam, deseja que o faça de forma segura e protegida que atenda às políticas de negócios e conformidade da sua organização.

A aplicação de rótulos de sensibilidade permite-lhe indicar o quão sensíveis determinados dados são na sua organização. Por exemplo, um nome específico do projeto pode ser altamente confidencial dentro da sua organização, enquanto que esse mesmo termo não é confidencial para outras organizações. 

### <a name="sensitivity-labels-in-azure-purview"></a>Etiquetas de sensibilidade em Azure Purview

Em Purview, as classificações são semelhantes às tags de assunto, e são usadas para marcar e identificar dados de um tipo específico que é encontrado dentro da sua propriedade de dados durante a digitalização.

A Purview utiliza as mesmas classificações, também conhecidas como tipos de informação sensível, como o Microsoft 365.  As etiquetas de sensibilidade MIP são criadas no Microsoft 365 Security and Compliance Center (SCC). Isto permite-lhe estender as suas etiquetas de sensibilidade existentes através dos seus ativos Azure Purview.

**As classificações** são correspondidas diretamente, como um número de segurança social, que tem uma classificação de Número de **Segurança Social.** 

Em contrapartida, **aplicam-se etiquetas de sensibilidade** quando uma ou mais classificações e condições são encontradas em conjunto. Neste contexto, [as condições](/microsoft-365/compliance/apply-sensitivity-label-automatically) referem-se a todos os parâmetros que pode definir para dados não estruturados, tais como *a proximidade a outra classificação*, e *a confiança de %*. 

As etiquetas de sensibilidade no Azure Purview podem ser utilizadas para aplicar automaticamente rótulos em ficheiros e colunas de bases de dados.

Para obter mais informações, consulte:

- [Conheça as etiquetas de sensibilidade](/microsoft-365/compliance/sensitivity-labels) na documentação da Microsoft 365
- [O que são regras de rotulagem automática?](#what-are-auto-labeling-rules)
- [Tipos de dados suportados para etiquetas de sensibilidade em Azure Purview](#supported-data-types-for-sensitivity-labels-in-azure-purview)
- [Rotulagem para colunas de base de dados SQL](#labeling-for-sql-database-columns)

#### <a name="what-are-auto-labeling-rules"></a>O que são regras de rotulagem automática?

Os seus dados estão em constante crescimento e mudança. Rastrear os dados atualmente não rotulados e tomar medidas para aplicar etiquetas manualmente não só é complicado, como também é uma dor de cabeça desnecessária. 

As regras de rotulagem automática são condições que especifica, indicando quando deve ser aplicada uma determinada etiqueta. Quando estas condições são satisfeitas, o rótulo é automaticamente atribuído aos dados, retendo etiquetas de sensibilidade consistentes nos seus dados, em escala.

Quando criar as suas etiquetas, certifique-se de definir regras de rotulagem automática para ambos os [ficheiros](#define-auto-labeling-rules-for-files) e [colunas de base de dados](#define-auto-labeling-rules-for-database-columns) para aplicar automaticamente as suas etiquetas com cada digitalização de dados. 

Depois de digitalizar os seus dados em Purview, pode ver as etiquetas automaticamente aplicadas nos relatórios do Catálogo e Insight de Purview.
#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Tipos de dados suportados para etiquetas de sensibilidade em Azure Purview

As etiquetas de sensibilidade são suportadas no Azure Purview para os seguintes tipos de dados:

|Tipo de dados  |Origens  |
|---------|---------|
|Rotulagem automática para ficheiros     |     - Armazenamento Azure Blob  </br>- Azure Data Lake Storage Gen 1 e Gen 2  |
|Rotulagem automática para colunas de base de dados     |  - Servidor SQL </br>- Base de dados Azure SQL </br>- Azure SQL Database Managed Instance   <br> - Azure Synapse  <br>- Azure Cosmos DB <br><br>Para obter mais informações, consulte [a rotulagem das colunas de base de dados SQL](#labeling-for-sql-database-columns) abaixo.  |
| | |

#### <a name="labeling-for-sql-database-columns"></a>Rotulagem para colunas de base de dados SQL

Além da rotulagem de Purview para colunas de bases de dados, a Microsoft também suporta a rotulagem para colunas de base de dados SQL usando a classificação de dados SQL no [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Enquanto a Purview utiliza os [rótulos globais de sensibilidade mip,](/microsoft-365/compliance/sensitivity-labels)o SSMS utiliza apenas rótulos definidos localmente.

A rotulagem em Purview e a rotulagem em SSMS são processos separados que não interagem atualmente entre si. Por conseguinte, as etiquetas aplicadas em SSMS não são mostradas em Purview, e vice-versa. Recomendamos a Azure Purview para a rotulagem das bases de dados SQL, uma vez que utiliza rótulos MIP globais que podem ser aplicados em várias plataformas.

Para mais informações, consulte a [documentação de descoberta e classificação de dados SQL.](/sql/relational-databases/security/sql-data-discovery-and-classification)

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>Como criar etiquetas de sensibilidade na Microsoft 365

Se ainda não tiver etiquetas de sensibilidade, terá de criá-las e disponibilizá-las para o Azure Purview. Os rótulos de sensibilidade existentes também podem ser modificados para os disponibilizar para o Azure Purview.

Para obter mais informações, consulte:

- [Requisitos de licenciamento](#licensing-requirements)
- [Alargamento dos rótulos de sensibilidade à Azure Purview](#extending-sensitivity-labels-to-azure-purview)
- [Criar novos rótulos de sensibilidade ou modificar os rótulos existentes](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>Requisitos de licenciamento

As etiquetas de sensibilidade MIP são criadas e geridas no Microsoft 365 Security and Compliance Center. Para criar etiquetas de sensibilidade para utilização no Azure Purview, tem de ter uma licença Microsoft 365 E5 ativa.

Se ainda não tiver a licença necessária, pode inscrever-se para um teste do [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion).

### <a name="extending-sensitivity-labels-to-azure-purview"></a>Alargamento dos rótulos de sensibilidade à Azure Purview

Por padrão, as etiquetas de sensibilidade MIP só estão disponíveis para ativos no Microsoft 365, onde pode aplicá-las em ficheiros e e-mails.

Para aplicar etiquetas de sensibilidade MIP aos ativos da Azure em Azure Purview, tem de consentir explicitamente em alargar as etiquetas e selecionar as etiquetas específicas que pretende estar disponível em Purview.

Ao alargar os rótulos de sensibilidade do MIP com o Azure Purview, as organizações podem agora descobrir, classificar e obter uma visão da sensibilidade através de uma gama mais ampla de fontes de dados, minimizando o risco de conformidade.

> [!NOTE]
> Uma vez que a Microsoft 365 e a Azure Purview são serviços separados, existe a possibilidade de serem implantados em diferentes regiões. Os nomes de etiquetas e os nomes de tipo informação sensível personalizado são considerados dados do cliente, e são mantidos dentro da mesma localização GEO por padrão para proteger a sensibilidade dos seus dados e evitar as leis do RGPD.
>
> Por esta razão, as etiquetas e os tipos de informação sensíveis personalizados não são partilhados com a Azure Purview por padrão, e exigem o seu consentimento para usá-los em Azure Purview.

**Para alargar os rótulos de sensibilidade à Purview:**

Os seguintes passos permitem que as suas etiquetas de sensibilidade estejam disponíveis para utilização no Azure Purview, onde pode aplicar as suas etiquetas de sensibilidade a ativos como colunas SQL, ficheiros no Azure Blob Storage, entre outros.

1. Na Microsoft 365, navegue para a página **de Proteção de Informação.** 
1. Na **rotulagem Extend para ativos em Azure Purview,** selecione o botão **Ligar e,** em seguida, selecione **Sim** no diálogo de confirmação que aparece.

Por exemplo:

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="Selecione **Ligue** para estender os rótulos de sensibilidade ao Purview" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
Uma vez que estenda a rotulagem aos ativos em Azure Purview, pode selecionar as etiquetas que pretende disponibilizar em Purview. Para obter mais informações, consulte [a criação de novos rótulos de sensibilidade ou modificar as etiquetas existentes.](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>Criar novos rótulos de sensibilidade ou modificar os rótulos existentes

Quando utiliza etiquetas de sensibilidade para aplicações do Office no Windows, macOS, iOS e Android, os utilizadores vêem novas etiquetas dentro de quatro horas e dentro de uma hora para o Office na web. No entanto, permita até 24 horas para que as alterações se reproduzam em todas as aplicações e serviços.

> [!IMPORTANT]
> Não elimine uma etiqueta a menos que compreenda o impacto para os seus utilizadores. Para obter mais informações, consulte [remover e eliminar etiquetas](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels) na documentação da Microsoft 365.
>

**Criar novos rótulos de sensibilidade ou modificar os rótulos existentes:**

1. Abra o [Microsoft 365 Security and Compliance Center](https://protection.office.com/homepage). 

1. Em **Soluções**, selecione **proteção de informação** e, em seguida, selecione **Criar uma etiqueta**. 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="Criar etiquetas de sensibilidade no Microsoft 365 Security and Compliance Center" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. Diga o rótulo. Em seguida, ao **definir o âmbito para este rótulo:**

    - Em todos os casos, selecione **os ativos da Azure Purview**.
    - Para rotular ficheiros, selecione também **ficheiros & e-mails**. Esta opção não é exigida apenas para rotular os ativos da base de dados. 
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="Crie a sua etiqueta no Microsoft 365 Security and Compliance Center" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. Siga o resto das indicações no assistente para as definições da etiqueta. 

    Especificamente, defina regras de rotulagem automática para ficheiros e colunas de bases de dados:

    - [Definir regras de rotulagem automática para ficheiros](#define-auto-labeling-rules-for-files)
    - [Definir regras de rotulagem automática para colunas de bases de dados](#define-auto-labeling-rules-for-database-columns)

    Para obter mais informações sobre as opções de assistente, consulte [o que as etiquetas de sensibilidade podem fazer](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) na documentação da Microsoft 365.

1. Repita os passos listados acima para criar mais etiquetas. 

    Para criar um sublbel, selecione a etiqueta dos pais > **...**  >  **Mais ações**  >  **Adicione a etiqueta de sub.**

1. Para modificar as etiquetas existentes, navegue nas **etiquetas de proteção**  >  **de** informação e selecione a sua etiqueta. 

    Em seguida, **selecione editar** a etiqueta para abrir novamente o assistente de etiqueta de **sensibilidade Editar,** com todas as definições que definiu quando criou a etiqueta.

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="Editar um rótulo de sensibilidade existente" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. Quando terminar de criar todas as suas etiquetas, certifique-se de ver o seu pedido de etiqueta e reencomenda-os conforme necessário. 

    Para alterar a ordem de  uma etiqueta, selecione... **> Mais ações**  >  **Mova-se para cima** ou para **baixo.** 

    Para obter mais informações, consulte [a prioridade do Rótulo (questões de encomenda)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) na documentação microsoft 365.


Continue [digitalizando os seus dados para aplicar as etiquetas automaticamente,](#scan-your-data-to-apply-labels-automatically)e depois:

- [Ver rótulos em ativos](#view-labels-on-assets)
- [Ver relatórios insight para as classificações e rótulos de sensibilidade](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-auto-labeling-rules-for-files"></a>Definir regras de rotulagem automática para ficheiros

Defina regras de rotulagem automática para ficheiros no assistente quando criar ou editar a sua etiqueta. 

Na página **de rotulagem automática para aplicações do Office,** permita **a rotulagem automática das aplicações do Office e,** em seguida, defina as condições em que deseja que o seu rótulo seja automaticamente aplicado aos seus dados.

Por exemplo:

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="Defina regras de rotulagem automática para ficheiros no Microsoft 365 Security and Compliance Center" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
Para obter mais informações, consulte [Aplicar automaticamente uma etiqueta de sensibilidade aos dados](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) na documentação da Microsoft 365. 

#### <a name="define-auto-labeling-rules-for-database-columns"></a>Definir regras de rotulagem automática para colunas de bases de dados

Defina regras de rotulagem automática para colunas de base de dados no assistente quando criar ou editar a sua etiqueta. 

Sob a opção **Azure Purview ativos (pré-visualização):**

1. Selecione a rotulagem automática para o slider de colunas de base de **dados.**

1. Selecione **Verifique os tipos de informações sensíveis** para escolher os tipos de informações sensíveis que pretende aplicar à sua etiqueta.

Por exemplo:
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="Definir regras de rotulagem automática para colunas SQL no Microsoft 365 Security and Compliance Center" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>Digitalize os seus dados para aplicar etiquetas automaticamente

Digitalize os seus dados em Azure Purview para aplicar automaticamente as etiquetas que criou, com base nas regras de rotulagem automática que definiu. 

Para obter mais informações sobre como configurar digitalizações em vários ativos em Azure Purview, consulte:

|Origem  |Referência  |
|---------|---------|
|**Armazenamento de Blobs do Azure**     |[Registar e digitalizar o armazenamento da bolha de Azure](register-scan-azure-blob-storage-source.md)         |
|**Azure Data Lake Storage**     |[Registar e digitalizar Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[Registar e digitalizar Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)         |
|**Bases de Dados SQL do Azure**|[Registe-se e digitalize uma Base de Dados Azure SQL](register-scan-azure-sql-database.md) </br>[Registe e digitalize uma caixa de dados de base de dados Azure SQL](register-scan-azure-sql-database-managed-instance.md)|
| | |

## <a name="view-labels-on-assets"></a>Ver rótulos em ativos

Depois de definir as regras de rotulagem automática das suas etiquetas no Microsoft 365 e digitalizar os seus dados em Azure Purview, as etiquetas são automaticamente aplicadas aos seus ativos. 

**Para visualizar as etiquetas aplicadas aos seus ativos no Catálogo Azure Purview:**

No Catálogo Azure Purview, utilize as opções de filtragem **do Rótulo** para mostrar ficheiros apenas com etiquetas específicas. Por exemplo: 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="Pesquisa de ativos por etiqueta" lightbox="media/create-sensitivity-label/filter-search-results.png":::

Por exemplo:

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="Ver uma etiqueta de sensibilidade num ficheiro no seu Azure Blob Storage" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>Ver relatórios insight para as classificações e rótulos de sensibilidade

Encontre informações sobre os seus dados classificados e rotulados no Azure Purview utilizando os relatórios de **rotulagem de classificação** e **sensibilidade.**

> [!div class="nextstepaction"]
> [Insights de classificação](./classification-insights.md)

> [!div class="nextstepaction"]
> [Insights do rótulo de sensibilidade](sensitivity-insights.md)
