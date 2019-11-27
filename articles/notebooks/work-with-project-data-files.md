---
title: Importar e exportar dados com projetos com blocos de notas do Azure
description: Como colocar dados num projeto de blocos de notas do Azure a partir de origens externas e como exportar dados de um projeto.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: bd7ba27859e9d05c0d57c2f78b6449c2bc48ca33
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277389"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Trabalhar com arquivos de dados em projetos de bloco de notas do Azure

Os dados são a alma de muitas Jupyter notebooks, especialmente blocos de notas utilizados para ciência de dados. Com blocos de notas do Azure, pode facilmente importar a partir de uma variedade de origens num projeto e, em seguida, utilizar esses dados de blocos de notas. Também pode ter blocos de notas gerar dados armazenados no projeto, que, em seguida, pode ser baixado para uso em outro lugar.

O menu **dados** em um notebook em execução também fornece comandos de **upload** e **Download** , que funcionam com arquivos no projeto, bem como arquivos temporários para a sessão atual do bloco de anotações.

Também pode utilizar código dentro de um bloco de notas para aceder a uma variedade de origens de dados diretamente, incluindo ficheiros dentro de um projeto. Também pode acessar dados arbitrários com comandos numa célula de código. Uma vez que esses dados são armazenados em variáveis dentro da sessão de bloco de notas, ele não será guardado no projeto, a menos que usar o código para gerar especificamente os arquivos de projeto.

