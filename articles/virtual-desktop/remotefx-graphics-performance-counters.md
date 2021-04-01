---
title: Diagnosticar problemas de desempenho gráficos Remote Desktop - Azure
description: Este artigo descreve como utilizar contadores de gráficos RemoteFX em sessões de protocolo de ambiente de trabalho remoto para diagnosticar problemas de desempenho com gráficos no Windows Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: dc5c6499fa47a1e32a517032d5cc2a97b3f2677f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88007171"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnosticar problemas de desempenho gráficos em Ambiente de Trabalho Remoto

Para diagnosticar problemas de qualidade com as suas sessões remotas, foram fornecidos contadores na secção De Gráficos RemoteFX do Monitor de Desempenho. Este artigo ajuda-o a identificar e corrigir estrangulamentos de desempenho relacionados com gráficos durante as sessões do Protocolo de Ambiente de Trabalho Remoto (RDP) utilizando estes contadores.

## <a name="find-your-remote-session-name"></a>Encontre o nome da sessão remota

Vai precisar do nome da sessão remota para identificar os contadores de desempenho gráficos. Siga as instruções desta secção para identificar a sua instância de cada contador.

1. Abra o pedido de comando do Windows a partir da sua sessão remota.
2. Executar o comando **qwinsta** e encontrar o nome da sua sessão.
    - Se a sua sessão for hospedada numa máquina virtual de várias sessões (VM): A sua instância de cada contador é sufixada pelo mesmo número que sufixa o nome da sessão, como "RDP-tcp 37".
    - Se a sua sessão for hospedada num VM que suporta Unidades de Processamento de Gráficos Virtuais (vGPU): A sua instância de cada contador é armazenada no servidor em vez de no seu VM. As suas contra-instâncias incluem o nome VM em vez do número no nome da sessão, como "Win8 Enterprise VM".

>[!NOTE]
> Embora os contadores tenham RemoteFX nos seus nomes, também incluem gráficos remotos de desktop em cenários vGPU.

## <a name="access-performance-counters"></a>Contadores de desempenho de acesso

Depois de ter determinado o nome da sessão remota, siga estas instruções para recolher os contadores de desempenho RemoteFX Graphics para a sua sessão remota.

1. Selecione **Iniciar**  >  **Ferramentas Administrativas**  >  **Monitor de Desempenho**.
2. Na caixa de diálogo do Monitor de **Desempenho,** expanda **as Ferramentas de Monitorização,** selecione **Monitor de Desempenho** e, em seguida, selecione **Add**.
3. Na caixa de diálogo **Add Counters,** da lista **de Contadores disponíveis,** expanda a secção para Gráficos RemoteFX.
4. Selecione os contadores a monitorizar.
5. Nas **instâncias da lista de objetos selecionados,** selecione as instâncias específicas a serem monitorizadas para os contadores selecionados e, em seguida, selecione **Adicionar**. Para selecionar todas as instâncias de contra-ataque disponíveis, selecione **Todas as instâncias**.
6. Depois de adicionar os contadores, selecione **OK**.

Os contadores de desempenho selecionados aparecerão no ecrã do Monitor de Desempenho.

>[!NOTE]
>Cada sessão ativa de um anfitrião tem a sua própria instância de cada contador de desempenho.

## <a name="diagnose-issues"></a>Diagnosticar problemas

As questões de desempenho relacionadas com gráficos geralmente se enquadram em quatro categorias:

- Baixa taxa de fotogramas
- Barracas aleatórias
- Alta latência de entrada
- Má qualidade do quadro

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Abordando a baixa taxa de fotogramas, as bancas aleatórias e a elevada latência de entrada

Verifique primeiro os Quadros de Saída/Segundo contador. Mede o número de quadros disponibilizados ao cliente. Se este valor for inferior ao contador Input Frames/Second, os quadros estão a ser ignorados. Para identificar o estrangulamento, utilize os balcões Frames Skipped/Second.

Existem três tipos de balcões De Quadros Saltados/Segundo:

