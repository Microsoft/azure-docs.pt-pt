---
title: StorSimple localmente fixado volumes FAQ/ Microsoft Docs
description: Fornece respostas a perguntas frequentes sobre volumes fixados localmente StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60319552"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>Volumes storSimple fixados localmente: perguntas frequentes (FAQ)
## <a name="overview"></a>Descrição geral
Seguem-se perguntas e respostas que poderá ter quando criar um volume storSimple fixado localmente, converter um volume hierárquico num volume fixado localmente (e vice-versa), ou fazer recuar e restaurar um volume fixado localmente.

Perguntas e respostas são organizadas nas seguintes categorias

* Criando um volume localmente fixado
* Apoiando um preso localmente
* Converter um volume hierárquico num volume fixado localmente
* Restaurar um volume localmente fixado
* Falhando sobre um volume localmente fixado

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Perguntas sobre a criação de um volume localmente fixado
**P.** Qual é o tamanho máximo de um volume fixado localmente que posso criar nos dispositivos da série 8000?

**A** Em dispositivos em execução StorSimple 8000 Series Update 3.0, pode fornecer volumes fixados localmente até 8,5 TB ou volumes hierárquicos até 200 TB no dispositivo 8100. No dispositivo 8600 maior, pode aprovisionar volumes localmente afixados até 22,5 TB ou volumes em camadas até 500 TB.

**P.** Atualizei recentemente o meu dispositivo 8100 para o Update 3.0 e quando tento criar um volume localmente fixado, o tamanho máximo disponível é de apenas 6 TB e não 8,5 TB. Por que não posso criar um volume de 8,5 TB?

**A** Se o seu dispositivo estiver a ser atualizado 3.0, pode fornecer volumes fixados localmente até 8,5 TB OU volumes nihosos até 200 TB no dispositivo 8100. Se o seu dispositivo já tiver volumes hierárquicos, então o espaço disponível para criar um volume fixado localmente será proporcionalmente inferior a este limite máximo. Por exemplo, se já foram aprovisionados cerca de 106 TB de volumes hierárquicos no seu dispositivo 8100 (que é metade da capacidade hierárquica), então o tamanho máximo de um volume local que pode criar no dispositivo 8100 será correspondentemente reduzido a 4 TB (aproximadamente metade da capacidade máxima de volume fixada localmente).

Como algum espaço local no dispositivo é usado para alojar o conjunto de volumes hierárquicos, o espaço disponível para criar um volume fixado localmente é reduzido se o dispositivo tiver volumes hierárquicos. Inversamente, a criação de um volume fixado localmente reduz proporcionalmente o espaço disponível para volumes hierárquicos. As tabelas seguintes resumem a capacidade hierárquica disponível nos dispositivos 8100 e 8600 quando são criados volumes fixados localmente.

#### <a name="update-30"></a>Atualização 3.0 

| Capacidade aprovisionada de volumes locais | Capacidade disponível a fornecer para volumes hierárquicos - 8100 | Capacidade disponível a fornecer para volumes hierárquicos - 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8,5 TB |0 TB |311.1 TB |
| 10 TB |ND |277.8 TB |
| 15 TB |ND |166.7 TB |
| 22,5 TB |ND |0 TB |

**P.** Porque é que a criação de volume local mente é uma operação de longa duração?

**A.** Os volumes fixados localmente são densamente provisionados. Para criar espaço nos níveis locais do dispositivo, alguns dados provenientes de volumes hierárquicos existentes podem ser empurrados para a nuvem durante o processo de provisionamento. E uma vez que isso depende do tamanho do volume que está a ser aprovisionado, os dados existentes no seu dispositivo e a largura de banda disponível para a nuvem, o tempo de vida para criar um volume local pode ser de várias horas.

**P.** Quanto tempo demora a criar um volume local?

