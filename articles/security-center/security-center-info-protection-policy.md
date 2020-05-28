---
title: Personalizar proteção de informação SQL - Centro de Segurança Azure
description: Saiba personalizar as políticas de proteção de informação no Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: memildin
ms.openlocfilehash: 5120e7ca3a29f12066343e5c4a81d6e51278f9ef
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020409"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Personalize a política de proteção de informação SQL no Centro de Segurança Azure (Pré-visualização)
 
Você pode definir e personalizar uma política de proteção de informação SQL para todo o seu inquilino Azure, no Azure Security Center.

A proteção de informação é uma capacidade avançada de segurança para descobrir, classificar, rotular e reportar dados sensíveis nos seus recursos de dados do Azure. Descobrir e classificar os seus dados mais sensíveis (negócios, financeiros, cuidados de saúde, dados pessoais, etc.) pode desempenhar um papel fundamental na sua estatura organizacional de proteção de informação. Pode funcionar como infraestrutura para:
- Ajudar a cumprir as normas de privacidade dos dados e os requisitos de conformidade regulamentar
- Cenários de segurança como monitorização (auditoria) e alerta sobre acesso anómalo a dados sensíveis
- Controlar o acesso e reforçar a segurança das lojas de dados que contenham dados altamente sensíveis
 
[A SQL Information Protection](../azure-sql/database/data-discovery-and-classification-overview.md) implementa este paradigma para as suas lojas de dados SQL, atualmente suportadas para a Base de Dados Azure SQL. A SQL Information Protection descobre e classifica automaticamente dados potencialmente sensíveis, fornece um mecanismo de rotulagem para marcar persistentemente os dados sensíveis com atributos de classificação, e fornece um dashboard detalhado que mostra o estado de classificação da base de dados. Além disso, calcula a sensibilidade do conjunto de resultados das consultas SQL, de modo a que as consultas que extraem dados sensíveis possam ser explicitamente auditadas, e os dados possam ser protegidos. Para obter mais informações sobre a Proteção de Informação SQL, consulte a Descoberta e Classificação de Dados da Base de [Dados Azure SQL](../azure-sql/database/data-discovery-and-classification-overview.md).
 
O mecanismo de classificação baseia-se em duas construções primárias que compõem a taxonomia de classificação - **Rótulos** e Tipos de **Informação**.
- **Etiquetas** – Os principais atributos de classificação, utilizados para definir o nível de sensibilidade dos dados armazenados na coluna. 
- Tipos de **Informação** – Fornece granularidade adicional no tipo de dados armazenados na coluna.
 
A Proteção da Informação vem com um conjunto incorporado de etiquetas e tipos de informação, que são utilizados por padrão. Para personalizar estes rótulos e tipos, pode personalizar a política de proteção de informação no Security Center.
 
## <a name="customize-the-information-protection-policy"></a>Personalizar a política de proteção predefinida
Para personalizar a política de proteção de informação para o seu inquilino Azure, você precisa ter [privilégios administrativos no grupo de gestão de raiz do inquilino.](security-center-management-groups.md) 
 