O trabalho com código em dados é mais bem experiente em um próprio bloco de anotações em execução: para essa finalidade, consulte a [introdução aos seus dados no Azure notebooks bloco de anotações de exemplo](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

O restante deste artigo fornece detalhes sobre as operações de arquivo de nível de projeto.

## <a name="import-data"></a>Importar dados

Você pode colocar arquivos em um projeto no painel do projeto ou em um bloco de anotações em execução usando o menu **dados** ou um comando como `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Importar ficheiros a partir do dashboard do projeto

1. No projeto, navegue para a pasta onde pretende importar os ficheiros.

1. Selecione o comando **carregar** , **de URL** ou **de computador** e projeto os detalhes necessários para os dados que você deseja importar:

   - **Da URL**: Insira o endereço de origem no campo **URL do arquivo** e o nome do arquivo para atribuir ao bloco de anotações em seu projeto no campo nome do **arquivo** . Em seguida, selecione **+ Adicionar arquivo** para adicionar a URL à lista de carregamento. Repita o processo para qualquer URL adicional e, em seguida, selecione **concluído**.

     ![Carregar a partir do pop-up de URL](media/quickstarts/upload-from-url-popup.png)

   - **Em computador**: arraste e solte arquivos para o pop-up, ou selecione **escolher arquivos**, navegue até e selecione os arquivos de dados que deseja importar. Pode remover ou escolher qualquer número de ficheiros, de qualquer tipo e o formato, porque é o código no bloco de notas, abra o ficheiro e analisar os dados.

     ![Carregar a partir do pop-up de computador](media/quickstarts/upload-from-computer-popup.png)

1. Depois de importados, os ficheiros são apresentados no dashboard do projeto e podem ser acedidos no código do bloco de notas com pathnames relativo para a pasta que contém.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importar arquivos do menu arquivo em um bloco de anotações

1. Em um notebook em execução, selecione o comando **arquivo** > **carregar** :

    ![Comando de menu de carregamento dentro de um bloco de notas do ficheiro](media/file-menu-upload.png)

1. Na caixa de diálogo que se abre, navegue até e selecione os ficheiros que pretende carregar. Pode selecionar qualquer número de ficheiros de qualquer tipo. Selecione **abrir** quando terminar.

1. No pop-up **status de carregamento** que aparece, selecione uma **pasta de destino** na lista suspensa:

    - Pasta de sessão ( *~/* ): carrega arquivos na sessão atual do bloco de anotações, mas não cria arquivos no projeto. A pasta de sessão é um elemento de rede para a pasta de projeto, mas não persistem após a sessão termina. Para acessar os arquivos de sessão no código, Prefixe os nomes de arquivo com o caminho relativo *. /* .

        Com a pasta de sessão é útil para a experimentação e evita a sobrecarregar o projeto com arquivos, pode ou não poderá ter de forma longa duração. Também pode carregar ficheiros para a pasta de sessão que têm nomes idênticos para ficheiros do projeto sem causar conflitos e sem ter de mudar o nome de ficheiros. Por exemplo, digamos que você já tenha uma versão de *Data. csv* no projeto, mas queira experimentar uma versão diferente de *Data. csv*. Ao carregar o arquivo na pasta de sessão, você pode executar o bloco de anotações usando dados no arquivo carregado (fazendo referência a ele no código usando *.. /Data.csv*) em vez dos dados no arquivo do projeto.

    - Pasta do projeto ( */Project*): carrega arquivos no projeto, onde eles podem ser acessados usando caminhos relativos no código. Carregar um ficheiro para esta pasta é igual a carregar um ficheiro no dashboard do projeto. O ficheiro é guardado com o projeto e está disponível em sessões posteriores.

        A carregar falha se tentar carregar um ficheiro com o mesmo nome que um que já exista no projeto. Para substituir um ficheiro, carregue o novo ficheiro a partir do dashboard do projeto em vez disso, que lhe dá a opção para substituir.

1. Selecione **Iniciar carregamento** para concluir o processo.

### <a name="create-or-import-files-using-commands"></a>Criar ou importar ficheiros através de comandos

Pode utilizar os comandos dentro de um terminal ou dentro de uma célula de código do Python para criar arquivos dentro do projeto e a sessão de pastas. Por exemplo, comandos como `curl` e `wget` baixar arquivos diretamente da Internet.

Para baixar arquivos no terminal, selecione o comando **terminal** no painel do projeto e insira comandos adequados:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Ao usar uma célula de código Python em um bloco de anotações, Prefixe os comandos com `!`.

A pasta do projeto é a pasta padrão, portanto, especificar um nome de arquivo de destino como *oil_price. csv* cria o arquivo no projeto. Para criar um arquivo de sessão, Prefixe o nome com *.. /* como em *.. /oil_price. csv*.

### <a name="create-files-in-code"></a>Criar ficheiros no código

Ao usar o código que cria um arquivo, como a função pandas `write_csv`, os caminhos são sempre relativos à pasta do projeto. Usando *.. /* cria um arquivo de sessão que é Descartado quando o notebook é interrompido e fechado.

## <a name="export-files"></a>Ficheiros de exportação

Pode exportar dados do dashboard do projeto ou a partir um bloco de notas.

## <a name="export-files-from-the-project-dashboard"></a>Exportar ficheiros a partir do dashboard do projeto

No painel do projeto, clique com o botão direito do mouse em um arquivo e selecione **baixar**:

![Baixe o comando no menu de contexto do item de projeto](media/download-command.png)

Você também pode selecionar um arquivo e usar o comando de **Download** (atalho de teclado: d) no painel:

![Baixe o comando da barra de ferramentas no dashboard do projeto](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Exportar ficheiros no menu de dados num bloco de notas

1. Selecione o comando de menu **Download** de **arquivo** > :

    ![Comando de menu de transferência de dados dentro de um bloco de notas](media/file-menu-download.png)

1. Aparece um pop-up que mostra as pastas na sessão; a pasta do *projeto* contém os arquivos de projeto:

    ![Popup de comando de transferência de dados em que selecione ficheiros e pastas](media/file-menu-download-popup.png)

1. Selecione as caixas à esquerda dos arquivos e pastas que você deseja baixar e selecione **baixar selecionados**.

1. O notebook prepara um único arquivo *. zip* contendo os arquivos selecionados, que você salva como faria normalmente no navegador. O notebook cria um arquivo *. zip* mesmo quando você baixa um único arquivo.

## <a name="next-steps"></a>Passos seguintes

- [Acessar dados de nuvem em um bloco de anotações](access-data-resources-jupyter-notebooks.md)
