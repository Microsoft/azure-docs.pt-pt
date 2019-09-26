---
title: Desenvolver operadores do U-SQL definidos pelo usuário-Azure Data Lake Analytics
description: Saiba como desenvolver operadores definidos pelo usuário para serem usados e reutilizados em Azure Data Lake Analytics trabalhos.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: b2d1293b06b4d8791138ed666bc3cb4abe3adf40
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316543"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Desenvolver operadores do U-SQL definidos pelo usuário (UDOs)
Este artigo descreve como desenvolver operadores definidos pelo usuário para processar dados em um trabalho do U-SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definir e usar um operador definido pelo usuário no U-SQL
**Para criar e enviar um trabalho do U-SQL**

1. No Visual Studio, selecione **arquivo > novo projeto de > > projeto U-SQL**.
2. Clique em **OK**. O Visual Studio cria uma solução com um arquivo script. usql.
3. Em **Gerenciador de soluções**, expanda script. usql e clique duas vezes em **script.usql.cs**.
4. Cole o seguinte código no arquivo:

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
6. Abra **script. usql**e cole o seguinte script U-SQL:

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
10. Se você não se conectou à sua assinatura do Azure, será solicitado que você insira suas credenciais de conta do Azure.
11. Clique em **Submit** (Submeter). Os resultados de envio e o link de trabalho estão disponíveis na janela resultados quando o envio é concluído.
12. Clique no botão **Atualizar** para ver o status do trabalho mais recente e atualizar a tela.

**Para ver a saída**

1. Em **Gerenciador de servidores**, expanda **Azure**, expanda **Data Lake Analytics**, expanda sua conta do data Lake Analytics, expanda **contas de armazenamento**, clique com o botão direito do mouse no armazenamento padrão e clique em **Gerenciador**.
2. Expanda amostras, expanda saídas e clique duas vezes em **drivers. csv**.

## <a name="see-also"></a>Consulte também
* [Estendendo expressões U-SQL com código de usuário](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Use as ferramentas de Data Lake para o Visual Studio para desenvolver aplicativos U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
