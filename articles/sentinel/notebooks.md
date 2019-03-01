---
title: Capacidades de procura com blocos de notas na pré-visualização de sentinela de Azure | Documentos da Microsoft
description: Este artigo descreve como utilizar blocos de notas com as capacidades de buscar sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: c6202f3e945d817c5e8fd7caa340a3e237bc34bd
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993112"
---
# <a name="use-notebooks-to-hunt-for-anomalies"></a>Utilizar blocos de notas para procurar anomalias

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sentinel do Azure tira partido do poder dos blocos de notas interativos do Jupyter para fornecer informações e ações para investigar ou procurar anomalias de segurança no seu ambiente. Azure Sentinel vem pré-carregada com blocos de notas que foram desenvolvidos por analistas de segurança da Microsoft. Cada bloco de notas é a finalidade criada com um fluxo de trabalho independente para um caso de utilização específicos. Visualizações são incluídas em cada bloco de notas para exploração de dados mais rápida e detecção de ameaças. Personalizar os blocos de notas incorporados para atender às suas necessidades, criar novos blocos de notas do zero ou importar blocos de notas de sentinela a Azure "Comunidade do GitHub. Blocos de notas do Jupyter são importados como um projeto na página de blocos de notas do Azure – isso permite que o utilizador aceder a todos os seus blocos de notas do Azure sentinela num único local. Blocos de notas podem ser executados com um clique de um botão ou podem ser configurados pelo utilizador para corresponder ao seu ambiente.

A experiência totalmente integrada que permite que blocos de notas executar no armazenamento e computação na Cloud sem sobrecarga de manutenção subjacente. A capacidade de aproveitar o ecossistema de blocos de notas do Jupyter existente permite-lhe a modelos de origem da multidão, sem partilhar dados do cliente. 


Cada bloco de notas é hospedado em blocos de notas do Azure (atualmente em pré-visualização), um ambiente de desenvolvimento interativo na cloud do Azure. Blocos de anotações são sempre acessível, sempre disponível em qualquer browser e em qualquer lugar no mundo.  O Azure sentinela "blocos de notas incorporados para a investigação e buscar são clonados para um projeto que lhe pertence, e o que pode modificar e adaptar ao seu ambiente. Algumas dos blocos de notas mais populares incorporados são:

- **Alerta de investigação e buscar**: Este bloco de notas interativo permite uma rápida triagem de diferentes classes de alertas ao obter a atividade relacionada e enriquecer o alerta com a atividade associada e dados a partir do qual o alerta foi gerado.

- **Anfitrião de ponto final orientada hunting**: Permite-lhe hunt para detetar sinais de uma violação de segurança por desagregar as atividades relevantes de segurança associadas a um anfitrião de ponto final.  

- **Office entrar orientada caçar**: Permite-lhe hunt para inícios de sessão suspeitos ao visualizar as localizações geográficas de registos suspeitos, bem como a exibição de padrões de início de sessão invulgares derivados de dados do Office 365.

## <a name="run-a-notebook"></a>Executar um bloco de notas
No exemplo a seguir, executamos o bloco de notas para pesquisar por explorar as profunda de anomalias de localização para ver se alguém numa localização inesperada está a fazer algo na sua rede interno.

1. No portal do Azure sentinela, clique em **blocos de notas** e, em seguida, selecione qualquer um dos blocos de notas incorporados.
  
   ![Selecione o bloco de notas](./media/notebooks/select-notebook.png)

3.  Clique em **importação** para clonar o repositório do GitHub para o seu projeto de blocos de notas do Azure.
  ![Bloco de notas de importação](./media/notebooks/import1.png)
4.  Cada bloco de anotações orienta-o através dos passos para realizar uma busca ou a investigação. Modelos, bibliotecas e outras dependências e a configuração de conectividade para o Azure sentinela são automaticamente importados para habilitar a execução de um clique. Todas as bibliotecas necessárias para executar um bloco de notas e código são previamente carregadas. Pode começar imediatamente a execução de um bloco de notas no seu espaço de trabalho do Log Analytics sem qualquer configuração.

   ![Repositório de importação](./media/notebooks/import2.png)

5.  Explore, modificar e executar todas as notas de exemplo fornecidas. Estes podem ser utilizados como blocos de construção para vários cenários diferentes.

  ![Selecione o bloco de notas](./media/notebooks/import3.png)



## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a executar uma investigação de detecção com blocos de notas no Azure sentinela. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:

- [Proativamente hunt relativamente a ameaças](hunting.md)
- [Utilizar marcadores para salvar informações interessantes ao sorte!](bookmarks.md)