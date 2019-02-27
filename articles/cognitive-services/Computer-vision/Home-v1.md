---
title: O que é a API de Imagem Digitalizada?
titlesuffix: Azure Cognitive Services
description: A API de Imagem Digitalizada fornece aos programadores acesso a algoritmos avançados para processamento de imagens e devolução de informações.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 08/10/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 812f957ffe2a3f24ead4ef0fe66a25c3cfd17995
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866917"
---
# <a name="what-is-computer-vision-api-version-10"></a>O que é a Versão 1.0 da API de Imagem Digitalizada?

> [!IMPORTANT]
> Está agora disponível uma nova versão da API de Imagem Digitalizada. Veja os seguintes documentos:
>- [Descrição geral](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Versão 2.0 da API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

A API de Imagem Digitalizada baseada na cloud fornece aos programadores acesso a algoritmos avançados para processamento de imagens e devolução de informações. Ao carregar uma imagem ou especificar um URL de imagem, os algoritmos da Imagem Digitalizada da Microsoft podem analisar o conteúdo visual de formas diferentes com base nas entradas e opções do utilizador. Com a API de Imagem Digitalizada, os utilizadores podem analisar imagens para:
* Imagens de etiqueta com base no conteúdo.
* Categorize imagens.
* Identifica o tipo e a qualidade das imagens.
* [Detetar rostos humanos e retornar seus coordenadas.](#Faces)
* Reconhece conteúdo específico do domínio.
* Gere descrições do conteúdo.
* Utilize o reconhecimento ótico de carateres para identificar o texto impresso encontrado nas imagens.
* Reconhece texto manuscrito.
* Distingui os esquemas de cores.
* Sinalizar conteúdo para adultos.
* Fotografias de recorte para ser utilizado como miniaturas.

## <a name="requirements"></a>Requisitos
* Suporte para métodos de entrada: Imagens RAW binárias na forma de um application/octet-stream ou URL da imagem.
* Suporte para formatos de imagem: JPEG, PNG, GIF, BMP.
* Tamanho do ficheiro de imagem: Menos de 4 MB.
* Dimensão da imagem: Maior que 50 x 50 pixéis.

## <a name="tagging-images"></a>Identificar Imagens
API de imagem digitalizada devolve etiquetas com base em milhares de objetos reconhecíveis, seres vivos, paisagens e ações. Quando as etiquetas são ambíguas ou não são do conhecimento geral, a resposta da API fornece "sugestões" para clarificar o significado da etiqueta no contexto de um cenário conhecido. As etiquetas não são organizadas como uma taxonomia e não existem hierarquias de herança. Uma coleção de etiquetas de conteúdos é a base da "descrição" de uma imagem apresentada como um idioma legível por humanos e formatada em frases completas. Tenha em atenção que, neste momento, o inglês é o único idioma suportado para a descrição de imagens.

Depois de carregar uma imagem ou especificar o URL de uma imagem, os algoritmos da API de Imagem Digitalizada geram etiquetas com base nos objetos, seres vivos e ações identificados na imagem. As etiquetas não se limitam ao tema principal, como por exemplo uma pessoa em primeiro plano, mas também incluem o cenário (interior ou exterior), o mobiliário, ferramentas, plantas, animais, acessórios, gadgets, etc.

### <a name="example"></a>Exemplo
![House_Yard](./Images/house_yard.png) '

```json
Returned Json
{
   'tags':[
      {
         "name":"grass",
         "confidence":0.999999761581421
      },
      {
         "name":"outdoor",
         "confidence":0.999970674514771
      },
      {
         "name":"sky",
         "confidence":999289751052856
      },
      {
         "name":"building",
         "confidence":0.996463239192963
      },
      {
         "name":"house",
         "confidence":0.992798030376434
      },
      {
         "name":"lawn",
         "confidence":0.822680294513702
      },
      {
         "name":"green",
         "confidence":0.641222536563873
      },
      {
         "name":"residential",
         "confidence":0.314032256603241
      },
   ],
}
```
## <a name="categorizing-images"></a>Categorizar Imagens
Além das etiquetas e descrições, a API de Imagem Digitalizada devolve as categorias baseadas na taxonomia definidas nas versões anteriores. Estas categorias são organizadas como uma taxonomia com hierarquias hereditárias principais/subordinadas. Todas as categorias estão em inglês. Podem ser utilizadas isoladamente ou com os nossos novos modelos.

### <a name="the-86-category-concept"></a>O conceito de 86 categorias
Com base numa lista de 86 conceitos apresentados no seguinte diagrama, as funcionalidades visuais encontradas numa imagem podem ser categorizadas de forma mais geral ou mais específica. Para obter a taxonomia completa no formato de texto, veja [Taxonomia de 86 Categorias](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Analisar as Categorias](./Images/analyze_categories.png)

Imagem                                                  | Resposta
------------------------------------------------------ | ----------------
![Mulher num Terraço](./Images/woman_roof.png)                 | people
![Fotografia de Família](./Images/family_photo.png)             | people_crowd
![Cão fofinho](./Images/cute_dog.png)                     | animal_dog
![Exterior/Montanha](./Images/mountain_vista.png)       | outdoor_mountain
![Análise de imagem de pão](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>Identificar Tipos de Imagem
Existem várias formas de categorizar imagens. A API de Imagem Digitalizada pode definir um sinalizador booleano para indicar se uma imagem é a preto e branco ou a cores. Também pode definir um sinalizador para indicar se uma imagem é ou não um desenho. Também pode indicar se uma imagem é ou não ClipArt e indicar a respetiva qualidade enquanto tal, numa escala de 0 a 3.

### <a name="clip-art-type"></a>Tipo de ClipArt
Deteta se uma imagem é ou não ClipArt.  

Value | Significado
----- | --------------
0     | Não é ClipArt
1     | Ambígua
2     | ClipArt normal
3     | ClipArt de qualidade

Imagem|Resposta
----|----
![Análise de imagem ClipArt de queijo](./Images/cheese_clipart.png)|3 ClipArt de qualidade
![Análise de imagem de casa com quintal](./Images/house_yard.png)|0 Não é ClipArt

### <a name="line-drawing-type"></a>Tipo de desenho de linha
Deteta se uma imagem é ou não um desenho de linha.

Imagem|Resposta
----|----
![Análise de Imagem de Leão Desenhado](./Images/lion_drawing.png)|Verdadeiro
![Análise de Imagem de Flor](./Images/flower.png)|Falso

### <a name="faces"></a>Rostos
Deteta rostos humanos numa imagem e gera as coordenadas faciais, o retângulo do rosto, o género e a idade. Estas funcionalidades visuais são um subconjunto dos metadados gerados para rostos. Para obter metadados mais extensos gerados para rostos (identificação facial, deteção de posição e mais), utilize a API Face.  

Imagem|Resposta
----|----
![Análise de imagem de rosto de mulher num terraço](./Images/woman_roof_face.png) | [ { "age": 23, "gender": "Female", "faceRectangle": { "left": 1379, "top": 320, "width": 310, "height": 310 } } ]
![Análise de Imagem de Rostos de Mãe e Filha](./Images/mom_daughter_face.png) | [ { "age": 28, "gender": "Female", "faceRectangle": { "left": 447, "top": 195, "width": 162, "height": 162 } }, { "age": 10, "gender": "Male", "faceRectangle": { "left": 355, "top": 87, "width": 143, "height": 143 } } ]
![Análise de Imagem de Rostos em Fotografia de Família](./Images/family_photo_face.png) | [ { "age": 11, "gender": "Male", "faceRectangle": { "left": 113, "top": 314, "width": 222, "height": 222 } }, { "age": 11, "gender": "Female", "faceRectangle": { "left": 1200, "top": 632, "width": 215, "height": 215 } }, { "age": 41, "gender": "Male", "faceRectangle": { "left": 514, "top": 223, "width": 205, "height": 205 } }, { "age": 37, "gender": "Female", "faceRectangle": { "left": 1008, "top": 277, "width": 201, "height": 201 } } ]


## <a name="domain-specific-content"></a>Conteúdos Específicos de Um Domínio

Além das etiquetas e da categorização de nível superior, a API de Imagem Digitalizada também suporta informações especializadas (ou específicas de domínio). As informações especializadas podem ser implementadas como um método autónomo ou com a categorização geral. Funcionam como um meio para aperfeiçoar a taxonomia de 86 categorias ao adicionar modelos específicos de um domínio.

Atualmente, as únicas informações especializadas suportadas são o reconhecimento de celebridades e o reconhecimento de marcos. Trata-se de aperfeiçoamentos específicos de um domínio para as categorias de pessoas e de grupos de pessoas, e para marcos em todo o mundo.

Existem duas opções para utilizar os modelos específicos de um domínio:

### <a name="option-one---scoped-analysis"></a>Opção Um – Análise com Âmbito
Analise apenas um modelo escolhido ao invocar uma chamada HTTP POST. Para esta opção, se souber que modelo quer utilizar, irá especificar o nome do modelo e obter apenas informações relevantes para esse modelo. Por exemplo, pode utilizar esta opção apenas para o reconhecimento de celebridades. A resposta contém uma lista de potenciais correspondências de celebridades, juntamente com as respetivas pontuações de confiança.

### <a name="option-two---enhanced-analysis"></a>Opção Dois – Análise Avançada
Analise para obter detalhes adicionais relacionados com as categorias da taxonomia de 86 categorias. Esta opção está disponível para utilização em aplicações nas quais os utilizadores querem obter uma análise de imagem genérica, além dos detalhes de um ou mais modelos específicos de um domínio. Ao invocar este método, é chamado primeiro o classificador da taxonomia de 86 categorias. Se alguma das categorias corresponder à dos modelos de correspondência/conhecidos, seguir-se-á uma segunda transmissão das invocações do classificador. Por exemplo, se "details=all" ou "details" incluir "celebrities", o método chama o classificador de celebridades depois de o classificador da taxonomia de 86 categorias ser chamado. O resultado inclui etiquetas que comecem com "people_".

## <a name="generating-descriptions"></a>Gerar Descrições 
Os algoritmos da API de Imagem Digitalizada analisam os conteúdos numa imagem. Esta análise é a base de uma "descrição" apresentada como um idioma legível por humanos em frases completas. A descrição resume o que é encontrado na imagem. Os algoritmos da API de Imagem Digitalizada geram várias descrições com base nos objetos identificados na imagem. Cada uma das descrições é avaliada e é gerada uma pontuação de confiança. Em seguida, é devolvida uma lista ordenada da pontuação de confiança mais alta para a mais baixa. Pode encontrar um exemplo de um bot que utiliza esta tecnologia para gerar legendas de imagem [aqui](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Geração de Descrição de Exemplo
![Edifícios a preto e branco](./Images/bw_buildings.png) '
```json
 Returned Json

'description':{
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a large city',
         'confidence':0.607638706850331
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a photo of a large city',
         'confidence':0.577256764264197
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a city',
         'confidence':0.538493271791207
      }
   ]   
   'description':[
      "tags":{
         "outdoor",
         "city",
         "building",
         "photo",
         "large",
      }
   ]
}
```

## <a name="perceiving-color-schemes"></a>Detetar Esquemas de Cores
O algoritmo de Imagem Digitalizada extrai as cores de uma imagem. As cores são analisadas em três contextos diferentes: primeiro plano, segundo plano e tudo. São agrupadas em 12 cores de destaque dominantes. Estas cores de destaque são o preto, azul, castanho, cinzento, verde, laranja, rosa, roxo, vermelho, verde-cinza, branco e amarelo. Consoante as cores numa imagem, poderão ser devolvidos o preto e branco simples ou cores de destaque em códigos de cor hexadecimais.

Imagem                                                       | Primeiro Plano |Segundo plano| Cores
----------------------------------------------------------- | --------- | ------- | ------
![Exterior/Montanha](./Images/mountain_vista.png)            | Preto     | Preto   | Branco
![Análise de Imagem de Flor](./Images/flower.png)               | Preto     | Branco   | Branco, Preto, Verde
![Análise de Imagem de Estação de Comboios](./Images/train_station.png) | Preto     | Preto   | Preto

### <a name="accent-color"></a>Cor de destaque
Cor extraída de uma imagem projetada para representar a cor mais atrativa para os utilizadores, através de uma combinação de cores dominantes e de saturação.

Imagem                                                       | Resposta
----------------------------------------------------------- | ----
![Exterior/Montanha](./Images/mountain_vista.png)            | #BC6F0F
![Análise de Imagem de Flor](./Images/flower.png)               | #CAA501
![Análise de Imagem de Estação de Comboios](./Images/train_station.png) | #484B83


### <a name="black--white"></a>Preto e branco
Sinalizador booleano que indica se uma imagem é ou não a preto e branco.

Imagem                                                      | Resposta
---------------------------------------------------------- | ----
![Análise de Imagem de Edifício](./Images/bw_buildings.png)      | Verdadeiro
![Análise de Imagem de Casa com Quintal](./Images/house_yard.png)      | Falso

## <a name="flagging-adult-content"></a>Sinalizar Conteúdo para Adultos
Entre as várias categorias visuais encontra-se o grupo para adultos e de profanidades, que permite a deteção de materiais para adultos e restringe a exibição de imagens com conteúdos sexuais. O filtro para a deteção de conteúdos para adultos ou profano pode ser definido numa escala móvel para se adaptar às preferências do utilizador.

## <a name="optical-character-recognition-ocr"></a>Reconhecimento Ótico de Carateres (OCR)
A tecnologia OCR deteta os conteúdos de texto numa imagem e extrai o texto identificado para um fluxo de carateres legível por computador. Pode utilizar o resultado para pesquisa e inúmeras outras finalidades, como registos médicos, segurança e atividades bancárias. O OCR deteta automaticamente o idioma. O OCR poupa tempo e é mais cómodo para os utilizadores ao permitir tirar fotografias do texto em vez de o transcrever.

O OCR suporta 25 idiomas. Essas linguagens são: Árabe, chinês simplificado, chinês tradicional, checo, dinamarquês, Holandês, inglês, finlandês, francês, alemão, grego, húngaro, italiano, japonês, coreano, norueguês, polaco, português, romeno, russo, Sérvio (Cirílico e Latim), eslovaco, espanhol, Sueco e turco.

Se for necessário, o OCR corrige a rotação do texto reconhecido, em graus, à volta do eixo de imagem horizontal. O OCR fornece as coordenadas de fotograma de cada palavra, conforme apresentado na ilustração abaixo.

![Descrição Geral do OCR](./Images/vision-overview-ocr.png) Requisitos do OCR:
- O tamanho da imagem de entrada tem de se encontrar entre 40x40 e 3200x3200 pixéis.
- A imagem não pode ser maior do que 10 megapixéis.

A imagem de entrada pode ser rodada segundo qualquer múltiplo de 90 graus e num ângulo pequeno de até 40 graus.

A precisão de reconhecimento do texto depende da qualidade da imagem. As seguintes situações podem causar uma leitura incorreta:
- Imagens desfocadas.
- Texto manuscrito ou cursivo.
- Estilos de tipos de letra artísticos.
- Tamanho do texto pequeno.
- Planos de fundo complexos, sombras ou distorção de perspetiva ou por reflexos sobre o texto.
- Letras maiúsculas demasiado grandes ou em falta no início das palavras.
- Texto inferior à linha, superior à linha ou rasurado.

Limitações: Em que o texto é dominante de fotografias, os falsos positivos podem ser provenientes de palavras parcialmente reconhecidas. Em algumas fotografias, especialmente sem texto, a precisão pode variar bastante em função do tipo de imagem.

## <a name="recognize-handwritten-text"></a>Reconhecer Texto Manuscrito
Esta tecnologia permite detetar e extrair texto manuscrito de notas, cartas, ensaios, quadros, formulários, etc. Funciona com diferentes superfícies e fundos, como documentos técnicos, notas de post-it e quadros.

O reconhecimento de texto manuscrito poupa tempo e esforço, e pode torná-lo mais produtivo ao permitir obter imagens de texto, em vez de ter de transcrever o texto. Possibilita a digitalização de notas. Esta digitalização permite-lhe implementar uma pesquisa rápida e fácil. Também reduz a aglomeração de papel.

Requisitos de entrada:
- Suporte para formatos de imagem: JPEG, PNG e BMP.
- O tamanho do ficheiro de imagem tem de ser inferior a 4 MB.
- As imagens têm de ter um mínimo de 40x40 pixéis e um máximo de 3200x3200 pixéis de dimensão.

Nota: esta tecnologia está atualmente em pré-visualização e só está disponível para texto em inglês.

## <a name="generating-thumbnails"></a>Gerar Miniaturas
Uma miniatura é uma representação pequena de uma imagem de tamanho normal. Vários dispositivos, como telemóveis, tablets e PCs, criam a necessidade de esquemas de experiência de utilizador (UX) e tamanhos de miniaturas diferentes. Ao utilizar o recorte inteligente, esta funcionalidade da API de Imagem Digitalizada ajuda a resolver o problema.

Depois de carregar uma imagem, é gerada uma miniatura de alta qualidade e o algoritmo da API de Imagem Digitalizada analisa os objetos na imagem. Ele, em seguida, recorta a imagem de acordo com os requisitos da área de interesse. O resultado é apresentado numa estrutura especial, conforme a ilustração abaixo. A miniatura gerada pode ser apresentada com uma proporção diferente da proporção da imagem original, para se adaptar às necessidades do utilizador.

O algoritmo de miniatura funciona da seguinte forma:

1. Remove a distração de elementos da imagem e reconhece o objeto principal, a área de interesse.
2. Recorta a imagem com base na área de identificados de interesse.
3. Altera a proporção de acordo com as dimensões da miniatura de destino.

![Miniaturas](./Images/thumbnail-demo.png)
