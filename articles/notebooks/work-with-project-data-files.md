---
title: Dados de importação e exportação com projetos com Pré-visualização de cadernos Azure
description: Saiba como trazer dados para um projeto de pré-visualização de cadernos Azure de fontes externas e como exportar dados de um projeto.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: e1d4a52ab7f4ad2ca3438af4bc87bec0b79f34d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646981"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Trabalhar com ficheiros de dados em projetos de pré-visualização de cadernos Azure

Os dados são o sangue vital de muitos cadernos jupyter, especialmente cadernos usados para a ciência dos dados. Com os Cadernos Azure, pode facilmente importar de uma variedade de fontes para um projeto, e depois usar esses dados a partir de cadernos. Também pode ter cadernos que gerem dados que estão armazenados no projeto, que pode depois descarregar para uso em outros lugares.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

O menu **Dados** dentro de um caderno em execução também fornece comandos **de Upload** e **Download,** que funcionam com ficheiros no projeto, bem como ficheiros temporários para a sessão de caderno seletiva atual.

Também pode usar código dentro de um caderno para aceder diretamente a uma variedade de fontes de dados, incluindo ficheiros dentro de um projeto. Também pode aceder a dados arbitrários utilizando comandos numa célula de código. Como esses dados são armazenados em variáveis dentro da sessão do caderno, não serão guardados no projeto a menos que utilize código para gerar especificamente ficheiros de projeto.

