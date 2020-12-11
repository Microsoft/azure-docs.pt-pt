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
ms.date: 09/06/2020
ms.openlocfilehash: 43d7a697b3cb013a73a0b14db8ec1758244ae3b9
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092198"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Use o Caderno Jupyter para caçar ameaças à segurança

A fundação do Azure Sentinel é a loja de dados; combina consulta de alto desempenho, esquema dinâmico e escalas a volumes de dados maciços. O portal Azure e todas as ferramentas Azure Sentinel utilizam uma API comum para aceder a esta loja de dados. A mesma API também está disponível para ferramentas externas, como cadernos [Jupyter](https://jupyter.org/) e Python. Embora muitas tarefas comuns possam ser realizadas no portal, Jupyter alarga o âmbito do que você pode fazer com estes dados. Combina a programabilidade total com uma enorme coleção de bibliotecas para machine learning, visualização e análise de dados. Estes atributos fazem de Jupyter uma ferramenta convincente para a investigação de segurança e caça.

![caderno exemplo](./media/notebooks/sentinel-notebooks-map.png)

Integrámos a experiência Jupyter no portal Azure, facilitando-lhe a criação e execução de cadernos para analisar os seus dados. A biblioteca *Kqlmagic* fornece a cola que permite tirar consultas de Azure Sentinel e executá-las diretamente dentro de um caderno. As consultas utilizam a [língua de consulta kusto.](https://kusto.azurewebsites.net/docs/kusto/query/index.html) Vários cadernos, desenvolvidos por alguns dos analistas de segurança da Microsoft, são embalados com Azure Sentinel. Alguns destes cadernos são construídos para um cenário específico e podem ser usados como-é. Outros destinam-se a amostras para ilustrar técnicas e funcionalidades que pode copiar ou adaptar para uso nos seus próprios cadernos. Outros cadernos podem também ser importados do GitHub da Comunidade Azure Sentinel.

A experiência jupyter integrada usa [cadernos Azure](https://notebooks.azure.com/) para armazenar, partilhar e executar cadernos. Você também pode executar estes cadernos localmente se você tem um ambiente Python e Jupyter no seu computador, ou em outros ambientes JupterHub, como Azure Databricks.

Os cadernos têm dois componentes:

- A interface baseada no navegador onde você entra e executa consultas e código, e onde os resultados da execução são exibidos.
- Um *núcleo* responsável por analisar e executar o código em si.

O núcleo do caderno Azure Sentinel funciona numa máquina virtual Azure (VM). Existem várias opções de licenciamento para alavancar máquinas virtuais mais poderosas se os seus cadernos incluirem modelos complexos de aprendizagem automática.

Os cadernos Azure Sentinel usam muitas bibliotecas populares de Python, como pandas, matplotlib, bokeh, entre outros. Há muitos outros pacotes Python para você escolher, cobrindo áreas como:

- Visualizações e gráficos
- Processamento e análise de dados
- Estatísticas e computação numérica
- Machine learning e aprendizagem profunda

Também lançámos algumas ferramentas de segurança jupyter de código aberto num pacote chamado [msticpy.](https://github.com/Microsoft/msticpy/) Este pacote é usado em muitos dos cadernos incluídos. As ferramentas Msticpy são projetadas especificamente para ajudar a criar cadernos para a caça e investigação e estamos trabalhando ativamente em novas funcionalidades e melhorias.

O [repositório GitHub da Comunidade Azure Sentinel](https://github.com/Azure/Azure-Sentinel) é o local para quaisquer futuros cadernos Azure Sentinel construídos pela Microsoft ou que contribuíram da comunidade.

Para utilizar os cadernos, tem primeiro de criar um espaço de trabalho para aprendizagem automática Azure (ML).

## <a name="create-an-azure-ml-workspace"></a>Criar um espaço de trabalho Azure ML

1. A partir do portal Azure, navegue até **Azure Sentinel**  >  **Threat management**  >  **Notebooks** e, em seguida, selecione O Caderno de **Lançamento**.

    > [!div class="mx-imgBorder"]
    > ![caderno de lançamento para iniciar espaço de trabalho azul ml](./media/notebooks/sentinel-notebooks-launch.png)

1. No **espaço de trabalho AzureML,** selecione Create **New**.

    > [!div class="mx-imgBorder"]
    > ![criar espaço de trabalho](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. Na página **Machine Learning,** forneça as seguintes informações e, em seguida, selecione **Review + create**.

    |Campo|Descrição|
    |--|--|
    |Subscrição|Selecione a subscrição do Azure que pretende utilizar.|
    |Grupo de recursos|Utilize um grupo de recursos já existente na sua subscrição ou introduza um nome para criar um grupo de recursos novo. Um grupo de recursos detém recursos relacionados para uma solução Azure. Neste exemplo, utilizamos **a AzureMLRG.**|
    |Nome da área de trabalho|Insira um nome único que identifique o seu espaço de trabalho. Neste exemplo, utilizamos **o espaço de testes1.** Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de recordar e diferenciar dos espaços de trabalho criados por outros.|
    |Região|Selecione a localização mais próxima dos seus utilizadores e os recursos de dados para criar o seu espaço de trabalho.|
    |Edição do espaço de trabalho|Selecione **Basic** como o tipo de espaço de trabalho neste exemplo. O tipo de espaço de trabalho (Basic & Enterprise) determina as funcionalidades a que terá acesso e preços.|

    > [!div class="mx-imgBorder"]
    > ![fornecer detalhes do espaço de trabalho](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. Reveja as informações, verifique se está correta e, em seguida, **selecione Criar** para iniciar a implantação do seu espaço de trabalho.

    > [!div class="mx-imgBorder"]
    > ![rever detalhes do espaço de trabalho](./media/notebooks/sentinel-notebooks-azureml-review.png)

    Pode levar vários minutos para criar o seu espaço de trabalho na nuvem durante o qual a página **'Vista Geral'** apresenta o estado de implantação atual.

    > [!div class="mx-imgBorder"]
    > ![implantação do espaço de trabalho](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

Uma vez concluída a sua implementação, pode lançar cadernos no seu novo espaço de trabalho Azure ML.

> [!div class="mx-imgBorder"]
> ![implementação do espaço de trabalho conseguiu](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>Lance um caderno usando o seu espaço de trabalho Azure ML

1. A partir do portal Azure, navegue até **Azure Sentinel**  >  **Threat management**  >  **Notebooks,** onde você pode ver cadernos que Azure Sentinel fornece.

    > [!TIP]
    > Selecione **Guides & Feedback** para abrir um painel com ajuda adicional e orientação em cadernos.
    > ![ver guias de cadernos](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. Selecione cadernos individuais para visualizar as suas descrições, tipos de dados necessários e fontes de dados.

    > [!div class="mx-imgBorder"]
    > ![ver detalhes do caderno](./media/notebooks/sentinel-azure-notebooks-view.png)

1. Selecione o caderno que pretende utilizar e, em seguida, selecione **Launch Notebook** para clonar e configurar o caderno num novo projeto Azure Notebooks que se conecta ao seu espaço de trabalho Azure Sentinel. Quando o processo estiver concluído, o caderno abre dentro de Cadernos Azure para que possa executar.

    > [!div class="mx-imgBorder"]
    > ![selecionar caderno](./media/notebooks/sentinel-azure-notebooks-select.png)

1. No espaço de trabalho AzureML, selecione o seu espaço de trabalho Azure ML e, em seguida, selecione **Lançamento**.

    > [!div class="mx-imgBorder"]
    > ![caderno de lançamento](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. Selecione uma instância de cálculo. Se não tiver uma instância computacional, faça o seguinte:
    1. Selecione o sinal de mais (+) para iniciar o novo assistente **de instância de computação.**

        > [!div class="mx-imgBorder"]
        > ![começar a calcular assistente de instância](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. Na página de instância do **novo cálculo,** forneça as informações necessárias e, em seguida, selecione **Criar**.

        > [!div class="mx-imgBorder"]
        > ![criar instância computacional](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. Uma vez criado o servidor de portátil, dentro de cada célula seleciona o ícone de execução para executar código nos cadernos.

    > [!div class="mx-imgBorder"]
    > ![executar caderno](./media/notebooks/sentinel-azure-notebooks-run.png)

Recomendações:

- Para uma rápida introdução aos dados de consulta em Azure Sentinel, veja o [Getting Started com Azure ML Notebooks e o guia Azure Sentinel.](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb)

- Você encontrará cadernos de amostras adicionais na sub-dobragem [**GitHub de amostras.**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) Estes cadernos de amostras foram guardados com dados, de modo que é mais fácil ver a saída pretendida. Recomendamos a visualização destes cadernos em [nbviewer.](https://nbviewer.jupyter.org/)

- A sub-dobragem [**HowTos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) GitHub contém cadernos que descrevem, por exemplo: Definir a versão Python padrão, configurar um DSVM, criar marcadores Azure Sentinel a partir de um caderno, e outros assuntos.

Os cadernos fornecidos destinam-se tanto a ferramentas úteis como a ilustrações e amostras de código que pode utilizar no desenvolvimento dos seus próprios cadernos.

Congratulamo-nos com o feedback, sejam sugestões, pedidos de funcionalidades, Cadernos contribuídos, relatórios de bugs ou melhorias e adições aos cadernos existentes. Vá ao [GitHub da Comunidade Azure Sentinel](https://github.com/Azure/Azure-Sentinel) para criar um problema ou garfo e fazer o upload de uma contribuição.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar o Jupyter Notebook em Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- [Caça proativamente por ameaças](hunting.md)
- [Use marcadores para guardar informações interessantes durante a caça](bookmarks.md)
