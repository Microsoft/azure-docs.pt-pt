---
title: Kit de processamento de lote para recipientes de fala
titleSuffix: Azure Cognitive Services
description: Utilize o kit de processamento do Lote para escalar os pedidos do recipiente de discurso.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: 80e0de73bbeae2ee1a79199fde34a3c430959ac8
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356710"
---
# <a name="batch-processing-kit-for-speech-containers"></a>Kit de processamento de lote para recipientes de fala

Utilize o kit de processamento de lote para complementar e escalar cargas de trabalho em recipientes de fala. Disponível como um recipiente, este utilitário de código aberto ajuda a facilitar a transcrição de lotes para um grande número de ficheiros áudio, em qualquer número de pontos finais de contentores de fala baseados na nuvem. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="Um diagrama que mostra um fluxo de trabalho de recipiente de lote de exemplo.":::

O recipiente do kit de lote está disponível gratuitamente no centro [GitHub](https://github.com/microsoft/batch-processing-kit) e [Docker.](https://hub.docker.com/r/batchkit/speech-batch-kit/tags) Só é [cobrado](speech-container-howto.md#billing) pelos recipientes de fala que usa.

| Funcionalidade  | Descrição  |
|---------|---------|
| Distribuição de ficheiros áudio de lote     | Despacha automaticamente um grande número de ficheiros para as instalações ou pontos finais de contentores de fala baseados na nuvem. Os ficheiros podem estar em qualquer volume compatível com POSIX, incluindo sistemas de ficheiros de rede.       |
| Integração do SDK de discurso | Passe bandeiras comuns para o Discurso SDK, incluindo: n-melhores hipóteses, diarização, linguagem, máscara de profanação.  |
|Modos de execução     | Executar o cliente do lote uma vez, continuamente em segundo plano, ou criar pontos finais HTTP para ficheiros áudio.         |
| Tolerância a falhas | Recandidatura automaticamente e continue a transcrição sem perder progresso, e diferenciar entre quais os erros que podem e não podem ser novamente julgados. |
| Deteção da disponibilidade de pontos finais | Se um ponto final ficar indisponível, o cliente do lote continuará a transcrever, utilizando outros pontos finais do contentor. Depois de voltar a estar disponível, o cliente começará automaticamente a utilizar o ponto final.   |
| Troca de ponto final | Adicione, remova ou modifique os pontos finais do recipiente da fala durante o tempo de funcionamento sem interromper o progresso do lote. As atualizações são imediatas. |
| Registo em tempo real | Registo em tempo real de pedidos de tentativas, relógios e razões de falha, com ficheiros de registo SDK de fala para cada ficheiro áudio. |

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com `docker pull`

Utilize o comando de puxar o [estivador](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar o mais recente recipiente do kit de lote.

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>Configuração do ponto final

O cliente do lote toma um ficheiro de configuração yaml que especifica os pontos finais do recipiente on-prem. O exemplo a seguir pode ser escrito `/mnt/my_nfs/config.yaml` , que é usado nos exemplos abaixo. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

Este exemplo de yaml especifica três recipientes de fala em três anfitriões. O primeiro anfitrião é especificado por um endereço IPv4, o segundo está em execução no mesmo VM que o cliente do lote, e o terceiro recipiente é especificado pelo nome de anfitrião DNS de outro VM. O `concurrency` valor especifica as transcrições de ficheiros em simultâneo máximas que podem ser executadas no mesmo recipiente. O `rtf` valor (Fator Em Tempo Real) é opcional e pode ser usado para sintonizar o desempenho.
 
O cliente do lote pode detetar dinamicamente se um ponto final ficar indisponível (por exemplo, devido a um problema de reinicialização ou rede de contentores), e quando voltar a estar disponível. Os pedidos de transcrição não serão enviados para contentores que não estejam disponíveis, e o cliente continuará a utilizar outros contentores disponíveis. Pode adicionar, remover ou editar pontos finais a qualquer momento sem interromper o progresso do seu lote.


## <a name="run-the-batch-processing-container"></a>Executar o recipiente de processamento de lote
  
> [!NOTE] 
> * Este exemplo utiliza o mesmo diretório `/my_nfs` () para o ficheiro de configuração e as entradas, saídas e diretórios de registos. Pode utilizar diretórios hospedados ou montados em NFS para estas pastas.
> * Executar o cliente irá `–h` listar os parâmetros disponíveis da linha de comando e os seus valores predefinidos. 
> * O recipiente de processamento de lote só é suportado em Linux.

Use o comando Docker `run` para ligar o recipiente. Isto irá iniciar uma concha interativa dentro do recipiente.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

Para executar o cliente do lote:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

Para executar o cliente do lote e o recipiente num único comando:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


O cliente vai começar a correr. Se um ficheiro áudio já tiver sido transcrito numa execução anterior, o cliente saltará automaticamente o ficheiro. Os ficheiros são enviados com uma repetição automática se ocorrerem erros transitórios, e pode diferenciar entre os erros que pretende que o cliente rejueça. Num erro de transcrição, o cliente continuará a transcrição, e pode voltar a tentar sem perder o progresso.  

## <a name="run-modes"></a>Modos de execução 

O kit de processamento de lote oferece três modos, utilizando o `--run-mode` parâmetro.

#### <a name="oneshot"></a>[Um tiro](#tab/oneshot)

`ONESHOT` o modo transcreve um único lote de ficheiros áudio (de um diretório de entrada e lista de ficheiros opcionais) para uma pasta de saída.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="Um diagrama que mostra os ficheiros de processamento do recipiente do kit de lote no modo oneshot.":::

1. Defina os pontos finais do recipiente speech que o cliente do lote utilizará no `config.yaml` ficheiro. 
2. Coloque ficheiros de áudio para transcrição num diretório de entrada.  
3. Invoque o recipiente no diretório, que começará a processar os ficheiros. Se o ficheiro áudio já tiver sido transcrito numa execução anterior com o mesmo diretório de saída (mesmo nome de ficheiro e conta de verificação), o cliente saltará o ficheiro. 
4. Os ficheiros são enviados para os pontos finais do contentor a partir do passo 1.
5. Os registos e a saída do contentor da fala são devolvidos ao diretório de saída especificado. 

#### <a name="daemon"></a>[Rio Daemon](#tab/daemon)

> [!TIP]
> Se vários ficheiros forem adicionados ao diretório de entrada ao mesmo tempo, pode melhorar o desempenho adicionando-os num intervalo regular.

`DAEMON` o modo transcreve os ficheiros existentes numa determinada pasta e transcreve continuamente novos ficheiros de áudio à medida que são adicionados.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="Um diagrama que mostra ficheiros de processamento de recipientes de kit de lote no modo Daemon.":::

1. Defina os pontos finais do recipiente speech que o cliente do lote utilizará no `config.yaml` ficheiro. 
2. Invoque o recipiente num diretório de entrada. O cliente do lote começará a monitorizar o diretório para os ficheiros de entrada. 
3. Configurar a entrega contínua de ficheiros áudio para o diretório de entrada. Se o ficheiro áudio já tiver sido transcrito numa execução anterior com o mesmo diretório de saída (mesmo nome de ficheiro e conta de verificação), o cliente saltará o ficheiro. 
4. Uma vez detetado um ficheiro escrito ou sinal POSIX, o recipiente é acionado para responder.
5. Os ficheiros são enviados para os pontos finais do contentor a partir do passo 1.
6. Os registos e a saída do contentor da fala são devolvidos ao diretório de saída especificado. 

#### <a name="rest"></a>[REST](#tab/rest)

`REST` modo é um modo de servidor API que fornece um conjunto básico de pontos finais HTTP para submissão de ficheiros áudio, verificação de estado e sondagens longas. Também permite o consumo programático usando uma extensão de módulo python, ou importando como uma submula.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="Um diagrama que mostra os ficheiros de processamento do recipiente do kit de lote no modo REST.":::

1. Defina os pontos finais do recipiente speech que o cliente do lote utilizará no `config.yaml` ficheiro. 
2. Envie um pedido HTTP a um dos pontos finais do servidor API. 
        
    |Ponto final  |Descrição  |
    |---------|---------|
    |`/submit`     | Ponto final para a criação de novos pedidos de lote.        |
    |`/status`     | Ponto final para verificar o estado de um pedido de lote. A ligação permanecerá aberta até que o lote esteja concluído.       |
    |`/watch`     | Ponto final para a utilização de sondagens longas HTTP até que o lote esteja concluído.        |

3. Os ficheiros áudio são enviados a partir do diretório de entradas. Se o ficheiro áudio já tiver sido transcrito numa execução anterior com o mesmo diretório de saída (mesmo nome de ficheiro e conta de verificação), o cliente saltará o ficheiro. 
4. Se um pedido for enviado para o `/submit` ponto final, os ficheiros são enviados para os pontos finais do contentor a partir do passo 1.
5. Os registos e a saída do contentor da fala são devolvidos ao diretório de saída especificado. 

---

## <a name="logging"></a>Registo

> [!NOTE]
> O cliente do lote pode substituir periodicamente o ficheiro *run.log* se ficar demasiado grande.

O cliente cria um ficheiro *run.log* no diretório especificado pelo `-log_folder` argumento no comando do estivador. `run` Os registos são capturados ao nível DEBUG por defeito. Os mesmos registos são enviados para o `stdout/stderr` , e filtrados dependendo do `-log_level` argumento. Este registo é apenas necessário para depurar, ou se precisar enviar um rastreio para obter apoio. A pasta de registo também contém os registos SDK de discurso para cada ficheiro áudio.

O diretório de saída especificado `-output_folder` conterá um *run_summary.jsem*   arquivo, que é periodicamente reescrito a cada 30 segundos ou sempre que novas transcrições estejam terminadas. Pode utilizar este ficheiro para verificar o progresso à medida que o lote prossegue. Também conterá as estatísticas finais de execução e o estado final de cada ficheiro quando o lote estiver concluído. O lote é concluído quando o processo tiver uma saída limpa. 

## <a name="next-steps"></a>Próximos passos

* [Como instalar e executar contentores](speech-container-howto.md)