- Quadros ignorados/segundo (Recursos insuficientes do servidor)
- Quadros Ignorados/Segundo (Recursos de Rede Insuficientes)
- Quadros Ignorados/Segundo (Recursos insuficientes do cliente)

Um valor elevado para qualquer um dos contadores Frames Skipped/Second implica que o problema está relacionado com o recurso que o contador rastreia. Por exemplo, se o cliente não descodificar e apresentar quadros à mesma taxa que o servidor fornece os quadros, o contador Frames Skipped/Second (Insuficientes Recursos do Cliente) será elevado.

Se o contador Quadros/Segundo De saída corresponder ao contador 'Quadros de entrada/segundo', no entanto, ainda nota um atraso ou empate invulgar, o tempo médio de codificação pode ser o culpado. A codificação é um processo sincronizado que ocorre no servidor no cenário de uma sessão única (vGPU) e no VM no cenário de várias sessão. O tempo médio de codificação deve ser inferior a 33 ms. Se o tempo médio de codificação tiver menos de 33 ms mas ainda tiver problemas de desempenho, pode haver um problema com a app ou sistema operativo que está a utilizar.

Para obter mais informações sobre o diagnóstico de problemas relacionados com a aplicação, consulte os [contadores de desempenho do Atraso de Entrada do Utilizador](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

Uma vez que o RDP suporta um tempo médio de codificação de 33 ms, suporta uma taxa de fotogramas de entrada até 30 fotogramas/segundo. Note que 33 ms é a taxa máxima de fotogramas suportada. Em muitos casos, a taxa de fotogramas experimentada pelo utilizador será menor, dependendo da frequência com que uma moldura é fornecida ao PDR pela fonte. Por exemplo, tarefas como assistir a um vídeo requerem uma taxa de fotogramas de entrada completa de 30 fotogramas/segundo, mas tarefas menos computacionalmente intensivas como a edição rara de um documento resultam num valor muito mais baixo para Os Quadros de Entrada/Segundo sem degradação na qualidade de experiência do utilizador.

### <a name="addressing-poor-frame-quality"></a>Abordar a má qualidade do quadro

Utilize o contador de qualidade do quadro para diagnosticar problemas de qualidade do quadro. Este contador expressa a qualidade do quadro de saída em percentagem da qualidade do quadro de origem. A perda de qualidade pode dever-se ao RemoteFX, ou pode ser inerente à fonte gráfica. Se o RemoteFX causou a perda de qualidade, o problema pode ser a falta de recursos de rede ou servidor para enviar conteúdo de maior fidelidade.

## <a name="mitigation"></a>Mitigação

Se os recursos do servidor estiverem a causar o estrangulamento, experimente uma das seguintes abordagens para melhorar o desempenho:

- Reduza o número de sessões por anfitrião.
- Aumente a memória e calcule os recursos no servidor.
- Deixe cair a resolução da ligação.

Se os recursos de rede estiverem a causar o estrangulamento, experimente uma das seguintes abordagens para melhorar a disponibilidade da rede por sessão:

- Reduza o número de sessões por anfitrião.
- Utilize uma rede de largura de banda mais alta.
- Deixe cair a resolução da ligação.

Se os recursos dos clientes estiverem a causar o estrangulamento, experimente uma das seguintes abordagens para melhorar o desempenho:

- Instale o mais recente cliente de Ambiente de Trabalho Remoto.
- Aumente a memória e calcule os recursos na máquina cliente.

> [!NOTE]
> Atualmente, não suportamos o contador Source Frames/Second. Por enquanto, o contador Source Frames/Second mostrará sempre 0.

## <a name="next-steps"></a>Passos seguintes

- Para criar uma máquina virtual Azure otimizada da GPU, consulte [a aceleração da unidade de processamento de gráficos Configure (GPU) para o ambiente virtual do Ambiente de Trabalho virtual do Windows](configure-vm-gpu.md).
- Para uma visão geral das faixas de resolução de problemas e de escalada, consulte [a visão geral da resolução de problemas, o feedback e o suporte](troubleshoot-set-up-overview.md).
- Para saber mais sobre o serviço, consulte o [ambiente de ambiente de trabalho do Windows](environment-setup.md).
