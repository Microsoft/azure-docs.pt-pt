---
title: Aplicações de escala HPC - Azure Virtual Machines Microsoft Docs
description: Saiba como escalar as aplicações HPC em VMs Azure.
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
ms.openlocfilehash: 1de7b1412a242e1b658128ba11f482a2d9dc3107
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82594802"
---
# <a name="scaling-hpc-applications"></a>Aplicações de escala HPC

O melhor desempenho de escala e escala das aplicações HPC no Azure requer experiências de afinação e otimização de desempenho para a carga de trabalho específica. Esta secção e as páginas específicas da série VM oferecem orientações gerais para o escalonamento das suas aplicações.

## <a name="compiling-applications"></a>Compilação de aplicações

Embora não seja necessário, compilar aplicações com bandeiras de otimização apropriadas proporciona o melhor desempenho de escala em HB e HC série VMs.

### <a name="amd-optimizing-cc-compiler"></a>Compilador de otimização da AMD C/C++

O sistema de compilador de compilador C/C++ (AOCC) da AMD otimização oferece um alto nível de otimizações avançadas, multi-threading e suporte ao processador que inclui otimização global, vectorização, análises inter-processuais, transformações de loop e geração de códigos. Os binários de compilador AOCC são adequados para sistemas Linux com a versão 2.17 ou superior da GNU C Library (glibc). A suíte de compilador é composta por um compilador C/C++ (clang), um compilador Fortran (FLANG) e uma extremidade frontal de Fortran para Clang (Ovo de Dragão).

### <a name="clang"></a>Clang

Clang é um compilador C, C++e Objective-C que lida com pré-processamento, análise, otimização, geração de códigos, montagem e ligação. Clang suporta a `-march=znver1` bandeira para permitir a melhor geração de código e afinação para a arquitetura zen baseada em X86 da AMD.

### <a name="flang"></a>RIO FLANG

O compilador FLANG é uma adição recente à suite AOCC (adicionado abril de 2018) e está atualmente em pré-lançamento para os desenvolvedores descarregarem e testarem. Com base no Fortran 2008, a AMD estende a versão GitHub de FLANG ( https://github.com/flang-compiler/flang) . O compilador FLANG suporta todas as opções do compilador Clang e um número adicional de opções de compilador específico flang.

### <a name="dragonegg"></a>DragonEgg

DragonEgg é um plugin gcc que substitui os otimizadores e geradores de código da GCC com os do projeto LLVM. DragonEgg que vem com ao AOCC funciona com gcc-4.8.x, foi testado para alvos x86-32/x86-64 e tem sido usado com sucesso em várias plataformas Linux.

GFortran é o frontend real para os programas Fortran responsáveis pelo pré-processamento, análise e análise semântica gerando a representação intermediária GCC GIMPLE (IR). DragonEgg é um plugin GNU, que liga o fluxo de compilação GFortran. Implementa a API plugin plugin GNU. Com a arquitetura plugin, DragonEgg torna-se o condutor do compilador, conduzindo as diferentes fases da compilação.  Após seguir as instruções de descarregamento e instalação, o Ovo de Dragão pode ser invocado utilizando: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Compilador de IGP
Edição Comunitária de IGP ver. 17 está confirmado para trabalhar com a AMD EPYC. Uma versão compilada por PGI do STREAM fornece largura de banda de memória completa da plataforma. A nova Edição Comunitária 18.10 (nov 2018) também deve funcionar bem. Abaixo está a amostra CLI para compilar o melhor com o Compilador Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Compilador intel
Intel Compiler ver. 18 está confirmado para trabalhar com a AMD EPYC. Abaixo está a amostra CLI para compilar o melhor com o Compilador Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Compilador GCC 
Para o HPC, a AMD recomenda o compilador GCC 7.3 ou mais recente. Não são recomendadas versões mais antigas, como 4.8.5 incluídas com RHEL/CentOS 7.4. O GCC 7.3, e mais recente, proporcionará um desempenho significativamente maior nos testes de HPL, HPCG e DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Dimensionamento de aplicações 

As seguintes sugestões aplicam-se para uma melhor eficiência, desempenho e consistência de escala de aplicações:

* Pin processa para núcleos 0-59 utilizando uma abordagem de fixação sequencial (em oposição a uma abordagem de equilíbrio automático). 
* A ligação por Numa/Core/HwThread é melhor do que a ligação padrão.
* Para aplicações paralelas híbridas (OpenMP+MPI), utilize 4 fios e 1 grau MPI por CCX.
* Para aplicações mpi puras, experimente com 1-4 mpi ranks por CCX para obter um desempenho ideal.
* Algumas aplicações com extrema sensibilidade à largura de banda de memória podem beneficiar da utilização de um número reduzido de núcleos por CCX. Para estas aplicações, a utilização de 3 ou 2 núcleos por CCX pode reduzir a contenção da largura de banda da memória e produzir um desempenho mais elevado no mundo real ou uma escalabilidade mais consistente. Em particular, o MPI Allreduce pode beneficiar disto.
* Para corridas de escala significativamente maiores, recomenda-se a utilização de transportes UD ou híbridos RC+UD. Muitas bibliotecas mpi/bibliotecas de tempo de execução fazem-no internamente (como UCX ou MVAPICH2). Verifique as configurações de transporte para obter execuções em larga escala.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) em Azure.
