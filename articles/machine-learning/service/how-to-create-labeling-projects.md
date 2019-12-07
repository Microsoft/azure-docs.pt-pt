---
title: Obter rótulos de dados
titleSuffix: Azure Machine Learning
description: Este artigo mostra como criar e executar rótulos de projetos para marcar dados para o aprendizado de máquina.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: c171310fd1c9caf78288fa156b353ecdd517d602
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894695"
---
# <a name="get-labels-for-data"></a>Obter rótulos de dados

Rotular dados de volumosos em projetos de aprendizado de máquina costuma ser uma dor de cabeça. Os projetos que têm um componente de visão computacional, como classificação de imagem ou detecção de objetos, geralmente exigem rótulos para milhares de imagens.
 
[Azure Machine Learning](https://ml.azure.com/) oferece um local central para criar, gerenciar e monitorar projetos de rotulagem. Use-o para coordenar dados, rótulos e membros da equipe para gerenciar com eficiência as tarefas de rotulagem. O Machine Learning dá suporte à classificação de imagem, com vários rótulos ou várias classes, e identificação de objeto junto com caixas delimitadas.

Machine Learning rastreia o progresso e mantém a fila de tarefas de rotulagem incompletas. Os rotuladores não precisam de uma conta do Azure para participar. Depois que eles são autenticados com seu conta Microsoft ou [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), eles podem fazer o máximo de rotulagem que o tempo permitir.

No Machine Learning, você inicia e interrompe o projeto, adiciona e remove pessoas e equipes e monitora o progresso. Você pode exportar dados rotulados no formato COCO ou como um conjunto de dado Azure Machine Learning.

> [!Important]
> Somente a classificação de imagem e a identificação de objeto rotulando projetos têm suporte no momento. Além disso, as imagens de dados devem estar disponíveis em um armazenamento de BLOBs do Azure. (Se você não tiver um repositório de armazenamento existente, poderá carregar imagens durante a criação do projeto.) 

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um projeto
> * Especificar os dados e a estrutura do projeto
> * Gerenciar equipes e pessoas que trabalham no projeto
> * Executar e monitorar o projeto
> * Exportar os rótulos


## <a name="prerequisites"></a>Pré-requisitos

* Os dados que você deseja rotular, seja em arquivos locais ou no armazenamento do Azure.
* O conjunto de rótulos que você deseja aplicar.
* As instruções para rotulagem.
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um espaço de trabalho Machine Learning. Consulte [criar um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Criar um projeto de rotulagem

Os projetos de rotulagem são administrados a partir de Azure Machine Learning. Use a página **rotulando projetos** para gerenciar seus projetos e pessoas. Um projeto tem uma ou mais equipes atribuídas a ele, e uma equipe tem uma ou mais pessoas atribuídas a ela.

Se seus dados já estiverem no armazenamento de BLOBs do Azure, você deverá disponibilizá-los como um armazenamento de dados antes de criar o projeto de rotulagem. Para obter detalhes, consulte [criar e registrar repositórios de armazenamento](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores).

Para criar um projeto, selecione **Adicionar projeto**. Dê ao projeto um nome apropriado e selecione **rotulagem tipo de tarefa**.

![Rotulando assistente de criação de projeto](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Escolha **classificação de imagem com vários rótulos** para projetos quando desejar aplicar *um ou mais* rótulos de um conjunto de classes a uma imagem. Por exemplo, uma foto de um cachorro pode ser rotulada com *cachorro* e *dia*.
* Escolha **classificação de imagem multiclasse** para projetos quando você quiser aplicar apenas uma *única classe* de um conjunto de classes a uma imagem.
* Escolha **identificação de objeto (caixa delimitadora)** para projetos quando desejar atribuir uma classe e uma caixa delimitadora a cada objeto dentro de uma imagem.

Selecione **Avançar** quando estiver pronto para continuar.

## <a name="specify-the-data-to-label"></a>Especificar os dados a serem rotulados

Se você já tiver criado um conjunto de dados que contenha seu dado, selecione-o na lista suspensa **selecionar um conjunto** de dados existente. Ou então, selecione **criar um conjunto de uma** para usar um repositório de armazenamento do Azure existente ou carregar arquivos locais.

### <a name="create-a-dataset-from-an-azure-datastore"></a>Criar um conjunto de um DataSet de um repositório de armazenamento do Azure

Em muitos casos, é bom carregar apenas arquivos locais. Mas [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) fornece uma maneira mais rápida e robusta de transferir uma grande quantidade de dados. É recomendável Gerenciador de Armazenamento como a maneira padrão de mover arquivos.

Para criar um conjunto de dados a partir de um dado que você já armazenou no armazenamento de BLOBs do Azure:

1. Selecione **criar um conjunto** **de > do repositório de armazenamento**.
1. Atribua um **nome** ao seu conjunto de seus conjuntos de seus.
1. Escolha **arquivo** como o **tipo de conjunto de texto**.  
1. Selecione o repositório de armazenamento.
1. Se os dados estiverem em uma subpasta no armazenamento de BLOBs, escolha **procurar** para selecionar o caminho.
    * Acrescente "/* *" ao caminho para incluir todos os arquivos em subpastas do caminho selecionado.
    * Acrescente "* */* . *" para incluir todos os dados no contêiner atual e suas subpastas.
1. Forneça uma descrição para o conjunto de seus conjuntos de seus.
1. Selecione **Seguinte**.
1. Confirme os detalhes. Selecione **voltar** para modificar as configurações ou **criar** para criar o conjunto de um.

### <a name="create-a-dataset-from-uploaded-data"></a>Criar um conjunto de dados com base em data de carregamento

Para carregar seus dados diretamente:

1. Selecione **criar um conjunto** de > **de arquivos locais**.
1. Atribua um **nome** ao seu conjunto de seus conjuntos de seus.
1. Escolha "arquivo" como o **tipo de conjunto de texto**.
1. *Opcional:* Selecione **Configurações avançadas** para personalizar o armazenamento de dados, o contêiner e o caminho para os Data.
1. Selecione **procurar** para selecionar os arquivos locais a serem carregados.
1. Forneça uma descrição do seu conjunto de seus conjuntos de seus.
1. Selecione **Seguinte**.
1. Confirme os detalhes. Selecione **voltar** para modificar as configurações ou **criar** para criar o conjunto de um.

Os dados são carregados no repositório de blob padrão ("workspaceblobstore") de seu espaço de trabalho do Machine Learning.

## <a name="specify-label-classes"></a>Especificar classes de rótulo

Na página **classes de rótulo** , especifique o conjunto de classes para categorizar os dados. Faça isso com cuidado, pois a precisão e a velocidade dos rotuladores serão afetadas por sua capacidade de escolher entre as classes. Por exemplo, em vez de soletrar o genus completo e as espécies de plantas ou animais, use um código de campo ou abreviar o genus.

Insira um rótulo por linha. Use o botão **+** para adicionar uma nova linha. Se você tiver mais de 3 ou 4 rótulos, mas menos de 10, talvez queira prefixar os nomes com números ("1:", "2:") para que os rotuladores possam usar as teclas numéricas para acelerar o trabalho.

## <a name="describe-the-labeling-task"></a>Descrever a tarefa de rotulagem

É importante explicar claramente a tarefa de rotulagem. Na página **instruções de rotulagem** , você pode adicionar um link a um site externo para instruções de rotulagem. Mantenha as instruções orientadas a tarefas e apropriadas para o público. Considere estas perguntas:

* Quais são os rótulos que eles verão e como eles vão escolher entre eles? Há um texto de referência ao qual se refere?
* O que eles devem fazer se nenhum rótulo parecer apropriado?
* O que eles devem fazer se vários rótulos parecerem apropriados?
* Qual limite de confiança deve ser aplicado a um rótulo? Deseja sua "melhor adivinhação" se não tiver certeza?
* O que devemos fazer com objetos parcialmente obstruídos ou sobrepostos de interesse?
* O que devemos fazer se um objeto de interesse for recortado pela borda da imagem?
* O que eles devem fazer depois que enviarem um rótulo se acreditarem que fizeram um erro?

Para caixas delimitadoras, perguntas importantes incluem:

* Como a caixa delimitadora é definida para essa tarefa? Ele deve ser totalmente no interior do objeto ou deve estar no exterior? Eles devem ser cortados de maneira mais próxima possível, ou algum espaço é aceitável?
* Que nível de cuidado e consistência você espera que os rotuladores apliquem na definição de caixas delimitadoras?

>[!NOTE]
> Lembre-se de observar que os rotuladores poderão selecionar os primeiros 9 rótulos usando as chaves de número 1-9.

## <a name="initialize-the-labeling-project"></a>Inicializar o projeto de rotulagem

Depois que o projeto de rotulação é inicializado, alguns aspectos do projeto são imutáveis. Você não pode alterar o tipo de tarefa ou o conjunto de texto. Você *pode* modificar os rótulos e a URL para a descrição da tarefa. Examine cuidadosamente as configurações antes de criar o projeto. Depois de enviar o projeto, você será retornado para a home page do **rótulo** , que mostrará o projeto como **inicializando**. Esta página não é atualizada automaticamente. Portanto, após uma pausa, atualize manualmente a página para ver o status do projeto como **criado**.

## <a name="manage-teams-and-people"></a>Gerenciar equipes e pessoas

Por padrão, cada projeto de rotulagem que você cria Obtém uma nova equipe com você como um membro. Mas as equipes também podem ser compartilhadas entre projetos. E os projetos podem ter mais de uma equipe. Para criar uma equipe, selecione **Adicionar equipe** na página **equipes** .

Você gerencia pessoas na página **pessoas** . Adicionar e remover pessoas por endereço de email. Cada Labeler precisa se autenticar por meio de seu conta Microsoft ou Azure Active Directory, se você usá-lo.  

Depois de adicionar uma pessoa, você pode atribuir essa pessoa a uma ou mais equipes: Vá para a página **equipes** , selecione a equipe e, em seguida, selecione **atribuir pessoas** ou **remover pessoas**.

Para enviar um email para a equipe, selecione a equipe para exibir a página de **detalhes da equipe** . Nessa página, selecione **equipe de email** para abrir um rascunho de email com os endereços de todos na equipe.

## <a name="run-and-monitor-the-project"></a>Executar e monitorar o projeto

Depois de inicializar o projeto, o Azure começará a executá-lo. Selecione o projeto na página de **rotulagem** principal para ir para os **detalhes do projeto**. A guia **painel** mostra o progresso da tarefa de rotulagem.

Na guia **dados** , você pode ver seu conjunto de dados e examinar os dado rotulados. Se você vir dados rotulados incorretamente, selecione-o e escolha **rejeitar**, que removerá os rótulos e colocará os dados de volta na fila sem rótulo.

Use a guia **equipe** para atribuir ou cancelar a atribuição de equipes ao projeto.

Para pausar ou reiniciar o projeto, selecione o botão **Pausar**/**Iniciar** . Você só pode rotular dados quando o projeto está em execução.

Você pode rotular dados diretamente da página de **detalhes do projeto** selecionando **dados do rótulo**.

## <a name="export-the-labels"></a>Exportar os rótulos

Você pode exportar os dados do rótulo para Machine Learning experimentação a qualquer momento. Os rótulos de imagem podem ser exportados no [formato coco](http://cocodataset.org/#format-data) ou como um conjunto de Azure Machine Learning. Use o botão **Exportar** na página **detalhes do projeto** de seu projeto de rotulagem.

O arquivo COCO é criado no repositório de blob padrão do espaço de trabalho Azure Machine Learning em uma pasta dentro de *Export/coco*. Você pode **acessar o conjunto** de Azure Machine Learning exportado na seção DataSets de Machine Learning. A página de detalhes do conjunto de informações também fornece um código de exemplo para acessar seus rótulos do Python.

![DataSet exportado](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Passos seguintes

* Imagens de rótulo para [classificação de imagem ou detecção de objeto](how-to-label-images.md)
* Saiba mais sobre [Azure Machine Learning e Machine Learning Studio (clássico)](../compare-azure-ml-to-studio-classic.md)
