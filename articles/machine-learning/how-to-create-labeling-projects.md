---
title: Criar um projeto de rotulagem de dados
titleSuffix: Azure Machine Learning
description: Aprenda a criar e executar projetos de rotulagem para marcar dados para machine learning.  Utilize a rotulagem assistida ML, ou humana na rotulagem do loop, para ajudar na tarefa.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: 5abb222a6211e2f3f92c002a49987ac54edc78e2
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346661"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Criar um projeto de rotulagem de dados e rótulos de exportação 



Rotular dados volumosos em projetos de aprendizagem automática é muitas vezes uma dor de cabeça. Projetos que têm uma componente de visão computacional, como classificação de imagem ou deteção de objetos, geralmente requerem etiquetas para milhares de imagens.
 
A rotulagem de dados [de aprendizagem automática Azure](https://ml.azure.com/) dá-lhe um lugar central para criar, gerir e monitorizar projetos de rotulagem. Use-o para coordenar dados, etiquetas e membros da equipa para gerir eficientemente as tarefas de rotulagem. Machine Learning suporta a classificação de imagem, multi-rótulo ou multi-classe, e identificação de objetos com caixas delimitadas.

A rotulagem dos dados acompanha o progresso e mantém a fila de tarefas de rotulagem incompletas.

É capaz de iniciar e parar o projeto e controlar o progresso da rotulagem. Pode rever os dados e exportações rotulados no formato COCO ou como um conjunto de dados de Aprendizagem automática Azure.

> [!Important]
> Atualmente, apenas são apoiados projetos de rotulagem de classificação de imagem e identificação de objetos. Além disso, as imagens de dados devem estar disponíveis numa loja de dados blob Azure. (Se não tiver uma loja de dados existente, poderá enviar imagens durante a criação do projeto.)

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um projeto
> * Especificar os dados e estrutura do projeto
> * Executar e monitorizar o projeto
> * Exportar as etiquetas


## <a name="prerequisites"></a>Pré-requisitos

* Os dados que pretende rotular, quer em ficheiros locais, quer no armazenamento de blob Azure.
* O conjunto de etiquetas que pretende aplicar.
* As instruções de rotulagem.
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um espaço de trabalho de aprendizagem automática. Ver [Criar um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Criar um projeto de rotulagem

Os projetos de rotulagem são administrados a partir da Azure Machine Learning. Utiliza a página **de projetos de rotulagem** para gerir os seus projetos.

Se os seus dados já estiverem no armazenamento da Azure Blob, deverá disponibilizá-lo como uma loja de dados antes de criar o projeto de rotulagem. Para um exemplo de utilização de uma loja de dados, consulte [Tutorial: Crie o seu primeiro projeto de rotulagem de classificação de imagem.](tutorial-labeling.md)

Para criar um projeto, **selecione Adicionar o projeto.** Dê ao projeto um nome adequado e **selecione o tipo de tarefa de rotulagem**.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Assistente de criação de projeto de rotulagem":::

* Escolha **classificação de imagem multiclasse** para projetos quando pretende aplicar apenas uma *etiqueta* de um conjunto de rótulos para uma imagem.
* Escolha **classificação de imagem Multi-label** para projetos quando pretende aplicar *uma ou mais* etiquetas de um conjunto de rótulos para uma imagem. Por exemplo, uma foto de um cão pode ser rotulada com *cão* e *durante o dia.*
* Escolha **a identificação de objetos (Caixa de Delimitação)** para projetos quando pretende atribuir uma etiqueta e uma caixa de delimitação a cada objeto dentro de uma imagem.

Selecione **a seguir** quando estiver pronto para continuar.

## <a name="specify-the-data-to-label"></a>Especificar os dados para rotular

Se já criou um conjunto de dados que contém os seus dados, selecione-os a partir da lista de drop-down existente do conjunto de **dados.** Ou, **selecione Criar um conjunto de dados** para utilizar uma loja de dados Azure existente ou para carregar ficheiros locais.

> [!NOTE]
> Um projeto não pode conter mais de 500.000 imagens.  Se o seu conjunto de dados tiver mais, apenas as primeiras 500.000 imagens serão carregadas.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Criar um conjunto de dados a partir de uma loja de dados Azure

Em muitos casos, não faz mal carregar ficheiros locais. Mas [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) fornece uma forma mais rápida e robusta de transferir uma grande quantidade de dados. Recomendamos o Storage Explorer como a forma padrão de mover ficheiros.

Para criar um conjunto de dados a partir de dados que já armazenou no armazenamento da Azure Blob:

1. Selecione **Criar um conjunto de**  >  **dados a partir da datastore**.
1. Atribua um **Nome** ao seu conjunto de dados.
1. Escolha **o Ficheiro** como o tipo de conjunto de **dados.**  Apenas os tipos de conjunto de dados de ficheiros são suportados.
1. Selecione a datastore.
1. Se os seus dados estiverem numa sub-dobragem dentro do armazenamento do seu blob, escolha **procurar** para selecionar o caminho.
    * Apecimento "/**" ao caminho para incluir todos os ficheiros nas sub-dobradeiras do caminho selecionado.
    * Apêndice "* */* .*" para incluir todos os dados no recipiente atual e nas suas sub-dobradinhas.
1. Forneça uma descrição para o seu conjunto de dados.
1. Selecione **Seguinte**.
1. Confirme os detalhes. Selecione **Back** para modificar as definições ou **criar** para criar o conjunto de dados.


### <a name="create-a-dataset-from-uploaded-data"></a>Criar um conjunto de dados a partir de dados carregados

Para fazer o upload direto dos seus dados:

1. Selecione Criar um conjunto de **dados**  >  **a partir de ficheiros locais**.
1. Atribua um **Nome** ao seu conjunto de dados.
1. Escolha "Ficheiro" como **tipo conjunto de dados.**
1. *Opcional:* Selecione **definições avançadas** para personalizar a datastore, o recipiente e o caminho para os seus dados.
1. **Selecione Navegar** para selecionar os ficheiros locais para carregar.
1. Forneça uma descrição do seu conjunto de dados.
1. Selecione **Seguinte**.
1. Confirme os detalhes. Selecione **Back** para modificar as definições ou **criar** para criar o conjunto de dados.

Os dados são enviados para a loja blob padrão ("workspaceblobstore") do seu espaço de trabalho Machine Learning.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"></a> Configurar atualização incremental

Se pretende adicionar novas imagens ao seu conjunto de dados, utilize a atualização incremental para adicionar estas novas imagens ao seu projeto.   Quando **a atualização incremental** é ativada, o conjunto de dados é verificado periodicamente para que novas imagens sejam adicionadas a um projeto, com base na taxa de conclusão da rotulagem.   A verificação de novos dados para quando o projeto contém o máximo de 500.000 imagens.

Para adicionar mais imagens ao seu projeto, utilize o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para fazer o upload para a pasta apropriada no armazenamento de bolhas. 

Verifique a caixa para **Ativar a atualização incremental** quando pretender que o seu projeto monitorize continuamente novos dados na datastore. Estes dados serão retirados do seu projeto uma vez por dia quando estiver ativado.

Desmarque esta caixa se não quiser que novas imagens que apareçam na datastore sejam adicionadas ao seu projeto.

Pode encontrar o tempo de duração para a última atualização no **separador De atualização Incremental** **para** o seu projeto.


## <a name="specify-label-classes"></a>Especificar classes de etiquetas

Na página de **classes Label,** especifique o conjunto de classes para categorizar os seus dados. Faça isto com cuidado, porque a precisão e velocidade dos seus rotuladores serão afetadas pela sua capacidade de escolher entre as classes. Por exemplo, em vez de soletrar o género e as espécies completas para plantas ou animais, utilize um código de campo ou abreveve o género.

Introduza uma etiqueta por linha. Utilize o **+** botão para adicionar uma nova linha. Se tiver mais de 3 ou 4 etiquetas mas menos de 10, pode querer pré-afixar os nomes com números ("1: " "2: ") para que os rotuladores possam utilizar as teclas numeradas para acelerar o seu trabalho.

## <a name="describe-the-labeling-task"></a>Descreva a tarefa de rotulagem

É importante explicar claramente a tarefa de rotulagem. Na página de **instruções de rotulagem,** pode adicionar um link a um site externo para instruções de rotulagem ou fornecer instruções na caixa de edição da página. Mantenha as instruções orientadas para a tarefa e apropriadas ao público. Considere estas questões:

* Quais são os rótulos que verão e como vão escolher entre eles? Existe um texto de referência a que se refere?
* O que devem fazer se nenhuma etiqueta parecer adequada?
* O que devem fazer se várias etiquetas parecerem adequadas?
* Que limiar de confiança devem aplicar-se a um rótulo? Queres o "melhor palpite" deles, se não tiverem a certeza?
* O que devem fazer com objetos de interesse parcialmente obstruídos ou sobrepostos?
* O que devem fazer se um objeto de interesse for cortado pela borda da imagem?
* O que devem fazer depois de apresentarem um rótulo se acham que cometeram um erro?

Para as caixas de delimitação, questões importantes incluem:

* Como é definida a caixa de delimitação para esta tarefa? Deve ser inteiramente no interior do objeto, ou deve estar no exterior? Deve ser cortada o mais perto possível, ou algum apuramento é aceitável?
* Que nível de cuidado e consistência espera que os rotuladores apliquem na definição de caixas de delimitação?
* Como rotular o objeto que é parcialmente mostrado na imagem? 
* Como rotular o objeto que parcialmente coberto por outro objeto?

>[!NOTE]
> Tenha em atenção que os rótulos poderão selecionar as primeiras 9 etiquetas utilizando as teclas 1-9.

## <a name="use-ml-assisted-labeling"></a>Utilizar rotulagem assistida ML

A página **de rotulagem assistida ML** permite-lhe acionar modelos automáticos de aprendizagem automática para acelerar a tarefa de rotulagem. No início do seu projeto de rotulagem, as imagens são baralhadas numa ordem aleatória para reduzir o preconceito potencial. No entanto, quaisquer enviesamentos que estejam presentes no conjunto de dados serão refletidos no modelo treinado. Por exemplo, se 80% das suas imagens forem de uma única classe, então aproximadamente 80% dos dados utilizados para treinar o modelo serão dessa classe. Esta formação não inclui a aprendizagem ativa.

Selecione *a rotulagem assistida ML* e especifique uma GPU para permitir a rotulagem assistida, que consiste em duas fases:
* Clustering
* Pré-etiqueta

O número exato de imagens etiquetadas necessárias para iniciar a rotulagem assistida não é um número fixo.  Isto pode variar significativamente de um projeto de rotulagem para outro. Para alguns projetos, por vezes é possível ver tarefas de pré-selo ou cluster depois de 300 imagens terem sido etiquetadas manualmente. A ML Assisted Labeling utiliza uma técnica chamada *Transfer Learning,* que usa um modelo pré-treinado para iniciar o processo de treino. Se as aulas do seu conjunto de dados forem semelhantes às do modelo pré-treinado, as pré-etiquetas podem estar disponíveis após apenas algumas centenas de imagens etiquetadas manualmente. Se o seu conjunto de dados for significativamente diferente dos dados utilizados para pré-treinar o modelo, pode demorar muito mais tempo.

Uma vez que as etiquetas finais ainda dependem da entrada do rótulo, esta tecnologia é por vezes chamada humana na rotulagem *do loop.*

> [!NOTE]
> A rotulagem de dados assistidos ML não suporta contas de armazenamento padrão protegidas por trás de uma [rede virtual](how-to-network-security-overview.md). Deve utilizar uma conta de armazenamento não padrão para a rotulagem de dados assistidos ML. A conta de armazenamento não padrão pode ser protegida por trás da rede virtual. 

### <a name="clustering"></a>Clustering

Após a submissão de um certo número de rótulos, o modelo de machine learning para classificação de imagem começa a agrupar imagens semelhantes.  Estas imagens semelhantes são apresentadas aos rotuladores no mesmo ecrã para acelerar a marcação manual. O agrupamento é especialmente útil quando o rótulo está a visualizar uma grelha de 4, 6 ou 9 imagens. 

Uma vez treinado um modelo de machine learning nos seus dados etiquetados manualmente, o modelo é truncado até à sua última camada totalmente ligada. As imagens não identificadas são então passadas através do modelo truncado num processo vulgarmente conhecido como "incorporação" ou "apetência". Isto incorpora cada imagem num espaço de alta dimensão definido por esta camada de modelo. As imagens que são vizinhos mais próximos do espaço são usadas para tarefas de agrupamento. 

A fase de agrupamento não aparece para modelos de deteção de objetos.

### <a name="prelabeling"></a>Pré-etiqueta

Depois de serem submetidas etiquetas de imagem suficientes, um modelo de classificação é usado para prever etiquetas de imagem. Ou um modelo de deteção de objetos é usado para prever caixas de delimitação. O rotulador vê agora páginas que contêm etiquetas previstas já presentes em cada imagem. Para a deteção de objetos, as caixas previstas também são mostradas. A tarefa é então rever estas previsões e corrigir quaisquer imagens mal rotuladas antes de submeter a página.  

Uma vez treinado um modelo de machine learning nos seus dados etiquetados manualmente, o modelo é avaliado num conjunto de testes de imagens etiquetadas manualmente para determinar a sua precisão numa variedade de diferentes limiares de confiança. Este processo de avaliação é utilizado para determinar um limiar de confiança acima do qual o modelo é suficientemente preciso para apresentar pré-etiquetas. O modelo é então avaliado com dados não rotulados. Imagens com previsões mais confiantes do que este limiar são usadas para pré-rotulagem.

## <a name="initialize-the-labeling-project"></a>Inicializar o projeto de rotulagem

Após a inicial do projeto de rotulagem, alguns aspetos do projeto são imutáveis. Não é possível alterar o tipo de tarefa ou o conjunto de dados. *Pode* modificar as etiquetas e o URL para a descrição da tarefa. Reveja cuidadosamente as definições antes de criar o projeto. Depois de submeter o projeto, é devolvido à página inicial da **Rotulagem de Dados,** que mostrará o projeto como **Initializing**.

> [!NOTE]
> Esta página pode não ser automaticamente atualizada. Assim, após uma pausa, refresque manualmente a página para ver o estado do projeto como **Criado**.

## <a name="run-and-monitor-the-project"></a>Executar e monitorizar o projeto
Depois de rubricar o projeto, o Azure começará a executá-lo. Selecione o projeto na página principal **de Rotulagem de Dados** para ver detalhes do projeto

Para interromper ou reiniciar o projeto, altere o estado **de Funcionamento** no topo direito. Só é possível rotular dados quando o projeto está em execução.

### <a name="dashboard"></a>Dashboard

O **separador Dashboard** mostra o progresso da tarefa de rotulagem.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Painel de rotulagem de dados":::

O gráfico de progresso mostra quantos itens foram rotulados e quantos ainda não foram feitos.  Os artigos pendentes podem ser:

* Ainda não adicionado a uma tarefa
* Incluído numa tarefa que é atribuída a um rotulador mas ainda não concluída 
* Na fila de tarefas ainda por atribuir

A secção central mostra a fila de tarefas ainda por atribuir. Quando a rotulagem assistida ML está desligada, esta secção mostra o número de tarefas manuais a atribuir. Quando a rotulagem assistida ml estiver em cima, isto também mostrará:

* Tarefas que contenham itens agrupados na fila
* Tarefas que contenham itens pré-rotulados na fila

Além disso, quando a rotulagem assistida da ML está ativada, uma pequena barra de progresso mostra quando ocorrerá o próximo treino.  As secções experimentos dão links para cada uma das máquinas de aprendizagem.

* Formação - treina um modelo para prever os rótulos
* Validação - determina se a previsão deste modelo será utilizada para pré-rotulagem dos itens 
* Inferência - previsão para novos itens
* Caracterização - clusters itens (apenas para projetos de classificação de imagem)

Do lado direito está uma distribuição dos rótulos para as tarefas que estão completas.  Lembre-se que em alguns tipos de projetos, um item pode ter várias etiquetas, caso em que o número total de etiquetas pode ser maior do que o número total de itens.

### <a name="data-tab"></a>Separador de dados

No separador **Dados,** pode ver o conjunto de dados e rever os dados rotulados. Se vir dados rotulados incorretamente, selecione-os e escolha **Rejeitar**, que removerá as etiquetas e colocará os dados de volta na fila não identificada.

### <a name="details-tab"></a>Separador de detalhes

Veja os detalhes do seu projeto.  Neste separador pode:

* Ver detalhes do projeto e conjuntos de dados de entrada
* Permitir atualização incremental
* Veja os detalhes do recipiente de armazenamento utilizado para armazenar saídas etiquetadas no seu projeto
* Adicione etiquetas ao seu projeto
* Editar instruções que dê às suas etiquetas
* Editar detalhes da rotulagem assistida ML, incluindo ativar/desativar

### <a name="access-for-labelers"></a>Acesso aos rotuladores

Qualquer pessoa que tenha acesso ao seu espaço de trabalho pode rotular dados no seu projeto.  Também pode personalizar as permissões para os seus rotuladores para que possam aceder à rotulagem, mas não a outras partes do espaço de trabalho ou ao seu projeto de rotulagem.  Para obter mais detalhes, consulte [Gerir o acesso a um espaço de trabalho Azure Machine Learning](how-to-assign-roles.md)e aprender a criar o papel personalizado do [labeler.](how-to-assign-roles.md#labeler)

## <a name="add-new-label-class-to-a-project"></a>Adicione nova classe de rótulo a um projeto

Durante o processo de rotulagem, poderá descobrir que são necessárias etiquetas adicionais para classificar as suas imagens.  Por exemplo, pode querer adicionar uma etiqueta "Desconhecido" ou "Outro" para indicar imagens confusas.

Utilize estes passos para adicionar um ou mais rótulos a um projeto:

1. Selecione o projeto na página principal **de Rotulagem de Dados.**
1. No topo direito da página, **alternar Running** to **Paused** para impedir que os rotuladores da sua atividade.
1. Selecione o separador **Detalhes**.
1. Na lista à esquerda, selecione **Label classes**.
1. No topo da lista, selecione **+ Adicionar etiquetas** ![ Adicionar uma etiqueta](media/how-to-create-labeling-projects/add-label.png)
1. No formulário, adicione a sua nova etiqueta e escolha como proceder.  Uma vez que alterou as etiquetas disponíveis para uma imagem, escolhe como tratar os dados já rotulados:
    * Comece de novo, removendo todas as etiquetas existentes.  Escolha esta opção se quiser começar a rotular desde o início com o novo conjunto completo de etiquetas. 
    * Comece de novo, mantendo todas as etiquetas existentes.  Escolha esta opção para marcar todos os dados como não rotulados, mas mantenha as etiquetas existentes como uma etiqueta padrão para imagens que foram previamente rotuladas.
    * Continue, mantendo todas as etiquetas existentes. Escolha esta opção para manter todos os dados já rotulados como está, e comece a usar a nova etiqueta para dados ainda não rotulados.
1. Modifique a página de instruções conforme necessário para a nova etiqueta.
1. Depois de ter adicionado todas as novas etiquetas, no topo direito da página **alternar Pausa para** **Correr** para reiniciar o projeto.  

## <a name="export-the-labels"></a>Exportar as etiquetas

Pode exportar os dados do rótulo para a experimentação de Machine Learning a qualquer momento. As etiquetas de imagem podem ser exportadas em [formato COCO](http://cocodataset.org/#format-data) ou como um conjunto [de dados de aprendizagem automática Azure com etiquetas.](how-to-use-labeled-dataset.md) Utilize o botão **Exportação** na página de detalhes do **Projeto** do seu projeto de rotulagem.

O ficheiro COCO é criado na loja blob padrão do espaço de trabalho Azure Machine Learning numa pasta dentro *da exportação/coco*. Pode aceder ao conjunto de dados de aprendizagem automática Azure exportado na secção **Datasets** da Machine Learning. A página de detalhes do conjunto de dados também fornece código de amostra para aceder às suas etiquetas a partir de Python.

![Conjunto de dados exportado](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Crie o seu primeiro projeto de rotulagem de classificação de imagem.](tutorial-labeling.md)
* Rotular imagens para [classificação de imagem ou deteção de objetos](how-to-label-images.md)
* Saiba mais sobre [o Azure Machine Learning e o Machine Learning Studio (clássico)](./overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
