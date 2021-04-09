---
title: StorSimple volumes fixados localmente FAQ| Microsoft Docs
description: Fornece respostas a perguntas frequentes sobre volumes fixados localmente pela StorSimple.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: 483fa81b409e1bd740af85b431a86b6c814831e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002719"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple volumes fixados localmente: perguntas frequentes (FAQ)
## <a name="overview"></a>Descrição Geral
Seguem-se perguntas e respostas que poderá ter quando criar um volume fixado localmente, converter um volume hierárquico num volume fixado localmente (e vice-versa), ou recuar e restaurar um volume fixado localmente.

Perguntas e respostas são organizadas nas seguintes categorias

* Criar um volume fixado localmente
* Apoiar um localmente preso
* Conversão de um volume hierárquico para um volume fixado localmente
* Restaurar um volume fixado localmente
* Falhando sobre um volume fixado localmente

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Perguntas sobre a criação de um volume fixado localmente
**Q.** Qual é o tamanho máximo de um volume fixado localmente que posso criar nos dispositivos da série 8000?

**A** Nos dispositivos que executam StorSimple 8000 Series Update 3.0, pode providenciar volumes fixados localmente até 8,5 TB ou volumes hierárquicos até 200 TB no dispositivo 8100. No dispositivo 8600 maior, pode aprovisionar volumes localmente afixados até 22,5 TB ou volumes em camadas até 500 TB.

**Q.** Atualizei recentemente o meu dispositivo 8100 para Update 3.0 e quando tento criar um volume fixado localmente, o tamanho máximo disponível é de apenas 6 TB e não 8.5 TB. Por que não posso criar um volume de 8,5 TB?

**A** Se o seu dispositivo estiver a executar a atualização 3.0, pode prever volumes fixados localmente até 8,5 volumes de TB OU tiered até 200 TB no dispositivo 8100. Se o seu dispositivo já tiver volumes hierarquizados, então o espaço disponível para a criação de um volume fixado localmente será proporcionalmente inferior a este limite máximo. Por exemplo, se já foram abastetados cerca de 106 TB de volumes hierárquicos no seu dispositivo 8100 (que é metade da capacidade hierárquica), então o tamanho máximo de um volume local que se pode criar no dispositivo 8100 será correspondentemente reduzido a 4 TB (cerca de metade da capacidade máxima de volume fixado localmente).

Uma vez que algum espaço local no dispositivo é utilizado para hospedar o conjunto de trabalho de volumes hierárquicos, o espaço disponível para a criação de um volume fixado localmente é reduzido se o dispositivo tiver volumes hierarquizados. Inversamente, a criação de um volume fixado localmente reduz proporcionalmente o espaço disponível para volumes hierárquicos. As tabelas que se seguem resumem a capacidade hierárquica disponível nos dispositivos 8100 e 8600 quando são criados volumes fixados localmente.

#### <a name="update-30"></a>Atualização 3.0 

| Volumes fixados localmente alocando capacidade | Capacidade disponível a a provisionar para volumes hierárquicos - 8100 | Capacidade disponível a a provisionar para volumes hierárquicos - 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8.5 TB |0 TB |311.1 TB |
| 10 TB |ND |277.8 TB |
| 15 TB |ND |166.7 TB |
| 22.5 TB |ND |0 TB |

**Q.** Porque é que a criação de volume fixo local é uma operação de longa duração?

**A.** Os volumes fixados localmente são densamente a provisionados. Para criar espaço nos níveis locais do dispositivo, alguns dados dos volumes hierárquicos existentes podem ser empurrados para a nuvem durante o processo de provisionamento. E uma vez que isso depende do tamanho do volume a ser a provisionado, os dados existentes no seu dispositivo e a largura de banda disponível para a nuvem, o tempo necessário para criar um volume local pode ser de várias horas.

**Q.** Quanto tempo demora a criar um volume fixado localmente?

