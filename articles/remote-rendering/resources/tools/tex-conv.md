---
title: TexConv - Ferramenta de conversão de textura
description: Manual do utilizador para a ferramenta de linha de comando TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80680027"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - Ferramenta de conversão de textura

A TexConv é uma ferramenta de linha de comando para processar texturas de formatos típicos de entrada como PNG, TGA, JPEG e DDS em formatos otimizados para consumo de tempo de execução.
Embora o cenário mais comum seja converter um único ficheiro de entrada `A.xxx` num formato `B.yyy` otimizado, a ferramenta tem muitas opções adicionais para utilizações avançadas.

## <a name="command-line-help"></a>Ajuda na linha de comando

Executar TexConv.exe com o `--help` parâmetro listará todas as opções disponíveis. Além disso, a TexConv imprime as opções usadas quando é executada, para ajudar a entender o que está a fazer. Consulte esta saída para mais detalhes.

## <a name="general-usage"></a>Utilização geral

A TexConv produz sempre exatamente um ficheiro **de saída.** Pode utilizar **vários** ficheiros de entrada para montar a saída. Para a montagem, também precisa de um **mapeamento de canais**, que lhe diz qual canal (*Vermelho, Verde, Azul* ou *Alfa*) para tirar de qual ficheiro de entrada e movê-lo para o canal da imagem de saída.

A linha de comando mais direta é esta:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out` especifica o ficheiro de saída e o formato
- `-in0` especifica a primeira imagem de entrada
- `-rgba` diz-lhe que a imagem de saída deve usar todos os quatro canais e que devem ser tirados 1:1 da imagem de entrada

## <a name="multiple-input-files"></a>Vários ficheiros de entrada

Para montar a saída a partir de vários ficheiros de entrada, especifique cada ficheiro de entrada utilizando a `-in` opção com um número crescente:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Ao montar um mapa cúbico a partir de texturas 2D, também se pode usar `-right` , , , , ou , , `-left` , , `-top` . `-bottom` `-front` `-back` `-px` `-nx` `-py` `-ny` `-pz` `-nz` .

Para mapear estas entradas para o ficheiro de saída, é necessário um mapeamento de canal adequado.

## <a name="channel-mappings"></a>Mapeamentos de canais

As opções de mapeamento de canais especificam a partir da entrada para preencher os canais de saída determinados. Pode especificar a entrada para cada canal individualmente assim:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Aqui os canais RGB da saída seriam preenchidos usando a primeira imagem de entrada, mas vermelho e azul serão trocados. O canal alfa da saída seria preenchido com os valores do canal vermelho da segunda imagem de entrada.

Especificar o mapeamento de cada canal de forma separada dá a maior flexibilidade. Para conveniência, o mesmo pode ser escrito utilizando operadores "swizzling":

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Canais de saída

Estão disponíveis as seguintes opções de mapeamento de canais:

- `-r``-g`, " Estes `-b` `-a` especificam atribuições de canais únicos
- `-rg` : Especificar as atribuições de canal vermelho e verde.
- `-rgb` : Especificar as atribuições do canal vermelho, verde e azul.
- `-rgba` : Especifica todas as quatro missões de canal.

Mencionando apenas o canal R, RG ou RGB, instrui a TexConv a criar um ficheiro de saída com apenas 1, 2 ou 3 canais, respectivamente.

### <a name="input-swizzling"></a>Entrada a rodopiar

Ao indicar qual a textura de entrada que deve preencher qual o canal de saída, pode-se girar a entrada:

- `-rgba in0` é equivalente a `-rgba in0.rgba`
- `-rgba in0.bgra` vai girar os canais de entrada
- `-rgb in0.rrr` vai duplicar o canal vermelho em todos os canais

Pode-se também encher canais com preto ou branco:

- `-rgb in0 -a white` vai criar uma textura de saída de 4 canais, mas definir alfa para totalmente opaco
- `-rg black -b white` vai criar uma textura inteiramente azul

## <a name="common-options"></a>Opções comuns

As opções mais interessantes estão listadas abaixo. Mais opções são listadas por `TexConv --help` .

### <a name="output-type"></a>Tipo de saída

- `-type 2D` : A saída será uma imagem 2D regular.
- `-type Cubemap` : A saída será uma imagem de mapa de cubos. Suportado apenas para ficheiros de saída DDS. Quando isto é especificado, pode-se montar o mapa cúbico a partir de 6 imagens de entrada 2D regulares.

### <a name="image-compression"></a>Compressão da imagem

- `-compression none` : A imagem de saída não será compacta.
- `-compression medium` : Se suportado, a imagem de saída utilizará a compressão sem sacrificar demasiada qualidade.
- `-compression high` : Se suportado, a imagem de saída utilizará a compressão e sacrificará a qualidade em favor de um ficheiro menor.

### <a name="mipmaps"></a>Mipmaps

Por predefinição, a TexConv gera mipmaps quando o formato de saída o suporta.

- `-mipmaps none` : Mipmaps não serão gerados.
- `-mipmaps Linear` : Se suportado, os mipmaps serão gerados com um filtro de caixa.

### <a name="usage-srgb--gamma-correction"></a>Utilização (correção sRGB / gama)

A `-usage` opção especifica a finalidade da saída e, assim, diz à TexConv se deve aplicar a correção gama aos ficheiros de entrada e saída. O uso só afeta os canais RGB. O canal alfa é sempre considerado como contendo valores 'lineares'. Se a utilização não for especificada, o modo 'auto' tentará detetar a utilização a partir do formato e nome do ficheiro da primeira imagem de entrada. Por exemplo, os formatos de saída de canais simples e duplos são sempre lineares. Verifique a saída para ver que decisão a TexConv tomou.

- `-usage Linear` : A imagem de saída contém valores que não representam cores. Este é tipicamente o caso de texturas metálicas e de aspereza, bem como todos os tipos de máscaras.

- `-usage Color` : A imagem de saída representa a cor, tal como mapas difusos/albedo. A bandeira sRGB será colocada no cabeçalho DDS de saída.

- `-usage HDR` : O ficheiro de saída deve utilizar mais de 8 bits por pixel para codificação. Consequentemente, todos os valores são armazenados no espaço linear. Para texturas HDR não importa se os dados representam cor ou outros dados.

- `-usage NormalMap` : A imagem de saída representa um mapa normal do espaço tangente. Os valores serão normalizados e a computação mipmap será ligeiramente otimizada.

- `-usage NormalMap_Inverted` : A saída é um mapa normal do espaço tangente com Y apontando na direção oposta à entrada.

### <a name="image-rescaling"></a>Rescalagem de imagem

- `-minRes 64` : Especifica a resolução mínima da saída. Se a imagem de entrada for menor, será alta.
- `-maxRes 1024` : Especifica a resolução máxima da saída. Se a imagem de entrada for maior, ficará descalça.
- `-downscale 1` : Se for superior a 0, as imagens de entrada serão reduzidas para metade na resolução N vezes. Utilize isto para aplicar uma redução global da qualidade.

## <a name="examples"></a>Exemplos

### <a name="convert-a-color-texture"></a>Converter uma textura de cor

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Converter um mapa normal

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Criar um mapa de cubos HDR

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