1. No menu principal do Security Center, em **higiene** de SEGURANÇA DE RECURSOS vá ao **armazenamento de dados &** e clique no botão de **Proteção de Informação SQL.**

   ![Configurar a política de proteção de informação](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Na página de Proteção de **Informação SQL,** pode ver o seu conjunto atual de etiquetas. Estes são os principais atributos de classificação que são usados para categorizar o nível de sensibilidade dos seus dados. A partir daqui, pode configurar os rótulos de proteção de **informação** e os tipos de **informação** para o inquilino. 
 
### <a name="customizing-labels"></a>Etiquetas de personalização
 
1. Pode editar ou eliminar qualquer etiqueta existente, ou adicionar uma nova etiqueta. Para editar uma etiqueta existente, selecione essa etiqueta e, em seguida, clique em **Configurar**, seja na parte superior ou a partir do menu de contexto à direita. Para adicionar uma nova etiqueta, clique em **Criar** etiqueta na barra de menu superior ou na parte inferior da tabela de etiquetas.
2. No ecrã de etiqueta de **sensibilidade Configure,** pode criar ou alterar o nome da etiqueta e a descrição. Também pode definir se a etiqueta está ativa ou desativada ao ligar ou desligar o interruptor **ativado.** Por fim, pode adicionar ou remover tipos de informação associados à etiqueta. Quaisquer dados descobertos que correspondam a esse tipo de informação incluirão automaticamente o rótulo de sensibilidade associado nas recomendações de classificação.
3. Clique em **OK**.
 
   ![Configurar rótulo de sensibilidade](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. As etiquetas são listadas por ordem de sensibilidade ascendente. Para alterar o ranking entre etiquetas, arraste as etiquetas para as reencomendar na tabela ou utilize os botões **Mover para cima** e mover para **baixo** para alterar a ordem. 
 
    ![Configurar a política de proteção de informação](./media/security-center-info-protection-policy/move-up.png)
 
5. Certifique-se de clicar em **Guardar** na parte superior do ecrã quando terminar.
 
 
## <a name="adding-and-customizing-information-types"></a>Adicionar e personalizar tipos de informação
 
1. Pode gerir e personalizar tipos de informação clicando em tipos de **informação Gerir**.
2. Para adicionar um novo tipo de **Informação,** selecione Criar tipo de **informação** no menu superior. Pode configurar um nome, descrição e cadeias de padrão de pesquisa para o **tipo informação**. As cadeias de padrões de pesquisa podem utilizar opcionalmente palavras-chave com caracteres wildcard (utilizando o personagem '%'), que o motor de descoberta automatizado utiliza para identificar dados sensíveis nas suas bases de dados, com base nos metadados das colunas.
 
    ![Configurar a política de proteção de informação](./media/security-center-info-protection-policy/info-types.png)
 
3. Também pode configurar os tipos de **Informação** incorporados adicionando cadeias de padrões de pesquisa adicionais, desativando algumas das cordas existentes, ou alterando a descrição. Não é possível eliminar tipos de **informação** incorporados ou editar os seus nomes. 
4. Os tipos de **informação** são listados por ordem de classificação ascendente de descoberta, o que significa que os tipos mais altos da lista tentarão corresponder primeiro. Para alterar o ranking entre tipos de informação, arraste os tipos para o local certo da tabela ou utilize os botões **Mover para cima** e mover para **baixo** para alterar a ordem. 
5. Clique **bem** quando terminar.
6. Depois de ter concluído a gestão dos seus tipos de informação, certifique-se de associar os tipos relevantes aos rótulos relevantes, clicando em **Configurar** para um determinado rótulo e adicionando ou eliminando os tipos de informação conforme apropriado.
7. Certifique-se de clicar em **Guardar** na lâmina de **etiquetas** principais para aplicar todas as suas alterações.
 
Após a sua política de proteção de informação estar totalmente definida e guardada, aplicar-se-á à classificação de dados em todas as bases de dados Azure SQL no seu inquilino.
 
 
## <a name="next-steps"></a>Próximos passos
 
Neste artigo, aprendeu a definir uma política de proteção de informação SQL no Centro de Segurança Azure. Para saber mais sobre a utilização da Proteção de Informação SQL para classificar e proteger dados sensíveis nas suas bases de dados SQL, consulte a Descoberta e Classificação de Dados de Base de [Dados Azure SQL](../azure-sql/database/data-discovery-and-classification-overview.md). 

Para obter mais informações sobre políticas de segurança e segurança de dados no Centro de Segurança Azure, consulte os seguintes artigos:
 
- [Definição de políticas](tutorial-security-policy.md)de segurança no Azure Security Center : Saiba configurar políticas de segurança para as suas subscrições e grupos de recursos Do Azure
- [Segurança de dados do Azure Security Center](security-center-data-security.md): Saiba como o Centro de Segurança gere e salvaguarda dados
