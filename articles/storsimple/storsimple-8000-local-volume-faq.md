---
title: StorSimple localmente afixado volumes perguntas frequentes | Documentos da Microsoft
description: Fornece respostas às perguntas mais frequentes sobre volumes do StorSimple afixado localmente.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: aa69d8b07d31b5cf0386e34c113475cbf4191891
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58013795"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>Volumes do StorSimple localmente afixados: Perguntas mais frequentes sobre (FAQ)
## <a name="overview"></a>Descrição geral
Seguem-se perguntas e respostas que possa ter quando criar um volume localmente afixado do StorSimple, converter um volume em camadas para um volume localmente afixado (e vice-versa), ou criar cópias de segurança e restaurar um volume afixado localmente.

Perguntas e respostas estão organizadas nas seguintes categorias

* Criar um volume afixado localmente
* Backup de um afixado localmente
* Converter um volume em camadas para um volume afixado localmente
* Restaurar um volume afixado localmente
* Realizar a ativação pós-falha de um volume afixado localmente

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Perguntas sobre a criação de um volume afixado localmente
**P.** O que é o tamanho máximo de um volume afixado localmente que posso criar nos dispositivos da 8000 série?

**A** em dispositivos com o StorSimple 8000 Series Update 3.0, pode aprovisionar volumes localmente afixados até 8,5 TB ou volumes em camadas até 200 TB no dispositivo 8100. No dispositivo 8600 maior, pode aprovisionar volumes localmente afixados até 22,5 TB ou volumes em camadas até 500 TB.

**P.** Atualizei recentemente o meu dispositivo 8100 para Update 3.0 e ao tentar criar um volume localmente afixado, o tamanho máximo disponível é apenas 6 TB e não 8,5 TB. Por que motivo não consigo criar um volume de 8,5 TB?

**A** se o dispositivo estiver a executar o update 3.0, pode aprovisionar volumes localmente afixados até 8.5 TB ou volumes de em camadas até 200 TB no dispositivo 8100. Se o seu dispositivo já tem volumes em camadas, em seguida, o espaço disponível para a criação de um volume localmente afixado será proporcionalmente inferior este limite máximo. Por exemplo, se já tiverem sido aprovisionada aproximadamente, 106 TB de volumes escalonados no dispositivo 8100 (que é a metade da capacidade em camadas), em seguida, o tamanho máximo de um volume local que pode criar no dispositivo 8100 será proporcionalmente reduzido para 4 TB (aproximadamente metade o máximo localmente afixado a capacidade de volume).

Uma vez que algum espaço local no dispositivo é utilizado para alojar o conjunto de trabalho de volumes em camadas, o espaço disponível para a criação de um volume localmente afixado é reduzido, se o dispositivo tem volumes em camadas. Por outro lado, a criação de um volume localmente afixado proporcionalmente reduz o espaço disponível para volumes em camadas. As tabelas a seguir resume a capacidade em camadas disponível nos dispositivos 8100 e 8600 quando são criados os volumes afixados localmente.

#### <a name="update-30"></a>Atualizar 3.0 

| Capacidade aprovisionada de volumes localmente afixados | Capacidade disponível para ser aprovisionada para volumes em camadas - 8100 | Capacidade disponível para ser aprovisionada para volumes em camadas - 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8.5 TB |0 TB |311.1 TB |
| 10 TB |ND |277.8 TB |
| 15 TB |ND |166.7 TB |
| 22.5 TB |ND |0 TB |

**P.** Por que é a criação do volume afixado localmente uma operação de longa execução?

**R.** Os volumes afixados localmente são amplamente aprovisionados. Para criar espaço nas camadas locais do dispositivo, alguns dados a partir de volumes em camadas existentes podem ser enviados por push para a cloud durante o processo de aprovisionamento. E, uma vez que isso depende do tamanho do volume a ser aprovisionado, os dados existentes no seu dispositivo e a largura de banda disponível para a cloud, o tempo necessário para criar um volume local pode ser várias horas.

**P.** Quanto tempo demora a criar um volume localmente afixado?

