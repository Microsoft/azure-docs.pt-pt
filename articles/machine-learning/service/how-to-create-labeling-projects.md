---
title: Obter rótulos de dados
titleSuffix: Azure Machine Learning
description: Este artigo mostra como criar e executar rótulos de projetos para marcar dados para o aprendizado de máquina.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: ca3486610d6cf71ba315e407b58a2a2551ad6ee1
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837488"
---
# <a name="get-labels-for-data"></a>Obter rótulos de dados

Rotular grandes quantidades de dados costuma ser uma dor de cabeça nos projetos do Machine Learning. Os projetos ML com um componente da pesquisa Visual computacional, como classificação de imagem ou detecção de objetos, geralmente exigem milhares de imagens e rótulos correspondentes. 
 
Azure Machine Learning fornece um local central para criar, gerenciar e monitorar projetos de rotulagem. Rotular projetos ajuda a coordenar os dados, os rótulos e os membros da equipe, permitindo que você gerencie com mais eficiência as tarefas de rotulagem. Atualmente, as tarefas com suporte são a classificação de imagem, de vários rótulos ou de várias classes, e da identificação de objeto usando caixas delimitadas.

O Azure rastreia o progresso e mantém a fila de tarefas de rotulagem incompletas. Rotuladores não exigem uma conta do Azure para participar. Uma vez autenticado com sua MSA (conta da Microsoft) ou [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), eles podem fazer tantos quanto forem pequenos rótulos à medida que o tempo permitir. Eles podem atribuir e alterar rótulos usando atalhos de teclado. 

Você pode iniciar e parar o projeto, adicionar e remover pessoas e equipes e monitorar o progresso. Você pode exportar os dados rotulados em um formato COCO ou como um DataSet ML do Azure. 

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um projeto
> * Especificar os dados e a estrutura do projeto
> * Gerenciar equipes e pessoas trabalhando no projeto
> * Executar e monitorar o projeto
> * Exportar os rótulos 

## <a name="prerequisites"></a>Pré-requisitos

