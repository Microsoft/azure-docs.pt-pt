---
title: Dimensionamento de aplicações de HPC - máquinas virtuais do Azure | Documentos da Microsoft
description: Saiba como dimensionar aplicações HPC em VMs do Azure.
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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707846"
---
# <a name="scaling-hpc-applications"></a>Dimensionamento de aplicações de HPC

Desempenho ideal de aumento vertical e horizontal das aplicações de HPC no Azure requer o ajuste de desempenho e otimização experimentações para a carga de trabalho específica. Esta secção e as páginas de série específicos VM oferecem orientação geral para dimensionar as suas aplicações.

## <a name="compiling-applications"></a>Compilação de aplicativos

Embora não necessário, compilar aplicativos com os sinalizadores de otimização apropriado fornece o melhor desempenho de aumentar verticalmente em HB e as VMs da série HC.

### <a name="amd-optimizing-cc-compiler"></a>AMD otimizar C /C++ compilador

A AMD otimizar C /C++ sistema de compilador do compilador (AOCC) oferece um elevado nível de otimizações avançadas, vários segmentos e o suporte de processador que inclui a otimização global, vetorização, análises de entre procedimentos, transformações de loop, e geração de código. Binários de compilador AOCC são adequados para sistemas Linux com a biblioteca de C do GNU (glibc) versão 2.17 e superior. O conjunto de compilador consiste num C /C++ compilador (clang), um compilador Fortran (FLANG) e um front-end de publicação na Fortran ' para Clang (Dragon ovo).

### <a name="clang"></a>Clang

Clang é um C, C++e o compilador de Objective-C, manipulação de pré-processamento, análise, otimização, geração de código, assembly e de ligação. Clang suporta o `-march=znver1` sinalizador para ativar melhor geração de códigos e ajuste para o Zen da AMD com base em x86 arquitetura.

### <a name="flang"></a>FLANG

O compilador FLANG é uma adição recente para o conjunto AOCC (Abril de 2018 adicionado) e está atualmente em versões de pré-lançamento para desenvolvedores baixar e testar. Com base no Fortran 2008, AMD estende a versão do GitHub do FLANG (https://github.com/flangcompiler/flang). O compilador FLANG suporta todas as opções de compilador Clang e um número adicional de opções do compilador de FLANG específicas.

### <a name="dragonegg"></a>DragonEgg

DragonEgg é um plug-in de gcc que substitui do GCC otimizadores e geradores de código com os do projeto LLVM. DragonEgg que vem com AOCC funciona com gcc-4.8.x, foi testado para destinos de x86-32/x86-64 e tem sido usada com êxito em várias plataformas de Linux.

GFortran é o front-end real para programas de Fortran responsáveis pelo processamento prévio de, análise e análises semântico geração da representação intermediária de GCC GIMPLE (IR). DragonEgg é um plug-in do GNU, conectando-se no flow de compilação GFortran. Ele implementa o plug-in do GNU API. Com a arquitetura de plug-in, o DragonEgg torna-se o controlador de compilador, orientando as diferentes fases da compilação.  Depois de seguir as instruções de transferência e instalação, Dragon ovo podem ser invocado usando: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Compilador PGI
Comunidade PGI ver. de edição 17 foi confirmada para trabalhar com AMD EPYC. Uma versão de compilação PGI do fluxo de fornecer a largura de banda de memória cheia da plataforma. O 18.10 de edição de Comunidade (Novembro de 2018) mais recente da mesma forma deve funcionar bem. Segue-se da CLI de exemplo para o compilador de forma otimizada com o compilador Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Compilador Intel
Ver. de compilador Intel 18 foi confirmada para trabalhar com AMD EPYC. Segue-se da CLI de exemplo para o compilador de forma otimizada com o compilador Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Compilador GCC 
Para HPC, AMD recomenda o compilador GCC 7.3 ou mais recente. Versões mais antigas, como 4.8.5 incluído no RHEL/CentOS 7.4, não são recomendadas. GCC 7.3 e mais recente, irá fornecer um desempenho significativamente superior nos testes HPL HPCG e DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Dimensionamento de aplicações 

As sugestões seguintes aplicam-se para aumentar a eficiência, desempenho e consistência de aplicações ideal:

* PIN processos a serem núcleos 0-59 usando uma fixação sequenciais abordar (em vez de uma abordagem de saldo automática). 
* A ligação Numa/Core/HwThread é melhor do que a vinculação padrão.
* Nos aplicativos em paralelo híbrida (OpenMP + MPI), utilize 4 threads e 1 MPI classificação por CCX.
* Para aplicações de MPI puras, experimente com 1 a 4 que MPI classifica por CCX para um desempenho ideal.
* Alguns aplicativos com extremo sensibilidade a largura de banda de memória podem beneficiar da utilização de uma redução do número de núcleos por CCX. Para estas aplicações, o utilizar núcleos de 2 ou 3 por CCX pode reduzir a contenção de largura de banda de memória e resultar em desempenho mais elevado do mundo real ou escalabilidade mais consistente. Em particular, MPI Allreduce podem tirar partido desta.
* Para execuções de dimensionamento significativamente maiores, é recomendado utilizar transportes de RC + UD UD ou híbrida. Muitas bibliotecas de bibliotecas/runtime MPI fazê-lo internamente (como UCX ou MVAPICH2). Verifique as configurações de transporte para execuções de grande escala.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
