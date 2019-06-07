---
title: Diagnosticar problemas de desempenho de gráficos no ambiente de trabalho remoto - Azure
description: Este artigo descreve como utilizar contadores de gráficos de RemoteFX em sessões do protocolo RDP para diagnosticar problemas de desempenho com gráficos na área de Trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: 0b4113f1e0024415135aa99d1fb4e881efe448a3
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499268"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnosticar problemas de desempenho de gráficos no ambiente de trabalho remoto

Quando o sistema não realiza conforme esperado, é importante identificar a origem do problema. Este artigo ajuda-o a identificar e corrigir afunilamentos de desempenho relacionados com gráficos durante sessões de protocolo RDP (Remote Desktop).

## <a name="find-your-remote-session-name"></a>Encontrar o nome da sua sessão remota

Terá o nome da sua sessão remota para identificar os contadores de desempenho de gráficos. Siga as instruções nesta secção para identificar o seu nome de sessão remota do Windows Virtual Desktop Preview.

1. Abra a linha de comandos do Windows da sua sessão remota.
2. Executar o **qwinsta** comando.
    - Se a sua sessão estiver alojada numa máquina virtual (VM) multi sessão: O sufixo de nome de cada contador é o mesmo sufixo em seu nome de sessão, como "rdp-tcp 37."
    - Se a sua sessão estiver alojada numa VM que suporte virtual gráficos unidades de processamento (vGPU): Os contadores são armazenados no servidor, em vez de na sua VM. As instâncias do contador incluem o nome da VM em vez do número no nome da sessão, como "Win8 Enterprise VM."

>[!NOTE]
> Embora os contadores tenham RemoteFX nos respetivos nomes, eles incluem gráficos de área de trabalho remotos em cenários de vGPU também.

## <a name="access-performance-counters"></a>Contadores de desempenho de acesso

Contadores de desempenho em gráficos de RemoteFX ajudar a detectar afunilamentos ao ajudá-lo a controlar coisas como o intervalo de tempo de codificação e ignorada quadros.

Depois de determinar o nome da sua sessão remota, siga estas instruções para recolher os contadores de desempenho de gráficos de RemoteFX para a sessão remota.

1. Selecione **começar** > **ferramentas administrativas** > **Monitor de desempenho**.
2. Na **Monitor de desempenho** caixa de diálogo caixa, expanda **ferramentas de monitoramento**, selecione **Monitor de desempenho**e, em seguida, selecione **adicionar**.
3. Na **adicionar contadores** caixa de diálogo, da **contadores disponíveis** lista, expanda o objeto de contador de desempenho para gráficos de RemoteFX.
4. Selecione os contadores a serem monitoradas.
5. Na **objeto instâncias de selecionado** , selecione as instâncias de específicas a ser monitorizados para os contadores selecionados e, em seguida, selecione **Add**. Para selecionar todas as instâncias de contadores disponíveis, selecione **todas as instâncias**.
6. Depois de adicionar os contadores, selecione **OK**.

Os contadores de desempenho selecionados serão apresentado no ecrã de Monitor de desempenho.

>[!NOTE]
>Cada sessão ativa num anfitrião tem sua própria instância de cada contador de desempenho.

## <a name="diagnosis"></a>Diagnóstico

Em geral, os problemas de desempenho relacionados com gráficos enquadram-se em quatro categorias:

- Taxa de quadros baixa
- Paralisações aleatórias
- Latência elevada de entrada
- Qualidade de quadro fraca

Iniciar ao tratar de baixa taxa de quadros, paralisações aleatórias e latência elevada de entrada. A secção seguinte informará que contadores de desempenho medem cada categoria.

### <a name="performance-counters"></a>Contadores de desempenho

Esta secção ajuda-o a identificar afunilamentos.

Em primeiro lugar, verifique o contador de Frames/Second de saída. Ele mede o número de quadros fiquem disponíveis para o cliente. Se este valor é menor que o contador de entrada Frames/Second, os quadros são a ser ignorados. Para identificar o afunilamento, utilize os quadros contadores ignorados por segundo.