**A.** Uma vez que os volumes fixados localmente são densamente provisionados, alguns dados existentes a partir de volumes hierárquicos podem ser empurrados para a nuvem durante o processo de provisionamento. Portanto, o tempo necessário para criar um volume fixado localmente depende de múltiplos fatores, incluindo o tamanho do volume, os dados do seu dispositivo e a largura de banda disponível. Num dispositivo recém-instalado que não tem volumes, o tempo para criar um volume localmente fixado é de cerca de 10 minutos por terabyte de dados. No entanto, a criação de volumes locais pode demorar várias horas com base nos fatores acima explicados num dispositivo que está em uso.

**P.** Quero criar um volume localmente fixado. Há algumas boas práticas que eu precise de ter conhecimento?

**A.** Os volumes fixados localmente são adequados para cargas de trabalho que requerem garantias locais de dados em todos os momentos e são sensíveis às tardios em nuvem. Ao considerar a utilização de volumes locais para qualquer uma das suas cargas de trabalho, tenha em atenção o seguinte:

* Os volumes fixados localmente são densamente provisionados, e a criação de volumes locais impacta o espaço disponível para volumes hierárquicos. Por isso, sugerimos que comece com volumes de menor dimensão e aumente à medida que o seu requisito de armazenamento aumenta.
* O fornecimento de volumes locais é uma operação de longo prazo que pode implicar a pressão dos dados existentes de volumes hierárquicos para a nuvem. Como resultado, poderá experimentar um desempenho reduzido nestes volumes.
* O fornecimento de volumes locais é uma operação morosa. O tempo real envolvido depende de múltiplos fatores: a dimensão do volume a ser aprovisionado, os dados do seu dispositivo e a largura de banda disponível. Se não tiver apoiado os volumes existentes para a nuvem, então a criação de volume é mais lenta. Sugerimos que tire fotos em nuvem dos seus volumes existentes antes de fornecer um volume local.
* Pode converter volumes hierárquicos existentes em volumes fixados localmente, e esta conversão envolve o fornecimento de espaço no dispositivo para o volume fixado localmente resultante (além de retirar dados hierárquicos, se houver, da nuvem). Mais uma vez, esta é uma operação de longa duração que depende dos fatores que discutimos acima. Sugerimos que recue os volumes existentes antes da conversão, uma vez que o processo será ainda mais lento se os volumes existentes não forem apoiados. O seu dispositivo também pode experimentar um desempenho reduzido durante este processo.

