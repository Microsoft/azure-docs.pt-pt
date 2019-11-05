---
title: Recursos de busca usando blocos de anotações no Azure Sentinel | Microsoft Docs
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
ms.date: 11/04/2019
ms.author: rkarlin
ms.openlocfilehash: ba22cc3db0ca50a292ddef4d0d646f8578c15cd4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489128"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Use blocos de anotações do Jupyter para procurar ameaças de segurança

A base do Azure Sentinel é o armazenamento de dados; Ele combina consultas de alto desempenho, esquema dinâmico e escalas para grandes volumes de dados. O portal do Azure e todas as ferramentas do Azure Sentinel usam uma API comum para acessar esse armazenamento de dados. A mesma API também está disponível para ferramentas externas, como notebooks [Jupyter](https://jupyter.org/) e Python. Embora muitas tarefas comuns possam ser executadas no portal, o Jupyter estende o escopo do que você pode fazer com esses dados. Ele combina programação completa com uma enorme coleção de bibliotecas para aprendizado de máquina, visualização e análise de dados. Esses atributos tornam o Jupyter uma ferramenta atraente para investigação de segurança e busca.

![Bloco de anotações de exemplo](./media/notebooks/sentinel-notebooks-map.png)

Integramos a experiência do Jupyter à portal do Azure, facilitando a criação e a execução de notebooks para analisar seus dados. A biblioteca *Kqlmagic* fornece a cola que permite que você faça consultas do Azure Sentinel e execute-as diretamente dentro de um notebook. As consultas usam a [linguagem de consulta Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Vários notebooks, desenvolvidos por alguns dos analistas de segurança da Microsoft, são empacotados com o Azure Sentinel. Alguns desses notebooks são criados para um cenário específico e podem ser usados no estado em que se encontram. Outras são destinadas a exemplos para ilustrar técnicas e recursos que você pode copiar ou adaptar para uso em seus próprios blocos de anotações. Outros blocos de anotações também podem ser importados do GitHub da Comunidade do Azure Sentinel.

A experiência de Jupyter integrada usa [Azure notebooks](https://notebooks.azure.com/) para armazenar, compartilhar e executar blocos de anotações. Você também pode executar esses blocos de anotações localmente se tiver um ambiente de Python e Jupyter em seu computador ou em outros ambientes de JupterHub, como Azure Databricks.

Os notebooks têm dois componentes:

- A interface baseada em navegador na qual você insere e executa consultas e código e onde os resultados da execução são exibidos.
- Um *kernel* que é responsável por analisar e executar o próprio código. 

Em Azure Notebooks, por padrão, esse kernel é executado no *armazenamento e computação de nuvem gratuita*do Azure. Se os seus notebooks incluírem modelos ou visualizações de aprendizado de máquina complexos, considere o uso de recursos de computação mais avançados e dedicados, como DSVM ( [máquinas virtuais de ciência de dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) ). Os blocos de anotações em sua conta são mantidos privados, a menos que você opte por compartilhá-los.

Os notebooks do Azure Sentinel usam muitas bibliotecas de Python populares, como pandas, matplotlib, bokeh e outros. Há muitos outros pacotes python para você escolher, cobrindo áreas como:

- Visualizações e gráficos
- Processamento e análise de dados
- Estatísticas e computação numérica
- Aprendizado de máquina e aprendizado profundo

Também lançamos algumas ferramentas de segurança Jupyter de código aberto em um pacote chamado [msticpy](https://github.com/Microsoft/msticpy/). Esse pacote é usado em muitos dos Notebooks incluídos. As ferramentas Msticpy foram projetadas especificamente para ajudar na criação de notebooks para busca e investigação e estamos trabalhando ativamente em novos recursos e aprimoramentos.

Os notebooks iniciais incluem:

- **Investigação guiada-alertas de processo**: permite que você faça a triagem rapidamente de alertas analisando a atividade no host ou hosts afetados.
- **Busca guiada – Gerenciador de host do Windows**: permite que você explore a atividade da conta, execuções de processo, atividade de rede e outros eventos em um host.
- **Busca guiada-Office365-explorando**: busca por atividade suspeita do Office 365 em vários conjuntos de dados do Office 365.

O [repositório GitHub da Comunidade do Azure Sentinel](https://github.com/Azure/Azure-Sentinel) é o local para os notebooks futuros do Azure Sentinel criados pela Microsoft ou contribuídos pela Comunidade.

Para usar os notebooks, você deve ter uma conta de Azure Notebooks. Para obter mais informações, consulte [início rápido: entrar e definir uma ID de usuário](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) na documentação do Azure notebooks. Para criar essa conta, você pode usar a opção **inscrever-se no Azure notebooks** na barra de comandos no **Azure Sentinel-notebooks**:

> [!div class="mx-imgBorder"]
>![inscrever-se para Azure Notebooks opção](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

## <a name="view-available-notebooks-from-azure-sentinel"></a>Exibir blocos de anotações disponíveis do Azure Sentinel
 
1. No portal do Azure, navegue até **Azure Sentinel** > **gerenciamento de ameaças** > **notebooks**, onde você pode ver os blocos de anotações que o Azure Sentinel fornece. 

2. Selecione blocos de anotações individuais para ler suas descrições, tipos de dados necessários e fontes de dados. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![iniciar](./media/notebooks/sentinel-azure-notebooks-nolaunch.png) do bloco de anotações

3. Selecione **Iniciar bloco de anotações** para procurar os notebooks no [repositório GitHub da Comunidade do Azure Sentinel](https://github.com/Azure/Azure-Sentinel).

No momento, não é possível iniciar um bloco de anotações diretamente do Azure Sentinel. Em vez disso, use o procedimento a seguir que percorre a clonagem dos blocos de anotações no GitHub em um projeto Azure Notebooks.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Clonar blocos de anotações do Azure Sentinel para um novo projeto Azure Notebooks

Este procedimento cria um projeto Azure Notebooks para você, que contém os notebooks do Azure Sentinel. Em seguida, você pode executar os blocos de anotações como estão, ou fazer alterações neles e executá-los.

1. No portal do Azure, navegue até **Azure Sentinel** > **gerenciamento de ameaças** > **notebooks** e, em seguida, selecione **clonar blocos de anotações** na barra de comandos:
  
    > [!div class="mx-imgBorder"]
    >opção ![clonar blocos de anotações](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Quando a caixa de diálogo a seguir for exibida, selecione **importar** para clonar o repositório do GitHub em seu projeto de Azure notebooks. Se você não tiver uma conta de Azure Notebooks existente, será solicitado que você crie uma e entre.

   ![Importar bloco de anotações](./media/notebooks/sentinel-notebooks-clone.png)

3. Na caixa de diálogo **carregar repositório GitHub** , não selecione **clonar recursivamente** porque essa opção se refere a repositórios do GitHub vinculado. Para o nome do projeto, use o nome padrão ou digite um novo. Em seguida, clique em **importar** para iniciar a clonagem do conteúdo do GitHub, o que pode levar alguns minutos para ser concluído.

   ![Importar bloco de anotações](./media/notebooks/sentinel-create-project.png)

4. Abra o projeto que você acabou de criar e, em seguida, abra a pasta **blocos** de anotações para ver os blocos de anotações. Por exemplo:

   ![Importar repositório](./media/notebooks/sentinel-open-notebook1.png)

Em seguida, você pode executar os blocos de anotações do Azure Notebooks. Para retornar a esses blocos de anotações do Azure Sentinel, selecione **ir para seus blocos** de anotações na barra de comandos no **Azure Sentinel-notebooks**:

> [!div class="mx-imgBorder"]
>![ir para a opção de seus blocos de anotações](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Usando blocos de anotações para procurar

Cada Notebook orienta você pelas etapas para realizar uma busca ou investigação. As bibliotecas e outras dependências necessárias para o notebook podem ser instaladas no próprio bloco de anotações ou por meio de um procedimento de configuração simples. A configuração que vincula o seu projeto de bloco de anotações à sua assinatura do Azure Sentinel é automaticamente provisionada nas etapas anteriores.

1. Se ainda não estiver no Azure Notebooks, você poderá usar a opção **ir para o bloco de anotações** na barra de comandos no **Azure Sentinel-notebooks**:
    
    > [!div class="mx-imgBorder"]
    >![ir para a opção de seus blocos de anotações](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Em Azure Notebooks, selecione **meus projetos**, o projeto que contém os blocos de anotações do Azure Sentinel e, por fim, a pasta **blocos de anotações** .
    
2. Antes de abrir um bloco de anotações, lembre-se de que, por padrão, a computação gratuita é selecionada para executar os blocos de anotações:
    
   ![selecionar bloco de anotações](./media/notebooks/sentinel-open-notebook2.png)
    
    Se você tiver configurado uma DSVM (máquinas virtuais de ciência de dados) para usar conforme explicado na introdução, selecione o DSVM e autenticar antes de abrir o primeiro bloco de anotações. 

3. Selecione um bloco de anotações para abri-lo.
    
    Na primeira vez que você abrir um bloco de anotações, você poderá ser solicitado a selecionar uma versão do kernel. Se não for solicitado, você poderá selecionar a **versão do kernel no kernel >  ** **alterar kernel**e, em seguida, selecionar uma versão que seja pelo menos 3,6. A versão do kernel selecionada é exibida no canto superior direito da janela do notebook:
    
   ![selecionar bloco de anotações](./media/notebooks/sentinel-select-kernel.png)

4. Antes de fazer alterações no notebook que você baixou, é uma boa ideia fazer uma cópia do notebook original e trabalhar na cópia. Para fazer isso, selecione **arquivo** > **fazer uma cópia**. Trabalhar em cópias permite que você atualize com segurança para versões futuras de blocos de anotações sem substituir nenhum dos seus dados.
    
    Agora você está pronto para executar ou editar o bloco de anotações selecionado.

Recommendations

- Para obter uma breve introdução à consulta de dados no Azure Sentinel, examine o bloco de anotações [getstarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) na pasta do **bloco de anotações** principal. 

- Você encontrará blocos de anotações de exemplo adicionais na subpasta de **blocos de anotações de exemplo** . Esses blocos de anotações de exemplo foram salvos com dados, para que seja mais fácil ver a saída pretendida. É recomendável exibir esses blocos de anotações no [nbviewer](https://nbviewer.jupyter.org/). 

- A pasta **HOWTOs** contém blocos de anotações que descrevem, por exemplo: definir a versão padrão do Python, configurar um DSVM, criar indicadores do Azure Sentinel de um notebook e outros assuntos.

Os notebooks fornecidos são destinados como ferramentas úteis, como ilustrações e exemplos de código que você pode usar no desenvolvimento de seus próprios blocos de anotações.

Agradecemos comentários, sejam sugestões, solicitações de recursos, blocos de anotações contribuídos, relatórios de bugs ou melhorias e adições a blocos de anotações existentes. Acesse o [GitHub da Comunidade do Azure Sentinel](https://github.com/Azure/Azure-Sentinel) para criar um problema ou bifurcar e carregar uma contribuição.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a começar a usar o Jupyter notebooks no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- [Busca de ameaças de forma proativa](hunting.md)
- [Use indicadores para salvar informações interessantes durante a busca](bookmarks.md)
