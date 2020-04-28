---
title: Aplicações de HPC de escala - Máquinas Virtuais Azure / Microsoft Docs
description: Saiba como dimensionar as aplicações de HPC em VMs Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67707846"
---
# <a name="scaling-hpc-applications"></a>Aplicações de HPC de escala

O ótimo desempenho de escala e escala das aplicações hpc no Azure requer experiências de afinação e otimização de desempenho para a carga de trabalho específica. Esta secção e as páginas específicas da série VM oferecem orientação geral para escalonar as suas aplicações.

## <a name="compiling-applications"></a>Compilação de aplicações

Embora não seja necessário, a compilação de aplicações com bandeiras de otimização apropriadas proporciona o melhor desempenho em HB e HC-series VMs.

### <a name="amd-optimizing-cc-compiler"></a>Compilador de otimização AMD C/C++

O sistema compilador amd Optimizing C/C++ (AOCC) oferece um alto nível de otimizações avançadas, multi-threading e suporte ao processador que inclui otimização global, vectorização, análises interprocessutivas, transformações em loop e geração de códigos. Binários de compilação AOCC são adequados para sistemas Linux com a versão 2.17 da Biblioteca GNU C (glibc) 2.17. A suíte compilada é composta por um compilador C/C++ (clang), um compilador Fortran (FLANG) e uma extremidade frontal Fortran para Clang (Ovo de Dragão).

### <a name="clang"></a>Rio Clang

Clang é um compilador C, C++, e compilador Objective-C que trata do pré-processamento, análise, otimização, geração de códigos, montagem e ligação. Clang suporta `-march=znver1` a bandeira para permitir a melhor geração de código e sintonização para a arquitetura zen x86 baseada em AMD.

### <a name="flang"></a>FLANG

O compilador FLANG é uma adição recente à suite AOCC (adicionada abril de 2018) e está atualmente em pré-lançamento para os desenvolvedores descarregarem e testarem. Com base no Fortran 2008, a AMD alargahttps://github.com/flangcompiler/flang)a versão GitHub da FLANG . O compilador FLANG suporta todas as opções de compilador Clang e um número adicional de opções de compilador específicas da FLANG.

### <a name="dragonegg"></a>Ovo de Dragão

DragonEgg é um plugin gcc que substitui os otimizadores e geradores de código da GCC com os do projeto LLVM. DragonEgg que vem com trabalhos ao AOCC com gcc-4.8.x, foi testado para alvos x86-32/x86-64 e tem sido usado com sucesso em várias plataformas Linux.

GFortran é o frontend real para programas Fortran responsáveis por pré-processamento, análise e análise semântica gerando a representação intermédia GCC GIMPLE (IR). DragonEgg é um plugin GNU, ligando-se ao fluxo de compilação GFortran. Implementa a API de plugin gnu. Com a arquitetura plugin, dragonEgg torna-se o controlador, conduzindo as diferentes fases de compilação.  Após seguir as instruções de descarregamento e instalação, o Ovo de Dragão pode ser invocado usando: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Compilador de IGP
PgI Community Edition ver. 17 está confirmado para trabalhar com amd EPYC. Uma versão compilada por IGP do STREAM oferece largura de banda de memória completa da plataforma. A mais recente Edição Comunitária 18.10 (nov 2018) também deverá funcionar bem. Abaixo está a amostra CLI para compilar da melhor forma com o Compilador intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Compilador de Intel
Intel Compiler ver. 18 está confirmado para trabalhar com amd EPYC. Abaixo está a amostra CLI para compilar da melhor forma com o Compilador Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Compilador GCC 
Para o HPC, a AMD recomenda o compilador GCC 7.3 ou mais recente. Versões mais antigas, como 4.8.5 incluídas com RHEL/CentOS 7.4, não são recomendadas. GCC 7.3, e mais recente, irá proporcionar um desempenho significativamente mais elevado nos testes HPL, HPCG e DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Dimensionamento de aplicações 

As seguintes sugestões aplicam-se para uma ótima eficiência de dimensionamento de aplicações, desempenho e consistência:

* Os processos de pin para os núcleos 0-59 utilizando uma abordagem sequencial de fixação (em oposição a uma abordagem de equilíbrio automático). 
* A ligação por Numa/Core/HwThread é melhor do que a ligação padrão.
* Para aplicações paralelas híbridas (OpenMP+MPI), utilize 4 fios e 1 classificação DE MPI por CCX.
* Para aplicações de MPI puras, experimente 1-4 mpI por CCX para um desempenho ótimo.
* Algumas aplicações com extrema sensibilidade à largura de banda da memória podem beneficiar da utilização de um número reduzido de núcleos por CCX. Para estas aplicações, a utilização de 3 ou 2 núcleos por CCX pode reduzir a contenção da largura de banda da memória e gerar um desempenho mais elevado no mundo real ou uma escalabilidade mais consistente. Em particular, o MPI Allreduce pode beneficiar disto.
* Para corridas de escala significativamente maiores, recomenda-se a utilização de transportes ud ou híbridos RC+UD. Muitas bibliotecas/bibliotecas de tempo de execução do MPI fazem-no internamente (como ucX ou MVAPICH2). Verifique as configurações de transporte para obter corridas em larga escala.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
