---
title: Importar e exportar dados com projetos com blocos de notas do Azure
description: Como colocar dados num projeto de blocos de notas do Azure a partir de origens externas e como exportar dados de um projeto.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 586b423b-6668-4bdd-9592-4c237d7458fb
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: fd1e7ccdc54b5c5111ecfa466e916a376f43521a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770722"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Trabalhar com arquivos de dados em projetos de bloco de notas do Azure

Os dados são a alma de muitas Jupyter notebooks, especialmente blocos de notas utilizados para ciência de dados. Com blocos de notas do Azure, pode facilmente importar a partir de uma variedade de origens num projeto e, em seguida, utilizar esses dados de blocos de notas. Também pode ter blocos de notas gerar dados armazenados no projeto, que, em seguida, pode ser baixado para uso em outro lugar.

O **dados** menu dentro de um bloco de notas em execução também fornece **carregar** e **transferir** comandos, que trabalham com ficheiros do projeto, bem como os arquivos temporários na atual sessão de bloco de notas.

Também pode utilizar código dentro de um bloco de notas para aceder a uma variedade de origens de dados diretamente, incluindo ficheiros dentro de um projeto. Também pode acessar dados arbitrários com comandos numa célula de código. Uma vez que esses dados são armazenados em variáveis dentro da sessão de bloco de notas, ele não será guardado no projeto, a menos que usar o código para gerar especificamente os arquivos de projeto.