**A.** Como os volumes fixados localmente são densamente a provisionados, alguns dados existentes de volumes hierárquicos podem ser empurrados para a nuvem durante o processo de provisionamento. Portanto, o tempo necessário para criar um volume fixado localmente depende de múltiplos fatores, incluindo o tamanho do volume, os dados do seu dispositivo e a largura de banda disponível. Num dispositivo recém-instalado que não tem volumes, o tempo para criar um volume fixado localmente é de cerca de 10 minutos por terabyte de dados. No entanto, a criação de volumes locais pode demorar várias horas com base nos fatores acima explicados num dispositivo que está em uso.

**Q.** Quero criar um volume localmente fixado. Há algumas boas práticas que preciso de ter em conta?

**A.** Os volumes fixados localmente são adequados para cargas de trabalho que requerem garantias locais de dados em todos os momentos e são sensíveis às latências em nuvem. Ao considerar a utilização de volumes locais para qualquer uma das suas cargas de trabalho, esteja ciente do seguinte:

* Os volumes fixados localmente são densamente a provisionados, e a criação de volumes locais impacta o espaço disponível para volumes hierárquicos. Por isso, sugerimos que comece com volumes de menor dimensão e aumente à medida que o seu requisito de armazenamento aumenta.
* O fornecimento de volumes locais é uma operação de longa duração que pode envolver a pressão dos dados existentes de volumes hierárquicos para a nuvem. Como resultado, poderá experimentar um desempenho reduzido nestes volumes.
* O fornecimento de volumes locais é uma operação morosa. O tempo em causa depende de múltiplos fatores: o tamanho do volume a ser a provisionado, os dados no seu dispositivo e a largura de banda disponível. Se não tiver apoiado os seus volumes existentes para a nuvem, então a criação de volume é mais lenta. Sugerimos que tire fotos em nuvem dos seus volumes existentes antes de providenciar um volume local.
* Pode converter volumes hierárquicos existentes em volumes fixados localmente, e esta conversão envolve o fornecimento de espaço no dispositivo para o volume fixado localmente (além de retirar dados hierárquicos, se houver, da nuvem). Mais uma vez, esta é uma operação de longa duração que depende de fatores que discutimos acima. Sugerimos que faça o back up dos seus volumes existentes antes da conversão, uma vez que o processo será ainda mais lento se os volumes existentes não forem apoiados. O seu dispositivo também pode experimentar um desempenho reduzido durante este processo.