* Os dados que você deseja rotular, seja em arquivos locais ou já no armazenamento do Azure
* O conjunto de rótulos que você deseja aplicar
* Instruções para rotulagem
* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje
* Uma área de trabalho do Azure Machine Learning. Consulte [criar um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Criar um projeto de rotulagem

Os projetos de rotulagem são administrados a partir de [Azure Machine Learning](https://ml.azure.com/). A página **rotulando projetos** permite que você gerencie seus projetos, equipes e pessoas. Um projeto tem uma ou mais equipes atribuídas a ele, e uma equipe tem uma ou mais pessoas atribuídas a ela. 

Se os dados já estiverem armazenados no armazenamento de BLOBs do Azure, você deverá disponibilizá-los como um repositório de armazenamento antes de criar seu projeto de rotulagem. Para obter informações, consulte [criar e registrar armazenamentos de](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores)dados. 

Para criar um projeto, escolha **Adicionar projeto**. Dê a ele um nome apropriado e selecione **rotulagem tipo de tarefa**. 

![Rotulando assistente de criação de projeto](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Escolha **classificação de imagem com vários rótulos** para projetos nos **quais _um ou mais_**  rótulos de um conjunto de classes podem ser aplicados a uma imagem. Por exemplo, uma foto de um cachorro pode ser rotulada com *cachorro* e *dia*
* Escolha a **classificação de imagem multiclasse** para projetos nos quais apenas uma **única classe** de um conjunto de classes pode ser aplicada a uma imagem
* Escolha a **identificação de objeto (caixa delimitadora)** para projetos nos quais a tarefa é para atribuir uma classe a um objeto dentro de uma imagem e para especificar uma caixa delimitadora ao redor do objeto

Escolha **Avançar** quando estiver pronto para continuar.

## <a name="specify-data-to-be-labeled"></a>Especificar os dados a serem rotulados

Se você já tiver criado um conjunto de dados que contenha seu dado, poderá selecioná-lo na lista suspensa **selecionar um conjunto** de dados existente. Ou escolha **criar um conjunto de uma** para selecionar um repositório de armazenamento do Azure existente ou carregar arquivos locais. 

### <a name="create-a-dataset-from-an-azure-datastore"></a>Criar um conjunto de um DataSet de um repositório de armazenamento do Azure

Embora a escolha do carregamento direto de arquivos locais seja adequada para muitos casos de uso, [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) é mais robusta e mais rápida para transferir quantidades significativas de dados. É recomendável Gerenciador de Armazenamento do Azure como a maneira padrão de mover arquivos.

Para criar um conjunto de dados a partir de um dado que você já armazenou no armazenamento de BLOBs do Azure:

1. Escolha **criar um conjunto de um** e **do repositório de armazenamento**
1. Atribuir um **nome** para o conjunto de seus conjuntos de seus
1. Você deve escolher "arquivo" como o **tipo de conjunto de texto**  
1. Selecione o repositório de armazenamento 
1. Se os dados estiverem em uma subpasta no armazenamento de BLOBs, escolha **procurar** para selecionar o caminho. 
    * Além disso, você pode acrescentar `/**` depois do caminho para incluir todos os arquivos em subpastas do caminho selecionado
    * Use `**/*.*` para incluir todos os dados no contêiner atual e subpastas
1. Forneça uma descrição do seu conjunto de seus conjuntos de seus
1. Escolha **Avançar** 
1. Confirme os detalhes. Você pode escolher **voltar** para modificar as configurações ou escolher **criar** para criar o conjunto de um

### <a name="create-a-dataset-by-uploading-data"></a>Criar um DataSet carregando dados

Se você deseja carregar seus dados diretamente:

1. Escolha **criar um conjunto de um** e **de arquivos locais**
1. Atribuir um **nome** para o conjunto de seus conjuntos de seus
1. Você deve escolher "arquivo" como o **tipo de conjunto de texto**
1. Se você escolher **Configurações avançadas**, você poderá personalizar o armazenamento de dados, o contêiner e o caminho para os data
1. Escolha **procurar** para selecionar os arquivos locais para carregamento
1. Forneça uma descrição do seu conjunto de seus conjuntos de seus
1. Escolha **Avançar** 
1. Confirme os detalhes. Você pode escolher **voltar** para modificar as configurações ou escolher **criar** para criar o conjunto de um

Os dados são carregados no repositório de blob padrão (`workspaceblobstore`) do seu espaço de trabalho do Azure ML.

## <a name="specify-label-classes"></a>Especificar classes de rótulo

Na página **classes de rótulo** , você especifica o conjunto de classes usadas para categorizar seus dados. Pense nessas classes, pois a precisão e a velocidade dos rotuladores serão afetadas pela facilidade com a qual podem escolher corretamente entre elas. Por exemplo, em vez de soletrar o genus completo e a espécie de plantas ou animais, pode ser melhor usar códigos de campo ou abreviar o genus. 

Insira um rótulo por linha, usando o botão de **+** para adicionar uma nova linha. Se você tiver mais de 3 ou 4 rótulos, mas menos de 10, considere prefixando-os com "1:", "2:", etc. para fornecer algumas diretrizes para rotuladores usando as teclas numéricas para acelerar seu trabalho. 

## <a name="describe-the-labeling-task"></a>Descrever a tarefa de rotulagem

É importante explicar claramente a tarefa de rotulagem. A página **instruções de rotulagem** permite que você vincule a um site externo para obter instruções a serem apresentadas aos rotuladores. Mantenha as instruções orientadas a tarefas e apropriadas para o público. 

* Quais são os rótulos que eles verão e como eles vão escolher entre eles? Há um texto de referência ao qual eles devem se referir?
* O que eles devem fazer se nenhum rótulo parecer apropriado? 
* O que eles devem fazer se vários rótulos parecerem apropriados?
* Qual limite de confiança deve ser aplicado a um rótulo? Deseja sua "melhor adivinhação" se não tiver certeza?
* O que devemos fazer com objetos parcialmente obstruídos ou sobrepostos de interesse?
* O que devemos fazer se um objeto de interesse for recortado pela borda da imagem?
* O que devemos fazer se achar que houve um erro com uma imagem depois de enviá-la? 

Com caixas delimitadoras, outras perguntas importantes incluem:

* Como a caixa delimitadora é definida para essa tarefa? Ela deve ser totalmente interior ao objeto, cortada o mais próximo possível de sua extensão, ou uma quantidade de espaço livre aceitável? 
* Que nível de cuidado e consistência você espera que o Labeler aplique para definir caixas delimitadoras?

>[!Note]
> Não deixe de dizer que o Labeler poderá escolher entre os primeiros 9 rótulos com chaves de número 1-9. 

## <a name="initialize-the-labeling-project"></a>Inicializar o projeto de rotulagem

Depois de inicializados, alguns aspectos do projeto de rotulagem são imutáveis: não é possível alterar o tipo de tarefa ou o conjunto de texto. Você pode modificar os rótulos e alterar a URL para a descrição da tarefa. Examine cuidadosamente as configurações antes de criar o projeto. Depois de enviar o projeto, você será levado de volta para a home page do **rótulo** , que mostrará o projeto como **inicializando**. Esta página não é atualizada automaticamente, portanto, após algum tempo, atualizá-la manualmente mostrará o projeto como **criado**. 

## <a name="manage-teams-and-people"></a>Gerenciar equipes e pessoas

Um projeto de rotulagem Obtém uma equipe padrão e a adiciona como um membro padrão. Cada projeto de rotulagem Obtém uma nova equipe padrão, mas as equipes podem ser compartilhadas entre projetos. Os projetos podem ter mais de uma equipe. A criação de uma equipe é feita escolhendo **Adicionar equipe** na página **equipes** . 

As pessoas são gerenciadas na página **pessoas** . Você pode adicionar e remover pessoas com chave em seu endereço de email. Cada Labeler terá que se autenticar usando sua conta da Microsoft ou Azure Active Directory se você usá-la.  

Depois de adicionar uma pessoa, você pode atribuí-las a uma ou mais equipes. Navegue até a página **equipes** , selecione a equipe específica na qual você está interessado e, em seguida, use **atribuir pessoas** ou **remover pessoas** conforme desejado.

Se você já deseja enviar um email para todos na equipe, você pode fazer isso escolhendo a equipe para abrir a página de detalhes da **equipe** . Nessa página, o botão **equipe de email** abrirá seu editor de email com os endereços de todos na equipe.

## <a name="run-and-monitor-the-project"></a>Executar e monitorar o projeto

Depois que o projeto for inicializado, o Azure começará a executá-lo. Se, na página de **rotulagem** principal, você clicar no projeto, você será levado para os **detalhes do projeto**. A guia **painel** mostrará o progresso na tarefa de rotulagem. 

Na guia **dados** , você pode examinar seu conjunto de dados e examinar os dado rotulados. Se você vir dados rotulados incorretamente, poderá **selecioná** -los e escolher **rejeitar**, o que removerá os rótulos e colocará os dados de volta na fila sem rótulo. 

Na guia **equipe** , você pode atribuir ou cancelar a atribuição de equipes a este projeto. 

Se você quiser colocar o projeto offline ou online, escolha o botão **Pausar**/**Iniciar** , que alterna o estado de execução do projeto.

Você pode rotular dados diretamente da página de **detalhes do projeto** selecionando **dados do rótulo**. Você só pode rotular dados quando o projeto está em execução. 

## <a name="export-the-labels"></a>Exportar os rótulos

A qualquer momento, você pode exportar os dados do rótulo para experimentação do Machine Learning. Os rótulos de imagem podem ser exportados no [formato coco](http://cocodataset.org/#format-data) ou como um conjunto de um Azure ml. Você encontrará o botão **Exportar** na página de **detalhes do projeto** de seu projeto de rotulagem.

O arquivo COCO é criado no repositório de blob padrão do espaço de trabalho do Azure ML em uma pasta dentro de **Export/coco**. Você pode acessar o conjunto de Azure Machine Learning do Azure ML exportado na seção **conjuntos** de banco de A página de detalhes do conjunto de informações também fornece um código de exemplo para acessar seus rótulos do Python.

![DataSet exportado](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Passos Seguintes

* Imagens de rótulo para [classificação de imagem ou detecção de objeto](how-to-label-images.md)
* Saiba mais sobre o [Azure Machine Learning e o estúdio](../compare-azure-ml-to-studio-classic.md)