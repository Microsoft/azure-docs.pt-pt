---
title: Como resolver problemas com as falhas de tempo de execução U-SQL do Azure Data Lake Analytics
description: Saiba como resolver falhas de tempo de funcionação U-SQL.
services: data-lake-analytics
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 39b4a3bc5e5f70a5699f4fd84ec86dc61cf21483
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87475265"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Saiba como resolver problemas de funcionamento do U-SQL devido a alterações no tempo de funcionamento

O tempo de execução U-SQL do Azure Data Lake, incluindo o compilador, otimizador e gestor de emprego, é o que processa o seu código U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Escolher a sua versão de runtime U-SQL

Quando submeter trabalhos U-SQL a partir de Visual Studio, ADL SDK ou do portal Azure Data Lake Analytics, o seu trabalho utilizará o tempo de execução padrão atualmente disponível. As novas versões do tempo de execução U-SQL são lançadas regularmente e incluem atualizações menores e correções de segurança.

Também pode escolher uma versão de tempo de execução personalizada; ou porque pretende experimentar uma nova atualização, precisa de permanecer numa versão mais antiga de um tempo de execução, ou foi-lhe fornecido um hotfix para um problema relatado onde não pode esperar pela nova atualização regular.

> [!CAUTION]
> Escolher um tempo de execução diferente do padrão tem o potencial de quebrar os seus empregos U-SQL. Utilize estas outras versões apenas para testar.

Em casos raros, o Microsoft Support pode fixar uma versão diferente de um tempo de execução como o padrão da sua conta. Certifique-se de que reverte este pino o mais rapidamente possível. Se permanecer preso a essa versão, expirará mais tarde.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Monitorização dos seus trabalhos Versão de runtime U-SQL

Pode ver a história da versão em tempo de execução que os seus trabalhos anteriores utilizaram no histórico de trabalho da sua conta através do browser de emprego do Visual Studio ou do histórico de trabalho do portal Azure.

1. No portal Azure, aceda à sua conta Data Lake Analytics.
2. Selecione **Ver Todos os Trabalhos**. Aparece uma lista de todos os empregos ativos e recentemente acabados na conta.
3. Opcionalmente, clique em **Filtro** para ajudá-lo a encontrar os trabalhos por **Time Range,** **Job Name**e **Author** values.
4. Pode ver o tempo de funcionação usado nos trabalhos concluídos.

![Exibindo a versão de tempo de execução de um trabalho passado](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

As versões de tempo de execução disponíveis mudam ao longo do tempo. O tempo de funcionação predefinido é sempre chamado de "padrão" e mantemos pelo menos o tempo de execução anterior disponível durante algum tempo, bem como disponibilizamos tempos de execução especiais por uma variedade de razões. Os tempos de execução explicitamente nomeados geralmente seguem o seguinte formato (os itálicos são utilizados para peças variáveis e [] indica peças opcionais):

release_YYYYMMDD_adl_buildno[_modifier]

Por exemplo, release_20190318_adl_3394512_2 significa que a segunda versão da construção 3394512 do lançamento em tempo de execução de 18 de março de 2019 e release_20190318_adl_3394512_private significa uma construção privada do mesmo lançamento. Nota: A data está relacionada com a data em que foi feito o último check-in para essa libertação e não necessariamente a data de lançamento oficial.


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Problemas na resolução de problemas problemas da versão de execução U-SQL

Existem dois possíveis problemas de versão de tempo de execução que pode encontrar:

1. Um script ou algum código de utilizador está a mudar o comportamento de uma versão para a outra. Tais alterações de rutura são normalmente comunicadas com antecedência com a publicação de notas de lançamento. Se encontrar uma mudança de rutura, contacte o Microsoft Support para reportar este comportamento de rutura (caso ainda não tenha sido documentado) e submeta os seus trabalhos contra a versão de tempo de execução mais antiga.

2. Tem vindo a utilizar um tempo de execução não padrão, explicitamente ou implicitamente, quando este foi fixado na sua conta, e esse tempo de funcionação foi removido após algum tempo. Se encontrar tempo de execução em falta, por favor atualize os seus scripts para executar com o tempo de execução atual. Se precisar de tempo adicional, contacte o Microsoft Support

## <a name="see-also"></a>Consulte também

- [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Gerir a Azure Data Lake Analytics usando o portal Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorize empregos em Azure Data Lake Analytics usando o portal Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
