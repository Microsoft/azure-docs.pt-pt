---
title: Personalizar a proteção de informação SQL - Azure Security Center
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
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: aa73fed0af0d6cd7154118d8987f42e55814e25a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448936"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Personalize a política de proteção de informação SQL no Azure Security Center (Preview)
 
Você pode definir e personalizar uma política de proteção de informação SQL para todo o seu inquilino Azure, no Azure Security Center.

A proteção da informação é uma capacidade avançada de segurança para descobrir, classificar, rotular e reportar dados sensíveis nos seus recursos de dados Azure. Descobrir e classificar os seus dados mais sensíveis (negócios, financeiros, cuidados de saúde, dados pessoais, etc.) pode desempenhar um papel fundamental na sua estatura organizacional de proteção da informação. Pode funcionar como infraestrutura para:
- Ajudar a cumprir as normas de privacidade dos dados e os requisitos de conformidade regulamentar
- Cenários de segurança como monitorização (auditoria) e alertação sobre o acesso anómalo a dados sensíveis
- Controlar o acesso e endurecer a segurança das lojas de dados que contenham dados altamente sensíveis
 
[A SQL Information Protection](../azure-sql/database/data-discovery-and-classification-overview.md) implementa este paradigma para as suas lojas de dados SQL, atualmente suportadas pela Azure SQL Database. A SQL Information Protection descobre e classifica automaticamente dados potencialmente sensíveis, fornece um mecanismo de rotulagem para a marcação persistente dos dados sensíveis com atributos de classificação, e fornece um dashboard detalhado que mostra o estado de classificação da base de dados. Além disso, calcula a sensibilidade do conjunto de resultados das consultas SQL, de modo que as consultas de extrair dados sensíveis podem ser explicitamente auditadas, e os dados podem ser protegidos. Para obter mais informações sobre a Proteção de Informações [SQL, consulte Azure SQL Database Data Discovery and Classification](../azure-sql/database/data-discovery-and-classification-overview.md).
 
O mecanismo de classificação baseia-se em duas construções primárias que compõem a taxonomia de classificação - **Rótulos** e **Tipos de Informação**.
- **Etiquetas** – Os principais atributos de classificação, utilizados para definir o nível de sensibilidade dos dados armazenados na coluna. 
- **Tipos de Informação** – Fornece granularidade adicional ao tipo de dados armazenados na coluna.
 
A Proteção de Informação vem com um conjunto incorporado de rótulos e tipos de informação, que são utilizados por padrão. Para personalizar estes rótulos e tipos, pode personalizar a política de proteção de informação no Centro de Segurança.
 
## <a name="customize-the-information-protection-policy"></a>Personalizar a política de proteção predefinida
Para personalizar a política de proteção de informação para o seu inquilino Azure, você precisa ter [privilégios administrativos no grupo de gestão de raiz do inquilino.](security-center-management-groups.md) 
 
