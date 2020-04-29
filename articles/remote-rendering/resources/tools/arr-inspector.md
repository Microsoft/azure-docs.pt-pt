---
title: Ferramenta de inspeção ArrInspector
description: Manual do utilizador da ferramenta ArrInspector
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680079"
---
# <a name="the-arrinspector-inspection-tool"></a>Ferramenta de inspeção ArrInspector

O ArrInspector é uma ferramenta baseada na web usada para inspecionar uma sessão de renderização remota Azure em execução. Destina-se a ser utilizado para fins de depuração, para inspecionar a estrutura da cena que está a ser renderizada, mostrar as mensagens de registo e monitorizar o desempenho ao vivo no servidor.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Ligação ao ArrInspector

Assim que obtiver o `mixedreality.azure.com`nome de anfitrião (terminando em ) do seu servidor ARR, ligue-se utilizando [o ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector). Esta função `StartArrInspector.html` cria um dispositivo no qual a aplicação está em execução. Para lançar o ArrInspector, abra esse ficheiro com um browser (Edge, Firefox ou Chrome) num PC. O ficheiro só é válido por 24 horas.

Se a aplicação que liga `ConnectToArrInspectorAsync` já estiver em execução num PC:

* Se estiver a utilizar a integração da Unidade, pode ser lançado automaticamente para si.
* Caso contrário, encontrará o ficheiro nas *Pastas\\de\\Utilizador\\\\LocalAppData [your_app] AC Temp*.

Se a aplicação estiver a funcionar num HoloLens:

1. Aceda aos HoloLens utilizando o Portal do [Dispositivo Windows](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal).
1. Ir ao *System > File Explorer*.
1. Navegue para *pastas\\de utilizador\\\\\\LocalAppData [your_app] AC Temp*.
1. Guarde *startArrInspector.html* para o seu PC.
1. Open *StartArrInspector.html* para carregar o ArrInspector da sessão.

## <a name="the-performance-panel"></a>O painel performance

![O Painel de Performance](./media/performance-panel.png)

Este painel mostra gráficos de todos os valores de desempenho por quadro expostos pelo servidor. Os valores atualmente incluem o tempo de fotogramas, FPS, CPU e uso da memória, estatísticas de memória como uso geral de RAM, contagem de objetos, etc.

Para visualizar um destes parâmetros, clique no botão **Adicionar Novo** e selecione um dos valores disponíveis no diálogo. Esta ação adiciona um novo gráfico de scrolling ao painel, traçando os valores em tempo real. À sua direita pode ver o valor *mínimo,* *máximo* e *atual.*

Pode fazer pan no gráfico, arrastando o seu conteúdo com o rato, no entanto, a panorâmica horizontal só é possível quando o ArrInspector estiver em estado de pausa.

Segurar a CTRL durante o arrasto, permite-lhe fazer zoom. O zoom horizontal também pode ser controlado com o slider na parte inferior.

A gama vertical é por padrão computada com base nos valores atualmente apresentados, e os valores min e máximos são mostrados nas caixas de texto à direita. Quando os valores são definidos manualmente, quer escrevendo-os diretamente na caixa de texto, quer através da panorâmica/zooming, o gráfico utilizará esses valores. Para restaurar o enquadramento vertical automático, clique no ícone no canto superior direito.

![alcance vertical](./media/vertical-range.png)

## <a name="the-log-panel"></a>O painel de registo

![Painel de Registo](./media/log-panel.png)

O painel de registo mostra uma lista de mensagens de registo geradas no lado do servidor. Na ligação, apresentar-se-á até 200 mensagens de registo anteriores e irá imprimir novas à medida que acontecerem.

