---
title: Personalizar a proteção de informações do SQL – central de segurança do Azure
description: Saiba como personalizar as políticas de proteção de informações na central de segurança do Azure.
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
ms.openlocfilehash: d37333c0ca3f8acab7a35c23bbab67beef056b72
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664395"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Personalizar a política de proteção de informações do SQL na central de segurança do Azure (versão prévia)
 
Você pode definir e personalizar uma política de proteção de informações do SQL para todo o seu locatário do Azure, na central de segurança do Azure.

A proteção de informações é uma funcionalidade de segurança avançada para descobrir, classificar, rotular e proteger dados confidenciais em seus recursos de dados do Azure. Descobrir e classificar seus dados mais confidenciais (negócios, financeiros, de saúde, dados pessoais etc.) pode desempenhar uma função dinâmica em sua estatura de proteção de informações organizacionais. Ele pode servir como uma infraestrutura para:
- Ajudando a cumprir os padrões de privacidade de dados e os requisitos de conformidade regulatória
- Cenários de segurança, como monitoramento (auditoria) e alertas de acesso anormal a dados confidenciais
- Controlando o acesso e protegendo a segurança de armazenamentos de dados que contêm dados altamente confidenciais
 
A [proteção de informações do SQL](../sql-database/sql-database-data-discovery-and-classification.md) implementa esse paradigma para seus armazenamentos de dados SQL, atualmente com suporte no banco de dado SQL do Azure. A proteção de informações do SQL descobre e classifica automaticamente dados potencialmente confidenciais, fornece um mecanismo de rotulagem para marcar de forma persistente os dados confidenciais com atributos de classificação e fornece um painel detalhado que mostra o Estado de classificação do banco de dados. Além disso, ele calcula a sensibilidade do conjunto de resultados de consultas SQL, para que as consultas que extraem dados confidenciais possam ser auditadas explicitamente e os dados possam ser protegidos. Para obter mais informações sobre a proteção de informações do SQL, consulte [classificação e descoberta de dados do banco de dados SQL do Azure](../sql-database/sql-database-data-discovery-and-classification.md).
 
O mecanismo de classificação baseia-se em duas construções principais que compõem os **Rótulos** de taxonomia de classificação e os **tipos de informações**.
- **Rótulos** – os principais atributos de classificação, usados para definir o nível de sensibilidade dos dados armazenados na coluna. 
- **Tipos de informações** – fornece granularidade adicional ao tipo de dados armazenados na coluna.
 
A proteção de informações vem com um conjunto interno de rótulos e tipos de informações, que são usados por padrão. Para personalizar esses rótulos e tipos, você pode personalizar a política de proteção de informações na central de segurança.
 
## <a name="customize-the-information-protection-policy"></a>Personalizar a política de proteção predefinida
Para personalizar a política de proteção de informações para seu locatário do Azure, você precisa ter [privilégios administrativos no grupo de gerenciamento raiz do locatário](security-center-management-groups.md). 
 
1. No menu principal da central de segurança, em **higiene de segurança de recursos** , acesse **dados & armazenamento** e clique no botão proteção de informações do **SQL** .

   ![Configurar política de proteção de informações](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Na página **proteção de informações do SQL** , você pode exibir seu conjunto atual de rótulos. Esses são os principais atributos de classificação que são usados para categorizar o nível de sensibilidade de seus dados. A partir daqui, você pode configurar os **Rótulos de proteção de informações** e os tipos de **informações** para o locatário. 
 
### <a name="customizing-labels"></a>Personalizando rótulos
 
1. Você pode editar ou excluir qualquer rótulo existente ou adicionar um novo rótulo. Para editar um rótulo existente, selecione esse rótulo e, em seguida, clique em **Configurar**, na parte superior ou no menu de contexto à direita. Para adicionar um novo rótulo, clique em **criar rótulo** na barra de menus superior ou na parte inferior da tabela de rótulos.
2. Na tela **Configurar rótulo de sensibilidade** , você pode criar ou alterar o nome do rótulo e a descrição. Você também pode definir se o rótulo está ativo ou desabilitado alternando a opção **habilitada** ativar ou desativar. Por fim, você pode adicionar ou remover tipos de informações associados ao rótulo. Todos os dados descobertos que correspondem a esse tipo de informação incluirão automaticamente o rótulo de sensibilidade associado nas recomendações de classificação.
3. Clique em **OK**.
 
   ![Configurar rótulo de sensibilidade](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Os rótulos são listados na ordem de sensibilidade crescente. Para alterar a classificação entre os rótulos, arraste os rótulos para reordená-los na tabela ou use os botões **mover para cima** e **mover para baixo** para alterar a ordem. 
 
    ![Configurar política de proteção de informações](./media/security-center-info-protection-policy/move-up.png)
 
5. Certifique-se de clicar em **salvar** na parte superior da tela quando tiver terminado.
 
 
## <a name="adding-and-customizing-information-types"></a>Adicionando e personalizando tipos de informações
 
1. Você pode gerenciar e personalizar tipos de informações clicando em **gerenciar tipos de informações**.
2. Para adicionar um novo **tipo de informação**, selecione **criar tipo de informação** no menu superior. Você pode configurar um nome, uma descrição e as cadeias de caracteres de padrão de pesquisa para o **tipo de informações**. As cadeias de caracteres de padrão de pesquisa podem, opcionalmente, usar palavras-chave com curingas (usando o caractere "%"), que o mecanismo de descoberta automatizado usa para identificar dados confidenciais em seus bancos, com base nos metadados das colunas.
 
    ![Configurar política de proteção de informações](./media/security-center-info-protection-policy/info-types.png)
 
3. Você também pode configurar os **tipos de informações** internas adicionando cadeias de caracteres de padrão de pesquisa adicionais, desabilitando algumas das cadeias de caracteres existentes ou alterando a descrição. Você não pode excluir tipos de **informações** internas ou editar seus nomes. 
4. Os **tipos de informações** são listados em ordem crescente de classificação de descoberta, o que significa que os tipos mais altos na lista tentarão corresponder primeiro. Para alterar a classificação entre os tipos de informações, arraste os tipos para o local certo na tabela ou use os botões **mover para cima** e mover para **baixo** para alterar a ordem. 
5. Clique em **OK** quando terminar.
6. Depois de concluir o gerenciamento dos tipos de informações, lembre-se de associar os tipos relevantes aos rótulos relevantes, clicando em **Configurar** para um rótulo específico e adicionando ou excluindo tipos de informações conforme apropriado.
7. Certifique-se de clicar em **salvar** na folha **Rótulos** principais para aplicar todas as suas alterações.
 
Depois que a política de proteção de informações estiver totalmente definida e salva, ela será aplicada à classificação de dados em todos os bancos de dado SQL do Azure em seu locatário.
 
 
## <a name="next-steps"></a>Passos seguintes
 
Neste artigo, você aprendeu a definir uma política de proteção de informações do SQL na central de segurança do Azure. Para saber mais sobre como usar a proteção de informações do SQL para classificar e proteger dados confidenciais nos bancos de dados SQL, confira [descoberta e classificação de dado do Azure SQL Database](../sql-database/sql-database-data-discovery-and-classification.md). 

Para obter mais informações sobre políticas de segurança e segurança de dados na central de segurança do Azure, consulte os seguintes artigos:
 
- [Configurando políticas de segurança na central de segurança do Azure](tutorial-security-policy.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure
- [Segurança de dados da central de segurança do Azure](security-center-data-security.md): saiba como a central de segurança gerencia e protege os dados