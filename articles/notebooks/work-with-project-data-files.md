---
title: Dados de importação e exportação com projetos com pré-visualização de cadernos Azure
description: Saiba como trazer dados para um projeto de pré-visualização de Cadernos Azure a partir de fontes externas e como exportar dados de um projeto.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: b3669128582d3bdd6a3c4506a040856ab7b07e9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85834119"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Trabalhar com ficheiros de dados em projetos de pré-visualização de cadernos Azure

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Os dados são a força vital de muitos cadernos Jupyter, especialmente cadernos usados para a ciência dos dados. Com os Cadernos Azure, você pode facilmente importar de uma variedade de fontes para um projeto, e depois usar esses dados de cadernos. Também pode ter cadernos que geram dados que estão armazenados no projeto, que depois pode descarregar para uso em outros locais.

O menu **Data** dentro de um caderno em execução também fornece comandos **upload** e **download,** que funcionam com ficheiros no projeto, bem como ficheiros temporários para a sessão de cadernos em curso.

Também pode usar código dentro de um portátil para aceder diretamente a uma variedade de fontes de dados, incluindo ficheiros dentro de um projeto. Também pode aceder a dados arbitrários utilizando comandos numa célula de código. Como esses dados são armazenados em variáveis dentro da sessão de cadernos, não serão guardados no projeto a menos que utilize código para gerar especificamente ficheiros de projeto.

