---
title: Personalizar a política de proteção de informações de SQL no Centro de segurança do Azure | Documentos da Microsoft
description: Saiba como personalizar as políticas de proteção de informações no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 9b63fb963408b8f22453c7ea78e36a49402273a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60906167"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Personalizar a política de proteção de informações de SQL no Centro de segurança do Azure (pré-visualização)
 
Uma política de proteção de informações do SQL pode ser definida e personalizada para o inquilino do Azure completo, no Centro de segurança do Azure.

Proteção de informações é um recurso de segurança avançadas para detetar, classificar, Etiquetar e proteger dados confidenciais em seus recursos de dados do Azure. A detetar e classificar os dados mais confidenciais (comerciais, financeiros, saúde, PII, etc.) pode desempenham um papel essencial no seu escritor de proteção de informações da organização. Que possa servir como a infraestrutura para:
- Ajudar a atender a requisitos de conformidade a normas e de normas de privacidade de dados
- Vários cenários de segurança, como monitorização (auditoria) e os alertas no anómalo acesso a dados confidenciais
- Armazena, controlar o acesso ao e a segurança dos dados de sistema de proteção que contêm dados altamente confidenciais
 
[Proteção de informações de SQL](../sql-database/sql-database-data-discovery-and-classification.md) implementa esse paradigma para seus arquivos de dados SQL, atualmente, suportado para a base de dados do Azure SQL. Proteção de informações de SQL automaticamente Deteta e classifica os dados potencialmente confidenciais, fornece um mecanismo de etiquetagem de forma permanente os dados confidenciais com atributos de classificação de marcação e fornece a mostrar um dashboard detalhadas a Estado de classificação da base de dados. Além disso, ele calcula o resultado definido sensibilidade das consultas SQL, para que as consultas que extraem dados confidenciais podem ser auditadas explicitamente, e os dados podem ser protegidos. Para obter mais detalhes sobre a proteção de informações de SQL, consulte [deteção de dados do Azure SQL da base de dados e classificação](../sql-database/sql-database-data-discovery-and-classification.md).
 
O mecanismo de classificação baseia-se em duas construções primárias que compõem a taxonomia de classificação - **etiquetas** e **tipos de informações**.
- **Etiquetas** – os atributos de classificação principal, utilizados para definir o nível de sensibilidade dos dados armazenados na coluna. 
- **Tipos de informações** – fornece a granularidade adicional para o tipo de dados armazenados na coluna.
 
Proteção de informações é fornecido com um conjunto interno de etiquetas e tipos de informações, que são utilizados por predefinição. Para personalizá-los, pode personalizar a política de proteção de informações no Centro de segurança do Azure.
 
## <a name="customize-the-information-protection-policy"></a>Personalizar a política de proteção predefinida
Para personalizar a política de proteção de informações para o seu inquilino do Azure, tem de ter [privilégios administrativos no grupo de gestão de raiz do inquilino](security-center-management-groups.md). 
 
1. No menu principal do Centro de segurança, selecione **política de segurança**.
2. Escolher **vista hierárquica (pré-visualização)** e, em **grupo de raiz de inquilino**, clique em **editar definições**.
 
   ![Configurar a política de proteção de informações](./media/security-center-info-protection-policy/security-policy.png) 
 
3. Sob **componentes da política**, clique em **proteção de informações**. Na **definições de proteção de informações** página, pode ver o conjunto atual de etiquetas. Estes são os atributos de classificação principal que são utilizados para categorizar o nível de sensibilidade dos seus dados. A partir daqui, pode configurar o **etiquetas de proteção de informações** e **tipos de informações** para o inquilino. 
 
### <a name="customizing-labels"></a>Personalizando as etiquetas
 