1. No menu principal do Centro de Segurança, em **termos de SEGURANÇA DE RECURSO A HIGIENE** aceda ao armazenamento de & de **dados** e clique no botão **SQL Information Protection.**

   ![Política de Configure de Proteção de Informação](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Na página **SQL Information Protection,** pode ver o seu conjunto atual de etiquetas. Estes são os principais atributos de classificação que são usados para categorizar o nível de sensibilidade dos seus dados. A partir daqui, pode configurar os rótulos e tipos de **Informação** de **Proteção de Informação** para o arrendatário. 
 
### <a name="customizing-labels"></a>Personalização de rótulos
 
1. Pode editar ou eliminar qualquer etiqueta existente ou adicionar uma nova etiqueta. Para editar uma etiqueta existente, selecione essa etiqueta e, em seguida, clique em **Configurar**, quer no topo, quer a partir do menu de contexto à direita. Para adicionar uma nova etiqueta, clique em **Criar etiqueta** na barra de menu superior ou na parte inferior da tabela de etiquetas.
2. No ecrã da etiqueta de **sensibilidade Configure,** pode criar ou alterar o nome da etiqueta e a descrição. Também pode definir se a etiqueta está ativa ou desativada, ligando ou desligando o interruptor **ativado.** Finalmente, pode adicionar ou remover tipos de informação associados à etiqueta. Quaisquer dados descobertos que correspondam a esse tipo de informação incluirão automaticamente o rótulo de sensibilidade associado nas recomendações de classificação.
3. Clique em **OK**.
 
   ![Etiqueta de sensibilidade de configuração](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. As etiquetas são listadas por ordem de sensibilidade ascendente. Para alterar o ranking entre as etiquetas, arraste as etiquetas para as reencomendar na tabela, ou utilize os botões **Move up** e **Move down** para alterar a ordem. 
 
    ![Lista de etiquetas](./media/security-center-info-protection-policy/move-up.png)
 
5. Certifique-se de clicar em **Guardar** na parte superior do ecrã quando terminar.
 
 
## <a name="adding-and-customizing-information-types"></a>Adicionar e personalizar tipos de informação
 
1. Pode gerir e personalizar tipos de informação clicando em **Gerir tipos de informação.**
2. Para adicionar um novo **tipo de Informação,** selecione **Criar tipo de informação** no menu superior. Pode configurar um nome, descrição e cadeias de padrão de pesquisa para o **tipo de informação**. As cordas de padrão de pesquisa podem utilizar opcionalmente palavras-chave com caracteres wildcard (usando o '%'), que o motor de descoberta automatizado utiliza para identificar dados sensíveis nas suas bases de dados, com base nos metadados das colunas.
 
    ![Criar tipo de informação](./media/security-center-info-protection-policy/info-types.png)
 
3. Também pode configurar os tipos de **Informação** incorporada, adicionando cordas de padrão de pesquisa adicionais, desativando algumas das cordas existentes, ou alterando a descrição. Não é possível eliminar tipos de **informação** incorporada ou editar os seus nomes. 
4. **Os tipos de informação** são listados por ordem de classificação ascendente de descoberta, o que significa que os tipos mais altos da lista tentarão corresponder primeiro. Para alterar o ranking entre tipos de informação, arraste os tipos para o local certo da tabela ou use os botões **Move up** e **Move down** para alterar a ordem. 
5. Clique **em OK** quando terminar.
6. Depois de ter concluído a gestão dos seus tipos de informação, certifique-se de associar os tipos relevantes às etiquetas relevantes, clicando **em Configurar** para uma determinada etiqueta e adicionando ou eliminando tipos de informação conforme apropriado.
7. Certifique-se de clicar em **Guardar** na lâmina principal **das etiquetas** para aplicar todas as alterações.
 
Após a sua política de proteção de informação estar totalmente definida e guardada, aplicar-se-á à classificação de dados em todas as bases de dados Azure SQL do seu inquilino.

## <a name="manage-sql-information-protection-using-azure-powershell"></a>Gerir a proteção de informação SQL utilizando a Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](https://docs.microsoft.com/powershell/module/az.security/get-azsqlinformationprotectionpolicy): Recupera a política eficaz de proteção de informação sql do inquilino.
- [Set-AzSqlInformationProtectionPolicy](https://docs.microsoft.com/powershell/module/az.security/set-azsqlinformationprotectionpolicy): Define a política eficaz de proteção da informação do inquilino SQL.
 
## <a name="next-steps"></a>Passos seguintes
 
Neste artigo, você aprendeu sobre a definição de uma política de proteção de informação SQL no Azure Security Center. Para saber mais sobre a utilização da SqL Information Protection para classificar e proteger dados sensíveis nas suas bases de dados SQL, consulte [a Azure SQL Database Data Discovery and Classification](../azure-sql/database/data-discovery-and-classification-overview.md). 

Para obter mais informações sobre políticas de segurança e segurança de dados no Azure Security Center, consulte os seguintes artigos:
 
- [Definição de políticas de segurança no Azure Security Center](tutorial-security-policy.md): Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos Azure
- Segurança de [dados do Azure Security Center](security-center-data-security.md): Saiba como o Security Center gere e salvaguarda dados