Existem três tipos de fotogramas ignorados por segundo contadores:

- Fotogramas ignorados por segundo (recursos de rede insuficiente)
- Fotogramas ignorados por segundo (recursos de cliente suficiente)
- Fotogramas ignorados por segundo (recursos de servidor insuficiente)

Um valor alto para qualquer um dos quadros ignorados por segundo contadores implica que o problema está relacionado ao recurso o contador controla. Por exemplo, se o cliente não decodificar e quadros presentes na mesma taxa que o servidor fornece os quadros, o contador de fotogramas ignorados por segundo (cliente recursos insuficientes) será elevado.

Se o contador de saída Frames/Second corresponder o contador de entrada Frames/Second, ainda que ainda terá desfasamento invulgar ou bloqueando, o problema pode ser o tempo médio de codificação. Codificação é um processo síncrono que ocorre no servidor no cenário de sessão único (vGPU) e na VM no cenário multi sessão. Tempo médio de codificação deve ser em 33 ms. Se o tempo médio de codificação é em 33 ms, mas ainda terá problemas de desempenho, pode haver um problema com a aplicação ou sistema operativo que está a utilizar.

Para obter mais informações sobre diagnosticar problemas relacionados com a aplicação, consulte [contadores de desempenho de atraso de entrada do usuário](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters).

Como o RDP suporta um tempo médio de codificação de 33 ms, ele oferece suporte a uma taxa de quadros de entrada até 30 quadros por segundo. Observe que 33 ms é a taxa de quadros suportado máximo. Em muitos casos, a taxa de quadros experiente pelo usuário será mais baixa, consoante a frequência com que um quadro é fornecido para RDP a origem. Por exemplo, tarefas, como ver um vídeo requerem uma taxa de quadros de entrada total de 30 quadros por segundo, mas menos tarefas de recursos pesados, como com pouca frequência editando um documento do word não precisam de uma taxa elevada de entrada fotogramas por segundo para uma boa experiência de utilizador.

Utilize o contador de qualidade do quadro para diagnosticar problemas de qualidade do quadro. Este contador expressa a qualidade da estrutura de saída como uma percentagem da qualidade do quadro de origem. A perda de qualidade pode ser devido a RemoteFX, ou pode ser inerente à origem de gráficos. Se o RemoteFX causou a perda de qualidade, o problema pode ser uma falta de recursos de rede ou servidor para enviar o conteúdo de fidelidade mais elevada.

## <a name="mitigation"></a>Mitigação

Se os recursos de servidor estão a causar o congestionamento, experimente uma das seguintes ações para melhorar o desempenho:

- Reduza o número de sessões por anfitrião.
- Aumentar a memória e recursos do servidor de computação.
- Remova a resolução da ligação.

Se os recursos de rede estão a causar o congestionamento, experimente uma das seguintes ações para melhorar a disponibilidade de rede por sessão:

- Reduza o número de sessões por anfitrião.
- Remova a resolução da ligação.
- Utilize uma rede de largura de banda superior.

Se os recursos de cliente estão a causar o congestionamento, efetue uma ou ambas das seguintes ações para melhorar o desempenho:

- Instale o cliente mais recente do ambiente de trabalho remoto.
- Aumente a memória e recursos no computador cliente de computação.

> [!NOTE]
> Atualmente não suportamos o contador de Frames/Second de origem. Por enquanto, o contador de origem Frames/Second sempre será definido como 0.

## <a name="next-steps"></a>Passos Seguintes

- Para criar uma máquina virtual do Azure com otimização de GPU, veja [configurar a aceleração da (GPU unidade) para o ambiente de pré-visualização de ambiente de Trabalho Virtual do Windows de processamento gráfico](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu).
- Para uma descrição geral de faixas de escalamento e resolução de problemas, consulte [descrição geral, comentários e suporte de resolução de problemas](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview).
- Para saber mais sobre o serviço de pré-visualização, veja [ambiente de pré-visualização do Windows Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
