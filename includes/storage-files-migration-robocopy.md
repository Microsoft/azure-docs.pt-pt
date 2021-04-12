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
ms.openlocfilehash: 01512f0e37761915fcb6eeba8c162e1a3db62c48
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491690"
---
```console
Robocopy /MT:16 /R:5 /W:5 /B /MIR /IT /COPY:DATSO /DCOPY:DAT /NP /NFL /NDL /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Comutador                | Significado |
|-----------------------|---------|
| `/MT:n`               | Permite que o RoboCopy corra com vários fios. Predefinição para n = 8, e o máximo é de 128 fios. Combine este valor com a contagem de núcleo do seu processador e a contagem de fios por núcleo. Considere se os núcleos precisam de ser reservados para outras tarefas que um servidor de produção possa ter. |
| `/R:n`                | A velocidade de uma execução RoboCopy pode ser melhorada especificando uma contagem máxima de retírimento para um ficheiro que não consegue copiar na primeira tentativa. n = número máximo de retrações antes que o ficheiro falhe permanentemente em copiar nesta execução RoboCopy. Esta switch funciona melhor em cenários onde já é claro que haverá mais execuções roboCopy. Se o ficheiro não copiar nesta execução, o próximo trabalho do RoboCopy tentará novamente. Muitas vezes os ficheiros que falharam porque estavam a ser utilizados ou devido a problemas de tempo limite, poderiam eventualmente ser copiados com sucesso com esta abordagem. |
| `/W:n`                | Este switch especifica o tempo que o RoboCopy espera antes de tentar copiar um ficheiro que não copiou com sucesso na última tentativa. n = o número de segundos para esperar entre as retrósries. `/W:n` é frequentemente usado juntamente com `/R:n` . |
| `/B`                  | Executa roboCopy no mesmo modo que uma aplicação de backup usaria. Permite que o RoboCopy mova ficheiros para os que o utilizador atual não tem permissões. |
| `/MIR`                | *A fonte de espelho para o alvo* permite que o RoboCopy só tenha de copiar deltas entre a fonte e o alvo. Subdiretórios vazios serão copiados. Os itens (ficheiros ou pastas) que tenham mudado ou não existam no alvo serão copiados. Os itens que existam no alvo mas não na fonte serão purgados (eliminados) do alvo. Ao utilizar este interruptor, é imperativo que combine exatamente a estrutura da fonte e da pasta alvo. "Matching" significa que copia do nível de origem e pasta correto para o nível de pasta correspondente no alvo. Só então uma cópia de "catch up" pode ser bem sucedida. Quando a fonte e o alvo são desajustados, a utilização `/MIR` levará a eliminações e recopies em larga escala. |
| `/IT`                 | Garante que a fidelidade é preservada em certos cenários de espelhos. </br>*Exemplo* - Se entre dois RoboCopy executa um ficheiro experimenta uma alteração ACL e uma atualização de atributos: por exemplo, está marcada *como escondida*. Sem /TI, a alteração ACL pode ser ignorada pela RoboCopy e não transferida para o local alvo. |
|`/COPY:[copyflags]`    | Fidelidade da cópia do ficheiro (predefinição se não especificado `/COPY:DAT` é), bandeiras de cópia: `D` `A` =Dados, `T` =Atributos, =Timestamps, `S` =Security=NTFS ACLs, `O` =Informações do proprietário, `U` =A<u>u</u>diting informações. As informações de auditoria não podem ser armazenadas numa partilha de ficheiros Azure. |
| `/DCOPY:[copyflags]`  | Fidelidade para a cópia de diretórios (padrão se não especificado `/DCOPY:DA` é), bandeiras de cópia: `D` =Dados, `A` `T` =Atributos, =Timestamps. |
| `/NP`                 | O progresso da cópia para cada ficheiro e pasta não será apresentado. A visualização do progresso diminui significativamente o desempenho da cópia. |
| `/NFL`                | Especifica que os nomes de ficheiro não estão registados. Melhora o desempenho da cópia. |
| `/NDL`                | Especifica que os nomes de diretório não estão registados. Melhora o desempenho da cópia. |
| `/UNILOG:<file name>` | Estado das saídas para ficheiro LOG como UNICODE (substitui registo existente). |
| `/LFSM`               | **apenas para alvos com armazenamento hierárquico** </br>A utilização /LFSM solicita à RoboCopy que opere em "modo de espaço livre baixo". Este interruptor só é útil para alvos com armazenamento hierárquico, que podem ficar sem capacidade local antes que roboCopy possa terminar. Este interruptor foi adicionado especificamente para ser utilizado com um alvo ativado por Azure File Sync. Pode ser utilizado independentemente do Azure File Sync. Neste modo, o RoboCopy fará uma pausa sempre que uma cópia de ficheiro faça com que o espaço livre do volume de destino fique abaixo de um valor de 'piso'. Este valor pode ser especificado pela `/LFSM:n` forma da bandeira. O parâmetro `n` é especificado na base 2: , ou `nKB` `nMB` `nGB` . Se `/LFSM` for especificado sem valor explícito do piso, o piso é definido para 10% do tamanho do volume de destino. O modo de espaço livre baixo é incompatível com /MT, /EFSRAW, /B, e /ZB. |
| `/Z`                  | **uso cuidadoso** </br>Copia ficheiros no modo de reinício, a utilização só é recomendada num ambiente de rede instável. Esta opção reduz significativamente o desempenho da cópia devido à extração extra. |
| `/ZB`                 | **uso cuidadoso** </br>Utiliza o modo de reinício. Se o acesso for negado, esta opção utiliza o modo de backup. Esta opção reduz significativamente o desempenho da cópia devido ao controlo de pontos de verificação. |
   