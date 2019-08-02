---
title: Recursos de busca usando blocos de anotações na visualização do Azure Sentinel | Microsoft Docs
description: Este artigo descreve como usar blocos de anotações com os recursos de busca do Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6372a7958caf108903321e5ee87ea6bf1a42271c
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689529"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Use blocos de anotações do Jupyter para procurar ameaças de segurança

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A base do Azure Sentinel é o armazenamento de dados; Ele combina consultas de alto desempenho, esquema dinâmico e escalas para grandes volumes de dados. O portal do Azure Sentinel e todas as ferramentas do Azure Sentinel usam uma API comum para acessar esse armazenamento de dados. A mesma API também está disponível para ferramentas externas, como notebooks [Jupyter](https://jupyter.org/) e Python. Embora muitas tarefas comuns possam ser executadas no portal, o Jupyter estende o escopo do que você pode fazer com esses dados. Ele combina programação completa com uma enorme coleção de bibliotecas para aprendizado de máquina, visualização e análise de dados. Esses atributos tornam o Jupyter uma ferramenta atraente para investigação de segurança e busca.

![bloco de anotações de exemplo](./media/notebooks/sentinel-notebooks-map.png)

Nós integramos a experiência do Jupyter no portal do Azure Sentinel, facilitando a criação e a execução de notebooks para analisar seus dados. A biblioteca *Kqlmagic* fornece a cola que permite que você faça consultas do Azure Sentinel e execute-as diretamente dentro de um notebook. As consultas usam a [linguagem de consulta Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Vários notebooks, desenvolvidos por alguns dos analistas de segurança da Microsoft, são empacotados com o Azure Sentinel. Alguns desses notebooks são criados para um cenário específico e podem ser usados no estado em que se encontram. Outras são destinadas a exemplos para ilustrar técnicas e recursos que você pode copiar ou adaptar para uso em seus próprios blocos de anotações. Outros blocos de anotações também podem ser importados do GitHub da Comunidade do Azure Sentinel.

A experiência de Jupyter integrada usa [Azure notebooks](https://notebooks.azure.com/) para armazenar, compartilhar e executar blocos de anotações. Você também pode executar esses blocos de anotações localmente (se você tiver um ambiente de Python e Jupyter em seu computador) ou em outros ambientes de JupterHub, como Azure Databricks.

Os notebooks têm dois componentes:

- a interface baseada em navegador na qual você insere e executa consultas e código e onde os resultados da execução são exibidos.
- um *kernel* que é responsável por analisar e executar o próprio código. 

Em Azure Notebooks, esse kernel é executado em *armazenamento e computação de nuvem gratuita* do Azure por padrão. Se os seus notebooks incluírem modelos ou visualizações de aprendizado de máquina complexos, você deverá considerar o uso de recursos de computação mais avançados e dedicados, como DSVM ( [máquinas virtuais de ciência de dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) ). Os blocos de anotações em sua conta são mantidos privados, a menos que você opte por compartilhá-los.

Os notebooks do Azure Sentinel usam muitas bibliotecas de Python populares, como pandas, matplotlib, bokeh e outros. Há um grande número de outros pacotes python para sua escolha, cobrindo áreas como:

- visualizações e gráficos
- processamento e análise de dados
- estatísticas e computação numérica
- aprendizado de máquina e aprendizado profundo

Também lançamos algumas ferramentas de segurança Jupyter de código aberto em um pacote chamado [msticpy](https://github.com/Microsoft/msticpy/). Esse pacote é usado em muitos dos Notebooks incluídos. As ferramentas Msticpy foram projetadas especificamente para ajudar na criação de notebooks para busca e investigação e estamos trabalhando ativamente em novos recursos e aprimoramentos.

Os notebooks iniciais incluem:

- **Investigação guiada-alertas de processo**: Permite que você faça a triagem rapidamente de alertas analisando a atividade nos hosts afetados.
- **Busca guiada-explorador de host do Windows**: Permite explorar atividade de conta, execuções de processo, atividade de rede e outros eventos em um host.  
- **Busca guiada-Office365-explorando**: Procure uma atividade suspeita do Office 365 em vários conjuntos de dados do O365.

O [repositório GitHub da Comunidade do Azure Sentinel](https://github.com/Azure/Azure-Sentinel) é o local para os notebooks futuros do Azure Sentinel criados pela Microsoft ou contribuídos pela Comunidade.

## <a name="run-a-notebook"></a>Executar um bloco de anotações

No exemplo a seguir, criamos um projeto Azure Notebooks no portal do Azure Sentinel, preenchendo o projeto com blocos de anotações. Antes de usar esses notebooks, é uma boa ideia fazer uma cópia do bloco de anotações e trabalhar na cópia. Trabalhar em cópias permite que você atualize com segurança para versões futuras de blocos de anotações sem substituir nenhum dos seus dados.

1. No portal do Azure Sentinel, clique em **blocos de anotações** no menu de navegação. Para criar um novo projeto de Azure Notebooks, clique em **clonar blocos de anotações do Azure Sentinel** ou para abrir seus projetos de blocos de anotações existentes clique em **ir para seus blocos de anotações**.
  
   ![selecionar blocos de anotações](./media/notebooks/sentinel-azure-notebooks-home.png)

2. Se você escolheu **clonar blocos de anotações do Azure Sentinel** na etapa anterior, a caixa de diálogo a seguir será exibida. Clique em **importar** para clonar o repositório GitHub em seu projeto Azure notebooks. Se você não tiver uma conta de Azure Notebooks existente, será solicitado que você crie uma e entre.

   ![Importar bloco de anotações](./media/notebooks/sentinel-notebooks-clone.png)

3. Ao criar um novo projeto, você precisará nomear o projeto-Use o nome padrão ou digite um novo. Não marque a opção clonar recursivamente – essa opção se refere a repositórios do GitHub vinculado. Clicar em **importar** inicia a clonagem do conteúdo do GitHub, o que pode levar alguns minutos para ser concluído.

   ![Importar bloco de anotações](./media/notebooks/sentinel-create-project.png)

4. Abra a pasta **blocos** de anotações para ver os blocos de anotações. Cada Notebook orienta você pelas etapas para realizar uma busca ou investigação. As bibliotecas e outras dependências necessárias para o notebook podem ser instaladas no próprio bloco de anotações ou por meio de um procedimento de configuração simples. A configuração que vincula o seu projeto de bloco de anotações à sua assinatura do Azure Sentinel é automaticamente provisionada nas etapas anteriores. Seus blocos de anotações estão prontos para serem executados em seu espaço de trabalho Log Analytics do Azure Sentinel.

   ![Importar repositório](./media/notebooks/sentinel-open-notebook1.png)

5. Abra um bloco de anotações. A computação gratuita é selecionada por padrão para executar os blocos de anotações (realçado). Se você tiver configurado um DSVM para usar (veja acima), selecione o DSVM e autenticar antes de abrir o primeiro bloco de anotações. Clique em um bloco de anotações para abri-lo.

   ![selecionar bloco de anotações](./media/notebooks/sentinel-open-notebook2.png)

6. Selecionando a versão do Python. Quando você abre um notebook pela primeira vez, ele pode solicitar que você selecione uma versão do kernel. Caso contrário, selecione o kernel a ser usado da seguinte maneira. O Python 3,6 ou posterior deve ser o kernel selecionado (na parte superior direita da janela do bloco de anotações).

   ![selecionar bloco de anotações](./media/notebooks/sentinel-select-kernel.png)

Para obter uma breve introdução à consulta de dados no Azure Sentinel, examine o [](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) bloco de anotações getstarted na pasta do bloco de anotações principal. Blocos de anotações de exemplo adicionais podem ser encontrados na subpasta de **blocos de anotações de exemplo** . Os blocos de anotações de exemplo foram salvos com dados, para que seja mais fácil ver a saída pretendida (Recomendamos exibi-los em [nbviewer](https://nbviewer.jupyter.org/)). A pasta HOWTOs contém blocos de anotações que descrevem, por exemplo: definir a versão padrão do Python, configurar um DSVM, criar indicadores do Azure Sentinel de um notebook e outros assuntos.

Esses notebooks destinam-se a ferramentas úteis e ilustrações e exemplos de código que você pode usar no desenvolvimento de seus próprios blocos de anotações.

Agradecemos comentários, sejam sugestões, solicitações de recursos, blocos de anotações contribuídos, relatórios de bugs ou melhorias e adições a blocos de anotações existentes. Acesse o [GitHub da Comunidade do Azure Sentinel](https://github.com/Azure/Azure-Sentinel) para criar um problema ou bifurcar e carregar uma contribuição.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a começar a usar o Jupyter notebooks no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- [Busca de ameaças de forma proativa](hunting.md)
- [Use indicadores para salvar informações interessantes durante a busca](bookmarks.md)
