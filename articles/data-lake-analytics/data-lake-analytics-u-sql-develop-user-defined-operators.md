---
title: Desenvolver operadores U-SQL definidos pelo utilizador (UDOs) no Azure Data Lake Analytics
description: Saiba como desenvolver operadores definidos pelo utilizador para ser usados e reutilizados nas tarefas de Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 122a4b6af78a22f74d5057da75767077f8d9b978
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496151"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Desenvolver operadores definidos pelo utilizador do U-SQL (UDOs)
Este artigo descreve como desenvolver operadores definidos pelo utilizador para processar os dados numa tarefa U-SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definir e usar um operador definido pelo utilizador em U-SQL
**Para criar e submeter uma tarefa U-SQL**

1. No, selecione Visual Studio **ficheiro > novo > projeto > projeto U-SQL**.
2. Clique em **OK**. Visual Studio cria uma solução com um ficheiro de script. usql.
3. Partir **Explorador de soluções**, expanda o script. usql e, em seguida, faça duplo clique em **Script.usql.cs**.
4. Cole o seguinte código no ficheiro:

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
6. Open **script. usql**e cole o seguinte script U-SQL:

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
10. Se ainda não tiver ligado à sua subscrição do Azure, será solicitado que introduza as credenciais de conta do Azure.
11. Clique em **Submit** (Submeter). Os resultados da submissão e a ligação da tarefa ficam disponíveis na janela de resultados quando a submissão estiver concluída.
12. Clique nas **atualizar** botão para ver a versão mais recente estado da tarefa e atualizar o ecrã.

**Para ver a saída**

1. Partir **Explorador de servidores**, expanda **Azure**, expanda **do Data Lake Analytics**, expanda a conta do Data Lake Analytics, expanda **decontasdearmazenamento**, faça duplo clique o armazenamento predefinido e, em seguida, clique em **Explorer**.
2. Expanda os exemplos, expanda saídas e, em seguida, faça duplo clique em **Drivers.csv**.

## <a name="see-also"></a>Consulte também
* [Estendendo as expressões de U-SQL com o código de utilizador](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Utilizar ferramentas do Data Lake para Visual Studio para desenvolver aplicações U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
