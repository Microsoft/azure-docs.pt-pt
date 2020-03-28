---
title: Criar um projeto de rotulagem de dados
titleSuffix: Azure Machine Learning
description: Aprenda a criar e executar projetos de rotulagem para marcar dados para machine learning.  As ferramentas incluem rotulagem assistida ml ou humano na rotulagem do laço para ajudar na tarefa.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/01/2020
ms.openlocfilehash: d39cf8745c6f53cb11bb12561fd452325fe52ac6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79296953"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Criar um projeto de rotulagem de dados e rótulos de exportação 

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Rotular dados volumosos em projetos de aprendizagem automática é muitas vezes uma dor de cabeça. Os projetos que possuem um componente de visão computacional, como a classificação de imagem ou a deteção de objetos, geralmente requerem etiquetas para milhares de imagens.
 
[O Azure Machine Learning](https://ml.azure.com/) dá-lhe um lugar central para criar, gerir e monitorizar projetos de rotulagem. Use-o para coordenar dados, etiquetas e membros da equipa para gerir eficientemente as tarefas de rotulagem. O Machine Learning suporta a classificação de imagem, multi-etiqueta ou multi-classe, e identificação de objetos com caixas limitadas.

O Machine Learning acompanha o progresso e mantém a fila de tarefas de rotulagem incompletas. Os rotuladores não precisam de uma conta Azure para participar. Depois de autenticados com a sua conta Microsoft ou Com o [Diretório Ativo Azure,](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)podem fazer o máximo de rotulagem que o seu tempo permitir.

Você começa e para o projeto, adiciona e remove os rótulos e as equipas, e monitoriza o progresso da rotulagem. Pode exportar dados rotulados em formato COCO ou como um conjunto de dados de Aprendizagem automática Azure.

> [!Important]
> Apenas os projetos de classificação de imagem e identificação de objetos são atualmente suportados. Além disso, as imagens de dados devem estar disponíveis numa loja de dados azure blob. (Se não tiver uma datastore existente, poderá fazer upload de imagens durante a criação do projeto.) 

Neste artigo, aprenderá a:

> [!div class="checklist"]
> * Criar um projeto
> * Especificar os dados e estrutura do projeto
> * Gerir as equipas e as pessoas que trabalham no projeto
> * Executar e monitorizar o projeto
> * Exportar os rótulos


## <a name="prerequisites"></a>Pré-requisitos

* Os dados que pretende rotular, quer em ficheiros locais, quer no armazenamento de blob Azure.
* O conjunto de etiquetas que pretende aplicar.
* As instruções para a rotulagem.
* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um espaço de trabalho de Aprendizagem automática. Consulte Criar um espaço de [trabalho de aprendizagem automática Azure](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Criar um projeto de rotulagem

Os projetos de rotulagem são geridos a partir de Azure Machine Learning. Usa a página de projetos de **rotulagem** para gerir os seus projetos e pessoas. Um projeto tem uma ou mais equipas atribuídas a ele, e uma equipa tem uma ou mais pessoas atribuídas a ele.

Se os seus dados já se encontra no armazenamento da Azure Blob, deverá disponibilizá-lo como uma datastore antes de criar o projeto de rotulagem. Para mais detalhes, consulte Criar e registar lojas de [dados.](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)

Para criar um projeto, selecione **Adicionar projeto**. Dê ao projeto um nome adequado e selecione o tipo de **tarefa de rotulagem**.

![Assistente de criação de projeto de rotulagem](./media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Escolha **classificação de imagem Multi-classe** para projetos quando quiser aplicar apenas uma *única classe* de um conjunto de classes a uma imagem.
* Escolha a **classificação de imagem Multi-etiqueta** para projetos quando pretender aplicar *uma ou mais* etiquetas de um conjunto de classes a uma imagem. Por exemplo, uma foto de um cão pode ser rotulada com *cão* e *durante o dia.*
* Escolha **identificação de objetos (Caixa de delimitação)** para projetos quando pretender atribuir uma classe e uma caixa de delimitação a cada objeto dentro de uma imagem.

Selecione **Next** quando estiver pronto para continuar.

## <a name="specify-the-data-to-label"></a>Especificar os dados a rotular

Se já criou um conjunto de dados que contenha os seus dados, selecione-os a partir da lista de lançamento **seletiva** de dados existente. Ou, selecione **Criar um conjunto** de dados para utilizar uma loja de dados Azure existente ou para carregar ficheiros locais.


### <a name="create-a-dataset-from-an-azure-datastore"></a>Criar um conjunto de dados a partir de uma loja de dados Azure

Em muitos casos, não faz mal enviar ficheiros locais. Mas o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) fornece uma forma mais rápida e robusta de transferir uma grande quantidade de dados. Recomendamos o Storage Explorer como a forma padrão de mover ficheiros.

Para criar um conjunto de dados a partir de dados que já armazenou no armazenamento da Blob Azure:

1. Selecione **Criar um conjunto de** > dados**A partir da datastore**.
1. Atribuir um **Nome** ao seu conjunto de dados.
1. Escolha o **Ficheiro** como o **tipo dataset**.  
1. Selecione a loja de dados.
1. Se os seus dados estiverem numa subpasta dentro do seu armazenamento de blob, escolha **Navegar** para selecionar o caminho.
    * Apêndice "/**" ao caminho para incluir todos os ficheiros em subpastas do caminho selecionado.
    * Apêndice "**/*.*" para incluir todos os dados do recipiente atual e das suas subpastas.
1. Forneça uma descrição para o seu conjunto de dados.
1. Selecione **Next**.
1. Confirme os detalhes. Selecione **Voltar** a modificar as definições ou **criar** para criar o conjunto de dados.

> [!NOTE]
> Os dados que escolher está carregado no seu projeto.  A adição de mais dados à datastore não aparecerá neste projeto uma vez que o projeto seja criado.  

### <a name="create-a-dataset-from-uploaded-data"></a>Criar um conjunto de dados a partir de dados carregados

Para fazer o upload direto dos seus dados:

1. Selecione **Criar um conjunto** > de dados**a partir de ficheiros locais**.
1. Atribuir um **Nome** ao seu conjunto de dados.
1. Escolha "File" como **o tipo dataset**.
1. *Opcional:* Selecione **configurações avançadas** para personalizar a loja de dados, o recipiente e o caminho para os seus dados.
1. **Selecione Navegar** para selecionar os ficheiros locais para carregar.
1. Forneça uma descrição do seu conjunto de dados.
1. Selecione **Next**.
1. Confirme os detalhes. Selecione **Voltar** a modificar as definições ou **criar** para criar o conjunto de dados.

Os dados são enviados para a loja de blob padrão ("workspaceblobstore") do seu espaço de trabalho machine learning.

## <a name="specify-label-classes"></a>Especificar aulas de etiquetas

Na página de aulas do **Label,** especifique o conjunto de aulas para categorizar os seus dados. Faça-o com cuidado, porque a precisão e a velocidade dos seus rotuladores serão afetadas pela sua capacidade de escolher entre as classes. Por exemplo, em vez de soletrar todo o género e espécies para plantas ou animais, use um código de campo ou abreobre o género.

Introduza uma etiqueta por linha. Utilize **+** o botão para adicionar uma nova linha. Se tiver mais de 3 ou 4 etiquetas mas menos de 10, pode querer prefixar os nomes com números ("1: ", "2: ") para que os rotuladores possam usar as teclas de número para acelerar o seu trabalho.

## <a name="describe-the-labeling-task"></a>Descreva a tarefa de rotulagem

É importante explicar claramente a tarefa de rotulagem. Na página de instruções de **rotulagem,** pode adicionar um link a um site externo para instruções de rotulagem ou fornecer instruções na caixa de edição da página. Mantenha as instruções orientadas para a tarefa e adequadas ao público. Considere estas questões:

* Quais são os rótulos que vão ver, e como vão escolher entre eles? Há algum texto de referência a que se referir?
* O que devem fazer se nenhum rótulo parecer apropriado?
* O que devem fazer se várias etiquetas parecerem apropriadas?
* Que limiar de confiança devem aplicar-se a um rótulo? Queres o "melhor palpite" se não tiverem a certeza?
* O que devem fazer com objetos de interesse parcialmente obstruídos ou sobrepostos?
* O que devem fazer se um objeto de interesse é cortado pela borda da imagem?
* O que devem fazer depois de apresentarem uma etiqueta se acharem que cometeram um erro?

Para as caixas de delimitação, questões importantes incluem:

* Como é definida a caixa de delimitação para esta tarefa? Deve estar inteiramente no interior do objeto, ou deve estar no exterior? Deve ser cortado o mais próximo possível, ou é aceitável algum apuramento?
* Que nível de cuidado e consistência espera que os rotuladores se apliquem na definição de caixas de delimitação?
* Como rotular o objeto que é parcialmente mostrado na imagem? 
* Como rotular o objeto que parcialmente coberto por outro objeto?

>[!NOTE]
> Certifique-se de que os rótulos serão capazes de selecionar as primeiras 9 etiquetas utilizando as teclas 1-9.

## <a name="use-ml-assisted-labeling"></a>Utilize a rotulagem assistida ML

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

A página de **rotulagem assistida ML** permite-lhe acionar modelos automáticos de aprendizagem automática para acelerar a tarefa de rotulagem. No início do seu projeto de rotulagem, as imagens são baralhadas numa ordem aleatória para reduzir o preconceito potencial. No entanto, quaisquer preconceitos presentes no conjunto de dados refletir-se-ão no modelo treinado. Por exemplo, se 80% das suas imagens forem de uma única classe, então aproximadamente 80% dos dados utilizados para treinar o modelo serão dessa classe. Esta formação não inclui a aprendizagem ativa.

Esta funcionalidade está disponível para tarefas de classificação de imagem (multi-classe ou multi-etiquetas).  

*Selecione Ativar a rotulagem assistida ML* e especificar um GPU para permitir a rotulagem assistida, que consiste em duas fases:
* Clustering
* Pré-rotulagem

O número exato de imagens etiquetadas necessárias para iniciar a rotulagem assistida não é um número fixo.  Isto pode variar significativamente de um projeto de rotulagem para outro. Para alguns projetos, por vezes é possível ver tarefas pré-etiquetadas ou de cluster depois de 300 imagens terem sido rotuladas manualmente. ML Assisted Labeling usa uma técnica chamada *Transfer Learning,* que usa um modelo pré-treinado para iniciar o processo de treino. Se as aulas do seu conjunto de dados forem semelhantes às do modelo pré-treinado, as pré-etiquetas podem estar disponíveis após apenas algumas centenas de imagens etiquetadas manualmente. Se o seu conjunto de dados for significativamente diferente dos dados utilizados para pré-treinar o modelo, pode demorar muito mais tempo.

Uma vez que as etiquetas finais ainda dependem da entrada do rotulador, esta tecnologia é por vezes chamada de humana na rotulagem *em loop.*

### <a name="clustering"></a>Clustering

Após a apresentação de um certo número de rótulos, o modelo de machine learning começa a agrupar imagens semelhantes.  Estas imagens semelhantes são apresentadas aos rotuladores no mesmo ecrã para acelerar a marcação manual. O agrupamento é especialmente útil quando o rotulador está a visualizar uma grelha de 4, 6 ou 9 imagens. 

Uma vez treinado um modelo de aprendizagem automática nos seus dados manualmente rotulados, o modelo é truncado até à sua última camada totalmente conectada. As imagens não rotuladas são então transmitidas através do modelo truncado num processo vulgarmente conhecido como "incorporação" ou "caracterização". Isto incorpora cada imagem num espaço de alta dimensão definido por esta camada de modelo. As imagens que são vizinhos mais próximos do espaço são usadas para agrupar tarefas. 

### <a name="prelabeling"></a>Pré-rotulagem

Depois de serem submetidas mais etiquetas de imagem, um modelo de classificação é usado para prever etiquetas de imagem.  O rotulador vê agora páginas que contêm etiquetas previstas já presentes em cada imagem.  A tarefa é então rever estes rótulos e corrigir quaisquer imagens mal rotuladas antes de submeter a página.  

Uma vez treinado um modelo de aprendizagem automática nos seus dados manualmente rotulados, o modelo é avaliado num conjunto de testes de imagens com etiquetamanual para determinar a sua precisão em diferentes limiares de confiança. Este processo de avaliação é utilizado para determinar um limiar de confiança acima do qual o modelo é suficientemente preciso para apresentar pré-etiquetas. O modelo é então avaliado com dados não rotulados. Imagens com previsões mais confiantes do que este limiar são usadas para pré-rotulagem.

> [!NOTE]
> A rotulagem assistida ml está disponível **apenas** nos espaços de trabalho da edição da Enterprise.

## <a name="initialize-the-labeling-project"></a>Inicializar o projeto de rotulagem

Após a rubrica do projeto de rotulagem, alguns aspetos do projeto são imutáveis. Não é possível alterar o tipo de tarefa ou o conjunto de dados. *Pode* modificar as etiquetas e o URL para a descrição da tarefa. Reveja cuidadosamente as definições antes de criar o projeto. Depois de submeter o projeto, é devolvido à página inicial da **Data Labeling,** que mostrará o projeto como **Inicialização**. Esta página não atualiza automaticamente. Assim, após uma pausa, refresque manualmente a página para ver o estado do projeto como **Criado**.

## <a name="manage-teams-and-people"></a>Gerir equipas e pessoas

Por padrão, cada projeto de rotulagem que crias recebe uma nova equipa consigo como membro. Mas as equipas também podem ser partilhadas entre projetos. E os projetos podem ter mais do que uma equipa. Para criar uma equipa, selecione **Adicionar equipa** na página das **Equipas.**

Geres as pessoas na página do **People.** Adicione e remova as pessoas por endereço de e-mail. Cada rotulador tem de autenticar através da sua conta Microsoft ou do Diretório Ativo Azure, se a utilizar.  

Depois de adicionar uma pessoa, pode atribuir essa pessoa a uma ou mais equipas: Vá à página das **Equipas,** selecione a equipa e, em seguida, selecione **Atribuir pessoas** ou **remover pessoas**.

Para enviar um e-mail para a equipa, selecione a equipa para ver a página **de detalhes da Equipa.** Nesta página, selecione **a equipa de e-mail** para abrir um rascunho de e-mail com os endereços de todos na equipa.

## <a name="run-and-monitor-the-project"></a>Executar e monitorizar o projeto

Depois de rubricar o projeto, o Azure vai começar a executá-lo. Selecione o projeto na página principal de **Rotulagem** de Dados para ir aos **detalhes do Projeto**. O **separador Dashboard** mostra o progresso da tarefa de rotulagem.

No separador **Dados,** pode ver o seu conjunto de dados e rever os dados rotulados. Se vir dados incorretamente rotulados, selecione-os e escolha **Rejeitar**, que removerá as etiquetas e colocará os dados novamente na fila não marcada.

Utilize o separador **Team** para atribuir ou desatribuir equipas ao projeto.

Para interromper ou reiniciar o projeto, selecione o botão **Pausa**/**Iniciar.** Só é possível rotular dados quando o projeto está em execução.

Pode rotular os dados diretamente da página de detalhes do **Projeto** selecionando **os dados**do Rótulo .

## <a name="add-new-label-class-to-a-project"></a>Adicione nova classe de etiquetas a um projeto

Durante o processo de rotulagem, pode descobrir que são necessárias etiquetas adicionais para classificar as suas imagens.  Por exemplo, pode querer adicionar uma etiqueta "Desconhecida" ou "Outra" para indicar imagens confusas.

Utilize estes passos para adicionar um ou mais rótulos a um projeto:

1. Selecione o projeto na página principal de Rotulagem de **Dados.**
1. No topo da página, selecione **Pause** para parar os rótulos da sua atividade.
1. Selecione o separador **Detalhes**.
1. Na lista à esquerda, selecione Aulas de **Etiqueta**.
1. No topo da lista, selecione **+ Adicionar Rótulos** ![Adicione um rótulo](media/how-to-create-labeling-projects/add-label.png)
1. Na forma, adicione a sua nova etiqueta e escolha como proceder.  Uma vez que mudou as etiquetas disponíveis para uma imagem, escolhe como tratar os dados já rotulados:
    * Comece de novo, removendo todos os rótulos existentes.  Escolha esta opção se quiser começar a rotular desde o início com o novo conjunto completo de etiquetas. 
    * Comece de novo, mantendo todos os rótulos existentes.  Escolha esta opção para marcar todos os dados como não rotulados, mas mantenha as etiquetas existentes como uma etiqueta padrão para imagens que foram previamente rotuladas.
    * Continue, mantendo todos os rótulos existentes. Escolha esta opção para manter todos os dados já rotulados como estão, e comece a usar a nova etiqueta para dados ainda não rotulados.
1. Modifique a sua página de instruções conforme necessário para a nova etiqueta.
1. Depois de ter adicionado todas as etiquetas novas, no topo da página selecione **Iniciar** a reiniciar o projeto.  

## <a name="export-the-labels"></a>Exportar os rótulos

Pode exportar os dados do rótulo para experimentação de Machine Learning a qualquer momento. As etiquetas de imagem podem ser exportadas em [formato COCO](http://cocodataset.org/#format-data) ou como um conjunto de dados de Aprendizagem automática Azure. Utilize o botão **Export** na página de detalhes do **Projeto** do seu projeto de rotulagem.

O ficheiro COCO é criado na loja de blob padrão do espaço de trabalho Azure Machine Learning numa pasta dentro *da exportação/coco*. Pode aceder ao conjunto de dados de Aprendizagem automática Azure exportado na secção **Datasets** de Machine Learning. A página de detalhes do conjunto de dados também fornece código de amostra para aceder aos seus rótulos a partir de Python.

![Conjunto de dados exportado](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Passos seguintes

* Rotular imagens para [classificação de imagem ou deteção de objetos](how-to-label-images.md)
* Saiba mais sobre [o Azure Machine Learning e o Machine Learning Studio (clássico)](compare-azure-ml-to-studio-classic.md)
