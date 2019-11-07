---
title: Como solucionar problemas de falhas de tempo de execução do Azure Data Lake Analytics U-SQL
description: Saiba como solucionar falhas de tempo de execução do U-SQL.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648457"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Saiba como solucionar falhas de tempo de execução do U-SQL devido a alterações no tempo de execução

O tempo de execução do Azure Data Lake U-SQL, incluindo o compilador, o otimizador e o Gerenciador de trabalhos, é o que processa seu código U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Escolhendo sua versão de tempo de execução do U-SQL

Quando você envia trabalhos do U-SQL do Visual Studio, do SDK do ADL ou do portal de Azure Data Lake Analytics, seu trabalho usará o tempo de execução padrão disponível no momento. Novas versões do tempo de execução do U-SQL são lançadas regularmente e incluem correções de segurança e atualizações secundárias.

Você também pode escolher uma versão de tempo de execução personalizada; Porque você deseja experimentar uma nova atualização, precisa permanecer em uma versão mais antiga de um tempo de execução ou ser fornecido com um hotfix para um problema relatado em que não é possível esperar pela nova atualização regular.

> [!CAUTION]
> Escolher um tempo de execução diferente do padrão tem o potencial de quebrar seus trabalhos do U-SQL. Use essas outras versões somente para teste.

Em casos raros, Suporte da Microsoft pode fixar uma versão diferente de um tempo de execução como o padrão para sua conta. Verifique se você reverta esse PIN assim que possível. Se você permanecer fixado a essa versão, ela expirará em alguma data posterior.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Monitorando sua versão de tempo de execução U-SQL de trabalhos

Você pode ver o histórico de qual versão de tempo de execução seus trabalhos anteriores usaram no histórico de trabalho da sua conta por meio do navegador de trabalho do Visual Studio ou do histórico de trabalho do portal do Azure.

1. Na portal do Azure, vá para sua conta do Data Lake Analytics.
2. Selecione **Exibir todos os trabalhos**. Uma lista de todos os trabalhos ativos e concluídos recentemente na conta é exibida.
3. Opcionalmente, clique em **Filtrar** para ajudá-lo a encontrar os trabalhos por **intervalo de tempo**, nome do **trabalho**e valores de **autor** .
4. Você pode ver o tempo de execução usado nos trabalhos concluídos.

![Exibindo a versão de tempo de execução de um trabalho passado](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

As versões de tempo de execução disponíveis mudam com o passar do tempo. O tempo de execução padrão é sempre chamado de "padrão" e mantemos pelo menos o tempo de execução anterior disponível por algum tempo, além de tornar os tempos de execução especiais disponíveis por vários motivos. Tempos de execução explicitamente nomeados geralmente seguem o seguinte formato (os itálicos são usados para partes variáveis e [] indicam partes opcionais):

release_YYYYMMDD_adl_buildno [_modifier]

Por exemplo, release_20190318_adl_3394512_2 significa a segunda versão do Build 3394512 da versão de tempo de execução de 18 2019 de março e release_20190318_adl_3394512_private significa uma compilação particular da mesma versão. Observação: a data está relacionada a quando o último check-in foi realizado para essa versão e não necessariamente a data de lançamento oficial.

A seguir estão as versões de tempo de execução disponíveis no momento.

- release_20190318_adl_3394512
- release_20190318_adl_5832669 o padrão atual
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Solucionando problemas de versão do tempo de execução do U-SQL

Há dois possíveis problemas de versão de tempo de execução que você pode encontrar:

1. Um script ou algum código de usuário está alterando o comportamento de uma versão para outra. Essas alterações significativas normalmente são comunicadas antecipadamente com a publicação de notas de versão. Se você encontrar essa alteração significativa, entre em contato com Suporte da Microsoft para relatar esse comportamento de interrupção (caso ainda não tenha sido documentado) e envie seus trabalhos para a versão de tempo de execução mais antiga.

2. Você esteve usando um tempo de execução não padrão de forma explícita ou implícita quando foi fixado em sua conta e esse tempo de execução foi removido após algum tempo. Se você encontrar tempos de execução ausentes, atualize seus scripts para executar com o tempo de execução padrão atual. Se você precisar de mais tempo, entre em contato com Suporte da Microsoft

## <a name="see-also"></a>Consultar também

- [Visão geral de Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Gerir o Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorar trabalhos no Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
