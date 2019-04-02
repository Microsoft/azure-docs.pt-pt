---
title: Nível de compatibilidade no Azure Analysis Services do modelo de dados | Documentos da Microsoft
description: Noções básicas sobre o nível de compatibilidade do modelo de dados tabulares.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 67a6c99253c549f0b8d3b55809b35b81756843eb
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803498"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Nível de compatibilidade para modelos em tabela do Analysis Services

*Nível de compatibilidade* refere-se a versão específica comportamentos no motor do Analysis Services. Normalmente, coincidir alterações para o nível de compatibilidade com versões principais do SQL Server. Essas alterações também são implementadas no Azure Analysis Services para manter a paridade entre as duas plataformas. Nível de compatibilidade também altera as funcionalidades de efeito disponíveis nos seus modelos em tabela. Por exemplo, DirectQuery e metadados do objeto em tabela têm implementações diferentes dependendo do nível de compatibilidade. Nível de compatibilidade é especificado no projeto de modelo em tabela no Visual Studio (SSDT).

O Azure Analysis Services suporta modelos em tabela nos níveis de compatibilidade 1200 e 1400. O nível de compatibilidade mais recente é 1400. Este nível coincide com o SQL Server 2017 Analysis Services. Principais recursos do nível de compatibilidade 1400 incluem:

*  Novos recursos para conectividade de dados e importação com suporte para as APIs de JOSÉ e scripts TMSL. 
*  Transformação de dados e recursos de mashup de dados usando expressões de obter dados e M.
*  Medidas suportam uma propriedade de linhas de detalhe com uma expressão DAX. Esta propriedade permite que as ferramentas de cliente, como o Microsoft Excel para desagregar dados detalhados a partir de um relatório de agregados. Por exemplo, quando os usuários exibiam total de vendas para uma região e o mês, pode ver os detalhes do pedido associados. 
*  Segurança de nível de objeto para nomes de tabela e coluna, além dos dados dentro dos mesmos.
*  Suporte avançado para hierarquias desbalanceadas.
*  Monitoramento de desempenho e melhorias.

> [!NOTE]
> O Azure Analysis Services suporta ficheiros do Power BI Desktop importados no nível de compatibilidade 1465. No entanto, a importação da funcionalidade do Power BI Desktop, que sempre se encontra uma funcionalidade de pré-visualização, foi descontinuada e removida do serviço em Março de 2019. Modelos existentes no nível de compatibilidade 1465 ser suportados.  


## <a name="set-compatibility-level"></a>Nível de compatibilidade de conjunto

 Ao criar um novo projeto de modelo em tabela no SSDT, pode especificar o nível de compatibilidade no **model designer em tabela** caixa de diálogo. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 Se selecionar a **não mostrar esta mensagem novamente** opção, todos os projetos subseqüentes utilizam o nível de compatibilidade que especificou como predefinição. Pode alterar o nível de compatibilidade predefinido no SSDT, no **ferramentas** > **opções**.  
  
 Para atualizar um projeto de modelo em tabela existente no SSDT, defina o **nível de compatibilidade** propriedade no modelo **propriedades** janela. Lembre-se, atualizar o nível de compatibilidade é irreversível.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Nível de compatibilidade de verificação para uma base de dados de modelo em tabela no SQL Server Management Studio 

 No SSMS, faça duplo clique o nome de base de dados > **propriedades** > **nível de compatibilidade**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Verifique o nível de compatibilidade suportado para um servidor no SSMS  

 No SSMS, faça duplo clique no nome do servidor > **propriedades** > **nível de compatibilidade suportado**.  
  
 Esta propriedade especifica o maior nível de compatibilidade de uma base de dados que vai ser executado no servidor (excluindo a pré-visualização). Não é possível alterar o nível de compatibilidade suportado.  

> [!NOTE]
> No SSMS, quando estiver ligado a um servidor Azure Analysis Services, o **nível de compatibilidade suportado** mostrará a propriedade **1200**. Este é um problema conhecido e será resolvido numa futura SSMS atualização. Quando resolvido, esta propriedade irá mostrar o mais alto nível de compatibilidade suportado.

## <a name="next-steps"></a>Passos Seguintes

  [Gerir os serviços de análise](analysis-services-manage.md)  