Pode filtrar a lista com `[Error/Warning/Info/Debug]` base no tipo de registo utilizando os botões na parte superior.
![Botões de filtro de log](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>O painel de captura de dados de tempo

![Captura de dados de tempo](./media/timing-data-capture.png)

Este painel é utilizado para capturar informações de tempo do servidor e descarregá-la. O ficheiro utiliza o [formato JSON](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit)de rastreio do Chrome. Para inspecionar os dados, abra `Chrome://tracing` o Chrome no URL e arraste e deixe cair o ficheiro descarregado na página. Os dados de tempo são continuamente recolhidos num tampão de anel de tamanho fixo. Quando escrita, a captura inclui apenas informações sobre o passado imediato, o que significa alguns segundos a poucos minutos.

## <a name="the-scene-inspection-panel"></a>O painel de Inspeção de Cena

![Painel de Inspeção de Cena](./media/scene-inspection-panel.png)

Este painel mostra a estrutura da cena renderizada. A hierarquia do objeto está à esquerda, o conteúdo do objeto selecionado está à direita. O painel é apenas de leitura e é atualizado em tempo real.

## <a name="the-vm-debug-information-panel"></a>O painel de informação VM Debug

![Painel de Informação VM Debug](./media/state-debugger-panel.png)

Este painel oferece alguma funcionalidade de depuração.

### <a name="restart-service"></a>Serviço de reinício

O botão **Restart Service** reinicia o tempo de execução na máquina virtual a que o arrInspector está ligado. Qualquer cliente em anexo será desligado e a página arrInspector deve ser recarregada para ligar ao serviço reiniciado.

### <a name="collect-debug-information"></a>Recolher informações sobre depuração

O botão **Collect Debug Information for VM** abre um diálogo que lhe permite ativar a instância DE ARR para recolher informações sobre o depurador no VM:

![Diálogo de informação vm depuração](./media/state-debugger-dialog.png)

A informação de depuração ajuda a equipa de renderização remota Azure a analisar quaisquer problemas que ocorram numa instância DE ARR em execução. O diálogo tem um campo de texto para fornecer detalhes adicionais, por exemplo, passos para reproduzir um problema.

Depois de clicar no botão **Iniciar Recolha,** o diálogo fechará e o processo de recolha começa. Recolher a informação sobre o VM pode demorar alguns minutos.

![Coleção de informação VM Debug em andamento](./media/state-debugger-panel-in-progress.png)

Uma vez terminada a recolha, receberá uma notificação na janela ArrInspector. Esta notificação contém um ID que identifica esta coleção em particular. Certifique-se de guardar este ID para passá-lo para a equipa de renderização remota Azure.

![VM Debug Sucesso de recolha de informação](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Não é possível descarregar ou aceder a informações sobre depuração vm. Apenas a equipa de renderização remota Azure tem acesso aos dados recolhidos. Tem de nos contactar e enviar a identificação da coleção para que investiguemos o problema que está a ver.

## <a name="pause-mode"></a>Modo de pausa

No canto superior direito, um interruptor permite-lhe parar a atualização ao vivo dos painéis. Este modo pode ser útil para inspecionar cuidadosamente um estado específico.

![Modo pausa](./media/pause-mode.png)

Ao reativar a atualização ao vivo, todos os painéis são reset.

## <a name="host-configuration"></a>Configuração do anfitrião

Por defeito, a ferramenta liga-se ao servidor ARR que está a funcionar no mesmo hospedeiro ao serviço do ArrInspector. No entanto, pode configurá-lo para inspecionar outro servidor, assumindo que está a executar uma instância de ARR com a porta de ferramentas aberta.

Para isso, aceda ao menu principal à esquerda da barra de cabeçalho e selecione a configuração do *Anfitrião*. Clique em **Adicionar novo anfitrião**e insira o nome e o nome do anfitrião. Para o nome de *anfitrião* utilize apenas o nome de anfitrião que termina em `.mixedreality.azure.com`, não inclua `http://` ou uma porta.

![Configuração do anfitrião](./media/host-configuration.png)

Para mudar rapidamente de um hospedeiro para outro, use a queda para baixo na parte superior direita.

![Host Combo](./media/host-switch-combo.png)

A lista de anfitriões está armazenada no armazenamento local do navegador, pelo que será preservada na reabertura do mesmo navegador.
