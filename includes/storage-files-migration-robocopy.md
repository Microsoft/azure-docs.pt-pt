---
title: Mapear uma estrutura de pasta para uma topologia de sincronização de ficheiros Azure
description: Mapear uma estrutura de ficheiros e pasta existentes para ações de ficheiros Azure para utilização com o Azure File Sync. Um bloco de texto comum, partilhado através de documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: cde85e245c8cc6ae8c55b24270f125bacc111737
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547560"
---
```console
Robocopy /MT:16 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Comutador              | Significado |
|---------------------|---------|
| /MT                 | Permite que o RoboCopy corra com vários fios. O padrão é 8, e o máximo é 128. Combine este valor com a contagem de núcleo do seu processador e a contagem de fios por núcleo. Considere se os núcleos precisam de ser reservados para outras tarefas que um servidor de produção possa ter. |
| /NP                 | O progresso da cópia para cada ficheiro e pasta não será apresentado. A visualização do progresso diminui significativamente o desempenho da cópia. |
| /NFL                | Especifica que os nomes de ficheiro não estão registados. Melhora o desempenho da cópia. |
| /NDL                | Especifica que os nomes de diretório não estão registados. Melhora o desempenho da cópia. |
| /B                  | Executa roboCopy no mesmo modo que uma aplicação de backup usaria. Permite que o RoboCopy mova ficheiros para os que o utilizador atual não tem permissões. |
| /MIR                | *A fonte de espelho para o alvo* permite que o RoboCopy só tenha de copiar deltas entre a fonte e o alvo. Subdiretórios vazios serão copiados. Os itens (ficheiros ou pastas) que tenham mudado ou não existam no alvo serão copiados. Os itens que existam no alvo mas não na fonte serão purgados (eliminados) do alvo. Ao utilizar este interruptor, é imperativo que combine exatamente a estrutura da fonte e da pasta alvo. "Matching" significa que copia do nível de origem e pasta correto para o nível de pasta correspondente no alvo. Só então uma cópia de "catch up" pode ser bem sucedida. Quando a fonte e o alvo são desajustados, a utilização `/MIR` levará a eliminações e recopies em larga escala. |
| /IT                 | Garante que a fidelidade é preservada em certos cenários de espelhos. </br>*Exemplo* - Se entre dois RoboCopy executa um ficheiro experimenta uma alteração ACL e uma atualização de atributos: por exemplo, está marcada *como escondida*. Sem /TI, a alteração ACL pode ser ignorada pela RoboCopy e não transferida para o local alvo. |
|/CÓPIA:`[copyflags]`  | Fidelidade da cópia do ficheiro (predefinição se não especificado `/COPY:DAT` é), bandeiras de cópia: `D` `A` =Dados, `T` =Atributos, =Timestamps, `S` =Security=NTFS ACLs, `O` =Informações do proprietário, `U` =A<u>u</u>diting informações. As informações de auditoria não podem ser armazenadas numa partilha de ficheiros Azure. |
| /DCOPY:`[copyflags]`| Fidelidade para a cópia de diretórios (padrão se não especificado `/DCOPY:DA` é), bandeiras de cópia: `D` =Dados, `A` `T` =Atributos, =Timestamps. |
| /UNILOG:<file name> | Estado das saídas para ficheiro LOG como UNICODE (substitui registo existente). |
| /LFSM               | **apenas para alvos com armazenamento hierárquico** </br>A utilização /LFSM solicita à RoboCopy que opere em "modo de espaço livre baixo". Este interruptor só é útil para alvos com armazenamento hierárquico, que podem ficar sem capacidade local antes que roboCopy possa terminar. Este interruptor foi adicionado especificamente para ser utilizado com um alvo ativado por Azure File Sync. Pode ser utilizado independentemente do Azure File Sync. Neste modo, o RoboCopy fará uma pausa sempre que uma cópia de ficheiro faça com que o espaço livre do volume de destino fique abaixo de um valor de 'piso'. Este valor pode ser especificado pela `/LFSM:n` forma da bandeira. O parâmetro `n` é especificado na base 2: , ou `nKB` `nMB` `nGB` . Se `/LFSM` for especificado sem valor explícito do piso, o piso é definido para 10% do tamanho do volume de destino. O modo de espaço livre baixo é incompatível com /MT, /EFSRAW, /B, e /ZB. |
| /Z                  | **uso cuidadoso** </br>Copia ficheiros no modo de reinício, a utilização só é recomendada num ambiente de rede instável. Esta opção reduz significativamente o desempenho da cópia devido à extração extra. |
| /ZB                 | **uso cuidadoso** </br>Utiliza o modo de reinício. Se o acesso for negado, esta opção utiliza o modo de backup. Esta opção reduz significativamente o desempenho da cópia devido ao controlo de pontos de verificação. |
   