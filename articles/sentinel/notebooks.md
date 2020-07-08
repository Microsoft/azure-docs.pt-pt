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
ms.openlocfilehash: bf63d5c8cb46fd791508af40dcefd7b39d4ba9de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83652022"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Use cadernos Jupyter para caçar ameaças à segurança

A fundação do Azure Sentinel é a loja de dados; combina consulta de alto desempenho, esquema dinâmico e escalas a volumes de dados maciços. O portal Azure e todas as ferramentas Azure Sentinel utilizam uma API comum para aceder a esta loja de dados. A mesma API também está disponível para ferramentas externas, como cadernos [Jupyter](https://jupyter.org/) e Python. Embora muitas tarefas comuns possam ser realizadas no portal, Jupyter alarga o âmbito do que você pode fazer com estes dados. Combina a programabilidade total com uma enorme coleção de bibliotecas para machine learning, visualização e análise de dados. Estes atributos fazem de Jupyter uma ferramenta convincente para a investigação de segurança e caça.

![caderno exemplo](./media/notebooks/sentinel-notebooks-map.png)

Integrámos a experiência Jupyter no portal Azure, facilitando-lhe a criação e execução de cadernos para analisar os seus dados. A biblioteca *Kqlmagic* fornece a cola que permite tirar consultas de Azure Sentinel e executá-las diretamente dentro de um caderno. As consultas utilizam a [língua de consulta kusto.](https://kusto.azurewebsites.net/docs/query/index.html) Vários cadernos, desenvolvidos por alguns dos analistas de segurança da Microsoft, são embalados com Azure Sentinel. Alguns destes cadernos são construídos para um cenário específico e podem ser usados como-é. Outros destinam-se a amostras para ilustrar técnicas e funcionalidades que pode copiar ou adaptar para uso nos seus próprios cadernos. Outros cadernos também podem ser importados da comunidade Azure Sentinel GitHub.

A experiência jupyter integrada usa [cadernos Azure](https://notebooks.azure.com/) para armazenar, partilhar e executar cadernos. Você também pode executar estes cadernos localmente se você tem um ambiente Python e Jupyter no seu computador, ou em outros ambientes JupterHub, como Azure Databricks.

Os cadernos têm dois componentes:

- A interface baseada no navegador onde você entra e executa consultas e código, e onde os resultados da execução são exibidos.
- Um *núcleo* responsável por analisar e executar o código em si. 

Nos Cadernos Azure, por padrão, este núcleo funciona em Azure *Free Cloud Compute and Storage*. Se os seus cadernos incluem modelos complexos de aprendizagem automática ou visualizações, considere usar recursos computacionais mais poderosos e [dedicados, como máquinas virtuais de ciência de dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Os cadernos na sua conta são mantidos privados, a menos que opte por partilhá-los.

Os cadernos Azure Sentinel usam muitas bibliotecas populares de Python, como pandas, matplotlib, bokeh, entre outros. Há muitos outros pacotes Python para você escolher, cobrindo áreas como:

- Visualizações e gráficos
- Processamento e análise de dados
- Estatísticas e computação numérica
- Aprendizagem automática e aprendizagem profunda

Também lançámos algumas ferramentas de segurança jupyter de código aberto num pacote chamado [msticpy.](https://github.com/Microsoft/msticpy/) Este pacote é usado em muitos dos cadernos incluídos. As ferramentas Msticpy são projetadas especificamente para ajudar a criar cadernos para a caça e investigação e estamos trabalhando ativamente em novas funcionalidades e melhorias.

Os cadernos iniciais incluem:

- **Investigação guiada - Alertas de Processo**: Permite-lhe triagem rápida de alertas analisando a atividade no hospedeiro ou hospedeiros afetados.
- **Caça guiada - Explorador anfitrião do Windows**: Permite-lhe explorar a atividade da conta, execuções de processos, atividade de rede e outros eventos em um anfitrião.
- **Caça guiada - Office365-Exploreing**: Caça à atividade suspeita do Office 365 em vários conjuntos de dados do Office 365.

O [repositório GitHub da Comunidade Azure Sentinel](https://github.com/Azure/Azure-Sentinel) é o local para quaisquer futuros cadernos Azure Sentinel construídos pela Microsoft ou que contribuíram da comunidade.

Para utilizar os cadernos, tem de ter uma conta Azure Notebooks. Para obter mais informações, consulte [Quickstart: Inicie sedutada e descreva um ID](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) do utilizador a partir da documentação dos Cadernos Azure. Para criar esta conta, pode utilizar a opção **Inscrição para Cadernos Azure** a partir da barra de comando em **Azure Sentinel - Cadernos**:

> [!div class="mx-imgBorder"]
>![Inscreva-se na opção Azure Notebooks](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Você pode executar um caderno diretamente de Azure Sentinel, ou clonar todos os cadernos Azure Sentinel para um novo projeto Azure Notebooks.

## <a name="run-a-notebook-from-azure-sentinel"></a>Executar um caderno de Azure Sentinel
 
1. A partir do portal Azure, navegue até **Azure Sentinel**  >  **Threat management**  >  **Notebooks,** onde você pode ver cadernos que Azure Sentinel fornece. 

2. Selecione cadernos individuais para ler as suas descrições, tipos de dados necessários e fontes de dados. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![caderno de lançamento](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Selecione o caderno que pretende utilizar e, em seguida, selecione **O Caderno de Lançamento (Pré-visualização)** para clonar e configurar o caderno num novo projeto Azure Notebooks que se conecta ao seu espaço de trabalho Azure Sentinel. Quando o processo estiver concluído, o caderno abre dentro de Cadernos Azure para que possa executar.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Cadernos Clone Azure Sentinel para um novo projeto de Cadernos Azure

Este procedimento cria um projeto Azure Notebooks para si, que contém os cadernos Azure Sentinel. Em seguida, pode executar os cadernos como está, ou fazer alterações neles e, em seguida, executá-los.

1. A partir do portal Azure, navegue até **Azure Sentinel**  >  **Threat management**  >  **Notebooks** e, em seguida, selecione **Cadernos Clones** da barra de comando:
  
    > [!div class="mx-imgBorder"]
    >![Opção De Cadernos Clones](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Quando o diálogo seguinte aparecer, **selecione Import** para clonar o gitHub repo no seu projeto Azure Notebooks. Se não tiver uma conta de Azure Notebooks existente, será solicitado a criar um e iniciar sessão.

   ![Caderno de importação](./media/notebooks/sentinel-notebooks-clone.png)

3. Na caixa de diálogo do **Repositório Do Upload GitHub,** não selecione **Clone de forma recorrente** porque esta opção se refere a repos GitHub ligados. Para o nome do projeto, utilize o nome predefinido ou escreva num novo. Em seguida, clique em **Importar** para começar a clonar o conteúdo do GitHub, que pode demorar alguns minutos a ser concluído.

   ![Caderno de importação](./media/notebooks/sentinel-create-project.png)

4. Abra o projeto que acabou de criar e, em seguida, abra a pasta **Cadernos** para ver os cadernos. Por exemplo:

   ![Repo de importação](./media/notebooks/sentinel-open-notebook1.png)

Em seguida, pode executar os cadernos dos Cadernos Azure. Para voltar a estes cadernos do Azure Sentinel, selecione **Vá aos seus Cadernos** a partir da barra de comando em **Azure Sentinel - Cadernos**:

> [!div class="mx-imgBorder"]
>![Vá para a opção De Cadernos](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Usando cadernos para caçar

Cada caderno leva-o através dos passos para realizar uma caça ou investigação. Bibliotecas e outras dependências necessárias pelo caderno podem ser instaladas a partir do próprio caderno ou através de um procedimento simples de configuração. A configuração que liga o seu projeto de portátil de volta à subscrição do Azure Sentinel é automaticamente a provisionada nos passos anteriores.

1. Se ainda não estiver em Cadernos Azure, pode utilizar a opção **Ir para os Seus Cadernos** a partir da barra de comando em **Azure Sentinel - Cadernos**:
    
    > [!div class="mx-imgBorder"]
    >![Vá para a opção De Cadernos](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Em Cadernos Azure, selecione **My Projects**, em seguida, o projeto que contém os cadernos Azure Sentinel e, finalmente, a pasta **Cadernos.**
    
2. Antes de abrir um caderno, esteja ciente de que, por padrão, o Free Compute é selecionado para executar os cadernos:
    
   ![selecionar caderno](./media/notebooks/sentinel-open-notebook2.png)
    
    Se configurar uma Máquina Virtual de Ciência de Dados (DSVM) para usar como explicado na introdução, selecione o DSVM e autente antes de abrir o primeiro portátil. 

3. Selecione um caderno para abri-lo.
    
    A primeira vez que abrir um caderno, poderá ser solicitado a selecionar uma versão kernel. Se não for solicitado, pode selecionar a **Kernel**versão kernel do  >   **kernel Change**e, em seguida, selecionar uma versão que seja pelo menos 3.6. A versão de kernel selecionada é apresentada no lado superior direito da janela do portátil:
    
   ![selecionar caderno](./media/notebooks/sentinel-select-kernel.png)

4. Antes de fazer quaisquer alterações ao caderno que descarregou, é uma boa ideia fazer uma cópia do caderno original e trabalhar na cópia. Para isso, selecione **'File**  >  **Make a Copy'.** Trabalhar em cópias permite-lhe atualizar com segurança futuras versões de cadernos sem sobrepor nenhum dos seus dados.
    
    Está agora pronto para executar ou editar o caderno selecionado.

Recomendações:

- Para uma rápida introdução aos dados de consulta em Azure Sentinel, veja o caderno [GetStarted](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/345cf9f7c8f6137f5af4593a3f9d7568acd6cbc2/DeprecatedNotebooks/Get%20Started.ipynb) na pasta dos **principais Cadernos.** 

- Encontrará cadernos de amostras adicionais na sub-dobragem **sample-notebooks.** Estes cadernos de amostras foram guardados com dados, de modo que é mais fácil ver a saída pretendida. Recomendamos a visualização destes cadernos em [nbviewer.](https://nbviewer.jupyter.org/) 

- A pasta **HowTos** contém cadernos que descrevem, por exemplo: Definir a versão Python padrão, configurar um DSVM, criar marcadores Azure Sentinel a partir de um caderno, e outros assuntos.

Os cadernos fornecidos destinam-se tanto a ferramentas úteis como a ilustrações e amostras de código que pode utilizar no desenvolvimento dos seus próprios cadernos.

Congratulamo-nos com o feedback, sejam sugestões, pedidos de funcionalidades, Cadernos contribuídos, relatórios de bugs ou melhorias e adições aos cadernos existentes. Vá ao [GitHub da Comunidade Azure Sentinel](https://github.com/Azure/Azure-Sentinel) para criar um problema ou garfo e fazer o upload de uma contribuição.

## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu a usar cadernos Jupyter em Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- [Caça proativamente por ameaças](hunting.md)
- [Use marcadores para guardar informações interessantes durante a caça](bookmarks.md)
