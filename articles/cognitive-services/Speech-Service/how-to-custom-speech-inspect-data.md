---
title: Inspecione a qualidade dos dados para discurso personalizado - serviço de fala
titleSuffix: Azure Cognitive Services
description: A Custom Speech fornece ferramentas que permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando os dados áudio com o resultado correspondente do reconhecimento. Pode reproduzir o áudio carregado e determinar se o resultado do reconhecimento fornecido está correto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: erhopf
ms.openlocfilehash: 87bc411903604a7068f346604853a2a61c9a6f1c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499191"
---
# <a name="inspect-custom-speech-data"></a>Inspecionar dados da Voz Personalizada

> [!NOTE]
> Esta página assume que leu [Preparar os dados](./how-to-custom-speech-test-and-train.md) do teste para a Fala Personalizada e ter enviado um conjunto de dados para inspeção.

A Custom Speech fornece ferramentas que permitem inspecionar visualmente a qualidade de reconhecimento de um modelo, comparando os dados áudio com o resultado correspondente do reconhecimento. A partir do [portal Discurso Personalizado,](https://speech.microsoft.com/customspeech)pode reproduzir áudio carregado e determinar se o resultado do reconhecimento fornecido está correto. Esta ferramenta ajuda-o a inspecionar a qualidade do modelo de linha de base da Microsoft, a inspecionar um modelo personalizado treinado ou a comparar a transcrição por dois modelos.

Neste documento, aprende-se a inspecionar visualmente a qualidade do modelo de fala-texto da Microsoft e/ou modelos personalizados que treinou. Também aprende a usar o editor de transcrição online para criar e refinar conjuntos de dados de áudio rotulados.

## <a name="create-a-test"></a>Criar um teste

Siga estas instruções para criar um teste:

1. Inscreva-se no [portal de Discurso Personalizado](https://speech.microsoft.com/customspeech).
2. Navegue **para discurso a texto > > de discurso personalizado [nome do projeto] > Testes**.
3. Clique **em Adicionar Teste**.
4. **Selecione Inspecionar a qualidade (dados áudio-only)**. Dê ao teste um nome, descrição e selecione o seu conjunto de dados áudio.
5. Selecione até dois modelos que gostaria de testar.
6. Clique em **Criar**.

Depois de um teste ter sido criado com sucesso, pode ver como um modelo transcreve o conjunto de dados áudio especificado ou compara os resultados de dois modelos lado a lado.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Comparações de modelos lado a lado

Quando o estado do teste _for bem sucedido,_ clique no nome do item de teste para ver os detalhes do teste. Esta página de detalhe lista todas as expressões no seu conjunto de dados, e mostra os resultados de reconhecimento dos dois modelos que está a comparar.

Para ajudar a inspecionar a comparação lado a lado, pode alternar vários tipos de erros, incluindo inserção, eliminação e substituição. Ao ouvir o áudio e comparar os resultados de reconhecimento em cada coluna (mostrando transcrição com rótulo humano e os resultados de dois modelos de discurso a texto), pode decidir qual o modelo que satisfaz as suas necessidades e onde são necessárias melhorias.

Os testes de modelo lado a lado são úteis para validar qual o modelo de reconhecimento de voz melhor para uma aplicação. Para uma medida objetiva de precisão, requerendo áudio transcrito, siga as instruções encontradas na [Avaliação de Precisão](how-to-custom-speech-evaluate-data.md).

## <a name="online-transcription-editor"></a>Editor de transcrição online

O editor de transcrição online permite-lhe trabalhar facilmente com transcrições de áudio em Discurso Personalizado. Os principais casos de utilização do editor são os seguintes: 

* Tem apenas dados áudio, mas pretende construir conjuntos de dados de áudio precisos + com rótulo humano do zero para ser usado no treino de modelos.
* Já tem conjuntos de dados com rótulo sonoro + humanos, mas existem erros ou defeitos na transcrição. O editor permite-lhe modificar rapidamente as transcrições para obter a melhor precisão de treino.

O único requisito para usar o editor de transcrição é ter dados áudio carregados (apenas áudio, ou áudio + transcrição).

### <a name="import-datasets-to-editor"></a>Conjuntos de dados de importação para editor

Para importar dados para o Editor, navegue primeiro para o **Custom Speech > [O seu projeto] > Editor**.

![Separador Editor](media/custom-speech/custom-speech-editor-detail.png)

Em seguida, utilize os seguintes passos para importar dados.

1. Clique **em dados de importação**
1. Criar um novo conjunto de dados e dar-lhe uma descrição
1. Selecione conjuntos de dados. Várias seleções são suportadas, e você pode selecionar apenas dados de áudio, bem como dados áudio + com rótulo humano.
1. Para dados áudio-only, pode utilizar opcionalmente os modelos predefinidos para gerar automaticamente transcrição de máquina após importar para o Editor
1. Clique em **Importar**

Depois de os dados terem sido importados com sucesso, pode clicar nos conjuntos de dados e começar a editar.

> [!TIP]
> Também pode importar conjuntos de dados para o Editor diretamente selecionando conjuntos de dados e clicando em **Exportação para Editor**

### <a name="edit-transcription-by-listening-to-audio"></a>Editar transcrição ouvindo áudio

Depois de o upload de dados ter sido bem sucedido, clique em cada nome de item para ver detalhes dos dados. Também pode utilizar **Antes** e **Seguinte** para mover entre cada ficheiro.

A página de detalhes lista todos os segmentos em cada ficheiro áudio, e pode clicar na expressão desejada. Para cada expressão, pode reproduzir o áudio e examinar as transcrições e editar as transcrições se encontrar erros de inserção, supressão ou substituição. Consulte a [avaliação de dados como fazer para](how-to-custom-speech-evaluate-data.md) obter mais detalhes sobre os tipos de erros.

![Página do editor](media/custom-speech/custom-speech-editor.png)

Depois de fazer edições, clique no botão **Guardar.**

### <a name="export-datasets-from-the-editor"></a>Conjuntos de dados de exportação do Editor

Para exportar conjuntos de dados de volta para o separador **Dados,** navegue na página de detalhes de dados e clique no botão **Exportar** todos os ficheiros como um novo conjunto de dados. Também pode filtrar os ficheiros pelo último tempo editado, durações de áudio, etc. para selecionar parcialmente os ficheiros pretendidos. 

![Exportar os dados](media/custom-speech/custom-speech-editor-export.png)

Os ficheiros exportados para Dados serão utilizados como um conjunto de dados novinho em folha e não afetarão nenhuma das entidades existentes de dados/formação/teste.

## <a name="next-steps"></a>Passos seguintes

- [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
- [Preparar o seu modelo](how-to-custom-speech-train-model.md)
- [Melhore o seu modelo](./how-to-custom-speech-evaluate-data.md)
- [Implemente o seu modelo](./how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Preparar dados de teste para discurso personalizado](./how-to-custom-speech-test-and-train.md)