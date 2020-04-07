---
title: TexConv - Ferramenta de conversão de textura
description: Manual do utilizador da ferramenta de linha de comando TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680027"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - Ferramenta de conversão de textura

TexConv é uma ferramenta de linha de comando para processar texturas de formatos de entrada típicos como PNG, TGA, JPEG e DDS em formatos otimizados para consumo de tempo de execução.
Embora o cenário mais comum seja `A.xxx` converter um único `B.yyy`ficheiro de entrada num formato otimizado, a ferramenta tem muitas opções adicionais para utilizações avançadas.

## <a name="command-line-help"></a>Ajuda da linha de comando

Executar TexConv.exe `--help` com o parâmetro listará todas as opções disponíveis. Além disso, a TexConv imprime as opções usadas quando é executada, para ajudar a entender o que está a fazer. Consulte esta saída para mais detalhes.

## <a name="general-usage"></a>Utilização geral

TexConv produz sempre **exatamente um** ficheiro de saída. Pode utilizar **vários** ficheiros de entrada para montar a saída a partir de. Para a montagem, também precisa de um **cartografia**de canal , que lhe diz qual canal (*Vermelho, Verde, Azul* ou *Alfa*) para tirar a partir do ficheiro de entrada e movê-lo para o canal da imagem de saída.

A linha de comando mais direta é esta:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`especifica o ficheiro de saída e o formato
- `-in0`especifica a primeira imagem de entrada
- `-rgba`diz-lhe que a imagem de saída deve usar todos os quatro canais e que devem ser retirados 1:1 da imagem de entrada

## <a name="multiple-input-files"></a>Vários ficheiros de entrada

Para montar a saída a partir de vários `-in` ficheiros de entrada, especifique cada ficheiro de entrada utilizando a opção com um número crescente:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Ao montar um mapa cubo a partir de `-right` `-left`texturas 2D, também se pode usar, `-top` `-bottom` `-front` `-back` `-px`ou , `-nx` `-py`ou , , `-ny`, . . `-pz`. . `-nz`

Para mapear estas inputs para o ficheiro de saída, é necessário um mapeamento adequado do canal.

## <a name="channel-mappings"></a>Mapeamento de canais

As opções de mapeamento do canal especificam a partir da qual a entrada para preencher os canais de saída dada. Pode especificar a entrada de cada canal individualmente desta forma:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Aqui os canais RGB da saída seriam preenchidos usando a primeira imagem de entrada, mas o vermelho e o azul serão trocados. O canal alfa da saída seria preenchido com os valores do canal vermelho da segunda imagem de entrada.

Especificar o mapeamento de cada canal separadamente dá a maior flexibilidade. Por conveniência, o mesmo pode ser escrito utilizando operadores "swizzling":

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Canais de saída

Estão disponíveis as seguintes opções de mapeamento de canais:

- `-r`, `-g` `-b`, `-a` - Estes especificam atribuições de canais únicos
- `-rg`: Especificar as atribuições do canal vermelho e verde.
- `-rgb`: Especificar as atribuições do canal vermelho, verde e azul.
- `-rgba`: Especifica as quatro atribuições de canais.

Mencionando apenas o canal R, RG ou RGB, instrui o TexConv a criar um ficheiro de saída com apenas 1, 2 ou 3 canais, respectivamente.

### <a name="input-swizzling"></a>Entrada swizzling

Ao indicar qual a textura de entrada que deve preencher qual canal de saída, pode-se deslizar a entrada:

- `-rgba in0`é equivalente a`-rgba in0.rgba`
- `-rgba in0.bgra`vai swizzle os canais de entrada
- `-rgb in0.rrr`vai duplicar o canal vermelho em todos os canais

Pode também encher canais com preto ou branco:

- `-rgb in0 -a white`vai criar uma textura de saída de 4 canais, mas definir alfa para totalmente opaco
- `-rg black -b white`vai criar uma textura inteiramente azul

## <a name="common-options"></a>Opções comuns

As opções mais interessantes estão listadas abaixo. Mais opções `TexConv --help`são listadas por .

### <a name="output-type"></a>Tipo de saída

- `-type 2D`: A saída será uma imagem 2D regular.
- `-type Cubemap`: A saída será uma imagem de cubemap. Apenas suportado para ficheiros de saída DDS. Quando isto for especificado, pode-se montar o mapa do cubo a partir de 6 imagens de entrada 2D regulares.

### <a name="image-compression"></a>Compressão de imagem

- `-compression none`: A imagem de saída não será comprimida.
- `-compression medium`: Se suportado, a imagem de saída utilizará compressão sem sacrificar demasiada qualidade.
- `-compression high`: Se suportado, a imagem de saída utilizará a compressão e sacrificará a qualidade em favor de um ficheiro menor.

### <a name="mipmaps"></a>Ipmaps

Por padrão, o TexConv gera mipmaps quando o formato de saída o suporta.

- `-mipmaps none`: Não serão gerados mipmaps.
- `-mipmaps Linear`: Se suportado, os ipmaps serão gerados através de um filtro de caixa.

### <a name="usage-srgb--gamma-correction"></a>Utilização (correção sRGB/ gama)

A `-usage` opção especifica o objetivo da saída e, assim, diz à TexConv se aplica a correção gama aos ficheiros de entrada e saída. A utilização afeta apenas os canais RGB. O canal alfa é sempre considerado como contendo valores 'lineares'. Se a utilização não for especificada, o modo 'auto' tentará detetar a utilização a partir do formato e nome de ficheiro da primeira imagem de entrada. Por exemplo, os formatos de saída de um canais simples e duplos são sempre lineares. Verifique a saída para ver que decisão a TexConv tomou.

- `-usage Linear`: A imagem de saída contém valores que não representam cores. Este é tipicamente o caso de texturas metálicas e ásperas, bem como de todos os tipos de máscaras.

- `-usage Color`: A imagem de saída representa a cor, tais como mapas difusos/albedo. A bandeira sRGB será colocada no cabeçalho DDS de saída.

- `-usage HDR`: O ficheiro de saída deve utilizar mais de 8 bits por pixel para codificação. Consequentemente, todos os valores são armazenados no espaço linear. Para texturas HDR não importa se os dados representam cor ou outros dados.

- `-usage NormalMap`: A imagem de saída representa um mapa normal de tangente-espaço. Os valores serão normalizados e a computação do MIPMAP será ligeiramente otimizada.

- `-usage NormalMap_Inverted`: A saída é um mapa normal de tangente-espaço com Y apontando na direção oposta do que a entrada.

### <a name="image-rescaling"></a>Rescaling de imagem

- `-minRes 64`: Especifica a resolução mínima da saída. Se a imagem de entrada for menor, será dimensionada.
- `-maxRes 1024`: Especifica a resolução máxima da saída. Se a imagem de entrada for maior, será reduzida.
- `-downscale 1`: Se for maior do que 0, as imagens de entrada serão reduzidas para metade na resolução N vezes. Use isto para aplicar uma redução global de qualidade.

## <a name="examples"></a>Exemplos

### <a name="convert-a-color-texture"></a>Converter uma textura de cor

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Converter um mapa normal

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Crie um mapa cubo HDR

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

Uma grande fonte para os cubemaps HDR é [hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Asse várias imagens em uma

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Extrair um único canal

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
