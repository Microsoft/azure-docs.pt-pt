---
title: Como resolver problemas com as falhas de tempo de execução U-SQL do Azure Data Lake Analytics
description: Saiba como resolver falhas de tempo de funcionação U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 1236b83b410057e55015391772e37bd461a448d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030618"
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
3. Opcionalmente, clique em **Filtro** para ajudá-lo a encontrar os trabalhos por **Time Range,** **Job Name** e **Author** values.
4. Pode ver o tempo de funcionação usado nos trabalhos concluídos.

![Exibindo a versão de tempo de execução de um trabalho passado](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

As versões de tempo de execução disponíveis mudam ao longo do tempo. O tempo de funcionação predefinido é sempre chamado de "padrão" e mantemos pelo menos o tempo de execução anterior disponível durante algum tempo, bem como disponibilizamos tempos de execução especiais por uma variedade de razões. Os tempos de execução explicitamente nomeados geralmente seguem o seguinte formato (os itálicos são utilizados para peças variáveis e [] indica peças opcionais):

release_YYYYMMDD_adl_buildno[_modifier]

Por exemplo, release_20190318_adl_3394512_2 significa que a segunda versão da construção 3394512 do lançamento em tempo de execução de 18 de março de 2019 e release_20190318_adl_3394512_private significa uma construção privada do mesmo lançamento. Nota: A data está relacionada com a data em que foi feito o último check-in para essa libertação e não necessariamente a data de lançamento oficial.


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Problemas na resolução de problemas problemas da versão de execução U-SQL

Existem dois possíveis problemas de versão de tempo de execução que pode encontrar:

1. Um script ou algum código de utilizador está a mudar o comportamento de uma versão para a outra. Tais alterações de rutura são normalmente comunicadas com antecedência com a publicação de notas de lançamento. Se encontrar uma mudança tão quebrada, contacte o Microsoft Support para reportar este comportamento de rutura (caso ainda não tenha sido documentado) e submeta os seus trabalhos contra a versão de tempo de execução mais antiga.

2. Tem vindo a utilizar um tempo de execução não padrão, explicitamente ou implicitamente, quando este foi fixado na sua conta, e esse tempo de funcionação foi removido após algum tempo. Se encontrar tempo de execução em falta, atualize os seus scripts para executar com o tempo de execução atual. Se precisar de tempo adicional, contacte o Microsoft Support

## <a name="known-issues"></a>Problemas conhecidos

1. A referência Newtonsoft.Jsna versão de ficheiro 12.0.3 ou em diante num script USQL causará a seguinte falha de compilação:

    *"Lamentamos; os empregos que funcionam na sua conta Data Lake Analytics provavelmente funcionarão mais lentamente ou não completarão. Um problema inesperado está a impedir-nos de restaurar automaticamente esta funcionalidade na sua conta Azure Data Lake Analytics. Engenheiros do Azure Data Lake foram contactados para investigar."*  

    Onde a pilha de chamadas conterá:  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **Solução**: Utilize Newtonsoft.Jsno ficheiro v12.0.2 ou inferior.
2. Os clientes podem ver ficheiros e pastas temporárias na sua loja. Estes são produzidos como parte da execução normal do trabalho, mas geralmente são apagados antes que os clientes os vejam. Sob certas circunstâncias, raras e aleatórias, podem permanecer visíveis por um período de tempo. São eventualmente eliminados, e nunca são contados como parte do armazenamento do utilizador, ou geram qualquer forma de encargos. Dependendo da lógica de trabalho dos clientes, podem causar problemas. Por exemplo, se o trabalho enumerar todos os ficheiros da pasta e depois comparar listas de ficheiros, pode falhar devido à presença de ficheiros temporários inesperados. Da mesma forma, se um trabalho a jusante enumerar todos os ficheiros de uma determinada pasta para posterior processamento, também poderá enumerar os ficheiros temporários.  

    **Solução**: Uma correção é identificada no tempo de funcionação onde os ficheiros temporários serão armazenados na pasta temp de nível de conta do que na pasta de saída atual. Os ficheiros temporários serão escritos nesta nova pasta temporária e serão eliminados no final da execução do trabalho.  
    Uma vez que esta correção está a tratar os dados do cliente, é extremamente importante ter esta correção bem validada dentro da MSFT antes de ser lançada. Espera-se que esta correção esteja disponível como tempo de execução beta em meados do ano de 2021 e como tempo de execução padrão na segunda metade do ano de 2021. 


## <a name="see-also"></a>Ver também

- [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Gerir a Azure Data Lake Analytics usando o portal Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorize empregos em Azure Data Lake Analytics usando o portal Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
