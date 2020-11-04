---
title: Política de proteção de informação SQL no Centro de Segurança Azure
description: Saiba como personalizar as políticas de proteção de informação no Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: aa3492cb67a4ccd1c09a1f1cb55ddc4f2e00953d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318660"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>Política de proteção de informação SQL no Centro de Segurança Azure
 
O mecanismo de [deteção e classificação](../azure-sql/database/data-discovery-and-classification-overview.md) de dados da SQL é integrado na [Base de Dados Azure SQL,](../azure-sql/database/sql-database-paas-overview.md) [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md), e [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Fornece capacidades avançadas para descobrir, classificar, rotular e reportar os dados sensíveis nas suas bases de dados.

O mecanismo de classificação baseia-se em duas construções primárias que compõem a taxonomia de classificação:

- **Etiquetas** – Os principais atributos de classificação, utilizados para definir o nível de sensibilidade dos dados armazenados na coluna. 
- **Tipos de Informação** – Fornece granularidade adicional ao tipo de dados armazenados na coluna.

As opções de política de proteção da informação dentro do Centro de Segurança fornecem um conjunto predefinido de rótulos e tipos de informação que servem como os predefinidos para o motor de classificação. Pode personalizar a política, de acordo com as necessidades da sua organização, conforme descrito abaixo.

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="A página que mostra a sua política de proteção de informação SQL":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>Como acesso à política de proteção de informação SQL?

Existem três formas de aceder à política de proteção da informação:

- **(Recomendado)** A partir da página de preços e definições do Centro de Segurança.
- A partir da recomendação de segurança "Os dados sensíveis nas suas bases de dados SQL devem ser classificados".
- A partir da página de descoberta de dados Azure SQL DB.

Cada um destes é mostrado no separador abaixo.



### <a name="from-security-centers-settings"></a>[**Das configurações do Centro de Segurança**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>Aceda à política a partir da página de preços e definições do Centro de Segurança <a name="sqlip-tenant"></a>

Para personalizar a política de proteção de informação para o seu inquilino Azure, você precisa ter [privilégios administrativos no grupo de gestão de raiz do inquilino.](security-center-management-groups.md) 

A partir da página **de preços e definições** do Security Center, selecione **a proteção de informação SQL**.

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="Aceder à política de proteção de informação SQL a partir da página de preços e definições do Centro de Segurança Azure":::



### <a name="from-security-centers-recommendation"></a>[**Da recomendação do Centro de Segurança**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>Aceda à política a partir da recomendação do Centro de Segurança <a name="sqlip-db"></a>

Utilize a recomendação do Centro de Segurança, "Dados sensíveis nas suas bases de dados SQL devem ser classificados", para visualizar a página de descoberta e classificação de dados para a sua base de dados. Lá, também verá as colunas descobertas para conter informações que recomendamos classificar.

1. A partir da página de **Recomendações** do Centro de Segurança, procure a recomendação **Os dados sensíveis nas bases de dados SQL devem ser classificados.**

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="Encontrar a recomendação que dá acesso às políticas de proteção da informação DO SQL":::

1. Na página de detalhes da recomendação, selecione a base de dados relevante a partir dos separadores **saudáveis** ou **não saudáveis.**

1. A página **de Classificação & data Discovery** abre. Selecione **Configurar**. 

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Abertura da política de proteção de informação SQL a partir da recomendação relevante no Centro de Segurança Azure":::



### <a name="from-azure-sql"></a>[**De Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Aceda à política a partir do Azure SQL <a name="sqlip-azuresql"></a>

1. A partir do portal Azure, abra o Azure SQL.

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="Abertura Azure SQL a partir do portal Azure":::

1. Selecione qualquer base de dados.

1. A partir da área de **Segurança** do menu, abra a página **de Classificação & Data Discovery** (1) e selecione **Configure** (2).

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="Abertura da política de proteção de informação SQL a partir do Azure SQL":::

--- 


## <a name="customize-your-information-types"></a>Personalize os seus tipos de informação

Para gerir e personalizar tipos de informação:

1. **Selecione Gerir tipos de informações**.

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="Gerir tipos de informação para a sua política de proteção de informação":::

1. Para adicionar um novo **tipo de Informação,** selecione **Criar tipo de informação** no menu superior. Pode configurar um nome, descrição e cadeias de padrão de pesquisa para o **tipo de informação**. As cordas de padrão de pesquisa podem utilizar opcionalmente palavras-chave com caracteres wildcard (usando o '%'), que o motor de descoberta automatizado utiliza para identificar dados sensíveis nas suas bases de dados, com base nos metadados das colunas.
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="Configure um novo tipo de informação para a sua política de proteção de informação":::

1. Também pode configurar os tipos de **Informação** incorporada, adicionando cordas de padrão de pesquisa adicionais, desativando algumas das cordas existentes, ou alterando a descrição. Não é possível eliminar tipos de **informação** incorporada ou editar os seus nomes. 
1. **Os tipos de informação** são listados por ordem de classificação ascendente de descoberta, o que significa que os tipos mais altos da lista tentarão corresponder primeiro. Para alterar o ranking entre tipos de informação, arraste os tipos para o local certo da tabela ou use os botões **Move up** e **Move down** para alterar a ordem. 
1. Selecione **OK** quando terminar.
1. Depois de ter concluído a gestão dos seus tipos de informação, certifique-se de associar os tipos relevantes às etiquetas relevantes, clicando **em Configurar** para uma determinada etiqueta e adicionando ou eliminando tipos de informação conforme apropriado.
1. Para aplicar as suas alterações, **selecione Guardar** na página principal **do Rótulo.**
 

## <a name="exporting-and-importing-a-policy"></a>Exportação e importação de uma política

Pode descarregar um ficheiro JSON com as suas etiquetas e tipos de informação definidos, editar o ficheiro no editor à sua escolha e, em seguida, importar o ficheiro atualizado. 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="Exportar e importar a sua política de proteção da informação":::

> [!NOTE]
> Vai precisar de permissões de nível de inquilino para importar um ficheiro de apólice. 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>Gerir a proteção de informação SQL utilizando a Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): Recupera a política eficaz de proteção de informação sql do inquilino.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): Define a política eficaz de proteção da informação do inquilino SQL.
 

## <a name="next-steps"></a>Passos seguintes
 
Neste artigo, você aprendeu sobre a definição de uma política de proteção de informação SQL no Azure Security Center. Para saber mais sobre a utilização da SqL Information Protection para classificar e proteger dados sensíveis nas suas bases de dados SQL, consulte [a Azure SQL Database Data Discovery and Classification](../azure-sql/database/data-discovery-and-classification-overview.md). 

Para obter mais informações sobre políticas de segurança e segurança de dados no Azure Security Center, consulte os seguintes artigos:
 
- [Definição de políticas de segurança no Azure Security Center](tutorial-security-policy.md): Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos Azure
- Segurança de [dados do Azure Security Center](security-center-data-security.md): Saiba como o Security Center gere e salvaguarda dados