1. Pode editar ou eliminar qualquer etiqueta existente ou adicionar uma nova etiqueta. Para editar uma etiqueta existente, selecione essa etiqueta e, em seguida, clique em **configurar**, na parte superior ou no menu de contexto à direita. Para adicionar uma nova etiqueta, clique em **criar etiqueta** na barra de menus superior, ou na parte inferior da tabela de etiquetas.
2. Na **etiqueta de sensibilidade configurar** ecrã, pode criar ou alterar o nome de etiqueta e a descrição. Também pode definir se a etiqueta está desativada ou Active Directory, Ativando a **ativado** mudar ou desativar. Por fim, pode adicionar ou remover tipos de informações associados com a etiqueta. Todos os dados detetados que corresponda ao que tipo de informações incluirá automaticamente a etiqueta de sensibilidade associado nas recomendações de classificação.
3. Clique em **OK**.
 
   ![Configurar etiqueta de confidencialidade](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. As etiquetas são listadas por ordem de ascendente sensibilidade. Para alterar a classificação entre as etiquetas, arraste as etiquetas para reordenar na tabela ou utilize o **mover para cima** e **mover para baixo** botões para alterar a ordem. 
 
    ![Configurar a política de proteção de informações](./media/security-center-info-protection-policy/move-up.png)
 
5. Certifique-se de que clique em **guardar** na parte superior do ecrã quando tiver terminado.
 
 
## <a name="adding-and-customizing-information-types"></a>Adicionando e personalizando tipos de informações
 
1. Pode gerir e personalizar os tipos de informações ao clicar em **gerir tipos de informações**.
2. Para adicionar um novo **tipo de informação**, selecione **criar o tipo de informações** no menu superior. Pode configurar um nome, descrição e pesquisar cadeias de caracteres padrão para o **tipo de informações**. Cadeias de caracteres de padrão de pesquisa, opcionalmente, podem utilizar palavras-chave com carateres universais (usando o caráter '%'), que o mecanismo de descoberta automática utiliza para identificar os dados confidenciais em bancos de dados, com base nos metadados das colunas.
 
    ![Configurar a política de proteção de informações](./media/security-center-info-protection-policy/info-types.png)
 
3. Também pode configurar o incorporado **tipos de informações** adicionando adicionais de pesquisa padrão cadeias de caracteres, desativar algumas das cadeias de caracteres existentes, ou ao alterar a descrição. Não é possível eliminar incorporado **tipos de informações** ou editar os respetivos nomes. 
4. **Tipos de informações** estão listados por ordem de ascendente de classificação de deteção, o que significa que os tipos mais altos na lista irão tentar corresponder ao primeiro. Para alterar a classificação entre tipos de informação, arraste os tipos no lugar certo na tabela ou utilizar o **mover para cima** e **mover para baixo** botões para alterar a ordem. 
5. Clique em **OK** quando tiver terminado.
6. Depois de concluída a gerir os seus tipos de informações, certifique-se de que associa os tipos relevantes etiquetas relevantes, ao clicar em **configurar** para uma etiqueta específica e adicionar ou eliminar a tipos de informações conforme apropriado.
7. Certifique-se de que clique em **salvar** no principal **etiquetas** painel para aplicar todas as alterações.
 
Depois da política de proteção de informações é totalmente definida e guardada, será aplicada à classificação de dados em todas as bases de dados SQL do Azure no seu inquilino.
 
 
## <a name="next-steps"></a>Passos Seguintes
 
Neste artigo, aprendeu sobre como definir uma política de proteção de informações de SQL no Centro de segurança do Azure. Para saber mais sobre como utilizar a proteção de informações de SQL para classificar e proteger dados confidenciais em seus bancos de dados SQL, veja [deteção de dados do Azure SQL da base de dados e classificação](../sql-database/sql-database-data-discovery-and-classification.md). 

Para obter mais informações sobre políticas de segurança e a segurança de dados no Centro de segurança do Azure, consulte os artigos seguintes:
 
- [Descrição geral das políticas de segurança](security-center-policies-overview.md): Obtenha uma visão geral das políticas de segurança no Centro de segurança
- [Definir políticas de segurança no Centro de segurança do Azure](tutorial-security-policy.md): Saiba como configurar políticas de segurança para as suas subscrições do Azure e grupos de recursos
- [Segurança de dados do Centro de segurança do Azure](security-center-data-security.md): Saiba como o Centro de segurança gere e protege os dados