Trabalhar com o código nos dados é melhor teve dentro de um bloco de notas em execução em si: para essa finalidade, consulte a [introdução aos seus dados no bloco de notas de exemplo de blocos de notas do Azure](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

O restante deste artigo fornece detalhes sobre as operações de arquivo de nível de projeto.

## <a name="import-data"></a>Importar dados

Pode colocar arquivos num projeto do dashboard do projeto, ou dentro de um bloco de notas em execução, através da **dados** menu ou um comando como `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Importar ficheiros a partir do dashboard do projeto

1. No projeto, navegue para a pasta onde pretende importar os ficheiros.

1. Selecione o **carregue** comando, então **de URL** ou **do computador** e os detalhes necessários para os dados que pretende importar do projeto:

    - **Partir do URL**: Introduza o endereço de origem na **URL de ficheiro** campo e o nome de ficheiro para atribuir ao bloco de notas no seu projeto no **nome do ficheiro** campo. Em seguida, selecione **+ adicionar ficheiro** para adicionar o URL para a lista de carregamento. Repita o processo para todos os URLs adicionais, em seguida, selecione **feito**.

    ![Carregar a partir do pop-up de URL](media/quickstarts/upload-from-url-popup.png)

    - **Do computador**: Arrastar e soltar arquivos em pop-up ou selecione **escolher ficheiros**, em seguida, procure e selecione os ficheiros de dados que pretende importar. Pode remover ou escolher qualquer número de ficheiros, de qualquer tipo e o formato, porque é o código no bloco de notas, abra o ficheiro e analisar os dados.

    ![Carregar a partir do pop-up de computador](media/quickstarts/upload-from-computer-popup.png)

1. Depois de importados, os ficheiros são apresentados no dashboard do projeto e podem ser acedidos no código do bloco de notas com pathnames relativo para a pasta que contém.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importar ficheiros a partir do menu ficheiro num bloco de notas

1. Dentro de um bloco de notas em execução, selecione o **arquivo** > **carregar** comando:

    ![Comando de menu de carregamento dentro de um bloco de notas do ficheiro](media/file-menu-upload.png)

1. Na caixa de diálogo que se abre, navegue até e selecione os ficheiros que pretende carregar. Pode selecionar qualquer número de ficheiros de qualquer tipo. Selecione **aberto** quando tiver terminado.

1. Na **estado do carregamento** pop-up que é apresentada, selecione um **pasta de destino** na lista pendente:

    - Pasta de sessão (*~/* ): Carrega ficheiros para a sessão atual do bloco de notas, mas não cria ficheiros no projeto. A pasta de sessão é um elemento de rede para a pasta de projeto, mas não persistem após a sessão termina. Para acessar arquivos de sessão no código, os nomes de arquivo com o caminho relativo do prefixo *.... /*.

        Com a pasta de sessão é útil para a experimentação e evita a sobrecarregar o projeto com arquivos, pode ou não poderá ter de forma longa duração. Também pode carregar ficheiros para a pasta de sessão que têm nomes idênticos para ficheiros do projeto sem causar conflitos e sem ter de mudar o nome de ficheiros. Por exemplo, digamos que tenha uma versão do *CSV* no projeto, mas pretende experimentar com uma versão diferente do *CSV*. Ao carregar o ficheiro para a pasta de sessão, pode executar o bloco de notas com dados no arquivo carregado (faça referência a ele no código usando *... /data.csv*) em vez dos dados no arquivo do projeto.

    - Pasta do projeto (*/Project*): carrega ficheiros para o projeto em que pode ser acessada usando pathnames relativo no código. Carregar um ficheiro para esta pasta é igual a carregar um ficheiro no dashboard do projeto. O ficheiro é guardado com o projeto e está disponível em sessões posteriores.

        A carregar falha se tentar carregar um ficheiro com o mesmo nome que um que já exista no projeto. Para substituir um ficheiro, carregue o novo ficheiro a partir do dashboard do projeto em vez disso, que lhe dá a opção para substituir.

1. Selecione **começar a carregar** para concluir o processo.

### <a name="create-or-import-files-using-commands"></a>Criar ou importar ficheiros através de comandos

Pode utilizar os comandos dentro de um terminal ou dentro de uma célula de código do Python para criar arquivos dentro do projeto e a sessão de pastas. Por exemplo, os comandos, como `curl` e `wget` transfira ficheiros a partir da Internet diretamente.

Para transferir os ficheiros no terminal, selecione o **Terminal** comando no dashboard do projeto, em seguida, introduza os comandos adequados:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Quando utilizar uma célula de código Python num bloco de notas, os comandos com o prefixo `!`.

A pasta de projeto é a pasta predefinida, então, especificando um nome de ficheiro de destino, como *oil_price.csv* cria o arquivo no projeto. Para criar um ficheiro de sessão, prefixo de nome com *.... /* como na *.... /oil_price.csv*.

### <a name="create-files-in-code"></a>Criar ficheiros no código

Ao utilizar o código que cria um arquivo, como os pandas `write_csv` função, pathnames são sempre relativas a pasta do projeto. Usando *.... /* cria um ficheiro de sessão é descartado quando o bloco de notas é interrompido e fechado.

## <a name="export-files"></a>Ficheiros de exportação

Pode exportar dados do dashboard do projeto ou a partir um bloco de notas.

## <a name="export-files-from-the-project-dashboard"></a>Exportar ficheiros a partir do dashboard do projeto

No dashboard do projeto, um ficheiro com o botão direito e selecione **transferir**:

![Baixe o comando no menu de contexto do item de projeto](media/download-command.png)

Também pode selecionar um ficheiro e utilizar o **transferir** comando (atalho de teclado: 1!d) no dashboard:

![Baixe o comando da barra de ferramentas no dashboard do projeto](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Exportar ficheiros no menu de dados num bloco de notas

1. Selecione o **arquivo** > **transferir** comando de menu:

    ![Comando de menu de transferência de dados dentro de um bloco de notas](media/file-menu-download.png)

1. É apresentado um pop-up que mostra as pastas na sessão; o *projeto* pasta contém os ficheiros de projeto:

    ![Popup de comando de transferência de dados em que selecione ficheiros e pastas](media/file-menu-download-popup.png)

1. Selecione as caixas à esquerda dos ficheiros e pastas que pretende transferir, em seguida, selecione **transferir selecionados**.

1. O bloco de notas prepara uma única *. zip* ficheiro que contém os ficheiros selecionados, o que, em seguida, Salvar como normalmente fazer a partir do seu browser. O bloco de notas cria um *. zip* , mesmo quando baixar um único arquivo de ficheiros.

## <a name="next-steps"></a>Passos Seguintes

- [Aceder a dados na cloud num bloco de notas](access-data-resources-jupyter-notebooks.md)