Trabalhar com código em dados é melhor experimentado dentro de um caderno em execução em si: para o efeito, consulte a Obtenção dos seus dados no caderno de [amostras de Cadernos Azure](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

O restante deste artigo fornece detalhes sobre as operações de ficheiros ao nível do projeto.

## <a name="import-data"></a>Importar dados

Pode introduzir ficheiros num projeto a partir do painel de instrumentos do projeto, ou dentro de um caderno em execução utilizando o menu **Dados** ou um comando como `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Importar ficheiros do painel do projeto

1. No projeto, navegue para a pasta onde pretende importar os ficheiros.

1. Selecione o comando **upload,** em **seguida, seja a partir de URL** ou de **computador** e projete os detalhes necessários para os dados que pretende importar:

   - **A partir de URL**: Introduza o endereço de origem no campo URL do **Ficheiro** e o nome de ficheiro para atribuir ao caderno no seu projeto no campo Nome do **Ficheiro.** Em seguida, selecione **+ Adicionar Ficheiro** para adicionar o URL à lista de upload. Repita o processo para quaisquer URLs adicionais e, em seguida, selecione **Done**.

     ![Upload de URL popup](media/quickstarts/upload-from-url-popup.png)

   - **A partir do computador:** Arraste e deixe cair ficheiros no popup, ou selecione **'Escolher Ficheiros'** e, em seguida, navegue e selecione os ficheiros de dados que pretende importar. Pode deixar cair ou escolher qualquer número de ficheiros, de qualquer tipo e formato, porque cabe ao código do caderno abrir o ficheiro e analisar os seus dados.

     ![Upload de popup de computador](media/quickstarts/upload-from-computer-popup.png)

1. Uma vez importados, os ficheiros aparecem no painel do projeto e podem ser acedidos dentro do código do portátil utilizando nomes relativos para a pasta contendo.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importar ficheiros do menu Do ficheiro num caderno

1. Dentro de um bloco de notas em execução, selecione o comando**de upload** de **ficheiros:** > 

    ![Comando de menu de upload de ficheirodentro de um caderno](media/file-menu-upload.png)

1. Na caixa de diálogo que abre, navegue e selecione os ficheiros que pretende carregar. Pode selecionar qualquer número de ficheiros de qualquer tipo. **Selecione Open** quando terminar.

1. No **popup** do estado de upload que aparece, selecione uma pasta de **destino** na lista de drop-down:

    - Pasta de*~/* sessão (): Faça upload de ficheiros na sessão de caderno atual, mas não cria ficheiros no projeto. A pasta da sessão é um par para a pasta do projeto, mas não persiste após o final da sessão. Para aceder aos ficheiros da sessão em código, prefixe os nomes de ficheiros com o caminho relativo *.. /*.

        A utilização da pasta da sessão é útil para a experimentação e evita a confusão do projeto com ficheiros que pode ou não necessitar a longo prazo. Também pode fazer o upload de ficheiros para a pasta da sessão que têm nomes idênticos a ficheiros no projeto sem causar conflitos e sem ter de mudar o nome dos ficheiros. Por exemplo, diga que já tem uma versão de *data.csv* no projeto, mas quer experimentar com uma versão diferente de *data.csv*. Ao enviar o ficheiro para a pasta da sessão, pode executar o caderno utilizando dados no ficheiro carregado (referindo-se ao mesmo em código utilizando *.. /data.csv*) em vez dos dados no ficheiro do projeto.

    - A pasta do projeto *(/projeto):* envia ficheiros para o projeto onde podem ser acedidos usando nomes relativos em código. O upload de um ficheiro para esta pasta é o mesmo que carregar um ficheiro no painel de instrumentos do projeto. O ficheiro é guardado com o projeto e está disponível em sessões posteriores.

        O upload falha se tentar carregar um ficheiro com o mesmo nome que já existe no projeto. Para substituir um ficheiro, faça o upload do novo ficheiro do painel de instrumentos do projeto, o que lhe dá a opção de sobrepor.

1. Selecione **Iniciar o Upload** para completar o processo.

### <a name="create-or-import-files-using-commands"></a>Criar ou importar ficheiros utilizando comandos

Pode utilizar comandos dentro de um terminal ou dentro de uma célula de código Python para criar ficheiros dentro do projeto e das pastas de sessão. Por exemplo, comandos como `curl` e `wget` descarregamento de ficheiros a partir da Internet diretamente.

Para descarregar ficheiros no terminal, selecione o comando **Terminal** no painel de instrumentos do projeto e introduza os comandos adequados:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Quando utilizar uma célula de código Python num `!`caderno, prefixe os comandos com .

A pasta do projeto é a pasta predefinida, especificando assim um nome de ficheiro de destino como *oil_price.csv* cria o ficheiro no projeto. Para criar um ficheiro de sessão, prefixe o nome com *.. /* como em *.. /oil_price.csv.*

### <a name="create-files-in-code"></a>Criar ficheiros em código

Ao utilizar o código que cria um `write_csv` ficheiro, como a função pandas, os nomes de caminhos são sempre relativos à pasta do projeto. *Usando.. /cria* um ficheiro de sessão que é descartado quando o caderno é parado e fechado.

## <a name="export-files"></a>Ficheiros de exportação

Pode exportar dados do painel de instrumentos do projeto ou de dentro de um caderno.

## <a name="export-files-from-the-project-dashboard"></a>Ficheiros de exportação do painel de instrumentos do projeto

No painel de instrumentos do projeto, clique num ficheiro e selecione **Download:**

![Descarregue o comando no menu de contexto do item do projeto](media/download-command.png)

Também pode selecionar um ficheiro e utilizar o comando **Descarregamento** (atalho do teclado: d) no painel de instrumentos:

![Descarregue o comando da barra de ferramentas no painel de instrumentos do projeto](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Exportar ficheiros do menu Dados num caderno

1. Selecione o comando do menu**de descarregamento** de **ficheiros:** > 

    ![Comando de menu de descarregamento de dados dentro de um caderno](media/file-menu-download.png)

1. Aparece um popup que mostra as pastas da sessão; a pasta do *projeto* contém os ficheiros do projeto:

    ![Data Download command popup no qual seleciona ficheiros e pastas](media/file-menu-download-popup.png)

1. Selecione as caixas à esquerda dos ficheiros e pastas que pretende descarregar e, em seguida, selecione **Download Selecionado**.

1. O caderno prepara um único ficheiro *.zip* contendo os ficheiros selecionados, que depois economiza como normalmente faz do seu navegador. O caderno cria um ficheiro *.zip* mesmo quando descarrega um único ficheiro.

## <a name="next-steps"></a>Passos seguintes

- [Aceder a dados da cloud num bloco de notas](access-data-resources-jupyter-notebooks.md)
