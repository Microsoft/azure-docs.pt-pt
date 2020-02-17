---
title: Diagnosticar problemas de desempenho gráficos Remote Desktop - Azure
description: Este artigo descreve como usar contadores de gráficos RemoteFX em sessões remotas de protocolo sinuoso para diagnosticar problemas de desempenho com gráficos no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
ms.openlocfilehash: eee8fdf515861b43b58d5af111930e2224c9a60a
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367234"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnosticar problemas de desempenho gráficos no Ambiente de Trabalho Remoto

Para diagnosticar problemas de qualidade com as suas sessões remotas, foram fornecidos contadores sob a secção De gráficos RemoteFX do Monitor de Desempenho. Este artigo ajuda-o a identificar e corrigir estrangulamentos de desempenho relacionados com gráficos durante as sessões do Protocolo de Ambiente de Trabalho Remoto (RDP) utilizando estes contadores.

## <a name="find-your-remote-session-name"></a>Encontre o seu nome de sessão remota

Vai precisar do seu nome de sessão remota para identificar os contadores de desempenho gráficos. Siga as instruções nesta secção para identificar a sua instância de cada contador.

1. Abra o pedido de comando do Windows a partir da sua sessão remota.
2. Execute o comando **qwinsta** e encontre o seu nome de sessão.
    - Se a sua sessão for hospedada numa máquina virtual multi-sessão (VM): A sua instância de cada contador é sufixada pelo mesmo número que sufixo o seu nome de sessão, como "rdp-tcp 37".
    - Se a sua sessão for hospedada num VM que suporta unidades de processamento de gráficos virtuais (vGPU): A sua instância de cada contador é armazenada no servidor em vez de no seu VM. As suas contra-instâncias incluem o nome VM em vez do número no nome da sessão, como "Win8 Enterprise VM".

>[!NOTE]
> Embora os contadores tenham RemoteFX em seus nomes, eles incluem gráficos remotos de desktop em cenários vGPU também.

## <a name="access-performance-counters"></a>Contadores de desempenho de acesso

Depois de ter determinado o seu nome de sessão remota, siga estas instruções para recolher os contadores de desempenho da RemoteFX Graphics para a sua sessão remota.

1. Selecione **Iniciar** > **Ferramentas Administrativas** > Monitor de **Desempenho**.
2. Na caixa de diálogo Do Monitor de **Desempenho,** expanda **as ferramentas de monitorização,** selecione **Performance Monitor,** e, em seguida, selecione **Adicionar**.
3. Na caixa de diálogo **Add Counters,** a partir da lista de **contadores disponíveis,** expanda a secção para Gráficos RemoteFX.
4. Selecione os balcões a monitorizar.
5. Nos **casos da lista de objetos selecionados,** selecione as instâncias específicas a serem monitorizadas para os contadores selecionados e, em seguida, selecione **Adicionar**. Para selecionar todas as contrainstâncias disponíveis, selecione **Todas as instâncias**.
6. Depois de adicionar os balcões, selecione **OK**.

Os contadores de desempenho selecionados aparecerão no ecrã do Monitor de Desempenho.

>[!NOTE]
>Cada sessão ativa de um hospedeiro tem a sua própria instância de cada contador de desempenho.

## <a name="diagnose-issues"></a>Diagnosticar problemas

As questões de desempenho relacionadas com gráficos geralmente se enquadram em quatro categorias:

- Baixa taxa de fotogramas
- Barracas aleatórias
- Alta latência de entrada
- Má qualidade de quadro

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Abordando a baixa taxa de fotogramas, as bancas aleatórias e a elevada latência de entrada

Primeiro verifique os quadros de saída/segundo contador. Mede o número de quadros disponibilizados ao cliente. Se este valor for inferior ao input Frames/Segundo contador, os quadros estão a ser ignorados. Para identificar o estrangulamento, utilize os balcões Desbloqueados/Segundos.

Existem três tipos de quadros ignorados/segundos contadores:

- Quadros ignorados/segundo (Recursos insuficientes do servidor)
- Quadros ignorados/segundo (recursos de rede insuficientes)
- Quadros ignorados/segundo (Recursos insuficientes do cliente)

Um valor elevado para qualquer um dos balcões Desemcimados/Segundos dos Quadros implica que o problema está relacionado com o recurso que o contador rastreia. Por exemplo, se o cliente não descodificar e apresentar quadros ao mesmo ritmo que o servidor fornece os quadros, o contador Frames Skipped/Second (Recursos Clientes Insuficientes) será elevado.

Se o contador 'Frames/Segundo 'Quadros de saída corresponder ao 'Quadros de Entrada/Segundo balcão', no entanto, ainda nota lag ou paragem invulgar, o tempo médio de codificação pode ser o culpado. A codificação é um processo sincronizado que ocorre no servidor no cenário de uma única sessão (vGPU) e no VM no cenário de várias sessões. O tempo médio de codificação deve ser inferior a 33 ms. Se o tempo médio de codificação for inferior a 33 ms, mas ainda tiver problemas de desempenho, pode haver um problema com a app ou sistema operativo que está a utilizar.

Para obter mais informações sobre o diagnóstico de problemas relacionados com aplicações, consulte [os contadores](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)de desempenho do Atraso de Entrada do Utilizador .

Como o RDP suporta um tempo médio de codificação de 33 ms, suporta uma taxa de fotogramas de entrada até 30 fotogramas/segundo. Note que 33 ms é a taxa máxima de fotogramas suportada. Em muitos casos, a taxa de fotogramas experimentada pelo utilizador será mais baixa, dependendo da frequência com que um quadro é fornecido ao RDP pela fonte. Por exemplo, tarefas como ver um vídeo requerem uma taxa de fotogramas de entrada completa de 30 fotogramas/segundo, mas tarefas menos computacionalmente intensivas como a edição pouco frequente de um documento resultam num valor muito menor para Quadros de Entrada/Segundo sem degradação na qualidade de experiência do utilizador.

### <a name="addressing-poor-frame-quality"></a>Abordar a má qualidade do quadro

Utilize o contador de qualidade de quadro para diagnosticar problemas de qualidade de quadro. Este contador expressa a qualidade do quadro de saída em percentagem da qualidade do quadro de origem. A perda de qualidade pode ser devido ao RemoteFX, ou pode ser inerente à fonte gráfica. Se o RemoteFX causou a perda de qualidade, o problema pode ser a falta de recursos de rede ou servidor para enviar conteúdo de maior fidelidade.

## <a name="mitigation"></a>Mitigação

Se os recursos do servidor estiverem a causar o estrangulamento, experimente uma das seguintes abordagens para melhorar o desempenho:

- Reduza o número de sessões por hospedeiro.
- Aumente a memória e calcule os recursos no servidor.
- Largue a resolução da ligação.

Se os recursos da rede estiverem a causar o estrangulamento, experimente uma das seguintes abordagens para melhorar a disponibilidade da rede por sessão:

- Reduza o número de sessões por hospedeiro.
- Utilize uma rede de largura de banda mais elevada.
- Largue a resolução da ligação.

Se os recursos do cliente estiverem a causar o estrangulamento, experimente uma das seguintes abordagens para melhorar o desempenho:

- Instale o mais recente cliente remote desktop.
- Aumentar a memória e calcular recursos na máquina do cliente.

> [!NOTE]
> Atualmente não apoiamos o contador Source Frames/Second. Por enquanto, o contador Source Frames/Segundo apresentará sempre 0.

## <a name="next-steps"></a>Passos seguintes

- Para criar uma máquina virtual Azure otimizada por GPU, consulte a aceleração da unidade de [processamento de gráficos (GPU) para](configure-vm-gpu.md)o ambiente de ambiente de trabalho virtual do Windows .
- Para uma visão geral das pistas de resolução de problemas e escalada, consulte a [visão geral, o feedback e](troubleshoot-set-up-overview.md)o suporte de Resolução de Problemas.
- Para saber mais sobre o serviço, consulte o [ambiente windows desktop](environment-setup.md).