Mais informações sobre como [criar um volume fixado localmente](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**Q.** Posso criar vários volumes fixados localmente ao mesmo tempo?

**A.** Sim, mas quaisquer empregos de criação e expansão de volumes fixados localmente são processados sequencialmente.

Os volumes fixados localmente são densamente a provisionados e isso requer a criação de espaço local no dispositivo (o que pode resultar em dados existentes a partir de volumes hierárquicos a serem empurrados para a nuvem durante o processo de provisionamento). Por conseguinte, se um emprego de provisionamento estiver em curso, outros postos de trabalho locais de criação de volume serão postos de trabalho na fila até que esse trabalho esteja concluído.

Do mesmo modo, se um volume local existente estiver a ser expandido ou se um volume hierárquico for convertido para um volume fixado localmente, então a criação de um novo volume fixado localmente é feita em fila até que o trabalho anterior esteja concluído. Expandir o tamanho de um volume fixado localmente envolve a expansão do espaço local existente para esse volume. A conversão de um volume hierárquico para um volume fixado local também envolve a criação de espaço local para o volume fixado localmente. Em ambas as operações, a criação ou expansão do espaço local é um trabalho de longa duração.

Pode ver estes trabalhos na lâmina **Jobs** do serviço StorSimple Device Manager. O trabalho que está a ser tratado ativamente é continuamente atualizado para refletir o progresso do provisionamento espacial. Os restantes postos de trabalho de volume fixados localmente são marcados como funcionando, mas os seus progressos estão parados e são escolhidos pela ordem em que foram postos em fila.

**Q.** Apaguei um volume fixado localmente. Por que não vejo o espaço recuperado refletido no espaço disponível quando tento criar um novo volume?

**A.** Se eliminar um volume fixado localmente, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O Serviço de Gestor de Dispositivos StorSimple atualiza o espaço local disponível aproximadamente a cada hora. Sugerimos que espere uma hora antes de tentar criar o novo volume.

**Q.** Os volumes fixados localmente são suportados no aparelho em nuvem?

**A.** Os volumes fixados localmente não são suportados no aparelho em nuvem (dispositivos 8010 e 8020 anteriormente referidos como dispositivo virtual StorSimple).

**Q.** Posso usar os cmdlets Azure PowerShell para criar e gerir volumes fixados localmente?

**A.** Não, não é possível criar volumes fixados localmente através de cmdlets Azure PowerShell (qualquer volume que crie através do Azure PowerShell é hierarquizado). Também sugerimos que não utilize os cmdlets Azure PowerShell para modificar quaisquer propriedades de um volume fixado localmente, uma vez que terá o efeito não utilizado de modificar o tipo de volume para tiered.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Perguntas sobre o backup de um volume fixado localmente
**Q.** São suportadas imagens locais de volumes fixados localmente?

**A.** Sim, pode tirar fotos locais dos seus volumes fixados localmente. No entanto, sugerimos fortemente que faça o back up regular dos seus volumes fixados localmente com instantâneos em nuvem para garantir que os seus dados estão protegidos na eventualidade de um desastre.

Note que as imagens locais de volumes fixados localmente também podem ser esvoacodas para a nuvem e não são garantidas para permanecer no nível local do dispositivo.

**Q.** Existem alguma orientação para a gestão de instantâneos locais para volumes fixados localmente?

**A.** Imagens locais frequentes ao lado de uma alta taxa de dados no volume fixado localmente podem fazer com que o espaço local no dispositivo seja consumido rapidamente e resulte em dados de volumes hierárquicos sendo empurrados para a nuvem. Por isso, sugerimos que minimize o número de instantâneos locais.

**Q.** Recebi um alerta a dizer que as minhas fotos locais de volumes fixados localmente podem ser invalidadas. Quando é que isto pode acontecer?

**A.** Imagens locais frequentes ao lado de uma alta taxa de dados no volume fixado localmente podem fazer com que o espaço local no dispositivo seja consumido rapidamente. Se os níveis locais do dispositivo forem fortemente utilizados, uma paragem prolongada da nuvem pode resultar na totalidade do dispositivo, e a entrada escreve para o volume pode resultar na invalidação dos instantâneos (uma vez que não existe espaço para atualizar as imagens para se referir aos blocos mais antigos de dados que foram substituídos). Em tal situação, os escritos para o volume continuarão a ser servidos, mas os instantâneos locais podem ser inválidos. Não há impacto nas suas fotos de nuvem existentes.

O aviso de alerta é para notificá-lo de que tal situação pode surgir e garantir que você aborda o mesmo em tempo oportuno, ou revendo os seus horários de instantâneos locais para tirar fotos locais menos frequentes ou eliminando fotos locais mais antigas que já não são necessárias.

Se as imagens locais forem invalidadas, receberá um alerta de informação informando-o de que as imagens locais para a política de backup específica foram invalidadas juntamente com a lista de timetamps dos instantâneos locais que foram invalidados. Estas imagens serão apagadas automaticamente e deixará de ser possível vê-las na lâmina **de Catálogos de Cópia de Segurança** no portal Azure.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Perguntas sobre a conversão de um volume hierárquico para um volume fixado localmente
**Q.** Estou a observar alguma lentidão no dispositivo enquanto converto um volume hierárquico num volume fixado localmente. Porque é que isto está a acontecer?

**A.** O processo de conversão envolve duas etapas:

1. Provisionamento de espaço no dispositivo para o volume fixado localmente em breve.
2. Descarregar quaisquer dados hierárquicos da nuvem para garantir garantias locais.

Ambos os passos são operações de longa duração que dependem do tamanho do volume que está a ser convertido, dados no dispositivo e largura de banda disponível. Como alguns dados de volumes hierárquicos existentes podem derramar para a nuvem como parte do processo de provisionamento, o seu dispositivo pode experimentar um desempenho reduzido durante este período. Além disso, o processo de conversão pode ser mais lento se:

* Os volumes existentes não foram apoiados até à nuvem; por isso sugerimos que faça backup dos seus volumes antes de iniciar uma conversão.
* Foram aplicadas políticas de estrangulamento de largura de banda, que podem restringir a largura de banda disponível à nuvem; por isso recomendamos que tenha uma ligação dedicada de 40 Mbps ou mais à nuvem.
* O processo de conversão pode demorar várias horas devido aos múltiplos fatores acima explicados; por conseguinte, sugerimos que realize esta operação em horários não-picos ou num fim de semana para evitar o impacto nos consumidores finais.

Mais informações sobre como [converter um volume hierárquico para um volume fixado localmente](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**Q.** Posso cancelar a operação de conversão de volume?

**A.** Não, não pode cancelar a operação de conversão uma vez iniciada. Como discutido na pergunta anterior, esteja ciente dos potenciais problemas de desempenho que poderá encontrar durante o processo e siga as melhores práticas acima enumeradas quando planeia a sua conversão.

**Q.** O que acontece ao meu volume se a operação de conversão falhar?

**A.** A conversão de volume pode falhar devido a problemas de conectividade na nuvem. O dispositivo pode eventualmente parar o processo de conversão após uma série de tentativas falhadas de retirar dados hierárquicos da nuvem. Neste cenário, o tipo de volume continuará a ser o tipo de volume de origem antes da conversão e:

* Será levantado um alerta crítico para notificá-lo da falha de conversão de volume. Mais informações sobre [alertas relacionados com volumes fixados localmente](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Se estiver a converter um hierarquizado para um volume fixado localmente, o volume continuará a exibir propriedades de um volume hierárquico, uma vez que os dados ainda podem residir na nuvem. Sugerimos que resolva os problemas de conectividade e, em seguida, relemisse a operação de conversão.
* Da mesma forma, quando a conversão de um volume fixado localmente para um volume hierarquizado falha, embora o volume seja marcado como um volume fixado localmente, funcionará como um volume hierárquico (porque os dados poderiam ter derramado para a nuvem). No entanto, continuará a ocupar espaço nos níveis locais do dispositivo. Este espaço não estará disponível para outros volumes fixados localmente. Sugerimos que redoça esta operação para garantir que a conversão de volume está completa e que o espaço local do dispositivo pode ser recuperado.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Perguntas sobre restaurar um volume fixado localmente
**Q.** Os volumes fixados localmente são restaurados instantaneamente?

**A.** Sim, os volumes fixados localmente são restaurados instantaneamente. Assim que a informação dos metadados para o volume é retirada da nuvem como parte da operação de restauro, o volume é trazido on-line e pode ser acedido pelo anfitrião. No entanto, as garantias locais para os dados de volume não estarão presentes até que todos os dados tenham sido descarregados da nuvem, e poderá experimentar um desempenho reduzido nestes volumes durante a duração da restauração.

**Q.** Quanto tempo demora a restaurar um volume fixado localmente?

**A.** Os volumes fixados localmente são restaurados instantaneamente e trazidos on-line assim que a informação de metadados de volume é recuperada da nuvem, enquanto os dados de volume continuam a ser descarregados em segundo plano. Esta última parte da operação de restauro-- recuperar as garantias locais para os dados de volume - é uma operação de longa duração e pode levar várias horas para que todos os dados sejam tornados locais novamente. O tempo necessário para completar o mesmo depende de múltiplos fatores, tais como o tamanho do volume a ser restaurado e a largura de banda disponível. Se o volume original que está a ser restaurado tiver sido eliminado, será necessário um tempo adicional para criar o espaço local no dispositivo como parte da operação de restauro.

**Q.** Preciso restaurar o meu volume fixado localmente para um instantâneo mais antigo (tirado quando o volume foi tiered). O volume será restaurado como hierarquizado neste caso?

**A.** Não, o volume será restaurado como um volume fixado localmente. Embora o instantâneo data do momento em que o volume foi tiered, enquanto restaura os volumes existentes, storSimple usa sempre o tipo de volume no disco tal como existe atualmente.

**Q.** Aumentei recentemente o meu volume fixado localmente, mas agora preciso de restaurar os dados para uma altura em que o volume era menor. Restabelecerá o tamanho atual e terei de aumentar o tamanho do volume assim que a restauração estiver concluída?

**A.** Sim, a restauração irá redimensionar o volume, e você precisará estender o tamanho do volume após a restauração ser concluída.

**Q.** Posso alterar o tipo de volume durante a restauração?

**A.** Não, não é possível alterar o tipo de volume durante a restauração.

* Os volumes que foram eliminados são restaurados como o tipo armazenado no instantâneo.
* Os volumes existentes são restaurados com base no seu tipo atual, independentemente do tipo armazenado no instantâneo (consulte as duas perguntas anteriores).

**Q.** Preciso restaurar o meu volume fixado localmente, mas escolhi um ponto incorreto no tempo. Posso cancelar a operação de restauro atual?

**A.** Sim, pode cancelar uma operação de restauro em curso. O estado do volume será devolvido ao estado no início da restauração. No entanto, quaisquer escritos que tenham sido feitos ao volume enquanto a restauração estava em curso serão perdidos.

**Q.** Iniciei uma operação de restauro num dos meus volumes fixados localmente, e agora vejo uma foto no meu catálogo de atrasos que não me lembro de ter criado. Para que é usado?

**A.** Esta é a imagem temporária que é criada antes da operação de restauro e é usada para reversão no caso de a restauração ser cancelada ou falha. Não elimine esta imagem; será automaticamente eliminado quando a restauração estiver completa. Este comportamento pode ocorrer se o seu trabalho de restauro tiver apenas volumes fixados localmente ou uma mistura de volumes fixados localmente e hierárquicos. Se o trabalho de restauro incluir apenas volumes hierárquicos, então este comportamento não ocorrerá.

**Q.** Posso clonar um volume fixado localmente?

**A.** Sim, pode. No entanto, o volume fixado localmente será clonado como um volume hierárquico por defeito. Mais informações sobre como [clonar um volume fixado localmente](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Perguntas sobre falhar sobre um volume fixado localmente
**Q.** Tenho de falhar o meu dispositivo para outro dispositivo físico. Os meus volumes fixados localmente serão falhados como fixados localmente ou nivelados?

**A.** Os volumes fixados localmente são falhados como fixados localmente se o dispositivo-alvo estiver a executar a atualização da série StorSimple 8000 3 ou superior.

Mais informações sobre [failover e DR de volumes fixados localmente em versões](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**Q.** Os volumes fixados localmente são restaurados instantaneamente durante a recuperação de desastres (DR)?

**A.** Sim, os volumes fixados localmente são restaurados instantaneamente durante o failover. Assim que a informação dos metadados para o volume é retirada da nuvem como parte da operação de failover, o volume é colocado on-line no dispositivo-alvo e pode ser acedido pelo anfitrião. Entretanto, os dados de volume continuarão a ser descarregados em segundo plano, e poderá sofrer um desempenho reduzido nestes volumes durante a duração do failover.

**Q.** Vejo o trabalho de failover concluído, como posso acompanhar o progresso do volume fixado localmente que está a ser restaurado no dispositivo-alvo?

**A.** Durante uma operação de failover, o trabalho de failover é marcado como completo uma vez que todos os volumes do conjunto de failover foram imediatamente restaurados e ligados on-line no dispositivo alvo. Isto inclui quaisquer volumes fixados localmente que possam ter sido falhados; no entanto, as garantias locais dos dados só estarão disponíveis quando todos os dados do volume tão volume tão pouco foram descarregados. Pode acompanhar este progresso para cada volume fixado localmente que foi falhado através da monitorização dos correspondentes empregos de restauro que são criados como parte do failover. Estes postos de trabalho individuais só serão criados para volumes fixados localmente.

**Q.** Posso alterar o tipo de volume durante o failover?

**A.** Não, não é possível alterar o tipo de volume durante uma falha. Se estiver a falhar com outro dispositivo físico que esteja a executar a atualização da série StorSimple 8000 3, os volumes são falhados com base no tipo de volume armazenado no instantâneo.

**Q.** Posso falhar sobre um recipiente de volume com volumes fixados localmente no aparelho em nuvem?

**A.** Sim, pode. Os volumes fixados localmente serão falhados como volumes hierárquicos. Mais informações sobre [failover e DR de volumes fixados localmente em versões](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

