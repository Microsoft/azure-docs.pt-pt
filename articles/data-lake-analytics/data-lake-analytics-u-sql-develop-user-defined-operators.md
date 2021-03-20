---
title: Desenvolver operadores definidos pelo utilizador U-SQL - Azure Data Lake Analytics
description: Saiba como desenvolver operadores definidos pelo utilizador para serem usados e reutilizados em trabalhos Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: 11efdb727bacadb674fb49374ef1c70fcc788ecc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219994"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Desenvolver operadores definidos pelo utilizador U-SQL (UDOs)
Este artigo descreve como desenvolver operadores definidos pelo utilizador para processar dados num trabalho U-SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definir e utilizar um operador definido pelo utilizador em U-SQL

### <a name="to-create-and-submit-a-u-sql-job"></a>Para criar e submeter um trabalho U-SQL

1. Do Estúdio Visual selecione **Arquivo > Projeto New > > U-SQL Project**.
2. Clique em **OK**. O Visual Studio cria uma solução com um ficheiro Script.usql.
3. A partir do **Solution Explorer,** expanda o Script.usql e, em seguida, clique duas vezes **em Script.usql.cs**.
4. Cole o código seguinte no ficheiro:

   ```usql
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
   ```

5. Abra **o Script.usql** e cole o seguinte script U-SQL:

   ```usql
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
   ```

6. Especifique a conta do Data Lake Analytics, a Base de Dados e o Esquema.
7. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Criar Script**.
8. No **Explorador de Soluções**, clique com o botão direito do rato em **Script.usql**, e, em seguida, clique em **Submeter Script**.
9. Se não tiver ligado à sua subscrição Azure, será solicitado que introduza as suas credenciais de conta Azure.
10. Clique **em Submeter.** Os resultados de submissão e o link de trabalho estão disponíveis na janela Resultados quando a submissão estiver concluída.
11. Clique no botão **Refresh** para ver o estado de trabalho mais recente e refrescar o ecrã.

### <a name="to-see-the-output"></a>Para ver a saída

1. A partir do **Server Explorer**, expanda **o Azure,** expanda **o Data Lake Analytics,** expanda a sua conta Data Lake Analytics, expanda **as Contas de Armazenamento,** clique com o botão direito no Armazenamento Padrão e, em seguida, clique no **Explorer**.

2. Expandir amostras, expandir saídas e, em seguida, clicar duas vezes **Drivers.csv**.

## <a name="next-steps"></a>Passos seguintes

* [Extensão de Expressões U-SQL com Código do Utilizador](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Utilize ferramentas do Data Lake para o Estúdio Visual para o desenvolvimento de aplicações U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
