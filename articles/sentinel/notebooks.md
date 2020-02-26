---
title: Use cadernos com Azure Sentinel para a caça à segurança
description: Este artigo descreve como usar cadernos com as capacidades de caça Azure Sentinel.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: 84b72a71ed2de910bce44b0c3f3309782f096680
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77581842"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Use cadernos Jupyter para caçar ameaças à segurança

A fundação do Azure Sentinel é a loja de dados; combina consultas de alto desempenho, esquemadinâmico e escalas a volumes de dados maciços. O portal Azure e todas as ferramentas Azure Sentinel utilizam uma API comum para aceder a esta loja de dados. A mesma API também está disponível para ferramentas externas como os cadernos [Jupyter](https://jupyter.org/) e Python. Embora muitas tarefas comuns possam ser executadas no portal, jupyter estende o âmbito do que pode fazer com estes dados. Combina programabilidade completa com uma enorme coleção de bibliotecas para machine learning, visualização e análise de dados. Estes atributos fazem de Jupyter uma ferramenta convincente para a investigação de segurança e caça.

![portátil exemplo](./media/notebooks/sentinel-notebooks-map.png)

Integrámos a experiência Jupyter no portal Azure, facilitando a criação e execução de cadernos para analisar os seus dados. A biblioteca *Kqlmagic* fornece a cola que permite pegar nas consultas do Azure Sentinel e executá-las diretamente dentro de um caderno. As consultas usam a [linguagem de consulta kusto](https://kusto.azurewebsites.net/docs/query/index.html). Vários cadernos, desenvolvidos por alguns dos analistas de segurança da Microsoft, são embalados com o Azure Sentinel. Alguns destes cadernos são construídos para um cenário específico e podem ser usados como está. Outros destinam-se a ilustrar técnicas e características que pode copiar ou adaptar para utilização nos seus próprios cadernos. Outros cadernos também podem ser importados da comunidade Azure Sentinel GitHub.

A experiência jupyter integrada utiliza [cadernos Azure](https://notebooks.azure.com/) para armazenar, partilhar e executar cadernos. Você também pode executar estes cadernos localmente se você tem um ambiente Python e Jupyter no seu computador, ou em outros ambientes JupterHub, como Azure Databricks.

Os cadernos têm dois componentes:

- A interface baseada no navegador onde você entra e executa consultas e código, e onde os resultados da execução são apresentados.
- Um *núcleo* que é responsável por analisar e executar o código em si. 

Nos Cadernos Azure, por padrão, este núcleo funciona em Azure *Free Cloud Compute and Storage*. Se os seus cadernos incluirem modelos complexos de aprendizagem automática ou visualizações, considere usar recursos computacionais mais poderosos e [dedicados,](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) como máquinas virtuais de ciência de dados (DSVM). Os cadernos na sua conta são mantidos privados a menos que opte por partilhá-los.

Os cadernos Azure Sentinel usam muitas bibliotecas populares de Python, como pandas, matplotlib, bokeh, entre outros. Existem muitos outros pacotes Python para você escolher, cobrindo áreas como:

- Visualizações e gráficos
- Processamento e análise de dados
- Estatísticas e computação numérica
- Aprendizagem automática e aprendizagem profunda

Também lançámos algumas ferramentas de segurança jupyter de código aberto num pacote chamado [Msticpy.](https://github.com/Microsoft/msticpy/) Este pacote é usado em muitos dos cadernos incluídos. As ferramentas Msticpy são projetadas especificamente para ajudar na criação de cadernos para a caça e investigação e estamos trabalhando ativamente em novas funcionalidades e melhorias.

Os cadernos iniciais incluem:

- **Investigação guiada - Alertas**de Processo : Permite-lhe triagem rápida alertas analisando atividade no hospedeiro ou hospedeiros afetados.
- **Caça guiada - Explorador**de anfitriões windows : Permite-lhe explorar a atividade da conta, executar execuções de processos, atividade de rede e outros eventos num hospedeiro.
- **Caça guiada - Office365-Exploreing**: Hunt for suspect Office 365 activity in multiple Office 365 data sets.

O [repositório Azure Sentinel Community GitHub](https://github.com/Azure/Azure-Sentinel) é o local para quaisquer futuros cadernos Azure Sentinel construídos pela Microsoft ou contribuídos da comunidade.

Para utilizar os cadernos, deve ter uma conta De Cadernos Azure. Para mais informações, consulte [Quickstart: Inicie o seu acesso e detete teime um id](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) do utilizador a partir da documentação dos Cadernos Azure. Para criar esta conta, pode utilizar a opção **'Sign up' para cadernos Azure** a partir da barra de comando **sintetizadora em Azure Sentinel - Notebooks:**

> [!div class="mx-imgBorder"]
>![Inscreva-se na opção Deporporna Azure](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Você pode executar um caderno diretamente de Azure Sentinel, ou clonar todos os cadernos Azure Sentinel para um novo projeto Denotebooks Azure.

## <a name="run-a-notebook-from-azure-sentinel"></a>Executar um caderno de Azure Sentinel
 
1. Do portal Azure, navegue até > **à** **gestão** de > de  Desemposição de  **Cadernos,** onde pode ver cadernos que o Azure Sentinel fornece. 

2. Selecione cadernos individuais para ler as suas descrições, tipos de dados necessários e fontes de dados. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![lançamento de](./media/notebooks/sentinel-azure-notebooks-launch.png) de caderno

3. Selecione o caderno que pretende utilizar e, em seguida, selecione **Launch Notebook (Preview)** para clonar e configurar o caderno num novo projeto de Cadernos Azure que se conecta ao seu espaço de trabalho Azure Sentinel. Quando o processo estiver concluído, o caderno abre dentro dos Cadernos Azure para que possa executar.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Cadernos Clone Azure Sentinel para um novo projeto de Cadernos Azure

Este procedimento cria um projeto de Cadernos Azure para si, que contém os cadernos Azure Sentinel. Em seguida, pode executar os cadernos como está, ou fazer alterações neles e, em seguida, executá-los.

1. A partir do portal Azure, navegue até à **gestão** de > ameaça **do Azure Sentinel** > **Cadernos** e, em seguida, selecione **Cadernos Clone** da barra de comando:
  
    > [!div class="mx-imgBorder"]
    >![opção Cadernos Clone](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Quando aparecer o seguinte diálogo, selecione **Import** para clonar o repo GitHub no seu projeto Deporporporna Azure. Se não tiver uma conta de Cadernos Azure existente, será solicitado a criar um e iniciar sessão.

   ![Caderno de importação](./media/notebooks/sentinel-notebooks-clone.png)

3. Na caixa de diálogo **repositório Upload GitHub,** não selecione **Clone de forma recursiva** porque esta opção se refere a repos gitHub ligados. Para o nome do projeto, utilize o nome ou o tipo por defeito num novo. Em seguida, clique **em Importar** para começar a clonar o conteúdo GitHub, que pode demorar alguns minutos a concluir.

   ![Caderno de importação](./media/notebooks/sentinel-create-project.png)

4. Abra o projeto que acabou de criar e, em seguida, abra a pasta **Cadernos** para ver os cadernos. Por exemplo:

   ![Repo importa](./media/notebooks/sentinel-open-notebook1.png)

Em seguida, pode executar os cadernos dos Cadernos Azure. Para voltar a estes cadernos do Azure Sentinel, selecione **Ir aos seus Cadernos** a partir do bar de comandos em **Azure Sentinel - Notebooks:**

> [!div class="mx-imgBorder"]
>![vá à sua opção de Cadernos](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Usando cadernos para caçar

Cada caderno acompanha-o através dos degraus para realizar uma caça ou investigação. As bibliotecas e outras dependências necessárias pelo caderno podem ser instaladas a partir do próprio caderno ou através de um simples procedimento de configuração. A configuração que liga o seu projeto de portátil à subscrição do Azure Sentinel é automaticamente disponibilizada nos passos anteriores.

1. Se ainda não estiver nos Cadernos Azure, pode utilizar a opção Ir para a sua opção **Deporporado** a partir do bar de comandos em **Azure Sentinel - Notebooks**:
    
    > [!div class="mx-imgBorder"]
    >![vá à sua opção de Cadernos](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Nos Cadernos Azure, selecione **My Projects**, depois o projeto que contém os cadernos Azure Sentinel e, finalmente, a pasta **Cadernos.**
    
2. Antes de abrir um caderno, esteja ciente de que, por padrão, a Computação Gratuita é selecionada para executar os cadernos:
    
   ![caderno selecionado](./media/notebooks/sentinel-open-notebook2.png)
    
    Se configurar uma Máquina Virtual de Ciência de Dados (DSVM) para usar como explicado na introdução, selecione o DSVM e autenticar antes de abrir o primeiro caderno. 

3. Selecione um caderno para o abrir.
    
    A primeira vez que abrir um caderno, pode ser solicitado a selecionar uma versão kernel. Se não for solicitado, pode selecionar a versão kernel de **Kernel** >  **Alterar kernel**, e, em seguida, selecionar uma versão que seja pelo menos 3.6. A versão kernel selecionada é exibida na parte superior direita da janela do caderno:
    
   ![caderno selecionado](./media/notebooks/sentinel-select-kernel.png)

4. Antes de fazer qualquer alteração ao caderno que descarregou, é uma boa ideia fazer uma cópia do caderno original e trabalhar na cópia. Para isso, selecione **File** > **Fazer uma Cópia**. Trabalhar em cópias permite-lhe atualizar com segurança para futuras versões de cadernos sem sobrepor nenhum dos seus dados.
    
    Está agora pronto para executar ou editar o caderno selecionado.

Recomendações:

- Para uma rápida introdução aos dados de consulta no Azure Sentinel, veja o caderno [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) na pasta principal dos **Cadernos.** 

- Encontrará cadernos de amostra adicionais na subpasta **Sample-Notebooks.** Estes cadernos de amostraforam guardados com dados, de modo que é mais fácil ver a saída pretendida. Recomendamos visualizar estes cadernos no [nbviewer](https://nbviewer.jupyter.org/). 

- A pasta **HowTos** contém cadernos que descrevem, por exemplo: Definição da versão padrão python, configurando um DSVM, criando marcadores Azure Sentinel a partir de um caderno, e outros assuntos.

Os cadernos fornecidos destinam-se tanto a ferramentas úteis como a ilustrações e amostras de código que pode utilizar no desenvolvimento dos seus próprios cadernos.

Congratulamo-nos com o feedback, sejam sugestões, pedidos de funcionalidades, cadernos contribuídos, relatórios de bugs ou melhorias e adições aos cadernos existentes. Vá ao [Azure Sentinel Community GitHub](https://github.com/Azure/Azure-Sentinel) para criar um problema ou garfo e faça upload de uma contribuição.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a começar a usar cadernos Jupyter em Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- [Caça proativamente por ameaças](hunting.md)
- [Use marcadores para guardar informações interessantes durante a caça](bookmarks.md)
