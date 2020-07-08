---
title: Reconhecimento de Caracteres Óticos (OCR) - Visão computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com o reconhecimento de caracteres óticos (OCR) a partir de imagens e documentos com texto impresso e manuscrito utilizando a API de Visão Computacional.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: d1c642a660b24cfc54c9c4308b8956582e13d50a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954745"
---
# <a name="optical-character-recognition-ocr"></a>Reconhecimento Ótico de Carateres (OCR)

A Visão Computacional inclui novas capacidades de reconhecimento de caracteres óticos baseados em aprendizagem profunda (OCR) que extraem texto impresso ou manuscrito a partir de imagens e documentos PDF. A Visão Computacional extrai texto de documentos analógicos (imagens, documentos digitalizados) e documentos digitalizados. Pode extrair texto de imagens na natureza, como fotos de placas ou contentores com números de série, bem como de documentos - faturas, contas, relatórios financeiros, artigos e muito mais. Esta funcionalidade OCR está disponível como parte do serviço gerido na nuvem ou nas instalações (contentores). Além disso, suporta redes virtuais e pontos finais privados para atender às necessidades de conformidade e privacidade da sua empresa.

## <a name="read-api"></a>Ler API 

[A API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) de Leitura da Visão de Computador é a mais recente tecnologia OCR da Microsoft que extrai texto impresso (sete idiomas), texto manuscrito (apenas inglês), dígitos e símbolos de moeda a partir de imagens e documentos PDF de várias páginas. É otimizado para extrair texto de imagens pesadas de texto silvestre e documentos PDF de várias páginas com línguas mistas. Suporta a deteção de texto impresso e manuscrito (apenas em inglês) na mesma imagem ou documento. A lista completa de idiomas suportados pode ser encontrada na página [de suporte idioma para visão](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) de computador.

### <a name="how-it-works"></a>Como funciona

A [API de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) suporta documentos pesados de texto até 2000 páginas e, portanto, executa assíncrono. O primeiro passo é chamar a operação Ler. A operação Ler requer uma imagem ou documento PDF à medida que a entrada e devolve um ID de operação. 

O segundo passo é convocar a operação [Get Results.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) Esta operação requer a operação ID que foi criada pela operação Ler. Em seguida, devolve o conteúdo de texto extraído da sua imagem ou documento sob a forma de JSON. A resposta JSON mantém os agrupamentos de linha originais de palavras reconhecidas. Inclui as linhas de texto extraídas e as suas coordenadas de caixa de delimitação. Cada linha de texto inclui todas as palavras extraídas com as suas coordenadas e uma pontuação de confiança.

Se necessário, leia a rotação da página reconhecida devolvendo o offset rotativo em graus sobre o eixo de imagem horizontal, como se pode ver na seguinte ilustração.

![Uma imagem sendo girada e seu texto sendo lido e delineado](./Images/vision-overview-ocr-read.png)

Siga o [extrato impresso e manuscrito de texto](./QuickStarts/CSharp-hand-text.md) rápido para implementar OCR usando C# e a API REST.

### <a name="input-requirements"></a>Requisitos de entrada

A API de Leitura tem as seguintes entradas:
* Formatos de ficheiros suportados: JPEG, PNG, BMP, PDF e TIFF
* Para PDF e TIFF, até 2000 páginas são processadas. Para assinantes de nível gratuito, apenas as duas primeiras páginas são processadas.
* O tamanho do ficheiro deve ser inferior a 50 MB e dimensões de pelo menos 50 x 50 pixels e, no máximo, 10000 x 10000 pixels.
* As dimensões PDF devem ser no máximo 17 x 17 polegadas, correspondentes a tamanhos legais ou de papel A3 e menores.


### <a name="text-from-images"></a>Texto a partir de imagens

A seguinte saída da API de leitura mostra as linhas de texto e palavras extraídas de uma imagem com texto em diferentes ângulos, cores e fontes

![Uma imagem sendo girada e seu texto sendo lido e delineado](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Texto a partir de documentos

Além das imagens, a API de Leitura toma como entrada um documento PDF.

![Uma imagem sendo girada e seu texto sendo lido e delineado](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>Texto manuscrito em inglês

Neste momento, a operação Read suporta a extração de texto manuscrito exclusivamente em inglês.

![Uma imagem sendo girada e seu texto sendo lido e delineado](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>Texto impresso em línguas apoiadas

A API de leitura suporta a extração de texto impresso em línguas inglesa, espanhola, alemã, francesa, italiana, portuguesa e neerlandesa. Se o seu cenário necessitar de apoiar mais línguas, consulte a visão geral da API do OCR neste documento. Consulte a lista de todos os [idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition)

![Uma imagem sendo girada e seu texto sendo lido e delineado](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Suporte de línguas mistas

A API de Leitura suporta imagens e documentos com múltiplas línguas neles, vulgarmente conhecidas como documentos de linguagem mista. Fá-lo classificando cada linha de texto do documento para a língua detetada antes de extrair o conteúdo do texto.

![Uma imagem sendo girada e seu texto sendo lido e delineado](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os serviços cognitivos, os desenvolvedores que usam o serviço Read devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Consulte a página de Serviços Cognitivos no [Microsoft Trust Center](https://www.microsoft.com/en-us/trust-center/product-overview) para saber mais.

### <a name="deploy-on-premises"></a>Implementar no local

A leitura também está disponível como um recipiente Docker (pré-visualização) para permitir a implementação das novas capacidades de OCR no seu próprio ambiente. Os contentores são ótimos para requisitos específicos de segurança e governação de dados. Veja [como instalar e executar Recipientes de leitura.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>OCR API

A [API OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) utiliza um modelo de reconhecimento mais antigo, suporta apenas imagens e executa de forma sincronizada, regressando imediatamente com o texto detetado. Suporta [mais línguas](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) do que ler API.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre a [API De Leitura 3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)REST .
- Siga o [quickstart de texto extrato](./QuickStarts/CSharp-hand-text.md) para implementar OCR usando C#, Java, JavaScript ou Python juntamente com a REST API.