Trabalhar com código em dados é mais bem experimentado dentro de um próprio caderno de execução: para o efeito, consulte o [Caderno de Acesso aos seus dados no caderno de amostras Azure Notebooks](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

O restante deste artigo fornece detalhes sobre as operações de ficheiros ao nível do projeto.

## <a name="import-data"></a>Importar dados

Pode trazer ficheiros para um projeto a partir do painel de instrumentos do projeto, ou dentro de um caderno de execução utilizando o menu **Data** ou um comando como `curl` .

### <a name="import-files-from-the-project-dashboard"></a>Importar ficheiros do painel de instrumentos do projeto

1. No projeto, navegue para a pasta onde pretende importar os ficheiros.

1. Selecione o comando **Upload,** em seguida, **quer do URL** quer do **computador,** e projete os dados necessários para os dados que pretende importar:

   - **A partir de URL**: Introduza o endereço de origem no campo URL de **ficheiro** e o nome de ficheiro para atribuir ao portátil no seu projeto no campo Nome de **Ficheiros.** Em seguida, **selecione + Adicionar Ficheiro** para adicionar o URL à lista de upload. Repita o processo para quaisquer URLs adicionais e, em seguida, selecione **'Fazer'**

     ![Upload do popup URL](media/quickstarts/upload-from-url-popup.png)

   - **A partir do computador**: Arraste e deixe cair ficheiros no popup, ou selecione **Choose Files**, em seguida, navegue para e selecione os ficheiros de dados que pretende importar. Pode deixar cair ou escolher qualquer número de ficheiros, de qualquer tipo e formato, porque cabe ao código do portátil abrir o ficheiro e analisar os seus dados.

     ![Upload do popup do computador](media/quickstarts/upload-from-computer-popup.png)

1. Uma vez importados, os ficheiros aparecem no painel de instrumentos do projeto e podem ser acedidos dentro do código do portátil utilizando nomes de pathnam relativos à pasta que contém.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importar ficheiros do menu do Ficheiro num caderno

1. Dentro de um caderno em execução, selecione o comando **'Upload de ficheiros':**  >  **Upload**

    ![Comando do menu de upload de ficheiros dentro de um caderno](media/file-menu-upload.png)

1. Na caixa de diálogo que abre, navegue e selecione os ficheiros que pretende carregar. Pode selecionar qualquer número de ficheiros de qualquer tipo. Selecione **Abrir** quando terminar.

1. No popup **de estado do upload** que aparece, selecione uma Pasta de **Destino** da lista de drop-down:

    - Pasta de sessão *~/* (): Envia ficheiros para a sessão de portátil atual, mas não cria ficheiros no projeto. A pasta da sessão é um par na pasta do projeto, mas não persiste após o fim da sessão. Para aceder aos ficheiros da sessão em código, prefixe os nomes de ficheiros com o caminho relativo *.. /*.

        A utilização da pasta de sessão é útil para a experimentação e evita desordenar o projeto com ficheiros que pode ou não necessitar numa base a longo prazo. Também pode fazer upload de ficheiros para a pasta de sessão que têm nomes idênticos aos ficheiros do projeto sem causar conflitos e sem ter de mudar o nome dos ficheiros. Por exemplo, digamos que já tem uma versão de *data.csv* no projeto, mas quer experimentar uma versão diferente de *data.csv*. Ao enviar o ficheiro para a pasta da sessão, pode executar o portátil utilizando dados no ficheiro carregado (referindo-se ao mesmo em código utilizando *.. /data.csv*) em vez dos dados no ficheiro do projeto.

    - Pasta de projeto *(/projeto):* envia ficheiros para o projeto onde podem ser acedidos utilizando nomes de pathnames relativos em código. O envio de um ficheiro para esta pasta é o mesmo que carregar um ficheiro no painel de instrumentos do projeto. O ficheiro é guardado com o projeto e está disponível em sessões posteriores.

        O upload falha se tentar carregar um ficheiro com o mesmo nome que já existe no projeto. Para substituir um ficheiro, faça o upload do novo ficheiro do painel de instrumentos do projeto, o que lhe dá a opção de substituir em excesso.

1. Selecione **Iniciar upload** para concluir o processo.

### <a name="create-or-import-files-using-commands"></a>Criar ou importar ficheiros usando comandos

Pode utilizar comandos dentro de um terminal ou dentro de uma célula de código Python para criar ficheiros dentro das pastas do projeto e da sessão. Por exemplo, os comandos gostam `curl` e `wget` descarregam ficheiros da Internet diretamente.

Para descarregar ficheiros no terminal, selecione o comando **Terminal** no painel de instrumentos do projeto e, em seguida, introduza comandos adequados:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Quando utilizar uma célula de código Python num caderno, prefixe os comandos com `!` .

A pasta do projeto é a pasta predefinida, especificando assim um nome de ficheiro de destino como *oil_price.csv* cria o ficheiro no projeto. Para criar um ficheiro de sessão, prefixe o nome com *.. /* como em *.. /oil_price.csv. *

### <a name="create-files-in-code"></a>Criar ficheiros em código

Ao utilizar código que cria um ficheiro, como a `write_csv` função pandas, os nomes de pathnam são sempre relativos à pasta do projeto. Usando *.. /* cria um ficheiro de sessão que é descartado quando o caderno é parado e fechado.

## <a name="export-files"></a>Ficheiros de exportação

Pode exportar dados do painel de instrumentos do projeto ou dentro de um caderno.

## <a name="export-files-from-the-project-dashboard"></a>Ficheiros de exportação do painel de instrumentos do projeto

No painel de instrumentos do projeto, clique com o botão direito num ficheiro e selecione **Download**:

![Descarregue o comando no menu de contexto de item do projeto](media/download-command.png)

Também pode selecionar um ficheiro e utilizar o comando **Descarregamento** (atalho de teclado: d) no painel de instrumentos:

![Descarregue o comando da barra de ferramentas no painel de instrumentos do projeto](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Ficheiros de exportação do menu Data em um caderno

1. Selecione o comando do menu **File**  >  **de descarregamento de** ficheiros:

    ![Data Descarregue o comando do menu dentro de um caderno](media/file-menu-download.png)

1. Aparece um pop-up que mostra as pastas na sessão; a pasta do *projeto* contém os ficheiros do projeto:

    ![Data Download de comando popup no qual seleciona ficheiros e pastas](media/file-menu-download-popup.png)

1. Selecione as caixas à esquerda dos ficheiros e pastas que pretende descarregar e, em seguida, selecione **Download Selected**.

1. O portátil prepara um único ficheiro *.zip* contendo os ficheiros selecionados, que depois guarda como normalmente faz a partir do seu navegador. O notebook cria um ficheiro *.zip* mesmo quando descarrega um único ficheiro.

## <a name="next-steps"></a>Passos seguintes

- [Aceda aos dados da nuvem de acesso em um caderno](access-data-resources-jupyter-notebooks.md)
