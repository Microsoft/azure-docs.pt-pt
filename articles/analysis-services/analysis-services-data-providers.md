---
title: Azure Analysis Services bibliotecas de cliente | Microsoft Docs
description: Descreve as bibliotecas de cliente necessárias para que os aplicativos cliente e as ferramentas se conectem Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9db76ca32c97cca622dc68642be5b770254191a1
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73834227"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliotecas de cliente para conexão com o Azure Analysis Services

As bibliotecas de cliente são necessárias para que aplicativos cliente e ferramentas se conectem a servidores Analysis Services. Aplicativos cliente da Microsoft como Power BI Desktop, Excel, SQL Server Management Studio (SSMS) e extensão de projetos de Analysis Services para Visual Studio instalam todas as três bibliotecas de cliente e as atualizam juntamente com atualizações de aplicativos regulares. Em alguns casos, talvez seja necessário instalar versões mais recentes das bibliotecas de cliente. Aplicativos cliente personalizados também exigem que bibliotecas de cliente sejam instaladas.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Baixar as bibliotecas de cliente mais recentes (Windows Installer)  

|Transferência  |Versão do produto  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.10.25    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.10.25       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.2.3.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.2.3.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO e ADOMD (pacotes NuGet)

Os ADOMD (objetos de gerenciamento do Analysis Services) e as bibliotecas de cliente do [amNuGet.org](https://www.nuget.org/)estão disponíveis como pacotes instaláveis do do. É recomendável migrar para referências do NuGet em vez de usar Windows Installer. 

|Pacote  | Versão do produto  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.0.9     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.0.9      |

Os assemblies do pacote NuGet AssemblyVersion seguem o controle de versão semântico: MAJOR. Secundária. Distribuído. As referências do NuGet carregam a versão esperada mesmo se houver uma versão diferente no GAC (resultante da instalação do MSI). O PATCH é incrementado para cada versão. As versões AMO e ADOMD são mantidas em sincronia.

## <a name="understanding-client-libraries"></a>Noções básicas sobre bibliotecas de cliente

Analysis Services utilizam três bibliotecas de cliente, também conhecidas como provedores de dados. O AMO (ADOMD.NET and Analysis Services Management Objects) são bibliotecas de cliente gerenciadas. O Provedor Analysis Services OLE DB (MSOLAP DLL) é uma biblioteca de cliente nativa. Normalmente, todos os três são instalados ao mesmo tempo. **Azure Analysis Services requer as versões mais recentes de todas as três bibliotecas**. 

Aplicativos cliente da Microsoft como Power BI Desktop e Excel instalam todas as três bibliotecas de cliente e as atualizam quando novas versões estão disponíveis. Dependendo da versão ou da frequência das atualizações, algumas bibliotecas de cliente podem não ser as versões mais recentes exigidas pelo Azure Analysis Services. O mesmo se aplica a aplicações personalizadas ou a outras interfaces, como sCmd, TOM ou ADOMD.NET. Esses aplicativos exigem a instalação manual ou programática das bibliotecas. As bibliotecas de cliente para instalação manual estão incluídas no SQL Server Feature packs como pacotes distribuíveis. No entanto, essas bibliotecas de cliente estão vinculadas à versão SQL Server e podem não ser as mais recentes.  

As bibliotecas de cliente para conexões de cliente são diferentes dos provedores de dados necessários para se conectar de um servidor de Azure Analysis Services a uma fonte de dados. Para saber mais sobre conexões de fonte de dados, consulte [conexões de fonte de dados](analysis-services-datasource.md).

## <a name="client-library-types"></a>Tipos de biblioteca de cliente

### <a name="analysis-services-ole-db-provider-msolap"></a>Provedor Analysis Services OLE DB (MSOLAP) 

 O Provedor Analysis Services OLE DB (MSOLAP) é a biblioteca de cliente nativa para conexões de banco de dados Analysis Services. Ele é usado indiretamente por ADOMD.NET e AMO, delegando solicitações de conexão ao provedor de dados. Você também pode chamar o provedor de OLE DB diretamente do código do aplicativo.  
  
 O Provedor Analysis Services OLE DB é instalado automaticamente pela maioria das ferramentas e aplicativos cliente usados para acessar bancos de dados do Analysis Services. Ele deve ser instalado em computadores usados para acessar Analysis Services dados.  
  
 Os provedores de OLE DB geralmente são especificados em cadeias de conexão. Uma cadeia de conexão Analysis Services usa um nomenclatura diferente para fazer referência ao provedor de OLE DB: MSOLAP.\<versão >. dll.

### <a name="amo"></a>AMO  

 O AMO é uma biblioteca de cliente gerenciada usada para administração de servidor e definição de dados. Ele é instalado e usado por ferramentas e aplicativos cliente. Por exemplo, SQL Server Management Studio (SSMS) usa o AMO para se conectar ao Analysis Services. Uma conexão usando o AMO normalmente é mínima, consistindo em `"data source=\<servername>"`. Depois que uma conexão é estabelecida, você usa a API para trabalhar com coleções de banco de dados e objetos principais. O Visual Studio e o SSMS usam o AMO para se conectar a uma instância de Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET é uma biblioteca de cliente de dados gerenciado usada para consultar dados de Analysis Services. Ele é instalado e usado por ferramentas e aplicativos cliente. 
  
 Ao se conectar a um banco de dados, as propriedades da cadeia de conexão para todas as três bibliotecas são semelhantes. Quase todas as cadeias de conexão que você define para ADOMD.NET usando [Microsoft. AnalysisServices. AdomdClient. AdomdConnection. ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) também funcionam para amo e o provedor Analysis Services OLE DB (MSOLAP). Para saber mais, confira [Propriedades &#40;da cadeia&#41;de conexão Analysis Services](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a>Como determinar a versão da biblioteca do cliente   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Aceda a `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Se você tiver mais de uma pasta, escolha o número mais alto.
  
2.  Clique com o botão direito do mouse em **MSOLAP. dll** > **Propriedades** > **detalhes**. Se o nome do arquivo for msolap140. dll, ele será mais antigo que a versão mais recente e deverá ser atualizado.
    
    ![Detalhes da biblioteca de cliente](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Aceda a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Se você tiver mais de uma pasta, escolha o número mais alto.
2. Clique com o botão direito do mouse em **Microsoft. AnalysisServices** > **Propriedades** > **detalhes**.  

### <a name="adomd"></a>ADOMD

1. Aceda a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Se você tiver mais de uma pasta, escolha o número mais alto.
2. Clique com o botão direito do mouse em **Microsoft. AnalysisServices. AdomdClient** > **Propriedades** > **detalhes**.  


## <a name="next-steps"></a>Passos seguintes
[Conectar-se ao Excel](analysis-services-connect-excel.md)    
[Ligar com o Power BI](analysis-services-connect-pbi.md)
