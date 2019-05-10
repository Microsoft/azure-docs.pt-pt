---
title: Capacidades de procura com blocos de notas na pré-visualização de sentinela de Azure | Documentos da Microsoft
description: Este artigo descreve como utilizar blocos de notas com as capacidades de buscar sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
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
ms.openlocfilehash: ffe3ae5b6aa26d154928a74e51864a0574b82c68
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228629"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Utilizar blocos de notas do Jupyter para procurar ameaças de segurança

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A base do Azure sentinela é o arquivo de dados; ele combina consultar, o esquema dinâmico e é dimensionado para volumes de grandes quantidades de dados de elevado desempenho. O portal do Azure sentinela e todas as ferramentas de sentinela de Azure utilizam uma API comum para aceder este arquivo de dados. A mesma API, como também está disponível para ferramentas externas [Jupyter](https://jupyter.org/) blocos de notas e Python. Enquanto muitas tarefas comuns podem ser executadas no portal, o Jupyter amplia o escopo do que pode fazer com estes dados. Ele combina programação completa com uma grande coleção de bibliotecas para análise de dados, visualização e aprendizagem de máquina. Esses atributos certifique Jupyter uma ferramenta muito apelativa para investigação de segurança e detecção.

![Bloco de notas de exemplo](./media/notebooks/sentinel-nb-mapandtimeline.png)

Integramos a experiência do Jupyter no portal do Azure sentinela, tornando mais fácil para criar e executar blocos de notas para analisar os dados. O *Kqlmagic* biblioteca fornece a cola que lhe permite realizar consultas de sentinela do Azure e executá-los diretamente dentro de um bloco de notas. Consulta de utilização a [linguagem de consulta de Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Vários blocos de notas, desenvolvidos por alguns dos analistas de segurança da Microsoft, são empacotados com sentinela do Azure. Algumas destes blocos de notas baseiam-se para um cenário específico e pode ser usadas como-é. Outros pretendem como exemplos para ilustrar técnicas e funcionalidades que pode copiar ou adaptar para uso em seus próprios blocos de notas. Também é possível importar outros blocos de notas da Comunidade do Azure sentinela GitHub.

Utiliza a experiência integrada do Jupyter [blocos de notas do Azure](https://notebooks.azure.com/) para armazenar, partilhar e executar blocos de notas. Também pode executar estes blocos de notas localmente (se tiver um ambiente de Python e o Jupyter no seu computador) ou em outros ambientes de JupterHub, como o Azure Databricks.

Blocos de notas tem dois componentes:

- a interface baseada no browser e em que vai introduzir e executar consultas e de código, e onde estão apresentados os resultados da execução.
- um *kernel* que é responsável pela análise e execução do código em si. 

Em blocos de notas do Azure, este kernel é executado no Azure *gratuitos de computação na Cloud e armazenamento* por predefinição. Se seus blocos de notas incluem modelos de aprendizagem automática complexas ou visualizações deve considerar a utilização mais poderosa e dedicada recursos de computação, tais como [máquinas de virtuais de ciência de dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Blocos de notas na sua conta sejam mantidos privados, a menos que pretenda partilhá-los.

Os blocos de notas do Azure sentinela usar muitas bibliotecas de Python populares, como o pandas, matplotlib, bokeh e outras pessoas. Há um grande número de outros pacotes do Python para a sua escolha, que cobre áreas como:

- visualizações e gráficos
- processamento de dados e análise
- as estatísticas e computação numérica
- Machine learning e de aprendizagem profunda

Disponibilizámos também algumas ferramentas de segurança do código-fonte aberto Jupyter num pacote designado [msticpy](https://github.com/Microsoft/msticpy/). Este pacote é usado em muitos dos blocos de notas incluídos. Ferramentas de Msticpy foram concebidas especificamente para ajudar com a criação de blocos de notas para buscar e investigação e estamos a trabalhar ativamente novos recursos e melhorias.

Os blocos de notas iniciais incluem:

- **Orientada investigação - processo alertas**: Permite-lhe rapidamente Triar alertas através da análise de atividade nos anfitriões afetados.
- **Orientada hunting - Explorador do host Windows**: Permite-lhe explorar a atividade de conta, as execuções de processo, atividade de rede e outros eventos num anfitrião.  
- **Orientada hunting - explorando o Office 365**: Busca de atividade suspeita do Office 365 em vários conjuntos de dados do Office 365.

O [repositório do GitHub do Azure sentinela Comunidade](https://github.com/Azure/Azure-Sentinel) é a localização para qualquer futuras blocos de notas do Azure sentinela criada pela Microsoft ou que tenham feito parte da Comunidade.

## <a name="run-a-notebook"></a>Executar um bloco de notas

No exemplo a seguir, vamos criar um projeto de blocos de notas do Azure no portal do Azure sentinela, preenchendo o projeto com blocos de notas. Antes de utilizar estes blocos de notas, é uma boa idéia fazer uma cópia do bloco de notas e a cópia de trabalho. Trabalhar em cópias permite-lhe com segurança atualizar para versões futuras do blocos de notas, sem substituir qualquer um dos seus dados.

1. No portal do Azure sentinela, clique em **blocos de notas** no menu de navegação. Para criar um novo projeto de blocos de notas do Azure, clique em **sentinela blocos de notas do Azure Clone** ou abrir seus blocos de notas existentes projetos clique **vá para os seus blocos de notas**.
  
   ![Selecione os blocos de notas](./media/notebooks/sentinel-az-notebooks-home.png)

2. Se escolheu **sentinela blocos de notas do Azure Clone** no passo anterior, será apresentada a caixa de diálogo seguinte. Clique em **importação** para clonar o repositório do GitHub para o seu projeto de blocos de notas do Azure. Se não tiver uma conta de blocos de notas do Azure existente, será solicitado a criar uma e iniciar sessão.

   ![Bloco de notas de importação](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. Ao criar um novo projeto, terá de nomear o projeto - utilizar o nome predefinido ou o tipo numa nova. Não verificar a **Clone recursivamente** opção - esta opção refere-se a repositórios de GitHub ligados. Clicar em **importação** inicia o conteúdo do GitHub, o que pode demorar alguns minutos para concluir a clonagem.

   ![Bloco de notas de importação](./media/notebooks/sentinel-create-nb-project.png)

4. Abra o **blocos de notas** pasta para ver os blocos de notas. Cada bloco de anotações descreve os passos para a realização de uma busca ou a investigação. Bibliotecas e outras dependências necessárias para o bloco de notas podem ser instaladas do bloco de notas em si ou por meio de um procedimento de configuração simples. Configuração de que o vincula seu projeto de bloco de notas para a sua subscrição do Azure sentinela é automaticamente aprovisionada nos passos anteriores. Blocos de notas do estão prontos para executar na sua área de trabalho do Log Analytics com sentinela do Azure.

   ![Repositório de importação](./media/notebooks/sentinel-open-notebook1.png)

5. Abra um bloco de notas. Computação de gratuita está selecionada por predefinição para executar os blocos de notas (realçados). Se tiver configurado uma DSVM do usar (veja acima), selecione a DSVM e autenticar antes de abrir o primeiro bloco de notas. Clique num bloco de notas para abri-lo.

   ![Selecione o bloco de notas](./media/notebooks/sentinel-open-notebook2.png)

6. Selecionando a versão de Python. Quando abre pela primeira vez um bloco de notas, ele poderá pedir-lhe para selecionar uma versão de kernel. Caso contrário, selecione o kernel para utilizar como a seguir. Python 3.6 ou posterior deve ser o kernel selecionado (no canto superior direito da janela do bloco de notas).

   ![Selecione o bloco de notas](./media/notebooks/sentinel-select-kernel.png)

Para obter uma introdução rápida para consultar dados no Azure sentinela, examinar os [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) bloco de notas na pasta de principal de blocos de notas. Blocos de notas de exemplo adicionais podem ser encontrados no **blocos de notas de exemplo** subpasta. Os blocos de notas de exemplo foram guardados com dados, para que seja mais fácil ver a saída desejada (Recomendamos a vê-los no [nbviewer](https://nbviewer.jupyter.org/)). O **HowTos** pasta contém blocos de notas que descreve, por exemplo: definição predefinida a versão do Python, configurar uma DSVM, Azure sentinela a criar os marcadores de um bloco de notas e outros assuntos.

Estes blocos de notas destinam-se-á como as duas ferramentas úteis e como as ilustrações e exemplos de código que podem ser usados no desenvolvimento de seus próprios blocos de notas.

Apreciamos os seus comentários, se sugestões, pedidos de funcionalidades, contribuiu com blocos de notas, relatórios de bugs ou melhorias e adições para blocos de notas existentes. Vá para o [Azure sentinela Comunidade GitHub](https://github.com/Azure/Azure-Sentinel) para criar um problema ou o fork e carregue uma contribuição.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como começar a utilizar blocos de notas do Jupyter no Azure sentinela. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:

- [Proativamente hunt relativamente a ameaças](hunting.md)
- [Utilizar marcadores para salvar informações interessantes ao sorte!](bookmarks.md)