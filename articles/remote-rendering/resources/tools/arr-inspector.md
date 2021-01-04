---
title: Ferramenta de inspeção ArrInspector
description: Manual do utilizador da ferramenta ArrInspector
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: 300e0ff26d643ae0263d21e604cb26da37a18841
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723830"
---
# <a name="the-arrinspector-inspection-tool"></a>Ferramenta de inspeção ArrInspector

O ArrInspector é uma ferramenta baseada na web utilizada para inspecionar uma sessão de renderização remota Azure. Destina-se a ser utilizado para depurar fins, inspecionar a estrutura da cena que está a ser prestada, mostrar as mensagens de registo e monitorizar o desempenho ao vivo no servidor.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Ligação ao ArrInspector

Assim que obtiver o nome de anfitrião (terminando `mixedreality.azure.com` em) do seu servidor ARR, conecte-se utilizando [o ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector). Esta função cria um `StartArrInspector.html` no dispositivo em que a aplicação está a funcionar. Para lançar o ArrInspector, abra esse ficheiro com um browser (Edge, Firefox ou Chrome) num PC. O ficheiro só é válido por 24 horas.

Se a aplicação que liga `ConnectToArrInspectorAsync` já estiver em execução num PC:

* Se estiver a utilizar a integração Da Unidade, poderá ser lançado automaticamente para si.
* Caso contrário, encontrará o ficheiro nas *pastas de utilizador \\ LocalAppData \\ [your_app] \\ AC \\ Temp*.

Se a aplicação estiver a funcionar num HoloLens:

1. Aceda aos HoloLens utilizando o Portal do [Dispositivo Windows](/windows/mixed-reality/using-the-windows-device-portal).
1. Vá ao *System > File Explorer*.
1. Navegue para *as pastas de utilizador \\ LocalAppData \\ [your_app] \\ AC \\ Temp*.
1. Guarde *StartArrInspector.html* para o seu PC.
1. Abra *StartArrInspector.html* para carregar o ArrInspector da sessão.

## <a name="the-performance-panel"></a>O painel performance

![O Painel de Performance](./media/performance-panel.png)

Este painel mostra gráficos de todos os valores de desempenho por quadro expostos pelo servidor. Os valores atualmente incluem o tempo de fotogramas, FPS, CPU e utilização da memória, estatísticas de memória como o uso geral de RAM, contagens de objetos, etc.

Para visualizar um destes parâmetros, clique no botão **Adicionar Novo** e selecione um dos valores disponíveis indicados no diálogo. Esta ação adiciona um novo gráfico de scrolling ao painel, traçando os valores em tempo real. À sua direita pode ver o valor *mínimo,* *máximo* e *corrente.*

Pode fazer o gráfico, arrastando o seu conteúdo com o rato, no entanto, panorâmica horizontalmente só é possível quando o ArrInspector estiver no estado de pausa.

Segurar o CTRL durante o arrastamento, permite-lhe fazer zoom. O zoom horizontal também pode ser controlado com o deslizador na parte inferior.

A gama vertical é calculada por padrão com base nos valores atualmente apresentados, e os valores min e máximo são mostrados nas caixas de texto à direita. Quando os valores são definidos manualmente, quer digitando-os diretamente na caixa de texto, quer por panorâmica/zooming, o gráfico utilizará esses valores. Para restaurar o enquadramento vertical automático, clique no ícone no canto superior direito.

![gama vertical](./media/vertical-range.png)

## <a name="the-log-panel"></a>O painel de registo

![Painel de Registo](./media/log-panel.png)

O painel de registo mostra uma lista de mensagens de registo geradas no lado do servidor. Na ligação, aparecerá até 200 mensagens de registo anteriores e imprimirá novas à medida que forem acontecendo.

Pode filtrar a lista com base no tipo de registo `[Error/Warning/Info/Debug]` utilizando os botões na parte superior.
![Botões de filtro de registo](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>O painel de captura de dados de tempo

![Captura de dados de cronometragem](./media/timing-data-capture.png)

Este painel é utilizado para capturar informações de tempo do servidor e descarregá-la. O ficheiro utiliza o [formato Chrome Tracing JSON](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit). Para inspecionar os dados, abra o Chrome no URL `Chrome://tracing` e arraste e deixe cair o ficheiro descarregado na página. Os dados de tempo são continuamente recolhidos num tampão de anel de tamanho fixo. Quando escrita, a captura inclui apenas informações sobre o passado imediato, o que significa alguns segundos a poucos minutos.

## <a name="the-scene-inspection-panel"></a>O painel de inspeção de cena

![Painel de Inspeção de Cena](./media/scene-inspection-panel.png)

Este painel mostra a estrutura da cena renderizada. A hierarquia do objeto está à esquerda, o conteúdo do objeto selecionado está à direita. O painel é apenas de leitura e é atualizado em tempo real.

## <a name="the-vm-debug-information-panel"></a>O painel de informação VM Debug

![Painel de Informação VM Debug](./media/state-debugger-panel.png)

Este painel oferece alguma funcionalidade de depurar.

### <a name="restart-service"></a>Reiniciar o serviço

O botão **Reiniciar o serviço** reinicia o tempo de funcionaamento na máquina virtual a que o arrInspector está ligado. Qualquer cliente anexo será desligado e a página arrInspector deve ser recarregada para ligar ao serviço reiniciado.

### <a name="collect-debug-information"></a>Recolher informações de depurg

A **Informação de Depuração de Recolha para** botão VM abre um diálogo que lhe permite acionar a instância ARR para recolher informações de depuração no VM:

![Diálogo de Informação VM Debug](./media/state-debugger-dialog.png)

A informação de depuração ajuda a equipa de renderização remota do Azure a analisar quaisquer problemas que ocorram num caso de ARR em execução. O diálogo tem um campo de texto para fornecer detalhes adicionais, por exemplo, passos para reproduzir um problema.

Depois de clicar no botão **Iniciar a Recolha,** o diálogo fechar-se-á e o processo de recolha começa. Recolher a informação sobre o VM pode demorar alguns minutos.

![VM Debug Recolha de informação em curso](./media/state-debugger-panel-in-progress.png)

Uma vez terminada a recolha, receberá uma notificação na janela ArrInspector. Esta notificação contém um ID que identifica esta coleção em particular. Certifique-se de guardar este ID para passá-lo para a equipa de renderização remota Azure.

![VM Debug Sucesso da recolha de informação](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Não é possível descarregar ou aceder a informações de depurar VM. Apenas a equipa de Renderização Remota Azure tem acesso aos dados recolhidos. Você precisa nos contactar e enviar a identificação da coleção junto, para que nós investiguemos o problema que você está vendo.

## <a name="pause-mode"></a>Modo de pausa

No canto superior direito, um interruptor permite-lhe interromper a atualização ao vivo dos painéis. Este modo pode ser útil para inspecionar cuidadosamente um estado específico.

![Modo pausa](./media/pause-mode.png)

Ao voltar a ativar a atualização ao vivo, todos os painéis são reiniciados.