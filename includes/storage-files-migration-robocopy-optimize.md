---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 57d14ae6e6da7cfa883f1aed74cce390c0185d98
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491692"
---
A velocidade e a taxa de sucesso de uma determinada execução roboCopy dependerá de vários fatores:

* IOPS na origem e armazenamento de alvos
* a largura de banda disponível entre a fonte e o alvo
* a capacidade de processar rapidamente ficheiros e pastas num espaço de nome
* o número de alterações entre roboCopy corre


### <a name="iops-and-bandwidth-considerations"></a>IOPS e considerações de largura de banda

Nesta categoria, é necessário considerar as capacidades do armazenamento de **origem,** o **armazenamento do alvo** e a **rede** que os liga. A produção máxima possível é determinada pelo mais lento destes três componentes. Certifique-se de que a sua infraestrutura de rede está configurada para suportar velocidades de transferência ideais para as suas melhores capacidades.

> [!CAUTION]
> Embora a cópia o mais rápido possível seja muitas vezes a mais desejada, considere a utilização da sua rede local e do aparelho NAS para outras tarefas críticas, muitas vezes empresariais.

Copiar o mais rápido possível pode não ser desejável quando há o risco de a migração poder monopolizar os recursos disponíveis.

* Considere quando é melhor no seu ambiente executar migrações: durante o dia, fora de horas ou durante os fins de semana.
* Considere também o QoS de rede num Servidor do Windows para acelerar a velocidade do RoboCopy.
* Evite trabalhos desnecessários para as ferramentas de migração.

O RobCopy pode inserir atrasos entre pacotes especificando o `/IPG:n` interruptor onde é medido em `n` milissegundos entre pacotes RoboCopy. A utilização deste interruptor pode ajudar a evitar a monopolização de recursos em dispositivos constrangidos da IO e em ligações de rede lotadas.

`/IPG:n` não pode ser utilizado para um estrangulamento preciso da rede a um determinado Mbps. Utilize o QoS da Rede de Servidor do Windows. A RoboCopy baseia-se inteiramente no protocolo SMB para todas as necessidades de networking. Usar o SMB é a razão pela qual o RoboCopy não pode influenciar a própria produção da rede, mas pode abrandar o seu uso. 

Uma linha de pensamento semelhante aplica-se ao IOPS observado no NAS. O tamanho do cluster no volume NAS, tamanhos de pacotes e uma variedade de outros fatores influenciam o IOPS observado. Introduzir atrasos entre pacotes é muitas vezes a forma mais fácil de controlar a carga no NAS. Teste vários valores, por exemplo, de cerca de 20 milissegundos (n=20) a múltiplos desse número. Uma vez que introduza um atraso, pode avaliar se as suas outras aplicações podem agora funcionar como esperado. Esta estratégia de otimização permitir-lhe-á encontrar a velocidade ideal do RoboCopy no seu ambiente.

### <a name="processing-speed"></a>Velocidade de processamento

O RoboCopy vai atravessar o espaço de nome a que está apontado e avaliar cada ficheiro e pasta para cópia. Cada ficheiro será avaliado durante uma cópia inicial e durante as cópias de atualização. Por exemplo, repetições de RoboCopy /MIR contra os mesmos locais de armazenamento de fonte e alvo. Estes runs repetidos são úteis para minimizar o tempo de inatividade para utilizadores e apps, e para melhorar a taxa de sucesso geral dos ficheiros migrados.

Muitas vezes, não consideramos a largura de banda o fator mais limitativo numa migração - o que pode ser verdade. Mas a capacidade de enumerar um espaço de nome pode influenciar o tempo total de copiar ainda mais para espaços de nome maiores com ficheiros menores. Considere que copiar 1 TiB de pequenos ficheiros levará consideravelmente mais tempo do que copiar 1 TiB de menos, mas maiores ficheiros. Assumindo que todas as outras variáveis permanecem as mesmas.