**R.** Uma vez que os volumes afixados localmente são amplamente aprovisionados, alguns dados existentes a partir de volumes em camadas podem ser enviados por push para a cloud durante o processo de aprovisionamento. Por conseguinte, o tempo necessário para criar um volume localmente afixado depende de vários fatores, incluindo o tamanho do volume, os dados no seu dispositivo e a largura de banda disponível. Num dispositivo recém-instalado que tem não existem volumes, o tempo para criar um volume localmente afixado é cerca de 10 minutos por terabyte de dados. No entanto, a criação de volumes locais pode demorar várias horas com base nos fatores explicados acima num dispositivo que está a ser utilizado.

**P.** Quero criar um volume afixado localmente. Existem as melhores práticas, preciso ter em consideração?

**R.** Os volumes afixados localmente são adequados para cargas de trabalho que precisam de garantias locais de dados durante todo o tempo e são sensíveis a latências da cloud. Ao considerar a utilização de volumes locais para qualquer uma das suas cargas de trabalho, seja em atenção o seguinte:

* Os volumes afixados localmente são amplamente aprovisionados e criação de volumes locais afeta o espaço disponível para volumes em camadas. Por conseguinte, sugerimos que comece com volumes de menor porte e aumentar verticalmente à medida que sua aumenta de requisito de armazenamento.
* Aprovisionamento de volumes locais é uma operação de longa execução que pode envolver a enviar dados existentes de volumes em camadas para a cloud. Como resultado, poderá ter um desempenho reduzido nestes volumes.
* Aprovisionamento de volumes locais é uma operação demorada. O tempo em questão envolvidos depende de vários fatores: o tamanho do volume a ser aprovisionado, os dados no seu dispositivo e a largura de banda disponível. Se não efetuou uma cópia os volumes existentes para a cloud, criação do volume é mais lenta. Sugerimos que tira instantâneos de cloud do seus volumes existentes, antes de aprovisionar um volume local.
* Pode converter os volumes em camadas existentes para volumes afixados localmente e essa conversão envolve o aprovisionamento de espaço no dispositivo para o volume afixado localmente resultante (além de trazer dados em camadas, se houver, da cloud). Novamente, essa é uma operação de longa execução que depende de fatores que discutimos acima. Sugerimos que criar cópias de segurança os volumes existentes antes da conversão, o processo será ainda mais lento, se os volumes existentes não são guardados. O dispositivo também poderá detetar um desempenho reduzido durante este processo.