Mais informações sobre como [criar um volume localmente fixado](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**P.** Posso criar vários volumes localmente presos ao mesmo tempo?

**A.** Sim, mas quaisquer trabalhos locais de criação de volume e expansão são processados sequencialmente.

Os volumes fixados localmente são densamente provisionados e isso requer a criação de espaço local no dispositivo (o que pode resultar em dados existentes a partir de volumes hierárquicos a serem empurrados para a nuvem durante o processo de provisionamento). Por conseguinte, se houver um emprego de provisão, outros postos de trabalho locais de criação de volume serão postos em fila até que esse emprego esteja concluído.

Da mesma forma, se um volume local existente está a ser expandido ou se um volume hierárquico estiver a ser convertido para um volume fixado localmente, então a criação de um novo volume local é feita em fila até que o trabalho anterior esteja concluído. Expandir o tamanho de um volume localmente fixado envolve a expansão do espaço local existente para esse volume. A conversão de um volume nitente para local mente também envolve a criação de espaço local para o volume local resultante. Em ambas as operações, a criação ou expansão do espaço local é um trabalho de longa duração.

Pode ver estes trabalhos na lâmina **Jobs** do serviço StorSimple Device Manager. O trabalho que está a ser processado ativamente é continuamente atualizado para refletir o progresso do fornecimento de espaço. Os restantes postos de trabalho de volume local mentem marcados como funcionando, mas os seus progressos estão parados e são escolhidos na ordem em que foram em fila.

**P.** Apaguei um volume localmente fixado. Por que não vejo o espaço recuperado refletido no espaço disponível quando tento criar um novo volume?

**A.** Se eliminar um volume fixado localmente, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O Serviço de Gestor de Dispositivos StorSimple atualiza o espaço local disponível aproximadamente a cada hora. Sugerimos que espere uma hora antes de tentar criar o novo volume.

**P.** Os volumes fixados localmente são suportados no aparelho de nuvem?

**A.** Os volumes fixados localmente não são suportados no aparelho em nuvem (8010 e 8020 dispositivos anteriormente referidos como dispositivo virtual StorSimple).

**P.** Posso usar os cmdlets Azure PowerShell para criar e gerir volumes locais?

**A.** Não, não é possível criar volumes fixados localmente através de cmdlets Azure PowerShell (qualquer volume que crie via Azure PowerShell é hierárquico). Sugerimos também que não utilize os cmdlets Azure PowerShell para modificar quaisquer propriedades de um volume fixado localmente, uma vez que terá o efeito indesejado de modificar o tipo de volume para o nível.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Perguntas sobre o backup de um volume localmente fixado
**P.** As imagens locais de volumes locais são suportadas?

**A.** Sim, pode tirar fotos locais dos seus volumes locais. No entanto, sugerimos fortemente que você recue regularmente os seus volumes com imagens de nuvem local para garantir que os seus dados estão protegidos na eventualidade de um desastre.

Note que as imagens locais de volumes fixados localmente também podem ser nividas para a nuvem e não estão garantidas para permanecer no nível local do dispositivo.

**P.** Existem algumas diretrizes para gerir instantâneos locais para volumes locais?

**A.** Instantâneos locais frequentes ao lado de uma alta taxa de dados no volume fixado localmente podem fazer com que o espaço local no dispositivo seja consumido rapidamente e resultar em dados de volumes hierárquicos sendo empurrados para a nuvem. Por isso, sugerimos que minimize o número de instantâneos locais.

**P.** Recebi um alerta a dizer que as minhas fotos locais de volumes locais podem ser invalidadas. Quando é que isto pode acontecer?

**A.** Instantâneos locais frequentes ao lado de uma alta taxa de dados no volume fixado localmente podem fazer com que o espaço local no dispositivo seja consumido rapidamente. Se os níveis locais do dispositivo forem fortemente utilizados, uma paragem prolongada da nuvem pode resultar na formação do dispositivo, e a entrada de escritos para o volume pode resultar na invalidação dos instantâneos (uma vez que não existe espaço para atualizar os instantâneos para se referir aos blocos mais antigos de dados que foram substituídos). Numa situação destas, os escritos ao volume continuarão a ser servidos, mas os instantâneos locais podem ser inválidos. Não há impacto nas suas imagens de nuvens existentes.

O alerta é para notificá-lo de que tal situação pode surgir e garantir que você aborda o mesmo atempadamente, ou analisando os seus horários locais para tirar fotos locais menos frequentes ou apagar fotos locais mais antigas que já não são necessárias.

Se as imagens locais forem invalidadas, receberá um alerta de informação informando-o de que as imagens locais para a política de backup específica foram invalidadas juntamente com a lista de selos temporais dos instantâneos locais que foram invalidados. Estas imagens serão eliminadas automaticamente e deixará de poder vê-las na lâmina **dos Catálogos de Backup** no portal Azure.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Perguntas sobre a conversão de um volume hierárquico para um volume localmente fixado
**P.** Estou a observar alguma lentidão no dispositivo enquanto converto um volume hierárquico num volume local. Porque é que isto está a acontecer?

**A.** O processo de conversão envolve duas etapas:

1. Fornecimento de espaço no dispositivo para o volume local em breve convertido.
2. Descarregar quaisquer dados hierárquicos da nuvem para garantir garantias locais.

Ambos os passos são operações de longo funcionamento que dependem do tamanho do volume que está a ser convertido, dados sobre o dispositivo e largura de banda disponível. Como alguns dados de volumes tiered existentes podem derramar para a nuvem como parte do processo de provisionamento, o seu dispositivo pode experimentar um desempenho reduzido durante este tempo. Além disso, o processo de conversão pode ser mais lento se:

* Os volumes existentes não foram apoiados até à nuvem; por isso sugerimos que faça backup dos seus volumes antes de dar início a uma conversão.
* Foram aplicadas políticas de estrangulamento da largura de banda, o que pode limitar a largura de banda disponível à nuvem; recomendamos, portanto, que tenha uma ligação dedicada de 40 Mbps ou mais à nuvem.
* O processo de conversão pode demorar várias horas devido aos múltiplos fatores acima explicados; por conseguinte, sugerimos que realize esta operação em tempos não picos ou num fim de semana para evitar o impacto nos consumidores finais.

Mais informações sobre como [converter um volume hierárquico num volume localmente fixado](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**P.** Posso cancelar a operação de conversão de volume?

**A.** Não, não pode cancelar a operação de conversão uma vez iniciada. Conforme discutido na pergunta anterior, esteja ciente dos potenciais problemas de desempenho que poderá encontrar durante o processo e siga as melhores práticas acima listadas quando planeia a sua conversão.

**P.** O que acontece ao meu volume se a operação de conversão falhar?

**A.** A conversão de volume pode falhar devido a problemas de conectividade na nuvem. O dispositivo pode eventualmente parar o processo de conversão após uma série de tentativas falhadas de derrubar dados hierárquicos da nuvem. Neste cenário, o tipo de volume continuará a ser o tipo de volume de origem antes da conversão e:

* Será levantado um alerta crítico para notificá-lo da falha de conversão de volume. Mais informações sobre [alertas relacionados com volumes locais fixados](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Se estiver a converter um volume nidível localmente, o volume continuará a exibir propriedades de um volume hierárquico, uma vez que os dados ainda podem residir na nuvem. Sugerimos que resolva os problemas de conectividade e, em seguida, retente a operação de conversão.
* Da mesma forma, quando a conversão de um local fixado para um volume hierárquico falha, embora o volume seja marcado como um volume fixado localmente, funcionará como um volume hierárquico (porque os dados poderiam ter derramado para a nuvem). No entanto, continuará a ocupar espaço nas camadas locais do dispositivo. Este espaço não estará disponível para outros volumes locais. Sugerimos que retente esta operação para garantir que a conversão de volume está completa e que o espaço local no dispositivo pode ser recuperado.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Perguntas sobre restaurar um volume localmente fixado
**P.** Os volumes fixados localmente são restaurados instantaneamente?

**A.** Sim, os volumes fixados localmente são restaurados instantaneamente. Assim que a informação dos metadados para o volume é retirada da nuvem como parte da operação de restauro, o volume é trazido on-line e pode ser acedido pelo anfitrião. No entanto, as garantias locais para os dados de volume não estarão presentes até que todos os dados tenham sido descarregados da nuvem, podendo experimentar um desempenho reduzido nestes volumes durante a duração da restauração.

**P.** Quanto tempo demora a restaurar um volume local?

**A.** Os volumes fixados localmente são restaurados instantaneamente e trazidos on-line assim que a informação de metadados de volume é recuperada da nuvem, enquanto os dados de volume continuam a ser descarregados em segundo plano. Esta última parte da operação de restauro -- recuperar as garantias locais para os dados de volume -- é uma operação de longo curso e pode demorar várias horas para que todos os dados sejam novamente feitos locais. O tempo necessário para completar o mesmo depende de múltiplos fatores, tais como o tamanho do volume que está a ser restaurado e a largura de banda disponível. Se o volume original que está a ser restaurado tiver sido eliminado, será necessário tempo adicional para criar o espaço local no dispositivo como parte da operação de restauro.

**P.** Preciso restaurar o meu volume localmente fixado para um instantâneo mais antigo (tirado quando o volume foi niveado). Será que o volume será restaurado como niverizado neste caso?

**A.** Não, o volume será restaurado como um volume localmente fixado. Embora o instantâneo data do momento em que o volume foi nidificado, enquanto restaura os volumes existentes, o StorSimple usa sempre o tipo de volume no disco tal como existe atualmente.

**P.** Estendei o meu volume localmente fixado recentemente, mas agora preciso de restaurar os dados numa altura em que o volume era menor em tamanho. Irá restaurar a redimensionação do volume atual e terei de alargar o tamanho do volume assim que a restauração estiver concluída?

**A.** Sim, o restauro irá redimensionar o volume, e você precisará estender o tamanho do volume após a restauração ser concluída.

**P.** Posso alterar o tipo de volume durante a restauração?

**A.** Não, não pode alterar o tipo de volume durante a restauração.

* Os volumes que foram eliminados são restaurados como o tipo armazenado no instantâneo.
* Os volumes existentes são restaurados com base no seu tipo atual, independentemente do tipo armazenado no instantâneo (consulte as duas perguntas anteriores).

**P.** Preciso restaurar o meu volume local, mas escolhi um ponto incorreto no instantâneo do tempo. Posso cancelar a operação de restauro atual?

**A.** Sim, pode cancelar uma operação de restauro em curso. O estado do volume será revirado para o estado no início do restauro. No entanto, quaisquer escritos que tenham sido feitos ao volume enquanto o restauro estava em curso serão perdidos.

**P.** Iniciei uma operação de restauro num dos meus volumes locais, e agora vejo uma foto no meu catálogo de atrasos que não me lembro de ter criado. Para que é usado?

**A.** Este é o instantâneo temporário que é criado antes da operação de restauro e é usado para reversão no caso de o restauro ser cancelado ou falhar. Não apague este instantâneo; será automaticamente eliminado quando a restauração estiver concluída. Este comportamento pode ocorrer se o seu trabalho de restauro tiver apenas volumes fixados localmente ou uma mistura de volumes fixados localmente e hierárquicos. Se o trabalho de restauro incluir apenas volumes hierárquicos, então este comportamento não ocorrerá.

**P.** Posso clonar um volume local?

**A.** Sim, pode. No entanto, o volume fixado localmente será clonado como um volume hierárquico por padrão. Mais informações sobre como [clonar um volume localmente fixado](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Perguntas sobre falhar sobre um volume localmente fixado
**P.** Preciso de falhar o meu dispositivo para outro dispositivo físico. Será que os meus volumes locais serão falhados como fixados localmente ou nivilhados?

**A.** Os volumes fixados localmente são falhados como fixado localmente se o dispositivo alvo estiver executando storSimple 8000 série atualização 3 ou superior.

Mais informações sobre [failover e DR de volumes locais presos em versões](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**P.** Os volumes fixados localmente são restaurados instantaneamente durante a recuperação de desastres (DR)?

**A.** Sim, os volumes fixados localmente são restaurados instantaneamente durante a falha. Assim que as informações de metadados para o volume forem retiradas da nuvem como parte da operação de failover, o volume é colocado online no dispositivo-alvo e pode ser acedido pelo anfitrião. Entretanto, os dados de volume continuarão a ser descarregados em segundo plano, podendo experimentar um desempenho reduzido nestes volumes durante a duração da falha.

**P.** Vejo o trabalho de failover concluído, como posso acompanhar o progresso do volume local mente fixado que está a ser restaurado no dispositivo-alvo?

**A.** Durante uma operação de failover, o trabalho de failover é marcado como completo uma vez que todos os volumes do conjunto de failover foram restaurados instantaneamente e introduzidos on-line no dispositivo alvo. Isto inclui quaisquer volumes fixados localmente que possam ter sido falhados; no entanto, as garantias locais dos dados só estarão disponíveis quando todos os dados relativos ao volume forem descarregados. Pode acompanhar este progresso para cada volume fixado localmente que foi falhado através da monitorização dos postos de trabalho de restauro correspondentes que são criados como parte da falha. Estes postos de trabalho individuais só serão criados para volumes fixados localmente.

**P.** Posso alterar o tipo de volume durante a falha?

**A.** Não, não pode alterar o tipo de volume durante uma falha. Se estiver a falhar noutro dispositivo físico que está a executar a atualização da série StorSimple 8000 3, os volumes são falhados com base no tipo de volume armazenado no instantâneo.

**P.** Posso falhar sobre um contentor de volume com volumes fixados localmente no aparelho de nuvem?

**A.** Sim, pode. Os volumes fixados localmente serão falhados à medida que os volumes nievantes serão nividados. Mais informações sobre [failover e DR de volumes locais presos em versões](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

