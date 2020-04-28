---
title: Como resolver os problemas com as falhas de tempo de funcionação do Azure Data Lake Analytics U-SQL
description: Aprenda a resolver problemas com falhas no tempo de funcionação da U-SQL.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73648457"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Saiba como resolver falhas no tempo de funcionamento da U-SQL devido a alterações no tempo de funcionamento

O tempo de execução do Azure Data Lake U-SQL, incluindo o compilador, o timómetro e o gestor de emprego, é o que processa o seu código U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Escolha a sua versão u-SQL runtime

Quando submeter empregos U-SQL a partir de Visual Studio, a ADL SDK ou o portal Azure Data Lake Analytics, o seu trabalho utilizará o tempo de execução padrão atualmente disponível. As novas versões do tempo de execução U-SQL são lançadas regularmente e incluem atualizações menores e correções de segurança.

Também pode escolher uma versão personalizada de tempo de execução; quer porque pretende experimentar uma nova atualização, precisa de se manter numa versão mais antiga de um tempo de execução, ou foi-lhe fornecido um hotfix para um problema relatado onde não pode esperar pela nova atualização regular.

> [!CAUTION]
> Escolher um tempo de execução diferente do padrão tem o potencial de quebrar os seus empregos u-SQL. Utilize estas outras versões apenas para testes.

Em casos raros, o Microsoft Support pode fixar uma versão diferente de um tempo de execução como padrão para a sua conta. Certifique-se de que reverte este pino o mais rápido possível. Se permanecer fixado a essa versão, expirará mais tarde.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Monitorização da versão runtime U-SQL

Pode ver a história da versão runtime que os seus trabalhos anteriores usaram no histórico de trabalho da sua conta através do navegador de emprego do Estúdio Visual ou do histórico de trabalho do portal Azure.

1. No portal Azure, vá à sua conta data Lake Analytics.
2. Selecione **Ver todos os empregos**. Aparece uma lista de todos os trabalhos ativos e recentemente acabados na conta.
3. Opcionalmente, clique em **Filtro** para ajudá-lo a encontrar os trabalhos por **Time Range,** **Nome de Trabalho**e Valores **de Autor.**
4. Pode ver o tempo de execução usado nos trabalhos concluídos.

![Exibindo a versão de tempo de execução de um trabalho passado](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

As versões de tempo de execução disponíveis mudam ao longo do tempo. O tempo de execução padrão é sempre chamado de "padrão" e mantemos pelo menos o tempo de funcionar anterior disponível por algum tempo, bem como disponibilizamos tempos de execução especiais por uma variedade de razões. Os tempos de execução explicitamente nomeados geralmente seguem o seguinte formato (os itálicos são usados para peças variáveis e [] indica partes opcionais):

release_YYYYMMDD_adl_buildno[_modifier]

Por exemplo, release_20190318_adl_3394512_2 significa a segunda versão da construção 3394512 do lançamento do prazo de execução de 18 de março de 2019 e release_20190318_adl_3394512_private significa uma construção privada do mesmo lançamento. Nota: A data está relacionada com a data em que o último check-in foi feito para esse lançamento e não necessariamente a data de lançamento oficial.

Seguem-se as versões atualmente disponíveis.

- release_20190318_adl_3394512
- release_20190318_adl_5832669 o padrão atual
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Problemas de resolução de problemas de problemas de versão runtime U-SQL

Existem dois possíveis problemas de versão de prazo de execução que pode encontrar:

1. Um script ou algum código de utilizador está a mudar o comportamento de uma versão para outra. Tais alterações de rutura são normalmente comunicadas com antecedência com a publicação de notas de lançamento. Se encontrar uma mudança tão grande, contacte o Microsoft Support para relatar este comportamento de rutura (caso ainda não tenha sido documentado) e submeta os seus trabalhos contra a versão mais antiga.

2. Tem usado um prazo de execução não predefinido, explícita ou implicitamente, quando está fixado na sua conta, e esse tempo de funcionação foi removido após algum tempo. Se encontrar tempos de execução em falta, por favor atualize os seus scripts para executar com o tempo de execução padrão atual. Se precisar de mais tempo, contacte o Microsoft Support

## <a name="see-also"></a>Consulte também

- [Visão geral do Lago de Dados Azure](data-lake-analytics-overview.md)
- [Gerir o Azure Data Lake Analytics usando o portal Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorize trabalhos em Azure Data Lake Analytics usando o portal Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