Obter mais informações sobre como [criar um volume localmente afixado](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**P.** Pode criar vários volumes localmente afixados ao mesmo tempo?

**R.** Sim, mas as tarefas de criação e a expansão do volume afixado localmente são processados sequencialmente.

Os volumes afixados localmente são amplamente aprovisionados e isso requer a criação de espaço local no dispositivo (o que pode resultar em dados existentes do volumes escalonados para ser enviado para a cloud durante o processo de aprovisionamento). Por conseguinte, se uma tarefa de aprovisionamento está em curso, outras tarefas de criação do local volume ficarão em fila até que essa tarefa é concluída.

Da mesma forma, se está a ser expandido um volume local existente ou um volume em camadas que está a ser convertido para um volume afixado localmente, em seguida, a criação de um novo volume afixado localmente é colocada na fila até que a tarefa anterior seja concluída. Expandir o tamanho de um volume localmente afixado envolve a expansão de espaço local existente para esse volume. Conversão de um em camadas para localmente afixado volume também envolve a criação de espaço local para resultante localmente afixado de volume. Em ambos essas operações, a criação ou a expansão de espaço local é uma longa executar tarefa.

Pode ver estas tarefas na **tarefas** painel do serviço StorSimple Device Manager. A tarefa que está a ser processada ativamente é continuamente atualizada para refletir o progresso do aprovisionamento de espaço. As tarefas restantes do volume afixado localmente são marcadas como em execução, mas está parado o progresso e elas são aplicadas pela ordem em que foram colocados na fila.

**P.** Posso eliminar um volume afixado localmente. Por que motivo não vejo o espaço reclaimed refletido no espaço disponível ao tentar criar um novo volume?

**R.** Se eliminar um volume localmente afixado, o espaço disponível para novos volumes não pode ser atualizado imediatamente. O serviço StorSimple Device Manager atualiza o espaço local disponível aproximadamente a cada hora. Sugerimos que aguarde uma hora antes de tentar criar o novo volume.

**P.** Os volumes afixados localmente são suportados na aplicação da cloud?

**R.** Os volumes afixados localmente não são suportados na aplicação da cloud (8010 e 8020 dispositivos anteriormente conhecidos como o dispositivo virtual StorSimple).

**P.** Pode utilizar os cmdlets do PowerShell do Azure para criar e gerir volumes afixados localmente?

**R.** Não, não é possível criar volumes localmente afixados através de cmdlets do Azure PowerShell (qualquer volume que criar através do Azure PowerShell está em camadas). Sugerimos também que não utilize os cmdlets do Azure PowerShell para modificar quaisquer propriedades de um volume localmente afixado, como ele terá o efeito indesejado de modificar o tipo de volume para em camadas.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Perguntas sobre como fazer backup de um volume afixado localmente
**P.** São os instantâneos locais de volumes localmente afixados suportados?

**R.** Sim, pode tirar instantâneos locais dos volumes afixados localmente. No entanto, sugerimos enfaticamente que faça regularmente backup os volumes afixados localmente com instantâneos de cloud para garantir que os seus dados estão protegidos na eventualidade de um desastre.

Tenha em atenção que os instantâneos locais de volumes localmente afixados podem separar também para a cloud e mantenha-se no escalão local do dispositivo não são garantidos.

**P.** Existem diretrizes disponíveis para gerir os instantâneos locais para volumes afixados localmente?

**R.** Os instantâneos locais frequentes juntamente com uma alta taxa de alterações a dados do volume afixado localmente podem causar o espaço local no dispositivo seja consumido rapidamente e resultar em dados a partir de volumes em camadas que está sendo transmitidos para a cloud. Sugerimos, portanto, que estará a minimizar o número de instantâneos locais.

**P.** Recebi um alerta a indicar que o meu instantâneos locais de volumes localmente afixados podem ser invalidados. Quando pode isso acontece?

**R.** Os instantâneos locais frequentes juntamente com uma alta taxa de alterações a dados do volume afixado localmente podem causar o espaço local no dispositivo seja consumido rapidamente. Se as camadas de locais do dispositivo mais utilizadas, uma indisponibilidade de cloud expandida poderá resultar num dispositivo fiquem cheios e entradas escritas no volume poderão resultar em invalidação dos instantâneos (como não existe espaço existe para atualizar os instantâneos referir-se para os blocos mais antigos do dados que foram substituídos). Em tal situação as escritas para o volume irão continuar a ser atendidos, mas os instantâneos locais poderão ser inválidos. Não há nenhum impacto sobre a sua instantâneos de cloud existente.

É o aviso de alerta para notificá-lo que tal situação pode surgir e certifique-se de que mesmo endereço em tempo hábil ao rever as agendas de instantâneos locais para tirar instantâneos locais menos frequentes ou eliminar os instantâneos locais mais antigos que não são mais necessários.

Se os instantâneos locais são também invalidados, receberá um alerta de informação notificá-lo de que os instantâneos locais para a política de cópia de segurança específica foram invalidados juntamente com a lista de carimbos dos instantâneos locais que foram invalidados. Estes instantâneos serão automaticamente eliminados e não poderá vê-las na **catálogos de cópia de segurança** painel no portal do Azure.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Perguntas sobre como converter um volume em camadas num volume afixado localmente
**P.** Estou a observar alguns lentidão no dispositivo durante a conversão de um volume em camadas para um volume afixado localmente. Por que motivo é que isto acontece?

**R.** O processo de conversão envolve dois passos:

1. Aprovisionamento de espaço no dispositivo para o em breve-para--convertido localmente afixado de volume.
2. Baixar todos os dados em camadas a partir da cloud para se certificar de local garante.

As duas etapas são longas executar operações de dependentes do tamanho do volume que está a ser convertido, os dados no dispositivo e a largura de banda disponível. Como alguns dados a partir de volumes em camadas existentes poderão transbordam para a nuvem como parte do processo de aprovisionamento, o seu dispositivo poderá detetar um desempenho reduzido durante este período. Além disso, o processo de conversão pode ser mais lento se:

* Volumes existentes não tem sido submetidos a backup na cloud. portanto, sugerimos que seus volumes antes de iniciar uma conversão de cópia de segurança.
* Foram aplicadas políticas de limitação de largura de banda, que pode restringir a largura de banda disponível na cloud. Recomendamos, por isso, que tem um Mbps de 40 dedicado ou ligação mais para a cloud.
* O processo de conversão pode demorar várias horas devido aos vários fatores explicados acima; Por conseguinte, sugerimos que efetue esta operação durante as horas de não-picos ou no final de semana para evitar o impacto sobre consumidores finais.

Obter mais informações sobre como [converter um volume em camadas para um volume afixado localmente](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**P.** Pode cancelar a operação de conversão do volume?

**R.** Não, não é possível a cancelar a operação de conversão depois de iniciada. Como discutido na pergunta anterior, lembre os potenciais problemas de desempenho que poderá encontrar durante o processo e siga as melhores práticas indicadas acima ao planejar sua conversão.

**P.** O que acontece ao meu volume se a operação de conversão falhar?

**R.** Conversão de volume pode falhar devido a problemas de conectividade de cloud. O dispositivo, por fim, pode parar o processo de conversão depois de uma série de tentativas mal sucedidas de trazer dados em camadas a partir da cloud. Neste cenário, o tipo de volume irá continuar a ser o tipo de volume de origem antes da conversão, e:

* Será desencadeado um alerta crítico para notificá-lo sobre a falha na conversão do volume. Obter mais informações sobre [alertas relacionados com os volumes afixados localmente](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Se estiver a converter um em camadas para um volume localmente afixado, o volume irá continuar a apresentar as propriedades de um volume em camadas, como dados ainda podem residir na cloud. Sugerimos que resolva os problemas de conectividade e, em seguida, repita a operação de conversão.
* Da mesma forma, quando a conversão de um afixado localmente num volume em camadas falha, embora o volume será marcado como um volume localmente afixado, que irá funcionar como um volume em camadas (uma vez que poderiam ter derramou dados para a cloud). No entanto, irá continuar a ocupar espaço nas camadas locais do dispositivo. Este espaço não estará disponível para outros volumes afixados localmente. Sugerimos que repita esta operação para garantir que a conversão do volume for concluída e pode ser recuperado o espaço local no dispositivo.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Perguntas sobre o restauro de um volume afixado localmente
**P.** São restaurados instantaneamente os volumes afixados localmente?

**R.** Sim, os volumes afixados localmente são restaurados instantaneamente. Assim que as informações de metadados para o volume são obtidas a partir da cloud, como parte da operação de restauro, o volume é colocado online e pode ser acedido pelo anfitrião. No entanto, garantias locais para o volume de dados não estará presentes até que todos os dados já foi baixado a partir da cloud e pode vir a ter um desempenho reduzido nestes volumes durante o restauro.

**P.** Quanto tempo demora a restaurar um volume localmente afixado?

**R.** Os volumes afixados localmente são restaurados instantaneamente e colocados online, assim como as informações de metadados do volume são obtidas a partir da cloud, enquanto os dados de volume continuam a ser transferido em segundo plano. Esta última parte da operação de restauro, voltando as garantias locais para os dados de volume – é uma operação de longa execução e pode demorar várias horas para todos os dados devem se transformar em local novamente. O tempo necessário para concluir o mesmo depende de vários fatores, como o tamanho do volume a ser restaurado e a largura de banda disponível. Se o volume original que está a ser restaurado tiver sido eliminado, mais tempo será conduzido para criar o espaço local no dispositivo como parte da operação de restauro.

**P.** Eu preciso restaurar meu volume afixado localmente existente para um instantâneo mais antigo (executado quando o volume foi em camadas). O volume vai ser restaurado como camadas em vez disso?

**R.** Não, o volume será restaurado como um volume afixado localmente. Embora as datas de instantâneos para a hora quando o volume foi em camadas, ao restaurar os volumes existentes, o StorSimple utiliza sempre o tipo de volume no disco tal como existe atualmente.

**P.** Recentemente estendi meu volume afixado localmente, mas preciso agora restaurar os dados para um tempo quando o volume foi menor de tamanho. Restauro redimensionará volume atual e será necessário expandir o tamanho do volume depois do restauro estiver terminado?

**R.** Sim, o restauro será redimensionado o volume, e precisará de expandir o tamanho do volume após o restauro estiver terminado.

**P.** Pode alterar o tipo de um volume durante o restauro?

**R.** Não, não é possível alterar o tipo de volume durante o restauro.

* Os volumes que tiverem sido eliminados são restaurados como o tipo armazenado no instantâneo.
* Os volumes existentes são restaurados com base em seu tipo atual, independentemente do tipo armazenado no instantâneo (consulte as duas perguntas anteriores).

**P.** Preciso restaurar meu volume afixado localmente, mas escolhi um ponto de incorreto no instantâneo de tempo. Pode cancelar a operação de restauro atual?

**R.** Sim, pode cancelar uma operação de restauro em curso. O estado do volume será revertido para o estado no início da restauração. No entanto, quaisquer gravações que foram feitas para o volume enquanto o restauro estava em curso serão perdidas.

**P.** Comecei a uma operação de restauro em um dos meus volumes afixados localmente e, agora posso ver um instantâneo no catálogo minha lista de pendências que eu não recollect criar. É isso usado para quê?

**R.** Este é o instantâneo temporário que é criado antes da operação de restauro e é utilizado para reversão em caso do restauro foi cancelado ou falha. Não elimine este instantâneo; ele serão automaticamente eliminado quando o restauro estiver concluído. Esse comportamento pode ocorrer se a tarefa de restauro tem apenas localmente afixado volumes ou uma combinação de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, em seguida, esse comportamento não ocorrerá.

**P.** Pode clonar um volume localmente afixado?

**R.** Sim, pode. No entanto, será possível clonar o volume afixado localmente como um volume em camadas por predefinição. Obter mais informações sobre como [clonar um volume afixado localmente](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Perguntas sobre a ativação pós-falha de um volume afixado localmente
**P.** Eu preciso fazer a ativação pós-falha meu dispositivo para outro dispositivo físico. Os meus volumes localmente afixados falha mais à medida que afixado localmente ou em camadas?

**R.** Os volumes afixados localmente com ativação pós-falha como localmente afixado se o dispositivo de destino estiver a executar atualização de série StorSimple 8000 3 ou superior.

Obter mais informações sobre [ativação pós-falha e DR de volumes afixados localmente em todas as versões](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**P.** Os volumes afixados localmente são restaurados instantaneamente durante a recuperação após desastre (DR)?

**R.** Sim, os volumes afixados localmente são restaurados instantaneamente durante a ativação pós-falha. Assim que as informações de metadados para o volume são obtidas a partir da cloud, como parte da operação de ativação pós-falha, o volume é colocado online no dispositivo de destino e pode ser acedido pelo anfitrião. Enquanto isso, os dados de volume irão continuar a transferir em segundo plano, e poderá ter desempenho reduzido nestes volumes durante a ativação pós-falha.

**P.** Posso ver a tarefa de ativação pós-falha concluída, como posso controlar o progresso do volume afixado localmente que está a ser restaurada no dispositivo de destino?

**R.** Durante uma operação de ativação pós-falha, a tarefa de ativação pós-falha está marcada como executar uma vez a todos os volumes no conjunto de ativação pós-falha foram instantaneamente restaurados e colocados online no dispositivo de destino. Isto inclui todos os volumes afixados localmente que poderão ter falhados; No entanto, garantias locais dos dados só estará disponíveis quando todos os dados para o volume tiver sido baixado. Pode controlar esse progresso de cada volume afixado localmente que foi ativada com pós-falha pelo monitorização as tarefas de restauro correspondente, que são criadas como parte da ativação pós-falha. Estas tarefas de restauro individual só serão criadas para volumes afixados localmente.

**P.** Pode alterar o tipo de um volume durante a ativação pós-falha?

**R.** Não, não é possível alterar o tipo de volume durante uma ativação pós-falha. Se estão a falhar ao longo para outro dispositivo físico que está a executar o StorSimple 8000 series a atualização 3, os volumes com ativação pós-falha com base no tipo de volume armazenado no instantâneo.

**P.** Pode falhar ao longo de um contentor de volumes com os volumes afixados localmente para a aplicação da cloud?

**R.** Sim, pode. Os volumes afixados localmente vão efetuar a ativação pós-falha como volumes em camadas. Obter mais informações sobre [ativação pós-falha e DR de volumes afixados localmente em todas as versões](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