A causa para esta diferença é o poder de processamento necessário para andar por um espaço de nome. O RoboCopy suporta cópias multi-roscadas através do `/MT:n` parâmetro onde n representa o número de fios do processador. Assim, ao fornecer uma máquina especificamente para roboCopy, considere o número de núcleos de processador e a sua relação com a contagem de fios que eles fornecem. Mais comum são dois fios por núcleo. A contagem de núcleo e de rosca de uma máquina é um ponto de dados importante para decidir quais os valores multi-thread `/MT:n` que deve especificar. Considere também quantos empregos robocopia você pretende executar em paralelo numa determinada máquina.

Mais fios copiarão o nosso exemplo 1-TiB de pequenos ficheiros consideravelmente mais rápidos do que menos fios. Ao mesmo tempo, o investimento extra de recursos no nosso 1 TiB de ficheiros maiores pode não produzir benefícios proporcionais. Uma contagem de fios elevado tentará copiar mais dos grandes ficheiros em simultâneo na rede. Esta atividade extra de rede aumenta a probabilidade de ser constrangido por IOPS de produção ou armazenamento.

### <a name="avoid-unnecessary-work"></a>Evitar trabalhos desnecessários

Evite alterações em larga escala no seu espaço de nome. Por exemplo, mover ficheiros entre diretórios, alterar propriedades em larga escala ou alterar permissões (NTFS ACLs). Especialmente as alterações da ACL podem ter um impacto elevado porque muitas vezes têm um efeito de mudança em cascata em ficheiros mais baixos na hierarquia da pasta. As consequências podem ser:

* tempo de funcionamento prolongado do roboCopy porque cada ficheiro e pasta afetados por uma alteração ACL precisa de ser atualizado
* reutilização de dados movidos mais cedo pode ter de ser recopitado. Por exemplo, mais dados terão de ser copiados quando as estruturas das pastas mudarem depois de os ficheiros já terem sido copiados anteriormente. Um trabalho de RoboCopy não pode "reproduzir" uma mudança de espaço de nome. O próximo trabalho deve purgar os ficheiros previamente transportados para a estrutura da pasta antiga e carregar novamente os ficheiros na nova estrutura da pasta.

Outro aspeto importante é utilizar eficazmente a ferramenta RoboCopy. Com o script RoboCopy recomendado, irá criar e guardar um ficheiro de registo para erros. Podem ocorrer erros de cópia - isto é normal. Estes erros muitas vezes tornam necessário executar várias rondas de uma ferramenta de cópia como roboCopy. Uma execução inicial, digamos, de um NAS para o DataBox ou de um servidor para uma partilha de ficheiros Azure. E uma ou mais corridas extras com o interruptor /MIR para capturar e revassar ficheiros que não foram copiados.

Você deve estar preparado para executar várias rondas de RoboCopy contra um determinado âmbito de espaço de nome. As sucessivas corridas terminarão mais rapidamente, uma vez que têm menos para copiar, mas são cada vez mais limitadas pela velocidade de processamento do espaço de nome. Quando fizer várias rondas, pode acelerar cada ronda sem que o RoboCopy tente de forma irracional para copiar tudo numa determinada corrida. Estes interruptores RoboCopy podem fazer uma diferença significativa:

* `/R:n` n = quantas vezes se recandid copiar um ficheiro falhado e 
* `/W:n` n = quantos segundos para esperar entre retróstas

`/R:5 /W:5` é uma definição razoável que pode ajustar ao seu gosto. Neste exemplo, um ficheiro falhado será novamente julgado cinco vezes, com um tempo de espera de cinco segundos entre as retrórias. Se o ficheiro ainda não conseguir copiar, o próximo trabalho do RoboCopy tentará novamente. Muitas vezes os ficheiros que falharam porque estão a ser utilizados ou devido a problemas de tempo limite podem eventualmente ser copiados com sucesso desta forma.
   
