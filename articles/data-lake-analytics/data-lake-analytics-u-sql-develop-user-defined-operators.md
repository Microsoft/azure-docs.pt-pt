---
title: Desenvolver operadores definidos pelo utilizador U-SQL - Azure Data Lake Analytics
description: Aprenda a desenvolver operadores definidos pelo utilizador para serem utilizados e reutilizados em trabalhos de Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: b2d1293b06b4d8791138ed666bc3cb4abe3adf40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71316543"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Desenvolver operadores definidos pelo utilizador U-SQL (UDOs)
Este artigo descreve como desenvolver operadores definidos pelo utilizador para processar dados num trabalho U-SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Defina e utilize um operador definido pelo utilizador em U-SQL
**Para criar e submeter um trabalho U-SQL**

1. Do Estúdio Visual selecione **File > New > Project > U-SQL Project**.
2. Clique em **OK**. O Visual Studio cria uma solução com um ficheiro Script.usql.
3. Do **Solution Explorer,** expanda o Script.usql e, em seguida, clique duas vezes **Script.usql.cs**.
4. Colá-lo no ficheiro:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Suisse", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. Abra **script.usql**, e reexa o seguinte script U-SQL:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. Especifique a conta do Data Lake Analytics, a Base de Dados e o Esquema.
8. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Criar Script**.
9. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Submeter Script**.
10. Se não tiver conectado à sua subscrição Azure, será solicitado a introduzir as credenciais da sua conta Azure.
11. Clique em **Submeter**. Os resultados da submissão e o link de trabalho estão disponíveis na janela resultados quando a submissão estiver concluída.
12. Clique no botão **Refresh** para ver o estado de trabalho mais recente e refrescar o ecrã.

**Para ver a saída**

1. A partir do **Server Explorer,** expanda **o Azure,** expanda data **lake analytics,** expanda a conta de **Armazenamento,** clique no armazenamento predefinido e, em seguida, clique em **Explorer**.
2. Expandir amostras, expandir saídas e, em seguida, clicar duas vezes **em Drivers.csv**.

## <a name="see-also"></a>Consulte também
* [Alargar expressões U-SQL com código de utilizador](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Utilize ferramentas de data lake para estúdio visual para desenvolver aplicações U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
